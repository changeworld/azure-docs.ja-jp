---
title: Azure Functions の Event Grid トリガー
description: Azure Functions で Event Grid イベントを処理する方法を説明します。
services: functions
documentationcenter: na
author: tdykstra
manager: cfowler
editor: ''
tags: ''
keywords: ''
ms.service: functions
ms.devlang: multiple
ms.topic: reference
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 01/26/2018
ms.author: tdykstra
ms.openlocfilehash: a1ffd9311f6ff171502efe64557463abc49ad636
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/08/2018
---
# <a name="event-grid-trigger-for-azure-functions"></a>Azure Functions の Event Grid トリガー

この記事では、Azure Functions で [Event Grid](../event-grid/overview.md) イベントを処理する方法を説明します。

Event Grid は、"*パブリッシャー*" 内で発生したイベントについてユーザーに通知する HTTP 要求を送信する Azure サービスです。 パブリッシャーは、イベントを生成するサービスまたはリソースです。 たとえば、Azure Blob Storage アカウントはパブリッシャーであり、BLOB のアップロードまたは削除がイベントです。 一部の [Azure サービスには、Event Grid にイベントを発行するサポートが組み込まれています](../event-grid/overview.md#event-publishers)。 

イベント "*ハンドラー*" は、イベントを受信して処理します。 Azure Functions は、[Event Grid イベントを処理する組み込みサポートを備えている Azure サービス](../event-grid/overview.md#event-handlers)の 1 つです。 この記事では、Event Grid からイベントを受信したときに、Event Grid トリガーを使って関数を呼び出す方法を説明します。

好みに応じて、Event Grid イベントの処理に HTTP トリガーを使うこともできます。後の「[Event Grid トリガーとして HTTP トリガーを使用する](#use-an-http-trigger-as-an-event-grid-trigger)」をご覧ください。

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

## <a name="packages"></a>パッケージ

Event Grid トリガーは、[Microsoft.Azure.WebJobs.Extensions.EventGrid](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.EventGrid) NuGet パッケージで提供されます。 パッケージのソース コードは、[azure-functions-eventgrid-extension](https://github.com/Azure/azure-functions-eventgrid-extension) GitHub リポジトリにあります。

このパッケージは、[C# クラス ライブラリ開発](functions-triggers-bindings.md#local-c-development-using-visual-studio-or-vs-code)と [Functions v2 バインド拡張機能登録](functions-triggers-bindings.md#local-development-azure-functions-core-tools)に使用されます。

<!--
If you want to bind to the `Microsoft.Azure.EventGrid.Models.EventGridEvent` type instead of `JObject`, install the [Microsoft.Azure.EventGrid](https://www.nuget.org/packages/Microsoft.Azure.EventGrid) package.
-->

## <a name="example"></a>例

Event Grid トリガーの言語固有の例をご覧ください。

* [C#](#c-example)
* [C# スクリプト (.csx)](#c-script-example)
* [JavaScript](#javascript-example)

HTTP トリガーの例については、後の「[HTTP トリガーを使用する方法](#use-an-http-trigger-as-an-event-grid-trigger)」をご覧ください。

### <a name="c-example"></a>C# の例

次の例は、`JObject` にバインドする[ C# 関数](functions-dotnet-class-library.md)を示したものです。

```cs
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Host;
using Microsoft.Azure.WebJobs.Extensions.EventGrid;
using Newtonsoft.Json;
using Newtonsoft.Json.Linq;

namespace Company.Function
{
    public static class EventGridTriggerCSharp
    {
        [FunctionName("EventGridTriggerCSharp")]
        public static void Run([EventGridTrigger]JObject eventGridEvent, TraceWriter log)
        {
            log.Info(eventGridEvent.ToString(Formatting.Indented));
        }
    }
}
```

<!--
The following example shows a [C# function](functions-dotnet-class-library.md) that binds to `EventGridEvent`:

```cs
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Host;
using Microsoft.Azure.WebJobs.Extensions.EventGrid;

namespace Company.Function
{
    public static class EventGridTriggerCSharp
    {
        [FunctionName("EventGridTest")]
            public static void EventGridTest([EventGridTrigger] Microsoft.Azure.EventGrid.Models.EventGridEvent eventGridEvent, TraceWriter log)
        {
            log.Info("C# Event Grid function processed a request.");
            log.Info($"Subject: {eventGridEvent.Subject}");
            log.Info($"Time: {eventGridEvent.EventTime}");
            log.Info($"Data: {eventGridEvent.Data.ToString()}");
        }
    }
}
```
-->

詳しくは、「[パッケージ](#packages)」、「[属性](#attributes)」、「[構成](#configuration)」、および「[使用法](#usage)」をご覧ください。

### <a name="c-script-example"></a>C# スクリプトの例

次の例は、*function.json* ファイルのトリガー バインドと、そのバインドが使用される [C# スクリプト関数](functions-reference-csharp.md)を示しています。

*function.json* ファイルのバインディング データを次に示します。

```json
{
  "bindings": [
    {
      "type": "eventGridTrigger",
      "name": "eventGridEvent",
      "direction": "in"
    }
  ],
  "disabled": false
}
```

`JObject` にバインドする C# スクリプト コードを次に示します。

```cs
#r "Newtonsoft.Json"

using Newtonsoft.Json;
using Newtonsoft.Json.Linq;

public static void Run(JObject eventGridEvent, TraceWriter log)
{
    log.Info(eventGridEvent.ToString(Formatting.Indented));
}
```

<!--
Here's C# script code that binds to `EventGridEvent`:

```csharp
#r "Newtonsoft.Json"
#r "Microsoft.Azure.WebJobs.Extensions.EventGrid"
#r "Microsoft.Azure.EventGrid"

using Microsoft.Azure.WebJobs.Extensions.EventGrid;
Using Microsoft.Azure.EventGrid.Models;

public static void Run(EventGridEvent eventGridEvent, TraceWriter log)
{
    log.Info("C# Event Grid function processed a request.");
    log.Info($"Subject: {eventGridEvent.Subject}");
    log.Info($"Time: {eventGridEvent.EventTime}");
    log.Info($"Data: {eventGridEvent.Data.ToString()}");
}
```
-->

詳しくは、「[パッケージ](#packages)」、「[属性](#attributes)」、「[構成](#configuration)」、および「[使用法](#usage)」をご覧ください。

### <a name="javascript-example"></a>JavaScript の例

次の例は、*function.json* ファイルのトリガー バインドと、そのバインドを使用する [JavaScript 関数](functions-reference-node.md)を示しています。

*function.json* ファイルのバインディング データを次に示します。

```json
{
  "bindings": [
    {
      "type": "eventGridTrigger",
      "name": "eventGridEvent",
      "direction": "in"
    }
  ],
  "disabled": false
}
```

JavaScript コードを次に示します。

```javascript
module.exports = function (context, eventGridEvent) {
    context.log("JavaScript Event Grid function processed a request.");
    context.log("Subject: " + eventGridEvent.subject);
    context.log("Time: " + eventGridEvent.eventTime);
    context.log("Data: " + JSON.stringify(eventGridEvent.data));
    context.done();
};
```
     
## <a name="attributes"></a>属性

[C# クラス ライブラリ](functions-dotnet-class-library.md)では、[EventGridTrigger](https://github.com/Azure/azure-functions-eventgrid-extension/blob/master/src/EventGridExtension/EventGridTriggerAttribute.cs) 属性を使用します。

メソッド シグネチャでの `EventGridTrigger` 属性を次に示します。

```csharp
[FunctionName("EventGridTest")]
public static void EventGridTest([EventGridTrigger] JObject eventGridEvent, TraceWriter log)
{
    ...
}
 ```

完全な例については、「[C# の例](#c-example)」を参照してください。

## <a name="configuration"></a>構成

次の表は、*function.json* ファイルで設定したバインド構成のプロパティを説明しています。 `EventGridTrigger` 属性で設定するコンストラクター パラメーターまたはプロパティはありません。

|function.json のプロパティ |[説明]|
|---------|---------|----------------------|
| **type** | 必須 - `eventGridTrigger` に設定する必要があります。 |
| **direction** | 必須 - `in` に設定する必要があります。 |
| **name** | 必須 - イベント データを受信するパラメーターの、関数コードで使われている変数名。 |

## <a name="usage"></a>使用法

C# および F# 関数については、Event Grid トリガーに次のパラメーター型を使用できます。

* `JObject`
* `string`
* `Microsoft.Azure.WebJobs.Extensions.EventGrid.EventGridEvent`- すべてのイベントの種類に共通するフィールドのプロパティを定義します。 **この型は使用されなくなりました**が、それに置き換わるものがまだ NuGet に公開されていません。

JavaScript 関数では、*function.json* `name` プロパティによって指定されているパラメーターが、イベント オブジェクトへの参照を保持しています。

## <a name="event-schema"></a>イベント スキーマ

Event Grid イベントのデータは、HTTP 要求の本文内の JSON オブジェクトとして受信されます。 JSON は次の例のようになります。

```json
[{
  "topic": "/subscriptions/{subscriptionid}/resourceGroups/eg0122/providers/Microsoft.Storage/storageAccounts/egblobstore",
  "subject": "/blobServices/default/containers/{containername}/blobs/blobname.jpg",
  "eventType": "Microsoft.Storage.BlobCreated",
  "eventTime": "2018-01-23T17:02:19.6069787Z",
  "id": "{guid}",
  "data": {
    "api": "PutBlockList",
    "clientRequestId": "{guid}",
    "requestId": "{guid}",
    "eTag": "0x8D562831044DDD0",
    "contentType": "application/octet-stream",
    "contentLength": 2248,
    "blobType": "BlockBlob",
    "url": "https://egblobstore.blob.core.windows.net/{containername}/blobname.jpg",
    "sequencer": "000000000000272D000000000003D60F",
    "storageDiagnostics": {
      "batchId": "{guid}"
    }
  },
  "dataVersion": "",
  "metadataVersion": "1"
}]
```

示されている例は、1 要素の配列です。 Event Grid は常に配列を送信し、配列で複数のイベントを送信できます。 ランタイムは、配列の各要素に対して 1 回、関数を呼び出します。

イベント JSON データの最上位レベルのプロパティはすべてのイベントの種類で同じですが、`data` プロパティの内容は各イベントの種類に固有です。 この例では、BLOB ストレージ イベントのものです。

共通プロパティとイベント固有プロパティについて詳しくは、Event Grid のドキュメントの「[イベントのプロパティ](../event-grid/event-schema.md#event-properties)」をご覧ください。

`EventGridEvent` 型では、最上位レベルのプロパティのみが定義されています。`Data` プロパティは `JObject` です。 

## <a name="create-a-subscription"></a>サブスクリプションの作成

Event Grid の HTTP 要求の受信を始めるには、関数を呼び出すエンドポイント URL を指定する Event Grid サブスクリプションを作成します。

### <a name="azure-portal"></a>Azure ポータル

Event Grid トリガーを使って Azure Portal で開発した関数の場合は、**[vent Grid サブスクリプションの追加]** を選びます。

![ポータルでサブスクリプションを作成する](media/functions-bindings-event-grid/portal-sub-create.png)

このリンクを選ぶと、ポータルに **[イベント サブスクリプションの作成]** ページが表示され、エンドポイント URL があらかじめ設定されています。

![事前設定されたエンドポイント URL](media/functions-bindings-event-grid/endpoint-url.png)

Azure Portal を使ってサブスクリプションを作成する方法について詳しくは、Event Grid のドキュメントの「[カスタム イベントの作成 - Azure Portal](../event-grid/custom-event-quickstart-portal.md)」をご覧ください。

### <a name="azure-cli"></a>Azure CLI

[Azure CLI](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli?view=azure-cli-latest) を使ってサブスクリプションを作成するには、[az eventgrid event-subscription create](https://docs.microsoft.com/cli/azure/eventgrid/event-subscription?view=azure-cli-latest#az_eventgrid_event_subscription_create) コマンドを使います。

このコマンドには、関数を呼び出すエンドポイント URL が必要です。 URL パターンの例を次に示します。

```
https://{functionappname}.azurewebsites.net/admin/extensions/EventGridExtensionConfig?functionName={functionname}&code={systemkey}
```

システム キーは、Event Grid トリガー用のエンドポイント URL に含める必要のある承認キーです。 次のセクションでは、システム キーを取得する方法について説明します。

BLOB ストレージ アカウントをサブスクライブする例を次に示します (システム キーのプレースホルダーを含みます)。

```azurecli
az eventgrid resource event-subscription create -g myResourceGroup \
--provider-namespace Microsoft.Storage --resource-type storageAccounts \
--resource-name glengablobstorage --name myFuncSub  \
--included-event-types Microsoft.Storage.BlobCreated \
--subject-begins-with /blobServices/default/containers/images/blobs/ \
--endpoint https://glengastorageevents.azurewebsites.net/admin/extensions/EventGridExtensionConfig?functionName=imageresizefunc&code=LUwlnhIsNtSiUjv/sNtSiUjvsNtSiUjvsNtSiUjvYb7XDonDUr/RUg==
```

サブスクリプションを作成する方法について詳しくは、[BLOB ストレージのクイック スタート](../storage/blobs/storage-blob-event-quickstart.md#subscribe-to-your-storage-account)または他の Event Grid クイック スタートのページをご覧ください。

### <a name="get-the-system-key"></a>システム キーを取得する

次の API (HTTP GET) を使って、システム キーを取得できます。

```
http://{functionappname}.azurewebsites.net/admin/host/systemkeys/eventgridextensionconfig_extension?code={adminkey}
```

これは管理 API なので、お使いの[管理者キー](functions-bindings-http-webhook.md#authorization-keys)が必要です。 システム キー (Event Grid トリガー関数を呼び出す場合) と管理者キー (関数アプリで管理タスクを実行する場合) を混同しないでください。 Event Grid トピックをサブスクライブするときは、システム キーを使います。

システム キーを提供する応答の例を次に示します。

```
{
  "name": "eventgridextensionconfig_extension",
  "value": "{the system key for the function}",
  "links": [
    {
      "rel": "self",
      "href": "{the URL for the function, without the system key}"
    }
  ]
}
```

詳しくは、HTTP トリガーのリファレンス記事の「[承認キー](functions-bindings-http-webhook.md#authorization-keys)」をご覧ください。 

代わりに、HTTP PUT を送信して自分でキーの値を指定することもできます。

## <a name="local-testing-with-requestbin"></a>RequestBin でのローカル テスト

Event Grid トリガーをローカルにテストするには、クラウド内の送信元からローカル コンピューターに配信された Event Grid の HTTP 要求を取得する必要があります。 これを行う方法の 1 つは、オンラインで要求をキャプチャし、ローカル コンピューター上でそれを手動で再送信することです。

2. [RequestBin エンドポイントを作成](#create-a-RequestBin-endpoint)します。
3. RequestBin エンドポイントにイベントを送信する [Event Grid サブスクリプションを作成](#create-an-event-grid-subscription)します。
4. [要求を生成](#generate-a-request)し、RequestBin サイトから要求の本文をコピーします。
5. Event Grid トリガー関数の localhost URL に[要求を手動で投稿](#manually-post-the-request)します。

テストが完了したら、エンドポイントを更新することで、同じサブスクリプションを運用環境に使うことができます。 [az eventgrid event-subscription update](https://docs.microsoft.com/cli/azure/eventgrid/event-subscription?view=azure-cli-latest#az_eventgrid_event_subscription_update) Azure CLI コマンドを使います。

### <a name="create-a-requestbin-endpoint"></a>RequestBin エンドポイントを作成する

RequestBin は、HTTP 要求を受け入れて要求本文を表示するオープン ソース ツールです。 http://requestb.in の URL は、Azure Event Grid によって特別な処理を行われます。 テストを容易にするため、Event Grid は、サブスクリプション検証要求に対する正しい応答を必要としないで、RequestBin の URL にイベントを送信します。 他の 2 つのテストツール http://webhookinbox.com および http://hookbin.com も、同じように処理されます。

RequestBin は、高いスループットでの使用を意図されていません。 一度に複数のイベントをプッシュすると、一部のイベントがツールから見えない場合があります。

エンドポイントを作成します。

![RequestBin エンドポイントを作成します。](media/functions-bindings-event-grid/create-requestbin.png)

エンドポイントの URL をコピーします。

![RequestBin エンドポイントをコピーします。](media/functions-bindings-event-grid/save-requestbin-url.png)

### <a name="create-an-event-grid-subscription"></a>Event Grid のサブスクリプションを作成する

テストする種類の Event Grid サブスクリプションを作成し、それに RequestBin エンドポイントを提供します。 サブスクリプションを作成する方法については、前の「[サブスクリプションの作成](#create-a-subscription)」をご覧ください。

### <a name="generate-a-request"></a>要求を生成する

RequestBin エンドポイントへの HTTP トラフィックを生成するイベントをトリガーします。  たとえば、BLOB ストレージ サブスクリプションを作成した場合は、BLOB をアップロードまたは削除します。 要求が RequestBin のページに表示されたら、要求の本文をコピーします。

最初に、サブスクリプション検証要求を受信します。検証要求はすべて無視し、イベント要求をコピーします。

![RequestBin から要求本文をコピーする](media/functions-bindings-event-grid/copy-request-body.png)

### <a name="manually-post-the-request"></a>要求を手動で投稿する

Event Grid 関数をローカルで実行します。

[Postman](https://www.getpostman.com/) や [curl](https://curl.haxx.se/docs/httpscripting.html) などのツールを使って、HTTP POST 要求を作成します。

* `Content-Type: application/json` ヘッダーを設定します。
* `aeg-event-type: Notification` ヘッダーを設定します。
* 要求本文に RequestBin のデータを貼り付けます。 
* 次のパターンを使って、Event Grid トリガー関数の URL に投稿します。

```
http://localhost:7071/admin/extensions/EventGridExtensionConfig?functionName={functionname}
``` 

`functionName` パラメーターには、`FunctionName` 属性で指定されている名前を指定する必要があります。

次のスクリーンショットでは、Postman のヘッダーと要求本文を示します。

![Postman でのヘッダー](media/functions-bindings-event-grid/postman2.png)

![Postman での要求本文](media/functions-bindings-event-grid/postman.png)

Event Grid トリガー関数が実行されて、次の例のようなログが表示されます。

![Event Grid トリガー関数のログの例](media/functions-bindings-event-grid/eg-output.png)

## <a name="local-testing-with-ngrok"></a>ngrok でのローカル テスト

Event Grid トリガーをローカルにテストするもう 1 つの方法は、インターネットと開発用コンピューターの間の HTTP 接続を自動化することです。 これは、[ngrok](https://ngrok.com/) という名前のオープン ソース ツールを使って行うことができます。

3. [ngrok のエンドポイントを作成](#create-an-ngrok-endpoint)します。
4. [Event Grid トリガー関数を実行](#run-the-event-grid-trigger-function)します。
5. ngrok のエンドポイントにイベントを送信する [Event Grid サブスクリプションを作成](#create-a-subscription)します。
6. [イベントをトリガー](#trigger-an-event)します。

テストが完了したら、エンドポイントを更新することで、同じサブスクリプションを運用環境に使うことができます。 [az eventgrid event-subscription update](https://docs.microsoft.com/cli/azure/eventgrid/event-subscription?view=azure-cli-latest#az_eventgrid_event_subscription_update) Azure CLI コマンドを使います。

### <a name="create-an-ngrok-endpoint"></a>ngrok のエンドポイントを作成する

[ngrok](https://ngrok.com/) から *ngrok.exe* をダウンロードし、次のコマンドで実行します。

```
ngrok http -host-header=localhost 7071
```

-host-header パラメーターは、関数ランタイムは localhost で実行するときは localhost からの要求を期待するので必要です。 7071 は、ランタイムがローカルで実行するときの既定のポート番号です。

コマンドは、次のような出力を作成します。

```
Session Status                online
Version                       2.2.8
Region                        United States (us)
Web Interface                 http://127.0.0.1:4040
Forwarding                    http://263db807.ngrok.io -> localhost:7071
Forwarding                    https://263db807.ngrok.io -> localhost:7071

Connections                   ttl     opn     rt1     rt5     p50     p90
                              0       0       0.00    0.00    0.00    0.00
```

Event Grid サブスクリプションには、https://<サブドメイン>.ngrok.io という URL を使います。

### <a name="run-the-event-grid-trigger-function"></a>Event Grid トリガー関数を実行する

ngrok の URL は Event Grid によって特別に処理されないので、サブスクリプションが作成される時点で、関数がローカルで実行している必要があります。 実行していないと、検証応答が送信されず、サブスクリプションの作成が失敗します。

### <a name="create-a-subscription"></a>サブスクリプションの作成

テストする種類の Event Grid サブスクリプションを作成し、次のパターンを使って、それに ngrok のエンドポイントを提供します。

```
https://{subdomain}.ngrok.io/admin/extensions/EventGridExtensionConfig?functionName={functionname}
``` 

`functionName` パラメーターには、`FunctionName` 属性で指定されている名前を指定する必要があります。

Azure CLI を使う例を次に示します。

```
az eventgrid event-subscription create --resource-id /subscriptions/aeb4b7cb-b7cb-b7cb-b7cb-b7cbb6607f30/resourceGroups/eg0122/providers/Microsoft.Storage/storageAccounts/egblobstor0122 --name egblobsub0126 --endpoint https://263db807.ngrok.io/admin/extensions/EventGridExtensionConfig?functionName=EventGridTrigger
```

サブスクリプションを作成する方法については、前の「[サブスクリプションの作成](#create-a-subscription)」をご覧ください。

### <a name="trigger-an-event"></a>イベントをトリガーする

ngrok のエンドポイントへの HTTP トラフィックを生成するイベントをトリガーします。  たとえば、BLOB ストレージ サブスクリプションを作成した場合は、BLOB をアップロードまたは削除します。

Event Grid トリガー関数が実行されて、次の例のようなログが表示されます。

![Event Grid トリガー関数のログの例](media/functions-bindings-event-grid/eg-output.png)

## <a name="use-an-http-trigger-as-an-event-grid-trigger"></a>Event Grid トリガーとして HTTP トリガーを使用する

Event Grid イベントは HTTP 要求として受信されるので、Event Grid トリガーの代わりに HTTP トリガーを使ってイベントを処理できます。 これを行う理由の 1 つとして考えられるのは、関数を呼び出すエンドポイントの URL をより詳細に制御するためです。 

HTTP トリガーを使う場合は、Event Grid トリガーによって自動的に行われる処理のコードを記述する必要があります。

* [サブスクリプション検証要求](../event-grid/security-authentication.md#webhook-event-delivery)に検証応答を送信します。
* 要求本文に含まれるイベント配列の要素ごとに、関数を 1 回呼び出します。

次に示す HTTP トリガーの C# コード サンプルは、Event Grid トリガーの動作をシミュレートします。

```csharp
[FunctionName("HttpTrigger")]
public static async Task<HttpResponseMessage> Run(
    [HttpTrigger(AuthorizationLevel.Anonymous, "post")]HttpRequestMessage req,
    TraceWriter log)
{
    log.Info("C# HTTP trigger function processed a request.");

    var messages = await req.Content.ReadAsAsync<JArray>();

    // If the request is for subscription validation, send back the validation code.
    if (messages.Count > 0 && string.Equals((string)messages[0]["eventType"], 
        "Microsoft.EventGrid.SubscriptionValidationEvent", 
        System.StringComparison.OrdinalIgnoreCase))
    {
        log.Info("Validate request received");
        return req.CreateResponse<object>(new
        {
            validationResponse = messages[0]["data"]["validationCode"]
        });
    }

    // The request is not for subscription validation, so it's for one or more events.
    foreach (JObject message in messages)
    {
        // Handle one event.
        EventGridEvent eventGridEvent = message.ToObject<EventGridEvent>();
        log.Info($"Subject: {eventGridEvent.Subject}");
        log.Info($"Time: {eventGridEvent.EventTime}");
        log.Info($"Event data: {eventGridEvent.Data.ToString()}");
    }

    return req.CreateResponse(HttpStatusCode.OK);
}
```

次に示す HTTP トリガーの JavaScript コード サンプルは、Event Grid トリガーの動作をシミュレートします。

```javascript
module.exports = function (context, req) {
    context.log('JavaScript HTTP trigger function processed a request.');

    var messages = req.body;
    // If the request is for subscription validation, send back the validation code.
    if (messages.length > 0 && messages[0].eventType == "Microsoft.EventGrid.SubscriptionValidationEvent") {
        context.log('Validate request received');
        context.res = { status: 200, body: messages[0].data.validationCode }
    }
    else {
        // The request is not for subscription validation, so it's for one or more events.
        for (var i = 0; i < messages.length; i++) {
            // Handle one event.
            var message = messages[i];
            context.log('Subject: ' + message.subject);
            context.log('Time: ' + message.eventTime);
            context.log('Data: ' + JSON.stringify(message.data));
        }
    }
    context.done();
};
```

イベント処理コードは、`messages` 配列のループ内を処理します。

関数をローカルに呼び出す場合、または Azure 内で実行するときに使う URL については、[HTTP トリガーのバインドに関するリファレンス ドキュメント](functions-bindings-http-webhook.md)をご覧ください 

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [Azure Functions のトリガーとバインドの詳細情報](functions-triggers-bindings.md)

> [!div class="nextstepaction"]
> [Event Grid の詳細を確認します](../event-grid/overview.md)
