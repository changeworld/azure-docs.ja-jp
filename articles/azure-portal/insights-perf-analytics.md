<properties 
	pageTitle="Azure Web アプリのパフォーマンスの監視" 
	description="チャートの読み込みおよび応答時間、依存関係の情報やパフォーマンス警告を設定します。" 
	services="azure-portal"
    documentationCenter="na"
	authors="alancameronwills" 
	manager="douge"/>

<tags 
	ms.service="azure-portal" 
	ms.workload="na" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="07/17/2015" 
	ms.author="awills"/>

# Azure Web アプリのパフォーマンスの監視

[Azure ポータル](http://portal.azure.com)を使用すると、[Azure Web アプリ](../app-service-web/app-service-web-overview.md)または [Virtual Machines](../virtual-machines/virtual-machines-about.md) のアプリケーションの依存関係の統計と詳細を収集する監視を設定できます。

Azure は、*拡張機能*を活用してアプリケーション パフォーマンス監視 (*APM*) をサポートします。これらの拡張機能はアプリケーションにインストールされ、データを収集し、監視サービスにレポートを返します。

Application Insights と New Relic は、利用できるパフォーマンス監視拡張機能のうちの 2 つです。New Relic を使用するには、実行時にエージェントをインストールします。Application Insights を使用するには、SDK でコードを再ビルドします。また、追加データにアクセスできる拡張機能もインストールできます。SDK では、アプリの使用状況とパフォーマンスをさらに詳細に監視するコードを記述できます。

## 拡張機能を有効にする

1. **[参照]** をクリックし、インストルメント化する Web アプリまたは仮想マシンを選択します。

2. Application Insights または New Relic の拡張機能を追加します。

    Web アプリをインストルメント化する場合:

![設定、拡張機能、追加、Application Insights](./media/insights-perf-analytics/05-extend.png)

仮想マシンを使用している場合は、次のように操作します。

![分析タイルをクリックする](./media/insights-perf-analytics/10-vm1.png)

### Application Insights 用: SDK を使用して再構築する

Application Insights は、アプリに SDK をインストールすることによって機能します。

Application Insights SDK を Visual Studio のプロジェクトに追加します。

![Web プロジェクトを右クリックし、[Application Insights の追加] を選択する](./media/insights-perf-analytics/03-add.png)

ログインが要求されたら、Azure アカウントの資格情報を使用します。

テレメトリをテストするには、開発用コンピューターでアプリを実行するか、単純に再発行します。

SDK には[カスタム テレメトリを記述して](../app-insights-api-custom-events-metrics.md)利用状況を追跡する API があります。

## データを検索する

しばらくの間、アプリケーションを使用していくつかのテレメトリを生成します。

1. 次に、Web アプリまたは仮想マシンのブレードから、インストールされている拡張機能を確認します。
2. アプリケーションを示す行をクリックし、そのプロバイダーに移動します。![[最新の情報に更新] をクリックする](./media/insights-perf-analytics/06-overview.png)

**[参照]** を使用して、使用した Application Insights コンポーネントまたは New Relic アカウントに直接移動することもできます。

Application Insights のブレードが表示されたら、[パフォーマンス] などを開くことができます。

![Application Insights の概要ブレードで、[パフォーマンス] タイルをクリックする](./media/insights-perf-analytics/07-dependency.png)

- ドリル スルーし、個別の要求を表示します。

![グリッドの依存関係をクリックし、関連する要求を参照します。](./media/insights-perf-analytics/08-requests.png)

- この例は、SQL 呼び出しの数と、平均期間や標準偏差などの関連する統計情報を含む、SQL 依存関係に費やされた時間を示しています。 

![](./media/insights-perf-analytics/01-example.png)



## 次のステップ

* [サービスの正常性のメトリックスを監視](insights-how-to-customize-monitoring.md)して、サービスの可用性と応答性を確認します。
* [監視と診断を有効](insights-how-to-use-diagnostics.md)にしてサービスに関する詳細な頻度の高いメトリックを収集します。
* 操作イベントが発生したり、メトリックがしきい値を超えたりするたびに、[アラート通知を受け取り](insights-receive-alert-notifications.md)ます。
* [JavaScript のアプリや Web ページに Application Insights](../app-insights-web-track-usage.md) を使用して、Web ページを参照しているブラウザーに関するクライアント分析を取得します。
* Application Insights で [Web ページの可用性と応答性を監視](../app-insights-monitor-web-app-availability.md)すると、ページがダウンしているかどうかを検出できます。
 

<!---HONumber=July15_HO5-->