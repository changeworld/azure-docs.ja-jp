---
title: Azure Data Factory を使用して Phoenix からデータをコピーする | Microsoft Docs
description: Azure Data Factory パイプラインでコピー アクティビティを使用して、Phoenix のデータをサポートされているシンク データ ストアにコピーする方法について説明します。
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
ms.date: 04/19/2017
ms.author: jingwang
ms.openlocfilehash: fc4eb2b717ea9f4c1b2813db7dcf02062948bae0
ms.sourcegitcommit: 0c490934b5596204d175be89af6b45aafc7ff730
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/27/2018
ms.locfileid: "37048358"
---
# <a name="copy-data-from-phoenix-using-azure-data-factory"></a>Azure Data Factory を使用して Phoenix からデータをコピーする 

この記事では、Azure Data Factory のコピー アクティビティを使用して、Phoenix からデータをコピーする方法について説明します。 この記事は、コピー アクティビティの概要を示している[コピー アクティビティの概要](copy-activity-overview.md)に関する記事に基づいています。

## <a name="supported-capabilities"></a>サポートされる機能

Phoenix から、サポートされている任意のシンク データ ストアにデータをコピーできます。 コピー アクティビティによってソースまたはシンクとしてサポートされているデータ ストアの一覧については、[サポートされているデータ ストア](copy-activity-overview.md#supported-data-stores-and-formats)に関する記事の表をご覧ください。

Azure Data Factory では接続を有効にする組み込みのドライバーが提供されるので、このコネクタを使用してドライバーを手動でインストールする必要はありません。

## <a name="getting-started"></a>使用の開始

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

次のセクションでは、Phoenix コネクタに固有の Data Factory エンティティの定義に使用されるプロパティについて詳しく説明します。

## <a name="linked-service-properties"></a>リンクされたサービスのプロパティ

Phoenix のリンクされたサービスでは、次のプロパティがサポートされます。

| プロパティ | 説明 | 必須 |
|:--- |:--- |:--- |
| type | type プロパティは **Phoenix** に設定する必要があります | [はい] |
| host | Phoenix サーバーの IP アドレスまたはホスト名。 (つまり、192.168.222.160)  | [はい] |
| ポート | Phoenix サーバーがクライアント接続のリッスンに使用する TCP ポート。 既定値は 8765 です。 Azure HDInsights に接続する場合は、port を 443 と指定します。 | いいえ  |
| httpPath | Phoenix サーバーに対応する部分的な URL。 (つまり、/gateway/sandbox/phoenix/version)。 WindowsAzureHDInsightService を使う場合の既定値は `hbasephoenix` です。  | いいえ  |
| authenticationType | Phoenix サーバーへの接続に使用する認証メカニズム。 <br/>使用可能な値: **Anonymous**、**UsernameAndPassword**、**WindowsAzureHDInsightService** | [はい] |
| username | Phoenix サーバーへの接続に使用されるユーザー名。  | いいえ  |
| password | ユーザー名に対応するパスワード。 このフィールドを SecureString としてマークして Data Factory に安全に保管するか、[Azure Key Vault に格納されているシークレットを参照](store-credentials-in-key-vault.md)します。 | いいえ  |
| enableSsl | SSL を使用して、サーバーへの接続を暗号化するかどうかを指定します。 既定値は false です。  | いいえ  |
| trustedCertPath | SSL 経由で接続するときにサーバーを検証するための信頼された CA 証明書を含む .pem ファイルの完全なパス。 このプロパティは、セルフホステッド IR で SSL を使用する場合にのみ設定できます。 既定値は、IR でインストールされる cacerts.pem ファイルです。  | いいえ  |
| useSystemTrustStore | システムの信頼ストアと指定した PEM ファイルのどちらの CA 証明書を使用するかを指定します。 既定値は false です。  | いいえ  |
| allowHostNameCNMismatch | SSL 経由で接続するときに、CA が発行した SSL 証明書名がサーバーのホスト名と一致する必要があるかどうかを指定します。 既定値は false です。  | いいえ  |
| allowSelfSignedServerCert | サーバーからの自己署名証明書を許可するかどうかを指定します。 既定値は false です。  | いいえ  |
| connectVia | データ ストアに接続するために使用される[統合ランタイム](concepts-integration-runtime.md)。 セルフホステッド統合ランタイムまたは Azure 統合ランタイム (データ ストアがパブリックにアクセスできる場合) を使用できます。 指定されていない場合は、既定の Azure 統合ランタイムが使用されます。 |いいえ  |

**例:**

```json
{
    "name": "PhoenixLinkedService",
    "properties": {
        "type": "Phoenix",
        "typeProperties": {
            "host" : "<cluster>.azurehdinsight.net",
            "port" : "443",
            "httpPath" : "hbasephoenix",
            "authenticationType" : "WindowsAzureHDInsightService",
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

データセットを定義するために使用できるセクションとプロパティの完全な一覧については、[データセット](concepts-datasets-linked-services.md)に関する記事をご覧ください。 このセクションでは、Phoenix データセットでサポートされるプロパティの一覧を示します。

Phoenix からデータをコピーするには、データセットの type プロパティを **PhoenixObject** に設定します。 この種類のデータセットに追加の種類固有のプロパティはありません。

**例**

```json
{
    "name": "PhoenixDataset",
    "properties": {
        "type": "PhoenixObject",
        "linkedServiceName": {
            "referenceName": "<Phoenix linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

## <a name="copy-activity-properties"></a>コピー アクティビティのプロパティ

アクティビティの定義に利用できるセクションとプロパティの完全な一覧については、[パイプライン](concepts-pipelines-activities.md)に関する記事を参照してください。 このセクションでは、Phoenix ソースでサポートされるプロパティの一覧を示します。

### <a name="phoenixsource-as-source"></a>ソースとしての PhoenixSource

Phoenix からデータをコピーするには、コピー アクティビティのソース タイプを **PhoenixSource** に設定します。 コピー アクティビティの **source** セクションでは、次のプロパティがサポートされます。

| プロパティ | 説明 | 必須 |
|:--- |:--- |:--- |
| type | コピー アクティビティのソースの type プロパティを **PhoenixSource** に設定する必要があります。 | [はい] |
| クエリ | カスタム SQL クエリを使用してデータを読み取ります。 たとえば、「 `"SELECT * FROM MyTable"`」のように入力します。 | [はい] |

**例:**

```json
"activities":[
    {
        "name": "CopyFromPhoenix",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Phoenix input dataset name>",
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
                "type": "PhoenixSource",
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
Azure Data Factory のコピー アクティビティによってソースおよびシンクとしてサポートされるデータ ストアの一覧については、[サポートされるデータ ストア](copy-activity-overview.md#supported-data-stores-and-formats)の表をご覧ください。
