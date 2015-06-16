<properties 
	pageTitle="ASP.NET 5 向けの Application Insights" 
	description="Web アプリケーションの可用性、パフォーマンス、使用状況を監視します。" 
	services="application-insights" 
    documentationCenter=".net"
	authors="alancameronwills" 
	manager="ronmart"/>

<tags 
	ms.service="application-insights" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="ibiza" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="05/27/2015" 
	ms.author="awills"/>

# ASP.NET 5 向けの Application Insights

Visual Studio Application Insights を使うと、Web アプリケーションの可用性、パフォーマンス、利用状況を監視できます。アプリのパフォーマンスと効果に関するフィードバックが得られたら、各開発ライフサイクルにおける設計の方向性について、情報に基づいて選択できます。

![例](./media/app-insights-asp-net-five/sample.png)

[Microsoft Azure](http://azure.com) のサブスクリプションが必要になります。Windows、XBox Live、またはその他の Microsoft クラウド サービスに与えられる Microsoft アカウントでサインインします。

## ASP.NET 5 プロジェクトを作成する

まだ、それを作成していない場合。

Visual Studio 2015 の標準の ASP.NET 5 プロジェクト テンプレートを使用します。


## Application Insights リソースの作成

[Azure ポータル][portal]で、Application Insights の新しいリソースを作成します。ASP.NET オプションを選択します。

![[新規]、[開発者向けサービス]、[Application Insights] の順にクリックする](./media/app-insights-asp-net-five/01-new-asp.png)

表示される [[リソース][roles]] ブレードには、アプリに関するパフォーマンスと使用状況データが表示されます。次に Azure にログインするときにこのブレードに戻るには、スタート画面でそのタイルを見つけてください。あるいは、[参照] ボタンをクリックして探します。

アプリケーションの種類を選択すると、[リソース] ブレードの既定のコンテンツと[メトリック エクスプ ローラー][metrics]に表示されるプロパティが設定されます。

##  インストルメンテーション キーでプロジェクトを構成します。

Application Insights リソースからキーをコピーします。

![[プロパティ] をクリックし、キーを選択して、Ctrl キーを押しながら C キーを押す](./media/app-insights-asp-net-five/02-props-asp.png)

ASP.NET 5 プロジェクトで、`config.json` にキーを貼り付けます。

    {
      "ApplicationInsights": {
        "InstrumentationKey": "11111111-2222-3333-4444-555555555555"
      }
    }

あるいは、構成を動的にする場合、このコードをアプリケーションのスタートアップ クラスに追加できます。

    configuration.AddApplicationInsightsSettings(
      instrumentationKey: "11111111-2222-3333-4444-555555555555");


## アプリケーションの正常性と利用状況の監視


#### NuGet パッケージを参照する

NuGet パッケージの [最新のリリース番号](https://github.com/Microsoft/ApplicationInsights-aspnet5/releases)を探します。

`project.json` を開き、`dependencies` セクションを編集します。

    {
      "dependencies": {
        // Replace 0.* with a specific version:
        "Microsoft.ApplicationInsights.AspNet": "0.*",

       // Add these if they aren't already there:
       "Microsoft.Framework.ConfigurationModel.Interfaces": "1.0.0-beta4",
       "Microsoft.Framework.ConfigurationModel.Json":  "1.0.0-beta4"
      }
    }

#### 構成ファイルを解析する

`startup.cs` で:

    using Microsoft.ApplicationInsights.AspNet;

    public IConfiguration Configuration { get; set; }

`Startup` メソッドで:

    // Setup configuration sources.
    var configuration = new Configuration()
       .AddJsonFile("config.json")
       .AddJsonFile($"config.{env.EnvironmentName}.json", optional: true);
    configuration.AddEnvironmentVariables();
    Configuration = configuration;

    if (env.IsEnvironment("Development"))
    {
      configuration.AddApplicationInsightsSettings(developerMode: true);
    }

`ConfigurationServices` メソッドで:

    services.AddApplicationInsightsTelemetry(Configuration);

`Configure` メソッドで:

    // Add Application Insights monitoring to the request pipeline as a very first middleware.
    app.UseApplicationInsightsRequestTelemetry();

    // Any other error handling middleware goes here.

    // Add Application Insights exceptions handling to the request pipeline.
    app.UseApplicationInsightsExceptionTelemetry();

## JavaScript クライアント インストルメンテーションを追加する

_Layout.cshtml ファイルがある場合、そこに次のコードを挿入します。なければ、追跡するいずれかのページにコードを置きます。

ファイルの最上部でインジェクションを定義します。

    @inject Microsoft.ApplicationInsights.Extensibility.TelemetryConfiguration TelemetryConfiguration

`</head>` タグの前とその他のスクリプトの前に Html ヘルパーを挿入します。ページから報告するカスタム JavaScript の利用統計情報があれば、このスニペットの後に挿入します。

    <head> 

      @Html.ApplicationInsightsJavaScript(TelemetryConfiguration) 

      <!-- other scripts -->
    </head>

## アプリケーションを実行する

Visual Studio でアプリケーションをデバッグするか、それを Web サーバーに公開します。

## アプリに関するデータを表示する

[Azure ポータル][portal]に戻り、Application Insights のリソースを参照します。[概要] ブレードにデータがない場合、1 ～ 2 分待ってから [更新] をクリックします。

* [アプリの使用状況の追跡][usage]
* [パフォーマンス問題の診断][detect]
* [可用性を監視するための Web テストの設定][availability]



## オープン ソース

[コードを読んで協力してください](https://github.com/Microsoft/ApplicationInsights-aspnet5)。


<!--Link references-->

[api]: app-insights-api-custom-events-metrics.md
[apikey]: app-insights-api-custom-events-metrics.md#ikey
[availability]: app-insights-monitor-web-app-availability.md
[azure]: ../insights-perf-analytics.md
[client]: app-insights-javascript.md
[detect]: app-insights-detect-triage-diagnose.md
[diagnostic]: app-insights-diagnostic-search.md
[knowUsers]: app-insights-overview-usage.md
[metrics]: app-insights-metrics-explorer.md
[netlogs]: app-insights-asp-net-trace-logs.md
[perf]: app-insights-web-monitor-performance.md
[portal]: http://portal.azure.com/
[qna]: app-insights-troubleshoot-faq.md
[roles]: app-insights-resources-roles-access-control.md
[start]: app-insights-get-started.md
[usage]: app-insights-web-track-usage.md

<!---HONumber=58--> 