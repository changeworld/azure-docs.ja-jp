---
title: MongoDB Atlas のデータをコピーする、または MongoDB Atlas にデータをコピーする
description: Azure Data Factory または Synapse Analytics パイプラインで Copy アクティビティを使用して、MongoDB Atlas からサポートされているシンク データ ストアに、またはサポートされているソース データ ストアから MongoDB Atlas にデータをコピーする方法について説明します。
titleSuffix: Azure Data Factory & Azure Synapse
author: jianleishen
ms.author: jianleishen
ms.service: data-factory
ms.subservice: data-movement
ms.topic: conceptual
ms.custom: synapse
ms.date: 09/09/2021
ms.openlocfilehash: bd99246d5174049e2f58f3c4dc114dcb8ef41eb6
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/13/2021
ms.locfileid: "124827140"
---
# <a name="copy-data-from-or-to-mongodb-atlas-using-azure-data-factory-or-synapse-analytics"></a>Azure Data Factory または Azure Synapse Analytics を使用して MongoDB Atlas との間でデータをコピーする

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

この記事では、Azure Data Factory または Synapse Analytics パイプラインで Copy アクティビティを使用して、MongoDB Atlas データベースからデータをコピーする方法について説明します。 この記事は、コピー アクティビティの概要を示している[コピー アクティビティの概要](copy-activity-overview.md)に関する記事に基づいています。

## <a name="supported-capabilities"></a>サポートされる機能

MongoDB Atlas データベースからサポートされる任意のシンク データ ストアにデータをコピーしたり、サポートされる任意のソース データ ストアから MongoDB Atlas データベースにデータをコピーしたりできます。 コピー アクティビティによってソースまたはシンクとしてサポートされているデータ ストアの一覧については、[サポートされているデータ ストア](copy-activity-overview.md#supported-data-stores-and-formats)に関する記事の表をご覧ください。

具体的には、この MongoDB Atlas コネクタは **4.2 までのバージョン** をサポートしています。

## <a name="prerequisites"></a>前提条件

コピーに Azure Integration Runtime を使用する場合、有効なリージョンの [Azure Integration Runtime の IP](azure-integration-runtime-ip-addresses.md) を MongoDB Atlas IP アクセスの一覧に追加してください。

## <a name="getting-started"></a>はじめに

[!INCLUDE [data-factory-v2-connector-get-started](includes/data-factory-v2-connector-get-started.md)]

## <a name="create-a-linked-service-to-mongodb-atlas-using-ui"></a>UI を使用して MongoDB Atlas のリンク サービスを作成する

次の手順を使用して、Azure portal UI で MongoDB Atlas のリンク サービスを作成します。

1. Azure Data Factory または Synapse ワークスペースの [管理] タブに移動し、[リンク サービス] を選択して、[新規] をクリックします。

    # <a name="azure-data-factory"></a>[Azure Data Factory](#tab/data-factory)

    :::image type="content" source="media/doc-common-process/new-linked-service.png" alt-text="Azure Data Factory の UI で新しいリンク サービスを作成する。":::

    # <a name="azure-synapse"></a>[Azure Synapse](#tab/synapse-analytics)

    :::image type="content" source="media/doc-common-process/new-linked-service-synapse.png" alt-text="Azure Synapse の UI を使用して新しいリンク サービスを作成します。":::

2. MongoDB を検索し、MongoDB Atlas コネクタを選択します。

    :::image type="content" source="media/connector-mongodb-atlas/mongodb-atlas-connector.png" alt-text="MongoDB Atlas コネクタを選択します。":::    

1. サービスの詳細を構成し、接続をテストして、新しいリンク サービスを作成します。

    :::image type="content" source="media/connector-mongodb-atlas/configure-mongodb-atlas-linked-service.png" alt-text="MongoDB Atlas へのリンク サービスを構成します。":::

## <a name="connector-configuration-details"></a>コネクタの構成の詳細



以下のセクションでは、MongoDB Atlas コネクタに固有の Data Factory エンティティを定義するために使用されるプロパティの詳細について説明します。

## <a name="linked-service-properties"></a>リンクされたサービスのプロパティ

MongoDB Atlas のリンクされたサービスでは、次のプロパティがサポートされます。

| プロパティ | 説明 | 必須 |
|:--- |:--- |:--- |
| type |type プロパティは、次のように設定する必要があります:**MongoDbAtlas** |はい |
| connectionString |MongoDB Atlas 接続文字列 (例: `mongodb+srv://<username>:<password>@<clustername>.<randomString>.<hostName>/<dbname>?<otherProperties>`) を指定します。 <br/><br /> 接続文字列を Azure Key Vault に格納することもできます。 詳細については、「[Azure Key Vault への資格情報の格納](store-credentials-in-key-vault.md)」を参照してください。 |はい |
| database | アクセスするデータベースの名前。 | はい |
| connectVia | データ ストアに接続するために使用される[統合ランタイム](concepts-integration-runtime.md)。 詳細については、「[前提条件](#prerequisites)」セクションを参照してください。 指定されていない場合は、既定の Azure 統合ランタイムが使用されます。 |いいえ |

**例:**

```json
{
    "name": "MongoDbAtlasLinkedService",
    "properties": {
        "type": "MongoDbAtlas",
        "typeProperties": {
            "connectionString": "mongodb+srv://<username>:<password>@<clustername>.<randomString>.<hostName>/<dbname>?<otherProperties>",
            "database": "myDatabase"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

## <a name="dataset-properties"></a>データセットのプロパティ

データセットの定義に使用できるセクションとプロパティの完全な一覧については、「[データセットとリンクされたサービス](concepts-datasets-linked-services.md)」を参照してください。 MongoDB Atlas データセットでは、次のプロパティがサポートされます。

| プロパティ | 説明 | 必須 |
|:--- |:--- |:--- |
| type | データセットの type プロパティは、次のように設定する必要があります:**MongoDbAtlasCollection** | はい |
| collectionName |MongoDB Atlas データベースのコレクション名。 |はい |

**例:**

```json
{
    "name": "MongoDbAtlasDataset",
    "properties": {
        "type": "MongoDbAtlasCollection",
        "typeProperties": {
            "collectionName": "<Collection name>"
        },
        "schema": [],
        "linkedServiceName": {
            "referenceName": "<MongoDB Atlas linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

## <a name="copy-activity-properties"></a>コピー アクティビティのプロパティ

アクティビティの定義に利用できるセクションとプロパティの完全な一覧については、[パイプライン](concepts-pipelines-activities.md)に関する記事を参照してください。 このセクションでは、MongoDB Atlas ソースおよびシンクでサポートされるプロパティの一覧を示します。

### <a name="mongodb-atlas-as-source"></a>ソースとしての MongoDB Atlas

コピー アクティビティの **source** セクションでは、次のプロパティがサポートされます。

| プロパティ | 説明 | 必須 |
|:--- |:--- |:--- |
| type | コピー アクティビティのソースの type プロパティは、次のように設定する必要があります:**MongoDbAtlasSource** | はい |
| filter | クエリ演算子を使用して選択フィルターを指定します。 コレクション内のすべてのドキュメントを返すには、このパラメーターを省略するか、空のドキュメント ({}) を渡します。 | いいえ |
| cursorMethods.project | プロジェクションのドキュメントで返されるフィールドを指定します。 一致するドキュメント内のすべてのフィールドを返すには、このパラメーターを省略します。 | いいえ |
| cursorMethods.sort | 一致するドキュメントがクエリによって返される順序を指定します。 「[cursor.sort()](https://docs.mongodb.com/manual/reference/method/cursor.sort/#cursor.sort)」を参照してください。 | いいえ |
| cursorMethods.limit | サーバーが返すドキュメントの最大数を指定します。 「[cursor.limit()](https://docs.mongodb.com/manual/reference/method/cursor.limit/#cursor.limit)」を参照してください。  | いいえ |
| cursorMethods.skip | スキップするドキュメントの数と、MongoDB Atlas が結果を返すときの開始位置を指定します。 「[cursor.skip()](https://docs.mongodb.com/manual/reference/method/cursor.skip/#cursor.skip)」を参照してください。 | いいえ |
| batchSize | MongoDB Atlas インスタンスからの応答の各バッチで返されるドキュメントの数を指定します。 ほとんどの場合、バッチ サイズを変更しても、ユーザーまたはアプリケーションへの影響はありません。 Cosmos DB では各バッチのサイズが 40 MB を超過しないように制限されていますが、これはドキュメントが batchSize の数だけ存在するときの合計サイズなので、ドキュメントのサイズが大きくなる場合はこの値を減らしてください。 | いいえ<br/>(既定値は **100**) |

>[!TIP]
>このサービスは、**厳格モード** での BSON ドキュメントの利用をサポートしています。 フィルター クエリがシェル モードではなく厳格モードであることを確認してください。 詳細については、[MongoDB のマニュアル](https://docs.mongodb.com/manual/reference/mongodb-extended-json/index.html)を参照してください。

**例:**

```json
"activities":[
    {
        "name": "CopyFromMongoDbAtlas",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<MongoDB Atlas input dataset name>",
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
                "type": "MongoDbAtlasSource",
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

### <a name="mongodb-atlas-as-sink"></a>シンクとしての MongoDB Atlas

コピー アクティビティの **sink** セクションでは、次のプロパティがサポートされます。

| プロパティ | 説明 | 必須 |
|:--- |:--- |:--- |
| type | Copy アクティビティのシンクの **type** プロパティには **MongoDbAtlasSink** を設定する必要があります。 |はい |
| writeBehavior |MongoDB Atlas にデータを書き込む方法について説明します。 使用可能な値は、**Insert**、**Upsert** です。<br/><br/>**upsert** の動作は、同じ `_id` を持つドキュメントが既に存在する場合、そのドキュメントを置き換えます。それ以外の場合は、ドキュメントを挿入します。<br /><br />**注**: 元のドキュメントまたは列マッピングで `_id` が指定されていない場合は、サービスによってドキュメントの `_id` が自動的に生成されます。 つまり、**upsert** が期待どおりに動作するには、ドキュメントに ID があることを確認する必要があります。 |いいえ<br />(既定値は **insert** です) |
| writeBatchSize | **writeBatchSize** プロパティにより、各バッチで書き込むドキュメントのサイズが制御されます。 パフォーマンスを向上させるには **writeBatchSize** の値を大きくしてみて、ドキュメントのサイズが大きい場合は値を小さくしてみます。 |いいえ<br />(既定値は **10,000**) |
| writeBatchTimeout | タイムアウトするまでに一括挿入操作の完了を待つ時間です。許容される値は期間です。 | いいえ<br/>(既定値は **00:30:00** - 30 分) |

>[!TIP]
>JSON ドキュメントをそのままインポートするには、「[JSON ドキュメントをインポートまたはエクスポートする](#import-and-export-json-documents)」セクションを参照してください。表形式のデータからコピーするには、「[スキーマ マッピング](#schema-mapping)」を参照してください。

**例**

```json
"activities":[
    {
        "name": "CopyToMongoDBAtlas",
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
                "type": "MongoDbAtlasSink",
                "writeBehavior": "upsert"
            }
        }
    }
]
```

## <a name="import-and-export-json-documents"></a>JSON ドキュメントのインポートとエクスポート

この MongoDB Atlas コネクタを使用すると、簡単に次を実行できます。

* 2 つの MongoDB Atlas コレクション間でドキュメントをそのままコピーします。
* Azure Cosmos DB、Azure Blob Storage、Azure Data Lake Store、その他のサポートされているファイルベースのストアなど、さまざまなソースから MongoDB Atlas に JSON ドキュメントをインポートします。
* JSON ドキュメントをMongoDB Atlas コレクションからさまざまなファイル ベースのストアにエクスポートします。

このようなスキーマに依存しないコピーを実現するには、データセットの "構造" ("*スキーマ*" とも呼ばれる) のセクションと、コピー アクティビティでのスキーマ マッピングをスキップします。


## <a name="schema-mapping"></a>スキーマ マッピング

MongoDB Atlas から表形式のシンクにデータをコピーする、またはシンクから MongoDB Atlas にデータをコピーするには、[スキーマ マッピング](copy-activity-schema-and-type-mapping.md#schema-mapping)に関する記事をご覧ください。

## <a name="next-steps"></a>次のステップ
Copy アクティビティでソースおよびシンクとしてサポートされるデータ ストアの一覧については、[サポートされるデータ ストア](copy-activity-overview.md#supported-data-stores-and-formats)に関するセクションを参照してください。
