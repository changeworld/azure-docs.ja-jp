---
title: リファレンス - Azure Functions での Azure Web PubSub のトリガーとバインド
description: このリファレンスでは、Azure Functions での Azure Web PubSub のトリガーとバインドについて説明します
author: vicancy
ms.author: lianwei
ms.service: azure-web-pubsub
ms.topic: conceptual
ms.date: 11/08/2021
ms.openlocfilehash: 863fd6ce34c2f2fb822490ae363cf2f979594c99
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/17/2021
ms.locfileid: "132705191"
---
#  <a name="azure-web-pubsub-trigger-and-bindings-for-azure-functions"></a>Azure Functions での Azure Web PubSub のトリガーとバインド

このリファレンスでは、Azure Functions で Web PubSub イベントを処理する方法について説明します。

Web PubSub は、開発者がリアルタイムの機能と発行-サブスクライブ パターンを使用して Web アプリケーションを簡単に構築するために役立つ Azure マネージド サービスです。

| アクション | Type |
|---------|---------|
| サービスからメッセージを受信したときに関数を実行する | [トリガー バインド](#trigger-binding) |
| ネゴシエーション要求とアップストリーム要求の HTTP トリガーでターゲット オブジェクトに要求をバインドする | [入力バインド](#input-binding)
| サービスを呼び出してアクションを実行する | [出力バインド](#output-binding) |

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
| C# スクリプト、JavaScript、Python、PowerShell       | [拡張機能を明示的にインストールする]、[拡張機能バンドルを使用する] | Visual Studio Code で使用するには [Azure Tools 拡張機能]をお勧めします。 |
| C# スクリプト (Azure portal ではオンラインのみ)         | バインディングを追加する                                   | 関数アプリを再発行せずにポータルで既存のバインディング拡張機能を更新するには、[拡張機能の更新]に関する記事を参照してください。 |

> [!NOTE]
> 指定したパッケージとバージョンの [NuGet](https://www.nuget.org/) からクライアント ライブラリをインストールします。
> 
> ```bash
> func extensions install --package Microsoft.Azure.WebJobs.Extensions.WebPubSub --version 1.0.0
> ```

[NuGet パッケージ]: https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.WebPubSub
[拡張機能バンドルを使用する]: ../azure-functions/functions-bindings-register.md#extension-bundles
[拡張機能を明示的にインストールする]: ../azure-functions/functions-bindings-register.md#explicitly-install-extensions 
[Azure Tools 拡張機能]: https://marketplace.visualstudio.com/items?itemName=ms-vscode.vscode-node-azure-pack
[拡張機能の更新]: ../azure-functions/functions-bindings-register.md

## <a name="key-concepts"></a>主要な概念

![関数アプリを操作する Azure Web PubSub サービスのワークフローを示す図。](./media/reference-functions-bindings/functions-workflow.png)

(1)-(2) クライアント接続を生成する HttpTrigger を使用した `WebPubSubConnection` 入力バインド。

(3)-(4) サービス要求を処理する `WebPubSubTrigger` トリガー バインドまたは HttpTrigger を使用した `WebPubSubContext` 入力バインド。

(5)-(6) サービスに何らかの実行を要求する `WebPubSub` 出力バインド。

## <a name="trigger-binding"></a>トリガー バインド

関数トリガーを使用して、Azure Web PubSub サービスからの要求を処理します。 

`WebPubSubTrigger` は、サービス側からの要求を処理する必要がある場合に使用します。 トリガー エンドポイント パターンは次のようになります。これは、Web PubSub サービス側で設定する必要があります (ポータル: 設定 -> イベント ハンドラー -> URL テンプレート)。 エンドポイント パターンでは、[セキュリティ](../azure-functions/security-concepts.md#system-key)上の理由で、Azure 関数アプリを使用している場合は、クエリ部分 `code=<API_KEY>` が **必須** です。 このキーは **Azure portal** で確認できます。 関数アプリを Azure にデプロイした後に、関数アプリ リソースを見つけて、 **[関数]**  ->  **[アプリ キー]**  ->  **[システム キー]**  ->  **[webpubsub_extension]** の順に移動します。 ただし、ローカル関数を使用する場合、このキーは必要ありません。

```
<Function_App_Url>/runtime/webhooks/webpubsub?code=<API_KEY>
```

:::image type="content" source="media/quickstart-serverless/func-keys.png" alt-text="関数のシステム キーを取得するスクリーンショット。":::

### <a name="example"></a>例


# <a name="c"></a>[C#](#tab/csharp)

```cs
[FunctionName("WebPubSubTrigger")]
public static void Run(
    [WebPubSubTrigger("<hub>", "message", EventType.User)]
    UserEventRequest request,
    WebPubSubConnectionContext context,
    string data,
    WebPubSubDataType dataType)
{
    Console.WriteLine($"Request from: {context.userId}");
    Console.WriteLine($"Request message data: {data}");
    Console.WriteLine($"Request message dataType: {dataType}");
}
```

`WebPubSubTrigger` バインドでは、同期シナリオの戻り値もサポートされています。たとえば、サーバーがクライアント要求を確認して拒否できる場合や、呼び出し元に直接メッセージを送信できる場合の、システム `Connect` イベントやユーザー イベントなどです。 `Connect` イベントでは `ConnectEventResponse` と `EventErrorResponse` が考慮され、ユーザー イベントでは `UserEventResponse` と `EventErrorResponse` が考慮されます。現在のシナリオと一致しない残りの型は無視されます。 さらに、`EventErrorResponse` が返された場合、サービスによってクライアント接続が切断されます。

```cs
[FunctionName("WebPubSubTriggerReturnValue")]
public static MessageResponse Run(
    [WebPubSubTrigger("<hub>", "message", EventType.User)]
    UserEventRequest request,
    ConnectionContext context,
    string data,
    WebPubSubDataType dataType)
{
    return new UserEventResponse
    {
        Data = BinaryData.FromString("ack"),
        DataType = WebPubSubDataType.Text
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
      "name": "data",
      "hub": "<hub>",
      "eventName": "message",
      "eventType": "user"
    }
  ]
}
```

`index.js` で関数を定義します。

```js
module.exports = function (context, data) {
  console.log('Request from: ', context.bindingData.request.connectionContext.userId);
  console.log('Request message data: ', data);
  console.log('Request message dataType: ', context.bindingData.request.dataType);
}
```

`WebPubSubTrigger` バインドでは、同期シナリオの戻り値もサポートされています。たとえば、サーバーがクライアント要求を確認して拒否できる場合や、要求クライアントに直接メッセージを送信できる場合の、システム `Connect` イベントやユーザー イベントなどです。 厳密に型指定されていない JavaScript 言語では、オブジェクト キーに関して逆シリアル化されます。 また `EventErrorResponse` は、残りのオブジェクトと比較して最高の優先順位を持ちます。`code` が戻り値に含まれる場合、それは `EventErrorResponse` に解析され、クライアント接続が切断されます。

```js
module.exports = async function (context) {
  return { 
    "data": "ack",
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
public static void Run([WebPubSubTrigger("<hub>", "<event-Name>", <WebPubSubEventType>)] 
WebPubSubConnectionContext context, ILogger log)
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
| **eventName** | EventName | 必須 - この値は、トリガーされる関数のメッセージのイベントとして設定する必要があります。 </br> `system` イベントの種類の場合、イベント名は `connect`、`connected`、`disconnected` である必要があります。 </br> ユーザー定義のサブプロトコルの場合、イベント名は `message` です。 </br> システムでサポートされているサブプロトコル `json.webpubsub.azure.v1.` の場合、イベント名はユーザー定義のイベント名です。 |
| **connection** | Connection | 省略可能 - アップストリームの Azure Web PubSub サービスを指定するアプリ設定または設定コレクションの名前。 値は署名の検証に使用されます。 また、値は既定でアプリ設定の "WebPubSubConnectionString" を使用して自動解決されます。 `null` は、検証が不要であり、常に成功することを意味します。 |

### <a name="usages"></a>使用法

C# で `WebPubSubEventRequest` は、型が認識されたバインディング パラメーターであり、残りのパラメーターはパラメーター名によってバインドされます。 下の使用可能なパラメーターと型の表を確認してください。

JavaScript のような厳密に型指定されていない言語では、下のマッピング テーブルに関して、`function.json` の `name` を使用してトリガー オブジェクトがバインドされます。 さらに、トリガー入力のバインディング オブジェクトとして `name` が `data` に設定されている場合に、`function.json` の `dataType` が考慮され、それに応じてメッセージが変換されます。 すべてのパラメーターは `context.bindingData.<BindingName>` から読み取ることができ、`JObject` 変換されます。 

| バインディング名 | バインドの種類 | 説明 | プロパティ |
|---------|---------|---------|---------|
|request|`WebPubSubEventRequest`|アップストリーム要求を記述します|派生クラス `ConnectEventRequest`、`ConnectedEventRequest`、`UserEventRequest`、`DisconnectedEventRequest` を含め、イベントの種類によってプロパティは異なります |
|connectionContext|`WebPubSubConnectionContext`|一般的な要求情報| EventType、EventName、Hub、ConnectionId、UserId、Headers、Origin、Signature、States |
|data|`BinaryData`,`string`,`Stream`,`byte[]`| ユーザー `message` イベントでのクライアントからの要求メッセージ データ | -|
|dataType|`WebPubSubDataType`| 要求メッセージのデータ型。`binary`、`text`、`json` がサポートされています | -|
|claims|`IDictionary<string, string[]>`|システム `connect` 要求でのユーザー要求 | -|
|query|`IDictionary<string, string[]>`|システム `connect` 要求でのユーザー クエリ | -|
|subprotocols|`IList<string>`|システム `connect` 要求での使用可能なサブプロトコル | -|
|clientCertificates|`IList<ClientCertificate>`|システム `connect` 要求でのクライアントからの証明書の拇印のリスト|-|
|reason|`string`|システム `disconnected` 要求での理由|-|

### <a name="return-response"></a> 応答を返す

`WebPubSubTrigger` では、`connect` およびユーザー イベントの同期イベントに対して、顧客から返された応答が考慮されます。 一致した応答だけがサービスに返送されます。それ以外の場合は、無視されます。 さらに、`WebPubSubTrigger` 戻りオブジェクトでは、接続のメタデータを管理するために、ユーザによる `SetState()` と `ClearStates()` がサポートされます。 また、拡張機能により、戻り値の結果が要求の `WebPubSubConnectionContext.States` の元の値とマージされます。 既存のキーの値が上書きされ、新しいキーの値が追加されます。

| 戻り値の型 | 説明 | プロパティ |
|---------|---------|---------|
|`ConnectEventResponse`| `connect` イベントの応答 | Groups、Roles、UserId、Subprotocol |
|`UserEventResponse`| ユーザー イベントの応答 | DataType、Data |
|`EventErrorResponse`| 同期イベントのエラー応答 | Code、ErrorMessage |
|`*WebPubSubEventResponse`| 戻り値が不明な場合に使用される、サポートされている基本の応答の種類 | - |

## <a name="input-binding"></a>入力バインド

拡張機能では、さまざまなニーズに対応する 2 つの入力バインドを提供しています。

- `WebPubSubConnection`

  クライアントを Azure Web PubSub サービスに接続させるには、それがサービス エンドポイント URL と有効なアクセス トークンを認識している必要があります。 `WebPubSubConnection` 入力バインディングによって必要な情報が生成されるため、クライアントはこのトークン生成自体を処理する必要がありません。 トークンは時間制限があり、接続に対して特定のユーザーを認証するために使用できるため、トークンをキャッシュしたり、クライアント間で共有したりしないでください。 この入力バインドと連携する HTTP トリガーは、クライアントが接続情報を取得するために使用できます。

- `WebPubSubContext`

  Static Web Apps を使用している場合は、`HttpTrigger` がサポートされている唯一のトリガーであり、Web PubSub のシナリオでは、`WebPubSubContext` 入力バインディングを提供し、ユーザーが Web PubSub プロトコルでサービス側からアップストリームの http 要求を逆シリアル化するのに役立ちます。 顧客は、関数で簡単に処理するために、`WebPubSubTrigger` と比較して同様の結果を得ることができます。 下記の[例](#example---webpubsubcontext)を参照してください。
  `HttpTrigger` と共に使用する場合、顧客は、HttpTrigger で公開されている URL をイベント ハンドラーで適宜構成する必要があります。

### <a name="example---webpubsubconnection"></a>例 - `WebPubSubConnection`

次の例は、入力バインドを使用して Web PubSub 接続情報を取得し、HTTP 経由でそれを返す C# 関数を示しています。 次の例では、`UserId` は、`?userid={User-A}` のようにクライアント要求のクエリ部分を使用して渡されます。

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

### <a name="example---webpubsubcontext"></a>例 - `WebPubSubContext`

次の例は、`connect` イベントの種類で入力バインドを使用して Web PubSub アップストリーム要求情報を取得し、HTTP 経由でそれを返す C# 関数を示しています。

# <a name="c"></a>[C#](#tab/csharp)

```cs
[FunctionName("WebPubSubContextInputBinding")]
public static object Run(
    [HttpTrigger(AuthorizationLevel.Anonymous, "get", "post")] HttpRequest req,
    [WebPubSubContext] WebPubSubContext wpsContext)
{
    if (wpsContext.IsPreflight || !wpsContext.HasError)
    {
        return wpsReq.Response;
    }
    var request = wpsReq.Request as ConnectEventRequest;
    var response = new ConnectEventResponse
    {
        UserId = wpsContext.Request.ConnectionContext.UserId
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
      "type": "webPubSubContext",
      "name": "wpsContext",
      "direction": "in"
    }
  ]
}
```

`index.js` で関数を定義します。

```js
module.exports = async function (context, req, wpsContext) {
  if (!wpsContext.hasError || wpsContext.isPreflight)
  {
    console.log(`invalid request: ${wpsContext.response.message}.`);
    return wpsReq.response;
  }
  console.log(`user: ${wpsContext.connectionContext.userId} is connecting.`);
  return { body: {"userId": wpsContext.connectionContext.userId} };
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
| **hub** | ハブ | 必須 - この値は、トリガーされる関数の Web PubSub ハブの名前に設定する必要があります。 高い優先順位として属性への値の設定をサポートしていますが、またはグローバル値としてアプリ設定に設定することもできます。 |
| **userId** | UserId | 省略可能 - アクセス キー トークンに設定するユーザー識別子要求の値。 |
| **connection** | Connection | 必須 - Web PubSub Service 接続文字列を含むアプリ設定の名前 (既定値は "WebPubSubConnectionString")。 |

#### <a name="webpubsubcontext"></a>WebPubSubContext

次の表は、functions.json ファイルと `WebPubSubContext` 属性に設定したバインド構成のプロパティを説明しています。

| function.json のプロパティ | 属性のプロパティ | 説明 |
|---------|---------|---------|
| **type** | 該当なし | `webPubSubContext` に設定する必要があります。 |
| **direction** | 該当なし | `in` に設定する必要があります。 |
| **name** | 該当なし | 入力 Web PubSub 要求の関数コードで使用される変数名。 |
| **connection** | Connection | 省略可能 - アップストリームの Azure Web PubSub サービスを指定するアプリ設定または設定コレクションの名前。 値は[不正使用防止](https://github.com/cloudevents/spec/blob/v1.0.1/http-webhook.md#4-abuse-protection)と署名検証に使用されます。 値は既定で "WebPubSubConnectionString" を使用して自動解決されます。 `null` は、検証が不要であり、常に成功することを意味します。 |

### <a name="usage"></a>使用法

#### <a name="webpubsubconnection"></a>WebPubSubConnection

# <a name="c"></a>[C#](#tab/csharp)

`WebPubSubConnection` では以下のプロパティが提供されます。

| バインディング名 | バインドの種類 | 説明 |
|---------|---------|---------|
| BaseUri | Uri | Web PubSub クライアント接続 URI。 |
| Uri | Uri | Web PubSub 接続の絶対 URI。要求に基づいて生成された `AccessToken` が含まれます。 |
| AccessToken | string | 要求の UserId とサービス情報に基づいて生成された `AccessToken`。 |

# <a name="javascript"></a>[JavaScript](#tab/javascript)

`WebPubSubConnection` では以下のプロパティが提供されます。

| バインディング名 | 説明 |
|---------|---------|
| baseUrl | Web PubSub クライアント接続 URI。 |
| url | Web PubSub 接続の絶対 URI。要求に基づいて生成された `AccessToken` が含まれます。 |
| accessToken | 要求の UserId とサービス情報に基づいて生成された `AccessToken`。 |

---

#### <a name="webpubsubcontext"></a>WebPubSubContext

`WebPubSubContext` では以下のプロパティが提供されます。

| バインディング名 | バインドの種類 | 説明 | プロパティ |
|---------|---------|---------|---------|
| request | `WebPubSubEventRequest` | クライアントからの要求。詳細については、下の表を参照してください。 | 要求ヘッダーの `WebPubSubConnectionContext` と要求本文から逆シリアル化された他のプロパティでは要求を説明します (`DisconnectedEventRequest` の `Reason` など)。 |
| 応答 | `HttpResponseMessage` | 拡張機能によって、主に `AbuseProtection` とエラー ケースに対する応答が作成されます。 | - |
| errorMessage | string | アップストリーム要求を処理するときのエラーの詳細を説明します。 | - |
| hasError | [bool] | 有効な Web PubSub アップストリーム要求かどうかを示すフラグ。 | - |
| isPreflight | [bool] | `AbuseProtection` のプレフライト要求かどうかを示すフラグ。 | - |

`WebPubSubEventRequest` の場合、要求シナリオに関するさまざまな情報を提供するさまざまなクラスに逆シリアル化されます。 `PreflightRequest` または無効なケースの場合、ユーザーは `IsPreflight` および `HasError` フラグを調べて確認できます。 システムによって作成された応答 `WebPubSubContext.Response` を直接返すことが推奨されます。または、顧客が必要に応じてエラーをログに記録することもできます。 さまざまなシナリオで、顧客は次のように要求のプロパティを読み取ることができます。

| 派生クラス | 説明 | プロパティ |
| -- | -- | -- |
| `PreflightRequest` | `IsPreflight` が **true** の場合に `AbuseProtection` で使用します | - |
| `ConnectEventRequest` | システム `Connect` イベントの種類で使用します | Claims、Query、Subprotocols、ClientCertificates |
| `ConnectedEventRequest` | システム `Connected` イベントの種類で使用します | - |
| `UserEventRequest` | ユーザー イベントの種類で使用します | Data、DataType |
| `DisconnectedEventRequest` | システム `Disconnected` イベントの種類で使用します | 理由 |

> [!NOTE]
> `WebPubSubContext` は、`HttpTrigger` で `WebPubSubTrigger`と同様に要求を逆シリアル化できる入力バインドですが、マージ後の接続状態がサポートされていないなどの制限があります。 返される応答はサービス側で引き続き考慮されますが、ユーザーは自分で応答を作成する必要があります。 ユーザーがイベント応答を設定する必要がある場合は、`ConnectEventResponse` またはユーザー イベントのメッセージを含む `HttpResponseMessage` を **応答本文** として返し、`ce-connectionstate` キーを含む接続状態を **応答ヘッダー** に配置する必要があります。

## <a name="output-binding"></a>出力バインド

Web PubSub 出力バインドを使用して Azure Web PubSub サービスを呼び出し、なんらかの処理を実行します。 次の宛先にメッセージをブロードキャストできます。

* 接続されているすべてのクライアント
* 特定のユーザーに対して認証された接続されているクライアント
* 特定のグループに参加している接続されているクライアント
* 特定のクライアント接続

出力バインドを使用して、クライアントとグループを管理し、特定の connectionId を対象とするアクセス許可をグループに付与したり、取り消したりすることもできます。

* グループに接続を追加する
* ユーザーをグループに追加する
* グループから接続を削除する
* グループからユーザーを削除する
* ユーザーをすべてのグループから削除する
* すべてのクライアント接続を閉じる
* 特定のクライアント接続を閉じる
* グループ内の接続を閉じる
* 接続のアクセス許可を付与する
* 接続のアクセス許可を取り消す

セットアップと構成の詳細については、概要を参照してください。

### <a name="example"></a>例

# <a name="c"></a>[C#](#tab/csharp)

```cs
[FunctionName("WebPubSubOutputBinding")]
public static async Task RunAsync(
    [HttpTrigger(AuthorizationLevel.Anonymous, "get", "post")] HttpRequest req,
    [WebPubSub(Hub = "<hub>")] IAsyncCollector<WebPubSubAction> actions)
{
    await actions.AddAsync(WebPubSubAction.CreateSendToAllAction("Hello Web PubSub!", WebPubSubDataType.Text));
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
      "name": "actions",
      "hub": "<hub>",
      "direction": "out"
    }
  ]
}
```

`index.js` で関数を定義します。

```js
module.exports = async function (context) {
  context.bindings.actions = {
    "actionName": "sendToAll",
    "data": "hello",
    "dataType": "text"
  };
  context.done();
}
```

---

### <a name="webpubsubaction"></a>WebPubSubAction 

`WebPubSubAction` は、出力バインドの基本抽象型です。 派生型は、サーバーがサービスに呼び出させるアクションを表します。 

# <a name="c"></a>[C#](#tab/csharp)

C# 言語では、使用可能なアクションを検出できるように、`WebPubSubAction` に静的メソッドがいくつか用意されています。 たとえば、ユーザーは `WebPubSubAction.CreateSendToAllAction()` を呼び出して `SendToAllAction` を作成できます。

| 派生クラス | プロパティ |
| -- | -- |
| `SendToAllAction`|Data、DataType、Excluded |
| `SendToGroupAction`|Group、Data、DataType、Excluded |
| `SendToUserAction`|UserId、Data、DataType |
| `SendToConnectionAction`|ConnectionId、Data、DataType |
| `AddUserToGroupAction`|UserId、Group |
| `RemoveUserFromGroupAction`|UserId、Group |
| `RemoveUserFromAllGroupsAction`|UserId |
| `AddConnectionToGroupAction`|ConnectionId、Group |
| `RemoveConnectionFromGroupAction`|ConnectionId、Group |
| `CloseAllConnectionsAction`|Excluded、Reason |
| `CloseClientConnectionAction`|ConnectionId、Reason |
| `CloseGroupConnectionsAction`|Group、Excluded、Reason |
| `GrantPermissionAction`|ConnectionId、Permission、TargetName |
| `RevokePermissionAction`|ConnectionId、Permission、TargetName |

# <a name="javascript"></a>[JavaScript](#tab/javascript)

`javascript` のような厳密に型指定されていない言語では、 **`actionName`** は型を解決するためのキー パラメーターです。使用可能なアクションを次に示します。

| ActionName | プロパティ |
| -- | -- |
| `SendToAll`|Data、DataType、Excluded |
| `SendToGroup`|Group、Data、DataType、Excluded |
| `SendToUser`|UserId、Data、DataType |
| `SendToConnection`|ConnectionId、Data、DataType |
| `AddUserToGroup`|UserId、Group |
| `RemoveUserFromGroup`|UserId、Group |
| `RemoveUserFromAllGroups`|UserId |
| `AddConnectionToGroup`|ConnectionId、Group |
| `RemoveConnectionFromGroup`|ConnectionId、Group |
| `CloseAllConnections`|Excluded、Reason |
| `CloseClientConnection`|ConnectionId、Reason |
| `CloseGroupConnections`|Group、Excluded、Reason |
| `GrantPermission`|ConnectionId、Permission、TargetName |
| `RevokePermission`|ConnectionId、Permission、TargetName |

> [!IMPORTANT]
> データ変換のあいまいさを避けるために、データ型が `json` または `text` に設定されている場合、メッセージの送信に関連するアクションのメッセージ データ プロパティは `string` である必要があります。 `JSON.stringify()` を使用して、必要な JSON オブジェクトを変換してください。 これは、`WebPubSubTrigger` と連携する `UserEventResponse.Data` など、メッセージ プロパティを使用するあらゆる場所に適用されます。 
> 
> データ型が `binary` に設定されている場合は、`function.json` で `binary` として構成された `dataType` が自然にサポートされるバインドを利用できます。詳細については、「[トリガーとバインドの定義](../azure-functions/functions-triggers-bindings.md?tabs=csharp#trigger-and-binding-definitions)」を参照してください。

---

### <a name="configuration"></a>構成

#### <a name="webpubsub"></a>WebPubSub

次の表は、function.json ファイルと `WebPubSub` 属性で設定したバインド構成のプロパティを説明しています。

| function.json のプロパティ | 属性のプロパティ | 説明 |
|---------|---------|---------|
| **type** | 該当なし | `webPubSub` に設定されている必要があります。 |
| **direction** | 該当なし | `out` に設定されている必要があります。 |
| **name** | 該当なし | 出力バインド オブジェクトの関数コードで使用される変数名。 |
| **hub** | ハブ | この値は、トリガーされる関数の Web PubSub ハブの名前に設定する必要があります。 高い優先順位として属性への値の設定をサポートしていますが、またはグローバル値としてアプリ設定に設定することもできます。 |
| **connection** | Connection | Web PubSub Service 接続文字列を含むアプリ設定の名前 (既定値は "WebPubSubConnectionString")。 |

## <a name="troubleshooting"></a>トラブルシューティング

### <a name="setting-up-console-logging"></a>コンソール ログの設定
また、サービスに対して行う要求の詳細を確認する場合は、[コンソールのログ記録を簡単に有効にする](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/core/Azure.Core/samples/Diagnostics.md#logging)こともできます。

[azure_sub]: https://azure.microsoft.com/free/
[samples_ref]: https://github.com/Azure/azure-webpubsub/tree/main/samples/functions

## <a name="next-steps"></a>次のステップ

[!INCLUDE [next step](includes/include-next-step.md)]