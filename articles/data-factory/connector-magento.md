---
title: "Azure Data Factory (Beta) を使用して Magento からデータをコピーする | Microsoft Docs"
description: "Azure Data Factory パイプラインでコピー アクティビティを使用して、Magento のデータをサポートされているシンク データ ストアにコピーする方法について説明します。"
services: data-factory
documentationcenter: 
author: linda33wj
manager: jhubbard
editor: spelluru
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/30/2017
ms.author: jingwang
ms.openlocfilehash: ae66077ba17614ea6ec8349673cfcee8cd6deeb6
ms.sourcegitcommit: aaba209b9cea87cb983e6f498e7a820616a77471
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/12/2017
---
# <a name="copy-data-from-magento-using-azure-data-factory-beta"></a>Azure Data Factory (Beta) を使用して Magento からデータをコピーする

この記事では、Azure Data Factory のコピー アクティビティを使用して、Magento からデータをコピーする方法について説明します。 この記事は、コピー アクティビティの概要を示している[コピー アクティビティの概要](copy-activity-overview.md)に関する記事に基づいています。

> [!NOTE]
> この記事は、現在プレビュー段階にある Data Factory のバージョン 2 に適用されます。 一般公開 (GA) されている Data Factory サービスのバージョン 1 を使用している場合は、「[Copy Activity in V1 (V1 でのコピー アクティビティ)](v1/data-factory-data-movement-activities.md)」を参照してください。

> [!IMPORTANT]
> このコネクタは、現在ベータ版です。 実際にお試しいただき、フィードバックをお寄せください。 運用環境では使用しないでください。

## <a name="supported-capabilities"></a>サポートされる機能

Magento から、サポートされている任意のシンク データ ストアにデータをコピーできます。 コピー アクティビティによってソースまたはシンクとしてサポートされているデータ ストアの一覧については、[サポートされているデータ ストア](copy-activity-overview.md#supported-data-stores-and-formats)に関する記事の表をご覧ください。

Azure Data Factory では接続を有効にする組み込みのドライバーが提供されるので、このコネクタを使用してドライバーを手動でインストールする必要はありません。

## <a name="getting-started"></a>使用の開始

コピー アクティビティを含むパイプラインは、.NET SDK、Python SDK、Azure PowerShell、REST API、または Azure Resource Manager テンプレートを使用して作成できます。 コピー アクティビティを含むパイプラインを作成するための詳細な手順については、[コピー アクティビティのチュートリアル](quickstart-create-data-factory-dot-net.md)をご覧ください。

次のセクションでは、Magento コネクタに固有の Data Factory エンティティを定義するために使用されるプロパティについて詳しく説明します。

## <a name="linked-service-properties"></a>リンクされたサービスのプロパティ

Magento のリンクされたサービスでは、次のプロパティがサポートされます。

| プロパティ | 説明 | 必須 |
|:--- |:--- |:--- |
| type | type プロパティは **Magento** に設定する必要があります。 | あり |
| host | Magento インスタンスの URL。 (つまり、192.168.222.110/magento3)  | あり |
| accessToken | Magento のアクセス トークン。 このフィールドを SecureString としてマークして ADF に安全に格納するか、Azure Key Vault にパスワードを格納し、データ コピーの実行時にコピー アクティビティでそこからプルするかを選択できます。詳しくは、[Key Vault への資格情報の格納](store-credentials-in-key-vault.md)に関するページをご覧ください。 | あり |
| useEncryptedEndpoints | データ ソースのエンドポイントが HTTPS を使用して暗号化されるかどうかを指定します。 既定値は true です。  | いいえ |
| useHostVerification | SSL 経由で接続するときに、サーバーの証明書内のホスト名がサーバーのホスト名と一致する必要があるかどうかを指定します。 既定値は true です。  | いいえ |
| usePeerVerification | SSL 経由で接続するときに、サーバーの ID を検証するかどうかを指定します。 既定値は true です。  | いいえ |

**例:**

```json
{
    "name": "MagentoLinkedService",
    "properties": {
        "type": "Magento",
        "typeProperties": {
            "host" : "192.168.222.110/magento3",
            "accessToken": {
                 "type": "SecureString",
                 "value": "<accessToken>"
            },
            "useEncryptedEndpoints" : true,
            "useHostVerification" : true,
            "usePeerVerification" : true
        }
    }
}
```

## <a name="dataset-properties"></a>データセットのプロパティ

データセットを定義するために使用できるセクションとプロパティの完全な一覧については、[データセット](concepts-datasets-linked-services.md)に関する記事をご覧ください。 このセクションでは、Magento データセットでサポートされるプロパティの一覧を示します。

Magento からデータをコピーするには、データセットの type プロパティを **MagentoObject** に設定します。 この種類のデータセットに追加の種類固有のプロパティはありません。

**例**

```json
{
    "name": "MagentoDataset",
    "properties": {
        "type": "MagentoObject",
        "linkedServiceName": {
            "referenceName": "<Magento linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

## <a name="copy-activity-properties"></a>コピー アクティビティのプロパティ

アクティビティの定義に利用できるセクションとプロパティの完全な一覧については、[パイプライン](concepts-pipelines-activities.md)に関する記事を参照してください。 このセクションでは、Magento ソース でサポートされるプロパティの一覧を示します。

### <a name="magentosource-as-source"></a>ソースとしての MagentoSource

Magento からデータをコピーするには、コピー アクティビティのソースの種類を **MagentoSource** に設定します。 コピー アクティビティの **source** セクションでは、次のプロパティがサポートされます。

| プロパティ | 説明 | 必須 |
|:--- |:--- |:--- |
| type | コピー アクティビティのソースの type プロパティは **MagentoSource** に設定する必要があります。 | あり |
| クエリ | カスタム SQL クエリを使用してデータを読み取ります。 (例: `"SELECT * FROM Customers"`)。 | あり |

**例:**

```json
"activities":[
    {
        "name": "CopyFromMagento",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Magento input dataset name>",
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
                "type": "MagentoSource",
                "query": "SELECT * FROM Customers where Id > XXX"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

## <a name="next-steps"></a>次のステップ
Azure Data Factory のコピー アクティビティによってソースおよびシンクとしてサポートされるデータ ストアの一覧については、[サポートされるデータ ストア](copy-activity-overview.md#supported-data-stores-and-formats)の表をご覧ください。
