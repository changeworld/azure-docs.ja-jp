---
title: 数が 800 に制限されないリソース
description: リソース グループ内に 800 より多くのインスタンスを作成できる Azure リソースの種類の一覧を示します。
ms.topic: conceptual
ms.date: 10/20/2021
ms.openlocfilehash: 728e41167fcabb6d30fa7af79cdb17f5aaad07d6
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/22/2021
ms.locfileid: "130260666"
---
# <a name="resources-not-limited-to-800-instances-per-resource-group"></a>リソース グループあたり 800 インスタンスに制限されないリソース

既定では、各リソース グループにはリソースの種類のインスタンスを最大 800 個までデプロイできます。 ただし、一部のリソースの種類は、800 インスタンスの制限から除外されています。 この記事では、リソース グループ内に 800 より多くのインスタンスを作成できる Azure リソースの種類の一覧を示します。 他のすべてのリソースの種類は、800 インスタンスに制限されます。

リソースの種類によっては、800 インスタンスの制限を解除するためにサポートに連絡する必要があります。 この記事では、それらのリソースの種類について注記されています。

一部のリソースでは、リージョンあたりのインスタンス数に制限があります。 この制限は、リソース グループあたり 800 インスタンスとは異なります。 リージョンごとにインスタンスを確認するには、Azure portal を使用します。 左側のウィンドウでサブスクリプションと **[使用量 + クォータ]** を選択します。 詳細については、「[制限に照らしたリソース使用量の確認](../../networking/check-usage-against-limits.md)」を参照してください。

## <a name="microsoftalertsmanagement"></a>Microsoft.AlertsManagement

* resourceHealthAlertRules
* smartDetectorAlertRules

## <a name="microsoftautomation"></a>Microsoft.Automation

* automationAccounts

## <a name="microsoftazurestack"></a>Microsoft.AzureStack

* linkedSubscriptions
* registrations
* registrations/customerSubscriptions
* registrations/products

## <a name="microsoftbotservice"></a>Microsoft.BotService

* botServices - 既定では、800 インスタンスに制限されています。 この制限を増やすには、サポートにご連絡ください。

## <a name="microsoftcompute"></a>Microsoft.Compute

* disks
* galleries
* galleries/images
* galleries/images/versions
* images
* スナップショット
* virtualMachineScaleSets - 既定では、800 インスタンスに制限されています。 この制限を増やすには、サポートにご連絡ください。
* virtualMachines

## <a name="microsoftcontainerinstance"></a>Microsoft.ContainerInstance

* containerGroups

## <a name="microsoftcontainerregistry"></a>Microsoft.ContainerRegistry

* registries/buildTasks
* registries/buildTasks/listSourceRepositoryProperties
* registries/buildTasks/steps
* registries/buildTasks/steps/listBuildArguments
* registries/eventGridFilters
* registries/replications
* registries/tasks
* registries/webhooks

## <a name="microsoftd365customerinsights"></a>Microsoft.D365CustomerInsights

* instances

## <a name="microsoftdbformariadb"></a>Microsoft.DBforMariaDB

* servers

## <a name="microsoftdbformysql"></a>Microsoft.DBforMySQL

* flexibleServers
* servers

## <a name="microsoftdbforpostgresql"></a>Microsoft.DBforPostgreSQL

* flexibleServers
* serverGroups
* serverGroupsv2
* servers
* serversv2

## <a name="microsoftdevtestlab"></a>Microsoft.DevTestLab

* schedules

## <a name="microsoftenterpriseknowledgegraph"></a>Microsoft.EnterpriseKnowledgeGraph

* services

## <a name="microsofteventhub"></a>Microsoft.EventHub

* clusters
* namespaces

## <a name="microsoftexperimentation"></a>Microsoft.Experimentation

* experimentWorkspaces

## <a name="microsoftguestconfiguration"></a>Microsoft.GuestConfiguration

* autoManagedVmConfigurationProfiles
* configurationProfileAssignments
* guestConfigurationAssignments
* software
* softwareUpdateProfile
* softwareUpdates

## <a name="microsofthybridcompute"></a>Microsoft.HybridCompute

* machines - 最大 5,000 インスタンスをサポートします
* machines/extensions - 無制限の数の VM 拡張機能インスタンスをサポートします

## <a name="microsoftinsights"></a>microsoft.insights

* metricalerts
* scheduledqueryrules

## <a name="microsoftlogic"></a>Microsoft.Logic

* integrationAccounts
* workflows

## <a name="microsoftmedia"></a>Microsoft.Media

* mediaservices/liveEvents

## <a name="microsoftnetapp"></a>Microsoft.NetApp

* netAppAccounts
* netAppAccounts/capacityPools
* netAppAccounts/capacityPools/volumes
* netAppAccounts/capacityPools/volumes/mountTargets
* netAppAccounts/capacityPools/volumes/snapshots
* netAppAccounts / capacityPools / volumes / subvolumes
* netAppAccounts/snapshotPolicies
* netAppAccounts/volumeGroups

## <a name="microsoftnetwork"></a>Microsoft.Network

* applicationGatewayWebApplicationFirewallPolicies
* applicationSecurityGroups
* bastionHosts
* ddosProtectionPlans
* dnszones
* dnszones/A
* dnszones/AAAA
* dnszones/CAA
* dnszones/CNAME
* dnszones/MX
* dnszones/NS
* dnszones/PTR
* dnszones/SOA
* dnszones/SRV
* dnszones/TXT
* dnszones/all
* dnszones/recordsets
* networkIntentPolicies
* networkInterfaces
* privateDnsZones
* privateDnsZones/A
* privateDnsZones/AAAA
* privateDnsZones/CNAME
* privateDnsZones/MX
* privateDnsZones/PTR
* privateDnsZones/SOA
* privateDnsZones/SRV
* privateDnsZones/TXT
* privateDnsZones/all
* privateDnsZones/virtualNetworkLinks
* privateEndpoints
* privateLinkServices
* publicIPAddresses
* serviceEndpointPolicies
* trafficmanagerprofiles
* virtualNetworkTaps

## <a name="microsoftportalsdk"></a>Microsoft.PortalSdk

* rootResources

## <a name="microsoftpowerbi"></a>Microsoft.PowerBI

* workspaceCollections - 既定では、800 インスタンスに制限されています。 この制限を増やすには、サポートにご連絡ください。

## <a name="microsoftpowerbidedicated"></a>Microsoft.PowerBIDedicated

* autoScaleVCores - 既定では、800 インスタンスに制限されています。 この制限を増やすには、サポートにご連絡ください。
* capacities - 既定では、800 インスタンスに制限されています。 この制限を増やすには、サポートにご連絡ください。

## <a name="microsoftrelay"></a>Microsoft.Relay

* namespaces

## <a name="microsoftscheduler"></a>Microsoft.Scheduler

* jobcollections

## <a name="microsoftservicebus"></a>Microsoft.ServiceBus

* namespaces

## <a name="microsoftsingularity"></a>Microsoft.Singularity

* accounts
* accounts/accountQuotaPolicies
* accounts/groupPolicies
* accounts/jobs
* accounts/models
* accounts/storageContainers

## <a name="microsoftsql"></a>Microsoft.Sql

* servers/databases

## <a name="microsoftstorage"></a>Microsoft.Storage

* storageAccounts

## <a name="microsoftstreamanalytics"></a>Microsoft.StreamAnalytics

* streamingjobs - 既定では、800 インスタンスに制限されています。 この制限を増やすには、サポートにご連絡ください。

## <a name="next-steps"></a>次のステップ

クォータと制限の完全な一覧については、「[Azure サブスクリプションとサービスの制限、クォータ、制約](azure-subscription-service-limits.md)」を参照してください。
