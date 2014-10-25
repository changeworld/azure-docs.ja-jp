<properties title="Explore your metrics" pageTitle="Explore your metrics" description="Analyze usage, availability and performance of your on-premises or Microsoft Azure web application with Application Insights." metaKeywords="analytics monitoring application insights" authors="awills"  />

<tags ms.service="application-insights" ms.workload="tbd" ms.tgt_pltfrm="ibiza" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="awills"></tags>

# メトリックの探索

[Application Insights 用の Web プロジェクトの設定][Application Insights 用の Web プロジェクトの設定]がまだ済んでいない場合は今すぐ設定してください。

次に示すのは、Application Insights のアプリケーションのブレードに表示されるレポートです。Visual Studio からこのページを表示するには、Web プロジェクトを右クリックし、[Application Insights を開く] を選択します。Microsoft Azure プレビューのスタート ボードからこのページを表示するには、[参照]、[Application Insights]、プロジェクトの順に選択します。

-   [アプリケーションの健全性][アプリケーションの健全性]
-   [利用状況分析][利用状況分析]
-   [次のステップ][次のステップ]

## <a name="health"></a>アプリケーションの健全性

アプリケーションの健全性は、アプリケーションのコードをインストルメント化することによって監視できます。

![][]

### 平均応答時間

アプリケーションが Web 要求を受け取ってから応答を返すまでの時間を測定します。

ポイントは、移動平均を示します。大量の要求があった場合には、グラフに顕著なピークや下落が見られなくても、平均から逸脱するデータが存在する可能性があります。

異常なピークを探してください。一般的には、要求数が増えるに応じて応答時間も上昇します。上昇率がつりあわない場合、CPU や使用しているサービスの容量などのリソース制限にアプリケーションが達している可能性があります。

### 要求数

指定された期間内に受け取った要求の数。この値を他のグラフの結果と比較して、負荷が変化したときのアプリケーションの動作を調べます。

### Web テスト

![][1]

[[Web テスト]](../app-insights-monitor-web-app-availability/) には、世界各地の Application Insights サーバーから対象のサーバーに規則的な間隔で送信された Web 要求の結果が示されます。

要求カウントと共に結果が変化しているかどうかを確認します。

[Web テストを設定する方法](../app-insights-monitor-web-app-availability/)

### 最も低速な要求

![][2]

パフォーマンスの調整が必要と考えられる要求が表示されます。

### 診断検索

![][3]

[診断ログを設定][診断ログを設定]している場合は、クリックして最新のイベントを表示できます。

### 失敗した要求の数

![][4]

キャッチされない例外をスローした要求の数。

## <a name="usage"></a>利用状況分析

![][5]

利用状況データは、サーバーと [Web ページのスクリプト][Application Insights 用の Web プロジェクトの設定]から取得されます。

### ブラウザーあたりのセッション数

*セッション*とは、ユーザーが Web サイトのいずれかのページを開いたときに開始され、ユーザーが Web 要求を送信することなく 30 分のタイムアウト期間が経過した後に終了する期間です。

クリックすると、グラフが拡大されます。

### トップ ページ ビュー

過去 24 時間の合計数が表示されます。

クリックすると、この 1 週間のページ ビューのグラフが表示されます。

### タイルの並べ替え

![Choose settings, customize][Choose settings, customize]

## <a name="next"></a>次のステップ

[Web テストの設定](../app-insights-monitor-web-app-availability/)

[診断ログのキャプチャと検索][診断ログを設定]

[トラブルシューティング][トラブルシューティング]

## 詳細情報

-   [Application Insights - 概要][Application Insights - 概要]
-   [アプリケーションの正常性と利用状況の監視][Application Insights 用の Web プロジェクトの設定]
-   [ライブ Web サーバーの監視を今すぐ始める][ライブ Web サーバーの監視を今すぐ始める]
-   [メトリックの探索][メトリックの探索]
-   [Application Insights での診断ログの検索][診断ログを設定]
-   [Web アプリケーションの可用性と応答性の監視](../app-insights-monitor-web-app-availability/)
-   [カスタム イベントおよびメトリックを使用した利用状況の追跡][カスタム イベントおよびメトリックを使用した利用状況の追跡]
-   [トラブルシューティングおよび Q & A - Microsoft Azure プレビューの Application Insights][トラブルシューティング]

<!--Link references-->

  [Application Insights 用の Web プロジェクトの設定]: ../app-insights-monitor-application-health-usage/
  [アプリケーションの健全性]: #health
  [利用状況分析]: #usage
  [次のステップ]: #next
  []: ./media/appinsights/appinsights-42reqs.png
  [1]: ./media/appinsights/appinsights-43webtests.png
  [2]: ./media/appinsights/appinsights-44slowest.png
  [3]: ./media/appinsights/appinsights-45diagnostic.png
  [診断ログを設定]: ../app-insights-search-diagnostic-logs/
  [4]: ./media/appinsights/appinsights-46failed.png
  [5]: ./media/appinsights/appinsights-47usage.png
  [Choose settings, customize]: ./media/appinsights/appinsights-21-customizeblade.png
  [トラブルシューティング]: ../app-insights-troubleshoot-faq/
  [Application Insights - 概要]: ../app-insights-get-started/
  [ライブ Web サーバーの監視を今すぐ始める]: ../app-insights-monitor-performance-live-website-now/
  [メトリックの探索]: ../app-insights-explore-metrics/
  [カスタム イベントおよびメトリックを使用した利用状況の追跡]: ../app-insights-track-usage-custom-events-metrics/
