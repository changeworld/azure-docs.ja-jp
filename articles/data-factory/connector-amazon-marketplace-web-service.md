---
title: "Azure Data Factory (Beta) を使用して Amazon Marketplace Web Service からデータをコピーする | Microsoft Docs"
description: "Azure Data Factory パイプラインでコピー アクティビティを使用して、Amazon Marketplace Web Service からサポートされているシンク データ ストアにデータをコピーする方法について説明します。"
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
ms.date: 02/07/2018
ms.author: jingwang
ms.openlocfilehash: 62036e8a4e0611958ffacf7f7d7896e6ebde5e16
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/09/2018
---
# <a name="copy-data-from-amazon-marketplace-web-service-using-azure-data-factory-beta"></a>Azure Data Factory (Beta) を使用して Amazon Marketplace Web Service からデータをコピーする

この記事では、Azure Data Factory のコピー アクティビティを使用して、Amazon Marketplace Web Service からデータコピーする方法について説明します。 この記事は、コピー アクティビティの概要を示している[コピー アクティビティの概要](copy-activity-overview.md)に関する記事に基づいています。

> [!NOTE]
> この記事は、現在プレビュー段階にある Data Factory のバージョン 2 に適用されます。 一般公開 (GA) されている Data Factory サービスのバージョン 1 を使用している場合は、「[Copy Activity in V1 (V1 でのコピー アクティビティ)](v1/data-factory-data-movement-activities.md)」を参照してください。

> [!IMPORTANT]
> このコネクタは、現在ベータ版です。 実際にお試しいただき、フィードバックをお寄せください。 運用環境では使用しないでください。

## <a name="supported-capabilities"></a>サポートされる機能

Amazon Marketplace Web Service から、サポートされている任意のシンク データ ストアにデータをコピーできます。 コピー アクティビティによってソースまたはシンクとしてサポートされているデータ ストアの一覧については、[サポートされているデータ ストア](copy-activity-overview.md#supported-data-stores-and-formats)に関する記事の表をご覧ください。

Azure Data Factory では接続を有効にする組み込みのドライバーが提供されるので、このコネクタを使用してドライバーを手動でインストールする必要はありません。

## <a name="getting-started"></a>使用の開始

[!INCLUDE [data-factory-v2-connector-get-started-2](../../includes/data-factory-v2-connector-get-started-2.md)]

次のセクションでは、Amazon Marketplace Web Service コネクターに固有の Data Factory エンティティの定義に使用されるプロパティについて詳しく説明します。

## <a name="linked-service-properties"></a>リンクされたサービスのプロパティ

Amazon Marketplace Web Service のリンクされたサービスでは、次のプロパティがサポートされます。

| プロパティ | [説明] | 必須 |
|:--- |:--- |:--- |
| 型 | type プロパティは **AmazonMWS** に設定する必要があります。 | [はい] |
| endpoint | Amazon MWS サーバーのエンドポイント (つまり、mws.amazonservices.com)  | [はい] |
| marketplaceID | データを取得する Amazon Marketplace ID。 複数の Marketplace ID からデータを取得するには、コンマ (`,`) で区切って指定します。 (つまり、A2EUQ1WTGCTBG2)  | [はい] |
| sellerID | Amazon の販売者 ID。  | [はい] |
| mwsAuthToken | Amazon MWS 認証トークン。 このフィールドを SecureString としてマークして Data Factory に安全に格納するか、[Azure Key Vault に格納されているシークレットを参照](store-credentials-in-key-vault.md)します。 | [はい] |
| accessKeyId | データのアクセスに使用されるアクセス キー ID。  | [はい] |
| secretKey | データのアクセスに使用されるシークレット キー。 このフィールドを SecureString としてマークして Data Factory に安全に格納するか、[Azure Key Vault に格納されているシークレットを参照](store-credentials-in-key-vault.md)します。 | [はい] |
| useEncryptedEndpoints | データ ソースのエンドポイントが HTTPS を使用して暗号化されるかどうかを指定します。 既定値は true です。  | いいえ  |
| useHostVerification | SSL 経由で接続するときに、サーバーの証明書内のホスト名がサーバーのホスト名と一致する必要があるかどうかを指定します。 既定値は true です。  | いいえ  |
| usePeerVerification | SSL 経由で接続するときに、サーバーの ID を検証するかどうかを指定します。 既定値は true です。  | いいえ  |

**例:**

```json
{
    "name": "AmazonMWSLinkedService",
    "properties": {
        "type": "AmazonMWS",
        "typeProperties": {
            "endpoint" : "mws.amazonservices.com",
            "marketplaceID" : "A2EUQ1WTGCTBG2",
            "sellerID" : "<sellerID>",
            "mwsAuthToken": {
                 "type": "SecureString",
                 "value": "<mwsAuthToken>"
            },
            "accessKeyId" : "<accessKeyId>",
            "secretKey": {
                 "type": "SecureString",
                 "value": "<secretKey>"
            }
        }
    }
}
```

## <a name="dataset-properties"></a>データセットのプロパティ

データセットを定義するために使用できるセクションとプロパティの完全な一覧については、[データセット](concepts-datasets-linked-services.md)に関する記事をご覧ください。 このセクションでは、Amazon Marketplace Web Service データセットでサポートされるプロパティの一覧を示します。

Amazon Marketplace Web Service からデータをコピーするには、データセットの type プロパティを **AmazonMWSObject** に設定します。 この種類のデータセットに追加の種類固有のプロパティはありません。

**例**

```json
{
    "name": "AmazonMWSDataset",
    "properties": {
        "type": "AmazonMWSObject",
        "linkedServiceName": {
            "referenceName": "<AmazonMWS linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}

```

## <a name="copy-activity-properties"></a>コピー アクティビティのプロパティ

アクティビティの定義に利用できるセクションとプロパティの完全な一覧については、[パイプライン](concepts-pipelines-activities.md)に関する記事を参照してください。 このセクションでは、Amazon Marketplace Web Service ソースでサポートされるプロパティの一覧を示します。

### <a name="amazonmwssource-as-source"></a>ソースとしての AmazonMWSSource

Amazon Marketplace Web Service からデータをコピーするには、コピー アクティビティでソースの型を **AmazonMWSSource** に設定します。 コピー アクティビティの **source** セクションでは、次のプロパティがサポートされます。

| プロパティ | [説明] | 必須 |
|:--- |:--- |:--- |
| 型 | コピー アクティビティのソースの type プロパティは **AmazonMWSSource** に設定する必要があります。 | [はい] |
| クエリ | カスタム SQL クエリを使用してデータを読み取ります。 たとえば、「 `"SELECT * FROM Orders where  Amazon_Order_Id = 'xx'"`」のように入力します。 | [はい] |

**例:**

```json
"activities":[
    {
        "name": "CopyFromAmazonMWS",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<AmazonMWS input dataset name>",
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
                "type": "AmazonMWSSource",
                "query": "SELECT * FROM Orders where Amazon_Order_Id = 'xx'"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

## <a name="next-steps"></a>次の手順
Azure Data Factory のコピー アクティビティによってソースおよびシンクとしてサポートされるデータ ストアの一覧については、[サポートされるデータ ストア](copy-activity-overview.md#supported-data-stores-and-formats)の表をご覧ください。
