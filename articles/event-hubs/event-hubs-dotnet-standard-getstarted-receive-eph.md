---
title: ".NET Standard を使用して Azure Event Hubs からイベントを受信する | Microsoft Docs"
description: ".NET standard で EventProcessorHost を使用したメッセージ受信を開始する"
services: event-hubs
documentationcenter: na
author: jtaubensee
manager: timlt
editor: 
ms.assetid: 
ms.service: event-hubs
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/01/2017
ms.author: jotaub
translationtype: Human Translation
ms.sourcegitcommit: f92909e0098a543f99baf3df3197a799bc9f1edc
ms.openlocfilehash: 8f0870c98f625a56cfddacd91b401206293122f1
ms.lasthandoff: 03/01/2017

---

# <a name="get-started-receiving-messages-with-the-eventprocessorhost-in-net-standard"></a>.NET standard で EventProcessorHost を使用したメッセージ受信を開始する

> [!NOTE]
> このサンプルは [GitHub](https://github.com/Azure/azure-event-hubs-dotnet/tree/master/samples/SampleEphReceiver) で入手できます。

## <a name="what-will-be-accomplished"></a>作業内容

このチュートリアルでは、既存のソリューション **SampleEphReceiver** を (このフォルダー内に) 作成する方法について説明します。 ソリューションをそのまま実行するには、`EhConnectionString`、`EhEntityPath`、および `StorageAccount` 文字列を Event Hub とストレージ アカウントの値に置き換えるか、このチュートリアルに従って独自のものを作成します。

このチュートリアルでは、.NET Core コンソール アプリケーションを記述し、**EventProcessorHost** を使用して Event Hub からメッセージを受信します。

## <a name="prerequisites"></a>前提条件

1. [Microsoft Visual Studio 2015 または 2017](http://www.visualstudio.com)。

2. [.NET Core Visual Studio 2015 または 2017 ツール](http://www.microsoft.com/net/core)。

3. Azure サブスクリプション。

4. Event Hubs 名前空間。
    
## <a name="receive-messages-from-the-event-hub"></a>Event Hub からのメッセージ受信

### <a name="create-a-console-application"></a>コンソール アプリケーションの作成

1. Visual Studio を起動し、.NET Core コンソール アプリケーションを作成します。

### <a name="add-the-event-hubs-nuget-package"></a>Event Hubs NuGet パッケージの追加

* 次の Nuget パッケージをプロジェクトに追加します。
  * [`Microsoft.Azure.EventHubs`](https://www.nuget.org/packages/Microsoft.Azure.EventHubs/)
  * [`Microsoft.Azure.EventHubs.Processor`](https://www.nuget.org/packages/Microsoft.Azure.EventHubs.Processor/)

### <a name="implement-the-ieventprocessor-interface"></a>IEventProcessor インターフェイスの実装

1. "SimpleEventProcessor" と呼ばれる新しいクラスを作成します。

2. SimpleEventProcessor.cs ファイルの先頭に `using` ステートメントを追加します。

    ```cs
    using Microsoft.Azure.EventHubs;
    using Microsoft.Azure.EventHubs.Processor;
    ```

3. `IEventProcessor` インターフェイスを実装します。 クラスは次のようになります。

    ```cs
    namespace SampleEphReceiver
    {
        using System;
        using System.Collections.Generic;
        using System.Text;
        using System.Threading.Tasks;
        using Microsoft.Azure.EventHubs;
        using Microsoft.Azure.EventHubs.Processor;
    
        public class SimpleEventProcessor : IEventProcessor
        {
            public Task CloseAsync(PartitionContext context, CloseReason reason)
            {
                Console.WriteLine($"Processor Shutting Down. Partition '{context.PartitionId}', Reason: '{reason}'.");
                return Task.CompletedTask;
            }
    
            public Task OpenAsync(PartitionContext context)
            {
                Console.WriteLine($"SimpleEventProcessor initialized. Partition: '{context.PartitionId}'");
                return Task.CompletedTask;
            }
    
            public Task ProcessErrorAsync(PartitionContext context, Exception error)
            {
                Console.WriteLine($"Error on Partition: {context.PartitionId}, Error: {error.Message}");
                return Task.CompletedTask;
            }
    
            public Task ProcessEventsAsync(PartitionContext context, IEnumerable<EventData> messages)
            {
                foreach (var eventData in messages)
                {
                    var data = Encoding.UTF8.GetString(eventData.Body.Array, eventData.Body.Offset, eventData.Body.Count);
                    Console.WriteLine($"Message received. Partition: '{context.PartitionId}', Data: '{data}'");
                }
    
                return context.CheckpointAsync();
            }
        }
    }
    ```

### <a name="write-a-main-console-method-that-uses-simpleeventprocessor-to-receive-messages-from-an-event-hub"></a>`SimpleEventProcessor` を使用するメイン コンソール メソッドを記述して Event Hub からメッセージを受信

1. Program.cs ファイルの先頭に次の `using` ステートメントを追加します。
  
    ```cs
    using Microsoft.Azure.EventHubs;
    using Microsoft.Azure.EventHubs.Processor;
    ```

2. Event Hubs 接続文字列、Event Hub パス、ストレージ コンテナー名、ストレージ アカウント名、およびストレージ アカウント キーの `Program` クラスに定数を追加します。 プレースホルダーを対応する値に置き換えます。

    ```cs
    private const string EhConnectionString = "{Event Hubs connection string}";
    private const string EhEntityPath = "{Event Hub path/name}";
    private const string StorageContainerName = "{Storage account container name}";
    private const string StorageAccountName = "{Storage account name}";
    private const string StorageAccountKey = "{Storage account key}";

    private static readonly string StorageConnectionString = string.Format("DefaultEndpointsProtocol=https;AccountName={0};AccountKey={1}", StorageAccountName, StorageAccountKey);
    ```   

3. 次のように、`Program` クラスに `MainAsync` という名前の新しいメソッドを追加します。
    ```cs
    private static async Task MainAsync(string[] args)
    {
        Console.WriteLine("Registering EventProcessor...");

        var eventProcessorHost = new EventProcessorHost(
            EhEntityPath,
            PartitionReceiver.DefaultConsumerGroupName,
            EhConnectionString,
            StorageConnectionString,
            StorageContainerName);

        // Registers the Event Processor Host and starts receiving messages
        await eventProcessorHost.RegisterEventProcessorAsync<SimpleEventProcessor>();

        Console.WriteLine("Receiving. Press enter key to stop worker.");
        Console.ReadLine();

        // Disposes of the Event Processor Host
        await eventProcessorHost.UnregisterEventProcessorAsync();
    }
    ```

3. `Main` メソッドに次のコード行を追加します。

    ```cs
    MainAsync(args).GetAwaiter().GetResult();
    ```

    Program.cs ファイルは次のようになります。

    ```cs
    namespace SampleEphReceiver
    {
        using System;
        using System.Threading.Tasks;
        using Microsoft.Azure.EventHubs;
        using Microsoft.Azure.EventHubs.Processor;
    
        public class Program
        {
            private const string EhConnectionString = "{Event Hubs connection string}";
            private const string EhEntityPath = "{Event Hub path/name}";
            private const string StorageContainerName = "{Storage account container name}";
            private const string StorageAccountName = "{Storage account name}";
            private const string StorageAccountKey = "{Storage account key}";
    
            private static readonly string StorageConnectionString = string.Format("DefaultEndpointsProtocol=https;AccountName={0};AccountKey={1}", StorageAccountName, StorageAccountKey);
    
            public static void Main(string[] args)
            {
                MainAsync(args).GetAwaiter().GetResult();
            }
    
            private static async Task MainAsync(string[] args)
            {
                Console.WriteLine("Registering EventProcessor...");
    
                var eventProcessorHost = new EventProcessorHost(
                    EhEntityPath,
                    PartitionReceiver.DefaultConsumerGroupName,
                    EhConnectionString,
                    StorageConnectionString,
                    StorageContainerName);
    
                // Registers the Event Processor Host and starts receiving messages
                await eventProcessorHost.RegisterEventProcessorAsync<SimpleEventProcessor>();
    
                Console.WriteLine("Receiving. Press enter key to stop worker.");
                Console.ReadLine();
    
                // Disposes of the Event Processor Host
                await eventProcessorHost.UnregisterEventProcessorAsync();
            }
        }
    }
    ```
  
4. プログラムを実行し、エラーがないことを確認します。
  
お疲れさまでした。 Event Hub からメッセージを受信しました。

## <a name="next-steps"></a>次のステップ
Event Hubs の詳細については、次のリンク先を参照してください:

* [Event Hubs の概要](event-hubs-what-is-event-hubs.md)
* [Event Hub を作成する](event-hubs-create.md)
* [Event Hubs の FAQ](event-hubs-faq.md)
