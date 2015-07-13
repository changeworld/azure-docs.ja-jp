<properties 
	pageTitle="Web Apps の障害と例外の検出と診断" 
	description="Web Apps の障害と例外の検出と診断" 
	services="application-insights" 
    documentationCenter=""
	authors="alancameronwills" 
	manager="keboyd"/>

<tags 
	ms.service="application-insights" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="ibiza" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="03/31/2015" 
	ms.author="awills"/>
 
# Application Insights を利用し、Web Apps の障害と例外を診断する

[Visual Studio Application Insights][start] は、アプリケーションの障害を検出、診断するための強力なツールです。ここでは特に、Web アプリケーションについて説明します (ただし、Application Insights は [他のさまざまなプラットフォーム][platforms]にも適用できます)。

## Application Insights でアプリをセットアップする

SDK をアプリケーション プロジェクトに追加します。アプリがリリースされて実行されると、SDK はアプリのパフォーマンスに関するテレメトリを Application Insights ポータルに送信します。

* [Application Insights を ASP.NET アプリに追加する][greenbrown]
* [Application Insights を Java アプリに追加する][java]

Web ページに大量のスクリプトがある場合、*[Application Insights を Web ページに追加できます][track]


## 診断検索



診断検索を開くと、SDK から自動送信されたテレメトリが表示されます。

![](./media/app-insights-web-failures-exceptions/appinsights-45diagnostic.png)

![](./media/app-insights-web-failures-exceptions/appinsights-31search.png)

詳細は、アプリケーションの種類によって異なります。個々のイベントをクリックして詳細情報を表示できます。

##<a name="events"></a>カスタム イベント

カスタム イベントは、[診断検索][diagnostic]と[メトリック エクスプローラー][metrics]の両方に表示されます。カスタム イベントは、デバイス、Web ページ、サーバー アプリケーションから送信できます。これらは、診断の目的にも、[使用状況のパターンを理解する][track]ためにも利用できます。

カスタム イベントには名前があり、プロパティを持つこともできるため、数値の測定値と共にフィルター処理をすることができます。

クライアント側の JavaScript

    appInsights.trackEvent("WinGame",
         // String properties:
         {Game: currentGame.name, Difficulty: currentGame.difficulty},
         // Numeric measurements:
         {Score: currentGame.score, Opponents: currentGame.opponentCount}
         );

サーバー側の C#

    // Set up some properties:
    var properties = new Dictionary <string, string> 
       {{"game", currentGame.Name}, {"difficulty", currentGame.Difficulty}};
    var measurements = new Dictionary <string, double>
       {{"Score", currentGame.Score}, {"Opponents", currentGame.OpponentCount}};

    // Send the event:
    telemetry.TrackEvent("WinGame", properties, measurements);


サーバー側の VB

    ' Set up some properties:
    Dim properties = New Dictionary (Of String, String)
    properties.Add("game", currentGame.Name)
    properties.Add("difficulty", currentGame.Difficulty)

    Dim measurements = New Dictionary (Of String, Double)
    measurements.Add("Score", currentGame.Score)
    measurements.Add("Opponents", currentGame.OpponentCount)

    ' Send the event:
    telemetry.TrackEvent("WinGame", properties, measurements)

### アプリを実行して結果を確認します。

診断検索を開きます。

カスタム イベントを選択し、特定のイベント名を選択します。

![](./media/app-insights-web-failures-exceptions/appinsights-332filterCustom.png)


プロパティ値を検索用語として入力し、さらにデータをフィルター処理します。

![](./media/app-insights-web-failures-exceptions/appinsights-23-customevents-5.png)

個々のイベントにドリルダウンして詳細なプロパティを確認します。

![](./media/app-insights-web-failures-exceptions/appinsights-23-customevents-4.png)



##<a name="trace"></a> トレース テレメトリ

トレース テレメトリは、特に診断ログを作成するためだけに挿入するコードです。

たとえば、次のような呼び出しを挿入できます。

    var telemetry = new Microsoft.ApplicationInsights.TelemetryClient();
    telemetry.TrackTrace("Slow response - database01");


####  ログ記録フレームワークにアダプターをインストールする

log4Net、NLog、System.Diagnostics.Trace といった、ログ記録フレームワークで生成されたログの検索もできます。

1. log4Net や NLog を使用する場合は、プロジェクト内にインストールします。 
2. ソリューション エクスプローラーでプロジェクトを右クリックし、[**NuGet パッケージの管理**] を選択します。
3. [オンライン]、[すべて]、**[プレリリースを含める]** の順に選択し、"Microsoft.ApplicationInsights" を検索します。

    ![適切なパッケージのプレリリース バージョンを入手する](./media/app-insights-web-failures-exceptions/appinsights-36nuget.png)

4. 次のいずれかの適切なパッケージを選択します。
  + Microsoft.ApplicationInsights.TraceListener (System.Diagnostics.Trace 呼び出しをキャプチャするため)
  + Microsoft.ApplicationInsights.NLogTarget
  + Microsoft.ApplicationInsights.Log4NetAppender

NuGet パッケージは、必要なアセンブリをインストールし、web.config や app.config も変更します。

#### <a name="pepper"></a>診断ログの呼び出しを挿入する

System.Diagnostics.Trace を使用する場合、通常の呼び出しは次のようになります。

    System.Diagnostics.Trace.TraceWarning("Slow response - database01");

log4net や NLog を使用する場合

    logger.Warn("Slow response - database01");

デバッグ モードでアプリを実行するか、アプリをデプロイします。

トレース フィルターを選択すると、診断検索にメッセージが表示されます。

### <a name="exceptions"></a>例外

Application Insights で例外レポートを取得すると、特に、失敗した要求と例外間をナビゲートして例外スタックを確認できるようになるため、非常に強力なエクスペリエンスが提供されます。

例外テレメトリを送信するためのコードを記述できます。

JavaScript

    try 
    { ...
    }
    catch (ex)
    {
      appInsights.TrackException(ex, "handler loc",
        {Game: currentGame.Name, 
         State: currentGame.State.ToString()});
    }

C#

    var telemetry = new TelemetryClient();
    ...
    try 
    { ...
    }
    catch (Exception ex)
    {
       // Set up some properties:
       var properties = new Dictionary <string, string> 
         {{"Game", currentGame.Name}};

       var measurements = new Dictionary <string, double>
         {{"Users", currentGame.Users.Count}};

       // Send the exception telemetry:
       telemetry.TrackException(ex, properties, measurements);
    }

VB

    Dim telemetry = New TelemetryClient
    ...
    Try
      ...
    Catch ex as Exception
      ' Set up some properties:
      Dim properties = New Dictionary (Of String, String)
      properties.Add("Game", currentGame.Name)

      Dim measurements = New Dictionary (Of String, Double)
      measurements.Add("Users", currentGame.Users.Count)
  
      ' Send the exception telemetry:
      telemetry.TrackException(ex, properties, measurements)
    End Try

プロパティと測定値のパラメーターは省略可能ですが、フィルター処理と、特別な情報を追加するのに便利です。たとえば、複数のゲームを実行できるアプリケーションを使用している場合、1 つのゲームに関連する例外レポートをすべて検索できます。必要な数だけ項目を各辞書に追加できます。

#### 例外の表示

概要ブレードに表示される例外の概要をクリックすると、詳細が表示されます。次に例を示します。


![](./media/app-insights-web-failures-exceptions/appinsights-039-1exceptions.png)

例外の種類をクリックすると、実際に発生した例外について表示されます。

![](./media/app-insights-web-failures-exceptions/appinsights-333facets.png)

診断検索を直接開き、例外でフィルター処理し、表示する例外の種類を選択することもできます。

### ハンドルされていない例外のレポート

Application Insights は可能な場合、[Status Monitor][usage] と [Application Insights SDK][redfield] のどちらでインストルメントされたかにかかわらず、デバイス、[Web ブラウザー][greenbrown]、Web サーバーから送信された、ハンドルされていない例外をレポートします。

> [AZURE.NOTE]Web ブラウザーで、CDN や他のドメインからのスクリプト ファイルを組み込む場合、スクリプト タグに属性 ```crossorigin="anonymous"``` が含まれること、サーバーが CORS ヘッダーを送信して、スタック トレースやこれらのリソースからハンドルされない javascript 例外の詳細を取得することをご確認ください。

ただし、.NET フレームワークが例外をキャッチする場合もあるため、必ずレポートされるというわけではありません。したがって、すべての例外を確実に表示するためには、ちょっとした例外ハンドラーを作成する必要があります。最良の対処方法は、テクノロジによって異なります。詳細については、[このブログ](http://blogs.msdn.com/b/visualstudioalm/archive/2014/12/12/application-insights-exception-telemetry.aspx)をご覧ください。

### ビルドとの関連付け

診断ログを参照するとき、ソース コードは、現在のコードがデプロイされた後に変更されている可能性があります。

そのため、例外やトレースごとに、現行バージョンの URL などのビルド情報をプロパティに入れるようにすると便利です。

すべての例外呼び出しにプロパティを別途追加するのではなく、既定のコンテキストで情報を設定できます。

    // Telemetry initializer class
    public class MyTelemetryInitializer : IContextInitializer
    {
        public void Initialize (TelemetryContext context)
        {
            context.Properties["AppVersion"] = "v2.1";
        }
    }

アプリ初期化子 (Global.asax.cs など) 内:

    protected void Application_Start()
    {
        // ...
        TelemetryConfiguration.Active.ContextInitializers
        .Add(new MyTelemetryInitializer());
    }

###<a name="requests"></a> サーバー Web 要求

[Status Monitor を Web サーバーにインストールする][redfield]か、[Application Insights を Web プロジェクトに追加する][greenbrown]と、要求テレメトリが自動的に送信されます。また、このテレメトリは、メトリック エクスプローラーの要求と応答のタイムチャートや概要ページにもフィードされます。

その他のイベントを送信する場合は、TrackRequest() API を使用します。

## <a name="questions"></a>Q & A

### <a name="emptykey"></a>エラー「インストルメンテーション キーは空にできません」が発生しました

Application Insights をインストールしないでログ アダプターの Nuget パッケージをインストールした可能性があります。

ソリューション エクスプローラーで、`ApplicationInsights.config` を右クリックし、[**Application Insights の更新**] を選択します。Azure へのサインインを促すダイアログが表示されます。または、Application Insights のリソースを作成するか、既存のリソースを再利用します。これで問題は修正されます。

### <a name="limits"></a>保持されるデータの量はどのくらいですか

各アプリケーションで、1 秒あたり 500 イベントまでです。イベントは 7 日間保持されます。

## <a name="add"></a>次のステップ

* [可用性と応答性のテストを設定する][availability]
* [トラブルシューティング][qna]





<!--Link references-->

[availability]: app-insights-monitor-web-app-availability.md
[diagnostic]: app-insights-diagnostic-search.md
[greenbrown]: app-insights-start-monitoring-app-health-usage.md
[java]: app-insights-java-get-started.md
[metrics]: app-insights-metrics-explorer.md
[platforms]: app-insights-platforms.md
[qna]: app-insights-troubleshoot-faq.md
[redfield]: app-insights-monitor-performance-live-website-now.md
[start]: app-insights-get-started.md
[track]: app-insights-custom-events-metrics-api.md
[usage]: app-insights-web-track-usage.md

 

<!---HONumber=62-->