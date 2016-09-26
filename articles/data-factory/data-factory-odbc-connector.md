<properties 
	pageTitle="ODBC データ ストアからデータを移動する |Azure Data Factory" 
	description="Azure Data Factory を使用して ODBC データ ストアからデータを移動する方法を説明します。" 
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
	ms.date="09/12/2016" 
	ms.author="spelluru"/>

# Azure Data Factory を使用して ODBC データ ストアからデータを移動する
この記事では、Azure Data Factory のコピー アクティビティを使用して、オンプレミスの ODBC データ ストアから他のデータ ストアにデータを移動する方法について説明します。この記事は、「[データ移動アクティビティ](data-factory-data-movement-activities.md)」という記事に基づき、コピー アクティビティによるデータ移動の一般概要とサポートされるデータ ストアの組み合わせについて紹介しています。

Data Factory は、現在、オンプレミスの ODBCデータ ストアから他のデータ ストアへのデータの移動のみをサポートします。他のデータ ストアからオンプレミスの ODBC データ ストアへのデータの移動はサポートされていません。


## 接続を有効にする
Data Factory のサービスでは、Data Management Gateway を使用したオンプレミスの ODBC ソースへの接続をサポートします。Data Management Gateway の詳細およびゲートウェイの設定手順については、「[オンプレミスの場所とクラウド間のデータ移動](data-factory-move-data-between-onprem-and-cloud.md)」を参照してください。Azure IaaS VM でホストされている場合でも、ODBC データ ストアへの接続にゲートウェイを使用します。

ODBC データ ストアとして、同じオンプレミスのコンピューターまたは Azure VM にゲートウェイをインストールできます。ただし、リソースの競合を防ぎ、パフォーマンスの向上を図るために、別のコンピューターまたは Azure IaaS VM にゲートウェイをインストールすることをお勧めします。別のマシンにゲートウェイをインストールすると、そのマシンが ODBC データ ストアを持つマシンにアクセスできるようになります。

Data Management Gateway とは別に、ゲートウェイ マシン上にデータ ストア用の ODBC ドライバーもインストールする必要があります。

> [AZURE.NOTE] 接続/ゲートウェイに関する問題のトラブルシューティングのヒントについては、[ゲートウェイの問題のトラブルシューティング](data-factory-data-management-gateway.md#troubleshoot-gateway-issues)に関するセクションをご覧ください。

## データのコピー ウィザード
ODBC ソースとの間でデータをコピーするパイプラインを作成する最も簡単な方法は、データのコピー ウィザードを使用することです。データのコピー ウィザードを使用してパイプラインを作成する簡単な手順については、「[チュートリアル: コピー ウィザードを使用してパイプラインを作成する](data-factory-copy-data-wizard-tutorial.md)」をご覧ください。

以下の例は、[Azure ポータル](data-factory-copy-activity-tutorial-using-azure-portal.md)、[Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md)、または [Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md) を使用してパイプラインを作成する際に使用できるサンプルの JSON 定義です。これらの例は、ODBC ソースから Azure BLOB ストレージにデータをコピーする方法を示しています。ただし、Azure Data Factory のコピー アクティビティを使用して、[こちら](data-factory-data-movement-activities.md#supported-data-stores)に記載されているシンクのいずれかにデータをコピーすることができます。


## サンプル: ODBC データ ストアから Azure BLOB にデータをコピーする

このサンプルは、ODBC データ ストアから Azure BLOB ストレージにデータをコピーする方法を示します。Azure Data Factory のコピー アクティビティを使用して、[こちら](data-factory-data-movement-activities.md#supported-data-stores)に記載されているシンクのいずれかにデータを**直接**コピーすることもできます。
 
このサンプルでは、次の Data Factory のエンティティがあります。

1.	[OnPremisesOdbc](#odbc-linked-service-properties) 型のリンクされたサービス。
2.	[AzureStorage](data-factory-azure-blob-connector.md#azure-storage-linked-service-properties) 型のリンクされたサービス。
3.	[RelationalTable](#odbc-dataset-type-properties) 型の入力[データセット](data-factory-create-datasets.md)。
4.	[AzureBlob](data-factory-azure-blob-connector.md#azure-blob-dataset-type-properties) 型の出力[データセット](data-factory-create-datasets.md)。
4.	[RelationalSource](#odbc-copy-activity-type-properties) と [BlobSink](data-factory-azure-blob-connector.md#azure-blob-copy-activity-type-properties) を使用するコピー アクティビティを含む[パイプライン](data-factory-create-pipelines.md)。

このサンプルは ODBC データ ストアのクエリ結果のデータを BLOB に 1 時間ごとにコピーします。これらのサンプルで使用される JSON プロパティの説明はサンプルに続くセクションにあります。

最初の手順として、データ管理ゲートウェイを設定します。設定手順は、[オンプレミスの場所とクラウドの間でのデータ移動](data-factory-move-data-between-onprem-and-cloud.md)に関する記事に記載されています。

**ODBC のリンクされたサービス**: この例では基本認証を使用しています。使用できるさまざまな種類の認証については、[ODBC のリンクされたサービス](#odbc-linked-service-properties)に関するセクションをご覧ください。

	{
	    "name": "OnPremOdbcLinkedService",
	    "properties":
	    {
	        "type": "OnPremisesOdbc",
	        "typeProperties":
	        {
	            "authenticationType": "Basic",
	            "connectionString": "Driver={SQL Server};Server=Server.database.windows.net; Database=TestDatabase;",
	            "userName": "username",
	            "password": "password",
	            "gatewayName": "mygateway"
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

**ODBC の入力データセット**

このサンプルでは、ODBC データベースで「MyTable」という名前のテーブルを作成し、時系列データ用に「timestampcolumn」という名前の列が含まれているものと想定しています。

”external” を ”true” に設定すると、データセットが Data Factory の外部にあり、Data Factory のアクティビティによって生成されたものではないことが Data Factory サービスに通知されます。
	
	{
	    "name": "ODBCDataSet",
	    "properties": {
	        "published": false,
	        "type": "RelationalTable",
	        "linkedServiceName": "OnPremOdbcLinkedService",
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
	    "name": "AzureBlobOdbcDataSet",
	    "properties": {
	        "type": "AzureBlob",
	        "linkedServiceName": "AzureStorageLinkedService",
	        "typeProperties": {
	            "folderPath": "mycontainer/odbc/yearno={Year}/monthno={Month}/dayno={Day}/hourno={Hour}",
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

パイプラインには、この入力データセットと出力データセットを使用するように構成され、1 時間おきに実行するようにスケジュールされているコピー アクティビティが含まれています。パイプライン JSON 定義で、**source** 型が **RelationalSource** に設定され、**sink** 型が **BlobSink** に設定されています。**query** プロパティに指定されている SQL クエリは過去のデータを選択してコピーします。
	
	{
	    "name": "CopyODBCToBlob",
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
	                        "name": "OdbcDataSet"
	                    }
	                ],
	                "outputs": [
	                    {
	                        "name": "AzureBlobOdbcDataSet"
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
	                "name": "OdbcToBlob"
	            }
	        ],
	        "start": "2014-06-01T18:00:00Z",
	        "end": "2014-06-01T19:00:00Z"
	    }
	}



## ODBC のリンクされたサービスのプロパティ

次の表は、ODBC のリンクされたサービスに固有の JSON 要素の説明をまとめたものです。

| プロパティ | 説明 | 必須 |
| -------- | ----------- | -------- | 
| type | type プロパティを **OnPremisesOdbc** に設定する必要があります。 | はい |
| connectionString | 接続文字列の非アクセス資格情報部分と省略可能な暗号化された資格情報。次のセクションの例を参照してください。 | はい
| 資格情報 | ドライバー固有のプロパティ値の形式で指定された接続文字列のアクセス資格情報の部分。例: "Uid=<ユーザー ID>;Pwd=<パスワード>;RefreshToken=<シークレット更新トークン >;" | なし
| authenticationType | ODBC データ ストアへの接続に使用される認証の種類です。Anonymous と Basic のいずれかの値になります。 | はい | 
| username | 基本認証を使用している場合は、ユーザー名を指定します。 | いいえ | 
| パスワード | ユーザー名に指定したユーザー アカウントのパスワードを指定します。 | いいえ | 
| gatewayName | Data Factory サービスが、ODBC データ ストアへの接続に使用するゲートウェイの名前。 | はい |


オンプレミスの ODBC データ ストアの資格情報の設定について詳しくは、[資格情報とセキュリティの設定](data-factory-move-data-between-onprem-and-cloud.md#set-credentials-and-security)に関する記事をご覧ください。

### 基本認証を使用する

	{
	    "name": "odbc",
	    "properties":
	    {
	        "type": "OnPremisesOdbc",
	        "typeProperties":
	        {
	            "authenticationType": "Basic",
	            "connectionString": "Driver={SQL Server};Server=Server.database.windows.net; Database=TestDatabase;",
	            "userName": "username",
	            "password": "password",
	            "gatewayName": "mygateway"
	        }
	    }
	}

### 暗号化された資格情報で基本認証を使用する
[New-AzureRMDataFactoryEncryptValue](https://msdn.microsoft.com/library/mt603802.aspx) (Azure PowerShell のバージョン 1.0) コマンドレットまたは [New-AzureDataFactoryEncryptValue](https://msdn.microsoft.com/library/dn834940.aspx) (Azure PowerShell のバージョン 0.9 以前) を使用して資格情報を暗号化することができます。

	{
	    "name": "odbc",
	    "properties":
	    {
	        "type": "OnPremisesOdbc",
	        "typeProperties":
	        {
	            "authenticationType": "Basic",
	            "connectionString": "Driver={SQL Server};Server=myserver.database.windows.net; Database=TestDatabase;;EncryptedCredential=eyJDb25uZWN0...........................",
	            "gatewayName": "mygateway"
	        }
	    }
	}


### 匿名認証を使用する

	{
	    "name": "odbc",
	    "properties":
	    {
	        "type": "OnPremisesOdbc",
	        "typeProperties":
	        {
	            "authenticationType": "Anonymous",
	            "connectionString": "Driver={SQL Server};Server={servername}.database.windows.net; Database=TestDatabase;",
	            "credential": "UID={uid};PWD={pwd}",
	            "gatewayName": "mygateway"
	        }
	    }
	}



## ODBC データセットの type プロパティ

データセットの定義に利用できるセクションとプロパティの完全な一覧については、「[データセットの作成](data-factory-create-datasets.md)」という記事を参照してください。データセット JSON の構造、可用性、ポリシーなどのセクションは、データセットのすべての型 (Azure SQL、Azure BLOB、Azure テーブルなど) でほぼ同じです。

**typeProperties** セクションはデータセット型ごとに異なり、データ ストアのデータの場所などに関する情報を提供します。**RelationalTable** 型のデータセット (ODBC データセットを含む) の typeProperties セクションには次のプロパティがあります。

| プロパティ | 説明 | 必須 |
| -------- | ----------- | -------- |
| tableName | ODBC データ ストア内のテーブルの名前。 | はい | 

## ODBC のコピー アクティビティの type プロパティ

アクティビティの定義に利用できるセクションとプロパティの完全な一覧については、「[パイプラインの作成](data-factory-create-pipelines.md)」を参照してください。名前、説明、入力テーブル、出力テーブル、ポリシーなどのプロパティは、あらゆる種類のアクティビティで使用できます。

一方、アクティビティの **typeProperties** セクションで使用できるプロパティは、各アクティビティの種類によって異なります。コピー アクティビティの場合、ソースとシンクの種類によって異なります。

コピー アクティビティで、source が **RelationalSource** 型 (ODBC を含む) の場合は、typeProperties セクションで次のプロパティを使用できます。

| プロパティ | 説明 | 使用できる値 | 必須 |
| -------- | ----------- | -------------- | -------- |
| query | カスタム クエリを使用してデータを読み取ります。 | SQL クエリ文字列。例: Select * from MyTable。 | はい | 

[AZURE.INCLUDE [data-factory-structure-for-rectangualr-datasets](../../includes/data-factory-structure-for-rectangualr-datasets.md)]

### ODBC の型マッピング

[データ移動アクティビティ](data-factory-data-movement-activities.md)に関する記事のとおり、コピー アクティビティは次の 2 段階のアプローチで型を source から sink に自動的に変換します。

1. ネイティブの source 型から .NET 型に変換する
2. .NET 型からネイティブの sink 型に変換する

「[ODBC データ型のマッピング](https://msdn.microsoft.com/library/cc668763.aspx)」で説明するように、ODBC データ ストアからデータを移動する際に、ODBC データ型は .NET 型にマップされます。


[AZURE.INCLUDE [data-factory-column-mapping](../../includes/data-factory-column-mapping.md)]

[AZURE.INCLUDE [data-factory-type-repeatability-for-relational-sources](../../includes/data-factory-type-repeatability-for-relational-sources.md)]

## GE Historian ストア
次の例に示すように、[GE Proficy Historian (現在は GE Historian)](http://www.geautomation.com/products/proficy-historian) データ ストアを Azure Data Factory にリンクする、ODBC のリンクされたサービスを作成します。

	{
	    "name": "HistorianLinkedService",
	    "properties":
	    {
	        "type": "OnPremisesOdbc",
	        "typeProperties":
	        {
			    "connectionString": "DSN=<name of the GE Historian store>",
			    "gatewayName": "<gateway name>",
			    "authenticationType": "Basic",
			    "userName": "<user name>",
			    "password": "<password>"
	        }
	    }
	}

オンプレミス コンピューターに Data Management Gateway をインストールし、ゲートウェイをポータルに登録します。オンプレミス コンピューターにインストールされているゲートウェイは、GE Historian データ ストアに接続するために GE Historian 用の ODBC ドライバーを使用します。そのため、ゲートウェイ コンピューターにまだドライバーがインストールされていない場合は、インストールしてください。詳細については、「[接続を有効にする](#enabling-connectivity)」をご覧ください。

Data Factory ソリューションで GE Historian ストアを使用する前に、ゲートウェイが次のセクションの手順を使用してデータ ストアに接続できるかどうかを確認します。

コピー操作で ODBC データ ストアをソース データ ストアとして使用する方法の詳細については、記事を最初からお読みください。

## 接続の問題のトラブルシューティング
接続の問題をトラブルシューティングするには、**Data Management Gateway 構成マネージャー**の **[診断]** タブを使用します。

1. **Data Management Gateway 構成マネージャー**を起動します。"C:\\Program Files\\Microsoft Data Management Gateway\\1.0\\Shared\\ConfigManager.exe" を直接実行することも、次の図に示すように、**Gateway** を検索して **Microsoft Data Management Gateway** アプリケーションへのリンクを見つけることもできます。

	![Search gateway](./media/data-factory-odbc-connector/search-gateway.png)
2. **[診断]** タブに切り替えます。

	![Gateway diagnostics](./media/data-factory-odbc-connector/data-factory-gateway-diagnostics.png)
3. データ ストアの**種類** (リンクされたサービス) を選択します。
4. **認証**方法を指定し、**資格情報**を入力します。または、データ ストアへの接続に使用する**接続文字列**を入力します。
5. **[テスト接続]** をクリックして、データ ストアへの接続をテストします。

## パフォーマンスとチューニング  
Azure Data Factory でのデータ移動 (コピー アクティビティ) のパフォーマンスに影響する主な要因と、パフォーマンスを最適化するための各種方法については、「[コピー アクティビティのパフォーマンスとチューニングに関するガイド](data-factory-copy-activity-performance.md)」をご覧ください。

<!---HONumber=AcomDC_0914_2016-->