---
title: Azure Service Bus キューを Java で使用する (azure-messaging-servicebus)
description: このチュートリアルでは、Java を使用して、Azure Service Bus キューとの間でメッセージを送受信する方法について説明します。 新しい azure-messaging-servicebus パッケージを使用します。
ms.devlang: Java
ms.topic: quickstart
ms.date: 11/09/2020
ms.custom: seo-java-july2019, seo-java-august2019, seo-java-september2019, devx-track-java
ms.openlocfilehash: a91ed2a358a9595a4d22dd629b8d470423b786d6
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/25/2020
ms.locfileid: "95909528"
---
# <a name="send-messages-to-and-receive-messages-from-azure-service-bus-queues-java"></a>Azure Service Bus キューとの間でメッセージを送受信する (Java)
このクイックスタートでは、Azure Service Bus キューとの間でメッセージを送受信する Java アプリを作成します。 

> [!IMPORTANT]
> このクイックスタートでは、新しい azure-messaging-servicebus パッケージ (**プレビュー**) を使用します。 現在の一般提供 (GA) 版の azure-servicebus パッケージを使用したクイックスタートについては、[azure-servicebus を使用したメッセージの送受信](service-bus-java-how-to-use-queues-legacy.md)に関するページを参照してください。

## <a name="prerequisites"></a>前提条件
- Azure サブスクリプション。 このチュートリアルを完了するには、Azure アカウントが必要です。 [MSDN のサブスクライバー特典](https://azure.microsoft.com/pricing/member-offers/credit-for-visual-studio-subscribers/?WT.mc_id=A85619ABF)を有効にするか、[無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A85619ABF)にサインアップしてください。
- 使用するキューがない場合は、「[Azure portal を使用して Service Bus キューを作成する](service-bus-quickstart-portal.md)」の記事にある手順に従って、キューを作成します。 Service Bus 名前空間の **接続文字列** と、作成した **キュー** の名前をメモしておいてください。
- [Azure SDK for Java][Azure SDK for Java] をインストールします。 Eclipse を使用している場合は、Azure SDK for Java が含まれている [Azure Toolkit for Eclipse][Azure Toolkit for Eclipse] をインストールできます。 これで **Microsoft Azure Libraries for Java** をプロジェクトに追加できます。 IntelliJ を使用している場合は、[Azure Toolkit for IntelliJ のインストール](/azure/developer/java/toolkit-for-intellij/installation)に関するページを参照してください。 


## <a name="send-messages-to-a-queue"></a>メッセージをキューに送信する
このセクションでは、Java コンソール プロジェクトを作成し、既に作成してあるキューにメッセージを送信するコードを追加します。 

### <a name="create-a-java-console-project"></a>Java コンソール プロジェクトを作成する
Eclipse または任意のツールを使用して Java プロジェクトを作成します。 

### <a name="configure-your-application-to-use-service-bus"></a>Service Bus を使用するようにアプリケーションを構成する
Azure Service Bus ライブラリへの参照を追加します。 Service Bus 用の Java クライアント ライブラリは、[Maven Central Repository](https://search.maven.org/search?q=a:azure-messaging-servicebus) にあります。 Maven プロジェクト ファイル内で次の依存関係宣言を使用して、このライブラリを参照できます。

```xml
<dependency>
    <groupId>com.azure</groupId>
    <artifactId>azure-messaging-servicebus</artifactId>
    <version>7.0.0-beta.7</version>
</dependency>
```

### <a name="add-code-to-send-messages-to-the-queue"></a>キューにメッセージを送信するコードを追加する
1. 次の `import` ステートメントを Java ファイルの冒頭に追加します。 

    ```java
    import com.azure.messaging.servicebus.*;
    import com.azure.messaging.servicebus.models.*;
    import java.util.concurrent.TimeUnit;
    import java.util.function.Consumer;
    import java.util.Arrays;
    import java.util.List;
    ```    
5. このクラスに、接続文字列とキュー名を保持する変数を次のように定義します。 

    ```java
    static String connectionString = "<NAMESPACE CONNECTION STRING>";
    static String queueName = "<QUEUE NAME>";    
    ```

    `<NAMESPACE CONNECTION STRING>` を Service Bus 名前空間の接続文字列に置き換えます。 さらに、`<QUEUE NAME>` をキューの名前に置き換えます。
3. 1 つのメッセージをキューに送信するためのメソッドを、`sendMessage` という名前でこのクラスに追加します。 

    ```java
    static void sendMessage()
    {
        // create a Service Bus Sender client for the queue 
        ServiceBusSenderClient senderClient = new ServiceBusClientBuilder()
                .connectionString(connectionString)
                .sender()
                .queueName(queueName)
                .buildClient();
        
        // send one message to the queue
        senderClient.sendMessage(new ServiceBusMessage("Hello, World!"));
        System.out.println("Sent a single message to the queue: " + queueName);        
    }
    ```
1. 一連のメッセージを作成するためのメソッドを、`createMessages` という名前でこのクラスに追加します。 通常、これらのメッセージはアプリケーションのさまざまな部分から届きます。 ここでは、次のように一連のサンプル メッセージを作成します。

    ```java
    static List<ServiceBusMessage> createMessages()
    {
        // create a list of messages and return it to the caller
        ServiceBusMessage[] messages = {
                new ServiceBusMessage("First message"),
                new ServiceBusMessage("Second message"),
                new ServiceBusMessage("Third message")
        };
        return Arrays.asList(messages);
    }
    ```
1. 作成したキューにメッセージを送信するためのメソッドを、`sendMessageBatch` という名前で追加します。 このメソッドは、キューの `ServiceBusSenderClient` を作成し、`createMessages` メソッドを呼び出して一連のメッセージを取得した後、1 つまたは複数のバッチを用意して、そのバッチをキューに送信します。 

```java
    static void sendMessageBatch()
    {
        // create a Service Bus Sender client for the queue 
        ServiceBusSenderClient senderClient = new ServiceBusClientBuilder()
                .connectionString(connectionString)
                .sender()
                .queueName(queueName)
                .buildClient();

        // Creates an ServiceBusMessageBatch where the ServiceBus.
        ServiceBusMessageBatch messageBatch = senderClient.createMessageBatch();        
        
        // create a list of messages
        List<ServiceBusMessage> listOfMessages = createMessages();
        
        // We try to add as many messages as a batch can fit based on the maximum size and send to Service Bus when
        // the batch can hold no more messages. Create a new batch for next set of messages and repeat until all
        // messages are sent.        
        for (ServiceBusMessage message : listOfMessages) {
            if (messageBatch.tryAddMessage(message)) {
                continue;
            }

            // The batch is full, so we create a new batch and send the batch.
            senderClient.sendMessages(messageBatch);
            System.out.println("Sent a batch of messages to the queue: " + queueName);
            
            // create a new batch
            messageBatch = senderClient.createMessageBatch();

            // Add that message that we couldn't before.
            if (!messageBatch.tryAddMessage(message)) {
                System.err.printf("Message is too large for an empty batch. Skipping. Max size: %s.", messageBatch.getMaxSizeInBytes());
            }
        }
        
        if (messageBatch.getCount() > 0) {
            senderClient.sendMessages(messageBatch);
            System.out.println("Sent a batch of messages to the queue: " + queueName);
        }

        //close the client
        senderClient.close();
    }
```

## <a name="receive-messages-from-a-queue"></a>キューからメッセージを受信する
このセクションでは、キューからメッセージを取得するコードを追加します。 

1. キューからメッセージを受信するメソッドを、`receiveMessages` という名前で追加します。 このメソッドは、メッセージを処理するためのハンドラーとエラーを処理するためのハンドラーを指定してキューの `ServiceBusProcessorClient` を作成します。 次に、プロセッサを起動して数秒間待機し、受信したメッセージを出力した後、プロセッサを停止して終了します。

    ```java
    // handles received messages
    static void receiveMessages() throws InterruptedException
    {
        // consumer that processes a single message received from Service Bus
        Consumer<ServiceBusReceivedMessageContext> messageProcessor = context -> {
            ServiceBusReceivedMessage message = context.getMessage();
            System.out.println("Received message: " + message.getBody().toString());
        };

        // handles any errors that occur when receiving messages
        Consumer<Throwable> errorHandler = throwable -> {
            System.out.println("Error when receiving messages: " + throwable.getMessage());
            if (throwable instanceof ServiceBusReceiverException) {
                ServiceBusReceiverException serviceBusReceiverException = (ServiceBusReceiverException) throwable;
                System.out.println("Error source: " + serviceBusReceiverException.getErrorSource());
            }
        };

        // create an instance of the processor through the ServiceBusClientBuilder
        ServiceBusProcessorClient processorClient = new ServiceBusClientBuilder()
            .connectionString(connectionString)
            .processor()
            .queueName(queueName)
            .processMessage(messageProcessor)
            .processError(errorHandler)
            .buildProcessorClient();

        System.out.println("Starting the processor");
        processorClient.start();

        TimeUnit.SECONDS.sleep(10);
        System.out.println("Stopping and closing the processor");
        processorClient.close();        
    }    
    ```
2. `sendMessage`、`sendMessageBatch`、`receiveMessages` の各メソッドを呼び出し、`InterruptedException` をスローするように `main` メソッドを更新します。     

    ```java
    public static void main(String[] args) throws InterruptedException {        
        sendMessage();
        sendMessageBatch();
        receiveMessages();
    }   
    ```

## <a name="run-the-app"></a>アプリを実行する
アプリケーションを実行すると、コンソール ウィンドウに次のメッセージが表示されます。 

```console
Sent a single message to the queue: myqueue
Sent a batch of messages to the queue: myqueue
Starting the processor
Received message: Hello, World!
Received message: First message in the batch
Received message: Second message in the batch
Received message: Three message in the batch
Stopping and closing the processor
```

Azure portal の Service Bus 名前空間の **[概要]** ページで、**受信** メッセージ数と **送信** メッセージ数を確認できます。 最新の値を表示するには、1 分程度待ってから、ページを最新の情報に更新しなければならないことがあります。 

:::image type="content" source="./media/service-bus-java-how-to-use-queues/overview-incoming-outgoing-messages.png" alt-text="受信メッセージ数と送信メッセージ数" lightbox="./media/service-bus-java-how-to-use-queues/overview-incoming-outgoing-messages.png":::

この **[概要]** ページでキューを選択して、 **[Service Bus キュー]** ページに移動します。 このページにも、**受信** メッセージ数と **送信** メッセージ数が表示されます。 加えて、キューの **現在のサイズ**、**最大サイズ**、**アクティブなメッセージ数** など、他の情報も表示されます。 

:::image type="content" source="./media/service-bus-java-how-to-use-queues/queue-details.png" alt-text="キューの詳細" lightbox="./media/service-bus-java-how-to-use-queues/queue-details.png":::



## <a name="next-steps"></a>次の手順
次のドキュメントおよびサンプルを参照してください。

- [Java 用の Azure Service Bus クライアント ライブラリ - Readme](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/servicebus/azure-messaging-servicebus/README.md)
- [GitHub のサンプル](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/servicebus/azure-messaging-servicebus/src/samples)
- [Java API リファレンス](https://docs.microsoft.com/java/api/overview/azure/servicebus?view=azure-java-preview&preserve-view=true)

[その他のサンプルを GitHub で](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/servicebus/azure-messaging-servicebus)ご覧ください。 

[Azure SDK for Java]: /azure/developer/java/sdk/java-sdk-azure-get-started
[Azure Toolkit for Eclipse]: /azure/developer/java/toolkit-for-eclipse/installation
[Queues, topics, and subscriptions]: service-bus-queues-topics-subscriptions.md
[BrokeredMessage]: /dotnet/api/microsoft.servicebus.messaging.brokeredmessage
