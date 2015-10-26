<properties 
   pageTitle="Azure Event Hubs API の概要 | Microsoft Azure"
   description="主要な Event Hubs .NET クライアント API のまとめ。"
   services="event-hubs"
   documentationCenter="na"
   authors="sethmanheim"
   manager="timlt"
   editor="" />
<tags 
   ms.service="event-hubs"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="10/14/2015"
   ms.author="sethm" />

# Event Hubs API 概要

この記事は主要な Event Hubs .NET クライアント API についてまとめてあります。2 つのカテゴリがあります。管理 API とランタイム API です。ランタイム API はメッセージの送受信に必要なすべての操作で構成されています。管理操作では、エンティティを作成、更新、削除することで Event Hubs エンティティの状態を管理できます。

監視シナリオは管理とランタイムの両方にまたがります。.NET API に関する詳細なリファレンス ドキュメントについては、「[.NET クラス ライブラリ](https://msdn.microsoft.com/library/azure/mt419900.aspx)」リファレンスと「[EventProcessorHost API](https://msdn.microsoft.com/library/azure/mt445521.aspx)」リファレンスを参照してください。

## 管理 API

次の管理操作を実行するには、Service Bus 名前空間の**管理**権限が必要になります。

### 作成

```
// Create the Event Hub
EventHubDescription ehd = new EventHubDescription(eventHubName);
ehd.PartitionCount = SampleManager.numPartitions;
namespaceManager.CreateEventHubAsync(ehd).Wait();
```

### 更新

```
EventHubDescription ehd = await namespaceManager.GetEventHubAsync(eventHubName);

// Create a customer SAS rule with Manage permissions
ehd.UserMetadata = "Some updated info";
string ruleName = "myeventhubmanagerule";
string ruleKey = SharedAccessAuthorizationRule.GenerateRandomKey();
ehd.Authorization.Add(new SharedAccessAuthorizationRule(ruleName, ruleKey, new AccessRights[] {AccessRights.Manage, AccessRights.Listen, AccessRights.Send} )); 
namespaceManager.UpdateEventHubAsync(ehd).Wait();
```

### 削除

```
namespaceManager.DeleteEventHubAsync("Event Hub name").Wait();
```

## ランタイム API

### 発行元の作成

```
// EventHubClient model (uses implicit factory instance, so all links on same connection)
EventHubClient eventHubClient = EventHubClient.Create("Event Hub name");
```

### メッセージの発行

```
// Create the device/temperature metric
MetricEvent info = new MetricEvent() { DeviceId = random.Next(SampleManager.NumDevices), Temperature = random.Next(100) };
EventData data = new EventData(new byte[10]); // Byte array
EventData data = new EventData(Stream); // Stream 
EventData data = new EventData(info, serializer) //Object and serializer 
    {
       PartitionKey = info.DeviceId.ToString()
    };

// Set user properties if needed
data.Properties.Add("Type", "Telemetry_" + DateTime.Now.ToLongTimeString());

// Send single message async
await client.SendAsync(data);
```

### コンシューマーの作成

```
// Create the Event Hub client
EventHubClient eventHubClient = EventHubClient.Create(EventHubName);

// Get the default subscriber group
EventHubSubscriberGroup defaultSubscriberGroup = eventHubClient.GetDefaultSubscriberGroup();

// All messages
EventHubReceiver consumer = await defaultConsumerGroup.CreateReceiverAsync(shardId: index);

// From one day ago
EventHubReceiver consumer = await defaultConsumerGroup.CreateReceiverAsync(shardId: index, startingDateTimeUtc:DateTime.Now.AddDays(-1));
                        
// From specific offset, -1 means oldest
EventHubReceiver consumer = await defaultConsumerGroup.CreateReceiverAsync(shardId: index,startingOffset:-1); 
```

### メッセージの使用

```
var message = await consumer.ReceiveAsync();

// Provide a serializer
var info = message.GetBody<Type>(Serializer)
                                    
// Get a byte[]
var info = message.GetBytes(); 
msg = UnicodeEncoding.UTF8.GetString(info);
```

## イベント プロセッサ ホスト API

これらの API は、利用可能な worker にシャードを分散することによって、利用不可になる可能性がある worker プロセスに回復性を与えます。

```
// Checkpointing is done within the SimpleEventProcessor and on a per-consumerGroup per-partition basis, workers resume from where they last left off.
// Use the EventData.Offset value for checkpointing yourself, this value is unique per partition.
string eventHubConnectionString = System.Configuration.ConfigurationManager.AppSettings["Microsoft.ServiceBus.ConnectionString"];
string blobConnectionString = System.Configuration.ConfigurationManager.AppSettings["AzureStorageConnectionString"]; // Required for checkpoint/state

EventHubDescription eventHubDescription = new EventHubDescription(EventHubName);
EventProcessorHost host = new EventProcessorHost(WorkerName, EventHubName, defaultConsumerGroup.GroupName, eventHubConnectionString, blobConnectionString);
            host.RegisterEventProcessorAsync<SimpleEventProcessor>();

// To close
host.UnregisterEventProcessorAsync().Wait();   
```

[IEventProcessor](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.ieventprocessor.aspx) インターフェイスは次のように定義されています。

```
public class SimpleEventProcessor : IEventProcessor
{
    IDictionary<string, string> map;
    PartitionContext partitionContext;

    public SimpleEventProcessor()
    {
        this.map = new Dictionary<string, string>();
    }

    public Task OpenAsync(PartitionContext context)
    {
        this.partitionContext = context;

        return Task.FromResult<object>(null);
    }

    public async Task ProcessEventsAsync(PartitionContext context, IEnumerable<EventData> messages)
    {
        foreach (EventData message in messages)
        {
            Process messages here
        }
        
        // Checkpoint when appropriate
        await context.CheckpointAsync();

    }


    public async Task CloseAsync(PartitionContext context, CloseReason reason)
    {
        if (reason == CloseReason.Shutdown)
        {
            await context.CheckpointAsync();
        }
    }
}
```

## 次のステップ

Event Hubs シナリオに関する詳細については、次のリンク先を参照してください。

- [Azure Event Hubs とは](event-hubs-what-is-event-hubs.md)
- [Event Hubs の概要](event-hubs-overview.md)
- [Event Hubs のプログラミング ガイド](event-hubs-programming-guide.md)
- [Event Hubs コード サンプル](http://code.msdn.microsoft.com/site/search?query=event hub&f[0].Value=event hubs&f[0].Type=SearchText&ac=5)

.NET API リファレンスはここにあります。

- [Service Bus と Event Hubs の .NET API リファレンス](https://msdn.microsoft.com/library/azure/mt419900.aspx)
- [イベント プロセッサ ホスト API リファレンス](https://msdn.microsoft.com/library/azure/mt445521.aspx)

<!---HONumber=Oct15_HO3-->