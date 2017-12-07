---
title: "Azure Functions における Azure Service Bus のバインド"
description: "Azure Functions で Azure Service Bus トリガーとバインドを使用する方法を説明します。"
services: functions
documentationcenter: na
author: tdykstra
manager: cfowler
editor: 
tags: 
keywords: "Azure Functions, 関数, イベント処理, 動的コンピューティング, サーバーなしのアーキテクチャ"
ms.assetid: daedacf0-6546-4355-a65c-50873e74f66b
ms.service: functions
ms.devlang: multiple
ms.topic: reference
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 04/01/2017
ms.author: tdykstra
ms.openlocfilehash: 6d59b26fa4ab17c17827a8e3450e808e40e5c2dd
ms.sourcegitcommit: 29bac59f1d62f38740b60274cb4912816ee775ea
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/29/2017
---
# <a name="azure-service-bus-bindings-for-azure-functions"></a>Azure Functions における Azure Service Bus のバインド

この記事では、Azure Functions で Azure Service Bus のバインドを操作する方法について説明します。 Azure Functions は、Service Bus のキューおよびトピックのトリガーおよび出力バインドをサポートしています。

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

## <a name="trigger"></a>トリガー

Service Bus トリガーを使用して、Service Bus キューまたはトピックからのメッセージに応答します。 

## <a name="trigger---example"></a>トリガー - 例

言語固有の例をご覧ください。

* [プリコンパイル済み C#](#trigger---c-example)
* [C# スクリプト](#trigger---c-script-example)
* [F#](#trigger---f-example)
* [JavaScript](#trigger---javascript-example)

### <a name="trigger---c-example"></a>トリガー - C# の例

次の例は、Service Bus キュー メッセージを記録する[プリコンパイル済み C# 関数](functions-dotnet-class-library.md)を示しています。

```cs
[FunctionName("ServiceBusQueueTriggerCSharp")]                    
public static void Run(
    [ServiceBusTrigger("myqueue", AccessRights.Manage, Connection = "ServiceBusConnection")] 
    string myQueueItem, 
    TraceWriter log)
{
    log.Info($"C# ServiceBus queue trigger function processed message: {myQueueItem}");
}
```

### <a name="trigger---c-script-example"></a>トリガー - C# スクリプトの例

次の例は、*function.json* ファイルの Service Bus トリガー バインドと、そのバインドが使用される [C# スクリプト関数](functions-reference-csharp.md)を示しています。 この関数は、Service Bus キュー メッセージを記録します。

*function.json* ファイルのバインディング データを次に示します。

```json
{
"bindings": [
    {
    "queueName": "testqueue",
    "connection": "MyServiceBusConnection",
    "name": "myQueueItem",
    "type": "serviceBusTrigger",
    "direction": "in"
    }
],
"disabled": false
}
```

C# スクリプト コードを次に示します。

```cs
public static void Run(string myQueueItem, TraceWriter log)
{
    log.Info($"C# ServiceBus queue trigger function processed message: {myQueueItem}");
}
```

### <a name="trigger---f-example"></a>トリガー - F# の例

次の例は、*function.json* ファイルの Service Bus トリガー バインドと、そのバインドが使用される [F# 関数](functions-reference-fsharp.md)を示しています。 この関数は、Service Bus キュー メッセージを記録します。 

*function.json* ファイルのバインディング データを次に示します。

```json
{
"bindings": [
    {
    "queueName": "testqueue",
    "connection": "MyServiceBusConnection",
    "name": "myQueueItem",
    "type": "serviceBusTrigger",
    "direction": "in"
    }
],
"disabled": false
}
```

F# スクリプト コードを次に示します。

```fsharp
let Run(myQueueItem: string, log: TraceWriter) =
    log.Info(sprintf "F# ServiceBus queue trigger function processed message: %s" myQueueItem)
```

### <a name="trigger---javascript-example"></a>トリガー - JavaScript の例

次の例は、*function.json* ファイルの Service Bus トリガー バインドと、そのバインドが使用される [JavaScript 関数](functions-reference-node.md)を示しています。 この関数は、Service Bus キュー メッセージを記録します。 

*function.json* ファイルのバインディング データを次に示します。

```json
{
"bindings": [
    {
    "queueName": "testqueue",
    "connection": "MyServiceBusConnection",
    "name": "myQueueItem",
    "type": "serviceBusTrigger",
    "direction": "in"
    }
],
"disabled": false
}
```

JavaScript スクリプト コードを次に示します。

```javascript
module.exports = function(context, myQueueItem) {
    context.log('Node.js ServiceBus queue trigger function processed message', myQueueItem);
    context.done();
};
```

## <a name="trigger---attributes"></a>トリガー - 属性

[プリコンパイル済み C#](functions-dotnet-class-library.md) 関数では、次の属性を使用して Service Bus トリガーを構成します。

* NuGet パッケージ [Microsoft.Azure.WebJobs.ServiceBus](http://www.nuget.org/packages/Microsoft.Azure.WebJobs.ServiceBus) で定義された [ServiceBusTriggerAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.ServiceBus/ServiceBusTriggerAttribute.cs)

  属性のコンストラクターは、キューの名前、またはトピックとサブスクリプションを受け取ります。 接続のアクセス権を指定することもできます。 アクセス権を指定しない場合、既定値は `Manage` になります。 アクセス権の設定を選択する方法については、「[トリガー - 構成](#trigger---configuration)」セクションで説明されています。 文字列パラメーターで使用される属性を示す例は次のとおりです。

  ```csharp
  [FunctionName("ServiceBusQueueTriggerCSharp")]                    
  public static void Run(
      [ServiceBusTrigger("myqueue")] string myQueueItem, TraceWriter log)
  {
      ...
  }
  ```

  次の例で示すように、`Connection` プロパティを設定して、使用する Service Bus アカウントを指定できます。

  ```csharp
  [FunctionName("ServiceBusQueueTriggerCSharp")]                    
  public static void Run(
      [ServiceBusTrigger("myqueue", Connection = "ServiceBusConnection")] 
      string myQueueItem, TraceWriter log)
  {
      ...
  }
  ```

  完全な例については、[トリガー - プリコンパイル済み C# の例](#trigger---c-example)に関する記事をご覧ください。

* NuGet パッケージ [Microsoft.Azure.WebJobs.ServiceBus](http://www.nuget.org/packages/Microsoft.Azure.WebJobs.ServiceBus) で定義された [ServiceBusAccountAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.ServiceBus/ServiceBusAccountAttribute.cs)

  使用する Service Bus アカウントを指定する別の方法を示します。 コンストラクターは、Service Bus 接続文字列を含むアプリ設定の名前を受け取ります。 属性は、パラメーター、メソッド、またはクラス レベルで適用できます。 次の例では、クラス レベルとメソッド レベルを示します。

  ```csharp
  [ServiceBusAccount("ClassLevelServiceBusAppSetting")]
  public static class AzureFunctions
  {
      [ServiceBusAccount("MethodLevelServiceBusAppSetting")]
      [FunctionName("ServiceBusQueueTriggerCSharp")]
      public static void Run(
          [ServiceBusTrigger("myqueue", AccessRights.Manage)] 
          string myQueueItem, TraceWriter log)
  {
      ...
  }
  ```

使用する Service Bus アカウントは、次の順序で決定されます。

* `ServiceBusTrigger` 属性の `Connection` プロパティ。
* `ServiceBusTrigger` 属性と同じパラメーターに適用された `ServiceBusAccount` 属性。
* 関数に適用される `ServiceBusAccount` 属性。
* クラスに適用される `ServiceBusAccount` 属性。
* "AzureWebJobsServiceBus" アプリの設定。

## <a name="trigger---configuration"></a>トリガー - 構成

次の表は、*function.json* ファイルと `ServiceBusTrigger` 属性で設定したバインド構成のプロパティを説明しています。

|function.json のプロパティ | 属性のプロパティ |説明|
|---------|---------|----------------------|
|**type** | 該当なし | "serviceBusTrigger" に設定する必要があります。 このプロパティは、Azure Portal でトリガーを作成するときに自動で設定されます。|
|**direction** | 該当なし | "in" に設定する必要があります。 このプロパティは、Azure Portal でトリガーを作成するときに自動で設定されます。 |
|**name** | 該当なし | 関数コード内のキューまたはトピック メッセージを表す変数の名前。 "$return" に設定して、関数の戻り値を参照します。 | 
|**queueName**|**QueueName**|監視するキューの名前。  トピックではなくキューを監視する場合にのみ設定します。
|**topicName**|**TopicName**|監視するトピックの名前。 キューではなくトピックを監視する場合にのみ設定します。|
|**subscriptionName**|**SubscriptionName**|監視するサブスクリプションの名前。 キューではなくトピックを監視する場合にのみ設定します。|
|**connection**|**Connection**|このバインドに使用する Service Bus 接続文字列を含むアプリ設定の名前です。 アプリ設定の名前が "AzureWebJobs" で始まる場合は、名前の残りの部分のみを指定できます。 たとえば、`connection` を "MyServiceBus" に設定した場合、Functions ランタイムは "AzureWebJobsMyServiceBus" という名前のアプリ設定を探します。 `connection` を空のままにした場合、Functions ランタイムは、アプリ設定内の "AzureWebJobsServiceBus" という名前の既定の Service Bus 接続文字列を使用します。<br><br>接続文字列は、「[管理資格情報の取得](../service-bus-messaging/service-bus-dotnet-get-started-with-queues.md#obtain-the-management-credentials)」の手順に従って取得します。 接続文字列は、特定のキューまたはトピックに限らず、Service Bus 名前空間のものである必要があります。 |
|**accessRights**|**Access (アクセス)**|接続文字列のアクセス権。 使用できる値は `manage` と `listen` です。 既定値は `manage` で、`connection` が**管理**アクセス許可を持つことを示します。 **管理**アクセス許可を持たない接続文字列を使用する場合は、`accessRights` を "listen" に設定します。 設定しないと、Functions ランタイムが管理権限を必要とする操作の試行に失敗する可能性があります。|

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="trigger---usage"></a>トリガー - 使用方法

C# と C# スクリプトでは、`string paramName` などのメソッド パラメーターを使用して、キューまたはトピック メッセージにアクセスします。 C# スクリプトでは、`paramName` は *function.json* の `name` プロパティで指定された値です。 `string` の代わりに、次の型を使用することもできます。

* `byte[]` - バイナリ データに有効です。
* カスタム型 - メッセージに JSON が含まれている場合、Azure Functions は JSON データの逆シリアル化を試みます。
* `BrokeredMessage` - [BrokeredMessage.GetBody<T>()](https://msdn.microsoft.com/library/hh144211.aspx) メソッドで逆シリアル化されたメッセージを返します。

JavaScript で、`context.bindings.<name>` を使用して、キューまたはトピック メッセージにアクセスします。 `<name>` は *function.json* の `name` プロパティで指定された値です。 Service Bus メッセージが文字列または JSON オブジェクトとして関数に渡されます。

## <a name="trigger---poison-messages"></a>トリガー - 有害メッセージ

Azure Functions では、有害メッセージの処理を制御することも構成することもできません。 Service Bus は、それ自体が有害なメッセージを処理します。

## <a name="trigger---peeklock-behavior"></a>トリガー - PeekLock 動作

Functions ランタイムは、メッセージを [PeekLock モード](../service-bus-messaging/service-bus-performance-improvements.md#receive-mode)で受信します。 それは、関数が正常に終了した場合はメッセージに対して `Complete` を呼び出し、関数が失敗した場合は `Abandon` を呼び出します。 関数の実行時間が `PeekLock` タイムアウトよりも長くなると、ロックが自動的に更新されます。

## <a name="trigger---hostjson-properties"></a>トリガー - host.json のプロパティ

[host.json](functions-host-json.md#servicebus) ファイルには、Service Bus トリガーの動作を制御する設定が含まれています。

[!INCLUDE [functions-host-json-event-hubs](../../includes/functions-host-json-service-bus.md)]

## <a name="output"></a>出力

キューまたはトピック メッセージを送信するには、Azure Service Bus 出力バインドを使用します。

## <a name="output---example"></a>出力 - 例

言語固有の例をご覧ください。

* [プリコンパイル済み C#](#output---c-example)
* [C# スクリプト](#output---c-script-example)
* [F#](#output---f-example)
* [JavaScript](#output---javascript-example)

### <a name="output---c-example"></a>出力 - C# の例

次の例は、Service Bus キュー メッセージを送信する[プリコンパイル済み C# 関数](functions-dotnet-class-library.md)を示しています。

```cs
[FunctionName("ServiceBusOutput")]
[return: ServiceBus("myqueue", Connection = "ServiceBusConnection")]
public static string ServiceBusOutput([HttpTrigger] dynamic input, TraceWriter log)
{
    log.Info($"C# function processed: {input.Text}");
    return input.Text;
}
```

### <a name="output---c-script-example"></a>出力 - C# スクリプトの例

次の例は、*function.json* ファイルの Service Bus 出力バインドと、そのバインドを使用する [C# スクリプト関数](functions-reference-csharp.md)を示しています。 この関数は、タイマー トリガーを使用して、15 秒ごとにキュー メッセージを送信します。

*function.json* ファイルのバインディング データを次に示します。

```json
{
    "bindings": [
        {
            "schedule": "0/15 * * * * *",
            "name": "myTimer",
            "runsOnStartup": true,
            "type": "timerTrigger",
            "direction": "in"
        },
        {
            "name": "outputSbQueue",
            "type": "serviceBus",
            "queueName": "testqueue",
            "connection": "MyServiceBusConnection",
            "direction": "out"
        }
    ],
    "disabled": false
}
```

単一のメッセージを作成する C# スクリプト コードを次に示します。

```cs
public static void Run(TimerInfo myTimer, TraceWriter log, out string outputSbQueue)
{
    string message = $"Service Bus queue message created at: {DateTime.Now}";
    log.Info(message); 
    outputSbQueue = message;
}
```

複数のメッセージを作成する C# スクリプト コードを次に示します。

```cs
public static void Run(TimerInfo myTimer, TraceWriter log, ICollector<string> outputSbQueue)
{
    string message = $"Service Bus queue messages created at: {DateTime.Now}";
    log.Info(message); 
    outputSbQueue.Add("1 " + message);
    outputSbQueue.Add("2 " + message);
}
```

### <a name="output---f-example"></a>出力 - F# の例

次の例は、*function.json* ファイルの Service Bus 出力バインドと、そのバインドを使用する [F# スクリプト関数](functions-reference-fsharp.md)を示しています。 この関数は、タイマー トリガーを使用して、15 秒ごとにキュー メッセージを送信します。

*function.json* ファイルのバインディング データを次に示します。

```json
{
    "bindings": [
        {
            "schedule": "0/15 * * * * *",
            "name": "myTimer",
            "runsOnStartup": true,
            "type": "timerTrigger",
            "direction": "in"
        },
        {
            "name": "outputSbQueue",
            "type": "serviceBus",
            "queueName": "testqueue",
            "connection": "MyServiceBusConnection",
            "direction": "out"
        }
    ],
    "disabled": false
}
```

単一のメッセージを作成する F# スクリプト コードを次に示します。

```fsharp
let Run(myTimer: TimerInfo, log: TraceWriter, outputSbQueue: byref<string>) =
    let message = sprintf "Service Bus queue message created at: %s" (DateTime.Now.ToString())
    log.Info(message)
    outputSbQueue = message
```

### <a name="output---javascript-example"></a>出力 - JavaScript の例

次の例は、*function.json* ファイルの Service Bus 出力バインドと、そのバインドを使用する [JavaScript スクリプト関数](functions-reference-node.md)を示しています。 この関数は、タイマー トリガーを使用して、15 秒ごとにキュー メッセージを送信します。

*function.json* ファイルのバインディング データを次に示します。

```json
{
    "bindings": [
        {
            "schedule": "0/15 * * * * *",
            "name": "myTimer",
            "runsOnStartup": true,
            "type": "timerTrigger",
            "direction": "in"
        },
        {
            "name": "outputSbQueue",
            "type": "serviceBus",
            "queueName": "testqueue",
            "connection": "MyServiceBusConnection",
            "direction": "out"
        }
    ],
    "disabled": false
}
```

単一のメッセージを作成する JavaScript スクリプト コードを次に示します。

```javascript
module.exports = function (context, myTimer) {
    var message = 'Service Bus queue message created at ' + timeStamp;
    context.log(message);   
    context.bindings.outputSbQueueMsg = message;
    context.done();
};
```

複数のメッセージを作成する JavaScript スクリプト コードを次に示します。

```javascript
module.exports = function (context, myTimer) {
    var message = 'Service Bus queue message created at ' + timeStamp;
    context.log(message);   
    context.bindings.outputSbQueueMsg = [];
    context.bindings.outputSbQueueMsg.push("1 " + message);
    context.bindings.outputSbQueueMsg.push("2 " + message);
    context.done();
};
```

## <a name="output---attributes"></a>出力 - 属性

[プリコンパイル済み C#](functions-dotnet-class-library.md) 関数では、NuGet パッケージ [Microsoft.Azure.WebJobs.ServiceBus](http://www.nuget.org/packages/Microsoft.Azure.WebJobs.ServiceBus) で定義されている [ServiceBusAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.ServiceBus/ServiceBusAttribute.cs) 属性を使用します。

属性のコンストラクターは、キューの名前、またはトピックとサブスクリプションを受け取ります。 接続のアクセス権を指定することもできます。 アクセス権の設定を選択する方法については、「[出力 - 構成](#output---configuration)」セクションで説明されています。 関数の戻り値に適用される属性に適用される属性を示す例は次のとおりです。

```csharp
[FunctionName("ServiceBusOutput")]
[return: ServiceBus("myqueue")]
public static string Run([HttpTrigger] dynamic input, TraceWriter log)
{
    ...
}
```

次の例で示すように、`Connection` プロパティを設定して、使用する Service Bus アカウントを指定できます。

```csharp
[FunctionName("ServiceBusOutput")]
[return: ServiceBus("myqueue", Connection = "ServiceBusConnection")]
public static string Run([HttpTrigger] dynamic input, TraceWriter log)
{
    ...
}
```

完全な例については、[出力 - プリコンパイル済み C# の例](#output---c-example)に関する記事をご覧ください。

`ServiceBusAccount` 属性を使用して、クラス、メソッド、またはパラメーターのレベルで使用する Service Bus アカウントを指定できます。  詳細については、[トリガー - 属性](#trigger---attributes-for-precompiled-c)をご覧ください。

## <a name="output---configuration"></a>出力 - 構成

次の表は、*function.json* ファイルと `ServiceBus` 属性で設定したバインド構成のプロパティを説明しています。

|function.json のプロパティ | 属性のプロパティ |説明|
|---------|---------|----------------------|
|**type** | 該当なし | "serviceBus" に設定する必要があります。 このプロパティは、Azure Portal でトリガーを作成するときに自動で設定されます。|
|**direction** | 該当なし | "out" に設定する必要があります。 このプロパティは、Azure Portal でトリガーを作成するときに自動で設定されます。 |
|**name** | 該当なし | 関数コード内のキューまたはトピックを表す変数の名前。 "$return" に設定して、関数の戻り値を参照します。 | 
|**queueName**|**QueueName**|キューの名前。  トピックではなくキューのメッセージを送信する場合にのみ設定します。
|**topicName**|**TopicName**|監視するトピックの名前。 キューではなくトピックのメッセージを送信する場合にのみ設定します。|
|**subscriptionName**|**SubscriptionName**|監視するサブスクリプションの名前。 キューではなくトピックのメッセージを送信する場合にのみ設定します。|
|**connection**|**Connection**|このバインドに使用する Service Bus 接続文字列を含むアプリ設定の名前です。 アプリ設定の名前が "AzureWebJobs" で始まる場合は、名前の残りの部分のみを指定できます。 たとえば、`connection` を "MyServiceBus" に設定した場合、Functions ランタイムは "AzureWebJobsMyServiceBus" という名前のアプリ設定を探します。 `connection` を空のままにした場合、Functions ランタイムは、アプリ設定内の "AzureWebJobsServiceBus" という名前の既定の Service Bus 接続文字列を使用します。<br><br>接続文字列は、「[管理資格情報の取得](../service-bus-messaging/service-bus-dotnet-get-started-with-queues.md#obtain-the-management-credentials)」の手順に従って取得します。 接続文字列は、特定のキューまたはトピックに限らず、Service Bus 名前空間のものである必要があります。|
|**accessRights**|**Access (アクセス)** |接続文字列のアクセス権。 使用可能な値は、"manage" と "listen" です。 既定値は "manage" です。これは、接続が**管理**アクセス許可を持ってることを示します。 **管理**アクセス許可を持たない接続文字列を使用する場合は、`accessRights` を "listen" に設定します。 設定しないと、Functions ランタイムが管理権限を必要とする操作の試行に失敗する可能性があります。|

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="output---usage"></a>出力 - 使用方法

C# と C# スクリプトでは、`out string paramName` などのメソッド パラメーターを使用して、キューまたはトピックにアクセスします。 C# スクリプトでは、`paramName` は *function.json* の `name` プロパティで指定された値です。 次のようなパラメーターの型を使用することもできます。

* `out T paramName` - `T` は任意の JSON シリアル化可能な型にすることができます。 関数が終了したときにこのパラメーターの値が null の場合、Functions は、null オブジェクトでメッセージを作成します。
* `out string` - 関数が終了したときにパラメーター値が null の場合、Functions はメッセージを作成しません。
* `out byte[]` - 関数が終了したときにパラメーター値が null の場合、Functions はメッセージを作成しません。
* `out BrokeredMessage` - 関数が終了したときにパラメーター値が null の場合、Functions はメッセージを作成しません。

C# または C# スクリプト関数で複数のメッセージを作成するには、`ICollector<T>` または `IAsyncCollector<T>` を使用できます。 `Add` メソッドを呼び出すときに、メッセージが作成されます。

JavaScript で、`context.bindings.<name>` を使用して、キューまたはトピックにアクセスします。 `<name>` は *function.json* の `name` プロパティで指定された値です。 文字列、バイト配列、または (JSON に逆シリアル化された) Javascript オブジェクトを `context.binding.<name>` に割り当てることができます。

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [Azure Functions のトリガーとバインドの詳細情報](functions-triggers-bindings.md)
