<properties 
	pageTitle="Azure BLOB との間でのデータの移動 | Azure Data Factory" 
	description="Azure Data Factory を使用して Azure BLOB ストレージに、または Azure BLOB ストレージからデータを移動する方法を説明します。" 
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
	ms.date="11/03/2015" 
	ms.author="spelluru"/>

# Azure Data Factory を使用した Azure BLOB との間でのデータの移動
この記事では、Azure Data Factory のコピー アクティビティを利用し、Azure BLOB と別のデータ ストアの間でデータを移動する方法について説明します。この記事は、「[データ移動アクティビティ](data-factory-data-movement-activities.md)」という記事に基づき、コピー アクティビティによるデータ移動の一般概要とサポートされるデータ ストアの組み合わせについて紹介しています。

## サンプル: Azure BLOB から Azure SQL にデータをコピーする
下のサンプルで確認できる要素:

1.	[AzureSqlDatabase](data-factory-azure-sql-connector.md#azure-sql-linked-service-properties) 型のリンクされたサービス。
2.	[AzureStorage](#azure-storage-linked-service-properties) 型のリンクされたサービス。
3.	[AzureBlob](#azure-blob-dataset-type-properties) 型の入力[データセット](data-factory-create-datasets.md)。
4.	[AzureSqlTable](data-factory-azure-sql-connector.md#azure-sql-dataset-type-properties) 型の出力[データセット](data-factory-create-datasets.md)。
4.	[BlobSource](#azure-blob-copy-activity-type-properties) と [SqlSink](data-factory-azure-sql-connector.md#azure-sql-copy-activity-type-properties) を使用するコピー アクティビティの[パイプライン](data-factory-create-pipelines.md)。

このサンプルはある時系列に属するデータを 1 時間おきに Azure BLOB から Azure SQL Database のテーブルにコピーします。これらのサンプルで使用される JSON プロパティの説明はサンプルに続くセクションにあります。

**Azure SQL のリンクされたサービス:**

	{
	  "name": "AzureSqlLinkedService",
	  "properties": {
	    "type": "AzureSqlDatabase",
	    "typeProperties": {
	      "connectionString": "Server=tcp:<servername>.database.windows.net,1433;Database=<databasename>;User ID=<username>@<servername>;Password=<password>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30"
	    }
	  }
	}

**Azure Storage のリンクされたサービス:**

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

データは新しい BLOB から 1 時間おきに取得されます (頻度: 時間、間隔: 1)。BLOB のフォルダー パスとファイル名は、処理中のスライスの開始時間に基づき、動的に評価されます。フォルダー パスは開始時間の年、月、日の部分を利用し、ファイル名は開始時間の時刻部分を使用します。「“external”: “true”」設定は Data Factory サービスにこのテーブルが Data Factory の外部にあり、Data Factory のアクティビティでは生成されないことを通知します。

	{
	  "name": "AzureBlobInput",
	  "properties": {
	    "type": "AzureBlob",
	    "linkedServiceName": "StorageLinkedService",
	    "typeProperties": {
	      "folderPath": "mycontainer/myfolder/yearno={Year}/monthno={Month}/dayno={Day}/",
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

**Azure SQL の出力データセット:**

このサンプルは Azure SQL Database の「MyTable」というテーブルにデータをコピーします。BLOB CSV ファイルに含めることが予想される数の列で Azure SQL Database にテーブルを作成する必要があります。新しい行は 1 時間ごとにテーブルに追加されます。

	{
	  "name": "AzureSqlOutput",
	  "properties": {
	    "type": "AzureSqlTable",
	    "linkedServiceName": "AzureSqlLinkedService",
	    "typeProperties": {
	      "tableName": "MyOutputTable"
	    },
	    "availability": {
	      "frequency": "Hour",
	      "interval": 1
	    }
	  }
	}

**コピー アクティビティのあるパイプライン:**

上記の入力データセットと出力データセットを使用するように構成され、1 時間おきに実行するようにスケジュールされているコピー アクティビティがパイプラインに含まれています。パイプラインの JSON 定義で、**source** 型が **BlobSource** に設定され、**sink** 型が **SqlSink** に設定されています。

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
	            "name": "AzureSqlOutput"
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

## サンプル: Azure SQL から Azure BLOB にデータをコピーする
下のサンプルで確認できる要素:

1.	[AzureSqlDatabase](data-factory-azure-sql-connector.md#azure-sql-linked-service-properties) 型のリンクされたサービス。
2.	[AzureStorage](#azure-storage-linked-service-properties) 型のリンクされたサービス。
3.	[AzureSqlTable](data-factory-azure-sql-connector.md#azure-sql-dataset-type-properties) 型の入力[データセット](data-factory-create-datasets.md)。
4.	[AzureBlob](#azure-blob-dataset-type-properties) 型の出力[データセット](data-factory-create-datasets.md)。
4.	[SqlSource](data-factory-azure-sql-connector.md#azure-sql-copy-activity-type-properties) と [BlobSink](#azure-blob-copy-activity-type-properties) を使用するコピー アクティビティを含む[パイプライン](data-factory-create-pipelines.md)。


このサンプルはある時系列に属するデータを 1 時間おきに Azure SQL Database のテーブルから BLOB にコピーします。これらのサンプルで使用される JSON プロパティの説明はサンプルに続くセクションにあります。

**Azure SQL のリンクされたサービス:**

	{
	  "name": "AzureSqlLinkedService",
	  "properties": {
	    "type": "AzureSqlDatabase",
	    "typeProperties": {
	      "connectionString": "Server=tcp:<servername>.database.windows.net,1433;Database=<databasename>;User ID=<username>@<servername>;Password=<password>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30"
	    }
	  }
	}

**Azure ストレージのリンクされたサービス:**

	{
	  "name": "StorageLinkedService",
	  "properties": {
	    "type": "AzureStorage",
	    "typeProperties": {
	      "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
	    }
	  }
	}

**Azure SQL の入力データセット:**

このサンプルでは、Azure SQL で「MyTable」という名前のテーブルを作成し、時系列データ用に「timestampcolumn」という名前の列が含まれているものと想定しています。

“external”: ”true” を設定して externalData ポリシーを指定すると、テーブルが Data Factory に対して外部にあり、Data Factory のアクティビティでは生成されてないことが Azure Data Factory のサービスに通知されます。

	{
	  "name": "AzureSqlInput",
	  "properties": {
	    "type": "AzureSqlTable",
	    "linkedServiceName": "AzureSqlLinkedService",
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
	      "folderPath": "mycontainer/myfolder/yearno={Year}/monthno={Month}/dayno={Day}/hourno={Hour}/",
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

上記の入力データセットと出力データセットを使用するように構成され、1 時間おきに実行するようにスケジュールされているコピー アクティビティがパイプラインに含まれています。パイプライン JSON 定義で、**source** 型が **SqlSource** に設定され、**sink** 型が **BlobSink** に設定されています。**SqlReaderQuery** プロパティに指定されている SQL クエリは過去のデータを選択してコピーします。


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
		    	        "name": "AzureSQLInput"
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

## Azure Storage のリンクされたサービスのプロパティ

Azure Storage のリンクされたサービスを利用し、Azure ストレージ アカウントを Azure Data Factory にリンクできます。次の表は、Azure Storage のリンクされたサービスに固有の JSON 要素の説明をまとめたものです。

| プロパティ | 説明 | 必須 |
| -------- | ----------- | -------- |
| type | type プロパティを **AzureStorage** に設定する必要があります。 | あり |
| connectionString | connectionString プロパティのために Azure ストレージに接続するために必要な情報を指定します。Azure クラシック ポータルから Azure ストレージの connectionString を取得できます。 | あり |

## Azure BLOB データセットの type プロパティ

データセットの定義に利用できる JSON のセクションとプロパティの完全一覧については、「[データセットの作成](data-factory-create-datasets.md)」という記事を参照してください。データセット JSON の構造、可用性、ポリシーなどのセクションはすべてのデータセット型 (Azure SQL、Azure BLOB、Azure テーブルなど) で同じです。

**typeProperties** セクションはデータセット型ごとに異なり、データ ストアのデータの場所や書式などに関する情報を提供します。**AzureBlob** 型のデータセットの typeProperties セクションには次のプロパティがあります。

| プロパティ | 説明 | 必須 |
| -------- | ----------- | -------- | 
| folderPath | BLOB ストレージのコンテナーとフォルダーのパス。例: myblobcontainer\\myblobfolder\\ | あり |
| fileName | <p>blob. fileName の名前は任意です。</p><p>fileName を指定した場合、アクティビティ (コピーを含む) は特定の BLOB で機能します。</p><p>fileName が指定されていない場合、コピーには入力データセットの folderPath のすべての BLOB が含まれます。</p><p>出力データセットに fileName が指定されていないとき、「Data.<Guid>.txt (例: Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt</p>)」の形式でファイルが生成されます。 | いいえ |
| partitionedBy | partitionedBy は任意のプロパティです。これを使用し、時系列データに動的な folderPath と fileName を指定できます。たとえば、1 時間ごとのデータに対して folderPath をパラメーター化できます。詳細と例については、「[partitionedBy プロパティの活用](#Leveraging-partitionedBy-property)」セクションを参照してください。 | いいえ
| BlobSink の format | **TextFormat** と **AvroFormat** の 2 種類の形式がサポートされています。形式の下にある type プロパティをいずれかの値に設定する必要があります。形式が TextFormat のとき、形式に追加で任意のプロパティを指定できます。詳細については、下にある「[TextFormat の指定](#specifying-textformat)」セクションを参照してください。 | いいえ
| compression | データの圧縮の種類とレベルを指定します。サポートされる種類: GZip、Deflate、および BZip2。サポートされるレベル: Optimal および Fastest。詳細については、「[圧縮のサポート](#compression-support)」セクションを参照してください。 | いいえ |

### partitionedBy プロパティの活用
前述のように、時系列データに対して動的な folderPath と fileName を指定するときに、**partitionedBy** セクション、Data Factory マクロ、特定のデータ スライスの開始時刻と終了時刻を示すシステム変数の SliceStart と SliceEnd を使用できます。

partitionedBy セクションで使用可能な Data Factory の変数と関数については、「[Data Factory のシステム変数](data-factory-scheduling-and-execution.md#data-factory-system-variables)」と「[Data Factory 関数リファレンス](data-factory-scheduling-and-execution.md#data-factory-functions-reference)」を参照してください。

時系列データセット、スケジュール作成、スライスに関する詳細については、「[データセットの作成](data-factory-create-datasets.md)」と「[スケジュールと実行](data-factory-scheduling-and-execution.md)」を参照してください。

#### サンプル 1

	"folderPath": "wikidatagateway/wikisampledataout/{Slice}",
	"partitionedBy": 
	[
	    { "name": "Slice", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyyMMddHH" } },
	],

上記の例では、{Slice} は、指定された形式 (YYYYMMDDHH) で、Data Factory システム変数の SliceStart の値で置換されます。SliceStart はスライスの開始時刻です。folderPath はスライスごとに異なります。例: wikidatagateway/wikisampledataout/2014100103 または wikidatagateway/wikisampledataout/2014100104

#### サンプル 2

	"folderPath": "wikidatagateway/wikisampledataout/{Year}/{Month}/{Day}",
	"fileName": "{Hour}.csv",
	"partitionedBy": 
	 [
	    { "name": "Year", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyy" } },
	    { "name": "Month", "value": { "type": "DateTime", "date": "SliceStart", "format": "MM" } }, 
	    { "name": "Day", "value": { "type": "DateTime", "date": "SliceStart", "format": "dd" } }, 
	    { "name": "Hour", "value": { "type": "DateTime", "date": "SliceStart", "format": "hh" } } 
	],

上記の例では、SliceStart の年、月、日、時刻が folderPath プロパティと fileName プロパティで使用される個別の変数に抽出されます。

### TextFormat の指定

書式が **TextFormat** に設定されている場合、次の**任意の**プロパティを **Format** セクションで指定できます。

| プロパティ | 説明 | 必須 |
| -------- | ----------- | -------- |
| columnDelimiter | ファイルの列の区切り記号として使用される文字です。このタグは任意です。既定値はコンマです (,)。 | いいえ |
| rowDelimiter | ファイルの行の区切り記号として使用される文字です。このタグは任意です。既定値は [“\\r\\n”, “\\r”,” \\n”] のいずれかになります。 | いいえ |
| escapeChar | <p>コンテンツに表示される列区切り記号のエスケープに使用される特殊文字です。このタグは任意です。既定値はありません。このプロパティに指定する文字は 1 つだけです。</p><p>たとえば、列の区切り文字としてコンマ (,) を使用しているときにテキストにもコンマ文字が必要な場合 (例: “Hello, world”)、エスケープ文字として「$」を定義し、ソースで文字列「Hello$, world」を使用できます。</p><p>1 つのテーブルに escapeChar と quoteChar の両方は指定できないことに注意してください。</p> | いいえ | 
| quoteChar | <p>この特殊文字は文字列値を引用符で囲むために使用されます。引用符文字内の列区切り文字と行区切り文字は文字列値の一部として処理されます。このタグは任意です。既定値はありません。このプロパティに指定する文字は 1 つだけです。</p><p>たとえば、列の区切り文字としてコンマ (,) を使用しているときにテキストにもコンマ文字が必要な場合 (例: <Hello  world>)、引用符文字として「"」を定義し、ソースで文字列「"Hello, world"」を使用できます。このプロパティは入力テーブルと出力テーブルの両方に適用されます。</p><p>1 つのテーブルに escapeChar と quoteChar の両方は指定できないことに注意してください。</p> | いいえ |
| nullValue | <p>BLOB ファイル コンテンツで null 値を表すために使用する文字です。このタグは任意です。既定値は「\\N」です。</p><p>たとえば、上記のサンプルに基づくと、BLOB の「NaN」は SQL Server などにコピーされるときに null 値として変換されます。</p> | いいえ |
| encodingName | エンコード名の指定。有効なエンコード名の一覧については、[Encoding.EncodingName プロパティに関する記事](https://msdn.microsoft.com/library/system.text.encoding.aspx)を参照してください。例: windows-1250 または shift\_jis。既定値は UTF-8 です。 | いいえ | 

#### サンプル
次の例は、TextFormat の format プロパティの一部を示します。

	"typeProperties":
	{
	    "folderPath": "mycontainer/myfolder",
	    "fileName": "myblobname"
	    "format":
	    {
	        "type": "TextFormat",
	        "columnDelimiter": ",",
	        "rowDelimiter": ";",
	        "quoteChar": """,
	        "NullValue": "NaN"
	    }
	},

quoteChar ではなく escapeChar を使用するには、quoteChar の行を次で置き換えます。

	"escapeChar": "$",

### AvroFormat の指定
形式が AvroFormat に設定されている場合、typeProperties セクション内の Format セクションにプロパティを指定する必要はありません。例:

	"format":
	{
	    "type": "AvroFormat",
	}

Hive テーブルで Avro 形式を使用するには、[Apache Hive のチュートリアルに関するページ](https://cwiki.apache.org/confluence/display/Hive/AvroSerDe)を参照してください。

[AZURE.INCLUDE [data-factory-compression](../../includes/data-factory-compression.md)]


## Azure BLOB のコピー アクティビティの type プロパティ  
アクティビティの定義に利用できるセクションとプロパティの完全な一覧については、「[パイプラインの作成](data-factory-create-pipelines.md)」を参照してください。名前、説明、入力テーブル、出力テーブル、さまざまなポリシーなどのプロパティがあらゆる種類のアクティビティで利用できます。

一方で、アクティビティの typeProperties セクションで利用できるプロパティはアクティビティの種類により異なり、コピー アクティビティの場合、sources と sinks の種類によって異なります。

**BlobSource** の **typeProperties** セクションでは次のプロパティがサポートされます。

| プロパティ | 説明 | 使用できる値 | 必須 |
| -------- | ----------- | -------------- | -------- | 
| treatEmptyAsNull | null と空の文字列を NULL 値として処理するかどうかを指定します。 | TRUE<br/>FALSE | いいえ |
| skipHeaderLineCount | スキップする必要がある行数を指定します。入力データセットで **TextFormat** を利用しているときにのみ適用されます。 | 0 から最大値までの整数。 | いいえ | 
| recursive | データをサブ フォルダーから再帰的に読み取るか、指定したフォルダーからのみ読み取るかを指定します。 | True (既定値)、False | いいえ | 


**BlobSink** の **typeProperties** セクションでは次のプロパティがサポートされます。

| プロパティ | 説明 | 使用できる値 | 必須 |
| -------- | ----------- | -------------- | -------- |
| blobWriterAddHeader | 列定義のヘッダーを追加するかどうかを指定します。 | TRUE<br/>FALSE (既定値) | いいえ |
| copyBehavior | ソースが BlobSource または FileSystem である場合のコピー動作を定義します。 | <p>copyBehavior プロパティには 3 つの値があります。</p><ul><li>**PreserveHierarchy:** ターゲット フォルダー内でファイル階層を保持します。つまり、ソース フォルダーへのソース ファイルの相対パスはターゲット フォルダーへのターゲット ファイルの相対パスと同じになります。</li><li>。**FlattenHierarchy:** ソース フォルダーのすべてのファイルはターゲット フォルダーの最上位レベルに配置されます。ターゲット ファイルの名前は自動的に生成されます。</li><li>**MergeFiles (既定):** ソース フォルダーのすべてのファイルを 1 つのファイルにマージします。ファイル名/BLOB 名を指定した場合、マージ後のファイルの名前は指定した名前になります。それ以外は自動生成されたファイル名になります。</li></ul> | いいえ |

### recursive と copyBehavior の例
このセクションでは、recursive 値と copyBhavior 値の組み合わせごとに、Copy 操作で行われる動作について説明します。

recursive | copyBehavior | 行われる動作
--------- | ------------ | --------
true | preserveHierarchy | <p>ソース フォルダ Folder1 が次のような構造の場合:</p> <p>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5</p>ターゲット フォルダ Folder1 の構造はソースと同じになります。<p>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5</p>.  
true | flattenHierarchy | <p>ソース フォルダ Folder1 が次のような構造の場合: </p> <p>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5</p>ターゲットの Folder1 の構造は次のようになります: <p>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1 の自動生成された名前<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2 の自動生成された名前<br/>&nbsp;&nbsp;&nbsp;&nbsp;File3 の自動生成された名前<br/>&nbsp;&nbsp;&nbsp;&nbsp;File4 の自動生成された名前<br/>&nbsp;&nbsp;&nbsp;&nbsp;File5 の自動生成された名前</p>
true | mergeFiles | <p>ソース フォルダ Folder1 が次のような構造の場合:</p> <p>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5</p>ターゲットの Folder1 の構造は次のようになります: <p>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1、File2、File3、File4、File 5 の内容は、自動生成されたファイル名を持つ 1 つのファイルにマージされます。</p>
false | preserveHierarchy | <p>ソース フォルダ Folder1 が次のような構造の場合:</p> <p>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5</p>ターゲット フォルダ Folder1 の構造は次のようになります。<p>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/></p><p>Subfolder1 と File3、File4、File5 は取得されません。</p>
false | flattenHierarchy | <p>ソース フォルダ Folder1 が次のような構造の場合:</p> <p>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5</p>ターゲット フォルダ Folder1 の構造は次のようになります。<p>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1 の自動生成された名前<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2 の自動生成された名前<br/></p><p>Subfolder1 と File3、File4、File5 は取得されません。</p>
false | mergeFiles | <p>ソース フォルダ Folder1 が次のような構造の場合:</p> <p>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5</p>ターゲット フォルダ Folder1 の構造は次のようになります。<p>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1 と File2 の内容は、自動生成されたファイル名 (File1 の自動生成された名前) を持つ 1 つのファイルにマージされます。</p><p>Subfolder1 と File3、File4、File5 は取得されません。</p>.

  


[AZURE.INCLUDE [data-factory-structure-for-rectangualr-datasets](../../includes/data-factory-structure-for-rectangualr-datasets.md)]

[AZURE.INCLUDE [data-factory-type-conversion-sample](../../includes/data-factory-type-conversion-sample.md)]

[AZURE.INCLUDE [data-factory-column-mapping](../../includes/data-factory-column-mapping.md)]

<!---HONumber=AcomDC_1203_2015-->