---
title: Dynamics AX からデータをコピーする
description: Azure Data Factory または Synapse Analytics パイプラインでコピー アクティビティを使用して、Dynamics AX からサポートされているシンク データ ストアにデータをコピーする方法について説明します。
titleSuffix: Azure Data Factory & Azure Synapse
ms.author: jianleishen
author: jianleishen
ms.service: data-factory
ms.subservice: data-movement
ms.topic: conceptual
ms.custom: synapse
ms.date: 09/09/2021
ms.openlocfilehash: 249feb6b906db9c89b9b77dff022effe5bce4e83
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/13/2021
ms.locfileid: "124778003"
---
# <a name="copy-data-from-dynamics-ax-using-azure-data-factory-or-synapse-analytics"></a>Azure Data Factory または Synapse Analytics を使用して Dynamics AX からデータをコピーする

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

この記事では、Azure Data Factory および Azure Synapse Analytics パイプラインで Copy アクティビティを使用して、Dynamics AX からデータをコピーする方法について説明します。 この記事は、Copy アクティビティの概要を説明する [Copy アクティビティ](copy-activity-overview.md)に関する記事に基づいています。

## <a name="supported-capabilities"></a>サポートされる機能

この Dynamics AX コネクタは、次のアクティビティでサポートされます。

- [サポートされるソース/シンク マトリックス](copy-activity-overview.md)での[コピー アクティビティ](copy-activity-overview.md)
- [Lookup アクティビティ](control-flow-lookup-activity.md)

Dynamics AX から、サポートされている任意のシンク データ ストアにデータをコピーできます。 コピー アクティビティでソースおよびシンクとしてサポートされているデータ ストアの一覧については、「[サポートされるデータ ストアと形式](copy-activity-overview.md#supported-data-stores-and-formats)」を参照してください。

具体的には、この Dynamics AX コネクタは、**OData プロトコル** と **サービス プリンシパル認証** を使用した Dynamics AX からのデータ コピーをサポートしています。

>[!TIP]
>**Dynamics 365 Finance and Operations** からデータをコピーするために、このコネクタを使用することもできます。 Dynamics 365 の [OData のサポート](/dynamics365/unified-operations/dev-itpro/data-entities/odata)と[認証方法](/dynamics365/unified-operations/dev-itpro/data-entities/services-home-page#authentication)に関するページを参照してください。

## <a name="get-started"></a>はじめに

[!INCLUDE [data-factory-v2-connector-get-started](includes/data-factory-v2-connector-get-started.md)]

## <a name="create-a-linked-service-to-dynamics-ax-using-ui"></a>UI を使用して Dynamics AX のリンク サービスを作成する

次の手順を使用して、Azure portal UI で Dynamics AX のリンク サービスを作成します。

1. Azure Data Factory または Synapse ワークスペースの [管理] タブに移動し、[リンクされたサービス] を選択して、[新規] をクリックします。

    # <a name="azure-data-factory"></a>[Azure Data Factory](#tab/data-factory)

    :::image type="content" source="media/doc-common-process/new-linked-service.png" alt-text="Azure Data Factory の UI で新しいリンク サービスを作成する。":::

    # <a name="azure-synapse"></a>[Azure Synapse](#tab/synapse-analytics)

    :::image type="content" source="media/doc-common-process/new-linked-service-synapse.png" alt-text="Azure Synapse の UI を使用して新しいリンク サービスを作成します。":::

2. Dynamics を検索し、Dynamics AX コネクタを選択します。

    :::image type="content" source="media/connector-dynamics-ax/dynamics-ax-connector.png" alt-text="Dynamics AX コネクタを選択します。":::    

1. サービスの詳細を構成し、接続をテストして、新しいリンク サービスを作成します。

    :::image type="content" source="media/connector-dynamics-ax/configure-dynamics-ax-linked-service.png" alt-text="Dynamics AX のリンク サービスを構成します。":::

## <a name="connector-configuration-details"></a>コネクタの構成の詳細

以降のセクションでは、Dynamics AX コネクタに固有の Data Factory エンティティを定義するために使用できるプロパティについて詳細に説明します。

## <a name="prerequisites"></a>前提条件

サービス プリンシパル認証を使用するには、次の手順のようにします。

1. 「[アプリケーションを Azure AD テナントに登録する](../storage/common/storage-auth-aad-app.md#register-your-application-with-an-azure-ad-tenant)」に従って、Azure Active Directory (Azure AD) にアプリケーション エンティティを登録します。 次の値を記録しておきます。リンクされたサービスを定義するときに使います。

    - アプリケーション ID
    - アプリケーション キー
    - テナント ID

2. Dynamics AX に移動し、このサービス プリンシパルに Dynamics AX にアクセスするための適切なアクセス許可を付与します。

## <a name="linked-service-properties"></a>リンクされたサービスのプロパティ

Dynamics AX のリンクされたサービスでは、次のプロパティがサポートされます。

| プロパティ | 説明 | 必須 |
|:--- |:--- |:--- |
| type | **type** プロパティを **DynamicsAX** に設定する必要があります。 |はい |
| url | Dynamics AX (または Dynamics 365 Finance および Operations) インスタンスの OData エンドポイント。 |はい |
| servicePrincipalId | アプリケーションのクライアント ID を取得します。 | はい |
| servicePrincipalKey | アプリケーションのキーを取得します。 このフィールドを **SecureString** とマークして安全に保存するか、[Azure Key Vault に保存されているシークレットを参照](store-credentials-in-key-vault.md)します。 | はい |
| tenant | アプリケーションが存在するテナントの情報 (ドメイン名またはテナント ID) を指定します。 Azure portal の右上隅にマウスを置くことで取得します。 | はい |
| aadResourceId | 認可を要求する AAD リソースを指定します。 たとえば、Dynamics の URL が `https://sampledynamics.sandbox.operations.dynamics.com/data/` である場合、対応する AAD リソースは通常 `https://sampledynamics.sandbox.operations.dynamics.com` となります。 | はい |
| connectVia | データ ストアに接続するために使用される [Integration Runtime](concepts-integration-runtime.md)。 Azure Integration Runtime またはセルフホステッド統合ランタイムを選択できます (データ ストアがプライベート ネットワークに配置されている場合)。 指定されていない場合は、既定の Azure Integration Runtime が使用されます。 |いいえ |

**例**

```json
{
    "name": "DynamicsAXLinkedService",
    "properties": {
        "type": "DynamicsAX",
        "typeProperties": {
            "url": "<Dynamics AX instance OData endpoint>",
            "servicePrincipalId": "<service principal id>",
            "servicePrincipalKey": {
                "type": "SecureString",
                "value": "<service principal key>"
            },
            "tenant": "<tenant info, e.g. microsoft.onmicrosoft.com>",
            "aadResourceId": "<AAD resource, e.g. https://sampledynamics.sandbox.operations.dynamics.com>"
        }
    },
    "connectVia": {
        "referenceName": "<name of Integration Runtime>",
        "type": "IntegrationRuntimeReference"
    }
}

```

## <a name="dataset-properties"></a>データセットのプロパティ

このセクションでは、Dynamics AX データセットでサポートされているプロパティの一覧を示します。

データセットの定義に使用できるセクションとプロパティの完全な一覧については、「[データセットとリンクされたサービス](concepts-datasets-linked-services.md)」を参照してください。 

Dynamics AX からデータをコピーするには、データセットの **type** プロパティを **DynamicsAXResource** に設定します。 次のプロパティがサポートされています。

| プロパティ | 説明 | 必須 |
|:--- |:--- |:--- |
| type | データセットの **type** プロパティは、**DynamicsAXResource** に設定する必要があります。 | はい |
| path | Dynamics AX OData エンティティのパス。 | はい |

**例**

```json
{
    "name": "DynamicsAXResourceDataset",
    "properties": {
        "type": "DynamicsAXResource",
        "typeProperties": {
            "path": "<entity path e.g. dd04tentitySet>"
        },
        "schema": [],
        "linkedServiceName": {
            "referenceName": "<Dynamics AX linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

## <a name="copy-activity-properties"></a>コピー アクティビティのプロパティ

このセクションでは、Dynamics AX ソースでサポートされているプロパティの一覧を示します。

アクティビティの定義に利用できるセクションとプロパティの完全な一覧については、[パイプライン](concepts-pipelines-activities.md)に関する記事を参照してください。 

### <a name="dynamics-ax-as-source"></a>ソースとしての Dynamics AX

Dynamics AX からデータをコピーするは、コピー アクティビティの **source** の種類を **DynamicsAXSource** に設定します。 コピー アクティビティの **source** セクションでは、次のプロパティがサポートされます。

| プロパティ | 説明 | 必須 |
|:--- |:--- |:--- |
| type | コピー アクティビティのソースの **type** プロパティは **DynamicsAXSource** に設定する必要があります | はい |
| query | データをフィルター処理するための OData クエリ オプション。 例: `"?$select=Name,Description&$top=5"`.<br/><br/>**注**:コネクタは、次の結合された URL からデータをコピーします。`[URL specified in linked service]/[path specified in dataset][query specified in copy activity source]` 詳細については、[OData の URL コンポーネント](https://www.odata.org/documentation/odata-version-3-0/url-conventions/)に関するページを参照してください。 | いいえ |
| httpRequestTimeout | HTTP 要求が応答を取得する際のタイムアウト (**TimeSpan** 値)。 この値は、応答データの読み取りのタイムアウトではなく、応答の取得のタイムアウトです。 指定しない場合は、既定値の **00:30:00** (30 分) が使用されます。 | いいえ |

**例**

```json
"activities":[
    {
        "name": "CopyFromDynamicsAX",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Dynamics AX input dataset name>",
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
                "type": "DynamicsAXSource",
                "query": "$top=10"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```


## <a name="lookup-activity-properties"></a>Lookup アクティビティのプロパティ

プロパティの詳細については、[Lookup アクティビティ](control-flow-lookup-activity.md)に関するページを参照してください。

## <a name="next-steps"></a>次のステップ

コピー アクティビティでソースおよびシンクとしてサポートされているデータ ストアの一覧については、「[サポートされるデータ ストアと形式](copy-activity-overview.md#supported-data-stores-and-formats)」を参照してください。