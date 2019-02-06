---
title: Azure Data Factory を使用するオープン ハブを介して SAP Business Warehouse からデータをコピーする | Microsoft Docs
description: Azure Data Factory パイプラインでコピー アクティビティを使用して、オープン ハブを介して SAP Business Warehouse (BW) から、サポートされているシンク データ ストアへデータをコピーする方法について説明します。
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/28/2019
ms.author: jingwang
ms.openlocfilehash: 74061eb081fcc7c2c84707f2414a2edfbfde3289
ms.sourcegitcommit: a7331d0cc53805a7d3170c4368862cad0d4f3144
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/30/2019
ms.locfileid: "55299539"
---
# <a name="copy-data-from-sap-business-warehouse-via-open-hub-using-azure-data-factory"></a>Azure Data Factory を使用するオープン ハブを介して SAP Business Warehouse からデータをコピーする

この記事では、Azure Data Factory のコピー アクティビティを使用して、オープン ハブを介して SAP Business Warehouse (BW) からデータをコピーする方法の概要を説明します。 この記事は、コピー アクティビティの概要を示している[コピー アクティビティの概要](copy-activity-overview.md)に関する記事に基づいています。

## <a name="sap-bw-open-hub-integration"></a>SAP BW オープン ハブの統合 

[SAP BW オープン ハブ サービス](https://wiki.scn.sap.com/wiki/display/BI/Overview+of+Open+Hub+Service)は、SAP BW からデータを抽出する効率的な方法です。 次の図は、お客様が利用する SAP システム内の典型的なフローの 1 つを示しています。この場合、データは SAP ECC、PSA、DSO、キューブと流れています。

SAP BW オープン ハブ宛先 (OHD) は、SAP データの中継先のターゲットを定義します。 SAP データ転送プロセス (DTP) によってサポートされているすべてのオブジェクトは、たとえば DSO、InfoCube、MultiProvider、DataSource などのオープン ハブ データ ソースとして使用できます。中継されたデータが保存されるオープン ハブ宛先の種類は、データベース テーブル (ローカルまたはリモート) またはフラット ファイルである場合があります。 この SAP BW オープン ハブ コネクタは、BW の OHD ローカル テーブルからのデータ コピーをサポートしています。 その他の種類を使用している場合は、その他のコネクタを使用してデータベースまたはファイル システムに直接接続できます。

![SAP BW オープン ハブ](./media/connector-sap-business-warehouse-open-hub/sap-bw-open-hub.png)

## <a name="supported-capabilities"></a>サポートされる機能

SAP Business Warehouse のデータは、オープン ハブを介して、サポートされている任意のシンク データ ストアにコピーできます。 コピー アクティビティによってソースまたはシンクとしてサポートされているデータ ストアの一覧については、[サポートされているデータ ストア](copy-activity-overview.md#supported-data-stores-and-formats)に関する記事の表をご覧ください。

具体的には、この SAP Business Warehouse オープン ハブ コネクタは以下をサポートします。

- SAP Business Warehouse **バージョン 7.30 以上 (2015 年以後にリリースされた最近の SAP サポート パッケージ スタックの場合)**。
- オープン ハブ宛先の、DSO、InfoCube、MultiProvider、DataSource などがその下にある可能性があるローカル テーブルを介したデータのコピー。
- 基本認証を使用したデータのコピー。
- アプリケーション サーバーへの接続。

## <a name="prerequisites"></a>前提条件

この SAP Business Warehouse オープン ハブ コネクタを使用するには、次の作業を行う必要があります。

- バージョン 3.13 以上のセルフホステッド統合ランタイムをセットアップする。 詳細については、[セルフホステッド統合ランタイム](create-self-hosted-integration-runtime.md)に関する記事をご覧ください。

- SAP の Web サイトから **64 ビットの [SAP .NET Connector 3.0](https://support.sap.com/en/product/connectors/msnet.html)** をダウンロードし、それをセルフホステッド IR マシンにインストールします。 インストール時には、省略可能なセットアップ手順のウィンドウで、次の図のように **[Install Assemblies to GAC] (アセンブリを GAC にインストールする)** オプションを選択したことを確認します。 

    ![SAP .NET Connector をインストールする](./media/connector-sap-business-warehouse-open-hub/install-sap-dotnet-connector.png)

- Data Factory の BW コネクタで使用する予定の SAP ユーザーは、以下のアクセス許可を持っている必要があります。 

    - RFC と SAP BW の認可。 
    - 認可オブジェクト "S_SDSAUTH" の "実行" アクティビティに対するアクセス許可。

- SAP オープン ハブ宛先の種類は、[Technical Key] (テクニカル キー) オプションをオンにして、**データベース テーブル**として作成します。  また、必須ではありませんが、[Deleting Data from Table] (テーブルからデータを削除する) はオンにしないままにすることをお勧めします。 DTP を利用して (直接実行するか、既存のプロセス チェーンに統合する)、選択したソース オブジェクト (キューブなど) のデータをオープン ハブ宛先テーブルに取り込みます。

## <a name="getting-started"></a>使用の開始

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

以下のセクションでは、SAP Business Warehouse オープン ハブ コネクタに固有の Data Factory エンティティを定義するために使用されるプロパティの詳細を説明します。

## <a name="linked-service-properties"></a>リンクされたサービスのプロパティ

SAP Business Warehouse オープン ハブのリンクされたサービスでは、以下のプロパティがサポートされています。

| プロパティ | 説明 | 必須 |
|:--- |:--- |:--- |
| type | type プロパティは、次のように設定する必要があります:**SapOpenHub** | [はい] |
| server | SAP BW インスタンスが存在するサーバーの名前。 | [はい] |
| systemNumber | SAP BW システムのシステムの数。<br/>使用できる値: 文字列として表される 2 桁の 10 進数。 | [はい] |
| clientId | SAP BW システム内のクライアントのクライアント ID。<br/>使用できる値: 文字列として表される 3 桁の 10 進数。 | [はい] |
| language | SAP システムで使用する言語。 | いいえ (既定値は **EN**)|
| userName | SAP サーバーにアクセスできるユーザーの名前。 | [はい] |
| password | ユーザーのパスワード。 このフィールドを SecureString としてマークして Data Factory に安全に保管するか、[Azure Key Vault に格納されているシークレットを参照](store-credentials-in-key-vault.md)します。 | はい |
| connectVia | データ ストアに接続するために使用される[統合ランタイム](concepts-integration-runtime.md)。 「[前提条件](#prerequisites)」に記されているように、セルフホステッド統合ランタイムが必要です。 |[はい] |

**例:**

```json
{
    "name": "SapBwOpenHubLinkedService",
    "properties": {
        "type": "SapOpenHub",
        "typeProperties": {
            "server": "<server name>",
            "systemNumber": "<system number>",
            "clientId": "<client id>",
            "userName": "<SAP user>",
            "password": {
                "type": "SecureString",
                "value": "<Password for SAP user>"
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

データセットを定義するために使用できるセクションとプロパティの完全な一覧については、[データセット](concepts-datasets-linked-services.md)に関する記事をご覧ください。 このセクションでは、Salesforce データセット でサポートされるプロパティの一覧を示します。

SAP BW オープン ハブとの間でデータをコピーするには、データセットの type プロパティを **SapOpenHubTable** に設定します。 次のプロパティがサポートされています。

| プロパティ | 説明 | 必須 |
|:--- |:--- |:--- |
| type | type プロパティは **SapOpenHubTable** に設定する必要があります。  | [はい] |
| openHubDestinationName | データのコピー元になるオープン ハブ宛先の名前。 | [はい] |
| excludeLastRequest | 最後の要求のレコードを除外するかどうか。 | いいえ (既定値は **true**)。 |
| baseRequestId | 差分読み込み要求の ID。 設定されると、requestId がこのプロパティの値**より大きい**データのみが取得されます。  | いいえ  |

>[!TIP]
>オープン ハブ テーブルには、1 つの要求 ID によって生成されたデータのみが含まれています。たとえば、常に完全な読み込みを行い、テーブル内の既存のデータを上書きする場合や、DTP はテストのために 1 回のみ実行する場合は、データを外へコピーするため、忘れずに "excludeLastRequest" オプションをオフにします。

**例:**

```json
{
    "name": "SAPBWOpenHubDataset",
    "properties": {
        "type": "SapOpenHubTable",
        "linkedServiceName": {
            "referenceName": "<SAP BW Open Hub linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "openHubDestinationName": "<open hub destination name>"
        }
    }
}
```

## <a name="copy-activity-properties"></a>コピー アクティビティのプロパティ

アクティビティの定義に利用できるセクションとプロパティの完全な一覧については、[パイプライン](concepts-pipelines-activities.md)に関する記事を参照してください。 このセクションでは、SAP BW オープン ハブ ソースでサポートされるプロパティの一覧を示します。

### <a name="sap-bw-open-hub-as-source"></a>ソースとしての SAP BW オープン ハブ

SAP BW オープン ハブからデータをコピーするには、コピー アクティビティのソースの種類を **SapOpenHubSource** に設定します。 一方、コピー アクティビティの**ソース** セクションで必要とされる、種類固有のその他のプロパティはありません。

**例:**

```json
"activities":[
    {
        "name": "CopyFromSAPBWOpenHub",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<SAP BW Open Hub input dataset name>",
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
                "type": "SapOpenHubSource"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

## <a name="data-type-mapping-for-sap-bw-open-hub"></a>SAP BW オープン ハブのデータ型マッピング

SAP BW オープン ハブからデータをコピーするときには、以下に示す、SAP BW のデータ型から Azure Data Factory の中間データ型へのマッピングが使用されます。 コピー アクティビティでソースのスキーマとデータ型がシンクにマッピングされるしくみについては、[スキーマとデータ型のマッピング](copy-activity-schema-and-type-mapping.md)に関する記事を参照してください。

| SAP ABAP の型 | Data Factory の中間データ型 |
|:--- |:--- |
| C (String) | String |
| I (integer) | Int32 |
| F (Float) | Double |
| D (Date) | String |
| T (Time) | String |
| P (BCD Packed、Currency、Decimal、Qty) | Decimal |
| N (Numc) | String |
| X (Binary および Raw) | String |

## <a name="next-steps"></a>次の手順
Azure Data Factory のコピー アクティビティによってソースおよびシンクとしてサポートされるデータ ストアの一覧については、[サポートされるデータ ストア](copy-activity-overview.md#supported-data-stores-and-formats)の表をご覧ください。
