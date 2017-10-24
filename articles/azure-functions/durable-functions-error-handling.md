---
title: "Durable Functions のエラー処理 - Azure"
description: "Azure Functions の Durable Functions 拡張機能で発生したエラーを処理する方法について説明します。"
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
ms.openlocfilehash: ee5362d33bb9dadadb4194457cfd7726f4825f56
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="handling-errors-in-durable-functions-azure-functions"></a>Durable Functions のエラー処理 (Azure Functions)

Durable Function のオーケストレーションはコードで実装され、プログラミング言語のエラー処理機能を使用できます。 この点を考慮すると、エラー処理と補正をオーケストレーションに組み込むときに知っておく必要がある新しい概念は実際にはありません。 ただし、注意する必要があるいくつかの動作があります。

## <a name="errors-in-activity-functions"></a>アクティビティ関数のエラー

アクティビティ関数でスローされた例外はオーケストレーター関数に戻され、`TaskFailedException` としてスローされます。 ニーズに合ったエラー処理と補正コードをオーケストレーター関数内に記述できます。

たとえば、ある口座の資金を別の口座に送金する次のオーケストレーター関数を検討します。

```csharp
#r "Microsoft.Azure.WebJobs.Extensions.DurableTask"

public static async Task Run(DurableOrchestrationContext context)
{
    var transferDetails = ctx.GetInput<TransferOperation>();

    await context.CallActivityAsync("DebitAccount",
        new
        { 
            Account = transferDetails.SourceAccount,
            Amount = transferDetails.Amount
        });

    try
    {
        await context.CallActivityAsync("CreditAccount",         
            new
            { 
                Account = transferDetails.DestinationAccount,
                Amount = transferDetails.Amount
            });
    }
    catch (Exception)
    {
        // Refund the source account.
        // Another try/catch could be used here based on the needs of the application.
        await context.CallActivityAsync("CreditAccount",         
            new
            { 
                Account = transferDetails.SourceAccount,
                Amount = transferDetails.Amount
            });
    }
}
```

送金先口座に対する **CreditAccount** 関数の呼び出しが失敗した場合、オーケストレーター関数は、資金を送金元口座に戻すことで、これを補正します。

## <a name="automatic-retry-on-failure"></a>エラー発生時の自動再試行

アクティビティ関数またはサブオーケストレーション関数を呼び出すときに、自動再試行ポリシーを指定できます。 次の例では、関数の呼び出しを最大 3 回試行し、次の再試行まで 5 秒間待機します。

```csharp
public static async Task Run(DurableOrchestrationContext context)
{
    var retryOptions = new RetryOptions(
        firstRetryInterval: TimeSpan.FromSeconds(5),
        maxNumberOfAttempts: 3);

    await ctx.CallActivityWithRetryAsync("FlakyFunction", retryOptions);
    
    // ...
}
```

`CallActivityWithRetryAsync` API は、`RetryOptions` パラメーターを使用します。 `CallSubOrchestratorWithRetryAsync` API を使用するサブオーケストレーション呼び出しは、同じ再試行ポリシーを使用できます。

自動再試行ポリシーをカスタマイズするためのいくつかのオプションがあります。 次のオプションが含まれます。

* **最大試行回数**: 再試行が行われる最大回数。
* **1 回目の再試行の間隔**: 1 回目の再試行を行う前の待機時間。
* **バックオフ係数**: バックオフの増加率を決定するために使用される係数。 既定値は 1 です。
* **最大再試行間隔**: 次の再試行を実行する前に待機する最大時間。
* **再試行タイムアウト**: 再試行を実行するために費やす最大時間。 既定の動作は、無限の再試行です。
* **カスタム**: 関数呼び出しを再試行するかどうかを決定するユーザー定義のコールバックを指定できます。

## <a name="function-timeouts"></a>関数のタイムアウト

完了に時間がかかりすぎる場合は、オーケストレーター関数内の関数呼び出しを破棄できます。 現時点でこれを行う適切な方法は、次の例に示すように、`context.CreateTimer` と `Task.WhenAny` を使用して[永続タイマー](durable-functions-timers.md)を作成することです。

```csharp
public static async Task<bool> Run(DurableOrchestrationContext context)
{
    TimeSpan timeout = TimeSpan.FromSeconds(30);
    DateTime deadline = context.CurrentUtcDateTime.Add(timeout);

    using (var cts = new CancellationTokenSource())
    {
        Task activityTask = context.CallActivityAsync("FlakyFunction");
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

## <a name="unhandled-exceptions"></a>ハンドルされない例外

オーケストレーター関数がハンドルされない例外で失敗した場合、例外の詳細がログに記録され、インスタンスは `Failed` 状態で完了します。

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [問題を診断する方法を知る](durable-functions-diagnostics.md)
