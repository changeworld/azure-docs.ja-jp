---
title: Apache Kafka Producer および Consumer API の使用 - Azure HDInsight | Microsoft Docs
description: HDInsight 上の Kafka で Apache Kafka Producer および Consumer API を使用する方法を説明します。 これらの API を使用すると、Apache Kafka に対して書き込みおよび読み取りを行うアプリケーションを開発できます。
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
ms.openlocfilehash: 01592401c4c88adeed49b11df4e7963e27b1bcee
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/16/2018
---
# <a name="apache-kafka-producer-and-consumer-apis"></a>Apache Kafka Producer および Consumer API

HDInsight 上の Kafka で Kafka Producer および Consumer API を使用するアプリケーションを作成する方法を説明します。

API のドキュメントについては、「[Producer API](https://kafka.apache.org/documentation/#producerapi)」と「[Consumer API](https://kafka.apache.org/documentation/#consumerapi)」を参照してください。

## <a name="set-up-your-development-environment"></a>開発環境を設定する

開発環境に、次のコンポーネントがインストールされている必要があります。

* [Java JDK 8](http://www.oracle.com/technetwork/java/javase/downloads/index.html) または同等の OpenJDK など。

* [Apache Maven](http://maven.apache.org/)

* SSH クライアントと `scp` コマンド。 詳細については、[HDInsight での SSH の使用](../hdinsight-hadoop-linux-use-ssh-unix.md)に関するドキュメントを参照してください。

## <a name="set-up-your-deployment-environment"></a>デプロイ環境を設定する

この例では、HDInsight 3.6 上の Kafka が必要です。 HDInsight クラスターに Kafka を作成する方法については、[HDInsight での Kafka の開始](apache-kafka-get-started.md)に関するドキュメントをご覧ください。

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

2. この例によって使用される Kafka トピックを作成するには、次のコマンドを使用します。

    ```bash
    sudo apt -y install jq

    CLUSTERNAME='your cluster name'

    export KAFKAZKHOSTS=`curl -sS -u admin -G https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME/services/ZOOKEEPER/components/ZOOKEEPER_SERVER | jq -r '["\(.host_components[].HostRoles.host_name):2181"] | join(",")' | cut -d',' -f1,2`

    export KAFKABROKERS=`curl -sS -u admin -G https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME/services/KAFKA/components/KAFKA_BROKER | jq -r '["\(.host_components[].HostRoles.host_name):9092"] | join(",")' | cut -d',' -f1,2`

    /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --create --replication-factor 3 --partitions 8 --topic test --zookeeper $KAFKAZKHOSTS
    ```

    __your cluster name__ を、使用する HDInsight クラスターの名前に置き換えます。 メッセージが表示されたら、HDInsight クラスターのログイン アカウントのパスワードを入力します。

    > [!NOTE]
    > クラスター ログインが `admin` の既定値と異なる場合、前のコマンドの `admin` 値をクラスター ログイン名に置き換えます。

3. プロデューサーを実行し、トピックにデータを書き込むには、次のコマンドを使用します。

    ```bash
    java -jar kafka-producer-consumer.jar producer $KAFKABROKERS
    ```

4. プロデューサーが完了したら、次のコマンドを使用してトピックから読み取ります。
   
    ```bash
    java -jar kafka-producer-consumer.jar consumer $KAFKABROKERS
    ```
   
    読み取られたレコードが、レコードの件数とともに表示されます。

5. __Ctrl+C__ キーを使用してコンシューマーを終了します。

### <a name="multiple-consumers"></a>複数のコンシューマー

Kafka コンシューマーは、レコードを読み取る際に、コンシューマー グループを使用します。 複数のコンシューマーで同じグループを使用すると、トピックからの読み取りの負荷が分散されます。 グループ内の各コンシューマーは、レコードの一部を受け取ります。

コンシューマー アプリケーションは、グループ ID として使用されるパラメーターを受け取ります。 たとえば、次のコマンドは `mygroup` のグループ ID を使用して、コンシューマーを起動します。
   
```bash
java -jar kafka-producer-consumer.jar consumer $KAFKABROKERS mygroup
```

このプロセスを実際に確認するには、次の手順に従います。

1. 「[例を実行する](#run)」セクションの手順 1. および 2. を繰り返して、新しい SSH セッションを開きます。

2. 両方の SSH セッションで、次のコマンドを入力します。

    ```bash
    java -jar kafka-producer-consumer.jar consumer $KAFKABROKERS mygroup
    ```
    
    > [!IMPORTANT]
    > 両方のコマンドが同時に実行されるように、両方のコマンドを同時に入力します。

    メッセージ読み取りの数が、コンシューマーが 1 つしかない前のセクションと同じでないことに注意してください。 代わりに、メッセージ読み取りは、インスタンス間で分割されます。

同じグループ内のクライアントによる消費は、トピックのパーティションを介して処理されます。 先ほど作成した `test` トピックには、8 つのパーティションがあります。 8 個の SSH セッションを開き、すべてのセッションで 1 つのコンシューマーを起動した場合、各コンシューマーはトピックの 1 つのパーティションからレコードを読み取ります。

> [!IMPORTANT]
> コンシューマー グループに、パーティションの数よりも多いコンシューマー インスタンスを含めることはできません。 この例では、トピック内のパーティション数が 8 であるため、1 つのコンシューマー グループに最大 8 個のコンシューマーを含めることができます。 または、それぞれコンシューマーが 8 個以下の複数のコンシューマー グループを存在させることができます。

Kafka に格納されたレコードは、受信した順番でパーティション内に格納されます。 *パーティション内*のレコードの順次配信順を実現するには、コンシューマー インスタンスの数がパーティションの数と同じコンシューマー グループを作成します。 *トピック内*のレコードの順次配信を実現するには、コンシューマー インスタンスが 1 つのみのコンシューマー グループを作成します。

## <a name="next-steps"></a>次の手順

このドキュメントでは、HDInsight 上の Kafka で Kafka Producer および Consumer API を使用する方法について説明しました。 次の各ドキュメントを参考に、Kafka の使用の詳細を確認してください。

* [Kafka ログの分析](apache-kafka-log-analytics-operations-management.md)
* [Kafka クラスター間でデータをレプリケートする](apache-kafka-mirroring.md)
* [HDInsight における Kafka Streams API](apache-kafka-streams-api.md)
* [HDInsight 上で Kafka を用いて Apache Spark ストリーミング (DStream) を使用する](../hdinsight-apache-spark-with-kafka.md)
* [HDInsight 上で Kafka を用いて Apache Spark 構造化ストリーミングを使用する](../hdinsight-apache-kafka-spark-structured-streaming.md)
* [Apache Spark 構造化ストリーミングを使って HDInsight 上の Kafka から Cosmos DB にデータを移動する](../apache-kafka-spark-structured-streaming-cosmosdb.md)
* [HDInsight での Kafka に Apache Storm を使用する](../hdinsight-apache-storm-with-kafka.md)
* [Azure Virtual Network 経由で Kafka に接続する](apache-kafka-connect-vpn-gateway.md)