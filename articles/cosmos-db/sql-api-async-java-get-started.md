---
title: Azure Cosmos DB Async Java SDK を使用して Java アプリケーションを構築する | Microsoft Docs
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
ms.openlocfilehash: 2ea09b8f138f9cb6729af0e0aff1500caccde10f
ms.sourcegitcommit: 1478591671a0d5f73e75aa3fb1143e59f4b04e6a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/19/2018
ms.locfileid: "39163310"
---
# <a name="build-a-java-application-by-using-azure-cosmos-db-async-java-sdk"></a>Azure Cosmos DB Async Java SDK を使用して Java アプリケーションを構築する 

> [!div class="op_single_selector"]
> * [.NET](sql-api-get-started.md)
> * [.NET Core](sql-api-dotnetcore-get-started.md)
> * [MongoDB 用 Node.js](mongodb-samples.md)
> * [Node.js](sql-api-nodejs-get-started.md)
> * [Java](sql-api-java-get-started.md)
> * [Asyn Java](sql-api-async-java-get-started.md)
>  
> 

Azure Cosmos DB は、グローバル分散型のマルチモデル データベースです。 このチュートリアルでは、Async Java アプリケーションを使用して、Azure Cosmos DB SQL API アカウントでデータを格納したりデータにアクセスしたりする方法について説明します。 

説明内容は次のとおりです。

* Azure Cosmos DB アカウントを作成し、アカウントに接続する
* ソリューションを構成する
* コレクションを作成する
* JSON ドキュメントを作成する
* コレクションをクエリする

それでは始めましょう。

## <a name="prerequisites"></a>前提条件
次のものを用意してください。

* アクティブな Azure アカウントアカウントがない場合、Azure 試用版にサインアップして、最大 10 件の無料 Mobile Apps を入手できます。 お持ちでない場合は、 [無料アカウント](https://azure.microsoft.com/free/)にサインアップしてください。 

  [!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]

* [Git](https://git-scm.com/downloads)。
* [Java Development Kit (JDK) 8 以降](http://www.oracle.com/technetwork/java/javase/downloads/index.html)。
* [Maven](http://maven.apache.org/download.cgi)。

## <a name="step-1-create-an-azure-cosmos-db-account"></a>手順 1: Azure Cosmos DB アカウントを作成する
それでは、Azure Cosmos DB アカウントを作成してみましょう。 使用するアカウントが既にある場合は、「[GitHub プロジェクトを複製する](#GitClone)」に進んでかまいません。 Azure Cosmos DB Emulator を使用する場合は、[DB Emulator](local-emulator.md) に関する記事に記載されている手順に従ってエミュレーターをセットアップし、「[GitHub プロジェクトを複製する](#GitClone)」に進んでください。

[!INCLUDE [cosmos-db-create-dbaccount](../../includes/cosmos-db-create-dbaccount.md)]

## <a id="GitClone"></a>手順 2: GitHub プロジェクトを複製する
最初に、[Azure Cosmos DB と Java の概要](https://github.com/Azure-Samples/azure-cosmos-db-sql-api-async-java-getting-started)に関する GitHub リポジトリを複製します。 たとえば、ローカル ディレクトリで次のコマンドを実行し、サンプル プロジェクトをローカルに取得します。

```bash
git clone https://github.com/Azure-Samples/azure-cosmos-db-sql-api-async-java-getting-started.git

cd azure-cosmos-db-sql-api-async-java-getting-started
cd azure-cosmosdb-get-started

```
ディレクトリには、プロジェクトの `pom.xml` のほか、Java ソース コードが含まれた `src/main/java/com/microsoft/azure/cosmosdb/sample` フォルダーがあります。このフォルダーには、ドキュメントの作成やコレクション内のデータのクエリなど、Azure Cosmos DB での単純な操作の実行方法を示す `Main.java` が含まれています。 `pom.xml` には、[Azure Cosmos DB Java SDK on Maven](https://mvnrepository.com/artifact/com.microsoft.azure/azure-documentdb) の依存関係が含まれています。

```xml
<dependency>
  <groupId>com.microsoft.azure</groupId>
  <artifactId>azure-documentdb</artifactId>
  <version>2.0.0</version>
</dependency>
```

## <a id="Connect"></a>手順 3: Azure Cosmos DB アカウントに接続する
次に、[Azure Portal](https://portal.azure.com) に戻り、エンドポイントとプライマリ マスター キーを取得します。 Azure Cosmos DB のエンドポイントとプライマリ キーは、アプリケーションが接続先を認識し、Azure Cosmos DB がアプリケーションの接続を信頼するために必要です。 `AccountSettings.java` ファイルは、プライマリ キーと URI の値を保持します。 

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

![Java コンソール アプリケーションを作成するために NoSQL チュートリアルで使用される Azure Portal のスクリーン ショット。 アクティブなハブが強調表示され、[Azure Cosmos DB account] (Azure Cosmos DB アカウント) ブレードで [キー] ボタンが強調表示され、[キー] ブレードで URI 値、プライマリ キー値、およびセカンダリ キー値が強調表示されている Azure Cosmos DB アカウントを示します。][keys]

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

[ドキュメント](sql-api-resources.md#documents)は、DocumentClient クラスの createDocument メソッドを使用して作成できます。 ドキュメントは、ユーザー定義の (ユーザーが自由に定義できる) JSON コンテンツです。 ここで 1 つ以上のドキュメントを挿入できます。 "src/main/java/com/microsoft/azure/cosmosdb/sample/Families.java" ファイルは、ファミリ JSON ドキュメントを定義します 

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

## <a id="Run"></a>手順 9: Java コンソール アプリケーションをまとめて実行する
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
* Java Web アプリケーションのチュートリアルが必要な場合、 [Azure Cosmos DB を使用した Java Web アプリケーションの作成](sql-api-java-application.md)に関するページを参照してください。
* [Azure Cosmos DB アカウントを監視する](monitor-accounts.md)方法を学習します。
* [クエリのプレイ グラウンド](https://www.documentdb.com/sql/demo)でサンプル データセットに対してクエリを実行します。

[keys]: media/sql-api-get-started/nosql-tutorial-keys.png
