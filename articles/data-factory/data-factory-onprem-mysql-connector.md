<properties 
	pageTitle="MySQL からデータを移動する | Azure Data Factory" 
	description="Azure Data Factory を使用して MySQL データベースからデータを移動する方法を説明します。" 
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
	ms.date="09/20/2016" 
	ms.author="spelluru"/>

# Azure Data Factory を使用して MySQL からデータを移動する

この記事では、Azure Data Factory のコピー アクティビティを使用して、MySQL と他のデータ ストアとの間でデータを移動する方法について説明します。この記事は、「[データ移動アクティビティ](data-factory-data-movement-activities.md)」という記事に基づき、コピー アクティビティによるデータ移動の一般概要とサポートされるデータ ストアの組み合わせについて紹介しています。

Data Factory のサービスでは、Data Management Gateway を使用したオンプレミスの MySQL ソースへの接続をサポートします。Data Management Gateway の詳細およびゲートウェイの設定手順については、「[オンプレミスの場所とクラウド間のデータ移動](data-factory-move-data-between-onprem-and-cloud.md)」を参照してください。

> [AZURE.NOTE] MySQL データベースが Azure IaaS 仮想マシン (VM) でホストされている場合でも、ゲートウェイは必須です。ゲートウェイはデータ ストアと同じ VM にインストールできるほか、ゲートウェイがデータベースに接続できれば別の VM にインストールしてもかまいません。

データ ファクトリは、他のデータ ストアから MySQL へのデータの移動ではなく、MySQL から他のデータ ストアへのデータの移動のみをサポートします。

## インストール 
Data Management Gateway で MySQL Databases に接続するには、[MySQL コネクタ/Net 6.6.5 for Microsoft Windows](http://go.microsoft.com/fwlink/?LinkId=278885) を Data Management Gateway と同じシステムにインストールする必要があります。

> [AZURE.NOTE] 接続/ゲートウェイに関する問題のトラブルシューティングのヒントについては、[ゲートウェイの問題のトラブルシューティング](data-factory-data-management-gateway.md#troubleshoot-gateway-issues)に関するセクションをご覧ください。

## データのコピー ウィザード
MySQL データベースから、サポートされているシンク データ ストアにデータをコピーするパイプラインを作成する最も簡単な方法は、データのコピー ウィザードを使用することです。データのコピー ウィザードを使用してパイプラインを作成する簡単な手順については、「[チュートリアル: コピー ウィザードを使用してパイプラインを作成する](data-factory-copy-data-wizard-tutorial.md)」をご覧ください。

次の例は、パイプラインを作成する際に使用できるサンプルの JSON 定義です。手順が記載された完全なチュートリアルについては、[オンプレミスの場所とクラウドの間でのデータの移動](data-factory-move-data-between-onprem-and-cloud.md)に関する記事を参照してください。Azure Data Factory のコピー アクティビティを使用して、[こちら](data-factory-data-movement-activities.md#supported-data-stores)に記載されているシンクのいずれかにデータをコピーすることができます。

## サンプル: MySQLから Azure BLOB にデータをコピーする
このサンプルは、オンプレミスの MySQL データベースから Azure BLOB ストレージにデータをコピーする方法を示します。Azure Data Factory のコピー アクティビティを使用して、[こちら](data-factory-data-movement-activities.md#supported-data-stores)に記載されているシンクのいずれかにデータを**直接**コピーすることもできます。

> [AZURE.IMPORTANT] このサンプルでは、JSON のスニペットを使用します。データ ファクトリを作成する手順は含まれてません。手順については、記事「[Data Management Gateway を使用してオンプレミスのソースとクラウドの間でデータを移動する](data-factory-move-data-between-onprem-and-cloud.md)」を参照してください。
 
このサンプルでは、次の Data Factory のエンティティがあります。

1.	[OnPremisesMySql](data-factory-onprem-mysql-connector.md#mysql-linked-service-properties) 型のリンクされたサービス。
2.	[AzureStorage](data-factory-azure-blob-connector.md#azure-storage-linked-service-properties) 型のリンクされたサービス。
3.	[RelationalTable](data-factory-onprem-mysql-connector.md#mysql-dataset-type-properties) 型の入力[データセット](data-factory-create-datasets.md)。
4.	[AzureBlob](data-factory-azure-blob-connector.md#azure-blob-dataset-type-properties) 型の出力[データセット](data-factory-create-datasets.md)。
4.	[RelationalSource](data-factory-onprem-mysql-connector.md#mysql-copy-activity-type-properties) と [BlobSink](data-factory-azure-blob-connector.md#azure-blob-copy-activity-type-properties) を使用するコピー アクティビティを含む[パイプライン](data-factory-create-pipelines.md)。

このサンプルは MySQL データベースのクエリ結果のデータを BLOB に 1 時間ごとにコピーします。これらのサンプルで使用される JSON プロパティの説明はサンプルに続くセクションにあります。

最初の手順として、データ管理ゲートウェイを設定します。設定手順は、[オンプレミスの場所とクラウドの間でのデータ移動](data-factory-move-data-between-onprem-and-cloud.md)に関する記事に記載されています。

**MySQL のリンクされたサービス**

	{
	  "name": "OnPremMySqlLinkedService",
	  "properties": {
	    "type": "OnPremisesMySql",
	    "typeProperties": {
	      "server": "<server name>",
	      "database": "<database name>",
	      "schema": "<schema name>",
	      "authenticationType": "<authentication type>",
	      "userName": "<user name>",
	      "password": "<password>",
	      "gatewayName": "<gateway>"
	    }
	  }
	}

**Azure ストレージのリンクされたサービス**

	{
	  "name": "AzureStorageLinkedService",
	  "properties": {
	    "type": "AzureStorage",
	    "typeProperties": {
	      "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
	    }
	  }
	}

**MySQL の入力データセット**

このサンプルでは、MySQL で「MyTable」という名前のテーブルを作成し、時系列データ用に「timestampcolumn」という名前の列が含まれているものと想定しています。

"external": true の設定により、このテーブルが Data Factory の外部にあり、Data Factory のアクティビティによって生成されたものではないことが Data Factory サービスに通知されます。
	
	{
	    "name": "MySqlDataSet",
	    "properties": {
	        "published": false,
	        "type": "RelationalTable",
	        "linkedServiceName": "OnPremMySqlLinkedService",
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



**Azure BLOB の出力データセット**

データは新しい BLOB に 1 時間おきに書き込まれます (頻度: 時間、間隔: 1)。BLOB のフォルダー パスは、処理中のスライスの開始時間に基づき、動的に評価されます。フォルダー パスは開始時間の年、月、日、時刻の部分を使用します。

	{
	    "name": "AzureBlobMySqlDataSet",
	    "properties": {
	        "type": "AzureBlob",
	        "linkedServiceName": "AzureStorageLinkedService",
	        "typeProperties": {
	            "folderPath": "mycontainer/mysql/yearno={Year}/monthno={Month}/dayno={Day}/hourno={Hour}",
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
	    "name": "CopyMySqlToBlob",
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
	                        "name": "MySqlDataSet"
	                    }
	                ],
	                "outputs": [
	                    {
	                        "name": "AzureBlobMySqlDataSet"
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
	                "name": "MySqlToBlob"
	            }
	        ],
	        "start": "2014-06-01T18:00:00Z",
	        "end": "2014-06-01T19:00:00Z"
	    }
	}



## MySQL のリンクされたサービスのプロパティ

次の表は、MySQL のリンクされたサービスに固有の JSON 要素の説明をまとめたものです。

| プロパティ | 説明 | 必須 |
| -------- | ----------- | -------- | 
| type | type プロパティを **OnPremisesMySql** に設定する必要があります | はい |
| server | MySQL サーバーの名前です。 | はい |
| database | MySQL データベースの名前です。 | はい | 
| schema | データベース内のスキーマの名前です。 | いいえ | 
| authenticationType | MySQL データベースへの接続に使用される認証の種類です。Anonymous、Basic、Windows のいずれかの値になります。 | はい | 
| username | Basic または Windows 認証を使用している場合は、ユーザー名を指定します。 | いいえ | 
| パスワード | ユーザー名に指定したユーザー アカウントのパスワードを指定します。 | いいえ | 
| gatewayName | Data Factory サービスが、オンプレミスの MySQL データベースへの接続に使用するゲートウェイの名前です。 | はい |

オンプレミスの MySQL データ ソースの資格情報の設定について詳しくは、「[資格情報とセキュリティの設定](data-factory-move-data-between-onprem-and-cloud.md#set-credentials-and-security)」をご覧ください。

## MySQL データセットの type プロパティ

データセットの定義に利用できるセクションとプロパティの完全な一覧については、「[データセットの作成](data-factory-create-datasets.md)」という記事を参照してください。データセット JSON の構造、可用性、ポリシーなどのセクションは、データセットのすべての型 (Azure SQL、Azure BLOB、Azure テーブルなど) でほぼ同じです。

**typeProperties** セクションはデータセット型ごとに異なり、データ ストアのデータの場所などに関する情報を提供します。**RelationalTable** 型のデータセット (MySQL データセットを含む) の typeProperties セクションには次のプロパティがあります

| プロパティ | 説明 | 必須 |
| -------- | ----------- | -------- |
| tableName | リンクされたサービスが参照する MySQL Databases インスタンスのテーブルの名前です。 | いいえ ( **RelationalSource** の **クエリ** が指定されている場合) | 

## MySQL のコピー アクティビティの type プロパティ

アクティビティの定義に利用できるセクションとプロパティの完全な一覧については、「[パイプラインの作成](data-factory-create-pipelines.md)」を参照してください。名前、説明、入力テーブル、出力テーブル、ポリシーなどのプロパティは、あらゆる種類のアクティビティで使用できます。

一方、アクティビティの **typeProperties** セクションで使用できるプロパティは、各アクティビティの種類によって異なります。コピー アクティビティの場合、ソースとシンクの種類によって異なります。

コピー アクティビティの source の種類が **RelationalSource** (MySQL を含む) である場合は、typeProperties セクションで次のプロパティを使用できます。

| プロパティ | 説明 | 使用できる値 | 必須 |
| -------- | ----------- | -------------- | -------- |
| query | カスタム クエリを使用してデータを読み取ります。 | SQL クエリ文字列。例: Select * from MyTable。 | いいえ (**データセット**の **tableName** が指定されている場合) | 

[AZURE.INCLUDE [data-factory-structure-for-rectangualr-datasets](../../includes/data-factory-structure-for-rectangualr-datasets.md)]

### MySQL の型マッピング

[データ移動アクティビティ](data-factory-data-movement-activities.md)に関する記事のとおり、コピー アクティビティは次の 2 段階のアプローチで型を source から sink に自動的に変換します。

1. ネイティブの source 型から .NET 型に変換する
2. .NET 型からネイティブの sink 型に変換する

MySQL にデータを移動する場合、MySQL 型から .NET 型に対する次のマッピングが使用されます。

| MySQL Databases 型 | .NET Framework 型 |
| ------------------- | ------------------- | 
| 符号なしの bigint | Decimal |
| bigint | Int64 |
| bit | Decimal |
| BLOB | Byte |
| bool | Boolean | 
| char | String |
| date | Datetime |
| datetime | Datetime |
| Decimal | Decimal |
| double precision | Double |
| double | Double |
| enum | String |
| float | Single |
| 符号なしの int | Int64 |
| int | Int32 |
| 符号なしの integer | Int64 |
| integer | Int32 | 
| long varbinary | Byte |
| long varchar | String |
| longblob | Byte |
| longtext | String | 
| mediumblob | 	Byte | 
| 符号なしの mediumint | Int64 |
| mediumint | Int32 | 
| mediumtext | String |
| numeric | Decimal |
| real | Double |
| set | String |
| 符号なしの smallint | Int32 |
| smallint | Int16 |
| text | String |
| time | TimeSpan |
| timestamp | Datetime |
| tinyblob | Byte |
| 符号なしの tinyint | Int16 | 
| tinyint | Int16 |
| tinytext | String | 
| varchar | String | 
| year | Int | 


[AZURE.INCLUDE [data-factory-column-mapping](../../includes/data-factory-column-mapping.md)]

[AZURE.INCLUDE [data-factory-type-repeatability-for-relational-sources](../../includes/data-factory-type-repeatability-for-relational-sources.md)]

## パフォーマンスとチューニング  
Azure Data Factory でのデータ移動 (コピー アクティビティ) のパフォーマンスに影響する主な要因と、パフォーマンスを最適化するための各種方法については、「[コピー アクティビティのパフォーマンスとチューニングに関するガイド](data-factory-copy-activity-performance.md)」をご覧ください。

<!---HONumber=AcomDC_0921_2016-->