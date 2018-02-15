---
title: "Azure のアプリケーションおよびリソースの監視 | Microsoft Docs"
description: "Azure のサービスおよびアプリケーションの包括的な監視戦略に役立つ Microsoft のさまざまなサービスと機能の概要を説明します。"
author: robb
manager: carmonm
editor: 
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: 1b962c74-8d36-4778-b816-a893f738f92d
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/30/2018
ms.author: robb,bwren
ms.openlocfilehash: 3ab7d2d5c3b95d215f3ee9eb9346e8a7895e734c
ms.sourcegitcommit: eeb5daebf10564ec110a4e83874db0fb9f9f8061
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/03/2018
---
# <a name="monitoring-azure-applications-and-resources"></a>Azure のアプリケーションおよびリソースの監視

監視とは、ビジネス アプリケーションやそのアプリが使用するリソースのパフォーマンス、正常性、可用性を見極めるために、データを収集し、分析することを指します。 効果的な監視戦略を策定すれば、アプリケーションの各種コンポーネントの動作状況を詳細に把握できるだけでなく、問題が顕在化する前に対処できるよう、重大な問題を事前に通知させることでアップタイムを向上できます。

Azure には、監視領域における特定のロールやタスクを個別に実行するサービスが豊富に用意されています。また、それらが組み合わされた包括的なソリューションを使用すれば、アプリケーションやそれを下支えする Azure リソースからテレメトリを収集、分析し、それに基づいて対処できます。  また、オンプレミスの重要なリソースを監視して、ハイブリッド監視環境を構築することもできます。   アプリケーションの包括的な監視戦略を策定するための最初のステップは、利用可能なツールとデータの把握です。 

次の図は、各種コンポーネントが連携して Azure リソースの監視機能を提供する概念図を示しています。  以降のセクションで、それぞれを解説し、詳細な技術情報へのリンクを紹介します。

![監視の概要](media/monitoring-overview/overview.png)

## <a name="basic-monitoring"></a>Basic 監視機能
ベーシックな監視機能は、Azure リソースの監視に必要な基本機能です。  これに含まれるサービスは、構成が最小限で済み、高度な監視サービスで使用されるコア テレメトリを収集します。    

### <a name="azure-monitor"></a>Azure Monitor
[Azure Monitor](../monitoring-and-diagnostics/monitoring-overview-azure-monitor.md) は、Azure サービスのベーシックな監視用に、[メトリック](../monitoring-and-diagnostics/monitoring-overview-metrics.md)、[アクティビティ ログ](../monitoring-and-diagnostics/monitoring-overview-activity-logs.md)、[診断ログ](../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md)を収集します。  たとえば、アクティビティ ログでは、新しいリソースが作成または変更されたときに通知するよう設定できます。  メトリックでは、各種リソースはもちろんのこと、仮想マシン内のオペレーティング システムについても、パフォーマンス統計情報を収集できます。  これらのデータは Azure Portal のエクスプローラーを使って表示でき、Log Analytics に送信してトレンド把握や詳細な分析に使用したり、重大な問題を事前に通知するためのアラート ルールを作成したりすることもできます。

### <a name="service-health"></a>サービス正常性
アプリケーションの正常性は、それが使用している Azure サービスの影響を受けます。  [Azure Service Health](../service-health/service-health-overview.md) は、アプリケーションに影響を与える可能性のある Azure サービスに問題が発生すると、それを特定します。この情報を活用して、保守スケジュールを計画できます。

### <a name="azure-advisor"></a>Azure Advisor
[Azure Advisor](../advisor/advisor-overview.md) は、リソースの構成と使用状況に関するテレメトリを常時監視し、ベスト プラクティスに基づいてユーザーの環境に合わせた推奨事項を提示します。  この推奨事項に従って、アプリケーションをサポートするリソースのパフォーマンス、セキュリティ、可用性の向上を図ることができます。


## <a name="premium-monitoring-services"></a>高度な監視サービス
以下の Azure サービスでは、監視データの収集と分析に関する高度な機能が提供されます。  これらのサービスはベーシックな監視機能を基盤として Azure の共通機能を活用します。収集されたデータについて強力な分析を行い、アプリケーションやインフラストラクチャに関する他では得られない洞察を提供します。  さまざまなユーザーを対象とした各種シナリオにおいても、状況に応じたデータを提供します。

### <a name="application-insights"></a>アプリケーション インサイト
[Application Insights](http://azure.microsoft.com/documentation/services/application-insights) では、クラウドにホストされているかオンプレミスかにかかわらず、アプリケーションの可用性、パフォーマンス、使用状況を監視できます。  Application Insights と連携するようにアプリケーションを構成すると、詳細な洞察を取得して、ユーザーからの報告を待たずに、エラーを速やかに特定して診断できます。 収集した情報を活用すれば、アプリケーションのメンテナンスや機能強化に関する選択を十分な情報に基づいて判断することができます。  Application Insights では、収集されたデータを操作するための豊富なツールが用意されているだけでなく、データを共通リポジトリに格納しておき、Log Analytics クエリ言語を使用して、アラート、ダッシュボード、詳細分析などの共有機能を活用することもできます。

### <a name="log-analytics"></a>Log Analytics
[Log Analytics](http://azure.microsoft.com/documentation/services/log-analytics) は、Azure の監視の中心的役割を果たすサービスで、さまざまなリソースから収集したデータを単一のリポジトリに格納し、強力なクエリ言語を使用してデータを分析することができます。  Application Insights と Azure Security Center では、Log Analytics データ ストアにデータを格納し、Log Analytics の分析エンジンを活用しています。  これらのデータと、Azure Monitor、管理ソリューション、クラウドやオンプレミスの仮想マシンにインストールされたエージェントによって収集されたデータを組み合わせることで、環境の全体像を詳細に把握することができます。 


### <a name="service-map"></a>サービス マップ
[Service Map](../operations-management-suite/operations-management-suite-service-map.md) は、他のコンピューターの各種プロセスや依存関係、外部プロセスを使用して仮想マシンを分析し、IaaS 環境に関する洞察を提供します。  Log Analytics 内のイベント、パフォーマンス データ、管理ソリューションが Service Map で統合されるため、データを各コンピューターのコンテキストや環境内の他の要素との関係の中で把握することができます。  Service Map は [Application Insights のアプリケーション マップ](../application-insights/app-insights-app-map.md)に似ていますが、Service Map ではアプリケーションをサポートしているインフラストラクチャ コンポーネントに焦点が当てられています。

### <a name="network-watcher"></a>Network Watcher
[Network Watcher](../network-watcher/network-watcher-monitoring-overview.md) は、Azure の各種ネットワーク シナリオに応じた監視と診断の機能を提供します。  将来の分析に使用できるように Azure のメトリックおよび診断データを格納し、[Log Analytics の管理ソリューション](../log-analytics/log-analytics-azure-networking-analytics.md)と連携して、ネットワーク リソースの包括的な監視を行います。


### <a name="management-solutions"></a>管理ソリューション
[管理ソリューション](../log-analytics/log-analytics-add-solutions.md)は、特定のアプリケーションまたはサービスに関する洞察を提供するロジックを 1 つにまとめたものです。  管理ソリューションは Log Analytics を使用して、収集した監視データの格納と分析を行います。  Azure とサード パーティの各種サービスを監視するさまざまな管理ソリューションが Microsoft とサード パーティから提供されています。 監視ソリューションの例には、コンテナー ホストの把握と管理に役立つ[コンテナー監視ソリューション](../log-analytics/log-analytics-containers.md)や、Azure SQL Database のパフォーマンス メトリックの収集と可視化を行う [Azure SQL Analytics](../log-analytics/log-analytics-azure-sql.md) などがあります。


## <a name="shared-functionality"></a>共有機能
以下の Azure ツールは、高度な監視サービスに重要な機能を提供します。  これらの機能はさまざまなサービスで共有されているため、ユーザーは各種サービスで共通の機能や構成を利用することができます。

### <a name="alerts"></a>アラート
[Azure アラート](../monitoring-and-diagnostics/monitoring-overview-alerts.md)は、重大な状態を事前に通知し、可能であれば修正のためのアクションを実行します。  アラートのルールには、メトリックやログなど各種ソースのデータを使用できます。 また、アラートごとに通知の受信者とアクションをセットにした[アクション グループ](../monitoring-and-diagnostics/monitoring-action-groups.md)も設定できます。  環境の要件に応じて、アラートで webhook を使用して外部アクションを起動したり、ITSM ツールと連携したりすることもできます。

### <a name="dashboards"></a>ダッシュボード
[Azure ダッシュボード](../azure-portal/azure-portal-dashboards.md)を使用すると、さまざまな種類のデータを組み合わせて Azure Portal 内の 1 つのウィンドウに表示し、他の Azure ユーザーと共有することができます。  たとえば、メトリックのグラフ、アクティビティ ログの表、Application Insights の使用状況グラフ、Log Analytics のログ検索の結果を組み合わせて 1 つのダッシュボードを作成できます。

また、Log Analytics データを [Power BI](https://docs.microsoft.com/power-bi/) にエクスポートして、視覚化の向上を図ったり、社内外のユーザーがデータを利用できるようにしたりできます。

### <a name="metrics-explorer"></a>メトリックス エクスプローラー
[メトリック](../monitoring-and-diagnostics/monitoring-overview-metrics.md)は、Azure リソースによって生成される数値で、これを基にしてリソースの動作状況やパフォーマンスを把握できます。 また、メトリックを Log Analytics に送信して、他のソースから収集されたデータと合わせて分析できます。



### <a name="activity-logs"></a>アクティビティ ログ
[アクティビティ ログ](../monitoring-and-diagnostics/monitoring-overview-activity-logs.md)は、Azure リソースの動作状況に関するデータを提供します。  具体的には、リソース構成の変更、サービス正常性インシデント、リソースの有効活用に関する推奨事項、自動スケール操作に関する情報などがあります。  特定のリソースのログは Azure Portal のそのリソースのページで確認できます。また、アクティビティ ログ エクスプローラーでは、複数のリソースのログを表示できます。  アクティビティ ログを Log Analytics に送信すれば、管理ソリューションや仮想マシン上のエージェントなど他のソースで収集されたデータを使って分析できます。


## <a name="example-scenarios"></a>シナリオ例
以降では、さまざまなシナリオで Azure の各種監視ツールを活用する方法を概要的な例を挙げて紹介します。

### <a name="monitoring-a-web-application"></a>Web アプリケーションの監視
Azure にデプロイされ、App Services、Azure Storage、SQL データベースを使用する Web アプリケーションがあるとします。  このアプリケーションを監視するには、まず、使用するリソースごとに Azure Portal の各リソースのページで[メトリック](../monitoring-and-diagnostics/monitoring-overview-metrics.md)と[アクティビティ ログ](../monitoring-and-diagnostics/monitoring-overview-activity-logs.md)にアクセスします。  ここで、アプリケーションの要求数や平均応答時間などの重要情報を確認したり、構成の変更を特定したりできます。

Azure Portal で Azure Monitor にアクセスすると、各種リソースのメトリックとログをまとめて確認できます。  メトリックの標準パラメーターを確認したら、平均応答時間がしきい値を超えた場合などに事前通知する[アラート ルールを作成](../monitoring-and-diagnostics/monitoring-overview-unified-alerts.md)します。  アプリケーションの日々のパフォーマンスをすばやく把握するには、重要な KPI を示すメトリックのグラフが表示される Azure ダッシュボードを作成します。

アプリケーションの詳細な監視を行うには、[Application Insights と連携するようアプリケーションを構成](../application-insights/quick-monitor-portal.md)します。  アプリケーションの動作状況やパフォーマンスに関する詳しい洞察を提供する追加データを収集できるようになります。  Application Insights では、アプリケーションの基盤となるコンポーネント間の関係を検出し、[アプリケーション マップ](../application-insights/app-insights-app-map.md)と[エンドツーエンドのトレース](../application-insights/app-insights-transaction-diagnostics.md)を組み合わせて視覚的に表示します。これを基に、コンポーネント、依存関係、問題発生時の例外を正確に診断できます。  また、[可用性テスト](../application-insights/app-insights-monitor-web-app-availability.md)を作成して、複数のリージョンからアプリケーションを事前にテストします。  開発者をサポートするために、[Profiler を有効に](../application-insights/enable-profiler-compute.md)します。これで、要求や例外を特定のコード業まで絞り込むことができます。  

アプリケーションで使用されているサービスを詳細に把握するには、Log Analytics に追加データを収集する [SQL Analytics ソリューション](../log-analytics/log-analytics-azure-sql.md)を追加します。 しばらく運用した後で、サイトのパフォーマンスがしきい値以下に低下した期間について根本原因を調査することにしたとします。  その場合は、Log Analytics を使用してクエリを作成し、Application Insights で収集された使用状況およびパフォーマンスに関するデータと、アプリケーションをサポートしている Azure Portal リソースの構成およびパフォーマンスに関するデータを関連付けます。


### <a name="monitoring-virtual-machines"></a>仮想マシンの監視
Azure で Windows 仮想マシンと Linux 仮想マシンが混在して実行されているとします。  Azure Monitor を使用して[アクティビティ ログ](../monitoring-and-diagnostics/monitoring-overview-activity-logs.md)と[ホスト レベル メトリック](../monitoring-and-diagnostics/monitoring-overview-metrics.md)を確認し、[Microsoft Azure 診断の拡張機能](../virtual-machines/linux/tutorial-monitoring.md#install-diagnostics-extension)を仮想マシンに追加して、ゲスト オペレーティング システムからメトリックを収集します。  次に、プロセッサ使用率やメモリなどの基本メトリックがしきい値を超えたときに事前通知するための[アラート ルール](../monitoring-and-diagnostics/monitoring-overview-unified-alerts.md)を作成します。

ビジネス アプリケーションを実行している仮想マシンの詳細情報を収集するには、各マシンで [Log Analytics ワークスペースを作成し、VM 拡張機能を有効に](../log-analytics/log-analytics-quick-collect-azurevm.md)します。  アプリケーションに関する[各種データの収集を構成](../log-analytics/log-analytics-data-sources.md)し、日々の運用状況やパフォーマンスをレポートする[ビューを作成](../log-analytics/log-analytics-view-designer.md)します。  また、特定のエラー イベントを受信したときに通知する[アラート ルール](../monitoring-and-diagnostics/monitoring-overview-unified-alerts.md)を作成します。  インストールされているエージェントの正常性を継続的に監視するには、[Agent Health 管理ソリューション](../operations-management-suite/oms-solution-agenthealth.md)を追加します。

アプリケーションに関する詳しい洞察を取得するには、仮想マシンに[依存関係エージェントを追加](../operations-management-suite/operations-management-suite-service-map-configure.md)し、仮想マシンを [Service Map](../operations-management-suite/operations-management-suite-service-map.md) に追加します。  これにより、重要なプロセスが検出され、仮想マシンと他のサービスとの接続が特定されます。  サービスの停止が報告されたときには、Service Map を使ってフォレンジクスを実行し、問題が発生している仮想マシンを特定します。  また、今後問題が発生したときに特定するための[クエリを Log Analytics データに対して](../log-analytics/log-analytics-log-search-new.md)作成し、重大な状態が検出されたときに事前通知するためのアラート ルールを作成します。



## <a name="next-steps"></a>次の手順
項目ごとに詳しい情報を確認できます。

* [Azure Monitor の紹介ビデオ (Ignite 2016 より)](https://myignite.microsoft.com/videos/4977)
* [Azure Monitor の概要](monitoring-get-started.md)
* [Azure 診断](../azure-diagnostics.md) (クラウド サービス、仮想マシン、仮想マシン スケール セット、または Service Fabric アプリケーションの問題を診断する場合)。
* [Application Insights](https://azure.microsoft.com/documentation/services/application-insights/) App Service Web アプリの問題を診断する場合には、こちらをご覧ください。
* [Log Analytics](https://azure.microsoft.com/documentation/services/log-analytics/) 収集した監視データを分析する場合は、こちらをご覧ください。
