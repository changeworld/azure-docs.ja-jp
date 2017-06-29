---
title: "Graph API を使用した Azure Cosmos DB .NET アプリケーションの構築 | Microsoft Docs"
description: "Azure Cosmos DB への接続とデータの照会に使用できる .NET コード サンプルについて説明します"
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
ms.date: 05/21/2017
ms.author: arramac
ms.translationtype: Human Translation
ms.sourcegitcommit: 80be19618bd02895d953f80e5236d1a69d0811af
ms.openlocfilehash: 3491aa53a55d988876710c0ac19383e642dda27b
ms.contentlocale: ja-jp
ms.lasthandoff: 06/07/2017


---
# <a name="azure-cosmos-db-build-a-net-application-using-the-graph-api"></a>Azure Cosmos DB: Graph API を使用した .NET アプリケーションの構築

Azure Cosmos DB は、Microsoft のグローバルに分散されたマルチモデル データベース サービスです。 Azure Cosmos DB の中核をなすグローバルな分散と水平方向のスケール機能を利用して、ドキュメント、キー/値、およびグラフ データベースをすばやく作成およびクエリできます。 

このクイック スタートでは、Azure Portal を使用した Azure Cosmos DB アカウント、データベース、およびグラフ (コンテナー) の作成方法を説明します。 続いて、[Graph API](graph-sdk-dotnet.md) (プレビュー) で構築されたコンソール アプリをビルドして実行します。  

## <a name="prerequisites"></a>前提条件

まだ Visual Studio 2017 をインストールしていない場合は、**無料**の [Visual Studio 2017 Community エディション](https://www.visualstudio.com/downloads/)をダウンロードして使用できます。 Visual Studio のセットアップ中に、必ず **[Azure の開発]** を有効にしてください。

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="create-a-database-account"></a>How to create a DocumentDB account (DocumentDB アカウントの作成方法)

[!INCLUDE [cosmos-db-create-dbaccount-graph](../../includes/cosmos-db-create-dbaccount-graph.md)]

## <a name="add-a-graph"></a>グラフの追加

[!INCLUDE [cosmos-db-create-graph](../../includes/cosmos-db-create-graph.md)]

## <a name="clone-the-sample-application"></a>サンプル アプリケーションの複製

github から Graph API アプリの複製を作成し、接続文字列を設定して実行します。 プログラムでデータを処理することが非常に簡単であることがわかります。 

1. git ターミナル ウィンドウ (git bash など) を開き、`cd` を実行して作業ディレクトリに移動します。  

2. 次のコマンドを実行して、サンプル レポジトリを複製します。 

    ```bash
    git clone https://github.com/Azure-Samples/azure-cosmos-db-graph-dotnet-getting-started.git
    ```

3. 次に、Visual Studio でソリューション ファイルを開きます。 

## <a name="review-the-code"></a>コードの確認

アプリで何が行われているかを簡単に確認してみましょう。 Program.cs ファイルを開くと、以下のコード行によって、Azure Cosmos DB リソースが作成されていることがわかります。 

* DocumentClient が初期化されます。 プレビューでは、Azure Cosmos DB クライアントでグラフ拡張機能 API を追加しました。 Azure Cosmos DB クライアントおよびリソースから切り離されたスタンドアロンのグラフ クライアントで作業しています。

    ```csharp
    using (DocumentClient client = new DocumentClient(
        new Uri(endpoint),
        authKey,
        new ConnectionPolicy { ConnectionMode = ConnectionMode.Direct, ConnectionProtocol = Protocol.Tcp }))
    ```

* 新しいデータベースが作成されます。

    ```csharp
    Database database = await client.CreateDatabaseIfNotExistsAsync(new Database { Id = "graphdb" });
    ```

* 新しいグラフが作成されます。

    ```csharp
    DocumentCollection graph = await client.CreateDocumentCollectionIfNotExistsAsync(
        UriFactory.CreateDatabaseUri("graphdb"),
        new DocumentCollection { Id = "graph" },
        new RequestOptions { OfferThroughput = 1000 });
    ```
* Gremlin の一連の手順は、`CreateGremlinQuery` メソッドを使用して実行されます。

    ```csharp
    // The CreateGremlinQuery method extensions allow you to execute Gremlin queries and iterate
    // results asychronously
    IDocumentQuery<dynamic> query = client.CreateGremlinQuery<dynamic>(graph, "g.V().count()");
    while (query.HasMoreResults)
    {
        foreach (dynamic result in await query.ExecuteNextAsync())
        {
            Console.WriteLine($"\t {JsonConvert.SerializeObject(result)}");
        }
    }

    ```

## <a name="update-your-connection-string"></a>接続文字列の更新

ここで Azure Portal に戻り、接続文字列情報を取得し、アプリにコピーします。

1. [Azure Portal](http://portal.azure.com/) で、Azure Cosmos DB アカウントの左のナビゲーションから、**[キー]** をクリックし、**[読み取り/書き込みキー]** をクリックします。 次の手順では、画面右側のコピー ボタンを使用して、URI とプライマリ キーを `App.config` ファイルにコピーします。

    ![Azure Portal の [キー] ブレードでアクセス キーを表示およびコピーする](./media/create-graph-dotnet/keys.png)

2. Visual Studio 2017 で、`App.config` ファイルを開きます。 

3. ポータルから (コピー ボタンを使用して) URI 値をコピーし、`App.config` の endpoint キーの値に設定します。 

    `<add key="Endpoint" value="FILLME.documents.azure.com:443" />`

4. 次に、ポータルから PRIMARY KEY 値をコピーし、`App.config` の authKey の値に設定します。 

    `<add key="AuthKey" value="FILLME" />`

これで、Azure Cosmos DB と通信するために必要なすべての情報でアプリを更新しました。 

## <a name="run-the-console-app"></a>コンソール アプリの実行

1. Visual Studio の**ソリューション エクスプローラー**で **[GraphGetStarted]** プロジェクトを右クリックし、**[NuGet パッケージの管理]** をクリックします。 

2. NuGet の**[Browse]** (参照) ボックスに、「*Microsoft.Azure.Graphs*」と入力し、**[Includes prerelease]** (プレリリース版を含める) ボックスをオンにします。 

3. 結果から、**Microsoft.Azure.Graphs** ライブラリをインストールします。 これにより、Azure Cosmos DB グラフ拡張機能ライブラリ パッケージとすべての依存関係がインストールされます。

4. Ctrl + F5 キーを押してアプリケーションを実行します。

   コンソール ウィンドウには、グラフに追加されている頂点と辺が表示されます。 スクリプトが完了したら、Enter キーを 2 度押してコンソール ウィンドウを閉じます。 

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


