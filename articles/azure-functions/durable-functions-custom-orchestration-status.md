---
title: Durable Functions でのカスタムオーケストレーションの状態 - Azure
description: Durable Functions 用のカスタム オーケストレーションの状態を構成し使用する方法を説明します。
services: functions
author: kadimitr
manager: cfowler
editor: ''
tags: ''
keywords: ''
ms.service: functions
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 04/24/2018
ms.author: azfuncdf
ms.openlocfilehash: 840b96b9cfdb28ca1b17f54698677f4d491342c8
ms.sourcegitcommit: 6e43006c88d5e1b9461e65a73b8888340077e8a2
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/01/2018
---
# <a name="custom-orchestration-status-in-durable-functions-azure-functions"></a>Durable Functions でのカスタムオーケストレーションの状態 (Azure Functions)

カスタムオーケストレーションの状態を使用すると、オーケストレーター関数のカスタム状態値を設定できます。 この状態は、HTTP GetStatus API または `DurableOrchestrationClient.GetStatusAsync` API を通してのみ提供されます。

## <a name="sample-use-cases"></a>サンプル ユース ケース 

### <a name="visualize-progress"></a>進行状況を視覚化

クライアントは、状態の終了点をポーリングし、進行状況の現在の実行のステージを視覚化する UI を表示できます。 次の例では、進行状況の共有を示しています:

```csharp
[FunctionName("E1_HelloSequence")]
public static async Task<List<string>> Run(
  [OrchestrationTrigger] DurableOrchestrationContextBase context)
{
  var outputs = new List<string>();

  outputs.Add(await context.CallActivityAsync<string>("E1_SayHello", "Tokyo"));
  context.SetCustomStatus("Tokyo");
  outputs.Add(await context.CallActivityAsync<string>("E1_SayHello", "Seattle"));
  context.SetCustomStatus("Seattle");
  outputs.Add(await context.CallActivityAsync<string>("E1_SayHello", "London"));
  context.SetCustomStatus("London");

  // returns ["Hello Tokyo!", "Hello Seattle!", "Hello London!"]
  return outputs;
}

[FunctionName("E1_SayHello")]
public static string SayHello([ActivityTrigger] string name)
{
  return $"Hello {name}!";
}
```

`CustomStatus`フィールドが"London"に設定されている時のみ、クライアントは、オーケストレーションの出力を受け取ります:

```csharp
[FunctionName("HttpStart")]
public static async Task<HttpResponseMessage> Run(
  [HttpTrigger(AuthorizationLevel.Function, methods: "post", Route = "orchestrators/{functionName}")] HttpRequestMessage req,
  [OrchestrationClient] DurableOrchestrationClientBase starter,
  string functionName,
  ILogger log)
{
    // Function input comes from the request content.
    dynamic eventData = await req.Content.ReadAsAsync<object>();
    string instanceId = await starter.StartNewAsync(functionName, eventData);

    log.LogInformation($"Started orchestration with ID = '{instanceId}'.");

    DurableOrchestrationStatus durableOrchestrationStatus = await starter.GetStatusAsync(instanceId);
    while (durableOrchestrationStatus.CustomStatus.ToString() != "London")
    {
      await Task.Delay(200);
      durableOrchestrationStatus = await starter.GetStatusAsync(instanceId);
    }

    HttpResponseMessage httpResponseMessage = new HttpResponseMessage(HttpStatusCode.OK)
    {
      Content = new StringContent(JsonConvert.SerializeObject(durableOrchestrationStatus))
    };

    return httpResponseMessage;
  }
}
```

### <a name="output-customization"></a>出力のカスタマイズ 

もう 1 つの興味深いシナリオは、固有の特性や相互作用に基づいてカスタマイズされた出力を返すことによってユーザーをセグメント化することです。 カスタムオーケストレーションの状態のヘルプで、クライアント側のコードはジェネラルのままになります。 すべての主な変更は、次の例に示すように、サーバー側で行われます:

```csharp
[FunctionName("CityRecommender")]
public static void Run(
  [OrchestrationTrigger] DurableOrchestrationContextBase context)
{
  int userChoice = context.GetInput<int>();

  switch (userChoice)
  {
    case 1:
    context.SetCustomStatus(new
    {
      recommendedCities = new[] {"Tokyo", "Seattle"},
      recommendedSeasons = new[] {"Spring", "Summer"}
     });
      break;
    case 2:
      context.SetCustomStatus(new
      {
        recommendedCity = new[] {"Seattle, London"},
        recommendedSeasons = new[] {"Summer"}
      });
        break;
      case 3:
      context.SetCustomStatus(new
      {
        recommendedCity = new[] {"Tokyo, London"},
        recommendedSeasons = new[] {"Spring", "Summer"}
      });
        break;
  }

  // Wait for user selection and refine the recommendation
} 
```

### <a name="instruction-specification"></a>手順の仕様 

オーケストレーターは、カスタムの状態を使用してクライアントに固有の指示を提供できます。 カスタム状態の手順は、オーケストレーション コードでの手順にマップされます:

```csharp
[FunctionName("ReserveTicket")]
public static async Task<bool> Run(
  [OrchestrationTrigger] DurableOrchestrationContextBase context)
{
  string userId = context.GetInput<string>();

  int discount = await context.CallActivityAsync<int>("CalculateDiscount", userId);

  context.SetCustomStatus(new
  {
    discount = discount,
    discountTimeout = 60,
    bookingUrl = "https://www.myawesomebookingweb.com",
  });

  bool isBookingConfirmed = await context.WaitForExternalEvent<bool>("BookingConfirmed");

  context.SetCustomStatus(isBookingConfirmed
    ? new {message = "Thank you for confirming your booking."}
    : new {message = "The booking was not confirmed on time. Please try again."});

  return isBookingConfirmed;
}
```

## <a name="sample"></a>サンプル

次のサンプルでは、カスタムの状態が先に設定します:

```csharp
public static async Task SetStatusTest([OrchestrationTrigger] DurableOrchestrationContext ctx)
{
    // ...do work...

    // update the status of the orchestration with some arbitrary data
    var customStatus = new { nextActions = new [] {"A", "B", "C"}, foo = 2, };
    ctx.SetCustomStatus(customStatus);

    // ...do more work...
}
```

オーケストレーションの実行中に、外部クライアントはこのカスタム状態を取り込むことができます:

```http
GET /admin/extensions/DurableTaskExtension/instances/instance123

```

クライアントは次の応答を取得します: 

```http
{
  "runtimeStatus": "Running",
  "input": null,
  "customStatus": { "nextActions": ["A", "B", "C"], "foo": 2 },
  "output": null,
  "createdTime": "2017-10-06T18:30:24Z",
  "lastUpdatedTime": "2017-10-06T19:40:30Z"
}
```

> [!WARNING]
>  カスタム状態ペイロードは、Azure Table Storage の列に収まる必要があるため、16 KB の UTF-16 JSON テキストに制限されています。 これより大きなペイロードが必要な場合は、外部ストレージを使用できます。


## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [Azure のDurable Functions での HTTP API](durable-functions-http-api.md)


