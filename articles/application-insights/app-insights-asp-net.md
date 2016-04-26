<properties 
	pageTitle="Application Insights を使用した ASP.NET の Web アプリの分析" 
	description="オンプレミスまたは Azure でホストされている ASP.NET Web サイトのパフォーマンス、可用性、利用状況の分析。" 
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
	ms.date="04/14/2016" 
	ms.author="awills"/>


# ASP.NET 向けの Application Insights の設定


[AZURE.INCLUDE [app-insights-selector-get-started-dotnet](../../includes/app-insights-selector-get-started-dotnet.md)]

Application Insights SDK は実行中の Web アプリケーションから Azure ポータルに分析テレメトリを送信します。Azure ポータルにサインインすると、アプリのパフォーマンスや使用状況をグラフで確認できます。

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

最初に Azure にサインインしないと、SDK がリソースに接続されずにインストールされます。Visual Studio の検索ウィンドウでは、デバッグ時に Application Insights Telemetry を表示および検索することができます。他の手順は後で完了することができます。

## <a name="run"></a> プロジェクトのデバッグ

F5 キーを使用してアプリケーションを実行して、試します。さまざまなページが開き、いくつかのテレメトリが生成されます。

Visual Studio で、ログに記録されたイベント数が表示されます。

![Visual Studio では、Application Insights ボタンはデバッグ時に表示されます。](./media/app-insights-asp-net/appinsights-09eventcount.png)

このボタンをクリックして診断検索を開きます。



## テレメトリのデバッグ

### 診断ハブ

Application Insights サーバー テレメトリが生成されると、診断ハブ (Visual Studio 2015 以降) に表示されます。これは、SDK のインストールだけを選択した場合でも、Azure ポータルでリソースに接続しなくても機能します。

![診断ツール ウィンドウを開き、Application Insights のイベントを調べます。](./media/app-insights-asp-net/31.png)


### 診断検索

[検索] ウィンドウには、ログに記録されたイベントが表示されます (Application Insights を設定する際に Azure にサインインした場合は、ポータルで同じイベントを検索できるようになります)。

![プロジェクトを右クリックし、[Application Insights]、[検索] を選択する](./media/app-insights-asp-net/34.png)

フリー テキスト検索は、イベント内の任意のフィールドに使用できます。たとえば、ページの URL の一部や、クライアントの市区町村などのプロパティ値、トレース ログの特定の単語などを検索できます。

[関連アイテム] タブを開いて、失敗した要求や例外を診断することもできます。


![](./media/app-insights-asp-net/41.png)



### 例外

[例外の監視を設定](app-insights-asp-net-exceptions.md)している場合は、例外レポートが [検索] ウィンドウに表示されます。

スタック トレースを取得するには、例外をクリックします。Visual Studio でアプリのコードが開かれている場合は、コードの該当する行をスタック トレースからクリックできます。




### ローカル監視



Visual Studio 2015 Update 2 以降、Application Insights ポータルにテレメトリを送信するように SDK を構成していない (ApplicationInsights.config にインストルメンテーション キーが存在しない) 場合、診断ウィンドウには、直近のデバッグ セッションからテレメトリが表示されます。

これは以前のバージョンのアプリを既に発行済みである場合に役立ちます。デバッグ セッションから得られたテレメトリが、Application Insights ポータル上の発行済みアプリから得られたテレメトリと混同されるのは望ましくありません。

これは、ポータルにテレメトリを送信する前に、いくつかの[カスタム テレメトリ](app-insights-api-custom-events-metrics.md)をデバッグする場合にも役立ちます。


* *最初に、ポータルにテレメトリを送信するよう Application Insights を完全に構成しましたが、今は、Visual Studio でテレメトリだけを表示したくなりました。*

 * アプリからポータルにテレメトリを送信している場合でも、[検索] ウィンドウの [設定] に用意されているオプションでローカルの診断を検索できます。
 * ポータルへのテレメトリの送信を中止するには、ApplicationInsights.config から `<instrumentationkey>...` 行をコメント アウトしてください。もう一度ポータルにテレメトリを送信する準備ができたら、コメント解除します。



## <a name="monitor"></a> Application Insights ポータルでのテレメトリの表示

アプリケーションの発行後やデバッグ中、テレメトリが正しく送信されているかどうかを確認したいときは、Application Insights ポータルでテレメトリを確認できます。

[Azure ポータル][portal]に Application Insights のリソースを開きます。

![プロジェクトを右クリックして Azure ポータルを開く](./media/app-insights-asp-net/appinsights-04-openPortal.png)

このアプリに Application Ingsights を追加する際に Azure にサインインしなかった場合は、ここでサインインします。**[Application Insights の構成]** を選択します。こうすると、デプロイ後も引き続き実行中のアプリのテレメトリを表示できます。テレメトリは、Application Insights ポータルに表示されます。

### メトリック: 集計データ

概要グラフでデータを探します。最初、1 つまたは 2 つのポイントだけが表示されます。次に例を示します。

![クリックしてより多くのデータを表示する](./media/app-insights-asp-net/12-first-perf.png)

任意のグラフをクリックして、より詳細なメトリックを表示します。[メトリックの詳細についてはこちらをご覧ください。][perf]

* *ユーザーまたはページのデータがない場合* - [ユーザーとページのデータを追加します](app-insights-web-track-usage.md)。

### 検索: 個々のイベント

個々の要求とそれに関連するイベントを調査するには [検索] を開きます。

![検索ブレードで、ページ名やその他のプロパティを検索します。](./media/app-insights-asp-net/21-search.png)

[検索の詳細についてはこちらを参照してください。](app-insights-diagnostic-search.md)

* *関連するイベントがない場合 - * [サーバー例外](app-insights-asp-net-exceptions.md)と[依存関係](app-insights-asp-net-dependencies.md)を設定します。


## データが表示されない場合

* Visual Studio で、アプリがテレメトリを送信していることを確認します。出力ウィンドウと診断ハブにトレースが表示されるはずです。
* Azure で表示が正しいことを確認します。[Azure ポータル](https://portal.azure.com)にサインインし、[参照]、[Application Insights] の順にクリックしてからアプリケーションを選択します。
* アプリケーションを使用して、テレメトリがいくつか生成されるようにさまざまなページを開きます。
* [[検索]][diagnostic] ブレードを開き、個々のイベントを表示します。メトリック パイプラインを経由すると、イベントの表示に少し時間がかかる場合があります。
* 数秒待機してから [最新の情報に更新] をクリックします。
* [トラブルシューティング][qna]に関するページを参照します。



## アプリケーションの発行

ここで、アプリケーションをデプロイし、データ累積を確認します。

### ライブ ストリーム

デプロイの最初の数分でアプリが正しく動作しているかどうかがわかります。古いバージョンを取り替えるときは特に、性能の改善が気になります。問題がある場合、古いバージョンに戻すことがあります。

ライブ ストリームを利用すれば、一連の主要指数がすばやく表示されます。再デプロイや再構成の間に参照できるように設計されています。

![概要ブレードで、[ライブ ストリーム] をクリックします](./media/app-insights-asp-net/45.png)

他の指数グラフとは異なり、ライブ ストリームはほんの数分前からのデータを表示し、データを保持しません。集計パイプラインは最小であり、表示は毎秒更新されます。

ライブ ストリームを利用するには、2.1.0-beta1 以降のバージョンの SDK が必要です。

*ライブ ストリームが構成ページで止まりましたか? - ブラウザーを更新してください (F5)。*

#### ビルド サーバーで問題が発生した場合

[このトラブルシューティング項目](app-insights-asp-net-troubleshoot-no-data.md#NuGetBuild)を参照してください。

> [AZURE.NOTE] \(ASP.NET SDK バージョン 2.0.0-beta3 以降を使用している状態で) アプリから大量のテレメトリが生成されると、アダプティブ サンプリング モジュールからイベントの代表的な部分のみが送信され、ポータルに送信されるデータ量が自動的に削減されます。ただし、同じ要求に関連するイベントはグループ単位で選択または選択解除されるので、関連するイベントごとに操作できます。[サンプリングについてはこちらを参照してください](app-insights-sampling.md)。



## 次のステップ

- [ユーザーとページのデータ](../article/application-insights/app-insights-javascript.md#selector1)
- [Exceptions](../article/application-insights/app-insights-asp-net-exceptions.md#selector1)
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

 

<!---HONumber=AcomDC_0420_2016-->