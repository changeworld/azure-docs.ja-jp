---
title: Durable Functions のバインド - Azure
description: Azure Functions の Durable Functons 拡張機能のトリガーとバインドの使用方法。
ms.topic: conceptual
ms.date: 08/03/2021
ms.author: azfuncdf
ms.openlocfilehash: 097527dbbf4363365e609a1f5aac1d851eb5dc60
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/13/2021
ms.locfileid: "121742660"
---
# <a name="bindings-for-durable-functions-azure-functions"></a>Durable Functions のバインド (Azure Functions)

[Durable Functions](durable-functions-overview.md) 拡張機能には、オーケストレーター関数、エンティティ関数、アクティビティ関数の実行を制御する 3 つの新しいトリガーのバインドが導入されています。 Durable Functions ランタイムのクライアントとして機能する出力バインドも導入されています。

## <a name="orchestration-trigger"></a>オーケストレーション トリガー

オーケストレーション トリガーを使用して、[永続的なオーケストレーター関数](durable-functions-types-features-overview.md#orchestrator-functions)を作成できます。 このトリガーは、新しいオーケストレーション インスタンスのスケジュールが設定されたときと、既存のオーケストレーション インスタンスでイベントが受信されたときに実行されます。 オーケストレーター関数がトリガーされる可能性があるイベントの例としては、永続的なタイマーの有効期限、アクティビティ関数の応答、外部クライアントによって発生したイベントがあります。

.NET で関数を作成すると、[OrchestrationTriggerAttribute](/dotnet/api/microsoft.azure.webjobs.extensions.durabletask.orchestrationtriggerattribute) .NET 属性を使用して、オーケストレーション トリガーが構成されます。

オーケストレーター関数を (JavaScript、Python、PowerShell などの) スクリプト言語で記述する場合、オーケストレーション トリガーは、*function.json* ファイルの `bindings` 配列の次の JSON オブジェクトによって定義されます。

```json
{
    "name": "<Name of input parameter in function signature>",
    "orchestration": "<Optional - name of the orchestration>",
    "type": "orchestrationTrigger",
    "direction": "in"
}
```

* `orchestration` は、クライアントがこのオーケストレーター関数の新しいインスタンスを開始するときに使用する必要がある、オーケストレーションの名前です。 このプロパティは省略可能です。 指定されない場合は関数の名前が使用されます。

内部的には、このトリガー バインドによって、オーケストレーション開始イベント、永続的なタイマーの有効期限のイベント、アクティビティ関数の応答イベント、他の関数によって発生した外部イベントなど、新しいオーケストレーション イベントについて、構成された永続ストアがポーリングされます。

### <a name="trigger-behavior"></a>トリガーの動作

オーケストレーション トリガーについての注意事項を次に示します。

* **シングル スレッド** - 1 つのホスト インスタンスのすべてのオーケストレーター関数の実行で単一のディスパッチャー スレッドが使用されます。 このため、オーケストレーター関数コードが効率的であり、I/O の実行がないことを保証することが重要です。 さらに、このスレッドが、Durable Functions 固有のタスクの種類のために待機しているとき以外は、非同期操作を行わないことを保証することも重要です。
* **有害メッセージの処理** - オーケストレーション トリガーでは、有害メッセージはサポートされません。
* **メッセージの可視性** - オーケストレーション トリガー メッセージはキューから削除され、構成可能な期間にわたって非表示を保持します。 これらのメッセージの可視性は、関数アプリが正常に実行されている限り、自動的に更新されます。
* **戻り値** - 戻り値は JSON にシリアル化され、Azure Table Storage のオーケストレーション履歴テーブルに保存されます。 これらの戻り値は、オーケストレーション クライアントのバインドによるクエリを実行できます。これについては、後で説明します。

> [!WARNING]
> オーケストレーター関数は、オーケストレーション トリガーのバインドを除いて、入力または出力バインドを使用しないでください。 これらのバインドはシングル スレッド ルールと I/O ルールに従っていない可能性があるため、これらのバインドを実行すると、Durable Task 拡張機能で問題が発生する可能性があります。 他のバインドを使用する場合は、オーケストレーター関数から呼び出されたアクティビティ関数にそれらを追加します。 オーケストレーター関数のコーディングの制約について詳しくは、「[オーケストレーター関数のコードの制約](durable-functions-code-constraints.md)」のドキュメントをご覧ください。

> [!WARNING]
> JavaScript と Python のオーケストレーター関数は、`async` で宣言されてはなりません。

### <a name="trigger-usage"></a>トリガーの使用方法

オーケストレーション トリガーのバインドは、入力と出力の両方をサポートします。 入力と出力の処理に関する注意事項を次に示します。

* **入力** - オーケストレーション トリガーは、コンテキスト入力オブジェクトを介してアクセスされる入力を使用して呼び出すことができます。 すべての入力は、JSON にシリアル化できる必要があります。
* **出力** - オーケストレーション トリガーは、入力と同じように出力値をサポートします。 関数の戻り値は、出力値を割り当てるために使用され、JSON にシリアル化できる必要があります。

### <a name="trigger-sample"></a>トリガー サンプル

最もシンプルな "Hello World" オーケストレーター関数のコードの例を次に示します。

# <a name="c"></a>[C#](#tab/csharp)

```csharp
[FunctionName("HelloWorld")]
public static string Run([OrchestrationTrigger] IDurableOrchestrationContext context)
{
    string name = context.GetInput<string>();
    // ... do some work ...
    return $"Hello {name}!";
}
```

> [!NOTE]
> 前のコードは Durable Functions 2.x 用です。 Durable Functions 1.x の場合、`IDurableOrchestrationContext` の代わりに `DurableOrchestrationContext` を使用する必要があります。 バージョン間の相違点の詳細については、[Durable Functions のバージョン](durable-functions-versions.md)に関する記事を参照してください。

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
const df = require("durable-functions");

module.exports = df.orchestrator(function*(context) {
    const name = context.df.getInput();
    // ... do some work ...
    return `Hello ${name}!`;
});
```

> [!NOTE]
> ライブラリ `durable-functions` によって、ジェネレーター関数の終了時に `context.done` メソッドの呼び出しが処理されます。

# <a name="python"></a>[Python](#tab/python)

```python
import azure.durable_functions as df

def orchestrator_function(context: df.DurableOrchestrationContext):
    input_ = context.get_input()
    # Do some work
    return f"Hello {name}!"

main = df.Orchestrator.create(orchestrator_function)
```

---

オーケストレーター関数の大半はアクティビティ関数を呼び出すため、"Hello World" の例でアクティビティ関数を呼び出す方法を示します。

# <a name="c"></a>[C#](#tab/csharp)

```csharp
[FunctionName("HelloWorld")]
public static async Task<string> Run(
    [OrchestrationTrigger] IDurableOrchestrationContext context)
{
    string name = context.GetInput<string>();
    string result = await context.CallActivityAsync<string>("SayHello", name);
    return result;
}
```

> [!NOTE]
> 前のコードは Durable Functions 2.x 用です。 Durable Functions 1.x の場合、`IDurableOrchestrationContext` の代わりに `DurableOrchestrationContext` を使用する必要があります。 バージョン間の相違点の詳細については、[Durable Functions のバージョン](durable-functions-versions.md)に関する記事を参照してください。

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
const df = require("durable-functions");

module.exports = df.orchestrator(function*(context) {
    const name = context.df.getInput();
    const result = yield context.df.callActivity("SayHello", name);
    return result;
});
```

# <a name="python"></a>[Python](#tab/python)

```python
import azure.durable_functions as df

def orchestrator_function(context: df.DurableOrchestrationContext):
    input_ = context.get_input()
    result = yield context.call_activity('SayHello', name)
    return result

main = df.Orchestrator.create(orchestrator_function)
```

---

## <a name="activity-trigger"></a>アクティビティ トリガー

アクティビティ トリガーを使用すると、オーケストレーター関数によって呼び出される関数 ([アクティビティ関数](durable-functions-types-features-overview.md#activity-functions)と呼ばれる) を作成できます。

.NET を使用して関数を作成する場合、アクティビティ トリガーは [ActvityTriggerAttribute](/dotnet/api/microsoft.azure.webjobs.extensions.durabletask.activitytriggerattribute) .NET 属性を使用して構成されます。

JavaScript、Python、または PowerShell を使用する場合、アクティビティ トリガーは、*function.json* の `bindings` 配列の次の JSON オブジェクトによって定義されます。

```json
{
    "name": "<Name of input parameter in function signature>",
    "activity": "<Optional - name of the activity>",
    "type": "activityTrigger",
    "direction": "in"
}
```

* `activity` はアクティビティの名前です。 この値は、オーケストレーター関数がこのアクティビティ関数を呼び出すために使用する名前です。 このプロパティは省略可能です。 指定されない場合は関数の名前が使用されます。

内部的には、このトリガー バインドによって、構成された永続ストアに対して新しいアクティビティ実行イベントがポーリングされます。

### <a name="trigger-behavior"></a>トリガーの動作

アクティビティ トリガーに関する注意事項を次に示します。

* **スレッド処理** - オーケストレーション トリガーとは異なり、アクティビティ トリガーにはスレッド処理と I/O に関する制限はありません。 それらは、標準的な関数と同様に扱うことができます。
* **有害メッセージの処理** - アクティビティ トリガーには、有害メッセージのサポートはありません。
* **メッセージの可視性** - アクティビティ トリガー メッセージはキューから削除され、構成可能な期間にわたって非表示を保持します。 これらのメッセージの可視性は、関数アプリが正常に実行されている限り、自動的に更新されます。
* **戻り値** - 戻り値は JSON にシリアル化され、構成された永続ストアに保存されます。

### <a name="trigger-usage"></a>トリガーの使用方法

アクティビティ トリガーのバインドは、オーケストレーション トリガーと同じように、入力と出力の両方をサポートします。 入力と出力の処理に関する注意事項を次に示します。

* **入力** - アクティビティ トリガーは、オーケストレーター関数からの入力を使用して呼び出すことができます。 すべての入力は、JSON にシリアル化できる必要があります。
* **出力** - アクティビティ関数は、入力と同じように出力値をサポートします。 関数の戻り値は、出力値を割り当てるために使用され、JSON にシリアル化できる必要があります。
* **メタデータ** - .NET アクティビティ関数を `string instanceId` パラメーターにバインドして、呼び出し元オーケストレーションのインスタンス ID を取得できます。

### <a name="trigger-sample"></a>トリガー サンプル

シンプルな `SayHello` アクティビティ関数のコードの例を次に示します。

# <a name="c"></a>[C#](#tab/csharp)

```csharp
[FunctionName("SayHello")]
public static string SayHello([ActivityTrigger] IDurableActivityContext helloContext)
{
    string name = helloContext.GetInput<string>();
    return $"Hello {name}!";
}
```

.NET の `ActivityTriggerAttribute` バインディングの既定のパラメーター型は [IDurableActivityContext](/dotnet/api/microsoft.azure.webjobs.extensions.durabletask.idurableactivitycontext) (Durable Functions v1 の場合は [DurableActivityContext](/dotnet/api/microsoft.azure.webjobs.durableactivitycontext?view=azure-dotnet-legacy&preserve-view=true)) です。 ただし、.NET アクティビティ トリガーは、JSON にシリアル化できる型 (プリミティブ型を含む) への直接的なバインドもサポートしているため、同じ関数を次のように単純化できます。

```csharp
[FunctionName("SayHello")]
public static string SayHello([ActivityTrigger] string name)
{
    return $"Hello {name}!";
}
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
module.exports = async function(context) {
    return `Hello ${context.bindings.name}!`;
};
```

JavaScript のバインドは、追加のパラメーターとして渡すこともできるため、同じ関数を次のように単純化できます。

```javascript
module.exports = async function(context, name) {
    return `Hello ${name}!`;
};
```

# <a name="python"></a>[Python](#tab/python)

```python
def main(name: str) -> str:
    return f"Hello {name}!"
```

---

### <a name="using-input-and-output-bindings"></a>入出力バインドを使用する

アクティビティ トリガーのバインドに加えて、通常の入出力バインドを使用できます。 たとえば、アクティビティ バインドへの入力を取得し、EventHub 出力バインドを使用して EventHub にメッセージを送信できます。

```json
{
  "bindings": [
    {
      "name": "message",
      "type": "activityTrigger",
      "direction": "in"
    },
    {
      "type": "eventHub",
      "name": "outputEventHubMessage",
      "connection": "EventhubConnectionSetting",
      "eventHubName": "eh_messages",
      "direction": "out"
  }
  ]
}
```

```javascript
module.exports = async function (context) {
    context.bindings.outputEventHubMessage = context.bindings.message;
};
```

## <a name="orchestration-client"></a>オーケストレーション クライアント

オーケストレーション クライアントのバインドを使用すると、オーケストレーター関数とやりとりする関数を記述できます。 これらの関数は、多くの場合、[クライアント関数](durable-functions-types-features-overview.md#client-functions)と呼ばれます。 たとえば、次のようにオーケストレーション インスタンスを操作できます。

* インスタンスを開始する。
* インスタンスの状態をクエリする。
* インスタンスを終了する。
* インスタンスの実行中にイベントを送信する。
* インスタンスの履歴を消去します。

.NET を使用する場合は、[DurableClientAttribute](/dotnet/api/microsoft.azure.webjobs.extensions.durabletask.durableclientattribute) 属性 (Durable Functions v1.x の場合は [OrchestrationClientAttribute](/dotnet/api/microsoft.azure.webjobs.orchestrationclientattribute?view=azure-dotnet-legacy&preserve-view=true)) を使用して、オーケストレーション クライアントにバインドできます。

JavaScript、Python、PowerShell などのスクリプト言語を使用する場合、永続的なクライアント トリガーは、*function.json* の `bindings` 配列の次の JSON オブジェクトによって定義されます。

```json
{
    "name": "<Name of input parameter in function signature>",
    "taskHub": "<Optional - name of the task hub>",
    "connectionName": "<Optional - name of the connection string app setting>",
    "type": "orchestrationClient",
    "direction": "in"
}
```

* `taskHub` - 複数の関数アプリが同じストレージ アカウントを共有するが、相互に分離する必要があるシナリオで使用されます。 指定されていない場合は、`host.json` の既定値が使用されます。 この値は、ターゲットのオーケストレーター関数によって使用される値と一致している必要があります。
* `connectionName` - ストレージ アカウント接続文字列を含むアプリ設定の名前。 この接続文字列で表されるストレージ アカウントは、ターゲットのオーケストレーター関数によって使用されるものと同じにする必要があります。 指定されない場合は、関数アプリの既定のストレージ アカウント接続文字列が使用されます。

> [!NOTE]
> ほとんどの場合、これらのプロパティを省略し、既定の動作を使用することをお勧めします。

### <a name="client-usage"></a>クライアントの使用

.NET 関数では、通常は [IDurableClient](/dotnet/api/microsoft.azure.webjobs.extensions.durabletask.idurableclient) (Durable Functions v1.x の場合は [DurableOrchestrationClient](/dotnet/api/microsoft.azure.webjobs.durableorchestrationclient?view=azure-dotnet-legacy&preserve-view=true)) にバインドします。これにより、Durable Functions によってサポートされるすべてのオーケストレーション クライアント API にフル アクセスできます。 他の言語では、その言語に固有の SDK を使用してクライアント オブジェクトにアクセスする必要があります。

"HelloWorld" オーケストレーションを開始する、キューによってトリガーされる関数の例を次に示します。

# <a name="c"></a>[C#](#tab/csharp)

```csharp
[FunctionName("QueueStart")]
public static Task Run(
    [QueueTrigger("durable-function-trigger")] string input,
    [DurableClient] IDurableOrchestrationClient starter)
{
    // Orchestration input comes from the queue message content.
    return starter.StartNewAsync("HelloWorld", input);
}
```

> [!NOTE]
> 前記の C# コードは Durable Functions 2.x 用です。 Durable Functions 1.x では、`DurableClient` 属性の代わりに `OrchestrationClient` 属性を使用する必要があります。また、`IDurableOrchestrationClient` ではなく `DurableOrchestrationClient` パラメーター型を使用する必要があります。 バージョン間の相違点の詳細については、[Durable Functions のバージョン](durable-functions-versions.md)に関する記事を参照してください。

# <a name="javascript"></a>[JavaScript](#tab/javascript)

**function.json**
```json
{
  "bindings": [
    {
      "name": "input",
      "type": "queueTrigger",
      "queueName": "durable-function-trigger",
      "direction": "in"
    },
    {
      "name": "starter",
      "type": "durableClient",
      "direction": "in"
    }
  ]
}
```

**index.js**
```javascript
const df = require("durable-functions");

module.exports = async function (context) {
    const client = df.getClient(context);
    return instanceId = await client.startNew("HelloWorld", undefined, context.bindings.input);
};
```

# <a name="python"></a>[Python](#tab/python)

**function.json**
```json
{
  "bindings": [
    {
      "name": "input",
      "type": "queueTrigger",
      "queueName": "durable-function-trigger",
      "direction": "in"
    },
    {
      "name": "starter",
      "type": "durableClient",
      "direction": "in"
    }
  ]
}
```

**__init__.py**
```python
import json
import azure.functions as func
import azure.durable_functions as df

async def main(msg: func.QueueMessage, starter: str) -> None:
    client = df.DurableOrchestrationClient(starter)
    payload = msg.get_body().decode('utf-8')
    instance_id = await client.start_new("HelloWorld", client_input=payload)
```

---

インスタンスの開始の詳細については、[インスタンスの管理](durable-functions-instance-management.md)に関する記事を参照してください。

## <a name="entity-trigger"></a>エンティティ トリガー

エンティティ トリガーを使用すると、[エンティティ関数](durable-functions-entities.md)を作成できます。 このトリガーでは、特定のエンティティ インスタンスのイベントの処理がサポートされています。

> [!NOTE]
> エンティティ トリガーは、Durable Functions 2.x 以降で使用できます。

内部的には、このトリガー バインドによって、実行する必要がある新しいエンティティ操作について、構成された永続ストアがポーリングされます。

.NET を使用して関数を作成する場合、エンティティ トリガーは [EntityTriggerAttribute](/dotnet/api/microsoft.azure.webjobs.extensions.durabletask.entitytriggerattribute) .NET 属性を使用して構成されます。

JavaScript、Python、または PowerShell を使用する場合、エンティティ トリガーは、*function.json* の `bindings` 配列の次の JSON オブジェクトによって定義されます。

```json
{
    "name": "<Name of input parameter in function signature>",
    "entityName": "<Optional - name of the entity>",
    "type": "entityTrigger",
    "direction": "in"
}
```

既定では、エンティティの名前は関数の名前です。

### <a name="trigger-behavior"></a>トリガーの動作

エンティティ トリガーに関する注意事項を次に示します。

* **シングルスレッド**: 特定のエンティティに対する操作を処理する場合は、単一のディスパッチャー スレッドが使用されます。 複数のメッセージが同時に 1 つのエンティティに送信される場合、操作は一度に 1 つずつ処理されます。
* **有害メッセージの処理** - エンティティ トリガーには、有害メッセージのサポートはありません。
* **メッセージの可視性** - エンティティ トリガー メッセージはキューから削除され、構成可能な期間にわたって非表示を保持します。 これらのメッセージの可視性は、関数アプリが正常に実行されている限り、自動的に更新されます。
* **戻り値** - エンティティ関数では戻り値をサポートしません。 特定の API を使用することで、状態を保存したり、オーケストレーションに値を渡したりすることができます。

実行中にエンティティに対して行われた状態の変更はいずれも、実行の完了後も自動的に保持されます。

エンティティ トリガーの定義と操作の詳細と例について詳しくは、[持続エンティティ](durable-functions-entities.md)に関するドキュメントをご覧ください。

## <a name="entity-client"></a>エンティティ クライアント

エンティティ クライアント バインディングを使用すると、[エンティティ関数](#entity-trigger)を非同期的にトリガーできます。 これらの関数は、[クライアント関数](durable-functions-types-features-overview.md#client-functions)と呼ばれることもあります。

.NET にプリコンパイル済みの関数を使用する場合は、[DurableClientAttribute](/dotnet/api/microsoft.azure.webjobs.extensions.durabletask.durableclientattribute) .NET 属性を使用してエンティティ クライアントにバインドできます。

> [!NOTE]
> また、`[DurableClientAttribute]` を使用して、[オーケストレーション クライアント](#orchestration-client)にバインドすることもできます。

スクリプト言語 (C# スクリプト、JavaScript、Python など) を開発に使用する場合、エンティティ トリガーは、*function.json* の `bindings` 配列の次の JSON オブジェクトによって定義されます。

```json
{
    "name": "<Name of input parameter in function signature>",
    "taskHub": "<Optional - name of the task hub>",
    "connectionName": "<Optional - name of the connection string app setting>",
    "type": "durableClient",
    "direction": "in"
}
```

* `taskHub` - 複数の関数アプリが同じストレージ アカウントを共有するが、相互に分離する必要があるシナリオで使用されます。 指定されていない場合は、`host.json` の既定値が使用されます。 この値は、ターゲットのエンティティ関数によって使用される値と一致している必要があります。
* `connectionName` - ストレージ アカウント接続文字列を含むアプリ設定の名前。 この接続文字列で表されるストレージ アカウントは、ターゲットのエンティティ関数によって使用されるものと同じにする必要があります。 指定されない場合は、関数アプリの既定のストレージ アカウント接続文字列が使用されます。

> [!NOTE]
> ほとんどの場合、オプションのプロパティを省略し、既定の動作を使用することをお勧めします。

エンティティをクライアントとして操作する方法の詳細と例について詳しくは、[持続エンティティ](durable-functions-entities.md#access-entities)に関するドキュメントをご覧ください。

<a name="host-json"></a>
## <a name="hostjson-settings"></a>host.json 設定

[!INCLUDE [durabletask](../../../includes/functions-host-json-durabletask.md)]

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [インスタンス管理用の組み込みの HTTP API リファレンス](durable-functions-http-api.md)
