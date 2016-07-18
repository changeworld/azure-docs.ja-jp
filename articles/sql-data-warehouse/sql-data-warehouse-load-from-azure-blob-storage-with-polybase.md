<properties
   pageTitle="Azure Blob Storage から SQL Data Warehouse へのデータの読み込み (PolyBase) | Microsoft Azure"
   description="PolyBase を使用して、Azure Blob Storage から SQL Data Warehouse にデータを読み込む方法について説明します。パブリック データから Contoso Retail Data Warehouse スキーマにテーブルをいくつか読み込みます。"
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
   ms.date="06/30/2016"
   ms.author="jrj;barbkess;sonyama"/>


# Azure Blob Storage ストレージから SQL Data Warehouse へのデータの読み込み (PolyBase)

> [AZURE.SELECTOR]
- [Data Factory](sql-data-warehouse-load-from-azure-blob-storage-with-data-factory.md)
- [PolyBase](sql-data-warehouse-load-from-azure-blob-storage-with-polybase.md)

PolyBase コマンドと T-SQL コマンドを使用して、Azure Blob Storage から Azure SQL Data Warehouse にデータを読み込みます。

このチュートリアルでは、シンプルにするために、パブリック Azure Storage BLOB の 2 つのテーブルを Contoso Retail Data Warehouse スキーマに読み込みます。完全なデータ セットを読み込むには、Microsoft SQL Server のサンプル リポジトリから[完全な Contoso Retail Data Warehouse を読み込む][]例を実行します。

このチュートリアルでは、次のことについて説明します。

1. Azure Blob Storage から読み込むために PolyBase を構成する
2. パブリック データをデータベースに読み込む
3. 読み込みの完了後、最適化を実行する。


## 開始する前に
このチュートリアルを実行するには、SQL Data Warehouse データベースを既に持つ Azure アカウントが必要です。アカウントがない場合は、[SQL Data Warehouse の作成][]に関するページをご覧ください。

## 1\.データ ソースの構成

PolyBase では T-SQL 外部オブジェクトを使用して、外部データの場所と属性を定義します。外部オブジェクトの定義は、SQL Data Warehouse に格納されます。データ自体は外部に保存されます。

### 1\.1.資格情報を作成する

Contoso パブリック データを読み込む場合は、**この手順をスキップ**してください。パブリック データにはだれでもアクセスできるため、セキュリティで保護されたアクセスは必要ありません。

このチュートリアルをご自身のデータを読み込むためのテンプレートとして使用する場合は、**この手順をスキップしないでください**。資格情報を使用してデータにアクセスするには、次のスクリプトを使用してデータベース スコープの資格情報を作成し、データ ソースの場所を定義するときに、その資格情報を使用します。


```sql
-- A: Create a master key.
-- Only necessary if one does not already exist.
-- Required to encrypt the credential secret in the next step.

CREATE MASTER KEY;


-- B: Create a database scoped credential
-- IDENTITY: Provide any string, it is not used for authentication to Azure storage.
-- SECRET: Provide your Azure storage account key.


CREATE DATABASE SCOPED CREDENTIAL AzureStorageCredential
WITH
    IDENTITY = 'user',
    SECRET = '<azure_storage_account_key>'
;


-- C: Create an external data source
-- TYPE: HADOOP - PolyBase uses Hadoop APIs to access data in Azure blob storage.
-- LOCATION: Provide Azure storage account name and blob container name.
-- CREDENTIAL: Provide the credential created in the previous step.

CREATE EXTERNAL DATA SOURCE AzureStorage
WITH (
    TYPE = HADOOP,
    LOCATION = 'wasbs://<blob_container_name>@<azure_storage_account_name>.blob.core.windows.net',
    CREDENTIAL = AzureStorageCredential
);
```

スキップして手順 2. に進みます。

### 1\.2.外部データ ソースを作成する

この [CREATE EXTERNAL DATA SOURCE][] コマンドを使用して、データの場所とデータ型を格納します。

```sql
CREATE EXTERNAL DATA SOURCE AzureStorage_west_public
WITH 
(  
    TYPE = Hadoop 
,   LOCATION = 'wasbs://contosoretaildw-tables@contosoretaildw.blob.core.windows.net/'
); 
```

> [AZURE.IMPORTANT] Azure Blob Storage コンテナーをパブリックにすると、データがデータ センターから発信されるときに、データ所有者としてデータ送信料金が課金されることに注意してください。

## 2\.データ形式の構成

データは Azure Blob Storage 内のテキスト ファイルに格納され、各フィールドは区切り記号で区切られます。この [CREATE EXTERNAL FILE FORMAT][] コマンドを実行して、テキスト ファイル内のデータ形式を指定します。Contoso データは圧縮されず、パイプで区切られます。

```sql
CREATE EXTERNAL FILE FORMAT TextFileFormat 
WITH 
(   FORMAT_TYPE = DELIMITEDTEXT
,	FORMAT_OPTIONS	(   FIELD_TERMINATOR = '|'
					,	STRING_DELIMITER = ''
					,	DATE_FORMAT		 = 'yyyy-MM-dd HH:mm:ss.fff'
					,	USE_TYPE_DEFAULT = FALSE 
					)
);
``` 

## 3\.外部テーブルの作成

データ ソースとファイル形式を指定したら、外部テーブルを作成できます。

### 3\.1.データのスキーマを作成する 

データベースの Contoso データを格納する場所を作成するには、スキーマを作成します。

```sql
CREATE SCHEMA [asb]
GO
```

### 3\.2.外部テーブルを作成する 

このスクリプトを実行して、DimProduct 外部テーブルと FactOnlineSales 外部テーブルを作成します。ここでは列名とデータ型を定義し、Azure Blob Storage ファイルの場所と形式にバインドします。定義は SQL Data Warehouse に格納されますが、データはまだ Azure Storage BLOB にあります。

**LOCATION** パラメーターは、Azure Storage BLOB のルート フォルダーの下にあるフォルダーです。各テーブルは、別のフォルダーにあります。


```sql

--DimProduct
CREATE EXTERNAL TABLE [asb].DimProduct (
	[ProductKey] [int] NOT NULL,
	[ProductLabel] [nvarchar](255) NULL,
	[ProductName] [nvarchar](500) NULL,
	[ProductDescription] [nvarchar](400) NULL,
	[ProductSubcategoryKey] [int] NULL,
	[Manufacturer] [nvarchar](50) NULL,
	[BrandName] [nvarchar](50) NULL,
	[ClassID] [nvarchar](10) NULL,
	[ClassName] [nvarchar](20) NULL,
	[StyleID] [nvarchar](10) NULL,
	[StyleName] [nvarchar](20) NULL,
	[ColorID] [nvarchar](10) NULL,
	[ColorName] [nvarchar](20) NOT NULL,
	[Size] [nvarchar](50) NULL,
	[SizeRange] [nvarchar](50) NULL,
	[SizeUnitMeasureID] [nvarchar](20) NULL,
	[Weight] [float] NULL,
	[WeightUnitMeasureID] [nvarchar](20) NULL,
	[UnitOfMeasureID] [nvarchar](10) NULL,
	[UnitOfMeasureName] [nvarchar](40) NULL,
	[StockTypeID] [nvarchar](10) NULL,
	[StockTypeName] [nvarchar](40) NULL,
	[UnitCost] [money] NULL,
	[UnitPrice] [money] NULL,
	[AvailableForSaleDate] [datetime] NULL,
	[StopSaleDate] [datetime] NULL,
	[Status] [nvarchar](7) NULL,
	[ImageURL] [nvarchar](150) NULL,
	[ProductURL] [nvarchar](150) NULL,
	[ETLLoadID] [int] NULL,
	[LoadDate] [datetime] NULL,
	[UpdateDate] [datetime] NULL
)
WITH
(
    LOCATION='/DimProduct/' 
,   DATA_SOURCE = AzureStorage_west_public
,   FILE_FORMAT = TextFileFormat
,   REJECT_TYPE = VALUE
,   REJECT_VALUE = 0
)
;
 
--FactOnlineSales
CREATE EXTERNAL TABLE [asb].FactOnlineSales 
(
	[OnlineSalesKey] [int]  NOT NULL,
	[DateKey] [datetime] NOT NULL,
	[StoreKey] [int] NOT NULL,
	[ProductKey] [int] NOT NULL,
	[PromotionKey] [int] NOT NULL,
	[CurrencyKey] [int] NOT NULL,
	[CustomerKey] [int] NOT NULL,
	[SalesOrderNumber] [nvarchar](20) NOT NULL,
	[SalesOrderLineNumber] [int] NULL,
	[SalesQuantity] [int] NOT NULL,
	[SalesAmount] [money] NOT NULL,
	[ReturnQuantity] [int] NOT NULL,
	[ReturnAmount] [money] NULL,
	[DiscountQuantity] [int] NULL,
	[DiscountAmount] [money] NULL,
	[TotalCost] [money] NOT NULL,
	[UnitCost] [money] NULL,
	[UnitPrice] [money] NULL,
	[ETLLoadID] [int] NULL,
	[LoadDate] [datetime] NULL,
	[UpdateDate] [datetime] NULL
)
WITH
(
    LOCATION='/FactOnlineSales/' 
,   DATA_SOURCE = AzureStorage_west_public
,   FILE_FORMAT = TextFileFormat
,   REJECT_TYPE = VALUE
,   REJECT_VALUE = 0
)
;
```

## 4\.データを読み込む
外部データにはさまざまな方法でアクセスできます。外部テーブルのデータに直接クエリを実行できます。また、データを新しいデータベース テーブルに読み込んだり、外部データを既存のデータベース テーブルに追加したりできます。


### 4\.1.新しいスキーマを作成する

CTAS により、データを含む新しいテーブルが作成されます。最初に、contoso データのスキーマを作成します。

```sql
CREATE SCHEMA [cso]
GO
```

### 4\.2.データを新しいテーブルに読み込む

Azure Blob Storage からデータを読み込み、データベース内のテーブルに保存するには、[CREATE TABLE AS SELECT][] \(CTAS) T-SQL ステートメントを使用します。CTAS での読み込みには、作成したばかりの厳密に型指定されたテーブルを利用します。データを新しいテーブルに読み込むには、テーブルごとに 1 つの [CTAS][] ステートメントを使用します。

CTAS により新しいテーブルが作成され、select ステートメントの結果が設定されます。CTAS では、select ステートメントの結果と同じ列とデータ型が保持されるように、新しいテーブルが定義されます。外部テーブルからすべての列を選択すると、新しいテーブルは、外部テーブルの列とデータ型のレプリカになります。

この例では、ディメンションとファクト テーブルの両方を、ハッシュ分散テーブルとして作成します。


```sql
SELECT GETDATE();
GO

CREATE TABLE [cso].[DimProduct]            WITH (DISTRIBUTION = HASH([ProductKey]  ) ) AS SELECT * FROM [asb].[DimProduct]             OPTION (LABEL = 'CTAS : Load [cso].[DimProduct]             ');
CREATE TABLE [cso].[FactOnlineSales]       WITH (DISTRIBUTION = HASH([ProductKey]  ) ) AS SELECT * FROM [asb].[FactOnlineSales]        OPTION (LABEL = 'CTAS : Load [cso].[FactOnlineSales]        ');
```

### 4\.3 読み込みの進行状況を追跡する

読み込みの進捗状況を追跡するには、`[sys].[dm_pdw_exec_requests]` 動的管理ビュー (DMV) を使用します。

```sql
-- To see all requests
SELECT * FROM sys.dm_pdw_exec_requests;

-- To see a particular request identified by its label
SELECT * FROM sys.dm_pdw_exec_requests as r;
WHERE r.label = 'CTAS : Load [cso].[DimProduct]             '
      OR r.label = 'CTAS : Load [cso].[FactOnlineSales]        '
;
```

## 5\.列ストア圧縮の最適化

既定では、SQL Data Warehouse には、テーブルがクラスター化列ストア インデックスとして格納されます。読み込みの完了時、一部のデータ行が、列ストアに圧縮されない可能性があります。これにはさまざまな理由があります。詳細については、[列ストア インデックスの管理][]に関するページをご覧ください。

読み込み後のクエリのパフォーマンスと列ストア圧縮を最適化するには、列ストア インデックスですべての行が強制的に圧縮されるようにテーブルを再構築します。

```sql
SELECT GETDATE();
GO

ALTER INDEX ALL ON [cso].[DimProduct]               REBUILD;
ALTER INDEX ALL ON [cso].[FactOnlineSales]          REBUILD;
```

列ストア インデックスの保守の詳細については、[列ストア インデックスの管理][]に関する記事をご覧ください。

## 6\.統計の最適化

読み込みの直後に単一列の統計を作成することをお勧めします。統計には選択肢がいくつかあります。たとえば、すべての列に対して単一列の統計を作成する場合は、すべての統計の再構築に時間がかかる場合があります。クエリ述語に含まれない列があることがわかっている場合は、その列に対する統計の作成はスキップできます。

すべてのテーブルのすべての列で単一列の統計を作成する場合は、[統計][]に関する記事のストアド プロシージャのコード サンプル `prc_sqldw_create_stats` を使用できます。

次の例は、統計の作成の出発点として適しています。これによりディメンション テーブルの各列と、ファクト テーブルの各結合列に、単一列の統計が作成されます。他のファクト テーブルの列には、後で単一列または複数列の統計をいつでも追加できます。


```sql
CREATE STATISTICS [stat_cso_DimProduct_AvailableForSaleDate] ON [cso].[DimProduct]([AvailableForSaleDate]);
CREATE STATISTICS [stat_cso_DimProduct_BrandName] ON [cso].[DimProduct]([BrandName]);
CREATE STATISTICS [stat_cso_DimProduct_ClassID] ON [cso].[DimProduct]([ClassID]);
CREATE STATISTICS [stat_cso_DimProduct_ClassName] ON [cso].[DimProduct]([ClassName]);
CREATE STATISTICS [stat_cso_DimProduct_ColorID] ON [cso].[DimProduct]([ColorID]);
CREATE STATISTICS [stat_cso_DimProduct_ColorName] ON [cso].[DimProduct]([ColorName]);
CREATE STATISTICS [stat_cso_DimProduct_ETLLoadID] ON [cso].[DimProduct]([ETLLoadID]);
CREATE STATISTICS [stat_cso_DimProduct_ImageURL] ON [cso].[DimProduct]([ImageURL]);
CREATE STATISTICS [stat_cso_DimProduct_LoadDate] ON [cso].[DimProduct]([LoadDate]);
CREATE STATISTICS [stat_cso_DimProduct_Manufacturer] ON [cso].[DimProduct]([Manufacturer]);
CREATE STATISTICS [stat_cso_DimProduct_ProductDescription] ON [cso].[DimProduct]([ProductDescription]);
CREATE STATISTICS [stat_cso_DimProduct_ProductKey] ON [cso].[DimProduct]([ProductKey]);
CREATE STATISTICS [stat_cso_DimProduct_ProductLabel] ON [cso].[DimProduct]([ProductLabel]);
CREATE STATISTICS [stat_cso_DimProduct_ProductName] ON [cso].[DimProduct]([ProductName]);
CREATE STATISTICS [stat_cso_DimProduct_ProductSubcategoryKey] ON [cso].[DimProduct]([ProductSubcategoryKey]);
CREATE STATISTICS [stat_cso_DimProduct_ProductURL] ON [cso].[DimProduct]([ProductURL]);
CREATE STATISTICS [stat_cso_DimProduct_Size] ON [cso].[DimProduct]([Size]);
CREATE STATISTICS [stat_cso_DimProduct_SizeRange] ON [cso].[DimProduct]([SizeRange]);
CREATE STATISTICS [stat_cso_DimProduct_SizeUnitMeasureID] ON [cso].[DimProduct]([SizeUnitMeasureID]);
CREATE STATISTICS [stat_cso_DimProduct_Status] ON [cso].[DimProduct]([Status]);
CREATE STATISTICS [stat_cso_DimProduct_StockTypeID] ON [cso].[DimProduct]([StockTypeID]);
CREATE STATISTICS [stat_cso_DimProduct_StockTypeName] ON [cso].[DimProduct]([StockTypeName]);
CREATE STATISTICS [stat_cso_DimProduct_StopSaleDate] ON [cso].[DimProduct]([StopSaleDate]);
CREATE STATISTICS [stat_cso_DimProduct_StyleID] ON [cso].[DimProduct]([StyleID]);
CREATE STATISTICS [stat_cso_DimProduct_StyleName] ON [cso].[DimProduct]([StyleName]);
CREATE STATISTICS [stat_cso_DimProduct_UnitCost] ON [cso].[DimProduct]([UnitCost]);
CREATE STATISTICS [stat_cso_DimProduct_UnitOfMeasureID] ON [cso].[DimProduct]([UnitOfMeasureID]);
CREATE STATISTICS [stat_cso_DimProduct_UnitOfMeasureName] ON [cso].[DimProduct]([UnitOfMeasureName]);
CREATE STATISTICS [stat_cso_DimProduct_UnitPrice] ON [cso].[DimProduct]([UnitPrice]);
CREATE STATISTICS [stat_cso_DimProduct_UpdateDate] ON [cso].[DimProduct]([UpdateDate]);
CREATE STATISTICS [stat_cso_DimProduct_Weight] ON [cso].[DimProduct]([Weight]);
CREATE STATISTICS [stat_cso_DimProduct_WeightUnitMeasureID] ON [cso].[DimProduct]([WeightUnitMeasureID]);
CREATE STATISTICS [stat_cso_FactOnlineSales_CurrencyKey] ON [cso].[FactOnlineSales]([CurrencyKey]);
CREATE STATISTICS [stat_cso_FactOnlineSales_CustomerKey] ON [cso].[FactOnlineSales]([CustomerKey]);
CREATE STATISTICS [stat_cso_FactOnlineSales_DateKey] ON [cso].[FactOnlineSales]([DateKey]);
CREATE STATISTICS [stat_cso_FactOnlineSales_OnlineSalesKey] ON [cso].[FactOnlineSales]([OnlineSalesKey]);
CREATE STATISTICS [stat_cso_FactOnlineSales_ProductKey] ON [cso].[FactOnlineSales]([ProductKey]);
CREATE STATISTICS [stat_cso_FactOnlineSales_PromotionKey] ON [cso].[FactOnlineSales]([PromotionKey]);
CREATE STATISTICS [stat_cso_FactOnlineSales_StoreKey] ON [cso].[FactOnlineSales]([StoreKey]);
```

## 結果

パブリック データが Azure SQL Data Warehouse に正常に読み込まれました。すばらしい結果です。

次のようなクエリを使用すると、テーブルに対するクエリ実行を開始できます。

```sql
SELECT  SUM(f.[SalesAmount]) AS [sales_by_brand_amount]
,       p.[BrandName]
FROM    [cso].[FactOnlineSales] AS f
JOIN    [cso].[DimProduct]      AS p ON f.[ProductKey] = p.[ProductKey]
GROUP BY p.[BrandName]
```

SQL Data Warehouse をもっと試してみてください。

## 次のステップ
Contoso Retail Data Warehouse データを完全に読み込むには、スクリプトを使用します。開発に関するその他のヒントについては、[SQL Data Warehouse の開発の概要][]に関するページをご覧ください。

<!--Image references-->

<!--Article references-->
[SQL Data Warehouse の作成]: ./sql-data-warehouse-get-started-provision.md
[Load data into SQL Data Warehouse]: ./sql-data-warehouse-overview-load.md
[SQL Data Warehouse の開発の概要]: ./sql-data-warehouse-overview-develop.md
[列ストア インデックスの管理]: ./sql-data-warehouse-tables-index.md
[統計]: ./sql-data-warehouse-tables-statistics.md
[CTAS]: ./sql-data-warehouse-develop-ctas.md
[label]: ./sql-data-warehouse-develop-label.md

<!--MSDN references-->
[CREATE EXTERNAL DATA SOURCE]: https://msdn.microsoft.com/ja-JP/library/dn935022.aspx
[CREATE EXTERNAL FILE FORMAT]: https://msdn.microsoft.com/ja-JP/library/dn935026.aspx
[sys.dm_pdw_exec_requests]: https://msdn.microsoft.com/library/mt203887.aspx
[REBUILD]: https://msdn.microsoft.com/library/ms188388.aspx

<!--Other Web references-->
[Microsoft Download Center]: http://www.microsoft.com/download/details.aspx?id=36433
[完全な Contoso Retail Data Warehouse を読み込む]: https://github.com/Microsoft/sql-server-samples/tree/master/samples/databases/contoso-data-warehouse/readme.md

<!---HONumber=AcomDC_0706_2016-->