<properties 
	pageTitle="カスタムのイベントとメトリックのための Application Insights API | Microsoft Azure" 
	description="デバイスまたはデスクトップ アプリケーション、Web ページまたはサービスに数行のコードを追加し、使用状況を追跡し、問題を診断します。" 
	services="application-insights"
    documentationCenter="" 
	authors="alancameronwills" 
	manager="douge"/>
 
<tags 
	ms.service="application-insights" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="ibiza" 
	ms.devlang="multiple" 
	ms.topic="article" 
	ms.date="09/11/2016" 
	ms.author="awills"/>

# カスタムのイベントとメトリックのための Application Insights API 

*Application Insights はプレビュー段階です。*

アプリケーションに数行のコードを挿入して、ユーザーの行動を調べたり、問題の診断に役立つ情報を取得したりすることができます。デバイスとデスクトップ アプリケーション、Web クライアント、Web サーバーからテレメトリを送信できます。[Visual Studio Application Insights](app-insights-overview.md) コア テレメトリ API を使用すると、カスタムのイベントとメトリック、および独自のバージョンの標準テレメトリを送信できます。この API は、Application Insights の標準のデータ コレクターで使用される API と同じです。

## API の概要

この API は、いくつかの小さな違いは別として、すべてのプラットフォームで一様になっています。

メソッド | 使用対象
---|---
[`TrackPageView`](#page-views) | ページ、画面、ブレード、フォーム
[`TrackEvent`](#track-event) | ユーザー アクションとその他のイベント。ユーザーの行動を追跡するために、またはパフォーマンスを監視するために使用されます。
[`TrackMetric`](#track-metric) | キューの長さなど、特定のイベントに関連しないパフォーマンスの測定
[`TrackException`](#track-exception)|例外を記録し、診断に利用します。他のイベントとの関連で例外の発生箇所を追跡し、スタック トレースを調べます。
[`TrackRequest`](#track-request)| サーバー要求の頻度と期間を記録し、パフォーマンス分析に利用します。
[`TrackTrace`](#track-trace)|メッセージを記録し、診断に利用します。サード パーティのログをキャプチャすることもできます。
[`TrackDependency`](#track-dependency)|アプリが依存する外部コンポーネントへの呼び出しの実行時間と頻度をログに記録します。

これらのテレメトリの呼び出しのほとんどに[プロパティとメトリックをアタッチ](#properties)できます。


## <a name="prep"></a>開始する前に

次のことをまだ実行していない場合は、実行します。

* Application Insights SDK をプロジェクトに追加します。
 * [ASP.NET プロジェクト][greenbrown]
 * [Java プロジェクト][java]
 * [各 Web ページの JavaScript][client]

* デバイスまたは Web サーバー コードに次を追加します。

    *C#:* `using Microsoft.ApplicationInsights;`

    *VB:* `Imports Microsoft.ApplicationInsights`

    *Java:* `import com.microsoft.applicationinsights.TelemetryClient;`

## TelemetryClient を作成する

TelemetryClient のインスタンスの作成 (Web ページの JavaScript を除く):

*C#:*

    private TelemetryClient telemetry = new TelemetryClient();

*VB:*

    Private Dim telemetry As New TelemetryClient

*Java*

    private TelemetryClient telemetry = new TelemetryClient();

TelemetryClient はスレッド セーフです。

アプリのモジュールごとに `TelemetryClient` のインスタンスを使用することをお勧めします。たとえば、`TelemetryClient` は、着信 http 要求をレポートするために Web サービス内に 1 つ使用され、ビジネス ロジック イベントを報告するためにミドルウェア クラス内にもう 1 つ使用される場合があります。`TelemetryClient.Context.User.Id` などのプロパティを設定してユーザーとセッションを追跡したり、`TelemetryClient.Context.Device.Id` を設定してコンピューターを識別したりできます。この情報はインスタンスから送信されたすべてのイベントに関連付けられます。


## イベントを追跡する

Application Insights では、*カスタム イベント*はデータ ポイントであり、[メトリックス エクスプローラー][metrics]に集計カウントとして表示することも、[診断検索][diagnostic]で個々の出現として表示することもできます (これは MVC にも他のフレームワークの "イベント" にも関連していません)。

ユーザーが特定の機能を使用する頻度、特定の目標を達成する頻度、または特定の種類の間違いを起こす頻度を数えるには、TrackEvent をコードに挿入します。

たとえば、ゲーム アプリで、ユーザーが勝利したときにイベントを送信します。

*JavaScript*

    appInsights.trackEvent("WinGame");

*C#*
    
    telemetry.TrackEvent("WinGame");

*VB*


    telemetry.TrackEvent("WinGame")

*Java*

    telemetry.trackEvent("WinGame");


### Azure ポータルでイベントを表示する

イベントの数を表示するには、[[メトリックス エクスプローラー]](app-insights-metrics-explorer.md) ブレードを開き、新しいグラフを追加して、[イベント] を選択します。

![](./media/app-insights-api-custom-events-metrics/01-custom.png)

さまざまなイベントの数を比較するには、グラフの種類を [グリッド] に設定し、イベント名でグループ化します。

![](./media/app-insights-api-custom-events-metrics/07-grid.png)


グリッドでイベント名をクリックすると、そのイベントの個々の発生の情報が表示されます。

![イベントをドリルスルーする](./media/app-insights-api-custom-events-metrics/03-instances.png)

任意のインスタンスをクリックすると、詳細が表示されます。

検索またはメトリックス エクスプローラーで特定のイベントを対象にするには、ブレードのフィルターを対象となるイベントの名前に設定します。

![[フィルター] を開き、イベント名を展開して、1 つ以上の値を選択する](./media/app-insights-api-custom-events-metrics/06-filter.png)

## メトリックを追跡する

TrackMetric を使用し、特定のイベントに関連付けられていないメトリックを送信します。たとえば、一定の間隔でキューの長さを監視できます。

メトリックはメトリック エクスプローラーに統計グラフとして表示されますが、診断検索で個別のメトリックを検索することはできません。

メトリック値を正しく表示するには、0 以上にする必要があります。


*JavaScript*

    appInsights.trackMetric("Queue", queue.Length);

*C#*

    telemetry.TrackMetric("Queue", queue.Length);

*VB*

    telemetry.TrackMetric("Queue", queue.Length)

*Java*

    telemetry.trackMetric("Queue", queue.Length);

実際には、バックグラウンド スレッドでこれを行うことがあります。

*C#*

    private void Run() {
     var appInsights = new TelemetryClient();
     while (true) {
      Thread.Sleep(60000);
      appInsights.TrackMetric("Queue", queue.Length);
     }
    }


結果を表示するには、メトリックス エクスプローラーを開き、新しいグラフを追加します。メトリックを表示するように設定します。

![新しいグラフを追加するか、グラフを選択して、[カスタム] の下でメトリックを選択する](./media/app-insights-api-custom-events-metrics/03-track-custom.png)

使用できる[メトリック数には制限](#limits)があります。

## ページ ビュー

デバイスまたは Web ページ アプリケーションで、各画面または各ページが読み込まれたときに、既定でページ ビュー テレメトリが送信されます。ただし、これを変更し、ページ ビューを追跡する回数を増やしたり、変えたりできます。たとえば、タブまたはブレードを表示するアプリケーションで、ユーザーが新しいブレードを開いたときに「ページ」を追跡します。

![[概要] ブレードの使用状況レンズ](./media/app-insights-api-custom-events-metrics/appinsights-47usage-2.png)

ユーザーとセッションのデータはページ ビューとともにプロパティとして送信されます。そのため、ページ ビューのテレメトリがあれば、ユーザーとセッションのグラフがアクティブになります。

#### カスタム ページ ビュー

*JavaScript*

    appInsights.trackPageView("tab1");

*C#*

    telemetry.TrackPageView("GameReviewPage");

*VB*

    telemetry.TrackPageView("GameReviewPage")


別々の HTML ページ内で複数のタブを使用している場合、URL を指定することもできます。

    appInsights.trackPageView("tab1", "http://fabrikam.com/page1.htm");

#### ページ ビューのタイミング

既定では、"ページ ビューの読み込み時間" として報告される時間は、ブラウザーが要求を送信した時点から、ブラウザーのページ読み込みイベントが呼び出されるまで測定されます。

代わりに、次のいずれかを行うことができます。

* [trackPageView](https://github.com/Microsoft/ApplicationInsights-JS/blob/master/API-reference.md#trackpageview) の呼び出しで明示的な時間を設定する。
 * `appInsights.trackPageView("tab1", null, null, null, durationInMilliseconds);`
* ページ ビューのタイミングの呼び出し (`startTrackPage` と `stopTrackPage`) を使用する。

*JavaScript*

    // To start timing a page:
    appInsights.startTrackPage("Page1");

...

    // To stop timing and log the page:
    appInsights.stopTrackPage("Page1", url, properties, measurements);

最初のパラメーターとして使用する名前は、開始呼び出しと停止呼び出しに関連します。既定値は現在のページ名です。

メトリック エクスプローラーに結果として表示されるページ読み込み時間は、開始呼び出しから停止呼び出しまでの時間から取得されます。実際の時間間隔はユーザーが決定します。

## 要求を追跡する

HTTP 要求を記録するために、サーバー SDK によって使用されます。

Web サービス モジュールが実行されていない状況で要求をシミュレーションする場合に、これを自分で呼び出すこともできます。

*C#*

    // At start of processing this request:

    // Operation Id and Name are attached to all telemetry and help you identify
    // telemetry associated with one request:
    telemetry.Context.Operation.Id = Guid.NewGuid().ToString();
    telemetry.Context.Operation.Name = requestName;
    
    var stopwatch = System.Diagnostics.Stopwatch.StartNew();

    // ... process the request ...

    stopwatch.Stop();
    telemetry.TrackRequest(requestName, DateTime.Now,
       stopwatch.Elapsed, 
       "200", true);  // Response code, success



## 操作コンテキスト

テレメトリ項目に共通の操作 ID を割り当てることで、項目をまとめて関連付けることができます。標準の要求追跡モジュールでは、HTTP 要求を処理するときに、例外や他のイベントに対してこれを実行します。[Search](app-insights-diagnostic-search.md) および [Analytics](app-insights-analytics.md) では、ID を使用して、要求に関連付けられたイベントを簡単に見つけることができます。

ID を設定する最も簡単な方法は、次のパターンを使用して操作コンテキストを設定することです。

    // Establish an operation context and associated telemetry item:
    using (var operation = telemetry.StartOperation<RequestTelemetry>("operationName"))
    {
        // Telemetry sent in here will use the same operation ID.
        ...
        telemetry.TrackEvent(...); // or other Track* calls
        ...
        // Set properties of containing telemetry item - for example:
        operation.Telemetry.ResponseCode = "200";
        
        // Optional: explicitly send telemetry item:
        telemetry.StopOperation(operation);

    } // When operation is disposed, telemetry item is sent.

操作コンテキストを設定するほかに、`StartOperation` は指定したタイプのテレメトリ項目を作成し、操作を破棄するとき、または明示的に `StopOperation` を呼び出す場合に、そのテレメトリ項目を送信します。`RequestTelemetry` をテレメトリ タイプとして使用する場合、その継続時間は、開始から停止までの間の一定の間隔に設定します。

操作コンテキストを入れ子にすることはできません。操作コンテキストが既にある場合は、含まれているすべての項目 (StartOperation で作成された項目を含む) に、その操作コンテキストの ID が関連付けられます。

Search では、操作コンテキストを使用して、関連項目の一覧が作成されます。

![関連項目](./media/app-insights-api-custom-events-metrics/21.png)


## 例外を追跡する

例外を Application Insights に送信して[数え][metrics]、問題の発生頻度を示し、[個々の問題を調べます][diagnostic]。レポートにはスタック トレースが含まれます。

*C#*

    try
    {
        ...
    }
    catch (Exception ex)
    {
       telemetry.TrackException(ex);
    }

*JavaScript*

    try
    {
       ...
    }
    catch (ex)
    {
       appInsights.trackException(ex);
    }

SDK が多数の例外を自動的にキャッチするため、常に TrackException を明示的に呼び出す必要はありません。

* ASP.NET: [例外をキャッチするコードを記述する](app-insights-asp-net-exceptions.md)
* J2EE: [例外は自動的にキャッチされる](app-insights-java-get-started.md#exceptions-and-request-failures)
* JavaScript: 自動的にキャッチされる。自動コレクションを無効にする場合は、Web ページに挿入するコード スニペットに次の行を追加します。

    ```
    ({
      instrumentationKey: "your key"
      , disableExceptionTracking: true
    })
    ```


## トレースを追跡する 

Application Insights に「階層リンクの軌跡」を送信して問題を診断するときに役立ちます。診断データの塊を送信し、[診断検索][diagnostic]で検査できます。

 

[ログ アダプター][trace]はこの API を使用し、ポータルにサード パーティのログを送信します。


*C#*

    telemetry.TrackTrace(message, SeverityLevel.Warning, properties);


メッセージ コンテンツで検索できますが、(プロパティ値とは異なり) フィルター処理はできません。

`message`のサイズ制限はプロパティの制限よりも高くなっています。TrackTrace の利点は、比較的長いデータをメッセージの中に配置できることです。たとえば、その中に POST データをエンコードできます。


加えて、メッセージに重大度レベルを追加することができます。また他のテレメトリと同様、プロパティ値を追加することで、さまざまなトレースの組み合わせをフィルタで抽出したり検索したりすることができます。For example:


    var telemetry = new Microsoft.ApplicationInsights.TelemetryClient();
    telemetry.TrackTrace("Slow database response",
                   SeverityLevel.Warning,
                   new Dictionary<string,string> { {"database", db.ID} });

特定のデータベースに関連した特定の重大度レベルに該当するすべてのメッセージを [[検索]][diagnostic] で簡単に抽出することができます。

## 依存関係の追跡

応答時間と外部コードの呼び出しの成功率を追跡するには、次の呼び出しを使用します。結果は、ポータルの依存関係グラフに表示されます。

```C#

            var success = false;
            var startTime = DateTime.UtcNow;
            var timer = System.Diagnostics.Stopwatch.StartNew();
            try
            {
                success = dependency.Call();
            }
            finally
            {
                timer.Stop();
                telemetry.TrackDependency("myDependency", "myCall", startTime, timer.Elapsed, success);
            }
```

サーバー SDK には、データベースや REST API などに対する依存関係の呼び出しを自動的に検出して追跡する[依存関係モジュール](app-insights-dependencies.md)が含まれています。このモジュールを機能させるには、サーバーにエージェントをインストールする必要があります。この呼び出しは、自動追跡によってキャッチされない呼び出しを追跡する場合、またはエージェントをインストールしない場合に使用します。

標準の依存関係追跡モジュールを無効にするには、[ApplicationInsights.config](app-insights-configuration-with-applicationinsights-config.md) を編集して `DependencyCollector.DependencyTrackingTelemetryModule` への参照を削除します。



## データのフラッシュ

通常、SDK は、ユーザーへの影響を最小限に抑えるために選択した時間帯に、データを送信します。ただし、場合によっては、バッファーをフラッシュする必要が生じることがあります (たとえば、終了するアプリケーションで SDK を使用している場合)。

*C#*

    telemetry.Flush();

    // Allow some time for flushing before shutdown.
    System.Threading.Thread.Sleep(1000);

この関数はインメモリ チャネルでは非同期ですが、[永続化チャネル](app-insights-api-filtering-sampling.md#persistence-channel)の使用を選択した場合は同期になることに注意してください。


## 認証されたユーザー

Web アプリでは、ユーザーは既定で Cookie により識別されます。ユーザーは、別のコンピューターまたはブラウザーからアプリにアクセスしたり、Cookie を削除した場合、複数回カウントされることがあります。

ただし、ユーザーがアプリにサインインしていれば、ブラウザーのコードに認証されたユーザーの ID を設定して、より正確な数値を取得できます。

*JavaScript*

```JS
    // Called when my app has identified the user.
    function Authenticated(signInId) {
      var validatedId = signInId.replace(/[,;=| ]+/g, "_");
      appInsights.setAuthenticatedUserContext(validatedId);
      ...
    }
```

ASP.NET Web MVC アプリケーションでの例:

*Razor*

        @if (Request.IsAuthenticated)
        {
            <script>
                appInsights.setAuthenticatedUserContext("@User.Identity.Name
                   .Replace("\", "\\")"
                   .replace(/[,;=| ]+/g, "_"));
            </script>
        }

ユーザーの実際のサインイン名を使用する必要はありません。必要なのは、そのユーザーに一意の ID であるということだけです。ID には、スペースや `,;=|` を含めることはできません。

ユーザー ID は、セッション Cookie にも設定され、サーバーに送信されます。サーバー SDK がインストールされている場合、認証されたユーザーの ID は、フィルター処理や検索を行えるように、クライアントおよびサーバー テレメトリの両方のコンテキスト プロパティの一部として送信されます。

アカウントにアプリのグループ ユーザーがある場合、アカウントの識別子も渡すことができます (同じ文字制約が適用されます)。


      appInsights.setAuthenticatedUserContext(validatedId, accountId);

[メトリックス エクスプローラー](app-insights-metrics-explorer.md)で、ユーザー、**認証されたアカウント**、**ユーザー アカウント**のグラフを作成できます。

特定のユーザー名とアカウントを持つクライアント データ ポイントを[検索][diagnostic]することもできます。

## <a name="properties"></a>プロパティを使用してデータをフィルター処理、検索、分割する

プロパティと測定値をイベント (およびメトリック、ページ ビュー、その他のテレメトリ データ) に結び付けることができます。

**プロパティ**は、利用状況レポートでテレメトリをフィルター処理するのに使用できる文字列値です。たとえば、アプリケーションで複数のゲームを提供する場合、各イベントにゲームの名前を結び付けると、人気のあるゲームを確認できます。

文字列の長さには約 1 k の制限があります。(大きなデータの塊を送信する場合、[TrackTrace](#track-trace) のメッセージ パラメーターを使用します。)

**メトリック**はグラフィカルに表示できる数値です。たとえば、ゲーマーが達成するスコアに漸増があるかどうかを確認できます。グラフはイベントともに送信されるプロパティ別に分割できます。そのため、ゲームごとに個別グラフまたは積み重ねグラフを表示できます。

メトリック値を正しく表示するには、0 以上にする必要があります。


使用できる[プロパティ、プロパティ値、およびメトリックの数には制限](#limits)があります。


*JavaScript*

    appInsights.trackEvent
      ("WinGame",
         // String properties:
         {Game: currentGame.name, Difficulty: currentGame.difficulty},
         // Numeric metrics:
         {Score: currentGame.score, Opponents: currentGame.opponentCount}
         );

    appInsights.trackPageView
        ("page name", "http://fabrikam.com/pageurl.html",
          // String properties:
         {Game: currentGame.name, Difficulty: currentGame.difficulty},
         // Numeric metrics:
         {Score: currentGame.score, Opponents: currentGame.opponentCount}
         );
          

*C#*

    // Set up some properties and metrics:
    var properties = new Dictionary <string, string> 
       {{"game", currentGame.Name}, {"difficulty", currentGame.Difficulty}};
    var metrics = new Dictionary <string, double>
       {{"Score", currentGame.Score}, {"Opponents", currentGame.OpponentCount}};

    // Send the event:
    telemetry.TrackEvent("WinGame", properties, metrics);


*VB*

    ' Set up some properties:
    Dim properties = New Dictionary (Of String, String)
    properties.Add("game", currentGame.Name)
    properties.Add("difficulty", currentGame.Difficulty)

    Dim metrics = New Dictionary (Of String, Double)
    metrics.Add("Score", currentGame.Score)
    metrics.Add("Opponents", currentGame.OpponentCount)

    ' Send the event:
    telemetry.TrackEvent("WinGame", properties, metrics)


*Java*
    
    Map<String, String> properties = new HashMap<String, String>();
    properties.put("game", currentGame.getName());
    properties.put("difficulty", currentGame.getDifficulty());
    
    Map<String, Double> metrics = new HashMap<String, Double>();
    metrics.put("Score", currentGame.getScore());
    metrics.put("Opponents", currentGame.getOpponentCount());
    
    telemetry.trackEvent("WinGame", properties, metrics);


> [AZURE.NOTE] プロパティで個人を特定できる情報を記録しないように注意します。

**メトリックを使用した場合**、メトリック エクスプ ローラーを開き、カスタム グループからメトリックを選択します。

![メトリック エクスプローラーを開き、グラフを選択し、メトリックを選択する](./media/app-insights-api-custom-events-metrics/03-track-custom.png)

*メトリックが表示されない場合、またはカスタムの見出しがない場合は、選択ブレードを閉じて後でやり直してください。パイプラインを介したメトリックの集計が終了するまで 1 時間かかる場合があります。*

**プロパティとメトリックを使用した場合**、プロパティ別にメトリックを分割します。


![グループ化を設定し、グループ化基準のプロパティを選択する](./media/app-insights-api-custom-events-metrics/04-segment-metric-event.png)



**診断検索では**、イベントのそれぞれの発生箇所のプロパティとメトリックを表示できます。


![インスタンスを選択し、「...」を選択する](./media/app-insights-api-custom-events-metrics/appinsights-23-customevents-4.png)


[検索] ボックスを使用して、特定のプロパティ値を持つイベントを表示します。


![検索用語を入力する](./media/app-insights-api-custom-events-metrics/appinsights-23-customevents-5.png)

[検索式の詳細情報][diagnostic]

#### プロパティとメトリックを設定する別の方法

個別のオブジェクトでイベントのパラメーターを集めたほうが便利であれば、そのようにできます。

    var event = new EventTelemetry();

    event.Name = "WinGame";
    event.Metrics["processingTime"] = stopwatch.Elapsed.TotalMilliseconds;
    event.Properties["game"] = currentGame.Name;
    event.Properties["difficulty"] = currentGame.Difficulty;
    event.Metrics["Score"] = currentGame.Score;
    event.Metrics["Opponents"] = currentGame.Opponents.Length;

    telemetry.TrackEvent(event);

> [AZURE.WARNING] Track*() を複数回呼び出すために、同じテレメトリ項目インスタンス (この例では `event`) を再利用しないでください。再利用すると、正しくない構成でテレメトリが送信される場合があります。



## <a name="timed"></a> タイミング イベント

何らかのアクションを実行するためにかかる時間をグラフに示す必要が生じることがあります。たとえば、ユーザーがゲームで選択肢について考える時間について調べることができます。これは、測定のパラメーターの使用に役立つ例です。


*C#*

    var stopwatch = System.Diagnostics.Stopwatch.StartNew();

    // ... perform the timed action ...

    stopwatch.Stop();

    var metrics = new Dictionary <string, double>
       {{"processingTime", stopwatch.Elapsed.TotalMilliseconds}};

    // Set up some properties:
    var properties = new Dictionary <string, string> 
       {{"signalSource", currentSignalSource.Name}};

    // Send the event:
    telemetry.TrackEvent("SignalProcessed", properties, metrics);



## <a name="defaults"></a>カスタム テレメトリの既定のプロパティ

記述するカスタム イベントにいくつかに既定のプロパティ値を設定する場合、TelemetryClient でそれを設定できます。既定値はそのクライアントから送信されたすべてのテレメトリ アイテムに追加されます。

*C#*

    using Microsoft.ApplicationInsights.DataContracts;

    var gameTelemetry = new TelemetryClient();
    gameTelemetry.Context.Properties["Game"] = currentGame.Name;
    // Now all telemetry will automatically be sent with the context property:
    gameTelemetry.TrackEvent("WinGame");
    
*VB*

    Dim gameTelemetry = New TelemetryClient()
    gameTelemetry.Context.Properties("Game") = currentGame.Name
    ' Now all telemetry will automatically be sent with the context property:
    gameTelemetry.TrackEvent("WinGame")

*Java*

    import com.microsoft.applicationinsights.TelemetryClient;
    import com.microsoft.applicationinsights.TelemetryContext;
    ...


    TelemetryClient gameTelemetry = new TelemetryClient();
    TelemetryContext context = gameTelemetry.getContext();
    context.getProperties().put("Game", currentGame.Name);
    
    gameTelemetry.TrackEvent("WinGame");


    
個別のテレメトリの呼び出しはプロパティ ディクショナリの既定値を上書きできます。

**JavaScript Web クライアント**の場合、[JavaScript テレメトリ初期化子](#js-initializer)を使用します。

標準コレクション モジュールのデータなど、**すべてのテレメトリにプロパティを追加する**には、`ITelemetryInitializer` を[実装](app-insights-api-filtering-sampling.md#add-properties)します。


## テレメトリのサンプリング、フィルター処理、および処理 

SDK からテレメトリを送信する前に、テレメトリを処理するコードを記述することができます。この処理では、HTTP 要求のコレクションや依存関係のコレクションなど、標準的なテレメトリ モジュールから送信されるデータも対象となります。

* `ITelemetryInitializer` の実装によるテレメトリへの[プロパティの追加](app-insights-api-filtering-sampling.md#add-properties) - たとえば、バージョン番号や、他のプロパティから算出された値などを追加します。
* `ITelemetryProcesor` を実装することによって、テレメトリを SDK から送信する前に、[フィルタリング](app-insights-api-filtering-sampling.md#filtering)によって変更または破棄することができます。何を送信して何を破棄するかを操作できますが、メトリックへの影響を考慮する必要があります。項目を破棄する方法によっては、関連する項目間を移動する機能が失われる可能性があります。
* [サンプリング](app-insights-api-filtering-sampling.md#sampling)は、使用しているアプリからポータルに送信するデータ量を減らすためのパッケージ化ソリューションです。サンプリングの実行では、表示されるメトリックに影響を与えることも、例外、要求、ページ ビューなどの関連する項目間を移動して問題を診断する機能に影響を与えることもありません。

[詳細情報](app-insights-api-filtering-sampling.md)


## テレメトリの無効化

テレメトリの収集と送信を**動的に停止および開始**するには

*C#*

```C#

    using  Microsoft.ApplicationInsights.Extensibility;

    TelemetryConfiguration.Active.DisableTelemetry = true;
```

パフォーマンス カウンター、HTTP 要求、依存関係など、**選択されている標準のコレクターを無効にする**には、[ApplicationInsights.config][config] 内の該当する行を削除するか、コメントにします。たとえば、独自の TrackRequest データを送信する場合にこれを行います。

## <a name="debug"></a>開発者モード

デバッグ中、結果をすぐに確認できるように、テレメトリをパイプラインから送信すると便利です。テレメトリで問題を追跡する際に役立つ付加的なメッセージも取得できます。アプリケーションを遅くする可能性があるため、本稼働ではオフにします。


*C#*
    
    TelemetryConfiguration.Active.TelemetryChannel.DeveloperMode = true;

*VB*

    TelemetryConfiguration.Active.TelemetryChannel.DeveloperMode = True


## <a name="ikey"></a> 選択したカスタム テレメトリにインストルメンテーション キーを設定する

*C#*
    
    var telemetry = new TelemetryClient();
    telemetry.InstrumentationKey = "---my key---";
    // ...


## <a name="dynamic-ikey"></a> 動的なインストルメンテーション キー

開発、テスト、および運用環境からのテレメトリの混合を回避するために、[別の Application Insights リソースを作成し、][create]環境に応じてそれぞれのキーを変更することができます。

インストルメンテーション キーは構成ファイルから取得する代わりにコードで設定できます。ASP.NET サービスの global.aspx.cs など、初期化メソッドでキーを設定します。

*C#*

    protected void Application_Start()
    {
      Microsoft.ApplicationInsights.Extensibility.
        TelemetryConfiguration.Active.InstrumentationKey = 
          // - for example -
          WebConfigurationManager.Settings["ikey"];
      ...

*JavaScript*

    appInsights.config.instrumentationKey = myKey; 



Web ページで、スクリプトに一語一語コーディングするのではなく、Web サーバーの状態から設定します。たとえば、ASP.NET アプリケーションで生成された Web ページで

*Razor の JavaScript*

    <script type="text/javascript">
    // Standard Application Insights web page script:
    var appInsights = window.appInsights || function(config){ ...
    // Modify this part:
    }({instrumentationKey:  
      // Generate from server property:
      @Microsoft.ApplicationInsights.Extensibility.
         TelemetryConfiguration.Active.InstrumentationKey"
    }) // ...


## TelemetryContext

TelemetryClient には、すべてのテレメトリ データとともに送信される値の数が含まれるコンテキスト プロパティがあります。通常、標準のテレメトリ モジュールによって設定されますが、自分で設定することもできます。For example:

    telemetry.Context.Operation.Name = "MyOperationName";

いずれかの値を自分で設定した場合は、その値と標準の値が混同されないように、[ApplicationInsights.config][config] から関連する行を削除することを検討します。

* **Component** は、アプリケーションとそのバージョンを識別します。
* アプリケーションが実行されているデバイスに関する**Device** Data (Web アプリケーションでは、テレメトリの送信元となるサーバーまたはクライアントのデバイス)
* **InstrumentationKey** は、テレメトリが表示される Azure で Application Insights リソースを識別します。通常、ApplicationInsights.config から取得されます。
* **Location**は、デバイスの地理的な場所を識別します。
* Web アプリケーションで **Operation** は、現在の HTTP 要求を示します。他の種類のアプリケーションでは、イベントをグループ化するために、これを設定できます。
 * **Id**: [診断検索] でイベントを調べるときに「関連項目」を見つけることができるように、さまざまなイベントを関連付けるために生成される値
 * **名前**: 識別子。通常は HTTP 要求の URL。
 * **SyntheticSource**: null 値または空ではない場合、この文字列は、要求元がロボットまたは Web テストとして識別されたことを示します。既定で、これはメトリックス エクスプローラーでの計算から除外されます。
* **Properties**: すべてのテレメトリ データとともに送信されるプロパティ。個々 の Track* 呼び出しでオーバーライドできます。
* **Session** は、ユーザーのセッションを識別します。Id は、生成される値に設定されます。これは、ユーザーがしばらくの間アクティブ化されていない場合に、変更されます。
* **ユーザー** ユーザー情報。

## 制限


[AZURE.INCLUDE [application-insights-limits](../../includes/application-insights-limits.md)]

*データ速度の上限に達するのを回避する方法*

* [サンプリング](app-insights-sampling.md)を使用します。

*データが保持される期間*

* [データの保持とプライバシー][data]に関するページを参照してください。


## リファレンス ドキュメント

* [ASP.NET リファレンス](https://msdn.microsoft.com/library/dn817570.aspx)
* [Java リファレンス](http://dl.windowsazure.com/applicationinsights/javadoc/)
* [JavaScript リファレンス](https://github.com/Microsoft/ApplicationInsights-JS/blob/master/API-reference.md)
* [Android SDK](https://github.com/Microsoft/ApplicationInsights-Android)
* [iOS SDK](https://github.com/Microsoft/ApplicationInsights-iOS)


## SDK コード

* [ASP.NET コア SDK](https://github.com/Microsoft/ApplicationInsights-dotnet)
* [ASP.NET 5](https://github.com/Microsoft/ApplicationInsights-aspnet5)
* [Windows Server パッケージ](https://github.com/Microsoft/applicationInsights-dotnet-server)
* [Java SDK](https://github.com/Microsoft/ApplicationInsights-Java)
* [JavaScript SDK](https://github.com/Microsoft/ApplicationInsights-JS)
* [すべてのプラットフォーム](https://github.com/Microsoft?utf8=%E2%9C%93&query=applicationInsights)

## 疑問がある場合

* *Track\_() の呼び出しでは、どのような例外がスローされることがありますか。*
    
    なし。try catch 句で例外をラップする必要はありません。SDK で問題が発生すると、デバッグ コンソール出力に表示されるメッセージが記録されます。メッセージがスルーされる場合は、診断検索にも記録されます。



* *ポータルからデータを取得する REST API はありますか。*

    はい、近日対応予定です。それまでの間は、[連続エクスポート](app-insights-export-telemetry.md)を使用してください。

## <a name="next"></a>次のステップ


[イベントおよびログを検索する][diagnostic]

[サンプルとチュートリアル](app-insights-code-samples.md)

[トラブルシューティング][qna]


<!--Link references-->

[client]: app-insights-javascript.md
[config]: app-insights-configuration-with-applicationinsights-config.md
[create]: app-insights-create-new-resource.md
[data]: app-insights-data-retention-privacy.md
[diagnostic]: app-insights-diagnostic-search.md
[exceptions]: app-insights-asp-net-exceptions.md
[greenbrown]: app-insights-asp-net.md
[java]: app-insights-java-get-started.md
[metrics]: app-insights-metrics-explorer.md
[qna]: app-insights-troubleshoot-faq.md
[trace]: app-insights-search-diagnostic-logs.md

 

<!---HONumber=AcomDC_0914_2016-->