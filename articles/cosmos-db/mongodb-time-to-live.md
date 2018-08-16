---
title: MongoDB API を使用して作成された Azure Cosmos DB ドキュメントの Time to Live 値を設定して、一定期間経過後にシステムからドキュメントを自動的に消去する方法について説明します。
description: MongoDB のドキュメントごとの TTL 機能に関するドキュメント。
services: cosmos-db
author: orestis-ms
manager: kfile
editor: ''
ms.service: cosmos-db
ms.devlang: javascript
ms.topic: quickstart
ms.date: 08/10/2018
ms.author: orkostak
ms.openlocfilehash: 0bf8891e28897321dbfbad4486dd11c0b5ee4264
ms.sourcegitcommit: 17fe5fe119bdd82e011f8235283e599931fa671a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/11/2018
ms.locfileid: "40043157"
---
# <a name="expire-data-in-azure-cosmos-db-mongodb-api"></a>Azure Cosmos DB MongoDB API でデータを期限切れにする

Time to Live (TTL) 機能により、データベースはデータを自動的に期限切れにすることができます。 MongoDB API では、Azure Cosmos DB の TTL 機能を利用します。 コレクション全体に既定の TTL 値を設定するモードと、ドキュメントごとに個別の TTL 値を設定するモードの 2 つのモードがサポートされています。 MongoDB API における TTL インデックスとドキュメントごとの TTL 値を管理するロジックは、[Azure Cosmos DB](../cosmos-db/mongodb-indexing.md) におけるロジックと同じです。

## <a name="ttl-indexes"></a>TTL インデックス
コレクション全体に対して TTL を有効にするには、["TTL インデックス" (Time to Live インデックス)](../cosmos-db/mongodb-indexing.md) を作成する必要があります。 TTL インデックスは、"expireAfterSeconds" 値を含む _ts フィールドのインデックスです。

例:
```JavaScript
globaldb:PRIMARY> db.coll.createIndex({"_ts":1}, {expireAfterSeconds: 10})
{
        "_t" : "CreateIndexesResponse",
        "ok" : 1,
        "createdCollectionAutomatically" : true,
        "numIndexesBefore" : 1,
        "numIndexesAfter" : 4
}
```

上記の例のコマンドでは、TTL 機能を使用するインデックスを作成します。 インデックスが作成されると、データベースによって、そのコレクション内の、この 10 秒間に変更されていないドキュメントが自動的に削除されます。 

> [!NOTE]
> **_ts** は Cosmos DB に固有のフィールドであり、MongoDB クライアントからアクセスすることはできません。 予約された (システム) プロパティであり、ドキュメントの最終変更日時のタイムスタンプを含んでいます。
>
    
C# の例: 
```C# 
var options = new CreateIndexOptions {ExpireAfter = TimeSpan.FromSeconds(10)}; 
var field = new StringFieldDefinition<BsonDocument>("_ts"); 
var indexDefinition = new IndexKeysDefinitionBuilder<BsonDocument>().Ascending(field); 
await collection.Indexes.CreateOneAsync(indexDefinition, options); 
``` 

## <a name="set-time-to-live-value-for-a-document"></a>ドキュメントの Time to Live 値の設定 
ドキュメントごとの TTL 値もサポートされています。 ドキュメントには、ルート レベルのプロパティである "ttl" (小文字) が含まれている必要があります。また、前述の TTL インデックスがそのコレクションに対して作成されている必要があります。 ドキュメントに設定された TTL 値は、コレクションの TTL 値よりも優先されます。

TTL 値は int32 である必要があります。 または、int32 に収まる int64、あるいは int32 に収まる小数部を持たない double を指定します。 これらの仕様に準拠していない TTL プロパティ値を使用することもできますが、ドキュメントの有効な TTL 値としては扱われません。

ドキュメントの TTL 値は省略可能です。TTL 値のないドキュメントをコレクションに挿入できます。  この場合、コレクションの TTL 値が適用されます。 

次のドキュメントには有効な TTL 値があります。 ドキュメントが挿入されると、ドキュメントの TTL 値がコレクションの TTL 値よりも優先されます。 そのため、ドキュメントは 20 秒後に削除されます。  

```JavaScript 
globaldb:PRIMARY> db.coll.insert({id:1, location: "Paris", ttl: 20.0}) 
globaldb:PRIMARY> db.coll.insert({id:1, location: "Paris", ttl: NumberInt(20)}) 
globaldb:PRIMARY> db.coll.insert({id:1, location: "Paris", ttl: NumberLong(20)}) 
```

次のドキュメントには無効な TTL 値があります。 ドキュメントは挿入されますが、ドキュメントの TTL 値は適用されません。 そのため、コレクションの TTL 値が使用され、ドキュメントは 10 秒後に削除されます。 

```JavaScript 
globaldb:PRIMARY> db.coll.insert({id:1, location: "Paris", ttl: 20.5}) //TTL value contains non-zero decimal part. 
globaldb:PRIMARY> db.coll.insert({id:1, location: "Paris", ttl: NumberLong(2147483649)}) //TTL value is greater than Int32.MaxValue (2,147,483,648). 
``` 

## <a name="how-to-activate-the-per-document-ttl-feature"></a>ドキュメントごとの TTL 機能をアクティブにする方法
ドキュメントごとの TTL 機能は、Azure portal の MongoDB API アカウントの [プレビュー機能] タブでアクティブにすることができます。

![ポータルでのドキュメントごとの TTL 機能のアクティブ化を示すスクリーンショット](./media/mongodb-ttl/mongodb_portal_ttl.png) 

## <a name="next-steps"></a>次の手順
* [TTL (Time to Live) を使って Azure Cosmos DB コレクションのデータの有効期限が自動的に切れるようにする](../cosmos-db/time-to-live.md)
* [Azure Cosmos DB MongoDB API でのインデックス作成](../cosmos-db/mongodb-indexing.md)
