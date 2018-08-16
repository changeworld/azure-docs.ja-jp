---
title: Azure Functions における Azure Event Hubs のバインド
description: Azure Functions で Azure Event Hubs のバインドを使用する方法について説明します。
services: functions
documentationcenter: na
author: ggailey777
manager: jeconnoc
editor: ''
tags: ''
keywords: Azure Functions, 関数, イベント処理, 動的コンピューティング, サーバーなしのアーキテクチャ
ms.assetid: daf81798-7acc-419a-bc32-b5a41c6db56b
ms.service: functions
ms.devlang: multiple
ms.topic: reference
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 11/08/2017
ms.author: glenga
ms.openlocfilehash: 961126f62c3e8fbb947b9d1b34ac157bf37a8cba
ms.sourcegitcommit: fc5555a0250e3ef4914b077e017d30185b4a27e6
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/03/2018
ms.locfileid: "39480939"
---
# <a name="azure-event-hubs-bindings-for-azure-functions"></a>Azure Functions における Azure Event Hubs のバインド

この記事では、Azure Functions で [Azure Event Hubs](../event-hubs/event-hubs-what-is-event-hubs.md) のバインドを使用する方法について説明します。 Azure Functions は、イベント ハブのトリガーおよび出力バインドをサポートしています。

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

## <a name="packages---functions-1x"></a>パッケージ - Functions 1.x

Azure Functions バージョン 1.x の場合、Event Hubs バインディングは [Microsoft.Azure.WebJobs.ServiceBus](http://www.nuget.org/packages/Microsoft.Azure.WebJobs.ServiceBus) NuGet パッケージ、バージョン 2.x で提供されます。
パッケージのソース コードは、[azure-webjobs-sdk](https://github.com/Azure/azure-webjobs-sdk/tree/v2.x/src/Microsoft.Azure.WebJobs.ServiceBus/EventHubs) GitHub リポジトリにあります。


[!INCLUDE [functions-package](../../includes/functions-package.md)]

## <a name="packages---functions-2x"></a>パッケージ - Functions 2.x

Functions 2.x の場合、[Microsoft.Azure.WebJobs.Extensions.EventHubs](http://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.EventHubs) パッケージ、バージョン 3.x を使用します。
パッケージのソース コードは、[azure-webjobs-sdk](https://github.com/Azure/azure-webjobs-sdk/tree/master/src/Microsoft.Azure.WebJobs.Extensions.EventHubs) GitHub リポジトリにあります。

[!INCLUDE [functions-package-v2](../../includes/functions-package-v2.md)]

## <a name="trigger"></a>トリガー

イベント ハブ トリガーを使用して、イベント ハブのイベント ストリームに送信されたイベントに応答します。 トリガーをセットアップするには、イベント ハブへの読み取りアクセスが必要です。

イベント ハブ トリガー関数がトリガーされると、それをトリガーするメッセージが文字列として関数に渡されます。

## <a name="trigger---scaling"></a>トリガー - スケーリング

Event Hub-Triggered 関数の各インスタンスは、EventProcessorHost (EPH) インスタンスを 1 つのみ使用します。 Event Hubs では、1 つの EPH のみが特定のパーティションのリースを取得できます。

たとえば、次の設定と前提条件の Event Hub があったとします。

1. 10 パーティション。
1. 1000 イベントがすべてのパーティション間で均等に分散され、各パーティション内に 100 以上のメッセージがある。

関数が最初に有効化されたときに存在する関数インスタンスは 1 つのみ。 この関数のインスタンスを Function_0 とします。 Function_0 には、10 パーティションすべてのリースの取得を管理する 1 EPH があります。 1 EPH は、パーティション 0 ～ 9 からのイベントの読み取りを開始します。 この後、次のいずれかが発生します。

* **関数インスタンスが 1 つのみ必要** - Function_0 は、Azure Functions のスケーリングのロジックが開始される前に 1000 メッセージすべてを処理することができます。 そのため、1000 メッセージすべてが、Function_0 によって処理されます。

* **関数インスタンスを 1 つ追加** - Azure Functions のスケーリング ロジックにより、Function_0 が処理できる量より多いメッセージがあると判断され、新しいインスタンス Function_1 が作成されます。 Event Hubs は、新しい EPH インスタンスがメッセージを読み取ろうとしていることを検知します。 Event Hubs は、複数の EPH インスタンスのパーティションにわたって負荷分散を開始します。たとえば、パーティション 0 ～ 4 は Function_0 に割り当てられ、パーティション 5 ～ 9 は Function_1 に割り当てられます。 

* **N 個の関数インスタンスを追加** - Azure Functions のスケーリング ロジックが、Function_0 と Function_1 の両方に、それぞれが処理できる量より多いメッセージがあると判断します。 Function_2... N まで再スケーリングします。ここで、N は Event Hub パーティションよりも大きい値です。 Event Hubs は、Function_0...9 インスタンスにわたってパーティションを負荷分散します。

Azure Functions の現在のロジック スケーリングに固有の特長として、N はパーティションの数より大きい数になります。 これは、他のインスタンスから利用可能になったパーティションへのロックを EPH のインスタンスが常にすぐに使用できるようにするための設定です。 ユーザーには、関数インスタンスが実行されたときに使用されたリソースに対してのみ料金が発生し、オーバー プロビジョニングには課金されません。

すべての関数がエラーなく実行された場合は、関連付けられているストレージ アカウントにチェックポイントが追加されます。 チェックポイントが成功した場合、1000 のすべてのメッセージが再取得されることはありません。

## <a name="trigger---example"></a>トリガー - 例

言語固有の例をご覧ください。

* [C#](#trigger---c-example)
* [C# スクリプト (.csx)](#trigger---c-script-example)
* [F#](#trigger---f-example)
* [JavaScript](#trigger---javascript-example)

### <a name="trigger---c-example"></a>トリガー - C# の例

次の例は、イベント ハブ トリガーのメッセージ本文をログに記録する [C# 関数](functions-dotnet-class-library.md)を示しています。

```csharp
[FunctionName("EventHubTriggerCSharp")]
public static void Run([EventHubTrigger("samples-workitems", Connection = "EventHubConnectionAppSetting")] string myEventHubMessage, TraceWriter log)
{
    log.Info($"C# Event Hub trigger function processed a message: {myEventHubMessage}");
}
```

関数コードの[イベント メタデータ](#trigger---event-metadata)にアクセスするには、(`Microsoft.ServiceBus.Messaging` の using ステートメントを必要とする) [EventData](/dotnet/api/microsoft.servicebus.messaging.eventdata) オブジェクトにバインドします。 また、メソッド シグネチャ内のバインディング式を使用して、同じプロパティにアクセスすることもできます。  次の例は、同じデータを取得する 2 つの方法を示しています。

```csharp
[FunctionName("EventHubTriggerCSharp")]
public static void Run(
    [EventHubTrigger("samples-workitems", Connection = "EventHubConnectionAppSetting")] EventData myEventHubMessage, 
    DateTime enqueuedTimeUtc, 
    Int64 sequenceNumber,
    string offset,
    TraceWriter log)
{
    log.Info($"Event: {Encoding.UTF8.GetString(myEventHubMessage.GetBytes())}");
    // Metadata accessed by binding to EventData
    log.Info($"EnqueuedTimeUtc={myEventHubMessage.EnqueuedTimeUtc}");
    log.Info($"SequenceNumber={myEventHubMessage.SequenceNumber}");
    log.Info($"Offset={myEventHubMessage.Offset}");
    // Metadata accessed by using binding expressions
    log.Info($"EnqueuedTimeUtc={enqueuedTimeUtc}");
    log.Info($"SequenceNumber={sequenceNumber}");
    log.Info($"Offset={offset}");
}
```

イベントを一括で受け取るには、`string` または `EventData` を配列にします。

```cs
[FunctionName("EventHubTriggerCSharp")]
public static void Run([EventHubTrigger("samples-workitems", Connection = "EventHubConnectionAppSetting")] string[] eventHubMessages, TraceWriter log)
{
    foreach (var message in eventHubMessages)
    {
        log.Info($"C# Event Hub trigger function processed a message: {message}");
    }
}
```

### <a name="trigger---c-script-example"></a>トリガー - C# スクリプトの例

次の例は、*function.json* ファイルのイベント ハブ トリガー バインドと、そのバインドが使用される [C# スクリプト関数](functions-reference-csharp.md)を示しています。 この関数では、イベント ハブ トリガーのメッセージ本文を記録します。

次の例は、*function.json* ファイル内の Event Hubs バインディング データを示しています。 最初の例は Functions 1.x 用で、2 つ目の例は Functions 2.x 用です。 

```json
{
  "type": "eventHubTrigger",
  "name": "myEventHubMessage",
  "direction": "in",
  "path": "MyEventHub",
  "connection": "myEventHubReadConnectionAppSetting"
}
```
```json
{
  "type": "eventHubTrigger",
  "name": "myEventHubMessage",
  "direction": "in",
  "eventHubName": "MyEventHub",
  "connection": "myEventHubReadConnectionAppSetting"
}
```

C# スクリプト コードを次に示します。

```cs
using System;

public static void Run(string myEventHubMessage, TraceWriter log)
{
    log.Info($"C# Event Hub trigger function processed a message: {myEventHubMessage}");
}
```

関数コードの[イベント メタデータ](#trigger---event-metadata)にアクセスするには、(`Microsoft.ServiceBus.Messaging` の using ステートメントを必要とする) [EventData](/dotnet/api/microsoft.servicebus.messaging.eventdata) オブジェクトにバインドします。 また、メソッド シグネチャ内のバインディング式を使用して、同じプロパティにアクセスすることもできます。  次の例は、同じデータを取得する 2 つの方法を示しています。

```cs
#r "Microsoft.ServiceBus"
using System.Text;
using System;
using Microsoft.ServiceBus.Messaging;

public static void Run(EventData myEventHubMessage,
    DateTime enqueuedTimeUtc, 
    Int64 sequenceNumber,
    string offset,
    TraceWriter log)
{
    log.Info($"Event: {Encoding.UTF8.GetString(myEventHubMessage.GetBytes())}");
    // Metadata accessed by binding to EventData
    log.Info($"EnqueuedTimeUtc={myEventHubMessage.EnqueuedTimeUtc}");
    log.Info($"SequenceNumber={myEventHubMessage.SequenceNumber}");
    log.Info($"Offset={myEventHubMessage.Offset}");
    // Metadata accessed by using binding expressions
    log.Info($"EnqueuedTimeUtc={enqueuedTimeUtc}");
    log.Info($"SequenceNumber={sequenceNumber}");
    log.Info($"Offset={offset}");
}
```

イベントを一括で受け取るには、`string` または `EventData` を配列にします。

```cs
public static void Run(string[] eventHubMessages, TraceWriter log)
{
    foreach (var message in eventHubMessages)
    {
        log.Info($"C# Event Hub trigger function processed a message: {message}");
    }
}
```

### <a name="trigger---f-example"></a>トリガー - F# の例

次の例は、*function.json* ファイルのイベント ハブ トリガー バインドと、そのバインドが使用される [F# 関数](functions-reference-fsharp.md)を示しています。 この関数では、イベント ハブ トリガーのメッセージ本文を記録します。

次の例は、*function.json* ファイル内の Event Hubs バインディング データを示しています。 最初の例は Functions 1.x 用で、2 つ目の例は Functions 2.x 用です。 

```json
{
  "type": "eventHubTrigger",
  "name": "myEventHubMessage",
  "direction": "in",
  "path": "MyEventHub",
  "connection": "myEventHubReadConnectionAppSetting"
}
```
```json
{
  "type": "eventHubTrigger",
  "name": "myEventHubMessage",
  "direction": "in",
  "eventHubName": "MyEventHub",
  "connection": "myEventHubReadConnectionAppSetting"
}
```

F# コードを次に示します。

```fsharp
let Run(myEventHubMessage: string, log: TraceWriter) =
    log.Info(sprintf "F# eventhub trigger function processed work item: %s" myEventHubMessage)
```

### <a name="trigger---javascript-example"></a>トリガー - JavaScript の例

次の例は、*function.json* ファイルのイベント ハブ トリガー バインドと、そのバインドが使用される [JavaScript 関数](functions-reference-node.md)を示しています。 この関数は、[イベント メタデータ](#trigger---event-metadata)を読み取り、メッセージをログに記録します。

次の例は、*function.json* ファイル内の Event Hubs バインディング データを示しています。 最初の例は Functions 1.x 用で、2 つ目の例は Functions 2.x 用です。 

```json
{
  "type": "eventHubTrigger",
  "name": "myEventHubMessage",
  "direction": "in",
  "path": "MyEventHub",
  "connection": "myEventHubReadConnectionAppSetting"
}
```
```json
{
  "type": "eventHubTrigger",
  "name": "myEventHubMessage",
  "direction": "in",
  "eventHubName": "MyEventHub",
  "connection": "myEventHubReadConnectionAppSetting"
}
```

JavaScript コードを次に示します。

```javascript
module.exports = function (context, eventHubMessage) {
    context.log('Event Hubs trigger function processed message: ', myEventHubMessage);
    context.log('EnqueuedTimeUtc =', context.bindingData.enqueuedTimeUtc);
    context.log('SequenceNumber =', context.bindingData.sequenceNumber);
    context.log('Offset =', context.bindingData.offset);
     
    context.done();
};
```

イベントをまとめて受け取るには、次の例に示すように、*function.json* ファイルで `cardinality` を `many` に設定します。 最初の例は Functions 1.x 用で、2 つ目の例は Functions 2.x 用です。 

```json
{
  "type": "eventHubTrigger",
  "name": "eventHubMessages",
  "direction": "in",
  "path": "MyEventHub",
  "cardinality": "many",
  "connection": "myEventHubReadConnectionAppSetting"
}
```
```json
{
  "type": "eventHubTrigger",
  "name": "eventHubMessages",
  "direction": "in",
  "eventHubName": "MyEventHub",
  "cardinality": "many",
  "connection": "myEventHubReadConnectionAppSetting"
}
```

JavaScript コードを次に示します。

```javascript
module.exports = function (context, eventHubMessages) {
    context.log(`JavaScript eventhub trigger function called for message array ${eventHubMessages}`);
    
    eventHubMessages.forEach((message, index) => {
        context.log(`Processed message ${message}`);
        context.log(`EnqueuedTimeUtc = ${context.bindingData.enqueuedTimeUtcArray[index]}`);
        context.log(`SequenceNumber = ${context.bindingData.sequenceNumberArray[index]}`);
        context.log(`Offset = ${context.bindingData.offsetArray[index]}`);
    });

    context.done();
};
```

## <a name="trigger---attributes"></a>トリガー - 属性

[C# クラス ライブラリ](functions-dotnet-class-library.md)では、[EventHubTriggerAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.ServiceBus/EventHubs/EventHubTriggerAttribute.cs) 属性を使用します。

この属性のコンストラクターは、イベント ハブの名前とコンシューマー グループの名前のほか、接続文字列が含まれたアプリ設定の名前を受け取ります。 これらの設定の詳細については、「[トリガー - 構成](#trigger---configuration)」セクションを参照してください。 `EventHubTriggerAttribute` 属性の例を次に示します。

```csharp
[FunctionName("EventHubTriggerCSharp")]
public static void Run([EventHubTrigger("samples-workitems", Connection = "EventHubConnectionAppSetting")] string myEventHubMessage, TraceWriter log)
{
    ...
}
```

完全な例については、「[トリガー - C# の例](#trigger---c-example)」を参照してください。

## <a name="trigger---configuration"></a>トリガー - 構成

次の表は、*function.json* ファイルと `EventHubTrigger` 属性で設定したバインド構成のプロパティを説明しています。

|function.json のプロパティ | 属性のプロパティ |説明|
|---------|---------|----------------------|
|**type** | 該当なし | `eventHubTrigger` に設定する必要があります。 このプロパティは、Azure Portal でトリガーを作成するときに自動で設定されます。|
|**direction** | 該当なし | `in` に設定する必要があります。 このプロパティは、Azure Portal でトリガーを作成するときに自動で設定されます。 |
|**name** | 該当なし | 関数コード内のイベント項目を表す変数の名前。 | 
|**path** |**EventHubName** | Functions 1.x のみ。 イベント ハブの名前。 イベント ハブの名前は接続文字列にも存在し、その値が実行時にこのプロパティをオーバーライドします。 | 
|**eventHubName** |**EventHubName** | Functions 2.x のみ。 イベント ハブの名前。 イベント ハブの名前は接続文字列にも存在し、その値が実行時にこのプロパティをオーバーライドします。 |
|**consumerGroup** |**ConsumerGroup** | ハブのイベントのサブスクライブに使用される[コンシューマー グループ](../event-hubs/event-hubs-features.md#event-consumers)を設定する、省略可能なプロパティ。 省略した場合は、`$Default` コンシューマー グループが使用されます。 | 
|**cardinality** | 該当なし | Javascript 用。 バッチ処理を有効にするには `many` に設定します。  省略するか、`one` に設定した場合、1 つのメッセージが関数に渡されます。 | 
|**connection** |**Connection** | イベント ハブの名前空間への接続文字列が含まれたアプリ設定の名前。 この接続文字列をコピーするには、イベント ハブ自体ではなく、"[名前空間](../event-hubs/event-hubs-create.md#create-an-event-hubs-namespace)" の **[接続情報]** をクリックします。 この接続文字列には、トリガーをアクティブにするために少なくとも読み取りアクセス許可が必要です。|

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="trigger---event-metadata"></a>トリガー - イベント メタデータ

Event Hubs トリガーには、いくつかの[メタデータ プロパティ](functions-triggers-bindings.md#binding-expressions---trigger-metadata)があります。 これらのプロパティは、他のバインドのバインド式の一部として、またはコードのパラメーターとして使用できます。 これらは [EventData](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.eventdata) クラスのプロパティです。

|プロパティ|種類|説明|
|--------|----|-----------|
|`PartitionContext`|[PartitionContext](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.partitioncontext)|`PartitionContext` インスタンス。|
|`EnqueuedTimeUtc`|`DateTime`|エンキューされた時刻 (UTC)。|
|`Offset`|`string`|イベント ハブ パーティション ストリームを基準としたデータのオフセット。 オフセットは、Event Hubs ストリーム内のイベントのマーカーまたは識別子です。 この識別子は、Event Hubs ストリームのパーティション内で一意です。|
|`PartitionKey`|`string`|イベント データを送信するパーティション。|
|`Properties`|`IDictionary<String,Object>`|イベント データのユーザー プロパティ。|
|`SequenceNumber`|`Int64`|イベントの論理シーケンス番号。|
|`SystemProperties`|`IDictionary<String,Object>`|イベント データなどのシステム プロパティ。|

この記事の前半でこれらのプロパティを使用している[コード例](#trigger---example)を参照してください。

## <a name="trigger---hostjson-properties"></a>トリガー - host.json のプロパティ

[host.json](functions-host-json.md#eventhub) ファイルには、Event Hubs トリガーの動作を制御する設定が含まれています。

[!INCLUDE [functions-host-json-event-hubs](../../includes/functions-host-json-event-hubs.md)]

## <a name="output"></a>出力

Event Hubs 出力バインドを使用して、イベント ストリームにイベントを書き込みます。 イベントを書き込むには、イベント ハブへの送信アクセス許可が必要です。

必要なパッケージ参照 ([Functions 1.x](#packages---functions-1.x) または [Functions 2.x](#packages---functions-2.x)) が用意されていることを確認してください。 

## <a name="output---example"></a>出力 - 例

言語固有の例をご覧ください。

* [C#](#output---c-example)
* [C# スクリプト (.csx)](#output---c-script-example)
* [F#](#output---f-example)
* [JavaScript](#output---javascript-example)

### <a name="output---c-example"></a>出力 - C# の例

次の例は、メソッドの戻り値を出力として使用してメッセージをイベント ハブに書き込む [C# 関数](functions-dotnet-class-library.md)を示しています。

```csharp
[FunctionName("EventHubOutput")]
[return: EventHub("outputEventHubMessage", Connection = "EventHubConnectionAppSetting")]
public static string Run([TimerTrigger("0 */5 * * * *")] TimerInfo myTimer, TraceWriter log)
{
    log.Info($"C# Timer trigger function executed at: {DateTime.Now}");
    return $"{DateTime.Now}";
}
```

### <a name="output---c-script-example"></a>出力 - C# スクリプトの例

次の例は、*function.json* ファイルのイベント ハブ トリガー バインドと、そのバインドが使用される [C# スクリプト関数](functions-reference-csharp.md)を示しています。 この関数では、メッセージをイベント ハブに書き込みます。

次の例は、*function.json* ファイル内の Event Hubs バインディング データを示しています。 最初の例は Functions 1.x 用で、2 つ目の例は Functions 2.x 用です。 

```json
{
    "type": "eventHub",
    "name": "outputEventHubMessage",
    "path": "myeventhub",
    "connection": "MyEventHubSendAppSetting",
    "direction": "out"
}
```
```json
{
    "type": "eventHub",
    "name": "outputEventHubMessage",
    "eventHubName": "myeventhub",
    "connection": "MyEventHubSendAppSetting",
    "direction": "out"
}
```

1 つのメッセージを作成する C# スクリプト コードを次に示します。

```cs
using System;

public static void Run(TimerInfo myTimer, out string outputEventHubMessage, TraceWriter log)
{
    String msg = $"TimerTriggerCSharp1 executed at: {DateTime.Now}";
    log.Verbose(msg);   
    outputEventHubMessage = msg;
}
```

複数のメッセージを作成する C# スクリプト コードを次に示します。

```cs
public static void Run(TimerInfo myTimer, ICollector<string> outputEventHubMessage, TraceWriter log)
{
    string message = $"Event Hub message created at: {DateTime.Now}";
    log.Info(message);
    outputEventHubMessage.Add("1 " + message);
    outputEventHubMessage.Add("2 " + message);
}
```

### <a name="output---f-example"></a>出力 - F# の例

次の例は、*function.json* ファイルのイベント ハブ トリガー バインドと、そのバインドが使用される [F# 関数](functions-reference-fsharp.md)を示しています。 この関数では、メッセージをイベント ハブに書き込みます。

次の例は、*function.json* ファイル内の Event Hubs バインディング データを示しています。 最初の例は Functions 1.x 用で、2 つ目の例は Functions 2.x 用です。 

```json
{
    "type": "eventHub",
    "name": "outputEventHubMessage",
    "path": "myeventhub",
    "connection": "MyEventHubSendAppSetting",
    "direction": "out"
}
```
```json
{
    "type": "eventHub",
    "name": "outputEventHubMessage",
    "eventHubName": "myeventhub",
    "connection": "MyEventHubSendAppSetting",
    "direction": "out"
}
```

F# コードを次に示します。

```fsharp
let Run(myTimer: TimerInfo, outputEventHubMessage: byref<string>, log: TraceWriter) =
    let msg = sprintf "TimerTriggerFSharp1 executed at: %s" DateTime.Now.ToString()
    log.Verbose(msg);
    outputEventHubMessage <- msg;
```

### <a name="output---javascript-example"></a>出力 - JavaScript の例

次の例は、*function.json* ファイルのイベント ハブ トリガー バインドと、そのバインドが使用される [JavaScript 関数](functions-reference-node.md)を示しています。 この関数では、メッセージをイベント ハブに書き込みます。

次の例は、*function.json* ファイル内の Event Hubs バインディング データを示しています。 最初の例は Functions 1.x 用で、2 つ目の例は Functions 2.x 用です。 

```json
{
    "type": "eventHub",
    "name": "outputEventHubMessage",
    "path": "myeventhub",
    "connection": "MyEventHubSendAppSetting",
    "direction": "out"
}
```
```json
{
    "type": "eventHub",
    "name": "outputEventHubMessage",
    "eventHubName": "myeventhub",
    "connection": "MyEventHubSendAppSetting",
    "direction": "out"
}
```

1 つのメッセージを送信する JavaScript コードを次に示します。

```javascript
module.exports = function (context, myTimer) {
    var timeStamp = new Date().toISOString();
    context.log('Event Hub message created at: ', timeStamp);   
    context.bindings.outputEventHubMessage = "Event Hub message created at: " + timeStamp;
    context.done();
};
```

複数のメッセージを送信する JavaScript コードを次に示します。

```javascript
module.exports = function(context) {
    var timeStamp = new Date().toISOString();
    var message = 'Event Hub message created at: ' + timeStamp;

    context.bindings.outputEventHubMessage = [];

    context.bindings.outputEventHubMessage.push("1 " + message);
    context.bindings.outputEventHubMessage.push("2 " + message);
    context.done();
};
```

## <a name="output---attributes"></a>出力 - 属性

[C# クラス ライブラリ](functions-dotnet-class-library.md)では、[EventHubAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.ServiceBus/EventHubs/EventHubAttribute.cs) 属性を使用します。

この属性のコンストラクターは、イベント ハブの名前のほか、接続文字列が含まれたアプリ設定の名前を受け取ります。 これらの設定の詳細については、「[出力 - 構成](#output---configuration)」を参照してください。 `EventHub` 属性の例を次に示します。

```csharp
[FunctionName("EventHubOutput")]
[return: EventHub("outputEventHubMessage", Connection = "EventHubConnectionAppSetting")]
public static string Run([TimerTrigger("0 */5 * * * *")] TimerInfo myTimer, TraceWriter log)
{
    ...
}
```

完全な例については、「[出力 - C# の例](#output---c-example)」を参照してください。

## <a name="output---configuration"></a>出力 - 構成

次の表は、*function.json* ファイルと `EventHub` 属性で設定したバインド構成のプロパティを説明しています。

|function.json のプロパティ | 属性のプロパティ |説明|
|---------|---------|----------------------|
|**type** | 該当なし | "eventHub" に設定する必要があります。 |
|**direction** | 該当なし | "out" に設定する必要があります。 このパラメーターは、Azure Portal でバインドを作成するときに自動で設定されます。 |
|**name** | 該当なし | イベントを表す関数コードに使用される変数の名前。 | 
|**path** |**EventHubName** | Functions 1.x のみ。 イベント ハブの名前。 イベント ハブの名前は接続文字列にも存在し、その値が実行時にこのプロパティをオーバーライドします。 | 
|**eventHubName** |**EventHubName** | Functions 2.x のみ。 イベント ハブの名前。 イベント ハブの名前は接続文字列にも存在し、その値が実行時にこのプロパティをオーバーライドします。 |
|**connection** |**Connection** | イベント ハブの名前空間への接続文字列が含まれたアプリ設定の名前。 この接続文字列をコピーするには、イベント ハブ自体ではなく、"*名前空間*" の **[接続情報]** をクリックします。 この接続文字列には、イベント ストリームにメッセージを送信するための送信アクセス許可が必要です。|

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="output---usage"></a>出力 - 使用方法

C# および C# スクリプトでは、`out string paramName` などのメソッド パラメーターを使用してメッセージを送信します。 C# スクリプトでは、`paramName` は *function.json* の `name` プロパティで指定された値です。 複数のメッセージを書き込むには、`out string` の代わりに `ICollector<string>` または `IAsyncCollector<string>` を使用します。

JavaScript では、`context.bindings.<name>` を使用して出力イベントにアクセスします。 `<name>` は *function.json* の `name` プロパティで指定された値です。

## <a name="exceptions-and-return-codes"></a>例外とリターン コード

| バインド | リファレンス |
|---|---|
| イベント ハブ | [運用ガイド](https://docs.microsoft.com/rest/api/eventhub/publisher-policy-operations) |

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [Azure Functions のトリガーとバインドの詳細情報](functions-triggers-bindings.md)
