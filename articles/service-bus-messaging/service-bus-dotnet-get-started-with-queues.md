---
title: Azure Service Bus キューの概要 (Azure.Messaging.ServiceBus)
description: このチュートリアルでは、.NET Core C# アプリケーションを作成して、Service Bus キューとの間でメッセージを送受信します。
ms.topic: quickstart
ms.tgt_pltfrm: dotnet
ms.date: 10/11/2021
ms.custom: contperf-fy22q2
ms.openlocfilehash: c7e0ddee8e42f76c034ce79c7200e48fcb8bae04
ms.sourcegitcommit: 05c8e50a5df87707b6c687c6d4a2133dc1af6583
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/16/2021
ms.locfileid: "132555909"
---
# <a name="get-started-with-azure-service-bus-queues-net"></a>Azure Service Bus キューの使用を開始する (.NET)
このクイックスタートでは、次の手順を実行します。

1. Azure Portal を使用して Service Bus 名前空間を作成する。
2. Azure Portal を使用して Service Bus キューを作成する。
3. キューに一連のメッセージを送信するための .NET Core コンソール アプリケーションを作成する。
4. それらのメッセージをキューから受信する .NET Core コンソール アプリケーションを作成する。

> [!NOTE]
> このクイックスタートでは、メッセージのバッチを Service Bus キューに送信し、それらを受信するという簡単なシナリオでのステップ バイ ステップの手順を説明します。 Azure Service Bus の事前構築済みの .NET サンプルが、[GitHub の Azure SDK for .NET リポジトリ](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/servicebus/Azure.Messaging.ServiceBus/samples)に用意されています。 

## <a name="prerequisites"></a>前提条件
このサービスを初めて使用する場合は、このクイックスタートを実行する前に、<bpt id="p1">[</bpt>Service Bus の概要<ept id="p1">](service-bus-messaging-overview.md)</ept>に関する記事を参照してください。 

- **Azure サブスクリプション**。 Azure Service Bus など、Azure の各種サービスを使用するには、サブスクリプションが必要です。 Azure アカウントを持っていない場合、[無料試用版](https://azure.microsoft.com/free/)でサインアップできます。
- **Microsoft Visual Studio 2019**。 Azure Service Bus クライアント ライブラリでは、C# 8.0 で導入された新機能を使用します。  以前のバージョンの C# 言語でライブラリを使用することもできますが、新しい構文は使用できません。 完全な構文を使用するには、.NET Core SDK 3.0 以上で、言語バージョンを `latest` に設定してコンパイルすることをお勧めします。 Visual Studio を使用している場合、Visual Studio 2019 より前のバージョンには、C# 8.0 プロジェクトをビルドするために必要なツールとの互換性がありません。


[!INCLUDE [service-bus-create-namespace-portal](./includes/service-bus-create-namespace-portal.md)]

[!INCLUDE [service-bus-create-queue-portal](./includes/service-bus-create-queue-portal.md)]


## <a name="send-messages-to-the-queue"></a>キューへのメッセージの送信
このセクションでは、Service Bus キューにメッセージを送信する .NET Core コンソール アプリケーションを作成する方法を説明します。 

### <a name="create-a-console-application"></a>コンソール アプリケーションの作成

1. Visual Studio 2019 を起動します。 
1. **[新しいプロジェクトの作成]** を選択します。 
1. <bpt id="p1">**</bpt>[新しいプロジェクトの作成]<ept id="p1">**</ept> ダイアログ ボックスで、次の手順に従います。このダイアログ ボックスが表示されない場合は、メニューで <bpt id="p2">**</bpt>[ファイル]<ept id="p2">**</ept> 、 <bpt id="p3">**</bpt>[新規]<ept id="p3">**</ept> 、 <bpt id="p4">**</bpt>[プロジェクト]<ept id="p4">**</ept> の順に選択します。 
    1. プログラミング言語として <bpt id="p1">**</bpt>[C#]<ept id="p1">**</ept> を選択します。
    1. アプリケーションの種類として <bpt id="p1">**</bpt>[コンソール]<ept id="p1">**</ept> を選択します。 
    1. 結果リストから <bpt id="p1">**</bpt>[コンソール アプリケーション]<ept id="p1">**</ept> を選択します。 
    1. 次に、 **[次へ]** を選択します。 

        :::image type="content" source="./media/service-bus-dotnet-get-started-with-queues/new-send-project.png" alt-text="[C#] と [コンソール] が選択された [新しいプロジェクトの作成] ダイアログ ボックスを示す画像":::
1. プロジェクト名に「**QueueSender**」、ソリューション名に「**ServiceBusQueueQuickStart**」と入力し、 **[次へ]** を選択します。 

    :::image type="content" source="./media/service-bus-dotnet-get-started-with-queues/project-solution-names.png" alt-text="[新しいプロジェクトの構成] ダイアログ ボックスのソリューション名とプロジェクト名を示す画像":::
1. **[追加情報]** ページで、 **[作成]** を選択してソリューションとプロジェクトを作成します。 

### <a name="add-the-service-bus-nuget-package"></a>Service Bus NuGet パッケージの追加

1. メニューから <bpt id="p1">**</bpt>[ツール]<ept id="p1">**</ept> <ph id="ph1"> > </ph> <bpt id="p2">**</bpt>[NuGet パッケージ マネージャー]<ept id="p2">**</ept> <ph id="ph2"> > </ph> <bpt id="p3">**</bpt>[パッケージ マネージャー コンソール]<ept id="p3">**</ept> の順に選択します。 
1. 次のコマンドを実行して、**Azure.Messaging.ServiceBus** NuGet パッケージをインストールします。

    ```cmd
    Install-Package Azure.Messaging.ServiceBus
    ```

### <a name="add-code-to-send-messages-to-the-queue"></a>キューにメッセージを送信するコードを追加する

1. **Program.cs** 内の名前空間定義の先頭 (クラス宣言の前) に、次の `using` ステートメントを追加します。

    ```csharp
    using System.Threading.Tasks;
    using Azure.Messaging.ServiceBus;
    ```
2. `Program` クラス内で、`Main` メソッドの直前に次のプロパティを宣言します。 

    `<NAMESPACE CONNECTION STRING>` を Service Bus 名前空間のプライマリ接続文字列に置き換えます。 また、`<QUEUE NAME>` を実際のキューの名前に置き換えます。

    ```csharp

    // connection string to your Service Bus namespace
    static string connectionString = "<NAMESPACE CONNECTION STRING>";

    // name of your Service Bus queue
    static string queueName = "<QUEUE NAME>";

    // the client that owns the connection and can be used to create senders and receivers
    static ServiceBusClient client;

    // the sender used to publish messages to the queue
    static ServiceBusSender sender;

    // number of messages to be sent to the queue
    private const int numOfMessages = 3;

    ```
3. `Main` メソッドのコードを次のコードに置き換えます。 コードの詳細については、コードのコメントを参照してください。 コードの重要な手順を次に示します。  
    1. 名前空間へのプライマリ接続文字列を使用して [ServiceBusClient](/dotnet/api/azure.messaging.servicebus.servicebusclient) オブジェクトを作成します。 
    1. [ServiceBusClient](/dotnet/api/azure.messaging.servicebus.servicebusclient) オブジェクトの [CreateSender](/dotnet/api/azure.messaging.servicebus.servicebusclient.createsender) メソッドを呼び出して、特定の Service Bus キュー用の [ServiceBusSender](/dotnet/api/azure.messaging.servicebus.servicebussender) オブジェクトを作成します。     
    1. [ServiceBusSender.CreateMessageBatchAsync](/dotnet/api/azure.messaging.servicebus.servicebussender.createmessagebatchasync) メソッドを使用して [ServiceBusMessageBatch](/dotnet/api/azure.messaging.servicebus.servicebusmessagebatch) オブジェクトを作成します。
    1. [ServiceBusMessageBatch.TryAddMessage](/dotnet/api/azure.messaging.servicebus.servicebusmessagebatch.tryaddmessage) を使用して、メッセージをバッチに追加します。 
    1. [ServiceBusSender.SendMessagesAsync](/dotnet/api/azure.messaging.servicebus.servicebussender.sendmessagesasync) メソッドを使用してメッセージのバッチを Service Bus キューに送信します。

        ```csharp
        static async Task Main()
        {
            // The Service Bus client types are safe to cache and use as a singleton for the lifetime
            // of the application, which is best practice when messages are being published or read
            // regularly.
            //
            // Create the clients that we'll use for sending and processing messages.
            client = new ServiceBusClient(connectionString);
            sender = client.CreateSender(queueName);
    
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
                // Use the producer client to send the batch of messages to the Service Bus queue
                await sender.SendMessagesAsync(messageBatch);
                Console.WriteLine($"A batch of {numOfMessages} messages has been published to the queue.");
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
        ```    
1. Program.cs ファイルは次のようになります。 
    
    ```csharp
    using System;
    using System.Threading.Tasks;
    using Azure.Messaging.ServiceBus;
    
    namespace QueueSender
    {
        class Program
        {
            // connection string to your Service Bus namespace
            static string connectionString = "<NAMESPACE CONNECTION STRING>";

            // name of your Service Bus queue
            static string queueName = "<QUEUE NAME>";
    
            // the client that owns the connection and can be used to create senders and receivers
            static ServiceBusClient client;
    
            // the sender used to publish messages to the queue
            static ServiceBusSender sender;
    
            // number of messages to be sent to the queue
            private const int numOfMessages = 3;
    
            static async Task Main()
            {
                // The Service Bus client types are safe to cache and use as a singleton for the lifetime
                // of the application, which is best practice when messages are being published or read
                // regularly.
                //
                // Create the clients that we'll use for sending and processing messages.
                client = new ServiceBusClient(connectionString);
                sender = client.CreateSender(queueName);
    
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
                    // Use the producer client to send the batch of messages to the Service Bus queue
                    await sender.SendMessagesAsync(messageBatch);
                    Console.WriteLine($"A batch of {numOfMessages} messages has been published to the queue.");
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
1. `<NAMESPACE CONNECTION STRING>` を Service Bus 名前空間のプライマリ接続文字列に置き換えます。 また、`<QUEUE NAME>` を実際のキューの名前に置き換えます。
1. プロジェクトをビルドし、エラーがないことを確認します。 
1. プログラムを実行し、確認メッセージが表示されるまで待ちます。
    
    ```bash
    A batch of 3 messages has been published to the queue
    ```
1. Azure portal で次の手順を実行します。
    1. 自分の Service Bus 名前空間に移動します。 
    1. **[概要]** ページ中央下のペインでキューを選択します。 
    
        :::image type="content" source="./media/service-bus-dotnet-get-started-with-queues/select-queue.png" alt-text="キューが選択された、Azure portal の [Service Bus 名前空間] ページを示す画像。" lightbox="./media/service-bus-dotnet-get-started-with-queues/select-queue.png":::
    1. **[要点]** セクションの値に注目します。

    :::image type="content" source="./media/service-bus-dotnet-get-started-with-queues/sent-messages-essentials.png" alt-text="受信したメッセージの数とキューのサイズを示す画像" lightbox="./media/service-bus-dotnet-get-started-with-queues/sent-messages-essentials.png":::

    次の値に注目してください。
    - キューの **[アクティブ]** メッセージ数の値は、現在 **3** です。 メッセージを取得せずにこの送信側アプリを実行するたびに、この値が 3 ずつ増えます。
    - アプリからキューにメッセージを追加するたびに、キューの **[現在のサイズ]** が増加します。
    - 下部の **[メトリック]** セクションの **[メッセージ]** グラフでは、キューに 3 つの受信メッセージがあることがわかります。 

## <a name="receive-messages-from-the-queue"></a>キューからメッセージを受け取る
このセクションでは、キューからメッセージを受信する .NET Core コンソール アプリケーションを作成します。 

### <a name="create-a-project-for-the-receiver"></a>受信側のプロジェクトを作成する

1. ソリューション エクスプローラー ウィンドウで、 **[ServiceBusQueueQuickStart]** ソリューションを右クリックし、 **[追加]** をポイントして、 **[新しいプロジェクト]** を選択します。 
1. **[コンソール アプリケーション]** を選択し、 **[次へ]** を選択します。 
1. **[プロジェクト名]** に「**QueueReceiver**」と入力し、 **[作成]** を選択します。 
1. **ソリューション エクスプローラー** ウィンドウで、 **[QueueReceiver]** を右クリックし、 **[Set as a Startup Project]\(スタートアップ プロジェクトとして設定\)** を選択します。 

### <a name="add-the-service-bus-nuget-package"></a>Service Bus NuGet パッケージの追加

1. メニューから **[ツール]**  >  **[NuGet パッケージ マネージャー]**  >  **[パッケージ マネージャー コンソール]** の順に選択します。 
1. **[パッケージ マネージャー コンソール]** ウィンドウで、 **[既定のプロジェクト]** に **QueueReceiver** が選択されていることを確認します。 選択されていない場合は、ドロップダウン リストを使用して **QueueReceiver** を選択します。

    :::image type="content" source="./media/service-bus-dotnet-get-started-with-queues/package-manager-console.png" alt-text="QueueReceiver のスクリーンショット。パッケージ マネージャー コンソールで選択されています":::
1. 次のコマンドを実行して、**Azure.Messaging.ServiceBus** NuGet パッケージをインストールします。

    ```cmd
    Install-Package Azure.Messaging.ServiceBus
    ```

### <a name="add-the-code-to-receive-messages-from-the-queue"></a>キューからメッセージを受信するコードを追加する
このセクションでは、キューからメッセージを取得するコードを追加します。

1. **Program.cs** 内の名前空間定義の先頭 (クラス宣言の前) に、次の `using` ステートメントを追加します。

    ```csharp
    using System.Threading.Tasks;
    using Azure.Messaging.ServiceBus;
    ```
2. `Program` クラス内で、`Main` メソッドの直前に次のプロパティを宣言します。 

    `<NAMESPACE CONNECTION STRING>` を Service Bus 名前空間のプライマリ接続文字列に置き換えます。 また、`<QUEUE NAME>` を実際のキューの名前に置き換えます。

    ```csharp
    // connection string to your Service Bus namespace
    static string connectionString = "<NAMESPACE CONNECTION STRING>";

    // name of your Service Bus queue
    static string queueName = "<QUEUE NAME>";

    // the client that owns the connection and can be used to create senders and receivers
    static ServiceBusClient client;

    // the processor that reads and processes messages from the queue
    static ServiceBusProcessor processor;
    ```
3. 受信したメッセージや発生したエラーを処理するために、次のメソッドを `Program` クラスに追加します。 

    ```csharp
    // handle received messages
    static async Task MessageHandler(ProcessMessageEventArgs args)
    {
        string body = args.Message.Body.ToString();
        Console.WriteLine($"Received: {body}");

        // complete the message. messages is deleted from the queue. 
        await args.CompleteMessageAsync(args.Message);
    }

    // handle any errors when receiving messages
    static Task ErrorHandler(ProcessErrorEventArgs args)
    {
        Console.WriteLine(args.Exception.ToString());
        return Task.CompletedTask;
    }
    ```
4. `Main` メソッドのコードを次のコードに置き換えます。 コードの詳細については、コードのコメントを参照してください。 コードの重要な手順を次に示します。 
    1. 名前空間へのプライマリ接続文字列を使用して [ServiceBusClient](/dotnet/api/azure.messaging.servicebus.servicebusclient) オブジェクトを作成します。 
    1. [ServiceBusClient](/dotnet/api/azure.messaging.servicebus.servicebusclient) オブジェクトの [CreateProcessor](/dotnet/api/azure.messaging.servicebus.servicebusclient.createprocessor) メソッドを呼び出し、指定した Service Bus キュー用の [ServiceBusProcessor](/dotnet/api/azure.messaging.servicebus.servicebusprocessor) オブジェクトを作成します。 
    1. [ServiceBusProcessor](/dotnet/api/azure.messaging.servicebus.servicebusprocessor) オブジェクトの [ProcessMessageAsync](/dotnet/api/azure.messaging.servicebus.servicebusprocessor.processmessageasync) および [ProcessErrorAsync](/dotnet/api/azure.messaging.servicebus.servicebusprocessor.processerrorasync) の各イベントのハンドラーを指定します。 
    1. [ServiceBusProcessor](/dotnet/api/azure.messaging.servicebus.servicebusprocessor) オブジェクトの [StartProcessingAsync](/dotnet/api/azure.messaging.servicebus.servicebusprocessor.startprocessingasync) を呼び出して、メッセージの処理を開始します。 
    1. ユーザーが任意のキーを押して処理を終了すると、[ServiceBusProcessor](/dotnet/api/azure.messaging.servicebus.servicebusprocessor) オブジェクトの [StopProcessingAsync](/dotnet/api/azure.messaging.servicebus.servicebusprocessor.stopprocessingasync) が呼び出されます。 

        詳細については、コードのコメントを参照してください。

        ```csharp
                static async Task Main()
                {
                    // The Service Bus client types are safe to cache and use as a singleton for the lifetime
                    // of the application, which is best practice when messages are being published or read
                    // regularly.
                    //
        
                    // Create the client object that will be used to create sender and receiver objects
                    client = new ServiceBusClient(connectionString);
        
                    // create a processor that we can use to process the messages
                    processor = client.CreateProcessor(queueName, new ServiceBusProcessorOptions());
        
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
        ```
4. `Program.cs` は次のようになります。  

    ```csharp
    using System;
    using System.Threading.Tasks;
    using Azure.Messaging.ServiceBus;
    
    namespace QueueReceiver
    {
        class Program
        {
            // connection string to your Service Bus namespace
            static string connectionString = "<NAMESPACE CONNECTION STRING>";
    
            // name of your Service Bus queue
            static string queueName = "<QUEUE NAME>";
    
    
            // the client that owns the connection and can be used to create senders and receivers
            static ServiceBusClient client;
    
            // the processor that reads and processes messages from the queue
            static ServiceBusProcessor processor;
    
            // handle received messages
            static async Task MessageHandler(ProcessMessageEventArgs args)
            {
                string body = args.Message.Body.ToString();
                Console.WriteLine($"Received: {body}");
    
                // complete the message. messages is deleted from the queue. 
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
    
                // Create the client object that will be used to create sender and receiver objects
                client = new ServiceBusClient(connectionString);
    
                // create a processor that we can use to process the messages
                processor = client.CreateProcessor(queueName, new ServiceBusProcessorOptions());
    
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
1. `<NAMESPACE CONNECTION STRING>` を Service Bus 名前空間のプライマリ接続文字列に置き換えます。 また、`<QUEUE NAME>` を実際のキューの名前に置き換えます。 
1. プロジェクトをビルドし、エラーがないことを確認します。
1. 受信側アプリを実行します。 受信メッセージが表示されます。 任意のキーを押して、受信側とアプリケーションを停止します。 

    ```console
    Wait for a minute and then press any key to end the processing
    Received: Message 1
    Received: Message 2
    Received: Message 3
    
    Stopping the receiver...
    Stopped receiving messages
    ```
1. もう一度ポータルを確認します。 **[アクティブ]** メッセージ数に `0` と表示されていない場合は、数分待ってからページを最新の情報に更新します。 

    - **[アクティブ]** メッセージ数と **[現在のサイズ]** の値が **0** になりました。
    - 下部の **[メトリック]** セクションの **[メッセージ]** グラフを見ると、このキューには 3 つの受信メッセージと 3 つの送信メッセージがあることがわかります。 
    
        :::image type="content" source="./media/service-bus-dotnet-get-started-with-queues/queue-messages-size-final.png" alt-text="受信後のアクティブなメッセージとサイズ" lightbox="./media/service-bus-dotnet-get-started-with-queues/queue-messages-size-final.png":::


## <a name="next-steps"></a>次のステップ
次のドキュメントおよびサンプルを参照してください。

- [.NET 用の Azure Service Bus クライアント ライブラリ - Readme](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/servicebus/Azure.Messaging.ServiceBus)
- [GitHub のサンプル](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/servicebus/Azure.Messaging.ServiceBus/samples)
- [.NET API リファレンス](/dotnet/api/azure.messaging.servicebus)
