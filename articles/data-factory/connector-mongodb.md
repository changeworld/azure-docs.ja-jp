---
title: MongoDB のデータをコピーする、または MongoDB にデータをコピーする
titleSuffix: Azure Data Factory & Azure Synapse
description: Azure Data Factory または Synapse Analytics パイプラインでコピー アクティビティを使用して、MongoDB からサポートされているシンク データ ストアに、またはサポートされているソース データ ストアから MongoDB にデータをコピーする方法について説明します。
author: jianleishen
ms.author: jianleishen
ms.service: data-factory
ms.subservice: data-movement
ms.topic: conceptual
ms.custom: synapse
ms.date: 09/09/2021
ms.openlocfilehash: 5642577cf8b8e1edf741c09bf4e1968d5cd69770
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/13/2021
ms.locfileid: "124831724"
---
# <a name="copy-data-from-or-to-mongodb-using-azure-data-factory-or-synapse-analytics"></a>Azure Data Factory または Synapse Analytics を使用して MongoDB との間で双方向にデータをコピーする

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

この記事では、Azure Data Factory および Azure Data Factory Synapse Analytics パイプラインで Copy アクティビティを使用して、MongoDB データベースとの間で双方向にデータをコピーする方法について説明します。 この記事は、コピー アクティビティの概要を示している[コピー アクティビティの概要](copy-activity-overview.md)に関する記事に基づいています。

>[!IMPORTANT]
>新しい MongoDB コネクタでは、ネイティブ MongoDB サポートが強化されています。 下位互換性のためにのみ現状のままサポートされているレガシ MongoDB コネクタをソリューションで使用している場合は、[MongoDB コネクタ (レガシ)](connector-mongodb-legacy.md)に関するページを参照してください。


## <a name="supported-capabilities"></a>サポートされる機能

MongoDB データベースからサポートされる任意のシンク データ ストアにデータをコピーしたり、サポートされる任意のソース データ ストアから MongoDB データベースにデータをコピーしたりできます。 コピー アクティビティによってソースまたはシンクとしてサポートされているデータ ストアの一覧については、[サポートされているデータ ストア](copy-activity-overview.md#supported-data-stores-and-formats)に関する記事の表をご覧ください。

具体的には、この MongoDB コネクタでは **4.2 までのバージョン** がサポートされます。


## <a name="prerequisites"></a>前提条件

[!INCLUDE [data-factory-v2-integration-runtime-requirements](includes/data-factory-v2-integration-runtime-requirements.md)]


## <a name="getting-started"></a>作業の開始

[!INCLUDE [data-factory-v2-connector-get-started](includes/data-factory-v2-connector-get-started.md)]

## <a name="create-a-linked-service-to-mongodb-using-ui"></a>UI を使用して MongoDB のリンク サービスを作成する

次の手順を使用して、Azure portal UI で MongoDB のリンク サービスを作成します。

1. Azure Data Factory または Synapse ワークスペースの [管理] タブに移動し、[リンクされたサービス] を選択して、[新規] をクリックします。

    # <a name="azure-data-factory"></a>[Azure Data Factory](#tab/data-factory)

    :::image type="content" source="media/doc-common-process/new-linked-service.png" alt-text="Azure Data Factory の UI で新しいリンク サービスを作成する。":::

    # <a name="azure-synapse"></a>[Azure Synapse](#tab/synapse-analytics)

    :::image type="content" source="media/doc-common-process/new-linked-service-synapse.png" alt-text="Azure Synapse の UI で新しいリンク サービスを作成する。":::

2. MongoDB を検索し、MongoDB コネクタを選択します。

    :::image type="content" source="media/connector-mongodb/mongodb-connector.png" alt-text="MongoDB コネクタを選択します。":::    

1. サービスの詳細を設定し、接続をテストし、新しいリンク サービスを作成します。

    :::image type="content" source="media/connector-mongodb/configure-mongodb-linked-service.png" alt-text="MongoDB のリンク サービスを構成します。":::

## <a name="connector-configuration-details"></a>コネクタの構成の詳細

次のセクションでは、MongoDB コネクタに固有の Data Factory エンティティを定義するために使用されるプロパティについて詳しく説明します。


## <a name="linked-service-properties"></a>リンクされたサービスのプロパティ

MongoDB のリンクされたサービスでは、次のプロパティがサポートされます。

| プロパティ | 説明 | 必須 |
|:--- |:--- |:--- |
| type |type プロパティは、次のように設定する必要があります:**MongoDbV2** |はい |
| connectionString |MongoDB 接続文字列 (例: `mongodb://[username:password@]host[:port][/[database][?options]]`) を指定します。 詳細については、[MongoDB のマニュアルの接続文字列に関するページ](https://docs.mongodb.com/manual/reference/connection-string/)を参照してください。 <br/><br /> 接続文字列を Azure Key Vault に格納することもできます。 詳細については、「[Azure Key Vault への資格情報の格納](store-credentials-in-key-vault.md)」を参照してください。 |はい |
| database | アクセスするデータベースの名前。 | はい |
| connectVia | データ ストアに接続するために使用される[統合ランタイム](concepts-integration-runtime.md)。 詳細については、「[前提条件](#prerequisites)」セクションを参照してください。 指定されていない場合は、既定の Azure 統合ランタイムが使用されます。 |いいえ |

**例:**

```json
{
    "name": "MongoDBLinkedService",
    "properties": {
        "type": "MongoDbV2",
        "typeProperties": {
            "connectionString": "mongodb://[username:password@]host[:port][/[database][?options]]",
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

データセットの定義に使用できるセクションとプロパティの完全な一覧については、「[データセットとリンクされたサービス](concepts-datasets-linked-services.md)」を参照してください。 MongoDB データセットでは、次のプロパティがサポートされます。

| プロパティ | 説明 | 必須 |
|:--- |:--- |:--- |
| type | データセットの type プロパティは、次のように設定する必要があります:**MongoDbV2Collection** | はい |
| collectionName |MongoDB データベースのコレクション名前。 |はい |

**例:**

```json
{
    "name": "MongoDbDataset",
    "properties": {
        "type": "MongoDbV2Collection",
        "typeProperties": {
            "collectionName": "<Collection name>"
        },
        "schema": [],
        "linkedServiceName": {
            "referenceName": "<MongoDB linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```


## <a name="copy-activity-properties"></a>コピー アクティビティのプロパティ

アクティビティの定義に利用できるセクションとプロパティの完全な一覧については、[パイプライン](concepts-pipelines-activities.md)に関する記事を参照してください。 このセクションでは、MongoDB ソースとシンクでサポートされるプロパティの一覧を示します。

### <a name="mongodb-as-source"></a>ソースとしての MongoDB

コピー アクティビティの **source** セクションでは、次のプロパティがサポートされます。

| プロパティ | 説明 | 必須 |
|:--- |:--- |:--- |
| type | コピー アクティビティのソースの type プロパティは、次のように設定する必要があります:**MongoDbV2Source** | はい |
| filter | クエリ演算子を使用して選択フィルターを指定します。 コレクション内のすべてのドキュメントを返すには、このパラメーターを省略するか、空のドキュメント ({}) を渡します。 | いいえ |
| cursorMethods.project | プロジェクションのドキュメントで返されるフィールドを指定します。 一致するドキュメント内のすべてのフィールドを返すには、このパラメーターを省略します。 | いいえ |
| cursorMethods.sort | 一致するドキュメントがクエリによって返される順序を指定します。 「[cursor.sort()](https://docs.mongodb.com/manual/reference/method/cursor.sort/#cursor.sort)」を参照してください。 | いいえ |
| cursorMethods.limit | サーバーが返すドキュメントの最大数を指定します。 「[cursor.limit()](https://docs.mongodb.com/manual/reference/method/cursor.limit/#cursor.limit)」を参照してください。  | いいえ |
| cursorMethods.skip | スキップするドキュメントの数と、MongoDB が結果を返すときの開始位置を指定します。 「[cursor.skip()](https://docs.mongodb.com/manual/reference/method/cursor.skip/#cursor.skip)」を参照してください。 | いいえ |
| batchSize | MongoDB インスタンスからの応答の各バッチで返されるドキュメントの数を指定します。 ほとんどの場合、バッチ サイズを変更しても、ユーザーまたはアプリケーションへの影響はありません。 Cosmos DB では各バッチのサイズが 40 MB を超過しないように制限されていますが、これはドキュメントが batchSize の数だけ存在するときの合計サイズなので、ドキュメントのサイズが大きくなる場合はこの値を減らしてください。 | いいえ<br/>(既定値は **100**) |

>[!TIP]
>このサービスは、**厳格モード** での BSON ドキュメントの利用をサポートしています。 フィルター クエリがシェル モードではなく厳格モードであることを確認してください。 詳細については、[MongoDB のマニュアル](https://docs.mongodb.com/manual/reference/mongodb-extended-json/index.html)を参照してください。

**例:**

```json
"activities":[
    {
        "name": "CopyFromMongoDB",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<MongoDB input dataset name>",
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
                "type": "MongoDbV2Source",
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

### <a name="mongodb-as-sink"></a>シンクとしての MongoDB

コピー アクティビティの **sink** セクションでは、次のプロパティがサポートされます。

| プロパティ | 説明 | 必須 |
|:--- |:--- |:--- |
| type | Copy アクティビティのシンクの **type** プロパティには **MongoDbV2Sink** を設定する必要があります。 |はい |
| writeBehavior |MongoDB データを書き込む方法について説明します。 使用可能な値は、**Insert**、**Upsert** です。<br/><br/>**upsert** の動作は、同じ `_id` を持つドキュメントが既に存在する場合、そのドキュメントを置き換えます。それ以外の場合は、ドキュメントを挿入します。<br /><br />**注**: 元のドキュメントまたは列マッピングで `_id` が指定されていない場合は、サービスによってドキュメントの `_id` が自動的に生成されます。 つまり、**upsert** が期待どおりに動作するには、ドキュメントに ID があることを確認する必要があります。 |いいえ<br />(既定値は **insert** です) |
| writeBatchSize | **writeBatchSize** プロパティにより、各バッチで書き込むドキュメントのサイズが制御されます。 パフォーマンスを向上させるには **writeBatchSize** の値を大きくしてみて、ドキュメントのサイズが大きい場合は値を小さくしてみます。 |いいえ<br />(既定値は **10,000**) |
| writeBatchTimeout | タイムアウトするまでに一括挿入操作の完了を待つ時間です。許容される値は期間です。 | いいえ<br/>(既定値は **00:30:00** - 30 分) |

>[!TIP]
>JSON ドキュメントをそのままインポートするには、「[JSON ドキュメントをインポートまたはエクスポートする](#import-and-export-json-documents)」セクションを参照してください。表形式のデータからコピーするには、「[スキーマ マッピング](#schema-mapping)」を参照してください。

**例**

```json
"activities":[
    {
        "name": "CopyToMongoDB",
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
                "type": "MongoDbV2Sink",
                "writeBehavior": "upsert"
            }
        }
    }
]
```

## <a name="import-and-export-json-documents"></a>JSON ドキュメントのインポートとエクスポート

この MongoDB コネクタを使用すると、簡単に次を実行できます。

* 2 つの MongoDB コレクション間でドキュメントをそのままコピーします。
* Azure Cosmos DB、Azure Blob Storage、Azure Data Lake Store、その他のサポートされているファイルベースのストアなど、さまざまなソースから MongoDB に JSON ドキュメントをインポートします。
* JSON ドキュメントを MongoDB コレクションからさまざまなファイル ベースのストアにエクスポートします。

このようなスキーマに依存しないコピーを実現するには、データセットの "構造" ("*スキーマ*" とも呼ばれる) のセクションと、コピー アクティビティでのスキーマ マッピングをスキップします。


## <a name="schema-mapping"></a>スキーマ マッピング

MongoDB から表形式のシンクにデータをコピーする、またはシンクから MongoDB にデータをコピーするには、[スキーマ マッピング](copy-activity-schema-and-type-mapping.md#schema-mapping)に関する記事をご覧ください。


## <a name="next-steps"></a>次のステップ
Copy アクティビティでソースおよびシンクとしてサポートされるデータ ストアの一覧については、[サポートされるデータ ストア](copy-activity-overview.md#supported-data-stores-and-formats)に関するセクションを参照してください。
