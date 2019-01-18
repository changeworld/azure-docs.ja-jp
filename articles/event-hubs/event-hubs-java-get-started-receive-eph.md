---
title: Java を使用してイベントを受信する - Azure Event Hubs | Microsoft Docs
description: この記事では、Azure Event Hubs からイベントを受信する Java アプリケーションを作成するためのチュートリアルを提供します。
services: event-hubs
author: ShubhaVijayasarathy
manager: timlt
ms.service: event-hubs
ms.workload: core
ms.topic: article
ms.custom: seodec18
ms.date: 12/06/2018
ms.author: shvija
ms.openlocfilehash: e29cf43f490bf5e8bac5e5c36b16476f93d80bfa
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/08/2018
ms.locfileid: "53081963"
---
# <a name="receive-events-from-azure-event-hubs-using-java"></a>Java を使用して Azure Event Hubs からイベントを受信する

Event Hubs は、拡張性の高いインジェスト システムで、1 秒あたり何百万ものイベントを取り込むことができます。そのためアプリケーションは、接続されているデバイスやアプリケーションによって生成された大量のデータを処理し、分析できます。 Event Hubs に収集されたデータは、任意のリアルタイム分析プロバイダーやストレージ クラスターを使用して転送と格納できます。

詳細については、「[Event Hubs の概要][Event Hubs overview]」をご覧ください。

このチュートリアルでは、Java で記述されたコンソール アプリケーションを使用して、イベント ハブへのイベントを受信する方法について説明します。

## <a name="prerequisites"></a>前提条件

このチュートリアルを完了するには、次の前提条件を用意しておく必要があります。

* Java 開発環境。 このチュートリアルでは、 [Eclipse](https://www.eclipse.org/)を想定しています。
* アクティブな Azure アカウントアカウントがない場合、Azure 試用版にサインアップして、最大 10 件の無料 Mobile Apps を入手できます。 Azure サブスクリプションをお持ちでない場合は、開始する前に[無料アカウント][]を作成してください。

このチュートリアルのコードは [GitHub の EventProcessorSample コード](https://github.com/Azure/azure-event-hubs/tree/master/samples/Java/Basic/EventProcessorSample)に基づくものであり、完全に動作するアプリケーションを表示する場合に確認することができます。

## <a name="receive-messages-with-eventprocessorhost-in-java"></a>Java の EventProcessorHost を使用したメッセージの受信

**EventProcessorHost** は、永続的なチェックポイントと、Event Hubs からの並列受信を管理することで、Event Hubs のイベントの受信を簡素化する Java クラスです。 EventProcessorHost を使用すると、異なる複数のノードでホストされている場合でも、複数の受信側の間でイベントを分割することができます。 この例では、受信側が単一の場合に EventProcessorHost を使用する方法を示します。

### <a name="create-a-storage-account"></a>ストレージ アカウントの作成

EventProcessorHost を使用するには、[Azure ストレージ アカウント][Azure Storage account]が必要です。

1. [Azure Portal][Azure portal] にログインし、画面左側の **[+ リソースの作成]** をクリックします。
2. **[ストレージ]**、**[ストレージ アカウント]** の順にクリックします。 **[ストレージ アカウントの作成]** ウィンドウで、ストレージ アカウントの名前を入力します。 残りのフィールドを完了し、目的の地域を選択し、**[作成]** をクリックします。
   
    ![[ストレージ アカウントの作成]](./media/event-hubs-dotnet-framework-getstarted-receive-eph/create-storage2.png)

3. 新しく作成したストレージ アカウントをクリックし、**[アクセス キー]** をクリックします。
   
    ![アクセス キーを取得する](./media/event-hubs-dotnet-framework-getstarted-receive-eph/create-storage3.png)

    key1 の値を一時的な場所にコピーします。 このチュートリアルの後の方で、それを使用します。

### <a name="create-a-java-project-using-the-eventprocessor-host"></a>EventProcessor ホストを使用した Java プロジェクトの作成

Event Hubs の Java クライアント ライブラリは、[Maven Central Repository][Maven Package] の Maven プロジェクトで利用でき、Maven プロジェクト ファイル内の以下の依存関係宣言を使用して参照できます。 アーティファクト azure-eventhubs-eph の現在のバージョンは 2.0.1 で、アーティファクト azure-eventhubs の現在のバージョンは 1.0.2 です。    

```xml
<dependency>
    <groupId>com.microsoft.azure</groupId>
    <artifactId>azure-eventhubs</artifactId>
    <version>1.0.2</version>
</dependency>
<dependency>
    <groupId>com.microsoft.azure</groupId>
    <artifactId>azure-eventhubs-eph</artifactId>
    <version>2.0.1</version>
</dependency>
```

ビルド環境の種類に応じて、[Maven Central Repository][Maven Package] から最新リリースの JAR ファイルを明示的に取得できます。  

1. 次のサンプルでは、最初に、好みの Java 開発環境でコンソール/シェル アプリケーション用の新しい Maven プロジェクトを作成します。 このクラスは `ErrorNotificationHandler`と呼ばれます。     
   
    ```java
    import java.util.function.Consumer;
    import com.microsoft.azure.eventprocessorhost.ExceptionReceivedEventArgs;
   
    public class ErrorNotificationHandler implements Consumer<ExceptionReceivedEventArgs>
    {
        @Override
        public void accept(ExceptionReceivedEventArgs t)
        {
            System.out.println("SAMPLE: Host " + t.getHostname() + " received general error notification during " + t.getAction() + ": " + t.getException().toString());
        }
    }
    ```
2. 次のコードで `EventProcessorSample`という新しいクラスを作成します。 プレースホルダーを、イベント ハブとストレージ アカウントの作成時に使用した値に置き換えます。
   
   ```java
   package com.microsoft.azure.eventhubs.samples.eventprocessorsample;

   import com.microsoft.azure.eventhubs.ConnectionStringBuilder;
   import com.microsoft.azure.eventhubs.EventData;
   import com.microsoft.azure.eventprocessorhost.CloseReason;
   import com.microsoft.azure.eventprocessorhost.EventProcessorHost;
   import com.microsoft.azure.eventprocessorhost.EventProcessorOptions;
   import com.microsoft.azure.eventprocessorhost.ExceptionReceivedEventArgs;
   import com.microsoft.azure.eventprocessorhost.IEventProcessor;
   import com.microsoft.azure.eventprocessorhost.PartitionContext;

   import java.util.concurrent.ExecutionException;
   import java.util.function.Consumer;

   public class EventProcessorSample
   {
       public static void main(String args[]) throws InterruptedException, ExecutionException
       {
           String consumerGroupName = "$Default";
           String namespaceName = "----NamespaceName----";
           String eventHubName = "----EventHubName----";
           String sasKeyName = "----SharedAccessSignatureKeyName----";
           String sasKey = "----SharedAccessSignatureKey----";
           String storageConnectionString = "----AzureStorageConnectionString----";
           String storageContainerName = "----StorageContainerName----";
           String hostNamePrefix = "----HostNamePrefix----";
        
           ConnectionStringBuilder eventHubConnectionString = new ConnectionStringBuilder()
                .setNamespaceName(namespaceName)
                .setEventHubName(eventHubName)
                .setSasKeyName(sasKeyName)
                .setSasKey(sasKey);
        
           EventProcessorHost host = new EventProcessorHost(
                EventProcessorHost.createHostName(hostNamePrefix),
                eventHubName,
                consumerGroupName,
                eventHubConnectionString.toString(),
                storageConnectionString,
                storageContainerName);
        
           System.out.println("Registering host named " + host.getHostName());
           EventProcessorOptions options = new EventProcessorOptions();
           options.setExceptionNotification(new ErrorNotificationHandler());

           host.registerEventProcessor(EventProcessor.class, options)
           .whenComplete((unused, e) ->
           {
               if (e != null)
               {
                   System.out.println("Failure while registering: " + e.toString());
                   if (e.getCause() != null)
                   {
                       System.out.println("Inner exception: " + e.getCause().toString());
                   }
               }
           })
           .thenAccept((unused) ->
           {
               System.out.println("Press enter to stop.");
               try 
               {
                   System.in.read();
               }
               catch (Exception e)
               {
                   System.out.println("Keyboard read failed: " + e.toString());
               }
           })
           .thenCompose((unused) ->
           {
               return host.unregisterEventProcessor();
           })
           .exceptionally((e) ->
           {
               System.out.println("Failure while unregistering: " + e.toString());
               if (e.getCause() != null)
               {
                   System.out.println("Inner exception: " + e.getCause().toString());
               }
               return null;
           })
           .get(); // Wait for everything to finish before exiting main!
        
           System.out.println("End of sample");
       }
    ```
3. 次のコードを使用して、`EventProcessor` という名前のクラスをもう 1 つ作成します。
   
    ```java
    public static class EventProcessor implements IEventProcessor
    {
        private int checkpointBatchingCount = 0;

        // OnOpen is called when a new event processor instance is created by the host. 
        @Override
        public void onOpen(PartitionContext context) throws Exception
        {
            System.out.println("SAMPLE: Partition " + context.getPartitionId() + " is opening");
        }

        // OnClose is called when an event processor instance is being shut down. 
        @Override
        public void onClose(PartitionContext context, CloseReason reason) throws Exception
        {
            System.out.println("SAMPLE: Partition " + context.getPartitionId() + " is closing for reason " + reason.toString());
        }
        
        // onError is called when an error occurs in EventProcessorHost code that is tied to this partition, such as a receiver failure.
        @Override
        public void onError(PartitionContext context, Throwable error)
        {
            System.out.println("SAMPLE: Partition " + context.getPartitionId() + " onError: " + error.toString());
        }

        // onEvents is called when events are received on this partition of the Event Hub. 
        @Override
        public void onEvents(PartitionContext context, Iterable<EventData> events) throws Exception
        {
            System.out.println("SAMPLE: Partition " + context.getPartitionId() + " got event batch");
            int eventCount = 0;
            for (EventData data : events)
            {
                try
                {
                    System.out.println("SAMPLE (" + context.getPartitionId() + "," + data.getSystemProperties().getOffset() + "," +
                            data.getSystemProperties().getSequenceNumber() + "): " + new String(data.getBytes(), "UTF8"));
                    eventCount++;
                    
                    // Checkpointing persists the current position in the event stream for this partition and means that the next
                    // time any host opens an event processor on this event hub+consumer group+partition combination, it will start
                    // receiving at the event after this one. 
                    this.checkpointBatchingCount++;
                    if ((checkpointBatchingCount % 5) == 0)
                    {
                        System.out.println("SAMPLE: Partition " + context.getPartitionId() + " checkpointing at " +
                            data.getSystemProperties().getOffset() + "," + data.getSystemProperties().getSequenceNumber());
                        // Checkpoints are created asynchronously. It is important to wait for the result of checkpointing
                        // before exiting onEvents or before creating the next checkpoint, to detect errors and to ensure proper ordering.
                        context.checkpoint(data).get();
                    }
                }
                catch (Exception e)
                {
                    System.out.println("Processing failed for an event: " + e.toString());
                }
            }
            System.out.println("SAMPLE: Partition " + context.getPartitionId() + " batch size was " + eventCount + " for host " + context.getOwner());
        }
    }
    ```

このチュートリアルでは、EventProcessorHost の単一のインスタンスを使用します。 スループットを向上させるには、EventProcessorHost の複数のインスタンスを (なるべく別のコンピューターで) 実行することをお勧めします。  これにより、冗長性も提供されます。 このような場合、受信したイベントの負荷を分散するために、さまざまなインスタンスが自動的に連携します。 複数の受信側でぞれぞれ *すべて* のイベントを処理する場合、 **ConsumerGroup** 概念を使用する必要があります。 さまざまなコンピューターからイベントを受信する場合、デプロイしたコンピューター (またはロール) に基づいて EventProcessorHost インスタンスの名前を指定するのに便利です。

## <a name="publishing-messages-to-eventhub"></a>メッセージを EventHub に発行する

コンシューマーがメッセージを取得するためには、あらかじめパブリッシャーがそのメッセージをパーティションに発行する必要があります。 com.microsoft.azure.eventhubs.EventHubClient オブジェクトの sendSync() メソッドを使ってイベント ハブに同期的にメッセージを発行するとき、パーティション キーが指定されているかどうかに応じて、そのメッセージは特定のパーティションに送信されるか、または利用可能なすべてのパーティションにラウンド ロビン方式で分散されることに注目してください。

パーティション キーを表す文字列が指定されている場合、そのキーをハッシュすることによって、イベントの送信先となるパーティションが決定されます。

パーティション キーが設定されていない場合は、利用可能なすべてのパーティションに対してラウンド ロビン式にメッセージが分散されます

```java
// Serialize the event into bytes
byte[] payloadBytes = gson.toJson(messagePayload).getBytes(Charset.defaultCharset());

// Use the bytes to construct an {@link EventData} object
EventData sendEvent = EventData.create(payloadBytes);

// Transmits the event to event hub without a partition key
// If a partition key is not set, then we will round-robin to all topic partitions
eventHubClient.sendSync(sendEvent);

//  the partitionKey will be hash'ed to determine the partitionId to send the eventData to.
eventHubClient.sendSync(sendEvent, partitionKey);

```

## <a name="implementing-a-custom-checkpointmanager-for-eventprocessorhost-eph"></a>EventProcessorHost (EPH) 用のカスタム CheckpointManager を実装する

この API には、既定のチェックポイント マネージャーでは実際のユース ケースに対応できない場合を想定し、カスタム チェックポイント マネージャーを実装するメカニズムが用意されています。

既定のチェックポイント マネージャーには、Blob Storage が使用されます。しかし、EPH で使用されているチェックポイント マネージャーを独自の実装でオーバーライドすれば、その独自実装の基盤として任意のストアを使用することができます。

インターフェイス com.microsoft.azure.eventprocessorhost.ICheckpointManager を実装するクラスを作成します

チェックポイント マネージャー (com.microsoft.azure.eventprocessorhost.ICheckpointManager) のカスタム実装を使用します。

既定のチェックポイントのメカニズムを実装内でオーバーライドし、独自のデータ ストア (SQL Server、CosmosDB、Azure Redis Cache など) に基づいて独自のチェックポイントを実装できます。 チェックポイント マネージャーの実装の基盤として使用するストアには、コンシューマー グループのイベントを処理するすべての EPH インスタンスからアクセスできるストアを使用することをお勧めします。

環境内で利用できるどのデータストアでも使用できます。

com.microsoft.azure.eventprocessorhost.EventProcessorHost クラスには、EventProcessorHost のチェックポイント マネージャーをオーバーライドする際に使用できる 2 つのコンストラクターがあります。

## <a name="next-steps"></a>次の手順
このクイック スタートでは、イベント ハブからメッセージを受信する Java アプリケーションを作成しました。 Java を使用してイベント ハブにイベントを送信する方法については、[Java を使用してイベント ハブからイベントを送信すること](event-hubs-java-get-started-send.md)に関するページを参照してください。

<!-- Links -->
[Event Hubs overview]: event-hubs-what-is-event-hubs.md
[Azure Storage account]: ../storage/common/storage-create-storage-account.md
[Azure portal]: https://portal.azure.com
[Maven Package]: https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22azure-eventhubs-eph%22

<!-- Images -->
[11]: ./media/service-bus-event-hubs-get-started-receive-ephjava/create-eph-csharp2.png
[12]: ./media/service-bus-event-hubs-get-started-receive-ephjava/create-eph-csharp3.png
[無料アカウント]: https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio
