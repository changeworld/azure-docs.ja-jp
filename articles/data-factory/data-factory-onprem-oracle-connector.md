<properties 
	pageTitle="Oracle からのデータの移動 | Azure Data Factory" 
	description="Azure Data Factory を使用してオンプレミスの Oracle データベースとの間でデータを移動する方法を説明します。" 
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
	ms.date="08/26/2015" 
	ms.author="spelluru"/>

# Azure Data Factory を使用してオンプレミスの Oracle からデータを移動する 

この記事では、データ ファクトリのコピー アクティビティを使用して Oracle から他のデータ ソースにデータを移動する方法について説明します。この記事は、「[データ移動アクティビティ](data-factory-data-movement-activities.md)」という記事に基づき、コピー アクティビティによるデータ移動の一般概要とサポートされるデータ ストアの組み合わせについて紹介しています。

## サンプル: Oracle から Azure BLOB にデータをコピーする

下のサンプルで確認できる要素:

1.	[OnPremisesOracle](data-factory-onprem-oracle-connector.md#oracle-linked-service-properties) 型のリンクされたサービス。
2.	[AzureStorage](data-factory-azure-blob-connector.md#azure-storage-linked-service-properties) 型のリンクされたサービス。
3.	[OracleTable](data-factory-onprem-oracle-connector.md#oracle-dataset-type-properties) 型の入力[データセット](data-factory-create-datasets.md)。 
4.	[AzureBlob](data-factory-azure-blob-connector.md#azure-blob-dataset-type-properties) 型の出力[データセット](data-factory-create-datasets.md)。
5.	source として [OracleSource](data-factory-onprem-oracle-connector.md#oracle-copy-activity-type-properties) を、sink として [BlobSink](data-factory-azure-blob-connector.md#azure-blob-copy-activity-type-properties) を使用するコピー アクティビティを含む[パイプライン](data-factory-create-pipelines.md)。

このサンプルはオンプレミスの Oracle データベース内のテーブルから BLOB に 1 時間ごとにデータをコピーします。下のサンプルで使用されるさまざまなプロパティの詳細については、サンプルに続くセクションの各プロパティのドキュメントを参照してください。

**Oracle のリンクされたサービス:**

	{
	  "name": "OnPremisesOracleLinkedService",
	  "properties": {
	    "type": "OnPremisesOracle",
	    "typeProperties": {
	      "ConnectionString": "data source=<data source>;User Id=<User Id>;Password=<Password>;",
	      "gatewayName": "<gateway name>"
	    }
	  }
	}

**Azure BLOB ストレージのリンクされたサービス:**

	{
	  "name": "StorageLinkedService",
	  "properties": {
	    "type": "AzureStorage",
	    "typeProperties": {
	      "connectionString": "DefaultEndpointsProtocol=https;AccountName=<account name>;AccountKey=<Account key>"
	    }
	  }
	}

**Oracle の入力データセット:**

このサンプルでは、Oracle で「MyTable」という名前のテーブルを作成し、時系列データ用に「timestampcolumn」という名前の列が含まれているものと想定しています。

“external”: ”true” を設定して externalData ポリシーを指定すると、これがデータ ファクトリに対して外部にあるテーブルで、データ ファクトリのアクティビティでは生成されていないことが Data Factory に通知されます。

	{
	    "name": "OracleInput",
	    "properties": {
	        "type": "OracleTable",
	        "linkedServiceName": "OnPremisesOracleLinkedService",
	        "typeProperties": {
	            "tableName": "MyTable"
	        },
	           "external": true,
	        "availability": {
	            "offset": "01:00:00",
	            "interval": "1",
	            "anchorDateTime": "2014-02-27T12:00:00",
	            "frequency": "Hour"
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

データは新しい BLOB に 1 時間おきに書き込まれます (頻度: 時間、間隔: 1)。BLOB のフォルダー パスとファイル名は、処理中のスライスの開始時間に基づき、動的に評価されます。フォルダー パスは開始時間の年、月、日、時刻の部分を使用します。
	
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
	            "format": "%M"
	          }
	        },
	        {
	          "name": "Day",
	          "value": {
	            "type": "DateTime",
	            "date": "SliceStart",
	            "format": "%d"
	          }
	        },
	        {
	          "name": "Hour",
	          "value": {
	            "type": "DateTime",
	            "date": "SliceStart",
	            "format": "%H"
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

上記の入力データセットと出力データセットを使用するように構成され、1 時間おきに実行するようにスケジュールされているコピー アクティビティがパイプラインに含まれています。パイプライン JSON 定義で、**source** 型が **RelationalSource** に設定され、**sink** 型が **BlobSink** に設定されています。**oracleReaderQuery** プロパティに指定されている SQL クエリは過去のデータを選択してコピーします。

	
	{  
	    "name":"SamplePipeline",
	    "properties":{  
	    "start":"2014-06-01T18:00:00",
	    "end":"2014-06-01T19:00:00",
	    "description":"pipeline for copy activity",
	    "activities":[  
	      {
	        "name": "AzureSQLtoBlob",
	        "description": "copy activity",
	        "type": "Copy",
	        "inputs": [
	          {
	            "name": " OracleInput"
	          }
	        ],
	        "outputs": [
	          {
	            "name": "AzureBlobOutput"
	          }
	        ],
	        "typeProperties": {
	          "source": {
	            "type": "OracleSource",
	            "oracleReaderQuery": "$$Text.Format('select * from MyTable where timestampcolumn >= \\'{0:yyyy-MM-dd HH:mm}\\' AND timestampcolumn < \\'{1:yyyy-MM-dd HH:mm}\\'', WindowStart, WindowEnd)"
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

## Oracle のリンクされたサービスのプロパティ

次の表は、Oracle のリンクされたサービスに固有の JSON 要素の説明をまとめたものです。

プロパティ | 説明 | 必須
-------- | ----------- | --------
type | type プロパティを **OnPremisesOracle** に設定する必要があります | あり
connectionString | connectionString プロパティの Oracle Database インスタンスに接続するために必要な情報を指定します。 | あり 
gatewayName | オンプレミスの Oracle サーバーへの接続に使用されるゲートウェイの名前です | あり

オンプレミスの Oracle データ ソースの資格情報の設定について詳しくは、「[資格情報とセキュリティの設定](data-factory-move-data-between-onprem-and-cloud.md#setting-credentials-and-security)」をご覧ください。
## Oracle データセットの type プロパティ

データセットの定義に利用できるセクションとプロパティの完全な一覧については、「[データセットの作成](data-factory-create-datasets.md)」という記事を参照してください。データセット JSON の構造、可用性、ポリシーなどのセクションはすべてのデータセット型 (Oracle、Azure BLOB、Azure テーブルなど) で同じです。
 
typeProperties セクションはデータセット型ごとに異なり、データ ストアのデータの場所などに関する情報を提供します。OracleTable 型のデータセットの typeProperties セクションには次のプロパティがあります。

プロパティ | 説明 | 必須
-------- | ----------- | --------
tableName | リンクされたサービスが参照する Oracle データベース インスタンスのテーブルの名前です。 | あり

## Oracle のコピー アクティビティの type プロパティ

アクティビティの定義に利用できるセクションとプロパティの完全な一覧については、「[パイプラインの作成](data-factory-create-pipelines.md)」という記事を参照してください。名前、説明、入力テーブル、出力テーブル、さまざまなポリシーなどのプロパティがあらゆる種類のアクティビティで利用できます。

**注:** コピー アクティビティは入力を 1 つだけ受け取り、出力を 1 つだけ生成します。

一方で、アクティビティの typeProperties セクションで利用できるプロパティはアクティビティの種類により異なり、コピー アクティビティの場合、source と sink の種類によって異なります。

コピー アクティビティで、source の種類が SqlSource である場合は、typeProperties セクションで次のプロパティを使用できます。

プロパティ | 説明 |使用できる値 | 必須
-------- | ----------- | ------------- | --------
oracleReaderQuery | カスタム クエリを使用してデータを読み取ります。 | SQL クエリ文字列。 
例: select * from MyTable <p>指定されていない場合に実行される SQL ステートメント: select * from MyTable</p> | いいえ

[AZURE.INCLUDE [data-factory-structure-for-rectangualr-datasets](../../includes/data-factory-structure-for-rectangualr-datasets.md)]

### Oracle の型マッピング

「[データ移動アクティビティ](data-factory-data-movement-activities.md)」の記事のとおり、コピー アクティビティは次の 2 段階のアプローチで型を source から sink に自動的に変換します。

1. ネイティブの source 型から .NET 型に変換する
2. .NET 型からネイティブの sink 型に変換する

Oracle からデータを移動すると、Oracle データ型から .NET 型へのマッピング (およびその逆) に、次のマッピングが使用されます。

Oracle データ型 | .NET Framework データ型
---------------- | ------------------------
BFILE | Byte
BLOB | Byte
CHAR | String
CLOB | String
DATE | DateTime
FLOAT | 小数点
INTEGER | 小数点
INTERVAL YEAR TO MONTH | Int32
INTERVAL DAY TO SECOND | TimeSpan
LONG | String
LONG RAW | Byte
NCHAR | String
NCLOB | String
NUMBER | 小数点
NVARCHAR2 | String
RAW | Byte
ROWID | String
TIMESTAMP | DateTime
TIMESTAMP WITH LOCAL TIME ZONE | DateTime
TIMESTAMP WITH TIME ZONE | DateTime
符号なし INTEGER | Number
VARCHAR2 | String
XML | String


[AZURE.INCLUDE [data-factory-column-mapping](../../includes/data-factory-column-mapping.md)]

<!---HONumber=Oct15_HO2-->