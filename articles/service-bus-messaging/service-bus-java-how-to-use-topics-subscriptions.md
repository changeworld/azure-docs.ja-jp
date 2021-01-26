---
title: Java で Azure Service Bus のトピックとサブスクリプションを使用する (azure-messaging-servicebus)
description: このクイックスタートでは、Azure Service Bus トピックにメッセージを送信してそのトピックのサブスクリプションからメッセージを受信する Java コードを、azure-messaging-servicebus パッケージを使用して作成します。
ms.devlang: Java
ms.topic: quickstart
ms.date: 11/09/2020
ms.openlocfilehash: cd95b84c4b55279b40f95eef65cb2490a55d1780
ms.sourcegitcommit: 8c3a656f82aa6f9c2792a27b02bbaa634786f42d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/17/2020
ms.locfileid: "97630243"
---
# <a name="send-messages-to-an-azure-service-bus-topic-and-receive-messages-from-subscriptions-to-the-topic-java"></a>Azure Service Bus トピックへのメッセージ送信とトピックのサブスクリプションからのメッセージ受信 (Java)
このクイックスタートでは、Azure Service Bus トピックにメッセージを送信してそのトピックのサブスクリプションからメッセージを受信する Java コードを、azure-messaging-servicebus パッケージを使用して作成します。

> [!IMPORTANT]
> このクイックスタートでは、新しい azure-messaging-servicebus パッケージを使用します。 古い azure-servicebus パッケージを使用したクイックスタートについては、[azure-servicebus を使用したメッセージの送受信](service-bus-java-how-to-use-topics-subscriptions-legacy.md)に関する記事をご覧ください。

## <a name="prerequisites"></a>前提条件

- Azure サブスクリプション。 このチュートリアルを完了するには、Azure アカウントが必要です。 [Visual Studio または MSDN のサブスクライバー特典](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A85619ABF)を有効にするか、[無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A85619ABF)にサインアップしてください。
- 「[Quickstart:Azure portal を使用して Service Bus トピックとそのサブスクリプションを作成する](service-bus-quickstart-topics-subscriptions-portal.md)」の手順に従ってください。 接続文字列、トピック名、サブスクリプション名を書き留めておきます。 このクイックスタートで使用するサブスクリプションは 1 つだけです。 
- [Azure SDK for Java][Azure SDK for Java] をインストールします。 Eclipse を使用している場合は、Azure SDK for Java が含まれている [Azure Toolkit for Eclipse][Azure Toolkit for Eclipse] をインストールできます。 これで **Microsoft Azure Libraries for Java** をプロジェクトに追加できます。 IntelliJ を使用している場合は、[Azure Toolkit for IntelliJ のインストール](/azure/developer/java/toolkit-for-intellij/installation)に関するページを参照してください。 


## <a name="send-messages-to-a-topic"></a>メッセージをトピックに送信する
このセクションでは、Java コンソール プロジェクトを作成し、既に作成してあるトピックにメッセージを送信するコードを追加します。 

### <a name="create-a-java-console-project"></a>Java コンソール プロジェクトを作成する
Eclipse または任意のツールを使用して Java プロジェクトを作成します。 

### <a name="configure-your-application-to-use-service-bus"></a>Service Bus を使用するようにアプリケーションを構成する
Azure Service Bus ライブラリへの参照を追加します。 Service Bus 用の Java クライアント ライブラリは、[Maven Central Repository](https://search.maven.org/search?q=a:azure-messaging-servicebus) にあります。 Maven プロジェクト ファイル内で次の依存関係宣言を使用して、このライブラリを参照できます。

```xml
<dependency>
    <groupId>com.azure</groupId>
    <artifactId>azure-messaging-servicebus</artifactId>
    <version>7.0.0</version>
</dependency>
```

### <a name="add-code-to-send-messages-to-the-topic"></a>トピックにメッセージを送信するためのコードを追加する
1. 次の `import` ステートメントを Java ファイルの冒頭に追加します。 

    ```java
    import com.azure.messaging.servicebus.*;
    import com.azure.messaging.servicebus.models.*;
    import java.util.concurrent.TimeUnit;
    import java.util.function.Consumer;
    import java.util.Arrays;
    import java.util.List;
    ```    
5. このクラスに、接続文字列とトピック名を保持する変数を次のように定義します。 

    ```java
    static String connectionString = "<NAMESPACE CONNECTION STRING>";
    static String topicName = "<TOPIC NAME>";    
    static String subName = "<SUBSCRIPTION NAME>";
    ```

    `<NAMESPACE CONNECTION STRING>` を Service Bus 名前空間の接続文字列に置き換えます。 さらに、`<TOPIC NAME>` を対象のトピックの名前に置き換えます。
3. 1 つのメッセージをトピックに送信するためのメソッドを、`sendMessage` という名前でこのクラスに追加します。 

    ```java
        static void sendMessage()
    {
        // create a Service Bus Sender client for the queue 
        ServiceBusSenderClient senderClient = new ServiceBusClientBuilder()
                .connectionString(connectionString)
                .sender()
                .topicName(topicName)
                .buildClient();
        
        // send one message to the topic
        senderClient.sendMessage(new ServiceBusMessage("Hello, World!"));
        System.out.println("Sent a single message to the topic: " + topicName);        
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
1. 作成したトピックにメッセージを送信するためのメソッドを、`sendMessageBatch` という名前で追加します。 このメソッドは、トピックの `ServiceBusSenderClient` を作成し、`createMessages` メソッドを呼び出して一連のメッセージを取得した後、1 つまたは複数のバッチを用意して、そのバッチをトピックに送信します。 

```java
    static void sendMessageBatch()
    {
        // create a Service Bus Sender client for the topic 
        ServiceBusSenderClient senderClient = new ServiceBusClientBuilder()
                .connectionString(connectionString)
                .sender()
                .topicName(topicName)
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
            System.out.println("Sent a batch of messages to the topic: " + topicName);
            
            // create a new batch
            messageBatch = senderClient.createMessageBatch();

            // Add that message that we couldn't before.
            if (!messageBatch.tryAddMessage(message)) {
                System.err.printf("Message is too large for an empty batch. Skipping. Max size: %s.", messageBatch.getMaxSizeInBytes());
            }
        }

        if (messageBatch.getCount() > 0) {
            senderClient.sendMessages(messageBatch);
            System.out.println("Sent a batch of messages to the topic: " + topicName);
        }

        //close the client
        senderClient.close();
    }
```

## <a name="receive-messages-from-a-subscription"></a>サブスクリプションからメッセージを受信する
このセクションでは、トピックのサブスクリプションからメッセージを取得するコードを追加します。 

1. サブスクリプションからメッセージを受信するメソッドを、`receiveMessages` という名前で追加します。 このメソッドは、メッセージを処理するためのハンドラーとエラーを処理するためのハンドラーを指定してサブスクリプションの `ServiceBusProcessorClient` を作成します。 次に、プロセッサを起動して数秒間待機し、受信したメッセージを出力した後、プロセッサを停止して終了します。

    ```java
    // handles received messages
    static void receiveMessages() throws InterruptedException
    {
        // Consumer that processes a single message received from Service Bus
        Consumer<ServiceBusReceivedMessageContext> messageProcessor = context -> {
            ServiceBusReceivedMessage message = context.getMessage();
            System.out.println("Received message: " + message.getBody().toString() + " from the subscription: " + subName);
        };

        // Consumer that handles any errors that occur when receiving messages
        Consumer<Throwable> errorHandler = throwable -> {
            System.out.println("Error when receiving messages: " + throwable.getMessage());
            if (throwable instanceof ServiceBusReceiverException) {
                ServiceBusReceiverException serviceBusReceiverException = (ServiceBusReceiverException) throwable;
                System.out.println("Error source: " + serviceBusReceiverException.getErrorSource());
            }
        };

        // Create an instance of the processor through the ServiceBusClientBuilder
        ServiceBusProcessorClient processorClient = new ServiceBusClientBuilder()
            .connectionString(connectionString)
            .processor()
            .topicName(topicName)
            .subscriptionName(subName)
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
プログラムを実行すると、次の出力に似た出力が表示されます。

```console
Sent a batch of messages to the topic: mytopic
Starting the processor
Received message: First message from the subscription: mysub
Received message: Second message from the subscription: mysub
Received message: Third message from the subscription: mysub
Stopping and closing the processor
```

Azure portal の Service Bus 名前空間の **[概要]** ページで、**受信** メッセージ数と **送信** メッセージ数を確認できます。 最新の値を表示するには、1 分程度待ってから、ページを最新の情報に更新しなければならないことがあります。 

:::image type="content" source="./media/service-bus-java-how-to-use-queues/overview-incoming-outgoing-messages.png" alt-text="受信メッセージ数と送信メッセージ数" lightbox="./media/service-bus-java-how-to-use-queues/overview-incoming-outgoing-messages.png":::

下部中央のペインの **[トピック]** タブに切り替えてトピックを選択すると、そのトピックの **[Service Bus トピック]** ページが表示されます。 このページの **[メッセージ]** グラフに 4 つの受信メッセージと 4 つの送信メッセージが確認できると思います。 

:::image type="content" source="./media/service-bus-java-how-to-use-topics-subscriptions/topic-page-portal.png" alt-text="受信メッセージと送信メッセージ" lightbox="./media/service-bus-java-how-to-use-topics-subscriptions/topic-page-portal.png":::

`main` メソッドの `receiveMessages` 呼び出しをコメント アウトして再度アプリを実行した場合、 **[Service Bus トピック]** ページには、8 つの受信メッセージ (うち 4 つは新規) が表示されますが、送信メッセージは 4 つと表示されます。 

:::image type="content" source="./media/service-bus-java-how-to-use-topics-subscriptions/updated-topic-page.png" alt-text="更新後のトピック ページ" lightbox="./media/service-bus-java-how-to-use-topics-subscriptions/updated-topic-page.png":::

このページでいずれかのサブスクリプションを選択すると、その **[Service Bus Subscription]\(Service Bus サブスクリプション\)** ページが表示されます。 このページで、アクティブなメッセージ数や配信不能メッセージ数を確認できます。 この例では、まだ受信者が受け取っていないアクティブなメッセージが 4 つ存在します。 

:::image type="content" source="./media/service-bus-java-how-to-use-topics-subscriptions/active-message-count.png" alt-text="アクティブなメッセージ数" lightbox="./media/service-bus-java-how-to-use-topics-subscriptions/active-message-count.png":::

## <a name="next-steps"></a>次のステップ
次のドキュメントおよびサンプルを参照してください。

- [Java 用の Azure Service Bus クライアント ライブラリ - Readme](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/servicebus/azure-messaging-servicebus/README.md)
- [GitHub のサンプル](https://docs.microsoft.com/samples/azure/azure-sdk-for-java/servicebus-samples/)
- [Java API リファレンス](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-messaging-servicebus/7.0.0/index.html)


[Azure SDK for Java]: /java/api/overview/azure/
[Azure Toolkit for Eclipse]: /azure/developer/java/toolkit-for-eclipse/installation
[Service Bus queues, topics, and subscriptions]: service-bus-queues-topics-subscriptions.md
[SqlFilter]: /dotnet/api/microsoft.azure.servicebus.sqlfilter
[SqlFilter.SqlExpression]: /dotnet/api/microsoft.azure.servicebus.sqlfilter.sqlexpression
[BrokeredMessage]: /dotnet/api/microsoft.servicebus.messaging.brokeredmessage
