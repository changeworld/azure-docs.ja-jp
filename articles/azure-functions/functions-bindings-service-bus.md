<properties
	pageTitle="Azure Functions における Service Bus トリガーとバインド | Microsoft Azure"
	description="Azure Functions で Azure Service Bus トリガーとバインドを使用する方法を説明します。"
	services="functions"
	documentationCenter="na"
	authors="christopheranderson"
	manager="erikre"
	editor=""
	tags=""
	keywords="Azure Functions, 関数, イベント処理, 動的コンピューティング, サーバーなしのアーキテクチャ"/>

<tags
	ms.service="functions"
	ms.devlang="multiple"
	ms.topic="reference"
	ms.tgt_pltfrm="multiple"
	ms.workload="na"
	ms.date="08/22/2016"
	ms.author="chrande; glenga"/>

# Azure Functions における Service Bus トリガーとキューとトピックに使用するバインド

[AZURE.INCLUDE [functions-selector-bindings](../../includes/functions-selector-bindings.md)]

この記事では、Azure Functions で Azure Service Bus のトリガーとバインドを構成したりコーディングしたりする方法について説明します。

[AZURE.INCLUDE [intro](../../includes/functions-bindings-intro.md)]

## <a id="sbtrigger"></a>Service Bus キューまたはトピックのトリガー

#### function.json

*function.json* ファイルでは、次のプロパティを指定します。

- `name`: キューまたはトピック、キューまたはトピックのメッセージの関数コードで使用される変数名。
- `queueName`: (キュー トリガーのみ) ポーリングするキューの名前。
- `topicName`: (トピック トリガーのみ) ポーリングするトピックの名前。
- `subscriptionName`: (トピック トリガーのみ) サブスクリプション名。
- `connection`: Service Bus 接続文字列を含むアプリ設定の名前。接続文字列は、特定のキューまたはトピックに限らず、Service Bus 名前空間のものである必要があります。接続文字列に管理権限がない場合は、`accessRights` プロパティを設定します。`connection` を空のままにすると、トリガーまたはバインドは、AzureWebJobsServiceBus アプリ設定で指定される Function App の既定の Service Bus 接続文字列で動作します。
- `accessRights`: 接続文字列に使用できるアクセス権を指定します。既定値は `manage` です。manage アクセス権を提供しない接続文字列を使用している場合は、`listen` に設定します。それ以外の場合は、Functions ランタイムは、管理権限を必要とする操作を試行し、失敗する可能性があります。
- `type`: *serviceBusTrigger* に設定する必要があります。
- `direction`: *in* に設定する必要があります。

Service Bus キューのトリガーに使用する *function.json* の例

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

#### Service Bus キュー メッセージを処理する C# コードの例

```csharp
public static void Run(string myQueueItem, TraceWriter log)
{
    log.Info($"C# ServiceBus queue trigger function processed message: {myQueueItem}");
}
```

#### Service Bus キュー メッセージを処理する F# コードの例

```fsharp
let Run(myQueueItem: string, log: TraceWriter) =
    log.Info(sprintf "F# ServiceBus queue trigger function processed message: %s" myQueueItem)
```

#### Service Bus キュー メッセージを処理する Node.js コードの例

```javascript
module.exports = function(context, myQueueItem) {
    context.log('Node.js ServiceBus queue trigger function processed message', myQueueItem);
    context.done();
};
```

#### サポートされている型

Service Bus キュー メッセージを、次のいずれかの型に逆シリアル化できます。

* オブジェクト (JSON)
* string
* byte array
* `BrokeredMessage` (C#)

#### <a id="sbpeeklock"></a> PeekLock 動作

Functions ランタイムは、`PeekLock` モードでメッセージを受信して、関数が正常に終了した場合はメッセージの `Complete` を呼び出し、関数が失敗した場合は `Abandon` を呼び出します。関数の実行時間が `PeekLock` タイムアウトよりも長くなると、ロックが自動的に更新されます。

#### <a id="sbpoison"></a> 有害メッセージの処理

Service Bus では、Azure Functions の構成やコードで制御または構成することができない、独自の有害メッセージを処理します。

#### <a id="sbsinglethread"></a> シングルスレッド

既定では、Functions ランタイムは、複数のキュー メッセージを同時に処理します。一度に 1 つのキューまたはトピックのメッセージのみを処理するようにランタイムに指示するには、*host.json* ファイルで `serviceBus.maxConcurrrentCalls` を 1 に設定します。*host.json* ファイルの詳細については、開発者向けリファレンスの記事「[フォルダー構造](functions-reference.md#folder-structure)」と、WebJobs.Script リポジトリ Wiki の「[host.json](https://github.com/Azure/azure-webjobs-sdk-script/wiki/host.json)」を参照してください。

## <a id="sboutput"></a>Service Bus キューまたはトピックの出力バインド

#### function.json

*function.json* ファイルでは、次のプロパティを指定します。

- `name`: キューまたはキュー メッセージの関数コードで使用される変数名。
- `queueName`: (キュー トリガーのみ) ポーリングするキューの名前。
- `topicName`: (トピック トリガーのみ) ポーリングするトピックの名前。
- `subscriptionName`: (トピック トリガーのみ) サブスクリプション名。
- `connection`: Service Bus のトリガーと同じです。
- `accessRights`: 接続文字列に使用できるアクセス権を指定します。既定値は `manage` です。manage アクセス権を提供しない接続文字列を使用している場合は、`send` に設定します。それ以外の場合は、Functions ランタイムは、キューの作成などの管理権限を必要とする操作を試行し、失敗する可能性があります。
- `type`: *serviceBus* に設定する必要があります。
- `direction`: *out* に設定する必要があります。

Service Bus キュー メッセージを記述するためにタイマー トリガーを使用する *function.json* の例を次に示します。

```JSON
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

#### サポートされている型

Azure Functions は、次の型のいずれかから Service Bus キュー メッセージを作成できます。

* オブジェクト (常に JSON メッセージを作成し、関数が終了したときに、値が null である場合は、null オブジェクトでメッセージを作成)
* string (関数が終了したときに、値が null でない場合は、メッセージを作成します)
* byte array (string と同様に動作)
* `BrokeredMessage` (C#、string と同様に動作)

C# 関数で複数のメッセージを作成するには、`ICollector<T>` または `IAsyncCollector<T>` を使用できます。`Add` メソッドを呼び出すときに、メッセージが作成されます。

#### Service Bus キュー メッセージを作成する C# コードの例

```csharp
public static void Run(TimerInfo myTimer, TraceWriter log, out string outputSbQueue)
{
	string message = $"Service Bus queue message created at: {DateTime.Now}";
    log.Info(message); 
    outputSbQueue = message;
}
```

```csharp
public static void Run(TimerInfo myTimer, TraceWriter log, ICollector<string> outputSbQueue)
{
	string message = $"Service Bus queue message created at: {DateTime.Now}";
    log.Info(message); 
    outputSbQueue.Add("1 " + message);
    outputSbQueue.Add("2 " + message);
}
```

#### Service Bus キュー メッセージを作成する F# コードの例

```fsharp
let Run(myTimer: TimerInfo, log: TraceWriter, outputSbQueue: byref<string>) =
    let message = sprintf "Service Bus queue message created at: %s" (DateTime.Now.ToString())
    log.Info(message)
    outputSbQueue = message
```

#### Service Bus キュー メッセージを作成する Node.js コードの例

```javascript
module.exports = function (context, myTimer) {
    var timeStamp = new Date().toISOString();
    
    if(myTimer.isPastDue)
    {
        context.log('Node.js is running late!');
    }
    var message = 'Service Bus queue message created at ' + timeStamp;
    context.log(message);   
    context.bindings.outputSbQueueMsg = message;
    context.done();
};
```

## 次のステップ

[AZURE.INCLUDE [次のステップ](../../includes/functions-bindings-next-steps.md)]

<!---HONumber=AcomDC_0921_2016-->