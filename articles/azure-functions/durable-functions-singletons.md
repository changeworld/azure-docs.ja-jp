---
title: Durable Functions のシングルトン - Azure
description: Azure Functions の Durable Functons 拡張機能でシングルトンを使用する方法
services: functions
author: cgillum
manager: cfowler
editor: ''
tags: ''
keywords: ''
ms.service: functions
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 09/29/2017
ms.author: azfuncdf
ms.openlocfilehash: ea8b5db946d6b35ea4583d9170ec36e5f95e16cd
ms.sourcegitcommit: a36a1ae91968de3fd68ff2f0c1697effbb210ba8
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/17/2018
ms.locfileid: "29972553"
---
# <a name="singleton-orchestrators-in-durable-functions-azure-functions"></a>Durable Functions のシングルトン オーケストレーター (Azure Functions)

バックグラウンド ジョブやアクター スタイルのオーケストレーションでは、多くの場合、特定のオーケストレーターのインスタンスが 1 度に 1 つだけ実行されることを保証する必要があります。 これは、[Durable Functions](durable-functions-overview.md) でオーケストレーターを作成するときに、特定のインスタンス ID を割り当てることによって実現できます。

## <a name="singleton-example"></a>シングルトンの例

次の C# の例は、シングルトン バックグラウンド ジョブのオーケストレーションを作成する HTTP トリガー関数を示しています。 このコードは、指定されたインスタンス ID の インスタンスが 1 つだけ存在することを保証します。

```cs
[FunctionName("HttpStartSingle")]
public static async Task<HttpResponseMessage> RunSingle(
    [HttpTrigger(AuthorizationLevel.Function, methods: "post", Route = "orchestrators/{functionName}/{instanceId}")] HttpRequestMessage req,
    [OrchestrationClient] DurableOrchestrationClient starter,
    string functionName,
    string instanceId,
    TraceWriter log)
{
    // Check if an instance with the specified ID already exists.
    var existingInstance = await starter.GetStatusAsync(instanceId);
    if (existingInstance == null)
    {
        // An instance with the specified ID doesn't exist, create one.
        dynamic eventData = await req.Content.ReadAsAsync<object>();
        await starter.StartNewAsync(functionName, instanceId, eventData);
        log.Info($"Started orchestration with ID = '{instanceId}'.");
        return starter.CreateCheckStatusResponse(req, instanceId);
    }
    else
    {
        // An instance with the specified ID exists, don't create one.
        return req.CreateErrorResponse(
            HttpStatusCode.Conflict,
            $"An instance with ID '{instanceId}' already exists.");
    }
}
```

既定では、インスタンス ID は、ランダムに生成される GUID です。 ただし、ここでは、インスタンス ID は URL からルート データに渡されます。 コードは、[GetStatusAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html#Microsoft_Azure_WebJobs_DurableOrchestrationContext_GetStatusAsync_) を呼び出して、指定した ID を持つインスタンスが既に実行されているかどうかを確認します。 実行されていなければ、インスタンスがその ID で作成されます。

オーケストレーター関数の 実装の詳細は、実際は問題ではありません。 開始して完了する通常のオーケストレーター関数でも、永久に実行される関数 (つまり、[永続的オーケストレーション](durable-functions-eternal-orchestrations.md)) でも可能です。 重要な点は、インスタンスが 1 度に 1 つだけ実行されていることです。

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [サブオーケストレーションを呼び出す方法を知る](durable-functions-sub-orchestrations.md)
