<properties 
	pageTitle="カスタムのイベントとメトリックのための Application Insights API" 
	description="デバイスまたはデスクトップ アプリ、Web ページまたはサービスに数行のコードを追加し、使用状況を追跡し、問題を診断します。" 
	services="application-insights"
    documentationCenter="" 
	authors="alancameronwills" 
	manager="ronmart"/>
 
<tags 
	ms.service="application-insights" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="ibiza" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="04/28/2015" 
	ms.author="awills"/>

# カスタムのイベントとメトリックのための Application Insights API 

*Application Insights はプレビュー段階です。*

アプリケーションに数行のコードを挿入して、ユーザーの行動を調べたり、問題の診断に役立つ情報を取得したりすることができます。デバイスとデスクトップ アプリ、Web クライアント、Web サーバーから利用統計情報を送信できます。

Application Insights でデータを集めるとき、この API を使用すれば、ページ ビューや例外レポートなど、標準の利用統計情報を送信できるだけでなく、独自のカスタム利用統計情報も送信できます。

## API summary

この API は、いくつかの小さな違いは別として、すべてのプラットフォームで一様になっています。

メソッド | 使用対象
---|---
[`TrackPageView`](#page-views) | ページ、画面、ブレード、フォーム
[`TrackEvent`](#track-event) | ユーザー アクションとその他のイベント。ユーザーの行動を追跡するために、またはパフォーマンスを監視するために使用されます。
[`TrackMetric`](#track-metric) | キューの長さなど、特定のイベントに関連しないパフォーマンスの測定
[`TrackException`](#track-exception)|例外を記録し、診断に利用します。他のイベントとの関連で例外の発生箇所を追跡し、スタック トレースを調べます。
[`TrackRequest`](#track-request)| サーバー要求の頻度と期間を記録し、パフォーマンス分析に利用します。
[`TrackTrace`](#track-trace)|メッセージを記録し、診断に利用します。サード パーティのログをキャプチャすることもできます。

これらの利用統計情報呼び出しのほとんどに[プロパティとメトリックをアタッチ](#properties)できます。


## <a name="prep"></a>開始する前に

次のことをまだ実行していない場合は、実行します。

* Application Insights SDK をプロジェクトに追加します。
 * [ASP.NET プロジェクト][greenbrown]
 * [Windows プロジェクト][windows]
 * [Java プロジェクト][java] 
 * [各 Web ページの JavaScript][client]   

* デバイスまたは Web サーバー コードに次を追加します。

    *C#:* `using Microsoft.ApplicationInsights;`

    *VB:* `Imports Microsoft.ApplicationInsights`

    *Java:* `import com.microsoft.applicationinsights.TelemetryClient;`

## TelemetryClient を作成する

TelemetryClient のインスタンスの作成 (web ページの JavaScript を除く):

*C#:*

    private TelemetryClient telemetry = new TelemetryClient();

*VB:*

    Private Dim telemetry As New TelemetryClient

*Java*

    private TelemetryClient telemetry = new TelemetryClient();

Web アプリの要求ごとに、あるいは他のアプリのセッションごとに、`TelemetryClient` のインスタンスを 1 つ使用することをお勧めします。`TelemetryClient.Context.User.Id` などのプロパティを設定し、ユーザーとセッションを追跡できます。この情報はインスタンスから送信されたすべてのイベントに関連付けられます。

TelemetryClient はスレッド セーフです。



## イベントを追跡する

イベントは [メトリック エクスプ ローラー][metrics]に総数として表示できます。[診断検索][diagnostic]の個別出現回数を表示することもできます。

イベントをコードに挿入し、特定の機能を使用する頻度、特定の目標を達成する頻度、特定の選択を行う頻度を数えます。

たとえば、ゲーム アプリで、ユーザーが勝利したときにイベントを送信します。

*JavaScript*

    appInsights.trackEvent("WinGame");

*C#*
    
    telemetry.TrackEvent("WinGame");

*VB*


    telemetry.TrackEvent("WinGame")

*Java*

    telemetry.trackEvent("WinGame");


概要ブレードでカスタム イベントのタイルをクリックします。

![portal.azure.com でアプリケーション リソースを参照する](./media/app-insights-api-custom-events-metrics/01-custom.png)

クリックし、概要グラフと完全一覧を表示します。

グラフを選択し、イベント名で分割し、最も重要なイベントの相対的寄与率を表示します。

![グラフを選択し、グループ化を設定する](./media/app-insights-api-custom-events-metrics/02-segment.png)

グラフの下にある一覧から、イベント名を選択します。クリックし、イベントの個別インスタンスを表示します。

![イベントをドリルスルーする](./media/app-insights-api-custom-events-metrics/03-instances.png)

出現をクリックすると、詳細が表示されます。

## <a name="properties"></a>プロパティを使用してデータをフィルター処理、検索、分割する

プロパティと測定値をイベント (およびメトリック、ページ ビュー、その他の利用統計情報データ) に結び付けることができます。

**プロパティ**は、利用状況レポートで利用統計情報をフィルター処理するのに使用できる文字列値です。たとえば、アプリケーションで複数のゲームを提供する場合、各イベントにゲームの名前を結び付けると、人気のあるゲームを確認できます。

文字列の長さには約 1 k の制限があります。(大きなデータの塊を送信する場合、[TrackTrace](#track-trace) のメッセージ パラメーターを使用します。)

**メトリック**はグラフィカルに表示できる数値です。たとえば、ゲーマーが達成するスコアに漸増があるかどうかを確認できます。グラフはイベントともに送信されるプロパティ別に分割できます。そのため、ゲームごとに個別グラフまたは積み重ねグラフを表示できます。

メトリック値を正しく表示するには、0 以上にする必要があります。

*JavaScript*

    appInsights.trackEvent // or trackPageView, trackMetric, ...
      ("WinGame",
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
    
    telemetry.trackEvent("WinGame", properties, metrics2/7/2015 12:05:25 AM );


> [AZURE.NOTE]プロパティで個人を特定できる情報を記録しないように注意します。

**メトリックを使用した場合**、メトリック エクスプ ローラーを開き、カスタム グループからメトリックを選択します。

![メトリック エクスプローラーを開き、グラフを選択し、メトリックを選択する](./media/app-insights-api-custom-events-metrics/03-track-custom.png)

*メトリックが表示されない場合、選択ブレードを閉じ、しばらく待ってから [更新] をクリックします。*

**プロパティとメトリックを使用した場合**、プロパティ別にメトリックを分割します。


![グループ化を設定し、グループ化基準のプロパティを選択する](./media/app-insights-api-custom-events-metrics/04-segment-metric-event.png)



**診断検索では**、イベントのそれぞれの発生箇所のプロパティとメトリックを表示できます。


![インスタンスを選択し、「...」を選択する](./media/appinsights/appinsights-23-customevents-4.png)


[検索] ボックスを使用して、特定のプロパティ値を持つイベントを表示します。


![検索用語を入力する](./media/appinsights/appinsights-23-customevents-5.png)

[検索文字列の詳細][diagnostic]

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


## <a name="timed"></a> 時間指定イベント

何らかのアクションを実行するためにかかる時間をグラフに表すことがあります。たとえば、ユーザーがゲームで選択肢について考える時間について調べることがあります。

イベントにタイミング データを結び付けることができます。Web クライアントで、trackEvent を呼び出す代わりに、これらの呼び出しを使用します。

*Web クライアントの JavaScript*

    // At the start of the game:
    appInsights.startTrackEvent(game.id);

    // At the end of the game:
    appInsights.stopTrackEvent(game.id, {GameName: game.name}, {Score: game.score});

開始の呼び出しと停止の呼び出しで、最初のパラメーターに同じ文字列を使用します。

この機能は他の SDK には組み込まれていません。ただし、次のように独自のコードを記述できます。

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



## メトリックを追跡する

TrackMetric を使用し、特定のイベントに関連付けられていないメトリックを送信します。たとえば、一定の間隔でキューの長さを監視できます。

メトリックはメトリック エクスプローラーに統計グラフとして表示されますが、診断検索で個別の発生を検索することはできません。

メトリック値を正しく表示するには、0 以上にする必要があります。


*JavaScript*

    appInsights.trackMetric("Queue", queue.Length);

*C#*

    telemetry.TrackMetric("Queue", queue.Length);

*VB*

    telemetry.TrackMetric("Queue", queue.Length)

*Java*

    telemetry.trackMetric("Queue", queue.Length);

実際には、バック グラウンド スレッドでこれを行うことがあります。

*C#*

    private void Run() {
     var appInsights = new TelemetryClient();
     while (true) {
      Thread.Sleep(60000);
      appInsights.TrackMetric("Queue", queue.Length);
     }
    }


結果を表示するには、メトリック エクスプローラーを開き、新しいグラフを追加します。メトリックを表示するように設定します。

![新しいグラフを追加するか、グラフを選択し、[カスタム] でメトリックを選択する](./media/app-insights-api-custom-events-metrics/03-track-custom.png)


## ページ ビュー

デバイスまたは Web ページ アプリで、各画面または各ページが読み込まれたときに、既定でページ ビュー利用統計情報が送信されます。ただし、これを変更し、ページ ビューを追跡する回数を増やしたり、変えたりできます。たとえば、タブまたはブレードを表示するアプリで、ユーザーが新しいブレードを開いたときに「ページ」を追跡します。

![概要ブレードの使用状況レンズ](./media/appinsights/appinsights-47usage-2.png)

ユーザーとセッションのデータはページ ビューとともにプロパティとして送信されます。そのため、ページ ビューの利用統計情報があれば、ユーザーとセッションのグラフが活発になります。

#### カスタム ページ ビュー

*JavaScript*

    appInsights.trackPageView("tab1");

*C#*

    telemetry.TrackPageView("GameReviewPage");

*VB*

    telemetry.TrackPageView("GameReviewPage")


別々の HTML ページ内で複数のタブを使用している場合、URL を指定することもできます。

    appInsights.trackPageView("tab1", "http://fabrikam.com/page1.htm");

#### 時間指定ページ ビュー

trackPageView の代わりにこの組み合わせのメソッド呼び出しを使用することで、ユーザーがページに残った時間を分析できます。

    // At the start of a page view:
    appInsights.startTrackPage(myPage.name);

    // At the completion of a page view:
    appInsights.stopTrackPage(myPage.name, "http://fabrikam.com/page", properties, measurements);

開始の呼び出しと停止の呼び出しで、最初のパラメーターに同じ文字列を使用します。

[メトリック エクスプ ローラー][metrics]の Page Duration メトリックをご覧ください。


## 要求を追跡する

サーバー SDK によって使用され、HTTP 要求を記録します。

Web サービス モジュールの実行が望まれない状況で要求をシミュレーションする場合にこれを自分で呼び出すこともできます。

*C#*

    // At start of processing this request:

    // Operation Id is attached to all telemetry and helps you identify
    // telemetry associated with one request:
    telemetry.Context.Operation.Id = Guid.NewGuid().ToString();
    
    var stopwatch = System.Diagnostics.Stopwatch.StartNew();

    // ... process the request ...

    stopwatch.Stop();
    telemetryClient.TrackRequest(requestName, DateTime.Now,
       stopwatch.Elapsed, 
       "200", true);  // Response code, success

## 例外を追跡する

例外を Application Insights に送信して[数え][metrics]、問題の発生頻度を示し、[個々の発生を調べます][diagnostic]。

*C#*

    try
    {
        ...
    }
    catch (Exception ex)
    {
       telemetry.TrackException(ex);
    }

Windows モバイル アプリでは、SDK が未処理の例外をキャッチするので、記録する必要がありません。



## トレースを追跡する 

Application Insights に「階層リンクの軌跡」を送信して問題を診断するときに役立ちます。診断データの塊を送信し、[診断検索][diagnostic]で検査できます。

 

[ログ アダプター][trace]はこの API を使用し、ポータルにサード パーティのログを送信します。


*C#*

    telemetry.TrackTrace(message, SeverityLevel.Warning, properties);

`message`のサイズ制限はプロパティの制限よりも高くなっています。メッセージ コンテンツで検索できますが、(プロパティ値とは異なり) フィルター処理はできません。


## すべての利用統計情報に既定のプロパティを設定する

すべての新しい TelemetryClients が自動的にコンテキストを使用できるように、汎用の初期化子を設定できます。

これには、Web サーバー要求追跡など、プラットフォーム固有の利用統計情報モジュールにより送信される標準の利用統計情報が含まれます。

*C#*

    // Telemetry initializer class
    public class MyTelemetryInitializer : IContextInitializer
    {
        public void Initialize (TelemetryContext context)
        {
            context.Properties["AppVersion"] = "v2.1";
        }
    }

    // In the app initializer such as Global.asax.cs:

    protected void Application_Start()
    {
        // ...
        TelemetryConfiguration.Active.ContextInitializers
        .Add(new MyTelemetryInitializer());
    }

*Java*

    import com.microsoft.applicationinsights.extensibility.ContextInitializer;
    import com.microsoft.applicationinsights.telemetry.TelemetryContext;

    public class MyTelemetryInitializer implements ContextInitializer {
      @Override
      public void initialize(TelemetryContext context) {
        context.getProperties().put("AppVersion", "2.1");
      }
    }

    // load the context initializer
    TelemetryConfiguration.getActive().getContextInitializers().add(new MyTelemetryInitializer());



## すべての利用統計情報について、コードでインストルメンテーション キーを設定する

インストルメンテーション キーは構成ファイルから取得する代わりにコードで設定できます。たとえば、ライブ アプリケーションの利用統計情報とは異なる Application Insights リソースにテスト インストールの利用統計情報を送信できます。

ASP.NET サービスの global.aspx.cs など、初期化メソッドでキーを設定します。

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



Web ページで、スクリプトに一語一語コーディングするのではなく、Web サーバーの状態から設定します。たとえば、ASP.NET アプリで生成された Web ページで

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


## <a name="defaults"></a>選択したカスタム利用統計情報に既定値を設定する

記述するカスタム イベントにいくつかに既定のプロパティ値を設定する場合、TelemetryClient でそれを設定できます。既定値はそのクライアントから送信されたすべての利用統計情報アイテムに追加されます。

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
    
個別の利用統計情報呼び出しはプロパティ ディクショナリの既定値を上書きできます。



## <a name="ikey"></a> 選択したカスタム利用統計情報にインストルメンテーション キーを設定する

*C#*
    
    var telemetry = new TelemetryClient();
    telemetry.Context.InstrumentationKey = "---my key---";
    // ...


## 標準の利用統計情報を無効にする

`ApplicationInsights.config` を編集し、[標準の利用統計情報から選択した部分を無効にできます][config]。たとえば、独自の TrackRequest データを送信する場合にこれを行います。

[詳細情報][config]。


## <a name="debug"></a>開発者モード

デバッグ中、結果をすぐに確認できるように、利用統計情報をパイプラインから送信すれば便利です。利用統計情報で問題を追跡する際に役立つ付加的なメッセージも取得できます。アプリを遅くする可能性があるため、本稼働ではオフにします。


*C#*
    
    TelemetryConfiguration.Active.TelemetryChannel.DeveloperMode = true;

*VB*

    TelemetryConfiguration.Active.TelemetryChannel.DeveloperMode = True



## リファレンス ドキュメント

* [ASP.NET リファレンス](https://msdn.microsoft.com/library/dn817570.aspx)
* [Java リファレンス](http://dl.windowsazure.com/applicationinsights/javadoc/)


* *Q: REST API はありますか?*

    はい。ただし、まだ公開されていません。

## <a name="next"></a>次のステップ


[イベントとログを検索する][diagnostic]

[トラブルシューティング][qna]


<!--Link references-->

[client]: app-insights-javascript.md
[config]: app-insights-configuration-with-applicationinsights-config.md
[diagnostic]: app-insights-diagnostic-search.md
[greenbrown]: app-insights-start-monitoring-app-health-usage.md
[java]: app-insights-java-get-started.md
[metrics]: app-insights-metrics-explorer.md
[qna]: app-insights-troubleshoot-faq.md
[trace]: app-insights-search-diagnostic-logs.md
[windows]: app-insights-windows-get-started.md

<!---HONumber=58-->