---
title: Azure Data Factory を使用して Azure Data Explorer をコピー先またはコピー元としてデータをコピーする | Microsoft Docs
description: Azure Data Factory パイプラインでコピー アクティビティを使用して、Azure Data Explorer との間で双方向にデータをコピーする方法について説明します。
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
ms.date: 02/01/2019
ms.author: orspod
ms.openlocfilehash: 8f2a7a953ce2964645c281d9454a73b0cf1a8ff6
ms.sourcegitcommit: 947b331c4d03f79adcb45f74d275ac160c4a2e83
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/05/2019
ms.locfileid: "55747190"
---
# <a name="copy-data-to-or-from-azure-data-explorer-using-azure-data-factory"></a>Azure Data Factory を使用して Azure Data Explorer をコピー先またはコピー元としてデータをコピーする

この記事では、Azure Data Factory のコピー アクティビティを使用して、[Azure Data Explorer](../data-explorer/data-explorer-overview.md) との間で双方向にデータをコピーする方法について説明します。 この記事は、コピー アクティビティの概要を示している[コピー アクティビティの概要](copy-activity-overview.md)に関する記事に基づいています。

## <a name="supported-capabilities"></a>サポートされる機能

Azure Data Explorer には、サポートされているソース データ ストアからデータをコピーすることができます。 また、Azure Data Explorer のデータを、サポートされているシンク データ ストアにコピーできます。 コピー アクティビティによってソースまたはシンクとしてサポートされるデータ ストアの一覧については、[サポートされるデータ ストア](copy-activity-overview.md)に関する記事の表をご覧ください。

>[!NOTE]
>現時点では、セルフホステッド統合ランタイムを使用して、Azure Data Explorer とオンプレミス データ ストアの間で双方向にデータをコピーすることは、まだサポートされていません。

Azure Data Explorer コネクタを使用すると、次のことができます。

* Azure Active Directory (Azure AD) アプリケーション トークン認証と**サービス プリンシパル**を使用して、データをコピーする。
* ソースとして、KQL (Kusto) クエリを使用してデータを取得する。
* シンクとして、コピー先テーブルにデータを追加する。

## <a name="getting-started"></a>使用の開始

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

以下のセクションでは、Azure Data Explorer コネクタに固有の Data Factory エンティティを定義するために使用されるプロパティの詳細を説明します。

## <a name="linked-service-properties"></a>リンクされたサービスのプロパティ

Azure Data Explorer のリンクされたサービスでは、次のプロパティがサポートされます。

| プロパティ | 説明 | 必須 |
|:--- |:--- |:--- |
| type | **type** プロパティは、**AzureDataExplorer** に設定する必要があります | はい |
| endpoint | Azure Data Explorer クラスターのエンドポイント URL。形式は `https://<clusterName>.<regionName>.kusto.windows.net ` です。 | はい |
| database | データベースの名前。 | はい |
| tenant | アプリケーションが存在するテナントの情報 (ドメイン名またはテナント ID) を指定します。 Azure portal の右上隅にマウスを置くことで取得します。 | はい |
| servicePrincipalId | アプリケーションのクライアント ID を取得します。 | はい |
| servicePrincipalKey | アプリケーションのキーを取得します。 このフィールドを **SecureString** としてマークして Data Factory に安全に保管するか、[Azure Key Vault に格納されているシークレットを参照](store-credentials-in-key-vault.md)します。 | はい |

**リンクされたサービスのプロパティの例:**

```json
{
    "name": "AzureDataExplorerLinkedService",
    "properties": {
        "type": "AzureDataExplorer",
        "typeProperties": {
            "endpoint": "https://<clusterName>.<regionName>.kusto.windows.net ",
            "database": "<database name>",
            "tenant": "<tenant name/id e.g. microsoft.onmicrosoft.com>",
            "servicePrincipalId": "<service principal id>",
            "servicePrincipalKey": {
                "type": "SecureString",
                "value": "<service principal key>"
            }
        }
    }
}
```

## <a name="dataset-properties"></a>データセットのプロパティ

データセットを定義するために使用できるセクションとプロパティの完全な一覧については、[データセット](concepts-datasets-linked-services.md)に関する記事をご覧ください。 このセクションでは、Azure Data Explorer データセットでサポートされるプロパティの一覧を示します。

Azure Data Explorer にデータをコピーするには、データセットの type プロパティを **AzureDataExplorerTable** に設定します。

次のプロパティがサポートされています。

| プロパティ | 説明 | 必須 |
|:--- |:--- |:--- |
| type | **type** プロパティは、**AzureDataExplorerTable** に設定する必要があります | はい |
| table | リンクされたサービスが参照するテーブルの名前。 | シンクの場合は Yes、ソースの場合は No |

**データセットのプロパティの例**

```json
{
   "name": "AzureDataExplorerDataset",
    "properties": {
        "type": "AzureDataExplorerTable",
        "linkedServiceName": {
            "referenceName": "<Azure Data Explorer linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "table": "<table name>"
        }
    }
}
```

## <a name="copy-activity-properties"></a>コピー アクティビティのプロパティ

アクティビティの定義に利用できるセクションとプロパティの完全な一覧については、[パイプライン](concepts-pipelines-activities.md)に関する記事を参照してください。 このセクションでは、Azure Data Explorer のソースとシンクでサポートされるプロパティの一覧を示します。

### <a name="azure-data-explorer-as-source"></a>ソースとしての Azure Data Explorer

Azure Data Explorer からデータをコピーするには、コピー アクティビティ ソースの **type** プロパティを **AzureDataExplorerSource** に設定します。 コピー アクティビティの **source** セクションでは、次のプロパティがサポートされます。

| プロパティ | 説明 | 必須 |
|:--- |:--- |:--- |
| type | コピー アクティビティのソースの **type** プロパティを、次の値に設定する必要があります:**AzureDataExplorerSource** | はい |
| query | [KQL 形式](/azure/kusto/query/)で指定された読み取り専用要求。 参照としてカスタム KQL クエリを使用します。 | はい |
| queryTimeout | クエリ要求がタイムアウトするまでの待機時間。既定値は 10 分 (00:10:00)、許容される最大値は 1 時間 (01:00:00) です。 | いいえ  |

**例:**

```json
"activities":[
    {
        "name": "CopyFromAzureDataExplorer",
        "type": "Copy",
        "typeProperties": {
            "source": {
                "type": "AzureDataExplorerSource",
                "query": "TestTable1 | take 10",
                "queryTimeout": "00:10:00"
            },
            "sink": {
                "type": "<sink type>"
            }
        },
        "inputs": [
            {
                "referenceName": "<Azure Data Explorer input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<output dataset name>",
                "type": "DatasetReference"
            }
        ]
    }
]
```

### <a name="azure-data-explorer-as-sink"></a>シンクとしての Azure Data Explorer

Azure Data Explorer にデータをコピーするには、コピー アクティビティ シンクの type プロパティを **AzureDataExplorerSink** に設定します。 コピー アクティビティの **sink** セクションでは、次のプロパティがサポートされます。

| プロパティ | 説明 | 必須 |
|:--- |:--- |:--- |
| type | コピー アクティビティのシンクの **type** プロパティは、次のように設定する必要があります:**AzureDataExplorerSink** | はい |
| ingestionMappingName | Kusto テーブルで事前作成済みの [csv マッピング](/azure/kusto/management/mappings#csv-mapping)の名前。 ソースから Azure Data Explore に列をマップするには、コピー アクティビティの[列マッピング](copy-activity-schema-and-type-mapping.md)も使用できます。 | いいえ  |

**例:**

```json
"activities":[
    {
        "name": "CopyToAzureDataExplorer",
        "type": "Copy",
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "AzureDataExplorerSink",
                "ingestionMappingName": "<optional csv mapping name>"
            }
        },
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Azure Data Explorer output dataset name>",
                "type": "DatasetReference"
            }
        ]
    }
]
```

## <a name="next-steps"></a>次の手順
Azure Data Factory のコピー アクティビティによってソースおよびシンクとしてサポートされるデータ ストアの一覧については、[サポートされるデータ ストア](copy-activity-overview.md#supported-data-stores-and-formats)の表をご覧ください。