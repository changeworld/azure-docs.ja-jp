---
title: Azure Functions における RabbitMQ の出力バインド
description: Azure Functions から RabbitMQ メッセージを送信する方法について説明します。
author: cachai2
ms.assetid: ''
ms.topic: reference
ms.date: 12/13/2020
ms.author: cachai
ms.custom: ''
ms.openlocfilehash: 212bfcee09cd63b6ff09faaba4d99e4b4c583fe8
ms.sourcegitcommit: 2ba6303e1ac24287762caea9cd1603848331dd7a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/15/2020
ms.locfileid: "97505679"
---
# <a name="rabbitmq-output-binding-for-azure-functions-overview"></a>Azure Functions における RabbitMQ の出力バインドの概要

> [!NOTE]
> RabbitMQ のバインドは **Windows Premium** プランでのみ完全にサポートされています。 現在、従量課金と Linux はサポートされていません。

RabbitMQ 出力バインドを使用して、RabbitMQ キューにメッセージを送信します。

セットアップと構成の詳細については、[概要](functions-bindings-rabbitmq-output.md)に関する記事を参照してください。

## <a name="example"></a>例

# <a name="c"></a>[C#](#tab/csharp)

次の例は、メソッドの戻り値を出力として使用して 5 分ごとに TimerTrigger によってトリガーされたときに RabbitMQ メッセージを送信する [C# 関数](functions-dotnet-class-library.md)を示しています。

```cs
[FunctionName("RabbitMQOutput")]
[return: RabbitMQ("outputQueue", ConnectionStringSetting = "ConnectionStringSetting")]
public static string Run([TimerTrigger("0 */5 * * * *")] TimerInfo myTimer, ILogger log)
{
    log.LogInformation($"C# Timer trigger function executed at: {DateTime.Now}");
    return $"{DateTime.Now}";
}
```

次の例は、IAsyncCollector インターフェイスを使用してメッセージを送信する方法を示しています。

```cs
[FunctionName("RabbitMQOutput")]
public static async Task Run(
[RabbitMQTrigger("sourceQueue", ConnectionStringSetting = "TriggerConnectionString")] string rabbitMQEvent,
[RabbitMQ("destinationQueue", ConnectionStringSetting = "OutputConnectionString")]IAsyncCollector<string> outputEvents,
ILogger log)
{
    // processing:
    var myProcessedEvent = DoSomething(rabbitMQEvent);
    
     // send the message
    await outputEvents.AddAsync(JsonConvert.SerializeObject(myProcessedEvent));
}
```

次の例は、メッセージを POCO として送信する方法を示しています。

```cs
public class TestClass
{
    public string x { get; set; }
}

[FunctionName("RabbitMQOutput")]
public static async Task Run(
[RabbitMQTrigger("sourceQueue", ConnectionStringSetting = "TriggerConnectionString")] TestClass rabbitMQEvent,
[RabbitMQ("destinationQueue", ConnectionStringSetting = "OutputConnectionString")]IAsyncCollector<TestClass> outputPocObj,
ILogger log)
{
    // send the message
    await outputPocObj.Add(rabbitMQEvent);
}
```

# <a name="c-script"></a>[C# スクリプト](#tab/csharp-script)

次の例は、*function.json* ファイルの RabbitMQ 出力バインドと、そのバインドを使用する [C# スクリプト関数](functions-reference-csharp.md)を示しています。 この関数は、HTTP トリガーからメッセージを読み取り、RabbitMQ キューに出力します。

*function.json* ファイルのバインディング データを次に示します。

```json
{
    "bindings": [
        {
            "type": "httpTrigger",
            "direction": "in",
            "authLevel": "function",
            "name": "input",
            "methods": [
                "get",
                "post"
            ]
        },
        {
            "type": "rabbitMQ",
            "name": "outputMessage",
            "queueName": "outputQueue",
            "connectionStringSetting": "connectionStringAppSetting",
            "direction": "out"
        }
    ]
}
```

C# スクリプト コードを次に示します。

```csx
using System;
using Microsoft.Extensions.Logging;

public static void Run(string input, out string outputMessage, ILogger log)
{
    log.LogInformation(input);
    outputMessage = input;
}
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

次の例は、*function.json* ファイルの RabbitMQ 出力バインドと、そのバインドを使用する [JavaScript 関数](functions-reference-node.md)を示しています。 この関数は、HTTP トリガーからメッセージを読み取り、RabbitMQ キューに出力します。

*function.json* ファイルのバインディング データを次に示します。

```json
{
    "bindings": [
        {
            "type": "httpTrigger",
            "direction": "in",
            "authLevel": "function",
            "name": "input",
            "methods": [
                "get",
                "post"
            ]
        },
        {
            "type": "rabbitMQ",
            "name": "outputMessage",
            "queueName": "outputQueue",
            "connectionStringSetting": "connectionStringAppSetting",
            "direction": "out"
        }
    ]
}
```

JavaScript コードを次に示します。

```javascript
module.exports = function (context, input) {
    context.bindings.myQueueItem = input.body;
    context.done();
};
```

# <a name="python"></a>[Python](#tab/python)

次の例は、*function.json* ファイルの RabbitMQ 出力バインドと、そのバインドを使用する Python 関数を示しています。 この関数は、HTTP トリガーからメッセージを読み取り、RabbitMQ キューに出力します。

*function.json* ファイルのバインディング データを次に示します。

```json
{
    "scriptFile": "__init__.py",
    "bindings": [
        {
            "authLevel": "function",
            "type": "httpTrigger",
            "direction": "in",
            "name": "req",
            "methods": [
                "get",
                "post"
            ]
        },
        {
            "type": "http",
            "direction": "out",
            "name": "$return"
        },
        {
            "type": "rabbitMQ",
            "name": "outputMessage",
            "queueName": "outputQueue",
            "connectionStringSetting": "connectionStringAppSetting",
            "direction": "out"
        }
    ]
}
```

*_\_init_\_.py* で、値を `set` メソッドに渡すことでメッセージをキューに書き出すことができます。

```python
import azure.functions as func

def main(req: func.HttpRequest, msg: func.Out[str]) -> func.HttpResponse:
    input_msg = req.params.get('message')
    msg.set(input_msg)
    return 'OK'
```

# <a name="java"></a>[Java](#tab/java)

次の例は、5 分ごとに TimerTrigger によってトリガーされたときに RabbitMQ キューにメッセージを送信する Java 関数を示しています。

```java
@FunctionName("RabbitMQOutputExample")
public void run(
@TimerTrigger(name = "keepAliveTrigger", schedule = "0 */5 * * * *") String timerInfo,
@RabbitMQOutput(connectionStringSetting = "rabbitMQ", queueName = "hello") OutputBinding<String> output,
final ExecutionContext context) {
    output.setValue("Some string");
}
```

---

## <a name="attributes-and-annotations"></a>属性と注釈

# <a name="c"></a>[C#](#tab/csharp)

[C# クラス ライブラリ](functions-dotnet-class-library.md)では、[RabbitMQAttribute](https://github.com/Azure/azure-functions-rabbitmq-extension/blob/dev/src/RabbitMQAttribute.cs) を使用します。

メソッド シグネチャでの `RabbitMQAttribute` 属性を次に示します。

```csharp
[FunctionName("RabbitMQOutput")]
public static async Task Run(
[RabbitMQTrigger("SourceQueue", ConnectionStringSetting = "TriggerConnectionString")] string rabbitMQEvent,
[RabbitMQ("DestinationQueue", ConnectionStringSetting = "OutputConnectionString")]IAsyncCollector<string> outputEvents,
ILogger log)
{
    ...
}
```

完全な例については、C# の[例](#example)を参照してください。

# <a name="c-script"></a>[C# スクリプト](#tab/csharp-script)

属性は、C# スクリプトではサポートされていません。

# <a name="javascript"></a>[JavaScript](#tab/javascript)

属性は、JavaScript ではサポートされていません。

# <a name="python"></a>[Python](#tab/python)

属性は、Python ではサポートされていません。

# <a name="java"></a>[Java](#tab/java)

`RabbitMQOutput` 注釈を使用すると、RabbitMQ メッセージの送信時に実行される関数を作成できます。 使用可能な構成オプションには、キュー名と接続文字列名があります。 パラメーターの詳細については、[RabbitMQOutput Java 注釈](https://github.com/Azure/azure-functions-rabbitmq-extension/blob/dev/binding-library/java/src/main/java/com/microsoft/azure/functions/rabbitmq/annotation/RabbitMQOutput.java)に関するページを参照してください。

詳細については、出力バインドの[例](#example)を参照してください。

---

## <a name="configuration"></a>構成

次の表は、*function.json* ファイルと `RabbitMQ` 属性で設定したバインド構成のプロパティを説明しています。

|function.json のプロパティ | 属性のプロパティ |説明|
|---------|---------|----------------------|
|**type** | 該当なし | "RabbitMQ" に設定する必要があります。|
|**direction** | 該当なし | "out" に設定する必要があります。 |
|**name** | 該当なし | 関数コード内のキューを表す変数の名前。 |
|**queueName**|**QueueName**| メッセージの送信先となるキューの名前。 |
|**hostName**|**HostName**|(ConnectStringSetting を使用する場合は省略可能) <br>キューのホスト名 (例:10.26.45.210)|
|**userNameSetting**|**UserNameSetting**|(ConnectionStringSetting を使用している場合は省略可能) <br>キューにアクセスするための名前 |
|**passwordSetting**|**PasswordSetting**|(ConnectionStringSetting を使用している場合は省略可能) <br>キューにアクセスするためのパスワード|
|**connectionStringSetting**|**ConnectionStringSetting**|RabbitMQ メッセージ キュー接続文字列を含むアプリ設定の名前。 接続文字列は、local.settings.json のアプリ設定ではなく、直接指定した場合、トリガーは機能しないことに注意してください。 (例:*function.json* の場合: connectionStringSetting: "rabbitMQConnection" <br> *local.settings.json* の場合: "rabbitMQConnection" : "< ActualConnectionstring >")|
|**port**|**[ポート]**|使用されているポートを取得または設定します。 既定値は 0 です。|

## <a name="usage"></a>使用法

# <a name="c"></a>[C#](#tab/csharp)

出力バインドには、次のパラメーターの型を使用します。

* `byte[]` - 関数が終了したときにパラメーター値が null の場合、Functions はメッセージを作成しません。
* `string` - 関数が終了したときにパラメーター値が null の場合、Functions はメッセージを作成しません。
* `POCO` - パラメーター値が C# オブジェクトとして書式設定されていない場合は、エラーが発生します。 完全な例については、C# の[例](#example)を参照してください。

C# 関数を使用する場合:

* 非同期関数には、`out` パラメーターの代わりに戻り値または `IAsyncCollector` が必要です。

# <a name="c-script"></a>[C# スクリプト](#tab/csharp-script)

出力バインドには、次のパラメーターの型を使用します。

* `byte[]` - 関数が終了したときにパラメーター値が null の場合、Functions はメッセージを作成しません。
* `string` - 関数が終了したときにパラメーター値が null の場合、Functions はメッセージを作成しません。
* `POCO` - パラメーター値が C# オブジェクトとして書式設定されていない場合は、エラーが発生します。

C# スクリプト関数を使用する場合:

* 非同期関数には、`out` パラメーターの代わりに戻り値または `IAsyncCollector` が必要です。

# <a name="javascript"></a>[JavaScript](#tab/javascript)

RabbitMQ メッセージは、文字列を介して送信されます。

# <a name="python"></a>[Python](#tab/python)

RabbitMQ メッセージは、文字列を介して送信されます。

# <a name="java"></a>[Java](#tab/java)

出力バインドには、次のパラメーターの型を使用します。

* `byte[]` - 関数が終了したときにパラメーター値が null の場合、Functions はメッセージを作成しません。
* `string` - 関数が終了したときにパラメーター値が null の場合、Functions はメッセージを作成しません。
* `POJO` - パラメーター値が Java オブジェクトとして書式設定されていない場合は、エラーが発生します。

---

## <a name="next-steps"></a>次の手順

- [RabbitMQ メッセージの作成時に関数を実行する (トリガー)](./functions-bindings-rabbitmq-trigger.md)
