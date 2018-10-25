---
title: Durable Functions での外部イベントの処理 - Azure
description: Azure Functions の Durable Functions 拡張機能で外部イベントを処理する方法について説明します。
services: functions
author: kashimiz
manager: jeconnoc
keywords: ''
ms.service: azure-functions
ms.devlang: multiple
ms.topic: conceptual
ms.date: 10/23/2018
ms.author: azfuncdf
ms.openlocfilehash: 98380cc5b9daff314283ac4e45e5edf7b5601e1b
ms.sourcegitcommit: c2c279cb2cbc0bc268b38fbd900f1bac2fd0e88f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/24/2018
ms.locfileid: "49983948"
---
# <a name="handling-external-events-in-durable-functions-azure-functions"></a>Durable Functions での外部イベントの処理 (Azure Functions)

オーケストレーター関数には、外部イベントを待ち受ける機能があります。 [Durable Functions](durable-functions-overview.md) のこの機能は、多くの場合、人による操作または他の外部トリガーを処理するときに便利です。

## <a name="wait-for-events"></a>イベントを待つ

[WaitForExternalEvent](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html#Microsoft_Azure_WebJobs_DurableOrchestrationContext_WaitForExternalEvent_) メソッドを使用すると、オーケストレーター関数が、外部イベントを非同期的に待ち受けることができます。 リスニング オーケストレーター関数は、受信するイベントの "*名前*" と "*データのシェイプ*" を宣言します。

#### <a name="c"></a>C#

```csharp
[FunctionName("BudgetApproval")]
public static async Task Run(
    [OrchestrationTrigger] DurableOrchestrationContext context)
{
    bool approved = await context.WaitForExternalEvent<bool>("Approval");
    if (approved)
    {
        // approval granted - do the approved action
    }
    else
    {
        // approval denied - send a notification
    }
}
```

#### <a name="javascript-functions-v2-only"></a>JavaScript (Functions v2 のみ)

```javascript
const df = require("durable-functions");

module.exports = df.orchestrator(function*(context) {
    const approved = yield context.df.waitForExternalEvent("Approval");
    if (approved) {
        // approval granted - do the approved action
    } else {
        // approval denied - send a notification
    }
});
```

前の例では、特定の 1 つのイベントをリッスンし、受信したときにアクションを実行します。

次の例に示すように、複数のイベントを同時にリッスンできます。この例では、発生する可能性がある 3 つのイベント通知のいずれかを待っています。

#### <a name="c"></a>C#

```csharp
[FunctionName("Select")]
public static async Task Run(
    [OrchestrationTrigger] DurableOrchestrationContext context)
{
    var event1 = context.WaitForExternalEvent<float>("Event1");
    var event2 = context.WaitForExternalEvent<bool>("Event2");
    var event3 = context.WaitForExternalEvent<int>("Event3");

    var winner = await Task.WhenAny(event1, event2, event3);
    if (winner == event1)
    {
        // ...
    }
    else if (winner == event2)
    {
        // ...
    }
    else if (winner == event3)
    {
        // ...
    }
}
```

#### <a name="javascript-functions-v2-only"></a>JavaScript (Functions v2 のみ)

```javascript
const df = require("durable-functions");

module.exports = df.orchestrator(function*(context) {
    const event1 = context.df.waitForExternalEvent("Event1");
    const event2 = context.df.waitForExternalEvent("Event2");
    const event3 = context.df.waitForExternalEvent("Event3");

    const winner = yield context.df.Task.any([event1, event2, event3]);
    if (winner === event1) {
        // ...
    } else if (winner === event2) {
        // ...
    } else if (winner === event3) {
        // ...
    }
});
```

前の例では、複数のイベントの "*いずれか*" をリッスンします。 "*すべて*" のイベントを待つこともできます。

#### <a name="c"></a>C#

```csharp
[FunctionName("NewBuildingPermit")]
public static async Task Run(
    [OrchestrationTrigger] DurableOrchestrationContext context)
{
    string applicationId = context.GetInput<string>();

    var gate1 = context.WaitForExternalEvent("CityPlanningApproval");
    var gate2 = context.WaitForExternalEvent("FireDeptApproval");
    var gate3 = context.WaitForExternalEvent("BuildingDeptApproval");

    // all three departments must grant approval before a permit can be issued
    await Task.WhenAll(gate1, gate2, gate3);

    await context.CallActivityAsync("IssueBuildingPermit", applicationId);
}
```

#### <a name="javascript-functions-v2-only"></a>JavaScript (Functions v2 のみ)

```javascript
const df = require("durable-functions");

module.exports = df.orchestrator(function*(context) {
    const applicationId = context.df.getInput();

    const gate1 = context.df.waitForExternalEvent("CityPlanningApproval");
    const gate2 = context.df.waitForExternalEvent("FireDeptApproval");
    const gate3 = context.df.waitForExternalEvent("BuildingDeptApproval");

    // all three departments must grant approval before a permit can be issued
    yield context.df.Task.all([gate1, gate2, gate3]);

    yield context.df.callActivity("IssueBuildingPermit", applicationId);
});
```

[WaitForExternalEvent](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html#Microsoft_Azure_WebJobs_DurableOrchestrationContext_WaitForExternalEvent_) は、ある入力を無期限に待ちます。  関数アプリは、待機中に安全にアンロードすることができます。 これは、このオーケストレーション インスタンスのイベントが到着すると、自動的に起動され、すぐにそのイベントを処理します。

> [!NOTE]
> 関数アプリで従量課金プランを使用する場合、オーケストレーター関数が `WaitForExternalEvent` からのタスクを待っている間は、待ち時間がどんなに長くなっても課金されません。

.NET では、イベント ペイロードを想定された型 `T` に変換できない場合、例外がスローされます。

## <a name="send-events"></a>送信イベント

[DurableOrchestrationClient](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html) クラスの [RaiseEventAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_RaiseEventAsync_) メソッドは、`WaitForExternalEvent` が待っているイベントを送信します。  `RaiseEventAsync` メソッドでは、*eventName* と *eventData* がパラメーターとして使用されます。 イベント データは、JSON でシリアル化できる必要があります。

次の例は、キューによってトリガーされる関数で、"承認" イベントをオーケストレーター関数インスタンスに送信します。 オーケストレーション インスタンス ID は、キュー メッセージの本文から取得されます。

```csharp
[FunctionName("ApprovalQueueProcessor")]
public static async Task Run(
    [QueueTrigger("approval-queue")] string instanceId,
    [OrchestrationClient] DurableOrchestrationClient client)
{
    await client.RaiseEventAsync(instanceId, "Approval", true);
}
```

#### <a name="javascript-functions-v2-only"></a>JavaScript (Functions v2 のみ)
JavaScript では、Durable Function が待機しているイベントをトリガーするには REST API を呼び出す必要があります。
次のコードでは、"request" パッケージを使用しています。 下のメソッドを使用すると、任意の Durable Function インスタンスに対して任意のイベントを生成できます。

```js
function raiseEvent(instanceId, eventName) {
        var url = `<<BASE_URL>>/runtime/webhooks/durabletask/instances/${instanceId}/raiseEvent/${eventName}?taskHub=DurableFunctionsHub`;
        var body = <<BODY>>
            
        return new Promise((resolve, reject) => {
            request({
                url,
                json: body,
                method: "POST"
            }, (e, response) => {
                if (e) {
                    return reject(e);
                }

                resolve();
            })
        });
    }
```

<<BASE_URL>> は、関数アプリのベース URL になります。 コードをローカルで実行している場合は、 http://localhost:7071 や、Azure では https://<<functionappname>>.azurewebsites.net のようになります。


内部的には、`RaiseEventAsync` は、待機オーケストレーター関数によって取得されるメッセージをエンキューします。

> [!WARNING]
> 指定した "*インスタンス ID*" のオーケストレーション インスタンスが存在しない場合、または指定した "*イベント名*" でインスタンスが待機していない場合、イベント メッセージは破棄されます。 この動作の詳細については、[GitHub の問題](https://github.com/Azure/azure-functions-durable-extension/issues/29)に関するトピックをご覧ください。

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [永続的オーケストレーションを設定する方法を確認する](durable-functions-eternal-orchestrations.md)

> [!div class="nextstepaction"]
> [外部イベントを待つサンプルを実行する](durable-functions-phone-verification.md)

> [!div class="nextstepaction"]
> [人による操作を待つサンプルを実行する](durable-functions-phone-verification.md)

