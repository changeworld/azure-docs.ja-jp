---
title: "Azure Functions における Blob Storage のバインディング"
description: "Azure Functions で Azure Blob Storage のトリガーとバインディングを使用する方法について説明します。"
services: functions
documentationcenter: na
author: ggailey777
manager: cfowler
editor: 
tags: 
keywords: "Azure Functions, 関数, イベント処理, 動的コンピューティング, サーバーなしのアーキテクチャ"
ms.service: functions
ms.devlang: multiple
ms.topic: reference
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 10/27/2017
ms.author: glenga
ms.openlocfilehash: 31a2fa3d3c87c16109514b130c95e731f401f8bd
ms.sourcegitcommit: c7215d71e1cdeab731dd923a9b6b6643cee6eb04
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/17/2017
---
# <a name="azure-functions-blob-storage-bindings"></a>Azure Functions における Blob Storage のバインディング

この記事では、Azure Functions で Azure Blob Storage のバインディングを操作する方法について説明します。 Azure Functions は、BLOB のトリガー、入力、出力のバインディングをサポートしています。

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

> [!NOTE]
> [BLOB のみのストレージ アカウント](../storage/common/storage-create-storage-account.md#blob-storage-accounts)はサポートされていません。 Blob ストレージのトリガーとバインドには、汎用のストレージ アカウントが必要です。 

## <a name="blob-storage-trigger"></a>Blob Storage のトリガー

Blob Storage のトリガーを使用して、新しいまたは更新された BLOB が検出されたときに関数を開始します。 BLOB のコンテンツは、関数への入力として提供されます。

> [!NOTE]
> 従量課金プランで BLOB トリガーを使用していると、関数アプリがアイドル状態になったあと、新しい BLOB の処理が最大で 10 分遅延する場合があります。 関数アプリが実行されると、BLOB は直ちに処理されます。 この初期段階の遅延を避けるために、次のオプションのいずれかを検討してください。
> - 常時接続が有効な App Service プランを使用する。
> - BLOB 名を含むキュー メッセージなど、別のメカニズムを使用して BLOB 処理をトリガーする。 例については、[この記事で後述する BLOB 入力/出力バインディングの例](#input--output---example)をご覧ください。

## <a name="trigger---example"></a>トリガー - 例

言語固有の例をご覧ください。

* [プリコンパイル済み C#](#trigger---c-example)
* [C# スクリプト](#trigger---c-script-example)
* [JavaScript](#trigger---javascript-example)

### <a name="trigger---c-example"></a>トリガー - C# の例

次の例は、BLOB が `samples-workitems` コンテナーで追加または更新されたときにログを書き込む[プリコンパイル済み C#](functions-dotnet-class-library.md) コードを示しています。

```csharp
[FunctionName("BlobTriggerCSharp")]        
public static void Run([BlobTrigger("samples-workitems/{name}")] Stream myBlob, string name, TraceWriter log)
{
    log.Info($"C# Blob trigger function Processed blob\n Name:{name} \n Size: {myBlob.Length} Bytes");
}
```

`BlobTrigger` 属性について詳しくは、「[トリガー - プリコンパイル済み C# の属性](#trigger---attributes-for-precompiled-c)」をご覧ください。

### <a name="trigger---c-script-example"></a>トリガー - C# スクリプトの例

次の例は、*function.json* ファイルの BLOB トリガー バインディングと、バインディングを使用する [C# スクリプト](functions-reference-csharp.md) コードを示しています。 関数は、`samples-workitems` コンテナーで BLOB が追加または更新されたときにログを書き込みます。

*function.json* ファイルのバインディング データを次に示します。

```json
{
    "disabled": false,
    "bindings": [
        {
            "name": "myBlob",
            "type": "blobTrigger",
            "direction": "in",
            "path": "samples-workitems",
            "connection":"MyStorageAccountAppSetting"
        }
    ]
}
```

[構成](#trigger---configuration)のセクションで、これらのプロパティについて説明します。

`Stream` にバインドする C# スクリプト コードを次に示します。

```cs
public static void Run(Stream myBlob, TraceWriter log)
{
   log.Info($"C# Blob trigger function Processed blob\n Name:{name} \n Size: {myBlob.Length} Bytes");
}
```

`CloudBlockBlob` にバインドする C# スクリプト コードを次に示します。

```cs
#r "Microsoft.WindowsAzure.Storage"

using Microsoft.WindowsAzure.Storage.Blob;

public static void Run(CloudBlockBlob myBlob, string name, TraceWriter log)
{
    log.Info($"C# Blob trigger function Processed blob\n Name:{name}\nURI:{myBlob.StorageUri}");
}
```

### <a name="trigger---javascript-example"></a>トリガー - JavaScript の例

次の例は、*function.json* ファイルの BLOB トリガー バインディングと、バインディングを使用する [JavaScript コード] (functions-reference-node.md) を示しています。 関数は、`samples-workitems` コンテナーで BLOB が追加または更新されたときにログを書き込みます。

*function.json* ファイルを次に示します。

```json
{
    "disabled": false,
    "bindings": [
        {
            "name": "myBlob",
            "type": "blobTrigger",
            "direction": "in",
            "path": "samples-workitems",
            "connection":"MyStorageAccountAppSetting"
        }
    ]
}
```

これらのプロパティについては、「[構成](#trigger---configuration)」セクションを参照してください。

JavaScript コードを次に示します。

```javascript
module.exports = function(context) {
    context.log('Node.js Blob trigger function processed', context.bindings.myBlob);
    context.done();
};
```

## <a name="trigger---attributes-for-precompiled-c"></a>トリガー - プリコンパイル済み C# の属性

[プリコンパイル済み C#](functions-dotnet-class-library.md) 関数では、次の属性を使用して BLOB トリガーを構成します。

* NuGet パッケージ [Microsoft.Azure.WebJobs](http://www.nuget.org/packages/Microsoft.Azure.WebJobs) で定義された [BlobTriggerAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/BlobTriggerAttribute.cs)

  属性のコンストラクターは、監視するコンテナーを示すパス文字列と、必要に応じて [BLOB 名のパターン](#trigger---blob-name-patterns)を受け取ります。 次に例を示します。

  ```csharp
  [FunctionName("ResizeImage")]
  public static void Run(
      [BlobTrigger("sample-images/{name}")] Stream image, 
      [Blob("sample-images-md/{name}", FileAccess.Write)] Stream imageSmall)
  ```

  次の例で示すように、`Connection` プロパティを設定して、使用するストレージ アカウントを指定できます。

   ```csharp
  [FunctionName("ResizeImage")]
  public static void Run(
      [BlobTrigger("sample-images/{name}", Connection = "StorageConnectionAppSetting")] Stream image, 
      [Blob("sample-images-md/{name}", FileAccess.Write)] Stream imageSmall)
  ```

* NuGet パッケージ [Microsoft.Azure.WebJobs](http://www.nuget.org/packages/Microsoft.Azure.WebJobs) で定義された [StorageAccountAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/StorageAccountAttribute.cs)

  使用するストレージ アカウントを指定する別の方法を提供します。 コンストラクターは、ストレージ接続文字列を含むアプリ設定の名前を受け取ります。 属性は、パラメーター、メソッド、またはクラス レベルで適用できます。 次の例では、クラス レベルとメソッド レベルを示します。

  ```csharp
  [StorageAccount("ClassLevelStorageAppSetting")]
  public static class AzureFunctions
  {
      [FunctionName("BlobTrigger")]
      [StorageAccount("FunctionLevelStorageAppSetting")]
      public static void Run( //...
  ```

使用するストレージ アカウントは、次の順序で決定されます。

* `BlobTrigger` 属性の `Connection` プロパティ。
* `BlobTrigger` 属性と同じパラメーターに適用された `StorageAccount` 属性。
* 関数に適用される `StorageAccount` 属性。
* クラスに適用される `StorageAccount` 属性。
* 関数アプリの既定のストレージ アカウント ("AzureWebJobsStorage" アプリ設定)。

## <a name="trigger---configuration"></a>トリガー - 構成

次の表は、*function.json* ファイルと `BlobTrigger` 属性で設定したバインド構成のプロパティを説明しています。

|function.json のプロパティ | 属性のプロパティ |説明|
|---------|---------|----------------------|
|**type** | 該当なし | `blobTrigger` に設定する必要があります。 このプロパティは、Azure Portal でトリガーを作成するときに自動で設定されます。|
|**direction** | 該当なし | `in` に設定する必要があります。 このプロパティは、Azure Portal でトリガーを作成するときに自動で設定されます。 例外は、[使用方法](#trigger---usage)のセクションに記載しています。 |
|**name** | 該当なし | 関数コード内の BLOB を表す変数の名前。 | 
|**path** | **BlobPath** |監視するコンテナー。  [BLOB 名パターン](#trigger-blob-name-patterns)の場合があります。 | 
|**connection** | **Connection** | このバインドに使用するストレージ接続文字列を含むアプリ設定の名前です。 アプリ設定の名前が "AzureWebJobs" で始まる場合は、ここで名前の残りの部分のみを指定できます。 たとえば、`connection` を "MyStorage" に設定した場合、Functions ランタイムは "AzureWebJobsMyStorage" という名前のアプリ設定を探します。 `connection` を空のままにした場合、Functions ランタイムは、アプリ設定内の `AzureWebJobsStorage` という名前の既定のストレージ接続文字列を使用します。<br><br>接続文字列は、[BLOB のみのストレージ アカウント](../storage/common/storage-create-storage-account.md#blob-storage-accounts)ではなく汎用ストレージ アカウントに対するものである必要があります。<br>ローカルで開発している場合、アプリ設定は [local.settings.json ファイル](functions-run-local.md#local-settings-file)の値になります。|

## <a name="trigger---usage"></a>トリガー - 使用方法

C# および C# スクリプトでは、`Stream paramName` のようなメソッド パラメーターを使用して BLOB データにアクセスします。 C# スクリプトでは、`paramName` は *function.json* の `name` プロパティで指定された値です。 次の型のいずれにでもバインドできます。

* `TextReader`
* `Stream`
* `ICloudBlob` (*function.json* に "inout" バインド方向が必要)
* `CloudBlockBlob` (*function.json* に "inout" バインド方向が必要)
* `CloudPageBlob` (*function.json* に "inout" バインド方向が必要)
* `CloudAppendBlob` (*function.json* に "inout" バインド方向が必要)

前述のように、これらの型の一部は、*function.json* に `inout` バインド方向を必要とします。 この方向は Azure Portal の標準のエディターではサポートされていないため、詳細エディターを使用する必要があります。

テキスト BLOB がある場合は、`string` 型にバインドできます。 BLOB 全体のコンテンツがメモリに読み込まれるため、これが推奨されるのは、BLOB のサイズが小さい場合のみです。 通常、`Stream` 型または `CloudBlockBlob` 型の使用が推奨されます。

JavaScript では、`context.bindings.<name>` を使用して入力 BLOB データにアクセスします。

## <a name="trigger---blob-name-patterns"></a>トリガー - BLOB 名のパターン

*function.json* の `path` プロパティまたは `BlobTrigger` 属性コンストラクターで BLOB 名のパターンを指定することができます。 名前のパターンは、[フィルターまたはバインド式](functions-triggers-bindings.md#binding-expressions-and-patterns)にすることができます。

### <a name="filter-on-blob-name"></a>BLOB 名のフィルター

次の例は、文字列 "original-" で始まる `input` コンテナーの BLOB でのみトリガーします。

```json
"path": "input/original-{name}",
```
 
BLOB 名が *original-Blob1.txt* の場合、関数コード内の `name` 変数の値は `Blob1` です。

### <a name="filter-on-file-type"></a>ファイルの種類のフィルター

次の例は、*.png* ファイルでのみトリガーします。

```json
"path": "samples/{name}.png",
```

### <a name="filter-on-curly-braces-in-file-names"></a>ファイル名の中かっこのフィルター

ファイル名の中かっこを検索するには、2 つの中かっこを使用して中かっこをエスケープします。 次の例は、名前に中かっこを含む BLOB をフィルター処理します。

```json
"path": "images/{{20140101}}-{name}",
```

BLOB の名前が*{20140101}-soundfile.mp3* の場合、関数コード内の `name` 変数の値は *soundfile.mp3* です。 

### <a name="get-file-name-and-extension"></a>ファイル名と拡張子の取得

次の例は、BLOB ファイル名と拡張子を別々にバインドする方法を示します。

```json
"path": "input/{blobname}.{blobextension}",
```
BLOB の名前が *original-Blob1.txt* の場合、関数コード内の `blobname` 変数と `blobextension` 変数の値は *original-Blob1* と *txt* です。

## <a name="trigger---metadata"></a>トリガー - メタデータ

BLOB トリガーは、いくつかのメタデータ プロパティを提供します。 これらのプロパティは、他のバインドのバインド式の一部として、またはコードのパラメーターとして使用できます。 これらの値は、[CloudBlob](https://docs.microsoft.com/dotnet/api/microsoft.windowsazure.storage.blob.cloudblob?view=azure-dotnet) 型と同じセマンティクスを持ちます。


|プロパティ  |型  |説明  |
|---------|---------|---------|
|`BlobTrigger`|`string`|トリガーする BLOB のパス。|
|`Uri`|`System.Uri`|プライマリ ロケーションの BLOB URI。|
|`Properties` |[BlobProperties](https://docs.microsoft.com/dotnet/api/microsoft.windowsazure.storage.blob.blobproperties)|Blob のシステム プロパティ。 |
|`Metadata` |`IDictionary<string,string>`|BLOB のユーザー定義メタデータ。|

## <a name="trigger---blob-receipts"></a>トリガー - BLOB の配信確認メッセージ

Azure Functions ランタイムでは、BLOB トリガー関数は、同一の新規または更新された BLOB について 2 回以上呼び出されることはありません。 特定の BLOB バージョンが処理されているかどうかを判断するために、*BLOB の配信確認メッセージ*が維持されます。

Azure Functions では、BLOB の配信確認メッセージは (アプリ設定 `AzureWebJobsStorage` で指定した) 関数アプリの Azure ストレージ アカウント内の *azure-webjobs-hosts* というコンテナーに格納されます。 BLOB の配信確認メッセージには次の情報が含まれています。

* トリガーされた関数 ("*&lt;関数アプリ名>*.Functions.*&lt;関数名>*"。たとえば、"MyFunctionApp.Functions.CopyBlob")
* コンテナーの名前
* BLOB の種類 ("BlockBlob" か "PageBlob")
* BLOB の名前
* ETag (BLOB のバージョン識別子。たとえば、"0x8D1DC6E70A277EF")

BLOB を強制的に再処理する場合は、*azure-webjobs-hosts* コンテナーからその BLOB の配信確認メッセージを手動で削除します。

## <a name="trigger---poison-blobs"></a>トリガー - 有害 BLOB

指定された BLOB に対する BLOB トリガー関数が失敗すると、Azure Functions は既定で最大 5 回その関数を再試行します。 

試行が 5 回とも失敗した場合、Azure Functions は *webjobs-blobtrigger-poison* という名前のストレージ キューにメッセージを追加します。 有害な BLOB のキュー メッセージは次のプロパティを持つ JSON オブジェクトです。

* FunctionId (形式: *&lt;Function App 名>*.Functions.*&lt;関数名>*)
* BLOB の種類 ("BlockBlob" か "PageBlob")
* コンテナー名
* BlobName
* ETag (BLOB のバージョン識別子。たとえば、"0x8D1DC6E70A277EF")

## <a name="trigger---polling-for-large-containers"></a>トリガー - 大規模なコンテナーのポーリング

監視対象の BLOB コンテナーに 10,000 を超える BLOB が含まれる場合は、Functions ランタイムによりログ ファイルがスキャンされ、新規または変更された BLOB が監視されます。 このプロセスによって遅延が発生することがあります。 関数は、BLOB が作成されてから数分以上経過しないとトリガーされない可能性があります。 また、[ ストレージ ログは "ベスト エフォート"](/rest/api/storageservices/About-Storage-Analytics-Logging) ベースで作成されます。 すべてのイベントがキャプチャされる保証はありません。 ある条件下では、ログが欠落する可能性があります。 より高速で信頼性の高い BLOB 処理が必要な場合は、BLOB 作成時に[キュー メッセージ](../storage/queues/storage-dotnet-how-to-use-queues.md)を作成することを検討してください。 次に、BLOB トリガーの代わりに[キュー トリガー](functions-bindings-storage-queue.md)を使用して BLOB を処理します。 別のオプションは、Event Grid の使用です。「[Event Grid を使用して、アップロードされたイメージのサイズ変更を自動化する](../event-grid/resize-images-on-storage-blob-upload-event.md)」のチュートリアルをご覧ください。

## <a name="blob-storage-input--output-bindings"></a>Blob Storage の入力および出力バインディング

Blob Storage の入力および出力バインディングを使用して、BLOB の読み取りと書き込みを行います。

## <a name="input--output---example"></a>入力と出力 - 例

言語固有の例をご覧ください。

* [プリコンパイル済み C#](#input--output---c-example)
* [C# スクリプト](#input--output---c-script-example)
* [JavaScript](#input--output---javascript-example)

### <a name="input--output---c-example"></a>入力と出力 - C# の例

次の例は、1 つの BLOB トリガーと 2 つの出力の BLOB バインディングを使用する[プリコンパイル済み C#](functions-dotnet-class-library.md) 関数です。 関数は、*sample-images* コンテナーのイメージ BLOB の作成によってトリガーされます。 イメージ BLOB の小規模および中規模サイズのコピーを作成します。 

```csharp
[FunctionName("ResizeImage")]
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

### <a name="input--output---c-script-example"></a>入力と出力 - C# のスクリプトの例

次の例は、*function.json* ファイルの BLOB 入出力バインドと、バインドを使用する [C# スクリプト](functions-reference-csharp.md) コードを示しています。 関数は、テキスト BLOB のコピーを作成します。 関数は、コピーする BLOB の名前を含むキュー メッセージによってトリガーされます。 新しい BLOB の名前は *{originalblobname}-Copy* です。

*function.json* ファイルでは、`queueTrigger` メタデータ プロパティは `path` プロパティ内の BLOB 名の指定に使用されます。

```json
{
  "bindings": [
    {
      "queueName": "myqueue-items",
      "connection": "MyStorageConnectionAppSetting",
      "name": "myQueueItem",
      "type": "queueTrigger",
      "direction": "in"
    },
    {
      "name": "myInputBlob",
      "type": "blob",
      "path": "samples-workitems/{queueTrigger}",
      "connection": "MyStorageConnectionAppSetting",
      "direction": "in"
    },
    {
      "name": "myOutputBlob",
      "type": "blob",
      "path": "samples-workitems/{queueTrigger}-Copy",
      "connection": "MyStorageConnectionAppSetting",
      "direction": "out"
    }
  ],
  "disabled": false
}
``` 

[構成](#input--output---configuration)のセクションで、これらのプロパティについて説明します。

C# スクリプト コードを次に示します。

```cs
public static void Run(string myQueueItem, string myInputBlob, out string myOutputBlob, TraceWriter log)
{
    log.Info($"C# Queue trigger function processed: {myQueueItem}");
    myOutputBlob = myInputBlob;
}
```

### <a name="input--output---javascript-example"></a>入力と出力 - JavaScript の例

次の例は、*function.json* ファイルの BLOB 入出力バインドと、バインドを使用する [JavaScript コード](functions-reference-node.md)を示しています。 関数は、BLOB のコピーを作成します。 関数は、コピーする BLOB の名前を含むキュー メッセージによってトリガーされます。 新しい BLOB の名前は *{originalblobname}-Copy* です。

*function.json* ファイルでは、`queueTrigger` メタデータ プロパティは `path` プロパティ内の BLOB 名の指定に使用されます。

```json
{
  "bindings": [
    {
      "queueName": "myqueue-items",
      "connection": "MyStorageConnectionAppSetting",
      "name": "myQueueItem",
      "type": "queueTrigger",
      "direction": "in"
    },
    {
      "name": "myInputBlob",
      "type": "blob",
      "path": "samples-workitems/{queueTrigger}",
      "connection": "MyStorageConnectionAppSetting",
      "direction": "in"
    },
    {
      "name": "myOutputBlob",
      "type": "blob",
      "path": "samples-workitems/{queueTrigger}-Copy",
      "connection": "MyStorageConnectionAppSetting",
      "direction": "out"
    }
  ],
  "disabled": false
}
``` 

[構成](#input--output---configuration)のセクションで、これらのプロパティについて説明します。

JavaScript コードを次に示します。

```javascript
module.exports = function(context) {
    context.log('Node.js Queue trigger function processed', context.bindings.myQueueItem);
    context.bindings.myOutputBlob = context.bindings.myInputBlob;
    context.done();
};
```

## <a name="input--output---attributes-for-precompiled-c"></a>入力と出力 - プリコンパイル済み C# の属性

[プリコンパイル済み C#](functions-dotnet-class-library.md) 関数では、NuGet パッケージ [Microsoft.Azure.WebJobs](http://www.nuget.org/packages/Microsoft.Azure.WebJobs) で定義されている [BlobAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/BlobAttribute.cs) を使用します。

次の例で示すように、属性のコンストラクターは、BLOB へのパスと、読み取りまたは書き込みを示す `FileAccess` パラメーターを受け取ります。

```csharp
[FunctionName("ResizeImage")]
public static void Run(
    [BlobTrigger("sample-images/{name}")] Stream image, 
    [Blob("sample-images-md/{name}", FileAccess.Write)] Stream imageSmall)
```

次の例で示すように、`Connection` プロパティを設定して、使用するストレージ アカウントを指定できます。

```csharp
[FunctionName("ResizeImage")]
public static void Run(
    [BlobTrigger("sample-images/{name}")] Stream image, 
    [Blob("sample-images-md/{name}", FileAccess.Write, Connection = "StorageConnectionAppSetting")] Stream imageSmall)
```

`StorageAccount` 属性を使用して、クラス、メソッド、またはパラメーターのレベルでストレージ アカウントを指定できます。 詳しくは、「[トリガー - プリコンパイル済み C# の属性](#trigger---attributes-for-precompiled-c)」をご覧ください。

## <a name="input--output---configuration"></a>入力と出力 - 構成

次の表は、*function.json* ファイルと `Blob` 属性で設定したバインディング構成のプロパティを説明しています。

|function.json のプロパティ | 属性のプロパティ |説明|
|---------|---------|----------------------|
|**type** | 該当なし | `blob` に設定する必要があります。 |
|**direction** | 該当なし | 入力バインディングの場合は `in` に、出力バインディングの場合は out に設定する必要があります。 例外は、[使用方法](#input--output---usage)のセクションに記載しています。 |
|**name** | 該当なし | 関数コード内の BLOB を表す変数の名前。  `$return` に設定して、関数の戻り値を参照します。|
|**path** |**BlobPath** | BLOB へのパス。 | 
|**connection** |**Connection**| このバインドに使用するストレージ接続文字列を含むアプリ設定の名前です。 アプリ設定の名前が "AzureWebJobs" で始まる場合は、ここで名前の残りの部分のみを指定できます。 たとえば、`connection` を "MyStorage" に設定した場合、Functions ランタイムは "AzureWebJobsMyStorage" という名前のアプリ設定を探します。 `connection` を空のままにした場合、Functions ランタイムは、アプリ設定内の `AzureWebJobsStorage` という名前の既定のストレージ接続文字列を使用します。<br><br>接続文字列は、[BLOB のみのストレージ アカウント](../storage/common/storage-create-storage-account.md#blob-storage-accounts)ではなく汎用ストレージ アカウントに対するものである必要があります。<br>ローカルで開発している場合、アプリ設定は [local.settings.json ファイル](functions-run-local.md#local-settings-file)の値になります。|
|該当なし | **Access (アクセス)** | 読み取りと書き込みのどちらを行うかを示します。 |

## <a name="input--output---usage"></a>入力と出力 - 使用方法

プリコンパイル済みの C# および C# スクリプトでは、`Stream paramName` のようなメソッド パラメーターを使用して BLOB にアクセスします。 C# スクリプトでは、`paramName` は *function.json* の `name` プロパティで指定された値です。 次の型のいずれにでもバインドできます。

* `out string`
* `TextWriter` 
* `TextReader`
* `Stream`
* `ICloudBlob` (*function.json* に "inout" バインド方向が必要)
* `CloudBlockBlob` (*function.json* に "inout" バインド方向が必要)
* `CloudPageBlob` (*function.json* に "inout" バインド方向が必要)
* `CloudAppendBlob` (*function.json* に "inout" バインド方向が必要)

前述のように、これらの型の一部は、*function.json* に `inout` バインド方向を必要とします。 この方向は Azure Portal の標準のエディターではサポートされていないため、詳細エディターを使用する必要があります。

テキスト BLOB を読み取る場合は、`string` 型にバインドすることができます。 BLOB 全体のコンテンツがメモリに読み込まれるため、この型が推奨されるのは、BLOB のサイズが小さい場合のみです。 通常、`Stream` 型または `CloudBlockBlob` 型の使用が推奨されます。

JavaScript では、`context.bindings.<name>` を使用して BLOB データにアクセスします。

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [Blob Storage のトリガーを使用するクイック スタートに進む](functions-create-storage-blob-triggered-function.md)

> [!div class="nextstepaction"]
> [Azure Functions のトリガーとバインドの詳細情報](functions-triggers-bindings.md)
