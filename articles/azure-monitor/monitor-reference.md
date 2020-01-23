---
title: Azure Monitor によって監視される内容
description: Azure Monitor によって監視されるすべてのサービスとその他のリソースのリファレンス。
ms.service: azure-monitor
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 01/09/2020
ms.openlocfilehash: 21ad0c38b8901c1e33e05ab162552cb074a392a4
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/15/2020
ms.locfileid: "75969591"
---
# <a name="what-is-monitored-by-azure-monitor"></a>Azure Monitor によって監視される内容
この記事では、Azure Monitor によって監視されるさまざまなアプリケーションとサービスについて説明します。 

## <a name="insights-and-core-solutions"></a>Insights とコア ソリューション
コアの Insights とソリューションは Azure Monitor の一部と見なされ、Azure のサポートとサービス レベル アグリーメントに従います。 これらは、Azure Monitor が使用可能なすべての Azure リージョンでサポートされています。

### <a name="insights"></a>洞察

Insights は、特定のアプリケーションやサービスを監視するためのカスタマイズされたエクスペリエンスを提供します。 ログとメトリックの両方を収集して分析します。

| 分析情報 | [説明] |
|:---|:---|
| [Application Insights](app/app-insights-overview.md) | 任意のプラットフォームでライブ Web アプリケーションを監視するための拡張可能なアプリケーション パフォーマンス管理 (APM) サービス。 |
| [Azure Monitor for Containers](insights/container-insights-overview.md) | Azure Kubernetes Service (AKS) でホストされている Azure Container Instances またはマネージド Kubernetes クラスターにデプロイされたコンテナー ワークロードのパフォーマンスを監視します。 |
| [Azure Monitor for Cosmos DB (プレビュー)](insights/cosmosdb-insights-overview.md) | すべての Azure Cosmos DB リソースの全体的なパフォーマンス、エラー、容量、操作上の正常性のビューを、統一された対話型エクスペリエンスで提供します。 |
| [Azure Monitor for Networks (プレビュー)](insights/network-insights-overview.md) | すべてのネットワーク リソースの正常性とメトリックの包括的ビューを提供します。 高度な検索機能を使用すると、Web サイト名を検索するだけで、Web サイトをホストしているリソースを特定するなどのシナリオが可能になり、リソースの依存関係を特定できます。 |
[Azure Monitor for Resource Groups (プレビュー)](insights/resource-group-insights.md) |  個別のリソースで発生した問題をトリアージおよび診断する一方で、リソース グループ全体の正常性とパフォーマンスに関するコンテキストを提供します。 |
| [Azure Monitor for Storage (プレビュー)](insights/storage-insights-overview.md) | Azure Storage サービスのパフォーマンス、容量、可用性の統一されたビューを提供することにより、Azure Storage アカウントの包括的な監視を提供します。 |
| [Azure Monitor for VMs (プレビュー)](insights/container-insights-overview.md) | Azure 仮想マシン (VM) と仮想マシン スケール セットを大規模に監視します。 これにより、ご利用の Windows VM および Linux VM のプロセスや、その他のリソースおよび外部プロセスとの依存関係を監視することにより、それらの VM のパフォーマンスおよび正常性が分析されます。 |

### <a name="core-solutions"></a>コア ソリューション

ソリューションは、特定のアプリケーションまたはサービス用にカスタマイズされたログ クエリとビューに基づいています。 それらはログの収集と分析のみを行い、時間の経過とともに非推奨となり、Insights が優先されます。

| 解決策 | [説明] |
|:---|:---|
| [Agent Health](insights/solution-agenthealth.md) | Log Analytics エージェントの正常性と構成を分析します。 |
| [アラートの管理](platform/alert-management-solution.md) | System Center Operations Manager、Nagios、または Zabbix から収集されたアラートを分析します。 |
| [サービス マップ](insights/service-map.md) | Windows および Linux システム上のアプリケーション コンポーネントを自動的に検出し、サービス間の通信をマップします。 次でも同じ機能が提供されます:   |



## <a name="azure-services"></a>Azure サービス
次の表に、Azure サービスと、Azure Monitor に収集されるデータを一覧表示します。 

- メトリック - このサービスは、メトリックを自動的に Azure Monitor Metrics に収集します。 
- ログ - このサービスは、プラットフォームのログとメトリックを Azure Monitor ログに収集できる診断設定をサポートしています。
- 分析情報 - サービスで使用可能な分析情報があり、サービスに対するカスタマイズされた監視エクスペリエンスを提供します。

| サービス | メトリック | ログ | 分析情報 | メモ |
|:---|:---|:---|:---|:---|
|Active Directory | いいえ | はい | [はい](../active-directory/reports-monitoring/howto-use-azure-monitor-workbooks.md) |  |
|Active Directory B2C | いいえ | いいえ | いいえ |  |
|Active Directory Domain Services | いいえ | はい | いいえ |  |
|アクティビティ ログ | いいえ | はい | いいえ | |
|高度な脅威保護 | いいえ | いいえ | いいえ |  |
|Advisor | いいえ | いいえ | いいえ |  |
|AI Builder | いいえ | いいえ | いいえ |  |
|Analysis Services | はい | はい | いいえ |  |
|API for FHIR | いいえ | いいえ | いいえ |  |
|API Management | はい | はい | いいえ |  |
|App Service | はい | はい | いいえ |  |
|AppConfig | いいえ | いいえ | いいえ |  |
|Application Gateway | はい | はい | いいえ |  |
|構成証明サービス | いいえ | いいえ | いいえ |  |
|Automation | はい | はい | いいえ |  |
|Azure Service Manager (RDFE) | いいえ | いいえ | いいえ |  |
|バックアップ | いいえ | はい | いいえ |  |
|Bastion | いいえ | いいえ | いいえ |  |
|Batch | はい | はい | いいえ |  |
|Batch AI | いいえ | いいえ | いいえ |  |
|Blockchain Service | いいえ | はい | いいえ |  |
|ブループリント | いいえ | いいえ | いいえ |  |
|ボット サービス | いいえ | いいえ | いいえ |  |
|Cloud Services | はい | はい | いいえ | ゲスト オペレーティング システムとワークフローを監視するためにエージェントが必要。  |
|Cloud Shell | いいえ | いいえ | いいえ |  |
|Cognitive Services | はい | はい | いいえ |  |
|Container Instances | はい | いいえ | いいえ |  |
|Container Registry | はい | はい | いいえ |  |
|Content Delivery Network (CDN) | いいえ | はい | いいえ |  |
|Cosmos DB | はい | はい | [はい](insights/cosmosdb-insights-overview.md) |  |
|Cost Management | いいえ | いいえ | いいえ |  |
|Data Box | いいえ | いいえ | いいえ |  |
|Data Catalog Gen2 | いいえ | いいえ | いいえ |  |
|Data Explorer | はい | はい | いいえ |  |
|Data Factory | はい | はい | いいえ |  |
|Data Factory v2 | いいえ | はい | いいえ |  |
|データ共有 | いいえ | いいえ | いいえ |  |
|Database for MariaDB | はい | はい | いいえ |  |
|Database for MySQL | はい | はい | いいえ |  |
|Database for PostgreSQL | はい | はい | いいえ |  |
|Database Migration Service | いいえ | いいえ | いいえ |  |
|Databricks | いいえ | はい | いいえ |  |
|DDoS Protection | はい | はい | いいえ |  |
|DevOps | いいえ | いいえ | いいえ |  |
|DNS | はい | いいえ | いいえ |  |
|ドメイン名 | いいえ | いいえ | いいえ |  |
|DPS | いいえ | いいえ | いいえ |  |
|Dynamics 365 Customer Engagement | いいえ | いいえ | いいえ |  |
|Dynamics 365 Finance and Operations | いいえ | いいえ | いいえ |  |
|Event Grid | はい | いいえ | いいえ |  |
|Event Hubs | はい | はい | いいえ |  |
|ExpressRoute | はい | はい | いいえ |  |
|ファイアウォール | はい | はい | いいえ |  |
|フロントドア | はい | はい | いいえ |  |
|関数 | はい | はい | いいえ |  |
|HDInsight | いいえ | はい | いいえ |  |
|HPC Cache | いいえ | いいえ | いいえ |  |
|Information Protection | いいえ | はい | いいえ |  |
|Intune | いいえ | はい | いいえ |  |
|IoT Central | いいえ | いいえ | いいえ |  |
|IoT Hub | はい | はい | いいえ |  |
|Key Vault | はい | はい | いいえ |  |
|Kubernetes サービス (AKS) | いいえ | いいえ | [はい](insights/container-insights-overview.md)  |  |
|Load Balancer | はい | はい | いいえ |  |
|Logic Apps | はい | はい | いいえ |  |
|Machine Learning Service | いいえ | いいえ | いいえ |  |
|Managed Applications  | いいえ | いいえ | いいえ |  |
|マップ  | いいえ | いいえ | いいえ |  |
|Media Services | はい | はい | いいえ |  |
|Microsoft Flow | いいえ | いいえ | いいえ |  |
|Microsoft マネージド デスクトップ | いいえ | いいえ | いいえ |  |
|Microsoft PowerApps | いいえ | いいえ | いいえ |  |
|Microsoft Social Engagement | いいえ | いいえ | いいえ |  |
|Microsoft Stream | はい | はい | いいえ |  |
|移行 | いいえ | いいえ | いいえ |  |
|Multi-Factor Authentication | いいえ | はい | いいえ |  |
|Network Watcher | はい | はい | いいえ |  |
|Notification Hubs | はい | いいえ | いいえ |  |
|オープン データセット | いいえ | いいえ | いいえ |  |
|ポリシー | いいえ | いいえ | いいえ |  |
|Power BI | はい | はい | いいえ |  |
|Power BI Embedded | いいえ | いいえ | いいえ |  |
|Private Link | いいえ | いいえ | いいえ |  |
|Project Spool Communication Platform | いいえ | いいえ | いいえ |  |
|Red Hat OpenShift | いいえ | いいえ | いいえ |  |
|Redis Cache | はい | はい | いいえ |  |
|リソース グラフ | いいえ | いいえ | いいえ |  |
|リソース マネージャー | いいえ | いいえ | いいえ |  |
|Retail Search - Bing | いいえ | いいえ | いいえ |  |
|検索 | はい | はい | いいえ |  |
|Service Bus | はい | はい | いいえ |  |
|Service Fabric | いいえ | はい | いいえ | ゲスト オペレーティング システムとワークフローを監視するためにエージェントが必要。  |
|サインアップ ポータル | いいえ | いいえ | いいえ |  |
|Site Recovery | いいえ | はい | いいえ |  |
|Spring Cloud サービス | いいえ | いいえ | いいえ |  |
|SQL Data Warehouse | はい | はい | いいえ |  |
|SQL Database | はい | はい | いいえ |  |
|SQL Server Stretch Database | はい | はい | いいえ |  |
|スタック | いいえ | いいえ | いいえ |  |
|ストレージ | はい | いいえ | [はい](insights/storage-insights-overview.md) |  |
|ストレージ キャッシュ | いいえ | いいえ | いいえ |  |
|ストレージ同期サービス | いいえ | いいえ | いいえ |  |
|Stream Analytics | はい | はい | いいえ |  |
|Time Series Insights | はい | はい | いいえ |  |
|TINA | いいえ | いいえ | いいえ |  |
|Traffic Manager | はい | はい | いいえ |  |
|ユニバーサル印刷 | いいえ | いいえ | いいえ |  |
|Virtual Machine Scale Sets | いいえ | はい | [はい](insights/vminsights-overview.md) | ゲスト オペレーティング システムとワークフローを監視するためにエージェントが必要。 |
|Virtual Machines | はい | はい | [はい](insights/vminsights-overview.md) | ゲスト オペレーティング システムとワークフローを監視するためにエージェントが必要。 |
|Virtual Network | はい | はい | [はい](insights/network-insights-overview.md) |  |
|Virtual Network - NSG フロー ログ | いいえ | はい | いいえ |  |
|VPN Gateway | はい | はい | いいえ |  |
|Windows Virtual Desktop | いいえ | いいえ | いいえ |  |


## <a name="product-integrations"></a>製品の統合
次の表のサービスとソリューションは、Azure Monitor によって収集された他のログ データを使用して分析できるように、データを Log Analytics ワークスペースに格納します。

| 製品/サービス | [説明] |
|:---|:---|
| [Azure Automation](/azure/automation/) | Windows および Linux のコンピューターでオペレーティング システムの更新を管理し、変更を追跡します。 [Change Tracking](../automation/change-tracking.md) と [Update Management](../automation/automation-update-management.md) に関する記事を参照してください。 |
| [Azure Information Protection ](https://docs.microsoft.com/azure/information-protection/) | ドキュメントと電子メールを分類し、必要に応じて保護します。 「[Azure Information Protection の中央レポート機能](https://docs.microsoft.com/azure/information-protection/reports-aip#configure-a-log-analytics-workspace-for-the-reports)」を参照してください。 |
| [Azure Security Center](/azure/security-center/) | セキュリティ イベントを収集して分析し、脅威分析を実行します。 「[Azure Security Center でのデータ収集](/azure/security-center/security-center-enable-data-collection)」を参照してください。 |
| [Azure Sentinel](/azure/sentinel/) | Office 365 やアマゾン ウェブ サービスの Cloud Trail などのさまざまなソースに接続します。 「[データ ソースの接続](/azure/sentinel/connect-data-sources)」のページを参照してください。 |
| [Key Vault Analytics](insights/azure-key-vault.md) | Azure Key Vault の AuditEvent ログを分析します。 |
| [Microsoft Intune](https://docs.microsoft.com/intune/) | Azure Monitor にログを送信するための診断設定を作成します。 「[Intune でストレージ、イベント ハブ、または Log Analytics にログ データを送信する (プレビュー)](https://docs.microsoft.com/intune/fundamentals/review-logs-using-azure-monitor)」を参照してください。  |
| ネットワーク  | [DNS Analytics](insights/dns-analytics.md) - Windows DNS の分析ログと監査ログおよび他の関連データを DNS サーバーから収集して分析し、関連付けます。<br>[Network Performance Monitor](insights/network-performance-monitor.md) - サービスとアプリケーションのエンドポイントへのネットワーク接続とパフォーマンスを監視します。<br>[Azure Application Gateway](insights/azure-networking-analytics.md#azure-application-gateway-analytics-solution-in-azure-monitor) - Azure Application Gateway からログとメトリックを分析します。<br>[Traffic Analytics](/azure/network-watcher/traffic-analytics) - Network Watcher のネットワーク セキュリティ グループ (NSG) フロー ログを分析して、Azure クラウドでのトラフィック フローに関する分析情報を提供します。 |
| [Office 365](insights/solution-office-365.md) | Office 365 環境を監視します。 Azure Sentinel から使用できる改善されたオンボード機能を備えた更新バージョンです。 |
| [SQL Analytics](insights/azure-sql.md) | 複数のサブスクリプションにわたって大規模に Azure SQL データベース、エラスティック プール、マネージド インスタンスのパフォーマンスを監視します。 |
| [Surface Hub](insights/surface-hubs.md) | Surface Hub デバイスの正常性と使用状況を追跡します。 |
| [System Center Operations Manager](https://docs.microsoft.com/system-center/scom) | 管理グループを Azure Monitor に接続して Operations Manager エージェントからデータを収集します。 「[Operations Manager を Azure Monitor に接続する](platform/om-agents.md)」を参照してください。<br> [Operations Manager Assessment](insights/scom-assessment.md) ソリューションを使用して、System Center Operations Manager 管理グループのリスクと正常性を評価します。 |
| [Microsoft Teams ミーティング](https://docs.microsoft.com/microsoftteams/room-systems/azure-monitor-deploy) | Microsoft Teams ミーティング デバイスの統合されたエンドツーエンド管理。 |
| [Visual Studio App Center](https://docs.microsoft.com/appcenter/) | アプリケーションをビルド、テスト、および配布し、その状態と使用状況を監視します。 「[App Center と Application Insights によるモバイル アプリの分析の開始](learn/mobile-center-quickstart.md)」を参照してください。 |
| Windows | [Windows Update Compliance](https://docs.microsoft.com/windows/deployment/update/update-compliance-get-started) - Windows デスクトップのアップグレードを評価します。<br>[Desktop Analytics](https://docs.microsoft.com/configmgr/desktop-analytics/overview) - Configuration Manager と統合して、Windows クライアントの更新準備について、より多くの情報に基づいて意思決定を行うための分析情報とインテリジェンスを提供します。 |



## <a name="other-solutions"></a>その他のソリューション
さまざまなアプリケーションやサービスを監視するためにその他のソリューションを使用することもできますが、アクティブな開発は停止しており、すべてのリージョンで使用できるとは限りません。 それらは、Azure Log Analytics データ インジェストのサービス レベル アグリーメントによってカバーされます。

| 解決策 | [説明] |
|:---|:---|
| [Active Directory Assessment](insights/ad-assessment.md) | Active Directory 環境のリスクと正常性を評価します。 |
| [Active Directory Replication Status](insights/ad-replication-status.md) | レプリケーションの失敗について Active Directory 環境を定期的に監視します。 |
| [Activity Log Analytics](platform/activity-log-view.md#activity-logs-analytics-monitoring-solution) | 定義済みのログ クエリおよびビューを使用して、アクティビティ ログのエントリを分析します。 |
| [Cloud Foundry](../cloudfoundry/cloudfoundry-oms-nozzle.md) | 複数のデプロイにわたって Cloud Foundry システムの正常性とパフォーマンスのメトリックを収集、表示、および分析します。 |
| [Containers](insights/containers.md) | Docker と Windows のコンテナー ホストを表示および管理します。 |
| [オンデマンド評価](https://docs.microsoft.com/services-hub/health/getting_started_with_on_demand_assessments) | オンプレミス、ハイブリッド、クラウドの Microsoft テクノロジ環境の可用性、セキュリティ、およびパフォーマンスを評価し、最適化します。 |
| [SQL Assessment](insights/sql-assessment.md) | SQL Server 環境のリスクと正常性を評価します。  |
| [ワイヤ データ](insights/wire-data.md) | Log Analytics エージェントを使用して、Windows に接続されたコンピューターおよび Linux に接続されたコンピューターから収集したネットワークとパフォーマンスのデータを統合したものです。 |


## <a name="third-party-integration"></a>サード パーティの統合

| 解決策 | [説明] |
|:---|:---|
| [ITSM](platform/itsmc-overview.md) | IT Service Management Connector (ITSMC) を使用すると、サポートされている IT Service Management (ITSM) 製品/サービスと Azure を接続できます。  |


## <a name="resources-outside-of-azure"></a>Azure 外部のリソース
Azure Monitor は、次の表に示す方法を使用して、Azure 外部のリソースからデータを収集できます。

| リソース | 方法 |
|:---|:---|
| [アプリケーション] | Application Insights を使用して、Azure 外部の Web アプリケーションを監視します。 「[Application Insights とは何か?](https://docs.microsoft.com/azure/azure-monitor/app/app-insights-overview)」を参照してください。 |
| 仮想マシン | Log Analytics エージェントを使用して、他のクラウド環境やオンプレミスにある仮想マシンのゲスト オペレーティング システムからデータを収集します。 「[Log Analytics エージェントを使用してログ データを収集する](platform/log-analytics-agent.md)」を参照してください。 |
| REST API クライアント | 別の API を使用して、任意の REST API クライアントから Azure Monitor ログとメトリックにデータを書き込むことができます。 ログについては、「[HTTP データ コレクター API を使用して Azure Monitor にログ データを送信する](platform/data-collector-api.md)」を参照し、メトリックについては、「[REST API を使用して Azure リソースのカスタム メトリックを Azure Monitor メトリック ストアに送信する](platform/metrics-store-custom-rest-api.md)」を参照してください。 |



## <a name="next-steps"></a>次のステップ

- [分析とソリューションによって収集されたログとメトリックを格納する Azure Monitor データ プラットフォーム](platform/data-platform.md)に関する詳細情報をお読みください。
- [Azure リソースの監視に関するチュートリアル](learn/tutorial-resource-logs.md)を完了してください。
- [Azure Monitor ログでデータを分析するためのログ クエリの作成に関するチュートリアル](learn/tutorial-resource-logs.md)を完了してください。
- [Azure Monitor メトリックでデータを分析するためのメトリック グラフの作成に関するチュートリアル](learn/tutorial-metrics-explorer.md)を完了してください。

 
