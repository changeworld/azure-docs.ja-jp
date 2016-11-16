---
title: "Application Insights を使用した ASP.NET の Web アプリ分析を設定する | Microsoft Docs"
description: "オンプレミスまたは Azure でホストされている ASP.NET Web サイトのパフォーマンス、可用性、利用状況の分析を構成します。"
services: application-insights
documentationcenter: .net
author: NumberByColors
manager: douge
ms.assetid: d0eee3c0-b328-448f-8123-f478052751db
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: get-started-article
ms.date: 10/13/2016
ms.author: awills
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: b6e5898d94a43b6859ce354f154bdb25948f7686


---
# <a name="set-up-application-insights-for-aspnet"></a>ASP.NET 向けの Application Insights の設定
[Visual Studio Application Insights](app-insights-overview.md) は、実行中のアプリケーションを監視し、[パフォーマンスの問題や例外の検出と診断](app-insights-detect-triage-diagnose.md)、[アプリの使用方法の把握](app-insights-overview-usage.md)に役立ちます。  Azure Web アプリに加えて、独自のオンプレミス IIS サーバーやクラウドの仮想マシンでホストされているアプリに対しても機能します。

## <a name="before-you-start"></a>開始する前に
必要なもの:

* Visual Studio 2013 Update 3 以降。 より新しいバージョンが適しています。
* [Microsoft Azure](http://azure.com) サブスクリプション。 チームまたは組織で Azure サブスクリプションを取得している場合、所有者は [Microsoft アカウント](http://live.com)を使用してあなたを追加できます。 

関心をお持ちの場合は、他にも次のような記事をご覧いただけます。

* [実行時の Web アプリのインストルメント化](app-insights-monitor-performance-live-website-now.md)
* [Azure Cloud Services](app-insights-cloudservices.md)

## <a name="a-nameidea-1-add-application-insights-sdk"></a><a name="ide"></a> 1.Application Insights SDK の追加
### <a name="if-its-a-new-project"></a>新しいプロジェクトの場合
Visual Studio で新しいプロジェクトを作成するときに、Application Insights が選択されていることを確認してください。 

![ASP.NET プロジェクトを作成する](./media/app-insights-asp-net/appinsights-01-vsnewp1.png)

### <a name="-or-if-its-an-existing-project"></a>既存のプロジェクトの場合
ソリューション エクスプローラーでプロジェクトを右クリックし、**[Application Insights Telemetry の追加]** または **[Application Insights の構成]** を選択します。

![[Application Insights の追加] を選択する](./media/app-insights-asp-net/appinsights-03-addExisting.png)

* ASP.NET Core プロジェクトの場合 [これらの手順に従って、コード行をいくつか修正します](https://github.com/Microsoft/ApplicationInsights-aspnetcore/wiki/Getting-Started#add-application-insights-instrumentation-code-to-startupcs)。 

## <a name="a-nameruna-2-run-your-app"></a><a name="run"></a> 2.アプリケーションを実行する
F5 キーを使用してアプリケーションを実行して、試します。さまざまなページが開き、いくつかのテレメトリが生成されます。

Visual Studio で、ログに記録されたイベントの数が表示されます。 

![Visual Studio では、Application Insights ボタンはデバッグ時に表示されます。](./media/app-insights-asp-net/54.png)

## <a name="3-see-your-telemetry"></a>3.テレメトリの確認
### <a name="-in-visual-studio"></a>Visual Studio で確認する
[Application Insights] ボタンをクリックするか、次のようにソリューション エクスプローラーでプロジェクトを右クリックし、[Application Insights] ウィンドウを開きます。

![Visual Studio では、Application Insights ボタンはデバッグ時に表示されます。](./media/app-insights-asp-net/55.png)

このビューには、アプリのサーバー側で生成されたテレメトリが表示されます。 フィルターを試したり、任意のイベントをクリックして詳細を表示したりしてみましょう。

[Visual Studio の Application Insights ツールの詳細については、こちらを参照してください](app-insights-visual-studio.md)。

<a name="monitor"></a> 

### <a name="-in-the-portal"></a>ポータルで確認する
*[SDK のみをインストール]* を選択した場合を除き、Application Insights Web ポータルでもテレメトリを確認できます。 

ポータルには、Visual Studio より多くのグラフ、分析ツール、ダッシュボードが用意されています。 

[Azure ポータル](https://portal.azure.com/)に Application Insights のリソースを開きます。

![プロジェクトを右クリックして Azure ポータルを開く](./media/app-insights-asp-net/appinsights-04-openPortal.png)

ポータルを開くと、アプリのテレメトリが表示されます。 ![](./media/app-insights-asp-net/66.png)

* [ライブ メトリック ストリーム](app-insights-metrics-explorer.md#live-metrics-stream)に最初のテレメトリが表示されます。
* **[検索]** (1) に個々のイベントが表示されます。 データが表示されるまでに数分かかることがあります。 イベントのプロパティを表示するには、イベントをクリックします。 
* メトリックの集計値はグラフ (2) に表示されます。 データがここに表示されるまで、1 ～ 2 分かかる場合があります。 グラフをクリックすると、詳細を含むブレードが開きます。

[Azure ポータルでの Application Insights の使用方法の詳細については、こちらを参照してください](app-insights-dashboards.md)。

## <a name="4-publish-your-app"></a>4.アプリケーションの発行
IIS サーバーまたは Azure にアプリを発行します。 [ライブ メトリック ストリーム](app-insights-metrics-explorer.md#live-metrics-stream) を観察して、必要な処理がすべて滞りなく実行されていることを確認してください。

Application Insights ポータルにはテレメトリが蓄積されており、メトリックを監視したり、目的のテレメトリを検索したり、 [ダッシュボード](app-insights-dashboards.md)を設定したりすることができます。 強力な [Analytics クエリ言語](app-insights-analytics.md) を使って使用状況やパフォーマンスを分析したり、特定のイベントを見つけたりすることができます。 

[Visual Studio](app-insights-visual-studio.md) から各種ツール (診断検索、[傾向](app-insights-visual-studio-trends.md)など) を使って継続的にテレメトリを分析することもできます。

> [!NOTE]
> アプリから送信されたテレメトリの量が[スロットル制限](app-insights-pricing.md#limits-summary)に近づくと、自動[サンプリング](app-insights-sampling.md)がオンに変わります。 アプリから送信されるテレメトリの量をサンプリングによって抑えながら、診断に利用できる相関性のあるデータを維持することができます。
> 
> 

## <a name="a-namelanda-what-did-add-application-insights-do"></a><a name="land"></a> [Application Insights の追加] の実行結果
Application Insights によって、アプリのテレメトリが (Microsoft Azure でホストされている) Application Insights ポータルに送信されます。

![](./media/app-insights-asp-net/01-scheme.png)

つまり、コマンドによって実行された操作は次の 3 つです。

1. プロジェクトに Application Insights Web SDK NuGet パッケージを追加する。 Visual Studio で表示するには、プロジェクトを右クリックし、[NuGet パッケージの管理] を選択します。
2. [Azure ポータル](https://portal.azure.com/)で、Application Insights のリソースを作成する。 ここにデータが表示されます。 リソースを識別する *インストルメンテーション キー* を取得します。
3. `ApplicationInsights.config`にインストルメンテーション キーを挿入し、SDK がポータルにテレメトリを送信できるようにする。

必要な場合は、[ASP.NET 4](app-insights-windows-services.md) または [ASP.NET Core](https://github.com/Microsoft/ApplicationInsights-aspnetcore/wiki/Getting-Started) 向けにこれらの手順を手動で実行することもできます。

### <a name="to-upgrade-to-future-sdk-versions"></a>新しいバージョンの SDK にアップグレードするには
[SDK の新しいリリース](https://github.com/Microsoft/ApplicationInsights-dotnet-server/releases)にアップグレードするには、NuGet パッケージ マネージャーをもう一度開き、インストールされているパッケージに対してフィルターを実行します。 [Microsoft.ApplicationInsights.Web]、[アップグレード] の順に選択します。

ApplicationInsights.config をカスタマイズしている場合は、アップグレードする前にコピーを保存しておき、後から新しいバージョンに変更をマージします。

## <a name="add-more-telemetry"></a>テレメトリをさらに追加
### <a name="web-pages-and-singlepage-apps"></a>Web ページおよび単一ページ アプリ
1. ページ ビュー、読み込み時間、ブラウザーの例外、AJAX 呼び出しのパフォーマンス、ユーザーとセッションの数に関するデータを [ブラウザー] ブレードと [使用状況] ブレードに表示するために、Web ページに [JavaScript スニペットを追加](app-insights-javascript.md)します。
2. ユーザー アクションのカウント、時間の計測、または測定を行う[カスタム イベントのコードを記述](app-insights-api-custom-events-metrics.md)します。

### <a name="dependencies-exceptions-and-performance-counters"></a>依存関係、例外、パフォーマンス カウンター
アプリに関する追加のテレメトリを取得するために、各サーバー コンピューターに [Status Monitor をインストール](app-insights-monitor-performance-live-website-now.md)します。 取得できるテレメトリは次のとおりです。

* [パフォーマンス カウンター](app-insights-performance-counters.md) - 
  アプリに関連する CPU、メモリ、ディスクなどのパフォーマンス カウンターです。 
* [例外](app-insights-asp-net-exceptions.md) - 一部の例外の詳細なテレメトリです。
* [依存関係](app-insights-asp-net-dependencies.md) - REST API または SQL サービスへの呼び出しです。 外部コンポーネントの応答が遅いことが原因でアプリのパフォーマンスの問題が発生しているかどうかを確認できます (アプリが .NET 4.6 で実行される場合、Status Monitor がなくても、このテレメトリを取得できます)。

### <a name="diagnostic-code"></a>診断コード
問題が発生した場合に備えて 診断に役立つコードをアプリに挿入する場合は、いくつかのオプションがあります。

* [ログ トレースのキャプチャ](app-insights-asp-net-trace-logs.md): 既に Log4N、NLog、または System.Diagnostics.Trace を使用して、トレース イベントをログに記録している場合は、出力を Application Insights に送信し、要求と関連付けて、検索、分析できます。 
* [カスタム イベントとメトリック](app-insights-api-custom-events-metrics.md): サーバーまたは Web ページ コードで TrackEvent() と TrackMetric() のどちらかを使用します。
* [テレメトリへの追加のプロパティのタグ付け](app-insights-api-filtering-sampling.md#add-properties)

[検索](app-insights-diagnostic-search.md)を使用して、特定のイベントを検索して関連付け、[Analytics](app-insights-analytics.md) でより強力なクエリを実行します。

## <a name="alerts"></a>アラート
率先してアプリに問題があるかどうかを確認します (ユーザーからの指摘を待たないでください!)。 

* サイトが Web で表示できることを確認するには、[Web テストを作成](app-insights-monitor-web-app-availability.md)します。
* [プロアクティブ診断](app-insights-proactive-diagnostics.md)が自動的に実行されます (アプリに特定の最小限のトラフィックがある場合)。 何も設定する必要ありません。 アプリの要求が失敗する割合が異常な場合に通知します。
* メトリックがしきい値を超えた場合に警告するように[メトリック アラートを設定](app-insights-alerts.md)します。 メトリック アラートはカスタム メトリックで設定し、コード化してアプリに組み込むことができます。

既定では、アラート通知は Azure サブスクリプションの所有者に送信されます。 

![アラート メールのサンプル](./media/app-insights-asp-net/alert-email.png)

## <a name="version-and-release-tracking"></a>バージョンおよびリリースの追跡
### <a name="track-application-version"></a>アプリケーションのバージョンを追跡する
MSBuild プロセスで `buildinfo.config` が生成されていることを確認します。 .csproj ファイルに、次のコードを追加します。  

```XML

    <PropertyGroup>
      <GenerateBuildInfoConfigFile>true</GenerateBuildInfoConfigFile>    <IncludeServerNameInBuildInfo>true</IncludeServerNameInBuildInfo>
    </PropertyGroup> 
```

ビルド情報がある場合、Application Insights Web モジュールは、 **アプリケーションのバージョン** をプロパティとしてテレメトリのすべての項目に自動的に追加します。 これにより、[診断の検索](app-insights-diagnostic-search.md)を実行するとき、または[メトリックを調べる](app-insights-metrics-explorer.md)ときに、バージョンによってフィルター処理できます。 

ただし、Visual Studio で開発者向けのビルドではなく、MS ビルドでのみビルド バージョン番号が生成されることに注意してください。

### <a name="release-annotations"></a>リリース注釈
Visual Studio Team Services を使用する場合は、新しいバージョンをリリースするたびに、グラフに[注釈マーカーを追加](app-insights-annotations.md)できます。

![リリース注釈のサンプル](./media/app-insights-asp-net/release-annotation.png)

## <a name="next-steps"></a>次のステップ
|  |
| --- | --- |
| **[Visual Studio での Application Insights の操作](app-insights-visual-studio.md)**<br/>テレメトリを使用したデバッグ、診断検索、コードのドリル スルー。 |
| **[Application Insights ポータルの操作](app-insights-dashboards.md)**<br/>ダッシュボード、強力な診断および分析ツール、アラート、アプリケーションのリアルタイム依存関係マップ、テレメトリのエクスポート。 |
| **[データの追加](app-insights-asp-net-more.md)**<br/>使用状況、可用性、依存関係、例外の監視。 ログ記録フレームワークからのトレースを統合します。 カスタム テレメトリを記述します。 |




<!--HONumber=Nov16_HO2-->


