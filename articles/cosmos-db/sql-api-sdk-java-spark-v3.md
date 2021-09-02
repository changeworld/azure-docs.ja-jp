---
title: SQL API 用 Azure Cosmos DB Apache Spark 3 OLTP コネクタ (プレビュー) のリリース ノートとリソース
description: リリース日、提供終了日、Azure Cosmos DB SQL Java SDK の各バージョン間の変更など、SQL API 用 Azure Cosmos DB Apache Spark 3 OLTP コネクタについて説明します。
author: anfeldma-ms
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: java
ms.topic: reference
ms.date: 06/21/2021
ms.author: anfeldma
ms.custom: devx-track-java
ms.openlocfilehash: 2107f607d7af3dbcf1a0bed91be2a1e9b9b7edcc
ms.sourcegitcommit: 30e3eaaa8852a2fe9c454c0dd1967d824e5d6f81
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/22/2021
ms.locfileid: "112466728"
---
# <a name="azure-cosmos-db-apache-spark-3-oltp-connector-for-core-sql-api-release-notes-and-resources"></a>Core (SQL) API 用 Azure Cosmos DB Apache Spark 3 OLTP コネクタのリリース ノートとリソース
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

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
> * [SQL](./sql-query-getting-started.md)
> * [Bulk Executor - .NET v2](sql-api-sdk-bulk-executor-dot-net.md)
> * [Bulk Executor - Java](sql-api-sdk-bulk-executor-java.md)

**Azure Cosmos DB Spark 3 OLTP コネクタ** では、SQL API を使用した Azure Cosmos DB の Apache Spark v3 サポートを提供しています。
[Azure Cosmos DB](introduction.md) は、開発者が SQL、MongoDB、Cassandra、Graph、Table などのさまざまな標準 API を使用してデータを操作できるようにするグローバル分散型データベース サービスです。

## <a name="documentation"></a>ドキュメント

- [作業の開始](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/cosmos/azure-cosmos-spark_3-1_2-12/docs/quick-start.md)
- [Catalog API](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/cosmos/azure-cosmos-spark_3-1_2-12/docs/catalog-api.md)
- [構成パラメーター参照](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/cosmos/azure-cosmos-spark_3-1_2-12/docs/configuration-reference.md)


## <a name="version-compatibility"></a>バージョンの互換性

| コネクタ     | Spark         | 最小 Java バージョン | サポートされているスケールのバージョン |
| ------------- | ------------- | -------------------- | -----------------------  |
| 4.0.0         | 3.1.1         |        8             | 2.12                     |

## <a name="download"></a>ダウンロード 

jar の maven 座標を使用して、Maven から Databricks Runtime 8 にSpark コネクタを自動インストールできます。`com.azure.cosmos.spark:azure-cosmos-spark_3-1_2-12:4.1.0`

SBT プロジェクトの Cosmos DB Spark コネクタに対して統合することもできます。
```scala
libraryDependencies += "com.azure.cosmos.spark" % "azure-cosmos-spark_3-1_2-12" % "4.1.0"
```

Cosmos DB Spark コネクタは、 [Maven Central リポジトリ](https://search.maven.org/artifact/com.azure.cosmos.spark/azure-cosmos-spark_3-1_2-12/)で利用できます。

### <a name="general"></a>全般

バグが発生した場合は、 [ここで](https://github.com/Azure/azure-sdk-for-java/issues/new)問題を報告してください。

新しい機能または変更された可能性のある変更を提案するには、バグの場合と同じ方法で問題を報告します。

[!INCLUDE[Changelog](~/azure-sdk-for-java-cosmos-db/sdk/cosmos/azure-cosmos-spark_3-1_2-12/CHANGELOG.md)]

## <a name="next-steps"></a>次のステップ

[Azure Cosmos DB Spark 3 OLTP コネクタを使用するためのクイックスタート ガイド](create-sql-api-spark.md)を確認してください。
