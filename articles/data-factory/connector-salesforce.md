---
title: Azure Data Factory を使用して Salesforce をコピー元またはコピー先としてデータをコピーする | Microsoft Docs
description: データ ファクトリ パイプラインでコピー アクティビティを使用して、Salesforce からサポートされているシンク データ ストアに、またはサポートされているソース データ ストアから Salesforce にデータをコピーする方法について説明します。
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/18/2018
ms.author: jingwang
ms.openlocfilehash: 69e3e308fb5af98dd5763c56503cc28bd4ecfa9e
ms.sourcegitcommit: b9786bd755c68d602525f75109bbe6521ee06587
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/18/2018
ms.locfileid: "39125250"
---
# <a name="copy-data-from-and-to-salesforce-by-using-azure-data-factory"></a>Azure Data Factory を使用して Salesforce をコピー元またはコピー先としてデータをコピーする
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Version 1](v1/data-factory-salesforce-connector.md)
> * [現在のバージョン](connector-salesforce.md)

この記事では、Azure Data Factory のコピー アクティビティを使用して、Salesforce から、または Salesforce にデータをコピーする方法について説明します。 この記事は、コピー アクティビティの概要を示している[コピー アクティビティの概要](copy-activity-overview.md)に関する記事に基づいています。

## <a name="supported-capabilities"></a>サポートされる機能

Salesforce から、サポートされている任意のシンク データ ストアにデータをコピーできます。 サポートされている任意のソース データ ストアから Salesforce にデータをコピーすることもできます。 コピー アクティビティによってソースまたはシンクとしてサポートされるデータ ストアの一覧については、[サポートされるデータ ストア](copy-activity-overview.md#supported-data-stores-and-formats)に関する記事の表を参照してください。

具体的には、この Salesforce コネクタは以下をサポートします。

- Salesforce Developer、Professional、Enterprise、または Unlimited エディション。
- Salesforce 運用環境、サンドボックス、およびカスタム ドメインをコピー先またはコピー元とするデータのコピー。

## <a name="prerequisites"></a>前提条件

Salesforce で API アクセス許可を有効にする必要があります。 詳細については、「[How do I enable API access in Salesforce by permission set?](https://www.data2crm.com/migration/faqs/enable-api-access-salesforce-permission-set/)」(権限セットで Salesforce の API アクセスを有効にする方法) を参照してください。

## <a name="salesforce-request-limits"></a>Salesforce の要求の制限

Salesforce では、API 要求数の合計と、API の同時要求数に上限が設けられています。 以下の点に注意してください。

- 同時要求数が上限を超えると調整が発生し、ランダムにエラーが表示されます。
- 要求数の合計が上限を超えると、Salesforce アカウントが 24 時間ブロックされます。

また、どちらのシナリオでも、"REQUEST_LIMIT_EXCEEDED" エラー メッセージが表示されることがあります。 詳細については、[Salesforce Developer の制限](http://resources.docs.salesforce.com/200/20/en-us/sfdc/pdf/salesforce_app_limits_cheatsheet.pdf)に関する資料の「API Request Limits」(API 要求の制限) をご覧ください。

## <a name="get-started"></a>作業開始

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

次のセクションでは、Salesforce コネクタに固有の Data Factory エンティティの定義に使用されるプロパティについて詳しく説明します。

## <a name="linked-service-properties"></a>リンクされたサービスのプロパティ

Salesforce のリンクされたサービスでは、次のプロパティがサポートされます。

| プロパティ | 説明 | 必須 |
|:--- |:--- |:--- |
| type |type プロパティを **Salesforce** に設定する必要があります。 |[はい] |
| environmentUrl | Salesforce インスタンスの URL を指定します。 <br> 既定値は `"https://login.salesforce.com"` です。 <br> - サンドボックスからデータをコピーするには、`"https://test.salesforce.com"` を指定します。 <br> - カスタム ドメインからデータをコピーするには、`"https://[domain].my.salesforce.com"`のように指定します。 |いいえ  |
| username |ユーザー アカウントのユーザー名を指定します。 |[はい] |
| password |ユーザー アカウントのパスワードを指定します。<br/><br/>このフィールドを SecureString としてマークして Data Factory に安全に保管するか、[Azure Key Vault に格納されているシークレットを参照](store-credentials-in-key-vault.md)します。 |[はい] |
| securityToken |ユーザー アカウントのセキュリティ トークンを指定します。 セキュリティ トークンのリセット/取得方法については、[セキュリティ トークンの取得](https://help.salesforce.com/apex/HTViewHelpDoc?id=user_security_token.htm)に関する記事をご覧ください。 セキュリティ トークンの概要については、「[Security and the API (セキュリティと API)](https://developer.salesforce.com/docs/atlas.en-us.api.meta/api/sforce_api_concepts_security.htm)」をご覧ください。<br/><br/>このフィールドを SecureString としてマークして Data Factory に安全に保管するか、[Azure Key Vault に格納されているシークレットを参照](store-credentials-in-key-vault.md)します。 |[はい] |
| connectVia | データ ストアに接続するために使用される[統合ランタイム](concepts-integration-runtime.md)。 指定されていない場合は、既定の Azure 統合ランタイムが使用されます。 | ソースの場合は「いいえ」、シンクの場合は「はい」 (ソースにリンクされたサービスに統合ランタイムがない場合) |

>[!IMPORTANT]
>Salesforce にデータをコピーする場合は、既定の Azure Integration Runtime を使用してコピーを実行することはできません。 言い換えると、ソースのリンクされたサービスに指定された統合ランタイムがない場合は、Salesforce インスタンスに近い場所に明示的に [Azure Integration Runtime を作成](create-azure-integration-runtime.md#create-azure-ir)します。 次の例のように、Salesforce のリンクされたサービスを関連付けます。

**例: データ ファクトリに資格情報を格納する**

```json
{
    "name": "SalesforceLinkedService",
    "properties": {
        "type": "Salesforce",
        "typeProperties": {
            "username": "<username>",
            "password": {
                "type": "SecureString",
                "value": "<password>"
            },
            "securityToken": {
                "type": "SecureString",
                "value": "<security token>"
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

**例: キー コンテナーに資格情報を格納する**

```json
{
    "name": "SalesforceLinkedService",
    "properties": {
        "type": "Salesforce",
        "typeProperties": {
            "username": "<username>",
            "password": {
                "type": "AzureKeyVaultSecret",
                "secretName": "<secret name of password in AKV>",
                "store":{
                    "referenceName": "<Azure Key Vault linked service>",
                    "type": "LinkedServiceReference"
                }
            },
            "securityToken": {
                "type": "AzureKeyVaultSecret",
                "secretName": "<secret name of security token in AKV>",
                "store":{
                    "referenceName": "<Azure Key Vault linked service>",
                    "type": "LinkedServiceReference"
                }
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

## <a name="dataset-properties"></a>データセットのプロパティ

データセットを定義するために使用できるセクションとプロパティの完全な一覧については、[データセット](concepts-datasets-linked-services.md)に関する記事をご覧ください。 このセクションでは、Salesforce データセット でサポートされるプロパティの一覧を示します。

Salesforce をコピー元またはコピー先としてデータをコピーするには、データセットの type プロパティを **SalesforceObject** に設定します。 次のプロパティがサポートされています。

| プロパティ | 説明 | 必須 |
|:--- |:--- |:--- |
| type | type プロパティは **SalesforceObject** に設定する必要があります。  | [はい] |
| objectApiName | データの取得元の Salesforce オブジェクト名。 | ソースの場合はいいえ、シンクの場合ははい |

> [!IMPORTANT]
> カスタム オブジェクトには、**API 名**の "__c" の部分が必要となります。

![Data Factory の Salesforce 接続 API 名](media/copy-data-from-salesforce/data-factory-salesforce-api-name.png)

**例:**

```json
{
    "name": "SalesforceDataset",
    "properties": {
        "type": "SalesforceObject",
        "linkedServiceName": {
            "referenceName": "<Salesforce linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "objectApiName": "MyTable__c"
        }
    }
}
```

>[!NOTE]
>旧バージョンとの互換性の維持: Salesforce からデータをコピーする際に、以前の "RelationalTable" 型のデータセットを引き続き使用できますが、新しい "SalesforceObject" 型に切り替えることを推奨するメッセージが表示されます。

| プロパティ | 説明 | 必須 |
|:--- |:--- |:--- |
| type | データセットの type プロパティは **RelationalTable** に設定する必要があります。 | [はい] |
| tableName | Salesforce のテーブル名。 | いいえ (アクティビティ ソースの "query" が指定されている場合) |

## <a name="copy-activity-properties"></a>コピー アクティビティのプロパティ

アクティビティの定義に利用できるセクションとプロパティの完全な一覧については、[パイプライン](concepts-pipelines-activities.md)に関する記事を参照してください。 このセクションでは、Salesforce ソースとシンクでサポートされるプロパティの一覧を示します。

### <a name="salesforce-as-a-source-type"></a>ソース タイプとしての Salesforce

Salesforce からデータをコピーするには、コピー アクティビティのソースの種類を **SalesforceSource** に設定します。 コピー アクティビティの **source** セクションでは、次のプロパティがサポートされます。

| プロパティ | 説明 | 必須 |
|:--- |:--- |:--- |
| type | コピー アクティビティのソースの type プロパティは **SalesforceSource** に設定する必要があります。 | [はい] |
| query |カスタム クエリを使用してデータを読み取ります。 SQL-92 クエリまたは [Salesforce オブジェクト クエリ言語 (SOQL)](https://developer.salesforce.com/docs/atlas.en-us.soql_sosl.meta/soql_sosl/sforce_api_calls_soql.htm) クエリを使用できます。 例: `select * from MyTable__c`。 | いいえ (データセットの "tableName" が指定されている場合) |
| readBehavior | 既存のレコード、または削除されたものを含むすべてのレコードの、どちらのクエリを行うかを示します。 指定しない場合の既定の動作は前者です。 <br>使用可能な値: **query** (既定値)、**queryAll**.  | いいえ  |

> [!IMPORTANT]
> カスタム オブジェクトには、**API 名**の "__c" の部分が必要となります。

![Data Factory の Salesforce 接続 API 名一覧](media/copy-data-from-salesforce/data-factory-salesforce-api-name-2.png)

**例:**

```json
"activities":[
    {
        "name": "CopyFromSalesforce",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Salesforce input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "SalesforceSource",
                "query": "SELECT Col_Currency__c, Col_Date__c, Col_Email__c FROM AllDataType__c"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

>[!NOTE]
>旧バージョンとの互換性の維持: Salesforce からデータをコピーする際に、以前の "RelationalSource" 型のコピーを引き続き使用できますが、新しい "SalesforceSource" 型に切り替えることを推奨するメッセージが表示されます。

### <a name="salesforce-as-a-sink-type"></a>シンクの種類としての Salesforce

Salesforce にデータをコピーするには、コピー アクティビティのシンクの種類を **SalesforceSink** に設定します。 コピー アクティビティの **sink** セクションでは、次のプロパティがサポートされます。

| プロパティ | 説明 | 必須 |
|:--- |:--- |:--- |
| type | コピー アクティビティのシンクの type プロパティは **SalesforceSink** に設定する必要があります。 | [はい] |
| writeBehavior | 操作の書き込み動作。<br/>使用可能な値: **Insert** および **Upsert**。 | いいえ (既定値は Insert) |
| externalIdFieldName | Upsert 操作の外部 ID フィールドの名前。 指定するフィールドは、Salesforce オブジェクトに "External Id Field" として定義されている必要があります。 対応する入力データに NULL 値を持つことはできません。 | "Upsert" の場合ははい |
| writeBatchSize | 各バッチで Salesforce に書き込まれたデータの行数。 | いいえ (既定値は 5,000) |
| ignoreNullValues | 書き込み操作時に入力データからの NULL 値を無視するかどうかを示します。<br/>使用可能な値: **true** および **false**。<br>- **True**: upsert または更新操作を行うときに、対象オブジェクト内のデータが変更されないようにします。 挿入操作を実行するときに、定義済みの既定値を挿入します。<br/>- **False**: upsert または更新操作を行うときに、対象オブジェクト内のデータを NULL に更新します。 挿入操作を実行するときに、NULL 値を挿入します。 | いいえ (既定値は false) |

**例: コピー アクティビティでの Salesforce シンク**

```json
"activities":[
    {
        "name": "CopyToSalesforce",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Salesforce output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "SalesforceSink",
                "writeBehavior": "Upsert",
                "externalIdFieldName": "CustomerId__c",
                "writeBatchSize": 10000,
                "ignoreNullValues": true
            }
        }
    }
]
```

## <a name="query-tips"></a>クエリのヒント

### <a name="retrieve-data-from-a-salesforce-report"></a>Salesforce レポートからデータを取得する

クエリを `{call "<report name>"}` と指定することで、Salesforce レポートからデータを取得できます。 例: `"query": "{call \"TestReport\"}"`。

### <a name="retrieve-deleted-records-from-the-salesforce-recycle-bin"></a>Salesforce のごみ箱から削除済みレコードを取得する

論理的に削除されたレコードを Salesforce のごみ箱から検索するには、クエリで **"IsDeleted = 1"** と指定できます。 例: 

* 削除されたレコードのみを検索するには、"select * from MyTable__c **where IsDeleted= 1**" と指定します。
* 既存および削除済みを含むすべてのレコードを検索するには、"select * from MyTable__c **where IsDeleted = 0 or IsDeleted = 1**" と指定します。

### <a name="retrieve-data-by-using-a-where-clause-on-the-datetime-column"></a>DateTime 列で where 句を使用してデータを取得する

SOQL クエリまたは SQL クエリを指定する場合は、DateTime 形式の違いに注意してください。 例: 

* **SOQL サンプル**: `SELECT Id, Name, BillingCity FROM Account WHERE LastModifiedDate >= @{formatDateTime(pipeline().parameters.StartTime,'yyyy-MM-ddTHH:mm:ssZ')} AND LastModifiedDate < @{formatDateTime(pipeline().parameters.EndTime,'yyyy-MM-ddTHH:mm:ssZ')}`
* **SQL の例**: `SELECT * FROM Account WHERE LastModifiedDate >= {ts'@{formatDateTime(pipeline().parameters.StartTime,'yyyy-MM-dd HH:mm:ss')}'} AND LastModifiedDate < {ts'@{formatDateTime(pipeline().parameters.EndTime,'yyyy-MM-dd HH:mm:ss')}'}`

## <a name="data-type-mapping-for-salesforce"></a>Salesforce のデータ型マッピング

Salesforce からデータをコピーするとき、次の Salesforce のデータ型から Data Factory の中間データ型へのマッピングが使用されます。 コピー アクティビティでソースのスキーマとデータ型がシンクにマッピングされるしくみについては、[スキーマとデータ型のマッピング](copy-activity-schema-and-type-mapping.md)に関する記事を参照してください。

| Salesforce のデータ型 | Data Factory の中間データ型 |
|:--- |:--- |
| オート ナンバー |String |
| チェックボックス |Boolean |
| 通貨 |Decimal |
| 日付 |Datetime |
| 日付/時刻 |Datetime |
| 電子メール |String |
| ID |String |
| 参照リレーションシップ |String |
| 複数選択の候補リスト |String |
| Number |Decimal |
| Percent |Decimal |
| 電話 |String |
| 候補リスト |String |
| Text |String |
| テキスト領域 |String |
| テキスト領域 (ロング) |String |
| テキスト領域 (リッチ) |String |
| テキスト (暗号化) |String |
| URL |String |

## <a name="next-steps"></a>次の手順
Data Factory のコピー アクティビティによってソースおよびシンクとしてサポートされるデータ ストアの一覧については、[サポートされるデータ ストア](copy-activity-overview.md#supported-data-stores-and-formats)の表をご覧ください。
