---
title: "Graph API を使用した Azure Cosmos DB Node.js アプリケーションの構築 | Microsoft Docs"
description: "Azure Cosmos DB への接続とデータの照会に使用できる Node.js コード サンプルについて説明します"
services: cosmosdb
documentationcenter: 
author: mimig1
manager: jhubbard
editor: 
ms.assetid: daacbabf-1bb5-497f-92db-079910703046
ms.service: cosmosdb
ms.custom: quick start connect
ms.workload: 
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: hero-article
ms.date: 05/21/2017
ms.author: arramac
ms.translationtype: Human Translation
ms.sourcegitcommit: a30a90682948b657fb31dd14101172282988cbf0
ms.openlocfilehash: 5c996068ff5fbadda6730244c34c0d0d1f8fb447
ms.contentlocale: ja-jp
ms.lasthandoff: 05/25/2017


---
# <a name="azure-cosmos-db-build-a-nodejs-application-using-the-graph-api"></a>Azure Cosmos DB: Graph API を使用した Node.js アプリケーションの構築

Azure Cosmos DB は、Microsoft のグローバルに分散されたマルチモデル データベース サービスです。 Azure Cosmos DB の中核をなすグローバルな分散と水平方向のスケール機能を利用して、ドキュメント、キー/値、およびグラフ データベースをすばやく作成およびクエリできます。 

このクイック スタートでは、Azure Portal を使用した Graph API (プレビュー) 用の Azure Cosmos DB アカウント、データベース、およびグラフの作成方法を説明します。 続いて OSS [Gremlin Node.js](https://www.npmjs.com/package/gremlin-secure) ドライバーを使用して、コンソール アプリを構築し実行します。  

> [!NOTE]
> NPM モジュール `gremlin-secure` は、Azure Cosmos DB との接続に必要な SSL と SASL がサポートされている、`gremlin` モジュールを変更したバージョンです。 ソース コードは [GitHub](https://github.com/CosmosDB/gremlin-javascript) から入手できます。
>

## <a name="prerequisites"></a>前提条件

* このサンプルを実行する前に、以下の前提条件を満たしている必要があります。
    * [Node.js](https://nodejs.org/en/) バージョン v0.10.29 以降
    * [Git](http://git-scm.com/)

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="create-a-database-account"></a>How to create a DocumentDB account (DocumentDB アカウントの作成方法)

[!INCLUDE [cosmosdb-create-dbaccount-graph](../../includes/cosmosdb-create-dbaccount-graph.md)]

## <a name="add-a-graph"></a>グラフの追加

[!INCLUDE [cosmosdb-create-graph](../../includes/cosmosdb-create-graph.md)]

## <a name="clone-the-sample-application"></a>サンプル アプリケーションの複製

github から Graph API アプリの複製を作成し、接続文字列を設定して実行します。 プログラムでデータを処理することが非常に簡単であることがわかります。 

1. git ターミナル ウィンドウ (git bash など) を開き、`cd` を実行して作業ディレクトリに移動します。  

2. 次のコマンドを実行して、サンプル レポジトリを複製します。 

    ```bash
    git clone https://github.com/Azure-Samples/azure-cosmos-db-graph-nodejs-getting-started.git
    ```

3. 次に、Visual Studio でソリューション ファイルを開きます。 

## <a name="review-the-code"></a>コードの確認

アプリで何が行われているかを簡単に確認してみましょう。 `app.js` ファイルを開くと、以下のコード行が表示されます。 

* Gremlin クライアントが作成されます。

    ```nodejs
    const client = Gremlin.createClient(
        443, 
        config.endpoint, 
        { 
            "session": false, 
            "ssl": true, 
            "user": `/dbs/${config.database}/colls/${config.collection}`,
            "password": config.primaryKey
        });
    ```

構成はすべて、次のセクションで編集する `config.js` に含まれています。

* Gremlin の一連の手順は、`client.execute` メソッドを使用して実行されます。

    ```nodejs
    console.log('Running Count'); 
    client.execute("g.V().count()", { }, (err, results) => {
        if (err) return console.error(err);
        console.log(JSON.stringify(results));
        console.log();
    });
    ```

## <a name="update-your-connection-string"></a>接続文字列の更新

ここで Azure Portal に戻り、接続文字列情報を取得し、アプリにコピーします。

1. [Azure Portal](http://portal.azure.com/) で、Azure Cosmos DB アカウントの左のナビゲーションから、**[キー]** をクリックし、**[読み取り/書き込みキー]** をクリックします。 次の手順では、画面右側のコピー ボタンを使用して、URI とプライマリ キーを `app.js` ファイルにコピーします。

    ![Azure Portal の [キー] ブレードでアクセス キーを表示およびコピーする](./media/create-documentdb-dotnet/keys.png)

2. ポータルから (コピー ボタンを使用して) Gremlin URI 値をコピーし、config.js の `config.endpoint` キーの値に設定します。 Gremlin エンドポイントは、`mygraphdb.graphs.azure.com` のように、プロトコル/ポート番号が付いていないホスト名のみにする必要があります (`https://mygraphdb.graphs.azure.com` や `mygraphdb.graphs.azure.com:433` は不可)。

    `config.endpoint = "GRAPHENDPOINT";`

3. ポータルから PRIMARY KEY 値をコピーし、config.js の config.primaryKey の値に設定します。 これで、Azure Cosmos DB と通信するために必要なすべての情報でアプリを更新しました。 

    `config.primaryKey = "PRIMARYKEY";`

4. データベース名とグラフ (コンテナー) 名を config.database と config.collection の値として入力します。 

たとえば、完成した config.js ファイルの内容は次のようになります。

```nodejs
var config = {}

// Note that this must not have HTTPS or the port number
config.endpoint = "mygraphdb.graphs.azure.com";
config.primaryKey = "OjlhK6tjxfSXyKtrmCiM9O6gQQgu5DmgAoauzD1PdPIq1LZJmILTarHvrolyUYOB0whGQ4j21rdAFwoYep7Kkw==";
config.database = "graphdb"
config.collection = "Persons"

module.exports = config;
```

## <a name="run-the-console-app"></a>コンソール アプリの実行

1. ターミナル ウィンドウを開き、`cd` でプロジェクトに含まれる package.json ファイルのインストール ディレクトリに移動します。  

2. `npm install` を実行し、必要な npm モジュールをインストールします。 ここには、`gremlin-secure` が含まれています。

3. ターミナルで `node app.js` を実行し、node アプリケーションを起動します。

これで、データ エクスプローラーに戻って、この新しいデータの表示、クエリ、変更、操作を行うことができます。 

## <a name="browse-using-the-data-explorer"></a>データ エクスプローラーを使用した参照

次に、Azure Portal のデータ エクスプローラーに戻り、新しいグラフ データを参照しクエリできます。

* データ エクスプローラーで新しいデータベースが [コレクション] ウィンドウに表示されます。 **graphdb**、**graphcoll** の順に展開し、**[グラフ]** をクリックします。

    サンプル アプリで生成されたデータは、[グラフ] ウィンドウに表示されます。

## <a name="review-slas-in-the-azure-portal"></a>Azure Portal での SLA の確認

[!INCLUDE [cosmosdb-tutorial-review-slas](../../includes/cosmosdb-tutorial-review-slas.md)]

## <a name="clean-up-resources"></a>リソースのクリーンアップ

このアプリの使用を続けない場合は、以下の手順に従い、Azure Portal でこのクイック スタートで作成したすべてのリソースを削除してください。 

1. Azure Portal の左側のメニューで、**[リソース グループ]** をクリックし、作成したリソースの名前をクリックします。 
2. リソース グループのページで **[削除]** をクリックし、削除するリソースの名前をテキスト ボックスに入力してから **[削除]** をクリックします。

## <a name="next-steps"></a>次のステップ

このクイック スタートでは、Azure Cosmos DB アカウントを作成し、データ エクスプローラーを使用してグラフを作成し、アプリを実行する方法を説明しました。 これで Gremlin を使用して、さらに複雑なクエリを作成し、強力なグラフ トラバーサル ロジックを実装できます。 

> [!div class="nextstepaction"]
> [Gremlin を使用したクエリ](tutorial-query-graph.md)
