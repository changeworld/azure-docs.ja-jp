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
ms.openlocfilehash: 1b7499990a049f276bf1af9e31b639ea4944d8f7
ms.sourcegitcommit: c282021dbc3815aac9f46b6b89c7131659461e49
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/12/2018
ms.locfileid: "49167570"
---
# <a name="copy-data-from-netezza-by-using-azure-data-factory"></a>Azure Data Factory を使用して Netezza からデータをコピーする 

この記事では、Azure Data Factory のコピー アクティビティを使用して、Netezza からデータをコピーする方法について説明します。 この記事は、コピー アクティビティの概要が説明されている「[Azure Data Factory のコピー アクティビティ](copy-activity-overview.md)」を基に作成されています。

## <a name="supported-capabilities"></a>サポートされる機能

Netezza から、サポートされている任意のシンク データ ストアにデータをコピーできます。 コピー アクティビティでソースおよびシンクとしてサポートされているデータ ストアの一覧については、「[サポートされるデータ ストアと形式](copy-activity-overview.md#supported-data-stores-and-formats)」をご覧ください。

Azure Data Factory では、接続を可能にする組み込みのドライバーが提供されます。 このコネクタを使用するためにドライバーを手動でインストールする必要はありません。

## <a name="get-started"></a>作業開始

コピー アクティビティを使用するパイプラインは、.NET SDK、Python SDK、Azure PowerShell、REST API、または Azure Resource Manager テンプレートを使用して作成できます。 コピー アクティビティを使用するパイプラインを作成する詳細な手順については、[コピー アクティビティのチュートリアル](quickstart-create-data-factory-dot-net.md)をご覧ください。

次のセクションでは、Netezza コネクタに固有の Data Factory エンティティの定義に使用できるプロパティについて詳しく説明します。

## <a name="linked-service-properties"></a>リンクされたサービスのプロパティ

Netezza のリンクされたサービスでは、次のプロパティがサポートされます。

| プロパティ | 説明 | 必須 |
|:--- |:--- |:--- |
| type | **type** プロパティを **Netezza** に設定する必要があります。 | [はい] |
| connectionString | Netezza に接続するための ODBC 接続文字列。 Data Factory に安全に格納するには、このフィールドを **SecureString** 型として指定します。 [Azure Key Vault に格納されているシークレットを参照する](store-credentials-in-key-vault.md)こともできます。 | [はい] |
| connectVia | データ ストアに接続するために使用される[統合ランタイム](concepts-integration-runtime.md)。 セルフホステッド統合ランタイムまたは Azure Integration Runtime (データ ストアがパブリックにアクセスできる場合) を選択できます。 指定されていない場合は、既定の Azure Integration Runtime が使用されます。 |いいえ  |

一般的な接続文字列は `Server=<server>;Port=<port>;Database=<database>;UID=<user name>;PWD=<password>` です。 次の表では、設定できる他のプロパティについて説明します。

| プロパティ | 説明 | 必須 |
|:--- |:--- |:--- |
| SecurityLevel | ドライバーがデータ ストアへの接続に使用するセキュリティ (SSL/TLS) のレベル。 例: `SecurityLevel=preferredSecured`. サポートされる値は次のとおりです。<br/>- **セキュリティで保護されていない接続のみ** (**onlyUnSecured**): ドライバーは SSL を使用しません。<br/>- **セキュリティで保護されていない接続を推奨 (preferredUnSecured) (既定値)**: サーバーで選択できる場合、ドライバーは SSL を使用しません。 <br/>- **セキュリティで保護されている接続を推奨 (preferredSecured)**: サーバーで選択できる場合、ドライバーは SSL を使用します。 <br/>- **セキュリティで保護されている接続のみ (onlySecured)**: SSL 接続を利用できない場合、ドライバーは接続しません。 | いいえ  |
| CaCertFile | サーバーによって使用される SSL 証明書の完全パス。 例: `CaCertFile=<cert path>;`| はい (SSL が有効になっている場合) |

**例**

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

このセクションでは、Netezza データセットでサポートされているプロパティの一覧を示します。

データセットの定義に使用できるセクションとプロパティの完全な一覧については、[データセット](concepts-datasets-linked-services.md)に関するページをご覧ください。 

Netezza からデータをコピーするには、データセットの **type** プロパティを **NetezzaTable** に設定します。 この種類のデータセットに追加の種類固有のプロパティはありません。

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

このセクションでは、Netezza ソースでサポートされているプロパティの一覧を示します。

アクティビティの定義に利用できるセクションとプロパティの完全な一覧については、[パイプライン](concepts-pipelines-activities.md)に関する記事をご覧ください。 

### <a name="netezza-as-source"></a>ソースとしての Netezza

Netezza からデータをコピーするには、コピー アクティビティの **source** のタイプを **NetezzaSource** に設定します。 コピー アクティビティの **source** セクションでは、次のプロパティがサポートされます。

| プロパティ | 説明 | 必須 |
|:--- |:--- |:--- |
| type | コピー アクティビティのソースの **type** プロパティを **NetezzaSource** に設定する必要があります。 | [はい] |
| query | カスタム SQL クエリを使用してデータを読み取ります。 例: `"SELECT * FROM MyTable"` | [はい] |

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

Azure Data Factory のコピー アクティビティによってソースおよびシンクとしてサポートされるデータ ストアの一覧については、「[サポートされるデータ ストアと形式](copy-activity-overview.md#supported-data-stores-and-formats)」をご覧ください。
