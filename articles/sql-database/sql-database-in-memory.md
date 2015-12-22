<properties
	pageTitle="SQL インメモリの使用 | Microsoft Azure"
	description="SQL インメモリは、トランザクション ワークロードと分析ワークロードのパフォーマンスを大幅に向上するテクノロジです。これらのテクノロジを活用する方法について説明します。"
	services="sql-database"
	documentationCenter=""
	authors="jodebrui"
	manager="jeffreyg"
	editor=""/>


<tags
	ms.service="sql-database"
	ms.workload="data-management"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="hero-article"
	ms.date="12/11/2015"
	ms.author="jodebrui"/>


# SQL Database でのインメモリ (プレビュー) の使用

インメモリ機能は、適切な状況でトランザクション ワークロードと分析ワークロードのパフォーマンスを大幅に向上させます。

このトピックでは、2 つのデモンストレーションに重点を置きます。1 つはインメモリ OLTP のデモ、もう 1 つはインメモリ分析のデモです。各デモでは、デモを実行するのに必要となる手順とコードを詳しく説明します。次のいずれかを実行できます。

- バリエーションをテストするコードを使用して、パフォーマンスの結果の違いを確認します。
- コードを読んで、シナリオを把握し、インメモリ オブジェクトの作成および利用の方法を確認します。

> [AZURE.VIDEO azure-sql-database-in-memory-technologies]

#### インメモリ OLTP

インメモリ [OLTP](#install_oltp_manuallink) (オンライン トランザクション処理) の機能は次のとおりです。

- メモリが最適化されたテーブル。
- ネイティブでコンパイルされたストアド プロシージャ。


メモリ最適化テーブルは、ハード ドライブ上の標準的な表現に加えて、アクティブ メモリ内にそれ自体の表現を持っています。ビジネス トランザクションはアクティブ メモリ内の表現のみと直接やり取りするので、テーブルに対するビジネス トランザクションの実行はより速くなります。

インメモリ OLTP を使用すると、ワークロードの詳細にもよりますが、トランザクションのスループットを最大で 30 倍向上させることができます。


ネイティブ コンパイル ストアド プロシージャでは、従来の解釈ストアド プロシージャのように作成した場合と比べて、実行時に必要とするマシン語命令の数が少なくて済みます。解釈期間の 1/100 である期間内にネイティブ コンパイルの結果を確認できました。


#### インメモリ分析 

インメモリ[分析](#install_analytics_manuallink)の機能は次のとおりです。

- 列ストア インデックス


列ストア インデックスは、特殊なデータ圧縮法によってクエリ ワークロードのパフォーマンスを向上させます。

その他のサービスでは、列ストア インデックスは必然的にメモリが最適化されたものとなります。ただし、Azure SQL Database では、列ストア インデックスは、インデックス付けされた従来のテーブルと共にハード ドライブ上に存在することができます。


#### リアルタイム分析

[リアルタイム分析](http://msdn.microsoft.com/library/dn817827.aspx)の場合は、インメモリ OLTP とインメモリ分析を組み合わせることで、次のことを可能にします。

- 運用データに基づくリアルタイムのビジネスの把握。


#### 可用性


完全一般公開 (GA)

- *ディスク上*の[列ストア インデックス](http://msdn.microsoft.com/library/dn817827.aspx)。


更新:

- インメモリ OLTP
- メモリ最適化列ストア インデックスを使用するインメモリ分析
- リアルタイム運用分析


プレビュー段階のインメモリ機能に関する考慮事項を、[このトピックの後の方で](#preview_considerations_for_in_memory)説明します。


> [AZURE.NOTE]このようなプレビュー段階の機能は [*Premium*](sql-database-service-tiers.md) Azure SQL データベースでのみ使用可能であり、Standard または Basic サービス レベルのデータベースでは使用できません。



<a id="install_oltp_manuallink" name="install_oltp_manuallink"></a>

&nbsp;

## A.インメモリ OLTP のサンプルをインストールする

[Azure ポータル](http://portal.azure.com/)で数回クリックするだけで、AdventureWorksLT [V12] のサンプル データベースを作成できます。このセクションの手順では、以下を使用して AdventureWorksLT データベースを強化する方法について説明します。

- インメモリ テーブル。
- ネイティブでコンパイルされたストアド プロシージャ。


#### インストール手順

1. [Azure ポータル](http://portal.azure.com/)で、V12 サーバー上に Premium データベースを作成します。**ソース**を AdventureWorksLT [V12] サンプル データベースに設定します。
 - 詳細な手順については、[最初の Azure SQL データベースの作成](sql-database-get-started.md)に関するページを参照してください。

2. SQL Server Management Studio [(SSMS.exe)](http://msdn.microsoft.com/library/mt238290.aspx) を使用して、データベースに接続します。

3. [インメモリ OLTP Transact-SQL スクリプト](http://raw.githubusercontent.com/Azure/azure-sql-database-samples/master/T-SQL/In-Memory/sql_in-memory_oltp_sample.sql)をクリップボードにコピーします。
 - この T-SQL スクリプトによって、手順 1. で作成した AdventureWorksLT サンプル データベース内に、必要なインメモリ オブジェクトが作成されます。

4. T-SQL スクリプトを SSMS に貼り付け、スクリプトを実行します。
 - 次のように、`MEMORY_OPTIMIZED = ON` 句の CREATE TABLE ステートメントが重要です。


```
CREATE TABLE [SalesLT].[SalesOrderHeader_inmem](
	[SalesOrderID] int IDENTITY NOT NULL PRIMARY KEY NONCLUSTERED ...,
	...
) WITH (MEMORY_OPTIMIZED = ON);
```


#### エラー 40536


T-SQL スクリプトを実行するときにエラー 40536 が発生する場合は、次の T-SQL スクリプトを実行し、データベースがインメモリをサポートしているかどうかを確認します。


```
SELECT DatabasePropertyEx(DB_Name(), 'IsXTPSupported');
```


結果が **0** の場合、インメモリがサポートされていないことを示します。1 の場合はサポートされています。問題を診断するには

- インメモリ OLTP 機能がプレビューとしてアクティブになった後にデータベースが作成されていることを確認します。
- データベースが Premium サービス レベルにあることを確認します。


#### 作成されるメモリ最適化項目の概要

**テーブル**: このサンプルには、次のメモリ最適化テーブルが含まれています。

- SalesLT.Product\_inmem
- SalesLT.SalesOrderHeader\_inmem
- SalesLT.SalesOrderDetail\_inmem
- Demo.DemoSalesOrderHeaderSeed
- Demo.DemoSalesOrderDetailSeed


SSMS で**オブジェクト エクスプローラー**を使用してメモリ最適化テーブルを確認できます。

- **[テーブル]** を右クリックし、**[フィルター]**、**[フィルターの設定]** の順に選択して、**[メモリ最適化]** が 1 であることを確認します。


または、次のようにカタログ ビューをクエリすることができます。


```
SELECT is_memory_optimized, name, type_desc, durability_desc
	FROM sys.tables
	WHERE is_memory_optimized = 1;
```


**ネイティブ コンパイル ストアド プロシージャ**: SalesLT.usp\_InsertSalesOrder\_inmem は、カタログ ビューのクエリを使用して確認できます。


```
SELECT uses_native_compilation, OBJECT_NAME(object_id), definition
	FROM sys.sql_modules
	WHERE uses_native_compilation = 1;
```


&nbsp;

## サンプルの OLTP ワークロードを実行する

次の 2 つの*ストアド プロシージャ*の違いは、1 つ目のプロシージャはメモリ最適化バージョンのテーブルを使用し、2 つ目のプロシージャは通常のディスク上のテーブルを使用している点です。

- SalesLT**.**usp\_InsertSalesOrder**\_inmem**
- SalesLT**.**usp\_InsertSalesOrder**\_ondisk**


このセクションでは、便利な **ostress.exe** ユーティリティを使用して、負荷が高い状態で 2 つのストアド プロシージャを実行する方法について説明します。2 つのストレス実行が完了するまでの時間を比較することができます。


ostress.exe を実行する場合、指定したパラメーター値を両方に渡すことをお勧めします。

- 多数の同時接続を実行するには、たとえば -n100 を使用します。
- 各接続を数百回ループさせるには、たとえば -r500 を使用します。


一方、すべてが適切に動作するようにするには、-n10、-r50 などの小さな値から始めることもできます。


### ostress.exe のスクリプト


このセクションでは、ostress.exe コマンド ラインに埋め込まれた T-SQL スクリプトを示します。このスクリプトでは、インストールした T-SQL スクリプトで作成されたアイテムを使用します。


次のスクリプトでは、5 行のアイテムがあるサンプルの販売注文を、次のメモリ最適化*テーブル*に挿入します。

- SalesLT.SalesOrderHeader\_inmem
- SalesLT.SalesOrderDetail\_inmem


```
DECLARE
	@i int = 0,
	@od SalesLT.SalesOrderDetailType_inmem,
	@SalesOrderID int,
	@DueDate datetime2 = sysdatetime(),
	@CustomerID int = rand() * 8000,
	@BillToAddressID int = rand() * 10000,
	@ShipToAddressID int = rand() * 10000;
	
INSERT INTO @od
	SELECT OrderQty, ProductID
	FROM Demo.DemoSalesOrderDetailSeed
	WHERE OrderID= cast((rand()*60) as int);
	
WHILE (@i < 20)
begin;
	EXECUTE SalesLT.usp_InsertSalesOrder_inmem @SalesOrderID OUTPUT,
		@DueDate, @CustomerID, @BillToAddressID, @ShipToAddressID, @od;
end
```


前述した ostress.exe の T-SQL の \_ondisk バージョンを作成するには、両方の *\_inmem* サブストリングを *\_ondisk* に置き換えます。この置換は、テーブルとストアド プロシージャの名前に影響があります。


### RML ユーティリティと ostress をインストールする


Azure VM で ostress.exe を実行する計画を立てるのが理想的です。AdventureWorksLT データベースがある Azure リージョンと同じリージョンに [Azure 仮想マシン](http://azure.microsoft.com/documentation/services/virtual-machines/)を作成します。代わりにノートパソコンで ostress.exe を実行することもできます。


VM または選択した任意のホストに、ostress.exe を含む Replay Markup Language (RML) ユーティリティをインストールします。

- [インメモリ OLTP のサンプル データベース](http://msdn.microsoft.com/library/mt465764.aspx)に関するページにある ostress.exe の説明を参照してください。
 - または、[インメモリ OLTP のサンプル データベース](http://msdn.microsoft.com/library/mt465764.aspx)に関するページを参照してください。
 - または、[ostress.exe のインストールに関するブログ](http://blogs.msdn.com/b/psssql/archive/2013/10/29/cumulative-update-2-to-the-rml-utilities-for-microsoft-sql-server-released.aspx)を参照してください。



<!--
dn511655.aspx is for SQL 2014,
[Extensions to AdventureWorks to Demonstrate In-Memory OLTP]
(http://msdn.microsoft.com/library/dn511655&#x28;v=sql.120&#x29;.aspx)

whereas for SQL 2016+
[Sample Database for In-Memory OLTP]
(http://msdn.microsoft.com/library/mt465764.aspx)
-->



### 最初に \_inmem stress ワークロードを実行する


*RML コマンド プロンプト* ウィンドウを使用して、ostress.exe コマンド ラインを実行できます。コマンド ライン パラメーターは ostress に次のことを行うように求めます。

- 100 個の接続を同時に実行する (-n100)。
- 各接続に T-SQL スクリプトを 50 回実行させる (-r50)。


```
ostress.exe -n100 -r50 -S<servername>.database.windows.net -U<login> -P<password> -d<database> -q -Q"DECLARE @i int = 0, @od SalesLT.SalesOrderDetailType_inmem, @SalesOrderID int, @DueDate datetime2 = sysdatetime(), @CustomerID int = rand() * 8000, @BillToAddressID int = rand() * 10000, @ShipToAddressID int = rand()* 10000; INSERT INTO @od SELECT OrderQty, ProductID FROM Demo.DemoSalesOrderDetailSeed WHERE OrderID= cast((rand()*60) as int); WHILE (@i < 20) begin; EXECUTE SalesLT.usp_InsertSalesOrder_inmem @SalesOrderID OUTPUT, @DueDate, @CustomerID, @BillToAddressID, @ShipToAddressID, @od; set @i += 1; end"
```


前述の ostress.exe コマンド ラインを実行するには:


1. SSMS で次のコマンドを実行してデータベースのデータ コンテンツをリセットし、前回の実行で挿入されたすべてのデータを削除します。```
EXECUTE Demo.usp_DemoReset;
```

2. 上記の ostress.exe コマンドラインのテキストをクリップボードにコピーします。

3. パラメーター -S -U -P -d の <placeholders> を実際の正しい値に置き換えます。

4. 編集したコマンドラインを RML コマンド ウィンドウで実行します。


#### 結果は期間


ostress.exe が完了すると、RML コマンド ウィンドウに表示される出力の最終行に実行時間が出力されます。たとえば、短いテストの場合、約 1.5 分かかります。

`11/12/15 00:35:00.873 [0x000030A8] OSTRESS exiting normally, elapsed time: 00:01:31.867`


#### リセット、\_ondisk の編集、再実行


\_inmem 実行の結果を取得したら、\_ondisk 実行に次の手順を実行します。


1. SSMS で次のコマンドを実行してデータベースをリセットし、前回の実行で挿入されたすべてのデータを削除します。```
EXECUTE Demo.usp_DemoReset;
```

2. ostress.exe コマンド ラインを編集して、すべての *\_inmem* を *\_ondisk* に置き換えます。

3. ostress.exe を再び実行し、期間の結果を取得します。

4. 大量のテスト データとなる可能性があるデータを確実に削除するために、もう一度データベースをリセットします。


#### 予想される比較結果

アウトインメモリ テストの結果、ostress をデータベースと同じ Azure リージョンにある Azure VM で実行した場合、この単純なワークロードではパフォーマンスが **9 倍**向上することがわかりました。


ネイティブでコンパイルされたストアド プロシージャへの変換を追加すると、パフォーマンスの改善レベルは高くなります。


## B.インメモリ分析のサンプルをインストールする


このセクションでは、列ストア インデックスと通常のインデックスを使用した場合の IO と統計情報の結果を比較します。


列ストア インデックスは、論理的には通常のインデックスと同じですが、物理的には違いがあります。列ストア インデックスは独自のデータ構成で大幅にデータを圧縮します。結果としてパフォーマンスが大幅に改善されます。


OLTP ワークロードのリアルタイム分析では、多くの場合、クラスター化されていない列ストア インデックスを使用するのが最適です。詳細については、「[列ストア インデックスの説明](http://msdn.microsoft.com/library/gg492088.aspx)」を参照してください。



### 列ストア分析テストを準備する


1. Azure ポータルを使用して、サンプルから最新の AdventureWorksLT データベースを作成します。
 - 正確な名前を使用します。
 - 任意の Premium サービス階層を選択します。

2. [sql\_in-memory\_analytics\_sample](http://raw.githubusercontent.com/Azure/azure-sql-database-samples/master/T-SQL/In-Memory/sql_in-memory_analytics_sample.sql) をクリップボードにコピーします。
 - この T-SQL スクリプトによって、手順 1. で作成した AdventureWorksLT サンプル データベース内に、必要なインメモリ オブジェクトが作成されます。
 - このスクリプトでは、Dimension テーブルと 2 つの fact テーブルを作成します。fact テーブルには、それぞれ 350 万行のデータが設定されています。
 - スクリプトが完了するには約 15 分かかります。

3. T-SQL スクリプトを SSMS に貼り付け、スクリプトを実行します。
 - 次のように、**CREATE INDEX** ステートメントの **COLUMNSTORE** キーワードが重要です。<br/>`CREATE NONCLUSTERED COLUMNSTORE INDEX ...;`

4. AdventureWorksLT を互換性レベル 130 に設定します。<br/>`ALTER DATABASE AdventureworksLT SET compatibility_level = 130;`
 - レベル 130 はインメモリ機能に直接、関係ありません。しかし、レベル 130 は一般に、120 の場合よりも高いクエリ パフォーマンスを提供します。


#### 重要なテーブルと列ストア インデックス


- dbo.FactResellerSalesXL\_CCI は、クラスター化された**列ストア** インデックスがあるテーブルです。これは、*データ* レベルで高度に圧縮されています。

- dbo.FactResellerSalesXL\_PageCompressed は、通常のクラスター化されたインデックスと同等のテーブルです。*ページ* レベルでのみ圧縮されます。


#### 列ストア インデックスを比較する重要なクエリ


パフォーマンスの改善を確認できるいくつかの T-SQL クエリの種類については、[こちら](http://raw.githubusercontent.com/Azure/azure-sql-database-samples/master/T-SQL/In-Memory/clustered_columnstore_sample_queries.sql)を参照してください。T-SQL スクリプトの手順 2 には、直接関係がある 1 組のクエリがあります。2 つのクエリの違いは、次の 1 行のみです。


- `FROM FactResellerSalesXL_PageCompressed a`
- `FROM FactResellerSalesXL_CCI a`


クラスター化された列ストア インデックスは FactResellerSalesXL**\_CCI** テーブルにあります。

次の T-SQL スクリプトの抜粋では、各テーブルのクエリの IO と TIME の統計情報を出力します。


```
/*********************************************************************
Step 2 -- Overview
-- Page Compressed BTree table v/s Columnstore table performance differences
-- Enable actual Query Plan in order to see Plan differences when Executing
*/
-- Ensure Database is in 130 compatibility mode
ALTER DATABASE AdventureworksLT SET compatibility_level = 130
GO

-- Execute a typical query that joins the Fact Table with dimension tables
-- Note this query will run on the Page Compressed table, Note down the time
SET STATISTICS IO ON
SET STATISTICS TIME ON
GO

SELECT c.Year
	,e.ProductCategoryKey
	,FirstName + ' ' + LastName AS FullName
	,count(SalesOrderNumber) AS NumSales
	,sum(SalesAmount) AS TotalSalesAmt
	,Avg(SalesAmount) AS AvgSalesAmt
	,count(DISTINCT SalesOrderNumber) AS NumOrders
	,count(DISTINCT a.CustomerKey) AS CountCustomers
FROM FactResellerSalesXL_PageCompressed a
INNER JOIN DimProduct b ON b.ProductKey = a.ProductKey
INNER JOIN DimCustomer d ON d.CustomerKey = a.CustomerKey
Inner JOIN DimProductSubCategory e on e.ProductSubcategoryKey = b.ProductSubcategoryKey
INNER JOIN DimDate c ON c.DateKey = a.OrderDateKey
WHERE e.ProductCategoryKey =2
	AND c.FullDateAlternateKey BETWEEN '1/1/2014' AND '1/1/2015'
GROUP BY e.ProductCategoryKey,c.Year,d.CustomerKey,d.FirstName,d.LastName
GO
SET STATISTICS IO OFF
SET STATISTICS TIME OFF
GO


-- This is the same Prior query on a table with a Clustered Columnstore index CCI 
-- The comparison numbers are even more dramatic the larger the table is, this is a 11 million row table only.
SET STATISTICS IO ON
SET STATISTICS TIME ON
GO
SELECT c.Year
	,e.ProductCategoryKey
	,FirstName + ' ' + LastName AS FullName
	,count(SalesOrderNumber) AS NumSales
	,sum(SalesAmount) AS TotalSalesAmt
	,Avg(SalesAmount) AS AvgSalesAmt
	,count(DISTINCT SalesOrderNumber) AS NumOrders
	,count(DISTINCT a.CustomerKey) AS CountCustomers
FROM FactResellerSalesXL_CCI a
INNER JOIN DimProduct b ON b.ProductKey = a.ProductKey
INNER JOIN DimCustomer d ON d.CustomerKey = a.CustomerKey
Inner JOIN DimProductSubCategory e on e.ProductSubcategoryKey = b.ProductSubcategoryKey
INNER JOIN DimDate c ON c.DateKey = a.OrderDateKey
WHERE e.ProductCategoryKey =2
	AND c.FullDateAlternateKey BETWEEN '1/1/2014' AND '1/1/2015'
GROUP BY e.ProductCategoryKey,c.Year,d.CustomerKey,d.FirstName,d.LastName
GO

SET STATISTICS IO OFF
SET STATISTICS TIME OFF
GO
```



<a id="preview_considerations_for_in_memory" name="preview_considerations_for_in_memory"></a>


## インメモリ OLTP のプレビューの考慮事項


Azure SQL Database のインメモリ OLTP 機能は、[2015 年 10 月 28 日にプレビュー段階になりました](http://azure.microsoft.com/updates/public-preview-in-memory-oltp-and-real-time-operational-analytics-for-azure-sql-database/)。


一般公開 (GA) 前のプレビュー段階では、インメモリ OLTP は次の条件でのみサポートされます。

- *Premium* サービス レベルのデータベース。

- インメモリ OLTP 機能が有効になった後に作成されたデータベース。
 - インメモリ OLTP 機能がアクティブになる前に作成されたデータベースから復元された新しいデータベースは、インメモリ OLTP をサポートできません。


不明な場合は、次の T-SQL SELECT を実行して、データベースがインメモリ OLTP をサポートしているかどうかを確認することができます。結果が **1** の場合、データベースがインメモリ OLTP をサポートしていることを示します。

```
SELECT DatabasePropertyEx(DB_NAME(), 'IsXTPSupported');
```


クエリによって **1** が返された場合、そのデータベースではインメモリ OLTP がサポートされており、このデータベースに基づいて作成されたデータベース コピーとデータベースの復元でもサポートされます。


#### Premium でのみ使用できるオブジェクト


データベースに次の種類のインメモリ OLTP オブジェクトまたは型のいずれかが含まれている場合、データベースのサービス階層を Premium から Basic または Standard にダウングレードすることはできません。データベースをダウングレードするには、まずこれらのオブジェクトを削除します。

- メモリ最適化テーブル
- メモリ最適化テーブル型
- ネイティブでコンパイルされたモジュール


#### その他のリレーションシップ


- プレビュー段階では、エラスティック プール内のデータベースでインメモリ OLTP 機能を使用することはできませんが、今後はサポートされる可能性があります。

- SQL Data Warehouse でインメモリ OLTP を使用することはサポートされていません。
 - インメモリ分析の列ストア インデックス機能は、SQL Data Warehouse でサポートされています。

- プレビュー段階では、クエリ ストアはネイティブでコンパイルされたモジュール内のクエリをキャプチャすることはできませんが、将来的にはキャプチャできるようになる可能性があります。

- Transact-SQL の機能の一部はインメモリ OLTP でサポートされません。これは Microsoft SQL Server と Azure SQL Database の両方に適用されます。詳細は、以下を参照してください。
 - [Transact-SQL によるインメモリ OLTP のサポート](http://msdn.microsoft.com/library/dn133180.aspx)
 - [インメモリ OLTP でサポートされていない Transact-SQL の構造](http://msdn.microsoft.com/library/dn246937.aspx)


## 以降のステップ


- [既存の Azure SQL アプリケーションでインメモリ OLTP](sql-database-in-memory-oltp-migration.md) を試します。


## その他のリソース

#### 詳細情報

- [インメモリ OLTP の概要 (Microsoft SQL Server と Azure SQL Database の両方に適用されます)](http://msdn.microsoft.com/library/dn133186.aspx)

- [MSDN のリアルタイム運用分析に関する記事](http://msdn.microsoft.com/library/dn817827.aspx)

- [一般的なワークロード パターンと移行の考慮事項に関するホワイト ペーパー](http://msdn.microsoft.com/library/dn673538.aspx)。インメモリ OLTP によってパフォーマンスが大幅に向上する一般的なワークロード パターンが記載されています。

#### アプリケーションの設計

- [インメモリ OLTP (インメモリ最適化)](http://msdn.microsoft.com/library/dn133186.aspx)

- [既存の Azure SQL アプリケーションでインメモリ OLTP を使用する。](sql-database-in-memory-oltp-migration.md)

#### ツール

- [SQL Server Data Tools プレビュー (SSDT)](http://msdn.microsoft.com/library/mt204009.aspx) (最新月バージョン)

- [SQL Server の Replay Markup Language (RML) ユーティリティの説明](http://support.microsoft.com/ja-JP/kb/944837)

- インメモリ OLTP のために[インメモリ ストレージを監視する](sql-database-in-memory-oltp-monitoring.md)

<!---HONumber=AcomDC_1217_2015-->