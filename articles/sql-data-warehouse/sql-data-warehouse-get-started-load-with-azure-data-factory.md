<properties
	pageTitle="Azure Data Factory を使用してデータを読み込む | Microsoft Azure"
	description="Azure Data Factory を使用してデータを読み込む方法"
	services="sql-data-warehouse"
	documentationCenter="NA"
	authors="lodipalm"
	manager="barbkess"
	editor=""
	tags="azure-sql-data-warehouse"/>
<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="11/19/2015"
   ms.author="lodipalm"/>

# Azure Data Factory を使用してデータを読み込む

> [AZURE.SELECTOR]
- [Data Factory](sql-data-warehouse-get-started-load-with-azure-data-factory.md)
- [PolyBase](sql-data-warehouse-get-started-load-with-polybase.md)
- [BCP](sql-data-warehouse-load-with-bcp.md)

 このチュートリアルでは、Azure Storage BLOB から SQL Data Warehouse にデータを移動するパイプラインを Azure Data Factory に作成する方法について説明します。以降の手順では、次の操作を実行します。

+ Azure Storage Blob にサンプルのデータを設定する。
+ Azure Data Factory にリソースを接続する。
+ Storage の BLOB から SQL Data Warehouse にデータを移動するためのパイプラインを作成する。

>[AZURE.VIDEO loading-azure-sql-data-warehouse-with-azure-data-factory]

## リソース

このチュートリアルを行うには、以下のリソースが必要です。

   + **Azure Storage BLOB**: Azure Storage BLOB が、パイプラインのデータのソースになります。既存の BLOB を使うことも、[新しい BLOB をプロビジョニング](../storage/storage-create-storage-account/)することもできます。

   + **SQL Data Warehouse**: このチュートリアルでは、SQL Data Warehouse にデータを移動します。インスタンスをまだ設定していない方のために、[こちら](sql-data-warehouse-get-started-provision.md)で方法を説明しています。さらに、インスタンスは、AdventureWorks DW データセットを使って設定する必要があります。データ ウェアハウスにサンプル データをプロビジョニングしていない場合は、[これを手動で読み込む](sql-data-warehouse-get-started-manually-load-samples.md)ことができます。

   + **Azure Data Factory**: Azure Data Factory は、実際の読み込みを実行します。Azure Data Factory の設定やパイプラインの作成の詳細については、[こちら](../data-factory/data-factory-build-your-first-pipeline-using-editor/)を参照してください。

必要なものがすべて揃ったら、データを準備して、Azure Data Factory パイプラインを作成する作業を開始できます。

## サンプル データ

パイプラインの各要素に加えて、Azure Data Factory でデータを読み込む練習に使用できるサンプル データも必要になります。

1. 最初に、[サンプル データをダウンロード](https://migrhoststorage.blob.core.windows.net/adfsample/FactInternetSales.csv)します。このデータは、ご利用のサンプル データに既に含まれているサンプル データと共に使用することで、さらに 3 年分の売上データを提供します。

2. ダウンロードしたデータは、AZCopy で次のスクリプトを実行して Blob Storage に移動できます。

        AzCopy /Source:<Sample Data Location>  /Dest:https://<storage account>.blob.core.windows.net/<container name> /DestKey:<storage key> /Pattern:FactInternetSales.csv

	AZCopy をインストールして使用する方法の詳細については、[AZCopy のドキュメント](../storage/storage-use-azcopy/)を参照してください。

データを準備した後は、ストレージ アカウントから SQL Data Warehouse にデータを移動するパイプラインを作成するために、データ ファクトリに移動します。

## Azure Data Factory の使用

すべての設定が完了したら、Azure ポータルの Azure Data Factory インスタンスに移動して、パイプラインの設定を開始できます。そのためには、[Azure クラシック ポータル](portal.azure.com)に移動し、左側のメニューから対象のデータ ファクトリを選択します。

データ ウェアハウスにデータを転送する Azure Data Factory パイプラインを設定するには、サービスのリンク、データセットの定義、パイプラインの作成の 3 つの手順を実行します。

### リンクされたサービスの作成

最初の手順では、Azure ストレージ アカウントと SQL Data Warehouse をデータ ファクトリにリンクします。

1. まず、データ ファクトリの [リンクされたサービス] セクション、[新しいデータ ストア] の順にクリックして、登録プロセスを開始します。 次に、Azure ストレージを登録する名前を選択します。種類として [Azure Storage] を選択し、アカウント名とアカウント キーを入力します。

2. SQL Data Warehouse を登録するには、[作成とデプロイ] セクションに移動して [新しいデータ ストア] を選択し、[Azure SQL Data Warehouse] を選択する必要があります。その後、次のテンプレートへの入力が必要になります。

		{
		    "name": "<Linked Service Name>",
		    "properties": {
		        "description": "",
		        "type": "AzureSqlDW",
		        "typeProperties": {
		            "connectionString": "Data Source=tcp:<server name>.database.windows.net,1433;Initial Catalog=<server name>;Integrated Security=False;User ID=<user>@<servername>;Password=<password>;Connect Timeout=30;Encrypt=True"
		        }
		    }
		}

### データセットの登録

リンクされたサービスを作成した後は、データ セットを定義する必要があります。これは、ストレージからデータ ウェアハウスに移動するデータの構造を定義することを意味します。作成に関する詳しい情報を得ることができます。

1. データ ファクトリの [作成とデプロイ] セクションに移動し、このプロセスを開始します。

2. [新しいデータセット]、[Azure BLOB ストレージ] の順にクリックし、ストレージをデータ ファクトリにリンクします。次のスクリプトを使用して、Azure Blob Storage のデータを定義することができます。

		{
			"name": "<Dataset Name>",
			"properties": {
				"type": "AzureBlob",
				"linkedServiceName": "<linked storage name>",
				"typeProperties": {
					"folderPath": "<containter name>",
					"fileName": "FactInternetSales.csv",
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



3. 次に、SQL Data Warehouse のデータセットも定義します。前の手順と同様に、[新しいデータセット]、[Azure SQL Data Warehouse] の順にクリックします。

		{
		  "name": "<dataset name>",
		  "properties": {
		    "type": "AzureSqlDWTable",
		    "linkedServiceName": "<linked data warehouse name>",
		    "typeProperties": {
		      "tableName": "FactInternetSales"
		    },
		    "availability": {
		      "frequency": "Hour",
		      "interval": 1
		    }
		  }
		}

		{
		  "name": "DWDataset",
		  "properties": {
			"type": "AzureSqlDWTable",
			"linkedServiceName": "AzureSqlDWLinkedService",
			"typeProperties": {
			  "tableName": "FactInternetSales"
			},
			"availability": {
			  "frequency": "Hour",
			  "interval": 1
			}
		  }
		}

### パイプラインの設定

最後に、Azure Data Factory でパイプラインを設定して実行します。これは、実際のデータの移動を実行する操作です。SQL Data Warehouse と Azure Data Factory で実行できるすべての操作については、[こちら](../data-factory/data-factory-azure-sql-data-warehouse-connector/)を参照してください。

[作成とデプロイ] セクションで、[その他のコマンド]、[新しいパイプライン] の順にクリックします。パイプラインを作成した後、次のコードを使用して、データ ウェアハウスにデータを転送できます。

	{
	"name": "<Pipeline Name>",
	"properties": {
		"description": "<Description>",
		"activities": [
			{
				"type": "Copy",
				"typeProperties": {
					"source": {
						"type": "BlobSource",
						"skipHeaderLineCount": 1
					},
					"sink": {
						"type": "SqlDWSink",
						"writeBatchSize": 0,
						"writeBatchTimeout": "00:00:10"
					}
				},
				"inputs": [
					{
						"name": "<Storage Dataset>"
					}
				],
				"outputs": [
					{
						"name": "<Data Warehouse Dataset>"
					}
				],
				"policy": {
					"timeout": "01:00:00",
					"concurrency": 1
				},
				"scheduler": {
					"frequency": "Hour",
					"interval": 1
				},
				"name": "Sample Copy",
				"description": "Copy Activity"
			}
		],
		"start": "<Date YYYY-MM-DD>",
		"end": "<Date YYYY-MM-DD>",
		"isPaused": false
	}
	}
	

<!---HONumber=AcomDC_1203_2015-->