---
title: "Azure Functions のトリガーとバインド"
description: "Azure Functions で、トリガーとバインドを使用してコード実行をオンライン イベントおよびクラウドベース サービスに接続する方法について説明します。"
services: functions
documentationcenter: na
author: ggailey777
manager: cfowler
editor: 
tags: 
keywords: "Azure Functions, 機能, イベント処理, Webhook, 動的コンピューティング, サーバーなしのアーキテクチャ"
ms.service: functions
ms.devlang: multiple
ms.topic: reference
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 11/21/2017
ms.author: glenga
ms.openlocfilehash: a122271b5fdffd9db33a7dca5908e15f002041d7
ms.sourcegitcommit: 71fa59e97b01b65f25bcae318d834358fea5224a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/11/2018
---
# <a name="azure-functions-triggers-and-bindings-concepts"></a>Azure Functions でのトリガーとバインドの概念

この記事では、Azure Functions のトリガーとバインドの概念的な概要を説明します。 ここでは、すべてのバインドとすべてのサポートされている言語に共通する機能について説明します。

## <a name="overview"></a>概要

*トリガー*は、関数を呼び出す方法を定義します。 1 つの関数には 1 つのトリガーしか含められません。 トリガーにはデータが関連付けられていて、通常そのデータは、その関数をトリガーしたペイロードです。

入出力*バインド*によって、コード内からデータに接続する宣言型の方法が提供されます。 バインドは省略可能で、関数は複数の入出力バインドを持つことができます。 

トリガーとバインドを使用すると、操作するサービスの詳細をハードコードする必要がなくなります。 関数は、関数パラメーターでデータ (キュー メッセージの内容など) を受け取ります。 関数の戻り値、`out` パラメーター、または[コレクター オブジェクト](functions-reference-csharp.md#writing-multiple-output-values)を使用して、(たとえば、キュー メッセージを作成するために) データを送信します。

Azure Portal を使用して関数を開発する場合、トリガーとバインドは *function.json* ファイルに構成されます。 ポータルではこの構成のために UI が提供されますが、**詳細エディター**に切り替えることで直接ファイルを編集できます。

Visual Studio を使用してクラス ライブラリを作成して関数を開発する場合は、メソッドとパラメーターを属性で修飾してトリガーとバインドを構成します。

## <a name="supported-bindings"></a>サポートされるバインディング

[!INCLUDE [Full bindings table](../../includes/functions-bindings.md)]

どのバインディングがプレビューでどのバインディングが実稼働環境で承認されているかについては、[サポートされている言語](supported-languages.md)に関する記事をご覧ください。

## <a name="example-queue-trigger-and-table-output-binding"></a>例: キュー トリガーとテーブルの出力バインド

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

`bindings` 配列の最初の要素は、Queue Storage トリガーです。 `type` および `direction` プロパティは、トリガーを識別します。 `name` プロパティは、キュー メッセージの内容を受け取る関数パラメーターを識別します。 監視するキューの名前は `queueName` に含まれ、接続文字列は `connection` で識別されるアプリ設定に含まれています。

`bindings` 配列の 2 番目の要素は、Azure Table Storage の出力バインドです。 `type` および `direction` プロパティは、バインドを識別します。 `name` プロパティは、関数が新しいテーブル行を提供する方法を指定します。ここでは、関数の戻り値を使用します。 テーブルの名前は `tableName` に含まれ、接続文字列は `connection` で識別されるアプリ設定に含まれています。

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

クラス ライブラリでは、同じトリガーとバインド情報 (キュー名とテーブル名、ストレージ アカウント、入力と出力の関数パラメーター) が属性によって提供されます。

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

## <a name="binding-direction"></a>バインドの方向

すべてのトリガーとバインドには、*function.json* ファイルに `direction` プロパティがあります。

- トリガーの場合、方向は常に `in` です
- 入出力バインドは `in` と `out` を使用します
- 一部のバインドは、特殊な方向の `inout` をサポートしてします。 `inout` を使用する場合、**[統合]** タブで使用できるのは**詳細エディター**のみです。

[クラス ライブラリの属性](functions-dotnet-class-library.md)を使用してトリガーとバインドを構成した場合、その方向は属性コンストラクターで提供されるか、またはパラメーター型から推論されます。

## <a name="using-the-function-return-type-to-return-a-single-output"></a>単一出力を返すための関数の戻り値の型の使用

前の例は、関数の戻り値を使用してバインドに出力を提供する方法を示しています。これは、`name` プロパティの特殊値 `$return` を使用して *function.json* に指定されています  (これは、C# スクリプト、JavaScript、F# などの、戻り値がある言語でのみサポートされています)。1 つの関数に複数の出力バインドがある場合は、1 つの出力バインドに対してのみ `$return` を使用します。 

```json
// excerpt of function.json
{
    "name": "$return",
    "type": "blob",
    "direction": "out",
    "path": "output-container/{id}"
}
```

以下の例は、C# スクリプト、JavaScript、および F# で、戻り値の型が出力バインドでどのように使用されるかを示しています。

```cs
// C# example: use method return value for output binding
public static string Run(WorkItem input, TraceWriter log)
{
    string json = string.Format("{{ \"id\": \"{0}\" }}", input.Id);
    log.Info($"C# script processed queue message. Item={json}");
    return json;
}
```

```cs
// C# example: async method, using return value for output binding
public static Task<string> Run(WorkItem input, TraceWriter log)
{
    string json = string.Format("{{ \"id\": \"{0}\" }}", input.Id);
    log.Info($"C# script processed queue message. Item={json}");
    return Task.FromResult(json);
}
```

```javascript
// JavaScript: return a value in the second parameter to context.done
module.exports = function (context, input) {
    var json = JSON.stringify(input);
    context.log('Node.js script processed queue message', json);
    context.done(null, json);
}
```

```fsharp
// F# example: use return value for output binding
let Run(input: WorkItem, log: TraceWriter) =
    let json = String.Format("{{ \"id\": \"{0}\" }}", input.Id)   
    log.Info(sprintf "F# script processed queue message '%s'" json)
    json
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

## <a name="resolving-app-settings"></a>アプリケーション設定の解決

ベスト プラクティスとしては、シークレットや接続文字列は、構成ファイルではなくアプリ設定を使用して管理する必要があります。 これにより、これらのシークレットへのアクセスが制限され、パブリックなソース管理レポジトリに *function.json* を安全に格納できるようにします。

環境に基づいて構成を変更するときには、アプリ設定も常に役立ちます。 たとえば、テスト環境で、別のキューや Blob Storage コンテナーを監視することもできます。

アプリ設定は、`%MyAppSetting%` のように値がパーセント記号で囲まれたときには常に解決されます。 トリガーやバインドの `connection` プロパティは特殊ケースであり、値はアプリ設定として自動的に解決されることに注意してください。 

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

## <a name="trigger-metadata-properties"></a>トリガーのメタデータ プロパティ

トリガーによって提供されるデータ ペイロード (関数をトリガーしたキュー メッセージなど) に加え、多くのトリガーは追加のメタデータ値を提供します。 これらの値は、C# および F# で入力パラメーターとして使用したり、JavaScript で `context.bindings` オブジェクトのプロパティとして使用したりできます。 

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

## <a name="binding-expressions-and-patterns"></a>バインド式とパターン

トリガーとバインドの最も強力な機能の 1 つは、*バインド式*です。 バインドの構成にパターン式を定義して、それを他のバインドやコードで使用することができます。 トリガーのメタデータは、前のセクションに示したように、バインド式でも使用できます。

たとえば、Azure Portal の **[新しい関数]** ページの **Image Resizer** テンプレートのように、特定の Blob Storage コンテナー内のイメージのサイズを変更するとします (**サンプル** シナリオを参照してください)。 

*function.json* の定義は次のようになります。

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

`filename` パラメーターは、BLOB トリガーの定義と BLOB 出力バインドの両方で使用されることに注意してください。 このパラメーターは、関数コードでも使用できます。

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

バインド式とパターンを使用するのと同じ機能がクラス ライブラリの属性に適用されます。 たとえば、クラス ライブラリ内のイメージ サイズ変更関数を次に示します。

```csharp
[FunctionName("ResizeImage")]
[StorageAccount("AzureWebJobsStorage")]
public static void Run(
    [BlobTrigger("sample-images/{name}")] Stream image, 
    [Blob("sample-images-sm/{name}", FileAccess.Write)] Stream imageSmall, 
    [Blob("sample-images-md/{name}", FileAccess.Write)] Stream imageMedium)
{
    var imageBuilder = ImageResizer.ImageBuilder.Current;
    var size = imageDimensionsTable[ImageSize.Small];

    imageBuilder.Build(image, imageSmall,
        new ResizeSettings(size.Item1, size.Item2, FitMode.Max, null), false);

    image.Position = 0;
    size = imageDimensionsTable[ImageSize.Medium];

    imageBuilder.Build(image, imageMedium,
        new ResizeSettings(size.Item1, size.Item2, FitMode.Max, null), false);
}

public enum ImageSize { ExtraSmall, Small, Medium }

private static Dictionary<ImageSize, (int, int)> imageDimensionsTable = new Dictionary<ImageSize, (int, int)>() {
    { ImageSize.ExtraSmall, (320, 200) },
    { ImageSize.Small,      (640, 400) },
    { ImageSize.Medium,     (800, 600) }
};
```

### <a name="create-guids"></a>GUID の作成

`{rand-guid}` バインド式は GUID を作成します。 次の例では、GUID を使用して一意の BLOB 名を作成しています。 

```json
{
  "type": "blob",
  "name": "blobOutput",
  "direction": "out",
  "path": "my-output-container/{rand-guid}"
}
```

### <a name="current-time"></a>現在の時刻

バインド式 `DateTime` は `DateTime.UtcNow` に解決されます。

```json
{
  "type": "blob",
  "name": "blobOutput",
  "direction": "out",
  "path": "my-output-container/{DateTime}"
}
```

## <a name="bind-to-custom-input-properties"></a>カスタム入力プロパティへのバインド

バインド式は、トリガー ペイロード自体に定義されているプロパティも参照できます。 たとえば、webhook で提供されるファイル名から Blob Storage ファイルに動的にバインドすることもできます。

たとえば、次の *function.json* では、トリガー ペイロードの `BlobName` という名前のプロパティを使用しています。

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

C# および F# でこれを実現するには、トリガー ペイロードで逆シリアル化されるフィールドを定義する POCO を定義する必要があります。

```csharp
using System.Net;

public class BlobInfo
{
    public string BlobName { get; set; }
}
  
public static HttpResponseMessage Run(HttpRequestMessage req, BlobInfo info, string blobContents)
{
    if (blobContents == null) {
        return req.CreateResponse(HttpStatusCode.NotFound);
    } 

    return req.CreateResponse(HttpStatusCode.OK, new {
        data = $"{blobContents}"
    });
}
```

JavaScript では、JSON のシリアル化解除は自動的に実行され、プロパティを直接使用することができます。

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

## <a name="configuring-binding-data-at-runtime"></a>実行時のデータ バインドの構成

C# やその他の .NET 言語では、*function.json* の宣言型のバインドと属性ではなく、命令型のバインド パターンを使用できます。 命令型のバインドは、設計時ではなくランタイム時にバインド パラメーターを計算する必要がある場合に便利です。 詳細については、C# 開発者用リファレンスの[命令型のバインドによる実行時のバインド](functions-reference-csharp.md#imperative-bindings)に関する記事をご覧ください。

## <a name="functionjson-file-schema"></a>function.json ファイル スキーマ

*function.json* ファイル スキーマは [http://json.schemastore.org/function](http://json.schemastore.org/function) から入手できます。

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
