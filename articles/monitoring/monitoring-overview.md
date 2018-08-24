---
title: Azure のアプリケーションおよびリソースの監視 | Microsoft Docs
description: Azure のサービスおよびアプリケーションの包括的な監視戦略に役立つ Microsoft のサービスと機能の概要。
author: bwren
editor: ''
services: azure-monitor
ms.assetid: 1b962c74-8d36-4778-b816-a893f738f92d
ms.service: azure-monitor
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/10/2018
ms.author: bwren
ms.openlocfilehash: 878765a1f84e0825e86def2b59310732b22aaacc
ms.sourcegitcommit: 744747d828e1ab937b0d6df358127fcf6965f8c8
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/16/2018
ms.locfileid: "42139942"
---
# <a name="monitoring-azure-applications-and-resources"></a>Azure のアプリケーションおよびリソースの監視

監視とは、ビジネス アプリケーションやそのアプリが使用するリソースのパフォーマンス、正常性、可用性を見極めるために、データを収集し、分析することを指します。 効果的な監視戦略を策定することで、アプリケーションのコンポーネントの動作状況を詳細に把握できます。 また、問題が顕在化する前に解決できるように重大な問題を事前に通知して、アップタイムを向上させることも可能です。

Azure には、監視領域において特定の役割やタスクを個別に実行するサービスが複数用意されています。 また、これらのサービスが組み合わされた包括的なソリューションを使用すれば、アプリケーションやそれらのサービスを支える Azure リソースからテレメトリを収集、分析し、それに基づいて対処できます。 また、オンプレミスの重要なリソースを監視して、ハイブリッド監視環境を構築することもできます。 アプリケーションの包括的な監視戦略を策定するための最初のステップは、利用可能なツールとデータの把握です。 

次の図は、コンポーネントが連携して Azure リソースの監視機能を提供する概念図を示しています。 以降のセクションでは、これらのコンポーネントについて解説し、詳細な技術情報へのリンクを紹介します。

![監視の概要](media/monitoring-overview/monitoring-products-overview.png)


## <a name="shared-capabilities"></a>共有機能
コアおよび詳細な監視サービスは、次の機能を提供する機能を共有します。 

### <a name="alerts"></a>アラート
[Azure アラート](../monitoring-and-diagnostics/monitoring-overview-alerts.md)は、重大な状態を事前に通知し、可能であれば修正のためのアクションを実行します。 アラートのルールには、メトリックやログなど各種ソースのデータを活用できます。 また、アラートごとに通知の受信者とアクションをセットにした[アクション グループ](../monitoring-and-diagnostics/monitoring-action-groups.md)も設定できます。 環境の要件に応じて、アラートで webhook を使用して外部アクションを開始したり、ITSM ツールと連携したりすることもできます。

### <a name="dashboards"></a>ダッシュボード
[Azure ダッシュボード](../azure-portal/azure-portal-dashboards.md)を使用すると、さまざまな種類のデータを組み合わせて、[Azure Portal](https://portal.azure.com) 内の 1 つのウィンドウに表示できます。 ダッシュボードは、他の Azure ユーザーと共有することができます。 

たとえば、以下を組み合わせてダッシュボードを作成できます。
- メトリックのグラフが表示されているタイル
- アクティビティ ログの表
- Application Insights の使用状況グラフ
- Log Analytics のログ検索の結果

また、Log Analytics データを [Power BI](https://docs.microsoft.com/power-bi/) にエクスポートすることもできます。 そこで、追加の視覚化を利用できます。 また、組織内外のユーザーがデータを利用できるように設定することもできます。

### <a name="metrics-explorer"></a>メトリックス エクスプローラー
[メトリック](../monitoring-and-diagnostics/monitoring-overview-metrics.md)は、Azure リソースによって生成される数値で、これを基にしてリソースの動作状況やパフォーマンスを把握できます。 メトリックス エクスプローラーを使用すると、メトリックを Log Analytics に送信して、他のソースから収集されたデータと合わせて分析できます。


## <a name="core-monitoring"></a>コアな監視
コアな監視機能は、Azure リソースの監視に必要な基本機能です。 これに含まれるサービスは、構成が最小限で済み、高度な監視サービスで使用されるコア テレメトリを収集します。    

### <a name="azure-monitor"></a>Azure Monitor
[Azure Monitor](../monitoring-and-diagnostics/monitoring-overview-azure-monitor.md) は、Azure サービスのコアな監視用に、[メトリック](../monitoring-and-diagnostics/monitoring-overview-metrics.md)、[アクティビティ ログ](../monitoring-and-diagnostics/monitoring-overview-activity-logs.md)、および[診断ログ](../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md)を収集します。 たとえば、アクティビティ ログでは、新しいリソースが作成または変更されたときに通知するよう設定できます。 

メトリックでは、各種リソースはもちろんのこと、仮想マシン内のオペレーティング システムについても、パフォーマンス統計情報を収集できます。 Azure Portal で、いずれかのエクスプローラーを使用してこのデータを表示し、これらのメトリックに基づいてアラートを作成することができます。 Azure Monitor では非常に高速なメトリックのパイプライン (5 分から 1 分) が提供されるため、タイム クリティカルなアラートと通知に使用してください。 

これらのメトリックとログを Azure Log Analytics に送信してトレンド把握や詳細な分析に使用したり、追加のアラート ルールを作成し、分析結果として重大な問題について事前に通知を受けたりすることもできます。  

### <a name="azure-advisor"></a>Azure Advisor
[Azure Advisor](../advisor/advisor-overview.md) では、リソース構成と使用状況に関するテレメトリを常に監視します。 その上で、ベスト プラクティスに基づいてユーザーの環境に合わせた推奨事項を提示します。 この推奨事項に従って、アプリケーションをサポートするリソースのパフォーマンス、セキュリティ、可用性の向上を図ることができます。

### <a name="service-health"></a>サービス正常性
アプリケーションの正常性は、それが使用している Azure サービスの影響を受けます。 [Azure Service Health](../service-health/service-health-overview.md) は、Azure サービスでアプリケーションに影響を与える可能性のある問題が発生すると、それを特定します。 Service Health は、予定メンテナンスの計画にも役立ちます。

### <a name="activity-log"></a>アクティビティ ログ
[アクティビティ ログ](../monitoring-and-diagnostics/monitoring-overview-activity-logs.md)は、Azure リソースの動作状況に関するデータを提供します。 次の情報が表示されます。
- リソースの構成変更。
- サービス正常性インシデント。
- リソースの有効活用に関する推奨事項。
- 自動スケール操作に関する情報。 

特定のリソースのログは Azure Portal のそのリソースのページで確認できます。 また、アクティビティ ログ エクスプローラーでは、複数のリソースのログを表示できます。 

アクティビティ ログ エントリを Log Analytics に送信することもできます。 そこで、管理ソリューション、仮想マシン上のエージェント、および他のソースで収集されたデータを使って、ログを分析できます。

## <a name="deep-monitoring-services"></a>詳細な監視サービス
以下の Azure サービスでは、監視データの収集と分析に関する高度な機能が詳細レベルで提供されます。 これらのサービスはコアな監視機能を基盤として Azure の共通機能を活用します。 収集されたデータについて強力な分析を行い、アプリケーションやインフラストラクチャに関する他では得られない洞察を提供します。 さまざまなユーザーを対象としたシナリオにおいても、状況に応じたデータを提供します。

### <a name="deep-application-monitoring"></a>詳細なアプリケーション監視
#### <a name="application-insights"></a>Application Insights
[Azure Application Insights](http://azure.microsoft.com/documentation/services/application-insights) を使用すると、クラウドにホストされているかオンプレミスかにかかわらず、アプリケーションの可用性、パフォーマンス、使用状況を監視できます。 

Application Insights と連携するようにアプリケーションをインストルメント化すると、詳細な洞察を取得し、DevOps のシナリオを実装することができます。 そのため、ユーザーからの報告を待たずに、エラーを速やかに特定して診断できます。 収集した情報を活用すれば、アプリケーションのメンテナンスや機能強化に関する選択を十分な情報に基づいて判断することができます。 

Application Insights には、収集されたデータを操作するための豊富なツールが用意されています。 Application Insights では、そのデータは共通リポジトリに格納されます。 アラート、ダッシュボード、Log Analytics クエリ言語を使用した詳細分析などの共有機能を活用できます。

### <a name="deep-infrastructure-monitoring"></a>詳細なインフラストラクチャ監視
#### <a name="log-analytics"></a>Log Analytics
[Log Analytics](http://azure.microsoft.com/documentation/services/log-analytics) は、Azure の監視の中心的役割を果たすサービスで、さまざまなリソース (Microsoft 以外のツールを含む) から収集したデータを単一のリポジトリに格納します。 そこで、強力なクエリ言語を使用してデータを分析することができます。 

Application Insights と Azure Security Center では、Log Analytics データ ストアにデータを格納し、Log Analytics の分析エンジンを活用しています。 データは、Azure Monitor、管理ソリューション、およびクラウドやオンプレミスの仮想マシンにインストールされたエージェントからも収集されます。 この共有機能は、環境の全体像を把握するうえで役に立ちます。

#### <a name="management-solutions"></a>管理ソリューション
[管理ソリューション](../log-analytics/log-analytics-add-solutions.md)は、特定のアプリケーションまたはサービスに関する洞察を提供するロジックを 1 つにまとめたものです。 管理ソリューションは Log Analytics を使用して、収集した監視データの格納と分析を行います。 

Azure およびサード パーティの各種サービスを監視するさまざまな管理ソリューションが Microsoft とサード パーティから提供されています。 監視ソリューションの例を次に示します。
* [コンテナー監視](../log-analytics/log-analytics-containers.md): コンテナー ホストの表示および管理に役立ちます。
* [Azure SQL Analytics](../log-analytics/log-analytics-azure-sql.md): Azure SQL データベースのパフォーマンス メトリックを収集および視覚化します。

利用可能なすべての管理ソリューションを Azure Portal の *[監視]* 画面に表示できます。 

#### <a name="network-monitoring"></a>ネットワーク監視
Azure かオンプレミスかに関係なく、連携してネットワークのさまざまな面を監視するためのツールがいくつかあります。  

[Network Watcher](../network-watcher/network-watcher-monitoring-overview.md) は、Azure の各種ネットワーク シナリオに応じた監視と診断の機能を提供します。 将来の分析に使用できるように、Azure メトリックおよび診断にデータが格納されます。 次のソリューションと連携して、ネットワークのさまざまな面を監視します。 

[Network Performance Monitor (NPM)](https://blogs.msdn.microsoft.com/azuregov/2017/09/05/network-performance-monitor-general-availability/) は、パブリック クラウド、データ センター、オンプレミス環境間の接続を監視するクラウドベースのネットワーク監視ソリューションです。

[ExpressRoute モニター](https://azure.microsoft.com/blog/monitoring-of-azure-expressroute-in-preview/)は、Azure ExpressRoute 回線を介したエンド ツー エンド接続とパフォーマンスを監視する NPM 機能です。

[DNS Analytics](../log-analytics/log-analytics-dns.md) は、DNS サーバーに基づいて、セキュリティ、パフォーマンス、および操作に関連する分析情報を提供するソリューションです。

[サービス エンドポイント モニター](../networking/network-monitoring-overview.md)は、アプリケーションの到達可能性をテストし、オンプレミス、通信事業者ネットワーク、クラウド/プライベート データ センターの間のパフォーマンス ボトルネックを検出します。


#### <a name="service-map"></a>サービス マップ
[Service Map](../operations-management-suite/operations-management-suite-service-map.md) は、他のコンピューターの各種プロセスや依存関係、外部プロセスを使用して仮想マシンを分析し、IaaS 環境に関する洞察を提供します。 これにより、Log Analytics 内のイベント、パフォーマンス データ、管理ソリューションが統合されます。 このデータは、各コンピューターのコンテキストや環境内の他の要素との関係の中で把握することができます。 

Service Map は [Application Insights のアプリケーション マップ](../application-insights/app-insights-app-map.md)に似ています。 Service Map では、アプリケーションをサポートしているインフラストラクチャ コンポーネントに焦点が当てられています。


## <a name="example-scenarios"></a>シナリオ例
以降では、さまざまなシナリオで Azure の各種監視ツールを使用する方法を、概要的な例を挙げて紹介します。

### <a name="monitoring-a-web-application"></a>Web アプリケーションの監視
Azure App Service、Azure Storage、および SQL データベースを使用する、Azure にデプロイされた Web アプリケーションがあるとします。 開始するには、Azure Portal のリソース ページで、そのリソースの[メトリック](../monitoring-and-diagnostics/monitoring-overview-metrics.md)と[アクティビティ ログ](../monitoring-and-diagnostics/monitoring-overview-activity-logs.md)にアクセスします。 アプリケーションへの要求数、平均応答時間などの重要情報を確認します。 構成の変更も特定します。

次に、ポータルの Monitor に移動して、各種リソースのメトリックとログをまとめて確認します。 メトリックの標準パラメーターを確認したら、[アラート ルールを作成](../monitoring-and-diagnostics/monitoring-overview-unified-alerts.md)します。 これらのルールにより、平均応答時間がしきい値を超えた場合などに事前通知が行われます。 アプリケーションの日々のパフォーマンスをすばやく把握するには、重要な KPI を示すメトリックのグラフが表示される Azure ダッシュボードを作成します。

アプリケーションの詳細な監視を行うには、[Application Insights と連携するようアプリケーションを構成](../application-insights/quick-monitor-portal.md)します。 アプリケーションの動作状況やパフォーマンスに関する詳しい洞察を提供する追加データを収集できるようになります。 Application Insights では、アプリのコンポーネント間の基盤となる関係が検出されます。 [アプリケーション マップ](../application-insights/app-insights-app-map.md)と[エンドツーエンドのトレース](../application-insights/app-insights-transaction-diagnostics.md)を組み合わせて視覚的に表示し、問題が発生しているコンポーネント、依存関係、または例外を正確に診断できます。 

また、[可用性テスト](../application-insights/app-insights-monitor-web-app-availability.md)を作成して、複数のリージョンからアプリケーションを事前にテストします。 開発者をサポートするために、[Profiler を有効に](../application-insights/enable-profiler-compute.md)します。これで、要求や例外を特定のコード業まで絞り込むことができます。 アプリケーションで使用されているサービスを詳細に把握するには、Log Analytics で追加データを収集する [SQL Analytics ソリューション](../log-analytics/log-analytics-azure-sql.md)を追加します。 

しばらく運用した後で、サイトのパフォーマンスがしきい値以下に低下した期間について根本原因を調査することにしたとします。 Log Analytics を使用してクエリを作成します。 これは、Application Insights で収集された使用状況およびパフォーマンスに関するデータと、アプリケーションをサポートしている Azure Portal リソースの構成およびパフォーマンスに関するデータを関連付ける際に役立ちます。


### <a name="monitoring-virtual-machines"></a>仮想マシンの監視
Azure で Windows 仮想マシンと Linux 仮想マシンが混在して実行されているとします。 Azure Monitor を使用して[アクティビティ ログ](../monitoring-and-diagnostics/monitoring-overview-activity-logs.md)と[ホスト レベル メトリック](../monitoring-and-diagnostics/monitoring-overview-metrics.md)を確認します。 [Microsoft Azure 診断の拡張機能](../virtual-machines/linux/tutorial-monitoring.md#install-diagnostics-extension)を仮想マシンに追加して、ゲスト オペレーティング システムからメトリックを収集します。 次に、プロセッサ使用率、メモリなどの基本メトリックがしきい値を超えたときに事前通知するための[アラート ルール](../monitoring-and-diagnostics/monitoring-overview-unified-alerts.md)を作成します。

ビジネス アプリケーションを実行している仮想マシンの詳細情報を収集するには、各マシンで [Log Analytics ワークスペースを作成し、VM 拡張機能を有効に](../log-analytics/log-analytics-quick-collect-azurevm.md)します。 アプリケーションに関する[各種データ ソースの収集](../log-analytics/log-analytics-data-sources.md)を構成し、日々の運用状況やパフォーマンスをレポートする[ビューを作成](../log-analytics/log-analytics-view-designer.md)します。 また、特定のエラー イベントを受信したときに通知する[アラート ルール](../monitoring-and-diagnostics/monitoring-overview-unified-alerts.md)を作成します。 

インストールされているエージェントの正常性を継続的に監視するには、[Agent Health 管理ソリューション](../operations-management-suite/oms-solution-agenthealth.md)を追加します。 アプリケーションに関する詳しい洞察を得るには、仮想マシンに[依存関係エージェントを追加](../operations-management-suite/operations-management-suite-service-map-configure.md)し、仮想マシンを [Service Map](../operations-management-suite/operations-management-suite-service-map.md) に追加します。 Service Map により重要なプロセスが検出され、仮想マシンと他のサービスとの接続が特定されます。 

サービスの停止が報告されたときには、Service Map を使ってフォレンジクスを実行し、問題が発生している仮想マシンを特定します。 次に、今後発生する問題を特定するために、[Log Analytics データに対するクエリ](../log-analytics/log-analytics-log-search-new.md)を作成します。 そして、そのような状態が検出されたときに事前通知するためのアラート ルールを作成します。



## <a name="next-steps"></a>次の手順
各項目の詳細情報

* [Azure Monitor](https://azure.microsoft.com/services/monitor/)。コアな監視のメトリックとアラートの使用を開始します。
* [Application Insights](https://azure.microsoft.com/documentation/services/application-insights/) (App Service Web アプリの問題を診断する場合)。
* [Log Analytics](https://azure.microsoft.com/documentation/services/log-analytics/)。収集した監視データとログを分析する場合は、こちらをご覧ください。
