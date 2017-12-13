---
title: "Durable Functions のステートフル シングルトン - Azure"
description: "Azure Functions の Durable Functions 拡張機能でステートフル シングルトンを実装する方法を説明します。"
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
ms.openlocfilehash: 05099e868e62f612be0a3354eb8b339507ac7e4a
ms.sourcegitcommit: a48e503fce6d51c7915dd23b4de14a91dd0337d8
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/05/2017
---
# <a name="stateful-singletons-in-durable-functions---counter-sample"></a>Durable Functions のステートフル シングルトン - カウンター サンプル

ステートフル シングルトンは、長時間実行される (永久に実行される可能性がある) オーケストレーター関数であり、状態を保存でき、他の関数による呼び出しとクエリが可能な関数です。 ステートフル シングルトンは、分散コンピューティングにおける[アクター モデル](https://en.wikipedia.org/wiki/Actor_model)に似ています。

オーケストレーター関数は、厳密には "アクター" の実装ではありませんが、同じランタイム特性を多数備えています。 たとえば、オーケストレーター関数はステートフルであり、信頼でき、シングル スレッドであり、ロケーションが透過的であり、グローバルにアドレス可能です。 これらは本物のアクターの実装を特に便利にしている特性ですが、オーケストレーター関数では別のフレームワークは必要ありません。

この記事では、"*カウンター*" サンプルを実行する方法を示します。 このサンプルは、"*インクリメント*" 操作と "*デクリメント*" 操作をサポートし、それに合わせて内部状態を更新するシングルトン オブジェクトを示します。

## <a name="prerequisites"></a>前提条件

* [Durable Functions のインストール](durable-functions-install.md)に関するページの指示に従って、サンプルを設定します。
* この記事では、[Hello シーケンス](durable-functions-sequence.md) サンプルのチュートリアルを既に終了していることを前提としています。

## <a name="scenario-overview"></a>シナリオの概要

カウンター シナリオは、通常のステートレス関数を使用して実装するのは驚くほど困難です。 ここで発生する大きな問題の 1 つは、**並行性**です。 "*インクリメント*" や "*デクリメント*" などの操作はアトミックである必要があり、そうでなければ、操作が相互に上書きを行う競合状態が発生する可能性があります。

単一の VM を使用したカウンター データのホストは 1 つの選択肢ですが、これは高価であり、単一の VM は定期的にリブートされることがあるため、**信頼性**の管理が問題になる可能性があります。 並列性を管理するために BLOB リースなどの同期ツール付きの分散プラットフォームを使用することもできますが、この方法では、**複雑さが**大幅に高まります。

Durable Functions は、この種のシナリオの実装をありふれたものにします。その理由は、オーケストレーション インスタンスは単一の VM に密接に関連付けられ、オーケストレーター関数の実行は常にシングルスレッドで行われるためです。 それだけでなく、オーケストレーター関数は長時間実行され、ステートフルで、外部イベントに反応することもできます。 次のサンプル コードは、長時間実行されるオーケストレーター関数としてそのようなカウンターを実装する方法を示します。

## <a name="the-sample-function"></a>サンプル関数

この記事では、サンプル アプリの **E3_Counter** について説明します。



## <a name="the-counter-orchestration"></a>カウンターのオーケストレーション

以降のセクションでは、Visual Studio Code および Azure Portal 開発で使用されるコードについて説明します。

### <a name="c-script"></a>C# スクリプト

function.json ファイル:

[!code-json[Main](~/samples-durable-functions/samples/csx/E3_Counter/function.json)]

run.csx ファイル:

[!code-csharp[Main](~/samples-durable-functions/samples/csx/E3_Counter/run.csx)]

### <a name="precompiled-c"></a>プリコンパイル済み C# 

以降のセクションでは、Visual Studio 開発で使用されるコードについて説明します。

オーケストレーター関数を実装するコードを次に示します。

[!code-csharp[Main](~/samples-durable-functions/samples/precompiled/Counter.cs)]

### <a name="explanation-of-the-code"></a>コードの説明

このオーケストレーター関数は、基本的に次の操作を行います。

1. *operation* という名前の外部イベントを [WaitForExternalEvent](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html#Microsoft_Azure_WebJobs_DurableOrchestrationContext_WaitForExternalEvent_) を使用してリッスンします。
2. 要求された操作に応じて、`counterState` ローカル変数を増減します。
3. [ContinueAsNew](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html#Microsoft_Azure_WebJobs_DurableOrchestrationContext_ContinueAsNew_) メソッドを使用してオーケストレーターを再開します。その際、`counterState` の最新の値を新しい入力として設定します。
4. 無限に実行を続けるか、*end* メッセージが受信されるまで実行を続けます。

これは、"*永続的オーケストレーション*" の例です。つまり、このオーケストレーションは永久に終わらない可能性があります。 このオーケストレーションは、[RaiseEventAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_RaiseEventAsync_) メソッドによって送信されたメッセージに応答します。このメソッドは、オーケストレーター関数以外の任意の関数によって呼び出すことができます。

このオーケストレーター関数の独自の特性の 1 つは、事実上履歴がないことです。`ContinueAsNew` メソッドは、イベントの処理後に毎回履歴をリセットします。 これは、有効期間が任意であるオーケストレーターを実装するときに推奨される方法です。 `while` ループを使用すると、オーケストレーター関数の履歴が無限に多くなり、メモリ使用量が不必要に大きくなる可能性があります。

> [!NOTE]
> `ContinueAsNew` メソッドには、永続的オーケストレーション以外のユースケースがあります。 詳細については、[外部オーケストレーション](durable-functions-eternal-orchestrations.md)に関する記事を参照してください。

## <a name="run-the-sample"></a>サンプルの実行

次の HTTP POST 要求を送信してオーケストレーションを開始できます。 `counterState` をゼロ (`int` の既定値) で開始できるように、この要求には内容がありません。

```
POST http://{host}/orchestrators/E3_Counter
Content-Length: 0
```

```
HTTP/1.1 202 Accepted
Content-Length: 719
Content-Type: application/json; charset=utf-8
Location: http://{host}/admin/extensions/DurableTaskExtension/instances/bcf6fb5067b046fbb021b52ba7deae5a?taskHub=DurableFunctionsHub&connection=Storage&code={systemKey}

{
  "id":"bcf6fb5067b046fbb021b52ba7deae5a",
  "statusQueryGetUri":"http://{host}/admin/extensions/DurableTaskExtension/instances/bcf6fb5067b046fbb021b52ba7deae5a?taskHub=DurableFunctionsHub&connection=Storage&code={systemKey}",
  "sendEventPostUri":"http://{host}/admin/extensions/DurableTaskExtension/instances/bcf6fb5067b046fbb021b52ba7deae5a/raiseEvent/{eventName}?taskHub=DurableFunctionsHub&connection=Storage&code={systemKey}",
  "terminatePostUri":"http://{host}/admin/extensions/DurableTaskExtension/instances/bcf6fb5067b046fbb021b52ba7deae5a/terminate?reason={text}&taskHub=DurableFunctionsHub&connection=Storage&code={systemKey}"}
```

**E3_Counter** インスタンスが起動し、その直後に、`RaiseEventAsync` または 202 応答で参照される **sendEventUrl** HTTP POST webhook を使用して送信されるイベントのために待機します。 有効な `eventName` 値には、*incr*、*decr* および *end* が含まれます。

```
POST http://{host}/admin/extensions/DurableTaskExtension/instances/bcf6fb5067b046fbb021b52ba7deae5a/raiseEvent/operation?taskHub=DurableFunctionsHub&connection=Storage&code={systemKey}
Content-Type: application/json
Content-Length: 6

"incr"
```

"incr" 操作の結果は、Azure Functions ポータルで関数ログを調べることで確認できます。

```
2017-06-29T18:54:53.998 Function started (Id=34e34a61-38b3-4eac-b6e2-98b85e32eec8)
2017-06-29T18:54:53.998 Current counter state is 0. Waiting for next operation.
2017-06-29T18:58:01.458 Function started (Id=b45d6c2f-39f3-42a2-b904-7761b2614232)
2017-06-29T18:58:01.458 Current counter state is 0. Waiting for next operation.
2017-06-29T18:58:01.458 Received 'incr' operation.
2017-06-29T18:58:01.458 Function completed (Success, Id=b45d6c2f-39f3-42a2-b904-7761b2614232, Duration=8ms)
2017-06-29T18:58:11.518 Function started (Id=e1f38cb2-546a-404d-ab22-1ac8f81a93d9)
2017-06-29T18:58:11.518 Current counter state is 1. Waiting for next operation.
```

同様に、オーケストレーターの状態をチェックすると、`input` フィールドが更新された値 (1) に設定されていることがわかります。

```
GET http://{host}/admin/extensions/DurableTaskExtension/instances/bcf6fb5067b046fbb021b52ba7deae5a?taskHub=DurableFunctionsHub&connection=Storage&code={systemKey} HTTP/1.1
```

```
HTTP/1.1 202 Accepted
Content-Length: 129
Content-Type: application/json; charset=utf-8
Location: http://{host}/admin/extensions/DurableTaskExtension/instances/bcf6fb5067b046fbb021b52ba7deae5a?taskHub=DurableFunctionsHub&connection=Storage&code={systemKey}

{"runtimeStatus":"Running","input":1,"output":null,"createdTime":"2017-06-29T18:58:01Z","lastUpdatedTime":"2017-06-29T18:58:11Z"}
```

このインスタンスに新しい操作を送信し、その状態が操作に応じて更新されることを観察できます。 インスタンスを中止する場合は、*end* 操作を送信することでそれを行うことができます。

> [!WARNING]
> この記事の執筆時点では、`ContinueAsNew` の呼び出しと、外部イベントや終了要求などのメッセージの処理が並行して発生した場合に競合状態が発生することがわかっています。 これらの競合状態に関する最新の情報については、[GitHub の問題](https://github.com/Azure/azure-functions-durable-extension/issues/67)を参照してください。

## <a name="next-steps"></a>次のステップ

このサンプルでは、[ステートフル シングルトン](durable-functions-singletons.md)で[外部イベント](durable-functions-external-events.md)を処理し、[永続的オーケストレーション](durable-functions-eternal-orchestrations.md)を実装する方法について説明しました。 次のサンプルでは、外部イベントと[持続的タイマー](durable-functions-timers.md)を使用してユーザーの操作を処理する方法について説明します。

> [!div class="nextstepaction"]
> [人との対話が含まれるサンプルを実行する](durable-functions-phone-verification.md)
