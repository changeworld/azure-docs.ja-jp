<properties 
	pageTitle="Data Factory を使用して Amazon Redshift からデータを移動する | Microsoft Azure" 
	description="Azure Data Factory を使用して Amazon Redshift からデータを移動する方法を説明します。" 
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
	ms.date="08/23/2016" 
	ms.author="jingwang"/>

# Azure Data Factory を使用して Amazon Redshift からデータを移動する

この記事では、Azure Data Factory のコピー アクティビティを使用して、Amazon Redshift と他のデータ ストアとの間でデータを移動する方法について説明します。この記事は、「[データ移動アクティビティ](data-factory-data-movement-activities.md)」という記事に基づき、コピー アクティビティによるデータ移動の一般概要とソース/シンク データ ストアの一覧について紹介しています。

データ ファクトリは、他のデータ ストアから Amazon Redshift へのデータの移動ではなく、Amazon Redshift から他のデータ ストアへのデータの移動のみをサポートします。

## 前提条件

- オンプレミスのデータ ストアにデータを移動する場合は、Data Management Gateway (コンピューターの IP アドレスを使用) による Amazon Redshift クラスターへのアクセスを付与します。手順については、「[クラスターへのアクセスを承認する](http://docs.aws.amazon.com/redshift/latest/gsg/rs-gsg-authorize-cluster-access.html)」を参照してください。
- Azure データ ストアにデータを移動する場合、Microsoft Azure データ センターで使用されるコンピューティング IP アドレス (SQL 範囲を含む) については 「[Azure データ センターの IP 範囲](https://www.microsoft.com/download/details.aspx?id=41653)」を参照してください。

## データのコピー ウィザード
Amazon Redshift からデータをコピーするパイプラインを作成する最も簡単な方法は、データのコピー ウィザードを使用することです。データのコピー ウィザードを使用してパイプラインを作成する簡単な手順については、「[チュートリアル: コピー ウィザードを使用してパイプラインを作成する](data-factory-copy-data-wizard-tutorial.md)」をご覧ください。

次の例は、[Azure ポータル](data-factory-copy-activity-tutorial-using-azure-portal.md)、[Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md)、または [Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md) を使用してパイプラインを作成する際に使用できるサンプルの JSON 定義です。これは、Amazon Redshift から Azure BLOB ストレージにデータをコピーする方法を示します。ただし、データは[ここ](data-factory-data-movement-activities.md#supported-data-stores)に記述されている任意のシンクにコピーできます。

## サンプル: Amazon Redshift から Azure BLOB にデータをコピーする
このサンプルは、Amazon Redshift データベースから Azure BLOB ストレージにデータをコピーする方法を示します。Azure Data Factory のコピー アクティビティを使用して、[こちら](data-factory-data-movement-activities.md#supported-data-stores)に記載されているシンクのいずれかにデータを**直接**コピーすることもできます。
 
このサンプルでは、次の Data Factory のエンティティがあります。

- [Amazon Redshift](#linked-service-properties) 型のリンクされたサービス。
- [AzureStorage](data-factory-azure-blob-connector.md#azure-storage-linked-service-properties) 型のリンクされたサービス。
- [RelationalTable](#dataset-type-properties) 型の入力[データセット](data-factory-create-datasets.md)。
- [AzureBlob](data-factory-azure-blob-connector.md#azure-blob-dataset-type-properties) 型の出力[データセット](data-factory-create-datasets.md)。
- [RelationalSource](#copy-activity-type-properties) と [BlobSink](data-factory-azure-blob-connector.md#azure-blob-copy-activity-type-properties) を使用するコピー アクティビティを含む[パイプライン](data-factory-create-pipelines.md)。

このサンプルは Amazon Redshift データベースのクエリ結果のデータを BLOB に 1 時間ごとにコピーします。これらのサンプルで使用される JSON プロパティの説明はサンプルに続くセクションにあります。

**Amazon Redshift のリンクされたサービス**

	{
	    "name": "AmazonRedshiftLinkedService",
	    "properties":
	    {
	        "type": "AmazonRedshift",
	        "typeProperties":
	        {
	            "server": "< The IP address or host name of the Amazon Redshift server >",
	            "port": <The number of the TCP port that the Amazon Redshift server uses to listen for client connections.>,
	            "database": "<The database name of the Amazon Redshift database>",
	            "username": "<username>",
	            "password": "<password>"
	        }
	    }
	}


**Azure Storage のリンクされたサービス**

	{
	  "name": "AzureStorageLinkedService",
	  "properties": {
	    "type": "AzureStorage",
	    "typeProperties": {
	      "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
	    }
	  }
	}

**Amazon Redshift の入力データセット**

**”external” を true** に設定すると、データセットが Data Factory の外部にあり、Data Factory のアクティビティによって生成されたものではないことが Data Factory サービスに通知されます。パイプラインのアクティビティで生成されない入力データセットで、このプロパティを true に設定します。

	{
	    "name": "AmazonRedshiftInputDataset",
	    "properties": {
	        "type": "RelationalTable",
	        "linkedServiceName": "AmazonRedshiftLinkedService",
	        "typeProperties": {
	            "tableName": "<Table name>"
	        },
	        "availability": {
	            "frequency": "Hour",
	            "interval": 1
	        },
			"external": true
	    }
	}


**Azure BLOB の出力データセット**

データは新しい BLOB に 1 時間おきに書き込まれます (頻度: 時間、間隔: 1)。BLOB のフォルダー パスは、処理中のスライスの開始時間に基づき、動的に評価されます。フォルダー パスは開始時間の年、月、日、時刻の部分を使用します。

	{
	    "name": "AzureBlobOutputDataSet",
	    "properties": {
	        "type": "AzureBlob",
	        "linkedServiceName": "AzureStorageLinkedService",
	        "typeProperties": {
	            "folderPath": "mycontainer/fromamazonredshift/yearno={Year}/monthno={Month}/dayno={Day}/hourno={Hour}",
	            "format": {
	                "type": "TextFormat",
	                "rowDelimiter": "\n",
	                "columnDelimiter": "\t"
	            },
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
	        "availability": {
	            "frequency": "Hour",
	            "interval": 1
	        }
	    }
	}



**コピー アクティビティのあるパイプライン**

パイプラインには、入力データセットと出力データセットを使用するように構成され、1 時間おきに実行するようにスケジュールされているコピー アクティビティが含まれています。パイプライン JSON 定義で、**source** 型が **RelationalSource** に設定され、**sink** 型が **BlobSink** に設定されています。**query** プロパティに指定されている SQL クエリは過去のデータを選択してコピーします。
	
	{
	    "name": "CopyAmazonRedshiftToBlob",
	    "properties": {
	        "description": "pipeline for copy activity",
	        "activities": [
	            {
	                "type": "Copy",
	                "typeProperties": {
	                    "source": {
	                        "type": "RelationalSource",
	                        "query": "$$Text.Format('select * from MyTable where timestamp >= \\'{0:yyyy-MM-ddTHH:mm:ss}\\' AND timestamp < \\'{1:yyyy-MM-ddTHH:mm:ss}\\'', WindowStart, WindowEnd)"
	                    },
	                    "sink": {
	                        "type": "BlobSink",
	                        "writeBatchSize": 0,
	                        "writeBatchTimeout": "00:00:00"
	                    }
	                },
	                "inputs": [
	                    {
	                        "name": "AmazonRedshiftInputDataset"
	                    }
	                ],
	                "outputs": [
	                    {
	                        "name": "AzureBlobOutputDataSet"
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
	                "name": "AmazonRedshiftToBlob"
	            }
	        ],
	        "start": "2014-06-01T18:00:00Z",
	        "end": "2014-06-01T19:00:00Z"
	    }
	}



## リンクされたサービスのプロパティ

次の表は、Amazon Redshift のリンクされたサービスに固有の JSON 要素の説明をまとめたものです。

| プロパティ | 説明 | 必須 |
| -------- | ----------- | -------- | 
| type | type プロパティを **AmazonRedshift ** に設定する必要があります。 | はい |
| server | Amazon Redshift サーバーの IP アドレスまたはホスト名。 | はい |
| ポート | Amazon Redshift サーバーがクライアント接続のリッスンに使用する TCP ポートの数。 | いいえ、既定値: 5439 |
| database | Amazon Redshift データベースの名前。 | はい |
| username | データベースへのアクセスを持つユーザーの名前。| はい |
| パスワード | ユーザー アカウントのパスワード。| はい |


## データセットの type プロパティ

データセットの定義に利用できるセクションとプロパティの完全な一覧については、「[データセットの作成](data-factory-create-datasets.md)」という記事を参照してください。構造、可用性、ポリシーなどのセクションは、データセットのすべての型 (Azure SQL、Azure BLOB、Azure テーブルなど) でほぼ同じです。

**typeProperties** セクションはデータセット型ごとに異なり、データ ストアのデータの場所などに関する情報を提供します。**RelationalTable** 型のデータセット (Amazon Redshift データセットを含む) の typeProperties セクションには次のプロパティがあります。

| プロパティ | 説明 | 必須 |
| -------- | ----------- | -------- |
| tableName | リンクされたサービスが参照する Amazon Redshift データベースのテーブルの名前です。 | いいえ ( **RelationalSource** の **クエリ** が指定されている場合) | 

## コピー アクティビティの type プロパティ

アクティビティの定義に利用できるセクションとプロパティの完全な一覧については、「[パイプラインの作成](data-factory-create-pipelines.md)」を参照してください。名前、説明、入力テーブル、出力テーブル、ポリシーなどのプロパティは、あらゆる種類のアクティビティで使用できます。

一方、アクティビティの **typeProperties** セクションで使用できるプロパティは、各アクティビティの種類によって異なります。コピー アクティビティの場合、ソースとシンクの種類によって異なります。

コピー アクティビティのソースの種類が **RelationalSource** (Amazon Redshift を含む) である場合は、typeProperties セクションで次のプロパティを使用できます。

| プロパティ | 説明 | 使用できる値 | 必須 |
| -------- | ----------- | -------------- | -------- |
| query | カスタム クエリを使用してデータを読み取ります。 | SQL クエリ文字列。例: Select * from MyTable。 | いいえ (**データセット**の **tableName** が指定されている場合) | 

[AZURE.INCLUDE [data-factory-structure-for-rectangualr-datasets](../../includes/data-factory-structure-for-rectangualr-datasets.md)]

### Amazon Redshift の型マッピング

[データ移動アクティビティ](data-factory-data-movement-activities.md)に関する記事のとおり、コピー アクティビティは次の 2 段階のアプローチで型を source から sink に自動的に変換します。

1. ネイティブの source 型から .NET 型に変換する
2. .NET 型からネイティブの sink 型に変換する

Amazon Redshift にデータを移動する場合、Amazon Redshift 型から .NET 型に対する次のマッピングが使用されます。

Amazon Redshift 型 | .NET ベースの型
-------------------- | ---------------
SMALLINT | Int16
INTEGER | Int32
BIGINT | Int64
DECIMAL | Decimal
REAL | Single
DOUBLE PRECISION | Double
BOOLEAN | String
CHAR | String
VARCHAR | String
DATE | DateTime
TIMESTAMP | DateTime
TEXT | String



[AZURE.INCLUDE [data-factory-column-mapping](../../includes/data-factory-column-mapping.md)]

[AZURE.INCLUDE [data-factory-type-repeatability-for-relational-sources](../../includes/data-factory-type-repeatability-for-relational-sources.md)]

## パフォーマンスとチューニング  
Azure Data Factory でのデータ移動 (コピー アクティビティ) のパフォーマンスに影響する主な要因と、パフォーマンスを最適化するための各種方法については、「[コピー アクティビティのパフォーマンスとチューニングに関するガイド](data-factory-copy-activity-performance.md)」をご覧ください。

## 次のステップ
次の記事を参照してください。
- コピー アクティビティでパイプラインを作成するための詳細な手順については、[コピー アクティビティのチュートリアル](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)を参照してください。

<!---HONumber=AcomDC_0928_2016-->