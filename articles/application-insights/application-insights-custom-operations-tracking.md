---
title: "Azure Application Insights .NET SDK でカスタム操作を追跡する | Microsoft Docs"
description: "Azure Application Insights .NET SDK でカスタム操作を追跡する"
services: application-insights
documentationcenter: .net
author: SergeyKanzhelev
manager: carmonm
ms.service: application-insights
ms.workload: TBD
ms.tgt_pltfrm: ibiza
ms.devlang: multiple
ms.topic: article
ms.date: 06/31/2017
ms.author: sergkanz
ms.translationtype: HT
ms.sourcegitcommit: bde1bc7e140f9eb7bb864c1c0a1387b9da5d4d22
ms.openlocfilehash: 883750c939aa3bd605189f513e5ce74642f91709
ms.contentlocale: ja-jp
ms.lasthandoff: 07/21/2017

---

# <a name="tracking-custom-operations-with-application-insights-net-sdk"></a>Application Insights .NET SDK でカスタム操作を追跡する

Application Insights SDK は、受信 HTTP 要求とその依存サービス呼び出し (HTTP 要求、SQL クエリなど) を自動的に追跡します。たとえば複数のマイクロ サービスから成るアプリケーションがあるとします。 要求とその依存サービスを追跡してそれらの相関関係を把握できれば、アプリケーション全体の応答性と信頼性をすべてのマイクロ サービスにわたって見通すことができます。 今後その対応範囲は徐々に拡大され、広く使われている他のプラットフォームやフレームワークについても自動的に収集できるようになります。 

ただし、一律に対応することのできない類のアプリケーション パターンも存在します。 そうしたパターンを適切に監視するためには、手動によるコードのインストルメンテーションが必要となります。 この記事では、手動によるインストルメンテーションが必要ないくつかのパターンについて取り上げます。 カスタム キューの処理や時間がかかるタスクのバックグラウンド実行もその 1 つです。

このドキュメントでは、ApplicationInsights SDK を使ってカスタム操作を追跡する方法についての指針を示します。

このドキュメントは、次の内容に関連しています。
- Application Insights for .NET (Base SDK) バージョン `2.4+`
- Application Insights for Web Applications バージョン `2.4+` (ASP.NET を実行する Web アプリケーション用)
- Application Insights for AspNetCore バージョン `2.1+`

## <a name="overview"></a>概要
私たちは、アプリケーションによって実行される論理上の処理を、その操作によって理解します。 操作には名前があり、開始時刻や実行時間があり、また実行コンテキスト (ユーザー名、プロパティ、結果など) があります。 `A` という操作が `B` という操作によって開始された場合、操作 `B` は `A` の親に設定されます。  どのような操作も、親となる操作は 1 つだけですが、子の操作は複数存在することがあります。 操作とテレメトリの関連付けについて詳しくは、[こちら](application-insights-correlation.md)をご覧ください。

Application Insights .NET SDK では、抽象クラス [OperationTelemetry](https://github.com/Microsoft/ApplicationInsights-dotnet/blob/develop/src/Core/Managed/Shared/Extensibility/Implementation/OperationTelemetry.cs) とその子孫である [RequestTelemetry](https://github.com/Microsoft/ApplicationInsights-dotnet/blob/develop/src/Core/Managed/Shared/DataContracts/RequestTelemetry.cs) と [DependencyTelemetry](https://github.com/Microsoft/ApplicationInsights-dotnet/blob/develop/src/Core/Managed/Shared/DataContracts/DependencyTelemetry.cs) によって操作が表されます。

## <a name="incoming-operations-tracking"></a>受信操作の追跡 
Application Insights Web SDK は、IIS パイプラインで実行される ASP.NET アプリケーションとあらゆる ASP.NET Core アプリケーションとを対象に、HTTP 要求を自動的に収集します。 それ以外のプラットフォームとフレームワークについては、コミュニティによってサポートされるソリューションが存在します。 一方、標準のソリューションでも、コミュニティによってサポートされるソリューションでも対応していないアプリケーションについては、独自にインストルメント化することができます。

キューからアイテムを受け取る worker も、独自の追跡が必要なケースです。 キューによっては、そのキューにメッセージを追加する呼び出しが、依存関係として追跡されます。 しかしメッセージの処理を表す上位の操作については、自動的には収集されません。

そのような操作を追跡するには、どうすればよいのでしょうか。

上位レベルで、`RequestTelemetry` を作成し、既知のプロパティを設定する作業が必要となります。操作が完了した後で、テレメトリを追跡するのです。 その具体的な例を次に示します。

### <a name="http-request-in-owin-self-hosted-app"></a>Owin 自己ホスト型アプリにおける HTTP 要求
[HTTP Correlation プロトコル](https://github.com/dotnet/corefx/blob/master/src/System.Diagnostics.DiagnosticSource/src/HttpCorrelationProtocol.md)に従い、このプロトコルで規定されているヘッダーを受信することを前提としましょう。

``` C#
public class ApplicationInsightsMiddleware : OwinMiddleware
{
    private readonly TelemetryClient telemetryClient = new TelemetryClient(TelemetryConfiguration.Active);
    
    public ApplicationInsightsMiddleware(OwinMiddleware next) : base(next) {}

    public override async Task Invoke(IOwinContext context)
    {
        // Let's create and start RequestTelemetry
        var requestTelemetry = new RequestTelemetry
        {
            Name = $"{context.Request.Method} {context.Request.Uri.GetLeftPart(UriPartial.Path)}"
        };

        // if there is Request-Id recevied from the upstream service, set telemetry context accordingly
        if (context.Request.Headers.ContainsKey("Request-Id"))
        {
            var requestId = context.Request.Headers.Get("Request-Id");
            // get the operation id from the Request-Id (if you follow the 'Http Protocol for Correlation')
            requestTelemetry.Context.Operation.Id = GetOperationId(requestId);
            requestTelemetry.Context.Operation.ParentId = requestId;
        }

        // StartOperation is a helper method that allows to correlate 
        // current operations with nested operations/telemetry
        // and initializes start time and duration on telemetry item
        var operation = telemetryClient.StartOperation(requestTelemetry);

        // process request
        try
        {
            await Next.Invoke(context);
        }
        catch (Exception e)
        {
            requestTelemetry.Success = false;
            telemetryClient.TrackException(e);
            throw;
        }
        finally
        {
            // update status code and success as appropriate
            if (context.Response != null)
            {
                requestTelemetry.ResponseCode = context.Response.StatusCode.ToString();
                requestTelemetry.Success = context.Response.StatusCode >= 200 && context.Response.StatusCode <= 299;
            }
            else
            {
                requestTelemetry.Success = false;
            }

            // now it's time to stop operation (and track telemetry)
            telemetryClient.StopOperation(operation);
        }
    }
    
    public static string GetOperationId(string id)
    {
        // returns root Id from the '|' to first '.' if any
        int rootEnd = id.IndexOf('.');
        if (rootEnd < 0)
            rootEnd = id.Length;

        int rootStart = id[0] == '|' ? 1 : 0;
        return id.Substring(rootStart, rootEnd - rootStart);
    }
}
```

HTTP Correlation プロトコルでは `Correlation-Context` ヘッダーも宣言されていますが、ここでは説明を単純化するために省略しています。

## <a name="queue-instrumentation"></a>キューのインストルメンテーション
HTTP 通信に関して、Microsoft は、相関情報を受け渡しするためのプロトコルを作成しました。 メッセージと共に補足的なメタデータを渡すことができるかどうかは、キューのプロトコルによって異なります。

### <a name="service-bus-queue"></a>Service Bus キュー
[Service Bus キュー](../service-bus-messaging/index.md)では、メッセージと共にプロパティ バッグを渡すことができます。そこでここでは、それを使用して相関 ID を受け渡しすることにします。

Service Bus キューには、TCP ベースのプロトコルが使用されています。 キューの操作は、Application Insights で自動的には追跡されないので、独自に追跡することになります。 デキュー操作はプッシュ型の API であり、追跡することはできません。

#### <a name="enqueue"></a>エンキュー

```C#
public async Task Enqueue(string payload)
{
    // StartOperation is a helper method that initializes telemetry item
    // and allows to correlate this operation with its parent and children
    var operation = telemetryClient.StartOperation<DependencyTelemetry>("enqueue " + queueName);
    operation.Telemetry.Type = "Queue";
    operation.Telemetry.Data = "Enqueue " + queueName;

    var message = new BrokeredMessage(payload);
    // ServiceBus Queue allows to pass property bag along with the message
    // we will use them to pass our correlation identifiers (and other context)
    // to the consumer
    message.Properties.Add("ParentId", operation.Telemetry.Id);
    message.Properties.Add("RootId", operation.Telemetry.Context.Operation.Id);

    try
    {
        await queue.SendAsync(message);
        
        // set operation.Telemetry Success and ResponseCode here
        operation.Telemetry.Success = true;
    }
    catch (Exception e)
    {
        telemetryClient.TrackException(e);
        // set operation.Telemetry Success and ResponseCode here
        operation.Telemetry.Success = false;
        throw;
    }
    finally
    {
        telemetryClient.StopOperation(operation);
    }
}
```

#### <a name="process"></a>Process
```C#
public async Task Process(BrokeredMessage message)
{
    // once the message is taken from the queue, create ReqeustTelemetry to track its processing
    // it may also make sense to get name from the message
    RequestTelemetry requestTelemetry = new RequestTelemetry { Name = "Dequeue " + queueName };

    var rootId = message.Properties["RootId"].ToString();
    var parentId = message.Properties["ParentId"].ToString();
    // get the operation id from the Request-Id (if you follow the 'Http Protocol for Correlation')
    requestTelemetry.Context.Operation.Id = rootId;
    requestTelemetry.Context.Operation.ParentId = parentId;

    var operation = telemetryClient.StartOperation(requestTelemetry);

    try
    {
        await ProcessMessage();
    }
    catch (Exception e)
    {
        telemetryClient.TrackException(e);
        throw;
    }
    finally
    {
        // update status code and success as appropriate
        telemetryClient.StopOperation(operation);
    }
}
```

### <a name="azure-storage-queue"></a>Azure Storage キュー
[Azure Storage キュー](../storage/storage-dotnet-how-to-use-queues.md)の操作を追跡し、プロデューサー、コンシューマー、Azure Storage 間でテレメトリを相互に関連付ける例を次に示します。 

Azure Storage キューには HTTP API があり、キューの呼び出しはすべて HTTP 要求の ApplicationInsights DependencyCollector によって追跡されます。
`applicationInsights.config` に `Microsoft.ApplicationInsights.DependencyCollector.HttpDependenciesParsingTelemetryInitializer` が存在することを確認するか、[こちら](app-insights-api-filtering-sampling.md)の説明に従ってプログラムから追加してください。

ApplicationInsights を手動で構成する場合は、次のように `Microsoft.ApplicationInsights.DependencyCollector.DependencyTrackingTelemetryModule` を作成して初期化する必要があります。
 
``` C#
DependencyTrackingTelemetryModule module = new DependencyTrackingTelemetryModule();

// you can prevent correlation header injection to some domains by adding it to the excluded list.
// Make sure you add Azure Storage endpoint, otherwise you may experience request signature validation issues on the Storage service side
module.ExcludeComponentCorrelationHttpHeadersOnDomains.Add("core.windows.net");
module.Initialize(TelemetryConfiguration.Active);

// do not forget to dispose module during application shutdown
```

ApplicationInsights の操作 ID と Azure Storage RequestId とを相互に関連付けることもできます。 ストレージ要求のクライアントおよびサーバー要求 ID を設定したり取得したりする方法については、[こちらの記事](../storage/storage-monitoring-diagnosing-troubleshooting.md#end-to-end-tracing)をご覧ください。

#### <a name="enqueue"></a>エンキュー
Azure Storage キューは HTTP API をサポートしているため、キューを使ったすべての操作は自動的に ApplicationInsights によって追跡されます。 多くのケースには、このインストルメンテーションで対応することができます。
ただし、コンシューマー側のトレースとプロデューサー側のトレースとを相互に関連付けるためには、"HTTP Correlation プロトコル" に見られるような、関連付けのための何らかのコンテキストが必要です。 

下の例では、オプションの `Enqueue` 操作を追跡しています。 次のことができます。
 - 発生した再試行を相互に関連付ける。すべての再試行には、`Enqueue` 操作という共通の親が 1 つ存在します。 また、再試行が受信要求の子として追跡されることもあります。 したがってキューに対する論理要求が複数存在した場合、どの呼び出しが再試行されたかを見極めることは難しい可能性があります。
 - Log Azure Storage ログ (必要な場合) と ApplicationInsights のテレメトリとを相互に関連付ける。

`Enqueue` 操作は、受信 HTTP 要求などの "親" 操作の子であり、"Http" 依存関係呼び出しは `Enqueue` 操作の子で、かつ受信要求の孫となります。

```C#
public async Task Enqueue(CloudQueue queue, string message)
{
    var operation = telemetryClient.StartOperation<DependencyTelemetry>("enqueue " + queue.Name);
    operation.Telemetry.Type = "Queue";
    operation.Telemetry.Data = "Enqueue " + queue.Name;

    // MessagePayload represents your custom message and also serializes correlation identifiers into payload
    // e.g. if you choose to pass payload serialized to json, it may look like
    // {'RootId' : 'some-id', 'ParentId' : '|some-id.1.2.3.', 'message' : 'your message to process'}
    var jsonPayload = JsonConvert.SerializeObject(new MessagePayload
    {
        RootId = operation.Telemetry.Context.Operation.Id,
        ParentId = operation.Telemetry.Id,
        Payload = message
    });
    
    CloudQueueMessage queueMessage = new CloudQueueMessage(jsonPayload);

    // Add operation.Telemetry.Id to the OperationContext to correlate Azure Storage logs and ApplciationInsights telemetry
    OperationContext context = new OperationContext { ClientRequestID = operation.Telemetry.Id};

    try
    {
        await queue.AddMessageAsync(queueMessage, null, null, new QueueRequestOptions(), context);
    }
    catch (StorageException e)
    {
        operation.Telemetry.Properties.Add("AzureServiceRequestID", e.RequestInformation.ServiceRequestID);
        operation.Telemetry.Success = false;
        operation.Telemetry.ResultCode = e.RequestInformation.HttpStatusCode.ToString();
        telemetryClient.TrackException(e);
    }
    finally
    {
        // update status code and success as appropriate
        telemetryClient.StopOperation(operation);
    }
}  
```

アプリケーションから報告されるテレメトリの量を減らしたい場合や、その他の理由で `enqueue` 操作を追跡したくない場合は、`Activity` API を直接使用することができます。

- ApplicationInsights 操作を開始する代わりに新しい `Activity` を作成 (して開始) します (操作の名前以外、プロパティを割り当てる必要はありません)。
- `operation.Telemetry.Id` ではなく `yourActivity.Id` をメッセージ ペイロードに対してシリアル化します。 `Activity.Current.Id` を使用してもかまいません。


#### <a name="dequeue"></a>デキュー
`Enqueue` と同様、Azure Storage キューに対する実際の HTTP 要求は ApplicationInsights によって自動的に追跡されます。 ただし `Enqueue` 操作の発生源は親のコンテキスト、たとえば "受信" 要求のコンテキストであると推測できます。 そのような操作 (その HTTP 要素も含む) は、Application Insights SDK によって自動的に親要求や同じスコープ内で報告される他のテレメトリと相互に関連付けられます。

一方、`Dequeue` 操作には注意が必要です。Application Insights SDK は HTTP 要求を自動的に追跡しますが、関連付けのコンテキストはメッセージが解析されるまでわかりません。 メッセージを取得する HTTP 要求と他のテレメトリとを相互に関連付けることは不可能です。

多くの場合、キューに対する HTTP 要求は、他のトレースと相互に関連付けて役立てることもできます。 その具体的な方法を次の例で紹介します。

``` C#
public async Task<MessagePayload> Dequeue(CloudQueue queue)
{
    var telemetry = new DependencyTelemetry
    {
        Type = "Queue",
        Name = "Dequeue " + queue.Name
    };

    telemetry.Start();

    try
    {
        var message = await queue.GetMessageAsync();

        if (message != null)
        {
            var payload = JsonConvert.DeserializeObject<MessagePayload>(message.AsString);

            // if there is a message, we want to correlate Dequeue operation with processing
            // However we will only know what correlation Id to use AFTER we will get it from the message
            // So we will report telemetry once we know the Ids.
            telemetry.Context.Operation.Id = payload.RootId;
            telemetry.Context.Operation.ParentId = payload.ParentId;

            // delete message
            return payload;
        }
    }
    catch (StorageException e)
    {
        telemetry.Properties.Add("AzureServiceRequestID", e.RequestInformation.ServiceRequestID);
        telemetry.Success = false;
        telemetry.ResultCode = e.RequestInformation.HttpStatusCode.ToString();
        telemetryClient.TrackException(e);
    }
    finally
    {
        // update status code and success as appropriate
        telemetry.Stop();
        telemetryClient.Track(telemetry);
    }

    return null;
}
```

#### <a name="process"></a>Process

次の例では、受信 HTTP 要求をトレースするときと同様の方法で "受信" メッセージをトレースしています。

```C#
public async Task Process(MessagePayload message)
{
    // once the message is dequeued from the queue, create ReqeustTelemetry to track it's processing
    RequestTelemetry requestTelemetry = new RequestTelemetry { Name = "Dequeue " + queueName };
    // it may also make sense to get name from the message
    requestTelemetry.Context.Operation.Id = message.RootId;
    requestTelemetry.Context.Operation.ParentId = message.ParentId;

    var operation = telemetryClient.StartOperation(requestTelemetry);

    try
    {
        await ProcessMessage();
    }
    catch (Exception e)
    {
        telemetryClient.TrackException(e);
        throw;
    }
    finally
    {
        // update status code and success as appropriate
        telemetryClient.StopOperation(operation);
    }
}
```

他のキュー操作も同じようにインストルメント化することができます。 Peek 操作をインストルメント化する場合は、デキューと同様の方法を使用する必要があります。 キューの管理操作をインストルメント化する必要はありません。 HTTP などの各種操作は、Application Insights によって追跡されます。ほとんどのケースはそれで十分です。

メッセージの削除をインストルメント化するときは、必ず操作 (関連付け) ID を設定してください。 さらに別の方法として、`Activity` API を使用することもできます。その場合は、テレメトリ項目に対する操作 ID を自分で設定する必要はありません。ApplicationInsights によって自動的に行われます。

- キューから項目を取得した後、新しい `Activity` を作成します。
- `Activity.SetParentId(message.ParentId)` を使用して、コンシューマーとプロデューサーのログを相互に関連付けます。
- `Activity` を開始します。
- `Start/StopOperation` ヘルパーを使用して、Dequeue、Process、Delete の各操作を追跡します。 追跡は、同じ非同期制御フロー (実行コンテキスト) から行ってください。 そうすることで、相互の関連付けが適切に行われます。
- その後、`Activity` を停止します。
- `Start/StopOperation` を使用するか、Track テレメトリを直接呼び出します。 

### <a name="batch-processing"></a>バッチ処理
キューによっては、1 回の要求で複数のメッセージをデキューすることができます。しかし、そのようなメッセージの処理は、互いに独立していて異なる論理操作に属していると考えられます。
その場合、`Dequeue` 操作を特定のメッセージ処理に関連付けることはできません。

当然それぞれのメッセージ処理は、その独自の非同期制御フローの中で行われることになります。 この点については、「[出力方向の依存関係の追跡](#outgoing-dependencies-tracking)」セクションで詳しく説明します。

## <a name="long-running-background-tasks"></a>長時間実行されるバックグラウンド タスク
アプリケーションの中には、ユーザーの要求によって引き起こされる、長時間実行される操作を開始するものがあります。 トレース/インストルメンテーションの観点から見ると、要求や依存関係のインストルメンテーションと変わりません。 

``` C#
async Task BackgroundTask()
{
    var operation = telemetryClient.StartOperation<RequestTelemetry>(taskName);
    operation.Telemetry.Type = "Background";
    try
    {
        int progress = 0;
        while (progress < 100)
        {
            // process task
            telemetryClient.TrackTrace($"done {progress++}%");
        }
        // update status code and success as appropriate
    }
    catch (Exception e)
    {
        telemetryClient.TrackException(e);
        // update status code and success as appropriate
        throw;
    }
    finally
    {
        telemetryClient.StopOperation(operation);
    }
}
```

この例では、`telemetryClient.StartOperation` を使用して `RequestTelemetry` を作成し、関連付けのコンテキストを設定しています。 たとえば親操作として、操作のスケジュールが設定された受信要求があるとしましょう。 `BackgroundTask` は、受信要求と同じ非同期制御フローで開始される限り、その親操作と関連付けられます。 `BackgroundTask` とそこに入れ子にされているすべてのテレメトリ項目は自動的に、それを引き起こした要求と関連付けられます (要求の終了後であっても同様)。

いずれの操作 (アクティビティ) も関連付けられていないバックグラウンド スレッドからタスクが開始された場合、`BackgroundTask` には親が存在しません。 ただし入れ子にされた操作が存在する可能性はあり、そのタスクから報告されるテレメトリ項目はすべて、`BackgroundTask` で作成された `RequestTelemetry` に関連付けられます。

## <a name="outgoing-dependencies-tracking"></a>出力方向の依存関係の追跡
独自の "依存関係" の種類や、ApplicationInsights でサポートされていない何らかの操作を追跡しなければならなくなったとしましょう。

そうしたカスタム追跡の例として、Service Bus キューまたは Azure Storage キューの `Enqueue` メソッドが挙げられます。

カスタム依存関係の追跡に使用される一般的な手法は次のとおりです。
- 関連付けに必要な `DependencyTelemetry` のプロパティなど何らかのプロパティ (開始のタイムスタンプ、実行時間) に値を設定する `TelemetryClient.StartOperation` (拡張機能) メソッドを呼び出します。
- その他 `DependencyTelemetry` のカスタム プロパティ (名前やその他の必要なコンテキスト) を設定します。
- 依存関係の呼び出しを行ってから待機します。
- 完了したら、`StopOperation` で操作を停止します。
- 例外を処理します。

`StopOperation` で停止されるのは、開始済みの操作だけです。停止しようとしている操作が現在実行中でなければ、StopOperation は何も行いません。 そのような状況は、同じ実行コンテキストで複数の操作を並行して開始した場合に起こる可能性があります。

```C#
var firstOperation = telemetryClient.StartOperation<DependencyTelemetry>("task 1");
var firstOperation = telemetryClient.StartOperation<DependencyTelemetry>("task 1");
var firstTask = RunMyTaskAsync();

var secondOperation = telemetryClient.StartOperation<DependencyTelemetry>("task 2");
var secondTask = RunMyTaskAsync();

await firstTask;

// this will do nothing and will not report telemetry for the first operation
// as currently secondOperation is active
telemetryClient.StopOperation(firstOperation); 

await secondTask;
```

したがって対象となるタスクは必ず、その独自のコンテキストで `StartOperation` を呼び出して実行する必要があります。
```C#
public async Task RunMyTaskAsync()
{
    var operation = telemetryClient.StartOperation<DependencyTelemetry>("task 1");
    try 
    {
        var myTask = await StartMyTaskAsync();
        // update status code and success as appropriate
    }
    catch(...) 
    {
        // update status code and success as appropriate
    }
    finally 
    {
        telemetryClient.StopOperation(operation);
    }
}
```

## <a name="next-steps"></a>次のステップ

- Application Insights における[テレメトリの相関付け](application-insights-correlation.md)について基本的な知識を身に付けます。
- Application Insights の型とデータ モデルについては、[データ モデル](application-insights-data-model.md)に関するページを参照してください。
- カスタムの[イベントとメトリック](app-insights-api-custom-events-metrics.md)を Application Insights にレポートします。
- 標準的なコンテキスト プロパティ コレクションの[構成](app-insights-configuration-with-applicationinsights-config.md#telemetry-initializers-aspnet)を確認します。
- テレメトリを相互に関連付ける方法を [System.Diagnostics.Activity ユーザー ガイド](https://github.com/dotnet/corefx/blob/master/src/System.Diagnostics.DiagnosticSource/src/ActivityUserGuide.md)で確認します。

