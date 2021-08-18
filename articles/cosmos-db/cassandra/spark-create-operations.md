---
title: Spark から Azure Cosmos DB Cassandra API にデータを作成または挿入する
description: この記事では、Azure Cosmos DB Cassandra API テーブルにサンプル データを挿入する方法を説明します。
author: TheovanKraay
ms.author: thvankra
ms.reviewer: sngun
ms.service: cosmos-db
ms.subservice: cosmosdb-cassandra
ms.topic: how-to
ms.date: 09/24/2018
ms.openlocfilehash: 1769ef181f977170bf205f4454ebd0254bfbe702
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/13/2021
ms.locfileid: "121779000"
---
# <a name="createinsert-data-into-azure-cosmos-db-cassandra-api-from-spark"></a>Spark から Azure Cosmos DB Cassandra API にデータを作成/挿入する
[!INCLUDE[appliesto-cassandra-api](../includes/appliesto-cassandra-api.md)]
 
この記事では、Spark から Azure Cosmos DB Cassandra API のテーブルにサンプル データを挿入する方法について説明します。

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
> Spark 3.0 以降を使用している場合は、Cosmos DB ヘルパーと接続ファクトリをインストールする必要はありません。 また、Spark 3 コネクタの場合は、`connections_per_executor_max` ではなく `remoteConnectionsPerExecutor` を使用する必要があります (上記を参照)。 接続関連のプロパティが、上記のノートブック内で定義されていることがわかります。 クラスター単位で指定しなくても、下の構文により、この方法で接続プロパティを指定できます (Spark コンテキスト初期化)。

## <a name="dataframe-api"></a>データフレーム API

### <a name="create-a-dataframe-with-sample-data"></a>サンプル データを使用してデータフレームを作成する

```scala
// Generate a dataframe containing five records
val booksDF = Seq(
   ("b00001", "Arthur Conan Doyle", "A study in scarlet", 1887),
   ("b00023", "Arthur Conan Doyle", "A sign of four", 1890),
   ("b01001", "Arthur Conan Doyle", "The adventures of Sherlock Holmes", 1892),
   ("b00501", "Arthur Conan Doyle", "The memoirs of Sherlock Holmes", 1893),
   ("b00300", "Arthur Conan Doyle", "The hounds of Baskerville", 1901)
).toDF("book_id", "book_author", "book_name", "book_pub_year")

//Review schema
booksDF.printSchema

//Print
booksDF.show
```

> [!NOTE]
> 行レベルでの "存在しない場合は作成" 機能はまだサポートされていません。

### <a name="persist-to-azure-cosmos-db-cassandra-api"></a>Azure Cosmos DB Cassandra API に永続化する

データを保存する場合、次の例に示すとおり、Time to Live および一貫性ポリシー設定を設定することも可能です。

```scala
//Persist
booksDF.write
  .mode("append")
  .format("org.apache.spark.sql.cassandra")
  .options(Map( "table" -> "books", "keyspace" -> "books_ks", "output.consistency.level" -> "ALL", "ttl" -> "10000000"))
  .save()
```

> [!NOTE]
> 列レベルでの TTL はまだサポートされていません。

#### <a name="validate-in-cqlsh"></a>cqlsh で検証する

```sql
use books_ks;
select * from books;
```

## <a name="resilient-distributed-database-rdd-api"></a>Resilient Distributed Database (RDD) API

### <a name="create-a-rdd-with-sample-data"></a>サンプル データを使用して RDD を作成する
```scala
//Drop and re-create table to delete records created in the previous section 
val cdbConnector = CassandraConnector(sc)
cdbConnector.withSessionDo(session => session.execute("DROP TABLE IF EXISTS books_ks.books;"))

cdbConnector.withSessionDo(session => session.execute("CREATE TABLE IF NOT EXISTS books_ks.books(book_id TEXT,book_author TEXT, book_name TEXT,book_pub_year INT,book_price FLOAT, PRIMARY KEY(book_id,book_pub_year)) WITH cosmosdb_provisioned_throughput=4000 , WITH default_time_to_live=630720000;"))

//Create RDD
val booksRDD = sc.parallelize(Seq(
   ("b00001", "Arthur Conan Doyle", "A study in scarlet", 1887),
   ("b00023", "Arthur Conan Doyle", "A sign of four", 1890),
   ("b01001", "Arthur Conan Doyle", "The adventures of Sherlock Holmes", 1892),
   ("b00501", "Arthur Conan Doyle", "The memoirs of Sherlock Holmes", 1893),
   ("b00300", "Arthur Conan Doyle", "The hounds of Baskerville", 1901)
))

//Review
booksRDD.take(2).foreach(println)
```

> [!NOTE]
> "存在しない場合は作成" 機能はまだサポートされていません。

### <a name="persist-to-azure-cosmos-db-cassandra-api"></a>Azure Cosmos DB Cassandra API に永続化する

Cassandra API にデータを保存する場合、次の例に示すとおり、Time to Live および一貫性ポリシー設定を設定することも可能です。

```scala
import com.datastax.spark.connector.writer._

//Persist
booksRDD.saveToCassandra("books_ks", "books", SomeColumns("book_id", "book_author", "book_name", "book_pub_year"),writeConf = WriteConf(ttl = TTLOption.constant(900000),consistencyLevel = ConsistencyLevel.ALL))
```

#### <a name="validate-in-cqlsh"></a>cqlsh で検証する

```sql
use books_ks;
select * from books;
```

## <a name="next-steps"></a>次のステップ

Azure Cosmos DB Cassandra API テーブルにデータを挿入したら、次の記事に進み、Cosmos DB Cassandra API に保存されているデータでその他の操作を実行します。
 
* [操作を読み取ります。](spark-read-operation.md)
* [操作を Upsert する](spark-upsert-operations.md)
* [操作を削除する](spark-delete-operation.md)
* [集計操作](spark-aggregation-operations.md)
* [テーブル コピー操作](spark-table-copy-operations.md)
