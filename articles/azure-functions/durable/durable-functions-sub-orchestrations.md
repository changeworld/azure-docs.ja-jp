---
title: Durable Functions のサブオーケストレーション - Azure
description: Azure Functions の拡張機能である Durable Functions のオーケストレーションからオーケストレーションを呼び出す方法。
ms.topic: conceptual
ms.date: 11/03/2019
ms.author: azfuncdf
ms.openlocfilehash: 0f27b19b101c997e002f6cf60b7cab988e3cbed4
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/22/2021
ms.locfileid: "114457715"
---
# <a name="sub-orchestrations-in-durable-functions-azure-functions"></a>Durable Functions (Azure Functions) でのサブオーケストレーション

オーケストレーター関数はアクティビティ関数を呼び出すだけでなく、別のオーケストレーター関数を呼び出すことができます。 たとえば、小規模なオーケストレーター関数のライブラリから大規模なオーケストレーションを作成できます。 またはオーケストレーター関数の複数のインスタンスを並列で実行できます。

オーケストレーター関数は、.NET 内では `CallSubOrchestratorAsync` または `CallSubOrchestratorWithRetryAsync` メソッド、JavaScript 内では `callSubOrchestrator` または `callSubOrchestratorWithRetry` メソッド、Python 内では `call_sub_orchestrator` または `call_sub_orchestrator_with_retry` メソッドを使用して、別のオーケストレーター関数を呼び出すことができます。 [エラー処理と補正](durable-functions-error-handling.md#automatic-retry-on-failure)の記事で自動再試行の詳細について説明しています。

呼び出し元から見ると、サブオーケストレーター関数はアクティビティ関数と同じように動作します。 それらは値を返したり、例外をスローしたり、親のオーケストレーター関数によって待機させたりすることができます。 

> [!NOTE]
> サブオーケストレーションは現在、.NET、JavaScript、Python 内でサポートされています。

## <a name="example"></a>例

次の例では、プロビジョニングが必要な複数のデバイスがある IoT ("モノのインターネット") シナリオについて説明しています。 次の関数は、各デバイスに対して実行する必要があるプロビジョニング ワークフローを表します。

# <a name="c"></a>[C#](#tab/csharp)

```csharp
public static async Task DeviceProvisioningOrchestration(
    [OrchestrationTrigger] IDurableOrchestrationContext context)
{
    string deviceId = context.GetInput<string>();

    // Step 1: Create an installation package in blob storage and return a SAS URL.
    Uri sasUrl = await context.CallActivityAsync<Uri>("CreateInstallationPackage", deviceId);

    // Step 2: Notify the device that the installation package is ready.
    await context.CallActivityAsync("SendPackageUrlToDevice", Tuple.Create(deviceId, sasUrl));

    // Step 3: Wait for the device to acknowledge that it has downloaded the new package.
    await context.WaitForExternalEvent<bool>("DownloadCompletedAck");

    // Step 4: ...
}
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
const df = require("durable-functions");

module.exports = df.orchestrator(function*(context) {
    const deviceId = context.df.getInput();

    // Step 1: Create an installation package in blob storage and return a SAS URL.
    const sasUrl = yield context.df.callActivity("CreateInstallationPackage", deviceId);

    // Step 2: Notify the device that the installation package is ready.
    yield context.df.callActivity("SendPackageUrlToDevice", { id: deviceId, url: sasUrl });

    // Step 3: Wait for the device to acknowledge that it has downloaded the new package.
    yield context.df.waitForExternalEvent("DownloadCompletedAck");

    // Step 4: ...
});
```

# <a name="python"></a>[Python](#tab/python)

```python
import azure.functions as func
import azure.durable_functions as df

def orchestrator_function(context: df.DurableOrchestrationContext):
    device_id = context.get_input()

    # Step 1: Create an installation package in blob storage and return a SAS URL.
    sas_url = yield context.call_activity("CreateInstallationPackage", device_id)

    # Step 2: Notify the device that the installation package is ready.
    yield context.call_activity("SendPackageUrlToDevice", { "id": device_id, "url": sas_url })

    # Step 3: Wait for the device to acknowledge that it has downloaded the new package.
    yield context.call_activity("DownloadCompletedAck")

    # Step 4: ...
```

---

このオーケストレーター関数は、そのまま一回限りのデバイス プロビジョニングに使用するか、または大規模なオーケストレーションに組み込むことができます。 後者の場合、親のオーケストレーター関数は `CallSubOrchestratorAsync` (.NET)、`callSubOrchestrator` (JavaScript)、`call_sub_orchestrator` (Python) のいずれかの API を使用して、`DeviceProvisioningOrchestration` のインスタンスをスケジュールできます。

複数のオーケストレーター関数を並列で実行する方法を次に示します。

# <a name="c"></a>[C#](#tab/csharp)

```csharp
[FunctionName("ProvisionNewDevices")]
public static async Task ProvisionNewDevices(
    [OrchestrationTrigger] IDurableOrchestrationContext context)
{
    string[] deviceIds = await context.CallActivityAsync<string[]>("GetNewDeviceIds");

    // Run multiple device provisioning flows in parallel
    var provisioningTasks = new List<Task>();
    foreach (string deviceId in deviceIds)
    {
        Task provisionTask = context.CallSubOrchestratorAsync("DeviceProvisioningOrchestration", deviceId);
        provisioningTasks.Add(provisionTask);
    }

    await Task.WhenAll(provisioningTasks);

    // ...
}
```

> [!NOTE]
> 前述の C# の例は Durable Functions 2.x 用です。 Durable Functions 1.x の場合、`IDurableOrchestrationContext` の代わりに `DurableOrchestrationContext` を使用する必要があります。 バージョン間の相違点の詳細については、[Durable Functions のバージョン](durable-functions-versions.md)に関する記事を参照してください。

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
const df = require("durable-functions");

module.exports = df.orchestrator(function*(context) {
    const deviceIds = yield context.df.callActivity("GetNewDeviceIds");

    // Run multiple device provisioning flows in parallel
    const provisioningTasks = [];
    var id = 0;
    for (const deviceId of deviceIds) {
        const child_id = context.df.instanceId+`:${id}`;
        const provisionTask = context.df.callSubOrchestrator("DeviceProvisioningOrchestration", deviceId, child_id);
        provisioningTasks.push(provisionTask);
        id++;
    }

    yield context.df.Task.all(provisioningTasks);

    // ...
});
```


# <a name="python"></a>[Python](#tab/python)

```Python
import azure.functions as func
import azure.durable_functions as df

def orchestrator_function(context: df.DurableOrchestrationContext):

    device_IDs = yield context.call_activity("GetNewDeviceIds")

    # Run multiple device provisioning flows in parallel
    provisioning_tasks = []
    id_ = 0
    for device_id in device_IDs:
        child_id = f"{context.instance_id}:{id_}"
        provision_task = context.call_sub_orchestrator("DeviceProvisioningOrchestration", device_id, child_id)
        provisioning_tasks.append(provision_task)
        id_ += 1

    yield context.task_all(provisioning_tasks)

    # ...
```
---

> [!NOTE]
> 親オーケストレーションと同じ関数アプリでサブオーケストレーションを定義する必要があります。 別の関数アプリでオーケストレーションを呼び出して待機する必要がある場合は、HTTP API の組み込みサポートと HTTP 202 ポーリング コンシューマー パターンの使用を検討してください。 詳細については、「[HTTP 機能](durable-functions-http-features.md)」トピックを参照してください。

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [カスタム オーケストレーションの状態を設定する方法を学習します](durable-functions-custom-orchestration-status.md)
