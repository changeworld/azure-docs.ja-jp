---
title: Azure Data Factory を使用して OData ソースからデータをコピーする | Microsoft Docs
description: Azure Data Factory パイプラインでコピー アクティビティを使用して、ODBC ソースからサポートされているシンク データ ストアへデータをコピーする方法について説明します。
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
ms.date: 05/22/2018
ms.author: jingwang
ms.openlocfilehash: c8bee6902fb74cb77c34395fd05c1c861b4f630e
ms.sourcegitcommit: c282021dbc3815aac9f46b6b89c7131659461e49
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/12/2018
ms.locfileid: "49166136"
---
# <a name="copy-data-from-an-odata-source-by-using-azure-data-factory"></a>Azure Data Factory を使用して OData ソースからデータをコピーする

> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Version 1](v1/data-factory-odata-connector.md)
> * [現在のバージョン](connector-odata.md)

この記事では、Azure Data Factory のコピー アクティビティを使用して OData ソースからデータをコピーする方法の概要について説明します。 この記事は、コピー アクティビティの概要が説明されている「[Azure Data Factory のコピー アクティビティ](copy-activity-overview.md)」を基に作成されています。

## <a name="supported-capabilities"></a>サポートされる機能

OData ストアから、サポートされている任意のシンク データ ストアにデータをコピーできます。 コピー アクティビティでソースおよびシンクとしてサポートされているデータ ストアの一覧については、「[サポートされるデータ ストアと形式](copy-activity-overview.md#supported-data-stores-and-formats)」を参照してください。

具体的には、この OData コネクタは以下をサポートします。

- OData バージョン 3.0 および 4.0。
- **匿名**、**基本**、**Windows** のいずれかの認証を使用したデータのコピー。

## <a name="get-started"></a>作業開始

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

以降のセクションでは、OData コネクタに固有の Data Factory エンティティを定義するために使用できるプロパティについて詳細に説明します。

## <a name="linked-service-properties"></a>リンクされたサービスのプロパティ

OData のリンクされたサービスでは、次のプロパティがサポートされます。

| プロパティ | 説明 | 必須 |
|:--- |:--- |:--- |
| type | **type** プロパティは **OData** に設定する必要があります。 |[はい] |
| url | OData サービスのルート URL。 |[はい] |
| authenticationType | OData ソースに接続するために使用される認証の種類。 使用できる値は **Anonymous**、**Basic**、および **Windows** です。 OAuth はサポートされていません。 | [はい] |
| userName | 基本認証または Windows 認証を使用する場合は、**userName** を指定します。 | いいえ  |
| password | **userName** に指定したユーザー アカウントの **password** を指定します。 Data Factory に安全に格納するには、このフィールドを **SecureString** 型として指定します。 また、[Azure Key Vault に格納されているシークレットを参照する](store-credentials-in-key-vault.md)こともできます。 | いいえ  |
| connectVia | データ ストアに接続するために使用される [Integration Runtime](concepts-integration-runtime.md)。 Azure Integration Runtime またはセルフホステッド統合ランタイムを選択できます (データ ストアがプライベート ネットワークに配置されている場合)。 指定されていない場合は、既定の Azure Integration Runtime が使用されます。 |いいえ  |

**例 1: 匿名認証の使用**

```json
{
    "name": "ODataLinkedService",
    "properties": {
        "type": "OData",
        "typeProperties": {
            "url": "http://services.odata.org/OData/OData.svc",
            "authenticationType": "Anonymous"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

**例 2: 基本認証の使用**

```json
{
    "name": "ODataLinkedService",
    "properties": {
        "type": "OData",
        "typeProperties": {
            "url": "<endpoint of OData source>",
            "authenticationType": "Basic",
            "userName": "<user name>",
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

**例 3: Windows 認証の使用**

```json
{
    "name": "ODataLinkedService",
    "properties": {
        "type": "OData",
        "typeProperties": {
            "url": "<endpoint of on-premises OData source>",
            "authenticationType": "Windows",
            "userName": "<domain>\\<user>",
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

このセクションでは、OData データセットでサポートされているプロパティの一覧を示します。

データセットの定義に使用できるセクションとプロパティの完全な一覧については、「[データセットとリンクされたサービス](concepts-datasets-linked-services.md)」を参照してください。 

OData からデータをコピーするには、データセットの **type** プロパティを **ODataResource** に設定します。 次のプロパティがサポートされています。

| プロパティ | 説明 | 必須 |
|:--- |:--- |:--- |
| type | データセットの **type** プロパティは **ODataResource** に設定する必要があります。 | [はい] |
| path | OData リソースへのパス。 | [はい] |

**例**

```json
{
    "name": "ODataDataset",
    "properties":
    {
        "type": "ODataResource",
        "linkedServiceName": {
            "referenceName": "<OData linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties":
        {
            "path": "Products"
        }
    }
}
```

## <a name="copy-activity-properties"></a>コピー アクティビティのプロパティ

このセクションでは、OData ソースでサポートされているプロパティの一覧を示します。

アクティビティの定義に利用できるセクションとプロパティの完全な一覧については、[パイプライン](concepts-pipelines-activities.md)に関する記事を参照してください。 

### <a name="odata-as-source"></a>ソースとしての OData

OData からデータをコピーするには、コピー アクティビティの **source** の種類を **RelationalSource** に設定します。 コピー アクティビティの **source** セクションでは、次のプロパティがサポートされます。

| プロパティ | 説明 | 必須 |
|:--- |:--- |:--- |
| type | コピー アクティビティのソースの **type** プロパティは **RelationalSource** に設定する必要があります。 | [はい] |
| query | データをフィルター処理するための OData クエリ オプション。 例: `"?$select=Name,Description&$top=5"`.<br/><br/>**注**: OData コネクタは、次の結合された URL からデータをコピーします`[URL specified in linked service]/[path specified in dataset][query specified in copy activity source]`。 詳細については、[OData の URL コンポーネント](http://www.odata.org/documentation/odata-version-3-0/url-conventions/)に関するページを参照してください。 | いいえ  |

**例**

```json
"activities":[
    {
        "name": "CopyFromOData",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<OData input dataset name>",
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
                "type": "RelationalSource",
                "query": "?$select=Name,Description&$top=5"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

## <a name="data-type-mapping-for-odata"></a>OData のデータ型マッピング

OData からデータをコピーする場合は、OData のデータ型と Azure Data Factory の中間データ型の間で次のマッピングが使用されます。 コピー アクティビティでソースのスキーマとデータ型がシンクにマッピングされる方法の詳細については、[スキーマとデータ型のマッピング](copy-activity-schema-and-type-mapping.md)に関するページを参照してください。

| OData のデータ型 | Data Factory の中間データ型 |
|:--- |:--- |
| Edm.Binary | Byte[] |
| Edm.Boolean | ブール値 |
| Edm.Byte | Byte[] |
| Edm.DateTime | Datetime |
| Edm.Decimal | Decimal |
| Edm.Double | Double |
| Edm.Single | Single |
| Edm.Guid | Guid |
| Edm.Int16 | Int16 |
| Edm.Int32 | Int32 |
| Edm.Int64 | Int64 |
| Edm.SByte | Int16 |
| Edm.String | String |
| Edm.Time | timespan |
| Edm.DateTimeOffset | DateTimeOffset |

> [!NOTE]
> OData の複雑なデータ型 (**Object** など) はサポートされていません。


## <a name="next-steps"></a>次の手順

Azure Data Factory のコピー アクティビティによってソースおよびシンクとしてサポートされるデータ ストアの一覧については、「[サポートされるデータ ストアと形式](copy-activity-overview.md##supported-data-stores-and-formats)」を参照してください。