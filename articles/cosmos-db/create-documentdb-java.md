---
title: "Azure Cosmos DB ドキュメント データベースを Java で作成する | Microsoft Docs | Microsoft Docs'"
description: "Azure Cosmos DB DocumentDB API への接続とクエリに使用できる Java コード サンプルについて説明します。"
services: cosmos-db
documentationcenter: 
author: mimig1
manager: jhubbard
editor: 
ms.assetid: 89ea62bb-c620-46d5-baa0-eefd9888557c
ms.service: cosmos-db
ms.custom: quick start connect, mvc, devcenter
ms.workload: 
ms.tgt_pltfrm: na
ms.devlang: java
ms.topic: quickstart
ms.date: 10/30/2017
ms.author: mimig
ms.openlocfilehash: 5a793abdc24387ae2b758d29b9dfb25f134097d3
ms.sourcegitcommit: e5355615d11d69fc8d3101ca97067b3ebb3a45ef
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/31/2017
---
# <a name="azure-cosmos-db-create-a-document-database-using-java-and-the-azure-portal"></a>Azure Cosmos DB: ドキュメント データベースを Java と Azure Portal で作成する

Azure Cosmos DB は、Microsoft のグローバルに分散されたマルチモデル データベース サービスです。 Azure Cosmos DB を使用すると、管理対象のドキュメントやテーブル、グラフのデータベースを迅速に作成しクエリできます。

このクイックスタートでは、Azure Cosmos DB 用の Azure Portal ツールを使ってドキュメント データベースを作成します。 また、[DocumentDB Java API](documentdb-sdk-java.md) を使ってすばやく Java コンソール アプリを作成する方法も紹介します。 このクイックスタートの手順は、Java を実行できる任意のオペレーティング システムで使用できます。 このクイックスタートを完了すると、UI とプログラムのどちらか好きな方法で、ドキュメント データベース リソースの作成と変更ができるようになります。

## <a name="prerequisites"></a>前提条件

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)] 
[!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]

加えて次の作業を行います。 

* [Java Development Kit (JDK) 1.7 以降](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html)
    * Ubuntu で `apt-get install default-jdk` を実行して JDK をインストールします。
    * 必ず、JDK のインストール先フォルダーを指すように JAVA_HOME 環境変数を設定してください。
* [Maven](http://maven.apache.org/) バイナリ アーカイブの[ダウンロード](http://maven.apache.org/download.cgi)と[インストール](http://maven.apache.org/install.html)
    * Ubuntu で `apt-get install maven` を実行して Maven をインストールします。
* [Git](https://www.git-scm.com/)
    * Ubuntu で `sudo apt-get install git` を実行して Git をインストールします。

## <a name="create-a-database-account"></a>How to create a DocumentDB account (DocumentDB アカウントの作成方法)

ドキュメント データベースを作成するには、Azure Cosmos DB を含んだ SQL (DocumentDB) データベース アカウントを事前に作成しておく必要があります。

[!INCLUDE [cosmos-db-create-dbaccount](../../includes/cosmos-db-create-dbaccount.md)]

## <a name="add-a-collection"></a>コレクションの追加

[!INCLUDE [cosmos-db-create-collection](../../includes/cosmos-db-create-collection.md)]

<a id="add-sample-data"></a>
## <a name="add-sample-data"></a>サンプル データの追加

これで、データ エクスプローラーを使用して、新しいコレクションにデータを追加できます。

1. **[項目]** コレクションを展開して、**[ドキュメント]** > **[新しいドキュメント]** をクリックします。

   ![Azure Portal のデータ エクスプローラーで新しいドキュメントを作成する](./media/create-documentdb-java/azure-cosmosdb-data-explorer-new-document.png)
  
2. ここで、次の構造のドキュメントをコレクションに追加し、**[保存]** をクリックします。

     ```json
     {
         "id": "1",
         "category": "personal",
         "name": "groceries",
         "description": "Pick up apples and strawberries.",
         "isComplete": false
     }
     ```

    ![json データをコピーし、Azure Portal のデータ エクスプローラーで [保存] をクリックします。](./media/create-documentdb-java/azure-cosmosdb-data-explorer-save-document.png)

3.  `id` を 2 に変更したもう 1 つのドキュメントを作成して保存し、他のプロパティを適宜変更します。 Azure Cosmos DB では、データにスキーマを課さないため、新しいドキュメントは必要な任意の構造にすることができます。

## <a name="query-your-data"></a>データのクエリ

これで、データ エクスプローラーでクエリを使用して、データを取得しフィルター処理できるようになりました。

1. 既定では、クエリは `SELECT * FROM c` と設定されていることを確認してください。 この既定のクエリでは、コレクション内のすべてのドキュメントを取得し、表示します。 

    ![データ エクスプローラーの既定のクエリは `SELECT * FROM c`](./media/create-documentdb-java/azure-cosmosdb-data-explorer-query.png)

2. **[フィルターの編集]** ボタンをクリックし、クエリの述語のボックスに `ORDER BY c._ts DESC` を追加してから、**[フィルターの適用]** をクリックすることでクエリを変更します。

    ![ORDER BY c._ts DESC を追加し [フィルタの適用] をクリックすることで既定のクエリを変更](./media/create-documentdb-java/azure-cosmosdb-data-explorer-edit-query.png)

この変更したクエリでは、ドキュメントがそのタイムスタンプの降順に一覧表示されるので、ここでは 2 番目のドキュメントが最初に表示されます。 SQL 構文に慣れている場合は、サポートされている任意の [SQL クエリ](documentdb-sql-query.md)をこのボックスに入力できます。 

以上でデータ エクスプローラーでの作業は完了です。 コードにとりかかる前に、データ エクスプローラーを使用すると、ストアド プロシージャ、UDF、トリガーを作成し、サーバー側ビジネス ロジックを実行できるほか、スループットのスケールもできることに注目してください。 データ エクスプローラーでは、API で使用可能な、組み込みのプログラムによるデータ アクセスがすべて公開されていますが、Azure Portal でデータに簡単にアクセスできます。

## <a name="clone-the-sample-application"></a>サンプル アプリケーションの複製

次は、コードを使った作業に移りましょう。 GitHub から DocumentDB API アプリを複製し、接続文字列を設定して実行します。 プログラムでデータを処理することが非常に簡単であることがわかります。 

1. git bash などの git ターミナル ウィンドウを開き、`cd` コマンドを使用して、サンプル アプリをインストールするフォルダーに変更します。 

    ```bash
    cd "C:\git-samples"
    ```

2. 次のコマンドを実行して、サンプル レポジトリを複製します。 このコマンドは、コンピューター上にサンプル アプリのコピーを作成します。

    ```bash
    git clone https://github.com/Azure-Samples/azure-cosmos-db-documentdb-java-getting-started.git
    ```

## <a name="review-the-code"></a>コードの確認

この手順は省略可能です。 コード内のデータベース リソースの作成方法に関心がある場合は、次のスニペットを確認できます。 これらのスニペットはすべて C:\git-samples\azure-cosmos-db-documentdb-java-getting-started\src\GetStarted フォルダーにインストールされている `Program.java` ファイルのものです。 関心がない場合は、「[接続文字列の更新](#update-your-connection-string)」に進んでください。 

* `DocumentClient` の初期化。 [DocumentClient](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb._document_client) は Azure Cosmos DB データベース サービスのクライアント側の論理表現を提供します。 このクライアントは、サービスに対する要求の構成と実行に使用されます。

    ```java
    this.client = new DocumentClient("https://FILLME.documents.azure.com",
            "FILLME", 
            new ConnectionPolicy(),
            ConsistencyLevel.Session);
    ```

* [Database](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb._database) の作成。

    ```java
    Database database = new Database();
    database.setId(databaseName);
    
    this.client.createDatabase(database, null);
    ```

* [DocumentCollection](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb._document_collection) の作成。

    ```java
    DocumentCollection collectionInfo = new DocumentCollection();
    collectionInfo.setId(collectionName);

    ...

    this.client.createCollection(databaseLink, collectionInfo, requestOptions);
    ```

* [createDocument](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb._document_client.createdocument) メソッドを使用したドキュメントの作成。

    ```java
    // Any Java object within your code can be serialized into JSON and written to Azure Cosmos DB
    Family andersenFamily = new Family();
    andersenFamily.setId("Andersen.1");
    andersenFamily.setLastName("Andersen");
    // More properties

    String collectionLink = String.format("/dbs/%s/colls/%s", databaseName, collectionName);
    this.client.createDocument(collectionLink, family, new RequestOptions(), true);
    ```

* [queryDocuments](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb._document_client.querydocuments) メソッドを使用して JSON に対する SQL クエリを実行します。

    ```java
    FeedOptions queryOptions = new FeedOptions();
    queryOptions.setPageSize(-1);
    queryOptions.setEnableCrossPartitionQuery(true);

    String collectionLink = String.format("/dbs/%s/colls/%s", databaseName, collectionName);
    FeedResponse<Document> queryResults = this.client.queryDocuments(
        collectionLink,
        "SELECT * FROM Family WHERE Family.lastName = 'Andersen'", queryOptions);

    System.out.println("Running SQL query...");
    for (Document family : queryResults.getQueryIterable()) {
        System.out.println(String.format("\tRead %s", family));
    }
    ```    

## <a name="update-your-connection-string"></a>接続文字列の更新

ここで Azure Portal に戻り、接続文字列情報を取得し、アプリにコピーします。 これでアプリが、ホストされているデータベースと通信できます。

1. [Azure Portal](http://portal.azure.com/) で **[キー]** をクリックします。 

    画面の右側にある [コピー] ボタンを使用して 1 番上の [URI] の値をコピーします。

    ![Azure Portal の [キー] ページでアクセス キーを表示およびコピーする](./media/create-documentdb-java/keys.png)

2. C:\git-samples\azure-cosmos-db-documentdb-java-getting-started\src\GetStarted フォルダーから `Program.java` ファイルを開きます。 

3. 45 行目の `https://FILLME.documents.azure.com` にポータルの [URI] の値を貼り付けます。

4. ポータルに戻り、スクリーン ショットに示すように、[主キー] の値をコピーします。 46 行目の `FILLME` にポータルの [主キー] の値を貼り付けます。

    GetStartedDemo メソッドは、次のようになります。 
    
    ```java
    private void getStartedDemo() throws DocumentClientException, IOException {
        this.client = new DocumentClient("https://youraccountname.documents.azure.com:443/",
                "your-primary-key...RJhQrqQ5QQ==", 
                new ConnectionPolicy(),
                ConsistencyLevel.Session);
    ```

5. Program.java ファイルを保存します。

## <a name="run-the-app"></a>アプリの実行

1. git ターミナル ウィンドウで、azure-cosmos-db-documentdb-java-getting-started フォルダーに `cd` で移動します。

    ```git
    cd "C:\git-samples\azure-cosmos-db-documentdb-java-getting-started"
    ```

2. git ターミナル ウィンドウで「`mvn package`」と入力して、必要な Java パッケージをインストールします。

3. git ターミナル ウィンドウで `mvn exec:java -D exec.mainClass=GetStarted.Program` を実行して Java アプリケーションを起動します。

    ターミナル ウィンドウに、FamilyDB データベースが作成されたという通知が表示されます。 キーを押してコレクションを作成した後、データ エクスプローラーに切り替えると、FamilyDB データベースが追加されていることがわかります。
    
    キーを繰り返し押して、ドキュメントを作成した後、クエリを実行します。
    
    プログラムの終了時に、このアプリのすべてのリソースはアカウントから削除されるので、料金は発生しません。 

    ![コンソール出力](./media/create-documentdb-java/console-output.png)


## <a name="review-slas-in-the-azure-portal"></a>Azure Portal での SLA の確認

[!INCLUDE [cosmosdb-tutorial-review-slas](../../includes/cosmos-db-tutorial-review-slas.md)]

## <a name="clean-up-resources"></a>リソースのクリーンアップ

[!INCLUDE [cosmosdb-delete-resource-group](../../includes/cosmos-db-delete-resource-group.md)]

## <a name="next-steps"></a>次のステップ

このクイックスタートでは、データ エクスプローラーを使って Azure Cosmos DB アカウント、ドキュメント データベース、コレクションを作成する方法について説明しました。また、同じことを行うアプリをプログラムから実行する方法についても説明しました。 これで、Azure Cosmos DB コレクションに追加のデータをインポートできます。 

> [!div class="nextstepaction"]
> [Azure Cosmos DB へのデータのインポート](import-data.md)


