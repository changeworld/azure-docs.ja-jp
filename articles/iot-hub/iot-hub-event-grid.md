---
title: "Azure IoT Hub と Event Grid | Microsoft Docs"
description: "Azure Event Grid を使い、IoT Hub で発生したアクションに基づいてプロセスをトリガーします。"
services: iot-hub
documentationcenter: 
author: kgremban
manager: timlt
editor: 
ms.service: iot-hub
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/14/2018
ms.author: kgremban
ms.openlocfilehash: 6123039ba5eeb720e0ca590fa69af915da91367c
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/21/2018
---
# <a name="react-to-iot-hub-events-by-using-event-grid-to-trigger-actions---preview"></a>Event Grid を使用し IoT Hub のイベントに対応してアクションをトリガーする - プレビュー

他のサービスにイベント通知を送信して、ダウンストリームのプロセスをトリガーできるように、Azure IoT Hub は Azure Event Grid と統合します。 信頼性が高く、スケーラブルで、安全な方法により、重大なイベントに対応できるよう、IoT Hub のイベントをリッスンするようにビジネス アプリケーションを構成します。 たとえば、新しい IoT デバイスが IoT Hub に登録されるたびに、データベースの更新、チケットの作成、メール通知の配信などの複数のアクションを実行するよう、アプリケーションを構築します。 

[Azure Event Grid][lnk-eg-overview] は、発行-サブスクライブ モデルを使う、完全管理型のイベント ルーティング サービスです。 Event Grid は、[Azure Functions](../azure-functions/functions-overview.md) や [Azure Logic Apps](../logic-apps/logic-apps-what-are-logic-apps.md) などの Azure s サービスの組み込みサポートを備えており、webhook を使って Azure 以外のサービスにイベント アラートを配信できます。 Event Grid がサポートするイベント ハンドラーの完全な一覧については、「[Azure Event Grid の概要][lnk-eg-overview]」をご覧ください。 

![Azure Event Grid のアーキテクチャ](./media/iot-hub-event-grid/event-grid-functional-model.png)

## <a name="regional-availability"></a>リージョン別の提供状況

Event Grid の統合はパブリック プレビュー段階であり、利用できるリージョンの数は限られます。 統合は、次のリージョンにある IoT Hub で機能します。

* 米国中央部
* 米国東部
* 米国東部 2
* 米国中西部
* 米国西部
* 米国西部 2

## <a name="event-types"></a>イベントの種類

IoT Hub は次のイベントの種類を発行します。 

| イベントの種類 | [説明] |
| ---------- | ----------- |
| Microsoft.Devices.DeviceCreated | デバイスが IoT Hub に登録されると発行されます。 |
| Microsoft.Devices.DeviceDeleted | デバイスが IoT Hub から削除されると発行されます。 | 

各 IoT Hub から発行するイベントを構成するには、Azure Portal または Azure CLI を使います。 例については、チュートリアル「[Logic Apps を使用して Azure IoT Hub イベントに関する電子メール通知を送信する](../event-grid/publish-iot-hub-events-to-logic-apps.md)」をお試しください。 

## <a name="event-schema"></a>イベント スキーマ

IoT Hub イベントには、デバイスのライフサイクルの変更に対応するために必要なすべての情報が含まれます。 eventType プロパティが **Microsoft.Devices** で始まることをチェックすることにより、IoT Hub イベントを識別できます。 Event Grid イベント プロパティの使い方について詳しくは、「[Event Grid イベント スキーマ](../event-grid/event-schema.md)」をご覧ください。

### <a name="device-created-schema"></a>デバイスで作成されたスキーマ

次の例では、デバイス作成スキーマを示します。 

```json
[{
  "id": "56afc886-767b-d359-d59e-0da7877166b2",
  "topic": "/SUBSCRIPTIONS/<subscription ID>/RESOURCEGROUPS/<resource group name>/PROVIDERS/MICROSOFT.DEVICES/IOTHUBS/<hub name>",
  "subject": "devices/LogicAppTestDevice",
  "eventType": "Microsoft.Devices.DeviceCreated",
  "eventTime": "2018-01-02T19:17:44.4383997Z",
  "data": {
    "twin": {
      "deviceId": "LogicAppTestDevice",
      "etag": "AAAAAAAAAAE=",
      "status": "enabled",
      "statusUpdateTime": "0001-01-01T00:00:00",
      "connectionState": "Disconnected",
      "lastActivityTime": "0001-01-01T00:00:00",
      "cloudToDeviceMessageCount": 0,
      "authenticationType": "sas",
      "x509Thumbprint": {
        "primaryThumbprint": null,
        "secondaryThumbprint": null
      },
      "version": 2,
      "properties": {
        "desired": {
          "$metadata": {
            "$lastUpdated": "2018-01-02T19:17:44.4383997Z"
          },
          "$version": 1
        },
        "reported": {
          "$metadata": {
            "$lastUpdated": "2018-01-02T19:17:44.4383997Z"
          },
          "$version": 1
        }
      }
    },
    "hubName": "egtesthub1",
    "deviceId": "LogicAppTestDevice",
    "operationTimestamp": "2018-01-02T19:17:44.4383997Z",
    "opType": "DeviceCreated"
  },
  "dataVersion": "",
  "metadataVersion": "1"
}]
```

各プロパティの詳しい説明については、「[IoT Hub の Azure Event Grid イベント スキーマ](../event-grid/event-schema-iot-hub.md)」をご覧ください

## <a name="filter-events"></a>イベントのフィルター処理

IoT Hub イベント サブスクリプションは、イベントの種類とデバイス名に基づいてイベントをフィルター処理できます。 Event Grid のサブジェクト フィルターは**プレフィックス**と**サフィックス**の一致に基づいて動作します。 フィルターは `AND` 演算子を使用するため、プレフィックスとサフィックスの両方に一致するサブジェクトを持つイベントがサブスクライバーに配信されます。 

IoT イベントのサブジェクトには次の形式が使われます。

```json
devices/{deviceId}
```

### <a name="tips-for-consuming-events"></a>イベントの使用に関するヒント

IoT Hub イベントを処理するアプリケーションは、以下の推奨される手法に従う必要があります。

* 同じイベント ハンドラーにイベントをルーティングするように複数のサブスクリプションを構成できるので、イベントが特定のソースからであると想定しないことが重要です。 常にメッセージ トピックをチェックし、予期される IoT Hub からものであることを確認してください。 
* 受信するすべてのイベントが予期する種類であると想定してはいけません。 メッセージを処理する前に、常に eventType をチェックしてください。
* メッセージは、順不同で、または遅延の後に、到着する場合があります。 etag フィールドを使って、オブジェクトに関する情報が最新かどうか確認してください。



## <a name="next-steps"></a>次の手順

* [IoT Hub イベントのチュートリアルを試します](../event-grid/publish-iot-hub-events-to-logic-apps.md)
* [Event Grid の詳細を確認します][lnk-eg-overview]
* [IoT Hub のイベントとメッセージのルーティングの違いを比較します][lnk-eg-compare]

<!-- Links -->
[lnk-eg-overview]: ../event-grid/overview.md
[lnk-eg-compare]: iot-hub-event-grid-routing-comparison.md