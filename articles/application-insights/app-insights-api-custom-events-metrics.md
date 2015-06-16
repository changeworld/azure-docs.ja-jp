<properties 
	pageTitle="カスタムのイベントとメトリックのための Application Insights API" 
	description="デバイスまたはデスクトップ アプリケーション、Web ページまたはサービスに数行のコードを追加し、使用状況を追跡し、問題を診断します。" 
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
	ms.date="05/08/2015" 
	ms.author="awills"/>

# カスタムのイベントとメトリックのための Application Insights API 

*Application Insights はプレビュー段階です。*

アプリケーションに数行のコードを挿入して、ユーザーの行動を調べたり、問題の診断に役立つ情報を取得したりすることができます。デバイスとデスクトップ アプリケーション、Web クライアント、Web サーバーからテレメトリを送信できます。

Application Insights でデータを集めるとき、この API を使用すれば、ページ ビューや例外レポートなど、標準のテレメトリを送信できるだけでなく、独自のカスタム テレメトリも送信できます。

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

これらのテレメトリの呼び出しのほとんどに[プロパティとメトリックをアタッチ](#properties)できます。


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

TelemetryClient のインスタンスの作成 (Web ページの JavaScript を除く):

*C#:*

    private TelemetryClient telemetry = new TelemetryClient();

*VB:*

    Private Dim telemetry As New TelemetryClient

*Java*

    private TelemetryClient telemetry = new TelemetryClient();

Web アプリケーションの要求ごとに、あるいは他のアプリケーションのセッションごとに、`TelemetryClient` のインスタンスを 1 つ使用することをお勧めします。`TelemetryClient.Context.User.Id` などのプロパティを設定し、ユーザーとセッションを追跡できます。この情報はインスタンスから送信されたすべてのイベントに関連付けられます。

TelemetryClient はスレッド セーフです。



## イベントを追跡する

イベントは [メトリック エクスプローラー][metrics]に総数として表示できます。[診断検索][diagnostic]の個別出現回数を表示することもできます。

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


[概要] ブレードでカスタム イベントのタイルをクリックします。

![portal.azure.com でアプリケーション リソースを参照する](./media/app-insights-api-custom-events-metrics/01-custom.png)

クリックし、概要グラフと詳細一覧を表示します。

グラフを選択し、イベント名で分割し、最も重要なイベントの相対的寄与率を表示します。

![グラフを選択し、グループ化を設定する](./media/app-insights-api-custom-events-metrics/02-segment.png)

グラフの下にある一覧から、イベント名を選択します。クリックし、イベントの個別インスタンスを表示します。

![イベントをドリルスルーする](./media/app-insights-api-custom-events-metrics/03-instances.png)

任意のインスタンスをクリックすると、詳細が表示されます。

## <a name="properties"></a>プロパティを使用してデータをフィルター処理、検索、分割する

プロパティと測定値をイベント (およびメトリック、ページ ビュー、その他のテレメトリ データ) に結び付けることができます。

**プロパティ**は、利用状況レポートでテレメトリをフィルター処理するのに使用できる文字列値です。たとえば、アプリケーションで複数のゲームを提供する場合、各イベントにゲームの名前を結び付けると、人気のあるゲームを確認できます。

文字列の長さには約 1 k の制限があります。(大きなデータの塊を送信する場合、[TrackTrace](#track-trace) のメッセージ パラメーターを使用します。)

**メトリック**はグラフィカルに表示できる数値です。たとえば、ゲーマーが達成するスコアに漸増があるかどうかを確認できます。グラフはイベントともに送信されるプロパティ別に分割できます。そのため、ゲームごとに個別グラフまたは積み重ねグラフを表示できます。

メトリック値を正しく表示するには、0 以上にする必要があります。


使用できる[プロパティ、プロパティ値、およびメトリックの数には制限](#limits)があります。


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


#### <a name="timed"></a> タイミング イベント

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


結果を表示するには、メトリック エクスプローラーを開き、新しいグラフを追加します。メトリックを表示するように設定します。

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

#### 時間指定ページ ビュー

trackPageView の代わりにこの組み合わせのメソッド呼び出しを使用することで、ユーザーがページに残った時間を分析できます。

    // At the start of a page view:
    appInsights.startTrackPage(myPage.name);

    // At the completion of a page view:
    appInsights.stopTrackPage(myPage.name, "http://fabrikam.com/page", properties, measurements);

開始の呼び出しと停止の呼び出しで、最初のパラメーターに同じ文字列を使用します。

[メトリック エクスプ ローラー][metrics]の Page Duration メトリックをご覧ください。


## 要求を追跡する

HTTP 要求を記録するために、サーバー SDK によって使用されます。

Web サービス モジュールが実行されていない状況で要求をシミュレーションする場合に、これを自分で呼び出すこともできます。

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

例外を Application Insights に送信して[数え][metrics]、問題の発生頻度を示し、[個々の問題を調べます][diagnostic]。

*C#*

    try
    {
        ...
    }
    catch (Exception ex)
    {
       telemetry.TrackException(ex);
    }

Windows モバイル アプリでは、SDK が未処理の例外をキャッチするので、記録する必要がありません。ASP.NET では、[自動的に例外をキャッチするコードを記述][exceptions]できます。 


## トレースを追跡する 

Application Insights に「階層リンクの軌跡」を送信して問題を診断するときに役立ちます。診断データの塊を送信し、[診断検索][diagnostic]で検査できます。

 

[ログ アダプター][trace]はこの API を使用し、ポータルにサード パーティのログを送信します。


*C#*

    telemetry.TrackTrace(message, SeverityLevel.Warning, properties);

`message`のサイズ制限はプロパティの制限よりも高くなっています。メッセージ コンテンツで検索できますが、(プロパティ値とは異なり) フィルター処理はできません。


## <a name="default-properties"></a>すべてのテレメトリに既定のプロパティを設定する

すべての新しい TelemetryClients が自動的にコンテキストを使用できるように、汎用の初期化子を設定できます。これには、Web サーバー要求追跡など、プラットフォーム固有のテレメトリ モジュールにより送信される標準の利用統計情報が含まれます。

一般的な使用方法では、アプリケーションのさまざまなバージョンやコンポーネントからのテレメトリを識別します。ポータルでは、このプロパティによって結果にフィルターを適用し、グループ化することができます。

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



## <a name="dynamic-ikey"></a> 動的なインストルメンテーション キー

開発、テスト、および実稼働環境からのテレメトリの混合を回避するために、[別の Application Insights リソースを作成し、][create]環境に応じてそれぞれのキーを変更することができます。

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


## <a name="defaults"></a>選択したカスタム テレメトリに既定値を設定する

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



## <a name="ikey"></a> 選択したカスタム テレメトリにインストルメンテーション キーを設定する

*C#*
    
    var telemetry = new TelemetryClient();
    telemetry.Context.InstrumentationKey = "---my key---";
    // ...

## データのフラッシュ

通常、SDK は、ユーザーへの影響を最小限に抑えるために選択した時間帯に、データを送信します。ただし、場合によっては、バッファーをフラッシュする必要が生じることがあります (たとえば、終了するアプリケーションで SDK を使用している場合)。

*C#*

    telemetry.Flush();

この機能は同期的であることに注意してください。



## 標準のテレメトリを無効にする

`ApplicationInsights.config` を編集し、[標準のテレメトリから選択した部分を無効にできます][config]。たとえば、独自の TrackRequest データを送信する場合にこれを行います。

[詳細情報][config]。


## <a name="debug"></a>開発者モード

デバッグ中、結果をすぐに確認できるように、テレメトリをパイプラインから送信すると便利です。テレメトリで問題を追跡する際に役立つ付加的なメッセージも取得できます。アプリケーションを遅くする可能性があるため、本稼働ではオフにします。


*C#*
    
    TelemetryConfiguration.Active.TelemetryChannel.DeveloperMode = true;

*VB*

    TelemetryConfiguration.Active.TelemetryChannel.DeveloperMode = True

## TelemetryContext

TelemetryClient には、すべてのテレメトリ データとともに送信される値の数が含まれるコンテキスト プロパティがあります。通常、標準のテレメトリ モジュールによって設定されますが、自分で設定することもできます。

いずれかの値を自分で設定した場合は、その値と標準の値が混同されないように、[ApplicationInsights.config][config] から関連する行を削除することを検討します。

* **Component** は、アプリケーションとそのバージョンを識別します。
* アプリケーションが実行されているデバイスに関する**Device** Data (Web アプリケーションでは、テレメトリの送信元となるサーバーまたはクライアントのデバイス)
* **InstrumentationKey** は、テレメトリが表示される Azure で Application Insights リソースを識別します。通常、ApplicationInsights.config から取得されます。
* **Location**は、デバイスの地理的な場所を識別します。
* Web アプリケーションで **Operation** は、現在の HTTP 要求を示します。他の種類のアプリケーションでは、イベントをグループ化するために、これを設定できます。
 * **Id**: [診断検索] でイベントを調べるときに「関連項目」を見つけることができるように、さまざまなイベントを関連付けるために生成される値
 * **Name**: HTTP 要求の URL
 * **SyntheticSource**: null 値または空ではない場合、この文字列は、要求元がロボットまたは Web テストとして識別されたことを示します。既定で、これはメトリックス エクスプ ローラーでの計算から除外されます。
* **Properties**: すべてのテレメトリ データとともに送信されるプロパティ。個々 の Track*  呼び出しでオーバーライドできます。
* **Session** は、ユーザーのセッションを識別します。Id は、生成される値に設定されます。これは、ユーザーがしばらくの間アクティブ化されていない場合に、変更されます。
* **User** によって、ユーザーを数えることができます。Web アプリケーションで、Cookie がある場合、ユーザー Id は Cookie から取得されます。Cookie がない場合は、新しい Id が生成されます。ユーザーがアプリケーションにログインする必要がある場合は、認証済みの ID から Id を設定できます。これにより、ユーザーが別のコンピューターからサインインしている場合でも、正確でより信頼できる数が提供されます。 

## 制限

アプリケーションごとのメトリックとイベントの数には制限があります。

1. インストルメンテーション キー (つまり、アプリケーション) ごとに 1 秒あたり最大 500 のテレメトリ データ ポイント。これには、SDK モジュールによって送信される標準テレメトリと、コードによって送信されるカスタム イベント、メトリック、およびその他のテレメトリの両方が含まれます。
1.	アプリケーションに対して最大 200 の一意のメトリックの名前と 200 の一意のプロパティの名前。メトリックには、TrackMetric を通じて送信されるデータと、イベントなどの他のデータ型の測定値が含まれます。メトリックとプロパティの名前は、データ型にスコープが制限されず、インストルメンテーション キーごとにグローバルです。
2.	各プロパティに対する一意の値は 100 未満であり、プロパティは、フィルタリングとグループ化のみに使用できます。一意の値が 100 を超えた後も、プロパティは検索とフィルタリングに使用できますが、フィルター処理には使用できなくなります。
3.	要求名やページの URL などの標準プロパティは、1 週間あたりの 1000 の一意な値に制限されます。1000 の一意の値を超えると、追加の値は「その他の値」としてマークされます。元の値は、全文テキスト検索とフィルタリングに引き続き使用できます。

* *質問: データは、どのくらいの期間保持されますか。*

    [データの保持とプライバシーに関するページ][data]を参照してください。

## リファレンス ドキュメント

* [ASP.NET リファレンス](https://msdn.microsoft.com/library/dn817570.aspx)
* [Java リファレンス](http://dl.windowsazure.com/applicationinsights/javadoc/)

## 疑問がある場合

* *Track * の呼び出しでは、どのような例外がスローされることがありますか。*
    
    ありません。Catch 句で例外をキャッチする必要はありません。



* *REST API はありますか。*

    はい。ただし、まだ公開されていません。

## <a name="next"></a>次のステップ


[イベントおよびログを検索する][diagnostic]

[トラブルシューティング][qna]


<!--Link references-->

[client]: app-insights-javascript.md
[config]: app-insights-configuration-with-applicationinsights-config.md
[create]: app-insights-create-new-resource.md
[data]: app-insights-data-retention-privacy.md
[diagnostic]: app-insights-diagnostic-search.md
[exceptions]: app-insights-asp-net-exceptions.md
[greenbrown]: app-insights-start-monitoring-app-health-usage.md
[java]: app-insights-java-get-started.md
[metrics]: app-insights-metrics-explorer.md
[qna]: app-insights-troubleshoot-faq.md
[trace]: app-insights-search-diagnostic-logs.md
[windows]: app-insights-windows-get-started.md

<!---HONumber=58--> 