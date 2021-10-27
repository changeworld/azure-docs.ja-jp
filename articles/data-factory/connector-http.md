---
title: HTTP ソースからデータをコピーする
titleSuffix: Azure Data Factory & Azure Synapse
description: Azure Data Factory または Azure Synapse Analytics パイプラインで Copy アクティビティを使用して、クラウドまたはオンプレミスの HTTP ソースからサポートされているシンク データ ストアにデータをコピーする方法について説明します。
author: jianleishen
ms.service: data-factory
ms.subservice: data-movement
ms.custom: synapse
ms.topic: conceptual
ms.date: 09/09/2021
ms.author: jianleishen
ms.openlocfilehash: 7bb163d0864290962465f18b65a73236e3ff3ecb
ms.sourcegitcommit: 91915e57ee9b42a76659f6ab78916ccba517e0a5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/15/2021
ms.locfileid: "130040420"
---
# <a name="copy-data-from-an-http-endpoint-by-using-azure-data-factory-or-azure-synapse-analytics"></a>Azure Data Factory または Azure Synapse Analytics を使用して HTTP エンドポイントからデータをコピーする

> [!div class="op_single_selector" title1="使用している Data Factory サービスのバージョンを選択してください:"]
> * [Version 1](v1/data-factory-http-connector.md)
> * [現在のバージョン](connector-http.md)

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

この記事では、Azure Data Factory および Azure Synapse で Copy アクティビティを使用して、HTTP エンドポイントからデータをコピーする方法について説明します。 この記事は、Copy アクティビティの概要を説明する [Copy アクティビティ](copy-activity-overview.md)に関する記事に基づいています。

この HTTP コネクタ、[REST コネクタ](connector-rest.md)および [Web テーブル コネクタ](connector-web-table.md)の違いは次のとおりです。

- **REST コネクタ** では、具体的には RESTful API からのデータのコピーがサポートされます。 
- **HTTP コネクタ** では一般的に、HTTP エンドポイントからデータを取得します (たとえば、ファイルをダウンロードします)。 REST コネクタが使用可能になる前に、HTTP コネクタを使用して RESTful API からデータをコピーする場合があります。これはサポートされますが、REST コネクタと比べると機能は低くなります。
- **Web テーブル コネクタ** では、HTML Web ページからテーブルの内容を抽出します。

## <a name="supported-capabilities"></a>サポートされる機能

この HTTP コネクタは、次のアクティビティでサポートされます。

- [サポートされるソース/シンク マトリックス](copy-activity-overview.md)での[コピー アクティビティ](copy-activity-overview.md)
- [Lookup アクティビティ](control-flow-lookup-activity.md)

HTTP ソースから、サポートされている任意のシンク データ ストアにデータをコピーできます。 コピー アクティビティでソースおよびシンクとしてサポートされているデータ ストアの一覧については、「[サポートされるデータ ストアと形式](copy-activity-overview.md#supported-data-stores-and-formats)」を参照してください。

この HTTP コネクタは、以下のために使用できます。

- HTTP の **GET** または **POST** メソッドを使用して、HTTP/S エンドポイントからデータを取得する。
- 次の認証のいずれかを使用してデータを取得する: **Anonymous**、**Basic**、**Digest**、**Windows**、**ClientCertificate**。
- HTTP 応答をそのままコピーするか、[サポートされているファイル形式と圧縮コーデック](supported-file-formats-and-compression-codecs.md)を使用して解析する。

> [!TIP]
> HTTP コネクタを構成する前に、データ取得のために HTTP 要求をテストするには、ヘッダーおよび本文の要件に関する API 仕様を確認してください。 Postman や Web ブラウザーのようなツールを使用して検証することができます。

## <a name="prerequisites"></a>前提条件

[!INCLUDE [data-factory-v2-integration-runtime-requirements](includes/data-factory-v2-integration-runtime-requirements.md)]

## <a name="get-started"></a>はじめに

[!INCLUDE [data-factory-v2-connector-get-started](includes/data-factory-v2-connector-get-started.md)]

## <a name="create-a-linked-service-to-an-http-source-using-ui"></a>UI を使用して HTTP ソースのリンク サービスを作成する

次の手順を使用して、Azure portal UI で HTTP ソースのリンク サービスを作成します。

1. Azure Data Factory または Synapse ワークスペースの [管理] タブに移動し、[リンクされたサービス] を選択して、[新規] をクリックします。

    # <a name="azure-data-factory"></a>[Azure Data Factory](#tab/data-factory)

    :::image type="content" source="media/doc-common-process/new-linked-service.png" alt-text="Azure Data Factory の UI で新しいリンク サービスを作成するスクリーンショット。":::

    # <a name="azure-synapse"></a>[Azure Synapse](#tab/synapse-analytics)

    :::image type="content" source="media/doc-common-process/new-linked-service-synapse.png" alt-text="Azure Synapse の UI を使用した新しいリンク サービスの作成を示すスクリーンショット。":::

2. HTTP を検索し、HTTP コネクタを選択します。

    :::image type="content" source="media/connector-http/http-connector.png" alt-text="HTTP コネクタのスクリーンショット。":::    

1. サービスの詳細を構成し、接続をテストして、新しいリンク サービスを作成します。

    :::image type="content" source="media/connector-http/configure-http-linked-service.png" alt-text="HTTP のリンク サービスの構成のスクリーンショット。":::

## <a name="connector-configuration-details"></a>コネクタの構成の詳細

以下のセクションでは、HTTP コネクタに固有のエンティティの定義に使用できるプロパティについて詳しく説明します。

## <a name="linked-service-properties"></a>リンクされたサービスのプロパティ

HTTP のリンクされたサービスでは、次のプロパティがサポートされます。

| プロパティ | 説明 | 必須 |
|:--- |:--- |:--- |
| type | **type** プロパティを **HttpServer** に設定する必要があります。 | はい |
| url | Web サーバーへのベース URL | はい |
| enableServerCertificateValidation | HTTP エンドポイントに接続するときに、サーバーの TLS/SSL 証明書の検証を有効にするかどうかを指定します。 HTTPS サーバーが自己署名証明書を使用している場合は、このプロパティを **false** に設定します。 | いいえ<br /> (既定値は **true** です)。 |
| authenticationType | 認証の種類を指定します。 使用できる値は、**Anonymous**、**Basic**、**Digest**、**Windows**、**ClientCertificate** です。 ユーザー ベースの OAuth はサポートされていません。 `authHeader` プロパティで認証ヘッダーを追加で構成することもできます。 このような認証の種類のその他のプロパティと JSON サンプルについては、この表の後のセクションを参照してください。 | はい |
| authHeaders | 追加の認証用 HTTP 要求ヘッダー。<br/> たとえば、API キー認証を使うには、認証の種類として "匿名" を選択し、ヘッダーに API キーを指定します。 | いいえ |
| connectVia | データ ストアに接続するために使用される [Integration Runtime](concepts-integration-runtime.md)。 詳細については、「[前提条件](#prerequisites)」セクションを参照してください。 指定されていない場合は、既定の Azure Integration Runtime が使用されます。 |いいえ |

### <a name="using-basic-digest-or-windows-authentication"></a>基本、ダイジェスト、または Windows 認証の使用

**authenticationType** プロパティを **Basic**、**Digest**、または **Windows** に設定します。 前のセクションで説明した汎用的なプロパティに加えて、次のプロパティを指定します。

| プロパティ | 説明 | 必須 |
|:--- |:--- |:--- |
| userName | HTTP エンドポイントにアクセスするために使用するユーザー名。 | はい |
| password | ユーザー (**userName** 値) のパスワード。 安全に保存するには、このフィールドを **SecureString** 型としてマークします。 [Azure Key Vault に格納されているシークレットを参照する](store-credentials-in-key-vault.md)こともできます。 | はい |

**例**

```json
{
    "name": "HttpLinkedService",
    "properties": {
        "type": "HttpServer",
        "typeProperties": {
            "authenticationType": "Basic",
            "url" : "<HTTP endpoint>",
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

### <a name="using-clientcertificate-authentication"></a>クライアント証明書認証の使用

ClientCertificate 認証を使用するには、**authenticationType** プロパティを **ClientCertificate** に設定します。 前のセクションで説明した汎用的なプロパティに加えて、次のプロパティを指定します。

| プロパティ | 説明 | 必須 |
|:--- |:--- |:--- |
| embeddedCertData | Base64 でエンコードされた証明書データ。 | **embeddedCertData** または **certThumbprint** のいずれかを指定します。 |
| certThumbprint | セルフホステッド統合ランタイム マシンの証明書ストアにインストールされている証明書の拇印。 セルフホステッド型の統合ランタイムが **connectVia** プロパティで指定されている場合にのみ適用されます。 | **embeddedCertData** または **certThumbprint** のいずれかを指定します。 |
| password | 証明書に関連付けられているパスワード。 安全に保存するには、このフィールドを **SecureString** 型としてマークします。 [Azure Key Vault に格納されているシークレットを参照する](store-credentials-in-key-vault.md)こともできます。 | いいえ |

認証に **certThumbprint** を使用し、証明書がローカル コンピューターの個人用ストアにインストールされている場合は、セルフホステッド統合ランタイムに読み取りアクセス許可を付与します。

1. Microsoft 管理コンソール (MMC) を開きます。 **ローカル コンピューター** を対象とする **[証明書]** スナップインを追加します。
2. **[証明書]**  >  **[個人]** を展開し、 **[証明書]** を選択します。
3. 個人用ストアの証明書を右クリックし、 **[すべてのタスク]**  >  **[秘密キーの管理]** の順に選択します。
3. **[セキュリティ]** タブで、証明書に対する読み取りアクセス権を使用して Integration Runtime Host Service (DIAHostService) を実行しているユーザー アカウントを追加します。

**例 1: certThumbprint の使用**

```json
{
    "name": "HttpLinkedService",
    "properties": {
        "type": "HttpServer",
        "typeProperties": {
            "authenticationType": "ClientCertificate",
            "url": "<HTTP endpoint>",
            "certThumbprint": "<thumbprint of certificate>"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

**例 2: embeddedCertData の使用**

```json
{
    "name": "HttpLinkedService",
    "properties": {
        "type": "HttpServer",
        "typeProperties": {
            "authenticationType": "ClientCertificate",
            "url": "<HTTP endpoint>",
            "embeddedCertData": "<Base64-encoded cert data>",
            "password": {
                "type": "SecureString",
                "value": "password of cert"
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
    "name": "HttpLinkedService",
    "properties": {
        "type": "HttpServer",
        "typeProperties": {
            "url": "<HTTP endpoint>",
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

データセットを定義するために使用できるセクションとプロパティの完全な一覧については、[データセット](concepts-datasets-linked-services.md)に関する記事をご覧ください。 

[!INCLUDE [data-factory-v2-file-formats](includes/data-factory-v2-file-formats.md)] 

HTTP では、形式ベースのデータセットの `location` 設定において、次のプロパティがサポートされています。

| プロパティ    | 説明                                                  | 必須 |
| ----------- | ------------------------------------------------------------ | -------- |
| type        | データセットの `location` の type プロパティは、**HttpServerLocation** に設定する必要があります。 | はい      |
| relativeUrl | データを含むリソースへの相対 URL。 HTTP コネクタは、次の結合された URL からデータをコピーします。`[URL specified in linked service][relative URL specified in dataset]`   | いいえ       |

> [!NOTE]
> サポートされる HTTP 要求のペイロード サイズは約 500 KB です。 Web エンドポイントに渡すペイロード サイズが 500 KB を超える場合は、より小さなチャンクにペイロードをまとめることを検討してください。

**例:**

```json
{
    "name": "DelimitedTextDataset",
    "properties": {
        "type": "DelimitedText",
        "linkedServiceName": {
            "referenceName": "<HTTP linked service name>",
            "type": "LinkedServiceReference"
        },
        "schema": [ < physical schema, optional, auto retrieved during authoring > ],
        "typeProperties": {
            "location": {
                "type": "HttpServerLocation",
                "relativeUrl": "<relative url>"
            },
            "columnDelimiter": ",",
            "quoteChar": "\"",
            "firstRowAsHeader": true,
            "compressionCodec": "gzip"
        }
    }
}
```

## <a name="copy-activity-properties"></a>コピー アクティビティのプロパティ

このセクションでは、HTTP ソースでサポートされているプロパティの一覧を示します。

アクティビティの定義に利用できるセクションとプロパティの完全な一覧については、[パイプライン](concepts-pipelines-activities.md)に関する記事を参照してください。 

### <a name="http-as-source"></a>ソースとしての HTTP

[!INCLUDE [data-factory-v2-file-formats](includes/data-factory-v2-file-formats.md)] 

HTTP では、形式ベースのコピー ソースの `storeSettings` 設定において、次のプロパティがサポートされています。

| プロパティ                 | 説明                                                  | 必須 |
| ------------------------ | ------------------------------------------------------------ | -------- |
| type                     | `storeSettings` の type プロパティは **HttpReadSettings** に設定する必要があります。 | はい      |
| requestMethod            | HTTP メソッド。 <br>使用できる値は、**Get** (既定値) と **Post** です。 | いいえ       |
| additionalHeaders         | 追加の HTTP 要求ヘッダー。                             | いいえ       |
| requestBody              | HTTP 要求の本文。                               | いいえ       |
| httpRequestTimeout           | HTTP 要求が応答を取得する際のタイムアウト (**TimeSpan** 値)。 この値は、応答データの読み取りのタイムアウトではなく、応答の取得のタイムアウトです。 既定値は **00:01:40** です。 | いいえ       |
| maxConcurrentConnections |アクティビティの実行中にデータ ストアに対して確立されたコンカレント接続数の上限。 コンカレント接続を制限する場合にのみ、値を指定します。| いいえ       |

**例:**

```json
"activities":[
    {
        "name": "CopyFromHTTP",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Delimited text input dataset name>",
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
                "type": "DelimitedTextSource",
                "formatSettings":{
                    "type": "DelimitedTextReadSettings",
                    "skipLineCount": 10
                },
                "storeSettings":{
                    "type": "HttpReadSettings",
                    "requestMethod": "Post",
                    "additionalHeaders": "<header key: header value>\n<header key: header value>\n",
                    "requestBody": "<body for POST HTTP request>"
                }
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

## <a name="legacy-models"></a>レガシ モデル

>[!NOTE]
>次のモデルは、下位互換性のために引き続きそのままサポートされます。 今後は、上記のセクションで説明した新しいモデルを使用することをお勧めします。作成 UI は、新しいモデルを生成するように切り替えられています。

### <a name="legacy-dataset-model"></a>レガシ データセット モデル

| プロパティ | 説明 | 必須 |
|:--- |:--- |:--- |
| type | データセットの **type** プロパティを **HttpFile** に設定する必要があります。 | はい |
| relativeUrl | データを含むリソースへの相対 URL。 このプロパティが指定されていない場合は、リンクされたサービス定義に指定されている URL のみが使用されます。 | いいえ |
| requestMethod | HTTP メソッド。 使用できる値は、**Get** (既定値) と **Post** です。 | いいえ |
| additionalHeaders | 追加の HTTP 要求ヘッダー。 | いいえ |
| requestBody | HTTP 要求の本文。 | いいえ |
| format | データを解析せずにデータを HTTP エンドポイントからそのまま取得し、ファイル ベースのストアにデータをコピーする場合は、入力と出力の両方のデータセット定義で **format** セクションをスキップします。<br/><br/>コピー中に HTTP 応答の内容を解析する場合に、サポートされるファイル形式の種類は、**TextFormat**、**JsonFormat**、**AvroFormat**、**OrcFormat**、**ParquetFormat**。 **format** の **type** プロパティをいずれかの値に設定します。 詳細については、[JSON 形式](supported-file-formats-and-compression-codecs-legacy.md#json-format)、[Text 形式](supported-file-formats-and-compression-codecs-legacy.md#text-format)、[Avro 形式](supported-file-formats-and-compression-codecs-legacy.md#avro-format)、[Orc 形式](supported-file-formats-and-compression-codecs-legacy.md#orc-format)、[Parquet 形式](supported-file-formats-and-compression-codecs-legacy.md#parquet-format)の各セクションを参照してください。 |いいえ |
| compression | データの圧縮の種類とレベルを指定します。 詳細については、[サポートされるファイル形式と圧縮コーデック](supported-file-formats-and-compression-codecs-legacy.md#compression-support)に関する記事を参照してください。<br/><br/>サポートされる種類は、**GZip**、**Deflate**、**BZip2**、**ZipDeflate** です。<br/>サポートされるレベルは、**Optimal** と **Fastest** です。 |いいえ |

> [!NOTE]
> サポートされる HTTP 要求のペイロード サイズは約 500 KB です。 Web エンドポイントに渡すペイロード サイズが 500 KB を超える場合は、より小さなチャンクにペイロードをまとめることを検討してください。

**例 1: Get メソッド (既定値) を使用する**

```json
{
    "name": "HttpSourceDataInput",
    "properties": {
        "type": "HttpFile",
        "linkedServiceName": {
            "referenceName": "<HTTP linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "relativeUrl": "<relative url>",
            "additionalHeaders": "Connection: keep-alive\nUser-Agent: Mozilla/5.0\n"
        }
    }
}
```

**例 2: Post メソッドを使用する**

```json
{
    "name": "HttpSourceDataInput",
    "properties": {
        "type": "HttpFile",
        "linkedServiceName": {
            "referenceName": "<HTTP linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "relativeUrl": "<relative url>",
            "requestMethod": "Post",
            "requestBody": "<body for POST HTTP request>"
        }
    }
}
```

### <a name="legacy-copy-activity-source-model"></a>レガシ コピー アクティビティ ソース モデル

| プロパティ | 説明 | 必須 |
|:--- |:--- |:--- |
| type | コピー アクティビティのソースの **type** プロパティを **HttpSource** に設定する必要があります。 | はい |
| httpRequestTimeout | HTTP 要求が応答を取得する際のタイムアウト (**TimeSpan** 値)。 この値は、応答データの読み取りのタイムアウトではなく、応答の取得のタイムアウトです。 既定値は **00:01:40** です。  | いいえ |

**例**

```json
"activities":[
    {
        "name": "CopyFromHTTP",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<HTTP input dataset name>",
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
                "type": "HttpSource",
                "httpRequestTimeout": "00:01:00"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

## <a name="next-steps"></a>次のステップ

コピー アクティビティでソースおよびシンクとしてサポートされているデータ ストアの一覧については、「[サポートされるデータ ストアと形式](copy-activity-overview.md#supported-data-stores-and-formats)」を参照してください。
