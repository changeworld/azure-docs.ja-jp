<properties 
	pageTitle="PostgreSQL からデータを移動する | Azure Data Factory" 
	description="Azure Data Factory を使用して PostgreSQL Databases からデータを移動する方法を説明します。" 
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

# Azure Data Factory を使用して PostgreSQL からデータを移動する

この記事では、Azure Data Factory のコピー アクティビティを使用して、PostgreSQL と他のデータ ストアとの間でデータを移動する方法について説明します。この記事は、「[データ移動アクティビティ](data-factory-data-movement-activities.md)」という記事に基づき、コピー アクティビティによるデータ移動の一般概要とサポートされるデータ ストアの組み合わせについて紹介しています。

Data Factory のサービスでは、Data Management Gateway を使用したオンプレミスの PostgreSQL ソースへの接続をサポートします。Data Management Gateway の詳細およびゲートウェイの設定手順については、「[オンプレミスの場所とクラウド間のデータ移動](data-factory-move-data-between-onprem-and-cloud.md)」を参照してください。

> [AZURE.NOTE]
PostgreSQL データベースが Azure IaaS VM でホストされている場合でも、ゲートウェイは必須です。ゲートウェイはデータ ストアと同じ IaaS VM にインストールできるほか、ゲートウェイがデータベースに接続できれば別の VM にインストールしてもかまいません。

Data Factory は、PostgreSQL から他のデータ ストアへのデータ移動をサポートしますが、他のデータ ストアから PostgreSQL へのデータ移動はサポートしません。

## インストール 

Data Management Gateway で PostgreSQL Databases に接続するには、[PostgreSQL の Ngpsql データ プロバイダー](http://go.microsoft.com/fwlink/?linkid=282716)を Data Management Gateway と同じシステムにインストールする必要があります。

> [AZURE.NOTE] 接続/ゲートウェイに関する問題のトラブルシューティングのヒントについては、[ゲートウェイの問題のトラブルシューティング](data-factory-data-management-gateway.md#troubleshoot-gateway-issues)に関するセクションをご覧ください。

## データのコピー ウィザード
PostgreSQL データベースから、サポートされているシンク データ ストアにデータをコピーするパイプラインを作成する最も簡単な方法は、データのコピー ウィザードを使用することです。データのコピー ウィザードを使用してパイプラインを作成する簡単な手順については、「[チュートリアル: コピー ウィザードを使用してパイプラインを作成する](data-factory-copy-data-wizard-tutorial.md)」をご覧ください。

次の例は、[Azure ポータル](data-factory-copy-activity-tutorial-using-azure-portal.md)、[Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md)、または [Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md) を使用してパイプラインを作成する際に使用できるサンプルの JSON 定義です。これらの例は、PostgreSQL データベースから Azure BLOB ストレージにデータをコピーする方法を示しています。ただし、Azure Data Factory のコピー アクティビティを使用して、[こちら](data-factory-data-movement-activities.md#supported-data-stores)に記載されているシンクのいずれかにデータをコピーすることができます。

## サンプル: PostgreSQL から Azure BLOB にデータをコピーする

このサンプルは、PostgreSQL データベースから Azure BLOB ストレージにデータをコピーする方法を示します。Azure Data Factory のコピー アクティビティを使用して、[こちら](data-factory-data-movement-activities.md#supported-data-stores)に記載されているシンクのいずれかにデータを**直接**コピーすることもできます。
 
このサンプルでは、次の Data Factory のエンティティがあります。

1.	[OnPremisesPostgreSql](data-factory-onprem-postgresql-connector.md#postgresql-linked-service-properties) 型のリンクされたサービス。
2.	[AzureStorage](data-factory-azure-blob-connector.md#azure-storage-linked-service-properties) 型のリンクされたサービス。
3.	[RelationalTable](data-factory-onprem-postgresql-connector.md#postgresql-dataset-type-properties) 型の入力[データセット](data-factory-create-datasets.md)。
4.	[AzureBlob](data-factory-azure-blob-connector.md#azure-blob-dataset-type-properties) 型の出力[データセット](data-factory-create-datasets.md)。
4.	[RelationalSource](data-factory-onprem-postgresql-connector.md#postgresql-copy-activity-type-properties) と [BlobSink](data-factory-azure-blob-connector.md#azure-blob-copy-activity-type-properties) を使用するコピー アクティビティを含む[パイプライン](data-factory-create-pipelines.md)。

このサンプルは PostgreSQL データベースのクエリ結果のデータを BLOB に 1 時間ごとにコピーします。これらのサンプルで使用される JSON プロパティの説明はサンプルに続くセクションにあります。

最初の手順として、データ管理ゲートウェイを設定します。設定手順は、[オンプレミスの場所とクラウドの間でのデータ移動](data-factory-move-data-between-onprem-and-cloud.md)に関する記事に記載されています。

**PostgreSQL のリンクされたサービス:**

	{
	    "name": "OnPremPostgreSqlLinkedService",
	    "properties": {
	        "type": "OnPremisesPostgreSql",
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
	    "type": "AzureStorage",
	    "typeProperties": {
	      "connectionString": "DefaultEndpointsProtocol=https;AccountName=<AccountName>;AccountKey=<AccountKey>"
	    }
	  }
	}

**PostgreSQL の入力データセット:**

このサンプルでは、PostgreSQL で「MyTable」という名前のテーブルを作成し、時系列データ用に「timestamp」という名前の列が含まれているものと想定しています。

"external" を "true" に設定すると、データセットが Data Factory の外部にあり、Data Factory のアクティビティによって生成されたものではないことが Data Factory サービスに通知されます。

	{
	    "name": "PostgreSqlDataSet",
	    "properties": {
	        "type": "RelationalTable",
	        "linkedServiceName": "OnPremPostgreSqlLinkedService",
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

データは新しい BLOB に 1 時間おきに書き込まれます (頻度: 時間、間隔: 1)。BLOB のフォルダー パスとファイル名は、処理中のスライスの開始時間に基づき、動的に評価されます。フォルダー パスは開始時間の年、月、日、時刻の部分を使用します。

	{
	    "name": "AzureBlobPostgreSqlDataSet",
	    "properties": {
	        "type": "AzureBlob",
	        "linkedServiceName": "AzureStorageLinkedService",
	        "typeProperties": {
	            "folderPath": "mycontainer/postgresql/yearno={Year}/monthno={Month}/dayno={Day}/hourno={Hour}",
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


**コピー アクティビティ:**

パイプラインには、入力データセットと出力データセットを使用するように構成され、1 時間おきに実行するようにスケジュールされているコピー アクティビティが含まれています。パイプライン JSON 定義で、**source** 型が **RelationalSource** に設定され、**sink** 型が **BlobSink** に設定されています。**query** プロパティに指定された SQL クエリは、PostgreSQL データベースの public.usstates テーブルからデータを選択します。
	
	{
	    "name": "CopyPostgreSqlToBlob",
	    "properties": {
	        "description": "pipeline for copy activity",
	        "activities": [
	            {
	                "type": "Copy",
	                "typeProperties": {
	                    "source": {
	                        "type": "RelationalSource",
	                        "query": "select * from "public"."usstates""
	                    },
	                    "sink": {
	                        "type": "BlobSink"
	                    }
	                },
	                "inputs": [
	                    {
	                        "name": "PostgreSqlDataSet"
	                    }
	                ],
	                "outputs": [
	                    {
	                        "name": "AzureBlobPostgreSqlDataSet"
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
	                "name": "PostgreSqlToBlob"
	            }
	        ],
	        "start": "2014-06-01T18:00:00Z",
	        "end": "2014-06-01T19:00:00Z"
	    }
	}


## PostgreSQL のリンクされたサービスのプロパティ

次の表は、PostgreSQL のリンクされたサービスに固有の JSON 要素の説明をまとめたものです。

プロパティ | 説明 | 必須
-------- | ----------- | --------
type | type プロパティを **OnPremisesPostgreSql** に設定する必要があります | はい
server | PostgreSQL サーバーの名前です。 | はい 
database | PostgreSQL データベースの名前です。 | はい 
schema | データベース内のスキーマの名前です。スキーマ名は、大文字と小文字が区別されます。 | なし 
authenticationType | PostgreSQL データベースへの接続に使用される認証の種類です。Anonymous、Basic、Windows のいずれかの値になります。 | はい 
username | Basic または Windows 認証を使用している場合は、ユーザー名を指定します。 | いいえ 
パスワード | ユーザー名に指定したユーザー アカウントのパスワードを指定します。 | いいえ 
gatewayName | Data Factory サービスが、オンプレミスの PostgreSQL データベースへの接続に使用するゲートウェイの名前です。 | はい 

オンプレミスの PostgreSQL データ ソースの資格情報の設定について詳しくは、「[資格情報とセキュリティの設定](data-factory-move-data-between-onprem-and-cloud.md#set-credentials-and-security)」をご覧ください。

## PostgreSQL データセットの type プロパティ

データセットの定義に利用できるセクションとプロパティの完全な一覧については、「[データセットの作成](data-factory-create-datasets.md)」という記事を参照してください。データセット JSON の構造、可用性、ポリシーなどのセクションは、データセットのすべての型 (Azure SQL、Azure BLOB、Azure テーブルなど) でほぼ同じです。

typeProperties セクションはデータセット型ごとに異なり、データ ストアのデータの場所などに関する情報を提供します。**RelationalTable** 型のデータセット (PostgreSQL データセットを含む) の typeProperties セクションには次のプロパティがあります。

プロパティ | 説明 | 必須
-------- | ----------- | --------
tableName | リンクされたサービスが参照する PostgreSQL Databases インスタンスのテーブルの名前です。tableName は、大文字と小文字が区別されます。 | いいえ ( **RelationalSource** の **クエリ** が指定されている場合) 

## PostgreSQL のコピー アクティビティの type プロパティ

アクティビティの定義に利用できるセクションとプロパティの完全な一覧については、「[パイプラインの作成](data-factory-create-pipelines.md)」を参照してください。名前、説明、入力テーブル、出力テーブル、ポリシーなどのプロパティは、あらゆる種類のアクティビティで使用できます。

一方、アクティビティの typeProperties セクションで使用できるプロパティは、各アクティビティの種類によって異なります。コピー アクティビティの場合、ソースとシンクの種類によって異なります。

source の種類が **RelationalSource** (PostgreSQL を含む) である場合は、typeProperties セクションで次のプロパティを使用できます。

プロパティ | 説明 | 使用できる値 | 必須
-------- | ----------- | -------------- | --------
query | カスタム クエリを使用してデータを読み取ります。 | SQL クエリ文字列。例: "query": "select * from "MySchema"."MyTable"" | いいえ (**データセット**の **tableName** が指定されている場合)

> [AZURE.NOTE] スキーマとテーブルの名前は、大文字と小文字を区別します。クエリ内では、"" (二重引用符) で囲む必要があります。

**例:**

 "query": "select * from "MySchema"."MyTable""

[AZURE.INCLUDE [data-factory-structure-for-rectangualr-datasets](../../includes/data-factory-structure-for-rectangualr-datasets.md)]

## PostgreSQL の型マッピング

[データ移動アクティビティ](data-factory-data-movement-activities.md)に関する記事のとおり、コピー アクティビティは次の 2 段階のアプローチで型を source から sink に自動的に変換します。

1. ネイティブの source 型から .NET 型に変換する
1. .NET 型からネイティブの sink 型に変換する

PostgreSQL にデータを移動する場合、PostgreSQL 型から .NET 型に対する次のマッピングが使用されます。

PostgreSQL Databases 型 |	PostgreSQL エイリアス | .NET Framework 型
------------------------ | -------------------- | ---------------------
abstime | | Datetime
bigint | int8 | Int64
bigserial | serial8 | Int64
bit [ (n) ] | | Byte、String
bit varying [ (n) ] | varbit | Byte、String
boolean | bool | Boolean
box | | Byte, String
bytea | | Byte、String
character [ (n) ] | char [ (n) ] | String
character varying [ (n) ] | varchar [ (n) ] | String
cid | | String
cidr | | String
circle | | Byte, String
date | | Datetime
daterange | | String
double precision| float8 | Double
inet | | Byte, String
intarry | | String
int4range | | String
int8range | | String
integer | int, int4 | Int32
interval [ fields ] [ (p) ] | | Timespan
json | | String
jsonb | | Byte
line | | Byte, String
lseg | | Byte、String
macaddr | | Byte、String
money | | Decimal
numeric [ (p, s) ] | decimal [ (p, s) ] | Decimal
numrange | | String
oid | | Int32
path | | Byte, String
pg\_lsn | | Int64
point | | Byte、String
polygon | | Byte、String
real | float4 | Single
smallint | int2 | Int16
smallserial | serial2 | Int16
serial | serial4 | Int32
text | | String


[AZURE.INCLUDE [data-factory-column-mapping](../../includes/data-factory-column-mapping.md)]

[AZURE.INCLUDE [data-factory-type-repeatability-for-relational-sources](../../includes/data-factory-type-repeatability-for-relational-sources.md)]

## パフォーマンスとチューニング  
Azure Data Factory でのデータ移動 (コピー アクティビティ) のパフォーマンスに影響する主な要因と、パフォーマンスを最適化するための各種方法については、「[コピー アクティビティのパフォーマンスとチューニングに関するガイド](data-factory-copy-activity-performance.md)」をご覧ください。

<!---HONumber=AcomDC_0921_2016-->