---
title: "Durable Functions のシングルトン - Azure"
description: "Azure Functions の Durable Functons 拡張機能でシングルトンを使用する方法"
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
ms.openlocfilehash: e82cc53d53a6d0296aaab2c3a76ad4e2f6c12c54
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="singleton-orchestrators-in-durable-functions-azure-functions"></a>Durable Functions のシングルトン オーケストレーター (Azure Functions)

バックグラウンド ジョブやアクター スタイルのオーケストレーションでは、多くの場合、特定のオーケストレーターのインスタンスが 1 度に 1 つだけ実行されることを保証する必要があります。 これは、[Durable Functions](durable-functions-overview.md) でオーケストレーターを作成するときに、特定のインスタンス ID を割り当てることによって実現できます。

## <a name="singleton-example"></a>シングルトンの例

次の C# の例は、シングルトン バックグラウンド ジョブのオーケストレーションを作成する HTTP トリガー関数を示しています。 それは、既知のインスタンス ID を使用して、インスタンスが 1 つだけ存在することを保証しています。

```cs
[FunctionName("EnsureSingletonTrigger")]
public static async Task<HttpResponseMessage> Ensure(
    [HttpTrigger(AuthorizationLevel.Function, methods: "post")] HttpRequestMessage req,
    [OrchestrationClient] DurableOrchestrationClient starter,
    TraceWriter log)
{
    // Ensure only one instance is ever running at a time
    const string OrchestratorName = "MySingletonOrchestrator";
    const string InstanceId = "MySingletonInstanceId";

    var existingInstance = await starter.GetStatusAsync(InstanceId);
    if (existingInstance == null)
    {
        log.Info($"Creating singleton instance with ID = {InstanceId}...");
        await starter.StartNewAsync(OrchestratorName, InstanceId, input: null);
    }

    return starter.CreateCheckStatusResponse(req, InstanceId);
}
```

既定では、インスタンス ID は、ランダムに生成される GUID です。 ただし、ここでは、トリガー関数は、値 `MySingletonInstanceId` の定義済みの `InstanceId` 変数を使用して、オーケストレーター関数にインスタンス ID を事前に割り当てています。 これにより、トリガーは、[GetStatusAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html#Microsoft_Azure_WebJobs_DurableOrchestrationContext_GetStatusAsync_) を呼び出すことで、既知のインスタンスが既に実行中であるかどうかを確認できます。

オーケストレーター関数の 実装の詳細は、実際は問題ではありません。 開始して完了する通常のオーケストレーター関数でも、永久に実行される関数 (つまり、[永続的オーケストレーション](durable-functions-eternal-orchestrations.md)) でも可能です。 重要な点は、インスタンスが 1 度に 1 つだけ実行されていることです。

> [!NOTE]
> シングルトン オーケストレーション インスタンスが終了するか、失敗するか、完了した場合、同じ ID で再作成することはできません。 このような場合は、新しいインスタンス ID を使用して再作成するための準備を行う必要があります。

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [サブオーケストレーションを呼び出す方法を知る](durable-functions-sub-orchestrations.md)

> [!div class="nextstepaction"]
> [サンプル シングルトンを実行する](durable-functions-counter.md)
