<properties
   pageTitle="SQL Data Warehouse での統計の管理 | Microsoft Azure"
   description="ソリューション開発のための Azure SQL Data Warehouse での統計の管理に関するヒント。"
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
   ms.date="12/11/2015"
   ms.author="JRJ@BigBangData.co.uk;barbkess"/>

# SQL Data Warehouse での統計の管理
 SQL Data Warehouse では、統計を使用して分散クエリのさまざまな実行方法のコストを評価します。統計が正確であれば、クエリ オプティマイザーはクエリのパフォーマンスを向上させる高品質のクエリ プランを生成できます。

SQL Data Warehouse で実現されるクエリのパフォーマンスを得るためには、統計の作成と更新が重要となります。このガイドでは、統計の概要を説明し、次の方法を示します。

- データベース設計の一環として統計を作成する
- データベース メンテナンスの一環として統計を更新する
- システム ビューとシステム関数を使用して統計を表示する

## 統計の概要

単一列統計は、1 つの列の値の範囲と度数に関する情報を含むオブジェクトです。クエリ オプティマイザーでは、このヒストグラムを使用して、クエリ結果の行数を推定します。これは、クエリを最適化する方法に関する決定に直接影響します。

複数列統計は、列のリストで作成される統計です。この統計には、リストの最初の列の単一列統計に加え、密度と呼ばれる列間の相関関係情報が含まれます。複合結合やグループ化などの一部の操作では、複数列統計によってクエリのパフォーマンスを向上させることができます。

詳細については、MSDN の [DBCC SHOW\_STATISTICS][] に関するページをご覧ください。

## 統計が必要な理由
適切な統計がない場合、SQL Data Warehouse で実現されるパフォーマンスは得られません。SQL Data Warehouse では、テーブルと列の統計は自動的には生成されないので、開発者が自分で作成する必要があります。テーブルの作成時に統計を作成し、統計を設定したら統計を更新することをお勧めします。

> [AZURE.NOTE]SQL Server を使用する場合、必要に応じて単一列統計の作成と更新が自動的に実行されます。SQL Data Warehouse はこの点で異なります。SQL Data Warehouse では、データが分散しているため、すべての分散データにわたる統計が自動的に集計されるわけではありません。集計された統計が生成されるのは、開発者が統計を作成および更新したときだけです。

## 統計を作成する場合
最新の統計の一貫性のあるセットは、SQL Data Warehouse の重要な部分です。そのため、テーブルのデザインの一環として統計を作成することが重要です。

統計を開始する簡単な方法は、すべての列の単一列統計を作成することです。ただし、パフォーマンスと統計を作成および更新するコストの間には常にトレードオフの関係が存在します。すべての列の単一列統計を作成し、すべての統計を更新するのに時間がかかりすぎることが後でわかった場合は、統計の一部をいつでも削除できます。また、一部の統計を他の統計よりも頻繁に更新することもできます。

複数列統計は、列が複合結合句やグループ化句に含まれる場合にのみ、クエリ オプティマイザーで使用されます。現在、複合フィルターでは複数列統計のメリットは得られません。

そのため、SQL Data Warehouse の開発を開始するときは、次のパターンを実装することをお勧めします。- すべてのテーブルのすべての列の単一列統計を作成します。- クエリの結合句とグループ化句で使用される列の複数列統計を作成します。

データを照会する方法を把握したら、特にテーブルの幅が広い場合に、このモデルを改良することもできます。さらに高度な手法については、「統計管理の実装」(## 統計管理の実装) をご覧ください。

## 統計を更新する場合
統計の更新をデータベース管理ルーチンに含めることが重要です。データベース内のデータの分布が変わったら、統計を更新する必要があります。そうしないと、クエリのパフォーマンスが十分に最適化されない可能性があり、労力をかけてクエリのトラブルシューティングをさらに行うだけの価値がなくなります。

したがって、クエリのトラブルシューティングを行うときに最初に尋ねる質問の 1 つは、「統計は最新の状態ですか」です。

この質問は時間で答えることができるものではありません。最新の統計オブジェクトが非常に古い可能性もあります。特定の列の値の分布で行数やデータが変わった場合は、統計を更新する必要があります。

たとえば、データ ウェアハウスの日付列では、通常、統計を頻繁に更新する必要があります。新しい行がデータ ウェアハウスに読み込まれるたびに、新しい読み込みの日付またはトランザクションの日付が追加されます。これらによってデータの分布が変わり、統計が古くなります。

一方、顧客テーブルの性別列の統計は更新する必要がないと考えられます。顧客間で分布が一定であると仮定すると、テーブル バリエーションに新しい行を追加しても、データの分布が変わることはありません。ただし、データ ウェアハウスに 1 つの性別しか含まれておらず、新しい要件によって複数の性別が含まれるようになった場合は、性別列の統計を更新する必要があることは明らかです。

詳細については、MSDN の「[統計][]」をご覧ください。

## 統計管理の実装

多くの場合、読み込みの終わりに統計が確実に更新されるように、データ読み込みプロセスを拡張することが推奨されます。テーブルのサイズや値の分布が変わる頻度が最も高いのがデータの読み込み時です。したがって、これが管理プロセスを実装する論理的な場所となります。

読み込みプロセスで統計を更新する際の基本原則は、次のとおりです。

- 読み込まれた各テーブルに更新された統計オブジェクトが少なくとも 1 つは含まれていることを確認します。これにより、統計の更新の一環として、テーブル サイズ (行数とページ数) 情報が更新されます。
- JOIN、GROUP BY、ORDER BY、DISTINCT の各句に関与している列を重視します。
- トランザクションの日付などの "昇順キー" 列の値は、統計ヒストグラムに含まれないため、これらの列の更新頻度を増やすことを検討します。
- 静的な分布列の更新頻度を減らすことを検討します。
- 各統計オブジェクトは系列で更新されることに注意してください。特に、多数の統計オブジェクトが含まれた幅の広いテーブルでは、`UPDATE STATISTICS <TABLE_NAME>` を実装するだけでは十分とはいえない場合があります。

> [AZURE.NOTE][昇順キー] の詳細については、SQL Server 2014 の基数推定モデルに関するホワイト ペーパーをご覧ください。

詳細については、MSDN の[基数推定][]に関するページをご覧ください。

## 例: 統計の作成

以下の例では、さまざまなオプションを使用して統計を作成する方法を示します。各列に使用するオプションは、データの特性とクエリでの列の使用方法によって異なります。

### A.既定のオプションを使用した単一列統計の作成

列の統計を作成するには、統計オブジェクトの名前と列の名前を指定するだけです。

次の構文では、既定のオプションをすべて使用しています。既定では、SQL Data Warehouse は統計を作成するときに、テーブルの 20% をサンプリングします。

```
CREATE STATISTICS [statistics_name] ON [schema_name].[table_name]([column_name]);
```

次に例を示します。

```
CREATE STATISTICS col1_stats ON dbo.table1 (col1);
```

### B.すべての列の検査による単一列統計の作成

ほとんどの場合、20% という既定のサンプリング レートで十分です。ただし、サンプリング レートを調整することもできます。

テーブル全体をサンプリングするには、次の構文を使用します。

```
CREATE STATISTICS [statistics_name] ON [schema_name].[table_name]([column_name]) WITH FULLSCAN;
```

次に例を示します。

```
CREATE STATISTICS col1_stats ON dbo.table1 (col1) WITH FULLSCAN;
```

### C.サンプル サイズを指定した単一列統計の作成

サンプル サイズをパーセントで指定することもできます。

```
CREATE STATISTICS col1_stats ON dbo.table1 (col1) WITH SAMPLE = 50 PERCENT;
```

### D.一部の行のみの単一列統計の作成

テーブルの一部の行の統計を作成することもできます。これは、フィルター選択された統計と呼ばれます。

たとえば、大規模なパーティション テーブルの特定のパーティションのクエリを計画するときに、フィルター選択された統計を使用できます。パーティション値のみで統計を作成すると、統計の精度が向上するので、クエリのパフォーマンスが向上します。

次の例では、値の範囲の統計を作成します。パーティションの値の範囲に一致する値を簡単に定義できます。

```
CREATE STATISTICS stats_col1 ON table1(col1) WHERE col1 > '2000101' AND col1 < '20001231';
```

> [AZURE.NOTE]クエリ オプティマイザーが分散クエリ プランを選択するときに、フィルター選択された統計の使用も考慮されるようにするには、クエリが統計オブジェクトの定義の範囲内である必要があります。前の例では、クエリの WHERE 句で col1 の値として 2000101 ～ 20001231 の値を指定する必要があります。

### E.すべてのオプションを使用した単一列統計の作成

オプションは組み合わせることができます。次の例では、カスタム サンプル サイズを指定してフィルター選択された統計オブジェクトを作成します。

```
CREATE STATISTICS stats_col1 ON table1 (col1) WHERE col1 > '2000101' AND col1 < '20001231' WITH SAMPLE = 50 PERCENT;
```

詳細については、MSDN の [CREATE STATISTICS][] に関するページをご覧ください。

### F.複数列統計の作成

複数列統計を作成するには、これまでの例を使用するだけですが、複数の列を指定します。

> [AZURE.NOTE]クエリ結果の行数の推定に使用されるヒストグラムは、統計オブジェクト定義に示されている最初の列にのみ使用できます。

次の例では、ヒストグラムは *product\_category* で使用されます。列間の統計は、*product\_category* と *product\_sub\_c\\ategory* で計算されます。

```
CREATE STATISTICS stats_2cols ON table1 (product_category, product_sub_category) WHERE product_category > '2000101' AND product_category < '20001231' WITH SAMPLE = 50 PERCENT;
```

*product\_category* と *product\_sub\_category* の間には相関関係があるため、これらの列に同時にアクセスする場合は複数列統計が役立ちます。

### G.テーブルのすべての列の統計の作成

統計を作成する方法の 1 つとして、テーブルの作成後に CREATE STATISTICS コマンドを発行します。

```
CREATE TABLE dbo.table1 
(
   col1 int
,  col2 int
,  col3 int
)
WITH
  (
    CLUSTERED COLUMNSTORE INDEX
  )
;

CREATE STATISTICS stats_col1 on dbo.table1 (col1);
CREATE STATISTICS stats_col2 on dbo.table2 (col2);
CREATE STATISTICS stats_col3 on dbo.table3 (col3);
```

### H.ストアド プロシージャを使用した、データベース内のすべての列の統計の作成

SQL Data Warehouse には、SQL Server の [sp\_create\_stats][] に相当するシステム ストアド プロシージャはありません。このストアド プロシージャは、まだ統計がないデータベースのすべての列の単一列統計オブジェクトを作成します。

これは、データベースの設計を開始する際に役立ちます。ニーズに合わせて、このオブジェクトを自由に変更できます。

```
CREATE PROCEDURE    [dbo].[prc_sqldw_create_stats]
(   @create_type    tinyint -- 1 default 2 Fullscan 3 Sample
,   @sample_pct     tinyint
)
AS

IF @create_type NOT IN (1,2,3)
BEGIN
    THROW 151000,'Invalid value for @stats_type parameter. Valid range 1 (default), 2 (fullscan) or 3 (sample).',1;
END;

IF @sample_pct IS NULL
BEGIN;
    SET @sample_pct = 20;
END;

IF OBJECT_ID('tempdb..#stats_ddl') IS NOT NULL
BEGIN;
	DROP TABLE #stats_ddl;
END;

CREATE TABLE #stats_ddl
WITH    (   DISTRIBUTION    = HASH([seq_nmbr])
        ,   LOCATION        = USER_DB
        )
AS
WITH T
AS
(
SELECT      t.[name]                        AS [table_name]
,           s.[name]                        AS [table_schema_name]
,           c.[name]                        AS [column_name]
,           c.[column_id]                   AS [column_id]
,           t.[object_id]                   AS [object_id]
,           ROW_NUMBER()
            OVER(ORDER BY (SELECT NULL))    AS [seq_nmbr]
FROM        sys.[tables] t
JOIN        sys.[schemas] s         ON  t.[schema_id]       = s.[schema_id]
JOIN        sys.[columns] c         ON  t.[object_id]       = c.[object_id]
LEFT JOIN   sys.[stats_columns] l   ON  l.[object_id]       = c.[object_id]
                                    AND l.[column_id]       = c.[column_id]
                                    AND l.[stats_column_id] = 1
LEFT JOIN	sys.[external_tables] e	ON	e.[object_id]		= t.[object_id]
WHERE       l.[object_id] IS NULL
AND			e.[object_id] IS NULL -- not an external table
)
SELECT  [table_schema_name]
,       [table_name]
,       [column_name]
,       [column_id]
,       [object_id]
,       [seq_nmbr]
,       CASE @create_type
        WHEN 1
        THEN    CAST('CREATE STATISTICS '+QUOTENAME('stat_'+table_schema_name+ '_' + table_name + '_'+column_name)+' ON '+QUOTENAME(table_schema_name)+'.'+QUOTENAME(table_name)+'('+QUOTENAME(column_name)+')' AS VARCHAR(8000))
        WHEN 2
        THEN    CAST('CREATE STATISTICS '+QUOTENAME('stat_'+table_schema_name+ '_' + table_name + '_'+column_name)+' ON '+QUOTENAME(table_schema_name)+'.'+QUOTENAME(table_name)+'('+QUOTENAME(column_name)+') WITH FULLSCAN' AS VARCHAR(8000))
        WHEN 3
        THEN    CAST('CREATE STATISTICS '+QUOTENAME('stat_'+table_schema_name+ '_' + table_name + '_'+column_name)+' ON '+QUOTENAME(table_schema_name)+'.'+QUOTENAME(table_name)+'('+QUOTENAME(column_name)+') WITH SAMPLE '+@sample_pct+'PERCENT' AS VARCHAR(8000))
        END AS create_stat_ddl
FROM T
;

DECLARE @i INT              = 1
,       @t INT              = (SELECT COUNT(*) FROM #stats_ddl)
,       @s NVARCHAR(4000)   = N''
;

WHILE @i <= @t
BEGIN
    SET @s=(SELECT create_stat_ddl FROM #stats_ddl WHERE seq_nmbr = @i);

    PRINT @s
    EXEC sp_executesql @s
    SET @i+=1;
END

DROP TABLE #stats_ddl;
```

このプロシージャを使用して、テーブルのすべての列の統計を作成するには、プロシージャを呼び出すだけです。

```
prc_sqldw_create_stats;
```

## 例: 統計の更新

統計を更新するには、次の操作を行います。

1. 統計オブジェクトを 1 つ更新します。更新する統計オブジェクトの名前を指定します。
2. テーブルのすべての統計オブジェクトを更新します。特定の統計オブジェクトではなく、テーブルの名前を指定します。


### A.1 つの特定の統計オブジェクトの更新 ###
特定の統計オブジェクトを更新するには、次の構文を使用します。

```
UPDATE STATISTICS [schema_name].[table_name]([stat_name]);
```

次に例を示します。

```
UPDATE STATISTICS [dbo].[table1] ([stats_col1]);
```

特定の統計オブジェクトを更新することで、統計を管理するために必要な時間とリソースを最小限に抑えることができます。ただし、この場合、更新する最適な統計オブジェクトの選択について少し検討する必要があります。


### B.テーブルのすべての統計の更新 ###
テーブルのすべての統計オブジェクトを更新する簡単な方法を次に示します。

```
UPDATE STATISTICS [schema_name].[table_name];
```

次に例を示します。

```
UPDATE STATISTICS dbo.table1;
```

これは使いやすいステートメントです。このステートメントはテーブルのすべての統計を更新するので、必要以上の処理が実行される可能性があります。パフォーマンスが問題でない場合は、これが、統計が最新の状態であることを保証する最も簡単で最も包括的な方法であることは確かです。

> [AZURE.NOTE]テーブルのすべての統計を更新する場合、SQL Data Warehouse では、統計ごとにテーブルのスキャンを実行してサンプリングします。テーブルが大きく、多数の列と統計が含まれている場合は、ニーズに基づいて個々の統計を更新する方が効率的です。

`UPDATE STATISTICS` プロシージャの実装については、[一時テーブル]に関する記事をご覧ください。実装方法は前述の `CREATE STATISTICS` プロシージャと若干異なりますが、最終的な結果は同じです。

完全な構文については、MSDN の [Update Statistics][] に関するページをご覧ください。

## 統計のメタデータ
統計に関する情報を確認する際に使用できるシステム ビューとシステム関数がいくつかあります。たとえば、stats-date 関数を使用して、統計が最後に作成または更新されたのがいつであるかを確認することで、統計オブジェクトが古くなっているかどうかがわかります。

### 統計のカタログ ビュー
次のシステム ビューは、統計に関する情報を提供します。

| カタログ ビュー | 説明 |
| :----------- | :---------- |
| [sys.columns][] | 列ごとに 1 行。 |
| [sys.objects][] | データベース内のオブジェクトごとに 1 行。 | |
| [sys.schemas][] | データベースのスキーマごとに 1 行。 | |
| [sys.stats][] | 統計オブジェクトごとに 1 行。 |
| [sys.stats\_columns][] | 統計オブジェクトの列ごとに 1 行。sys.columns にリンク。 |
| [sys.tables][] | テーブル (外部テーブルを含む) ごとに 1 行。 |
| [sys.table\_types][] | データ型ごとに 1 行。 |


### 統計のシステム関数
次のシステム関数は統計の操作に役立ちます。

| システム関数 | 説明 |
| :-------------- | :---------- |
| [STATS\_DATE][] | 統計オブジェクトの最終更新日。 |
| [DBCC SHOW\_STATISTICS][] | 統計オブジェクトで認識される値の分布に関する概要レベルの情報と詳細情報を提供します。 |

### 1 つのビューへの統計列と関数の統合

このビューには、統計に関連する列と、[STATS\_DATE()][] 関数の結果が一緒に表示されます。

```
CREATE VIEW dbo.vstats_columns
AS
SELECT
        sm.[name]                           AS [schema_name]
,       tb.[name]                           AS [table_name]
,       st.[name]                           AS [stats_name]
,       st.[filter_definition]              AS [stats_filter_defiinition]
,       st.[has_filter]                     AS [stats_is_filtered]
,       STATS_DATE(st.[object_id],st.[stats_id])
                                            AS [stats_last_updated_date]
,       co.[name]                           AS [stats_column_name]
,       ty.[name]                           AS [column_type]
,       co.[max_length]                     AS [column_max_length]
,       co.[precision]                      AS [column_precision]
,       co.[scale]                          AS [column_scale]
,       co.[is_nullable]                    AS [column_is_nullable]
,       co.[collation_name]                 AS [column_collation_name]
,       QUOTENAME(sm.[name])+'.'+QUOTENAME(tb.[name])
                                            AS two_part_name
,       QUOTENAME(DB_NAME())+'.'+QUOTENAME(sm.[name])+'.'+QUOTENAME(tb.[name])
                                            AS three_part_name
FROM    sys.objects                         AS ob
JOIN    sys.stats           AS st ON    ob.[object_id]      = st.[object_id]
JOIN    sys.stats_columns   AS sc ON    st.[stats_id]       = sc.[stats_id]
                            AND         st.[object_id]      = sc.[object_id]
JOIN    sys.columns         AS co ON    sc.[column_id]      = co.[column_id]
                            AND         sc.[object_id]      = co.[object_id]
JOIN    sys.types           AS ty ON    co.[user_type_id]   = ty.[user_type_id]
JOIN    sys.tables          AS tb ON  co.[object_id]        = tb.[object_id]
JOIN    sys.schemas         AS sm ON  tb.[schema_id]        = sm.[schema_id]
WHERE   1=1 
AND     st.[user_created] = 1
;
```

## DBCC SHOW\_STATISTICS() の例

DBCC SHOW\_STATISTICS() は、統計オブジェクト内に保持されているデータを表示します。このデータは 3 つの部分で提供されます。

1. ヘッダー
2. 密度ベクトル
3. ヒストグラム

ヘッダーには、統計に関するメタデータが含まれます。ヒストグラムには、統計オブジェクトの最初のキー列の値の分布が表示されます。密度ベクトルは、列間の相関関係を測定します。SQLDW では、統計オブジェクト内のデータを使用して基数推定値を計算します。

### ヘッダー、密度、ヒストグラムの表示

次の簡単な例は、統計オブジェクトの 3 つの部分をすべて表示します。

```
DBCC SHOW_STATISTICS([<schema_name>.<table_name>],<stats_name>)
```

次に例を示します。

```
DBCC SHOW_STATISTICS (dbo.table1, stats_col1);
```

### DBCC SHOW\_STATISTICS(); の 1 つ以上の部分の表示

特定の部分だけを表示する場合は、`WITH` 句を使用して表示する部分を指定します。

```
DBCC SHOW_STATISTICS([<schema_name>.<table_name>],<stats_name>) WITH stat_header, histogram, density_vector
```

次に例を示します。

```
DBCC SHOW_STATISTICS (dbo.table1, stats_col1) WITH histogram, density_vector
```

## DBCC SHOW\_STATISTICS() の相違点
SQL Server に比べ、SQL Data Warehouse では、DBCC SHOW\_STATISTICS() がより厳密に実装されています。

1. ドキュメントに記載されていない機能はサポートされていません。
- Stats\_stream は使用できません。
- 統計データの特定のサブセットの結果を結合することはできません (STAT\_HEADER JOIN DENSITY\_VECTOR など)。
2. メッセージを抑制するために、NO\_INFOMSGS を設定することはできません。
3. 統計名を囲む角かっこは使用できません。
4. 列名を使用して、統計オブジェクトを識別することはできません。
5. カスタム エラー 2767 はサポートされていません。


## 次のステップ
開発に関するその他のヒントについては、[SQL Data Warehouse の開発の概要][]に関するページをご覧ください。

<!--Image references-->

<!--Link references--In actual articles, you only need a single period before the slash.-->
[SQL Data Warehouse の開発の概要]: ./sql-data-warehouse-overview-develop.md
[一時テーブル]: ./sql-data-warehouse-develop-temporary-tables.md

<!-- External Links -->
[基数推定]: https://msdn.microsoft.com/library/dn600374.aspx
[CREATE STATISTICS]: https://msdn.microsoft.com/library/ms188038.aspx
[DBCC SHOW\_STATISTICS]: https://msdn.microsoft.com/library/ms174384.aspx
[統計]: https://msdn.microsoft.com/library/ms190397.aspx
[STATS\_DATE]: https://msdn.microsoft.com/library/ms190330.aspx
[sys.columns]: https://msdn.microsoft.com/library/ms176106.aspx
[sys.objects]: https://msdn.microsoft.com/library/ms190324.aspx
[sys.schemas]: https://msdn.microsoft.com/library/ms190324.aspx
[sys.stats]: https://msdn.microsoft.com/library/ms177623.aspx
[sys.stats\_columns]: https://msdn.microsoft.com/library/ms187340.aspx
[sys.tables]: https://msdn.microsoft.com/library/ms187406.aspx
[sys.table\_types]: https://msdn.microsoft.com/library/bb510623.aspx
[UPDATE STATISTICS]: https://msdn.microsoft.com/library/ms187348.aspx

<!---HONumber=AcomDC_1217_2015-->