---
title: Apache Spark とデュアル書き込みプロキシを使用した Azure Managed Instance for Apache Cassandra へのライブ マイグレーション
description: Apache Spark とデュアル書き込みプロキシを使用して Azure Managed Instance for Apache Cassandra にライブ マイグレーションを行う方法について説明します。
author: TheovanKraay
ms.author: thvankra
ms.service: managed-instance-apache-cassandra
ms.topic: tutorial
ms.date: 11/02/2021
ms.custom: ignite-fall-2021
ms.openlocfilehash: 061fd0f1244221aabb6fa37a84924be653fafbaa
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/02/2021
ms.locfileid: "131032458"
---
# <a name="live-migration-to-azure-managed-instance-for-apache-cassandra-by-using-a-dual-write-proxy"></a>デュアル書き込みプロキシを使用した Azure Managed Instance for Apache Cassandra へのライブ マイグレーション

可能な限り、Apache Cassandra のネイティブ機能を使用し、[ハイブリッド クラスター](configure-hybrid-cluster.md)を構成することで、既存のクラスターから Azure Managed Instance for Apache Cassandra にデータを移行することをお勧めします。 この機能では、Apache Cassandra のゴシップ プロトコルが使用され、ソース データセンターから新しいマネージド インスタンス データセンターにデータがシームレスにレプリケートされます。 ただし、ソース データベースのバージョンに互換性がない場合や、ハイブリッド クラスターのセットアップが実行できない場合があります。 

このチュートリアルでは、[デュアル書き込みプロキシ](https://github.com/Azure-Samples/cassandra-proxy)と Apache Spark を使用して、Azure Managed Instance for Apache Cassandra にデータのライブ マイグレーションを行う方法について説明します。 このアプローチには次のような利点があります。

- **アプリケーションの変更が最小限**。 構成をほとんど、またはまったく変更することなく、プロキシでアプリケーション コードからの接続を受け入れることができます。 すべての要求がソース データベースにルーティングされ、書き込みがセカンダリ ターゲットに非同期にルーティングされます。 
- **クライアントでのワイヤー プロトコルの依存関係**。 このアプローチはバックエンド リソースや内部プロトコルに依存しないため、Apache Cassandra ワイヤー プロトコルが実装されている任意のソースまたはターゲット Cassandra システムで使用できます。

次の図はこのアプローチを示したものです。

:::image type="content" source="./media/migration/live-migration.gif" alt-text="Azure Managed Instance for Apache Cassandra へのデータのライブ マイグレーションを示すアニメーション。" border="false":::

## <a name="prerequisites"></a>前提条件

* [Azure portal](create-cluster-portal.md) または [Azure CLI](create-cluster-cli.md) を使用して、Azure Managed Instance for Apache Cassandra クラスターをプロビジョニングします。 [CQLSH を使用してクラスターに接続](./create-cluster-portal.md#connecting-to-your-cluster)できることを確認します。

* [マネージド Cassandra 仮想ネットワーク内に Azure Databricks アカウントをプロビジョニングします](deploy-cluster-databricks.md)。 アカウントにソース Cassandra クラスターへのネットワーク アクセス権があることも確認します。 履歴データの読み込み用に、このアカウントに Spark クラスターを作成します。

* キースペースまたはテーブル スキームをソース Cassandra データベースからターゲット Cassandra マネージド インスタンス データベースに既に移行していることを確認します。

## <a name="provision-a-spark-cluster"></a>Spark クラスターをプロビジョニングする

Spark 3.0 をサポートする Azure Databricks ランタイム バージョン 7.5 を選択することをお勧めします。

:::image type="content" source="../cosmos-db/cassandra/media/migrate-data-databricks/databricks-runtime.png" alt-text="Azure Databricks ランタイムのバージョンを見つける方法を示すスクリーンショット。":::

## <a name="add-spark-dependencies"></a>Spark の依存関係を追加する

Apache Spark Cassandra コネクタ ライブラリをクラスターに追加して、ネイティブと Azure Cosmos DB Cassandra 両方のエンドポイントに接続する必要があります。 自分のクラスターで、 **[ライブラリ]**  >  **[新規インストール]**  >  **[Maven]** の順に選択し、Maven 座標に `com.datastax.spark:spark-cassandra-connector-assembly_2.12:3.0.0` を追加します。

:::image type="content" source="../cosmos-db/cassandra/media/migrate-data-databricks/databricks-search-packages.png" alt-text="Azure Databricks で Maven パッケージを検索する方法を示すスクリーンショット。":::

**[インストール]** を選択し、インストールが完了したらクラスターを再起動します。

> [!NOTE]
> Cassandra コネクタ ライブラリがインストールされた後で、必ず Azure Databricks クラスターを再起動してください。

## <a name="install-the-dual-write-proxy"></a>デュアル書き込みプロキシをインストールする

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

## <a name="start-the-dual-write-proxy"></a>デュアル書き込みプロキシを開始する

ソース Cassandra クラスターのすべてのノードにプロキシをインストールすることをお勧めします。 少なくとも、各ノード上で次のコマンドを実行してプロキシを開始します。 `<target-server>` をターゲット クラスターのいずれかのノードの IP またはサーバー アドレスに置き換えます。 `<path to JKS file>` をローカル環境の .jks ファイルへのパスに置き換え、`<keystore password>` を対応するパスワードに置き換えます。  

```bash
java -jar target/cassandra-proxy-1.0-SNAPSHOT-fat.jar localhost <target-server> --proxy-jks-file <path to JKS file> --proxy-jks-password <keystore password>
```

この方法でプロキシを開始する場合は、次の条件を満たすことが前提となります。

- ソースとターゲットのエンドポイントで、ユーザー名とパスワードが同じである。
- ソースとターゲットのエンドポイントに、Secure Sockets Layer (SSL) が実装されている。

ソースとターゲットのエンドポイントでこれらの条件が満たされない場合は、詳細な構成オプションを参照してください。

### <a name="configure-ssl"></a>SSL の構成

SSL については、既存のキーストア (ソース クラスターで使用されているものなど) を実装するか、`keytool` を使用して自己署名証明書を作成することができます。

```bash
keytool -genkey -keyalg RSA -alias selfsigned -keystore keystore.jks -storepass password -validity 360 -keysize 2048
```
また、ソースまたはターゲットのエンドポイントに SSL が実装されていない場合は、SSL を無効にすることができます。 `--disable-source-tls` または `--disable-target-tls` のフラグを使用します。

```bash
java -jar target/cassandra-proxy-1.0-SNAPSHOT-fat.jar localhost <target-server> --source-port 9042 --target-port 10350 --proxy-jks-file <path to JKS file> --proxy-jks-password <keystore password> --target-username <username> --target-password <password> --disable-source-tls true  --disable-target-tls true 
```

> [!NOTE]
> データベースへのプロキシ経由の SSL 接続を構築する場合は、必ずデュアル書き込みプロキシに使用したものと同じキーストアとパスワードをクライアント アプリケーションに使用してください。

### <a name="configure-the-credentials-and-port"></a>資格情報とポートを構成する

既定では、ソースの資格情報がクライアント アプリから渡されます。 プロキシにより、ソースとターゲットのクラスターに対する接続を確立するためにその資格情報が使用されます。 前に説明したように、このプロセスでは、ソースとターゲットの資格情報が同じであることを前提としています。 必要な場合は、プロキシを開始するときに、ターゲット Cassandra エンドポイントに異なるユーザー名とパスワードを指定することができます。

```bash
java -jar target/cassandra-proxy-1.0-SNAPSHOT-fat.jar localhost <target-server> --proxy-jks-file <path to JKS file> --proxy-jks-password <keystore password> --target-username <username> --target-password <password>
```

指定されていない場合、ソースとターゲットの既定のポートは 9042 になります。 ターゲットまたはソースの Cassandra エンドポイントが異なるポートで実行されている場合は、`--source-port` または `--target-port` を使用して別のポート番号を指定できます。 

```bash
java -jar target/cassandra-proxy-1.0-SNAPSHOT-fat.jar localhost <target-server> --source-port 9042 --target-port 10350 --proxy-jks-file <path to JKS file> --proxy-jks-password <keystore password> --target-username <username> --target-password <password>
```

### <a name="deploy-the-proxy-remotely"></a>プロキシをリモートでデプロイする

クラスター ノード自体にプロキシをインストールせず、別のマシンにインストールしたい場合があります。 そのようなシナリオでは、`<source-server>` の IP アドレスを指定する必要があります。

```bash
java -jar target/cassandra-proxy-1.0-SNAPSHOT-fat.jar <source-server> <destination-server>
```

> [!NOTE]
> ネイティブ Apache Cassandra クラスターのすべてのノードにプロキシをインストールして実行しない場合、アプリケーションのパフォーマンスに影響します。 クライアント ドライバーで、クラスター内のすべてのノードに対する接続を開くことができません。 

### <a name="allow-zero-application-code-changes"></a>アプリケーションのコードを変更しなくてよいようにする

既定では、プロキシにより、ポート 29042 がリッスンされます。 このポートを指すように、アプリケーションのコードを変更する必要があります。 ただし、プロキシによりリッスンされるポートを変更することができます。 次の方法によりアプリケーション レベルでコードを変更しなくて済むようにしたい場合、これを行うことがあります。

- ソース Cassandra サーバーを別のポートで実行する。
- プロキシを Cassandra の標準ポート 9042 で実行する。

```bash
java -jar target/cassandra-proxy-1.0-SNAPSHOT-fat.jar source-server destination-server --proxy-port 9042
```

> [!NOTE]
> クラスター ノードにプロキシをインストールした場合、ノードを再起動する必要はありません。 ただし、多数のアプリケーション クライアントがあり、アプリケーション レベルのコード変更を不要にするために、プロキシを Cassandra の標準ポート 9042 で実行したい場合は、[Apache Cassandra の既定のポート](https://cassandra.apache.org/doc/latest/cassandra/faq/#what-ports-does-cassandra-use)を変更する必要があります。 その後、クラスター内のノードを再起動し、ソース Cassandra クラスターに対して定義した新しいポートに、ソース ポートを構成する必要があります。 
>
> 次の例では、ポート 3074 で実行するようにソース Cassandra クラスターを変更し、ポート 9042 でクラスターを起動します。
>
>```bash
>java -jar target/cassandra-proxy-1.0-SNAPSHOT-fat.jar source-server destination-server --proxy-port 9042 --source-port 3074
>``` 

### <a name="force-protocols"></a>プロトコルを強制する

このプロキシにはプロトコルを強制する機能があります。これは、ソース エンドポイントがターゲットよりも高度な場合、またはそうしないとサポートされない場合に、必要になることがあります。 その場合は、`--protocol-version` と `--cql-version` を指定して、プロトコルをターゲットに準拠させることができます。

```bash
java -jar target/cassandra-proxy-1.0-SNAPSHOT-fat.jar source-server destination-server --protocol-version 4 --cql-version 3.11
```

デュアル書き込みプロキシが実行されるようになった後、アプリケーション クライアントでポートを変更し、再起動する必要があります。 (または、その方法を選択した場合は、Cassandra のポートを変更してクラスターを再起動します)。その後、プロキシによるターゲット エンドポイントへの書き込みの転送が開始されます。 プロキシ ツールで使用できる[監視とメトリックについてはこちら](https://github.com/Azure-Samples/cassandra-proxy#monitoring)を参照してください。 

## <a name="run-the-historical-data-load"></a>履歴データの読み込みを実行する

データを読み込むには、Azure Databricks アカウントで Scala ノートブックを作成します。 ソースとターゲットの Cassandra 構成を対応する資格情報に置き換え、ソースとターゲットのキースペースとテーブルを置き換えます。 次のサンプルのように、必要に応じて各テーブルに変数を追加し、実行します。 アプリケーションによってデュアル書き込みプロキシへの要求送信が開始されたら、履歴データを移行する準備は完了です。 

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
> 前の Scala サンプルを見ると、ソース テーブル内のすべてのデータが読み取られる前に、`timestamp` が現在の時刻に設定されていることがわかります。 その後、`writetime` がこの過去のタイムスタンプに設定されます。 これは、履歴データが読み込まれている間に、履歴データの読み込みからターゲット エンドポイントに書き込まれたレコードによって、後のタイム スタンプでデュアル書き込みプロキシから送られた更新が上書きされないようにするためです。
>
> 何らかの理由で "*正確な*" タイムスタンプを保持する必要がある場合は、[こちらのサンプル](https://github.com/Azure-Samples/cassandra-migrator)のような、タイムスタンプを保持する履歴データの移行アプローチを採用する必要があります。 

## <a name="validate-the-source-and-target"></a>ソースとターゲットを検証する

履歴データの読み込みが完了すると、データベースは同期され、切り替えの準備が整います。 ただし、最終的に切り替える前に、ソースとターゲットを検証し、要求の結果が一致することを確認することをお勧めします。

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [Azure CLI を利用して Azure Managed Instance for Apache Cassandra リソースを管理する](manage-resources-cli.md)
