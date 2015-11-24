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
	ms.date="11/16/2015"
	ms.author="jodebrui"/>


# SQL Database でのインメモリ (プレビュー) の使用

通常の SQL テーブルはハード ドライブにのみ格納されます。インメモリ機能を使用してテーブルを強化し、サーバーの実行中にアクティブなメモリ内にテーブルを格納できるようにします。


インメモリは、トランザクション ワークロードと分析ワークロードのパフォーマンスを大幅に向上するテクノロジです。

- インメモリ OLTP (オンライン トランザクション プロセス) を使用すると、ワークロードの指定に応じて、トランザクションのスループットで最大 30 回を達成できます。
 - メモリが最適化されたテーブル。
 - ネイティブでコンパイルされたストアド プロシージャ。

- インメモリ分析では、クエリ パフォーマンスで最大 100 回の改善を達成できます。
 - 列ストア インデックス。


[Real-Time Analytics](http://msdn.microsoft.com/library/dn817827.aspx) の場合、次のテクノロジを組み合わせて取得します。

- 運用データに基づくリアルタイムのビジネスの把握。
- 高速な OLTP。


#### 可用性


- *GA:* インメモリ分析は、一般公開 (GA) されています。
- *プレビュー:* インメモリ OLTP とリアルタイム運用分析はどちらもプレビュー段階です。
 - これら 2 つの機能は、[Premium](sql-database-service-tiers.md) Azure SQL Database でのみ使用できます。


#### OLTP と分析

このトピックには、主に 2 つのセクションがあります。

- A.インメモリ [OLTP](#install_oltp_manuallink) (読み取りと書き込みがあります)
- B.インメモリ[分析](#install_analytics_manuallink) (読み取りがあります)



<a id="install_oltp_manuallink" name="install_oltp_manuallink"></a>

&nbsp;

## A.インメモリ OLTP のサンプルをインストールする

[Azure プレビュー ポータル](http://portal.azure.com/)で数回クリックするだけで、AdventureWorksLT [V12] のサンプル データベースを作成できます。このセクションの手順では、以下を使用して AdventureWorksLT データベースを強化する方法について説明します。

- インメモリ テーブル。
- ネイティブでコンパイルされたストアド プロシージャ。


#### インストール手順

1. [Azure プレビュー ポータル](http://portal.azure.com/)で、V12 サーバー上に Premium データベースを作成します。**ソース**を AdventureWorksLT [V12] サンプル データベースに設定します。
 - 詳細な手順については、「[最初の Azure SQL Database を作成する](sql-database-get-started.md)」を参照してください。

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


結果が **0** の場合、インメモリがサポートされていないことを示します。1 の場合はサポートされています。


#### 作成されるメモリ最適化項目の概要

**テーブル**: このサンプルには、次のメモリ最適化テーブルが含まれています。

- SalesLT.Product\_inmem
- SalesLT.SalesOrderHeader\_inmem
- SalesLT.SalesOrderDetail\_inmem
- Demo.DemoSalesOrderHeaderSeed
- Demo.DemoSalesOrderDetailSeed


SSMS で**オブジェクト エクスプローラー**を使用してメモリ最適化テーブルを確認できます。

- **[テーブル]** を右クリックし、**[フィルター]** > **[フィルターの設定]** > **[メモリ最適化]** が 1 であることを確認します。


または、次のようにカタログ ビューをクエリすることができます。


```
SELECT is_memory_optimized, name, type_desc, durability_desc
	FROM sys.tables
	WHERE is_memory_optimized = 1;
```


**ネイティブでコンパイルされたストアド プロシージャ**: SalesLT.usp\_InsertSalesOrder\_inmem は、カタログ ビュー クエリで確認できます。


```
SELECT uses_native_compilation, OBJECT_NAME(object_id), definition
	FROM sys.sql_modules
	WHERE uses_native_compilation = 1;
```


&nbsp;

## サンプルの OLTP ワークロードを実行する

次の 2 つの*ストアド プロシージャ*の違いは、1 つ目の手順はメモリ最適化バージョンのテーブルを使用し、2 つ目の手順は通常のディスク上のテーブルを使用している点です。

- SalesLT**.**usp\_InsertSalesOrder**\_inmem**
- SalesLT**.**usp\_InsertSalesOrder**\_ondisk**


このセクションでは、便利な **ostress.exe** ユーティリティを使用して、ストレスが高いレベルで 2 つのストアド プロシージャを実行する方法について説明します。2 つのストレス実行が完了するまでの時間を比較することができます。


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
	
EXECUTE SalesLT.usp_InsertSalesOrder_inmem @SalesOrderID OUTPUT,
	@DueDate, @CustomerID, @BillToAddressID, @ShipToAddressID, @od;
```


前述した ostress.exe の T-SQL の \_ondisk バージョンを作成するには、両方の *\_inmem* サブストリングを *\_ondisk* に置き換えます。この置換は、テーブルとストアド プロシージャの名前に影響があります。


### RML ユーティリティと ostress をインストールする


Azure VM で ostress.exe を実行する計画を立てるのが理想的です。AdventureWorksLT データベースがある Azure リージョンと同じリージョンに [Azure Virtual Machines](http://azure.microsoft.com/documentation/services/virtual-machines/) を作成します。代わりにノートパソコンで ostress.exe を実行することもできます。


VM または選択した任意のホストに、ostress.exe を含む Replay Markup Language (RML) ユーティリティをインストールします。

- ostress.exe については、[インメモリ OLTP を実行する AdventureWorks の拡張](http://msdn.microsoft.com/library/dn511655&#x28;v=sql.120&#x29;.aspx)に関するページを参照してください。
 - または、[インメモリ OLTP のサンプル データベース](http://msdn.microsoft.com/library/mt465764.aspx)に関するページを参照してください。
 - または、[ostress.exe のインストールに関するブログ](http://blogs.msdn.com/b/psssql/archive/2013/10/29/cumulative-update-2-to-the-rml-utilities-for-microsoft-sql-server-released.aspx)を参照してください。



<!--
dn511655.aspx is for SQL 2014, whereas mt465764.aspx is for SQL 2016.
-->



### 最初に \_inmem stress ワークロードを実行する


*RML コマンド プロンプト* ウィンドウを使用して、ostress.exe コマンド ラインを実行できます。

RML コマンド プロンプトから次の ostress.exe コマンドを実行します。

- それぞれ 5 行のアイテムがある 100 万件の販売注文をメモリ最適化テーブルに挿入します。
- 100 個の同時接続 (-n100) を使用します。


```
ostress.exe -n100 -r500 -S<servername>.database.windows.net
	 -U<login> -P<password> -d<database>
	 -q -Q"DECLARE @i int = 0, @od SalesLT.SalesOrderDetailType_inmem,
	 @SalesOrderID int, @DueDate datetime2 = sysdatetime(), @CustomerID int = rand() *
	 8000, @BillToAddressID int = rand() * 10000, @ShipToAddressID int = rand()*
	 10000;
	 INSERT INTO @od SELECT OrderQty, ProductID FROM
	 Demo.DemoSalesOrderDetailSeed WHERE OrderID= cast((rand()*60) as int);
	 WHILE (@i < 20) begin;
	 EXECUTE SalesLT.usp_InsertSalesOrder_inmem
	 @SalesOrderID OUTPUT, @DueDate, @CustomerID, @BillToAddressID,
	 @ShipToAddressID, @od; set @i += 1; end"
```


前述の ostress.exe コマンド ラインを実行するには:


1. SSMS で次のコマンドを実行してデータベースをリセットし、前回の実行で挿入されたすべてのデータを削除します。```
EXECUTE Demo.usp_DemoReset;
```

2. テキストをクリップボードにコピーします。

3. すべての行末文字 (\\r\\n) とすべてのタブをスペースに置き換えます。

4. パラメーター -S -U -P -d の <placeholders> を実際の正しい値に置き換えます。


#### 結果は期間


ostress.exe が完了すると、RML コマンド ウィンドウに表示される出力の最終行に実行時間が出力されます。たとえば、短いテストの場合、約 1.5 分かかります。

`11/12/15 00:35:00.873 [0x000030A8] OSTRESS exiting normally, elapsed time: 00:01:31.867`


#### リセット、\_ondisk の編集、再実行


\_inmem 実行の結果を取得したら、\_indisk 実行に次の手順を実行します。


1. SSMS で次のコマンドを実行してデータベースをリセットし、前回の実行で挿入されたすべてのデータを削除します。```
EXECUTE Demo.usp_DemoReset;
```

2. ostress.exe コマンド ラインを編集して、すべての *\_inmem* を *\_ondisk* に置き換えます。

3. ostress.exe を再実行し、期間の結果を取得します。

4. 確実なクリーンアップのために、データベースをもう一度リセットします。
 - 100 万件の販売注文を挿入する 1 回のテスト実行で、テーブルには 500 MB 以上のデータが挿入されます。


#### 予想される比較結果

テストの結果、ostress をデータベースと同じ Azure リージョンにある VM で実行した場合、このワークロードではメモリ最適化テーブルのパフォーマンスがディスク ベース テーブルに比べて約 **9 倍**向上することがわかりました。

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


#### 重要なテーブルと列ストア インデックス


- dbo.FactResellerSalesXL\_CCI は、クラスター化された**列ストア** インデックスがあるテーブルです。*データ* レベルの高い圧縮率です。

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


## インメモリのプレビューの考慮事項


Azure SQL Database のインメモリ機能は、[2015 年 10 月 28 日にプレビュー段階になりました](http://azure.microsoft.com/updates/public-preview-in-memory-oltp-and-real-time-operational-analytics-for-azure-sql-database/)。


一般公開 (GA) 前のプレビュー段階では、インメモリ OLTP は次の条件でのみサポートされます。

- *Premium* サービス階層のデータベース。

- インメモリ機能が有効になった後に作成されたデータベース。
 - インメモリ機能が有効になった日の前に作成されたバックアップから復元された新しいデータベースは、インメモリをサポートできません。
 - たとえば、インメモリをサポートするデータベースをバックアップするとします。そのバックアップを古い Premium データベースに復元します。すると、古いデータベースはインメモリをサポートするようになります。


不明な場合は、次の T-SQL SELECT を実行して、データベースがインメモリ OLTP をサポートしているかどうかを確認することができます。結果が **1** の場合、データがインメモリをサポートしていることを示します。

```
SELECT DatabasePropertyEx(DB_NAME(), 'IsXTPSupported');
```


クエリに対して **1** が返された場合、そのデータベースではインメモリ OLTP がサポートされており、このデータベースに基づいて作成されたデータベース コピーとデータベースの復元でもサポートされます。


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

<!---HONumber=Nov15_HO4-->