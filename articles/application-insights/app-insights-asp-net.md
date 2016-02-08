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
	ms.date="01/26/2016" 
	ms.author="awills"/>


# ASP.NET 向けの Application Insights の設定


[AZURE.INCLUDE [app-insights-selector-get-started-dotnet](../../includes/app-insights-selector-get-started-dotnet.md)]

Application Insights SDK は実行中の Web アプリケーションから Azure ポータルにテレメトリを送信します。Azure ポータルにサインインすれば、アプリのパフォーマンスや使用状況をグラフで確認できます。

![パフォーマンス監視グラフのサンプル](./media/app-insights-asp-net/10-perf.png)

特定の要求、例外、ログ イベントを調査し、相互に関連付けることもできます。API を利用してテレメトリを追加し、パフォーマンスと使用状況を詳しく監視できます。

#### 開始する前に

必要なもの:

* [Microsoft Azure](http://azure.com) のサブスクリプションチームまたは組織で Azure サブスクリプションを取得している場合、所有者は [Microsoft アカウント](http://live.com)を使用してあなたを追加できます。
* Visual Studio 2013 Update 3 以降。

## <a name="ide"></a> Application Insights を Visual Studio のプロジェクトに追加する

#### 新しいプロジェクトの場合

Visual Studio に新しいプロジェクトを作成するとき、Application Insights が選択されていることを確認してください。


![ASP.NET プロジェクトを作成する](./media/app-insights-asp-net/appinsights-01-vsnewp1.png)

Azure にサインインしているアカウントを選択します。資格情報の再入力を求められる場合があります。(または、サインインしない場合は、SDK のコードが追加され、後で構成することができます)。


#### 既存のプロジェクトの場合

ソリューション エクスプローラーでプロジェクトを右クリックし、**[Application Insights の追加]** または **[Application Insights の構成]** を選択します。

![[Application Insights の追加] を選択する](./media/app-insights-asp-net/appinsights-03-addExisting.png)


#### セットアップ オプション

初めての場合には、Microsoft Azure にサインインまたサインアップするように求められます

このアプリがより大きなアプリケーションの一部である場合は、**[設定の構成]** を使用して、他のコンポーネントと同じリソース グループに配置することをお勧めします。


####<a name="land"></a> [Application Insights の追加] の実行結果

このコマンドによって次の手順が実行されます (必要に応じて、代わりに[手動で実行](app-insights-start-monitoring-app-health-usage.md)できます)。

1. プロジェクトに Application Insights Web SDK NuGet パッケージが追加されます。Visual Studio で表示するには、プロジェクトを右クリックし、[NuGet パッケージの管理] を選択します。
2. [Azure ポータル][portal]に Application Insights のリソースが作成されます。ここにデータが表示されます。リソースを識別する*インストルメンテーション キー*を取得します。
3. `ApplicationInsights.config` にインストルメンテーション キーを挿入し、SDK がポータルにテレメトリを送信できるようにします。

最初に Azure にサインインしないと、SDK がリソースに接続されずにインストールされます。Visual Studio の診断ハブでは、デバッグ時に Application Insights テレメトリを表示および検索することができます。他の手順は後で完了することができます。

## <a name="run"></a> プロジェクトの実行

F5 キーを使用してアプリケーションを実行して、試します。さまざまなページが開き、いくつかのテレメトリが生成されます。

Visual Studio で、送信されたイベント数が表示されます。

![Visual Studio では、Application Insights ボタンはデバッグ時に表示されます。](./media/app-insights-asp-net/appinsights-09eventcount.png)

このボタンをクリックして診断検索を開きます。


### 診断検索

[検索] ウィンドウには、Application Insights ポータルに送信されたイベントが示されます。(ポータルにも同等の検索機能があります。)

![プロジェクトを右クリックし、[Application Insights]、[検索] を選択する](./media/app-insights-asp-net/34.png)

フリー テキスト検索は、イベント内の任意のフィールドに使用できます。たとえば、ページの URL の一部や、クライアントの市区町村などのプロパティ値、トレース ログの特定の単語などを検索できます。


[検索の詳細についてはこちらを参照してください。](app-insights-diagnostic-search.md)

### 例外

[例外の監視を設定](app-insights-asp-net-exception-mvc.md)している場合は、例外レポートが [検索] ウィンドウに表示されます。

スタック トレースを取得するには、例外をクリックします。Visual Studio でアプリのコードが開かれている場合は、コードの該当する行をスタック トレースからクリックできます。



## <a name="monitor"></a> Application Insights を開く

[Azure ポータル][portal]に Application Insights のリソースを開きます。

![プロジェクトを右クリックして Azure ポータルを開く](./media/app-insights-asp-net/appinsights-04-openPortal.png)

### メトリック: 集計データ

概要グラフでデータを探します。最初、1 つまたは 2 つのポイントだけが表示されます。次に例を示します。

![クリックしてより多くのデータを表示する](./media/app-insights-asp-net/12-first-perf.png)

任意のグラフをクリックして、より詳細なメトリックを表示します。[メトリックの詳細についてはこちらをご覧ください。][perf]

* *ユーザーまたはページのデータがない場合* - [ユーザーとページのデータを追加します](app-insights-asp-net-client.md)。

### 検索: 個々のイベント

個々の要求とそれに関連するイベントを調査するには [検索] を開きます。

![検索ブレードで、ページ名やその他のプロパティを検索します。](./media/app-insights-asp-net/21-search.png)

[検索の詳細についてはこちらを参照してください。](app-insights-diagnostic-search.md)

* *関連するイベントがない場合 - * [サーバー例外](app-insights-asp-net-exception-mvc.md)と[依存関係](app-insights-asp-net-dependencies.md)を設定します。


## データが表示されない場合

* Visual Studio で、アプリがテレメトリを送信していることを確認します。出力ウィンドウと診断ハブにトレースが表示されるはずです。
* Azure で表示が正しいことを確認します。[Azure ポータル](https://portal.azure.com)にサインインし、[参照]、[Application Insights] の順にクリックしてからアプリケーションを選択します。
* アプリケーションを使用して、テレメトリがいくつか生成されるようにさまざまなページを開きます。
* [[検索]][diagnostic] ブレードを開き、個々のイベントを表示します。メトリック パイプラインを経由すると、イベントの表示に少し時間がかかる場合があります。
* 数秒待機してから [最新の情報に更新] をクリックします。
* [トラブルシューティング][qna]に関するページを参照します。



## アプリケーションの発行

ここで、アプリケーションをデプロイし、データ累積を確認します。

デバッグ モードで実行している場合、テレメトリはパイプラインにより時間が短縮されるので、数秒でデータが表示されます。アプリケーションをデプロイすると、データ累積速度は遅くなります。

#### ビルド サーバーで問題が発生した場合

[このトラブルシューティング項目](app-insights-troubleshoot-faq.md#NuGetBuild)を参照してください。

> [AZURE.NOTE] (ASP.NET SDK バージョン 2.0.0-beta3 以降を使用している状態で) アプリから大量のテレメトリが生成されると、アダプティブ サンプリング モジュールからイベントの代表的な部分のみが送信され、ポータルに送信されるデータ量が自動的に削減されます。ただし、同じ要求に関連するイベントはグループ単位で選択または選択解除されるので、関連するイベントごとに操作できます。[サンプリングについてはこちらを参照してください](app-insights-sampling.md)。


## テレメトリのデバッグ

### 診断ハブ

Application Insights サーバー テレメトリが生成されると、診断ハブ (Visual Studio 2015 以降) に表示されます。これは、SDK のインストールだけを選択した場合でも、Azure ポータルでリソースに接続しなくても機能します。

![診断ツール ウィンドウを開き、Application Insights のイベントを調べます。](./media/app-insights-asp-net/31.png)

これは、ポータルにテレメトリを送信する前に、いくつかの[カスタム テレメトリ](app-insights-api-custom-events-metrics.md)をデバッグする場合に特に役立ちます。

* *最初に、ポータルにテレメトリを送信する Application Insights を完全に構成しましたが、今は、Visual Studio でテレメトリだけを表示したくなりました。*

    ApplicationInsights.config から行 `<instrumentationkey>...` をコメント アウトします。もう一度ポータルにテレメトリを送信する準備ができたら、コメント解除します。



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

 

<!---HONumber=AcomDC_0128_2016-->