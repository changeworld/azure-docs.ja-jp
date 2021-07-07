---
title: Apache Spark とデュアル書き込みプロキシを使用して Azure Managed Instance for Apache Cassandra にライブ マイグレーションを行う
description: Apache Spark とデュアル書き込みプロキシを使用して Azure Managed Instance for Apache Cassandra にライブ マイグレーションを行う方法について説明します。
author: TheovanKraay
ms.author: thvankra
ms.service: managed-instance-apache-cassandra
ms.topic: overview
ms.date: 06/02/2021
ms.openlocfilehash: 2d82e4fa00c387ca2b7de58b223a6abf11ad1491
ms.sourcegitcommit: c072eefdba1fc1f582005cdd549218863d1e149e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/10/2021
ms.locfileid: "111956024"
---
# <a name="live-migration-to-azure-managed-instance-for-apache-cassandra-using-dual-write-proxy"></a>デュアル書き込みプロキシを使用した Azure Managed Instance for Apache Cassandra へのライブ マイグレーション

> [!IMPORTANT]
> Azure Managed Instance for Apache Cassandra は現在、パブリック プレビューの段階にあります。
> このプレビュー バージョンはサービス レベル アグリーメントなしで提供されています。運用環境のワークロードに使用することはお勧めできません。 特定の機能はサポート対象ではなく、機能が制限されることがあります。
> 詳しくは、[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)に関するページをご覧ください。

可能な限り、Apache Cassandra のネイティブ機能を使用し、[ハイブリッド クラスター](configure-hybrid-cluster.md)を構成することで、既存のクラスターから Azure Managed Instance for Apache Cassandra にデータを移行することをお勧めします。 こうすることで、Apache Cassandra のゴシップ プロトコルが使用され、ソース データセンターから新しいマネージド インスタンス データセンターにデータがシームレスにレプリケートされます。 ただし、ソース データベースのバージョンに互換性がない場合や、ハイブリッド クラスターのセットアップが実行できない場合があります。 この記事では、[デュアル書き込みプロキシ](https://github.com/Azure-Samples/cassandra-proxy)と Apache Spark を使用して、Azure Managed Instance for Apache Cassandra にデータのライブ マイグレーションを行う方法について説明します。 このアプローチには次のような利点があります。

- **最小限のアプリケーション変更** - このプロキシを使用すると、ほとんど、またはまったく構成を変更することなく、アプリケーション コードからの接続を受け入れることができます。また、すべての要求がソース データベースにルーティングされ、書き込みがセカンダリ ターゲットに非同期にルーティングされます。 
- **クライアント ワイヤー プロトコル依存** - このアプローチはバックエンド リソースや内部プロトコルに依存しないため、Apache Cassandra ワイヤー プロトコルを実装するあらゆるソースまたはターゲットの Cassandra システムに使用できます。

次の画像は、このアプローチを示しています。


:::image type="content" source="./media/migration/live-migration.gif" alt-text="Azure Managed Instance for Apache Cassandra は Apache Cassandra 向けの管理サービスです。" border="false":::

## <a name="prerequisites"></a>前提条件

* [Azure portal](create-cluster-portal.md) または [Azure CLI](create-cluster-cli.md) を使用して Azure Managed Instance for Apache Cassandra クラスターをプロビジョニングし、[CQLSH を使用してクラスターに接続](./create-cluster-portal.md#connecting-to-your-cluster)できることを確認します。

* [マネージド Cassandra VNet 内に Azure Databricks アカウントをプロビジョニングします](deploy-cluster-databricks.md)。 また、ソース Cassandra クラスターへのネットワーク アクセスがあることも確認します。 履歴データの読み込み用に、このアカウントに Spark クラスターを作成します。

* キースペースまたはテーブル スキームをソース Cassandra データベースからターゲット Cassandra Managed Instance データベースにすでに移行していることを確認します。


## <a name="provision-a-spark-cluster"></a>Spark クラスターをプロビジョニングする

Spark 3.0 をサポートする Azure Databricks ランタイム バージョン 7.5 を選択することをお勧めします。

:::image type="content" source="../cosmos-db/media/cassandra-migrate-cosmos-db-databricks/databricks-runtime.png" alt-text="Databricks ランタイムのバージョンを見つける方法を示すスクリーンショット。":::

## <a name="add-spark-dependencies"></a>Spark の依存関係を追加する

Apache Spark Cassandra コネクタ ライブラリをクラスターに追加して、ネイティブと Azure Cosmos DB Cassandra 両方のエンドポイントに接続する必要があります。 自分のクラスターで、 **[ライブラリ]**  >  **[新規インストール]**  >  **[Maven]** の順に選択し、Maven 座標に `com.datastax.spark:spark-cassandra-connector-assembly_2.12:3.0.0` を追加します。

:::image type="content" source="../cosmos-db/media/cassandra-migrate-cosmos-db-databricks/databricks-search-packages.png" alt-text="Databricks で Maven パッケージを検索する方法を示すスクリーンショット。":::

**[インストール]** を選択し、インストールが完了したらクラスターを再起動します。

> [!NOTE]
> Cassandra コネクタ ライブラリがインストールされたら、必ず Databricks クラスターを再起動してください。

## <a name="install-dual-write-proxy"></a>デュアル書き込みプロキシをインストールする

デュアル書き込み時のパフォーマンスを最適化するために、ソース Cassandra クラスター内のすべてのノードにプロキシをインストールすることをお勧めします。

```bash
#assuming you do not have git already installed
sudo apt-get install git 

#assuming you do not have maven already installed
sudo apt install maven

#clone repo for dual-write proxy
git clone https://github.com/Azure-Samples/cassandra-proxy.git

#change directory
cd cassandra-proxy

#compile the proxy
mvn package
```

## <a name="start-dual-write-proxy"></a>デュアル書き込みプロキシを起動する

ソース Cassandra クラスターのすべてのノードにプロキシをインストールすることをお勧めします。 各ノード上でプロキシを起動するには、最低でも次のコマンドを実行する必要があります。 `<target-server>` をターゲット クラスターのいずれかのノードの IP またはサーバー アドレスに置き換えます。 `<path to JKS file>` をローカルの jks ファイルへのパスに、`<keystore password>` を対応するパスワードに置き換えます。  

```bash
java -jar target/cassandra-proxy-1.0-SNAPSHOT-fat.jar localhost <target-server> --proxy-jks-file <path to JKS file> --proxy-jks-password <keystore password>
```
SSL については、既存のキーストア (ソース クラスターで使用されているものなど) を実装するか、keytool を使用して自己署名証明書を作成することができます。

```bash
keytool -genkey -keyalg RSA -alias selfsigned -keystore keystore.jks -storepass password -validity 360 -keysize 2048
```

> [!NOTE]
> データベースへのプロキシ経由の SSL 接続を構築する場合は、必ずデュアル書き込みプロキシに使用したものと同じキーストアとパスワードをクライアント アプリケーションに使用してください。

この方法でプロキシを起動すると、次のことが確保されます。

- ソース エンドポイントとターゲット エンドポイントが同じユーザー名とパスワードである
- ソース エンドポイントとターゲット エンドポイントに SSL が実装されている

既定では、ソースの資格情報はクライアント アプリから渡され、プロキシにより、ソースとターゲットのクラスターに対する接続を確立するために使用されます。 必要に応じて、プロキシの起動時にターゲット Cassandra エンドポイントのユーザー名とパスワードを個別に指定することができます。

```bash
java -jar target/cassandra-proxy-1.0-SNAPSHOT-fat.jar localhost <target-server> --proxy-jks-file <path to JKS file> --proxy-jks-password <keystore password> --target-username <username> --target-password <password>
```

指定されていない場合、既定のソースとターゲットのポートは `9042` になります。 ターゲットまたはソースの Cassandra エンドポイントのいずれかが別のポートで実行されている場合は、`--source-port` または `--target-port` を使用して別のポート番号を指定できます。 

```bash
java -jar target/cassandra-proxy-1.0-SNAPSHOT-fat.jar localhost <target-server> --source-port 9042 --target-port 10350 --proxy-jks-file <path to JKS file> --proxy-jks-password <keystore password> --target-username <username> --target-password <password>
```

また、ソースまたはターゲットのエンドポイントに SSL が実装されていない場合は、SSL を無効にすることができます。 `--disable-source-tls` または `--disable-target-tls` のフラグを使用します。

```bash
java -jar target/cassandra-proxy-1.0-SNAPSHOT-fat.jar localhost <target-server> --source-port 9042 --target-port 10350 --proxy-jks-file <path to JKS file> --proxy-jks-password <keystore password> --target-username <username> --target-password <password> --disable-source-tls true  --disable-target-tls true 
```

クラスター ノード自体にプロキシをインストールせず、別のマシンにインストールしたい場合があります。 このようなシナリオの場合は、`<source-server>` の IP を指定する必要があります。

```bash
java -jar target/cassandra-proxy-1.0-SNAPSHOT-fat.jar <source-server> <destination-server>
```

> [!NOTE]
> ネイティブ Apache Cassandra クラスターのすべてのノードにプロキシをインストールして実行しない場合、クラスター内のすべてのノードに対する接続をクライアント ドライバーから開くことができなくなるため、アプリケーションのパフォーマンスに影響します。 

既定では、プロキシにより、ポート 29042 がリッスンされます。 ただし、プロキシによりリッスンされるポートを変更することもできます。 ソース Cassandra サーバーを別のポートで実行し、プロキシを標準の Cassandra ポートで実行することで、アプリケーション レベルのコードの変更を排除したい場合は、次のようにすることができます。

```bash
java -jar target/cassandra-proxy-1.0-SNAPSHOT-fat.jar source-server destination-server --proxy-port 9042
```

> [!NOTE]
> クラスター ノードにプロキシをインストールした場合、ノードを再起動する必要はありません。 ただし、多数のアプリケーション クライアントがあり、アプリケーション レベルのコード変更を排除するためにプロキシを標準の Cassandra ポート 9042 で実行したい場合は、クラスターを再起動する必要があります。 

このプロキシにはプロトコルを強制する機能があります。これは、ソース エンドポイントがターゲットよりも高度な場合に必要になることがあります。 その場合は、`--protocol-version` と `--cql-version` を指定できます。

```bash
java -jar target/cassandra-proxy-1.0-SNAPSHOT-fat.jar source-server destination-server --protocol-version 4 --cql-version 3.11
```

デュアル書き込みプロキシを起動して実行したら、アプリケーション クライアントのポートを変更し、再起動する必要があります (または、このアプローチを選択した場合、Cassandra のポートを変更してクラスターを再起動します)。 これで、プロキシによるターゲット エンドポイントへの書き込みの転送が開始されます。 プロキシ ツールで使用できる[監視とメトリックについてはこちら](https://github.com/Azure-Samples/cassandra-proxy#monitoring)を参照してください。 


## <a name="run-the-historic-data-load"></a>履歴データの読み込みを実行します。

データを読み込むには、Databricks アカウントで Scala ノートブックを作成します。 ソースとターゲットの Cassandra 構成を、対応する資格情報、ソースとターゲットのキースペースおよびテーブルで置き換えます。 次のサンプルのように、必要に応じて各テーブルに変数を追加し、実行します。 アプリケーションによってデュアル書き込みプロキシへの要求送信が開始されたら、履歴データを移行する準備は完了です。 

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
    "spark.cassandra.connection.ssl.enabled" -> "true",
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
    "spark.cassandra.connection.remoteConnectionsPerExecutor" -> "1",
    "spark.cassandra.concurrent.reads" -> "512",
    "spark.cassandra.output.batch.grouping.buffer.size" -> "1000",
    "spark.cassandra.connection.keep_alive_ms" -> "600000000"
)

//set timestamp to ensure it is before read job starts
val timestamp: Long = System.currentTimeMillis / 1000

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
  .option("writetime", timestamp)
  .mode(SaveMode.Append)
  .save
```

> [!NOTE]
> 上の Scala のサンプルを見ると、ソース テーブル内のすべてのデータが読み取られる前に、`timestamp` が現在の時刻に設定され、その後、`writetime` がこの過去のタイムスタンプに設定されていることがわかります。 これは、履歴データが読み込まれている間に、履歴データの読み込みからターゲット エンドポイントに書き込まれたレコードによって、後のタイム スタンプでデュアル書き込みプロキシから送られた更新が上書きされないようにするためです。 何らかの理由で "*正確な*" タイムスタンプを保持する必要がある場合は、[この](https://github.com/scylladb/scylla-migrator) サンプルのように、タイムスタンプを保持する履歴データの移行アプローチを採用することをお勧めします。 

## <a name="validation"></a>検証

履歴データの読み込みが完了すると、データベースは同期され、切り替えの準備が整います。 ただし、最終的に切り替える前に、ソースとターゲットの検証を行い、要求の結果が一致することの確認をお勧めします。


## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [Azure CLI を利用して Azure Managed Instance for Apache Cassandra リソースを管理する](manage-resources-cli.md)