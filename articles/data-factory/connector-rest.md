---
title: Azure Data Factory を使用して REST エンドポイントとの間でデータをコピーおよび変換する
titleSuffix: Azure Data Factory & Azure Synapse
description: Azure Data Factory または Azure Synapse Analytics パイプラインで Copy アクティビティを使用して、クラウドまたはオンプレミスの REST ソースからサポートされているシンク データ ストアに、またはサポートされているソース データ ストアから REST シンクにデータをコピーし、Data Flow を使用してデータを変換する方法について説明します。
author: jianleishen
ms.service: data-factory
ms.subservice: data-movement
ms.custom: synapse
ms.topic: conceptual
ms.date: 11/11/2021
ms.author: makromer
ms.openlocfilehash: 5ea0e509f7969c011cb18f99433c85fc97ed5528
ms.sourcegitcommit: 362359c2a00a6827353395416aae9db492005613
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/15/2021
ms.locfileid: "132487195"
---
# <a name="copy-and-transform-data-from-and-to-a-rest-endpoint-by-using-azure-data-factory"></a>Azure Data Factory を使用して REST エンドポイントとの間でデータをコピーおよび変換する
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

この記事では、Azure Data Factory の Copy アクティビティを使用して、REST エンドポイントとの間でデータコピーする方法について説明します。 この記事は、コピー アクティビティの概要が説明されている「[Azure Data Factory のコピー アクティビティ](copy-activity-overview.md)」を基に作成されています。

この REST コネクタ、[HTTP コネクタ](connector-http.md)、および [Web テーブル コネクタ](connector-web-table.md)の違いは次のとおりです。

- **REST コネクタ** では、特に RESTful API からのデータのコピーがサポートされます。
- **HTTP コネクタ** は、ファイルをダウンロードするなど、任意の HTTP エンドポイントからデータを取得するための一般的なものです。 この REST コネクタの前に、HTTP コネクタを使用して RESTful API からデータをコピーする場合があります。これはサポートされますが、REST コネクタと比べると機能は低くなります。
- **Web テーブル コネクタ** では、HTML Web ページからテーブルの内容を抽出します。

## <a name="supported-capabilities"></a>サポートされる機能

REST ソースから、サポートされている任意のシンク データ ストアにデータをコピーできます。 サポートされている任意のソース データ ストアから REST シンクにデータをコピーすることもできます。 コピー アクティビティでソースおよびシンクとしてサポートされているデータ ストアの一覧については、「[サポートされるデータ ストアと形式](copy-activity-overview.md#supported-data-stores-and-formats)」を参照してください。

具体的には、この汎用 REST コネクタは以下をサポートします。

- **GET** または **POST** メソッドを使用した REST エンド ポイントからのデータのコピー、および **POST**、**PUT** または **PATCH** メソッドを使用した REST エンドポイントへのデータのコピー。
- 次の認証のいずれかを使用したデータのコピー。**匿名**、**基本**、**AAD サービス プリンシパル**、**Azure リソースのマネージド ID**。
- REST API 内の **[改ページ位置の自動修正](#pagination-support)** 。
- ソースとしての REST の場合、REST JSON 応答を[そのまま](#export-json-response-as-is)コピーするか、[スキーマ マッピング](copy-activity-schema-and-type-mapping.md#schema-mapping)を使用して解析する。 **JSON** では応答ペイロードのみがサポートされます。

> [!TIP]
> Data Factory で REST コネクタを構成する前に、データ取得のために要求をテストするには、ヘッダーおよび本文の要件に関する API 仕様を確認してください。 Postman や Web ブラウザーのようなツールを使用して検証することができます。

## <a name="prerequisites"></a>前提条件

[!INCLUDE [data-factory-v2-integration-runtime-requirements](includes/data-factory-v2-integration-runtime-requirements.md)]

## <a name="get-started"></a>はじめに

[!INCLUDE [data-factory-v2-connector-get-started](includes/data-factory-v2-connector-get-started.md)]

## <a name="create-a-rest-linked-service-using-ui"></a>UI を使用して REST のリンク サービスを作成する

次の手順を使用して、Azure portal UI で REST のリンク サービスを作成します。

1. Azure Data Factory または Synapse ワークスペースの [管理] タブに移動し、[リンクされたサービス] を選択して、[新規] をクリックします。

    # <a name="azure-data-factory"></a>[Azure Data Factory](#tab/data-factory)

    :::image type="content" source="media/doc-common-process/new-linked-service.png" alt-text="Azure Data Factory の UI で新しいリンク サービスを作成するスクリーンショット。":::

    # <a name="azure-synapse"></a>[Azure Synapse](#tab/synapse-analytics)

    :::image type="content" source="media/doc-common-process/new-linked-service-synapse.png" alt-text="Azure Synapse の UI を使用した新しいリンク サービスの作成を示すスクリーンショット。":::

2. REST を検索し、REST コネクタを選択します。

    :::image type="content" source="media/connector-rest/rest-connector.png" alt-text="REST コネクタを選択します。":::    

1. サービスの詳細を構成し、接続をテストして、新しいリンク サービスを作成します。

    :::image type="content" source="media/connector-rest/configure-rest-linked-service.png" alt-text="REST のリンク サービスを構成します。":::

## <a name="connector-configuration-details"></a>コネクタの構成の詳細

次のセクションでは、REST コネクタに固有の Data Factory エンティティの定義に使用できるプロパティについて詳しく説明します。

## <a name="linked-service-properties"></a>リンクされたサービスのプロパティ

REST のリンクされたサービスでは、次のプロパティがサポートされます。

| プロパティ | 説明 | 必須 |
|:--- |:--- |:--- |
| type | **type** プロパティには **RestService** を設定する必要があります。 | はい |
| url | REST サービスのベース URL。 | はい |
| enableServerCertificateValidation | エンドポイントに接続するときに、サーバー側の TLS/SSL 証明書を検証するかどうか。 | いいえ<br /> (既定値は **true** です)。 |
| authenticationType | REST サービスへの接続に使用される認証の種類。 使用できる値は、**Anonymous**、**Basic**、**AadServicePrincipal**、および **ManagedServiceIdentity** です。 ユーザー ベースの OAuth はサポートされていません。 `authHeader` プロパティで認証ヘッダーを追加で構成することもできます。 それぞれのプロパティとサンプルについては、以下の対応するセクションを参照してください。| はい |
| authHeaders | 追加の認証用 HTTP 要求ヘッダー。<br/> たとえば、API キー認証を使うには、認証の種類として "匿名" を選択し、ヘッダーに API キーを指定します。 | いいえ |
| connectVia | データ ストアに接続するために使用される [Integration Runtime](concepts-integration-runtime.md)。 詳細については、「[前提条件](#prerequisites)」セクションを参照してください。 指定されていない場合は、既定の Azure Integration Runtime が使用されます。 |いいえ |

### <a name="use-basic-authentication"></a>基本認証を使用する

**authenticationType** プロパティを **Basic** に設定します。 前のセクションで説明した汎用的なプロパティに加えて、次のプロパティを指定します。

| プロパティ | 説明 | 必須 |
|:--- |:--- |:--- |
| userName | REST エンドポイントにアクセスするために使用するユーザー名。 | はい |
| password | ユーザー (**userName** 値) のパスワード。 Data Factory に安全に格納するには、このフィールドを **SecureString** 型として指定します。 [Azure Key Vault に格納されているシークレットを参照する](store-credentials-in-key-vault.md)こともできます。 | はい |

**例**

```json
{
    "name": "RESTLinkedService",
    "properties": {
        "type": "RestService",
        "typeProperties": {
            "authenticationType": "Basic",
            "url" : "<REST endpoint>",
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

### <a name="use-aad-service-principal-authentication"></a>AAD サービス プリンシパル認証を使用する

**authenticationType** プロパティを **AadServicePrincipal** に設定します。 前のセクションで説明した汎用的なプロパティに加えて、次のプロパティを指定します。

| プロパティ | 説明 | 必須 |
|:--- |:--- |:--- |
| servicePrincipalId | Azure Active Directory アプリケーションのクライアント ID を指定します。 | はい |
| servicePrincipalKey | Azure Active Directory アプリケーションのキーを指定します。 このフィールドを **SecureString** としてマークして Data Factory に安全に保管するか、[Azure Key Vault に格納されているシークレットを参照](store-credentials-in-key-vault.md)します。 | はい |
| tenant | アプリケーションが存在するテナントの情報 (ドメイン名またはテナント ID) を指定します。 Azure portal の右上隅にマウスを置くことで取得します。 | はい |
| aadResourceId | 承認を要求しようとしている AAD リソースを指定します。例: `https://management.core.windows.net`。| はい |
| azureCloudType | サービス プリンシパル認証の場合は、AAD アプリケーションの登録先である Azure クラウド環境の種類を指定します。 <br/> 指定できる値は、**AzurePublic**、**AzureChina**、**AzureUsGovernment**、および **AzureGermany** です。 既定では、データ ファクトリのクラウド環境が使用されます。 | いいえ |

**例**

```json
{
    "name": "RESTLinkedService",
    "properties": {
        "type": "RestService",
        "typeProperties": {
            "url": "<REST endpoint e.g. https://www.example.com/>",
            "authenticationType": "AadServicePrincipal",
            "servicePrincipalId": "<service principal id>",
            "servicePrincipalKey": {
                "value": "<service principal key>",
                "type": "SecureString"
            },
            "tenant": "<tenant info, e.g. microsoft.onmicrosoft.com>",
            "aadResourceId": "<AAD resource URL e.g. https://management.core.windows.net>"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

### <a name="use-system-assigned-managed-identity-authentication"></a><a name="managed-identity"></a> システム割り当てマネージド ID 認証を使用する

**authenticationType** プロパティを **ManagedServiceIdentity** に設定します。 前のセクションで説明した汎用的なプロパティに加えて、次のプロパティを指定します。

| プロパティ | 説明 | 必須 |
|:--- |:--- |:--- |
| aadResourceId | 承認を要求しようとしている AAD リソースを指定します。例: `https://management.core.windows.net`。| はい |

**例**

```json
{
    "name": "RESTLinkedService",
    "properties": {
        "type": "RestService",
        "typeProperties": {
            "url": "<REST endpoint e.g. https://www.example.com/>",
            "authenticationType": "ManagedServiceIdentity",
            "aadResourceId": "<AAD resource URL e.g. https://management.core.windows.net>"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

### <a name="use-user-assigned-managed-identity-authentication"></a>ユーザー割り当てマネージド ID 認証を使用する
**authenticationType** プロパティを **ManagedServiceIdentity** に設定します。 前のセクションで説明した汎用的なプロパティに加えて、次のプロパティを指定します。

| プロパティ | 説明 | 必須 |
|:--- |:--- |:--- |
| aadResourceId | 承認を要求しようとしている AAD リソースを指定します。例: `https://management.core.windows.net`。| はい |
| 資格情報 | ユーザー割り当てマネージド ID を資格情報オブジェクトとして指定します。 | はい |


**例**

```json
{
    "name": "RESTLinkedService",
    "properties": {
        "type": "RestService",
        "typeProperties": {
            "url": "<REST endpoint e.g. https://www.example.com/>",
            "authenticationType": "ManagedServiceIdentity",
            "aadResourceId": "<AAD resource URL e.g. https://management.core.windows.net>",
            "credential": {
                "referenceName": "credential1",
                "type": "CredentialReference"
            }    
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

### <a name="using-authentication-headers"></a>認証ヘッダーの使用

また、組み込みの認証の種類と共に、認証用の要求ヘッダーを構成できます。

**例: API キー認証の使用**

```json
{
    "name": "RESTLinkedService",
    "properties": {
        "type": "RestService",
        "typeProperties": {
            "url": "<REST endpoint>",
            "authenticationType": "Anonymous",
            "authHeader": {
                "x-api-key": {
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

このセクションでは、REST データセットでサポートされているプロパティの一覧を示します。 

データセットの定義に使用できるセクションとプロパティの完全な一覧については、「[データセットとリンクされたサービス](concepts-datasets-linked-services.md)」を参照してください。 

REST からのデータ コピーについては、次のプロパティがサポートされています。

| プロパティ | 説明 | 必須 |
|:--- |:--- |:--- |
| type | データセットの **type** プロパティを **RestResource** に設定する必要があります。 | はい |
| relativeUrl | データを含むリソースへの相対 URL。 このプロパティが指定されていない場合は、リンクされたサービス定義に指定されている URL のみが使用されます。 HTTP コネクタは、次の結合された URL からデータをコピーします。`[URL specified in linked service]/[relative URL specified in dataset]` | いいえ |

データセットに `requestMethod`、`additionalHeaders`、`requestBody`、`paginationRules` を設定していた場合は現状のまま引き続きサポートされますが、今後のアクティビティでは新しいモデルを使用することをお勧めします。

**例:**

```json
{
    "name": "RESTDataset",
    "properties": {
        "type": "RestResource",
        "typeProperties": {
            "relativeUrl": "<relative url>"
        },
        "schema": [],
        "linkedServiceName": {
            "referenceName": "<REST linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

## <a name="copy-activity-properties"></a>コピー アクティビティのプロパティ

このセクションでは、REST のソースとシンクでサポートされるプロパティの一覧を示します。

アクティビティの定義に利用できるセクションとプロパティの完全な一覧については、[パイプライン](concepts-pipelines-activities.md)に関する記事を参照してください。 

### <a name="rest-as-source"></a>ソースとしての REST

コピー アクティビティの **source** セクションでは、次のプロパティがサポートされます。

| プロパティ | 説明 | 必須 |
|:--- |:--- |:--- |
| type | コピー アクティビティのソースの **type** プロパティを **RestSource** に設定する必要があります | はい |
| requestMethod | HTTP メソッド。 使用できる値は、**GET** (既定値) と **POST** です。 | いいえ |
| additionalHeaders | 追加の HTTP 要求ヘッダー。 | いいえ |
| requestBody | HTTP 要求の本文。 | いいえ |
| paginationRules | 次のページ要求を作成する改ページ位置の自動修正規則。 詳細については、「[pagination support](#pagination-support)」(改ページ位置の自動調整のサポート) セクションを参照してください。 | いいえ |
| httpRequestTimeout | HTTP 要求が応答を取得する際のタイムアウト (**TimeSpan** 値)。 この値は、応答データの読み取りのタイムアウトではなく、応答の取得のタイムアウトです。 既定値は **00:01:40** です。  | いいえ |
| requestInterval | 次のページに対する要求を送信する前に待機する時間。 既定値は **00:00:01** です。 |  いいえ |

>[!NOTE]
>REST コネクタは、`additionalHeaders` で指定された "Accept" ヘッダーをすべて無視します。 REST コネクタは JSON の応答のみをサポートし、`Accept: application/json` のヘッダーを自動生成します。

**例 1: 改ページ位置の自動修正で Get メソッドを使用する**

```json
"activities":[
    {
        "name": "CopyFromREST",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<REST input dataset name>",
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
                "type": "RestSource",
                "additionalHeaders": {
                    "x-user-defined": "helloworld"
                },
                "paginationRules": {
                    "AbsoluteUrl": "$.paging.next"
                },
                "httpRequestTimeout": "00:01:00"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

**例 2: Post メソッドを使用する**

```json
"activities":[
    {
        "name": "CopyFromREST",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<REST input dataset name>",
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
                "type": "RestSource",
                "requestMethod": "Post",
                "requestBody": "<body for POST REST request>",
                "httpRequestTimeout": "00:01:00"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

### <a name="rest-as-sink"></a>シンクとしての REST

コピー アクティビティの **sink** セクションでは、次のプロパティがサポートされます。

| プロパティ | 説明 | 必須 |
|:--- |:--- |:--- |
| type | Copy アクティビティのシンクの **type** プロパティには **RestSink** を設定する必要があります。 | はい |
| requestMethod | HTTP メソッド。 使用できる値は、**POST** (既定値)、**PUT**、および **PATCH** です。 | いいえ |
| additionalHeaders | 追加の HTTP 要求ヘッダー。 | いいえ |
| httpRequestTimeout | HTTP 要求が応答を取得する際のタイムアウト (**TimeSpan** 値)。 この値は、データの書き込みのタイムアウトではなく、応答の取得のタイムアウトです。 既定値は **00:01:40** です。  | いいえ |
| requestInterval | 異なる要求間の間隔時間 (ミリ秒単位)。 要求間隔の値は、[10, 60000] の間の数値にする必要があります。 |  いいえ |
| httpCompressionType | 最適な圧縮レベルでデータを送信するときに使用する HTTP 圧縮の種類。 使用できる値は **none** と **gzip** です。 | いいえ |
| writeBatchSize | バッチごとに REST シンクに書き込むレコードの数。 既定値は 10000 です。 | いいえ |

シンクとしての REST コネクタは、JSON を受け入れる REST API と連携します。 データは次のパターンで JSON で送信されます。 必要に応じて、コピー アクティビティ [スキーマ マッピング](copy-activity-schema-and-type-mapping.md#schema-mapping)を使用し、REST API によって求められるペイロードに準拠するようにソース データを整形できます。

```json
[
    { <data object> },
    { <data object> },
    ...
]
```

**例:**

```json
"activities":[
    {
        "name": "CopyToREST",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<REST output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "RestSink",
                "requestMethod": "POST",
                "httpRequestTimeout": "00:01:40",
                "requestInterval": 10,
                "writeBatchSize": 10000,
                "httpCompressionType": "none",
            },
        }
    }
]
```

## <a name="mapping-data-flow-properties"></a>Mapping Data Flow のプロパティ

REST は、統合データセットとインライン データセットの両方のデータ フローでサポートされています。

### <a name="source-transformation"></a>ソース変換

| プロパティ | 説明 | 必須 |
|:--- |:--- |:--- |
| requestMethod | HTTP メソッド。 使用できる値は **GET** と **POST** です。 | はい |
| relativeUrl | データを含むリソースへの相対 URL。 このプロパティが指定されていない場合は、リンクされたサービス定義に指定されている URL のみが使用されます。 HTTP コネクタは、次の結合された URL からデータをコピーします。`[URL specified in linked service]/[relative URL specified in dataset]` | いいえ |
| additionalHeaders | 追加の HTTP 要求ヘッダー。 | いいえ |
| httpRequestTimeout | HTTP 要求が応答を取得する際のタイムアウト (**TimeSpan** 値)。 この値は、データの書き込みのタイムアウトではなく、応答の取得のタイムアウトです。 既定値は **00:01:40** です。  | いいえ |
| requestInterval | 異なる要求間の間隔時間 (ミリ秒単位)。 要求間隔の値は、[10, 60000] の間の数値にする必要があります。 |  いいえ |
| QueryParameters.*request_query_parameter* または QueryParameters['request_query_parameter'] | "request_query_parameter" は、次の HTTP 要求 URL 内で 1 つのクエリ パラメーター名を参照するユーザー定義です。 | いいえ |

### <a name="sink-transformation"></a>シンク変換

| プロパティ | 説明 | 必須 |
|:--- |:--- |:--- |
| additionalHeaders | 追加の HTTP 要求ヘッダー。 | いいえ |
| httpRequestTimeout | HTTP 要求が応答を取得する際のタイムアウト (**TimeSpan** 値)。 この値は、データの書き込みのタイムアウトではなく、応答の取得のタイムアウトです。 既定値は **00:01:40** です。  | いいえ |
| requestInterval | 異なる要求間の間隔時間 (ミリ秒単位)。 要求間隔の値は、[10, 60000] の間の数値にする必要があります。 |  いいえ |
| httpCompressionType | 最適な圧縮レベルでデータを送信するときに使用する HTTP 圧縮の種類。 使用できる値は **none** と **gzip** です。 | いいえ |
| writeBatchSize | バッチごとに REST シンクに書き込むレコードの数。 既定値は 10000 です。 | いいえ |

削除、挿入、更新、アップサートの各メソッドと、CRUD 操作のために REST シンクに送信する相対行データを設定できます。

:::image type="content" source="media/data-flow/data-flow-sink.png" alt-text="データ フロー REST シンク":::

## <a name="sample-data-flow-script"></a>データ フロー スクリプトのサンプル

シンクの前に行の変更変換を使用して、REST シンクで実行するアクションの種類 (挿入、更新、アップサート、削除) を ADF に指示します。

```
AlterRow1 sink(allowSchemaDrift: true,
    validateSchema: false,
    deletable:true,
    insertable:true,
    updateable:true,
    upsertable:true,
    rowRelativeUrl: 'periods',
    insertHttpMethod: 'PUT',
    deleteHttpMethod: 'DELETE',
    upsertHttpMethod: 'PUT',
    updateHttpMethod: 'PATCH',
    timeout: 30,
    requestFormat: ['type' -> 'json'],
    skipDuplicateMapInputs: true,
    skipDuplicateMapOutputs: true) ~> sink1
```

## <a name="pagination-support"></a>改ページ位置の自動調整のサポート

REST API からデータをコピーするとき、通常、REST API では、1 つの要求の応答ペイロードのサイズが適切な数値を超えないように制限されています。大量のデータが返される場合は、結果が複数のページに分割され、呼び出し元に連続する要求を送信して結果の次のページを取得することが要求されます。 通常、1 つのページに対する要求は動的で、前のページの応答から返される情報で構成されます。

この汎用 REST コネクタでは、次の改ページ位置の自動修正パターンをサポートしています。 

* 次の要求の絶対または相対 URL = 現在の応答本文のプロパティ値
* 次の要求の絶対または相対 URL = 現在の応答ヘッダーのヘッダー値
* 次の要求のクエリ パラメーター = 現在の応答本文のプロパティ値
* 次の要求のクエリ パラメーター = 現在の応答ヘッダーのヘッダー値
* 次の要求のヘッダー = 現在の応答本文のプロパティ値
* 次の要求のヘッダー = 現在の応答ヘッダーのヘッダー値

**改ページ位置の自動修正規則** はデータセット内のディクショナリとして定義されます。これには、大文字と小文字が区別される、1 つまたは複数のキーと値のペアが含まれます。 構成は 2 番目のページから始まる要求を生成するのに使用されます。 コネクタで HTTP 状態コード 204 (コンテンツなし) が取得されるか、"paginationRules" 内のいずれかの JSONPath 式で null が返されると、繰り返し処理が停止されます。

改ページ位置の自動修正規則で **サポートされるキー**:

| Key | 説明 |
|:--- |:--- |
| AbsoluteUrl | 次の要求を発行する URL を示します。 これは、**絶対 URL と相対 URL のどちらか** です。 |
| QueryParameters.*request_query_parameter* または QueryParameters['request_query_parameter'] | "request_query_parameter" は、次の HTTP 要求 URL 内で 1 つのクエリ パラメーター名を参照するユーザー定義です。 |
| Headers.*request_header* または Headers['request_header'] | "request_header" は、次の HTTP 要求内で 1 つのヘッダー名を参照するユーザー定義です。 |
| EndCondition:*end_condition* | "end_condition" は、次の HTTP 要求で改ページ ループを終了させる条件を示すユーザー定義です。 |
| MaxRequestNumber | 改ページ要求の最大数を示します。 これを空のままにすると、制限がなくなります。 |
| SupportRFC5988 | RFC 5988 は、改ページ規則でサポートされています。 既定では、これは true に設定されています。 これは、他の改ページ規則が定義されていない場合にのみ優先されます。

改ページ位置の自動修正規則で **サポートされる値**:

| 値 | 説明 |
|:--- |:--- |
| Headers.*response_header* または Headers['response_header'] | "response_header" は、現在の HTTP 応答内で 1 つのヘッダー名を参照するユーザー定義で、その値は、次の要求を発行するために使用されます。 |
| (応答本文のルートを表す) "$" から始まる JSONPath 式 | 応答本文には、1 つの JSON オブジェクトのみを含める必要があります。 JSONPath 式では、1 つのプリミティブ値が返される必要があります。この値は、次の要求を発行するために使用されます。 |

**例:**

Facebook Graph API によって、次の構造で応答が返されます。この場合、次のページの URL は ***paging.next*** で表されます。

```json
{
    "data": [
        {
            "created_time": "2017-12-12T14:12:20+0000",
            "name": "album1",
            "id": "1809938745705498_1809939942372045"
        },
        {
            "created_time": "2017-12-12T14:14:03+0000",
            "name": "album2",
            "id": "1809938745705498_1809941802371859"
        },
        {
            "created_time": "2017-12-12T14:14:11+0000",
            "name": "album3",
            "id": "1809938745705498_1809941879038518"
        }
    ],
    "paging": {
        "cursors": {
            "after": "MTAxNTExOTQ1MjAwNzI5NDE=",
            "before": "NDMyNzQyODI3OTQw"
        },
        "previous": "https://graph.facebook.com/me/albums?limit=25&before=NDMyNzQyODI3OTQw",
        "next": "https://graph.facebook.com/me/albums?limit=25&after=MTAxNTExOTQ1MjAwNzI5NDE="
    }
}
```

対応する REST コピー アクティビティ ソース構成、特に `paginationRules` は次のようになります。

```json
"typeProperties": {
    "source": {
        "type": "RestSource",
        "paginationRules": {
            "AbsoluteUrl": "$.paging.next"
        },
        ...
    },
    "sink": {
        "type": "<sink type>"
    }
}
```

## <a name="use-oauth"></a>OAuth を使用する
このセクションでは、OAuth を使って REST コネクタから Azure Data Lake Storage にデータを JSON 形式でコピーするためのソリューション テンプレートの使用方法について説明します。 

### <a name="about-the-solution-template"></a>ソリューション テンプレートについて

このテンプレートには、2 つのアクティビティが含まれています。
- **Web** アクティビティでは、ベアラー トークンを取得し、それを後のコピー アクティビティに認証として渡します。
- **コピー** アクティビティでは、データを REST から Azure Data Lake Storage にコピーします。

このテンプレートには、次の 2 つのパラメーターが定義されています。
- **SinkContainer** は、Azure Data Lake Storage 内のデータのコピー先となるルート フォルダーのパスです。 
- **SinkDirectory** は、Azure Data Lake Storage 内のデータのコピー先となるルートの下のディレクトリ パスです。 

### <a name="how-to-use-this-solution-template"></a>このソリューション テンプレートの使用方法

1. **[Copy from REST or HTTP using OAuth]** (OAuth を使用して REST または HTTP からコピーする) テンプレートにアクセスします。 コピー元の接続用に新しい接続を作成します。 
    :::image type="content" source="media/solution-template-copy-from-rest-or-http-using-oauth/source-connection.png" alt-text="新しい接続を作成する":::

    新しいリンクされたサービス (REST) 設定の主要な手順を以下に示します。
    
     1. **[ベース URL]** で、自分のソース REST サービスの URL パラメーターを指定します。 
     2. **[認証の種類]** で *[匿名]* を選択します。
        :::image type="content" source="media/solution-template-copy-from-rest-or-http-using-oauth/new-rest-connection.png" alt-text="新しい REST 接続":::

2. コピー先の接続用に新しい接続を作成します。  
    :::image type="content" source="media/solution-template-copy-from-rest-or-http-using-oauth/destination-connection.png" alt-text="新しい Gen2 接続":::

3. **[このテンプレートを使用]** を選択します。
    :::image type="content" source="media/solution-template-copy-from-rest-or-http-using-oauth/use-this-template.png" alt-text="このテンプレートを使用":::

4. 次の例に示すように、作成されたパイプラインが表示されます。:::image type="content" source="media/solution-template-copy-from-rest-or-http-using-oauth/pipeline.png" alt-text="テンプレートから作成されたパイプラインを示すスクリーンショット。":::

5. **[Web]** アクティビティを選択します。 **[設定]** で、対応する **[URL]** 、 **[Method]** (メソッド)、 **[Headers]** (ヘッダー)、および **[Body]** (本文) を指定して、データのコピー元となるサービスのログイン API から OAuth ベアラー トークンを取得します。 テンプレート内のプレースホルダーは、Azure Active Directory (AAD) OAuth のサンプルを示しています。 AAD 認証が REST コネクタによってネイティブにサポートされていることに注意してください。OAuth フローの一例を次に示します。 

    | プロパティ | 説明 |
    |:--- |:--- |
    | URL |OAuth ベアラー トークンの取得元の URL を指定します。 たとえば、このサンプルでは https://login.microsoftonline.com/microsoft.onmicrosoft.com/oauth2/token です |
    | Method | HTTP メソッド。 使用できる値は **Post** と **Get** です。 | 
    | ヘッダー | [ヘッダー] はユーザー定義であり、HTTP 要求内で 1 つのヘッダー名を参照します。 | 
    | Body | HTTP 要求の本文。 | 

    :::image type="content" source="media/solution-template-copy-from-rest-or-http-using-oauth/web-settings.png" alt-text="パイプライン":::

6. **[データのコピー]** アクティビティで *[ソース]* タブを選択すると、前の手順で取得したベアラー トークン (access_token) が、[追加ヘッダー] で **[認証]**  として [データのコピー] アクティビティに渡されることを確認できます。 パイプラインの実行を開始する前に、次のプロパティの設定を確認してください。

    | プロパティ | 説明 |
    |:--- |:--- |
    | 要求メソッド | HTTP メソッド。 使用できる値は、**Get** (既定値) と **Post** です。 | 
    | 追加ヘッダー | 追加の HTTP 要求ヘッダー。| 

   :::image type="content" source="media/solution-template-copy-from-rest-or-http-using-oauth/copy-data-settings.png" alt-text="コピー元の認証":::

7. **[デバッグ]** を選択し、 **[パラメーター]** で入力し、 **[完了]** を選択します。
   :::image type="content" source="media/solution-template-copy-from-rest-or-http-using-oauth/pipeline-run.png" alt-text="パイプラインの実行"::: 

8. パイプラインの実行が正常に完了すると、次の例のような結果が表示されます。:::image type="content" source="media/solution-template-copy-from-rest-or-http-using-oauth/run-result.png" alt-text="パイプラインの実行結果"::: 

9. **[アクション]** 列で WebActivity の [出力] アイコンをクリックすると、サービスによって返された access_token が表示されます。

   :::image type="content" source="media/solution-template-copy-from-rest-or-http-using-oauth/token-output.png" alt-text="トークンの出力"::: 

10. **アクション** 列で CopyActivity の [入力] アイコンをクリックすると、WebActivity によって取得された access_token が、認証のために CopyActivity に渡されます。 

    :::image type="content" source="media/solution-template-copy-from-rest-or-http-using-oauth/token-input.png" alt-text="トークンの入力":::
        
    >[!CAUTION] 
    >トークンがプレーン テキストでログに記録されないようにするには、Web アクティビティの "セキュリティで保護された出力" とコピー アクティビティの "セキュリティで保護された入力" を有効にします。


## <a name="export-json-response-as-is"></a>JSON の応答をそのままエクスポートする

この REST コネクタを使用して REST API JSON の応答をそのままさまざまなファイル ベースのストアにエクスポートできます。 このようなスキーマに依存しないコピーを実現するには、データセットの "構造" ("*スキーマ*" とも呼ばれる) のセクションと、コピー アクティビティでのスキーマ マッピングをスキップします。

## <a name="schema-mapping"></a>スキーマ マッピング

REST エンドポイントから表形式のシンクにデータをコピーするには、[スキーマ マッピング](copy-activity-schema-and-type-mapping.md#schema-mapping)を参照してください。

## <a name="next-steps"></a>次のステップ

Azure Data Factory のコピー アクティビティによってソースおよびシンクとしてサポートされるデータ ストアの一覧については、「[サポートされるデータ ストアと形式](copy-activity-overview.md#supported-data-stores-and-formats)」を参照してください。
