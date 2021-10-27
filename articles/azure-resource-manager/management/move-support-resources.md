---
title: リソースの種類別の移動操作のサポート
description: 新しいリソース グループ、サブスクリプション、またはリージョンに移動できる Azure リソースの種類を一覧表示します。
ms.topic: conceptual
ms.date: 10/15/2021
ms.openlocfilehash: 98685bfd969cd02848330c8abb3108fa044a25a0
ms.sourcegitcommit: 37cc33d25f2daea40b6158a8a56b08641bca0a43
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/15/2021
ms.locfileid: "130071418"
---
# <a name="move-operation-support-for-resources"></a>リソースの操作のサポートの移動

この記事では、Azure リソースの種類は、移動操作をサポートしているかどうかを示します。 また、リソースを移動するときに考慮すべき特別な条件に関する情報も提供します。

> [!IMPORTANT]
> ほとんどの場合、親リソースから独立して子リソースを移動することはできません。 子リソースのリソースの種類は、`<resource-provider-namespace>/<parent-resource>/<child-resource>` という形式です。 たとえば、`Microsoft.ServiceBus/namespaces/queues` は `Microsoft.ServiceBus/namespaces` の子リソースです。 親リソースを移動すると、子リソースもそれと一緒に自動的に移動されます。 この記事で子リソースが示されていない場合は、親リソースと共に移動されるものと見なすことができます。 親リソースで移動がサポートされていない場合、子リソースを移動することはできません。

リソース プロバイダーの名前空間に移動します。
> [!div class="op_single_selector"]
> - [Microsoft.AAD](#microsoftaad)
> - [microsoft.aadiam](#microsoftaadiam)
> - [Microsoft.Addons](#microsoftaddons)
> - [Microsoft.ADHybridHealthService](#microsoftadhybridhealthservice)
> - [Microsoft.Advisor](#microsoftadvisor)
> - [Microsoft.AlertsManagement](#microsoftalertsmanagement)
> - [Microsoft.AnalysisServices](#microsoftanalysisservices)
> - [Microsoft.ApiManagement](#microsoftapimanagement)
> - [Microsoft.AppConfiguration](#microsoftappconfiguration)
> - [Microsoft.AppPlatform](#microsoftappplatform)
> - [Microsoft.AppService](#microsoftappservice)
> - [Microsoft.Attestation](#microsoftattestation)
> - [Microsoft.Authorization](#microsoftauthorization)
> - [Microsoft.Automation](#microsoftautomation)
> - [Microsoft.AVS](#microsoftavs)
> - [Microsoft.AzureActiveDirectory](#microsoftazureactivedirectory)
> - [Microsoft.AzureData](#microsoftazuredata)
> - [Microsoft.AzureStack](#microsoftazurestack)
> - [Microsoft.AzureStackHCI](#microsoftazurestackhci)
> - [Microsoft.Batch](#microsoftbatch)
> - [Microsoft.Billing](#microsoftbilling)
> - [Microsoft.BingMaps](#microsoftbingmaps)
> - [Microsoft.BizTalkServices](#microsoftbiztalkservices)
> - [Microsoft.Blockchain](#microsoftblockchain)
> - [Microsoft.BlockchainTokens](#microsoftblockchaintokens)
> - [Microsoft.Blueprint](#microsoftblueprint)
> - [Microsoft.BotService](#microsoftbotservice)
> - [Microsoft.Cache](#microsoftcache)
> - [Microsoft.Capacity](#microsoftcapacity)
> - [Microsoft.Cdn](#microsoftcdn)
> - [Microsoft.CertificateRegistration](#microsoftcertificateregistration)
> - [Microsoft.ClassicCompute](#microsoftclassiccompute)
> - [Microsoft.ClassicInfrastructureMigrate](#microsoftclassicinfrastructuremigrate)
> - [Microsoft.ClassicNetwork](#microsoftclassicnetwork)
> - [Microsoft.ClassicStorage](#microsoftclassicstorage)
> - [Microsoft.ClassicSubscription](#microsoftclassicsubscription)
> - [Microsoft.CognitiveServices](#microsoftcognitiveservices)
> - [Microsoft.Commerce](#microsoftcommerce)
> - [Microsoft.Compute](#microsoftcompute)
> - [Microsoft.Consumption](#microsoftconsumption)
> - [Microsoft.ContainerInstance](#microsoftcontainerinstance)
> - [Microsoft.ContainerRegistry](#microsoftcontainerregistry)
> - [Microsoft.ContainerService](#microsoftcontainerservice)
> - [Microsoft.ContentModerator](#microsoftcontentmoderator)
> - [Microsoft.CortanaAnalytics](#microsoftcortanaanalytics)
> - [Microsoft.CostManagement](#microsoftcostmanagement)
> - [Microsoft.CustomerInsights](#microsoftcustomerinsights)
> - [Microsoft.CustomerLockbox](#microsoftcustomerlockbox)
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
> - [Microsoft.DesktopVirtualization](#microsoftdesktopvirtualization)
> - [Microsoft.Devices](#microsoftdevices)
> - [Microsoft.DevOps](#microsoftdevops)
> - [Microsoft.DevSpaces](#microsoftdevspaces)
> - [Microsoft.DevTestLab](#microsoftdevtestlab)
> - [Microsoft.DigitalTwins](#microsoftdigitaltwins)
> - [Microsoft.DocumentDB](#microsoftdocumentdb)
> - [Microsoft.DomainRegistration](#microsoftdomainregistration)
> - [Microsoft.EnterpriseKnowledgeGraph](#microsoftenterpriseknowledgegraph)
> - [Microsoft.EventGrid](#microsofteventgrid)
> - [Microsoft.EventHub](#microsofteventhub)
> - [Microsoft.Experimentation](#microsoftexperimentation)
> - [Microsoft.ExtendedLocation](#microsoftextendedlocation)
> - [Microsoft.Falcon](#microsoftfalcon)
> - [Microsoft.Features](#microsoftfeatures)
> - [Microsoft.Genomics](#microsoftgenomics)
> - [Microsoft.GuestConfiguration](#microsoftguestconfiguration)
> - [Microsoft.HanaOnAzure](#microsofthanaonazure)
> - [Microsoft.HardwareSecurityModules](#microsofthardwaresecuritymodules)
> - [Microsoft.HDInsight](#microsofthdinsight)
> - [Microsoft.HealthcareApis](#microsofthealthcareapis)
> - [Microsoft.HybridCompute](#microsofthybridcompute)
> - [Microsoft.HybridData](#microsofthybriddata)
> - [Microsoft.HybridNetwork](#microsofthybridnetwork)
> - [Microsoft.Hydra](#microsofthydra)
> - [Microsoft.ImportExport](#microsoftimportexport)
> - [microsoft.insights](#microsoftinsights)
> - [Microsoft.IoTCentral](#microsoftiotcentral)
> - [Microsoft.IoTHub](#microsoftiothub)
> - [Microsoft.IoTSpaces](#microsoftiotspaces)
> - [Microsoft.KeyVault](#microsoftkeyvault)
> - [Microsoft.Kubernetes](#microsoftkubernetes)
> - [Microsoft.KubernetesConfiguration](#microsoftkubernetesconfiguration)
> - [Microsoft.Kusto](#microsoftkusto)
> - [Microsoft.LabServices](#microsoftlabservices)
> - [Microsoft.LocationBasedServices](#microsoftlocationbasedservices)
> - [Microsoft.LocationServices](#microsoftlocationservices)
> - [Microsoft.Logic](#microsoftlogic)
> - [Microsoft.MachineLearning](#microsoftmachinelearning)
> - [Microsoft.MachineLearningCompute](#microsoftmachinelearningcompute)
> - [Microsoft.MachineLearningExperimentation](#microsoftmachinelearningexperimentation)
> - [Microsoft.MachineLearningModelManagement](#microsoftmachinelearningmodelmanagement)
> - [Microsoft.MachineLearningServices](#microsoftmachinelearningservices)
> - [Microsoft.Maintenance](#microsoftmaintenance)
> - [Microsoft.ManagedIdentity](#microsoftmanagedidentity)
> - [Microsoft.ManagedNetwork](#microsoftmanagednetwork)
> - [Microsoft.ManagedServices](#microsoftmanagedservices)
> - [Microsoft.Management](#microsoftmanagement)
> - [Microsoft.Maps](#microsoftmaps)
> - [Microsoft.Marketplace](#microsoftmarketplace)
> - [Microsoft.MarketplaceApps](#microsoftmarketplaceapps)
> - [Microsoft.MarketplaceOrdering](#microsoftmarketplaceordering)
> - [Microsoft.Media](#microsoftmedia)
> - [Microsoft.Microservices4Spring](#microsoftmicroservices4spring)
> - [Microsoft.Migrate](#microsoftmigrate)
> - [Microsoft.MixedReality](#microsoftmixedreality)
> - [Microsoft.NetApp](#microsoftnetapp)
> - [Microsoft.Network](#microsoftnetwork)
> - [Microsoft.NotificationHubs](#microsoftnotificationhubs)
> - [Microsoft.ObjectStore](#microsoftobjectstore)
> - [Microsoft.OffAzure](#microsoftoffazure)
> - [Microsoft.OperationalInsights](#microsoftoperationalinsights)
> - [Microsoft.OperationsManagement](#microsoftoperationsmanagement)
> - [Microsoft.Peering](#microsoftpeering)
> - [Microsoft.PolicyInsights](#microsoftpolicyinsights)
> - [Microsoft.Portal](#microsoftportal)
> - [Microsoft.PowerBI](#microsoftpowerbi)
> - [Microsoft.PowerBIDedicated](#microsoftpowerbidedicated)
> - [Microsoft.ProjectBabylon](#microsoftprojectbabylon)
> - [Microsoft.ProviderHub](#microsoftproviderhub)
> - [Microsoft.Quantum](#microsoftquantum)
> - [Microsoft.RecoveryServices](#microsoftrecoveryservices)
> - [Microsoft.RedHatOpenShift](#microsoftredhatopenshift)
> - [Microsoft.Relay](#microsoftrelay)
> - [Microsoft.ResourceGraph](#microsoftresourcegraph)
> - [Microsoft.ResourceHealth](#microsoftresourcehealth)
> - [Microsoft.Resources](#microsoftresources)
> - [Microsoft.SaaS](#microsoftsaas)
> - [Microsoft.Search](#microsoftsearch)
> - [Microsoft.Security](#microsoftsecurity)
> - [Microsoft.SecurityInsights](#microsoftsecurityinsights)
> - [Microsoft.SerialConsole](#microsoftserialconsole)
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
> - [Microsoft.Storage](#microsoftstorage)
> - [Microsoft.StorageCache](#microsoftstoragecache)
> - [Microsoft.StorageSync](#microsoftstoragesync)
> - [Microsoft.StorageSyncDev](#microsoftstoragesyncdev)
> - [Microsoft.StorageSyncInt](#microsoftstoragesyncint)
> - [Microsoft.StorSimple](#microsoftstorsimple)
> - [Microsoft.StreamAnalytics](#microsoftstreamanalytics)
> - [Microsoft.StreamAnalyticsExplorer](#microsoftstreamanalyticsexplorer)
> - [Microsoft.Subscription](#microsoftsubscription)
> - [microsoft.support](#microsoftsupport)
> - [Microsoft.Synapse](#microsoftsynapse)
> - [Microsoft.TimeSeriesInsights](#microsofttimeseriesinsights)
> - [Microsoft.Token](#microsofttoken)
> - [Microsoft.VirtualMachineImages](#microsoftvirtualmachineimages)
> - [microsoft.visualstudio](#microsoftvisualstudio)
> - [Microsoft.VMware](#microsoftvmware)
> - [Microsoft.VMwareCloudSimple](#microsoftvmwarecloudsimple)
> - [Microsoft.VnfManager](#microsoftvnfmanager)
> - [Microsoft.VSOnline](#microsoftvsonline)
> - [Microsoft.Web](#microsoftweb)
> - [Microsoft.WindowsESU](#microsoftwindowsesu)
> - [Microsoft.WindowsIoT](#microsoftwindowsiot)
> - [Microsoft.WorkloadBuilder](#microsoftworkloadbuilder)
> - [Microsoft.WorkloadMonitor](#microsoftworkloadmonitor)

## <a name="microsoftaad"></a>Microsoft.AAD

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション | リージョンの移動 |
> | ------------- | ----------- | ---------- | ----------- |
> | domainservices | いいえ | いいえ |  いいえ |

## <a name="microsoftaadiam"></a>microsoft.aadiam

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション | リージョンの移動 |
> | ------------- | ----------- | ---------- | ----------- |
> | diagnosticsettings | いいえ | いいえ | いいえ |
> | diagnosticsettingscategories | いいえ | いいえ | いいえ |
> | privatelinkforazuread | はい | はい | いいえ |
> | tenants | はい | はい | いいえ |

## <a name="microsoftaddons"></a>Microsoft.Addons

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション | リージョンの移動 |
> | ------------- | ----------- | ---------- | ----------- |
> | supportproviders | いいえ | いいえ | いいえ |

## <a name="microsoftadhybridhealthservice"></a>Microsoft.ADHybridHealthService

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション | リージョンの移動 |
> | ------------- | ----------- | ---------- | ----------- |
> | aadsupportcases | いいえ | いいえ | いいえ |
> | addsservices | いいえ | いいえ | いいえ |
> | agents | いいえ | いいえ | いいえ |
> | anonymousapiusers | いいえ | いいえ | いいえ |
> | configuration | いいえ | いいえ | いいえ |
> | logs | いいえ | いいえ | いいえ |
> | reports | いいえ | いいえ | いいえ |
> | servicehealthmetrics | いいえ | いいえ | いいえ |
> | services | いいえ | いいえ | いいえ |

## <a name="microsoftadvisor"></a>Microsoft.Advisor

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション | リージョンの移動 |
> | ------------- | ----------- | ---------- | ----------- |
> | configuration | いいえ | いいえ | いいえ |
> | generaterecommendations | いいえ | いいえ | いいえ |
> | metadata | いいえ | いいえ | いいえ |
> | recommendations | いいえ | いいえ | いいえ |
> | suppressions | いいえ | いいえ | いいえ |

## <a name="microsoftalertsmanagement"></a>Microsoft.AlertsManagement

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション | リージョンの移動 |
> | ------------- | ----------- | ---------- | ----------- |
> | actionrules | はい | はい | いいえ |
> | alerts | いいえ | いいえ | いいえ |
> | alertslist | いいえ | いいえ | いいえ |
> | alertsmetadata | いいえ | いいえ | いいえ |
> | alertssummary | いいえ | いいえ | いいえ |
> | alertssummarylist | いいえ | いいえ | いいえ |
> | smartdetectoralertrules | はい | はい | いいえ |
> | smartgroups | いいえ | いいえ | いいえ |

## <a name="microsoftanalysisservices"></a>Microsoft.AnalysisServices

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション | リージョンの移動 |
> | ------------- | ----------- | ---------- | ----------- |
> | servers | はい | はい | いいえ |

## <a name="microsoftapimanagement"></a>Microsoft.ApiManagement

> [!IMPORTANT]
> 従量課金 SKU に設定されている API Management サービスは移動できません。

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション | リージョンの移動 |
> | ------------- | ----------- | ---------- | ----------- |
> | reportfeedback | いいえ | いいえ | いいえ |
> | サービス (service) | はい | はい | はい (テンプレートを使用) <br/><br/> [Azure API Management をリージョン間で移行する](../../api-management/api-management-howto-migrate.md)。 |

## <a name="microsoftappconfiguration"></a>Microsoft.AppConfiguration

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション | リージョンの移動 |
> | ------------- | ----------- | ---------- | ----------- |
> | configurationstores | はい | はい | いいえ |
> | configurationstores / eventgridfilters | いいえ | いいえ | いいえ |

## <a name="microsoftappplatform"></a>Microsoft.AppPlatform

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション | リージョンの移動 |
> | ------------- | ----------- | ---------- | ----------- |
> | spring | はい | はい | いいえ |

## <a name="microsoftappservice"></a>Microsoft.AppService

> [!IMPORTANT]
> [App Service move guidance (App Service の移動のガイダンス)](./move-limitations/app-service-move-limitations.md) に関する記事をご覧ください。

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション | リージョンの移動 |
> | ------------- | ----------- | ---------- | ----------- |
> | apiapps | いいえ | いいえ | はい (テンプレートを使用)<br/><br/> [App Service アプリを別のリージョンに移動する](../../app-service/manage-move-across-regions.md) |
> | appidentities | いいえ | いいえ | いいえ |
> | gateways | いいえ | いいえ | いいえ |

## <a name="microsoftattestation"></a>Microsoft.Attestation

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション | リージョンの移動 |
> | ------------- | ----------- | ---------- | ----------- |
> | attestationproviders | はい | はい | いいえ |

## <a name="microsoftauthorization"></a>Microsoft.Authorization

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション | リージョンの移動 |
> | ------------- | ----------- | ---------- | ----------- |
> | classicadministrators | いいえ | いいえ | いいえ |
> | dataaliases | いいえ | いいえ | いいえ |
> | denyassignments | いいえ | いいえ | いいえ |
> | elevateaccess | いいえ | いいえ | いいえ |
> | findorphanroleassignments | いいえ | いいえ | いいえ |
> | locks | いいえ | いいえ | いいえ |
> | 権限 | いいえ | いいえ | いいえ |
> | policyassignments | いいえ | いいえ | いいえ |
> | policydefinitions | いいえ | いいえ | いいえ |
> | policysetdefinitions | いいえ | いいえ | いいえ |
> | privatelinkassociations | いいえ | いいえ | いいえ |
> | resourcemanagementprivatelinks | いいえ | いいえ | いいえ |
> | roleassignments | いいえ | いいえ | いいえ |
> | roleassignmentsusagemetrics | いいえ | いいえ | いいえ |
> | roledefinitions | いいえ | いいえ | いいえ |

## <a name="microsoftautomation"></a>Microsoft.Automation

> [!IMPORTANT]
> Runbook は Automation アカウントと同じリソース グループに存在する必要があります。
>
> 詳しくは、「[Azure Automation アカウントを別のサブスクリプションに移動する](../../automation/how-to/move-account.md?toc=/azure/azure-resource-manager/toc.json)」をご覧ください。

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション | リージョンの移動 |
> | ------------- | ----------- | ---------- | ----------- |
> | automationaccounts | はい | はい | はい (テンプレートを使用) <br/><br/> [geo レプリケーションを使用する](../../automation/automation-managing-data.md#geo-replication-in-azure-automation) |
> | automationaccounts/configurations | はい | はい | いいえ |
> | automationaccounts/runbooks | はい | はい | いいえ |

## <a name="microsoftavs"></a>Microsoft.AVS

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション | リージョンの移動 |
> | ------------- | ----------- | ---------- | ----------- |
> | privateclouds | はい | はい | いいえ |

## <a name="microsoftazureactivedirectory"></a>Microsoft.AzureActiveDirectory

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション | リージョンの移動 |
> | ------------- | ----------- | ---------- | ----------- |
> | b2cdirectories | はい | はい | いいえ |
> | b2ctenants | いいえ | いいえ | いいえ |

## <a name="microsoftazuredata"></a>Microsoft.AzureData

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション | リージョンの移動 |
> | ------------- | ----------- | ---------- | ----------- |
> | datacontrollers | いいえ | いいえ | いいえ |
> | hybriddatamanagers | いいえ | いいえ | いいえ |
> | postgresinstances | いいえ | いいえ | いいえ |
> | sqlinstances | いいえ | いいえ | いいえ |
> | sqlmanagedinstances | いいえ | いいえ | いいえ |
> | sqlserverinstances | いいえ | いいえ | いいえ |
> | sqlserverregistrations | はい | はい | いいえ |

## <a name="microsoftazurestack"></a>Microsoft.AzureStack

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション | リージョンの移動 |
> | ------------- | ----------- | ---------- | ----------- |
> | cloudmanifestfiles | いいえ | いいえ | いいえ |
> | registrations | はい | はい | いいえ |

## <a name="microsoftazurestackhci"></a>Microsoft.AzureStackHCI

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション | リージョンの移動 |
> | ------------- | ----------- | ---------- | ----------- |
> | clusters | いいえ | いいえ | いいえ |

## <a name="microsoftbatch"></a>Microsoft.Batch

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション | リージョンの移動 |
> | ------------- | ----------- | ---------- | ----------- |
> | batchaccounts | はい | はい | Batch アカウントを別のリージョンに直接移行することはできませんが、テンプレートを使用し、テンプレートをエクスポートしてそれを変更し、テンプレートを新しいリージョンにデプロイすることはできます。 <br/><br/> [リージョン間で Batch アカウントを移行する](../../batch/account-move.md)方法を参照してください。 |

## <a name="microsoftbilling"></a>Microsoft.Billing

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション | リージョンの移動 |
> | ------------- | ----------- | ---------- | ----------- |
> | billingaccounts | いいえ | いいえ | いいえ |
> | billingperiods | いいえ | いいえ | いいえ |
> | billingpermissions | いいえ | いいえ | いいえ |
> | billingproperty | いいえ | いいえ | いいえ |
> | billingroleassignments | いいえ | いいえ | いいえ |
> | billingroledefinitions | いいえ | いいえ | いいえ |
> | departments | いいえ | いいえ | いいえ |
> | enrollmentaccounts | いいえ | いいえ | いいえ |
> | invoices | いいえ | いいえ | いいえ |
> | transfers | いいえ | いいえ | いいえ |

## <a name="microsoftbingmaps"></a>Microsoft.BingMaps

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション | リージョンの移動 |
> | ------------- | ----------- | ---------- | ----------- |
> | mapapis | いいえ | いいえ | いいえ |

## <a name="microsoftbiztalkservices"></a>Microsoft.BizTalkServices

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション | リージョンの移動 |
> | ------------- | ----------- | ---------- | ----------- |
> | biztalk | いいえ | いいえ | いいえ |

## <a name="microsoftblockchain"></a>Microsoft.Blockchain

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション | リージョンの移動 |
> | ------------- | ----------- | ---------- | ----------- |
> | blockchainmembers | いいえ | いいえ | いいえ <br/><br/> ブロックチェーン ネットワークでは、異なるリージョンにノードを持つことはできません。 |
> | cordamembers | いいえ | いいえ | いいえ |
> | watchers | いいえ | いいえ | いいえ |

## <a name="microsoftblockchaintokens"></a>Microsoft.BlockchainTokens

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション | リージョンの移動 |
> | ------------- | ----------- | ---------- | ----------- |
> | tokenservices | いいえ | いいえ | いいえ |

## <a name="microsoftblueprint"></a>Microsoft.Blueprint

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション | リージョンの移動 |
> | ------------- | ----------- | ---------- | ----------- |
> | blueprintassignments | いいえ | いいえ | いいえ |
> | blueprints | いいえ | いいえ | いいえ |

## <a name="microsoftbotservice"></a>Microsoft.BotService

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション | リージョンの移動 |
> | ------------- | ----------- | ---------- | ----------- |
> | botservices | はい | はい | いいえ |

## <a name="microsoftcache"></a>Microsoft.Cache

> [!IMPORTANT]
> 仮想ネットワークを使用して Azure Cache for Redis インスタンスが構成されている場合、インスタンスを別のサブスクリプションに移動させることはできません。 「[ネットワーク リソースの移動ガイダンス](./move-limitations/networking-move-limitations.md)」を参照してください。

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション | リージョンの移動 |
> | ------------- | ----------- | ---------- | ----------- |
> | redis | はい | はい | いいえ |
> | redisenterprise | いいえ | いいえ | いいえ |

## <a name="microsoftcapacity"></a>Microsoft.Capacity

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション | リージョンの移動 |
> | ------------- | ----------- | ---------- | ----------- |
> | appliedreservations | いいえ | いいえ | いいえ |
> | calculateexchange | いいえ | いいえ | いいえ |
> | calculateprice | いいえ | いいえ | いいえ |
> | calculatepurchaseprice | いいえ | いいえ | いいえ |
> | catalogs | いいえ | いいえ | いいえ |
> | commercialreservationorders | いいえ | いいえ | いいえ |
> | exchange | いいえ | いいえ | いいえ |
> | reservationorders | いいえ | いいえ | いいえ |
> | reservations | いいえ | いいえ | いいえ |
> | resources | いいえ | いいえ | いいえ |
> | validatereservationorder | いいえ | いいえ | いいえ |

## <a name="microsoftcdn"></a>Microsoft.Cdn

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション | リージョンの移動 |
> | ------------- | ----------- | ---------- | ----------- |
> | cdnwebapplicationfirewallmanagedrulesets | いいえ | いいえ | いいえ |
> | cdnwebapplicationfirewallpolicies | はい | はい | いいえ |
> | edgenodes | いいえ | いいえ | いいえ |
> | profiles | はい | はい | いいえ |
> | profiles/endpoints | はい | はい | いいえ |

## <a name="microsoftcertificateregistration"></a>Microsoft.CertificateRegistration

> [!IMPORTANT]
> [App Service move guidance (App Service の移動のガイダンス)](./move-limitations/app-service-move-limitations.md) に関する記事をご覧ください。

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション | リージョンの移動 |
> | ------------- | ----------- | ---------- | ----------- |
> | certificateorders | はい | はい | いいえ |

## <a name="microsoftclassiccompute"></a>Microsoft.ClassicCompute

> [!IMPORTANT]
> [Classic deployment move guidance (クラシック デプロイの移動のガイダンス)](./move-limitations/classic-model-move-limitations.md) に関する記事をご覧ください。 クラシック デプロイのリソースは、そのシナリオに固有の操作を使用して、サブスクリプション間で移動できます。

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション | リージョンの移動 |
> | ------------- | ----------- | ---------- | ----------- |
> | capabilities | いいえ | いいえ | いいえ |
> | domainnames | はい | いいえ | いいえ |
> | quotas | いいえ | いいえ | いいえ |
> | resourcetypes | いいえ | いいえ | いいえ |
> | validatesubscriptionmoveavailability | いいえ | いいえ | いいえ |
> | virtualmachines | はい | はい | いいえ |

## <a name="microsoftclassicinfrastructuremigrate"></a>Microsoft.ClassicInfrastructureMigrate

> [!IMPORTANT]
> [Classic deployment move guidance (クラシック デプロイの移動のガイダンス)](./move-limitations/classic-model-move-limitations.md) に関する記事をご覧ください。 クラシック デプロイのリソースは、そのシナリオに固有の操作を使用して、サブスクリプション間で移動できます。

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション | リージョンの移動 |
> | ------------- | ----------- | ---------- | ----------- |
> | classicinfrastructureresources | いいえ | いいえ | いいえ |

## <a name="microsoftclassicnetwork"></a>Microsoft.ClassicNetwork

> [!IMPORTANT]
> [Classic deployment move guidance (クラシック デプロイの移動のガイダンス)](./move-limitations/classic-model-move-limitations.md) に関する記事をご覧ください。 クラシック デプロイのリソースは、そのシナリオに固有の操作を使用して、サブスクリプション間で移動できます。

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション | リージョンの移動 |
> | ------------- | ----------- | ---------- | ----------- |
> | capabilities | いいえ | いいえ | いいえ |
> | expressroutecrossconnections | いいえ | いいえ | いいえ |
> | expressroutecrossconnections / peerings | いいえ | いいえ | いいえ |
> | gatewaysupporteddevices | いいえ | いいえ | いいえ |
> | networksecuritygroups | いいえ | いいえ | いいえ |
> | quotas | いいえ | いいえ | いいえ |
> | reservedips | いいえ | いいえ | いいえ |
> | virtualnetworks | いいえ | いいえ | いいえ |

## <a name="microsoftclassicstorage"></a>Microsoft.ClassicStorage

> [!IMPORTANT]
> [Classic deployment move guidance (クラシック デプロイの移動のガイダンス)](./move-limitations/classic-model-move-limitations.md) に関する記事をご覧ください。 クラシック デプロイのリソースは、そのシナリオに固有の操作を使用して、サブスクリプション間で移動できます。

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション | リージョンの移動 |
> | ------------- | ----------- | ---------- | ----------- |
> | disks | いいえ | いいえ | いいえ |
> | images | いいえ | いいえ | いいえ |
> | osimages | いいえ | いいえ | いいえ |
> | osplatformimages | いいえ | いいえ | いいえ |
> | publicimages | いいえ | いいえ | いいえ |
> | quotas | いいえ | いいえ | いいえ |
> | storageaccounts | はい | いいえ | はい |
> | vmimages | いいえ | いいえ | × |

## <a name="microsoftclassicsubscription"></a>Microsoft.ClassicSubscription

> [!IMPORTANT]
> [Classic deployment move guidance (クラシック デプロイの移動のガイダンス)](./move-limitations/classic-model-move-limitations.md) に関する記事をご覧ください。 クラシック デプロイのリソースは、そのシナリオに固有の操作を使用して、サブスクリプション間で移動できます。

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション | リージョンの移動 |
> | ------------- | ----------- | ---------- | ----------- |
> | operations | いいえ | いいえ | いいえ |

## <a name="microsoftcognitiveservices"></a>Microsoft.CognitiveServices

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション | リージョンの移動 |
> | ------------- | ----------- | ---------- | ----------- |
> | accounts | はい | はい | いいえ |
> | Cognitive Search | はい | はい | 手動の手順でサポートされています。<br/><br/> [Azure Cognitive Search サービスを別のリージョンに移行する](../../search/search-howto-move-across-regions.md)方法を参照してください。 |

## <a name="microsoftcommerce"></a>Microsoft.Commerce

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション | リージョンの移動 |
> | ------------- | ----------- | ---------- | ----------- |
> | ratecard | いいえ | いいえ | いいえ |
> | usageaggregates | いいえ | いいえ | いいえ |

## <a name="microsoftcompute"></a>Microsoft.Compute

> [!IMPORTANT]
> [Virtual Machines move guidance (仮想マシンの移動のガイダンス)](./move-limitations/virtual-machines-move-limitations.md) に関する記事をご覧ください。

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション | リージョンの移動 |
> | ------------- | ----------- | ---------- | ----------- |
> | availabilitysets | はい | はい |  はい <br/><br/> 可用性セットを移動するには [Azure Resource Mover](../../resource-mover/tutorial-move-region-virtual-machines.md) を使用します。 |
> | diskaccesses | いいえ | いいえ | いいえ |
> | diskencryptionsets | いいえ | いいえ | いいえ |
> | disks | はい | はい | はい <br/><br/> Azure VM および関連ディスクを移動するには [Azure Resource Mover](../../resource-mover/tutorial-move-region-virtual-machines.md) を使用します。 |
> | galleries | いいえ | いいえ | いいえ |
> | galleries/images | いいえ | いいえ | いいえ |
> | galleries/images/versions | いいえ | いいえ | いいえ |
> | hostgroups | いいえ | いいえ | いいえ |
> | hostgroups/hosts | いいえ | いいえ | いいえ |
> | images | はい | はい | いいえ |
> | proximityplacementgroups | はい | はい | いいえ |
> | restorepointcollections | いいえ | いいえ | いいえ |
> | restorepointcollections / restorepoints | いいえ | いいえ | いいえ |
> | sharedvmextensions | いいえ | いいえ | いいえ |
> | sharedvmimages | いいえ | いいえ | いいえ |
> | sharedvmimages/versions | いいえ | いいえ | いいえ |
> | スナップショット | はい | はい | いいえ |
> | sshpublickeys | いいえ | いいえ | いいえ |
> | virtualmachines | はい | はい | はい <br/><br/> Azure VM を移動するには [Azure Resource Mover](../../resource-mover/tutorial-move-region-virtual-machines.md) を使用します。 |
> | virtualmachines/extensions | はい | はい | いいえ |
> | virtualmachinescalesets | はい | はい | いいえ |

## <a name="microsoftconsumption"></a>Microsoft.Consumption

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション | リージョンの移動 |
> | ------------- | ----------- | ---------- | ----------- |
> | aggregatedcost | いいえ | いいえ | いいえ |
> | balances | いいえ | いいえ | いいえ |
> | budgets | いいえ | いいえ | いいえ |
> | charges | いいえ | いいえ | いいえ |
> | costtags | いいえ | いいえ | いいえ |
> | credits | いいえ | いいえ | いいえ |
> | events | いいえ | いいえ | いいえ |
> | forecasts | いいえ | いいえ | いいえ |
> | lots | いいえ | いいえ | いいえ |
> | marketplaces | いいえ | いいえ | いいえ |
> | pricesheets | いいえ | いいえ | いいえ |
> | products | いいえ | いいえ | いいえ |
> | reservationdetails | いいえ | いいえ | いいえ |
> | reservationrecommendationdetails | いいえ | いいえ | いいえ |
> | reservationrecommendations | いいえ | いいえ | いいえ |
> | reservationsummaries | いいえ | いいえ | いいえ |
> | reservationtransactions | いいえ | いいえ | いいえ |
> | tags | いいえ | いいえ | いいえ |
> | tenants | いいえ | いいえ | いいえ |
> | terms | いいえ | いいえ | いいえ |
> | usagedetails | いいえ | いいえ | いいえ |

## <a name="microsoftcontainerinstance"></a>Microsoft.ContainerInstance

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション | リージョンの移動 |
> | ------------- | ----------- | ---------- | ----------- |
> | containergroups | いいえ | いいえ | いいえ |
> | serviceassociationlinks | いいえ | いいえ | いいえ |

## <a name="microsoftcontainerregistry"></a>Microsoft.ContainerRegistry

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション | リージョンの移動 |
> | ------------- | ----------- | ---------- | ----------- |
> | registries | はい | はい | いいえ |
> | registries / agentpools | はい | はい | いいえ |
> | registries/buildtasks | はい | はい | いいえ |
> | registries/replications | はい | はい | いいえ |
> | registries/tasks | はい | はい | いいえ |
> | registries/webhooks | はい | はい | いいえ |

## <a name="microsoftcontainerservice"></a>Microsoft.ContainerService

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション | リージョンの移動 |
> | ------------- | ----------- | ---------- | ----------- |
> | containerservices | いいえ | いいえ | いいえ |
> | managedclusters | いいえ | いいえ | いいえ |
> | openshiftmanagedclusters | いいえ | いいえ | いいえ |

## <a name="microsoftcontentmoderator"></a>Microsoft.ContentModerator

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション | リージョンの移動 |
> | ------------- | ----------- | ---------- | ----------- |
> | applications | いいえ | いいえ | いいえ |

## <a name="microsoftcortanaanalytics"></a>Microsoft.CortanaAnalytics

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション | リージョンの移動 |
> | ------------- | ----------- | ---------- | ----------- |
> | accounts | いいえ | いいえ | いいえ |

## <a name="microsoftcostmanagement"></a>Microsoft.CostManagement

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション | リージョンの移動 |
> | ------------- | ----------- | ---------- | ----------- |
> | alerts | いいえ | いいえ | いいえ |
> | billingaccounts | いいえ | いいえ | いいえ |
> | budgets | いいえ | いいえ | いいえ |
> | cloudconnectors | いいえ | いいえ | いいえ |
> | connectors | はい | はい | いいえ |
> | departments | いいえ | いいえ | いいえ |
> | dimensions | いいえ | いいえ | いいえ |
> | enrollmentaccounts | いいえ | いいえ | いいえ |
> | exports | いいえ | いいえ | いいえ |
> | externalbillingaccounts | いいえ | いいえ | いいえ |
> | forecast | いいえ | いいえ | いいえ |
> | query | いいえ | いいえ | いいえ |
> | registrations | いいえ | いいえ | いいえ |
> | reportconfigs | いいえ | いいえ | いいえ |
> | reports | いいえ | いいえ | いいえ |
> | settings | いいえ | いいえ | いいえ |
> | showbackrules | いいえ | いいえ | いいえ |
> | views | いいえ | いいえ | いいえ |

## <a name="microsoftcustomerinsights"></a>Microsoft.CustomerInsights

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション | リージョンの移動 |
> | ------------- | ----------- | ---------- | ----------- |
> | hubs | いいえ | いいえ | いいえ |

## <a name="microsoftcustomerlockbox"></a>Microsoft.CustomerLockbox

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション | リージョンの移動 |
> | ------------- | ----------- | ---------- | ----------- |
> | requests | いいえ | いいえ | いいえ |

## <a name="microsoftcustomproviders"></a>Microsoft.CustomProviders

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション | リージョンの移動 |
> | ------------- | ----------- | ---------- | ----------- |
> | associations | いいえ | いいえ | いいえ |
> | resourceproviders | はい | はい | いいえ |

## <a name="microsoftdatabox"></a>Microsoft.DataBox

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション | リージョンの移動 |
> | ------------- | ----------- | ---------- | ----------- |
> | jobs | いいえ | いいえ | いいえ |

## <a name="microsoftdataboxedge"></a>Microsoft.DataBoxEdge

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション | リージョンの移動 |
> | ------------- | ----------- | ---------- | ----------- |
> | availableskus | いいえ | いいえ | いいえ |
> | databoxedgedevices | いいえ | いいえ | いいえ |

## <a name="microsoftdatabricks"></a>Microsoft.Databricks

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション | リージョンの移動 |
> | ------------- | ----------- | ---------- | ----------- |
> | workspaces | いいえ | いいえ | いいえ |

## <a name="microsoftdatacatalog"></a>Microsoft.DataCatalog

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション | リージョンの移動 |
> | ------------- | ----------- | ---------- | ----------- |
> | catalogs | はい | はい | いいえ |
> | datacatalogs | いいえ | いいえ | いいえ |

## <a name="microsoftdataconnect"></a>Microsoft.DataConnect

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション | リージョンの移動 |
> | ------------- | ----------- | ---------- | ----------- |
> | connectionmanagers | いいえ | いいえ | いいえ |

## <a name="microsoftdataexchange"></a>Microsoft.DataExchange

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション | リージョンの移動 |
> | ------------- | ----------- | ---------- | ----------- |
> | packages | いいえ | いいえ | いいえ |
> | plans | いいえ | いいえ | いいえ |

## <a name="microsoftdatafactory"></a>Microsoft.DataFactory

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション | リージョンの移動 |
> | ------------- | ----------- | ---------- | ----------- |
> | datafactories | はい | はい | いいえ |
> | factories | はい | はい | いいえ |

## <a name="microsoftdatalake"></a>Microsoft.DataLake

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション | リージョンの移動 |
> | ------------- | ----------- | ---------- | ----------- |
> | datalakeaccounts | いいえ | いいえ | いいえ |

## <a name="microsoftdatalakeanalytics"></a>Microsoft.DataLakeAnalytics

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション | リージョンの移動 |
> | ------------- | ----------- | ---------- | ----------- |
> | accounts | はい | はい | いいえ |

## <a name="microsoftdatalakestore"></a>Microsoft.DataLakeStore

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション | リージョンの移動 |
> | ------------- | ----------- | ---------- | ----------- |
> | accounts | はい | はい | いいえ |

## <a name="microsoftdatamigration"></a>Microsoft.DataMigration

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション | リージョンの移動 |
> | ------------- | ----------- | ---------- | ----------- |
> | services | いいえ | いいえ | いいえ |
> | services/projects | いいえ | いいえ | いいえ |
> | slots | いいえ | いいえ | いいえ |

## <a name="microsoftdataprotection"></a>Microsoft.DataProtection

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション | リージョンの移動 |
> | ------------- | ----------- | ---------- | ---------- |
> | backupvaults | [あり](../../backup/backup-vault-overview.md#use-azure-portal-to-move-backup-vault-to-a-different-resource-group) | [はい](../../backup/backup-vault-overview.md#use-azure-portal-to-move-backup-vault-to-a-different-subscription) | いいえ |

## <a name="microsoftdatashare"></a>Microsoft.DataShare

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション | リージョンの移動 |
> | ------------- | ----------- | ---------- | ----------- |
> | accounts | はい | はい | いいえ |

## <a name="microsoftdbformariadb"></a>Microsoft.DBforMariaDB

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション | リージョンの移動 |
> | ------------- | ----------- | ---------- | ----------- |
> | servers | はい | はい | リージョン間の読み取りレプリカを使用して、既存のサーバーを移動できます。 [詳細については、こちらを参照してください](../../postgresql/howto-move-regions-portal.md)。<br/><br/> サービスが geo 冗長バックアップ ストレージを使用してプロビジョニングされている場合、geo リストアを使用して他のリージョンで復元できます。 [詳細については、こちらを参照してください](../../mariadb/concepts-business-continuity.md#recover-from-an-azure-regional-data-center-outage)。

## <a name="microsoftdbformysql"></a>Microsoft.DBforMySQL

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション | リージョンの移動 |
> | ------------- | ----------- | ---------- | ----------- |
> | flexibleServers | いいえ | いいえ | いいえ |
> | servers | はい | はい | リージョン間の読み取りレプリカを使用して、既存のサーバーを移動できます。 [詳細については、こちらを参照してください](../../mysql/howto-move-regions-portal.md)。

## <a name="microsoftdbforpostgresql"></a>Microsoft.DBforPostgreSQL

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション | リージョンの移動 |
> | ------------- | ----------- | ---------- | ----------- |
> | flexibleServers | いいえ | いいえ | いいえ |
> | servergroups | いいえ | いいえ | いいえ |
> | servers | はい | はい | リージョン間の読み取りレプリカを使用して、既存のサーバーを移動できます。 [詳細については、こちらを参照してください](../../postgresql/howto-move-regions-portal.md)。
> | serversv2 | はい | はい | いいえ |
> | singleservers | はい | はい | いいえ |

## <a name="microsoftdeploymentmanager"></a>Microsoft.DeploymentManager

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション | リージョンの移動 |
> | ------------- | ----------- | ---------- | ----------- |
> | artifactsources | はい | はい | いいえ |
> | rollouts | はい | はい | いいえ |
> | servicetopologies | はい | はい | いいえ |
> | servicetopologies/services | はい | はい | いいえ |
> | servicetopologies/services/serviceunits | はい | はい | いいえ |
> | steps | はい | はい | いいえ |

## <a name="microsoftdesktopvirtualization"></a>Microsoft.DesktopVirtualization

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション | リージョンの移動 |
> | ------------- | ----------- | ---------- | ----------- |
> | applicationgroups | はい | はい | いいえ |
> | hostpools | はい | はい | いいえ |
> | workspaces | はい | はい | いいえ |

## <a name="microsoftdevices"></a>Microsoft.Devices

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション | リージョンの移動 |
> | ------------- | ----------- | ---------- | ----------- |
> | elasticpools | いいえ | いいえ | いいえ。 リソースは公開されていません。 |
> | elasticpools/iothubtenants | いいえ | いいえ | いいえ。 リソースは公開されていません。 |
> | iothubs | はい | はい | はい。 [詳細情報](../../iot-hub/iot-hub-how-to-clone.md) |
> | provisioningservices | はい | はい | いいえ |

## <a name="microsoftdevops"></a>Microsoft.DevOps

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション | リージョンの移動 |
> | ------------- | ----------- | ---------- | ----------- |
> | pipelines | はい | はい | いいえ |
> | controllers | **保留中** | **保留中** | いいえ |

## <a name="microsoftdevspaces"></a>Microsoft.DevSpaces

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション | リージョンの移動 |
> | ------------- | ----------- | ---------- | ----------- |
> | controllers | はい | はい | いいえ |
> | AKS クラスター | **保留中** | **保留中** | いいえ<br/><br/> 別リージョンへの移行の[詳細をご覧ください](/previous-versions/azure/dev-spaces/)。

## <a name="microsoftdevtestlab"></a>Microsoft.DevTestLab

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション | リージョンの移動 |
> | ------------- | ----------- | ---------- | ----------- |
> | labcenters | いいえ | いいえ | いいえ |
> | labs | はい | いいえ | いいえ |
> | labs/environments | はい | はい | いいえ |
> | labs/servicerunners | はい | はい | いいえ |
> | labs/virtualmachines | はい | いいえ | いいえ |
> | schedules | はい | はい | いいえ |

## <a name="microsoftdigitaltwins"></a>Microsoft.DigitalTwins

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション | リージョンの移動 |
> | ------------- | ----------- | ---------- | ----------- |
> | digitaltwinsinstances | いいえ | いいえ | はい。新しいリージョン内にリソースを再作成します。 [詳細情報](../../digital-twins/how-to-move-regions.md) |

## <a name="microsoftdocumentdb"></a>Microsoft.DocumentDB

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション | リージョンの移動 |
> | ------------- | ----------- | ---------- | ----------- |
> | databaseaccountnames | いいえ | いいえ | いいえ |
> | databaseaccounts | はい | はい | いいえ |

## <a name="microsoftdomainregistration"></a>Microsoft.DomainRegistration

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション | リージョンの移動 |
> | ------------- | ----------- | ---------- | ----------- |
> | domains | はい | はい | いいえ |
> | generatessorequest | いいえ | いいえ | いいえ |
> | topleveldomains | いいえ | いいえ | いいえ |
> | validatedomainregistrationinformation | いいえ | いいえ | いいえ |

## <a name="microsoftenterpriseknowledgegraph"></a>Microsoft.EnterpriseKnowledgeGraph

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション | リージョンの移動 |
> | ------------- | ----------- | ---------- | ----------- |
> | services | はい | はい | いいえ |

## <a name="microsofteventgrid"></a>Microsoft.EventGrid

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション | リージョンの移動 |
> | ------------- | ----------- | ---------- | ----------- |
> | domains | はい | はい | いいえ |
> | eventsubscriptions | いいえ - 個別に移動できませんが、サブスクライブしたリソースで自動的に移動されます。 | いいえ - 個別に移動できませんが、サブスクライブしたリソースで自動的に移動されます。 | いいえ |
> | extensiontopics | いいえ | いいえ | いいえ |
> | partnernamespaces | はい | はい | いいえ |
> | partnerregistrations | いいえ | いいえ | いいえ |
> | partnertopics | はい | はい | いいえ |
> | systemtopics | はい | はい | いいえ |
> | topics | はい | はい | いいえ |
> | topictypes | いいえ | いいえ | いいえ |

## <a name="microsofteventhub"></a>Microsoft.EventHub

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション | リージョンの移動 |
> | ------------- | ----------- | ---------- | ----------- |
> | clusters | はい | はい | いいえ |
> | namespaces | はい | はい | はい (テンプレートを使用)<br/><br/> [Event Hub 名前空間を別のリージョンに移動する](../../event-hubs/move-across-regions.md) |
> | sku | いいえ | いいえ | いいえ |

## <a name="microsoftexperimentation"></a>Microsoft.Experimentation

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション | リージョンの移動 |
> | ------------- | ----------- | ---------- | ----------- |
> | experimentworkspaces | いいえ | いいえ | いいえ |

## <a name="microsoftextendedlocation"></a>Microsoft.ExtendedLocation

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション | リージョンの移動 |
> | ------------- | ----------- | ---------- | ----------- |
> | customLocations | いいえ | いいえ | いいえ |

## <a name="microsoftfalcon"></a>Microsoft.Falcon

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション | リージョンの移動 |
> | ------------- | ----------- | ---------- | ----------- |
> | namespaces | はい | はい | いいえ |

## <a name="microsoftfeatures"></a>Microsoft.Features

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション | リージョンの移動 |
> | ------------- | ----------- | ---------- | ----------- |
> | featureproviders | いいえ | いいえ | いいえ |
> | features | いいえ | いいえ | いいえ |
> | providers | いいえ | いいえ | いいえ |
> | subscriptionfeatureregistrations | いいえ | いいえ | いいえ |

## <a name="microsoftgenomics"></a>Microsoft.Genomics

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション | リージョンの移動 |
> | ------------- | ----------- | ---------- | ----------- |
> | accounts | いいえ | いいえ | いいえ |

## <a name="microsoftguestconfiguration"></a>Microsoft.GuestConfiguration

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション | リージョンの移動 |
> | ------------- | ----------- | ---------- | ----------- |
> | automanagedaccounts | いいえ | いいえ | いいえ |
> | automanagedvmconfigurationprofiles | いいえ | いいえ | いいえ |
> | guestconfigurationassignments | いいえ | いいえ | いいえ |
> | software | いいえ | いいえ | いいえ |
> | softwareupdateprofile | いいえ | いいえ | いいえ |
> | softwareupdates | いいえ | いいえ | いいえ |

## <a name="microsofthanaonazure"></a>Microsoft.HanaOnAzure

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション | リージョンの移動 |
> | ------------- | ----------- | ---------- | ----------- |
> | hanainstances | いいえ | いいえ | いいえ |
> | sapmonitors | いいえ | いいえ | いいえ |

## <a name="microsofthardwaresecuritymodules"></a>Microsoft.HardwareSecurityModules

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション | リージョンの移動 |
> | ------------- | ----------- | ---------- | ----------- |
> | dedicatedhsms | いいえ | いいえ | いいえ |

## <a name="microsofthdinsight"></a>Microsoft.HDInsight

> [!IMPORTANT]
> HDInsight クラスターは、新しいサブスクリプションまたはリソース グループに移動できます。 ただし、HDInsight クラスターにリンクされているネットワーク リソース (仮想ネットワーク、NIC、ロード バランサーなど) をサブスクリプション間で移動することはできません。 また、クラスターの仮想マシンに接続されている NIC を新しいリソース グループに移動することはできません。
>
> HDInsight クラスターを新しいサブスクリプションに移動するときは、まず、他のリソース (ストレージ アカウントなど) を移動します。 その後、HDInsight クラスターを単独で移動します。

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション | リージョンの移動 |
> | ------------- | ----------- | ---------- | ----------- |
> | clusters | はい | はい | いいえ |

## <a name="microsofthealthcareapis"></a>Microsoft.HealthcareApis

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション | リージョンの移動 |
> | ------------- | ----------- | ---------- | ----------- |
> | services | はい | はい | いいえ |

## <a name="microsofthybridcompute"></a>Microsoft.HybridCompute

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション | リージョンの移動 |
> | ------------- | ----------- | ---------- | ----------- |
> | machines | はい | はい | いいえ |
> | machines / extensions | はい | はい | いいえ |

## <a name="microsofthybriddata"></a>Microsoft.HybridData

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション | リージョンの移動 |
> | ------------- | ----------- | ---------- | ----------- |
> | datamanagers | はい | はい | いいえ |

## <a name="microsofthybridnetwork"></a>Microsoft.HybridNetwork

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション | リージョンの移動 |
> | ------------- | ----------- | ---------- | ----------- |
> | devices | いいえ | いいえ | いいえ |
> | vnfs | いいえ | いいえ | いいえ |

## <a name="microsofthydra"></a>Microsoft.Hydra

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション | リージョンの移動 |
> | ------------- | ----------- | ---------- | ----------- |
> | components | いいえ | いいえ | いいえ |
> | networkscopes | いいえ | いいえ | いいえ |

## <a name="microsoftimportexport"></a>Microsoft.ImportExport

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション | リージョンの移動 |
> | ------------- | ----------- | ---------- | ----------- |
> | jobs | はい | はい | いいえ |

## <a name="microsoftinsights"></a>Microsoft.Insights

> [!IMPORTANT]
> 新しいサブスクリプションへの移動によって[サブスクリプション クォータ](azure-subscription-service-limits.md#azure-monitor-limits)を超えないようにします。

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション | リージョンの移動 |
> | ------------- | ----------- | ---------- | ----------- |
> | accounts | はい | はい | いいえ。 [詳細については、こちらを参照してください](../../azure-monitor/faq.yml#how-do-i-move-an-application-insights-resource-to-a-new-region-)。 |
> | actiongroups | はい | はい | いいえ |
> | activitylogalerts | いいえ | いいえ | いいえ |
> | alertrules | はい | はい | いいえ |
> | autoscalesettings | はい | はい | いいえ |
> | baseline | いいえ | いいえ | いいえ |
> | components | はい | はい | いいえ |
> | datacollectionrules | いいえ | いいえ | いいえ |
> | diagnosticsettings | いいえ | いいえ | いいえ |
> | diagnosticsettingscategories | いいえ | いいえ | いいえ |
> | eventcategories | いいえ | いいえ | いいえ |
> | eventtypes | いいえ | いいえ | いいえ |
> | extendeddiagnosticsettings | いいえ | いいえ | いいえ |
> | guestdiagnosticsettings | いいえ | いいえ | いいえ |
> | listmigrationdate | いいえ | いいえ | いいえ |
> | logdefinitions | いいえ | いいえ | いいえ |
> | logprofiles | いいえ | いいえ | いいえ |
> | logs | いいえ | いいえ | いいえ |
> | metricalerts | いいえ | いいえ | いいえ |
> | metricbaselines | いいえ | いいえ | いいえ |
> | metricbatch | いいえ | いいえ | いいえ |
> | metricdefinitions | いいえ | いいえ | いいえ |
> | metricnamespaces | いいえ | いいえ | いいえ |
> | metrics | いいえ | いいえ | いいえ |
> | migratealertrules | いいえ | いいえ | いいえ |
> | migratetonewpricingmodel | いいえ | いいえ | いいえ |
> | myworkbooks | いいえ | いいえ | いいえ |
> | notificationgroups | いいえ | いいえ | いいえ |
> | privatelinkscopes | いいえ | いいえ | いいえ |
> | rollbacktolegacypricingmodel | いいえ | いいえ | いいえ |
> | scheduledqueryrules | はい | はい | いいえ |
> | トポロジ | いいえ | いいえ | いいえ |
> | トランザクション | いいえ | いいえ | いいえ |
> | vminsightsonboardingstatuses | いいえ | いいえ | いいえ |
> | webtests | はい | はい | いいえ |
> | webtests / gettestresultfile | いいえ | いいえ | いいえ |
> | Workbooks | はい | はい | いいえ |
> | workbooktemplates | はい | はい | いいえ |

## <a name="microsoftiotcentral"></a>Microsoft.IoTCentral

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション | リージョンの移動 |
> | ------------- | ----------- | ---------- | ----------- |
> | apptemplates | いいえ | いいえ | いいえ |
> | iotapps | はい | はい | いいえ |

## <a name="microsoftiothub"></a>Microsoft.IoTHub

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション | リージョンの移動 |
> | ------------- | ----------- | ---------- | ----------- |
> | iothub | はい | はい | はい (ハブを複製) <br/><br/> [IoT ハブを別のリージョンに複製する](../../iot-hub/iot-hub-how-to-clone.md) |

## <a name="microsoftiotspaces"></a>Microsoft.IoTSpaces

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション | リージョンの移動 |
> | ------------- | ----------- | ---------- | ----------- |
> | graph | はい | はい | いいえ |

## <a name="microsoftkeyvault"></a>Microsoft.KeyVault

> [!IMPORTANT]
> ディスクの暗号化に使用されるキー コンテナーは、同じサブスクリプション内のリソース グループに移動したり、サブスクリプション間で移動したりすることはできません。

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション | リージョンの移動 |
> | ------------- | ----------- | ---------- | ----------- |
> | deletedvaults | いいえ | いいえ | いいえ |
> | hsmpools | いいえ | いいえ | いいえ |
> | managedhsms | いいえ | いいえ | いいえ |
> | vaults | はい | はい | いいえ |

## <a name="microsoftkubernetes"></a>Microsoft.Kubernetes

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション | リージョンの移動 |
> | ------------- | ----------- | ---------- | ----------- |
> | connectedclusters | いいえ | いいえ | いいえ |
> | registeredsubscriptions | いいえ | いいえ | いいえ |

## <a name="microsoftkubernetesconfiguration"></a>Microsoft.KubernetesConfiguration

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション | リージョンの移動 |
> | ------------- | ----------- | ---------- | ----------- |
> | sourcecontrolconfigurations | いいえ | いいえ | いいえ |

## <a name="microsoftkusto"></a>Microsoft.Kusto

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション | リージョンの移動 |
> | ------------- | ----------- | ---------- | ----------- |
> | clusters | はい | はい | いいえ |

## <a name="microsoftlabservices"></a>Microsoft.LabServices

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション | リージョンの移動 |
> | ------------- | ----------- | ---------- | ----------- |
> | labaccounts | いいえ | いいえ | いいえ |
> | users | いいえ | いいえ | いいえ |

## <a name="microsoftlocationbasedservices"></a>Microsoft.LocationBasedServices

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション | リージョンの移動 |
> | ------------- | ----------- | ---------- | ----------- |
> | accounts | いいえ | いいえ | いいえ |

## <a name="microsoftlocationservices"></a>Microsoft.LocationServices

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション | リージョンの移動 |
> | ------------- | ----------- | ---------- | ----------- |
> | accounts | いいえ | いいえ | いいえ。グローバル サービスです。 |

## <a name="microsoftlogic"></a>Microsoft.Logic

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション | リージョンの移動 |
> | ------------- | ----------- | ---------- | ----------- |
> | hostingenvironments | いいえ | いいえ | いいえ |
> | integrationaccounts | はい | はい | いいえ |
> | integrationserviceenvironments | はい | いいえ | いいえ |
> | integrationserviceenvironments / managedapis | はい | いいえ | いいえ |
> | isolatedenvironments | いいえ | いいえ | いいえ |
> | workflows | はい | はい | いいえ |

## <a name="microsoftmachinelearning"></a>Microsoft.MachineLearning

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション | リージョンの移動 |
> | ------------- | ----------- | ---------- | ----------- |
> | commitmentplans | いいえ | いいえ | いいえ |
> | webservices | はい | いいえ | いいえ |
> | workspaces | はい | はい | いいえ |

## <a name="microsoftmachinelearningcompute"></a>Microsoft.MachineLearningCompute

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション | リージョンの移動 |
> | ------------- | ----------- | ---------- | ----------- |
> | operationalizationclusters | いいえ | いいえ | いいえ |

## <a name="microsoftmachinelearningexperimentation"></a>Microsoft.MachineLearningExperimentation

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション | リージョンの移動 |
> | ------------- | ----------- | ---------- | ----------- |
> | accounts | いいえ | いいえ | いいえ |
> | teamaccounts | いいえ | いいえ | いいえ |

## <a name="microsoftmachinelearningmodelmanagement"></a>Microsoft.MachineLearningModelManagement

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション | リージョンの移動 |
> | ------------- | ----------- | ---------- | ----------- |
> | accounts | いいえ | いいえ | いいえ |

## <a name="microsoftmachinelearningservices"></a>Microsoft.MachineLearningServices

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション | リージョンの移動 |
> | ------------- | ----------- | ---------- | ----------- |
> | workspaces | いいえ | いいえ | いいえ |

## <a name="microsoftmaintenance"></a>Microsoft.Maintenance

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション | リージョンの移動 |
> | ------------- | ----------- | ---------- | ----------- |
> | configurationassignments | いいえ | いいえ | はい。 [詳細情報](../../virtual-machines/move-region-maintenance-configuration.md) |
> | maintenanceconfigurations | はい | はい | はい。 [詳細情報](../../virtual-machines/move-region-maintenance-configuration-resources.md) |
> | updates | いいえ | いいえ | いいえ |

## <a name="microsoftmanagedidentity"></a>Microsoft.ManagedIdentity

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション | リージョンの移動 |
> | ------------- | ----------- | ---------- | ----------- |
> | ID | いいえ | いいえ | いいえ |
> | userassignedidentities | いいえ | いいえ | いいえ |

## <a name="microsoftmanagednetwork"></a>Microsoft.ManagedNetwork

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション | リージョンの移動 |
> | ------------- | ----------- | ---------- | ----------- |
> | managednetworks | いいえ | いいえ | いいえ |
> | managednetworks / managednetworkgroups | いいえ | いいえ | いいえ |
> | managednetworks / managednetworkpeeringpolicies | いいえ | いいえ | いいえ |
> | 通知 (notification) | いいえ | いいえ | いいえ |

## <a name="microsoftmanagedservices"></a>Microsoft.ManagedServices

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション | リージョンの移動 |
> | ------------- | ----------- | ---------- | ----------- |
> | marketplaceregistrationdefinitions | いいえ | いいえ | いいえ |
> | registrationassignments | いいえ | いいえ | いいえ |
> | registrationdefinitions | いいえ | いいえ | いいえ |

## <a name="microsoftmanagement"></a>Microsoft.Management

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション | リージョンの移動 |
> | ------------- | ----------- | ---------- | ----------- |
> | getentities | いいえ | いいえ | いいえ |
> | managementgroups | いいえ | いいえ | いいえ |
> | managementgroups / settings | いいえ | いいえ | いいえ |
> | resources | いいえ | いいえ | いいえ |
> | starttenantbackfill | いいえ | いいえ | いいえ |
> | tenantbackfillstatus | いいえ | いいえ | いいえ |

## <a name="microsoftmaps"></a>Microsoft.Maps

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション | リージョンの移動 |
> | ------------- | ----------- | ---------- | ----------- |
> | accounts | はい | はい | いいえ。Azure Maps は地理空間サービスです。 |
> | accounts / privateatlases | はい | はい | いいえ |

## <a name="microsoftmarketplace"></a>Microsoft.Marketplace

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション | リージョンの移動 |
> | ------------- | ----------- | ---------- | ----------- |
> | offers | いいえ | いいえ | いいえ |
> | offertypes | いいえ | いいえ | いいえ |
> | privategalleryitems | いいえ | いいえ | いいえ |
> | privatestoreclient | いいえ | いいえ | いいえ |
> | privatestores | いいえ | いいえ | いいえ |
> | products | いいえ | いいえ | いいえ |
> | publishers | いいえ | いいえ | いいえ |
> | registrations | いいえ | いいえ | いいえ |

## <a name="microsoftmarketplaceapps"></a>Microsoft.MarketplaceApps

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション | リージョンの移動 |
> | ------------- | ----------- | ---------- | ----------- |
> | classicdevservices | いいえ | いいえ | いいえ |

## <a name="microsoftmarketplaceordering"></a>Microsoft.MarketplaceOrdering

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション | リージョンの移動 |
> | ------------- | ----------- | ---------- | ----------- |
> | agreements | いいえ | いいえ | いいえ |
> | offertypes | いいえ | いいえ | いいえ |

## <a name="microsoftmedia"></a>Microsoft.Media

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション | リージョンの移動 |
> | ------------- | ----------- | ---------- | ----------- |
> | mediaservices | はい | はい | いいえ |
> | mediaservices/liveevents | はい | はい | いいえ |
> | mediaservices/streamingendpoints | はい | はい | いいえ |

## <a name="microsoftmicroservices4spring"></a>Microsoft.Microservices4Spring

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション | リージョンの移動 |
> | ------------- | ----------- | ---------- | ----------- |
> | appclusters | いいえ | いいえ | いいえ |

## <a name="microsoftmigrate"></a>Microsoft.Migrate

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション | リージョンの移動 |
> | ------------- | ----------- | ---------- | ----------- |
> | assessmentprojects | いいえ | いいえ | いいえ |
> | migrateprojects | いいえ | いいえ | いいえ |
> | movecollections | いいえ | いいえ | いいえ |
> | projects | いいえ | いいえ | いいえ |

## <a name="microsoftmixedreality"></a>Microsoft.MixedReality

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション | リージョンの移動 |
> | ------------- | ----------- | ---------- | ---------- |
> | holographicsbroadcastaccounts | いいえ | いいえ | いいえ |
> | objectunderstandingaccounts | いいえ | いいえ | いいえ |
> | remoterenderingaccounts | はい | はい | いいえ |
> | spatialanchorsaccounts | はい | はい | いいえ |

## <a name="microsoftnetapp"></a>Microsoft.NetApp

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション | リージョンの移動 |
> | ------------- | ----------- | ---------- | ----------- |
> | netappaccounts | いいえ | いいえ | いいえ |
> | netappaccounts/capacitypools | いいえ | いいえ | いいえ |
> | netappaccounts/capacitypools/volumes | いいえ | いいえ | いいえ |
> | netappaccounts/capacitypools/volumes/mounttargets | いいえ | いいえ | いいえ |
> | netappaccounts/capacitypools/volumes/snapshots | いいえ | いいえ | いいえ |

## <a name="microsoftnetwork"></a>Microsoft.Network

> [!IMPORTANT]
> [ネットワークの移動ガイダンス](./move-limitations/networking-move-limitations.md)をご覧ください。

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション | リージョンの移動 |
> | ------------- | ----------- | ---------- | ----------- |
> | applicationgateways | いいえ | いいえ | いいえ |
> | applicationgatewaywebapplicationfirewallpolicies | いいえ | いいえ | いいえ |
> | applicationsecuritygroups | はい | はい | いいえ |
> | azurefirewalls | いいえ | いいえ | いいえ |
> | bastionhosts | いいえ | いいえ | いいえ |
> | bgpservicecommunities | いいえ | いいえ | いいえ |
> | connections | はい | はい | いいえ |
> | ddoscustompolicies | はい | はい | いいえ |
> | ddosprotectionplans | いいえ | いいえ | いいえ |
> | dnszones | はい | はい | いいえ |
> | expressroutecircuits | いいえ | いいえ | いいえ |
> | expressroutegateways | いいえ | いいえ | いいえ |
> | expressrouteserviceproviders | いいえ | いいえ | いいえ |
> | firewallpolicies | いいえ | いいえ | いいえ |
> | frontdoors | いいえ | いいえ | いいえ |
> | ipallocations | はい | はい | いいえ |
> | ipgroups | はい | はい | いいえ |
> | loadbalancers | はい - Basic SKU<br> はい - Standard SKU | はい - Basic SKU<br>いいえ - Standard SKU | はい <br/><br/> 内部および外部のロード バランサーを移動するには [Azure Resource Mover](../../resource-mover/tutorial-move-region-virtual-machines.md) を使用します。 |
> | localnetworkgateways | はい | はい | いいえ |
> | natgateways | いいえ | いいえ | いいえ |
> | networkexperimentprofiles | いいえ | いいえ | いいえ |
> | networkintentpolicies | はい | はい | いいえ |
> | networkinterfaces | はい | はい | はい <br/><br/> NIC を移動するには [Azure Resource Mover](../../resource-mover/tutorial-move-region-virtual-machines.md) を使用します。 |
> | networkprofiles | いいえ | いいえ | いいえ |
> | networksecuritygroups | はい | はい | はい <br/><br/> ネットワーク セキュリティ グループ (NSG) を移動するには [Azure Resource Mover](../../resource-mover/tutorial-move-region-virtual-machines.md) を使用します。 |
> | networkwatchers | いいえ | いいえ | いいえ |
> | networkwatchers/connectionmonitors | はい | いいえ | いいえ |
> | networkwatchers/flowlogs | はい | いいえ | いいえ |
> | networkwatchers/pingmeshes | はい | いいえ | いいえ |
> | p2svpngateways | いいえ | いいえ | いいえ |
> | privatednszones | はい | はい | いいえ |
> | privatednszones/virtualnetworklinks | はい | はい | いいえ |
> | privatednszonesinternal | いいえ | いいえ | いいえ |
> | privateendpointredirectmaps | いいえ | いいえ | いいえ |
> | privateendpoints | いいえ | いいえ | いいえ |
> | privatelinkservices | いいえ | いいえ | いいえ |
> | publicipaddresses | はい - Basic SKU<br>はい - Standard SKU | はい - Basic SKU<br>いいえ - Standard SKU | はい<br/><br/> [Azure Resource Mover](../../resource-mover/tutorial-move-region-virtual-machines.md) を使用してパブリック IP アドレス構成を移動します (IP アドレスは保持されません)。 |
> | publicipprefixes | はい | はい | いいえ |
> | routefilters | いいえ | いいえ | いいえ |
> | routetables | はい | はい | いいえ |
> | securitypartnerproviders | はい | はい | いいえ |
> | serviceendpointpolicies | はい | はい | いいえ |
> | trafficmanagergeographichierarchies | いいえ | いいえ | いいえ |
> | trafficmanagerprofiles | はい | はい | いいえ |
> | trafficmanagerprofiles / heatmaps | いいえ | いいえ | いいえ |
> | trafficmanagerusermetricskeys | いいえ | いいえ | いいえ |
> | virtualhubs | いいえ | いいえ | いいえ |
> | virtualnetworkgateways | はい | はい | いいえ |
> | virtualnetworks | はい | はい | いいえ |
> | virtualnetworktaps | いいえ | いいえ | いいえ |
> | virtualrouters | はい | はい | いいえ |
> | virtualwans | いいえ | いいえ |
> | vpngateways (仮想 WAN) | いいえ | いいえ | いいえ |
> | vpnserverconfigurations | いいえ | いいえ | いいえ |
> | vpnsites (仮想 WAN) | いいえ | いいえ | いいえ |

## <a name="microsoftnotificationhubs"></a>Microsoft.NotificationHubs

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション | リージョンの移動 |
> | ------------- | ----------- | ---------- | ----------- |
> | namespaces | はい | はい | いいえ |
> | namespaces/notificationhubs | はい | はい | いいえ |

## <a name="microsoftobjectstore"></a>Microsoft.ObjectStore

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション | リージョンの移動 |
> | ------------- | ----------- | ---------- | ----------- |
> | osnamespaces | はい | はい | いいえ |

## <a name="microsoftoffazure"></a>Microsoft.OffAzure

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション | リージョンの移動 |
> | ------------- | ----------- | ---------- | ----------- |
> | hypervsites | いいえ | いいえ | いいえ |
> | importsites | いいえ | いいえ | いいえ |
> | serversites | いいえ | いいえ | いいえ |
> | vmwaresites | いいえ | いいえ | いいえ |

## <a name="microsoftoperationalinsights"></a>Microsoft.OperationalInsights

> [!IMPORTANT]
> 新しいサブスクリプションへの移行によって、[サブスクリプション クォータ](azure-subscription-service-limits.md#azure-monitor-limits)が超えることがないようにします。
>
> Automation アカウントがリンクされているワークスペースは移動できません。 移動操作を開始する前に、リンクされている Automation アカウントは必ず解除してください。

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション | リージョンの移動 |
> | ------------- | ----------- | ---------- | ----------- |
> | clusters | いいえ | いいえ | いいえ |
> | deletedworkspaces | いいえ | いいえ | いいえ |
> | linktargets | いいえ | いいえ | いいえ |
> | storageinsightconfigs | いいえ | いいえ | いいえ |
> | workspaces | はい | はい | いいえ |

## <a name="microsoftoperationsmanagement"></a>Microsoft.OperationsManagement

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション | リージョンの移動 |
> | ------------- | ----------- | ---------- | ----------- |
> | managementassociations | いいえ | いいえ | いいえ |
> | managementconfigurations | はい | はい | いいえ |
> | solutions | はい | はい | いいえ |
> | views | はい | はい | いいえ |

## <a name="microsoftpeering"></a>Microsoft.Peering

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション | リージョンの移動 |
> | ------------- | ----------- | ---------- | ----------- |
> | legacypeerings | いいえ | いいえ | いいえ |
> | peerasns | いいえ | いいえ | いいえ |
> | peeringlocations | いいえ | いいえ | いいえ |
> | peerings | いいえ | いいえ | いいえ |
> | peeringservicecountries | いいえ | いいえ | いいえ |
> | peeringservicelocations | いいえ | いいえ | いいえ |
> | peeringserviceproviders | いいえ | いいえ | いいえ |
> | peeringservices | いいえ | いいえ | いいえ |

## <a name="microsoftpolicyinsights"></a>Microsoft.PolicyInsights

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション | リージョンの移動 |
> | ------------- | ----------- | ---------- | ----------- |
> | policyevents | いいえ | いいえ | いいえ |
> | policystates | いいえ | いいえ | いいえ |
> | policytrackedresources | いいえ | いいえ | いいえ |
> | remediations | いいえ | いいえ | いいえ |

## <a name="microsoftportal"></a>Microsoft.Portal

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション | リージョンの移動 |
> | ------------- | ----------- | ---------- | ----------- |
> | consoles | いいえ | いいえ | いいえ |
> | dashboards | はい | はい | いいえ |
> | usersettings | いいえ | いいえ | いいえ |

## <a name="microsoftpowerbi"></a>Microsoft.PowerBI

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション | リージョンの移動 |
> | ------------- | ----------- | ---------- | ----------- |
> | workspacecollections | はい | はい | いいえ |

## <a name="microsoftpowerbidedicated"></a>Microsoft.PowerBIDedicated

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション | リージョンの移動 |
> | ------------- | ----------- | ---------- | ----------- |
> | capacities | はい | はい | いいえ |

## <a name="microsoftprojectbabylon"></a>Microsoft.ProjectBabylon

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション | リージョンの移動 |
> | ------------- | ----------- | ---------- | ---------- |
> | accounts | いいえ | いいえ | いいえ |

## <a name="microsoftpurview"></a>Microsoft.Purview

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション | リージョンの移動 |
> | ------------- | ----------- | ---------- | ---------- |
> | accounts | はい | はい | いいえ |

## <a name="microsoftproviderhub"></a>Microsoft.ProviderHub

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション | リージョンの移動 |
> | ------------- | ----------- | ---------- | ----------- |
> | availableaccounts | いいえ | いいえ | いいえ |
> | providerregistrations | いいえ | いいえ | いいえ |
> | rollouts | いいえ | いいえ | いいえ |

## <a name="microsoftquantum"></a>Microsoft.Quantum

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション | リージョンの移動 |
> | ------------- | ----------- | ---------- | ----------- |
> | workspaces | いいえ | いいえ | いいえ |

## <a name="microsoftrecoveryservices"></a>Microsoft.RecoveryServices

>[!IMPORTANT]
>- [Recovery Services の移動のガイダンス](../../backup/backup-azure-move-recovery-services-vault.md?toc=/azure/azure-resource-manager/toc.json)に関する記事をご覧ください。
>- [リージョン間でリソースを移動した後に Recovery Services コンテナー内でバックアップを続行する](../../backup/azure-backup-move-vaults-across-regions.md?toc=/azure/azure-resource-manager/toc.json)に関連する記事をご覧ください。

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション | リージョンの移動 |
> | ------------- | ----------- | ---------- | ----------- |
> | replicationeligibilityresults | いいえ | いいえ | いいえ |
> | vaults | はい | はい | いいえ。<br/><br/> Azure リージョン間で Azure Backup 用の Recovery Services コンテナーを移動することはできません。<br/><br/> Azure Site Recovery 用の Recovery Services コンテナーでは、[そのコンテナーを無効にして、ターゲット リージョンで再作成する](../../site-recovery/move-vaults-across-regions.md)ことができます。 |

## <a name="microsoftredhatopenshift"></a>Microsoft.RedHatOpenShift

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション | リージョンの移動 |
> | ------------- | ----------- | ---------- | ----------- |
> | openshiftclusters | いいえ | いいえ | いいえ |

## <a name="microsoftrelay"></a>Microsoft.Relay

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション | リージョンの移動 |
> | ------------- | ----------- | ---------- | ----------- |
> | namespaces | はい | はい | いいえ |

## <a name="microsoftresourcegraph"></a>Microsoft.ResourceGraph

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション | リージョンの移動 |
> | ------------- | ----------- | ---------- | ----------- |
> | Query | はい | はい | いいえ |
> | resourcechangedetails | いいえ | いいえ | いいえ |
> | resourcechanges | いいえ | いいえ | いいえ |
> | resources | いいえ | いいえ | いいえ |
> | resourceshistory | いいえ | いいえ | いいえ |
> | subscriptionsstatus | いいえ | いいえ | いいえ |

## <a name="microsoftresourcehealth"></a>Microsoft.ResourceHealth

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション | リージョンの移動 |
> | ------------- | ----------- | ---------- | ----------- |
> | childresources | いいえ | いいえ | いいえ |
> | emergingissues | いいえ | いいえ | いいえ |
> | events | いいえ | いいえ | いいえ |
> | metadata | いいえ | いいえ | いいえ |
> | notifications | いいえ | いいえ | いいえ |

## <a name="microsoftresources"></a>Microsoft.Resources

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション | リージョンの移動 |
> | ------------- | ----------- | ---------- | ----------- |
> | deployments | いいえ | いいえ | いいえ |
> | deploymentscripts | いいえ | いいえ | はい<br/><br/>[Microsoft.Resources リソースを新しいリージョンに移動する](microsoft-resources-move-regions.md) |
> | deploymentscripts / logs | いいえ | いいえ | いいえ |
> | リンク | いいえ | いいえ | いいえ |
> | providers | いいえ | いいえ | いいえ |
> | resourcegroups | いいえ | いいえ | いいえ |
> | resources | いいえ | いいえ | いいえ |
> | subscriptions | いいえ | いいえ | いいえ |
> | tags | いいえ | いいえ | いいえ |
> | templatespecs | いいえ | いいえ | はい<br/><br/>[Microsoft.Resources リソースを新しいリージョンに移動する](microsoft-resources-move-regions.md) |
> | templatespecs / versions | いいえ | いいえ | いいえ |
> | tenants | いいえ | いいえ | いいえ |

## <a name="microsoftsaas"></a>Microsoft.SaaS

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション | リージョンの移動 |
> | ------------- | ----------- | ---------- | ----------- |
> | applications | はい | いいえ | いいえ |
> | saasresources | いいえ | いいえ | いいえ |

## <a name="microsoftsearch"></a>Microsoft.Search

> [!IMPORTANT]
> 1 回の操作で異なるリージョンにあるいくつかの Search リソースを一度に移動することはできません。 代わりに、別の操作で移動します。

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション | リージョンの移動 |
> | ------------- | ----------- | ---------- | ----------- |
> | resourcehealthmetadata | いいえ | いいえ | いいえ |
> | searchservices | はい | はい | いいえ |

## <a name="microsoftsecurity"></a>Microsoft.Security

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション | リージョンの移動 |
> | ------------- | ----------- | ---------- | ----------- |
> | adaptivenetworkhardenings | いいえ | いいえ | いいえ |
> | advancedthreatprotectionsettings | いいえ | いいえ | いいえ |
> | alerts | いいえ | いいえ | いいえ |
> | allowedconnections | いいえ | いいえ | いいえ |
> | applicationwhitelistings | いいえ | いいえ | いいえ |
> | assessmentmetadata | いいえ | いいえ | いいえ |
> | assessments | いいえ | いいえ | いいえ |
> | autodismissalertsrules | いいえ | いいえ | いいえ |
> | automations | はい | はい | いいえ |
> | autoprovisioningsettings | いいえ | いいえ | いいえ |
> | complianceresults | いいえ | いいえ | いいえ |
> | compliances | いいえ | いいえ | いいえ |
> | datacollectionagents | いいえ | いいえ | いいえ |
> | devicesecuritygroups | いいえ | いいえ | いいえ |
> | discoveredsecuritysolutions | いいえ | いいえ | いいえ |
> | externalsecuritysolutions | いいえ | いいえ | いいえ |
> | informationprotectionpolicies | いいえ | いいえ | いいえ |
> | iotsecuritysolutions | はい | はい | いいえ |
> | iotsecuritysolutions / analyticsmodels | いいえ | いいえ | いいえ |
> | iotsecuritysolutions / analyticsmodels / aggregatedalerts | いいえ | いいえ | いいえ |
> | iotsecuritysolutions / analyticsmodels / aggregatedrecommendations | いいえ | いいえ | いいえ |
> | jitnetworkaccesspolicies | いいえ | いいえ | いいえ |
> | policies | いいえ | いいえ | いいえ |
> | pricings | いいえ | いいえ | いいえ |
> | regulatorycompliancestandards | いいえ | いいえ | いいえ |
> | regulatorycompliancestandards / regulatorycompliancecontrols | いいえ | いいえ | いいえ |
> | regulatorycompliancestandards / regulatorycompliancecontrols / regulatorycomplianceassessments | いいえ | いいえ | いいえ |
> | securitycontacts | いいえ | いいえ | いいえ |
> | securitysolutions | いいえ | いいえ | いいえ |
> | securitysolutionsreferencedata | いいえ | いいえ | いいえ |
> | securitystatuses | いいえ | いいえ | いいえ |
> | securitystatusessummaries | いいえ | いいえ | いいえ |
> | servervulnerabilityassessments | いいえ | いいえ | いいえ |
> | settings | いいえ | いいえ | いいえ |
> | subassessments | いいえ | いいえ | いいえ |
> | tasks | いいえ | いいえ | いいえ |
> | topologies | いいえ | いいえ | いいえ |
> | workspacesettings | いいえ | いいえ | いいえ |

## <a name="microsoftsecurityinsights"></a>Microsoft.SecurityInsights

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション | リージョンの移動 |
> | ------------- | ----------- | ---------- | ----------- |
> | aggregations | いいえ | いいえ | いいえ |
> | alertrules | いいえ | いいえ | いいえ |
> | alertruletemplates | いいえ | いいえ | いいえ |
> | automationrules | いいえ | いいえ | いいえ |
> | bookmarks | いいえ | いいえ | いいえ |
> | cases | いいえ | いいえ | いいえ |
> | dataconnectors | いいえ | いいえ | いいえ |
> | entities | いいえ | いいえ | いいえ |
> | entityqueries | いいえ | いいえ | いいえ |
> | incidents | いいえ | いいえ | いいえ |
> | officeconsents | いいえ | いいえ | いいえ |
> | settings | いいえ | いいえ | いいえ |
> | threatintelligence | いいえ | いいえ | いいえ |

## <a name="microsoftserialconsole"></a>Microsoft.SerialConsole

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション | リージョンの移動 |
> | ------------- | ----------- | ---------- | ----------- |
> | consoleservices | いいえ | いいえ | いいえ |

## <a name="microsoftservermanagement"></a>Microsoft.ServerManagement

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション | リージョンの移動 |
> | ------------- | ----------- | ---------- | ----------- |
> | gateways | いいえ | いいえ | いいえ |
> | nodes | いいえ | いいえ | いいえ |

## <a name="microsoftservicebus"></a>Microsoft.ServiceBus

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション | リージョンの移動 |
> | ------------- | ----------- | ---------- | ----------- |
> | namespaces | はい | はい | いいえ |
> | premiummessagingregions | いいえ | いいえ | いいえ |
> | sku | いいえ | いいえ | いいえ |

## <a name="microsoftservicefabric"></a>Microsoft.ServiceFabric

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション | リージョンの移動 |
> | ------------- | ----------- | ---------- | ----------- |
> | applications | いいえ | いいえ | いいえ |
> | clusters | はい | はい | いいえ |
> | containergroups | いいえ | いいえ | いいえ |
> | containergroupsets | いいえ | いいえ | いいえ |
> | edgeclusters | いいえ | いいえ | いいえ |
> | managedclusters | いいえ | いいえ | いいえ |
> | networks | いいえ | いいえ | いいえ |
> | secretstores | いいえ | いいえ | いいえ |
> | volumes | いいえ | いいえ | いいえ |

## <a name="microsoftservicefabricmesh"></a>Microsoft.ServiceFabricMesh

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション | リージョンの移動 |
> | ------------- | ----------- | ---------- | ----------- |
> | applications | はい | はい | いいえ |
> | containergroups | いいえ | いいえ | いいえ |
> | gateways | はい | はい | いいえ |
> | networks | はい | はい | いいえ |
> | secrets | はい | はい | いいえ |
> | volumes | はい | はい | いいえ |

## <a name="microsoftservices"></a>Microsoft.Services

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション | リージョンの移動 |
> | ------------- | ----------- | ---------- | ----------- |
> | rollouts | いいえ | いいえ | いいえ |

## <a name="microsoftsignalrservice"></a>Microsoft.SignalRService

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション | リージョンの移動 |
> | ------------- | ----------- | ---------- | ----------- |
> | signalr | はい | はい | いいえ |

## <a name="microsoftsoftwareplan"></a>Microsoft.SoftwarePlan

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション | リージョンの移動 |
> | ------------- | ----------- | ---------- | ----------- |
> | hybridusebenefits | いいえ | いいえ | いいえ |

## <a name="microsoftsolutions"></a>Microsoft.Solutions

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション | リージョンの移動 |
> | ------------- | ----------- | ---------- | ----------- |
> | applicationdefinitions | いいえ | いいえ | いいえ |
> | applications | いいえ | いいえ | いいえ |
> | jitrequests | いいえ | いいえ | いいえ |

## <a name="microsoftsql"></a>Microsoft.Sql

> [!IMPORTANT]
> データベースとサーバーは同じリソース グループ内に存在する必要があります。 SQL Server を移動すると、そのデータベースもすべて移動されます。 この動作は、Azure SQL Database と Azure Synapse Analytics データベースに適用されます。

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション | リージョンの移動 |
> | ------------- | ----------- | ---------- | ----------- |
> | instancepools | いいえ | いいえ | いいえ |
> | locations | はい | はい | いいえ |
> | managedinstances | いいえ | いいえ | はい <br/><br/> マネージド インスタンスのリージョン間での移動に関する[詳細を参照してください](../../azure-sql/database/move-resources-across-regions.md)。 |
> | managedinstances/databases | いいえ | いいえ | はい |
> | servers | はい | はい |はい |
> | servers/databases | はい | はい | はい <br/><br/> データベースのリージョン間での移動に関する[詳細を参照してください](../../azure-sql/database/move-resources-across-regions.md)。<br/><br/> Azure Resource Mover を使用した Azure SQL データベースの移動に関する[詳細を参照してください](../../resource-mover/tutorial-move-region-sql.md)。  |
> | servers / databases / backuplongtermretentionpolicies | はい | はい | いいえ |
> | servers/elasticpools | はい | はい | はい <br/><br/> エラスティック プールのリージョン間での移動に関する[詳細を参照してください](../../azure-sql/database/move-resources-across-regions.md)。<br/><br/> Azure Resource Mover を使用した Azure SQL エラスティック プールを移動に関する[詳細を参照してください](../../resource-mover/tutorial-move-region-sql.md)。  |
> | servers/jobaccounts | はい | はい | いいえ |
> | servers/jobagents | はい | はい | いいえ |
> | virtualclusters | はい | はい | はい |

## <a name="microsoftsqlvirtualmachine"></a>Microsoft.SqlVirtualMachine

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション | リージョンの移動 |
> | ------------- | ----------- | ---------- | ----------- |
> | sqlvirtualmachinegroups | はい | はい | いいえ |
> | sqlvirtualmachines | はい | はい | いいえ |

## <a name="microsoftstorage"></a>Microsoft.Storage

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション | リージョンの移動 |
> | ------------- | ----------- | ---------- | ----------- |
> | storageaccounts | はい | はい | はい<br/><br/> [Azure ストレージ アカウントを別のリージョンに移動する](../../storage/common/storage-account-move.md) |

## <a name="microsoftstoragecache"></a>Microsoft.StorageCache

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション | リージョンの移動 |
> | ------------- | ----------- | ---------- | ----------- |
> | caches | いいえ | いいえ | いいえ |

## <a name="microsoftstoragesync"></a>Microsoft.StorageSync

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション | リージョンの移動 |
> | ------------- | ----------- | ---------- | ----------- |
> | storagesyncservices | はい | はい | いいえ |

## <a name="microsoftstoragesyncdev"></a>Microsoft.StorageSyncDev

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション | リージョンの移動 |
> | ------------- | ----------- | ---------- | ----------- |
> | storagesyncservices | いいえ | いいえ | いいえ |

## <a name="microsoftstoragesyncint"></a>Microsoft.StorageSyncInt

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション | リージョンの移動 |
> | ------------- | ----------- | ---------- | ----------- |
> | storagesyncservices | いいえ | いいえ | いいえ |

## <a name="microsoftstorsimple"></a>Microsoft.StorSimple

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション | リージョンの移動 |
> | ------------- | ----------- | ---------- | ----------- |
> | managers | いいえ | いいえ | いいえ |

## <a name="microsoftstreamanalytics"></a>Microsoft.StreamAnalytics

> [!IMPORTANT]
> 実行中状態の Stream Analytics ジョブは移動できません。

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション | リージョンの移動 |
> | ------------- | ----------- | ---------- | ----------- |
> | clusters | いいえ | いいえ | いいえ |
> | streamingjobs | はい | はい | いいえ |

## <a name="microsoftstreamanalyticsexplorer"></a>Microsoft.StreamAnalyticsExplorer

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション | リージョンの移動 |
> | ------------- | ----------- | ---------- | ----------- |
> | 環境 | いいえ | いいえ | いいえ |
> | instances | いいえ | いいえ | いいえ |

## <a name="microsoftsubscription"></a>Microsoft.Subscription

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション | リージョンの移動 |
> | ------------- | ----------- | ---------- | ----------- |
> | subscriptions | いいえ | いいえ | いいえ |

## <a name="microsoftsupport"></a>Microsoft.Support

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション | リージョンの移動 |
> | ------------- | ----------- | ---------- | ----------- |
> | services | いいえ | いいえ | いいえ |
> | supporttickets | いいえ | いいえ | いいえ |

## <a name="microsoftsynapse"></a>Microsoft.Synapse

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション | リージョンの移動 |
> | ------------- | ----------- | ---------- | ----------- |
> | workspaces | いいえ | いいえ | いいえ |
> | workspaces / bigdatapools | いいえ | いいえ | いいえ |
> | workspaces / sqlpools | いいえ | いいえ | いいえ |

## <a name="microsofttimeseriesinsights"></a>Microsoft.TimeSeriesInsights

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション | リージョンの移動 |
> | ------------- | ----------- | ---------- | ----------- |
> | 環境 | はい | はい | いいえ |
> | environments/eventsources | はい | はい | いいえ |
> | environments/referencedatasets | はい | はい | いいえ |

## <a name="microsofttoken"></a>Microsoft.Token

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション | リージョンの移動 |
> | ------------- | ----------- | ---------- | ----------- |
> | stores | はい | はい | いいえ |

## <a name="microsoftvirtualmachineimages"></a>Microsoft.VirtualMachineImages

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション | リージョンの移動 |
> | ------------- | ----------- | ---------- | ----------- |
> | imagetemplates | いいえ | いいえ | いいえ |

## <a name="microsoftvisualstudio"></a>Microsoft.VisualStudio

> [!IMPORTANT]
> Azure DevOps のサブスクリプションを変更するには、[change the Azure subscription used for billing (課金に使用される Azure サブスクリプションの変更)](/azure/devops/organizations/billing/change-azure-subscription?toc=/azure/azure-resource-manager/toc.json) に関する記事をご覧ください。

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション | リージョンの移動 |
> | ------------- | ----------- | ---------- | ----------- |
> | account | いいえ | いいえ | いいえ |
> | account / extension | いいえ | いいえ | いいえ |
> | account/project | いいえ | いいえ | いいえ |

## <a name="microsoftvmware"></a>Microsoft.VMware

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション | リージョンの移動 |
> | ------------- | ----------- | ---------- | ----------- |
> | arczones | いいえ | いいえ | いいえ |
> | resourcepools | いいえ | いいえ | いいえ |
> | vcenters | いいえ | いいえ | いいえ |
> | virtualmachines | いいえ | いいえ | いいえ |
> | virtualmachinetemplates | いいえ | いいえ | いいえ |
> | virtualnetworks | いいえ | いいえ | いいえ |

## <a name="microsoftvmwarecloudsimple"></a>Microsoft.VMwareCloudSimple

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション | リージョンの移動 |
> | ------------- | ----------- | ---------- | ----------- |
> | dedicatedcloudnodes | いいえ | いいえ | いいえ |
> | dedicatedcloudservices | いいえ | いいえ | いいえ |
> | virtualmachines | いいえ | いいえ | いいえ |

## <a name="microsoftvnfmanager"></a>Microsoft.VnfManager

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション | リージョンの移動 |
> | ------------- | ----------- | ---------- | ----------- |
> | devices | いいえ | いいえ | いいえ |
> | vnfs | いいえ | いいえ | いいえ |

## <a name="microsoftvsonline"></a>Microsoft.VSOnline

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション | リージョンの移動 |
> | ------------- | ----------- | ---------- | ----------- |
> | accounts | いいえ | いいえ | いいえ |
> | plans | いいえ | いいえ | いいえ |
> | registeredsubscriptions | いいえ | いいえ | いいえ |

## <a name="microsoftweb"></a>Microsoft.Web

> [!IMPORTANT]
> [App Service move guidance (App Service の移動のガイダンス)](./move-limitations/app-service-move-limitations.md) に関する記事をご覧ください。

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション | リージョンの移動 |
> | ------------- | ----------- | ---------- | ----------- |
> | availablestacks | いいえ | いいえ | いいえ |
> | billingmeters | いいえ | いいえ | いいえ |
> | certificates | いいえ | はい | いいえ |
> | certificates (マネージド) | いいえ | いいえ | いいえ |
> | connectiongateways | はい | はい | いいえ |
> | connections | はい | はい | いいえ |
> | customapis | はい | はい | いいえ |
> | deletedsites | いいえ | いいえ | いいえ |
> | deploymentlocations | いいえ | いいえ | いいえ |
> | georegions | いいえ | いいえ | いいえ |
> | hostingenvironments | いいえ | いいえ | いいえ |
> | kubeenvironments | はい | はい | いいえ |
> | publishingusers | いいえ | いいえ | いいえ |
> | recommendations | いいえ | いいえ | いいえ |
> | resourcehealthmetadata | いいえ | いいえ | いいえ |
> | runtimes | いいえ | いいえ | いいえ |
> | serverfarms | はい | はい | いいえ |
> | serverfarms / eventgridfilters | いいえ | いいえ | いいえ |
> | sites | はい | はい | いいえ |
> | sites/premieraddons | はい | はい | いいえ |
> | sites/slots | はい | はい | いいえ |
> | sourcecontrols | いいえ | いいえ | いいえ |
> | staticsites | いいえ | いいえ | いいえ |

## <a name="microsoftwindowsesu"></a>Microsoft.WindowsESU

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション | リージョンの移動 |
> | ------------- | ----------- | ---------- | ----------- |
> | multipleactivationkeys | いいえ | いいえ | いいえ |

## <a name="microsoftwindowsiot"></a>Microsoft.WindowsIoT

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション | リージョンの移動 |
> | ------------- | ----------- | ---------- | ----------- |
> | deviceservices | いいえ | いいえ | いいえ |

## <a name="microsoftworkloadbuilder"></a>Microsoft.WorkloadBuilder

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション | リージョンの移動 |
> | ------------- | ----------- | ---------- | ----------- |
> | workloads | いいえ | いいえ | いいえ |

## <a name="microsoftworkloadmonitor"></a>Microsoft.WorkloadMonitor

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション | リージョンの移動 |
> | ------------- | ----------- | ---------- | ----------- |
> | components | いいえ | いいえ | いいえ |
> | componentssummary | いいえ | いいえ | いいえ |
> | monitorinstances | いいえ | いいえ | いいえ |
> | monitorinstancessummary | いいえ | いいえ | いいえ |
> | monitors | いいえ | いいえ | いいえ |

## <a name="third-party-services"></a>サード パーティーのサービス

サード パーティのサービスは現在、移動操作をサポートしていません。

## <a name="next-steps"></a>次のステップ

- リソースの移動のコマンドについては、「[新しいリソース グループまたはサブスクリプションへのリソースの移動](move-resource-group-and-subscription.md)」を参照してください。
- Resource Mover サービスの[詳細を学習](../../resource-mover/overview.md)します。
- 同じデータをコンマ区切り値のファイルとして入手するには、リソース グループおよびサブスクリプションでの移動のサポートに関する [move-support-resources.csv](https://github.com/tfitzmac/resource-capabilities/blob/master/move-support-resources.csv) をダウンロードしてください。 これらのプロパティとリージョンでの移動のサポートが必要な場合は、[move-support-resources-with-regions.csv](https://github.com/tfitzmac/resource-capabilities/blob/master/move-support-resources-with-regions.csv) をダウンロードしてください。
