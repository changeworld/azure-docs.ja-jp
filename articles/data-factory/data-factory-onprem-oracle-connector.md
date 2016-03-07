<properties 
	pageTitle="Oracle からのデータの移動 | Azure Data Factory" 
	description="Azure Data Factory を使用してオンプレミスの Oracle データベースとの間でデータを移動する方法を説明します。" 
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

# Azure Data Factory を使用してオンプレミスの Oracle からデータを移動する 

この記事では、データ ファクトリのコピー アクティビティを使用して Oracle から他のデータ ストアにデータを移動する方法について説明します。この記事は、「[データ移動アクティビティ](data-factory-data-movement-activities.md)」という記事に基づき、コピー アクティビティによるデータ移動の一般概要とサポートされるデータ ストアの組み合わせについて紹介しています。

## インストール 
Azure Data Factory サービスをオンプレミスの Oracle データベースに接続できるようにするには、次をインストールする必要があります。

- データベースをホストするコンピューターと同じコンピューター、またはデータベースとのリソースの競合を避けるために別のコンピューター上にインストールされた Data Management Gateway。Data Management Gateway は、安全かつ管理された方法でオンプレミスのデータをクラウド サービスに接続するソフトウェアです。Data Management Gateway の詳細については、[オンプレミスとクラウド間でのデータ移動](data-factory-move-data-between-onprem-and-cloud.md)に関する記事を参照してください。 
- [Oracle Data Access Components (ODAC) for Windows](http://www.oracle.com/technetwork/topics/dotnet/downloads/index.html)。これは、ゲートウェイがインストールされているホスト コンピューターにインストールする必要があります。

> [AZURE.NOTE] 接続/ゲートウェイに関する問題をトラブルシューティングするためのヒントについては、「[ゲートウェイのトラブルシューティング](data-factory-move-data-between-onprem-and-cloud.md#gateway-troubleshooting)」を参照してください。

## サンプル: Oracle から Azure BLOB にデータをコピーする
このサンプルは、オンプレミスの Oracle データベースから Azure BLOB ストレージにデータをコピーする方法を示します。ただし、Azure Data Factory のコピー アクティビティを使用して[ここ](data-factory-data-movement-activities.md#supported-data-stores)から開始したいずれかのシンクに、データを**直接**コピーすることができます。
 
このサンプルでは、次の Data Factory のエンティティがあります。

1.	[OnPremisesOracle](data-factory-onprem-oracle-connector.md#oracle-linked-service-properties) 型のリンクされたサービス。
2.	[AzureStorage](data-factory-azure-blob-connector.md#azure-storage-linked-service-properties) 型のリンクされたサービス。
3.	[OracleTable](data-factory-onprem-oracle-connector.md#oracle-dataset-type-properties) 型の入力[データセット](data-factory-create-datasets.md)。 
4.	[AzureBlob](data-factory-azure-blob-connector.md#azure-blob-dataset-type-properties) 型の出力[データセット](data-factory-create-datasets.md)。
5.	source として [OracleSource](data-factory-onprem-oracle-connector.md#oracle-copy-activity-type-properties) を、sink として [BlobSink](data-factory-azure-blob-connector.md#azure-blob-copy-activity-type-properties) を使用するコピー アクティビティを含む[パイプライン](data-factory-create-pipelines.md)。

このサンプルはオンプレミスの Oracle データベース内のテーブルから BLOB に 1 時間ごとにデータをコピーします。下のサンプルで使用されるさまざまなプロパティの詳細については、サンプルに続くセクションの各プロパティのドキュメントを参照してください。

**Oracle のリンクされたサービス:**

	{
	  "name": "OnPremisesOracleLinkedService",
	  "properties": {
	    "type": "OnPremisesOracle",
	    "typeProperties": {
	      "ConnectionString": "data source=<data source>;User Id=<User Id>;Password=<Password>;",
	      "gatewayName": "<gateway name>"
	    }
	  }
	}

**Azure BLOB ストレージのリンクされたサービス:**

	{
	  "name": "StorageLinkedService",
	  "properties": {
	    "type": "AzureStorage",
	    "typeProperties": {
	      "connectionString": "DefaultEndpointsProtocol=https;AccountName=<account name>;AccountKey=<Account key>"
	    }
	  }
	}

**Oracle の入力データセット:**

このサンプルでは、Oracle で「MyTable」という名前のテーブルを作成し、時系列データ用に「timestampcolumn」という名前の列が含まれているものと想定しています。

“external”: ”true” を設定して externalData ポリシーを指定すると、これがデータ ファクトリに対して外部にあるテーブルで、データ ファクトリのアクティビティでは生成されていないことが Data Factory に通知されます。

	{
	    "name": "OracleInput",
	    "properties": {
	        "type": "OracleTable",
	        "linkedServiceName": "OnPremisesOracleLinkedService",
	        "typeProperties": {
	            "tableName": "MyTable"
	        },
	           "external": true,
	        "availability": {
	            "offset": "01:00:00",
	            "interval": "1",
	            "anchorDateTime": "2014-02-27T12:00:00",
	            "frequency": "Hour"
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

データは新しい BLOB に 1 時間おきに書き込まれます (頻度: 時間、間隔: 1)。BLOB のフォルダー パスとファイル名は、処理中のスライスの開始時間に基づき、動的に評価されます。フォルダー パスは開始時間の年、月、日、時刻の部分を使用します。
	
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

上記の入力データセットと出力データセットを使用するように構成され、1 時間おきに実行するようにスケジュールされているコピー アクティビティがパイプラインに含まれています。パイプライン JSON 定義で、**source** 型が **RelationalSource** に設定され、**sink** 型が **BlobSink** に設定されています。**oracleReaderQuery** プロパティに指定されている SQL クエリは過去のデータを選択してコピーします。

	
	{  
	    "name":"SamplePipeline",
	    "properties":{  
	    "start":"2014-06-01T18:00:00",
	    "end":"2014-06-01T19:00:00",
	    "description":"pipeline for copy activity",
	    "activities":[  
	      {
	        "name": "AzureSQLtoBlob",
	        "description": "copy activity",
	        "type": "Copy",
	        "inputs": [
	          {
	            "name": " OracleInput"
	          }
	        ],
	        "outputs": [
	          {
	            "name": "AzureBlobOutput"
	          }
	        ],
	        "typeProperties": {
	          "source": {
	            "type": "OracleSource",
	            "oracleReaderQuery": "$$Text.Format('select * from MyTable where timestampcolumn >= \\'{0:yyyy-MM-dd HH:mm}\\' AND timestampcolumn < \\'{1:yyyy-MM-dd HH:mm}\\'', WindowStart, WindowEnd)"
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


Oracle データベースでの日付の構成方法に基づいて、クエリ文字列を調整する必要があります。次のようなエラー メッセージが表示されることがあります。

	Message=Operation failed in Oracle Database with the following error: 'ORA-01861: literal does not match format string'.,Source=,''Type=Oracle.DataAccess.Client.OracleException,Message=ORA-01861: literal does not match format string,Source=Oracle Data Provider for .NET,'.

(to\_date 関数を使用して) 次のようにクエリを変更する必要があります。

	"oracleReaderQuery": "$$Text.Format('select * from MyTable where timestampcolumn >= to_date(\\'{0:MM-dd-yyyy HH:mm}\\',\\'MM/DD/YYYY HH24:MI\\')  AND timestampcolumn < to_date(\\'{1:MM-dd-yyyy HH:mm}\\',\\'MM/DD/YYYY HH24:MI\\') ', WindowStart, WindowEnd)"


## Oracle のリンクされたサービスのプロパティ

次の表は、Oracle のリンクされたサービスに固有の JSON 要素の説明をまとめたものです。

プロパティ | 説明 | 必須
-------- | ----------- | --------
type | type プロパティを **OnPremisesOracle** に設定する必要があります | はい
connectionString | connectionString プロパティの Oracle Database インスタンスに接続するために必要な情報を指定します。 | はい 
gatewayName | オンプレミスの Oracle サーバーへの接続に使用されるゲートウェイの名前です | はい

オンプレミスの Oracle データ ソースの資格情報の設定について詳しくは、「[資格情報とセキュリティの設定](data-factory-move-data-between-onprem-and-cloud.md#setting-credentials-and-security)」をご覧ください。
## Oracle データセットの type プロパティ

データセットの定義に利用できるセクションとプロパティの完全な一覧については、「[データセットの作成](data-factory-create-datasets.md)」という記事を参照してください。データセット JSON の構造、可用性、ポリシーなどのセクションはすべてのデータセット型 (Oracle、Azure BLOB、Azure テーブルなど) で同じです。
 
typeProperties セクションはデータセット型ごとに異なり、データ ストアのデータの場所などに関する情報を提供します。OracleTable 型のデータセットの typeProperties セクションには次のプロパティがあります。

プロパティ | 説明 | 必須
-------- | ----------- | --------
tableName | リンクされたサービスが参照する Oracle データベース インスタンスのテーブルの名前です。 | いいえ (**SqlSource** の **oracleReaderQuery** が指定されている場合)

## Oracle のコピー アクティビティの type プロパティ

アクティビティの定義に利用できるセクションとプロパティの完全な一覧については、「[パイプラインの作成](data-factory-create-pipelines.md)」という記事を参照してください。名前、説明、入力テーブル、出力テーブル、さまざまなポリシーなどのプロパティがあらゆる種類のアクティビティで利用できます。

**注:** コピー アクティビティは入力を 1 つだけ受け取り、出力を 1 つだけ生成します。

一方で、アクティビティの typeProperties セクションで利用できるプロパティはアクティビティの種類により異なり、コピー アクティビティの場合、source と sink の種類によって異なります。

コピー アクティビティで、source の種類が **OracleSource** である場合は、**typeProperties** セクションで次のプロパティを使用できます。

プロパティ | 説明 |使用できる値 | 必須
-------- | ----------- | ------------- | --------
oracleReaderQuery | カスタム クエリを使用してデータを読み取ります。 | SQL クエリ文字列。 
例: select * from MyTable <p>指定されていない場合に実行される SQL ステートメント: select * from MyTable</p> | いいえ (**dataset**の **tableName** が指定されている場合)

[AZURE.INCLUDE [data-factory-structure-for-rectangualr-datasets](../../includes/data-factory-structure-for-rectangualr-datasets.md)]

### Oracle の型マッピング

「[データ移動アクティビティ](data-factory-data-movement-activities.md)」の記事のとおり、コピー アクティビティは次の 2 段階のアプローチで型を source から sink に自動的に変換します。

1. ネイティブの source 型から .NET 型に変換する
2. .NET 型からネイティブの sink 型に変換する

Oracle からデータを移動すると、Oracle データ型から .NET 型へのマッピング (およびその逆) に、次のマッピングが使用されます。

Oracle データ型 | .NET Framework データ型
---------------- | ------------------------
BFILE | Byte
BLOB | Byte
CHAR | String
CLOB | String
DATE | DateTime
FLOAT | Decimal
INTEGER | Decimal
INTERVAL YEAR TO MONTH | Int32
INTERVAL DAY TO SECOND | TimeSpan
LONG | String
LONG RAW | Byte
NCHAR | String
NCLOB | String
NUMBER | Decimal
NVARCHAR2 | String
RAW | Byte
ROWID | String
TIMESTAMP | DateTime
TIMESTAMP WITH LOCAL TIME ZONE | DateTime
TIMESTAMP WITH TIME ZONE | DateTime
符号なし INTEGER | Number
VARCHAR2 | String
XML | String

## トラブルシューティングのヒント

****問題: ** 次の**エラー メッセージ**が表示される: コピー アクティビティに次の無効なパラメーターがあります: 'UnknownParameterName'、詳細メッセージ: 要求された .Net Framework Data Provider が見つかりません。インストールされていない可能性があります。

**考えられる原因**

1. Oracle 用の .NET Framework Data Provider がインストールされていません。
2. Oracle 用の .NET Framework Data Provider が .NET Framework 2.0 にインストールされ、.NET Framework 4.0 フォルダーで検出されません。 

**解決/回避策**

1. Oracle 用の .NET Provider をインストールしていない場合は[インストール](http://www.oracle.com/technetwork/topics/dotnet/utilsoft-086879.html)した後、シナリオをやり直します。 
2. プロバイダーをインストールしてもエラー メッセージが表示される場合は、次の操作を行います。 
	1. 次のフォルダーから .NET 2.0 のコンピューター構成を開きます。<system disk>:\\Windows\\Microsoft.NET\\Framework64\\v2.0.50727\\CONFIG\\machine.config
	2. **Oracle Data Provider for .NET** を探します。**system.data** の **DbProviderFactories** の下に次のようなエントリを見つけることができます。“<add name="Oracle Data Provider for .NET" invariant="Oracle.DataAccess.Client" description="Oracle Data Provider for .NET" type="Oracle.DataAccess.Client.OracleClientFactory, Oracle.DataAccess, Version=2.112.3.0, Culture=neutral, PublicKeyToken=89b483f429c47342" />”
2.	このエントリを、v4.0 フォルダーの machine.config ファイル (<system disk>: \\Windows\\Microsoft.NET\\Framework64\\v4.0.30319\\Config\\machine.config) にコピーし、バージョンを 4.xxx.x.x に変更します。
3.	“gacutil /i [プロバイダーのパス]” を実行して、“<ODP.NET Installed Path>\\11.2.0\\client\_1\\odp.net\\bin\\4\\Oracle.DataAccess.dll” をグローバル アセンブリ キャッシュ (GAC) にインストールします。



[AZURE.INCLUDE [data-factory-column-mapping](../../includes/data-factory-column-mapping.md)]

<!---HONumber=AcomDC_0224_2016-->