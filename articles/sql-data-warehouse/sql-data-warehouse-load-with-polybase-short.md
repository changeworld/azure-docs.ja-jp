<properties
   pageTitle="PolyBase でのデータの読み込みチュートリアル | Microsoft Azure"
   description="PolyBase を使用して SQL Data Warehouse にデータを読み込む方法を説明します。"
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="sahajs"
   manager="jhubbard"
   editor="sahajs"/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="11/04/2015"
   ms.author="sahajs;barbkess"/>


# PolyBase を使用したデータのロード

> [AZURE.SELECTOR]
- [Data Factory](sql-data-warehouse-get-started-load-with-azure-data-factory.md)
- [PolyBase](sql-data-warehouse-load-with-polybase-short.md)
- [BCP](sql-data-warehouse-load-with-bcp.md)

このチュートリアルでは、PolyBase を使用して SQL Data Warehouse にデータを読み込む方法を説明します。PolyBase の詳細については、「[SQL Data Warehouse の PolyBase チュートリアル][]」を参照してください。


## 前提条件
このチュートリアルを進めるには、次が必要です。

- SQL Data Warehouse データベース
- Azure ストレージ アカウント


## 手順 1: ソース データ ファイルを作成する
メモ帳を開き、データの次の行を新しいファイルにコピーします。これをローカル一時ディレクトリ C:\\Temp\\DimDate2.txt に保存します。

```
20150301,1,3
20150501,2,4
20151001,4,2
20150201,1,3
20151201,4,2
20150801,3,1
20150601,2,4
20151101,4,2
20150401,2,4
20150701,3,1
20150901,3,1
20150101,1,3
```


## 手順 2: Azure BLOB ストレージにデータを移行する

- [最新バージョンの AzCopy][] をダウンロードします。
- コマンド ウィンドウを開き、AzCopy.exe が格納されている、コンピューターの AzCopy インストール ディレクトリに移動します。64 ビット Windows コンピューターの既定のインストール ディレクトリは、%ProgramFiles(x86)%\\Microsoft SDKs\\Azure\\AzCopy\\ です。
- 次のコマンドを実行してファイルをアップロードします。/DestKey には Azure ストレージ アカウント キーを指定します。

```
.\AzCopy.exe /Source:C:\Temp\ /Dest:https://pbdemostorage.blob.core.windows.net/datacontainer/datedimension/ /DestKey:<azure_storage_account_key> /Pattern:DimDate2.txt
```

AzCopy の詳細については、「[AzCopy コマンド ライン ユーティリティの概要][]」を参照してください。


## 手順 3: 外部テーブルを作成する

次に、SQL Data Warehouse に外部テーブルを作成して Azure BLOB ストレージを参照する必要があります。外部テーブルは次の手順で作成します。

- [CREATE MASTER KEY ][]\: データベース スコープの資格情報のシークレットを暗号化します。
- [CREATE DATABASE SCOPED CREDENTIAL ]\: Azure ストレージ アカウントの認証情報を指定します。
- [CREATE EXTERNAL DATA SOURCE ]\: Azure BLOB ストレージの場所を指定します。
- [CREATE EXTERNAL FILE FORMAT ]\: データのレイアウトを指定します。
- [CREATE EXTERNAL TABLE]\: Azure Storage のデータを参照します。


```

-- A: Create Master Key
-- Required to encrypt the credential secret in the next step.
CREATE MASTER KEY;


-- B: Create Database Scoped Credential
-- Provide the Azure storage account key. The identity name does not affect authentication to Azure storage.
CREATE DATABASE SCOPED CREDENTIAL AzureStorageCredential 
WITH IDENTITY = 'user', 
SECRET = '<azure_storage_account_key>';


-- C: Create External Data Source
-- Specify location and credential to access your Azure blob storage.
CREATE EXTERNAL DATA SOURCE AzureStorage 
WITH (	
		TYPE = Hadoop, 
		LOCATION = 'wasbs://datacontainer@pbdemostorage.blob.core.windows.net',
		CREDENTIAL = AzureStorageCredential
); 


-- D: Create External File format 
-- Specify the layout of data stored in Azure storage blobs. 
CREATE EXTERNAL FILE FORMAT TextFile 
WITH (FORMAT_TYPE = DelimitedText, 
FORMAT_OPTIONS (FIELD_TERMINATOR = ','));


-- E: Create External Table
-- To reference data stored in Azure blob storage.
CREATE EXTERNAL TABLE dbo.DimDate2External (
	DateId INT NOT NULL, 
	CalendarQuarter TINYINT NOT NULL, 
	FiscalQuarter TINYINT NOT NULL
)
WITH (
		LOCATION='datedimension/', 
		DATA_SOURCE=AzureStorage, 
		FILE_FORMAT=TextFile
);


-- Run a query on external table to confirm that the Azure Storage data can be referenced.
SELECT count(*) FROM dbo.DimDate2External;

```



## 手順 4: SQL Data Warehouse にデータを読み込む

- 新しいテーブルにデータを読み込むには、[CREATE TABLE AS SELECT (Transact-SQL)][] ステートメントを実行します。新しいテーブルは、クエリで指定された列を継承します。これは、外部テーブル定義からそれらの列のデータ型を継承します。 
- 既存のテーブルにデータに読み込むには、INSERT...SELECT ステートメントを使用します。  


```

-- Load the data from Azure blob storage to SQL Data Warehouse

CREATE TABLE dbo.DimDate2
WITH 
(   
    CLUSTERED COLUMNSTORE INDEX,
    DISTRIBUTION = ROUND_ROBIN
)
AS 
SELECT * 
FROM   [dbo].[DimDate2External];

```


## 手順 5: 新しく読み込んだデータの統計を作成する 

Azure SQL Data Warehouse は、統計の自動作成または自動更新をまだサポートしていません。クエリから最高のパフォーマンスを取得するには、最初の読み込み後またはそれ以降のデータの変更後に、すべてのテーブルのすべての列で統計を作成することが重要です。統計の詳細については、開発トピック グループの「[統計][]」トピックを参照してください。この例でロードしたテーブルの統計を作成する方法の簡単な例を次に示します


```
create statistics [DateId] on [DimDate2] ([DateId]);
create statistics [CalendarQuarter] on [DimDate2] ([CalendarQuarter]);
create statistics [FiscalQuarter] on [DimDate2] ([FiscalQuarter]);
```

<!--Article references-->
[SQL Data Warehouse の PolyBase チュートリアル]: sql-data-warehouse-load-with-polybase.md


<!-- External Links -->
[最新バージョンの AzCopy]: http://aka.ms/downloadazcopy
[AzCopy コマンド ライン ユーティリティの概要]: https://azure.microsoft.com/documentation/articles/storage-use-azcopy/

[CREATE EXTERNAL DATA SOURCE ]: https://msdn.microsoft.com/library/dn935022(v=sql.130).aspx
[CREATE EXTERNAL FILE FORMAT ]: https://msdn.microsoft.com/library/dn935026(v=sql.130).aspx
[Create External Table]: https://msdn.microsoft.com/library/dn935021(v=sql.130).aspx
[CREATE MASTER KEY ]: https://msdn.microsoft.com/library/ms174382.aspx
[CREATE DATABASE SCOPED CREDENTIAL ]: https://msdn.microsoft.com/library/mt270260.aspx
[CREATE TABLE AS SELECT (Transact-SQL)]: https://msdn.microsoft.com/library/mt204041.aspx


<!--Article references-->

[統計]: ./sql-data-warehouse-develop-statistics.md

<!---HONumber=AcomDC_1210_2015-->