<properties 
	pageTitle="SQL Data Warehouse との間でのデータの移動 | Microsoft Azure" 
	description="Azure Data Factory を使用して Azure SQL Data Warehouse に、または Azure SQL Data Warehouse からデータを移動する方法を説明します。" 
	services="data-factory" 
	documentationCenter="" 
	authors="spelluru" 
	manager="jhubbard" 
	editor="monicar"/>

<tags 
	ms.service="data-factory" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="09/20/2016" 
	ms.author="spelluru"/>

# Azure Data Factory を使用した Azure SQL Data Warehouse との間でのデータの移動

この記事では、Azure Data Factory のコピー アクティビティを使用して、Azure SQL Data Warehouse と別のデータ ストアの間でデータを移動する方法の概要を説明します。

Azure SQL Data Warehouse にデータを読み込むときに PolyBase を使用するかどうかを指定できます。Azure SQL Data Warehouse へのデータの読み込みで最高のパフォーマンスを実現できるよう、PolyBase の使用をお勧めします。「[PolyBase を使用して Azure SQL Data Warehouse にデータを読み込む](data-factory-azure-sql-data-warehouse-connector.md#use-polybase-to-load-data-into-azure-sql-data-warehouse)」セクションに詳細が記載されています。


## データのコピー ウィザード
Azure SQL Data Warehouse との間でデータをコピーするパイプラインを作成する最も簡単な方法は、データのコピー ウィザードを使用することです。データのコピー ウィザードを使用してパイプラインを作成する簡単な手順については、「[チュートリアル: コピー ウィザードを使用してパイプラインを作成する](data-factory-copy-data-wizard-tutorial.md)」をご覧ください。

以下の例は、[Azure ポータル](data-factory-copy-activity-tutorial-using-azure-portal.md)、[Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md)、または [Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md) を使用してパイプラインを作成する際に使用できるサンプルの JSON 定義です。これらの例は、SQL Data Warehouse と Azure BLOB ストレージの間でデータをコピーする方法を示しています。ただし、Azure Data Factory のコピー アクティビティを使用して、[こちら](data-factory-data-movement-activities.md#supported-data-stores)に記載されているいずれかのシンクに、任意のソースからデータを**直接**コピーすることができます。


> [AZURE.NOTE] 
Azure Data Factory サービスの概要については、[Azure Data Factory の概要](data-factory-introduction.md)に関する記事をご覧ください。
> 
> この記事では、JSON の例を紹介しますが、データ ファクトリを作成するための詳細な手順を説明していません。Azure Data Factory のコピー アクティビティを使用するための手順を含む簡単なチュートリアルについては、[Azure BLOB から Azure SQL Database にデータをコピーする](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)方法に関するチュートリアルをご覧ください。


## サンプル: Azure SQL Data Warehouse から Azure BLOB にデータをコピーする

このサンプルでは、次の Data Factory のエンティティを定義します。

1. [AzureSqlDW](#azure-sql-data-warehouse-linked-service-properties) 型のリンクされたサービス。
2. [AzureStorage](data-factory-azure-blob-connector.md#azure-storage-linked-service-properties) 型のリンクされたサービス。
3. [AzureSqlDWTable](#azure-sql-data-warehouse-dataset-type-properties) 型の入力[データセット](data-factory-create-datasets.md)。
4. [AzureBlob](data-factory-azure-blob-connector.md#azure-blob-dataset-type-properties) 型の出力[データセット](data-factory-create-datasets.md)。
4. [SqlDWSource](#azure-sql-data-warehouse-copy-activity-type-properties) と [BlobSink](data-factory-azure-blob-connector.md#azure-blob-copy-activity-type-properties) を使用するコピー アクティビティを含む[パイプライン](data-factory-create-pipelines.md)。

このサンプルは時系列 (時間単位、日単位など) のデータを 1 時間おきに Azure SQL Data Warehouse データベースのテーブルから BLOB にコピーします。これらのサンプルで使用される JSON プロパティの説明はサンプルに続くセクションにあります。

**Azure SQL Data Warehouse のリンクされたサービス:**

	{
	  "name": "AzureSqlDWLinkedService",
	  "properties": {
	    "type": "AzureSqlDW",
	    "typeProperties": {
	      "connectionString": "Server=tcp:<servername>.database.windows.net,1433;Database=<databasename>;User ID=<username>@<servername>;Password=<password>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30"
	    }
	  }
	}

**Azure BLOB ストレージのリンクされたサービス:**

	{
	  "name": "StorageLinkedService",
	  "properties": {
	    "type": "AzureStorage",
	    "typeProperties": {
	      "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
	    }
	  }
	}

**Azure SQL Data Warehouse の入力データセット:**

このサンプルでは、Azure SQL Data Warehouse で「MyTable」という名前のテーブルを作成し、時系列データ用に「timestampcolumn」という名前の列が含まれているものと想定しています。
 
”external” を ”true” に設定すると、データセットが Data Factory の外部にあり、Data Factory のアクティビティによって生成されたものではないことが Data Factory サービスに通知されます。

	{
	  "name": "AzureSqlDWInput",
	  "properties": {
	    "type": "AzureSqlDWTable",
	    "linkedServiceName": "AzureSqlDWLinkedService",
	    "typeProperties": {
	      "tableName": "MyTable"
	    },
	    "external": true,
	    "availability": {
	      "frequency": "Hour",
	      "interval": 1
	    },
	    "policy": {
	      "externalData": {
	        "retryInterval": "00:01:00",
	        "retryTimeout": "00:10:00",
	        "maximumRetry": 3
	      }
	    }
	  }
	}

**Azure BLOB の出力データセット:**

データは新しい BLOB に 1 時間おきに書き込まれます (頻度: 時間、間隔: 1)。BLOB のフォルダー パスは、処理中のスライスの開始時間に基づき、動的に評価されます。フォルダー パスは開始時間の年、月、日、時刻の部分を使用します。

	{
	  "name": "AzureBlobOutput",
	  "properties": {
	    "type": "AzureBlob",
	    "linkedServiceName": "StorageLinkedService",
	    "typeProperties": {
	      "folderPath": "mycontainer/myfolder/yearno={Year}/monthno={Month}/dayno={Day}/hourno={Hour}",
	      "partitionedBy": [
	        {
	          "name": "Year",
	          "value": {
	            "type": "DateTime",
	            "date": "SliceStart",
	            "format": "yyyy"
	          }
	        },
	        {
	          "name": "Month",
	          "value": {
	            "type": "DateTime",
	            "date": "SliceStart",
	            "format": "MM"
	          }
	        },
	        {
	          "name": "Day",
	          "value": {
	            "type": "DateTime",
	            "date": "SliceStart",
	            "format": "dd"
	          }
	        },
	        {
	          "name": "Hour",
	          "value": {
	            "type": "DateTime",
	            "date": "SliceStart",
	            "format": "HH"
	          }
	        }
	      ],
	      "format": {
	        "type": "TextFormat",
	        "columnDelimiter": "\t",
	        "rowDelimiter": "\n"
	      }
	    },
	    "availability": {
	      "frequency": "Hour",
	      "interval": 1
	    }
	  }
	}


**コピー アクティビティのあるパイプライン:**

パイプラインには、入力データセットと出力データセットを使用するように構成され、1 時間おきに実行するようにスケジュールされているコピー アクティビティが含まれています。パイプライン JSON 定義で、**source** 型が **SqlDWSource** に設定され、**sink** 型が **BlobSink** に設定されています。**SqlReaderQuery** プロパティに指定されている SQL クエリは過去のデータを選択してコピーします。

	{  
	    "name":"SamplePipeline",
	    "properties":{  
	    "start":"2014-06-01T18:00:00",
	    "end":"2014-06-01T19:00:00",
	    "description":"pipeline for copy activity",
	    "activities":[  
	      {
	        "name": "AzureSQLDWtoBlob",
	        "description": "copy activity",
	        "type": "Copy",
	        "inputs": [
	          {
	            "name": "AzureSqlDWInput"
	          }
	        ],
	        "outputs": [
	          {
	            "name": "AzureBlobOutput"
	          }
	        ],
	        "typeProperties": {
	          "source": {
	            "type": "SqlDWSource",
	            "sqlReaderQuery": "$$Text.Format('select * from MyTable where timestampcolumn >= \\'{0:yyyy-MM-dd HH:mm}\\' AND timestampcolumn < \\'{1:yyyy-MM-dd HH:mm}\\'', WindowStart, WindowEnd)"
	          },
	          "sink": {
	            "type": "BlobSink"
	          }
	        },
	       "scheduler": {
	          "frequency": "Hour",
	          "interval": 1
	        },
	        "policy": {
	          "concurrency": 1,
	          "executionPriorityOrder": "OldestFirst",
	          "retry": 0,
	          "timeout": "01:00:00"
	        }
	      }
	     ]
	   }
	}

> [AZURE.NOTE] この例では、SqlDWSource に **sqlReaderQuery** が指定されています。コピー アクティビティでは、データを取得するために SQL Data Warehouse ソースに対してこのクエリを実行します。
>  
> または、**sqlReaderStoredProcedureName** と **storedProcedureParameters** を指定して、ストアド プロシージャを指定することができます (ストアド プロシージャでパラメーターを使用する場合)。
>  
> SqlReaderQuery や sqlReaderStoredProcedureName を指定しない場合は、Azure SQL Database に対して実行するクエリを作成するために、データセット JSON の構造セクションで定義された列が使用されます (mytable から column1 と column2 を選択)。データセット定義に構造がない場合は、すべての列がテーブルから選択されます。

## サンプル: Azure BLOB から Azure SQL Data Warehouse にデータをコピーする

このサンプルでは、次の Data Factory のエンティティを定義します。

1.	[AzureSqlDW](#azure-sql-data-warehouse-linked-service-properties) 型のリンクされたサービス。
2.	[AzureStorage](data-factory-azure-blob-connector.md#azure-storage-linked-service-properties) 型のリンクされたサービス。
3.	[AzureBlob](data-factory-azure-blob-connector.md#azure-blob-dataset-type-properties) 型の入力[データセット](data-factory-create-datasets.md)。
4.	[AzureSqlDWTable](#azure-sql-data-warehouse-dataset-type-properties) 型の出力[データセット](data-factory-create-datasets.md)。
4.	[BlobSource](data-factory-azure-blob-connector.md#azure-blob-copy-activity-type-properties) と [SqlDWSink](#azure-sql-data-warehouse-copy-activity-type-properties) を使用するコピー アクティビティの[パイプライン](data-factory-create-pipelines.md)。


このサンプルは、Azure SQL Data Warehouse データベースのテーブルから Azure BLOB に時系列データ (時間単位、日単位など) を 1 時間おきにコピーします。これらのサンプルで使用される JSON プロパティの説明はサンプルに続くセクションにあります。

**Azure SQL Data Warehouse のリンクされたサービス:**

	{
	  "name": "AzureSqlDWLinkedService",
	  "properties": {
	    "type": "AzureSqlDW",
	    "typeProperties": {
	      "connectionString": "Server=tcp:<servername>.database.windows.net,1433;Database=<databasename>;User ID=<username>@<servername>;Password=<password>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30"
	    }
	  }
	}

**Azure BLOB ストレージのリンクされたサービス:**

	{
	  "name": "StorageLinkedService",
	  "properties": {
	    "type": "AzureStorage",
	    "typeProperties": {
	      "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
	    }
	  }
	}

**Azure BLOB の入力データセット:**

データは新しい BLOB から 1 時間おきに取得されます (頻度: 時間、間隔: 1)。BLOB のフォルダー パスとファイル名は、処理中のスライスの開始時間に基づき、動的に評価されます。フォルダー パスは開始時間の年、月、日の部分を利用し、ファイル名は開始時間の時刻部分を使用します。"external": "true" 設定は Data Factory サービスにこのテーブルが Data Factory の外部にあり、Data Factory のアクティビティでは生成されないことを通知します。

	{
	  "name": "AzureBlobInput",
	  "properties": {
	    "type": "AzureBlob",
	    "linkedServiceName": "StorageLinkedService",
	    "typeProperties": {
	      "folderPath": "mycontainer/myfolder/yearno={Year}/monthno={Month}/dayno={Day}",
	      "fileName": "{Hour}.csv",
	      "partitionedBy": [
	        {
	          "name": "Year",
	          "value": {
	            "type": "DateTime",
	            "date": "SliceStart",
	            "format": "yyyy"
	          }
	        },
	        {
	          "name": "Month",
	          "value": {
	            "type": "DateTime",
	            "date": "SliceStart",
	            "format": "MM"
	          }
	        },
	        {
	          "name": "Day",
	          "value": {
	            "type": "DateTime",
	            "date": "SliceStart",
	            "format": "dd"
	          }
	        },
	        {
	          "name": "Hour",
	          "value": {
	            "type": "DateTime",
	            "date": "SliceStart",
	            "format": "HH"
	          }
	        }
	      ],
	      "format": {
	        "type": "TextFormat",
	        "columnDelimiter": ",",
	        "rowDelimiter": "\n"
	      }
	    },
	    "external": true,
	    "availability": {
	      "frequency": "Hour",
	      "interval": 1
	    },
	    "policy": {
	      "externalData": {
	        "retryInterval": "00:01:00",
	        "retryTimeout": "00:10:00",
	        "maximumRetry": 3
	      }
	    }
	  }
	}

**Azure SQL Data Warehouse の出力データセット:**

このサンプルは Azure SQL Data Warehouse の「MyTable」というテーブルにデータをコピーします。BLOB CSV ファイルに含めることが予想される列の数と同じ列数で Azure SQL Data Warehouse にテーブルを作成する必要があります。新しい行は 1 時間ごとにテーブルに追加されます。

	{
	  "name": "AzureSqlDWOutput",
	  "properties": {
	    "type": "AzureSqlDWTable",
	    "linkedServiceName": "AzureSqlDWLinkedService",
	    "typeProperties": {
	      "tableName": "MyOutputTable"
	    },
	    "availability": {
	      "frequency": "Hour",
	      "interval": 1
	    }
	  }
	}

**コピー アクティビティのあるパイプライン**

パイプラインには、入力データセットと出力データセットを使用するように構成され、1 時間おきに実行するようにスケジュールされているコピー アクティビティが含まれています。パイプラインの JSON 定義で、**source** 型が **BlobSource** に設定され、**sink** 型が **SqlDWSink** に設定されています。

	{  
	    "name":"SamplePipeline",
	    "properties":{  
	    "start":"2014-06-01T18:00:00",
	    "end":"2014-06-01T19:00:00",
	    "description":"pipeline with copy activity",
	    "activities":[  
	      {
	        "name": "AzureBlobtoSQLDW",
	        "description": "Copy Activity",
	        "type": "Copy",
	        "inputs": [
	          {
	            "name": "AzureBlobInput"
	          }
	        ],
	        "outputs": [
	          {
	            "name": "AzureSqlDWOutput"
	          }
	        ],
	        "typeProperties": {
	          "source": {
	            "type": "BlobSource",
	            "blobColumnSeparators": ","
	          },
	          "sink": {
	            "type": "SqlDWSink"
	          }
	        },
	       "scheduler": {
	          "frequency": "Hour",
	          "interval": 1
	        },
	        "policy": {
	          "concurrency": 1,
	          "executionPriorityOrder": "OldestFirst",
	          "retry": 0,
	          "timeout": "01:00:00"
	        }
	      }
	      ]
	   }
	}

チュートリアルについては、Azure SQL Data Warehouse のドキュメント内の記事「[Azure Data Factory を使用してデータを読み込む](../sql-data-warehouse/sql-data-warehouse-get-started-load-with-azure-data-factory.md)」をご覧ください。

## Azure SQL Data Warehouse のリンクされたサービスのプロパティ

次の表は、Azure SQL Data Warehouse のリンクされたサービスに固有の JSON 要素の説明をまとめたものです。

プロパティ | 説明 | 必須
-------- | ----------- | --------
type | type プロパティを **AzureSqlDW** に設定する必要があります。 | はい
**connectionString** | connectionString プロパティの Azure SQL Data Warehouse インスタンスに接続するために必要な情報を指定します。 | はい

> [AZURE.IMPORTANT] [サーバーへのアクセスを Azure サービスに許可する](https://msdn.microsoft.com/library/azure/ee621782.aspx#ConnectingFromAzure)ように [Azure SQL Database ファイアウォール](https://msdn.microsoft.com/library/azure/ee621782.aspx#ConnectingFromAzure) とデータベース サーバーを構成します。さらに、Azure の外部から (たとえば、Data Factory ゲートウェイのあるオンプレミスのデータ ソースから) Azure SQL Data Warehouse にデータをコピーする場合は、Azure SQL Data Warehouse にデータを送信するマシンに適切な IP アドレス範囲を設定します。

## Azure SQL Data Warehouse データセットの type プロパティ

データセットの定義に利用できるセクションとプロパティの完全な一覧については、「[データセットの作成](data-factory-create-datasets.md)」という記事を参照してください。データセット JSON の構造、可用性、ポリシーなどのセクションは、データセットのすべての型 (Azure SQL、Azure BLOB、Azure テーブルなど) でほぼ同じです。

typeProperties セクションはデータセット型ごとに異なり、データ ストアのデータの場所などに関する情報を提供します。**AzureSqlDWTable** 型のデータセットの **typeProperties** セクションには次のプロパティがあります。

| プロパティ | 説明 | 必須 |
| -------- | ----------- | -------- |
| tableName | リンクされたサービスが参照する Azure SQL Data Warehouse データベースのテーブルの名前です。 | はい |

## Azure SQL Data Warehouse のコピー アクティビティの type プロパティ

アクティビティの定義に利用できるセクションとプロパティの完全な一覧については、「[パイプラインの作成](data-factory-create-pipelines.md)」を参照してください。名前、説明、入力テーブル、出力テーブル、ポリシーなどのプロパティは、あらゆる種類のアクティビティで使用できます。

> [AZURE.NOTE]
コピー アクティビティは入力を 1 つだけ受け取り、出力を 1 つだけ生成します。

一方、アクティビティの typeProperties セクションで使用できるプロパティは、各アクティビティの種類によって異なります。コピー アクティビティの場合、ソースとシンクの種類によって異なります。

### SqlDWSource

コピー アクティビティで、source の種類が **SqlDWSource** である場合は、**typeProperties** セクションで次のプロパティを使用できます。

| プロパティ | 説明 | 使用できる値 | 必須 |
| -------- | ----------- | -------------- | -------- |
| sqlReaderQuery | カスタム クエリを使用してデータを読み取ります。 | SQL クエリ文字列。例: Select * from MyTable。 | なし |
| sqlReaderStoredProcedureName | ソース テーブルからデータを読み取るストアド プロシージャの名前。 | ストアド プロシージャの名前。 | いいえ |
| storedProcedureParameters | ストアド プロシージャのパラメーター。 | 名前と値のペア。パラメーターの名前とその大文字と小文字は、ストアド プロシージャのパラメーターの名前とその大文字小文字と一致する必要があります。 | なし |

SqlDWSource に **sqlReaderQuery** が指定されている場合、コピー アクティビティでは、データを取得するために SQL Data Warehouse ソースに対してこのクエリを実行します。

または、**sqlReaderStoredProcedureName** と **storedProcedureParameters** を指定して、ストアド プロシージャを指定することができます (ストアド プロシージャでパラメーターを使用する場合)。

sqlReaderQuery や sqlReaderStoredProcedureName を指定しない場合は、Azure SQL Data Warehouse に対して実行するクエリを作成するために、データセット JSON の構造セクションで定義された列が使用されます。例: `select column1, column2 from mytable`.データセット定義に構造がない場合は、すべての列がテーブルから選択されます。

#### SqlDWSource の例

    "source": {
        "type": "SqlDWSource",
        "sqlReaderStoredProcedureName": "CopyTestSrcStoredProcedureWithParameters",
        "storedProcedureParameters": {
            "stringData": { "value": "str3" },
            "id": { "value": "$$Text.Format('{0:yyyy}', SliceStart)", "type": "Int"}
        }
    }

**ストアド プロシージャの定義:**

	CREATE PROCEDURE CopyTestSrcStoredProcedureWithParameters
	(
		@stringData varchar(20),
		@id int
	)
	AS
	SET NOCOUNT ON;
	BEGIN
	     select *
	     from dbo.UnitTestSrcTable
	     where dbo.UnitTestSrcTable.stringData != stringData
	    and dbo.UnitTestSrcTable.id != id
	END
	GO
 

### SqlDWSink
**SqlDWSink** では次のプロパティがサポートされます。

| プロパティ | 説明 | 使用できる値 | 必須 |
| -------- | ----------- | -------------- | -------- |
| writeBatchSize | バッファー サイズが writeBatchSize に到達したときに SQL テーブルにデータを挿入します。 | 整数 (行数) | いいえ (既定値: 10000) |
| writeBatchTimeout | タイムアウトする前に一括挿入操作の完了を待つ時間です。 | timespan<br/><br/> 例: "00:30:00" (30 分)。 | なし | 
| sqlWriterCleanupScript | 特定のスライスのデータを消去するコピー アクティビティのクエリを指定します。詳細については、[再現性に関するセクション](#repeatability-during-copy)をご覧ください。 | クエリ ステートメント。 | なし |
| allowPolyBase | Azure SQL Data Warehouse へのデータの読み込みに BULKINSERT メカニズムではなく PolyBase (該当する場合) を使用するかどうかを示します。<br/><br/>現時点でサポートされているのは、ソース データセットとして **format** が **TextFormat** に設定されている **Azure BLOB** データセットのみです。<br/><br/>制約と詳細については、「[PolyBase を使用して Azure SQL Data Warehouse にデータを読み込む](#use-polybase-to-load-data-into-azure-sql-data-warehouse)」をご覧ください。 | True <br/>False (既定値) | なし |  
| polyBaseSettings | **allowPolybase** プロパティが **true** に設定されているときに指定できるプロパティのグループ。 | &nbsp; | なし |  
| rejectValue | クエリが失敗するまでに拒否できる行の数または割合を指定します。<br/><br/>PolyBase の拒否オプションの詳細については、「[CREATE EXTERNAL TABLE (Transact-SQL)](https://msdn.microsoft.com/library/dn935021.aspx)」の「**Arguments (引数)**」をご覧ください。 | 0 (既定値)、1、2、… | なし |  
| rejectType | rejectValue オプションをリテラル値と割合のどちらで指定するかを指定します。 | Value (既定値)、Percentage | なし |   
| rejectSampleValue | 拒否された行の割合が PolyBase で再計算されるまでに取得する行数を決定します。 | 1、2、… | はい (**rejectType** が **percentage** の場合) |  
| useTypeDefault | PolyBase がテキスト ファイルからデータを取得する場合に区切りテキスト ファイルに存在しない値を処理する方法を指定します。<br/><br/>このプロパティの詳細については、「[CREATE EXTERNAL FILE FORMAT (Transact-SQL)](https://msdn.microsoft.com/library/dn935026.aspx)」の「Arguments (引数)」をご覧ください。 | True、False (既定値) | なし | 


#### SqlDWSink の例


    "sink": {
        "type": "SqlDWSink",
        "writeBatchSize": 1000000,
        "writeBatchTimeout": "00:05:00"
    }

## PolyBase を使用して Azure SQL Data Warehouse にデータを読み込む
**PolyBase** を使用すると、高いスループットで Azure SQL Data Warehouse に大量のデータを効率的に読み込むことができます。既定の BULKINSERT メカニズムではなく PolyBase を使用することで、スループットが大幅に向上することがわかります。

次の例に示すように、Azure Data Factory で PolyBase を使用して Azure SQL Data Warehouse にデータをコピーするために、**allowPolyBase** プロパティを **true** に設定します。allowPolyBase を true に設定すると、**polyBaseSettings** プロパティ グループを使用して PolyBase 固有のプロパティを指定できます。polyBaseSettings で使用できるプロパティの詳細については、上記の「[SqlDWSink](#SqlDWSink)」をご覧ください。


    "sink": {
        "type": "SqlDWSink",
		"allowPolyBase": true,
		"polyBaseSettings":
		{
			"rejectType": "percentage",
			"rejectValue": 10.0,
			"rejectSampleValue": 100,
			"useTypeDefault": true 
		}

    }

### PolyBase を使用して直接コピーする
ソース データがこのセクションで説明する条件を満たす場合は、PolyBase を使用してソース データ ストアから Azure SQL Data Warehouse に直接コピーできます。それ以外の場合は、[PolyBase を使用したステージング コピー](#staged-copy-using-polybase)を利用できます。

要件が満たされない場合は、Azure Data Factory が設定を確認し、データ移動には自動的に BULKINSERT メカニズムが使用されるように戻ります。

1.	**ソースのリンクされたサービス**の種類が **Azure Storage** で、SAS (Shared Access Signature) 認証を使用するように構成されていないこと。詳細については、「[Azure Storage のリンクされたサービス](data-factory-azure-blob-connector.md#azure-storage-linked-service)」をご覧ください。
2. **入力データセット**の型が **Azure BLOB** で、type プロパティの形式の種類が次の構成の **OrcFormat** または **TextFormat** であること。
	1. **rowDelimiter** が **\\n** である。
	2. **nullValue** が**空の文字列** ("") に設定されている。
	3. **encodingName** が **utf-8** に設定されている。これは**既定**値であるため、別の値に設定しないでください。
	4. **escapeChar** と **quoteChar** が指定されていない。
	5. **Compression** が **BZIP2** ではない。
	 
			"typeProperties": {
				"folderPath": "<blobpath>",
				"format": {
					"type": "TextFormat",     
					"columnDelimiter": "<any delimiter>", 
					"rowDelimiter": "\n",       
					"nullValue": "",           
					"encodingName": "utf-8"    
				},
            	"compression": {  
                	"type": "GZip",  
	                "level": "Optimal"  
    	        }  
			},
3.	パイプラインのコピー アクティビティでは、**BlobSource** の下に **skipHeaderLineCount** 設定がないこと。
4.	パイプラインのコピー アクティビティでは、**SqlDWSink** の下に **sliceIdentifierColumnName** 設定がないこと。(PolyBase で保証されるのは、1 回の実行ですべてのデータが更新されるか、何も更新されないかのいずれかです。**再現性**を実現するには、**sqlWriterCleanupScript** を使用します。
5.	関連付けられたコピー アクティビティで **columnMapping** が使用されていないこと。

### PolyBase を使用してステージング コピーする
ソース データが前のセクションで紹介した条件を満たしていない場合は、中間ステージング Azure BLOB ストレージ経由でのデータのコピーを有効にすることができます。その場合、Azure Data Factory は PolyBase のデータ形式の要件を満たすためにデータの変換を実行し、PolyBase を使用して SQL Data Warehouse にデータを読み込みます。ステージング Azure BLOB 経由でデータをコピーする通常の操作方法の詳細については、「[ステージング コピー](data-factory-copy-activity-performance.md#staged-copy)」をご覧ください。

> [AZURE.IMPORTANT] オンプレミスのデータ ストアから PolyBase を使用して Azure SQL Data Warehouse にデータをコピーし、ステージングする場合は、ソース データを適切な形式に変換するために使用するゲートウェイ コンピューターに JRE 8 (Java ランタイム環境) をインストールします。64 ビットのゲートウェイでは 64 ビットの JRE が必要になり、32 ビットのゲートウェイでは 32 ビットの JRE が必要になります。[Java のダウンロード ページ](http://go.microsoft.com/fwlink/?LinkId=808605)から適切なバージョンをダウンロードしてください。

この機能を使用するには、中間 Blob Storage がある Azure ストレージ アカウントを参照する [Azure Storage のリンクされたサービス](data-factory-azure-blob-connector.md#azure-storage-linked-service)を作成し、次のコードに示すように、コピー アクティビティの **enableStaging** および **stagingSettings** プロパティを指定します。

	"activities":[  
	{
		"name": "Sample copy activity from SQL Server to SQL Data Warehouse via PolyBase",
		"type": "Copy",
		"inputs": [{ "name": "OnpremisesSQLServerInput" }],
		"outputs": [{ "name": "AzureSQLDWOutput" }],
		"typeProperties": {
			"source": {
				"type": "SqlSource",
			},
			"sink": {
				"type": "SqlDwSink",
				"allowPolyBase": true
			},
    		"enableStaging": true,
			"stagingSettings": {
				"linkedServiceName": "MyStagingBlob"
			}
		}
	}
	]


### PolyBase を使用する際のベスト プラクティス

#### 行のサイズ制限
Polybase は、サイズが 32 KB を超える行をサポートしません。32 KB を超える行を含むテーブルを読み込もうとすると、次のエラーが発生します。

	Type=System.Data.SqlClient.SqlException,Message=107093;Row size exceeds the defined Maximum DMS row size: [35328 bytes] is larger than the limit of [32768 bytes],Source=.Net SqlClient

ソース データの行のサイズが 32 KB を超える場合は、ソース テーブルを垂直方向に複数の小さいテーブルに分割し、各テーブルの行の最大サイズが制限を超えないようにすることができます。その後、この分割した小さいテーブルは、PolyBase を使用して Azure SQL Data Warehouse に読み込み、マージすることができます。

#### Azure SQL Data Warehouse の tableName
次の表では、スキーマとテーブル名のさまざまな組み合わせについて、データセットの JSON で **tableName** プロパティを指定する方法の例を示します。

| DB スキーマ | テーブル名 | tableName JSON プロパティ |
| --------- | -----------| ----------------------- | 
| dbo | MyTable | MyTable、dbo.MyTable、または [dbo].[MyTable] |
| dbo1 | MyTable | dbo1.MyTable または [dbo1].[MyTable] |
| dbo | My.Table | [My.Table] または [dbo].[My.Table] |
| dbo1 | My.Table | [dbo1].[My.Table] |

次のエラーが表示される場合は、tableName プロパティに指定した値に問題がある可能性があります。tableName JSON プロパティの値を指定する正しい方法については、上の表を参照してください。

	Type=System.Data.SqlClient.SqlException,Message=Invalid object name 'stg.Account_test'.,Source=.Net SqlClient Data Provider

#### 既定値を持つ列
現在、Data Factory の PolyBase 機能では、ターゲット テーブルと同じ数の列のみを使用できます。たとえば、4 つの列を含むテーブルがあり、その列の 1 つには既定値が定義されているとします。そのような場合にも、入力データには 4 つの列を含める必要があります。3 列の入力データセットを指定すると、次のメッセージのようなエラーが発生します。

	All columns of the table must be specified in the INSERT BULK statement.

null 値は、特殊な形式の既定値です。列が null 値を許容している場合は、その列の (BLOB 内の) 入力データが空である可能性があります (入力データセットからなくすことはできません)。PolyBase は、その列の null 値を Azure SQL Data Warehouse に挿入します。


[AZURE.INCLUDE [data-factory-type-repeatability-for-sql-sources](../../includes/data-factory-type-repeatability-for-sql-sources.md)]

[AZURE.INCLUDE [data-factory-structure-for-rectangualr-datasets](../../includes/data-factory-structure-for-rectangualr-datasets.md)]

### Azure SQL Data Warehouse の型のマッピング

[データ移動アクティビティ](data-factory-data-movement-activities.md)に関する記事のとおり、コピー アクティビティは次の 2 段階のアプローチで型を source から sink に自動的に変換します。

1. ネイティブの source 型から .NET 型に変換する
2. .NET 型からネイティブの sink 型に変換する

Azure SQL、SQL Server、Sybase との間でデータを移動するとき、SQL 型から .NET 型へのマッピング (およびその逆) に次のマッピングが使用されます。

マッピングは [ADO.NET の SQL Server データ型マッピング](https://msdn.microsoft.com/library/cc716729.aspx)と同じです。

| SQL Server Databases エンジンの型 | .NET Framework 型 |
| ------------------------------- | ------------------- |
| bigint | Int64 |
| binary | Byte |
| bit | Boolean |
| char | String、Char |
| date | DateTime |
| Datetime | DateTime |
| datetime2 | DateTime |
| Datetimeoffset | DateTimeOffset |
| Decimal | Decimal |
| FILESTREAM 属性 (varbinary(max)) | Byte |
| Float | Double |
| image | Byte | 
| int | Int32 | 
| money | Decimal |
| nchar | String、Char |
| ntext | String、Char |
| numeric | Decimal |
| nvarchar | String、Char |
| real | Single |
| rowversion | Byte |
| smalldatetime | DateTime |
| smallint | Int16 |
| smallmoney | Decimal | 
| sql\_variant | Object * |
| text | String、Char |
| time | TimeSpan |
| timestamp | Byte |
| tinyint | Byte |
| uniqueidentifier | Guid |
| varbinary | Byte |
| varchar | String、Char |
| xml | Xml |



[AZURE.INCLUDE [data-factory-type-conversion-sample](../../includes/data-factory-type-conversion-sample.md)]

[AZURE.INCLUDE [data-factory-column-mapping](../../includes/data-factory-column-mapping.md)]

## パフォーマンスとチューニング  
Azure Data Factory でのデータ移動 (コピー アクティビティ) のパフォーマンスに影響する主な要因と、パフォーマンスを最適化するための各種方法については、「[コピー アクティビティのパフォーマンスとチューニングに関するガイド](data-factory-copy-activity-performance.md)」をご覧ください。

<!---HONumber=AcomDC_0921_2016-->