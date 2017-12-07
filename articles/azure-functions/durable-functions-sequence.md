---
title: "Durable Functions での関数チェーン - Azure"
description: "一連の関数を実行する Durable Functions のサンプルを実行する方法を説明します。"
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
ms.openlocfilehash: 0973f83ae839597f3b499814a4a04a8a640a1fb6
ms.sourcegitcommit: cf42a5fc01e19c46d24b3206c09ba3b01348966f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/29/2017
---
# <a name="function-chaining-in-durable-functions---hello-sequence-sample"></a>Durable Functions での関数チェーン - Hello シーケンス サンプル

関数チェーンとは、特定の順序で一連の関数を実行するパターンです。 ある関数の出力が、別の関数の入力に適用される必要がある、ということがよくあります。 この記事では、[Durable Functions](durable-functions-overview.md) を使用して関数チェーンを実装するサンプルを説明します。

## <a name="prerequisites"></a>前提条件

* [Durable Functions のインストール](durable-functions-install.md)に関するページの指示に従って、サンプルを設定します。

## <a name="the-functions"></a>関数

この記事では、サンプル アプリにおける次の関数について説明します。

* `E1_HelloSequence`: 1 つのシーケンスで `E1_SayHello` を複数回呼び出す orchestrator 機能。 `E1_SayHello` 呼び出しからの出力を格納し、結果を記録します。
* `E1_SayHello`: 文字列の先頭に「Hello」を付加するアクティビティ関数。

以下のセクションでは、Azure Portal の開発に使用される構成とコードについて説明します。 Visual Studio 開発用のコードは、記事の最後に記載されています。
 
## <a name="functionjson-file"></a>function.json ファイル

Azure Portal を開発に使用する場合は、こちらの orchestrator 機能の *function.json* ファイルの内容を参照してください。 ほとんどの orchestrator *function.json* ファイルは、このような内容です。

[!code-json[Main](~/samples-durable-functions/samples/csx/E1_HelloSequence/function.json)]

重要な点は、`orchestrationTrigger` というバインドの種類です。 すべての orchestrator 機能は、このトリガーの種類を使用する必要があります。

> [!WARNING]
> Orchestrator 機能の "I/O なし" の規則に従うには、`orchestrationTrigger` トリガー バインドを使用する場合に、入力または出力バインドを使用しないでください。  他の入力または出力バインドが必要な場合は、`activityTrigger` 関数のコンテキストで使用する必要があります。それらがオーケストレーターによって呼び出されます。

## <a name="c-script"></a>C# スクリプト

以下がソース コードです。

[!code-csharp[Main](~/samples-durable-functions/samples/csx/E1_HelloSequence/run.csx)]

すべての C# オーケストレーション関数は、`Microsoft.Azure.WebJobs.Extensions.DurableTask` アセンブリにある `DurableOrchestrationContext` 型のパラメーターを持つ必要があります。 C# スクリプトを使用している場合、アセンブリは `#r` 表記を使用して参照できます。 このコンテキスト オブジェクトでは、他の*アクティビティ*関数を呼び出し、その [CallActivityAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html#Microsoft_Azure_WebJobs_DurableOrchestrationContext_CallActivityAsync_) メソッドを使用して入力パラメーターを渡すことができます。

このコードでは、`E1_SayHello` を異なるパラメーター値で 3 回続けて呼び出します。 各呼び出しの戻り値が `outputs` 一覧に追加され、それが関数の末尾に返されます。

アクティビティ関数 `E1_SayHello` の *function.json* ファイルは、`E1_HelloSequence` のそれに似ていますが、バインドの種類 `orchestrationTrigger` の代わりにバインドの種類 `activityTrigger` を使用する点が違います。

[!code-json[Main](~/samples-durable-functions/samples/csx/E1_SayHello/function.json)]

> [!NOTE]
> オーケストレーション関数によって呼び出される関数は、`activityTrigger` バインドを使用する必要があります。

`E1_SayHello` の実装は、比較的単純な文字列の書式設定操作です。

[!code-csharp[Main](~/samples-durable-functions/samples/csx/E1_SayHello/run.csx)]

この関数は [DurableActivityContext](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableActivityContext.html) 型のパラメーターを持ち、オーケストレーター関数の [`CallActivityAsync<T>`](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html#Microsoft_Azure_WebJobs_DurableOrchestrationContext_CallActivityAsync_) への呼び出しによって渡された入力を取得するのに使用されます。

## <a name="run-the-sample"></a>サンプルの実行

`E1_HelloSequence` オーケストレーションを実行するには、次の HTTP POST 要求を送信します。

```
POST http://{host}/orchestrators/E1_HelloSequence
```

たとえば、"myfunctionapp" という関数アプリのサンプルを実行する場合、"{host}" を "myfunctionapp.azurewebsites.net" に置き換えます。

結果は次のような HTTP 202 応答です (簡潔にするため省略しています)。

```
HTTP/1.1 202 Accepted
Content-Length: 719
Content-Type: application/json; charset=utf-8
Location: http://{host}/admin/extensions/DurableTaskExtension/instances/96924899c16d43b08a536de376ac786b?taskHub=DurableFunctionsHub&connection=Storage&code={systemKey}

(...trimmed...)
```

この時点で、オーケストレーションはキューに登録され、すぐに実行を開始します。 `Location` ヘッダー内の URL は、実行状態を確認するのに使用できます。

```
GET http://{host}/admin/extensions/DurableTaskExtension/instances/96924899c16d43b08a536de376ac786b?taskHub=DurableFunctionsHub&connection=Storage&code={systemKey}
```

この結果がオーケストレーションの状態です。 迅速に実行して完了し、次のような応答で*完了*状態にあることがわかります(簡潔にするため省略しています)。

```
HTTP/1.1 200 OK
Content-Length: 179
Content-Type: application/json; charset=utf-8

{"runtimeStatus":"Completed","input":null,"output":["Hello Tokyo!","Hello Seattle!","Hello London!"],"createdTime":"2017-06-29T05:24:57Z","lastUpdatedTime":"2017-06-29T05:24:59Z"}
```

このように、インスタンスの `runtimeStatus` は*完了*しており、`output` は orchestrator 関数の実行の JSON でシリアル化された結果を格納します。

> [!NOTE]
> orchestrator 関数を開始した HTTP POST エンドポイントは、"HttpStart" という名前の HTTP トリガー関数としてサンプル アプリに実装されています。 同様のスターター ロジックを `queueTrigger`、`eventHubTrigger`、`timerTrigger` などの他のトリガーの種類に実装することができます。

関数の実行ログを確認しましょう。 `E1_HelloSequence` 関数が、[概要](durable-functions-overview.md)で説明されている再生の動作のため、複数回、開始し完了しています。 その一方で、`E1_SayHello` の実行は 3 回だけでした。これらの関数の実行は再生されなかったためです。

## <a name="visual-studio-sample-code"></a>Visual Studio サンプル コード

Visual Studio プロジェクトの単一の C# ファイルとしてのオーケストレーションを次に示します。

[!code-csharp[Main](~/samples-durable-functions/samples/precompiled/HelloSequence.cs)]

## <a name="next-steps"></a>次のステップ

このサンプルでは、単純な関数チェーンのオーケストレーションについて説明しました。 次のサンプルでは、ファンアウト/ファンイン パターンの実装方法について説明します。 

> [!div class="nextstepaction"]
> [ファンアウト/ファンイン サンプルの実行](durable-functions-cloud-backup.md)
