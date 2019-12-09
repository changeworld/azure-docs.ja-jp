---
title: Azure Cosmos DB の MongoDB 用 API での変更ストリーム
description: Azure Cosmos DB の MongoDB 用 API で変更ストリームを使用し、データに対して行われた変更を取得する方法について説明します。
author: srchi
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: conceptual
ms.date: 11/16/2019
ms.author: srchi
ms.openlocfilehash: fbfce1c107fcf4b6f7d0b5f590a8ddfa64e69190
ms.sourcegitcommit: dbde4aed5a3188d6b4244ff7220f2f75fce65ada
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/19/2019
ms.locfileid: "74184733"
---
# <a name="change-streams-in-azure-cosmos-dbs-api-for-mongodb"></a>Azure Cosmos DB の MongoDB 用 API での変更ストリーム

Azure Cosmos DB の MongoDB 用 API での[変更フィード](change-feed.md)のサポートは、変更ストリーム API を使用することで利用できます。 アプリケーションで変更ストリーム API を使用すると、単一のシャード内のコレクションまたは項目に対して行われた変更を取得できます。 その後、結果に基づいてさらにアクションを実行できます。 コレクション内の項目に対する変更は、変更時刻の順序でキャプチャされ、並べ替え順序はシャード キーごとに保証されます。

次の例では、コレクション内のすべての項目で変更ストリームを取得する方法を示します。 この例では、項目の挿入、更新、置換を監視するためのカーソルを作成します。 変更ストリームを取得するには、$match ステージ、$project ステージ、および fullDocument オプションが必要です。 変更ストリームを使用した削除操作の監視は、現在はサポートされていません。 回避策として、削除される項目にソフト マーカーを追加できます。 たとえば、項目に "deleted" という名前の属性を追加して "true" に設定し、項目に TTL を設定すると、自動的に削除したり、追跡したりできます。

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

次の例では、1 つのシャード内の項目に対する変更を取得する方法を示します。 この例では、シャード キーが "a" で、シャード キー値が "1" の項目の変更を取得します。

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

## <a name="current-limitations"></a>現時点での制限事項

変更ストリームを使用する場合、次の制限が適用されます。

* `operationType` および `updateDescription` プロパティは、出力ドキュメントではまだサポートされていません。
* `insert`、`update`、`replace` の操作の種類は、現在サポートされています。 削除操作またはその他のイベントは、まだサポートされていません。

これらの制限により、前の例で示したように、$match ステージ、$project ステージ、および fullDocument オプションが必要になります。

## <a name="error-handling"></a>エラー処理

変更ストリームを使用するときは、次のエラー コードとメッセージがサポートされます。

* **HTTP エラー コード 429** - 変更ストリームが調整されると、空のページが返されます。

* **NamespaceNotFound (OperationType 無効化)** - 存在しないコレクションで変更ストリームを実行した場合、またはコレクションが削除された場合は、`NamespaceNotFound` エラーが返されます。 出力ドキュメントでは `operationType` プロパティを返すことができないため、`operationType Invalidate` エラーの代わりに、`NamespaceNotFound` エラーが返されます。

## <a name="next-steps"></a>次の手順

* [Time to Live を使用して Azure Cosmos DB の MongoDB 用 API でデータを自動的に期限切れにする](mongodb-time-to-live.md)
* [Azure Cosmos DB の MongoDB 用 API でのインデックス作成](mongodb-indexing.md)