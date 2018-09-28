---
title: Java アプリケーションを使用して、Azure Cosmos DB の Cassandra API テーブルにサンプル データを読み込む | Microsoft Docs
description: この記事では、java アプリケーションを使用して Azure Cosmos DB の Cassandra API アカウント内のテーブルにサンプル ユーザー データを読み込む方法を示します。
services: cosmos-db
author: kanshiG
ms.service: cosmos-db
ms.component: cosmosdb-cassandra
ms.topic: tutorial
ms.date: 09/24/2018
ms.author: govindk
ms.reviewer: sngun
ms.openlocfilehash: d659004e94c51f173bc1e9ae42fd9c62ae45912f
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/24/2018
ms.locfileid: "46972540"
---
# <a name="load-sample-data-into-an-azure-cosmos-db-cassandra-api-table"></a>Azure Cosmos DB の Cassandra API のテーブルにサンプル データを読み込む

このチュートリアルでは、Java アプリケーションを使用して Azure Cosmos DB の Cassandra API アカウント内のテーブルにサンプル ユーザー データを読み込む方法を示します。 Java アプリケーションは [Datastax Java ドライバー](https://github.com/datastax/java-driver)を使用して、ユーザー ID やユーザー名、ユーザー所在地などのユーザー データを読み込みます。 

このチュートリアルに含まれるタスクは次のとおりです。

> [!div class="checklist"]
> * Cassandra テーブルへのデータの読み込み
> * アプリの実行

## <a name="prerequisites"></a>前提条件

* この記事では、マルチパートのチュートリアルの一部です｡ このドキュメントに取り組むにあたっては､必ず事前に[Cassandra API アカウントとキースペース､テーブルを作成](create-cassandra-api-account-java.md)しておいてください   

## <a name="load-data-into-the-table"></a>テーブルにデータを読み込む

"Src\main\java\com\azure\cosmosdb\cassandra"フォルダーにある "UserRepository.java" ファイルを開いて、user_id、user_name、および user_bcity フィールドをテーブルに挿入するコードを付加します。

```java
/**
* Insert a row into user table
*
* @param id   user_id
* @param name user_name
* @param city user_bcity
*/
public void insertUser(PreparedStatement statement, int id, String name, String city) {
        BoundStatement boundStatement = new BoundStatement(statement);
        session.execute(boundStatement.bind(id, name, city));
}

/**
* Create a PrepareStatement to insert a row to user table
*
* @return PreparedStatement
*/
public PreparedStatement prepareInsertStatement() {
    final String insertStatement = "INSERT INTO  uprofile.user (user_id, user_name , user_bcity) VALUES (?,?,?)";
    return session.prepare(insertStatement);
}
```
 
"Src\main\java\com\azure\cosmosdb\cassandra"フォルダーにある "UserProfile.java" ファイルを開きます。 このクラスには、以前に定義した createKeyspace と createTable メソッドを呼び出すメイン メソッドが含まれています。 これで、Cassandra API テーブルにサンプル データを挿入する次のコードを追加します。

```java
//Insert rows into user table
PreparedStatement preparedStatement = repository.prepareInsertStatement();
    repository.insertUser(preparedStatement, 1, "JohnH", "Seattle");
    repository.insertUser(preparedStatement, 2, "EricK", "Spokane");
    repository.insertUser(preparedStatement, 3, "MatthewP", "Tacoma");
    repository.insertUser(preparedStatement, 4, "DavidA", "Renton");
    repository.insertUser(preparedStatement, 5, "PeterS", "Everett");
```

## <a name="run-the-app"></a>アプリの実行

コマンド プロンプトまたはターミナル ウィンドウを開いて、プロジェクトを作成した場所にフォルダーのパスを変更します。 「mvn clean install」コマンドを実行することでターゲット フォルダー内に cassandra-examples.jar ファイルを生成し、アプリケーションを実行します。 

```bash
cd "cassandra-demo"

mvn clean install

java -cp target/cosmosdb-cassandra-examples.jar com.azure.cosmosdb.cassandra.examples.UserProfile
```

これで､Azure portal でデータ エクスプローラーを開きユーザー情報がテーブルに追加されていることを確認できます｡
    
## <a name="next-steps"></a>次の手順

このチュートリアルでは、Azure Cosmos DB の Cassandra API アカウントにサンプル データを読み込む方法を学びました｡ これで次の記事に進むことができます。

> [!div class="nextstepaction"]
> [Cassandra API アカウントからデータを照会する](cassandra-api-query-data.md)
