---
title: Spark からの Azure Cosmos DB Cassandra API にデータをアップサートする
description: この記事では、Spark から Azure Cosmos DB Cassandra API のテーブルにアップサートする方法について説明します。
author: TheovanKraay
ms.author: thvankra
ms.reviewer: sngun
ms.service: cosmos-db
ms.subservice: cosmosdb-cassandra
ms.topic: how-to
ms.date: 09/24/2018
ms.openlocfilehash: e4858ca9532c65cd1a4a0a3c8cffea2f8b8463cd
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/13/2021
ms.locfileid: "121779448"
---
# <a name="upsert-data-into-azure-cosmos-db-cassandra-api-from-spark"></a>Spark からの Azure Cosmos DB Cassandra API にデータをアップサートする
[!INCLUDE[appliesto-cassandra-api](../includes/appliesto-cassandra-api.md)]

この記事では、Spark から Azure Cosmos DB Cassandra API にデータをアップサートする方法について説明します。

## <a name="cassandra-api-configuration"></a>Cassandra API の構成

```scala
import org.apache.spark.sql.cassandra._
//Spark connector
import com.datastax.spark.connector._
import com.datastax.spark.connector.cql.CassandraConnector

//if using Spark 2.x, CosmosDB library for multiple retry
//import com.microsoft.azure.cosmosdb.cassandra

//Connection-related
spark.conf.set("spark.cassandra.connection.host","YOUR_ACCOUNT_NAME.cassandra.cosmosdb.azure.com")
spark.conf.set("spark.cassandra.connection.port","10350")
spark.conf.set("spark.cassandra.connection.ssl.enabled","true")
spark.conf.set("spark.cassandra.auth.username","YOUR_ACCOUNT_NAME")
spark.conf.set("spark.cassandra.auth.password","YOUR_ACCOUNT_KEY")
// if using Spark 2.x
// spark.conf.set("spark.cassandra.connection.factory", "com.microsoft.azure.cosmosdb.cassandra.CosmosDbConnectionFactory")

//Throughput-related...adjust as needed
spark.conf.set("spark.cassandra.output.batch.size.rows", "1")
//spark.conf.set("spark.cassandra.connection.connections_per_executor_max", "10") // Spark 2.x
spark.conf.set("spark.cassandra.connection.remoteConnectionsPerExecutor", "10") // Spark 3.x
spark.conf.set("spark.cassandra.output.concurrent.writes", "1000")
spark.conf.set("spark.cassandra.concurrent.reads", "512")
spark.conf.set("spark.cassandra.output.batch.grouping.buffer.size", "1000")
spark.conf.set("spark.cassandra.connection.keep_alive_ms", "600000000")
```

> [!NOTE]
> Spark 3.0 以降を使用している場合は、Cosmos DB ヘルパーと接続ファクトリをインストールする必要はありません。 また、Spark 3 コネクタの場合は、`connections_per_executor_max` ではなく `remoteConnectionsPerExecutor` を使用する必要があります (上記を参照)。 接続関連のプロパティが、上記のノートブック内で定義されていることがわかります。 次の構文を使用すると、クラスター レベル (Spark コンテキストの初期化) で定義しなくても、この方法で接続プロパティを定義できます。 ただし、spark コンテキストを必要とする操作 (次に示す `update` の `CassandraConnector(sc)` など) を使用する場合は、接続プロパティをクラスター レベルで定義する必要があります。

## <a name="dataframe-api"></a>データフレーム API

### <a name="create-a-dataframe"></a>データフレームの作成 

```scala
// (1) Update: Changing author name to include prefix of "Sir"
// (2) Insert: adding a new book

val booksUpsertDF = Seq(
    ("b00001", "Sir Arthur Conan Doyle", "A study in scarlet", 1887),
    ("b00023", "Sir Arthur Conan Doyle", "A sign of four", 1890),
    ("b01001", "Sir Arthur Conan Doyle", "The adventures of Sherlock Holmes", 1892),
    ("b00501", "Sir Arthur Conan Doyle", "The memoirs of Sherlock Holmes", 1893),
    ("b00300", "Sir Arthur Conan Doyle", "The hounds of Baskerville", 1901),
    ("b09999", "Sir Arthur Conan Doyle", "The return of Sherlock Holmes", 1905)
    ).toDF("book_id", "book_author", "book_name", "book_pub_year")
booksUpsertDF.show()
```

### <a name="upsert-data"></a>データをアップサートする

```scala
// Upsert is no different from create
booksUpsertDF.write
  .mode("append")
  .format("org.apache.spark.sql.cassandra")
  .options(Map( "table" -> "books", "keyspace" -> "books_ks"))
  .save()
```

### <a name="update-data"></a>データの更新

```scala
//Cassandra connector instance
val cdbConnector = CassandraConnector(sc)

//This runs on the driver, leverage only for one off updates
cdbConnector.withSessionDo(session => session.execute("update books_ks.books set book_price=99.33 where book_id ='b00300' and book_pub_year = 1901;"))
```

## <a name="rdd-api"></a>RDD API
> [!NOTE]
> RDD API からのアップサートは、作成操作と同じです。 

## <a name="next-steps"></a>次のステップ

次の記事に進み、Azure Cosmos DB Cassandra API テーブルに保存されているデータに対してその他の操作を実行します。
 
* [操作を削除する](spark-delete-operation.md)
* [集計操作](spark-aggregation-operations.md)
* [テーブル コピー操作](spark-table-copy-operations.md)
