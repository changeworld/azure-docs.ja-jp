<properties 
	pageTitle="ASP.NET 向けの Application Insights" 
	description="オンプレミスまたは Microsoft Azure Web アプリケーションのパフォーマンス、可用性、使用状況を Application Insights で分析します。" 
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
	ms.date="10/13/2015" 
	ms.author="awills"/>


# ASP.NET 向けの Application Insights の設定

*Application Insights はプレビュー段階です。*

<a name="selector1"></a>

[Visual Studio Application Insights](http://azure.microsoft.com/services/application-insights) は、実行中のアプリケーションを監視し、[パフォーマンスの問題や例外の検出と診断][detect]、[アプリの使用方法の把握][knowUsers]に役立ちます。Application Insights は、アプリに SDK をインストールすることによって機能します。アプリに関するテレメトリが SDK から Application Insights サービスに送信されます。このサービスで、ユーザーはアプリの動作を分析および視覚化します。


[AZURE.INCLUDE [app-insights-selector-get-started-dotnet](../../includes/app-insights-selector-get-started-dotnet.md)]

Visual Studio でアプリに SDK を追加すると、サーバー要求、応答時間、およびエラーのグラフが表示されます。

![Example performance monitoring charts](./media/app-insights-asp-net/10-perf.png)

API を使用して、使用状況の詳細も監視できるようになります。

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

初めての場合には、Microsoft Azure にログインまたはサインインするように求められます

このアプリがより大きなアプリケーションの一部である場合は、**[設定の構成]** を使用して、他のコンポーネントと同じリソース グループに配置することをお勧めします。


####<a name="land"></a> [Application Insights の追加] の実行結果

このコマンドによって次の手順が実行されます (必要に応じて、代わりに[手動で実行](app-insights-start-monitoring-app-health-usage.md)できます)。

* [Azure ポータル][portal]に Application Insights のリソースが作成されます。ここにデータが表示されます。リソースを識別する*インストルメンテーション キー*を取得します。
* プロジェクトに Application Insights Web SDK NuGet パッケージが追加されます。Visual Studio で表示するには、プロジェクトを右クリックし、[NuGet パッケージの管理] を選択します。
* インストルメンテーション キーが `ApplicationInsights.config` に配置されます。


## <a name="run"></a> プロジェクトの実行

F5 キーを使用してアプリケーションを実行して、試します。さまざまなページが開き、いくつかのテレメトリが生成されます。

Visual Studio で、送信されたイベント数が表示されます。

![](./media/app-insights-asp-net/appinsights-09eventcount.png)

## <a name="monitor"></a> Application Insights を開く

[Azure ポータル][portal]に Application Insights のリソースを開きます。

![プロジェクトを右クリックして Azure ポータルを開く](./media/app-insights-asp-net/appinsights-04-openPortal.png)

### メトリック: 集計データ

概要グラフでデータを探します。最初、1 つまたは 2 つのポイントだけが表示されます。次に例を示します。

![クリックしてより多くのデータを表示する](./media/app-insights-asp-net/12-first-perf.png)

任意のグラフをクリックして、より詳細なメトリックを表示します。[メトリックの詳細についてはこちらをご覧ください。][perf]

* *ユーザーまたはページのデータがない場合* - [ユーザーとページのデータを追加します](../article/application-insights/app-insights-asp-net-client.md)。

### 検索: 個々のイベント

個々の要求とそれに関連するイベントを調査するには [検索] を開きます。

![](./media/app-insights-asp-net/21-search.png)

[検索の詳細についてはこちらを参照してください。](app-insights-diagnostic-search.md)

* *関連するイベントがない場合* - [サーバー例外](../article/application-insights/app-insights-asp-net-exception-mvc.md)と[依存関係](../article/application-insights/app-insights-asp-net-dependencies.md)を設定します。

### データが表示されない場合

* 対象が正しいことを確認します。[Azure ポータル](https://portal.azure.com)にサインインし、[参照]、[Application Insights] の順にクリックしてからアプリケーションを選択します。
* アプリケーションを使用して、テレメトリがいくつか生成されるようにさまざまなページを開きます。
* [[検索]][diagnostic] ブレードを開き、個々のイベントを表示します。メトリック パイプラインを経由すると、イベントの表示に少し時間がかかる場合があります。
* 数秒待機してから [最新の情報に更新] をクリックします。
* [トラブルシューティング][qna]に関するページを参照します。


## アプリケーションの発行

ここで、アプリケーションをデプロイし、データ累積を確認します。

デバッグ モードで実行している場合、テレメトリはパイプラインにより時間が短縮されるので、数秒でデータが表示されます。アプリケーションをデプロイすると、データ累積速度は遅くなります。

#### ビルド サーバーで問題が発生した場合

[このトラブルシューティング項目](app-insights-troubleshoot-faq.md#NuGetBuild)を参照してください。

## 次のステップ

- [ユーザーとページのデータ](../article/application-insights/app-insights-asp-net-client.md#selector1)
- [Exceptions](../article/application-insights/app-insights-asp-net-exception-mvc.md#selector1)
- [依存関係](../article/application-insights/app-insights-asp-net-dependencies.md#selector1)
- [可用性](../article/application-insights/app-insights-monitor-web-app-availability.md#selector1)





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
[start]: app-insights-overview.md

 

<!---HONumber=Oct15_HO4-->