---
title: "Azure Cosmos DB チュートリアル: Apache TinkerPops Gremlin コンソールでの作成、クエリ、走査 | Microsoft Docs"
description: "Azure Cosmos DB Graph API を使用して頂点、辺、およびクエリを作成するための Azure Cosmos DB クイック スタート。"
services: cosmos-db
author: AndrewHoh
manager: jhubbard
editor: monicar
ms.assetid: bf08e031-718a-4a2a-89d6-91e12ff8797d
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: terminal
ms.topic: hero-article
ms.date: 05/19/2017
ms.author: anhoh
ms.translationtype: Human Translation
ms.sourcegitcommit: 300958a69fc854cb8db02120a383a4cbbfcacd7b
ms.openlocfilehash: caf3b69b25ccd15322054a0bbf95fc2a5816e00a
ms.contentlocale: ja-jp
ms.lasthandoff: 05/20/2017


---
# <a name="azure-cosmos-db-create-query-and-traverse-a-graph-in-the-gremlin-console"></a>Azure Cosmos DB: Gremlin コンソールでのグラフの作成、クエリ、および走査

Azure Cosmos DB は、Microsoft のグローバルに分散されたマルチモデル データベース サービスです。 Azure Cosmos DB の中核をなすグローバルな分散と水平方向のスケール機能を利用して、ドキュメント、キー/値、およびグラフ データベースをすばやく作成およびクエリできます。 

このクイック スタートでは、Azure Portal を使用して Azure Cosmos DB アカウント、データベース、グラフ (コンテナー) を作成してから、[Apache TinkerPop](http://tinkerpop.apache.org) で [Gremlin コンソール](https://tinkerpop.apache.org/docs/current/reference/#gremlin-console)を使用して Graph API (プレビュー) データを操作する方法を説明します。 このチュートリアルでは、頂点プロパティを更新しながら頂点と辺を作成およびクエリし、頂点をクエリし、グラフを走査し、頂点を削除します。

![Apache Gremlin コンソールからの Azure Cosmos DB](./media/create-graph-gremlin-console/gremlin-console.png)

Gremlin コンソールは Groovy/Java ベースであり、Linux、Mac、および Windows 上で実行します。 これは [Apache TinkerPop サイト](https://www.apache.org/dyn/closer.lua/tinkerpop/3.2.4/apache-tinkerpop-gremlin-console-3.2.4-bin.zip)からダウンロードできます。

## <a name="prerequisites"></a>前提条件

このクイック スタートの Azure Cosmos DB アカウントを作成するには、Azure サブスクリプションが必要です。

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[Gremlin コンソール](http://tinkerpop.apache.org/)もインストールする必要があります。 バージョン 3.2.4 以降を使用してください。

## <a name="create-a-database-account"></a>データベース アカウントの作成

[!INCLUDE [cosmos-db-create-dbaccount-graph](../../includes/cosmos-db-create-dbaccount-graph.md)]

## <a name="add-a-graph"></a>グラフの追加

[!INCLUDE [cosmos-db-create-graph](../../includes/cosmos-db-create-graph.md)]

## <a id="ConnectAppService"></a>App Service への接続
1. Gremlin コンソールを開始する前に、*apache-tinkerpop-gremlin-console-3.2.4/conf* ディレクトリで *remote-secure.yaml* 構成ファイルを作成または変更します。
2. *host*、*port*、*username*、*password*、*connectionPool*、および *serializer* の構成を入力します。

    設定|推奨値|説明
    ---|---|---
    ホスト|***.graphs.azure.com|Graph サービス URI。Azure Portal から取得できます
    ポート|443|443 に設定します
    ユーザー名|*自分のユーザー名*|フォーム `/dbs/<db>/colls/<coll>` のリソース。
    パスワード|*プライマリ マスター キー*|Azure Cosmos DB のプライマリ マスター キー
    接続プール|{enableSsl: true}|SSL 用の接続プールの設定
    シリアライザー|{ className:org.apache.tinkerpop.gremlin.<br>driver.ser.GraphSONMessageSerializerV1d0,<br> config: { serializeResultToString: true }}|この値に設定します

3. ターミナルで、*bin/gremlin.bat* または *bin/gremlin.sh* を実行して、[Gremlin コンソール](http://tinkerpop.apache.org/docs/3.2.4/tutorials/getting-started/)を起動します。
4. ターミナルで、*:remote connect tinkerpop.server conf/remote-secure.yaml* を実行して、App Service に接続します。

これでセットアップは終了です。 いくつかのコンソール コマンドの実行を開始しましょう。

## <a name="create-vertices-and-edges"></a>頂点と辺の作成

まずはじめに、*Thomas*、*Mary Kay*、*Robin*、および *Ben* の 4 つの人物頂点を追加しましょう。

入力 (Thomas):

```
:> g.addV('person').property('firstName', 'Thomas').property('lastName', 'Andersen').property('age', 44)
```

出力:

```
==>[id:1eb91f79-94d7-4fd4-b026-18f707952f21,label:person,type:vertex,properties:[firstName:[[id:ec5fcfbe-040e-48c3-b961-31233c8b1801,value:Thomas]],lastName:[[id:86e5b580-0bca-4bc2-bc53-a46f92c1a182,value:Andersen]],age:[[id:2caeab3c-c66d-4098-b673-40a8101bb72a,value:44]]]]
```
入力 (Mary Kay):

```
:> g.addV('person').property('firstName', 'Mary Kay').property('lastName', 'Andersen').property('age', 39)
```

出力:

```
==>[id:899a9d37-6701-48fc-b0a1-90950be7e0f4,label:person,type:vertex,properties:[firstName:[[id:c79c5599-8646-47d1-9a49-3456200518ce,value:Mary Kay]],lastName:[[id:c1362095-9dcc-479d-ab21-86c1b6d4ffc1,value:Andersen]],age:[[id:0b530408-bfae-4e8f-98ad-c160cd6e6a8f,value:39]]]]
```

入力 (Robin):

```
:> g.addV('person').property('firstName', 'Robin').property('lastName', 'Wakefield')
```

出力:

```
==>[id:953aefd9-5a54-4033-9b3a-d4dc3049f720,label:person,type:vertex,properties:[firstName:[[id:bbda02e0-8a96-4ca1-943e-621acbb26824,value:Robin]],lastName:[[id:f0291ad3-05a3-40ec-aabb-6538a7c331e3,value:Wakefield]]]]
```

入力 (Ben):

```
:> g.addV('person').property('firstName', 'Ben').property('lastName', 'Miller')
```

出力:

```
==>[id:81c891d9-beca-4c87-9009-13a826c9ed9a,label:person,type:vertex,properties:[firstName:[[id:3a3b53d3-888c-46da-bb54-1c42194b1e18,value:Ben]],lastName:[[id:48c6dd50-79c4-4585-ab71-3bf998061958,value:Miller]]]]
```

次に、人物間の関係に対し辺を追加してみましょう。

入力 (Thomas -> Mary Kay):

```
:> g.V().hasLabel('person').has('firstName', 'Thomas').addE('knows').to(g.V().hasLabel('person').has('firstName', 'Mary Kay'))
```

出力:

```
==>[id:c12bf9fb-96a1-4cb7-a3f8-431e196e702f,label:knows,type:edge,inVLabel:person,outVLabel:person,inV:0d1fa428-780c-49a5-bd3a-a68d96391d5c,outV:1ce821c6-aa3d-4170-a0b7-d14d2a4d18c3]
```

入力 (Thomas -> Robin):

```
:> g.V().hasLabel('person').has('firstName', 'Thomas').addE('knows').to(g.V().hasLabel('person').has('firstName', 'Robin'))
```

出力:

```
==>[id:58319bdd-1d3e-4f17-a106-0ddf18719d15,label:knows,type:edge,inVLabel:person,outVLabel:person,inV:3e324073-ccfc-4ae1-8675-d450858ca116,outV:1ce821c6-aa3d-4170-a0b7-d14d2a4d18c3]
```

入力 (Robin -> Ben):

```
:> g.V().hasLabel('person').has('firstName', 'Robin').addE('knows').to(g.V().hasLabel('person').has('firstName', 'Ben'))
```

出力:

```
==>[id:889c4d3c-549e-4d35-bc21-a3d1bfa11e00,label:knows,type:edge,inVLabel:person,outVLabel:person,inV:40fd641d-546e-412a-abcc-58fe53891aab,outV:3e324073-ccfc-4ae1-8675-d450858ca116]
```

## <a name="update-a-vertex"></a>頂点の更新

*Thomas* の頂点を *45* の新しい年齢で更新しましょう。

次の内容を入力します。
```
:> g.V().hasLabel('person').has('firstName', 'Thomas').property('age', 45)
```
出力:

```
==>[id:ae36f938-210e-445a-92df-519f2b64c8ec,label:person,type:vertex,properties:[firstName:[[id:872090b6-6a77-456a-9a55-a59141d4ebc2,value:Thomas]],lastName:[[id:7ee7a39a-a414-4127-89b4-870bc4ef99f3,value:Andersen]],age:[[id:a2a75d5a-ae70-4095-806d-a35abcbfe71d,value:45]]]]
```

## <a name="query-your-graph"></a>グラフのクエリ

次に、グラフに対してさまざまなクエリを実行してみましょう。

最初に、40 歳よりも年齢が上の人物だけを返すフィルターを使用してクエリを試してみましょう。

入力 (フィルター クエリ):

```
:> g.V().hasLabel('person').has('age', gt(40))
```

出力:

```
==>[id:ae36f938-210e-445a-92df-519f2b64c8ec,label:person,type:vertex,properties:[firstName:[[id:872090b6-6a77-456a-9a55-a59141d4ebc2,value:Thomas]],lastName:[[id:7ee7a39a-a414-4127-89b4-870bc4ef99f3,value:Andersen]],age:[[id:a2a75d5a-ae70-4095-806d-a35abcbfe71d,value:45]]]]
```

次に、40 歳よりも年齢が上の人物の名を表示してみましょう。

入力 (フィルター + プロジェクション クエリ):

```
:> g.V().hasLabel('person').has('age', gt(40)).values('firstName')
```

出力:

```
==>Thomas
```

## <a name="traverse-your-graph"></a>グラフの走査

Thomas のすべての友人を返すようにグラフを走査してみましょう。

入力 (Thomas の友人):

```
:> g.V().hasLabel('person').has('firstName', 'Thomas').outE('knows').inV().hasLabel('person')
```

出力: 

```
==>[id:f04bc00b-cb56-46c4-a3bb-a5870c42f7ff,label:person,type:vertex,properties:[firstName:[[id:14feedec-b070-444e-b544-62be15c7167c,value:Mary Kay]],lastName:[[id:107ab421-7208-45d4-b969-bbc54481992a,value:Andersen]],age:[[id:4b08d6e4-58f5-45df-8e69-6b790b692e0a,value:39]]]]
==>[id:91605c63-4988-4b60-9a30-5144719ae326,label:person,type:vertex,properties:[firstName:[[id:f760e0e6-652a-481a-92b0-1767d9bf372e,value:Robin]],lastName:[[id:352a4caa-bad6-47e3-a7dc-90ff342cf870,value:Wakefield]]]]
```

次に、次のレイヤーの頂点を取得しましょう。 Thomas の友人のすべての友人を返すようにグラフを走査します。

入力 (Thomas の友人の友人):

```
:> g.V().hasLabel('person').has('firstName', 'Thomas').outE('knows').inV().hasLabel('person').outE('knows').inV().hasLabel('person')
```
出力:

```
==>[id:a801a0cb-ee85-44ee-a502-271685ef212e,label:person,type:vertex,properties:[firstName:[[id:b9489902-d29a-4673-8c09-c2b3fe7f8b94,value:Ben]],lastName:[[id:e084f933-9a4b-4dbc-8273-f0171265cf1d,value:Miller]]]]
```

## <a name="drop-a-vertex"></a>頂点の削除

次に、グラフ データベースから頂点を削除しましょう。

入力 (Robin の頂点の削除):

```
:> g.V().hasLabel('person').has('firstName', 'Robin').drop()
```

## <a name="clear-your-graph"></a>グラフのクリア

最後に、すべての頂点と辺のデータベースをクリアしてみましょう。

入力:

```
:> g.V().drop()
```

お疲れさまでした。 この Azure Cosmos DB: Graph API チュートリアルは完了しました。

## <a name="review-slas-in-the-azure-portal"></a>Azure Portal での SLA の確認

[!INCLUDE [cosmosdb-tutorial-review-slas](../../includes/cosmos-db-tutorial-review-slas.md)]

## <a name="clean-up-resources"></a>リソースのクリーンアップ

このアプリの使用を続けない場合は、以下の手順に従い、Azure Portal でこのクイック スタートで作成したすべてのリソースを削除してください。  

1. Azure Portal の左側のメニューで、**[リソース グループ]** をクリックし、作成したリソースの名前をクリックします。 
2. リソース グループのページで **[削除]** をクリックし、削除するリソースの名前をテキスト ボックスに入力してから **[削除]** をクリックします。

## <a name="next-steps"></a>次のステップ

このクイック スタートでは、Azure Cosmos DB アカウントの作成、データ エクスプローラーを使用したグラフの作成、Gremlin コンソールを使用した頂点と辺の作成およびグラフの走査を行う方法を説明しました。 これで Gremlin を使用して、さらに複雑なクエリを作成し、強力なグラフ トラバーサル ロジックを実装できます。 

> [!div class="nextstepaction"]
> [Gremlin を使用したクエリ](tutorial-query-graph.md)
