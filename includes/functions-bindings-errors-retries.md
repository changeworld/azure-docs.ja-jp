---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 10/01/2020
ms.author: glenga
ms.openlocfilehash: 285c3bf37e9d6de042cb028745fc8b094d34c3a1
ms.sourcegitcommit: 7863fcea618b0342b7c91ae345aa099114205b03
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/03/2020
ms.locfileid: "93284405"
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
- [再試行ポリシーを実装する](#retry-policies) (該当する場合)

### <a name="use-structured-error-handling"></a>構造化エラー処理を使用する

アプリケーションの正常性を監視するには、エラーをキャプチャしてログすることが重要です。 関数コードの最上位レベルに、try/catch ブロックを含める必要があります。 catch ブロックでは、エラーをキャプチャしてログできます。

## <a name="retry-policies"></a>再試行ポリシー

再試行ポリシーは、関数アプリ内の任意の関数について、任意のトリガー種類で定義できます。  再試行ポリシーでは、正常に実行されるか、最大回数の再試行が発生するまで、関数が再実行されます。  再試行ポリシーは、アプリ内のすべての関数または個々の関数対して定義することができます。  既定では、関数アプリによりメッセージは再試行されません ([トリガー ソースに再試行ポリシーがある特定のトリガー](#trigger-specific-retry-support)を除きます)。  再試行ポリシーは、実行結果がキャッチされない例外になるたびに評価されます。  ベスト プラクティスとして、コード内のすべての例外をキャッチし、再試行が発生する可能性のあるエラーを再スローする必要があります。  Event Hubs および Azure Cosmos DB チェックポイントは、実行の再試行ポリシーが完了するまで書き込まれません。つまり、現在のバッチが完了するまで、そのパーティションの進行が一時停止されます。

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
---

#### <a name="exponential-backoff-retry"></a>エクスポネンシャル バックオフ再試行

# <a name="c"></a>[C#](#tab/csharp)

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
---

|function.json のプロパティ  |属性のプロパティ | 説明 |
|---------|---------|---------| 
|strategy|該当なし|必須。 使用する再試行戦略。 有効な値は `fixedDelay` または `exponentialBackoff`です。|
|maxRetryCount|該当なし|必須。 関数の実行ごとに許可される再試行の最大回数。 `-1` は、無制限に再試行することを意味します。|
|delayInterval|該当なし|`fixedDelay` 戦略を使用するときに、再試行の間に使用される遅延。|
|minimumInterval|該当なし|`exponentialBackoff` 戦略を使用する場合の最小再試行遅延。|
|maximumInterval|該当なし|`exponentialBackoff` 戦略を使用する場合の最大再試行遅延。| 

## <a name="trigger-specific-retry-support"></a>トリガー固有の再試行サポート

トリガーの中には、トリガー ソースで再試行を行うものもあります。  これらのトリガーの再試行は、関数アプリのホストの再試行ポリシーに加えて、または代替として使用できます。  一定回数の再試行が必要な場合は、汎用ホスト再試行ポリシーよりもトリガー固有の再試行ポリシーを使用する必要があります。  次のトリガーは、トリガー ソースでの再試行をサポートしています。

* [Azure BLOB Storage](../articles/azure-functions/functions-bindings-storage-blob.md)
* [Azure Queue Storage](../articles/azure-functions/functions-bindings-storage-queue.md)
* [Azure Service Bus (キュー/トピック)](../articles/azure-functions/functions-bindings-service-bus.md)

既定では、これらのトリガーにより要求が最大 5 回再試行されます。 5 回目の再試行後に、Azure Queue storage と Azure Service Bus の両方のトリガーによって[有害キュー](../articles/azure-functions/functions-bindings-storage-queue-trigger.md#poison-messages)にメッセージが書き込まれます。
