---
title: Spark からの Azure Cosmos DB Cassandra API での DDL 操作
description: この記事では、Spark からの Azure Cosmos DB Cassandra API に対するキースペースとテーブルの DDL 操作について詳しく説明します。
author: TheovanKraay
ms.author: thvankra
ms.reviewer: sngun
ms.service: cosmos-db
ms.subservice: cosmosdb-cassandra
ms.topic: how-to
ms.date: 10/07/2020
ms.openlocfilehash: 99eee3fd7506f3baf8b119b3fdf4ba881850d61e
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/13/2021
ms.locfileid: "121778999"
---
# <a name="ddl-operations-in-azure-cosmos-db-cassandra-api-from-spark"></a>Spark からの Azure Cosmos DB Cassandra API での DDL 操作
[!INCLUDE[appliesto-cassandra-api](../includes/appliesto-cassandra-api.md)]

この記事では、Spark からの Azure Cosmos DB Cassandra API に対するキースペースとテーブルの DDL 操作について詳しく説明します。

## <a name="spark-context"></a>Spark コンテキスト

 Cassandra API のコネクタでは、spark コンテキストのために、Cassandra への接続の詳細の初期化が必要です。 ノートブックを起動すると、spark コンテキストは既に初期化されています。停止して再初期化することはお勧めしません。 解決方法の一つとして、クラスターの spark 構成で、Cassandra API インスタンスの構成情報をクラスターレベルで追加することができます。 これは、クラスターあたり 1 回限りのアクティビティです。 Spark の構成情報に、以下のコードをスペース区切りのキー値のペアとして追加します。
 
  ```scala
  spark.cassandra.connection.host YOUR_COSMOSDB_ACCOUNT_NAME.cassandra.cosmosdb.azure.com
  spark.cassandra.connection.port 10350
  spark.cassandra.connection.ssl.enabled true
  spark.cassandra.auth.username YOUR_COSMOSDB_ACCOUNT_NAME
  spark.cassandra.auth.password YOUR_COSMOSDB_KEY
  ```

## <a name="cassandra-api-related-configuration"></a>Cassandra API に関連する構成 

```scala
import org.apache.spark.sql.cassandra._
//Spark connector
import com.datastax.spark.connector._
import com.datastax.spark.connector.cql.CassandraConnector

//if using Spark 2.x, CosmosDB library for multiple retry
//import com.microsoft.azure.cosmosdb.cassandra
//spark.conf.set("spark.cassandra.connection.factory", "com.microsoft.azure.cosmosdb.cassandra.CosmosDbConnectionFactory")

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
> Spark 3.0 以降を使用している場合は、Cosmos DB ヘルパーと接続ファクトリをインストールする必要はありません。 また、Spark 3 コネクタの場合は、`connections_per_executor_max` ではなく `remoteConnectionsPerExecutor` を使用する必要があります (上記を参照)。

## <a name="keyspace-ddl-operations"></a>キースペースの DDL 操作

### <a name="create-a-keyspace"></a>キースペースを作成する

```scala
//Cassandra connector instance
val cdbConnector = CassandraConnector(sc)

// Create keyspace
cdbConnector.withSessionDo(session => session.execute("CREATE KEYSPACE IF NOT EXISTS books_ks WITH REPLICATION = {'class': 'SimpleStrategy', 'replication_factor': 1 } "))
```

#### <a name="validate-in-cqlsh"></a>cqlsh で検証する

cqlsh で次のコマンドを実行すると、先ほど作成したキースペースが表示されます。

```bash
DESCRIBE keyspaces;
```

### <a name="drop-a-keyspace"></a>キースペースを削除する

```scala
val cdbConnector = CassandraConnector(sc)
cdbConnector.withSessionDo(session => session.execute("DROP KEYSPACE books_ks"))
```

#### <a name="validate-in-cqlsh"></a>cqlsh で検証する

```bash
DESCRIBE keyspaces;
```
## <a name="table-ddl-operations"></a>テーブルの DDL 操作

**考慮事項:**  

- create table ステートメントを使用して、テーブル レベルでスループットを割り当てることができます。  
- 1 つのパーティション キーで、20 GB のデータを格納できます。  
- 1 つのレコードで、最大 2 MB のデータを格納できます。  
- 1 つのパーティション キー範囲で、複数のパーティション キーを格納できます。

### <a name="create-a-table"></a>テーブルを作成する

```scala
cdbConnector.withSessionDo(session => session.execute("CREATE TABLE IF NOT EXISTS books_ks.books(book_id TEXT,book_author TEXT, book_name TEXT,book_pub_year INT,book_price FLOAT, PRIMARY KEY(book_id,book_pub_year)) WITH cosmosdb_provisioned_throughput=4000 , WITH default_time_to_live=630720000;"))
```

#### <a name="validate-in-cqlsh"></a>cqlsh で検証する

cqlsh で次のコマンドを実行すると、"books" という名前のテーブルが表示されます。 

```bash
USE books_ks;
DESCRIBE books;
```

前のコマンドの出力には、プロビジョニングされたスループットと既定の TTL の値は表示されません。これらの値はポータルで確認できます。

### <a name="alter-table"></a>テーブルを変更する

alter table コマンドを使用して、次の値を変更できます。

* プロビジョニングされているスループット 
* Time-To-Live の値
<br>現在、列の変更はサポートされていません。

```scala
val cdbConnector = CassandraConnector(sc)
cdbConnector.withSessionDo(session => session.execute("ALTER TABLE books_ks.books WITH cosmosdb_provisioned_throughput=8000, WITH default_time_to_live=0;"))
```

### <a name="drop-table"></a>テーブルの削除

```scala
val cdbConnector = CassandraConnector(sc)
cdbConnector.withSessionDo(session => session.execute("DROP TABLE IF EXISTS books_ks.books;"))
```

#### <a name="validate-in-cqlsh"></a>cqlsh で検証する

cqlsh で次のコマンドを実行すると、"books" テーブルが使用できなくなったことがわかります。

```bash
USE books_ks;
DESCRIBE tables;
```

## <a name="next-steps"></a>次のステップ

キースペースとテーブルを作成した後は、CRUD 操作などに関する次の記事に進んでください。
 
* [操作を作成/挿入する](spark-create-operations.md)  
* [操作を読み取ります。](spark-read-operation.md)  
* [操作を Upsert する](spark-upsert-operations.md)  
* [操作を削除する](spark-delete-operation.md)  
* [集計操作](spark-aggregation-operations.md)  
* [テーブル コピー操作](spark-table-copy-operations.md)  
