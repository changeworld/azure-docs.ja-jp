---
title: Azure Functions のトリガーとバインド
description: Azure Functions で、トリガーとバインドを使用してコード実行をオンライン イベントおよびクラウドベース サービスに接続する方法について説明します。
services: functions
documentationcenter: na
author: ggailey777
manager: cfowler
editor: ''
tags: ''
keywords: Azure Functions, 機能, イベント処理, Webhook, 動的コンピューティング, サーバーなしのアーキテクチャ
ms.service: functions
ms.devlang: multiple
ms.topic: reference
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 05/24/2018
ms.author: glenga
ms.openlocfilehash: 85b7248c9b4c61e29ce3c29b9432f94934255819
ms.sourcegitcommit: 30fd606162804fe8ceaccbca057a6d3f8c4dd56d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/30/2018
ms.locfileid: "39346604"
---
# <a name="azure-functions-triggers-and-bindings-concepts"></a>Azure Functions でのトリガーとバインドの概念

この記事では、Azure Functions のトリガーとバインドの概念的な概要を説明します。 ここでは、すべてのバインドとすべてのサポートされている言語に共通する機能について説明します。

## <a name="overview"></a>概要

*トリガー*は、関数を呼び出す方法を定義します。 1 つの関数には 1 つのトリガーしか含められません。 トリガーにはデータが関連付けられていて、通常そのデータは、その関数をトリガーしたペイロードです。

入出力*バインド*によって、コード内からデータに接続する宣言型の方法が提供されます。 バインドは省略可能で、関数は複数の入出力バインドを持つことができます。 

トリガーとバインドを使用すると、操作するサービスの詳細をハードコードする必要がなくなります。 関数は、関数パラメーターでデータ (キュー メッセージの内容など) を受信します。 関数の戻り値を使用して、(たとえば、キュー メッセージを作成するために) データを送信します。 C# と C# スクリプトでは、`out` パラメーターや[コレクター オブジェクト](functions-reference-csharp.md#writing-multiple-output-values)を使用してデータを送信できます。

Azure Portal を使用して関数を開発する場合、トリガーとバインドは *function.json* ファイルに構成されます。 ポータルではこの構成のために UI が提供されますが、**詳細エディター**に切り替えることで直接ファイルを編集できます。

Visual Studio を使用してクラス ライブラリを作成して関数を開発する場合は、メソッドとパラメーターを属性で修飾してトリガーとバインドを構成します。

## <a name="example-trigger-and-binding"></a>トリガーとバインディングの例

Azure Queue Storage に新しいメッセージが表示されるたびに Azure Table Storage に新しい行を書き込むとします。 このシナリオは、Azure Queue Storage トリガーと Azure Table Storage の出力バインドを使用して実装できます。 

このシナリオ用の *function.json* ファイルを次に示します。 

```json
{
  "bindings": [
    {
      "name": "order",
      "type": "queueTrigger",
      "direction": "in",
      "queueName": "myqueue-items",
      "connection": "MY_STORAGE_ACCT_APP_SETTING"
    },
    {
      "name": "$return",
      "type": "table",
      "direction": "out",
      "tableName": "outTable",
      "connection": "MY_TABLE_STORAGE_ACCT_APP_SETTING"
    }
  ]
}
```

`bindings` 配列の最初の要素は、Queue Storage トリガーです。 `type` および `direction` プロパティは、トリガーを識別します。 `name` プロパティは、キュー メッセージの内容を受信する関数パラメーターを識別します。 監視するキューの名前は `queueName` に含まれ、接続文字列は `connection` で識別されるアプリ設定に含まれています。

`bindings` 配列の 2 番目の要素は、Azure Table Storage の出力バインドです。 `type` および `direction` プロパティは、バインドを識別します。 `name` プロパティは、その関数が (この場合は関数戻り値を使用して) 新しいテーブル行を提供する方法を指定します。 テーブルの名前は `tableName` に含まれ、接続文字列は `connection` で識別されるアプリ設定に含まれています。

Azure ポータルで *function.json* の内容を表示して編集するには、関数の **[統合]** タブにある **[詳細エディター]** オプションをクリックします。

> [!NOTE]
> `connection` の値は、接続文字列自体ではなく、接続文字列を含むアプリ設定の名前です。 "*function.json* にサービス シークレットを含めない" というベスト プラクティスを適用するために、バインドでは、アプリ設定に格納された接続文字列を使用します。

このトリガーとバインドで動作する C# のスクリプト コードを次に示します。 キュー メッセージの内容を提供するパラメーターの名前が `order` であることに注意してください。*function.json* の `name` プロパティ値が `order` であるため、この名前が必要になります。 

```cs
#r "Newtonsoft.Json"

using Newtonsoft.Json.Linq;

// From an incoming queue message that is a JSON object, add fields and write to Table storage
// The method return value creates a new row in Table Storage
public static Person Run(JObject order, TraceWriter log)
{
    return new Person() { 
            PartitionKey = "Orders", 
            RowKey = Guid.NewGuid().ToString(),  
            Name = order["Name"].ToString(),
            MobileNumber = order["MobileNumber"].ToString() };  
}
 
public class Person
{
    public string PartitionKey { get; set; }
    public string RowKey { get; set; }
    public string Name { get; set; }
    public string MobileNumber { get; set; }
}
```

同じ function.json ファイルを JavaScript 関数でも使用できます。

```javascript
// From an incoming queue message that is a JSON object, add fields and write to Table Storage
// The second parameter to context.done is used as the value for the new row
module.exports = function (context, order) {
    order.PartitionKey = "Orders";
    order.RowKey = generateRandomId(); 

    context.done(null, order);
};

function generateRandomId() {
    return Math.random().toString(36).substring(2, 15) +
        Math.random().toString(36).substring(2, 15);
}
```

クラス ライブラリでは、同じトリガーとバインディング情報 (&mdash;キュー名とテーブル名、ストレージ アカウント、入力と出力の関数パラメーター&mdash;) が function.json ファイルではなく、属性によって提供されます。 次に例を示します。

```csharp
 public static class QueueTriggerTableOutput
 {
     [FunctionName("QueueTriggerTableOutput")]
     [return: Table("outTable", Connection = "MY_TABLE_STORAGE_ACCT_APP_SETTING")]
     public static Person Run(
         [QueueTrigger("myqueue-items", Connection = "MY_STORAGE_ACCT_APP_SETTING")]JObject order, 
         TraceWriter log)
     {
         return new Person() {
                 PartitionKey = "Orders",
                 RowKey = Guid.NewGuid().ToString(),
                 Name = order["Name"].ToString(),
                 MobileNumber = order["MobileNumber"].ToString() };
     }
 }

 public class Person
 {
     public string PartitionKey { get; set; }
     public string RowKey { get; set; }
     public string Name { get; set; }
     public string MobileNumber { get; set; }
 }
```

## <a name="supported-bindings"></a>サポートされるバインディング

[!INCLUDE [Full bindings table](../../includes/functions-bindings.md)]

どのバインディングがプレビューでどのバインディングが実稼働環境で承認されているかについては、[サポートされている言語](supported-languages.md)に関する記事をご覧ください。

## <a name="register-binding-extensions"></a>バインディング拡張機能を登録する

一部の開発環境では、明示的に使用するバインディングを*登録する*必要があります。 バインディング拡張機能は NuGet パッケージで提供されます。また、拡張機能を登録するにはパッケージをインストールします。 次の表に、バインディング拡張機能を登録するタイミングと方法を示します。

|開発環境 |登録<br/> (Functions 1.x)  |登録<br/> (Functions 2.x)  |
|---------|---------|---------|
|Azure ポータル|自動|[プロンプトで自動](#azure-portal-development)|
|Azure Functions Core Tools を使用するローカル|自動|[Core Tools CLI コマンドを使用](#local-development-azure-functions-core-tools)|
|Visual Studio 2017 を使用する C# クラス ライブラリ|[NuGet ツールを使用](#c-class-library-with-visual-studio-2017)|[NuGet ツールを使用](#c-class-library-with-visual-studio-2017)|
|Visual Studio Code を使用する C# クラス ライブラリ|該当なし|[.NET Core CLI を使用](#c-class-library-with-visual-studio-code)|

次のバインドの種類は、HTTP、タイマー、および Azure Storage (Blob、キュー、およびテーブル) のすべてのバージョンと環境に自動登録されているため、明示的な登録を必要としない例外です。 

### <a name="azure-portal-development"></a>Azure Portal 開発

このセクションは Functions 2.x にのみ適用されます。 バインディングの拡張機能は、Functions 1.x に明示的に登録する必要はありません。

関数を作成するか、またはバインディングを追加する場合に、トリガーまたはバインディングの拡張機能が登録を必要とするときは、プロンプトが表示されます。 **[インストール]** をクリックして拡張機能を登録することで、プロンプトに応答します。 従量課金プランで、インストールには最大 10 分かかる可能性があります。

各拡張機能は、特定の関数アプリごとに 1 回だけインストールする必要があります。 

### <a name="local-development-azure-functions-core-tools"></a>ローカル開発の Azure Functions Core Tools

このセクションは Functions 2.x にのみ適用されます。 バインディングの拡張機能は、Functions 1.x に明示的に登録する必要はありません。

[!INCLUDE [functions-core-tools-install-extension](../../includes/functions-core-tools-install-extension.md)]

<a name="local-csharp"></a>
### <a name="c-class-library-with-visual-studio-2017"></a>Visual Studio 2017 を使用する C# クラス ライブラリ

**Visual Studio 2017** では、次の例に示すように [Install-Package](https://docs.microsoft.com/nuget/tools/ps-ref-install-package) コマンドを使用して、Package Manager Console からパッケージをインストールできます。

```powershell
Install-Package Microsoft.Azure.WebJobs.ServiceBus --Version <target_version>
```

特定のバインディングに使用するパッケージ名は、該当のバインディングのリファレンス記事に示されています。 たとえば、[Service Bus バインディングのリファレンス記事にある「パッケージ」セクション](functions-bindings-service-bus.md#packages---functions-1x)を参照してください。

例の中の `<target_version>` を `3.0.0-beta5` などの特定のバージョンのパッケージに置き換えます。 有効なバージョンは、[NuGet.org](https://nuget.org) の個々のパッケージ ページに記載されています。Functions ランタイム 1.x または 2.x に対応する主要なバージョンは、バインデイングのリファレンス記事に示されています。

### <a name="c-class-library-with-visual-studio-code"></a>Visual Studio Code を使用する C# クラス ライブラリ

**Visual Studio Code** では、次の例のように、.NET Core CLI の [dotnet add package](https://docs.microsoft.com/dotnet/core/tools/dotnet-add-package) コマンドを使用してコマンド プロンプトからパッケージをインストールできます。

```terminal
dotnet add package Microsoft.Azure.WebJobs.ServiceBus --version <target_version>
```

.NET Core CLI は、Azure Functions 2.x 開発のみに使用できます。

特定のバインディングに使用するパッケージ名は、該当のバインディングのリファレンス記事に示されています。 たとえば、[Service Bus バインディングのリファレンス記事にある「パッケージ」セクション](functions-bindings-service-bus.md#packages---functions-1x)を参照してください。

例の中の `<target_version>` を `3.0.0-beta5` などの特定のバージョンのパッケージに置き換えます。 有効なバージョンは、[NuGet.org](https://nuget.org) の個々のパッケージ ページに記載されています。Functions ランタイム 1.x または 2.x に対応する主要なバージョンは、バインデイングのリファレンス記事に示されています。

## <a name="binding-direction"></a>バインドの方向

すべてのトリガーとバインドには、*function.json* ファイルに `direction` プロパティがあります。

- トリガーの場合、方向は常に `in` です
- 入出力バインドは `in` と `out` を使用します
- 一部のバインドは、特殊な方向の `inout` をサポートしてします。 `inout` を使用する場合、**[統合]** タブで使用できるのは**詳細エディター**のみです。

[クラス ライブラリの属性](functions-dotnet-class-library.md)を使用してトリガーとバインドを構成した場合、その方向は属性コンストラクターで提供されるか、またはパラメーター型から推論されます。

## <a name="using-the-function-return-value"></a>関数戻り値の使用

戻り値がある言語では、その戻り値に出力バインディングをバインドできます。

* C# クラス ライブラリでは、メソッド戻り値に出力バインディング属性を適用します。
* その他の言語では、*function.json* 内の `name` プロパティを `$return` に設定します。

複数の出力バインディングが存在する場合は、そのうちの 1 つにのみ戻り値を使用します。

C# と C# スクリプトでは、`out` パラメーターや[コレクター オブジェクト](functions-reference-csharp.md#writing-multiple-output-values)を使用してデータを出力バインディングに送信できます。

言語固有の戻り値の使用例を次に示します。

* [C#](#c-example)
* [C# スクリプト (.csx)](#c-script-example)
* [F#](#f-example)
* [JavaScript](#javascript-example)

### <a name="c-example"></a>C# の例

出力バインディングに戻り値を使用する C# コードと非同期の例を次に示します。

```cs
[FunctionName("QueueTrigger")]
[return: Blob("output-container/{id}")]
public static string Run([QueueTrigger("inputqueue")]WorkItem input, TraceWriter log)
{
    string json = string.Format("{{ \"id\": \"{0}\" }}", input.Id);
    log.Info($"C# script processed queue message. Item={json}");
    return json;
}
```

```cs
[FunctionName("QueueTrigger")]
[return: Blob("output-container/{id}")]
public static Task<string> Run([QueueTrigger("inputqueue")]WorkItem input, TraceWriter log)
{
    string json = string.Format("{{ \"id\": \"{0}\" }}", input.Id);
    log.Info($"C# script processed queue message. Item={json}");
    return Task.FromResult(json);
}
```

### <a name="c-script-example"></a>C# スクリプトの例

*function.json* ファイル内の出力バインディングを次に示します。

```json
{
    "name": "$return",
    "type": "blob",
    "direction": "out",
    "path": "output-container/{id}"
}
```

C# スクリプト コードと非同期の例を次に示します。

```cs
public static string Run(WorkItem input, TraceWriter log)
{
    string json = string.Format("{{ \"id\": \"{0}\" }}", input.Id);
    log.Info($"C# script processed queue message. Item={json}");
    return json;
}
```

```cs
public static Task<string> Run(WorkItem input, TraceWriter log)
{
    string json = string.Format("{{ \"id\": \"{0}\" }}", input.Id);
    log.Info($"C# script processed queue message. Item={json}");
    return Task.FromResult(json);
}
```

### <a name="f-example"></a>F# の例

*function.json* ファイル内の出力バインディングを次に示します。

```json
{
    "name": "$return",
    "type": "blob",
    "direction": "out",
    "path": "output-container/{id}"
}
```

F# コードを次に示します。

```fsharp
let Run(input: WorkItem, log: TraceWriter) =
    let json = String.Format("{{ \"id\": \"{0}\" }}", input.Id)   
    log.Info(sprintf "F# script processed queue message '%s'" json)
    json
```

### <a name="javascript-example"></a>JavaScript の例

*function.json* ファイル内の出力バインディングを次に示します。

```json
{
    "name": "$return",
    "type": "blob",
    "direction": "out",
    "path": "output-container/{id}"
}
```

JavaScript では、戻り値は `context.done` の 2 番目のパラメーターに入ります。

```javascript
module.exports = function (context, input) {
    var json = JSON.stringify(input);
    context.log('Node.js script processed queue message', json);
    context.done(null, json);
}
```

## <a name="binding-datatype-property"></a>dataType プロパティのバインド

.NET では、パラメーター型を使用して、入力データのデータ型を定義します。 たとえば、キュー トリガーのテキストにバインドするには `string` を、バイナリとして読み取るにはバイト配列を、POCO オブジェクトを逆シリアル化するにはカスタム型を使用します。

JavaScript などの動的に型指定される言語の場合は、*function.json* ファイルの `dataType` プロパティを使用します。 たとえば、バイナリ形式で HTTP 要求のコンテンツを読み取るには、`dataType` を `binary` に設定します。

```json
{
    "type": "httpTrigger",
    "name": "req",
    "direction": "in",
    "dataType": "binary"
}
```

`dataType` のその他のオプションは、`stream` と `string` です。

## <a name="binding-expressions-and-patterns"></a>バインド式とパターン

トリガーとバインドの最も強力な機能の 1 つは、*バインド式*です。 *function.json* ファイルおよび関数パラメーターとコードでは、さまざまなソースの値に解決される式を使用できます。

ほとんどの式は、それを中かっこで囲むことによって識別されます。 たとえば、キュー トリガー関数では、`{queueTrigger}` はキュー メッセージ テキストに解決されます。 BLOB 出力バインディングの `path` プロパティが `container/{queueTrigger}` であり、その関数がキュー メッセージ `HelloWorld` によってトリガーされる場合は、`HelloWorld` という名前の BLOB が作成されます。

バインディング式の種類

* [アプリ設定](#binding-expressions---app-settings)
* [トリガー ファイル名](#binding-expressions---trigger-file-name)
* [トリガー メタデータ](#binding-expressions---trigger-metadata)
* [JSON ペイロード](#binding-expressions---json-payloads)
* [新しい GUID](#binding-expressions---create-guids)
* [現在の日付と時刻](#binding-expressions---current-time)

### <a name="binding-expressions---app-settings"></a>バインディング式 - アプリ設定

ベスト プラクティスとしては、シークレットや接続文字列は、構成ファイルではなくアプリ設定を使用して管理する必要があります。 これにより、これらのシークレットへのアクセスが制限され、*function.json* などのファイルをパブリックなソース管理リポジトリに格納することが安全になります。

環境に基づいて構成を変更するときには、アプリ設定も常に役立ちます。 たとえば、テスト環境で、別のキューや Blob Storage コンテナーを監視することもできます。

アプリ設定のバインディング式は、他のバインディング式とはさまざまに区別されます。これらは中かっこではなく、パーセント記号で囲まれます。 たとえば、BLOB 出力バインディング パスが `%Environment%/newblob.txt` であり、`Environment` アプリ設定値が `Development` である場合は、BLOB が `Development` コンテナー内に作成されます。

関数がローカルに実行されている場合、アプリ設定値は *local.settings.json* ファイルから来ます。

トリガーとバインディングの `connection` プロパティは特殊なケースであり、値をアプリ設定 (パーセント記号なし) として自動的に解決することに注意してください。 

次の例は、アプリ設定 `%input-queue-name%` を使用してトリガーの対象となるキューを定義する Azure Queue Storage トリガーです。

```json
{
  "bindings": [
    {
      "name": "order",
      "type": "queueTrigger",
      "direction": "in",
      "queueName": "%input-queue-name%",
      "connection": "MY_STORAGE_ACCT_APP_SETTING"
    }
  ]
}
```

クラス ライブラリでも同じ方法を使用できます。

```csharp
[FunctionName("QueueTrigger")]
public static void Run(
    [QueueTrigger("%input-queue-name%")]string myQueueItem, 
    TraceWriter log)
{
    log.Info($"C# Queue trigger function processed: {myQueueItem}");
}
```

### <a name="binding-expressions---trigger-file-name"></a>バインディング式 - トリガー ファイル名

BLOB トリガーの `path` は、他のバインディングや関数コード内のトリガー BLOB の名前を参照するために使用できるパターンにすることができます。 このパターンにはまた、どの BLOB が関数呼び出しをトリガーできるかを指定するフィルター条件を含めることもできます。

たとえば、次の BLOB トリガーのバインディングでは、`path` パターンは `sample-images/{filename}` です。これは、`filename` という名前のバインディング式を作成します。

```json
{
  "bindings": [
    {
      "name": "image",
      "type": "blobTrigger",
      "path": "sample-images/{filename}",
      "direction": "in",
      "connection": "MyStorageConnection"
    },
    ...
```

その後、出力バインディングで式 `filename` を使用して、作成される BLOB の名前を指定できます。

```json
    ...
    {
      "name": "imageSmall",
      "type": "blob",
      "path": "sample-images-sm/{filename}",
      "direction": "out",
      "connection": "MyStorageConnection"
    }
  ],
}
```

関数コードは、`filename` をパラメーター名として使用して、この同じ値にアクセスできます。

```csharp
// C# example of binding to {filename}
public static void Run(Stream image, string filename, Stream imageSmall, TraceWriter log)  
{
    log.Info($"Blob trigger processing: {filename}");
    // ...
} 
```

<!--TODO: add JavaScript example -->
<!-- Blocked by bug https://github.com/Azure/Azure-Functions/issues/248 -->

バインド式とパターンを使用するのと同じ機能がクラス ライブラリの属性に適用されます。 次の例では、属性コンストラクター パラメーターは前の *function.json* の例と同じ `path` 値です。 

```csharp
[FunctionName("ResizeImage")]
public static void Run(
    [BlobTrigger("sample-images/{filename}")] Stream image,
    [Blob("sample-images-sm/{filename}", FileAccess.Write)] Stream imageSmall,
    string filename,
    TraceWriter log)
{
    log.Info($"Blob trigger processing: {filename}");
    // ...
}

```

また、拡張機能などのファイル名の一部のための式を作成することもできます。 式を使用する方法および BLOB パス文字列内のパターンの詳細については、[ストレージ BLOB バインディングのリファレンス](functions-bindings-storage-blob.md)に関するページを参照してください。
 
### <a name="binding-expressions---trigger-metadata"></a>バインディング式 - トリガー メタデータ

トリガーによって提供されるデータ ペイロード (関数をトリガーしたキュー メッセージの内容など) に加え、多くのトリガーは追加のメタデータ値を提供します。 これらの値は、C# および F# で入力パラメーターとして使用したり、JavaScript で `context.bindings` オブジェクトのプロパティとして使用したりできます。 

たとえば、Azure Queue Storage トリガーは、以下のプロパティをサポートしています。

* QueueTrigger - 有効な文字列の場合はメッセージの内容をトリガーします
* DequeueCount
* ExpirationTime
* ID
* InsertionTime
* NextVisibleTime
* PopReceipt

これらのメタデータ値は、*function.json* ファイルのプロパティでアクセスできます。 たとえば、キュー トリガーを使用していて、読み取る BLOB の名前がキュー メッセージに含まれているとします。 次の例に示すように、*function.json* ファイルで、BLOB `path` プロパティの `queueTrigger` メタデータ プロパティを使用できます。

```json
  "bindings": [
    {
      "name": "myQueueItem",
      "type": "queueTrigger",
      "queueName": "myqueue-items",
      "connection": "MyStorageConnection",
    },
    {
      "name": "myInputBlob",
      "type": "blob",
      "path": "samples-workitems/{queueTrigger}",
      "direction": "in",
      "connection": "MyStorageConnection"
    }
  ]
```

各トリガーのメタデータ プロパティの詳細については、対応するリファレンス記事を参照してください。 例については、[キュー トリガー メタデータ](functions-bindings-storage-queue.md#trigger---message-metadata)に関するセクションを参照してください。 ドキュメントは、ポータルの **[統合]** タブの、バインド構成領域の下の **[ドキュメント]** セクションでも参照できます。  

### <a name="binding-expressions---json-payloads"></a>バインディング式 - JSON ペイロード

トリガー ペイロードが JSON である場合は、同じ関数および関数コードで、他のバインディング用の構成内のそのプロパティを参照できます。

次の例は、JSON での BLOB 名を受信する Webhook 関数の *function.json* ファイルを示しています: `{"BlobName":"HelloWorld.txt"}`。 BLOB 入力バインディングが BLOB を読み取り、HTTP 出力バインディングが HTTP 応答で BLOB コンテンツを返します。 BLOB 入力バインディングが `BlobName` プロパティ (`"path": "strings/{BlobName}"`) を直接参照することによって BLOB 名を取得していることに注意してください。

```json
{
  "bindings": [
    {
      "name": "info",
      "type": "httpTrigger",
      "direction": "in",
      "webHookType": "genericJson"
    },
    {
      "name": "blobContents",
      "type": "blob",
      "direction": "in",
      "path": "strings/{BlobName}",
      "connection": "AzureWebJobsStorage"
    },
    {
      "name": "res",
      "type": "http",
      "direction": "out"
    }
  ]
}
```

C# および F# でこれが機能するには、次の例に示すように、逆シリアル化されるフィールドを定義するクラスが必要です。

```csharp
using System.Net;

public class BlobInfo
{
    public string BlobName { get; set; }
}
  
public static HttpResponseMessage Run(HttpRequestMessage req, BlobInfo info, string blobContents, TraceWriter log)
{
    if (blobContents == null) {
        return req.CreateResponse(HttpStatusCode.NotFound);
    } 

    log.Info($"Processing: {info.BlobName}");

    return req.CreateResponse(HttpStatusCode.OK, new {
        data = $"{blobContents}"
    });
}
```

JavaScript では、JSON の逆シリアル化が自動的に実行されます。

```javascript
module.exports = function (context, info) {
    if ('BlobName' in info) {
        context.res = {
            body: { 'data': context.bindings.blobContents }
        }
    }
    else {
        context.res = {
            status: 404
        };
    }
    context.done();
}
```

#### <a name="dot-notation"></a>ドット表記

JSON ペイロード内のいくつかのプロパティがプロパティを持つオブジェクトである場合は、ドット表記を使用してこれらを直接参照できます。 たとえば、次のような JSON があるとします。

```json
{"BlobName": {
  "FileName":"HelloWorld",
  "Extension":"txt"
  }
}
```

`FileName` を `BlobName.FileName` として直接参照できます。 この JSON 形式では、前の例の `path` プロパティは次のようになります。

```json
"path": "strings/{BlobName.FileName}.{BlobName.Extension}",
```

C# では、次の 2 つのクラスが必要になります。

```csharp
public class BlobInfo
{
    public BlobName BlobName { get; set; }
}
public class BlobName
{
    public string FileName { get; set; }
    public string Extension { get; set; }
}
```

### <a name="binding-expressions---create-guids"></a>バインディング式 - GUID を作成する

`{rand-guid}` バインド式は GUID を作成します。 `function.json` ファイル内の次の BLOB パスは、*50710cb5-84b9-4d87-9d83-a03d6976a682.txt* などの名前を持つ BLOB を作成します。

```json
{
  "type": "blob",
  "name": "blobOutput",
  "direction": "out",
  "path": "my-output-container/{rand-guid}"
}
```

### <a name="binding-expressions---current-time"></a>バインディング式 - 現在の時刻

バインド式 `DateTime` は `DateTime.UtcNow` に解決されます。 `function.json` ファイル内の次の BLOB パスは、*2018-02-16T17-59-55Z.txt* などの名前を持つ BLOB を作成します。

```json
{
  "type": "blob",
  "name": "blobOutput",
  "direction": "out",
  "path": "my-output-container/{DateTime}"
}
```

## <a name="binding-at-runtime"></a>実行時のバインド

C# やその他の .NET 言語では、*function.json* の宣言型のバインドと属性ではなく、命令型のバインド パターンを使用できます。 命令型のバインドは、設計時ではなくランタイム時にバインド パラメーターを計算する必要がある場合に便利です。 詳細については、[C# 開発者向けリファレンス](functions-dotnet-class-library.md#binding-at-runtime)または[C# スクリプト開発者向けリファレンス](functions-reference-csharp.md#binding-at-runtime)を参照してください。

## <a name="functionjson-file-schema"></a>function.json ファイル スキーマ

*function.json* ファイル スキーマは [http://json.schemastore.org/function](http://json.schemastore.org/function) から入手できます。

## <a name="handling-binding-errors"></a>バインド エラーの処理

[!INCLUDE [bindings errors intro](../../includes/functions-bindings-errors-intro.md)]

Functions でサポートされているさまざまなサービスに関連するすべてのエラー トピックへのリンクについては、概要トピック「[Azure Functions error handling (Azure Functions のエラー処理)](functions-bindings-error-pages.md)」の「[Binding error codes (バインド エラー コード)](functions-bindings-error-pages.md#binding-error-codes)」セクションを参照してください。  

## <a name="next-steps"></a>次の手順

特定のバインドの詳細については、以下の記事を参照してください。

- [HTTP と webhook](functions-bindings-http-webhook.md)
- [Timer](functions-bindings-timer.md)
- [Queue Storage](functions-bindings-storage-queue.md)
- [Blob Storage](functions-bindings-storage-blob.md)
- [Table Storage](functions-bindings-storage-table.md)
- [イベント ハブ](functions-bindings-event-hubs.md)
- [Service Bus](functions-bindings-service-bus.md)
- [Azure Cosmos DB](functions-bindings-cosmosdb.md)
- [Microsoft Graph](functions-bindings-microsoft-graph.md)
- [SendGrid](functions-bindings-sendgrid.md)
- [Twilio](functions-bindings-twilio.md)
- [Notification Hubs](functions-bindings-notification-hubs.md)
- [Mobile Apps](functions-bindings-mobile-apps.md)
- [外部ファイル](functions-bindings-external-file.md)
