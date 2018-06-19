---
title: Kafka エコシステム用 Azure Event Hubs へのストリーム配信 | Microsoft Docs
description: Kafka プロトコルおよび API を使用して Event Hubs にストリーム配信します。
services: event-hubs
documentationcenter: ''
author: basilhariri
manager: timlt
ms.service: event-hubs
ms.devlang: na
ms.topic: quickstart
ms.custom: mvc
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/08/2018
ms.author: bahariri
ms.openlocfilehash: 8ef6240d19ce1ac1b891c95ce525a8bd211a2900
ms.sourcegitcommit: 6f6d073930203ec977f5c283358a19a2f39872af
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/11/2018
ms.locfileid: "35297225"
---
# <a name="stream-into-event-hubs-for-the-kafka-ecosystem"></a>Kafka エコシステム用 Event Hubs へのストリーム配信

> [!NOTE]
> このサンプルは [GitHub](https://github.com/Azure/azure-event-hubs) で入手できます。

このクイック スタートでは、プロトコル クライアントを変更したり、独自のクラスターを実行したりせずに、Kafka 対応 Event Hubs にストリーム配信する方法を示します。 プロデューサーとコンシューマーを使用して、アプリケーションの構成変更だけで Kafka 対応 Event Hubs と通信する方法を説明します。 Kafka エコシステム用 Azure Event Hubs では、[Apache Kafka バージョン 1.0](https://kafka.apache.org/10/documentation.html) がサポートされています。

## <a name="prerequisites"></a>前提条件

このクイック スタートを完了するには、次の前提条件を満たしている必要があります。

* Azure サブスクリプション。 お持ちでない場合は、開始する前に[無料アカウント](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)を作成してください。
* [Java Development Kit (JDK) 1.7 以降](http://www.oracle.com/technetwork/java/javase/downloads/index.html)
* Maven バイナリ アーカイブの[ダウンロード](http://maven.apache.org/download.cgi)と[インストール](http://maven.apache.org/install.html)
* [Git](https://www.git-scm.com/)
* [Kafka 対応 Event Hubs 名前空間](event-hubs-create.md)

## <a name="send-and-receive-messages-with-kafka-in-event-hubs"></a>Event Hubs で Kafka を使用してメッセージを送受信する

1. [Azure Event Hubs リポジトリ](https://github.com/Azure/azure-event-hubs)を複製します。

2. `azure-event-hubs/samples/kafka/quickstart/producer` に移動します。

3. `src/main/resources/producer.config` で次のようにプロデューサーの構成の詳細を更新します。

    ```xml
    bootstrap.servers={YOUR.EVENTHUBS.FQDN}:9093
    security.protocol=SASL_SSL
    sasl.mechanism=PLAIN
    sasl.jaas.config=org.apache.kafka.common.security.plain.PlainLoginModule required username="$ConnectionString" password="{YOUR.EVENTHUBS.CONNECTION.STRING}";
    ```
4. プロデューサー コードを実行し、Kafka 対応 Event Hubs にストリーム配信します。
   
    ```shell
    mvn clean package
    mvn exec:java -Dexec.mainClass="TestProducer"                                    
    ```
5. `azure-event-hubs/samples/kafka/quickstart/consumer` に移動します。

6. `src/main/resources/consumer.config` で次のようにコンシューマーの構成の詳細を更新します。
   
    ```xml
    bootstrap.servers={YOUR.EVENTHUBS.FQDN}:9093
    security.protocol=SASL_SSL
    sasl.mechanism=PLAIN
    sasl.jaas.config=org.apache.kafka.common.security.plain.PlainLoginModule required username="$ConnectionString" password="{YOUR.EVENTHUBS.CONNECTION.STRING}";
    ```

7. Kafka クライアントを使用して、Kafka 対応 Event Hubs からコンシューマー コードとプロセスを実行します。

    ```java
    mvn clean package
    mvn exec:java -Dexec.mainClass="TestConsumer"                                    
    ```

Event Hubs Kafka クラスターにイベントがある場合は、コンシューマーからそれらの受信を開始します。

## <a name="next-steps"></a>次の手順

* [Event Hubs について確認する](event-hubs-what-is-event-hubs.md)
* [Kafka エコシステム用 Event Hubs について確認する](event-hubs-for-kafka-ecosystem-overview.md)
* [MirrorMaker](https://cwiki.apache.org/confluence/pages/viewpage.action?pageId=27846330) を使用して[オンプレミスの Kafka からクラウドの Kafka 対応 Event Hubs にイベントをストリーム配信する。](event-hubs-kafka-mirror-maker-tutorial.md)
* [Apache Flink](event-hubs-kafka-flink-tutorial.md) または [Akka Streams](event-hubs-kafka-akka-streams-tutorial.md) を使用して Kafka 対応 Event Hubs にストリーム配信する方法について確認する。
