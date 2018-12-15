---
title: リソースでの Azure Resource Manager タグのサポート
description: タグをサポートしている Azure リソースの種類を示します。 すべての Azure サービスの詳細を提供します。
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: reference
ms.date: 11/20/2018
ms.author: tomfitz
ms.openlocfilehash: 580955d3c6fd7a33c152e49e601d8078eb169a22
ms.sourcegitcommit: c37122644eab1cc739d735077cf971edb6d428fe
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/14/2018
ms.locfileid: "53409668"
---
# <a name="tag-support-for-azure-resources"></a>Azure リソースでのタグのサポート
この記事では、リソースの種類が[タグ付け](resource-group-using-tags.md)をサポートしているかどうかについて説明します。

## <a name="aad-domain-services"></a>AAD ドメイン サービス
| リソースの種類 | タグのサポート |
| ------------- | ----------- |
| domains | いいえ  | 

## <a name="ad-hybrid-health-service"></a>AD Hybrid Health Service
| リソースの種類 | タグのサポート |
| ------------- | ----------- |
| services | いいえ  | 
| addsservices | いいえ  | 
| 構成 | いいえ  | 
| agents | いいえ  | 
| aadsupportcases | いいえ  | 
| reports | いいえ  | 
| servicehealthmetrics | いいえ  | 
| ログ | いいえ  | 
| anonymousapiusers | いいえ  | 

## <a name="analysis-services"></a>Analysis Services
| リソースの種類 | タグのサポート |
| ------------- | ----------- |
| サーバー | [はい] | 

## <a name="api-hubs"></a>API ハブ
| リソースの種類 | タグのサポート |
| ------------- | ----------- |
| apiManagementAccounts | いいえ  | 
| apiManagementAccounts/connectionProviders | いいえ  | 
| apiManagementAccounts/connections | いいえ  | 
| apiManagementAccounts/connectionAcls | いいえ  | 
| apiManagementAccounts/connectionProviderAcls | いいえ  | 
| apiManagementAccounts/apis | いいえ  | 

## <a name="api-management"></a>API Management
| リソースの種類 | タグのサポート |
| ------------- | ----------- |
| service | [はい] | 

## <a name="automation"></a>Automation
| リソースの種類 | タグのサポート |
| ------------- | ----------- |
| automationAccounts | [はい] | 
| automationAccounts/runbooks | [はい] | 
| automationAccounts/configurations | [はい] | 
| automationAccounts/webhooks | いいえ  | 
| automationAccounts/softwareUpdateConfigurations | いいえ  | 
| automationAccounts/jobs | いいえ  | 

## <a name="batch"></a>Batch
| リソースの種類 | タグのサポート |
| ------------- | ----------- |
| batchAccounts | [はい] | 

## <a name="bing-maps"></a>Bing Maps
| リソースの種類 | タグのサポート |
| ------------- | ----------- |
| mapApis | [はい] | 

## <a name="biztalk-services"></a>BizTalk Services
| リソースの種類 | タグのサポート |
| ------------- | ----------- |
| BizTalk | [はい] | 

## <a name="cache"></a>キャッシュ
| リソースの種類 | タグのサポート |
| ------------- | ----------- |
| Redis | [はい] | 

## <a name="cdn"></a>CDN
| リソースの種類 | タグのサポート |
| ------------- | ----------- |
| profiles | [はい] | 
| profiles/endpoints | [はい] | 
| profiles/endpoints/origins | いいえ  | 
| profiles/endpoints/customdomains | いいえ  | 
| validateProbe | いいえ  | 
| edgenodes | いいえ  | 

## <a name="classic-compute"></a>従来の Compute
| リソースの種類 | タグのサポート |
| ------------- | ----------- |
| domainNames | いいえ  | 
| domainNames/slots | いいえ  | 
| domainNames/slots/roles | いいえ  | 
| virtualMachines | いいえ  | 
| virtualMachines/diagnosticSettings | いいえ  | 
| virtualMachines/metricDefinitions | いいえ  | 
| virtualMachines/metrics | いいえ  | 

## <a name="classic-infrastructure-migrate"></a>従来のインフラストラクチャの移行
| リソースの種類 | タグのサポート |
| ------------- | ----------- |
| classicInfrastructureResources | いいえ  | 

## <a name="classic-network"></a>従来のネットワーク
| リソースの種類 | タグのサポート |
| ------------- | ----------- |
| virtualNetworks | いいえ  | 
| virtualNetworks/virtualNetworkPeerings | いいえ  | 
| virtualNetworks/remoteVirtualNetworkPeeringProxies | いいえ  | 

## <a name="classic-storage"></a>従来のストレージ
| リソースの種類 | タグのサポート |
| ------------- | ----------- |
| storageAccounts/services | いいえ  | 
| storageAccounts/services/diagnosticSettings | いいえ  | 

## <a name="compute"></a>Compute
| リソースの種類 | タグのサポート |
| ------------- | ----------- |
| availabilitySets | [はい] | 
| virtualMachines | [はい] | 
| virtualMachines/extensions | [はい] | 
| virtualMachineScaleSets | [はい] | 
| virtualMachineScaleSets/extensions | いいえ  | 
| virtualMachineScaleSets/virtualMachines | いいえ  | 
| virtualMachineScaleSets/networkInterfaces | いいえ  | 
| virtualMachineScaleSets/virtualMachines/networkInterfaces | いいえ  | 
| virtualMachineScaleSets/publicIPAddresses | いいえ  | 
| restorePointCollections | [はい] | 
| restorePointCollections/restorePoints | いいえ  | 
| virtualMachines/diagnosticSettings | いいえ  | 
| virtualMachines/metricDefinitions | いいえ  | 
| sharedVMImages | [はい] | 
| sharedVMImages/versions | [はい] | 
| ディスク | [はい] | 
| スナップショット | [はい] | 
| images | [はい] | 

## <a name="container"></a>コンテナー
| リソースの種類 | タグのサポート |
| ------------- | ----------- |
| containerGroups | [はい] | 

## <a name="container-instance"></a>コンテナー インスタンス
| リソースの種類 | タグのサポート |
| ------------- | ----------- |
| containerGroups | [はい] | 
| serviceAssociationLinks | いいえ  | 

## <a name="container-service"></a>Container Service
| リソースの種類 | タグのサポート |
| ------------- | ----------- |
| containerServices | [はい] | 

## <a name="cortana-analytics"></a>Cortana Analytics
| リソースの種類 | タグのサポート |
| ------------- | ----------- |
| accounts | [はい] | 

## <a name="cosmos-db"></a>Cosmos DB
| リソースの種類 | タグのサポート |
| ------------- | ----------- |
| databaseAccounts | [はい] | 
| databaseAccountNames | いいえ  | 

## <a name="cost-management"></a>Cost Management
| リソースの種類 | タグのサポート |
| ------------- | ----------- |
| コネクタ | [はい] | 

## <a name="data-box-edge"></a>Data Box Edge
| リソースの種類 | タグのサポート |
| ------------- | ----------- |
| DataBoxEdgeDevices | [はい] | 

## <a name="data-catalog"></a>Data Catalog
| リソースの種類 | タグのサポート |
| ------------- | ----------- |
| カタログ | [はい] | 

## <a name="data-connect"></a>データ接続
| リソースの種類 | タグのサポート |
| ------------- | ----------- |
| connectionManagers | [はい] | 

## <a name="data-factory"></a>Data Factory
| リソースの種類 | タグのサポート |
| ------------- | ----------- |
| dataFactories | [はい] | 
| ファクトリ | [はい] | 
| factories/integrationRuntimes | いいえ  | 
| dataFactories/diagnosticSettings | いいえ  | 
| dataFactories/metricDefinitions | いいえ  | 
| dataFactorySchema | いいえ  | 

## <a name="devices"></a>デバイス
| リソースの種類 | タグのサポート |
| ------------- | ----------- |
| IotHubs | [はい] | 
| IotHubs/eventGridFilters | いいえ  | 
| ProvisioningServices | [はい] | 

## <a name="devspaces"></a>Devspaces
| リソースの種類 | タグのサポート |
| ------------- | ----------- |
| controllers | [はい] | 

## <a name="devtest-lab"></a>Devtest ラボ
| リソースの種類 | タグのサポート |
| ------------- | ----------- |
| labs | [はい] | 
| schedules | [はい] | 
| labs/virtualMachines | [はい] | 
| labs/serviceRunners | [はい] | 

## <a name="dynamics-lcs"></a>Dynamics LCS
| リソースの種類 | タグのサポート |
| ------------- | ----------- |
| lcsprojects | いいえ  | 
| lcsprojects/connectors | いいえ  | 
| lcsprojects/clouddeployments | いいえ  | 

## <a name="event-grid"></a>Event Grid
| リソースの種類 | タグのサポート |
| ------------- | ----------- |
| eventSubscriptions | いいえ  | 
| topics | [はい] | 
| domains | [はい] | 
| domains/topics | いいえ  | 
| topicTypes | いいえ  | 
| extensionTopics | いいえ  | 

## <a name="event-hub"></a>イベント ハブ
| リソースの種類 | タグのサポート |
| ------------- | ----------- |
| namespaces | [はい] | 
| クラスター | [はい] | 

## <a name="hana-on-azure"></a>HANA on Azure
| リソースの種類 | タグのサポート |
| ------------- | ----------- |
| hanaInstances | [はい] | 

## <a name="hdinsight"></a>HDInsight
| リソースの種類 | タグのサポート |
| ------------- | ----------- |
| クラスター | [はい] | 
| clusters/applications | いいえ  | 

## <a name="import-export"></a>Import Export
| リソースの種類 | タグのサポート |
| ------------- | ----------- |
| ジョブ | [はい] | 

## <a name="insights"></a>洞察
| リソースの種類 | タグのサポート |
| ------------- | ----------- |
| components | [はい] | 
| components/query | いいえ  | 
| components/metrics | いいえ  | 
| components/events | いいえ  | 
| webtests | [はい] | 
| クエリ | いいえ  | 
| scheduledqueryrules | [はい] | 
| components/pricingPlans | いいえ  | 
| migrateToNewPricingModel | いいえ  | 
| rollbackToLegacyPricingModel | いいえ  | 
| automatedExportSettings | いいえ  | 
| Workbooks | [はい] | 
| myWorkbooks | いいえ  | 
| ログ | いいえ  | 

## <a name="key-vault"></a>Key Vault
| リソースの種類 | タグのサポート |
| ------------- | ----------- |
| vaults | [はい] | 
| vaults/secrets | いいえ  | 
| vaults/accessPolicies | いいえ  | 
| deletedVaults | いいえ  | 

## <a name="log-analytics"></a>Log Analytics
| リソースの種類 | タグのサポート |
| ------------- | ----------- |
| ログ | いいえ  | 

## <a name="logic"></a>ロジック
| リソースの種類 | タグのサポート |
| ------------- | ----------- |
| workflows | [はい] | 
| integrationAccounts | [はい] | 

## <a name="machine-learning-services"></a>Machine Learning サービス
| リソースの種類 | タグのサポート |
| ------------- | ----------- |
| workspaces | [はい] | 
| workspaces/computes | いいえ  | 

## <a name="managed-identity"></a>マネージド ID
| リソースの種類 | タグのサポート |
| ------------- | ----------- |
| Identities | いいえ  | 
| userAssignedIdentities | [はい] | 

## <a name="mariadb"></a>MariaDB
| リソースの種類 | タグのサポート |
| ------------- | ----------- |
| サーバー | [はい] | 
| servers/recoverableServers | いいえ  | 
| servers/virtualNetworkRules | いいえ  | 

## <a name="marketplace-apps"></a>Marketplace アプリ
| リソースの種類 | タグのサポート |
| ------------- | ----------- |
| classicDevServices | [はい] | 

## <a name="marketplace-ordering"></a>Marketplace 注文
| リソースの種類 | タグのサポート |
| ------------- | ----------- |
| agreements | いいえ  | 
| offertypes | いいえ  | 

## <a name="media"></a>メディア
| リソースの種類 | タグのサポート |
| ------------- | ----------- |
| mediaservices | [はい] | 
| mediaservices/assets | いいえ  | 
| mediaservices/contentKeyPolicies | いいえ  | 
| mediaservices/streamingLocators | いいえ  | 
| mediaservices/streamingPolicies | いいえ  | 
| mediaservices/eventGridFilters | いいえ  | 
| mediaservices/transforms | いいえ  | 
| mediaservices/transforms/jobs | いいえ  | 
| mediaservices/streamingEndpoints | [はい] | 
| mediaservices/liveEvents | [はい] | 
| mediaservices/liveEvents/liveOutputs | いいえ  | 
| mediaservices/streamingEndpointOperations | いいえ  | 
| mediaservices/liveEventOperations | いいえ  | 
| mediaservices/liveOutputOperations | いいえ  | 
| mediaservices/assets/assetFilters | いいえ  | 
| mediaservices/accountFilters | いいえ  | 

## <a name="mysql"></a>MySQL
| リソースの種類 | タグのサポート |
| ------------- | ----------- |
| サーバー | [はい] | 
| servers/recoverableServers | いいえ  | 
| servers/virtualNetworkRules | いいえ  | 

## <a name="network"></a>ネットワーク
| リソースの種類 | タグのサポート |
| ------------- | ----------- |
| virtualNetworks | [はい] | 
| publicIPAddresses | [はい] | 
| networkInterfaces | [はい] | 
| interfaceEndpoints | [はい] | 
| loadBalancers | [はい] | 
| networkSecurityGroups | [はい] | 
| applicationSecurityGroups | [はい] | 
| serviceEndpointPolicies | [はい] | 
| networkIntentPolicies | [はい] | 
| routeTables | [はい] | 
| publicIPPrefixes | [はい] | 
| networkWatchers | [はい] | 
| networkWatchers/connectionMonitors | [はい] | 
| networkWatchers/lenses | [はい] | 
| networkWatchers/pingMeshes | [はい] | 
| virtualNetworkGateways | [はい] | 
| localNetworkGateways | [はい] | 
| connections | [はい] | 
| applicationGateways | [はい] | 
| expressRouteCircuits | [はい] | 
| routeFilters | [はい] | 
| virtualWans | [はい] | 
| vpnSites | [はい] | 
| virtualHubs | [はい] | 
| vpnGateways | [はい] | 
| azureFirewalls | [はい] | 
| virtualNetworkTaps | [はい] | 
| privateLinkServices | [はい] | 
| ddosProtectionPlans | [はい] | 
| networkProfiles | [はい] | 
| frontdoors | [はい] | 
| frontdoorWebApplicationFirewallPolicies | [はい] | 
| webApplicationFirewallPolicies | [はい] | 

## <a name="notification-hubs"></a>Notification Hubs
| リソースの種類 | タグのサポート |
| ------------- | ----------- |
| namespaces | [はい] | 
| namespaces/notificationHubs | [はい] | 

## <a name="portal"></a>ポータル
| リソースの種類 | タグのサポート |
| ------------- | ----------- |
| dashboards | [はい] | 

## <a name="portal-sdk"></a>ポータル SDK
| リソースの種類 | タグのサポート |
| ------------- | ----------- |
| rootResources | [はい] | 

## <a name="postgresql"></a>PostgreSQL
| リソースの種類 | タグのサポート |
| ------------- | ----------- |
| サーバー | [はい] | 
| servers/recoverableServers | いいえ  | 
| servers/virtualNetworkRules | いいえ  | 
| servers/topQueryStatistics | いいえ  | 
| servers/queryTexts | いいえ  | 
| servers/waitStatistics | いいえ  | 
| servers/advisors | いいえ  | 

## <a name="power-bi"></a>Power BI
| リソースの種類 | タグのサポート |
| ------------- | ----------- |
| workspaceCollections | [はい] | 

## <a name="recovery-services"></a>復旧サービス
| リソースの種類 | タグのサポート |
| ------------- | ----------- |
| vaults | [はい] | 
| backupProtectedItems | いいえ  | 

## <a name="relay"></a>リレー
| リソースの種類 | タグのサポート |
| ------------- | ----------- |
| namespaces | [はい] | 

## <a name="resources"></a>リソース
| リソースの種類 | タグのサポート |
| ------------- | ----------- |
| resourceGroups | [はい] | 
| subscriptions/resourceGroups | [はい] | 

## <a name="scheduler"></a>Scheduler
| リソースの種類 | タグのサポート |
| ------------- | ----------- |
| jobcollections | [はい] | 
| フロー | [はい] | 

## <a name="search"></a>Search
| リソースの種類 | タグのサポート |
| ------------- | ----------- |
| searchServices | [はい] | 
| resourceHealthMetadata | いいえ  | 

## <a name="security"></a>セキュリティ
| リソースの種類 | タグのサポート |
| ------------- | ----------- |
| dataCollectionAgents | いいえ  | 

## <a name="service-bus"></a>Service Bus
| リソースの種類 | タグのサポート |
| ------------- | ----------- |
| namespaces | [はい] | 
| namespaces/eventgridfilters | いいえ  | 

## <a name="service-fabric"></a>Service Fabric
| リソースの種類 | タグのサポート |
| ------------- | ----------- |
| クラスター | [はい] | 
| clusters/applications | いいえ  | 

## <a name="service-fabric-mesh"></a>Service Fabric Mesh
| リソースの種類 | タグのサポート |
| ------------- | ----------- |
| applications | [はい] | 
| ネットワーク | [はい] | 
| volumes | [はい] | 

## <a name="signalr-service"></a>SignalR Service
| リソースの種類 | タグのサポート |
| ------------- | ----------- |
| SignalR | [はい] | 

## <a name="site-recovery"></a>Site Recovery
| リソースの種類 | タグのサポート |
| ------------- | ----------- |
| SiteRecoveryVault | [はい] | 

## <a name="solutions"></a>解決方法
| リソースの種類 | タグのサポート |
| ------------- | ----------- |
| applications | [はい] | 
| applicationDefinitions | [はい] | 
| jitRequests | [はい] | 

## <a name="sql-virtual-machine"></a>SQL 仮想マシン
| リソースの種類 | タグのサポート |
| ------------- | ----------- |
| DWVM | [はい] | 

## <a name="storage"></a>Storage
| リソースの種類 | タグのサポート |
| ------------- | ----------- |
| storageAccounts | [はい] | 
| storageAccounts/blobServices | いいえ  | 
| storageAccounts/tableServices | いいえ  | 
| storageAccounts/queueServices | いいえ  | 
| storageAccounts/fileServices | いいえ  | 
| storageAccounts/services | いいえ  | 
| storageAccounts/services/metricDefinitions | いいえ  | 

## <a name="storage-sync"></a>ストレージ同期
| リソースの種類 | タグのサポート |
| ------------- | ----------- |
| storageSyncServices | [はい] | 
| storageSyncServices/syncGroups | いいえ  | 
| storageSyncServices/syncGroups/cloudEndpoints | いいえ  | 
| storageSyncServices/syncGroups/serverEndpoints | いいえ  | 
| storageSyncServices/registeredServers | いいえ  | 
| storageSyncServices/workflows | いいえ  | 

## <a name="storsimple"></a>Storsimple
| リソースの種類 | タグのサポート |
| ------------- | ----------- |
| マネージャー | [はい] | 

## <a name="stream-analytics"></a>Stream Analytics
| リソースの種類 | タグのサポート |
| ------------- | ----------- |
| streamingjobs | [はい] | 
| streamingjobs/diagnosticSettings | いいえ  | 
| streamingjobs/metricDefinitions | いいえ  | 

## <a name="subscription"></a>サブスクリプション
| リソースの種類 | タグのサポート |
| ------------- | ----------- |
| SubscriptionDefinitions | いいえ  | 
| SubscriptionOperations | いいえ  | 

## <a name="support"></a>サポート
| リソースの種類 | タグのサポート |
| ------------- | ----------- |
| supporttickets | いいえ  | 

## <a name="visual-studio"></a>Visual Studio
| リソースの種類 | タグのサポート |
| ------------- | ----------- |
| アカウント | [はい] | 
| アカウント/プロジェクト | [はい] | 
| アカウント/拡張 | [はい] | 
| アカウント | [はい] | 
| アカウント/プロジェクト | [はい] | 
| アカウント/拡張 | [はい] | 

## <a name="web"></a>Web
| リソースの種類 | タグのサポート |
| ------------- | ----------- |
| sites/instances | いいえ  | 
| sites/slots/instances | いいえ  | 
| sites/instances/extensions | いいえ  | 
| sites/slots/instances/extensions | いいえ  | 
| publishingUsers | いいえ  | 
| validate | いいえ  | 
| sourceControls | いいえ  | 
| sites/hostNameBindings | いいえ  | 
| sites/domainOwnershipIdentifiers | いいえ  | 
| sites/slots/hostNameBindings | いいえ  | 
| 証明書 | [はい] | 
| serverFarms | [はい] | 
| serverFarms/workers | いいえ  | 
| sites | [はい] | 
| サイト/スロット | [はい] | 
| sites/metrics | いいえ  | 
| sites/slots/metrics | いいえ  | 
| サイト/premieraddons | [はい] | 
| hostingEnvironments | [はい] | 
| hostingEnvironments/multiRolePools | いいえ  | 
| hostingEnvironments/workerPools | いいえ  | 
| hostingEnvironments/metrics | いいえ  | 
| functions | いいえ  | 
| deletedSites | いいえ  | 
| apiManagementAccounts | いいえ  | 
| apiManagementAccounts/connections | いいえ  | 
| apiManagementAccounts/connectionAcls | いいえ  | 
| apiManagementAccounts/apis/connections/connectionAcls | いいえ  | 
| apiManagementAccounts/apis/connectionAcls | いいえ  | 
| apiManagementAccounts/apiAcls | いいえ  | 
| apiManagementAccounts/apis/apiAcls | いいえ  | 
| apiManagementAccounts/apis | いいえ  | 
| apiManagementAccounts/apis/localizedDefinitions | いいえ  | 
| apiManagementAccounts/apis/connections | いいえ  | 
| connections | [はい] | 
| customApis | [はい] | 
| connectionGateways | [はい] | 
| billingMeters | いいえ  | 
| verifyHostingEnvironmentVnet | いいえ  | 

## <a name="xrm"></a>XRM
| リソースの種類 | タグのサポート |
| ------------- | ----------- |
| organizations | いいえ  | 

## <a name="next-steps"></a>次の手順
リソースにタグを適用する方法については、「[タグを使用した Azure リソースの整理](resource-group-using-tags.md)」をご覧ください。
