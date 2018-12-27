---
title: Spark から Azure Cosmos DB Cassandra API にデータを作成/挿入する
description: この記事では、Azure Cosmos DB Cassandra API テーブルにサンプル データを挿入する方法を説明します。
services: cosmos-db
author: anagha-microsoft
ms.service: cosmos-db
ms.component: cosmosdb-cassandra
ms.devlang: spark-scala
ms.topic: conceptual
ms.date: 09/24/2018
ms.author: ankhanol
ms.openlocfilehash: 74578dc7e69a1454e815679cf403839c3b7df4d7
ms.sourcegitcommit: ad08b2db50d63c8f550575d2e7bb9a0852efb12f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/26/2018
ms.locfileid: "47220405"
---
# <a name="createinsert-data-into-azure-cosmos-db-cassandra-api-from-spark"></a>Spark から Azure Cosmos DB Cassandra API にデータを作成/挿入する
 
この記事では、Spark から Azure Cosmos DB Cassandra API のテーブルにサンプル データを挿入する方法について説明します。

## <a name="cassandra-api-configuration"></a>Cassandra API の構成

```scala
import org.apache.spark.sql.cassandra._
//Spark connector
import com.datastax.spark.connector._
import com.datastax.spark.connector.cql.CassandraConnector

//CosmosDB library for multiple retry
import com.microsoft.azure.cosmosdb.cassandra

//Connection-related
spark.conf.set("spark.cassandra.connection.host","YOUR_ACCOUNT_NAME.cassandra.cosmosdb.azure.com")
spark.conf.set("spark.cassandra.connection.port","10350")
spark.conf.set("spark.cassandra.connection.ssl.enabled","true")
spark.conf.set("spark.cassandra.auth.username","YOUR_ACCOUNT_NAME")
spark.conf.set("spark.cassandra.auth.password","YOUR_ACCOUNT_KEY")
spark.conf.set("spark.cassandra.connection.factory", "com.microsoft.azure.cosmosdb.cassandra.CosmosDbConnectionFactory")
//Throughput-related...adjust as needed
spark.conf.set("spark.cassandra.output.batch.size.rows", "1")
spark.conf.set("spark.cassandra.connection.connections_per_executor_max", "10")
spark.conf.set("spark.cassandra.output.concurrent.writes", "1000")
spark.conf.set("spark.cassandra.concurrent.reads", "512")
spark.conf.set("spark.cassandra.output.batch.grouping.buffer.size", "1000")
spark.conf.set("spark.cassandra.connection.keep_alive_ms", "600000000")
```
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
//Delete records created in the previous section 
val cdbConnector = CassandraConnector(sc)
cdbConnector.withSessionDo(session => session.execute("delete from books_ks.books where book_id in ('b00300','b00001','b00023','b00501','b09999','b01001','b00999','b03999','b02999');"))

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

## <a name="next-steps"></a>次の手順

Azure Cosmos DB Cassandra API テーブルにデータを挿入したら、次の記事に進み、Cosmos DB Cassandra API に保存されているデータでその他の操作を実行します。
 
* [操作を読み取る](cassandra-spark-read-ops.md)
* [操作を Upsert する](cassandra-spark-upsert-ops.md)
* [操作を削除する](cassandra-spark-delete-ops.md)
* [集計操作](cassandra-spark-aggregation-ops.md)
* [テーブル コピー操作](cassandra-spark-table-copy-ops.md)

