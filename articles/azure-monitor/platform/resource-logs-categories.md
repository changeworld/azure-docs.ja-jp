---
title: Azure Monitor リソース ログでサポートされているサービスとカテゴリ
description: Azure Monitor のリファレンス - Azure リソース ログでサポートされているサービスとイベント スキーマについて説明します。
ms.subservice: logs
ms.topic: reference
ms.date: 12/09/2020
ms.openlocfilehash: c7b2d48b40843930bba78f54d2294769d952daf6
ms.sourcegitcommit: 1756a8a1485c290c46cc40bc869702b8c8454016
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/09/2020
ms.locfileid: "96931231"
---
# <a name="supported-categories-for-azure-resource-logs"></a>Azure リソース ログでサポートされているカテゴリ

> [!NOTE]
> リソース ログは、以前は診断ログと呼ばれていました。 この名前は、Azure Monitor によって収集されたログの種類が、Azure リソースだけでなくそれ以外のものも含むように移行したため、2019 年 10 月に変更されました。

[Azure Monitor リソース ログ](./platform-logs-overview.md)は、Azure サービスによって出力されるログであり、そのサービスやリソースの操作が記述されます。 Azure Monitor を通じて使用できるすべてのリソース ログには、共通の上位スキーマが共有されます。各サービスが、独自のイベントに固有のプロパティを出力するための柔軟性も備わっています。

(`resourceId` プロパティで使用可能な) リソースの種類と `category` を組み合わせて、スキーマを一意に識別します。 サービス固有のフィールドを含むすべてのリソース ログに共通のスキーマがあり、それぞれのログ カテゴリに対して追加されています。 詳細については、[Azure リソース ログの共通のスキーマとサービス固有のスキーマ]()に関するページを参照してください


## <a name="costs"></a>コスト

Log Analytics、Azure Storage、またはイベント ハブにデータを送信し、格納することでコストが発生します。 これらの場所にデータを送り、そこに保管するために、場合によってはコストを支払う必要があります。  リソース ログは、これらの場所に送信できるデータの一種です。 これらの場所に[一部のカテゴリのリソース ログをエクスポートするとコスト](https://azure.microsoft.com/pricing/details/monitor/)が追加で発生します。それに該当しないカテゴリの場合、エクスポート費用は無料です。 エクスポート コストの詳細を下の表に示します。

## <a name="supported-log-categories-per-resource-type"></a>リソースの種類ごとのサポートされているログ カテゴリ

各リソースの種類で使用可能なログの種類の一覧を次に示します。 

一部のカテゴリは、特定の種類のリソースに対してのみサポートされています。 リソースが不足していると思われる場合は、リソース固有のドキュメントを参照してください。 たとえば、Microsoft.Sql/servers/databases カテゴリは、すべての種類のデータベースで使用できるわけではありません。 詳細については、[SQL Database の診断ログに関する情報](../../azure-sql/database/metrics-diagnostic-telemetry-logging-streaming-export-configure.md)を参照してください。 

まだ何かが不足している場合は、この記事の下部にある GitHub コメントを開くことができます。
## <a name="microsoftanalysisservicesservers"></a>Microsoft.AnalysisServices/servers

コスト:Free 

|カテゴリ |カテゴリの表示名|
|---|---|
|エンジン|エンジン|
|サービス|サービス|


## <a name="microsoftapimanagementservice"></a>Microsoft.ApiManagement/service

コスト:Free 

|カテゴリ |カテゴリの表示名|
|---|---|
|GatewayLogs|ApiManagement Gateway に関連するログ|


## <a name="microsoftappplatformspring"></a>Microsoft.AppPlatform/Spring

コスト:Free 

|カテゴリ |カテゴリの表示名|
|---|---|
|ApplicationConsole|アプリケーション コンソール|
|SystemLogs|システム ログ|


## <a name="microsoftautomationautomationaccounts"></a>Microsoft.Automation/automationAccounts

コスト:Free 

|カテゴリ |カテゴリの表示名|
|---|---|
|DscNodeStatus|DSC ノードの状態|
|JobLogs|ジョブ ログ|
|JobStreams|ジョブ ストリーム|


## <a name="microsoftbatchbatchaccounts"></a>Microsoft.Batch/batchAccounts

コスト:Free 

|カテゴリ |カテゴリの表示名|
|---|---|
|ServiceLog|サービス ログ|


## <a name="microsoftbatchaiworkspaces"></a>Microsoft.BatchAI/workspaces

コスト:Free 

|カテゴリ |カテゴリの表示名|
|---|---|
|BaiClusterEvent|BaiClusterEvent|
|BaiClusterNodeEvent|BaiClusterNodeEvent|
|BaiJobEvent|BaiJobEvent|


## <a name="microsoftblockchainblockchainmembers"></a>Microsoft.Blockchain/blockchainMembers

コスト:Free 

|カテゴリ |カテゴリの表示名|
|---|---|
|BlockchainApplication|ブロックチェーン アプリケーション|
|FabricOrderer|Fabric Orderer|
|FabricPeer|Fabric Peer|
|プロキシ|プロキシ|


## <a name="microsoftblockchaincordamembers"></a>Microsoft.Blockchain/cordaMembers

コスト:Free 

|カテゴリ |カテゴリの表示名|
|---|---|
|BlockchainApplication|ブロックチェーン アプリケーション|


## <a name="microsoftcdncdnwebapplicationfirewallpolicies"></a>Microsoft.Cdn/cdnwebapplicationfirewallpolicies

コスト:Free 

|カテゴリ |カテゴリの表示名|
|---|---|
|WebApplicationFirewallLogs|Web アプリケーション ファイアウォールのログ|


## <a name="microsoftcdnprofiles"></a>Microsoft.Cdn/profiles

コスト:Free 

|カテゴリ |カテゴリの表示名|
|---|---|
|AzureCdnAccessLog|Azure Cdn アクセス ログ|


## <a name="microsoftcdnprofilesendpoints"></a>Microsoft.Cdn/profiles/endpoints

コスト:Free 

|カテゴリ |カテゴリの表示名|
|---|---|
|CoreAnalytics|エンドポイントのメトリック (帯域幅、エグレスなど) を取得します。|


## <a name="microsoftclassicnetworknetworksecuritygroups"></a>Microsoft.ClassicNetwork/networksecuritygroups

コスト:Free 

|カテゴリ |カテゴリの表示名|
|---|---|
|ネットワーク セキュリティ グループの規則フロー イベント|ネットワーク セキュリティ グループの規則フロー イベント|


## <a name="microsoftcognitiveservicesaccounts"></a>Microsoft.CognitiveServices/accounts

コスト:Free 

|カテゴリ |カテゴリの表示名|
|---|---|
|Audit|[監査ログ]|
|RequestResponse|要求と応答のログ|
|Trace|トレース ログ|


## <a name="microsoftcontainerregistryregistries"></a>Microsoft.ContainerRegistry/registries

コスト:Free 

|カテゴリ |カテゴリの表示名|
|---|---|
|ContainerRegistryLoginEvents|ログイン イベント|
|ContainerRegistryRepositoryEvents|RepositoryEvent ログ|


## <a name="microsoftcontainerservicemanagedclusters"></a>Microsoft.ContainerService/managedClusters

コスト:Free 

|カテゴリ |カテゴリの表示名|
|---|---|
|cluster-autoscaler|Kubernetes クラスター オートスケーラー|
|kube-apiserver|Kubernetes API サーバー|
|kube-audit|Kubernetes の監査|
|kube-controller-manager|Kubernetes コントローラー マネージャー|
|kube-scheduler|Kubernetes スケジューラ|


## <a name="microsoftcustomprovidersresourceproviders"></a>Microsoft.CustomProviders/resourceproviders

コスト:Free 

|カテゴリ |カテゴリの表示名|
|---|---|
|AuditLogs|MiniRP 呼び出しの監査ログ|


## <a name="microsoftdatabricksworkspaces"></a>Microsoft.Databricks/workspaces

コスト:Free 

|カテゴリ |カテゴリの表示名|
|---|---|
|accounts|Databricks アカウント|
|clusters|Databricks クラスター|
|dbfs|Databricks ファイル システム|
|instancePools|インスタンス プール|
|jobs|Databricks ジョブ|
|ノートブック|Databricks Notebook|
|secrets|Databricks シークレット|
|sqlPermissions|Databricks SQLPermissions|
|ssh|Databricks SSH|
|ワークスペース|Databricks ワークスペース|


## <a name="microsoftdatafactoryfactories"></a>Microsoft.DataFactory/factories

コスト:Free 

|カテゴリ |カテゴリの表示名|
|---|---|
|ActivityRuns|パイプライン アクティビティ実行ログ|
|PipelineRuns|パイプライン実行ログ|
|TriggerRuns|トリガー実行ログ|


## <a name="microsoftdatalakestoreaccounts"></a>Microsoft.DataLakeStore/accounts

コスト:Free 

|カテゴリ |カテゴリの表示名|
|---|---|
|Audit|[監査ログ]|
|Requests|要求ログ|


## <a name="microsoftdatashareaccounts"></a>Microsoft.DataShare/accounts

コスト:Free 

|カテゴリ |カテゴリの表示名|
|---|---|
|ReceivedShareSnapshots|受信された共有スナップショット|
|SentShareSnapshots|送信された共有スナップショット|
|共有|共有|
|ShareSubscriptions|共有サブスクリプション|


## <a name="microsoftdbformariadbservers"></a>Microsoft.DBforMariaDB/servers

コスト:Free 

|カテゴリ |カテゴリの表示名|
|---|---|
|MySqlAuditLogs|MariaDB 監査ログ|
|MySqlSlowLogs|MariaDB サーバー ログ|


## <a name="microsoftdbformysqlflexibleservers"></a>Microsoft.DBforMySQL/flexibleServers

コスト:Free 

|カテゴリ |カテゴリの表示名|
|---|---|
|MySqlAuditLogs|MySQL 監査ログ|
|MySqlSlowLogs|MySQL Slow ログ|


## <a name="microsoftdbformysqlservers"></a>Microsoft.DBforMySQL/servers

コスト:Free 

|カテゴリ |カテゴリの表示名|
|---|---|
|MySqlAuditLogs|MySQL 監査ログ|
|MySqlSlowLogs|MySQL サーバーのログ|


## <a name="microsoftdbforpostgresqlflexibleservers"></a>Microsoft.DBforPostgreSQL/flexibleServers

コスト:Free 

|カテゴリ |カテゴリの表示名|
|---|---|
|PostgreSQLLogs|PostgreSQL サーバー ログ|


## <a name="microsoftdbforpostgresqlservers"></a>Microsoft.DBforPostgreSQL/servers

コスト:Free 

|カテゴリ |カテゴリの表示名|
|---|---|
|PostgreSQLLogs|PostgreSQL サーバー ログ|
|QueryStoreRuntimeStatistics|PostgreSQL クエリ ストアのランタイム統計|
|QueryStoreWaitStatistics|PostgreSQL クエリ ストアの待機統計|


## <a name="microsoftdbforpostgresqlserversv2"></a>Microsoft.DBforPostgreSQL/serversv2

コスト:Free 

|カテゴリ |カテゴリの表示名|
|---|---|
|PostgreSQLLogs|PostgreSQL サーバー ログ|


## <a name="microsoftdesktopvirtualizationapplicationgroups"></a>Microsoft.DesktopVirtualization/applicationgroups

コスト:Free 

|カテゴリ |カテゴリの表示名|
|---|---|
|Checkpoint|Checkpoint|
|エラー|エラー|
|管理|管理|


## <a name="microsoftdesktopvirtualizationhostpools"></a>Microsoft.DesktopVirtualization/hostpools

コスト:Free 

|カテゴリ |カテゴリの表示名|
|---|---|
|Checkpoint|Checkpoint|
|Connection|Connection|
|エラー|エラー|
|HostRegistration|HostRegistration|
|管理|管理|


## <a name="microsoftdesktopvirtualizationworkspaces"></a>Microsoft.DesktopVirtualization/workspaces

コスト:Free 

|カテゴリ |カテゴリの表示名|
|---|---|
|Checkpoint|Checkpoint|
|エラー|エラー|
|フィード|フィード|
|管理|管理|


## <a name="microsoftdevicesiothubs"></a>Microsoft.Devices/IotHubs

コスト:Free 

|カテゴリ |カテゴリの表示名|
|---|---|
|C2DCommands|C2D コマンド|
|C2DTwinOperations|C2D ツイン操作|
|構成|構成|
|接続|接続|
|D2CTwinOperations|D2CTwinOperations|
|DeviceIdentityOperations|デバイス ID の操作|
|DeviceStreams|デバイス ストリーム (プレビュー)|
|DeviceTelemetry|デバイス テレメトリ|
|DirectMethods|ダイレクト メソッド|
|DistributedTracing|分散トレース (プレビュー)|
|FileUploadOperations|ファイルのアップロード操作|
|JobsOperations|ジョブ操作|
|ルート|ルート|
|TwinQueries|ツイン クエリ|


## <a name="microsoftdevicesprovisioningservices"></a>Microsoft.Devices/provisioningServices

コスト:Free 

|カテゴリ |カテゴリの表示名|
|---|---|
|DeviceOperations|デバイス操作|
|ServiceOperations|サービス操作|


## <a name="microsoftdocumentdbdatabaseaccounts"></a>Microsoft.DocumentDB/databaseAccounts

コスト:Free 

|カテゴリ |カテゴリの表示名|
|---|---|
|CassandraRequests|CassandraRequests|
|ControlPlaneRequests|ControlPlaneRequests|
|DataPlaneRequests|DataPlaneRequests|
|GremlinRequests|GremlinRequests|
|MongoRequests|MongoRequests|
|PartitionKeyRUConsumption|PartitionKeyRUConsumption|
|PartitionKeyStatistics|PartitionKeyStatistics|
|QueryRuntimeStatistics|QueryRuntimeStatistics|


## <a name="microsofteventgriddomains"></a>Microsoft.EventGrid/domains

コスト:Free 

|カテゴリ |カテゴリの表示名|
|---|---|
|DeliveryFailures|配信エラーのログ|
|PublishFailures|発行エラーのログ|


## <a name="microsofteventgridsystemtopics"></a>Microsoft.EventGrid/systemTopics

コスト:Free 

|カテゴリ |カテゴリの表示名|
|---|---|
|DeliveryFailures|配信エラーのログ|


## <a name="microsofteventgridtopics"></a>Microsoft.EventGrid/topics

コスト:Free 

|カテゴリ |カテゴリの表示名|
|---|---|
|DeliveryFailures|配信エラーのログ|
|PublishFailures|発行エラーのログ|


## <a name="microsofteventhubnamespaces"></a>Microsoft.EventHub/namespaces

コスト:Free 

|カテゴリ |カテゴリの表示名|
|---|---|
|ArchiveLogs|アーカイブ ログ|
|AutoScaleLogs|自動スケール ログ|
|CustomerManagedKeyUserLogs|顧客管理キーのログ|
|EventHubVNetConnectionEvent|VNet/IP フィルタリング接続ログ|
|KafkaCoordinatorLogs|Kafka コーディネーター ログ|
|KafkaUserErrorLogs|Kafka ユーザー エラー ログ|
|OperationalLogs|操作ログ|


## <a name="microsofthealthcareapisservices"></a>Microsoft.HealthcareApis/services

コスト:Free 

|カテゴリ |カテゴリの表示名|
|---|---|
|AuditLogs|監査ログ|


## <a name="microsoftinsightsautoscalesettings"></a>Microsoft.Insights/AutoscaleSettings

コスト:Free 

|カテゴリ |カテゴリの表示名|
|---|---|
|AutoscaleEvaluations|自動スケーリング検証|
|AutoscaleScaleActions|自動スケーリングのスケーリング操作|


## <a name="microsoftinsightscomponents"></a>Microsoft.Insights/Components

コスト:Free 

|カテゴリ |カテゴリの表示名|
|---|---|
|AppAvailabilityResults|可用性の結果|
|AppBrowserTimings|ブラウザーのタイミング|
|AppDependencies|依存関係|
|AppEvents|events|
|AppExceptions|例外|
|AppMetrics|メトリック|
|AppPageViews|ページ ビュー|
|AppPerformanceCounters|パフォーマンス カウンター|
|AppRequests|Requests|
|AppSystemEvents|システム イベント|
|AppTraces|トレース|


## <a name="microsoftkeyvaultvaults"></a>Microsoft.KeyVault/vaults

コスト:Free 

|カテゴリ |カテゴリの表示名|
|---|---|
|AuditEvent|[監査ログ]|


## <a name="microsoftkustoclusters"></a>Microsoft.Kusto/Clusters

コスト:Free 

|カテゴリ |カテゴリの表示名|
|---|---|
|コマンド|コマンド|
|FailedIngestion|失敗した取り込み操作|
|IngestionBatching|インジェスト バッチ処理|
|クエリ|クエリ|
|SucceededIngestion|成功した取り込み操作|
|TableDetails|テーブル詳細|
|TableUsageStatistics|テーブルの使用状況に関する統計情報|


## <a name="microsoftlogicintegrationaccounts"></a>Microsoft.Logic/integrationAccounts

コスト:Free 

|カテゴリ |カテゴリの表示名|
|---|---|
|IntegrationAccountTrackingEvents|統合アカウント追跡イベント|


## <a name="microsoftlogicworkflows"></a>Microsoft.Logic/workflows

コスト:Free 

|カテゴリ |カテゴリの表示名|
|---|---|
|WorkflowRuntime|ワークフロー ランタイムの診断イベント|


## <a name="microsoftmachinelearningservicesworkspaces"></a>Microsoft.MachineLearningServices/workspaces

コスト:Free 

|カテゴリ |カテゴリの表示名|
|---|---|
|AmlComputeClusterEvent|AmlComputeClusterEvent|
|AmlComputeClusterNodeEvent|AmlComputeClusterNodeEvent|
|AmlComputeCpuGpuUtilization|AmlComputeCpuGpuUtilization|
|AmlComputeJobEvent|AmlComputeJobEvent|
|AmlRunStatusChangedEvent|AmlRunStatusChangedEvent|


## <a name="microsoftmediamediaservices"></a>Microsoft.Media/mediaservices

コスト:Free 

|カテゴリ |カテゴリの表示名|
|---|---|
|KeyDeliveryRequests|キー配信要求|


## <a name="microsoftnetworkapplicationgateways"></a>Microsoft.Network/applicationGateways

コスト:Free 

|カテゴリ |カテゴリの表示名|
|---|---|
|ApplicationGatewayAccessLog|アプリケーション ゲートウェイのアクセス ログ|
|ApplicationGatewayFirewallLog|アプリケーション ゲートウェイのファイアウォール ログ|
|ApplicationGatewayPerformanceLog|アプリケーション ゲートウェイのパフォーマンス ログ|


## <a name="microsoftnetworkazurefirewalls"></a>Microsoft.Network/azurefirewalls

コスト:Free 

|カテゴリ |カテゴリの表示名|
|---|---|
|AzureFirewallApplicationRule|Azure Firewall アプリケーション ルール|
|AzureFirewallNetworkRule|Azure Firewall ネットワーク ルール|


## <a name="microsoftnetworkbastionhosts"></a>Microsoft.Network/bastionHosts

コスト:Free 

|カテゴリ |カテゴリの表示名|
|---|---|
|BastionAuditLogs|Bastion 監査ログ|


## <a name="microsoftnetworkexpressroutecircuits"></a>Microsoft.Network/expressRouteCircuits

コスト:Free 

|カテゴリ |カテゴリの表示名|
|---|---|
|PeeringRouteLog|ピアリングのルート テーブルのログ|


## <a name="microsoftnetworkfrontdoors"></a>Microsoft.Network/frontdoors

コスト:Free 

|カテゴリ |カテゴリの表示名|
|---|---|
|FrontdoorAccessLog|Frontdoor アクセス ログ|
|FrontdoorWebApplicationFirewallLog|Frontdoor Web アプリケーション ファイアウォール ログ|


## <a name="microsoftnetworkloadbalancers"></a>Microsoft.Network/loadBalancers

コスト:Free 

|カテゴリ |カテゴリの表示名|
|---|---|
|LoadBalancerAlertEvent|ロード バランサーのアラート イベント|
|LoadBalancerProbeHealthStatus|ロード バランサーのプローブ正常性状態|


## <a name="microsoftnetworknetworksecuritygroups"></a>Microsoft.Network/networksecuritygroups

コスト:Free 

|カテゴリ |カテゴリの表示名|
|---|---|
|NetworkSecurityGroupEvent|ネットワーク セキュリティ グループ イベント|
|NetworkSecurityGroupFlowEvent|ネットワーク セキュリティ グループの規則フロー イベント|
|NetworkSecurityGroupRuleCounter|ネットワーク セキュリティ グループの規則数|


## <a name="microsoftnetworkpublicipaddresses"></a>Microsoft.Network/publicIPAddresses

コスト:Free 

|カテゴリ |カテゴリの表示名|
|---|---|
|DDoSMitigationFlowLogs|DDoS 軽減策に関する意思決定のフロー ログ|
|DDoSMitigationReports|DDoS 軽減策のレポート|
|DDoSProtectionNotifications|DDoS 保護通知|


## <a name="microsoftnetworktrafficmanagerprofiles"></a>Microsoft.Network/trafficManagerProfiles

コスト:Free 

|カテゴリ |カテゴリの表示名|
|---|---|
|ProbeHealthStatusEvents|Traffic Manager プローブの正常性結果イベント|


## <a name="microsoftnetworkvirtualnetworkgateways"></a>Microsoft.Network/virtualNetworkGateways

コスト:Free 

|カテゴリ |カテゴリの表示名|
|---|---|
|GatewayDiagnosticLog|ゲートウェイ診断ログ|
|IKEDiagnosticLog|IKE 診断ログ|
|P2SDiagnosticLog|P2S 診断ログ|
|RouteDiagnosticLog|ルート診断ログ|
|TunnelDiagnosticLog|トンネル診断ログ|


## <a name="microsoftnetworkvirtualnetworks"></a>Microsoft.Network/virtualNetworks

コスト:Free 

|カテゴリ |カテゴリの表示名|
|---|---|
|VMProtectionAlerts|VM 保護アラート|


## <a name="microsoftpowerbidedicatedcapacities"></a>Microsoft.PowerBIDedicated/capacities

コスト:Free 

|カテゴリ |カテゴリの表示名|
|---|---|
|エンジン|エンジン|


## <a name="microsoftrecoveryservicesvaults"></a>Microsoft.RecoveryServices/Vaults

コスト:Free 

|カテゴリ |カテゴリの表示名|
|---|---|
|AddonAzureBackupAlerts|アドオン Azure Backup アラート データ|
|AddonAzureBackupJobs|アドオン Azure Backup ジョブ データ|
|AddonAzureBackupPolicy|アドオン Azure Backup ポリシー データ|
|AddonAzureBackupProtectedInstance|アドオン Azure Backup 保護されたインスタンス データ|
|AddonAzureBackupStorage|アドオン Azure Backup ストレージ データ|
|AzureBackupReport|Azure Backup レポート データ|
|AzureSiteRecoveryEvents|Azure Site Recovery イベント|
|AzureSiteRecoveryJobs|Azure Site Recovery ジョブ|
|AzureSiteRecoveryProtectedDiskDataChurn|Azure Site Recovery で保護されたディスクのデータ変更頻度|
|AzureSiteRecoveryRecoveryPoints|Azure Site Recovery 回復ポイント|
|AzureSiteRecoveryReplicatedItems|Azure Site Recovery レプリケートされた項目|
|AzureSiteRecoveryReplicationDataUploadRate|Azure Site Recovery のレプリケーション データ アップロード速度|
|AzureSiteRecoveryReplicationStats|Azure Site Recovery レプリケーション状態|
|CoreAzureBackup|コア Azure Backup データ|


## <a name="microsoftrelaynamespaces"></a>Microsoft.Relay/namespaces

コスト:Free 

|カテゴリ |カテゴリの表示名|
|---|---|
|HybridConnectionsEvent|HybridConnections イベント|


## <a name="microsoftsearchsearchservices"></a>Microsoft.Search/searchServices

コスト:Free 

|カテゴリ |カテゴリの表示名|
|---|---|
|OperationLogs|[操作ログ]|


## <a name="microsoftservicebusnamespaces"></a>Microsoft.ServiceBus/namespaces

コスト:Free 

|カテゴリ |カテゴリの表示名|
|---|---|
|OperationalLogs|操作ログ|


## <a name="microsoftsignalrservicesignalr"></a>Microsoft.SignalRService/SignalR

コスト:Free 

|カテゴリ |カテゴリの表示名|
|---|---|
|AllLogs|Azure SignalR Service ログ。|


## <a name="microsoftsqlmanagedinstances"></a>Microsoft.Sql/managedInstances

コスト:Free 

|カテゴリ |カテゴリの表示名|
|---|---|
|DevOpsOperationsAudit|Devops 操作の監査ログ|
|ResourceUsageStats|リソース使用統計|
|SQLSecurityAuditEvents|SQL セキュリティ監査イベント|


## <a name="microsoftsqlmanagedinstancesdatabases"></a>Microsoft.Sql/managedInstances/databases

コスト:Free 

|カテゴリ |カテゴリの表示名|
|---|---|
|エラー|エラー|
|QueryStoreRuntimeStatistics|クエリ ストアのランタイム統計|
|QueryStoreWaitStatistics|クエリ ストアの待機統計|
|SQLInsights|SQL Insights|


## <a name="microsoftsqlserversdatabases"></a>Microsoft.Sql/servers/databases

コスト:Free 

|カテゴリ |カテゴリの表示名|
|---|---|
|AutomaticTuning|自動チューニング|
|Blocks|Blocks|
|DatabaseWaitStatistics|データベースの待機統計|
|デッドロック|デッドロック|
|DevOpsOperationsAudit|Devops 操作の監査ログ|
|DmsWorkers|DMS worker|
|エラー|エラー|
|ExecRequests|実行要求|
|QueryStoreRuntimeStatistics|クエリ ストアのランタイム統計|
|QueryStoreWaitStatistics|クエリ ストアの待機統計|
|RequestSteps|要求ステップ|
|SQLInsights|SQL Insights|
|SqlRequests|SQL 要求|
|SQLSecurityAuditEvents|SQL セキュリティ監査イベント|
|Timeouts|Timeouts|
|待機|待機|


## <a name="microsoftstoragestorageaccountsblobservices"></a>Microsoft.Storage/storageAccounts/blobServices

コスト:有料。詳細は [Azure Monitor の価格](https://azure.microsoft.com/pricing/details/monitor/)ページの「プラットフォーム ログ」セクションにあります。 

|カテゴリ |カテゴリの表示名|
|---|---|
|StorageDelete|StorageDelete|
|StorageRead|StorageRead|
|StorageWrite|StorageWrite|


## <a name="microsoftstoragestorageaccountsfileservices"></a>Microsoft.Storage/storageAccounts/fileServices

コスト:有料。詳細は [Azure Monitor の価格](https://azure.microsoft.com/pricing/details/monitor/)ページの「プラットフォーム ログ」セクションにあります。 

|カテゴリ |カテゴリの表示名|
|---|---|
|StorageDelete|StorageDelete|
|StorageRead|StorageRead|
|StorageWrite|StorageWrite|


## <a name="microsoftstoragestorageaccountsqueueservices"></a>Microsoft.Storage/storageAccounts/queueServices

コスト:有料。詳細は [Azure Monitor の価格](https://azure.microsoft.com/pricing/details/monitor/)ページの「プラットフォーム ログ」セクションにあります。 
 
|カテゴリ |カテゴリの表示名|
|---|---|
|StorageDelete|StorageDelete|
|StorageRead|StorageRead|
|StorageWrite|StorageWrite|


## <a name="microsoftstoragestorageaccountstableservices"></a>Microsoft.Storage/storageAccounts/tableServices

コスト:有料。詳細は [Azure Monitor の価格](https://azure.microsoft.com/pricing/details/monitor/)ページの「プラットフォーム ログ」セクションにあります。 
 
|カテゴリ |カテゴリの表示名|
|---|---|
|StorageDelete|StorageDelete|
|StorageRead|StorageRead|
|StorageWrite|StorageWrite|


## <a name="microsoftstreamanalyticsstreamingjobs"></a>Microsoft.StreamAnalytics/streamingjobs

コスト:Free 

|カテゴリ |カテゴリの表示名|
|---|---|
|Authoring|Authoring|
|実行|実行|


## <a name="microsoftsynapseworkspaces"></a>Microsoft.Synapse ワークスペース

コスト:Free 

|カテゴリ |カテゴリの表示名|
|---|---|
|BuiltinSqlReqsEnded|組み込みの SQL プール要求終了|
|GatewayApiRequests|Synapse Gateway API 要求|
|SQLSecurityAuditEvents|SQL セキュリティ監査イベント|
|SynapseRbacOperations|Synapse RBAC 操作|


## <a name="microsoftsynapseworkspacesbigdatapools"></a>Microsoft.Synapse/workspaces/bigDataPools

コスト:Free 

|カテゴリ |カテゴリの表示名|
|---|---|
|BigDataPoolAppsEnded|ビッグ データ プール アプリケーション終了|


## <a name="microsoftsynapseworkspacessqlpools"></a>Microsoft.Synapse/workspaces/sqlPools

コスト:Free 

|カテゴリ |カテゴリの表示名|
|---|---|
|DmsWorkers|DMS worker|
|ExecRequests|実行要求|
|RequestSteps|要求ステップ|
|SqlRequests|SQL 要求|
|SQLSecurityAuditEvents|SQL セキュリティ監査イベント|
|待機|待機|


## <a name="microsoftwebhostingenvironments"></a>microsoft.web/hostingenvironments

コスト:Free 

|カテゴリ |カテゴリの表示名|
|---|---|
|AppServiceEnvironmentPlatformLogs|Azure App Service Environment のプラットフォーム ログ|


## <a name="microsoftwebsites"></a>microsoft.web/sites

コスト:Free 


|カテゴリ |カテゴリの表示名|
|---|---|
|AppServiceAppLogs|App Service アプリケーション ログ|
|AppServiceAuditLogs|監査ログへのアクセス|
|AppServiceConsoleLogs|App Service コンソール ログ|
|AppServiceFileAuditLogs|サイト コンテンツの変更に関する監査ログ|
|AppServiceHTTPLogs|HTTP ログ|
|FunctionAppLogs|関数アプリケーション ログ|


## <a name="microsoftwebsitesslots"></a>microsoft.web/sites/slots

コスト:Free 


|カテゴリ |カテゴリの表示名|
|---|---|
|AppServiceAppLogs|App Service アプリケーション ログ|
|AppServiceAuditLogs|監査ログへのアクセス|
|AppServiceConsoleLogs|App Service コンソール ログ|
|AppServiceFileAuditLogs|サイト コンテンツの変更に関する監査ログ|
|AppServiceHTTPLogs|HTTP ログ|
|FunctionAppLogs|関数アプリケーション ログ|


## <a name="next-steps"></a>次の手順

* [リソース ログの詳細を確認する](./platform-logs-overview.md)
* [リソースのリソース ログを **Event Hubs** にストリーミングする](./resource-logs.md#send-to-azure-event-hubs)
* [Azure Monitor REST API を使用してリソース ログの診断設定を変更する](/rest/api/monitor/diagnosticsettings)
* [Log Analytics を使用した、Azure ストレージからのログの分析](./resource-logs.md#send-to-log-analytics-workspace)

