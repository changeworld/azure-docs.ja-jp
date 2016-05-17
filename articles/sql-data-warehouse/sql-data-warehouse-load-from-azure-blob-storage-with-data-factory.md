<properties
   pageTitle="Azure Blob Storage から Azure SQL Data Warehouse へのデータの読み込み (Azure Data Factory) | Microsoft Azure"
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
   ms.date="04/29/2016"
   ms.author="lodipalm;barbkess;sonyama"/>

# Azure Blob Storage から Azure SQL Data Warehouse へのデータの読み込み (Azure Data Factory)

> [AZURE.SELECTOR]
- [Data Factory](sql-data-warehouse-load-from-azure-blob-storage-with-data-factory.md)
- [PolyBase](sql-data-warehouse-load-from-azure-blob-storage-with-polybase.md)

 このチュートリアルでは、Azure Storage BLOB から SQL Data Warehouse にデータを移動するパイプラインを Azure Data Factory で作成する方法について説明します。以降の手順では、次の操作を実行します。

+ Azure Storage Blob にサンプルのデータを設定する。
+ Azure Data Factory にリソースを接続する。
+ Storage の BLOB から SQL Data Warehouse にデータを移動するためのパイプラインを作成する。

>[AZURE.VIDEO loading-azure-sql-data-warehouse-with-azure-data-factory]


## 開始する前に

Azure Data Factory を理解するには、[Azure Data Factory の概要][]に関する記事を参照してください。

### リソースを作成または識別する

このチュートリアルを開始する前に、次のリソースを用意する必要があります。

   + **Azure Storage BLOB**: このチュートリアルでは、Azure Data Factory パイプラインのデータ ソースとして Azure Storage BLOB を使用します。そのため、サンプル データを格納できる Azure Storage BLOB が必要です。まだお持ちでない場合は、「[ストレージ アカウントの作成][]」を参照してください。

   + **SQL Data Warehouse**: このチュートリアルでは、Azure Storage BLOB から SQL Data Warehouse にデータを移動します。したがって、AdventureWorksDW サンプル データを読み込むデータ ウェアハウスをオンラインにする必要があります。データ ウェアハウスがまだない場合は、[データ ウェアハウスをプロビジョニング][Create a SQL Data Warehouse]する方法を学習してください。データ ウェアハウスはあっても、それをサンプル データでまだプロビジョニングしていない場合は、[サンプル データを手動で読み込む][Load sample data into SQL Data Warehouse]ことができます。

   + **Azure Data Factory**: Azure Data Factory によって実際の読み込みが実行されるので、データ移動パイプラインの作成に使用できるデータ ファクトリが必要です。まだデータ ファクトリがない場合は、[Azure Data Factory (Data Factory Editor) の概要][]に関するページの手順 1. でその作成方法を確認してください。

   + **AZCopy**: サンプル データをローカル クライアントから Azure Storage BLOB にコピーするには、AZCopy が必要です。インストールの手順については、[AZCopy のドキュメント][]を参照してください。

## 手順 1: サンプル データを Azure Storage BLOB にコピーする

すべてのピースが揃ったら、サンプル データを Azure Storage BLOB にいつでもコピーすることができます。

1. [サンプル データをダウンロードします][]。このデータにより、別の 3 年分の売上データが AdventureWorksDW サンプル データに追加されます。

2. 次の AZCopy コマンドを使用して、3 年分のデータを Azure Storage BLOB にコピーします。

````
AzCopy /Source:<Sample Data Location>  /Dest:https://<storage account>.blob.core.windows.net/<container name> /DestKey:<storage key> /Pattern:FactInternetSales.csv
````


## 手順 2: Azure Data Factory にリソースを接続する

これでデータの準備ができたので、Azure Data Factory パイプラインを作成して、データを Azure Storage BLOB から SQL Data Warehouse に移動することができます。

そのためには、まず、[Azure ポータル][]を開き、左側のメニューで対象のデータ ファクトリを選択します。

### 手順 2.1: リンクされたサービスを作成する

Azure ストレージ アカウントと SQL Data Warehouse をデータ ファクトリにリンクします。

1. まず、データ ファクトリの [リンクされたサービス] セクション、[新しいデータ ストア] の順にクリックして、登録プロセスを開始します。 次に、Azure ストレージを登録する名前を選択します。種類として [Azure Storage] を選択し、アカウント名とアカウント キーを入力します。

2. SQL Data Warehouse を登録するには、[作成とデプロイ] セクションに移動して [新しいデータ ストア] を選択し、[Azure SQL Data Warehouse] を選択します。コピーおよび貼り付けを行って、このテンプレートに特定の情報を入力します。

```JSON
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
```

### 手順 2.2: データ ソースを定義する

リンクされたサービスを作成した後は、データ セットを定義する必要があります。これは、ストレージからデータ ウェアハウスに移動するデータの構造を定義することを意味します。作成に関する詳しい情報を得ることができます。

1. データ ファクトリの [作成とデプロイ] セクションに移動し、このプロセスを開始します。

2. [新しいデータセット]、[Azure BLOB ストレージ] の順にクリックし、ストレージをデータ ファクトリにリンクします。次のスクリプトを使用して、Azure BLOB ストレージのデータを定義することができます。

```JSON
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
```


3. 次に、SQL Data Warehouse のデータセットも定義します。前の手順と同様に、[新しいデータセット]、[Azure SQL Data Warehouse] の順にクリックします。

```JSON
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
```

## 手順 3: パイプラインを作成して実行する

最後に、Azure Data Factory でパイプラインを設定して実行します。これは、実際のデータの移動を実行する操作です。SQL Data Warehouse と Azure Data Factory で実行できるすべての操作については、[こちら][Move data to and from Azure SQL Data Warehouse using Azure Data Factory]を参照してください。

[作成とデプロイ] セクションで、[その他のコマンド]、[新しいパイプライン] の順にクリックします。パイプラインを作成した後、次のコードを使用して、データ ウェアハウスにデータを転送できます。

```JSON
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
```

## 次のステップ

詳細については、まず以下の情報を参照してください。

- [Azure Data Factory のラーニング パス][]。
- [Azure SQL Data Warehouse コネクタ][]。これは、Azure Data Factory と Azure SQL Data Warehouse を組み合わせて使用するための主要な参照トピックとなります。


以下のトピックでは、Azure Data Factory に関する詳細情報を提供します。Azure SQL Database または HDinsight について説明しますが、この情報は Azure SQL Data Warehouse にも該当します。

- [チュートリアル: 初めての Data Factory の作成][]。これは、Azure Data Factory を使用してデータを処理するための主要なチュートリアルです。このチュートリアルでは、HDInsight を使用して Web ログの変換および分析を毎月行う初めてのパイプラインを作成します。なお、このチュートリアルには、コピー アクティビティはありません。
- [チュートリアル: Azure BLOB Storage から Azure SQL Database にデータをコピーする][]。このチュートリアルでは、Azure Data Factory でパイプラインを作成し、データを Azure Storage BLOB から Azure SQL Database にコピーします。
- [実際のシナリオのチュートリアル][]。これは、Azure Data Factory の使用に関する詳細なチュートリアルです。

<!--Image references-->

<!--Article references-->
[AZCopy のドキュメント]: ../storage/storage-use-azcopy.md
[Azure SQL Data Warehouse コネクタ]: ../data-factory/data-factory-azure-sql-data-warehouse-connector.md
[BCP]: sql-data-warehouse-load-with-bcp.md
[Create a SQL Data Warehouse]: sql-data-warehouse-get-started-provision.md
[ストレージ アカウントの作成]: ../storage/storage-create-storage-account.md#create-a-storage-account
[Data Factory]: sql-data-warehouse-get-started-load-with-azure-data-factory.md
[Azure Data Factory (Data Factory Editor) の概要]: ../data-factory/data-factory-build-your-first-pipeline-using-editor.md
[Azure Data Factory の概要]: ../data-factory/data-factory-introduction.md
[Load sample data into SQL Data Warehouse]: sql-data-warehouse-get-started-manually-load-samples.md
[Move data to and from Azure SQL Data Warehouse using Azure Data Factory]: ../data-factory/data-factory-azure-sql-data-warehouse-connector.md
[PolyBase]: sql-data-warehouse-get-started-load-with-polybase.md
[実際のシナリオのチュートリアル]: ../data-factory/data-factory-tutorial.md
[チュートリアル: Azure BLOB Storage から Azure SQL Database にデータをコピーする]: ../data-factory/data-factory-get-started
[チュートリアル: 初めての Data Factory の作成]: ../data-factory/data-factory-build-your-first-pipeline.md

<!--MSDN references-->

<!--Other Web references-->
[Azure Data Factory のラーニング パス]: https://azure.microsoft.com/documentation/learning-paths/data-factory
[Azure ポータル]: https://portal.azure.com
[サンプル データをダウンロードします]: https://migrhoststorage.blob.core.windows.net/adfsample/FactInternetSales.csv

<!---HONumber=AcomDC_0511_2016-->