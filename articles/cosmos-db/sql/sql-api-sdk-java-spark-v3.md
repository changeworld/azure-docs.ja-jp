---
title: SQL API 用 Azure Cosmos DB Apache Spark 3 OLTP コネクタ (プレビュー) のリリース ノートとリソース
description: リリース日、提供終了日、Azure Cosmos DB SQL Java SDK の各バージョン間の変更など、SQL API 用 Azure Cosmos DB Apache Spark 3 OLTP コネクタについて説明します。
author: anfeldma-ms
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: java
ms.topic: reference
ms.date: 11/12/2021
ms.author: anfeldma
ms.custom: devx-track-java
ms.openlocfilehash: abb10485baebc0b7379ec650fd9eeb95ebadf326
ms.sourcegitcommit: 362359c2a00a6827353395416aae9db492005613
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/15/2021
ms.locfileid: "132491281"
---
# <a name="azure-cosmos-db-apache-spark-3-oltp-connector-for-core-sql-api-release-notes-and-resources"></a>Core (SQL) API 用 Azure Cosmos DB Apache Spark 3 OLTP コネクタのリリース ノートとリソース
[!INCLUDE[appliesto-sql-api](../includes/appliesto-sql-api.md)]

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
> * [Spark 3 OLTP コネクタ](sql-api-sdk-java-spark-v3.md)
> * [Spark 2 OLTP コネクタ](sql-api-sdk-java-spark.md)
> * [Python](sql-api-sdk-python.md)
> * [REST](/rest/api/cosmos-db/)
> * [REST リソース プロバイダー](/rest/api/cosmos-db-resource-provider/)
> * [SQL](sql-query-getting-started.md)
> * [Bulk Executor - .NET v2](sql-api-sdk-bulk-executor-dot-net.md)
> * [Bulk Executor - Java](sql-api-sdk-bulk-executor-java.md)

**Azure Cosmos DB OLTP Spark コネクタ** では、SQL API を使用した Apache Spark に対するサポートを Azure Cosmos DB で提供しています。 Azure Cosmos DB は、開発者が SQL、MongoDB、Cassandra、Graph、Table などのさまざまな標準 API を使用してデータを操作できるようにするグローバル分散型データベース サービスです。

エクスペリエンスを向上させる方法に関するフィードバックやアイデアがある場合は、[SDK GitHub リポジトリ](https://github.com/Azure/azure-sdk-for-java/issues/new)でイシューを作成してください

## <a name="documentation-links"></a>ドキュメント リンク

* [作業の開始](https://github.com/Azure/azure-sdk-for-java/blob/main/sdk/cosmos/azure-cosmos-spark_3-1_2-12/docs/quick-start.md)
* [Catalog API](https://github.com/Azure/azure-sdk-for-java/blob/main/sdk/cosmos/azure-cosmos-spark_3-1_2-12/docs/catalog-api.md)
* [構成パラメーター参照](https://github.com/Azure/azure-sdk-for-java/blob/main/sdk/cosmos/azure-cosmos-spark_3-1_2-12/docs/configuration-reference.md)

## <a name="version-compatibility"></a>バージョンの互換性

| コネクタ     | 最小 Spark バージョン | 最小 Java バージョン | サポートされているスケールのバージョン | サポートされている Databricks ランタイム |
| ------------- | --------------------- | -------------------- | -----------------------  | ----------------------------- |
| 4.4.0         | 3.1.1                 | 8                    | 2.12                     | 8.\*、9.\*                    |
| 4.3.1         | 3.1.1                 | 8                    | 2.12                     | 8.\*、9.\*                    |
| 4.3.0         | 3.1.1                 | 8                    | 2.12                     | 8.\*、9.\*                    |
| 4.2.0         | 3.1.1                 | 8                    | 2.12                     | 8.\*                          |
| 4.1.0         | 3.1.1                 | 8                    | 2.12                     | 8.\*                          |
| 4.0.0         | 3.1.1                 | 8                    | 2.12                     | 8.\*                          |
| 4.0.0-beta.3  | 3.1.1                 | 8                    | 2.12                     | 8.\*                          |
| 4.0.0-beta.2  | 3.1.1                 | 8                    | 2.12                     | 8.\*                          |
| 4.0.0-beta.1  | 3.1.1                 | 8                    | 2.12                     | 8.\*                          |

## <a name="download"></a>ダウンロード

jar の maven 座標を使用して、Maven から Databricks Runtime 8 にSpark コネクタを自動インストールできます。`com.azure.cosmos.spark:azure-cosmos-spark_3-1_2-12:4.4.0`

SBT プロジェクトの Cosmos DB Spark コネクタに対して統合することもできます。

```scala
libraryDependencies += "com.azure.cosmos.spark" % "azure-cosmos-spark_3-1_2-12" % "4.4.0"
```

Azure Cosmos DB Spark コネクタは、[Maven Central リポジトリ](https://search.maven.org/search?q=g:com.azure.cosmos.spark)で利用できます。

バグが発生した場合や、機能の変更を提案する場合は、[問題を報告してください](https://github.com/Azure/azure-sdk-for-java/issues/new)。 

## <a name="next-steps"></a>次のステップ

[Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/) の詳細をご覧ください。

詳しくは、[Apache Spark](https://spark.apache.org/) に関するページをご覧ください。