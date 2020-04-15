---
title: Azure Cosmos DB の MongoDB 用 API でのインデックス作成
description: Azure Cosmos DB の MongoDB 用 API のインデックス作成機能の概要を示します。
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.devlang: nodejs
ms.topic: conceptual
ms.date: 04/03/2020
author: timsander1
ms.author: tisande
ms.openlocfilehash: f3f369928270c77557337bfdb1037cc5174c39f2
ms.sourcegitcommit: 0450ed87a7e01bbe38b3a3aea2a21881f34f34dd
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/03/2020
ms.locfileid: "80637960"
---
# <a name="indexing-using-azure-cosmos-dbs-api-for-mongodb"></a>Azure Cosmos DB の MongoDB 用 API を使用するインデックス作成

Azure Cosmos DB の MongoDB 用 API では、Azure Cosmos DB のコア インデックス管理機能を利用します。 このドキュメントでは、Azure Cosmos DB の MongoDB 用 API を使用してインデックスを追加する方法を重点的に取り上げます。 また、すべての API に関連する [Azure Cosmos DB のインデックス作成の概要](index-overview.md)もご一読ください。

## <a name="indexing-for-version-36"></a>バージョン 3.6 用のインデックス作成

`_id` フィールドは常にインデックスが自動的に作成され、削除することはできません。 Azure Cosmos DB の MongoDB 用 API では、シャード キーごとに `_id` フィールドの一意性が自動的に適用されます。

その他のフィールドのインデックスを作成するには、MongoDB インデックス管理コマンドを適用する必要があります。 MongoDB と同様に、自動的にインデックスが作成されるのは `_id` フィールドのみです。 この既定のインデックス作成ポリシーは Azure Cosmos DB の SQL API とは異なり、すべてのフィールドに既定でインデックスが作成されます。

クエリに並べ替えを適用するには、並べ替え操作で使用されるフィールドに関してインデックスを作成する必要があります。

## <a name="index-types"></a>インデックスの種類

### <a name="single-field"></a>単一フィールド

単一フィールドに対してインデックスを作成できます。 単一フィールドのインデックスの並べ替え順序は重要ではありません。 次のコマンドでは、フィールド `name` に対してインデックスが作成されます。

`db.coll.createIndex({name:1})`

1 つのクエリで、使用可能な場合は複数の単一フィールドのインデックスが使用されます。 コンテナーごとに最大 500 の単一フィールド インデックスを作成できます。

### <a name="compound-indexes-36"></a>複合インデックス (3.6)

複合インデックスは、3.6 ワイヤ プロトコルを使用するアカウントでサポートされます。 複合インデックスには、最大 8 個のフィールドを含めることができます。 MongoDB とは異なり、複合インデックスを作成する必要があるのは、一度に複数のフィールドに対してクエリを効率的に並べ替える必要がある場合のみです。 複数のフィルターを使用するクエリの場合は、1 つの複合インデックスではなく、複数のフィールドのインデックスを作成する必要があります。

次のコマンドは、フィールド `name` と `age` に関する複合インデックスを作成します。

`db.coll.createIndex({name:1,age:1})`

複合インデックスを使用すると、次のように、複数のフィールドに関して直ちに効率的に並べ替えできます。

`db.coll.find().sort({name:1,age:1})`

上記の複合インデックスを使用して、すべてのフィールドに対して逆の並べ替え順序でクエリの効率的な並べ替えを行うこともできます。 次に例を示します。

`db.coll.find().sort({name:-1,age:-1})`

ただし、複合インデックス内のパスの順序は、クエリと完全に一致している必要があります。 追加の複合インデックスを必要とするクエリの例を次に示します。

`db.coll.find().sort({age:1,name:1})`

### <a name="multikey-indexes"></a>複数キー インデックス

Azure Cosmos DB では、配列に格納されているコンテンツにインデックスを作成するために、複数のキー インデックスが作成されます。 配列値を含むフィールドのインデックスを作成すると、Azure Cosmos DB によって配列内のすべての要素のインデックスが自動的に作成されます。

### <a name="geospatial-indexes"></a>地理空間のインデックス

多くの地理空間演算子は、地理空間インデックスの恩恵を受けます。 現在、Azure Cosmos DB の MongoDB 用 API では、`2dsphere` インデックスがサポートされています。 `2d` インデックスはまだサポートされていません。

`location` フィールドに対して地理空間インデックスを作成する例を次に示します。

`db.coll.createIndex({ location : "2dsphere" })`

### <a name="text-indexes"></a>テキスト インデックス

テキスト インデックスは現在はサポートされていません。 文字列に対するテキスト検索クエリの場合は、Azure Cosmos DB との [Azure Cognitive Search](https://docs.microsoft.com/azure/search/search-howto-index-cosmosdb) の統合を使用する必要があります。

## <a name="index-properties"></a>インデックスのプロパティ

次の操作は、ワイヤ プロトコル バージョン 3.6 に対応するアカウントと以前のワイヤ プロトコル バージョンに対応するアカウントの両方について共通です。 詳細については、[サポートされているインデックスとインデックス付きプロパティ](mongodb-feature-support-36.md#indexes-and-index-properties)も参照してください。

### <a name="unique-indexes"></a>一意なインデックス

[一意なインデックス](unique-keys.md)を使用すると、インデックス付けされたフィールドに関して、同じ値を含んだドキュメントが複数存在することを確実に回避できます。

>[!Important]
> 一意なインデックスを作成できるのは、コレクションが空である (ドキュメントが含まれていない) ときに限られます。

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

シャード コレクションの場合、一意なインデックスを作成するためには、シャード (パーティション) キーを指定する必要があります。 つまり、シャード コレクション上の一意なインデックスはすべて、いずれかのフィールドがパーティション キーである複合インデックスとなります。

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

### <a name="ttl-indexes"></a>TTL インデックス

特定のコレクションにおいてドキュメントの有効期限を有効にするには、["TTL インデックス" (Time to Live インデックス)](../cosmos-db/time-to-live.md) を作成する必要があります。 TTL インデックスは、"expireAfterSeconds" を値として持つ _ts フィールドに対するインデックスです。

例:

```JavaScript
globaldb:PRIMARY> db.coll.createIndex({"_ts":1}, {expireAfterSeconds: 10})
```

前出のコマンドでは、過去 10 秒間、変更されなかったドキュメントが ```db.coll``` コレクションからすべて削除されます。

> [!NOTE]
> **_ts** は Azure Cosmos DB に固有のフィールドであり、MongoDB クライアントからアクセスすることはできません。 予約された (システム) プロパティであり、ドキュメントの最終変更日時のタイムスタンプを含んでいます。

## <a name="track-the-index-progress"></a>インデックスの進行状況を追跡する

MongoDB アカウント用の Azure Cosmos DB の API 3.6 バージョンでは、データベース インスタンスでのインデックス作成の進行状況を追跡する `currentOp()` コマンドがサポートされます。 このコマンドでは、データベース インスタンスで進行中の操作に関する情報を含むドキュメントが返されます。 `currentOp` コマンドは、ネイティブ MongoDB では進行中のすべての操作の追跡に使用されます。それに対し、Azure Cosmos DB の MongoDB 用 API では、このコマンドはインデックス操作の追跡のみがサポートされます。

以下に、`currentOp` コマンドを使用してインデックス作成の進行状況を追跡する方法を示す例をいくつか紹介します。

* コレクションに対するインデックスの進行状況を取得します。

   ```shell
   db.currentOp({"command.createIndexes": <collectionName>, "command.$db": <databaseName>})
   ```

* データベース内のすべてのコレクションに対するインデックスの進行状況を取得します。

  ```shell
  db.currentOp({"command.$db": <databaseName>})
  ```

* Azure Cosmos アカウント内のすべてのデータベースとコレクションに対するインデックスの進行状況を取得します。

  ```shell
  db.currentOp({"command.createIndexes": { $exists : true } })
  ```

インデックスの進行状況の詳細には、現在のインデックス操作の進行状況のパーセンテージが含まれます。 次の例では、インデックスの進行状況のさまざまな段階での出力ドキュメントの形式を示します。

1. インデックス作成が 60% 完了した 'foo' コレクションと 'bar' データベースでのインデックス操作では、出力ドキュメントは次のようになります。 `Inprog[0].progress.total` は、目標の完了率として 100 を示しています。

   ```json
   {
        "inprog" : [
        {
                ………………...
                "command" : {
                        "createIndexes" : foo
                        "indexes" :[ ],
                        "$db" : bar
                },
                "msg" : "Index Build (background) Index Build (background): 60 %",
                "progress" : {
                        "done" : 60,
                        "total" : 100
                },
                …………..…..
        }
        ],
        "ok" : 1
   }
   ```

2. 'foo' コレクションと 'bar' データベースで開始されたばかりのインデックス操作の場合、出力ドキュメントの進行状況は、測定可能なレベルに到達するまで 0% と表示されることがあります。

   ```json
   {
        "inprog" : [
        {
                ………………...
                "command" : {
                        "createIndexes" : foo
                        "indexes" :[ ],
                        "$db" : bar
                },
                "msg" : "Index Build (background) Index Build (background): 0 %",
                "progress" : {
                        "done" : 0,
                        "total" : 100
                },
                …………..…..
        }
        ],
       "ok" : 1
   }
   ```

3. 進行中のインデックス操作が完了すると、出力ドキュメントには空の inprog 操作が表示されます。

   ```json
   {
      "inprog" : [],
      "ok" : 1
   }
   ```

### <a name="background-index-updates"></a>バックグラウンドでのインデックスの更新

**background** インデックス プロパティに指定された値に関係なく、インデックスの更新は常にバックグラウンドで実行されます。 インデックスの更新では、他のデータベース操作よりも優先順位が低い RU が使用されます。 そのため、インデックスの変更によって、書き込み、更新、または削除のダウンタイムは発生しません。

新しいインデックスを追加すると、クエリによってすぐにそのインデックスが使用されます。 つまり、一致するすべての結果がクエリで返らない場合があり、その際にエラーが返されることはありません。 インデックス変換が完了すると、クエリ結果に一貫性が確保されます。 [インデックスの進行状況を追跡する](#track-the-index-progress)ことができます。

## <a name="migrating-collections-with-indexes"></a>インデックスを伴うコレクションの移行

現在、一意なインデックスを作成できるのは、コレクションにドキュメントが含まれていないときに限られます。 広く使われている MongoDB 移行ツールはいずれも、データのインポート後に一意なインデックスを作成しようと試みます。 この問題を回避するために、移行ツールに任せるのではなく、ユーザーが対応するコレクションと一意のインデックスを手動で作成することをお勧めします (```mongorestore``` の場合、この動作は、コマンド ラインで `--noIndexRestore` フラグを使用することによって実現されます)。

## <a name="indexing-for-version-32"></a>バージョン 3.2 用のインデックス作成

3\.2 バージョンの MongoDB ワイヤ プロトコルと互換性のある Azure Cosmos DB アカウントの場合、使用可能なインデックス作成機能と既定値は異なります。 [アカウントのバージョンを確認する](mongodb-feature-support-36.md#protocol-support)ことができます。 [サポート リクエスト](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)を提出することで、3.6 バージョンにアップグレードできます。

バージョン 3.2 を使用する場合は、このセクションでバージョン 3.6 との主な違いについて概説します。

### <a name="dropping-the-default-indexes-32"></a>既定のインデックスの削除 (3.2)

3\.6 バージョンの Azure Cosmos DB の MongoDB 用 API とは異なり、3.2 バージョンでは既定ですべてのプロパティのインデックスが作成されます。 コレクション ```coll``` に使用されるこれらの既定のインデックスを削除するには、次のコマンドを使用します。

```JavaScript
> db.coll.dropIndexes()
{ "_t" : "DropIndexesResponse", "ok" : 1, "nIndexesWas" : 3 }
```

既定のインデックスを削除した後は、バージョン 3.6 のように追加のインデックスを追加できます。

### <a name="compound-indexes-32"></a>複合インデックス (3.2)

複合インデックスは、ドキュメントの複数のフィールドに対する参照を保持します。 複合インデックスを作成する場合は、[サポート リクエスト](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)を提出して、3.6 バージョンにアップグレードしてください。

## <a name="next-steps"></a>次のステップ

* [Azure Cosmos DB のインデックス作成](../cosmos-db/index-policy.md)
* [Time to Live を使用して Azure Cosmos DB のデータの有効期限が自動的に切れるようにする](../cosmos-db/time-to-live.md)
