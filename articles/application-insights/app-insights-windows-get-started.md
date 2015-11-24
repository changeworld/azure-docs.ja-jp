<properties
	pageTitle="Windows Phone アプリとストア アプリの分析 | Microsoft Azure"
	description="Windows デバイス アプリの使用状況とパフォーマンスを分析します。"
	services="application-insights"
    documentationCenter="windows"
	authors="alancameronwills"
	manager="douge"/>

<tags
	ms.service="application-insights"
	ms.workload="tbd"
	ms.tgt_pltfrm="ibiza"
	ms.devlang="na"
	ms.topic="get-started-article"
	ms.date="11/11/2015"
	ms.author="awills"/>

# Windows Phone アプリとストア アプリの分析



Visual Studio Application Insights を使用すると、発行されたアプリケーションの使用状況とパフォーマンスを監視できます。


> [AZURE.NOTE]クラッシュ レポートの取得、分析、配布、およびフィードバックの管理には、[HockeyApp](http://support.hockeyapp.net/kb/client-integration-windows-and-windows-phone/hockeyapp-for-windows-store-apps-and-windows-phone-store-apps) をお勧めします。

![](./media/app-insights-windows-get-started/appinsights-d018-oview.png)


## Windowsデバイス プロジェクト用の Application Insights の設定

必要なものは次のとおりです。

* [Microsoft Azure][azure] のサブスクリプション
* Visual Studio 2013 以降

**C++ UAP アプリ** - [Application Insights C++ セットアップ ガイド](https://github.com/Microsoft/ApplicationInsights-CPP)を参照してください

### <a name="new"></a>新しい Windows アプリ プロジェクトを作成する場合...

**[新しいプロジェクト]** ダイアログ ボックスで **[Application Insights]** を選択します。

サインインが要求されたら、Azure アカウントの資格情報を使用します。

![](./media/app-insights-windows-get-started/appinsights-d21-new.png)


### <a name="existing"></a>既存のプロジェクトの場合...

ソリューション エクスプローラーから Application Insights を追加します。


![](./media/app-insights-windows-get-started/appinsights-d22-add.png) **Windows ユニバーサル アプリ**: Phone プロジェクトとストア プロジェクトの両方に対して繰り返します。[Windows 8.1 ユニバーサル アプリの例](https://github.com/Microsoft/ApplicationInsights-Home/tree/master/Samples/Windows%208.1%20Universal)。

## <a name="network"></a>3.アプリのネットワーク アクセスの有効化

アプリで[インターネット アクセスを要求](https://msdn.microsoft.com/library/windows/apps/hh452752.aspx)していない場合は、[必要な機能](https://msdn.microsoft.com/library/windows/apps/br211477.aspx)としてとしてそれをマニフェストに追加する必要があります。

## <a name="run"></a>4.プロジェクトの実行

[F5 キーを押してアプリケーションを実行](http://msdn.microsoft.com/library/windows/apps/bg161304.aspx)し、これを使用してテレメトリを生成します。

Visual Studio で、受け取ったイベント数を確認できます。

![](./media/app-insights-windows-get-started/appinsights-09eventcount.png)

デバッグ モードでは、テレメトリは生成されるとすぐに送信されます。リリース モードでは、テレメトリはデバイスに格納され、アプリケーションの再開時にのみ送信されます。


## <a name="monitor"></a>5.監視データの表示

[Azure ポータル](https://portal.azure.com)で、以前に作成した Application Insights リソースを開きます。

最初、1 つまたは 2 つのポイントだけが表示されます。次に例を示します。

![クリックしてより多くのデータを表示する](./media/app-insights-windows-get-started/appinsights-26-devices-01.png)

大量のデータが予想される場合は、数秒後に **[最新の情報に更新]** をクリックします。

いずれかのグラフをクリックして、詳細を表示します。


## <a name="deploy"></a>5.ストアへのアプリケーションの発行

[アプリケーションを発行](http://dev.windows.com/publish)して、ユーザーがそのアプリケーションをダウンロードして使用すると累積されるデータを確認します。

## テレメトリのカスタマイズ

#### コレクターの選択

Application Insights SDK には、さまざまな種類のデータをアプリから自動的に収集する、複数のコレクターが用意されています。既定では、これらはすべてアクティブになります。ただし、アプリ コンストラクターでどのコレクターを初期化するかを選択することができます。

    WindowsAppInitializer.InitializeAsync( "00000000-0000-0000-0000-000000000000",
       WindowsCollectors.Metadata
       | WindowsCollectors.PageView
       | WindowsCollectors.Session
       | WindowsCollectors.UnhandledException);

#### 独自のテレメトリ データを送信する

[API][api] を使用して、イベント、メトリック、および診断データを Application Insights に送信します。概要:

```C#

 var tc = new TelemetryClient(); // Call once per thread

 // Send a user action or goal:
 tc.TrackEvent("Win Game");

 // Send a metric:
 tc.TrackMetric("Queue Length", q.Length);

 // Provide properties by which you can filter events:
 var properties = new Dictionary{"game", game.Name};

 // Provide metrics associated with an event:
 var measurements = new Dictionary{"score", game.score};

 tc.TrackEvent("Win Game", properties, measurements);

```

詳細については、[カスタムのイベントとメトリックに関する記事][api]を参照してください。

## 次の手順

* [アプリのクラッシュの検出と診断][windowsCrash]
* [メトリックの詳細][metrics]
* [診断検索の詳細][diagnostic]




## SDK の新しいリリースにアップグレードする

[新しい SDK がリリースされた](app-insights-release-notes-windows.md)場合:

* プロジェクトを右クリックし、[NuGet パッケージの管理] を選択します。
* インストール済みの Application Insights パッケージを選択し、**[アップグレード]** アクションを選択します。


## <a name="usage"></a>次のステップ


[アプリのクラッシュの検出と診断][windowsCrash]

[診断ログのキャプチャと検索][diagnostic]


[アプリの使用状況の追跡][windowsUsage]

[API を使用したカスタム テレメトリの送信][api]

[トラブルシューティング][qna]



<!--Link references-->

[api]: app-insights-api-custom-events-metrics.md
[azure]: ../insights-perf-analytics.md
[diagnostic]: app-insights-diagnostic-search.md
[metrics]: app-insights-metrics-explorer.md
[portal]: http://portal.azure.com/
[qna]: app-insights-troubleshoot-faq.md
[roles]: app-insights-resources-roles-access-control.md
[windowsCrash]: app-insights-windows-crashes.md
[windowsUsage]: app-insights-windows-usage.md

<!---HONumber=Nov15_HO4-->