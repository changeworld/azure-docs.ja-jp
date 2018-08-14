---
title: 'チュートリアル: Apache Kafka Producer および Consumer API の使用 - Azure HDInsight '
description: HDInsight 上の Kafka で Apache Kafka Producer および Consumer API を使用する方法を説明します。 このチュートリアルでは、これらの API を Java アプリケーションから HDInsight 上の Kafka で使用する方法を学習します。
services: hdinsight
author: jasonwhowell
ms.author: jasonh
editor: jasonwhowell
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: tutorial
ms.date: 04/16/2018
ms.openlocfilehash: 8b20b2aa75c3872df1082ef1059000d80a2dd472
ms.sourcegitcommit: 35ceadc616f09dd3c88377a7f6f4d068e23cceec
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/08/2018
ms.locfileid: "39621098"
---
# <a name="tutorial-use-the-apache-kafka-producer-and-consumer-apis"></a>チュートリアル: Apache Kafka Producer および Consumer API の使用

HDInsight 上の Kafka で Kafka Producer および Consumer API を使用する方法を説明します。

Kafka Producer API では、アプリケーションが Kafka クラスターにデータ ストリームを送信できます。 Kafka Consumer API では、アプリケーションがクラスターからデータ ストリームを読み取ることができます。

このチュートリアルで学習する内容は次のとおりです。

> [!div class="checklist"]
> * 開発環境を設定する
> * デプロイ環境を設定する
> * コードの理解
> * アプリケーションをビルドしてデプロイする
> * クラスターでアプリケーションを実行する

API の詳細については、[Producer API](https://kafka.apache.org/documentation/#producerapi) と [Consumer API](https://kafka.apache.org/documentation/#consumerapi) に関する Apache のドキュメントをご覧ください。

## <a name="set-up-your-development-environment"></a>開発環境を設定する

開発環境に、次のコンポーネントがインストールされている必要があります。

* [Java JDK 8](http://www.oracle.com/technetwork/java/javase/downloads/index.html) または同等の OpenJDK など。

* [Apache Maven](http://maven.apache.org/)

* SSH クライアントと `scp` コマンド。 詳細については、[HDInsight での SSH の使用](../hdinsight-hadoop-linux-use-ssh-unix.md)に関するドキュメントを参照してください。

* テキスト エディターまたは Java IDE。

開発用ワークステーションに Java と JDK をインストールするときに、次のような環境変数が設定される場合があります。 ただし、これらが存在するかどうかや、システムに対して適切な値が含まれているかを確認する必要があります。

* `JAVA_HOME` - JDK がインストールされているディレクトリを指している必要があります。
* `PATH` - 次のパスを含む必要があります。
  
    * `JAVA_HOME` (または同等のパス)。
    * `JAVA_HOME\bin` (または同等のパス)。
    * Maven がインストールされているディレクトリ。

## <a name="set-up-your-deployment-environment"></a>デプロイ環境を設定する

このチュートリアルでは、HDInsight 3.6 上の Kafka が必要です。 HDInsight クラスターに Kafka を作成する方法については、[HDInsight での Kafka の開始](apache-kafka-get-started.md)に関するドキュメントをご覧ください。

## <a name="understand-the-code"></a>コードの理解

アプリケーションの例は、[https://github.com/Azure-Samples/hdinsight-kafka-java-get-started](https://github.com/Azure-Samples/hdinsight-kafka-java-get-started) の `Producer-Consumer` サブディレクトリにあります。 アプリケーションは、主に次の 4 つのファイルで構成されます。

* `pom.xml`: このファイルは、プロジェクトの依存関係、Java バージョン、およびパッケージ化メソッドを定義します。
* `Producer.java`: このファイルは、Producer API を使用して Kafka に 100 万のランダムな文を送信します。
* `Consumer.java`: このファイルは、Consumer API を使用して Kafka からデータを読み取り、それを STDOUT に出力します。
* `Run.java`: プロデューサーおよびコンシューマーのコードの実行に使用されるコマンド ライン インターフェイスです。

### <a name="pomxml"></a>Pom.xml

`pom.xml` ファイル内で理解すべき重要な点は、次のとおりです。

* 依存関係: このプロジェクトは、`kafka-clients` パッケージによって提供される Kafka Producer および Consumer API に依存します。 次の XML コードがこの依存関係を定義します。

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

    * `maven-compiler-plugin`: プロジェクトで使用される Java バージョンを 8 に設定するために使用します。 これは、HDInsight 3.6 によって使用される Java のバージョンです。
    * `maven-shade-plugin`: このアプリケーションとすべての依存関係を含む uber jar を生成するために使用します。 また、アプリケーションのエントリ ポイントの設定にも使用されるため、メイン クラスを指定しなくても Jar ファイルを直接実行できます。

### <a name="producerjava"></a>Producer.java

プロデューサーは、Kafka ブローカー ホスト (ワーカー ノード) と通信して、Kafka トピックにデータを保存します。 次のコード スニペットは、`Producer.java` ファイルからのものです。

```java
package com.microsoft.example;

import org.apache.kafka.clients.producer.KafkaProducer;
import org.apache.kafka.clients.producer.ProducerRecord;
import java.util.Properties;
import java.util.Random;
import java.io.IOException;

public class Producer
{
    public static void produce(String brokers) throws IOException
    {

        // Set properties used to configure the producer
        Properties properties = new Properties();
        // Set the brokers (bootstrap servers)
        properties.setProperty("bootstrap.servers", brokers);
        // Set how to serialize key/value pairs
        properties.setProperty("key.serializer","org.apache.kafka.common.serialization.StringSerializer");
        properties.setProperty("value.serializer","org.apache.kafka.common.serialization.StringSerializer");
        KafkaProducer<String, String> producer = new KafkaProducer<>(properties);

        // So we can generate random sentences
        Random random = new Random();
        String[] sentences = new String[] {
                "the cow jumped over the moon",
                "an apple a day keeps the doctor away",
                "four score and seven years ago",
                "snow white and the seven dwarfs",
                "i am at two with nature"
        };

        String progressAnimation = "|/-\\";
        // Produce a bunch of records
        for(int i = 0; i < 1000000; i++) {
            // Pick a sentence at random
            String sentence = sentences[random.nextInt(sentences.length)];
            // Send the sentence to the test topic
            producer.send(new ProducerRecord<String, String>("test", sentence));
            String progressBar = "\r" + progressAnimation.charAt(i % progressAnimation.length()) + " " + i;
            System.out.write(progressBar.getBytes());
        }
    }
}
```

このコードは、Kafka ブローカー ホスト (ワーカー ノード) に接続してから、Producer API を使用して 100 万の文を Kafka に送信します。

### <a name="consumerjava"></a>Consumer.java

コンシューマーは、Kafka ブローカー ホスト (ワーカー ノード) と通信し、ループ内のレコードを読み取ります。 次のコード スニペットは、`Consumer.java` ファイルからのものです。

```java
package com.microsoft.example;

import org.apache.kafka.clients.consumer.KafkaConsumer;
import org.apache.kafka.clients.consumer.ConsumerRecords;
import org.apache.kafka.clients.consumer.ConsumerRecord;
import java.util.Properties;
import java.util.Arrays;

public class Consumer {
    public static void consume(String brokers, String groupId) {
        // Create a consumer
        KafkaConsumer<String, String> consumer;
        // Configure the consumer
        Properties properties = new Properties();
        // Point it to the brokers
        properties.setProperty("bootstrap.servers", brokers);
        // Set the consumer group (all consumers must belong to a group).
        properties.setProperty("group.id", groupId);
        // Set how to serialize key/value pairs
        properties.setProperty("key.deserializer","org.apache.kafka.common.serialization.StringDeserializer");
        properties.setProperty("value.deserializer","org.apache.kafka.common.serialization.StringDeserializer");
        // When a group is first created, it has no offset stored to start reading from. This tells it to start
        // with the earliest record in the stream.
        properties.setProperty("auto.offset.reset","earliest");
        consumer = new KafkaConsumer<>(properties);

        // Subscribe to the 'test' topic
        consumer.subscribe(Arrays.asList("test"));

        // Loop until ctrl + c
        int count = 0;
        while(true) {
            // Poll for records
            ConsumerRecords<String, String> records = consumer.poll(200);
            // Did we get any?
            if (records.count() == 0) {
                // timeout/nothing to read
            } else {
                // Yes, loop over records
                for(ConsumerRecord<String, String> record: records) {
                    // Display record and count
                    count += 1;
                    System.out.println( count + ": " + record.value());
                }
            }
        }
    }
}
```

このコードでは、コンシューマーがトピックの先頭から読み取るように構成されます (`auto.offset.reset` は `earliest` に設定されています)。

### <a name="runjava"></a>Run.java

`Run.java` ファイルは、プロデューサーまたはコンシューマーのいずれかのコードを実行するコマンド ライン インターフェイスを提供します。 パラメーターとして Kafka ブローカー ホスト情報を指定する必要があります。 必要に応じて、コンシューマー プロセスで使用されるグループ ID 値を含めることができます。 同じグループ ID を使用して複数のコンシューマー インスタンスを作成すると、それらはトピックからの読み取りの負荷を分散します。

```java
package com.microsoft.example;

import java.io.IOException;
import java.util.UUID;

// Handle starting producer or consumer
public class Run {
    public static void main(String[] args) throws IOException {
        if(args.length < 2) {
            usage();
        }

        // Get the brokers
        String brokers = args[1];
        switch(args[0].toLowerCase()) {
            case "producer":
                Producer.produce(brokers);
                break;
            case "consumer":
                // Either a groupId was passed in, or we need a random one
                String groupId;
                if(args.length == 3) {
                    groupId = args[2];
                } else {
                    groupId = UUID.randomUUID().toString();
                }
                Consumer.consume(brokers, groupId);
                break;
            default:
                usage();
        }
        System.exit(0);
    }
    // Display usage
    public static void usage() {
        System.out.println("Usage:");
        System.out.println("kafka-example.jar <producer|consumer> brokerhosts [groupid]");
        System.exit(1);
    }
}
```

## <a name="build-and-deploy-the-example"></a>例を構築してデプロイする

1. 例を [https://github.com/Azure-Samples/hdinsight-kafka-java-get-started](https://github.com/Azure-Samples/hdinsight-kafka-java-get-started) からダウンロードします。

2. `Producer-Consumer` ディレクトリの場所に移動し、次のコマンドを使用します。

    ```
    mvn clean package
    ```

    このコマンドにより、`kafka-producer-consumer-1.0-SNAPSHOT.jar` というファイルを含む `target` という名前のディレクトリが作成されます。

3. 次のコマンドを使用して、HDInsight クラスターに `kafka-producer-consumer-1.0-SNAPSHOT.jar` ファイルをコピーします。
   
    ```bash
    scp ./target/kafka-producer-consumer-1.0-SNAPSHOT.jar SSHUSER@CLUSTERNAME-ssh.azurehdinsight.net:kafka-producer-consumer.jar
    ```
   
    **SSHUSER** は、クラスターの SSH ユーザーに置き換えます。また、**CLUSTERNAME** はクラスターの名前に置き換えます。 メッセージが表示されたら、SSH ユーザーのパスワードを入力します。

## <a id="run"></a>例を実行する

1. クラスターとの SSH 接続を開くには、次のコマンドを使用します。

    ```bash
    ssh SSHUSER@CLUSTERNAME-ssh.azurehdinsight.net
    ```

    **SSHUSER** は、クラスターの SSH ユーザーに置き換えます。また、**CLUSTERNAME** はクラスターの名前に置き換えます。 メッセージが表示されたら、SSH ユーザー アカウントのパスワードを入力します。 HDInsight での `scp` の使用の詳細については、「[HDInsight で SSH を使用する](../hdinsight-hadoop-linux-use-ssh-unix.md)」を参照してください。

2. この例によって使用される Kafka トピックを作成するには、次の手順に従います。

    1. クラスター名を変数に保存して JSON 解析ユーティリティ (`jq`) をインストールするには、次のコマンドを使用します。 メッセージが表示されたら、Kafka クラスター名を入力します。
    
        ```bash
        sudo apt -y install jq
        read -p 'Enter your Kafka cluster name:' CLUSTERNAME
        ```
    
    2. Kafka ブローカー ホストと Zookeeper ホストを取得するには、次のコマンドを使用します。 プロンプトが表示されたら、クラスターのログイン (管理者) アカウントのパスワードを入力します。
    
        ```bash
        export KAFKAZKHOSTS=`curl -sS -u admin -G https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME/services/ZOOKEEPER/components/ZOOKEEPER_SERVER | jq -r '["\(.host_components[].HostRoles.host_name):2181"] | join(",")' | cut -d',' -f1,2`; \
        export KAFKABROKERS=`curl -sS -u admin -G https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME/services/KAFKA/components/KAFKA_BROKER | jq -r '["\(.host_components[].HostRoles.host_name):9092"] | join(",")' | cut -d',' -f1,2`; \
        ```

    3. `test` トピックを作成するには、次のコマンドを使用します。

        ```bash
        /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --create --replication-factor 3 --partitions 8 --topic test --zookeeper $KAFKAZKHOSTS
        ```
    4. jar ファイルを使用してトピックを作成することもできます。 たとえば `test2` トピックを作成するには、次のコマンドを使用します。

        ```bash
        java -jar kafka-producer-consumer.jar create test2 $KAFKABROKERS
        ```

3. プロデューサーを実行し、トピックにデータを書き込むには、次のコマンドを使用します。

    ```bash
    java -jar kafka-producer-consumer.jar producer test $KAFKABROKERS
    ```

4. プロデューサーが完了したら、次のコマンドを使用してトピックから読み取ります。
   
    ```bash
    java -jar kafka-producer-consumer.jar consumer test $KAFKABROKERS
    ```
   
    読み取られたレコードが、レコードの件数とともに表示されます。

5. __Ctrl+C__ キーを使用してコンシューマーを終了します。

### <a name="multiple-consumers"></a>複数のコンシューマー

Kafka コンシューマーは、レコードを読み取る際に、コンシューマー グループを使用します。 複数のコンシューマーで同じグループを使用すると、トピックからの読み取りの負荷が分散されます。 グループ内の各コンシューマーは、レコードの一部を受け取ります。

コンシューマー アプリケーションは、グループ ID として使用されるパラメーターを受け取ります。 たとえば、次のコマンドは `mygroup` のグループ ID を使用して、コンシューマーを起動します。
   
```bash
java -jar kafka-producer-consumer.jar consumer test $KAFKABROKERS mygroup
```

このプロセスを実際に確認するには、次のコマンドを使用します。

```bash
tmux new-session 'java -jar kafka-producer-consumer.jar consumer test $KAFKABROKERS mygroup' \; split-w
indow -h 'java -jar kafka-producer-consumer.jar consumer test $KAFKABROKERS mygroup' \; attach
```

このコマンドは、`tmux` を使用してターミナルを 2 つの列に分割します。 同じグループ ID 値を持つ各列でコンシューマーが開始されます。 コンシューマーが読み取りを完了した後は、それぞれがレコードの一部だけを読み取ることに注意してください。 __Ctrl + C __ を 2 回使用して、`tmux` を終了します。

同じグループ内のクライアントによる消費は、トピックのパーティションを介して処理されます。 先ほど作成した `test` トピックには、8 つのパーティションがあります。 8 つのコンシューマーを開始すると、各コンシューマーはトピックの 1 つのパーティションからレコードを読み取ります。

> [!IMPORTANT]
> コンシューマー グループに、パーティションの数よりも多いコンシューマー インスタンスを含めることはできません。 この例では、トピック内のパーティション数が 8 であるため、1 つのコンシューマー グループに最大 8 個のコンシューマーを含めることができます。 または、それぞれコンシューマーが 8 個以下の複数のコンシューマー グループを存在させることができます。

Kafka に格納されたレコードは、受信した順番でパーティション内に格納されます。 *パーティション内*のレコードの順次配信順を実現するには、コンシューマー インスタンスの数がパーティションの数と同じコンシューマー グループを作成します。 *トピック内*のレコードの順次配信を実現するには、コンシューマー インスタンスが 1 つのみのコンシューマー グループを作成します。

## <a name="next-steps"></a>次の手順

このドキュメントでは、HDInsight 上の Kafka で Kafka Producer および Consumer API を使用する方法について説明しました。 次の各ドキュメントを参考に、Kafka の使用の詳細を確認してください。

* [Kafka ログの分析](apache-kafka-log-analytics-operations-management.md)
* [Kafka クラスター間でデータをレプリケートする](apache-kafka-mirroring.md)
* [HDInsight における Kafka Streams API](apache-kafka-streams-api.md)
