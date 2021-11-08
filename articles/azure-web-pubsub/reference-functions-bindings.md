---
title: リファレンス - Azure Functions での Azure Web PubSub のトリガーとバインド
description: このリファレンスでは、Azure Functions での Azure Web PubSub のトリガーとバインドについて説明します
author: vicancy
ms.author: lianwei
ms.service: azure-web-pubsub
ms.topic: conceptual
ms.date: 11/08/2021
ms.openlocfilehash: 981160fe8d0778d122dd7dbd92c0c27c468ff34a
ms.sourcegitcommit: 27ddccfa351f574431fb4775e5cd486eb21080e0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/08/2021
ms.locfileid: "131997896"
---
#  <a name="azure-web-pubsub-trigger-and-bindings-for-azure-functions"></a>Azure Functions での Azure Web PubSub のトリガーとバインド

このリファレンスでは、Azure Functions で Web PubSub イベントを処理する方法について説明します。

Web PubSub は、開発者がリアルタイムの機能と発行-サブスクライブ パターンを使用して Web アプリケーションを簡単に構築するために役立つ Azure マネージド サービスです。

| アクション | Type |
|---------|---------|
| サービスからメッセージを受信したときに関数を実行する | [トリガー バインド](#trigger-binding) |
| サービス エンドポイント URL とアクセス トークンを返す | [入力バインド](#input-binding)
| Web PubSub メッセージを送信する |[出力バインド](#output-binding) |

[ソース コード](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/webpubsub/) |
[パッケージ](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.WebPubSub) |
[API リファレンス ドキュメント](https://github.com/Azure/azure-sdk-for-net/blob/main/sdk/webpubsub/Microsoft.Azure.WebJobs.Extensions.WebPubSub/api/Microsoft.Azure.WebJobs.Extensions.WebPubSub.netstandard2.0.cs) |
[製品ドキュメント](./index.yml) |
[サンプル][samples_ref]

## <a name="add-to-your-functions-app"></a>Functions アプリに追加する

トリガーとバインドを使用するには、適切なパッケージを参照する必要があります。 NuGet パッケージは .NET クラス ライブラリに使用されますが、他のすべてのアプリケーションの種類には拡張バンドルが使用されます。

| 言語                                        | 追加手段                                   | 解説 
|-------------------------------------------------|---------------------------------------------|-------------|
| C#                                              | [NuGet パッケージ] バージョン プレリリースをインストールする | |
| C# スクリプト、JavaScript、Python、PowerShell       | [拡張機能を明示的にインストールする]                    | Visual Studio Code で使用するには [Azure Tools 拡張機能]をお勧めします。 |
| C# スクリプト (Azure portal ではオンラインのみ)         | バインディングを追加する                                   | 関数アプリを再発行せずにポータルで既存のバインディング拡張機能を更新するには、[拡張機能の更新]に関する記事を参照してください。 |

指定したパッケージとバージョンの [NuGet](https://www.nuget.org/) からクライアント ライブラリをインストールします。

```bash
func extensions install --package Microsoft.Azure.WebJobs.Extensions.WebPubSub --version 1.0.0-beta.3
```

[NuGet パッケージ]: https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.WebPubSub
[拡張機能を明示的にインストールする]: ../azure-functions/functions-bindings-register.md#explicitly-install-extensions 
[Azure Tools 拡張機能]: https://marketplace.visualstudio.com/items?itemName=ms-vscode.vscode-node-azure-pack
[拡張機能の更新]: ../azure-functions/functions-bindings-register.md

## <a name="key-concepts"></a>主要な概念

![関数アプリを操作する Azure Web PubSub サービスのワークフローを示す図。](./media/reference-functions-bindings/functions-workflow.png)

(1)-(2) クライアント接続を生成する HttpTrigger を使用した `WebPubSubConnection` 入力バインド。

(3)-(4) サービス要求を処理する `WebPubSubTrigger` トリガー バインドまたは HttpTrigger を使用した `WebPubSubRequest` 入力バインド。

(5)-(6) サービスに何らかの実行を要求する `WebPubSub` 出力バインド。

## <a name="trigger-binding"></a>トリガー バインド

関数トリガーを使用して、Azure Web PubSub サービスからの要求を処理します。 

`WebPubSubTrigger` は、サービス側からの要求を処理する必要がある場合に使用します。 トリガー エンドポイント パターンは次のようになります。これは、Web PubSub サービス側で設定する必要があります (ポータル: 設定 -> イベント ハンドラー -> URL テンプレート)。 エンドポイント パターンでは、[セキュリティ](../azure-functions/security-concepts.md#system-key)上の理由で、Azure 関数アプリを使用している場合は、クエリ部分 `code=<API_KEY>` が **必須** です。 このキーは **Azure portal** で確認できます。 関数アプリを Azure にデプロイした後に、関数アプリ リソースを見つけて、 **[関数]**  ->  **[アプリ キー]**  ->  **[システム キー]**  ->  **[webpubsub_extension]** の順に移動します。 ただし、ローカル関数を使用する場合、このキーは必要ありません。

```
<Function_App_Url>/runtime/webhooks/webpubsub?code=<API_KEY>
```

### <a name="example"></a>例


# <a name="c"></a>[C#](#tab/csharp)

```cs
[FunctionName("WebPubSubTrigger")]
public static void Run(
    [WebPubSubTrigger("<hub>", "message", EventType.User)] 
    ConnectionContext context,
    string message,
    MessageDataType dataType)
{
    Console.WriteLine($"Request from: {context.userId}");
    Console.WriteLine($"Request message: {message}");
    Console.WriteLine($"Request message DataType: {dataType}");
}
```

`WebPubSubTrigger` バインディングでは、一部のシナリオで戻り値もサポートされています。たとえば、サーバーがクライアント要求を確認して拒否する場合、または要求クライアントに直接メッセージを送信する場合の、`Connect`、`Message` イベントなどです。 `Connect` イベントでは `ConnectResponse` および `ErrorResponse` が考慮され、`Message` イベントでは `MessageResponse` および `ErrorResponse` が考慮され、現在のシナリオと一致しない残りの型は無視されます。 さらに、`ErrorResponse` が返された場合、サービスによってクライアント接続が切断されます。

```cs
[FunctionName("WebPubSubTriggerReturnValue")]
public static MessageResponse Run(
    [WebPubSubTrigger("<hub>", "message", EventType.User)] 
    ConnectionContext context,
    string message,
    MessageDataType dataType)
{
    return new MessageResponse
    {
        Message = BinaryData.FromString("ack"),
        DataType = MessageDataType.Text
    };
}
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

`function.json` でトリガー バインディングを定義します。

```json
{
  "disabled": false,
  "bindings": [
    {
      "type": "webPubSubTrigger",
      "direction": "in",
      "name": "message",
      "hub": "<hub>",
      "eventName": "message",
      "eventType": "user"
    }
  ]
}
```

`index.js` で関数を定義します。

```js
module.exports = function (context, message) {
  console.log('Request from: ', context.userId);
  console.log('Request message: ', message);
  console.log('Request message dataType: ', context.bindingData.dataType);
}
```

`WebPubSubTrigger` バインディングでは、一部のシナリオで戻り値もサポートされています。たとえば、`Connect`、`Message` イベントなどです。 サーバーがクライアント要求を確認して拒否できるか、または要求クライアントに直接メッセージを送信できる場合。 JavaScript のタイプレス言語では、オブジェクト キーに関して逆シリアル化されます。 また `ErrorResponse` は、残りのオブジェクトと比較して最高の優先順位を持ちます。`code` が戻り値に含まれる場合、それは `ErrorResponse` に解析され、クライアント接続が切断されます。

```js
module.exports = async function (context) {
  return { 
    "message": "ack",
    "dataType" : "text"
  };
}
```

---


### <a name="attributes-and-annotations"></a>属性と注釈

[C# クラス ライブラリ](../azure-functions/functions-dotnet-class-library.md)では、`WebPubSubTrigger` 属性を使用します。

メソッド シグネチャでの `WebPubSubTrigger` 属性を次に示します。

```csharp
[FunctionName("WebPubSubTrigger")]
public static void Run([WebPubSubTrigger("<hub>", "<eventName>", <eventType>)] 
ConnectionContext context, ILogger log)
{
    ...
}
```

完全な例については、「C# の例」を参照してください。

### <a name="configuration"></a>構成

次の表は、*function.json* ファイルで設定したバインド構成のプロパティを説明しています。

| function.json のプロパティ | 属性のプロパティ | 説明 |
|---------|---------|---------|
| **type** | 該当なし |必須 - `webPubSubTrigger` に設定する必要があります。 |
| **direction** | 該当なし | 必須 - `in` に設定する必要があります。 |
| **name** | 該当なし | 必須 - イベント データを受信するパラメーターの、関数コードで使われている変数名。 |
| **hub** | ハブ | 必須 - この値は、トリガーされる関数の Web PubSub ハブの名前に設定する必要があります。 高い優先順位として属性への値の設定をサポートしていますが、またはグローバル値としてアプリ設定に設定することもできます。 |
| **eventType** | EventType | 必須 - この値は、トリガーされる関数のメッセージのイベントの種類として設定する必要があります。 値は、`user` または `system` である必要があります。 |
| **eventName** | EventName | 必須 - この値は、トリガーされる関数のメッセージのイベントとして設定する必要があります。 </br> `system` イベントの種類の場合、イベント名は `connect`、`connected`、`disconnect` である必要があります。 </br> システムでサポートされているサブプロトコル `json.webpubsub.azure.v1.` の場合、イベント名はユーザー定義のイベント名です。 </br> ユーザー定義のサブプロトコルの場合、イベント名は `message` です。 |

### <a name="usages"></a>使用法

C# で `ConnectionContext` は、型が認識されたバインディング パラメーターであり、残りのパラメーターはパラメーター名によってバインドされます。 下の使用可能なパラメーターと型の表を確認してください。

JavaScript のようなタイプレスの言語では、`function.json` の `name` を使用して、下のマッピング テーブルに関してトリガー オブジェクトがバインドされます。 さらに、トリガー入力のバインディング オブジェクトとして `name` が `message` に設定されている場合に、`function.json` の `dataType` が考慮され、それに応じてメッセージが変換されます。 すべてのパラメーターは `context.bindingData.<BindingName>` から読み取ることができ、`JObject` 変換されます。 

| バインディング名 | バインドの種類 | 説明 | プロパティ |
|---------|---------|---------|---------|
|connectionContext|`ConnectionContext`|一般的な要求情報| EventType、EventName、Hub、ConnectionId、UserId、Headers、Signature |
|message|`BinaryData`,`string`,`Stream`,`byte[]`| クライアントからの要求メッセージ | -|
|dataType|`MessageDataType`| 要求メッセージのデータ型。`binary`、`text`、`json` がサポートされています | -|
|claims|`IDictionary<string, string[]>`|`connect` 要求でのユーザー要求 | -|
|query|`IDictionary<string, string[]>`|`connect` 要求でのユーザー クエリ | -|
|subprotocols|`string[]`|`connect` 要求での使用可能なサブプロトコル | -|
|clientCertificates|`ClientCertificate[]`|`connect` 要求でのクライアントからの証明書の拇印の一覧|-|
|reason|`string`|切断要求での理由|-|

### <a name="return-response"></a> 応答を返す

`WebPubSubTrigger` では、`connect` の同期イベントおよびユーザー イベント `message` に対して、顧客が返した応答が考慮されます。 一致した応答だけがサービスに返送されます。それ以外の場合は、無視されます。 

| 戻り値の型 | 説明 | プロパティ |
|---------|---------|---------|
|`ConnectResponse`| `connect` イベントの応答 | Groups、Roles、UserId、Subprotocol |
|`MessageResponse`| ユーザー イベントの応答 | DataType、Message |
|`ErrorResponse`| 同期イベントのエラー応答 | Code、ErrorMessage |
|`ServiceResponse`| 戻り値が不明な場合に使用される、サポートされている基本の応答の種類 | - |

## <a name="input-binding"></a>入力バインド

拡張機能では、さまざまなニーズに対応する 2 つの入力バインドを提供しています。

- `WebPubSubConnection`

  クライアントを Azure Web PubSub サービスに接続させるには、それがサービス エンドポイント URL と有効なアクセス トークンを認識している必要があります。 `WebPubSubConnection` 入力バインディングによって必要な情報が生成されるため、クライアントはこのトークン生成自体を処理する必要がありません。 トークンは時間制限があり、接続に対して特定のユーザーを認証するために使用できるため、トークンをキャッシュしたり、クライアント間で共有したりしないでください。 この入力バインドと連携する HTTP トリガーは、クライアントが接続情報を取得するために使用できます。

- `WebPubSubRequest`

  Static Web Apps を使用している場合は、`HttpTrigger` がサポートされている唯一のトリガーであり、Web PubSub のシナリオでは、`WebPubSubRequest` 入力バインディングを提供し、ユーザーが Web PubSub プロトコルでサービス側からアップストリームの http 要求を逆シリアル化するのに役立ちます。 顧客は、関数で簡単に処理するために、`WebPubSubTrigger` と比較して、同様の結果を得ることができます。 下記の[例](#example---webpubsubrequest)を参照してください。
  `HttpTrigger` と共に使用する場合、顧客は、HttpTrigger で公開されている url をアップストリームでそれに従って構成する必要があります。

### <a name="example---webpubsubconnection"></a>例 - `WebPubSubConnection`

次の例は、入力バインドを使用して Web PubSub 接続情報を取得し、HTTP 経由でそれを返す C# 関数を示しています。

# <a name="c"></a>[C#](#tab/csharp)

```cs
[FunctionName("WebPubSubConnectionInputBinding")]
public static WebPubSubConnection Run(
    [HttpTrigger(AuthorizationLevel.Anonymous, "get", "post")] HttpRequest req,
    [WebPubSubConnection(Hub = "<hub>", UserId = "{query.userid}")] WebPubSubConnection connection)
{
    Console.WriteLine("login");
    return connection;
}
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

`function.json` に入力バインドを定義します。

```json
{
  "disabled": false,
  "bindings": [
    {
      "authLevel": "anonymous",
      "type": "httpTrigger",
      "direction": "in",
      "name": "req"
    },
    {
      "type": "http",
      "direction": "out",
      "name": "res"
    },
    {
      "type": "webPubSubConnection",
      "name": "connection",
      "userId": "{query.userid}",
      "hub": "<hub>",
      "direction": "in"
    }
  ]
}
```

`index.js` で関数を定義します。

```js
module.exports = function (context, req, connection) {
  context.res = { body: connection };
  context.done();
};
```

---

### <a name="authenticated-tokens"></a>認証済み **トークン**

認証済みクライアントによって関数がトリガーされている場合は、ユーザー ID 要求を生成済みトークンに追加できます。 App Service 認証を使用すると、認証を関数アプリに簡単に追加することができます。

App Service 認証によって、`x-ms-client-principal-id` および `x-ms-client-principal-name` という名前の HTTP ヘッダーが設定されます。この 2 つの HTTP ヘッダーには、認証済みユーザーのクライアント プリンシパルの ID と名前がそれぞれ含まれています。

バインドの UserId プロパティをいずれかのヘッダーの値に設定するには、バインド式 `{headers.x-ms-client-principal-id}` または `{headers.x-ms-client-principal-name}` を使用します。

```cs
[FunctionName("WebPubSubConnectionInputBinding")]
public static WebPubSubConnection Run(
    [HttpTrigger(AuthorizationLevel.Anonymous, "get", "post")] HttpRequest req,
    [WebPubSubConnection(Hub = "<hub>", UserId = "{headers.x-ms-client-principal-name}")] WebPubSubConnection connection)
{
    Console.WriteLine("login");
    return connection;
}
```

### <a name="example---webpubsubrequest"></a>例 - `WebPubSubRequest`

次の例は、接続イベントの種類で入力バインドを使用して Web PubSub 要求情報を取得し、HTTP 経由でそれを返す C# 関数を示しています。

# <a name="c"></a>[C#](#tab/csharp)

```cs
[FunctionName("WebPubSubRequestInputBinding")]
public static object Run(
    [HttpTrigger(AuthorizationLevel.Anonymous, "get", "post")] HttpRequest req,
    [WebPubSubRequest] WebPubSubRequest wpsReq)
{
    if (wpsReq.Request.IsValidationRequest || !wpsReq.Request.Valid)
    {
        return wpsReq.Response;
    }
    var request = wpsReq.Request as ConnectEventRequest;
    var response = new ConnectResponse
    {
        UserId = wpsReq.ConnectionContext.UserId
    };
    return response;
}
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

`function.json` に入力バインドを定義します。

```json
{
  "disabled": false,
  "bindings": [
    {
      "authLevel": "anonymous",
      "type": "httpTrigger",
      "direction": "in",
      "name": "req",
      "methods": ["get", "post"]
    },
    {
      "type": "http",
      "direction": "out",
      "name": "$return"
    },
    {
      "type": "webPubSubRequest",
      "name": "wpsReq",
      "direction": "in"
    }
  ]
}
```

`index.js` で関数を定義します。

```js
module.exports = async function (context, req, wpsReq) {
  if (!wpsReq.request.valid || wpsReq.request.isValidationRequest)
  {
    console.log(`invalid request: ${wpsReq.response.message}.`);
    return wpsReq.response;
  }
  console.log(`user: ${context.bindings.wpsReq.connectionContext.userId} is connecting.`);
  return { body: {"userId": context.bindings.wpsReq.connectionContext.userId} };
};
```

---

### <a name="configuration"></a>構成

#### <a name="webpubsubconnection"></a>WebPubSubConnection

次の表は、function.json ファイルと `WebPubSubConnection` 属性で設定したバインド構成のプロパティを説明しています。

| function.json のプロパティ | 属性のプロパティ | 説明 |
|---------|---------|---------|
| **type** | 該当なし | `webPubSubConnection` に設定されている必要があります。 |
| **direction** | 該当なし | `in` に設定されている必要があります。 |
| **name** | 該当なし | 入力接続バインド オブジェクトの関数コードで使用される変数名。 |
| **hub** | ハブ | この値は、トリガーされる関数の Web PubSub ハブの名前に設定する必要があります。 高い優先順位として属性への値の設定をサポートしていますが、またはグローバル値としてアプリ設定に設定することもできます。 |
| **userId** | UserId | 省略可能 - アクセス キー トークンに設定するユーザー識別子要求の値。 |
| **connectionStringSetting** | ConnectionStringSetting | Web PubSub Service 接続文字列を含むアプリ設定の名前 (既定値は "WebPubSubConnectionString") |

#### <a name="webpubsubrequest"></a>WebPubSubRequest

次の表は、functions.json ファイルと `WebPubSubRequest` 属性に設定したバインド構成のプロパティを説明しています。

| function.json のプロパティ | 属性のプロパティ | 説明 |
|---------|---------|---------|
| **type** | 該当なし | `webPubSubRequest` に設定されている必要があります。 |
| **direction** | 該当なし | `in` に設定されている必要があります。 |
| **name** | 該当なし | 入力 Web PubSub 要求の関数コードで使用される変数名。 |

### <a name="usage"></a>使用法

#### <a name="webpubsubconnection"></a>WebPubSubConnection

`WebPubSubConnection` では以下のプロパティが提供されます。

バインディング名 | バインドの種類 | 説明
---------|---------|---------
BaseUrl | string | Web PubSub クライアント接続 url
url | string | Web PubSub 接続の絶対 Uri。要求に基づいて生成された `AccessToken` が含まれます
AccessToken | string | 要求の UserId とサービス情報に基づいて生成された `AccessToken`

#### <a name="webpubsubrequest"></a>WebPubSubRequest

`WebPubSubRequest` では以下のプロパティが提供されます。

バインディング名 | バインドの種類 | 説明 | プロパティ
---------|---------|---------|---------
connectionContext | `ConnectionContext` | 一般的な要求情報| EventType、EventName、Hub、ConnectionId、UserId、Headers、Signature
request | `ServiceRequest` | クライアントからの要求。詳細については以下の表を参照してください | IsValidationRequest、Valid、Unauthorized、BadRequest、ErrorMessage、Name など
応答 | `HttpResponseMessage` | 拡張機能によって、主に `AbuseProtection` とエラーケースに対する応答が作成されます | -

`ServiceRequest` の場合、要求シナリオに関するさまざまな情報を提供するさまざまなクラスに逆シリアル化されます。 `ValidationRequest` または `InvalidRequest` の場合、システムによって作成された応答 `WebPubSubRequest.Response` を直接返すか、または顧客が必要なエラーをログに記録できることが推奨されます。 さまざまなシナリオで、顧客は次のように要求のプロパティを読み取ることができます。

派生クラス | 説明 | プロパティ
--|--|--
`ValidationRequest` | `IsValidationRequest` が **true** の場合に `AbuseProtection` で使用します | -
`ConnectEventRequest` | `Connect` イベントの種類で使用します | Claims、Query、Subprotocols、ClientCertificates
`ConnectedEventRequest` | `Connected` イベントの種類で使用します | -
`MessageEventRequest` | ユーザー イベントの種類で使用します | Message、DataType
`DisconnectedEventRequest` | `Disconnected` イベントの種類で使用します | 理由
`InvalidRequest` | 要求が無効な場合に使用します | -

## <a name="output-binding"></a>出力バインド

Web PubSub 出力バインドを使用して、Azure Web PubSub Service で 1 つ以上のメッセージを送信します。 次の宛先にメッセージをブロードキャストできます。

* 接続されているすべてのクライアント
* 特定のユーザーに対して認証された接続されているクライアント
* 特定のグループに参加している接続されているクライアント

出力バインドにより、グループを管理し、特定の connectionId を対象とするアクセス許可をグループに許可/取り消すこともできます。

セットアップと構成の詳細については、概要を参照してください。

### <a name="example"></a>例

# <a name="c"></a>[C#](#tab/csharp)

```cs
[FunctionName("WebPubSubOutputBinding")]
public static async Task RunAsync(
    [HttpTrigger(AuthorizationLevel.Anonymous, "get", "post")] HttpRequest req,
    [WebPubSub(Hub = "<hub>")] IAsyncCollector<WebPubSubOperation> operations)
{
    await operations.AddAsync(new SendToAll
    {
        Message = BinaryData.FromString("Hello Web PubSub"),
        DataType = MessageDataType.Text
    });
}
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

`functions.json` にバインドを定義します。

```json
{
  "disabled": false,
  "bindings": [
    {
      "type": "webPubSub",
      "name": "webPubSubOperation",
      "hub": "<hub>",
      "direction": "out"
    }
  ]
}
```

`index.js` で関数を定義します。

```js
module.exports = async function (context) {
  context.bindings.webPubSubOperation = {
    "operationKind": "sendToAll",
    "message": "hello",
    "dataType": "text"
  };
  context.done();
}
```

---

### <a name="webpubsuboperation"></a>WebPubSubOperation 

`WebPubSubOperation` は、出力バインドの基本抽象型です。 派生型は、サーバーがサービスに呼び出させる操作を表します。 `javascript` などのタイプレスの言語では、`OperationKind` は型を解決するためのキー パラメーターです。 また、`csharp` のような厳密な型の言語では、ユーザーはターゲットの操作の種類を直接作成でき、顧客割り当ての `OperationKind` 値は無視されます。

派生クラス|プロパティ
--|--
`SendToAll`|Message、DataType、Excluded
`SendToGroup`|Group、Message、DataType、Excluded
`SendToUser`|UserId、Message、DataType
`SendToConnection`|ConnectionId、Message、DataType
`AddUserToGroup`|UserId、Group
`RemoveUserFromGroup`|UserId、Group
`RemoveUserFromAllGroups`|UserId
`AddConnectionToGroup`|ConnectionId、Group
`RemoveConnectionFromGroup`|ConnectionId、Group
`CloseClientConnection`|ConnectionId、Reason
`GrantGroupPermission`|ConnectionId、Group、Permission、TargetName
`RevokeGroupPermission`|ConnectionId、Group、Permission、TargetName

### <a name="configuration"></a>構成

#### <a name="webpubsub"></a>WebPubSub

次の表は、function.json ファイルと `WebPubSub` 属性で設定したバインド構成のプロパティを説明しています。

| function.json のプロパティ | 属性のプロパティ | 説明 |
|---------|---------|---------|
| **type** | 該当なし | `webPubSub` に設定されている必要があります。 |
| **direction** | 該当なし | `out` に設定されている必要があります。 |
| **name** | 該当なし | 出力バインド オブジェクトの関数コードで使用される変数名。 |
| **hub** | ハブ | この値は、トリガーされる関数の Web PubSub ハブの名前に設定する必要があります。 高い優先順位として属性への値の設定をサポートしていますが、またはグローバル値としてアプリ設定に設定することもできます。 |
| **connectionStringSetting** | ConnectionStringSetting | Web PubSub Service 接続文字列を含むアプリ設定の名前 (既定値は "WebPubSubConnectionString") |

## <a name="troubleshooting"></a>トラブルシューティング

### <a name="setting-up-console-logging"></a>コンソール ログの設定
また、サービスに対して行う要求の詳細を確認する場合は、[コンソールのログ記録を簡単に有効にする](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/core/Azure.Core/samples/Diagnostics.md#logging)こともできます。

[azure_sub]: https://azure.microsoft.com/free/
[samples_ref]: https://github.com/Azure/azure-webpubsub/tree/main/samples/functions

## <a name="next-steps"></a>次のステップ

[!INCLUDE [next step](includes/include-next-step.md)]