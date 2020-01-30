---
title: Azure Functions における Azure Queue Storage のバインド
description: Azure Functions で Azure Queue Storage トリガーと出力バインドを使用する方法について説明します。
author: craigshoemaker
ms.topic: reference
ms.date: 09/03/2018
ms.author: cshoe
ms.custom: cc996988-fb4f-47
ms.openlocfilehash: ea213921c736bc3b6bf88c0bdd81a96656ecbe5b
ms.sourcegitcommit: 87781a4207c25c4831421c7309c03fce5fb5793f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/23/2020
ms.locfileid: "76547287"
---
# <a name="azure-queue-storage-bindings-for-azure-functions"></a>Azure Functions における Azure Queue Storage のバインド

この記事では、Azure Functions で Azure Queue Storage のバインドを使用する方法について説明します。 Azure Functions は、キューのトリガーと出力バインドをサポートしています。

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

## <a name="packages---functions-1x"></a>パッケージ - Functions 1.x

Queue ストレージ バインディングは [Microsoft.Azure.WebJobs](https://www.nuget.org/packages/Microsoft.Azure.WebJobs) NuGet パッケージのバージョン 2.x で提供されます。 パッケージのソース コードは、[azure-webjobs-sdk](https://github.com/Azure/azure-webjobs-sdk/tree/v2.x/src/Microsoft.Azure.WebJobs.Storage/Queue) GitHub リポジトリにあります。

[!INCLUDE [functions-package-auto](../../includes/functions-package-auto.md)]

[!INCLUDE [functions-storage-sdk-version](../../includes/functions-storage-sdk-version.md)]

## <a name="packages---functions-2x-and-higher"></a>パッケージ - Functions 2.x 以降

Queue ストレージ バインディングは [Microsoft.Azure.WebJobs.Extensions.Storage](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.Storage) NuGet パッケージのバージョン 3.x で提供されます。 パッケージのソース コードは、[azure-webjobs-sdk](https://github.com/Azure/azure-webjobs-sdk/tree/dev/src/Microsoft.Azure.WebJobs.Extensions.Storage/Queues) GitHub リポジトリにあります。

[!INCLUDE [functions-package-v2](../../includes/functions-package-v2.md)]

## <a name="encoding"></a>エンコード
Azure Functions で想定されているのは *base64* でエンコードされた文字列です。 (データを *base64* でエンコードされた文字列として準備するために) エンコードの種類を調整する場合、それらはすべて呼び出し元のサービスに実装する必要があります。

## <a name="trigger"></a>トリガー

キュー トリガーを使用して、キューで新しい項目を受け取ったときに関数を開始します。 キュー メッセージは、関数への入力として提供されます。

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

次の例は、キュー項目が処理されるたびに `myqueue-items` キューをポーリングし、ログを書き込む [C# 関数](functions-dotnet-class-library.md)を示しています。

```csharp
public static class QueueFunctions
{
    [FunctionName("QueueTrigger")]
    public static void QueueTrigger(
        [QueueTrigger("myqueue-items")] string myQueueItem, 
        ILogger log)
    {
        log.LogInformation($"C# function processed: {myQueueItem}");
    }
}
```

# <a name="c-scripttabcsharp-script"></a>[C# スクリプト](#tab/csharp-script)

次の例は、*function.json* ファイルのキュー トリガー バインディングと、バインディングを使用する [C# スクリプト (.csx)](functions-reference-csharp.md) コードを示しています。 この関数は、キュー項目が処理されるたびに `myqueue-items` キューをポーリングし、ログを書き込みます。

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

using Microsoft.Extensions.Logging;
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
    ILogger log)
{
    log.LogInformation($"C# Queue trigger function processed: {myQueueItem.AsString}\n" +
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

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

次の例は、*function.json* ファイルのキュー トリガー バインドと、そのバインドを使用する [JavaScript 関数](functions-reference-node.md)を示しています。 この関数は、キュー項目が処理されるたびに `myqueue-items` キューをポーリングし、ログを書き込みます。

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

> [!NOTE]
> name パラメーターは、キュー項目ペイロードを含む JavaScript コードの `context.bindings.<name>` として反映されます。 このペイロードは、関数に対する 2 番目のパラメーターとしても渡されます。

JavaScript コードを次に示します。

```javascript
module.exports = async function (context, message) {
    context.log('Node.js queue trigger function processed work item', message);
    // OR access using context.bindings.<name>
    // context.log('Node.js queue trigger function processed work item', context.bindings.myQueueItem);
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

# <a name="pythontabpython"></a>[Python](#tab/python)

次の例では、トリガーを使用してキュー メッセージを読み取って関数に渡す方法を示します。

ストレージ キュー トリガーは *function.json* で定義され、そこで *type* は `queueTrigger` に設定されます。

```json
{
  "scriptFile": "__init__.py",
  "bindings": [
    {
      "name": "msg",
      "type": "queueTrigger",
      "direction": "in",
      "queueName": "messages",
      "connection": "AzureStorageQueuesConnectionString"
    }
  ]
}
```

*_\_init_\_.py* のコードによってパラメーターが `func.ServiceBusMessage` として宣言され、関数でキュー メッセージを読み取ることができるようになります。

```python
import logging
import json

import azure.functions as func

def main(msg: func.QueueMessage):
    logging.info('Python queue trigger function processed a queue item.')

    result = json.dumps({
        'id': msg.id,
        'body': msg.get_body().decode('utf-8'),
        'expiration_time': (msg.expiration_time.isoformat()
                            if msg.expiration_time else None),
        'insertion_time': (msg.insertion_time.isoformat()
                           if msg.insertion_time else None),
        'time_next_visible': (msg.time_next_visible.isoformat()
                              if msg.time_next_visible else None),
        'pop_receipt': msg.pop_receipt,
        'dequeue_count': msg.dequeue_count
    })

    logging.info(result)
```

# <a name="javatabjava"></a>[Java](#tab/java)

次の Java の例は、キュー `myqueuename` に格納されるトリガーされたメッセージを記録するストレージ キュー トリガー関数を示しています。

 ```java
 @FunctionName("queueprocessor")
 public void run(
    @QueueTrigger(name = "msg",
                   queueName = "myqueuename",
                   connection = "myconnvarname") String message,
     final ExecutionContext context
 ) {
     context.getLogger().info(message);
 }
 ```

 ---

## <a name="trigger---attributes-and-annotations"></a>トリガー - 属性と注釈

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

[C# クラス ライブラリ](functions-dotnet-class-library.md)では、以下の属性を使用してキュー トリガーを構成します。

* [QueueTriggerAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.Extensions.Storage/Queues/QueueTriggerAttribute.cs)

  次の例のように、属性のコンストラクターは監視するキューの名前を受け取ります。

  ```csharp
  [FunctionName("QueueTrigger")]
  public static void Run(
      [QueueTrigger("myqueue-items")] string myQueueItem, 
      ILogger log)
  {
      ...
  }
  ```

  次の例で示すように、`Connection` プロパティを設定して、使用するストレージ アカウント接続ストリングを含むアプリ設定を指定できます。

  ```csharp
  [FunctionName("QueueTrigger")]
  public static void Run(
      [QueueTrigger("myqueue-items", Connection = "StorageConnectionAppSetting")] string myQueueItem, 
      ILogger log)
  {
      ....
  }
  ```

  完全な例については、「[トリガー - C# の例](#trigger)」を参照してください。

* [StorageAccountAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/StorageAccountAttribute.cs)

  使用するストレージ アカウントを指定する別の方法を提供します。 コンストラクターは、ストレージ接続文字列を含むアプリ設定の名前を受け取ります。 属性は、パラメーター、メソッド、またはクラス レベルで適用できます。 次の例では、クラス レベルとメソッド レベルを示します。

  ```csharp
  [StorageAccount("ClassLevelStorageAppSetting")]
  public static class AzureFunctions
  {
      [FunctionName("QueueTrigger")]
      [StorageAccount("FunctionLevelStorageAppSetting")]
      public static void Run( //...
  {
      ...
  }
  ```

使用するストレージ アカウントは、次の順序で決定されます。

* `QueueTrigger` 属性の `Connection` プロパティ。
* `QueueTrigger` 属性と同じパラメーターに適用された `StorageAccount` 属性。
* 関数に適用される `StorageAccount` 属性。
* クラスに適用される `StorageAccount` 属性。
* "AzureWebJobsStorage" アプリ設定。

# <a name="c-scripttabcsharp-script"></a>[C# スクリプト](#tab/csharp-script)

属性は、C# スクリプトではサポートされていません。

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

属性は、JavaScript ではサポートされていません。

# <a name="pythontabpython"></a>[Python](#tab/python)

属性は、Python ではサポートされていません。

# <a name="javatabjava"></a>[Java](#tab/java)

`QueueTrigger` 注釈を使用すると、関数をトリガーするキューにアクセスできます。 次の例では、`message` パラメーターを使用して、キュー メッセージを関数で使用できるようにします。

```java
package com.function;
import com.microsoft.azure.functions.annotation.*;
import java.util.Queue;
import com.microsoft.azure.functions.*;

public class QueueTriggerDemo {
    @FunctionName("QueueTriggerDemo")
    public void run(
        @QueueTrigger(name = "message", queueName = "messages", connection = "MyStorageConnectionAppSetting") String message,
        final ExecutionContext context
    ) {
        context.getLogger().info("Queue message: " + message);
    }
}
```

| プロパティ    | [説明] |
|-------------|-----------------------------|
|`name`       | 関数シグネチャのパラメーター名を宣言します。 関数がトリガーされると、このパラメーターの値にはキュー メッセージの内容が含められます。 |
|`queueName`  | ストレージ アカウントのキュー名を宣言します。 |
|`connection` | ストレージ アカウントの接続文字列を示します。 |

---

## <a name="trigger---configuration"></a>トリガー - 構成

次の表は、*function.json* ファイルと `QueueTrigger` 属性で設定したバインド構成のプロパティを説明しています。

|function.json のプロパティ | 属性のプロパティ |[説明]|
|---------|---------|----------------------|
|**type** | 該当なし| `queueTrigger` に設定する必要があります。 このプロパティは、Azure Portal でトリガーを作成するときに自動で設定されます。|
|**direction**| 該当なし | *function.json* ファイルの場合のみ。 `in` に設定する必要があります。 このプロパティは、Azure Portal でトリガーを作成するときに自動で設定されます。 |
|**name** | 該当なし |関数コードでキュー項目ペイロードを含む変数の名前。  |
|**queueName** | **QueueName**| ポーリングするキューの名前。 |
|**connection** | **[接続]** |このバインドに使用するストレージ接続文字列を含むアプリ設定の名前です。 アプリ設定の名前が "AzureWebJobs" で始まる場合は、ここで名前の残りの部分のみを指定できます。 たとえば、`connection` を "MyStorage" に設定した場合、Functions ランタイムは "MyStorage" という名前のアプリ設定を探します。 `connection` を空のままにした場合、Functions ランタイムは、アプリ設定内の `AzureWebJobsStorage` という名前の既定のストレージ接続文字列を使用します。|

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="trigger---usage"></a>トリガー - 使用方法

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

`string paramName` のようなメソッド パラメーターを使用してメッセージ データにアクセスします。 次の型のいずれにでもバインドできます。

* オブジェクト - Functions ランタイムは、JSON ペイロードを、コードで定義されている任意のクラスのインスタンスに逆シリアル化します。 
* `string`
* `byte[]`
* [CloudQueueMessage]

`CloudQueueMessage` にバインドしようとしてエラー メッセージが表示された場合は、[適切な Storage SDK バージョン](#azure-storage-sdk-version-in-functions-1x)への参照があることをご確認ください。

# <a name="c-scripttabcsharp-script"></a>[C# スクリプト](#tab/csharp-script)

`string paramName` のようなメソッド パラメーターを使用してメッセージ データにアクセスします。 `paramName` は *function.json* の `name` プロパティで指定された値です。 次の型のいずれにでもバインドできます。

* オブジェクト - Functions ランタイムは、JSON ペイロードを、コードで定義されている任意のクラスのインスタンスに逆シリアル化します。 
* `string`
* `byte[]`
* [CloudQueueMessage]

`CloudQueueMessage` にバインドしようとしてエラー メッセージが表示された場合は、[適切な Storage SDK バージョン](#azure-storage-sdk-version-in-functions-1x)への参照があることをご確認ください。

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

キュー項目ペイロードは、`context.bindings.<NAME>` を介して使用できます。ここで、`<NAME>` は *function.json* で定義されている名前と一致します。 ペイロードが JSON の場合、値はオブジェクトに逆シリアル化されます。

# <a name="pythontabpython"></a>[Python](#tab/python)

[QueueMessage](https://docs.microsoft.com/python/api/azure-functions/azure.functions.queuemessage?view=azure-python) として型指定されたパラメーターを使用して、キュー メッセージにアクセスします。

# <a name="javatabjava"></a>[Java](#tab/java)

[QueueTrigger](https://docs.microsoft.com/java/api/com.microsoft.azure.functions.annotation.queuetrigger?view=azure-java-stable) 注釈を使用すると、関数をトリガーしたキュー メッセージにアクセスできます。

---

## <a name="trigger---message-metadata"></a>トリガー - メッセージのメタデータ

キュー トリガーは、いくつかの[メタデータ プロパティ](./functions-bindings-expressions-patterns.md#trigger-metadata)を提供します。 これらのプロパティは、他のバインドのバインド式の一部として、またはコードのパラメーターとして使用できます。 これらは [CloudQueueMessage](https://docs.microsoft.com/dotnet/api/microsoft.azure.storage.queue.cloudqueuemessage) クラスのプロパティです。

|プロパティ|種類|[説明]|
|--------|----|-----------|
|`QueueTrigger`|`string`|キュー ペイロード (有効な文字列の場合)。 キュー メッセージ ペイロードが文字列の場合、`QueueTrigger` は、*function.json* の `name` プロパティで指定された変数と同じ値になります。|
|`DequeueCount`|`int`|このメッセージがデキューされた回数。|
|`ExpirationTime`|`DateTimeOffset`|メッセージが期限切れになる時刻。|
|`Id`|`string`|キュー メッセージ ID。|
|`InsertionTime`|`DateTimeOffset`|メッセージがキューに追加された時刻。|
|`NextVisibleTime`|`DateTimeOffset`|メッセージが次に表示される時刻。|
|`PopReceipt`|`string`|メッセージのポップ受信。|

## <a name="trigger---poison-messages"></a>トリガー - 有害メッセージ

キュー トリガー関数が失敗すると、Azure Functions は、その関数を特定のキュー メッセージに対して (最初の試行を含め) 最大 5 回再試行します。 試行が 5 回とも失敗した場合、Functions ランタイム は、 *&lt;originalqueuename>-poison* という名前のキューにメッセージを追加します。 メッセージのログを取得するか、手動での対処が必要であるという通知を送信することにより有害キューからのメッセージを処理する関数が記述できます。

有害メッセージを手動で処理するには、キュー メッセージの [dequeueCount](#trigger---message-metadata) を確認します。

## <a name="trigger---polling-algorithm"></a>トリガー - ポーリング アルゴリズム

キュー トリガーは、アイドル状態のキューのポーリングがストレージ トランザクション コストに与える影響を軽減するために、ランダムな指数バックオフ アルゴリズムを実装します。

アルゴリズムでは次のロジックが使用されます。

- メッセージが見つかると、ランタイムは 2 秒間待機してから、別のメッセージを確認します
- メッセージが見つからない場合は、約 4 秒間待機してから再試行します。
- 再試行後もキュー メッセージが取得できなかった場合、待ち時間が最大になるまで再試行が続けられます。既定の最大待ち時間は 1 分間です。
- 最大待ち時間は、[host.json ファイル](functions-host-json.md#queues)内の `maxPollingInterval` プロパティで構成できます。

ローカル開発の場合、最大ポーリング間隔は既定で 2 秒に設定されます。

課金に関しては、ランタイムによるポーリングに費やされた時間は "無料" であり、お使いのアカウントに対してカウントされません。

## <a name="trigger---concurrency"></a>トリガー - コンカレンシー

待機中のキュー メッセージが複数存在する場合、キュー トリガーはメッセージのバッチを取得し、関数インスタンスを同時に呼び出してそれらを処理します。 既定では、このバッチ サイズは 16 です。 処理されている数が 8 まで減少すると、このランタイムは別のバッチを取得し、それらのメッセージの処理を開始します。 そのため、1 つの仮想マシン (VM) 上で 1 関数あたりに処理されている同時実行メッセージの最大数は 24 です。 この制限は、各 VM 上のキューによってトリガーされる各関数に個別に適用されます。 関数アプリが複数の VM にスケールアウトした場合、各 VM はトリガーを待機し、関数を実行しようとします。 たとえば、関数アプリが 3 つの VM にスケールアウトした場合、キューによってトリガーされる 1 つの関数の同時実行インスタンスの既定の最大数は 72 です。

新しいバッチを取得するためのバッチ サイズとしきい値は、[host.json ファイル](functions-host-json.md#queues)で構成できます。 関数アプリ内のキューによってトリガーされる関数の並列実行を最小限に抑えたい場合は、このバッチ サイズを 1 に設定できます。 この設定によってコンカレンシーが解消されるのは、関数アプリが 1 つの仮想マシン (VM) 上で実行される場合に限ります。 

キュー トリガーは、関数がキュー メッセージを複数回処理することを自動的に防止します。関数をべき等として記述する必要はありません。

## <a name="trigger---hostjson-properties"></a>トリガー - host.json のプロパティ

[host.json](functions-host-json.md#queues) ファイルには、キュー トリガーの動作を制御する設定が含まれています。 使用可能な設定の詳細については、「[host.json 設定](#hostjson-settings)」を参照してください。

## <a name="output"></a>Output

Azure Queue Storage の出力バインドを使用して、キューにメッセージを書き込みます。

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

次の例は、受け取った HTTP 要求ごとにキュー メッセージを作成する [C# 関数](functions-dotnet-class-library.md)を示しています。

```csharp
[StorageAccount("MyStorageConnectionAppSetting")]
public static class QueueFunctions
{
    [FunctionName("QueueOutput")]
    [return: Queue("myqueue-items")]
    public static string QueueOutput([HttpTrigger] dynamic input,  ILogger log)
    {
        log.LogInformation($"C# function processed: {input.Text}");
        return input.Text;
    }
}
```

# <a name="c-scripttabcsharp-script"></a>[C# スクリプト](#tab/csharp-script)

次の例は、*function.json* ファイルの HTTP トリガー バインディングと、バインディングを使用する [C# スクリプト (.csx)](functions-reference-csharp.md) コードを示しています。 この関数では、受け取った HTTP 要求ごとに **CustomQueueMessage** ペイロードを使用してキュー項目を作成します。

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
      "name": "$return"
    },
    {
      "type": "queue",
      "direction": "out",
      "name": "$return",
      "queueName": "outqueue",
      "connection": "MyStorageConnectionAppSetting"
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

public static CustomQueueMessage Run(CustomQueueMessage input, ILogger log)
{
    return input;
}
```

一度で複数のメッセージを送信するには、`ICollector` または `IAsyncCollector` パラメーターを使用します。 HTTP 要求データを含む 1 つのメッセージと、ハードコーディングされた値を含む 1 つのメッセージという、複数のメッセージを送信する C# スクリプト コードを次に示します。

```cs
public static void Run(
    CustomQueueMessage input, 
    ICollector<CustomQueueMessage> myQueueItems, 
    ILogger log)
{
    myQueueItems.Add(input);
    myQueueItems.Add(new CustomQueueMessage { PersonName = "You", Title = "None" });
}
```

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

次の例は、*function.json* ファイルの HTTP トリガー バインドと、そのバインドを使用する [JavaScript 関数](functions-reference-node.md)を示しています。 この関数では、受け取った HTTP 要求ごとにキュー項目を作成します。

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
      "name": "$return"
    },
    {
      "type": "queue",
      "direction": "out",
      "name": "$return",
      "queueName": "outqueue",
      "connection": "MyStorageConnectionAppSetting"
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

# <a name="pythontabpython"></a>[Python](#tab/python)

次の例では、ストレージ キューに 1 つの値と複数の値を出力する方法を示します。 *function.json* で必要な構成は、どちらでも同じです。

ストレージ キュー バインディングは *function.json* で定義され、そこで *type* は `queue` に設定されます。

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
      "type": "queue",
      "direction": "out",
      "name": "msg",
      "queueName": "outqueue",
      "connection": "AzureStorageQueuesConnectionString"
    }
  ]
}
```

キューに個々のメッセージを設定するには、 `set` メソッドに 1 つの値を渡します。

```python
import azure.functions as func

def main(req: func.HttpRequest, msg: func.Out[str]) -> func.HttpResponse:

    input_msg = req.params.get('message')

    msg.set(input_msg)

    return 'OK'
```

キュー上で複数のメッセージを作成するには、パラメーターを適切なリスト型として宣言し、値の配列 (リスト型と一致する) を `set` メソッドに渡します。

```python
import azure.functions as func
import typing

def main(req: func.HttpRequest, msg: func.Out[typing.List[str]]) -> func.HttpResponse:

    msg.set(['one', 'two'])

    return 'OK'
```

# <a name="javatabjava"></a>[Java](#tab/java)

 次の例は、HTTP 要求によってトリガーされたときにキュー メッセージを作成する Java 関数を示しています。

```java
@FunctionName("httpToQueue")
@QueueOutput(name = "item", queueName = "myqueue-items", connection = "MyStorageConnectionAppSetting")
 public String pushToQueue(
     @HttpTrigger(name = "request", methods = {HttpMethod.POST}, authLevel = AuthorizationLevel.ANONYMOUS)
     final String message,
     @HttpOutput(name = "response") final OutputBinding<String> result) {
       result.setValue(message + " has been added.");
       return message;
 }
```

[Java 関数ランタイム ライブラリ](/java/api/overview/azure/functions/runtime)で、その値が Queue Storage に書き込まれる関数のパラメーター上で `@QueueOutput` 注釈を使用します。  パラメーターの型は `OutputBinding<T>` にする必要があります。`T` は POJO の Java の任意のネイティブ型です。

---

## <a name="output---attributes-and-annotations"></a>出力 - 属性と注釈

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

[C# クラス ライブラリ](functions-dotnet-class-library.md)では、[QueueAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/QueueAttribute.cs) を使用します。

この属性は、`out` パラメーターまたは関数の戻り値に適用されます。 次の例のように、属性のコンストラクターはキューの名前を受け取ります。

```csharp
[FunctionName("QueueOutput")]
[return: Queue("myqueue-items")]
public static string Run([HttpTrigger] dynamic input,  ILogger log)
{
    ...
}
```

次の例で示すように、`Connection` プロパティを設定して、使用するストレージ アカウントを指定できます。

```csharp
[FunctionName("QueueOutput")]
[return: Queue("myqueue-items", Connection = "StorageConnectionAppSetting")]
public static string Run([HttpTrigger] dynamic input,  ILogger log)
{
    ...
}
```

完全な例については、「[出力 - C# の例](#output)」を参照してください。

`StorageAccount` 属性を使用して、クラス、メソッド、またはパラメーターのレベルでストレージ アカウントを指定できます。 詳細については、「トリガー - 属性」をご覧ください。

# <a name="c-scripttabcsharp-script"></a>[C# スクリプト](#tab/csharp-script)

属性は、C# スクリプトではサポートされていません。

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

属性は、JavaScript ではサポートされていません。

# <a name="pythontabpython"></a>[Python](#tab/python)

属性は、Python ではサポートされていません。

# <a name="javatabjava"></a>[Java](#tab/java)

`QueueOutput` 注釈を使用すると、関数の出力をメッセージに書き込むためのアクセスが許可されます。 次の例は、キュー メッセージを作成する HTTP トリガー関数を示しています。

```java
package com.function;
import java.util.*;
import com.microsoft.azure.functions.annotation.*;
import com.microsoft.azure.functions.*;

public class HttpTriggerQueueOutput {
    @FunctionName("HttpTriggerQueueOutput")
    public HttpResponseMessage run(
            @HttpTrigger(name = "req", methods = {HttpMethod.GET, HttpMethod.POST}, authLevel = AuthorizationLevel.FUNCTION) HttpRequestMessage<Optional<String>> request,
            @QueueOutput(name = "message", queueName = "messages", connection = "MyStorageConnectionAppSetting") OutputBinding<String> message,
            final ExecutionContext context) {

        message.setValue(request.getQueryParameters().get("name"));
        return request.createResponseBuilder(HttpStatus.OK).body("Done").build();
    }
}
```

| プロパティ    | [説明] |
|-------------|-----------------------------|
|`name`       | 関数シグネチャのパラメーター名を宣言します。 関数がトリガーされると、このパラメーターの値にはキュー メッセージの内容が含められます。 |
|`queueName`  | ストレージ アカウントのキュー名を宣言します。 |
|`connection` | ストレージ アカウントの接続文字列を示します。 |

`QueueOutput` 注釈に関連するパラメーターは、[OutputBinding\<T\>](https://github.com/Azure/azure-functions-java-library/blob/master/src/main/java/com/microsoft/azure/functions/OutputBinding.java) インスタンスとして型指定されます。

---

## <a name="output---configuration"></a>出力 - 構成

次の表は、*function.json* ファイルと `Queue` 属性で設定したバインド構成のプロパティを説明しています。

|function.json のプロパティ | 属性のプロパティ |[説明]|
|---------|---------|----------------------|
|**type** | 該当なし | `queue` に設定する必要があります。 このプロパティは、Azure Portal でトリガーを作成するときに自動で設定されます。|
|**direction** | 該当なし | `out` に設定する必要があります。 このプロパティは、Azure Portal でトリガーを作成するときに自動で設定されます。 |
|**name** | 該当なし | 関数コード内のキューを表す変数の名前。 `$return` に設定して、関数の戻り値を参照します。|
|**queueName** |**QueueName** | キューの名前。 |
|**connection** | **[接続]** |このバインドに使用するストレージ接続文字列を含むアプリ設定の名前です。 アプリ設定の名前が "AzureWebJobs" で始まる場合は、ここで名前の残りの部分のみを指定できます。 たとえば、`connection` を "MyStorage" に設定した場合、Functions ランタイムは "MyStorage" という名前のアプリ設定を探します。 `connection` を空のままにした場合、Functions ランタイムは、アプリ設定内の `AzureWebJobsStorage` という名前の既定のストレージ接続文字列を使用します。|

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="output---usage"></a>出力 - 使用方法

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

`out T paramName` などのメソッドのパラメーターを使用して、単一のキュー メッセージを書き込みます。 `out` パラメーターではなくメソッドの戻り値の型を使用することができます。`T` は次に示すいずれかの型の場合があります。

* JSON としてシリアル化可能なオブジェクト
* `string`
* `byte[]`
* [CloudQueueMessage] 

`CloudQueueMessage` にバインドしようとしてエラー メッセージが表示された場合は、[適切な Storage SDK バージョン](#azure-storage-sdk-version-in-functions-1x)への参照があることをご確認ください。

C# と C# スクリプトで複数のキュー メッセージを書き込むには、次のいずれかの型を使用します。 

* `ICollector<T>` または `IAsyncCollector<T>`
* [CloudQueue](/dotnet/api/microsoft.azure.storage.queue.cloudqueue)

# <a name="c-scripttabcsharp-script"></a>[C# スクリプト](#tab/csharp-script)

`out T paramName` などのメソッドのパラメーターを使用して、単一のキュー メッセージを書き込みます。 `paramName` は *function.json* の `name` プロパティで指定された値です。 `out` パラメーターではなくメソッドの戻り値の型を使用することができます。`T` は次に示すいずれかの型の場合があります。

* JSON としてシリアル化可能なオブジェクト
* `string`
* `byte[]`
* [CloudQueueMessage] 

`CloudQueueMessage` にバインドしようとしてエラー メッセージが表示された場合は、[適切な Storage SDK バージョン](#azure-storage-sdk-version-in-functions-1x)への参照があることをご確認ください。

C# と C# スクリプトで複数のキュー メッセージを書き込むには、次のいずれかの型を使用します。 

* `ICollector<T>` または `IAsyncCollector<T>`
* [CloudQueue](/dotnet/api/microsoft.azure.storage.queue.cloudqueue)

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

出力キュー項目は、`context.bindings.<NAME>` を介して使用できます。ここで、`<NAME>` は *function.json* で定義されている名前と一致します。 キュー項目ペイロードには、文字列または JSON のシリアル化可能なオブジェクトを使用できます。

# <a name="pythontabpython"></a>[Python](#tab/python)

関数からイベント ハブ メッセージを出力するには、次の 2 つのオプションがあります。

- **戻り値**:*function.json* 内の `name` プロパティを `$return` に設定します。 この構成では、関数の戻り値は Queue storage メッセージとして永続化されます。

- **命令型**:[Out](https://docs.microsoft.com/python/api/azure-functions/azure.functions.out?view=azure-python) 型として宣言されたパラメーターの [set](https://docs.microsoft.com/python/api/azure-functions/azure.functions.out?view=azure-python#set-val--t-----none) メソッドに値を渡します。 `set` に渡された値は、Queue storage メッセージとして永続化されます。

# <a name="javatabjava"></a>[Java](#tab/java)

[QueueOutput](https://docs.microsoft.com/java/api/com.microsoft.azure.functions.annotation.queueoutput) 注釈を使用して関数からイベント ハブ メッセージを出力するには、次の 2 つのオプションがあります。

- **戻り値**:関数自体に注釈を適用すると、関数の戻り値がイベントハブ メッセージとして永続化されます。

- **命令型**:メッセージ値を明示的に設定するには、[`OutputBinding<T>`](https://docs.microsoft.com/java/api/com.microsoft.azure.functions.OutputBinding) 型の特定のパラメーターに注釈を適用します。 ここで、`T` は POJO または任意のネイティブ Java 型です。 この構成では、`setValue` メソッドに値を渡すと、その値がイベント ハブ メッセージとして保持されます。

---

## <a name="exceptions-and-return-codes"></a>例外とリターン コード

| バインド |  リファレンス |
|---|---|
| キュー | [キュー エラー コード](https://docs.microsoft.com/rest/api/storageservices/queue-service-error-codes) |
| BLOB、テーブル、キュー | [ストレージ エラー コード](https://docs.microsoft.com/rest/api/storageservices/fileservices/common-rest-api-error-codes) |
| BLOB、テーブル、キュー |  [トラブルシューティング](https://docs.microsoft.com/rest/api/storageservices/fileservices/troubleshooting-api-operations) |

<a name="host-json"></a>  

## <a name="hostjson-settings"></a>host.json 設定

このセクションでは、バージョン 2.x 以降でこのバインドに使用可能なグローバル構成設定について説明します。 次の host.json ファイルの例には、このバインドのバージョン 2.x 以降の設定のみが含まれています。 バージョン 2.x 以降でのグローバル構成設定の詳細については、「[Azure Functions の host.json のリファレンス](functions-host-json.md)」を参照してください。

> [!NOTE]
> Functions 1.x の host.json のリファレンスについては、「[host.json reference for Azure Functions 1.x (Azure Functions 1.x の host.json のリファレンス)](functions-host-json-v1.md)」を参照してください。

```json
{
    "version": "2.0",
    "extensions": {
        "queues": {
            "maxPollingInterval": "00:00:02",
            "visibilityTimeout" : "00:00:30",
            "batchSize": 16,
            "maxDequeueCount": 5,
            "newBatchThreshold": 8
        }
    }
}
```

|プロパティ  |Default | [説明] |
|---------|---------|---------|
|maxPollingInterval|00:00:01|キューのポーリングの最大間隔。 最小は 00:00:00.100 (100 ミリ秒) であり、最大 00:01:00 (1 分) まで増分されます。  データ型は 1.x ではミリ秒であり、2.x 以降では TimeSpan です。|
|visibilityTimeout|00:00:00|メッセージの処理が失敗したときの再試行間隔。 |
|batchSize|16|Functions ランタイムが同時に取得して並列で処理するキュー メッセージの数。 処理中のメッセージの数が `newBatchThreshold` まで減少すると、ランタイムは は別のバッチを取得し、そのメッセージの処理を開始します。 そのため、1 つの関数につき同時に処理されるメッセージの最大数は、`batchSize` に `newBatchThreshold` を加えた値です。 この制限は、キューによってトリガーされる各関数に個別に適用されます。 <br><br>1 つのキューで受信した複数のメッセージの並列実行を回避したい場合は、`batchSize` を 1 に設定します。 ただし、この設定では、関数アプリが単一の仮想マシン (VM) で実行されている限り、コンカレンシーは実現しません。 この関数アプリを複数の VM にスケール アウトすると、各 VM では、キューによってトリガーされる関数ごとに 1 つのインスタンスを実行できます。<br><br>最大の `batchSize` は 32 です。 |
|maxDequeueCount|5|有害キューに移動する前に、メッセージの処理を試行する回数。|
|newBatchThreshold|batchSize/2|同時に処理されているメッセージの数がこの数まで減少すると、ランタイムは別のバッチを取得します。|

## <a name="next-steps"></a>次のステップ

* [Azure Functions のトリガーとバインドの詳細情報](functions-triggers-bindings.md)

<!--
> [!div class="nextstepaction"]
> [Go to a quickstart that uses a Queue storage trigger](functions-create-storage-queue-triggered-function.md)
-->

> [!div class="nextstepaction"]
> [Queue Storage 出力バインドを使用するチュートリアルに進む](functions-integrate-storage-queue-output-binding.md)

<!-- LINKS -->

[CloudQueueMessage]: /dotnet/api/microsoft.azure.storage.queue.cloudqueuemessage
