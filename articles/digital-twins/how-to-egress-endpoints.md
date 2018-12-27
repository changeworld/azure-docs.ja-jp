---
title: Azure Digital Twins でのエグレスとエンドポイント | Microsoft Docs
description: Azure Digital Twins を使用してエンドポイントを作成する方法のガイドラインです
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 10/26/2018
ms.author: alinast
ms.openlocfilehash: c94d29f16c011a9ff9951d064d7496d3a87f70ef
ms.sourcegitcommit: 542964c196a08b83dd18efe2e0cbfb21a34558aa
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/14/2018
ms.locfileid: "51636307"
---
# <a name="egress-and-endpoints"></a>エグレスとエンドポイント

Azure Digital Twins では、**エンドポイント**の概念がサポートされます。 各エンドポイントは、ユーザーの Azure サブスクリプション内のメッセージまたはイベント ブローカーを表します。 イベントとメッセージは、Azure Event Hubs、Azure Event Grid、および Azure Service Bus のトピックに送信できます。

イベントは、ルーティングの定義済み優先設定に従ってエンドポイントに送信されます。 ユーザーは、次のいずれかのイベントを受信する必要があるエンドポイントを指定できます。 

- TopologyOperation
- UdfCustom
- SensorChange
- SpaceChange
- DeviceMessage

イベントのルーティングとイベントの種類の基本については、「[ルーティング イベントおよびメッセージ](concepts-events-routing.md)」をご覧ください。

## <a name="event-types-description"></a>イベントの種類の説明

各イベントの種類のイベント形式について、以下のセクションで説明します。

### <a name="topologyoperation"></a>TopologyOperation

**TopologyOperation** は、グラフの変更に適用されます。 **subject** プロパティは、影響を受けるオブジェクトの種類を指定します。 次のオブジェクトの種類によって、このイベントがトリガーされる可能性があります。 

- Device
- DeviceBlobMetadata
- DeviceExtendedProperty
- ExtendedPropertyKey
- ExtendedType
- KeyStore
- レポート
- RoleDefinition
- センサー
- SensorBlobMetadata
- SensorExtendedProperty
- スペース
- SpaceBlobMetadata
- SpaceExtendedProperty
- SpaceResource
- SpaceRoleAssignment
- システム
- User
- UserBlobMetadata
- UserExtendedProperty

#### <a name="example"></a>例

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
  "topic": "/subscriptions/YOUR_TOPIC_NAME"
}
```

| 値 | 置換後の文字列 |
| --- | --- |
| YOUR_TOPIC_NAME | カスタマイズしたトピックの名前 |

### <a name="udfcustom"></a>UdfCustom

**UdfCustom** は、ユーザー定義関数 (UDF) によって送信されるイベントです。 
  
> [!IMPORTANT]  
> このイベントは、UDF 自体から明示的に送信する必要があります。

#### <a name="example"></a>例

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
  "topic": "/subscriptions/YOUR_TOPIC_NAME"
}
```

| 値 | 置換後の文字列 |
| --- | --- |
| YOUR_TOPIC_NAME | カスタマイズしたトピックの名前 |

### <a name="sensorchange"></a>SensorChange

**SensorChange** は、テレメトリの変化に基づく、センサーの状態に対する更新です。

#### <a name="example"></a>例

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
  "topic": "/subscriptions/YOUR_TOPIC_NAME"
}
```

| 値 | 置換後の文字列 |
| --- | --- |
| YOUR_TOPIC_NAME | カスタマイズしたトピックの名前 |

### <a name="spacechange"></a>SpaceChange

**SpaceChange** は、テレメトリの変化に基づく、スペースの状態に対する更新です。

#### <a name="example"></a>例

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
  "topic": "/subscriptions/YOUR_TOPIC_NAME"
}
```

| 値 | 置換後の文字列 |
| --- | --- |
| YOUR_TOPIC_NAME | カスタマイズしたトピックの名前 |

### <a name="devicemessage"></a>DeviceMessage

**DeviceMessage** を使用すると、Azure Digital Twins からも生テレメトリ イベントをルーティングできる **EventHub** 接続を指定できます。

> [!NOTE]
> - **DeviceMessage** は、**EventHub** とのみ組み合わせることができます。 **DeviceMessage** はその他のいずれのイベントの種類とも組み合わせることができません。
> - 種類 **EventHub** または **DeviceMessage** の組み合わせのエンドポイントは、1 つだけ指定できます。

## <a name="configure-endpoints"></a>エンドポイントを構成する

エンドポイントの管理は、Endpoints API を使用して行います。 次の例は、サポートされているさまざまなエンドポイントの構成方法を示しています。 イベントの種類の配列は、エンドポイントのルーティングを定義しているので、特に注意してください。

```plaintext
POST https://endpoints-demo.azuresmartspaces.net/management/api/v1.0/endpoints
```

- Service Bus イベントの種類へのルート **SensorChange**、**SpaceChange**、**TopologyOperation**:

  ```JSON
  {
    "type": "ServiceBus",
    "eventTypes": [
      "SensorChange",
      "SpaceChange",
      "TopologyOperation"
    ],
    "connectionString": "Endpoint=sb://YOUR_NAMESPACE.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=YOUR_PRIMARY_KEY",
    "secondaryConnectionString": "Endpoint=sb://YOUR_NAMESPACE.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=YOUR_SECONDARY_KEY",
    "path": "YOUR_TOPIC_NAME"
  }
  ```

    | 値 | 置換後の文字列 |
    | --- | --- |
    | YOUR_NAMESPACE | エンドポイントの名前空間 |
    | YOUR_PRIMARY_KEY | 認証に使用されるプライマリ接続文字列 |
    | YOUR_SECONDARY_KEY | 認証に使用されるセカンダリ接続文字列 |
    | YOUR_TOPIC_NAME | カスタマイズしたトピックの名前 |

- Event Grid イベントの種類へのルート **SensorChange**、**SpaceChange**、**TopologyOperation**:

  ```JSON
  {
    "type": "EventGrid",
    "eventTypes": [
      "SensorChange",
      "SpaceChange",
      "TopologyOperation"
    ],
    "connectionString": "YOUR_PRIMARY_KEY",
    "secondaryConnectionString": "YOUR_SECONDARY_KEY",
    "path": "YOUR_TOPIC_NAME.westus-1.eventgrid.azure.net"
  }
  ```

    | 値 | 置換後の文字列 |
    | --- | --- |
    | YOUR_PRIMARY_KEY | 認証に使用されるプライマリ接続文字列|
    | YOUR_SECONDARY_KEY | 認証に使用されるセカンダリ接続文字列 |
    | YOUR_TOPIC_NAME | カスタマイズしたトピックの名前 |

- Event Hubs イベントの種類へのルート **SensorChange**、**SpaceChange**、**TopologyOperation**:

  ```JSON
  {
    "type": "EventHub",
    "eventTypes": [
      "SensorChange",
      "SpaceChange",
      "TopologyOperation"
    ],
    "connectionString": "Endpoint=sb://YOUR_NAMESPACE.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=YOUR_PRIMARY_KEY",
    "secondaryConnectionString": "Endpoint=sb://YOUR_NAMESPACE.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=YOUR_SECONDARY_KEY",
    "path": "YOUR_EVENT_HUB_NAME"
  }
  ```

    | 値 | 置換後の文字列 |
    | --- | --- |
    | YOUR_NAMESPACE | エンドポイントの名前空間 |
    | YOUR_PRIMARY_KEY | 認証に使用されるプライマリ接続文字列 |
    | YOUR_SECONDARY_KEY | 認証に使用されるセカンダリ接続文字列 |
    | YOUR_EVENT_HUB_NAME | イベント ハブの名前 |

- Event Hubs イベントの種類へのルート **DeviceMessage**。 **connectionString** に `EntityPath` を含めることが必須です。

  ```JSON
  {
    "type": "EventHub",
    "eventTypes": [
      "DeviceMessage"
    ],
    "connectionString": "Endpoint=sb://YOUR_NAMESPACE.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=YOUR_PRIMARY_KEY;EntityPath=YOUR_EVENT_HUB_NAME",
    "secondaryConnectionString": "Endpoint=sb://YOUR_NAMESPACE.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=YOUR_SECONDARY_KEY;EntityPath=YOUR_EVENT_HUB_NAME",
    "path": "YOUR_EVENT_HUB_NAME"
  }
  ```

    | 値 | 置換後の文字列 |
    | --- | --- |
    | YOUR_NAMESPACE | エンドポイントの名前空間 |
    | YOUR_PRIMARY_KEY | 認証に使用されるプライマリ接続文字列 |
    | YOUR_SECONDARY_KEY | 認証に使用されるセカンダリ接続文字列 |
    | YOUR_EVENT_HUB_NAME | イベント ハブの名前 |

> [!NOTE]  
> 新しいエンドポイントを作成するとき、そのエンドポイントでイベントの受信が始まるまで最大 5 ～ 10 分かかる場合があります。

## <a name="primary-and-secondary-connection-keys"></a>プライマリ接続キーとセカンダリ接続キー

プライマリ接続キーが承認されなくなると、システムは自動的にセカンダリ接続キーを試みます。 これによってバックアップが提供され、Endpoints API を使用してプライマリ キーを正常に認証および更新できる可能性があります。

プライマリとセカンダリ両方の接続キーが承認されなくなると、システムは最大 30 分の指数バックオフ待機時間に入ります。 トリガーされた各バックオフ待機時間では、イベントは削除されます。

システムがバックオフ待機状態のときは常に、Endpoints API による接続キーの更新が有効になるまでに最大で 30 分かかる場合があります。

## <a name="unreachable-endpoints"></a>到達できないエンドポイント

エンドポイントが到達不能になると、システムは最大 30 分の指数バックオフ待機時間に入ります。 トリガーされた各バックオフ待機時間では、イベントは削除されます。

## <a name="next-steps"></a>次の手順

- [Azure Digital Twins Swagger の使用方法](how-to-use-swagger.md)を確認します。

- Azure Digital Twins 内での[イベントとメッセージのルーティング](concepts-events-routing.md)の詳細を確認します。
