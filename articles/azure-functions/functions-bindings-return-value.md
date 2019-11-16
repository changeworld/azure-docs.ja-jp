---
title: Azure 関数からの戻り値の使用
description: Azure Functions の戻り値を管理する方法について学習します
services: functions
documentationcenter: na
author: craigshoemaker
manager: gwallace
ms.service: azure-functions
ms.topic: reference
ms.date: 01/14/2019
ms.author: cshoe
ms.openlocfilehash: 8dd5a4d9d869c879ed402c5450690f0a691e1d2c
ms.sourcegitcommit: a107430549622028fcd7730db84f61b0064bf52f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/14/2019
ms.locfileid: "74074396"
---
# <a name="using-the-azure-function-return-value"></a>Azure 関数の戻り値の使用

この記事では、戻り値が関数内でどのように機能するかについて説明します。

戻り値がある言語では、その戻り値に関数の[出力バインディング](./functions-triggers-bindings.md#binding-direction)をバインドできます。

* C# クラス ライブラリでは、メソッド戻り値に出力バインディング属性を適用します。
* その他の言語では、*function.json* 内の `name` プロパティを `$return` に設定します。

複数の出力バインディングが存在する場合は、そのうちの 1 つにのみ戻り値を使用します。

C# と C# スクリプトでは、`out` パラメーターや[コレクター オブジェクト](functions-reference-csharp.md#writing-multiple-output-values)を使用してデータを出力バインディングに送信できます。

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

出力バインディングに戻り値を使用する C# コードと非同期の例を次に示します。

```cs
[FunctionName("QueueTrigger")]
[return: Blob("output-container/{id}")]
public static string Run([QueueTrigger("inputqueue")]WorkItem input, ILogger log)
{
    string json = string.Format("{{ \"id\": \"{0}\" }}", input.Id);
    log.LogInformation($"C# script processed queue message. Item={json}");
    return json;
}
```

```cs
[FunctionName("QueueTrigger")]
[return: Blob("output-container/{id}")]
public static Task<string> Run([QueueTrigger("inputqueue")]WorkItem input, ILogger log)
{
    string json = string.Format("{{ \"id\": \"{0}\" }}", input.Id);
    log.LogInformation($"C# script processed queue message. Item={json}");
    return Task.FromResult(json);
}
```

# <a name="c-scripttabcsharp-script"></a>[C# スクリプト](#tab/csharp-script)

*function.json* ファイル内の出力バインディングを次に示します。

```json
{
    "name": "$return",
    "type": "blob",
    "direction": "out",
    "path": "output-container/{id}"
}
```

C# スクリプト コードと非同期の例を次に示します。

```cs
public static string Run(WorkItem input, ILogger log)
{
    string json = string.Format("{{ \"id\": \"{0}\" }}", input.Id);
    log.LogInformation($"C# script processed queue message. Item={json}");
    return json;
}
```

```cs
public static Task<string> Run(WorkItem input, ILogger log)
{
    string json = string.Format("{{ \"id\": \"{0}\" }}", input.Id);
    log.LogInformation($"C# script processed queue message. Item={json}");
    return Task.FromResult(json);
}
```

# <a name="ftabfsharp"></a>[F#](#tab/fsharp)

*function.json* ファイル内の出力バインディングを次に示します。

```json
{
    "name": "$return",
    "type": "blob",
    "direction": "out",
    "path": "output-container/{id}"
}
```

F# コードを次に示します。

```fsharp
let Run(input: WorkItem, log: ILogger) =
    let json = String.Format("{{ \"id\": \"{0}\" }}", input.Id)   
    log.LogInformation(sprintf "F# script processed queue message '%s'" json)
    json
```

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

*function.json* ファイル内の出力バインディングを次に示します。

```json
{
    "name": "$return",
    "type": "blob",
    "direction": "out",
    "path": "output-container/{id}"
}
```

JavaScript では、戻り値は `context.done` の 2 番目のパラメーターに入ります。

```javascript
module.exports = function (context, input) {
    var json = JSON.stringify(input);
    context.log('Node.js script processed queue message', json);
    context.done(null, json);
}
```

# <a name="pythontabpython"></a>[Python](#tab/python)

*function.json* ファイル内の出力バインディングを次に示します。

```json
{
    "name": "$return",
    "type": "blob",
    "direction": "out",
    "path": "output-container/{id}"
}
```
Python コードを次に示します。

```python
def main(input: azure.functions.InputStream) -> str:
    return json.dumps({
        'name': input.name,
        'length': input.length,
        'content': input.read().decode('utf-8')
    })
```

---

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [Azure Functions のバインド エラーの処理](./functions-bindings-errors.md)
