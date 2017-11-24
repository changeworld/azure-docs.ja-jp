---
title: "Azure Functions における Queue Storage バインド"
description: "Azure Functions で Azure Queue Storage トリガーと出力バインドを使用する方法について説明します。"
services: functions
documentationcenter: na
author: ggailey777
manager: cfowler
editor: 
tags: 
keywords: "Azure Functions, 関数, イベント処理, 動的コンピューティング, サーバーなしのアーキテクチャ"
ms.service: functions
ms.devlang: multiple
ms.topic: reference
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 10/23/2017
ms.author: glenga
ms.openlocfilehash: 9cf506d571c8d67a1e48ce34860db3dbc3445509
ms.sourcegitcommit: bc8d39fa83b3c4a66457fba007d215bccd8be985
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/10/2017
---
# <a name="azure-functions-queue-storage-bindings"></a>Azure Functions における Queue Storage バインド

この記事では、Azure Functions で Azure Queue Storage のバインドを使用する方法について説明します。 Azure Functions は、キューのトリガーと出力バインドをサポートしています。

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

## <a name="queue-storage-trigger"></a>Queue ストレージ トリガー

キュー トリガーを使用して、キューで新しい項目を受け取ったときに関数を開始します。 キュー メッセージは、関数への入力として提供されます。

## <a name="trigger---example"></a>トリガー - 例

言語固有の例をご覧ください。

* [プリコンパイル済み C#](#trigger---c-example)
* [C# スクリプト](#trigger---c-script-example)
* [JavaScript](#trigger---javascript-example)

### <a name="trigger---c-example"></a>トリガー - C# の例

次の例は、キュー項目が処理されるたびに `myqueue-items` キューをポーリングし、ログを書き込む[プリコンパイル済み C#](functions-dotnet-class-library.md) コードを示しています。

```csharp
public static class QueueFunctions
{
    [FunctionName("QueueTrigger")]
    public static void QueueTrigger(
        [QueueTrigger("myqueue-items")] string myQueueItem, 
        TraceWriter log)
    {
        log.Info($"C# function processed: {myQueueItem}");
    }
}
```

### <a name="trigger---c-script-example"></a>トリガー - C# スクリプトの例

次の例は、*function.json* ファイルの BLOB トリガー バインドと、バインドを使用する [C# スクリプト](functions-reference-csharp.md) コードを示しています。 この関数は、キュー項目が処理されるたびに `myqueue-items` キューをポーリングし、ログを書き込みます。

*function.json* ファイルを次に示します。

```json
{
    "disabled": false,
    "bindings": [
        {
            "type": "queueTrigger",
            "direction": "in",
            "name": "myQueueItem",
            "queueName": "myqueue-items",
            "connection":"MyStorageConnectionAppSetting"
        }
    ]
}
```

これらのプロパティについては、「[構成](#trigger---configuration)」セクションを参照してください。

C# スクリプト コードを次に示します。

```csharp
#r "Microsoft.WindowsAzure.Storage"

using Microsoft.WindowsAzure.Storage.Queue;
using System;

public static void Run(CloudQueueMessage myQueueItem, 
    DateTimeOffset expirationTime, 
    DateTimeOffset insertionTime, 
    DateTimeOffset nextVisibleTime,
    string queueTrigger,
    string id,
    string popReceipt,
    int dequeueCount,
    TraceWriter log)
{
    log.Info($"C# Queue trigger function processed: {myQueueItem.AsString}\n" +
        $"queueTrigger={queueTrigger}\n" +
        $"expirationTime={expirationTime}\n" +
        $"insertionTime={insertionTime}\n" +
        $"nextVisibleTime={nextVisibleTime}\n" +
        $"id={id}\n" +
        $"popReceipt={popReceipt}\n" + 
        $"dequeueCount={dequeueCount}");
}
```

function.json の `name` プロパティで名前が指定された `myQueueItem` については、「[使用方法](#trigger---usage)」セクションを参照してください。  ここに表示されているその他すべての変数については、「[メッセージのメタデータ](#trigger---message-metadata)」セクションを参照してください。

### <a name="trigger---javascript-example"></a>トリガー - JavaScript の例

次の例は、*function.json* ファイルの BLOB トリガー バインドと、そのバインドを使用する [JavaScript 関数](functions-reference-node.md)を示しています。 この関数は、キュー項目が処理されるたびに `myqueue-items` キューをポーリングし、ログを書き込みます。

*function.json* ファイルを次に示します。

```json
{
    "disabled": false,
    "bindings": [
        {
            "type": "queueTrigger",
            "direction": "in",
            "name": "myQueueItem",
            "queueName": "myqueue-items",
            "connection":"MyStorageConnectionAppSetting"
        }
    ]
}
```

これらのプロパティについては、「[構成](#trigger---configuration)」セクションを参照してください。

JavaScript コードを次に示します。

```javascript
module.exports = function (context) {
    context.log('Node.js queue trigger function processed work item', context.bindings.myQueueItem);
    context.log('queueTrigger =', context.bindingData.queueTrigger);
    context.log('expirationTime =', context.bindingData.expirationTime);
    context.log('insertionTime =', context.bindingData.insertionTime);
    context.log('nextVisibleTime =', context.bindingData.nextVisibleTime);
    context.log('id =', context.bindingData.id);
    context.log('popReceipt =', context.bindingData.popReceipt);
    context.log('dequeueCount =', context.bindingData.dequeueCount);
    context.done();
};
```

function.json の `name` プロパティで名前が指定された `myQueueItem` については、「[使用方法](#trigger---usage)」セクションを参照してください。  ここに表示されているその他すべての変数については、「[メッセージのメタデータ](#trigger---message-metadata)」セクションを参照してください。

## <a name="trigger---attributes-for-precompiled-c"></a>トリガー - プリコンパイル済み C# の属性
 
[プリコンパイル済み C#](functions-dotnet-class-library.md) 関数では、次の属性を使用してキュー トリガーを構成します。

* NuGet パッケージ [Microsoft.Azure.WebJobs](http://www.nuget.org/packages/Microsoft.Azure.WebJobs) で定義された [QueueTriggerAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/QueueTriggerAttribute.cs)

  次の例のように、属性のコンストラクターは監視するキューの名前を受け取ります。

  ```csharp
  [FunctionName("QueueTrigger")]
  public static void Run(
      [QueueTrigger("myqueue-items")] string myQueueItem, 
      TraceWriter log)
  ```

  次の例で示すように、`Connection` プロパティを設定して、使用するストレージ アカウントを指定できます。

  ```csharp
  [FunctionName("QueueTrigger")]
  public static void Run(
      [QueueTrigger("myqueue-items", Connection = "StorageConnectionAppSetting")] string myQueueItem, 
      TraceWriter log)
  ```
 
* NuGet パッケージ [Microsoft.Azure.WebJobs](http://www.nuget.org/packages/Microsoft.Azure.WebJobs) で定義された [StorageAccountAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/StorageAccountAttribute.cs)

  使用するストレージ アカウントを指定する別の方法を提供します。 コンストラクターは、ストレージ接続文字列を含むアプリ設定の名前を受け取ります。 属性は、パラメーター、メソッド、またはクラス レベルで適用できます。 次の例では、クラス レベルとメソッド レベルを示します。

  ```csharp
  [StorageAccount("ClassLevelStorageAppSetting")]
  public static class AzureFunctions
  {
      [FunctionName("QueueTrigger")]
      [StorageAccount("FunctionLevelStorageAppSetting")]
      public static void Run( //...
  ```

使用するストレージ アカウントは、次の順序で決定されます。

* `QueueTrigger` 属性の `Connection` プロパティ。
* `QueueTrigger` 属性と同じパラメーターに適用された `StorageAccount` 属性。
* 関数に適用される `StorageAccount` 属性。
* クラスに適用される `StorageAccount` 属性。
* "AzureWebJobsStorage" アプリ設定。

## <a name="trigger---configuration"></a>トリガー - 構成

次の表は、*function.json* ファイルと `QueueTrigger` 属性で設定したバインド構成のプロパティを説明しています。

|function.json のプロパティ | 属性のプロパティ |Description|
|---------|---------|----------------------|
|**type** | 該当なし| `queueTrigger` に設定する必要があります。 このプロパティは、Azure Portal でトリガーを作成するときに自動で設定されます。|
|**direction**| 該当なし | *function.json* ファイルの場合のみ。 `in` に設定する必要があります。 このプロパティは、Azure Portal でトリガーを作成するときに自動で設定されます。 |
|**name** | 該当なし |関数コード内のキューを表す変数の名前。  | 
|**queueName** | **QueueName**| ポーリングするキューの名前。 | 
|**connection** | **Connection** |このバインドに使用するストレージ接続文字列を含むアプリ設定の名前です。 アプリ設定の名前が "AzureWebJobs" で始まる場合は、ここで名前の残りの部分のみを指定できます。 たとえば、`connection` を "MyStorage" に設定した場合、Functions ランタイムは "AzureWebJobsMyStorage" という名前のアプリ設定を探します。 `connection` を空のままにした場合、Functions ランタイムは、アプリ設定内の `AzureWebJobsStorage` という名前の既定のストレージ接続文字列を使用します。<br/>ローカルで開発している場合、アプリ設定は [local.settings.json ファイル](functions-run-local.md#local-settings-file)の値になります。|

## <a name="trigger---usage"></a>トリガー - 使用方法
 
C# および C# スクリプトでは、`Stream paramName` のようなメソッド パラメーターを使用して BLOB データにアクセスします。 C# スクリプトでは、`paramName` は *function.json* の `name` プロパティで指定された値です。 次の型のいずれにでもバインドできます。

* POCO オブジェクト - Functions ランタイムは、JSON ペイロードを POCO オブジェクトに逆シリアル化します。 
* `string`
* `byte[]`
* [CloudQueueMessage]

JavaScript の場合、`context.bindings.<name>` を使用してキュー項目ペイロードにアクセスします。 ペイロードが JSON の場合は、オブジェクトに逆シリアル化されます。

## <a name="trigger---message-metadata"></a>トリガー - メッセージのメタデータ

キュー トリガーは、いくつかのメタデータ プロパティを提供します。 これらのプロパティは、他のバインドのバインド式の一部として、またはコードのパラメーターとして使用できます。 値は、[CloudQueueMessage](https://docs.microsoft.com/dotnet/api/microsoft.windowsazure.storage.queue.cloudqueuemessage) と同じセマンティクスを持ちます。

|プロパティ|型|Description|
|--------|----|-----------|
|`QueueTrigger`|`string`|キュー ペイロード (有効な文字列の場合)。 キュー メッセージ ペイロードが文字列の場合、`QueueTrigger` は、*function.json* の `name` プロパティで指定された変数と同じ値になります。|
|`DequeueCount`|`int`|このメッセージがデキューされた回数。|
|`ExpirationTime`|`DateTimeOffset?`|メッセージが期限切れになる時刻。|
|`Id`|`string`|キュー メッセージ ID。|
|`InsertionTime`|`DateTimeOffset?`|メッセージがキューに追加された時刻。|
|`NextVisibleTime`|`DateTimeOffset?`|メッセージが次に表示される時刻。|
|`PopReceipt`|`string`|メッセージのポップ受信。|

## <a name="trigger---poison-messages"></a>トリガー - 有害メッセージ

キュー トリガー関数が失敗すると、Azure Functions は、その関数を特定のキュー メッセージに対して (最初の試行を含め) 最大 5 回再試行します。 試行が 5 回とも失敗した場合、Functions ランタイム は、*&lt;originalqueuename>-poison* という名前のキューにメッセージを追加します。 メッセージのログを取得するか、手動での対処が必要であるという通知を送信することにより有害キューからのメッセージを処理する関数が記述できます。

有害メッセージを手動で処理するには、キュー メッセージの [dequeueCount](#trigger---message-metadata) を確認します。

## <a name="trigger---hostjson-properties"></a>トリガー - host.json のプロパティ

[host.json](functions-host-json.md#queues) ファイルには、キュー トリガーの動作を制御する設定が含まれています。

[!INCLUDE [functions-host-json-queues](../../includes/functions-host-json-queues.md)]

## <a name="queue-storage-output-binding"></a>キュー ストレージの出力バインド

Azure Queue Storage の出力バインドを使用して、キューにメッセージを書き込みます。

## <a name="output---example"></a>出力 - 例

言語固有の例をご覧ください。

* [プリコンパイル済み C#](#output---c-example)
* [C# スクリプト](#output---c-script-example)
* [JavaScript](#output---javascript-example)

### <a name="output---c-example"></a>出力 - C# の例

次の例は、受け取った HTTP 要求ごとにキュー メッセージを作成する[プリコンパイル済み C#](functions-dotnet-class-library.md) コードを示します。

```csharp
[StorageAccount("AzureWebJobsStorage")]
public static class QueueFunctions
{
    [FunctionName("QueueOutput")]
    [return: Queue("myqueue-items")]
    public static string QueueOutput([HttpTrigger] dynamic input,  TraceWriter log)
    {
        log.Info($"C# function processed: {input.Text}");
        return input.Text;
    }
}
```

### <a name="output---c-script-example"></a>出力 - C# スクリプトの例

次の例は、*function.json* ファイルの BLOB トリガー バインドと、バインドを使用する [C# スクリプト](functions-reference-csharp.md) コードを示しています。 この関数では、受け取った HTTP 要求ごとに POCO ペイロードを使用してキュー項目を作成します。

*function.json* ファイルを次に示します。

```json
{
  "bindings": [
    {
      "type": "httpTrigger",
      "direction": "in",
      "authLevel": "function",
      "name": "input"
    },
    {
      "type": "http",
      "direction": "out",
      "name": "return"
    },
    {
      "type": "queue",
      "direction": "out",
      "name": "$return",
      "queueName": "outqueue",
      "connection": "MyStorageConnectionAppSetting",
    }
  ]
}
``` 

これらのプロパティについては、「[構成](#output---configuration)」セクションを参照してください。

単一のキュー メッセージを作成する C# スクリプト コードを次に示します。

```cs
public class CustomQueueMessage
{
    public string PersonName { get; set; }
    public string Title { get; set; }
}

public static CustomQueueMessage Run(CustomQueueMessage input, TraceWriter log)
{
    return input;
}
```

一度で複数のメッセージを送信するには、`ICollector` または `IAsyncCollector` パラメーターを使用します。 HTTP 要求データを含む 1 つのメッセージと、ハードコーディングされた値を含む 1 つのメッセージという、複数のメッセージを送信する C# スクリプト コードを次に示します。

```cs
public static void Run(
    CustomQueueMessage input, 
    ICollector<CustomQueueMessage> myQueueItem, 
    TraceWriter log)
{
    myQueueItem.Add(input);
    myQueueItem.Add(new CustomQueueMessage { PersonName = "You", Title = "None" });
}
```

### <a name="output---javascript-example"></a>出力 - JavaScript の例

次の例は、*function.json* ファイルの BLOB トリガー バインドと、そのバインドを使用する [JavaScript 関数](functions-reference-node.md)を示しています。 この関数では、受け取った HTTP 要求ごとにキュー項目を作成します。

*function.json* ファイルを次に示します。

```json
{
  "bindings": [
    {
      "type": "httpTrigger",
      "direction": "in",
      "authLevel": "function",
      "name": "input"
    },
    {
      "type": "http",
      "direction": "out",
      "name": "return"
    },
    {
      "type": "queue",
      "direction": "out",
      "name": "$return",
      "queueName": "outqueue",
      "connection": "MyStorageConnectionAppSetting",
    }
  ]
}
``` 

これらのプロパティについては、「[構成](#output---configuration)」セクションを参照してください。

JavaScript コードを次に示します。

```javascript
module.exports = function (context, input) {
    context.done(null, input.body);
};
```

一度で複数のメッセージを送信するには、`myQueueItem` 出力バインドのメッセージ配列を定義します。 次の JavaScript コードは、受け取った HTTP 要求ごとにハードコーディングされた値を含む 2 つのキュー メッセージを送信します。

```javascript
module.exports = function(context) {
    context.bindings.myQueueItem = ["message 1","message 2"];
    context.done();
};
```

## <a name="output---attributes-for-precompiled-c"></a>出力 - プリコンパイル済み C# の属性
 
[プリコンパイル済み C#](functions-dotnet-class-library.md) 関数では、NuGet パッケージ [Microsoft.Azure.WebJobs](http://www.nuget.org/packages/Microsoft.Azure.WebJobs) で定義されている [QueueAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/QueueAttribute.cs) を使用します。

この属性は、`out` パラメーターまたは関数の戻り値に適用されます。 次の例のように、属性のコンストラクターはキューの名前を受け取ります。

```csharp
[FunctionName("QueueOutput")]
[return: Queue("myqueue-items")]
public static string Run([HttpTrigger] dynamic input,  TraceWriter log)
```

次の例で示すように、`Connection` プロパティを設定して、使用するストレージ アカウントを指定できます。

```csharp
[FunctionName("QueueOutput")]
[return: Queue("myqueue-items, Connection = "StorageConnectionAppSetting")]
public static string Run([HttpTrigger] dynamic input,  TraceWriter log)
```

`StorageAccount` 属性を使用して、クラス、メソッド、またはパラメーターのレベルでストレージ アカウントを指定できます。 詳細については、「[トリガー - プリコンパイル済み C# の属性](#trigger---attributes-for-precompiled-c)」を参照してください。

## <a name="output---configuration"></a>出力 - 構成

次の表は、*function.json* ファイルと `Queue` 属性で設定したバインド構成のプロパティを説明しています。

|function.json のプロパティ | 属性のプロパティ |Description|
|---------|---------|----------------------|
|**type** | 該当なし | `queue` に設定する必要があります。 このプロパティは、Azure Portal でトリガーを作成するときに自動で設定されます。|
|**direction** | 該当なし | `out` に設定する必要があります。 このプロパティは、Azure Portal でトリガーを作成するときに自動で設定されます。 |
|**name** | 該当なし | 関数コード内のキューを表す変数の名前。 `$return` に設定して、関数の戻り値を参照します。| 
|**queueName** |**QueueName** | キューの名前。 | 
|**connection** | **Connection** |このバインドに使用するストレージ接続文字列を含むアプリ設定の名前です。 アプリ設定の名前が "AzureWebJobs" で始まる場合は、ここで名前の残りの部分のみを指定できます。 たとえば、`connection` を "MyStorage" に設定した場合、Functions ランタイムは "AzureWebJobsMyStorage" という名前のアプリ設定を探します。 `connection` を空のままにした場合、Functions ランタイムは、アプリ設定内の `AzureWebJobsStorage` という名前の既定のストレージ接続文字列を使用します。<br>ローカルで開発している場合、アプリ設定は [local.settings.json ファイル](functions-run-local.md#local-settings-file)の値になります。|

## <a name="output---usage"></a>出力 - 使用方法
 
C# と C# スクリプトで単一のキュー メッセージを書き込むには、`out T paramName` などのメソッドのパラメーターを使用します。 C# スクリプトでは、`paramName` は *function.json* の `name` プロパティで指定された値です。 `out` パラメーターではなくメソッドの戻り値の型を使用することができます。`T` は次に示すいずれかの型の場合があります。

* JSON としてシリアル化可能な POCO
* `string`
* `byte[]`
* [CloudQueueMessage] 

C# と C# スクリプトで複数のキュー メッセージを書き込むには、次のいずれかの型を使用します。 

* `ICollector<T>` または `IAsyncCollector<T>`
* [CloudQueue](/dotnet/api/microsoft.windowsazure.storage.queue.cloudqueue)

JavaScript 関数の場合は、`context.bindings.<name>` を使用して出力キュー メッセージにアクセスします。 キュー項目ペイロードには、文字列または JSON のシリアル化可能なオブジェクトを使用できます。

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [Queue Storage のトリガーを使用するクイック スタートに進む](functions-create-storage-queue-triggered-function.md)

> [!div class="nextstepaction"]
> [Queue Storage 出力バインドを使用するチュートリアルに進む](functions-integrate-storage-queue-output-binding.md)

> [!div class="nextstepaction"]
> [Azure Functions のトリガーとバインドの詳細情報](functions-triggers-bindings.md)

<!-- LINKS -->

[CloudQueueMessage]: /dotnet/api/microsoft.windowsazure.storage.queue.cloudqueuemessage