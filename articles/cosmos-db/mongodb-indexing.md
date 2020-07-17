---
title: Azure Cosmos DB の MongoDB 用 API でのインデックス作成を管理する
description: この記事では、MongoDB API を使用した Azure Cosmos DB インデックス作成機能の概要を示します。
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.devlang: nodejs
ms.topic: conceptual
ms.date: 04/03/2020
author: timsander1
ms.author: tisande
ms.openlocfilehash: fd602f88acf26e821e57e0a844f543aac08dad0d
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/28/2020
ms.locfileid: "81732702"
---
# <a name="manage-indexing-in-azure-cosmos-dbs-api-for-mongodb"></a>Azure Cosmos DB の MongoDB 用 API でのインデックス作成を管理する

Azure Cosmos DB の MongoDB 用 API では、Azure Cosmos DB のコア インデックス管理機能を利用します。 この記事では、Azure Cosmos DB の MongoDB 用 API を使用してインデックスを追加する方法を重点的に取り上げます。 また、すべての API に関連する [Azure Cosmos DB のインデックス作成の概要](index-overview.md)もご一読ください。

## <a name="indexing-for-mongodb-server-version-36"></a>MongoDB サーバー バージョン 3.6 のインデックス作成

Azure Cosmos DB の MongoDB サーバー バージョン 3.6 用 API では、`_id` フィールドのインデックスが自動的に作成され、これを削除することはできません。 シャード キーごとに `_id` フィールドの一意性が自動的に適用されます。

その他のフィールドのインデックスを作成するには、MongoDB インデックス管理コマンドを適用します。 MongoDB の場合と同様に、Azure Cosmos DB の MongoDB 用 API では、`_id` フィールドのインデックスのみが自動的に作成されます。 この既定のインデックス作成ポリシーは Azure Cosmos DB の SQL API とは異なり、既定ですべてのフィールドのインデックスが作成されます。

クエリに並べ替えを適用するには、並べ替え操作で使用されるフィールドに対してインデックスを作成する必要があります。

## <a name="index-types"></a>インデックスの種類

### <a name="single-field"></a>単一フィールド

単一フィールドに対してインデックスを作成できます。 単一フィールドのインデックスの並べ替え順序は重要ではありません。 次のコマンドでは、フィールド `name` に対してインデックスが作成されます。

`db.coll.createIndex({name:1})`

1 つのクエリで、使用可能な場合は複数の単一フィールドのインデックスが使用されます。 コンテナーごとに最大 500 の単一フィールド インデックスを作成できます。

### <a name="compound-indexes-mongodb-server-version-36"></a>複合インデックス (MongoDB サーバー バージョン 3.6)

Azure Cosmos DB の MongoDB 用 API では、バージョン 3.6 のワイヤ プロトコルを使用するアカウントの複合インデックスがサポートされます。 複合インデックスには、最大 8 個のフィールドを含めることができます。 MongoDB とは異なり、複合インデックスを作成する必要があるのは、一度に複数のフィールドに対してクエリを効率的に並べ替える必要がある場合のみです。 並べ替える必要がない、複数のフィルターを使用するクエリの場合は、単一の複合インデックスではなく、複数の単一フィールド インデックスを作成します。

次のコマンドでは、フィールド `name` と `age` に対して複合インデックスを作成します。

`db.coll.createIndex({name:1,age:1})`

複合インデックスを使用すると、次の例に示すように、一度に複数のフィールドで効率的に並べ替えることができます。

`db.coll.find().sort({name:1,age:1})`

上記の複合インデックスを使用して、すべてのフィールドに対して逆の並べ替え順序でクエリを効率的に並べ替えることもできます。 次に例を示します。

`db.coll.find().sort({name:-1,age:-1})`

ただし、複合インデックス内のパスの順序は、クエリと完全に一致している必要があります。 追加の複合インデックスを必要とするクエリの例を次に示します。

`db.coll.find().sort({age:1,name:1})`

### <a name="multikey-indexes"></a>複数キー インデックス

Azure Cosmos DB では、配列に格納されているコンテンツにインデックスを作成するために、複数のキー インデックスが作成されます。 配列値を含むフィールドのインデックスを作成すると、Azure Cosmos DB によって配列内のすべての要素のインデックスが自動的に作成されます。

### <a name="geospatial-indexes"></a>地理空間のインデックス

多くの地理空間演算子は、地理空間インデックスの恩恵を受けます。 現在、Azure Cosmos DB の MongoDB 用 API では、`2dsphere` インデックスがサポートされています。 API ではまだ `2d` インデックスはサポートされていません。

`location` フィールドに対して地理空間インデックスを作成する例を以下に示します。

`db.coll.createIndex({ location : "2dsphere" })`

### <a name="text-indexes"></a>テキスト インデックス

Azure Cosmos DB の MongoDB 用 API では、現在、テキスト インデックスはサポートされていません。 文字列に対するテキスト検索クエリの場合は、Azure Cosmos DB との [Azure Cognitive Search](https://docs.microsoft.com/azure/search/search-howto-index-cosmosdb) の統合を使用する必要があります。

## <a name="index-properties"></a>インデックスのプロパティ

次の操作は、ワイヤ プロトコル バージョン 3.6 に対応するアカウントと、以前のバージョンに対応するアカウントに共通です。 詳細については、[サポートされているインデックスとインデックス付きプロパティ](mongodb-feature-support-36.md#indexes-and-index-properties)を参照してください。

### <a name="unique-indexes"></a>一意なインデックス

[一意なインデックス](unique-keys.md) は、複数のドキュメントのインデックス付きフィールドで、同じ値が確実に含まれないようにする場合に便利です。

> [!IMPORTANT]
> 一意なインデックスを作成できるのは、コレクションが空である (ドキュメントが含まれていない) ときに限られます。

次のコマンドでは、フィールド `student_id` に対して一意なインデックスを作成します。

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

シャード コレクションの場合、一意なインデックスを作成するには、シャード (パーティション) キーを指定する必要があります。 つまり、シャード コレクション上の一意なインデックスはすべて、いずれかのフィールドがパーティション キーである複合インデックスとなります。

以下のコマンドでは、フィールド `student_id` と `university` に対する一意なインデックスがあるシャード コレクション```coll``` (シャード キーは ```university```) を作成します。

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

前の例では、```"university":1``` 句を省略すると、次のメッセージを含むエラーが返されます。

```"cannot create unique index over {student_id : 1.0} with shard key pattern { university : 1.0 }"```

### <a name="ttl-indexes"></a>TTL インデックス

特定のコレクションでドキュメントの有効期限を有効にするには、[TTL (Time-to-Live) インデックス](../cosmos-db/time-to-live.md)を作成する必要があります。 TTL インデックスは、`expireAfterSeconds` 値を持つ `_ts` フィールドのインデックスです。

例:

```JavaScript
globaldb:PRIMARY> db.coll.createIndex({"_ts":1}, {expireAfterSeconds: 10})
```

上記のコマンドでは、過去 10 秒間、変更されなかったドキュメントが ```db.coll``` コレクションからすべて削除されます。

> [!NOTE]
> **_ts** フィールドは Azure Cosmos DB に固有のものであり、MongoDB クライアントからアクセスすることはできません。 これは予約された (システム) プロパティであり、ドキュメントの最終変更日時のタイム スタンプが含まれます。

## <a name="track-index-progress"></a>インデックスの進行状況を追跡する

Azure Cosmos DB の MongoDB 用 API のバージョン 3.6 では、データベース インスタンスでのインデックス作成の進行状況を追跡する `currentOp()` コマンドがサポートされます。 このコマンドでは、データベース インスタンスで進行中の操作に関する情報を含むドキュメントが返されます。 ネイティブ MongoDB で進行中のすべての操作を追跡するには、`currentOp` コマンドを使用します。 Azure Cosmos DB の MongoDB 用 API では、このコマンドでサポートされるのは、インデックス操作の追跡のみとなります。

以下に、`currentOp` コマンドを使用してインデックス作成の進行状況を追跡する方法を示す例をいくつか示します。

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

### <a name="examples-of-index-progress-output"></a>インデックスの進行状況の出力例

インデックスの進行状況の詳細には、現在のインデックス操作の進行状況のパーセンテージが表示されます。 以下は、インデックスの進行状況のさまざまな段階での出力ドキュメントの形式を示す例です。

- 60% 完了した "foo" コレクションと "bar" データベースでのインデックス操作では、出力ドキュメントは次のようになります。 `Inprog[0].progress.total` フィールドには、ターゲットの完了パーセンテージとして 100 が示されています。

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

- インデックス操作が "foo" コレクションと "bar" データベースで開始されたばかりの場合、出力ドキュメントの進行状況は、測定可能なレベルに到達するまで 0% と表示されることがあります。

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

- 進行中のインデックス操作が終了すると、出力ドキュメントには空の `inprog` 操作が表示されます。

   ```json
   {
      "inprog" : [],
      "ok" : 1
   }
   ```

### <a name="background-index-updates"></a>バックグラウンドでのインデックスの更新

**background** インデックス プロパティに指定された値に関係なく、インデックスの更新は常にバックグラウンドで実行されます。 インデックスの更新では他のデータベース操作よりも優先順位が低い要求ユニット (RU) が使用されるため、インデックスの変更によって書き込み、更新、削除のダウンタイムが発生することはありません。

新しいインデックスを追加すると、クエリですぐにそのインデックスが使用されます。 つまり、クエリから一致するすべての結果が返されない場合があり、その際にエラーが返されることはありません。 インデックス変換が完了すると、クエリ結果の一貫性が保たれます。 [インデックスの進行状況を追跡する](#track-index-progress)ことができます。

## <a name="migrate-collections-with-indexes"></a>インデックス付きのコレクションを移行する

現在、一意なインデックスを作成できるのは、コレクションにドキュメントが含まれていない場合のみです。 広く使われている MongoDB 移行ツールでは、データのインポート後に一意なインデックスの作成が試行されます。 この問題を回避するために、移行ツールでの試行を許可するのではなく、対応するコレクションと一意なインデックスを手動で作成できます  (コマンド ラインで `--noIndexRestore` フラグを使用することで、```mongorestore``` に対してこの動作を実現できます)。

## <a name="indexing-for-mongodb-version-32"></a>MongoDB バージョン 3.2 のインデックス作成

バージョン 3.2 の MongoDB ワイヤ プロトコルと互換性のある Azure Cosmos アカウントの場合、使用可能なインデックス作成機能と既定値は異なります。 [アカウントのバージョンを確認する](mongodb-feature-support-36.md#protocol-support)ことができます。 [サポート リクエスト](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)を提出することで、3.6 バージョンにアップグレードできます。

バージョン 3.2 をご利用の場合は、このセクションのバージョン 3.6 との主な違いの概要を参照してください。

### <a name="dropping-default-indexes-version-32"></a>既定のインデックスの削除 (バージョン 3.2)

3\.6 バージョンの Azure Cosmos DB の MongoDB 用 API とは異なり、バージョン 3.2 では既定ですべてのプロパティのインデックスが作成されます。 コレクション ```coll``` のこれらの既定のインデックスを削除する場合は、次のコマンドを使用できます。

```JavaScript
> db.coll.dropIndexes()
{ "_t" : "DropIndexesResponse", "ok" : 1, "nIndexesWas" : 3 }
```

既定のインデックスを削除した後、バージョン 3.6 の場合と同じようにインデックスをさらに追加することができます。

### <a name="compound-indexes-version-32"></a>複合インデックス (バージョン 3.2)

複合インデックスは、ドキュメントの複数のフィールドに対する参照を保持します。 複合インデックスを作成する場合は、[サポート要求](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)を提出して、バージョン 3.6 にアップグレードします。

## <a name="next-steps"></a>次のステップ

* [Azure Cosmos DB のインデックス作成](../cosmos-db/index-policy.md)
* [Time to Live を使用して Azure Cosmos DB のデータの有効期限が自動的に切れるようにする](../cosmos-db/time-to-live.md)
