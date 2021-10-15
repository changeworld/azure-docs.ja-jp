---
title: Azure Event Grid イベントに対するイベント ハンドラーとしてのイベント ハブ
description: Azure Event Grid イベントのイベント ハンドラーとしてイベント ハブを使用する方法について説明します。
ms.topic: conceptual
ms.date: 09/30/2021
ms.openlocfilehash: 590364d759fe6f628cfaa306d162d00f575c0622
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/01/2021
ms.locfileid: "129354429"
---
# <a name="event-hub-as-an-event-handler-for-azure-event-grid-events"></a>Azure Event Grid イベントに対するイベント ハンドラーとしてのイベント ハブ
イベント ハンドラーは、イベントの送信先となる場所です。 ハンドラーでは、イベントを処理するアクションが実行されます。 一部の Azure サービスは、イベントを処理するように自動的に構成されます。**Azure Event Hubs** はその 1 つです。 

Event Grid からのイベントが速すぎて、ソリューションで処理しきれない場合は、**Event Hubs** を使用します。 イベントがイベント ハブに格納されたら、アプリケーションでは独自のスケジュールでイベント ハブからイベントを取得して処理できます。 イベント処理をスケーリングして、受信イベントを処理することができます。

## <a name="tutorials"></a>チュートリアル
次の例を参照してください。 

|タイトル  |説明  |
|---------|---------|
| [クイック スタート: Azure CLI を使用してカスタム イベントを Azure Event Hubs にルーティングする](custom-event-to-eventhub.md) | アプリケーションで処理するためにカスタム イベントをイベント ハブに送信します。 |
| [Resource Manager テンプレート:Event Grid カスタム トピックを作成し、イベント ハブにイベントを送信する](https://github.com/Azure/azure-quickstart-templates/tree/master/quickstarts/microsoft.eventgrid/event-grid-event-hubs-handler)| カスタム トピックのサブスクリプションを作成する Resource Manager テンプレート。 Azure Event Hubs にイベントを送信します。 |

[!INCLUDE [event-grid-message-headers](./includes/event-grid-message-headers.md)]


## <a name="rest-examples-for-put"></a>REST の例 (PUT 用)


### <a name="event-hub"></a>イベント ハブ

```json
{
  "properties": 
  {
    "destination": 
    {
      "endpointType": "EventHub",
      "properties": 
      {
        "resourceId": "/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventHub/namespaces/<EVENT HUBS NAMESPACE NAME>/eventhubs/<EVENT HUB NAME>"
      }
    },
    "eventDeliverySchema": "EventGridSchema"
  }
}
```

### <a name="event-hub---delivery-with-managed-identity"></a>イベント ハブ - マネージド ID を使用した配信

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
        "endpointType": "EventHub",
        "properties": 
        {
          "resourceId": "/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventHub/namespaces/<EVENT HUBS NAMESPACE NAME>/eventhubs/<EVENT HUB NAME>"
        }
      }
    },
    "eventDeliverySchema": "EventGridSchema"
  }
}
```

## <a name="delivery-properties"></a>配信プロパティ
イベント サブスクリプションを使用すると、配信されたイベントに含まれる HTTP ヘッダーを設定できます。 この機能を使用すると、宛先に必要なカスタム ヘッダーを設定できます。 Azure Event Hubs に配信されるイベントにカスタム ヘッダーを設定できます。

イベントをイベント ハブ内の特定のパーティションに発行する必要がある場合は、イベント サブスクリプションに `ParitionKey` プロパティを設定して、対象のイベント ハブ パーティションを識別するパーティション キーを指定します。

| ヘッダー名 | ヘッダーの種類 |
| :-- | :-- |
|`PartitionKey` | スタティック |

詳細については、「[カスタム配信のプロパティ](delivery-properties.md)」を参照してください。 

## <a name="next-steps"></a>次のステップ
サポートされているイベント ハンドラーの一覧については、「[イベント ハンドラー](event-handlers.md)」を参照してください。 
