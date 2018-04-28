---
title: Apache Kafka Streams API の使用 - Azure HDInsight | Microsoft Docs
description: HDInsight 上の Kafka で Apache Kafka Streams API を使用する方法を説明します。 この API を使用して、Kafka でトピック間のストリーム処理を実行できます。
services: hdinsight
documentationcenter: ''
author: Blackmist
manager: cgronlun
editor: cgronlun
tags: azure-portal
ms.service: hdinsight
ms.custom: hdinsightactive
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 04/10/2018
ms.author: larryfr
ms.openlocfilehash: 36d67cdb99871f3948db1f6497b1a4638df4f3f1
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/16/2018
---
# <a name="apache-kafka-streams-api"></a>Apache Kafka Streams API

Kafka Streams API を使用するアプリケーションを作成し、HDInsight 上の Kafka でそれを実行する方法を説明します。

Apache Kafka を操作している場合、ストリーム処理は多くの場合に Apache Spark または Storm を使用して実行されます。 Kafka バージョン 0.10.0 (HDInsight 3.5 および 3.6 での) では、Kafka Streams API が導入されました。 この API を使用して、Kafka で実行するアプリケーションを使用して、入力および出力トピック間で、データ ストリームを変換できます。 場合により、Spark または Storm ストリーミング ソリューションを作成する代わりになることがあります。 Kafka Streams の詳細については、Apache.org の「[Intro to Streams](https://kafka.apache.org/10/documentation/streams/)」ドキュメントを参照してください。

## <a name="set-up-your-development-environment"></a>開発環境を設定する

開発環境に、次のコンポーネントがインストールされている必要があります。

* [Java JDK 8](http://www.oracle.com/technetwork/java/javase/downloads/index.html) または同等の OpenJDK など。

* [Apache Maven](http://maven.apache.org/)

* SSH クライアントと `scp` コマンド。 詳細については、[HDInsight での SSH の使用](../hdinsight-hadoop-linux-use-ssh-unix.md)に関するドキュメントを参照してください。

## <a name="set-up-your-deployment-environment"></a>デプロイ環境を設定する

この例では、HDInsight 3.6 上の Kafka が必要です。 HDInsight クラスターに Kafka を作成する方法については、[HDInsight での Kafka の開始](apache-kafka-get-started.md)に関するドキュメントをご覧ください。

## <a name="build-and-deploy-the-example"></a>例を構築してデプロイする

次の手順を使用して、プロジェクトを構築し、HDInsight クラスター上の Kafka にデプロイします。

1. 例は [https://github.com/Azure-Samples/hdinsight-kafka-java-get-started](https://github.com/Azure-Samples/hdinsight-kafka-java-get-started) からダウンロードしてください。

2. `Streaming` ディレクトリに移動し、次のコマンドを使用して、jar パッケージを作成します。

    ```bash
    mvn clean package
    ```

    このコマンドにより、`kafka-streaming-1.0-SNAPSHOT.jar` というファイルを含む `target` という名前のディレクトリが作成されます。

3. 次のコマンドを使用して、HDInsight クラスターに `kafka-streaming-1.0-SNAPSHOT.jar` ファイルをコピーします。
   
    ```bash
    scp ./target/kafka-streaming-1.0-SNAPSHOT.jar SSHUSER@CLUSTERNAME-ssh.azurehdinsight.net:kafka-streaming.jar
    ```
   
    **SSHUSER** は、クラスターの SSH ユーザーに置き換えます。また、**CLUSTERNAME** はクラスターの名前に置き換えます。 メッセージが表示されたら、SSH ユーザー アカウントのパスワードを入力します。 HDInsight での `scp` の使用の詳細については、「[HDInsight で SSH を使用する](../hdinsight-hadoop-linux-use-ssh-unix.md)」を参照してください。

## <a name="run-the-example"></a>例を実行する

1. クラスターとの SSH 接続を開くには、次のコマンドを使用します。

    ```bash
    ssh SSHUSER@CLUSTERNAME-ssh.azurehdinsight.net
    ```

    **SSHUSER** は、クラスターの SSH ユーザーに置き換えます。また、**CLUSTERNAME** はクラスターの名前に置き換えます。 メッセージが表示されたら、SSH ユーザー アカウントのパスワードを入力します。 HDInsight での `scp` の使用の詳細については、「[HDInsight で SSH を使用する](../hdinsight-hadoop-linux-use-ssh-unix.md)」を参照してください。

4. この例によって使用される Kafka トピックを作成するには、次のコマンドを使用します。

    ```bash
    sudo apt -y install jq

    CLUSTERNAME='your cluster name'

    export KAFKAZKHOSTS=`curl -sS -u admin -G https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME/services/ZOOKEEPER/components/ZOOKEEPER_SERVER | jq -r '["\(.host_components[].HostRoles.host_name):2181"] | join(",")' | cut -d',' -f1,2`

    export KAFKABROKERS=`curl -sS -u admin -G https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME/services/KAFKA/components/KAFKA_BROKER | jq -r '["\(.host_components[].HostRoles.host_name):9092"] | join(",")' | cut -d',' -f1,2`

    /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --create --replication-factor 3 --partitions 8 --topic test --zookeeper $KAFKAZKHOSTS

    /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --create --replication-factor 3 --partitions 8 --topic wordcounts --zookeeper $KAFKAZKHOSTS
    ```

    __your cluster name__ を、使用する HDInsight クラスターの名前に置き換えます。 メッセージが表示されたら、HDInsight クラスターのログイン アカウントのパスワードを入力します。

    > [!NOTE]
    > クラスター ログインが `admin` の既定値と異なる場合、前のコマンドの `admin` 値をクラスター ログイン名に置き換えます。

5. この例を実行するには、次の 3 つのことを行う必要があります。

    * `kafka-streaming.jar` に含まれている Streams ソリューションを起動します。
    * `test` トピックに書き込むプロデューサーを起動します。
    * `wordcounts` トピックに書き込まれた出力を表示できるように、コンシューマーを起動します

    > [!NOTE]
    > Kafka ブローカーの構成ファイルで `auto.create.topics.enable` プロパティが `true` に設定されていることを確認してください。 詳細な Kafka ブローカーの構成ファイルでこのプロパティを表示し、変更するには、Ambari Web UI を使用します。 それ以外の場合は、次のコマンドを使用してこのサンプルを実行する前に中間トピック `RekeyedIntermediateTopic` を作成する必要があります。
    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --create --replication-factor 3 --partitions 8 --topic RekeyedIntermediateTopic  --zookeeper $KAFKAZKHOSTS
    ```
    
    これらの操作は、3 つの SSH セッションを開いて実現できました。 ただし、次に各 SSH セッションでこのセクションの手順 4 を実行して、それぞれに `$KAFKABROKERS` と `$KAFKAZKHOSTS` を設定する必要があります。 より簡単なソリューションは、`tmux` ユーティリティを使用することです。これは、現在の SSH 表示を複数のセクションに分割できます。 `tmux` を使用してストリーム、プロデューサー、およびコンシューマーを起動するには、次のコマンドを使用します。

    ```bash
    tmux new-session '/usr/hdp/current/kafka-broker/bin/kafka-console-consumer.sh --bootstrap-server $KAFKABROKERS --topic wordcounts --formatter kafka.tools.DefaultMessageFormatter --property print.key=true --property key.deserializer=org.apache.kafka.common.serialization.StringDeserializer --property value.deserializer=org.apache.kafka.common.serialization.LongDeserializer' \; split-window -h 'java -jar kafka-streaming.jar $KAFKABROKERS $KAFKAZKHOSTS' \; split-window -v '/usr/hdp/current/kafka-broker/bin/kafka-console-producer.sh --broker-list $KAFKABROKERS --topic test' \; attach
    ```

    このコマンドは、SSH 表示を 3 つのセクションに分割します。

    * 左側のセクションは、`wordcounts` トピックからメッセージを読み取るコンソール コンシューマーを実行します。`/usr/hdp/current/kafka-broker/bin/kafka-console-consumer.sh --bootstrap-server $KAFKABROKERS --topic wordcounts --formatter kafka.tools.DefaultMessageFormatter --property print.key=true --property key.deserializer=org.apache.kafka.common.serialization.StringDeserializer --property value.deserializer=org.apache.kafka.common.serialization.LongDeserializer`

        > [!NOTE]
        > `--property` パラメーターはコンソール コンシューマーに、カウント (値) と共にキー (ワード) を出力するように指示します。 このパラメーターは、Kafka からこれらの値を読み取るときに使用するデシリアライザーも構成します。

    * 右上のセクションでは、Streams API ソリューションを実行します。`java -jar kafka-streaming.jar $KAFKABROKERS $KAFKAZKHOSTS`

    * 右下のセクションでは、コンソール プロデューサーを実行し、ユーザーが `test` トピックに送信するメッセージを入力するまで待機します。`/usr/hdp/current/kafka-broker/bin/kafka-console-producer.sh --broker-list $KAFKABROKERS --topic test`
 
6. `tmux` コマンドで表示を分割した後、カーソルは右下のセクションにあります。 文の入力を開始します。 各文の後に、左側のウィンドウが更新され、一意のワードの数が表示されます。 出力は次のテキストのようになります。
   
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
    > 単語が現れるたびにカウントが増加します。

7. __Ctrl + C__ キーを使用してプロデューサーを終了します。 引き続き __Ctrl + C__ キーを使用して、アプリケーションとコンシューマーを終了します。

## <a name="next-steps"></a>次の手順

このドキュメントでは、HDInsight 上の Kafka で Kafka Streams API を使用する方法について説明しました。 次の各ドキュメントを参考に、Kafka の使用の詳細を確認してください。

* [Kafka ログの分析](apache-kafka-log-analytics-operations-management.md)
* [Kafka クラスター間でデータをレプリケートする](apache-kafka-mirroring.md)
* [HDInsight における Kafka Producer API と Consumer API](apache-kafka-producer-consumer-api.md)
* [HDInsight 上で Kafka を用いて Apache Spark ストリーミング (DStream) を使用する](../hdinsight-apache-spark-with-kafka.md)
* [HDInsight 上で Kafka を用いて Apache Spark 構造化ストリーミングを使用する](../hdinsight-apache-kafka-spark-structured-streaming.md)
* [Apache Spark 構造化ストリーミングを使って HDInsight 上の Kafka から Cosmos DB にデータを移動する](../apache-kafka-spark-structured-streaming-cosmosdb.md)
* [HDInsight での Kafka に Apache Storm を使用する](../hdinsight-apache-storm-with-kafka.md)
* [Azure Virtual Network 経由で Kafka に接続する](apache-kafka-connect-vpn-gateway.md)
