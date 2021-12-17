---
title: Azure Event Grid から HTTP エンドポイントへのイベントの受信
description: HTTP エンドポイントを検証した後、Azure Event Grid からのイベントを受信して逆シリアル化する方法について説明します
ms.topic: conceptual
ms.date: 07/16/2021
ms.custom: devx-track-js, devx-track-csharp
ms.openlocfilehash: f655982203c81ef6ff32a01566e37c346cf78138
ms.sourcegitcommit: f2eb1bc583962ea0b616577f47b325d548fd0efa
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/28/2021
ms.locfileid: "114730281"
---
# <a name="receive-events-to-an-http-endpoint"></a>HTTP エンドポイントへのイベントの受信

この記事では、イベント サブスクリプションからイベントを受信する [HTTP エンドポイントを検証](webhook-event-delivery.md)した後、イベントを逆シリアル化する方法を説明します。 この記事では、デモンストレーション用に Azure 関数を使用しますが、アプリケーションがどこでホストされている場合でも、同じ概念を適用できます。

> [!NOTE]
> Event Grid で Azure 関数をトリガーするときは、[Event Grid トリガー](../azure-functions/functions-bindings-event-grid.md)を使用することをお勧めします。 それにより、Event Grid と Azure Functions をより簡単で迅速に統合できます。 ただし、Azure Functions の Event Grid トリガーでは、ホストされているコードが Event Grid に返される HTTP 状態コードを制御する必要があるシナリオはサポートされていないことに注意してください。 この制限があるため、Azure 関数で実行されているコードは、たとえば、Event Grid によるイベント配信の再試行を開始するために 5XX エラーを返すことができません。

## <a name="prerequisites"></a>前提条件

HTTP によってトリガーされる関数を含む関数アプリが必要です。

## <a name="add-dependencies"></a>依存関係を追加する

.NET で開発する場合は、`Azure.Messaging.EventGrid` [NuGet パッケージ](https://www.nuget.org/packages/Azure.Messaging.EventGrid)の関数に[依存関係を追加します](../azure-functions/functions-reference-csharp.md#referencing-custom-assemblies)。

他の言語用の SDK は、[発行 SDK](./sdk-overview.md#data-plane-sdks) リファレンスを介して利用できます。 これらのパッケージには、`EventGridEvent``StorageBlobCreatedEventData`、`EventHubCaptureFileCreatedEventData` などのネイティブなイベントの種類用のモデルが含まれています。

## <a name="endpoint-validation"></a>エンドポイントの検証

最初に実行することは、`Microsoft.EventGrid.SubscriptionValidationEvent` イベントの処理です。 だれかがイベントにサブスクライブするたびに、Event Grid は、データ ペイロード内に `validationCode` を含む検証イベントをエンドポイントに送信します。 エンドポイントは、これを応答本文にエコー バックして、[エンドポイントが有効であり、ユーザーによって所有されていることを証明する](webhook-event-delivery.md)必要があります。 WebHook によってトリガーされる関数ではなく [Event Grid トリガー](../azure-functions/functions-bindings-event-grid.md)を使用している場合、エンドポイントの検証は自動的に処理されます。 サード パーティ製 API サービス ([Zapier](https://zapier.com/home) や [IFTTT](https://ifttt.com/) など) を使用する場合は、検証コードをプログラムでエコーできないことがあります。 このようなサービスの場合は、サブスクリプション検証イベントで送信される検証 URL を使用すると、サブスクリプションを手動で検証できます。 その URL を `validationUrl` プロパティにコピーし、REST クライアントまたは Web ブラウザーのいずれかを使って GET 要求を送信します。

C# では、`ParseMany()` メソッドを使用して、1 つ以上のイベントを含む `BinaryData` インスタンスを `EventGridEvent` の配列に逆シリアル化します。 1 つのイベントのみを逆シリアル化することが事前にわかっている場合は、代わりに `Parse` メソッドを使用できます。

検証コードをプログラムでエコーするには、次のコードを使用します。

```c#
using System.Threading.Tasks;
using Microsoft.AspNetCore.Mvc;
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Extensions.Http;
using Microsoft.AspNetCore.Http;
using Microsoft.Extensions.Logging;
using System;
using Azure.Messaging.EventGrid;
using Azure.Messaging.EventGrid.SystemEvents;

namespace Function1
{
    public static class Function1
    {
        [FunctionName("Function1")]
        public static async Task<IActionResult> Run(
            [HttpTrigger(AuthorizationLevel.Anonymous, "get", "post", Route = null)] HttpRequest req,
            ILogger log)
        {
            log.LogInformation("C# HTTP trigger function processed a request.");
            string response = string.Empty;
            BinaryData events = await BinaryData.FromStreamAsync(req.Body);
            log.LogInformation($"Received events: {events}");

            EventGridEvent[] eventGridEvents = EventGridEvent.ParseMany(events);

            foreach (EventGridEvent eventGridEvent in eventGridEvents)
            {
                // Handle system events
                if (eventGridEvent.TryGetSystemEventData(out object eventData))
                {
                    // Handle the subscription validation event
                    if (eventData is SubscriptionValidationEventData subscriptionValidationEventData)
                    {
                        log.LogInformation($"Got SubscriptionValidation event data, validation code: {subscriptionValidationEventData.ValidationCode}, topic: {eventGridEvent.Topic}");
                        // Do any additional validation (as required) and then return back the below response

                        var responseData = new SubscriptionValidationResponse()
                        {
                            ValidationResponse = subscriptionValidationEventData.ValidationCode
                        };
                        return new OkObjectResult(responseData);
                    }
                }
            }
            return new OkObjectResult(response);
        }
    }
}
```

```javascript
module.exports = function (context, req) {
    context.log('JavaScript HTTP trigger function begun');
    var validationEventType = "Microsoft.EventGrid.SubscriptionValidationEvent";

    for (var events in req.body) {
        var body = req.body[events];
        // Deserialize the event data into the appropriate type based on event type
        if (body.data && body.eventType == validationEventType) {
            context.log("Got SubscriptionValidation event data, validation code: " + body.data.validationCode + " topic: " + body.topic);

            // Do any additional validation (as required) and then return back the below response
            var code = body.data.validationCode;
            context.res = { status: 200, body: { "ValidationResponse": code } };
        }
    }
    context.done();
};
```

### <a name="test-validation-response"></a>検証の応答をテストする

サンプル イベントを関数のテスト フィールドに貼り付けることによって、検証応答関数をテストします。

```json
[{
  "id": "2d1781af-3a4c-4d7c-bd0c-e34b19da4e66",
  "topic": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "subject": "",
  "data": {
    "validationCode": "512d38b6-c7b8-40c8-89fe-f46f9e9622b6"
  },
  "eventType": "Microsoft.EventGrid.SubscriptionValidationEvent",
  "eventTime": "2018-01-25T22:12:19.4556811Z",
  "metadataVersion": "1",
  "dataVersion": "1"
}]
```

[実行] をクリックすると、200 OK と、本文に `{"validationResponse":"512d38b6-c7b8-40c8-89fe-f46f9e9622b6"}` が出力されます。

:::image type="content" source="./media/receive-events/validation-request.png" alt-text="検証要求":::

:::image type="content" source="./media/receive-events/validation-output.png" alt-text="検証の出力":::

## <a name="handle-blob-storage-events"></a>BLOB ストレージ イベントを処理する

次に、`Microsoft.Storage.BlobCreated` システム イベントを処理するように関数を拡張してみましょう。

```c#
using System.Threading.Tasks;
using Microsoft.AspNetCore.Mvc;
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Extensions.Http;
using Microsoft.AspNetCore.Http;
using Microsoft.Extensions.Logging;
using System;
using Azure.Messaging.EventGrid;
using Azure.Messaging.EventGrid.SystemEvents;

namespace Function1
{
    public static class Function1
    {
        [FunctionName("Function1")]
        public static async Task<IActionResult> Run(
            [HttpTrigger(AuthorizationLevel.Anonymous, "get", "post", Route = null)] HttpRequest req,
            ILogger log)
        {
            log.LogInformation("C# HTTP trigger function processed a request.");
            string response = string.Empty;
            BinaryData events = await BinaryData.FromStreamAsync(req.Body);
            log.LogInformation($"Received events: {events}");

            EventGridEvent[] eventGridEvents = EventGridEvent.ParseMany(events);

            foreach (EventGridEvent eventGridEvent in eventGridEvents)
            {
                // Handle system events
                if (eventGridEvent.TryGetSystemEventData(out object eventData))
                {
                    // Handle the subscription validation event
                    if (eventData is SubscriptionValidationEventData subscriptionValidationEventData)
                    {
                        log.LogInformation($"Got SubscriptionValidation event data, validation code: {subscriptionValidationEventData.ValidationCode}, topic: {eventGridEvent.Topic}");
                        // Do any additional validation (as required) and then return back the below response

                        var responseData = new SubscriptionValidationResponse()
                        {
                            ValidationResponse = subscriptionValidationEventData.ValidationCode
                        };
                        return new OkObjectResult(responseData);
                    }
                    // Handle the storage blob created event
                    else if (eventData is StorageBlobCreatedEventData storageBlobCreatedEventData)
                    {
                        log.LogInformation($"Got BlobCreated event data, blob URI {storageBlobCreatedEventData.Url}");
                    }
                }
            }
            return new OkObjectResult(response);
        }
    }
}
```

```javascript
module.exports = function (context, req) {
    context.log('JavaScript HTTP trigger function begun');
    var validationEventType = "Microsoft.EventGrid.SubscriptionValidationEvent";
    var storageBlobCreatedEvent = "Microsoft.Storage.BlobCreated";

    for (var events in req.body) {
        var body = req.body[events];
        // Deserialize the event data into the appropriate type based on event type  
        if (body.data && body.eventType == validationEventType) {
            context.log("Got SubscriptionValidation event data, validation code: " + body.data.validationCode + " topic: " + body.topic);

            // Do any additional validation (as required) and then return back the below response
            var code = body.data.validationCode;
            context.res = { status: 200, body: { "ValidationResponse": code } };
        }

        else if (body.data && body.eventType == storageBlobCreatedEvent) {
            var blobCreatedEventData = body.data;
            context.log("Relaying received blob created event payload:" + JSON.stringify(blobCreatedEventData));
        }
    }
    context.done();
};

```

### <a name="test-blob-created-event-handling"></a>イベント処理用に作成した BLOB をテストする

テスト フィールドに [BLOB ストレージ イベント](./event-schema-blob-storage.md#example-event)を配置して実行することで、関数の新しい機能をテストします。

```json
[{
  "topic": "/subscriptions/{subscription-id}/resourceGroups/Storage/providers/Microsoft.Storage/storageAccounts/xstoretestaccount",
  "subject": "/blobServices/default/containers/testcontainer/blobs/testfile.txt",
  "eventType": "Microsoft.Storage.BlobCreated",
  "eventTime": "2017-06-26T18:41:00.9584103Z",
  "id": "831e1650-001e-001b-66ab-eeb76e069631",
  "data": {
    "api": "PutBlockList",
    "clientRequestId": "6d79dbfb-0e37-4fc4-981f-442c9ca65760",
    "requestId": "831e1650-001e-001b-66ab-eeb76e000000",
    "eTag": "0x8D4BCC2E4835CD0",
    "contentType": "text/plain",
    "contentLength": 524288,
    "blobType": "BlockBlob",
    "url": "https://example.blob.core.windows.net/testcontainer/testfile.txt",
    "sequencer": "00000000000004420000000000028963",
    "storageDiagnostics": {
      "batchId": "b68529f3-68cd-4744-baa4-3c0498ec19f0"
    }
  },
  "dataVersion": "",
  "metadataVersion": "1"
}]
```

関数ログに BLOB URL が出力されます。

![出力ログ](./media/receive-events/blob-event-response.png)

Blob ストレージ アカウントまたは General Purpose V2 (GPv2) Storage アカウントを作成し、[イベント サブスクリプションを追加](../storage/blobs/storage-blob-event-quickstart.md)し、エンドポイントを関数 URL に設定してテストすることもできます。

![関数の URL](./media/receive-events/function-url.png)

## <a name="handle-custom-events&quot;></a>カスタム イベントを処理する

最後に、関数をもう一度拡張して、カスタム イベントも処理できるようにしましょう。 

イベント `Contoso.Items.ItemReceived` のチェックを追加します。 最終的なコードは、次のようになります。

```c#
using System.Threading.Tasks;
using Microsoft.AspNetCore.Mvc;
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Extensions.Http;
using Microsoft.AspNetCore.Http;
using Microsoft.Extensions.Logging;
using System;
using Azure.Messaging.EventGrid;
using Azure.Messaging.EventGrid.SystemEvents;

namespace Function1
{
    public static class Function1
    {
        [FunctionName(&quot;Function1")]
        public static async Task<IActionResult> Run(
            [HttpTrigger(AuthorizationLevel.Anonymous, "get", "post", Route = null)] HttpRequest req,
            ILogger log)
        {
            log.LogInformation("C# HTTP trigger function processed a request.");
            string response = string.Empty;
            BinaryData events = await BinaryData.FromStreamAsync(req.Body);
            log.LogInformation($"Received events: {events}");

            EventGridEvent[] eventGridEvents = EventGridEvent.ParseMany(events);

            foreach (EventGridEvent eventGridEvent in eventGridEvents)
            {
                // Handle system events
                if (eventGridEvent.TryGetSystemEventData(out object eventData))
                {
                    // Handle the subscription validation event
                    if (eventData is SubscriptionValidationEventData subscriptionValidationEventData)
                    {
                        log.LogInformation($"Got SubscriptionValidation event data, validation code: {subscriptionValidationEventData.ValidationCode}, topic: {eventGridEvent.Topic}");
                        // Do any additional validation (as required) and then return back the below response

                        var responseData = new SubscriptionValidationResponse()
                        {
                            ValidationResponse = subscriptionValidationEventData.ValidationCode
                        };
                        return new OkObjectResult(responseData);
                    }
                    // Handle the storage blob created event
                    else if (eventData is StorageBlobCreatedEventData storageBlobCreatedEventData)
                    {
                        log.LogInformation($"Got BlobCreated event data, blob URI {storageBlobCreatedEventData.Url}");
                    }
                }
                // Handle the custom contoso event
                else if (eventGridEvent.EventType == "Contoso.Items.ItemReceived")
                {
                    var contosoEventData = eventGridEvent.Data.ToObjectFromJson<ContosoItemReceivedEventData>();
                    log.LogInformation($"Got ContosoItemReceived event data, item SKU {contosoEventData.ItemSku}");
                }
            }
            return new OkObjectResult(response);
        }
    }
}
```

```javascript
module.exports = function (context, req) {
    context.log('JavaScript HTTP trigger function begun');
    var validationEventType = "Microsoft.EventGrid.SubscriptionValidationEvent";
    var storageBlobCreatedEvent = "Microsoft.Storage.BlobCreated";
    var customEventType = "Contoso.Items.ItemReceived";

    for (var events in req.body) {
        var body = req.body[events];
        // Deserialize the event data into the appropriate type based on event type
        if (body.data && body.eventType == validationEventType) {
            context.log("Got SubscriptionValidation event data, validation code: " + body.data.validationCode + " topic: " + body.topic);

            // Do any additional validation (as required) and then return back the below response
            var code = body.data.validationCode;
            context.res = { status: 200, body: { "ValidationResponse": code } };
        }

        else if (body.data && body.eventType == storageBlobCreatedEvent) {
            var blobCreatedEventData = body.data;
            context.log("Relaying received blob created event payload:" + JSON.stringify(blobCreatedEventData));
        }

        else if (body.data && body.eventType == customEventType) {
            var payload = body.data;
            context.log("Relaying received custom payload:" + JSON.stringify(payload));
        }
    }
    context.done();
};
```

### <a name="test-custom-event-handling"></a>カスタム イベントの処理をテストする

最後に、関数がカスタム イベントの種類を処理できるようになっていることをテストします。

```json
[{
    "subject": "Contoso/foo/bar/items",
    "eventType": "Contoso.Items.ItemReceived",
    "eventTime": "2017-08-16T01:57:26.005121Z",
    "id": "602a88ef-0001-00e6-1233-1646070610ea",
    "data": { 
            "itemSku": "Standard"
            },
    "dataVersion": "",
    "metadataVersion": "1"
}]
```

この機能は、[カスタム イベントと CURL をポータルから送信する](./custom-event-quickstart-portal.md)か、[Postman](https://www.getpostman.com/) などのエンドポイントに POST できる任意のサービスまたはアプリケーションを使用して[カスタム トピックを投稿する](./post-to-custom-topic.md)ことで、ライブでテストすることもできます。 エンドポイントが関数 URL として設定されたカスタム トピックとイベント サブスクリプションを作成します。

[!INCLUDE [event-grid-message-headers](./includes/event-grid-message-headers.md)]

## <a name="next-steps"></a>次のステップ

* [Azure Event Grid の Management SDK と Publish SDK](./sdk-overview.md) を確認する
* [カスタム トピックを投稿する](./post-to-custom-topic.md)方法を確認する
* [BLOB ストレージにアップロード済みの画像のサイズ変更](resize-images-on-storage-blob-upload-event.md)などの Event Grid と関数の詳細なチュートリアルを試す
