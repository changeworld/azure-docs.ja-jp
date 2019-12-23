---
title: Azure Functions における SignalR Service のバインド
description: Azure Functions で SignalR Service のバインドを使用する方法を説明します。
author: craigshoemaker
ms.topic: reference
ms.date: 02/28/2019
ms.author: cshoe
ms.openlocfilehash: 208b5462efeb579e30550824bd7ba931db1825b2
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/08/2019
ms.locfileid: "74925636"
---
# <a name="signalr-service-bindings-for-azure-functions"></a>Azure Functions における SignalR Service のバインド

この記事では、Azure Functions で SignalR Service のバインドを使用して、[Azure SignalR Service](https://azure.microsoft.com/services/signalr-service/) に接続されたクライアントに対して認証を行い、リアルタイム メッセージを送信する方法を説明します。 Azure Functions は、SignalR Service の入力および出力バインドをサポートしています。

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

## <a name="packages---functions-2x-and-higher"></a>パッケージ - Functions 2.x 以降

SignalR Service のバインドは [Microsoft.Azure.WebJobs.Extensions.SignalRService](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.SignalRService) NuGet パッケージ、バージョン 1.* で提供されます。 パッケージのソース コードは、[azure-functions-signalrservice-extension](https://github.com/Azure/azure-functions-signalrservice-extension) GitHub リポジトリにあります。

[!INCLUDE [functions-package-v2](../../includes/functions-package-v2-manual-portal.md)]

SignalR Service と Azure Functions を構成して一緒に使用する方法の詳細については、「[Azure Functions development and configuration with Azure SignalR Service (Azure SignalR Service を使用した Azure Functions の開発と構成)](../azure-signalr/signalr-concept-serverless-development-config.md)」を参照してください。

### <a name="annotations-library-java-only"></a>注釈ライブラリ (Java のみ)

SignalR サービスの注釈を Java 関数で使用するには、*azure-functions-java-library-signalr* アーティファクト (バージョン 1.0 以降) への依存関係を pom.xml に追加する必要があります。

```xml
<dependency>
    <groupId>com.microsoft.azure.functions</groupId>
    <artifactId>azure-functions-java-library-signalr</artifactId>
    <version>1.0.0</version>
</dependency>
```

## <a name="input"></a>入力

クライアントが Azure SignalR Service に接続するには、そのクライアントに、サービス エンドポイント URL と有効なアクセス トークンが必要です。 サービスへの接続に使用される SignalR Service エンドポイント URL と有効なトークンは、*SignalRConnectionInfo* 入力バインドによって生成されます。 トークンを使用できる期間は限られています。また、トークンを使うと、特定のユーザーを接続に対して認証できます。このため、トークンをキャッシュしたりクライアント間で共有したりすることはお勧めしません。 このバインドを使用した HTTP トリガーは、クライアントが接続情報を取得するときに使用できます。

SignalR クライアント SDK によって使用できる "negotiate" 関数を作成するためのこのバインディングの使用方法の詳細については、 SignalR Service の概念に関するドキュメントの [Azure Functions の開発と構成](../azure-signalr/signalr-concept-serverless-development-config.md)に関する記事を参照してください。

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

次の例は、入力バインドを使用して SignalR 接続情報を取得し、HTTP 経由でそれを返す [C# 関数](functions-dotnet-class-library.md)を示しています。

```cs
[FunctionName("negotiate")]
public static SignalRConnectionInfo Negotiate(
    [HttpTrigger(AuthorizationLevel.Anonymous)]HttpRequest req,
    [SignalRConnectionInfo(HubName = "chat")]SignalRConnectionInfo connectionInfo)
{
    return connectionInfo;
}
```

# <a name="c-scripttabcsharp-script"></a>[C# スクリプト](#tab/csharp-script)

次の例は、*function.json* ファイルの SignalR 接続情報入力バインドと、そのバインドを使用して接続情報を返す [C# スクリプト関数](functions-reference-csharp.md)を示しています。

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

C# スクリプト コードを次に示します。

```cs
#r "Microsoft.Azure.WebJobs.Extensions.SignalRService"
using Microsoft.Azure.WebJobs.Extensions.SignalRService;

public static SignalRConnectionInfo Run(HttpRequest req, SignalRConnectionInfo connectionInfo)
{
    return connectionInfo;
}
```

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

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
module.exports = async function (context, req, connectionInfo) {
    context.res.body = connectionInfo;
};
```

# <a name="pythontabpython"></a>[Python](#tab/python)

次の例は、*function.json* ファイルの SignalR 接続情報入力バインドと、そのバインドを使用して接続情報を返す [Python 関数](functions-reference-python.md)を示しています。

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

Python コードを次に示します。

```python
def main(req: func.HttpRequest, connectionInfoJson: str) -> func.HttpResponse:
    return func.HttpResponse(
        connectionInfoJson,
        status_code=200,
        headers={
            'Content-type': 'application/json'
        }
    )
```

# <a name="javatabjava"></a>[Java](#tab/java)

次の例は、入力バインドを使用して SignalR 接続情報を取得し、HTTP 経由でそれを返す [Java 関数](functions-reference-java.md)を示しています。

```java
@FunctionName("negotiate")
public SignalRConnectionInfo negotiate(
        @HttpTrigger(
            name = "req",
            methods = { HttpMethod.POST },
            authLevel = AuthorizationLevel.ANONYMOUS) HttpRequestMessage<Optional<String>> req,
        @SignalRConnectionInfoInput(
            name = "connectionInfo",
            hubName = "chat") SignalRConnectionInfo connectionInfo) {
    return connectionInfo;
}
```

---

### <a name="authenticated-tokens"></a>認証済みトークン

認証済みクライアントによって関数がトリガーされている場合は、ユーザー ID 要求を生成済みトークンに追加できます。 [App Service 認証](../app-service/overview-authentication-authorization.md)を使用すると、認証を関数アプリに簡単に追加することができます。

App Service 認証によって、`x-ms-client-principal-id` および `x-ms-client-principal-name` という名前の HTTP ヘッダーが設定されます。この 2 つの HTTP ヘッダーには、認証済みユーザーのクライアント プリンシパルの ID と名前がそれぞれ含まれています。

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

バインドの `UserId` プロパティをいずれかのヘッダーの値に設定するには、[バインド式](./functions-bindings-expressions-patterns.md)として `{headers.x-ms-client-principal-id}` または `{headers.x-ms-client-principal-name}` を使用します。

```cs
[FunctionName("negotiate")]
public static SignalRConnectionInfo Negotiate(
    [HttpTrigger(AuthorizationLevel.Anonymous)]HttpRequest req, 
    [SignalRConnectionInfo
        (HubName = "chat", UserId = "{headers.x-ms-client-principal-id}")]
        SignalRConnectionInfo connectionInfo)
{
    // connectionInfo contains an access key token with a name identifier claim set to the authenticated user
    return connectionInfo;
}
```

# <a name="c-scripttabcsharp-script"></a>[C# スクリプト](#tab/csharp-script)

バインドの `userId` プロパティをいずれかのヘッダーの値に設定するには、[バインド式](./functions-bindings-expressions-patterns.md)として `{headers.x-ms-client-principal-id}` または `{headers.x-ms-client-principal-name}` を使用します。

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

C# スクリプト コードを次に示します。

```cs
#r "Microsoft.Azure.WebJobs.Extensions.SignalRService"
using Microsoft.Azure.WebJobs.Extensions.SignalRService;

public static SignalRConnectionInfo Run(HttpRequest req, SignalRConnectionInfo connectionInfo)
{
    // connectionInfo contains an access key token with a name identifier
    // claim set to the authenticated user
    return connectionInfo;
}
```

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

バインドの `userId` プロパティをいずれかのヘッダーの値に設定するには、[バインド式](./functions-bindings-expressions-patterns.md)として `{headers.x-ms-client-principal-id}` または `{headers.x-ms-client-principal-name}` を使用します。

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
module.exports = async function (context, req, connectionInfo) {
    // connectionInfo contains an access key token with a name identifier
    // claim set to the authenticated user
    context.res.body = connectionInfo;
};
```

# <a name="pythontabpython"></a>[Python](#tab/python)

バインドの `userId` プロパティをいずれかのヘッダーの値に設定するには、[バインド式](./functions-bindings-expressions-patterns.md)として `{headers.x-ms-client-principal-id}` または `{headers.x-ms-client-principal-name}` を使用します。

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

Python コードを次に示します。

```python
def main(req: func.HttpRequest, connectionInfoJson: str) -> func.HttpResponse:
    # connectionInfo contains an access key token with a name identifier
    # claim set to the authenticated user
    return func.HttpResponse(
        connectionInfoJson,
        status_code=200,
        headers={
            'Content-type': 'application/json'
        }
    )
```

# <a name="javatabjava"></a>[Java](#tab/java)

バインドの `userId` プロパティをいずれかのヘッダーの値に設定するには、[バインド式](./functions-bindings-expressions-patterns.md)として `{headers.x-ms-client-principal-id}` または `{headers.x-ms-client-principal-name}` を使用します。

```java
@FunctionName("negotiate")
public SignalRConnectionInfo negotiate(
        @HttpTrigger(
            name = "req",
            methods = { HttpMethod.POST },
            authLevel = AuthorizationLevel.ANONYMOUS) HttpRequestMessage<Optional<String>> req,
        @SignalRConnectionInfoInput(
            name = "connectionInfo",
            hubName = "chat",
            userId = "{headers.x-ms-client-principal-id}") SignalRConnectionInfo connectionInfo) {
    return connectionInfo;
}
```

---

## <a name="output"></a>Output

*SignalR* 出力バインドを使用して、Azure SignalR Service で 1 つ以上のメッセージを送信します。 すべての接続済みクライアントにメッセージをブロードキャストすることも、特定のユーザーに対して認証されている接続済みクライアントだけにブロードキャストすることも可能です。

これは、ユーザーが属するグループの管理にも使用できます。

### <a name="broadcast-to-all-clients"></a>すべてのクライアントにブロードキャストする

次の例は、出力バインドを使用してすべての接続済みクライアントにメッセージを送信する関数を示しています。 *target* は、各クライアントで呼び出されるメソッドの名前です。 *Arguments* は、クライアント メソッドに渡される 0 個以上のオブジェクトの配列です。

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

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

# <a name="c-scripttabcsharp-script"></a>[C# スクリプト](#tab/csharp-script)

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

C# スクリプト コードを次に示します。

```cs
#r "Microsoft.Azure.WebJobs.Extensions.SignalRService"
using Microsoft.Azure.WebJobs.Extensions.SignalRService;

public static Task Run(
    object message,
    IAsyncCollector<SignalRMessage> signalRMessages)
{
    return signalRMessages.AddAsync(
        new SignalRMessage 
        {
            Target = "newMessage", 
            Arguments = new [] { message } 
        });
}
```

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

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
module.exports = async function (context, req) {
    context.bindings.signalRMessages = [{
        "target": "newMessage",
        "arguments": [ req.body ]
    }];
};
```

# <a name="pythontabpython"></a>[Python](#tab/python)

*function.json* ファイルのバインド データを次に示します。

function.json の例:

```json
{
  "type": "signalR",
  "name": "out_message",
  "hubName": "<hub_name>",
  "connectionStringSetting": "<name of setting containing SignalR Service connection string>",
  "direction": "out"
}
```

Python コードを次に示します。

```python
def main(req: func.HttpRequest, out_message: func.Out[str]) -> func.HttpResponse:
    message = req.get_json()
    out_message.set(json.dumps({
        'target': 'newMessage',
        'arguments': [ message ]
    }))
```

# <a name="javatabjava"></a>[Java](#tab/java)

```java
@FunctionName("sendMessage")
@SignalROutput(name = "$return", hubName = "chat")
public SignalRMessage sendMessage(
        @HttpTrigger(
            name = "req",
            methods = { HttpMethod.POST },
            authLevel = AuthorizationLevel.ANONYMOUS) HttpRequestMessage<Object> req) {

    SignalRMessage message = new SignalRMessage();
    message.target = "newMessage";
    message.arguments.add(req.getBody());
    return message;
}
```

---

### <a name="send-to-a-user"></a>ユーザーに送信する

ユーザーに対して認証された接続だけにメッセージを送信するには、SignalR メッセージの *user ID* を設定します。

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

```cs
[FunctionName("SendMessage")]
public static Task SendMessage(
    [HttpTrigger(AuthorizationLevel.Anonymous, "post")]object message, 
    [SignalR(HubName = "chat")]IAsyncCollector<SignalRMessage> signalRMessages)
{
    return signalRMessages.AddAsync(
        new SignalRMessage 
        {
            // the message will only be sent to this user ID
            UserId = "userId1",
            Target = "newMessage",
            Arguments = new [] { message }
        });
}
```

# <a name="c-scripttabcsharp-script"></a>[C# スクリプト](#tab/csharp-script)

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

C# スクリプト コードを次に示します。

```cs
#r "Microsoft.Azure.WebJobs.Extensions.SignalRService"
using Microsoft.Azure.WebJobs.Extensions.SignalRService;

public static Task Run(
    object message,
    IAsyncCollector<SignalRMessage> signalRMessages)
{
    return signalRMessages.AddAsync(
        new SignalRMessage 
        {
            // the message will only be sent to this user ID
            UserId = "userId1",
            Target = "newMessage", 
            Arguments = new [] { message } 
        });
}
```

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

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
module.exports = async function (context, req) {
    context.bindings.signalRMessages = [{
        // message will only be sent to this user ID
        "userId": "userId1",
        "target": "newMessage",
        "arguments": [ req.body ]
    }];
};
```

# <a name="pythontabpython"></a>[Python](#tab/python)

*function.json* ファイルのバインド データを次に示します。

function.json の例:

```json
{
  "type": "signalR",
  "name": "out_message",
  "hubName": "<hub_name>",
  "connectionStringSetting": "<name of setting containing SignalR Service connection string>",
  "direction": "out"
}
```

Python コードを次に示します。

```python
def main(req: func.HttpRequest, out_message: func.Out[str]) -> func.HttpResponse:
    message = req.get_json()
    out_message.set(json.dumps({
        #message will only be sent to this user ID
        'userId': 'userId1',
        'target': 'newMessage',
        'arguments': [ message ]
    }))
```

# <a name="javatabjava"></a>[Java](#tab/java)

```java
@FunctionName("sendMessage")
@SignalROutput(name = "$return", hubName = "chat")
public SignalRMessage sendMessage(
        @HttpTrigger(
            name = "req",
            methods = { HttpMethod.POST },
            authLevel = AuthorizationLevel.ANONYMOUS) HttpRequestMessage<Object> req) {

    SignalRMessage message = new SignalRMessage();
    message.userId = "userId1";
    message.target = "newMessage";
    message.arguments.add(req.getBody());
    return message;
}
```

---

### <a name="send-to-a-group"></a>グループに送信する

グループに追加された接続だけにメッセージを送信するには、SignalR メッセージの *group name* を設定します。

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

```cs
[FunctionName("SendMessage")]
public static Task SendMessage(
    [HttpTrigger(AuthorizationLevel.Anonymous, "post")]object message,
    [SignalR(HubName = "chat")]IAsyncCollector<SignalRMessage> signalRMessages)
{
    return signalRMessages.AddAsync(
        new SignalRMessage
        {
            // the message will be sent to the group with this name
            GroupName = "myGroup",
            Target = "newMessage",
            Arguments = new [] { message }
        });
}
```

# <a name="c-scripttabcsharp-script"></a>[C# スクリプト](#tab/csharp-script)

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

C# スクリプト コードを次に示します。

```cs
#r "Microsoft.Azure.WebJobs.Extensions.SignalRService"
using Microsoft.Azure.WebJobs.Extensions.SignalRService;

public static Task Run(
    object message,
    IAsyncCollector<SignalRMessage> signalRMessages)
{
    return signalRMessages.AddAsync(
        new SignalRMessage 
        {
            // the message will be sent to the group with this name
            GroupName = "myGroup",
            Target = "newMessage", 
            Arguments = new [] { message } 
        });
}
```

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

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
module.exports = async function (context, req) {
    context.bindings.signalRMessages = [{
        // message will only be sent to this group
        "groupName": "myGroup",
        "target": "newMessage",
        "arguments": [ req.body ]
    }];
};
```

# <a name="pythontabpython"></a>[Python](#tab/python)

*function.json* ファイルのバインド データを次に示します。

function.json の例:

```json
{
  "type": "signalR",
  "name": "out_message",
  "hubName": "<hub_name>",
  "connectionStringSetting": "<name of setting containing SignalR Service connection string>",
  "direction": "out"
}
```

Python コードを次に示します。

```python
def main(req: func.HttpRequest, out_message: func.Out[str]) -> func.HttpResponse:
    message = req.get_json()
    out_message.set(json.dumps({
        #message will only be sent to this group
        'groupName': 'myGroup',
        'target': 'newMessage',
        'arguments': [ message ]
    }))
```

# <a name="javatabjava"></a>[Java](#tab/java)

```java
@FunctionName("sendMessage")
@SignalROutput(name = "$return", hubName = "chat")
public SignalRMessage sendMessage(
        @HttpTrigger(
            name = "req",
            methods = { HttpMethod.POST },
            authLevel = AuthorizationLevel.ANONYMOUS) HttpRequestMessage<Object> req) {

    SignalRMessage message = new SignalRMessage();
    message.groupName = "myGroup";
    message.target = "newMessage";
    message.arguments.add(req.getBody());
    return message;
}
```

---

### <a name="group-management"></a>グループ管理

SignalR Service を使用すると、ユーザーをグループに追加できます。 その後にメッセージをグループに送信できます。 `SignalR` 出力バインドを使用して、ユーザーのグループ メンバーシップを管理できます。

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

#### <a name="add-user-to-a-group"></a>ユーザーをグループに追加する

次の例では、ユーザーをグループに追加します。

```csharp
[FunctionName("addToGroup")]
public static Task AddToGroup(
    [HttpTrigger(AuthorizationLevel.Anonymous, "post")]HttpRequest req,
    ClaimsPrincipal claimsPrincipal,
    [SignalR(HubName = "chat")]
        IAsyncCollector<SignalRGroupAction> signalRGroupActions)
{
    var userIdClaim = claimsPrincipal.FindFirst(ClaimTypes.NameIdentifier);
    return signalRGroupActions.AddAsync(
        new SignalRGroupAction
        {
            UserId = userIdClaim.Value,
            GroupName = "myGroup",
            Action = GroupAction.Add
        });
}
```

#### <a name="remove-user-from-a-group"></a>グループからユーザーを削除する

次の例では、ユーザーをグループから削除します。

```csharp
[FunctionName("removeFromGroup")]
public static Task RemoveFromGroup(
    [HttpTrigger(AuthorizationLevel.Anonymous, "post")]HttpRequest req,
    ClaimsPrincipal claimsPrincipal,
    [SignalR(HubName = "chat")]
        IAsyncCollector<SignalRGroupAction> signalRGroupActions)
{
    var userIdClaim = claimsPrincipal.FindFirst(ClaimTypes.NameIdentifier);
    return signalRGroupActions.AddAsync(
        new SignalRGroupAction
        {
            UserId = userIdClaim.Value,
            GroupName = "myGroup",
            Action = GroupAction.Remove
        });
}
```

> [!NOTE]
> `ClaimsPrincipal` を正しくバインドするためには、Azure Functions で認証設定を構成しておく必要があります。

# <a name="c-scripttabcsharp-script"></a>[C# スクリプト](#tab/csharp-script)

#### <a name="add-user-to-a-group"></a>ユーザーをグループに追加する

次の例では、ユーザーをグループに追加します。

*function.json* の例

```json
{
    "type": "signalR",
    "name": "signalRGroupActions",
    "connectionStringSetting": "<name of setting containing SignalR Service connection string>",
    "hubName": "chat",
    "direction": "out"
}
```

*Run.csx*

```cs
#r "Microsoft.Azure.WebJobs.Extensions.SignalRService"
using Microsoft.Azure.WebJobs.Extensions.SignalRService;

public static Task Run(
    HttpRequest req,
    ClaimsPrincipal claimsPrincipal,
    IAsyncCollector<SignalRGroupAction> signalRGroupActions)
{
    var userIdClaim = claimsPrincipal.FindFirst(ClaimTypes.NameIdentifier);
    return signalRGroupActions.AddAsync(
        new SignalRGroupAction
        {
            UserId = userIdClaim.Value,
            GroupName = "myGroup",
            Action = GroupAction.Add
        });
}
```

#### <a name="remove-user-from-a-group"></a>グループからユーザーを削除する

次の例では、ユーザーをグループから削除します。

*function.json* の例

```json
{
    "type": "signalR",
    "name": "signalRGroupActions",
    "connectionStringSetting": "<name of setting containing SignalR Service connection string>",
    "hubName": "chat",
    "direction": "out"
}
```

*Run.csx*

```cs
#r "Microsoft.Azure.WebJobs.Extensions.SignalRService"
using Microsoft.Azure.WebJobs.Extensions.SignalRService;

public static Task Run(
    HttpRequest req,
    ClaimsPrincipal claimsPrincipal,
    IAsyncCollector<SignalRGroupAction> signalRGroupActions)
{
    var userIdClaim = claimsPrincipal.FindFirst(ClaimTypes.NameIdentifier);
    return signalRGroupActions.AddAsync(
        new SignalRGroupAction
        {
            UserId = userIdClaim.Value,
            GroupName = "myGroup",
            Action = GroupAction.Remove
        });
}
```

> [!NOTE]
> `ClaimsPrincipal` を正しくバインドするためには、Azure Functions で認証設定を構成しておく必要があります。

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

#### <a name="add-user-to-a-group"></a>ユーザーをグループに追加する

次の例では、ユーザーをグループに追加します。

*function.json* の例

```json
{
    "type": "signalR",
    "name": "signalRGroupActions",
    "connectionStringSetting": "<name of setting containing SignalR Service connection string>",
    "hubName": "chat",
    "direction": "out"
}
```

*index.js*

```javascript
module.exports = async function (context, req) {
  context.bindings.signalRGroupActions = [{
    "userId": req.query.userId,
    "groupName": "myGroup",
    "action": "add"
  }];
};
```

#### <a name="remove-user-from-a-group"></a>グループからユーザーを削除する

次の例では、ユーザーをグループから削除します。

*function.json* の例

```json
{
    "type": "signalR",
    "name": "signalRGroupActions",
    "connectionStringSetting": "<name of setting containing SignalR Service connection string>",
    "hubName": "chat",
    "direction": "out"
}
```

*index.js*

```javascript
module.exports = async function (context, req) {
  context.bindings.signalRGroupActions = [{
    "userId": req.query.userId,
    "groupName": "myGroup",
    "action": "remove"
  }];
};
```

# <a name="pythontabpython"></a>[Python](#tab/python)

#### <a name="add-user-to-a-group"></a>ユーザーをグループに追加する

次の例では、ユーザーをグループに追加します。

*function.json* の例

```json
{
    "type": "signalR",
    "name": "action",
    "connectionStringSetting": "<name of setting containing SignalR Service connection string>",
    "hubName": "chat",
    "direction": "out"
}
```

*\_\_init.py__*

```python
def main(req: func.HttpRequest, action: func.Out[str]) -> func.HttpResponse:
    action.set(json.dumps({
        'userId': 'userId1',
        'groupName': 'myGroup',
        'action': 'add'
    }))
```

#### <a name="remove-user-from-a-group"></a>グループからユーザーを削除する

次の例では、ユーザーをグループから削除します。

*function.json* の例

```json
{
    "type": "signalR",
    "name": "action",
    "connectionStringSetting": "<name of setting containing SignalR Service connection string>",
    "hubName": "chat",
    "direction": "out"
}
```

*\_\_init.py__*

```python
def main(req: func.HttpRequest, action: func.Out[str]) -> func.HttpResponse:
    action.set(json.dumps({
        'userId': 'userId1',
        'groupName': 'myGroup',
        'action': 'remove'
    }))
```

# <a name="javatabjava"></a>[Java](#tab/java)

#### <a name="add-user-to-a-group"></a>ユーザーをグループに追加する

次の例では、ユーザーをグループに追加します。

```java
@FunctionName("addToGroup")
@SignalROutput(name = "$return", hubName = "chat")
public SignalRGroupAction addToGroup(
        @HttpTrigger(
            name = "req",
            methods = { HttpMethod.POST },
            authLevel = AuthorizationLevel.ANONYMOUS) HttpRequestMessage<Object> req,
        @BindingName("userId") String userId) {

    SignalRGroupAction groupAction = new SignalRGroupAction();
    groupAction.action = "add";
    groupAction.userId = userId;
    groupAction.groupName = "myGroup";
    return action;
}
```

#### <a name="remove-user-from-a-group"></a>グループからユーザーを削除する

次の例では、ユーザーをグループから削除します。

```java
@FunctionName("removeFromGroup")
@SignalROutput(name = "$return", hubName = "chat")
public SignalRGroupAction removeFromGroup(
        @HttpTrigger(
            name = "req",
            methods = { HttpMethod.POST },
            authLevel = AuthorizationLevel.ANONYMOUS) HttpRequestMessage<Object> req,
        @BindingName("userId") String userId) {

    SignalRGroupAction groupAction = new SignalRGroupAction();
    groupAction.action = "remove";
    groupAction.userId = userId;
    groupAction.groupName = "myGroup";
    return action;
}
```

---

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

> [!div class="nextstepaction"]
> [Azure SignalR Service を使用した Azure Functions の開発と構成](../azure-signalr/signalr-concept-serverless-development-config.md)
