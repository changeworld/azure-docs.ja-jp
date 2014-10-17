<properties title="Monitor your app's health and usage with Application Insights" pageTitle="Monitor your app's health and usage with Application Insights" description="Get started with Application Insights. Analyze usage, availability and performance of your on-premises or Microsoft Azure applications." metaKeywords="analytics monitoring application insights" authors="awills"  manager="kamrani" />

<tags ms.service="application-insights" ms.workload="tbd" ms.tgt_pltfrm="ibiza" ms.devlang="na" ms.topic="article" ms.date="2014-09-24" ms.author="awills"></tags>

# Web アプリケーションのパフォーマンスを監視する

*Application Insights はプレビュー段階です。*

アプリケーションが正常に実行されていること、および障害がすぐに検出されることを確認します。[Application Insights][Application Insights] はパフォーマンス上の問題や例外を通知し、問題の原因を把握して診断するのに役立ちます。

Application Insights が監視できるのは、オンプレミスのマシンまたは仮想マシンでホストされている ASP.NET Web アプリケーション、および Microsoft Azure Websites です。

-   [パフォーマンス モニターを設定する][パフォーマンス モニターを設定する]
-   [データを表示する][データを表示する]
-   [どのような意味がありますか?][どのような意味がありますか?]
-   [問題を診断する][問題を診断する]
-   [次のステップ][次のステップ]

## <a name="setup"></a>パフォーマンス モニターを設定する

プロジェクトに Application Insights を追加していない場合 (つまり、ApplicationInsights.config がない場合)、以下のいずれかの方法で開始します。

-   [Visual Studio のアプリケーション プロジェクトに Application Insights を追加する][Application Insights] - 推奨されている方法です。パッシブなパフォーマンス モニターに加え、診断ログを挿入したり、利用状況を追跡したりできます。
-   [ライブ Websites のパフォーマンスを監視する][ライブ Websites のパフォーマンスを監視する] - この場合、アプリケーション プロジェクトを更新したり、Web サイトを再デプロイしたりする必要はありません。

## <a name="view"></a>レポートを表示する

F5 を使用してアプリケーションを実行して、試します。別のページが開きます。

Visual Studio で、受け取ったイベント数を確認できます。

![][]

プロジェクトから Application Insights を開きます。

![プロジェクトを右クリックして Azure ポータルを開く][プロジェクトを右クリックして Azure ポータルを開く]

**[アプリケーションの使用状況]** タイル内でデータを探します。最初、1 つまたは 2 つのポイントだけが表示されます。次に例を示します。

![クリックしてより多くのデータを表示する][クリックしてより多くのデータを表示する]

デバッグ モードで実行している場合、利用統計情報はパイプラインにより時間が短縮されるので、数秒でデータが表示されます。アプリケーションをデプロイすると、データ累積速度は遅くなります。

すぐにデータが表示されない場合、1 分ほど待ってから [更新] をクリックします。

### メトリックを確認する

いずれかのタイルをクリックして、詳細を表示したり、より長い期間の結果を表示したりします。たとえば、[要求] タイルをクリックして、次の時間範囲を選択します。

![クリックしてより多くのデータを表示し、時間範囲を選択する][クリックしてより多くのデータを表示し、時間範囲を選択する]

グラフをクリックし、表示するメトリックを選択します。

![グラフをクリックし、メトリックを選択する][グラフをクリックし、メトリックを選択する]

> [AZURE.NOTE] 利用可能なすべての選択項目を表示するには、**メトリックすべてのチェック ボックスをオフにしてください**。メトリックはグループに分けられ、グループ内のあるメンバーが選択されると、そのグループのメンバーのみが表示されます。

## <a name="metrics"></a>どのような意味がありますか?パフォーマンス タイルとレポート

多様なパフォーマンス メトリックを取得できます。まず、アプリケーション ブレードに既定で表示されるメトリックから始めます。

### 要求

指定の期間に受け取った HTTP 要求の数です。これを他のレポートにおける結果と比較して、負荷が変化するとアプリケーションの動作がどのように変わるかを確認します。

HTTP 要求には、ページ、データ、画像に関するすべての GET 要求または POST 要求が含まれます。

タイルをクリックして、特定の URL のカウントを取得します。

### 平均応答時間

アプリケーションに入力した Web 要求と返された応答の間の時間を測定します。

各ポイントは移動平均を示しています。多くの要求がある場合、グラフ内の明確なピークや下落以外に、平均から逸脱している要求が存在することがあります。

異常なピークを探します。通常、要求の数が多くなれば応答時間も長くなることが想定されます。上昇の形が不均衡な場合、アプリケーションがリソース制限 (CPU、および CPU が使用するサービス機能など) に達した可能性があります。

タイルをクリックして、特定の URL の時間を取得します。

![][1]

### 最も遅い要求

![][2]

パフォーマンス チューニングが必要となる可能性がある要求を示します。

### 失敗した要求

![][3]

受信できない例外がスローされた要求の数。

タイルをクリックして、特定の障害の詳細を表示したり、個別の要求を選択してその詳細を表示したりします。

障害の代表的な例だけが、それぞれの検査用に保持されます。

### その他のメトリック

表示できる他のメトリックを確認するには、グラフをクリックし、すべてのメトリックを選択解除してすべてのメトリック セットを表示します。(i) をクリックし、各メトリック定義を表示します。

![すべてのメトリックを選択解除し、全セットを表示する][すべてのメトリックを選択解除し、全セットを表示する]

いずれかのメトリックを選択すると、同じグラフ上に表示できない他のメトリックは非表示になります。

## <a name="diagnosis"></a>問題を診断する

パフォーマンス上の問題を検出して診断するためのいくつかのヒントを以下に記します。

-   [Web テスト][Web テスト]を設定し、Web サイトが停止したり、間違って応答したり、応答速度が低下したりする場合にアラートを送信するようにします。
-   要求の数を他のメトリックと比較し、障害や応答速度の低下が負荷と関連しているかどうかを確認します。
-   コード内に[トレース ステートメントを挿入して、そうしたステートメントを検索する][トレース ステートメントを挿入して、そうしたステートメントを検索する]ことにより、問題の特定に役立てます。

## <a name="next"></a>次のステップ

[Web テスト][Web テスト] - 世界中から定期的に、Web 要求をご使用のアプリケーションに送信します。

[診断トレースを収集して検索する][トレース ステートメントを挿入して、そうしたステートメントを検索する] - トレース呼び出しを挿入し、結果を詳しく調べて問題を特定します。

[利用状況を追跡する][利用状況を追跡する] - ご使用のアプリケーションをどれほどのユーザーが使用しているかを調べます。

[トラブルシューティング][トラブルシューティング] - および Q & A

## 詳細情報

-   [Application Insights - 開始する][Application Insights]
-   [ライブ Web サーバーを監視する][ライブ Websites のパフォーマンスを監視する]
-   [Web アプリケーションのパフォーマンスを監視する][Web アプリケーションのパフォーマンスを監視する]
-   [診断ログを検索する][トレース ステートメントを挿入して、そうしたステートメントを検索する]
-   [Web テストで可用性を追跡する][Web テスト]
-   [利用状況を追跡する][利用状況を追跡する]
-   [Q & A およびトラブルシューティング][トラブルシューティング]

<!--Link references-->

  [Application Insights]: ../app-insights-start-monitoring-app-health-usage/
  [パフォーマンス モニターを設定する]: #setup
  [データを表示する]: #view
  [どのような意味がありますか?]: #metrics
  [問題を診断する]: #diagnosis
  [次のステップ]: #next
  [ライブ Websites のパフォーマンスを監視する]: ../app-insights-monitor-performance-live-website-now/
  []: ./media/appinsights/appinsights-09eventcount.png
  [プロジェクトを右クリックして Azure ポータルを開く]: ./media/appinsights/appinsights-04-openPortal.png
  [クリックしてより多くのデータを表示する]: ./media/appinsights/appinsights-41firstHealth.png
  [クリックしてより多くのデータを表示し、時間範囲を選択する]: ./media/appinsights/appinsights-48metrics.png
  [グラフをクリックし、メトリックを選択する]: ./media/appinsights/appinsights-61perfchoices.png
  [1]: ./media/appinsights/appinsights-42reqs.png
  [2]: ./media/appinsights/appinsights-44slowest.png
  [3]: ./media/appinsights/appinsights-46failed.png
  [すべてのメトリックを選択解除し、全セットを表示する]: ./media/appinsights/appinsights-62allchoices.png
  [Web テスト]: ../app-insights-monitor-web-app-availability/
  [トレース ステートメントを挿入して、そうしたステートメントを検索する]: ../app-insights-search-diagnostic-logs/
  [利用状況を追跡する]: ../app-insights-web-track-usage/
  [トラブルシューティング]: ../app-insights-troubleshoot-faq/
  [Web アプリケーションのパフォーマンスを監視する]: ../app-insights-web-monitor-performance/
