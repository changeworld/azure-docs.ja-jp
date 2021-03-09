---
title: インクルード ファイル
description: インクルード ファイル
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: include
ms.date: 02/04/2021
ms.author: spelluru
ms.custom: include file
ms.openlocfilehash: 49e35687820679a1c06cf19e7a26b3b04a1e1318
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/14/2021
ms.locfileid: "100363248"
---
## <a name="available-event-types"></a>使用可能なイベントの種類

Service Bus から出力されるイベントの種類は次のとおりです。

| イベントの種類 | 説明 |
| ---------- | ----------- |
| Microsoft.ServiceBus.ActiveMessagesAvailableWithNoListeners | キューまたはサブスクリプションにアクティブなメッセージがあり、リッスンしているレシーバーがない場合に生成されます。 |
| Microsoft.ServiceBus.DeadletterMessagesAvailableWithNoListener | 配信不能キューにアクティブなメッセージがあり、アクティブなレシーバーがない場合に生成されます。 |
| Microsoft.ServiceBus.ActiveMessagesAvailablePeriodicNotifications | キューまたはサブスクリプションにアクティブなメッセージがある場合に、その特定のキューまたはサブスクリプションにアクティブなリスナーが存在する場合でも、定期的に生成されます。 |
| Microsoft.ServiceBus.DeadletterMessagesAvailablePeriodicNotifications | キューまたはサブスクリプションの配信不能エンティティにメッセージがある場合に、その特定のキューまたはサブスクリプションの配信不能エンティティにアクティブなリスナーが存在する場合でも、定期的に生成されます。 | 

## <a name="example-event"></a>イベントの例

# <a name="event-grid-event-schema"></a>[Event Grid イベント スキーマ](#tab/event-grid-event-schema)

### <a name="active-messages-available-with-no-listeners"></a>アクティブなメッセージが利用可能 (リスナーなしの場合)
このイベントは、キューまたはサブスクリプションにアクティブなメッセージがあり、リッスンしているレシーバーがない場合に生成されます。

```json
[{
  "topic": "/subscriptions/{subscription-id}/resourcegroups/{your-rg}/providers/Microsoft.ServiceBus/namespaces/{your-service-bus-namespace}",
  "subject": "topics/{your-service-bus-topic}/subscriptions/{your-service-bus-subscription}",
  "eventType": "Microsoft.ServiceBus.ActiveMessagesAvailableWithNoListeners",
  "eventTime": "2018-02-14T05:12:53.4133526Z",
  "id": "dede87b0-3656-419c-acaf-70c95ddc60f5",
  "data": {
    "namespaceName": "YOUR SERVICE BUS NAMESPACE WILL SHOW HERE",
    "requestUri": "https://{your-service-bus-namespace}.servicebus.windows.net/{your-topic}/subscriptions/{your-service-bus-subscription}/messages/head",
    "entityType": "subscriber",
    "queueName": "QUEUE NAME IF QUEUE",
    "topicName": "TOPIC NAME IF TOPIC",
    "subscriptionName": "SUBSCRIPTION NAME"
  },
  "dataVersion": "1",
  "metadataVersion": "1"
}]
```

#### <a name="deadletter-messages-available-with-no-listener"></a>配信不能なメッセージが利用可能 (リスナーなしの場合)

配信不能キュー イベントのスキーマも同様です。 メッセージがあり、アクティブなレシーバーがいない配信不能キューごとに少なくとも 1 つのイベントを受信します。

```json
[{
  "topic": "/subscriptions/{subscription-id}/resourcegroups/{your-rg}/providers/Microsoft.ServiceBus/namespaces/{your-service-bus-namespace}",
  "subject": "topics/{your-service-bus-topic}/subscriptions/{your-service-bus-subscription}",
  "eventType": "Microsoft.ServiceBus.DeadletterMessagesAvailableWithNoListener",
  "eventTime": "2018-02-14T05:12:53.4133526Z",
  "id": "dede87b0-3656-419c-acaf-70c95ddc60f5",
  "data": {
    "namespaceName": "YOUR SERVICE BUS NAMESPACE WILL SHOW HERE",
    "requestUri": "https://{your-service-bus-namespace}.servicebus.windows.net/{your-topic}/subscriptions/{your-service-bus-subscription}/$deadletterqueue/messages/head",
    "entityType": "subscriber",
    "queueName": "QUEUE NAME IF QUEUE",
    "topicName": "TOPIC NAME IF TOPIC",
    "subscriptionName": "SUBSCRIPTION NAME"
  },
  "dataVersion": "1",
  "metadataVersion": "1"
}]
```

#### <a name="active-messages-available-periodic-notifications"></a>アクティブなメッセージが利用可能な定期的な通知
このイベントは、特定のキューまたはサブスクリプションにアクティブなメッセージがある場合に、その特定のキューまたはサブスクリプションにアクティブなリスナーが存在する場合でも、定期的に生成されます。

```json
[{
  "topic": "/subscriptions/<subscription id>/resourcegroups/DemoGroup/providers/Microsoft.ServiceBus/namespaces/<YOUR SERVICE BUS NAMESPACE WILL SHOW HERE>",
  "subject": "topics/<service bus topic>/subscriptions/<service bus subscription>",
  "eventType": "Microsoft.ServiceBus.ActiveMessagesAvailablePeriodicNotifications",
  "eventTime": "2018-02-14T05:12:53.4133526Z",
  "id": "dede87b0-3656-419c-acaf-70c95ddc60f5",
  "data": {
    "namespaceName": "YOUR SERVICE BUS NAMESPACE WILL SHOW HERE",
    "requestUri": "https://YOUR-SERVICE-BUS-NAMESPACE-WILL-SHOW-HERE.servicebus.windows.net/TOPIC-NAME/subscriptions/SUBSCRIPTIONNAME/$deadletterqueue/messages/head",
    "entityType": "subscriber",
    "queueName": "QUEUE NAME IF QUEUE",
    "topicName": "TOPIC NAME IF TOPIC",
    "subscriptionName": "SUBSCRIPTION NAME"
  },
  "dataVersion": "1",
  "metadataVersion": "1"
}]
```

#### <a name="deadletter-messages-available-periodic-notifications"></a>配信不能なメッセージが利用可能な定期的な通知
このイベントは、特定のキューまたはサブスクリプションに配信不能なメッセージがある場合に、その特定のキューまたはサブスクリプションの配信不能なエンティティにアクティブなリスナーが存在する場合でも、定期的に生成されます。

```json
[{
  "topic": "/subscriptions/<subscription id>/resourcegroups/DemoGroup/providers/Microsoft.ServiceBus/namespaces/<YOUR SERVICE BUS NAMESPACE WILL SHOW HERE>",
  "subject": "topics/<service bus topic>/subscriptions/<service bus subscription>",
  "eventType": "Microsoft.ServiceBus.DeadletterMessagesAvailablePeriodicNotifications",
  "eventTime": "2018-02-14T05:12:53.4133526Z",
  "id": "dede87b0-3656-419c-acaf-70c95ddc60f5",
  "data": {
    "namespaceName": "YOUR SERVICE BUS NAMESPACE WILL SHOW HERE",
    "requestUri": "https://YOUR-SERVICE-BUS-NAMESPACE-WILL-SHOW-HERE.servicebus.windows.net/TOPIC-NAME/subscriptions/SUBSCRIPTIONNAME/$deadletterqueue/messages/head",
    "entityType": "subscriber",
    "queueName": "QUEUE NAME IF QUEUE",
    "topicName": "TOPIC NAME IF TOPIC",
    "subscriptionName": "SUBSCRIPTION NAME"
  },
  "dataVersion": "1",
  "metadataVersion": "1"
}]
```

# <a name="cloud-event-schema"></a>[クラウド イベント スキーマ](#tab/cloud-event-schema)

### <a name="active-messages-available-with-no-listeners"></a>アクティブなメッセージが利用可能 (リスナーなしの場合)
このイベントは、キューまたはサブスクリプションにアクティブなメッセージがあり、リッスンしているレシーバーがない場合に生成されます。

```json
[{
  "source": "/subscriptions/{subscription-id}/resourcegroups/{your-rg}/providers/Microsoft.ServiceBus/namespaces/{your-service-bus-namespace}",
  "subject": "topics/{your-service-bus-topic}/subscriptions/{your-service-bus-subscription}",
  "type": "Microsoft.ServiceBus.ActiveMessagesAvailableWithNoListeners",
  "time": "2018-02-14T05:12:53.4133526Z",
  "id": "dede87b0-3656-419c-acaf-70c95ddc60f5",
  "data": {
    "namespaceName": "YOUR SERVICE BUS NAMESPACE WILL SHOW HERE",
    "requestUri": "https://{your-service-bus-namespace}.servicebus.windows.net/{your-topic}/subscriptions/{your-service-bus-subscription}/messages/head",
    "entityType": "subscriber",
    "queueName": "QUEUE NAME IF QUEUE",
    "topicName": "TOPIC NAME IF TOPIC",
    "subscriptionName": "SUBSCRIPTION NAME"
  },
  "specversion": "1.0"
}]
```

#### <a name="deadletter-messages-available-with-no-listener"></a>配信不能なメッセージが利用可能 (リスナーなしの場合)

配信不能キュー イベントのスキーマも同様です。 メッセージがあり、アクティブなレシーバーがいない配信不能キューごとに少なくとも 1 つのイベントを受信します。

```json
[{
  "source": "/subscriptions/{subscription-id}/resourcegroups/{your-rg}/providers/Microsoft.ServiceBus/namespaces/{your-service-bus-namespace}",
  "subject": "topics/{your-service-bus-topic}/subscriptions/{your-service-bus-subscription}",
  "type": "Microsoft.ServiceBus.DeadletterMessagesAvailableWithNoListener",
  "time": "2018-02-14T05:12:53.4133526Z",
  "id": "dede87b0-3656-419c-acaf-70c95ddc60f5",
  "data": {
    "namespaceName": "YOUR SERVICE BUS NAMESPACE WILL SHOW HERE",
    "requestUri": "https://{your-service-bus-namespace}.servicebus.windows.net/{your-topic}/subscriptions/{your-service-bus-subscription}/$deadletterqueue/messages/head",
    "entityType": "subscriber",
    "queueName": "QUEUE NAME IF QUEUE",
    "topicName": "TOPIC NAME IF TOPIC",
    "subscriptionName": "SUBSCRIPTION NAME"
  },
  "specversion": "1.0"
}]
```

#### <a name="active-messages-available-periodic-notifications"></a>アクティブなメッセージが利用可能な定期的な通知
このイベントは、特定のキューまたはサブスクリプションにアクティブなメッセージがある場合に、その特定のキューまたはサブスクリプションにアクティブなリスナーが存在する場合でも、定期的に生成されます。

```json
[{
  "source": "/subscriptions/<subscription id>/resourcegroups/DemoGroup/providers/Microsoft.ServiceBus/namespaces/<YOUR SERVICE BUS NAMESPACE WILL SHOW HERE>",
  "subject": "topics/<service bus topic>/subscriptions/<service bus subscription>",
  "type": "Microsoft.ServiceBus.ActiveMessagesAvailablePeriodicNotifications",
  "time": "2018-02-14T05:12:53.4133526Z",
  "id": "dede87b0-3656-419c-acaf-70c95ddc60f5",
  "data": {
    "namespaceName": "YOUR SERVICE BUS NAMESPACE WILL SHOW HERE",
    "requestUri": "https://YOUR-SERVICE-BUS-NAMESPACE-WILL-SHOW-HERE.servicebus.windows.net/TOPIC-NAME/subscriptions/SUBSCRIPTIONNAME/$deadletterqueue/messages/head",
    "entityType": "subscriber",
    "queueName": "QUEUE NAME IF QUEUE",
    "topicName": "TOPIC NAME IF TOPIC",
    "subscriptionName": "SUBSCRIPTION NAME"
  },
  "specversion": "1.0"
}]
```

#### <a name="deadletter-messages-available-periodic-notifications"></a>配信不能なメッセージが利用可能な定期的な通知
このイベントは、特定のキューまたはサブスクリプションに配信不能なメッセージがある場合に、その特定のキューまたはサブスクリプションの配信不能なエンティティにアクティブなリスナーが存在する場合でも、定期的に生成されます。

```json
[{
  "source": "/subscriptions/<subscription id>/resourcegroups/DemoGroup/providers/Microsoft.ServiceBus/namespaces/<YOUR SERVICE BUS NAMESPACE WILL SHOW HERE>",
  "subject": "topics/<service bus topic>/subscriptions/<service bus subscription>",
  "type": "Microsoft.ServiceBus.DeadletterMessagesAvailablePeriodicNotifications",
  "time": "2018-02-14T05:12:53.4133526Z",
  "id": "dede87b0-3656-419c-acaf-70c95ddc60f5",
  "data": {
    "namespaceName": "YOUR SERVICE BUS NAMESPACE WILL SHOW HERE",
    "requestUri": "https://YOUR-SERVICE-BUS-NAMESPACE-WILL-SHOW-HERE.servicebus.windows.net/TOPIC-NAME/subscriptions/SUBSCRIPTIONNAME/$deadletterqueue/messages/head",
    "entityType": "subscriber",
    "queueName": "QUEUE NAME IF QUEUE",
    "topicName": "TOPIC NAME IF TOPIC",
    "subscriptionName": "SUBSCRIPTION NAME"
  },
  "specversion": "1.0"
}]
```

---



### <a name="event-properties"></a>イベントのプロパティ

# <a name="event-grid-event-schema"></a>[Event Grid イベント スキーマ](#tab/event-grid-event-schema)
イベントのトップレベルのデータを次に示します。

| プロパティ | 種類 | 説明 |
| -------- | ---- | ----------- |
| `topic` | string | イベント ソースの完全なリソース パス。 このフィールドは書き込み可能ではありません。 この値は Event Grid によって指定されます。 |
| `subject` | string | 発行元が定義したイベントの対象のパス。 |
| `eventType` | string | このイベント ソース用に登録されたイベントの種類のいずれか。 |
| `eventTime` | string | プロバイダーの UTC 時刻に基づくイベントの生成時刻。 |
| `id` | string | イベントの一意識別子。 |
| `data` | object | Blob Storage イベントのデータ。 |
| `dataVersion` | string | データ オブジェクトのスキーマ バージョン。 スキーマ バージョンは発行元によって定義されます。 |
| `metadataVersion` | string | イベント メタデータのスキーマ バージョン。 最上位プロパティのスキーマは Event Grid によって定義されます。 この値は Event Grid によって指定されます。 |

# <a name="cloud-event-schema"></a>[クラウド イベント スキーマ](#tab/cloud-event-schema)

イベントのトップレベルのデータを次に示します。

| プロパティ | 種類 | 説明 |
| -------- | ---- | ----------- |
| `source` | string | イベント ソースの完全なリソース パス。 このフィールドは書き込み可能ではありません。 この値は Event Grid によって指定されます。 |
| `subject` | string | 発行元が定義したイベントの対象のパス。 |
| `type` | string | このイベント ソース用に登録されたイベントの種類のいずれか。 |
| `time` | string | プロバイダーの UTC 時刻に基づくイベントの生成時刻。 |
| `id` | string | イベントの一意識別子。 |
| `data` | object | Blob Storage イベントのデータ。 |
| `specversion` | string | CloudEvents スキーマ仕様バージョン。 |

---

データ オブジェクトには、次のプロパティがあります。

| プロパティ | 種類 | 説明 |
| -------- | ---- | ----------- |
| `namespaceName` | string | リソースが存在する Service Bus 名前空間。 |
| `requestUri` | string | イベントを生成している特定のキューまたはサブスクリプションの URI。 |
| `entityType` | string | イベントを生成している Service Bus エンティティの種類 (キューまたはサブスクリプション)。 |
| `queueName` | string | キューをサブスクライブしている場合、アクティブなメッセージのあるキュー。 トピック/サブスクリプションを使っている場合は値 null。 |
| `topicName` | string | アクティブなメッセージのある Service Bus サブスクリプションが属しているトピック。 キューを使っている場合は値 null。 |
| `subscriptionName` | string | アクティブなメッセージのある Service Bus サブスクリプション。 キューを使っている場合は値 null。 |