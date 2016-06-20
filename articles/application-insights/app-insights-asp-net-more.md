<properties 
	pageTitle="Application Insights をさらに活用する" 
	description="Application Insights の利用開始後に使用できる機能の概要を示します。" 
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
	ms.date="06/02/2016" 
	ms.author="awills"/>

# Application Insights からのテレメトリの追加

ここでは、[Visual Studio Application Insights](app-insights-overview.md) の機能の中でもあまり使われていないものについて概要を説明します。既に[使用を開始](app-insights-asp-net.md)していると想定します。Application Insights を使うと、Web アプリケーションの可用性、パフォーマンス、利用状況を監視できます。アプリのパフォーマンスと効果に関するフィードバックが得られたら、各開発ライフサイクルにおける設計の方向性について、情報に基づいて選択できます。

## テレメトリの追加

||
|---|---
|[**可用性テスト**](app-insights-monitor-web-app-availability.md)<br/>世界中から定期的に Web アプリの HTTP 要求を送信します。応答が低速または低い信頼性の場合は、アラートが発行されます。| 
|[**依存関係および AJAX 呼び出し**](app-insights-asp-net-dependencies.md)<br/>SQL クエリを監視し、REST またはその他のリソースへの呼び出しを行います。|
|[**例外**](app-insights-asp-net-exceptions.md)<br/>ハンドル済みおよびハンドルされない例外をカウントし、スタック トレースを取得し、クリックしてコードを表示します。|
|[**Web ページ**](app-insights-javascript.md)<br/>Web ページをインストルメント化して、ページの使用状況とパフォーマンスを監視します。
|**ホスト パフォーマンス: [Azure 診断](app-insights-azure-diagnostics.md)、[Windows パフォーマンス カウンター](app-insights-web-monitor-performance.md)**<br/>CPU 使用率とその他のコンテキストのメトリックを参照してください。 |![](./media/app-insights-asp-net-more/04.png)
|[**SDK API - カスタム テレメトリ**](app-insights-api-custom-events-metrics.md)<br/>サーバーとクライアントの両方のコードで、アプリのパフォーマンスと使用の詳細を表示するための独自のイベントとメトリックスを送信します。|
|[**ログ統合**](app-insights-asp-net-trace-logs.md)<br/>log4Net、NLog、System.Diagnostics.Trace などのログ記録フレームワークを使用している場合、そのトレースを他のテレメトリと共に Application Insights に送信するアダプターがあります。|
|[**TelemetryProcessors**](app-insights-api-filtering-sampling.md)<br/>アプリの SDK から送信されたテレメトリをフィルター処理、変更または強化します。 |


## 強力な分析とプレゼンテーション

||
|---|---
|[**インスタンス データの診断検索**](app-insights-visual-studio.md)<br/>要求、例外、依存関係の呼び出し、ログ トレースおよびページ ビューなどのイベントを検索およびフィルター処理します。Visual Studio では、スタック トレースからコードに移動します。|![Visual studio](./media/app-insights-asp-net/61.png)
|[**集計データのメトリックス エクスプ ローラー**](app-insights-metrics-explorer.md)<br/>要求、失敗、および例外の比率、応答時間、ページの読み込み時間などの集計データを調査、フィルター処理、およびセグメント分割します。|![Visual studio](./media/app-insights-asp-net-more/060.png)
|[**ダッシュボード**](app-insights-dashboards.md#dashboards)<br/>複数のリソースからのデータをマッシュアップし、他のユーザーと共有します。複数コンポーネントのアプリケーションと、チーム ルームでの継続的な表示に最適です。 |![ダッシュボードのサンプル](./media/app-insights-asp-net/62.png)
|[**ライブ ストリーム**](app-insights-metrics-explorer.md#live-stream)<br/>新しいビルドをデプロイする場合、このほぼリアルタイムのパフォーマンス インジケーターを監視し、すべてが期待どおりに動作することを確認します。|![分析のサンプル](./media/app-insights-asp-net-more/050.png)
|[**分析**](app-insights-analytics.md)<br/>この強力なクエリ言語を使用して、アプリのパフォーマンスと使用状況に関する難しい質問に回答します。|![分析のサンプル](./media/app-insights-asp-net-more/010.png)
|[**自動および手動のアラート**](app-insights-alerts.md)<br/>アプリのテレメトリの通常パターンに対して自動アラートを適応し、通常とは異なるパターンがある場合にアラートをトリガします。カスタムまたは標準のメトリックスの特定レベルでアラートを設定することもできます。|![アラートのサンプル](./media/app-insights-asp-net-more/020.png)

## データ管理

|||
|---|---|
|[**連続エクスポート**](app-insights-export-telemetry.md)<br/>独自の方法で分析できるように、すべてのテレメトリをストレージにコピーします。|
|**データ アクセス API**<br/>近日対応予定。|
|[**サンプリング**](app-insights-sampling.md)<br/>データ速度を削減し、価格レベルの制限内で維持できます。|![サンプリング タイル](./media/app-insights-asp-net-more/030.png)

<!---HONumber=AcomDC_0608_2016-->