---
title: ".NET Framework を使用して Azure Event Hubs からイベントを受信する | Microsoft Docs"
description: "このチュートリアルでは、.NET Framework を使用して Azure Event Hubs からイベントを受信する操作を行います。"
services: event-hubs
documentationcenter: 
author: jtaubensee
manager: timlt
editor: 
ms.assetid: c4974bd3-2a79-48a1-aa3b-8ee2d6655b28
ms.service: event-hubs
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 01/30/2017
ms.author: jotaub
translationtype: Human Translation
ms.sourcegitcommit: fe331199333d492dbc42c9125c9da96a44066ee1
ms.openlocfilehash: 4a3656d09f216e5c895bac5d7d680b44d76f4df8


---
# <a name="get-started-with-event-hubs-using-the-net-framework"></a>.NET Framework で Event Hubs を使ってみる

## <a name="receive-messages-with-eventprocessorhost"></a>EventProcessorHost を使用したメッセージの受信
[EventProcessorHost][EventProcessorHost] は、永続的なチェックポイントの管理によって Event Hubs のイベントの受信を簡素化し、並列してそれらの Event Hubs から受信する .NET クラスです。 [EventProcessorHost][EventProcessorHost] を使用すると、異なる複数のノードでホストされている場合でも、複数の受信側の間でイベントを分割することができます。 この例では、受信側が単一の場合に [EventProcessorHost][EventProcessorHost] を使用する方法を示します。 [イベント処理のスケールアウト][Scale out Event Processing with Event Hubs]のサンプルは、受信者側が複数の場合に [EventProcessorHost][EventProcessorHost] を使用する方法を示します。

[EventProcessorHost][EventProcessorHost] を使用するには、[Azure ストレージ アカウント][Azure Storage account]が必要です。

1. [Azure Portal][Azure portal] にログインし、画面の左上にある **[新規]** をクリックします。
2. **[ストレージ]**、**[ストレージ アカウント]** の順にクリックします。
   
    ![](./media/event-hubs-dotnet-framework-getstarted-receive-eph/create-storage1.png)
3. **[ストレージ アカウントの作成]** ブレードで、ストレージ アカウントの名前を入力します。 リソースが作成される Azure サブスクリプション、リソース グループ、場所を選択します。 **[Create]**をクリックします。
   
    ![](./media/event-hubs-dotnet-framework-getstarted-receive-eph/create-storage2.png)
4. ストレージ アカウントの一覧で、新しく作成したストレージ アカウントをクリックします。
5. ストレージ アカウントのブレードで、 **[アクセス キー]**をクリックします。 後でこのチュートリアルで使用するため、 **key1** の値をコピーしておきます。
   
    ![](./media/event-hubs-dotnet-framework-getstarted-receive-eph/create-storage3.png)
6. Visual Studio で、**コンソール アプリケーション** プロジェクト テンプレートを使用して、新しい Visual C# のデスクトップ アプリ プロジェクトを作成します。 プロジェクトの名前として「 **Receiver**」と入力します。
   
    ![](./media/event-hubs-dotnet-framework-getstarted-receive-eph/create-receiver-csharp1.png)
7. ソリューション エクスプローラーでソリューションを右クリックし、 **[ソリューションの NuGet パッケージの管理]**をクリックします。
8. **[参照]** タブをクリックして、`Microsoft Azure Service Bus Event Hub - EventProcessorHost` を検索します。 プロジェクト名 (**Receiver**) が **[バージョン]** ボックスで指定されていることを確認します。 **[インストール]**をクリックして、使用条件に同意します。
   
    ![](./media/event-hubs-dotnet-framework-getstarted-receive-eph/create-eph-csharp1.png)
   
    Visual Studio によって、 [Azure Service Bus Event Hub - EventProcessorHost NuGet パッケージ](https://www.nuget.org/packages/Microsoft.Azure.ServiceBus.EventProcessorHost)への参照がすべての依存関係と共にダウンロード、インストール、追加されます。
9. **[Receiver]** プロジェクトを右クリックし、**[追加]**、**[クラス]** の順にクリックします。 新しいクラスの名前として「**SimpleEventProcessor**」と入力し、**[追加]** をクリックしてクラスを作成します。
   
    ![](./media/event-hubs-dotnet-framework-getstarted-receive-eph/create-receiver-csharp2.png)
10. SimpleEventProcessor.cs ファイルの先頭に次のステートメントを追加します。
    
     ```csharp
     using Microsoft.ServiceBus.Messaging;
     using System.Diagnostics;
     ```
    
     次に、クラスの本文に次のコードを置き換えます。
    
     ```csharp
     class SimpleEventProcessor : IEventProcessor
     {
         Stopwatch checkpointStopWatch;
    
         async Task IEventProcessor.CloseAsync(PartitionContext context, CloseReason reason)
         {
             Console.WriteLine("Processor Shutting Down. Partition '{0}', Reason: '{1}'.", context.Lease.PartitionId, reason);
             if (reason == CloseReason.Shutdown)
             {
                 await context.CheckpointAsync();
             }
         }
    
         Task IEventProcessor.OpenAsync(PartitionContext context)
         {
             Console.WriteLine("SimpleEventProcessor initialized.  Partition: '{0}', Offset: '{1}'", context.Lease.PartitionId, context.Lease.Offset);
             this.checkpointStopWatch = new Stopwatch();
             this.checkpointStopWatch.Start();
             return Task.FromResult<object>(null);
         }
    
         async Task IEventProcessor.ProcessEventsAsync(PartitionContext context, IEnumerable<EventData> messages)
         {
             foreach (EventData eventData in messages)
             {
                 string data = Encoding.UTF8.GetString(eventData.GetBytes());
    
                 Console.WriteLine(string.Format("Message received.  Partition: '{0}', Data: '{1}'",
                     context.Lease.PartitionId, data));
             }
    
             //Call checkpoint every 5 minutes, so that worker can resume processing from 5 minutes back if it restarts.
             if (this.checkpointStopWatch.Elapsed > TimeSpan.FromMinutes(5))
             {
                 await context.CheckpointAsync();
                 this.checkpointStopWatch.Restart();
             }
         }
     }
     ```
    
     このクラスは、 **EventProcessorHost** から呼び出されて、Event Hub から受信したイベントを処理します。 `SimpleEventProcessor` クラスは、ストップウォッチを使用して **EventProcessorHost** コンテキストで定期的にチェックポイント メソッドを呼び出します。 これにより、受信側を再起動すると、処理の作業の&5; 分以内に機能が失われます。
11. **Program** クラスで、ファイルの先頭に次の `using` ステートメントを追加します。
    
     ```csharp
     using Microsoft.ServiceBus.Messaging;
     ```
    
     次に、`Program` クラスの `Main` メソッドを次のコードに置き換え、先ほど保存した Event Hub の名前と名前空間レベルの接続文字列、および前のセクションでコピーしたストレージ アカウントとキーを代入します。 
    
     ```csharp
     static void Main(string[] args)
     {
       string eventHubConnectionString = "{Event Hub connection string}";
       string eventHubName = "{Event Hub name}";
       string storageAccountName = "{storage account name}";
       string storageAccountKey = "{storage account key}";
       string storageConnectionString = string.Format("DefaultEndpointsProtocol=https;AccountName={0};AccountKey={1}", storageAccountName, storageAccountKey);
    
       string eventProcessorHostName = Guid.NewGuid().ToString();
       EventProcessorHost eventProcessorHost = new EventProcessorHost(eventProcessorHostName, eventHubName, EventHubConsumerGroup.DefaultGroupName, eventHubConnectionString, storageConnectionString);
       Console.WriteLine("Registering EventProcessor...");
       var options = new EventProcessorOptions();
       options.ExceptionReceived += (sender, e) => { Console.WriteLine(e.Exception); };
       eventProcessorHost.RegisterEventProcessorAsync<SimpleEventProcessor>(options).Wait();
    
       Console.WriteLine("Receiving. Press enter key to stop worker.");
       Console.ReadLine();
       eventProcessorHost.UnregisterEventProcessorAsync().Wait();
     }
     ```

> [!NOTE]
> このチュートリアルでは、[EventProcessorHost][EventProcessorHost] の単一のインスタンスを使用します。 スループットを向上させるには、[EventProcessorHost][EventProcessorHost] の複数のインスタンスを実行することをお勧めします ([イベント処理のスケールアウト][イベント処理のスケールアウト]のサンプルを参照してください)。 このような場合、受信したイベントの負荷を分散するために、さまざまなインスタンスが自動的に連携します。 複数の受信側でぞれぞれ *すべて* のイベントを処理する場合、 **ConsumerGroup** 概念を使用する必要があります。 さまざまなコンピューターからイベントを受信する場合、デプロイしたコンピューター (またはロール) に基づいて [EventProcessorHost][EventProcessorHost] インスタンスの名前を指定するのに便利です。 これらのトピックの詳細については、[Event Hubs の概要][Event Hubs Overview]に関するトピックと、「[Event Hubs のプログラミング ガイド][Event Hubs Programming Guide]」を参照してください。
> 
> 

<!-- Links -->
[Event Hubs Overview]: event-hubs-overview.md
[Event Hubs Programming Guide]: event-hubs-programming-guide.md
[Azure Storage account]: ../storage/storage-create-storage-account.md
[EventProcessorHost]: /dotnet/api/microsoft.servicebus.messaging.eventprocessorhost
[Azure portal]: https://portal.azure.com

## <a name="next-steps"></a>次のステップ
イベント ハブを作成し、データを送受信する実用的なアプリケーションが構築できたので、以下のリンク先にアクセスして学習を進めることができます。

* [イベント プロセッサ ホスト](/dotnet/api/microsoft.servicebus.messaging.eventprocessorhost)
* [Event Hubs の概要][Event Hubs overview]
* [Event Hubs の FAQ](event-hubs-faq.md)

<!-- Images. -->
[19]: ./media/event-hubs-csharp-ephcs-getstarted/create-eh-proj1.png
[20]: ./media/event-hubs-csharp-ephcs-getstarted/create-eh-proj2.png
[21]: ./media/event-hubs-csharp-ephcs-getstarted/run-csharp-ephcs1.png
[22]: ./media/event-hubs-csharp-ephcs-getstarted/run-csharp-ephcs2.png

<!-- Links -->
[Event Processor Host]: https://www.nuget.org/packages/Microsoft.Azure.ServiceBus.EventProcessorHost
[Event Hubs overview]: event-hubs-overview.md
[Scale out Event Processing with Event Hubs]: https://code.msdn.microsoft.com/Service-Bus-Event-Hub-45f43fc3


<!--HONumber=Feb17_HO1-->


