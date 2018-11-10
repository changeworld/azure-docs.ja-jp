---
title: Async Java SDK を使用して Java アプリを構築して Azure Cosmos DB SQL API データを管理する | Microsoft Docs
description: このチュートリアルでは、Async Java アプリケーションを使用して、Azure Cosmos DB SQL API アカウントでデータを格納したりデータにアクセスしたりする方法について説明します。
keywords: NoSQL チュートリアル, オンライン データベース, Java コンソール アプリケーション
services: cosmos-db
author: SnehaGunda
manager: kfile
ms.service: cosmos-db
ms.component: cosmosdb-sql
ms.devlang: java
ms.topic: tutorial
ms.date: 06/29/2018
ms.author: sngun
ms.openlocfilehash: 66e937e92528e2f0a1fca9d9aac78f7265eef4f7
ms.sourcegitcommit: ae45eacd213bc008e144b2df1b1d73b1acbbaa4c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/01/2018
ms.locfileid: "50741234"
---
# <a name="tutorial-build-a-java-app-with-async-java-sdk-to-manage-azure-cosmos-db-sql-api-data"></a>チュートリアル: Async Java SDK を使用して Java アプリを構築して Azure Cosmos DB SQL API データを管理する

> [!div class="op_single_selector"]
> * [.NET](sql-api-get-started.md)
> * [.NET Core](sql-api-dotnetcore-get-started.md)
> * [Java](sql-api-java-get-started.md)
> * [Async Java](sql-api-async-java-get-started.md)
> * [Node.js](sql-api-nodejs-get-started.md)
> 

このチュートリアルでは、Async Java SDK を使用して Java アプリケーションを構築し、Azure Cosmos DB SQL API データを格納したり、それにアクセスしたりする方法について説明します。

このチュートリアルに含まれるタスクは次のとおりです。

> [!div class="checklist"]
> * Azure Cosmos DB アカウントを作成し、アカウントに接続する
> * ソリューションを構成する
> * コレクションを作成する
> * JSON ドキュメントを作成する
> * コレクションをクエリする

## <a name="prerequisites"></a>前提条件

以下のリソースがそろっていることを確認してください。

* アクティブな Azure アカウントアカウントがない場合、Azure 試用版にサインアップして、最大 10 件の無料 Mobile Apps を入手できます。 お持ちでない場合は、 [無料アカウント](https://azure.microsoft.com/free/)にサインアップしてください。 

  [!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]

* [Git](https://git-scm.com/downloads)。
* [Java Development Kit (JDK) 8 以降](https://aka.ms/azure-jdks)。
* [Maven](http://maven.apache.org/download.cgi)。

## <a name="step-1-create-an-azure-cosmos-db-account"></a>手順 1: Azure Cosmos DB アカウントを作成する
それでは、Azure Cosmos DB アカウントを作成してみましょう。 使用するアカウントが既にある場合は、「[GitHub プロジェクトを複製する](#GitClone)」に進んでかまいません。 Azure Cosmos DB Emulator を使用する場合は、[DB Emulator](local-emulator.md) に関する記事に記載されている手順に従ってエミュレーターをセットアップし、「[GitHub プロジェクトを複製する](#GitClone)」に進んでください。

[!INCLUDE [cosmos-db-create-dbaccount](../../includes/cosmos-db-create-dbaccount.md)]

## <a id="GitClone"></a>手順 2: GitHub リポジトリを複製する

最初に、[Azure Cosmos DB と Java の概要](https://github.com/Azure-Samples/azure-cosmos-db-sql-api-async-java-getting-started)に関する GitHub リポジトリを複製します。 たとえば、ローカル ディレクトリで次のコマンドを実行し、サンプル プロジェクトをローカルに取得します。

```bash
git clone https://github.com/Azure-Samples/azure-cosmos-db-sql-api-async-java-getting-started.git

cd azure-cosmos-db-sql-api-async-java-getting-started
cd azure-cosmosdb-get-started
```

ディレクトリには `pom.xml` と、`Main.java` などの Java ソース コードを含む `src/main/java/com/microsoft/azure/cosmosdb/sample` フォルダーがあります。 プロジェクトには、Azure Cosmos DB の操作 (ドキュメントの作成やコレクション内のデータのクエリなど) に必要なコードが含まれています。 `pom.xml` には、[Azure Cosmos DB Java SDK on Maven](https://mvnrepository.com/artifact/com.microsoft.azure/azure-documentdb) の依存関係が含まれています。

```xml
<dependency>
  <groupId>com.microsoft.azure</groupId>
  <artifactId>azure-documentdb</artifactId>
  <version>2.0.0</version>
</dependency>
```

## <a id="Connect"></a>手順 3: Azure Cosmos DB アカウントに接続する

次に、[Azure portal](https://portal.azure.com) に戻り、エンドポイントとプライマリ マスター キーを取得します。 Azure Cosmos DB のエンドポイントとプライマリ キーは、アプリケーションが接続先を認識し、Azure Cosmos DB がアプリケーションの接続を信頼するために必要です。 `AccountSettings.java` ファイルは、プライマリ キーと URI の値を保持します。 

Azure Portal で Azure Cosmos DB アカウントに移動し、**[キー]** をクリックします。 ポータルから URI とプライマリ キーをコピーし、`AccountSettings.java` ファイルに貼り付けます。 

```java
public class AccountSettings 
{
  // Replace MASTER_KEY and HOST with values from your Azure Cosmos DB account.
    
  // <!--[SuppressMessage("Microsoft.Security", "CS002:SecretInNextLine")]-->
  public static String MASTER_KEY = System.getProperty("ACCOUNT_KEY", 
          StringUtils.defaultString(StringUtils.trimToNull(
          System.getenv().get("ACCOUNT_KEY")), "<Fill your Azure Cosmos DB account key>"));

  public static String HOST = System.getProperty("ACCOUNT_HOST",
           StringUtils.defaultString(StringUtils.trimToNull(
           System.getenv().get("ACCOUNT_HOST")),"<Fill your Azure Cosmos DB URI>"));
}
```

![ポータルからキーを取得するスクリーンショット][keys]

## <a name="step-4-initialize-the-client-object"></a>手順 4: クライアント オブジェクトを初期化する

"AccountSettings.java" ファイルで定義されているホスト URI とプライマリ キー値を使用してクライアント オブジェクトを初期化します

```java
client = new AsyncDocumentClient.Builder()
         .withServiceEndpoint(AccountSettings.HOST)
         .withMasterKey(AccountSettings.MASTER_KEY)
         .withConnectionPolicy(ConnectionPolicy.GetDefault())
         .withConsistencyLevel(ConsistencyLevel.Session)
         .build();
```

## <a id="CreateDatabase"></a>手順 5: データベースを作成する

Azure Cosmos DB [データベース](sql-api-resources.md#databases)は、DocumentClient クラスの createDatabaseIfNotExists() メソッドを使用して作成できます。 データベースは、コレクションに分割された JSON ドキュメント ストレージの論理上のコンテナーです。

```java
private void createDatabaseIfNotExists() throws Exception 
{
    
    writeToConsoleAndPromptToContinue("Check if database " + databaseName + " exists.");

    String databaseLink = String.format("/dbs/%s", databaseName);

    Observable<ResourceResponse<Database>> databaseReadObs = client.readDatabase(databaseLink, null);

    Observable<ResourceResponse<Database>> databaseExistenceObs = databaseReadObs
                .doOnNext(x -> {System.out.println("database " + databaseName + " already exists.");}).onErrorResumeNext(e -> {
   // if the database doesn't already exists
   // readDatabase() will result in 404 error
   if (e instanceof DocumentClientException) 
   {
       DocumentClientException de = (DocumentClientException) e;
       // if database 
       if (de.getStatusCode() == 404) {
       // if the database doesn't exist, create it.
       System.out.println("database " + databaseName + " doesn't existed," + " creating it...");
       Database dbDefinition = new Database();
       dbDefinition.setId(databaseName);
       return client.createDatabase(dbDefinition, null);
     }
     }

    // some unexpected failure in reading database happened.
    // pass the error up.
    System.err.println("Reading database " + databaseName + " failed.");
        return Observable.error(e);     
    });

   // wait for completion
   databaseExistenceObs.toCompletable().await();

   System.out.println("Checking database " + databaseName + " completed!\n");
}
```

## <a id="CreateColl"></a>手順 6: コレクションを作成する

> [!WARNING]
> **createCollection** は、予約済みのスループットで新しいコレクションを作成します。これによって価格に影響があります。 詳細については、[価格のページ](https://azure.microsoft.com/pricing/details/cosmos-db/)を参照してください。
> 
> 
コレクションは、DocumentClient クラスの createDocumentCollectionIfNotExists() メソッドを使用して作成できます。 コレクションには、JSON ドキュメントのほか、関連する JavaScript アプリケーション ロジックが格納されます。

```java
private void createDocumentCollectionIfNotExists() throws Exception 
{
    
    writeToConsoleAndPromptToContinue("Check if collection " + collectionName + " exists.");

    // query for a collection with a given id
    // if it exists nothing else to be done
    // if the collection doesn't exist, create it.

    String databaseLink = String.format("/dbs/%s", databaseName);

    // we know there is only single page of result (empty or with a match)
    client.queryCollections(databaseLink, new SqlQuerySpec("SELECT * FROM r where r.id = @id", 
            new SqlParameterCollection(new SqlParameter("@id", collectionName))), null).single() 
            .flatMap(page -> {
            if (page.getResults().isEmpty()) {
             // if there is no matching collection create the collection.
             DocumentCollection collection = new DocumentCollection();
             collection.setId(collectionName);
             System.out.println("Creating collection " + collectionName);

             return client.createCollection(databaseLink, collection, null);
            } 
            else {
              // collection already exists, nothing else to be done.
              System.out.println("Collection " + collectionName + "already exists");
              return Observable.empty();
            }
      }).toCompletable().await();

 System.out.println("Checking collection " + collectionName + " completed!\n");
    }
```

## <a id="CreateDoc"></a>手順 7: JSON ドキュメントを作成する

[ドキュメント](sql-api-resources.md#documents)は、DocumentClient クラスの createDocument メソッドを使用して作成できます。 ドキュメントは、ユーザー定義の (ユーザーが自由に定義できる) JSON コンテンツです。 ここで 1 つ以上のドキュメントを挿入できます。 "src/main/java/com/microsoft/azure/cosmosdb/sample/Families.java" ファイルでは、ファミリ JSON ドキュメントが定義されています 

```java
public static Family getJohnsonFamilyDocument() {
     Family andersenFamily = new Family();
     andersenFamily.setId("Johnson" + System.currentTimeMillis());
     andersenFamily.setLastName("Johnson");

      Parent parent1 = new Parent();
      parent1.setFirstName("John");

      Parent parent2 = new Parent();
      parent2.setFirstName("Lili");

      return andersenFamily;
    }
```

## <a id="Query"></a>手順 8: Azure Cosmos DB リソースにクエリを実行する

Azure Cosmos DB では、各コレクションに格納された JSON ドキュメントに対する豊富な[クエリ](sql-api-sql-query.md)がサポートされています。 次のサンプル コードは、[queryDocuments](/java/api/com.microsoft.azure.cosmosdb.rx._async_document_client.querydocuments) メソッドが含まれている SQL 構文を使用して Azure Cosmos DB のドキュメントを照会する方法を示します。

```java
private void executeSimpleQueryAsyncAndRegisterListenerForResult(CountDownLatch completionLatch) 
{
    // Set some common query options
    FeedOptions queryOptions = new FeedOptions();
    queryOptions.setMaxItemCount(100);
    queryOptions.setEnableCrossPartitionQuery(true);

    String collectionLink = String.format("/dbs/%s/colls/%s", databaseName, collectionName);
    Observable<FeedResponse<Document>> queryObservable = client.queryDocuments(collectionLink,
           "SELECT * FROM Family WHERE Family.lastName = 'Andersen'", queryOptions);

    queryObservable.subscribe(queryResultPage -> {
            System.out.println("Got a page of query result with " +
            queryResultPage.getResults().size() + " document(s)"
            + " and request charge of " + queryResultPage.getRequestCharge());
    },
   // terminal error signal
        e -> {
          e.printStackTrace();
          completionLatch.countDown();
        },

   // terminal completion signal
         () -> {
            completionLatch.countDown();
         });
}
```

## <a id="Run"></a>手順 9: Java コンソール アプリケーションを実行する

コンソールからアプリケーションを実行するには、プロジェクト フォルダーに移動し、Maven を使用してコンパイルします。

```bash
mvn package
```

`mvn package` を実行すると、Maven から最新の Azure Cosmos DB ライブラリがダウンロードされ、`GetStarted-0.0.1-SNAPSHOT.jar` が生成されます。 次に、次のコードを実行してアプリを実行します。

```bash
mvn exec:java -DACCOUNT_HOST=<YOUR_COSMOS_DB_HOSTNAME> -DACCOUNT_KEY= <YOUR_COSMOS_DB_MASTER_KEY>
```

お疲れさまでした。 この NoSQL チュートリアルを終え、実用的な Java コンソール アプリケーションが完成しました。

## <a name="next-steps"></a>次の手順

このチュートリアルでは、Async Java SDK を使用して Java アプリを構築して Azure Cosmos DB SQL API データを管理する方法について説明しました。 次の記事に進むことができます。

> [!div class="nextstepaction"]
> [JavaScript SDK と Azure Cosmos DB を使用して Node.js コンソール アプリを構築する](sql-api-nodejs-get-started.md)

[keys]: media/sql-api-get-started/nosql-tutorial-keys.png
