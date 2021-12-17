---
title: Azure Databricks から Azure Cosmos DB Cassandra API にアクセスする
description: この記事では、Azure Databricks から Azure Cosmos DB Cassandra API を使用する方法について説明します。
author: TheovanKraay
ms.author: thvankra
ms.reviewer: sngun
ms.service: cosmos-db
ms.subservice: cosmosdb-cassandra
ms.topic: how-to
ms.date: 09/24/2018
ms.openlocfilehash: 89986ca27b67f4bd420121c3113324e6e7397219
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/13/2021
ms.locfileid: "121780958"
---
# <a name="access-azure-cosmos-db-cassandra-api-data-from-azure-databricks"></a>Azure Databricks から Azure Cosmos DB Cassandra API のデータにアクセスする
[!INCLUDE[appliesto-cassandra-api](../includes/appliesto-cassandra-api.md)]

この記事では、[Azure Databricks](/azure/databricks/scenarios/what-is-azure-databricks) 上の Spark から Azure Cosmos DB Cassandra API を使用する方法について説明します。

## <a name="prerequisites"></a>前提条件

* [Azure Cosmos DB Cassandra API アカウントのプロビジョニング](manage-data-dotnet.md#create-a-database-account)

* [Azure Cosmos DB Cassandra API への接続の基本を確認する](connect-spark-configuration.md)

* [Azure Databricks クラスターのプロビジョニング](/azure/databricks/scenarios/quickstart-create-databricks-workspace-portal)

* [Cassandra API の使用におけるコード サンプルを確認する](connect-spark-configuration.md#next-steps)

* [必要な場合は検証のために cqlsh を使用する](connect-spark-configuration.md#connecting-to-azure-cosmos-db-cassandra-api-from-spark)

* **Cassandra コネクタ用の Cassandra API インスタンス構成:**

  Cassandra API のコネクタでは、spark コンテキストのために、Cassandra への接続の詳細の初期化が必要です。 Databricks のノートブックを起動すると、spark コンテキストは既に初期化されています。停止して再初期化することはお勧めしません。 解決方法の一つとして、クラスターの spark 構成で、Cassandra API インスタンスの構成情報をクラスターレベルで追加することができます。 これは、クラスターあたり 1 回限りのアクティビティです。 Spark の構成情報に、以下のコードをスペース区切りのキー値のペアとして追加します。
 
  ```scala
  spark.cassandra.connection.host YOUR_COSMOSDB_ACCOUNT_NAME.cassandra.cosmosdb.azure.com
  spark.cassandra.connection.port 10350
  spark.cassandra.connection.ssl.enabled true
  spark.cassandra.auth.username YOUR_COSMOSDB_ACCOUNT_NAME
  spark.cassandra.auth.password YOUR_COSMOSDB_KEY
  ```

## <a name="add-the-required-dependencies"></a>必要な依存関係を追加する

* **Cassandra Spark コネクタ:** - Cassandra コネクタは、Spark で Azure Cosmos DB Cassandra API と統合するために、Azure Databricks クラスターに接続する必要があります。 クラスターを接続する方法は以下の通りです。

  * Databricks ランタイム バージョンと Spark のバージョンを確認します。 次に Cassandra Spark コネクタと互換性がある [Maven 座標](https://mvnrepository.com/artifact/com.datastax.spark/spark-cassandra-connector)を探してから、クラスターに接続します。 コネクタ ライブラリのクラスタへの接続については、「[Maven パッケージまたは Spark パッケージをアップロードする」](https://docs.databricks.com/user-guide/libraries.html)」の記事を参照してください。 Spark 3.0 をサポートする Databricks ランタイム バージョン 7.5 を選択することをお勧めします。 Apache Spark Cassandra コネクタをクラスターに追加するには、 **[Libraries]\(ライブラリ\)**  >  **[Install New]\(新規インストール\)**  >  **[Maven]** を選択し、Maven 座標に `com.datastax.spark:spark-cassandra-connector-assembly_2.12:3.0.0` を追加します。 Spark 2.x を使用する場合は、Spark バージョン 2.4.5 を使用した環境で、maven 座標 `com.datastax.spark:spark-cassandra-connector_2.11:2.4.3` で spark コネクタを使用することをお勧めします。

* **Azure Cosmos DB Cassandra API 固有のライブラリ:** - Spark 2.x を使用している場合は、Cassandra Spark コネクタから Azure Cosmos DB Cassandra api への再試行ポリシーの構成では、カスタムの接続ファクトリが必要です。 ライブラリをクラスタに接続するために、`com.microsoft.azure.cosmosdb:azure-cosmos-cassandra-spark-helper:1.2.0`[ の Maven 座標](https://search.maven.org/artifact/com.microsoft.azure.cosmosdb/azure-cosmos-cassandra-spark-helper/1.2.0/jar)を追加します。

> [!NOTE]
> Spark 3.0 以上を使用している場合は、上記の Cosmos DB Cassandra API 固有のライブラリをインストールする必要はありません。

## <a name="sample-notebooks"></a>サンプル ノートブック

Azure Databricks の[ノートブックのサンプル](https://github.com/Azure-Samples/azure-cosmos-db-cassandra-api-spark-notebooks-databricks/tree/main/notebooks/scala)は、GitHub リポジトリからダウンロードできます。 ここには、Spark から Azure Cosmos DB Cassandra API への接続方法や、データに対してさまざまな CRUD 操作を実行する方法などのサンプルがあります。 また、Databricks のクラスタ ワークスペースに[すべてのノートブックをインポート](https://github.com/Azure-Samples/azure-cosmos-db-cassandra-api-spark-notebooks-databricks/tree/main/dbc)して、実行することもできます。 

## <a name="accessing-azure-cosmos-db-cassandra-api-from-spark-scala-programs"></a>Spark Scala プログラムから Azure Cosmos DB Cassandra API にアクセスする

Azure Databricks 上で自動化されたプロセスとして実行させる Spark プログラムは、[spark-submit](https://spark.apache.org/docs/latest/submitting-applications.html) を使用してクラスタに送信され、Azure Databricks のジョブがスケジュール実行されます。

以下は、Azure Cosmos DB Cassandra API との連携に向けて Spark Scala プログラムを構築し始めるときに参考となるリンクです。
* [Spark Scala から Azure Cosmos DB Cassandra API に接続する方法](https://github.com/Azure-Samples/azure-cosmos-db-cassandra-api-spark-connector-sample/blob/main/src/main/scala/com/microsoft/azure/cosmosdb/cassandra/SampleCosmosDBApp.scala)
* [Spark Scala プログラムを Azure Databricks 上の自動化されたジョブとして実行する方法](/azure/databricks/jobs)
* [Cassandra API を使用する場合のサンプルコードの一覧](connect-spark-configuration.md#next-steps)

## <a name="next-steps"></a>次のステップ

Java アプリケーションを使用した[ Cassandra API アカウント、データベースおよびテーブルの作成](create-account-java.md)の開始
