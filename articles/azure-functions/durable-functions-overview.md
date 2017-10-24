---
title: "Durable Functions の概要 - Azure"
description: "Azure Functions の Durable Functions 拡張機能の概要です。"
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
ms.openlocfilehash: 1ba4d68ba93073ebe3516c4fe886c7845080c534
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="durable-functions-overview-azure-functions"></a>Durable Functions の概要 (Azure Functions)

*Durable Functions* は、サーバーレス環境でステートフル関数を記述できる、[Azure Functions](functions-overview.md) と [Azure WebJobs](../app-service/web-sites-create-web-jobs.md) の拡張機能です。 この拡張機能は状態、チェックポイント、再起動を管理します。

この拡張機能を使用すると、*オーケストレーター関数*という新しいタイプの関数で、ステートフルなワークフローを定義できます。 オーケストレーター関数のメリットは以下のとおりです。

* コードでワークフローを定義します。 JSON スキーマまたはデザイナーが不要です。
* 他の関数を同期と非同期のどちらでも呼び出すことができます。 呼び出された関数からの出力は、ローカル変数に保存できます。
* 関数が待機するたびに、自動で進行状況にチェックポイントを設定します。 プロセスがリサイクルされるか VM が再起動される場合に、ローカルの状態が失われません。

> [!NOTE]
> Durable Functions は Azure Functions の高度な拡張機能であり、すべてのアプリケーションに適しているわけではありません。 この記事は、開発者が [Azure Functions](functions-overview.md) のコンセプトを十分に理解しており、サーバーレス アプリケーションの開発に取り組んでいることを前提としています。

Durable Functions の主な用途は、サーバーレス アプリケーションにおける複雑でステートフルな調整の問題をシンプルにすることです。 次のセクションでは、Durable Functions を使用することでメリットがある、いくつかの典型的なアプリケーションのパターンを示します。

## <a name="pattern-1-function-chaining"></a>パターン #1: 関数チェーン

*関数チェーン*は、特定の順序で関数のシーケンスを実行するパターンです。 ある関数の出力が、別の関数の入力に適用される必要がある、ということがよくあります。

![関数チェーンの図](media/durable-functions-overview/function-chaining.png)

Durable Functions を使用すれば、このパターンをコードで簡潔に実装できます。

```cs
public static async Task<object> Run(DurableOrchestrationContext ctx)
{
    try
    {
        var x = await ctx.CallActivityAsync<object>("F1");
        var y = await ctx.CallActivityAsync<object>("F2", x);
        var z = await ctx.CallActivityAsync<object>("F3", y);
        return  await ctx.CallActivityAsync<object>("F4", z);
    }
    catch (Exception)
    {
        // error handling/compensation goes here
    }
}
```

"F1"、"F2"、"F3"、"F4" という値は、関数アプリ内の他の関数の名前です。 制御フローは、通常の命令型のコーディング構造で実装されます。 つまり、コードは上から下へ実行され、コードに条件文やループなどの既存言語の制御フロー セマンティクスを含めることができます。  エラー処理ロジックを try ブロック、catch ブロック、finally ブロックに含めることもできます。

`ctx` パラメーター ([DurableOrchestrationContext](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html)) は名前によって他の関数を呼び出し、パラメーターを渡して、関数の出力を返すメソッドを提供します。 コードが `await` を呼び出すたびに Durable Functions フレームワークは、現在の関数インスタンスの進行状況に*チェックポイントを設定*します。 プロセスまたは VM が実行途中でリサイクルされる場合、その関数インスタンスは先の `await` 呼び出しから再開されます。 この再起動動作については、後で詳しく説明します。

## <a name="pattern-2-fan-outfan-in"></a>パターン #2: ファンアウト/ファンイン

*ファンアウト/ファンイン*は、複数の関数を並列に実行してすべてが完了するまで待機するパターンです。  複数の関数から返された結果に基づいて集計作業が行われる場合があります。

![ファンアウト/ファンインの図](media/durable-functions-overview/fan-out-fan-in.png)

通常の関数では、関数が複数のメッセージを 1 つのキューに送信することでファンアウトが行われます。 しかし、ファンインして戻すことはこれよりずっと難しくなります。 キューによってトリガーされる関数が終了し、関数の出力が格納される時間を追跡するように、コードを記述する必要があります。 Durable Functions 拡張機能は、比較的単純なコードでこのパターンを処理します。

```cs
public static async Task Run(DurableOrchestrationContext ctx)
{
    var parallelTasks = new List<Task<int>>();
 
    // get a list of N work items to process in parallel
    object[] workBatch = await ctx.CallActivityAsync<object[]>("F1");
    for (int i = 0; i < workBatch.Length; i++)
    {
        Task<int> task = ctx.CallActivityAsync<int>("F2", workBatch[i]);
        parallelTasks.Add(task);
    }
 
    await Task.WhenAll(parallelTasks);
 
    // aggregate all N outputs and send result to F3
    int sum = parallelTasks.Sum(t => t.Result);
    await ctx.CallActivityAsync("F3", sum);
}
```

ファンアウト作業が `F2` 関数の複数のインスタンスに配分され、タスクの動的リストを使用してこの作業が追跡されます。 .NET `Task.WhenAll` API が呼び出され、すべての呼び出された関数が終了するまで待機します。 次に、`F2` 関数の出力が動的なタスク リストから集計されて、`F3` 関数に渡されます。

`Task.WhenAll` の `await` 呼び出しの際に自動でチェックポイントを設定することで、実行途中でクラッシュや再起動が生じても既に完了したすべてのタスクをやり直す必要がなくなります。

## <a name="pattern-3-async-http-apis"></a>パターン #3: 非同期 HTTP API

3 番目のパターンでもっとも重要なのは、外部クライアントとの間で行われる実行時間の長い操作の状態を調整するという問題です。 このパターンを実装する一般的な方法は、HTTP 呼び出しにより実行時間の長い操作をトリガーし、その操作が完了する時間を照会できる状態エンドポイントにクライアントをリダイレクトするという方法です。

![HTTP API の図](media/durable-functions-overview/async-http-api.png)

Durable Functions は、実行時間の長い関数を操作するコードをシンプルにするビルトイン API を提供します。 [サンプル](durable-functions-install.md)では、新しいオーケストレーター関数のインスタンスを開始するために使用できる、シンプルな REST コマンドについて説明しています。 インスタンスが開始されると、この拡張機能はオーケストレーター関数の状態をクエリする Webhook HTTP API を公開します。 次の例では、オーケストレーターを開始してその状態をクエリする REST コマンドを示します。 わかりやすくするため、この例では細部をいくらか省略しています。

```
> curl -X POST https://myfunc.azurewebsites.net/orchestrators/DoWork -H "Content-Length: 0" -i
HTTP/1.1 202 Accepted
Content-Type: application/json
Location: https://myfunc.azurewebsites.net/admin/extensions/DurableTaskExtension/b79baf67f717453ca9e86c5da21e03ec

{"id":"b79baf67f717453ca9e86c5da21e03ec", ...}

> curl https://myfunc.azurewebsites.net/admin/extensions/DurableTaskExtension/b79baf67f717453ca9e86c5da21e03ec -i
HTTP/1.1 202 Accepted
Content-Type: application/json
Location: https://myfunc.azurewebsites.net/admin/extensions/DurableTaskExtension/b79baf67f717453ca9e86c5da21e03ec

{"runtimeStatus":"Running","lastUpdatedTime":"2017-03-16T21:20:47Z", ...}

> curl https://myfunc.azurewebsites.net/admin/extensions/DurableTaskExtension/b79baf67f717453ca9e86c5da21e03ec -i
HTTP/1.1 200 OK
Content-Length: 175
Content-Type: application/json

{"runtimeStatus":"Completed","lastUpdatedTime":"2017-03-16T21:20:57Z", ...}
```

状態は Durable Functions ランタイムによって管理されるため、独自の状態追跡メカニズムを実装する必要はありません。

Durable Functions 拡張機能には実行時間の長いオーケストレーションを管理するためのビルトイン Webhook がありますが、独自の関数トリガー (HTTP、キュー、またはイベント ハブなど) と `orchestrationClient` バインドを使用してこのパターンを自分で実装できます。

```cs
// HTTP-triggered function to start a new orchestrator function instance.
public static async Task<HttpResponseMessage> Run(
    HttpRequestMessage req,
    DurableOrchestrationClient starter,
    string functionName,
    TraceWriter log)
{
    // Function name comes from the request URL.
    // Function input comes from the request content.
    dynamic eventData = await req.Content.ReadAsAsync<object>();
    string instanceId = await starter.StartNewAsync(functionName, eventData);
    
    log.Info($"Started orchestration with ID = '{instanceId}'.");
    
    return starter.CreateCheckStatusResponse(req, instanceId);
}
```

[DurableOrchestrationClient](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html) `starter`パラメーターは、Durable Functions 拡張機能の一部である `orchestrationClient` 出力バインドからの値です。 このパラメーターは、新しいまたは既存のオーケストレーター関数インスタンスの開始や終了、インスタンスへのイベント送信やクエリ送信のためのメソッドを提供します。 上の例では、HTTP によりトリガーされる関数は受信 URL からの `functionName` の値を受け取って、その値を [StartNewAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_StartNewAsync_) に渡しています。 次にこのバインド API は、`Location` ヘッダーとともに、開始されたインスタンスの状態を後で調べるため、またはインスタンスを終了するために使用するインスタンスの追加情報を含めた応答を返します。

## <a name="pattern-4-stateful-singletons"></a>パターン #4: ステートフル シングルトン

ほとんどの関数には明示的な開始と終了があり、直接外部のイベント ソースとやりとりをすることはありません。 しかし、オーケストレーションは[ステートフル シングルトン](durable-functions-singletons.md) パターンをサポートしており、このパターンは関数を分散コンピューティングにおける信頼できる[アクター](https://en.wikipedia.org/wiki/Actor_model)として振る舞わせることができます。

次の図は、外部ソースから受け取ったイベントの処理中に、無限ループを実行している関数を示しています。

![ステートフル シングルトンの図](media/durable-functions-overview/stateful-singleton.png)

Durable Functions はアクター モデルの実装ではありませんが、オーケストレーター関数には、そのランタイムと同じ特性が数多くあります。 たとえば、実行時間が長く (無限の場合もあります)、ステートフルで、信頼でき、シングル スレッドで、ロケーションが透過的で、グローバルにアドレス可能であるなどの特性です。 このことが、別のフレームワークを必要とせずに "アクター" のように振る舞うことができるオーケストレーター関数を有用なものにしています。

通常の関数はステートレスであるため、ステートフル シングルトン パターンの実装には不向きです。 しかし、Durable Functions 拡張機能を使用すれば、ステートフル シングルトン パターンを比較的簡単に実装できます。 次のコードは、カウンターを実装するシンプルなオーケストレーター関数です。

```cs
public static async Task Run(DurableOrchestrationContext ctx)
{
    int counterState = ctx.GetInput<int>();

    string operation = await ctx.WaitForExternalEvent<string>("operation");
    if (operation == "incr")
    {
        counterState++;
    }
    else if (operation == "decr")
    {
        counterState--;
    }

    ctx.ContinueAsNew(counterState);
}
```

このコードは "永続的オーケストレーション" と呼ばれることがあるものです。&mdash; つまり、開始しても終了することがないコードです。 これは次の手順を実行します。

* `counterState` の入力値で開始します。
* `operation` と呼ばれるメッセージを永久に待機します。
* いくつかのロジックを実行してそのローカルの状態を更新します。
* `ctx.ContinueAsNew` を呼び出すことで、それ自身を "再起動" します。
* もう一度、次の操作を永久に待機します。

## <a name="pattern-5-human-interaction"></a>パターン #5: 人による操作

ほとんどのプロセスには、何らかの人による操作が含まれます。 自動化されたプロセスに人による操作が含まれる場合に問題になるのが、人は必ずしもクラウド サービスのように可用性と応答性が高くないということです。 自動化されたプロセスはこのことを許容する必要があり、通常タイムアウトや補正ロジックを使用してそれを行います。

人による操作を含むビジネス プロセスの一例として、承認プロセスがあります。 たとえば、特定の額を超えた経費報告書について上司から承認を得る必要があるなどの場合です。 72 時間以内に上司が承認しない (上司が休暇に入ったなどの) 場合に、他の誰か (おそらくは上司の上司) から承認を得るためにエスカレーション プロセスに入ります。

![人による操作の図](media/durable-functions-overview/approval.png)

このパターンは、オーケストレーター関数を使用して実装できます。 オーケストレーターは、[永続タイマー](durable-functions-timers.md)を使用して承認を要求し、タイムアウトした場合にエスカレーションします。 また、人による何らかの操作によって生成された通知である[外部イベント](durable-functions-external-events.md)を待機します。

```cs
public static async Task Run(DurableOrchestrationContext ctx)
{
    await ctx.CallActivityAsync("RequestApproval");
    using (var timeoutCts = new CancellationTokenSource())
    {
        DateTime dueTime = ctx.CurrentUtcDateTime.AddHours(72);
        Task durableTimeout = ctx.CreateTimer(dueTime, timeoutCts.Token);

        Task<bool> approvalEvent = ctx.WaitForExternalEvent<bool>("ApprovalEvent");
        if (approvalEvent == await Task.WhenAny(approvalEvent, durableTimeout))
        {
            timeoutCts.Cancel();
            await ctx.CallActivityAsync("HandleApproval", approvalEvent.Result);
        }
        else
        {
            await ctx.CallActivityAsync("Escalate");
        }
    }
}
```

永続タイマーは `ctx.CreateTimer` を呼び出すことで作成されます。 通知は `ctx.WaitForExternalEvent` が受け取ります。 そして、エスカレーションする (最初にタイムアウトが発生する) か、承認を処理する (タイムアウト前に承認を得る) かを決定するために `Task.WhenAny` が呼び出されます。

## <a name="the-technology"></a>テクノロジ

背後では、永続的タスクのオーケストレーションを構築するための GitHub のオープン ソース ライブラリである [Durable Task Framework](https://github.com/Azure/durabletask) の上に、Durable Functions 拡張機能が構築されています。 Azure Functions が Azure WebJobs のサーバーレスな進化形であるのと同じように、Durable Functions は Durable Task Framework のサーバーレスな進化形です。 Durable Task Framework は、ミッション クリティカルなプロセスを自動化するために Microsoft 内外で頻繁に使用されています。 サーバーレスな Azure Functions 環境には自然に適合します。

### <a name="event-sourcing-checkpointing-and-replay"></a>イベント ソーシング、チェックポイント設定、リプレイ

オーケストレーター関数は、[Event Sourcing](https://docs.microsoft.com/azure/architecture/patterns/event-sourcing) として知られるクラウド デザイン パターンを使用して、この関数の実行状態を安定的に維持します。 オーケストレーションの *"現在の"* 状態を直接格納する代わりに、この永続的な拡張機能は追加専用のストアを使用して、関数オーケストレーションがとる *"アクションのすべて"* を記録します。 これには、ランタイムの完全な状態を "ダンプする" のと比べて、パフォーマンス、スケーラビリティ、応答時間の改善など多くのメリットがあります。 他にも、トランザクション データに最終的な一貫性を提供する、完全な監査証跡や監査履歴を維持するなどのメリットもあります。 監査証跡のみで、信頼性の高い補正アクションが可能です。

この拡張機能による Event Sourcing の使用は透過的です。 背後では、オーケストレーター関数内の `await` 演算子が、Durable Task Framework のディスパッチャーにオーケストレーター スレッドのコントロールを引き渡します。 次に、このディスパッチャーは、オーケストレーター関数がスケジュールした新しいアクション (1 つ以上の子関数を呼び出す、永続タイマーをスケジュールする、など) をストレージにコミットします。 この透過的なコミット アクションは、オーケストレーション インスタンスの *"実行履歴"* に追加されます。 この履歴は永続ストレージに格納されます。 次に、このコミット アクションはキューにメッセージを追加して実際の作業をスケジュールします。 この時点では、メモリからオーケストレーター関数をアンロードできます。 それに対する課金は Azure Functions の従量課金プランを使用している場合は停止します。  処理すべき作業がさらにある場合は、関数は再起動され、関数の状態は再構築されます。

オーケストレーション関数にさらに処理すべき作業が与えられる (たとえば、応答メッセージを受け取る、または永続タイマーが期限切れになる) と、ローカルの状態を再構築するためにオーケストレーターがもう一度起動して始めからその関数全体を再実行します。 このリプレイ中にコードが関数を呼び出そうとする (または他の非同期作業を行おうとする) と、Durable Task Framework は現在のオーケストレーションの*実行履歴*を照会します。 アクティビティ関数が既に実行されいくつかの結果を生成していることが確認されると、その関数の結果をリプレイしてオーケストレーター コードが実行を継続します。 この動作は、関数コードが完了する、または新しい非同期作業をスケジュールする時点まで継続して行われます。

### <a name="orchestrator-code-constraints"></a>オーケストレーター コードの制約

このリプレイ動作は、オーケストレーター関数に記述できるコード タイプに関する制約を設けます。 たとえば、オーケストレーター コードは複数回リプレイされて毎回同じ結果を生成する必要があるため、確定的である必要があります。 制約に関する完全なリストについては、**チェックポイントの設定と再起動**に関する記事の「[Orchestrator code constraints (オーケストレーター コードの制約)](durable-functions-checkpointing-and-replay.md#orchestrator-code-constraints)」セクションをご覧ください。

## <a name="language-support"></a>言語のサポート

現在 Durable Functions でサポートされている言語は C# のみです。 これには、オーケストレーター関数とアクティビティ関数が含まれます。 今後、Azure Functions でサポートされているすべての言語をサポートしていく予定です。 言語サポートの追加に関する最新の進捗状況については、Azure Functions の [GitHub リポジトリの Issue 一覧](https://github.com/Azure/azure-functions-durable-extension/issues)をご覧ください。

## <a name="monitoring-and-diagnostics"></a>監視と診断

Durable Functions 拡張機能は、Application Insights キーで関数アプリが構成されるときに、構造化された追跡データを [Application Insights](functions-monitoring.md) に自動で出力します。 この追跡データは、オーケストレーションの動作や進行状況を監視するために使用できます。

[Application Insights Analytics](https://docs.microsoft.com/azure/application-insights/app-insights-analytics) を使用すると Application Insights ポータル内で Durable Functions の追跡イベントがどのように表示されるか、その例を次に示します。

![App Insights のクエリ結果](media/durable-functions-overview/app-insights-1.png)

各ログ エントリの `customDimensions` フィールドには、有用な構造化データが数多く格納されています。 完全に展開された、そのようなエントリの例を次に示します。

![App Insights クエリの customDimensions フィールド](media/durable-functions-overview/app-insights-2.png)

Durable Task Framework ディスパッチャーのリプレイ動作のおかげで、リプレイされたアクションの豊富なログ エントリを確認できます。 これはコア エンジンのリプレイ動作を理解するのに役立ちます。 [診断](durable-functions-diagnostics.md)に関する記事では、リプレイ ログをフィルターで除外したサンプル クエリを示しているため、"リアルタイム" ログを確認できます。

## <a name="storage-and-scalability"></a>ストレージとスケーラビリティ

Durable Functions 拡張機能は、Azure Storage の Queue、Table、BLOB を使用して実行履歴の状態を保持し、関数の実行をトリガーします。 関数アプリの既定のストレージ アカウントを使用、または別のストレージ アカウントを構成できます。 ストレージのスループット制限が原因で、別のアカウントを使用したいと思うこともあるでしょう。 記述するオーケストレーター コードは、これらのストレージ アカウント内のエンティティとやりとりをする必要はありません (というより、すべきではありません)。 エンティティは実装の詳細として、Durable Task Framework により直接管理されます。

オーケストレーター関数はアクティビティ関数をスケジュールし、内部キュー メッセージを介して応答を受け取ります。 Azure Functions の従量課金プランで関数アプリを実行するときは、これらのキューは [Azure Functions Scale Controller](functions-scale.md#how-the-consumption-plan-works) により監視され、必要に応じてコンピューティング インスタンスが追加されます。 オーケストレーター関数は、複数の VM にスケールアウトされるときに、呼び出すアクティビティ関数を複数の異なる VM で実行しつつ、1 つの VM 上で実行される場合があります。 Durable Functions のスケール動作の詳細については、[パフォーマンスとスケール](durable-functions-perf-and-scale.md)に関する記事をご覧ください。

Table Storage は、オーケストレーター アカウントの実行履歴を格納するために使用されます。 特定の VM 上でインスタンスが復元される場合は常に、そのローカルの状態を再構築できるようにするために、テーブルから実行履歴がフェッチされます。 Table Storage 内で履歴を使用できるようにすることの便利な点の 1 つは、[Microsoft Azure Storage Explorer](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer) などのツールを使用してオーケストレーションの履歴を確認できることです。

![Azure Storage Explorer のスクリーン ショット](media/durable-functions-overview/storage-explorer.png)

> [!WARNING]
> Table Storage 内で実行履歴を確認できるのは簡単で便利ですが、このテーブルに依存関係を設定することは避けてください。 これについては、Durable Functions 拡張機能が今後改善されていくうちに変更される可能性があります。

## <a name="known-issues-and-faq"></a>既知の問題とよくあるご質問

通常、すべての既知の問題は、[GitHub の Issue](https://github.com/Azure/azure-functions-durable-extension/issues) 一覧で追跡されています。 問題が発生してその問題が GitHub で見つからない場合は、新しい Issue を開き、その問題の詳細な説明を記載してお知らせください。 質問のみの場合でも、お気軽に GitHub の Issue を開き、質問のタグを付けてご連絡ください。

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [続けて Durable Functions のドキュメントを読む](durable-functions-bindings.md)

> [!div class="nextstepaction"]
> [Durable Functions 拡張機能のインストールとサンプル](durable-functions-install.md)

