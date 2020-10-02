---
title: SQL API 用 Spring Data Azure Cosmos DB v3 のリリース ノートとリソース
description: リリース日、提供終了日、Azure Cosmos DB SQL Async Java SDK の各バージョン間の変更など、SQL API 用 Spring Data Azure Cosmos DB v3 について説明します。
author: anfeldma-ms
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: java
ms.topic: reference
ms.date: 08/18/2020
ms.author: anfeldma
ms.custom: devx-track-java
ms.openlocfilehash: 9e5b92918d93109183740be555bb805877862407
ms.sourcegitcommit: 0fd1f3fe7817ad44d878d580ec167e1508051795
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/19/2020
ms.locfileid: "90817872"
---
# <a name="spring-data-azure-cosmos-db-v3-for-core-sql-api-release-notes-and-resources"></a>コア (SQL) API 用 Spring Data Azure Cosmos DB v3:リリース ノートとリソース
> [!div class="op_single_selector"]
> * [.NET SDK v3](sql-api-sdk-dotnet-standard.md)
> * [.NET SDK v2](sql-api-sdk-dotnet.md)
> * [.NET Core SDK v2](sql-api-sdk-dotnet-core.md)
> * [.NET Change Feed SDK v2](sql-api-sdk-dotnet-changefeed.md)
> * [Node.js](sql-api-sdk-node.md)
> * [Java SDK v4](sql-api-sdk-java-v4.md)
> * [Async Java SDK v2](sql-api-sdk-async-java.md)
> * [Sync Java SDK v2](sql-api-sdk-java.md)
> * [Spring Data v2](sql-api-sdk-java-spring-v2.md)
> * [Spring Data v3](sql-api-sdk-java-spring-v3.md)
> * [Spark コネクタ](sql-api-sdk-java-spark.md)
> * [Python](sql-api-sdk-python.md)
> * [REST](/rest/api/cosmos-db/)
> * [REST リソース プロバイダー](/rest/api/cosmos-db-resource-provider/)
> * [SQL](sql-api-query-reference.md)
> * [Bulk Executor - .NET v2](sql-api-sdk-bulk-executor-dot-net.md)
> * [Bulk Executor - Java](sql-api-sdk-bulk-executor-java.md)

コア (SQL) 用 Spring Data Azure Cosmos DB バージョン 3 により、開発者は Spring アプリケーションで Azure Cosmos DB を使用できます。 Spring Data Azure Cosmos DB では、データベースとコレクションの操作、ドキュメントの操作、クエリの発行を行うための Spring Data インターフェイスが公開されています。 同期 API と非同期 (リアクティブ) API の両方とも、同じ Maven 成果物でサポートされています。 

Spring Data Azure Cosmos DB は、Spring Data フレームワークに依存しています。 Azure Cosmos DB SDK チームから、Spring Data バージョン 2.2 および 2.3 用の Maven 成果物がリリースされます。

[Spring Framework](https://spring.io/projects/spring-framework) は、Java アプリケーション開発を効率化するプログラミングおよび構成モデルです。 Spring では依存関係の挿入を使用して、アプリケーションの "プラミング" が効率化されます。 アプリケーションの構築とテストが簡潔になるため、多くの開発者が Spring を好んでいます。 [Spring Boot](https://spring.io/projects/spring-boot) は、Web アプリとマイクロサービスの開発を目的として、このプラミングの処理を拡張したものです。 [Spring Data](https://spring.io/projects/spring-data) は、Spring または Spring Boot アプリケーションのコンテキストから Azure Cosmos DB などのデータストアにアクセスするためのプログラミング モデルおよびフレームワークです。 

Spring Data Azure Cosmos DB は [Azure Spring Cloud](https://azure.microsoft.com/services/spring-cloud/) アプリケーションで使用できます。

> [!IMPORTANT]  
> これらのリリース ノートは、Spring Data Azure Cosmos DB のバージョン 3 に関するものです。 [バージョン 2 のリリース ノートについてはこちら](sql-api-sdk-java-spring-v2.md)を参照してください。 
>
> Spring Data Azure Cosmos DB では、SQL API のみがサポートされています。
>
> 他の Azure Cosmos DB API での Spring Data については、次の記事を参照してください。
> * [Azure Cosmos DB での Apache Cassandra 用 Spring Data](https://docs.microsoft.com/azure/developer/java/spring-framework/configure-spring-data-apache-cassandra-with-cosmos-db)
> * [Azure Cosmos DB での Spring Data MongoDB](https://docs.microsoft.com/azure/developer/java/spring-framework/configure-spring-data-mongodb-with-cosmos-db)
> * [Azure Cosmos DB での Spring Data Gremlin](https://docs.microsoft.com/azure/developer/java/spring-framework/configure-spring-data-gremlin-java-app-with-cosmos-db)
>

## <a name="start-here"></a>ここから開始

# <a name="explore"></a>[探索](#tab/explore)

<img src="media/sql-api-sdk-java-spring-v3/up-arrow.png" alt="explore the tabs above" width="80"/>

#### <a name="these-tabs-contain-basic-spring-data-azure-cosmos-db-samples"></a>これらのタブには、Spring Data Azure Cosmos DB の基本的なサンプルが含まれています。

# <a name="pomxml"></a>[pom.xml](#tab/pom)

### <a name="configure-dependencies"></a>依存関係を構成する

  ```xml
  <dependency>
      <groupId>com.azure</groupId>
      <artifactId>azure-spring-data-cosmos</artifactId>
      <version>latest</version>
  </dependency>
  ```

# <a name="connect"></a>[のインスタンスに接続するときには、](#tab/connect)

### <a name="connect"></a>接続する

Azure Cosmos DB アカウントとコンテナーの詳細を指定します。 Spring Data Azure Cosmos DB は、クライアントを自動的に作成して、コンテナーに接続します。

[application.properties](https://github.com/Azure-Samples/azure-spring-data-cosmos-java-sql-api-getting-started/blob/main/azure-spring-data-cosmos-java-getting-started/src/main/resources/application.properties):
```
cosmos.uri=${ACCOUNT_HOST}
cosmos.key=${ACCOUNT_KEY}
cosmos.secondaryKey=${SECONDARY_ACCOUNT_KEY}

dynamic.collection.name=spel-property-collection
# Populate query metrics
cosmos.queryMetricsEnabled=true
```

# <a name="doc-ops"></a>[ドキュメント操作](#tab/docs)

### <a name="document-operations"></a>ドキュメントの操作

作成: 
:::code language="java" source="~/spring-data-azure-cosmos-db-sql-tutorial/azure-spring-data-cosmos-java-getting-started/src/main/java/com/azure/spring/data/cosmostutorial/SampleApplication.java" ID="Create":::

削除:
:::code language="java" source="~/spring-data-azure-cosmos-db-sql-tutorial/azure-spring-data-cosmos-java-getting-started/src/main/java/com/azure/spring/data/cosmostutorial/SampleApplication.java" ID="Delete":::

# <a name="query"></a>[クエリ](#tab/queries)

クエリ:
:::code language="java" source="~/spring-data-azure-cosmos-db-sql-tutorial/azure-spring-data-cosmos-java-getting-started/src/main/java/com/azure/spring/data/cosmostutorial/SampleApplication.java" ID="Query":::

---

## <a name="resources"></a>リソース

* **SDK に投稿する**:[GitHub の Spring Data Azure Cosmos DB リポジトリ](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/cosmos/azure-spring-data-cosmos)

* **チュートリアル**:[GitHub の Spring Data Azure Cosmos DB チュートリアル](https://github.com/Azure-Samples/azure-spring-data-cosmos-java-sql-api-getting-started) 

## <a name="release-history"></a>リリース履歴

### <a name="300-beta2-september-17-2020"></a>3.0.0-beta.2 (2020 年 9 月 17 日)

#### <a name="new-features"></a>新機能

* 成果物 ID が `azure-spring-data-cosmos` に更新されました。
* azure-cosmos の依存関係が `4.5.0` に更新されました。
* ネイティブ クエリでの `Query Annotation` のサポート。
* Java 11 のサポート。
* `@Container` 注釈の `partitionKeyPath` フィールドを公開することによって、入れ子になったパーティション キーのサポートを追加しました。
* リポジトリ API を定義するときに `top` と `first` を使用できるようにするクエリの種類 `limit` のサポートが追加されました。

#### <a name="key-bug-fixes"></a>主要なバグ修正

* `@GeneratedValue` 注釈と共に使用した場合の入れ子になったパーティション キーのバグが修正されました。

### <a name="300-beta1-august-17-2020"></a>3.0.0-beta.1 (2020 年 8 月 17 日)

#### <a name="new-features"></a>新機能

* グループ ID が `com.azure` に更新されました。
* 成果物 ID が `azure-spring-data-2-3-cosmos` に更新されました。
* azure-cosmos SDK の依存関係が `4.3.2-beta.2` に更新されました。
* エンティティの監査のサポートが追加されました: `createdBy`、`createdDate`、`lastModifiedBy`、および `lastModifiedDate` の注釈付きフィールドの自動管理。
* `String` 型の ID フィールドの自動 ID 生成に対する `@GeneratedValue` 注釈のサポートが追加されました。
* 複数のデータベースで使用する単一の Azure Cosmos DB アカウントおよび複数のデータベースで使用する複数の Azure Cosmos DB アカウントのための複数データベース構成のサポートが追加されました。
* 任意の文字列フィールドでの `@Version` 注釈のサポートが追加されました。
* 同期 API の戻り値の型が、`List` ではなく `Iterable` に更新されました。
* Azure Cosmos DB SDK から `@Configuration` クラスに Spring Bean として `CosmosClientBuilder` を公開しました。
* クエリ メトリックと応答診断プロセッサの実装を含むように、`CosmosConfig` が更新されました。
* 単一結果クエリに対して `Optional` データ型を返す機能のサポートが追加されました。

#### <a name="renames"></a>名前の変更

* `CosmosDbFactory` から `CosmosFactory`。
* `CosmosDBConfig` から `CosmosConfig`。
* `CosmosDBAccessException` から `CosmosAccessException`。
* `Document` 注釈から `Container` 注釈。
* `DocumentIndexingPolicy` 注釈から `CosmosIndexingPolicy` 注釈。
* `DocumentQuery` から `CosmosQuery`。
* application.properties フラグ `populateQueryMetrics` から `queryMetricsEnabled`。

#### <a name="key-bug-fixes"></a>主要なバグ修正

* Netty I/O スレッドがブロックされないようにするための、`Parallel` スレッドへの診断ログ タスクのスケジュール。
* 削除操作でのオプティミスティック ロックを修正しました。
* `IN` 句に対するクエリのエスケープに関する問題を修正しました。
* `@Id` に対して `long` データ型を許可することで、問題を修正しました。
* `@PartitionKey` 注釈のデータ型として `boolean`、`long`、`int`、`double` を許可することで、問題を修正しました。
* 大文字と小文字が区別されないクエリに対する `IgnoreCase` および `AllIgnoreCase` キーワードを修正しました。
* コンテナーを自動的に作成するときの、既定の要求ユニットの値 4,000 を削除しました。

## <a name="faq"></a>よく寄せられる質問

[!INCLUDE [cosmos-db-sdk-faq](../../includes/cosmos-db-sdk-faq.md)]

## <a name="next-steps"></a>次のステップ

[Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/) の詳細をご覧ください。

[Spring Framework](https://spring.io/projects/spring-framework) の詳細を確認してください。

[Spring Boot](https://spring.io/projects/spring-boot) の詳細を確認してください。

[Spring Data](https://spring.io/projects/spring-data) の詳細を確認してください。