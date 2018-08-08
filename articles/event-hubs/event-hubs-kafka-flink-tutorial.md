---
title: Apache Kafka 用の Azure Event Hubs での Apache Flink の使用 | Microsoft Docs
description: Kafka 対応 Event Hubs への Apache Flink の接続
services: event-hubs
documentationcenter: ''
author: basilhariri
manager: timlt
ms.service: event-hubs
ms.topic: article
ms.custom: mvc
ms.date: 06/06/2018
ms.author: bahariri
ms.openlocfilehash: ce1665c3cfd58d0d5aa8e253b5db317505b1959e
ms.sourcegitcommit: 068fc623c1bb7fb767919c4882280cad8bc33e3a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/27/2018
ms.locfileid: "39284579"
---
# <a name="use-apache-flink-with-azure-event-hubs-for-apache-kafka"></a>Apache Kafka 用の Azure Event Hubs での Apache Flink の使用

Apache Kafka を使用する主な利点の 1 つは、そこから接続できるフレームワークのエコシステムにあります。 Kafka 対応 Event Hubs では、Kafka の柔軟性と、Azure エコシステムのスケーラビリティ、一貫性、およびサポートが組み合わされます。

このチュートリアルでは、プロトコル クライアントを変更したり、独自のクラスターを実行したりせずに、Apache Flink を Kafka 対応 Event Hubs に接続する方法を示します。 Azure Event Hubs では、[Apache Kafka バージョン 1.0](https://kafka.apache.org/10/documentation.html) がサポートされています。

## <a name="prerequisites"></a>前提条件

このチュートリアルを完了するには、次の前提条件を満たしている必要があります。

* Azure サブスクリプション。 お持ちでない場合は、開始する前に[無料アカウント](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)を作成してください。
* [Java Development Kit (JDK) 1.7 以降](http://www.oracle.com/technetwork/java/javase/downloads/index.html)
    * Ubuntu で `apt-get install default-jdk` を実行して JDK をインストールします。
    * 必ず、JDK のインストール先フォルダーを指すように JAVA_HOME 環境変数を設定してください。
* Maven バイナリ アーカイブの[ダウンロード](http://maven.apache.org/download.cgi)と[インストール](http://maven.apache.org/install.html)
    * Ubuntu で `apt-get install maven` を実行して Maven をインストールします。
* [Git](https://www.git-scm.com/downloads)
    * Ubuntu で `sudo apt-get install git` を実行して Git をインストールします。

## <a name="create-an-event-hubs-namespace"></a>Event Hubs 名前空間を作成します

Event Hubs サービスとの間で送受信を行うには、Event Hubs 名前空間が必要です。 Event Hubs Kafka エンドポイントを取得する方法については、[Kafka 対応 Event Hubs の作成](event-hubs-create-kafka-enabled.md)に関するページをご覧ください。 後で使うので、イベント ハブの接続文字列をコピーしておきます。

## <a name="clone-the-example-project"></a>サンプル プロジェクトを複製する

Kafka が有効なイベント ハブの接続文字列を入手したので、Azure Event Hubs リポジトリを複製し、`flink` サブフォルダーに移動します。

```shell
git clone https://github.com/Azure/azure-event-hubs.git
cd azure-event-hubs/samples/kafka/flink
```

## <a name="flink-producer"></a>Flink プロデューサー

提供された Flink プロデューサーの例を使用して、Event Hubs サービスにメッセージを送信します。

### <a name="provide-an-event-hubs-kafka-endpoint"></a>Event Hubs Kafka エンドポイントを指定する

#### <a name="producerconfig"></a>producer.config

`producer/src/main/resources/producer.config` の `bootstrap.servers` 値と `sasl.jaas.config` 値を更新し、正しい認証を使用してプロデューサーを Event Hubs Kafka エンドポイントに転送します。

```xml
bootstrap.servers={YOUR.EVENTHUBS.FQDN}:9093
client.id=FlinkExampleProducer
sasl.mechanism=PLAIN
security.protocol=SASL_SSL
sasl.jaas.config=org.apache.kafka.common.security.plain.PlainLoginModule required \
   username="$ConnectionString" \
   password="{YOUR.EVENTHUBS.CONNECTION.STRING}";
```

### <a name="run-producer-from-the-command-line"></a>コマンド ラインからプロデューサーを実行する

コマンドラインからプロデューサーを実行するには、JAR を生成し、Maven 内から実行します (または、Maven を使用して JAR を生成し、必要な Kafka JAR をクラスパスに追加することによって、Java 内で実行します)。

```shell
mvn clean package
mvn exec:java -Dexec.mainClass="FlinkTestProducer"
```

プロデューサーは、トピック `test` にある Kafka 対応 Event Hub に対してイベントの送信を開始し、stdout にイベントを出力します。

## <a name="flink-consumer"></a>Flink コンシューマー

提供されたコンシューマーの例を使用して、Kafka 対応 Event Hubs からメッセージを受信します。

### <a name="provide-an-event-hubs-kafka-endpoint"></a>Event Hubs Kafka エンドポイントを指定する

#### <a name="consumerconfig"></a>consumer.config

`consumer/src/main/resources/consumer.config` の `bootstrap.servers` 値と `sasl.jaas.config` 値を更新し、正しい認証を使用してコンシューマーを Event Hubs Kafka エンドポイントに転送します。

```xml
bootstrap.servers={YOUR.EVENTHUBS.FQDN}:9093
group.id=FlinkExampleConsumer
sasl.mechanism=PLAIN
security.protocol=SASL_SSL
sasl.jaas.config=org.apache.kafka.common.security.plain.PlainLoginModule required \
   username="$ConnectionString" \
   password="{YOUR.EVENTHUBS.CONNECTION.STRING}";
```

### <a name="run-consumer-from-the-command-line"></a>コマンド ラインからコンシューマーを実行する

コマンドラインからコンシューマーを実行するには、JAR を生成し、Maven 内から実行します (または、Maven を使用して JAR を生成し、必要な Kafka JAR をクラスパスに追加することによって、Java 内で実行します)。

```shell
mvn clean package
mvn exec:java -Dexec.mainClass="FlinkTestConsumer"
```

Kafka 対応 Event Hubs にイベントがある場合 (たとえば、プロデューサーも実行されている場合)、コンシューマーはトピック `test` からのイベントの受信を開始します。

Flink を Kafka に接続する方法についての詳細は、[Flink の Kafka コネクタ ガイド](https://ci.apache.org/projects/flink/flink-docs-stable/dev/connectors/kafka.html)を調べてください。

## <a name="next-steps"></a>次の手順

* [Event Hubs について確認する](event-hubs-what-is-event-hubs.md)
* [Kafka 用 Event Hubs について確認する](event-hubs-for-kafka-ecosystem-overview.md)
* [MirrorMaker](https://cwiki.apache.org/confluence/pages/viewpage.action?pageId=27846330) を使用して[オンプレミスの Kafka からクラウドの Kafka 対応 Event Hubs にイベントをストリーム配信する。](event-hubs-kafka-mirror-maker-tutorial.md)
* [ネイティブの Kafka アプリケーション](event-hubs-quickstart-kafka-enabled-event-hubs.md)または [Akka Streams](event-hubs-kafka-akka-streams-tutorial.md) を使用して Kafka 対応 Event Hubs にストリーム配信する方法について確認する。
