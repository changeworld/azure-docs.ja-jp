---
title: "Durable Functions でのインスタンスの管理 - Azure"
description: "Azure Functions の Durable Functions 拡張機能でインスタンスを管理する方法を説明します。"
services: functions
author: cgillum
manager: cfowler
editor: 
tags: 
keywords: 
ms.service: functions
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 09/29/2017
ms.author: azfuncdf
ms.openlocfilehash: a938e5949896ad3bfa91903106d56ccdf827c725
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/21/2018
---
# <a name="manage-instances-in-durable-functions-azure-functions"></a>Durable Functions でのインスタンスの管理 (Azure Functions)

[Durable Functions](durable-functions-overview.md) オーケストレーション インスタンスは、開始、終了、照会したり、通知イベントを送信したりできます。 インスタンス管理はすべて、[オーケストレーション クライアント バインド](durable-functions-bindings.md)を使用して行います。 この記事では、各インスタンスの管理操作について詳しく説明します。

## <a name="starting-instances"></a>インスタンスの開始

[DurableOrchestrationClient](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html) の [StartNewAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_StartNewAsync_) メソッドは、オーケストレーター関数の新しいインスタンスを開始します。 このクラスのインスタンスを取得するには、`orchestrationClient` バインドを使用します。 内部的には、このメソッドは、メッセージをコントロール キューにエンキューし、これにより `orchestrationTrigger` トリガー バインドを使用する、指定された名前の関数がトリガーされます。

[StartNewAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_StartNewAsync_) のパラメーターは次のとおりです。

* **Name**: スケジュールを設定するオーケストレーター関数の名前。
* **Input**: オーケストレーター関数に入力として渡す必要がある、JSON でシリアル化できるデータ。
* **InstanceId**: (省略可能) インスタンスの一意の ID。 指定しない場合は、ランダムなインスタンス ID が生成されます。

単純な C# の例を次に示します。

```csharp
[FunctionName("HelloWorldManualStart")]
public static async Task Run(
    [ManualTrigger] string input,
    [OrchestrationClient] DurableOrchestrationClient starter,
    TraceWriter log)
{
    string instanceId = await starter.StartNewAsync("HelloWorld", input);
    log.Info($"Started orchestration with ID = '{instanceId}'.");
}
```

.NET 以外の言語では、関数の出力バインドを使用して、新しいインスタンスを開始することもできます。 この場合、上記の 3 つのパラメーターをフィールドとして持つ、JSON でシリアル化できる任意のオブジェクトを使用できます。 たとえば、次の Node.js 関数を考えてみましょう。

```js
module.exports = function (context, input) {
    var id = generateSomeUniqueId();
    context.bindings.starter = [{
        FunctionName: "HelloWorld",
        Input: input,
        InstanceId: id
    }];

    context.done(null);
};
```

> [!NOTE]
> インスタンス ID にはランダムな識別子を使用することをお勧めします。 これにより、複数の VM にわたってオーケストレーター関数をスケールするとき、負荷が均等に分散されるようになります。 ランダムではないインスタンスの ID は、外部ソースから ID を取得するとき、または[シングルトン オーケストレーター](durable-functions-singletons.md) パターンを実装するときに使用します。

## <a name="querying-instances"></a>インスタンスの照会

[DurableOrchestrationClient](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html) クラスの [GetStatusAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_GetStatusAsync_) メソッドは、オーケストレーション インスタンスの状態を照会します。 このメソッドは `instanceId` をパラメーターとして使用し、次のプロパティを持つオブジェクトを返します。

* **Name**: オーケストレーター関数の名前。
* **InstanceId**: オーケストレーションのインスタンス ID (`instanceId` 入力と同じにする必要があります)。
* **CreatedTime**: オーケストレーター関数の実行が開始された時刻。
* **LastUpdatedTime**: オーケストレーションが最後にチェックポイントされた時刻。
* **Input**: JSON 値としての関数の入力。
* **Output**: JSON 値として関数の出力 (関数が完了している場合)。 オーケストレーター関数が失敗した場合、このプロパティには、エラーの詳細が含まれます。 オーケストレーター関数が終了した場合、このプロパティには、提供されている終了の理由が含まれます (存在する場合)。
* **RuntimeStatus**: 次のいずれかの値を指定できます。
    * **Running**: インスタンスの実行が開始されました。
    * **Completed**: インスタンスが正常に完了しました。
    * **ContinuedAsNew**: インスタンスが新しい履歴で再起動されました。 これは遷移状態です。
    * **Failed**: インスタンスが失敗し、エラーが発生しました。
    * **Terminated**: インスタンスが突然終了しました。
    
インスタンスが存在しないか、まだ開始されていない場合、このメソッドは `null` を返します。

```csharp
[FunctionName("GetStatus")]
public static async Task Run(
    [OrchestrationClient] DurableOrchestrationClient client,
    [ManualTrigger] string instanceId)
{
    var status = await client.GetStatusAsync(instanceId);
    // do something based on the current status.
}
```

> [!NOTE]
> インスタンスのクエリは、現在、C# オーケストレーター関数でのみサポートされます。

## <a name="terminating-instances"></a>インスタンスの終了

実行中のインスタンスを終了するには、[DurableOrchestrationClient](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html) クラスの [TerminateAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_TerminateAsync_) メソッドを使用します。 2 つのパラメーター `instanceId` と `reason` 文字列は、ログとインスタンス状態に書き込まれます。 終了インスタンスは、次の `await` ポイントに到達するとすぐに実行が停止されます。また、すでに `await` にある場合は、直ちに終了します。

```csharp
[FunctionName("TerminateInstance")]
public static Task Run(
    [OrchestrationClient] DurableOrchestrationClient client,
    [ManualTrigger] string instanceId)
{
    string reason = "It was time to be done.";
    return client.TerminateAsync(instanceId, reason);
}
```

> [!NOTE]
> インスタンスの終了は、現在、C# オーケストレーター関数でのみサポートされます。

## <a name="sending-events-to-instances"></a>インスタンスへのイベントの送信

実行中のインスタンスにイベント通知を送信するには、[DurableOrchestrationClient](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html) クラスの [RaiseEventAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_RaiseEventAsync_) メソッドを使用します。 こうしたイベントを処理できるのは、[WaitForExternalEvent](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html#Microsoft_Azure_WebJobs_DurableOrchestrationContext_WaitForExternalEvent_) への呼び出しを待っているインスタンスです。 

[RaiseEventAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_RaiseEventAsync_) のパラメーターは次のとおりです。

* **InstanceId**: インスタンスの一意の ID。
* **EventName**: 送信するイベントの名前。
* **EventData**: インスタンスに送信する JSON でシリアル化できるペイロード。

```csharp
#r "Microsoft.Azure.WebJobs.Extensions.DurableTask"

[FunctionName("RaiseEvent")]
public static Task Run(
    [OrchestrationClient] DurableOrchestrationClient client,
    [ManualTrigger] string instanceId)
{
    int[] eventData = new int[] { 1, 2, 3 };
    return client.RaiseEventAsync(instanceId, "MyEvent", eventData);
}
```

> [!NOTE]
> イベントの生成は、現在、C# オーケストレーター関数でのみサポートされます。

> [!WARNING]
> 指定した "*インスタンス ID*" のオーケストレーション インスタンスが存在しない場合、または指定した "*イベント名*" でインスタンスが待機していない場合、イベント メッセージは破棄されます。 この動作の詳細については、[GitHub の問題](https://github.com/Azure/azure-functions-durable-extension/issues/29)に関するトピックをご覧ください。

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [インスタンス管理に HTTP API を使用する方法を確認する](durable-functions-http-api.md)
