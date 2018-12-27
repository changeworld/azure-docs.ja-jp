---
title: Azure WebJobs SDK の使用方法
description: WebJobs SDK 用のコードを書く方法を学びます。 Azure サービスやサード パーティのデータにアクセスするイベント ドリブンのバックグラウンド処理ジョブを作成します。
services: app-service\web, storage
documentationcenter: .net
author: ggailey777
manager: cfowler
editor: ''
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 04/27/2018
ms.author: glenga
ms.openlocfilehash: 3e06dc82baed4043ce490769aa0ec84ab3de8c24
ms.sourcegitcommit: 615403e8c5045ff6629c0433ef19e8e127fe58ac
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/06/2018
ms.locfileid: "39577013"
---
# <a name="how-to-use-the-azure-webjobs-sdk-for-event-driven-background-processing"></a>イベント ドリブンのバックグラウンド処理に Azure WebJobs SDK を使用する方法

この記事は、[Azure WebJobs SDK](webjobs-sdk-get-started.md) のコードを記述する方法のガイダンスを提供します。 このドキュメントは、特別に明記されていない限り、バージョン 2.x と 3.x に適用されます。 3.x で導入された主な変更は、.NET Framework の代わりに .NET Core を使用することです。

>[!NOTE]
> [Azure Functions](../azure-functions/functions-overview.md) は WebJobs SDK でビルドされており、この記事は Azure Functions ドキュメントの一部のトピックとリンクしています。 Functions と WebJobs SDK には以下の違いがあります。
> * Azure Functions バージョン 1.x は WebJobs SDK バージョン 2.x に対応しており、Azure Functions 2.x は WebJobs SDK 3.x に対応しています。 ソース コード リポジトリは、WebJobs SDK の番号付けに従っており、多くの場合、 v2.xブランチがあり、マスター ブランチが現在 3.x コードであります。
> * Azure Functions C# クラス ライブラリのサンプル コードは、WebJobs SDK プロジェクトに `FunctionName` 属性が必要ないということ以外は、WebJobs SDK コードに類似しています。
> * バインドの種類のいくつかは、HTTP、webhook、(HTTP に基づく) Event Grid のような Functions でのみサポートされます。 
> 
> 詳しくは、[WebJobs SDK と Azure Functions の比較](../azure-functions/functions-compare-logic-apps-ms-flow-webjobs.md#compare-functions-and-webjobs)に関するページをご覧ください。 

## <a name="prerequisites"></a>前提条件

この記事は、[WebJobs SDK を使い始める](webjobs-sdk-get-started.md)を既にお読みになっていることを想定して書かれています。

## <a name="jobhost"></a>JobHost

`JobHost` オブジェクトは関数のランタイム コンテナーで、トリガーをリッスンし、関数を呼び出します。 コードに `JobHost` を作成して、コードを書いてその動作をカスタマイズします。

これが、直接 WebJobs SDK を使用することと、Azure Functions を使用することにより間接的に使用することの主な違いです。 Azure Functions では、サービスが `JobHost` を制御するので、コードを記述してカスタマイズすることはできません。 Azure Functions では、*host.json* ファイルの設定を使用してホスト動作をカスタマイズできます。 これらの設定は文字列であり、コードではなく、実行できるカスタマイズの種類を制限します。

### <a name="jobhost-connection-strings"></a>JobHost 接続文字列

WebJobs SDK は、ローカルでまたは Azure の Web ジョブの環境で実行すると、*local.settings.json* で Storage と Service Busの接続文字列 を検索します。 これらの接続文字列に独自の名前を使用するか、別の場所に保存する場合は、以下に示すように、コード内で設定することができます。

```cs
static void Main(string[] args)
{
    var _storageConn = ConfigurationManager
        .ConnectionStrings["MyStorageConnection"].ConnectionString;

    var _dashboardConn = ConfigurationManager
        .ConnectionStrings["MyDashboardConnection"].ConnectionString;

    JobHostConfiguration config = new JobHostConfiguration();
    config.StorageConnectionString = _storageConn;
    config.DashboardConnectionString = _dashboardConn;
    JobHost host = new JobHost(config);
    host.RunAndBlock();
}
```

### <a name="jobhost-development-settings"></a>JobHost 開発設定

`JobHostConfiguration` クラスには、ローカルの開発をより効率的にするために呼び出すことができる `UseDevelopmentSettings` メソッドがあります。 このメソッドが変更する設定の一部を次に示します。

| プロパティ | 開発設定 |
| ------------- | ------------- |
| `Tracing.ConsoleLevel` | `TraceLevel.Verbose` でログ出力を最大化します。 |
| `Queues.MaxPollingInterval`  | キューのメソッドがすぐにトリガーされるように、値は低くしてあります。  |
| `Singleton.ListenerLockPeriod` | 素早い反復開発の支援は 15 秒です。 |

次の例では、開発設定を使用する方法を示します。 ローカルで実行中に `config.IsDevelopment` が `true` を返すようにするには、`AzureWebJobsEnv` という名前のローカルの環境変数を `Development` の値で設定します。

```cs
static void Main()
{
    config = new JobHostConfiguration();

    if (config.IsDevelopment)
    {
        config.UseDevelopmentSettings();
    }

    var host = new JobHost(config);
    host.RunAndBlock();
}
```

### <a name="jobhost-servicepointmanager-settings"></a>JobHost ServicePointManager 設定

.NET Framework には、[ServicePointManager.DefaultConnectionLimit](https://msdn.microsoft.com/library/system.net.servicepointmanager.defaultconnectionlimit) と呼ばれる API が含まれています。これは、ホストへの同時接続の数を制御します。 WebJobs ホストを開始する前に、既定値である 2 からこの値を増やすことをお勧めします。

`HttpClient` を使用することで関数から送信するすべての HTTP 要求は、`ServicePointManager` を通過します。 `DefaultConnectionLimit` に到達すると、`ServicePointManager` は送信する前にキュー処理要求を開始します。 たとえば、`DefaultConnectionLimit` が 2 に設定されていて、コードが 1,000 個の HTTP 要求を行ったとします。 まずは、2 個の要求のみが実際に OS に許可されます。 その他の 998 個は、余裕ができるまでキューされたままになります。 つまり、要求を行ったと*思っている*ものの、OS によって宛先サーバーに要求が送信されなかったため、`HttpClient` がタイムアウトする可能性があるということです。 従って、ローカルの `HttpClient` は、要求を完了するのに 10 秒間かかっているのに、サービスはどの要求も 200 ミリ秒で返しているというような、あまり意味をなさないように見える動作が目撃されることがあります。 

ASP.NET アプリケーションの既定値は `Int32.MaxValue` であり、これは Basic またはそれ以降の App Service プランで実行されている WebJobs で適切に動作するでしょう。 WebJobs は通常、Always On の設定を必要としており、これは Basic またはそれ以降の App Service プランでのみサポートされています。 

Web ジョブが Free または Shared App Service プランで実行されている場合、アプリケーションは、現在 [300 の接続制限](https://github.com/projectkudu/kudu/wiki/Azure-Web-App-sandbox#per-sandbox-per-appper-site-numerical-limits)を持つ App Service サンドボックスにより制限されています。 `ServicePointManager` の設定がバインドなしの接続制限だと、サンドボックスの接続がしきい値に達し、サイトがシャット ダウンする可能性が高いです。 その場合は、`DefaultConnectionLimit`の設定を 50 または 100 のようにより低いものにすることで、これを防ぎつつ十分なスループットを可能にします。

あらゆる HTTP 要求が行われる前に、この設定を構成する必要があります。 このため、WebJobs ホストが自動的に設定を調整するべきではありません。これは、ホストが起動する前に HTTP 要求が発生することがあり、予期しない動作が生じる可能性があるためです。 最良の手法は、次の例で示すように、`JobHost` を初期化する前に `Main` メソッドですぐに値を設定することです。

```csharp
static void Main(string[] args)
{
    // Set this immediately so that it is used by all requests.
    ServicePointManager.DefaultConnectionLimit = Int32.MaxValue;

    var host = new JobHost();
    host.RunAndBlock();
}
```

## <a name="triggers"></a>トリガー

関数は、パブリック メソッドである必要があり、1 つのトリガー属性または [NoAutomaticTrigger](#manual-trigger) 属性を必要とします。

### <a name="automatic-trigger"></a>自動トリガー

自動トリガーは、イベントに応答して関数を呼び出します。 例については、[WebJobs SDK を使い始める](webjobs-sdk-get-started.md)に記載されたキュー トリガーを参照してください。

### <a name="manual-trigger"></a>手動トリガー

関数を手動でトリガーするには、次の例で示すように、`NoAutomaticTrigger` 属性を使用します。

```cs
static void Main(string[] args)
{
    JobHost host = new JobHost();
    host.Call(typeof(Program).GetMethod("CreateQueueMessage"), new { value = "Hello world!" });
}
```

```cs
[NoAutomaticTrigger]
public static void CreateQueueMessage(
    TextWriter logger,
    string value,
    [Queue("outputqueue")] out string message)
{
    message = value;
    logger.WriteLine("Creating queue message: ", message);
}
```

## <a name="input-and-output-bindings"></a>入出力バインド

入力バインドは、Azure やサード パーティ サービスからのデータをコードに使用できるようにする宣言方法を提供します。 出力バインドは、データを更新する方法を提供します。 [WebJobs SDK を使い始める](webjobs-sdk-get-started.md)のページでは、それぞれの例を示しています。

出力バインドのメソッドの戻り値を使用するには、属性をメソッドの戻り値に適用します。 [Azure Functions でのトリガーとバインドの概念](../azure-functions/functions-triggers-bindings.md#using-the-function-return-value)のページで、例を参照してください。

## <a name="binding-types"></a>バインドの種類

次のトリガーとバインドの種類は、`Microsoft.Azure.WebJobs` パッケージに含まれています。

* BLOB ストレージ
* Queue Storage
* テーブル ストレージ

その他の種類のトリガーやバインドを使用するには、それらを含む NuGet パッケージをインストールして、`JobHostConfiguration` オブジェクトの `Use<binding>` メソッドを呼び出します。 たとえば、Timer トリガーを使用する場合は、次の例のように、`Microsoft.Azure.WebJobs.Extensions` をインストールして、`Main` メソッドの `UseTimers` を呼び出します。

```cs
static void Main()
{
    config = new JobHostConfiguration();
    config.UseTimers();
    var host = new JobHost(config);
    host.RunAndBlock();
}
```

特定のバインドの種類用にインストールするパッケージは、Azure Functions のそのバインドの種類の[参照資料](#binding-reference-information)の**パッケージ** セクションで見つけることができます。 例外は (ローカル ファイル システム用の) Files トリガーとバインドで、これは Azure Functions にサポートされていません。 Files バインドを使用するには、`Microsoft.Azure.WebJobs.Extensions` をインストールして `UseFiles` を呼び出します。

### <a name="usecore"></a>UseCore

先述した `Microsoft.Azure.WebJobs.Extensions` パッケージは、`UseCore` メソッドを呼び出すことによって登録できる特別なバインドの種類も提供しています。 このバインドは、[ExecutionContext](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions/Extensions/Core/ExecutionContext.cs) パラメーターを関数シグネチャで定義することができます。 コンテキスト オブジェクトは、呼び出し ID へのアクセスを可能にします。呼び出し ID を使うと、任意の関数呼び出しによって生成されたすべてのログを関連付けることができます。 次に例を示します。

```cs
class Program
{
    static void Main()
    {
        config = new JobHostConfiguration();
        config.UseCore();
        var host = new JobHost(config);
        host.RunAndBlock();
    }
}
public class Functions
{
    public static void ProcessQueueMessage([QueueTrigger("queue")] string message,
        ExecutionContext executionContext,
        ILogger logger)
    {
        logger.LogInformation($"{message}\n{executionContext.InvocationId}");
    }
}
```

## <a name="binding-configuration"></a>バインド構成

多くの種類のトリガーとバインドの動作は、`JobHost` に渡される構成オブジェクトでプロパティを設定することにより、構成することができます。

### <a name="queue-trigger-configuration"></a>キュー トリガーの構成

Storage キュー トリガーに構成できる設定については、Azure Functions の [host.json 参照資料](../azure-functions/functions-host-json.md#queues)で説明されています。 WebJobs SDK プロジェクトでの設定方法は、以下の例で示されています。

```cs
static void Main(string[] args)
{
    JobHostConfiguration config = new JobHostConfiguration();
    config.Queues.BatchSize = 8;
    config.Queues.NewBatchThreshold = 4;
    config.Queues.MaxDequeueCount = 4;
    config.Queues.MaxPollingInterval = TimeSpan.FromSeconds(15);
    JobHost host = new JobHost(config);
    host.RunAndBlock();
}
```

### <a name="configuration-for-other-bindings"></a>その他のバインドの構成

トリガーとバインドの種類によっては、独自のカスタム構成種類を定義するものもあります。 たとえば File トリガーは、監視するルート パスを指定することができます。

```cs
static void Main()
{
    config = new JobHostConfiguration();
    var filesConfig = new FilesConfiguration
    {
        RootPath = @"c:\data\import"
    };
    config.UseFiles(filesConfig);
    var host = new JobHost(config);
    host.RunAndBlock();
}
```

## <a name="binding-expressions"></a>バインド式

属性コンス トラクターのパラメーターでは、さまざまなソースからの値に解決する式を使用することができます。 たとえば、次のコードで `BlobTrigger` 属性のパスは、`filename` という名前の式を作成します。 これが出力バインドに使用されると、`filename` はトリガーする BLOB の名前に解決します。
 
```cs
public static void CreateThumbnail(
    [BlobTrigger("sample-images/{filename}")] Stream image,
    [Blob("sample-images-sm/{filename}", FileAccess.Write)] Stream imageSmall,
    string filename,
    ILogger logger)
{
    logger.Info($"Blob trigger processing: {filename}");
    // ...
}
```

バインド式の詳細については、Azure Functions ドキュメント内の[バインド式とパターン](../azure-functions/functions-triggers-bindings.md#binding-expressions-and-patterns)を参照してください。

### <a name="custom-binding-expressions"></a>カスタム バインド式

キュー名、BLOB 名、コンテナー、テーブル名をハード コーディングではなく、コードに指定する場合もあります。 たとえば、構成ファイルや環境変数で `QueueTrigger` 属性のキュー名を指定するとします。

これは、`NameResolver` オブジェクトを `JobHostConfiguration` オブジェクトに渡すことでできます。 プレースホルダーをトリガーやバインド属性コンストラクターのパラメーターに含めると、これらのプレースホルダーの代わりに使用する実際の値が `NameResolver` コードにより提供されます。 次の例で示すように、プレースホルダーはパーセント (%) 記号で囲むことで識別されます。
 
```cs
public static void WriteLog([QueueTrigger("%logqueue%")] string logMessage)
{
    Console.WriteLine(logMessage);
}
```

このコードにより、テスト環境で「logqueuetest」という名前のキューを、運用環境で「logqueueprod」という名前のキューを使用できます。 キュー名をハードコードする代わりに、`appSettings` コレクションのエントリの名前を指定します。 

カスタムの名前を提供しない場合、既定の NameResolver が使用されます。 既定値は、アプリの設定や環境変数から値を取得します。

次の例に示すように `NameResolver` クラスは、`appSettings` からキューの名前を取得します。

```cs
public class CustomNameResolver : INameResolver
{
    public string Resolve(string name)
    {
        return ConfigurationManager.AppSettings[name].ToString();
    }
}
```

次の例に示すように `NameResolver` クラスを `JobHost` オブジェクトに渡します。

```cs
 static void Main(string[] args)
{
    JobHostConfiguration config = new JobHostConfiguration();
    config.NameResolver = new CustomNameResolver();
    JobHost host = new JobHost(config);
    host.RunAndBlock();
}
```

Azure Functions では、次の例に示すように、アプリの設定から値を取得するために `INameResolver` が実装されます。 WebJobs SDK を直接使用する場合は、お好みのソースからプレースホルダーの置換値を取得するカスタム実装を記述できます。 

## <a name="binding-at-runtime"></a>実行時のバインド

`Queue`、`Blob`、`Table` などのバインド属性を使用する前に関数で何らかの処理を行う必要がある場合は、`IBinder` インターフェイスを使用できます。

次の例では、入力キュー メッセージを取得して同じ内容の新しい出力キュー メッセージを作成します。 出力キュー名は、関数本体のコードによって設定されます。

```cs
public static void CreateQueueMessage(
    [QueueTrigger("inputqueue")] string queueMessage,
    IBinder binder)
{
    string outputQueueName = "outputqueue" + DateTime.Now.Month.ToString();
    QueueAttribute queueAttribute = new QueueAttribute(outputQueueName);
    CloudQueue outputQueue = binder.Bind<CloudQueue>(queueAttribute);
    outputQueue.AddMessage(new CloudQueueMessage(queueMessage));
}
```

詳細については、Azure Functions ドキュメント内の[実行時のバインド](../azure-functions/functions-dotnet-class-library.md#binding-at-runtime)を参照してください。

## <a name="binding-reference-information"></a>バインド参照情報

それぞれのバインドの種類に関する参照情報は、Azure Functions ドキュメントに記載されています。 それぞれのバインド参照資料で Storage キューを例として使用した、以下の情報を記載してあります。

* [パッケージ](../azure-functions/functions-bindings-storage-queue.md#packages---functions-1x) - WebJobs SDK プロジェクトのバインドのサポートを含めるためにインストールするパッケージに関する情報です。
* [例](../azure-functions/functions-bindings-storage-queue.md#trigger---example) - C# クラス ライブラリの例は、WebJobs SDK に適用されます。`FunctionName` 属性は省きます。
* [属性](../azure-functions/functions-bindings-storage-queue.md#trigger---attributes) - バインドの種類に使用する属性です。
* [構成](../azure-functions/functions-bindings-storage-queue.md#trigger---configuration) - 属性のプロパティとコンストラクターのパラメーターの説明です。
* [使用状況](../azure-functions/functions-bindings-storage-queue.md#trigger---usage) - どの種類にバインドできるかとバインドの動作方法に関する情報です。 例: ポーリング アルゴリズム、有害キュー処理。
  
バインドの参照資料の一覧は、[Azure Functions でのトリガーとバインドの概念](../azure-functions/functions-triggers-bindings.md#supported-bindings)の**サポートされるバインディング**を参照してください。 リストでは、HTTP、webhook、Event Grid バインドが WebJobs SDK ではなく、Azure Functions のみにサポートされます。

## <a name="disable-attribute"></a>Disable 属性 

[Disable](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/DisableAttribute.cs) 属性は、関数がトリガーされるかどうかを制御します。 

次の例では、アプリ設定 `Disable_TestJob` の値が、「1」または「True」(大文字小文字を区別しない) の場合、関数は実行されません。 その場合、ランタイムが*関数 'Functions.TestJob' が無効です*というログ メッセージを作成します。

```cs
[Disable("Disable_TestJob")]
public static void TestJob([QueueTrigger("testqueue2")] string message)
{
    Console.WriteLine("Function with Disable attribute executed!");
}
```

Azure Portal でアプリの設定値を変更すると、Web ジョブが再起動され、新しい設定を選択します。

属性は、パラメーター、メソッド、またはクラス レベルで宣言できます。 設定名には、バインド式を含めることもできます。

## <a name="timeout-attribute"></a>Timeout 属性

[Timeout](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/TimeoutAttribute.cs) 属性は、関数が指定した時間内に完了しない場合にキャンセルします。 次の例では、タイムアウトなしで関数は 1 日間実行されます。 タイムアウトがあると、関数は 15 秒後に取り消されます。

```cs
[Timeout("00:00:15")]
public static async Task TimeoutJob(
    [QueueTrigger("testqueue2")] string message,
    CancellationToken token,
    TextWriter log)
{
    await log.WriteLineAsync("Job starting");
    await Task.Delay(TimeSpan.FromDays(1), token);
    await log.WriteLineAsync("Job completed");
}
```

クラスまたはメソッド レベルで Timeout 属性を適用して、`JobHostConfiguration.FunctionTimeout` を使用してグローバル タイムアウトを指定することができます。 クラスまたはメソッド レベルのタイムアウトは、グローバル タイムアウトをオーバーライドします。

## <a name="singleton-attribute"></a>Singleton 属性

ホスト Web アプリの複数のインスタンスがある場合でも、関数の 1 つのインスタンスのみを実行するには [Singleton](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/SingletonAttribute.cs) 属性を使用します。 これは、[分散ロック](#viewing-lease-blobs)を実装することにより行われます。

次の例では、常に `ProcessImage` 関数の単一のインスタンスのみが実行されます。

```cs
[Singleton]
public static async Task ProcessImage([BlobTrigger("images")] Stream image)
{
     // Process the image
}
```

### <a name="singletonmodelistener"></a>SingletonMode.Listener

一部のトリガーには、同時実行管理の組み込みサポートがあります。

* **QueueTrigger** - `JobHostConfiguration.Queues.BatchSize` を 1 に設定します。
* **ServiceBusTrigger** - `ServiceBusConfiguration.MessageOptions.MaxConcurrentCalls` を 1 に設定します。
* **FileTrigger** - `FileProcessor.MaxDegreeOfParallelism` を 1 に設定します。

これらの設定を使用すると、単一のインスタンスで、関数がシングルトンとして実行されるようになります。 Web アプリが複数のインスタンスにスケール アウトする際に、関数の単一のインスタンスのみが実行されていることを確認するには、関数にリスナー レベルの Singleton ロック (`[Singleton(Mode = SingletonMode.Listener)]`) を適用します。 リスナー ロックは、JobHost の起動時に取得されます。 3 つのスケール アウト インスタンスのすべてが同時に開始すると、1 つのインスタンスのみがロックを取得して、1 つのみがリスナーを開始します。

### <a name="scope-values"></a>スコープ値

シングルトンに**スコープ式/値**を指定できます。これにより、そのスコープの関数のすべての実行がシリアル化されることが保証されます。 この方法で詳細なロックを実装すると、要件によって指示されたように他の呼び出しをシリアル化すると同時に、関数のある程度の並列処理が可能になります。 たとえば、次の例では、スコープ式は、受信メッセージの `Region` 値 にバインドしています。 リージョンが "東部"、"東部"、および "西部" である 3 つのメッセージがキューに含まれている場合、リージョンが "東部" であるメッセージは連続的に実行され、リージョンが "西部" であるメッセージはこれらと並行して実行されます。

```csharp
[Singleton("{Region}")]
public static async Task ProcessWorkItem([QueueTrigger("workitems")] WorkItem workItem)
{
     // Process the work item
}

public class WorkItem
{
     public int ID { get; set; }
     public string Region { get; set; }
     public int Category { get; set; }
     public string Description { get; set; }
}
```

### <a name="singletonscopehost"></a>SingletonScope.Host

ロックの既定スコープは `SingletonScope.Function` です。つまり、ロック スコープ (BLOB のリース パス) は、関数の完全修飾名に関連付けられています。 関数にわたってロックするには、`SingletonScope.Host` を指定して、同時に実行したくない関数すべてと同じスコープ ID 名を使用します。 次の例では、  `AddItem` または `RemoveItem`の1 つのみのインスタンス が一度に実行されます。

```charp
[Singleton("ItemsLock", SingletonScope.Host)]
public static void AddItem([QueueTrigger("add-item")] string message)
{
     // Perform the add operation
}

[Singleton("ItemsLock", SingletonScope.Host)]
public static void RemoveItem([QueueTrigger("remove-item")] string message)
{
     // Perform the remove operation
}
```

### <a name="viewing-lease-blobs"></a>リース BLOB のビュー

WebJobs SDK は、バックグラウンドで [Azure BLOB リース](../storage/common/storage-concurrency.md#pessimistic-concurrency-for-blobs)を使用して、分散ロックを実装します。 Singleton により使用されるリース BLOB は、「locks」のパスにある `AzureWebJobsStorage` ストレージ アカウント内の `azure-webjobs-host` コンテナーにあります。 たとえば、先に示した最初の `ProcessImage` の例のリース BLOB パスは、`locks/061851c758f04938a4426aa9ab3869c0/WebJobs.Functions.ProcessImage` です。 どのパスにも JobHost ID が含まれています。この場合は、061851c758f04938a4426aa9ab3869c0 になります。

## <a name="async-functions"></a>Async 関数

Async 関数のコードを書く方法については、Azure Functions ドキュメントの[Async 関数](../azure-functions/functions-dotnet-class-library.md#async)を参照してください。

## <a name="cancellation-tokens"></a>キャンセル トークン

キャンセル トークンを処理する方法については、Azure Functions ドキュメントの[キャンセル トークンと正常なシャットダウン](../azure-functions/functions-dotnet-class-library.md#cancellation-tokens)を参照してください。

## <a name="multiple-instances"></a>複数のインスタンス

Web アプリが複数のインスタンス上で稼働している場合、継続的な Web ジョブは各インスタンスで実行され、トリガーをリッスンして関数の呼び出しを行います。 各種のトリガー バインドは、インスタンス間で協調して作業を効率的に共有するよう設計されているので、より多くのインスタンスにスケール アウトすると、より多くの負荷を処理することができます。

キューと BLOB トリガーは、関数がキュー メッセージや BLOB を 1 回を超えて処理することを自動的に防止します。関数は冪等である必要はありません。

タイマー トリガーは、指定された時刻に常に 1 つを超える関数のインスタンスが実行しないように、自動的に タイマーの1 つのインスタンスのみが実行するようにします。

ホスト Web アプリの複数のインスタンスがある場合でも、関数の 1 つのインスタンスのみを実行するには [Singleton](#singleton) 属性を使用できます。
    
## <a name="filters"></a>フィルター 

関数のフィルター (プレビュー) は、独自のロジックで WebJobs 実行パイプラインをカスタマイズする方法を提供します。 フィルターは [ASP.NET Core フィルター](https://docs.microsoft.com/aspnet/core/mvc/controllers/filters)に似ています。 フィルターは、関数またはクラスに適用される宣言型の属性として実装することができます。 詳細については、[関数フィルター](https://github.com/Azure/azure-webjobs-sdk/wiki/Function-Filters)を参照してください。

## <a name="logging-and-monitoring"></a>ログ記録と監視

ASP.NET 用に開発されたログ記録フレームワークをお勧めします。[WebJobs SDK を使い始める](webjobs-sdk-get-started.md)のページに使用法が記載されています。 

### <a name="log-filtering"></a>ログのフィルタリング

`ILogger` インスタンスによって作成されたすべてのログには、関連付けられた `Category` と `Level` があります。 [LogLevel](https://docs.microsoft.com/aspnet/core/api/microsoft.extensions.logging.loglevel#Microsoft_Extensions_Logging_LogLevel) は列挙型で、整数コードは次のような相対的な重要度を示します。

|LogLevel    |コード|
|------------|---|
|Trace       | 0 |
|デバッグ       | 1 |
|情報 | 2 |
|警告     | 3 |
|Error       | 4 |
|重大    | 5 |
|なし        | 6 |

各カテゴリは個別に特定の [LogLevel](https://docs.microsoft.com/aspnet/core/api/microsoft.extensions.logging.loglevel) でフィルターをかけることができます。 たとえば、BLOB トリガー処理のすべてのログを表示するが、それ以外に関しては `Error` 以降のみを表示するなどが可能です。

フィルタリング規則の指定を容易にできるように、WebJobs SDK は、Application Insights や Console を含む多くの既存のログ プロバイダーに渡すことができる `LogCategoryFilter` を提供します。

`LogCategoryFilter` には、`Information` の初期値を持つ `Default` プロパティがあります。つまり、`Information`、`Warning`、`Error`、`Critical` のレベルを持つすべてのメッセージがログされますが、`Debug` や `Trace` のレベルのメッセージはフィルタされるということです。

`CategoryLevels` プロパティでは、ログ出力を微調整できるように、特定のカテゴリのログ レベルを指定することができます。 `CategoryLevels` ディクショナリ内で一致するものが見つからない場合、メッセージをフィルターするかどうかを決定する際に、フィルターが `Default` 値にフォールバックします。

次の例は、既定値で `Warning` レベルのすべてのログをフィルタリングするフィルターを構築します。 `Function` や `Host.Results` カテゴリは、`Error` レベルでフィルタリングされます。 `LogCategoryFilter` は現在のカテゴリを、登録されているすべての `CategoryLevels` と比較して、最長一致を選択します。 つまり、`Host.Triggers` に登録されている `Debug` レベルは、`Host.Triggers.Queue` や `Host.Triggers.Blob` に一致するということです。 これにより、カテゴリを 1 つずつ追加しなくても、より幅広いカテゴリを制御できます。

```csharp
var filter = new LogCategoryFilter();
filter.DefaultLevel = LogLevel.Warning;
filter.CategoryLevels[LogCategories.Function] = LogLevel.Error;
filter.CategoryLevels[LogCategories.Results] = LogLevel.Error;
filter.CategoryLevels["Host.Triggers"] = LogLevel.Debug;

config.LoggerFactory = new LoggerFactory()
    .AddApplicationInsights(instrumentationKey, filter.Filter)
    .AddConsole(filter.Filter);
```

### <a name="custom-telemetry-for-application-insights"></a>Application Insights のカスタム テレメトリ

内部的には、WebJobs SDK 用の Application Insights プロバイダーに作成された `TelemetryClient` は、[ServerTelemetryChannel](https://github.com/Microsoft/ApplicationInsights-dotnet/blob/develop/src/ServerTelemetryChannel/ServerTelemetryChannel.cs) を使用します。 Application Insights のエンドポイントが使用できない、または着信要求のスロットリングが行われている場合、このチャンネルが [Web アプリのファイル システムで要求を保存して、後でこれらを再送信](http://apmtips.com/blog/2015/09/03/more-telemetry-channels)します。

`TelemetryClient` は、`ITelemetryClientFactory` を実装するクラスにより作成されます。 既定値で、これは [DefaultTelemetryClientFactory](https://github.com/Azure/azure-webjobs-sdk/blob/dev/src/Microsoft.Azure.WebJobs.Logging.ApplicationInsights/DefaultTelemetryClientFactory.cs) です。

Application Insights パイプラインの一部を変更する場合、独自の `ITelemetryClientFactory` を使用することができます。すると、ホストはクラスを使用して `TelemetryClient` を構築します。 たとえば、このコードは `DefaultTelemetryClientFactory` をオーバーライドして、`ServerTelemetryChannel` のプロパティを変更します。

```csharp
private class CustomTelemetryClientFactory : DefaultTelemetryClientFactory
{
    public CustomTelemetryClientFactory(string instrumentationKey, Func<string, LogLevel, bool> filter)
        : base(instrumentationKey, new SamplingPercentageEstimatorSettings(), filter)
    {
    }

    protected override ITelemetryChannel CreateTelemetryChannel()
    {
        ServerTelemetryChannel channel = new ServerTelemetryChannel();

        // change the default from 30 seconds to 15 seconds
        channel.MaxTelemetryBufferDelay = TimeSpan.FromSeconds(15);

        return channel;
    }
}
```

SamplingPercentageEstimatorSettings オブジェクトは、[アダプティブ サンプリング](https://docs.microsoft.com/azure/application-insights/app-insights-sampling#adaptive-sampling-at-your-web-server)を構成します。 つまり、特定の大規模なシナリオで、App Insights はテレメトリ データの一部のサブセットをサーバーに送信するということです。

テレメトリ ファクトリを一度作成すると、Application Insights のログ記録プロバイダーに渡します。

```csharp
var clientFactory = new CustomTelemetryClientFactory(instrumentationKey, filter.Filter);

config.LoggerFactory = new LoggerFactory()
    .AddApplicationInsights(clientFactory);
```

## <a id="nextsteps"></a> 次のステップ

このガイドでは、WebJobs SDK を操作するための一般的なシナリオの処理方法を示すコード スニペットを提供しました。 完全なサンプルは、[azure-webjobs-sdk-samples](https://github.com/Azure/azure-webjobs-sdk-samples) を参照してください。