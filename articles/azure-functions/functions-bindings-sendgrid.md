---
title: Azure Functions における SendGrid のバインディング
description: Azure Functions における SendGrid のバインディングのリファレンス
services: functions
documentationcenter: na
author: ggailey777
manager: cfowler
ms.service: functions
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 11/29/2017
ms.author: glenga
ms.openlocfilehash: 2796a7d9fb44a05e1873e2fcbde82c837db202be
ms.sourcegitcommit: 30fd606162804fe8ceaccbca057a6d3f8c4dd56d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/30/2018
ms.locfileid: "39346455"
---
# <a name="azure-functions-sendgrid-bindings"></a>Azure Functions における SendGrid のバインディング

この記事では、Azure Functions で [SendGrid](https://sendgrid.com/docs/User_Guide/index.html) のバインディングを使用して電子メールを送信する方法について説明します。 Azure Functions では、SendGrid 用の出力バインディングがサポートされています。

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

## <a name="packages---functions-1x"></a>パッケージ - Functions 1.x

SendGrid バインディングは [Microsoft.Azure.WebJobs.Extensions.SendGrid](http://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.SendGrid) NuGet パッケージ、バージョン 2.x で提供されます。 パッケージのソース コードは、[azure-webjobs-sdk-extensions](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/v2.x/src/WebJobs.Extensions.SendGrid/) GitHub リポジトリにあります。

[!INCLUDE [functions-package](../../includes/functions-package.md)]

## <a name="packages---functions-2x"></a>パッケージ - Functions 2.x

SendGrid バインディングは [Microsoft.Azure.WebJobs.Extensions.SendGrid](http://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.SendGrid) NuGet パッケージ、バージョン 3.x で提供されます。 パッケージのソース コードは、[azure-webjobs-sdk-extensions](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.SendGrid/) GitHub リポジトリにあります。

[!INCLUDE [functions-package-v2](../../includes/functions-package-v2.md)]

## <a name="example"></a>例

言語固有の例をご覧ください。

* [C#](#c-example)
* [C# スクリプト (.csx)](#c-script-example)
* [JavaScript](#javascript-example)

### <a name="c-example"></a>C# の例

次の例は、Service Bus キュー トリガーと SendGrid 出力バインディングを使用する [C# 関数](functions-dotnet-class-library.md)を示しています。

```cs
[FunctionName("SendEmail")]
public static void Run(
    [ServiceBusTrigger("myqueue", Connection = "ServiceBusConnection")] OutgoingEmail email,
    [SendGrid(ApiKey = "CustomSendGridKeyAppSettingName")] out SendGridMessage message)
{
    message = new SendGridMessage();
    message.AddTo(email.To);
    message.AddContent("text/html", email.Body);
    message.SetFrom(new EmailAddress(email.From));
    message.SetSubject(email.Subject);
}

public class OutgoingEmail
{
    public string To { get; set; }
    public string From { get; set; }
    public string Subject { get; set; }
    public string Body { get; set; }
}
```

"AzureWebJobsSendGridApiKey" という名前のアプリ設定に API キーがある場合は、属性の `ApiKey` プロパティの設定を省略できます。

### <a name="c-script-example"></a>C# スクリプトの例

次の例は、*function.json* ファイルの Service SendGrid 出力バインディングと、そのバインディングを使用する [C# スクリプト関数](functions-reference-csharp.md)を示しています。

*function.json* ファイルのバインディング データを次に示します。

```json 
{
    "bindings": [
        {
            "name": "message",
            "type": "sendGrid",
            "direction": "out",
            "apiKey" : "MySendGridKey",
            "to": "{ToEmail}",
            "from": "{FromEmail}",
            "subject": "SendGrid output bindings"
        }
    ]
}
```

これらのプロパティについては、「[構成](#configuration)」セクションを参照してください。

C# スクリプト コードを次に示します。

```csharp
#r "SendGrid"
using System;
using SendGrid.Helpers.Mail;

public static void Run(TraceWriter log, string input, out Mail message)
{
     message = new Mail
    {        
        Subject = "Azure news"          
    };

    var personalization = new Personalization();
    personalization.AddTo(new Email("recipient@contoso.com"));   

    Content content = new Content
    {
        Type = "text/plain",
        Value = input
    };
    message.AddContent(content);
    message.AddPersonalization(personalization);
}
```

### <a name="javascript-example"></a>JavaScript の例

次の例は、*function.json* ファイルの Service SendGrid 出力バインディングと、そのバインディングを使用する [JavaScript 関数](functions-reference-node.md)を示しています。

*function.json* ファイルのバインディング データを次に示します。

```json 
{
    "bindings": [
        {
            "name": "$return",
            "type": "sendGrid",
            "direction": "out",
            "apiKey" : "MySendGridKey",
            "to": "{ToEmail}",
            "from": "{FromEmail}",
            "subject": "SendGrid output bindings"
        }
    ]
}
```

これらのプロパティについては、「[構成](#configuration)」セクションを参照してください。

JavaScript コードを次に示します。

```javascript
module.exports = function (context, input) {    
    var message = {
         "personalizations": [ { "to": [ { "email": "sample@sample.com" } ] } ],
        from: { email: "sender@contoso.com" },        
        subject: "Azure news",
        content: [{
            type: 'text/plain',
            value: input
        }]
    };

    context.done(null, message);
};
```

## <a name="attributes"></a>属性

[C# クラス ライブラリ](functions-dotnet-class-library.md)では、[SendGrid](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.SendGrid/SendGridAttribute.cs) 属性を使用します。

構成可能な属性プロパティについては、[構成](#configuration)を参照してください。 メソッド シグネチャでの `SendGrid` 属性の例を次に示します。

```csharp
[FunctionName("SendEmail")]
public static void Run(
    [ServiceBusTrigger("myqueue", Connection = "ServiceBusConnection")] OutgoingEmail email,
    [SendGrid(ApiKey = "CustomSendGridKeyAppSettingName")] out SendGridMessage message)
{
    ...
}
```

完全な例については、「[C# の例](#c-example)」を参照してください。

## <a name="configuration"></a>構成

次の表は、*function.json* ファイルと `SendGrid` 属性で設定したバインド構成のプロパティを説明しています。

|function.json のプロパティ | 属性のプロパティ |説明|
|---------|---------|----------------------|
|**type**|| 必須 - `sendGrid` に設定する必要があります。|
|**direction**|| 必須 - `out` に設定する必要があります。|
|**name**|| 必須 - 要求または要求本文の関数コードで使用される変数名。 戻り値が 1 つの場合、この値は ```$return``` です。 |
|**apiKey**|**ApiKey**| API キーを含むアプリ設定の名前。 設定されていない場合、既定のアプリの設定名は"AzureWebJobsSendGridApiKey" です。|
|**to**|**To**| 受信者の電子メール アドレス。 |
|**from**|**From**| 送信者の電子メール アドレス。 |
|**subject**|**[件名]**| 電子メールの件名。 |
|**text**|**テキスト**| 電子メールの本文。 |

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [Azure Functions のトリガーとバインドの詳細情報](functions-triggers-bindings.md)
