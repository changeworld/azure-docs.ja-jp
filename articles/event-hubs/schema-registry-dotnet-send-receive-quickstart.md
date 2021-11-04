---
title: イベントの送受信時にスキーマを検証する - AMQP と .NET
description: この記事では、スキーマ レジストリを使用したスキーマ検証を使用して、Azure Event Hubs との間でイベントを送受信する .NET Core アプリケーションを作成する方法について説明します。
ms.topic: quickstart
ms.date: 11/02/2021
ms.custom: devx-track-csharp, ignite-fall-2021
ms.openlocfilehash: d87fdaff91e46811319b259de01300fb2a70dff3
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/03/2021
ms.locfileid: "131433863"
---
# <a name="validate-schema-when-sending-and-receiving-events---amqp-and-net"></a>イベントの送受信時にスキーマを検証する - AMQP と .NET 
このクイックスタートでは、**Azure.Messaging.EventHubs** .NET ライブラリを使用したスキーマ検証を使用して、イベント ハブとの間でイベントを送受信する方法について説明します。 

## <a name="prerequisites"></a>前提条件
Azure Event Hubs を初めて使用する場合は、このクイックスタートを行う前に [Event Hubs の概要](event-hubs-about.md)を参照してください。 

このクイック スタートを完了するには、次の前提条件を用意しておく必要があります。
- [Event Hubs 名前空間とイベント ハブを作成する](event-hubs-create.md)クイック スタートに関するページの手順に従います。
- [接続文字列を取得する](event-hubs-get-connection-string.md)方法に関するページの手順に従って、使用している Event Hubs 名前空間への接続文字列を取得します。 現在のクイック スタートで使用する次の設定をメモします。
    - Event Hubs 名前空間の接続文字列
    - イベント ハブの名前
- **[.NET クイック スタート](event-hubs-dotnet-standard-getstarted-send.md)を完了して**、.NET を使用してイベント ハブとの間でイベントを送受信する方法について理解します。 以前に .NET クイック スタートを既に実行している場合は、この手順をスキップできます。 
- **[スキーマ レジストリを使用してスキーマを作成する](create-schema-registry.md)方法に関するページの手順に従って**、スキーマ グループとスキーマを作成します。 スキーマを作成する場合は、現在のクイックスタートの記事の「[スキーマを作成する](#create-a-schema)」の手順に従ってください。 
- **Microsoft Visual Studio 2019**。 Azure Event Hubs クライアント ライブラリでは、C# 8.0 で導入された新機能を利用しています。  以前のバージョンの C# 言語でライブラリを使用することもできますが、新しい構文は使用できません。 完全な構文を使用するには、[.NET Core SDK](https://dotnet.microsoft.com/download) 3.0 以上で、[言語バージョン](/dotnet/csharp/language-reference/configure-language-version#override-a-default)を `latest` に設定してコンパイルすることをお勧めします。 Visual Studio を使用している場合、Visual Studio 2019 より前のバージョンには、C# 8.0 プロジェクトをビルドするために必要なツールとの互換性がありません。 無料の Community エディションを含む Visual Studio 2019 は、<bpt id="p1">[</bpt>こちら<ept id="p1">](https://visualstudio.microsoft.com/vs/)</ept>からダウンロードできます。


## <a name="create-a-schema"></a>スキーマの作成 
1. スキーマ レジストリ ポータルを使用して、**contoso-sg** という名前のスキーマ グループを作成します。 シリアル化の種類として Avro を使用し、互換性モードに **[なし]** を使用します。 
1. そのスキーマ グループで、次のスキーマ コンテンツを使用して、スキーマ名 ``Microsoft.Azure.Data.SchemaRegistry.example.Order`` を使用して新しい Avro スキーマを作成します。 

    ```json 
    {
      "namespace": "Microsoft.Azure.Data.SchemaRegistry.example",
      "type": "record",
      "name": "Order",
      "fields": [
        {
          "name": "id",
          "type": "string"
        },
        {
          "name": "amount",
          "type": "double"
        },
        {
          "name": "description",
          "type": "string"
        }
      ]
    } 
    ```

## <a name="produce-events-to-event-hubs-with-schema-validation"></a>スキーマ検証を使用してイベント ハブにイベントを生成する


### <a name="create-console-application-for-event-producer"></a>イベント プロデューサー用のコンソール アプリケーションを作成する 

1. Visual Studio 2019 を起動します。 
1. **[新しいプロジェクトの作成]** を選択します。 
1. <bpt id="p1">**</bpt>[新しいプロジェクトの作成]<ept id="p1">**</ept> ダイアログ ボックスで、次の手順に従います。このダイアログ ボックスが表示されない場合は、メニューで <bpt id="p2">**</bpt>[ファイル]<ept id="p2">**</ept> 、 <bpt id="p3">**</bpt>[新規]<ept id="p3">**</ept> 、 <bpt id="p4">**</bpt>[プロジェクト]<ept id="p4">**</ept> の順に選択します。 
    1. プログラミング言語として <bpt id="p1">**</bpt>[C#]<ept id="p1">**</ept> を選択します。
    1. アプリケーションの種類として <bpt id="p1">**</bpt>[コンソール]<ept id="p1">**</ept> を選択します。 
    1. 結果リストから <bpt id="p1">**</bpt>[コンソール アプリケーション]<ept id="p1">**</ept> を選択します。 
    1. 次に、 <bpt id="p1">**</bpt>[次へ]<ept id="p1">**</ept> を選択します。 

        :::image type="content" source="./media/getstarted-dotnet-standard-send-v2/new-send-project.png" alt-text="[新しいプロジェクト] ダイアログ ボックスを示す画像。":::
1. プロジェクト名として「**OrderProducer**」、ソリューション名として「**SRQuickStart**」と入力し、 **[OK]** を選択してプロジェクトを作成します。 


### <a name="add-the-event-hubs-nuget-package"></a>Event Hubs NuGet パッケージの追加

1. メニューから **[ツール]**  >  **[NuGet パッケージ マネージャー]**  >  **[パッケージ マネージャー コンソール]** の順に選択します。 
1. 次のコマンドを実行して、**Azure.Messaging.EventHubs** NuGet パッケージをインストールします。

    ```cmd
    Install-Package Azure.Messaging.EventHubs
    Install-Package Azure.Identity
    Install-Package Microsoft.Azure.Data.SchemaRegistry.ApacheAvro -Version 1.0.0-beta.2
    Install-Package Azure.ResourceManager.Compute -Version 1.0.0-beta.1

    ```
1. [次に](/dotnet/api/overview/azure/identity-readme#authenticating-via-visual-studio)示すように、プロデューサー アプリケーションを認証して、Visual Studio Azure に接続します。  

### <a name="code-generation-using-the-avro-schema"></a>Avro スキーマを使用したコード生成  
1. 同じスキーマ コンテンツを使用し、OrderProducer プロジェクト内に Avro スキーマ ファイル ``Order.avsc`` を作成できます。 
1. その後、このスキーマ ファイルを使用して .NET のコードを生成できます。 コード生成には、[avrogen](https://www.nuget.org/packages/Apache.Avro.Tools/) などの外部コード生成ツールを使用できます。 (たとえば、`` avrogen -s .\Order.avsc `` を実行してコードを生成できます)。 
1. コードを生成したら、対応する C# 型をプロジェクト内で使用できる必要があります。 上記の Avro スキーマでは、``Microsoft.Azure.Data.SchemaRegistry.example`` 名前空間に C# 型が生成されます。 


### <a name="write-code-to-serialize-and-send-events-to-the-event-hub"></a>イベント ハブにイベントをシリアル化して送信するコードを記述する 

1. **Program.cs** ファイルの先頭に次の `using` ステートメントを追加します。

    ```csharp
    using System;
    using System.IO;
    using System.Threading;
    using Azure.Data.SchemaRegistry;
    using Azure.Identity;
    using Microsoft.Azure.Data.SchemaRegistry.ApacheAvro;
    using Azure.Messaging.EventHubs;
    using Azure.Messaging.EventHubs.Producer;
    using System.Threading.Tasks;
    ```
1. 次に示すように、``Order`` スキーマに関連する生成された型をインポートすることもできます。 
    ```csharp
    using Microsoft.Azure.Data.SchemaRegistry.example;   
    ```

2. Event Hubs 接続文字列およびイベント ハブ名を表す定数を、`Program` クラスに追加します。  

    ```csharp
        // connection string to the Event Hubs namespace
        private const string connectionString = "<EVENT HUBS NAMESPACE - CONNECTION STRING>";

        // name of the event hub
        private const string eventHubName = "<EVENT HUB NAME>";
        
        // Schema Registry endpoint 
        private const string schemaRegistryEndpoint = "<EVENT HUBS NAMESPACE>.servicebus.windows.net>";
            
        // name of the consumer group   
        private const string schemaGroup = "<SCHEMA GROUP>";

    ```

    > [!NOTE]
    > プレースホルダーの値を、名前空間への接続文字列、イベント ハブの名前、スキーマ グループに置き換えます。
3. `Program` クラスに次の静的プロパティを追加します。 コードのコメントを参照してください。 

    ```csharp
        // The Event Hubs client types are safe to cache and use as a singleton for the lifetime
        // of the application, which is best practice when events are being published or read regularly.
        static EventHubProducerClient producerClient;    
    ```
1. `Main` メソッドを次の `async Main` メソッドで置き換えます。 詳細については、コードのコメントを参照してください。 

    ```csharp
        static async Task Main()
        {
            // Create a producer client that you can use to send events to an event hub
            producerClient = new EventHubProducerClient(connectionString, eventHubName); 
    
            // Create a schema registry client that you can use to serialize and validate data.  
            var schemaRegistryClient = new SchemaRegistryClient(endpoint: schemaRegistryEndpoint, credential: new DefaultAzureCredential());
    
            // Create a batch of events 
            using EventDataBatch eventBatch = await producerClient.CreateBatchAsync();
            
            // Create a new order object using the generated type/class 'Order'. 
            var sampleOrder = new Order { id = "12345", amount = 55.99, description = "This is a sample order." };
    
            using var memoryStream = new MemoryStream();
            // Create an Avro object serializer using the Schema Registry client object. 
            var producerSerializer = new SchemaRegistryAvroObjectSerializer(schemaRegistryClient, schemaGroup, new SchemaRegistryAvroObjectSerializerOptions { AutoRegisterSchemas = true });
    
            // Serialize events data to the memory stream object. 
            producerSerializer.Serialize(memoryStream, sampleOrder, typeof(Order), CancellationToken.None);
    
            byte[] _memoryStreamBytes;
            _memoryStreamBytes = memoryStream.ToArray();
    
            // Create event data with serialized data and add it to an event batch. 
            eventBatch.TryAdd(new EventData(_memoryStreamBytes));
    
            // Send serilized event data to event hub. 
            await producerClient.SendAsync(eventBatch);
            Console.WriteLine("A batch of 1 order has been published.");
        }
    ```
5. プロジェクトをビルドし、エラーがないことを確認します。
6. プログラムを実行し、確認メッセージが表示されるまで待ちます。 

    ```csharp
    A batch of 1 order has been published.
    ```
1. Azure portal で、イベント ハブがイベントを受信したことを確認できます。 **[メトリック]** セクションで **[メッセージ]** ビューに切り替えます。 ページを最新の情報に更新して、グラフを更新します。 メッセージが受信されたことが示されるまでに数秒かかることがあります。 

    :::image type="content" source="./media/getstarted-dotnet-standard-send-v2/verify-messages-portal.png" alt-text="イベント ハブでイベントが受信されたことを確認する Azure portal ページの画像。" lightbox="./media/getstarted-dotnet-standard-send-v2/verify-messages-portal.png":::




## <a name="consume-events-from-event-hubs-with-schema-validation"></a>スキーマ検証を使用してイベント ハブからイベントを使用する
このセクションでは、イベント ハブからイベントを受信し、スキーマ レジストリを使用してイベント データを逆シリアル化する .NET Core コンソール アプリケーションを作成する方法について説明します。 


### <a name="create-consumer-application"></a>コンシューマー アプリケーションを作成する

1. ソリューション エクスプローラー ウィンドウで、 **[SRQuickStart]** ソリューションを右クリックし、 **[追加]** をポイントして、 **[新しいプロジェクト]** を選択します。 
1. <bpt id="p1">**</bpt>[コンソール アプリケーション]<ept id="p1">**</ept> を選択し、 <bpt id="p2">**</bpt>[次へ]<ept id="p2">**</ept> を選択します。 
1. **[プロジェクト名]** に「**OrderConsumer**」と入力し、 **[作成]** を選択します。 
1. **ソリューション エクスプローラー** ウィンドウで、 **[OrderConsumer]** を右クリックし、 **[スタートアップ プロジェクトとして設定]** を選択します。 

### <a name="add-the-event-hubs-nuget-package"></a>Event Hubs NuGet パッケージの追加

1. メニューから **[ツール]**  >  **[NuGet パッケージ マネージャー]**  >  **[パッケージ マネージャー コンソール]** の順に選択します。 
1. **[パッケージ マネージャー コンソール]** ウィンドウで、 **[既定のプロジェクト]** に **OrderConsumer** が選択されていることを確認します。 選択されていない場合は、ドロップダウン リストを使用して **OrderConsumer** を選択します。


1. 次のコマンドを実行して、必要な NuGet パッケージをインストールします。

    ```cmd
    Install-Package Azure.Messaging.EventHubs
    Install-Package Azure.Messaging.EventHubs.Processor
    Install-Package Azure.Identity
    Install-Package Microsoft.Azure.Data.SchemaRegistry.ApacheAvro -Version 1.0.0-beta.2
    Install-Package Azure.ResourceManager.Compute -Version 1.0.0-beta.1

    ```
1. [次に](/dotnet/api/overview/azure/identity-readme#authenticating-via-visual-studio)示すように、プロデューサー アプリケーションを認証して、Visual Studio Azure に接続します。 



### <a name="code-generation-using-the-avro-schema"></a>Avro スキーマを使用したコード生成  
1. 同じスキーマ コンテンツを使用し、``OrderProducer`` プロジェクト内に Avro スキーマ ファイル ``Order.avsc`` を作成できます。 
1. その後、このスキーマ ファイルを使用して .NET のコードを生成できます。 これには、[avrogen](https://www.nuget.org/packages/Apache.Avro.Tools/) などの外部コード生成ツールを使用できます。 (たとえば、`` avrogen -s .\Order.avsc `` を実行してコードを生成できます)。 
1. コードを生成したら、対応する C# 型をプロジェクト内で使用できる必要があります。 上記の Avro スキーマでは、``Microsoft.Azure.Data.SchemaRegistry.example`` 名前空間に C# 型が生成されます。 


### <a name="write-code-to-receive-events-and-deserialize-them-using-schema-registry"></a>スキーマ レジストリを使用し、イベントを受信して逆シリアル化するコードを記述する


1. **Program.cs** ファイルの先頭に次の `using` ステートメントを追加します。

    ```csharp
    using System;
    using System.IO;
    using System.Text;
    using System.Threading.Tasks;
    using System.Threading;
    using Azure.Data.SchemaRegistry;
    using Azure.Identity;
    using Microsoft.Azure.Data.SchemaRegistry.ApacheAvro;
    using Azure.Storage.Blobs;
    using Azure.Messaging.EventHubs;
    using Azure.Messaging.EventHubs.Consumer;
    using Azure.Messaging.EventHubs.Processor;
    ```
1. 次に示すように、``Order`` スキーマに関連する生成された型をインポートすることもできます。 
    ```csharp
    using Microsoft.Azure.Data.SchemaRegistry.example;   
    ```

2. Event Hubs 接続文字列およびイベント ハブ名を表す定数を、`Program` クラスに追加します。  

    ```csharp
        // connection string to the Event Hubs namespace
        private const string connectionString = "<EVENT HUBS NAMESPACE - CONNECTION STRING>";

        // name of the event hub
        private const string eventHubName = "<EVENT HUB NAME>";

        private const string blobStorageConnectionString = "<AZURE STORAGE CONNECTION STRING>";
        
        private const string blobContainerName = "<BLOB CONTAINER NAME>";
        
        // Schema Registry endpoint 
        private const string schemaRegistryEndpoint = "<EVENT HUBS NAMESPACE>.servicebus.windows.net>";
            
        // name of the consumer group   
        private const string schemaGroup = "<SCHEMA GROUP>";

    ```

3. `Program` クラスに次の静的プロパティを追加します。 

    ```csharp
        static BlobContainerClient storageClient;

        // The Event Hubs client types are safe to cache and use as a singleton for the lifetime
        // of the application, which is best practice when events are being published or read regularly.        
        static EventProcessorClient processor;    
    ```
1. `Main` メソッドを次の `async Main` メソッドで置き換えます。 詳細については、コードのコメントを参照してください。 

    ```csharp
        static async Task Main()
        {
            // Read from the default consumer group: $Default
            string consumerGroup = EventHubConsumerClient.DefaultConsumerGroupName;

            // Create a blob container client that the event processor will use 
            storageClient = new BlobContainerClient(blobStorageConnectionString, blobContainerName);

            // Create an event processor client to process events in the event hub
            processor = new EventProcessorClient(storageClient, consumerGroup, ehubNamespaceConnectionString, eventHubName);

            // Register handlers for processing events and handling errors
            processor.ProcessEventAsync += ProcessEventHandler;
            processor.ProcessErrorAsync += ProcessErrorHandler;

            // Start the processing
            await processor.StartProcessingAsync();

            // Wait for 30 seconds for the events to be processed
            await Task.Delay(TimeSpan.FromSeconds(30));

            // Stop the processing
            await processor.StopProcessingAsync();
        }
    ```
1. 次に、スキーマ レジストリを使用してイベントの逆シリアル化のロジックを含む次のイベント ハンドラー メソッドを追加します。 
    ```csharp
        static async Task ProcessEventHandler(ProcessEventArgs eventArgs)
        {
            // Create a schema registry client that you can use to deserialize and validate data.  
            var schemaRegistryClient = new SchemaRegistryClient(endpoint: schemaRegistryEndpoint, credential: new DefaultAzureCredential());
    
            // Retrieve event data and convert it to a byte array. 
            byte[] _memoryStreamBytes = eventArgs.Data.Body.ToArray();
            using var consumerMemoryStream = new MemoryStream(_memoryStreamBytes);
    
            var consumerSerializer = new SchemaRegistryAvroObjectSerializer(schemaRegistryClient, schemaGroup, new SchemaRegistryAvroObjectSerializerOptions { AutoRegisterSchemas = false });
            consumerMemoryStream.Position = 0;
    
            // Deserialize event data and create order object using schema registry. 
            Order sampleOrder = (Order)consumerSerializer.Deserialize(consumerMemoryStream, typeof(Order), CancellationToken.None);
            Console.WriteLine("Received - Order ID: " + sampleOrder.id);                 
    
            // Update checkpoint in the blob storage so that the app receives only new events the next time it's run
            await eventArgs.UpdateCheckpointAsync(eventArgs.CancellationToken);   
        }

    ```

 
1. ここで、次のエラー ハンドラー メソッドをクラスに追加します。 

    ```csharp
        static Task ProcessErrorHandler(ProcessErrorEventArgs eventArgs)
        {
            // Write details about the error to the console window
            Console.WriteLine($"\tPartition '{ eventArgs.PartitionId}': an unhandled exception was encountered. This was not expected to happen.");
            Console.WriteLine(eventArgs.Exception.Message);
            return Task.CompletedTask;
        }
  
    ```
1. プロジェクトをビルドし、エラーがないことを確認します。


6. 受信側アプリを実行します。 
1. イベントが受信されたことを示すメッセージが表示されます。 

    ```bash
    Received - Order ID: 12345
    ```
    これらのイベントは、前に送信側プログラムを実行してイベント ハブに送信した 3 つのイベントです。 


## <a name="next-steps"></a>次のステップ
詳細については、「[.NET 用 Azure Schema Registry クライアント ライブラリ](https://github.com/Azure/azure-sdk-for-net/tree/main/sdk/schemaregistry/Azure.Data.SchemaRegistry)」を参照してください。 
