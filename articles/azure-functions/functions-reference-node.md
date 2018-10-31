---
title: Azure Functions 用 JavaScript 開発者向けリファレンス | Microsoft Docs
description: JavaScript を使用して関数を開発する方法について説明します。
services: functions
documentationcenter: na
author: ggailey777
manager: jeconnoc
keywords: Azure Functions, 機能, イベント処理, Webhook, 動的コンピューティング, サーバーなしのアーキテクチャ
ms.assetid: 45dedd78-3ff9-411f-bb4b-16d29a11384c
ms.service: azure-functions
ms.devlang: nodejs
ms.topic: reference
ms.date: 03/04/2018
ms.author: glenga
ms.openlocfilehash: eb9387cec98621e27aff7dcb40b8897e326c6706
ms.sourcegitcommit: 8e06d67ea248340a83341f920881092fd2a4163c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/16/2018
ms.locfileid: "49353494"
---
# <a name="azure-functions-javascript-developer-guide"></a>Azure Functions の JavaScript 開発者向けガイド
このガイドには、JavaScript で Azure 関数を記述する複雑な作業についての情報が含まれます。

JavaScript 関数はエクスポートされた `function` であり、トリガーされると実行します ([トリガーは function.json で構成します](functions-triggers-bindings.md))。 各関数には `context` オブジェクトが渡され、バインド データの送受信、ログ記録、ランタイムとの通信に使用されます。

この記事では、「[Azure Functions の開発者向けガイド](functions-reference.md)」を既に読んでいることを前提としています。 "クイック スタート" のチュートリアルを実行し、[初めての関数を作成](functions-create-first-function-vs-code.md)しておくこともお勧めします。

## <a name="folder-structure"></a>フォルダー構造

JavaScript プロジェクトでは、次のようなフォルダー構造が必要です。 この既定の構造は変更できることに注意してください。詳しくは、[scriptFile](functions-reference-node.md#using-scriptfile) に関する後のセクションをご覧ください。

```
FunctionsProject
 | - MyFirstFunction
 | | - index.js
 | | - function.json
 | - MySecondFunction
 | | - index.js
 | | - function.json
 | - SharedCode
 | | - myFirstHelperFunction.js
 | | - mySecondHelperFunction.js
 | - node_modules
 | - host.json
 | - package.json
 | - extensions.csproj
 | - bin
```

プロジェクトのルートには、関数アプリの構成に使用できる共有 [host.json](functions-host-json.md) ファイルがあります。 各関数には、独自のコード ファイル (.js) とバインド構成ファイル (function.json) が含まれるフォルダーがあります。

Functions ランタイムの[バージョン 2.x](functions-versions.md) に必要なバインド拡張機能は `extensions.csproj` ファイルで定義されており、実際のライブラリ ファイルは `bin` フォルダーにあります。 ローカルで開発する場合は、[バインド拡張機能を登録する](functions-triggers-bindings.md#local-development-azure-functions-core-tools)必要があります。 Azure portal 上で関数を開発するときに、この登録が実行されます。

## <a name="exporting-a-function"></a>関数のエクスポート

[`module.exports`](https://nodejs.org/api/modules.html#modules_module_exports) (または [`exports`](https://nodejs.org/api/modules.html#modules_exports)) を使用して、JavaScript 関数をエクスポートする必要があります。 既定では、エクスポートされる関数は、そのファイルからの唯一のエクスポートである必要があります (`run` という名前のエクスポート、または `index` という名前のエクスポート)。 関数の既定の場所は `index.js` であり、`index.js` は対応する `function.json` と同じ親ディレクトリを共有します。 `function.json` の親ディレクトリの名前は常に関数の名前であることに注意してください。 

関数のファイルの場所とエクスポートの名前を構成する方法については、後の「[関数のエントリ ポイントを構成する](functions-reference-node.md#configure-function-entry-point)」をご覧ください。

エクスポートされる関数エントリ ポイントでは、常に、最初のパラメーターとして `context` オブジェクトを受け取る必要があります。

```javascript
// You must include a context, other arguments are optional
module.exports = function(context, myTrigger, myInput, myOtherInput) {
    // function logic goes here :)
    context.done();
};
```
```javascript
// You can also use 'arguments' to dynamically handle inputs
module.exports = async function(context) {
    context.log('Number of inputs: ' + arguments.length);
    // Iterates through trigger and input binding data
    for (i = 1; i < arguments.length; i++){
        context.log(arguments[i]);
    }
};
```

トリガーと入力バインド (`direction === "in"` のバインド) を、パラメーターとして関数に渡すことができます。 それらは、*function.json* に定義されている順序で関数に渡されます。 また、JavaScript の [`arguments`](https://msdn.microsoft.com/library/87dw3w1k.aspx) オブジェクトを使用して、入力を動的に処理することもできます。 たとえば、`function(context, a, b)` があり、それを `function(context, a)` に変更しても、`arguments[2]` を参照することで、関数コードの `b` の値を取得できます。

すべてのバインドは、方向に関係なく、`context.bindings` プロパティを使用して `context` オブジェクトで渡すこともできます。

### <a name="exporting-an-async-function"></a>async function をエクスポートする
JavaScript の [`async function`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Statements/async_function) 宣言またはプレーンな JavaScript の [Promise](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Promise) (関数 v1.x では使用できません) を使用するときは、[`context.done`](#contextdone-method) コールバックを呼び出して関数が完了したことを明示的に通知する必要はありません。 エクスポートされた async function/Promise が完了すると、関数は完了します。

たとえば、次に示すのはトリガーされてすぐに実行が完了したことを記録する簡単な関数です。
``` javascript
module.exports = async function (context) {
    context.log('JavaScript trigger function processed a request.');
};
```

async function をエクスポートするときは、`return` の値を取得するための出力バインドを構成することもできます。 これは、[`context.bindings`](#contextbindings-property) プロパティを使用した出力の割り当ての代わりになるアプローチです。

`return` を使用して出力を割り当てるには、`function.json` で `name` プロパティを `$return` に変更します。
```json
{
  "type": "http",
  "direction": "out",
  "name": "$return"
}
```
JavaScript 関数のコードは次のようになります。
```javascript
module.exports = async function (context, req) {
    context.log('JavaScript HTTP trigger function processed a request.');
    // You can call and await an async method here
    return {
        body: "Hello, world!"
    };
}
```

## <a name="context-object"></a>context オブジェクト
ランタイムでは、`context` オブジェクトを使用して、関数との間でデータをやり取りし、ユーザーがランタイムと通信できるようにします。

`context` オブジェクトは、常に関数の最初のパラメーターとし、必ず含める必要があります。context オブジェクトには、ランタイムを適切に使用するのに必要な `context.done` や `context.log` などのメソッドが用意されているためです。 オブジェクトには、任意の名前 (`ctx` や `c` など) を付けることができます。

```javascript
// You must include a context, but other arguments are optional
module.exports = function(ctx) {
    // function logic goes here :)
    ctx.done();
};
```

### <a name="contextbindings-property"></a>context.bindings プロパティ

```
context.bindings
```
すべての入力データと出力データを含む名前付きオブジェクトを返します。 たとえば、*function.json* で次のようなバインド定義を使用すると、`context.bindings.myInput` からキューの内容にアクセスし、`context.bindings.myOutput` を使用して出力をキューに割り当てることができます。

```json
{
    "type":"queue",
    "direction":"in",
    "name":"myInput"
    ...
},
{
    "type":"queue",
    "direction":"out",
    "name":"myOutput"
    ...
}
```

```javascript
// myInput contains the input data, which may have properties such as "name"
var author = context.bindings.myInput.name;
// Similarly, you can set your output data
context.bindings.myOutput = { 
        some_text: 'hello world', 
        a_number: 1 };
```

`context.binding` オブジェクトの代わりに `context.done` メソッドを使用して、出力バインド データを定義できることに注意してください (下記参照)。

### <a name="contextbindingdata-property"></a>context.bindingData プロパティ

```
context.bindingData
```
トリガーのメタデータと関数呼び出しデータを含む名前付きオブジェクトを返します (`invocationId`、`sys.methodName`、`sys.utcNow`、`sys.randGuid`)。 トリガーのメタデータの例については、こちらの[イベント ハブの例](functions-bindings-event-hubs.md#trigger---javascript-example)をご覧ください。

### <a name="contextdone-method"></a>context.done メソッド
```
context.done([err],[propertyBag])
```

コードが完了したことをランタイムに通知します。 関数で JavaScript の [`async function`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Statements/async_function) 宣言 (Functions バージョン 2.x で Node 8 以降を使用して利用可能) を使用する場合は、`context.done()` を使用する必要はありません。 `context.done` コールバックは暗黙的に呼び出されます。

関数が async function でない場合は、関数が完了したことをランタイムに通知するために、`context.done` を**呼び出す必要があります**。 これがない場合、実行はタイムアウトします。

`context.done` メソッドを使用すると、ランタイムに対するユーザー定義のエラーと、出力バインド データを含む JSON オブジェクトの両方を、戻すことができます。 `context.done` に渡されるプロパティは、`context.bindings` オブジェクトで設定されているすべてのものを上書きします。

```javascript
// Even though we set myOutput to have:
//  -> text: 'hello world', number: 123
context.bindings.myOutput = { text: 'hello world', number: 123 };
// If we pass an object to the done function...
context.done(null, { myOutput: { text: 'hello there, world', noNumber: true }});
// the done method will overwrite the myOutput binding to be: 
//  -> text: 'hello there, world', noNumber: true
```

### <a name="contextlog-method"></a>context.log メソッド  

```
context.log(message)
```
既定のトレース レベルでストリーミング関数ログに書き込むことができます。 `context.log` には、他のトレース レベルで関数のログを書き込むことができる追加のログ記録メソッドがあります。


| 方法                 | 説明                                |
| ---------------------- | ------------------------------------------ |
| **error(_message_)**   | エラー レベルのログ、またはそれ以下に書き込みます。   |
| **warn(_message_)**    | 警告レベルのログ、またはそれ以下に書き込みます。 |
| **info(_message_)**    | 情報レベルのログ、またはそれ以下に書き込みます。    |
| **verbose(_message_)** | 詳細なレベルのログに書き込みます。           |

次の例では、警告トレース レベルでログを書き込んでいます。

```javascript
context.log.warn("Something has happened."); 
```
host.json ファイルでは、[ログに対するトレース レベルのしきい値を構成する](#configure-the-trace-level-for-console-logging)ことができます。 ログの書き込みについて詳しくは、後の「[トレース出力をコンソールに書き込む](#writing-trace-output-to-the-console)」をご覧ください。

関数のログの表示とクエリについて詳しくは、「[Azure Functions を監視する](functions-monitoring.md)」をご覧ください。

## <a name="binding-data-type"></a>バインド データ型

入力バインドのデータ型を定義するには、バインド定義の `dataType` プロパティを使用します。 たとえば、バイナリ形式で HTTP 要求のコンテンツを読み取るには、`binary` 型を使用します。

```json
{
    "type": "httpTrigger",
    "name": "req",
    "direction": "in",
    "dataType": "binary"
}
```

`dataType` のオプションは、`binary`、`stream`、`string` です。

## <a name="writing-trace-output-to-the-console"></a>トレース出力をコンソールに書き込む 

関数で、`context.log` メソッドを使用してトレース出力をコンソールに書き込みます。 Functions v2.x では、`console.log` によるトレース出力は Function App レベルでキャプチャされます。 つまり、`console.log` からの出力は特定の関数呼び出しに関連付けられておらず、そのため特定の関数のログには表示されません。 ただし、Application Insights に伝達されます。 Functions v1.x では、`console.log` を使用してコンソールに書き込むことはできません。 

`context.log()` を呼び出すと、既定のトレース レベルである、_情報_ トレース レベルでコンソールにメッセージが書き込まれます。 次のコードは、情報トレース レベルでコンソールに書き込みます。

```javascript
context.log({hello: 'world'});  
```

次のコードは、上記のコードと同等です。

```javascript
context.log.info({hello: 'world'});  
```

このコードは、エラー レベルでコンソールに書き込みます。

```javascript
context.log.error("An error has occurred.");  
```

_エラー_ は最高のトレース レベルであるため、ログ記録が有効になっている限り、このトレースはすべてのトレース レベルで出力に書き込まれます。

すべての `context.log` メソッドは、Node.js の [util.format メソッド](https://nodejs.org/api/util.html#util_util_format_format)でサポートされているのと同じパラメーター形式をサポートしています。 既定のトレース レベルを使用して関数ログに書き込む次のようなコードについて考えます。

```javascript
context.log('Node.js HTTP trigger function processed a request. RequestUri=' + req.originalUrl);
context.log('Request Headers = ' + JSON.stringify(req.headers));
```

同じコードを次の形式で記述することもできます。

```javascript
context.log('Node.js HTTP trigger function processed a request. RequestUri=%s', req.originalUrl);
context.log('Request Headers = ', JSON.stringify(req.headers));
```

### <a name="configure-the-trace-level-for-console-logging"></a>コンソール ログのトレース レベルを構成する

関数を使用して、コンソールに書き込むためのしきい値のトレース レベルを定義できます。これによって、関数からコンソールにトレースを書き込む方法を簡単に制御できます。 コンソールに書き込まれるすべてのトレースのしきい値を設定するには、host.json ファイルの `tracing.consoleLevel` プロパティを使用します。 この設定は、関数アプリのすべての関数に適用されます。 次の例では、詳細ログ記録が有効になるようにトレースのしきい値を設定します。

```json
{ 
    "tracing": {      
        "consoleLevel": "verbose"     
    }
}  
```

**consoleLevel** の値は、`context.log` メソッドの名前に対応します。 コンソールへのすべてのトレース ログ記録を無効にするには、**consoleLevel** を _off_ に設定します。 詳細については、[host.json](functions-host-json.md) のリファレンスを参照してください。

## <a name="http-triggers-and-bindings"></a>HTTP トリガーとバインディング

HTTP、webhook トリガー、および HTTP 出力バインディングでは、要求オブジェクトと応答オブジェクトを使用して HTTP メッセージングを表します。  

### <a name="request-object"></a>要求オブジェクト

`context.req` (要求) オブジェクトには、次のプロパティがあります。

| プロパティ      | 説明                                                    |
| ------------- | -------------------------------------------------------------- |
| _body_        | 要求の本文を格納するオブジェクト。               |
| _headers_     | 要求ヘッダーを格納するオブジェクト。                   |
| _method_      | 要求の HTTP メソッド。                                |
| _originalUrl_ | 要求の URL。                                        |
| _params_      | 要求のルーティング パラメーターを格納するオブジェクト。 |
| _query_       | クエリ パラメーターを格納するオブジェクト。                  |
| _rawBody_     | 文字列としてのメッセージの本文。                           |


### <a name="response-object"></a>応答オブジェクト

`context.res` (応答) オブジェクトには、次のプロパティがあります。

| プロパティ  | 説明                                               |
| --------- | --------------------------------------------------------- |
| _body_    | 応答の本文を格納するオブジェクト。         |
| _headers_ | 応答ヘッダーを格納するオブジェクト。             |
| _isRaw_   | 応答の書式設定をスキップすることを示します。    |
| _状態_  | 応答の HTTP 状態コード。                     |

### <a name="accessing-the-request-and-response"></a>要求と応答へのアクセス 

HTTP トリガーを使用する場合、HTTP 要求オブジェクトと応答オブジェクトにアクセスする方法がいくつかあります。

+ `context` オブジェクトの `req` プロパティと `res` プロパティから。 この方法で、完全な `context.bindings.name` パターンを使用する代わりに、従来のパターンを使用して context オブジェクトから HTTP データにアクセスできます。 次の例では、`context` の `req` オブジェクトと `res` オブジェクトにアクセスする方法を示します。

    ```javascript
    // You can access your http request off the context ...
    if(context.req.body.emoji === ':pizza:') context.log('Yay!');
    // and also set your http response
    context.res = { status: 202, body: 'You successfully ordered more coffee!' }; 
    ```

+ 名前付きの入力バインディングと出力バインディングから。 この方法で、HTTP トリガーとバインディングは他のバインディングと同じように動作します。 次の例では、名前付き `response` バインディングを使用して、応答オブジェクトを設定します。 

    ```json
    {
        "type": "http",
        "direction": "out",
        "name": "response"
    }
    ```
    ```javascript
    context.bindings.response = { status: 201, body: "Insert succeeded." };
    ```
+ _[応答のみ]_ `context.res.send(body?: any)` の呼び出し。 HTTP の応答は、入力 `body` を応答本文として使用して作成されます。 `context.done()` は暗黙的に呼び出されます。

+ _[応答のみ]_ `context.done()` の呼び出し。 これは、`context.done()` メソッドに渡される応答を返す特殊な種類の HTTP バインディングです。 次の HTTP 出力バインディングで、`$return` 出力パラメーターを定義します。

    ```json
    {
      "type": "http",
      "direction": "out",
      "name": "$return"
    }
    ``` 
    ```javascript
     // Define a valid response object.
    res = { status: 201, body: "Insert succeeded." };
    context.done(null, res);   
    ```  

## <a name="node-version"></a>Node バージョン

次の表は、使用される Node.js バージョンを、Functions ランタイムのメジャー バージョンごとに示しています。

| Functions バージョン | Node.js バージョン | 
|---|---|
| 1.x | 6.11.2 (ランタイムによりロック) |
| 2.x  | _アクティブ LTS_ と_現在の_ Node.js のバージョン (8.11.1 と 10.6.0 を推奨)。 WEBSITE_NODE_DEFAULT_VERSION [アプリ設定](functions-how-to-use-azure-function-app-settings.md#settings)を使用してバージョンを設定します。|

ランタイムが使用している現在のバージョンを確認するには、上記のアプリ設定を調べるか、または任意の関数から `process.version` を出力します。

## <a name="dependency-management"></a>依存関係の管理
JavaScript コードでコミュニティ ライブラリを使用するには、次の例で示すように、Azure 内の関数アプリにすべての依存関係がインストールされている必要があります。

```javascript
// Import the underscore.js library
var _ = require('underscore');
var version = process.version; // version === 'v6.5.0'

module.exports = function(context) {
    // Using our imported underscore.js library
    var matched_names = _
        .where(context.bindings.myInput.names, {first: 'Carla'});
```

> [!NOTE]
> 関数アプリのルートに `package.json` ファイルを定義する必要があります。 このファイルを定義することによって、アプリのすべての関数を同じキャッシュされたパッケージで共有し、最高クラスのパフォーマンスを得ることができます。 バージョンの競合がある場合は、個別の関数のフォルダーに `package.json` ファイルを追加することで競合を解決できます。  

ソース管理から関数アプリをデプロイする場合、リポジトリ内に存在する `package.json` ファイルはすべて、デプロイ中にそのフォルダー内の `npm install` をトリガーします。 ただし、ポータルまたは CLI 経由でデプロイする場合は、パッケージを手動でインストールする必要があります。

関数アプリにパッケージをインストールするには 2 つの方法があります。 

### <a name="deploying-with-dependencies"></a>依存関係と共に展開する
1. `npm install` を実行して、すべての必要なパッケージをローカルにインストールします。

2. コードを展開し、`node_modules` フォルダーが展開に含まれることを確認します。 


### <a name="using-kudu"></a>Kudu を使用する
1. `https://<function_app_name>.scm.azurewebsites.net` にアクセスします。

2. **[デバッグ コンソール]** > **[CMD]** をクリックします。

3. `D:\home\site\wwwroot` に移動し、ページの上半分にある **wwwroot** フォルダーに package.json ファイルをドラッグします。  
    関数アプリにファイルをアップロードする方法は、他にもあります。 詳細については、「[関数アプリ ファイルを更新する方法](functions-reference.md#fileupdate)」を参照してください。 

4. package.json ファイルがアップロードされたら、**Kudu リモート実行コンソール**で `npm install` コマンドを実行します。  
    この操作によって、package.json ファイルに示されているパッケージがダウンロードされ、関数アプリが再起動されます。

## <a name="environment-variables"></a>環境変数
環境変数またはアプリの設定値を取得するには、この `GetEnvironmentVariable` 関数で示されているように、`process.env` を使用します。

```javascript
module.exports = function (context, myTimer) {
    var timeStamp = new Date().toISOString();

    context.log('Node.js timer trigger function ran!', timeStamp);   
    context.log(GetEnvironmentVariable("AzureWebJobsStorage"));
    context.log(GetEnvironmentVariable("WEBSITE_SITE_NAME"));

    context.done();
};

function GetEnvironmentVariable(name)
{
    return name + ": " + process.env[name];
}
```

## <a name="configure-function-entry-point"></a>関数のエントリ ポイントを構成する

`function.json` のプロパティ `scriptFile` と `entryPoint` を使用して、エクスポートされた関数の名前と場所を構成できます。 これらは、JavaScript がトランスパイルされる場合に重要になることがあります。

### <a name="using-scriptfile"></a>`scriptFile` を使用する

既定では、JavaScript 関数は `index.js` から実行されます。これは、対応する `function.json` と同じ親ディレクトリを共有するファイルです。

`scriptFile` を使用すると、次のようなフォルダー構造を取得できます。
```
FunctionApp
 | - host.json
 | - myNodeFunction
 | | - function.json
 | - lib
 | | - nodeFunction.js
 | - node_modules
 | | - ... packages ...
 | - package.json
```

`myNodeFunction` に対する `function.json` には、エクスポートされた関数を実行するファイルを指し示す `scriptFile` プロパティが含まれている必要があります。
```json
{
  "scriptFile": "../lib/nodeFunction.js",
  "bindings": [
    ...
  ]
}
```

### <a name="using-entrypoint"></a>`entryPoint` を使用する

`scriptFile` (または `index.js`) では、関数が発見されて実行されるためには、`module.exports` を使用して関数をエクスポートする必要があります。 既定では、トリガーされたときに実行される関数は、そのファイルからの唯一のエクスポートです (`run` という名前のエクスポート、または `index` という名前のエクスポート)。

これは、`function.json` の `entryPoint` を使用して構成することができます。
```json
{
  "entryPoint": "logFoo",
  "bindings": [
    ...
  ]
}
```

Functions v2.x では、ユーザー関数内の `this` パラメーターがサポートされており、関数のコードは次のようになります。
```javascript
class MyObj {
    constructor() {
        this.foo = 1;
    };
    
    function logFoo(context) { 
        context.log("Foo is " + this.foo); 
        context.done(); 
    }
}

const myObj = new MyObj();
module.exports = myObj;
```

この例では、オブジェクトはエクスポートされていますが、実行間で状態が保持される保証はないことに注意することが重要です。

## <a name="considerations-for-javascript-functions"></a>JavaScript 関数に関する考慮事項

JavaScript 関数を使用するときは、以下のセクションに記載されている事柄に注意する必要があります。

### <a name="choose-single-vcpu-app-service-plans"></a>シングル vCPU App Service プランを選択する

App Service プランを使用する関数アプリを作成するときは、複数の vCPU を持つプランではなく、シングル vCPU プランを選択することをお勧めします。 今日では、関数を使用して、シングル vCPU VM で JavaScript 関数をより効率的に実行できるようになりました。そのため、大規模な VM を使用しても、期待以上にパフォーマンスが向上することはありません。 必要な場合は、シングル vCPU VM インスタンスを追加することで手動でスケールアウトするか、自動スケールを有効にすることができます。 詳細については、「[手動または自動によるインスタンス数のスケール変更](../monitoring-and-diagnostics/insights-how-to-scale.md?toc=%2fazure%2fapp-service-web%2ftoc.json)」を参照してください。    

### <a name="typescript-and-coffeescript-support"></a>TypeScript と CoffeeScript のサポート
ランタイムによる TypeScript/CoffeeScript の自動コンパイルはまだ直接サポートされていません。そのため、デプロイ時にランタイムの外部ですべて処理する必要があります。 

### <a name="cold-start"></a>コールド スタート
サーバーレス ホスティング モデルで Azure 関数を開発するときは、コールド スタートが現実のものになります。 "コールド スタート" とは、非アクティブな期間の後で初めて関数アプリが起動するとき、起動に時間がかかることを意味します。 特に、大きな依存関係ツリーを持つ JavaScript 関数の場合は、これにより速度が大幅に低下する可能性があります。 プロセスを速くするには、可能であれば、[パッケージ ファイルとして関数を実行します](run-functions-from-deployment-package.md)。 多くの展開方法ではこのモデルが既定で選択されますが、大規模なコールド スタートが発生していて、パッケージ ファイルから実行していない場合は、大きな向上になる可能性があります。

## <a name="next-steps"></a>次の手順
詳細については、次のリソースを参照してください。

* [Azure Functions のベスト プラクティス](functions-best-practices.md)
* [Azure Functions 開発者向けリファレンス](functions-reference.md)
* [Azure Functions triggers and bindings (Azure Functions のトリガーとバインド)](functions-triggers-bindings.md)

