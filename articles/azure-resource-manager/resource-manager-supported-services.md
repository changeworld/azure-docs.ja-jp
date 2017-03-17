---
title: "Resource Manager でサポートされるサービス | Microsoft Docs"
description: "リソース マネージャーをサポートするリソース プロバイダー、そのスキーマと利用可能な API バージョン、およびリソースをホストできるリージョンについて説明します。"
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn
ms.assetid: 3c7a6fe4-371a-40da-9ebe-b574f583305b
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/06/2017
ms.author: tomfitz
translationtype: Human Translation
ms.sourcegitcommit: 094729399070a64abc1aa05a9f585a0782142cbf
ms.openlocfilehash: c645a8aa317b12d52f0246d0f9205294d56b6a0d
ms.lasthandoff: 03/07/2017


---
# <a name="resource-manager-providers-regions-api-versions-and-schemas"></a>リソース マネージャーのプロバイダー、リージョン、API のバージョン、およびスキーマ
Azure リソース マネージャーは、アプリケーションを構成するサービスをデプロイして管理するための新しい方法を提供します。 すべてではありませんがほとんどのサービスはリソース マネージャーをサポートし、一部のサービスはリソース マネージャーを部分的にのみサポートします。 このトピックでは、Azure リソース マネージャーに対してサポートされるリソース プロバイダーの一覧を示します。

リソースをデプロイするときは、リソースをサポートしているリージョンとリソースで利用できる API バージョンも知っておく必要があります。 「[サポートされているリージョン](#supported-regions)」セクションでは、サブスクリプションおよびリソースで有効なリージョンを確認する方法が示されています。 「 [サポートされる API バージョン](#supported-api-versions) 」セクションでは、利用できる API バージョンを決定する方法について紹介します。

Azure ポータルとクラシック ポータルでサポートされているサービスについては、 [Azure ポータルで利用できるサービスの表](https://azure.microsoft.com/features/azure-portal/availability/)を参照してください。 リソースの移動をサポートするサーバーを確認する方法については、「 [新しいリソース グループまたはサブスクリプションへのリソースの移動](resource-group-move-resources.md)」を参照してください。

次の表では、各 Microsoft サービスで Resource Manager によるデプロイと管理がサポートされるかどうかを示します。 " **クイック スタート テンプレート** " 列のリンクは、指定されたリソース プロバイダーに対するクエリを Azure クイック スタート テンプレート レポジトリに送信します。 クイック スタート テンプレートは、頻繁に追加、更新されます。 特定のサービスに対するリンクがあっても、必ずしもクエリによってリポジトリからテンプレートが返されるとは限りません。 Resource Manager をサポートするサード パーティのリソース プロバイダーも多数存在します。 すべてのリソース プロバイダーを確認する方法については、「[リソース プロバイダーと種類](#resource-providers-and-types)」セクションを参照してください。

## <a name="compute"></a>計算
| サービス | リソース マネージャーが有効 | REST API | スキーマ | クイック スタート テンプレート |
| --- | --- | --- | --- | --- |
| Batch |あり |[Batch REST](/rest/api/batchservice) |[Batch スキーマ](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2015-12-01/Microsoft.Batch.json) | |
| Container Registry |はい |[Container Registry の REST](/rest/api/containerregistry) |[Container Registry スキーマ](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2016-06-27-preview/Microsoft.ContainerRegistry.json) |[Microsoft.ContainerRegistry](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.ContainerRegistry%22&type=Code) |
| Container Service |はい |[コンテナー サービスの REST](/rest/api/compute/containerservices) |[コンテナー スキーマ](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2016-03-30/Microsoft.ContainerService.json) |[Microsoft.ContainerService](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.ContainerService%22&type=Code) |
| Dynamics Lifecycle Services |あり | | | |
| スケール セット |あり |[スケール セットの REST](/rest/api/compute/virtualmachinescalesets) |[スケール セット スキーマ](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2015-08-01/Microsoft.Compute.json) |[virtualMachineScaleSets](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=virtualMachineScaleSets&type=Code) |
| Service Fabric |あり |[Service Fabric Rest](/rest/api/servicefabric) | [Service Fabric スキーマ](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2016-09-01/Microsoft.ServiceFabric.json) |[Microsoft.ServiceFabric](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.ServiceFabric%22&type=Code) |
| Virtual Machines |あり |[VM REST](/rest/api/compute/virtualmachines) |[VM スキーマ](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2015-08-01/Microsoft.Compute.json) |[virtualMachines](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.Compute%2Fvirtualmachines%22&type=Code) |
| Virtual Machines (クラシック) |制限あり |- |- |- |
| リモート アプリ |いいえ |- |- |- |
| Cloud Services (クラシック) |制限あり (下記参照) |- |- |- |

「Virtual Machines (クラシック)」とは、リソース マネージャー デプロイ モデルではなくクラシック デプロイ モデルを使用してデプロイされたリソースのことです。 一般に、このようなリソースはリソース マネージャーの操作をサポートしていませんが、有効になっている操作がいくつかあります。 これらのデプロイメント モデルの詳細については、「 [リソース マネージャー デプロイと従来のデプロイを理解する](resource-manager-deployment-model.md)」を参照してください。 

Cloud Services (クラシック) は、他のクラシック リソースと共に使用できます。 ただし、クラシック リソースは、Resource Manager のすべての機能を利用するわけではないため、今後のソリューションに適したオプションではありません。 代わりに、アプリケーション インフラストラクチャを変更して、Microsoft.Compute、Microsoft.Storage、Microsoft.Network の各名前空間のリソースを使用することを検討してください。

## <a name="networking"></a>ネットワーク
| サービス | リソース マネージャーが有効 | REST API | スキーマ | クイック スタート テンプレート |
| --- | --- | --- | --- | --- |
| Application Gateway |あり |[Application Gateway REST](https://msdn.microsoft.com/library/azure/mt684939.aspx) | |[applicationGateways](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.Network%2FapplicationGateways%22&type=Code) |
| DNS |あり |[DNS REST](/rest/api/dns) |[DNS スキーマ](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2016-04-01/Microsoft.Network.json) |[dnsZones](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.Network%2FdnsZones%22&type=Code) |
| ExpressRoute |あり |[ExpressRoute REST](https://msdn.microsoft.com/library/azure/mt586720.aspx) | |[expressRouteCircuits](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.Network%2FexpressRouteCircuits%22&type=Code) |
| Load Balancer |あり |[Load Balancer REST](https://msdn.microsoft.com/library/azure/mt163651.aspx) |[Load Balancer スキーマ](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2015-08-01/Microsoft.Network.json) |[loadBalancers](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.Network%2Floadbalancers%22&type=Code) |
| Traffic Manager |あり |[Traffic Manager REST](https://msdn.microsoft.com/library/azure/mt163667.aspx) |[Traffic Manager スキーマ](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2015-11-01/Microsoft.Network.json) |[trafficmanagerprofiles](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.Network%2Ftrafficmanagerprofiles%22&type=Code) |
| Virtual Networks |あり |[Virtual Network REST](https://msdn.microsoft.com/en-us/library/azure/mt163650.aspx) |[Virtual Network スキーマ](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2015-08-01/Microsoft.Network.json) |[virtualNetworks](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.Network%2FvirtualNetworks%22&type=Code) |
| VPN Gateway |あり |[ネットワーク ゲートウェイ REST](https://msdn.microsoft.com/library/azure/mt163859.aspx) | |[virtualNetworkGateways](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.Network%2FvirtualNetworkGateways%22&type=Code) <br /> [localNetworkGateways](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.Network%2FlocalNetworkGateways%22&type=Code) <br />[connections](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.Network%2Fconnections%22&type=Code) |

## <a name="storage"></a>Storage
| サービス | リソース マネージャーが有効 | REST API | スキーマ | クイック スタート テンプレート |
| --- | --- | --- | --- | --- | --- |
| Storage |あり |[Storage REST](/rest/api/storagerp) |[ストレージ アカウント](resource-manager-template-storage.md) |[Microsoft.Storage](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.Storage%22&type=Code) |
| StorSimple |はい | | | |

## <a name="databases"></a>データベース
| サービス | リソース マネージャーが有効 | REST API | スキーマ | クイック スタート テンプレート |
| --- | --- | --- | --- | --- | --- |
| DocumentDB |あり |[DocumentDB REST](/rest/api/documentdbresourceprovider) |[DocumentDB スキーマ](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2015-04-08/Microsoft.DocumentDB.json) |[Microsoft.DocumentDB](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.DocumentDb%22&type=Code) |
| Redis Cache |はい | [Redis Cache REST](/rest/api/redis) |[Redis スキーマ](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2016-04-01/Microsoft.Cache.json) |[Microsoft.Cache](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.Cache%22&type=Code) |
| SQL Database |あり |[SQL Database REST](/rest/api/sql) |[SQL Database スキーマ](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2014-04-01-preview/Microsoft.Sql.json) |[Microsoft.Sql](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.Sql%22&type=Code) |
| SQL Data Warehouse |あり | | | |

## <a name="web--mobile"></a>Web とモバイル
| サービス | リソース マネージャーが有効 | REST API | スキーマ | クイック スタート テンプレート |
| --- | --- | --- | --- | --- |
| API Apps |はい | [App Service REST](/rest/api/appservice) |[API Apps スキーマ](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2015-08-01/Microsoft.Web.json) |[API Apps](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22kind%22%3A+%22apiApp%22&type=Code) |
| API Management |あり |[API Management REST](/rest/api/apimanagement) |[API Management スキーマ](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2016-07-07/Microsoft.ApiManagement.json) |[Microsoft.ApiManagement](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.ApiManagement%22&type=Code) |
| Content Moderator |はい | | | |
| Function App |はい | [Function App REST](/rest/api/appservice) | |[functionApp](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22functionApp%22&type=Code) |
| Logic Apps |はい |[Logic Apps REST](/rest/api/logic) |[Logic Apps スキーマ](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2016-06-01/Microsoft.Logic.json) |[Microsoft.Logic](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.Logic%22&type=Code) |
| Mobile Apps |はい | [App Service REST](/rest/api/appservice) |[Mobile Apps スキーマ](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2015-08-01/Microsoft.Web.json) |[mobileApp](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22mobileApp%22&type=Code) |
| Mobile Engagement |はい |[Mobile Engagement REST](https://msdn.microsoft.com/library/azure/mt683754.aspx) | |[Microsoft.MobileEngagements](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.MobileEngagement%22&type=Code) |
| Search |あり |[Search REST](/rest/api/searchservice) | [Search スキーマ](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2015-08-19/Microsoft.Search.json) |[Microsoft.Search](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.Search%22&type=Code) |
| Web Apps |はい | [Web Apps REST](/rest/api/appservice/webapps) |[Web Apps スキーマ](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2015-08-01/Microsoft.Web.json) |[Microsoft.Web](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.Web%22&type=Code) |

## <a name="intelligence--analytics"></a>インテリジェンス + 分析
| サービス | リソース マネージャーが有効 | REST API | スキーマ | クイック スタート テンプレート |
| --- | --- | --- | --- | --- |
| Analysis Services | はい | [Analysis Services REST](/rest/api/analysisservices) | [Analysis Services スキーマ](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2016-05-16/Microsoft.AnalysisServices.json) | |
| Cognitive Services |はい | [Cognitive Services REST](/rest/api/cognitiveservices) |[Cognitive Services スキーマ](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2016-02-01-preview/Microsoft.CognitiveServices.json) | |
| Data Catalog |あり |[Data Catalog REST](/rest/api/datacatalog) |[Data Catalog スキーマ](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2016-03-30/Microsoft.DataCatalog.json) | |
| Data Factory |あり |[Data Factory REST](/rest/api/datafactory) | |[Microsoft.DataFactory](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.DataFactory%22&type=Code) |
| Data Lake Analytics |あり | [Data Lake REST](/rest/api/datalakeanalytics) |[Data Lake Analytics のスキーマ](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2015-10-01-preview/Microsoft.DataLakeAnalytics.json) |[Microsoft.DataLakeAnalytics](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.DataLakeAnalytics%22&type=Code) |
| Data Lake Store |あり |[Data Lake Store REST](/rest/api/datalakestore) |[Data Lake Store のスキーマ](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2015-10-01-preview/Microsoft.DataLakeStore.json) |[Microsoft.DataLakeStore](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.DataLakeStore%22&type=Code) |
| HDInsights |あり |[HDInsights REST](/rest/api/hdinsight) | |[Microsoft.HDInsight](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.HDInsight%22&type=Code) |
| Machine Learning |あり |[Machine Learning REST](/rest/api/machinelearning) |[Machine Learning スキーマ](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2016-05-01-preview/Microsoft.MachineLearning.json) | |
| Stream Analytics |あり |[Steam Analytics REST](/rest/api/streamanalytics) | | |
| Power BI |あり |[Power BI Embedded REST](/rest/api/powerbiembedded) |[Power BI スキーマ](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2016-01-29/Microsoft.PowerBI.json) | |


## <a name="internet-of-things"></a>モノのインターネット
| サービス | リソース マネージャーが有効 | REST API | スキーマ | クイック スタート テンプレート |
| --- | --- | --- | --- | --- |
| イベント ハブ |あり |[Event Hubs REST](/rest/api/eventhub) |[Event Hub スキーマ](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2015-08-01/Microsoft.EventHub.json) |[Microsoft.EventHub](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.EventHub%22&type=Code) |
| IoTHubs |あり |[IoT Hub REST](/rest/api/iothub) |[IoT Hub スキーマ](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2016-02-03/Microsoft.Devices.json) |[Microsoft.Devices](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.Devices%22&type=Code) |
| Notification Hubs |あり |[Notification Hubs REST](/rest/api/notificationhubs) |[Notification Hub スキーマ](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2015-04-01/Microsoft.NotificationHubs.json) |[Microsoft.NotificationHubs](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.NotificationHubs%22&type=Code) |

## <a name="media--cdn"></a>メディアと CDN
| サービス | リソース マネージャーが有効 | REST API | スキーマ | クイック スタート テンプレート |
| --- | --- | --- | --- | --- |
| CDN |あり |[CDN REST](/rest/api/cdn) |[CDN スキーマ](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2016-04-02/Microsoft.Cdn.json) |[Microsoft.Cdn](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.Cdn%22&type=Code) |
| メディア サービス |あり |[Media Services REST](/rest/api/media) |[Media スキーマ](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2015-10-01/Microsoft.Media.json) |[Microsoft.Media](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.Media%22&type=Code)  |

## <a name="hybrid-integration"></a>ハイブリッド統合
| サービス | リソース マネージャーが有効 | REST API | スキーマ | クイック スタート テンプレート |
| --- | --- | --- | --- | --- |
| BizTalk Services |はい | |[BizTalk スキーマ](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2014-04-01/Microsoft.BizTalkServices.json) | |
| Recovery Service |はい |[Recovery Services REST](/rest/api/recoveryservices) |[Recovery Services スキーマ](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2016-06-01/Microsoft.RecoveryServices.json) |[Microsoft.RecoveryServices](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.RecoveryServices%22&type=Code) |
| Service Bus |はい |[Service Bus REST](/rest/api/servicebus) |[Service Bus スキーマ](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2015-08-01/Microsoft.ServiceBus.json) |[Microsoft.ServiceBus](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.ServiceBus%22&type=Code) |

## <a name="identity--access-management"></a>ID 管理とアクセス管理
Azure Active Directory はリソース マネージャーと連携して、サブスクリプションのロールベースのアクセス制御を可能にします。 ロールベースのアクセス制御と Active Directory の使用方法については、「 [Azure Active Directory リソースへのアクセスをロールの割り当てによって管理する](../active-directory/role-based-access-control-configure.md)」を参照してください。

## <a name="developer-services"></a>開発者サービス
| サービス | リソース マネージャーが有効 | REST API | スキーマ | クイック スタート テンプレート |
| --- | --- | --- | --- | --- |
| 監視 |はい |[Monitor REST](/rest/api/monitor) |[Insights スキーマ](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2014-04-01/Microsoft.Insights.json) |[Microsoft.insights](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.insights%22&type=Code) |
| Bing Maps |あり | | | |
| DevTest Labs |はい | [DevTest REST](/rest/api/dtl) |[DevTest Lab スキーマ](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2016-05-15/Microsoft.DevTestLab.json) |[Microsoft.DevTestLab](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.DevTestLab%22&type=Code) |
| Visual Studio アカウント |はい | |[Visual Studio スキーマ](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2014-02-26/microsoft.visualstudio.json) | |

## <a name="management-and-security"></a>管理とセキュリティ
| サービス | リソース マネージャーが有効 | REST API | スキーマ | クイック スタート テンプレート |
| --- | --- | --- | --- | --- |
| Automation |あり |[Automation REST](https://msdn.microsoft.com/library/azure/mt662285.aspx) |[Automation スキーマ](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2015-10-31/Microsoft.Automation.json) |[Microsoft.Automation](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.Automation%22&type=Code) |
| Key Vault |あり |[Key Vault](/rest/api/keyvault) |[Key vault](resource-manager-template-keyvault.md)<br />[Key Vault シークレット](resource-manager-template-keyvault-secret.md) |[Microsoft.KeyVault](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.KeyVault%22&type=Code) |
| Operational Insights |あり | | | |
| Scheduler |あり |[Scheduler REST](/rest/api/scheduler) |[Scheduler スキーマ](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2016-03-01/Microsoft.Scheduler.json) |[Microsoft.Scheduler](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.Scheduler%22&type=Code) |
| セキュリティ (プレビュー) |あり |[Security REST](https://msdn.microsoft.com/library/azure/mt704034.aspx) | |[Microsoft.Security](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.Security%22&type=Code) |

## <a name="resource-manager"></a>リソース マネージャー
| 機能 | リソース マネージャーが有効 | REST API | スキーマ | クイック スタート テンプレート |
| --- | --- | --- | --- | --- | --- |
| 承認 |はい |[Authorization REST](/rest/api/authorization) |[リソース ロック](resource-manager-template-lock.md)<br />[ロールの割り当て](resource-manager-template-role.md) |[Microsoft.Authorization](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.Authorization%22&type=Code) |
| リソース |はい |[Resources REST](/rest/api/resources) |[リソース リンク](resource-manager-template-links.md) |[Microsoft.Resources](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.Resources%22&type=Code) |

## <a name="resource-providers-and-types"></a>リソース プロバイダーと種類
リソースをデプロイするときに、リソース プロバイダーと種類に関する情報を取得しなければならないケースは少なくありません。 この情報は、REST API、Azure PowerShell、Azure CLI のいずれかを使って取得できます。

リソース プロバイダーを使用するには、そのリソース プロバイダーをアカウントに登録する必要があります。 既定では、多くのリソース プロバイダーが自動的に登録されます。ただし、一部のリソース プロバイダーは手動で登録することが必要な場合があります。 次の例は、リソース プロバイダーの登録状態を取得し、必要に応じてリソース プロバイダーを登録する方法を示しています。

### <a name="portal"></a>ポータル
サブスクリプション ブレードで **[リソース プロバイダー]** を選択すると、サポートされているリソース プロバイダーの一覧を簡単に確認できます。 サブスクリプションをリソース プロバイダーに登録するには、**[登録]** リンクを選択します。
   
![リソース プロバイダーの一覧](./media/resource-manager-supported-services/view-resource-providers.png)

### <a name="rest-api"></a>REST API
使用可能なすべてのリソース プロバイダーとその種類、場所、API バージョン、登録状態を取得するには、 [すべてのリソース プロバイダーの一覧表示](https://docs.microsoft.com/rest/api/resources/providers#Providers_List)操作を使用します。 リソース プロバイダーを登録する必要がある場合は、「 [リソース プロバイダーへのサブスクリプションの登録](https://docs.microsoft.com/rest/api/resources/providers#Providers_Register)」をご覧ください。

### <a name="powershell"></a>PowerShell
使用可能なすべてのリソース プロバイダーを取得する方法を次の例に示します。

```powershell
Get-AzureRmResourceProvider -ListAvailable
```


次の例では、特定のリソース プロバイダーのリソースの種類を取得しています。

```powershell
(Get-AzureRmResourceProvider -ProviderNamespace Microsoft.Web).ResourceTypes
```

リソース プロバイダーを登録するには、名前空間を指定します。

```powershell
Register-AzureRmResourceProvider -ProviderNamespace Microsoft.ApiManagement
```

### <a name="azure-cli"></a>Azure CLI
使用可能なすべてのリソース プロバイダーを取得する方法を次の例に示します。

```azurecli
az provider list
```

特定のリソース プロバイダーの情報は、次のコマンドで表示できます。

```azurecli
az provider show --namespace Microsoft.Web
```

リソース プロバイダーを登録するには、名前空間を指定します。

```azurecli
az provider register --namespace Microsoft.ServiceBus
```

## <a name="supported-regions"></a>サポートされているリージョン
通常、リソースをデプロイするときはリソースのリージョンを指定する必要があります。 リソース マネージャーはすべてのリージョンでサポートされていますが、デプロイするリソースはすべてのリージョンではサポートされていない場合があります。 さらに、サブスクリプションでの制限により、リソースをサポートする一部のリージョンを使用できない場合があります。 これらの制限事項は、本国での税金に関する問題のため、またはサブスクリプション管理者によって設けられた、使用を特定のリージョンに制限するポリシーの結果である場合があります。 

すべての Azure サービスでサポートされている全リージョンの一覧については、[リージョン別のサービス](https://azure.microsoft.com/regions/#services)に関するページを参照してください。 ただし、この一覧には、ご利用のサブスクリプションでサポートされていないリージョンも含まれている可能性があります。 ご利用のサブスクリプションでサポートされている、特定のリソースの種類に対応したリージョンは、Portal、REST API、PowerShell、または Azure CLI を使用して確認できます。

### <a name="portal"></a>ポータル
次の手順で、サポートされているリージョンをリソースの種類別に確認できます。

1. **[その他のサービス]** > **[リソース エクスプローラー]** を選択します。
   
    ![リソース エクスプローラー](./media/resource-manager-supported-services/select-resource-explorer.png)
2. **[プロバイダー]** ノードを開きます。
   
    ![プロバイダーの選択](./media/resource-manager-supported-services/select-providers.png)
3. リソース プロバイダーを選択し、サポートされているリージョンと API バージョンを確認します。
   
    ![プロバイダーの表示](./media/resource-manager-supported-services/view-provider.png)

### <a name="rest-api"></a>REST API
ご利用のサブスクリプションの特定のリソースの種類で使用可能なリージョンを確認するには、 [すべてのリソース プロバイダーの一覧表示](https://docs.microsoft.com/rest/api/resources/providers#Providers_List) 操作を利用します。 

### <a name="powershell"></a>PowerShell
次の例では、Web サイトでサポートされるリージョンを取得する方法を示します。

```powershell
((Get-AzureRmResourceProvider -ProviderNamespace Microsoft.Web).ResourceTypes | Where-Object ResourceTypeName -eq sites).Locations
```

### <a name="azure-cli"></a>Azure CLI
次の例では、Web サイトでサポートされる場所を取得する方法を示します。

```azurecli
az provider show --namespace Microsoft.Web --query "resourceTypes[?resourceType=='sites'].locations"
```


## <a name="supported-api-versions"></a>サポートされる API バージョン
テンプレートをデプロイするとき、各リソースの作成に使用する API バージョンを指定する必要があります。 API バージョンは、リリース プロバイダーがリリースする REST API のバージョンに一致します。 リソース プロバイダーは、新しい機能を有効にすると、REST API の新しいバージョンをリリースします。 そのため、テンプレートで指定した API のバージョンにより、テンプレートで指定できるプロパティが変わります。 通常、テンプレートを作成するときは、最新の API バージョンを選択します。 既存のテンプレートの場合、以前の API バージョンの使用を続けるか、テンプレートを最新版に更新して新しい機能を最大限に活用するか決定できます。

### <a name="portal"></a>ポータル
サポートされている API バージョンは、サポートされているリージョンと同様の方法 (前の説明のとおり) で確認できます。

### <a name="rest-api"></a>REST API
リソースの種類で使用可能な API バージョンを確認するには、 [すべてのリソース プロバイダーの一覧表示](https://docs.microsoft.com/rest/api/resources/providers#Providers_List) 操作を利用します。 

### <a name="powershell"></a>PowerShell
次の例では、特定のリソースの種類で利用できる API バージョンを取得する方法を示します。

```powershell
    ((Get-AzureRmResourceProvider -ProviderNamespace Microsoft.Web).ResourceTypes | Where-Object ResourceTypeName -eq sites).ApiVersions
```

次のように出力されます。

```powershell
2015-08-01
2015-07-01
2015-06-01
2015-05-01
2015-04-01
2015-02-01
2014-11-01
2014-06-01
2014-04-01-preview
2014-04-01
```

### <a name="azure-cli"></a>Azure CLI
次のコマンドを使用して、リソース プロバイダーで使用可能な API バージョンを取得します。

```azurecli
az provider show --namespace Microsoft.Web --query "resourceTypes[?resourceType=='sites'].apiVersions"
```

## <a name="next-steps"></a>次のステップ
* リソース マネージャーのテンプレートの作成の詳細については、 [Azure リソース マネージャーのテンプレートの作成](resource-group-authoring-templates.md)に関するページを参照してください。
* リソースをデプロイする方法を確認するには、「 [Azure リソース マネージャーのテンプレートを使用したアプリケーションのデプロイ](resource-group-template-deploy.md)」を参照してください。


