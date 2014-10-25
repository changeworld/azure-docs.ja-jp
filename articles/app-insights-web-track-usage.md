<properties title="Track usage with custom events and metrics" pageTitle="Track usage" description="Log user activities." metaKeywords="analytics monitoring application insights" authors="awills" manager="kamrani" />

<tags ms.service="application-insights" ms.workload="tbd" ms.tgt_pltfrm="ibiza" ms.devlang="na" ms.topic="article" ms.date="2014-09-24" ms.author="awills"></tags>

# 利用状況を追跡する

## <a name="webclient"></a>Web 利用状況分析を設定する

まだ実行していない場合には、[ご使用の Web プロジェクトに Application Insights を追加][start]してください。

## <a name="usage"></a>利用状況分析

[アプリケーション概要] ブレードには、以下の利用状況タイルが表示されます。

![](./media/appinsights/appinsights-47usage.png)

### ブラウザーごとのセッション数

*セッション* とは、ユーザーがご使用の Web サイト上のページを開いてから、Web 要求をまったく送信せずに 30 分のタイムアウト期間が終了するまでの期間のことです。

クリックして、グラフを拡大表示します。

### 上位ページ ビュー

過去 24 時間の合計数を示します。

[ページ ビュー] タイルをクリックして、詳細履歴を取得します。

![](./media/appinsights/appinsights-49usage.png)

[時間の範囲] をクリックして、最長で 7 日間の履歴を表示します。

グラフをクリックし、表示可能な他のメトリックを表示します。

![](./media/appinsights/appinsights-63usermetrics.png)

## ページ カウントのカスタマイズ

既定では、ページ カウントは新しいページがクライアント ブラウザーに読み込まれるたびに行われます。ただし、ページ ビューを別の場合にもカウントできます。たとえば、ページの内容がタブに表示され、ユーザーがタブを切り替えるときに対象ページをカウントしたい場合があります。またはページ内の JavaScript コードが、ブラウザーの URL を変更することなく新しいコンテンツを読み込む場合もあります。

次のような JavaScript 呼び出しをクライアント コードの適切な箇所に挿入します。

    appInsights.trackPageView(myPageName);

ページ名には、同じ文字を URL として含めることできますが、「\#」または「?」の後の文字はすべて無視されます。

## 各ページ ビュー イベントを調べる

通常、Application Insights がページ ビューの利用統計情報を分析し、お客様に対して表示されるのは、すべてのユーザーに関して平均した累積レポートのみです。ただし、デバッグのために個別のページ ビュー イベントを調べることもできます。

[診断検索] ブレードで、[フィルター] を [ページ ビュー] に設定します。

![](./media/appinsights/appinsights-51searchpageviews.png)

いずれかのイベントをクリックして、詳細を表示します。

> [WACOM.NOTE] [検索][diagnostic]を使用する場合、単語全体が完全に一致しなければなりません。「Abou」と「bout」は「About」とは一致しませんが、「Abou\*」は一致します。検索語をワイルドカードで始めることもできません。たとえば、「\*bou」を検索しても「About」とは一致しません。

> [診断検索の詳細][diagnostic]

## 利用状況を追跡する

> [AZURE.VIDEO tracking-usage-with-application-insights]

## 詳細情報

-   [Application Insights - 開始する][start]
-   [ライブ Web サーバーを監視する][redfield]
-   [Web アプリケーションのパフォーマンスを監視する][perf]
-   [診断ログを検索する][diagnostic]
-   [Web テストで可用性を追跡する][availability]
-   [利用状況を追跡する][usage]
-   [Q & A およびトラブルシューティング][qna]

<!--Link references-->

  [start]: ../app-insights-start-monitoring-app-health-usage/
  []: ./media/appinsights/appinsights-47usage.png
  [1]: ./media/appinsights/appinsights-49usage.png
  [2]: ./media/appinsights/appinsights-63usermetrics.png
  [3]: ./media/appinsights/appinsights-51searchpageviews.png
  [diagnostic]: ../app-insights-search-diagnostic-logs/
  [redfield]: ../app-insights-monitor-performance-live-website-now/
  [perf]: ../app-insights-web-monitor-performance/
  [availability]: ../app-insights-monitor-web-app-availability/
  [usage]: ../app-insights-web-track-usage/
  [qna]: ../app-insights-troubleshoot-faq/
