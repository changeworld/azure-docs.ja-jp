---
title: "Azure Functions C# developer reference (Azure Functions C# 開発者向けリファレンス)"
description: "C# を使用して Azure Functions を開発する方法について説明します。"
services: functions
documentationcenter: na
author: ggailey777
manager: cfowler
editor: 
tags: 
keywords: "Azure Functions, 機能, イベント処理, Webhook, 動的コンピューティング, サーバーなしのアーキテクチャ"
ms.service: functions
ms.devlang: dotnet
ms.topic: reference
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 12/12/2017
ms.author: glenga
ms.openlocfilehash: 9e9aa8a36d363ce28d61c5ba3cfe758520a626cf
ms.sourcegitcommit: fbba5027fa76674b64294f47baef85b669de04b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/24/2018
---
# <a name="azure-functions-c-developer-reference"></a>Azure Functions C# developer reference (Azure Functions C# 開発者向けリファレンス)

<!-- When updating this article, make corresponding changes to any duplicate content in functions-reference-csharp.md -->

この記事では、.NET クラス ライブラリの C# を使用した Azure Functions 開発の概要を示します。

Azure Functions では、C# および C# スクリプト プログラミング言語をサポートします。 [Azure Portal での C# の使用](functions-create-function-app-portal.md)に関するガイダンスを探している場合は、[C# スクリプト (.csx) 開発者向けリファレンス](functions-reference-csharp.md)をご覧ください。

この記事では、既に次の記事に目を通していることを前提とします。

* [Azure Functions の開発者向けガイド](functions-reference.md)
* [Azure Functions 向けの Visual Studio 2017 Tools](functions-develop-vs.md)

## <a name="functions-class-library-project"></a>関数クラス ライブラリ プロジェクト

Visual Studio では、**Azure Functions** プロジェクト テンプレートは、次のファイルを含む C# クラス ライブラリ プロジェクトを作成します。

* [host.json](functions-host-json.md) - ローカルまたは Azure 内で実行される場合に、プロジェクト内のすべての関数に影響を及ぼす構成設定を格納します。
* [local.settings.json](functions-run-local.md#local-settings-file) - ローカルで実行される場合に使用されるアプリ設定および接続文字列を格納します。

> [!IMPORTANT]
> ビルド処理では、関数ごとに *function.json* ファイルが作成されます。 この *function.json* ファイルに対しては、直接編集は行われません。 このファイルを編集して、バインド構成を変更したり、関数を無効にしたりすることはできません。 関数を無効にするには、[Disable](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/DisableAttribute.cs) 属性を使用します。 たとえば、ブール値アプリ設定 MY_TIMER_DISABLED を追加し、`[Disable("MY_TIMER_DISABLED")]` を関数に適用します。 アプリ設定を変更して有効と無効を切り替えることができます。

### <a name="functionname-and-trigger-attributes"></a>FunctionName およびトリガー属性

クラス ライブラリでは、次の例に示すように 関数は `FunctionName` を使用した静的メソッドです。

```csharp
public static class SimpleExample
{
    [FunctionName("QueueTrigger")]
    public static void Run(
        [QueueTrigger("myqueue-items")] string myQueueItem, 
        TraceWriter log)
    {
        log.Info($"C# function processed: {myQueueItem}");
    }
} 
```

`FunctionName` 属性は、関数のエントリ ポイントとしてメソッドをマークします。 名前は、プロジェクト内で一意にする必要があります。

トリガー属性は、トリガーの種類を指定し、メソッド パラメーターに入力データをバインドします。 例の関数は 1 つのクエリ メッセージによってトリガーされ、そのクエリ メッセージは `myQueueItem` パラメーターでメソッドに渡されます。

### <a name="additional-binding-attributes"></a>追加のバインド属性

追加の入力および出力バインド属性が使用される場合があります。 次の例では、出力キュー バインドを追加することで、前の属性を変更しています。 関数では、入力キュー メッセージを別のキューの新しいキュー メッセージに書き込んでいます。

```csharp
public static class SimpleExampleWithOutput
{
    [FunctionName("CopyQueueMessage")]
    public static void Run(
        [QueueTrigger("myqueue-items-source")] string myQueueItem, 
        [Queue("myqueue-items-destination")] out string myQueueItemCopy,
        TraceWriter log)
    {
        log.Info($"CopyQueueMessage function processed: {myQueueItem}");
        myQueueItemCopy = myQueueItem;
    }
}
```

### <a name="order-of-parameters"></a>パラメーターの順序

関数シグネチャのパラメーターの順序は関係ありません。 たとえば、トリガー パラメーターは、他のバインドの前後に配置できます。また、ロガー パラメーターは、トリガー パラメーターまたはバインド パラメーターの前後に配置できます。

### <a name="binding-expressions"></a>バインド式

属性コンストラクターのパラメーターおよび関数パラメーターでバインド式を使用できます。 たとえば、次のコードは、アプリ設定から監視するキューの名前を取得し、`insertionTime` パラメーターのキュー メッセージの作成時刻を取得します。

```csharp
public static class BindingExpressionsExample
{
    [FunctionName("LogQueueMessage")]
    public static void Run(
        [QueueTrigger("%queueappsetting%")] string myQueueItem,
        DateTimeOffset insertionTime,
        TraceWriter log)
    {
        log.Info($"Message content: {myQueueItem}");
        log.Info($"Created at: {insertionTime}");
    }
}
```

詳細については、[トリガーとバインド](functions-triggers-bindings.md#binding-expressions-and-patterns)に関するページの「**バインド式とパターン**」を参照してください。

### <a name="conversion-to-functionjson"></a>function.json への変換

ビルド処理では、ビルド フォルダー内の関数フォルダーに *function.json*ファイルを作成します。 前述のとおり、このファイルに対しては直接編集が行われません。 このファイルを編集して、バインド構成を変更したり、関数を無効にしたりすることはできません。 

このファイルの目的は、[従量課金プランに関する決定事項を評価](functions-scale.md#how-the-consumption-plan-works)する際に使用するスケール コントローラーに、情報を提供することです。 このため、ファイルはトリガー情報だけを含み、入力または出力バインドは含まれません。

生成された *function.json* ファイルには、*function.json* 構成ではなく、バインドの .NET 属性を使用するようにランタイムに指示する `configurationSource` プロパティが含まれます。 次に例を示します。

```json
{
  "generatedBy": "Microsoft.NET.Sdk.Functions-1.0.0.0",
  "configurationSource": "attributes",
  "bindings": [
    {
      "type": "queueTrigger",
      "queueName": "%input-queue-name%",
      "name": "myQueueItem"
    }
  ],
  "disabled": false,
  "scriptFile": "..\\bin\\FunctionApp1.dll",
  "entryPoint": "FunctionApp1.QueueTrigger.Run"
}
```

*function.json* ファイルの生成は、NuGet パッケージ ([Microsoft\.NET\.Sdk\.Functions](http://www.nuget.org/packages/Microsoft.NET.Sdk.Functions)) によって実行されます。 ソース コードは、GitHub リポジトリ ([azure\-functions\-vs\-build\-sdk](https://github.com/Azure/azure-functions-vs-build-sdk)) で利用できます。

## <a name="supported-types-for-bindings"></a>バインドでサポートされる型

各バインドには独自にサポートされる型があります。たとえば、BLOB トリガー属性は文字列パラメーター、POCO パラメーター、`CloudBlockBlob` パラメーター、またはサポートされるその他の複数の型のいずれかに適用できます。 [BLOB バインディングのバインド リファレンス](functions-bindings-storage-blob.md#trigger---usage)に関する記事に、サポートされるすべてのパラメーター型の一覧が示されています。 詳細については、[トリガーとバインド](functions-triggers-bindings.md)に関する記事と、[各バインドの種類に対応するバインド リファレンス ドキュメント](functions-triggers-bindings.md#next-steps)をご覧ください。

[!INCLUDE [HTTP client best practices](../../includes/functions-http-client-best-practices.md)]

## <a name="binding-to-method-return-value"></a>メソッドの戻り値へのバインド

出力バインドのメソッドの戻り値を使用するには、属性をメソッドの戻り値に適用します。 例については、[トリガーとバインド](functions-triggers-bindings.md#using-the-function-return-value)に関するページを参照してください。

## <a name="writing-multiple-output-values"></a>複数の出力値の書き込み

出力バインドに複数の値を書き込むには、[`ICollector`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/ICollector.cs) 型または [`IAsyncCollector`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/IAsyncCollector.cs) 型を使用します。 これらの型は、メソッド完了時に出力バインドに書き込まれる、書き込み専用接続です。

この例では、`ICollector` を使用して複数のキュー メッセージを同じキューに書き込みます。

```csharp
public static class ICollectorExample
{
    [FunctionName("CopyQueueMessageICollector")]
    public static void Run(
        [QueueTrigger("myqueue-items-source-3")] string myQueueItem,
        [Queue("myqueue-items-destination")] ICollector<string> myQueueItemCopy,
        TraceWriter log)
    {
        log.Info($"C# function processed: {myQueueItem}");
        myQueueItemCopy.Add($"Copy 1: {myQueueItem}");
        myQueueItemCopy.Add($"Copy 2: {myQueueItem}");
    }
}
```

## <a name="logging"></a>ログの記録

出力を C# のストリーミング ログにログ記録するために、`TraceWriter` 型の引数を含めます。 これの名前を `log`にすることをお勧めします。 Azure Functions では `Console.Write` を使用しないでください。 

`TraceWriter` は [Azure Web ジョブ SDK](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.Host/TraceWriter.cs) で定義されます。 `TraceWriter` のログ レベルは、[host.json](functions-host-json.md) で構成できます。

```csharp
public static class SimpleExample
{
    [FunctionName("QueueTrigger")]
    public static void Run(
        [QueueTrigger("myqueue-items")] string myQueueItem, 
        TraceWriter log)
    {
        log.Info($"C# function processed: {myQueueItem}");
    }
} 
```

> [!NOTE]
> `TraceWriter` の代わりに使用できる新しいログ記録フレームワークについては、**Azure Functions の監視**に関する記事にある、[C# 関数でのログの書き込み](functions-monitoring.md#write-logs-in-c-functions)についての説明をご覧ください。

## <a name="async"></a>非同期

関数を非同期にするには、`async` キーワードを使用して `Task` オブジェクトを返します。

```csharp
public static class AsyncExample
{
    [FunctionName("BlobCopy")]
    public static async Task RunAsync(
        [BlobTrigger("sample-images/{blobName}")] Stream blobInput,
        [Blob("sample-images-copies/{blobName}", FileAccess.Write)] Stream blobOutput,
        CancellationToken token,
        TraceWriter log)
    {
        log.Info($"BlobCopy function processed.");
        await blobInput.CopyToAsync(blobOutput, 4096, token);
    }
}
```

## <a name="cancellation-tokens"></a>キャンセル トークン

関数は、[CancellationToken](https://msdn.microsoft.com/library/system.threading.cancellationtoken.aspx) パラメーターを受け取ることができます。これによって、関数が中断しそうな場合に、オペレーティング システムからコードに通知を受けることができます。 この通知を使用すれば、関数が予期せず終了してデータが不整合な状態になることを防止できます。

次の例では、関数の終了が迫っているかどうかを確認する方法を示します。

```csharp
public static class CancellationTokenExample
{
    public static void Run(
        [QueueTrigger("inputqueue")] string inputText,
        TextWriter logger,
        CancellationToken token)
    {
        for (int i = 0; i < 100; i++)
        {
            if (token.IsCancellationRequested)
            {
                logger.WriteLine("Function was cancelled at iteration {0}", i);
                break;
            }
            Thread.Sleep(5000);
            logger.WriteLine("Normal processing for queue message={0}", inputText);
        }
    }
}
```

## <a name="environment-variables"></a>環境変数

環境変数またはアプリ設定値を取得するには、次のコード例のように、 `System.Environment.GetEnvironmentVariable`を使用します。

```csharp
public static class EnvironmentVariablesExample
{
    [FunctionName("GetEnvironmentVariables")]
    public static void Run([TimerTrigger("0 */5 * * * *")]TimerInfo myTimer, TraceWriter log)
    {
        log.Info($"C# Timer trigger function executed at: {DateTime.Now}");
        log.Info(GetEnvironmentVariable("AzureWebJobsStorage"));
        log.Info(GetEnvironmentVariable("WEBSITE_SITE_NAME"));
    }

    public static string GetEnvironmentVariable(string name)
    {
        return name + ": " +
            System.Environment.GetEnvironmentVariable(name, EnvironmentVariableTarget.Process);
    }
}
```

## <a name="binding-at-runtime"></a>実行時のバインド

C# および他の .NET 言語では、属性の[*宣言型*](https://en.wikipedia.org/wiki/Declarative_programming)のバインドではなく[命令型](https://en.wikipedia.org/wiki/Imperative_programming)のバインド パターンを使用できます。 命令型のバインドは、設計時ではなくランタイム時にバインド パラメーターを計算する必要がある場合に便利です。 このパターンを使用すると、サポートされている入力バインドと出力バインドに関数コード内でバインドできます。

次のように命令型のバインドを定義します。

- 必要な命令型のバインドの関数署名に属性を含め**ないでください。**
- 入力パラメーター [`Binder binder`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.Host/Bindings/Runtime/Binder.cs) または [`IBinder binder`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/IBinder.cs) を渡します。
- 次の C# パターンを使用してデータ バインドを実行します。

  ```cs
  using (var output = await binder.BindAsync<T>(new BindingTypeAttribute(...)))
  {
      ...
  }
  ```

  `BindingTypeAttribute` はバインドを定義する .NET 属性、`T` はそのバインドの種類でサポートされている入力または出力の型です。 `T` を `out` パラメーター型 (`out JObject` など) にすることはできません。 たとえば、Mobile Apps テーブルの出力バインドは [6 種類の出力](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.MobileApps/MobileTableAttribute.cs#L17-L22)をサポートしますが、強制バインドに使用できるのは [ICollector<T>](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/ICollector.cs) または [IAsyncCollector<T>](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/IAsyncCollector.cs) のみです。

### <a name="single-attribute-example"></a>単一属性の例

次のコード例は、実行時に BLOB パスが定義された [Storage Blob の出力バインド](functions-bindings-storage-blob.md#output)を作成し、この BLOB に文字列を書き込みます。

```cs
public static class IBinderExample
{
    [FunctionName("CreateBlobUsingBinder")]
    public static void Run(
        [QueueTrigger("myqueue-items-source-4")] string myQueueItem,
        IBinder binder,
        TraceWriter log)
    {
        log.Info($"CreateBlobUsingBinder function processed: {myQueueItem}");
        using (var writer = binder.Bind<TextWriter>(new BlobAttribute(
                    $"samples-output/{myQueueItem}", FileAccess.Write)))
        {
            writer.Write("Hello World!");
        };
    }
}
```

[BlobAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/BlobAttribute.cs) は [Storage Blob](functions-bindings-storage-blob.md) の入力バインドまたは出力バインドを定義します。[TextWriter](https://msdn.microsoft.com/library/system.io.textwriter.aspx) はサポートされている出力バインドの種類です。

### <a name="multiple-attribute-example"></a>複数属性の例

前の例では、関数アプリのメイン ストレージ アカウント接続文字列 (`AzureWebJobsStorage`) のアプリ設定を取得します。 ストレージ アカウントに使用するカスタム アプリ設定を指定するには、[StorageAccountAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/StorageAccountAttribute.cs) を追加し、属性の配列を `BindAsync<T>()` に渡します。 `IBinder`ではなく、`Binder` パラメーターを使用します。  例: 

```cs
public static class IBinderExampleMultipleAttributes
{
    [FunctionName("CreateBlobInDifferentStorageAccount")]
    public async static Task RunAsync(
            [QueueTrigger("myqueue-items-source-binder2")] string myQueueItem,
            Binder binder,
            TraceWriter log)
    {
        log.Info($"CreateBlobInDifferentStorageAccount function processed: {myQueueItem}");
        var attributes = new Attribute[]
        {
        new BlobAttribute($"samples-output/{myQueueItem}", FileAccess.Write),
        new StorageAccountAttribute("MyStorageAccount")
        };
        using (var writer = await binder.BindAsync<TextWriter>(attributes))
        {
            await writer.WriteAsync("Hello World!!");
        }
    }
}
```

## <a name="triggers-and-bindings"></a>トリガーとバインド 

次の表は、Azure Functions クラス ライブラリ プロジェクトで使用できるトリガーとバインド属性の一覧を示しています。 すべての属性は、名前空間 `Microsoft.Azure.WebJobs` に含まれています。

| トリガー | 入力 | 出力|
|------   | ------    | ------  |
| [BlobTrigger](functions-bindings-storage-blob.md#trigger---attributes)| [BLOB](functions-bindings-storage-blob.md#input---attributes)| [BLOB](functions-bindings-storage-blob.md#output---attributes)|
| [CosmosDBTrigger](functions-bindings-cosmosdb.md#trigger---attributes)| [DocumentDB](functions-bindings-cosmosdb.md#input---attributes)| [DocumentDB](functions-bindings-cosmosdb.md#output---attributes) |
| [EventHubTrigger](functions-bindings-event-hubs.md#trigger---attributes)|| [EventHub](functions-bindings-event-hubs.md#output---attributes) |
| [HTTPTrigger](functions-bindings-http-webhook.md#trigger---attributes)|||
| [QueueTrigger](functions-bindings-storage-queue.md#trigger---attributes)|| [キュー](functions-bindings-storage-queue.md#output---attributes) |
| [ServiceBusTrigger](functions-bindings-service-bus.md#trigger---attributes)|| [ServiceBus](functions-bindings-service-bus.md#output---attributes) |
| [TimerTrigger](functions-bindings-timer.md#attributes) | ||
| |[ApiHubFile](functions-bindings-external-file.md)| [ApiHubFile](functions-bindings-external-file.md)|
| |[MobileTable](functions-bindings-mobile-apps.md#input---attributes)| [MobileTable](functions-bindings-mobile-apps.md#output---attributes) | 
| |[テーブル](functions-bindings-storage-table.md#input---attributes)| [テーブル](functions-bindings-storage-table.md#output---attributes)  | 
| ||[NotificationHub](functions-bindings-notification-hubs.md#attributes) |
| ||[SendGrid](functions-bindings-sendgrid.md#attributes) |
| ||[Twilio](functions-bindings-twilio.md#attributes)| 

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [トリガーとバインドの詳細を確認する](functions-triggers-bindings.md)

> [!div class="nextstepaction"]
> [Azure Functions のベスト プラクティスを確認する](functions-best-practices.md)
