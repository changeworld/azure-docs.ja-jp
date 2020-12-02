---
title: Databricks (Spark) を使用して Apache Cassandra から Azure Cosmos DB Cassandra API にデータを移行する
description: Azure Databricks と Spark を使用して、Apache Cassandra データベースから Azure Cosmos DB Cassandra API にデータを移行する方法について説明します。
author: TheovanKraay
ms.service: cosmos-db
ms.subservice: cosmosdb-cassandra
ms.topic: how-to
ms.date: 11/16/2020
ms.author: thvankra
ms.reviewer: thvankra
ms.openlocfilehash: 877aa96b189de47d158721df6585cb94ace4a855
ms.sourcegitcommit: 03c0a713f602e671b278f5a6101c54c75d87658d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/19/2020
ms.locfileid: "94932870"
---
# <a name="migrate-data-from-cassandra-to-azure-cosmos-db-cassandra-api-account-using-azure-databricks"></a>Azure Databricks を使用して Cassandra から Azure Cosmos DB Cassandra API アカウントにデータを移行する
[!INCLUDE[appliesto-cassandra-api](includes/appliesto-cassandra-api.md)]

Azure Cosmos DB の Cassandra API は、次のようなさまざまな理由により、Apache Cassandra 上で実行されているエンタープライズ ワークロードに適した選択肢になりました。 

* **管理と監視のオーバーヘッドなし:** OS、JVM、および yaml ファイルやそれらの相互作用での無数の設定を管理および監視する際のオーバーヘッドが解消されます。

* **大幅なコスト削減:** Azure Cosmos DB によってコストを節約できます。これには、VM、帯域幅、適用されるすべてのライセンスのコストが含まれます。 さらに、データ センター、サーバー、SSD ストレージ、ネットワーク、電気代を管理する必要がありません。 

* **既存のコードとツールを使用可能:** Azure Cosmos DB では、既存の Cassandra SDK およびツールとのワイヤ プロトコル レベルの互換性が提供されます。 この互換性により、Azure Cosmos DB の Cassandra API を少し変更するだけで、既存のコードベースを使用できることが保証されます。

データベースのワークロードをプラットフォーム間で移行するには、さまざまな方法があります。 [Azure Databricks](https://azure.microsoft.com/services/databricks/) は、[Apache Spark](https://spark.apache.org/) 用の「サービスとしてのプラットフォーム」製品です。これにより、大規模なオフライン移行を実行できます。 この記事では、Azure Databricks を使用して、ネイティブ Apache Cassandra キースペース/テーブルから Azure Cosmos DB Cassandra API にデータを移行するために必要な手順について説明します。

## <a name="prerequisites"></a>前提条件

* [Azure Cosmos DB Cassandra API アカウントのプロビジョニング](create-cassandra-dotnet.md#create-a-database-account)

* [Azure Cosmos DB Cassandra API への接続の基本を確認する](cassandra-spark-generic.md)

* 互換性を確保するために「[Azure Cosmos DB Cassandra API でサポートされる機能](cassandra-support.md)」を確認する

* ターゲット Azure Cosmos DB Cassandra API アカウントに、空のキースペースとテーブルを既に作成していることを確認する

* [cqlsh またはホストされたシェルを使用して検証する](cassandra-support.md#hosted-cql-shell-preview)

## <a name="provision-an-azure-databricks-cluster"></a>Azure Databricks クラスターのプロビジョニング

手順に従って、[Azure Databricks クラスターをプロビジョニング](/azure/databricks/scenarios/quickstart-create-databricks-workspace-portal)できます。 ただし、現時点では、Apache Cassandra コネクタに対して Apache Spark 3. x がサポートされていないことに注意してください。 サポートされている Apache Spark v2.x バージョンを使用して、Databricks ランタイムをプロビジョニングする必要があります。 Databricks ランタイムのバージョン 6.6 をお勧めします。

:::image type="content" source="./media/cassandra-migrate-cosmos-db-databricks/databricks-runtime.png" alt-text="Databricks ランタイム":::


## <a name="add-dependencies"></a>依存関係を追加する

Apache Spark Cassandra コネクタ ライブラリをクラスターに追加して、ネイティブ エンドポイントと Cosmos DB Cassandra エンドポイントの両方に接続する必要があります。 クラスターで、[ライブラリ] -> [新規インストール] -> [Maven] -> [パッケージの検索] を選択します。

:::image type="content" source="./media/cassandra-migrate-cosmos-db-databricks/databricks-search-packages.png" alt-text="Databricks 検索パッケージ":::

検索ボックスに「`Cassandra`」と入力し、使用可能な最新の `spark-cassandra-connector` Maven リポジトリを選択し、[インストール] を選択します。

:::image type="content" source="./media/cassandra-migrate-cosmos-db-databricks/databricks-search-packages-2.png" alt-text="Databricks パッケージの選択":::

> [!NOTE]
> 必ず、Cassandra コネクタ ライブラリがインストールされた後に Databricks クラスターを再起動してください。

## <a name="create-scala-notebook-for-migration"></a>移行用の Scala ノートブックを作成する

次のコードで Databricks に Scala ノートブックを作成します。 ソースとターゲットの Cassandra 構成を、対応する資格情報、ソース/ターゲットのキースペースおよびテーブルに置き換えてから、以下を実行します。

```scala
import com.datastax.spark.connector._
import com.datastax.spark.connector.cql._
import org.apache.spark.SparkContext

// source cassandra configs
val nativeCassandra = Map( 
    "spark.cassandra.connection.host" -> "<Source Cassandra Host>",
    "spark.cassandra.connection.port" -> "9042",
    "spark.cassandra.auth.username" -> "<USERNAME>",
    "spark.cassandra.auth.password" -> "<PASSWORD>",
    "spark.cassandra.connection.ssl.enabled" -> "false",
    "keyspace" -> "<KEYSPACE>",
    "table" -> "<TABLE>"
)

//target cassandra configs
val cosmosCassandra = Map( 
    "spark.cassandra.connection.host" -> "<USERNAME>.cassandra.cosmos.azure.com",
    "spark.cassandra.connection.port" -> "10350",
    "spark.cassandra.auth.username" -> "<USERNAME>",
    "spark.cassandra.auth.password" -> "<PASSWORD>",
    "spark.cassandra.connection.ssl.enabled" -> "true",
    "keyspace" -> "<KEYSPACE>",
    "table" -> "<TABLE>",
    //throughput related settings below - tweak these depending on data volumes. 
    "spark.cassandra.output.batch.size.rows"-> "1",
    "spark.cassandra.connection.connections_per_executor_max" -> "10",
    "spark.cassandra.output.concurrent.writes" -> "1000",
    "spark.cassandra.concurrent.reads" -> "512",
    "spark.cassandra.output.batch.grouping.buffer.size" -> "1000",
    "spark.cassandra.connection.keep_alive_ms" -> "600000000"
)

//Read from native Cassandra
val DFfromNativeCassandra = sqlContext
  .read
  .format("org.apache.spark.sql.cassandra")
  .options(nativeCassandra)
  .load
  
//Write to CosmosCassandra
DFfromNativeCassandra
  .write
  .format("org.apache.spark.sql.cassandra")
  .options(cosmosCassandra)
  .save
```

> [!NOTE]
> `spark.cassandra.output.concurrent.writes` と `connections_per_executor_max` の構成は、[レート制限](https://docs.microsoft.com/samples/azure-samples/azure-cosmos-cassandra-java-retry-sample/azure-cosmos-db-cassandra-java-retry-sample/)を回避するために重要です。この制限は、Cosmos DB への要求が、プロビジョニングされたスループット ([要求ユニット](https://docs.microsoft.com/azure/cosmos-db/request-units)) を超えると発生します。 Spark クラスター内の実行プログラム数に応じて、これらの設定値の調整が必要になることがあります。また、ターゲット テーブルに書き込まれる各レコードのサイズ (および RU のコスト) によっても、調整が必要になる場合があります。

## <a name="next-steps"></a>次のステップ

* [コンテナーとデータベースのスループットのプロビジョニング](set-throughput.md) 
* [パーティション キーのベスト プラクティス](partitioning-overview.md#choose-partitionkey)
* 「[Azure Cosmos DB Capacity Planner を使用して RU/秒を見積もる](estimate-ru-with-capacity-planner.md)」の記事
* [Azure Cosmos DB Cassandra API での柔軟な拡張](manage-scale-cassandra.md)
