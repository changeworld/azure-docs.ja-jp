<properties 
	pageTitle="実行中の Web サイトのパフォーマンスの問題の診断" 
	description="Web サイトを再デプロイせずにそのパフォーマンスを監視します。依存関係のテレメトリを取得するには、スタンドアロンで使用するか、Application Insights SDK と併用します。" 
	services="application-insights" 
    documentationCenter=".net"
	authors="alancameronwills" 
	manager="ronmart"/>

<tags 
	ms.service="application-insights" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="ibiza" 
	ms.devlang="na" 
	ms.topic="get-started-article" 
	ms.date="04/27/2015" 
	ms.author="awills"/>
 

# Application Insights Status Monitor をインストールして Web サイトのパフォーマンスを監視する

*Application Insights はプレビュー段階です。*

Visual Studio Application Insights の Status Monitor があれば、どんな IIS サーバーで実行されている Web アプリケーションであっても、例外やパフォーマンスの問題を診断できます。IIS Web サーバーにインストールするだけで、サーバー内に見つかる ASP.NET Web アプリのインストルメント化や、Application Insights ポータルへデータが送信され、検索と分析が可能になります。管理者アクセス権をお持ちのサーバーであれば、どんな物理サーバーや仮想サーバーにでもインストールできます。

![サンプルのグラフ](./media/app-insights-monitor-performance-live-website-now/10-intro.png)

Application Insights を IIS Web アプリケーションに適用するには、次の 3 つの方法があります。

* **ビルド時:** Web アプリ コードに [Application Insights SDK を追加][greenbrown]します。次の機能が使用可能になります。
 * さまざまな標準診断と使用状況のテレメトリ。 
 * 独自のテレメトリを記述して使用状況追跡や問題の診断に活用するために、[Application Insights API][api] を使用できます。
* **実行時:** Status Monitor を使用して、サーバー上の Web アプリをインストルメント化します。
 * 既に実行されている Web アプリの監視。リビルドや再パブリッシュの必要はありません。
 * さまざまな標準診断と使用状況のテレメトリ。
 * 依存関係の診断 - アプリが、データベース、REST API、その他のサービスなどの他のコンポーネントを使用して発生している障害やパフォーマンスが低下している箇所を特定します。
 * テレメトリに関する問題のトラブルシューティング。
* **両方:** SDK を Web アプリ コードにコンパイルし、Web サーバー上で Status Monitor を実行します。両方の長所を活用できます。
 * 標準診断と使用状況のテレメトリ。
 * 依存関係の診断。
 * API を使用したカスタム テレメトリの記述。
 * SDK とテレメトリに関する問題のトラブルシューティング。



> [AZURE.TIP][Azure App Service の Web アプリ](../app-service-web/websites-learning-map.md)をご利用ですか。 Azure のアプリのコントロール パネルから、[Application Insights SDK を追加][greenbrown]し、[Application Insights の拡張機能を追加](../insights-perf-analytics.md)してください。


## IIS Web サーバーへの Application Insights Status Monitor のインストール

1. [Microsoft Azure](http://azure.com) サブスクリプションが必要です。 

1. IIS Web サーバーで、管理者の資格情報を使用してログインします。
2. [Status Monitor インストーラー](http://go.microsoft.com/fwlink/?LinkId=506648)をダウンロードし、実行します。

4. インストール ウィザードで、Microsoft Azure にサインインします。

    ![Microsoft アカウントの資格情報で Azure にサインインする](./media/app-insights-monitor-performance-live-website-now/appinsights-035-signin.png)

5. 監視するインストール済みの Web アプリケーションまたは Web サイトを選択し、Application Insights ポータルで結果を表示するときに使用するリソースを構成します。

    ![アプリとリソースを選択します。](./media/app-insights-monitor-performance-live-website-now/appinsights-036-configAIC.png)

    通常は、新しいリソースと[リソース グループ][roles]を構成する方法を選択してください。

    既存のリソースを使用するケースとしては、たとえば、サイト用の [Web テスト][availability]や、[Web クライアントの監視][client]を既に設定している場合があります。

6. IIS を再起動します。

    ![ダイアログの上部にある [再起動] を選択します。](./media/app-insights-monitor-performance-live-website-now/appinsights-036-restart.png)

    少しの間、Web サービスが中断されます。

6. 監視対象の Web アプリに ApplicationInsights.config が挿入されます。

    ![Web アプリのコード ファイルの横にある、.config ファイルを見つけます。](./media/app-insights-monitor-performance-live-website-now/appinsights-034-aiconfig.png)

   これ以外にも、web.config にいくつかの変更が加えられます。

### 後で (再) 構成する

ウィザードを完了した後、いつでも必要に応じてエージェントを再構成できます。この方法は、エージェントをインストールした際の初期設定に問題があった場合にも使用できます。

![Click the Application Insights icon on the task bar](./media/app-insights-monitor-performance-live-website-now/appinsights-033-aicRunning.png)

## パフォーマンス テレメトリの表示

[Azure プレビュー ポータル](http://portal.azure.com)にサインインし、Application Insights を参照して、作成したリソースを開きます。

![[参照]、[Application Insights] の順に選択し、アプリを選択します。](./media/app-insights-monitor-performance-live-website-now/appinsights-08openApp.png)

パフォーマンス ブレードを開くと、依存関係とその他のデータが表示されます。

![パフォーマンス](./media/app-insights-monitor-performance-live-website-now/21-perf.png)

任意のグラフをクリックすると、より詳細な情報が表示されます。


![](./media/app-insights-monitor-performance-live-website-now/appinsights-038-dependencies.png)

#### 依存関係

HTTP、SQL、AZUREBLOB というラベルの付いたグラフには、依存関係 (アプリケーションが使用する外部サービス) に対する呼び出しの応答時間と回数が表示されます。



#### パフォーマンス カウンター

パフォーマンス カウンターのグラフをクリックすると表示内容が変わります。新しいグラフを追加することもできます。
 
#### 例外

![サーバーの例外グラフをクリックする](./media/app-insights-monitor-performance-live-website-now/appinsights-039-1exceptions.png)

特定の例外 (過去 7 日間) をドリルダウンし、スタック トレースとコンテキスト データを取得できます。


### テレメトリが見つかりませんか?

  * サイトを使用してデータを生成します。
  * データが到着するまで数分待ち、[最新の情報に更新] をクリックします。
  * 診断検索 ([検索] タイル) を開き、個々のイベントを表示します。グラフに集計データが表示される前であっても、大抵の場合、イベントは診断検索に表示されます。
  * Status Monitor を開き、左ウィンドウ枠でアプリケーションを選択します。「通知の構成」セクションに、このアプリケーションの診断メッセージがあるかどうかを確認します。
  
  ![](./media/app-insights-monitor-performance-live-website-now/appinsights-status-monitor-diagnostics-message.png)

  * サーバーのファイアウォールで、ポート 443 を介した dc.services.visualstudio.com への発信トラフィックが許可されていることを確認します。 
  * サーバーに「権限が不十分」であるという内容のメッセージが表示される場合、次を実行してください。
  * IIS マネージャーで、アプリケーション プールを選択し、[詳細設定] を開きます。[プロセス モデル] に表示されている ID をメモします。
  * コンピューターの管理コントロール パネルで、この ID をパフォーマンス モニター ユーザー グループに追加します。
  * [トラブルシューティング][qna]に関するページを参照します。

## システム要件

サーバー上の Application Insights Status Monitor をサポートする OS:

- Windows Server 2008
- Windows Server 2008 R2
- Windows Server 2012
- Windows Server 2012 R2

最新の SP、.NET Framework 4.0 と 4.5

クライアント側: .NET Framework 4.0 と 4.5 がインストールされた Windows 7、8、8.1

IIS のサポート: IIS 7、7.5、8、8.5 (IIS は必須)。

## <a name="next"></a>次のステップ

* サイトがライブの状態であることを確認するには、[Web テストを作成][availability]します。
* 問題の診断に役立つ情報を得るには、[イベントおよびログを検索][diagnostic]します。
* Web ページ コードからの例外を参照してトレースの呼び出しを挿入するには、[Web クライアント テレメトリを追加][usage]します。
* トレースとログの呼び出しをサーバー コードで挿入するには、[Application Insights SDK を Web サービス コードに追加][greenbrown]します。

## ビデオ

#### パフォーマンスの監視

[AZURE.VIDEO app-insights-performance-monitoring]

<!--Link references-->

[api]: app-insights-api-custom-events-metrics.md
[availability]: app-insights-monitor-web-app-availability.md
[client]: app-insights-javascript.md
[diagnostic]: app-insights-diagnostic-search.md
[greenbrown]: app-insights-start-monitoring-app-health-usage.md
[qna]: app-insights-troubleshoot-faq.md
[roles]: app-insights-resources-roles-access-control.md
[usage]: app-insights-web-track-usage.md

 

<!---HONumber=August15_HO6-->