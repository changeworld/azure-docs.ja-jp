<properties 
	pageTitle="Sybase からデータを移動する | Azure Data Factory" 
	description="Azure Data Factory を使用して Sybase データベースからデータを移動する方法を説明します。" 
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
	ms.date="02/01/2016" 
	ms.author="spelluru"/>

# Azure Data Factory を使用して Sybase からデータを移動する 

この記事では、Azure Data Factory のコピー アクティビティを使用して、Sybase と他のデータ ストアとの間でデータを移動する方法について説明します。この記事は、「[データ移動アクティビティ](data-factory-data-movement-activities.md)」という記事に基づき、コピー アクティビティによるデータ移動の一般概要とサポートされるデータ ストアの組み合わせについて紹介しています。

Data Factory のサービスでは、Data Management Gateway を使用したオンプレミスの Sybase ソースへの接続をサポートします。Data Management Gateway の詳細およびゲートウェイの設定手順については、「[オンプレミスの場所とクラウド間のデータ移動](data-factory-move-data-between-onprem-and-cloud.md)」を参照してください。

**注:** Azure IaaS VM でホストされている場合でも、Sybase への接続にゲートウェイを利用する必要があります。クラウドでホストされている Sybase のインスタンスに接続しようとしている場合は、IaaS VM でゲートウェイ インスタンスをインストールすることもできます。

Data Factory は、他のデータ ストアから Sybase へのデータの移動ではなく、Sybase から他のデータ ストアへのデータの移動のみをサポートします。

## インストール

Data Management Gateway で Sybase データベースに接続するには、[Sybase のデータ プロバイダー](http://go.microsoft.com/fwlink/?linkid=324846)を Data Management Gateway と同じシステムにインストールする必要があります。

> [AZURE.NOTE] 接続/ゲートウェイに関する問題をトラブルシューティングするためのヒントについては、「[ゲートウェイのトラブルシューティング](data-factory-move-data-between-onprem-and-cloud.md#gateway-troubleshooting)」を参照してください。

## サンプル: Sybase から Azure BLOB にデータをコピーする
このサンプルは、Sybase データベースから Azure BLOB ストレージにデータをコピーする方法を示します。ただし、Azure Data Factory のコピー アクティビティを使用して[ここ](data-factory-data-movement-activities.md#supported-data-stores)から開始したいずれかのシンクに、データを**直接**コピーすることができます。
 
このサンプルでは、次の Data Factory のエンティティがあります。

1.	[OnPremisesSybase](data-factory-onprem-sybase-connector.md#sybase-linked-service-properties) 型のリンクされたサービス。
2.	[AzureStorage](data-factory-azure-blob-connector.md#azure-storage-linked-service-properties) 型のリンクされたサービス。
3.	[RelationalTable](data-factory-onprem-sybase-connector.md#sybase-dataset-type-properties) 型の入力[データセット](data-factory-create-datasets.md)。
4.	[AzureBlob](data-factory-azure-blob-connector.md#azure-blob-dataset-type-properties) 型の出力[データセット](data-factory-create-datasets.md)。
4.	[RelationalSource](data-factory-onprem-sybase-connector.md#sybase-copy-activity-type-properties) と [BlobSink](data-factory-azure-blob-connector.md#azure-blob-copy-activity-type-properties) を使用するコピー アクティビティを含む[パイプライン](data-factory-create-pipelines.md)。

このサンプルは Sybase データベースのクエリ結果のデータを BLOB に 1 時間ごとにコピーします。これらのサンプルで使用される JSON プロパティの説明はサンプルに続くセクションにあります。

最初の手順として、「[オンプレミスの場所とクラウド間のデータ移動](data-factory-move-data-between-onprem-and-cloud.md)」の指示に従って Data Management Gateway を設定してください。

**Sybase のリンクされたサービス:**

	{
	    "name": "OnPremSybaseLinkedService",
	    "properties": {
	        "type": "OnPremisesSybase",
	        "typeProperties": {
	            "server": "<server>",
	            "database": "<database>",
	            "schema": "<schema>",
	            "authenticationType": "<authentication type>",
	            "username": "<username>",
	            "password": "<password>",
	            "gatewayName": "<gatewayName>"
	        }
	    }
	}

**Azure BLOB ストレージのリンクされたサービス:**

	{
	    "name": "AzureStorageLinkedService",
	    "properties": {
	        "type": "AzureStorageLinkedService",
			"typeProperties": {
	        	"connectionString": "DefaultEndpointsProtocol=https;AccountName=<AccountName>;AccountKey=<AccountKey>"
			}
	    }
	}


**Sybase の入力データセット:**

このサンプルでは、Sybase で「MyTable」という名前のテーブルを作成し、時系列データ用に「timestamp」という名前の列が含まれているものと想定しています。

「“external”: true」を設定して externalData ポリシーを指定すると、このテーブルが Data Factory に対して外部にあるテーブルで、 Data Factory のアクティビティでは生成されていないことが Data Factory に通知されます。リンクされたサービスの**型**は **RelationalTable** に設定されることに注意してください。
	
	{
	    "name": "SybaseDataSet",
	    "properties": {
	        "type": "RelationalTable",
	        "linkedServiceName": "OnPremSybaseLinkedService",
	        "typeProperties": {},
	        "availability": {
	            "frequency": "Hour",
	            "interval": 1
	        },
			"external": true,
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
	    "name": "AzureBlobSybaseDataSet",
	    "properties": {
	        "type": "AzureBlob",
	        "linkedServiceName": "AzureStorageLinkedService",
	        "typeProperties": {
	            "folderPath": "mycontainer/sybase/yearno={Year}/monthno={Month}/dayno={Day}/hourno={Hour}",
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
	            ]
	        },
	        "availability": {
	            "frequency": "Hour",
	            "interval": 1
	        }
	    }
	}


**コピー アクティビティのあるパイプライン:**

上記の入力データセットと出力データセットを使用するように構成され、1 時間おきに実行するようにスケジュールされているコピー アクティビティがパイプラインに含まれています。パイプライン JSON 定義で、**source** 型が **RelationalSource** に設定され、**sink** 型が **BlobSink** に設定されています。**query** プロパティに指定された SQL クエリは、データベースの DBA.Orders テーブルからデータを選択します。


	{
	    "name": "CopySybaseToBlob",
	    "properties": {
	        "description": "pipeline for copy activity",
	        "activities": [
	            {
	                "type": "Copy",
	                "typeProperties": {
	                    "source": {
	                        "type": "RelationalSource",
	                        "query": "select * from DBA.Orders"
	                    },
	                    "sink": {
	                        "type": "BlobSink"
	                    }
	                },
	                "inputs": [
	                    {
	                        "name": "SybaseDataSet"
	                    }
	                ],
	                "outputs": [
	                    {
	                        "name": "AzureBlobSybaseDataSet"
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
	                "name": "SybaseToBlob"
	            }
	        ],
	        "start": "2014-06-01T18:00:00Z",
	        "end": "2014-06-01T19:00:00Z"
	    }
	}


## Sybase のリンクされたサービスのプロパティ

次の表は、Sybase のリンクされたサービスに固有の JSON 要素の説明をまとめたものです。

プロパティ | 説明 | 必須
-------- | ----------- | --------
type | type プロパティを **OnPremisesSybase** に設定する必要があります | はい
server | Sybase サーバーの名前です。 | はい
database | Sybase データベースの名前です。 | はい 
schema | データベース内のスキーマの名前です。 | いいえ
authenticationType | Sybase データベースへの接続に使用される認証の種類です。Anonymous、Basic、Windows のいずれかの値になります。 | はい
username | Basic または Windows 認証を使用している場合は、ユーザー名を指定します。 | いいえ
パスワード | ユーザー名に指定したユーザー アカウントのパスワードを指定します。 | いいえ
gatewayName | Data Factory サービスが、オンプレミスの Sybase データベースへの接続に使用するゲートウェイの名前です。 | はい 

オンプレミスの Sybase データ ソースの資格情報の設定について詳しくは、「[資格情報とセキュリティの設定](data-factory-move-data-between-onprem-and-cloud.md#set-credentials-and-security)」をご覧ください。

## Sybase データセットの type プロパティ

データセットの定義に利用できるセクションとプロパティの完全な一覧については、「[データセットの作成](data-factory-create-datasets.md)」という記事を参照してください。データセット JSON の構造、可用性、ポリシーなどのセクションはすべてのデータセット型 (Azure SQL、Azure BLOB、Azure テーブルなど) で同じです。

typeProperties セクションはデータセット型ごとに異なり、データ ストアのデータの場所などに関する情報を提供します。**RelationalTable** 型のデータセット (Sybase データセットを含む) の **typeProperties** セクションには次のプロパティがあります。

プロパティ | 説明 | 必須
-------- | ----------- | --------
tableName | リンクされたサービスが参照する Sybase データベース インスタンスのテーブルの名前です。 | いいえ ( **RelationalSource** の **クエリ** が指定されている場合)

## Sybase のコピー アクティビティの type プロパティ 

アクティビティの定義に利用できるセクションとプロパティの完全な一覧については、「[パイプラインの作成](data-factory-create-pipelines.md)」という記事を参照してください。名前、説明、入力テーブル、出力テーブル、さまざまなポリシーなどのプロパティがあらゆる種類のアクティビティで利用できます。

一方で、アクティビティの typeProperties セクションで利用できるプロパティはアクティビティの種類により異なり、コピー アクティビティの場合、source と sink の種類によって異なります。

コピー アクティビティで、source の種類が **RelationalSource** (Sybase を含む) である場合は、**typeProperties** セクションで次のプロパティを使用できます。

プロパティ | 説明 | 使用できる値 | 必須
-------- | ----------- | -------------- | --------
query | カスタム クエリを使用してデータを読み取ります。 | SQL クエリ文字列。例: Select * from MyTable。 | いいえ (**データセット**の **tableName** が指定されている場合)

[AZURE.INCLUDE [data-factory-structure-for-rectangualr-datasets](../../includes/data-factory-structure-for-rectangualr-datasets.md)]

## Sybase の型マッピング

「データ移動アクティビティ」の記事のとおり、コピー アクティビティは次の 2 段階のアプローチで型を source から sink に自動的に変換します。

1. ネイティブの source 型から .NET 型に変換する
2. .NET 型からネイティブの sink 型に変換する

Sybase では、T-SQL と T-SQL 型をサポートします。sql 型から .NET 型のマッピング テーブルについては、「[Azure SQL コネクタ](data-factory-azure-sql-connector.md)」の記事を参照してください。

[AZURE.INCLUDE [data-factory-column-mapping](../../includes/data-factory-column-mapping.md)]

[AZURE.INCLUDE [data-factory-type-repeatability-for-relational-sources](../../includes/data-factory-type-repeatability-for-relational-sources.md)]

<!---HONumber=AcomDC_0316_2016-->