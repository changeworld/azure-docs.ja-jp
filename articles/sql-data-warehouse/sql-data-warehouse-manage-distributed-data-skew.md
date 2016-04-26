<properties
   pageTitle="Azure SQL Data Warehouse での分散テーブルのデータ傾斜を管理する | Microsoft Azure"
   description="Azure SQL Data Warehouse でのハッシュ分散テーブルの分散全体で行が均等に分散していない場合は、データ傾斜を検索して修正します。" 
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

# Azure SQL Data Warehouse での分散テーブルのデータ傾斜を管理する
このチュートリアルでは、ハッシュ分散テーブルのデータ傾斜を識別し、問題の修正に関する推奨事項を説明します。

ハッシュ分散方法でテーブル データを配布するとき、一部の配布が傾斜し、配布間でデータ量が不均衡になることがあります。過度のデータ傾斜は、最も時間のかかる配布が終了するまで、分散クエリの最終的な結果が準備完了でないためにクエリのパフォーマンスに影響を与えることがあります。データ傾斜の程度によっては対処が必要になる場合があります。

このチュートリアルでは、次のことについて説明します。

- メタデータを使用して、どのテーブルにデータ傾斜があるかを特定する
- データ傾斜をいつ解決するか判断するためのヒントを確認する
- テーブルを再作成して、データ傾斜を解決する


## 手順 1: データ傾斜を検索するビューを作成する

このビューを作成し、どのテーブルにデータ傾斜があるかを識別します。

```sql
CREATE VIEW dbo.vDistributionSkew
AS
WITH base
AS
(
SELECT 
	SUBSTRING(@@version,34,4)															AS  [build_number]
,	GETDATE()																			AS  [execution_time]
,	DB_NAME()																			AS  [database_name]
,	s.name																				AS  [schema_name]
,	t.name																				AS  [table_name]
,	QUOTENAME(s.name)+'.'+QUOTENAME(t.name)												AS  [two_part_name]
,	nt.[name]																			AS  [node_table_name]
,	ROW_NUMBER() OVER(PARTITION BY nt.[name] ORDER BY (SELECT NULL))					AS  [node_table_name_seq]
,	tp.[distribution_policy_desc]														AS  [distribution_policy_name]
,	nt.[distribution_id]																AS  [distribution_id]
,	nt.[pdw_node_id]																	AS  [pdw_node_id]
,	pn.[type]																			AS	[pdw_node_type]
,	pn.[name]																			AS	[pdw_node_name]
,	nps.[partition_number]																AS	[partition_nmbr]
,	nps.[reserved_page_count]															AS	[reserved_space_page_count]
,	nps.[reserved_page_count] - nps.[used_page_count]									AS	[unused_space_page_count]
,	nps.[in_row_data_page_count] 
	+ nps.[row_overflow_used_page_count] + nps.[lob_used_page_count]					AS  [data_space_page_count]
,	nps.[reserved_page_count] 
	- (nps.[reserved_page_count] - nps.[used_page_count]) 
	- ([in_row_data_page_count]+[row_overflow_used_page_count]+[lob_used_page_count])	AS  [index_space_page_count]
,	nps.[row_count]																		AS  [row_count]
from sys.schemas s
join sys.tables t								ON	s.[schema_id]			= t.[schema_id]
join sys.pdw_table_distribution_properties	tp	ON	t.[object_id]			= tp.[object_id]
join sys.pdw_table_mappings tm					ON	t.[object_id]			= tm.[object_id]
join sys.pdw_nodes_tables nt					ON	tm.[physical_name]		= nt.[name]
join sys.dm_pdw_nodes pn 						ON  nt.[pdw_node_id]		= pn.[pdw_node_id]
join sys.dm_pdw_nodes_db_partition_stats nps	ON	nt.[object_id]			= nps.[object_id]
												AND nt.[pdw_node_id]		= nps.[pdw_node_id]
												AND nt.[distribution_id]	= nps.[distribution_id]
)
, size
AS
(
SELECT	[build_number]
,		[execution_time]
,		[database_name]
,		[schema_name]
,		[table_name]
,		[two_part_name]
,		[node_table_name]
,		[node_table_name_seq]
,		[distribution_policy_name]
,		[distribution_id]
,		[pdw_node_id]
,		[pdw_node_type]
,		[pdw_node_name]
,		[partition_nmbr]
,		[reserved_space_page_count]
,		[unused_space_page_count]
,		[data_space_page_count]
,		[index_space_page_count]
,		[row_count]
,		([reserved_space_page_count] * 8.0)				AS [reserved_space_KB]
,		([reserved_space_page_count] * 8.0)/1000		AS [reserved_space_MB]
,		([reserved_space_page_count] * 8.0)/1000000		AS [reserved_space_GB]
,		([reserved_space_page_count] * 8.0)/1000000000	AS [reserved_space_TB]
,		([unused_space_page_count]   * 8.0)				AS [unused_space_KB]
,		([unused_space_page_count]   * 8.0)/1000		AS [unused_space_MB]
,		([unused_space_page_count]   * 8.0)/1000000		AS [unused_space_GB]
,		([unused_space_page_count]   * 8.0)/1000000000	AS [unused_space_TB]
,		([data_space_page_count]     * 8.0)				AS [data_space_KB]
,		([data_space_page_count]     * 8.0)/1000		AS [data_space_MB]
,		([data_space_page_count]     * 8.0)/1000000		AS [data_space_GB]
,		([data_space_page_count]     * 8.0)/1000000000	AS [data_space_TB]
,		([index_space_page_count]	 * 8.0)				AS [index_space_KB]
,		([index_space_page_count]	 * 8.0)/1000		AS [index_space_MB]
,		([index_space_page_count]	 * 8.0)/1000000		AS [index_space_GB]
,		([index_space_page_count]	 * 8.0)/1000000000	AS [index_space_TB]
FROM	base
)
SELECT	* 
FROM	size
;
```

## 手順 2: ビューをクエリする

ビューを作成したら、次の例のクエリを実行して、どのテーブルにデータ傾斜があるかを識別します。

```sql
SELECT	[two_part_name]
,		[distribution_id]
,		[row_count]
,		[reserved_space_GB]
,		[unused_space_GB]
,		[data_space_GB]
,		[index_space_GB]
FROM	[dbo].[vDistributionSkew]
WHERE	[table_name] = 'FactInternetSales'
ORDER BY [row_count] DESC
```

>[AZURE.NOTE] ROUND\_ROBIN で配布されたテーブルには傾斜はないはずです。計画的に、データがノード間で均等に配布されます。

## 手順 3: データ傾斜を解決する必要があるかを決定する

テーブルでデータ傾斜を解決する必要があるかを決定するには、ワークロード内のデータ ボリュームとクエリについて可能な限り理解する必要があります。

データの配布には、データ傾斜を最小限に抑え、データ移動を最小限に抑える適切なバランスを見つけることが重要です。これらは相反する目標となる場合がありますが、場合によってはデータ移動を抑えるためにデータ傾斜を保持する必要があります。たとえば、分散列が頻繁に結合および集計に共有の列である場合は、データ移動を最小限に抑えます。最小限のデータ移動による利点は、データ傾斜による影響を上回る可能性があることです。

## 手順 4: データ傾斜を解決する

データ傾斜を解決する方法は次の 2 つです。傾斜を解決する必要があることが決まっている場合は、次のいずれかを使用します。

### 方法 1: 他の分散列を含むテーブルを再作成する

データ傾斜を解決する一般的な方法は、他の分散列を含むテーブルを再作成することです。ハッシュ分散列の選択に関するガイダンスについては、「[Hash distribution (ハッシュ分散)][]」を参照してください。この例では、[CTAS][] を使用して、他の分散列を含むテーブルを再作成します。

```sql
CREATE TABLE [dbo].[FactInternetSales_CustomerKey] 
WITH (  CLUSTERED COLUMNSTORE INDEX
     ,  DISTRIBUTION =  HASH([CustomerKey])
     ,  PARTITION       ( [OrderDateKey] RANGE RIGHT FOR VALUES (   20000101, 20010101, 20020101, 20030101
                                                                ,   20040101, 20050101, 20060101, 20070101
                                                                ,   20080101, 20090101, 20100101, 20110101
                                                                ,   20120101, 20130101, 20140101, 20150101
                                                                ,   20160101, 20170101, 20180101, 20190101
                                                                ,   20200101, 20210101, 20220101, 20230101
                                                                ,   20240101, 20250101, 20260101, 20270101
                                                                ,   20280101, 20290101
                                                                )
                        )
    )
AS
SELECT  *
FROM    [dbo].[FactInternetSales]
OPTION  (LABEL  = 'CTAS : FactInternetSales_CustomerKey')
;

--Rename the objects
RENAME OBJECT [dbo].[FactInternetSales] TO [FactInternetSales_ProductKey];
RENAME OBJECT [dbo].[FactInternetSales_CustomerKey] TO [FactInternetSales];
```

### 方法 2: ラウンド ロビン分散を使用してテーブルを再作成する

この例では、ハッシュ分散ではなくラウンド ロビンを使用してテーブルを再作成します。この変更では、データ分散も生成されます。ただし、クエリのデータ移動は通常、増加します。

```sql
CREATE TABLE [dbo].[FactInternetSales_ROUND_ROBIN] 
WITH (  CLUSTERED COLUMNSTORE INDEX
     ,  DISTRIBUTION =  ROUND_ROBIN
     ,  PARTITION       ( [OrderDateKey] RANGE RIGHT FOR VALUES (   20000101, 20010101, 20020101, 20030101
                                                                ,   20040101, 20050101, 20060101, 20070101
                                                                ,   20080101, 20090101, 20100101, 20110101
                                                                ,   20120101, 20130101, 20140101, 20150101
                                                                ,   20160101, 20170101, 20180101, 20190101
                                                                ,   20200101, 20210101, 20220101, 20230101
                                                                ,   20240101, 20250101, 20260101, 20270101
                                                                ,   20280101, 20290101
                                                                )
                        )
    )
AS
SELECT  *
FROM    [dbo].[FactInternetSales]
OPTION  (LABEL  = 'CTAS : FactInternetSales_ROUND_ROBIN')
;

--Rename the objects
RENAME OBJECT [dbo].[FactInternetSales] TO [FactInternetSales_HASH];
RENAME OBJECT [dbo].[FactInternetSales_ROUND_ROBIN] TO [FactInternetSales];
```

## 次のステップ
テーブル分散に関する詳細については、次の記事を参照してください。

* [テーブル設計][]
* [ハッシュ分散][]

<!--Image references-->

<!--Article references-->
[テーブル設計]: sql-data-warehouse-develop-table-design.md
[Hash distribution (ハッシュ分散)]: sql-data-warehouse-develop-hash-distribution-key.md
[ハッシュ分散]: sql-data-warehouse-develop-hash-distribution-key.md

<!--MSDN references-->

<!--Other Web references-->

<!---HONumber=AcomDC_0413_2016-->