<properties
	pageTitle="Application Insights SDK を追加して ASP.NET アプリを監視する | Microsoft Azure"
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
	ms.topic="get-started-article"
	ms.date="05/19/2016"
	ms.author="awills"/>


# Application Insights SDK を追加して ASP.NET アプリを監視する

*Application Insights はプレビュー段階です。*

[AZURE.INCLUDE [app-insights-selector-get-started](../../includes/app-insights-selector-get-started.md)]


Visual Studio Application Insights は、実行中のアプリケーションを監視し、[パフォーマンスの問題や例外の検出と診断][detect]、[アプリの使用方法の把握][knowUsers]に役立ちます。Application Insights は、さまざまな種類のアプリケーションで使用できます。Azure Web Apps に加えて、独自のオンプレミス IIS サーバーや Azure の仮想マシンでホストされているアプリに対しても機能します。



![Example performance monitoring charts](./media/app-insights-asp-net-manual/10-perf.png)

*関連項目:*

* [ASP.NET 5](app-insights-asp-net-five.md)
* [デバイス アプリと Java サーバー][platforms]

#### 開始する前に

多くのアプリケーションの種類では、ほとんどの場合に通知されることなく、[Visual Studio によってアプリに Application Insights が追加されます](#ide)。ただし、この記事を読んで状況を把握できるように、ここでは手動による手順について説明します。


必要なもの:

* [Microsoft Azure](http://azure.com) のサブスクリプションチームまたは組織で Azure サブスクリプションを取得している場合、所有者は [Microsoft アカウント](http://live.com)を使用してあなたを追加できます。
* Visual Studio 2013 以降

## <a name="add"></a>Application Insights リソースの作成

[Azure ポータル][portal]にサインインし、Application Insights の新しいリソースを作成します。アプリケーションの種類として ASP.NET を選択します。

![[新規]、[Application Insights] の順にクリックする](./media/app-insights-asp-net-manual/01-new-asp.png)

Azure の[リソース][roles]は、サービスのインスタンスです。このリソースでは、アプリのテレメトリが分析されて画面に表示されます。

アプリケーションの種類を選択すると、[リソース] ブレードの既定のコンテンツと[メトリックス エクスプローラー][metrics]に表示されるプロパティが設定されます。

#### インストルメンテーション キーのコピー

これはリソースを識別するキーです。データをリソースに送信するために SDK の後の手順でインストールします。

![[プロパティ] をクリックし、キーを選択して、Ctrl キーを押しながら C キーを押す](./media/app-insights-asp-net-manual/02-props-asp.png)

新しいリソースを作成するために実行した手順は、任意のアプリケーションの監視を開始するための優れた方法です。これで、データをリソースに送信できます。

## <a name="sdk"></a>アプリケーションに SDK をインストールする

Application Insights SDK のインストールと構成は、作業中のプラットフォームによって異なります。ASP.NET アプリの場合は簡単です。

1. Visual Studio で、Web アプリ プロジェクトの NuGet パッケージを編集します。

    ![プロジェクトを右クリックし、[Nuget パッケージの管理] を選択する](./media/app-insights-asp-net-manual/03-nuget.png)

2. Web Apps 向け Application Insights SDK をインストールします。

    ![「Application Insights」を検索する](./media/app-insights-asp-net-manual/04-ai-nuget.png)

3. (NuGet のインストールによって追加された) ApplicationInsights.config を編集します。次のコードを終了タグの直前に挿入します。

    `<InstrumentationKey>` *コピーしたインストルメンテーション キー* `</InstrumentationKey>`

    (または、アプリ内に[コードを記述してキーを設定][apikey]できます)。

#### 新しいバージョンの SDK にアップグレードするには

SDK の新しいバージョンは不定期でリリースされます。

[SDK の新しいリリース](app-insights-release-notes-dotnet.md)にアップグレードするには、NuGet パッケージ マネージャーをもう一度開き、インストールされているパッケージに対してフィルターを実行します。**[Microsoft.ApplicationInsights.Web]**、**[アップグレード]** の順に選択します。

ApplicationInsights.config をカスタマイズしている場合は、アップグレードする前にコピーを保存しておき、後から新しいバージョンに変更をマージします。


## <a name="run"></a> プロジェクトの実行

**F5** キーを使用してアプリケーションを実行して、試します。さまざまなページを開いて、いくつかのテレメトリを生成します。

Visual Studio で、送信されたイベント数が表示されます。

![](./media/app-insights-asp-net-manual/appinsights-09eventcount.png)

## <a name="monitor"></a> 利用統計情報を表示する

[Azure ポータル][portal]に戻り、Application Insights のリソースを参照します。


概要グラフでデータを探します。最初、1 つまたは 2 つのポイントだけが表示されます。次に例を示します。

![クリックしてより多くのデータを表示する](./media/app-insights-asp-net-manual/12-first-perf.png)

任意のグラフをクリックして、より詳細なメトリックを表示します。[メトリックの詳細についてはこちらをご覧ください。][perf]

#### データが表示されない場合

* アプリケーションを使用して、テレメトリがいくつか生成されるようにさまざまなページを開きます。
* [[検索]][diagnostic] タイルを開き、個々のイベントを表示します。メトリック パイプラインを経由すると、イベントの取得に少し時間がかかる場合があります。
* 数秒待機してから **[最新の情報に更新]** をクリックします。グラフは周期的に自動で更新されますが、データの表示を待機している場合、手動で更新することもできます。
* [トラブルシューティング][qna]に関するページを参照します。

## アプリケーションの発行

ここで、アプリケーションを IIS または Azure にデプロイし、データ累積を確認します。

![Visual Studio を使用してアプリを発行する](./media/app-insights-asp-net-manual/15-publish.png)

デバッグ モードで実行している場合、テレメトリはパイプラインにより時間が短縮されるので、数秒でデータが表示されます。リリース構成でアプリケーションをデプロイすると、データ累積速度は遅くなります。

#### サーバーに発行した後でデータはありませんか。

サーバーのファイアウォールで発信トラフィック用のこれらのポートを開きます。

+ `dc.services.visualstudio.com:443`
+ `f5.services.visualstudio.com:443`


#### ビルド サーバーで問題が発生した場合

[このトラブルシューティング項目](app-insights-asp-net-troubleshoot-no-data.md#NuGetBuild)を参照してください。

> [AZURE.NOTE] \(ASP.NET SDK バージョン 2.0.0-beta3 以降を使用している状態で) アプリから大量のテレメトリが生成されると、アダプティブ サンプリング モジュールからイベントの代表的な部分のみが送信され、ポータルに送信されるデータ量が自動的に削減されます。ただし、同じ要求に関連するイベントはグループ単位で選択または選択解除されるので、関連するイベントごとに操作できます。[サンプリングについてはこちらを参照してください](app-insights-sampling.md)。


## 依存関係の追跡 (および IIS パフォーマンス カウンター) の追加

SDK によるデータへのアクセスでは、若干のサポートが必要です。具体的には、アプリからデータベース、REST API、またその他の外部コンポーネントへの呼び出しを自動的に測定するには、次の追加のステップが必要です。依存関係のメトリックは、パフォーマンスに関する問題の診断に非常に役立つ場合があります。

独自の IIS サーバーで実行している場合、この手順は、[メトリックス エクスプローラー](app-insights-metrics-explorer.md)にシステム パォーマンス カウンターが表示されるようにできます。

#### アプリが IIS サーバーで実行される場合

管理者権限でサーバーにサインインし、[Application Insights Status Monitor](http://go.microsoft.com/fwlink/?LinkId=506648) をインストールします。

[ファイアウォールで追加の送信ポートを開く](app-insights-monitor-performance-live-website-now.md#troubleshooting)ことが必要な場合があります。

以下の手順では、CPU、メモリ、ネットワーク占有率などの[パフォーマンス カウンターのレポート](app-insights-web-monitor-performance.md#system-performance-counters)も有効化します。

#### アプリが Azure の Web アプリの場合

Azure の Web アプリのコントロール パネルで、Application Insights 拡張機能を追加します。

![Web アプリで、[設定]、[拡張機能]、[追加]、[Application Insights] の順に選択する](./media/app-insights-asp-net-manual/05-extend.png)


#### Azure Cloud Services プロジェクトの場合

[スクリプトを Web ロールと worker ロールに追加](app-insights-cloudservices.md)します。



## クライアント側の監視を追加します。

アプリケーションのサーバー側 (バックエンド) からテレメトリ データを送信する SDK を既にインストールしています。このため、クライアント側の監視を追加することができます。これにより、ユーザー、セッション、ページ ビュー、およびブラウザーで発生する例外やクラッシュに関するデータを入手できます。また、独自のコードを記述して、ユーザーのアプリの操作をクリックやキーボード操作までの細部にわたって追跡できます。


すべてのページに JavaScript のスニペットを追加します。コードは次に示す Application Insights のリソースから取得できます。

![Web アプリでクイック スタートを開き、[Web ページを監視するコードを取得する] をクリックする](./media/app-insights-asp-net-manual/02-monitor-web-page.png)

コードにはアプリケーション リソースを識別するインストルメンテーション キーが含まれています。

[Web ページの追跡についてはこちらをご覧ください。](app-insights-web-track-usage.md)


## アプリケーションのバージョンを追跡する

MSBuild プロセスで `buildinfo.config` が生成されていることを確認します。.csproj ファイルに、次のコードを追加します。

```XML

    <PropertyGroup>
      <GenerateBuildInfoConfigFile>true</GenerateBuildInfoConfigFile>    <IncludeServerNameInBuildInfo>true</IncludeServerNameInBuildInfo>
    </PropertyGroup> 
```

ビルド情報がある場合、Application Insights Web モジュールは、**アプリケーションのバージョン**をプロパティとしてテレメトリのすべての項目に自動的に追加します。これにより、[診断の検索][diagnostic]を実行するとき、または[メトリックを調べる][metrics]ときに、バージョンによってフィルター処理できます。

ただし、Visual Studio で開発者向けのビルドではなく、MS ビルドでのみビルド バージョン番号が生成されることに注意してください。

## インストールを完了する

アプリケーションを 360 度から表示するために実行できるいくつかの作業を次に示します。

* [Web テストを設定][availability]して、アプリケーションが動作していて応答できることを確認します。
* お気に入りのログ記録フレームワークから[ログ トレースをキャプチャ][netlogs]します。
* クライアント側とサーバー側 (またはそのいずれか) で[カスタム イベントとメトリックを追跡][api]して、アプリケーションの使用状況の詳細を確認します。

## <a name="ide"></a> 自動化された方法

この記事の冒頭では、Application Insights のリソースの作成と SDK のインストールを手動で実行する方法を示しました。その手順を構成する 2 つの部分を理解しておくことは良いことであると信じています。ただし、ASP.NET アプリ (とその他の多くのアプリ) には、もっとすばやく実行できる自動化された方法があります。

[Visual Studio](http://go.microsoft.com/fwlink/?linkid=397827&clcid=0x409) (2013 Update 3 以上) と、[Microsoft Azure](http://azure.com) のアカウントが必要です。

#### 新しいプロジェクトの場合

Visual Studio で新しいジョブを作成するときは、**[Application Insights の追加]** が選択されていることを確認します。


![ASP.NET プロジェクトを作成する](./media/app-insights-asp-net-manual/appinsights-01-vsnewp1.png)

Visual Studio によって、Application Insights にリソースが作成され、プロジェクトに SDK が追加されて、キーが `.config` ファイルに配置されます。

プロジェクトに Web ページが含まれている場合は、[JavaScript SDK][client] がマスター Web ページに追加されます。

#### 既存のプロジェクトの場合

ソリューション エクスプローラーでプロジェクトを右クリックし、**[Application Insights の追加]** を選択します。

![[Application Insights の追加] を選択する](./media/app-insights-asp-net-manual/appinsights-03-addExisting.png)

Visual Studio によって、Application Insights にリソースが作成され、プロジェクトに SDK が追加されて、キーが `.config` ファイルに配置されます。

この場合、[JavaScript SDK][client] は Web ページに追加されません。この操作は、次の手順として行うことをお勧めします。

#### セットアップ オプション

初めての場合には、Microsoft Azure プレビュー版にログインまたはサインインするように求められます

このアプリがより大きなアプリケーションの一部である場合は、**[設定の構成]** を使用して、他のコンポーネントと同じリソース グループに配置することをお勧めします。

*Application Insights オプションはありせんか。 Visual Studio 2013 Update 3 以降を使用していること、[拡張機能と更新プログラム] で Application Insights Tools が有効になっていることを確認します。*

#### プロジェクトから Application Insights を開く

![プロジェクトを右クリックして Azure ポータルを開く](./media/app-insights-asp-net-manual/appinsights-04-openPortal.png)




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
[platforms]: app-insights-platforms.md
[portal]: http://portal.azure.com/
[qna]: app-insights-troubleshoot-faq.md
[redfield]: app-insights-monitor-performance-live-website-now.md
[roles]: app-insights-resources-roles-access-control.md
[start]: app-insights-overview.md

<!---HONumber=AcomDC_0525_2016-->