---
title: ServiceNow からデータをコピーする
titleSuffix: Azure Data Factory & Azure Synapse
description: Azure Data Factory または Synapse Analytics パイプラインでコピー アクティビティを使用して、ServiceNow からサポートされているシンク データ ストアにデータをコピーする方法について説明します。
ms.author: jianleishen
author: jianleishen
ms.service: data-factory
ms.subservice: data-movement
ms.topic: conceptual
ms.custom: synapse
ms.date: 09/09/2021
ms.openlocfilehash: 505874b03e5a5c187f0bb5ea638d09808ffb418c
ms.sourcegitcommit: 91915e57ee9b42a76659f6ab78916ccba517e0a5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/15/2021
ms.locfileid: "130045619"
---
# <a name="copy-data-from-servicenow-using-azure-data-factory-or-synapse-analytics"></a>Azure Data Factory または Synapse Analytics を使用して ServiceNow からデータをコピーする
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

この記事では、Azure Data Factory および Synapse Analytics パイプラインでコピー アクティビティを使用して、ServiceNow からデータをコピーする方法について説明します。 この記事は、コピー アクティビティの概要を示している[コピー アクティビティの概要](copy-activity-overview.md)に関する記事に基づいています。

## <a name="supported-capabilities"></a>サポートされる機能

この ServiceNow コネクタは、次のアクティビティでサポートされます。

- [サポートされるソース/シンク マトリックス](copy-activity-overview.md)での[コピー アクティビティ](copy-activity-overview.md)
- [Lookup アクティビティ](control-flow-lookup-activity.md)

ServiceNow から、サポートされている任意のシンク データ ストアにデータをコピーできます。 コピー アクティビティによってソースまたはシンクとしてサポートされているデータ ストアの一覧については、[サポートされているデータ ストア](copy-activity-overview.md#supported-data-stores-and-formats)に関する記事の表をご覧ください。

このサービスでは、接続を可能にする組み込みのドライバーが提供されます。  そのため、このコネクタを使用するドライバーを手動でインストールする必要はありません。

## <a name="getting-started"></a>作業の開始

[!INCLUDE [data-factory-v2-connector-get-started](includes/data-factory-v2-connector-get-started.md)]

## <a name="create-a-linked-service-to-servicenow-using-ui"></a>UI を使用して ServiceNow のリンク サービスを作成する

次の手順を使用して、Azure portal UI で ServiceNow のリンク サービスを作成します。

1. Azure Data Factory または Synapse ワークスペースの [管理] タブに移動し、[リンクされたサービス] を選択して、[新規] をクリックします。

    # <a name="azure-data-factory"></a>[Azure Data Factory](#tab/data-factory)

    :::image type="content" source="media/doc-common-process/new-linked-service.png" alt-text="Azure Data Factory の UI で新しいリンク サービスを作成する。":::

    # <a name="azure-synapse"></a>[Azure Synapse](#tab/synapse-analytics)

    :::image type="content" source="media/doc-common-process/new-linked-service-synapse.png" alt-text="Azure Synapse の UI を使用して新しいリンク サービスを作成します。":::

2. ServiceNow を検索し、ServiceNow コネクタを選択します。

    :::image type="content" source="media/connector-servicenow/servicenow-connector.png" alt-text="ServiceNow コネクタを選択します。":::    

1. サービスの詳細を構成し、接続をテストして、新しいリンク サービスを作成します。

    :::image type="content" source="media/connector-servicenow/configure-servicenow-linked-service.png" alt-text="ServiceNow のリンク サービスを構成します。":::

## <a name="connector-configuration-details"></a>コネクタの構成の詳細

次のセクションでは、ServiceNow コネクタに固有の Data Factory エンティティの定義に使用されるプロパティについて詳しく説明します。

## <a name="linked-service-properties"></a>リンクされたサービスのプロパティ

ServiceNow のリンクされたサービスでは、次のプロパティがサポートされます。

| プロパティ | 説明 | 必須 |
|:--- |:--- |:--- |
| type | type プロパティは、次のように設定する必要があります:**ServiceNow** | はい |
| endpoint | ServiceNow サーバーのエンドポイント (`http://<instance>.service-now.com`)。  | はい |
| authenticationType | 使用する認証の種類。 <br/>使用できる値は、以下のとおりです。**Basic**、**OAuth2** | はい |
| username | Basic および OAuth2 認証で ServiceNow サーバーへの接続に使用されるユーザー名。  | はい |
| password | Basic および OAuth2 認証のユーザー名に対応するパスワード。 このフィールドを SecureString とマークして安全に保存するか、[Azure Key Vault に保存されているシークレットを参照](store-credentials-in-key-vault.md)します。 | はい |
| clientId | OAuth2 認証のクライアント ID。  | いいえ |
| clientSecret | OAuth2 認証のクライアント シークレット。 このフィールドを SecureString とマークして安全に保存するか、[Azure Key Vault に保存されているシークレットを参照](store-credentials-in-key-vault.md)します。 | いいえ |
| useEncryptedEndpoints | データ ソースのエンドポイントが HTTPS を使用して暗号化されるかどうかを指定します。 既定値は、true です。  | いいえ |
| useHostVerification | TLS 経由で接続するときに、サーバーの証明書内のホスト名がサーバーのホスト名と一致する必要があるかどうか指定します。 既定値は、true です。  | いいえ |
| usePeerVerification | TLS 経由で接続するときに、サーバーの ID を検証するかどうかを指定します。 既定値は、true です。  | いいえ |

**例:**

```json
{
    "name": "ServiceNowLinkedService",
    "properties": {
        "type": "ServiceNow",
        "typeProperties": {
            "endpoint" : "http://<instance>.service-now.com",
            "authenticationType" : "Basic",
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

データセットを定義するために使用できるセクションとプロパティの完全な一覧については、[データセット](concepts-datasets-linked-services.md)に関する記事をご覧ください。 このセクションでは、ServiceNow データセットでサポートされるプロパティの一覧を示します。

ServiceNow からデータをコピーするには、データセットの type プロパティを **ServiceNowObject** に設定します。 次のプロパティがサポートされています。

| プロパティ | 説明 | 必須 |
|:--- |:--- |:--- |
| type | データセットの type プロパティは、次のように設定する必要があります:**ServiceNowObject** | はい |
| tableName | テーブルの名前。 | いいえ (アクティビティ ソースの "query" が指定されている場合) |

**例**

```json
{
    "name": "ServiceNowDataset",
    "properties": {
        "type": "ServiceNowObject",
        "typeProperties": {},
        "schema": [],
        "linkedServiceName": {
            "referenceName": "<ServiceNow linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

## <a name="copy-activity-properties"></a>コピー アクティビティのプロパティ

アクティビティの定義に利用できるセクションとプロパティの完全な一覧については、[パイプライン](concepts-pipelines-activities.md)に関する記事を参照してください。 このセクションでは、ServiceNow ソース でサポートされるプロパティの一覧を示します。

### <a name="servicenow-as-source"></a>ソースとしての ServiceNow

ServiceNow からデータをコピーするには、コピー アクティビティのソースの種類を **ServiceNowSource** に設定します。 コピー アクティビティの **source** セクションでは、次のプロパティがサポートされます。

| プロパティ | 説明 | 必須 |
|:--- |:--- |:--- |
| type | コピー アクティビティのソースの type プロパティは、次のように設定する必要があります:**ServiceNowSource** | はい |
| query | カスタム SQL クエリを使用してデータを読み取ります。 (例: `"SELECT * FROM Actual.alm_asset"`)。 | いいえ (データセットの "tableName" が指定されている場合) |

クエリで ServiceNow のスキーマと列を指定するときは、次のことに注意してください。また、**コピーのパフォーマンスの影響について、「[パフォーマンスに関するヒント](#performance-tips)」を参照してください**。

- **スキーマ:** ServiceNow のクエリでは、スキーマを `Actual` または `Display` として指定します。これは、[ServiceNow REST API](https://developer.servicenow.com/app.do#!/rest_api_doc?v=jakarta&id=r_AggregateAPI-GET) を呼び出す際に、`sysparm_display_value` パラメーター (true または false) として確認できます。 
- **列:** `Actual` スキーマの下の実際の値の列名は `[column name]_value` で、`Display` スキーマの下の表示値は `[column name]_display_value` です。 列名は、クエリで使用されているスキーマにマップされる必要があります。

**サンプル クエリ:** 
`SELECT col_value FROM Actual.alm_asset`または `SELECT col_display_value FROM Display.alm_asset`

**例:**

```json
"activities":[
    {
        "name": "CopyFromServiceNow",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<ServiceNow input dataset name>",
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
                "type": "ServiceNowSource",
                "query": "SELECT * FROM Actual.alm_asset"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```
## <a name="performance-tips"></a>パフォーマンスに関するヒント

### <a name="schema-to-use"></a>使用するスキーマ

ServiceNow には 2 つの異なるスキーマがあります。1 つは **"Actual"** で、実際のデータが返されます。もう 1 つは **"Display"** で、データの表示値が返されます。 

クエリ内にフィルターがある場合は、コピーのパフォーマンスがよい "Actual" スキーマを使用してください。 "Actual" スキーマに対してクエリを実行すると、ServiceNow ではデータをフェッチするときにネイティブにフィルターがサポートされて、フィルター処理された結果セットのみが返されます。一方、"Display" スキーマに対してクエリを実行すると、ADF ではすべてのデータが取得されて、内部的にフィルターが適用されます。

### <a name="index"></a>インデックス

ServiceNow のテーブル インデックスは、クエリのパフォーマンスの向上に役立ちます。「[Create a table index](https://docs.servicenow.com/bundle/quebec-platform-administration/page/administer/table-administration/task/t_CreateCustomIndex.html)」(テーブル インデックスを作成する) をご覧ください。

## <a name="lookup-activity-properties"></a>Lookup アクティビティのプロパティ

プロパティの詳細については、[Lookup アクティビティ](control-flow-lookup-activity.md)に関するページを参照してください。


## <a name="next-steps"></a>次のステップ
Copy アクティビティでソースおよびシンクとしてサポートされるデータ ストアの一覧については、[サポートされるデータ ストア](copy-activity-overview.md#supported-data-stores-and-formats)に関するセクションを参照してください。
