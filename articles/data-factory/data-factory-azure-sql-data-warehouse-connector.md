<properties 
	pageTitle="Azure SQL Data Warehouse コネクタ - Azure SQL Data Warehouse との間のデータ移動" 
	description="Azure SQL Data Warehouse との間でのデータ移動を可能にする Data Factory サービスのための Azure SQL Data Warehouse コネクタについて学習する" 
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
	ms.date="07/29/2015" 
	ms.author="spelluru"/>

# Azure SQL Data Warehouse コネクタ - Azure SQL Data Warehouse との間のデータ移動 

この記事では、Data Factory のコピー アクティビティを利用し、Azure SQL Data Warehouse と別のデータ ストアの間でデータを移動する方法について説明します。この記事は、「[データ移動アクティビティ](data-factory-data-movement-activities.md)」という記事に基づき、コピー アクティビティによるデータ移動の一般概要とサポートされるデータ ストアの組み合わせについて紹介しています。

## サンプル: Azure SQL Data Warehouse から Azure BLOB にデータをコピーする

下のサンプルで確認できる要素:

1. AzureSqlDW 型のリンクされたサービス。
2. AzureStorage 型のリンクされたサービス。 
3. AzureSqlDWTable 型の入力データセット。 
4. AzureBlob 型の出力データセット。
4. SqlDWSource と BlobSink を使用するコピー アクティビティを含むパイプライン。

このサンプルはある時系列に属するデータを 1 時間おきに Azure SQL Data Warehouse データベースのテーブルから BLOB にコピーします。これらのサンプルで使用される JSON プロパティの説明はサンプルに続くセクションにあります。

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
 
“external”: ”true” を設定して externalData ポリシーを指定すると、テーブルがデータ ファクトリに対して外部にあり、データ ファクトリのアクティビティでは生成されていないことが Data Factory のサービスに通知されます。

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

上記の入力データセットと出力データセットを使用するように構成され、1 時間おきに実行するようにスケジュールされているコピー アクティビティがパイプラインに含まれています。パイプライン JSON 定義で、**source** 型が **SqlDWSource** に設定され、**sink** 型が **BlobSink** に設定されています。**SqlReaderQuery** プロパティに指定されている SQL クエリは過去のデータを選択してコピーします。

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
	            "SqlReaderQuery": "$$Text.Format('select * from MyTable where timestampcolumn >= \'{0:yyyy-MM-dd HH:mm}\' AND timestampcolumn < \'{1:yyyy-MM-dd HH:mm}\'', WindowStart, WindowEnd)"
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

## サンプル: Azure BLOB から Azure SQL Data Warehouse にデータをコピーする

下のサンプルで確認できる要素:

1.	AzureSqlDWDatabase 型のリンクされたサービス。
2.	AzureStorage 型のリンクされたサービス。
3.	AzureBlob 型の入力データセット。
4.	AzureSqlDWTable 型の出力データセット。
4.	BlobSource と SqlDWSink を使用するコピー アクティビティのパイプライン

このサンプルはある時系列に属するデータを 1 時間おきに Azure BLOB から Azure SQL Data Warehouse データベースのテーブルにコピーします。これらのサンプルで使用される JSON プロパティの説明はサンプルに続くセクションにあります。

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

データは新しい BLOB から 1 時間おきに取得されます (頻度: 時間、間隔: 1)。BLOB のフォルダー パスとファイル名は、処理中のスライスの開始時間に基づき、動的に評価されます。フォルダー パスは開始時間の年、月、日の部分を利用し、ファイル名は開始時間の時刻部分を使用します。「“external”: “true”」設定は Data Factory サービスにこのテーブルが Data Factory の外部にあり、Data Factory のアクティビティでは生成されないことを通知します。

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

**Azure SQL Data Warehouse の出力データセット:**

このサンプルは Azure SQL Data Warehouse の「MyTable」というテーブルにデータをコピーします。BLOB CSV ファイルに含めることが予想される数の列で Azure SQL Data Warehouse にテーブルを作成する必要があります。新しい行は 1 時間ごとにテーブルに追加されます。

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

上記の入力データセットと出力データセットを使用するように構成され、1 時間おきに実行するようにスケジュールされているコピー アクティビティがパイプラインに含まれています。パイプラインの JSON 定義で、**source** 型が **BlobSource** に設定され、**sink** 型が **SqlDWSink** に設定されています。

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

## Azure SQL Data Warehouse のリンクされたサービスのプロパティ

次の表は、Azure SQL Data Warehouse のリンクされたサービスに固有の JSON 要素の説明をまとめたものです。

プロパティ | 説明 | 必須
-------- | ----------- | --------
type | type プロパティを **AzureSqlDW** に設定する必要があります。 | あり
**connectionString** | connectionString プロパティの Azure SQL Database インスタンスに接続するために必要な情報を指定します。 | あり

注: [Azure SQL Database ファイアウォール](https://msdn.microsoft.com/library/azure/ee621782.aspx#ConnectingFromAzure)を構成する必要があります。[サーバーへのアクセスを Azure サービスに許可する](https://msdn.microsoft.com/library/azure/ee621782.aspx#ConnectingFromAzure)ようにデータベース サーバーを構成する必要があります。また、Azure の外部から (たとえば、Data Factory ゲートウェイのあるオンプレミスのデータ ソースから) Azure SQL Data Warehouse にデータをコピーする場合、Azure SQL Data Warehouse にデータを送信するマシンに適切な IP アドレス範囲を設定する必要があります。

## Azure SQL Data Warehouse データセットの type プロパティ

データセットの定義に利用できるセクションとプロパティの完全な一覧については、「[データセットの作成](data-factory-create-datasets.md)」という記事を参照してください。データセット JSON の構造、可用性、ポリシーなどのセクションはすべてのデータセット型 (Azure SQL、Azure BLOB、Azure テーブルなど) で同じです。

typeProperties セクションはデータセット型ごとに異なり、データ ストアのデータの場所などに関する情報を提供します。**AzureSqlDWTable** 型のデータセットの **typeProperties** セクションには次のプロパティがあります。

| プロパティ | 説明 | 必須 |
| -------- | ----------- | -------- |
| tableName | リンクされたサービスが参照する Azure SQL Data Warehouse データベースのテーブルの名前です。 | あり |

## Azure SQL Data Warehouse のコピー アクティビティの type プロパティ

アクティビティの定義に利用できるセクションとプロパティの完全な一覧については、「[パイプラインの作成](data-factory-create-pipelines.md)」という記事を参照してください。名前、説明、入力テーブル、出力テーブル、さまざまなポリシーなどのプロパティがあらゆる種類のアクティビティで利用できます。

**注:** コピー アクティビティは入力を 1 つだけ受け取り、出力を 1 つだけ生成します。

一方で、アクティビティの typeProperties セクションで利用できるプロパティはアクティビティの種類により異なり、コピー アクティビティの場合、source と sink の種類によって異なります。

コピー アクティビティで、source の種類が **SqlDWSource** である場合は、**typeProperties** セクションで次のプロパティを使用できます。

| プロパティ | 説明 | 使用できる値 | 必須 |
| -------- | ----------- | -------------- | -------- |
| sqlReaderQuery | カスタム クエリを使用してデータを読み取ります。 | SQL クエリ文字列。例: Select * from MyTable。指定されていない場合に実行される SQL ステートメント: select from MyTable | いいえ |

**SqlDWSink** では次のプロパティがサポートされます。

| プロパティ | 説明 | 使用できる値 | 必須 |
| -------- | ----------- | -------------- | -------- |
| sqlWriterStoredProcedureName | ターゲット テーブルにデータを UPSERT (更新/挿入) するストアド プロシージャのユーザー指定の名前。 | ストアド プロシージャの名前。 | いいえ |
| sqlWriterTableType | 上記のストアド プロシージャで使用するテーブル型のユーザー指定の名前。コピー アクティビティでは、このテーブル型の一時テーブルでデータを移動できます。その後、ストアド プロシージャのコードにより、コピーされたデータを既存のデータと結合できます。 | テーブルの種類の名前。 | いいえ |
| writeBatchSize | バッファー サイズが writeBatchSize に到達したときに SQL テーブルにデータを挿入します。 | 整数 (単位 = 行数) | いいえ (既定値 = 10000) |
| writeBatchTimeout | タイムアウトする前に一括挿入操作の完了を待つ時間です。 | (単位 = 時間) 例: “00:30:00” (30 分) | いいえ | 
| sqlWriterCleanupScript | 特定のスライスのデータを消去する方法で実行するコピー アクティビティのユーザー指定のクエリ。詳細については、下にある繰り返し性のセクションを参照してください。 | クエリ ステートメント。 | いいえ |
| sliceIdentifierColumnName | コピー アクティビティで、自動生成スライス ID を入力する列のユーザー指定の名前。再実行時、特定のスライスのデータを消去するために使用されます。詳細については、下にある繰り返し性のセクションを参照してください。 | バイナリ (32) のデータ型の列の列名。 | いいえ |

[AZURE.INCLUDE [data-factory-type-repeatability-for-sql-sources](../../includes/data-factory-type-repeatability-for-sql-sources.md)]

[AZURE.INCLUDE [data-factory-structure-for-rectangualr-datasets](../../includes/data-factory-structure-for-rectangualr-datasets.md)]

### Azure SQL Data Warehouse の型のマッピング

「[データ移動アクティビティ](data-factory-data-movement-activities.md)」の記事のとおり、コピー アクティビティは次の 2 段階のアプローチで型を source から sink に自動的に変換します。

1. ネイティブの source 型から .NET 型に変換する
2. .NET 型からネイティブの sink 型に変換する

Azure SQL、SQL Server、Sybase との間でデータを移動するとき、SQL 型から .NET 型へのマッピング (およびその逆) に次のマッピングが使用されます。

マッピングは [ADO.NET の SQL Server データ型マッピング](https://msdn.microsoft.com/library/cc716729.aspx)と同じです。

| SQL Server データベース エンジンの型 | .NET Framework 型 |
| ------------------------------- | ------------------- |
| bigint | Int64 |
| binary | Byte |
| ビット | Boolean |
| char | String、Char |
| date | DateTime |
| Datetime | DateTime |
| datetime2 | DateTime |
| Datetimeoffset | DateTimeOffset |
| 小数点 | 小数点 |
| FILESTREAM 属性 (varbinary(max)) | Byte |
| Float | Double |
| image | Byte | 
| int | Int32 | 
| money | 小数点 |
| nchar | String、Char |
| ntext | String、Char |
| 数値 | 小数点 |
| nvarchar | String、Char |
| real | Single |
| rowversion | Byte |
| smalldatetime | DateTime |
| smallint | Int16 |
| smallmoney | 小数点 | 
| sql\_variant | Object * |
| テキスト | String、Char |
| time | TimeSpan |
| timestamp | Byte |
| tinyint | Byte |
| uniqueidentifier | Guid |
| varbinary | Byte |
| varchar | String、Char |
| xml | Xml |



[AZURE.INCLUDE [data-factory-type-conversion-sample](../../includes/data-factory-type-conversion-sample.md)]

[AZURE.INCLUDE [data-factory-column-mapping](../../includes/data-factory-column-mapping.md)]

<!---HONumber=August15_HO6-->