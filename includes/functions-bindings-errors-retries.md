---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 10/01/2020
ms.author: glenga
ms.openlocfilehash: 2ccff72be66a88b9bf0a5e9eb9c29ade8397804b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "96356195"
---
Azure Functions で発生するエラーは、次のいずれかが元になっています。

- Azure Functions の組み込みの[トリガーとバインド](..\articles\azure-functions\functions-triggers-bindings.md)の使用
- 基になっている Azure サービスの API の呼び出し
- REST エンドポイントの呼び出し
- クライアント ライブラリ、パッケージ、またはサードパーティ API の呼び出し

データやメッセージが失われないようにするには、次の優れたエラー処理手法が重要です。 推奨されるエラー処理方法には、次のアクションが含まれます。

- [[Application Insights を有効にする]](../articles/azure-functions/functions-monitoring.md)
- [構造化エラー処理を使用する](#use-structured-error-handling)
- [べき等に設計する](../articles/azure-functions/functions-idempotent.md)
- [再試行ポリシーを実装する](#retry-policies-preview) (該当する場合)

### <a name="use-structured-error-handling"></a>構造化エラー処理を使用する

アプリケーションの正常性を監視するには、エラーをキャプチャしてログすることが重要です。 関数コードの最上位レベルに、try/catch ブロックを含める必要があります。 catch ブロックでは、エラーをキャプチャしてログできます。

## <a name="retry-policies-preview"></a>再試行ポリシー (プレビュー)

再試行ポリシーは、関数アプリ内の任意の関数について、任意のトリガー種類で定義できます。  再試行ポリシーでは、正常に実行されるか、最大回数の再試行が発生するまで、関数が再実行されます。  再試行ポリシーは、アプリ内のすべての関数または個々の関数対して定義することができます。  既定では、関数アプリによりメッセージは再試行されません ([トリガー ソースに再試行ポリシーがある特定のトリガー](#using-retry-support-on-top-of-trigger-resilience)を除きます)。  再試行ポリシーは、実行結果がキャッチされない例外になるたびに評価されます。  ベスト プラクティスとして、コード内のすべての例外をキャッチし、再試行が発生する可能性のあるエラーを再スローする必要があります。  Event Hubs および Azure Cosmos DB チェックポイントは、実行の再試行ポリシーが完了するまで書き込まれません。つまり、現在のバッチが完了するまで、そのパーティションの進行が一時停止されます。

### <a name="retry-policy-options"></a>再試行ポリシーのオプション

再試行ポリシーの定義には、次のオプションがあります。

**最大再試行回数** は、最終的に失敗となる前に実行が再試行される最大回数です。 値 `-1` は、無制限に再試行することを意味します。 現在の再試行回数は、インスタンスのメモリに格納されます。 インスタンスの再試行の間にエラーが発生する可能性があります。  再試行ポリシーの実行中にインスタンスでエラーが発生した場合、再試行回数は失われます。  インスタンスでエラーが発生すると、Event Hubs、Azure Cosmos DB、Queue Storage などのトリガーは処理を再開し、新しいインスタンスでバッチを再試行できます。再試行回数はゼロにリセットされます。  HTTP やタイマーなどの他のトリガーは、新しいインスタンスで再開されません。  これは、最大再試行回数がベスト エフォートであり、まれに、実行が最大値を超える場合や、HTTP やタイマーの再試行回数が最大値を下回ることがあることを意味します。

**再試行戦略** により、再試行の動作が制御されます。  次の 2 つの再試行オプションがサポートされています。

| オプション | 説明|
|---|---|
|**`fixedDelay`**| 各再試行の間に指定された時間を経過させることができます|
| **`exponentialBackoff`**| 最初の再試行は、最小遅延時間待機します。 その後の再試行では、最大遅延に達するまで、再試行のたびに最初の時間に対して指数関数的に時間が追加されます。  エクスポネンシャル バックオフを行うと、スループットの高いシナリオでの再試行時間をずらすために、わずかなランダム化が加わります。|

#### <a name="app-level-configuration"></a>アプリ レベルの構成

[`host.json` ファイル](../articles/azure-functions/functions-host-json.md#retry)を使用して、アプリのすべての関数に対して再試行ポリシーを定義できます。 

#### <a name="function-level-configuration"></a>関数レベルの構成

特定の関数に対して再試行ポリシーを定義できます。  関数固有の構成は、アプリ レベルの構成よりも優先されます。

#### <a name="fixed-delay-retry"></a>固定遅延の再試行

# <a name="c"></a>[C#](#tab/csharp)

再試行には [Microsoft.Azure.WebJobs](https://www.nuget.org/packages/Microsoft.Azure.WebJobs) >= 3.0.23 の NuGet パッケージが必要です。

```csharp
[FunctionName("EventHubTrigger")]
[FixedDelayRetry(5, "00:00:10")]
public static async Task Run([EventHubTrigger("myHub", Connection = "EventHubConnection")] EventData[] events, ILogger log)
{
// ...
}
```

# <a name="c-script"></a>[C# スクリプト](#tab/csharp-script)

*function.json* ファイルの再試行ポリシーを次に示します。

```json
{
    "disabled": false,
    "bindings": [
        {
            ....
        }
    ],
    "retry": {
        "strategy": "fixedDelay",
        "maxRetryCount": 4,
        "delayInterval": "00:00:10"
    }
}
```
# <a name="javascript"></a>[JavaScript](#tab/javascript)

*function.json* ファイルの再試行ポリシーを次に示します。


```json
{
    "disabled": false,
    "bindings": [
        {
            ....
        }
    ],
    "retry": {
        "strategy": "fixedDelay",
        "maxRetryCount": 4,
        "delayInterval": "00:00:10"
    }
}
```

# <a name="python"></a>[Python](#tab/python)

*function.json* ファイルの再試行ポリシーを次に示します。

```json
{
    "disabled": false,
    "bindings": [
        {
            ....
        }
    ],
    "retry": {
        "strategy": "fixedDelay",
        "maxRetryCount": 4,
        "delayInterval": "00:00:10"
    }
}
```

# <a name="java"></a>[Java](#tab/java)

*function.json* ファイルの再試行ポリシーを次に示します。


```json
{
    "disabled": false,
    "bindings": [
        {
            ....
        }
    ],
    "retry": {
        "strategy": "fixedDelay",
        "maxRetryCount": 4,
        "delayInterval": "00:00:10"
    }
}
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

*function.json* ファイルの再試行ポリシーを次に示します。


```json
{
    "disabled": false,
    "bindings": [
        {
            ....
        }
    ],
    "retry": {
        "strategy": "fixedDelay",
        "maxRetryCount": 4,
        "delayInterval": "00:00:10"
    }
}
```
---

#### <a name="exponential-backoff-retry"></a>エクスポネンシャル バックオフ再試行

# <a name="c"></a>[C#](#tab/csharp)

再試行には [Microsoft.Azure.WebJobs](https://www.nuget.org/packages/Microsoft.Azure.WebJobs) >= 3.0.23 の NuGet パッケージが必要です。

```csharp
[FunctionName("EventHubTrigger")]
[ExponentialBackoffRetry(5, "00:00:04", "00:15:00")]
public static async Task Run([EventHubTrigger("myHub", Connection = "EventHubConnection")] EventData[] events, ILogger log)
{
// ...
}
```

# <a name="c-script"></a>[C# スクリプト](#tab/csharp-script)

*function.json* ファイルの再試行ポリシーを次に示します。

```json
{
    "disabled": false,
    "bindings": [
        {
            ....
        }
    ],
    "retry": {
        "strategy": "exponentialBackoff",
        "maxRetryCount": 5,
        "minimumInterval": "00:00:10",
        "maximumInterval": "00:15:00"
    }
}
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

*function.json* ファイルの再試行ポリシーを次に示します。

```json
{
    "disabled": false,
    "bindings": [
        {
            ....
        }
    ],
    "retry": {
        "strategy": "exponentialBackoff",
        "maxRetryCount": 5,
        "minimumInterval": "00:00:10",
        "maximumInterval": "00:15:00"
    }
}
```

# <a name="python"></a>[Python](#tab/python)

*function.json* ファイルの再試行ポリシーを次に示します。

```json
{
    "disabled": false,
    "bindings": [
        {
            ....
        }
    ],
    "retry": {
        "strategy": "exponentialBackoff",
        "maxRetryCount": 5,
        "minimumInterval": "00:00:10",
        "maximumInterval": "00:15:00"
    }
}
```

# <a name="java"></a>[Java](#tab/java)

*function.json* ファイルの再試行ポリシーを次に示します。

```json
{
    "disabled": false,
    "bindings": [
        {
            ....
        }
    ],
    "retry": {
        "strategy": "exponentialBackoff",
        "maxRetryCount": 5,
        "minimumInterval": "00:00:10",
        "maximumInterval": "00:15:00"
    }
}
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

*function.json* ファイルの再試行ポリシーを次に示します。

```json
{
    "disabled": false,
    "bindings": [
        {
            ....
        }
    ],
    "retry": {
        "strategy": "exponentialBackoff",
        "maxRetryCount": 5,
        "minimumInterval": "00:00:10",
        "maximumInterval": "00:15:00"
    }
}
```
---

|function.json のプロパティ  |属性のプロパティ | 説明 |
|---------|---------|---------| 
|strategy|該当なし|必須。 使用する再試行戦略。 有効な値は `fixedDelay` または `exponentialBackoff`です。|
|maxRetryCount|該当なし|必須。 関数の実行ごとに許可される再試行の最大回数。 `-1` は、無制限に再試行することを意味します。|
|delayInterval|該当なし|`fixedDelay` 戦略を使用するときに、再試行の間に使用される遅延。|
|minimumInterval|該当なし|`exponentialBackoff` 戦略を使用する場合の最小再試行遅延。|
|maximumInterval|該当なし|`exponentialBackoff` 戦略を使用する場合の最大再試行遅延。| 

### <a name="retry-limitations-during-preview"></a>プレビュー期間中の再試行の制限事項

- .NET プロジェクトの場合は、[Microsoft.Azure.WebJobs](https://www.nuget.org/packages/Microsoft.Azure.WebJobs) > = 3.0.23 のバージョンを手動でプルすることが必要になる場合があります。
- 従量課金プランでは、キュー内の最後のメッセージの再試行中に、アプリがゼロにスケールダウンされることがあります。
- 従量課金プランでは、再試行の実行中にアプリがスケールダウンされることがあります。  最良の結果を得るには、再試行間隔を <= 00:01:00 で、再試行を <= 5 回として選択します。

## <a name="using-retry-support-on-top-of-trigger-resilience"></a>トリガーの回復性に加えて再試行サポートを使用する

関数アプリの再試行ポリシーは、トリガーによって提供される再試行または回復性に依存しません。  関数の再試行ポリシーは、トリガーによる回復性の高い再試行の上にある層として位置付けられます。  たとえば、Azure Service Bus を使用する場合、既定ではキューのメッセージ配信数は 10 です。  既定の配信数は、キュー メッセージの配信が 10 回試行された後、Service Bus によってメッセージが配信不能にされることを意味します。  Service Bus トリガーを持つ関数の再試行ポリシーを定義できますが、再試行は、Service Bus の配信試行の上位層になります。  

たとえば、既定の Service Bus 配信数に 10 を使用していて、関数の再試行ポリシーを 5 に定義したとします。  メッセージは最初にデキューされ、Service Bus の配信アカウントが 1 にインクリメントされます。  すべての実行が失敗して、同じメッセージのトリガーが 5 回試行されると、そのメッセージは破棄済みとしてマークされます。  Service Bus によってメッセージがすぐにキューへ再び戻されるので、関数がトリガーされ、配信数が 2 にインクリメントされます。  最終的に、50 回の試行が完了した後 (10 回の Service Bus の配信 * 配信あたり 5 回の関数の再試行)、メッセージは破棄され、Service Bus の配信不能メッセージがトリガーされます。

> [!WARNING]
> Service Bus キューなどのトリガーの配信数は 1 に設定しないことをお勧めします。これは、1 回の関数の再試行サイクルの直後にメッセージが配信不能になることを意味します。  これは、トリガーが再試行による回復性を提供しているのに対し、関数の再試行ポリシーはベスト エフォートであり、必要な再試行回数の合計よりも少なくなる可能性があるためです。

### <a name="triggers-with-additional-resiliency-or-retries"></a>追加の回復性または再試行を使用するトリガー

次のトリガーは、トリガー ソースでの再試行をサポートしています。

* [Azure BLOB Storage](../articles/azure-functions/functions-bindings-storage-blob.md)
* [Azure Queue Storage](../articles/azure-functions/functions-bindings-storage-queue.md)
* [Azure Service Bus (キュー/トピック)](../articles/azure-functions/functions-bindings-service-bus.md)

既定では、ほとんどのトリガーにより要求が最大 5 回再試行されます。 5 回目の再試行後に、Azure Queue ストレージによって[有害キュー](../articles/azure-functions/functions-bindings-storage-queue-trigger.md#poison-messages)にメッセージが書き込まれます。  既定の Service Bus キューおよびトピックのポリシーでは、10 回試行した後[配信不能キュー](../articles/service-bus-messaging/service-bus-dead-letter-queues.md)にメッセージが書き込まれます。
