---
title: リソースの種類別の移動操作のサポート
description: 新しいリソース グループまたはサブスクリプションに移動できる Azure リソースの種類を一覧表示します。
ms.topic: conceptual
ms.date: 01/17/2020
ms.openlocfilehash: da08775ed6c694b95ecec452507f94638091db0c
ms.sourcegitcommit: 2a2af81e79a47510e7dea2efb9a8efb616da41f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/17/2020
ms.locfileid: "76261060"
---
# <a name="move-operation-support-for-resources"></a>リソースの操作のサポートの移動
この記事では、Azure リソースの種類は、移動操作をサポートしているかどうかを示します。 また、リソースを移動するときに考慮すべき特別な条件に関する情報も提供します。

リソース プロバイダーの名前空間に移動します。
> [!div class="op_single_selector"]
> - [Microsoft.AAD](#microsoftaad)
> - [microsoft.aadiam](#microsoftaadiam)
> - [Microsoft.Advisor](#microsoftadvisor)
> - [Microsoft.AlertsManagement](#microsoftalertsmanagement)
> - [Microsoft.AnalysisServices](#microsoftanalysisservices)
> - [Microsoft.ApiManagement](#microsoftapimanagement)
> - [Microsoft.AppConfiguration](#microsoftappconfiguration)
> - [Microsoft.AppPlatform](#microsoftappplatform)
> - [Microsoft.AppService](#microsoftappservice)
> - [Microsoft.Authorization](#microsoftauthorization)
> - [Microsoft.Automation](#microsoftautomation)
> - [Microsoft.AzureActiveDirectory](#microsoftazureactivedirectory)
> - [Microsoft.AzureData](#microsoftazuredata)
> - [Microsoft.AzureStack](#microsoftazurestack)
> - [Microsoft.Batch](#microsoftbatch)
> - [Microsoft.BatchAI](#microsoftbatchai)
> - [Microsoft.Billing](#microsoftbilling)
> - [Microsoft.BingMaps](#microsoftbingmaps)
> - [Microsoft.BizTalkServices](#microsoftbiztalkservices)
> - [Microsoft.Blockchain](#microsoftblockchain)
> - [Microsoft.Blueprint](#microsoftblueprint)
> - [Microsoft.BotService](#microsoftbotservice)
> - [Microsoft.Cache](#microsoftcache)
> - [Microsoft.Cdn](#microsoftcdn)
> - [Microsoft.CertificateRegistration](#microsoftcertificateregistration)
> - [Microsoft.ClassicCompute](#microsoftclassiccompute)
> - [Microsoft.ClassicNetwork](#microsoftclassicnetwork)
> - [Microsoft.ClassicStorage](#microsoftclassicstorage)
> - [Microsoft.CognitiveServices](#microsoftcognitiveservices)
> - [Microsoft.Compute](#microsoftcompute)
> - [Microsoft.Consumption](#microsoftconsumption)
> - [Microsoft.Container](#microsoftcontainer)
> - [Microsoft.ContainerInstance](#microsoftcontainerinstance)
> - [Microsoft.ContainerRegistry](#microsoftcontainerregistry)
> - [Microsoft.ContainerService](#microsoftcontainerservice)
> - [Microsoft.ContentModerator](#microsoftcontentmoderator)
> - [Microsoft.CortanaAnalytics](#microsoftcortanaanalytics)
> - [Microsoft.CostManagement](#microsoftcostmanagement)
> - [Microsoft.CustomerInsights](#microsoftcustomerinsights)
> - [Microsoft.CustomProviders](#microsoftcustomproviders)
> - [Microsoft.DataBox](#microsoftdatabox)
> - [Microsoft.DataBoxEdge](#microsoftdataboxedge)
> - [Microsoft.Databricks](#microsoftdatabricks)
> - [Microsoft.DataCatalog](#microsoftdatacatalog)
> - [Microsoft.DataConnect](#microsoftdataconnect)
> - [Microsoft.DataExchange](#microsoftdataexchange)
> - [Microsoft.DataFactory](#microsoftdatafactory)
> - [Microsoft.DataLake](#microsoftdatalake)
> - [Microsoft.DataLakeAnalytics](#microsoftdatalakeanalytics)
> - [Microsoft.DataLakeStore](#microsoftdatalakestore)
> - [Microsoft.DataMigration](#microsoftdatamigration)
> - [Microsoft.DataProtection](#microsoftdataprotection)
> - [Microsoft.DataShare](#microsoftdatashare)
> - [Microsoft.DBforMariaDB](#microsoftdbformariadb)
> - [Microsoft.DBforMySQL](#microsoftdbformysql)
> - [Microsoft.DBforPostgreSQL](#microsoftdbforpostgresql)
> - [Microsoft.DeploymentManager](#microsoftdeploymentmanager)
> - [Microsoft.Devices](#microsoftdevices)
> - [Microsoft.DevOps](#microsoftdevops)
> - [Microsoft.DevSpaces](#microsoftdevspaces)
> - [Microsoft.DevTestLab](#microsoftdevtestlab)
> - [Microsoft.DocumentDB](#microsoftdocumentdb)
> - [Microsoft.DomainRegistration](#microsoftdomainregistration)
> - [Microsoft.EnterpriseKnowledgeGraph](#microsoftenterpriseknowledgegraph)
> - [Microsoft.EventGrid](#microsofteventgrid)
> - [Microsoft.EventHub](#microsofteventhub)
> - [Microsoft.Genomics](#microsoftgenomics)
> - [Microsoft.GuestConfiguration](#microsoftguestconfiguration)
> - [Microsoft.HanaOnAzure](#microsofthanaonazure)
> - [Microsoft.HDInsight](#microsofthdinsight)
> - [Microsoft.HealthcareApis](#microsofthealthcareapis)
> - [Microsoft.HybridCompute](#microsofthybridcompute)
> - [Microsoft.HybridData](#microsofthybriddata)
> - [Microsoft.ImportExport](#microsoftimportexport)
> - [microsoft.insights](#microsoftinsights)
> - [Microsoft.IoTCentral](#microsoftiotcentral)
> - [Microsoft.IoTSpaces](#microsoftiotspaces)
> - [Microsoft.KeyVault](#microsoftkeyvault)
> - [Microsoft.Kubernetes](#microsoftkubernetes)
> - [Microsoft.Kusto](#microsoftkusto)
> - [Microsoft.LabServices](#microsoftlabservices)
> - [Microsoft.LocationBasedServices](#microsoftlocationbasedservices)
> - [Microsoft.LocationServices](#microsoftlocationservices)
> - [Microsoft.Logic](#microsoftlogic)
> - [Microsoft.MachineLearning](#microsoftmachinelearning)
> - [Microsoft.MachineLearningCompute](#microsoftmachinelearningcompute)
> - [Microsoft.MachineLearningExperimentation](#microsoftmachinelearningexperimentation)
> - [Microsoft.MachineLearningModelManagement](#microsoftmachinelearningmodelmanagement)
> - [Microsoft.MachineLearningOperationalization](#microsoftmachinelearningoperationalization)
> - [Microsoft.MachineLearningServices](#microsoftmachinelearningservices)
> - [Microsoft.ManagedIdentity](#microsoftmanagedidentity)
> - [Microsoft.ManagedServices](#microsoftmanagedservices)
> - [Microsoft.Maps](#microsoftmaps)
> - [Microsoft.MarketplaceApps](#microsoftmarketplaceapps)
> - [Microsoft.Media](#microsoftmedia)
> - [Microsoft.Microservices4Spring](#microsoftmicroservices4spring)
> - [Microsoft.Migrate](#microsoftmigrate)
> - [Microsoft.NetApp](#microsoftnetapp)
> - [Microsoft.Network](#microsoftnetwork)
> - [Microsoft.NotificationHubs](#microsoftnotificationhubs)
> - [Microsoft.ObjectStore](#microsoftobjectstore)
> - [Microsoft.OperationalInsights](#microsoftoperationalinsights)
> - [Microsoft.OperationsManagement](#microsoftoperationsmanagement)
> - [Microsoft.Peering](#microsoftpeering)
> - [Microsoft.PolicyInsights](#microsoftpolicyinsights)
> - [Microsoft.Portal](#microsoftportal)
> - [Microsoft.PortalSdk](#microsoftportalsdk)
> - [Microsoft.PowerBI](#microsoftpowerbi)
> - [Microsoft.PowerBIDedicated](#microsoftpowerbidedicated)
> - [Microsoft.ProjectBabylon](#microsoftprojectbabylon)
> - [Microsoft.ProjectOxford](#microsoftprojectoxford)
> - [Microsoft.ProviderHub](#microsoftproviderhub)
> - [Microsoft.RecoveryServices](#microsoftrecoveryservices)
> - [Microsoft.Relay](#microsoftrelay)
> - [Microsoft.ResourceGraph](#microsoftresourcegraph)
> - [Microsoft.ResourceHealth](#microsoftresourcehealth)
> - [Microsoft.Resources](#microsoftresources)
> - [Microsoft.SaaS](#microsoftsaas)
> - [Microsoft.Scheduler](#microsoftscheduler)
> - [Microsoft.Search](#microsoftsearch)
> - [Microsoft.Security](#microsoftsecurity)
> - [Microsoft.SecurityInsights](#microsoftsecurityinsights)
> - [Microsoft.ServerManagement](#microsoftservermanagement)
> - [Microsoft.ServiceBus](#microsoftservicebus)
> - [Microsoft.ServiceFabric](#microsoftservicefabric)
> - [Microsoft.ServiceFabricMesh](#microsoftservicefabricmesh)
> - [Microsoft.Services](#microsoftservices)
> - [Microsoft.SignalRService](#microsoftsignalrservice)
> - [Microsoft.SoftwarePlan](#microsoftsoftwareplan)
> - [Microsoft.Solutions](#microsoftsolutions)
> - [Microsoft.Sql](#microsoftsql)
> - [Microsoft.SqlVirtualMachine](#microsoftsqlvirtualmachine)
> - [Microsoft.SqlVM](#microsoftsqlvm)
> - [Microsoft.Storage](#microsoftstorage)
> - [Microsoft.StorageSync](#microsoftstoragesync)
> - [Microsoft.StorageSyncDev](#microsoftstoragesyncdev)
> - [Microsoft.StorageSyncInt](#microsoftstoragesyncint)
> - [Microsoft.StorSimple](#microsoftstorsimple)
> - [Microsoft.StreamAnalytics](#microsoftstreamanalytics)
> - [Microsoft.StreamAnalyticsExplorer](#microsoftstreamanalyticsexplorer)
> - [Microsoft.Subscription](#microsoftsubscription)
> - [microsoft.support](#microsoftsupport)
> - [Microsoft.TerraformOSS](#microsoftterraformoss)
> - [Microsoft.TimeSeriesInsights](#microsofttimeseriesinsights)
> - [Microsoft.Token](#microsofttoken)
> - [Microsoft.VMwareCloudSimple](#microsoftvmwarecloudsimple)
> - [Microsoft.VSOnline](#microsoftvsonline)
> - [Microsoft.Web](#microsoftweb)
> - [Microsoft.WindowsIoT](#microsoftwindowsiot)
> - [Microsoft.WorkloadMonitor](#microsoftworkloadmonitor)

## <a name="microsoftaad"></a>Microsoft.AAD

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション |
> | ------------- | ----------- | ---------- |
> | domainservices | いいえ | いいえ |

## <a name="microsoftaadiam"></a>microsoft.aadiam

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション |
> | ------------- | ----------- | ---------- |
> | tenants | いいえ | いいえ |

## <a name="microsoftadvisor"></a>Microsoft.Advisor

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション |
> | ------------- | ----------- | ---------- |
> | configuration | いいえ | いいえ |
> | recommendations | いいえ | いいえ |
> | suppressions | いいえ | いいえ |

## <a name="microsoftalertsmanagement"></a>Microsoft.AlertsManagement

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション |
> | ------------- | ----------- | ---------- |
> | actionrules | はい | はい |
> | alerts | いいえ | いいえ |
> | alertssummary | いいえ | いいえ |
> | smartdetectoralertrules | はい | はい |

## <a name="microsoftanalysisservices"></a>Microsoft.AnalysisServices

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション |
> | ------------- | ----------- | ---------- |
> | servers | はい | はい |

## <a name="microsoftapimanagement"></a>Microsoft.ApiManagement

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション |
> | ------------- | ----------- | ---------- |
> | サービス (service) | はい | はい |

## <a name="microsoftappconfiguration"></a>Microsoft.AppConfiguration

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション |
> | ------------- | ----------- | ---------- |
> | configurationstores | はい | はい |

## <a name="microsoftappplatform"></a>Microsoft.AppPlatform

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション |
> | ------------- | ----------- | ---------- |
> | spring | はい | はい |

## <a name="microsoftappservice"></a>Microsoft.AppService

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション |
> | ------------- | ----------- | ---------- |
> | apiapps | いいえ | いいえ |
> | appidentities | いいえ | いいえ |
> | gateways | いいえ | いいえ |

> [!IMPORTANT]
> [App Service move guidance (App Service の移動のガイダンス)](./move-limitations/app-service-move-limitations.md) に関する記事をご覧ください。

## <a name="microsoftauthorization"></a>Microsoft.Authorization

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション |
> | ------------- | ----------- | ---------- |
> | checkaccess | いいえ | いいえ |
> | denyassignments | いいえ | いいえ |
> | findorphanroleassignments | いいえ | いいえ |
> | locks | いいえ | いいえ |
> | 権限 | いいえ | いいえ |
> | policyassignments | いいえ | いいえ |
> | policydefinitions | いいえ | いいえ |
> | policysetdefinitions | いいえ | いいえ |
> | roleassignments | いいえ | いいえ |
> | roleassignmentsusagemetrics | いいえ | いいえ |
> | roledefinitions | いいえ | いいえ |

## <a name="microsoftautomation"></a>Microsoft.Automation

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション |
> | ------------- | ----------- | ---------- |
> | automationaccounts | はい | はい |
> | automationaccounts/configurations | はい | はい |
> | automationaccounts/runbooks | はい | はい |

> [!IMPORTANT]
> Runbook は Automation アカウントと同じリソース グループに存在する必要があります。

## <a name="microsoftazureactivedirectory"></a>Microsoft.AzureActiveDirectory

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション |
> | ------------- | ----------- | ---------- |
> | b2cdirectories | はい | はい |

## <a name="microsoftazuredata"></a>Microsoft.AzureData

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション |
> | ------------- | ----------- | ---------- |
> | hybriddatamanagers | いいえ | いいえ |
> | postgresinstances | いいえ | いいえ |
> | sqlbigdataclusters | いいえ | いいえ |
> | sqlinstances | いいえ | いいえ |
> | sqlserverregistrations | はい | はい |

## <a name="microsoftazurestack"></a>Microsoft.AzureStack

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション |
> | ------------- | ----------- | ---------- |
> | registrations | はい | はい |

## <a name="microsoftbatch"></a>Microsoft.Batch

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション |
> | ------------- | ----------- | ---------- |
> | batchaccounts | はい | はい |

## <a name="microsoftbatchai"></a>Microsoft.BatchAI

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション |
> | ------------- | ----------- | ---------- |
> | clusters | いいえ | いいえ |
> | fileservers | いいえ | いいえ |
> | jobs | いいえ | いいえ |
> | workspaces | いいえ | いいえ |

## <a name="microsoftbilling"></a>Microsoft.Billing

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション |
> | ------------- | ----------- | ---------- |
> | billingperiods | いいえ | いいえ |
> | billingpermissions | いいえ | いいえ |
> | billingroleassignments | いいえ | いいえ |
> | billingroledefinitions | いいえ | いいえ |
> | createbillingroleassignment | いいえ | いいえ |

## <a name="microsoftbingmaps"></a>Microsoft.BingMaps

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション |
> | ------------- | ----------- | ---------- |
> | mapapis | いいえ | いいえ |

## <a name="microsoftbiztalkservices"></a>Microsoft.BizTalkServices

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション |
> | ------------- | ----------- | ---------- |
> | biztalk | いいえ | いいえ |

## <a name="microsoftblockchain"></a>Microsoft.Blockchain

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション |
> | ------------- | ----------- | ---------- |
> | blockchainmembers | いいえ | いいえ |
> | watchers | いいえ | いいえ |

## <a name="microsoftblueprint"></a>Microsoft.Blueprint

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション |
> | ------------- | ----------- | ---------- |
> | blueprintassignments | いいえ | いいえ |
> | blueprints | いいえ | いいえ |

## <a name="microsoftbotservice"></a>Microsoft.BotService

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション |
> | ------------- | ----------- | ---------- |
> | botservices | はい | はい |

## <a name="microsoftcache"></a>Microsoft.Cache

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション |
> | ------------- | ----------- | ---------- |
> | redis | はい | はい |

> [!IMPORTANT]
> 仮想ネットワークを使用して Azure Cache for Redis インスタンスが構成されている場合、インスタンスを別のサブスクリプションに移動させることはできません。 「[ネットワーク リソースの移動ガイダンス](./move-limitations/networking-move-limitations.md)」を参照してください。

## <a name="microsoftcdn"></a>Microsoft.Cdn

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション |
> | ------------- | ----------- | ---------- |
> | cdnwebapplicationfirewallpolicies | はい | はい |
> | profiles | はい | はい |
> | profiles/endpoints | はい | はい |

## <a name="microsoftcertificateregistration"></a>Microsoft.CertificateRegistration

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション |
> | ------------- | ----------- | ---------- |
> | certificateorders | はい | はい |

> [!IMPORTANT]
> [App Service move guidance (App Service の移動のガイダンス)](./move-limitations/app-service-move-limitations.md) に関する記事をご覧ください。

## <a name="microsoftclassiccompute"></a>Microsoft.ClassicCompute

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション |
> | ------------- | ----------- | ---------- |
> | domainnames | はい | いいえ |
> | virtualmachines | はい | いいえ |

> [!IMPORTANT]
> [Classic deployment move guidance (クラシック デプロイの移動のガイダンス)](./move-limitations/classic-model-move-limitations.md) に関する記事をご覧ください。 クラシック デプロイのリソースは、そのシナリオに固有の操作を使用して、サブスクリプション間で移動できます。

## <a name="microsoftclassicnetwork"></a>Microsoft.ClassicNetwork

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション |
> | ------------- | ----------- | ---------- |
> | networksecuritygroups | いいえ | いいえ |
> | reservedips | いいえ | いいえ |
> | virtualnetworks | いいえ | いいえ |

> [!IMPORTANT]
> [Classic deployment move guidance (クラシック デプロイの移動のガイダンス)](./move-limitations/classic-model-move-limitations.md) に関する記事をご覧ください。 クラシック デプロイのリソースは、そのシナリオに固有の操作を使用して、サブスクリプション間で移動できます。

## <a name="microsoftclassicstorage"></a>Microsoft.ClassicStorage

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション |
> | ------------- | ----------- | ---------- |
> | storageaccounts | はい | いいえ |

> [!IMPORTANT]
> [Classic deployment move guidance (クラシック デプロイの移動のガイダンス)](./move-limitations/classic-model-move-limitations.md) に関する記事をご覧ください。 クラシック デプロイのリソースは、そのシナリオに固有の操作を使用して、サブスクリプション間で移動できます。

## <a name="microsoftcognitiveservices"></a>Microsoft.CognitiveServices

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション |
> | ------------- | ----------- | ---------- |
> | accounts | はい | はい |

## <a name="microsoftcompute"></a>Microsoft.Compute

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション |
> | ------------- | ----------- | ---------- |
> | availabilitysets | はい | はい |
> | diskencryptionsets | いいえ | いいえ |
> | disks | はい | はい |
> | galleries | いいえ | いいえ |
> | galleries/images | いいえ | いいえ |
> | galleries/images/versions | いいえ | いいえ |
> | hostgroups | いいえ | いいえ |
> | hostgroups/hosts | いいえ | いいえ |
> | images | はい | はい |
> | proximityplacementgroups | いいえ | いいえ |
> | restorepointcollections | いいえ | いいえ |
> | sharedvmimages | いいえ | いいえ |
> | sharedvmimages/versions | いいえ | いいえ |
> | スナップショット | はい | はい |
> | virtualmachines | はい | はい |
> | virtualmachines/extensions | はい | はい |
> | virtualmachinescalesets | はい | はい |

> [!IMPORTANT]
> [Virtual Machines move guidance (仮想マシンの移動のガイダンス)](./move-limitations/virtual-machines-move-limitations.md) に関する記事をご覧ください。

## <a name="microsoftconsumption"></a>Microsoft.Consumption

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション |
> | ------------- | ----------- | ---------- |
> | aggregatedcost | いいえ | いいえ |
> | balances | いいえ | いいえ |
> | budgets | いいえ | いいえ |
> | charges | いいえ | いいえ |
> | costtags | いいえ | いいえ |
> | credits | いいえ | いいえ |
> | events | いいえ | いいえ |
> | forecasts | いいえ | いいえ |
> | lots | いいえ | いいえ |
> | marketplaces | いいえ | いいえ |
> | operationresults | いいえ | いいえ |
> | operationstatus | いいえ | いいえ |
> | pricesheets | いいえ | いいえ |
> | products | いいえ | いいえ |
> | reservationdetails | いいえ | いいえ |
> | reservationrecommendations | いいえ | いいえ |
> | reservationsummaries | いいえ | いいえ |
> | reservationtransactions | いいえ | いいえ |
> | tags | いいえ | いいえ |
> | tenants | いいえ | いいえ |
> | terms | いいえ | いいえ |
> | usagedetails | いいえ | いいえ |

## <a name="microsoftcontainer"></a>Microsoft.Container

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション |
> | ------------- | ----------- | ---------- |
> | containergroups | いいえ | いいえ |

## <a name="microsoftcontainerinstance"></a>Microsoft.ContainerInstance

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション |
> | ------------- | ----------- | ---------- |
> | containergroups | いいえ | いいえ |
> | serviceassociationlinks | いいえ | いいえ |

## <a name="microsoftcontainerregistry"></a>Microsoft.ContainerRegistry

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション |
> | ------------- | ----------- | ---------- |
> | registries | はい | はい |
> | registries/buildtasks | はい | はい |
> | registries/replications | はい | はい |
> | registries/taskruns | はい | はい |
> | registries/tasks | はい | はい |
> | registries/webhooks | はい | はい |

## <a name="microsoftcontainerservice"></a>Microsoft.ContainerService

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション |
> | ------------- | ----------- | ---------- |
> | containerservices | いいえ | いいえ |
> | managedclusters | いいえ | いいえ |
> | openshiftmanagedclusters | いいえ | いいえ |

## <a name="microsoftcontentmoderator"></a>Microsoft.ContentModerator

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション |
> | ------------- | ----------- | ---------- |
> | applications | いいえ | いいえ |

## <a name="microsoftcortanaanalytics"></a>Microsoft.CortanaAnalytics

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション |
> | ------------- | ----------- | ---------- |
> | accounts | いいえ | いいえ |

## <a name="microsoftcostmanagement"></a>Microsoft.CostManagement

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション |
> | ------------- | ----------- | ---------- |
> | alerts | いいえ | いいえ |
> | budgets | いいえ | いいえ |
> | connectors | はい | はい |
> | dimensions | いいえ | いいえ |
> | exports | いいえ | いいえ |
> | externalsubscriptions | いいえ | いいえ |
> | forecast | いいえ | いいえ |
> | query | いいえ | いいえ |
> | reportconfigs | いいえ | いいえ |
> | reports | いいえ | いいえ |
> | showbackrules | いいえ | いいえ |
> | views | いいえ | いいえ |

## <a name="microsoftcustomerinsights"></a>Microsoft.CustomerInsights

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション |
> | ------------- | ----------- | ---------- |
> | hubs | いいえ | いいえ |

## <a name="microsoftcustomproviders"></a>Microsoft.CustomProviders

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション |
> | ------------- | ----------- | ---------- |
> | associations | いいえ | いいえ |
> | resourceproviders | はい | はい |

## <a name="microsoftdatabox"></a>Microsoft.DataBox

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション |
> | ------------- | ----------- | ---------- |
> | jobs | いいえ | いいえ |

## <a name="microsoftdataboxedge"></a>Microsoft.DataBoxEdge

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション |
> | ------------- | ----------- | ---------- |
> | databoxedgedevices | いいえ | いいえ |

## <a name="microsoftdatabricks"></a>Microsoft.Databricks

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション |
> | ------------- | ----------- | ---------- |
> | workspaces | いいえ | いいえ |

## <a name="microsoftdatacatalog"></a>Microsoft.DataCatalog

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション |
> | ------------- | ----------- | ---------- |
> | catalogs | はい | はい |
> | datacatalogs | いいえ | いいえ |

## <a name="microsoftdataconnect"></a>Microsoft.DataConnect

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション |
> | ------------- | ----------- | ---------- |
> | connectionmanagers | いいえ | いいえ |

## <a name="microsoftdataexchange"></a>Microsoft.DataExchange

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション |
> | ------------- | ----------- | ---------- |
> | packages | いいえ | いいえ |
> | plans | いいえ | いいえ |

## <a name="microsoftdatafactory"></a>Microsoft.DataFactory

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション |
> | ------------- | ----------- | ---------- |
> | datafactories | はい | はい |
> | factories | はい | はい |

## <a name="microsoftdatalake"></a>Microsoft.DataLake

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション |
> | ------------- | ----------- | ---------- |
> | datalakeaccounts | いいえ | いいえ |

## <a name="microsoftdatalakeanalytics"></a>Microsoft.DataLakeAnalytics

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション |
> | ------------- | ----------- | ---------- |
> | accounts | はい | はい |

## <a name="microsoftdatalakestore"></a>Microsoft.DataLakeStore

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション |
> | ------------- | ----------- | ---------- |
> | accounts | はい | はい |

## <a name="microsoftdatamigration"></a>Microsoft.DataMigration

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション |
> | ------------- | ----------- | ---------- |
> | services | いいえ | いいえ |
> | services/projects | いいえ | いいえ |
> | slots | いいえ | いいえ |

## <a name="microsoftdataprotection"></a>Microsoft.DataProtection

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション |
> | ------------- | ----------- | ---------- |
> | backupvaults | いいえ | いいえ |

## <a name="microsoftdatashare"></a>Microsoft.DataShare

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション |
> | ------------- | ----------- | ---------- |
> | accounts | はい | はい |

## <a name="microsoftdbformariadb"></a>Microsoft.DBforMariaDB

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション |
> | ------------- | ----------- | ---------- |
> | servers | はい | はい |

## <a name="microsoftdbformysql"></a>Microsoft.DBforMySQL

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション |
> | ------------- | ----------- | ---------- |
> | servers | はい | はい |

## <a name="microsoftdbforpostgresql"></a>Microsoft.DBforPostgreSQL

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション |
> | ------------- | ----------- | ---------- |
> | servergroups | いいえ | いいえ |
> | servers | はい | はい |
> | serversv2 | はい | はい |

## <a name="microsoftdeploymentmanager"></a>Microsoft.DeploymentManager

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション |
> | ------------- | ----------- | ---------- |
> | artifactsources | はい | はい |
> | rollouts | はい | はい |
> | servicetopologies | はい | はい |
> | servicetopologies/services | はい | はい |
> | servicetopologies/services/serviceunits | はい | はい |
> | steps | はい | はい |

## <a name="microsoftdevices"></a>Microsoft.Devices

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション |
> | ------------- | ----------- | ---------- |
> | elasticpools | いいえ | いいえ |
> | elasticpools/iothubtenants | いいえ | いいえ |
> | iothubs | はい | はい |
> | provisioningservices | はい | はい |

## <a name="microsoftdevops"></a>Microsoft.DevOps

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション |
> | ------------- | ----------- | ---------- |
> | pipelines | はい | はい |

## <a name="microsoftdevspaces"></a>Microsoft.DevSpaces

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション |
> | ------------- | ----------- | ---------- |
> | controllers | はい | はい |

## <a name="microsoftdevtestlab"></a>Microsoft.DevTestLab

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション |
> | ------------- | ----------- | ---------- |
> | labcenters | いいえ | いいえ |
> | labs | はい | いいえ |
> | labs/environments | はい | はい |
> | labs/servicerunners | はい | はい |
> | labs/virtualmachines | はい | いいえ |
> | schedules | はい | はい |

## <a name="microsoftdocumentdb"></a>Microsoft.DocumentDB

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション |
> | ------------- | ----------- | ---------- |
> | databaseaccounts | はい | はい |

## <a name="microsoftdomainregistration"></a>Microsoft.DomainRegistration

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション |
> | ------------- | ----------- | ---------- |
> | domains | はい | はい |

## <a name="microsoftenterpriseknowledgegraph"></a>Microsoft.EnterpriseKnowledgeGraph

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション |
> | ------------- | ----------- | ---------- |
> | services | はい | はい |

## <a name="microsofteventgrid"></a>Microsoft.EventGrid

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション |
> | ------------- | ----------- | ---------- |
> | domains | はい | はい |
> | eventSubscriptions | いいえ - 個別に移動できませんが、サブスクライブしたリソースで自動的に移動されます。 | いいえ - 個別に移動できませんが、サブスクライブしたリソースで自動的に移動されます。 |
> | eventsubscriptions | いいえ - 個別に移動できませんが、サブスクライブしたリソースで自動的に移動されます。 | いいえ - 個別に移動できませんが、サブスクライブしたリソースで自動的に移動されます。 |
> | extensiontopics | いいえ | いいえ |
> | topics | はい | はい |

## <a name="microsofteventhub"></a>Microsoft.EventHub

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション |
> | ------------- | ----------- | ---------- |
> | clusters | はい | はい |
> | namespaces | はい | はい |

## <a name="microsoftgenomics"></a>Microsoft.Genomics

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション |
> | ------------- | ----------- | ---------- |
> | accounts | いいえ | いいえ |

## <a name="microsoftguestconfiguration"></a>Microsoft.GuestConfiguration

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション |
> | ------------- | ----------- | ---------- |
> | guestconfigurationassignments | いいえ | いいえ |
> | software | いいえ | いいえ |
> | softwareupdateprofile | いいえ | いいえ |
> | softwareupdates | いいえ | いいえ |

## <a name="microsofthanaonazure"></a>Microsoft.HanaOnAzure

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション |
> | ------------- | ----------- | ---------- |
> | hanainstances | いいえ | いいえ |
> | sapmonitors | はい | はい |

## <a name="microsofthdinsight"></a>Microsoft.HDInsight

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション |
> | ------------- | ----------- | ---------- |
> | clusters | はい | はい |

> [!IMPORTANT]
> HDInsight クラスターは、新しいサブスクリプションまたはリソース グループに移動できます。 ただし、HDInsight クラスターにリンクされているネットワーク リソース (仮想ネットワーク、NIC、ロード バランサーなど) をサブスクリプション間で移動することはできません。 また、クラスターの仮想マシンに接続されている NIC を新しいリソース グループに移動することはできません。
>
> HDInsight クラスターを新しいサブスクリプションに移動するときは、まず、他のリソース (ストレージ アカウントなど) を移動します。 その後、HDInsight クラスターを単独で移動します。

## <a name="microsofthealthcareapis"></a>Microsoft.HealthcareApis

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション |
> | ------------- | ----------- | ---------- |
> | services | はい | はい |

## <a name="microsofthybridcompute"></a>Microsoft.HybridCompute

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション |
> | ------------- | ----------- | ---------- |
> | machines | はい | はい |
> | machines / extensions | いいえ | いいえ |

## <a name="microsofthybriddata"></a>Microsoft.HybridData

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション |
> | ------------- | ----------- | ---------- |
> | datamanagers | はい | はい |

## <a name="microsoftimportexport"></a>Microsoft.ImportExport

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション |
> | ------------- | ----------- | ---------- |
> | jobs | はい | はい |

## <a name="microsoftinsights"></a>microsoft.insights

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション |
> | ------------- | ----------- | ---------- |
> | actiongroups | はい | はい |
> | activitylogalerts | いいえ | いいえ |
> | alertrules | はい | はい |
> | automatedexportsettings | いいえ | いいえ |
> | autoscalesettings | はい | はい |
> | baseline | いいえ | いいえ |
> | calculatebaseline | いいえ | いいえ |
> | components | はい | はい |
> | diagnosticsettings | いいえ | いいえ |
> | diagnosticsettingscategories | いいえ | いいえ |
> | eventtypes | いいえ | いいえ |
> | extendeddiagnosticsettings | いいえ | いいえ |
> | logdefinitions | いいえ | いいえ |
> | logs | いいえ | いいえ |
> | metricalerts | いいえ | いいえ |
> | metricbaselines | いいえ | いいえ |
> | metricdefinitions | いいえ | いいえ |
> | metricnamespaces | いいえ | いいえ |
> | metrics | いいえ | いいえ |
> | myworkbooks | いいえ | いいえ |
> | scheduledqueryrules | はい | はい |
> | トポロジ | いいえ | いいえ |
> | トランザクション | いいえ | いいえ |
> | vminsightsonboardingstatuses | いいえ | いいえ |
> | webtests | はい | はい |
> | Workbooks | はい | はい |
> | workbooktemplates | はい | はい |

> [!IMPORTANT]
> 新しいサブスクリプションへの移動によって[サブスクリプション クォータ](azure-subscription-service-limits.md#azure-monitor-limits)を超えないようにします。

## <a name="microsoftiotcentral"></a>Microsoft.IoTCentral

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション |
> | ------------- | ----------- | ---------- |
> | iotapps | はい | はい |

## <a name="microsoftiotspaces"></a>Microsoft.IoTSpaces

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション |
> | ------------- | ----------- | ---------- |
> | checknameavailability | はい | はい |
> | graph | はい | はい |

## <a name="microsoftkeyvault"></a>Microsoft.KeyVault

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション |
> | ------------- | ----------- | ---------- |
> | vaults | はい | はい |

> [!IMPORTANT]
> ディスクの暗号化に使用されるキー コンテナーは、同じサブスクリプション内のリソース グループに移動したり、サブスクリプション間で移動したりすることはできません。

## <a name="microsoftkubernetes"></a>Microsoft.Kubernetes

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション |
> | ------------- | ----------- | ---------- |
> | connectedclusters | いいえ | いいえ |

## <a name="microsoftkusto"></a>Microsoft.Kusto

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション |
> | ------------- | ----------- | ---------- |
> | clusters | はい | はい |

## <a name="microsoftlabservices"></a>Microsoft.LabServices

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション |
> | ------------- | ----------- | ---------- |
> | labaccounts | いいえ | いいえ |

## <a name="microsoftlocationbasedservices"></a>Microsoft.LocationBasedServices

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション |
> | ------------- | ----------- | ---------- |
> | accounts | いいえ | いいえ |

## <a name="microsoftlocationservices"></a>Microsoft.LocationServices

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション |
> | ------------- | ----------- | ---------- |
> | accounts | いいえ | いいえ |

## <a name="microsoftlogic"></a>Microsoft.Logic

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション |
> | ------------- | ----------- | ---------- |
> | hostingenvironments | いいえ | いいえ |
> | integrationaccounts | はい | はい |
> | integrationserviceenvironments | はい | いいえ |
> | integrationserviceenvironments / managedapis | はい | いいえ |
> | isolatedenvironments | いいえ | いいえ |
> | workflows | はい | はい |

## <a name="microsoftmachinelearning"></a>Microsoft.MachineLearning

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション |
> | ------------- | ----------- | ---------- |
> | commitmentplans | はい | はい |
> | webservices | はい | いいえ |
> | workspaces | はい | はい |

## <a name="microsoftmachinelearningcompute"></a>Microsoft.MachineLearningCompute

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション |
> | ------------- | ----------- | ---------- |
> | operationalizationclusters | いいえ | いいえ |

## <a name="microsoftmachinelearningexperimentation"></a>Microsoft.MachineLearningExperimentation

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション |
> | ------------- | ----------- | ---------- |
> | accounts | いいえ | いいえ |
> | accounts/workspaces | いいえ | いいえ |
> | accounts/workspaces/projects | いいえ | いいえ |
> | teamaccounts | いいえ | いいえ |
> | teamaccounts/workspaces | いいえ | いいえ |
> | teamaccounts/workspaces/projects | いいえ | いいえ |

## <a name="microsoftmachinelearningmodelmanagement"></a>Microsoft.MachineLearningModelManagement

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション |
> | ------------- | ----------- | ---------- |
> | accounts | いいえ | いいえ |

## <a name="microsoftmachinelearningoperationalization"></a>Microsoft.MachineLearningOperationalization

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション |
> | ------------- | ----------- | ---------- |
> | hostingaccounts | いいえ | いいえ |

## <a name="microsoftmachinelearningservices"></a>Microsoft.MachineLearningServices

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション |
> | ------------- | ----------- | ---------- |
> | workspaces | いいえ | いいえ |

## <a name="microsoftmanagedidentity"></a>Microsoft.ManagedIdentity

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション |
> | ------------- | ----------- | ---------- |
> | ID | いいえ | いいえ |
> | userassignedidentities | いいえ | いいえ |

## <a name="microsoftmanagedservices"></a>Microsoft.ManagedServices

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション |
> | ------------- | ----------- | ---------- |
> | registrationassignments | いいえ | いいえ |
> | registrationdefinitions | いいえ | いいえ |

## <a name="microsoftmaps"></a>Microsoft.Maps

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション |
> | ------------- | ----------- | ---------- |
> | accounts | はい | はい |

## <a name="microsoftmarketplaceapps"></a>Microsoft.MarketplaceApps

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション |
> | ------------- | ----------- | ---------- |
> | classicdevservices | いいえ | いいえ |

## <a name="microsoftmedia"></a>Microsoft.Media

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション |
> | ------------- | ----------- | ---------- |
> | mediaservices | はい | はい |
> | mediaservices/liveevents | はい | はい |
> | mediaservices/streamingendpoints | はい | はい |

## <a name="microsoftmicroservices4spring"></a>Microsoft.Microservices4Spring

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション |
> | ------------- | ----------- | ---------- |
> | appclusters | いいえ | いいえ |

## <a name="microsoftmigrate"></a>Microsoft.Migrate

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション |
> | ------------- | ----------- | ---------- |
> | assessmentprojects | はい | はい |
> | migrateprojects | はい | はい |
> | projects | いいえ | いいえ |

## <a name="microsoftnetapp"></a>Microsoft.NetApp

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション |
> | ------------- | ----------- | ---------- |
> | netappaccounts | いいえ | いいえ |
> | netappaccounts/backuppolicies | いいえ | いいえ |
> | netappaccounts/capacitypools | いいえ | いいえ |
> | netappaccounts/capacitypools/volumes | いいえ | いいえ |
> | netappaccounts/capacitypools/volumes/mounttargets | いいえ | いいえ |
> | netappaccounts/capacitypools/volumes/snapshots | いいえ | いいえ |

## <a name="microsoftnetwork"></a>Microsoft.Network

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション |
> | ------------- | ----------- | ---------- |
> | applicationgateways | いいえ | いいえ |
> | applicationgatewaywebapplicationfirewallpolicies | いいえ | いいえ |
> | applicationsecuritygroups | はい | はい |
> | azurefirewalls | はい | はい |
> | bastionhosts | いいえ | いいえ |
> | connections | はい | はい |
> | ddoscustompolicies | はい | はい |
> | ddosprotectionplans | いいえ | いいえ |
> | dnszones | はい | はい |
> | expressroutecircuits | いいえ | いいえ |
> | expressroutegateways | いいえ | いいえ |
> | frontdoors | いいえ | いいえ |
> | frontdoorwebapplicationfirewallpolicies | いいえ | いいえ |
> | loadbalancers | はい - Basic SKU<br>いいえ - Standard SKU | はい - Basic SKU<br>いいえ - Standard SKU |
> | localnetworkgateways | はい | はい |
> | networkexperimentprofiles | はい | はい |
> | networkintentpolicies | はい | はい |
> | networkinterfaces | はい | はい |
> | networkprofiles | いいえ | いいえ |
> | networksecuritygroups | はい | はい |
> | networkwatchers | はい | はい |
> | networkwatchers/connectionmonitors | はい | はい |
> | networkwatchers/flowlogs | はい | はい |
> | networkwatchers/lenses | はい | はい |
> | networkwatchers/pingmeshes | はい | はい |
> | p2svpngateways | いいえ | いいえ |
> | privatednszones | はい | はい |
> | privatednszones/virtualnetworklinks | はい | はい |
> | privateendpointredirectmaps | いいえ | いいえ |
> | privateendpoints | いいえ | いいえ |
> | privatelinkservices | いいえ | いいえ |
> | publicipaddresses | はい - Basic SKU<br>いいえ - Standard SKU | はい - Basic SKU<br>いいえ - Standard SKU |
> | publicipprefixes | はい | はい |
> | routefilters | いいえ | いいえ |
> | routetables | はい | はい |
> | serviceendpointpolicies | はい | はい |
> | trafficmanagerprofiles | はい | はい |
> | virtualhubs | いいえ | いいえ |
> | virtualnetworkgateways | はい | はい |
> | virtualnetworks | はい | はい |
> | virtualnetworktaps | いいえ | いいえ |
> | virtualrouters | はい | はい |
> | virtualwans | いいえ | いいえ |
> | vpngateways (仮想 WAN) | いいえ | いいえ |
> | vpnserverconfigurations | いいえ | いいえ |
> | vpnsites (仮想 WAN) | いいえ | いいえ |
> | webapplicationfirewallpolicies | はい | はい |

> [!IMPORTANT]
> [ネットワークの移動ガイダンス](./move-limitations/networking-move-limitations.md)をご覧ください。

## <a name="microsoftnotificationhubs"></a>Microsoft.NotificationHubs

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション |
> | ------------- | ----------- | ---------- |
> | namespaces | はい | はい |
> | namespaces/notificationhubs | はい | はい |

## <a name="microsoftobjectstore"></a>Microsoft.ObjectStore

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション |
> | ------------- | ----------- | ---------- |
> | osnamespaces | はい | はい |

## <a name="microsoftoperationalinsights"></a>Microsoft.OperationalInsights

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション |
> | ------------- | ----------- | ---------- |
> | storageinsightconfigs | いいえ | いいえ |
> | workspaces | はい | はい |

> [!IMPORTANT]
> 新しいサブスクリプションへの移動によって[サブスクリプション クォータ](azure-subscription-service-limits.md#azure-monitor-limits)を超えないようにします。

## <a name="microsoftoperationsmanagement"></a>Microsoft.OperationsManagement

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション |
> | ------------- | ----------- | ---------- |
> | managementassociations | いいえ | いいえ |
> | managementconfigurations | はい | はい |
> | solutions | はい | はい |
> | views | はい | はい |

## <a name="microsoftpeering"></a>Microsoft.Peering

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション |
> | ------------- | ----------- | ---------- |
> | peerings | はい | はい |
> | peeringservices | いいえ | いいえ |

## <a name="microsoftpolicyinsights"></a>Microsoft.PolicyInsights

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション |
> | ------------- | ----------- | ---------- |
> | policyevents | いいえ | いいえ |
> | policystates | いいえ | いいえ |
> | policytrackedresources | いいえ | いいえ |
> | remediations | いいえ | いいえ |

## <a name="microsoftportal"></a>Microsoft.Portal

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション |
> | ------------- | ----------- | ---------- |
> | dashboards | はい | はい |

## <a name="microsoftportalsdk"></a>Microsoft.PortalSdk

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション |
> | ------------- | ----------- | ---------- |
> | rootresources | いいえ | いいえ |

## <a name="microsoftpowerbi"></a>Microsoft.PowerBI

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション |
> | ------------- | ----------- | ---------- |
> | workspacecollections | はい | はい |

## <a name="microsoftpowerbidedicated"></a>Microsoft.PowerBIDedicated

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション |
> | ------------- | ----------- | ---------- |
> | capacities | はい | はい |

## <a name="microsoftprojectbabylon"></a>Microsoft.ProjectBabylon

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション |
> | ------------- | ----------- | ---------- |
> | accounts | いいえ | いいえ |

## <a name="microsoftprojectoxford"></a>Microsoft.ProjectOxford

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション |
> | ------------- | ----------- | ---------- |
> | accounts | いいえ | いいえ |

## <a name="microsoftproviderhub"></a>Microsoft.ProviderHub

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション |
> | ------------- | ----------- | ---------- |
> | rollouts | いいえ | いいえ |

## <a name="microsoftrecoveryservices"></a>Microsoft.RecoveryServices

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション |
> | ------------- | ----------- | ---------- |
> | backupprotecteditems | いいえ | いいえ |
> | replicationeligibilityresults | いいえ | いいえ |
> | vaults | はい | はい |

> [!IMPORTANT]
> [Recovery Services の移動のガイダンス](../../backup/backup-azure-move-recovery-services-vault.md?toc=/azure/azure-resource-manager/toc.json)に関する記事をご覧ください。

## <a name="microsoftrelay"></a>Microsoft.Relay

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション |
> | ------------- | ----------- | ---------- |
> | namespaces | はい | はい |

## <a name="microsoftresourcegraph"></a>Microsoft.ResourceGraph

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション |
> | ------------- | ----------- | ---------- |
> | Query | はい | はい |

## <a name="microsoftresourcehealth"></a>Microsoft.ResourceHealth

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション |
> | ------------- | ----------- | ---------- |
> | availabilitystatuses | いいえ | いいえ |
> | childavailabilitystatuses | いいえ | いいえ |
> | childresources | いいえ | いいえ |
> | events | いいえ | いいえ |
> | notifications | いいえ | いいえ |

## <a name="microsoftresources"></a>Microsoft.Resources

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション |
> | ------------- | ----------- | ---------- |
> | deploymentscripts | いいえ | いいえ |
> | リンク | いいえ | いいえ |
> | tags | いいえ | いいえ |

## <a name="microsoftsaas"></a>Microsoft.SaaS

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション |
> | ------------- | ----------- | ---------- |
> | applications | はい | いいえ |

## <a name="microsoftscheduler"></a>Microsoft.Scheduler

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション |
> | ------------- | ----------- | ---------- |
> | jobcollections | はい | はい |

## <a name="microsoftsearch"></a>Microsoft.Search

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション |
> | ------------- | ----------- | ---------- |
> | searchservices | はい | はい |

> [!IMPORTANT]
> 1 回の操作で異なるリージョンにあるいくつかの Search リソースを一度に移動することはできません。 代わりに、別の操作で移動します。

## <a name="microsoftsecurity"></a>Microsoft.Security

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション |
> | ------------- | ----------- | ---------- |
> | adaptivenetworkhardenings | いいえ | いいえ |
> | advancedthreatprotectionsettings | いいえ | いいえ |
> | assessmentmetadata | いいえ | いいえ |
> | assessments | いいえ | いいえ |
> | automations | はい | はい |
> | complianceresults | いいえ | いいえ |
> | compliances | いいえ | いいえ |
> | datacollectionagents | いいえ | いいえ |
> | datacollectionresults | いいえ | いいえ |
> | devicesecuritygroups | いいえ | いいえ |
> | informationprotectionpolicies | いいえ | いいえ |
> | iotsecuritysolutions | はい | はい |
> | servervulnerabilityassessments | いいえ | いいえ |

## <a name="microsoftsecurityinsights"></a>Microsoft.SecurityInsights

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション |
> | ------------- | ----------- | ---------- |
> | aggregations | いいえ | いいえ |
> | alertrules | いいえ | いいえ |
> | alertruletemplates | いいえ | いいえ |
> | bookmarks | いいえ | いいえ |
> | cases | いいえ | いいえ |
> | dataconnectors | いいえ | いいえ |
> | entities | いいえ | いいえ |
> | entityqueries | いいえ | いいえ |
> | officeconsents | いいえ | いいえ |
> | settings | いいえ | いいえ |

## <a name="microsoftservermanagement"></a>Microsoft.ServerManagement

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション |
> | ------------- | ----------- | ---------- |
> | gateways | いいえ | いいえ |
> | nodes | いいえ | いいえ |

## <a name="microsoftservicebus"></a>Microsoft.ServiceBus

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション |
> | ------------- | ----------- | ---------- |
> | namespaces | はい | はい |

## <a name="microsoftservicefabric"></a>Microsoft.ServiceFabric

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション |
> | ------------- | ----------- | ---------- |
> | applications | いいえ | いいえ |
> | clusters | はい | はい |
> | clusters/applications | いいえ | いいえ |
> | containergroups | いいえ | いいえ |
> | containergroupsets | いいえ | いいえ |
> | edgeclusters | いいえ | いいえ |
> | networks | いいえ | いいえ |
> | secretstores | いいえ | いいえ |
> | volumes | いいえ | いいえ |

## <a name="microsoftservicefabricmesh"></a>Microsoft.ServiceFabricMesh

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション |
> | ------------- | ----------- | ---------- |
> | applications | はい | はい |
> | gateways | はい | はい |
> | networks | はい | はい |
> | secrets | はい | はい |
> | volumes | はい | はい |

## <a name="microsoftservices"></a>Microsoft.Services

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション |
> | ------------- | ----------- | ---------- |
> | rollouts | いいえ | いいえ |

## <a name="microsoftsignalrservice"></a>Microsoft.SignalRService

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション |
> | ------------- | ----------- | ---------- |
> | signalr | はい | はい |

## <a name="microsoftsoftwareplan"></a>Microsoft.SoftwarePlan

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション |
> | ------------- | ----------- | ---------- |
> | hybridusebenefits | いいえ | いいえ |

## <a name="microsoftsolutions"></a>Microsoft.Solutions

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション |
> | ------------- | ----------- | ---------- |
> | applicationdefinitions | いいえ | いいえ |
> | applications | いいえ | いいえ |
> | jitrequests | いいえ | いいえ |

## <a name="microsoftsql"></a>Microsoft.Sql

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション |
> | ------------- | ----------- | ---------- |
> | instancepools | いいえ | いいえ |
> | managedinstances | いいえ | いいえ |
> | managedinstances/databases | いいえ | いいえ |
> | servers | はい | はい |
> | servers/databases | はい | はい |
> | servers/elasticpools | はい | はい |
> | virtualclusters | はい | はい |

> [!IMPORTANT]
> データベースとサーバーは同じリソース グループ内に存在する必要があります。 SQL Server を移動すると、そのデータベースもすべて移動されます。 この動作は、Azure SQL Database と Azure SQL Data Warehouse データベースに適用されます。

## <a name="microsoftsqlvirtualmachine"></a>Microsoft.SqlVirtualMachine

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション |
> | ------------- | ----------- | ---------- |
> | sqlvirtualmachinegroups | はい | はい |
> | sqlvirtualmachines | はい | はい |

## <a name="microsoftsqlvm"></a>Microsoft.SqlVM

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション |
> | ------------- | ----------- | ---------- |
> | dwvm | いいえ | いいえ |

## <a name="microsoftstorage"></a>Microsoft.Storage

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション |
> | ------------- | ----------- | ---------- |
> | storageaccounts | はい | はい |

## <a name="microsoftstoragesync"></a>Microsoft.StorageSync

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション |
> | ------------- | ----------- | ---------- |
> | storagesyncservices | はい | はい |

## <a name="microsoftstoragesyncdev"></a>Microsoft.StorageSyncDev

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション |
> | ------------- | ----------- | ---------- |
> | storagesyncservices | いいえ | いいえ |

## <a name="microsoftstoragesyncint"></a>Microsoft.StorageSyncInt

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション |
> | ------------- | ----------- | ---------- |
> | storagesyncservices | いいえ | いいえ |

## <a name="microsoftstorsimple"></a>Microsoft.StorSimple

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション |
> | ------------- | ----------- | ---------- |
> | managers | いいえ | いいえ |

## <a name="microsoftstreamanalytics"></a>Microsoft.StreamAnalytics

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション |
> | ------------- | ----------- | ---------- |
> | streamingjobs | はい | はい |

> [!IMPORTANT]
> 実行中状態の Stream Analytics ジョブは移動できません。

## <a name="microsoftstreamanalyticsexplorer"></a>Microsoft.StreamAnalyticsExplorer

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション |
> | ------------- | ----------- | ---------- |
> | 環境 | いいえ | いいえ |
> | environments/eventsources | いいえ | いいえ |
> | instances | いいえ | いいえ |
> | instances/environments | いいえ | いいえ |
> | instances/environments/eventsources | いいえ | いいえ |

## <a name="microsoftsubscription"></a>Microsoft.Subscription

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション |
> | ------------- | ----------- | ---------- |
> | createsubscription | いいえ | いいえ |

## <a name="microsoftsupport"></a>microsoft.support

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション |
> | ------------- | ----------- | ---------- |
> | createsupportticket | いいえ | いいえ |
> | supporttickets | いいえ | いいえ |

## <a name="microsoftterraformoss"></a>Microsoft.TerraformOSS

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション |
> | ------------- | ----------- | ---------- |
> | providerregistrations | いいえ | いいえ |
> | resources | いいえ | いいえ |

## <a name="microsofttimeseriesinsights"></a>Microsoft.TimeSeriesInsights

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション |
> | ------------- | ----------- | ---------- |
> | 環境 | はい | はい |
> | environments/eventsources | はい | はい |
> | environments/referencedatasets | はい | はい |

## <a name="microsofttoken"></a>Microsoft.Token

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション |
> | ------------- | ----------- | ---------- |
> | stores | はい | はい |

## <a name="microsoftvmwarecloudsimple"></a>Microsoft.VMwareCloudSimple

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション |
> | ------------- | ----------- | ---------- |
> | dedicatedcloudnodes | いいえ | いいえ |
> | dedicatedcloudservices | いいえ | いいえ |
> | virtualmachines | いいえ | いいえ |

## <a name="microsoftvsonline"></a>Microsoft.VSOnline

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション |
> | ------------- | ----------- | ---------- |
> | accounts | はい | はい |
> | plans | はい | はい |

## <a name="microsoftweb"></a>Microsoft.Web

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション |
> | ------------- | ----------- | ---------- |
> | certificates | いいえ | はい |
> | connectiongateways | はい | はい |
> | connections | はい | はい |
> | customapis | はい | はい |
> | hostingenvironments | いいえ | いいえ |
> | serverfarms | はい | はい |
> | sites | はい | はい |
> | sites/premieraddons | はい | はい |
> | sites/slots | はい | はい |
> | staticsites | いいえ | いいえ |

> [!IMPORTANT]
> [App Service move guidance (App Service の移動のガイダンス)](./move-limitations/app-service-move-limitations.md) に関する記事をご覧ください。

## <a name="microsoftwindowsiot"></a>Microsoft.WindowsIoT

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション |
> | ------------- | ----------- | ---------- |
> | deviceservices | いいえ | いいえ |

## <a name="microsoftworkloadmonitor"></a>Microsoft.WorkloadMonitor

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション |
> | ------------- | ----------- | ---------- |
> | components | いいえ | いいえ |
> | monitorinstances | いいえ | いいえ |
> | monitors | いいえ | いいえ |
> | notificationsettings | いいえ | いいえ |

## <a name="third-party-services"></a>サード パーティーのサービス

サード パーティのサービスは現在、移動操作をサポートしていません。

## <a name="next-steps"></a>次のステップ
リソースの移動のコマンドについては、「[新しいリソース グループまたはサブスクリプションへのリソースの移動](move-resource-group-and-subscription.md)」を参照してください。

コンマ区切りの値のファイルと同じデータを取得するには、[move-support-resources.csv](https://github.com/tfitzmac/resource-capabilities/blob/master/move-support-resources.csv) をダウンロードします。
