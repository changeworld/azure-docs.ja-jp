---
title: Azure Event Grid イベントに対するイベント ハンドラーとしての Service Bus のキューとトピック
description: Azure Event Grid のイベントに対するイベント ハンドラーとして Service Bus のキューとトピックを使用する方法を説明します。
ms.topic: conceptual
ms.date: 09/30/2021
ms.openlocfilehash: 676a9fcc6e260f2226e103ede26398b384e046ca
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/01/2021
ms.locfileid: "129354396"
---
# <a name="service-bus-queues-and-topics-as-event-handlers-for-azure-event-grid-events"></a>Azure Event Grid イベントに対するイベント ハンドラーとしての Service Bus のキューとトピック
イベント ハンドラーは、イベントの送信先となる場所です。 ハンドラーは、さらにいくつかのアクションを行ってイベントを処理します。 一部の Azure サービスは、イベントを処理するように自動的に構成されます。**Azure Service Bus** はその 1 つです。 

Event Grid からのイベントに対するハンドラーとして、Service Bus のキューまたはトピックを使用できます。 

## <a name="service-bus-queues"></a>Service Bus キュー

> [!NOTE]
> セッションで有効なキューは、Azure Event Grid イベントのイベント ハンドラーとしてはサポートされません
 
エンタープライズ アプリケーションでのバッファー処理または音声コマンドのシナリオで使用するために、Event Grid のイベントを Service Bus キューに直接ルーティングすることができます。

Azure portal で、イベント サブスクリプションを作成するときに、エンドポイントの種類として **[Service Bus キュー]** を選択し、 **[エンドポイントの選択]** をクリックして Service Bus キューを選択します。

### <a name="using-cli-to-add-a-service-bus-queue-handler"></a>CLI を使って Service Bus キューハンドラーを追加する

Azure CLI の場合、次の例でイベント グリッド トピックを Service Bus キューにサブスクライブして接続します。

```azurecli-interactive
az eventgrid event-subscription create \
    --name <my-event-subscription> \
    --source-resource-id /subscriptions/{SubID}/resourceGroups/{RG}/providers/Microsoft.EventGrid/topics/topic1 \
    --endpoint-type servicebusqueue \
    --endpoint /subscriptions/{SubID}/resourceGroups/TestRG/providers/Microsoft.ServiceBus/namespaces/ns1/queues/queue1
```

## <a name="service-bus-topics"></a>Service Bus トピック

Event Grid のイベントを Service Bus トピックに直接ルーティングして、Service Bus トピックやコマンドと制御メッセージング シナリオで Azure システム イベントを処理することができます。

Azure portal で、イベント サブスクリプションを作成するときに、エンドポイントの種類として **[Service Bus トピック]** を選択し、 **[エンドポイントの選択]** をクリックして Service Bus トピックを選択します。

### <a name="using-cli-to-add-a-service-bus-topic-handler"></a>CLI を使って Service Bus トピック ハンドラーを追加する

Azure CLI の場合、次の例では、イベント グリッド トピックをサブスクライブし、Service Bus トピックに接続します。

```azurecli-interactive
az eventgrid event-subscription create \
    --name <my-event-subscription> \
    --source-resource-id /subscriptions/{SubID}/resourceGroups/{RG}/providers/Microsoft.EventGrid/topics/topic1 \
    --endpoint-type servicebustopic \
    --endpoint /subscriptions/{SubID}/resourceGroups/TestRG/providers/Microsoft.ServiceBus/namespaces/ns1/topics/topic1
```

[!INCLUDE [event-grid-message-headers](./includes/event-grid-message-headers.md)]

ブローカー メッセージとして Service Bus のキューまたはトピックにイベントを送信する場合、ブローカー メッセージの `messageid` は内部システム ID です。

メッセージの内部システム ID は、イベントの再配信間で保持されるので、Service Bus のエンティティで **重複検出** をオンにすることで、配信の重複を回避できます。 Service Bus エンティティでの重複検出の期間を、イベントの有効期限 (TTL) または最大再試行期間のいずれか長い方に設定することをお勧めします。

## <a name="rest-examples-for-put"></a>REST の例 (PUT 用)

### <a name="service-bus-queue"></a>Service Bus キュー

```json
{
    "properties": 
    {
        "destination": 
        {
            "endpointType": "ServiceBusQueue",
            "properties": 
            {
                "resourceId": "/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.ServiceBus/namespaces/<SERVICE BUS NAMESPACE NAME>/queues/<SERVICE BUS QUEUE NAME>"
            }
        },
        "eventDeliverySchema": "EventGridSchema"
    }
}
```

### <a name="service-bus-queue---delivery-with-managed-identity"></a>Service Bus キュー - マネージド ID を使用した配信

```json
{
    "properties": {
        "deliveryWithResourceIdentity": 
        {
            "identity": 
            {
                "type": "SystemAssigned"
            },
            "destination": 
            {
                "endpointType": "ServiceBusQueue",
                "properties": 
                {
                    "resourceId": "/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.ServiceBus/namespaces/<SERVICE BUS NAMESPACE NAME>/queues/<SERVICE BUS QUEUE NAME>"
                }
            }
        },
        "eventDeliverySchema": "EventGridSchema"
    }
}
```

### <a name="service-bus-topic"></a>Service Bus トピック

```json
{
    "properties": 
    {
        "destination": 
        {
            "endpointType": "ServiceBusTopic",
            "properties": 
            {
                "resourceId": "/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.ServiceBus/namespaces/<SERVICE BUS NAMESPACE NAME>/topics/<SERVICE BUS TOPIC NAME>"
            }
        },
        "eventDeliverySchema": "EventGridSchema"
    }
}
```

### <a name="service-bus-topic---delivery-with-managed-identity"></a>Service Bus トピック - マネージド ID を使用した配信

```json
{
    "properties": 
    {
        "deliveryWithResourceIdentity": 
        {
            "identity": 
            {
                "type": "SystemAssigned"
            },
            "destination": 
            {
                "endpointType": "ServiceBusTopic",
                "properties": 
                {
                    "resourceId": "/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.ServiceBus/namespaces/<SERVICE BUS NAMESPACE NAME>/topics/<SERVICE BUS TOPIC NAME>"
                }
            }
        },
        "eventDeliverySchema": "EventGridSchema"
    }
}
```

## <a name="delivery-properties"></a>配信プロパティ
イベント サブスクリプションを使用すると、配信されたイベントに含まれる HTTP ヘッダーを設定できます。 この機能を使用すると、宛先に必要なカスタム ヘッダーを設定できます。 Azure Service Bus のキューとトピックに配信されるイベントにカスタム ヘッダーを設定できます。

Azure Service Bus は、単一メッセージを送信する際に、以下のメッセージプロパティの使用をサポートしています。 

| ヘッダー名 | ヘッダーの種類 |
| :-- | :-- |
| `MessageId` | 動的 |  
| `PartitionKey` | 静的または動的 |
| `SessionId` | 静的または動的 |
| `CorrelationId` | 静的または動的 |
| `Label` | 静的または動的 |
| `ReplyTo` | 静的または動的 | 
| `ReplyToSessionId` | 静的または動的 |
| `To` |静的または動的 |
| `ViaPartitionKey` | 静的または動的 |

> [!NOTE]
> - `MessageId` の既定値は、Event Grid のイベントの内部 ID です。 これはオーバーライドできます。 たとえば、「 `data.field` 」のように入力します。
> - 設定できるのは `SessionId` か `MessageId` のみです。 

詳細については、「[カスタム配信のプロパティ](delivery-properties.md)」を参照してください。 

## <a name="next-steps"></a>次のステップ
サポートされているイベント ハンドラーの一覧については、「[イベント ハンドラー](event-handlers.md)」を参照してください。 
