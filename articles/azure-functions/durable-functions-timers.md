---
title: "Durable Functions のタイマー - Azure"
description: "Azure Functions の Durable Functions 拡張機能で持続的タイマーを実装する方法について説明します。"
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
ms.openlocfilehash: 27312846caf4f51e708b48655578846a52cbd1c3
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="timers-in-durable-functions-azure-functions"></a>Durable Functions のタイマー (Azure Functions)

[Durable Functions](durable-functions-overview.md) には、遅延を実装したり、非同期アクションでタイムアウトを設定したりできるように、オーケストレーター関数で使用する "*持続的タイマー*" が用意されています。 持続的タイマーは、`Thread.Sleep` または `Task.Delay` の代わりに、オーケストレーター関数で使用されます。

持続的タイマーを作成するには、[CreateTimer](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html#Microsoft_Azure_WebJobs_DurableOrchestrationContext_CreateTimer_) を呼び出します。 メソッドは、指定した日時に再開するタスクを返します。

## <a name="timer-limitations"></a>タイマーの制限事項

午後 4 時 30 分に期限切れになるタイマーを作成すると、基になる Durable Task Framework は、午後 4 時 30分にのみ表示されるメッセージをエンキューします。 Azure Functions 従量課金プランで実行されている場合、新しく表示されるタイマー メッセージにより、適切な VM で関数アプリが確実にアクティブになります。

> [!WARNING]
> * Azure Storage の制限のため、持続的タイマーは 7 日を超えて使用することができません。 マイクロソフトでは、[タイマー延長 (7 日超) に対する機能要求](https://github.com/Azure/azure-functions-durable-extension/issues/14)に取り組んでいます。
> * 持続的タイマーの相対的な期限を計算するときは、以下の例に示すように、`DateTime.UtcNow` ではなく、必ず [CurrentUtcDateTime](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html#Microsoft_Azure_WebJobs_DurableOrchestrationContext_CurrentUtcDateTime) を使用します。

## <a name="usage-for-delay"></a>遅延の使用

次の例は、実行を遅らせる持続的タイマーを使用する方法を示しています。 この例では、10 日間、請求の通知を毎日発行します。

```csharp
[FunctionName("BillingIssuer")]
public static async Task Run(
    [OrchestrationTrigger] DurableOrchestrationContext context)
{
    for (int i = 0; i < 10; i++)
    {
        DateTime deadline = context.CurrentUtcDateTime.Add(TimeSpan.FromDays(1));
        await context.CreateTimer(deadline, CancellationToken.None);
        await context.CallFunctionAsync("SendBillingEvent");
    }
}
```

> [!WARNING]
> オーケストレーター関数の無限ループが発生しなようにしてください。 無限ループ シナリオを安全かつ効率的に実装する方法については、[永続的オーケストレーション](durable-functions-eternal-orchestrations.md)に関するページをご覧ください。 

## <a name="usage-for-timeout"></a>タイムアウトの使用

この例は、持続的タイマーを使用して、タイムアウトを実装する方法を示しています。

```csharp
[FunctionName("TryGetQuote")]
public static async Task<bool> Run(
    [OrchestrationTrigger] DurableOrchestrationContext context)
{
    TimeSpan timeout = TimeSpan.FromSeconds(30);
    DateTime deadline = context.CurrentUtcDateTime.Add(timeout);

    using (var cts = new CancellationTokenSource())
    {
        Task activityTask = context.CallFunctionAsync("GetQuote");
        Task timeoutTask = context.CreateTimer(deadline, cts.Token);

        Task winner = await Task.WhenAny(activityTask, timeoutTask);
        if (winner == activityTask)
        {
            // success case
            cts.Cancel();
            return true;
        }
        else
        {
            // timeout case
            return false;
        }
    }
}
```

> [!WARNING]
> コードがタイマーの完了を待たない場合は、`CancellationTokenSource` を使用して、持続的タイマーをキャンセルします。 Durable Task Framework では、未処理のタスクすべてが完了するかキャンセルされるまで、オーケストレーションの状態は "完了" に変更されません。

このメカニズムは、実際には、進行中のアクティビティ関数の実行を終了するのではなく、 オーケストレーター関数が、単に結果を無視して、次に進めるようにするだけです。 関数アプリが従量課金プランを使用している場合は、アクティビティ関数を破棄しても、その関数によって消費される時間とメモリはすべて課金対象となります。 既定では、従量課金プランで実行されている関数のタイムアウトは、5 分に設定されています。 この制限を超えると、Azure Functions ホストは、すべての実行を停止し、ランナウェイ課金状況を回避するために、リサイクルされます。 [関数のタイムアウトは構成可能](functions-host-json.md#functiontimeout)です。

オーケストレーター関数でタイムアウトを実装する方法の詳細な例については、[人による操作とタイムアウト - 電話検証](durable-functions-phone-verification.md)のチュートリアルをご覧ください。

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [外部イベントを発生させて処理する方法を確認する](durable-functions-external-events.md)

