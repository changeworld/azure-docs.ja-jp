---
title: Azure Cosmos DB の MongoDB 用 API でのインデックス作成
description: Azure Cosmos DB の MongoDB 用 API のインデックス作成機能の概要を示します。
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.devlang: nodejs
ms.topic: conceptual
ms.date: 12/26/2018
author: sivethe
ms.author: sivethe
ms.openlocfilehash: e51e96c0c553bcf37284878cab11f3ec592ddd05
ms.sourcegitcommit: 8074f482fcd1f61442b3b8101f153adb52cf35c9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/22/2019
ms.locfileid: "72753389"
---
# <a name="indexing-using-azure-cosmos-dbs-api-for-mongodb"></a>Azure Cosmos DB の MongoDB 用 API を使用するインデックス作成

Azure Cosmos DB の MongoDB 用 API では、Cosmos DB の自動インデックス管理機能を利用します。 したがって、ユーザーは Cosmos DB の既定のインデックス作成ポリシーにアクセスすることができます。 そのため、ユーザーによってインデックスが定義されなかった場合や、いずれのインデックスも削除されなかった場合、すべてのフィールドは、コレクションに挿入された時点で、インデックスが自動的に作成されます。 ほとんどのシナリオでは、アカウントに設定されている既定のインデックス作成ポリシーの使用をお勧めします。

## <a name="indexing-for-version-36"></a>バージョン 3.6 用のインデックス作成

ワイヤ プロトコル バージョン 3.6 に対応するアカウントは、以前のバージョンで提供されていたポリシーとは異なる既定のインデックス作成ポリシーを提供します。 既定では、_id フィールドのインデックスのみが作成されます。 その他のフィールドのインデックスを作成するには、ユーザーが MongoDB インデックス管理コマンドを適用する必要があります。 クエリに並べ替えを適用するには、現在、並べ替え操作で使用されるフィールドに関してインデックスを作成する必要があります。

### <a name="dropping-the-default-indexes-36"></a>既定のインデックスの削除 (3.6)

ワイヤ プロトコル バージョン 3.6 に対応するアカウントの場合、既定のインデックスは _id のみであり、これは削除できません。

### <a name="creating-a-compound-index-36"></a>複合インデックスの作成 (3.6)

真の複合インデックスは、3.6 ワイヤ プロトコルを使用するアカウントでサポートされます。 次のコマンドは、フィールド 'a' と 'b' に関する複合インデックスを作成します。`db.coll.createIndex({a:1,b:1})`

複合インデックスを使用すると、次のように、複数のフィールドに関して直ちに効率的に並べ替えできます。`db.coll.find().sort({a:1,b:1})`

## <a name="indexing-for-version-32"></a>バージョン 3.2 用のインデックス作成

### <a name="dropping-the-default-indexes-32"></a>既定のインデックスの削除 (3.2)

コレクション ```coll``` に使用される既定のインデックスを削除するには、次のコマンドを使用します。

```JavaScript
> db.coll.dropIndexes()
{ "_t" : "DropIndexesResponse", "ok" : 1, "nIndexesWas" : 3 }
```

### <a name="creating-a-compound-index-32"></a>複合インデックスの作成 (3.2)

複合インデックスは、ドキュメントの複数のフィールドに対する参照を保持します。 論理上は、フィールドごとに異なる複数のインデックスを作成することに相当します。 Cosmos DB のインデックス作成手法から得られる最適化の利点を活かすために、単一 (非一意性) の複合インデックスを作成するのではなく、複数の個別インデックスを作成するようお勧めします。

## <a name="common-indexing-operations"></a>共通のインデックス作成操作

次の操作は、ワイヤ プロトコル バージョン 3.6 に対応するアカウントと以前のワイヤ プロトコル バージョンに対応するアカウントの両方について共通です。 

## <a name="creating-unique-indexes"></a>一意なインデックスの作成

[一意なインデックス](unique-keys.md)を使用すると、インデックス付けされたフィールドに関して、同じ値を含んだドキュメントが複数存在することを確実に回避できます。

>[!Important]
> 現在、一意なインデックスを作成できるのは、コレクションが空である (ドキュメントが含まれていない) ときに限られます。

次のコマンドは、フィールド "student_id" に対して一意なインデックスを作成しています。

```shell
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

```shell
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

## <a name="migrating-collections-with-indexes"></a>インデックスを伴うコレクションの移行

現在、一意なインデックスを作成できるのは、コレクションにドキュメントが含まれていないときに限られます。 広く使われている MongoDB 移行ツールはいずれも、データのインポート後に一意なインデックスを作成しようと試みます。 この問題を回避するために、移行ツールに任せるのではなく、ユーザーが対応するコレクションと一意のインデックスを手動で作成することをお勧めします (```mongorestore``` の場合、この動作は、コマンド ラインで `--noIndexRestore` フラグを使用することによって実現されます)。

## <a name="next-steps"></a>次の手順

* [Azure Cosmos DB のインデックス作成](../cosmos-db/index-policy.md)
* [Time to Live を使用して Azure Cosmos DB のデータの有効期限が自動的に切れるようにする](../cosmos-db/time-to-live.md)
