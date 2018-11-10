---
title: Azure Data Factory を使用して QuickBooks Online からデータをコピーする (プレビュー) | Microsoft Docs
description: Azure Data Factory パイプラインでコピー アクティビティを使用して、QuickBooks Online からサポートされているシンク データ ストアへデータをコピーする方法について説明します。
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
ms.date: 10/29/2018
ms.author: jingwang
ms.openlocfilehash: a3d079483ecf4ea8cf9a4c6bda050bfe8befcfd0
ms.sourcegitcommit: dbfd977100b22699823ad8bf03e0b75e9796615f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/30/2018
ms.locfileid: "50241686"
---
# <a name="copy-data-from-quickbooks-online-using-azure-data-factory-preview"></a>Azure Data Factory を使用して QuickBooks Online からデータをコピーする (プレビュー)

この記事では、Azure Data Factory のコピー アクティビティを使用して、QuickBooks Online からデータをコピーする方法について説明します。 この記事は、コピー アクティビティの概要を示している[コピー アクティビティの概要](copy-activity-overview.md)に関する記事に基づいています。

> [!IMPORTANT]
> このコネクタは、現在プレビューの段階です。 実際にお試しいただき、フィードバックをお寄せください。 ソリューションでプレビュー版コネクタの依存関係を取得したい場合、[Azure サポート](https://azure.microsoft.com/support/)にお問い合わせください。

## <a name="supported-capabilities"></a>サポートされる機能

QuickBooks Online から、サポートされている任意のシンク データ ストアにデータをコピーできます。 コピー アクティビティによってソースまたはシンクとしてサポートされているデータ ストアの一覧については、[サポートされているデータ ストア](copy-activity-overview.md#supported-data-stores-and-formats)に関する記事の表をご覧ください。

Azure Data Factory では接続を有効にする組み込みのドライバーが提供されるので、このコネクタを使用してドライバーを手動でインストールする必要はありません。

現在、このコネクタでサポートされるのは 1.0a のみです。つまり、2017 年 7 月 17 日よりも前に作成されたアプリに対しては開発者アカウントを持つ必要があります。

## <a name="getting-started"></a>使用の開始

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

次のセクションでは、QuickBooks コネクタに固有の Data Factory エンティティの定義に使用されるプロパティについて詳しく説明します。

## <a name="linked-service-properties"></a>リンクされたサービスのプロパティ

QuickBooks のリンクされたサービスでは、次のプロパティがサポートされます。

| プロパティ | 説明 | 必須 |
|:--- |:--- |:--- |
| type | type プロパティは **QuickBooks** に設定する必要があります。 | [はい] |
| endpoint | QuickBooks Online サーバーのエンドポイント。 (つまり、quickbooks.api.intuit.com)  | [はい] |
| companyId | 承認する QuickBooks の会社の会社 ID。 企業 ID を検索する方法については、「[How do I find my Company ID? (会社 ID の検索方法)](https://quickbooks.intuit.com/community/Getting-Started/How-do-I-find-my-Company-ID/m-p/185551)」を参照してください。 | [はい] |
| consumerKey | OAuth 1.0 認証用のコンシューマー キー。 | [はい] |
| consumerSecret | OAuth 1.0 認証用のコンシューマー シークレット。 このフィールドを SecureString としてマークして Data Factory に安全に保管するか、[Azure Key Vault に格納されているシークレットを参照](store-credentials-in-key-vault.md)します。 | [はい] |
| accessToken | OAuth 1.0 認証のアクセス トークン。 このフィールドを SecureString としてマークして Data Factory に安全に保管するか、[Azure Key Vault に格納されているシークレットを参照](store-credentials-in-key-vault.md)します。 | [はい] |
| accessTokenSecret | OAuth 1.0 認証のアクセス トークン シークレット。 このフィールドを SecureString としてマークして Data Factory に安全に保管するか、[Azure Key Vault に格納されているシークレットを参照](store-credentials-in-key-vault.md)します。 | [はい] |
| useEncryptedEndpoints | データ ソースのエンドポイントが HTTPS を使用して暗号化されるかどうかを指定します。 既定値は true です。  | いいえ  |

**例:**

```json
{
    "name": "QuickBooksLinkedService",
    "properties": {
        "type": "QuickBooks",
        "typeProperties": {
            "endpoint" : "quickbooks.api.intuit.com",
            "companyId" : "<companyId>",
            "consumerKey": "<consumerKey>",
            "consumerSecret": {
                "type": "SecureString",
                "value": "<consumerSecret>"
            },
            "accessToken": {
                 "type": "SecureString",
                 "value": "<accessToken>"
            },
            "accessTokenSecret": {
                 "type": "SecureString",
                 "value": "<accessTokenSecret>"
            },
            "useEncryptedEndpoints" : true
        }
    }
}
```

## <a name="dataset-properties"></a>データセットのプロパティ

データセットを定義するために使用できるセクションとプロパティの完全な一覧については、[データセット](concepts-datasets-linked-services.md)に関する記事をご覧ください。 このセクションでは、QuickBooks データセットでサポートされるプロパティの一覧を示します。

QuickBooks Online からデータをコピーするには、データセットの type プロパティを **QuickBooksObject** に設定します。 この種類のデータセットに追加の種類固有のプロパティはありません。

**例**

```json
{
    "name": "QuickBooksDataset",
    "properties": {
        "type": "QuickBooksObject",
        "linkedServiceName": {
            "referenceName": "<QuickBooks linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

## <a name="copy-activity-properties"></a>コピー アクティビティのプロパティ

アクティビティの定義に利用できるセクションとプロパティの完全な一覧については、[パイプライン](concepts-pipelines-activities.md)に関する記事を参照してください。 このセクションでは、QuickBooks ソース でサポートされるプロパティの一覧を示します。

### <a name="quickbookssource-as-source"></a>ソースとしての QuickBooksSource

QuickBooks Online からデータをコピーするには、コピー アクティビティのソースの種類を **QuickBooksSource** に設定します。 コピー アクティビティの **source** セクションでは、次のプロパティがサポートされます。

| プロパティ | 説明 | 必須 |
|:--- |:--- |:--- |
| type | コピー アクティビティのソースの type プロパティは **QuickBooksSource** に設定する必要があります。 | [はい] |
| query | カスタム SQL クエリを使用してデータを読み取ります。 (例: `"SELECT * FROM "Bill" WHERE Id = '123'"`)。 | [はい] |

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

Azure Data Factory のコピー アクティビティでは、Quickbooks Desktop から直接データをコピーすることはできません。 Quickbooks Desktop からデータをコピーするには、Quickbooks データをコンマ区切り値 (CSV) ファイルにエクスポートし、そのファイルを Azure Blob Storage にアップロードします。 そこから、Data Factory を使用して、選択したシンクにデータをコピーできます。

## <a name="next-steps"></a>次の手順
Azure Data Factory のコピー アクティビティによってソースおよびシンクとしてサポートされるデータ ストアの一覧については、[サポートされるデータ ストア](copy-activity-overview.md#supported-data-stores-and-formats)の表をご覧ください。
