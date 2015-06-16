<properties 
	pageTitle="Monitor application performance" 
	description="Chart load and response time, dependency information and set alerts on performance." 
	services="azure-portal" 
	documentationCenter="na" 
	authors="alancameronwills" 
	manager="keboyd"/>
<tags 
	ms.service="azure-portal" 
	ms.workload="na" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="04/28/2015" 
	ms.author="awills"/> 

# アプリケーション パフォーマンスの監視 

[Azure ポータル](http:\\portal.azure.com) では、監視を設定して、Web アプリまたは仮想マシンのアプリケーションの依存関係に関する統計やの詳細情報を収集できます。

Azure は、拡張機能を活用してアプリケーション パフォーマンス監視 (*APM*) をサポートします。これらの拡張機能はアプリケーションにインストールされ、データを収集し、監視サービスにレポートを返します。

##拡張機能を有効にする

1. [参照] をクリックし、インストルメント化する Web アプリまたは仮想マシンを選択します。

2. [監視] の [アプリケーション監視] をクリックします。

3. [Application Insights] や [New Relic] などの、使用する拡張機能プロバイダーを選択します。

![Web app APM](./media/insights-perf-analytics/05-extend.png) 

仮想マシンを使用している場合は、次のように操作します。

![Virtual machine](./media/insights-perf-analytics/10-vm1.png) 

## データを検索する

しばらくの間、アプリケーションを使用していくつかのテレメトリを生成します。

1. 次に、Web アプリまたは仮想マシンのブレードから、インストールされている拡張機能を確認します。
2. アプリケーションを示す行をクリックし、そのプロバイダーに移動します。

![Click Refresh](./media/insights-perf-analytics/06-overview.png) 

[参照] を使用して、使用した Application Insights コンポーネントまたは New Relic アカウントに直接移動することもできます。

Application Insights などのブレードを取得すると、パフォーマンスを開くことができます。

![On the Application Insights overview blade, click the Performance tile](./media/insights-perf-analytics/07-dependency.png) 

- 個別の要求の詳細情報が表示されます。

![In the grid, click a dependency to see related requests.](./media/insights-perf-analytics/08-requests.png) 

- この例は、SQL 呼び出しの数と、平均期間や標準偏差などの関連する統計情報を含む、SQL 依存関係に費やされた時間を示しています。

![](./media/insights-perf-analytics/01-example.png)

### Application Insights の追加要件

New Relic は追加のインストルメンテーションなしで自動的にインストールされますが、Application Insights には 1 つの追加要件があります。


![In the New Project dialog, check Add Application Insights](./media/insights-perf-analytics/03-add.png) 

ログインが要求されたら、Azure アカウントの資格情報を使用します。

##次のステップ

* [サービス正常性のメトリックを監視](insights-how-to-customize-monitoring.md) して、サービスが利用可能で応答可能であることを確認します。

* [監視と診断を有効](insights-how-to-use-diagnostics.md) にして、サービスに関する詳細な頻度の高いメトリックを収集します。

* 操作イベントが発生したり、メトリックがしきい値を超えるたびに、[アラート通知を受け取り](insights-receive-alert-notifications.md) ます。

* [JavaScript のアプリや Web ページの Application Insights](application-insights/app-insights-web-track-usage.md) を使用して、Web ページを参照しているブラウザーに関するクライアント分析を取得します。

* Application Insights で [Web ページの可用性と応答性を監視](application-insights/app-insights-monitor-web-app-availability.md) すると、ページがダウンしているかどうかを検出できます。

<!--HONumber=54-->