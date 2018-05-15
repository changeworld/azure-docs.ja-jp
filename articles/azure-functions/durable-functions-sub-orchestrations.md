---
title: Durable Functions のサブオーケストレーション - Azure
description: Azure Functions の拡張機能である Durable Functions のオーケストレーションからオーケストレーションを呼び出す方法。
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
ms.openlocfilehash: 7545a371749ed9af88f08af23cce3a513f494374
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/07/2018
---
# <a name="sub-orchestrations-in-durable-functions-azure-functions"></a>Durable Functions (Azure Functions) でのサブオーケストレーション

オーケストレーター関数はアクティビティ関数を呼び出すだけでなく、別のオーケストレーター関数を呼び出すことができます。 たとえば、オーケストレーター関数のライブラリから大規模なオーケストレーションを作成できます。 またはオーケストレーター関数の複数のインスタンスを並列で実行できます。

オーケストレーター関数は、[CallSubOrchestratorAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html#Microsoft_Azure_WebJobs_DurableOrchestrationContext_CallSubOrchestratorAsync_) メソッドまたは [CallSubOrchestratorWithRetryAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html#Microsoft_Azure_WebJobs_DurableOrchestrationContext_CallSubOrchestratorWithRetryAsync_) メソッドを呼び出して別のオーケストレーター関数を呼び出すことができます。 [エラー処理と補正](durable-functions-error-handling.md#automatic-retry-on-failure)の記事で自動再試行の詳細について説明しています。

呼び出し元から見ると、サブオーケストレーター関数はアクティビティ関数と同じように動作します。 それらは値を返したり、例外をスローしたり、親のオーケストレーター関数によって待機させたりすることができます。

> [!NOTE]
> `CallSubOrchestratorAsync` メソッドと `CallSubOrchestratorWithRetryAsync` メソッドは、JavaScript ではまだ使用できません。

## <a name="example"></a>例

次の例では、プロビジョニングが必要な複数のデバイスがある IoT ("モノのインターネット") シナリオについて説明しています。 それぞれのデバイスに対して必要となる特定のオーケストレーションがあり、それは次のようになる場合があります。

```csharp
public static async Task DeviceProvisioningOrchestration(
    [OrchestrationTrigger] DurableOrchestrationContext ctx)
{
    string deviceId = ctx.GetInput<string>();

    // Step 1: Create an installation package in blob storage and return a SAS URL.
    Uri sasUrl = await ctx.CallActivityAsync<Uri>("CreateInstallationPackage", deviceId);

    // Step 2: Notify the device that the installation package is ready.
    await ctx.CallActivityAsync("SendPackageUrlToDevice", Tuple.Create(deviceId, sasUrl));

    // Step 3: Wait for the device to acknowledge that it has downloaded the new package.
    await ctx.WaitForExternalEvent<bool>("DownloadCompletedAck");

    // Step 4: ...
}
```

このオーケストレーター関数は、そのまま一回限りのデバイス プロビジョニングに使用するか、または大規模なオーケストレーションに組み込むことができます。 後者の場合、親のオーケストレーター関数は `CallSubOrchestratorAsync` API を使用して `DeviceProvisioningOrchestration` のインスタンスをスケジュールできます。

複数のオーケストレーター関数を並列で実行する方法を次に示します。

```csharp
[FunctionName("ProvisionNewDevices")]
public static async Task ProvisionNewDevices(
    [OrchestrationTrigger] DurableOrchestrationContext ctx)
{
    string[] deviceIds = await ctx.CallActivityAsync<string[]>("GetNewDeviceIds");

    // Run multiple device provisioning flows in parallel
    var provisioningTasks = new List<Task>();
    foreach (string deviceId in deviceIds)
    {
        Task provisionTask = ctx.CallSubOrchestratorAsync("DeviceProvisioningOrchestration", deviceId);
        provisioningTasks.Add(provisionTask);
    }

    await Task.WhenAll(provisioningTasks);

    // ...
}
```

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [タスク ハブとその構成方法について学習する](durable-functions-task-hubs.md)
