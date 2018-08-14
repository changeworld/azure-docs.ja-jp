---
title: 'チュートリアル: Apache Kafka Streams API の使用 - Azure HDInsight '
description: HDInsight 上の Kafka で Apache Kafka Streams API を使用する方法を説明します。 この API を使用して、Kafka でトピック間のストリーム処理を実行できます。
services: hdinsight
ms.service: hdinsight
author: jasonwhowell
ms.author: jasonh
editor: jasonwhowell
ms.custom: hdinsightactive
ms.topic: tutorial
ms.date: 04/17/2018
ms.openlocfilehash: d285575802dd830247533420154f6f5e868272a2
ms.sourcegitcommit: 35ceadc616f09dd3c88377a7f6f4d068e23cceec
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/08/2018
ms.locfileid: "39621047"
---
# <a name="tutorial-apache-kafka-streams-api"></a>チュートリアル: Apache Kafka Streams API

Kafka Streams API を使用するアプリケーションを作成し、HDInsight 上の Kafka でそれを実行する方法を説明します。 

このチュートリアルで使うアプリケーションはストリーミング ワード カウントです。 Kafka トピックからテキスト データを読み取り、個々のワードを抽出してから、ワードとカウントを別の Kafka トピックに保存します。

> [!NOTE]
> Kafka のストリーム処理は、多くの場合、Apache Spark または Storm を使用して実行されます。 Kafka バージョン 0.10.0 (HDInsight 3.5 および 3.6 での) では、Kafka Streams API が導入されました。 この API を使用して、入力および出力トピック間でデータ ストリームを変換できます。 場合により、Spark または Storm ストリーミング ソリューションを作成する代わりになることがあります。 
>
> Kafka Streams の詳細については、Apache.org の「[Intro to Streams](https://kafka.apache.org/10/documentation/streams/)」ドキュメントを参照してください。

このチュートリアルで学習する内容は次のとおりです。

> [!div class="checklist"]
> * 開発環境を設定する
> * コードの理解
> * アプリケーションをビルドしてデプロイする
> * Kafka トピックを構成する
> * コードの実行

## <a name="prerequisites"></a>前提条件

* HDInsight 3.6 クラスター上の Kafka。 HDInsight クラスターに Kafka を作成する方法については、[HDInsight での Kafka の開始](apache-kafka-get-started.md)に関するドキュメントをご覧ください。

* [Kafka Consumer および Producer API](apache-kafka-producer-consumer-api.md) に関するドキュメントの手順を完了します。 このドキュメントの手順では、このチュートリアルで作成したアプリケーションとトピックの例を使用します。

## <a name="set-up-your-development-environment"></a>開発環境を設定する

開発環境に、次のコンポーネントがインストールされている必要があります。

* [Java JDK 8](http://www.oracle.com/technetwork/java/javase/downloads/index.html) または同等の OpenJDK など。

* [Apache Maven](http://maven.apache.org/)

* SSH クライアントと `scp` コマンド。 詳細については、[HDInsight での SSH の使用](../hdinsight-hadoop-linux-use-ssh-unix.md)に関するドキュメントを参照してください。

## <a name="understand-the-code"></a>コードの理解

アプリケーションの例は、[https://github.com/Azure-Samples/hdinsight-kafka-java-get-started](https://github.com/Azure-Samples/hdinsight-kafka-java-get-started) の `Streaming` サブディレクトリにあります。 アプリケーションは、次の 2 つのファイルで構成されます。

* `pom.xml`: このファイルは、プロジェクトの依存関係、Java バージョン、およびパッケージ化メソッドを定義します。
* `Stream.java`: このファイルは、ストリーミング ロジックを実装します。

### <a name="pomxml"></a>Pom.xml

`pom.xml` ファイル内で理解すべき重要な点は、次のとおりです。

* 依存関係: このプロジェクトは、`kafka-clients` パッケージによって提供される Kafka Streams API に依存します。 次の XML コードがこの依存関係を定義します。

    ```xml
    <!-- Kafka client for producer/consumer operations -->
    <dependency>
      <groupId>org.apache.kafka</groupId>
      <artifactId>kafka-clients</artifactId>
      <version>${kafka.version}</version>
    </dependency>
    ```

    > [!NOTE]
    > `${kafka.version}` エントリは `pom.xml` の `<properties>..</properties>` セクション内で宣言され、Kafka バージョンの HDInsight クラスターに構成されています。

* プラグイン: Maven プラグインはさまざまな機能を備えています。 このプロジェクトでは、次のプラグインが使用されます。

    * `maven-compiler-plugin`: プロジェクトで使用される Java バージョンを 8 に設定するために使用します。 HDInsight 3.6 では Java 8 が必要です。
    * `maven-shade-plugin`: このアプリケーションとすべての依存関係を含む uber jar を生成するために使用します。 また、アプリケーションのエントリ ポイントの設定にも使用されるため、メイン クラスを指定しなくても Jar ファイルを直接実行できます。

### <a name="streamjava"></a>Stream.java

`Stream.java` ファイルは、Streams API を使用してワード カウント アプリケーションを実装します。 これは、`test` という名前の Kafka トピックからデータを読み取り、`wordcounts` という名前のトピックにワード カウントを書き込みます。

次のコードは、ワード カウント アプリケーションを定義します。

```java
package com.microsoft.example;

import org.apache.kafka.common.serialization.Serde;
import org.apache.kafka.common.serialization.Serdes;
import org.apache.kafka.streams.KafkaStreams;
import org.apache.kafka.streams.KeyValue;
import org.apache.kafka.streams.StreamsConfig;
import org.apache.kafka.streams.kstream.KStream;
import org.apache.kafka.streams.kstream.KStreamBuilder;

import java.util.Arrays;
import java.util.Properties;

public class Stream
{
    public static void main( String[] args ) {
        Properties streamsConfig = new Properties();
        // The name must be unique on the Kafka cluster
        streamsConfig.put(StreamsConfig.APPLICATION_ID_CONFIG, "wordcount-example");
        // Brokers
        streamsConfig.put(StreamsConfig.BOOTSTRAP_SERVERS_CONFIG, args[0]);
        // SerDes for key and values
        streamsConfig.put(StreamsConfig.KEY_SERDE_CLASS_CONFIG, Serdes.String().getClass().getName());
        streamsConfig.put(StreamsConfig.VALUE_SERDE_CLASS_CONFIG, Serdes.String().getClass().getName());

        // Serdes for the word and count
        Serde<String> stringSerde = Serdes.String();
        Serde<Long> longSerde = Serdes.Long();

        KStreamBuilder builder = new KStreamBuilder();
        KStream<String, String> sentences = builder.stream(stringSerde, stringSerde, "test");
        KStream<String, Long> wordCounts = sentences
                .flatMapValues(value -> Arrays.asList(value.toLowerCase().split("\\W+")))
                .map((key, word) -> new KeyValue<>(word, word))
                .countByKey("Counts")
                .toStream();
        wordCounts.to(stringSerde, longSerde, "wordcounts");

        KafkaStreams streams = new KafkaStreams(builder, streamsConfig);
        streams.start();

        Runtime.getRuntime().addShutdownHook(new Thread(streams::close));
    }
}
```


## <a name="build-and-deploy-the-example"></a>例を構築してデプロイする

プロジェクトを構築し、HDInsight クラスター上の Kafka にデプロイするには、次の手順を使用します。

1. 例を [https://github.com/Azure-Samples/hdinsight-kafka-java-get-started](https://github.com/Azure-Samples/hdinsight-kafka-java-get-started) からダウンロードします。

2. `Streaming` ディレクトリに移動し、次のコマンドを使用して、jar パッケージを作成します。

    ```bash
    mvn clean package
    ```

    このコマンドは、`target/kafka-streaming-1.0-SNAPSHOT.jar` にパッケージを作成します。

3. 次のコマンドを使用して、HDInsight クラスターに `kafka-streaming-1.0-SNAPSHOT.jar` ファイルをコピーします。
   
    ```bash
    scp ./target/kafka-streaming-1.0-SNAPSHOT.jar sshuser@clustername-ssh.azurehdinsight.net:kafka-streaming.jar
    ```
   
    `sshuser` は、クラスターの SSH ユーザーに置き換えます。また、`clustername` はクラスターの名前に置き換えます。 メッセージが表示されたら、SSH ユーザー アカウントのパスワードを入力します。 HDInsight での `scp` の使用の詳細については、「[HDInsight で SSH を使用する](../hdinsight-hadoop-linux-use-ssh-unix.md)」を参照してください。

## <a name="create-kafka-topics"></a>Kafka トピックの作成

1. クラスターとの SSH 接続を開くには、次のコマンドを使用します。

    ```bash
    ssh sshuser@clustername-ssh.azurehdinsight.net
    ```

    `sshuser` は、クラスターの SSH ユーザーに置き換えます。また、`clustername` はクラスターの名前に置き換えます。 メッセージが表示されたら、SSH ユーザー アカウントのパスワードを入力します。 HDInsight での `scp` の使用の詳細については、「[HDInsight で SSH を使用する](../hdinsight-hadoop-linux-use-ssh-unix.md)」を参照してください。

2. クラスター名を変数に保存して JSON 解析ユーティリティ (`jq`) をインストールするには、次のコマンドを使用します。 メッセージが表示されたら、Kafka クラスター名を入力します。

    ```bash
    sudo apt -y install jq
    read -p 'Enter your Kafka cluster name:' CLUSTERNAME
    ```

3. Kafka ブローカー ホストと Zookeeper ホストを取得するには、次のコマンドを使用します。 プロンプトが表示されたら、クラスターのログイン (管理者) アカウントのパスワードを入力します。 パスワードを 2 回入力するように求められます。

    ```bash
    export KAFKAZKHOSTS=`curl -sS -u admin -G https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME/services/ZOOKEEPER/components/ZOOKEEPER_SERVER | jq -r '["\(.host_components[].HostRoles.host_name):2181"] | join(",")' | cut -d',' -f1,2`; \
    export KAFKABROKERS=`curl -sS -u admin -G https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME/services/KAFKA/components/KAFKA_BROKER | jq -r '["\(.host_components[].HostRoles.host_name):9092"] | join(",")' | cut -d',' -f1,2`; \
    ```

4. ストリーミング操作で使用するトピックを作成するには、次のコマンドを使用します。

    > [!NOTE]
    > `test` トピックが既に存在するというエラーが発生する可能性があります。 これは問題ありません。Producer および Consumer API のチュートリアルで既に作成されているためです。

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --create --replication-factor 3 --partitions 8 --topic test --zookeeper $KAFKAZKHOSTS

    /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --create --replication-factor 3 --partitions 8 --topic wordcounts --zookeeper $KAFKAZKHOSTS

    /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --create --replication-factor 3 --partitions 8 --topic RekeyedIntermediateTopic --zookeeper $KAFKAZKHOSTS

    /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --create --replication-factor 3 --partitions 8 --topic wordcount-example-Counts-changelog --zookeeper $KAFKAZKHOSTS
    ```

    トピックは、次の目的で使用されます。

    * `test`: このトピックは、レコードが受信される場所です。 ストリーミング アプリケーションはここから読み取ります。
    * `wordcounts`: このトピックは、ストリーミング アプリケーションがその出力を格納する場所です。
    * `RekeyedIntermediateTopic`: このトピックは、`countByKey` 演算子によってカウントが更新される場合にデータ パーティションを再作成するために使用されます。
    * `wordcount-example-Counts-changelog`: このトピックは、`countByKey` 演算で使用される状態ストアです

    > [!IMPORTANT]
    > HDInsight 上の Kafka は、トピックを自動的に作成するように構成することもできます。 詳細については、[自動トピック作成の構成](apache-kafka-auto-create-topics.md)に関するドキュメントをご覧ください。

## <a name="run-the-code"></a>コードの実行

1. ストリーミング アプリケーションをバック グラウンド プロセスとして起動するには、次のコマンドを使用します。

    ```bash
    java -jar kafka-streaming.jar $KAFKABROKERS $KAFKAZKHOSTS &
    ```

    > [!NOTE]
    > log4j に関する警告が表示されることがあります。 これは無視できます。

2. レコードを `test` トピックに送信するには、次のコマンドを使用してプロデューサー アプリケーションを起動します。

    ```bash
    java -jar kafka-producer-consumer.jar producer test $KAFKABROKERS
    ```

3. プロデューサーが完了したら、次のコマンドを使用して、`wordcounts` トピックに保存されている情報を表示します。

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-console-consumer.sh --bootstrap-server $KAFKABROKERS --topic wordcounts --formatter kafka.tools.DefaultMessageFormatter --property print.key=true --property key.deserializer=org.apache.kafka.common.serialization.StringDeserializer --property value.deserializer=org.apache.kafka.common.serialization.LongDeserializer --from-beginning
    ```

    > [!NOTE]
    > `--property` パラメーターはコンソール コンシューマーに、カウント (値) と共にキー (ワード) を出力するように指示します。 このパラメーターは、Kafka からこれらの値を読み取るときに使用するデシリアライザーも構成します。

    出力は次のテキストのようになります。
   
        dwarfs  13635
        ago     13664
        snow    13636
        dwarfs  13636
        ago     13665
        a       13803
        ago     13666
        a       13804
        ago     13667
        ago     13668
        jumped  13640
        jumped  13641
   
    > [!NOTE]
    > パラメーター `--from-beginning` は、トピックに保存されているレコードの先頭から開始するようにコンシューマーを構成します。 カウントはワードが検出されるたびに増加するため、トピックには、ワードごとにカウントが増加する複数のエントリが含まれます。

7. __Ctrl + C__ キーを使用してプロデューサーを終了します。 引き続き __Ctrl + C__ キーを使用して、アプリケーションとコンシューマーを終了します。

## <a name="next-steps"></a>次の手順

このドキュメントでは、HDInsight 上の Kafka で Kafka Streams API を使用する方法について説明しました。 次の各ドキュメントを参考に、Kafka の使用の詳細を確認してください。

* [Kafka ログの分析](apache-kafka-log-analytics-operations-management.md)
* [Kafka クラスター間でデータをレプリケートする](apache-kafka-mirroring.md)
