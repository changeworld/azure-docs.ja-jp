---
title: チュートリアル:Java アプリをビルドして Azure Cosmos DB Cassandra API アカウントを作成する
description: このチュートリアルでは、Java アプリケーションを使用して、Cassandra API アカウントの作成、データベース (キースペースとも呼ばれます) の追加、そのアカウントへのテーブルの追加を行う方法について説明します。
author: TheovanKraay
ms.author: thvankra
ms.reviewer: sngun
ms.service: cosmos-db
ms.subservice: cosmosdb-cassandra
ms.topic: tutorial
ms.date: 12/06/2018
ms.custom: seodec18, devx-track-java
ms.openlocfilehash: abe6bcf71ed8c83bfed688a67d5a6f5de15c28df
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/13/2021
ms.locfileid: "121788208"
---
# <a name="tutorial-create-a-cassandra-api-account-in-azure-cosmos-db-by-using-a-java-application-to-store-keyvalue-data"></a>チュートリアル:Java アプリケーションを使用して Azure Cosmos DB に Cassandra API アカウントを作成し、キーと値のデータを格納する
[!INCLUDE[appliesto-cassandra-api](../includes/appliesto-cassandra-api.md)]

開発者は、キー/値ペアを使用するアプリケーションを持っていることがあります。 Azure Cosmos DB の Cassandra API アカウントを使用して、キーと値のデータを格納できます。 このチュートリアルでは、Java アプリケーションを使用して、Azure Cosmos DB に Cassandra API アカウントを作成し、データベース (キースペースとも呼ばれます) を追加してテーブルを追加する方法について説明します。 Java アプリケーションでは、[Java ドライバー](https://github.com/datastax/java-driver)を使用して、ユーザー ID、ユーザー名、ユーザーの住所などの詳細情報を格納するユーザー データベースを作成します。  

このチュートリアルに含まれるタスクは次のとおりです。

> [!div class="checklist"]
> * Cassandra データベース アカウントを作成する
> * アカウントの接続文字列を取得する
> * Maven プロジェクトと依存関係を作成する
> * データベースとテーブルを追加する
> * アプリを実行する

## <a name="prerequisites"></a>前提条件 

* Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) を作成してください。 

* 最新バージョンの [Java Development Kit (JDK)](/java/azure/jdk/) を入手します。 

* [Maven](https://maven.apache.org/) バイナリ アーカイブを[ダウンロード](https://maven.apache.org/download.cgi)して[インストール](https://maven.apache.org/install.html)します。 
  - Ubuntu で `apt-get install maven` を実行して Maven をインストールします。 

## <a name="create-a-database-account"></a>データベース アカウントの作成 

[!INCLUDE [cosmos-db-create-dbaccount-cassandra](../includes/cosmos-db-create-dbaccount-cassandra.md)]

## <a name="get-the-connection-details-of-your-account"></a>アカウントの接続の詳細を取得する  

Azure portal から接続文字列情報を取得し、Java の構成ファイルにそれをコピーします。 アプリはこの接続文字列によって、ホストされているデータベースと通信できます。 

1. [Azure portal](https://portal.azure.com/) でお客様の Azure Cosmos アカウントに移動します。 

2. **[接続文字列]** ウィンドウを開きます。  

3. 次の手順で使用するので、 **[コンタクト ポイント]** 、 **[ポート]** 、 **[ユーザー名]** 、 **[プライマリ パスワード]** の値をコピーします。

## <a name="create-the-project-and-the-dependencies"></a>プロジェクトと依存関係を作成する 

この記事でお客様が使用する Java のサンプル プロジェクトは、GitHub でホストされています。 このドキュメントの手順を実行するか、[azure-cosmos-db-cassandra-java-getting-started](https://github.com/Azure-Samples/azure-cosmos-db-cassandra-java-getting-started) リポジトリからサンプルをダウンロードすることができます。 

ファイルをダウンロードしたら、`java-examples\src\main\resources\config.properties` ファイル内の接続文字列情報を更新して、それを実行します。  

```java
cassandra_host=<FILLME_with_CONTACT POINT> 
cassandra_port = 10350 
cassandra_username=<FILLME_with_USERNAME> 
cassandra_password=<FILLME_with_PRIMARY PASSWORD> 
```

サンプルをゼロからビルドするには、次の手順を使用します。 

1. ターミナルまたはコマンド プロンプトから、Cassandra-demo という名前で新しい Maven プロジェクトを作成します。 

   ```bash
   mvn archetype:generate -DgroupId=com.azure.cosmosdb.cassandra -DartifactId=cassandra-demo -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false 
   ```
 
2. `cassandra-demo` フォルダーを検索します。 テキスト エディターを使用して、生成された `pom.xml` ファイルを開きます。 

   Cassandra 依存関係を追加し、[pom.xml](https://github.com/Azure-Samples/azure-cosmos-db-cassandra-java-getting-started/blob/main/pom.xml) ファイルで示されているように、お客様のプロジェクトに必要なプラグインをビルドします。  

3. `cassandra-demo\src\main` フォルダーの下に、`resources` という名前の新しいフォルダーを作成します。  resources フォルダーの下に、config.properties ファイルと log4j.properties ファイルを追加します。

   - [config.properties](https://github.com/Azure-Samples/azure-cosmos-db-cassandra-java-getting-started/blob/main/src/main/resources/config.properties) ファイルには、Cassandra API アカウントの接続エンドポイントとキーの値が格納されます。 
   
   - [log4j.properties](https://github.com/Azure-Samples/azure-cosmos-db-cassandra-java-getting-started/blob/main/src/main/resources/log4j.properties) ファイルでは、Cassandra API との対話に必要なログ記録のレベルを定義します。  

4. `src/main/java/com/azure/cosmosdb/cassandra/` フォルダーを参照します。 cassandra フォルダー内に、`utils` という名前の別のフォルダーを作成します。 新しいフォルダーには、Cassandra API アカウントに接続するために必要なユーティリティ クラスが格納されます。 

   クラスターを作成し、Cassandra セッションを開いたり閉じたりするには、[CassandraUtils](https://github.com/Azure-Samples/azure-cosmos-db-cassandra-java-getting-started/blob/main/src/main/java/com/azure/cosmosdb/cassandra/util/CassandraUtils.java) クラスを追加します。 クラスターは Azure Cosmos DB の Cassandra API アカウントに接続し、アクセスするセッションを返します。 config.properties ファイルから接続文字列の情報を読み取るには、[Configurations](https://github.com/Azure-Samples/azure-cosmos-db-cassandra-java-getting-started/blob/main/src/main/java/com/azure/cosmosdb/cassandra/util/Configurations.java) クラスを使用します。 

5. Java のサンプルでは、ユーザー名、ユーザー ID、ユーザーの住所などのユーザー情報が含まれるデータベースを作成します。 main 関数でユーザーの詳細にアクセスするには、get メソッドと set メソッドを定義する必要があります。
 
   get メソッドと set メソッドを含む [User.java](https://github.com/Azure-Samples/azure-cosmos-db-cassandra-java-getting-started/blob/main/src/main/java/com/azure/cosmosdb/cassandra/examples/UserProfile.java) クラスを `src/main/java/com/azure/cosmosdb/cassandra/` フォルダーに作成します。 

## <a name="add-a-database-and-a-table"></a>データベースとテーブルを追加する  

このセクションでは、CQL を使用してデータベース (キースペース) とテーブルを追加する方法について説明します。

1. `src\main\java\com\azure\cosmosdb\cassandra` フォルダーの下に、`repository` という名前の新しいフォルダーを作成します。 

2. `UserRepository` Java クラスを作成し、次のコードをそれに追加します。 

   ```java
   package com.azure.cosmosdb.cassandra.repository; 
   import java.util.List; 
   import com.datastax.driver.core.BoundStatement; 
   import com.datastax.driver.core.PreparedStatement; 
   import com.datastax.driver.core.Row; 
   import com.datastax.driver.core.Session; 
   import org.slf4j.Logger; 
   import org.slf4j.LoggerFactory; 
   
   /** 
    * Create a Cassandra session 
    */ 
   public class UserRepository { 
   
       private static final Logger LOGGER = LoggerFactory.getLogger(UserRepository.class); 
       private Session session; 
       public UserRepository(Session session) { 
           this.session = session; 
       } 
   
       /** 
       * Create keyspace uprofile in cassandra DB 
        */ 
   
       public void createKeyspace() { 
            final String query = "CREATE KEYSPACE IF NOT EXISTS uprofile WITH REPLICATION = { 'class' : 'NetworkTopologyStrategy', 'datacenter1' : 1 }"; 
           session.execute(query); 
           LOGGER.info("Created keyspace 'uprofile'"); 
       } 
   
       /** 
        * Create user table in cassandra DB 
        */ 
   
       public void createTable() { 
           final String query = "CREATE TABLE IF NOT EXISTS uprofile.user (user_id int PRIMARY KEY, user_name text, user_bcity text)"; 
           session.execute(query); 
           LOGGER.info("Created table 'user'"); 
       } 
   } 
   ```

3. `src\main\java\com\azure\cosmosdb\cassandra` フォルダーを検索し、`examples` という名前の新しいサブフォルダーを作成します。

4. `UserProfile` Java クラスを作成します。 このクラスには、前に定義した createKeyspace および createTable メソッドを呼び出す main メソッドが含まれます。 

   ```java
   package com.azure.cosmosdb.cassandra.examples; 
   import java.io.IOException; 
   import com.azure.cosmosdb.cassandra.repository.UserRepository; 
   import com.azure.cosmosdb.cassandra.util.CassandraUtils; 
   import com.datastax.driver.core.PreparedStatement; 
   import com.datastax.driver.core.Session; 
   import org.slf4j.Logger; 
   import org.slf4j.LoggerFactory; 
   
   /** 
    * Example class which will demonstrate following operations on Cassandra Database on CosmosDB 
    * - Create Keyspace 
    * - Create Table 
    * - Insert Rows 
    * - Select all data from a table 
    * - Select a row from a table 
    */ 
   
   public class UserProfile { 
   
       private static final Logger LOGGER = LoggerFactory.getLogger(UserProfile.class); 
       public static void main(String[] s) throws Exception { 
           CassandraUtils utils = new CassandraUtils(); 
           Session cassandraSession = utils.getSession(); 
   
           try { 
               UserRepository repository = new UserRepository(cassandraSession); 
               //Create keyspace in cassandra database 
               repository.createKeyspace(); 
               //Create table in cassandra database 
               repository.createTable(); 
   
           } finally { 
               utils.close(); 
               LOGGER.info("Please delete your table after verifying the presence of the data in portal or from CQL"); 
           } 
       } 
   } 
   ```
 
## <a name="run-the-app"></a>アプリを実行する 

1. コマンド プロンプトまたはターミナル ウィンドウを開きます。 次のコード ブロックを貼り付けます。 

   このコードは、プロジェクトを作成したフォルダー パスにディレクトリを変更 (cd) します。 次に、`mvn clean install` コマンドを実行して、ターゲット フォルダー内に `cosmosdb-cassandra-examples.jar` ファイルを生成します。 最後に、Java アプリケーションを実行します。

   ```bash
   cd cassandra-demo

   mvn clean install 

   java -cp target/cosmosdb-cassandra-examples.jar com.azure.cosmosdb.cassandra.examples.UserProfile 
   ```

   ターミナル ウィンドウに、キースペースとテーブルが作成されたという通知が表示されます。 
   
2. Azure portal で **データ エクスプローラー** を開き、キースペースとテーブルが作成されたことを確認します。

## <a name="next-steps"></a>次のステップ

このチュートリアルでは、Java アプリケーションを使用して、Azure Cosmos DB の Cassandra API アカウント、データベース、テーブルを作成する方法を学習しました。 次の記事に進むことができます。

> [!div class="nextstepaction"]
> [Cassandra API テーブルにサンプル データを読み込む](load-data-table.md)
