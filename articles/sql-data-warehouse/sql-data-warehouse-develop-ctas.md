<properties
   pageTitle="SQL Data Warehouse の CREATE TABLE AS SELECT (CTAS) | Microsoft Azure"
   description="ソリューションの開発のために、Azure SQL Data Warehouse の CREATE TABLE AS SELECT (CTAS) ステートメントでコーディングする際のヒントです。"
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
   ms.date="06/26/2015"
   ms.author="JRJ@BigBangData.co.uk;barbkess"/>

# SQL Data Warehouse での CREATE TABLE AS SELECT (CTAS)
CREATE TABLE AS SELECT (CTAS) は、使用可能な T-SQL 機能の中でも最も重要なものの 1 つです。これは、SELECT ステートメントの出力に基づいて新しいテーブルを作成するという、完全に並列化された操作です。SELECT..INTO の高性能バージョンだと考えてください。

CTAS を使用すると、サポートされていない機能の多くに対応することもできます。この機能は、ユーザーのコードに対応できるというだけでなく、SQL Data Warehouse 上でより高速に実行されるという効果があります。これは、完全に並列化された設計により可能になりました。

> [AZURE.NOTE]「まず最初に CTAS」を検討しましょう。CTAS によって問題を解決できると思われる場合は、たとえ結果的に多くのコードを作成することになったとしても、通常は CTAS を使用するのが最善の方法です。

CTAS で対処できるシナリオは次のとおりです。

- SELECT..INTO
- ANSI JOINS を使用した UPDATE 
- ANSI JOIN を使用した DELETE
- MERGE ステートメント

このドキュメントには、CTAS を使用してコーディングする際のベスト プラクティスも含まれます。

## SELECT..INTO
SELECT..INTO は、ソリューション内で頻繁に使用される場合があります。

SELECT..INTO の例を次に示します。

```
SELECT *
INTO    #tmp_fct
FROM    [dbo].[FactInternetSales]
```

これを CTAS に変換するのはとても簡単です。

```
CREATE TABLE #tmp_fct
WITH
(
    DISTRIBUTION = ROUND_ROBIN
,   LOCATION = USER_DB
)
AS
SELECT  *
FROM    [dbo].[FactInternetSales]
;
```

CTAS を使用すると、データ配布の設定を指定したり、テーブルのインデックス作成 (省略可能) もできます。

## UPDATE ステートメントの代わりに使用する ANSI JOIN

UPDATE または DELETE を実行するための ANSI JOIN 構文を使用して、2 つ以上のテーブルを結合するのは、複雑な更新方法だと思われるかもしれません。

このテーブルを更新しなければならない状況だと想定します。

```
CREATE TABLE [dbo].[AnnualCategorySales]
(	[EnglishProductCategoryName]	NVARCHAR(50)	NOT NULL
,	[CalendarYear]					SMALLINT		NOT NULL
,	[TotalSalesAmount]				MONEY			NOT NULL
)
WITH
(
	DISTRIBUTION = ROUND_ROBIN
)
;
```

元のクエリは次のようだったとします。

```
UPDATE	acs
SET		[TotalSalesAmount] = [fis].[TotalSalesAmount]
FROM	[dbo].[AnnualCategorySales] 	AS acs
JOIN	(
		SELECT	[EnglishProductCategoryName]
		,		[CalendarYear]
		,		SUM([SalesAmount])				AS [TotalSalesAmount]
		FROM	[dbo].[FactInternetSales]		AS s
		JOIN	[dbo].[DimDate]					AS d	ON s.[OrderDateKey]				= d.[DateKey]
		JOIN	[dbo].[DimProduct]				AS p	ON s.[ProductKey]				= p.[ProductKey]
		JOIN	[dbo].[DimProductSubCategory]	AS u	ON p.[ProductSubcategoryKey]	= u.[ProductSubcategoryKey]
		JOIN	[dbo].[DimProductCategory]		AS c	ON u.[ProductCategoryKey]		= c.[ProductCategoryKey]
		WHERE 	[CalendarYear] = 2004
		GROUP BY
				[EnglishProductCategoryName]
		,		[CalendarYear]
		) AS fis
ON	[acs].[EnglishProductCategoryName]	= [fis].[EnglishProductCategoryName]
AND	[acs].[CalendarYear]				= [fis].[CalendarYear]
;
```

SQL Data Warehouse では ANSI JOIN はサポートされていないため、変更を加えないとこのコードをコピーできません。

CTAS と暗黙的結合を組み合わせて使用することで、このコードを置き換えます。

```
-- Create an interim table
CREATE TABLE CTAS_acs
WITH (DISTRIBUTION = ROUND_ROBIN)
AS
SELECT	ISNULL(CAST([EnglishProductCategoryName] AS NVARCHAR(50)),0)	AS [EnglishProductCategoryName]
,		ISNULL(CAST([CalendarYear] AS SMALLINT),0) 						AS [CalendarYear]
,		ISNULL(CAST(SUM([SalesAmount]) AS MONEY),0)						AS [TotalSalesAmount]
FROM	[dbo].[FactInternetSales]		AS s
JOIN	[dbo].[DimDate]					AS d	ON s.[OrderDateKey]				= d.[DateKey]
JOIN	[dbo].[DimProduct]				AS p	ON s.[ProductKey]				= p.[ProductKey]
JOIN	[dbo].[DimProductSubCategory]	AS u	ON p.[ProductSubcategoryKey]	= u.[ProductSubcategoryKey]
JOIN	[dbo].[DimProductCategory]		AS c	ON u.[ProductCategoryKey]		= c.[ProductCategoryKey]
WHERE 	[CalendarYear] = 2004
GROUP BY
		[EnglishProductCategoryName]
,		[CalendarYear]
;

-- Use an implicit join to perform the update 
UPDATE  AnnualCategorySales
SET     AnnualCategorySales.TotalSalesAmount = CTAS_ACS.TotalSalesAmount
FROM    CTAS_acs
WHERE   CTAS_acs.[EnglishProductCategoryName] = AnnualCategorySales.[EnglishProductCategoryName]
AND     CTAS_acs.[CalendarYear]               = AnnualCategorySales.[CalendarYear]
;

--Drop the interim table
DROP TABLE CTAS_acs
;
```

## DELETE ステートメントの代わりに使用する ANSI JOIN
CTAS がデータ削除の際に最良のアプローチとなることもあります。単にデータを削除するのではなく、保持したいデータを選択するということです。これを実行できるのが、ANSI JOIN 構文を使用した DELETE ステートメントです。この機能は SQL Data Warehouse ではサポートされていません。

変換された DELETE ステートメントの例を次に示します。

```
CREATE TABLE dbo.DimProduct_upsert
WITH 
(   Distribution=HASH(ProductKey)
,   CLUSTERED INDEX (ProductKey)
)
AS -- Select Data you wish to keep
SELECT     p.ProductKey
,          p.EnglishProductName
,          p.Color
FROM       dbo.DimProduct p 
RIGHT JOIN dbo.stg_DimProduct s 
ON         p.ProductKey = s.ProductKey
;

RENAME OBJECT dbo.DimProduct        TO DimProduct_old;
RENAME OBJECT dbo.DimProduct_upsert TO DimProduct;
```

## MERGE ステートメントの代わりに使用
MERGE ステートメントは、少なくとも部分的に CTAS で置き換えることができます。`INSERT` および `UPDATE` を 1 つのステートメントに統合できます。削除されたレコードはすべて、2 つ目のステートメントで閉じる必要があります。

`UPSERT` の例を次に示します。

```
CREATE TABLE dbo.[DimProduct_upsert]
WITH 
(   DISTRIBUTION = HASH([ProductKey])
,   CLUSTERED INDEX ([ProductKey])
)
AS 
-- New rows and new versions of rows
SELECT      s.[ProductKey]
,           s.[EnglishProductName]
,           s.[Color]
FROM      dbo.[stg_DimProduct] AS s
UNION ALL  
-- Keep rows that are not being touched
SELECT      p.[ProductKey]
,           p.[EnglishProductName]
,           p.[Color]
FROM      dbo.[DimProduct] AS p
WHERE NOT EXISTS
(   SELECT  *
    FROM    [dbo].[stg_DimProduct] s
    WHERE   s.[ProductKey] = p.[ProductKey]
)
;

RENAME OBJECT dbo.[DimProduct]          TO [DimProduct_old];
RENAME OBJECT dbo.[DimpProduct_upsert]  TO [DimProduct];

```

## CTAS での推奨事項: 明示的にデータ型および出力の null 値の許容を示す

コードを移行するときに、このようなコーディング パターンが見られる場合があります。

```
DECLARE @d decimal(7,2) = 85.455
,       @f float(24)    = 85.455

CREATE TABLE result
(result DECIMAL(7,2) NOT NULL
)
WITH (DISTRIBUTION = ROUND_ROBIN)

INSERT INTO result
SELECT @d*@f 
;
```

このコードを CTAS に移行することが正しいと思われるかもしれません。しかし、これには問題があります。

次のコードからは、同じ結果が得られないからです。

```
DECLARE @d decimal(7,2) = 85.455
,       @f float(24)    = 85.455
;

CREATE TABLE ctas_r
WITH (DISTRIBUTION = ROUND_ROBIN)
AS
SELECT @d*@f as result
;
```

「result」の列では、式のデータ型と null 値の許容が引き継がれます。注意しないと、これによって結果にわずかな差異が出る場合があります。

次の例を試してください。

```
SELECT result,result*@d
from result
;

SELECT result,result*@d
from ctas_r
;
```

結果 (result) に格納される値が異なります。result 列の永続化された値が他の式で使用されるため、エラーの深刻度が増します。

![][1]

これは、データを移行する場合に特に重要になります。2 番目のクエリのほうが正確ではありますが、問題があります。ソース システムと比較するとデータが異なるため、移行中の整合性に疑問が出てきます。これは、「正しくない」結果が実は正しいという珍しいケースなのです。

この 2 つの結果に差異があるのは、暗黙の型変換が原因です。1 つ目の例の場合、テーブルで列が定義されています。行が挿入されると、暗黙の型変換が実行されます。2 つ目の例では、列のデータ型が式で定義されているため、暗黙の型変換は実行されません。2 つ目の例の列が null 許容の列として定義されている一方で、1 つ目の例の列はそのように定義されていないことにも注意してください。1 つ目の例でテーブルが作成されるとき、列の null 値の許容は明示的に定義されました。2 つ目の例では、列の null 値の許容は定義されていないため、既定で null 定義になります。

これらの問題を解決するには、 CTAS ステートメントの SELECT 部分に、型の変換と null 値の許容を明示的に設定する必要があります。これらのプロパティは、CREATE TABLE 部分で設定することはできません。

次の例で、このコードの修正方法を示しています。

```
DECLARE @d decimal(7,2) = 85.455
,       @f float(24)    = 85.455

CREATE TABLE ctas_r
WITH (DISTRIBUTION = ROUND_ROBIN)
AS
SELECT ISNULL(CAST(@d*@f AS DECIMAL(7,2)),0) as result
```

次のことに注意してください: - CAST または CONVERT を使用することもできます - null 値が許容されるよう COALESCE ではなく ISNULL が使用されています - ISNULL は最も外側の関数です - ISNULL の 2 番目の部分は 0 などの定数です。

> [AZURE.NOTE]null 値の許容を正しく設定するために、COALESCE ではなく ISNULL を使用することは重要です。COALESCE は確定的な関数ではないため、式の結果には常に null 値が使用されるからです。ISNULL の場合は異なります。ISNULL は確定的な関数です。そのため、ISNULL 関数の 2 番目の部分が定数またはリテラルの場合、結果の値は NOT NULL になります。

このヒントは、計算の整合性を確保する上で役立つだけではありません。テーブル パーティションの切り替えでも重要になります。このテーブルが、ファクトとして定義されていると考えてください。

```
CREATE TABLE [dbo].[Sales]
(
    [date]      INT     NOT NULL
,   [product]   INT     NOT NULL
,   [store]     INT     NOT NULL
,   [quantity]  INT     NOT NULL
,   [price]     MONEY   NOT NULL
,   [amount]    MONEY   NOT NULL
)
WITH 
(   DISTRIBUTION = HASH([product])
,   PARTITION   (   [date] RANGE RIGHT FOR VALUES
                    (20000101,20010101,20020101
                    ,20030101,20040101,20050101
                    )
                )
) 
;
```

ただし、値フィールドは計算された式で、ソース データの一部ではありません。

パーティション分割されたデータセットを作成するには、次を実行してください。

```
CREATE TABLE [dbo].[Sales_in]
WITH    
(   DISTRIBUTION = HASH([product])
,   PARTITION   (   [date] RANGE RIGHT FOR VALUES
                    (20000101,20010101
                    )
                )
)
AS
SELECT
    [date]    
,   [product] 
,   [store] 
,   [quantity]
,   [price]   
,   [quantity]*[price]  AS [amount]
FROM [stg].[source]
OPTION (LABEL = 'CTAS : Partition IN table : Create')
;
```

クエリは、問題なく実行されます。問題が発生するのは、パーティションの切り替えを実行するときです。テーブルの定義が一致しないためです。テーブルの定義を一致させるには、CTAS を変更する必要があります。

```
CREATE TABLE [dbo].[Sales_in]
WITH    
(   DISTRIBUTION = HASH([product])
,   PARTITION   (   [date] RANGE RIGHT FOR VALUES
                    (20000101,20010101
                    )
                )
)
AS
SELECT
    [date]    
,   [product] 
,   [store] 
,   [quantity]
,   [price]   
,   ISNULL(CAST([quantity]*[price] AS MONEY),0) AS [amount]
FROM [stg].[source]
OPTION (LABEL = 'CTAS : Partition IN table : Create');
```

そのため、CTAS では、型の一貫性と null 値の許容プロパティを保持することが、エンジニアリング上のベスト プラクティスだということがわかります。計算の整合性を維持するのに役立つほか、パーティションの切り替えも確実に実行できるからです。

[CTAS][] の使用方法に関する詳細については、MSDN を参照してください。これは、Azure SQL Data Warehouse で最も重要なステートメントの 1 つです。よく理解しておいてください。

## 次のステップ
開発のその他のヒントについては、[開発の概要][]に関するページをご覧ください。

<!--Image references-->
[1]: media/sql-data-warehouse-develop-ctas/ctas-results.png

<!--Article references-->
[開発の概要]: sql-data-warehouse-overview-develop.md

<!--MSDN references-->
[CTAS]: https://msdnstage.redmond.corp.microsoft.com/ja-jp/library/mt204041.aspx

<!--Other Web references-->

<!---HONumber=July15_HO3-->