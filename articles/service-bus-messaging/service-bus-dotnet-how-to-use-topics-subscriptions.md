---
title: azure-messaging-servicebus を使用して Azure Service Bus トピックにメッセージを送信する
description: このクイックスタートでは、azure-messaging-servicebus パッケージを使用して、Azure Service Bus トピックにメッセージを送信する方法について説明します。
ms.topic: quickstart
ms.tgt_pltfrm: dotnet
ms.date: 03/16/2021
ms.custom: contperf-fy21q3
ms.openlocfilehash: 79eb7783fd3daf546539dd5b9048f4e9f484374f
ms.sourcegitcommit: 02bc06155692213ef031f049f5dcf4c418e9f509
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/03/2021
ms.locfileid: "106279801"
---
# <a name="send-messages-to-an-azure-service-bus-topic-and-receive-messages-from-subscriptions-to-the-topic-net"></a>Azure Service Bus トピックへのメッセージ送信とトピックのサブスクリプションからのメッセージ受信 (.NET)
このチュートリアルでは、次のタスクを実行する C# アプリケーションを作成します。

1. Service Bus トピックにメッセージを送信します。 

    Service Bus トピックでは、送信側アプリケーションがメッセージを送信するためのエンドポイントが提供されます。 1 つのトピックで 1 つ以上のサブスクリプションを使用できます。 トピックの各サブスクリプションでは、そのトピックに送信されたメッセージのコピーが取得されます。 Service Bus トピックの詳細については、「[Azure Service Bus とは](service-bus-messaging-overview.md)」を参照してください。 
1. トピックのサブスクリプションからメッセージを受信します。 

    :::image type="content" source="./media/service-bus-messaging-overview/about-service-bus-topic.png" alt-text="Service Bus トピックとサブスクリプション":::

    > [!Important]
    > このクイックスタートでは、新しい **Azure.Messaging.ServiceBus** パッケージを使用します。 以前の Microsoft.Azure.ServiceBus パッケージを使用している場合は、[Microsoft.Azure.ServiceBus パッケージを使用したメッセージの送受信](service-bus-dotnet-how-to-use-topics-subscriptions-legacy.md)に関するページを参照してください。

## <a name="prerequisites"></a>前提条件

- Azure サブスクリプション。 このチュートリアルを完了するには、Azure アカウントが必要です。 [Visual Studio または MSDN のサブスクライバー特典](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A85619ABF)を有効にするか、[無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A85619ABF)にサインアップしてください。
- こちらの[クイック スタート](service-bus-quickstart-topics-subscriptions-portal.md)の手順に従って、Service Bus トピックとトピックへのサブスクリプションを作成します。 

    > [!NOTE]
    > このチュートリアルでは、名前空間への接続文字列、トピック名、およびトピックへの 1 つのサブスクリプションの名前を使用します。  
- [Visual Studio 2019](https://www.visualstudio.com/vs)。 
 
## <a name="send-messages-to-a-topic"></a>メッセージをトピックに送信する
このセクションでは、Visual Studio で .NET Core コンソール アプリケーションを作成し、作成した Service Bus トピックにメッセージを送信するためのコードを追加します。 

### <a name="create-a-console-application"></a>コンソール アプリケーションの作成
Visual Studio を使用して .NET Core コンソール アプリケーションを作成します。 

1. Visual Studio を起動します。  
1. **[はじめに]** ページが表示されたら、 **[新しいプロジェクトの作成]** を選択します。 
1. **[新しいプロジェクトの作成]** ページで、これらの手順を実行します。 
    1. プログラミング言語として **[C#]** を選択します。 
    1. プロジェクトの種類として **[コンソール]** を選択します。 
    1. テンプレートの一覧から **[コンソール アプリ (.NET Core)]** を選択します。 
    1. 次に、 **[次へ]** を選択します。 
    
        :::image type="content" source="./media/service-bus-dotnet-how-to-use-topics-subscriptions/create-console-project.png" alt-text="コンソール アプリ プロジェクトを作成する":::
1. **[新しいプロジェクトの構成]** ページで、これらの手順を実行します。 
    1. **[プロジェクト名]** に、プロジェクトの名前を入力します。 
    1. **[場所]** には、プロジェクトとソリューションのファイルの場所を選択します。 
    1. **[ソリューション名]** に、ソリューションの名前を入力します。 Visual Studio ソリューションには 1 つ以上のプロジェクトを含めることができきます。 このクイックスタートでは、ソリューションのプロジェクトは 1 つだけになります。 
    1. **[作成]** を選択してプロジェクトを作成します。 
            
        :::image type="content" source="./media/service-bus-dotnet-how-to-use-topics-subscriptions/create-console-project-2.png" alt-text="プロジェクトとソリューションの名前と場所を入力する":::    


### <a name="add-the-service-bus-nuget-package"></a>Service Bus NuGet パッケージの追加
1. 新しく作成したプロジェクトを右クリックし、**[NuGet パッケージの管理]** を選択します。

    :::image type="content" source="./media/service-bus-dotnet-how-to-use-topics-subscriptions/manage-nuget-packages-menu.png" alt-text="NuGet パッケージ メニューを管理する":::        
1. **[参照]** タブに切り替えます。
1. **[Azure.Messaging.ServiceBus](https://www.nuget.org/packages/Azure.Messaging.ServiceBus/)** を検索して選択します。
1. **[インストール]** を選択してインストールを完了します。

    :::image type="content" source="./media/service-bus-dotnet-how-to-use-topics-subscriptions/select-service-bus-package.png" alt-text="Service Bus NuGet パッケージを選択する。":::
5. **[変更のプレビュー]** ダイアログ ボックスが表示されたら、 **[OK]** を選択して続行します。 
1. **[ライセンスへの同意]** ページが表示されたら、 **[同意する]** を選択して続行します。 
    

### <a name="add-code-to-send-messages-to-the-topic"></a>トピックにメッセージを送信するためのコードを追加する 

1. **ソリューション エクスプローラー** ウィンドウで **Program.cs** をダブルクリックし、エディターでファイルを開きます。 
1. 次の `using` ステートメントを、名前空間定義の先頭 (クラス宣言の前) に追加します。
   
    ```csharp
    using System;
    using System.Collections.Generic;
    using System.Threading.Tasks;
    using Azure.Messaging.ServiceBus;
    ```
1. `Program` クラス内 (`Main` 関数の上) で次の変数を宣言します。

    ```csharp
        static string connectionString = "<NAMESPACE CONNECTION STRING>";
        static string topicName = "<SERVICE BUS TOPIC NAME>";
        static string subscriptionName = "<SERVICE BUS - TOPIC SUBSCRIPTION NAME>";
    ```

    次の値を置き換えます。
    - `<NAMESPACE CONNECTION STRING>`: Service Bus 名前空間の接続文字列
    - `<TOPIC NAME>`: トピックの名前
    - `<SUBSCRIPTION NAME>`: サブスクリプションの名前

### <a name="send-a-single-message-to-the-topic"></a>1 つのメッセージをトピックに送信する
`SendMessageToTopicAsync` という名前のメソッドを、`Main` メソッドの上または下の `Program` クラスに追加します。 このメソッドにより、1 つのメッセージがトピックに送信されます。

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

このメソッドでは、次の手順が実行されます。 
1. 名前空間への接続文字列を使用して [ServiceBusClient](/dotnet/api/azure.messaging.servicebus.servicebusclient) オブジェクトを作成します。 
1. `ServiceBusClient` オブジェクトを使用して、指定した Service Bus トピックに対する [ServiceBusSender](/dotnet/api/azure.messaging.servicebus.servicebussender) オブジェクトを作成します。 この手順では、[ServiceBusClient.CreateSender](/dotnet/api/azure.messaging.servicebus.servicebusclient.createsender) メソッドが使用されます。
1. 次に、このメソッドは、[ServiceBusSender.SendMessageAsync](/dotnet/api/azure.messaging.servicebus.servicebussender.sendmessageasync) メソッドを使用して、1 つのテスト メッセージを Service Bus トピックに送信します。 

### <a name="send-a-batch-of-messages-to-the-topic"></a>トピックにメッセージのバッチを送信する
1. メッセージのキュー (.NET キュー。Service Bus キューではない) を作成するための `CreateMessages` という名前のメソッドを `Program` クラスに追加します。 通常、これらのメッセージはアプリケーションのさまざまな部分から届きます。 ここでは、サンプル メッセージのキューを作成します。 .NET キューに慣れていない場合は、[Queue.Enqueue](/dotnet/api/system.collections.queue.enqueue) に関するページを参照してください。

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

    コードの重要な手順を次に示します。
    1. 名前空間への接続文字列を使用して [ServiceBusClient](/dotnet/api/azure.messaging.servicebus.servicebusclient) オブジェクトを作成します。 
    1. `ServiceBusClient` オブジェクトで [CreateSender](/dotnet/api/azure.messaging.servicebus.servicebusclient.createsender) メソッドを呼び出して、指定した Service Bus トピックに対して [ServiceBusSender](/dotnet/api/azure.messaging.servicebus.servicebussender) オブジェクトを作成します。 
    1. ヘルパー メソッド `GetMessages` を呼び出して、Service Bus トピックに送信されるメッセージのキューを取得します。 
    1. [ServiceBusSender.CreateMessageBatchAsync](/dotnet/api/azure.messaging.servicebus.servicebussender.createmessagebatchasync) を使用して [ServiceBusMessageBatch](/dotnet/api/azure.messaging.servicebus.servicebusmessagebatch) を作成します。
    1. [ServiceBusMessageBatch.TryAddMessage](/dotnet/api/azure.messaging.servicebus.servicebusmessagebatch.tryaddmessage) を使用して、メッセージをバッチに追加します。 メッセージはバッチに追加されると、.NET キューから削除されます。 
    1. [ServiceBusSender.SendMessagesAsync](/dotnet/api/azure.messaging.servicebus.servicebussender.sendmessagesasync) メソッドを使用して、メッセージのバッチを Service Bus トピックに送信します。

### <a name="update-the-main-method"></a>Main メソッドを更新する
`Main()` メソッドを次の **async** `Main` メソッドに置き換えます。 これは両方の送信メソッドを呼び出して、単一のメッセージおよびメッセージのバッチをトピックに送信するものです。  

```csharp
    static async Task Main()
    {
        // send a single message to the topic
        await SendMessageToTopicAsync();

        // send a batch of messages to the topic
        await SendMessageBatchToTopicAsync();
    }
```

### <a name="test-the-app-to-send-messages-to-the-topic"></a>メッセージをトピックに送信するアプリをテストする
1. アプリケーションを実行します。 次の出力が表示されます。

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

    コードの重要な手順を次に示します。
    1. 名前空間への接続文字列を使用して [ServiceBusClient](/dotnet/api/azure.messaging.servicebus.servicebusclient) オブジェクトを作成します。 
    1. `ServiceBusClient` オブジェクトで [CreateProcessor](/dotnet/api/azure.messaging.servicebus.servicebusclient.createprocessor) メソッドを呼び出して、指定した Service Bus トピックとサブスクリプションの組み合わせに対して [ServiceBusProcessor](/dotnet/api/azure.messaging.servicebus.servicebusprocessor) オブジェクトを作成します。 
    1. `ServiceBusProcessor` オブジェクトの [ProcessMessageAsync](/dotnet/api/azure.messaging.servicebus.servicebusprocessor.processmessageasync) および [ProcessErrorAsync](/dotnet/api/azure.messaging.servicebus.servicebusprocessor.processerrorasync) イベントのハンドラーを指定します。 
    1. `ServiceBusProcessor` オブジェクトで [StartProcessingAsync](/dotnet/api/azure.messaging.servicebus.servicebusprocessor.startprocessingasync) を呼び出して、メッセージの処理を開始します。 
    1. ユーザーがキーを押して処理を終了すると、`ServiceBusProcessor` オブジェクトで [StopProcessingAsync](/dotnet/api/azure.messaging.servicebus.servicebusprocessor.stopprocessingasync) が呼び出されます。 
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
- [GitHub にある Azure Service Bus 用の .NET サンプル](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/servicebus/Azure.Messaging.ServiceBus/samples)
- [.NET API リファレンス](/dotnet/api/azure.messaging.servicebus?preserve-view=true)