---
title: Azure Cosmos DB MongoDB API でのインデックス作成 | Microsoft Docs
description: Azure Cosmos DB MongoDB API のインデックス作成機能についての概要を紹介します。
services: cosmos-db
author: orestis-ms
manager: kfile
editor: ''
ms.service: cosmos-db
ms.component: cosmosdb-mongo
ms.devlang: nodejs
ms.topic: conceptual
ms.date: 03/01/2018
ms.author: orkostak
ms.openlocfilehash: d0c74bd2ba7a4a77a16d481ddb3b76f90a923254
ms.sourcegitcommit: df50934d52b0b227d7d796e2522f1fd7c6393478
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/12/2018
ms.locfileid: "38989511"
---
# <a name="indexing-in-the-azure-cosmos-db-mongodb-api"></a>Azure Cosmos DB MongoDB API でのインデックス作成

Azure Cosmos DB MongoDB API には、Azure Cosmos DB の自動インデックス管理機能が利用されています。 したがってユーザーは、Azure Cosmos DB の既定のインデックス作成ポリシーを利用することができます。 つまりユーザーによってインデックスが定義されなかった場合や、いずれのインデックスも削除されなかった場合、特に何もしなくても、すべてのフィールドは、コレクションに挿入された時点で、インデックスが自動的に作成されます。 ほとんどのシナリオでは、アカウントに設定されている既定のインデックス作成ポリシーの使用をお勧めします。

## <a name="dropping-the-default-indexes"></a>既定のインデックスの削除

コレクション ```coll``` に使用される既定のインデックスを削除するには、次のコマンドを使用します。

```JavaScript
> db.coll.dropIndexes()
{ "_t" : "DropIndexesResponse", "ok" : 1, "nIndexesWas" : 3 }
```

## <a name="creating-compound-indexes"></a>複合インデックスの作成

複合インデックスは、ドキュメントの複数のフィールドに対する参照を保持します。 論理上は、フィールドごとに異なる複数のインデックスを作成することに相当します。 Cosmos DB のインデックス作成手法から得られる最適化の利点を活かすために、単一 (非一意性) の複合インデックスを作成するのではなく、複数の個別インデックスを作成するようお勧めします。

## <a name="creating-unique-indexes"></a>一意なインデックスの作成

[一意なインデックス](unique-keys.md)を使用すると、インデックス付けされたフィールドに関して、同じ値を含んだドキュメントが複数存在することを確実に回避できます。 
>[!important] 
> 現在、一意なインデックスを作成できるのは、コレクションが空である (ドキュメントが含まれていない) ときに限られます。 

次のコマンドは、フィールド "student_id" に対して一意なインデックスを作成しています。

```JavaScript
globaldb:PRIMARY> db.coll.createIndex( { "student_id" : 1 }, {unique:true} ) 
{
        "_t" : "CreateIndexesResponse",
        "ok" : 1,
        "createdCollectionAutomatically" : false,
        "numIndexesBefore" : 1,
        "numIndexesAfter" : 4
}
```

シャード コレクションの場合、MongoDB の動作上、一意なインデックスを作成するためには、シャード (パーティション) キーを指定する必要があります。 つまり、シャード コレクション上の一意なインデックスはすべて、いずれかのフィールドがパーティション キーである複合インデックスとなります。

以下のコマンドでは、student_id フィールドと university フィールドに対する一意なインデックスを持ったシャード コレクション```coll``` (シャード キーは ```university```) を作成します。

```JavaScript
globaldb:PRIMARY> db.runCommand({shardCollection: db.coll._fullName, key: { university: "hashed"}});
{
        "_t" : "ShardCollectionResponse",
        "ok" : 1,
        "collectionsharded" : "test.coll"
}
globaldb:PRIMARY> db.coll.createIndex( { "student_id" : 1, "university" : 1 }, {unique:true})
{
        "_t" : "CreateIndexesResponse",
        "ok" : 1,
        "createdCollectionAutomatically" : false,
        "numIndexesBefore" : 3,
        "numIndexesAfter" : 4
}
```

上の例で、仮に ```"university":1``` 句を省略した場合、エラーが発生して次のメッセージが返されます。

```"cannot create unique index over {student_id : 1.0} with shard key pattern { university : 1.0 }"```

## <a name="ttl-indexes"></a>TTL インデックス

特定のコレクションにおいてドキュメントの有効期限を有効にするには、["TTL インデックス" (Time to Live インデックス)](../cosmos-db/time-to-live.md) を作成する必要があります。 TTL インデックスは、"expireAfterSeconds" を値として持つ _ts フィールドに対するインデックスです。
 
例:
```JavaScript
globaldb:PRIMARY> db.coll.createIndex({"_ts":1}, {expireAfterSeconds: 10})
```

前出のコマンドでは、過去 10 秒間、変更されなかったドキュメントが ```db.coll``` コレクションからすべて削除されます。 
 
> [!NOTE]
> **_ts** は Cosmos DB に固有のフィールドであり、MongoDB クライアントからアクセスすることはできません。 予約された (システム) プロパティであり、ドキュメントの最終変更日時のタイムスタンプを含んでいます。
>

## <a name="migrating-collections-with-indexes"></a>インデックスを伴うコレクションの移行

現在、一意なインデックスを作成できるのは、コレクションにドキュメントが含まれていないときに限られます。 広く使われている MongoDB 移行ツールはいずれも、データのインポート後に一意なインデックスを作成しようと試みます。 この問題を回避するためには、移行ツールに委ねることはせずに、対応するコレクションと一意なインデックスをユーザーが手動で作成することをお勧めします (```mongorestore``` の場合、この動作を実現するには、コマンド ラインで --noIndexRestore フラグを使用します)。

## <a name="next-steps"></a>次の手順
* [Azure Cosmos DB のデータ インデックス作成のしくみ](../cosmos-db/indexing-policies.md)
* [TTL (Time to Live) を使って Azure Cosmos DB コレクションのデータの有効期限が自動的に切れるようにする](../cosmos-db/time-to-live.md)
