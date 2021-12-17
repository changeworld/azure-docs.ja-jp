---
title: Spark を使用して Cassandra API のテーブル データを読み取る
titleSufix: Azure Cosmos DB
description: この記事では、Azure Cosmos DB 内の Cassandra API テーブルからデータを読み取る方法について説明します。
author: TheovanKraay
ms.author: thvankra
ms.reviewer: sngun
ms.service: cosmos-db
ms.subservice: cosmosdb-cassandra
ms.topic: how-to
ms.date: 06/02/2020
ms.custom: seodec18
ms.openlocfilehash: b43e58acc8b2942d4961f60659993b2d07efadaa
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/13/2021
ms.locfileid: "121780814"
---
# <a name="read-data-from-azure-cosmos-db-cassandra-api-tables-using-spark"></a>Spark を使用して Azure Cosmos DB の Cassandra API テーブルからデータを読み取る
[!INCLUDE[appliesto-cassandra-api](../includes/appliesto-cassandra-api.md)]

 この記事では、Spark から Azure Cosmos DB Cassandra API に格納されたデータを読み取る方法について説明します。

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
> バージョン 3.0 以上の Spark を使用している場合、Cosmos DB のヘルパーと接続ファクトリをインストールする必要はありません。 また、Spark 3 コネクタでは、`connections_per_executor_max` の代わりに `remoteConnectionsPerExecutor` を使うべきです (上の例)。 上の例では、ノートブックの中で接続関連のプロパティが指定されているのが分かるでしょう。 クラスター単位で指定しなくても、下の構文により、この方法で接続プロパティを指定できます (Spark コンテキスト初期化)。

## <a name="dataframe-api"></a>データフレーム API

### <a name="read-table-using-sessionreadformat-command"></a>session.read.format コマンドを使用してテーブルを読み取る

```scala
val readBooksDF = sqlContext
  .read
  .format("org.apache.spark.sql.cassandra")
  .options(Map( "table" -> "books", "keyspace" -> "books_ks"))
  .load

readBooksDF.explain
readBooksDF.show
```
### <a name="read-table-using-sparkreadcassandraformat"></a>spark.read.cassandraFormat を使用してテーブルを読み取る 

```scala
val readBooksDF = spark.read.cassandraFormat("books", "books_ks", "").load()
```

### <a name="read-specific-columns-in-table"></a>テーブル内の特定の列を読み取る

```scala
val readBooksDF = spark
  .read
  .format("org.apache.spark.sql.cassandra")
  .options(Map( "table" -> "books", "keyspace" -> "books_ks"))
  .load
  .select("book_name","book_author", "book_pub_year")

readBooksDF.printSchema
readBooksDF.explain
readBooksDF.show
```

### <a name="apply-filters"></a>フィルターを適用する

データベースに述語をプッシュ ダウンすると、より適切に最適化された Spark クエリを実行できます。 述語とは true または false を返すクエリに対する条件で、通常は WHERE 句に配置されます。 述語プッシュ ダウンは、データベース クエリのデータをフィルター処理することで、データベースから取得されるエントリの数を減らしてクエリのパフォーマンスを向上させます。 既定では、Spark データセット API は有効な WHERE 句をデータベースに自動的にプッシュダウンします。 

```scala
val df = spark.read.cassandraFormat("books", "books_ks").load
df.explain
val dfWithPushdown = df.filter(df("book_pub_year") > 1891)
dfWithPushdown.explain

readBooksDF.printSchema
readBooksDF.explain
readBooksDF.show
```

物理プランの `Cassandra Filters` セクションには、プッシュ ダウン フィルターも含まれます。 

:::image type="content" source="./media/spark-read-operation/pushdown-predicates-spark-3.png" alt-text="パーティション":::

## <a name="rdd-api"></a>RDD API

### <a name="read-table"></a>テーブルを読み取る
```scala
val bookRDD = sc.cassandraTable("books_ks", "books")
bookRDD.take(5).foreach(println)
```

### <a name="read-specific-columns-in-table"></a>テーブル内の特定の列を読み取る

```scala
val booksRDD = sc.cassandraTable("books_ks", "books").select("book_id","book_name").cache
booksRDD.take(5).foreach(println)
```

## <a name="sql-views"></a>SQL ビュー 

### <a name="create-a-temporary-view-from-a-dataframe"></a>データフレームから一時ビューを作成する

```scala
spark
  .read
  .format("org.apache.spark.sql.cassandra")
  .options(Map( "table" -> "books", "keyspace" -> "books_ks"))
  .load.createOrReplaceTempView("books_vw")
```

### <a name="run-queries-against-the-view"></a>ビューに対してクエリを実行する

```sql
select * from books_vw where book_pub_year > 1891
```

## <a name="next-steps"></a>次のステップ

Spark からの Azure Cosmos DB Cassandra API の操作に関するその他の記事を以下に示します。
 
 * [操作を Upsert する](spark-upsert-operations.md)
 * [操作を削除する](spark-delete-operation.md)
 * [集計操作](spark-aggregation-operations.md)
 * [テーブル コピー操作](spark-table-copy-operations.md)

