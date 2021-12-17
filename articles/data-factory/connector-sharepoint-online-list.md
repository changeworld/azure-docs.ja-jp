---
title: SharePoint Online リストからデータをコピーする
titleSuffix: Azure Data Factory & Azure Synapse
description: Azure Data Factory または Azure Synapse Analytics パイプラインでコピー アクティビティを使用して、SharePoint Online リストから、サポートされているシンク データ ストアへデータをコピーする方法について説明します。
author: jianleishen
ms.service: data-factory
ms.subservice: data-movement
ms.custom: synapse
ms.topic: conceptual
ms.date: 09/09/2021
ms.author: jianleishen
ms.openlocfilehash: ad9adb2c9f6b8308aba79f9e9bd1eadbf755760a
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/13/2021
ms.locfileid: "124763939"
---
# <a name="copy-data-from-sharepoint-online-list-by-using-azure-data-factory-or-azure-synapse-analytics"></a>Azure Data Factory または Azure Synapse Analytics を使用して SharePoint Online リストからデータをコピーする
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

この記事では、Azure Data Factory と Azure Synapse パイプラインのコピー アクティビティを使用して、SharePoint Online リストから、または SharePoint Online リストにデータをコピーする方法について説明します。 この記事は、Copy アクティビティの概要を説明する [Copy アクティビティ](copy-activity-overview.md)に関する記事に基づいています。

## <a name="supported-capabilities"></a>サポートされる機能

この SharePoint Online リスト コネクタは、次のアクティビティでサポートされています。

- [サポートされるソース/シンク マトリックス](copy-activity-overview.md)での[コピー アクティビティ](copy-activity-overview.md)
- [Lookup アクティビティ](control-flow-lookup-activity.md)

SharePoint Online リストから、任意のサポートされているシンク データ ストアにデータをコピーできます。 コピー アクティビティでソースおよびシンクとしてサポートされているデータ ストアの一覧については、「[サポートされるデータ ストアと形式](copy-activity-overview.md#supported-data-stores-and-formats)」を参照してください。

具体的には、この SharePoint List Online コネクタでは、サービス プリンシパル認証を使用して OData プロトコルを介してデータが取得されます。

> [!TIP]
> このコネクタは、ファイルではなく、SharePoint Online **リスト** からのデータのコピーをサポートしています。 「[SharePoint Online からのファイルをコピーする](#copy-file-from-sharepoint-online)」セクションからファイルをコピーする方法を参照してください。

## <a name="prerequisites"></a>前提条件

SharePoint List Online コネクタでは、サービス プリンシパル認証を使用して SharePoint に接続されます。 設定するには、次の手順を実行します。

1. 「[アプリケーションを Azure AD テナントに登録する](../storage/common/storage-auth-aad-app.md#register-your-application-with-an-azure-ad-tenant)」に従って、Azure Active Directory (Azure AD) にアプリケーション エンティティを登録します。 次の値を記録しておきます。リンクされたサービスを定義するときに使います。

    - アプリケーション ID
    - アプリケーション キー
    - テナント ID

2. 登録したアプリケーションに SharePoint Online サイトのアクセス許可を付与する: 

    > [!NOTE]
    > この操作には、SharePoint Online サイト所有者のアクセス許可が必要です。 所有者を確認するには、サイトのホーム ページに移動し、右隅にある "X メンバー" をクリックし、"所有者" ロールを持つユーザーを確認します。

    1. SharePoint Online サイト リンクを開きます。たとえば、`https://[your_site_url]/_layouts/15/appinv.aspx` (サイトの URL は置き換えてください)。
    2. 登録したアプリケーション ID を検索し、空欄に入力して [作成] をクリックします。

        - アプリ ドメイン: `localhost.com`
        - リダイレクト URL: `https://www.localhost.com`
        - 権限の要求 XML:

        ```xml
        <AppPermissionRequests AllowAppOnlyPolicy="true">
            <AppPermissionRequest Scope="http://sharepoint/content/sitecollection/web" Right="Read"/>
        </AppPermissionRequests>
        ```

        :::image type="content" source="media/connector-sharepoint-online-list/sharepoint-online-grant-permission.png" alt-text="sharepoint のアクセス許可​​の付与":::

    3. このアプリに対して [信頼する] をクリックします。

## <a name="get-started"></a>はじめに

[!INCLUDE [data-factory-v2-connector-get-started](includes/data-factory-v2-connector-get-started.md)]

## <a name="create-a-linked-service-to-a-sharepoint-online-list-using-ui"></a>UI を使用して SharePoint Online リストへのリンク サービスを作成する

次の手順を使用して、Azure portal の UI で SharePoint Online リストへのリンク サービスを作成します。

1. Azure Data Factory または Synapse ワークスペースの [管理] タブに移動し、[リンク サービス] を選択して、[新規] をクリックします。

    # <a name="azure-data-factory"></a>[Azure Data Factory](#tab/data-factory)

    :::image type="content" source="media/doc-common-process/new-linked-service.png" alt-text="Azure Data Factory の UI で新しいリンク サービスを作成するスクリーンショット。":::

    # <a name="azure-synapse"></a>[Azure Synapse](#tab/synapse-analytics)

    :::image type="content" source="media/doc-common-process/new-linked-service-synapse.png" alt-text="Azure Synapse の UI を使用した新しいリンク サービスの作成を示すスクリーンショット。":::

2. SharePoint を検索し、SharePoint Online リスト コネクタを選択します。

    :::image type="content" source="media/connector-sharepoint-online-list/sharepoint-online-list-connector.png" alt-text="SharePoint Online リスト コネクタのスクリーンショット。":::    

1. サービスの詳細を構成し、接続をテストして、新しいリンク サービスを作成します。

    :::image type="content" source="media/connector-sharepoint-online-list/configure-sharepoint-online-list-linked-service.png" alt-text="SharePoint Online リストのリンク サービス構成のスクリーンショット。":::

## <a name="connector-configuration-details"></a>コネクタの構成の詳細

次のセクションでは、SharePoint Online リスト コネクタに固有のエンティティの定義に使用できるプロパティについて詳しく説明します。

## <a name="linked-service-properties"></a>リンクされたサービスのプロパティ

SharePoint Online リストのリンクされたサービスでは、次のプロパティがサポートされます。

| **プロパティ**        | **説明**                                              | **必須** |
| ------------------- | ------------------------------------------------------------ | ------------ |
| type                | type プロパティを  **SharePointOnlineList** に設定する必要があります。  | はい          |
| siteUrl             | SharePoint Online サイトの url (例: `https://contoso.sharepoint.com/sites/siteName`)。 | はい          |
| servicePrincipalId  | Azure Active Directory に登録されているアプリケーションのアプリケーション (クライアント) ID。 | はい          |
| servicePrincipalKey | アプリケーションのキーです。 このフィールドを **SecureString** とマークして安全に保存するか、[Azure Key Vault に保存されているシークレットを参照](store-credentials-in-key-vault.md)します。 | はい          |
| tenantId            | アプリケーションが存在するテナント ID。          | はい          |
| connectVia          | データ ストアに接続するために使用される [Integration Runtime](concepts-integration-runtime.md)。 詳細については、この記事の「[前提条件](#prerequisites)」を参照してください。 指定されていない場合は、既定の Azure Integration Runtime が使用されます。 | いいえ           |

**例:**

```json
{
    "name": "SharePointOnlineList",
    "properties": {
        "type": "SharePointOnlineList",
        "typeProperties": {
            "siteUrl": "<site URL>",
            "servicePrincipalId": "<service principal id>",
            "servicePrincipalKey": {
                "type": "SecureString",
                "value": "<service principal key>"
            },
            "tenantId": "<tenant ID>"
        }
    }
}
```

## <a name="dataset-properties"></a>データセットのプロパティ

データセットの定義に使用できるセクションとプロパティの完全な一覧については、「[データセットとリンクされたサービス](concepts-datasets-linked-services.md)」を参照してください。 次のセクションでは、SAP テーブル データセットでサポートされるプロパティの一覧を示します。

| プロパティ | 説明 | 必須 |
|:--- |:--- |:--- |
| type | データセットの **type** プロパティは **SharePointOnlineLResource** に設定する必要があります。 | はい |
| listName | SharePoint Online リストの名前。 | はい |

**例**

```json
{
    "name": "SharePointOnlineListDataset",
    "properties":
    {
        "type": "SharePointOnlineListResource",
        "linkedServiceName": {
            "referenceName": "<SharePoint Online List linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties":
        {
            "listName": "<name of the list>"
        }
    }
}
```

## <a name="copy-activity-properties"></a>コピー アクティビティのプロパティ

アクティビティの定義に利用できるセクションとプロパティの完全な一覧については、[パイプライン](concepts-pipelines-activities.md)に関する記事を参照してください。  SharePoint Online リスト ソースでサポートされているプロパティの一覧を次のセクションに示します。

### <a name="sharepoint-online-list-as-source"></a>ソースとしての SharePoint Online リスト

SharePoint Online リストからデータをコピーするために、以下のプロパティがコピー アクティビティの **source** セクションでサポートされています。

| プロパティ | 説明 | 必須 |
|:--- |:--- |:--- |
| type | コピー アクティビティ ソースの **type** プロパティは、**SharePointOnlineListSource** に設定する必要があります。 | はい |
| query | データをフィルター処理するためのカスタムの OData クエリ オプション。 例: `"$top=10&$select=Title,Number"`. | いいえ |
| httpRequestTimeout | HTTP 要求の応答が返されるまでのタイムアウト (秒単位)。 既定値は 300 (5 分間) です。 | いいえ |

**例**

```json
"activities":[
    {
        "name": "CopyFromSharePointOnlineList",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<SharePoint Online List input dataset name>",
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
            "source": {
                "type": "SharePointOnlineListSource",
                "query": "<OData query e.g. $top=10&$select=Title,Number>"
            }, 
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

> [!NOTE]
> SharePoint Online リストのソースに対して複数の "*選択*" データ型を選択することはできません。

## <a name="data-type-mapping-for-sharepoint-online-list"></a>SharePoint Online リストのデータ型マッピング

SharePoint Online リストからデータをコピーする際、SharePoint Online リストのデータ型とサービスの内部で使用される中間データ型との間では、次のマッピングが使用されます。

| **SharePoint Online のデータ型**                 | **OData のデータ型**                                  | **中間データ型** |
| ----------------------------------------------- | ---------------------------------------------------- | ---------------------------------------- |
| 1 行のテキスト                             | Edm.String                                           | String                                   |
| 複数行のテキスト                          | Edm.String                                           | String                                   |
| 選択 (メニューから選択)                    | Edm.String                                           | String                                   |
| 数値 (1、1.0、100)                            | Edm.Double                                           | Double                                   |
| 通貨 ($、¥、&euro;)                              | Edm.Double                                           | Double                                   |
| 日時                                   | Edm.DateTime                                         | DateTime                                 |
| 参照 (このサイトに既に存在する情報)       | Edm.Int32                                            | Int32                                    |
| はい/いいえ (チェック ボックス)                              | Edm.Boolean                                          | Boolean                                  |
| ユーザーまたはグループ                                 | Edm.Int32                                            | Int32                                    |
| ハイパーリンクまたは画像                            | Edm.String                                           | String                                   |
| 計算 (他の列に基づく計算) | Edm.String / Edm.Double / Edm.DateTime / Edm.Boolean | String / Double / DateTime / Boolean     |
| Attachment                                      | サポートされていません                                        |                                          |
| タスクの結果                                    | サポートされていません                                        |                                          |
| 外部データ                                   | サポートされていません                                        |                                          |
| マネージド メタデータ                                | サポートされていません                                        |                                          |

## <a name="copy-file-from-sharepoint-online"></a>SharePoint Online からのファイルをコピーする

SharePoint Online からファイルをコピーするには、**Web アクティビティ** を使用して認証し、SPO からアクセス トークンを取得し、後続の **コピー アクティビティ** に渡し、**HTTP コネクタをソースとして** 使用してデータをコピーします。

:::image type="content" source="media/connector-sharepoint-online-list/sharepoint-online-copy-file-flow.png" alt-text="sharepoint のファイルのコピー フロー":::

1. 「[前提条件](#prerequisites)」セクションに従って AAD アプリケーションを作成し、SharePoint Online にアクセス許可を付与します。 

2. **Web アクティビティ** を作成し、SharePoint Online からアクセス トークンを取得します。

    - **URL**: `https://accounts.accesscontrol.windows.net/[Tenant-ID]/tokens/OAuth/2`。 テナント ID は置き換えてください。
    - **メソッド**: POST
    - **Headers**:
        - Content-Type: application/x-www-form-urlencoded
    - **本文**: `grant_type=client_credentials&client_id=[Client-ID]@[Tenant-ID]&client_secret=[Client-Secret]&resource=00000003-0000-0ff1-ce00-000000000000/[Tenant-Name].sharepoint.com@[Tenant-ID]`。 クライアント ID (アプリケーション ID)、クライアント シークレット (アプリケーション キー)、テナント ID、テナント名 (SharePoint テナント) を置き換えます。

    > [!CAUTION]
    > トークン値がプレーン テキストでログに記録されないようにするには、Web アクティビティで [セキュリティで保護された出力] オプションを true に設定します。 この値を使用するその他のアクティビティでは、[セキュリティで保護された入力] オプションが true に設定されている必要があります。

3. **コピー アクティビティ** を、SharePoint Online ファイルのコンテンツをコピーするソースとして HTTP コネクタとチェーンします。

    - HTTP のリンクされたサービス:
        - **ベース URL**: `https://[site-url]/_api/web/GetFileByServerRelativeUrl('[relative-path-to-file]')/$value`。 サイト URL とファイルの相対パスを置き換えてください。 ファイルの相対パス例: `/sites/site2/Shared Documents/TestBook.xlsx`。
        - **認証の種類:** 匿名 *(コピー アクティビティのソースで、構成されているベアラー トークンを後で使用します)*
    - データセット: 目的の形式を選択します。 ファイルをそのままコピーするには、"バイナリ" の種類を選択します。
    - コピー アクティビティのソース:
        - **要求メソッド**:GET
        - **追加のヘッダー**: 次の式を使用します。`@{concat('Authorization: Bearer ', activity('<Web-activity-name>').output.access_token)}`。ここで、アップストリームの Web アクティビティによって生成されたベアラー トークンが authorization ヘッダーとして使用されます。 Web アクティビティ名を置き換えてください。
    - コピー アクティビティのシンクを通常どおりに構成します。

> [!NOTE]
> Azure AD アプリケーションに SharePoint Online に対する `FullControl` アクセス許可がある場合でも、IRM が有効になっているドキュメント ライブラリからファイルをコピーすることはできません。

## <a name="lookup-activity-properties"></a>Lookup アクティビティのプロパティ

プロパティの詳細については、[Lookup アクティビティ](control-flow-lookup-activity.md)に関するページを参照してください。

## <a name="next-steps"></a>次のステップ

コピー アクティビティでソースおよびシンクとしてサポートされているデータ ストアの一覧については、「[サポートされるデータ ストアと形式](copy-activity-overview.md#supported-data-stores-and-formats)」を参照してください。
