<properties
	pageTitle="SQL Server との間でのデータの移動 | Azure Data Factory"
	description="Azure Data Factory を使用してオンプレミスまたは Azure VM の SQL Server データベースとの間でデータを移動する方法を説明します。"
	services="data-factory"
	documentationCenter=""
	authors="linda33wj"
	manager="jhubbard"
	editor="monicar"/>

<tags
	ms.service="data-factory"
	ms.workload="data-services"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="08/31/2016"
	ms.author="jingwang"/>

# Azure Data Factory を使用してオンプレミスまたは IaaS (Azure VM) の SQL Server との間でデータを移動する
この記事では、コピー アクティビティを使用して SQL Server と他のデータ ストアの間でデータを移動する方法について説明します。この記事は、データ移動の概要と、ソースおよびシンクとしてサポートされているデータ ストアについて紹介している、[データ移動アクティビティ](data-factory-data-movement-activities.md)に関する記事に基づいています。

## サポートされているソースとシンク
コピー アクティビティによってソースまたはシンクとしてサポートされているデータ ストアの一覧については、[サポートされているデータ ストア](data-factory-data-movement-activities.md#supported-data-stores-and-formats)の表をご覧ください。サポートされる任意のソース データ ストアのデータを、SQL Database に移動したり、SQL Database のデータを、サポートされる任意のシンク データ ストアに移動したりできます。

## パイプラインの作成
さまざまなツール/API を使用して、オンプレミスの SQL Server データベースとの間でデータを移動するコピー アクティビティでパイプラインを作成できます。

- コピー ウィザード
- Azure ポータル
- Visual Studio
- Azure PowerShell
- .NET API
- REST API

さまざまな方法でコピー アクティビティによってパイプラインを作成する手順については、[コピー アクティビティのチュートリアル](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)をご覧ください。

## 接続を有効にする

オンプレミスまたは Azure IaaS (サービスとしてのインフラストラクチャ) VM でホストされている SQL Server と接続するために必要な概念と手順は同じです。いずれの場合でも、接続には Data Management Gateway を使用する必要があります。

Data Management Gateway の詳細およびゲートウェイの設定手順については、「[オンプレミスの場所とクラウド間のデータ移動](data-factory-move-data-between-onprem-and-cloud.md)」を参照してください。ゲートウェイ インスタンスの設定は、SQL Server との接続の前提条件です。

SQL Server と同じオンプレミス コンピューターまたはクラウド VM インスタンスにゲートウェイをインストールできますが、パフォーマンスのためには、別のコンピューターにインストールすることをお勧めします。ゲートウェイと SQL Server を別のコンピューターにインストールすることで、リソースの競合が減少します。


## データのコピー ウィザード
SQL Server データベースから、サポートされているシンク データ ストアにデータをコピーするパイプラインを作成する最も簡単な方法は、データのコピー ウィザードを使用することです。データのコピー ウィザードを使用してパイプラインを作成する簡単な手順については、「[チュートリアル: コピー ウィザードを使用してパイプラインを作成する](data-factory-copy-data-wizard-tutorial.md)」をご覧ください。

次の例は、[Azure ポータル](data-factory-copy-activity-tutorial-using-azure-portal.md)、[Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md)、または [Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md) を使用してパイプラインを作成する際に使用できるサンプルの JSON 定義です。次のサンプルは、SQL Server と Azure BLOB ストレージとの間でデータをコピーする方法を示します。ただし、Azure Data Factory のコピー アクティビティを使用して、[ここ](data-factory-data-movement-activities.md#supported-data-stores)に示したいずれかのシンクに、任意のソースからデータを**直接**コピーすることができます。

## サンプル: SQL Server から Azure BLOB にデータをコピーする

次のサンプルは以下を示しています。

1.	[OnPremisesSqlServer](data-factory-sqlserver-connector.md#sql-server-linked-service-properties) 型のリンクされたサービス。
2.	[AzureStorage](data-factory-azure-blob-connector.md#azure-storage-linked-service-properties) 型のリンクされたサービス。
3.	[SqlServerTable](data-factory-sqlserver-connector.md#sql-server-dataset-type-properties) 型の入力[データセット](data-factory-create-datasets.md)。
4.	[AzureBlob](data-factory-azure-blob-connector.md#azure-blob-dataset-type-properties) 型の出力[データセット](data-factory-create-datasets.md)。
4.	[SqlSource](data-factory-sqlserver-connector.md#sql-server-copy-activity-type-properties) と [BlobSink](data-factory-azure-blob-connector.md#azure-blob-copy-activity-type-properties) を使用するコピー アクティビティを含む[パイプライン](data-factory-create-pipelines.md)。

このサンプルは、SQL Server テーブルから Azure BLOB に時系列データを 1 時間おきにコピーします。これらのサンプルで使用される JSON プロパティの説明はサンプルに続くセクションにあります。

最初の手順として、データ管理ゲートウェイを設定します。設定手順は、[オンプレミスの場所とクラウドの間でのデータ移動](data-factory-move-data-between-onprem-and-cloud.md)に関する記事に記載されています。

**SQL Server のリンクされているサービス**

	{
	  "Name": "SqlServerLinkedService",
	  "properties": {
	    "type": "OnPremisesSqlServer",
	    "typeProperties": {
	      "connectionString": "Data Source=<servername>;Initial Catalog=<databasename>;Integrated Security=False;User ID=<username>;Password=<password>;",
	      "gatewayName": "<gatewayname>"
	    }
	  }
	}

**Azure BLOB ストレージのリンクされたサービス**

	{
	  "name": "StorageLinkedService",
	  "properties": {
	    "type": "AzureStorage",
	    "typeProperties": {
	      "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
	    }
	  }
	}

**SQL Server 入力データセット**

このサンプルでは、SQL Server で「MyTable」という名前のテーブルを作成し、時系列データ用に「timestampcolumn」という名前の列が含まれているものと想定しています。1 つのデータセットを使用して同じデータベース内の複数のテーブルに対してクエリを実行することはできますが、データセットの tableName typeProperty には 1 つのテーブルを使用する必要があります。

"external": "true" の設定により、このデータセットが Data Factory の外部にあり、Data Factory のアクティビティによって生成されたものではないことが Data Factory サービスに通知されます。

	{
	  "name": "SqlServerInput",
	  "properties": {
	    "type": "SqlServerTable",
	    "linkedServiceName": "SqlServerLinkedService",
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

**Azure BLOB の出力データセット**

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

**コピー アクティビティのあるパイプライン**

パイプラインには、この入力データセットと出力データセットを使用するように構成され、1 時間おきに実行するようにスケジュールされているコピー アクティビティが含まれています。パイプライン JSON 定義で、**source** 型が **SqlSource** に設定され、**sink** 型が **BlobSink** に設定されています。**SqlReaderQuery** プロパティに指定されている SQL クエリは過去のデータを選択してコピーします。


	{  
	    "name":"SamplePipeline",
	    "properties":{  
	    "start":"2014-06-01T18:00:00",
	    "end":"2014-06-01T19:00:00",
	    "description":"pipeline for copy activity",
	    "activities":[  
	      {
	        "name": "SqlServertoBlob",
	        "description": "copy activity",
	        "type": "Copy",
	        "inputs": [
	          {
	            "name": " SqlServerInput"
	          }
	        ],
	        "outputs": [
	          {
	            "name": "AzureBlobOutput"
	          }
	        ],
	        "typeProperties": {
	          "source": {
	            "type": "SqlSource",
	            "SqlReaderQuery": "$$Text.Format('select * from MyTable where timestampcolumn >= \\'{0:yyyy-MM-dd HH:mm}\\' AND timestampcolumn < \\'{1:yyyy-MM-dd HH:mm}\\'', WindowStart, WindowEnd)"
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


この例では、SqlSource に **sqlReaderQuery** が指定されています。コピー アクティビティでは、データを取得するために SQL Server Database ソースに対してこのクエリを実行します。または、**sqlReaderStoredProcedureName** と **storedProcedureParameters** を指定して、ストアド プロシージャを指定することができます (ストアド プロシージャでパラメーターを使用する場合)。sqlReaderQuery は、入力データセットによって参照されるデータベースで複数のテーブルを参照できます。参照できるテーブルは、データセットの tableName typeProperty として設定されるテーブルに限りません。


SqlReaderQuery または sqlReaderStoredProcedureName を指定しない場合は、SQL Server Database に対して実行する選択クエリを作成するために、構造セクションで定義された列が使用されます。データセット定義に構造がない場合は、すべての列がテーブルから選択されます。


SqlSource と BlobSink でサポートされるプロパティの一覧については、「[SqlSource](#sqlsource)」および [BlobSink](data-factory-azure-blob-connector.md#azure-blob-copy-activity-type-properties) に関する記述を参照してください。

## サンプル: Azure BLOB から SQL Server にデータをコピーする

次のサンプルは以下を示しています。

1.	[OnPremisesSqlServer](data-factory-sqlserver-connector.md#sql-server-linked-service-properties) 型のリンクされたサービス。
2.	[AzureStorage](data-factory-azure-blob-connector.md#azure-storage-linked-service-properties) 型のリンクされたサービス。
3.	[AzureBlob](data-factory-azure-blob-connector.md#azure-blob-dataset-type-properties) 型の入力[データセット](data-factory-create-datasets.md)。
4.	[SqlServerTable](data-factory-sqlserver-connector.md#sql-server-dataset-type-properties) 型の出力[データセット](data-factory-create-datasets.md)。
4.	[BlobSource](data-factory-azure-blob-connector.md#azure-blob-copy-activity-type-properties) と [SqlSink](data-factory-sqlserver-connector.md#sql-server-copy-activity-type-properties) を使用するコピー アクティビティの[パイプライン](data-factory-create-pipelines.md)。

このサンプルは、Azure BLOB から SQL Server テーブルに時系列データを 1 時間おきにコピーします。これらのサンプルで使用される JSON プロパティの説明はサンプルに続くセクションにあります。

**SQL Server のリンクされているサービス**

	{
	  "Name": "SqlServerLinkedService",
	  "properties": {
	    "type": "OnPremisesSqlServer",
	    "typeProperties": {
	      "connectionString": "Data Source=<servername>;Initial Catalog=<databasename>;Integrated Security=False;User ID=<username>;Password=<password>;",
	      "gatewayName": "<gatewayname>"
	    }
	  }
	}

**Azure BLOB ストレージのリンクされたサービス**

	{
	  "name": "StorageLinkedService",
	  "properties": {
	    "type": "AzureStorage",
	    "typeProperties": {
	      "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
	    }
	  }
	}

**Azure BLOB の入力データセット**

データは新しい BLOB から 1 時間おきに取得されます (頻度: 時間、間隔: 1)。BLOB のフォルダー パスとファイル名は、処理中のスライスの開始時間に基づき、動的に評価されます。フォルダー パスでは開始時間の年、月、日の部分を使用し、ファイル名では開始時間の時刻部分を使用します。"external": "true" の設定により、このデータセットが Data Factory の外部にあり、Data Factory のアクティビティによって生成されたものではないことが Data Factory サービスに通知されます。

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

**SQL Server の出力データセット**

このサンプルは SQL Server の「MyTable」というテーブルにデータをコピーします。BLOB CSV ファイルに含めることが予想される数の列で SQL Server にテーブルを作成します。新しい行は 1 時間ごとにテーブルに追加されます。

	{
	  "name": "SqlServerOutput",
	  "properties": {
	    "type": "SqlServerTable",
	    "linkedServiceName": "SqlServerLinkedService",
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

パイプラインには、この入力データセットと出力データセットを使用するように構成され、1 時間おきに実行するようにスケジュールされているコピー アクティビティが含まれています。パイプラインの JSON 定義で、**source** 型が **BlobSource** に設定され、**sink** 型が **SqlSink** に設定されています。

	{  
	    "name":"SamplePipeline",
	    "properties":{  
	    "start":"2014-06-01T18:00:00",
	    "end":"2014-06-01T19:00:00",
	    "description":"pipeline with copy activity",
	    "activities":[  
	      {
	        "name": "AzureBlobtoSQL",
	        "description": "Copy Activity",
	        "type": "Copy",
	        "inputs": [
	          {
	            "name": "AzureBlobInput"
	          }
	        ],
	        "outputs": [
	          {
	            "name": " SqlServerOutput "
	          }
	        ],
	        "typeProperties": {
	          "source": {
	            "type": "BlobSource",
	            "blobColumnSeparators": ","
	          },
	          "sink": {
	            "type": "SqlSink"
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

## SQL Server のリンクされたサービスのプロパティ
サンプルでは、**OnPremisesSqlServer** 型のリンクされたサービスを使用して、オンプレミスの SQL Server データベースをデータ ファクトリにリンクします。次の表は、オンプレミスの SQL Server のリンクされたサービスに固有の JSON 要素の説明をまとめたものです。

次の表は、SQL Server のリンクされたサービスに固有の JSON 要素の説明をまとめたものです。

| プロパティ | 説明 | 必須 |
| -------- | ----------- | -------- |
| type | type プロパティは、**OnPremisesSqlServer** に設定されます。 | はい |
| connectionString | SQL 認証または Windows 認証を使用して、オンプレミス SQL Server データベースに接続するために必要な connectionString 情報を指定します。 | はい |
| gatewayName | Data Factory サービスが、オンプレミスの SQL Server データベースへの接続に使用するゲートウェイの名前です。 | はい |
| username | Windows 認証を使用している場合は、ユーザー名を指定します。例: **domainname\\username**。 | いいえ |
| パスワード | ユーザー名に指定したユーザー アカウントのパスワードを指定します。 | いいえ |

**New-AzureRmDataFactoryEncryptValue** コマンドレットを使用して資格情報を暗号化し、次の例で示すようにそれを接続文字列で使用できます (**EncryptedCredential** プロパティ)。

	"connectionString": "Data Source=<servername>;Initial Catalog=<databasename>;Integrated Security=True;EncryptedCredential=<encrypted credential>",

### サンプル

**SQL 認証を使用している場合の JSON**

	{
	    "name": "MyOnPremisesSQLDB",
	    "properties":
	    {
	        "type": "OnPremisesSqlLinkedService",
			"typeProperties": {
	        	"connectionString": "Data Source=<servername>;Initial Catalog=MarketingCampaigns;Integrated Security=False;User ID=<username>;Password=<password>;",
	        	"gatewayName": "<gateway name>"
			}
	    }
	}

**Windows 認証を使用している場合の JSON**

ユーザー名とパスワードが指定されている場合、ゲートウェイはその情報を使用して指定されたユーザー アカウントに偽装して、オンプレミス SQL Server データベースに接続します。指定されていない場合、ゲートウェイは、Gateway (スタートアップ アカウント) のセキュリティ コンテキストを使用して SQL Server に直接接続します。

	{
	     "Name": " MyOnPremisesSQLDB",
	     "Properties":
	     {
	         "type": "OnPremisesSqlLinkedService",
			 "typeProperties": {
	         	"ConnectionString": "Data Source=<servername>;Initial Catalog=MarketingCampaigns;Integrated Security=True;",
	         	"username": "<domain\\username>",
	         	"password": "<password>",
	         	"gatewayName": "<gateway name>"
			}
	     }
	}

SQL Server データ ソースの資格情報の設定について詳しくは、「[資格情報とセキュリティの設定](data-factory-move-data-between-onprem-and-cloud.md#set-credentials-and-security)」をご覧ください。

## SQL Server データセットの type プロパティ
サンプルでは、**SqlServerTable** 型のデータセットを使用して、SQL データベースのテーブルを表しています。

データセットの定義に利用できるセクションとプロパティの完全な一覧については、「[データセットの作成](data-factory-create-datasets.md)」という記事を参照してください。データセット JSON の構造、可用性、ポリシーなどのセクションは、すべてのデータセット型 (SQL Server 、Azure BLOB、Azure テーブルなど) で同じです。

typeProperties セクションはデータセット型ごとに異なり、データ ストアのデータの場所などに関する情報を提供します。**SqlServerTable** 型のデータセットの **typeProperties** セクションには次のプロパティがあります。

| プロパティ | 説明 | 必須 |
| -------- | ----------- | -------- |
| tableName | リンクされたサービスが参照する SQL Server Database インスタンスのテーブルの名前です。 | あり |

## SQL Server のコピー アクティビティの type プロパティ
SQL Server データベースからデータを移動する場合は、コピー アクティビティのソースの種類を **SqlSource** に設定します。同様に、SQL Server データベースにデータを移動する場合は、コピー アクティビティのシンクの種類を **SqlSink** に設定します。このセクションでは、SqlSource と SqlSink でサポートされるプロパティの一覧を示します。

アクティビティの定義に利用できるセクションとプロパティの完全な一覧については、「[パイプラインの作成](data-factory-create-pipelines.md)」を参照してください。名前、説明、入力テーブル、出力テーブル、ポリシーなどのプロパティは、あらゆる種類のアクティビティで使用できます。

> [AZURE.NOTE] コピー アクティビティは入力を 1 つだけ受け取り、出力を 1 つだけ生成します。

一方、アクティビティの typeProperties セクションで使用できるプロパティは、各アクティビティの種類によって異なります。コピー アクティビティの場合、ソースとシンクの種類によって異なります。

### SqlSource

コピー アクティビティで、source の種類が **SqlSource** である場合は、**typeProperties** セクションで次のプロパティを使用できます。

| プロパティ | 説明 | 使用できる値 | 必須 |
| -------- | ----------- | -------------- | -------- |
| sqlReaderQuery | カスタム クエリを使用してデータを読み取ります。 | SQL クエリ文字列。例: Select * from MyTable。入力データセットによって参照されるデータベースから複数のテーブルを参照する場合があります。指定されていない場合に実行される SQL ステートメント: select from MyTable | いいえ |
| sqlReaderStoredProcedureName | ソース テーブルからデータを読み取るストアド プロシージャの名前。 | ストアド プロシージャの名前。 | いいえ |
| storedProcedureParameters | ストアド プロシージャのパラメーター。 | 名前と値のペア。パラメーターの名前とその大文字と小文字は、ストアド プロシージャのパラメーターの名前とその大文字小文字と一致する必要があります。 | いいえ |

SqlSource に **sqlReaderQuery** が指定されている場合、コピー アクティビティでは、データを取得するために SQL Server Database ソースに対してこのクエリを実行します。

または、**sqlReaderStoredProcedureName** と **storedProcedureParameters** を指定して、ストアド プロシージャを指定することができます (ストアド プロシージャでパラメーターを使用する場合)。

SqlReaderQuery または sqlReaderStoredProcedureName を指定しない場合は、SQL Server Database に対して実行する選択クエリを作成するために、構造セクションで定義された列が使用されます。データセット定義に構造がない場合は、すべての列がテーブルから選択されます。

> [AZURE.NOTE] **sqlReaderStoredProcedureName** を使用する場合でも、データセット JSON の **tableName** プロパティの値を指定する必要があります。ただし、このテーブルに対して実行される検証はありません。

### SqlSink

**SqlSink** では次のプロパティがサポートされます。


| プロパティ | 説明 | 使用できる値 | 必須 |
| -------- | ----------- | -------------- | -------- |
| writeBatchTimeout | タイムアウトする前に一括挿入操作の完了を待つ時間です。 | timespan<br/><br/> 例: "00:30:00" (30 分)。 | いいえ |
| writeBatchSize | バッファー サイズが writeBatchSize に達したときに SQL テーブルにデータを挿入します。 | 整数 (行数) | いいえ (既定値: 10000)
| sqlWriterCleanupScript | 特定のスライスのデータを消去するコピー アクティビティのクエリを指定します。詳細については、[再現性に関するセクション](#repeatability-during-copy)をご覧ください。 | クエリ ステートメント。 | いいえ |
| sliceIdentifierColumnName | 自動生成スライス ID を入力するためのコピー アクティビティの列名を指定します。再実行時、特定のスライスのデータを消去するときに使用されます。詳細については、[再現性に関するセクション](#repeatability-during-copy)をご覧ください。 | バイナリ (32) のデータ型の列の列名。 | いいえ |
| sqlWriterStoredProcedureName | 対象テーブルにデータをアップサート (更新/挿入) するストアド プロシージャの名前。 | ストアド プロシージャの名前。 | いいえ |
| storedProcedureParameters | ストアド プロシージャのパラメーター。 | 名前と値のペア。パラメーターの名前とその大文字と小文字は、ストアド プロシージャのパラメーターの名前とその大文字小文字と一致する必要があります。 | いいえ |
| sqlWriterTableType | ストアド プロシージャで使用するテーブル型の名前を指定します。コピー アクティビティでは、このテーブル型の一時テーブルでデータを移動できます。その後、ストアド プロシージャのコードにより、コピーされたデータを既存のデータと結合できます。 | テーブルの種類の名前。 | いいえ |

## 接続の問題のトラブルシューティング

1. リモート接続を許可するよう、SQL Server を構成します。**SQL Server Management Studio** を起動し、**サーバー**を右クリックして、**[プロパティ]** をクリックします。一覧から **[接続]** を選択し、**[このサーバーへのリモート接続を許可する]** をオンにします。

	![リモート接続を有効にする](.\media\data-factory-sqlserver-connector\AllowRemoteConnections.png)

	詳細な手順については、「[remote access サーバー構成オプションの構成](https://msdn.microsoft.com/library/ms191464.aspx)」をご覧ください。
2. **SQL Server 構成マネージャー**を起動します。目的のインスタンスの **[SQL Server ネットワークの構成]** を展開し、**[MSSQLSERVER のプロトコル]** を選択します。右側のウィンドウにプロトコルが表示されます。**[TCP/IP]** を右クリックし、**[有効化]** をクリックして TCP/IP を有効にします。

	![TCP/IP を有効にする](.\media\data-factory-sqlserver-connector\EnableTCPProptocol.png)

	詳細および TCP/IP プロトコルを有効にする別の方法については、「[サーバー ネットワーク プロトコルの有効化または無効化](https://msdn.microsoft.com/library/ms191294.aspx)」をご覧ください。
3. 同じウィンドウで、**[TCP/IP]** をダブルクリックして、**[TCP/IP のプロパティ]** ウィンドウを起動します。
4. **[IP アドレス]** タブに切り替えます。下へスクロールして **[IPAll]** セクションを表示します。**[TCP ポート]** の値をメモしておきます (既定値は **1433** です)。
5. コンピューターに **Windows Firewall のルール**を作成し、このポート経由の受信トラフィックを許可します。
6. **接続の確認**: 完全修飾名を使って SQL Server に接続するには、別のコンピューターから SQL Server Management Studio を使用します。たとえば、"<コンピューター>.< ドメイン >.corp.<会社> .com,1433" を使用します。

	> [AZURE.IMPORTANT]
	詳細については、「[ポートとセキュリティに関する考慮事項](data-factory-move-data-between-onprem-and-cloud.md#port-and-security-considerations)」をご覧ください。
	>   
	> 接続/ゲートウェイに関する問題のトラブルシューティングのヒントについては、[ゲートウェイの問題のトラブルシューティング](data-factory-data-management-gateway.md#troubleshoot-gateway-issues)に関するセクションをご覧ください。

## ターゲット データベースの ID 列
このセクションでは、ID 列がないソース テーブルから ID 列がある対象テーブルにデータをコピーする例を示します。

**ソース テーブル:**

	create table dbo.SourceTbl
	(
	       name varchar(100),
	       age int
	)

**対象テーブル:**

	create table dbo.TargetTbl
	(
	       id int identity(1,1),
	       name varchar(100),
	       age int
	)


ターゲット テーブルには ID 列があることに注意してください。

**ソース データセット JSON の定義**

	{
	    "name": "SampleSource",
	    "properties": {
	        "published": false,
	        "type": " SqlServerTable",
	        "linkedServiceName": "TestIdentitySQL",
	        "typeProperties": {
	            "tableName": "SourceTbl"
	        },
	        "availability": {
	            "frequency": "Hour",
	            "interval": 1
	        },
	        "external": true,
	        "policy": {}
	    }
	}

**対象データセット JSON の定義**

	{
	    "name": "SampleTarget",
	    "properties": {
	        "structure": [
	            { "name": "name" },
	            { "name": "age" }
	        ],
	        "published": false,
	        "type": "AzureSqlTable",
	        "linkedServiceName": "TestIdentitySQLSource",
	        "typeProperties": {
	            "tableName": "TargetTbl"
	        },
	        "availability": {
	            "frequency": "Hour",
	            "interval": 1
	        },
	        "external": false,
	        "policy": {}
	    }
	}


ソースとターゲット テーブルには異なるスキーマがあることに注意してください (ターゲットには ID を持つ追加の列があります)。このシナリオでは、ターゲット データセット定義で **structure** プロパティを指定する必要があります。ここでは、ID 列は含みません。

次に、ソース データセットの列をターゲット データセットの列にマップします。例については、「[列マッピングの例](#column-mapping-samples)」を参照してください。

[AZURE.INCLUDE [data-factory-type-repeatability-for-sql-sources](../../includes/data-factory-type-repeatability-for-sql-sources.md)]


[AZURE.INCLUDE [data-factory-sql-invoke-stored-procedure](../../includes/data-factory-sql-invoke-stored-procedure.md)]


[AZURE.INCLUDE [data-factory-structure-for-rectangualr-datasets](../../includes/data-factory-structure-for-rectangualr-datasets.md)]


### SQL Server と Azure SQL の型のマッピング

[データ移動アクティビティ](data-factory-data-movement-activities.md)に関する記事のとおり、コピー アクティビティは次の 2 段階のアプローチで型を source から sink に自動的に変換します。

1. ネイティブの source 型から .NET 型に変換する
2. .NET 型からネイティブの sink 型に変換する

Azure SQL、SQL Server、Sybase との間でデータを移動するとき、SQL 型から .NET 型へのマッピング (およびその逆) に次のマッピングが使用されます。

マッピングは ADO.NET の SQL Server データ型マッピングと同じです。

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

<!---HONumber=AcomDC_0928_2016-->