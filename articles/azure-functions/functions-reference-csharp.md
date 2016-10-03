<properties
	pageTitle="Azure Functions 開発者向けリファレンス | Microsoft Azure"
	description="C# を使用して Azure Functions を開発する方法について説明します。"
	services="functions"
	documentationCenter="na"
	authors="christopheranderson"
	manager="erikre"
	editor=""
	tags=""
	keywords="Azure Functions, 機能, イベント処理, Webhook, 動的コンピューティング, サーバーなしのアーキテクチャ"/>

<tags
	ms.service="functions"
	ms.devlang="dotnet"
	ms.topic="reference"
	ms.tgt_pltfrm="multiple"
	ms.workload="na"
	ms.date="05/13/2016"
	ms.author="chrande"/>

# Azure Functions C# developer reference (Azure Functions C# 開発者向けリファレンス)

> [AZURE.SELECTOR]
- [C# スクリプト](../articles/azure-functions/functions-reference-csharp.md)
- [F# スクリプト](../articles/azure-functions/functions-reference-fsharp.md)
- [Node.JS](../articles/azure-functions/functions-reference-node.md)
 
Azure Functions の C# エクスペリエンスは、Azure WebJobs SDK に基づいています。データは、メソッドの引数を使用して C# 関数に渡されます。引数名は `function.json` で指定され、関数のロガーやキャンセル トークンなどにアクセスするための定義済みの名前があります。

この記事では、「[Azure Functions developer reference (Azure Functions 開発者向けリファレンス)](functions-reference.md)」を既に読んでいることを前提としています。

## .csx のしくみ

`.csx` の形式では、"定型" の記述が少なく、C# 関数のみの記述に重点が置かれています。Azure Functions の場合、通常どおりに上部に表示する任意のアセンブリ参照と名前空間を含め、すべてを名前空間とクラスにラッピングする代わりに、`Run` メソッドの定義のみを行います。POCO オブジェクトを定義する場合など、クラスを含める必要がある場合は、同じファイル内にクラスを含めることができます。

## 引数へのバインド

*function.json* 構成の `name` プロパティを通じて、さまざまなバインドが C# 関数にバインドされます。各バインドには、バンドごとに記述される、独自のサポートされている型があります。たとえば、blob のトリガーでは、文字列、POCO、その他いくつかの型がサポートされます。ニーズに合わせて最適な型を使用できます。

```csharp
public static void Run(string myBlob, out MyClass myQueueItem)
{
    log.Verbose($"C# Blob trigger function processed: {myBlob}");
    myQueueItem = new MyClass() { Id = "myid" };
}

public class MyClass
{
    public string Id { get; set; }
}
```

## ログの記録

出力を C# のストリーミング ログにログ記録するために、`TraceWriter` 型の引数を含めることができます。これの名前を `log` にすることをお勧めします。Azure Functions の `Console.Write` は避けることをお勧めします。

```csharp
public static void Run(string myBlob, TraceWriter log)
{
    log.Info($"C# Blob trigger function processed: {myBlob}");
}
```

## 非同期

関数を非同期にするには、`async` キーワードを使用して `Task` オブジェクトを返します。

```csharp
public async static Task ProcessQueueMessageAsync(
        string blobName, 
        Stream blobInput,
        Stream blobOutput)
    {
        await blobInput.CopyToAsync(blobOutput, 4096, token);
    }
```

## キャンセル トークン

場合によっては、シャット ダウンに影響を受ける操作があります。クラッシュに対応するコードを記述するのが最善の方法ですが、グレースフル シャットダウンの要求を処理する場合は、[`CancellationToken`](https://msdn.microsoft.com/library/system.threading.cancellationtoken.aspx) 型の引数を定義します。ホストのシャットダウンがトリガーされると、`CancellationToken` が提供されます。

```csharp
public async static Task ProcessQueueMessageAsyncCancellationToken(
        string blobName, 
        Stream blobInput,
        Stream blobOutput,
        CancellationToken token)
    {
        await blobInput.CopyToAsync(blobOutput, 4096, token);
    }
```

## 名前空間のインポート

名前空間をインポートする必要がある場合は、`using` 句を使用して、通常どおりにインポートできます。

```csharp
using System.Net;
using System.Threading.Tasks;

public static Task<HttpResponseMessage> Run(HttpRequestMessage req, TraceWriter log)
```

次の名前空間は自動的にインポートされるため、オプションとなります。

* `System`
* `System.Collections.Generic`
* `System.IO`
* `System.Linq`
* `System.Net.Http`
* `System.Threading.Tasks`
* `Microsoft.Azure.WebJobs`
* `Microsoft.Azure.WebJobs.Host`

## 外部アセンブリの参照

フレームワークのアセンブリには、`#r "AssemblyName"` ディレクティブを使用して参照を追加します。

```csharp
#r "System.Web.Http"

using System.Net;
using System.Net.Http;
using System.Threading.Tasks;

public static Task<HttpResponseMessage> Run(HttpRequestMessage req, TraceWriter log)
```

次のアセンブリは、Azure Functions をホストしている環境によって自動的に追加されます。

* `mscorlib`,
* `System`
* `System.Core`
* `System.Xml`
* `System.Net.Http`
* `Microsoft.Azure.WebJobs`
* `Microsoft.Azure.WebJobs.Host`
* `Microsoft.Azure.WebJobs.Extensions`
* `System.Web.Http`
* `System.Net.Http.Formatting`

さらに、次のアセンブリは特別扱いされ、simplename によって参照される場合があります (例: `#r "AssemblyName"`)。

* `Newtonsoft.Json`
* `Microsoft.WindowsAzure.Storage`
* `Microsoft.ServiceBus`
* `Microsoft.AspNet.WebHooks.Receivers`
* `Microsoft.AspNEt.WebHooks.Common`

プライベート アセンブリを参照する必要がある場合は、アセンブリ ファイルを関数に関連する `bin` フォルダーにアップロードし、ファイル名 (例: `#r "MyAssembly.dll"`) を使用して参照できます。関数フォルダーにファイルをアップロードする方法については、パッケージ管理の次のセクションを参照してください。

## パッケージの管理

NuGet パッケージを C# 関数で使用するには、*project.json* ファイルを関数アプリのファイル システムにある関数のフォルダーにアップロードします。Microsoft.ProjectOxford.Face バージョン 1.1.0 への参照を追加する *project.json* ファイルの例を次に示します。

```json
{
  "frameworks": {
    "net46":{
      "dependencies": {
        "Microsoft.ProjectOxford.Face": "1.1.0"
      }
    }
   }
}
```

.NET Framework 4.6 のみがサポートされているので、次に示すように *project.json* ファイルが `net46` を指定していることを確認します。

*project.json* ファイルをアップロードすると、ランタイムによってパッケージが取得され、パッケージ アセンブリに参照が自動的に追加されます。`#r "AssemblyName"` ディレクティブを追加する必要はありません。NuGet パッケージで定義されている型を使用するには、必要な `using` ステートメントを *run.csx* ファイルに追加するだけです。


### Project.json ファイルをアップロードする方法

1. Azure ポータルで関数を開き、関数アプリが実行中であることを確認して開始します。

	これにより、パッケージのインストール出力が表示されるストリーミング ログへのアクセス権が付与されます。

2. project.json ファイルをアップロードするには、「[Azure Functions developer reference (Azure Functions 開発者向けリファレンス)](functions-reference.md#fileupdate)」の「**関数アプリ ファイルを更新する方法**」セクションにあるいずれかの方法を利用してください。

3. *project.json* ファイルがアップロードされた後、関数のストリーミング ログの出力は次の例のようになります。

```
2016-04-04T19:02:48.745 Restoring packages.
2016-04-04T19:02:48.745 Starting NuGet restore
2016-04-04T19:02:50.183 MSBuild auto-detection: using msbuild version '14.0' from 'D:\Program Files (x86)\MSBuild\14.0\bin'.
2016-04-04T19:02:50.261 Feeds used:
2016-04-04T19:02:50.261 C:\DWASFiles\Sites\facavalfunctest\LocalAppData\NuGet\Cache
2016-04-04T19:02:50.261 https://api.nuget.org/v3/index.json
2016-04-04T19:02:50.261 
2016-04-04T19:02:50.511 Restoring packages for D:\home\site\wwwroot\HttpTriggerCSharp1\Project.json...
2016-04-04T19:02:52.800 Installing Newtonsoft.Json 6.0.8.
2016-04-04T19:02:52.800 Installing Microsoft.ProjectOxford.Face 1.1.0.
2016-04-04T19:02:57.095 All packages are compatible with .NETFramework,Version=v4.6.
2016-04-04T19:02:57.189 
2016-04-04T19:02:57.189 
2016-04-04T19:02:57.455 Packages restored.
```

## 環境変数

環境変数またはアプリ設定値を取得するには、次のコード例のように、`System.Environment.GetEnvironmentVariable` を使用します。

```csharp
public static void Run(TimerInfo myTimer, TraceWriter log)
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
```

## .csx コードの再利用

他の *.csx* ファイルで定義されたクラスとメソッドを、*run.csx* ファイルで使用できます。そのためには、次の例に示すように `#load` ディレクティブを *run.csx* ファイルで使用します。

*run.csx* の例:

```csharp
#load "mylogger.csx"

public static void Run(TimerInfo myTimer, TraceWriter log)
{
    log.Verbose($"Log by run.csx: {DateTime.Now}"); 
    MyLogger(log, $"Log by MyLogger: {DateTime.Now}");
}
```

*mylogger.csx* の例:

```csharp
public static void MyLogger(TraceWriter log, string logtext)
{
    log.Verbose(logtext); 
}
```

`#load` ディレクティブで相対パスを使用できます。

* `#load "mylogger.csx"` によって、関数フォルダーにあるファイルが読み込まれます。

* `#load "loadedfiles\mylogger.csx"` によって、関数フォルダー内のフォルダーにあるファイルが読み込まれます。

* `#load "..\shared\mylogger.csx"` によって、関数フォルダーと同じレベル (*wwwroot* の直下) にあるフォルダーのファイルが読み込まれます。
 
`#load` ディレクティブは、*.csx* (C# スクリプト) ファイルでのみ機能し、*.cs* ファイルでは機能しません。

## 次のステップ

詳細については、次のリソースを参照してください。

* [Azure Functions developer reference (Azure Functions 開発者向けリファレンス)](functions-reference.md)
* [Azure Functions NodeJS 開発者向けリファレンス](functions-reference-fsharp.md)
* [Azure Functions NodeJS 開発者向けリファレンス](functions-reference-node.md)
* [Azure Functions triggers and bindings (Azure Functions のトリガーとバインド)](functions-triggers-bindings.md)

<!---HONumber=AcomDC_0921_2016-->