---
title: Azure リソースの種類での操作のサポートの移動 |Microsoft Docs
description: 新しいリソース グループまたはサブスクリプションに移動できる Azure リソースの種類を一覧表示します。
services: azure-resource-manager
documentationcenter: ''
author: tfitzmac
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 09/28/2018
ms.author: tomfitz
ms.openlocfilehash: 253cfd7ddeb04a12a3609ab5e14a37365015a568
ms.sourcegitcommit: 295babdcfe86b7a3074fd5b65350c8c11a49f2f1
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/27/2018
ms.locfileid: "53790879"
---
# <a name="move-operation-support-for-resources"></a>リソースの操作のサポートの移動

この記事では、Azure リソースの種類は、移動操作をサポートしているかどうかを示します。 リソースの種類には、移動操作がサポートされていても、リソースが移動されることを妨げている条件がある可能性があります。 移動操作に影響する条件の詳細については、[新しいリソース グループまたはサブスクリプションへリソースを移動](resource-group-move-resources.md)を参照してください。

## <a name="find-resource-provider-and-resource-type"></a>リソース プロバイダーとリソースの種類を検索します。

リソースを移動できるかどうかを判断するには、そのリソース プロバイダーとリソースの種類を検索する必要があります。

PowerShell では、次を使用します。

```azurepowershell-interactive
Get-AzureRmResource -ResourceGroupName demogroup | Select Name, ResourceType | Format-table
```

Azure CLI では、次を使用します。

```azurecli-interactive
az resource list -g demogroup --query '[].{name:name, resourceType:type}' --output table
```

リソースの種類が形式で返されます`<resource-provider>/<resource-type-name>`。 それでは、値`Microsoft.OperationalInsights/workspaces`のリソース プロバイダーが**Microsoft.OperationalInsights**であり、そのリソースの種類名は**ワークスペース**です。

リソース プロバイダーとリソースの種類を検索した後、この記事でのテーブルを使用して、リソースの種類が、移動操作をサポートするかどうかを確認します。

## <a name="microsoftaad"></a>Microsoft.AAD

| リソースの種類 | リソース グループ | サブスクリプション |
| ------------- | --------------- | ----------- |
| domainservices | いいえ  | いいえ  |

## <a name="microsoftanalysisservices"></a>Microsoft.AnalysisServices
| リソースの種類 | リソース グループ | サブスクリプション |
| ------------- | -------------- | ------------ |
| サーバー | はい | はい |

## <a name="microsoftapimanagement"></a>Microsoft.ApiManagement
| リソースの種類 | リソース グループ | サブスクリプション |
| ------------- | --------------- | ----------- |
| service | はい | はい |

## <a name="microsoftauthorization"></a>Microsoft.Authorization
| リソースの種類 | リソース グループ | サブスクリプション |
| ------------- | --------------- | ----------- |
| policyassignments | いいえ  | いいえ  |

## <a name="microsoftautomation"></a>Microsoft.Automation
| リソースの種類 | リソース グループ | サブスクリプション |
| ------------- | -------------- | ------------ |
| automationaccounts | はい | はい |
| automationaccounts/configurations | はい | はい |
| automationaccounts/runbooks | はい | はい |

## <a name="microsoftazureactivedirectory"></a>Microsoft.AzureActiveDirectory
| リソースの種類 | リソース グループ | サブスクリプション |
| ------------- | -------------- | ------------ |
| b2cdirectories | はい | はい |

## <a name="microsoftazurestack"></a>Microsoft.AzureStack
| リソースの種類 | リソース グループ | サブスクリプション |
| ------------- | -------------- | ------------ |
| 登録 | はい | はい |

## <a name="microsoftbackup"></a>Microsoft.Backup
| リソースの種類 | リソース グループ | サブスクリプション |
| ------------- | -------------- | ------------ |
| backupvault | いいえ  | いいえ  |

## <a name="microsoftbatch"></a>Microsoft.Batch
| リソースの種類 | リソース グループ | サブスクリプション |
| ------------- | -------------- | ------------ |
| batchaccounts | はい | はい |

## <a name="microsoftbingmaps"></a>Microsoft.BingMaps
| リソースの種類 | リソース グループ | サブスクリプション |
| ------------- | -------------- | ------------ |
| mapapis | いいえ  | いいえ  |

## <a name="microsoftbiztalkservices"></a>Microsoft.BizTalkServices
| リソースの種類 | リソース グループ | サブスクリプション |
| ------------- | -------------- | ------------ |
| biztalk | はい | はい |

## <a name="microsoftblueprint"></a>Microsoft.Blueprint
| リソースの種類 | リソース グループ | サブスクリプション |
| ------------- | -------------- | ------------ |
| blueprintassignments | いいえ  | いいえ  |

## <a name="microsoftbotservice"></a>Microsoft.BotService
| リソースの種類 | リソース グループ | サブスクリプション |
| ------------- | -------------- | ------------ |
| botservices | はい | はい |

## <a name="microsoftcache"></a>Microsoft.Cache
| リソースの種類 | リソース グループ | サブスクリプション |
| ------------- | -------------- | ------------ |
| redis | はい | はい |

## <a name="microsoftcdn"></a>Microsoft.Cdn
| リソースの種類 | リソース グループ | サブスクリプション |
| ------------- | -------------- | ------------ |
| profiles | はい | はい |
| profiles/endpoints | はい | はい |

## <a name="microsoftcertificateregistration"></a>Microsoft.CertificateRegistration
| リソースの種類 | リソース グループ | サブスクリプション |
| ------------- | -------------- | ------------ |
| certificateorders | はい | はい |

## <a name="microsoftclassiccompute"></a>Microsoft.ClassicCompute
| リソースの種類 | リソース グループ | サブスクリプション |
| ------------- | -------------- | ------------ |
| domainnames | はい | いいえ  |
| virtualmachines | はい | いいえ  |

## <a name="microsoftclassicnetwork"></a>Microsoft.ClassicNetwork
| リソースの種類 | リソース グループ | サブスクリプション |
| ------------- | -------------- | ------------ |
| networksecuritygroups | いいえ  | いいえ  |
| reservedips | いいえ  | いいえ  |
| virtualnetworks | いいえ  | いいえ  |

## <a name="microsoftclassicstorage"></a>Microsoft.ClassicStorage
| リソースの種類 | リソース グループ | サブスクリプション |
| ------------- | -------------- | ------------ |
| storageaccounts | はい | いいえ  |

## <a name="microsoftcognitiveservices"></a>Microsoft.CognitiveServices
| リソースの種類 | リソース グループ | サブスクリプション |
| ------------- | -------------- | ------------ |
| accounts | はい | はい |

## <a name="microsoftcompute"></a>Microsoft.Compute
| リソースの種類 | リソース グループ | サブスクリプション |
| ------------- | -------------- | ------------ |
| availabilitysets | はい | はい |
| ディスク | はい | はい |
| ギャラリー | いいえ  | いいえ  |
| ギャラリー/イメージ | いいえ  | いいえ  |
| ギャラリー/イメージ/バージョン | いいえ  | いいえ  |
| images | はい | はい |
| restorepointcollections | いいえ  | いいえ  |
| sharedvmimages | いいえ  | いいえ  |
| sharedvmimages/versions | いいえ  | いいえ  |
| スナップショット | はい | はい |
| virtualmachines | はい | はい |
| virtualmachines/extensions | はい | はい |
| virtualmachinescalesets | はい | はい |

## <a name="microsoftcontainer"></a>Microsoft.Container
| リソースの種類 | リソース グループ | サブスクリプション |
| ------------- | -------------- | ------------ |
| containergroups | いいえ  | いいえ  |

## <a name="microsoftcontainerinstance"></a>Microsoft.ContainerInstance
| リソースの種類 | リソース グループ | サブスクリプション |
| ------------- | -------------- | ------------ |
| containergroups | いいえ  | いいえ  |

## <a name="microsoftcontainerregistry"></a>Microsoft.ContainerRegistry
| リソースの種類 | リソース グループ | サブスクリプション |
| ------------- | -------------- | ------------ |
| レジストリ | はい | はい |
| registries/buildtasks | はい | はい |
| registries/replications | いいえ  | いいえ  |
| registries/tasks | はい | はい |
| registries/webhooks | はい | はい |

## <a name="microsoftcontainerservice"></a>Microsoft.ContainerService
| リソースの種類 | リソース グループ | サブスクリプション |
| ------------- | -------------- | ------------ |
| containerservices | いいえ  | いいえ  |
| managedclusters | いいえ  | いいえ  |
| openshiftmanagedclusters | いいえ  | いいえ  |

## <a name="microsoftcontentmoderator"></a>Microsoft.ContentModerator
| リソースの種類 | リソース グループ | サブスクリプション |
| ------------- | -------------- | ------------ |
| applications | はい | はい |

## <a name="microsoftcostmanagement"></a>Microsoft.CostManagement
| リソースの種類 | リソース グループ | サブスクリプション |
| ------------- | -------------- | ------------ |
| コネクタ | はい | はい |

## <a name="microsoftcustomerinsights"></a>Microsoft.CustomerInsights
| リソースの種類 | リソース グループ | サブスクリプション |
| ------------- | -------------- | ------------ |
| ハブ | はい | はい |

## <a name="microsoftdatabox"></a>Microsoft.DataBox
| リソースの種類 | リソース グループ | サブスクリプション |
| ------------- | -------------- | ------------ |
| ジョブ | いいえ  | いいえ  |

## <a name="microsoftdataboxedge"></a>Microsoft.DataBoxEdge
| リソースの種類 | リソース グループ | サブスクリプション |
| ------------- | -------------- | ------------ |
| databoxedgedevices | いいえ  | いいえ  |

## <a name="microsoftdatabricks"></a>Microsoft.Databricks
| リソースの種類 | リソース グループ | サブスクリプション |
| ------------- | -------------- | ------------ |
| workspaces | いいえ  | いいえ  |

## <a name="microsoftdatacatalog"></a>Microsoft.DataCatalog
| リソースの種類 | リソース グループ | サブスクリプション |
| ------------- | -------------- | ------------ |
| カタログ | はい | はい |

## <a name="microsoftdatafactory"></a>Microsoft.DataFactory
| リソースの種類 | リソース グループ | サブスクリプション |
| ------------- | -------------- | ------------ |
| datafactories | はい | はい |
| ファクトリ | はい | はい |

## <a name="microsoftdatalake"></a>Microsoft.DataLake
| リソースの種類 | リソース グループ | サブスクリプション |
| ------------- | -------------- | ------------ |
| datalakeaccounts | いいえ  | いいえ  |

## <a name="microsoftdatalakeanalytics"></a>Microsoft.DataLakeAnalytics
| リソースの種類 | リソース グループ | サブスクリプション |
| ------------- | -------------- | ------------ |
| accounts | はい | はい |

## <a name="microsoftdatalakestore"></a>Microsoft.DataLakeStore
| リソースの種類 | リソース グループ | サブスクリプション |
| ------------- | -------------- | ------------ |
| accounts | はい | はい |

## <a name="microsoftdatamigration"></a>Microsoft.DataMigration
| リソースの種類 | リソース グループ | サブスクリプション |
| ------------- | -------------- | ------------ |
| services | いいえ  | いいえ  |
| サービス/プロジェクト | いいえ  | いいえ  |
| slots | いいえ  | いいえ  |

## <a name="microsoftdbformariadb"></a>Microsoft.DBforMariaDB
| リソースの種類 | リソース グループ | サブスクリプション |
| ------------- | -------------- | ------------ |
| サーバー | いいえ  | いいえ  |

## <a name="microsoftdbformysql"></a>Microsoft.DBforMySQL
| リソースの種類 | リソース グループ | サブスクリプション |
| ------------- | -------------- | ------------ |
| サーバー | はい | はい |

## <a name="microsoftdbforpostgresql"></a>Microsoft.DBforPostgreSQL
| リソースの種類 | リソース グループ | サブスクリプション |
| ------------- | -------------- | ------------ |
| servergroups | いいえ  | いいえ  |
| サーバー | はい | はい |

## <a name="microsoftdeploymentmanager"></a>Microsoft.DeploymentManager
| リソースの種類 | リソース グループ | サブスクリプション |
| ------------- | -------------- | ------------ |
| artifactsources | いいえ  | いいえ  |
| ロールアウト | いいえ  | いいえ  |
| servicetopologies | いいえ  | いいえ  |
| servicetopologies/services | いいえ  | いいえ  |
| servicetopologies/services/serviceunits | いいえ  | いいえ  |

## <a name="microsoftdevices"></a>Microsoft.Devices
| リソースの種類 | リソース グループ | サブスクリプション |
| ------------- | -------------- | ------------ |
| iothubs | はい | はい |
| provisioningservices | はい | はい |

## <a name="microsoftdevtestlab"></a>Microsoft.DevTestLab
| リソースの種類 | リソース グループ | サブスクリプション |
| ------------- | -------------- | ------------ |
| labcenters | いいえ  | いいえ  |
| labs | はい | いいえ  |
| labs/servicerunners | はい | はい |
| labs/virtualmachines | はい | いいえ  |
| schedules | いいえ  | いいえ  |

## <a name="microsoftdns"></a>microsoft.dns
| リソースの種類 | リソース グループ | サブスクリプション |
| ------------- | -------------- | ------------ |
| dnszones | いいえ  | いいえ  |
| dnszones/a | いいえ  | いいえ  |
| dnszones/aaaa | いいえ  | いいえ  |
| dnszones/cname | いいえ  | いいえ  |
| dnszones/mx | いいえ  | いいえ  |
| dnszones/ptr | いいえ  | いいえ  |
| dnszones/srv | いいえ  | いいえ  |
| dnszones/txt | いいえ  | いいえ  |
| trafficmanagerprofiles | いいえ  | いいえ  |

## <a name="microsoftdocumentdb"></a>Microsoft.DocumentDB
| リソースの種類 | リソース グループ | サブスクリプション |
| ------------- | -------------- | ------------ |
| databaseaccounts | はい | はい |

## <a name="microsoftdomainregistration"></a>Microsoft.DomainRegistration
| リソースの種類 | リソース グループ | サブスクリプション |
| ------------- | -------------- | ------------ |
| domains | はい | はい |

## <a name="microsofteventgrid"></a>Microsoft.EventGrid
| リソースの種類 | リソース グループ | サブスクリプション |
| ------------- | -------------- | ------------ |
| topics | はい | はい |

## <a name="microsofteventhub"></a>Microsoft.EventHub
| リソースの種類 | リソース グループ | サブスクリプション |
| ------------- | -------------- | ------------ |
| クラスター | はい | はい |
| namespaces | はい | はい |

## <a name="microsofthanaonazure"></a>Microsoft.HanaOnAzure
| リソースの種類 | リソース グループ | サブスクリプション |
| ------------- | -------------- | ------------ |
| hanainstances | はい | はい |

## <a name="microsofthardwaresecuritymodules"></a>Microsoft.HardwareSecurityModules
| リソースの種類 | リソース グループ | サブスクリプション |
| ------------- | -------------- | ------------ |
| dedicatedhsms | いいえ  | いいえ  |

## <a name="microsofthdinsight"></a>Microsoft.HDInsight
| リソースの種類 | リソース グループ | サブスクリプション |
| ------------- | -------------- | ------------ |
| クラスター | はい | はい |

## <a name="microsoftimportexport"></a>Microsoft.ImportExport
| リソースの種類 | リソース グループ | サブスクリプション |
| ------------- | -------------- | ------------ |
| ジョブ | はい | はい |

## <a name="microsoftinsights"></a>microsoft.insights
| リソースの種類 | リソース グループ | サブスクリプション |
| ------------- | -------------- | ------------ |
| actiongroups | はい | はい |
| activitylogalerts | いいえ  | いいえ  |
| alertrules | はい | はい |
| autoscalesettings | はい | はい |
| components | はい | はい |
| metricalerts | いいえ  | いいえ  |
| scheduledqueryrules | はい | はい |
| webtests | はい | はい |
| Workbooks | はい | はい |

## <a name="microsoftiotcentral"></a>Microsoft.IoTCentral
| リソースの種類 | リソース グループ | サブスクリプション |
| ------------- | -------------- | ------------ |
| iotapps | はい | はい |

## <a name="microsoftkeyvault"></a>Microsoft.KeyVault
| リソースの種類 | リソース グループ | サブスクリプション |
| ------------- | -------------- | ------------ |
| vaults | はい | はい |

## <a name="microsoftlabservices"></a>Microsoft.LabServices
| リソースの種類 | リソース グループ | サブスクリプション |
| ------------- | -------------- | ------------ |
| labaccounts | はい | はい |

## <a name="microsoftlocationbasedservices"></a>Microsoft.LocationBasedServices
| リソースの種類 | リソース グループ | サブスクリプション |
| ------------- | -------------- | ------------ |
| accounts | はい | はい |

## <a name="microsoftlocationservices"></a>Microsoft.LocationServices
| リソースの種類 | リソース グループ | サブスクリプション |
| ------------- | -------------- | ------------ |
| accounts | はい | はい |

## <a name="microsoftlogic"></a>Microsoft.Logic
| リソースの種類 | リソース グループ | サブスクリプション |
| ------------- | -------------- | ------------ |
| integrationaccounts | はい | はい |
| workflows | はい | はい |

## <a name="microsoftmachinelearning"></a>Microsoft.MachineLearning
| リソースの種類 | リソース グループ | サブスクリプション |
| ------------- | -------------- | ------------ |
| commitmentplans | はい | はい |
| webservices | はい | いいえ  |
| workspaces | はい | はい |

## <a name="microsoftmachinelearningcompute"></a>Microsoft.MachineLearningCompute
| リソースの種類 | リソース グループ | サブスクリプション |
| ------------- | -------------- | ------------ |
| operationalizationclusters | はい | はい |

## <a name="microsoftmachinelearningexperimentation"></a>Microsoft.MachineLearningExperimentation
| リソースの種類 | リソース グループ | サブスクリプション |
| ------------- | -------------- | ------------ |
| accounts | はい | はい |
| アカウント/ワークスペース | はい | はい |
| アカウント/ワークスペース/プロジェクト | はい | はい |
| teamaccounts | はい | はい |
| teamaccounts/ワークスペース | はい | はい |
| teamaccounts/ワークスペース/プロジェクト | はい | はい |

## <a name="microsoftmachinelearningmodelmanagement"></a>Microsoft.MachineLearningModelManagement
| リソースの種類 | リソース グループ | サブスクリプション |
| ------------- | -------------- | ------------ |
| accounts | はい | はい |

## <a name="microsoftmachinelearningservices"></a>Microsoft.MachineLearningServices
| リソースの種類 | リソース グループ | サブスクリプション |
| ------------- | -------------- | ------------ |
| workspaces | はい | はい |

## <a name="microsoftmanagedidentity"></a>Microsoft.ManagedIdentity
| リソースの種類 | リソース グループ | サブスクリプション |
| ------------- | -------------- | ------------ |
| userassignedidentities | はい | はい |

## <a name="microsoftmaps"></a>Microsoft.Maps
| リソースの種類 | リソース グループ | サブスクリプション |
| ------------- | -------------- | ------------ |
| accounts | はい | はい |

## <a name="microsoftmarketplaceapps"></a>Microsoft.MarketplaceApps
| リソースの種類 | リソース グループ | サブスクリプション |
| ------------- | -------------- | ------------ |
| classicdevservices | いいえ  | いいえ  |

## <a name="microsoftmedia"></a>Microsoft.Media
| リソースの種類 | リソース グループ | サブスクリプション |
| ------------- | -------------- | ------------ |
| mediaservices | はい | はい |
| mediaservices/liveevents | はい | はい |
| mediaservices/streamingendpoint | はい | はい |

## <a name="microsoftmigrate"></a>Microsoft.Migrate
| リソースの種類 | リソース グループ | サブスクリプション |
| ------------- | -------------- | ------------ |
| プロジェクト | いいえ  | いいえ  |

## <a name="microsoftnetwork"></a>Microsoft.Network
| リソースの種類 | リソース グループ | サブスクリプション |
| ------------- | -------------- | ------------ |
| applicationgateways | いいえ  | いいえ  |
| applicationsecuritygroups | はい | はい |
| azurefirewalls | いいえ  | いいえ  |
| connections | はい | はい |
| ddosprotectionplans | いいえ  | いいえ  |
| dnszones | はい | はい |
| expressroutecircuits | いいえ  | いいえ  |
| expressroutecrossconnections | いいえ  | いいえ  |
| expressroutegateways | いいえ  | いいえ  |
| expressrouteports | いいえ  | いいえ  |
| frontdoors | はい | はい |
| frontdoorwebapplicationfirewallpolicies | はい | はい |
| interfaceendpoints | いいえ  | いいえ  |
| loadbalancers | はい | はい |
| localnetworkgateways | はい | はい |
| networkintentpolicies | はい | はい |
| networkinterfaces | はい | はい |
| networkprofiles | いいえ  | いいえ  |
| networksecuritygroups | はい | はい |
| networkwatchers | はい | はい |
| networkwatchers/connectionmonitors | はい | はい |
| networkwatchers/レンズ | はい | はい |
| networkwatchers/pingmeshes | はい | はい |
| publicipaddresses | はい | はい |
| publicipprefixes | はい | はい |
| routefilters | いいえ  | いいえ  |
| routetables | はい | はい |
| serviceendpointpolicies | はい | はい |
| trafficmanagerprofiles | はい | はい |
| virtualhubs | はい | はい |
| virtualnetworkgateways | はい | はい |
| virtualnetworks | はい | はい |
| virtualnetworktaps | いいえ  | いいえ  |
| virtualwans | はい | はい |
| vpngateways | はい | はい |
| vpnsites | はい | はい |
| webapplicationfirewallpolicies | はい | はい |

## <a name="microsoftnotificationhubs"></a>Microsoft.NotificationHubs
| リソースの種類 | リソース グループ | サブスクリプション |
| ------------- | -------------- | ------------ |
| namespaces | はい | はい |
| namespaces/notificationhubs | はい | はい |

## <a name="microsoftoperationalinsights"></a>Microsoft.OperationalInsights
| リソースの種類 | リソース グループ | サブスクリプション |
| ------------- | -------------- | ------------ |
| workspaces | はい | はい |

## <a name="microsoftoperationsmanagement"></a>Microsoft.OperationsManagement
| リソースの種類 | リソース グループ | サブスクリプション |
| ------------- | -------------- | ------------ |
| managementconfigurations | はい | はい |
| solutions | はい | はい |
| ビュー | はい | はい |

## <a name="microsoftportal"></a>Microsoft.Portal
| リソースの種類 | リソース グループ | サブスクリプション |
| ------------- | -------------- | ------------ |
| dashboards | はい | はい |

## <a name="microsoftpowerbi"></a>Microsoft.PowerBI
| リソースの種類 | リソース グループ | サブスクリプション |
| ------------- | -------------- | ------------ |
| workspacecollections | はい | はい |

## <a name="microsoftpowerbidedicated"></a>Microsoft.PowerBIDedicated
| リソースの種類 | リソース グループ | サブスクリプション |
| ------------- | -------------- | ------------ |
| capacities | はい | はい |

## <a name="microsoftrecoveryservices"></a>Microsoft.RecoveryServices
| リソースの種類 | リソース グループ | サブスクリプション |
| ------------- | -------------- | ------------ |
| vaults | はい | はい |

## <a name="microsoftrelay"></a>Microsoft.Relay
| リソースの種類 | リソース グループ | サブスクリプション |
| ------------- | -------------- | ------------ |
| namespaces | はい | はい |

## <a name="microsoftsaas"></a>Microsoft.SaaS
| リソースの種類 | リソース グループ | サブスクリプション |
| ------------- | -------------- | ------------ |
| applications | はい | いいえ  |

## <a name="microsoftscheduler"></a>Microsoft.Scheduler
| リソースの種類 | リソース グループ | サブスクリプション |
| ------------- | -------------- | ------------ |
| フロー | はい | はい |
| jobcollections | はい | はい |

## <a name="microsoftsearch"></a>Microsoft.Search
| リソースの種類 | リソース グループ | サブスクリプション |
| ------------- | -------------- | ------------ |
| searchservices | はい | はい |

## <a name="microsoftservicebus"></a>Microsoft.ServiceBus
| リソースの種類 | リソース グループ | サブスクリプション |
| ------------- | -------------- | ------------ |
| namespaces | はい | はい |

## <a name="microsoftservicefabric"></a>Microsoft.ServiceFabric
| リソースの種類 | リソース グループ | サブスクリプション |
| ------------- | -------------- | ------------ |
| クラスター | はい | はい |

## <a name="microsoftservicefabricmesh"></a>Microsoft.ServiceFabricMesh
| リソースの種類 | リソース グループ | サブスクリプション |
| ------------- | -------------- | ------------ |
| applications | はい | はい |
| ネットワーク | はい | はい |
| volumes | はい | はい |

## <a name="microsoftsignalrservice"></a>Microsoft.SignalRService
| リソースの種類 | リソース グループ | サブスクリプション |
| ------------- | -------------- | ------------ |
| signalr | はい | はい |

## <a name="microsoftsiterecovery"></a>Microsoft.SiteRecovery
| リソースの種類 | リソース グループ | サブスクリプション |
| ------------- | -------------- | ------------ |
| siterecoveryvault | いいえ  | いいえ  |

## <a name="microsoftsolutions"></a>Microsoft.Solutions
| リソースの種類 | リソース グループ | サブスクリプション |
| ------------- | -------------- | ------------ |
| appliancedefinitions | いいえ  | いいえ  |
| アプライアンス | いいえ  | いいえ  |
| applicationdefinitions | いいえ  | いいえ  |
| applications | いいえ  | いいえ  |

## <a name="microsoftsql"></a>Microsoft.Sql
| リソースの種類 | リソース グループ | サブスクリプション |
| ------------- | -------------- | ------------ |
| managedinstances | はい | はい |
| managedinstances/データベース | はい | はい |
| サーバー | はい | はい |
| サーバ/データベース | はい | はい |
| servers/elasticpools | はい | はい |
| virtualclusters | はい | はい |

## <a name="microsoftstorage"></a>Microsoft.Storage
| リソースの種類 | リソース グループ | サブスクリプション |
| ------------- | -------------- | ------------ |
| storageaccounts | はい | はい |

## <a name="microsoftstoragesync"></a>Microsoft.StorageSync
| リソースの種類 | リソース グループ | サブスクリプション |
| ------------- | -------------- | ------------ |
| storagesyncservices | はい | はい |

## <a name="microsoftstorsimple"></a>Microsoft.StorSimple
| リソースの種類 | リソース グループ | サブスクリプション |
| ------------- | -------------- | ------------ |
| マネージャー | いいえ  | いいえ  |

## <a name="microsoftstreamanalytics"></a>Microsoft.StreamAnalytics
| リソースの種類 | リソース グループ | サブスクリプション |
| ------------- | -------------- | ------------ |
| streamingjobs | はい | はい |

## <a name="microsofttimeseriesinsights"></a>Microsoft.TimeSeriesInsights
| リソースの種類 | リソース グループ | サブスクリプション |
| ------------- | -------------- | ------------ |
| 環境 | はい | はい |
| 環境/eventsource | はい | はい |
| 環境/referencedatasets | はい | はい |

## <a name="microsoftvisualstudio"></a>microsoft.visualstudio
| リソースの種類 | リソース グループ | サブスクリプション |
| ------------- | -------------- | ------------ |
| アカウント | はい | はい |
| アカウント/拡張 | はい | はい |
| アカウント/プロジェクト | はい | はい |

## <a name="microsoftweb"></a>Microsoft.Web
| リソースの種類 | リソース グループ | サブスクリプション |
| ------------- | -------------- | ------------ |
| 証明書 | いいえ  | はい |
| classicmobileservices | いいえ  | いいえ  |
| connectiongateways | はい | はい |
| connections | はい | はい |
| customapis | はい | はい |
| hostingenvironments | いいえ  | いいえ  |
| serverfarms | はい | はい |
| sites | はい | はい |
| サイト/premieraddons | はい | はい |
| サイト/スロット | はい | はい |

## <a name="microsoftwindowsiot"></a>Microsoft.WindowsIoT
| リソースの種類 | リソース グループ | サブスクリプション |
| ------------- | -------------- | ------------ |
| deviceservices | はい | はい |


## <a name="third-party-services"></a>サード パーティーのサービス

サード パーティのサービスは現在、移動操作をサポートしていません。 これらのリソース プロバイダーは次のとおりです。

* 84codes.CloudAMQP
* AppDynamics.APM
* Aspera.Transfers
* Auth0.Cloud
* Citrix.Cloud
* Citrix.Services
* CloudSimple.PrivateCloudIaaS
* Cloudyn.Analytics
* Conexlink.MyCloudIT
* Crypteron.DataSecurity
* Dynatrace.DynatraceSaaS
* Dynatrace.Ruxit
* LiveArena.Broadcast
* Lombiq.DotNest
* Mailjet.Email
* Myget.PackageManagement
* NewRelic.APM
* nuubit.nextgencdn
* Paraleap.CloudMonix
* Pokitdok.Platform
* RavenHq.Db
* Raygun.CrashReporting
* RevAPM.MobileCDN
* Sendgrid.Email
* Sparkpost.Basic
* stackify.retrace
* SuccessBricks.ClearDB
* TrendMicro.DeepSecurity
* U2uconsult.TheIdentityHub


## <a name="next-steps"></a>次の手順

* リソースの移動のコマンドについては、「[新しいリソース グループまたはサブスクリプションへのリソースの移動](resource-group-move-resources.md)」を参照してください。
