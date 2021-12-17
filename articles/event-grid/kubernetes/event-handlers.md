---
title: Kubernetes 上の Azure Event Grid - イベント ハンドラーと送信先
description: この記事では、Kubernetes 上の Event Grid によってサポートされるさまざまな種類のイベント ハンドラーと送信先について説明します
author: jfggdl
ms.author: jafernan
ms.subservice: kubernetes
ms.date: 05/25/2021
ms.topic: conceptual
ms.openlocfilehash: b1052b996fd9da8452d0f23d60fc7ea53676f713
ms.sourcegitcommit: 2da83b54b4adce2f9aeeed9f485bb3dbec6b8023
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/24/2021
ms.locfileid: "122769824"
---
# <a name="event-handlers-destinations-in-event-grid-on-kubernetes"></a>Kubernetes 上の Event Grid でのイベント ハンドラーと送信先
イベント ハンドラーは、エンドポイントを公開するすべてのシステムであり、Event Grid によって送信されるイベントの送信先です。 イベントを受信するイベント ハンドラーはそれに対して動作し、イベント ペイロードを使用して何らかのロジックを実行します。それにより、新しいイベントが発生することがあります。

送信先にイベントを送信するように Event Grid を構成するには、イベント サブスクリプションを作成します。 これは、[Azure CLI](/cli/azure/eventgrid/event-subscription#az_eventgrid_event_subscription_create)、[管理 SDK](../sdk-overview.md#management-sdks)、または [2020-10-15-preview API](/rest/api/eventgrid/version2021-06-01-preview/event-subscriptions/create-or-update) バージョンを使用した HTTP の直接呼び出しを使用して、行うことができます。

一般に、Kubernetes 上の Event Grid からは、**Webhook** を介して任意の送信先にイベントを送信できます。 Webhook は、サービスまたはワークロードによって公開される HTTP エンドポイントであり、Event Grid はそれに対するアクセス権を持っています。 Webhook となるワークロードは、同じクラスター内、同じネットワーク領域内、クラウド上、オンプレミス、または Event Grid が到達できる任意の場所の、どこでホストされていてもかまいません。 

[!INCLUDE [event-grid-preview-feature-note.md](../includes/event-grid-preview-feature-note.md)]

Event Grid により、Webhook を通して、**Kubernetes クラスターでホストされている** 次の送信先がサポートされます。

* Azure Arc を使用する Kubernetes 上の Azure App Service。 
* Azure Arc を使用する Kubernetes 上の Azure Functions。 
* Azure Arc を使用する Kubernetes 上の Azure Logic Apps。

Webhook に加えて、Kubernetes 上の Event Grid からは、**Azure でホストされている** 次の送信先にイベントを送信できます。

- Azure Event Grid (**Webhook を使用**)
- Azure Functions (**Webhook のみを使用**)
- Azure Event Hubs (その Azure Resource Manager リソース ID を使用)
- Azure Service Bus のトピックまたはキュー (その Azure Resource Manager リソース ID を使用)
- Azure Storage キュー (その Azure Resource Manager リソース ID を使用)



## <a name="feature-parity"></a>機能パリティ
Kubernetes 上の Event Grid からは、イベント サブスクリプションに対する Azure Event Grid のサポートに関して、高いレベルの機能パリティが提供されます。 次の一覧は、イベント サブスクリプション機能での主な相違点です。 これらの違いとは別に、Kubernetes 上の Event Grid でイベント サブスクリプションを管理するときは、Azure Event Grid の [REST API バージョン 2020-10-15-preview](/rest/api/eventgrid/version2021-06-01-preview/event-subscriptions) を参照として使用できます。

1. [REST API バージョン 2020-10-15-preview](/rest/api/eventgrid/version2021-06-01-preview/event-subscriptions) を使用します。
2. [Azure Functions 用の Azure Event Grid トリガー](../../azure-functions/functions-bindings-event-grid-trigger.md?tabs=csharp%2Cconsole)はサポートされていません。 Azure Functions にイベントを配信するには、Webhook 送信先の種類を使用できます。
3. [配信不能の場所](../manage-event-delivery.md#set-dead-letter-location)はサポートされていません。 つまり、イベント サブスクリプション ペイロードで ``properties.deadLetterDestination`` を使用することはできません。
4. 送信先としての Azure Relay のハイブリッド接続は、まだサポートされていません。
5. CloudEvents スキーマだけがサポートされています。 サポートされているスキーマの値は "[CloudEventSchemaV1_0](/rest/api/eventgrid/version2021-06-01-preview/event-subscriptions/create-or-update#eventdeliveryschema)" です。 クラウド イベント スキーマは拡張可能であり、オープン標準に基づいています。  
6. ラベル ([properties.labels](/rest/api/eventgrid/version2021-06-01-preview/event-subscriptions/create-or-update#request-body)) は、Kubernetes 上の Event Grid には適用できません。 そのため、それらは使用できません。
7. [リソース ID を使用した配信](/rest/api/eventgrid/version2021-06-01-preview/event-subscriptions/create-or-update#deliverywithresourceidentity)はサポートされていません。 そのため、[イベント サブスクリプション ID](/rest/api/eventgrid/version2021-06-01-preview/event-subscriptions/create-or-update#eventsubscriptionidentity) のすべてのプロパティはサポートされていません。
8. [送信先エンドポイントの検証](../webhook-event-delivery.md#endpoint-validation-with-event-grid-events)はまだサポートされていません。

## <a name="event-filtering-in-event-subscriptions"></a>イベント サブスクリプションでのイベントのフィルター処理
イベント サブスクリプションの構成でのもう 1 つの重要な側面は、送信先に配信されるイベントの選択です。 詳細については、[イベントのフィルター処理](filter-events.md)に関する記事を参照してください。

## <a name="sample-destination-configurations"></a>送信先の構成のサンプル

次に、意図される送信先に応じた基本的な構成のサンプルをいくつか示します。

## <a name="webhook"></a>WebHook
WebHook エンドポイントに発行するには、`endpointType` を `WebHook` に設定し、以下を指定します。

* **endpointUrl**: Webhook エンドポイント URL

    ```json
        {
          "properties": {
            "destination": {
              "endpointType": "WebHook",
              "properties": {
                "endpointUrl": "<your-webhook-endpoint>"
              }
            }
          }
        }
    ```

## <a name="azure-event-grid"></a>Azure Event Grid

Azure Event Grid クラウド エンドポイントに発行するには、`endpointType` を `WebHook` に設定し、以下を指定します。

* **endpointUrl**: クラウド内の Azure Event Grid トピック URL。API バージョン パラメーターは **2018-01-01** に設定され、`aeg-sas-key` は URL エンコードされた SAS キーに設定されています。 

   ```json
    {
        "properties": {
            "destination": {
                "endpointType": "WebHook",
                "properties": {
                    "endpointUrl": "<your-event-grid-cloud-topic-endpoint-url>?api-version=2018-01-01&aeg-sas-key=urlencoded(sas-key-value)"
                }
            }
        }
    }
   ```

## <a name="event-hubs"></a>Event Hubs

イベント ハブに発行するには、`endpointType` を `eventHub` に設定し、以下を指定します。

* **resourceId**: 特定のイベント ハブのリソース ID。

    ```json
        {
          "properties": {
            "destination": {
              "endpointType": "eventHub",
              "properties": {
                "resourceId": "<Azure Resource ID of your event hub>"
              }
            }
          }
        }
    ```

## <a name="service-bus-queues"></a>Service Bus キュー

Service Bus キューに発行するには、`endpointType` を `serviceBusQueue` に設定し、以下を指定します。

* **resourceId**: 特定の Service Bus キューのリソース ID。

    ```json
        {
          "properties": {
            "destination": {
              "endpointType": "serviceBusQueue",
              "properties": {
                "resourceId": "<Azure Resource ID of your Service Bus queue>"
              }
            }
          }
        }
    ```

## <a name="service-bus-topics"></a>Service Bus トピック

Service Bus トピックに発行するには、`endpointType` を `serviceBusTopic` に設定し、以下を指定します。

* **resourceId**: 特定の Service Bus トピックのリソース ID。

    ```json
        {
          "properties": {
            "destination": {
              "endpointType": "serviceBusTopic",
              "properties": {
                "resourceId": "<Azure Resource ID of your Service Bus topic>"
              }
            }
          }
        }
    ```

## <a name="storage-queues"></a>ストレージ キュー

ストレージ キューに発行するには、`endpointType` を `storageQueue` に設定し、以下を指定します。

* **queueName**: 発行先の Azure ストレージ キューの名前。
* **resourceID**: キューが含まれるストレージ アカウントの Azure リソース ID。

    ```json
        {
          "properties": {
            "destination": {
              "endpointType": "storageQueue",
              "properties": {
                "queueName": "<your-storage-queue-name>",
                "resourceId": "<Azure Resource ID of your Storage account>"
              }
            }
          }
        }
    ```

## <a name="next-steps"></a>次の手順
* イベント サブスクリプションに[フィルター構成](filter-events.md)を追加して、配信されるイベントを選択します。 
* Azure Arc for Kubernetes 上の Event Grid によってサポートされるスキーマの詳細については、[Kubernetes 上の Event Grid - イベント スキーマ](event-schemas.md)に関する記事を参照してください。
