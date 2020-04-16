---
title: Azure Cosmos DB の MongoDB 用 API での変更ストリーム
description: Azure Cosmos DB の MongoDB 用 API で変更ストリームを使用し、データに対して行われた変更を取得する方法について説明します。
author: timsander1
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: conceptual
ms.date: 03/30/2020
ms.author: tisande
ms.openlocfilehash: 38e262abefe5444c1fe7586810f4b971cc7baf6c
ms.sourcegitcommit: fb23286d4769442631079c7ed5da1ed14afdd5fc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/10/2020
ms.locfileid: "81114161"
---
# <a name="change-streams-in-azure-cosmos-dbs-api-for-mongodb"></a>Azure Cosmos DB の MongoDB 用 API での変更ストリーム

Azure Cosmos DB の MongoDB 用 API での[変更フィード](change-feed.md)のサポートは、変更ストリーム API を使用することで利用できます。 アプリケーションで変更ストリーム API を使用すると、単一のシャード内のコレクションまたは項目に対して行われた変更を取得できます。 その後、結果に基づいてさらにアクションを実行できます。 コレクション内の項目に対する変更は、変更時刻の順序でキャプチャされ、並べ替え順序はシャード キーごとに保証されます。

> [!NOTE]
> 変更ストリームを使用するには、Azure Cosmos DB の MongoDB 用 API のバージョン 3.6 以降を使用してアカウントを作成します。 変更ストリームの例を以前のバージョンに対して実行すると、エラー `Unrecognized pipeline stage name: $changeStream` が表示されることがあります。

## <a name="current-limitations"></a>現在の制限

変更ストリームを使用する場合、次の制限が適用されます。

* `operationType` および `updateDescription` プロパティは、出力ドキュメントではまだサポートされていません。
* `insert`、`update`、`replace` の操作の種類は、現在サポートされています。 
* 削除操作またはその他のイベントは、まだサポートされていません。

これらの制限により、前の例で示したように、$match ステージ、$project ステージ、および fullDocument オプションが必要になります。

Azure Cosmos DB の SQL API での変更フィードとは異なり、変更ストリームの使用や、リース コンテナーのニーズのために、個別の[変更フィード プロセッサ ライブラリ](change-feed-processor.md)はありません。 現在、変更ストリームを処理するための [Azure Functions トリガー](change-feed-functions.md)はサポートされていません。

## <a name="error-handling"></a>エラー処理

変更ストリームを使用するときは、次のエラー コードとメッセージがサポートされます。

* **HTTP エラー コード 16500** - 変更ストリームが調整されると、空のページが返されます。

* **NamespaceNotFound (OperationType 無効化)** - 存在しないコレクションで変更ストリームを実行した場合、またはコレクションが削除された場合は、`NamespaceNotFound` エラーが返されます。 出力ドキュメントでは `operationType` プロパティを返すことができないため、`operationType Invalidate` エラーの代わりに、`NamespaceNotFound` エラーが返されます。

## <a name="examples"></a>例

次の例では、コレクション内のすべての項目で変更ストリームを取得する方法を示します。 この例では、項目の挿入、更新、置換を監視するためのカーソルを作成します。 変更ストリームを取得するには、`$match` ステージ、`$project` ステージ、および `fullDocument` オプションが必要です。 変更ストリームを使用した削除操作の監視は、現在はサポートされていません。 回避策として、削除される項目にソフト マーカーを追加できます。 たとえば、"deleted" という名前の項目に属性を追加できます。 項目を削除するときは、"deleted" を `true` に設定し、項目で TTL を設定します。 "deleted" の `true` への更新が更新されるまで、この変更は変更ストリームに表示されます。

### <a name="javascript"></a>JavaScript:

```javascript
var cursor = db.coll.watch(
    [
        { $match: { "operationType": { $in: ["insert", "update", "replace"] } } },
        { $project: { "_id": 1, "fullDocument": 1, "ns": 1, "documentKey": 1 } }
    ],
    { fullDocument: "updateLookup" });

while (!cursor.isExhausted()) {
    if (cursor.hasNext()) {
        printjson(cursor.next());
    }
}
```

### <a name="c"></a>C#:

```csharp
var pipeline = new EmptyPipelineDefinition<ChangeStreamDocument<BsonDocument>>()
    .Match(change => change.OperationType == ChangeStreamOperationType.Insert || change.OperationType == ChangeStreamOperationType.Update || change.OperationType == ChangeStreamOperationType.Replace)
    .AppendStage<ChangeStreamDocument<BsonDocument>, ChangeStreamDocument<BsonDocument>, BsonDocument>(
    "{ $project: { '_id': 1, 'fullDocument': 1, 'ns': 1, 'documentKey': 1 }}");

var options = new ChangeStreamOptions{
        FullDocument = ChangeStreamFullDocumentOption.UpdateLookup
    };

var enumerator = coll.Watch(pipeline, options).ToEnumerable().GetEnumerator();

while (enumerator.MoveNext()){
        Console.WriteLine(enumerator.Current);
    }

enumerator.Dispose();
```

## <a name="changes-within-a-single-shard"></a>1 つのシャード内の変更

次の例では、1 つのシャード内の項目に対する変更を取得する方法を示します。 この例では、シャード キーが "a" で、シャード キー値が "1" の項目の変更を取得します。 異なるシャードの変更を複数のクライアントで並列に読み取ることができます。

```javascript
var cursor = db.coll.watch(
    [
        {
            $match: {
                $and: [
                    { "fullDocument.a": 1 }, 
                    { "operationType": { $in: ["insert", "update", "replace"] } }
                ]
            }
        },
        { $project: { "_id": 1, "fullDocument": 1, "ns": 1, "documentKey": 1} }
    ],
    { fullDocument: "updateLookup" });

```

## <a name="next-steps"></a>次のステップ

* [Time to Live を使用して Azure Cosmos DB の MongoDB 用 API でデータを自動的に期限切れにする](mongodb-time-to-live.md)
* [Azure Cosmos DB の MongoDB 用 API でのインデックス作成](mongodb-indexing.md)
