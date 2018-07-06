---
title: Azure Data Factory を使用した HTTP ソースからのデータのコピー | Microsoft Docs
description: Azure Data Factory パイプラインでコピー アクティビティを使用して、クラウドまたはオンプレミスの HTTP ソースからサポートされているシンク データ ストアへデータをコピーする方法について説明します。
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
ms.openlocfilehash: a27d90006d31c83b5ebe6cfc4a8d97969743a91e
ms.sourcegitcommit: 0c490934b5596204d175be89af6b45aafc7ff730
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/27/2018
ms.locfileid: "37049860"
---
# <a name="copy-data-from-http-endpoint-using-azure-data-factory"></a>Azure Data Factory を使用した HTTP エンドポイントからのデータのコピー
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Version 1](v1/data-factory-http-connector.md)
> * [現在のバージョン](connector-http.md)

この記事では、Azure Data Factory のコピー アクティビティを使用して、HTTP エンドポイントからデータコピーする方法について説明します。 この記事は、コピー アクティビティの概要を示している[コピー アクティビティの概要](copy-activity-overview.md)に関する記事に基づいています。

## <a name="supported-capabilities"></a>サポートされる機能

HTTP ソースから、サポートされている任意のシンク データ ストアにデータをコピーできます。 コピー アクティビティによってソースまたはシンクとしてサポートされているデータ ストアの一覧については、[サポートされているデータ ストア](copy-activity-overview.md#supported-data-stores-and-formats)に関する記事の表をご覧ください。

具体的には、この HTTP コネクタは以下をサポートします。

- HTTP の **GET** または **POST** メソッドを使用して、HTTP/s エンドポイントからデータを取得する。
- **Anonymous**、**Basic**、**Digest**、**Windows**、および **ClientCertificate** 認証を使用して、データを取得する。
- HTTP 応答をそのままコピーするか、[サポートされているファイル形式と圧縮コーデック](supported-file-formats-and-compression-codecs.md)を使用して HTTP 応答を解析する。

このコネクタと [Web テーブル コネクタ](connector-web-table.md)の違いは、後者が Web HTML ページからテーブルの内容を抽出するために使用されることです。

>[!TIP]
>ADF で HTTP コネクタを構成する前に取得するデータに対する HTTP 要求をテストするには、ヘッダーおよび本文の要件の API 仕様から情報を取得し、Postman または Web ブラウザーなどのツールを使用して検証を行います。

## <a name="getting-started"></a>使用の開始

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

次のセクションでは、HTTP コネクタに固有の Data Factory エンティティの定義に使用されるプロパティについて詳しく説明します。

## <a name="linked-service-properties"></a>リンクされたサービスのプロパティ

HTTP のリンクされたサービスでは、次のプロパティがサポートされます。

| プロパティ | 説明 | 必須 |
|:--- |:--- |:--- |
| type | type プロパティを **HttpServer** に設定する必要があります。 | [はい] |
| url | Web サーバーへのベース URL | [はい] |
| enableServerCertificateValidation | HTTP エンドポイントに接続するときに、サーバーの SSL 証明書の検証を有効にするかどうかを指定します。 HTTPS サーバーが自己署名の証明書を使用している場合、これを false に設定します。 | いいえ。既定値は true です。 |
| authenticationType | 認証の種類を指定します。 使用可能な値は、**Anonymous**、**Basic**、**Digest**、**Windows**、**ClientCertificate** です。 <br><br> これらの認証の種類それぞれのプロパティと JSON の使用例については、この表の後のセクションを参照してください。 | [はい] |
| connectVia | データ ストアに接続するために使用される[統合ランタイム](concepts-integration-runtime.md)。 Azure 統合ランタイムまたは自己ホスト型統合ランタイム (データ ストアがプライベート ネットワークにある場合) を使用できます。 指定されていない場合は、既定の Azure 統合ランタイムが使用されます。 |いいえ  |

### <a name="using-basic-digest-or-windows-authentication"></a>基本、ダイジェスト、または Windows 認証の使用

"authenticationType" プロパティを **Basic**、**Digest**、または **Windows** に設定し、前のセクションに説明されている汎用プロパティと共に次のプロパティを指定します。

| プロパティ | 説明 | 必須 |
|:--- |:--- |:--- |
| userName | HTTP エンドポイントにアクセスするためのユーザー名。 | [はい] |
| password | ユーザー (userName) のパスワード。 このフィールドを SecureString としてマークして Data Factory に安全に保管するか、[Azure Key Vault に格納されているシークレットを参照](store-credentials-in-key-vault.md)します。 | [はい] |

**例**

```json
{
    "name": "HttpLinkedService",
    "properties": {
        "type": "HttpServer",
        "typeProperties": {
            "authenticationType": "Basic",
            "url" : "<HTTP endpoint>",
            "userName": "<username>",
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

ClientCertificate 認証を使用するには、"authenticationType" プロパティを **ClientCertificate** に設定し、前のセクションに説明されている汎用プロパティと共に次のプロパティを指定します。

| プロパティ | 説明 | 必須 |
|:--- |:--- |:--- |
| embeddedCertData | Base64 でエンコードされた証明書データ。 | `embeddedCertData` または `certThumbprint` を指定します。 |
| certThumbprint | セルフホステッド統合ランタイム コンピューターの証明書ストアにインストールされている証明書の拇印。 セルフホステッド統合ランタイムが connectVia で指定されている場合にのみ適用されます。 | `embeddedCertData` または `certThumbprint` を指定します。 |
| password | 証明書に関連付けられているパスワード。 このフィールドを SecureString としてマークして Data Factory に安全に保管するか、[Azure Key Vault に格納されているシークレットを参照](store-credentials-in-key-vault.md)します。 | いいえ  |

認証に "certThumbprint" を使用し、証明書がローカル コンピューターの個人用ストアにインストールされている場合は、セルフホステッド統合ランタイムに読み取りアクセス許可を付与する必要があります。

1. Microsoft 管理コンソール (MMC) を起動します。 **ローカル コンピューター**を対象とする **[証明書]** スナップインを追加します。
2. **[証明書]**、**[個人]** の順に展開し、**[証明書]** をクリックします。
3. 個人用ストアの証明書を右クリックし、**[すべてのタスク]** -> **[秘密キーの管理]** の順に選択します。
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
            "embeddedCertData": "<base64 encoded cert data>",
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

## <a name="dataset-properties"></a>データセットのプロパティ

データセットを定義するために使用できるセクションとプロパティの完全な一覧については、データセットに関する記事をご覧ください。 このセクションでは、HTTP データセット でサポートされるプロパティの一覧を示します。

HTTP からデータをコピーするには、データセットの type プロパティを **HttpFile** に設定します。 次のプロパティがサポートされています。

| プロパティ | 説明 | 必須 |
|:--- |:--- |:--- |
| type | データセットの type プロパティを **HttpFile** に設定する必要があります。 | [はい] |
| relativeUrl | データを含むリソースへの相対 URL。 このプロパティが指定されていないとき、リンクされたサービス定義に指定されている URL のみが使用されます。 | いいえ  |
| requestMethod | Http メソッド。<br/>使用できる値は、**Get** (既定値) または **Post** です。 | いいえ  |
| additionalHeaders | 追加の HTTP 要求ヘッダー。 | いいえ  |
| requestBody | HTTP 要求の本文。 | いいえ  |
| format | データを解析せずに**データを HTTP エンドポイントからそのまま取得**し、ファイル ベースのストアにコピーする場合は、入力と出力の両方のデータセット定義で format セクションをスキップします。<br/><br/>コピー中に HTTP 応答の内容を解析する場合、サポートされているファイル形式は、**TextFormat**、**JsonFormat**、**AvroFormat**、**OrcFormat**、**ParquetFormat** です。 形式の **type** プロパティをいずれかの値に設定します。 詳細については、[Json Format](supported-file-formats-and-compression-codecs.md#json-format)、[Text Format](supported-file-formats-and-compression-codecs.md#text-format)、[Avro Format](supported-file-formats-and-compression-codecs.md#avro-format)、[Orc Format](supported-file-formats-and-compression-codecs.md#orc-format)、[Parquet Format](supported-file-formats-and-compression-codecs.md#parquet-format) の各セクションを参照してください。 |いいえ  |
| compression | データの圧縮の種類とレベルを指定します。 詳細については、[サポートされるファイル形式と圧縮コーデック](supported-file-formats-and-compression-codecs.md#compression-support)に関する記事を参照してください。<br/>サポートされる種類は、**GZip**、**Deflate**、**BZip2**、および **ZipDeflate** です。<br/>サポートされるレベルは、**Optimal** と **Fastest** です。 |いいえ  |

**例 1: Get メソッド (既定値) の使用**

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

**例 2: Post メソッドの使用**

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

## <a name="copy-activity-properties"></a>コピー アクティビティのプロパティ

アクティビティの定義に利用できるセクションとプロパティの完全な一覧については、[パイプライン](concepts-pipelines-activities.md)に関する記事を参照してください。 このセクションでは、HTTP ソースでサポートされるプロパティの一覧を示します。

### <a name="http-as-source"></a>ソースとしての HTTP

HTTP からデータをコピーするは、コピー アクティビティでソースの種類を **HttpSource** に設定します。 コピー アクティビティの **source** セクションでは、次のプロパティがサポートされます。

| プロパティ | 説明 | 必須 |
|:--- |:--- |:--- |
| type | コピー アクティビティのソースの type プロパティを **HttpSource** に設定する必要があります。 | [はい] |
| httpRequestTimeout | HTTP 要求が応答を取得する際のタイムアウト (TimeSpan)。 応答データの読み取りのタイムアウトではなく、応答の取得のタイムアウトです。<br/> 既定値は 00:01:40 です。  | いいえ  |

**例:**

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


## <a name="next-steps"></a>次の手順
Azure Data Factory のコピー アクティビティによってソースおよびシンクとしてサポートされるデータ ストアの一覧については、[サポートされるデータ ストア](copy-activity-overview.md#supported-data-stores-and-formats)の表をご覧ください。
