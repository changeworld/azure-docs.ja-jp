---
title: Azure Data Explorer のデータのコピーと変換を行う
titleSuffix: Azure Data Factory & Azure Synapse
description: Data Factory または Azure Synapse Analytics を使用して、Azure Data Explorer のデータのコピーと変換を行う方法について説明します。
ms.author: orspodek
author: jianleishen
ms.service: data-factory
ms.subservice: data-movement
ms.topic: conceptual
ms.custom: synapse
ms.date: 10/14/2021
ms.openlocfilehash: 8aafbbd0cc7063fdccf3ae44e9442fa86cfe5f94
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/02/2021
ms.locfileid: "131005767"
---
# <a name="copy-data-to-or-from-azure-data-explorer-using-azure-data-factory-or-synapse-analytics"></a>Azure Data Factory または Synapse Analytics を使用して Azure Data Explorer をコピー先またはコピー元としてデータをコピーする

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

この記事では、Azure Data Factory または Synapse Analytics パイプラインでコピー アクティビティを使用して、[Azure Data Explorer](/azure/data-explorer/data-explorer-overview) をコピー先またはコピー元としてデータをコピーする方法について説明します。 この記事は、コピー アクティビティの概要を示している[コピー アクティビティの概要](copy-activity-overview.md)に関する記事に基づいています。

>[!TIP]
>Azure Data Explorer のサービスとの統合の詳細については、「[Azure Data Explorer の統合](/azure/data-explorer/data-factory-integration)」を参照してください。

## <a name="supported-capabilities"></a>サポートされる機能

この Azure Data Explorer コネクタは、次のアクティビティでサポートされます。

- [サポートされるソース/シンク マトリックス](copy-activity-overview.md)での[コピー アクティビティ](copy-activity-overview.md)
- [Lookup アクティビティ](control-flow-lookup-activity.md)

Azure Data Explorer には、サポートされているソース データ ストアからデータをコピーすることができます。 また、Azure Data Explorer のデータを、サポートされているシンク データ ストアにコピーできます。 コピー アクティビティによってソースまたはシンクとしてサポートされているデータ ストアの一覧については、[サポートされているデータ ストア](copy-activity-overview.md#supported-data-stores-and-formats)に関するページの表をご覧ください。

>[!NOTE]
>セルフホステッド統合ランタイムを使用して、Azure Data Explorer との間でオンプレミス データ ストアから双方向にデータをコピーすることは、バージョン 3.14 以降からサポートされています。

Azure Data Explorer コネクタを使用すると、次のことができます。

* Azure Active Directory (Azure AD) アプリケーション トークン認証と **サービス プリンシパル** を使用して、データをコピーする。
* ソースとして、KQL (Kusto) クエリを使用してデータを取得する。
* シンクとして、コピー先テーブルにデータを追加する。

## <a name="getting-started"></a>作業の開始

>[!TIP]
>Azure Data Explorer コネクタのチュートリアルについては、「[Azure Data Explorer との間でデータをコピーする](/azure/data-explorer/data-factory-load-data)」と、[データベースから Azure Data Explorer への一括コピー](/azure/data-explorer/data-factory-template)に関するページを参照してください。

[!INCLUDE [data-factory-v2-connector-get-started](includes/data-factory-v2-connector-get-started.md)]

## <a name="create-a-linked-service-to-azure-data-explorer-using-ui"></a>UI を使用して Azure Data Explorer のリンク サービスを作成する

次の手順を使用して、Azure portal UI で Azure Data Explorer のリンク サービスを作成します。

1. Azure Data Factory または Synapse ワークスペースの [管理] タブに移動し、[リンク サービス] を選択して、[新規] をクリックします。

    # <a name="azure-data-factory"></a>[Azure Data Factory](#tab/data-factory)

    :::image type="content" source="media/doc-common-process/new-linked-service.png" alt-text="Azure Data Factory の UI で新しいリンク サービスを作成するスクリーンショット。":::

    # <a name="azure-synapse"></a>[Azure Synapse](#tab/synapse-analytics)

    :::image type="content" source="media/doc-common-process/new-linked-service-synapse.png" alt-text="Azure Synapse の UI を使用した新しいリンク サービスの作成を示すスクリーンショット。":::

2. Explorer を検索し、Azure Data Explorer (Kusto) コネクタを選択します。

    :::image type="content" source="media/connector-azure-data-explorer/azure-data-explorer-connector.png" alt-text="Azure Data Explorer (Kusto) コネクタのスクリーンショット。":::    

1. サービスの詳細を構成し、接続をテストして、新しいリンク サービスを作成します。

    :::image type="content" source="media/connector-azure-data-explorer/configure-azure-data-explorer-linked-service.png" alt-text="Azure Data Explorer のリンク サービスの構成のスクリーンショット。":::

## <a name="connector-configuration-details"></a>コネクタの構成の詳細

以下のセクションでは、Azure Data Explorer コネクタに固有のエンティティを定義するために使用されるプロパティの詳細を説明します。

## <a name="linked-service-properties"></a>リンクされたサービスのプロパティ

Azure Data Explorer コネクタでは、次の認証の種類がサポートされています。 詳細については、対応するセクションをご覧ください。

- [サービス プリンシパルの認証](#service-principal-authentication)
- [システム割り当てマネージド ID 認証](#managed-identity)
- [ユーザー割り当てマネージド ID 認証](#user-assigned-managed-identity-authentication)

### <a name="service-principal-authentication"></a>サービス プリンシパルの認証

サービス プリンシパルの認証を使用するには、次の手順に従ってサービス プリンシパルを取得し、アクセス許可を付与します。

1. 「[アプリケーションを Azure AD テナントに登録する](../storage/common/storage-auth-aad-app.md#register-your-application-with-an-azure-ad-tenant)」の手順に従って、Azure Active Directory にアプリケーション エンティティを登録します。 次の値を記録しておきます。リンクされたサービスを定義するときに使います。

    - アプリケーション ID
    - アプリケーション キー
    - テナント ID

2. Azure Data Explorer でサービス プリンシパルに適切なアクセス許可を付与します。 ロールおよびアクセス許可の詳細について、またアクセス許可の管理方法の詳細については、「[Azure Data Explorer のデータベース アクセス許可を管理する](/azure/data-explorer/manage-database-permissions)」を参照してください。 一般的に、次のことを行う必要があります。

    - **ソースとして**、少なくとも **データベース ビューアー** ロールをデータベースに付与します。
    - **シンクとして**、少なくとも **データベースのデータ取り込み** ロールをデータベースに付与します。

>[!NOTE]
>UI を使用して作成する場合、既定では、Azure Data Explorer クラスター、データベース、およびテーブルを一覧表示するために、ログイン ユーザー アカウントが使用されます。 サービス プリンシパルを使用してオブジェクトを一覧表示するには、更新ボタンの横にあるドロップダウンをクリックします。これらの操作に対するアクセス許可がない場合は、名前を手動で入力します。

Azure Data Explorer のリンクされたサービスでは、次のプロパティがサポートされます。

| プロパティ | 説明 | 必須 |
|:--- |:--- |:--- |
| type | **type** プロパティは、**AzureDataExplorer** に設定する必要があります。 | はい |
| endpoint | Azure Data Explorer クラスターのエンドポイント URL。形式は `https://<clusterName>.<regionName>.kusto.windows.net` です。 | はい |
| database | データベースの名前。 | はい |
| tenant | アプリケーションが存在するテナントの情報 (ドメイン名またはテナント ID) を指定します。 これは、[Kusto 接続文字列](/azure/kusto/api/connection-strings/kusto#application-authentication-properties)の "機関 ID" として知られています。 これは、Azure portal の右上隅にマウス ポインターを合わせることで取得できます。 | はい |
| servicePrincipalId | アプリケーションのクライアント ID を取得します。 これは、[Kusto 接続文字列](/azure/kusto/api/connection-strings/kusto#application-authentication-properties)の "AAD アプリケーション クライアント ID" として知られています。 | はい |
| servicePrincipalKey | アプリケーションのキーを取得します。 これは、[Kusto 接続文字列](/azure/kusto/api/connection-strings/kusto#application-authentication-properties)の "AAD アプリケーション キー" として知られています。 このフィールドを **SecureString** とマークして安全に保存するか、[Azure Key Vault に格納されている安全なデータを参照](store-credentials-in-key-vault.md)します。 | はい |
| connectVia | データ ストアに接続するために使用される[統合ランタイム](concepts-integration-runtime.md)。 データ ストアがプライベート ネットワーク内にある場合、Azure Integration Runtime またはセルフホステッド統合ランタイムを使用できます。 指定されていない場合は、既定の Azure Integration Runtime が使用されます。 |いいえ |

**例: サービス プリンシパル キー認証の使用**

```json
{
    "name": "AzureDataExplorerLinkedService",
    "properties": {
        "type": "AzureDataExplorer",
        "typeProperties": {
            "endpoint": "https://<clusterName>.<regionName>.kusto.windows.net ",
            "database": "<database name>",
            "tenant": "<tenant name/id e.g. microsoft.onmicrosoft.com>",
            "servicePrincipalId": "<service principal id>",
            "servicePrincipalKey": {
                "type": "SecureString",
                "value": "<service principal key>"
            }
        }
    }
}
```

### <a name="system-assigned-managed-identity-authentication"></a><a name="managed-identity"></a> システム割り当てマネージド ID 認証

Azure リソース用マネージド ID の詳細については、[Azure リソース用マネージド ID](../active-directory/managed-identities-azure-resources/overview.md) に関するページを参照してください。

システム割り当てマネージド ID 認証を使用するには、次の手順に従ってアクセス許可を付与します。

1. ファクトリまたは Synapse ワークスペースと共に生成された **マネージド ID オブジェクト ID** の値をコピーして、[マネージド ID 情報を取得します](data-factory-service-identity.md#retrieve-managed-identity)。

2. Azure Data Explorer でマネージド ID に適切なアクセス許可を付与します。 ロールおよびアクセス許可の詳細について、またアクセス許可の管理方法の詳細については、「[Azure Data Explorer のデータベース アクセス許可を管理する](/azure/data-explorer/manage-database-permissions)」を参照してください。 一般的に、次のことを行う必要があります。

    - **ソースとして**、少なくとも **データベース ビューアー** ロールをデータベースに付与します。
    - **シンクとして**、少なくとも **データベースのデータ取り込み** ロールをデータベースに付与します。

>[!NOTE]
>UI を使用して作成する場合、Azure Data Explorer クラスター、データベース、およびテーブルを一覧表示するために、ログイン ユーザー アカウントが使用されます。 これらの操作のためのアクセス許可がない場合は、名前を手動で入力します。

Azure Data Explorer のリンクされたサービスでは、次のプロパティがサポートされます。

| プロパティ | 説明 | 必須 |
|:--- |:--- |:--- |
| type | **type** プロパティは、**AzureDataExplorer** に設定する必要があります。 | はい |
| endpoint | Azure Data Explorer クラスターのエンドポイント URL。形式は `https://<clusterName>.<regionName>.kusto.windows.net` です。 | はい |
| database | データベースの名前。 | はい |
| connectVia | データ ストアに接続するために使用される[統合ランタイム](concepts-integration-runtime.md)。 データ ストアがプライベート ネットワーク内にある場合、Azure Integration Runtime またはセルフホステッド統合ランタイムを使用できます。 指定されていない場合は、既定の Azure Integration Runtime が使用されます。 |いいえ |

**例: システム割り当てマネージド ID 認証を使用する**

```json
{
    "name": "AzureDataExplorerLinkedService",
    "properties": {
        "type": "AzureDataExplorer",
        "typeProperties": {
            "endpoint": "https://<clusterName>.<regionName>.kusto.windows.net ",
            "database": "<database name>",
        }
    }
}
```

### <a name="user-assigned-managed-identity-authentication"></a>ユーザー割り当てマネージド ID 認証
Azure リソース用マネージド ID の詳細については、[Azure リソース用マネージド ID](../active-directory/managed-identities-azure-resources/overview.md) に関するページを参照してください

ユーザー割り当てマネージド ID 認証を使用するには、次の手順に従います。

1. [1 つ以上のユーザー割り当てマネージド ID を作成](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md)して、Azure Data Explorer でアクセス許可を付与します。 ロールおよびアクセス許可の詳細について、またアクセス許可の管理方法の詳細については、「[Azure Data Explorer のデータベース アクセス許可を管理する](/azure/data-explorer/manage-database-permissions)」を参照してください。 一般的に、次のことを行う必要があります。

    - **ソースとして**、少なくとも **データベース ビューアー** ロールをデータベースに付与します。
    - **シンクとして**、少なくとも **データベースのデータ取り込み** ロールをデータベースに付与します。
     
2. 1 つ以上のユーザー割り当てマネージド ID をデータ ファクトリまたは Synapse ワークスペースに割り当て、ユーザー割り当てマネージド ID ごとに[資格情報を作成](credentials.md)します。

Azure Data Explorer のリンクされたサービスでは、次のプロパティがサポートされます。

| プロパティ | 説明 | 必須 |
|:--- |:--- |:--- |
| type | **type** プロパティは、**AzureDataExplorer** に設定する必要があります。 | はい |
| endpoint | Azure Data Explorer クラスターのエンドポイント URL。形式は `https://<clusterName>.<regionName>.kusto.windows.net` です。 | はい |
| database | データベースの名前。 | はい |
| 資格情報 | ユーザー割り当てマネージド ID を資格情報オブジェクトとして指定します。 | はい |
| connectVia | データ ストアに接続するために使用される[統合ランタイム](concepts-integration-runtime.md)。 データ ストアがプライベート ネットワーク内にある場合、Azure Integration Runtime またはセルフホステッド統合ランタイムを使用できます。 指定されていない場合は、既定の Azure Integration Runtime が使用されます。 |いいえ |

**例: ユーザー割り当てマネージド ID 認証を使用する**
```json
{
    "name": "AzureDataExplorerLinkedService",
    "properties": {
        "type": "AzureDataExplorer",
        "typeProperties": {
            "endpoint": "https://<clusterName>.<regionName>.kusto.windows.net ",
            "database": "<database name>",
            "credential": {
                "referenceName": "credential1",
                "type": "CredentialReference"
            }
        }
    }
}
```

## <a name="dataset-properties"></a>データセットのプロパティ

データセットの定義に使用できるセクションとプロパティの一覧については、[データセット](concepts-datasets-linked-services.md)に関する記事をご覧ください。 このセクションでは、Azure Data Explorer データセットでサポートされるプロパティの一覧を示します。

Azure Data Explorer にデータをコピーするには、データセットの type プロパティを **AzureDataExplorerTable** に設定します。

次のプロパティがサポートされています。

| プロパティ | 説明 | 必須 |
|:--- |:--- |:--- |
| type | **type** プロパティは、**AzureDataExplorerTable** に設定する必要があります。 | はい |
| table | リンクされたサービスが参照するテーブルの名前。 | シンクの場合は Yes、ソースの場合は No |

**データセットのプロパティの例:**

```json
{
   "name": "AzureDataExplorerDataset",
    "properties": {
        "type": "AzureDataExplorerTable",
        "typeProperties": {
            "table": "<table name>"
        },
        "schema": [],
        "linkedServiceName": {
            "referenceName": "<Azure Data Explorer linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

## <a name="copy-activity-properties"></a>コピー アクティビティのプロパティ

アクティビティの定義に利用できるセクションとプロパティの完全な一覧については、[パイプラインとアクティビティ](concepts-pipelines-activities.md)に関するページを参照してください。 このセクションでは、Azure Data Explorer のソースとシンクでサポートされるプロパティの一覧を示します。

### <a name="azure-data-explorer-as-source"></a>ソースとしての Azure Data Explorer

Azure Data Explorer からデータをコピーするには、コピー アクティビティ ソースの **type** プロパティを **AzureDataExplorerSource** に設定します。 コピー アクティビティの **source** セクションでは、次のプロパティがサポートされます。

| プロパティ | 説明 | 必須 |
|:--- |:--- |:--- |
| type | コピー アクティビティのソースの **type** プロパティを、次の値に設定する必要があります:**AzureDataExplorerSource** | はい |
| query | [KQL 形式](/azure/kusto/query/)で指定された読み取り専用要求。 参照としてカスタム KQL クエリを使用します。 | はい |
| queryTimeout | クエリ要求がタイムアウトするまでの待機時間。既定値は 10 分 (00:10:00)、許容される最大値は 1 時間 (01:00:00) です。 | いいえ |
| noTruncation | 返される結果セットを切り詰めるかどうかを示します。 既定では、結果は 500,000 件のレコードまたは 64 メガバイト (MB) の後に切り詰められます。 アクティビティの正常な動作のためには、切り詰めを強くお勧めします。 |いいえ |

>[!NOTE]
>既定では、Azure Data Explorer ソースには、500,000 レコードまたは 64 MB のサイズ制限があります。 切り捨てることなくすべてのレコードを取得するには、クエリの先頭に `set notruncation;` を指定します。 詳細については、「[クエリの制限](/azure/kusto/concepts/querylimits)」を参照してください。

**例:**

```json
"activities":[
    {
        "name": "CopyFromAzureDataExplorer",
        "type": "Copy",
        "typeProperties": {
            "source": {
                "type": "AzureDataExplorerSource",
                "query": "TestTable1 | take 10",
                "queryTimeout": "00:10:00"
            },
            "sink": {
                "type": "<sink type>"
            }
        },
        "inputs": [
            {
                "referenceName": "<Azure Data Explorer input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<output dataset name>",
                "type": "DatasetReference"
            }
        ]
    }
]
```

### <a name="azure-data-explorer-as-sink"></a>シンクとしての Azure Data Explorer

Azure Data Explorer にデータをコピーするには、コピー アクティビティ シンクの type プロパティを **AzureDataExplorerSink** に設定します。 コピー アクティビティの **sink** セクションでは、次のプロパティがサポートされます。

| プロパティ | 説明 | 必須 |
|:--- |:--- |:--- |
| type | コピー アクティビティのシンクの **type** プロパティは、次のように設定する必要があります:**AzureDataExplorerSink** | はい |
| ingestionMappingName | Kusto テーブルで事前作成済みの[マッピング](/azure/kusto/management/mappings#csv-mapping)の名前。 ソースから Azure Data Explorer に列をマッピングするには (CSV/JSON/Avro 形式など、[サポートされているすべてのソース ストアや形式](copy-activity-overview.md#supported-data-stores-and-formats)に適用されます)、コピー アクティビティの[列マッピング](copy-activity-schema-and-type-mapping.md) (名前で暗黙的に、または構成で明示的に) や Azure Data Explorer のマッピングを使用できます。 | いいえ |
| additionalProperties | まだ Azure Data Explorer シンクによって設定されていないインジェストのプロパティを指定するために使用できるプロパティ バッグ。 具体的には、インジェスト タグの指定に便利です。 詳細については [Azure Data Explore データ インジェスト ドキュメント](/azure/data-explorer/ingestion-properties)でご覧ください。 | いいえ |

**例:**

```json
"activities":[
    {
        "name": "CopyToAzureDataExplorer",
        "type": "Copy",
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "AzureDataExplorerSink",
                "ingestionMappingName": "<optional Azure Data Explorer mapping name>",
                "additionalProperties": {<additional settings for data ingestion>}
            }
        },
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Azure Data Explorer output dataset name>",
                "type": "DatasetReference"
            }
        ]
    }
]
```

## <a name="mapping-data-flow-properties"></a>Mapping Data Flow のプロパティ

マッピング データ フローでデータを変換するときに、Azure Data Explorer のテーブルに対する読み取りと書き込みを実行できます。 詳細については、マッピング データ フローの[ソース変換](data-flow-source.md)と[シンク変換](data-flow-sink.md)に関する記事をご覧ください。 ソースとシンクの種類として、Azure Data Explorer データセットまたは[インライン データセット](data-flow-source.md#inline-datasets)を使用できます。

### <a name="source-transformation"></a>ソース変換

次の表に、Azure Data Explorer ソースでサポートされるプロパティの一覧を示します。 これらのプロパティは、 **[ソース オプション]** タブで編集できます。

| 名前 | 説明 | 必須 | 使用できる値 | データ フロー スクリプトのプロパティ |
| ---- | ----------- | -------- | -------------- | ---------------- |
| テーブル | 入力として [テーブル] を選択した場合、データ フローでは、Azure Data Explorer データセットで指定された、またはインライン データセットを使用するときにソース オプションで指定されたテーブルからすべてのデータがフェッチされます。 | いいえ | String | *(インライン データセットのみ)*<br>tableName |
| クエリ | [KQL 形式](/azure/data-explorer/kusto/query/)で指定された読み取り専用要求。 参照としてカスタム KQL クエリを使用します。  | いいえ | String | query |
| タイムアウト | クエリ要求がタイムアウトするまでの待機時間。既定値は '172000' (2 日間) です。  | いいえ | Integer | timeout |

#### <a name="azure-data-explorer-source-script-examples"></a>Azure Data Explorer ソース スクリプトの例

ソースの種類として Azure Data Explorer データセットを使用する場合、関連付けられるデータ フロー スクリプトは次のようになります。

```
source(allowSchemaDrift: true,
    validateSchema: false,
    query: 'table | take 10',
    format: 'query') ~> AzureDataExplorerSource

```

インライン データセットを使用する場合、関連付けられているデータ フロー スクリプトは次のようになります。

```
source(allowSchemaDrift: true,
    validateSchema: false,
    format: 'query',
    query: 'table | take 10',
    store: 'azuredataexplorer') ~> AzureDataExplorerSource

```

### <a name="sink-transformation"></a>シンク変換

次の表に、Azure Data Explorer シンクでサポートされるプロパティの一覧を示します。 これらのプロパティは、 **[設定]** タブで編集できます。インライン データセットを使用する場合、「[データセットのプロパティ](#dataset-properties)」セクションで説明されているプロパティと同じ追加の設定が表示されます。 

| 名前 | 説明 | 必須 | 使用できる値 | データ フロー スクリプトのプロパティ |
| ---- | ----------- | -------- | -------------- | ---------------- |
| テーブル アクション | 書き込み前に変換先テーブルのすべての行を再作成するか削除するかを指定します。<br>- **なし**: テーブルに対してアクションは実行されません。<br>- **Recreate**:テーブルが削除され、再作成されます。 新しいテーブルを動的に作成する場合に必要です。<br>- **Truncate**:ターゲット テーブルのすべての行が削除されます。 | いいえ | `true` または `false` | recreate<br/>truncate |
| 事前および事後の SQL スクリプト | データがシンク データベースに書き込まれる前 (前処理) と書き込まれた後 (後処理) に実行される複数の [Kusto 管理コマンド](/azure/data-explorer/kusto/query/#control-commands)を指定します。 | いいえ | String | preSQL; postSQL |
| タイムアウト | クエリ要求がタイムアウトするまでの待機時間。既定値は '172000' (2 日間) です。 | いいえ | Integer | timeout |


#### <a name="azure-data-explorer-sink-script-examples"></a>Azure Data Explorer シンク スクリプトの例

シンクの種類として Azure Data Explorer データセットを使用する場合、関連付けられているデータ フロー スクリプトは次のようになります。

```
IncomingStream sink(allowSchemaDrift: true,
    validateSchema: false,
    format: 'table',
    preSQLs:['pre SQL scripts'],
    postSQLs:['post SQL script'],
    skipDuplicateMapInputs: true,
    skipDuplicateMapOutputs: true) ~> AzureDataExplorerSink

```

インライン データセットを使用する場合、関連付けられているデータ フロー スクリプトは次のようになります。

```
IncomingStream sink(allowSchemaDrift: true,
    validateSchema: false,
    format: 'table',
    store: 'azuredataexplorer',
    skipDuplicateMapInputs: true,
    skipDuplicateMapOutputs: true) ~> AzureDataExplorerSink

```

## <a name="lookup-activity-properties"></a>Lookup アクティビティのプロパティ

プロパティの詳細については、[ルックアップ アクティビティ](control-flow-lookup-activity.md)に関する記事を参照してください。

## <a name="next-steps"></a>次のステップ

* コピー アクティビティでソースおよびシンクとしてサポートされるデータ ストアの一覧については、[サポートされるデータ ストア](copy-activity-overview.md#supported-data-stores-and-formats)に関するページを参照してください。

* [Azure Data Factory と Synapse Analytics から Azure Data Explorer にデータをコピーする](/azure/data-explorer/data-factory-load-data)方法を参照してください。