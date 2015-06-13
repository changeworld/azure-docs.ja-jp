<properties 
	pageTitle="Azure のデータのファクトリでのコピー活動を使用するための例" 
	description="例を使用して、Azure のデータのファクトリでのコピー活動を示します。" 
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
	ms.date="04/14/2015" 
	ms.author="spelluru"/>

# Azure のデータのファクトリでのコピー活動を使用するための例
パイプラインで**コピー アクティビティ**を使用して、ソースからシンク (ターゲット) にデータを一括でコピーできます。このトピックは、出荷時のデータ パイプラインで、コピーのアクティビティの使用の例をいくつかを示します。コピー アクティビティと、コピー アクティビティがサポートする主要なシナリオの詳細については、「[Azure Data Factory を使用してデータをコピーする][adf-copyactivity]」を参照してください。

## データを内部設置型 SQL Server データベースを Azure の blob にコピーします。
この例では、入力テーブルと出力テーブルが定義されます。また、これらのテーブルが、内部設置型の SQL Server データベースから Azure BLOB にデータをコピーするパイプライン内のコピー アクティビティで使用されます。

### 前提条件
この例は、次の Azure データ工場出荷時のアイテムがあることを前提としています。

* **ADF** という名前のリソース グループ
* **CopyFactory** という名前の Azure Data Factory
* という名前のデータ管理ゲートウェイ **mygateway** が作成されはオンラインです。  

### ソースの内部設置型 SQL Server データベースのリンクされているサービスを作成します。
このステップでという名前のリンクされているサービスを作成する **MyOnPremisesSQLDB** 、内部設置型 SQL Server データベースを指します。

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

- **型** に設定されている **OnPremisesSqlLinkedService**です。
- **connectionString** 、SQL Server データベースの接続文字列に設定されています。 
- **gatewayName** 、内部設置型コンピューターにインストールされているし、Azure のデータのファクトリのサービスのポータルに登録されているが、Data Management Gateway の名前に設定されています。 

参照してください [内部設置型 SQL リンク サービス](https://msdn.microsoft.com/library/dn893523.aspx) 、内部設置型 SQL を定義するための要素の詳細については JSON サービスにリンクされています。
 
### 変換先の Azure blob の場合、リンクされているサービスを作成します。
このステップでという名前のリンクされているサービスを作成する **MyAzureStorage** 、Azure blob ストレージを参照します。

	{
	    "name": "MyAzureStorage",
	    "properties":
	    {
	        "type": "AzureStorageLinkedService",
	        "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>" "
	    }
	}

以下の点に注意してください。

- **型** に設定されている **AzureStorageLinkedService**です。
- **connectionString** - アカウント名を指定し、アカウント、Azure のストレージのキー。

参照してください [Azure ストレージ リンク サービス](https://msdn.microsoft.com/library/dn893522.aspx) リンク サービスの詳細については、Azure のストレージを定義する JSON 要素についてです。

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

- **型** に設定されている **OnPremisesSqlServerTableLocation**です。
- **tableName** に設定されている **MyTable**, 、ソース データが格納されます。 
- **linkedServiceName** に設定されている **MyOnPremisesSQLDB**, 、内部設置型の SQL データベース用に作成したサービスをリンクします。

参照してください [場所のプロパティを内部設置型 SQL](https://msdn.microsoft.com/library/dn894089.aspx#OnPremSQL) の詳細については、SQL Server テーブルを参照する、ファクトリのデータ テーブルを定義する JSON 要素。

### 出力テーブルの JSON
次の JSON スクリプトを出力テーブルを定義: **MyAzureBlob**, を Azure の blob を参照する: **MyBlob** blob フォルダー内: **MySubFolder** blob コンテナーに: **MyContainer**です。
         
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
 
- **型** に設定されている **AzureBlobLocation**です。
- **folderPath** に設定されている **MyContainer/MySubFolder**, 、コピーしたデータを保持する blob が含まれています。 
- **fileName** に設定されている **MyBlob**, 、出力データを保持する blob です。
- **linkedServiceName** に設定されている **MyAzureStorge**, 、Azure のストレージ用に作成したサービスをリンクします。    

参照してください [Azure blob の場所のプロパティ](https://msdn.microsoft.com/library/dn894089.aspx#AzureBlob) の詳細については、Azure の blob を参照する、ファクトリのデータ テーブルを定義する JSON 要素。

### パイプライン (コピー アクティビティ) の JSON
この例では、次のプロパティを使用して **CopyActivityPipeline** というパイプラインが定義されます。

- **type** プロパティは、**CopyActivity** に設定されます。
- **MyOnPremTable** が入力として指定されます (**inputs** タグ)。
- **MyAzureBlob** が出力として指定されます (**outputs** タグ)。
- **Transformation** セクションには、**source** と **sink** という 2 つのサブ セクションがあります。ソースの種類は **SqlSource** に設定され、シンクの種類は **BlobSink** に設定されます。**sqlReaderQuery** は、変換 (射影) がソース上で実行されるように定義します。すべてのプロパティの詳細については、「[JSON Scripting Reference (JSON スクリプト リファレンス)][json-script-reference]」を参照してください。

         
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

参照してください [パイプラインの JSON の参照を](https://msdn.microsoft.com/library/dn834988.aspx) の詳細については、データの工場出荷時のパイプラインを定義する JSON 要素と [サポートされているソースとシンク](https://msdn.microsoft.com/library/dn894007.aspx) [SqlSource のプロパティの (例: **sqlReaderQuery **の例では) と BlobSink 。


## Azure blob に、内部設置型のファイル システムからのデータのコピー
コピーのアクティビティを使用すると、(Windows と Linux のネットワーク共有またはローカルのホストを Windows)、内部設置型のファイル システムから Azure Blob にファイルをコピーするのにことができます。ホストには Windows または Linux で構成されている Samba のいずれかを指定できます。Data Management Gateway は、ホストに接続できる Windows マシンにインストールする必要があります。

### 前提条件
この例には、次の前提としています。

- **ホスト** -ファイル システムをホストするサーバーの名前が: **\contoso**です。
- **フォルダー** -入力ファイルが含まれているフォルダーの名前が: **marketingcampaign\regionaldata\ {スライス}、という名前のフォルダー {スライス}、2014121112 (2014 年、12 の月、日の 11、12 時間) などのファイルはパーティション分割します 。
### 内部設置型のファイル システムがリンクされているサービスを作成します。
JSON という名前のリンクされているサービスの作成に使用できる次のサンプル **FolderDataStore** 型の **OnPremisesFileSystemLinkedService**です。

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

> [AZURE.NOTE]エスケープ文字を使用してください ' のホストと JSON ファイル内のフォルダーの名前です。 **\Contoso**, を使用して **\Contoso**です。

参照してください [内部設置型のファイル システム リンク サービス](https://msdn.microsoft.com/library/dn930836.aspx) 詳細については、内部設置型のファイル システムを定義する JSON 要素に関するサービスとリンクします。

### 変換先の Azure blob の場合、リンクされているサービスを作成します。
JSON という名前のリンクされているサービスの作成に使用できる次のサンプル **MyAzureStorage** 型の **AzureStorageLinkedSerivce**です。

	{
	    "name": "MyAzureStorage",
	    "properties":
	    {
	        "type": "AzureStorageLinkedService",
	        "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>" "
	    }
	}

参照してください [Azure ストレージ リンク サービス](https://msdn.microsoft.com/library/dn893522.aspx) リンク サービスの詳細については、Azure のストレージを定義する JSON 要素についてです。

### 入力テーブルを作成します。
次の JSON スクリプトを先ほど作成した内部設置型のファイルのリンクされているシステム サービスを参照する入力テーブルを定義します。

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

参照してください [場所のプロパティをファイル システムの内部設置型](https://msdn.microsoft.com/library/dn894089.aspx#OnPremFileSystem) の詳細については、内部設置型のファイル システムを参照する、ファクトリのデータ テーブルを定義する JSON 要素。

### 出力を作成します。
次の JSON スクリプトを出力テーブルを定義: **AzureBlobDest**, を Azure の blob を参照する: **MyBlob** blob フォルダー内: **MySubFolder** blob コンテナーに: **MyContainer**です。
         
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

参照してください [Azure blob の場所のプロパティ](https://msdn.microsoft.com/library/dn894089.aspx#AzureBlob) の詳細については、Azure の blob を参照する、ファクトリのデータ テーブルを定義する JSON 要素。

### パイプラインを作成します。
JSON の次のパイプラインは、内部設置型のファイル システムから Azure の blob を変換先にデータをコピーするコピー アクティビティを含むパイプラインを定義します。
 
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

この例では、パイプライン内容をコピー、binary としてなくいずれかの解析や変換を実行します。活用できることを確認 **同時実行** を並列でファイルのスライスをコピーします。これは、スライスが既に過去の出来事を移動する場合に役立ちます。

> [AZURE.NOTE]「サーバーまたは 1 つ以上のユーザー名を使用して、同じユーザーによって共有リソースへの複数の接続は許可されていません」など、別のユーザー アカウントを持つ UNC パスを使用して同じホストで同時実行のコピー活動はエラーに可能性があります。これは、セキュリティ上の理由には、オペレーティング システムの制限です。ホスト内で、ゲートウェイをインストールまたはし UNC パスの代わりに"localhost"または"local"を使用していずれかの別のゲートウェイと、コピー、アクティビティのスケジュールを設定します。

参照してください [JSON のパイプライン参照](https://msdn.microsoft.com/library/dn834988.aspx) の詳細については、データの工場出荷時のパイプラインを定義する JSON 要素と [サポートされているソースとシンク](https://msdn.microsoft.com/library/dn894007.aspx) FileSystemSource および BlobSink のプロパティのです。

### ファイル システム テーブルを使用するための他のシナリオ

#### 特定のフォルダーの下のすべてのファイルをコピーします。
のみです **folderPath** は JSON の例で指定します。

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
 
#### 特定のフォルダーの下のすべての CSV ファイルをコピーします。
なお、 **fileFilter** に設定されている ***.csv**です。

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

#### 特定のファイルをコピーします。
注意して、 **fileFiter** 、特定のファイルに設定されている: **201501.csv**です。

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

## Azure blob に、内部設置型の Oracle データベースからデータをコピーします。
コピーのアクティビティを使用すると、ファイルを内部設置型の内部設置型の Oracle データベースから、Azure の Blob をコピーするのにことができます。

### 内部設置型の Oracle データベースのリンクされているサービスを作成します。
次の json 形式を内部設置型の Oracle データベースを指すリンクされているサービスを作成することができます。なお、 **型** に設定されている **OnPremisesOracleLinkedService**です。

	{
	    "name": "OnPremOracleSource",
	    "properties": {
	        "type": "OnPremisesOracleLinkedService",
	        "ConnectionString": "data source=ds;User Id=uid;Password=pwd;",
	        "gatewayName": "SomeGateway"
	    }
	}

参照してください [内部設置型の Oracle リンク サービス](https://msdn.microsoft.com/library/dn948537.aspx) 詳細については、内部設置型の Oracle を定義する JSON 要素に関するサービスとリンクします。

### 変換先の Azure blob の場合、リンクされているサービスを作成します。
JSON という名前のリンクされているサービスの作成に使用できる次のサンプル **MyAzureStorage** 型の **AzureStorageLinkedSerivce**です。

	{
	    "name": "AzureBlobDest",
	    "properties":
	    {
	        "type": "AzureStorageLinkedService",
	        "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>" "
	    }
	}

参照してください [Azure ストレージ リンク サービス](https://msdn.microsoft.com/library/dn893522.aspx) リンク サービスの詳細については、Azure のストレージを定義する JSON 要素についてです。

### 入力テーブルを作成します。
JSON の次の例を内部設置型の Oracle データベース内のテーブルを参照する Azure のデータのファクトリ テーブルを作成することができます。なお、 **の場所の種類** に設定されている **OnPremisesOracleTableLocation**です。

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

参照してください [場所のプロパティを内部設置型の Oracle](https://msdn.microsoft.com/library/dn894089.aspx#Oracle) の詳細については、内部設置型の Oracle データベース内のテーブルを参照する、ファクトリのデータ テーブルを定義する JSON 要素。

### 出力テーブルを作成します。
次の JSON スクリプトを出力テーブルを定義: **MyAzureBlob**, を Azure の blob を参照する: **MyBlob** blob フォルダー内: **MySubFolder** blob コンテナーに: **MyContainer**です。
         
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

参照してください [Azure blob の場所のプロパティ](https://msdn.microsoft.com/library/dn894089.aspx#AzureBlob) の詳細については、Azure の blob を参照する、ファクトリのデータ テーブルを定義する JSON 要素。

### パイプラインを作成します。
Oracle データベースのテーブルからのデータを Azure ストレージ blob にコピーするコピー アクティビティを次のサンプル パイプラインにあります。

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

参照してください [JSON のパイプライン参照](https://msdn.microsoft.com/library/dn834988.aspx) の詳細については、データの工場出荷時のパイプラインを定義する JSON 要素と [サポートされているソースとシンク](https://msdn.microsoft.com/library/dn894007.aspx) OracleSource および BlobSink のプロパティのです。

## 関連項目

- [Azure Data Factory を使用してデータをコピーする][adf-copyactivity]
- [コピー アクティビティ - JSON スクリプト リファレンス](https://msdn.microsoft.com/library/dn835035.aspx)
- [ビデオ: Azure Data Factory コピーアクティビティの概要][copy-activity-video]


[adf-copyactivity]: data-factory-copy-activity.md
[copy-activity-video]: http://azure.microsoft.com/documentation/videos/introducing-azure-data-factory-copy-activity/

<!---HONumber=GIT-SubDir--> 