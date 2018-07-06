---
title: Azure Data Factory を使用して Impala からデータをコピーする | Microsoft Docs
description: データ ファクトリ パイプラインでコピー アクティビティを使用して、Impala のデータをサポートされているシンク データ ストアにコピーする方法について説明します。
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
ms.date: 06/15/2018
ms.author: jingwang
ms.openlocfilehash: 366d0945bfac8546aa757648b6f797c2605a43ea
ms.sourcegitcommit: 0c490934b5596204d175be89af6b45aafc7ff730
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/27/2018
ms.locfileid: "37045869"
---
# <a name="copy-data-from-impala-by-using-azure-data-factory"></a>Azure Data Factory を使用して Impala からデータをコピーする

この記事では、Azure Data Factory のコピー アクティビティを使用して、Impala からデータをコピーする方法について説明します。 この記事は、コピー アクティビティの概要を示している[コピー アクティビティの概要](copy-activity-overview.md)に関する記事に基づいています。

> [!IMPORTANT]
> このコネクタは、現在プレビューの段階です。 実際にお試しいただき、フィードバックをお寄せください。 ソリューションでプレビュー版コネクタの依存関係を取得したい場合、[Azure サポート](https://azure.microsoft.com/support/)にお問い合わせください。

## <a name="supported-capabilities"></a>サポートされる機能

Impala から、サポートされている任意のシンク データ ストアにデータをコピーできます。 コピー アクティビティによってソースまたはシンクとしてサポートされるデータ ストアの一覧については、[サポートされるデータ ストア](copy-activity-overview.md#supported-data-stores-and-formats)に関する記事の表をご覧ください。

 Data Factory では、接続を可能にする組み込みのドライバーが提供されます。 そのため、このコネクタを使用するためにドライバーを手動でインストールする必要はありません。

## <a name="get-started"></a>作業開始

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

次のセクションでは、Impala コネクタに固有の Data Factory エンティティの定義に使用されるプロパティについて詳しく説明します。

## <a name="linked-service-properties"></a>リンクされたサービスのプロパティ

Impala のリンクされたサービスでは、次のプロパティがサポートされます。

| プロパティ | 説明 | 必須 |
|:--- |:--- |:--- |
| type | type プロパティを **Impala** に設定する必要があります。 | [はい] |
| host | Impala サーバーの IP アドレスまたはホスト名 (192.168.222.160)。  | [はい] |
| ポート | Impala サーバーがクライアント接続のリッスンに使用する TCP ポート。 既定値は 21050 です。  | いいえ  |
| authenticationType | 使用する認証の種類。 <br/>使用できる値は、**Anonymous**、**SASLUsername**、および **UsernameAndPassword** です。 | [はい] |
| username | Impala サーバーへのアクセスに使用するユーザー名。 既定値は anonymous です (SASLUsername を使用するとき)。  | いいえ  |
| password | ユーザー名に対応するパスワード (UsernameAndPassword を使用するとき)。 このフィールドを SecureString としてマークして Data Factory に安全に保管するか、[Azure Key Vault に格納されているシークレットを参照](store-credentials-in-key-vault.md)します。 | いいえ  |
| enableSsl | SSL を使用してサーバーへの接続を暗号化するかどうかを指定します。 既定値は **false** です。  | いいえ  |
| trustedCertPath | SSL 経由で接続するときにサーバーを検証するために使用する、信頼された CA 証明書を含む .pem ファイルの完全なパス。 このプロパティは、セルフホステッド統合ランタイムで SSL を使用する場合にのみ設定できます。 既定値は、IR でインストールされる cacerts.pem ファイルです。  | いいえ  |
| useSystemTrustStore | システムの信頼ストアと指定した PEM ファイルのどちらの CA 証明書を使用するかを指定します。 既定値は **false** です。  | いいえ  |
| allowHostNameCNMismatch | SSL 経由で接続するときに、CA が発行した SSL 証明書名がサーバーのホスト名と一致する必要があるかどうかを指定します。 既定値は **false** です。  | いいえ  |
| allowSelfSignedServerCert | サーバーからの自己署名証明書を許可するかどうかを指定します。 既定値は **false** です。  | いいえ  |
| connectVia | データ ストアに接続するために使用される[統合ランタイム](concepts-integration-runtime.md)。 セルフホステッド統合ランタイムまたは Azure 統合ランタイム (データ ストアがパブリックにアクセスできる場合) を使用できます。 指定されていない場合は、既定の Azure 統合ランタイムが使用されます。 |いいえ  |

**例:**

```json
{
    "name": "ImpalaLinkedService",
    "properties": {
        "type": "Impala",
        "typeProperties": {
            "host" : "<host>",
            "port" : "<port>",
            "authenticationType" : "UsernameAndPassword",
            "username" : "<username>",
            "password": {
                 "type": "SecureString",
                 "value": "<password>"
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

データセットを定義するために使用できるセクションとプロパティの完全な一覧については、[データセット](concepts-datasets-linked-services.md)に関する記事をご覧ください。 このセクションでは、Impala データセットでサポートされるプロパティの一覧を示します。

Impala からデータをコピーするには、データセットの type プロパティを **ImpalaObject** に設定します。 この種類のデータセットに追加の種類固有のプロパティはありません。

**例**

```json
{
    "name": "ImpalaDataset",
    "properties": {
        "type": "ImpalaObject",
        "linkedServiceName": {
            "referenceName": "<Impala linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

## <a name="copy-activity-properties"></a>コピー アクティビティのプロパティ

アクティビティの定義に利用できるセクションとプロパティの完全な一覧については、[パイプライン](concepts-pipelines-activities.md)に関する記事を参照してください。 このセクションでは、Impala ソース タイプでサポートされるプロパティの一覧を示します。

### <a name="impala-as-a-source-type"></a>ソース タイプとしての Impala

Impala からデータをコピーするには、コピー アクティビティのソース タイプを **ImpalaSource** に設定します。 コピー アクティビティの **source** セクションでは、次のプロパティがサポートされます。

| プロパティ | 説明 | 必須 |
|:--- |:--- |:--- |
| type | コピー アクティビティのソースの type プロパティは **ImpalaSource** に設定する必要があります。 | [はい] |
| クエリ | カスタム SQL クエリを使用してデータを読み取ります。 例: `"SELECT * FROM MyTable"`。 | [はい] |

**例:**

```json
"activities":[
    {
        "name": "CopyFromImpala",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Impala input dataset name>",
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
                "type": "ImpalaSource",
                "query": "SELECT * FROM MyTable"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

## <a name="next-steps"></a>次の手順
Data Factory のコピー アクティビティによってソースおよびシンクとしてサポートされるデータ ストアの一覧については、[サポートされるデータ ストア](copy-activity-overview.md#supported-data-stores-and-formats)の表をご覧ください。
