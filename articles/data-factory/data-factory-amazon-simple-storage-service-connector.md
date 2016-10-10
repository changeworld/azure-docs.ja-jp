<properties 
	pageTitle="Data Factory を使用した Amazon Simple Storage Service からのデータの移動 | Microsoft Azure" 
	description="Azure Data Factory を使用した Amazon Simple Storage Service (S3) からのデータの移動方法について説明します。" 
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
	ms.date="08/25/2016" 
	ms.author="jingwang"/>

# Azure Data Factory を使用した Amazon Simple Storage Service からのデータの移動

この記事では、Azure Data Factory のコピー アクティビティを使用して、Amazon Simple Storage Service (S3) と他のデータ ストアとの間でデータを移動する方法について説明します。この記事は、「[データ移動アクティビティ](data-factory-data-movement-activities.md)」という記事に基づき、データ移動の一般概要とコピー アクティビティでサポートされるソース/シンク データ ストアの一覧について紹介しています。

データ ファクトリは、他のデータ ストアから Amazon S3 へのデータの移動ではなく、Amazon S3 から他のデータ ストアへのデータの移動のみをサポートします。

## データのコピー ウィザード
Amazon S3 からデータをコピーするパイプラインを作成する最も簡単な方法は、データのコピー ウィザードを使用することです。データのコピー ウィザードを使用してパイプラインを作成する簡単な手順については、「[チュートリアル: コピー ウィザードを使用してパイプラインを作成する](data-factory-copy-data-wizard-tutorial.md)」をご覧ください。

次の例は、[Azure ポータル](data-factory-copy-activity-tutorial-using-azure-portal.md)、[Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md)、または [Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md) を使用してパイプラインを作成する際に使用できるサンプルの JSON 定義です。これは、Amazon S3 から Azure BLOB ストレージにデータをコピーする方法を示します。ただし、データは[ここ](data-factory-data-movement-activities.md#supported-data-stores)に記述されている任意のシンクにコピーできます。

## サンプル: Amazon S3 から Azure BLOB にデータをコピーする
このサンプルは、Amazon S3 から Azure BLOB ストレージにデータをコピーする方法を示します。Azure Data Factory のコピー アクティビティを使用して、[こちら](data-factory-data-movement-activities.md#supported-data-stores)に記載されているシンクのいずれかにデータを**直接**コピーすることもできます。
 
このサンプルでは、次の Data Factory のエンティティがあります。

- [AwsAccessKey](#linked-service-properties) 型のリンクされたサービス。
- [AzureStorage](data-factory-azure-blob-connector.md#azure-storage-linked-service-properties) 型のリンクされたサービス。
- [AmazonS3](#dataset-type-properties) 型の入力[データセット](data-factory-create-datasets.md)。
- [AzureBlob](data-factory-azure-blob-connector.md#azure-blob-dataset-type-properties) 型の出力[データセット](data-factory-create-datasets.md)。
- [FileSystemSource](#copy-activity-type-properties) および [BlobSink](data-factory-azure-blob-connector.md#azure-blob-copy-activity-type-properties) を使用するコピー アクティビティを含む[パイプライン](data-factory-create-pipelines.md)。

このサンプル データでは、1 時間おきに Amazon S3 から Azure BLOB にデータがコピーされます。これらのサンプルで使用される JSON プロパティの説明はサンプルに続くセクションにあります。

**Amazon S3 のリンクされたサービス**

	{
	    "name": "AmazonS3LinkedService",
	    "properties": {
	        "type": "AwsAccessKey",
	        "typeProperties": {
	            "accessKeyId": "<access key id>",
	            "secretAccessKey": "<secret access key>"
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

**Amazon S3 の入力データセット**

**”external” を true** に設定すると、データセットが Data Factory の外部にあり、Data Factory のアクティビティによって生成されたものではないことが Data Factory サービスに通知されます。パイプラインのアクティビティで生成されない入力データセットで、このプロパティを true に設定します。

	{
	    "name": "AmazonS3InputDataset",
	    "properties": {
	        "type": "AmazonS3",
	        "linkedServiceName": "AmazonS3LinkedService",
	        "typeProperties": {
	            "key": "testFolder/test.orc",
	            "bucketName": "testbucket",
	            "format": {
	                "type": "OrcFormat"
	            }
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
	            "folderPath": "mycontainer/fromamazons3/yearno={Year}/monthno={Month}/dayno={Day}/hourno={Hour}",
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

パイプラインには、入力データセットと出力データセットを使用するように構成され、1 時間おきに実行するようにスケジュールされているコピー アクティビティが含まれています。パイプライン JSON 定義で、**source** 型が **FileSystemSource** に設定され、**sink** 型が **BlobSink** に設定されています。
	
	{
	    "name": "CopyAmazonS3ToBlob",
	    "properties": {
	        "description": "pipeline for copy activity",
	        "activities": [
	            {
	                "type": "Copy",
	                "typeProperties": {
	                    "source": {
                        	"type": "FileSystemSource",
                        	"recursive": true
	                    },
	                    "sink": {
	                        "type": "BlobSink",
	                        "writeBatchSize": 0,
	                        "writeBatchTimeout": "00:00:00"
	                    }
	                },
	                "inputs": [
	                    {
	                        "name": "AmazonS3InputDataset"
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
	                "name": "AmazonS3ToBlob"
	            }
	        ],
	        "start": "2014-08-08T18:00:00Z",
	        "end": "2014-08-08T19:00:00Z"
	    }
	}



## リンクされたサービスのプロパティ

次の表は、Amazon S3 (**AwsAccessKey**) のリンクされたサービスに固有の JSON 要素の説明をまとめたものです。

| プロパティ | 説明 | 使用できる値 | 必須 |
| -------- | ----------- | -------- | ------- |  
| accessKeyID | シークレット アクセス キーの ID。 | string | はい |
| secretAccessKey | シークレット アクセス キー自体。 | 暗号化された秘密文字列 | はい | 


## データセットの type プロパティ

データセットの定義に利用できるセクションとプロパティの完全な一覧については、「[データセットの作成](data-factory-create-datasets.md)」という記事を参照してください。構造、可用性、ポリシーなどのセクションは、データセットのすべての型 (Azure SQL、Azure BLOB、Azure テーブルなど) でほぼ同じです。

**typeProperties** セクションはデータセット型ごとに異なり、データ ストアのデータの場所などに関する情報を提供します。**AmazonS3** 型のデータセットの typeProperties セクション (Amazon S3 データセットを含む) には次のプロパティがあります。

| プロパティ | 説明 | 使用できる値 | 必須 |
| -------- | ----------- | -------- | ------ | 
| bucketName | S3 バケットの名前。 | String | はい |
| key | S3 オブジェクト キー。 | String | なし | 
| prefix | S3 オブジェクト キーのプレフィックス。キーがこのプレフィックスで始まるオブジェクトが選択されます。キーが空の場合にのみ適用されます。 | String | なし | 
| version | S3 のバージョン管理が有効になっている場合の S3 オブジェクトのバージョン。 | String | なし |  
| BlobSink の format | **TextFormat**、**AvroFormat**、**JsonFormat**、**OrcFormat**、**ParquetFormat** の 5 種類の形式がサポートされています。形式の **type** プロパティをいずれかの値に設定します。詳細については、「[TextFormat の指定](#specifying-textformat)」、「[AvroFormat の指定](#specifying-avroformat)」、「[JsonFormat の指定](#specifying-jsonformat)」、「[OrcFormat の指定](#specifying-orcformat)」、および「[ParquetFormat の指定](#specifying-parquetformat)」を参照してください。ファイル ベースのストア間でファイルをそのままコピーする場合は (バイナリ コピー)、入力と出力の両方のデータセット定義で format セクションをスキップできます。| なし
| compression | データの圧縮の種類とレベルを指定します。サポートされる種類は、**GZip**、**Deflate**、**BZip2** です。サポートされるレベルは、**Optimal** と **Fastest** です。現時点では、**AvroFormat** と **OrcFormat** のデータの圧縮設定はサポートされていません。詳細については、「[圧縮のサポート](#compression-support)」を参照してください。 | なし |

> [AZURE.NOTE] bucketName をキーの組み合わせによって S3 オブジェクトの場所が指定されます。バケットは S3 オブジェクトのルート コンテナーであり、キーは、S3 オブジェクトへの完全パスです。

### プレフィックスを持つサンプル データセット

	{
	    "name": "dataset-s3",
	    "properties": {
	        "type": "AmazonS3",
	        "linkedServiceName": "link- testS3",
	        "typeProperties": {
	            "prefix": "testFolder/test",
	            "bucketName": "testbucket",
	            "format": {
	                "type": "OrcFormat"
	            }
	        },
			"availability": {
		    	"frequency": "Hour",
			    "interval": 1
		    },
			"external": true
	    }
	}

### サンプル データセット (バージョンあり)

	{
	    "name": "dataset-s3",
	    "properties": {
	        "type": "AmazonS3",
	        "linkedServiceName": "link- testS3",
	        "typeProperties": {
	            "key": "testFolder/test.orc",
	            "bucketName": "testbucket",
	            "version": "WBeMIxQkJczm0CJajYkHf0_k6LhBmkcL",
	            "format": {
	                "type": "OrcFormat"
	            }
	        },
			"availability": {
		    	"frequency": "Hour",
			    "interval": 1
		    },
			"external": true
	    }
	}


### S3 の動的パス

このサンプルでは、Amazon S3 データセット内のキーおよび bucketName プロパティの固定値を使用します。

	"key": "testFolder/test.orc",
	"bucketName": "testbucket",

実行時に、SliceStart などのシステム変数を使用して、Data Factory でキーと bucketName を動的に計算できます。

	"key": "$$Text.Format('{0:MM}/{0:dd}/test.orc', SliceStart)"
	"bucketName": "$$Text.Format('{0:yyyy}', SliceStart)"

同じことを、Amazon S3 データセットの prefix プロパティにも行うことができます。サポートされる関数と変数の一覧については、「[Data Factory の関数およびシステム変数](data-factory-functions-variables.md)」を参照してください。


[AZURE.INCLUDE [data-factory-file-format](../../includes/data-factory-file-format.md)]
[AZURE.INCLUDE [data-factory-compression](../../includes/data-factory-compression.md)]


## コピー アクティビティの type プロパティ

アクティビティの定義に利用できるセクションとプロパティの完全な一覧については、「[パイプラインの作成](data-factory-create-pipelines.md)」を参照してください。名前、説明、入力テーブル、出力テーブル、ポリシーなどのプロパティは、あらゆる種類のアクティビティで使用できます。

一方、アクティビティの **typeProperties** セクションで使用できるプロパティは、各アクティビティの種類によって異なります。コピー アクティビティの場合、ソースとシンクの種類によって異なります。

コピー アクティビティのソースの種類が **FileSystemSource** (Amazon S3を含む) である場合は、typeProperties セクションで次のプロパティを使用できます。

| プロパティ | 説明 | 使用できる値 | 必須 |
| -------- | ----------- | -------------- | -------- | 
| recursive | ディレクトリで S3 オブジェクトを再帰的に一覧表示するかどうかを指定します。 | true または false | なし | 

[AZURE.INCLUDE [data-factory-structure-for-rectangualr-datasets](../../includes/data-factory-structure-for-rectangualr-datasets.md)]

[AZURE.INCLUDE [data-factory-column-mapping](../../includes/data-factory-column-mapping.md)]

[AZURE.INCLUDE [data-factory-type-repeatability-for-relational-sources](../../includes/data-factory-type-repeatability-for-relational-sources.md)]

## パフォーマンスとチューニング  
Azure Data Factory でのデータ移動 (コピー アクティビティ) のパフォーマンスに影響する主な要因と、パフォーマンスを最適化するための各種方法については、「[コピー アクティビティのパフォーマンスとチューニングに関するガイド](data-factory-copy-activity-performance.md)」をご覧ください。

## 次のステップ
次の記事を参照してください。
- コピー アクティビティでパイプラインを作成するための詳細な手順については、[コピー アクティビティのチュートリアル](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)を参照してください。

<!---HONumber=AcomDC_0928_2016-->