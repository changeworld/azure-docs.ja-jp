---
title: Azure Event Hubs のイベントにカスタム データを追加する
description: この記事では、Azure Event Hubs のイベントにカスタム データを追加する方法について説明します。
ms.topic: how-to
ms.date: 03/19/2021
ms.openlocfilehash: 3362b6ac4b0d624969aa3ba36d2ebc83b8777cf5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "104893401"
---
# <a name="add-custom-data-to-events-in-azure-event-hubs"></a>Azure Event Hubs のイベントにカスタム データを追加する
イベントは主に不透明なバイトのセットで構成されるため、その処理方法について、これらのイベントのコンシューマーが、情報に基づいた決定を行うことが困難な場合があります。 イベントの発行元がより優れたコンテキストをコンシューマーに提供できるようにするために、イベントに、一連のキーと値のペアの形式でカスタム メタデータを含めることもできます。 メタデータを含める一般的なシナリオの 1 つは、イベントに含まれるデータの種類に関するヒントを提供して、コンシューマーがその形式を理解し、適切に逆シリアル化できるようにすることです。

> [!NOTE]
> このメタデータは、Event Hubs サービスによって使用されることも、何らかの方法で解釈されることもありません。これは、イベントの発行元とコンシューマーの間で調整を行うためだけに存在します。 

以下のセクションでは、さまざまなプログラミング言語でイベントにカスタム データを追加する方法について説明します。 

## <a name="net"></a>.NET 

```csharp
var eventBody = new BinaryData("Hello, Event Hubs!");
var eventData = new EventData(eventBody);
eventData.Properties.Add("EventType", "com.microsoft.samples.hello-event");
eventData.Properties.Add("priority", 1);
eventData.Properties.Add("score", 9.0);
```

完全なコード サンプルについては、「[カスタム メタデータとともにイベントを発行する](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/eventhub/Azure.Messaging.EventHubs/samples/Sample04_PublishingEvents.md#publishing-events-with-custom-metadata)」を参照してください。

## <a name="java"></a>Java

```java
EventData firstEvent = new EventData("EventData Sample 1".getBytes(UTF_8));
firstEvent.getProperties().put("EventType", "com.microsoft.samples.hello-event");
firstEvent.getProperties().put("priority", 1);
firstEvent.getProperties().put("score", 9.0);
```

完全なコード サンプルについては、「[カスタム メタデータとともにイベントを発行する](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/eventhubs/azure-messaging-eventhubs/src/samples/java/com/azure/messaging/eventhubs/PublishEventsWithCustomMetadata.java)」を参照してください。


## <a name="python"></a>Python

```python
event_data = EventData('Message with properties')
event_data.properties = {'event-type': 'com.microsoft.samples.hello-event', 'priority': 1, "score": 9.0}
```

完全なコード サンプルについては、[イベント データ バッチとプロパティの送信](https://github.com/Azure/azure-sdk-for-python/blob/azure-eventhub_5.3.1/sdk/eventhub/azure-eventhub/samples/async_samples/send_async.py)に関するページを参照してください。

## <a name="javascript"></a>JavaScript

```javascript
let eventData = { body: "First event", properties: { "event-type": "com.microsoft.samples.hello-event", "priority": 1, "score": 9.0  } };
```


## <a name="next-steps"></a>次のステップ
次のクイック スタートおよびサンプルを参照してください。 

- クイックスタート: [.NET](event-hubs-dotnet-standard-getstarted-send.md)、[Java](event-hubs-java-get-started-send.md)、[Python](event-hubs-python-get-started-send.md)、[JavaScript](event-hubs-node-get-started-send.md)
- GitHub のサンプル: [.NET](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/eventhub/Azure.Messaging.EventHubs/samples)、[Java](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/eventhubs/azure-messaging-eventhubs/src/samples)、[Python](https://github.com/Azure/azure-sdk-for-python/blob/azure-eventhub_5.3.1/sdk/eventhub/azure-eventhub/samples)、[JavaScript](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/eventhub/event-hubs/samples/javascript)、[TypeScript](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/eventhub/event-hubs/samples/typescript)
