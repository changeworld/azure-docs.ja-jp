---
title: QuickBooks Online からデータをコピーする (プレビュー)
description: Azure Data Factory または Synapse Analytics パイプラインでコピー アクティビティを使用して、QuickBooks Online からサポートされているシンク データ ストアにデータをコピーする方法について説明します。
titleSuffix: Azure Data Factory & Azure Synapse
author: jianleishen
ms.author: jianleishen
ms.service: data-factory
ms.subservice: data-movement
ms.topic: conceptual
ms.custom: synapse
ms.date: 09/09/2021
ms.openlocfilehash: 0d4cc79dd15e799d213fe89e85992883584af2e4
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/13/2021
ms.locfileid: "124820140"
---
# <a name="copy-data-from-quickbooks-online-using-azure-data-factory-or-synapse-analytics-preview"></a>Azure Data Factory または Synapse Analytics を使用して QuickBooks Online からデータをコピーする (プレビュー)
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

この記事では、Azure Data Factory または Synapse Analytics パイプラインでコピー アクティビティを使用して、QuickBooks Online からデータをコピーする方法について説明します。 この記事は、コピー アクティビティの概要を示している[コピー アクティビティの概要](copy-activity-overview.md)に関する記事に基づいています。

> [!IMPORTANT]
> このコネクタは、現在プレビューの段階です。 実際にお試しいただき、フィードバックをお寄せください。 ソリューションでプレビュー版コネクタの依存関係を取得したい場合、[Azure サポート](https://azure.microsoft.com/support/)にお問い合わせください。

## <a name="supported-capabilities"></a>サポートされる機能

この QuickBooks コネクタは、次のアクティビティでサポートされます。

- [サポートされるソース/シンク マトリックス](copy-activity-overview.md)での[コピー アクティビティ](copy-activity-overview.md)
- [Lookup アクティビティ](control-flow-lookup-activity.md)

QuickBooks Online から、サポートされている任意のシンク データ ストアにデータをコピーできます。 コピー アクティビティによってソースまたはシンクとしてサポートされているデータ ストアの一覧については、[サポートされているデータ ストア](copy-activity-overview.md#supported-data-stores-and-formats)に関する記事の表をご覧ください。

このコネクタでは、QuickBooks OAuth 2.0 認証がサポートされています。

## <a name="getting-started"></a>作業の開始

[!INCLUDE [data-factory-v2-connector-get-started](includes/data-factory-v2-connector-get-started.md)]

## <a name="create-a-linked-service-to-quickbooks-using-ui"></a>UI を使用して QuickBooks のリンク サービスを作成する

次の手順を使用して、Azure portal UI で QuickBooks のリンク サービスを作成します。

1. Azure Data Factory または Synapse ワークスペースの [管理] タブに移動し、[リンクされたサービス] を選択して、[新規] をクリックします。

    # <a name="azure-data-factory"></a>[Azure Data Factory](#tab/data-factory)

    :::image type="content" source="media/doc-common-process/new-linked-service.png" alt-text="Azure Data Factory の UI で新しいリンク サービスを作成するスクリーンショット。":::

    # <a name="azure-synapse"></a>[Azure Synapse](#tab/synapse-analytics)

    :::image type="content" source="media/doc-common-process/new-linked-service-synapse.png" alt-text="Azure Synapse の UI を使用した新しいリンク サービスの作成を示すスクリーンショット。":::

2. QuickBooks を検索し、QuickBooks コネクタを選択します。

   :::image type="content" source="media/connector-quickbooks/quickbooks-connector.png" alt-text="QuickBooks コネクタのスクリーンショット。":::    


1. サービスの詳細を構成し、接続をテストして、新しいリンク サービスを作成します。

   :::image type="content" source="media/connector-quickbooks/configure-quickbooks-linked-service.png" alt-text="QuickBooks のリンク サービスの構成のスクリーンショット。":::

## <a name="connector-configuration-details"></a>コネクタの構成の詳細

次のセクションでは、QuickBooks コネクタに固有の Data Factory エンティティの定義に使用されるプロパティについて詳しく説明します。

## <a name="linked-service-properties"></a>リンクされたサービスのプロパティ

QuickBooks のリンクされたサービスでは、次のプロパティがサポートされます。

| プロパティ | 説明 | 必須 |
|:--- |:--- |:--- |
| type | type プロパティは、次のように設定する必要があります:**QuickBooks** に設定する必要があります | はい |
| connectionProperties | QuickBooks への接続方法を定義するプロパティのグループ。 | はい |
| ***`connectionProperties` の下:*** | | |
| endpoint | QuickBooks Online サーバーのエンドポイント。 (つまり、quickbooks.api.intuit.com)  | はい |
| companyId | 承認する QuickBooks の会社の会社 ID。 会社 ID を検索する方法については、[会社 ID の検索方法](https://quickbooks.intuit.com/community/Getting-Started/How-do-I-find-my-Company-ID/m-p/185551)に関する記事を参照してください。 | はい |
| consumerKey | OAuth 2.0 認証用の QuickBooks Online アプリケーションのクライアント ID。 詳細については、[こちら](https://developer.intuit.com/app/developer/qbo/docs/develop/authentication-and-authorization/oauth-2.0#obtain-oauth2-credentials-for-your-app)を参照してください。 | はい |
| consumerSecret | OAuth 2.0 認証用の QuickBooks Online アプリケーションのクライアント シークレット。 このフィールドを SecureString とマークして安全に保存するか、[Azure Key Vault に保存されているシークレットを参照](store-credentials-in-key-vault.md)します。 | はい |
| refreshToken | QuickBooks アプリケーションに関連付けられた OAuth 2.0 更新トークン。 詳細については、[こちら](https://developer.intuit.com/app/developer/qbo/docs/develop/authentication-and-authorization/oauth-2.0#obtain-oauth2-credentials-for-your-app)を参照してください。 更新トークンは、180 日後に有効期限が切れることに注意してください。 お客様は、更新トークンを定期的に更新する必要があります。 <br/>このフィールドを SecureString とマークして安全に保存するか、[Azure Key Vault に保存されているシークレットを参照](store-credentials-in-key-vault.md)します。| はい |
| useEncryptedEndpoints | データ ソースのエンドポイントが HTTPS を使用して暗号化されるかどうかを指定します。 既定値は、true です。  | いいえ |

**例:**

```json
{
    "name": "QuickBooksLinkedService",
    "properties": {
        "type": "QuickBooks",
        "typeProperties": {
            "connectionProperties": {
                "endpoint": "quickbooks.api.intuit.com",
                "companyId": "<company id>",
                "consumerKey": "<consumer key>", 
                "consumerSecret": {
                     "type": "SecureString",
                     "value": "<clientSecret>"
                },
                "refreshToken": {
                     "type": "SecureString",
                     "value": "<refresh token>"
                },
                "useEncryptedEndpoints": true
            }
        }
    }
}
```

## <a name="dataset-properties"></a>データセットのプロパティ

データセットを定義するために使用できるセクションとプロパティの完全な一覧については、[データセット](concepts-datasets-linked-services.md)に関する記事をご覧ください。 このセクションでは、QuickBooks データセットでサポートされるプロパティの一覧を示します。

QuickBooks Online からデータをコピーするには、データセットの type プロパティを **QuickBooksObject** に設定します。 次のプロパティがサポートされています。

| プロパティ | 説明 | 必須 |
|:--- |:--- |:--- |
| type | データセットの type プロパティは、次のように設定する必要があります:**QuickBooksObject** に設定する必要があります | はい |
| tableName | テーブルの名前。 | いいえ (アクティビティ ソースの "query" が指定されている場合) |

**例**

```json
{
    "name": "QuickBooksDataset",
    "properties": {
        "type": "QuickBooksObject",
        "typeProperties": {},
        "schema": [],
        "linkedServiceName": {
            "referenceName": "<QuickBooks linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

## <a name="copy-activity-properties"></a>コピー アクティビティのプロパティ

アクティビティの定義に利用できるセクションとプロパティの完全な一覧については、[パイプライン](concepts-pipelines-activities.md)に関する記事を参照してください。 このセクションでは、QuickBooks ソース でサポートされるプロパティの一覧を示します。

### <a name="quickbooks-as-source"></a>ソースとしての QuickBooks

QuickBooks Online からデータをコピーするには、コピー アクティビティのソースの種類を **QuickBooksSource** に設定します。 コピー アクティビティの **source** セクションでは、次のプロパティがサポートされます。

| プロパティ | 説明 | 必須 |
|:--- |:--- |:--- |
| type | コピー アクティビティのソースの type プロパティは、次のように設定する必要があります:**QuickBooksSource** に設定する必要があります | はい |
| query | カスタム SQL クエリを使用してデータを読み取ります。 (例: `"SELECT * FROM "Bill" WHERE Id = '123'"`)。 | いいえ (データセットの "tableName" が指定されている場合) |

**例:**

```json
"activities":[
    {
        "name": "CopyFromQuickBooks",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<QuickBooks input dataset name>",
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
                "type": "QuickBooksSource",
                "query": "SELECT * FROM \"Bill\" WHERE Id = '123' "
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```
## <a name="copy-data-from-quickbooks-desktop"></a>QuickBooks Desktop からデータをコピーする

サービスのコピー アクティビティでは、Quickbooks Desktop から直接データをコピーすることはできません。 Quickbooks Desktop からデータをコピーするには、Quickbooks データをコンマ区切り値 (CSV) ファイルにエクスポートし、そのファイルを Azure Blob Storage にアップロードします。 そこから、サービスを使用して、選択したシンクにデータをコピーできます。

## <a name="lookup-activity-properties"></a>Lookup アクティビティのプロパティ

プロパティの詳細については、[Lookup アクティビティ](control-flow-lookup-activity.md)に関するページを参照してください。


## <a name="next-steps"></a>次のステップ
Copy アクティビティでソースおよびシンクとしてサポートされるデータ ストアの一覧については、[サポートされるデータ ストア](copy-activity-overview.md#supported-data-stores-and-formats)に関するセクションを参照してください。
