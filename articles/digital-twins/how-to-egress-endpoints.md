---
title: Azure Digital Twins でのエグレスとエンドポイント | Microsoft Docs
description: Azure Digital Twins でエンドポイントを作成する方法のガイドラインです
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 10/08/2018
ms.author: alinast
ms.openlocfilehash: c917fab84448684cf29af162ec0781d764605f71
ms.sourcegitcommit: 74941e0d60dbfd5ab44395e1867b2171c4944dbe
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/15/2018
ms.locfileid: "49323971"
---
# <a name="egress-and-endpoints"></a>エグレスとエンドポイント

Azure Digital Twins でサポートされる "_エンドポイント_" の概念では、各エンドポイントがユーザーの Azure サブスクリプションでのメッセージ/イベント ブローカーを表します。 イベントとメッセージは、**Event Hub**、**Event Grid**、および **Service Bus トピック**に送信できます。

イベントは、定義済みのルーティング設定に従ってエンドポイントに送信されます。ユーザーは、イベント `TopologyOperation`、`UdfCustom`、`SensorChange`、`SpaceChange`、`DeviceMessage` を受け取る必要があるエンドポイントを指定できます。

イベントのルーティングとイベントの種類の基本については、「[ルーティング イベントおよびメッセージ](concepts-events-routing.md)」をご覧ください。

## <a name="event-types-description"></a>イベントの種類の説明

各イベントの種類のイベント形式を次に示します。

- `TopologyOperation`

  グラフの変更を適用します。 `subject` プロパティは、影響を受けるオブジェクトの種類を指定します。 このイベントをトリガーできるオブジェクトの種類は、`Device, DeviceBlobMetadata`、`DeviceExtendedProperty`、`ExtendedPropertyKey`、`ExtendedType`、`KeyStore`、`Report`、`RoleDefinition`、`Sensor`、`SensorBlobMetadata`、`SensorExtendedProperty`、`Space`、`SpaceBlobMetadata`、`SpaceExtendedProperty`、`SpaceResource`、`SpaceRoleAssignment`、`System`、`User`、`UserBlobMetadata`、`UserExtendedProperty` です。

  例:

  ```JSON
  {
    "id": "00000000-0000-0000-0000-000000000000",
    "subject": "ExtendedPropertyKey",
    "data": {
      "SpacesToNotify": [
        "3a16d146-ca39-49ee-b803-17a18a12ba36"
      ],
      "Id": "00000000-0000-0000-0000-000000000000",
      "Type": "ExtendedPropertyKey",
      "AccessType": "Create"
    },
    "eventType": "TopologyOperation",
    "eventTime": "2018-04-17T17:41:54.9400177Z",
    "dataVersion": "1",
    "metadataVersion": "1",
    "topic": "/subscriptions/yourTopicName"
  }
  ```

    | カスタム属性の名前 | 置換後の文字列 |
    | --- | --- |
    | `yourTopicName` | カスタマイズしたトピックの名前 |

- `UdfCustom`

  ユーザー定義関数 (UDF) によって送信されるイベント。 このイベントは、UDF 自体から明示的に送信する必要があることに注意してください。

  例:

  ```JSON
  {
    "id": "568fd394-380b-46fa-925a-ebb96f658cce",
    "subject": "UdfCustom",
    "data": {
      "TopologyObjectId": "7c799bfc-1bff-4b9e-b15a-669933969d20",
      "ResourceType": "Space",
      "Payload": "\"Room is not available or air quality is poor\"",
      "CorrelationId": "568fd394-380b-46fa-925a-ebb96f658cce"
    },
    "eventType": "UdfCustom",
    "eventTime": "2018-10-02T06:50:15.198Z",
    "dataVersion": "1.0",
    "metadataVersion": "1",
    "topic": "/subscriptions/yourTopicName"
  }
  ```

    | カスタム属性の名前 | 置換後の文字列 |
    | --- | --- |
    | `yourTopicName` | カスタマイズしたトピックの名前 |

- `SensorChange`

  テレメトリの変化に基づく、センサーの状態に対する更新。

  例:

  ```JSON
  {
    "id": "60bf5336-2929-45b4-bb4c-b45699dfe95f",
    "subject": "SensorChange",
    "data": {
      "Type": "Classic",
      "DataType": "Motion",
      "Id": "60bf5336-2929-45b4-bb4c-b45699dfe95f",
      "Value": "False",
      "PreviousValue": "True",
      "EventTimestamp": "2018-04-17T17:46:15.4964262Z",
      "MessageType": "sensor",
      "Properties": {
        "ms-client-request-id": "c9e576b7-5eea-4f61-8617-92a57add5179",
        "ms-activity-id": "ct22YwXEGJ5u.605.0"
      }
    },
    "eventType": "SensorChange",
    "eventTime": "2018-04-17T17:46:18.5452993Z",
    "dataVersion": "1",
    "metadataVersion": "1",
    "topic": "/subscriptions/yourTopicName"
  }
  ```

    | カスタム属性の名前 | 置換後の文字列 |
    | --- | --- |
    | `yourTopicName` | カスタマイズしたトピックの名前 |

- `SpaceChange`

  テレメトリの変化に基づく、スペースの状態に対する更新。

  例:

  ```JSON
  {
    "id": "42522e10-b1aa-42ff-a5e7-7181788ffc4b",
    "subject": "SpaceChange",
    "data": {
      "Type": null,
      "DataType": "AvailableAndFresh",
      "Id": "7c799bfc-1bff-4b9e-b15a-669933969d20",
      "Value": "Room is not available or air quality is poor",
      "PreviousValue": null,
      "RawData": null,
      "transactionId": null,
      "EventTimestamp": null,
      "MessageType": null,
      "Properties": null,
      "CorrelationId": "42522e10-b1aa-42ff-a5e7-7181788ffc4b"
    },
    "eventType": "SpaceChange",
    "eventTime": "2018-10-02T06:50:20.128Z",
    "dataVersion": "1.0",
    "metadataVersion": "1",
    "topic": "/subscriptions/yourTopicName"
  }
  ```

    | カスタム属性の名前 | 置換後の文字列 |
    | --- | --- |
    | `yourTopicName` | カスタマイズしたトピックの名前 |

- `DeviceMessage`

  Azure Digital Twins からも生テレメトリ イベントをルーティングできる `EventHub` 接続を指定できます。

> [!NOTE]
> - `DeviceMessage` は `EventHub` だけと結合できます。`DeviceMessage` を他のイベントの種類と結合することはできません。
> - 種類 `EventHub`/`DeviceMessage` の組み合わせのエンドポイントは、1 つだけ指定できます。

## <a name="configuring-endpoints"></a>エンドポイントの構成

エンドポイントの管理は、Endpoints API を使用して行います。 サポートされているさまざまなエンドポイントの構成方法の例をいくつか示します。 イベントの種類の配列は、エンドポイントのルーティングを定義しているので、特に注意してください。

```plaintext
POST https://endpoints-demo.azuresmartspaces.net/management/api/v1.0/endpoints
```

- **Service Bus** イベントの種類に対するルート: `SensorChange`、`SpaceChange`、`TopologyOperation`

  ```JSON
  {
    "type": "ServiceBus",
    "eventTypes": [
      "SensorChange",
      "SpaceChange",
      "TopologyOperation"
    ],
    "connectionString": "Endpoint=sb://yourNamespace.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=yourPrimaryKey",
    "secondaryConnectionString": "Endpoint=sb://yourNamespace.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=yourSecondaryKey",
    "path": "yourTopicName"
  }
  ```

    | カスタム属性の名前 | 置換後の文字列 |
    | --- | --- |
    | `yourNamespace` | エンドポイントの名前空間 |
    | `yourPrimaryKey` | 認証に使用されるプライマリ接続文字列 |
    | `yourSecondaryKey` | 認証に使用されるセカンダリ接続文字列 |
    | `yourTopicName` | カスタマイズしたトピックの名前 |

- **Event Grid** イベントの種類に対するルート: `SensorChange`、`SpaceChange`、`TopologyOperation`

  ```JSON
  {
    "type": "EventGrid",
    "eventTypes": [
      "SensorChange",
      "SpaceChange",
      "TopologyOperation"
    ],
    "connectionString": "yourPrimaryKey",
    "secondaryConnectionString": "yourSecondaryKey",
    "path": "yourTopicName.westus-1.eventgrid.azure.net"
  }
  ```

    | カスタム属性の名前 | 置換後の文字列 |
    | --- | --- |
    | `yourPrimaryKey` | 認証に使用されるプライマリ接続文字列|
    | `yourSecondaryKey` | 認証に使用されるセカンダリ接続文字列 |
    | `yourTopicName` | カスタマイズしたトピックの名前 |

- **Event Hub** イベントの種類に対するルート: `SensorChange`、`SpaceChange`、`TopologyOperation`

  ```JSON
  {
    "type": "EventHub",
    "eventTypes": [
      "SensorChange",
      "SpaceChange",
      "TopologyOperation"
    ],
    "connectionString": "Endpoint=sb://yourNamespace.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=yourPrimaryKey",
    "secondaryConnectionString": "Endpoint=sb://yourNamespace.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=yourSecondaryKey",
    "path": "yourEventHubName"
  }
  ```

    | カスタム属性の名前 | 置換後の文字列 |
    | --- | --- |
    | `yourNamespace` | エンドポイントの名前空間 |
    | `yourPrimaryKey` | 認証に使用されるプライマリ接続文字列 |
    | `yourSecondaryKey` | 認証に使用されるセカンダリ接続文字列 |
    | `yourEventHubName` | イベント ハブの名前 |

- **Event Hub** イベントの種類に対するルート: `DeviceMessage` `connectionString` に _EntityPath_ が含まれることに注意してください。これは必須です。

  ```JSON
  {
    "type": "EventHub",
    "eventTypes": [
      "DeviceMessage"
    ],
    "connectionString": "Endpoint=sb://yourNamespace.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=yourPrimaryKey;EntityPath=yourEventHubName",
    "secondaryConnectionString": "Endpoint=sb://yourNamespace.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=yourSecondaryKey;EntityPath=yourEventHubName",
    "path": "yourEventHubName"
  }
  ```

    | カスタム属性の名前 | 置換後の文字列 |
    | --- | --- |
    | `yourNamespace` | エンドポイントの名前空間 |
    | `yourPrimaryKey` | 認証に使用されるプライマリ接続文字列 |
    | `yourSecondaryKey` | 認証に使用されるセカンダリ接続文字列 |
    | `yourEventHubName` | イベント ハブの名前 |

> [!NOTE]
> 新しいエンドポイントを作成するとき、そのエンドポイントでイベントの受信が始まるまで最大 5 ～ 10 分かかる場合があります。

## <a name="primary-and-secondary-connection-keys"></a>プライマリ接続キーとセカンダリ接続キー

プライマリ接続キーが承認されなくなると、システムは自動的にセカンダリ接続キーを試みます。 これによってバックアップが提供され、Endpoints API を使用してプライマリ キーを正常に認証および更新できる可能性があります。

プライマリとセカンダリ両方の接続キーが承認されなくなると、システムは最大 30 分の指数バックオフ待機時間に入ります。 トリガーされた各バックオフ待機時間では、イベントは削除されます。

システムがバックオフ待機状態のときは常に、Endpoints API による接続キーの更新が有効になるまでに最大で 30 分かかる場合があります。

## <a name="unreachable-endpoints"></a>到達できないエンドポイント

エンドポイントが到達不能になると、システムは最大 30 分の指数バックオフ待機時間に入ります。 トリガーされた各バックオフ待機時間では、イベントは削除されます。

## <a name="next-steps"></a>次の手順

Azure Digital Twins Swagger の使用方法については、[Azure Digital Twins Swagger](how-to-use-swagger.md) に関するページをご覧ください。

Azure Digital Twins でのイベントとメッセージのルーティングについて詳しくは、「[ルーティング イベントおよびメッセージ](concepts-events-routing.md)」をご覧ください。
