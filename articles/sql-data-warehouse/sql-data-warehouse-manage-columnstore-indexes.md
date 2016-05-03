<properties
   pageTitle="Azure SQL Data Warehouse で列ストア インデックスを管理する | Microsoft Azure"
   description="Azure SQL Data Warehouse での圧縮とクエリのパフォーマンスを向上させるために列ストア インデックスを管理するためのチュートリアルです。"
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
   ms.date="04/07/2016"
   ms.author="jrj;barbkess;sonyama"/>

# Azure SQL Data Warehouse で列ストア インデックスを管理する

このチュートリアルでは、クエリ パフォーマンスを向上させるために列ストア インデックスを管理する方法について説明します。

列ストア インデックスのクエリは、インデックスが行を 100 万 (正確には 1,048,576) 行の "行グループ" にまとめて圧縮した場合に、最適な動作を行います。これにより、圧縮とクエリ パフォーマンスが最適化されます。ただし、行グループの行が 100 万より大幅に少なくなる条件が発生する可能性があります。行グループに行が高密度に含まれない場合は、調整を行うことを検討してください。

このチュートリアルで学習する内容は次のとおりです。

- メタデータを使用して、行グループあたりの行の平均数を判断する
- 行グループの行が少なくなる根本原因を考える
- すべての行を新しい行グループに再圧縮するよう、列ストア インデックスを再構築する 

列ストア インデックスの基本事項については、「[列ストア インデックスの説明](https://msdn.microsoft.com/library/gg492088.aspx)」を参照してください。

## 手順 1: 行グループのメタデータを表示するビューを作成する

このビューでは、行グループあたりの平均行数が計算されます。また、行グループに関する追加情報も表示されます。

```sql
CREATE VIEW dbo.vColumnstoreDensity
AS
WITH CSI
AS
(
SELECT
        SUBSTRING(@@version,34,4)                                               AS [build_number]
,       GETDATE()                                                               AS [execution_date]
,       DB_Name()                                                               AS [database_name]
,       t.name                                                                  AS [table_name]
,		COUNT(DISTINCT rg.[partition_number])									AS [table_partition_count]
,       SUM(rg.[total_rows])                                                    AS [row_count_total]
,       SUM(rg.[total_rows])/COUNT(DISTINCT rg.[distribution_id])               AS [row_count_per_distribution_MAX]
,		CEILING	(	(SUM(rg.[total_rows])*1.0/COUNT(DISTINCT rg.[distribution_id])
						)/1048576
				)																AS [rowgroup_per_distribution_MAX]
,       SUM(CASE WHEN rg.[State] = 1 THEN 1                   ELSE 0    END)    AS [OPEN_rowgroup_count]
,       SUM(CASE WHEN rg.[State] = 1 THEN rg.[total_rows]     ELSE 0    END)    AS [OPEN_rowgroup_rows]
,       MIN(CASE WHEN rg.[State] = 1 THEN rg.[total_rows]     ELSE NULL END)    AS [OPEN_rowgroup_rows_MIN]
,       MAX(CASE WHEN rg.[State] = 1 THEN rg.[total_rows]     ELSE NULL END)    AS [OPEN_rowgroup_rows_MAX]
,       AVG(CASE WHEN rg.[State] = 1 THEN rg.[total_rows]     ELSE NULL END)    AS [OPEN_rowgroup_rows_AVG]
,       SUM(CASE WHEN rg.[State] = 2 THEN 1                   ELSE 0    END)    AS [CLOSED_rowgroup_count]
,       SUM(CASE WHEN rg.[State] = 2 THEN rg.[total_rows]     ELSE 0    END)    AS [CLOSED_rowgroup_rows]
,       MIN(CASE WHEN rg.[State] = 2 THEN rg.[total_rows]     ELSE NULL END)    AS [CLOSED_rowgroup_rows_MIN]
,       MAX(CASE WHEN rg.[State] = 2 THEN rg.[total_rows]     ELSE NULL END)    AS [CLOSED_rowgroup_rows_MAX]
,       AVG(CASE WHEN rg.[State] = 2 THEN rg.[total_rows]     ELSE NULL END)    AS [CLOSED_rowgroup_rows_AVG]
,       SUM(CASE WHEN rg.[State] = 3 THEN 1                   ELSE 0    END)    AS [COMPRESSED_rowgroup_count]
,       SUM(CASE WHEN rg.[State] = 3 THEN rg.[total_rows]     ELSE 0    END)    AS [COMPRESSED_rowgroup_rows]
,       SUM(CASE WHEN rg.[State] = 3 THEN rg.[deleted_rows]   ELSE 0    END)    AS [COMPRESSED_rowgroup_rows_DELETED]
,       MIN(CASE WHEN rg.[State] = 3 THEN rg.[total_rows]     ELSE NULL END)    AS [COMPRESSED_rowgroup_rows_MIN]
,       MAX(CASE WHEN rg.[State] = 3 THEN rg.[total_rows]     ELSE NULL END)    AS [COMPRESSED_rowgroup_rows_MAX]
,       AVG(CASE WHEN rg.[State] = 3 THEN rg.[total_rows]     ELSE NULL END)    AS [COMPRESSED_rowgroup_rows_AVG]
FROM    sys.[pdw_nodes_column_store_row_groups] rg
JOIN    sys.[pdw_nodes_tables] nt                   ON  rg.[object_id]          = nt.[object_id]
                                                    AND rg.[pdw_node_id]        = nt.[pdw_node_id]
                                                    AND rg.[distribution_id]    = nt.[distribution_id]
JOIN    sys.[pdw_table_mappings] mp                 ON  nt.[name]               = mp.[physical_name]
JOIN    sys.[tables] t                              ON  mp.[object_id]          = t.[object_id]
GROUP BY
        t.[name]
)
SELECT  *
FROM    CSI
;
```

## 手順 2: ビューをクエリする

これでビューを作成できたので、このサンプル クエリを実行して、列ストア インデックスの行グループ メタデータを表示します。

```sql
SELECT	[table_name]
,		[table_partition_count]
,		[row_count_total]
,		[row_count_per_distribution_MAX]
,		[COMPRESSED_rowgroup_rows]
,		[COMPRESSED_rowgroup_rows_AVG]
,		[COMPRESSED_rowgroup_rows_DELETED]
,		[COMPRESSED_rowgroup_count]
,		[OPEN_rowgroup_count]
,		[OPEN_rowgroup_rows]
,		[CLOSED_rowgroup_count]
,		[CLOSED_rowgroup_rows]
FROM	[dbo].[vColumnstoreDensity]
WHERE	[table_name] = 'FactInternetSales'
```

## 手順 3: 結果を分析する

クエリを実行したら、データの確認と結果の分析を開始できます。次の表では、行グループ分析で確認する項目について説明します。


| 分割 | このデータの使用方法 |
| ---------------------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| [table\_partition\_count] | テーブルがパーティション分割されている場合は、より多くの開いている行グループが表示されます。分散の各パーティションに、理論上、開いている行グループが関連付けられています。これを分析に組み込みます。パーティション分割されている小さなテーブルを最適化するには、パーティション分割をすべて削除して、圧縮を向上させます。 |
| [row\_count\_total] | テーブルの合計行数。たとえば、この値を使用して、圧縮状態の行の割合を計算できます。 |
| [row\_count\_per\_distribution\_MAX] | すべての行が均等に分散されている場合、この値は、分散あたりの目標行数になります。この値を compressed\_rowgroup\_count と比較します。 |
| [COMPRESSED\_rowgroup\_rows] | テーブルの列ストア形式の合計行数。 |
| [COMPRESSED\_rowgroup\_rows\_AVG] | 平均行数が行グループの最大行数を大幅に下回る場合は、CTAS または ALTER INDEX REBUILD を使用してデータを再圧縮することを検討してください。 |
| [COMPRESSED\_rowgroup\_count] | 列ストア形式の行グループの数。この数がテーブルに対して非常に多い場合は、列ストアの密度が低いことを示します。 |
| [COMPRESSED\_rowgroup\_rows\_DELETED] | 行が列ストア形式で論理的に削除されます。この数がテーブル サイズと比べて多い場合は、パーティションを再作成するか、インデックスを再構築して、行を物理的に削除することを検討してください。 |
| [COMPRESSED\_rowgroup\_rows\_MIN] | この値と、AVG 列および MAX 列を組み合わせて使用して、列ストアの行グループに対する値の範囲を把握します。読み込みのしきい値 (パーティションに合わせて整列された分散あたり 102,400) をわずかに上回っている場合は、データ読み込みで最適化が可能であることを示します。 |
| [COMPRESSED\_rowgroup\_rows\_MAX] | 上記と同じ。 |
| [OPEN\_rowgroup\_count] | 開いている行グループは正常です。テーブル分散ごとに 1 つの行グループが開いていることが期待できます (60)。数が多すぎる場合は、パーティションをまたいでデータを読み込むことをお勧めします。パーティション分割の計画が適切であることを再確認してください。 |
| [OPEN\_rowgroup\_rows] | 各行グループには最大で 1,048,576 行を含めることができます。この値は、開いている行グループの使用率を確認するときに使用します。 |
| [OPEN\_rowgroup\_rows\_MIN] | 開いているグループは、テーブルに読み込まれたデータが少量であることか、以前の読み込みで残りの行がこの行グループにあふれたことを示します。MIN、MAX、AVG の各列を使用して、開いている行グループに含まれるデータ量を確認します。テーブルが小さい場合は、100% になることもあります。 この場合は、ALTER INDEX REBUILD でデータを列ストアに強制的に移動します。 |
| [OPEN\_rowgroup\_rows\_MAX] | 上記と同じ。 |
| [OPEN\_rowgroup\_rows\_AVG] | 上記と同じ。 |
| [CLOSED\_rowgroup\_rows] | サニティ チェックとして、終了した行グループの行を確認します。 |
| [CLOSED\_rowgroup\_count] | 終了した行グループの数は少なくします (値が示されている場合)。終了した行グループを圧縮行グループに変換するには、ALTER INDEX ...REORGANISE コマンドを使用します。ただし、通常、これは必要ありません。終了したグループを自動的に列ストアの行グループに変換するには、バックグラウンドの "組ムーバー" プロセスを使用します。 |
| [CLOSED\_rowgroup\_rows\_MIN] | 終了した行グループのフィル レートは非常に高くなります。終了した行グループのフィル レートが低い場合は、列ストアをさらに分析する必要があります。 |
| [CLOSED\_rowgroup\_rows\_MAX] | 上記と同じ。 |
| [CLOSED\_rowgroup\_rows\_AVG] | 上記と同じ。 |


## 手順 4: 根本原因を調べる

根本原因を調べると、行グループ内の行密度を向上させるためのテーブル デザイン、読み込み、またはその他のプロセス変更を行えるかどうかを知るのに役立ちます。これらの変更により、圧縮とクエリのパフォーマンスが向上します。

これらの根本原因により、列ストア インデックスの列グループあたりの行が 1,048,576 より大幅に少なくなる可能性があります。また、行が圧縮行グループではなくデルタ行グループに移動される可能性もあります。

1. 高負荷 DML 操作
2. 小規模または少量の読み込み操作
3. 多すぎるパーティション

### 高負荷 DML 操作** 

行を更新および削除する高負荷 DML 操作では、列ストアの効率性が低下します。これは、行グループ内の多数の行が変更される場合に特に当てはまります。

- 圧縮行グループから行を削除すると、行は論理的にのみ削除済みとしてマークされます。パーティションまたはテーブルが再構築されるまで、行は圧縮行グループに残ります。
- 行を挿入すると、行はデルタ行グループと呼ばれる内部の行ストア テーブルに追加されます。挿入された行は、デルタ行グループがいっぱいになりクローズとしてマークされるまで、列ストアに変換されません。行グループは、1,048,576 行の最大容量に到着した時点で閉じられます。 
- 列ストア形式での行の更新は、論理的な削除、挿入として処理されます。挿入された行は、デルタ ストアに格納されます。

パーティションに合わせて整列された分散あたりの一括しきい値 102,400 行を超えるバッチ更新および挿入操作は、列ストア形式に直接書き込まれます。ただし、そのためには、均等な分散を想定すると、1 回の操作で 6,144,000 を超える行を変更する必要があります。パーティションに合わせて整列された特定の分散の行数が 102,400 未満である場合、行はデルタ ストアに移動されます。これらの行は、十分な行が挿入または変更されて行グループが閉じられるか、またはインデックスが再構築されるまで、その場所に残ります。

### 小規模または少量の読み込み操作

SQL Data Warehouse にフローする小規模な読み込みは、少量の読み込みとも呼ばれます。通常、これらの読み込みは、システムによってインジェストされるほぼ一定のデータ ストリームを表します。ただし、このストリームはほぼ連続的であるため、行の量はあまり多くありません。多くの場合、データは、列ストア形式への直接読み込みに必要なしきい値を大幅に下回ります。

こうした状況では、多くの場合、データを最初に Azure BLOB ストレージに配置し、読み込む前に蓄積する方が適切です。この手法は、多くの場合に*マイクロ バッチ処理*と呼ばれます。

### 多すぎるパーティション

パーティションが多すぎる場合があります。超並列処理 (MPP) アーキテクチャでは、1 つのユーザー定義テーブルが、内部的に 60 のテーブルとして配布および実装されます。そのため、すべての列ストア インデックスが、60 の列ストア インデックスとして実装されます。同様に、すべてのパーティションが、これらの 60 の列ストア インデックスにわたって実装されます。これは、対称型マルチプロセッシング (SMP) アーキテクチャを持つ SQL Server とは**大きく異なります**。

SMP の SQL Server テーブルの 1 つのパーティションに 1,048,576 行を読み込む場合は、列ストアに圧縮されます。ただし、SQL Data Warehouse テーブルの 1 つのパーティションに 1,048,576 行を読み込む場合は、60 の各分散に 17,467 行のみが配置されます (データの均等な分布を想定)。17,467 は分散あたりのしきい値 102,400 行を下回るので、SQL Data Warehouse ではデルタ ストアの行グループ内にデータが保持されます。そのため、圧縮列ストア形式に行を直接読み込むには、SQL Data Warehouse テーブルの単一のパーティションに 6,144,400 を超える行 (60 x 102,400) を読み込む必要があります。

## 手順 5: 追加のコンピューティング リソースを割り当てる

特に大規模なテーブルでインデックスを再構築するには、多くの場合、追加リソースが必要になります。SQL Data Warehouse にはワークロード管理機能が用意されており、より多くのメモリをユーザーに割り当てることができます。インデックスの再構築に対してより多くのメモリを予約する方法については、[同時実行][]の記事のワークロード管理のセクションを参照してください。

## 手順 6: 行グループあたりの平均行数を向上させるためにインデックスを再構築する

既存の圧縮行グループを結合し、デルタ行グループを列ストアに強制的に移動するには、インデックスを再構築する必要があります。通常、インデックス全体を再構築するにはデータが多すぎるので、1 つまたは複数のパーティションを再構築することをお勧めします。SQL Data Warehouse では、次の 2 つの方法のいずれかでインデックスを再構築できます。

1. [ALTER INDEX][] を使用してパーティションを再構築します。
2. [CTAS][] を使用して、パーティションを新しいテーブルに再作成し、パーティションの切り替えを使用して、パーティションを元のテーブルに戻します。

最良の方法はどちらでしょうか。 大量のデータでは、通常、[CTAS][] の方が [ALTER INDEX][] より高速です。より少量のデータでは、[ALTER INDEX][] の方が簡単に使用できます。

### 方法 1: ALTER INDEX を使用して、少量のデータをオフラインで再構築する

これらの例では、列ストア インデックス全体を再構築し、単一のパーティションを再構築する方法を示します。大規模なテーブルでは、単一のパーティションを再構築する操作のみが実用的です。これはオフライン操作です。

```sql
-- Rebuild the entire clustered index
ALTER INDEX ALL ON [dbo].[DimProduct] REBUILD
```

```sql
-- Rebuild a single partition
ALTER INDEX ALL ON [dbo].[FactInternetSales] REBUILD Partition = 5
```

詳細については、「[Columnstore Indexes Defragmentation (列ストア インデックスの最適化)](https://msdn.microsoft.com/library/dn935013.aspx#Anchor_1)」の「ALTER INDEX REBUILD」セクション、および構文トピック「[ALTER INDEX (Transact-SQL)](https://msdn.microsoft.com/library/ms188388.aspx)」を参照してください。

### 方法 2: CTAS を使用して、大量のデータの再構築およびパーティション切り替えをオンラインで行う

この例では、[CTAS][] とパーティション切り替えを使用して、テーブル パーティションを再構築します。


```sql
-- Step 01. Select the partition of data and write it out to a new table using CTAS
CREATE TABLE [dbo].[FactInternetSales_20000101_20010101]
    WITH    (   DISTRIBUTION = HASH([ProductKey])
            ,   CLUSTERED COLUMNSTORE INDEX
            ,   PARTITION   (   [OrderDateKey] RANGE RIGHT FOR VALUES
                                (20000101,20010101
                                )
                            )
            )
AS
SELECT  *
FROM    [dbo].[FactInternetSales]
WHERE   [OrderDateKey] >= 20000101
AND     [OrderDateKey] <  20010101
;

-- Step 02. Create a SWITCH out table
 
CREATE TABLE dbo.FactInternetSales_20000101
    WITH    (   DISTRIBUTION = HASH(ProductKey)
            ,   CLUSTERED COLUMNSTORE INDEX
            ,   PARTITION   (   [OrderDateKey] RANGE RIGHT FOR VALUES
                                (20000101
                                )
                            )
            )
AS
SELECT *
FROM    [dbo].[FactInternetSales]
WHERE   1=2 -- Note this table will be empty

-- Step 03. Switch OUT the data 
ALTER TABLE [dbo].[FactInternetSales] SWITCH PARTITION 2 TO  [dbo].[FactInternetSales_20000101] PARTITION 2;

-- Step 04. Switch IN the rebuilt data
ALTER TABLE [dbo].[FactInternetSales_20000101_20010101] SWITCH PARTITION 2 TO  [dbo].[FactInternetSales] PARTITION 2;
```

`CTAS` を使用してパーティションを再作成する方法の詳細については、[テーブル パーティション][]および[同時実行][]のセクションを参照してください。


## 次のステップ

インデックスの管理に関する詳細なアドバイスについては、[インデックス管理][]の記事を参照してください。

管理に関するその他のヒントについては、[管理][]の概要を参照してください。

<!--Image references-->

<!--Article references-->
[CTAS]: sql-data-warehouse-develop-ctas.md
[テーブル パーティション]: sql-data-warehouse-develop-table-partitions.md
[同時実行]: sql-data-warehouse-develop-concurrency.md
[管理]: sql-data-warehouse-manage-monitor.md
[インデックス管理]: sql-data-warehouse-manage-indexes.md

<!--MSDN references-->
[ALTER INDEX]: https://msdn.microsoft.com/library/ms188388.aspx


<!--Other Web references-->

<!---HONumber=AcomDC_0420_2016-->