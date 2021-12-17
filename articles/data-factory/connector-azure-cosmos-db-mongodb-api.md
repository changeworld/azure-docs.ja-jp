---
title: Azure Cosmos DB の MongoDB 用 API からデータをコピーする
description: Azure Data Factory または Synapse Analytics パイプラインを使用して、サポートされるソース データ ストアのデータを Azure Cosmos DB の MongoDB 用 API にコピーしたり、Azure Cosmos DB のデータをサポートされるシンク ストアにコピーしたりする方法を説明します。
titleSuffix: Azure Data Factory & Azure Synapse
ms.author: jianleishen
author: jianleishen
ms.service: data-factory
ms.subservice: data-movement
ms.topic: conceptual
ms.custom: synapse
ms.date: 09/09/2021
ms.openlocfilehash: 6720bcfdd4e0ce804bfd15803e1ed186d94e5181
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/13/2021
ms.locfileid: "124762091"
---
# <a name="copy-data-to-or-from-azure-cosmos-dbs-api-for-mongodb-using-azure-data-factory-or-synapse-analytics"></a>Azure Data Factory または Synapse Analytics を使用して Azure Cosmos DB の MongoDB 用 API との間でデータを双方向にコピーする

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

この記事では、Azure Data Factory および Synapse Analytics パイプラインで Copy アクティビティを使用して、Azure Cosmos DB の MongoDB 用 API との間で双方向にデータをコピーする方法について説明します。 この記事は、Copy アクティビティの概要を説明する [Copy アクティビティ](copy-activity-overview.md)に関する記事に基づいています。

>[!NOTE]
>このコネクタでは、Azure Cosmos DB の MongoDB 用 API との間でのデータの双方向コピーのみがサポートされます。 SQL API については、[Cosmos DB SQL API コネクタ](connector-azure-cosmos-db.md)に関する記事を参照してください。 現在、他の種類の API はサポートされていません。

## <a name="supported-capabilities"></a>サポートされる機能

Azure Cosmos DB の MongoDB 用 API のデータをサポートされる任意のシンク データ ストアにコピーしたり、サポートされる任意のソース データ ストアのデータを Azure Cosmos DB の MongoDB 用 API にコピーしたりできます。 コピー アクティビティでソースおよびシンクとしてサポートされているデータ ストアの一覧については、「[サポートされるデータ ストアと形式](copy-activity-overview.md#supported-data-stores-and-formats)」を参照してください。

Azure Cosmos DB の MongoDB 用 API コネクタを使用して次のことができます。

- [Azure Cosmos DB の MongoDB 用 API](../cosmos-db/mongodb-introduction.md) との間で双方向にデータをコピーします。
- **挿入** または **upsert** として Azure Cosmos DB に書き込みます。
- JSON ドキュメントをインポートおよびエクスポートしたり、表形式データセットに、または表形式データセットからデータをコピーしたりします。 例としては、SQL データベースや CSV ファイルなどがあります。 JSON ファイルまたは他の Azure Cosmos DB コレクションをコピー先またはコピー元としてドキュメントをそのままコピーするには、「JSON ドキュメントをインポートまたはエクスポートする」を参照してください。

## <a name="get-started"></a>はじめに

[!INCLUDE [data-factory-v2-connector-get-started](includes/data-factory-v2-connector-get-started.md)]

## <a name="create-a-linked-service-to-azure-cosmos-dbs-api-for-mongodb-using-ui"></a>UI を使用して Azure Cosmos DB の MongoDB 用 API のリンク サービスを作成する

次の手順を使用して、Azure portal UI で Azure Cosmos DB の MongoDB 用 API のリンク サービスを作成します。

1. Azure Data Factory または Synapse ワークスペースの [管理] タブに移動し、[リンクされたサービス] を選択して、[新規] をクリックします。

    # <a name="azure-data-factory"></a>[Azure Data Factory](#tab/data-factory)

    :::image type="content" source="media/doc-common-process/new-linked-service.png" alt-text="Azure Data Factory の UI で新しいリンク サービスを作成する。":::

    # <a name="azure-synapse"></a>[Azure Synapse](#tab/synapse-analytics)

    :::image type="content" source="media/doc-common-process/new-linked-service-synapse.png" alt-text="Azure Synapse の UI を使用して新しいリンク サービスを作成します。":::

2. Cosmos を検索し、Azure Cosmos DB の MongoDB 用 API コネクタを選択します。

    :::image type="content" source="media/connector-azure-cosmos-db-mongodb-api/azure-cosmos-db-mongodb-api-connector.png" alt-text="Azure Cosmos DB の MongoDB 用 API コネクタを選択します。":::    

1. サービスの詳細を構成し、接続をテストして、新しいリンク サービスを作成します。

    :::image type="content" source="media/connector-azure-cosmos-db-mongodb-api/configure-azure-cosmos-db-mongodb-api-linked-service.png" alt-text="Azure Cosmos DB の MongoDB 用 API のリンク サービスを構成します。":::

## <a name="connector-configuration-details"></a>コネクタの構成の詳細

以下のセクションでは、Azure Cosmos DB の MongoDB 用 API に固有の Data Factory エンティティを定義するために使用できるプロパティについて詳しく説明します。

## <a name="linked-service-properties"></a>リンクされたサービスのプロパティ

Azure Cosmos DB の MongoDB 用 API のリンクされたサービスでは、次のプロパティがサポートされます。

| プロパティ | 説明 | 必須 |
|:--- |:--- |:--- |
| type | **type** プロパティは **CosmosDbMongoDbApi** に設定する必要があります。 | はい |
| connectionString |Azure Cosmos DB の MongoDB 用 API 用の接続文字列を指定します。 これは、Azure portal、Cosmos DB ブレード、プライマリまたはセカンダリ接続文字列の順に移動して確認できます。 <br/>3\.2 サーバー バージョンの場合、文字列パターンは `mongodb://<cosmosdb-name>:<password>@<cosmosdb-name>.documents.azure.com:10255/?ssl=true&replicaSet=globaldb` です。 <br/>3\.6 以上のサーバー バージョンの場合、文字列パターンは `mongodb://<cosmosdb-name>:<password>@<cosmosdb-name>.mongo.cosmos.azure.com:10255/?ssl=true&replicaSet=globaldb&retrywrites=false&maxIdleTimeMS=120000&appName=@<cosmosdb-name>@` です。<br/><br />パスワードを Azure Key Vault に格納して、接続文字列から `password` 構成をプルすることもできます。 詳細については、「[Azure Key Vault への資格情報の格納](store-credentials-in-key-vault.md)」を参照してください。|はい |
| database | アクセスするデータベースの名前。 | はい |
| isServerVersionAbove32 | サーバー バージョンが 3.2 より新しいかどうかを指定します。 使用できる値は **true** と **false** (既定値) です。 これにより、サービスで使用するドライバーが決定されます。 | はい |
| connectVia | データ ストアに接続するために使用される [Integration Runtime](concepts-integration-runtime.md)。 Azure Integration Runtime またはセルフホステッド統合ランタイムを使用できます (データ ストアがプライベート ネットワークにある場合)。 このプロパティを指定しないと、既定の Azure Integration Runtime が使用されます。 |いいえ |

**例**

```json
{
    "name": "CosmosDbMongoDBAPILinkedService",
    "properties": {
        "type": "CosmosDbMongoDbApi",
        "typeProperties": {
            "connectionString": "mongodb://<cosmosdb-name>:<password>@<cosmosdb-name>.documents.azure.com:10255/?ssl=true&replicaSet=globaldb",
            "database": "myDatabase",
            "isServerVersionAbove32": "false"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

## <a name="dataset-properties"></a>データセットのプロパティ

データセットの定義に使用できるセクションとプロパティの完全な一覧については、「[データセットとリンクされたサービス](concepts-datasets-linked-services.md)」を参照してください。 Azure Cosmos DB の MongoDB 用 API データセットでは、次のプロパティがサポートされます。

| プロパティ | 説明 | 必須 |
|:--- |:--- |:--- |
| type | データセットの **type** プロパティは、**CosmosDbMongoDbApiCollection** に設定する必要があります。 |はい |
| collectionName |Azure Cosmos DB コレクションの名前。 |はい |

**例**

```json
{
    "name": "CosmosDbMongoDBAPIDataset",
    "properties": {
        "type": "CosmosDbMongoDbApiCollection",
        "typeProperties": {
            "collectionName": "<collection name>"
        },
        "schema": [],
        "linkedServiceName":{
            "referenceName": "<Azure Cosmos DB's API for MongoDB linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

## <a name="copy-activity-properties"></a>コピー アクティビティのプロパティ

このセクションでは、Azure Cosmos DB の MongoDB 用 API のソースとシンクでサポートされるプロパティの一覧を示します。

アクティビティの定義に利用できるセクションとプロパティの完全な一覧については、[パイプライン](concepts-pipelines-activities.md)に関する記事を参照してください。

### <a name="azure-cosmos-dbs-api-for-mongodb-as-source"></a>ソースとしての Azure Cosmos DB の MongoDB 用 API

コピー アクティビティの **source** セクションでは、次のプロパティがサポートされます。

| プロパティ | 説明 | 必須 |
|:--- |:--- |:--- |
| type | コピー アクティビティのソースの **type** プロパティは **CosmosDbMongoDbApiSource** に設定する必要があります。 |はい |
| filter | クエリ演算子を使用して選択フィルターを指定します。 コレクション内のすべてのドキュメントを返すには、このパラメーターを省略するか、空のドキュメント ({}) を渡します。 | いいえ |
| cursorMethods.project | プロジェクションのドキュメントで返されるフィールドを指定します。 一致するドキュメント内のすべてのフィールドを返すには、このパラメーターを省略します。 | いいえ |
| cursorMethods.sort | 一致するドキュメントがクエリによって返される順序を指定します。 「[cursor.sort()](https://docs.mongodb.com/manual/reference/method/cursor.sort/#cursor.sort)」を参照してください。 | いいえ |
| cursorMethods.limit |    サーバーが返すドキュメントの最大数を指定します。 「[cursor.limit()](https://docs.mongodb.com/manual/reference/method/cursor.limit/#cursor.limit)」を参照してください。  | いいえ | 
| cursorMethods.skip | スキップするドキュメントの数と、MongoDB が結果を返すときの開始位置を指定します。 「[cursor.skip()](https://docs.mongodb.com/manual/reference/method/cursor.skip/#cursor.skip)」を参照してください。 | いいえ |
| batchSize | MongoDB インスタンスからの応答の各バッチで返されるドキュメントの数を指定します。 ほとんどの場合、バッチ サイズを変更しても、ユーザーまたはアプリケーションへの影響はありません。 Cosmos DB では各バッチのサイズが 40 MB を超過しないように制限されていますが、これはドキュメントが batchSize の数だけ存在するときの合計サイズなので、ドキュメントのサイズが大きくなる場合はこの値を減らしてください。 | いいえ<br/>(既定値は **100**) |

>[!TIP]
>ADF は、**厳格モード** での BSON ドキュメントの利用をサポートしています。 フィルター クエリがシェル モードではなく厳格モードであることを確認してください。 詳細については、[MongoDB のマニュアル](https://docs.mongodb.com/manual/reference/mongodb-extended-json/index.html)を参照してください。

**例**

```json
"activities":[
    {
        "name": "CopyFromCosmosDBMongoDBAPI",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Azure Cosmos DB's API for MongoDB input dataset name>",
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
                "type": "CosmosDbMongoDbApiSource",
                "filter": "{datetimeData: {$gte: ISODate(\"2018-12-11T00:00:00.000Z\"),$lt: ISODate(\"2018-12-12T00:00:00.000Z\")}, _id: ObjectId(\"5acd7c3d0000000000000000\") }",
                "cursorMethods": {
                    "project": "{ _id : 1, name : 1, age: 1, datetimeData: 1 }",
                    "sort": "{ age : 1 }",
                    "skip": 3,
                    "limit": 3
                }
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

### <a name="azure-cosmos-dbs-api-for-mongodb-as-sink"></a>シンクとしての Azure Cosmos DB の MongoDB 用 API

コピー アクティビティの **sink** セクションでは、次のプロパティがサポートされます。

| プロパティ | 説明 | 必須 |
|:--- |:--- |:--- |
| type | コピー アクティビティのシンクの **type** プロパティは **CosmosDbMongoDbApiSink** に設定する必要があります。 |はい |
| writeBehavior |Azure Cosmos DB にデータを書き込む方法を示します。 使用可能な値は、**Insert**、**Upsert** です。<br/><br/>**upsert** の動作は、同じ `_id` を持つドキュメントが既に存在する場合、そのドキュメントを置き換えます。それ以外の場合は、ドキュメントを挿入します。<br /><br />**注**: 元のドキュメントまたは列マッピングで `_id` が指定されていない場合は、サービスによってドキュメントの `_id` が自動的に生成されます。 つまり、**upsert** が期待どおりに動作するには、ドキュメントに ID があることを確認する必要があります。 |いいえ<br />(既定値は **insert** です) |
| writeBatchSize | **writeBatchSize** プロパティにより、各バッチで書き込むドキュメントのサイズが制御されます。 パフォーマンスを向上させるには **writeBatchSize** の値を大きくしてみて、ドキュメントのサイズが大きい場合は値を小さくしてみます。 |いいえ<br />(既定値は **10,000**) |
| writeBatchTimeout | タイムアウトするまでに一括挿入操作の完了を待つ時間です。許容される値は期間です。 | いいえ<br/>(既定値は **00:30:00** - 30 分) |

>[!TIP]
>JSON ドキュメントをそのままインポートするには、「[JSON ドキュメントをインポートまたはエクスポートする](#import-and-export-json-documents)」セクションを参照してください。表形式のデータからコピーするには、「[スキーマ マッピング](#schema-mapping)」を参照してください。

**例**

```json
"activities":[
    {
        "name": "CopyToCosmosDBMongoDBAPI",
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
                "type": "CosmosDbMongoDbApiSink",
                "writeBehavior": "upsert"
            }
        }
    }
]
```

## <a name="import-and-export-json-documents"></a>JSON ドキュメントのインポートとエクスポート

Azure Cosmos DB コネクタを使用して簡単に次のことができます。

* 2 つの Azure Cosmos DB コレクション間でドキュメントをそのままコピーします。
* MongoDB、Azure Blob Storage、Azure Data Lake Store、サービスでサポートされている他のファイルベースのストアなど、さまざまなソースから Azure Cosmos DB に JSON ドキュメントをインポートします。
* JSON ドキュメントを Azure Cosmos DB コレクションからさまざまなファイル ベースのストアにエクスポートします。

スキーマに依存しないコピーを実行するには:

* データ コピー ツールを使うときに、**[Export as-is to JSON files or Cosmos DB collection]\(JSON ファイルまたは Cosmos DB コレクションにそのままエクスポートする\)** オプションを選択します。
* アクティビティの作成を使用する場合は、ソースまたはシンクの対応するファイル ストアで JSON 形式を選択します。

## <a name="schema-mapping"></a>スキーマ マッピング

Azure Cosmos DB の MongoDB 用 API から表形式のシンク、あるいはその逆の方向でデータをコピーするには、[スキーマ マッピング](copy-activity-schema-and-type-mapping.md#schema-mapping)に関するセクションを参照してください。

特に Cosmos DB への書き込みでは、必ずソース データの正しいオブジェクト ID を Cosmos DB に入力する必要があります。たとえば、SQL データベース テーブルに "id" 列があり、その値を MongoDB での挿入/アップサート用のドキュメント ID として使用したい場合は、MongoDB の厳格モードの定義 (`_id.$oid`) に従って、以下のように適切なスキーマ マッピングを設定する必要があります。

:::image type="content" source="./media/connector-azure-cosmos-db-mongodb-api/map-id-in-mongodb-sink.png" alt-text="MongoDB シンクに ID をマップする":::

コピー アクティビティの実行後は、以下の BSON ObjectId がシンクに生成されます。

```json
{
    "_id": ObjectId("592e07800000000000000000")
}
``` 

## <a name="next-steps"></a>次のステップ

Copy アクティビティでソースおよびシンクとしてサポートされるデータ ストアの一覧については、[サポートされるデータ ストア](copy-activity-overview.md#supported-data-stores-and-formats)に関するセクションを参照してください。
