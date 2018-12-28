---
title: Azure Event Hubs および Azure Service Bus にデータをエクスポートする | Microsoft Docs
description: Azure IoT Central アプリケーションから Azure Event Hubs および Azure Service Bus にデータをエクスポートする方法
services: iot-central
author: viv-liu
ms.author: viviali
ms.date: 12/07/2018
ms.topic: conceptual
ms.service: iot-central
manager: peterpr
ms.openlocfilehash: 14b51f109ca76661ac10c99d42002dda45bc0500
ms.sourcegitcommit: eb9dd01614b8e95ebc06139c72fa563b25dc6d13
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/12/2018
ms.locfileid: "53318400"
---
# <a name="export-your-data-in-azure-iot-central"></a>Azure IoT Central でデータをエクスポートする

*このトピックでは、管理者に適用されます。*

この記事では、Azure IoT Central の連続データ エクスポート機能を使用して、自身の **Azure Event Hubs** および **Azure Service Bus** のインスタンスにデータをエクスポートする方法を詳しく説明します。 ウォーム パスの分析情報と分析のため、**測定**、**デバイス**、および**デバイス テンプレート**を自身が指定した場所にエクスポートできます。 これには、Azure Stream Analytics でカスタム ルールをトリガーすること、Azure Logic Apps でカスタム ワークフローをトリガーすること、データを変換し、Azure Functions を介してそれを渡すことが含まれます。 

> [!Note]
> この場合も、連続データ エクスポートを有効にすると、その時点以降のデータのみが取得されます。 現在は、連続データ エクスポートがオフになっていたときのデータを取得することはできません。 より多くの履歴データを保持するには、連続データ エクスポートを早い段階で有効にしてください。


## <a name="prerequisites"></a>前提条件

- ご利用の IoT Central アプリケーションの管理者であること

## <a name="export-to-azure-event-hubs-and-azure-service-bus"></a>Azure Event Hubs および Azure Service Bus にエクスポートする

測定、デバイス、およびデバイス テンプレートのデータは、ほぼリアルタイムに、ご利用のイベント ハブや Service Bus のキュー、またはトピックにエクスポートされます。 エクスポートされた測定データには、測定自体の値だけでなく、デバイスが IoT Central に送信したメッセージ全体が含まれています。 エクスポートされたデバイス データには、すべてのデバイスのプロパティと設定に対する変更が含まれていて、エクスポートされたデバイス テンプレートには、すべてのデバイス テンプレートに対する変更が含まれています。 エクスポートされたデータは "body" プロパティ内にあり、JSON 形式になっています。

> [!NOTE]
> エクスポート先として Service Bus を選択する場合、キューおよびトピックで、**セッションまたは重複データ検出が有効になっていてはいけません**。 これらのオプションのいずれかが有効になっていると、一部のメッセージがキューやトピックに到着しません。

### <a name="measurements"></a>測定

新しいメッセージは、IoT Central がデバイスからメッセージを受信した後、迅速にエクスポートされます。 Event Hubs と Service Bus でエクスポートされた各メッセージには、デバイスが "body" プロパティで送信した完全なメッセージが JSON 形式で含まれています。 

> [!NOTE]
> 測定を送信するデバイスは、デバイス ID で表されます (以下のセクションを参照)。 デバイスの名前を取得するには、デバイスのデータをエクスポートし、デバイス メッセージの **deviceId** と一致する **connectionDeviceId** を使用して各メッセージを関連付けます。

次の例は、イベント ハブまたは Service Bus のキューまたはトピックで受信した測定データに関するメッセージを示しています。

```json
{
  "body": {
    "humidity": 29.06963648666288,
    "temp": 8.4503795661685,
    "pressure": 1075.8334910110093,
    "magnetometerX": 408.6966458887116,
    "magnetometerY": -532.8809796603962,
    "magnetometerZ": 174.70653875528205,
    "accelerometerX": 1481.546749013788,
    "accelerometerY": -1139.4316656437406,
    "accelerometerZ": 811.6928695575307,
    "gyroscopeX": 442.19879163299856,
    "gyroscopeY": 123.23710975717177,
    "gyroscopeZ": 708.5397575786151,
    "deviceState": "DANGER"
  },
  "annotations": {
    "iothub-connection-device-id": "<connectionDeviceId>",
    "iothub-connection-auth-method": "{\"scope\":\"hub\",\"type\":\"sas\",\"issuer\":\"iothub\",\"acceptingIpFilterRule\":null}",
    "iothub-connection-auth-generation-id": "<generationId>",
    "iothub-enqueuedtime": 1539381029965,
    "iothub-message-source": "Telemetry",
    "x-opt-sequence-number": 25325,
    "x-opt-offset": "<offset>",
    "x-opt-enqueued-time": 1539381030200
  },
  "sequenceNumber": 25325,
  "enqueuedTimeUtc": "2018-10-12T21:50:30.200Z",
  "offset": "<offset>",
  "properties": {
    "content_type": "application/json",
    "content_encoding": "utf-8"
  }
}
```

### <a name="devices"></a>デバイス

デバイス データを含むメッセージは、数分に 1 回、イベント ハブまたは Service Bus のキューまたはトピックに送信されます。 つまり数分ごとに、以下に関するデータを含むメッセージのバッチが到達します
- 追加された新しいデバイス
- プロパティや設定の値が変更されたデバイス

各メッセージは、最後にエクスポートされたメッセージ以降、デバイスに加えられた 1 つ以上の変更を表しています。 各メッセージで送信される情報には、以下が含まれます。
- `id` IoT Central でデバイスの
- デバイスの `name`
- `deviceId`Device Provisioning Service[ の](https://aka.ms/iotcentraldocsdps)
- デバイステンプレート情報
- プロパティ値
- 設定値。

> [!NOTE]
> 最後のバッチ以降に削除されたデバイスはエクスポートされません。 現在のところ、エクスポートされたメッセージ内に、削除されたデバイスを示すものはありません。
>
> 各デバイスが属するデバイス テンプレートは、デバイス テンプレート ID によって表されます。 デバイス テンプレートの名前を取得するには、デバイス テンプレートのデータも必ずエクスポートしてください。

次の例は、イベント ハブまたは Service Bus のキューまたはトピック内にあるデバイス データに関するメッセージを示しています。


```json
{
  "body": {
    "id": "<id>",
    "name": "<deviceName>",
    "simulated": true,
    "deviceId": "<deviceId>",
    "deviceTemplate": {
      "id": "<templateId>",
      "version": "1.0.0"
    },
    "properties": {
      "cloud": {
        "location": "Seattle"
      },
      "device": {
        "dieNumber": 5445.5862873026645
      }
    },
    "settings": {
      "device": {
        "fanSpeed": 0
      }
    }
  },
  "annotations": {
    "iotcentral-message-source": "devices",
    "x-opt-partition-key": "<partitionKey>",
    "x-opt-sequence-number": 39740,
    "x-opt-offset": "<offset>",
    "x-opt-enqueued-time": 1539274959654
  },
  "partitionKey": "<partitionKey>",
  "sequenceNumber": 39740,
  "enqueuedTimeUtc": "2018-10-11T16:22:39.654Z",
  "offset": "<offset>",
}
```

### <a name="device-templates"></a>デバイス テンプレート

デバイス テンプレート データを含むメッセージは、数分に 1 回、イベント ハブまたは Service Bus のキューまたはトピックに送信されます。 つまり数分ごとに、以下に関するデータを含むメッセージのバッチが到達します
- 追加された新しいデバイス テンプレート
- 測定、プロパティ、および設定の定義が変更されたデバイス テンプレート

各メッセージは、最後にエクスポートされたメッセージ以降、デバイス テンプレートに加えられた 1 つ以上の変更を表しています。 各メッセージで送信される情報には、以下が含まれます。
- デバイス テンプレートの`id`
- デバイス テンプレートの`name`
- デバイス テンプレートの`version`
- 測定のデータ型および最小/最大値
- プロパティのデータ型と既定値
- 設定のデータ型と既定値

> [!NOTE]
> 最後のバッチ以降に削除されたデバイス テンプレートはエクスポートされません。 現在のところ、エクスポートされたメッセージ内に、削除されたデバイス テンプレートを示すものはありません。

次の例は、イベント ハブまたは Service Bus のキューまたはトピック内にあるデバイス テンプレート データに関するメッセージを示しています。

```json
{
  "body": {
    "id": "<id>",
    "version": "1.0.0",
    "name": "<templateName>",
    "measurements": {
      "telemetry": {
        "humidity": {
          "dataType": "double",
          "name": "humidity"
        },
        "pressure": {
          "dataType": "double",
          "name": "pressure"
        },
        "temp": {
          "dataType": "double",
          "name": "temperature"
        }
      }
    },
    "properties": {
      "cloud": {
        "location": {
          "dataType": "string",
          "name": "Location"
        }
      },
      "device": {
        "dieNumber": {
          "dataType": "double",
          "name": "Die Number"
        }
      }
    },
    "settings": {
      "device": {
        "fanSpeed": {
          "dataType": "double",
          "name": "Fan Speed",
          "initialValue": 0
        }
      }
    }
  },
  "annotations": {
    "iotcentral-message-source": "deviceTemplates",
    "x-opt-partition-key": "<partitionKey>",
    "x-opt-sequence-number": 25315,
    "x-opt-offset": "<offset>",
    "x-opt-enqueued-time": 1539274985085
  },
  "partitionKey": "<partitionKey>",
  "sequenceNumber": 25315,
  "enqueuedTimeUtc": "2018-10-11T16:23:05.085Z",
  "offset": "<offset>",
}
```

## <a name="next-steps"></a>次の手順

これで、Azure Event Hubs と Azure Service Bus にデータをエクスポートする方法を理解できたので、次の手順に進みます。

> [!div class="nextstepaction"]
> [Azure Functions をトリガーする方法](howto-trigger-azure-functions.md)
