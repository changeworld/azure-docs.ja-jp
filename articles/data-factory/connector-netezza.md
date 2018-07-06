---
title: Azure Data Factory を使用して Netezza からデータをコピーする | Microsoft Docs
description: Azure Data Factory パイプラインでコピー アクティビティを使用して、Netezza のデータをサポートされているシンク データ ストアにコピーする方法について説明します。
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
ms.openlocfilehash: f8c10e2200f830ea6e568e7b3fba1f0a6085cef2
ms.sourcegitcommit: 0c490934b5596204d175be89af6b45aafc7ff730
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/27/2018
ms.locfileid: "37055658"
---
# <a name="copy-data-from-netezza-using-azure-data-factory"></a>Azure Data Factory を使用して Netezza からデータをコピーする 

この記事では、Azure Data Factory のコピー アクティビティを使用して、Netezza からデータをコピーする方法について説明します。 この記事は、コピー アクティビティの概要を示している[コピー アクティビティの概要](copy-activity-overview.md)に関する記事に基づいています。

## <a name="supported-capabilities"></a>サポートされる機能

Netezza から、サポートされている任意のシンク データ ストアにデータをコピーできます。 コピー アクティビティによってソースまたはシンクとしてサポートされているデータ ストアの一覧については、[サポートされているデータ ストア](copy-activity-overview.md#supported-data-stores-and-formats)に関する記事の表をご覧ください。

Azure Data Factory では接続を有効にする組み込みのドライバーが提供されるので、このコネクタを使用してドライバーを手動でインストールする必要はありません。

## <a name="getting-started"></a>使用の開始

コピー アクティビティを含むパイプラインは、.NET SDK、Python SDK、Azure PowerShell、REST API、または Azure Resource Manager テンプレートを使用して作成できます。 コピー アクティビティを含むパイプラインを作成するための詳細な手順については、[コピー アクティビティのチュートリアル](quickstart-create-data-factory-dot-net.md)を参照してください。

次のセクションでは、Netezza コネクタに固有の Data Factory エンティティの定義に使用されるプロパティについて詳しく説明します。

## <a name="linked-service-properties"></a>リンクされたサービスのプロパティ

Netezza のリンクされたサービスでは、次のプロパティがサポートされます。

| プロパティ | 説明 | 必須 |
|:--- |:--- |:--- |
| type | type プロパティを **Netezza** に設定する必要があります | [はい] |
| connectionString | Netezza に接続するための ODBC 接続文字列。 このフィールドを SecureString としてマークして Data Factory に安全に保管するか、[Azure Key Vault に格納されているシークレットを参照](store-credentials-in-key-vault.md)します。 | [はい] |
| connectVia | データ ストアに接続するために使用される[統合ランタイム](concepts-integration-runtime.md)。 セルフホステッド統合ランタイムまたは Azure 統合ランタイム (データ ストアがパブリックにアクセスできる場合) を使用できます。 指定されていない場合は、既定の Azure 統合ランタイムが使用されます。 |いいえ  |

一般的な接続文字列は `Server=<server>;Port=<port>;Database=<database>;UID=<user name>;PWD=<password>` です。 ケースごとにさらに多くのプロパティを設定できます。

| プロパティ | 説明 | 必須 |
|:--- |:--- |:--- |:--- |
| SecurityLevel | ドライバーがデータ ストアへの接続に使用するセキュリティ (SSL/TLS) のレベル。 例:  `SecurityLevel=preferredSecured` サポートされる値は次のとおりです。<br/>- セキュリティで保護されていない接続のみ (**onlyUnSecured**): ドライバーは SSL を使用しません。<br/>- **セキュリティで保護されていない接続を推奨 (preferredUnSecured) (既定値)** : サーバーで選択できる場合、ドライバーは SSL を使用しません。 <br/>- **セキュリティで保護されている接続を推奨 (preferredSecured)**: サーバーで選択できる場合、ドライバーは SSL を使用します。 <br/>- **セキュリティで保護されている接続のみ (onlySecured)**: SSL 接続を利用できない場合、ドライバーは接続しません | いいえ  |
| CaCertFile | サーバーによって使用される SSL 証明書の完全パス。 例:  `CaCertFile=<cert path>;`| はい (SSL が有効になっている場合) |

**例:**

```json
{
    "name": "NetezzaLinkedService",
    "properties": {
        "type": "Netezza",
        "typeProperties": {
            "connectionString": {
                 "type": "SecureString",
                 "value": "Server=<server>;Port=<port>;Database=<database>;UID=<user name>;PWD=<password>"
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

データセットを定義するために使用できるセクションとプロパティの完全な一覧については、[データセット](concepts-datasets-linked-services.md)に関する記事をご覧ください。 このセクションでは、Netezza データセット でサポートされるプロパティの一覧を示します。

Netezza からデータをコピーするには、データセットの type プロパティを **NetezzaTable** に設定します。 この種類のデータセットに追加の種類固有のプロパティはありません。

**例**

```json
{
    "name": "NetezzaDataset",
    "properties": {
        "type": "NetezzaTable",
        "linkedServiceName": {
            "referenceName": "<Netezza linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

## <a name="copy-activity-properties"></a>コピー アクティビティのプロパティ

アクティビティの定義に利用できるセクションとプロパティの完全な一覧については、[パイプライン](concepts-pipelines-activities.md)に関する記事を参照してください。 このセクションでは、Netezza ソースでサポートされるプロパティの一覧を示します。

### <a name="netezza-as-source"></a>ソースとしての Netezza

Netezza からデータをコピーするには、コピー アクティビティのソース タイプを **NetezzaSource** に設定します。 コピー アクティビティの **source** セクションでは、次のプロパティがサポートされます。

| プロパティ | 説明 | 必須 |
|:--- |:--- |:--- |
| type | コピー アクティビティのソースの type プロパティを **NetezzaSource** に設定する必要があります。 | [はい] |
| クエリ | カスタム SQL クエリを使用してデータを読み取ります。 たとえば、「 `"SELECT * FROM MyTable"`」のように入力します。 | [はい] |

**例:**

```json
"activities":[
    {
        "name": "CopyFromNetezza",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Netezza input dataset name>",
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
                "type": "NetezzaSource",
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
