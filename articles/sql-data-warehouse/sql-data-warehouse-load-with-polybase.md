<properties
   pageTitle="SQL Data Warehouse の PolyBase チュートリアル | Microsoft Azure"
   description="データ ウェアハウジングのシナリオに沿って、PolyBase の概要と、その使用方法を学習します。"
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="barbkess"
   manager="jhubbard"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="05/09/2015"
   ms.author="sahajs;barbkess"/>


# PolyBase を使用したデータのロード
PolyBase テクノロジを使用して、複数のソースからデータをクエリして結合できます。これらはすべて Transact-SQL コマンドを使用して実行できます。

次の手順に従って、PolyBase を使用し、Azure BLOB ストレージに格納されたデータに対してクエリを実行して、SQL Data Warehouse のデータベースにロードできます。

- データベース マスター キーと資格情報を作成します。
- PolyBase オブジェクト、つまり、外部データ ソース、外部ファイル形式、および外部テーブルを作成します。 
- Azure BLOB ストレージに格納されたデータをクエリします。
- Azure BLOB ストレージから SQL Data Warehouse にデータをロードします。


## 前提条件
このチュートリアルを進めるには、次が必要です。

- Azure ストレージ アカウント
- 区切りテキスト ファイルとして Azure BLOB ストレージに格納されたデータ

まず、Azure BLOB ストレージ内のデータに接続し、クエリを実行するために PolyBase で必要なオブジェクトを作成します。

## データベース マスター キーの作成
サーバー上のユーザー データベースに接続し、データベース マスター キーを作成します。このキーは、次の手順で資格情報シークレットの暗号化に使用されます。

```
-- Creating master key
CREATE MASTER KEY;
```

リファレンス トピック: [CREATE MASTER KEY (Transact-SQL)][]。

## データベース スコープの資格情報の作成
Azure BLOB ストレージにアクセスするには、Azure ストレージ アカウントの認証情報を格納するデータベース スコープの資格情報を作成する必要があります。Data Warehouse データベースに接続し、アクセスする各 Azure ストレージ アカウントのデータベース スコープ資格情報を作成します。ユーザー名と Azure ストレージ アカウント キーをシークレットとして指定します。ユーザー名は、Azure Storage への認証には影響を及ぼしません。

データベース スコープの資格情報が既に存在するかどうかを確認するには、サーバーの資格情報のみを表示する sys.credentials ではなく sys.database\_credentials を使用します。

```
-- Check for existing database-scoped credentials.
SELECT * FROM sys.database_credentials;

-- Create a database scoped credential
CREATE DATABASE SCOPED CREDENTIAL ASBSecret WITH IDENTITY = 'joe', 
	Secret = 'myazurestoragekey==';
```

リファレンス トピック: [CREATE CREDENTIAL (Transact-SQL)][]。

Azure ストレージ アカウント キーを起動するには、資格情報を削除し、シークレットとして新しいキーを使用して資格情報を再作成する必要があります。

```
-- Dropping credential
DROP DATABASE SCOPED CREDENTIAL ASBSecret;
```

リファレンス トピック: [DROP CREDENTIAL (Transact-SQL)][]。


## 外部データ ソースの作成
外部データ ソースは、Azure BLOB ストレージのデータとアクセス情報の場所を格納するデータベース オブジェクトです。アクセスする Azure Storage コンテナーごとに外部データ ソースを定義する必要があります。

```
-- Creating external data source (Azure Blob Storage) 
CREATE EXTERNAL DATA SOURCE azure_storage 
WITH (
	TYPE = HADOOP, 
       LOCATION ='wasbs://mycontainer@ test.blob.core.windows.net/path’,
      CREDENTIAL = ASBSecret
);
```

リファレンス トピック: [CREATE EXTERNAL DATA SOURCE (Transact-SQL)][]。

## 外部ファイル形式の作成
外部ファイル形式は、外部データの形式を指定するデータベース オブジェクトです。この例では、テキスト ファイル内のデータは圧縮せず、フィールドは、パイプ文字 ('|') で区切っています。

```
-- Creating external file format (delimited text file)
CREATE EXTERNAL FILE FORMAT text_file_format 
WITH (
	FORMAT_TYPE = DELIMITEDTEXT, 
	FORMAT_OPTIONS (
		FIELD_TERMINATOR ='|', 
		USE_TYPE_DEFAULT = TRUE
	)
);
```

PolyBase は、区切り文字、Hive RCFILE、および HIVE ORC 形式の圧縮データおよび非圧縮データを処理できます。

リファレンス トピック: [CREATE EXTERNAL FILE FORMAT (Transact-SQL)][]。

## 外部テーブルの作成

外部テーブルの定義は、リレーショナル テーブルの定義と似ています。主な相違点は、データの場所と形式です。外部テーブルの定義は、SQL Data Warehouse データベースに格納されます。データは、データ ソースで指定された場所に格納されます。

LOCATION オプションで、データ ソースのルートからデータまでのパスが指定されます。この例では、データは 'wasbs://mycontainer@ test.blob.core.windows.net/path/Demo/' にあります。

```
-- Creating external table pointing to file stored in Azure Storage
CREATE EXTERNAL TABLE [ext].[CarSensor_Data] (
    [SensorKey] int NOT NULL, 
    [CustomerKey] int NOT NULL, 
    [GeographyKey] int NULL, 
    [Speed] float NOT NULL, 
    [YearMeasured] int NOT NULL
)
WITH (LOCATION='/Demo/',
      DATA_SOURCE = azure_storage,
      FILE_FORMAT = text_file_format,      
);
```

> [AZURE.NOTE]この時点では、外部テーブルで統計を作成することはできません。

同じテーブルのすべてのファイルは、Azure BLOB の同じ論理フォルダーの下にある必要があります。SQL Data Warehouse を使用して並列処理を行う場合、可能であれば Azure Storage データを 1GB 以下のファイルに分割することをお勧めします。

リファレンス トピック: [CREATE EXTERNAL TABLE (Transact-SQL)][]。

作成したオブジェクトは、SQL Data Warehouse のデータベースに格納されます。SQL Server Data Tools (SSDT) のオブジェクト エクスプローラーでそれらを表示できます。


## Azure BLOB ストレージ データのクエリ
外部テーブルに対するクエリでは、リレーショナル テーブルであるかのように、単にテーブル名が使用されます。

これは、SQL Data Warehouse に格納されている保険顧客データを、Azure Storage BLOB に格納されている自動車センサー データと結びつけるアドホック クエリです。結果として、他のドライバーよりも運転速度が速いドライバーが表示されます。

```
-- Join SQL Data Warehouse relational data with Azure storage data. 
SELECT 
    [Insured_Customers].[FirstName],
    [Insured_Customers].[LastName],
    [Insured_Customers].[YearlyIncome],
    [CarSensor_Data].[Speed]
FROM [dbo].[Insured_Customers] INNER JOIN [ext].[CarSensor_Data]
ON [Insured_Customers].[CustomerKey] = [CarSensor_Data].[CustomerKey]
WHERE [CarSensor_Data].[Speed] > 60 
ORDER BY [CarSensor_Data].[Speed] desc;
```

## Azure BLOB ストレージからのデータのロード
この例では、Azure BLOB ストレージから SQL Data Warehouse データベースにデータをロードします。

データを直接格納すると、クエリのデータ転送に要する時間が削減されます。columnstore インデックスを使用してデータを格納すると、分析クエリに対するクエリのパフォーマンスが最大で 10 倍改善されます。

この例では、CREATE TABLE AS SELECT ステートメントを使用してデータをロードします。新しいテーブルは、クエリで指定された列を継承します。これは、外部テーブル定義からそれらの列のデータ型を継承します。

CREATE TABLE AS SELECT は、INSERT...SELECT に取って代わる高パフォーマンス Transact-SQL ステートメントです。元来、これは Analytics Platform System で超並列処理 (MPP) エンジン用に開発されたものですが、現在は SQL Data Warehouse で使用されています。

```
-- Load data from Azure blob storage to SQL Data Warehouse 

CREATE TABLE [dbo].[Customer_Speed]
WITH (
	CLUSTERED COLUMNSTORE INDEX
	DISTRIBUTION = HASH([CarSensor_Data].[CustomerKey])
	)
AS SELECT * from [ext].[CarSensor_Data];
```

「[CREATE TABLE AS SELECT (Transact-SQL)][]」を参照してください。


## 制限事項
PolyBase でのロードでは、UTF-8 エンコード スタイルのみがサポートされています。たとえば UTF-16 などの他のエンコード スタイルについては、bcp ユーティリティ、SSIS、または Azure Data Factory を使用して SQL Data Warehouse にデータをロードすることを検討してください。

## 次のステップ
開発のその他のヒントについては、[開発の概要][]に関するページをご覧ください。

<!--Image references-->

<!--Article references-->
[Load data with bcp]: sql-data-warehouse-load-with-bcp.md
[Load with PolyBase]: sql-data-warehouse-load-with-polybase.md
[solution partners]: sql-data-warehouse-solution-partners.md
[開発の概要]: sql-data-warehouse-overview-develop.md

<!--MSDN references-->
[supported source/sink]: https://msdn.microsoft.com/library/dn894007.aspx
[copy activity]: https://msdn.microsoft.com/library/dn835035.aspx
[SQL Server destination adapter]: https://msdn.microsoft.com/library/ms141095.aspx
[SSIS]: https://msdn.microsoft.com/library/ms141026.aspx


<!-- External Links -->
[CREATE EXTERNAL DATA SOURCE (Transact-SQL)]: https://msdn.microsoft.com/library/dn935022(v=sql.130).aspx
[CREATE EXTERNAL FILE FORMAT (Transact-SQL)]: https://msdn.microsoft.com/library/dn935026(v=sql.130).aspx
[CREATE EXTERNAL TABLE (Transact-SQL)]: https://msdn.microsoft.com/library/dn935021(v=sql.130).aspx
[CREATE TABLE AS SELECT (Transact-SQL)]: https://msdn.microsoft.com/library/mt204041.aspx
[CREATE MASTER KEY (Transact-SQL)]: https://msdn.microsoft.com/ja-jp/library/ms174382.aspx
[CREATE CREDENTIAL (Transact-SQL)]: https://msdn.microsoft.com/ja-jp/library/ms189522.aspx
[DROP CREDENTIAL (Transact-SQL)]: https://msdn.microsoft.com/ja-jp/library/ms189450.aspx

<!---HONumber=July15_HO5-->