---
title: OData ソースからデータをコピーする
titleSuffix: Azure Data Factory & Azure Synapse
description: Azure Data Factory または Synapse Analytics パイプラインでコピー アクティビティを使用して、OData ソースからサポートされているシンク データ ストアにデータをコピーする方法について説明します。
author: jianleishen
ms.service: data-factory
ms.subservice: data-movement
ms.custom: synapse
ms.topic: conceptual
ms.date: 10/13/2021
ms.author: jianleishen
ms.openlocfilehash: c1ac63dde4dd489449db5db913aeb55f5d3de3ad
ms.sourcegitcommit: 4abfec23f50a164ab4dd9db446eb778b61e22578
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/15/2021
ms.locfileid: "130064971"
---
# <a name="copy-data-from-an-odata-source-by-using-azure-data-factory-or-synapse-analytics"></a>Azure Data Factory または Synapse Analytics を使用して OData ソースからデータをコピーする
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

> [!div class="op_single_selector" title1="使用している Data Factory サービスのバージョンを選択してください:"]
> * [Version 1](v1/data-factory-odata-connector.md)
> * [現在のバージョン](connector-odata.md)

この記事では、Azure Data Factory または Synapse Analytics パイプラインでコピー アクティビティを使用して、OData ソースからデータをコピーする方法について説明します。 この記事は、Copy アクティビティの概要を説明する [Copy アクティビティ](copy-activity-overview.md)に関する記事に基づいています。

## <a name="supported-capabilities"></a>サポートされる機能

この OData コネクタは、以下のアクティビティでサポートされています。

- [サポートされるソース/シンク マトリックス](copy-activity-overview.md)での[コピー アクティビティ](copy-activity-overview.md)
- [Lookup アクティビティ](control-flow-lookup-activity.md)

OData ストアから、サポートされている任意のシンク データ ストアにデータをコピーできます。 コピー アクティビティでソースおよびシンクとしてサポートされているデータ ストアの一覧については、「[サポートされるデータ ストアと形式](copy-activity-overview.md#supported-data-stores-and-formats)」を参照してください。

具体的には、この OData コネクタは以下をサポートします。

- OData バージョン 3.0 および 4.0。
- 次の認証のいずれかを使用したデータのコピー。**匿名**、**基本**、**Windows**、および **AAD サービス プリンシパル**。

## <a name="prerequisites"></a>前提条件

[!INCLUDE [data-factory-v2-integration-runtime-requirements](includes/data-factory-v2-integration-runtime-requirements.md)]

## <a name="get-started"></a>はじめに

[!INCLUDE [data-factory-v2-connector-get-started](includes/data-factory-v2-connector-get-started.md)]

## <a name="create-a-linked-service-to-an-odata-store-using-ui"></a>UI を使用して OData ストアにリンク サービスを作成する

次の手順を使用して、Azure portal の UI で OData ストアにリンク サービスを作成します。

1. Azure Data Factory または Synapse ワークスペースの [管理] タブに移動し、[リンクされたサービス] を選択して、[新規] をクリックします。

    # <a name="azure-data-factory"></a>[Azure Data Factory](#tab/data-factory)

    :::image type="content" source="media/doc-common-process/new-linked-service.png" alt-text="Azure Data Factory の UI で新しいリンク サービスを作成するスクリーンショット。":::

    # <a name="azure-synapse"></a>[Azure Synapse](#tab/synapse-analytics)

    :::image type="content" source="media/doc-common-process/new-linked-service-synapse.png" alt-text="Azure Synapse の UI で新しいリンク サービスを作成するスクリーンショット。":::

2. OData を検索し、OData コネクタを選択します。

    :::image type="content" source="media/connector-odata/odata-connector.png" alt-text="OData コネクタのスクリーンショット。":::    

1. サービスの詳細を構成し、接続をテストして、新しいリンク サービスを作成します。

    :::image type="content" source="media/connector-odata/configure-odata-linked-service.png" alt-text="OData ストアのリンク サービスの構成のスクリーンショット。":::

## <a name="connector-configuration-details"></a>コネクタの構成の詳細

以降のセクションでは、OData コネクタに固有の Data Factory エンティティを定義するために使用できるプロパティについて詳細に説明します。

## <a name="linked-service-properties"></a>リンクされたサービスのプロパティ

OData のリンクされたサービスでは、次のプロパティがサポートされます。

| プロパティ | 説明 | 必須 |
|:--- |:--- |:--- |
| type | **type** プロパティは **OData** に設定する必要があります。 |はい |
| url | OData サービスのルート URL。 |はい |
| authenticationType | OData ソースに接続するために使用される認証の種類。 使用可能な値は、 **[匿名]** 、 **[基本]** 、 **[Windows]** 、および **[AadServicePrincipal]** です。 ユーザー ベースの OAuth はサポートされていません。 `authHeader` プロパティで認証ヘッダーを追加で構成することもできます。| はい |
| authHeaders | 追加の認証用 HTTP 要求ヘッダー。<br/> たとえば、API キー認証を使うには、認証の種類として "匿名" を選択し、ヘッダーに API キーを指定します。 | いいえ |
| userName | 基本認証または Windows 認証を使用する場合は、**userName** を指定します。 | いいえ |
| password | **userName** に指定したユーザー アカウントの **password** を指定します。 安全に保存するには、このフィールドを **SecureString** 型としてマークします。 また、[Azure Key Vault に格納されているシークレットを参照する](store-credentials-in-key-vault.md)こともできます。 | いいえ |
| servicePrincipalId | Azure Active Directory アプリケーションのクライアント ID を指定します。 | いいえ |
| aadServicePrincipalCredentialType | サービス プリンシパル認証に使用する資格情報の種類を指定します。 使用できる値: `ServicePrincipalKey` または `ServicePrincipalCert`。 | いいえ |
| servicePrincipalKey | Azure Active Directory アプリケーションのキーを指定します。 このフィールドを **SecureString** とマークして安全に保存するか、[Azure Key Vault に保存されているシークレットを参照](store-credentials-in-key-vault.md)します。 | いいえ |
| servicePrincipalEmbeddedCert | Azure Active Directory に登録されているアプリケーションの、Base64 でエンコードされた証明書を指定します。 このフィールドを **SecureString** とマークして安全に保存するか、[Azure Key Vault に保存されているシークレットを参照](store-credentials-in-key-vault.md)します。 | いいえ |
| servicePrincipalEmbeddedCertPassword | 証明書がパスワードで保護されている場合は、証明書のパスワードを指定します。 このフィールドを **SecureString** とマークして安全に保存するか、[Azure Key Vault に保存されているシークレットを参照](store-credentials-in-key-vault.md)します。  | いいえ|
| tenant | アプリケーションが存在するテナントの情報 (ドメイン名またはテナント ID) を指定します。 Azure portal の右上隅にマウスを置くことで取得します。 | いいえ |
| aadResourceId | 認可を要求する AAD リソースを指定します。| いいえ |
| azureCloudType | サービス プリンシパル認証の場合は、AAD アプリケーションの登録先である Azure クラウド環境の種類を指定します。 <br/> 指定できる値は、**AzurePublic**、**AzureChina**、**AzureUsGovernment**、および **AzureGermany** です。 既定では、サービスのクラウド環境が使用されます。 | いいえ |
| connectVia | データ ストアに接続するために使用される [Integration Runtime](concepts-integration-runtime.md)。 詳細については、「[前提条件](#prerequisites)」セクションを参照してください。 指定されていない場合は、既定の Azure Integration Runtime が使用されます。 |いいえ |

**例 1: 匿名認証を使用する**

```json
{
    "name": "ODataLinkedService",
    "properties": {
        "type": "OData",
        "typeProperties": {
            "url": "https://services.odata.org/OData/OData.svc",
            "authenticationType": "Anonymous"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

**例 2: 基本認証を使用する**

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

**例 3: Windows 認証を使用する**

```json
{
    "name": "ODataLinkedService",
    "properties": {
        "type": "OData",
        "typeProperties": {
            "url": "<endpoint of OData source>",
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

**例 4: サービス プリンシパル キー認証を使用する**

```json
{
    "name": "ODataLinkedService",
    "properties": {
        "type": "OData",
        "typeProperties": {
            "url": "<endpoint of OData source>",
            "authenticationType": "AadServicePrincipal",
            "servicePrincipalId": "<service principal id>",
            "aadServicePrincipalCredentialType": "ServicePrincipalKey",
            "servicePrincipalKey": {
                "type": "SecureString",
                "value": "<service principal key>"
            },
            "tenant": "<tenant info, e.g. microsoft.onmicrosoft.com>",
            "aadResourceId": "<AAD resource URL>"
        }
    },
    "connectVia": {
        "referenceName": "<name of Integration Runtime>",
        "type": "IntegrationRuntimeReference"
    }
}
```

**例 5: サービス プリンシパル証明書認証を使用する**

```json
{
    "name": "ODataLinkedService",
    "properties": {
        "type": "OData",
        "typeProperties": {
            "url": "<endpoint of OData source>",
            "authenticationType": "AadServicePrincipal",
            "servicePrincipalId": "<service principal id>",
            "aadServicePrincipalCredentialType": "ServicePrincipalCert",
            "servicePrincipalEmbeddedCert": { 
                "type": "SecureString", 
                "value": "<base64 encoded string of (.pfx) certificate data>"
            },
            "servicePrincipalEmbeddedCertPassword": { 
                "type": "SecureString", 
                "value": "<password of your certificate>"
            },
            "tenant": "<tenant info, e.g. microsoft.onmicrosoft.com>",
            "aadResourceId": "<AAD resource e.g. https://tenant.sharepoint.com>"
        }
    },
    "connectVia": {
        "referenceName": "<name of Integration Runtime>",
        "type": "IntegrationRuntimeReference"
    }
}
```

**例 6:API キー認証を使用する**

```json
{
    "name": "ODataLinkedService",
    "properties": {
        "type": "OData",
        "typeProperties": {
            "url": "<endpoint of OData source>",
            "authenticationType": "Anonymous",
            "authHeader": {
                "APIKey": {
                    "type": "SecureString",
                    "value": "<API key>"
                }
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
| type | データセットの **type** プロパティは **ODataResource** に設定する必要があります。 | はい |
| path | OData リソースへのパス。 | はい |

**例**

```json
{
    "name": "ODataDataset",
    "properties":
    {
        "type": "ODataResource",
        "schema": [],
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

OData からデータをコピーする場合、コピー アクティビティの **source** セクションで次のプロパティがサポートされます。

| プロパティ | 説明 | 必須 |
|:--- |:--- |:--- |
| type | コピー アクティビティのソースの **type** プロパティは **ODataSource** に設定する必要があります。 | はい |
| query | データをフィルター処理するための OData クエリ オプション。 例: `"$select=Name,Description&$top=5"`.<br/><br/>**注**:OData コネクタは、次の結合された URL からデータをコピーします。`[URL specified in linked service]/[path specified in dataset]?[query specified in copy activity source]` 詳細については、[OData の URL コンポーネント](https://www.odata.org/documentation/odata-version-3-0/url-conventions/)に関するページを参照してください。 | いいえ |
| httpRequestTimeout | HTTP 要求が応答を取得する際のタイムアウト (**TimeSpan** 値)。 この値は、応答データの読み取りのタイムアウトではなく、応答の取得のタイムアウトです。 指定しない場合は、既定値の **00:30:00** (30 分) が使用されます。 | いいえ |

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
                "type": "ODataSource",
                "query": "$select=Name,Description&$top=5"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

`RelationalSource` 型のソースを使用していた場合は現状のまま引き続きサポートされますが、今後は新しいものを使用することをお勧めします。

## <a name="data-type-mapping-for-odata"></a>OData のデータ型マッピング

OData からデータをコピーするときに、OData のデータ型とサービス内で内部的に使用される中間データ型の間で、次のマッピングが使用されます。 コピー アクティビティでソースのスキーマとデータ型がシンクにマッピングされる方法の詳細については、[スキーマとデータ型のマッピング](copy-activity-schema-and-type-mapping.md)に関するページを参照してください。

| OData のデータ型 | 中間サービス データ型 |
|:--- |:--- |
| Edm.Binary | Byte[] |
| Edm.Boolean | Bool |
| Edm.Byte | Byte[] |
| Edm.DateTime | DateTime |
| Edm.Decimal | Decimal |
| Edm.Double | Double |
| Edm.Single | Single |
| Edm.Guid | Guid |
| Edm.Int16 | Int16 |
| Edm.Int32 | Int32 |
| Edm.Int64 | Int64 |
| Edm.SByte | Int16 |
| Edm.String | String |
| Edm.Time | TimeSpan |
| Edm.DateTimeOffset | DateTimeOffset |

> [!NOTE]
> OData の複雑なデータ型 (**Object** など) はサポートされていません。

## <a name="copy-data-from-project-online"></a>Project Online からデータをコピーする

Project Online にはユーザーベースの OAuth が必要ですが、これは Azure Data Factory ではサポートされていません。 Project Online からデータをコピーするには、OData コネクタと、Postman などのツールで取得したアクセス トークンを使用します。

> [!CAUTION]
> 既定では、アクセス トークンは 1 時間で有効期限が切れます。有効期限が切れたときは、新しいアクセス トークンを取得する必要があります。

1. **Postman** を使用してアクセス トークンを取得します。

   1. Postman Web サイトの **[Authorization]** タブに移動します。
   1. **[Type]**  ボックスで **[OAuth 2.0]** を選択し、 **[Add authorization data to]** ボックスで **[Request Headers]** を選択します。
   1. **[Configure New Token]** ページで次の情報を入力し、新しいアクセス トークンを取得します。 
      - **Grant type**: **[Authorization Code]** を選択します。
      - **Callback URL**: 「`https://www.localhost.com/`」と入力します。 
      - **Auth URL**: 「`https://login.microsoftonline.com/common/oauth2/authorize?resource=https://<your tenant name>.sharepoint.com`」と入力します。 `<your tenant name>` は自分のテナント名に置き換えます。 
      - **Access Token URL**: 「`https://login.microsoftonline.com/common/oauth2/token`」と入力します。
      - **Client ID**: 自分の AAD サービス プリンシパル ID を入力します。
      - **Client Secret**: 自分のサービス プリンシパル シークレットを入力します。
      - **Client Authentication**: **[Send as Basic Auth header]** を選択します。
     
   1. 自分のユーザー名とパスワードを使用してログインするように求められます。
   1. アクセス トークンを取得したら、次の手順のためにそれをコピーして保存してください。
   
    :::image type="content" source="./media/connector-odata/odata-project-online-postman-access-token-inline.png" alt-text="Postman を使用してアクセス トークンを取得するスクリーンショット。" lightbox="./media/connector-odata/odata-project-online-postman-access-token-expanded.png":::        

1. OData のリンクされたサービスを作成します。
    - **Service URL**: 「`https://<your tenant name>.sharepoint.com/sites/pwa/_api/Projectdata`」と入力します。 `<your tenant name>` は自分のテナント名に置き換えます。 
    - **Authentication type**: **[Anonymous]** を選択します。
    - **Auth headers**:
        - **Property name**: **[Authorization]** を選択します。
        - **値**:「`Bearer <access token from step 1>`」と入力します。
    - リンクされたサービスをテストします。

    :::image type="content" source="./media/connector-odata/odata-project-online-linked-service.png" alt-text="OData のリンクされたサービスを作成する":::

1. OData データセットを作成します。
    1. 手順 2 で作成した、OData のリンクされたサービスを使用してデータセットを作成します。
    1. データをプレビューします。
 
    :::image type="content" source="./media/connector-odata/odata-project-online-preview-data.png" alt-text="データのプレビュー":::
 


## <a name="lookup-activity-properties"></a>Lookup アクティビティのプロパティ

プロパティの詳細については、[Lookup アクティビティ](control-flow-lookup-activity.md)に関するページを参照してください。

## <a name="next-steps"></a>次のステップ

コピー アクティビティでソースおよびシンクとしてサポートされているデータ ストアの一覧については、「[サポートされるデータ ストアと形式](copy-activity-overview.md#supported-data-stores-and-formats)」を参照してください。