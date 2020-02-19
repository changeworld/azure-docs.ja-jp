---
title: Java を使用して Azure Event Hubs との間でイベントを送受信する (最新バージョン)
description: この記事では、最新の azure-messaging-eventhubs パッケージを使用して、Azure Event Hubs との間でイベントを送受信する Java アプリケーションを作成する方法について説明します。
services: event-hubs
author: spelluru
ms.service: event-hubs
ms.workload: core
ms.topic: quickstart
ms.date: 02/11/2020
ms.author: spelluru
ms.openlocfilehash: 4ebb52aa3e8d4ccfee6b36fb60c7f041df08a69a
ms.sourcegitcommit: 76bc196464334a99510e33d836669d95d7f57643
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/12/2020
ms.locfileid: "77163026"
---
# <a name="use-java-to-send-events-to-or-receive-events-from-azure-event-hubs-azure-messaging-eventhubs"></a>Java を使用して Azure Event Hubs との間でイベントを送受信する (azure-messaging-eventhubs)
このクイックスタートでは、**azure-messaging-eventhubs** Java パッケージを使用して、イベント ハブとの間でイベントを送受信する方法について説明します。

> [!IMPORTANT]
> このクイックスタートでは、新しい **azure-messaging-eventhubs** ライブラリを使用します。 以前の **azure-eventhubs** および **azure-eventhubs-eph** パッケージを使用するクイックスタートについては、[azure-eventhubs と azure-eventhubs-eph を使用したイベントの送受信](event-hubs-java-get-started-send.md)に関するページを参照してください。 


## <a name="prerequisites"></a>前提条件
Azure Event Hubs を初めて使用する場合は、このクイックスタートを行う前に[イベント ハブの概要](event-hubs-about.md)を参照してください。 

このクイック スタートを完了するには、次の前提条件を用意しておく必要があります。

- **Microsoft Azure サブスクリプション**。 Azure Event Hubs を含む Azure サービスを使用するには、サブスクリプションが必要です。  既存の Microsoft Azure アカウントをお持ちでない場合は、[アカウントを作成する](https://azure.microsoft.com)際に、[無料試用版](https://azure.microsoft.com/free/)にサインアップするか、MSDN サブスクライバー特典を利用できます。
- Java 開発環境。 このクイックスタートでは [Eclipse](https://www.eclipse.org/) を使用します。 Java Development Kit (JDK) バージョン 8 以上が必要です。 
- **Event Hubs 名前空間とイベント ハブを作成する**。 最初の手順では、[Azure Portal](https://portal.azure.com) を使用して Event Hubs 型の名前空間を作成し、アプリケーションがイベント ハブと通信するために必要な管理資格情報を取得します。 名前空間とイベント ハブを作成するには、[こちらの記事](event-hubs-create.md)の手順に従います。 その後、次の記事の手順に従って、**Event Hubs 名前空間用の接続文字列**を取得します: [接続文字列を取得する](event-hubs-get-connection-string.md#get-connection-string-from-the-portal)。 この接続文字列は、このクイックスタートの後の手順で必要になります。

## <a name="send-events"></a>送信イベント 
このセクションでは、イベント ハブにイベントを送信する Java アプリケーションの作成方法を説明します。 

### <a name="add-reference-to-azure-event-hubs-library"></a>Azure Event Hubs ライブラリへの参照を追加する

[Maven Central Repository](https://search.maven.org/search?q=a:azure-messaging-eventhubs) の Maven プロジェクトで Event Hub 用の Java クライアント ライブラリを使用できます。 Maven プロジェクト ファイル内で次の依存関係宣言を使用して、このライブラリを参照できます。

```xml
<dependency>
    <groupId>com.azure</groupId>
    <artifactId>azure-messaging-eventhubs</artifactId>
    <version>5.0.1</version>
</dependency>
```

### <a name="write-code-to-send-messages-to-the-event-hub"></a>イベント ハブにメッセージを送信するコードの記述

次のサンプルでは、最初に、好みの Java 開発環境でコンソール/シェル アプリケーション用の新しい Maven プロジェクトを作成します。 `SimpleSend` という名前のクラスを追加し、このクラスに次のコードを追加します。

```java
import com.azure.messaging.eventhubs.*;
import static java.nio.charset.StandardCharsets.UTF_8;

public class Sender {
    public static void main(String[] args) {
    }
}
```

### <a name="connection-string-and-event-hub"></a>接続文字列とイベント ハブ
このコードでは、Event Hubs 名前空間への接続文字列と、イベント ハブの名前を使用して、Event Hubs クライアントを構築します。 

```java
String connectionString = "<CONNECTION STRING to EVENT HUBS NAMESPACE>";
String eventHubName = "<EVENT HUB NAME>";
```

### <a name="create-an-event-hubs-producer-client"></a>Event Hubs プロデューサー クライアントを作成する 
このコードでは、イベントの生成とイベント ハブへの送信に使用されるプロデューサー クライアント オブジェクトを作成します。 

```java
EventHubProducerClient producer = new EventHubClientBuilder()
    .connectionString(connectionString, eventHubName)
    .buildProducerClient();
```

### <a name="prepare-a-batch-of-events"></a>イベントのバッチを準備する
このコードでは、イベントのバッチを準備します。 

```java
EventDataBatch batch = producer.createBatch();
batch.tryAdd(new EventData("First event"));
batch.tryAdd(new EventData("Second event"));
batch.tryAdd(new EventData("Third event"));
batch.tryAdd(new EventData("Fourth event"));
batch.tryAdd(new EventData("Fifth event"));
```

### <a name="send-the-batch-of-events-to-the-event-hub"></a>イベントのバッチをイベント ハブに送信する
このコードは、前の手順で準備したイベントのバッチをイベント ハブに送信します。 次のコードでは、送信操作がブロックされます。 

```java
producer.send(batch);
```

### <a name="close-and-cleanup"></a>閉じてクリーンアップする
このコードでは、プロデューサーを閉じます。 

```java
producer.close();
```
### <a name="complete-code-to-send-events"></a>イベントを送信する完全なコード
イベント ハブにイベントを送信するための完全なコードを次に示します。 

```java
import com.azure.messaging.eventhubs.*;

public class Sender {
    public static void main(String[] args) {
        final String connectionString = "EVENT HUBS NAMESPACE CONNECTION STRING";
        final String eventHubName = "EVENT HUB NAME";

        // create a producer using the namespace connection string and event hub name
        EventHubProducerClient producer = new EventHubClientBuilder()
            .connectionString(connectionString, eventHubName)
            .buildProducerClient();

        // prepare a batch of events to send to the event hub    
        EventDataBatch batch = producer.createBatch();
        batch.tryAdd(new EventData("First event"));
        batch.tryAdd(new EventData("Second event"));
        batch.tryAdd(new EventData("Third event"));
        batch.tryAdd(new EventData("Fourth event"));
        batch.tryAdd(new EventData("Fifth event"));

        // send the batch of events to the event hub
        producer.send(batch);

        // close the producer
        producer.close();
    }
}
```

プログラムをビルドし、エラーがないことを確認します。 このプログラムは、受信側プログラムを実行した後に実行します。 

## <a name="receive-events"></a>受信イベント
このチュートリアルのコードは [GitHub の EventProcessorClient サンプル](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/eventhubs/azure-messaging-eventhubs/src/samples/java/com/azure/messaging/eventhubs/EventProcessorClientSample.java)に基づくものであり、完全に動作するアプリケーションを表示する場合に確認することができます。

### <a name="create-a-java-project"></a>Java プロジェクトを作成する

Event Hubs の Java クライアント ライブラリは、 [Maven セントラル リポジトリ](https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22azure-eventhubs-eph%22)の Maven プロジェクトで利用でき、Maven プロジェクト ファイル内の以下の依存関係宣言を使用して参照できます。 

```xml
<dependencies>
    <dependency>
        <groupId>com.azure</groupId>
        <artifactId>azure-messaging-eventhubs</artifactId>
        <version>5.0.1</version>
    </dependency>
</dependencies>
```

1. 次のコードで `Receiver`という新しいクラスを作成します。 プレースホルダーを、イベント ハブとストレージ アカウントの作成時に使用した値に置き換えます。
   
   ```java
     import com.azure.messaging.eventhubs.*;
     import com.azure.messaging.eventhubs.models.ErrorContext;
     import com.azure.messaging.eventhubs.models.EventContext;
     import java.util.concurrent.TimeUnit;
     import java.util.function.Consumer;
    
     public class Receiver {
    
         final static String connectionString = "<EVENT HUBS NAMESPACE - CONNECTION STRING>";
         final static String eventHubName = "<EVENT HUB NAME>";
         
         public static void main(String[] args) throws Exception {
    
             // function to process events
             Consumer<EventContext> processEvent = eventContext  -> {
                 System.out.print("Received event: ");
                 // print the body of the event
                 System.out.println(eventContext.getEventData().getBodyAsString());
                 eventContext.updateCheckpoint();
             };
    
             // function to process errors
             Consumer<ErrorContext> processError = errorContext -> {
                 // print the error message
                 System.out.println(errorContext.getThrowable().getMessage());
             };
    
            
             EventProcessorClient eventProcessorClient = new EventProcessorClientBuilder()
                     .connectionString(connectionString, eventHubName)
                     .processEvent(processEvent)
                     .processError(processError)
                     .consumerGroup(EventHubClientBuilder.DEFAULT_CONSUMER_GROUP_NAME)
                     .checkpointStore(new InMemoryCheckpointStore())
                     .buildEventProcessorClient();
    
             System.out.println("Starting event processor");
             eventProcessorClient.start();
    
             System.out.println("Press enter to stop.");
             System.in.read();
    
             System.out.println("Stopping event processor");
             eventProcessorClient.stop();
             System.out.println("Event processor stopped.");
    
             System.out.println("Exiting process");
         }
     }
    ```
    
2. [GitHub](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/eventhubs/azure-messaging-eventhubs/src/samples/java/com/azure/messaging/eventhubs) から **InMemoryCheckpointStore. java** ファイルをダウンロードし、プロジェクトに追加します。 
3. プログラムをビルドし、エラーがないことを確認します。 

## <a name="run-the-applications"></a>アプリケーションの実行
1. まず、**受信側**アプリケーションを実行します。
1. つぎに、**送信側**アプリケーションを実行します。 
1. **受信者**アプリケーション ウィンドウで、送信側アプリケーションによって発行されたイベントが表示されていることを確認します。
1. アプリケーションを停止するには、受信側アプリケーション ウィンドウで **Enter** キーを押します。 

## <a name="next-steps"></a>次のステップ
[GitHub で Java SDK のサンプルを確認する](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/eventhubs/azure-messaging-eventhubs/src/samples/java/com/azure/messaging/eventhubs)

