---
title: "Graph API を使用した Azure Cosmos DB Java アプリケーションの構築 | Microsoft Docs"
description: "Gremlin を使用した Azure Cosmos DB 内のグラフ データへの接続とクエリに使用できる Java コード サンプルについて説明します。"
services: cosmos-db
documentationcenter: 
author: dennyglee
manager: jhubbard
editor: 
ms.assetid: daacbabf-1bb5-497f-92db-079910703046
ms.service: cosmos-db
ms.custom: quick start connect, mvc
ms.workload: 
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: hero-article
ms.date: 07/14/2017
ms.author: denlee
ms.translationtype: HT
ms.sourcegitcommit: bde1bc7e140f9eb7bb864c1c0a1387b9da5d4d22
ms.openlocfilehash: 8eac406c6ef96d7ae8dd5f4931c7d16edb723be8
ms.contentlocale: ja-jp
ms.lasthandoff: 07/21/2017

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

* Gremlin `Client` は、`src/remote.yaml` 内の構成から初期化されます。

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
## <a name="update-your-connection-string"></a>接続文字列を更新する

1. src/remote.yaml ファイルを開きます。 

3. src/remote.yaml ファイルの *host*、*port*、*username*、*password*、*connectionPool*、および *serializer* の構成を入力します。

    Setting|推奨値|説明
    ---|---|---
    ホスト|[***.graphs.azure.com]|以下のスクリーンショットをご覧ください。 これは、Azure Portal の [概要] ページに表示される [Gremlin URI] の値から末尾の ":443/" を削除して角かっこで囲んだものです。<br><br>この値は、[キー] タブから取得することもできます。その場合は、[URI] の値から "https://" を削除し、documents を graphs に変更して、末尾の ":443/" を削除してください。
    ポート|443|443 に設定します。
    ユーザー名|*自分のユーザー名*|`/dbs/<db>/colls/<coll>` 形式のリソースです。`<db>` は実際のデータベースの名前、`<coll>` は実際のコレクションの名前になります。
    パスワード|*プライマリ マスター キー*|以下の 2 つ目のスクリーンショットをご覧ください。 これは自分のプライマリ キーです。Azure Portal の [キー] ページの [プライマリ キー] ボックスから取得できます。 ボックスの左側にあるコピー ボタンを使用して値をコピーしてください。
    接続プール|{enableSsl: true}|SSL 用の接続プールの設定です。
    シリアライザー|{ className: org.apache.tinkerpop.gremlin.<br>driver.ser.GraphSONMessageSerializerV1d0,<br> config: { serializeResultToString: true }}|この値に設定します。改行 (`\n`) を削除して値を貼り付けてください。

    Hosts の値については、**[概要]** ページにある **[Gremlin URI]** から値をコピーしてください。![Azure Portal の [概要] ページで [Gremlin URI] の値を表示してコピー](./media/create-graph-java/gremlin-uri.png)

    Password の値については、**[キー]** ページにある **[プライマリ キー]** から値をコピーしてください。![Azure Portal の [キー] ページでプライマリ キーを表示してコピー](./media/create-graph-java/keys.png)

## <a name="run-the-console-app"></a>コンソール アプリの実行

1. ターミナルで `mvn package` を実行し、必要な Java パッケージをインストールします。

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


