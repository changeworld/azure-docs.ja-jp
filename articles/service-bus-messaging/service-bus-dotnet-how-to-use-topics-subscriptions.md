---
title: Azure Service Bus のトピックとサブスクリプションの概要
description: このクイックスタートでは、azure-messaging-servicebus パッケージを使用して、Azure Service Bus トピックにメッセージを送信する方法について説明します。
ms.topic: quickstart
ms.tgt_pltfrm: dotnet
ms.date: 06/29/2021
ms.custom: contperf-fy21q3
ms.openlocfilehash: 8a53a8099df37ec3f24489cccaf91af2d53c97ce
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/13/2021
ms.locfileid: "121747125"
---
# <a name="send-messages-to-an-azure-service-bus-topic-and-receive-messages-from-its-subscriptions-net"></a>Azure Service Bus トピックへのメッセージ送信とそのサブスクリプションからのメッセージ受信 (.NET)
このクイックスタートでは、[Azure.Messaging.ServiceBus](https://www.nuget.org/packages/Azure.Messaging.ServiceBus/) .NET ライブラリを使用して、Service Bus トピックにメッセージを送信し、トピックへのサブスクリプションからメッセージを受信する方法について説明します。

## <a name="prerequisites"></a>前提条件
このサービスを初めて使用する場合は、このクイックスタートを実行する前に、[Service Bus の概要](service-bus-messaging-overview.md)に関する記事を参照してください。 

- **Azure サブスクリプション**。 Azure Service Bus など、Azure の各種サービスを使用するには、サブスクリプションが必要です。  既存の Microsoft Azure アカウントをお持ちでない場合は、[アカウントを作成する](https://azure.microsoft.com)際に、[無料試用版](https://azure.microsoft.com/free/)にサインアップするか、MSDN サブスクライバー特典を利用できます。
- **Microsoft Visual Studio 2019**。 Azure Service Bus クライアント ライブラリでは、C# 8.0 で導入された新機能を使用します。  以前のバージョンの C# 言語でライブラリを使用することもできますが、新しい構文は使用できません。 完全な構文を使用するには、[.NET Core SDK](https://dotnet.microsoft.com/download) 3.0 以上で、[言語バージョン](/dotnet/csharp/language-reference/configure-language-version#override-a-default)を `latest` に設定してコンパイルすることをお勧めします。 Visual Studio を使用している場合、Visual Studio 2019 より前のバージョンには、C# 8.0 プロジェクトをビルドするために必要なツールとの互換性がありません。 無料の Community エディションを含む Visual Studio 2019 は、[こちら](https://visualstudio.microsoft.com/vs/)からダウンロードできます。
- こちらの[クイック スタート](service-bus-quickstart-topics-subscriptions-portal.md)の手順に従って、Service Bus トピックとトピックへのサブスクリプションを作成します。 

    > [!IMPORTANT]
    > 名前空間への接続文字列、トピック名、トピックへのいずれかのサブスクリプションの名前を書き留めてください。 このチュートリアルで後ほど使用します。
 
## <a name="send-messages-to-a-topic"></a>メッセージをトピックに送信する
このセクションでは、Service Bus トピックにメッセージを送信する .NET Core コンソール アプリケーションを作成する方法を説明します。 

### <a name="create-a-console-application"></a>コンソール アプリケーションの作成

1. Visual Studio 2019 を起動します。 
1. **[新しいプロジェクトの作成]** を選択します。 
1. **[新しいプロジェクトの作成]** ダイアログ ボックスで、次の手順に従います。このダイアログ ボックスが表示されない場合は、メニューで **[ファイル]** 、 **[新規]** 、 **[プロジェクト]** の順に選択します。 
    1. プログラミング言語として **[C#]** を選択します。
    1. アプリケーションの種類として **[コンソール]** を選択します。 
    1. 結果リストから **[コンソール アプリケーション]** を選択します。 
    1. 次に、 **[次へ]** を選択します。 

        :::image type="content" source="./media/service-bus-dotnet-get-started-with-queues/new-send-project.png" alt-text="[C#] と [コンソール] が選択された [新しいプロジェクトの作成] ダイアログ ボックスを示す画像":::
1. プロジェクト名に「**TopicSender**」、ソリューション名に「**ServiceBusTopicQuickStart**」と入力し、 **[次へ]** を選択します。 
1. **[追加情報]** ページで、 **[作成]** を選択してソリューションとプロジェクトを作成します。

### <a name="add-the-service-bus-nuget-package"></a>Service Bus NuGet パッケージの追加

1. メニューから **[ツール]**  >  **[NuGet パッケージ マネージャー]**  >  **[パッケージ マネージャー コンソール]** の順に選択します。 
1. 次のコマンドを実行して、**Azure.Messaging.ServiceBus** NuGet パッケージをインストールします。

    ```cmd
    Install-Package Azure.Messaging.ServiceBus
    ```

### <a name="add-code-to-send-messages-to-the-topic"></a>トピックにメッセージを送信するためのコードを追加する 

1. **Program.cs** のコードを次のコードに置き換えます。 コードの重要な手順を次に示します。  
    1. 名前空間への接続文字列を使用して [ServiceBusClient](/dotnet/api/azure.messaging.servicebus.servicebusclient) オブジェクトを作成します。 
    1. `ServiceBusClient` オブジェクトで `CreateSender` メソッドを呼び出して、特定の Service Bus トピックに対して [ServiceBusSender](/dotnet/api/azure.messaging.servicebus.servicebussender) オブジェクトを作成します。     
    1. `ServiceBusSender.CreateMessageBatchAsync` メソッドを使用して `ServiceBusMessageBatch` オブジェクトを作成します。
    1. `ServiceBusMessageBatch.TryAddMessage` メソッドを使用して、メッセージをバッチに追加します。 
    1. `ServiceBusSender.SendMessagesAsync` メソッドを使用して、メッセージのバッチを Service Bus トピックに送信します。
    
        詳細については、コードのコメントを参照してください。
        ```csharp
        using System;
        using System.Threading.Tasks;
        using Azure.Messaging.ServiceBus;
        
        namespace TopicSender
        {
            class Program
            {
                // connection string to your Service Bus namespace
                static string connectionString = "<NAMESPACE CONNECTION STRING>";
        
                // name of your Service Bus topic
                static string topicName = "<TOPIC NAME>";
        
                // the client that owns the connection and can be used to create senders and receivers
                static ServiceBusClient client;
        
                // the sender used to publish messages to the topic
                static ServiceBusSender sender;
        
                // number of messages to be sent to the topic
                private const int numOfMessages = 3;
        
                static async Task Main()
                {
                    // The Service Bus client types are safe to cache and use as a singleton for the lifetime
                    // of the application, which is best practice when messages are being published or read
                    // regularly.
                    //
                    // Create the clients that we'll use for sending and processing messages.
                    client = new ServiceBusClient(connectionString);
                    sender = client.CreateSender(topicName);
        
                    // create a batch 
                    using ServiceBusMessageBatch messageBatch = await sender.CreateMessageBatchAsync();
        
                    for (int i = 1; i <= numOfMessages; i++)
                    {
                        // try adding a message to the batch
                        if (!messageBatch.TryAddMessage(new ServiceBusMessage($"Message {i}")))
                        {
                            // if it is too large for the batch
                            throw new Exception($"The message {i} is too large to fit in the batch.");
                        }
                    }
        
                    try
                    {
                        // Use the producer client to send the batch of messages to the Service Bus topic
                        await sender.SendMessagesAsync(messageBatch);
                        Console.WriteLine($"A batch of {numOfMessages} messages has been published to the topic.");
                    }
                    finally
                    {
                        // Calling DisposeAsync on client types is required to ensure that network
                        // resources and other unmanaged objects are properly cleaned up.
                        await sender.DisposeAsync();
                        await client.DisposeAsync();
                    }
        
                    Console.WriteLine("Press any key to end the application");
                    Console.ReadKey();
                }
            }
        }    
        ```
1. `<NAMESPACE CONNECTION STRING>` を Service Bus 名前空間の接続文字列に置き換えます。 また、`<TOPIC NAME>` を実際の Service Bus トピックの名前に置き換えます。 
1. プロジェクトをビルドし、エラーがないことを確認します。 
1. プログラムを実行し、確認メッセージが表示されるまで待ちます。
    
    ```bash
    A batch of 3 messages has been published to the topic
    ```
1. Azure portal で次の手順を実行します。
    1. 自分の Service Bus 名前空間に移動します。 
    1. **[概要]** ページ中央下のペインで、 **[トピック]** タブに切り替えてから、Service Bus トピックを選択します。 次の例では、`mytopic` になっています。
    
        :::image type="content" source="./media/service-bus-dotnet-how-to-use-topics-subscriptions/select-topic.png" alt-text="トピックの選択":::
    1. **[Service Bus トピック]** ページ下部の **[Metrics]\(メトリック\)** セクションの **[メッセージ]** グラフで、トピックに 3 つの受信メッセージがあることがわかります。 値が表示されない場合は、数分待ってページを最新の情報に更新すると、最新のグラフが表示されます。 

        :::image type="content" source="./media/service-bus-dotnet-how-to-use-topics-subscriptions/sent-messages-essentials.png" alt-text="トピックに送信されたメッセージ" lightbox="./media/service-bus-dotnet-how-to-use-topics-subscriptions/sent-messages-essentials.png":::
    4. 下のペインでサブスクリプションを選択します。 次の例では、**S1** になっています。 **[Service Bus Subscription]\(Service Bus サブスクリプション\)** ページには、 **[アクティブなメッセージ数]** が **3** と表示されます。 トピックに送信した 3 つのメッセージはサブスクリプションで受信されています。しかし、それらはどの受信者によっても取り出されていません。 
    
        :::image type="content" source="./media/service-bus-dotnet-how-to-use-topics-subscriptions/subscription-page.png" alt-text="サブスクリプションで受信されたメッセージ" lightbox="./media/service-bus-dotnet-how-to-use-topics-subscriptions/subscription-page.png":::
    
## <a name="receive-messages-from-a-subscription"></a>サブスクリプションからメッセージを受信する
このセクションでは、サブスクリプションから Service Bus トピックへのメッセージを受信する .NET Core コンソール アプリケーションを作成します。 

### <a name="create-a-project-for-the-receiver"></a>受信側のプロジェクトを作成する

1. ソリューション エクスプローラー ウィンドウで、 **[ServiceBusTopicQuickStart]** ソリューションを右クリックし、 **[追加]** をポイントして、 **[新しいプロジェクト]** を選択します。 
1. **[コンソール アプリケーション]** を選択し、 **[次へ]** を選択します。 
1. **[プロジェクト名]** に「**SubscriptionReceiver**」と入力し、 **[次へ]** を選択します。 
1. **[追加情報]** ページで **[作成]** を選択します。 
1. **ソリューション エクスプローラー** ウィンドウで、 **[SubscriptionReceiver]** を右クリックし、 **[Set as a Startup Project]\(スタートアップ プロジェクトとして設定\)** を選択します。 

### <a name="add-the-service-bus-nuget-package"></a>Service Bus NuGet パッケージの追加

1. メニューから **[ツール]**  >  **[NuGet パッケージ マネージャー]**  >  **[パッケージ マネージャー コンソール]** の順に選択します。 
1. **[パッケージ マネージャー コンソール]** ウィンドウで、 **[既定のプロジェクト]** に **[SubscriptionReceiver]** が選択されていることを確認します。 そうでない場合は、ドロップダウン リストを使用して **[SubscriptionReceiver]** を選択します。
1. 次のコマンドを実行して、**Azure.Messaging.ServiceBus** NuGet パッケージをインストールします。

    ```cmd
    Install-Package Azure.Messaging.ServiceBus
    ```

### <a name="add-code-to-receive-messages-from-the-subscription"></a>サブスクリプションからメッセージを受信するコードを追加する
1. **Program.cs** のコードを次のコードに置き換えます。 コードの重要な手順を次に示します。
    コードの重要な手順を次に示します。
    1. 名前空間への接続文字列を使用して [ServiceBusClient](/dotnet/api/azure.messaging.servicebus.servicebusclient) オブジェクトを作成します。 
    1. `ServiceBusClient` オブジェクトで `CreateProcessor` メソッドを呼び出し、指定した Service Bus キューに対して [ServiceBusProcessor](/dotnet/api/azure.messaging.servicebus.servicebusprocessor) オブジェクトを作成します。 
    1. `ServiceBusProcessor` オブジェクトの `ProcessMessageAsync` および `ProcessErrorAsync` イベントのハンドラーを指定します。 
    1. `ServiceBusProcessor` オブジェクトで `StartProcessingAsync` を呼び出して、メッセージの処理を開始します。 
    1. ユーザーがキーを押して処理を終了すると、`ServiceBusProcessor` オブジェクトで `StopProcessingAsync` が呼び出されます。 

        詳細については、コードのコメントを参照してください。

        ```csharp
        using System;
        using System.Threading.Tasks;
        using Azure.Messaging.ServiceBus;
        
        namespace SubscriptionReceiver
        {
            class Program
            {
                // connection string to your Service Bus namespace
                static string connectionString = "<NAMESPACE CONNECTION STRING>";
        
                // name of the Service Bus topic
                static string topicName = "<SERVICE BUS TOPIC NAME>";
            
                // name of the subscription to the topic
                static string subscriptionName = "<SERVICE BUS - TOPIC SUBSCRIPTION NAME>";
        
                // the client that owns the connection and can be used to create senders and receivers
                static ServiceBusClient client;
        
                // the processor that reads and processes messages from the subscription
                static ServiceBusProcessor processor;
        
                // handle received messages
                static async Task MessageHandler(ProcessMessageEventArgs args)
                {
                    string body = args.Message.Body.ToString();
                    Console.WriteLine($"Received: {body} from subscription: {subscriptionName}");
        
                    // complete the message. messages is deleted from the subscription. 
                    await args.CompleteMessageAsync(args.Message);
                }
        
                // handle any errors when receiving messages
                static Task ErrorHandler(ProcessErrorEventArgs args)
                {
                    Console.WriteLine(args.Exception.ToString());
                    return Task.CompletedTask;
                }
        
                static async Task Main()
                {
                    // The Service Bus client types are safe to cache and use as a singleton for the lifetime
                    // of the application, which is best practice when messages are being published or read
                    // regularly.
                    //
                    // Create the clients that we'll use for sending and processing messages.
                    client = new ServiceBusClient(connectionString);
        
                    // create a processor that we can use to process the messages
                    processor = client.CreateProcessor(topicName, subscriptionName, new ServiceBusProcessorOptions());
        
                    try
                    {
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
                    finally
                    {
                        // Calling DisposeAsync on client types is required to ensure that network
                        // resources and other unmanaged objects are properly cleaned up.
                        await processor.DisposeAsync();
                        await client.DisposeAsync();
                    }
                }
            }
        }
        ```
1. プレースホルダーを適切な値に置き換えます。
    - `<NAMESPACE CONNECTION STRING>`: Service Bus 名前空間の接続文字列
    - `<TOPIC NAME>`: Service Bus トピックの名前
    - `<SERVICE BUS - TOPIC SUBSCRIPTION NAME>`: トピックのサブスクリプションの名前。 
1. プロジェクトをビルドし、エラーがないことを確認します。
1. 受信側アプリを実行します。 受信メッセージが表示されます。 任意のキーを押して、受信側とアプリケーションを停止します。 

    ```console
    Wait for a minute and then press any key to end the processing
    Received: Message 1 from subscription: S1
    Received: Message 2 from subscription: S1
    Received: Message 3 from subscription: S1
    
    Stopping the receiver...
    Stopped receiving messages
    ```
1. もう一度ポータルを確認します。 
    - **[Service Bus トピック]** ページの **[メッセージ]** グラフに、3 つの受信メッセージと 3 つの送信メッセージが表示されます。 これらの数値が表示されない場合は、数分待ってページを最新の情報に更新すると、最新のグラフが表示されます。 
    
        :::image type="content" source="./media/service-bus-dotnet-how-to-use-topics-subscriptions/messages-size-final.png" alt-text="送受信されたメッセージ" lightbox="./media/service-bus-dotnet-how-to-use-topics-subscriptions/messages-size-final.png":::
    - **[Service Bus Subscription]\(Service Bus サブスクリプション\)** ページには、 **[アクティブなメッセージ数]** がゼロと表示されます。 これは受信者が、このサブスクリプションからのメッセージを受け取って、そのメッセージを完了したためです。 
    
        :::image type="content" source="./media/service-bus-dotnet-how-to-use-topics-subscriptions/subscription-page-final.png" alt-text="サブスクリプション側におけるアクティブなメッセージ数 (処理後)" lightbox="./media/service-bus-dotnet-how-to-use-topics-subscriptions/subscription-page-final.png":::
        


## <a name="next-steps"></a>次のステップ
次のドキュメントおよびサンプルを参照してください。

- [.NET 用の Azure Service Bus クライアント ライブラリ - Readme](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/servicebus/Azure.Messaging.ServiceBus)
- [GitHub にある Azure Service Bus 用の .NET サンプル](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/servicebus/Azure.Messaging.ServiceBus/samples)
- [.NET API リファレンス](/dotnet/api/azure.messaging.servicebus?preserve-view=true)
