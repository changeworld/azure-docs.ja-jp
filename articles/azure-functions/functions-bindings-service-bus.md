---
title: Azure Functions における Azure Service Bus のバインド
description: Azure Functions で Azure Service Bus トリガーとバインドを使用する方法を説明します。
author: craigshoemaker
ms.assetid: daedacf0-6546-4355-a65c-50873e74f66b
ms.topic: reference
ms.date: 04/01/2017
ms.author: cshoe
ms.openlocfilehash: 424d797410c091dc53687284c2b32e2f1f0358e1
ms.sourcegitcommit: 87781a4207c25c4831421c7309c03fce5fb5793f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/23/2020
ms.locfileid: "76549072"
---
# <a name="azure-service-bus-bindings-for-azure-functions"></a>Azure Functions における Azure Service Bus のバインド

この記事では、Azure Functions で Azure Service Bus のバインドを連携する方法について説明します。 Azure Functions は、Service Bus のキューおよびトピックのトリガーおよび出力バインドをサポートしています。

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

## <a name="packages---functions-1x"></a>パッケージ - Functions 1.x

Service Bus のバインドは [Microsoft.Azure.WebJobs.ServiceBus](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.ServiceBus) NuGet パッケージ、バージョン 2.x で提供されます。 

[!INCLUDE [functions-package](../../includes/functions-package.md)]

## <a name="packages---functions-2x-and-higher"></a>パッケージ - Functions 2.x 以降

Service Bus のバインドは [Microsoft.Azure.WebJobs.Extensions.ServiceBus](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.ServiceBus) NuGet パッケージ、バージョン 3.x で提供されます。 パッケージのソース コードは、[azure-functions-servicebus-extension](https://github.com/Azure/azure-functions-servicebus-extension) GitHub リポジトリにあります。

> [!NOTE]
> バージョン 2.x 以降では、`ServiceBusTrigger` インスタンスで構成されるトピックまたはサブスクリプションは作成されません。 これらのバージョンは、キューの管理を処理しない [Microsoft.Azure.ServiceBus](https://www.nuget.org/packages/Microsoft.Azure.ServiceBus) に基づいています。

[!INCLUDE [functions-package-v2](../../includes/functions-package-v2.md)]

## <a name="trigger"></a>トリガー

Service Bus トリガーを使用して、Service Bus キューまたはトピックからのメッセージに応答します。 

## <a name="trigger---example"></a>トリガー - 例

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

次の例は、[メッセージ メタデータ](#trigger---message-metadata)を読み取り、Service Bus キュー メッセージをログに記録する [C# 関数](functions-dotnet-class-library.md)を示しています。

```cs
[FunctionName("ServiceBusQueueTriggerCSharp")]                    
public static void Run(
    [ServiceBusTrigger("myqueue", Connection = "ServiceBusConnection")] 
    string myQueueItem,
    Int32 deliveryCount,
    DateTime enqueuedTimeUtc,
    string messageId,
    ILogger log)
{
    log.LogInformation($"C# ServiceBus queue trigger function processed message: {myQueueItem}");
    log.LogInformation($"EnqueuedTimeUtc={enqueuedTimeUtc}");
    log.LogInformation($"DeliveryCount={deliveryCount}");
    log.LogInformation($"MessageId={messageId}");
}
```

# <a name="c-scripttabcsharp-script"></a>[C# スクリプト](#tab/csharp-script)

次の例は、*function.json* ファイルの Service Bus トリガー バインドと、そのバインドが使用される [C# スクリプト関数](functions-reference-csharp.md)を示しています。 この機能は[メッセージ メタデータ](#trigger---message-metadata)を読み取り、Service Bus のキュー メッセージをログに記録します。

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
using System;

public static void Run(string myQueueItem,
    Int32 deliveryCount,
    DateTime enqueuedTimeUtc,
    string messageId,
    TraceWriter log)
{
    log.Info($"C# ServiceBus queue trigger function processed message: {myQueueItem}");

    log.Info($"EnqueuedTimeUtc={enqueuedTimeUtc}");
    log.Info($"DeliveryCount={deliveryCount}");
    log.Info($"MessageId={messageId}");
}
```

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

次の例は、*function.json* ファイルの Service Bus トリガー バインドと、そのバインドが使用される [JavaScript 関数](functions-reference-node.md)を示しています。 この機能は[メッセージ メタデータ](#trigger---message-metadata)を読み取り、Service Bus のキュー メッセージをログに記録します。 

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
    context.log('EnqueuedTimeUtc =', context.bindingData.enqueuedTimeUtc);
    context.log('DeliveryCount =', context.bindingData.deliveryCount);
    context.log('MessageId =', context.bindingData.messageId);
    context.done();
};
```

# <a name="pythontabpython"></a>[Python](#tab/python)

次の例では、トリガーを使用して ServiceBus キュー メッセージを読み取る方法を示します。

Service Bus のバインディングは *function.json* で定義され、そこで *type* は `serviceBusTrigger` に設定されます。

```json
{
  "scriptFile": "__init__.py",
  "bindings": [
    {
      "name": "msg",
      "type": "serviceBusTrigger",
      "direction": "in",
      "queueName": "inputqueue",
      "connection": "AzureServiceBusConnectionString"
    }
  ]
}
```

*_\_init_\_.py* のコードによってパラメーターが `func.ServiceBusMessage` として宣言され、関数でキュー メッセージを読み取ることができるようになります。

```python
import azure.functions as func

import logging
import json

def main(msg: func.ServiceBusMessage):
    logging.info('Python ServiceBus queue trigger processed message.')

    result = json.dumps({
        'message_id': msg.message_id,
        'body': msg.get_body().decode('utf-8'),
        'content_type': msg.content_type,
        'expiration_time': msg.expiration_time,
        'label': msg.label,
        'partition_key': msg.partition_key,
        'reply_to': msg.reply_to,
        'reply_to_session_id': msg.reply_to_session_id,
        'scheduled_enqueue_time': msg.scheduled_enqueue_time,
        'session_id': msg.session_id,
        'time_to_live': msg.time_to_live,
        'to': msg.to,
        'user_properties': msg.user_properties,
    })

    logging.info(result)
```

# <a name="javatabjava"></a>[Java](#tab/java)

次の Java 関数は、[Java 関数ランタイム ライブラリ](/java/api/overview/azure/functions/runtime)からの `@ServiceBusQueueTrigger` 注釈を使用して、Service Bus キュー トリガーの構成について記述します。 この関数は、キューにあるメッセージを取得し、ログに追加します。

```java
@FunctionName("sbprocessor")
 public void serviceBusProcess(
    @ServiceBusQueueTrigger(name = "msg",
                             queueName = "myqueuename",
                             connection = "myconnvarname") String message,
   final ExecutionContext context
 ) {
     context.getLogger().info(message);
 }
```

Service Bus トピックにメッセージが追加されたときに、Java 関数もトリガーできます。 次の例では、トリガー構成を記述する `@ServiceBusTopicTrigger` 注釈を使用します。

```java
@FunctionName("sbtopicprocessor")
    public void run(
        @ServiceBusTopicTrigger(
            name = "message",
            topicName = "mytopicname",
            subscriptionName = "mysubscription",
            connection = "ServiceBusConnection"
        ) String message,
        final ExecutionContext context
    ) {
        context.getLogger().info(message);
    }
```

---

## <a name="trigger---attributes-and-annotations"></a>トリガー - 属性と注釈

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

[C# クラス ライブラリ](functions-dotnet-class-library.md)では、以下の属性を使用して Service Bus トリガーを構成します。

* [ServiceBusTriggerAttribute](https://github.com/Azure/azure-functions-servicebus-extension/blob/master/src/Microsoft.Azure.WebJobs.Extensions.ServiceBus/ServiceBusTriggerAttribute.cs)

  属性のコンストラクターは、キューの名前、またはトピックとサブスクリプションを受け取ります。 Azure Functions バージョン 1.x では、接続のアクセス権を指定することもできます。 アクセス権を指定しない場合、既定値は `Manage` になります。 詳細については、「[トリガー - 構成](#trigger---configuration)」セクションをご覧ください。

  文字列パラメーターで使用される属性を示す例は次のとおりです。

  ```csharp
  [FunctionName("ServiceBusQueueTriggerCSharp")]                    
  public static void Run(
      [ServiceBusTrigger("myqueue")] string myQueueItem, ILogger log)
  {
      ...
  }
  ```

  次の例で示すように、`Connection` プロパティを設定して、使用する Service Bus 接続文字列を含むアプリ設定の名前を指定できます。

  ```csharp
  [FunctionName("ServiceBusQueueTriggerCSharp")]                    
  public static void Run(
      [ServiceBusTrigger("myqueue", Connection = "ServiceBusConnection")] 
      string myQueueItem, ILogger log)
  {
      ...
  }
  ```

  完全な例については、「[トリガー - 例](#trigger---example)」を参照してください。

* [ServiceBusAccountAttribute](https://github.com/Azure/azure-functions-servicebus-extension/blob/master/src/Microsoft.Azure.WebJobs.Extensions.ServiceBus/ServiceBusAccountAttribute.cs)

  使用する Service Bus アカウントを指定する別の方法を示します。 コンストラクターは、Service Bus 接続文字列を含むアプリ設定の名前を受け取ります。 属性は、パラメーター、メソッド、またはクラス レベルで適用できます。 次の例では、クラス レベルとメソッド レベルを示します。

  ```csharp
  [ServiceBusAccount("ClassLevelServiceBusAppSetting")]
  public static class AzureFunctions
  {
      [ServiceBusAccount("MethodLevelServiceBusAppSetting")]
      [FunctionName("ServiceBusQueueTriggerCSharp")]
      public static void Run(
          [ServiceBusTrigger("myqueue", AccessRights.Manage)] 
          string myQueueItem, ILogger log)
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

# <a name="c-scripttabcsharp-script"></a>[C# スクリプト](#tab/csharp-script)

属性は、C# スクリプトではサポートされていません。

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

属性は、JavaScript ではサポートされていません。

# <a name="pythontabpython"></a>[Python](#tab/python)

属性は、Python ではサポートされていません。

# <a name="javatabjava"></a>[Java](#tab/java)

`ServiceBusQueueTrigger` 注釈を使用すると、Service Bus キュー メッセージの作成時に実行される関数を作成できます。 使用可能な構成オプションには、キュー名と接続文字列名があります。

`ServiceBusTopicTrigger` 注釈を使用すると、関数をトリガーするデータを対象とするトピックとサブスクリプションを指定できます。

詳細については、トリガーの[例](#trigger---example)を参照してください。

---

## <a name="trigger---configuration"></a>トリガー - 構成

次の表は、*function.json* ファイルと `ServiceBusTrigger` 属性で設定したバインド構成のプロパティを説明しています。

|function.json のプロパティ | 属性のプロパティ |[説明]|
|---------|---------|----------------------|
|**type** | 該当なし | "serviceBusTrigger" に設定する必要があります。 このプロパティは、Azure Portal でトリガーを作成するときに自動で設定されます。|
|**direction** | 該当なし | "in" に設定する必要があります。 このプロパティは、Azure Portal でトリガーを作成するときに自動で設定されます。 |
|**name** | 該当なし | 関数コード内のキューまたはトピック メッセージを表す変数の名前。 |
|**queueName**|**QueueName**|監視するキューの名前。  トピックではなくキューを監視する場合にのみ設定します。
|**topicName**|**TopicName**|監視するトピックの名前。 キューではなくトピックを監視する場合にのみ設定します。|
|**subscriptionName**|**SubscriptionName**|監視するサブスクリプションの名前。 キューではなくトピックを監視する場合にのみ設定します。|
|**connection**|**[接続]**|このバインドに使用する Service Bus 接続文字列を含むアプリ設定の名前です。 アプリ設定の名前が "AzureWebJobs" で始まる場合は、名前の残りの部分のみを指定できます。 たとえば、`connection` を "MyServiceBus" に設定した場合、Functions ランタイムは "AzureWebJobsMyServiceBus" という名前のアプリ設定を探します。 `connection` を空のままにした場合、Functions ランタイムは、アプリ設定内の "AzureWebJobsServiceBus" という名前の既定の Service Bus 接続文字列を使用します。<br><br>接続文字列は、[管理資格情報の取得](../service-bus-messaging/service-bus-quickstart-portal.md#get-the-connection-string)に関する記事の手順に従って取得します。 接続文字列は、特定のキューまたはトピックに限らず、Service Bus 名前空間のものである必要があります。 |
|**accessRights**|**Access (アクセス)**|接続文字列のアクセス権。 使用できる値は `manage` と `listen` です。 既定値は `manage` で、`connection` が**管理**アクセス許可を持つことを示します。 **管理**アクセス許可を持たない接続文字列を使用する場合は、`accessRights` を "listen" に設定します。 設定しないと、Functions ランタイムが管理権限を必要とする操作の試行に失敗する可能性があります。 最新バージョンの Service Bus SDK が管理の操作をサポートしていないため、Azure Functions バージョン 2.x 以降ではこのプロパティを利用できません。|
|**isSessionsEnabled**|**IsSessionsEnabled**|[セッション対応の](../service-bus-messaging/message-sessions.md)キューまたはサブスクリプションに接続する場合は `true`。 それ以外の場合は `false` (既定値)。|

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="trigger---usage"></a>トリガー - 使用方法

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

キューまたはトピック メッセージに次のパラメーター型を使用できます。

* `string` - メッセージがテキストである場合。
* `byte[]` - バイナリ データの場合に便利です。
* カスタム型 - メッセージに JSON が含まれている場合、Azure Functions は JSON データの逆シリアル化を試みます。
* `BrokeredMessage` - [BrokeredMessage.GetBody\<T>()](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.getbody?view=azure-dotnet#Microsoft_ServiceBus_Messaging_BrokeredMessage_GetBody__1) メソッドで逆シリアル化されたメッセージを返します。

これらのパラメーター型は Azure Functions バージョン 1.x 用です。2.x 以降では、`BrokeredMessage` の代わりに [`Message`](https://docs.microsoft.com/dotnet/api/microsoft.azure.servicebus.message) を使用してください。

# <a name="c-scripttabcsharp-script"></a>[C# スクリプト](#tab/csharp-script)

キューまたはトピック メッセージに次のパラメーター型を使用できます。

* `string` - メッセージがテキストである場合。
* `byte[]` - バイナリ データの場合に便利です。
* カスタム型 - メッセージに JSON が含まれている場合、Azure Functions は JSON データの逆シリアル化を試みます。
* `BrokeredMessage` - [BrokeredMessage.GetBody\<T>()](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.getbody?view=azure-dotnet#Microsoft_ServiceBus_Messaging_BrokeredMessage_GetBody__1) メソッドで逆シリアル化されたメッセージを返します。

これらのパラメーターは Azure Functions バージョン 1.x 用です。2.x 以降では、`BrokeredMessage` の代わりに [`Message`](https://docs.microsoft.com/dotnet/api/microsoft.azure.servicebus.message) を使用してください。

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

`context.bindings.<name from function.json>` を使用して、キューまたはトピック メッセージにアクセスします。 Service Bus メッセージが文字列または JSON オブジェクトとして関数に渡されます。

# <a name="pythontabpython"></a>[Python](#tab/python)

キュー メッセージは、`func.ServiceBusMessage` として型指定されたパラメーターを介して関数で使用できます。 Service Bus メッセージが文字列または JSON オブジェクトとして関数に渡されます。

# <a name="javatabjava"></a>[Java](#tab/java)

受信 Service Bus メッセージは、`ServiceBusQueueMessage` または `ServiceBusTopicMessage` パラメーターを使用して利用できます。

[詳細についての例を参照してください。](#trigger)

---

## <a name="trigger---poison-messages"></a>トリガー - 有害メッセージ

Azure Functions では、有害メッセージの処理を制御することも構成することもできません。 Service Bus 自体で、有害なメッセージが処理されます。

## <a name="trigger---peeklock-behavior"></a>トリガー - PeekLock 動作

Functions ランタイムは、メッセージを [PeekLock モード](../service-bus-messaging/service-bus-performance-improvements.md#receive-mode)で受信します。 それは、関数が正常に終了した場合はメッセージに対して `Complete` を呼び出し、関数が失敗した場合は `Abandon` を呼び出します。 関数が実行されている限り、関数の実行時間が `PeekLock` タイムアウトよりも長くなると、ロックが自動的に更新されます。 

`maxAutoRenewDuration` は *host.json* に構成可能です。これは [OnMessageOptions.MaxAutoRenewDuration](https://docs.microsoft.com/dotnet/api/microsoft.azure.servicebus.messagehandleroptions.maxautorenewduration?view=azure-dotnet) にマップされます。 この設定に許可される最大値は、Service Bus のドキュメントに従って 5 分ですが、Functions の制限時間は既定の 5 分から 10 分に増やすことができます。 Service Bus 関数の場合は、Service Bus の更新制限を超えるので増やしません。

## <a name="trigger---message-metadata"></a>トリガー - メッセージのメタデータ

Service Bus トリガーには、いくつかの[メタデータ プロパティ](./functions-bindings-expressions-patterns.md#trigger-metadata)があります。 これらのプロパティは、他のバインドのバインド式の一部として、またはコードのパラメーターとして使用できます。 これらは [BrokeredMessage](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.brokeredmessage) クラスのプロパティです。

|プロパティ|種類|[説明]|
|--------|----|-----------|
|`DeliveryCount`|`Int32`|配信回数。|
|`DeadLetterSource`|`string`|配信不能のソース。|
|`ExpiresAtUtc`|`DateTime`|有効期限 (UTC)。|
|`EnqueuedTimeUtc`|`DateTime`|エンキューされた時刻 (UTC)。|
|`MessageId`|`string`|有効になっている場合に、重複するメッセージを識別するために Service Bus が使用できるユーザー定義の値|
|`ContentType`|`string`|アプリケーション固有のロジックのために送信者と受信者が利用するコンテンツ タイプ識別子。|
|`ReplyTo`|`string`|キュー アドレスへの返信。|
|`SequenceNumber`|`Int64`|Service Bus によってメッセージに割り当てられる一意の番号。|
|`To`|`string`|送信先アドレス。|
|`Label`|`string`|アプリケーション固有のラベル。|
|`CorrelationId`|`string`|関連付け ID。|

> [!NOTE]
> 現在、セッションで有効になっているキューやサブスクリプションで動作する Service Bus トリガーはプレビュー段階です。 これに関連した今後の更新については、[この項目](https://github.com/Azure/azure-webjobs-sdk/issues/529#issuecomment-491113458)を追跡してください。 

この記事の前半でこれらのプロパティを使用している[コード例](#trigger---example)を参照してください。

## <a name="output"></a>Output

キューまたはトピック メッセージを送信するには、Azure Service Bus 出力バインドを使用します。

### <a name="output---example"></a>出力 - 例

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

次の例は、Service Bus キュー メッセージを送信する [C# 関数](functions-dotnet-class-library.md)を示しています。

```cs
[FunctionName("ServiceBusOutput")]
[return: ServiceBus("myqueue", Connection = "ServiceBusConnection")]
public static string ServiceBusOutput([HttpTrigger] dynamic input, ILogger log)
{
    log.LogInformation($"C# function processed: {input.Text}");
    return input.Text;
}
```

# <a name="c-scripttabcsharp-script"></a>[C# スクリプト](#tab/csharp-script)

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
public static void Run(TimerInfo myTimer, ILogger log, out string outputSbQueue)
{
    string message = $"Service Bus queue message created at: {DateTime.Now}";
    log.LogInformation(message); 
    outputSbQueue = message;
}
```

複数のメッセージを作成する C# スクリプト コードを次に示します。

```cs
public static async Task Run(TimerInfo myTimer, ILogger log, IAsyncCollector<string> outputSbQueue)
{
    string message = $"Service Bus queue messages created at: {DateTime.Now}";
    log.LogInformation(message); 
    await outputSbQueue.AddAsync("1 " + message);
    await outputSbQueue.AddAsync("2 " + message);
}
```

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

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
    context.bindings.outputSbQueue = message;
    context.done();
};
```

複数のメッセージを作成する JavaScript スクリプト コードを次に示します。

```javascript
module.exports = function (context, myTimer) {
    var message = 'Service Bus queue message created at ' + timeStamp;
    context.log(message);   
    context.bindings.outputSbQueue = [];
    context.bindings.outputSbQueue.push("1 " + message);
    context.bindings.outputSbQueue.push("2 " + message);
    context.done();
};
```

# <a name="pythontabpython"></a>[Python](#tab/python)

次の例では、Python で Service Bus キューに書き出す方法を示します。

Service Bus のバインディング定義は *function.json* で定義され、そこで *type* は `serviceBus` に設定されます。

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
      "type": "serviceBus",
      "direction": "out",
      "connection": "AzureServiceBusConnectionString",
      "name": "msg",
      "queueName": "outqueue"
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

# <a name="javatabjava"></a>[Java](#tab/java)

次の例は、HTTP 要求によってトリガーされたときに Service Bus キュー `myqueue` にメッセージを送信する Java 関数を示しています。

```java
@FunctionName("httpToServiceBusQueue")
@ServiceBusQueueOutput(name = "message", queueName = "myqueue", connection = "AzureServiceBusConnection")
public String pushToQueue(
  @HttpTrigger(name = "request", methods = {HttpMethod.POST}, authLevel = AuthorizationLevel.ANONYMOUS)
  final String message,
  @HttpOutput(name = "response") final OutputBinding<T> result ) {
      result.setValue(message + " has been sent.");
      return message;
 }
```

 [Java 関数ランタイム ライブラリ ](/java/api/overview/azure/functions/runtime) で、その値が Service Bus キューに書き込まれる関数のパラメーターに関する `@QueueOutput` 注釈を使用します。  パラメーターの型は `OutputBinding<T>` にする必要があります。T は POJO の Java の任意のネイティブ型です。

Java 関数は、Service Bus トピックにも書き込むことができます。 次の例では、出力バインディングの構成を記述する`@ServiceBusTopicOutput`注釈を使用します。 

```java
@FunctionName("sbtopicsend")
    public HttpResponseMessage run(
            @HttpTrigger(name = "req", methods = {HttpMethod.GET, HttpMethod.POST}, authLevel = AuthorizationLevel.ANONYMOUS) HttpRequestMessage<Optional<String>> request,
            @ServiceBusTopicOutput(name = "message", topicName = "mytopicname", subscriptionName = "mysubscription", connection = "ServiceBusConnection") OutputBinding<String> message,
            final ExecutionContext context) {
        
        String name = request.getBody().orElse("Azure Functions");

        message.setValue(name);
        return request.createResponseBuilder(HttpStatus.OK).body("Hello, " + name).build();
        
    }
```

---

## <a name="output---attributes-and-annotations"></a>出力 - 属性と注釈

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

[C# クラス ライブラリ](functions-dotnet-class-library.md)では、[ServiceBusAttribute](https://github.com/Azure/azure-functions-servicebus-extension/blob/master/src/Microsoft.Azure.WebJobs.Extensions.ServiceBus/ServiceBusAttribute.cs) 属性を使用します。

属性のコンストラクターは、キューの名前、またはトピックとサブスクリプションを受け取ります。 接続のアクセス権を指定することもできます。 アクセス権の設定を選択する方法については、「[出力 - 構成](#output---configuration)」セクションで説明されています。 関数の戻り値に適用される属性に適用される属性を示す例は次のとおりです。

```csharp
[FunctionName("ServiceBusOutput")]
[return: ServiceBus("myqueue")]
public static string Run([HttpTrigger] dynamic input, ILogger log)
{
    ...
}
```

次の例で示すように、`Connection` プロパティを設定して、使用する Service Bus 接続文字列を含むアプリ設定の名前を指定できます。

```csharp
[FunctionName("ServiceBusOutput")]
[return: ServiceBus("myqueue", Connection = "ServiceBusConnection")]
public static string Run([HttpTrigger] dynamic input, ILogger log)
{
    ...
}
```

完全な例については、「[出力 - 例](#output---example)」を参照してください。

`ServiceBusAccount` 属性を使用して、クラス、メソッド、またはパラメーターのレベルで使用する Service Bus アカウントを指定できます。  詳細については、[トリガー - 属性](#trigger---attributes-and-annotations)をご覧ください。

# <a name="c-scripttabcsharp-script"></a>[C# スクリプト](#tab/csharp-script)

属性は、C# スクリプトではサポートされていません。

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

属性は、JavaScript ではサポートされていません。

# <a name="pythontabpython"></a>[Python](#tab/python)

属性は、Python ではサポートされていません。

# <a name="javatabjava"></a>[Java](#tab/java)

`ServiceBusQueueOutput` と `ServiceBusTopicOutput` 注釈を使用して、関数の出力としてメッセージを書き込むことができます。 これらの注釈で修飾されたパラメーターは `OutputBinding<T>` として宣言されている必要があります。ここで、`T` はメッセージの型に対応する型です。

---

## <a name="output---configuration"></a>出力 - 構成

次の表は、*function.json* ファイルと `ServiceBus` 属性で設定したバインド構成のプロパティを説明しています。

|function.json のプロパティ | 属性のプロパティ |[説明]|
|---------|---------|----------------------|
|**type** | 該当なし | "serviceBus" に設定する必要があります。 このプロパティは、Azure Portal でトリガーを作成するときに自動で設定されます。|
|**direction** | 該当なし | "out" に設定する必要があります。 このプロパティは、Azure Portal でトリガーを作成するときに自動で設定されます。 |
|**name** | 該当なし | 関数コード内のキューまたはトピック メッセージを表す変数の名前。 "$return" に設定して、関数の戻り値を参照します。 |
|**queueName**|**QueueName**|キューの名前。  トピックではなくキューのメッセージを送信する場合にのみ設定します。
|**topicName**|**TopicName**|トピックの名前。 キューではなくトピックのメッセージを送信する場合にのみ設定します。|
|**connection**|**[接続]**|このバインドに使用する Service Bus 接続文字列を含むアプリ設定の名前です。 アプリ設定の名前が "AzureWebJobs" で始まる場合は、名前の残りの部分のみを指定できます。 たとえば、`connection` を "MyServiceBus" に設定した場合、Functions ランタイムは "AzureWebJobsMyServiceBus" という名前のアプリ設定を探します。 `connection` を空のままにした場合、Functions ランタイムは、アプリ設定内の "AzureWebJobsServiceBus" という名前の既定の Service Bus 接続文字列を使用します。<br><br>接続文字列は、[管理資格情報の取得](../service-bus-messaging/service-bus-quickstart-portal.md#get-the-connection-string)に関する記事の手順に従って取得します。 接続文字列は、特定のキューまたはトピックに限らず、Service Bus 名前空間のものである必要があります。|
|**accessRights**|**Access (アクセス)**|接続文字列のアクセス権。 使用できる値は `manage` と `listen` です。 既定値は `manage` で、`connection` が**管理**アクセス許可を持つことを示します。 **管理**アクセス許可を持たない接続文字列を使用する場合は、`accessRights` を "listen" に設定します。 設定しないと、Functions ランタイムが管理権限を必要とする操作の試行に失敗する可能性があります。 最新バージョンの Service Bus SDK が管理の操作をサポートしていないため、Azure Functions バージョン 2.x 以降ではこのプロパティを利用できません。|

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="output---usage"></a>出力 - 使用方法

Azure Functions 1.x では、キューが存在しない場合に `accessRights` を `manage` に設定していると、ランタイムによってキューが作成されます。 Functions バージョン 2.x 以降では、キューまたはトピックが既に存在する必要があります。存在しないキューまたはトピックを指定した場合、関数は失敗します。 

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

出力バインドには、次のパラメーターの型を使用します。

* `out T paramName` - `T` は任意の JSON シリアル化可能な型にすることができます。 関数が終了したときにこのパラメーターの値が null の場合、Functions は、null オブジェクトでメッセージを作成します。
* `out string` - 関数が終了したときにパラメーター値が null の場合、Functions はメッセージを作成しません。
* `out byte[]` - 関数が終了したときにパラメーター値が null の場合、Functions はメッセージを作成しません。
* `out BrokeredMessage` - 関数が終了したときにパラメーター値が null の場合、Functions はメッセージを作成しません (Functions 1.x の場合)。
* `out Message` - パラメーター値が null の場合は、関数が終了したときに、Functions はメッセージを作成しません (Functions 2.x 以降の場合)
* `ICollector<T>` または `IAsyncCollector<T>`- 複数のメッセージを作成する場合。 `Add` メソッドを呼び出すときに、メッセージが作成されます。

C# 関数を使用する場合:

* 非同期関数には、`out` パラメーターの代わりに戻り値または `IAsyncCollector` が必要です。

* セッション ID にアクセスするには、 [`Message`](https://docs.microsoft.com/dotnet/api/microsoft.azure.servicebus.message) 型にバインドし、`sessionId` プロパティを使用します。

# <a name="c-scripttabcsharp-script"></a>[C# スクリプト](#tab/csharp-script)

出力バインドには、次のパラメーターの型を使用します。

* `out T paramName` - `T` は任意の JSON シリアル化可能な型にすることができます。 関数が終了したときにこのパラメーターの値が null の場合、Functions は、null オブジェクトでメッセージを作成します。
* `out string` - 関数が終了したときにパラメーター値が null の場合、Functions はメッセージを作成しません。
* `out byte[]` - 関数が終了したときにパラメーター値が null の場合、Functions はメッセージを作成しません。
* `out BrokeredMessage` - 関数が終了したときにパラメーター値が null の場合、Functions はメッセージを作成しません (Functions 1.x の場合)。
* `out Message` - パラメーター値が null の場合は、関数が終了したときに、Functions はメッセージを作成しません (Functions 2.x 以降の場合)
* `ICollector<T>` または `IAsyncCollector<T>`- 複数のメッセージを作成する場合。 `Add` メソッドを呼び出すときに、メッセージが作成されます。

C# 関数を使用する場合:

* 非同期関数には、`out` パラメーターの代わりに戻り値または `IAsyncCollector` が必要です。

* セッション ID にアクセスするには、 [`Message`](https://docs.microsoft.com/dotnet/api/microsoft.azure.servicebus.message) 型にバインドし、`sessionId` プロパティを使用します。

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

`context.bindings.<name from function.json>` を使用して、キューまたはトピックにアクセスします。 文字列、バイト配列、または (JSON に逆シリアル化された) JavaScript オブジェクトを `context.binding.<name>` に割り当てることができます。

# <a name="pythontabpython"></a>[Python](#tab/python)

組み込みの出力バインドではなく、[Azure Service Bus SDK](https://docs.microsoft.com/azure/service-bus-messaging) を使用します。

# <a name="javatabjava"></a>[Java](#tab/java)

組み込みの出力バインドではなく、[Azure Service Bus SDK](https://docs.microsoft.com/azure/service-bus-messaging) を使用します。

---

## <a name="exceptions-and-return-codes"></a>例外とリターン コード

| バインド | リファレンス |
|---|---|
| Service Bus | [Service Bus のエラー コード](https://docs.microsoft.com/azure/service-bus-messaging/service-bus-messaging-exceptions) |
| Service Bus | [Service Bus の制限](https://docs.microsoft.com/azure/service-bus-messaging/service-bus-quotas) |

<a name="host-json"></a>  

## <a name="hostjson-settings"></a>host.json 設定

このセクションでは、バージョン 2.x 以降でこのバインドに使用可能なグローバル構成設定について説明します。 次の host.json ファイルの例には、このバインドの設定のみが含まれています。 グローバル構成設定の詳細については、[Azure Functions の host.json のリファレンス](functions-host-json.md)を参照してください。

> [!NOTE]
> Functions 1.x の host.json のリファレンスについては、「[host.json reference for Azure Functions 1.x (Azure Functions 1.x の host.json のリファレンス)](functions-host-json-v1.md)」を参照してください。

```json
{
    "version": "2.0",
    "extensions": {
        "serviceBus": {
            "prefetchCount": 100,
            "messageHandlerOptions": {
                "autoComplete": false,
                "maxConcurrentCalls": 32,
                "maxAutoRenewDuration": "00:55:00"
            },
            "sessionHandlerOptions": {
                "autoComplete": false,
                "messageWaitTimeout": "00:00:30",
                "maxAutoRenewDuration": "00:55:00",
                "maxConcurrentSessions": 16
            }
        }
    }
}
```

|プロパティ  |Default | [説明] |
|---------|---------|---------|
|maxAutoRenewDuration|00:05:00|メッセージ ロックが自動的に更新される最大間隔。|
|autoComplete|true|トリガーがメッセージをすぐに完了としてマークする (オートコンプリート) か、complete を呼び出すために関数が正常に終了するまで待機するか。|
|maxConcurrentCalls|16|メッセージ ポンプが開始する必要があるコールバックの同時呼び出しの最大数 既定では、Functions ランタイムは、複数のメッセージを同時に処理します。 一度に 1 つのキューまたはトピックのメッセージのみを処理するようにランタイムに指示するには、`maxConcurrentCalls` を 1 に設定します。 |

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [Azure Functions のトリガーとバインドの詳細情報](functions-triggers-bindings.md)
