---
title: Durable Functions でのインスタンスの管理 - Azure
description: Azure Functions の Durable Functions 拡張機能でインスタンスを管理する方法を説明します。
services: functions
author: cgillum
manager: jeconnoc
keywords: ''
ms.service: azure-functions
ms.devlang: multiple
ms.topic: conceptual
ms.date: 08/31/2018
ms.author: azfuncdf
ms.openlocfilehash: c9b3cd112cef7a34e0d475cdeb85b9e07d77f584
ms.sourcegitcommit: 8e06d67ea248340a83341f920881092fd2a4163c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/16/2018
ms.locfileid: "49352595"
---
# <a name="manage-instances-in-durable-functions-azure-functions"></a>Durable Functions でのインスタンスの管理 (Azure Functions)

[Durable Functions](durable-functions-overview.md) オーケストレーション インスタンスは、開始、終了、照会したり、通知イベントを送信したりできます。 インスタンス管理はすべて、[オーケストレーション クライアント バインド](durable-functions-bindings.md)を使用して行います。 この記事では、各インスタンスの管理操作について詳しく説明します。

## <a name="starting-instances"></a>インスタンスの開始

[DurableOrchestrationClient](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html) の [StartNewAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_StartNewAsync_) メソッドは、オーケストレーター関数の新しいインスタンスを開始します。 このクラスのインスタンスを取得するには、`orchestrationClient` バインドを使用します。 内部的には、このメソッドは、メッセージをコントロール キューにエンキューし、これにより `orchestrationTrigger` トリガー バインドを使用する、指定された名前の関数がトリガーされます。 

タスクが完了するのは、オーケストレーション プロセスが開始するときです。 オーケストレーション プロセスは30 秒以内に開始する必要があります。 これよりも長くかかると `TimeoutException` がスローされます。 

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

.NET 以外の言語では、関数の出力バインドを使用して、新しいインスタンスを開始することもできます。 この場合、上記の 3 つのパラメーターをフィールドとして持つ、JSON でシリアル化できる任意のオブジェクトを使用できます。 たとえば、次の JavaScript 関数を考えてみましょう。

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
上記のコードでは、function.json ファイル内で、名前が "starter" で種類が "orchestrationClient" の出力バインドを定義したと仮定しています。 バインドが定義されていない場合、永続関数のインスタンスは作成されません。

永続関数を呼び出すには、次に説明するように、オーケストレーション クライアントに対するバインドを持つように function.json を変更する必要があります

```js
{
    "bindings": [{
        "name":"starter",
        "type":"orchestrationClient",
        "direction":"out"
    }]
}
```

> [!NOTE]
> インスタンス ID にはランダムな識別子を使用することをお勧めします。 これにより、複数の VM にわたってオーケストレーター関数をスケールするとき、負荷が均等に分散されるようになります。 ランダムではないインスタンスの ID は、外部ソースから ID を取得するとき、または[シングルトン オーケストレーター](durable-functions-singletons.md) パターンを実装するときに使用します。

## <a name="querying-instances"></a>インスタンスの照会

[DurableOrchestrationClient](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html) クラスの [GetStatusAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_GetStatusAsync_) メソッドは、オーケストレーション インスタンスの状態を照会します。 これは、パラメーターとして `instanceId` (必須)、`showHistory` (省略可能)、`showHistoryOutput` (省略可能) を使用します。 `showHistory` を `true` に設定すると、応答に実行履歴が含まれます。 `showHistoryOutput` も `true` に設定すると、実行履歴にアクティビティ出力が含まれます。 このメソッドは次のプロパティを持つオブジェクトを返します。

* **Name**: オーケストレーター関数の名前。
* **InstanceId**: オーケストレーションのインスタンス ID (`instanceId` 入力と同じにする必要があります)。
* **CreatedTime**: オーケストレーター関数の実行が開始された時刻。
* **LastUpdatedTime**: オーケストレーションが最後にチェックポイントされた時刻。
* **Input**: JSON 値としての関数の入力。
* **CustomStatus**: JSON 形式でのカスタム オーケストレーションの状態。 
* **Output**: JSON 値として関数の出力 (関数が完了している場合)。 オーケストレーター関数が失敗した場合、このプロパティには、エラーの詳細が含まれます。 オーケストレーター関数が終了した場合、このプロパティには、提供されている終了の理由が含まれます (存在する場合)。
* **RuntimeStatus**: 次のいずれかの値を指定できます。
    * **Pending**: インスタンスはスケジュールされていますが、まだ実行が開始されていません。
    * **Running**: インスタンスの実行が開始されました。
    * **Completed**: インスタンスが正常に完了しました。
    * **ContinuedAsNew**: インスタンスが新しい履歴で再起動されました。 これは遷移状態です。
    * **Failed**: インスタンスが失敗し、エラーが発生しました。
    * **Terminated**: インスタンスが突然終了しました。
* **History**: オーケストレーションの実行履歴。 このフィールドにデータが設定されるのは、`showHistory` を `true` に設定した場合のみです。
    
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
## <a name="querying-all-instances"></a>すべてのインスタンスのクエリを実行する

`GetStatusAsync` メソッドを使用して、すべてのオーケストレーション インスタンスの状態のクエリを実行することができます。 このメソッドはパラメーターを受け取りません。キャンセルする場合は、`CancellationToken` オブジェクトを渡すことができます。 このメソッドは、`GetStatusAsync` メソッドと同じプロパティを持つオブジェクトをパラメーターと共に返します。ただし、履歴は返しません。 

```csharp
[FunctionName("GetAllStatus")]
public static async Task Run(
    [HttpTrigger(AuthorizationLevel.Anonymous, "get", "post")]HttpRequestMessage req,
    [OrchestrationClient] DurableOrchestrationClient client,
    TraceWriter log)
{
    IList<DurableOrchestrationStatus> instances = await starter.GetStatusAsync(); // You can pass CancellationToken as a parameter.
    foreach (var instance in instances)
    {
        log.Info(JsonConvert.SerializeObject(instance));
    };
}
```
## <a name="querying-instances-with-filters"></a>フィルターを使用したインスタンスのクエリ

`GetStatusAsync` メソッドを使用して、定義済みのフィルター セットに一致するオーケストレーション インスタンスの一覧を取得することもできます。 使用可能なフィルター オプションには、オーケストレーションの作成時刻と、オーケストレーションのランタイム状態が含まれます。

```csharp
[FunctionName("QueryStatus")]
public static async Task Run(
    [HttpTrigger(AuthorizationLevel.Anonymous, "get", "post")]HttpRequestMessage req,
    [OrchestrationClient] DurableOrchestrationClient client,
    TraceWriter log)
{
    IEnumerable<OrchestrationRuntimeStatus> runtimeStatus = new List<OrchestrationRuntimeStatus> {
        OrchestrationRuntimeStatus.Completed,
        OrchestrationRuntimeStatus.Running
    };
    IList<DurableOrchestrationStatus> instances = await starter.GetStatusAsync(
        new DateTime(2018, 3, 10, 10, 1, 0),
        new DateTime(2018, 3, 10, 10, 23, 59),
        runtimeStatus
    ); // You can pass CancellationToken as a parameter.
    foreach (var instance in instances)
    {
        log.Info(JsonConvert.SerializeObject(instance));
    };
}
```

## <a name="terminating-instances"></a>インスタンスの終了

実行中のオーケストレーション インスタンスを終了するには、[DurableOrchestrationClient](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html) クラスの [TerminateAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_TerminateAsync_) メソッドを使用します。 2 つのパラメーター `instanceId` と `reason` 文字列は、ログとインスタンス状態に書き込まれます。 終了インスタンスは、次の `await` ポイントに到達するとすぐに実行が停止されます。また、すでに `await` にある場合は、直ちに終了します。 

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
> インスタンスの終了は、現在、伝達されません。 アクティビティ関数およびサブオーケストレーションは、これらを呼び出したオーケストレーション インスタンスが終了しているかどうかに関係なく、完了するまで実行されます。

## <a name="sending-events-to-instances"></a>インスタンスへのイベントの送信

実行中のインスタンスにイベント通知を送信するには、[DurableOrchestrationClient](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html) クラスの [RaiseEventAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_RaiseEventAsync_) メソッドを使用します。 こうしたイベントを処理できるのは、[WaitForExternalEvent](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html#Microsoft_Azure_WebJobs_DurableOrchestrationContext_WaitForExternalEvent_) への呼び出しを待っているインスタンスです。 

[RaiseEventAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_RaiseEventAsync_) のパラメーターは次のとおりです。

* **InstanceId**: インスタンスの一意の ID。
* **EventName**: 送信するイベントの名前。
* **EventData**: インスタンスに送信する JSON でシリアル化できるペイロード。

```csharp
[FunctionName("RaiseEvent")]
public static Task Run(
    [OrchestrationClient] DurableOrchestrationClient client,
    [ManualTrigger] string instanceId)
{
    int[] eventData = new int[] { 1, 2, 3 };
    return client.RaiseEventAsync(instanceId, "MyEvent", eventData);
}
```

> [!WARNING]
> 指定した "*インスタンス ID*" のオーケストレーション インスタンスが存在しない場合、または指定した "*イベント名*" でインスタンスが待機していない場合、イベント メッセージは破棄されます。 この動作の詳細については、[GitHub の問題](https://github.com/Azure/azure-functions-durable-extension/issues/29)に関するトピックをご覧ください。

## <a name="wait-for-orchestration-completion"></a>オーケストレーションの完了の待機

[DurableOrchestrationClient](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html) クラスは、オーケストレーション インスタンスから実際の出力を同期して取得するために使用できる [WaitForCompletionOrCreateCheckStatusResponseAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_WaitForCompletionOrCreateCheckStatusResponseAsync_) API を公開します。 このメソッドでは、`timeout` に 10 秒および `retryInterval` に 1 秒の既定値を使用します (設定されていない場合)。  

この API の使用方法を示す HTTP トリガー関数の例を次に示します。

[!code-csharp[Main](~/samples-durable-functions/samples/precompiled/HttpSyncStart.cs)]

この関数は、次のように 2 秒のタイムアウトと 0.5 秒の再試行間隔を使用して呼び出すことができます。

```bash
    http POST http://localhost:7071/orchestrators/E1_HelloSequence/wait?timeout=2&retryInterval=0.5
```

オーケストレーション インスタンスからの応答を取得するために必要な時間に応じて 2 つのケースがあります。

1. オーケストレーション インスタンスが、定義されたタイムアウト (このケースでは 2 秒) 内に完了すると、応答は、同期して提供される実際のオーケストレーション インスタンス出力です。

    ```http
        HTTP/1.1 200 OK
        Content-Type: application/json; charset=utf-8
        Date: Thu, 14 Dec 2017 06:14:29 GMT
        Server: Microsoft-HTTPAPI/2.0
        Transfer-Encoding: chunked

        [
            "Hello Tokyo!",
            "Hello Seattle!",
            "Hello London!"
        ]
    ```

2. オーケストレーション インスタンスが、定義されたタイムアウト (このケースでは 2 秒) 内に完了できない場合、応答は「**HTTP API URL の検出**」で説明されている既定のものになります。

    ```http
        HTTP/1.1 202 Accepted
        Content-Type: application/json; charset=utf-8
        Date: Thu, 14 Dec 2017 06:13:51 GMT
        Location: http://localhost:7071/admin/extensions/DurableTaskExtension/instances/d3b72dddefce4e758d92f4d411567177?taskHub={taskHub}&connection={connection}&code={systemKey}
        Retry-After: 10
        Server: Microsoft-HTTPAPI/2.0
        Transfer-Encoding: chunked

        {
            "id": "d3b72dddefce4e758d92f4d411567177",
            "sendEventPostUri": "http://localhost:7071/admin/extensions/DurableTaskExtension/instances/d3b72dddefce4e758d92f4d411567177/raiseEvent/{eventName}?taskHub={taskHub}&connection={connection}&code={systemKey}",
            "statusQueryGetUri": "http://localhost:7071/admin/extensions/DurableTaskExtension/instances/d3b72dddefce4e758d92f4d411567177?taskHub={taskHub}&connection={connection}&code={systemKey}",
            "terminatePostUri": "http://localhost:7071/admin/extensions/DurableTaskExtension/instances/d3b72dddefce4e758d92f4d411567177/terminate?reason={text}&taskHub={taskHub}&connection={connection}&code={systemKey}",
            "rewindPostUri": "https://localhost:7071/admin/extensions/DurableTaskExtension/instances/d3b72dddefce4e758d92f4d411567177/rewind?reason={text}&taskHub={taskHub}&connection={connection}&code={systemKey}"
        }
    ```

> [!NOTE]
> webhook URL の形式は、実行している Azure Functions ホストのバージョンによって異なる場合があります。 上記の例は、Azure Functions 2.0 ホスト用の形式です。

## <a name="retrieving-http-management-webhook-urls"></a>HTTP 管理 webhook URL の取得

外部システムは、[HTTP API URL の検出](durable-functions-http-api.md)に関するページで説明されている既定の応答の一部である webhook URL を介して、Durable Functions と通信できます。 ただし、webhook URL には、オーケストレーション クライアント、またはアクティビティ関数で [DurableOrchestrationClient](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html) クラスの [CreateHttpManagementPayload](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_CreateHttpManagementPayload_) メソッドを使用して、プログラムでアクセスすることもできます。 

[CreateHttpManagementPayload](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_CreateHttpManagementPayload_) には 1 つのパラメーターがあります。

* **instanceId**: インスタンスの一意の ID。

このメソッドは、[HttpManagementPayload](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.Extensions.DurableTask.HttpManagementPayload.html#Microsoft_Azure_WebJobs_Extensions_DurableTask_HttpManagementPayload_) のインスタンスと次の文字列プロパティを返します。

* **Id**: オーケストレーションのインスタンス ID (`InstanceId` 入力と同じにする必要があります)。
* **StatusQueryGetUri**: オーケストレーション インスタンスの状態の URL。
* **SendEventPostUri**: オーケストレーション インスタンスの "イベント発生" URL。
* **TerminatePostUri**: オーケストレーション インスタンスの "終了" URL。
* **RewindPostUri**: オーケストレーション インスタンスの "巻き戻し" URL。

アクティビティ関数は、[HttpManagementPayload](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.Extensions.DurableTask.HttpManagementPayload.html#Microsoft_Azure_WebJobs_Extensions_DurableTask_HttpManagementPayload_) のインスタンスを外部システムに送信して、イベントを監視したり、オーケストレーションに対するイベントを発生させたりすることができます。

```csharp
[FunctionName("SendInstanceInfo")]
public static void SendInstanceInfo(
    [ActivityTrigger] DurableActivityContext ctx,
    [OrchestrationClient] DurableOrchestrationClient client,
    [DocumentDB(
        databaseName: "MonitorDB",
        collectionName: "HttpManagementPayloads",
        ConnectionStringSetting = "CosmosDBConnection")]out dynamic document)
{
    HttpManagementPayload payload = client.CreateHttpManagementPayload(ctx.InstanceId);

    // send the payload to Cosmos DB
    document = new { Payload = payload, id = ctx.InstanceId };
}
```

## <a name="rewinding-instances-preview"></a>インスタンスの巻き戻し (プレビュー)

[RewindAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_RewindAsync_System_String_System_String_) API を使用して、失敗したオーケストレーション インスタンスを以前の正常な状態に "*巻き戻す*" ことができます。 これは、オーケストレーションを "*実行中*" 状態に戻し、オーケストレーション エラーの原因となったアクティビティやサブオーケストレーションの実行失敗を再実行することで機能します。

> [!NOTE]
> この API は、適切なエラー処理や再試行ポリシーの代わりとなるものではありません。 予期しない理由でオーケストレーション インスタンスが失敗する場合にのみ使用するものです。 エラー処理と再試行ポリシーについて詳しくは、[エラー処理](durable-functions-error-handling.md)に関するトピックをご覧ください。

"*巻き戻し*" のユース ケースの一例として、一連の[人による承認](durable-functions-overview.md#pattern-5-human-interaction)があります。 承認が必要であることをユーザーに通知し、リアルタイムの応答を待機する一連のアクティビティ関数があるとします。 すべての承認アクティビティが応答を受信するかタイムアウトになった後、アプリケーションの構成ミス (無効なデータベース接続文字列など) により別のアクティビティが失敗します。 結果として、ワークフローの深い部分でオーケストレーションが失敗します。 `RewindAsync` API を使用すると、アプリケーション管理者は、構成エラーを修正し、失敗したオーケストレーションを問題が発生する直前の状態に "*巻き戻し*" ます。 人間の対話手順はいずれも再承認が不要で、オーケストレーションは正常に完了できるようになります。

> [!NOTE]
> "*巻き戻し*" 機能では、永続タイマーを使用したオーケストレーション インスタンスの巻き戻しはサポートされません。

```csharp
[FunctionName("RewindInstance")]
public static Task Run(
    [OrchestrationClient] DurableOrchestrationClient client,
    [ManualTrigger] string instanceId)
{
    string reason = "Orchestrator failed and needs to be revived.";
    return client.RewindAsync(instanceId, reason);
}
```

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [インスタンス管理に HTTP API を使用する方法を確認する](durable-functions-http-api.md)
