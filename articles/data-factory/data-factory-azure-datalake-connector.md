<properties
	pageTitle="Azure Data Lake Store の間でのデータの移動 | Azure Data Factory"
	description="Azure Data Factory を使用して Azure Data Lake Store に、または Azure Data Lake Store からデータを移動する方法を説明します。"
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
	ms.date="02/25/2016"
	ms.author="spelluru"/>

# Azure Data Factory を使用した Azure Data Lake Store との間でのデータの移動
この記事では、Azure Data Factory のコピー アクティビティを利用し、Azure Data Lake Store と別のデータ ストアの間でデータを移動する方法について説明します。この記事は、「[データ移動アクティビティ](data-factory-data-movement-activities.md)」という記事に基づき、コピー アクティビティによるデータ移動の一般概要とサポートされるデータ ストアの組み合わせについて紹介しています。

> [AZURE.NOTE]
Azure Data Lake Store との間でデータを移動するには、コピー アクティビティを含むパイプラインを作成する前に Azure Data Lake Store アカウントを作成する必要があります。Azure Data Lake Store の詳細については、[Azure Data Lake Store の概要](../data-lake-store/data-lake-store-get-started-portal.md)に関するページを参照してください。
>  
> Data Factory、リンクされたサービス、データセット、およびパイプラインを作成する詳細な手順については、[最初のパイプラインを作成するチュートリアル](data-factory-build-your-first-pipeline.md)に関するページを確認してください。Data Factory エディター、Visual Studio、または Azure PowerShell と JSON のスニペットを使用して、Data Factory エンティティを作成できます。

次のサンプルは、Azure Data Lake Store と Azure BLOB ストレージとの間でデータをコピーする方法を示します。ただし、Azure Data Factory のコピー アクティビティを使用して[ここ](data-factory-data-movement-activities.md#supported-data-stores)から開始したいずれかのシンクに、任意のソースからデータを**直接**コピーすることができます。


## サンプル: Azure BLOB から Azure Data Lake Store にデータをコピーする
下のサンプルで確認できる要素:

1.	[AzureStorage](#azure-storage-linked-service-properties) 型のリンクされたサービス。
2.	[AzureDataLakeStore](#azure-data-lake-linked-service-properties) 型のリンクされたサービス。
3.	[AzureBlob](#azure-blob-dataset-type-properties) 型の入力[データセット](data-factory-create-datasets.md)。
4.	[AzureDataLakeStore](#azure-data-lake-dataset-type-properties) 型の出力[データセット](data-factory-create-datasets.md)。
4.	[BlobSource](#azure-blob-copy-activity-type-properties) と [AzureDataLakeStoreSink](#azure-data-lake-copy-activity-type-properties) を使用するコピー アクティビティを含む[パイプライン](data-factory-create-pipelines.md)。

このサンプルはある時系列に属するデータを 1 時間おきに Azure BLOB Storage から Azure Data Lake Store にコピーします。これらのサンプルで使用される JSON プロパティの説明はサンプルに続くセクションにあります。


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

**Azure Data Lake のリンクされたサービス:**

	{
	    "name": "AzureDataLakeStoreLinkedService",
	    "properties": {
	        "type": "AzureDataLakeStore",
	        "typeProperties": {
	            "dataLakeUri": "https://<accountname>.azuredatalakestore.net/webhdfs/v1",
				"sessionId": "<session ID>",
	            "authorization": "<authorization URL>"
	        }
	    }
	}

### Data Factory エディターを使用して Azure Data Lake のリンクされたサービスを作成するには
次の手順では、Data Factory エディターを使用して Azure Data Lake Store のリンクされたサービスを作成する手順について説明します。

1. コマンド バーの **[新しいデータ ストア]** をクリックし、**[Azure Data Lake Store]** を選択します。
2. JSON エディターで、**datalakeUri** プロパティに Data Lake の URI を入力します。
3. コマンド バーの **[承認する]** をクリックします。ポップアップ ウィンドウが表示されます。

	![Authorize button](./media/data-factory-azure-data-lake-connector/authorize-button.png)

4. 資格情報を使用してサインインし、JSON の **authorization** プロパティに今すぐ値を割り当てる必要があります。
5. (省略可能) JSON の **accountName**、**subscriptionID**、**resourceGroupName** などの省略可能なパラメーターの値を指定するか、これらのプロパティを JSON から削除します。
6. コマンド バーの **[デプロイ]** をクリックして、リンク サービスをデプロイします。

> [AZURE.IMPORTANT] **[認証]** ボタンを使用して生成した認証コードは、いずれ有効期限が切れます。**トークンの有効期限が切れたら**、**[認証]** ボタンを使用して**再認証**し、リンクされたサービスを再デプロイする必要があります。詳細については、「[Azure Data Lake Store のリンクされたサービス](#azure-data-lake-store-linked-service-properties)」のセクションを参照してください。



**Azure BLOB の入力データセット:**

データは新しい BLOB から 1 時間おきに取得されます (頻度: 時間、間隔: 1)。BLOB のフォルダー パスとファイル名は、処理中のスライスの開始時間に基づき、動的に評価されます。フォルダー パスは開始時間の年、月、日の部分を利用し、ファイル名は開始時間の時刻部分を使用します。「“external”: “true”」設定は Data Factory サービスにこのテーブルが Data Factory の外部にあり、Data Factory のアクティビティでは生成されないことを通知します。

	{
	  "name": "AzureBlobInput",
	  "properties": {
	    "type": "AzureBlob",
	    "linkedServiceName": "StorageLinkedService",
	    "typeProperties": {
	      "Path": "mycontainer/myfolder/yearno={Year}/monthno={Month}/dayno={Day}",
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
	      ]
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


**Azure Data Lake 出力データセット:**

このサンプルは Azure Data Lake Store にデータをコピーします。新しいデータが 1 時間おきに Data Lake Store にコピーされます。

	{
		"name": "AzureDataLakeStoreOutput",
	  	"properties": {
			"type": "AzureDataLakeStore",
		    "linkedServiceName": " AzureDataLakeStoreLinkedService",
		    "typeProperties": {
				"folderPath": "datalake/output/"
		    },
	    	"availability": {
	      		"frequency": "Hour",
	      		"interval": 1
	    	}
	  	}
	}



**コピー アクティビティのあるパイプライン:**

上記の入力データセットと出力データセットを使用するように構成され、1 時間おきに実行するようにスケジュールされているコピー アクティビティがパイプラインに含まれています。パイプライン JSON 定義で、**source** 型が **BlobSource** に設定され、**sink** 型が **AzureDataLakeStoreSink** に設定されています。

	{  
	    "name":"SamplePipeline",
	    "properties":
		{  
	    	"start":"2014-06-01T18:00:00",
	    	"end":"2014-06-01T19:00:00",
	    	"description":"pipeline with copy activity",
	    	"activities":
			[  
	      		{
	        		"name": "AzureBlobtoDataLake",
		        	"description": "Copy Activity",
		        	"type": "Copy",
		        	"inputs": [
		          	{
			            "name": "AzureBlobInput"
		          	}
		        	],
		        	"outputs": [
		          	{
			            "name": "AzureDataLakeStoreOutput"
		          	}
		        	],
		        	"typeProperties": {
			        	"source": {
		            		"type": "BlobSource",
			    			"treatEmptyAsNull": true,
		            		"blobColumnSeparators": ","
		          		},
		          		"sink": {
		            		"type": "AzureDataLakeStoreSink"
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

## サンプル: Azure Data Lake Store から Azure BLOB にデータをコピーする
下のサンプルで確認できる要素:

1.	[AzureDataLakeStore](#azure-data-lake-linked-service-properties) 型のリンクされたサービス。
2.	[AzureStorage](#azure-storage-linked-service-properties) 型のリンクされたサービス。
3.	[AzureDataLakeStore](#azure-data-lake-dataset-type-properties) 型の入力[データセット](data-factory-create-datasets.md)。
4.	[AzureBlob](#azure-blob-dataset-type-properties) 型の出力[データセット](data-factory-create-datasets.md)。
5.	[AzureDataLakeStoreSource](#azure-data-lake-copy-activity-type-properties) と [BlobSink](#azure-blob-copy-activity-type-properties) を使用するコピー アクティビティを含む[パイプライン](data-factory-create-pipelines.md)。

このサンプルはある時系列に属するデータを 1 時間おきに Azure Data Lake Store から Azure BLOB にコピーします。これらのサンプルで使用される JSON プロパティの説明はサンプルに続くセクションにあります。

**Azure Data Lake Store のリンクされたサービス:**

	{
	    "name": "AzureDataLakeStoreLinkedService",
	    "properties": {
	        "type": "AzureDataLakeStore",
	        "typeProperties": {
	            "dataLakeUri": "https://<accountname>.azuredatalakestore.net/webhdfs/v1",
				"sessionId": "<session ID>",
	            "authorization": "<authorization URL>"
	        }
	    }
	}

> [AZURE.NOTE] 承認 URL を取得するには、前の例の手順を参照してください。

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

**Azure Data Lake の入力データセット:**

**“external”: ”true”** を設定して **externalData** ポリシーを指定すると、テーブルが Data Factory に対して外部にあり、Data Factory のアクティビティでは生成されてないことが Azure Data Factory のサービスに通知されます。

	{
		"name": "AzureDataLakeStoreInput",
	  	"properties":
		{
	    	"type": "AzureDataLakeStore",
	    	"linkedServiceName": "AzureDataLakeStoreLinkedService",
		    "typeProperties": {
				"folderPath": "datalake/input/",
            	"fileName": "SearchLog.tsv",
            	"format": {
                	"type": "TextFormat",
	                "rowDelimiter": "\n",
    	            "columnDelimiter": "\t"
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

上記の入力データセットと出力データセットを使用するように構成され、1 時間おきに実行するようにスケジュールされているコピー アクティビティがパイプラインに含まれています。パイプライン JSON 定義で、**source** 型が **AzureDataLakeStoreSource** に設定され、**sink** 型が **BlobSink** に設定されています。


	{  
	    "name":"SamplePipeline",
	    "properties":{  
	    	"start":"2014-06-01T18:00:00",
	    	"end":"2014-06-01T19:00:00",
	    	"description":"pipeline for copy activity",
	    	"activities":[  
	      		{
	        		"name": "AzureDakeLaketoBlob",
		    	    "description": "copy activity",
		    	    "type": "Copy",
		    	    "inputs": [
		    	      {
		    	        "name": "AzureDataLakeStoreInput"
		    	      }
		    	    ],
		    	    "outputs": [
		    	      {
		    	        "name": "AzureBlobOutput"
		    	      }
		    	    ],
		    	    "typeProperties": {
		    	    	"source": {
		            		"type": "AzureDataLakeStoreSource",
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


## Azure Data Lake Store のリンクされたサービスのプロパティ

Azure Storage のリンクされたサービスを利用し、Azure Storage アカウントを Azure Data Factory にリンクできます。次の表は、Azure Storage のリンクされたサービスに固有の JSON 要素の説明をまとめたものです。

| プロパティ | 説明 | 必須 |
| :-------- | :----------- | :-------- |
| type | type プロパティを **AzureDataLakeStore** に設定する必要があります。 | あり |
| dataLakeUri | Azure Data Lake Store アカウントの情報を指定します。https://<Azure Data Lake account name>.azuredatalakestore.net/webhdfs/v1 という形式で指定します。 | あり |
| authorization | **Data Factory エディター**で **[承認する]** をクリックして資格情報を入力すると、自動生成された承認 URL がこのプロパティに割り当てられます。 | はい |
| sessionId | OAuth 承認セッションの OAuth セッション ID。各セッション ID は一意であり、1 回のみ使用できます。Data Factory エディターを使用すると自動的に生成されます。 | はい |  
| accountName | Data Lake アカウント名 | いいえ |
| subscriptionId | Azure サブスクリプション ID | いいえ (指定されていない場合は Data Factory のサブスクリプションが使用されます)。 |
| resourceGroupName | Azure リソース グループ名 | いいえ (指定されていない場合は Data Factory のリソース グループが使用されます)。 |

**[認証]** ボタンを使用して生成した認証コードは、いずれ有効期限が切れます。さまざまな種類のユーザー アカウントの有効期限については、次の表を参照してください。認証**トークンの有効期限が切れる**と、次のエラー メッセージが表示される場合があります: "資格情報の操作エラー: invalid\_grant - AADSTS70002: 資格情報の検証中にエラーが発生しました。AADSTS70008: 指定されたアクセス権の付与は期限が切れているか、失効しています。トレース ID: d18629e8-af88-43c5-88e3-d8419eb1fca1 相関 ID: fac30a0c-6be6-4e02-8d69-a776d2ffefd7 タイムスタンプ: 2015-12-15 21-09-31Z"


| ユーザー タイプ | 有効期限 |
| :-------- | :----------- | 
| Azure Active Directory で管理されていないユーザー アカウント (@hotmail.com、@live.com など) | 12 時間 |
| Azure Active Directory (AAD) で管理されているユーザー アカウント | スライスの最後の実行から 14 日後。<br/><br/>OAuth ベースのリンクされたサービスに基づいて、少なくとも 14 日間に 1 回スライスが実行する場合、90 日です。 |

このエラーを回避または解決するには、**トークンの有効期限が切れた**ときに、**[認証]** ボタンを使用して再認証し、リンクされたサービスを再デプロイする必要があります。次のセクションのコードを使用して、**sessionId** と **authorization** プロパティの値をプログラムで生成することもできます。

### プログラムを使用して sessionId と authorization の値を生成するには 

    if (linkedService.Properties.TypeProperties is AzureDataLakeStoreLinkedService ||
        linkedService.Properties.TypeProperties is AzureDataLakeAnalyticsLinkedService)
    {
        AuthorizationSessionGetResponse authorizationSession = this.Client.OAuth.Get(this.ResourceGroupName, this.DataFactoryName, linkedService.Properties.Type);

        WindowsFormsWebAuthenticationDialog authenticationDialog = new WindowsFormsWebAuthenticationDialog(null);
        string authorization = authenticationDialog.AuthenticateAAD(authorizationSession.AuthorizationSession.Endpoint, new Uri("urn:ietf:wg:oauth:2.0:oob"));

        AzureDataLakeStoreLinkedService azureDataLakeStoreProperties = linkedService.Properties.TypeProperties as AzureDataLakeStoreLinkedService;
        if (azureDataLakeStoreProperties != null)
        {
            azureDataLakeStoreProperties.SessionId = authorizationSession.AuthorizationSession.SessionId;
            azureDataLakeStoreProperties.Authorization = authorization;
        }

        AzureDataLakeAnalyticsLinkedService azureDataLakeAnalyticsProperties = linkedService.Properties.TypeProperties as AzureDataLakeAnalyticsLinkedService;
        if (azureDataLakeAnalyticsProperties != null)
        {
            azureDataLakeAnalyticsProperties.SessionId = authorizationSession.AuthorizationSession.SessionId;
            azureDataLakeAnalyticsProperties.Authorization = authorization;
        }
    }

コードで使用する Data Factory クラスに関する詳細は、「[AzureDataLakeStoreLinkedService クラス](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuredatalakestorelinkedservice.aspx)」、「[AzureDataLakeAnalyticsLinkedService クラス](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuredatalakeanalyticslinkedservice.aspx)」、「[AuthorizationSessionGetResponse クラス](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.authorizationsessiongetresponse.aspx)」のトピックを参照してください。WindowsFormsWebAuthenticationDialog クラスの Microsoft.IdentityModel.Clients.ActiveDirectory.WindowsForms.dll に参照を追加する必要があります。
 

## Azure Data Lake データセットの type プロパティ

データセットの定義に利用できる JSON のセクションとプロパティの完全一覧については、「[データセットの作成](data-factory-create-datasets.md)」という記事を参照してください。データセット JSON の構造、可用性、ポリシーなどのセクションはすべてのデータセット型 (Azure SQL、Azure BLOB、Azure テーブルなど) で同じです。

**typeProperties** セクションはデータセット型ごとに異なり、データ ストアのデータの場所や書式などに関する情報を提供します。**AzureDataLakeStore** 型のデータセットの typeProperties セクションには、次のプロパティがあります。

| プロパティ | 説明 | 必須 |
| :-------- | :----------- | :-------- |
| folderPath | Azure Data Lake Store のコンテナーとフォルダーのパス。 | はい |
| fileName | Azure Data Lake Store 内のファイルの名前。fileName は省略可能です。この名前は大文字と小文字が区別されます。<br/><br/>fileName を指定した場合、アクティビティ (コピーを含む) は特定のファイルで機能します。<br/><br/>fileName が指定されていない場合、コピーには入力データセットの folderPath のすべてのファイルが含まれます。<br/><br/>出力データセットに fileName が指定されていない場合、生成されるファイルの名前は、Data.<Guid>.txt (例: Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt) の形式になります。 | いいえ |
| partitionedBy | partitionedBy は任意のプロパティです。これを使用し、時系列データに動的な folderPath と fileName を指定できます。たとえば、1 時間ごとのデータに対して folderPath をパラメーター化できます。詳細と例については、「partitionedBy プロパティの活用」セクションを参照してください。 | いいえ |
| BlobSink の format | **TextFormat** と **AvroFormat** の 2 種類の形式がサポートされています。形式の下にある type プロパティをいずれかの値に設定する必要があります。形式が TextFormat のとき、形式に追加で任意のプロパティを指定できます。詳細については、下にある「[TextFormat の指定](#specifying-textformat)」セクションを参照してください。 | いいえ |
| compression | データの圧縮の種類とレベルを指定します。サポートされる種類: **GZip**、**Deflate**、および **BZip2**。サポートされるレベル: **Optimal** および **Fastest**。現時点で、**AvroFormat** のデータの圧縮設定はサポートされていないことに注意してください。詳細については、「[圧縮のサポート](#compression-support)」セクションを参照してください。 | いいえ |

### partitionedBy プロパティの活用
前述のように、時系列データの動的な folderPath と fileName を指定するとき、**partitionedBy** セクション、Data Factory マクロ、特定のデータ スライスの開始時刻と終了時刻を示すシステム変数の SliceStart と SliceEnd を使用できます。

時系列データセット、スケジュール作成、スライスに関する詳細については、記事「[データセットの作成](data-factory-create-datasets.md)」と記事「[スケジュールと実行](data-factory-scheduling-and-execution.md)」を参照してください。

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
| columnDelimiter | ファイルの列の区切り記号として使用される文字です。この時点では 1 文字だけが許可されています。このタグは任意です。既定値はコンマです (,)。 | いいえ |
| rowDelimiter | ファイルの行の区切り記号として使用される文字です。この時点では 1 文字だけが許可されています。このタグは任意です。既定値は [“\\r\\n”, “\\r”,” \\n”] のいずれかになります。 | いいえ |
| escapeChar | コンテンツに表示される列区切り記号のエスケープに使用される特殊文字です。このタグは任意です。既定値はありません。このプロパティに指定する文字は 1 つだけです。<br/><br/>たとえば、列の区切り文字としてコンマ (,) を使用しているとき、テキストにもコンマ文字が必要な場合 (例: “Hello, world”)、エスケープ文字として “$” を定義し、ソースで文字列 “Hello$, world” を使用できます。<br/><br/>1 つのテーブルに escapeChar と quoteChar の両方を指定できないことに注意してください。 | いいえ | 
| quoteChar | この特殊文字は文字列値を引用符で囲むために使用されます。引用符文字内の列区切り文字と行区切り文字は文字列値の一部として処理されます。このタグは任意です。既定値はありません。このプロパティに指定する文字は 1 つだけです。<br/><br/>たとえば、列の区切り文字としてコンマ (,) を使用しているときにテキストにもコンマ文字が必要な場合 (例: <Hello  world>)、引用符文字として「"」を定義し、ソースで文字列「"Hello, world"」を使用できます。このプロパティは入力テーブルと出力テーブルの両方に適用されます。<br/><br/>1 つのテーブルに escapeChar と quoteChar の両方を指定できないことに注意してください。 | いいえ |
| nullValue | BLOB ファイル コンテンツで null 値を表すために使用する文字です。このタグは任意です。既定値は “\\N” です。<br/><br/>たとえば、上記のサンプルに基づくと、BLOB の “NaN” は SQL Server などにコピーされるときに null 値として変換されます。 | いいえ |
| encodingName | エンコード名の指定。有効なエンコード名の一覧については、[Encoding.EncodingName プロパティに関する記事](https://msdn.microsoft.com/library/system.text.encoding.aspx)を参照してください。例: windows-1250 または shift\_jis。既定値は UTF-8 です。 | いいえ | 

#### サンプル
次の例は、TextFormat の format プロパティの一部を示します。

	"typeProperties":
	{
	    "folderPath": "mycontainer/myfolder",
	    "fileName": "myfilename"
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


### 圧縮のサポート  
大量のデータセットを処理すると、I/O およびネットワークにボトルネックが生じる可能性があります。そのため、データを圧縮して保存すると、ネットワークでのデータ転送速度が上昇してディスク領域を節約できるだけでなく、ビッグ データの処理性能を大幅に高めることができます。現時点では、圧縮は Azure BLOB やオンプレミスのファイル システムなど、ファイルベースのデータ ストアでサポートされています。

データセットの圧縮を指定するには、次の例のように、データセットの JSON で **compression** プロパティを使用します。

	{  
		"name": "AzureDatalakeStoreDataSet",  
	  	"properties": {  
	    	"availability": {  
	    		"frequency": "Day",  
	    	  	"interval": 1  
	    	},  
	    	"type": "AzureDatalakeStore",  
	    	"linkedServiceName": "DataLakeStoreLinkedService",  
	    	"typeProperties": {  
	    		"fileName": "pagecounts.csv.gz",  
	    	  	"folderPath": "compression/file/",  
	    	  	"compression": {  
	    	    	"type": "GZip",  
	    	    	"level": "Optimal"  
	    	  	}  
    		}  
	  	}  
	}  
 
**compression** セクションには次の 2 つのプロパティがあります。
  
- **type:** 圧縮コーデックです。**GZIP**、**Deflate**、または **BZIP2** を指定できます。  
- **level:** 圧縮率です。**Optimal** または **Fastest** を指定できます。 
	- **Fastest:** 圧縮操作は可能な限り短時間で完了しますが、生成ファイルが最適に圧縮されない場合があります。 
	- **Optimal**: 圧縮操作では最適に圧縮されますが、操作完了までの時間が増加する場合があります。 
	
	詳細については、[圧縮レベルに関するトピック](https://msdn.microsoft.com/library/system.io.compression.compressionlevel.aspx)を参照してください。

前述のサンプル データセットをコピー アクティビティの出力として使用し、コピー アクティビティで出力データを GZIP コーデックによって最適な圧縮率で圧縮してから、Azure Data Lake Store の pagecounts.csv.gz という名前のファイルに圧縮データを書き込む場合を考えます。

入力データセットの JSON で compression プロパティを指定すると、パイプラインでソースから圧縮データを読み取ることができ、出力データセットの JSON で compression プロパティを指定すると、コピー アクティビティにより出力先に圧縮データを書き込むことができます。いくつかのサンプル シナリオを次に示します。

- Azure Data Lake Store から GZIP 圧縮データを読み取り、展開して、生成されたデータを Azure SQL Database に書き込みます。この場合、Azure Data Lake Store 入力データセットを、compression JSON プロパティを使用して定義します。 
- オンプレミスのファイル システムのプレーンテキスト ファイルからデータを読み取り、GZip 形式で圧縮して、圧縮データを Azure Data Lake Store に書き込みます。この場合、Azure Data Lake 出力データセットを、compression JSON プロパティを使用して定義します。  
- Azure Data Lake Store から GZIP 圧縮データを読み取って展開し、BZIP2 で圧縮して、生成されたデータを Azure Data Lake Store に書き込みます。この場合、Azure Data Lake Store 入力データセットは圧縮タイプを GZIP に設定して定義し、Azure Data Lake Store 出力データセットは圧縮タイプを BZIP2 に設定して定義します。   


## Azure Data Lake のコピー アクティビティの type プロパティ  
アクティビティの定義に利用できるセクションとプロパティの完全な一覧については、「[パイプラインの作成](data-factory-create-pipelines.md)」という記事を参照してください。名前、説明、入力テーブル、出力テーブル、さまざまなポリシーなどのプロパティがあらゆる種類のアクティビティで利用できます。

一方で、アクティビティの typeProperties セクションで利用できるプロパティはアクティビティの種類により異なり、コピー アクティビティの場合、sources と sinks の種類によって異なります。

**AzureDataLakeStoreSource** の **typeProperties** セクションでは、次のプロパティがサポートされます。

| プロパティ | 説明 | 使用できる値 | 必須 |
| -------- | ----------- | -------------- | -------- |
| recursive | データをサブ フォルダーから再帰的に読み取るか、指定したフォルダーからのみ読み取るかを指定します。 | True (既定値)、False | いいえ |



**AzureDataLakeStoreSink** の **typeProperties** セクションでは、次のプロパティがサポートされます。

| プロパティ | 説明 | 使用できる値 | 必須 |
| -------- | ----------- | -------------- | -------- |
| copyBehavior | コピー動作を指定します。 | **PreserveHierarchy:** ターゲット フォルダー内でファイル階層を保持します。つまり、ソース フォルダーへのソース ファイルの相対パスはターゲット フォルダーへのターゲット ファイルの相対パスと同じになります。<br/><br/>**FlattenHierarchy:** ソース フォルダーのすべてのファイルがターゲット フォルダーの最上位レベルに配置されます。ターゲット ファイルの名前は自動的に生成されます。<br/><br/>**MergeFiles:** ソース フォルダーのすべてのファイルを 1 つのファイルにマージします。ファイル/Blob の名前を指定した場合、マージされたファイル名は指定した名前になります。それ以外は自動生成されたファイル名になります。 | いいえ |


[AZURE.INCLUDE [data-factory-structure-for-rectangualr-datasets](../../includes/data-factory-structure-for-rectangualr-datasets.md)]

[AZURE.INCLUDE [data-factory-type-conversion-sample](../../includes/data-factory-type-conversion-sample.md)]

[AZURE.INCLUDE [data-factory-column-mapping](../../includes/data-factory-column-mapping.md)]

<!---HONumber=AcomDC_0309_2016-->