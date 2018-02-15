---
title: "Azure Data Factory (ベータ) を使用して Google BigQuery からデータをコピーする | Microsoft Docs"
description: "データ ファクトリ パイプラインでコピー アクティビティを使用して、Google BigQuery からサポートされているシンク データ ストアへデータをコピーする方法について説明します。"
services: data-factory
documentationcenter: 
author: linda33wj
manager: jhubbard
editor: spelluru
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/05/2018
ms.author: jingwang
ms.openlocfilehash: 3b559e64f38727b1e390160515b7614ad1dfaa97
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/01/2018
---
# <a name="copy-data-from-google-bigquery-by-using-azure-data-factory-beta"></a>Azure Data Factory (ベータ) を使用して Google BigQuery からデータをコピーする

この記事では、Azure Data Factory のコピー アクティビティを使用して、Google BigQuery からデータをコピーする方法について説明します。 この記事は、コピー アクティビティの概要を示している[コピー アクティビティの概要](copy-activity-overview.md)に関する記事に基づいています。

> [!NOTE]
> この記事は、現在プレビュー段階にある Data Factory のバージョン 2 に適用されます。 一般公開されている Data Factory サービスのバージョン 1 を使用する場合は、[バージョン 1 でのコピー アクティビティ](v1/data-factory-data-movement-activities.md)に関する記事をご覧ください。

> [!IMPORTANT]
> このコネクタは、現在ベータ版です。 実際にお試しいただき、フィードバックをお寄せください。 運用環境では使用しないでください。

## <a name="supported-capabilities"></a>サポートされる機能

Google BigQuery から、サポートされている任意のシンク データ ストアにデータをコピーできます。 コピー アクティビティによってソースまたはシンクとしてサポートされるデータ ストアの一覧については、[サポートされるデータ ストア](copy-activity-overview.md#supported-data-stores-and-formats)に関する記事の表をご覧ください。

 Data Factory では、接続を可能にする組み込みのドライバーが提供されます。 そのため、このコネクタを使用するためにドライバーを手動でインストールする必要はありません。

## <a name="get-started"></a>作業開始

[!INCLUDE [data-factory-v2-connector-get-started-2](../../includes/data-factory-v2-connector-get-started-2.md)]

次のセクションでは、Google BigQuery コネクタに固有の Data Factory エンティティの定義に使用されるプロパティについて詳しく説明します。

## <a name="linked-service-properties"></a>リンクされたサービスのプロパティ

Google BigQuery のリンクされたサービスでは、次のプロパティがサポートされます。

| プロパティ | [説明] | 必須 |
|:--- |:--- |:--- |
| 型 | type プロパティは **GoogleBigQuery** に設定する必要があります。 | [はい] |
| project | クエリ対象の既定の BigQuery プロジェクトのプロジェクト ID。  | [はい] |
| additionalProjects | アクセス対象のパブリック BigQuery プロジェクトのプロジェクト ID のコンマ区切りリスト。  | いいえ  |
| requestGoogleDriveScope | Google Drive へのアクセスを要求するかどうか。 Google Drive のアクセスを許可すると、BigQuery データと Google Drive のデータを結合するフェデレーション テーブルのサポートが有効になります。 既定値は **false** です。  | いいえ  |
| authenticationType | 認証に使用される OAuth 2.0 認証メカニズム。 ServiceAuthentication はセルフホステッド統合ランタイムのみで使用できます。 <br/>使用可能な値は、**ServiceAuthentication** および **UserAuthentication** です。 | [はい] |
| refreshToken | UserAuthentication で BigQuery へのアクセスを承認するために使用される、Google から取得した更新トークン。 このフィールドを SecureString としてマークして、Data Factory に安全に格納することができます。 また、Azure Key Vault にパスワードを格納して、データ コピーの実行時にコピー アクティビティがそこからプルするようにもできます。 詳細については、「[Azure Key Vault への資格情報の格納](store-credentials-in-key-vault.md)」をご覧ください。 | いいえ  |
| email | ServiceAuthentication で使用されるサービス アカウントの電子メール ID。 これはセルフホステッド統合ランタイムのみで使用できます。  | いいえ  |
| keyFilePath | サービス アカウントの電子メール アドレスを認証するために使用される .p12 キー ファイルへの完全なパス。 これはセルフホステッド統合ランタイムのみで使用できます。  | いいえ  |
| trustedCertPath | SSL 経由で接続するときにサーバーを検証するために使用する、信頼された CA 証明書を含む .pem ファイルの完全なパス。 このプロパティは、セルフホステッド統合ランタイムで SSL を使用する場合にのみ設定できます。 既定値は、IR でインストールされる cacerts.pem ファイルです。  | いいえ  |
| useSystemTrustStore | システムの信頼ストアと指定した .pem ファイルのどちらの CA 証明書を使用するかを指定します。 既定値は **false** です。  | いいえ  |

**例:**

```json
{
    "name": "GoogleBigQueryLinkedService",
    "properties": {
        "type": "GoogleBigQuery",
        "typeProperties": {
            "project" : "<project ID>",
            "additionalProjects" : "<additional project IDs>",
            "requestGoogleDriveScope" : true,
            "authenticationType" : "UserAuthentication",
            "refreshToken": {
                 "type": "SecureString",
                 "value": "<refresh token>"
            }
        }
    }
}
```

## <a name="dataset-properties"></a>データセットのプロパティ

データセットを定義するために使用できるセクションおよびプロパティの完全な一覧については、[データセット](concepts-datasets-linked-services.md)に関する記事をご覧ください。 このセクションでは、Google BigQuery データセットでサポートされるプロパティの一覧を示します。

Google BigQuery からデータをコピーするには、データセットの type プロパティを **GoogleBigQueryObject** に設定します。 この種類のデータセットに追加の種類固有のプロパティはありません。

**例**

```json
{
    "name": "GoogleBigQueryDataset",
    "properties": {
        "type": "GoogleBigQueryObject",
        "linkedServiceName": {
            "referenceName": "<GoogleBigQuery linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

## <a name="copy-activity-properties"></a>コピー アクティビティのプロパティ

アクティビティの定義に利用できるセクションとプロパティの完全な一覧については、[パイプライン](concepts-pipelines-activities.md)に関する記事を参照してください。 このセクションでは、Google BigQuery ソース タイプでサポートされるプロパティの一覧を示します。

### <a name="googlebigquerysource-as-a-source-type"></a>ソース タイプとしての GoogleBigQuerySource

Google BigQuery からデータをコピーするには、コピー アクティビティのソースの種類を **GoogleBigQuerySource** に設定します。 コピー アクティビティの **source** セクションでは、次のプロパティがサポートされます。

| プロパティ | [説明] | 必須 |
|:--- |:--- |:--- |
| 型 | コピー アクティビティのソースの type プロパティは **GoogleBigQuerySource** に設定する必要があります。 | [はい] |
| クエリ | カスタム SQL クエリを使用してデータを読み取ります。 例: `"SELECT * FROM MyTable"`。 | [はい] |

**例:**

```json
"activities":[
    {
        "name": "CopyFromGoogleBigQuery",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<GoogleBigQuery input dataset name>",
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
                "type": "GoogleBigQuerySource",
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
Data Factory のコピー アクティビティによってソースおよびシンクとしてサポートされるデータ ストアの一覧については、[サポートされるデータ ストア](copy-activity-overview.md#supported-data-stores-and-formats)の表をご覧ください。
