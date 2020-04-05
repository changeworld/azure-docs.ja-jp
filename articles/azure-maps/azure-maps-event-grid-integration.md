---
title: Event Grid を使用してマップ イベントに対応する | Microsoft Azure Maps
description: この記事では、Event Grid を使用して Microsoft Azure Maps イベントに対応する方法について説明します。
author: philmea
ms.author: philmea
ms.date: 02/08/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.custom: mvc
ms.openlocfilehash: 9c9483af191e5439af0c0b5e433187d6475c178c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/28/2020
ms.locfileid: "80335714"
---
# <a name="react-to-azure-maps-events-by-using-event-grid"></a>Event Grid を使用して Azure Maps イベントに反応する 

Azure Maps は Azure Event Grid と統合されているため、ユーザーは他のサービスにイベント通知を送信して、ダウンストリームのプロセスをトリガーすることができます。 この記事の目的は、ご利用のビジネス アプリケーションを構成して Azure Maps イベントをリッスンできるようにすることです。 これにより、ユーザーは信頼性が高く、スケーラブルで安全な方法で重要なイベントに対応することができます。 たとえば、ユーザーは、デバイスがジオフェンスに入るたびに、データベースの更新、チケットの作成、電子メール通知の配信を実行するアプリケーションを構築できます。

Azure Event Grid は、発行 - サブスクライブ モデルを使用する、フル マネージドのイベント ルーティング サービスです。 Event Grid には、[Azure Functions](https://docs.microsoft.com/azure/azure-functions/functions-overview) や [Azure Logic Apps](https://docs.microsoft.com/azure/azure-functions/functions-overview) のような Azure サービスのサポートが組み込まれています。 また、Webhook を使用することで、Azure 以外のサービスにイベント アラートを配信することもできます。 Event Grid がサポートするイベント ハンドラーの完全な一覧については、「[Azure Event Grid の概要](https://docs.microsoft.com/azure/event-grid/overview)」をご覧ください。


![Azure Event Grid の機能モデル](./media/azure-maps-event-grid-integration/azure-event-grid-functional-model.png)


## <a name="azure-maps-events-types"></a>Azure Maps のイベントの種類

Event Grid は、[イベント サブスクリプション](https://docs.microsoft.com/azure/event-grid/concepts#event-subscriptions)を使って、イベント メッセージをサブスクライバーにルーティングします。 Azure Maps アカウントから出力されるイベントの種類は次のとおりです。 

| イベントの種類 | 説明 |
| ---------- | ----------- |
| Microsoft.Maps.GeofenceEntered | 受信した座標が指定されたジオフェンスの外部から内部に移動したときに発生します |
| Microsoft.Maps.GeofenceExited | 受信した座標が指定されたジオフェンスの内部から外部に移動したときに発生します |
| Microsoft.Maps.GeofenceResult | 状態に関係なく、ジオフェンシング クエリから結果が返されるたびに発生します |

## <a name="event-schema"></a>イベント スキーマ

次の例では、GeofenceResult のスキーマを示します。

```JSON
{   
   "id":"451675de-a67d-4929-876c-5c2bf0b2c000", 
   "topic":"/subscriptions/{subscriptionId}/resourceGroups/{resourceGroup}/providers/Microsoft.Maps/accounts/{accountName}", 
   "subject":"/spatial/geofence/udid/{udid}/id/{eventId}", 
   "data":{   
      "geometries":[   
         {   
            "deviceId":"device_1", 
            "udId":"1a13b444-4acf-32ab-ce4e-9ca4af20b169", 
            "geometryId":"1", 
            "distance":999.0, 
            "nearestLat":47.609833, 
            "nearestLon":-122.148274 
         }, 
         {   
            "deviceId":"device_1", 
            "udId":"1a13b444-4acf-32ab-ce4e-9ca4af20b169", 
            "geometryId":"2", 
            "distance":999.0, 
            "nearestLat":47.621954, 
            "nearestLon":-122.131841 
         } 
      ], 
      "expiredGeofenceGeometryId":[   
      ], 
      "invalidPeriodGeofenceGeometryId":[   
      ] 
   }, 
   "eventType":"Microsoft.Maps.GeofenceResult", 
   "eventTime":"2018-11-08T00:52:08.0954283Z", 
   "metadataVersion":"1", 
   "dataVersion":"1.0" 
}
```

## <a name="tips-for-consuming-events"></a>イベントの使用に関するヒント

Azure Maps ジオフェンス イベントを処理するアプリケーションでは、いくつかの推奨される手法に従う必要があります。

* 同じイベント ハンドラーにイベントをルーティングするように、複数のサブスクリプションを構成します。 イベントが特定のソースからのものであると想定しないことが重要です。 常にメッセージ トピックをチェックして、メッセージが予期されたソースからのものであることを確認します。
* 応答ヘッダーの `X-Correlation-id` フィールドを使用して、オブジェクトに関する情報が最新かどうかを確認します。 メッセージは、順不同で、または遅延の後に、到着する場合があります。
* モード パラメーターを `EnterAndExit` に設定して Geofence API 内の GET または POST 要求を呼び出すと、前回の Geofence API 呼び出しから状態が変化したジオフェンス内のジオメトリごとに、Enter または Exit イベントが生成されます。

## <a name="next-steps"></a>次のステップ

ジオフェンシングを使用して建設現場での操作を制御する方法について詳しくは、以下をご覧ください。

> [!div class="nextstepaction"] 
> [Azure Maps を使用してジオフェンスを設定する](tutorial-geofence.md)
