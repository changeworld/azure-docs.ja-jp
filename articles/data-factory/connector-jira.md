---
title: Azure Data Factory を使用して Jira からデータをコピーする | Microsoft Docs
description: Azure Data Factory パイプラインでコピー アクティビティを使用して、Jira のデータをサポートされているシンク データ ストアにコピーする方法について説明します。
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
ms.openlocfilehash: 743c0322152b555137b2bc37641377c3cfb3d0b2
ms.sourcegitcommit: 0c490934b5596204d175be89af6b45aafc7ff730
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/27/2018
ms.locfileid: "37054621"
---
# <a name="copy-data-from-jira-using-azure-data-factory"></a>Azure Data Factory を使用して Jira からデータをコピーする

この記事では、Azure Data Factory のコピー アクティビティを使用して、Jira からデータをコピーする方法について説明します。 この記事は、コピー アクティビティの概要を示している[コピー アクティビティの概要](copy-activity-overview.md)に関する記事に基づいています。

> [!IMPORTANT]
> このコネクタは、現在プレビューの段階です。 実際にお試しいただき、フィードバックをお寄せください。 ソリューションでプレビュー版コネクタの依存関係を取得したい場合、[Azure サポート](https://azure.microsoft.com/support/)にお問い合わせください。

## <a name="supported-capabilities"></a>サポートされる機能

Jira から、サポートされている任意のシンク データ ストアにデータをコピーできます。 コピー アクティビティによってソースまたはシンクとしてサポートされているデータ ストアの一覧については、[サポートされているデータ ストア](copy-activity-overview.md#supported-data-stores-and-formats)に関する記事の表をご覧ください。

Azure Data Factory では接続を有効にする組み込みのドライバーが提供されるので、このコネクタを使用してドライバーを手動でインストールする必要はありません。

## <a name="getting-started"></a>使用の開始

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

次のセクションでは、Jira コネクタに固有の Data Factory エンティティの定義に使用されるプロパティについて詳しく説明します。

## <a name="linked-service-properties"></a>リンクされたサービスのプロパティ

Jira のリンクされたサービスでは、次のプロパティがサポートされます。

| プロパティ | 説明 | 必須 |
|:--- |:--- |:--- |
| type | type プロパティを **Jira** に設定する必要があります | [はい] |
| host | Jira サービスの IP アドレスまたはホスト名。 (たとえば、jira.example.com)  | [はい] |
| ポート | Jira サーバーがクライアント接続のリッスンに使用する TCP ポート。 既定値は、HTTPS 経由で接続する場合は 443、HTTP 経由で接続する場合は 8080 です。  | いいえ  |
| username | Jira サービスへのアクセスに使用するユーザー名。  | [はい] |
| password | username フィールドに指定したユーザー名に対応するパスワード。 このフィールドを SecureString としてマークして Data Factory に安全に保管するか、[Azure Key Vault に格納されているシークレットを参照](store-credentials-in-key-vault.md)します。 | [はい] |
| useEncryptedEndpoints | データ ソースのエンドポイントが HTTPS を使用して暗号化されるかどうかを指定します。 既定値は true です。  | いいえ  |
| useHostVerification | SSL 経由で接続するときに、サーバーの証明書内のホスト名がサーバーのホスト名と一致する必要があるかどうかを指定します。 既定値は true です。  | いいえ  |
| usePeerVerification | SSL 経由で接続するときに、サーバーの ID を検証するかどうかを指定します。 既定値は true です。  | いいえ  |

**例:**

```json
{
    "name": "JiraLinkedService",
    "properties": {
        "type": "Jira",
        "typeProperties": {
            "host" : "<host>",
            "port" : "<port>",
            "username" : "<username>",
            "password": {
                 "type": "SecureString",
                 "value": "<password>"
            }
        }
    }
}
```

## <a name="dataset-properties"></a>データセットのプロパティ

データセットを定義するために使用できるセクションとプロパティの完全な一覧については、[データセット](concepts-datasets-linked-services.md)に関する記事をご覧ください。 このセクションでは、Jira データセットでサポートされるプロパティの一覧を示します。

Jira からデータをコピーするには、データセットの type プロパティを **JiraObject** に設定します。 この種類のデータセットに追加の種類固有のプロパティはありません。

**例**

```json
{
    "name": "JiraDataset",
    "properties": {
        "type": "JiraObject",
        "linkedServiceName": {
            "referenceName": "<Jira linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

## <a name="copy-activity-properties"></a>コピー アクティビティのプロパティ

アクティビティの定義に利用できるセクションとプロパティの完全な一覧については、[パイプライン](concepts-pipelines-activities.md)に関する記事を参照してください。 このセクションでは、Jira ソースでサポートされるプロパティの一覧を示します。

### <a name="jirasource-as-source"></a>ソースとしての JiraSource

Jira からデータをコピーするは、コピー アクティビティのソースの種類を **JiraSource** に設定します。 コピー アクティビティの **source** セクションでは、次のプロパティがサポートされます。

| プロパティ | 説明 | 必須 |
|:--- |:--- |:--- |
| type | コピー アクティビティのソースの type プロパティを **JiraSource** に設定する必要があります | [はい] |
| クエリ | カスタム SQL クエリを使用してデータを読み取ります。 たとえば、「 `"SELECT * FROM MyTable"`」のように入力します。 | [はい] |

**例:**

```json
"activities":[
    {
        "name": "CopyFromJira",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Jira input dataset name>",
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
                "type": "JiraSource",
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
Azure Data Factory のコピー アクティビティによってソースおよびシンクとしてサポートされるデータ ストアの一覧については、[サポートされるデータ ストア](copy-activity-overview.md#supported-data-stores-and-formats)の表をご覧ください。
