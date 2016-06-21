<properties 
	pageTitle="Application Insights を使用した ASP.NET の Web アプリ分析を設定する" 
	description="オンプレミスまたは Azure でホストされている ASP.NET Web サイトのパフォーマンス、可用性、利用状況の分析を構成します。" 
	services="application-insights" 
    documentationCenter=".net"
	authors="alancameronwills" 
	manager="douge"/>

<tags 
	ms.service="application-insights" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="ibiza" 
	ms.devlang="na" 
	ms.topic="get-started-article" 
	ms.date="06/08/2016" 
	ms.author="awills"/>


# ASP.NET 向けの Application Insights の設定

[Visual Studio Application Insights](app-insights-overview.md) は、実行中のアプリケーションを監視し、[パフォーマンスの問題や例外の検出と診断](app-insights-detect-triage-diagnose.md)、[アプリの使用方法の把握](app-insights-overview-usage.md)に役立ちます。Azure Web アプリに加えて、独自のオンプレミス IIS サーバーやクラウドの仮想マシンでホストされているアプリに対しても機能します。


## 開始する前に

必要なもの:

* Visual Studio 2013 Update 3 以降。より新しいバージョンが適しています。
* [Microsoft Azure](http://azure.com) のサブスクリプションチームまたは組織で Azure サブスクリプションを取得している場合、所有者は [Microsoft アカウント](http://live.com)を使用してあなたを追加できます。 

関心をお持ちの場合は、他にも次のような記事をご覧いただけます。

* [実行時の Web アプリのインストルメント化](app-insights-monitor-performance-live-website-now.md)
* [Azure Cloud Services](app-insights-cloudservices.md)

## <a name="ide"></a> 1.Application Insights SDK の追加


### 新しいプロジェクトの場合

Visual Studio で新しいプロジェクトを作成するときに、Application Insights が選択されていることを確認してください。


![ASP.NET プロジェクトを作成する](./media/app-insights-asp-net/appinsights-01-vsnewp1.png)


### 既存のプロジェクトの場合

ソリューション エクスプローラーでプロジェクトを右クリックし、**[Application Insights テレメトリの追加]** または **[Application Insights の構成]** を選択します。

![[Application Insights の追加] を選択する](./media/app-insights-asp-net/appinsights-03-addExisting.png)

* ASP.NET Core プロジェクトの場合 - [これらの手順に従って、コード行をいくつか修正します](https://github.com/Microsoft/ApplicationInsights-aspnetcore/wiki/Getting-Started#add-application-insights-instrumentation-code-to-startupcs)。 



## <a name="run"></a> 2.アプリケーションを実行する

F5 キーを使用してアプリケーションを実行して、試します。さまざまなページが開き、いくつかのテレメトリが生成されます。

Visual Studio で、ログに記録されたイベント数が表示されます。

![Visual Studio では、Application Insights ボタンはデバッグ時に表示されます。](./media/app-insights-asp-net/54.png)

## 3\.テレメトリの確認

### Visual Studio で確認する

[Application Insights] ボタンをクリックするか、次のようにソリューション エクスプローラーでプロジェクトを右クリックし、[Application Insights] ウィンドウを開きます。

![Visual Studio では、Application Insights ボタンはデバッグ時に表示されます。](./media/app-insights-asp-net/55.png)

このビューには、アプリのサーバー側で生成されたテレメトリが表示されます。フィルターを試したり、任意のイベントをクリックして詳細を表示したりしてみましょう。

[Visual Studio の Application Insights ツールの詳細については、こちらを参照してください](app-insights-visual-studio.md)。

<a name="monitor"></a>
### ポータルで確認する

*[SDK のみをインストール]* を選択した場合を除き、Application Insights Web ポータルでもテレメトリを確認できます。

ポータルには、Visual Studio より多くのグラフ、分析ツール、ダッシュボードが用意されています。


[Azure ポータル](https://portal.azure.com/)に Application Insights のリソースを開きます。

![プロジェクトを右クリックして Azure ポータルを開く](./media/app-insights-asp-net/appinsights-04-openPortal.png)

ポータルを開くと、アプリのテレメトリが表示されます。 ![](./media/app-insights-asp-net/66.png)

* 個々のイベントは **[検索]** (1) に表示されます。データはまずここ (および[ライブ メトリックス ストリーム](app-insights-metrics-explorer.md#live-metrics-stream)内) に表示されます。イベントをクリックすると、そのプロパティが表示されます。 
* メトリックの集計値はグラフ (2) に表示されます。データがここに表示されるまで、1 ～ 2 分かかる場合があります。グラフをクリックすると、詳細を含むブレードが開きます。

[Azure ポータルでの Application Insights の使用方法の詳細については、こちらを参照してください](app-insights-dashboards.md)。

##<a name="land"></a> [Application Insights の追加] の実行結果

Application Insights によって、アプリのテレメトリが (Microsoft Azure でホストされている) Application Insights ポータルに送信されます。

![](./media/app-insights-asp-net/01-scheme.png)

つまり、コマンドによって実行された操作は次の 3 つです。

1. プロジェクトに Application Insights Web SDK NuGet パッケージを追加する。Visual Studio で表示するには、プロジェクトを右クリックし、[NuGet パッケージの管理] を選択します。
2. [Azure ポータル](https://portal.azure.com/)で、Application Insights のリソースを作成する。ここにデータが表示されます。リソースを識別する*インストルメンテーション キー*を取得します。
3. `ApplicationInsights.config` にインストルメンテーション キーを挿入し、SDK がポータルにテレメトリを送信できるようにする。

必要な場合は、[ASP.NET 4](app-insights-asp-net-manual.md) または [ASP.NET Core](https://github.com/Microsoft/ApplicationInsights-aspnetcore/wiki/Getting-Started) 向けにこれらの手順を手動で実行することもできます。


## 次の手順

| | 
|---|---
|**[Visual Studio で Application Insights を操作する](app-insights-visual-studio.md)**<br/>テレメトリを使用したデバッグ、診断検索、コードのドリル スルー。|![Visual studio](./media/app-insights-asp-net/61.png)
|**[Application Insights ポータルを操作する](app-insights-dashboards.md)**<br/>ダッシュボード、強力な診断および分析ツール、アラート、アプリケーションのリアルタイム依存関係マップ、テレメトリのエクスポート。 |![Visual studio](./media/app-insights-asp-net/62.png)
|**[さらにデータを追加する](app-insights-asp-net-more.md)**<br/>使用状況、可用性、依存関係、例外を監視します。ログ記録フレームワークからのトレースを統合します。カスタム テレメトリを記述します。 | ![Visual studio](./media/app-insights-asp-net/64.png)

<!---HONumber=AcomDC_0615_2016-->