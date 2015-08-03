<properties 
	pageTitle="ASP.NET 向けの Application Insights" 
	description="オンプレミスまたは Microsoft Azure Web アプリケーションの使用状況、可用性、パフォーマンスを Application Insights で分析します。" 
	services="application-insights" 
    documentationCenter=".net"
	authors="alancameronwills" 
	manager="douge"/>

<tags 
	ms.service="application-insights" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="ibiza" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="07/14/2015" 
	ms.author="awills"/>


# ASP.NET 向けの Application Insights

*Application Insights はプレビュー段階です。*

[AZURE.INCLUDE [app-insights-selector-get-started](../../includes/app-insights-selector-get-started.md)]


[Visual Studio Application Insights](http://azure.microsoft.com/services/application-insights) は、実行中のアプリケーションを監視し、[パフォーマンスの問題や例外の検出と診断][detect]、[アプリの使用方法の把握][knowUsers]に役立ちます。Application Insights は、さまざまな種類のアプリケーションで使用できます。Azure Web Apps に加えて、独自のオンプレミス IIS サーバーや Azure の仮想マシンでホストされているアプリに対しても機能します ([デバイス アプリと Java サーバーも対象になります][start])。

![Example performance monitoring charts](./media/app-insights-asp-net/10-perf.png)


#### 開始する前に

必要なもの:

* [Microsoft Azure](http://azure.com) のサブスクリプションチームまたは組織で Azure サブスクリプションを取得している場合、所有者は [Microsoft アカウント](http://live.com)を使用してあなたを追加できます。
* Visual Studio 2013 Update 3 以降。

## <a name="ide"></a> Application Insights を Visual Studio のプロジェクトに追加する

#### 新しいプロジェクトの場合

Visual Studio に新しいプロジェクトを作成するとき、Application Insights が選択されていることを確認してください。


![ASP.NET プロジェクトを作成する](./media/app-insights-asp-net/appinsights-01-vsnewp1.png)


#### 既存のプロジェクトの場合

ソリューション エクスプローラーでプロジェクトを右クリックし、[Application Insights の追加] を選択します。

![[Application Insights の追加] を選択する](./media/app-insights-asp-net/appinsights-03-addExisting.png)





#### セットアップ オプション

初めての場合には、Microsoft Azure プレビュー版にログインまたはサインインするように求められます(Visual Studio Online アカウントとは異なります)。

このアプリがより大きなアプリケーションの一部である場合は、**[設定の構成]** を使用して、他のコンポーネントと同じリソース グループに配置することをお勧めします。


####<a name="land"></a> [Application Insights の追加] の実行結果

このコマンドによって次の手順が実行されます (必要に応じて代わりに手動で実行できます)。

* [Azure ポータル][portal]に Application Insights のリソースが作成されます。ここにデータが表示されます。リソースを識別する*インストルメンテーション キー*を取得します。
* プロジェクトに Application Insights Web SDK NuGet パッケージが追加されます。Visual Studio で表示するには、プロジェクトを右クリックし、[NuGet パッケージの管理] を選択します。
* インストルメンテーション キーが `ApplicationInsights.config` に配置されます。


## <a name="run"></a> プロジェクトの実行

F5 キーを使用してアプリケーションを実行して、試します。さまざまなページが開き、いくつかのテレメトリが生成されます。

Visual Studio で、送信されたイベント数が表示されます。

![](./media/app-insights-asp-net/appinsights-09eventcount.png)

## <a name="monitor"></a> Application Insights を開く

[Azure ポータル][portal]で Application Insights のリソースを開きます。

![プロジェクトを右クリックして Azure ポータルを開く](./media/app-insights-asp-net/appinsights-04-openPortal.png)


概要グラフでデータを探します。最初、1 つまたは 2 つのポイントだけが表示されます。次に例を示します。

![クリックしてより多くのデータを表示する](./media/app-insights-asp-net/12-first-perf.png)

任意のグラフをクリックして、より詳細なメトリックを表示します。[メトリックの詳細についてはこちらをご覧ください。][perf]

ここで、アプリケーションをデプロイし、データ累積を確認します。


デバッグ モードで実行している場合、テレメトリはパイプラインにより時間が短縮されるので、数秒でデータが表示されます。アプリケーションをデプロイすると、データ累積速度は遅くなります。

#### データが表示されない場合

* [[検索]][diagnostic] タイルを開き、個々のイベントを表示します。
* アプリケーションを使用して、テレメトリがいくつか生成されるようにさまざまなページを開きます。
* 数秒待機してから [最新の情報に更新] をクリックします。
* [トラブルシューティング][qna]に関するページを参照します。

#### ビルド サーバーで問題が発生した場合

[このトラブルシューティング項目](app-insights-troubleshoot-faq.md#NuGetBuild)を参照してください。


## ブラウザーの監視を追加する

ブラウザーまたはクライアント側の監視は、ユーザー、セッション、ページ ビュー、およびブラウザーで発生する例外やクラッシュに関するデータを提供します。

![[新規]、[開発者向けサービス]、[Application Insights] の順に選択する。](./media/app-insights-asp-net/16-page-views.png)

また、独自のコードを記述して、ユーザーのアプリの操作をクリックやキーボード操作までの細部にわたって追跡できます。

#### クライアントが Web ブラウザーの場合

アプリが Web ページに表示される場合は、JavaScript のスニペットをすべてのページに追加します。コードは次に示す Application Insights のリソースから取得できます。

![In your web app, open Quick Start and click 'Get code to monitor my web pages'](./media/app-insights-asp-net/02-monitor-web-page.png)

コードにはアプリケーション リソースを識別するインストルメンテーション キーが含まれています。

[Web ページの追跡については、こちらを参照してください。](app-insights-web-track-usage.md)

#### クライアントがデバイス アプリの場合

アプリケーションがスマートフォンなどのデバイスなどのクライアントに配信される場合は、デバイス アプリに[適切な SDK](app-insights-platforms.md) を追加します。

SDK クライアントをサーバーの SDK と同じインストルメンテーション キーで構成する場合、まとめて確認できるように 2 つのストリームが統合されます。

## 使用状況の追跡

新しいユーザー ストーリーを提供した際には、顧客がどの程度使用しているのか、目標を達成しているのか、または問題に直面しているのかを知る必要があります。クライアントおよびサーバーの両方に TrackEvent() とその他のコードを挿入することで、ユーザーの利用状況の詳細を把握できます。

[API を使用した利用状況の追跡][api]


## 診断ログ

お気に入りのログ記録フレームワークから[ログ トレースをキャプチャ][netlogs]して問題の診断に役立てます。ログ エントリが Application Insights のテレメトリ イベントと共に[診断検索][diagnostic]に表示されます。

## アプリケーションの発行

(Application Insights を追加するために) アプリをまだ発行していない場合は、今すぐ発行してください。ユーザーがアプリを使用するにつれてグラフのデータが増えます。

### 開発、テスト、およびリリース用に別のリソースを用意します。

主なアプリケーションでは、デバッグ環境、テスト環境、および運用環境からのテレメトリ データ[別のリソース](app-insights-separate-resources.md)に送信することをお勧めします。



## 依存関係の追跡を追加する

[依存関係のメトリック](app-insights-dependencies.md)は、パフォーマンスに関する問題の診断に非常に役立つ場合があります。これらは、アプリからデータベース、REST API、およびその他の外部コンポーネントへの呼び出しを測定します。

![](./media/app-insights-asp-net/04-dependencies.png)

#### アプリが IIS サーバーで実行されている場合

管理者権限でサーバーにログインし、[Application Insights Status Monitor](http://go.microsoft.com/fwlink/?LinkId=506648) をインストールします

(Status Monitor は、SDK で構築されていない場合でも、[既に実行中のアプリをインストルメント化](app-insights-monitor-performance-live-website-now.md)するためにも使用できます)。

#### アプリが Azure の Web アプリの場合

Azure の Web アプリのコントロール パネルで、Application Insights 拡張機能を追加します。

![In your web app, Settings, Extensions, Add, Application Insights](./media/app-insights-asp-net/05-extend.png)

(この拡張機能は、SDK で構築されたアプリのみをサポートします。Status Monitor とは異なり、既存のアプリはインストルメント化できません。)

## 可用性 Web テスト

[Web テストをセットアップ][availability]して、外部からアプリケーションの稼働状態と応答性をテストします。


![](./media/app-insights-asp-net/appinsights-10webtestresult.png)






## 新しいバージョンの SDK にアップグレードするには

[SDK の新しいリリース](app-insights-release-notes-dotnet.md)にアップグレードするには、NuGet パッケージ マネージャーをもう一度開き、インストールされているパッケージに対してフィルターを実行します。[Microsoft.ApplicationInsights.Web]、[アップグレード] の順に選択します。

ApplicationInsights.config をカスタマイズしている場合は、アップグレードする前にコピーを保存しておき、後から新しいバージョンに変更をマージします。



## <a name="video"></a>ビデオ

> [AZURE.VIDEO getting-started-with-application-insights]


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
[redfield]: app-insights-monitor-performance-live-website-now.md
[roles]: app-insights-resources-roles-access-control.md
[start]: app-insights-get-started.md

 

<!---HONumber=July15_HO4-->