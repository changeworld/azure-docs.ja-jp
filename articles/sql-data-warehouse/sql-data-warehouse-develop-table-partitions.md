<properties
   pageTitle="SQL Data Warehouse のテーブル パーティション | Microsoft Azure"
   description="ソリューション開発のための Azure SQL Data Warehouse でのテーブル パーティションの使用に関するヒント。"
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="jrowlandjones"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="06/22/2015"
   ms.author="JRJ@BigBangData.co.uk;barbkess"/>

# SQL Data Warehouse のテーブル パーティション

SQL Server のパーティション定義を SQL Data Warehouse に移行するには、次の操作を行います。

- SQL Server のパーティション関数とパーティション構成を削除します。これらは、テーブルの作成時に自動的に準備されます。
- テーブルの作成時にパーティションを定義します。パーティション境界点と、境界点を有効な `RANGE RIGHT` または `RANGE LEFT` にするかどうかを指定するだけです。

### パーティションのサイズ設定
パーティションのサイズ設定は、SQL Data Warehouse の重要な考慮事項の 1 つです。通常、データ管理操作とデータ読み込みルーチンは、一度にテーブル全体に対処するのではなく、個々のパーティションを対象とします。これは、特にクラスター化列ストア (CCI) に関係します。CCI は大量のメモリーを使用する可能性があります。そのため、パーティション分割プランの粒度を変更する必要がある場合、管理タスクの実行時にメモリ負荷がかかるようなパーティション サイズに設定することは望ましくありません。

パーティションの粒度を決定するときは、SQL Data Warehouse ではデータが各ディストリビューションに自動的に分散されることに注意する必要があります。そのため、SQL Server データベースの 1 つのテーブルの 1 つのパーティションに通常どおり存在していたデータは、SQL Data Warehouse データベースでは多数のテーブルにまたがる 1 つのパーティションに存在するようになります。各パーティションで有効な行数を維持するために、通常はパーティションの境界サイズを変更します。たとえば、データ ウェアハウスで日レベルのパーティション分割を使用していた場合は、月や四半期などの粒度の小さいものを検討することをお勧めします。

パーティション レベルで現在のデータベースのサイズを設定するには、次のようなクエリを使用します。

```
SELECT      s.[name]                        AS      [schema_name]
,           t.[name]                        AS      [table_name]
,           i.[name]                        AS      [index_name]
,           p.[partition_number]            AS      [partition_number]
,           SUM(a.[used_pages]*8.0)         AS      [partition_size_kb]
,           SUM(a.[used_pages]*8.0)/1024    AS      [partition_size_mb]
,           SUM(a.[used_pages]*8.0)/1048576 AS      [partition_size_gb]
,           p.[rows]                        AS      [partition_row_count]
,           rv.[value]                      AS      [partition_boundary_value]
,           p.[data_compression_desc]       AS      [partition_compression_desc]
FROM        sys.schemas s
JOIN        sys.tables t                    ON      t.[schema_id]         = s.[schema_id]
JOIN        sys.partitions p                ON      p.[object_id]         = t.[object_id]
JOIN        sys.allocation_units a          ON      a.[container_id]        = p.[partition_id]
JOIN        sys.indexes i                   ON      i.[object_id]         = p.[object_id]
                                            AND     i.[index_id]          = p.[index_id]
JOIN        sys.data_spaces ds              ON      ds.[data_space_id]    = i.[data_space_id]
LEFT JOIN   sys.partition_schemes ps        ON      ps.[data_space_id]    = ds.[data_space_id]
LEFT JOIN   sys.partition_functions pf      ON      pf.[function_id]      = ps.[function_id]
LEFT JOIN   sys.partition_range_values rv   ON      rv.[function_id]      = pf.[function_id]
                                            AND     rv.[boundary_id]      = p.[partition_number]
WHERE       p.[index_id] <=1
GROUP BY    s.[name]
,           t.[name]
,           i.[name]
,           p.[partition_number]
,           p.[rows]
,           rv.[value]
,           p.[data_compression_desc]
;
```

ディストリビューションの合計数は、テーブルの作成時に使用するストレージの場所の数と等しくなります。これは、各テーブルがディストリビューションごとに 1 回作成されるという複雑な方法です。

また、行数を大まかに予測できるので、各パーティションのサイズを予測できます。ソース データ ウェアハウスのパーティションは、各ディストリビューションに再分割されます。

パーティション サイズを決定するときの参考として、次の計算を使用します。

MPP パーティション サイズ = SMP パーティション サイズ/ディストリビューション数

SQL Data Warehouse データベースのディストリビューション数は、次のクエリを使用して調べることができます。

```
SELECT  COUNT(*)
FROM    sys.pdw_distributions
;
```

これで、ソース システムの各パーティションのサイズと、SQLDW 用に予測しているサイズがわかり、パーティション境界を決定できます。

### ワークロード管理
テーブル パーティションを決定する際に組み込む必要がある情報の最後の部分として、ワークロード管理があります。SQL Data Warehouse では、この機能によって、クエリの実行中に各ディストリビューションに割り当てられる最大メモリが管理されます。詳細については、[ワークロード管理]に関する記事をご覧ください。列ストア インデックスの再構築などのメモリ内操作を念頭に置いて、パーティションのサイズを設定するのが理想的です。インデックスの再構築は、大量のメモリを使用する操作です。したがって、パーティション インデックスの再構築でメモリ不足にならないようにする必要があります。クエリで使用できるメモリの量を増やすには、既定のロールから使用可能な他のロールのいずれかに切り替えます。

ディストリビューションごとのメモリの割り当てに関する情報は、リソース ガバナーの動的管理ビューを照会することで入手できます。実際には、メモリ許可は次の数値よりも少なくなります。ただし、データ管理操作のためにパーティションのサイズを設定するときに、これがある程度の目安になります。

```
SELECT  rp.[name]								AS [pool_name]
,       rp.[max_memory_kb]						AS [max_memory_kb]
,       rp.[max_memory_kb]/1024					AS [max_memory_mb]
,       rp.[max_memory_kb]/1048576				AS [mex_memory_gb]
,       rp.[max_memory_percent]					AS [max_memory_percent]
,       wg.[name]								AS [group_name]
,       wg.[importance]							AS [group_importance]
,       wg.[request_max_memory_grant_percent]	AS [request_max_memory_grant_percent]
FROM    sys.dm_pdw_nodes_resource_governor_workload_groups	wg
JOIN    sys.dm_pdw_nodes_resource_governor_resource_pools	rp ON wg.[pool_id] = rp.[pool_id]
WHERE   wg.[name] like 'SloDWGroup%'
AND     rp.[name]    = 'SloDWPool'
```

> [AZURE.NOTE]非常に大きなリソース クラスで提供されるメモリ許可を上回るパーティション サイズに設定しないようにしてください。パーティションのサイズがこの数値を上回ると、メモリ負荷のリスクが生じ、最適に圧縮できなくなります。

## パーティションの切り替え
2 つのテーブル間でパーティションを切り替えるには、それぞれの境界に合わせてパーティションが配置されていることと、テーブル定義が一致していることを確認する必要があります。テーブルで値の範囲を適用する際に CHECK 制約は使用できないため、ソース テーブルにターゲットテーブルと同じパーティション境界が含まれている必要があります。そうでない場合、パーティションのメータデータが同期されないため、パーティションの切り替えは失敗します。

### データが含まれたパーティションを分割する方法
既にデータが含まれているパーティションを分割する最も効率的な方法は、`CTAS` ステートメントを使用することです。パーティション テーブルがクラスター化列ストアの場合、テーブルのパーティションを分割するには、パーティションを空にしておく必要があります。

最後のパーティションに行が 1 つ含まれた、パーティション分割された列ストア テーブルのサンプルを次に示します。

```
CREATE TABLE [dbo].[FactInternetSales]
(
        [ProductKey]            int          NOT NULL
    ,   [OrderDateKey]          int          NOT NULL
    ,   [CustomerKey]           int          NOT NULL
    ,   [PromotionKey]          int          NOT NULL
    ,   [SalesOrderNumber]      nvarchar(20) NOT NULL
    ,   [OrderQuantity]         smallint     NOT NULL
    ,   [UnitPrice]             money        NOT NULL
    ,   [SalesAmount]           money        NOT NULL
)
WITH
(   CLUSTERED COLUMNSTORE INDEX
,   DISTRIBUTION = HASH([ProductKey])
,   PARTITION   (   [OrderDateKey] RANGE RIGHT FOR VALUES
                    (20000101
                    )
                )
)
;

INSERT INTO dbo.FactInternetSales
VALUES (1,20010101,1,1,1,1,1,1)

CREATE STATISTICS Stat_dbo_FactInternetSales_OrderDateKey ON dbo.FactInternetSales(OrderDateKey)
```

> [AZURE.NOTE]統計オブジェクトを作成することによって、テーブルのメタデータをより正確なものにすることができます。統計の作成を省略した場合は、既定値が使用されます。統計の詳細については､[統計][]に関するページをご覧ください。

次に、`sys.partitions` カタログ ビューを利用して行数を照会できます。

```
SELECT  QUOTENAME(s.[name])+'.'+QUOTENAME(t.[name]) as Table_name
,       i.[name] as Index_name
,       p.partition_number as Partition_nmbr
,       p.[rows] as Row_count
,       p.[data_compression_desc] as Data_Compression_desc
FROM    sys.partitions p
JOIN    sys.tables     t    ON    p.[object_id]   = t.[object_id]
JOIN    sys.schemas    s    ON    t.[schema_id]   = s.[schema_id]
JOIN    sys.indexes    i    ON    p.[object_id]   = i.[object_Id]
                            AND   p.[index_Id]    = i.[index_Id]
WHERE t.[name] = 'FactInternetSales'
```

このテーブルを分割しようとすると、次のエラーが発生します。

```
ALTER TABLE FactInternetSales SPLIT RANGE (20020101)
```

パーティションが空でないため、ALTER PARTITION ステートメントの Msg 35346, Level 15, State 1, Line 44 SPLIT 句が失敗しました。テーブルに列ストア インデックスが存在する場合、分割できるのは空のパーティションだけです。ALTER PARTITION ステートメントを発行する前に列ストア インデックスを無効にし、ALTER PARTITION が完了したら列ストア インデックスを再構築することを検討します。

ただし、`CTAS` を使用して、データを保持する新しいテーブルを作成できます。

```
CREATE TABLE dbo.FactInternetSales_20010101
    WITH    (   DISTRIBUTION = HASH(ProductKey)
            ,   CLUSTERED COLUMNSTORE INDEX
            ,   PARTITION   (   [OrderDateKey] RANGE RIGHT FOR VALUES
                                (20010101
                                )
                            )
            )
AS
SELECT *
FROM	FactInternetSales
WHERE	1=2
```

パーティション境界が配置されているので、切り替えが許可されます。これにより、後で分割できる空のパーティションを含むソース テーブルをそのままにしておくことができます。

```
ALTER TABLE FactInternetSales SWITCH PARTITION 2 TO  FactInternetSales_20010101 PARTITION 2

ALTER TABLE FactInternetSales SPLIT RANGE (20020101)
```

後は、`CTAS` を使用して新しいパーティション境界に合わせてデータを配置し、データをメイン テーブルに切り替えるだけです。

```
CREATE TABLE [dbo].[FactInternetSales_20010101_20020101]
    WITH    (   DISTRIBUTION = HASH([ProductKey])
            ,   CLUSTERED COLUMNSTORE INDEX
            ,   PARTITION   (   [OrderDateKey] RANGE RIGHT FOR VALUES
                                (20010101,20020101
                                )
                            )
            )
AS
SELECT  *
FROM	[dbo].[FactInternetSales_20010101]
WHERE	[OrderDateKey] >= 20010101
AND     [OrderDateKey] <  20020101

ALTER TABLE FactInternetSales_20010101_20020101 SWITCH PARTITION 3 TO  FactInternetSales PARTITION 3
```

データの移動が完了したら、ターゲット テーブルの統計に、それぞれのパーティションのデータの新しいディストリビューションが正確に反映されるように、統計を更新することをお勧めします。

```
UPDATE STATISTICS [dbo].[FactInternetSales]
```

### テーブル パーティションのソース管理
ソース管理システムでテーブル定義が**古く**ならないように、次の方法を検討することをお勧めします。

1. パーティション値を含まないパーティション テーブルとしてテーブルを作成します。

```
CREATE TABLE [dbo].[FactInternetSales]
(
    [ProductKey]            int          NOT NULL
,   [OrderDateKey]          int          NOT NULL
,   [CustomerKey]           int          NOT NULL
,   [PromotionKey]          int          NOT NULL
,   [SalesOrderNumber]      nvarchar(20) NOT NULL
,   [OrderQuantity]         smallint     NOT NULL
,   [UnitPrice]             money        NOT NULL
,   [SalesAmount]           money        NOT NULL
)
WITH
(   CLUSTERED COLUMNSTORE INDEX
,   DISTRIBUTION = HASH([ProductKey])
,   PARTITION   (   [OrderDateKey] RANGE RIGHT FOR VALUES
                    ()
                )
)
;
```

2. デプロイメント プロセスの一環として、テーブルを `SPLIT` (分割) します。

```
-- Create a table containing the partition boundaries

CREATE TABLE #partitions
WITH
(
    LOCATION = USER_DB
,   DISTRIBUTION = HASH(ptn_no)
)
AS
SELECT  ptn_no
,       ROW_NUMBER() OVER (ORDER BY (ptn_no)) as seq_no
FROM    (
        SELECT CAST(20000101 AS INT) ptn_no
        UNION ALL
        SELECT CAST(20010101 AS INT)
        UNION ALL
        SELECT CAST(20020101 AS INT)
        UNION ALL
        SELECT CAST(20030101 AS INT)
        UNION ALL
        SELECT CAST(20040101 AS INT)
        ) a
;

-- Iterate over the partition boundaries and split the table

DECLARE @c INT = (SELECT COUNT(*) FROM #partitions)
,       @i INT = 1                     --iterator for while loop
,       @q NVARCHAR(4000)              --query
,       @p NVARCHAR(20)     = N''      --partition_number
,       @s NVARCHAR(128)    = N'dbo'   --schema
,       @t NVARCHAR(128)    = N'table' --table
;

WHILE @i <= @c
BEGIN
    SET @p = (SELECT ptn_no FROM #partitions WHERE seq_no = @i);
    SET @q = (SELECT N'ALTER TABLE '+@s+N'.'+@t+N' SPLIT RANGE ('+@p+N');');

    -- PRINT @q;
    EXECUTE sp_executesql @q;

    SET @i+=1;
END

-- Code clean-up

DROP TABLE #partitions;
```

この方法では、ソース管理のコードを静的なコードとして維持し、パーティション境界値は動的にすることが可能になるので、時間の経過に伴って、ウェアハウスとともに進化させることができます。

>[AZURE.NOTE]パーティションの切り替えには、SQL Server と異なる点がいくつかあります。[コードの移行][]に関するページを必ず読んで、この詳細を確認してください。


## 次のステップ
SQL Data Warehouse にデータベース スキーマを正常に移行したら、次の記事のいずれかに進むことができます。

- [データの移行][]
- [コードの移行][]

<!--Image references-->

<!-- Article references -->
[コードの移行]: sql-data-warehouse-migrate-code.md
[データの移行]: sql-data-warehouse-migrate-data.md
[統計]: sql-data-warehouse-develop-statistics.md
[ワークロード管理]: sql-data-warehouse-develop-concurrency.md

<!-- MSDN Articles -->

<!-- Other web references -->

<!---HONumber=July15_HO3-->