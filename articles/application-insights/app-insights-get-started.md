<properties 
	pageTitle="Application Insights を使ってみる" 
	description="内部設置型または Microsoft Azure Web アプリケーションの使用状況、可用性、パフォーマンスを Application Insights で分析します。" 
	services="application-insights" 
documentationCenter=""
	authors="alancameronwills" 
	manager="kamrani"/>

<tags 
	ms.service="application-insights" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="ibiza" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="02/10/2015" 
	ms.author="awills"/>

# Application Insights - アプリケーションの状態と利用状況の監視を開始する

*Application Insights はプレビュー段階です。*

Application Insights を使用すると、ライブ アプリケーションの以下の事柄について監視できます。

* **可用性** - 世界中から数分ごとにご使用の URL についてテストします。
* **パフォーマンス** - パフォーマンスに関する問題と例外を検出して診断します。
* **利用状況** - ご使用のアプリケーションを使っているユーザーの実行内容を把握し、それらのユーザーにとってより良いアプリケーションとなるようにします。

構成は非常に簡単で、数分で結果を確認できます。現在、(サーバーまたは Azure 上の) ASP.NET Web アプリケーションをサポートしています。


## 開始方法

任意の順序でこの表の左側に示すエントリ ポイントを組み合わせて開始します。適切なルートを選択します。ASP.NET Web アプリケーションを開発する場合は、Web プロジェクトに Application Insights を追加することによって開始できます。その他の機能は後で簡単に追加できます。

[Microsoft Azure](http://azure.com) のアカウントが必要です (VSO バージョンを使用する場合を除く)。

<table >
<tr valign="top"><th>必要な項目</th><th colspan="2">必要な操作</th><th>取得する項目</th></tr>
<tr valign="top"><td>my ASP.NET アプリケーションのパフォーマンスと利用状況分析を取得する</td><td colspan="2"><a href="../app-insights-start-monitoring-app-health-usage/">Application Insights を Web プロジェクトに追加する</a></td><td>パフォーマンス メトリック: 読み込み数、応答時間など</td></tr>
<tr valign="top"><td></td><td></td><td><a href="../app-insights-web-track-usage-custom-events-metrics/">サーバー コードからのイベントとメトリックを送信する</a></td><td>カスタム ビジネス分析</td></tr>
<tr valign="top"><td></td><td></td><td><a href="../app-insights-search-diagnostic-logs/">トレースおよび例外の利用統計情報をサーバーから送信する、またはサード パーティのログ データをキャプチャする</td><td>サーバー アプリケーションの診断。ログ データを検索、フィルターする</a></td></tr>
<tr valign="top"><td>自分の Web ページの利用状況を取得する (任意のプラットフォーム) </td><td colspan="2"><a href="../app-insights-web-track-usage/">AI スクリプトをユーザーの Web ページに挿入する</a></td><td>利用状況分析: ページ ビュー、再訪ユーザー、セッション数</td></tr>
<tr valign="top"><td></td><td>&nbsp;&nbsp;</td><td><a href="../app-insights-web-track-usage-custom-events-metrics/">Web ページのスクリプトにイベントとメトリックの呼び出しを記述する</a></td><td>カスタムのユーザー エクスペリエンスの分析</td></tr>
<tr valign="top"><td></td><td></td><td><a href="../app-insights-search-diagnostic-logs/">Web ページのスクリプトにトレースと診断の呼び出しを記述する</a></td><td>ログ データを検索、フィルターする</td></tr>
<tr valign="top"><td>既に自分の Web サーバーで実行中の ASP.NET アプリケーションの問題を診断する</td><td colspan="2"><a href="../app-insights-monitor-performance-live-website-now/">ユーザーの Web サーバーに状態モニターをインストールする</a></td><td>依存関係の呼び出し期間と数、CPU、メモリ、およびネットワーク カウンター (読み込み数、応答時間)</td></tr>
<tr valign="top"><td>Web ページの可用性を監視する</td><td colspan="2"><a href="../app-insights-monitor-web-app-availability/">Application Insights に Web テストを設定する</a></td><td>可用性モニターおよびアラート</td></tr>
<tr valign="top"><td>Windows Phone アプリ、Windows ストア アプリ、または Java Web サイトのパフォーマンスおよび利用状況分析を取得する</td><td colspan="2"><a href="http://msdn.microsoft.com/library/dn481095.aspx">現在は、Application Insights の以前の VSO バージョンを使用する</a></td><td>利用状況とパフォーマンス分析<a href="http://msdn.microsoft.com/library/dn793604.aspx">Azure バージョンの機能は、徐々に構築されていきます。</a></td></tr>
</table>


## <a name="video"></a>ビデオ

####  はじめに

> [AZURE.VIDEO application-insights-introduction]

#### 開始方法

> [AZURE.VIDEO getting-started-with-application-insights]




[AZURE.INCLUDE [app-insights-learn-more](../../includes/app-insights-learn-more.md)]



<!--HONumber=46--> 
 
