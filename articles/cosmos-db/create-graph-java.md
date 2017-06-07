---
title: "Graph API を使用した Azure Cosmos DB Java アプリケーションの構築 | Microsoft Docs"
description: "Gremlin を使用した Azure Cosmos DB 内のグラフ データへの接続とクエリに使用できる Java コード サンプルについて説明します。"
services: cosmos-db
documentationcenter: 
author: mimig1
manager: jhubbard
editor: 
ms.assetid: daacbabf-1bb5-497f-92db-079910703046
ms.service: cosmos-db
ms.custom: quick start connect, mvc
ms.workload: 
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: hero-article
ms.date: 05/10/2017
ms.author: arramac
ms.translationtype: Human Translation
ms.sourcegitcommit: a643f139be40b9b11f865d528622bafbe7dec939
ms.openlocfilehash: e02dfacd7f67c6c935243a916140c8b29fb14f9d
ms.contentlocale: ja-jp
ms.lasthandoff: 05/31/2017


---
# <a name="azure-cosmos-db-build-a-java-application-using-the-graph-api"></a>Azure Cosmos DB: Graph API を使用した Java アプリケーションの構築

Azure Cosmos DB は、Microsoft のグローバルに分散されたマルチモデル データベース サービスです。 Azure Cosmos DB の中核をなすグローバルな分散と水平方向のスケール機能を利用して、ドキュメント、キー/値、およびグラフ データベースをすばやく作成およびクエリできます。 

このクイック スタートでは、Azure Portal を使用した Graph API (プレビュー) 用の Azure Cosmos DB アカウント、データベース、およびグラフの作成方法を説明します。 続いて OSS [Gremlin Java](https://mvnrepository.com/artifact/org.apache.tinkerpop/gremlin-driver) ドライバーを使用して、コンソール アプリを構築し実行します。  

## <a name="prerequisites"></a>前提条件

* このサンプルを実行する前に、以下の前提条件を満たしている必要があります。
   * JDK 1.7 以上 (JDK を所有していない場合は `apt-get install default-jdk` を実行します)。環境変数 (`JAVA_HOME` など) を設定してください。
   * Maven (Maven がない場合は、`apt-get install maven` を実行します)

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="create-a-database-account"></a>データベース アカウントの作成

[!INCLUDE [cosmos-db-create-dbaccount-graph](../../includes/cosmos-db-create-dbaccount-graph.md)]

## <a name="add-a-graph"></a>グラフの追加

[!INCLUDE [cosmos-db-create-graph](../../includes/cosmos-db-create-graph.md)]

## <a name="clone-the-sample-application"></a>サンプル アプリケーションの複製

github から Graph API (プレビュー) アプリの複製を作成し、接続文字列を設定して実行します。 プログラムでデータを処理することが非常に簡単であることがわかります。 

1. git ターミナル ウィンドウ (git bash など) を開き、`cd` を実行して作業ディレクトリに移動します。  

2. 次のコマンドを実行して、サンプル レポジトリを複製します。 

    ```bash
    git clone https://github.com/Azure-Samples/azure-cosmos-db-graph-java-getting-started.git
    ```

## <a name="review-the-code"></a>コードの確認

アプリで何が行われているかを簡単に確認してみましょう。 `Program.java` ファイルを開くと、以下のコード行が表示されます。 

* Gremlin `Client` は、前に設定した `src/remote-secure.yaml` 内の構成から初期化されます。

    ```java
    Cluster cluster = Cluster.build(new File("src/remote.yaml")).create();
    
    Client client = cluster.connect();
    ```

* Gremlin の一連の手順は、`client.submit` メソッドを使用して実行されます。

    ```java
    ResultSet results = client.submit("g.V()");

    CompletableFuture<List<Result>> completableFutureResults = results.all();
    List<Result> resultList = completableFutureResults.get();

    for (Result result : resultList) {
        System.out.println(result.toString());
    }
    ```
## <a name="update-your-connection-string"></a>接続文字列の更新

ここで Azure Portal に戻り、接続文字列情報を取得し、アプリにコピーします。

1. [Azure Portal](http://portal.azure.com/) で、Azure Cosmos DB アカウントの左のナビゲーションから、**[キー]** をクリックし、**[読み取り/書き込みキー]** をクリックします。 次の手順では、画面右側のコピー ボタンを使用して、URI とプライマリ キーを `Program.java` ファイルにコピーします。

    ![Azure Portal の [キー] ブレードでアクセス キーを表示およびコピーする](./media/create-graph-java/keys.png)

2. `src/remote-secure.yaml` ファイルを開きます。 

3. `src/remote-secure.yaml` ファイルの *host*、*port*、*username*、*password*、*connectionPool*、および *serializer* の構成を入力します。

    設定|推奨値|説明
    ---|---|---
    ホスト|***.graphs.azure.com|Graph サービス URI。Azure Portal から取得できます
    ポート|443|443 に設定します
    ユーザー名|*自分のユーザー名*|フォーム `/dbs/<db>/colls/<coll>` のリソース。
    パスワード|*プライマリ マスター キー*|Azure Cosmos DB のプライマリ マスター キー
    接続プール|{enableSsl: true}|SSL 用の接続プールの設定
    シリアライザー|{ className:org.apache.tinkerpop.gremlin.<br>driver.ser.GraphSONMessageSerializerV1d0,<br> config: { serializeResultToString: true }}|この値に設定します

## <a name="run-the-console-app"></a>コンソール アプリの実行

1. ターミナルで `mvn package` を実行し、必要な npm モジュールをインストールします。

2. ターミナルで `mvn exec:java -D exec.mainClass=GetStarted.Program` を実行し、Java アプリケーションを起動します。

これで、データ エクスプローラーに戻って、この新しいデータの表示、クエリ、変更、操作を行うことができます。 

## <a name="browse-using-the-data-explorer"></a>データ エクスプローラーを使用した参照

次に、Azure Portal のデータ エクスプローラーに戻り、新しいグラフ データを参照しクエリできます。

* データ エクスプローラーで新しいデータベースが [コレクション] ウィンドウに表示されます。 **graphdb**、**graphcoll** の順に展開し、**[グラフ]** をクリックします。

    サンプル アプリで生成されたデータは、[グラフ] ウィンドウに表示されます。

## <a name="review-slas-in-the-azure-portal"></a>Azure Portal での SLA の確認

[!INCLUDE [cosmosdb-tutorial-review-slas](../../includes/cosmos-db-tutorial-review-slas.md)]

## <a name="clean-up-resources"></a>リソースのクリーンアップ

このアプリの使用を続けない場合は、以下の手順に従い、Azure Portal でこのクイック スタートで作成したすべてのリソースを削除してください。 

1. Azure Portal の左側のメニューで、**[リソース グループ]** をクリックし、作成したリソースの名前をクリックします。 
2. リソース グループのページで **[削除]** をクリックし、削除するリソースの名前をテキスト ボックスに入力してから **[削除]** をクリックします。

## <a name="next-steps"></a>次のステップ

このクイック スタートでは、Azure Cosmos DB アカウントを作成し、データ エクスプローラーを使用してグラフを作成し、アプリを実行する方法を説明しました。 これで Gremlin を使用して、さらに複雑なクエリを作成し、強力なグラフ トラバーサル ロジックを実装できます。 

> [!div class="nextstepaction"]
> [Gremlin を使用したクエリ](tutorial-query-graph.md)


