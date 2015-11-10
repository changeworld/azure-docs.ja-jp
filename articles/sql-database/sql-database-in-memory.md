<properties
	pageTitle="SQL インメモリの使用 |Microsoft Azure"
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
	ms.date="10/28/2015"
	ms.author="jodebrui"/>


# インメモリの使用 (プレビュー)


SQL インメモリは、トランザクション ワークロードと分析ワークロードのパフォーマンスを大幅に向上するテクノロジです。インメモリ OLTP によってトランザクションのスループットが最大で 30 倍向上でき、インメモリ分析によってクエリのパフォーマンスがワークロードによっては最大 100 倍向上できます。リアルタイム分析では、これらのテクノロジを組み合わせて、運用データに基づくビジネスについての洞察をリアルタイムで得ることができます。

インメモリ分析は Azure DB で一般提供されています。インメモリ OLTP とリアルタイム運用分析はプレビューとして Premium の Azure SQL Database で提供されています。


## 使用の開始

トランザクション ワークロード向けのインメモリ OLTP を試す場合:


- [インメモリ OLTP のサンプルをインストールする](#install-the-in-memory-oltp-sample)。
- [既存の Azure SQL アプリケーションでインメモリ OLTP を使用する](sql-database-in-memory-oltp-migration.md)。
- [インメモリ ストレージを監視する](sql-database-in-memory-oltp-monitoring.md)。


分析ワークロード向けのインメモリ分析を試す場合:

- [メモリ分析のサンプルをインストールする](#install-the-in-memory-analytics-sample)。
- 詳細については、MSDN の[列ストア インデックス](https://msdn.microsoft.com/library/gg492088.aspx)に関するトピックを参照してください。


## インメモリ OLTP のサンプルをインストールする

Azure プレビュー ポータルで数回クリックするだけで、AdventureWorksLT [V12] のサンプル データベースを作成できます。以下の手順では、インメモリ OLTP オブジェクトの具体例として、テーブルとネイティブ コンパイル ストアド プロシージャを使用して AdventureWorksLT データベースを強化する方法について説明します。


1. [Azure プレビュー ポータル](https://portal.azure.com/)で、V12 サーバー上に Premium データベースを作成します。ソースを AdventureWorksLT [V12] サンプル データベースに設定します。
 - この手順の詳細については、[この記事](sql-database-get-started.md)を参照してください。

2. [SQL Server Management Studio (SSMS.exe)](https://msdn.microsoft.com/library/mt238290.aspx) または同様のユーティリティを使用して、データベースに接続します。

3. [インメモリ OLTP スクリプト](https://raw.githubusercontent.com/Azure/azure-sql-database-samples/master/T-SQL/In-Memory/sql_in-memory_oltp_sample.sql)をクリップボードにコピーします。
 - このスクリプトによって、手順 1. で作成した AdventureWorksLT サンプル データベース内に、必要なインメモリ オブジェクトが作成されます。

4. Transact-SQL スクリプトを SSMS.exe に貼り付け、スクリプトを実行します。



&nbsp;

このサンプルには、次のメモリ最適化テーブルが含まれています。

- SalesLT.Product\_inmem
- SalesLT.SalesOrderHeader\_inmem
- SalesLT.SalesOrderDetail\_inmem
- Demo.DemoSalesOrderHeaderSeed
- Demo.DemoSalesOrderDetailSeed

オブジェクト エクスプローラーまたはカタログ ビューのクエリを使用して、メモリ最適化テーブルを検査します。

例:


```
		SELECT name, object_id, type, type_desc, is_memory_optimized, durability, durability_desc
		FROM sys.tables
	WHERE is_memory_optimized=1;
```


同様に、オブジェクト エクスプローラーまたはカタログ ビューのクエリを使用して、ネイティブ コンパイル ストアド プロシージャ SalesLT.usp\_InsertSalesOrder\_inmem を、検査できます。

例:


```
		SELECT object_name(object_id), object_id, definition, uses_native_compilation
		FROM sys.sql_modules
	WHERE uses_native_compilation=1;
```


## サンプル ワークロードを実行する

SalesLT.usp\_InsertSalesOrder\_inmem および SalesLT.usp\_InsertSalesOrder\_ondisk ストアド プロシージャを使用して、ディスク ベース テーブルとメモリ最適化テーブルの挿入パフォーマンスを比較できます。

サンプル データベースと同じ Azure リージョンに配置されているアプリケーションから多数の同時クライアント接続を使用してワークロードを実行することをお勧めします。

### サンプル販売注文の挿入

次のスクリプトでは、5 つの行項目を含むサンプルの販売注文を SalesLT.SalesOrderHeader\_inmem および SalesLT.SalesOrderDetail\_inmem メモリ最適化テーブルに挿入します。


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

EXECUTE SalesLT.usp_InsertSalesOrder_inmem @SalesOrderID OUTPUT, @DueDate,
			@CustomerID, @BillToAddressID, @ShipToAddressID, @od;
```


### サンプルの負荷ワークロードを実行する

サンプルの負荷ワークロードを実行するには、サンプル データベースと同じリージョンに [Azure 仮想マシン](https://azure.microsoft.com/documentation/services/virtual-machines/) を作成します。ostress コマンドライン ツールを使用して、ワークロードを実行できます。[ostress をインストールして実行する](https://msdn.microsoft.com/library/dn511655&#x28;v=sql.120&#x29;.aspx)手順については、MSDN の記事を参照してください。

RML コマンド プロンプトから次のコマンドを実行すると、100 個の同時接続を使用して 100 万個の販売注文がそれぞれ 5 つの行項目でメモリ最適化テーブルに挿入されます。


```
		ostress.exe –n100 –r500 –S<servername>.database.windows.net -U<login> -P<password>
		 -d<database> -q -Q"DECLARE @i int = 0, @od SalesLT.SalesOrderDetailType_inmem,
		@SalesOrderID int, @DueDate datetime2 = sysdatetime(), @CustomerID int = rand() *
		8000, @BillToAddressID int = rand() * 10000, @ShipToAddressID int = rand()*  
		10000; INSERT INTO @od SELECT OrderQty, ProductID FROM
		Demo.DemoSalesOrderDetailSeed WHERE OrderID= cast((rand()*60) as int); while (@i <
		 20) begin; EXEC SalesLT.usp_InsertSalesOrder_inmem @SalesOrderID OUTPUT, @DueDate,
		 @CustomerID, @BillToAddressID, @ShipToAddressID, @od; set @i += 1 end"
```


必ず、<servername> をサーバーの名前に、<database> をデータベースの名前に、<login> および <password> を実際のログイン情報に置き換えてください。

メモリ最適化テーブルを従来のディスク ベース テーブルの挿入パフォーマンスと比較するために、次のコマンドを使用して、ディスク ベース テーブルに同じ 100 万個の販売注文を挿入します。


```
		ostress.exe –n100 –r500 –S<servername>.database.windows.net -U<login> -P<password>
		-d<database> -q -Q"DECLARE @i int = 0, @od SalesLT.SalesOrderDetailType_ondisk,
		@SalesOrderID int, @DueDate datetime2 = sysdatetime(), @CustomerID int = rand() *
		8000, @BillToAddressID int = rand() * 10000, @ShipToAddressID int = rand() *
		10000; INSERT INTO @od SELECT OrderQty, ProductID FROM
		Demo.DemoSalesOrderDetailSeed with (snapshot) WHERE OrderID= cast((rand()*60) as
		int); while (@i < 20) begin; EXEC SalesLT.usp_InsertSalesOrder_ondisk
		@SalesOrderID OUTPUT, @DueDate, @CustomerID, @BillToAddressID, @ShipToAddressID,
		@od; set @i += 1 end"
```


テストの結果、ostress をデータベースと同じ Azure リージョンにある VM で実行した場合、このワークロードではメモリ最適化テーブルのパフォーマンスがディスク ベース テーブルに比べて約 9 倍向上することがわかりました。

インメモリ ストレージ領域が不足しないように、テストを実行するたびに、必ずサンプルをリセットしてください。リセットは、データベースで次の T-SQL ステートメントを実行します。1 回のテストで 100 万個の販売注文が挿入されるため、結果としてメモリ最適化テーブルに 500 MB 以上のデータが挿入されます。


```
EXECUTE Demo.usp_DemoReset;
```


## メモリ分析のサンプルをインストールする

**最初に**、新しい Azure SQL Database を作成します。

- Premium Edition を選択します (列ストアを使用するために Premium が必要です)。
- 必ずサンプルからデータベースを作成します。

- わかりやすくするために、データベースの名前を AdventureworksLT に設定します。



**次に**、[SQL Server Management Studio (SSMS)](https://msdn.microsoft.com/library/mt238290.aspx) 2015 年 9 月版プレビュー以降を使用して、Azure SQL Database に接続します。


- セットアップ スクリプト [sql\_in-memory\_analytics\_sample](https://raw.githubusercontent.com/Azure/azure-sql-database-samples/master/T-SQL/In-Memory/sql_in-memory_analytics_sample.sql) を実行します。


- スクリプトを実行すると、ディメンション テーブルとファクト テーブルに必要なスキーマが作成されます。スクリプトによって作成される以下の 2 つのファクト テーブルには、それぞれ最大 350 万行が含まれています。


- FactResellerSales\_CCI には列ストア テーブルが 1 つ含まれています。


- FactResellerSalesXL\_PageCompressed は同等の B ツリー テーブルが含まれており、ページ圧縮されています。**注:** このスクリプトは、実行してデータを生成するのに最大 15 分かかることがあります。


デモ クエリ [clustered\_columnstore\_sample\_queries.sql](https://raw.githubusercontent.com/Azure/azure-sql-database-samples/master/T-SQL/In-Memory/clustered_columnstore_sample_queries.sql) を最後まで**実行**して機能を確認します。

## インメモリ OLTP の詳細情報

[インメモリ OLTP (インメモリ最適化)](https://msdn.microsoft.com/library/dn133186.aspx)

[一般的なワークロード パターンと移行の考慮事項に関するホワイト ペーパー](https://msdn.microsoft.com/library/dn673538.aspx)。インメモリ OLTP によってパフォーマンスが大幅に向上する一般的なワークロード パターンが記載されています。

## プレビューの考慮事項

インメモリ OLTP は、Premium Edition データベースで**のみ**サポートされています。

インメモリ OLTP は、新規作成されたデータベースでのみサポートされます。コピーまたは復元の機能を使用して既存のデータベースを基に作成されたデータベースではサポートされません。ただし、新しいデータベースを作成した後は、インメモリ OLTP のすべての機能を維持しながらこのデータベースをコピーまたは復元できます。

特定のデータベースでインメモリ OLTP がサポートされているかどうかを確認するには、次のクエリを実行します。


```
SELECT DATABASEPROPERTYEX(DB_NAME(), 'IsXTPSupported');
```


クエリに対して **1** が返された場合、そのデータベースではインメモリ OLTP がサポートされており、このデータベースに基づいて作成されたデータベース コピーとデータベースの復元でもサポートされます。

データベースに次の種類のオブジェクトまたは型が含まれている場合、データベースのサービス階層を Basic または Standard に変更することはサポートされていません。データベースをダウングレードするには、最初にオブジェクトを削除する必要があります。

- メモリ最適化テーブル
- メモリ最適化テーブル型
- データベースがエラスティック プール内にある状態でネイティブ コンパイル モジュールでインメモリ OLTP を使用することはサポートされていません。

SQL Data Warehouse でインメモリ OLTP を使用することはサポートされていません。

クエリ ストアでは、ネイティブ コンパイル モジュール内のクエリをキャプチャしません。

Transact-SQL の機能の一部はインメモリ OLTP でサポートされません。詳細については、「[Transact-SQL によるインメモリ OLTP のサポート](https://msdn.microsoft.com/library/dn133180.aspx)」を参照してください

## サポートされるツール

[SQL Server Management Studio (SSMS)](https://msdn.microsoft.com/library/mt238290.aspx) 2015 年 9 月版プレビュー以降

[SQL Server Data Tools (SSDT) プレビュー版](https://msdn.microsoft.com/library/mt204009.aspx) 2015 年 9 月版プレビュー以降

## その他のリソース

[MSDN のインメモリ OLTP に関する記事](https://msdn.microsoft.com/library/dn133186.aspx)

[MSDN のインメモリ分析 (列ストア) に関する記事](https://msdn.microsoft.com/library/gg492088.aspx)

[MSDN のリアルタイム運用分析に関する記事](https://msdn.microsoft.com/library/dn817827.aspx)

[一般的なワークロード パターンと移行の考慮事項に関するホワイト ペーパー](https://msdn.microsoft.com/library/dn673538.aspx)。インメモリ OLTP によってパフォーマンスが大幅に向上する一般的なワークロード パターンが記載されています。

## 次のステップ

[既存の Azure SQL アプリケーションでインメモリ OLTP を試す](sql-database-in-memory-oltp-migration.md)。

インメモリ OLTP のために[インメモリ ストレージを監視する](sql-database-in-memory-oltp-monitoring.md)。

<!---HONumber=Nov15_HO2-->