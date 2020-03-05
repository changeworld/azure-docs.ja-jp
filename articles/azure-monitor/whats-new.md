---
title: Azure Monitor のドキュメントの最新情報
description: 毎月更新される Azure Monitor のドキュメントに対する重要な更新。
ms.subservice: ''
ms.topic: overview
author: bwren
ms.author: bwren
ms.date: 02/05/2020
ms.openlocfilehash: ecee13850e735f827a5465e0f49039f514afe233
ms.sourcegitcommit: 747a20b40b12755faa0a69f0c373bd79349f39e3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/27/2020
ms.locfileid: "77657743"
---
# <a name="whats-new-in-azure-monitor-documentation"></a>Azure Monitor のドキュメントの最新情報
この記事では、Azure Monitor に関して新たに執筆された記事または大幅に更新された記事を一覧で紹介しています。 毎月第 1 週目に更新され、前月から更新された記事が掲載されます。

## <a name="january-2020"></a>2020 年 1 月

### <a name="general"></a>全般
- [Azure Monitor によって監視される内容](monitor-reference.md) - 新しい記事。

### <a name="agents"></a>エージェント
- [Azure Log Analytics エージェントを使用してログ データを収集する](platform/log-analytics-agent.md) - ネットワーク ファイアウォールの要件に関する表が更新されています。


### <a name="alerts"></a>警告
- [Azure portal でのアクション グループの作成および管理](platform/action-groups.md) - 不要になった v2 関数用の設定を削除しました。
- [Resource Manager テンプレートでのメトリック アラートの作成](platform/alerts-metric-create-templates.md) - *ignoreDataBefore* パラメーターの例を追加しました。  複数条件のルールに関する制約を追加しました。
- [Log Analytics のアラート REST API の使用](platform/api-alerts.md) - JSON の例を修正しました。


### <a name="application-insights"></a>Application Insights
- [Application Insights および Log Analytics によって使用される IP アドレス](app/ip-addresses.md) - Azure ネットワーク セキュリティ グループを使用してトラフィックを許可するための受信ポート規則の追加方法に関して、「可用性テスト」セクションを更新しました。
- [Azure Application Insights Profiler に関する問題のトラブルシューティング](app/profiler-troubleshooting.md) - 一般的なトラブルシューティングを更新しました。
- [Azure Application Insights におけるテレメトリ サンプリング](app/sampling.md) - お客様からのフィードバックに基づいて読みやすさを改善するために更新、改編しました。


### <a name="data-security"></a>データのセキュリティ
- [Azure Monitor のカスタマー マネージド キーの構成](platform/customer-managed-keys.md) - 新しい記事。

### <a name="insights-and-solutions"></a>分析情報とソリューション

#### <a name="azure-monitor-for-containers"></a>Azure Monitor for Containers
- [Azure Monitor for containers エージェントのデータ収集を構成する](https://docs.microsoft.com/azure/azure-monitor/insights/container-insights-agent-config) - Azure Red Hat OpenShift におけるエージェントのアップグレードに関する詳しい情報を追加しました。また、エージェントのアップグレード方法を識別するための補足情報を追加しました。
- [Azure Monitor for containers におけるパフォーマンス アラートを作成する](https://docs.microsoft.com/azure/azure-monitor/insights/container-insights-alerts) - ワークスペースコンテキストのアラートを使用して、ワークスペースに格納されているパフォーマンス データに関するアラートを作成する方法についての情報を修正し、手順を更新しました。
- [Azure Monitor for containers での Kubernetes の監視](https://docs.microsoft.com/azure/azure-monitor/insights/container-insights-analyze) - Windows Kubernetes クラスターのサポートに関する概要記事と分析記事の両方を更新しました。
- [Azure Red Hat OpenShift クラスターに Azure Monitor for containers を構成する](https://docs.microsoft.com/azure/azure-monitor/insights/container-insights-azure-redhat-setup) - Azure Red Hat OpenShift におけるエージェントのアップグレードに関する詳しい情報を追加しました。また、エージェントのアップグレード方法を識別するための補足情報を追加しました。
- [ハイブリッド Kubernetes クラスターに Azure Monitor for containers を構成する](https://docs.microsoft.com/azure/azure-monitor/insights/container-insights-hybrid-setup) - Kubelet の cAdvisor を使用した secure port:10250 のサポート追加を反映するために更新しました。
- [Azure Monitor for containers エージェントを管理する方法](https://docs.microsoft.com/azure/azure-monitor/insights/container-insights-manage-agent) - Azure Red Hat OpenShift と他の種類の Kubernetes クラスターとを比較して、メトリック スクレーピングの動作と構成に関する詳細情報を更新しました。
- [Azure Monitor for containers の Prometheus 統合を構成する](https://docs.microsoft.com/azure/azure-monitor/insights/container-insights-prometheus-integration) - Azure Red Hat OpenShift と他の種類の Kubernetes クラスターとを比較して、メトリック スクレーピングの動作と構成に関する詳細情報を更新しました。
- [メトリックのための Azure Monitor for containers の更新方法](https://docs.microsoft.com/azure/azure-monitor/insights/container-insights-update-metrics) - Azure Red Hat OpenShift と他の種類の Kubernetes クラスターとを比較して、メトリック スクレーピングの動作と構成に関する詳細情報を更新しました。


#### <a name="azure-monitor-for-vms"></a>VM に対する Azure Monitor
- [Azure Monitor for VMs (GA) についてよく寄せられる質問](insights/vminsights-ga-release-faq.md) - ワークスペースとエージェントの新バージョンへのアップグレードに関する情報を追加しました。

#### <a name="office-365"></a>Office 365
- [Azure の Office 365 管理ソリューション](insights/solution-office-365.md) - Azure Sentinel における Office 365 ソリューションへの移行に関する詳細および FAQ を追加しました。 オンボーディング セクションを削除しました。



### <a name="logs"></a>ログ
- [Azure Monitor で Log Analytics ワークスペースを管理する](platform/manage-access.md) - 「非アクション」を更新しました。
- [Azure Monitor ログの使用量とコストを管理する](platform/manage-cost-storage.md) - 「価格モデル」セクションにデータ量の計算についての説明を追加しました。
- [Azure Resource Manager テンプレートを使用して Log Analytics ワークスペースの作成と構成を行う](platform/template-workspace-configuration.md) - 新しい価格レベルでテンプレートを更新しました。


### <a name="platform-logs"></a>プラットフォーム ログ
- [診断設定を使用して Azure アクティビティ ログを収集する - Azure Monitor](platform/diagnostic-settings-legacy.md) - 変更されたプロパティについての情報を追加しました。
- [Azure アクティビティ ログをエクスポートする](platform/activity-log-export.md) - UI の変更に合わせて更新しました。 





## <a name="december-2019"></a>2019 年 12 月

### <a name="agents"></a>エージェント
- [Linux コンピューターを Azure Monitor に接続する](platform/agent-linux.md) - 新しい記事。

### <a name="alerts"></a>警告
- [Resource Manager テンプレートでのメトリック アラートの作成](platform/alerts-metric-create-templates.md) - カスタム メトリックの例を追加しました。
- [Azure Monitor で動的しきい値を使用したアラートを作成する](platform/alerts-dynamic-thresholds.md) - 動的しきい値のグラフの解釈に関するセクションを追加しました。
- [Azure でのアラートと通知監視の概要](platform/alerts-overview.md) - Resource Graph クエリを更新しました。
- [Azure Monitor のメトリック アラートでサポートされるリソース](platform/alerts-metric-near-real-time.md) - サポートされるメトリックとディメンションについて更新しました。
- [従来の Log Analytics アラート API から新しい Azure Alerts API に切り替える](platform/alerts-log-api-switch.md) - 変更されたアラート名についての注意を追加しました。
- [Azure Monitor でのメトリック アラートのしくみを理解する](platform/alerts-metric-overview.md) - 大規模な監視でサポートされるリソースの種類を追加しました。

### <a name="application-insights"></a>Application Insights
- [ワーカー サービス アプリ (非 HTTP アプリ) 向け Application Insights](app/worker-service.md) - C# コードに既定のログ レベルを追加しました。 パッケージの参照バージョンを更新しました。
- [ApplicationInsights.config リファレンス - Azure](app/configuration-with-applicationinsights-config.md) - サンプル コードを更新しました。
- [PowerShell での Azure Application Insights の自動化](app/powershell.md) - Resource Manager テンプレートへの更新。
- [Azure Monitor Application Insights NuGet パッケージ](app/nuget.md) - パッケージのバージョンを更新しました。
- [新しい Azure Application Insights リソースの作成](app/create-new-resource.md) - グローバルに一意の名前についての注意を追加しました。
- [Live Metrics Stream による診断 - Azure Application Insights](app/live-stream.md) - ASP.NET Core SDK バージョンの要件を更新しました。
- [Application Insights のイベント カウンター](app/eventcounters.md) - customMetrics のカテゴリとテーブルを更新しました。
- [Azure Application Insights を使用した Java トレース ログの探索](app/java-trace-logs.md) - Java エージェントのログのしきい値の構成を追加しました。
- [Application Insights および Log Analytics によって使用される IP アドレス](app/ip-addresses.md) - Live Metrics Stream の IP アドレスを更新しました。
- [Azure App Services のパフォーマンスを監視する](app/azure-web-apps.md) - ASP.NET Core 3.0 のサポートを追加しました。 
- [Azure Monitor を使用して Python アプリを監視する (プレビュー)](app/opencensus-python.md) - Azure Monitor スキーマに対する OpenCensus Python のスキーマ マッピングに関する説明を追加しました。
- [Azure Application Insights のリリース ノート](app/release-notes.md) - 以前のリリースに関する注意を追加しました。
- [Azure Application Insights におけるテレメトリ チャネル](app/telemetry-channels.md) - 長時間にわたる接続喪失時に破棄されるデータの期間を更新しました。
- [Azure Application Insights におけるテレメトリ サンプリング](app/sampling.md) - カスタム TelemetryInitializer のコード スニペットを修正しました。
- [Java Web プロジェクトでの Application Insights のトラブルシューティング](app/java-troubleshoot.md) - JDK 9 で依存関係の収集が未サポートであることに関する記述を削除しました。

### <a name="insights-and-solutions"></a>分析情報とソリューション
- [Azure Monitor for containers についてよく寄せられる質問](insights/container-insights-faq.md) - Image フィールドと Name フィールドに関する質問を追加しました。
- [Azure Monitor の Azure SQL Analytics ソリューション](insights/azure-sql.md) - 「データベース待機」の「マネージド インスタンスのサポート」を更新しました。
- [Azure Monitor for containers エージェントのデータ収集を構成する](insights/container-insights-agent-config.md) - enrich_container_logs の設定を追加しました。
- [ハイブリッド Kubernetes クラスターに Azure Monitor for containers を構成する](insights/container-insights-hybrid-setup.md) - トラブルシューティング セクションを追加しました。
- [Azure Monitor で Active Directory のレプリケーション状態を監視する](insights/ad-replication-status.md) - .NET Framework の前提条件を更新しました。
- [Azure の Network Performance Monitor ソリューション](insights/network-performance-monitor.md) - サポートされるリージョンを追加しました。
- [Azure Monitor で Active Directory 環境を最適化する](insights/ad-assessment.md) - .NET Framework の前提条件を更新しました。
- [Azure Monitor で SQL Server 環境を最適化する](insights/sql-assessment.md) - .NET Framework の前提条件を更新しました。
- [Azure Log Analytics で System Center Operations Manager 環境を最適化する](insights/scom-assessment.md) - .NET Framework の前提条件を更新しました。
- [Azure Log Analytics の IT Service Management Connector とのサポートされている接続](platform/itsmc-connections.md) - 前提条件のクライアント ID とクライアント シークレットにニューヨークを追加しました。

### <a name="logs"></a>ログ
- [Azure Log Analytics ワークスペースの削除と復旧](platform/delete-workspace.md) - PowerShell メソッドを追加しました。
- [Azure Monitor ログのデプロイの設計](platform/design-logs-deployment.md) - ワークスペースのインジェスト率を増やしました。

### <a name="metrics"></a>メトリック
- [診断設定を使用してエクスポート可能な Azure Monitor プラットフォーム メトリック](platform/metrics-supported-export-diagnostic-settings.md) - 新しい記事。

### <a name="platform-logs"></a>プラットフォーム ログ
プラットフォーム ログのコンテンツ再編成の一環として、診断設定を使用してアクティビティ ログを構成するための新機能に基づいて複数の記事を更新しました。

- [Azure リソース ログをストレージ アカウントにアーカイブする](platform/resource-logs-collect-storage.md)
- [Azure アクティビティ ログのイベント スキーマ](platform/activity-log-schema.md)
- [Azure Monitor サービスの制限](service-limits.md)
- [Log Analytics ワークスペースで Azure アクティビティ ログを収集して分析する](platform/activity-log-collect.md)
- [診断設定を使用して Azure アクティビティ ログを収集する (プレビュー) - Azure Monitor](platform/diagnostic-settings-legacy.md)
- [Azure テナントにまたがる Log Analytics ワークスペースに Azure アクティビティ ログを収集する](platform/activity-log-collect-tenants.md)
- [Log Analytics ワークスペースで Azure リソース ログを収集する](platform/resource-logs-collect-workspace.md)
- [Azure で Resource Manager テンプレートを使用して診断設定を作成する](platform/diagnostic-settings-template.md)
- [Azure でログとメトリックを収集するための診断設定を作成する](platform/diagnostic-settings.md)
- [Azure アクティビティ ログをエクスポートする](platform/activity-log-export.md)
- [Azure プラットフォーム ログの概要](platform/platform-logs-overview.md)
- [イベント ハブへの Azure 監視データのストリーム配信](platform/stream-monitoring-data-event-hubs.md)
- [Azure プラットフォーム ログをイベント ハブにストリーム配信する](platform/resource-logs-stream-event-hubs.md)

### <a name="quickstarts-and-tutorials"></a>クイック スタートとチュートリアル

- [Azure Monitor でのメトリック グラフの作成](learn/tutorial-metrics-explorer.md) - 新しい記事。
- [Azure リソースからリソース ログを収集し、Azure Monitor で分析する](learn/tutorial-resource-logs.md) - 新しい記事。
- [Azure Monitor を使用して Azure リソースを監視する](learn/quick-monitor-azure-resource.md) - 新しい記事。
   
## <a name="next-steps"></a>次のステップ

- Azure Monitor のドキュメント作成に協力していただける方は、[Docs 共同作成者ガイド](https://docs.microsoft.com/contribute/)に関するページを参照してください。