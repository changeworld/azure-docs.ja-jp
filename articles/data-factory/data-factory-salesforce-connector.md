<properties 
	pageTitle="Data Factory を使用して Salesforce からデータを移動する | Microsoft Azure " 
	description="Azure Data Factory を使用して Salesforce からデータを移動する方法を説明します。" 
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
	ms.date="07/07/2016" 
	ms.author="spelluru"/>

# Azure Data Factory を使用して Salesforce からデータを移動する
この記事では、Azure Data Factory のコピー アクティビティ を使用して、Salesforce から、[サポートされているソースとシンク](data-factory-data-movement-activities.md#supported-data-stores) に関するセクションのシンクの欄に一覧表示されているデータ ストアにデータをコピーする方法について説明します。この記事は、「[データ移動アクティビティ](data-factory-data-movement-activities.md)」という記事に基づき、コピー アクティビティによるデータ移動の一般概要とサポートされるデータ ストアの組み合わせについて紹介しています。

現在のところ、Data Factory は、Salesforce から [サポートされているシンク データ ストア](data-factory-data-movement-activities.md#supported-data-stores) へのデータの移動のみに対応しており、他のデータ ストアから Salesforce にデータを移動することはできません。

## 前提条件
- Developer Edition、Professional Edition、Enterprise Edition、Unlimited Edition のいずれかのエディションを使用する必要があります。
- API アクセス許可を有効にする必要があります。「[How do I enable API access in Salesforce by permission set? (権限セットで Salesforce の API アクセスを有効にする方法)](https://www.data2crm.com/migration/faqs/enable-api-access-salesforce-permission-set/)」を参照してください。
- Salesforce からオンプレミスのデータ ストアにデータをコピーするには、バージョン 2.0 以降のData Management Gateway がオンプレミス環境にインストールされている必要があります。

## データのコピー ウィザード
Salesforceから、サポートされているシンク データ ストアにデータをコピーするパイプラインを作成する最も簡単な方法は、データのコピー ウィザードを使用することです。データのコピー ウィザードを使用してパイプラインを作成する簡単な手順については、「[チュートリアル: コピー ウィザードを使用してパイプラインを作成する](data-factory-copy-data-wizard-tutorial.md)」を参照してください。

次の例は、[Azure ポータル](data-factory-copy-activity-tutorial-using-azure-portal.md)、[Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md)、または [Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md) を使用してパイプラインを作成する際に使用できるサンプルの JSON 定義です。

## サンプル: Salesforce から Azure BLOB にデータをコピーする
このサンプルでは、1 時間おきに Salesforce　から Azure BLOB にデータがコピーされます。これらのサンプルで使用される JSON プロパティの説明はサンプルに続くセクションにあります。Azure Data Factory のコピー アクティビティを使用して、[データ移動アクティビティ](data-factory-data-movement-activities.md#supported-data-stores)に関するトピックで説明されているシンクのいずれかにデータを直接コピーすることができます。

- [Salesforce](#salesforce-linked-service-properties)型のリンクされたサービス。
- [AzureStorage](data-factory-azure-blob-connector.md#azure-storage-linked-service-properties) 型のリンクされたサービス。
- [RelationalTable](#salesforce-dataset-properties) 型の入力[データセット](data-factory-create-datasets.md)。
- [AzureBlob](data-factory-azure-blob-connector.md#azure-blob-dataset-type-properties) 型の出力[データセット](data-factory-create-datasets.md)。
- [RelationalSource](#relationalsource-type-properties) と [BlobSink](data-factory-azure-blob-connector.md#azure-blob-copy-activity-type-properties) を使用するコピー アクティビティを含む[パイプライン](data-factory-create-pipelines.md)。

**Salesforce のリンクされたサービス**

この例では、**Salesforce** のリンクされたサービスを使用します。このリンクされたサービスでサポートされているプロパティについては、[Salesforce のリンクされたサービス](#salesforce-linked-service-properties) に関するセクションを参照してください。 セキュリティ トークンのリセット/取得方法については、[セキュリティ トークンの取得](https://help.salesforce.com/apex/HTViewHelpDoc?id=user_security_token.htm)に関する記事を参照してください。

	{
		"name": "SalesforceLinkedService",
		"properties":
		{
			"type": "Salesforce",
			"typeProperties":
			{
				"username": "<user name>",
				"password": "<password>",
				"securityToken": "<security token>"
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

**Salesforce の入力データセット**

	{
		"name": "SalesforceInput",
		"properties": {
			"linkedServiceName": "SalesforceLinkedService",
			"type": "RelationalTable",
			"typeProperties": {
				"tableName": "AllDataType__c"  
			},
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

**external** を **true** に設定すると、データセットが Data Factory に対して外部にあり、Data Factory のアクティビティでは生成されていないことが Data Factory のサービスに通知されます。

> [AZURE.IMPORTANT]  カスタム オブジェクトには、API 名の "\_\_c" の部分が必要となります。

![Data Factory - Salesforce の接続 - API 名](media/data-factory-salesforce-connector/data-factory-salesforce-api-name.png)

**Azure BLOB の出力データセット**

データは新しい BLOB に 1 時間おきに書き込まれます (頻度: 時間、間隔: 1)。

	{
	    "name": "AzureBlobOutput",
	    "properties":
	    {
	        "type": "AzureBlob",
	        "linkedServiceName": "AzureStorageLinkedService",
	        "typeProperties":
	        {
	            "folderPath": "adfgetstarted/alltypes_c"
	        },
	        "availability":
	        {
	            "frequency": "Hour",
	            "interval": 1
	        }
	    }
	}


**コピー アクティビティのあるパイプライン**

上記の入力データセットと出力データセットを使用するように構成され、1 時間おきに実行するようにスケジュールされているコピー アクティビティがパイプラインに含まれています。パイプライン JSON 定義で、**source** 型が **RelationalSource** に設定され、**sink** 型が **BlobSink** に設定されています。

RelationalSource でサポートされるプロパティの一覧については、「[RelationalSource type プロパティ](#relationalsource-type-properties)」を参照してください。
	
	{  
		"name":"SamplePipeline",
		"properties":{  
			"start":"2016-06-01T18:00:00",
			"end":"2016-06-01T19:00:00",
			"description":"pipeline with copy activity",
			"activities":[  
			{
				"name": "SaleforceToAzureBlob",
				"description": "Copy from Salesforce to an Azure blob",
				"type": "Copy",
				"inputs": [
				{
					"name": "SalesforceInput"
				}
				],
				"outputs": [
				{
					"name": "AzureBlobOutput"
				}
				],
				"typeProperties": {
					"source": {
						"type": "RelationalSource",
						"query": "SELECT Id, Col_AutoNumber__c, Col_Checkbox__c, Col_Currency__c, Col_Date__c, Col_DateTime__c, Col_Email__c, Col_Number__c, Col_Percent__c, Col_Phone__c, Col_Picklist__c, Col_Picklist_MultiSelect__c, Col_Text__c, Col_Text_Area__c, Col_Text_AreaLong__c, Col_Text_AreaRich__c, Col_URL__c, Col_Text_Encrypt__c, Col_Lookup__c FROM AllDataType__c"				
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

> [AZURE.IMPORTANT]  カスタム オブジェクトには、API 名の "\_\_c" の部分が必要となります。

![Data Factory - Salesforce の接続 - API 名](media/data-factory-salesforce-connector/data-factory-salesforce-api-name-2.png)

## Salesforce のリンクされたサービスのプロパティ

次の表は、Salesforce のリンクされたサービスに固有の JSON 要素の説明をまとめたものです。

| プロパティ | 説明 | 必須 |
| -------- | ----------- | -------- | 
| type | type プロパティを **Salesforce** に設定する必要があります。 | はい | 
| username |ユーザー アカウントのユーザー名を指定します。 | はい |
| パスワード | ユーザー アカウントのパスワードを指定します。 | はい |
| securityToken | ユーザー アカウントのセキュリティ トークンを指定します。セキュリティ トークンのリセット/取得方法については、[セキュリティ トークンの取得](https://help.salesforce.com/apex/HTViewHelpDoc?id=user_security_token.htm) に関する記事を参照してください。セキュリティ トークンの概要については、「[Security and the API (セキュリティと API)](https://developer.salesforce.com/docs/atlas.ja-JP.api.meta/api/sforce_api_concepts_security.htm)」を参照してください。 | はい | 

## Salesforce データセットのプロパティ

データセットの定義に利用できるセクションとプロパティの完全な一覧については、「[データセットの作成](data-factory-create-datasets.md)」という記事を参照してください。データセット JSON の構造、可用性、ポリシーなどのセクションはすべてのデータセット型 (Azure SQL、Azure BLOB、Azure テーブルなど) で同じです。

**typeProperties** セクションはデータセット型ごとに異なり、データ ストアのデータの場所などに関する情報を提供します。**RelationalTable** 型のデータセットの typeProperties セクションには次のプロパティがあります。

| プロパティ | 説明 | 必須 |
| -------- | ----------- | -------- |
| tableName | Salesforce のテーブル名。 | いいえ ( **RelationalSource** の **クエリ** が指定されている場合) | 

> [AZURE.IMPORTANT]  カスタム オブジェクトには、API 名の "\_\_c" の部分が必要となります。

![Data Factory - Salesforce の接続 - API 名](media/data-factory-salesforce-connector/data-factory-salesforce-api-name.png)

## RelationalSource type プロパティ
アクティビティの定義に利用できるセクションとプロパティの完全な一覧については、「[パイプラインの作成](data-factory-create-pipelines.md)」という記事を参照してください。名前、説明、入力テーブル、出力テーブル、さまざまなポリシーなどのプロパティがあらゆる種類のアクティビティで利用できます。

一方で、アクティビティの typeProperties セクションで利用できるプロパティはアクティビティの種類により異なり、コピー アクティビティの場合、source と sink の種類によって異なります。

コピー アクティビティで、source の種類が **RelationalSource** (Salesforce を含む) である場合は、typeProperties セクションで次のプロパティを使用できます。

| プロパティ | 説明 | 使用できる値 | 必須 |
| -------- | ----------- | -------------- | -------- |
| query | カスタム クエリを使用してデータを読み取ります。 | SQL-92 クエリまたは [Salesforce オブジェクト クエリ言語 (SOQL) ](https://developer.salesforce.com/docs/atlas.ja-JP.soql_sosl.meta/soql_sosl/sforce_api_calls_soql.htm) クエリ。例: select * from MyTable\_\_c | いいえ (**dataset**の **tableName** が指定されている場合) |

> [AZURE.IMPORTANT]  カスタム オブジェクトには、API 名の "\_\_c" の部分が必要となります。<br> 日付/時刻列の句を含むクエリの指定には SOQL を使用します。 例：$$Text.Format('SELECT Id, Type, Name, BillingCity, BillingCountry FROM Account WHERE LastModifiedDate >= {0:yyyy-MM-ddTHH:mm:ssZ} AND LastModifiedDate < {1:yyyy-MM-ddTHH:mm:ssZ}', WindowStart, WindowEnd)

![Data Factory - Salesforce の接続 - API 名](media/data-factory-salesforce-connector/data-factory-salesforce-api-name-2.png)

## Salesforce の要求の制限
Salesforce では、API 要求数の合計と、API の同時要求数に上限が設けられています。詳細については、[Salesforce API 要求の制限](http://resources.docs.salesforce.com/200/20/ja-JP/sfdc/pdf/salesforce_app_limits_cheatsheet.pdf)に関する資料の「**API Request Limits (API 要求の制限)**」セクションを参照してください。
 
同時要求数が上限を超えると調整が発生し、ランダムにエラーが表示されます。要求数の合計が上限を超えると、Salesforce アカウントが 24 時間ブロックされます。また、どちらの場合も、 "REQUEST\_LIMIT\_EXCEEDED" エラーが表示されることがあります。
 

[AZURE.INCLUDE [data-factory-structure-for-rectangualr-datasets](../../includes/data-factory-structure-for-rectangualr-datasets.md)]

### Salesforce の型マッピング
Salesforce の型 | .Net ベースの型
--------------- | ---------------
オート ナンバー | String
チェックボックス | Boolean
通貨 | Double
日付 | DateTime
日付/時刻 | DateTime
電子メール | String
ID | String
参照リレーションシップ | String
複数選択の候補リスト | String
Number | Double
Percent | Double
電話 | String
候補リスト | String
テキスト | String
テキスト領域 | String
テキスト領域 (ロング) | String
テキスト領域 (リッチ) | String
テキスト (暗号化) | String
URL | String

[AZURE.INCLUDE [data-factory-column-mapping](../../includes/data-factory-column-mapping.md)]
[AZURE.INCLUDE [data-factory-structure-for-rectangualr-datasets](../../includes/data-factory-structure-for-rectangualr-datasets.md)]

## パフォーマンスとチューニング  
Azure Data Factory でのデータ移動 (コピー アクティビティ) のパフォーマンスに影響する主な要因と、パフォーマンスを最適化するための各種方法については、「[コピー アクティビティのパフォーマンスとチューニングに関するガイド](data-factory-copy-activity-performance.md)」を参照してください。

<!---HONumber=AcomDC_0720_2016-->