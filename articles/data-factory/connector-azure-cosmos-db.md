---
title: Azure Cosmos DB のデータをコピーおよび変換する (SQL API)
titleSuffix: Azure Data Factory & Azure Synapse
description: Azure Data Factory および Azure Synapse Analytics を使用して、Azure Cosmos DB (SQL API) との間でデータをコピーし、Azure Cosmos DB (SQL API) のデータを変換する方法について説明します。
ms.author: jianleishen
author: jianleishen
ms.service: data-factory
ms.subservice: data-movement
ms.topic: conceptual
ms.custom: synapse
ms.date: 09/09/2021
ms.openlocfilehash: 836ad2692dffbb6c4977f5757c6f305c6fd66d40
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/13/2021
ms.locfileid: "124761879"
---
# <a name="copy-and-transform-data-in-azure-cosmos-db-sql-api-by-using-azure-data-factory"></a>Azure Data Factory を使用して Azure Cosmos DB (SQL API) のデータをコピーおよび変換する

> [!div class="op_single_selector" title1="使用している Data Factory サービスのバージョンを選択してください:"]
> * [Version 1](v1/data-factory-azure-documentdb-connector.md)
> * [現在のバージョン](connector-azure-cosmos-db.md)

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

この記事では、Azure Data Factory のコピー アクティビティを使用して、Azure Cosmos DB (SQL API) との間でデータをコピーする方法、および Data Flow を使用して Azure Cosmos DB (SQL API) のデータを変換する方法について説明します。 詳細については、[Azure Data Factory](introduction.md) および [Azure Synapse Analytics](../synapse-analytics/overview-what-is.md) の概要記事を参照してください。

>[!NOTE]
>このコネクタでは、Cosmos DB SQL API のみがサポートされます。 MongoDB API については、[Azure Cosmos DB の MongoDB 用の API コネクタ](connector-azure-cosmos-db-mongodb-api.md)に関する記事を参照してください。 現在、他の種類の API はサポートされていません。

## <a name="supported-capabilities"></a>サポートされる機能

この Azure Cosmos DB (SQL API) コネクタは、次のアクティビティでサポートされます。

- [サポートされるソース/シンク マトリックス](copy-activity-overview.md)での[コピー アクティビティ](copy-activity-overview.md)
- [マッピング データ フロー](concepts-data-flow-overview.md)
- [Lookup アクティビティ](control-flow-lookup-activity.md)

コピー アクティビティの場合、この Azure Cosmos DB (SQL API) コネクタは次のことをサポートします。

- Azure リソース認証用のキー、サービス プリンシパル、マネージド ID を使用して、Azure Cosmos DB [SQL API](../cosmos-db/introduction.md) との間でデータをコピーします。
- **挿入** または **upsert** として Azure Cosmos DB に書き込みます。
- JSON ドキュメントをインポートおよびエクスポートしたり、表形式データセットに、または表形式データセットからデータをコピーしたりします。 例としては、SQL データベースや CSV ファイルなどがあります。 JSON ファイルまたは他の Azure Cosmos DB コレクションをコピー先またはコピー元としてドキュメントをそのままコピーするには、「[JSON ドキュメントのインポートとエクスポート](#import-and-export-json-documents)」を参照してください。

Data Factory および Synapse パイプラインは、Azure Cosmos DB に書き込むときに最適なパフォーマンスを提供できるように、[Azure Cosmos DB バルク エグゼキューター ライブラリ](https://github.com/Azure/azure-cosmosdb-bulkexecutor-dotnet-getting-started)と統合されます。

> [!TIP]
> [データ移行に関するビデオ](https://youtu.be/5-SRNiC_qOU)では、Azure Blob Storage から Azure Cosmos DB にデータをコピーする手順について説明されています。 また、Azure Cosmos DB にデータを取り込むときのパフォーマンスのチューニングに関する一般的な考慮事項も説明されています。

## <a name="get-started"></a>はじめに

[!INCLUDE [data-factory-v2-connector-get-started](includes/data-factory-v2-connector-get-started.md)]
## <a name="create-a-linked-service-to-azure-cosmos-db-using-ui"></a>UI を使用して Azure Cosmos DB のリンク サービスを作成する

次の手順を使用して、Azure portal UI で Azure Cosmos DB のリンク サービスを作成します。

1. Azure Data Factory または Synapse ワークスペースの [管理] タブに移動し、[リンクされたサービス] を選択して、[新規] をクリックします。

    # <a name="azure-data-factory"></a>[Azure Data Factory](#tab/data-factory)

    :::image type="content" source="media/doc-common-process/new-linked-service.png" alt-text="Azure Data Factory の UI で新しいリンク サービスを作成するスクリーンショット。":::

    # <a name="azure-synapse"></a>[Azure Synapse](#tab/synapse-analytics)

    :::image type="content" source="media/doc-common-process/new-linked-service-synapse.png" alt-text="Azure Synapse の UI を使用した新しいリンク サービスの作成を示すスクリーンショット。":::

2. Cosmos を検索し、Azure Cosmos DB (SQL API) コネクタを選択します。

    :::image type="content" source="media/connector-azure-cosmos-db/azure-cosmos-db-connector.png" alt-text="Azure Cosmos DB (SQL API) コネクタを選択します。":::    

1. サービスの詳細を構成し、接続をテストして、新しいリンク サービスを作成します。

    :::image type="content" source="media/connector-azure-cosmos-db/configure-azure-cosmos-db-linked-service.png" alt-text="Azure Cosmos DB のリンク サービスの構成のスクリーンショット。":::

## <a name="connector-configuration-details"></a>コネクタの構成の詳細


以下のセクションでは、Azure Cosmos DB (SQL API) に固有のエンティティの定義に使用できるプロパティについて詳しく説明します。

## <a name="linked-service-properties"></a>リンクされたサービスのプロパティ

Azure Cosmos DB (SQL API) コネクタでは、次の認証の種類がサポートされています。 詳細については、対応するセクションをご覧ください。

- [キー認証](#key-authentication)
- [サービス プリンシパルの認証](#service-principal-authentication)
- [Azure リソースのマネージド ID 認証](#managed-identity)

### <a name="key-authentication"></a>キー認証

| プロパティ | 説明 | 必須 |
|:--- |:--- |:--- |
| type | **type** プロパティは **CosmosDb** に設定する必要があります。 | はい |
| connectionString |Azure Cosmos DB データベースに接続するために必要な情報を指定します。<br />**注**:後の例で示すように、接続文字列でデータベース情報を指定する必要があります。 <br/> アカウント キーを Azure Key Vault に格納して、接続文字列から `accountKey` 構成をプルすることもできます。 詳細については、下記の例と、「[Azure Key Vault への資格情報の格納](store-credentials-in-key-vault.md)」の記事を参照してください。 |はい |
| connectVia | データ ストアに接続するために使用される [Integration Runtime](concepts-integration-runtime.md)。 Azure Integration Runtime またはセルフホステッド統合ランタイムを使用できます (データ ストアがプライベート ネットワークにある場合)。 このプロパティを指定しないと、既定の Azure Integration Runtime が使用されます。 |いいえ |

**例**

```json
{
    "name": "CosmosDbSQLAPILinkedService",
    "properties": {
        "type": "CosmosDb",
        "typeProperties": {
            "connectionString": "AccountEndpoint=<EndpointUrl>;AccountKey=<AccessKey>;Database=<Database>"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

**例: アカウント キーを Azure Key Vault に格納する**

```json
{
    "name": "CosmosDbSQLAPILinkedService",
    "properties": {
        "type": "CosmosDb",
        "typeProperties": {
            "connectionString": "AccountEndpoint=<EndpointUrl>;Database=<Database>",
            "accountKey": { 
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

### <a name="service-principal-authentication"></a><a name="service-principal-authentication"></a> サービス プリンシパル認証

>[!NOTE]
>現在、サービス プリンシパル認証はデータ フローではサポートされていません。

サービス プリンシパル認証を使用するには、次の手順に従います。

1. 「[アプリケーションを Azure AD テナントに登録する](../storage/common/storage-auth-aad-app.md#register-your-application-with-an-azure-ad-tenant)」の手順に従って、Azure Active Directory (Azure AD) にアプリケーション エンティティを登録します。 次の値を記録しておきます。リンクされたサービスを定義するときに使います。

    - アプリケーション ID
    - アプリケーション キー
    - テナント ID

2. サービス プリンシパルに適切なアクセス許可を付与します。 Cosmos DB のアクセス許可の動作例については、「[ファイルとディレクトリのアクセス制御リスト](../cosmos-db/how-to-setup-rbac.md)」を参照してください。 具体的には、ロール定義を作成し、サービス プリンシパル オブジェクト ID を使用してロールをサービス プリンシパルに割り当てます。 

リンクされたサービスでは、次のプロパティがサポートされています。

| プロパティ | 説明 | 必須 |
|:--- |:--- |:--- |
| type | type プロパティは **CosmosDb** に設定する必要があります。 |Yes |
| accountEndpoint | Azure Cosmos DB のアカウント エンドポイントの URL を指定します。 | はい |
| database | データベースの名前を指定します。 | はい |
| servicePrincipalId | アプリケーションのクライアント ID を取得します。 | はい |
| servicePrincipalCredentialType | サービス プリンシパル認証に使用する資格情報の種類。 使用できる値は **ServicePrincipalKey** と **ServicePrincipalCert** です。 | Yes |
| servicePrincipalCredential | サービス プリンシパルの資格情報。 <br/> 資格情報の種類として **ServicePrincipalKey** を使用する場合は、アプリケーションのキーを指定します。 このフィールドを **SecureString** とマークして安全に保存するか、[Azure Key Vault に保存されているシークレットを参照します](store-credentials-in-key-vault.md)。 <br/> 資格情報として **ServicePrincipalCert** を使用する場合、Azure Key Vault 内の証明書を参照します。 | はい |
| tenant | アプリケーションが存在するテナントの情報 (ドメイン名またはテナント ID) を指定します。 これは、Azure portal の右上隅をマウスでポイントすることで取得できます。 | はい |
| azureCloudType | サービス プリンシパル認証の場合は、Azure Active Directory アプリケーションの登録先である Azure クラウド環境の種類を指定します。 <br/> 指定できる値は、**AzurePublic**、**AzureChina**、**AzureUsGovernment**、および **AzureGermany** です。 既定では、サービスのクラウド環境が使用されます。 | いいえ |
| connectVia | データ ストアに接続するために使用される[統合ランタイム](concepts-integration-runtime.md)。 データ ストアがプライベート ネットワーク内にある場合、Azure Integration Runtime またはセルフホステッド統合ランタイムを使用できます。 指定されていない場合は、既定の Azure Integration Runtime が使用されます。 |いいえ |

**例: サービス プリンシパル キー認証の使用**

サービス プリンシパル キーを Azure Key Vault に格納することもできます。

```json
{
    "name": "CosmosDbSQLAPILinkedService",
    "properties": {
        "type": "CosmosDb",
        "typeProperties": {
            "accountEndpoint": "<account endpoint>",
            "database": "<database name>",
            "servicePrincipalId": "<service principal id>",
            "servicePrincipalCredentialType": "ServicePrincipalKey",
            "servicePrincipalCredential": {
                "type": "SecureString",
                "value": "<service principal key>"
            },
            "tenant": "<tenant info, e.g. microsoft.onmicrosoft.com>" 
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

**例: サービス プリンシパル認証の使用**
```json
{
    "name": "CosmosDbSQLAPILinkedService",
    "properties": {
        "type": "CosmosDb",
        "typeProperties": {
            "accountEndpoint": "<account endpoint>",
            "database": "<database name>", 
            "servicePrincipalId": "<service principal id>",
            "servicePrincipalCredentialType": "ServicePrincipalCert",
            "servicePrincipalCredential": { 
                "type": "AzureKeyVaultSecret", 
                "store": { 
                    "referenceName": "<AKV reference>", 
                    "type": "LinkedServiceReference" 
                }, 
                "secretName": "<certificate name in AKV>" 
            },
            "tenant": "<tenant info, e.g. microsoft.onmicrosoft.com>" 
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

### <a name="managed-identities-for-azure-resources-authentication"></a><a name="managed-identity"></a> Azure リソースのマネージド ID 認証

>[!NOTE]
>現時点では、マネージド ID 認証はデータ フローではサポートされていません。

データ ファクトリまたは Synapse パイプラインは、特定のサービス インスタンスを表す、[Azure リソースのマネージド ID](data-factory-service-identity.md) に関連付けることができます。 独自のサービス プリンシパルを使用するのと同様に、Cosmos DB 認証にこのマネージド ID を直接使用できます。 これにより、この指定されたリソースは、Cosmos DB にアクセスしてデータをコピーできます。

Azure リソースのマネージド ID 認証を使用するには、次の手順に従います。

1. サービスと共に生成された **マネージド ID オブジェクト ID** の値をコピーして、[マネージド ID 情報を取得します](data-factory-service-identity.md#retrieve-managed-identity)。

2. マネージド ID に適切なアクセス許可を付与します。 Cosmos DB のアクセス許可の動作例については、「[ファイルとディレクトリのアクセス制御リスト](../cosmos-db/how-to-setup-rbac.md)」を参照してください。 具体的には、ロールの定義を作成し、そのロールをマネージド ID に割り当てる必要があります。

リンクされたサービスでは、次のプロパティがサポートされています。

| プロパティ | 説明 | 必須 |
|:--- |:--- |:--- |
| type | type プロパティは **CosmosDb** に設定する必要があります。 |Yes |
| accountEndpoint | Azure Cosmos DB のアカウント エンドポイントの URL を指定します。 | はい |
| database | データベースの名前を指定します。 | はい |
| connectVia | データ ストアに接続するために使用される[統合ランタイム](concepts-integration-runtime.md)。 データ ストアがプライベート ネットワーク内にある場合、Azure Integration Runtime またはセルフホステッド統合ランタイムを使用できます。 指定されていない場合は、既定の Azure Integration Runtime が使用されます。 |いいえ |

**例:**

```json
{
    "name": "CosmosDbSQLAPILinkedService",
    "properties": {
        "type": "CosmosDb",
        "typeProperties": {
            "accountEndpoint": "<account endpoint>",
            "database": "<database name>"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

## <a name="dataset-properties"></a>データセットのプロパティ

データセットの定義に使用できるセクションとプロパティの完全な一覧については、「[データセットとリンクされたサービス](concepts-datasets-linked-services.md)」を参照してください。

Azure Cosmos DB (SQL API) データセットでは、次のプロパティがサポートされます。 

| プロパティ | 説明 | 必須 |
|:--- |:--- |:--- |
| type | データセットの **type** プロパティは、**CosmosDbSqlApiCollection** に設定する必要があります. |はい |
| collectionName |Azure Cosmos DB ドキュメント コレクションの名前です。 |はい |

"DocumentDbCollection" 型のデータセットを使用する場合、コピーおよび検索のアクティビティの下位互換性のためにそのままサポートされています。Data Flow ではサポートされていません。 今後は新しいモデルを使用することをお勧めします。

**例**

```json
{
    "name": "CosmosDbSQLAPIDataset",
    "properties": {
        "type": "CosmosDbSqlApiCollection",
        "linkedServiceName":{
            "referenceName": "<Azure Cosmos DB linked service name>",
            "type": "LinkedServiceReference"
        },
        "schema": [],
        "typeProperties": {
            "collectionName": "<collection name>"
        }
    }
}
```

## <a name="copy-activity-properties"></a>コピー アクティビティのプロパティ

このセクションでは、Azure Cosmos DB (SQL API) のソースとシンクでサポートされるプロパティの一覧を示します。 アクティビティの定義に利用できるセクションとプロパティの完全な一覧については、[パイプライン](concepts-pipelines-activities.md)に関する記事を参照してください。

### <a name="azure-cosmos-db-sql-api-as-source"></a>ソースとしての Azure Cosmos DB (SQL API)

Azure Cosmos DB (SQL API) からデータをコピーするには、コピー アクティビティの **source** の種類を **DocumentDbCollectionSource** に設定します。 

コピー アクティビティの **source** セクションでは、次のプロパティがサポートされます。

| プロパティ | 説明 | 必須 |
|:--- |:--- |:--- |
| type | コピー アクティビティのソースの **type** プロパティを **CosmosDbSqlApiSource** に設定する必要があります。 |はい |
| query |データを読み取る Azure Cosmos DB クエリを指定します。<br/><br/>例:<br /> `SELECT c.BusinessEntityID, c.Name.First AS FirstName, c.Name.Middle AS MiddleName, c.Name.Last AS LastName, c.Suffix, c.EmailPromotion FROM c WHERE c.ModifiedDate > \"2009-01-01T00:00:00\"` |いいえ <br/><br/>指定しないと、SQL ステートメント `select <columns defined in structure> from mycollection` が実行されます |
| preferredRegions | Cosmos DB からデータを取得するときに接続するリージョンの優先リスト。 | いいえ |
| PageSize | クエリ結果のページあたりのドキュメント数。 既定値は "-1" で、これはサービス側の動的ページ サイズが最大 1000 まで使用されることを意味します。 | いいえ |
| detectDatetime | ドキュメント内の文字列値から datetime を検出するかどうか。 使用可能な値: **true** (既定値)、**false**。 | いいえ |

"DocumentDbCollectionSource" 型のソースを使用する場合、下位互換性のためにそのままサポートされます。 Cosmos DB からデータをコピーする豊富な機能を提供する新しいモデルを使用することをお勧めします。

**例**

```json
"activities":[
    {
        "name": "CopyFromCosmosDBSQLAPI",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Cosmos DB SQL API input dataset name>",
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
                "type": "CosmosDbSqlApiSource",
                "query": "SELECT c.BusinessEntityID, c.Name.First AS FirstName, c.Name.Middle AS MiddleName, c.Name.Last AS LastName, c.Suffix, c.EmailPromotion FROM c WHERE c.ModifiedDate > \"2009-01-01T00:00:00\"",
                "preferredRegions": [
                    "East US"
                ]
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

Cosmos DB からデータをコピーする場合、[JSON ドキュメントをそのままエクスポートする](#import-and-export-json-documents)場合を除き、Copy アクティビティでマッピングを指定するのがベスト プラクティスです。 サービスでは、アクティビティで指定したマッピングが優先されます。行に列の値が含まれていない場合、列の値には null 値が指定されます。 マッピングを指定しない場合、サービスはデータの最初の行を使用してスキーマを推測します。 最初の行に完全なスキーマが含まれていないと、アクティビティ操作の結果で一部の行が欠落します。

### <a name="azure-cosmos-db-sql-api-as-sink"></a>シンクとしての Azure Cosmos DB (SQL API)

Azure Cosmos DB (SQL API) にデータをコピーするには、コピー アクティビティの **sink** の種類を **DocumentDbCollectionSink** に設定します。 

コピー アクティビティの **sink** セクションでは、次のプロパティがサポートされます。

| プロパティ | 説明 | 必須 |
|:--- |:--- |:--- |
| type | コピー アクティビティのシンクの **type** プロパティは **CosmosDbSqlApiSink** に設定する必要があります。 |はい |
| writeBehavior |Azure Cosmos DB にデータを書き込む方法を示します。 使用可能な値は、**Insert**、**Upsert** です。<br/><br/>**upsert** の動作は、同じ ID を持つドキュメントが既に存在する場合に、そのドキュメントを置き換えることです。それ以外の場合はドキュメントを挿入します。<br /><br />**注**: 元のドキュメントまたは列マッピングで ID が指定されていない場合は、サービスによってドキュメントの ID が自動的に生成されます。 つまり、**upsert** が期待どおりに動作するには、ドキュメントに ID があることを確認する必要があります。 |いいえ<br />(既定値は **insert** です) |
| writeBatchSize | サービスでは、[Azure Cosmos DB バルク エグゼキューター ライブラリ](https://github.com/Azure/azure-cosmosdb-bulkexecutor-dotnet-getting-started)を使用して Azure Cosmos DB にデータが書き込まれます。 **writeBatchSize** プロパティにより、サービスでライブラリに提供されるドキュメントのサイズが制御されます。 パフォーマンスを向上させるには **writeBatchSize** の値を大きくしてみて、ドキュメントのサイズが大きい場合は値を小さくしてみます。以下のヒントをご覧ください。 |いいえ<br />(既定値は **10,000**) |
| disableMetricsCollection | サービスでは、コピーのパフォーマンスの最適化と推奨事項のために、Cosmos DB RU などのメトリックが収集されます。 この動作に不安がある場合は、`true` を指定してオフにします。 | いいえ (既定値は `false`) |
| maxConcurrentConnections |アクティビティの実行中にデータ ストアに対して確立されたコンカレント接続数の上限。 コンカレント接続を制限する場合にのみ、値を指定します。| いいえ |


>[!TIP]
>JSON ドキュメントをそのままインポートするには、「[JSON ドキュメントのインポートとエクスポート](#import-and-export-json-documents)」セクションを参照してください。表形式のデータからコピーするには、「[リレーショナル データベースから Cosmos DB への移行](#migrate-from-relational-database-to-cosmos-db)」を参照してください。

>[!TIP]
>Cosmos DB では、1 つの要求のサイズは 2 MB に制限されます。 式は、"要求サイズ = 1 つのドキュメント サイズ * バッチ書き込みサイズ" です。 "**要求のサイズが大きすぎる**" というエラーが発生する場合は、コピー シンクの構成で **`writeBatchSize` の値を小さくします**。

"DocumentDbCollectionSink" 型のソースを使用する場合、下位互換性のためにそのままサポートされます。 Cosmos DB からデータをコピーする豊富な機能を提供する新しいモデルを使用することをお勧めします。

**例**

```json
"activities":[
    {
        "name": "CopyToCosmosDBSQLAPI",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Document DB output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "CosmosDbSqlApiSink",
                "writeBehavior": "upsert"
            }
        }
    }
]
```

### <a name="schema-mapping"></a>スキーマ マッピング

Azure Cosmos DB から表形式のシンク、あるいはその逆の方向でデータをコピーするには、[スキーマ マッピング](copy-activity-schema-and-type-mapping.md#schema-mapping)に関するセクションを参照してください。

## <a name="mapping-data-flow-properties"></a>Mapping Data Flow のプロパティ

マッピング データフローでデータを変換する場合は、Cosmos DB でコレクションの読み取りと書き込みを行うことができます。 詳細については、マッピング データ フローの[ソース変換](data-flow-source.md)と[シンク変換](data-flow-sink.md)に関する記事をご覧ください。

### <a name="source-transformation"></a>ソース変換

Azure Cosmos DB に固有の設定は、ソース変換の **[Source Options]\(ソース オプション\)** タブにあります。 

**Include system columns (システム列を含める):** true にすると、CosmosDB からのデータ フローのメタデータに ```id``` や ```_ts``` といったシステム列が含められます。 コレクションを更新するときは、既存の行 ID を把握できるように、これを含めることが重要となります。

**ページ サイズ:** クエリ結果のページあたりのドキュメント数。 既定値は "-1" で、サービスの動的ページが最大 1000 個使用されます。

**スループット**: このデータ フローの各実行について、CosmosDB コレクションの読み取り操作時に適用したい RU 数に対するオプションの値を設定します。 最小は 400 です。

**Preferred regions (優先リージョン):** このプロセスの優先読み取りリージョンを選択します。

#### <a name="json-settings"></a>JSON 設定

**1 つのドキュメント:** サービスがファイル全体を 1 つの JSON ドキュメントとして扱う場合は、このオプションを選択します。

**引用符で囲まれていない列名:** JSON の列名が引用符で囲まれていない場合は、このオプションを選択します。

**コメントあり:** JSON ドキュメントのデータにコメントが含まれている場合は、このオプションを使用します。

**一重引用符付き:** ドキュメント内の列と値が一重引用符で囲まれている場合は、このオプションを選択する必要があります。

**円記号によるエスケープ:** バックスラッシュを使用して JSON 内の文字をエスケープする場合は、このオプションを選択します。

### <a name="sink-transformation"></a>シンク変換

Azure Cosmos DB に固有の設定は、シンク変換の **[設定]** タブにあります。

**[Update method]\(更新方法\)** : 対象となるデータベースに対して許可される操作を指定します。 既定では、挿入のみが許可されます。 行を更新、アップサート、または削除するには、それらのアクションに対して行をタグ付けするために行の変更変換が必要になります。 更新、アップサート、削除の場合、1 つまたは複数のキー列を設定して、変更する行を決定する必要があります。

**Collection action (コレクション アクション):** 書き込み前にターゲット コレクションを再作成するかどうかを決定します。
* なし: コレクションに対してアクションは実行されません。
* 再作成: コレクションは削除され、再作成されます

**Batch size**: 各バッチの Cosmos DB コレクションに書き込まれるオブジェクトの数を表す整数。 通常、既定のバッチ サイズで開始するだけで十分です。 この値をさらに調整するには、次の点に注意してください。

- Cosmos DB では、1 つの要求のサイズは 2 MB に制限されます。 式は、"要求サイズ = 1 つのドキュメント サイズ * バッチ サイズ" となります。 "要求のサイズが大きすぎる" というエラーが発生する場合は、バッチ サイズの値を小さくします。
- バッチ サイズを大きくすると、サービスのスループットの向上を実現できますが、ワークロードを強化するために十分な RU を割り当てる必要があります。

**パーティション キー**:コレクションのパーティション キーを表す文字列を入力します。 例: ```/movies/title```

**スループット**: このデータ フローの実行ごとに CosmosDB コレクションに適用する RU の数のオプション値を設定します。 最小は 400 です。

**Write throughput budget (書き込みスループット予算)** : コレクションに割り当てられた合計スループットのうち、このデータ フロー書き込み操作に割り当てる RU を表す整数。

## <a name="lookup-activity-properties"></a>Lookup アクティビティのプロパティ

プロパティの詳細については、[Lookup アクティビティ](control-flow-lookup-activity.md)に関するページを参照してください。

## <a name="import-and-export-json-documents"></a>JSON ドキュメントのインポートとエクスポート

Azure Cosmos DB (SQL API) コネクタを使用して簡単に次のことができます。

* 2 つの Azure Cosmos DB コレクション間でドキュメントをそのままコピーします。
* Azure Blob Storage、Azure Data Lake Store、サービスでサポートされている他のファイルベースのストアなど、さまざまなソースから Azure Cosmos DB に JSON ドキュメントをインポートします。
* JSON ドキュメントを Azure Cosmos DB コレクションからさまざまなファイル ベースのストアにエクスポートします。

スキーマに依存しないコピーを実行するには:

* データ コピー ツールを使うときに、**[Export as-is to JSON files or Cosmos DB collection]\(JSON ファイルまたは Cosmos DB コレクションにそのままエクスポートする\)** オプションを選択します。
* アクティビティの作成を使用する場合は、ソースまたはシンクの対応するファイル ストアで JSON 形式を選択します。

## <a name="migrate-from-relational-database-to-cosmos-db"></a>リレーショナル データベースから Cosmos DB への移行

SQL Server などのリレーショナル データベースから Azure Cosmos DB に移行する場合、コピー アクティビティを使うと、ソースの表形式のデータを Cosmos DB のフラット化された JSON ドキュメントに簡単にマッピングできます。 たとえば、1 つの JSON ドキュメント内に関連するすべてのサブ項目を埋め込んでデータを非正規化するために、データ モデルを再設計し、[Azure Cosmos DB のデータ モデリング](../cosmos-db/modeling-data.md)に従って NoSQL ユース ケースに合わせて最適化することもできます。 そのような場合は、Copy アクティビティを使用してこれを行う方法に関するチュートリアルを含む[こちらの記事](../cosmos-db/migrate-relational-to-cosmos-db-sql-api.md)を参照してください。

## <a name="next-steps"></a>次のステップ

Copy アクティビティでソースおよびシンクとしてサポートされるデータ ストアの一覧については、[サポートされるデータ ストア](copy-activity-overview.md#supported-data-stores-and-formats)に関するセクションを参照してください。