---
title: "Data Factory を使用して Salesforce からデータを移動する | Microsoft Docs"
description: "Azure Data Factory を使用して Salesforce からデータを移動する方法を説明します。"
services: data-factory
documentationcenter: 
author: linda33wj
manager: jhubbard
editor: monicar
ms.assetid: dbe3bfd6-fa6a-491a-9638-3a9a10d396d1
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/22/2016
ms.author: jingwang
translationtype: Human Translation
ms.sourcegitcommit: 9e70638af1ecdd0bf89244b2a83cd7a51d527037
ms.openlocfilehash: 98b841e300d5b704d134bcfab0968523f3b9c3f0


---
# <a name="move-data-from-salesforce-by-using-azure-data-factory"></a>Azure Data Factory を使用して Salesforce からデータを移動する
この記事では、Azure Data Factory のコピー アクティビティを使用して、Salesforce から、 [サポートされているソースとシンク](data-factory-data-movement-activities.md#supported-data-stores-and-formats) に関する表のシンクの欄に一覧表示されているデータ ストアにデータをコピーする方法について説明します。 この記事は、「 [データ移動アクティビティ](data-factory-data-movement-activities.md) 」という記事に基づき、コピー アクティビティによるデータ移動の一般概要とサポートされるデータ ストアの組み合わせについて紹介しています。

現在のところ、Azure Data Factory は、Salesforce から、[サポートされているシンク データ ストア](data-factory-data-movement-activities.md#supported-data-stores-and-formats) へのデータの移動のみに対応しており、他のデータ ストアから Salesforce にデータを移動することはできません。

## <a name="supported-versions"></a>サポートされているバージョン
このコネクタは、Salesforce の Developer Edition、Professional Edition、Enterprise Edition、Unlimited Edition の各エディションをサポートしています。 また、Salesforce 運用、サンドボックス、およびカスタム ドメインからのコピーをサポートしています。

## <a name="prerequisites"></a>前提条件
* API アクセス許可を有効にする必要があります。 「 [How do I enable API access in Salesforce by permission set? (権限セットで Salesforce の API アクセスを有効にする方法)](https://www.data2crm.com/migration/faqs/enable-api-access-salesforce-permission-set/)
* Salesforce からオンプレミスのデータ ストアにデータをコピーするには、バージョン 2.0 以降の Data Management Gateway がオンプレミス環境にインストールされている必要があります。

## <a name="salesforce-request-limits"></a>Salesforce の要求の制限
Salesforce では、API 要求数の合計と、API の同時要求数に上限が設けられています。 詳細については、[Salesforce Developer の制限](http://resources.docs.salesforce.com/200/20/en-us/sfdc/pdf/salesforce_app_limits_cheatsheet.pdf)に関する資料の「API Request Limits (API 要求の制限)」をご覧ください。 同時要求の数が制限を超えると、調整が行われてランダムなエラーが発生します。要求の合計数が制限を超えると、Salesforce アカウントは 24 時間のあいだブロックされます。また、どちらのシナリオにおいても “REQUEST_LIMIT_EXCEEDED“ エラーが発生することがあります。

## <a name="copy-data-wizard"></a>データのコピー ウィザード
Salesforce から、サポートされているシンク データ ストアにデータをコピーするパイプラインを作成する最も簡単な方法は、データのコピー ウィザードを使用することです。 データのコピー ウィザードを使用してパイプラインを作成する簡単な手順については、「 [チュートリアル: コピー ウィザードを使用してパイプラインを作成する](data-factory-copy-data-wizard-tutorial.md) 」を参照してください。

次の例は、[Azure Portal](data-factory-copy-activity-tutorial-using-azure-portal.md)、[Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md)、または [Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md) を使用してパイプラインを作成する際に使用できるサンプルの JSON 定義です。 これらの例は、Salesforce から Azure BLOB ストレージにデータをコピーする方法を示しています。 ただし、Azure Data Factory のコピー アクティビティを使用して、 [こちら](data-factory-data-movement-activities.md#supported-data-stores-and-formats) に記載されているシンクのいずれかにデータをコピーすることができます。   

## <a name="sample-copy-data-from-salesforce-to-an-azure-blob"></a>サンプル: Salesforce から Azure BLOB にデータをコピーする
このサンプルでは、1 時間おきに Salesforce から Azure BLOB にデータがコピーされます。 これらのサンプルで使用される JSON プロパティの説明はサンプルに続くセクションにあります。 Azure Data Factory のコピー アクティビティを使用して、 [データ移動アクティビティ](data-factory-data-movement-activities.md#supported-data-stores-and-formats) に関する記事に記載されているシンクのいずれかにデータを直接コピーすることができます。

このシナリオの実現にあたって作成する必要のある Data Factory のアーティファクトは次のとおりです。 これらの手順については、箇条書きの後に続く各セクションで詳しく説明します。

* [Salesforce](#salesforce-linked-service-properties)
* [AzureStorage](data-factory-azure-blob-connector.md#azure-storage-linked-service)
* [RelationalTable](#salesforce-dataset-properties) 型の入力[データセット](data-factory-create-datasets.md)
* [AzureBlob](data-factory-azure-blob-connector.md#azure-blob-dataset-type-properties) 型の出力[データセット](data-factory-create-datasets.md)
* [RelationalSource](#relationalsource-type-properties) と [BlobSink](data-factory-azure-blob-connector.md#azure-blob-copy-activity-type-properties) を使用するコピー アクティビティを含む[パイプライン](data-factory-create-pipelines.md)

**Salesforce のリンクされたサービス**

この例では、 **Salesforce** のリンクされたサービスを使用します。 このリンクされたサービスでサポートされているプロパティについては、 [Salesforce のリンクされたサービス](#salesforce-linked-service-properties) に関するセクションをご覧ください。  セキュリティ トークンのリセット/取得方法については、 [セキュリティ トークンの取得](https://help.salesforce.com/apex/HTViewHelpDoc?id=user_security_token.htm) に関する記事をご覧ください。

```JSON
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
```
**Azure Storage のリンクされたサービス**

```JSON
{
    "name": "AzureStorageLinkedService",
    "properties": {
    "type": "AzureStorage",
    "typeProperties": {
        "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
    }
    }
}
```
**Salesforce の入力データセット**

```JSON
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
```

**external** を **true** に設定すると、データセットが Data Factory の外部にあり、Data Factory のアクティビティによって生成されたものではないことが Data Factory サービスに通知されます。

> [!IMPORTANT]
> カスタム オブジェクトには、API 名の "__c" の部分が必要となります。
>
>

![Data Factory - Salesforce の接続 - API 名](media/data-factory-salesforce-connector/data-factory-salesforce-api-name.png)

**Azure BLOB の出力データセット**

データは新しい BLOB に 1 時間おきに書き込まれます (頻度: 時間、間隔: 1)。

```JSON
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
```

**コピー アクティビティのあるパイプライン**

上記の入力データセットと出力データセットを使用するように構成され、1 時間おきに実行するようにスケジュールされているコピー アクティビティがパイプラインに含まれています。 パイプライン JSON 定義で、**source** の型が **RelationalSource** に設定され、**sink** の型が **BlobSink** に設定されています。

RelationalSource でサポートされるプロパティの一覧については、「 [RelationalSource type プロパティ](#relationalsource-type-properties) 」をご覧ください。

```JSON
{  
    "name":"SamplePipeline",
    "properties":{  
        "start":"2016-06-01T18:00:00",
        "end":"2016-06-01T19:00:00",
        "description":"pipeline with copy activity",
        "activities":[  
        {
            "name": "SalesforceToAzureBlob",
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
```
> [!IMPORTANT]
> カスタム オブジェクトには、API 名の "__c" の部分が必要となります。
>

![Data Factory - Salesforce の接続 - API 名](media/data-factory-salesforce-connector/data-factory-salesforce-api-name-2.png)

## <a name="salesforce-linked-service-properties"></a>Salesforce のリンクされたサービスのプロパティ
次の表は、Salesforce のリンクされたサービスに固有の JSON 要素の説明をまとめたものです。

| プロパティ | 説明 | 必須 |
| --- | --- | --- |
| type |type プロパティを **Salesforce**に設定する必要があります。 |はい |
| environmentUrl | Salesforce インスタンスの URL を指定します。 <br><br> - 既定では "https://login.salesforce.com" です。 <br> - サンドボックスからデータをコピーするには、"https://test.salesforce.com" を指定します。 <br> - カスタム ドメインからデータをコピーするには、たとえば "https://[ドメイン].my.salesforce.com" を指定します。 |なし |
| username |ユーザー アカウントのユーザー名を指定します。 |はい |
| パスワード |ユーザー アカウントのパスワードを指定します。 |はい |
| securityToken |ユーザー アカウントのセキュリティ トークンを指定します。 セキュリティ トークンのリセット/取得方法については、 [セキュリティ トークンの取得](https://help.salesforce.com/apex/HTViewHelpDoc?id=user_security_token.htm) に関する記事をご覧ください。 セキュリティ トークンの概要については、「[Security and the API (セキュリティと API)](https://developer.salesforce.com/docs/atlas.en-us.api.meta/api/sforce_api_concepts_security.htm)」をご覧ください。 |はい |

## <a name="salesforce-dataset-properties"></a>Salesforce データセットのプロパティ
データセットの定義に使用できるセクションとプロパティの完全な一覧については、 [データセットの作成](data-factory-create-datasets.md) に関する記事をご覧ください。 データセット JSON の構造、可用性、ポリシーなどのセクションは、データセットのすべての型 (Azure SQL、Azure BLOB、Azure テーブルなど) でほぼ同じです。

**typeProperties** セクションはデータセット型ごとに異なり、データ ストアのデータの場所などに関する情報を提供します。 **RelationalTable** 型のデータセットの typeProperties セクションには次のプロパティがあります。

| プロパティ | 説明 | 必須 |
| --- | --- | --- |
| tableName |Salesforce のテーブル名。 |いいえ (**RelationalSource** の**クエリ**が指定されている場合) |

> [!IMPORTANT]
> カスタム オブジェクトには、API 名の "__c" の部分が必要となります。
>
>

![Data Factory - Salesforce の接続 - API 名](media/data-factory-salesforce-connector/data-factory-salesforce-api-name.png)

## <a name="relationalsource-type-properties"></a>RelationalSource type プロパティ
アクティビティの定義に使用できるセクションとプロパティの完全な一覧については、 [パイプラインの作成](data-factory-create-pipelines.md) に関する記事をご覧ください。 名前、説明、入力テーブル、出力テーブル、さまざまなポリシーなどのプロパティがあらゆる種類のアクティビティで利用できます。

一方、アクティビティの typeProperties セクションで使用できるプロパティは、各アクティビティの種類によって異なります。 コピー アクティビティの場合、ソースとシンクの種類によって異なります。

コピー アクティビティで、source が **RelationalSource** 型 (Salesforce を含む) の場合は、typeProperties セクションで次のプロパティを使用できます。

| プロパティ | 説明 | 使用できる値 | 必須 |
| --- | --- | --- | --- |
| query |カスタム クエリを使用してデータを読み取ります。 |SQL-92 クエリまたは [Salesforce オブジェクト クエリ言語 (SOQL)](https://developer.salesforce.com/docs/atlas.en-us.soql_sosl.meta/soql_sosl/sforce_api_calls_soql.htm) クエリ。 たとえば、「`select * from MyTable__c`」のように入力します。 |いいえ (**dataset** の **tableName** が指定されている場合) |

> [!IMPORTANT]
> カスタム オブジェクトには、API 名の "__c" の部分が必要となります。
>
>

![Data Factory - Salesforce の接続 - API 名](media/data-factory-salesforce-connector/data-factory-salesforce-api-name-2.png)

## <a name="query-tips"></a>クエリのヒント
### <a name="retrieving-data-using-where-clause-on-datetime-column"></a>DateTime 列に対して where 句を 使ってデータを取得する
SOQL クエリまたは SQL クエリを指定する場合は、DateTime 形式の相違点に注意してください。 For example:

* **SOQL サンプル**: $$Text.Format('SELECT Id, Name, BillingCity FROM Account WHERE LastModifiedDate >= {0:yyyy-MM-ddTHH:mm:ssZ} AND LastModifiedDate < {1:yyyy-MM-ddTHH:mm:ssZ}', WindowStart, WindowEnd)
* **SQL サンプル**: $$Text.Format('SELECT * FROM Account  WHERE LastModifiedDate >= {{ts\'{0:yyyy-MM-dd HH:mm:ss}\'}} AND LastModifiedDate  < {{ts\'{1:yyyy-MM-dd HH:mm:ss}\'}}', WindowStart, WindowEnd)`

### <a name="retrieving-data-from-salesforce-report"></a>Salesforce レポートからのデータの取得
`{call "<report name>"}` というクエリ (例: `"query": "{call \"TestReport\"}"`) を指定することで、Salesforce レポートからデータを取得できます。

### <a name="retrieving-deleted-records-from-salesforce-recycle-bin"></a>削除済みレコードを Salesforce のごみ箱から取得する
論理的に削除されたレコードを Salesforce のごみ箱から検索するには、クエリで **"IsDeleted = 1"** と指定します。 たとえば、次のように入力します。

* 削除されたレコードのみを検索するには、"select * from MyTable__c **where IsDeleted= 1**" と指定します。
* 既存および削除済みを含むすべてのレコードを検索するには、"select * from MyTable__c **where IsDeleted = 0 or IsDeleted = 1**" と指定します。

[!INCLUDE [data-factory-structure-for-rectangualr-datasets](../../includes/data-factory-structure-for-rectangualr-datasets.md)]

### <a name="type-mapping-for-salesforce"></a>Salesforce の型マッピング
| Salesforce の型 | .NET ベースの型 |
| --- | --- |
| オート ナンバー |String |
| チェックボックス |Boolean |
| 通貨 |Double |
| 日付 |DateTime |
| 日付/時刻 |DateTime |
| 電子メール |String |
| ID |String |
| 参照リレーションシップ |String |
| 複数選択の候補リスト |String |
| Number |Double |
| Percent |Double |
| 電話 |String |
| 候補リスト |String |
| テキスト |String |
| テキスト領域 |String |
| テキスト領域 (ロング) |String |
| テキスト領域 (リッチ) |String |
| テキスト (暗号化) |String |
| URL |String |

[!INCLUDE [data-factory-column-mapping](../../includes/data-factory-column-mapping.md)]

[!INCLUDE [data-factory-structure-for-rectangualr-datasets](../../includes/data-factory-structure-for-rectangualr-datasets.md)]

## <a name="performance-and-tuning"></a>パフォーマンスとチューニング
Azure Data Factory でのデータ移動 (コピー アクティビティ) のパフォーマンスに影響する主な要因と、パフォーマンスを最適化するための各種方法については、「 [コピー アクティビティのパフォーマンスとチューニングに関するガイド](data-factory-copy-activity-performance.md) 」をご覧ください。



<!--HONumber=Jan17_HO1-->


