---
title: 可用性と一貫性 - Azure Event Hubs | Microsoft Docs
description: パーティションを使用して Azure Event Hubs で最大限の可用性と一貫性を実現する方法
ms.topic: article
ms.date: 01/25/2021
ms.custom: devx-track-csharp
ms.openlocfilehash: 325cc80daba2a44dedbd5e09ac4858ae2815c1cd
ms.sourcegitcommit: ba676927b1a8acd7c30708144e201f63ce89021d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/07/2021
ms.locfileid: "102425925"
---
# <a name="availability-and-consistency-in-event-hubs"></a>Event Hubs における可用性と一貫性
この記事では、Azure Event Hubs でサポートされる可用性と整合性に関する情報を提供します。 

## <a name="availability"></a>可用性
Azure Event Hubs により、データセンター内の複数の障害ドメインにまたがるクラスター間で、個々のマシン、さらにはラック全体の致命的な障害のリスクが分散されます。 透過的な障害検出およびフェールオーバー メカニズムが実装されるため、通常はそのような障害が発生したときに顕著な中断なしに、保証されたサービスレベル内でサービスが動作し続けます。 

[可用性ゾーン](../availability-zones/az-overview.md)を有効にして Event Hubs 名前空間が作成されている場合、物理的に分離された 3 つの施設間で停止リスクがさらに分散されます。また、サービスには、施設全体の致命的な損失全体に瞬時に対処するための十分な容量が予約されています。 詳細については、「[Azure Event Hubs geo ディザスター リカバリー](event-hubs-geo-dr.md)」を参照してください。

パーティションの指定なしでクライアント アプリケーションによってイベント ハブにイベントが送信されると、イベントはイベント ハブのパーティション間に自動的に分散されます。 何らかの理由でパーティションが使用できない場合、イベントは残りのパーティション間で分散されます。 この動作により、アップ タイムを最大にすることができます。 最大のアップ タイム時間を必要とするユース ケースでは、特定のパーティションにイベントを送信する代わりに、このモデルを使用することをお勧めします。 

### <a name="availability-considerations-when-using-a-partition-id-or-key"></a>パーティション ID またはキーを使用する場合の可用性に関する考慮事項
パーティション ID またはパーティション キーの使用は任意です。 使用するかどうかを慎重に検討してください。 イベントの発行時にパーティション ID またはキーを指定しない場合、Event Hubs によってパーティション間の負荷が分散されます。 パーティション ID またはキーを使用すると、これらのパーティションでは単一のノードに対する可用性が必要になるため、時間が経つにつれて障害が発生する可能性があります。 たとえば、計算ノードの再起動またはパッチの適用が必要になる場合があります。 そのため、パーティション ID またはキーを設定した場合にそのパーティションがなんらかの理由で使用不能になると、そのパーティション内のデータにアクセスしようとしても失敗します。 高可用性が最も重要な場合、パーティション ID またはキーを指定しないでください。 その場合、内部の負荷分散アルゴリズムを利用してイベントがパーティションに送信されます。 このシナリオでは、可用性 (パーティション ID またはキーなし) と整合性 (イベントを特定のパーティションに固定) のどちらを優先するかを明確に選択することになります。 パーティション ID またはキーを使用すると、イベント ハブの可用性がパーティション レベルにダウングレードされます。 

### <a name="availability-considerations-when-handling-delays-in-processing-events"></a>イベントの処理の遅れに対処する際の可用性に関する考慮事項
もう 1 つの考慮事項は、イベントの処理の遅れに対処するコンシューマー アプリケーションに関するものです。 場合によっては、コンシューマー アプリケーションでは、処理が遅れないようにするよりも、データを破棄して再試行した方が良いこともあります。前者では、ダウンストリームの処理がさらに遅れる可能性があります。 たとえば、株式相場表示機では最新のデータが揃うまで待つ方が適切ですが、ライブ チャットや VOIP のシナリオでは不完全でもデータを素早く用意する必要があります。

こうした可用性に関する考慮事項を踏まえて、これらのシナリオでは、コンシューマー アプリケーションは次のエラー処理方法のいずれかを選択できます。

- 停止 (問題が解決するまでイベント ハブからの読み取りを停止する)
- 破棄 (重要ではないメッセージを破棄する)
- 再試行 (表示されるメッセージが適切になるまで再試行する)


## <a name="consistency"></a>一貫性
シナリオによっては、イベントの順序付けが重要になる場合があります。 たとえば、バックエンド システムで、delete コマンドの前に update コマンドを処理したいとします。 このシナリオでは、順序が維持されるように、クライアント アプリケーションによって特定のパーティションにイベントが送信されます。 コンシューマー アプリケーションによってパーティションからこれらのイベントが使用されるときに、それらは順番どおりに読み取られます。 

この構成では、送信先の特定のパーティションが使用できない場合は、エラー応答が受信される点に注意してください。 比較のポイントとして、1 つのパーティションにアフィニティがない場合は、Event Hubs サービスによって次の利用可能なパーティションにイベントが送信されます。


## <a name="appendix"></a>付録

### <a name="send-events-to-a-specific-partition"></a>特定のパーティションにイベントを送信する
このセクションでは、C#、Java、Python、JavaScript を使用して、特定のパーティションにイベントを送信する方法について説明します。 

### <a name="net"></a>[.NET](#tab/dotnet)
イベント バッチを (パーティション ID またはキーを設定せずに) イベント ハブに送信する方法を示す完全なサンプル コードについては、「[Azure Event Hubs との間でイベントを送受信する - .NET (Azure.Messaging.EventHubs)](event-hubs-dotnet-standard-getstarted-send.md)」を参照してください。

特定のパーティションにイベントを送信するには、[CreateBatchOptions](//dotnet/api/azure.messaging.eventhubs.producer.createbatchoptions) に `PartitionId` または `PartitionKey` を指定して、[EventHubProducerClient.CreateBatchAsync](/dotnet/api/azure.messaging.eventhubs.producer.eventhubproducerclient.createbatchasync#Azure_Messaging_EventHubs_Producer_EventHubProducerClient_CreateBatchAsync_Azure_Messaging_EventHubs_Producer_CreateBatchOptions_System_Threading_CancellationToken_) メソッドを使用してバッチを作成します。 次のコードでは、パーティション キーを指定することによって、特定のパーティションにイベントのバッチを送信します。 

```csharp
var batchOptions = new CreateBatchOptions { PartitionKey = "cities" };
using var eventBatch = await producer.CreateBatchAsync(batchOptions);
```

また、[SendEventOptions](/dotnet/api/azure.messaging.eventhubs.producer.sendeventoptions) に **PartitionId** または **PartitionKey** を指定して、[EventHubProducerClient.SendAsync](/dotnet/api/azure.messaging.eventhubs.producer.eventhubproducerclient.sendasync#Azure_Messaging_EventHubs_Producer_EventHubProducerClient_SendAsync_System_Collections_Generic_IEnumerable_Azure_Messaging_EventHubs_EventData__Azure_Messaging_EventHubs_Producer_SendEventOptions_System_Threading_CancellationToken_) メソッドを使用することもできます。

```csharp
var sendEventOptions  = new SendEventOptions { PartitionKey = "cities" };
// create the events array
producer.SendAsync(events, sendOptions)
```

### <a name="java"></a>[Java](#tab/java)
イベント バッチを (パーティション ID またはキーを設定せずに) イベント ハブに送信する方法を示す完全なサンプル コードについては、「[Java を使用して Azure Event Hubs との間でイベントを送受信する (azure-messaging-eventhubs)](event-hubs-java-get-started-send.md)」を参照してください。

特定のパーティションにイベントを送信するには、[createBatchOptions](/java/api/com.azure.messaging.eventhubs.models.createbatchoptions) に **パーティション ID** または **パーティション キー** を指定して、[createBatch](/java/api/com.azure.messaging.eventhubs.eventhubproducerclient.createbatch) メソッドを使用してバッチを作成します。 次のコードでは、パーティション キーを指定することによって、特定のパーティションにイベントのバッチを送信します。 

```java
CreateBatchOptions batchOptions = new CreateBatchOptions();
batchOptions.setPartitionKey("cities");
```

また、[SendOptions](/java/api/com.azure.messaging.eventhubs.models.sendoptions) に **パーティション ID** または **パーティション キー** を指定して、[EventHubProducerClient.send](/java/api/com.azure.messaging.eventhubs.eventhubproducerclient.send#com_azure_messaging_eventhubs_EventHubProducerClient_send_java_lang_Iterable_com_azure_messaging_eventhubs_EventData__com_azure_messaging_eventhubs_models_SendOptions_) メソッドを使用することもできます。

```java
List<EventData> events = Arrays.asList(new EventData("Melbourne"), new EventData("London"), new EventData("New York"));
SendOptions sendOptions = new SendOptions();
sendOptions.setPartitionKey("cities");
producer.send(events, sendOptions);
```

### <a name="python"></a>[Python](#tab/python) 
イベント バッチを (パーティション ID またはキーを設定せずに) イベント ハブに送信する方法を示す完全なサンプル コードについては、「[Python (azure-eventhub) を使用して Event Hubs との間でイベントを送受信する](event-hubs-python-get-started-send.md)」を参照してください。

特定のパーティションにイベントを送信するには、[`EventHubProducerClient.create_batch`](/python/api/azure-eventhub/azure.eventhub.eventhubproducerclient#create-batch---kwargs-) メソッドを使用してバッチを作成するときに、`partition_id` または `partition_key` 指定します。 次に、[`EventHubProducerClient.send_batch`](/python/api/azure-eventhub/azure.eventhub.aio.eventhubproducerclient#send-batch-event-data-batch--typing-union-azure-eventhub--common-eventdatabatch--typing-list-azure-eventhub-) メソッドを使用して、イベント ハブのパーティションにバッチを送信します。 

```python
event_data_batch = await producer.create_batch(partition_key='cities')
```

`partition_id` または `partition_key` パラメーターの値を指定することにより、[EventHubProducerClient.send_batch](/python/api/azure-eventhub/azure.eventhub.eventhubproducerclient#send-batch-event-data-batch----kwargs-) メソッドを使用することもできます。

```python
producer.send_batch(event_data_batch, partition_key="cities")
```


### <a name="javascript"></a>[JavaScript](#tab/javascript)
イベント バッチを (パーティション ID またはキーを設定せずに) イベント ハブに送信する方法を示す完全なサンプル コードについては、「[JavaScript (azure/event-hubs) を使用して Event Hubs との間でイベントを送受信する](event-hubs-node-get-started-send.md)」を参照してください。

特定のパーティションにイベントを送信するには、`partitionId` または `partitionKey` を指定して、[EventHubProducerClient.CreateBatchOptions](/javascript/api/@azure/event-hubs/eventhubproducerclient#createBatch_CreateBatchOptions_) オブジェクトを使用して[バッチを作成します](/javascript/api/@azure/event-hubs/eventhubproducerclient#createBatch_CreateBatchOptions_)。 次に、[EventHubProducerClient.SendBatch](/javascript/api/@azure/event-hubs/eventhubproducerclient#sendBatch_EventDataBatch__OperationOptions_) メソッドを使用して、イベントハブにバッチを送信します。 

次の例を参照してください。

```javascript
const batchOptions = { partitionKey = "cities"; };
const batch = await producer.createBatch(batchOptions);
```

また、[SendBatchOptions](/javascript/api/@azure/event-hubs/sendbatchoptions) に **パーティション ID** または **パーティション キー** を指定して、[EventHubProducerClient.sendBatch](/javascript/api/@azure/event-hubs/eventhubproducerclient#sendBatch_EventData____SendBatchOptions_) メソッドを使用することもできます。

```javascript
const sendBatchOptions = { partitionKey = "cities"; };
// prepare events
producer.sendBatch(events, sendBatchOptions);
```

---


## <a name="next-steps"></a>次のステップ
Event Hubs の詳細については、次のリンク先を参照してください:

* [Event Hubs サービスの概要](./event-hubs-about.md)
* [イベント ハブの作成](event-hubs-create.md)
