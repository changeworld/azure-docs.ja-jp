<properties
	pageTitle="Azure Functions triggers and bindings (Azure Functions のトリガーとバインド) | Microsoft Azure"
	description="Azure Functions でトリガーとバインドを使用する方法を説明します。"
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
	ms.date="04/07/2016"
	ms.author="chrande"/>

# Azure Functions のトリガーとバインドの開発者用リファレンス

この記事では、Azure Functions のトリガーとバインドを構成およびコード化する方法について説明します。これらのバインドのほとんどは Azure ポータルの**統合** UI で簡単に管理できますが、ポータルでは各バインドの一部の機能およびオプションしか解説されていません。

この記事では、「[Azure Functions developer reference (Azure Functions 開発者向けリファレンス)](functions-reference.md)」と [C#](functions-reference-csharp.md) または [ノード](functions-reference-node.md)の開発者向けリファレンスを既に読んでいることを前提としています。

## HTTP と WebHook のトリガーとバインド

HTTP または WebHook トリガーを使用して、HTTP 要求に応じて関数を呼び出すことができます。要求には、現在 Azure ポータル UI でのみ使用できる API キーが含まれる必要があります。

関数の URL は、関数アプリの URL と関数名を組み合わせたものになります。

```
 https://{function app name}.azurewebsites.net/api/{function name} 
```

*function.json* ファイルは、HTTP 要求と応答に関連するプロパティを提供します。

HTTP 要求のプロパティ:

- `name` : オブジェクトの関数コードで使用される変数名 (または Node.js 関数の要求本文)。
- `type` : *httpTrigger* に設定する必要があります。
- `direction` : *in* に設定する必要があります。 
- `webHookType` : WebHook トリガーの場合、有効な値は *github*、*slack*、および *genericJson* です。WebHook ではない HTTP トリガーの場合、このプロパティを空の文字列に設定します。WebHook の詳細については、以降の「[WebHook トリガー](#webhook-triggers)」を参照してください。
- `authLevel` : WebHook トリガーには適用されません。API キーを要求するには "function" に、API の主な要件を破棄するには "anonymous" に、マスター API キーを要求するには "admin" に設定します。詳しくは、以下の「[API キー](#apikeys)」を参照してください。

HTTP 応答のプロパティ:

- `name` : response オブジェクトの関数コードで使用される変数名。
- `type` : *http* に設定する必要があります。
- `direction` : *out* に設定する必要があります。 
 
*function.json* の例:

```json
{
  "bindings": [
    {
      "webHookType": "",
      "name": "req",
      "type": "httpTrigger",
      "direction": "in"
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

### WebHook トリガー

WebHook トリガーとは、Webhook 用に設計された次の機能を備えた HTTP トリガーを指します。

* 特定の WebHook プロバイダー (現在 GitHub と Slack がサポートされています) について、Functions ランタイムによりプロバイダーの署名が検証されます。
* Node.js 関数の場合、Functions ランタイムは request オブジェクトではなく、要求本文を提供します。C# 関数では、何が提供されるかは、パラメーターの種類を指定することで管理されるため、特別な処理は存在しません。`HttpRequestMessage` を指定すると、request オブジェクトが取得されます。POCO 型を指定すると、Functions ランタイムにより要求の本文で JSON オブジェクトの解析が試行され、オブジェクトのプロパティが入力されます。
* WebHook 関数をトリガーするには、HTTP 要求に API キーを含める必要があります。WebHook 以外の HTTP トリガーの場合、この要件は省略できます。

GitHub WebHook を設定する方法の詳細については、「[GitHub Developer - Creating WebHooks](http://go.microsoft.com/fwlink/?LinkID=761099&clcid=0x409)」 (GitHub 開発者 - WebHook の作成) を参照してください。

### API キー

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

### HTTP トリガー関数の C# コードの例 

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

### HTTP トリガー関数の Node.js コードの例 

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

### GitHub WebHook 関数の C# コードの例 

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

### GitHub WebHook 関数の Node.js コードの例 

次のコード例では、GitHub の問題に対するコメントをログに記録します。

```javascript
module.exports = function (context, data) {
    context.log('GitHub WebHook triggered!', data.comment.body);
    context.res = { body: 'New GitHub comment: ' + data.comment.body };
    context.done();
};
```

## タイマー トリガー

*Function.json* ファイルには、スケジュール式と、関数をすぐにトリガーする必要があるかどうかを示すスイッチが含まれています。

```json
{
  "bindings": [
    {
      "schedule": "0 * * * * *",
      "runOnStartup": true,
      "name": "myTimer",
      "type": "timerTrigger",
      "direction": "in"
    }
  ],
  "disabled": false
}
```

タイマー トリガーは、マルチインスタンスのスケールアウトを自動的に処理します。すべてのインスタンスで特定のタイマー関数の 1 つのインタンスだけが実行されます。

### スケジュール式の形式

スケジュール式には、{second} {minute} {hour} {day} {month} {day of the week} の 6 つのフィールドを含む [CRON 式](http://en.wikipedia.org/wiki/Cron#CRON_expression)を指定できます。オンラインで見つかる CRON 式に関するドキュメントの多くは、{second} フィールドが省略されているため、これらのいずれかからコピーする場合は、この追加フィールドを調整する必要があります。

スケジュール式では、*hh:mm:ss* 形式を使用して、関数のトリガー間隔の遅延時間を指定することもできます。

スケジュール式の例を以下に示します。

5 分に 1 回トリガーするには、次のように指定します。

```json
"schedule": "0 */5 * * * *",
"runOnStartup": false,
```

すぐにトリガーし、その後は 2 時間ごとにトリガーするには、次のように指定します。

```json
"schedule": "0 0 */2 * * *",
"runOnStartup": true,
```

15 秒ごとにトリガーするには、次のように指定します。

```json
"schedule": "00:00:15",
"runOnStartup": false,
```

### タイマー トリガーの C# コードの例

この C# コードの例では、関数がトリガーされるたびにログを 1 つだけ書き込みます。

```csharp
public static void Run(TimerInfo myTimer, TraceWriter log)
{
    log.Info($"C# Timer trigger function executed at: {DateTime.Now}");    
}
```

## Azure Storage (キュー、BLOB、テーブル) のトリガーとバインド

このセクションには、次のようにいくつかのサブセクションがあります。

* [Azure Storage キュー トリガー](#storagequeuetrigger)
* [Azure Storage キュー出力バインド](#storagequeueoutput)
* [Azure Storage BLOB トリガー](#storageblobtrigger)
* [Azure Storage BLOB 入力および出力バインド](#storageblobbindings)
* [Azure Storage テーブル入力および出力バインド](#storagetablesbindings)

### <a id="storagequeuetrigger"></a>Azure Storage キュー トリガー

ストレージ キュー トリガーの *function.json* ファイルでは、次のプロパティを指定します。

- `name` : キューまたはキュー メッセージの関数コードで使用される変数名。 
- `queueName`: ポーリングするキューの名前。キューの名前付け規則については、「[キューおよびメタデータの名前付け](https://msdn.microsoft.com/library/dd179349.aspx)」を参照してください。
- `connection` : ストレージ接続文字列を含むアプリ設定の名前。`connection` を空のままにすると、トリガーは AzureWebJobsStorage アプリ設定で指定される、関数アプリの既定のストレージ接続文字列で動作します。
- `type` : *queueTrigger* に設定する必要があります。
- `direction` : *in* に設定する必要があります。 

#### ストレージ キュー トリガーの *Function.json* の例

```json
{
    "disabled": false,
    "bindings": [
        {
            "name": "myQueueItem",
            "queueName": "myqueue-items",
            "connection":"",
            "type": "queueTrigger",
            "direction": "in"
        }
    ]
}
```

#### キュー トリガーにサポートされた型

キュー メッセージを、次のいずれかの型に逆シリアル化できます。

* オブジェクト (JSON)
* String
* Byte array 
* `CloudQueueMessage` (C#) 

#### キュー トリガー メタデータ

関数にキュー メタデータを取得するには、次の変数名を使用します。

* expirationTime
* insertionTime
* nextVisibleTime
* id
* popReceipt
* dequeueCount
* queueTrigger (文字列としてキュー メッセージ テキストを取得する別の方法)

この C# コードの例では、ログ キュー メタデータを取得し、記録します。

```csharp
public static void Run(string myQueueItem, 
    DateTimeOffset expirationTime, 
    DateTimeOffset insertionTime, 
    DateTimeOffset nextVisibleTime,
    string queueTrigger,
    string id,
    string popReceipt,
    int dequeueCount,
    TraceWriter log)
{
    log.Info($"C# Queue trigger function processed: {myQueueItem}\n" +
        $"queueTrigger={queueTrigger}\n" +
        $"expirationTime={expirationTime}\n" +
        $"insertionTime={insertionTime}\n" +
        $"nextVisibleTime={nextVisibleTime}\n" +
        $"id={id}\n" +
        $"popReceipt={popReceipt}\n" + 
        $"dequeueCount={dequeueCount}");
}
```

#### 有害キュー メッセージの処理

関数の失敗を引き起こす内容を含むメッセージは*有害メッセージ*と呼ばれます。関数が失敗してもキュー メッセージは削除されず、最終的には回収されて、このサイクルを繰り返します。SDK では一定数繰り返し送信されると自動的にそのサイクルを中断します。また手動でも処理できます。

SDKでは、キュー メッセージを処理する関数を最大 5 回呼び出します。5 回目の実行に失敗した場合、メッセージは有害キューに移動されます。

有害キューには *{originalqueuename}*-poison という名前が付けられます。メッセージのログを取得するか、手動での対処が必要であるという通知を送信することにより有害キューからのメッセージを処理する関数が記述できます。

有害メッセージを手動で処理する場合は、処理のためにメッセージが取得された回数を、`dequeueCount` を確認して取得できます。

### <a id="storagequeueoutput"></a> Azure Storage キュー出力バインド

ストレージ キュー出力バインドの *function.json* ファイルでは、次のプロパティを指定します。

- `name` : キューまたはキュー メッセージの関数コードで使用される変数名。 
- `queueName` : キューの名前。キューの名前付け規則については、「[キューおよびメタデータの名前付け](https://msdn.microsoft.com/library/dd179349.aspx)」を参照してください。
- `connection` : ストレージ接続文字列を含むアプリ設定の名前。`connection` を空のままにすると、トリガーは AzureWebJobsStorage アプリ設定で指定される、関数アプリの既定のストレージ接続文字列で動作します。
- `type` : *queue* に設定する必要があります。
- `direction` : *out* に設定する必要があります。 

#### ストレージ キュー出力バインドの *Function.json* の例

この例ではキュー トリガーを使用し、キュー メッセージを書き込みます。

```json
{
  "bindings": [
    {
      "queueName": "myqueue-items",
      "connection": "MyStorageConnection",
      "name": "myQueueItem",
      "type": "queueTrigger",
      "direction": "in"
    },
    {
      "name": "myQueue",
      "queueName": "samples-workitems-out",
      "connection": "MyStorageConnection",
      "type": "queue",
      "direction": "out"
    }
  ],
  "disabled": false
}
``` 

#### キュー出力バインドにサポートされた型

`queue` バインドは、次の型をキュー メッセージにシリアル化できます。

* オブジェクト (C# の `out T` で、関数が終了したときに、パラメーターが null である場合は、null オブジェクトでメッセージを作成します)
* String (C# の `out string` で、関数が終了したときに、パラメーター値が null でない場合は、キュー メッセージを作成します)
* Byte array (C# の `out byte[]` で、string と同様に動作) 
* `out CloudQueueMessage` (C#、string と同様に動作) 

また、C# では、`T` がいずれかのサポートされている型である場合、`ICollector<T>` または `IAsyncCollector<T>` にバインドすることもできます。

#### キュー出力バインドのコード例

この C# コード例では、入力キュー メッセージごとに 1 つの出力キュー メッセージを書き込みます。

```csharp
public static void Run(string myQueueItem, out string myOutputQueueItem, TraceWriter log)
{
    myOutputQueueItem = myQueueItem + "(next step)";
}
```

この C# コード例では、`ICollector<T>` を使用して複数のメッセージを書き込みます (非同期関数で `IAsyncCollector<T>` を使用)。

```csharp
public static void Run(string myQueueItem, ICollector<string> myQueue, TraceWriter log)
{
    myQueue.Add(myQueueItem + "(step 1)");
    myQueue.Add(myQueueItem + "(step 2)");
}
```

### <a id="storageblobtrigger"></a>Azure Storage BLOB トリガー

ストレージ BLOB トリガーの *function.json* ファイルでは、次のプロパティを指定します。

- `name` : BLOB の関数コードで使用される変数名。 
- `path` : 監視するコンテナーを指定するパスと、必要に応じて BLOB 名のパターンを指定します。
- `connection` : ストレージ接続文字列を含むアプリ設定の名前。`connection` を空のままにすると、トリガーは AzureWebJobsStorage アプリ設定で指定される、関数アプリの既定のストレージ接続文字列で動作します。
- `type` : *blobTrigger* に設定する必要があります。
- `direction` : *in* に設定する必要があります。

#### ストレージ BLOB トリガーの *Function.json* の例

この例は、samples-workitems コンテナーに追加されるすべての BLOB でトリガーされます。

```json
{
    "disabled": false,
    "bindings": [
        {
            "name": "myBlob",
            "type": "blobTrigger",
            "direction": "in",
            "path": "samples-workitems",
            "connection":""
        }
    ]
}
```

#### BLOB トリガーにサポートされた型

ノードまたは C# の関数では、BLOB は次の型のいずれかに逆シリアル化することができます。

* オブジェクト (JSON)
* String

C# 関数の場合は、次の型のいずれかにもバインドできます。

* `TextReader`
* `Stream`
* `ICloudBlob`
* `CloudBlockBlob`
* `CloudPageBlob`
* `CloudBlobContainer`
* `CloudBlobDirectory`
* `IEnumerable<CloudBlockBlob>`
* `IEnumerable<CloudPageBlob>`
* [ICloudBlobStreamBinder](../app-service-web/websites-dotnet-webjobs-sdk-storage-blobs-how-to.md#icbsb) によって逆シリアル化されるその他の種類 

#### BLOB トリガーの C# コードの例

この C# コード例では、監視対象のコンテナーに追加される各 BLOB の内容を記録します。

```csharp
public static void Run(string myBlob, TraceWriter log)
{
    log.Info($"C# Blob trigger function processed: {myBlob}");
}
```

#### BLOB トリガー名のパターン

BLOB 名のパターンは `path` プロパティに指定できます。次に例を示します。

```json
"path": "input/original-{name}",
```

このパスは、*入力*コンテナーの *original-Blob1.txt* という名前の BLOB を探し、関数コード内の `name` 変数の値は `Blob1` になります。

別の例:

```json
"path": "input/{blobname}.{blobextension}",
```

このパスは、*original-Blob1.txt* という名前の BLOB を探し、関数コード内の `blobname` および `blobextension` 変数の値は *original-Blob1* および *txt* になります。

関数をトリガーする BLOB の型を制限するには、ファイル拡張子の値が固定されたパターンを指定します。`path` を *samples/{name}.png* に設定した場合、*samples* コンテナー内の *.png* BLOB のみが関数をトリガーします。

名前に波括弧がある BLOB 名に新しいパターンを指定する必要がある場合は、波括弧を二重にします。たとえば、以下のような名前を持つ *イメージ*コンテナーに blob を見つける場合は、

		{20140101}-soundfile.mp3

`path` プロパティには以下を使用します。

		images/{{20140101}}-{name}

この例では、`name` 変数の値は、*soundfile.mp3* になります。

#### BLOB の配信確認メッセージ

Azure Functions ランタイムでは、BLOB トリガー関数は、同一の新規または更新された BLOB について 2 回以上呼び出されることはありません。これは*BLOB の配信確認メッセージ*を維持して、特定の BLOB バージョンが処理されているかどうかを判断するためです。

BLOB の配信確認メッセージは、AzureWebJobsStorage 接続文字列が指定した Azure ストレージ アカウントの *azure-webjobs-hosts* という名前のコンテナーに格納されています。BLOB の配信確認メッセージには次の情報が含まれています。

* BLOB に対して呼び出された関数 ("*{関数アプリ名}*.Functions.*{関数名}*"。たとえば、"functionsf74b96f7.Functions.CopyBlob")
* コンテナーの名前
* BLOB の種類 ("BlockBlob" か "PageBlob")
* BLOB の名前
* ETag (BLOB のバージョン識別子。たとえば、"0x8D1DC6E70A277EF")

BLOB を強制的に再処理する場合は、 *azure-webjobs-hosts* コンテナーからその BLOB の配信確認メッセージを手動で削除します。

#### 有害 BLOB の処理

BLOB トリガー関数が失敗した場合、失敗が一時的なエラーによって発生した場合は、SDK は再度関数を呼び出します。失敗が BLOB のコンテンツによって発生した場合は、BLOB の処理を試みるたびに関数は失敗します。既定では、SDK は特定の BLOB に対して最大 5 回、関数を呼び出します。5 回目が失敗すると、SDK はメッセージは、 *webjobs-blobtrigger-poison* という名前のキューにメッセージを追加します。

有害な BLOB のキュー メッセージは次のプロパティを持つ JSON オブジェクトです。

* FunctionId (形式: *{関数アプリ名}*.Functions.*{関数名}*)
* BLOB の種類 ("BlockBlob" か "PageBlob")
* コンテナー名
* BlobName
* ETag (BLOB のバージョン識別子。たとえば、"0x8D1DC6E70A277EF")

#### 大規模なコンテナーをポーリングする BLOB

トリガーが監視する BLOB コンテナーに 10,000 を超える BLOB が含まれる場合は、Functions ランタイムによりログ ファイルがスキャンされ、新しいまたは変更された BLOB が監視されます。このプロセスはリアルタイムではありません。関数は、BLOB が作成されてから数分またはそれ以上経過しないとトリガーされない可能性があります。また、[ストレージ ログの作成は "ベスト エフォート"](https://msdn.microsoft.com/library/azure/hh343262.aspx) ベースで行われます。そのため、すべてのイベントがキャプチャされる保証はありません。ある条件下では、ログが欠落する可能性があります。大規模なコンテナーで BLOB トリガーの速度と信頼性の制限がアプリケーションで許容されない場合は、BLOB を作成するときにキュー メッセージを作成し、BLOB トリガーではなくキュー トリガーを使って BLOB を処理することをお勧めします。
 
### <a id="storageblobbindings"></a>Azure Storage BLOB 入力および出力バインド

ストレージ BLOB 入力または出力バインドの *function.json* ファイルでは、次のプロパティを指定します。

- `name` : BLOB の関数コードで使用される変数名。 
- `path` : BLOB を読み込むまたは BLOB を書き込むコンテナーを指定するパスと、必要に応じて BLOB 名のパターンを指定します。
- `connection` : ストレージ接続文字列を含むアプリ設定の名前。`connection` を空のままにすると、バインドは AzureWebJobsStorage アプリ設定で指定される、関数アプリの既定のストレージ接続文字列で動作します。
- `type` : *blob* に設定する必要があります。
- `direction` : *in* または *out* に設定します。 

#### ストレージ BLOB 入力または出力バインドの *Function.json* の例

次の例では、キュー トリガーを使用して BLOB をコピーします。

```json
{
  "bindings": [
    {
      "queueName": "myqueue-items",
      "connection": "MyStorageConnection",
      "name": "myQueueItem",
      "type": "queueTrigger",
      "direction": "in"
    },
    {
      "name": "myInputBlob",
      "type": "blob",
      "path": "samples-workitems/{queueTrigger}",
      "connection": "MyStorageConnection",
      "direction": "in"
    },
    {
      "name": "myOutputBlob",
      "type": "blob",
      "path": "samples-workitems/{queueTrigger}-Copy",
      "connection": "MyStorageConnection",
      "direction": "out"
    }
  ],
  "disabled": false
}
``` 

#### BLOB の入力および出力にサポートされた型

`blob` バインドは、Node.js または C# 関数で次の型をシリアル化または逆シリアル化できます。

* オブジェクト (出力 BLOB の場合は、C# の`out T`: 関数が終了したときに、パラメーター値が null の場合に null オブジェクトとして BLOB を作成します)
* String (出力 BLOB の場合、C# の `out string`: 関数を返されたときに、文字列パラメーターが null 以外の場合にのみ BLOB を作成します)

C# 関数の場合は、次の型にもバインドできます。

* `TextReader` (入力のみ)
* `TextWriter` (出力のみ)
* `Stream`
* `CloudBlobStream` (出力のみ)
* `ICloudBlob`
* `CloudBlockBlob` 
* `CloudPageBlob` 

#### BLOB 出力の C# コードの例

この C# コード例では、名前をキュー メッセージで受信した BLOB をコピーします。

```csharp
public static void Run(string myQueueItem, string myInputBlob, out string myOutputBlob, TraceWriter log)
{
    log.Info($"C# Queue trigger function processed: {myQueueItem}");
    myOutputBlob = myInputBlob;
}
```

### <a id="storagetablesbindings"></a>Azure Storage テーブル入力および出力バインド

ストレージ テーブルの *function.json* ファイルでは、次のプロパティを指定します。

- `name` : テーブル バインドの関数コードで使用される変数名。 
- `tableName` : テーブルの名前。
- `partitionKey` および `rowKey` : 一緒に使用して、C# またはノード関数の単一エンティティを読み取るか、ノード関数の単一のエンティティを書き込みます。
- `take` : ノード関数でのテーブル入力のために読み取る行の最大数。
- `filter` : ノード関数のテーブル入力の OData フィルター式。
- `connection` : ストレージ接続文字列を含むアプリ設定の名前。`connection` を空のままにすると、バインドは AzureWebJobsStorage アプリ設定で指定される、関数アプリの既定のストレージ接続文字列で動作します。
- `type` : *table* に設定する必要があります。
- `direction` : *in* または *out* に設定します。 

#### ストレージ テーブルの入力および出力にサポートされた型

`table` バインドは、Node.js または C# 関数でオブジェクトをシリアル化または逆シリアル化できます。オブジェクトには、RowKey と PartitionKey プロパティがあります。

C# 関数の場合は、次の型にもバインドできます。

* `T` が `ITableEntity` を実装する場合の `T`
* `IQueryable<T>` (入力のみ)
* `ICollector<T>` (出力のみ)
* `IAsyncCollector<T>` (出力のみ)

#### ストレージ テーブル バインドのシナリオ

テーブル バインドは、次のシナリオをサポートしています。

* C# またはノード関数での単一行の読み取り。

	`partitionKey` と `rowKey` を設定します。`filter` と `take` プロパティは、このシナリオでは使用されません。

* C# 関数での複数行の読み取り。

	Functions ランタイムは、テーブルにバインドされている `IQueryable<T>` オブジェクトを指定します。型 `T` は `TableEntity` から派生するか、`ITableEntity` を実装する必要があります。`partitionKey`、`rowKey`、`filter`、および `take` プロパティは、このシナリオでは使用されません。必要なフィルターを実行するには、`IQueryable` オブジェクトを使用できます。

* ノード関数での複数行の読み取り。

	`filter` と `take` プロパティを設定します。`partitionKey` または `rowKey` は設定しません。

* C# 関数での 1 つまたは複数の行の書き込み。

	Functions ランタイムは、テーブルにバインドされた `ICollector<T>` または `IAsyncCollector<T>` を指定します。ここで、`T` は追加するエンティティのスキーマを指定します。型 `T` は `TableEntity` から派生するか、`ITableEntity` を実装するのが一般的ですが、必須ではありません。`partitionKey`、`rowKey`、`filter`、および `take` プロパティは、このシナリオでは使用されません。

#### ストレージ テーブルの例: C# またはノードでの 1 つのテーブル エンティティの読み取り

この *function.json* の例では、クエリ トリガーを使用して、ハードコーディングされたパーティション キーの値と、キュー メッセージに指定された行キーを使って 1 つのテーブル行を読み取ります。

```json
{
  "bindings": [
    {
      "queueName": "myqueue-items",
      "connection": "MyStorageConnection",
      "name": "myQueueItem",
      "type": "queueTrigger",
      "direction": "in"
    },
    {
      "name": "personEntity",
      "type": "table",
      "tableName": "Person",
      "partitionKey": "Test",
      "rowKey": "{queueTrigger}",
      "connection": "MyStorageConnection",
      "direction": "in"
    }
  ],
  "disabled": false
}
```

次の C# コード例では、上記の *function.json* ファイルを使用して、単一のテーブル エンティティを読み取ります。キュー メッセージには行キー値があり、テーブル エンティティは、*run.csx* ファイルに定義された型に読み込まれます。型は `PartitionKey` と `RowKey` プロパティを含み、`TableEntity` から派生しません。

```csharp
public static void Run(string myQueueItem, Person personEntity, TraceWriter log)
{
    log.Info($"C# Queue trigger function processed: {myQueueItem}");
    log.Info($"Name in Person entity: {personEntity.Name}");
}

public class Person
{
    public string PartitionKey { get; set; }
    public string RowKey { get; set; }
    public string Name { get; set; }
}
```

次のノード コード例でも、上記の *function.json* ファイルを使用して、単一のテーブル エンティティを読み取ります。

```javascript
module.exports = function (context, myQueueItem) {
    context.log('Node.js queue trigger function processed work item', myQueueItem);
    context.log('Person entity name: ' + context.bindings.personEntity.Name);
    context.done();
};
```

#### ストレージ テーブルの例: C での複数のテーブル エンティティの読み取り# 

次の *function.json* および C# コード例では、キュー メッセージに指定されたパーティション キーのエンティティを読み取ります。

```json
{
  "bindings": [
    {
      "queueName": "myqueue-items",
      "connection": "MyStorageConnection",
      "name": "myQueueItem",
      "type": "queueTrigger",
      "direction": "in"
    },
    {
      "name": "tableBinding",
      "type": "table",
      "connection": "MyStorageConnection",
      "tableName": "Person",
      "direction": "in"
    }
  ],
  "disabled": false
}
```

C# コードは、エンティティ型が `TableEntity` から派生できるように、Azure Storage SDK に参照を追加します。

```csharp
#r "Microsoft.WindowsAzure.Storage"
using Microsoft.WindowsAzure.Storage.Table;

public static void Run(string myQueueItem, IQueryable<Person> tableBinding, TraceWriter log)
{
    log.Info($"C# Queue trigger function processed: {myQueueItem}");
    foreach (Person person in tableBinding.Where(p => p.PartitionKey == myQueueItem).ToList())
    {
        log.Info($"Name: {person.Name}");
    }
}

public class Person : TableEntity
{
    public string Name { get; set; }
}
``` 

#### ストレージ テーブルの例: C でのテーブル エンティティの作成# 

次の *function.json* と *run.csx* の例は、C# でテーブル エンティティを書き込む方法を示します。

```json
{
  "bindings": [
    {
      "name": "input",
      "type": "manualTrigger",
      "direction": "in"
    },
    {
      "tableName": "Person",
      "connection": "MyStorageConnection",
      "name": "tableBinding",
      "type": "table",
      "direction": "out"
    }
  ],
  "disabled": false
}
```

```csharp
public static void Run(string input, ICollector<Person> tableBinding, TraceWriter log)
{
    for (int i = 1; i < 10; i++)
        {
            log.Info($"Adding Person entity {i}");
            tableBinding.Add(
                new Person() { 
                    PartitionKey = "Test", 
                    RowKey = i.ToString(), 
                    Name = "Name" + i.ToString() }
                );
        }

}

public class Person
{
    public string PartitionKey { get; set; }
    public string RowKey { get; set; }
    public string Name { get; set; }
}

```

#### ストレージ テーブルの例: ノードでのテーブル エンティティの作成

次の *function.json* と *run.csx* の例は、ノードでテーブル エンティティを書き込む方法を示します。

```json
{
  "bindings": [
    {
      "queueName": "myqueue-items",
      "connection": "MyStorageConnection",
      "name": "myQueueItem",
      "type": "queueTrigger",
      "direction": "in"
    },
    {
      "tableName": "Person",
      "partitionKey": "Test",
      "rowKey": "{queueTrigger}",
      "connection": "MyStorageConnection",
      "name": "personEntity",
      "type": "table",
      "direction": "out"
    }
  ],
  "disabled": true
}
```

```javascript
module.exports = function (context, myQueueItem) {
    context.log('Node.js queue trigger function processed work item', myQueueItem);
    context.bindings.personEntity = {"Name": "Name" + myQueueItem }
    context.done();
};
```

## Azure Service Bus のトリガーとバインド

このセクションには、次のようにいくつかのサブセクションがあります。

* [Azure Service Bus: PeekLock の動作](#sbpeeklock)
* [Azure Service Bus: 有害メッセージの処理](#sbpoison)
* [Azure Service Bus: シングル スレッド](#sbsinglethread)
* [Azure Service Bus キューまたはトピックのトリガー](#sbtrigger)
* [Azure Storage Bus キューまたはトピックの出力バインド](#sboutput)

### <a id="sbpeeklock"></a>Azure Service Bus: PeekLock の動作

Functions では、`PeekLock` モードでメッセージを受信して、関数が正常に終了した場合はメッセージの `Complete` を呼び出し、関数が失敗した場合は、`Abandon` を呼び出します。関数の実行時間が `PeekLock` タイムアウトよりも長くなると、ロックが自動的に更新されます。

### <a id="sbpoison"></a>Azure Service Bus: 有害メッセージの処理

Service Bus では、Azure Functions の構成やコードで制御または構成することができない、独自の有害メッセージを処理します。

### <a id="sbsinglethread"></a>Azure Service Bus: シングル スレッド

既定では、Functions ランタイムは、複数のキュー メッセージを同時に処理します。一度に 1 つのキューまたはトピックのメッセージのみを処理するように、ランタイムに指示するには、*host.json* ファイルで `serviceBus.maxConcurrrentCalls` を 1 に設定します。*host.json* ファイルの詳細については、開発者向けリファレンスの記事「[Folder Structure](functions-reference.md#folder-structure)」と、WebJobs.Script リポジトリ Wiki の「[host.json](https://github.com/Azure/azure-webjobs-sdk-script/wiki/host.json)」を参照してください。

### <a id="sbtrigger"></a>Azure Service Bus キューまたはトピックのトリガー

Service Bus トリガーの *function.json* ファイルでは、次のプロパティを指定します。

- `name` : キューまたはトピック、キューまたはトピックのメッセージの関数コードで使用される変数名。 
- `queueName` : キューのトリガーのみの場合、ポーリングするキューの名前。
- `topicName` : トピックのトリガーのみの場合は、ポーリングするトピックの名前。
- `subscriptionName` : トピックのトリガーのみの場合は、サブスクリプション名。
- `connection` : Service Bus 接続文字列を含むアプリ設定の名前。接続文字列は、特定のキューまたはトピックに限らず、Service Bus 名前空間に使用する必要があります。接続文字列に管理権限がない場合は、`accessRights` プロパティを設定します。`connection` を空のままにすると、トリガーまたはバインドは、AzureWebJobsServiceBus アプリ設定で指定される、関数アプリの既定の Service Bus 接続文字列で動作します。
- `accessRights` : 接続文字列に使用できるアクセス権を指定します。既定値は `manage` です。アクセス許可の管理を提供しない接続文字列を使用している場合、`listen` に設定します。それ以外の場合は、Functions ランタイムは、管理権限を必要とする操作を試行し、失敗する可能性があります。
- `type` : *serviceBusTrigger* に設定する必要があります。
- `direction` : *in* に設定する必要があります。 

Service Bus キュー メッセージを、次のいずれかの型に逆シリアル化できます。

* オブジェクト (JSON)
* string
* byte array 
* `BrokeredMessage` (C#) 

#### Service Bus キューのトリガーを使用する *Function.json* の例

```json
{
  "bindings": [
    {
      "queueName": "testqueue",
      "connection": "MyServiceBusConnection",
      "name": "myQueueItem",
      "type": "serviceBusTrigger",
      "direction": "in"
    }
  ],
  "disabled": false
}
```

#### Service Bus キュー メッセージを処理する C# コードの例

```csharp
public static void Run(string myQueueItem, TraceWriter log)
{
    log.Info($"C# ServiceBus queue trigger function processed message: {myQueueItem}");
}
```

#### Service Bus キュー メッセージを処理する Node.js コードの例

```javascript
module.exports = function(context, myQueueItem) {
    context.log('Node.js ServiceBus queue trigger function processed message', myQueueItem);
    context.done();
};
```

### <a id="sboutput"></a>Azure Service Bus キューまたはトピックの出力

Service Bus 出力バインドの *function.json* ファイルでは、次のプロパティを指定します。

- `name` : キューまたはキュー メッセージの関数コードで使用される変数名。 
- `queueName` : キューのトリガーのみの場合、ポーリングするキューの名前。
- `topicName` : トピックのトリガーのみの場合は、ポーリングするトピックの名前。
- `subscriptionName` : トピックのトリガーのみの場合は、サブスクリプション名。
- `connection` : Service Bus のトリガーと同じです。
- `accessRights` : 接続文字列に使用できるアクセス権を指定します。既定値は `manage` です。アクセス許可の管理を提供しない接続文字列を使用している場合、`send` に設定します。それ以外の場合は、Functions ランタイムは、キューの作成などの管理権限を必要とする操作を試行し、失敗する可能性があります。
- `type` : *serviceBus* に設定する必要があります。
- `direction` : *out* に設定する必要があります。 

Azure Functions は、次の型のいずれかから Service Bus キュー メッセージを作成できます。

* オブジェクト (常に JSON メッセージを作成し、関数が終了したときに、値が null である場合は、null オブジェクトでメッセージを作成)
* string (関数が終了したときに、値が null でない場合は、メッセージを作成します)
* byte array (string と同様に動作) 
* `BrokeredMessage` (C#、string と同様に動作)

C# 関数で複数のメッセージを作成するには、`ICollector<T>` または `IAsyncCollector<T>` を使用できます。`Add` メソッドを呼び出すときに、メッセージが作成されます。

#### Service Bus キュー メッセージを記述するために、タイマー トリガーを使用する *function.json* の例

```JSON
{
  "bindings": [
    {
      "schedule": "0/15 * * * * *",
      "name": "myTimer",
      "runsOnStartup": true,
      "type": "timerTrigger",
      "direction": "in"
    },
    {
      "name": "outputSbQueue",
      "type": "serviceBus",
      "queueName": "testqueue",
      "connection": "MyServiceBusConnection",
      "direction": "out"
    }
  ],
  "disabled": false
}
``` 

#### Service Bus キュー メッセージを作成する C# コードの例

```csharp
public static void Run(TimerInfo myTimer, TraceWriter log, out string outputSbQueue)
{
	string message = $"Service Bus queue message created at: {DateTime.Now}";
    log.Info(message); 
    outputSbQueue = message;
}
```

```csharp
public static void Run(TimerInfo myTimer, TraceWriter log, ICollector<string> outputSbQueue)
{
	string message = $"Service Bus queue message created at: {DateTime.Now}";
    log.Info(message); 
    outputSbQueue.Add("1 " + message);
    outputSbQueue.Add("2 " + message);
}
```

#### Service Bus キュー メッセージを作成する Node.js コードの例

```javascript
module.exports = function (context, myTimer) {
    var timeStamp = new Date().toISOString();
    
    if(myTimer.isPastDue)
    {
        context.log('Node.js is running late!');
    }
    var message = 'Service Bus queue message created at ' + timeStamp;
    context.log(message);   
    context.bindings.outputSbQueueMsg = message;
    context.done();
};
```

## Azure DocumentDB バインド

このセクションには、次のようにいくつかのサブセクションがあります。

* [Azure DocumentDB 入力バインド](#docdbinput)
* [Azure DocumentDB 出力バインド](#docdboutput)

### <a id="docdbinput"></a>Azure DocumentDB 入力バインド

入力バインドでは、DocumentDB コレクションからドキュメントを読み込んでそれをバインドに直接渡すことができます。ドキュメント ID は、関数を呼び出したトリガーに基づいて決定することができます。C# 関数で、レコードに加えられた変更は、関数が正常に終了したときに、コレクションに自動的に再送信されます。

function.json ファイルは、DocumentDB 入力バインドで使用するための次のプロパティを提供します。

- `name` : ドキュメントの関数コードで使用される変数名。
- `type` : "documentdb" に設定する必要があります。
- `databaseName` : ドキュメントを含むデータベース。
- `collectionName` : ドキュメントを含むコレクション。
- `id` : 取得するドキュメントの ID。このプロパティは、"{queueTrigger}" と同類のバインドをサポートします。ここでは、ドキュメント ID としてキュー メッセージの文字列値を使用します。
- `connection` : この文字列は、DocumentDB アカウントのエンドポイントに設定されたアプリケーション設定である必要があります。[Integrate] (統合) タブからアカウントを選択した場合、新しいアプリ設定が yourAccount\_DOCUMENTDB という名前形式で自動的に作成されます。アプリ設定を手動で作成する必要がある場合は、実際の接続文字列を次の形式にする必要があります: AccountEndpoint=<Endpoint for your account>;AccountKey=<Your primary access key>;。
- `direction : *"in"* に設定する必要があります。

function.json の例:
 
	{
	  "bindings": [
	    {
	      "name": "document",
	      "type": "documentdb",
	      "databaseName": "MyDatabase",
	      "collectionName": "MyCollection",
	      "id" : "{queueTrigger}",
	      "connection": "MyAccount_DOCUMENTDB",     
	      "direction": "in"
	    }
	  ],
	  "disabled": false
	}

#### Azure DocumentDB 入力コード例 (C# キュー トリガー)
 
上記の function.json の例を使用して、DocumentDB 入力バインドはキュー メッセージと一致する ID を持つドキュメントを取得し、それを document パラメーターに渡します。そのドキュメントが見つからない場合、document パラメーターは null になります。関数の終了時に、ドキュメントは新しいテキスト値で更新されます。
 
	public static void Run(string myQueueItem, dynamic document)
	{   
	    document.text = "This has changed.";
	}

#### Azure DocumentDB 入力コード例 (Node.js キュー トリガー)
 
上記の function.json の例を使用して、DocumentDB 入力バインドはキュー メッセージ文字列と一致する ID を持つドキュメントを取得し、それを `documentIn` バインド プロパティに渡します。Node.js 関数では、更新されたドキュメントは再びコレクションに送信されません。ただし、入力バインドを、更新をサポートする `documentOut` という名前の DocumentDB 出力バインドに直接渡すことができます。このコード例では、入力ドキュメントの text プロパティを更新し、出力ドキュメントとして設定します。
 
	module.exports = function (context, input) {   
	    context.bindings.documentOut = context.bindings.documentIn;
	    context.bindings.documentOut.text = "This was updated!";
	    context.done();
	};

### <a id="docdboutput"></a>Azure DocumentDB 出力バインド

関数を使用して、**Azure DocumentDB Document** 出力バインドを使って、JSON ドキュメントを Azure DocumentDB データベースに書き込むことができます。Azure DocumentDB の詳細については、「[DocumentDB の概要](../documentdb/documentdb-introduction.md)」および「[概要チュートリアル](../documentdb/documentdb-get-started.md)」を参照してください。

function.json ファイルは、DocumentDB 出力バインドで使用するための次のプロパティを提供します。

- `name` : 新しいドキュメントの関数コードで使用される変数名。
- `type` : *"documentdb"* に設定する必要があります。
- `databaseName` : 新しいドキュメントが作成されるコレクションを含むデータベース。
- `collectionName` : 新しいドキュメントが作成されるコレクション。
- `createIfNotExists` : これは、存在しない場合にコレクションを作成するかどうかを示すブール値です。既定値は *false* です。これは、新しいコレクションが予約済みのスループットで作成されるためです。これにより、価格に影響が及びます。詳細については、[価格のページ](https://azure.microsoft.com/pricing/details/documentdb/)を参照してください。
- `connection` : この文字列は、DocumentDB アカウントのエンドポイントに設定された**アプリケーション設定**である必要があります。**[統合]** タブからアカウントを選択した場合、新しいアプリ設定が `yourAccount_DOCUMENTDB` という名前形式で自動的に作成されます。アプリ設定を手動で作成する必要がある場合は、実際の接続文字列を次の形式にする必要があります: `AccountEndpoint=<Endpoint for your account>;AccountKey=<Your primary access key>;`。 
- `direction` : *"out"* に設定する必要があります。 
 
function.json の例:

	{
	  "bindings": [
	    {
	      "name": "document",
	      "type": "documentdb",
	      "databaseName": "MyDatabase",
	      "collectionName": "MyCollection",
	      "createIfNotExists": false,
	      "connection": "MyAccount_DOCUMENTDB",
	      "direction": "out"
	    }
	  ],
	  "disabled": false
	}


#### Azure DocumentDB 出力コード例 (Node.js キュー トリガー)

	module.exports = function (context, input) {
	   
	    context.bindings.document = {
	        text : "I'm running in a Node function! Data: '" + input + "'"
	    }   
	 
	    context.done();
	};

出力ドキュメント:

	{
	  "text": "I'm running in a Node function! Data: 'example queue data'",
	  "id": "01a817fe-f582-4839-b30c-fb32574ff13f"
	}
 

#### Azure DocumentDB 出力コード例 (C# キュー トリガー)


	using System;

	public static void Run(string myQueueItem, out object document, TraceWriter log)
	{
	    log.Info($"C# Queue trigger function processed: {myQueueItem}");
	   
	    document = new {
	        text = $"I'm running in a C# function! {myQueueItem}"
	    };
	}


#### ファイル名を設定する Azure DocumentDB 出力コード例

関数でドキュメントの名前を設定するには、単に `id` 値を設定します。たとえば、従業員の JSON コンテンツが次のようなキューにドロップされた場合は、以下のようになります。

	{
	  "name" : "John Henry",
      "employeeId" : "123456",
	  "address" : "A town nearby"
	}

キュー トリガー関数で、次の C# コードを使用できます。
	
	#r "Newtonsoft.Json"
	
	using System;
	using Newtonsoft.Json;
	using Newtonsoft.Json.Linq;
	
	public static void Run(string myQueueItem, out object employeeDocument, TraceWriter log)
	{
	    log.Info($"C# Queue trigger function processed: {myQueueItem}");
	    
	    dynamic employee = JObject.Parse(myQueueItem);
	    
	    employeeDocument = new {
	        id = employee.name + "-" + employee.employeeId,
	        name = employee.name,
	        employeeId = employee.employeeId,
	        address = employee.address
	    };
	}

出力例:

	{
	  "id": "John Henry-123456",
	  "name": "John Henry",
	  "employeeId": "123456",
	  "address": "A town nearby"
	}

## Azure Mobile Apps のバインド

Azure App Service Mobile Apps を使用すると、テーブル エンドポイント データをモバイル クライアントに公開できます。この同じ表形式のデータは、Azure Functions の入力バインドと出力バインドの両方で使用できます。動的スキーマをサポートしているため、Node.js バックエンドのモバイル アプリは、関数で使用するために表形式のデータを公開するのに最適です。動的スキーマは既定で有効になっていますが、運用環境のモバイル アプリでは無効にする必要があります。Node.js バックエンドでテーブル エンドポイントの詳細については、「[概要: テーブル操作](../app-service-mobile/app-service-mobile-node-backend-how-to-use-server-sdk.md#TableOperations)」を参照してください。Mobile Apps では、Node.js バックエンドは、ポータル内の参照とテーブルの編集をサポートします。詳細については、Node.js SDK トピックの「[ポータルでの編集](../app-service-mobile/app-service-mobile-node-backend-how-to-use-server-sdk.md#in-portal-editing)」を参照してください。Azure Functions で .NET バックエンドのモバイル アプリを使用する場合は、関数からの必要に応じて、データ モデルを手動で更新する必要があります。.NET バックエンドのモバイル アプリのテーブル エンドポイントの詳細については、.NET バックエンドの SDK トピックの「[方法: テーブル コントローラーを定義する](../app-service-mobile/app-service-mobile-dotnet-backend-how-to-use-server-sdk.md#define-table-controller)」を参照してください。

このセクションには、次のようにいくつかのサブセクションがあります。

* [Azure Mobile Apps のテーブルの API キー](#mobiletablesapikey)
* [Azure Mobile Apps のテーブルの入力バインド](#mobiletablesinput)
* [Azure Mobile Apps のテーブルの出力バインド](#mobiletablesoutput)

### <a id="mobiletablesapikey"></a>API キーを使用した Mobile Apps テーブル エンドポイントへの安全なアクセス

Azure Functions のモバイル テーブルのバインドでは、API キーを指定できます。これは、関数以外のアプリからの望ましくないアクセスを回避するために使用できる共有シークレットです。Mobile Apps には、API キー認証向けのサポートが組み込まれていません。ただし、「[API キーを実装する Azure App Service Mobile Apps バックエンド](https://github.com/Azure/azure-mobile-apps-node/tree/master/samples/api-key)」の例に従って、Node.js バックエンド モバイル アプリに API キーを実装できます。同様に、[.NET バックエンドのモバイル アプリ](https://github.com/Azure/azure-mobile-apps-net-server/wiki/Implementing-Application-Key)に API キーを実装することができます。

>[AZURE.IMPORTANT] この API キーはモバイル アプリ クライアントで配布する必要があり、Azure Functions のようなサービス側のクライアントにのみ安全に配布する必要があります。

### <a id="mobiletablesinput"></a>Azure Mobile Apps の入力バインド

入力バインドでは、モバイル テーブル エンドポイントからレコードを読み込んで、バインドに直接渡すことができます。レコード ID は、関数を呼び出したトリガーに基づいて決定されます。C# 関数で、レコードに加えられた変更は、関数が正常に終了したときに、テーブルに自動的に再送信されます。

function.json ファイルでは、Mobile Apps の入力バインドで使用するための次のプロパティがサポートされています。

- `name` : 新しいレコードの関数コードで使用される変数名。
- `type` : バインドの型は *mobileTable* に設定する必要があります。
- `tableName` : 新しいレコードの作成先のテーブル。
- `id` : 取得するレコードの ID。このプロパティは、`{queueTrigger}` と同様のバインドをサポートします。ここでは、レコード ID としてキュー メッセージの文字列値を使用します。
- `apiKey` : モバイル アプリ用のオプションの API キーを指定するアプリケーション設定である文字列。これは、モバイル アプリが API キーを使用してクライアント アクセスを制限するときに必要です。
- `connection` : モバイル アプリの URI を指定するアプリケーション設定である文字列。
- `direction` : バインドの方向。*in* に設定する必要があります。

function.json の例:

	{
	  "bindings": [
	    {
	      "name": "record",
	      "type": "mobileTable",
	      "tableName": "MyTable",
	      "id" : "{queueTrigger}",
	      "connection": "My_MobileApp_Uri",
	      "apiKey": "My_MobileApp_Key",
	      "direction": "in"
	    }
	  ],
	  "disabled": false
	}

#### Azure Mobile Apps のコード例 (C# キュー トリガー)

上記の function.json の例に基づいて、入力バインドはキュー メッセージ文字列と一致する ID を持つ Mobile Apps テーブル エンドポイントからレコードを取得し、それを *record* パラメーターに渡します。レコードが検出されなかった場合、パラメーターは null になります。関数の終了時に、レコードは新しい *Text* 値で更新されます。

	#r "Newtonsoft.Json"	
	using Newtonsoft.Json.Linq;
	
	public static void Run(string myQueueItem, JObject record)
	{
	    if (record != null)
	    {
	        record["Text"] = "This has changed.";
	    }    
	}

#### Azure Mobile Apps のコード例 (Node.js キュー トリガー)

上記の function.json の例に基づいて、入力バインドはキュー メッセージ文字列と一致する ID を持つ Mobile Apps テーブル エンドポイントからレコードを取得し、それを *record* パラメーターに渡します。Node.js 関数では、更新されたレコードは再びテーブルに送信されません。このコード例では、取得されたレコードをログに書き込みます。

	module.exports = function (context, input) {    
	    context.log(context.bindings.record);
	    context.done();
	};


### <a id="mobiletablesoutput"></a>Azure Mobile Apps の出力バインド

関数により、出力バインドを使用して、レコードを Mobile Apps テーブル エンドポイントに書き込めます。

function.json ファイルでは、モバイル テーブルの出力バインドで使用するための次のプロパティがサポートされています。

- `name` : 新しいレコードの関数コードで使用される変数名。
- `type` : *mobileTable* に設定する必要があるバインドの型。
- `tableName` : 新しいレコードが作成されるテーブル。
- `apiKey` : モバイル アプリ用のオプションの API キーを指定するアプリケーション設定である文字列。これは、モバイル アプリが API キーを使用してクライアント アクセスを制限するときに必要です。
- `connection` : モバイル アプリの URI を指定するアプリケーション設定である文字列。
- `direction` : バインドの方向。*out* に設定する必要があります。

function.json の例:

	{
	  "bindings": [
	    {
	      "name": "record",
	      "type": "mobileTable",
	      "tableName": "MyTable",
	      "connection": "My_MobileApp_Uri",
	      "apiKey": "My_MobileApp_Key",
	      "direction": "out"
	    }
	  ],
	  "disabled": false
	}

#### Azure Mobile Apps のコード例 (C# キュー トリガー)

この C# コード例では、*Text* プロパティを持つ Mobile Apps テーブル エンドポイントを、上のバインドで指定したテーブルに挿入します。

	public static void Run(string myQueueItem, out object record)
	{
	    record = new {
	        Text = $"I'm running in a C# function! {myQueueItem}"
	    };
	}

#### Azure Mobile Apps のコード例 (Node.js キュー トリガー)

この Node.js コード例では、*Text* プロパティを持つ Mobile Apps テーブル エンドポイントを、上のバインドで指定したテーブルに挿入します。

	module.exports = function (context, input) {
	
	    context.bindings.record = {
	        text : "I'm running in a Node function! Data: '" + input + "'"
	    }   
	
	    context.done();
	};

## Azure Notification Hub 出力バインド

関数を使用して、構成された Azure Notification Hub でほんの数行のコードを使ってプッシュ通知を送信できます。ただし、通知ハブは、使用するプラットフォーム通知サービス (PNS) 用に構成する必要があります。Azure Notification Hub の構成方法や、通知用に登録するクライアント アプリケーションの開発方法の詳細については、「[Notification Hubs の使用](../notification-hubs/notification-hubs-windows-store-dotnet-get-started.md)」を参照して、上部の目的のクライアント プラットフォームをクリックしてください。

function.json ファイルは、通知ハブ出力バインドに使用する次のプロパティを提供します。

- `name` : 通知ハブ メッセージの関数コードで使用される変数名。
- `type` : *"notificationHub"* に設定する必要があります。
- `tagExpression` : タグ式。これにより、タグ式に一致する通知を受信するように登録した一連のデバイスに通知を配信するように指定できます。詳細については、「[ルーティングとタグ式](../notification-hubs/notification-hubs-routing-tag-expressions.md)」を参照してください。
- `hubName` : Azure ポータル内の通知ハブ リソースの名前。
- `connection` : この接続文字列は、使用している通知ハブの *DefaultFullSharedAccessSignature* 値に設定された**アプリケーション設定**接続文字列である必要があります。
- `direction` : *"out"* に設定する必要があります。 
 
function.json の例:

	{
	  "bindings": [
	    {
	      "name": "notification",
	      "type": "notificationHub",
	      "tagExpression": "",
	      "hubName": "my-notification-hub",
	      "connection": "MyHubConnectionString",
	      "direction": "out"
	    }
	  ],
	  "disabled": false
	}

### Azure Notification Hub の接続文字列の設定

Notification Hub 出力バインドを使用するには、ハブの接続文字列を設定する必要があります。このためには、*[統合]* タブで、単に通知ハブを選択するか、新しいハブを作成します。

*DefaultFullSharedAccessSignature* の接続文字列を通知ハブに追加して、既存のハブの接続文字列を手動で追加することもできます。この接続文字列により、通知メッセージを送信するための関数アクセス権限が付与されます。*DefaultFullSharedAccessSignature* 接続文字列の値には、Azure ポータルの通知ハブ リソースのメイン ブレード内の **[キー]** ボタンからアクセスできます。ハブの接続文字列を手動で追加するには、次の手順を実行します。

1. Azure ポータルの **[関数アプリ]** ブレードで、**[関数アプリの設定]、[App Service の設定に移動]** の順にクリックします。

2. **[設定]** ブレードで、**[アプリケーション設定]** をクリックします。

3. **[接続文字列]** セクションまで下にスクロールして、通知ハブの *DefaultFullSharedAccessSignature* 値の名前付きエントリを追加します。種類を **[カスタム]** に変更します。
4. 出力バインドの接続文字列名を参照します。上の例で使用した **MyHubConnectionString** と同様です。

### Azure Notification Hub のコード例 (Node.js タイマー トリガー) 

この例では、`location` と `message` を含む[テンプレート登録](../notification-hubs/notification-hubs-templates.md)の通知を送信します。

	module.exports = function (context, myTimer) {
	    var timeStamp = new Date().toISOString();
	   
	    if(myTimer.isPastDue)
	    {
	        context.log('Node.js is running late!');
	    }
	    context.log('Node.js timer trigger function ran!', timeStamp);  
	    context.bindings.notification = {
	        location: "Redmond",
	        message: "Hello from Node!"
	    };
	    context.done();
	};

### Azure Notification Hub のコード例 (C# キュー トリガー)

この例では、`message` を含む[テンプレート登録](../notification-hubs/notification-hubs-templates.md)の通知を送信します。


	using System;
	using System.Threading.Tasks;
	using System.Collections.Generic;
	 
	public static void Run(string myQueueItem,  out IDictionary<string, string> notification, TraceWriter log)
	{
	    log.Info($"C# Queue trigger function processed: {myQueueItem}");
        notification = GetTemplateProperties(myQueueItem);
	}
	 
	private static IDictionary<string, string> GetTemplateProperties(string message)
	{
	    Dictionary<string, string> templateProperties = new Dictionary<string, string>();
	    templateProperties["message"] = message;
	    return templateProperties;
	}

この例では、有効な JSON 文字列を使用して `message` を含む[テンプレート登録](../notification-hubs/notification-hubs-templates.md)の通知を送信します。

	using System;
	 
	public static void Run(string myQueueItem,  out string notification, TraceWriter log)
	{
		log.Info($"C# Queue trigger function processed: {myQueueItem}");
		notification = "{"message":"Hello from C#. Processed a queue item!"}";
	}

### 種類 Notification を使用した Azure Notification Hub キュー トリガーの C# コード例

この例では、[Microsoft Azure Notification Hubs ライブラリ](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/)で定義された種類 `Notification` の使用方法を示します。この種類、およびライブラリを使用するには、関数アプリの *project.json* ファイルをアップロードする必要があります。project.json ファイルは、次のような JSON テキスト ファイルです。

	{
	  "frameworks": {
	    ".NETFramework,Version=v4.6": {
	      "dependencies": {
	        "Microsoft.Azure.NotificationHubs": "1.0.4"
	      }
	    }
	  }
	}

project.json ファイルのアップロードの詳細については、「[project.json ファイルのアップロード](http://stackoverflow.com/questions/36411536/how-can-i-use-nuget-packages-in-my-azure-functions)」を参照してください。

コード例:

	using System;
	using System.Threading.Tasks;
	using Microsoft.Azure.NotificationHubs;
	 
	public static void Run(string myQueueItem,  out Notification notification, TraceWriter log)
	{
	   log.Info($"C# Queue trigger function processed: {myQueueItem}");
	   notification = GetTemplateNotification(myQueueItem);
	}
	private static TemplateNotification GetTemplateNotification(string message)
	{
	    Dictionary<string, string> templateProperties = new Dictionary<string, string>();
	    templateProperties["message"] = message;
	    return new TemplateNotification(templateProperties);
	}

## 次のステップ

詳細については、次のリソースを参照してください。

* [Azure Functions developer reference (Azure Functions 開発者向けリファレンス)](functions-reference.md)
* [Azure Functions C# developer reference (Azure Functions C# 開発者向けリファレンス)](functions-reference-csharp.md)
* [Azure Functions NodeJS 開発者向けリファレンス](functions-reference-node.md)

<!---HONumber=AcomDC_0518_2016-->