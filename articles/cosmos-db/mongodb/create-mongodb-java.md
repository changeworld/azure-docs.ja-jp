---
title: 'クイックスタート: Mongo DB 用 Azure Cosmos DB API と Java SDK を使用して Web アプリを作成する'
description: Azure Cosmos DB の MongoDB 用 API に接続してクエリを実行することができる Java コード サンプルの作成方法について説明します。
author: gahl-levy
ms.author: gahllevy
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.devlang: java
ms.topic: quickstart
ms.date: 08/26/2021
ms.custom: seo-java-august2019, seo-java-september2019, devx-track-java
ms.openlocfilehash: ec9514f442e6b5b262dc634f8cdcd58ebd530736
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/22/2021
ms.locfileid: "130226737"
---
# <a name="quickstart-create-a-console-app-with-java-and-the-mongodb-api-in-azure-cosmos-db"></a>クイックスタート: Azure Cosmos DB の MongoDB API と Java を使ってコンソール アプリを作成する
[!INCLUDE[appliesto-mongodb-api](../includes/appliesto-mongodb-api.md)]

> [!div class="op_single_selector"]
> * [.NET](create-mongodb-dotnet.md)
> * [Python](create-mongodb-python.md)
> * [Java](create-mongodb-java.md)
> * [Node.js](create-mongodb-nodejs.md)
> * [Xamarin](create-mongodb-xamarin.md)
> * [Golang](create-mongodb-go.md)
>  

このクイックスタートでは、Azure portal から Azure Cosmos DB for MongoDB API アカウントを作成、管理し、GitHub からクローンした Java SDK アプリを使用してデータを追加します。 Azure Cosmos DB は、マルチモデル データベース サービスです。グローバルな分散と水平方向のスケーリング機能を備えたドキュメント データベースやテーブル データベース、キーと値のデータベース、グラフ データベースをすばやく作成し、クエリを実行することができます。

## <a name="prerequisites"></a>前提条件
- アクティブなサブスクリプションが含まれる Azure アカウント。 [無料で作成できます](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)。 または、Azure サブスクリプションなしで、[Azure Cosmos DB を無料で試す](https://azure.microsoft.com/try/cosmosdb/)こともできます。 [Azure Cosmos DB Emulator](https://aka.ms/cosmosdb-emulator) を使用することもできます。接続文字列には、`.mongodb://localhost:C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw==@localhost:10255/admin?ssl=true` を使用してください。
- [Java Development Kit (JDK) バージョン 8](https://www.azul.com/downloads/azure-only/zulu/?&version=java-8-lts&architecture=x86-64-bit&package=jdk)。 
- [Maven](https://maven.apache.org/download.cgi)。 または、`apt-get install maven` を実行して Maven をインストールします。
- [Git](https://git-scm.com/downloads). 

## <a name="create-a-database-account"></a>データベース アカウントの作成

[!INCLUDE [mongodb-create-dbaccount](../includes/cosmos-db-create-dbaccount-mongodb.md)]

## <a name="add-a-collection"></a>コレクションの追加

新しいデータベースに **db**、新しいコレクションに **coll** という名前を付けます。

[!INCLUDE [cosmos-db-create-collection](../includes/cosmos-db-mongodb-create-collection.md)] 

## <a name="clone-the-sample-application"></a>サンプル アプリケーションのクローン

それでは、GitHub からアプリをクローンし、接続文字列を設定して実行しましょう。 プログラムでデータを処理することが非常に簡単であることがわかります。 

1. コマンド プロンプトを開いて git-samples という名前の新しいフォルダーを作成し、コマンド プロンプトを閉じます。

    ```bash
    md "C:\git-samples"
    ```

2. git bash などの git ターミナル ウィンドウを開いて、`cd` コマンドを使用して、サンプル アプリをインストールする新しいフォルダーに変更します。

    ```bash
    cd "C:\git-samples"
    ```

3. 次のコマンドを実行して、サンプル レポジトリをクローンします。 このコマンドは、コンピューター上にサンプル アプリのコピーを作成します。

    ```bash
    git clone https://github.com/Azure-Samples/azure-cosmos-db-mongodb-java-getting-started.git
    ```

3. 次に、使い慣れたエディターでコードを開きます。 

## <a name="review-the-code"></a>コードの確認

この手順は省略可能です。 コード内のデータベース リソースの作成方法に関心がある場合は、次のスニペットを確認できます。 関心がない場合は、「[接続文字列の更新](#update-your-connection-string)」に進んでください。 

次のスニペットはすべて *Program.java* ファイルからのものです。

このコンソール アプリは、[MongoDB Java ドライバー](https://docs.mongodb.com/ecosystem/drivers/java/)を使用します。 

* DocumentClient が初期化されます。

    ```java
    MongoClientURI uri = new MongoClientURI("FILLME");`

    MongoClient mongoClient = new MongoClient(uri);            
    ```

* 新しいデータベースとコレクションが作成されます。

    ```java
    MongoDatabase database = mongoClient.getDatabase("db");

    MongoCollection<Document> collection = database.getCollection("coll");
    ```

* `MongoCollection.insertOne` を使用していくつかのドキュメントが挿入されます。

    ```java
    Document document = new Document("fruit", "apple")
    collection.insertOne(document);
    ```

* `MongoCollection.find` を使用していくつかのクエリが実行されます。

    ```java
    Document queryResult = collection.find(Filters.eq("fruit", "apple")).first();
    System.out.println(queryResult.toJson());       
    ```

## <a name="update-your-connection-string"></a>接続文字列を更新する

ここで Azure Portal に戻り、接続文字列情報を取得し、アプリにコピーします。

1. Azure Cosmos DB アカウントから **[クイック スタート]** を選択し、**Java** を選択して、接続文字列をクリップボードにコピーします。

2. *Program.java* ファイルを開き、MongoClientURI コンストラクターの引数を接続文字列で置き換えます。 これで、Azure Cosmos DB と通信するために必要なすべての情報でアプリを更新しました。 
    
## <a name="run-the-console-app"></a>コンソール アプリの実行

1. ターミナルで `mvn package` を実行し、必要な npm モジュールをインストールします。

2. ターミナルで `mvn exec:java -D exec.mainClass=GetStarted.Program` を実行し、Java アプリケーションを起動します。

これで、[Robomongo](connect-using-robomongo.md) / [Studio 3T](connect-using-mongochef.md) を使用して、この新しいデータをクエリ、変更、および操作できるようになりました。

## <a name="review-slas-in-the-azure-portal"></a>Azure Portal での SLA の確認

[!INCLUDE [cosmosdb-tutorial-review-slas](../includes/cosmos-db-tutorial-review-slas.md)]

## <a name="clean-up-resources"></a>リソースをクリーンアップする

[!INCLUDE [cosmosdb-delete-resource-group](../includes/cosmos-db-delete-resource-group.md)]

## <a name="next-steps"></a>次のステップ

このクイックスタートでは、Azure Cosmos DB API for Mongo DB アカウントを作成する方法や、データ エクスプローラーを使用してデータベースとコンテナーを追加する方法、Java コンソール アプリを使用してデータを追加する方法について説明しました。 これで、Cosmos データベースに追加のデータをインポートできます。 

Azure Cosmos DB への移行のための容量計画を実行しようとしていますか? 容量計画のために、既存のデータベース クラスターに関する情報を使用できます。
* 知っていることが既存のデータベース クラスター内の仮想コアとサーバーの数のみである場合は、[仮想コアまたは仮想 CPU の数を使用した要求ユニットの見積もり](../convert-vcore-to-request-unit.md)に関するページを参照してください 
* 現在のデータベース ワークロードに対する通常の要求レートがわかっている場合は、[Azure Cosmos DB 容量計画ツールを使用した要求ユニットに見積もり](estimate-ru-capacity-planner.md)に関するページを参照してください

> [!div class="nextstepaction"]
<<<<<<< HEAD:articles/cosmos-db/create-mongodb-java.md
> [MongoDB データを Azure Cosmos DB にインポートする](../dms/tutorial-mongodb-cosmos-db.md?toc=%2fazure%2fcosmos-db%2ftoc.json%253ftoc%253d%2fazure%2fcosmos-db%2ftoc.json)
=======
> [MongoDB データを Azure Cosmos DB にインポートする](../../dms/tutorial-mongodb-cosmos-db.md?toc=%2fazure%2fcosmos-db%2ftoc.json%253ftoc%253d%2fazure%2fcosmos-db%2ftoc.json)
>>>>>>> repo_sync_working_branch:articles/cosmos-db/mongodb/create-mongodb-java.md
