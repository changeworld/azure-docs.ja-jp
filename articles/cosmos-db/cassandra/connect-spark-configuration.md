---
title: Spark から Azure Cosmos DB Cassandra API を使用する
description: この記事は、Spark からの Cosmos DB Cassandra API 統合のメイン ページです。
author: TheovanKraay
ms.author: thvankra
ms.reviewer: sngun
ms.service: cosmos-db
ms.subservice: cosmosdb-cassandra
ms.topic: how-to
ms.date: 09/01/2019
ms.openlocfilehash: f0e695248ecddebb4e737465a639e6385f6d29f0
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/13/2021
ms.locfileid: "121780970"
---
# <a name="connect-to-azure-cosmos-db-cassandra-api-from-spark"></a>Spark から Azure Cosmos DB Cassandra API に接続する
[!INCLUDE[appliesto-cassandra-api](../includes/appliesto-cassandra-api.md)]

この記事は、Spark からの Azure Cosmos DB Cassandra API 統合に関する一連の記事の 1 つです。 この記事では、接続、データ定義言語 (DDL) 操作、基本的なデータ操作言語 (DML) 操作、および Spark からの高度な Azure Cosmos DB Cassandra API 統合について説明します。 

## <a name="prerequisites"></a>前提条件
* [Azure Cosmos DB Cassandra API アカウントのプロビジョニング](manage-data-dotnet.md#create-a-database-account)

* 任意の Spark 環境 [[Azure Databricks](/azure/databricks/scenarios/quickstart-create-databricks-workspace-portal) | [Azure HDInsight-Spark](../../hdinsight/spark/apache-spark-jupyter-spark-sql.md) | その他] をプロビジョニングします。

## <a name="dependencies-for-connectivity"></a>接続の依存関係
* **Cassandra 用スパークコネクタ:** Spark コネクタは、Azure Cosmos DB Cassandra API に接続するために使用されます。  [Maven central]( https://mvnrepository.com/artifact/com.datastax.spark/spark-cassandra-connector) で、実際の Spark 環境の Spark および Scala のバージョンと互換性のあるコネクタのバージョンを指定して使用します。 Spark 3.0 以上をサポートする環境と、maven 座標 `com.datastax.spark:spark-cassandra-connector-assembly_2.12:3.0.0` で使用できる spark コネクタを使用することをお勧めします。 Spark 2.x を使用する場合は、Spark バージョン 2.4.5 を使用した環境で、maven 座標 `com.datastax.spark:spark-cassandra-connector_2.11:2.4.3` で spark コネクタを使用することをお勧めします。


* **Cassandra API の Azure Cosmos DB ヘルパー ライブラリ:** Spark 2.x バージョンを使用している場合は、[レート制限](./scale-account-throughput.md#handling-rate-limiting-429-errors)を処理するために、Spark コネクタに加えて Azure Cosmos DB の maven 座標 `com.microsoft.azure.cosmosdb:azure-cosmos-cassandra-spark-helper:1.2.0` の [azure-cosmos-cassandra-spark-helper]( https://search.maven.org/artifact/com.microsoft.azure.cosmosdb/azure-cosmos-cassandra-spark-helper/1.2.0/jar) という別のライブラリが必要になります。 このライブラリには、カスタムの接続ファクトリと再試行ポリシー クラスが含まれています。

  Azure Cosmos DB の再試行ポリシーは、HTTP 状態コード 429 ("Request Rate Large") 例外を処理するように構成されています。 Azure Cosmos DB Cassandra API は、このような例外を Cassandra ネイティブ プロトコルに関する過負荷エラーに変換するため、バックオフで再試行できます。 Azure Cosmos DB はプロビジョニングされたスループット モデルを使用するため、受信/送信レートが増加すると、要求レートの制限例外が発生します。 再試行ポリシーにより、コンテナーに割り当てられたスループットを瞬間的に超えるデータの急増から、Spark ジョブが保護されます。 Spark 3.x コネクタを使用している場合は、このライブラリを実装する必要はありません。 

  > [!NOTE] 
  > 再試行ポリシーで、瞬間的な急増に対してのみ Spark ジョブを保護することができます。 ワークロードを実行するために必要十分な RU を構成していない場合は、再試行ポリシーは適用されず、再試行ポリシー クラスによって例外が再スローされます。

* **Azure Cosmos DB アカウントの接続の詳細:** Azure Cassandra API のアカウント名、アカウント エンドポイント、およびキー。

## <a name="optimizing-spark-connector-throughput-configuration"></a>Spark コネクタのスループット構成を最適化する 

次のセクションでは、Spark Connector for Cassandra を使用してスループットを制御するためのすべての関連パラメーターについて説明します。 パラメーターを最適化して Spark ジョブのスループットを最大化するために、`spark.cassandra.output.concurrent.writes`、`spark.cassandra.concurrent.reads`、および `spark.cassandra.input.reads_per_sec` の構成が正しく構成されている必要があります。これにより、調整とバックオフが過剰になってスループットが低下するのを防ぐことができます。

これらの構成の最適な値は、次の 4 つの要因に依存します。

-   データを取り込んでいるテーブルで構成されたスループット (要求単位) の量。
- Spark クラスター内の worker の数。
-   Spark ジョブに構成された Executor の数 (Spark バージョンに応じて `spark.cassandra.connection.connections_per_executor_max` または `spark.cassandra.connection.remoteConnectionsPerExecutor` を使用して制御できます)
-   cosmos DB に対する各要求の平均待機時間 (同じデータ センターに併置されている場合)。 この値は、書き込みの場合は 10 ミリ秒、読み取りの場合は 3 ミリ秒と想定します。

たとえば、ワーカー数が 5 で `spark.cassandra.output.concurrent.writes` の値が 1、`spark.cassandra.connection.remoteConnectionsPerExecutor` の値が 1 の場合、5 つのワーカーがテーブルに同時に書き込んでいて、それぞれのスレッドが 1 つです。 1 回の書き込みを実行するのに 10 ミリ秒かかる場合、1 秒あたり 100 の要求 (1000 ミリ秒を 10 で割る) をスレッドごとに送信できます。 ワーカー数が 5 の場合、1 秒あたりの書き込み数は 500 になります。 書き込み毎の平均コストが 5 要求ユニット (RU) となり、ターゲット テーブルには最低 2500 の要求ユニットがプロビジョニングされている必要があります (5 RU x 500 書き込み/秒)。

Executor の数を増やすと特定のジョブのスレッド数が増えて、スループットを向上させることができます。 ただし、これによる正確な影響はジョブによって変動することがあります。worker の数でスループットを制御する方が決定的です。 特定の要求について、プロファイリングして要求ユニット (RU) の料金を得ることで正確なコストを調べることもできます。 これは、テーブルまたはキースペースのスループットをより正確にプロビジョニングするときの役に立ちます。 要求レベルごとに要求ユニットの料金を取得する方法については、[こちら](./find-request-unit-charge-cassandra.md)の記事をご覧ください。 

### <a name="scaling-throughput-in-the-database"></a>データベースのスループットのスケーリング

Cassandra Spark コネクタを使用すると、Azure Cosmos DB でのスループットが非常に効率良く最大の状態になります。 その結果、効率的な再試行の場合でも、レート制限関連のエラーを防ぐために、テーブルまたはキースペースのレベルで十分なスループット (RU) がプロビジョニングされていることを確認する必要があります。 指定のテーブルまたはキースペースの最小設定である 400 RU では十分ではありません。 最小のスループット構成設定でも、Spark コネクタでは約 **6,000 要求単位** 以上に相当するレートで書き込むことができます。

Spark を使用したデータ移動に必要な RU 設定が、安定した状態のワークロードに必要な設定より高い場合は、特定の期間中のワークロードのニーズを満たすために、Azure Cosmos DB で簡単にスループットを体系的にスケールアップおよびダウンすることができます。 プログラムで動的にスケーリングするためのさまざまなオプションについては、[Cassandra API の柔軟なスケーリング](scale-account-throughput.md)に関する記事を参照してください。 

> [!NOTE]
> 上記のガイダンスでは、適度に均一なデータ分散が想定されています。 データに大きな偏りがある場合 (つまり、同じパーティション キー値に対する読み取り/書き込みの数が異常に大きい場合)、多数の[要求ユニット](../request-units.md)がテーブルにプロビジョニングされている場合でも、ボトルネックが発生する可能性があります。 要求ユニットが物理パーティション間で均等に分割され、大量のデータ スキューによって 1 つのパーティションに対して要求のボトルネックが発生するおそれがあります。
    
## <a name="spark-connector-throughput-configuration-parameters"></a>Spark コネクタのスループット構成パラメーター

次の表に、コネクタに用意されている Azure Cosmos DB Cassandra API 固有のスループット構成パラメーターを示します。 すべての構成パラメーターの詳細な一覧については、Spark Cassandra Connector GitHub リポジトリの[構成リファレンス](https://github.com/datastax/spark-cassandra-connector/blob/master/doc/reference.md)に関するページを参照してください。

| **プロパティ名** | **既定値** | **説明** |
|---------|---------|---------|
| spark.cassandra.output.batch.size.rows |  1 |バッチあたりの行数。 このパラメーターを 1 に設定します。 このパラメーターは、ワークロードが高い場合に高いスループットを達成するために使用されます。 |
| spark.cassandra.connection.connections_per_executor_max (Spark 2.x) spark.cassandra.connection.remoteConnectionsPerExecutor (Spark 3.x)  | なし | Executor あたりの各ノードの最大接続数。 10*n は、n ノード Cassandra クラスター内のノードあたりの 10 接続に相当します。 そのため、5 ノードの Cassandra クラスターで、Executor あたりの各ノードに 5 接続が必要な場合は、この構成を 25 に設定する必要があります。 この値は、Spark ジョブを構成する並列処理の次数または Executor の数に基づいて変更します。   |
| spark.cassandra.output.concurrent.writes  |  100 | Executor あたりで発生する可能性がある並列書き込み数を定義します。 "batch.size.rows" を 1 に設定しているので、それに従ってこの値をスケール アップします。 ワークロードのために達成したい並列処理の次数またはスループットに基づいて、この値を変更します。 |
| spark.cassandra.concurrent.reads |  512 | Executor あたりで実行できる並列読み取りの数を定義します。 ワークロードのために達成したい並列処理の次数またはスループットに基づいて、この値を変更します  |
| spark.cassandra.output.throughput_mb_per_sec  | なし | Executor あたりの合計書き込みスループットを定義します。 このパラメーターは、Spark ジョブのスループットの上限として使用できます。また、Cosmos コンテナーのプロビジョニングされたスループットに基づいています。   |
| spark.cassandra.input.reads_per_sec| なし   | Executor あたりの合計読み取りスループットを定義します。 このパラメーターは、Spark ジョブのスループットの上限として使用できます。また、Cosmos コンテナーのプロビジョニングされたスループットに基づいています。  |
| spark.cassandra.output.batch.grouping.buffer.size |  1000  | Cassandra API に送信する前にメモリに格納できる単一の Spark タスクあたりのバッチ数を定義します |
| spark.cassandra.connection.keep_alive_ms | 60000 | 使用されていない接続が使用可能になるまでの時間を定義します。 | 

Spark ジョブに期待するワークロードと、Cosmos DB アカウント用にプロビジョニングしたスループットに基づいて、これらのパラメーターのスループットと並列処理の次数を調整します。


## <a name="connecting-to-azure-cosmos-db-cassandra-api-from-spark"></a>Spark から Azure Cosmos DB Cassandra API に接続する

### <a name="cqlsh"></a>cqlsh
次のコマンドは、cqlsh から Azure CosmosDB Cassandra API に接続する詳しい方法を示しています。  これは、Spark でサンプルを実行するときの検証に役立ちます。<br>
**Linux/Unix/Mac から:**

```bash
export SSL_VERSION=TLSv1_2
export SSL_VALIDATE=false
cqlsh.py YOUR-COSMOSDB-ACCOUNT-NAME.cassandra.cosmosdb.azure.com 10350 -u YOUR-COSMOSDB-ACCOUNT-NAME -p YOUR-COSMOSDB-ACCOUNT-KEY --ssl
```

### <a name="1--azure-databricks"></a>1.Azure Databricks
以下の記事では、Azure Databricks クラスターのプロビジョニング、Azure Cosmos DB Cassandra API に接続するためのクラスター構成、および DDL 操作、DML 操作などに関するいくつかのサンプル ノートブックについて説明します。<BR>
[Azure Databricks から Azure Cosmos DB Cassandra API を使用する](spark-databricks.md)<BR>
  
### <a name="2--azure-hdinsight-spark"></a>2.Azure HDInsight-Spark
以下の記事では、HDinsight-Spark サービス、プロビジョニング、Azure Cosmos DB Cassandra API に接続するためのクラスター構成、および DDL 操作、DML 操作などに関するいくつかのサンプル ノートブックについて説明します。<BR>
[Azure HDInsight-Spark から Azure Cosmos DB Cassandra API を使用する](spark-hdinsight.md)
 
### <a name="3--spark-environment-in-general"></a>3.一般的な Spark 環境
上記のセクションは Azure Spark ベースの PaaS サービスに固有の内容ですが、このセクションでは一般的な Spark 環境について説明します。  コネクタの依存関係、インポート、Spark セッション構成について、以下で詳しく説明します。 「次の手順」セクションでは、DDL 操作、DML 操作などのコード サンプルについて説明します。  

#### <a name="connector-dependencies"></a>コネクタの依存関係:

1. [Spark 用の Cassandra コネクタ](connect-spark-configuration.md#dependencies-for-connectivity)を取得する Maven 座標を追加します
2. Cassandra API 用の [Azure Cosmos DB ヘルパー ライブラリ](connect-spark-configuration.md#dependencies-for-connectivity)の Maven 座標を追加します

#### <a name="imports"></a>インポート:

```scala
import org.apache.spark.sql.cassandra._
//Spark connector
import com.datastax.spark.connector._
import com.datastax.spark.connector.cql.CassandraConnector

//CosmosDB library for multiple retry
import com.microsoft.azure.cosmosdb.cassandra
```

#### <a name="spark-session-configuration"></a>Spark セッションの構成:

```scala
//Connection-related
spark.conf.set("spark.cassandra.connection.host","YOUR_ACCOUNT_NAME.cassandra.cosmosdb.azure.com")
spark.conf.set("spark.cassandra.connection.port","10350")
spark.conf.set("spark.cassandra.connection.ssl.enabled","true")
spark.conf.set("spark.cassandra.auth.username","YOUR_ACCOUNT_NAME")
spark.conf.set("spark.cassandra.auth.password","YOUR_ACCOUNT_KEY")
spark.conf.set("spark.cassandra.connection.factory", "com.microsoft.azure.cosmosdb.cassandra.CosmosDbConnectionFactory")

//Throughput-related. You can adjust the values as needed
spark.conf.set("spark.cassandra.output.batch.size.rows", "1")
//spark.conf.set("spark.cassandra.connection.connections_per_executor_max", "10") // Spark 2.x
spark.conf.set("spark.cassandra.connection.remoteConnectionsPerExecutor", "10") // Spark 3.x
spark.conf.set("spark.cassandra.output.concurrent.writes", "1000")
spark.conf.set("spark.cassandra.concurrent.reads", "512")
spark.conf.set("spark.cassandra.output.batch.grouping.buffer.size", "1000")
spark.conf.set("spark.cassandra.connection.keep_alive_ms", "600000000")
```

## <a name="next-steps"></a>次のステップ

以下の記事は、Spark と Azure Cosmos DB Cassandra API の統合方法を示しています。 
 
* [DDL 操作](spark-ddl-operations.md)
* [操作を作成/挿入する](spark-create-operations.md)
* [操作を読み取ります。](spark-read-operation.md)
* [操作を Upsert する](spark-upsert-operations.md)
* [操作を削除する](spark-delete-operation.md)
* [集計操作](spark-aggregation-operations.md)
* [テーブル コピー操作](spark-table-copy-operations.md)