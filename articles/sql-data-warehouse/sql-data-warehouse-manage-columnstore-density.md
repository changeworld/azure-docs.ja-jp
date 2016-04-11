<properties
   pageTitle="テーブル分散傾斜の管理 | Microsoft Azure"
   description="分散テーブルの分散傾斜を確認する方法"
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
   ms.date="03/23/2016"
   ms.author="jrj;barbkess;sonyama"/>

# 列ストア インデックスの管理
列ストア インデックスは、Azure SQL Data Warehouse の柱となる要素です。インデックスを最適な状態に保つことで、システムのパフォーマンスが最大限に引き出されます。

この記事では、テーブルの列ストア インデックスのメタデータを調査する方法について説明します。これは、問題を迅速に診断し、解決するのに役立ちます。

## 列ストア メタデータの照会
列ストア インデックスの密度を把握するには、システム メタデータに対するクエリが必要です。確認できる情報の例を次に示します。

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

一度ビューを作成したら、列ストア メタデータは簡単に分析できます。クエリの例を次に示します。

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

## 列ストア密度の向上
クエリを実行したら、データ確認と結果の分析を開始することができます。

確認事項はいくつかあります。

| 分割 | このデータの使用方法 |
| ---------------------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| [table\_partition\_count] | テーブルがパーティション分割されている場合は、より多くの開いている行グループが表示されます。分散の各パーティションに、理論上、開いている行グループが関連付けられています。これを分析に組み込みます。パーティション分割されている小さなテーブルを最適化するには、パーティション分割をすべて削除して、圧縮を向上させます。 |
| [row\_count\_total] | テーブルの合計行数。この値は、たとえば、圧縮状態の行の割合を計算する際に使用できます。 |
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

## インデックスの管理
行グループを再圧縮するには、[CTAS][] を使用してパーティションを作成するか、[ALTER INDEX][] を使用してインデックス自体を再構築します。[CTAS][] は、通常、[ALTER INDEX][] よりも高速に実行されます。特に、大きなテーブルやパーティションに対してはこれが顕著です。ただし、小さなテーブルやパーティションについては、ほとんどの場合、[ALTER INDEX][] の方がはるかに便利です。

>[AZURE.NOTE] ALTER INDEX...REBUILD はオフライン操作です。ALTER INDEX...REORGANISE はオンライン操作です。ただし、REORGANISE は、開いている行グループには関与しません。開いている行グループを含めるには、ALTER INDEX...REBUILD が必要です。

インデックスを再構築すると、特に大規模なテーブルで、多くの場合、追加リソースのメリットを得られます。Azure SQL Data Warehouse にはワークロード管理機能が用意されており、この機能を使用して、より多くのメモリをユーザーに割り当てることができます。インデックスの再構築に対してより多くのメモリを予約する方法については、[同時実行][]の記事のワークロード管理のセクションを参照してください。

## 次のステップ
`CTAS` を使用したパーティションの再作成の詳細については、次の記事を参照してください。

* [テーブル パーティション][]
* [同時実行][]

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
[ALTER INDEX]: https://msdn.microsoft.com/ja-JP/library/ms188388.aspx


<!--Other Web references-->

<!---HONumber=AcomDC_0330_2016-->