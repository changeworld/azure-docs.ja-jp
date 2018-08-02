---
title: Azure Functions の Twilio バインド
description: Azure Functions で Twilio バインドを使用する方法を説明します。
services: functions
documentationcenter: na
author: ggailey777
manager: cfowler
editor: ''
tags: ''
keywords: Azure Functions, 関数, イベント処理, 動的コンピューティング, サーバーなしのアーキテクチャ
ms.service: functions
ms.devlang: multiple
ms.topic: reference
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 07/09/2018
ms.author: glenga
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 580dd0409c2210de786723736128d489e5a93aa9
ms.sourcegitcommit: 30fd606162804fe8ceaccbca057a6d3f8c4dd56d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/30/2018
ms.locfileid: "39345832"
---
# <a name="twilio-binding-for-azure-functions"></a>Azure Functions の Twilio バインド

この記事では、Azure Functions の [Twilio](https://www.twilio.com/) バインドを使用してテキスト メッセージを送信する方法について説明します。 Azure Functions は、Twilio の出力バインドをサポートします。

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

## <a name="packages---functions-1x"></a>パッケージ - Functions 1.x

Twilio バインディングは [Microsoft.Azure.WebJobs.Extensions.Twilio](http://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.Twilio) NuGet パッケージ、バージョン 1.x で提供されます。 パッケージのソース コードは、[azure-webjobs-sdk](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/v2.x/src/WebJobs.Extensions.Twilio/) GitHub リポジトリにあります。

[!INCLUDE [functions-package](../../includes/functions-package.md)]

## <a name="packages---functions-2x"></a>パッケージ - Functions 2.x

Twilio バインディングは [Microsoft.Azure.WebJobs.Extensions.Twilio](http://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.Twilio) NuGet パッケージ、バージョン 3.x で提供されます。 パッケージのソース コードは、[azure-webjobs-sdk](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.Twilio/) GitHub リポジトリにあります。

[!INCLUDE [functions-package-v2](../../includes/functions-package-v2.md)]

## <a name="example---functions-1x"></a>例 - Functions 1.x

言語固有の例をご覧ください。

* [C#](#c-example)
* [C# スクリプト (.csx)](#c-script-example)
* [JavaScript](#javascript-example)

### <a name="c-example"></a>C# の例

次の例は、キュー メッセージによってトリガーされたときにテキスト メッセージを送信する [C# 関数](functions-dotnet-class-library.md)を示しています。

```cs
[FunctionName("QueueTwilio")]
[return: TwilioSms(AccountSidSetting = "TwilioAccountSid", AuthTokenSetting = "TwilioAuthToken", From = "+1425XXXXXXX" )]
public static SMSMessage Run(
    [QueueTrigger("myqueue-items", Connection = "AzureWebJobsStorage")] JObject order,
    TraceWriter log)
{
    log.Info($"C# Queue trigger function processed: {order}");

    var message = new SMSMessage()
    {
        Body = $"Hello {order["name"]}, thanks for your order!",
        To = order["mobileNumber"].ToString()
    };

    return message;
}
```

この例では、メソッドの戻り値で `TwilioSms` 属性を使用します。 代わりに、`out SMSMessage` パラメーターまたは `ICollector<SMSMessage>` または `IAsyncCollector<SMSMessage>` パラメーターを持つ属性を使用することができます。

### <a name="c-script-example"></a>C# スクリプトの例

次の例は、*function.json* ファイルの Twilio 出力バインドと、そのバインドを使用する [C# スクリプト関数](functions-reference-csharp.md)を示しています。 関数は、`out`パラメーターを使用してテキスト メッセージを送信します。

*function.json* ファイルのバインド データを次に示します。

function.json の例:

```json
{
  "type": "twilioSms",
  "name": "message",
  "accountSid": "TwilioAccountSid",
  "authToken": "TwilioAuthToken",
  "to": "+1704XXXXXXX",
  "from": "+1425XXXXXXX",
  "direction": "out",
  "body": "Azure Functions Testing"
}
```

C# スクリプト コードを次に示します。

```cs
#r "Newtonsoft.Json"
#r "Twilio.Api"

using System;
using Newtonsoft.Json;
using Twilio;

public static void Run(string myQueueItem, out SMSMessage message,  TraceWriter log)
{
    log.Info($"C# Queue trigger function processed: {myQueueItem}");

    // In this example the queue item is a JSON string representing an order that contains the name of a 
    // customer and a mobile number to send text updates to.
    dynamic order = JsonConvert.DeserializeObject(myQueueItem);
    string msg = "Hello " + order.name + ", thank you for your order.";

    // Even if you want to use a hard coded message and number in the binding, you must at least 
    // initialize the SMSMessage variable.
    message = new SMSMessage();

    // A dynamic message can be set instead of the body in the output binding. In this example, we use 
    // the order information to personalize a text message to the mobile number provided for
    // order status updates.
    message.Body = msg;
    message.To = order.mobileNumber;
}
```

非同期コードで out パラメーターを使用することはできません。 非同期 C# スクリプトのコード例を次に示します。

```cs
#r "Newtonsoft.Json"
#r "Twilio.Api"

using System;
using Newtonsoft.Json;
using Twilio;

public static async Task Run(string myQueueItem, IAsyncCollector<SMSMessage> message,  TraceWriter log)
{
    log.Info($"C# Queue trigger function processed: {myQueueItem}");

    // In this example the queue item is a JSON string representing an order that contains the name of a 
    // customer and a mobile number to send text updates to.
    dynamic order = JsonConvert.DeserializeObject(myQueueItem);
    string msg = "Hello " + order.name + ", thank you for your order.";

    // Even if you want to use a hard coded message and number in the binding, you must at least 
    // initialize the SMSMessage variable.
    SMSMessage smsText = new SMSMessage();

    // A dynamic message can be set instead of the body in the output binding. In this example, we use 
    // the order information to personalize a text message to the mobile number provided for
    // order status updates.
    smsText.Body = msg;
    smsText.To = order.mobileNumber;

    await message.AddAsync(smsText);
}
```

### <a name="javascript-example"></a>JavaScript の例

次の例は、*function.json* ファイルの Twilio 出力バインドと、バインドを使用する [JavaScript 関数](functions-reference-node.md)を示しています。

*function.json* ファイルのバインド データを次に示します。

function.json の例:

```json
{
  "type": "twilioSms",
  "name": "message",
  "accountSid": "TwilioAccountSid",
  "authToken": "TwilioAuthToken",
  "to": "+1704XXXXXXX",
  "from": "+1425XXXXXXX",
  "direction": "out",
  "body": "Azure Functions Testing"
}
```

JavaScript コードを次に示します。

```javascript
module.exports = function (context, myQueueItem) {
    context.log('Node.js queue trigger function processed work item', myQueueItem);

    // In this example the queue item is a JSON string representing an order that contains the name of a 
    // customer and a mobile number to send text updates to.
    var msg = "Hello " + myQueueItem.name + ", thank you for your order.";

    // Even if you want to use a hard coded message and number in the binding, you must at least 
    // initialize the message binding.
    context.bindings.message = {};

    // A dynamic message can be set instead of the body in the output binding. In this example, we use 
    // the order information to personalize a text message to the mobile number provided for
    // order status updates.
    context.bindings.message = {
        body : msg,
        to : myQueueItem.mobileNumber
    };

    context.done();
};
```

## <a name="example---functions-2x"></a>例 - Functions 2.x

言語固有の例をご覧ください。

* [2.x C#](#2x-c-example)
* [2.x C# スクリプト (.csx)](#2x-c-script-example)
* [2.x JavaScript](#2x-javascript-example)

### <a name="2x-c-example"></a>2.x C# の例

次の例は、キュー メッセージによってトリガーされたときにテキスト メッセージを送信する [C# 関数](functions-dotnet-class-library.md)を示しています。

```cs
[FunctionName("QueueTwilio")]
[return: TwilioSms(AccountSidSetting = "TwilioAccountSid", AuthTokenSetting = "TwilioAuthToken", From = "+1425XXXXXXX" )]
public static CreateMessageOptions Run(
    [QueueTrigger("myqueue-items", Connection = "AzureWebJobsStorage")] JObject order,
    TraceWriter log)
{
    log.Info($"C# Queue trigger function processed: {order}");

    var message = new CreateMessageOptions(new PhoneNumber("+1704XXXXXXX"))
    {
        Body = $"Hello {order["name"]}, thanks for your order!",
        To = order["mobileNumber"].ToString()
    };

    return message;
}
```

この例では、メソッドの戻り値で `TwilioSms` 属性を使用します。 代わりに、`out CreateMessageOptions` パラメーターまたは `ICollector<CreateMessageOptions>` または `IAsyncCollector<CreateMessageOptions>` パラメーターを持つ属性を使用することができます。

### <a name="2x-c-script-example"></a>2.x C# スクリプトの例

次の例は、*function.json* ファイルの Twilio 出力バインドと、そのバインドを使用する [C# スクリプト関数](functions-reference-csharp.md)を示しています。 関数は、`out`パラメーターを使用してテキスト メッセージを送信します。

*function.json* ファイルのバインド データを次に示します。

function.json の例:

```json
{
  "type": "twilioSms",
  "name": "message",
  "accountSid": "TwilioAccountSid",
  "authToken": "TwilioAuthToken",
  "to": "+1704XXXXXXX",
  "from": "+1425XXXXXXX",
  "direction": "out",
  "body": "Azure Functions Testing"
}
```

C# スクリプト コードを次に示します。

```cs
#r "Newtonsoft.Json"
#r "Twilio.Api"

using System;
using Newtonsoft.Json;
using Twilio.Rest.Api.V2010.Account;
using Twilio.Types;

public static void Run(string myQueueItem, out CreateMessageOptions message,  TraceWriter log)
{
    log.Info($"C# Queue trigger function processed: {myQueueItem}");

    // In this example the queue item is a JSON string representing an order that contains the name of a
    // customer and a mobile number to send text updates to.
    dynamic order = JsonConvert.DeserializeObject(myQueueItem);
    string msg = "Hello " + order.name + ", thank you for your order.";

    // Even if you want to use a hard coded message and number in the binding, you must at least
    // initialize the CreateMessageOptions variable.
    message = new CreateMessageOptions(new PhoneNumber("+1704XXXXXXX"));

    // A dynamic message can be set instead of the body in the output binding. In this example, we use
    // the order information to personalize a text message to the mobile number provided for
    // order status updates.
    message.Body = msg;
    message.To = order.mobileNumber;
}
```

非同期コードで out パラメーターを使用することはできません。 非同期 C# スクリプトのコード例を次に示します。

```cs
#r "Newtonsoft.Json"
#r "Twilio.Api"

using System;
using Newtonsoft.Json;
using Twilio.Rest.Api.V2010.Account;
using Twilio.Types;

public static async Task Run(string myQueueItem, IAsyncCollector<CreateMessageOptions> message,  TraceWriter log)
{
    log.Info($"C# Queue trigger function processed: {myQueueItem}");

    // In this example the queue item is a JSON string representing an order that contains the name of a
    // customer and a mobile number to send text updates to.
    dynamic order = JsonConvert.DeserializeObject(myQueueItem);
    string msg = "Hello " + order.name + ", thank you for your order.";

    // Even if you want to use a hard coded message and number in the binding, you must at least
    // initialize the CreateMessageOptions variable.
    CreateMessageOptions smsText = new CreateMessageOptions(new PhoneNumber("+1704XXXXXXX"));

    // A dynamic message can be set instead of the body in the output binding. In this example, we use
    // the order information to personalize a text message to the mobile number provided for
    // order status updates.
    smsText.Body = msg;
    smsText.To = order.mobileNumber;

    await message.AddAsync(smsText);
}
```

### <a name="2x-javascript-example"></a>2.x JavaScript の例

次の例は、*function.json* ファイルの Twilio 出力バインドと、バインドを使用する [JavaScript 関数](functions-reference-node.md)を示しています。

*function.json* ファイルのバインド データを次に示します。

function.json の例:

```json
{
  "type": "twilioSms",
  "name": "message",
  "accountSid": "TwilioAccountSid",
  "authToken": "TwilioAuthToken",
  "to": "+1704XXXXXXX",
  "from": "+1425XXXXXXX",
  "direction": "out",
  "body": "Azure Functions Testing"
}
```

JavaScript コードを次に示します。

```javascript
module.exports = function (context, myQueueItem) {
    context.log('Node.js queue trigger function processed work item', myQueueItem);

    // In this example the queue item is a JSON string representing an order that contains the name of a
    // customer and a mobile number to send text updates to.
    var msg = "Hello " + myQueueItem.name + ", thank you for your order.";

    // Even if you want to use a hard coded message and number in the binding, you must at least
    // initialize the message binding.
    context.bindings.message = {};

    // A dynamic message can be set instead of the body in the output binding. In this example, we use
    // the order information to personalize a text message to the mobile number provided for
    // order status updates.
    context.bindings.message = {
        body : msg,
        to : myQueueItem.mobileNumber
    };

    context.done();
};
```

## <a name="attributes"></a>属性

[C# クラス ライブラリ](functions-dotnet-class-library.md)では、[TwilioSms](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.Twilio/TwilioSMSAttribute.cs) 属性を使用します。

構成可能な属性プロパティについては、[構成](#configuration)を参照してください。 メソッド シグネチャでの `TwilioSms` 属性の例を次に示します。

```csharp
[FunctionName("QueueTwilio")]
[return: TwilioSms(
    AccountSidSetting = "TwilioAccountSid", 
    AuthTokenSetting = "TwilioAuthToken", 
    From = "+1425XXXXXXX" )]
public static SMSMessage Run(
    [QueueTrigger("myqueue-items", Connection = "AzureWebJobsStorage")] JObject order,
    TraceWriter log)
{
    ...
}
 ```

完全な例については、「[C# の例](#c-example)」を参照してください。

## <a name="configuration"></a>構成

次の表は、*function.json* ファイルと `TwilioSms` 属性で設定したバインド構成のプロパティを説明しています。

|function.json のプロパティ | 属性のプロパティ |説明|
|---------|---------|----------------------|
|**type**|| `twilioSms` に設定する必要があります。|
|**direction**|| `out` に設定する必要があります。|
|**name**|| Twilio SMS テキスト メッセージの関数コードで使用される変数名です。 |
|**accountSid**|**AccountSid**| この値には、Twilio Account Sid を保持するアプリ設定の名前を指定する必要があります。|
|**authToken**|**AuthToken**| この値には、Twilio 認証トークンを保持するアプリ設定の名前を指定する必要があります。|
|**to**|**To**| この値は、SMS テキストの送信先の電話番号に設定されます。|
|**from**|**From**| この値は、SMS テキストの送信元の電話番号に設定されます。|
|**body**|**本文**| この値は、SMS テキスト メッセージを関数のコードで動的に設定する必要がない場合に、メッセージをハード コーディングするために使用できます。 |

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [Azure Functions のトリガーとバインドの詳細情報](functions-triggers-bindings.md)