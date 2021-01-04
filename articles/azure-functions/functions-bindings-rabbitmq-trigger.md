---
title: Azure Functions の RabbitMQ トリガー
description: RabbitMQ メッセージの作成時に Azure 関数を実行する方法について説明します。
author: cachai2
ms.assetid: ''
ms.topic: reference
ms.date: 12/13/2020
ms.author: cachai
ms.custom: ''
ms.openlocfilehash: e7095c08c385457bddf6d70d345c4f47073b4adb
ms.sourcegitcommit: 2ba6303e1ac24287762caea9cd1603848331dd7a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/15/2020
ms.locfileid: "97505678"
---
# <a name="rabbitmq-trigger-for-azure-functions-overview"></a>Azure Functions の RabbitMQ トリガーの概要

> [!NOTE]
> RabbitMQ のバインドは **Windows Premium** プランでのみ完全にサポートされています。 現在、従量課金と Linux はサポートされていません。

RabbitMQ キューからのメッセージに応答するには、RabbitMQ トリガーを使用します。

セットアップと構成の詳細については、[概要](functions-bindings-rabbitmq.md)に関する記事を参照してください。

## <a name="example"></a>例

# <a name="c"></a>[C#](#tab/csharp)

次の例は、RabbitMQ メッセージを [RabbitMQ イベント](https://www.rabbitmq.com/releases/rabbitmq-dotnet-client/v3.2.2/rabbitmq-dotnet-client-3.2.2-client-htmldoc/html/type-RabbitMQ.Client.Events.BasicDeliverEventArgs.html)として読み取り、ログに記録する [C# 関数](functions-dotnet-class-library.md)を示しています。

```cs
[FunctionName("RabbitMQTriggerCSharp")]
public static void RabbitMQTrigger_BasicDeliverEventArgs(
    [RabbitMQTrigger("queue", ConnectionStringSetting = "rabbitMQConnection")] BasicDeliverEventArgs args,
    ILogger logger
    )
{
    logger.LogInformation($"C# RabbitMQ queue trigger function processed message: {Encoding.UTF8.GetString(args.Body)}");
}
```

次の例は、メッセージを POCO として読み取る方法を示しています。

```cs
public class TestClass
{
    public string x { get; set; }
}

[FunctionName("RabbitMQTriggerCSharp")]
public static void RabbitMQTrigger_BasicDeliverEventArgs(
    [RabbitMQTrigger("queue", ConnectionStringSetting = "rabbitMQConnection")] TestClass pocObj,
    ILogger logger
    )
{
    logger.LogInformation($"C# RabbitMQ queue trigger function processed message: {Encoding.UTF8.GetString(pocObj)}");
}
```

JSON オブジェクトと同様に、メッセージが C# オブジェクトとして適切に書式設定されていない場合、エラーが発生します。 その場合は、変数 pocObj にバインドされ、そこから必要なものに対して使用できます。

# <a name="c-script"></a>[C# スクリプト](#tab/csharp-script)

次の例は、*function.json* ファイルの RabbitMQ トリガー バインドと、そのバインドが使用される [C# スクリプト関数](functions-reference-csharp.md)を示しています。 この関数は、RabbitMQ メッセージを読み取り、ログに記録します。

*function.json* ファイルのバインディング データを次に示します。

```json
{
    "bindings": [
        {
            "name": "myQueueItem",
            "type": "rabbitMQTrigger",
            "direction": "in",
            "queueName": "queue",
            "connectionStringSetting": "rabbitMQConnection"
        }
    ]
}
```

C# スクリプト コードを次に示します。

```csx
using System;

public static void Run(string myQueueItem, ILogger log)
{
    log.LogInformation($"C# Script RabbitMQ trigger function processed: {myQueueItem}");
}
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

次の例は、*function.json* ファイルの RabbitMQ トリガー バインドと、そのバインドが使用される [JavaScript スクリプト関数](functions-reference-node.md)を示しています。 この関数は、RabbitMQ メッセージを読み取り、ログに記録します。

*function.json* ファイルのバインディング データを次に示します。

```json
{
    "bindings": [
        {
            "name": "myQueueItem",
            "type": "rabbitMQTrigger",
            "direction": "in",
            "queueName": "queue",
            "connectionStringSetting": "rabbitMQConnection"
        }
    ]
}
```

JavaScript スクリプト コードを次に示します。

```javascript
module.exports = async function (context, myQueueItem) {
    context.log('JavaScript RabbitMQ trigger function processed work item', myQueueItem);
};
```

# <a name="python"></a>[Python](#tab/python)

次の例では、トリガーを使用して RabbitMQ キュー メッセージを読み取る方法を示します。

RabbitMQ のバインドは *function.json* で定義され、そこで *type* は `RabbitMQTrigger` に設定されます。

```json
{
    "scriptFile": "__init__.py",
    "bindings": [
        {
            "name": "myQueueItem",
            "type": "rabbitMQTrigger",
            "direction": "in",
            "queueName": "",
            "connectionStringSetting": ""
        }
    ]
}
```

*_\_init_\_.py* のコードによってパラメーターが `func.RabbitMQMessage` として宣言され、関数でメッセージを読み取ることができるようになります。

```python
import logging
import azure.functions as func

def main(myQueueItem) -> None:
    logging.info('Python RabbitMQ trigger function processed a queue item: %s', myQueueItem)
```

# <a name="java"></a>[Java](#tab/java)

次の Java 関数は、[Java RabbitMQ type](https://mvnrepository.com/artifact/com.microsoft.azure.functions/azure-functions-java-library-rabbitmq) からの `@RabbitMQTrigger` 注釈を使用して、RabbitMQ キュー トリガーの構成を記述します。 この関数は、キューにあるメッセージを取得し、ログに追加します。

```java
@FunctionName("RabbitMQTriggerExample")
public void run(
    @RabbitMQTrigger(connectionStringSetting = "rabbitMQConnection", queueName = "queue") String input,
    final ExecutionContext context)
{
    context.getLogger().info("Java HTTP trigger processed a request." + input);
}
```

---

## <a name="attributes-and-annotations"></a>属性と注釈

# <a name="c"></a>[C#](#tab/csharp)

[C# クラス ライブラリ](functions-dotnet-class-library.md)では、[RabbitMQTrigger](https://github.com/Azure/azure-functions-rabbitmq-extension/blob/dev/src/Trigger/RabbitMQTriggerAttribute.cs) 属性を使用します。

メソッド シグネチャでの `RabbitMQTrigger` 属性を次に示します。

```csharp
[FunctionName("RabbitMQTest")]
public static void RabbitMQTest([RabbitMQTrigger("queue")] string message, ILogger log)
{
    ...
}
```

完全な例については、「C# の例」を参照してください。

# <a name="c-script"></a>[C# スクリプト](#tab/csharp-script)

属性は、C# スクリプトではサポートされていません。

# <a name="javascript"></a>[JavaScript](#tab/javascript)

属性は、JavaScript ではサポートされていません。

# <a name="python"></a>[Python](#tab/python)

属性は、Python ではサポートされていません。

# <a name="java"></a>[Java](#tab/java)

`RabbitMQTrigger` 注釈を使用すると、RabbitMQ メッセージの作成時に実行される関数を作成できます。 使用可能な構成オプションには、キュー名と接続文字列名があります。 パラメーターの詳細については、[RabbitMQTrigger Java 注釈](https://github.com/Azure/azure-functions-rabbitmq-extension/blob/dev/binding-library/java/src/main/java/com/microsoft/azure/functions/rabbitmq/annotation/RabbitMQTrigger.java)に関するページを参照してください。

詳細については、トリガーの[例](#example)を参照してください。

---

## <a name="configuration"></a>構成

次の表は、*function.json* ファイルと `RabbitMQTrigger` 属性で設定したバインド構成のプロパティを説明しています。

|function.json のプロパティ | 属性のプロパティ |説明|
|---------|---------|----------------------|
|**type** | 該当なし | "RabbitMQTrigger" に設定する必要があります。|
|**direction** | 該当なし | "in" に設定する必要があります。|
|**name** | 該当なし | 関数コード内のキューを表す変数の名前。 |
|**queueName**|**QueueName**| メッセージを受信するキューの名前です。 |
|**hostName**|**HostName**|(ConnectStringSetting を使用する場合は省略可能) <br>キューのホスト名 (例:10.26.45.210)|
|**userNameSetting**|**UserNameSetting**|(ConnectionStringSetting を使用している場合は省略可能) <br>キューにアクセスするための名前 |
|**passwordSetting**|**PasswordSetting**|(ConnectionStringSetting を使用している場合は省略可能) <br>キューにアクセスするためのパスワード|
|**connectionStringSetting**|**ConnectionStringSetting**|RabbitMQ メッセージ キュー接続文字列を含むアプリ設定の名前。 接続文字列は、local.settings.json のアプリ設定ではなく、直接指定した場合、トリガーは機能しないことに注意してください。 (例:*function.json* の場合: connectionStringSetting: "rabbitMQConnection" <br> *local.settings.json* の場合: "rabbitMQConnection" : "< ActualConnectionstring >")|
|**port**|**[ポート]**|使用されているポートを取得または設定します。 既定値は 0 です。|

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="usage"></a>使用法

# <a name="c"></a>[C#](#tab/csharp)

メッセージには次のパラメーター型を使用できます。

* [RabbitMQ イベント](https://www.rabbitmq.com/releases/rabbitmq-dotnet-client/v3.2.2/rabbitmq-dotnet-client-3.2.2-client-htmldoc/html/type-RabbitMQ.Client.Events.BasicDeliverEventArgs.html) - RabbitMQ メッセージの既定の形式。
  * `byte[]`- RabbitMQ イベントの 'Body' プロパティ経由の場合。
* `string` - メッセージがテキストである場合。
* `An object serializable as JSON` - メッセージが有効な JSON 文字列として配信されている場合。
* `POCO` - メッセージが C# オブジェクトとして書式設定されている場合。 完全な例については、C# の[例](#example)を参照してください。

# <a name="c-script"></a>[C# スクリプト](#tab/csharp-script)

メッセージには次のパラメーター型を使用できます。

* [RabbitMQ イベント](https://www.rabbitmq.com/releases/rabbitmq-dotnet-client/v3.2.2/rabbitmq-dotnet-client-3.2.2-client-htmldoc/html/type-RabbitMQ.Client.Events.BasicDeliverEventArgs.html) - RabbitMQ メッセージの既定の形式。
  * `byte[]`- RabbitMQ イベントの 'Body' プロパティ経由の場合。
* `string` - メッセージがテキストである場合。
* `An object serializable as JSON` - メッセージが有効な JSON 文字列として配信されている場合。
* `POCO` - メッセージが C# オブジェクトとして書式設定されている場合。

# <a name="javascript"></a>[JavaScript](#tab/javascript)

RabbitMQ メッセージが文字列または JSON オブジェクトとして関数に渡されます。

# <a name="python"></a>[Python](#tab/python)

RabbitMQ メッセージが文字列または JSON オブジェクトとして関数に渡されます。

# <a name="java"></a>[Java](#tab/java)

Java の[属性と注釈](#attributes-and-annotations)に関するページを参照してください。

---

## <a name="dead-letter-queues"></a>配信不能キュー
配信不能キューと交換は、RabbitMQ トリガーから制御または構成することはできません。  配信不能キューを使用するには、RabbitMQ のトリガーによって使用されるキューを事前に構成します。 [RabbitMQ のドキュメント](https://www.rabbitmq.com/dlx.html)を参照してください。

## <a name="hostjson-settings"></a>host.json 設定

このセクションでは、バージョン 2.x 以降でこのバインドに使用可能なグローバル構成設定について説明します。 次の *host.json* ファイルの例には、このバインドの設定のみが含まれています。 グローバル構成設定の詳細については、[Azure Functions の host.json のリファレンス](functions-host-json.md)を参照してください。

```json
{
    "version": "2.0",
    "extensions": {
        "rabbitMQ": {
            "prefetchCount": 100,
            "queueName": "queue",
            "connectionString": "amqp://user:password@url:port",
            "port": 10
        }
    }
}
```

|プロパティ  |Default | 説明 |
|---------|---------|---------|
|prefetchCount|30|メッセージの受信者が同時に要求およびキャッシュできるメッセージ数を取得または設定します。|
|queueName|該当なし| メッセージを受信するキューの名前です。 |
|connectionString|該当なし|RabbitMQ メッセージ キュー接続文字列を含むアプリ設定の名前。 接続文字列は、local.settings.json のアプリ設定ではなく、直接指定した場合、トリガーは機能しないことに注意してください。|
|port|0|スケーリングされたインスタンスごとに同時に処理できるセッションの最大数。|

## <a name="local-testing"></a>ローカル テスト

> [!NOTE]
> connectionString は、"hostName"、"userName"、および "password" よりも優先されます。 これらがすべて設定されている場合、connectionString によって他の 2 つがオーバーライドされます。

接続文字列を使用せずにローカルでテストする場合は、*host.json* の "rabbitMQ" セクションで、"hostName" 設定と "username" および "password" を設定する必要があります。

```json
{
    "version": "2.0",
    "extensions": {
        "rabbitMQ": {
            ...
            "hostName": "localhost",
            "username": "<your username>",
            "password": "<your password>"
        }
    }
}
```

|プロパティ  |Default | 説明 |
|---------|---------|---------|
|hostName|該当なし|(ConnectStringSetting を使用する場合は省略可能) <br>キューのホスト名 (例:10.26.45.210)|
|userName|該当なし|(ConnectionStringSetting を使用している場合は省略可能) <br>キューにアクセスするための名前 |
|password|該当なし|(ConnectionStringSetting を使用している場合は省略可能) <br>キューにアクセスするためのパスワード|

## <a name="monitoring-rabbitmq-endpoint"></a>RabbitMQ エンドポイントの監視
特定の RabbitMQ エンドポイントのキューと交換を監視するには、次のようにします。

* [RabbitMQ 管理プラグイン](https://www.rabbitmq.com/management.html)を有効にする
* http://{node-hostname}:15672 にアクセスし、ユーザー名とパスワードを使用してログインします。

## <a name="next-steps"></a>次のステップ

- [Azure Functions から RabbitMQ メッセージを送信する (出力バインド)](./functions-bindings-rabbitmq-output.md)
