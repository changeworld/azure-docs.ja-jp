---
title: Azure Functions の Azure Event Grid 出力バインディング
description: Azure Functions で Event Grid イベントを送信する方法について説明します。
author: craigshoemaker
ms.topic: reference
ms.date: 02/14/2020
ms.author: cshoe
ms.custom: devx-track-csharp, fasttrack-edit, devx-track-python
ms.openlocfilehash: f29302efdf6d2a0c0b12ec15d897efda16cdc368
ms.sourcegitcommit: 8000045c09d3b091314b4a73db20e99ddc825d91
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/19/2021
ms.locfileid: "122444417"
---
# <a name="azure-event-grid-output-binding-for-azure-functions"></a>Azure Functions の Azure Event Grid 出力バインディング

イベントをカスタム トピックに書き込むには、Event Grid 出力バインディングを使用します。 有効な[カスタム トピックのアクセス キー](../event-grid/security-authenticate-publishing-clients.md)が必要です。

セットアップと構成の詳細については、[概要](./functions-bindings-event-grid.md)を参照してください。

> [!NOTE]
> Event Grid 出力バインディングでは、Shared Access Signature (SAS トークン) はサポートされていません。 トピックのアクセス キーを使用する必要があります。

> [!IMPORTANT]
> Event Grid 出力バインディングは、Functions 2.x 以降でのみ使用することができます。

## <a name="example"></a>例

# <a name="c"></a>[C#](#tab/csharp)

### <a name="c-2x-and-higher"></a>C# (2.x 以降)

次の例は、メソッドの戻り値を出力として使用してメッセージを Event Grid のカスタム トピックに書き込む [C# 関数](functions-dotnet-class-library.md)を示しています。

```csharp
[FunctionName("EventGridOutput")]
[return: EventGrid(TopicEndpointUri = "MyEventGridTopicUriSetting", TopicKeySetting = "MyEventGridTopicKeySetting")]
public static EventGridEvent Run([TimerTrigger("0 */5 * * * *")] TimerInfo myTimer, ILogger log)
{
    return new EventGridEvent("message-id", "subject-name", "event-data", "event-type", DateTime.UtcNow, "1.0");
}
```

次の例では、`IAsyncCollector` インターフェイスを使用してメッセージのバッチを送信する方法を示します。

```csharp
[FunctionName("EventGridAsyncOutput")]
public static async Task Run(
    [TimerTrigger("0 */5 * * * *")] TimerInfo myTimer,
    [EventGrid(TopicEndpointUri = "MyEventGridTopicUriSetting", TopicKeySetting = "MyEventGridTopicKeySetting")]IAsyncCollector<EventGridEvent> outputEvents,
    ILogger log)
{
    for (var i = 0; i < 3; i++)
    {
        var myEvent = new EventGridEvent("message-id-" + i, "subject-name", "event-data", "event-type", DateTime.UtcNow, "1.0");
        await outputEvents.AddAsync(myEvent);
    }
}
```

### <a name="version-3x-preview"></a>バージョン 3.x (プレビュー)

次の例は、`CloudEvent` にバインドする Functions 3.x の [C# 関数](functions-dotnet-class-library.md)を示したものです。

```cs
using System.Threading.Tasks;
using Azure.Messaging;
using Microsoft.AspNetCore.Http;
using Microsoft.AspNetCore.Mvc;
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Extensions.EventGrid;
using Microsoft.Azure.WebJobs.Extensions.Http;

namespace Azure.Extensions.WebJobs.Sample
{
    public static class CloudEventBindingFunction
    {
        [FunctionName("CloudEventBindingFunction")]
        public static async Task<IActionResult> RunAsync(
            [HttpTrigger(AuthorizationLevel.Anonymous, "get", "post", Route = null)] HttpRequest req,
            [EventGrid(TopicEndpointUri = "EventGridEndpoint", TopicKeySetting = "EventGridKey")] IAsyncCollector<CloudEvent> eventCollector)
        {
            CloudEvent e = new CloudEvent("IncomingRequest", "IncomingRequest", await req.ReadAsStringAsync());
            await eventCollector.AddAsync(e);
            return new OkResult();
        }
    }
}
```

次の例は、`EventGridEvent` にバインドする Functions 3.x の [C# 関数](functions-dotnet-class-library.md)を示したものです。

```cs
using System.Threading.Tasks;
using Azure.Messaging.EventGrid;
using Microsoft.AspNetCore.Mvc;
using Microsoft.AspNetCore.Http;
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Extensions.Http;
using Microsoft.Azure.WebJobs.Extensions.EventGrid;

namespace Azure.Extensions.WebJobs.Sample
{
    public static class EventGridEventBindingFunction
    {
        [FunctionName("EventGridEventBindingFunction")]
        public static async Task<IActionResult> RunAsync(
            [HttpTrigger(AuthorizationLevel.Anonymous, "get", "post", Route = null)] HttpRequest req,
            [EventGrid(TopicEndpointUri = "EventGridEndpoint", TopicKeySetting = "EventGridKey")] IAsyncCollector<EventGridEvent> eventCollector)
        {
            EventGridEvent e = new EventGridEvent(await req.ReadAsStringAsync(), "IncomingRequest", "IncomingRequest", "1.0.0");
            await eventCollector.AddAsync(e);
            return new OkResult();
        }
    }
}
```

# <a name="c-script"></a>[C# スクリプト](#tab/csharp-script)

次の例は、*function.json* ファイル内の Event Grid 出力バインディング データを示しています。

```json
{
    "type": "eventGrid",
    "name": "outputEvent",
    "topicEndpointUri": "MyEventGridTopicUriSetting",
    "topicKeySetting": "MyEventGridTopicKeySetting",
    "direction": "out"
}
```

1 つのイベントを作成する C# スクリプト コードを次に示します。

```cs
#r "Microsoft.Azure.EventGrid"
using System;
using Microsoft.Azure.EventGrid.Models;
using Microsoft.Extensions.Logging;

public static void Run(TimerInfo myTimer, out EventGridEvent outputEvent, ILogger log)
{
    outputEvent = new EventGridEvent("message-id", "subject-name", "event-data", "event-type", DateTime.UtcNow, "1.0");
}
```

複数のイベントを作成する C# スクリプト コードを次に示します。

```cs
#r "Microsoft.Azure.EventGrid"
using System;
using Microsoft.Azure.EventGrid.Models;
using Microsoft.Extensions.Logging;

public static void Run(TimerInfo myTimer, ICollector<EventGridEvent> outputEvent, ILogger log)
{
    outputEvent.Add(new EventGridEvent("message-id-1", "subject-name", "event-data", "event-type", DateTime.UtcNow, "1.0"));
    outputEvent.Add(new EventGridEvent("message-id-2", "subject-name", "event-data", "event-type", DateTime.UtcNow, "1.0"));
}
```

# <a name="java"></a>[Java](#tab/java)

次の例は、メッセージを Event Grid カスタム トピックに書き込む Java 関数を示しています。 この関数では、バインドの `setValue` メソッドを使用して文字列を出力します。

```java
public class Function {
    @FunctionName("EventGridTriggerTest")
    public void run(@EventGridTrigger(name = "event") String content,
            @EventGridOutput(name = "outputEvent", topicEndpointUri = "MyEventGridTopicUriSetting", topicKeySetting = "MyEventGridTopicKeySetting") OutputBinding<String> outputEvent,
            final ExecutionContext context) {
        context.getLogger().info("Java EventGrid trigger processed a request." + content);
        final String eventGridOutputDocument = "{\"id\": \"1807\", \"eventType\": \"recordInserted\", \"subject\": \"myapp/cars/java\", \"eventTime\":\"2017-08-10T21:03:07+00:00\", \"data\": {\"make\": \"Ducati\",\"model\": \"Monster\"}, \"dataVersion\": \"1.0\"}";
        outputEvent.setValue(eventGridOutputDocument);
    }
}
```

POJO クラスを使用して EventGrid メッセージを送信することもできます。

```java
public class Function {
    @FunctionName("EventGridTriggerTest")
    public void run(@EventGridTrigger(name = "event") String content,
            @EventGridOutput(name = "outputEvent", topicEndpointUri = "MyEventGridTopicUriSetting", topicKeySetting = "MyEventGridTopicKeySetting") OutputBinding<EventGridEvent> outputEvent,
            final ExecutionContext context) {
        context.getLogger().info("Java EventGrid trigger processed a request." + content);

        final EventGridEvent eventGridOutputDocument = new EventGridEvent();
        eventGridOutputDocument.setId("1807");
        eventGridOutputDocument.setEventType("recordInserted");
        eventGridOutputDocument.setEventTime("2017-08-10T21:03:07+00:00");
        eventGridOutputDocument.setDataVersion("1.0");
        eventGridOutputDocument.setSubject("myapp/cars/java");
        eventGridOutputDocument.setData("{\"make\": \"Ducati\",\"model\":\"monster\"");

        outputEvent.setValue(eventGridOutputDocument);
    }
}

class EventGridEvent {
    private String id;
    private String eventType;
    private String subject;
    private String eventTime;
    private String dataVersion;
    private String data;

    public String getId() {
        return id;
    }

    public String getData() {
        return data;
    }

    public void setData(String data) {
        this.data = data;
    }

    public String getDataVersion() {
        return dataVersion;
    }

    public void setDataVersion(String dataVersion) {
        this.dataVersion = dataVersion;
    }

    public String getEventTime() {
        return eventTime;
    }

    public void setEventTime(String eventTime) {
        this.eventTime = eventTime;
    }

    public String getSubject() {
        return subject;
    }

    public void setSubject(String subject) {
        this.subject = subject;
    }

    public String getEventType() {
        return eventType;
    }

    public void setEventType(String eventType) {
        this.eventType = eventType;
    }

    public void setId(String id) {
        this.id = id;
    }  
}
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

次の例は、*function.json* ファイル内の Event Grid 出力バインディング データを示しています。

```json
{
    "type": "eventGrid",
    "name": "outputEvent",
    "topicEndpointUri": "MyEventGridTopicUriSetting",
    "topicKeySetting": "MyEventGridTopicKeySetting",
    "direction": "out"
}
```

単一のイベントを作成する JavaScript コードを次に示します。

```javascript
module.exports = async function (context, myTimer) {
    var timeStamp = new Date().toISOString();

    context.bindings.outputEvent = {
        id: 'message-id',
        subject: 'subject-name',
        dataVersion: '1.0',
        eventType: 'event-type',
        data: "event-data",
        eventTime: timeStamp
    };
    context.done();
};
```

複数のイベントを作成する JavaScript コードを次に示します。

```javascript
module.exports = function(context) {
    var timeStamp = new Date().toISOString();

    context.bindings.outputEvent = [];

    context.bindings.outputEvent.push({
        id: 'message-id-1',
        subject: 'subject-name',
        dataVersion: '1.0',
        eventType: 'event-type',
        data: "event-data",
        eventTime: timeStamp
    });
    context.bindings.outputEvent.push({
        id: 'message-id-2',
        subject: 'subject-name',
        dataVersion: '1.0',
        eventType: 'event-type',
        data: "event-data",
        eventTime: timeStamp
    });
    context.done();
};
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

次の例は、Event Grid イベント メッセージを出力するように関数を構成する方法を示しています。 `type` が `eventGrid` に設定されているセクションでは、Event Grid 出力バインディングを確立するのに必要な値が構成されます。

```powershell
{
  "bindings": [
    {
      "type": "eventGrid",
      "name": "outputEvent",
      "topicEndpointUri": "MyEventGridTopicUriSetting",
      "topicKeySetting": "MyEventGridTopicKeySetting",
      "direction": "out"
    },
    {
      "authLevel": "anonymous",
      "type": "httpTrigger",
      "direction": "in",
      "name": "Request",
      "methods": [
        "get",
        "post"
      ]
    },
    {
      "type": "http",
      "direction": "out",
      "name": "Response"
    }
  ]
}
```

関数では、`Push-OutputBinding` を使用して、Event Grid 出力バインディングを介してカスタム トピックにイベントを送信します。

```powershell
using namespace System.Net

# Input bindings are passed in via param block.
param($Request, $TriggerMetadata)

# Write to the Azure Functions log stream.
Write-Host "PowerShell HTTP trigger function processed a request."

# Interact with query parameters or the body of the request.
$message = $Request.Query.Message

Push-OutputBinding -Name outputEvent -Value  @{
    id = "1"
    EventType = "testEvent"
    Subject = "testapp/testPublish"
    EventTime = "2020-08-27T21:03:07+00:00"
    Data = @{
        Message = $message
    }
    DataVersion = "1.0"
}

Push-OutputBinding -Name Response -Value ([HttpResponseContext]@{
    StatusCode = 200
    Body = "OK"
})
```

# <a name="python"></a>[Python](#tab/python)

次の例は、*function.json* ファイルのトリガー バインドと、そのバインドが使用される [Python 関数](functions-reference-python.md)を示しています。 次に、`topicEndpointUri` によって指定されたとおり、イベントがカスタム トピックに送信されます。

*function.json* ファイルのバインディング データを次に示します。

```json
{
  "scriptFile": "__init__.py",
  "bindings": [
    {
      "type": "eventGridTrigger",
      "name": "eventGridEvent",
      "direction": "in"
    },
    {
      "type": "eventGrid",
      "name": "outputEvent",
      "topicEndpointUri": "MyEventGridTopicUriSetting",
      "topicKeySetting": "MyEventGridTopicKeySetting",
      "direction": "out"
    }
  ],
  "disabled": false
}
```

`EventGridOutputEvent` を設定してカスタム トピックにイベントを送信する Python サンプルを次に示します。

```python
import logging
import azure.functions as func
import datetime

def main(eventGridEvent: func.EventGridEvent, 
         outputEvent: func.Out[func.EventGridOutputEvent]) -> None:

    logging.log("eventGridEvent: ", eventGridEvent)

    outputEvent.set(
        func.EventGridOutputEvent(
            id="test-id",
            data={"tag1": "value1", "tag2": "value2"},
            subject="test-subject",
            event_type="test-event-1",
            event_time=datetime.datetime.utcnow(),
            data_version="1.0"))
```

---

## <a name="attributes-and-annotations"></a>属性と注釈

# <a name="c"></a>[C#](#tab/csharp)

[C# クラス ライブラリ](functions-dotnet-class-library.md)では、[EventGridAttribute](https://github.com/Azure/azure-functions-eventgrid-extension/blob/dev/src/EventGridExtension/OutputBinding/EventGridAttribute.cs) 属性を使用します。

属性のコンストラクターは、カスタム トピックの名前を含むアプリ設定の名前と、トピック キーを含むアプリ設定の名前を受け取ります。 これらの設定の詳細については、「[出力 - 構成](#configuration)」を参照してください。 `EventGrid` 属性の例を次に示します。

```csharp
[FunctionName("EventGridOutput")]
[return: EventGrid(TopicEndpointUri = "MyEventGridTopicUriSetting", TopicKeySetting = "MyEventGridTopicKeySetting")]
public static string Run([TimerTrigger("0 */5 * * * *")] TimerInfo myTimer, ILogger log)
{
    ...
}
```

完全な例については、「[例](#example)」を参照してください。

# <a name="c-script"></a>[C# スクリプト](#tab/csharp-script)

属性は、C# スクリプトではサポートされていません。

# <a name="java"></a>[Java](#tab/java)

Java クラスでは、[EventGridAttribute](https://github.com/Azure/azure-functions-java-library/blob/dev/src/main/java/com/microsoft/azure/functions/annotation/EventGridOutput.java) 属性を使用します。

属性のコンストラクターは、カスタム トピックの名前を含むアプリ設定の名前と、トピック キーを含むアプリ設定の名前を受け取ります。 これらの設定の詳細については、「[出力 - 構成](#configuration)」を参照してください。 `EventGridOutput` 属性の例を次に示します。

```java
public class Function {
    @FunctionName("EventGridTriggerTest")
    public void run(@EventGridTrigger(name = "event") String content,
            @EventGridOutput(name = "outputEvent", topicEndpointUri = "MyEventGridTopicUriSetting", topicKeySetting = "MyEventGridTopicKeySetting") OutputBinding<String> outputEvent, final ExecutionContext context) {
            ...
    }
}
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

属性は、JavaScript ではサポートされていません。

# <a name="powershell"></a>[PowerShell](#tab/powershell)

属性は、PowerShell ではサポートされていません。

# <a name="python"></a>[Python](#tab/python)

属性は、Python ではサポートされていません。

---

## <a name="configuration"></a>構成

次の表は、*function.json* ファイルと `EventGrid` 属性で設定したバインド構成のプロパティを説明しています。

|function.json のプロパティ | 属性のプロパティ |説明|
|---------|---------|----------------------|
|**type** | 該当なし | "eventGrid" に設定する必要があります。 |
|**direction** | 該当なし | "out" に設定する必要があります。 このパラメーターは、Azure Portal でバインドを作成するときに自動で設定されます。 |
|**name** | 該当なし | イベントを表す関数コードに使用される変数の名前。 |
|**topicEndpointUri** |**TopicEndpointUri** | カスタム トピックの URI を含むアプリ設定の名前 (例: `MyTopicEndpointUri`)。 |
|**topicKeySetting** |**TopicKeySetting** | カスタム トピックのアクセス キーを含むアプリ設定の名前。 |

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

> [!IMPORTANT]
> `TopicEndpointUri` 構成プロパティの値は、確実に、カスタム トピックの URI を含むアプリ設定の名前に設定してください。 このプロパティにカスタム トピックの URI を直接指定しないでください。

## <a name="usage"></a>使用法

# <a name="c"></a>[C#](#tab/csharp)

`out EventGridEvent paramName` などのメソッド パラメーターを使用してメッセージを送信します。 複数のメッセージを書き込むには、`out EventGridEvent` の代わりに `ICollector<EventGridEvent>` または `IAsyncCollector<EventGridEvent>` を使用します。

### <a name="additional-types"></a>その他の型 
3\.0.0 以降のバージョンの Event Grid 拡張機能を使用するアプリでは、[Azure.Messaging.EventGrid](/dotnet/api/azure.messaging.eventgrid.eventgridevent) 名前空間の `EventGridEvent` 型を使用します。 また、[Azure. Messaging](/dotnet/api/azure.messaging.cloudevent) 名前空間の `CloudEvent` 型にバインドすることもできます。

# <a name="c-script"></a>[C# スクリプト](#tab/csharp-script)

`out EventGridEvent paramName` などのメソッド パラメーターを使用してメッセージを送信します。 C# スクリプトでは、`paramName` は *function.json* の `name` プロパティで指定された値です。 複数のメッセージを書き込むには、`out EventGridEvent` の代わりに `ICollector<EventGridEvent>` または `IAsyncCollector<EventGridEvent>` を使用します。

### <a name="additional-types"></a>その他の型 
3\.0.0 以降のバージョンの Event Grid 拡張機能を使用するアプリでは、[Azure.Messaging.EventGrid](/dotnet/api/azure.messaging.eventgrid.eventgridevent) 名前空間の `EventGridEvent` 型を使用します。 また、[Azure. Messaging](/dotnet/api/azure.messaging.cloudevent) 名前空間の `CloudEvent` 型にバインドすることもできます。

# <a name="java"></a>[Java](#tab/java)

`out EventGridOutput paramName` などのメソッド パラメーターを呼び出して個々のメッセージを送信し、`ICollector<EventGridOutput>` を使用して複数のメッセージを書き込みます。

# <a name="javascript"></a>[JavaScript](#tab/javascript)

`context.bindings.<name>` を使用して出力イベントにアクセスします。`<name>` は *function.json* の `name` プロパティで指定された値です。

# <a name="powershell"></a>[PowerShell](#tab/powershell)

`Push-OutputBinding` コマンドレットを使用して出力イベントにアクセスし、Event Grid 出力バインディングにイベントを送信します。

# <a name="python"></a>[Python](#tab/python)

関数から Event Grid メッセージを出力するには、次の 2 つのオプションがあります。
- **戻り値**:*function.json* 内の `name` プロパティを `$return` に設定します。 この構成では、関数の戻り値は EventGrid メッセージとして永続化されます。
- **命令型**:[Out](/python/api/azure-functions/azure.functions.out) 型として宣言されたパラメーターの [set](/python/api/azure-functions/azure.functions.out#set-val--t-----none) メソッドに値を渡します。 `set` に渡された値は、EventGrid メッセージとして永続化されます。

---

## <a name="next-steps"></a>次のステップ

* [Event Grid イベントをディスパッチする](./functions-bindings-event-grid-trigger.md)
