---
title: リソースでの Azure Resource Manager タグのサポート
description: タグをサポートしている Azure リソースの種類を示します。 すべての Azure サービスの詳細を提供します。
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: reference
ms.date: 01/02/2019
ms.author: tomfitz
ms.openlocfilehash: 50ea7a2446b5560bd208b2da128fa877068ce452
ms.sourcegitcommit: da69285e86d23c471838b5242d4bdca512e73853
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/03/2019
ms.locfileid: "54000293"
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
| addsservices | いいえ  |
| aadsupportcases | いいえ  | 
| agents | いいえ  | 
| anonymousapiusers | いいえ  | 
| 構成 | いいえ  | 
| ログ | いいえ  | 
| reports | いいえ  | 
| services | いいえ  | 
| servicehealthmetrics | いいえ  | 

## <a name="aks"></a>AKS
| リソースの種類 | タグのサポート |
| ------------- | ----------- |
| managedClusters | [はい] | 

## <a name="analysis-services"></a>Analysis Services
| リソースの種類 | タグのサポート |
| ------------- | ----------- |
| サーバー | はい | 

## <a name="api-hubs"></a>API ハブ
| リソースの種類 | タグのサポート |
| ------------- | ----------- |
| apiManagementAccounts | いいえ  | 
| apiManagementAccounts/apis | いいえ  | 
| apiManagementAccounts/connectionAcls | いいえ  | 
| apiManagementAccounts/connectionProviders | いいえ  | 
| apiManagementAccounts/connectionProviderAcls | いいえ  | 
| apiManagementAccounts/connections | いいえ  | 

## <a name="api-management"></a>API Management
| リソースの種類 | タグのサポート |
| ------------- | ----------- |
| service | はい | 

## <a name="automation"></a>Automation
| リソースの種類 | タグのサポート |
| ------------- | ----------- |
| automationAccounts | [はい] | 
| automationAccounts/configurations | [はい] | 
| automationAccounts/jobs | いいえ  | 
| automationAccounts/runbooks | [はい] | 
| automationAccounts/softwareUpdateConfigurations | いいえ  | 
| automationAccounts/webhooks | いいえ  | 

## <a name="azure-database-for-mariadb"></a>Azure Database for MariaDB
| リソースの種類 | タグのサポート |
| ------------- | ----------- |
| サーバー | [はい] | 
| servers/configurations | いいえ  |
| サーバ/データベース | いいえ  |
| servers/firewallRules | いいえ  |
| servers/recoverableServers | いいえ  | 
| servers/securityAlertPolicies | いいえ  |
| servers/virtualNetworkRules | いいえ  | 

## <a name="azure-database-for-mysql"></a>Azure Database for MySQL
| リソースの種類 | タグのサポート |
| ------------- | ----------- |
| サーバー | [はい] | 
| servers/configurations | いいえ  |
| サーバ/データベース | いいえ  |
| servers/firewallRules | いいえ  |
| servers/recoverableServers | いいえ  | 
| servers/securityAlertPolicies | いいえ  |
| servers/virtualNetworkRules | いいえ  | 

## <a name="azure-database-for-postgresql"></a>Azure Database for PostgreSQL
| リソースの種類 | タグのサポート |
| ------------- | ----------- |
| サーバー | [はい] | 
| servers/advisors | いいえ  | 
| servers/configurations | いいえ  |
| サーバ/データベース | いいえ  |
| servers/firewallRules | いいえ  |
| servers/queryTexts | いいえ  | 
| servers/recoverableServers | いいえ  | 
| servers/securityAlertPolicies | いいえ  |
| servers/topQueryStatistics | いいえ  | 
| servers/virtualNetworkRules | いいえ  | 
| servers/waitStatistics | いいえ  | 

## <a name="batch"></a>Batch
| リソースの種類 | タグのサポート |
| ------------- | ----------- |
| batchAccounts | はい | 

## <a name="bing-maps"></a>Bing Maps
| リソースの種類 | タグのサポート |
| ------------- | ----------- |
| mapApis | はい | 

## <a name="biztalk-services"></a>BizTalk Services
| リソースの種類 | タグのサポート |
| ------------- | ----------- |
| BizTalk | はい | 

## <a name="cache"></a>キャッシュ
| リソースの種類 | タグのサポート |
| ------------- | ----------- |
| Redis | はい | 

## <a name="cdn"></a>CDN
| リソースの種類 | タグのサポート |
| ------------- | ----------- |
| edgenodes | いいえ  | 
| profiles | はい | 
| profiles/endpoints | [はい] | 
| profiles/endpoints/customdomains | いいえ  | 
| profiles/endpoints/origins | いいえ  | 
| validateProbe | いいえ  | 

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
| virtualNetworks/remoteVirtualNetworkPeeringProxies | いいえ  | 
| virtualNetworks/virtualNetworkPeerings | いいえ  | 

## <a name="classic-storage"></a>従来のストレージ
| リソースの種類 | タグのサポート |
| ------------- | ----------- |
| storageAccounts/services | いいえ  | 
| storageAccounts/services/diagnosticSettings | いいえ  | 

## <a name="compute"></a>Compute
| リソースの種類 | タグのサポート |
| ------------- | ----------- |
| availabilitySets | [はい] | 
| ディスク | [はい] | 
| images | [はい] | 
| restorePointCollections | はい | 
| restorePointCollections/restorePoints | いいえ  | 
| sharedVMImages | はい | 
| sharedVMImages/versions | [はい] | 
| スナップショット | [はい] | 
| virtualMachines | [はい] | 
| virtualMachines/diagnosticSettings | いいえ  | 
| virtualMachines/extensions | [はい] | 
| virtualMachines/metricDefinitions | いいえ  | 
| virtualMachineScaleSets | はい | 
| virtualMachineScaleSets/extensions | いいえ  | 
| virtualMachineScaleSets/networkInterfaces | いいえ  | 
| virtualMachineScaleSets/publicIPAddresses | いいえ  | 
| virtualMachineScaleSets/virtualMachines | いいえ  | 
| virtualMachineScaleSets/virtualMachines/networkInterfaces | いいえ  | 

## <a name="container"></a>コンテナー
| リソースの種類 | タグのサポート |
| ------------- | ----------- |
| containerGroups | はい | 

## <a name="container-instance"></a>コンテナー インスタンス
| リソースの種類 | タグのサポート |
| ------------- | ----------- |
| containerGroups | はい | 
| serviceAssociationLinks | いいえ  | 

## <a name="container-registry"></a>Container Registry
| リソースの種類 | タグのサポート |
| ------------- | ----------- |
| レジストリ | [はい] | 
| registries/replications | [はい] |
| registries/tasks | [はい] |
| registries/webhooks | [はい] |

## <a name="container-service"></a>Container Service
| リソースの種類 | タグのサポート |
| ------------- | ----------- |
| containerServices | はい | 

## <a name="cortana-analytics"></a>Cortana Analytics
| リソースの種類 | タグのサポート |
| ------------- | ----------- |
| accounts | はい | 

## <a name="cosmos-db"></a>Cosmos DB
| リソースの種類 | タグのサポート |
| ------------- | ----------- |
| databaseAccounts | はい | 
| databaseAccountNames | いいえ  | 

## <a name="cost-management"></a>Cost Management
| リソースの種類 | タグのサポート |
| ------------- | ----------- |
| コネクタ | [はい] | 

## <a name="data-box"></a>Data Box
| リソースの種類 | タグのサポート |
| ------------- | ----------- |
| ジョブ | [はい] | 

## <a name="data-box-edge"></a>Data Box Edge
| リソースの種類 | タグのサポート |
| ------------- | ----------- |
| DataBoxEdgeDevices | はい | 

## <a name="data-catalog"></a>Data Catalog
| リソースの種類 | タグのサポート |
| ------------- | ----------- |
| カタログ | はい | 

## <a name="data-connect"></a>データ接続
| リソースの種類 | タグのサポート |
| ------------- | ----------- |
| connectionManagers | はい | 

## <a name="data-factory"></a>Data Factory
| リソースの種類 | タグのサポート |
| ------------- | ----------- |
| dataFactories | [はい] | 
| dataFactories/diagnosticSettings | いいえ  | 
| dataFactories/metricDefinitions | いいえ  | 
| dataFactorySchema | いいえ  | 
| ファクトリ | はい | 
| factories/integrationRuntimes | いいえ  | 

## <a name="devices"></a>デバイス
| リソースの種類 | タグのサポート |
| ------------- | ----------- |
| IotHubs | はい | 
| IotHubs/eventGridFilters | いいえ  | 
| ProvisioningServices | はい | 

## <a name="devspaces"></a>Devspaces
| リソースの種類 | タグのサポート |
| ------------- | ----------- |
| controllers | はい | 

## <a name="devtest-lab"></a>Devtest ラボ
| リソースの種類 | タグのサポート |
| ------------- | ----------- |
| labs | [はい] | 
| labs/artifactsources | [はい] |
| labs/costs | [はい] |
| labs/customimages | [はい] |
| labs/formulas | [はい] |
| labs/notificationchannels | [はい] |
| labs/policysets/policies | [はい] |
| labs/schedules | [はい] |
| labs/serviceRunners | [はい] | 
| labs/users | [はい] |
| labs/users/disks | [はい] |
| labs/users/environments | [はい] |
| labs/users/secrets | [はい] |
| labs/users/servicefabrics | [はい] |
| labs/users/servicefabrics/schedules | [はい] |
| labs/virtualMachines | [はい] | 
| labs/virtualmachines/schedules | [はい] |
| labs/virtualnetworks | [はい] |
| schedules | [はい] | 

## <a name="dynamics-lcs"></a>Dynamics LCS
| リソースの種類 | タグのサポート |
| ------------- | ----------- |
| lcsprojects | いいえ  | 
| lcsprojects/connectors | いいえ  | 
| lcsprojects/clouddeployments | いいえ  | 

## <a name="event-grid"></a>Event Grid
| リソースの種類 | タグのサポート |
| ------------- | ----------- |
| domains | はい | 
| domains/topics | いいえ  | 
| eventSubscriptions | いいえ  | 
| extensionTopics | いいえ  | 
| topics | [はい] | 
| topicTypes | いいえ  | 

## <a name="event-hub"></a>イベント ハブ
| リソースの種類 | タグのサポート |
| ------------- | ----------- |
| クラスター | [はい] | 
| namespaces | [はい] | 
| namespaces/AuthorizationRules | いいえ  |
| namespaces/disasterRecoveryConfigs | いいえ  |
| namespaces/eventhubs | いいえ  |
| namespaces/eventhubs/authorizationRules | いいえ  |
| namespaces/eventhubs/consumergroups | いいえ  |

## <a name="hana-on-azure"></a>HANA on Azure
| リソースの種類 | タグのサポート |
| ------------- | ----------- |
| hanaInstances | はい | 

## <a name="hdinsight"></a>HDInsight
| リソースの種類 | タグのサポート |
| ------------- | ----------- |
| クラスター | はい | 
| clusters/applications | いいえ  | 

## <a name="import-export"></a>Import Export
| リソースの種類 | タグのサポート |
| ------------- | ----------- |
| ジョブ | はい | 

## <a name="insights"></a>洞察
| リソースの種類 | タグのサポート |
| ------------- | ----------- |
| actionGroups | [はい] |
| activityLogAlerts | [はい] |
| alertrules | [はい] |
| automatedExportSettings | いいえ  | 
| components | [はい] | 
| components/events | いいえ  | 
| components/metrics | いいえ  | 
| components/pricingPlans | いいえ  | 
| components/query | いいえ  | 
| ログ | いいえ  | 
| metricAlerts | [はい] |
| migrateToNewPricingModel | いいえ  | 
| myWorkbooks | いいえ  | 
| クエリ | いいえ  | 
| rollbackToLegacyPricingModel | いいえ  | 
| scheduledqueryrules | [はい] | 
| webtests | [はい] | 
| Workbooks | [はい] | 

## <a name="key-vault"></a>Key Vault
| リソースの種類 | タグのサポート |
| ------------- | ----------- |
| deletedVaults | いいえ  | 
| vaults | [はい] | 
| vaults/accessPolicies | いいえ  | 
| vaults/secrets | いいえ  | 

## <a name="log-analytics"></a>Log Analytics
| リソースの種類 | タグのサポート |
| ------------- | ----------- |
| ログ | いいえ  | 

## <a name="logic"></a>ロジック
| リソースの種類 | タグのサポート |
| ------------- | ----------- |
| integrationAccounts | [はい] | 
| workflows | [はい] | 

## <a name="machine-learning-services"></a>Machine Learning サービス
| リソースの種類 | タグのサポート |
| ------------- | ----------- |
| workspaces | はい | 
| workspaces/computes | いいえ  | 

## <a name="managed-identity"></a>マネージド ID
| リソースの種類 | タグのサポート |
| ------------- | ----------- |
| Identities | いいえ  | 
| userAssignedIdentities | [はい] | 

## <a name="marketplace-apps"></a>Marketplace アプリ
| リソースの種類 | タグのサポート |
| ------------- | ----------- |
| classicDevServices | はい | 

## <a name="marketplace-ordering"></a>Marketplace 注文
| リソースの種類 | タグのサポート |
| ------------- | ----------- |
| agreements | いいえ  | 
| offertypes | いいえ  | 

## <a name="media"></a>メディア
| リソースの種類 | タグのサポート |
| ------------- | ----------- |
| mediaservices | [はい] | 
| mediaservices/accountFilters | いいえ  | 
| mediaservices/assets | いいえ  | 
| mediaservices/assets/assetFilters | いいえ  | 
| mediaservices/contentKeyPolicies | いいえ  | 
| mediaservices/eventGridFilters | いいえ  | 
| mediaservices/liveEventOperations | いいえ  | 
| mediaservices/liveEvents | はい | 
| mediaservices/liveEvents/liveOutputs | いいえ  | 
| mediaservices/liveOutputOperations | いいえ  | 
| mediaservices/streamingEndpoints | [はい] | 
| mediaservices/streamingEndpointOperations | いいえ  | 
| mediaservices/streamingLocators | いいえ  | 
| mediaservices/streamingPolicies | いいえ  | 
| mediaservices/transforms | いいえ  | 
| mediaservices/transforms/jobs | いいえ  | 

## <a name="network"></a>ネットワーク
| リソースの種類 | タグのサポート |
| ------------- | ----------- |
| applicationGateways | [はい] | 
| applicationSecurityGroups | [はい] | 
| azureFirewalls | [はい] | 
| connections | [はい] | 
| ddosProtectionPlans | [はい] | 
| expressRouteCircuits | [はい] | 
| frontdoors | はい | 
| frontdoorWebApplicationFirewallPolicies | [はい] | 
| interfaceEndpoints | はい | 
| loadBalancers | [はい] | 
| localNetworkGateways | [はい] | 
| networkIntentPolicies | [はい] | 
| networkInterfaces | [はい] | 
| networkProfiles | [はい] | 
| networkSecurityGroups | [はい] | 
| networkWatchers | はい | 
| networkWatchers/connectionMonitors | はい | 
| networkWatchers/lenses | はい | 
| networkWatchers/pingMeshes | [はい] | 
| privateLinkServices | [はい] | 
| publicIPAddresses | [はい] | 
| publicIPPrefixes | [はい] | 
| routeFilters | [はい] | 
| routeTables | [はい] | 
| serviceEndpointPolicies | [はい] | 
| virtualHubs | [はい] | 
| virtualNetworks | [はい] | 
| virtualNetworkGateways | [はい] | 
| virtualNetworkTaps | [はい] | 
| virtualWans | [はい] | 
| vpnGateways | [はい] | 
| vpnSites | [はい] | 
| webApplicationFirewallPolicies | はい | 

## <a name="notification-hubs"></a>Notification Hubs
| リソースの種類 | タグのサポート |
| ------------- | ----------- |
| namespaces | はい | 
| namespaces/notificationHubs | [はい] | 

## <a name="operational-insights"></a>Operational Insights
| リソースの種類 | タグのサポート |
| ------------- | ----------- |
| workspaces | [はい] |
| workspaces/dataSources | [はい] |
| workspaces/linkedServices | [はい] |
| workspaces/savedSearches | いいえ  |
| workspaces/storageInsightConfigs | [はい] |

## <a name="operations-management"></a>Operations Management
| リソースの種類 | タグのサポート |
| ------------- | ----------- |
| solutions | いいえ  |

## <a name="portal"></a>ポータル
| リソースの種類 | タグのサポート |
| ------------- | ----------- |
| dashboards | はい | 

## <a name="portal-sdk"></a>ポータル SDK
| リソースの種類 | タグのサポート |
| ------------- | ----------- |
| rootResources | [はい] | 

## <a name="power-bi"></a>Power BI
| リソースの種類 | タグのサポート |
| ------------- | ----------- |
| workspaceCollections | はい | 

## <a name="recovery-services"></a>復旧サービス
| リソースの種類 | タグのサポート |
| ------------- | ----------- |
| backupProtectedItems | いいえ  | 
| vaults | [はい] | 

## <a name="relay"></a>リレー
| リソースの種類 | タグのサポート |
| ------------- | ----------- |
| namespaces | はい | 

## <a name="resources"></a>リソース
| リソースの種類 | タグのサポート |
| ------------- | ----------- |
| resourceGroups | はい | 
| subscriptions/resourceGroups | はい | 

## <a name="scheduler"></a>Scheduler
| リソースの種類 | タグのサポート |
| ------------- | ----------- |
| jobcollections | はい | 
| フロー | はい | 

## <a name="search"></a>Search
| リソースの種類 | タグのサポート |
| ------------- | ----------- |
| resourceHealthMetadata | いいえ  | 
| searchServices | [はい] | 

## <a name="security"></a>セキュリティ
| リソースの種類 | タグのサポート |
| ------------- | ----------- |
| dataCollectionAgents | いいえ  | 

## <a name="service-bus"></a>Service Bus
| リソースの種類 | タグのサポート |
| ------------- | ----------- |
| namespaces | はい | 
| namespaces/eventgridfilters | いいえ  | 

## <a name="service-fabric"></a>Service Fabric
| リソースの種類 | タグのサポート |
| ------------- | ----------- |
| クラスター | はい | 
| clusters/applications | いいえ  | 

## <a name="service-fabric-mesh"></a>Service Fabric Mesh
| リソースの種類 | タグのサポート |
| ------------- | ----------- |
| applications | はい | 
| ネットワーク | はい | 
| volumes | はい | 

## <a name="signalr-service"></a>SignalR Service
| リソースの種類 | タグのサポート |
| ------------- | ----------- |
| SignalR | はい | 

## <a name="site-recovery"></a>Site Recovery
| リソースの種類 | タグのサポート |
| ------------- | ----------- |
| SiteRecoveryVault | はい | 

## <a name="solutions"></a>解決方法
| リソースの種類 | タグのサポート |
| ------------- | ----------- |
| applications | はい | 
| applicationDefinitions | はい | 
| jitRequests | [はい] | 

## <a name="sql"></a>SQL
| リソースの種類 | タグのサポート |
| ------------- | ----------- |
| locations/instanceFailoverGroups | いいえ  |
| managedInstances | [はい] |
| managedInstances/databases | [はい] |
| managedInstances/databases/backupShortTermRetentionPolicies | いいえ  |
| managedInstances/databases/schemas/tables/columns/sensitivityLabels | いいえ  |
| managedInstances/databases/vulnerabilityAssessments | いいえ  |
| managedInstances/databases/vulnerabilityAssessments/rules/baselines | いいえ  |
| managedInstances/encryptionProtector | いいえ  |
| managedInstances/keys | いいえ  |
| managedInstances/restorableDroppedDatabases/backupShortTermRetentionPolicies | いいえ  |
| managedInstances/vulnerabilityAssessments | いいえ  |
| サーバー | [はい] |
| servers/administrators | いいえ  |
| servers/advisors | いいえ  |
| servers/auditingSettings | いいえ  |
| servers/backupLongTermRetentionVaults | いいえ  |
| servers/communicationLinks | いいえ  |
| servers/connectionPolicies | いいえ  |
| サーバ/データベース | [はい] |
| servers/databases/advisors | いいえ  |
| servers/databases/auditingSettings | いいえ  |
| servers/databases/backupLongTermRetentionPolicies | いいえ  |
| servers/databases/backupShortTermRetentionPolicies | いいえ  |
| servers/databases/connectionPolicies | いいえ  |
| servers/databases/dataMaskingPolicies | いいえ  |
| servers/databases/dataMaskingPolicies/rules | いいえ  |
| servers/databases/extendedAuditingSettings | いいえ  |
| servers/databases/extensions | いいえ  |
| servers/databases/geoBackupPolicies | いいえ  |
| servers/databases/schemas/tables/columns/sensitivityLabels | いいえ  |
| servers/databases/securityAlertPolicies | いいえ  |
| servers/databases/syncGroups | いいえ  |
| servers/databases/syncGroups/syncMembers | いいえ  |
| servers/databases/transparentDataEncryption | いいえ  |
| servers/databases/vulnerabilityAssessments | いいえ  |
| servers/databases/vulnerabilityAssessments/rules/baselines | いいえ  |
| servers/disasterRecoveryConfiguration | いいえ  |
| servers/dnsAliases | いいえ  |
| servers/elasticPools | [はい] |
| servers/encryptionProtector | いいえ  |
| servers/extendedAuditingSettings | いいえ  |
| servers/failoverGroups | [はい] |
| servers/firewallRules | いいえ  |
| servers/jobAgents | [はい] |
| servers/jobAgents/credentials | いいえ  |
| servers/jobAgents/jobs | いいえ  |
| servers/jobAgents/jobs/executions | いいえ  |
| servers/jobAgents/jobs/steps | いいえ  |
| servers/jobAgents/targetGroups | いいえ  |
| servers/keys | いいえ  |
| servers/securityAlertPolicies | いいえ  |
| servers/syncAgents | いいえ  |
| servers/virtualNetworkRules | いいえ  |
| servers/vulnerabilityAssessments | いいえ  |

## <a name="sql-virtual-machine"></a>SQL 仮想マシン
| リソースの種類 | タグのサポート |
| ------------- | ----------- |
| DWVM | はい | 

## <a name="storage"></a>Storage
| リソースの種類 | タグのサポート |
| ------------- | ----------- |
| storageAccounts | はい | 
| storageAccounts/blobServices | いいえ  | 
| storageAccounts/fileServices | いいえ  | 
| storageAccounts/queueServices | いいえ  | 
| storageAccounts/services | いいえ  | 
| storageAccounts/services/metricDefinitions | いいえ  | 
| storageAccounts/tableServices | いいえ  | 

## <a name="storage-sync"></a>ストレージ同期
| リソースの種類 | タグのサポート |
| ------------- | ----------- |
| storageSyncServices | [はい] | 
| storageSyncServices/registeredServers | いいえ  | 
| storageSyncServices/syncGroups | いいえ  | 
| storageSyncServices/syncGroups/cloudEndpoints | いいえ  | 
| storageSyncServices/syncGroups/serverEndpoints | いいえ  | 
| storageSyncServices/workflows | いいえ  | 

## <a name="storsimple"></a>Storsimple
| リソースの種類 | タグのサポート |
| ------------- | ----------- |
| マネージャー | はい | 

## <a name="stream-analytics"></a>Stream Analytics
| リソースの種類 | タグのサポート |
| ------------- | ----------- |
| streamingjobs | はい | 
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
| アカウント/拡張 | [はい] | 
| アカウント/プロジェクト | [はい] | 

## <a name="web"></a>Web
| リソースの種類 | タグのサポート |
| ------------- | ----------- |
| apiManagementAccounts | いいえ  | 
| apiManagementAccounts/apiAcls | いいえ  | 
| apiManagementAccounts/apis | いいえ  | 
| apiManagementAccounts/apis/apiAcls | いいえ  | 
| apiManagementAccounts/apis/connectionAcls | いいえ  | 
| apiManagementAccounts/apis/connections | いいえ  | 
| apiManagementAccounts/apis/connections/connectionAcls | いいえ  | 
| apiManagementAccounts/apis/localizedDefinitions | いいえ  | 
| apiManagementAccounts/connectionAcls | いいえ  | 
| apiManagementAccounts/connections | いいえ  | 
| billingMeters | いいえ  | 
| 証明書 | [はい] | 
| connectionGateways | [はい] | 
| connections | はい | 
| customApis | [はい] | 
| deletedSites | いいえ  | 
| functions | いいえ  | 
| hostingEnvironments | [はい] | 
| hostingEnvironments/metrics | いいえ  | 
| hostingEnvironments/multiRolePools | いいえ  | 
| hostingEnvironments/workerPools | いいえ  | 
| publishingUsers | いいえ  | 
| serverFarms | はい | 
| serverFarms/workers | いいえ  | 
| sites | [はい] | 
| sites/domainOwnershipIdentifiers | いいえ  | 
| sites/hostNameBindings | いいえ  | 
| sites/instances | いいえ  | 
| sites/instances/extensions | いいえ  | 
| sites/metrics | いいえ  | 
| サイト/premieraddons | [はい] | 
| サイト/スロット | はい | 
| sites/slots/hostNameBindings | いいえ  | 
| sites/slots/instances | いいえ  | 
| sites/slots/instances/extensions | いいえ  | 
| sites/slots/metrics | いいえ  | 
| sourceControls | いいえ  | 
| validate | いいえ  | 
| verifyHostingEnvironmentVnet | いいえ  | 

## <a name="xrm"></a>XRM
| リソースの種類 | タグのサポート |
| ------------- | ----------- |
| organizations | いいえ  | 

## <a name="next-steps"></a>次の手順
リソースにタグを適用する方法については、「[タグを使用した Azure リソースの整理](resource-group-using-tags.md)」をご覧ください。
