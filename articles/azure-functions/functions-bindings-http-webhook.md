<properties
	pageTitle="Azure Functions における HTTP と Webhook のバインド | Microsoft Azure"
	description="Azure Functions で HTTP トリガー、Webhook トリガー、バインドを使用する方法について説明します。"
	services="functions"
	documentationCenter="na"
	authors="christopheranderson"
	manager="erikre"
	editor=""
	tags=""
	keywords="Azure Functions, 機能, イベント処理, Webhook, 動的コンピューティング, サーバーなしのアーキテクチャ"/>

<tags
	ms.service="functions"
	ms.devlang="multiple"
	ms.topic="reference"
	ms.tgt_pltfrm="multiple"
	ms.workload="na"
	ms.date="08/22/2016"
	ms.author="chrande"/>

# Azure Functions における HTTP と Webhook のバインド

[AZURE.INCLUDE [functions-selector-bindings](../../includes/functions-selector-bindings.md)]

この記事では、HTTP トリガー、Webhook トリガー、バインディングの構成とコーディングを Azure Functions で行う方法について説明します。

[AZURE.INCLUDE [intro](../../includes/functions-bindings-intro.md)]

## HTTP と Webhook のバインドに使用する function.json

*function.json* ファイルでは、要求と応答の両方に関連するプロパティを指定します。

HTTP 要求のプロパティ:

- `name`: オブジェクトの関数コードで使用される変数名 (または Node.js 関数の要求本文)。
- `type`: *httpTrigger* に設定する必要があります。
- `direction`: *in* に設定する必要があります。
- `webHookType`: WebHook トリガーの場合、有効な値は *github*、*slack*、*genericJson* です。WebHook ではない HTTP トリガーの場合、このプロパティを空の文字列に設定します。WebHook の詳細については、以下の「[WebHook トリガー](#webhook-triggers)」を参照してください。
- `authLevel`: WebHook トリガーには適用されません。API キーを要求するには "function" に、API の主な要件を破棄するには "anonymous" に、マスター API キーを要求するには "admin" に設定します。詳しくは、以下の「[API キー](#apikeys)」を参照してください。

HTTP 応答のプロパティ:

- `name`: response オブジェクトの関数コードで使用される変数名。
- `type`: *http* に設定する必要があります。
- `direction`: *out* に設定する必要があります。
 
*function.json* の例:

```json
{
  "bindings": [
    {
      "webHookType": "",
      "name": "req",
      "type": "httpTrigger",
      "direction": "in",
      "authLevel": "function"
    },
    {
      "name": "res",
      "type": "http",
      "direction": "out"
    }
  ],
  "disabled": false
}
```

## WebHook トリガー

WebHook トリガーとは、Webhook 用に設計された次の機能を備えた HTTP トリガーを指します。

* 特定の WebHook プロバイダー (現在 GitHub と Slack がサポートされています) について、Functions ランタイムによりプロバイダーの署名が検証されます。
* Node.js 関数の場合、Functions ランタイムは request オブジェクトではなく、要求本文を提供します。C# 関数では、何が提供されるかは、パラメーターの種類を指定することで管理されるため、特別な処理は存在しません。`HttpRequestMessage` を指定すると、request オブジェクトが取得されます。POCO 型を指定すると、Functions ランタイムにより要求の本文で JSON オブジェクトの解析が試行され、オブジェクトのプロパティが入力されます。
* WebHook 関数をトリガーするには、HTTP 要求に API キーを含める必要があります。WebHook 以外の HTTP トリガーの場合、この要件は省略できます。

GitHub WebHook を設定する方法の詳細については、[GitHub Developer の WebHook の作成](http://go.microsoft.com/fwlink/?LinkID=761099&clcid=0x409)に関するページを参照してください。

## 関数をトリガーする URL

関数をトリガーするには、Function App の URL と関数名を組み合わせた URL に HTTP 要求を送信します。

```
 https://{function app name}.azurewebsites.net/api/{function name} 
```

## API キー

既定では、API キーを HTTP 要求に含めて、HTTP または WebHook 機能をトリガーします。キーは、`code` という名前のクエリ文字列の変数に含めることも、`x-functions-key` HTTP ヘッダーに含めることもできます。WebHook 以外の関数では、*function.json* ファイルで `authLevel` プロパティを "anonymous" に設定することで、API キーが不要であることを示せます。

API キーは関数アプリのファイル システムの *D:\\home\\data\\Functions\\secrets* フォルダーにあります。マスター キーと関数キーは、この例で示されているように *host.json* ファイルで設定します。

```json
{
  "masterKey": "K6P2VxK6P2VxK6P2VxmuefWzd4ljqeOOZWpgDdHW269P2hb7OSJbDg==",
  "functionKey": "OBmXvc2K6P2VxK6P2VxK6P2VxVvCdB89gChyHbzwTS/YYGWWndAbmA=="
}
```

*host.json* の関数キーを使用して関数をトリガーできますが、無効な関数はトリガーされません。マスター キーは任意の関数のトリガーに使用でき、無効になっている関数もトリガーします。マスター キーを要求するように関数を構成するには、`authLevel` プロパティを "admin" に設定します。

*secrets* フォルダーに関数と同じ名前の JSON ファイルが含まれる場合、そのファイルの `key` プロパティを使用して関数をトリガーできます。このキーは、それが参照する関数でのみ機能します。たとえば、`HttpTrigger` という名前の関数の API キーは、*secrets* フォルダー内の *HttpTrigger.json* に指定します。たとえば次のようになります。

```json
{
  "key":"0t04nmo37hmoir2rwk16skyb9xsug32pdo75oce9r4kg9zfrn93wn4cx0sxo4af0kdcz69a4i"
}
```

> [AZURE.NOTE] WebHook のトリガーを設定するときに、マスター キーを WebHook プロバイダーと共有しないでください。WebHook を処理する関数でのみ機能するキーを使用します。マスター キーを使用すると、無効になっている関数も含め、すべての関数をトリガーできます。

## HTTP トリガー関数の C# コードの例 

コード例では、クエリ文字列または HTTP 要求の本文で `name` パラメーターを探します。

```csharp
using System.Net;
using System.Threading.Tasks;

public static async Task<HttpResponseMessage> Run(HttpRequestMessage req, TraceWriter log)
{
    log.Info($"C# HTTP trigger function processed a request. RequestUri={req.RequestUri}");

    // parse query parameter
    string name = req.GetQueryNameValuePairs()
        .FirstOrDefault(q => string.Compare(q.Key, "name", true) == 0)
        .Value;

    // Get request body
    dynamic data = await req.Content.ReadAsAsync<object>();

    // Set name to query string or body data
    name = name ?? data?.name;

    return name == null
        ? req.CreateResponse(HttpStatusCode.BadRequest, "Please pass a name on the query string or in the request body")
        : req.CreateResponse(HttpStatusCode.OK, "Hello " + name);
}
```

## HTTP トリガー関数の F# コードの例

コード例では、クエリ文字列または HTTP 要求の本文で `name` パラメーターを探します。

```fsharp
open System.Net
open System.Net.Http
open FSharp.Interop.Dynamic

let Run(req: HttpRequestMessage) =
    async {
        let q =
            req.GetQueryNameValuePairs()
                |> Seq.tryFind (fun kv -> kv.Key = "name")
        match q with
        | Some kv ->
            return req.CreateResponse(HttpStatusCode.OK, "Hello " + kv.Value)
        | None ->
            let! data = Async.AwaitTask(req.Content.ReadAsAsync<obj>())
            try
                return req.CreateResponse(HttpStatusCode.OK, "Hello " + data?name)
            with e ->
                return req.CreateErrorResponse(HttpStatusCode.BadRequest, "Please pass a name on the query string or in the request body")
    } |> Async.StartAsTask
```

次のように、NuGet を使用して `FSharp.Interop.Dynamic` および `Dynamitey` アセンブリを参照する `project.json` ファイルが必要です。

```json
{
  "frameworks": {
    "net46": {
      "dependencies": {
        "Dynamitey": "1.0.2",
        "FSharp.Interop.Dynamic": "3.0.0"
      }
    }
  }
}
```

このファイルは NuGet を使用して依存関係を取得し、それをスクリプト内で参照します。

## HTTP トリガー関数の Node.js コードの例 

次のコード例では、クエリ文字列または HTTP 要求の本文で `name` パラメーターを探します。

```javascript
module.exports = function(context, req) {
    context.log('Node.js HTTP trigger function processed a request. RequestUri=%s', req.originalUrl);

    if (req.query.name || (req.body && req.body.name)) {
        context.res = {
            // status: 200, /* Defaults to 200 */
            body: "Hello " + (req.query.name || req.body.name)
        };
    }
    else {
        context.res = {
            status: 400,
            body: "Please pass a name on the query string or in the request body"
        };
    }
    context.done();
};
```

## GitHub WebHook 関数の C# コードの例 

次のコード例では、GitHub の問題に対するコメントをログに記録します。

```csharp
#r "Newtonsoft.Json"

using System;
using System.Net;
using System.Threading.Tasks;
using Newtonsoft.Json;

public static async Task<object> Run(HttpRequestMessage req, TraceWriter log)
{
    string jsonContent = await req.Content.ReadAsStringAsync();
    dynamic data = JsonConvert.DeserializeObject(jsonContent);

    log.Info($"WebHook was triggered! Comment: {data.comment.body}");

    return req.CreateResponse(HttpStatusCode.OK, new {
        body = $"New GitHub comment: {data.comment.body}"
    });
}
```

## GitHub WebHook 関数の F# コードの例

次のコード例では、GitHub の問題に対するコメントをログに記録します。

```fsharp
open System.Net
open System.Net.Http
open FSharp.Interop.Dynamic
open Newtonsoft.Json

type Response = {
    body: string
}

let Run(req: HttpRequestMessage, log: TraceWriter) =
    async {
        let! content = req.Content.ReadAsStringAsync() |> Async.AwaitTask
        let data = content |> JsonConvert.DeserializeObject
        log.Info(sprintf "GitHub WebHook triggered! %s" data?comment?body)
        return req.CreateResponse(
            HttpStatusCode.OK,
            { body = sprintf "New GitHub comment: %s" data?comment?body })
    } |> Async.StartAsTask
```

## GitHub WebHook 関数の Node.js コードの例 

次のコード例では、GitHub の問題に対するコメントをログに記録します。

```javascript
module.exports = function (context, data) {
    context.log('GitHub WebHook triggered!', data.comment.body);
    context.res = { body: 'New GitHub comment: ' + data.comment.body };
    context.done();
};
```

## 次のステップ

[AZURE.INCLUDE [次のステップ](../../includes/functions-bindings-next-steps.md)]

<!---HONumber=AcomDC_0921_2016-->