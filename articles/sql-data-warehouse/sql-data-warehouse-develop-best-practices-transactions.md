<properties
   pageTitle="SQL Data Warehouse のトランザクションの最適化 | Microsoft Azure"
   description="Azure SQL Data Warehouse で効率的なトランザクションの更新を記述するためのベスト プラクティス ガイダンス"
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
   ms.author="jrj;barbkess"/>

# SQL Data Warehouse のトランザクションの最適化

この記事では、変更の効率が最大限に高まるようにトランザクション コードを作成する方法について説明します。

## トランザクションとログの概念について

トランザクションは、リレーショナル データベース エンジンの重要な要素です。SQL Data Warehouse では、データに変更を加える際にトランザクションを使用します。これらのトランザクションは、明示的に指定することも、暗黙的に指定することもできます。単独のステートメントで使用する `INSERT`、`UPDATE`、`DELETE` は、いずれも暗黙的なトランザクションの例です。明示的なトランザクションは、開発者が `BEGIN TRAN`、`COMMIT TRAN`、または `ROLLBACK TRAN` を使用して明示的に記述します。一般的には、複数の変更ステートメントを関連付けて 1 つのアトミック単位にする必要がある場合に使用します。

Azure SQL Data Warehouse では、トランザクション ログを使用してデータベースに変更をコミットします。ディストリビューションには、それぞれ独自のトランザクション ログがあります。トランザクション ログの書き込みは自動で行われるため、手動で構成する必要はありません。ただし、このプロセスでは書き込みが保証されず、システムにオーバーヘッドが加わります。この影響を最小限に抑えるには、トランザクションの効率を考慮してコードを記述してください。トランザクションの効率が良いコードは、大きく分けて 2 つのカテゴリに分類されます。

- 可能であれば、最小ログ記録コンストラクトを使用する
- 単独で実行時間の長いトランザクションを避けるために、範囲を制限したバッチを使用してデータを処理する
- 特定のパーティションに対する大規模な変更に対しては、パーティション切り替えパターンを使用する

## 最小ログ記録と完全ログ記録の比較
完全にログに記録される操作では、トランザクション ログにすべての行の変更が記録されるのに対して、最小限のログが記録される操作ではエクステントの割り当てとメタデータの変更のみが記録されます。そのため、最小ログ記録で行われるのは、障害や明示的な要求が発生した場合にトランザクションのロールバック (`ROLLBACK TRAN`) に必要となる情報の記録のみです。最小ログ記録操作では、トランザクション ログに記録される情報がはるかに少なくなるため、同じサイズの完全ログ記録操作よりも処理速度が速くなります。また、トランザクション ログへの書き込みが少なくなるため、生成されるログ データの量も少なくなり、I/O 効率が高くなります。

>[AZURE.NOTE] 最小ログ記録操作は、明示的なトランザクションに含めることができます。割り当て構造に対する変更はすべて記録されるため、最小ログ記録操作のロールバックが可能です。変更はログに記録されないのではなく "最小限" に抑えられる点を理解しておくことが重要です。

## 最小ログ記録操作

次の操作は、最小ログ記録が可能です。

- CREATE TABLE AS SELECT (CTAS)
- INSERT..SELECT
- CREATE INDEX
- ALTER INDEX REBUILD
- DROP INDEX
- TRUNCATE TABLE
- DROP TABLE
- ALTER TABLE SWITCH PARTITION

<!--
- MERGE
- UPDATE on LOB Types .WRITE
- SELECT..INTO
-->

## 一括読み込み操作に対する最小ログ記録の条件

`CTAS` と `INSERT...SELECT` はどちらも一括読み込み操作です。ただし、どちらもターゲットのテーブル定義や読み込みシナリオによる影響を受けます。次の表に、一括操作に対する最小ログ記録と完全ログ記録の違いを示します。

| プライマリ インデックス | 読み込みシナリオ | ログ モード |
| --------------------------- | -------------------------------------------------------- | ------------ |
| ヒープ | 任意 | **最小** |
| クラスター化インデックス | 空のターゲット テーブル | **最小** |
| クラスター化インデックス | 読み込まれる行がターゲットの既存のページと重複しない | **最小** |
| クラスター化インデックス | 読み込まれる行がターゲットの既存のページと重複する | 完全 |
| クラスター化列ストア インデックス | パーティションに合わせて整列されたディストリビューションあたりのバッチ サイズが 102,400 以上 | **最小** |
| クラスター化列ストア インデックス | パーティションに合わせて整列されたディストリビューションあたりのバッチ サイズが 102,400 未満 | 完全 |

セカンダリ インデックスや非クラスター化インデックスを更新するための書き込みは常に完全ログ記録操作である点に注意してください。

> [AZURE.IMPORTANT] SQL Data Warehouse には 60 のディストリビューションがあります。そのため、すべての行が均等に分散されると仮定すると、1 つのパーティションに格納される場合、クラスター化列ストア インデックスに書き込む際に最小ログ記録が適用されるには、バッチに 6,144,000 行以上を含める必要があります。テーブルがパーティション分割されていて、行がパーティション境界をまたいで挿入される場合は、すべての行が均等に分散されると仮定すると、パーティション境界あたり 6,144,000 行を含める必要があります。各ディストリビューション内の各パーティションに含める行は、ディストリビューションへの挿入に対する最小ログ記録のしきい値である 102,400 行を超える必要があります。

クラスター化インデックスを持つ空でないテーブルにデータを読み込むと、完全ログ記録の行と最小ログ記録の行が混在する場合がよくあります。クラスター化インデックスは、ページのバランス木 (B ツリー) です。既に書き込みが行われているページに別のトランザクションの行が含まれている場合、この書き込みは完全ログ記録になります。一方、ページが空の場合は、そのページへの書き込みは最小ログ記録になります。

## 完全ログ記録操作
完全ログ記録操作になるステートメントは、セカンダリ インデックスの更新以外にもあります。
 
`UPDATE` ステートメントと `DELETE` ステートメントは**常に**完全ログ記録操作です。

ただし、これらのステートメントは、最適化して、実行の効率を向上させることができます。

下の 4 つの例を用いて、完全ログ記録操作のコードを最適化する方法について説明します。

- `CTAS`
- テーブル パーティション
- バッチ操作

### CTAS を使用した大規模な削除操作の最適化
テーブルまたはパーティションから大量のデータを削除する必要がある場合は、それらを削除する代わりに、残しておきたいデータを `SELECT` する方が合理的です。具体的には、[CTAS][] を使用して新しいテーブルを作成します。テーブルを作成したら、[RENAME OBJECT][] コマンドのペアを使用して、テーブルの名前を切り替えます。

```sql
-- Delete all sales transactions for Promotions except PromotionKey 2.

--Step 01. Create a new table select only the records we want to kep (PromotionKey 2)
CREATE TABLE [dbo].[FactInternetSales_d]
WITH
(	CLUSTERED COLUMNSTORE INDEX
,	DISTRIBUTION = HASH([ProductKey])
, 	PARTITION 	(	[OrderDateKey] RANGE RIGHT 
									FOR VALUES	(	20000101, 20010101, 20020101, 20030101, 20040101, 20050101
												,	20060101, 20070101, 20080101, 20090101, 20100101, 20110101
												,	20120101, 20130101, 20140101, 20150101, 20160101, 20170101
												,	20180101, 20190101, 20200101, 20210101, 20220101, 20230101
												,	20240101, 20250101, 20260101, 20270101, 20280101, 20290101
												)
)
AS
SELECT 	*
FROM 	[dbo].[FactInternetSales]
WHERE	[PromotionKey] = 2
OPTION (LABEL = 'CTAS : Delete')
;

--Step 02. Rename the Tables to replace the 
RENAME OBJECT [dbo].[FactInternetSales]   TO [FactInternetSales_old];
RENAME OBJECT [dbo].[FactInternetSales_d] TO [FactInternetSales];
```

### CTAS を使用した大規模な更新の最適化
テーブルまたはパーティション内の多数の行を更新する必要がある場合は、[CTAS][] などの最小ログ記録操作を使用すると、効率が大幅に向上することがよくあります。

下の例では、最小ログ記録ができるように、テーブルの完全更新を `CTAS` に変換しています。

この例では、テーブル内の売上に割引金額をさかのぼって追加しています。

```sql
--Step 01. Create a new table containing the "Update". 
CREATE TABLE [dbo].[FactInternetSales_u]
WITH
(	CLUSTERED INDEX
,	DISTRIBUTION = HASH([ProductKey])
, 	PARTITION 	(	[OrderDateKey] RANGE RIGHT 
									FOR VALUES	(	20000101, 20010101, 20020101, 20030101, 20040101, 20050101
												,	20060101, 20070101, 20080101, 20090101, 20100101, 20110101
												,	20120101, 20130101, 20140101, 20150101, 20160101, 20170101
												,	20180101, 20190101, 20200101, 20210101, 20220101, 20230101
												,	20240101, 20250101, 20260101, 20270101, 20280101, 20290101
												)
				)
)
AS 
SELECT
	[ProductKey]  
,	[OrderDateKey] 
,	[DueDateKey]  
,	[ShipDateKey] 
,	[CustomerKey] 
,	[PromotionKey] 
,	[CurrencyKey] 
,	[SalesTerritoryKey]
,	[SalesOrderNumber]
,	[SalesOrderLineNumber]
,	[RevisionNumber]
,	[OrderQuantity]
,	[UnitPrice]
,	[ExtendedAmount]
,	[UnitPriceDiscountPct]
,	ISNULL(CAST(5 as float),0) AS [DiscountAmount]
,	[ProductStandardCost]
,	[TotalProductCost]
,	ISNULL(CAST(CASE WHEN [SalesAmount] <=5 THEN 0
		 ELSE [SalesAmount] - 5
		 END AS MONEY),0) AS [SalesAmount]
,	[TaxAmt]
,	[Freight]
,	[CarrierTrackingNumber] 
,	[CustomerPONumber]
FROM	[dbo].[FactInternetSales]
OPTION (LABEL = 'CTAS : Update')
;

--Step 02. Rename the tables
RENAME OBJECT [dbo].[FactInternetSales]   TO [FactInternetSales_old];
RENAME OBJECT [dbo].[FactInternetSales_u] TO [FactInternetSales];

--Step 03. Drop the old table
DROP TABLE [dbo].[FactInternetSales_old]
```

> [AZURE.NOTE] 大きなテーブルを作成し直す場合は、SQL Data Warehouse のワークロード管理機能が役立ちます。詳細については、[同時実行][]に関する記事の「ワークロード管理」セクションを参照してください。

### パーティション切り替えを用いたデータ更新
パーティション内で大規模な変更を加える場合は、パーティション切り替えパターンを使用すると効率的です。データが大幅に変更されていて、複数のパーティションにまたがっている場合は、それらのパーティションを反復処理するだけで同じ結果を達成できます。

パーティション切り替えを実行する手順は次のとおりです。
1. 空の out パーティションを作成します。
2. "更新" を CTAS で実行します。
3. 既存のデータを out テーブルからスイッチ アウトします。
4. 新しいデータをスイッチ インします。
5. データをクリーンアップします。

ただし、切り替えるパーティションを区別しやすいように、次に示すようなヘルパー プロシージャを最初に作成する必要があります。

```sql
CREATE PROCEDURE dbo.partition_data_get
	@schema_name		   NVARCHAR(128)
,	@table_name			   NVARCHAR(128)
,	@boundary_value		   INT
AS
IF OBJECT_ID('tempdb..#ptn_data') IS NOT NULL
BEGIN
	DROP TABLE #ptn_data
END
CREATE TABLE #ptn_data
WITH	(	DISTRIBUTION = ROUND_ROBIN
		,	HEAP
		)
AS
WITH CTE
AS
(
SELECT 	s.name							AS [schema_name]
,		t.name							AS [table_name]
, 		p.partition_number				AS [ptn_nmbr]
,		p.[rows]						AS [ptn_rows]
,		CAST(r.[value] AS INT)			AS [boundary_value]
FROM		sys.schemas					AS s
JOIN		sys.tables					AS t	ON  s.[schema_id]		= t.[schema_id]
JOIN		sys.indexes					AS i	ON 	t.[object_id]		= i.[object_id]
JOIN		sys.partitions				AS p	ON 	i.[object_id]		= p.[object_id] 
												AND i.[index_id]		= p.[index_id] 
JOIN		sys.partition_schemes		AS h	ON 	i.[data_space_id]	= h.[data_space_id]
JOIN		sys.partition_functions		AS f	ON 	h.[function_id]		= f.[function_id]
LEFT JOIN	sys.partition_range_values	AS r 	ON 	f.[function_id]		= r.[function_id] 
												AND r.[boundary_id]		= p.[partition_number]
WHERE i.[index_id] <= 1
)
SELECT	*
FROM	CTE
WHERE	[schema_name]		= @schema_name
AND		[table_name]		= @table_name
AND		[boundary_value]	= @boundary_value
OPTION (LABEL = 'dbo.partition_data_get : CTAS : #ptn_data')
;
GO
```

このプロシージャによって、コードの再利用が最大限に活かされ、パーティション切り替えの例がコンパクトになっています。

次のコードは、上記で説明した、完全なパーティション切り替えルーチンを実現する 5 つの手順を示しています。

```sql
--Create a partitioned aligned empty table to switch out the data 
IF OBJECT_ID('[dbo].[FactInternetSales_out]') IS NOT NULL
BEGIN
	DROP TABLE [dbo].[FactInternetSales_out]
END

CREATE TABLE [dbo].[FactInternetSales_out]
WITH
(	DISTRIBUTION = HASH([ProductKey])
,	CLUSTERED COLUMNSTORE INDEX
, 	PARTITION 	(	[OrderDateKey] RANGE RIGHT 
									FOR VALUES	(	20020101, 20030101
												)
				)
)
AS
SELECT *
FROM	[dbo].[FactInternetSales]
WHERE 1=2
OPTION (LABEL = 'CTAS : Partition Switch IN : UPDATE')
;

--Create a partitioned aligned table and update the data in the select portion of the CTAS
IF OBJECT_ID('[dbo].[FactInternetSales_in]') IS NOT NULL
BEGIN
	DROP TABLE [dbo].[FactInternetSales_in]
END

CREATE TABLE [dbo].[FactInternetSales_in]
WITH
(	DISTRIBUTION = HASH([ProductKey])
,	CLUSTERED COLUMNSTORE INDEX
, 	PARTITION 	(	[OrderDateKey] RANGE RIGHT 
									FOR VALUES	(	20020101, 20030101
												)
				)
)
AS 
SELECT
	[ProductKey]  
,	[OrderDateKey] 
,	[DueDateKey]  
,	[ShipDateKey] 
,	[CustomerKey] 
,	[PromotionKey] 
,	[CurrencyKey] 
,	[SalesTerritoryKey]
,	[SalesOrderNumber]
,	[SalesOrderLineNumber]
,	[RevisionNumber]
,	[OrderQuantity]
,	[UnitPrice]
,	[ExtendedAmount]
,	[UnitPriceDiscountPct]
,	ISNULL(CAST(5 as float),0) AS [DiscountAmount]
,	[ProductStandardCost]
,	[TotalProductCost]
,	ISNULL(CAST(CASE WHEN [SalesAmount] <=5 THEN 0
		 ELSE [SalesAmount] - 5
		 END AS MONEY),0) AS [SalesAmount]
,	[TaxAmt]
,	[Freight]
,	[CarrierTrackingNumber] 
,	[CustomerPONumber]
FROM	[dbo].[FactInternetSales]
WHERE	OrderDateKey BETWEEN 20020101 AND 20021231
OPTION (LABEL = 'CTAS : Partition Switch IN : UPDATE')
;

--Use the helper procedure to identify the partitions
--The source table
EXEC dbo.partition_data_get 'dbo','FactInternetSales',20030101
DECLARE @ptn_nmbr_src INT = (SELECT ptn_nmbr FROM #ptn_data)
SELECT @ptn_nmbr_src

--The "in" table
EXEC dbo.partition_data_get 'dbo','FactInternetSales_in',20030101
DECLARE @ptn_nmbr_in INT = (SELECT ptn_nmbr FROM #ptn_data)
SELECT @ptn_nmbr_in

--The "out" table
EXEC dbo.partition_data_get 'dbo','FactInternetSales_out',20030101
DECLARE @ptn_nmbr_out INT = (SELECT ptn_nmbr FROM #ptn_data)
SELECT @ptn_nmbr_out

--Switch the partitions over
DECLARE @SQL NVARCHAR(4000) = '
ALTER TABLE [dbo].[FactInternetSales]	SWITCH PARTITION '+CAST(@ptn_nmbr_src AS VARCHAR(20))	+' TO [dbo].[FactInternetSales_out] PARTITION '	+CAST(@ptn_nmbr_out AS VARCHAR(20))+';
ALTER TABLE [dbo].[FactInternetSales_in] SWITCH PARTITION '+CAST(@ptn_nmbr_in AS VARCHAR(20))	+' TO [dbo].[FactInternetSales] PARTITION '		+CAST(@ptn_nmbr_src AS VARCHAR(20))+';'
EXEC sp_executesql @SQL

--Perform the clean-up
TRUNCATE TABLE dbo.FactInternetSales_out;
TRUNCATE TABLE dbo.FactInternetSales_in;

DROP TABLE dbo.FactInternetSales_out
DROP TABLE dbo.FactInternetSales_in
DROP TABLE #ptn_data
```

### 管理可能なチャンクに対する一括データ変更操作
大規模なデータ変更操作の場合、操作をチャンクやバッチに分割して、作業単位のスコープを設定すると効率的になることがあります。

実際の例を次に示します。この手法を強調するために、バッチ サイズは小さい数値に設定されていますが、実際には、バッチ サイズはもっと大きい値になります。

```sql
SET NO_COUNT ON;
IF OBJECT_ID('tempdb..#t') IS NOT NULL
BEGIN
	DROP TABLE #t;
	PRINT '#t dropped';
END

CREATE TABLE #t
WITH	(	DISTRIBUTION = ROUND_ROBIN
		,	HEAP
		)
AS
SELECT	ROW_NUMBER() OVER(ORDER BY (SELECT NULL)) AS seq_nmbr
,		SalesOrderNumber
,		SalesOrderLineNumber
FROM	dbo.FactInternetSales
WHERE	[OrderDateKey] BETWEEN 20010101 and 20011231
;

DECLARE	@seq_start		INT = 1
,		@batch_iterator	INT = 1
,		@batch_size		INT = 50
,		@max_seq_nmbr	INT = (SELECT MAX(seq_nmbr) FROM dbo.#t)
;

DECLARE	@batch_count	INT = (SELECT CEILING((@max_seq_nmbr*1.0)/@batch_size))
,		@seq_end		INT = @batch_size
;

SELECT COUNT(*)
FROM	dbo.FactInternetSales f

PRINT 'MAX_seq_nmbr '+CAST(@max_seq_nmbr AS VARCHAR(20))
PRINT 'MAX_Batch_count '+CAST(@batch_count AS VARCHAR(20))

WHILE	@batch_iterator <= @batch_count
BEGIN
	DELETE
	FROM	dbo.FactInternetSales
	WHERE EXISTS
	(
			SELECT	1
			FROM	#t t
			WHERE	seq_nmbr BETWEEN  @seq_start AND @seq_end
			AND		FactInternetSales.SalesOrderNumber		= t.SalesOrderNumber
			AND		FactInternetSales.SalesOrderLineNumber	= t.SalesOrderLineNumber
	)
	;

	SET @seq_start = @seq_end
	SET @seq_end = (@seq_start+@batch_size);
	SET @batch_iterator +=1;
END
```

## "一時停止" 操作と "スケール" 操作に関する追加ガイダンス
Azure SQL Data Warehouse では、データ ウェアハウスの一時停止、再開、およびスケールをオンデマンドで実行できます。SQL Data Warehouse の一時停止またはスケールを実行すると、実行中のトランザクションは直ちに終了し、開いているトランザクションはロールバックされる点を理解しておくことが重要です。一時停止操作やスケール操作の前にワークロードによって時間のかかるデータ変更が発行されており、完了していない場合は、この作業を元に戻す必要があります。そのため、Azure SQL Data Warehouse データベースを完全に一時停止するのに長時間かかる場合があります。

> [AZURE.IMPORTANT] `UPDATE` と `DELETE` はどちらも完全ログ記録操作であるため、これらの元に戻す/再実行操作は、同等の最小ログ記録操作よりもはるかに長い時間かかることがあります。

最善の策としては、実行中のデータ変更トランザクションが完了してから、SQL Data Warehouse の一時停止またはスケールを実行します。ただし、それが実用的でない場合もあります。ロールバックに長時間かかる可能性を軽減するのに役立つ次のオプションを検討してください。

- 長時間かかる操作を [CTAS][] を使用して書き換える
- 操作をチャンクに分割し、行のサブセットに対して実行する

## 次のステップ
開発のヒントや上記の例に関連するコンテンツについては、次の記事を参照してください。

- [開発][]
- [トランザクション][]
- [テーブル パーティション][]
- [同時実行][]
- [CTAS][]
- [RENAME OBJECT][]

<!--Image references-->

<!--ACOM references-->
[開発]: sql-data-warehouse-overview-develop.md
[トランザクション]: sql-data-warehouse-develop-transactions.md
[テーブル パーティション]: sql-data-warehouse-develop-table-partitions.md
[table partition]: sql-data-warehouse-develop-table-partitions.md
[同時実行]: sql-data-warehouse-develop-concurrency.md
[CTAS]: sql-data-warehouse-develop-ctas.md
[RENAME OBJECT]: sql-data-warehouse-develop-rename.md

<!--MSDN references-->
[alter index]: https://msdn.microsoft.com/ja-JP/library/ms188388.aspx

<!---HONumber=AcomDC_0330_2016------>