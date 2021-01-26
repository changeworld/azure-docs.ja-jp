---
title: azure-messaging-servicebus を使用して Azure Service Bus トピックにメッセージを送信する
description: このクイックスタートでは、azure-messaging-servicebus パッケージを使用して、Azure Service Bus トピックにメッセージを送信する方法について説明します。
ms.topic: quickstart
ms.tgt_pltfrm: dotnet
ms.date: 11/13/2020
ms.custom: devx-track-csharp
ms.openlocfilehash: 5dfdf846410e9b622f3ef1e9006aa9846de9979c
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/02/2020
ms.locfileid: "96498712"
---
# <a name="send-messages-to-an-azure-service-bus-topic-and-receive-messages-from-subscriptions-to-the-topic-net"></a>Azure Service Bus トピックへのメッセージ送信とトピックのサブスクリプションからのメッセージ受信 (.NET)
このチュートリアルでは、Service Bus トピックにメッセージを送信したりそのトピックのサブスクリプションからメッセージを受信したりする .NET Core コンソール アプリの作成方法について説明します。 

> [!Important]
> このクイックスタートでは、新しい **Azure.Messaging.ServiceBus** パッケージを使用します。 以前の Microsoft.Azure.ServiceBus パッケージを使用したクイックスタートについては、[Microsoft.Azure.ServiceBus パッケージを使用したメッセージの送受信](service-bus-dotnet-how-to-use-topics-subscriptions-legacy.md)に関するページを参照してください。

## <a name="prerequisites"></a>前提条件

- [Visual Studio 2019](https://www.visualstudio.com/vs)
- Azure サブスクリプション。 このチュートリアルを完了するには、Azure アカウントが必要です。 [Visual Studio または MSDN のサブスクライバー特典](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A85619ABF)を有効にするか、[無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A85619ABF)にサインアップしてください。
- 「[Quickstart:Azure portal を使用して Service Bus トピックとそのサブスクリプションを作成する](service-bus-quickstart-topics-subscriptions-portal.md)」の手順に従ってください。 接続文字列、トピック名、サブスクリプション名を書き留めておきます。 このクイックスタートで使用するサブスクリプションは 1 つだけです。 

## <a name="send-messages-to-a-topic"></a>メッセージをトピックに送信する
このセクションでは、Visual Studio で .NET Core コンソール アプリケーションを作成し、自分が作成したトピックにメッセージを送信するためのコードを追加します。 

### <a name="create-a-console-application"></a>コンソール アプリケーションの作成
Visual Studio を起動し、C# 用の新しい **コンソール アプリ (.NET Core)** プロジェクトを作成します。 

### <a name="add-the-service-bus-nuget-package"></a>Service Bus NuGet パッケージの追加

1. 新しく作成したプロジェクトを右クリックし、**[NuGet パッケージの管理]** を選択します。
1. **[参照]** を選択します。 **[Azure.Messaging.ServiceBus](https://www.nuget.org/packages/Azure.Messaging.ServiceBus/)** を検索して選択します。
1. **[インストール]** を選択し、インストールが完了したら、NuGet パッケージ マネージャーを閉じます。

### <a name="add-code-to-send-messages-to-the-topic"></a>トピックにメッセージを送信するためのコードを追加する 

1. Program.cs 内の名前空間定義とクラス宣言の前に、次の `using` ステートメントを追加します。
   
    ```csharp
    using System;
    using System.Collections.Generic;
    using System.Threading.Tasks;
    using Azure.Messaging.ServiceBus;
    ```
1. `Program` クラス内で次の変数を宣言します。

    ```csharp
        static string connectionString = "<NAMESPACE CONNECTION STRING>";
        static string topicName = "<TOPIC NAME>";
        static string subscriptionName = "<SUBSCRIPTION NAME>";
    ```

    次の値を置き換えます。
    - `<NAMESPACE CONNECTION STRING>`: Service Bus 名前空間の接続文字列
    - `<TOPIC NAME>`: トピックの名前
    - `<SUBSCRIPTION NAME>`: サブスクリプションの名前
2. トピックに 1 つのメッセージを送信する `SendMessageToTopicAsync` という名前のメソッドを追加します。 

    ```csharp
        static async Task SendMessageToTopicAsync()
        {
            // create a Service Bus client 
            await using (ServiceBusClient client = new ServiceBusClient(connectionString))
            {
                // create a sender for the topic
                ServiceBusSender sender = client.CreateSender(topicName);
                await sender.SendMessageAsync(new ServiceBusMessage("Hello, World!"));
                Console.WriteLine($"Sent a single message to the topic: {topicName}");
            }
        }
    ```
1. メッセージのキュー (.NET キュー) を作成するためのメソッドを `CreateMessages` という名前で `Program` クラスに追加します。 通常、これらのメッセージはアプリケーションのさまざまな部分から届きます。 ここでは、サンプル メッセージのキューを作成します。

    ```csharp
        static Queue<ServiceBusMessage> CreateMessages()
        {
            // create a queue containing the messages and return it to the caller
            Queue<ServiceBusMessage> messages = new Queue<ServiceBusMessage>();
            messages.Enqueue(new ServiceBusMessage("First message"));
            messages.Enqueue(new ServiceBusMessage("Second message"));
            messages.Enqueue(new ServiceBusMessage("Third message"));
            return messages;
        }
    ```
1. `SendMessageBatchAsync` という名前のメソッドを `Program` クラスに追加し、次のコードを追加します。 このメソッドでは、メッセージのキューを受け取り、Service Bus トピックに送信するバッチを 1 つ以上準備します。 

    ```csharp
        static async Task SendMessageBatchToTopicAsync()
        {
            // create a Service Bus client 
            await using (ServiceBusClient client = new ServiceBusClient(connectionString))
            {

                // create a sender for the topic 
                ServiceBusSender sender = client.CreateSender(topicName);

                // get the messages to be sent to the Service Bus topic
                Queue<ServiceBusMessage> messages = CreateMessages();

                // total number of messages to be sent to the Service Bus topic
                int messageCount = messages.Count;

                // while all messages are not sent to the Service Bus topic
                while (messages.Count > 0)
                {
                    // start a new batch 
                    using ServiceBusMessageBatch messageBatch = await sender.CreateMessageBatchAsync();

                    // add the first message to the batch
                    if (messageBatch.TryAddMessage(messages.Peek()))
                    {
                        // dequeue the message from the .NET queue once the message is added to the batch
                        messages.Dequeue();
                    }
                    else
                    {
                        // if the first message can't fit, then it is too large for the batch
                        throw new Exception($"Message {messageCount - messages.Count} is too large and cannot be sent.");
                    }

                    // add as many messages as possible to the current batch
                    while (messages.Count > 0 && messageBatch.TryAddMessage(messages.Peek()))
                    {
                        // dequeue the message from the .NET queue as it has been added to the batch
                        messages.Dequeue();
                    }

                    // now, send the batch
                    await sender.SendMessagesAsync(messageBatch);

                    // if there are any remaining messages in the .NET queue, the while loop repeats 
                }

                Console.WriteLine($"Sent a batch of {messageCount} messages to the topic: {topicName}");
            }
        }
    ```
1. `Main()` メソッドを次の **async** `Main` メソッドに置き換えます。 これは両方の送信メソッドを呼び出して、単一のメッセージおよびメッセージのバッチをトピックに送信するものです。  

    ```csharp
        static async Task Main()
        {
            // send a message to the topic
            await SendMessageToTopicAsync();

            // send a batch of messages to the topic
            await SendMessageBatchToTopicAsync();
        }
    ```
5. アプリケーションを実行します。 次の出力が表示されます。

    ```console
    Sent a single message to the topic: mytopic
    Sent a batch of 3 messages to the topic: mytopic
    ```
1. Azure portal で次の手順を実行します。
    1. 自分の Service Bus 名前空間に移動します。 
    1. **[概要]** ページ中央下のペインで、 **[トピック]** タブに切り替えてから、Service Bus トピックを選択します。 次の例では、`mytopic` になっています。
    
        :::image type="content" source="./media/service-bus-dotnet-how-to-use-topics-subscriptions/select-topic.png" alt-text="トピックの選択":::
    1. **[Service Bus トピック]** ページ下部の **[Metrics]\(メトリック\)** セクションの **[メッセージ]** グラフで、トピックに 4 つの受信メッセージがあることがわかります。 値が表示されない場合は、数分待ってページを最新の情報に更新すると、最新のグラフが表示されます。 

        :::image type="content" source="./media/service-bus-dotnet-how-to-use-topics-subscriptions/sent-messages-essentials.png" alt-text="トピックに送信されたメッセージ" lightbox="./media/service-bus-dotnet-how-to-use-topics-subscriptions/sent-messages-essentials.png":::
    4. 下のペインでサブスクリプションを選択します。 次の例では、**S1** になっています。 **[Service Bus Subscription]\(Service Bus サブスクリプション\)** ページには、 **[アクティブなメッセージ数]** が **4** と表示されます。 トピックに送信した 4 つのメッセージはサブスクリプションで受信されています。しかし、それらはどの受信者によっても取り出されていません。 
    
        :::image type="content" source="./media/service-bus-dotnet-how-to-use-topics-subscriptions/subscription-page.png" alt-text="サブスクリプションで受信されたメッセージ" lightbox="./media/service-bus-dotnet-how-to-use-topics-subscriptions/subscription-page.png":::
    

## <a name="receive-messages-from-a-subscription"></a>サブスクリプションからメッセージを受信する

1. メッセージや発生したエラーを処理する次のメソッドを `Program` クラスに追加します。 

    ```csharp
        static async Task MessageHandler(ProcessMessageEventArgs args)
        {
            string body = args.Message.Body.ToString();
            Console.WriteLine($"Received: {body} from subscription: {subscriptionName}");

            // complete the message. messages is deleted from the queue. 
            await args.CompleteMessageAsync(args.Message);
        }

        static Task ErrorHandler(ProcessErrorEventArgs args)
        {
            Console.WriteLine(args.Exception.ToString());
            return Task.CompletedTask;
        }
    ```
1. 次の `ReceiveMessagesFromSubscriptionAsync` メソッドを `Program` クラスに追加します。

    ```csharp
        static async Task ReceiveMessagesFromSubscriptionAsync()
        {
            await using (ServiceBusClient client = new ServiceBusClient(connectionString))
            {
                // create a processor that we can use to process the messages
                ServiceBusProcessor processor = client.CreateProcessor(topicName, subscriptionName, new ServiceBusProcessorOptions());

                // add handler to process messages
                processor.ProcessMessageAsync += MessageHandler;

                // add handler to process any errors
                processor.ProcessErrorAsync += ErrorHandler;

                // start processing 
                await processor.StartProcessingAsync();

                Console.WriteLine("Wait for a minute and then press any key to end the processing");
                Console.ReadKey();

                // stop processing 
                Console.WriteLine("\nStopping the receiver...");
                await processor.StopProcessingAsync();
                Console.WriteLine("Stopped receiving messages");
            }
        }
    ```
1. `ReceiveMessagesFromSubscriptionAsync` メソッドの呼び出しを `Main` メソッドに追加します。 単にメッセージの受信をテストしたい場合は、`SendMessagesToTopicAsync` メソッドをコメントアウトしてください。 そうしなかった場合は、トピックに送信された 4 つのメッセージがさらに表示されます。 

    ```csharp
        static async Task Main()
        {
            // send a message to the topic
            await SendMessageToTopicAsync();

            // send a batch of messages to the topic
            await SendMessageBatchToTopicAsync();

            // receive messages from the subscription
            await ReceiveMessagesFromSubscriptionAsync();
        }
    ```
## <a name="run-the-app"></a>アプリを実行する
アプリケーションを実行します。 しばらく待ってから、任意のキーを押してメッセージの受信を停止します。 次の出力が表示されます (押したのは Space キー)。 

```console
Sent a single message to the topic: mytopic
Sent a batch of 3 messages to the topic: mytopic
Wait for a minute and then press any key to end the processing
Received: Hello, World! from subscription: mysub
Received: First message from subscription: mysub
Received: Second message from subscription: mysub
Received: Third message from subscription: mysub
Received: Hello, World! from subscription: mysub
Received: First message from subscription: mysub
Received: Second message from subscription: mysub
Received: Third message from subscription: mysub

Stopping the receiver...
Stopped receiving messages
```

もう一度ポータルを確認します。 

- **[Service Bus トピック]** ページの **[メッセージ]** グラフに、8 つの受信メッセージと 8 つの送信メッセージが表示されます。 これらの数値が表示されない場合は、数分待ってページを最新の情報に更新すると、最新のグラフが表示されます。 

    :::image type="content" source="./media/service-bus-dotnet-how-to-use-topics-subscriptions/messages-size-final.png" alt-text="送受信されたメッセージ" lightbox="./media/service-bus-dotnet-how-to-use-topics-subscriptions/messages-size-final.png":::
- **[Service Bus Subscription]\(Service Bus サブスクリプション\)** ページには、 **[アクティブなメッセージ数]** がゼロと表示されます。 これは受信者が、このサブスクリプションからのメッセージを受け取って、そのメッセージを完了したためです。 

    :::image type="content" source="./media/service-bus-dotnet-how-to-use-topics-subscriptions/subscription-page-final.png" alt-text="サブスクリプション側におけるアクティブなメッセージ数 (処理後)" lightbox="./media/service-bus-dotnet-how-to-use-topics-subscriptions/subscription-page-final.png":::
    


## <a name="next-steps"></a>次の手順
次のドキュメントおよびサンプルを参照してください。

- [.NET 用の Azure Service Bus クライアント ライブラリ - Readme](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/servicebus/Azure.Messaging.ServiceBus)
- [GitHub のサンプル](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/servicebus/Azure.Messaging.ServiceBus/samples)
- [.NET API リファレンス](/dotnet/api/azure.messaging.servicebus?preserve-view=true&view=azure-dotnet-preview)