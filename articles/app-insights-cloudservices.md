<properties
   pageTitle="Azure Cloud Services 向けの Application Insights"
   description="Application Insights で Web とワーカー ロールを効果的に監視する"
   services="application-insights"
   documentationCenter=""
   authors="soubhagyadash"
   manager="victormu"
   editor="alancameronwills"/>

<tags
   ms.service="application-insights"
   ms.devlang="na"
   ms.tgt_pltfrm="ibiza"
   ms.topic="article"
   ms.workload="tbd"
   ms.date="05/21/2015"
   ms.author="sdash"/>

# Azure Cloud Services 向けの Application Insights


*Application Insights はプレビュー段階です。*

[Microsoft Azure Cloud サービス アプリ](http://azure.microsoft.com/services/cloud-services/)の可用性、パフォーマンス、障害、使用状況を [Visual Studio Application Insights][start] で監視できます。

<!-- For illustration purposes, we have added Application Insights to this [sample Azure cloud service](sample link). This code is available [here](git link), for you to follow along with the steps below. -->

## 役割ごとに Application Insights リソースを作成する

Application Insights リソースでは、利用統計情報データが分析され、表示されます。

1.  [Azure ポータル][portal]で、Application Insights の新しいリソースを作成します。アプリケーションの種類として ASP.NET アプリを選択します。 

    ![[新規]、[Application Insights] の順にクリックする](./media/app-insights-cloudservices/01-new.png)

2.  インストルメンテーション キーをコピーします。これはじきに SDK を構成するために必要になります。

    ![[プロパティ] をクリックし、キーを選択して、Ctrl キーを押しながら C キーを押す](./media/app-insights-cloudservices/02-props.png)


通常、それぞれの Web とワーカー ロールからのデータに対して個別のリソースを作成するのが最良の方法になります。

代替として、すべてのロールからのデータを 1 つだけのリソースに送信できます。ただし、各ロールからの結果をフィルター処理またはグループ化できるように[既定のプロパティ][apidefaults]を設定します。

## <a name="sdk"></a>各プロジェクトに SDK をインストールする


1. Visual Studio で、デスクトップ アプリ プロジェクトの NuGet パッケージを編集します。![プロジェクトを右クリックし、[Nuget パッケージの管理] を選択する](./media/app-insights-cloudservices/03-nuget.png)

2. Web アプリの Application Insights SDK をインストールします。

    ![**[オンライン]**、**[プレリリースを含める]** の順に選択し、"Application Insights" を検索する](./media/app-insights-cloudservices/04-ai-nuget.png)

    (代わりに、Application Insights SDK for Web Apps を選択することもできます。これによって、パフォーマンス カウンター テレメトリがいくつか組み込まれます。)

3. データを Application Insights リソースに送信するように SDK を構成します。

    `ApplicationInsights.config` を開き、この行を挿入します。

    `<InstrumentationKey>` *コピーしたインストルメンテーション キー* `</InstrumentationKey>`

    Application Insights リソースからコピーしたインストルメンテーション キーを使用します。

    代替として、[実行時にキーを設定できます][apidynamicikey]。これにより、さまざまな環境 (開発、テスト、本稼働) からさまざまなリソースに利用統計情報を向けることができます。

## アプリケーションを実行する

開発用コンピューターでデバッグ モードでプロジェクトを実行するか、Azure に公開します。数分間使用し、利用統計情報データを生成します。

## <a name="monitor"></a> 利用統計情報を表示する

[Azure ポータル][portal]に戻り、Application Insights のリソースを参照します。


概要グラフでデータを探します。最初、1 つまたは 2 つのポイントだけが表示されます。次に例を示します。

![クリックしてより多くのデータを表示する](./media/app-insights-asp-net/12-first-perf.png)

任意のグラフをクリックして、より詳細なメトリックを表示します。[メトリックの詳細についてはこちらをご覧ください。][perf]

ここで、アプリケーションを公開し、データが累積される様子を監視します。


#### データが表示されない場合

* [[検索]][diagnostic] タイルを開き、個々のイベントを表示します。
* アプリケーションを使用して、テレメトリがいくつか生成されるようにさまざまなページを開きます。
* 数秒待機してから [最新の情報に更新] をクリックします。
* [トラブルシューティング][qna]に関するページを参照します。


## インストールを完了する

アプリケーションを 360 度から表示するには、いくつかの作業が必要になります。


* [Web ページに JavaScript SDK を追加][client]して、ページ ビュー数、ページの読み込み時間、スクリプトの例外などのブラウザー ベースのテレメトリを取得し、ページ スクリプトにカスタムのテレメトリを記述することができます。
* 依存関係の追跡を追加して、アプリで使用されるデータベースなどのコンポーネントが原因で発生した問題を診断します。
 * [Azure Web アプリまたは VM で][azure]
 * [内部設置型の IIS サーバーで][redfield]
* お気に入りのログ記録フレームワークから[ログ トレースをキャプチャ][netlogs]します。
* クライアント側とサーバー側 (またはそのいずれか) で[カスタム イベントとメトリックを追跡][api]して、アプリケーションの使用状況の詳細を確認します。
* [Web テストを設定][availability]して、アプリケーションが動作していて応答できることを確認します。

#### ワーカー ロールの要求を追跡する

HTTP 要求の場合と同じ方法で追跡することで、ワーカー ロールの呼び出しのパフォーマンスを記録できます。Application Insights では、「要求」型の利用統計情報は、サーバー側で名前を付け、時間を指定し、個別に成功と失敗を判定できる作業単位を測定できます。SDK は HTTP 要求を自動的に記録します。一方で、独自のコードを挿入し、ワーカー ロールへの要求を追跡できます。

次に、ワーカー ロールの一般的な実行ループを示します。

```C#

    while (true)
    {
      Stopwatch s1 = Stopwatch.StartNew();
      var startTime = DateTimeOffset.UtcNow;
      try
      {
        // ... get and process messages ...

        s1.Stop();
        telemetryClient.TrackRequest("CheckItemsTable",
            startTime, s1.Elapsed, SUCCESS_CODE, true);
      }
      catch (Exception ex)
      {
        string err = ex.Message;
        if (ex.InnerException != null)
        {
           err += " Inner Exception: " + ex.InnerException.Message;
        }
        s1.Stop();
        telemetryClient.TrackRequest("CheckItemsTable", 
            startTime, s1.Elapsed, FAILURE_CODE, false);
        telemetryClient.TrackException(ex);

        // Don't flood if we have a bug in queue process loop.
        System.Threading.Thread.Sleep(60 * 1000);
      }
    }
```




[api]: app-insights-api-custom-events-metrics.md
[apidefaults]: app-insights-api-custom-events-metrics.md#default-properties
[apidynamicikey]: app-insights-api-custom-events-metrics.md#dynamic-ikey
[availability]: app-insights-monitor-web-app-availability.md
[azure]: app-insights-azure.md
[client]: app-insights-javascript.md
[diagnostic]: app-insights-diagnostic-search.md
[netlogs]: app-insights-asp-net-trace-logs.md
[perf]: app-insights-web-monitor-performance.md
[portal]: http://portal.azure.com/
[qna]: app-insights-troubleshoot-faq.md
[redfield]: app-insights-monitor-performance-live-website-now.md
[start]: app-insights-get-started.md

<!---HONumber=58-->