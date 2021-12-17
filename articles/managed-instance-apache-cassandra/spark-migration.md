---
title: Apache Spark を使用して Azure Managed Instance for Apache Cassandra に移行する
description: Apache Spark を使用して Azure Managed Instance for Apache Cassandra に移行する方法について説明します。
author: TheovanKraay
ms.author: thvankra
ms.service: managed-instance-apache-cassandra
ms.topic: tutorial
ms.date: 11/02/2021
ms.custom: ignite-fall-2021
ms.openlocfilehash: 7c061f808dfbe6b93d9fd6a9266c152fa5aecf57
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/02/2021
ms.locfileid: "131005254"
---
# <a name="migrate-to-azure-managed-instance-for-apache-cassandra-using-apache-spark"></a>Apache Spark を使用して Azure Managed Instance for Apache Cassandra に移行する

可能な限り、Apache Cassandra のネイティブ レプリケーションを使用し、[ハイブリッド クラスター](configure-hybrid-cluster.md)を構成することで、既存のクラスターから Azure Managed Instance for Apache Cassandra にデータを移行することをお勧めします。 このアプローチでは、Apache Cassandra のゴシップ プロトコルを使用して、ソース データセンターから新しいマネージド インスタンス データセンターにデータをレプリケートします。 ただし、ソース データベースのバージョンに互換性がない場合や、ハイブリッド クラスターのセットアップが実行できない場合があります。 

このチュートリアルでは、Cassandra Spark コネクタと Apache Spark 向けの Azure Databricks を使用して、オフラインで Azure Managed Instance for Apache Cassandra に移行するためにデータを移行する方法について説明します。

## <a name="prerequisites"></a>前提条件

* [Azure portal](create-cluster-portal.md) または [Azure CLI](create-cluster-cli.md) を使用して Azure Managed Instance for Apache Cassandra クラスターをプロビジョニングし、[CQLSH を使用してクラスターに接続](./create-cluster-portal.md#connecting-to-your-cluster)できることを確認します。

* [マネージド Cassandra VNet 内に Azure Databricks アカウントをプロビジョニングします](deploy-cluster-databricks.md)。 また、ソース Cassandra クラスターへのネットワーク アクセスがあることも確認します。

* キースペースまたはテーブル スキームをソース Cassandra データベースからターゲット Cassandra Managed Instance データベースに既に移行していることを確認します。

## <a name="provision-an-azure-databricks-cluster"></a>Azure Databricks クラスターのプロビジョニング

Spark 3.0 をサポートする Databricks ランタイム バージョン 7.5 を選択することをお勧めします。

:::image type="content" source="../cosmos-db/cassandra/media/migrate-data-databricks/databricks-runtime.png" alt-text="Databricks ランタイムのバージョンを見つける方法を示すスクリーンショット。":::

## <a name="add-dependencies"></a>依存関係を追加する

Apache Spark Cassandra コネクタ ライブラリをクラスターに追加して、ネイティブと Azure Cosmos DB Cassandra 両方のエンドポイントに接続します。 自分のクラスターで、 **[ライブラリ]**  >  **[新規インストール]**  >  **[Maven]** の順に選択し、Maven 座標に `com.datastax.spark:spark-cassandra-connector-assembly_2.12:3.0.0` を追加します。

:::image type="content" source="../cosmos-db/cassandra/media/migrate-data-databricks/databricks-search-packages.png" alt-text="Databricks で Maven パッケージを検索する方法を示すスクリーンショット。":::

**[インストール]** を選択し、インストールが完了したらクラスターを再起動します。

> [!NOTE]
> Cassandra コネクタ ライブラリがインストールされたら、必ず Databricks クラスターを再起動してください。

## <a name="create-scala-notebook-for-migration"></a>移行用の Scala ノートブックを作成する

Databricks で Scala ノートブックを作成します。 ソースとターゲットの Cassandra 構成を、対応する資格情報、ソースとターゲットのキースペースおよびテーブルで置き換えます。 次に、下のコードを実行します。

```scala
import com.datastax.spark.connector._
import com.datastax.spark.connector.cql._
import org.apache.spark.SparkContext

// source cassandra configs
val sourceCassandra = Map( 
    "spark.cassandra.connection.host" -> "<Source Cassandra Host>",
    "spark.cassandra.connection.port" -> "9042",
    "spark.cassandra.auth.username" -> "<USERNAME>",
    "spark.cassandra.auth.password" -> "<PASSWORD>",
    "spark.cassandra.connection.ssl.enabled" -> "false",
    "keyspace" -> "<KEYSPACE>",
    "table" -> "<TABLE>"
)

//target cassandra configs
val targetCassandra = Map( 
    "spark.cassandra.connection.host" -> "<Source Cassandra Host>",
    "spark.cassandra.connection.port" -> "9042",
    "spark.cassandra.auth.username" -> "<USERNAME>",
    "spark.cassandra.auth.password" -> "<PASSWORD>",
    "spark.cassandra.connection.ssl.enabled" -> "true",
    "keyspace" -> "<KEYSPACE>",
    "table" -> "<TABLE>",
    //throughput related settings below - tweak these depending on data volumes. 
    "spark.cassandra.output.batch.size.rows"-> "1",
    "spark.cassandra.output.concurrent.writes" -> "1000",
    "spark.cassandra.connection.remoteConnectionsPerExecutor" -> "10",
    "spark.cassandra.concurrent.reads" -> "512",
    "spark.cassandra.output.batch.grouping.buffer.size" -> "1000",
    "spark.cassandra.connection.keep_alive_ms" -> "600000000"
)

//Read from source Cassandra
val DFfromSourceCassandra = sqlContext
  .read
  .format("org.apache.spark.sql.cassandra")
  .options(sourceCassandra)
  .load
  
//Write to target Cassandra
DFfromSourceCassandra
  .write
  .format("org.apache.spark.sql.cassandra")
  .options(targetCassandra)
  .mode(SaveMode.Append) // only required for Spark 3.x
  .save
```

> [!NOTE]
> 各行の `writetime` を保持するか、日付をさかのぼる必要がある場合は、[ライブ マイグレーション](dual-write-proxy-migration.md)に関する記事を参照してください。 

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [Azure CLI を利用して Azure Managed Instance for Apache Cassandra リソースを管理する](manage-resources-cli.md)
