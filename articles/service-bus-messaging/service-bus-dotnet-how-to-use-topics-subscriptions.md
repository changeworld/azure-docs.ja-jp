---
title: Azure Service Bus のトピックとサブスクリプションの概要
description: このクイックスタートでは、azure-messaging-servicebus パッケージを使用して、Azure Service Bus トピックにメッセージを送信する方法について説明します。
ms.topic: quickstart
ms.tgt_pltfrm: dotnet
ms.date: 10/11/2021
ms.custom: contperf-fy22q2
ms.openlocfilehash: 05efcd742b4b5a70925cdd6bd8fad62a61ba4a53
ms.sourcegitcommit: 54e7b2e036f4732276adcace73e6261b02f96343
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/12/2021
ms.locfileid: "129807487"
---
# <a name="get-started-with-azure-service-bus-topics-and-subscriptions"></a>Azure Service Bus のトピックとサブスクリプションの概要
このクイックスタートでは、[Azure.Messaging.ServiceBus](https://www.nuget.org/packages/Azure.Messaging.ServiceBus/) .NET ライブラリを使用して、Service Bus トピックにメッセージを送信し、トピックへのサブスクリプションからメッセージを受信する方法について説明します。

このクイックスタートでは、次の手順を実行します。

1. Azure Portal を使用して Service Bus 名前空間を作成する。
2. Azure Portal を使用して Service Bus トピックを作成する。
3. そのトピックに対する Service Bus サブスクリプションを Azure Portal で作成する。
4. トピックに一連のメッセージを送信するための .NET Core コンソール アプリケーションを作成する。
5. それらのメッセージをサブスクリプションから受信する .NET Core コンソール アプリケーションを作成する。

> [!NOTE]
> このクイック スタートでは、メッセージのバッチを Service Bus トピックに送信し、それらのメッセージをトピックのサブスクリプションから受信するという単純なシナリオの手順を説明します。 Azure Service Bus の事前構築済みの .NET サンプルは、[GitHub の Azure SDK for .NET リポジトリ](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/servicebus/Azure.Messaging.ServiceBus/samples)にあります。 

## <a name="prerequisites"></a>前提条件
このサービスを初めて使用する場合は、このクイックスタートを実行する前に、<bpt id="p1">[</bpt>Service Bus の概要<ept id="p1">](service-bus-messaging-overview.md)</ept>に関する記事を参照してください。 

- **Azure サブスクリプション**。 Azure Service Bus など、Azure の各種サービスを使用するには、サブスクリプションが必要です。  既存の Microsoft Azure アカウントをお持ちでない場合は、<bpt id="p2">[</bpt>アカウントを作成する<ept id="p2">](https://azure.microsoft.com)</ept>際に、<bpt id="p1">[</bpt>無料試用版<ept id="p1">](https://azure.microsoft.com/free/)</ept>にサインアップするか、MSDN サブスクライバー特典を利用できます。
- <bpt id="p1">**</bpt>Microsoft Visual Studio 2019<ept id="p1">**</ept>。 Azure Service Bus クライアント ライブラリでは、C# 8.0 で導入された新機能を使用します。  以前のバージョンの C# 言語でライブラリを使用することもできますが、新しい構文は使用できません。 完全な構文を使用するには、<bpt id="p1">[</bpt>.NET Core SDK<ept id="p1">](https://dotnet.microsoft.com/download)</ept> 3.0 以上で、<bpt id="p2">[</bpt>言語バージョン<ept id="p2">](/dotnet/csharp/language-reference/configure-language-version#override-a-default)</ept>を <ph id="ph1">`latest`</ph> に設定してコンパイルすることをお勧めします。 Visual Studio を使用している場合、Visual Studio 2019 より前のバージョンには、C# 8.0 プロジェクトをビルドするために必要なツールとの互換性がありません。 無料の Community エディションを含む Visual Studio 2019 は、<bpt id="p1">[</bpt>こちら<ept id="p1">](https://visualstudio.microsoft.com/vs/)</ept>からダウンロードできます。

[!INCLUDE [service-bus-create-namespace-portal](./includes/service-bus-create-namespace-portal.md)]

[!INCLUDE [service-bus-create-topic-subscription-portal](./includes/service-bus-create-topic-subscription-portal.md)]

> [!IMPORTANT]
> 名前空間への接続文字列、トピック名、サブスクリプション名を書き留めてください。 このチュートリアルで後ほど使用します。

## <a name="send-messages-to-the-topic"></a>トピックにメッセージを送信する
このセクションでは、Service Bus トピックにメッセージを送信する .NET Core コンソール アプリケーションを作成する方法を説明します。 

### <a name="create-a-console-application"></a>コンソール アプリケーションの作成

1. Visual Studio 2019 を起動します。 
1. **[新しいプロジェクトの作成]** を選択します。 
1. <bpt id="p1">**</bpt>[新しいプロジェクトの作成]<ept id="p1">**</ept> ダイアログ ボックスで、次の手順に従います。このダイアログ ボックスが表示されない場合は、メニューで <bpt id="p2">**</bpt>[ファイル]<ept id="p2">**</ept> 、 <bpt id="p3">**</bpt>[新規]<ept id="p3">**</ept> 、 <bpt id="p4">**</bpt>[プロジェクト]<ept id="p4">**</ept> の順に選択します。 
    1. プログラミング言語として <bpt id="p1">**</bpt>[C#]<ept id="p1">**</ept> を選択します。
    1. アプリケーションの種類として <bpt id="p1">**</bpt>[コンソール]<ept id="p1">**</ept> を選択します。 
    1. 結果リストから <bpt id="p1">**</bpt>[コンソール アプリケーション]<ept id="p1">**</ept> を選択します。 
    1. 次に、 <bpt id="p1">**</bpt>[次へ]<ept id="p1">**</ept> を選択します。 

        <bpt id="p1">:::image type="content" source="./media/service-bus-dotnet-get-started-with-queues/new-send-project.png" alt-text="</bpt>[C#] と [コンソール] が選択された [新しいプロジェクトの作成] ダイアログ ボックスを示す画像<ept id=&quot;p1&quot;>":::</ept>
1. プロジェクト名に「<bpt id="p1">**</bpt>TopicSender<ept id="p1">**</ept>」、ソリューション名に「<bpt id="p2">**</bpt>ServiceBusTopicQuickStart<ept id="p2">**</ept>」と入力し、 <bpt id="p3">**</bpt>[次へ]<ept id="p3">**</ept> を選択します。 
1. <bpt id="p1">**</bpt>[追加情報]<ept id="p1">**</ept> ページで、 <bpt id="p2">**</bpt>[作成]<ept id="p2">**</ept> を選択してソリューションとプロジェクトを作成します。

### <a name="add-the-service-bus-nuget-package"></a>Service Bus NuGet パッケージの追加

1. メニューから <bpt id="p1">**</bpt>[ツール]<ept id="p1">**</ept> <ph id="ph1"> > </ph> <bpt id="p2">**</bpt>[NuGet パッケージ マネージャー]<ept id="p2">**</ept> <ph id="ph2"> > </ph> <bpt id="p3">**</bpt>[パッケージ マネージャー コンソール]<ept id="p3">**</ept> の順に選択します。 
1. 次のコマンドを実行して、<bpt id="p1">[</bpt>Azure.Messaging.ServiceBus<ept id="p1">](https://www.nuget.org/packages/Azure.Messaging.ServiceBus/)</ept> NuGet パッケージをインストールします。

    ```cmd
    Install-Package Azure.Messaging.ServiceBus
    ```

### <a name="add-code-to-send-messages-to-the-topic"></a>トピックにメッセージを送信するためのコードを追加する 

1. **Program.cs** 内の名前空間定義の先頭 (クラス宣言の前) に、次の `using` ステートメントを追加します。

    ```csharp
    using System.Threading.Tasks;
    using Azure.Messaging.ServiceBus;
    ```
2. `Program` クラス内で、`Main` メソッドの直前に次のプロパティを宣言します。 `<NAMESPACE CONNECTION STRING>` を Service Bus 名前空間の接続文字列に置き換えます。 また、<ph id="ph1">`<TOPIC NAME>`</ph> を実際の Service Bus トピックの名前に置き換えます。

    ```csharp
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
    ```    
1. <bpt id="p1">**</bpt>Program.cs<ept id="p1">**</ept> のコードを次のコードに置き換えます。 コードの重要な手順を次に示します。  
    1. 名前空間への接続文字列を使用して <bpt id="p1">[</bpt>ServiceBusClient<ept id="p1">](/dotnet/api/azure.messaging.servicebus.servicebusclient)</ept> オブジェクトを作成します。 
    1. <ph id="ph1">`ServiceBusClient`</ph> オブジェクトで <bpt id="p1">[</bpt>CreateSender<ept id="p1">](/dotnet/api/azure.messaging.servicebus.servicebusclient.createsender)</ept> メソッドを呼び出して、特定の Service Bus トピックに対して <bpt id="p2">[</bpt>ServiceBusSender<ept id="p2">](/dotnet/api/azure.messaging.servicebus.servicebussender)</ept> オブジェクトを作成します。     
    1. <bpt id="p2">[</bpt>ServiceBusSender.CreateMessageBatchAsync<ept id="p2">](/dotnet/api/azure.messaging.servicebus.servicebussender.createmessagebatchasync)</ept> を使用して <bpt id="p1">[</bpt>ServiceBusMessageBatch<ept id="p1">](/dotnet/api/azure.messaging.servicebus.servicebusmessagebatch)</ept> オブジェクトを作成します。
    1. <bpt id="p1">[</bpt>ServiceBusMessageBatch.TryAddMessage<ept id="p1">](/dotnet/api/azure.messaging.servicebus.servicebusmessagebatch.tryaddmessage)</ept> を使用して、メッセージをバッチに追加します。 
    1. <bpt id="p1">[</bpt>ServiceBusSender.SendMessagesAsync<ept id="p1">](/dotnet/api/azure.messaging.servicebus.servicebussender.sendmessagesasync)</ept> メソッドを使用して、メッセージのバッチを Service Bus トピックに送信します。
    
    ```csharp
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
    ```
1. Program.cs ファイルは次のようになります。 
    
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
1. `<NAMESPACE CONNECTION STRING>` を Service Bus 名前空間の接続文字列に置き換えます。 また、<ph id="ph1">`<TOPIC NAME>`</ph> を実際の Service Bus トピックの名前に置き換えます。 
1. プロジェクトをビルドし、エラーがないことを確認します。 
1. プログラムを実行し、確認メッセージが表示されるまで待ちます。
    
    ```bash
    A batch of 3 messages has been published to the topic
    ```
1. Azure portal で次の手順を実行します。
    1. 自分の Service Bus 名前空間に移動します。 
    1. <bpt id="p1">**</bpt>[概要]<ept id="p1">**</ept> ページ中央下のペインで、 <bpt id="p2">**</bpt>[トピック]<ept id="p2">**</ept> タブに切り替えてから、Service Bus トピックを選択します。 次の例では、<ph id="ph1">`mytopic`</ph> になっています。
    
        <bpt id="p1">:::image type="content" source="./media/service-bus-dotnet-how-to-use-topics-subscriptions/select-topic.png" alt-text="</bpt>トピックの選択<ept id=&quot;p1&quot;>":::</ept>
    1. <bpt id="p1">**</bpt>[Service Bus トピック]<ept id="p1">**</ept> ページ下部の <bpt id="p3">**</bpt>[Metrics]\(メトリック\)<ept id="p3">**</ept> セクションの <bpt id="p2">**</bpt>[メッセージ]<ept id="p2">**</ept> グラフで、トピックに 3 つの受信メッセージがあることがわかります。 値が表示されない場合は、数分待ってページを最新の情報に更新すると、最新のグラフが表示されます。 

        <bpt id="p1">:::image type="content" source="./media/service-bus-dotnet-how-to-use-topics-subscriptions/sent-messages-essentials.png" alt-text="</bpt>トピックに送信されたメッセージ<ept id=&quot;p1&quot;>" lightbox="./media/service-bus-dotnet-how-to-use-topics-subscriptions/sent-messages-essentials.png":::</ept>
    4. 下のペインでサブスクリプションを選択します。 次の例では、<bpt id="p1">**</bpt>S1<ept id="p1">**</ept> になっています。 <bpt id="p1">**</bpt>[Service Bus Subscription]\(Service Bus サブスクリプション\)<ept id="p1">**</ept> ページには、 <bpt id="p2">**</bpt>[アクティブなメッセージ数]<ept id="p2">**</ept> が <bpt id="p3">**</bpt>3<ept id="p3">**</ept> と表示されます。 トピックに送信した 3 つのメッセージはサブスクリプションで受信されています。しかし、それらはどの受信者によっても取り出されていません。 
    
        <bpt id="p1">:::image type="content" source="./media/service-bus-dotnet-how-to-use-topics-subscriptions/subscription-page.png" alt-text="</bpt>サブスクリプションで受信されたメッセージ<ept id=&quot;p1&quot;>" lightbox="./media/service-bus-dotnet-how-to-use-topics-subscriptions/subscription-page.png":::</ept>
    
## <a name="receive-messages-from-a-subscription"></a>サブスクリプションからメッセージを受信する
このセクションでは、サブスクリプションから Service Bus トピックへのメッセージを受信する .NET Core コンソール アプリケーションを作成します。 

### <a name="create-a-project-for-the-receiver"></a>受信側のプロジェクトを作成する

1. ソリューション エクスプローラー ウィンドウで、 <bpt id="p1">**</bpt>[ServiceBusTopicQuickStart]<ept id="p1">**</ept> ソリューションを右クリックし、 <bpt id="p2">**</bpt>[追加]<ept id="p2">**</ept> をポイントして、 <bpt id="p3">**</bpt>[新しいプロジェクト]<ept id="p3">**</ept> を選択します。 
1. <bpt id="p1">**</bpt>[コンソール アプリケーション]<ept id="p1">**</ept> を選択し、 <bpt id="p2">**</bpt>[次へ]<ept id="p2">**</ept> を選択します。 
1. <bpt id="p2">**</bpt>[プロジェクト名]<ept id="p2">**</ept> に「<bpt id="p1">**</bpt>SubscriptionReceiver<ept id="p1">**</ept>」と入力し、 <bpt id="p3">**</bpt>[次へ]<ept id="p3">**</ept> を選択します。 
1. <bpt id="p1">**</bpt>[追加情報]<ept id="p1">**</ept> ページで <bpt id="p2">**</bpt>[作成]<ept id="p2">**</ept> を選択します。 
1. <bpt id="p1">**</bpt>ソリューション エクスプローラー<ept id="p1">**</ept> ウィンドウで、 <bpt id="p2">**</bpt>[SubscriptionReceiver]<ept id="p2">**</ept> を右クリックし、 <bpt id="p3">**</bpt>[Set as a Startup Project]\(スタートアップ プロジェクトとして設定\)<ept id="p3">**</ept> を選択します。 

### <a name="add-the-service-bus-nuget-package"></a>Service Bus NuGet パッケージの追加

1. メニューから <bpt id="p1">**</bpt>[ツール]<ept id="p1">**</ept> <ph id="ph1"> > </ph> <bpt id="p2">**</bpt>[NuGet パッケージ マネージャー]<ept id="p2">**</ept> <ph id="ph2"> > </ph> <bpt id="p3">**</bpt>[パッケージ マネージャー コンソール]<ept id="p3">**</ept> の順に選択します。 
1. <bpt id="p1">**</bpt>[パッケージ マネージャー コンソール]<ept id="p1">**</ept> ウィンドウで、 <bpt id="p3">**</bpt>[既定のプロジェクト]<ept id="p3">**</ept> に <bpt id="p2">**</bpt>[SubscriptionReceiver]<ept id="p2">**</ept> が選択されていることを確認します。 そうでない場合は、ドロップダウン リストを使用して <bpt id="p1">**</bpt>[SubscriptionReceiver]<ept id="p1">**</ept> を選択します。

    :::image type="content" source="./media/service-bus-dotnet-how-to-use-topics-subscriptions/select-subscription-receiver-project.png" alt-text="[パッケージ マネージャー コンソール] ウィンドウで [SubscriptionReceiver] プロジェクトが選択されていることを示している画像。" lightbox="./media/service-bus-dotnet-how-to-use-topics-subscriptions/select-subscription-receiver-project.png":::    
1. 次のコマンドを実行して、<bpt id="p1">**</bpt>Azure.Messaging.ServiceBus<ept id="p1">**</ept> NuGet パッケージをインストールします。

    ```cmd
    Install-Package Azure.Messaging.ServiceBus
    ```

### <a name="add-code-to-receive-messages-from-the-subscription"></a>サブスクリプションからメッセージを受信するコードを追加する
1. **Program.cs** 内の名前空間定義の先頭 (クラス宣言の前) に、次の `using` ステートメントを追加します。

    ```csharp
    using System.Threading.Tasks;
    using Azure.Messaging.ServiceBus;
    ```
2. `Program` クラス内で、`Main` メソッドの直前に次のプロパティを宣言します。 プレースホルダーを適切な値に置き換えます。
    - <ph id="ph1">`<NAMESPACE CONNECTION STRING>`</ph>: Service Bus 名前空間の接続文字列
    - <ph id="ph1">`<TOPIC NAME>`</ph>: Service Bus トピックの名前
    - <ph id="ph1">`<SERVICE BUS - TOPIC SUBSCRIPTION NAME>`</ph>: トピックのサブスクリプションの名前。 

    ```csharp
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
    ```
3. 受信したメッセージや発生したエラーを処理するために、次のメソッドを `Program` クラスに追加します。 

    ```csharp
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
    ```
1. <bpt id="p1">**</bpt>Program.cs<ept id="p1">**</ept> のコードを次のコードに置き換えます。 コードの重要な手順を次に示します。
    1. 名前空間への接続文字列を使用して <bpt id="p1">[</bpt>ServiceBusClient<ept id="p1">](/dotnet/api/azure.messaging.servicebus.servicebusclient)</ept> オブジェクトを作成します。 
    1. <ph id="ph1">`ServiceBusClient`</ph> オブジェクトで <bpt id="p1">[</bpt>CreateProcessor<ept id="p1">](/dotnet/api/azure.messaging.servicebus.servicebusclient.createprocessor)</ept> メソッドを呼び出し、指定した Service Bus キューに対して <bpt id="p2">[</bpt>ServiceBusProcessor<ept id="p2">](/dotnet/api/azure.messaging.servicebus.servicebusprocessor)</ept> オブジェクトを作成します。 
    1. <ph id="ph1">`ServiceBusProcessor`</ph> オブジェクトの <bpt id="p1">[</bpt>ProcessMessageAsync<ept id="p1">](/dotnet/api/azure.messaging.servicebus.servicebusprocessor.processmessageasync)</ept> および <bpt id="p2">[</bpt>ProcessErrorAsync<ept id="p2">](/dotnet/api/azure.messaging.servicebus.servicebusprocessor.processerrorasync)</ept> イベントのハンドラーを指定します。 
    1. <ph id="ph1">`ServiceBusProcessor`</ph> オブジェクトで <bpt id="p1">[</bpt>StartProcessingAsync<ept id="p1">](/dotnet/api/azure.messaging.servicebus.servicebusprocessor.startprocessingasync)</ept> を呼び出して、メッセージの処理を開始します。 
    1. ユーザーがキーを押して処理を終了すると、<ph id="ph1">`ServiceBusProcessor`</ph> オブジェクトで <bpt id="p1">[</bpt>StopProcessingAsync<ept id="p1">](/dotnet/api/azure.messaging.servicebus.servicebusprocessor.stopprocessingasync)</ept> が呼び出されます。 
    

    詳細については、コードのコメントを参照してください。

    ```csharp
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
    ```
1. `Program.cs` は次のようになります。  

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
    - <ph id="ph1">`<NAMESPACE CONNECTION STRING>`</ph>: Service Bus 名前空間の接続文字列
    - <ph id="ph1">`<TOPIC NAME>`</ph>: Service Bus トピックの名前
    - <ph id="ph1">`<SERVICE BUS - TOPIC SUBSCRIPTION NAME>`</ph>: トピックのサブスクリプションの名前。 
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
    - <bpt id="p1">**</bpt>[Service Bus トピック]<ept id="p1">**</ept> ページの <bpt id="p2">**</bpt>[メッセージ]<ept id="p2">**</ept> グラフに、3 つの受信メッセージと 3 つの送信メッセージが表示されます。 これらの数値が表示されない場合は、数分待ってページを最新の情報に更新すると、最新のグラフが表示されます。 
    
        <bpt id="p1">:::image type="content" source="./media/service-bus-dotnet-how-to-use-topics-subscriptions/messages-size-final.png" alt-text="</bpt>送受信されたメッセージ<ept id=&quot;p1&quot;>" lightbox="./media/service-bus-dotnet-how-to-use-topics-subscriptions/messages-size-final.png":::</ept>
    - <bpt id="p1">**</bpt>[Service Bus Subscription]\(Service Bus サブスクリプション\)<ept id="p1">**</ept> ページには、 <bpt id="p2">**</bpt>[アクティブなメッセージ数]<ept id="p2">**</ept> がゼロと表示されます。 これは受信者が、このサブスクリプションからのメッセージを受け取って、そのメッセージを完了したためです。 
    
        <bpt id="p1">:::image type="content" source="./media/service-bus-dotnet-how-to-use-topics-subscriptions/subscription-page-final.png" alt-text="</bpt>サブスクリプション側におけるアクティブなメッセージ数 (処理後)<ept id=&quot;p1&quot;>" lightbox="./media/service-bus-dotnet-how-to-use-topics-subscriptions/subscription-page-final.png":::</ept>
        


## <a name="next-steps"></a>次の手順
次のドキュメントおよびサンプルを参照してください。

- <bpt id="p1">[</bpt>.NET 用の Azure Service Bus クライアント ライブラリ - Readme<ept id="p1">](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/servicebus/Azure.Messaging.ServiceBus)</ept>
- <bpt id="p1">[</bpt>GitHub にある Azure Service Bus 用の .NET サンプル<ept id="p1">](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/servicebus/Azure.Messaging.ServiceBus/samples)</ept>
- <bpt id="p1">[</bpt>.NET API リファレンス<ept id="p1">](/dotnet/api/azure.messaging.servicebus?preserve-view=true)</ept>
