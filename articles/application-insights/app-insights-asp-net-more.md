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
	ms.date="08/10/2016" 
	ms.author="awills"/>

# Application Insights からのテレメトリの追加

[Application Insights を ASP.NET コードに追加](app-insights-asp-net.md)した後、さらに多くのテレメトリを取得するためにできることがいくつかあります。

## アプリが IIS サーバーで実行されている場合

管理下の IIS サーバーでアプリがホストされている場合は、そのサーバーに Application Insights Status Monitor をインストールしてください。既にインストールされている場合は、何もする必要はありません。

1. 各 IIS Web サーバーで、管理者の資格情報を使用してサインインします。
2. [Status Monitor インストーラー](http://go.microsoft.com/fwlink/?LinkId=506648)をダウンロードし、実行します。
3. インストール ウィザードで、Microsoft Azure にサインインします。

それ以外に何もする必要はありませんが、アプリで監視が有効になっていることを確認できます。

![Extend in Azure](./media/app-insights-asp-net-more/025.png)

(Visual Studio でアプリをインストルメント化していない場合でも、Status Monitor を使用して、[実行時の監視を有効にする](app-insights-monitor-performance-live-website-now.md)こともできます。)

### 取得できるもの

Status Monitor をサーバー コンピューターにインストールすると、いくつかの追加のテレメトリを取得できます。

* .NET 4.5 アプリの依存関係テレメトリ (アプリによって行われた SQL 呼び出しと REST 呼び出し) (.NET の以降のバージョンでは、依存関係テレメトリに Status Monitor は必要ありません)。
* 例外スタック トレースで、詳細情報が表示されます。
* パフォーマンス カウンター。Application Insights では、これらのカウンターは、[サーバー] ブレードに表示されます。

![Extend in Azure](./media/app-insights-asp-net-more/070.png)

表示されるカウンターを増減するには、[グラフを編集](app-insights-metrics-explorer.md)します。必要なパフォーマンス カウンターが使用可能なセットにない場合は、[パフォーマンス カウンター モジュールによって収集されるセットに追加](app-insights-web-monitor-performance.md#system-performance-counters)できます。

## Azure Web アプリの場合

アプリが Azure Web アプリとして実行される場合は、アプリまたは VM の Azure コントロール パネルに移動し、Application Insights 拡張機能を追加します。**[ツール]** で **[パフォーマンスの監視]** を開き、**[Application Insights]** を構成します。メッセージが表示されたら、既に作成した Application Insights リソースを選択します。

![Extend in Azure](./media/app-insights-asp-net-more/05-extend.png)

### 取得できるもの

* 例外スタック トレースで、詳細情報が表示されます。
* .NET 4.5 アプリの依存関係テレメトリ (アプリによって行われた SQL 呼び出しと REST 呼び出し) (.NET の以降のバージョンでは、依存関係テレメトリに拡張機能は必要ありません)。

![Extend in Azure](./media/app-insights-asp-net-more/080.png)

(Visual Studio でアプリをインストルメント化していない場合でも、この方法を使用して、[実行時のパフォーマンス監視を有効にする](app-insights-monitor-performance-live-website-now.md)こともできます。)


## ブラウザーのパフォーマンス

クライアントのブラウザーからテレメトリを取得するには、Web ページに Application Insights の JavaScript スニペットを追加します。

1. Azure で、アプリの Application Insights リソースを開きます。
2. [クイック スタート]、[Monitor Client Side (クライアント側の監視)] の順に開き、スニペットをコピーします。
3. コピーしたスニペットを、各 Web ページの先頭に表示されるように貼り付けます。通常、これを行うには、マスター レイアウト ページに貼り付けます。

![Extend in Azure](./media/app-insights-asp-net-more/100.png)

### 取得できるもの

* ボタンのクリックの追跡など、[Web ページからカスタム テレメトリ](app-insights-api-custom-events-metrics.md)を送信する JavaScript を記述できます。
* [Analytics](app-insights-analytics.md) では、`pageViews` のデータと `dependencies` の AJAX データ。
* [ブラウザー] ブレードの[クライアント パフォーマンスと使用状況データ](app-insights-javascript.md)。

![Extend in Azure](./media/app-insights-asp-net-more/090.png)

## 可用性 Web テスト

世界中から定期的に Web アプリの HTTP 要求を送信してください。応答が低速または低い信頼性の場合は、アラートが発行されます。

アプリの Application Insights リソースで Web テストを追加、編集、および表示するには、[可用性] タイルをクリックします。

複数の場所で実行される複数のテストを追加することができます。

![Extend in Azure](./media/app-insights-asp-net-more/110.png)

[詳細情報](app-insights-monitor-web-app-availability.md)

## カスタム テレメトリとログ記録

コードに追加した Application Insights パッケージには、アプリケーションから呼び出すことができる API が用意されています。

* [独自のイベントとメトリックを生成](app-insights-api-custom-events-metrics.md)し、ビジネス イベントのカウントやパフォーマンスの監視などを実行します。
* Log4Net、NLog、または System.Diagnostics.Trace から[ログ トレースをキャプチャします](app-insights-asp-net-trace-logs.md)。
* テレメトリ プロセッサを記述して、アプリから送信された標準テレメトリを[フィルター、変更、または強化します](app-insights-api-filtering-sampling.md)。


## 強力な分析とプレゼンテーション

データを探索する方法は多数あります。Application Insights を最近使い始めた場合は、以下の記事を参照してください。

||
|---|---
|[**インスタンス データの診断検索**](app-insights-visual-studio.md)<br/>要求、例外、依存関係の呼び出し、ログ トレースおよびページ ビューなどのイベントを検索およびフィルター処理します。Visual Studio では、スタック トレースからコードに移動します。|![Visual studio](./media/app-insights-asp-net/61.png)
|[**集計データのメトリックス エクスプローラー**](app-insights-metrics-explorer.md)<br/>要求、失敗、および例外の比率、応答時間、ページの読み込み時間などの集計データを調査、フィルター処理、およびセグメント分割します。|![Visual studio](./media/app-insights-asp-net-more/060.png)
|[**ダッシュボード**](app-insights-dashboards.md#dashboards)<br/>複数のリソースからのデータをマッシュアップし、他のユーザーと共有します。複数コンポーネントのアプリケーションと、チーム ルームでの継続的な表示に最適です。 |![ダッシュボードのサンプル](./media/app-insights-asp-net/62.png)
|[**ライブ メトリックス ストリーム**](app-insights-metrics-explorer.md#live-metrics-stream)<br/>新しいビルドをデプロイする場合、このほぼリアルタイムのパフォーマンス インジケーターを監視し、すべてが期待どおりに動作することを確認します。|![分析のサンプル](./media/app-insights-asp-net-more/050.png)
|[**Analytics**](app-insights-analytics.md)<br/>この強力なクエリ言語を使用して、アプリのパフォーマンスと使用状況に関する難しい質問に回答します。|![分析のサンプル](./media/app-insights-asp-net-more/010.png)
|[**自動および手動のアラート**](app-insights-alerts.md)<br/>アプリのテレメトリの通常パターンに対して自動アラートを適応し、通常とは異なるパターンがある場合にアラートをトリガーします。カスタムまたは標準のメトリックスの特定レベルでアラートを設定することもできます。|![アラートのサンプル](./media/app-insights-asp-net-more/020.png)

## データ管理

|||
|---|---|
|[**連続エクスポート**](app-insights-export-telemetry.md)<br/>独自の方法で分析できるように、すべてのテレメトリをストレージにコピーします。|
|**データ アクセス API**<br/>近日対応予定。|
|[**サンプリング**](app-insights-sampling.md)<br/>データ速度を削減し、価格レベルの制限内で維持できます。|![サンプリング タイル](./media/app-insights-asp-net-more/030.png)

<!---HONumber=AcomDC_0810_2016-->