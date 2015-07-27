<properties 
	pageTitle="Azure Data Factory でコピー アクティビティを使用する例" 
	description="Azure Data Factory でコピー アクティビティを使用する例を示します。" 
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
	ms.date="07/07/2015" 
	ms.author="spelluru"/>

# Azure Data Factory でコピー アクティビティを使用する例
パイプラインで**コピー アクティビティ**を使用して、ソースからシンク (ターゲット) にデータを一括でコピーできます。このトピックでは、Data Factory パイプラインでコピー アクティビティを使用する例をいくつか紹介します。コピー アクティビティと、コピー アクティビティがサポートする主要なシナリオの詳細については、「[Azure Data Factory を使用してデータをコピーする][adf-copyactivity]」を参照してください。

## オンプレミスの SQL Server Database から Azure BLOB へのデータのコピー
この例では、入力テーブルと出力テーブルが定義されます。また、これらのテーブルが、内部設置型の SQL Server データベースから Azure BLOB にデータをコピーするパイプライン内のコピー アクティビティで使用されます。

### 前提条件
この例は、次の Azure Data Factory アーティファクトがあることを前提としています。

* **ADF** という名前のリソース グループ
* **CopyFactory** という名前の Azure Data Factory
* オンラインになっており、かつ **mygateway** という名前のデータ管理ゲートウェイ  

### ソースのオンプレミスの SQL Server Database のリンクされたサービスの作成
この手順では、オンプレミスの SQL Server Database を示す **MyOnPremisesSQLDB** という名前のリンクされたサービスを作成します。

	{
	    "name": "MyOnPremisesSQLDB",
	    "properties":
	    {
	        "type": "OnPremisesSqlLinkedService",
	        "connectionString": "Data Source=<servername>;Initial Catalog=<database>;Integrated Security=False;User ID=<username>;Password=<password>;",
	        "gatewayName": "mygateway"
	    }
	}

以下の点に注意してください。

- **type** が **OnPremisesSqlLinkedService** に設定されています。
- **connectionString** が SQL Server Database の接続文字列に設定されています。 
- **gatewayName** が、オンプレミスのコンピューターにインストールして Azure Data Factory サービス ポータルに登録したデータ管理ゲートウェイの名前に設定されています。 

オンプレミスの SQL のリンクされたサービスを定義するための JSON 要素の詳細については、[オンプレミスの SQL のリンクされたサービス](https://msdn.microsoft.com/library/dn893523.aspx)に関するページをご覧ください。
 
### コピー先 Azure BLOB のリンクされたサービスの作成
この手順では、Azure BLOB ストレージを示す **MyAzureStorage** という名前のリンクされたサービスを作成します。

	{
	    "name": "MyAzureStorage",
	    "properties":
	    {
	        "type": "AzureStorageLinkedService",
	        "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>" "
	    }
	}

以下の点に注意してください。

- **type** が **AzureStorageLinkedService** に設定されています。
- **connectionString** - Azure ストレージのアカウント名とアカウント キーを指定します。

Azure Storage のリンクされたサービスを定義するための JSON 要素の詳細については、[Azure Storage のリンクされたサービス](https://msdn.microsoft.com/library/dn893522.aspx)に関するページをご覧ください。

### 入力テーブルの JSON
次の JSON スクリプトは、入力テーブルを定義します。この入力テーブルは、リンクされたサービス **MyOnPremisesSQLDB** で定義されたオンプレミスの SQL Server データベースにある SQL テーブル **MyTable** を参照します。**name** は Azure Data Factory テーブルの名前で、**tableName** は SQL Server データベース内の SQL テーブルの名前です。

         
	{
		"name": "MyOnPremTable",
    	"properties":
   		{
			"location":
    		{
    			"type": "OnPremisesSqlServerTableLocation",
    			"tableName": "MyTable",
    			"linkedServiceName": "MyOnPremisesSQLDB"
    		},
    		"availability":
   			{
    			"frequency": "Hour",
    			"interval": 1
   			}
 		}
	}

以下の点に注意してください。

- **type** が **OnPremisesSqlServerTableLocation** に設定されています。
- **tableName** が、ソース データを格納する **MyTable** に設定されています。 
- **linkedServiceName** が **MyOnPremisesSQLDB** に設定されています。これは、オンプレミスの SQL Database に対して作成したリンクされたサービスです。

SQL Server テーブルを参照する Data Factory テーブルを定義するための JSON 要素の詳細については、[オンプレミスの SQL の場所のプロパティ](https://msdn.microsoft.com/library/dn894089.aspx#OnPremSQL)に関するセクションをご覧ください。

### 出力テーブルの JSON
次の JSON スクリプトは、出力テーブル **MyAzureBlob** を定義します。これは、**MyContainer** という BLOB コンテナー内の **MySubFolder** という BLOB フォルダー内にある **MyBlob** という Azure BLOB を参照します。
         
	{
   		"name": "MyAzureBlob",
	    "properties":
    	{
    		"location":
    		{
        		"type": "AzureBlobLocation",
        		"folderPath": "MyContainer/MySubFolder",
        		"fileName": "MyBlob",
        		"linkedServiceName": "MyAzureStorage",
        		"format":
        		{
            		"type": "TextFormat",
            		"columnDelimiter": ",",
            		"rowDelimiter": ";",
             		"EscapeChar": "$",
             		"NullValue": "NaN"
        		}
    		},
        	"availability":
      		{
       			"frequency": "Hour",
       			"interval": 1
      		}
   		}
	}

以下の点に注意してください。
 
- **type** が **AzureBlobLocation** に設定されています。
- **folderPath** が **MyContainer/MySubFolder** に設定されています。これは、コピーされたデータを保持する BLOB です。 
- **fileName** が、出力データを保持する BLOB である **MyBlob** に設定されています。
- **linkedServiceName** が **MyAzureStorge** に設定されています。これは、Azure ストレージに対して作成したリンクされたサービスです。    

Azure BLOB を参照する Data Factory テーブルを定義するための JSON 要素の詳細については、[Azure BLOB の場所のプロパティ](https://msdn.microsoft.com/library/dn894089.aspx#AzureBlob)に関するセクションをご覧ください。

### パイプライン (コピー アクティビティ) の JSON
この例では、次のプロパティを使用して **CopyActivityPipeline** というパイプラインが定義されます。

- **type** プロパティは、**CopyActivity** に設定されます。
- **MyOnPremTable** が入力として指定されます (**inputs** タグ)。
- **MyAzureBlob** が出力として指定されます (**outputs** タグ)。
- **Transformation** セクションには、**source** と **sink** という 2 つのサブ セクションがあります。ソースの種類は **SqlSource** に設定され、シンクの種類は **BlobSink** に設定されます。**sqlReaderQuery** は、変換 (射影) がソース上で実行されるように定義します。すべてのプロパティの詳細については、「[JSON Scripting Reference (JSON スクリプト リファレンス)](https://msdn.microsoft.com/library/dn835050.aspx)」を参照してください。

         
		{
		    "name": "CopyActivityPipeline",
    		"properties":
    		{
				"description" : "This is a sample pipeline to copy data from SQL Server to Azure Blob",
        		"activities":
        		[
      				{
						"name": "CopyActivity",
						"description": "description", 
						"type": "CopyActivity",
						"inputs":  [ { "name": "MyOnPremTable"  } ],
						"outputs":  [ { "name": "MyAzureBlob" } ],
						"transformation":
	    				{
							"source":
							{
								"type": "SqlSource",
                    			"sqlReaderQuery": "select * from MyTable"
							},
							"sink":
							{
                        		"type": "BlobSink"
							}
	    				}
      				}
        		]
    		}
		}

Data Factory パイプラインを定義するための JSON 要素の詳細については、[パイプライン JSON リファレンス](https://msdn.microsoft.com/library/dn834988.aspx)に関するページをご覧ください。SqlSource (たとえば、例の中の **sqlReaderQuery**) と BlobSink のプロパティについては、「[サポートされているソースとシンク](https://msdn.microsoft.com/library/dn894007.aspx)」をご覧ください。


## オンプレミスのファイル システムから Azure BLOB へのデータのコピー
コピー アクティビティを使用して、オンプレミスのファイル システム (Windows または Linux のネットワーク共有または Windows ローカル ホスト) から Azure BLOB にファイルをコピーできます。Samba が構成された Windows または Linux をホストとして使用できます。ホストに接続できる Windows コンピューターには Data Management Gateway をインストールする必要があります。

### 前提条件
この例では、次の条件を想定しています。

- **ホスト** - ファイル システムをホストするサーバーの名前: **\contoso**。
- **フォルダー** - 入力ファイルが含まれているフォルダーの名前: **marketingcampaign\regionaldata\{slice}。ここで、ファイルは、{slice} という名前のフォルダー内で分割されます (例: 2014121112 (2014 年 12 月 11 日 12 時))。

### オンプレミスのファイル システムのリンクされたサービスの作成
次のサンプル JSON を使用すると、**OnPremisesFileSystemLinkedService** 型の **FolderDataStore** という名前のリンクされたサービスを作成できます。

	{
	    "name": "FolderDataStore",
	    "properties": {
	        "type": "OnPremisesFileSystemLinkedService",
	        "host": "\contoso",
	        "userId": "username",
	        "password": "password",
	        "gatewayName": "ContosoGateway"
	    }
	}

> [AZURE.NOTE]JSON ファイル内ではホストとフォルダーの名前にエスケープ文字 () を使用してください。たとえば、**\Contoso** の場合は、**\Contoso** のように指定します。

オンプレミスのファイル システムのリンクされたサービスを定義するための JSON 要素の詳細については、[オンプレミスのファイル システムのリンクされたサービス](https://msdn.microsoft.com/library/dn930836.aspx)に関するページをご覧ください。

### コピー先 Azure BLOB のリンクされたサービスの作成
次のサンプル JSON を使用すると、**AzureStorageLinkedSerivce** 型の **MyAzureStorage** という名前のリンクされたサービスを作成できます。

	{
	    "name": "MyAzureStorage",
	    "properties":
	    {
	        "type": "AzureStorageLinkedService",
	        "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>" "
	    }
	}

Azure Storage のリンクされたサービスを定義するための JSON 要素の詳細については、[Azure Storage のリンクされたサービス](https://msdn.microsoft.com/library/dn893522.aspx)に関するページをご覧ください。

### 入力テーブルの作成
次の JSON スクリプトは、前の手順で作成したオンプレミスのファイル システムのリンクされたサービスを参照する入力テーブルを定義します。

	{
	    "name": "OnPremFileSource",
	    "properties": {
	        "location": {
	            "type": "OnPremisesFileSystemLocation",
	            "folderPath": "marketingcampaign\regionaldata\{Slice}",
	            "partitionedBy": [
	                { "name": "Slice", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyyMMddHH" } }
	            ],
	            "linkedServiceName": "FolderDataStore"
	        },
	        "availability": {
	            "waitOnExternal": { },
	            "frequency": "Hour",
	            "interval": 24
	        }
	    }
	}

オンプレミスのファイル システムを参照する Data Factory テーブルを定義するための JSON 要素の詳細については、[オンプレミスのファイル システムの場所のプロパティ](https://msdn.microsoft.com/library/dn894089.aspx#OnPremFileSystem)に関するセクションをご覧ください。

### 出力テーブルの作成
次の JSON スクリプトは、出力テーブル **AzureBlobDest** を定義します。これは、**MyContainer** という BLOB コンテナー内の **MySubFolder** という BLOB フォルダー内にある **MyBlob** という Azure BLOB を参照します。
         
	{
   		"name": "AzureBlobDest",
	    "properties":
    	{
    		"location":
    		{
        		"type": "AzureBlobLocation",
        		"folderPath": "MyContainer/MySubFolder",
        		"fileName": "MyBlob",
        		"linkedServiceName": "MyAzureStorage",
        		"format":
        		{
            		"type": "TextFormat",
            		"columnDelimiter": ",",
            		"rowDelimiter": ";",
             		"EscapeChar": "$",
             		"NullValue": "NaN"
        		}
    		},
        	"availability":
      		{
       			"frequency": "Hour",
       			"interval": 1
      		}
   		}
	}

Azure BLOB を参照する Data Factory テーブルを定義するための JSON 要素の詳細については、[Azure BLOB の場所のプロパティ](https://msdn.microsoft.com/library/dn894089.aspx#AzureBlob)に関するセクションをご覧ください。

### パイプラインの作成
次のパイプライン JSON は、オンプレミスのファイル システムからコピー先の Azure BLOB にデータをコピーするコピー アクティビティを含むパイプラインを定義します。
 
	{
	    "name": "CopyFileToBlobPipeline",
	    "properties": {
	        "activities": [
	            {
	                "name": "Ingress",
	                "type": "CopyActivity",
	                "inputs": [ { "name": "OnPremFileSource" } ],
	                "outputs": [ { "name": "AzureBlobDest" } ],
	                "transformation": {
	                    "source": {
	                        "type": "FileSystemSource"
	                    },
	                    "sink": {
	                        "type": "BlobSink"
	                    }
	                },
	                "policy": {
	                    "concurrency": 4,
	                    "timeout": "00:05:00"
	                }
	            }
	        ]
	    }
	}

この例のパイプラインは、解析や変換を実行することなく、コンテンツをバイナリとしてコピーします。**concurrency** を使って、ファイルのスライスを並列にコピーできます。この機能は、過去に既に発生したスライスを移動する場合に便利です。

> [AZURE.NOTE]UNC パスを使用する場合、異なるユーザー アカウントを複数使って同一のホストに同時にコピー アクティビティを実行すると、"同じユーザーが複数のユーザー名を使ってサーバーまたは共有リソースに複数の接続を確立することはできません" などのエラーが発生することがあります。これは、オペレーティング システムのセキュリティ上の制約によるものです。異なるゲートウェイを使用してコピー アクティビティをスケジュール設定するか、またはホスト内にゲートウェイをインストールし、UNC パスの代わりに "localhost" または "local" を使用してください。

Data Factory パイプラインを定義するための JSON 要素の詳細については、[パイプライン JSON リファレンス](https://msdn.microsoft.com/library/dn834988.aspx)に関するページをご覧ください。FileSystemSource と BlobSink のプロパティについては、「[サポートされているソースとシンク](https://msdn.microsoft.com/library/dn894007.aspx)」をご覧ください。

### ファイル システム テーブルを使用する追加のシナリオ

#### 特定のフォルダー内のすべてのファイルをコピーする
サンプル JSON で **folderPath** のみが指定されていることに注目してください。

	{
	    "name": "OnPremFileSource",
	    "properties": {
	        "location": {
	            "type": "OnPremisesFileSystemLocation",
	            "folderPath": "marketingcampaign\regionaldata\na",
	            "linkedServiceName": "FolderDataStore"
	        },
	        ...
	    }
	}
 
#### 特定のフォルダー内のすべての CSV ファイルをコピーする
**fileFilter** が ***.csv** に設定されている点に注目してください。

	{
	    "name": "OnPremFileSource",
	    "properties": {
	        "location": {
	            "type": "OnPremisesFileSystemLocation",
	            "folderPath": "marketingcampaign\regionaldata\na",
	            "fileFilter": "*.csv",
	            "linkedServiceName": "FolderDataStore"
	        },
	        ...
	    }
	}

#### 特定のファイルをコピーする
**fileFiter** が特定のファイル (**201501.csv**) に設定されている点に注目してください。

	{
	    "name": "OnPremFileSource",
	    "properties": {
	        "location": {
	            "type": "OnPremisesFileSystemLocation",
	            "folderPath": "marketingcampaign\regionaldata\na",
	            "fileFilter": "201501.csv",
	            "linkedServiceName": "FolderDataStore"
	        },
	        ...
	    }
	}

## オンプレミスの Oracle データベースから Azure BLOB へのデータのコピー
コピー アクティビティを使用して、オンプレミスの Oracle データベースから Azure BLOB にファイルをコピーできます。

### オンプレミスの Oracle データベースのリンクされたサービスの作成
次の JSON を使用すると、オンプレミスの Oracle データベースを示すリンクされたサービスを作成できます。**type** が **OnPremisesOracleLinkedService** に設定されている点に注目してください。

	{
	    "name": "OnPremOracleSource",
	    "properties": {
	        "type": "OnPremisesOracleLinkedService",
	        "ConnectionString": "data source=ds;User Id=uid;Password=pwd;",
	        "gatewayName": "SomeGateway"
	    }
	}

オンプレミスの Oracle のリンクされたサービスを定義するための JSON 要素の詳細については、[オンプレミスの Oracle のリンクされたサービス](https://msdn.microsoft.com/library/dn948537.aspx)に関するページをご覧ください。

### コピー先 Azure BLOB のリンクされたサービスの作成
次のサンプル JSON を使用すると、**AzureStorageLinkedSerivce** 型の **MyAzureStorage** という名前のリンクされたサービスを作成できます。

	{
	    "name": "AzureBlobDest",
	    "properties":
	    {
	        "type": "AzureStorageLinkedService",
	        "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>" "
	    }
	}

Azure Storage のリンクされたサービスを定義するための JSON 要素の詳細については、[Azure Storage のリンクされたサービス](https://msdn.microsoft.com/library/dn893522.aspx)に関するページをご覧ください。

### 入力テーブルの作成
次のサンプル JSON を使用すると、オンプレミスの Oracle データベースのテーブルを参照する Azure Data Factory テーブルを作成できます。**location type** が **OnPremisesOracleTableLocation** に設定されている点に注目してください。

	{
	    "name": "TableOracle",
	    "properties": {
	        "location": {
	            "type": "OnPremisesOracleTableLocation",
	            "tableName": "LOG",
	            "linkedServiceName": "OnPremOracleSource"
	        },
	        "availability": {
	            "frequency": "Day",
	            "interval": "1",
	            "waitOnExternal": {}
	        },
	        "policy": {}
	    }
	} 

オンプレミスの Oracle データベースのテーブルを参照する Azure Data Factory テーブルを定義するための JSON 要素の詳細については、[オンプレミスの Oracle の場所のプロパティ](https://msdn.microsoft.com/library/dn894089.aspx#Oracle)に関するセクションをご覧ください。

### 出力テーブルの作成
次の JSON スクリプトは、出力テーブル **MyAzureBlob** を定義します。これは、**MyContainer** という BLOB コンテナー内の **MySubFolder** という BLOB フォルダー内にある **MyBlob** という Azure BLOB を参照します。
         
	{
   		"name": "MyAzureBlob",
	    "properties":
    	{
    		"location":
    		{
        		"type": "AzureBlobLocation",
        		"folderPath": "MyContainer/MySubFolder",
        		"fileName": "MyBlob",
        		"linkedServiceName": "AzureBlobDest",
        		"format":
        		{
            		"type": "TextFormat",
            		"columnDelimiter": ",",
            		"rowDelimiter": ";",
             		"EscapeChar": "$",
             		"NullValue": "NaN"
        		}
    		},
        	"availability":
      		{
       			"frequency": "Hour",
       			"interval": 1
      		}
   		}
	}

Azure BLOB を参照する Data Factory テーブルを定義するための JSON 要素の詳細については、[Azure BLOB の場所のプロパティ](https://msdn.microsoft.com/library/dn894089.aspx#AzureBlob)に関するセクションをご覧ください。

### パイプラインの作成
次のサンプル パイプラインには、Oracle データベース テーブルから Azure Storage BLOB にデータをコピーするコピー アクティビティが含まれています。

	{
	    "name": "PipelineCopyOracleToBlob",
	    "properties": {
	        "activities": [
	            {
	                "name": "CopyActivity",
	                "description": "copy slices of oracle records to azure blob",
	                "type": "CopyActivity",
	                "inputs": [ { "name": "TableOracle" } ],
	                "outputs": [ { "name": "TableAzureBlob" } ],
	                "transformation": {
	                    "source": {
	                        "type": "OracleSource",
	                        "oracleReaderQuery": "$$Text.Format('select * from LOG where "Timestamp" >= to_date('{0:yyyy-MM-dd}', 'YYYY-MM-DD') AND "Timestamp" < to_date('{1:yyyy-MM-dd}', 'YYYY-MM-DD')', SliceStart, SliceEnd)"
	                    },
	                    "sink": {
	                        "type": "BlobSink"
	                    }
	                },
	                "policy": {
	                    "concurrency": 3,
	                    "timeout": "00:05:00"
	                }
	            }
	        ],
	        "start": "2015-03-01T00:00:00Z",
	        "end": "2015-03-15T00:00:00Z",
	        "isPaused": false
	    }
	}

Data Factory パイプラインを定義するための JSON 要素の詳細については、[パイプライン JSON リファレンス](https://msdn.microsoft.com/library/dn834988.aspx)に関するページをご覧ください。OracleSource と BlobSink のプロパティについては、「[サポートされているソースとシンク](https://msdn.microsoft.com/library/dn894007.aspx)」をご覧ください。

## 関連項目

- [Azure Data Factory を使用してデータをコピーする][adf-copyactivity]
- [コピー アクティビティ - JSON スクリプト リファレンス](https://msdn.microsoft.com/library/dn835035.aspx)
- [ビデオ: Azure Data Factory コピーアクティビティの概要][copy-activity-video]


[adf-copyactivity]: data-factory-copy-activity.md
[copy-activity-video]: http://azure.microsoft.com/documentation/videos/introducing-azure-data-factory-copy-activity/

<!----HONumber=July15_HO3-->