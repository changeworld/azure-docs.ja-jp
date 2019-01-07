---
title: Azure Functions における SignalR Service のバインド
description: Azure Functions で SignalR Service のバインドを使用する方法を説明します。
services: functions
documentationcenter: na
author: craigshoemaker
manager: jeconnoc
editor: ''
tags: ''
keywords: Azure Functions, 関数, イベント処理, 動的コンピューティング, サーバーなしのアーキテクチャ
ms.service: functions
ms.devlang: multiple
ms.topic: reference
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 09/23/2018
ms.author: cshoe
ms.openlocfilehash: 74092f57b3531a037aee71d433c33dddf8c2b694
ms.sourcegitcommit: 2469b30e00cbb25efd98e696b7dbf51253767a05
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/06/2018
ms.locfileid: "53001796"
---
# <a name="signalr-service-bindings-for-azure-functions"></a>Azure Functions における SignalR サービスのバインド

この記事では、Azure Functions で SignalR Service のバインドを使用して、[Azure SignalR Service](https://azure.microsoft.com/services/signalr-service/) に接続されたクライアントに対して認証を行い、リアルタイム メッセージを送信する方法を説明します。 Azure Functions は、SignalR Service の入力および出力バインドをサポートしています。

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

## <a name="packages---functions-2x"></a>パッケージ - Functions 2.x

SignalR Service のバインドは [Microsoft.Azure.WebJobs.Extensions.SignalRService](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.SignalRService) NuGet パッケージ、バージョン 1.0.0-preview1-* で提供されます。 パッケージのソース コードは、[azure-functions-signalrservice-extension](https://github.com/Azure/azure-functions-signalrservice-extension) GitHub リポジトリにあります。

> [!NOTE]
> Azure SignalR Service は一般公開されています。 ただし、Azure Functions における SignalR Service のバインドは現在プレビューの段階です。

[!INCLUDE [functions-package-v2](../../includes/functions-package-v2-manual-portal.md)]

## <a name="signalr-connection-info-input-binding"></a>SignalR 接続情報入力バインド

クライアントが Azure SignalR Service に接続するには、そのクライアントに、サービス エンドポイント URL と有効なアクセス トークンが必要です。 サービスへの接続に使用される SignalR Service エンドポイント URL と有効なトークンは、*SignalRConnectionInfo* 入力バインドによって生成されます。 トークンを使用できる期間は限られています。また、トークンを使うと、特定のユーザーを接続に対して認証できます。このため、トークンをキャッシュしたりクライアント間で共有したりすることはお勧めしません。 このバインドを使用した HTTP トリガーは、クライアントが接続情報を取得するときに使用できます。

言語固有の例をご覧ください。

* [2.x C#](#2x-c-input-example)
* [2.x JavaScript](#2x-javascript-input-example)

### <a name="2x-c-input-example"></a>2.x C# 入力の例

次の例は、入力バインドを使用して SignalR 接続情報を取得し、HTTP 経由でそれを返す [C# 関数](functions-dotnet-class-library.md)を示しています。

```cs
[FunctionName("GetSignalRInfo")]
public static SignalRConnectionInfo GetSignalRInfo(
    [HttpTrigger(AuthorizationLevel.Anonymous)]HttpRequest req, 
    [SignalRConnectionInfo(HubName = "chat")]SignalRConnectionInfo connectionInfo)
{
    return connectionInfo;
}
```

#### <a name="authenticated-tokens"></a>認証済みトークン

認証済みクライアントによって関数がトリガーされている場合は、ユーザー ID 要求を生成済みトークンに追加できます。 [App Service 認証] (../app-service/app-service-authentication-overview.md) を使用すると、認証を関数アプリに簡単に追加することができます。

App Service 認証によって、`x-ms-client-principal-id` および `x-ms-client-principal-name` という名前の HTTP ヘッダーが設定されます。この 2 つの HTTP ヘッダーには、認証済みユーザーのクライアント プリンシパルの ID と名前がそれぞれ含まれています。 バインドの `UserId` プロパティをいずれかのヘッダーの値に設定するには、[バインド式](functions-triggers-bindings.md#binding-expressions-and-patterns)として `{headers.x-ms-client-principal-id}` または `{headers.x-ms-client-principal-name}` を使用します。 

```cs
[FunctionName("GetSignalRInfo")]
public static SignalRConnectionInfo GetSignalRInfo(
    [HttpTrigger(AuthorizationLevel.Anonymous)]HttpRequest req, 
    [SignalRConnectionInfo
        (HubName = "chat", UserId = "{headers.x-ms-client-principal-id}")]
        SignalRConnectionInfo connectionInfo)
{
    // connectionInfo contains an access key token with a name identifier claim set to the authenticated user
    return connectionInfo;
}
```

### <a name="2x-javascript-input-example"></a>2.x JavaScript 入力の例

次の例は、*function.json* ファイルの SignalR 接続情報入力バインドと、そのバインドを使用して接続情報を返す [JavaScript 関数](functions-reference-node.md)を示しています。

*function.json* ファイルのバインド データを次に示します。

function.json の例:

```json
{
    "type": "signalRConnectionInfo",
    "name": "connectionInfo",
    "hubName": "chat",
    "connectionStringSetting": "<name of setting containing SignalR Service connection string>",
    "direction": "in"
}
```

JavaScript コードを次に示します。

```javascript
module.exports = function (context, req, connectionInfo) {
    context.res = { body: connectionInfo };
    context.done();
};
```

#### <a name="authenticated-tokens"></a>認証済みトークン

認証済みクライアントによって関数がトリガーされている場合は、ユーザー ID 要求を生成済みトークンに追加できます。 [App Service 認証] (../app-service/app-service-authentication-overview.md) を使用すると、認証を関数アプリに簡単に追加することができます。

App Service 認証によって、`x-ms-client-principal-id` および `x-ms-client-principal-name` という名前の HTTP ヘッダーが設定されます。この 2 つの HTTP ヘッダーには、認証済みユーザーのクライアント プリンシパルの ID と名前がそれぞれ含まれています。 バインドの `userId` プロパティをいずれかのヘッダーの値に設定するには、[バインド式](functions-triggers-bindings.md#binding-expressions-and-patterns)として `{headers.x-ms-client-principal-id}` または `{headers.x-ms-client-principal-name}` を使用します。 

function.json の例:

```json
{
    "type": "signalRConnectionInfo",
    "name": "connectionInfo",
    "hubName": "chat",
    "userId": "{headers.x-ms-client-principal-id}",
    "connectionStringSetting": "<name of setting containing SignalR Service connection string>",
    "direction": "in"
}
```

JavaScript コードを次に示します。

```javascript
module.exports = function (context, req, connectionInfo) {
    // connectionInfo contains an access key token with a name identifier 
    // claim set to the authenticated user
    context.res = { body: connectionInfo };
    context.done();
};
```

## <a name="signalr-output-binding"></a>SignalR 出力バインド

*SignalR* 出力バインドを使用して、Azure SignalR Service で 1 つ以上のメッセージを送信します。 すべての接続済みクライアントにメッセージをブロードキャストすることも、特定のユーザーに対して認証されている接続済みクライアントだけにブロードキャストすることも可能です。

言語固有の例をご覧ください。

* [2.x C#](#2x-c-output-example)
* [2.x JavaScript](#2x-javascript-output-example)

### <a name="2x-c-output-example"></a>2.x C# 出力の例

#### <a name="broadcast-to-all-clients"></a>すべてのクライアントにブロードキャストする

次の例は、出力バインドを使用してすべての接続済みクライアントにメッセージを送信する [C# 関数](functions-dotnet-class-library.md)を示しています。 `Target` は、各クライアントで呼び出されるメソッドの名前です。 `Arguments` プロパティは、クライアント メソッドに渡される 0 個以上のオブジェクトの配列です。

```cs
[FunctionName("SendMessage")]
public static Task SendMessage(
    [HttpTrigger(AuthorizationLevel.Anonymous, "post")]object message, 
    [SignalR(HubName = "chat")]IAsyncCollector<SignalRMessage> signalRMessages)
{
    return signalRMessages.AddAsync(
        new SignalRMessage 
        {
            Target = "newMessage", 
            Arguments = new [] { message } 
        });
}
```

#### <a name="send-to-a-user"></a>ユーザーに送信する

ユーザーに対して認証された接続だけにメッセージを送信するには、SignalR メッセージの `UserId` プロパティを設定します。

```cs
[FunctionName("SendMessage")]
public static Task SendMessage(
    [HttpTrigger(AuthorizationLevel.Anonymous, "post")]object message, 
    [SignalR(HubName = "chat")]IAsyncCollector<SignalRMessage> signalRMessages)
{
    return signalRMessages.AddAsync(
        new SignalRMessage 
        {
            // the message will only be sent to these user IDs
            UserId = "userId1",
            Target = "newMessage", 
            Arguments = new [] { message } 
        });
}
```

### <a name="2x-javascript-output-example"></a>2.x JavaScript 出力の例

#### <a name="broadcast-to-all-clients"></a>すべてのクライアントにブロードキャストする

次の例は、*function.json* ファイルの SignalR 出力バインドと、そのバインドを使用して Azure SignalR Service でメッセージを送信する [JavaScript 関数](functions-reference-node.md)を示しています。 出力バインドを、1 つ以上の SignalR メッセージの配列に設定します。 SignalR メッセージは、クライアントごとに呼び出すメソッドの名前を指定する `target` プロパティと、クライアント メソッドに引数として渡されるオブジェクトの配列である `arguments` プロパティで構成されます。

*function.json* ファイルのバインド データを次に示します。

function.json の例:

```json
{
  "type": "signalR",
  "name": "signalRMessages",
  "hubName": "<hub_name>",
  "connectionStringSetting": "<name of setting containing SignalR Service connection string>",
  "direction": "out"
}
```

JavaScript コードを次に示します。

```javascript
module.exports = function (context, req) {
    context.bindings.signalRMessages = [{
        "target": "newMessage",
        "arguments": [ req.body ]
    }];
    context.done();
};
```

#### <a name="send-to-a-user"></a>ユーザーに送信する

ユーザーに対して認証された接続だけにメッセージを送信するには、SignalR メッセージの `userId` プロパティを設定します。

*function.json* は同じままです。 JavaScript コードを次に示します。

```javascript
module.exports = function (context, req) {
    context.bindings.signalRMessages = [{
        // message will only be sent to these user IDs
        "userId": "userId1",
        "target": "newMessage",
        "arguments": [ req.body ]
    }];
    context.done();
};
```

## <a name="configuration"></a>構成

### <a name="signalrconnectioninfo"></a>SignalRConnectionInfo

次の表は、*function.json* ファイルと `SignalRConnectionInfo` 属性で設定したバインド構成のプロパティを説明しています。

|function.json のプロパティ | 属性のプロパティ |説明|
|---------|---------|----------------------|
|**type**|| `signalRConnectionInfo` に設定する必要があります。|
|**direction**|| `in` に設定する必要があります。|
|**name**|| 接続情報オブジェクトの関数コードで使用される変数名。 |
|**hubName**|**HubName**| この値は、接続情報が生成される SignalR ハブの名前に設定する必要があります。|
|**userId**|**UserId**| 省略可能:アクセス キー トークンに設定するユーザー識別子要求の値。 |
|**connectionStringSetting**|**ConnectionStringSetting**| SignalR Service 接続文字列を含むアプリ設定の名前 (既定値は "AzureSignalRConnectionString") |

### <a name="signalr"></a>SignalR

次の表は、*function.json* ファイルと `SignalR` 属性で設定したバインド構成のプロパティを説明しています。

|function.json のプロパティ | 属性のプロパティ |説明|
|---------|---------|----------------------|
|**type**|| `signalR` に設定する必要があります。|
|**direction**|| `out` に設定する必要があります。|
|**name**|| 接続情報オブジェクトの関数コードで使用される変数名。 |
|**hubName**|**HubName**| この値は、接続情報が生成される SignalR ハブの名前に設定する必要があります。|
|**connectionStringSetting**|**ConnectionStringSetting**| SignalR Service 接続文字列を含むアプリ設定の名前 (既定値は "AzureSignalRConnectionString") |

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [Azure Functions のトリガーとバインドの詳細情報](functions-triggers-bindings.md)

