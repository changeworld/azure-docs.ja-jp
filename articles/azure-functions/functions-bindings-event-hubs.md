---
title: Azure Functions における Azure Event Hubs のバインド
description: Azure Functions で Azure Event Hubs のバインドを使用する方法について説明します。
services: functions
documentationcenter: na
author: craigshoemaker
manager: jeconnoc
keywords: Azure Functions, 関数, イベント処理, 動的コンピューティング, サーバーなしのアーキテクチャ
ms.assetid: daf81798-7acc-419a-bc32-b5a41c6db56b
ms.service: azure-functions
ms.devlang: multiple
ms.topic: reference
ms.date: 11/08/2017
ms.author: cshoe
ms.openlocfilehash: bc7ed9051f95877760bccec65ff2fa7f49e44993
ms.sourcegitcommit: 2469b30e00cbb25efd98e696b7dbf51253767a05
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/06/2018
ms.locfileid: "53002155"
---
# <a name="azure-event-hubs-bindings-for-azure-functions"></a>Azure Functions における Azure Event Hubs のバインド

この記事では、Azure Functions で [Azure Event Hubs](../event-hubs/event-hubs-what-is-event-hubs.md) のバインドを使用する方法について説明します。 Azure Functions は、イベント ハブのトリガーおよび出力バインドをサポートしています。

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

## <a name="packages---functions-1x"></a>パッケージ - Functions 1.x

Azure Functions バージョン 1.x の場合、Event Hubs バインディングは [Microsoft.Azure.WebJobs.ServiceBus](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.ServiceBus) NuGet パッケージ、バージョン 2.x で提供されます。
パッケージのソース コードは、[azure-webjobs-sdk](https://github.com/Azure/azure-webjobs-sdk/tree/v2.x/src/Microsoft.Azure.WebJobs.ServiceBus/EventHubs) GitHub リポジトリにあります。


[!INCLUDE [functions-package](../../includes/functions-package.md)]

## <a name="packages---functions-2x"></a>パッケージ - Functions 2.x

Functions 2.x の場合、[Microsoft.Azure.WebJobs.Extensions.EventHubs](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.EventHubs) パッケージ、バージョン 3.x を使用します。
パッケージのソース コードは、[azure-webjobs-sdk](https://github.com/Azure/azure-webjobs-sdk/tree/master/src/Microsoft.Azure.WebJobs.Extensions.EventHubs) GitHub リポジトリにあります。

[!INCLUDE [functions-package-v2](../../includes/functions-package-v2.md)]

## <a name="trigger"></a>トリガー

イベント ハブ トリガーを使用して、イベント ハブのイベント ストリームに送信されたイベントに応答します。 トリガーをセットアップするには、イベント ハブへの読み取りアクセスが必要です。

イベント ハブ トリガー関数がトリガーされると、それをトリガーするメッセージが文字列として関数に渡されます。

## <a name="trigger---scaling"></a>トリガー - スケーリング

Event Hub-Triggered 関数の各インスタンスは、[EventProcessorHost](https://docs.microsoft.com/dotnet/api/microsoft.azure.eventhubs.processor) インスタンスを 1 つのみ使用します。 Event Hubs では、1 つの [EventProcessorHost](https://docs.microsoft.com/dotnet/api/microsoft.azure.eventhubs.processor) インスタンスのみが特定のパーティションのリースを取得できます。

たとえば、次のようなイベント ハブを検討します。

* 10 パーティション。
* 1000 イベントがすべてのパーティション間で均等に分散され、各パーティション内に 100 件のメッセージがある。

関数が最初に有効化されたときに存在する関数インスタンスは 1 つのみです。 この関数インスタンス `Function_0` を呼び出しましょう。 `Function_0` の単一の [EventProcessorHost](https://docs.microsoft.com/dotnet/api/microsoft.azure.eventhubs.processor) インスタンスは、10 個のすべてのパーティション上に 1 つのリースを持ちます。 このインスタンスは、パーティション 0 ～ 9 からイベントを読み取ります。 この後、次のいずれかが発生します。

* **新しい関数インスタンスが要**: `Function_0` は、Functions のスケーリングのロジックが開始される前に 1000 イベントすべてを処理することができます。 この場合、1000 メッセージすべてが、`Function_0` によって処理されます。

* **別の関数インスタンスが追加される**:Functions のスケーリングのロジックにより、`Function_0` が、処理能力を超える数のメッセージを持っていると判断されます。 この場合、新しい関数アプリのインスタンス (`Function_1`) が、新しい [EventProcessorHost](https://docs.microsoft.com/dotnet/api/microsoft.azure.eventhubs.processor) インスタンスとともに作成されます。 Event Hubs は、新しいホスト インスタンスがメッセージを読み取ろうとしていることを検知します。 Event Hubs は、そのホスト インスタンス全体でパーティションを負荷分散します。 たとえば、パーティション 0 ～ 4 を `Function_0` に割り当て、パーティション 5 ～ 9 を `Function_1` に割り当てることができます。

* **さらに N 個の関数インスタンスが追加される**:Functions のスケーリングのロジックにより、`Function_0` と `Function_1` の両方が、処理能力を超える数のメッセージを持っていると判断されます。 新しい関数アプリ のインスタンス `Function_2`...`Functions_N` が作成されます。ここで、`N` は、イベント ハブのパーティション数より大きい数値です。 この例では、Event Hubs は、パーティションを再び負荷分散します。この場合、 `Function_0`...`Functions_9` のインスタンス間で負荷分散します。

Functions がスケーリングするインスタンスの数 `N` が、イベント ハブのパーティション数より多いときは注意してください。 これは、[EventProcessorHost](https://docs.microsoft.com/dotnet/api/microsoft.azure.eventhubs.processor) インスタンスが、他のインスタンスから解放されて使用可能になったパーティションのロックを必ず取得できるようにするためです。 ユーザーには、関数インスタンスが実行されたときに使用されたリソースに対してのみ料金が発生し、オーバー プロビジョニングには課金されません。

すべての関数の実行が (エラーの有無にかかわらず) 完了すると、関連付けられているストレージ アカウントにチェックポイントが追加されます。 チェックポイントが成功した場合、1000 件のすべてのメッセージが再取得されることはありません。

## <a name="trigger---example"></a>トリガー - 例

言語固有の例をご覧ください。

* [C#](#trigger---c-example)
* [C# スクリプト (.csx)](#trigger---c-script-example)
* [F#](#trigger---f-example)
* [Java](#trigger---java-example)
* [JavaScript](#trigger---javascript-example)
* [Python](#trigger---python-example)

### <a name="trigger---c-example"></a>トリガー - C# の例

次の例は、イベント ハブ トリガーのメッセージ本文をログに記録する [C# 関数](functions-dotnet-class-library.md)を示しています。

```csharp
[FunctionName("EventHubTriggerCSharp")]
public static void Run([EventHubTrigger("samples-workitems", Connection = "EventHubConnectionAppSetting")] string myEventHubMessage, ILogger log)
{
    log.LogInformation($"C# Event Hub trigger function processed a message: {myEventHubMessage}");
}
```

関数コードの[イベント メタデータ](#trigger---event-metadata)にアクセスするには、(`Microsoft.Azure.EventHubs` の using ステートメントを必要とする) [EventData](/dotnet/api/microsoft.servicebus.messaging.eventdata) オブジェクトにバインドします。 また、メソッド シグネチャ内のバインディング式を使用して、同じプロパティにアクセスすることもできます。  次の例は、同じデータを取得する 2 つの方法を示しています。

```csharp
[FunctionName("EventHubTriggerCSharp")]
public static void Run(
    [EventHubTrigger("samples-workitems", Connection = "EventHubConnectionAppSetting")] EventData myEventHubMessage,
    DateTime enqueuedTimeUtc,
    Int64 sequenceNumber,
    string offset,
    ILogger log)
{
    log.LogInformation($"Event: {Encoding.UTF8.GetString(myEventHubMessage.Body)}");
    // Metadata accessed by binding to EventData
    log.LogInformation($"EnqueuedTimeUtc={myEventHubMessage.SystemProperties.EnqueuedTimeUtc}");
    log.LogInformation($"SequenceNumber={myEventHubMessage.SystemProperties.SequenceNumber}");
    log.LogInformation($"Offset={myEventHubMessage.SystemProperties.Offset}");
    // Metadata accessed by using binding expressions in method parameters
    log.LogInformation($"EnqueuedTimeUtc={enqueuedTimeUtc}");
    log.LogInformation($"SequenceNumber={sequenceNumber}");
    log.LogInformation($"Offset={offset}");
}
```

イベントを一括で受け取るには、`string` または `EventData` を配列にします。  

> [!NOTE]
> 一括で受け取る場合、上記の例のように `DateTime enqueuedTimeUtc` を使用してメソッド パラメーターをバインドすることはできないため、各 `EventData` オブジェクトからパラメーターを受け取る必要があります。  

```cs
[FunctionName("EventHubTriggerCSharp")]
public static void Run([EventHubTrigger("samples-workitems", Connection = "EventHubConnectionAppSetting")] EventData[] eventHubMessages, ILogger log)
{
    foreach (var message in eventHubMessages)
    {
        log.LogInformation($"C# Event Hub trigger function processed a message: {Encoding.UTF8.GetString(message.Body)}");
        log.LogInformation($"EnqueuedTimeUtc={message.SystemProperties.EnqueuedTimeUtc}");
    }
}
```

### <a name="trigger---c-script-example"></a>トリガー - C# スクリプトの例

次の例は、*function.json* ファイルのイベント ハブ トリガー バインドと、そのバインドが使用される [C# スクリプト関数](functions-reference-csharp.md)を示しています。 この関数では、イベント ハブ トリガーのメッセージ本文を記録します。

次の例は、*function.json* ファイル内の Event Hubs バインディング データを示しています。

#### <a name="version-2x"></a>バージョン 2.x

```json
{
  "type": "eventHubTrigger",
  "name": "myEventHubMessage",
  "direction": "in",
  "eventHubName": "MyEventHub",
  "connection": "myEventHubReadConnectionAppSetting"
}
```

#### <a name="version-1x"></a>バージョン 1.x

```json
{
  "type": "eventHubTrigger",
  "name": "myEventHubMessage",
  "direction": "in",
  "path": "MyEventHub",
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

関数コードの[イベント メタデータ](#trigger---event-metadata)にアクセスするには、(`Microsoft.Azure.EventHubs` の using ステートメントを必要とする) [EventData](/dotnet/api/microsoft.servicebus.messaging.eventdata) オブジェクトにバインドします。 また、メソッド シグネチャ内のバインディング式を使用して、同じプロパティにアクセスすることもできます。  次の例は、同じデータを取得する 2 つの方法を示しています。

```cs
#r "Microsoft.Azure.EventHubs"

using System.Text;
using System;
using Microsoft.ServiceBus.Messaging;
using Microsoft.Azure.EventHubs;

public static void Run(EventData myEventHubMessage,
    DateTime enqueuedTimeUtc,
    Int64 sequenceNumber,
    string offset,
    TraceWriter log)
{
    log.Info($"Event: {Encoding.UTF8.GetString(myEventHubMessage.Body)}");
    log.Info($"EnqueuedTimeUtc={myEventHubMessage.SystemProperties.EnqueuedTimeUtc}");
    log.Info($"SequenceNumber={myEventHubMessage.SystemProperties.SequenceNumber}");
    log.Info($"Offset={myEventHubMessage.SystemProperties.Offset}");

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

次の例は、*function.json* ファイル内の Event Hubs バインディング データを示しています。 

#### <a name="version-2x"></a>バージョン 2.x

```json
{
  "type": "eventHubTrigger",
  "name": "myEventHubMessage",
  "direction": "in",
  "eventHubName": "MyEventHub",
  "connection": "myEventHubReadConnectionAppSetting"
}
```

#### <a name="version-1x"></a>バージョン 1.x

```json
{
  "type": "eventHubTrigger",
  "name": "myEventHubMessage",
  "direction": "in",
  "path": "MyEventHub",
  "connection": "myEventHubReadConnectionAppSetting"
}
```

F# コードを次に示します。

```fsharp
let Run(myEventHubMessage: string, log: TraceWriter) =
    log.Log(sprintf "F# eventhub trigger function processed work item: %s" myEventHubMessage)
```

### <a name="trigger---javascript-example"></a>トリガー - JavaScript の例

次の例は、*function.json* ファイルのイベント ハブ トリガー バインドと、そのバインドが使用される [JavaScript 関数](functions-reference-node.md)を示しています。 この関数は、[イベント メタデータ](#trigger---event-metadata)を読み取り、メッセージをログに記録します。

次の例は、*function.json* ファイル内の Event Hubs バインディング データを示しています。

#### <a name="version-2x"></a>バージョン 2.x

```json
{
  "type": "eventHubTrigger",
  "name": "myEventHubMessage",
  "direction": "in",
  "eventHubName": "MyEventHub",
  "connection": "myEventHubReadConnectionAppSetting"
}
```

#### <a name="version-1x"></a>バージョン 1.x

```json
{
  "type": "eventHubTrigger",
  "name": "myEventHubMessage",
  "direction": "in",
  "path": "MyEventHub",
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

イベントをまとめて受け取るには、次の例に示すように、*function.json* ファイルで `cardinality` を `many` に設定します。

#### <a name="version-2x"></a>バージョン 2.x

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

#### <a name="version-1x"></a>バージョン 1.x

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

### <a name="trigger---python-example"></a>トリガー - Python の例

次の例は、*function.json* ファイルのイベント ハブ トリガー バインドと、そのバインドが使用される [Python 関数](functions-reference-python.md)を示しています。 この関数は、[イベント メタデータ](#trigger---event-metadata)を読み取り、メッセージをログに記録します。

次の例は、*function.json* ファイル内の Event Hubs バインディング データを示しています。

```json
{
  "type": "eventHubTrigger",
  "name": "event",
  "direction": "in",
  "eventHubName": "MyEventHub",
  "connection": "myEventHubReadConnectionAppSetting"
}
```

Python コードを次に示します。

```python
import logging
import azure.functions as func

def main(event: func.EventHubEvent):
    logging.info('Event Hubs trigger function processed message: ', event.get_body())
    logging.info('  EnqueuedTimeUtc =', event.enqueued_time)
    logging.info('  SequenceNumber =', event.sequence_number)
    logging.info('  Offset =', event.offset)
```

### <a name="trigger---java-example"></a>トリガー - Java の例

次の例は、*function.json* ファイルの Event Hub トリガー バインドと、そのバインドが使用される [Java 関数](functions-reference-java.md)を示しています。 この関数では、Event Hub トリガーのメッセージ本文を記録します。

```json
{
  "type": "eventHubTrigger",
  "name": "msg",
  "direction": "in",
  "eventHubName": "myeventhubname",
  "connection": "myEventHubReadConnectionAppSetting"
}
```

```java
@FunctionName("ehprocessor")
public void eventHubProcessor(
  @EventHubTrigger(name = "msg",
                  eventHubName = "myeventhubname",
                  connection = "myconnvarname") String message,
       final ExecutionContext context )
       {
          context.getLogger().info(message);
 }
 ```

 [Java 関数ランタイム ライブラリ](/java/api/overview/azure/functions/runtime)で、その値が Event Hub に由来するパラメーター上で `EventHubTrigger` 注釈を使用します。 これらの注釈を使用したパラメーターによって、イベントを受信したときに関数が実行されます。  この注釈は、Java のネイティブ型、POJO、または Optional<T> を使用した null 許容値で使用できます。

## <a name="trigger---attributes"></a>トリガー - 属性

[C# クラス ライブラリ](functions-dotnet-class-library.md)では、[EventHubTriggerAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.Extensions.EventHubs/EventHubTriggerAttribute.cs) 属性を使用します。

この属性のコンストラクターは、イベント ハブの名前とコンシューマー グループの名前のほか、接続文字列が含まれたアプリ設定の名前を受け取ります。 これらの設定の詳細については、「[トリガー - 構成](#trigger---configuration)」セクションを参照してください。 `EventHubTriggerAttribute` 属性の例を次に示します。

```csharp
[FunctionName("EventHubTriggerCSharp")]
public static void Run([EventHubTrigger("samples-workitems", Connection = "EventHubConnectionAppSetting")] string myEventHubMessage, ILogger log)
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

|プロパティ|type|説明|
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

必要なパッケージ参照が用意されていることを確認します:[Functions 1.x](#packages---functions-1.x) または [Functions 2.x](#packages---functions-2.x)

## <a name="output---example"></a>出力 - 例

言語固有の例をご覧ください。

* [C#](#output---c-example)
* [C# スクリプト (.csx)](#output---c-script-example)
* [F#](#output---f-example)
* [Java](#output---java-example)
* [JavaScript](#output---javascript-example)
* [Python](#output---python-example)

### <a name="output---c-example"></a>出力 - C# の例

次の例は、メソッドの戻り値を出力として使用してメッセージをイベント ハブに書き込む [C# 関数](functions-dotnet-class-library.md)を示しています。

```csharp
[FunctionName("EventHubOutput")]
[return: EventHub("outputEventHubMessage", Connection = "EventHubConnectionAppSetting")]
public static string Run([TimerTrigger("0 */5 * * * *")] TimerInfo myTimer, ILogger log)
{
    log.LogInformation($"C# Timer trigger function executed at: {DateTime.Now}");
    return $"{DateTime.Now}";
}
```

### <a name="output---c-script-example"></a>出力 - C# スクリプトの例

次の例は、*function.json* ファイルのイベント ハブ トリガー バインドと、そのバインドが使用される [C# スクリプト関数](functions-reference-csharp.md)を示しています。 この関数では、メッセージをイベント ハブに書き込みます。

次の例は、*function.json* ファイル内の Event Hubs バインディング データを示しています。 最初の例は Functions 2.x 用で、2 つ目の例は Functions 1.x 用です。 

```json
{
    "type": "eventHub",
    "name": "outputEventHubMessage",
    "eventHubName": "myeventhub",
    "connection": "MyEventHubSendAppSetting",
    "direction": "out"
}
```

```json
{
    "type": "eventHub",
    "name": "outputEventHubMessage",
    "path": "myeventhub",
    "connection": "MyEventHubSendAppSetting",
    "direction": "out"
}
```

1 つのメッセージを作成する C# スクリプト コードを次に示します。

```cs
using System;
using Microsoft.Extensions.Logging;

public static void Run(TimerInfo myTimer, out string outputEventHubMessage, ILogger log)
{
    String msg = $"TimerTriggerCSharp1 executed at: {DateTime.Now}";
    log.LogInformation(msg);   
    outputEventHubMessage = msg;
}
```

複数のメッセージを作成する C# スクリプト コードを次に示します。

```cs
public static void Run(TimerInfo myTimer, ICollector<string> outputEventHubMessage, ILogger log)
{
    string message = $"Event Hub message created at: {DateTime.Now}";
    log.LogInformation(message);
    outputEventHubMessage.Add("1 " + message);
    outputEventHubMessage.Add("2 " + message);
}
```

### <a name="output---f-example"></a>出力 - F# の例

次の例は、*function.json* ファイルのイベント ハブ トリガー バインドと、そのバインドが使用される [F# 関数](functions-reference-fsharp.md)を示しています。 この関数では、メッセージをイベント ハブに書き込みます。

次の例は、*function.json* ファイル内の Event Hubs バインディング データを示しています。 最初の例は Functions 2.x 用で、2 つ目の例は Functions 1.x 用です。 

```json
{
    "type": "eventHub",
    "name": "outputEventHubMessage",
    "eventHubName": "myeventhub",
    "connection": "MyEventHubSendAppSetting",
    "direction": "out"
}
```
```json
{
    "type": "eventHub",
    "name": "outputEventHubMessage",
    "path": "myeventhub",
    "connection": "MyEventHubSendAppSetting",
    "direction": "out"
}
```

F# コードを次に示します。

```fsharp
let Run(myTimer: TimerInfo, outputEventHubMessage: byref<string>, log: ILogger) =
    let msg = sprintf "TimerTriggerFSharp1 executed at: %s" DateTime.Now.ToString()
    log.LogInformation(msg);
    outputEventHubMessage <- msg;
```

### <a name="output---javascript-example"></a>出力 - JavaScript の例

次の例は、*function.json* ファイルのイベント ハブ トリガー バインドと、そのバインドが使用される [JavaScript 関数](functions-reference-node.md)を示しています。 この関数では、メッセージをイベント ハブに書き込みます。

次の例は、*function.json* ファイル内の Event Hubs バインディング データを示しています。 最初の例は Functions 2.x 用で、2 つ目の例は Functions 1.x 用です。 

```json
{
    "type": "eventHub",
    "name": "outputEventHubMessage",
    "eventHubName": "myeventhub",
    "connection": "MyEventHubSendAppSetting",
    "direction": "out"
}
```

```json
{
    "type": "eventHub",
    "name": "outputEventHubMessage",
    "path": "myeventhub",
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

### <a name="output---python-example"></a>出力 - Python の例

次の例は、*function.json* ファイルのイベント ハブ トリガー バインドと、そのバインドが使用される [Python 関数](functions-reference-python.md)を示しています。 この関数では、メッセージをイベント ハブに書き込みます。

次の例は、*function.json* ファイル内の Event Hubs バインディング データを示しています。

```json
{
    "type": "eventHub",
    "name": "$return",
    "eventHubName": "myeventhub",
    "connection": "MyEventHubSendAppSetting",
    "direction": "out"
}
```

1 つのメッセージを送信する Python コードを次に示します。

```python
import datetime
import logging
import azure.functions as func

def main(timer: func.TimerRequest) -> str:
    timestamp = datetime.datetime.utcnow()
    logging.info('Event Hub message created at: %s', timestamp);   
    return 'Event Hub message created at: {}'.format(timestamp)
```

### <a name="output---java-example"></a>出力 - Java の例

次の例は、現在の時刻を含むメッセージを Event Hub に書き込む Java 関数を示しています。

```java
@}FunctionName("sendTime")
@EventHubOutput(name = "event", eventHubName = "samples-workitems", connection = "AzureEventHubConnection")
public String sendTime(
   @TimerTrigger(name = "sendTimeTrigger", schedule = "0 *&#47;5 * * * *") String timerInfo)  {
     return LocalDateTime.now().toString();
 }
 ```

[Java 関数ランタイム ライブラリ](/java/api/overview/azure/functions/runtime)で、その値が Event Hub に公開されるパラメーター上で `@EventHubOutput` 注釈を使用します。  パラメーターの型は `OutputBinding<T>` にする必要があります。T は POJO または Java の任意のネイティブ型です。

## <a name="output---attributes"></a>出力 - 属性

[C# クラス ライブラリ](functions-dotnet-class-library.md)では、[EventHubAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.ServiceBus/EventHubs/EventHubAttribute.cs) 属性を使用します。

この属性のコンストラクターは、イベント ハブの名前のほか、接続文字列が含まれたアプリ設定の名前を受け取ります。 これらの設定の詳細については、「[出力 - 構成](#output---configuration)」を参照してください。 `EventHub` 属性の例を次に示します。

```csharp
[FunctionName("EventHubOutput")]
[return: EventHub("outputEventHubMessage", Connection = "EventHubConnectionAppSetting")]
public static string Run([TimerTrigger("0 */5 * * * *")] TimerInfo myTimer, ILogger log)
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

<a name="host-json"></a>  

## <a name="hostjson-settings"></a>host.json 設定

このセクションでは、バージョン 2.x でこのバインディングに使用可能なグローバル構成設定について説明します。 次の host.json ファイルの例には、このバインディングのバージョン 2.x の設定のみが含まれています。 バージョン 2.x でのグローバル構成設定の詳細については、[Azure Functions バージョン 2.x の host.json のリファレンス](functions-host-json.md)を参照してください。

> [!NOTE]
> Functions 1.x の host.json のリファレンスについては、「[host.json reference for Azure Functions 1.x (Azure Functions 1.x の host.json のリファレンス)](functions-host-json-v1.md)」を参照してください。

```json
{
    "version": "2.0",
    "extensions": {
        "eventHubs": {
            "batchCheckpointFrequency": 5,
            "eventProcessorOptions": {
                "maxBatchSize": 256,
                "prefetchCount": 512
            }
        }
    }
}  
```  

|プロパティ  |既定値 | 説明 |
|---------|---------|---------| 
|maxBatchSize|64|受信ループあたりで受信される最大イベント数。|
|prefetchCount|該当なし|基になる EventProcessorHost に使用される既定の PrefetchCount。| 
|batchCheckpointFrequency|1|EventHub カーソル チェックポイントを作成する前に処理するイベント バッチ数。| 

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [Azure Functions のトリガーとバインドの詳細情報](functions-triggers-bindings.md)
