---
title: DB2 からデータをコピーする
titleSuffix: Azure Data Factory & Azure Synapse
description: Azure Data Factory のコピー アクティビティまたは Synapse Analytics パイプラインで、DB2 からサポートしているシンク データ ストアにデータをコピーする方法を説明します。
author: jianleishen
ms.service: data-factory
ms.subservice: data-movement
ms.custom: synapse
ms.topic: conceptual
ms.date: 09/09/2021
ms.author: jianleishen
ms.openlocfilehash: 5fb5a7c83aba80d5eb58bac4437121ce37b6345a
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/02/2021
ms.locfileid: "131024396"
---
# <a name="copy-data-from-db2-using-azure-data-factory-or-synapse-analytics"></a>Azure Data Factory または Synapse Analytics で DB2 からデータをコピーする
> [!div class="op_single_selector" title1="使用している Data Factory サービスのバージョンを選択してください:"]
> * [Version 1](v1/data-factory-onprem-db2-connector.md)
> * [現在のバージョン](connector-db2.md)

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

この記事では、Azure Data Factory のコピー アクティビティと Synapse Analytics パイプラインで、DB2 データベースからデータをコピーする方法の概要を説明します。 この記事は、コピー アクティビティの概要を示している[コピー アクティビティの概要](copy-activity-overview.md)に関する記事に基づいています。

## <a name="supported-capabilities"></a>サポートされる機能

この DB2 データベース コネクタは、以下のアクティビティでサポートされています。

- [サポートされるソース/シンク マトリックス](copy-activity-overview.md)での[コピー アクティビティ](copy-activity-overview.md)
- [Lookup アクティビティ](control-flow-lookup-activity.md)

DB2 データベースのデータを、サポートされているシンク データ ストアにコピーできます。 コピー アクティビティによってソースまたはシンクとしてサポートされているデータ ストアの一覧については、[サポートされているデータ ストア](copy-activity-overview.md#supported-data-stores-and-formats)に関する記事の表をご覧ください。

具体的には、この DB2 コネクタでは、分散型リレーショナル データベース アーキテクチャ (DRDA) SQL アクセス マネージャー (SQLAM) バージョン 9、10、および 11 に対応した、次の IBM DB2 のプラットフォームとバージョンがサポートされています。  DDM/DRDA プロトコルが利用されます。

* IBM DB2 for z/OS 12.1
* IBM DB2 for z/OS 11.1
* IBM DB2 for z/OS 10.1
* IBM DB2 for i 7.3
* IBM DB2 for i 7.2
* IBM DB2 for i 7.1
* IBM DB2 for LUW 11
* IBM DB2 for LUW 10.5
* IBM DB2 for LUW 10.1

>[!TIP]
>DB2 コネクタは、Microsoft OLE DB Provider for DB2 の上に構築されています。 DB2 コネクタのエラーのトラブルシューティングを行うには、「[データ プロバイダーのエラー コード](/host-integration-server/db2oledbv/data-provider-error-codes#drda-protocol-errors)」を参照してください。

## <a name="prerequisites"></a>前提条件

[!INCLUDE [data-factory-v2-integration-runtime-requirements](includes/data-factory-v2-integration-runtime-requirements.md)]

統合ランタイムには DB2 ドライバーが組み込まれているため、DB2 からデータをコピーするときにドライバーを手動でインストールする必要はありません。

## <a name="getting-started"></a>作業の開始

[!INCLUDE [data-factory-v2-connector-get-started](includes/data-factory-v2-connector-get-started.md)]

## <a name="create-a-linked-service-to-db2-using-ui"></a>UI を使用して DB2 のリンク サービスを作成する

次の手順を使用して、Azure portal UI で DB2 のリンク サービスを作成します。

1. Azure Data Factory または Synapse ワークスペースの [管理] タブに移動し、[リンクされたサービス] を選択して、[新規] をクリックします。

    # <a name="azure-data-factory"></a>[Azure Data Factory](#tab/data-factory)

    :::image type="content" source="media/doc-common-process/new-linked-service.png" alt-text="Azure Data Factory の UI で新しいリンク サービスを作成するスクリーンショット。":::

    # <a name="azure-synapse"></a>[Azure Synapse](#tab/synapse-analytics)

    :::image type="content" source="media/doc-common-process/new-linked-service-synapse.png" alt-text="Azure Synapse の UI を使用した新しいリンク サービスの作成を示すスクリーンショット。":::

2. DB2 を検索し、DB2 コネクタを選択します。

    :::image type="content" source="media/connector-db2/db2-connector.png" alt-text="DB2 コネクタのスクリーンショット。":::    

1. サービスの詳細を構成し、接続をテストして、新しいリンク サービスを作成します。

    :::image type="content" source="media/connector-db2/configure-db2-linked-service.png" alt-text="DB2 のリンク サービスの構成のスクリーンショット。":::

## <a name="connector-configuration-details"></a>コネクタの構成の詳細

次のセクションでは、DB2 コネクタに固有の Data Factory エンティティを定義するために使用されるプロパティについて詳しく説明します。

## <a name="linked-service-properties"></a>リンクされたサービスのプロパティ

DB2 のリンクされたサービスでは、次のプロパティがサポートされます。

| プロパティ | 説明 | 必須 |
|:--- |:--- |:--- |
| type | type プロパティは、次のように設定する必要があります:**Db2** | はい |
| connectionString | DB2 インスタンスに接続するために必要な情報を指定します。<br/> パスワードを Azure Key Vault に格納して、接続文字列から `password` 構成をプルすることもできます。 詳細については、下記の例と、「[Azure Key Vault への資格情報の格納](store-credentials-in-key-vault.md)」の記事を参照してください。 | はい |
| connectVia | データ ストアに接続するために使用される[統合ランタイム](concepts-integration-runtime.md)。 詳細については、「[前提条件](#prerequisites)」セクションを参照してください。 指定されていない場合は、既定の Azure 統合ランタイムが使用されます。 |いいえ |

接続文字列内の一般的なプロパティを次に示します。

| プロパティ | 説明 | 必須 |
|:--- |:--- |:--- |
| server |DB2 サーバーの名前です。 サーバー名に続けて、コロンで区切ってポート番号を指定できます (例: `server:port`)。<br>DB2 コネクタでは、DDM/DRDA プロトコルが利用されます。指定されていない場合、既定でポート 50000 が使用されます。 特定の DB2 データベースが使用するポートは、バージョンと設定によって異なる場合があります。たとえば、DB2 LUW の場合、既定のポートは 50000 です。AS400 の場合、既定のポートは 446 または448 (TLS が有効になっている場合) です。 ポートの一般的な構成方法については、[DB2 z/OS](https://www.ibm.com/support/knowledgecenter/SSEPGG_11.5.0/com.ibm.db2.luw.qb.dbconn.doc/doc/t0008229.html)、[DB2 iSeries](https://www.ibm.com/support/knowledgecenter/ssw_ibm_i_74/ddp/rbal1ports.htm)、[DB2 LUW](https://www.ibm.com/support/knowledgecenter/en/SSEKCU_1.1.3.0/com.ibm.psc.doc/install/psc_t_install_typical_db2_port.html) の各ドキュメントをご覧ください。 |はい |
| database |DB2 データベースの名前です。 |はい |
| authenticationType |DB2 データベースへの接続に使用される認証の種類です。<br/>使用可能な値:**Basic**。 |はい |
| username |DB2 データベースに接続するユーザー名を指定します。 |はい |
| password |ユーザー名に指定したユーザー アカウントのパスワードを指定します。 このフィールドを SecureString とマークして安全に保存するか、[Azure Key Vault に保存されているシークレットを参照](store-credentials-in-key-vault.md)します。 |はい |
| packageCollection    | データベースに対するクエリを実行したときに、サービスによって必要なパッケージを自動作成する場所を指定します。 これを設定しない場合、サービスでは {username} を規定値として使用します。 | いいえ |
| certificateCommonName | Secure Sockets Layer (SSL) またはトランスポート層セキュリティ (TLS) 暗号化を使用する場合は、証明書共通名の値を入力する必要があります。 | いいえ |

> [!TIP]
> `The package corresponding to an SQL statement execution request was not found. SQLSTATE=51002 SQLCODE=-805` というエラー メッセージが表示された場合は、そのユーザーに必要なパッケージが作成されていません。 既定では、サービスはDB2への接続に使用したユーザー名のコレクションの下にパッケージを作成します。 パッケージ コレクションのプロパティを指定することで、データベースに対するクエリを実行したときに、サービスによって必要なパッケージをどこに作成するかを指定します。

**例:**

```json
{
    "name": "Db2LinkedService",
    "properties": {
        "type": "Db2",
        "typeProperties": {
            "connectionString": "server=<server:port>;database=<database>;authenticationType=Basic;username=<username>;password=<password>;packageCollection=<packagecollection>;certificateCommonName=<certname>;"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```
**例: パスワードを Azure Key Vault に格納する**

```json
{
    "name": "Db2LinkedService",
    "properties": {
        "type": "Db2",
        "typeProperties": {
            "connectionString": "server=<server:port>;database=<database>;authenticationType=Basic;username=<username>;packageCollection=<packagecollection>;certificateCommonName=<certname>;",
            "password": { 
                "type": "AzureKeyVaultSecret", 
                "store": { 
                    "referenceName": "<Azure Key Vault linked service name>", 
                    "type": "LinkedServiceReference" 
                }, 
                "secretName": "<secretName>" 
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

以下のペイロードで DB2 のリンクされたサービスを使用していた場合、引き続きそのままサポートされますが、今後は新しいものを使用することをお勧めします。

**以前のペイロード:**

```json
{
    "name": "Db2LinkedService",
    "properties": {
        "type": "Db2",
        "typeProperties": {
            "server": "<servername:port>",
            "database": "<dbname>",
            "authenticationType": "Basic",
            "username": "<username>",
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

## <a name="dataset-properties"></a>データセットのプロパティ

データセットを定義するために使用できるセクションとプロパティの完全な一覧については、[データセット](concepts-datasets-linked-services.md)に関する記事をご覧ください。 このセクションでは、DB2 データセット でサポートされるプロパティの一覧を示します。

DB2 からのデータ コピーについては、次のプロパティがサポートされています。

| プロパティ | 説明 | 必須 |
|:--- |:--- |:--- |
| type | データセットの type プロパティは、次のように設定する必要があります:**Db2Table** | はい |
| schema | スキーマの名前。 |いいえ (アクティビティ ソースの "query" が指定されている場合)  |
| table | テーブルの名前。 |いいえ (アクティビティ ソースの "query" が指定されている場合)  |
| tableName | スキーマがあるテーブルの名前。 このプロパティは下位互換性のためにサポートされています。 新しいワークロードでは、`schema` と `table` を使用します。 | いいえ (アクティビティ ソースの "query" が指定されている場合) |

**例**

```json
{
    "name": "DB2Dataset",
    "properties":
    {
        "type": "Db2Table",
        "typeProperties": {},
        "schema": [],
        "linkedServiceName": {
            "referenceName": "<DB2 linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

`RelationalTable` 型のデータセットを使用していた場合、現状のまま引き続きサポートされますが、今後は新しいものを使用することをお勧めします。

## <a name="copy-activity-properties"></a>コピー アクティビティのプロパティ

アクティビティの定義に利用できるセクションとプロパティの完全な一覧については、[パイプライン](concepts-pipelines-activities.md)に関する記事を参照してください。 このセクションでは、DB2 ソースでサポートされるプロパティの一覧を示します。

### <a name="db2-as-source"></a>ソースとしての DB2

DB2 からデータをコピーするために、コピー アクティビティの **source** セクションでは次のプロパティがサポートされています。

| プロパティ | 説明 | 必須 |
|:--- |:--- |:--- |
| type | コピー アクティビティのソースの type プロパティは、次のように設定する必要があります:**Db2Source** | はい |
| query | カスタム SQL クエリを使用してデータを読み取ります。 (例: `"query": "SELECT * FROM \"DB2ADMIN\".\"Customers\""`)。 | いいえ (データセットの "tableName" が指定されている場合) |

**例:**

```json
"activities":[
    {
        "name": "CopyFromDB2",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<DB2 input dataset name>",
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
                "type": "Db2Source",
                "query": "SELECT * FROM \"DB2ADMIN\".\"Customers\""
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

`RelationalSource` 型のソースを使用していた場合は現状のまま引き続きサポートされますが、今後は新しいものを使用することをお勧めします。

## <a name="data-type-mapping-for-db2"></a>DB2 のデータ型のマッピング

DB2 からデータをコピーするときは、DB2 のデータ型からサービスでの内的処理に使用する暫定データ型への、次のマッピングを使用します。 コピー アクティビティでソースのスキーマとデータ型がシンクにマッピングされるしくみについては、[スキーマとデータ型のマッピング](copy-activity-schema-and-type-mapping.md)に関する記事を参照してください。

| DB2 データベース型 | 中間サービス データ型 |
|:--- |:--- |
| BigInt |Int64 |
| Binary |Byte[] |
| BLOB |Byte[] |
| Char |String |
| Clob |String |
| Date |Datetime |
| DB2DynArray |String |
| DbClob |String |
| Decimal |Decimal |
| DecimalFloat |Decimal |
| Double |Double |
| Float |Double |
| Graphic |String |
| Integer |Int32 |
| LongVarBinary |Byte[] |
| LongVarChar |String |
| LongVarGraphic |String |
| 数値 |Decimal |
| Real |Single |
| SmallInt |Int16 |
| Time |TimeSpan |
| Timestamp |DateTime |
| VarBinary |Byte[] |
| VarChar |String |
| VarGraphic |String |
| xml |Byte[] |

## <a name="lookup-activity-properties"></a>Lookup アクティビティのプロパティ

プロパティの詳細については、[Lookup アクティビティ](control-flow-lookup-activity.md)に関するページを参照してください。

## <a name="next-steps"></a>次のステップ
Copy アクティビティでソースおよびシンクとしてサポートされるデータ ストアの一覧については、[サポートされるデータ ストア](copy-activity-overview.md#supported-data-stores-and-formats)に関するセクションを参照してください。