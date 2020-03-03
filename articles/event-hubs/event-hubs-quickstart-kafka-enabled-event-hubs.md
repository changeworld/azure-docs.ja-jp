---
title: クイック スタート:Kafka プロトコルを使用した Azure Event Hubs によるデータ ストリーミング
description: クイック スタート:この記事では、Kafka のプロトコルおよび API を使用して、Azure Event Hubs にストリーム配信する方法に関する情報を示します。
services: event-hubs
author: ShubhaVijayasarathy
ms.author: shvija
ms.service: event-hubs
ms.topic: quickstart
ms.custom: seodec18
ms.date: 02/12/2020
ms.openlocfilehash: 18976a29a716a0e5a627747d98edc0d3e1bf71e9
ms.sourcegitcommit: 7f929a025ba0b26bf64a367eb6b1ada4042e72ed
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/25/2020
ms.locfileid: "77587143"
---
# <a name="quickstart-data-streaming-with-event-hubs-using-the-kafka-protocol"></a>クイック スタート:Kafka プロトコルを使用した Event Hubs によるデータ ストリーミング
このクイック スタートでは、プロトコル クライアントを変更したり、独自のクラスターを実行したりせずに、Kafka 対応 Event Hubs にストリーム配信する方法を示します。 プロデューサーとコンシューマーを使用して、アプリケーションの構成変更だけで Kafka 対応 Event Hubs と通信する方法を説明します。 Azure Event Hubs では、[Apache Kafka バージョン 1.0](https://kafka.apache.org/10/documentation.html) がサポートされています。

> [!NOTE]
> このサンプルは [GitHub](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/quickstart/java) で入手できます。

## <a name="prerequisites"></a>前提条件

このクイック スタートを完了するには、次の前提条件を満たしている必要があります。

* [Apache Kafka 用の Event Hubs](event-hubs-for-kafka-ecosystem-overview.md) に関する記事を読む。
* Azure サブスクリプション。 お持ちでない場合は、開始する前に[無料アカウント](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)を作成してください。
* [Java Development Kit (JDK) 1.7 以降](https://aka.ms/azure-jdks)
* Maven バイナリ アーカイブの[ダウンロード](https://maven.apache.org/download.cgi)と[インストール](https://maven.apache.org/install.html)
* [Git](https://www.git-scm.com/)


## <a name="create-a-kafka-enabled-event-hubs-namespace"></a>Kafka 対応 Event Hubs 名前空間の作成
**Standard** レベルの Event Hubs 名前空間を作成すると、名前空間の Kafka エンドポイントが自動的に有効になります。 Kafka プロトコルを使用するアプリケーションから Standard レベルの Event Hubs にイベントをストリーム配信できます。 **Standard** レベルの Event Hubs 名前空間を作成するには、[Azure portal を使用したイベント ハブの作成](event-hubs-create.md)に関するページの手順に従います。 

> [!NOTE]
> Kafka 用の Event Hubs は、**Standard** および **Dedicated** レベルでのみ使用できます。 **Basic** レベルでは、Event Hubs 上の Kafka はサポートされません。

## <a name="send-and-receive-messages-with-kafka-in-event-hubs"></a>Event Hubs で Kafka を使用してメッセージを送受信する

1. [Kafka 用 Azure Event Hubs リポジトリ](https://github.com/Azure/azure-event-hubs-for-kafka)を複製します。

2. `azure-event-hubs-for-kafka/quickstart/java/producer` に移動します。

3. `src/main/resources/producer.config` で次のようにプロデューサーの構成の詳細を更新します。

    **SSL:**

    ```xml
    bootstrap.servers=NAMESPACENAME.servicebus.windows.net:9093
    security.protocol=SASL_SSL
    sasl.mechanism=PLAIN
    sasl.jaas.config=org.apache.kafka.common.security.plain.PlainLoginModule required username="$ConnectionString" password="{YOUR.EVENTHUBS.CONNECTION.STRING}";
    ```
    **OAuth:**

    ```xml
    bootstrap.servers=NAMESPACENAME.servicebus.windows.net:9093
    security.protocol=SASL_SSL
    sasl.mechanism=OAUTHBEARER
    sasl.jaas.config=org.apache.kafka.common.security.oauthbearer.OAuthBearerLoginModule required;
    sasl.login.callback.handler.class=CustomAuthenticateCallbackHandler;
    ```    

    サンプル ハンドラー クラス CustomAuthenticateCallbackHandler のソース コードは、GitHub の[こちら](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/oauth/java/appsecret/producer/src/main/java)にあります。
4. プロデューサー コードを実行し、Kafka 対応 Event Hubs にイベントをストリーム配信します。
   
    ```shell
    mvn clean package
    mvn exec:java -Dexec.mainClass="TestProducer"                                    
    ```
    
5. `azure-event-hubs-for-kafka/quickstart/java/consumer` に移動します。

6. `src/main/resources/consumer.config` で次のようにコンシューマーの構成の詳細を更新します。
   
    **SSL:**

    ```xml
    bootstrap.servers=NAMESPACENAME.servicebus.windows.net:9093
    security.protocol=SASL_SSL
    sasl.mechanism=PLAIN
    sasl.jaas.config=org.apache.kafka.common.security.plain.PlainLoginModule required username="$ConnectionString" password="{YOUR.EVENTHUBS.CONNECTION.STRING}";
    ```

    **OAuth:**

    ```xml
    bootstrap.servers=NAMESPACENAME.servicebus.windows.net:9093
    security.protocol=SASL_SSL
    sasl.mechanism=OAUTHBEARER
    sasl.jaas.config=org.apache.kafka.common.security.oauthbearer.OAuthBearerLoginModule required;
    sasl.login.callback.handler.class=CustomAuthenticateCallbackHandler;
    ``` 

    サンプル ハンドラー クラス CustomAuthenticateCallbackHandler のソース コードは、GitHub の[こちら](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/oauth/java/appsecret/consumer/src/main/java)にあります。

    Kafka 用 Event Hubs の OAuth サンプルはすべて[こちら](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/oauth)にあります。
7. Kafka クライアントを使用して、Kafka 対応 Event Hubs からコンシューマー コードとプロセスを実行します。

    ```java
    mvn clean package
    mvn exec:java -Dexec.mainClass="TestConsumer"                                    
    ```

Event Hubs Kafka クラスターにイベントがある場合は、コンシューマーからそれらの受信を開始します。

## <a name="next-steps"></a>次のステップ
この記事では、プロトコル クライアントを変更したり、独自のクラスターを実行したりせずに、Kafka 対応 Event Hubs にストリーム配信する方法を紹介しました。 詳細については、以下の記事とサンプルを参照してください。

- [Kafka 用 Event Hubs について確認する](event-hubs-for-kafka-ecosystem-overview.md)
- [GitHub の Kafka 用 Event Hubs のクイックスタート](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/quickstart)
- [GitHub の Kafka 用 Event Hubs のチュートリアル](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials)
- [MirrorMaker](https://cwiki.apache.org/confluence/pages/viewpage.action?pageId=27846330) を使用して[オンプレミスの Kafka からクラウドの Kafka 対応 Event Hubs にイベントをストリーム配信する。](event-hubs-kafka-mirror-maker-tutorial.md)
- [Apache Flink](event-hubs-kafka-flink-tutorial.md) または [Akka Streams](event-hubs-kafka-akka-streams-tutorial.md) を使用して Kafka 対応 Event Hubs にストリーム配信する方法について確認する
