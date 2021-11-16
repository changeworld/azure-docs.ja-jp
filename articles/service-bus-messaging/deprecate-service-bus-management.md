---
title: Azure のメッセージング サービス - Service Manager から Resource Manager へ
description: この記事では、非推奨になった Azure Service Manager の REST API と PowerShell コマンドレットから Resource Manager の REST API と PowerShell コマンドレットへの対応について説明します。
ms.topic: article
ms.date: 08/31/2021
ms.openlocfilehash: fc64aa41ab0ae2b0ecec735c9b9d19575dd9950e
ms.sourcegitcommit: 838413a8fc8cd53581973472b7832d87c58e3d5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/10/2021
ms.locfileid: "132136212"
---
# <a name="deprecation-of-azure-service-manager-support-for-azure-service-bus-relay-and-event-hubs"></a>Azure Service Bus、Relay、Event Hubs に対する Azure Service Manager のサポートの非推奨化

次世代のクラウド インフラストラクチャ スタックである Resource Manager により、"従来の" Azure サービス管理モデル (クラシック デプロイ モデル) は完全に置き換えられます。 その結果、クラシック デプロイ モデルの REST API と Service Bus、Relay、Event Hubs は、2021 年 12 月 1 日に廃止される予定です。 この非推奨化は、[Microsoft Tech Community の発表](https://techcommunity.microsoft.com/t5/Service-Bus-blog/Deprecating-Service-Management-support-for-Azure-Service-Bus/ba-p/370909)で最初に発表されました。 簡単に見分けることができるように、これらの API の URI には `management.core.windows.net` が含まれます。 非推奨になった API と、代わりに使用する必要がある Azure Resource Manager バージョンの API の一覧については、次の表を参照してください。

Service Bus、Relay、Event Hubs を引き続き使用するには、2021 年 11 月 30 日までに Resource Manager に移行してください。 まだ古い API を使用しているお客様には、すぐに切り替えて Resource Manager にしかないメリットを利用することをお勧めします。リソースのグループ化、タグ、合理化されたデプロイと管理のプロセス、Azure のロールベースのアクセス制御 (Azure RBAC) を使用するきめ細かなアクセス制御などがあります。

Azure Resource Manager と Azure Service Manager の比較について詳しくは、[TechNet のブログ](https://blogs.technet.microsoft.com/meamcs/2016/12/22/difference-between-azure-service-manager-and-azure-resource-manager/)をご覧ください。

Azure Service Bus、Relay、Event Hubs に関する Service Manager API と Resource Manager API について詳しくは、REST API のドキュメントをご覧ください。

- [Azure Service Bus](/rest/api/servicebus/)
- [Azure Event Hubs](/rest/api/eventhub/)
- [Azure Relay](/rest/api/relay/)

## <a name="service-manager-rest-api---resource-manager-rest-api"></a>Service Manager REST API と Resource Manager REST API

| Service Manager API (非推奨) | Resource Manager - Service Bus API | Resource Manager - Event Hubs API | Resource Manager - Relay API |
| --------------- | ----------------- | ----------------- | ----------------- | 
| **Namespaces-GetNamespaceAsync** <br/>[サービス バスの名前空間を取得する](/rest/api/servicebus/get-namespace)<br/>[イベント ハブの名前空間を取得する](/rest/api/eventhub/get-event-hub)<br/>[リレーの名前空間を取得する](/rest/api/servicebus/get-relays)<br/> ```GET https://management.core.windows.net/{subscription ID}/services/ServiceBus/Namespaces/{namespace name}``` | [get](/rest/api/servicebus/stable/namespaces/get) | [get](/rest/api/eventhub/stable/namespaces/get) | [get](/rest/api/relay/namespaces/get) |
| **ConnectionDetails-GetConnectionDetails**<br/>サービス バス/イベント ハブ/リレー GetConnectionDetals<br/>```GET https://management.core.windows.net/{subscription ID}/services/ServiceBus/Namespaces/{namespace name}/ConnectionDetails``` | [listkeys](/rest/api/servicebus/stable/namespaces-authorization-rules/list-keys) | [listkeys](/rest/api/eventhub/stable/authorization-rules-event-hubs/list-keys) | [listkeys](/rest/api/relay/namespaces/listkeys) |
| **Topics-GetTopicsAsync**<br/>Service Bus<br/>```GET https://management.core.windows.net/{subscription ID}/services/ServiceBus/Namespaces/{namespace name}/topics? $skip={skip}&$top={top}``` | [list](/rest/api/servicebus/stable/topics/listbynamespace) | &nbsp; | &nbsp; | 
| **Queues-GetQueueAsync** <br/>Service Bus<br/>```GET https://management.core.windows.net/{subscription ID}/services/ServiceBus/Namespaces/{namespace name}/queues/{queueName}``` | [get](/rest/api/servicebus/stable/queues/get) | &nbsp; | &nbsp; | 
| **Relays-GetRelaysAsync**<br/>[リレーを取得する](/rest/api/servicebus/get-relays)<br/>```GET https://management.core.windows.net/{subscription ID}/services/ServiceBus/Namespaces/{namespace name}/relays? $skip={skip}&$top={top}```| &nbsp; | &nbsp; | [list](/rest/api/relay/wcfrelays/listbynamespace) | 
| **NamespaceAuthorizationRules-GetNamespaceAuthorizationRuleAsync**<br/>サービス バス/イベント ハブ/リレー GetNamespaceAuthRule<br/>```GET https://management.core.windows.net/{subscription ID}/services/ServiceBus/Namespaces/{namespace name}/authorizationrules?``` | [getauthorizationrule](/rest/api/servicebus/stable/namespaces-authorization-rules/get-authorization-rule) | [getauthorizationrule](/rest/api/eventhub/stable/authorization-rules-namespaces/get-authorization-rule) | [getauthorizationrule](/rest/api/relay/namespaces/getauthorizationrule) |
| **Namespaces-DeleteNamespaceAsync**<br/>[サービス バスの名前空間を削除する](/rest/api/servicebus/delete-namespace)<br/>[イベント ハブの名前空間を削除する](/rest/api/eventhub/delete-event-hub)<br/>[リレーの名前空間を削除する](/rest/api/servicebus/delete-namespace)<br/> ```DELETE  https://management.core.windows.net/{subscription ID}/services/ServiceBus/Namespaces/{namespace name}``` | [delete](/rest/api/servicebus/stable/namespaces/delete) | [delete](/rest/api/eventhub/stable/namespaces/delete) | [delete](/rest/api/relay/namespaces/delete) | 
| **MessagingSKUPlan-GetPlanAsync**<br/>サービス バス/イベント ハブ/リレーの名前空間を取得する<br/>```GET https://management.core.windows.net/{subscription ID}/services/ServiceBus/Namespaces/{namespace name}/MessagingPlan``` | [get](/rest/api/servicebus/stable/namespaces/get) | [get](/rest/api/eventhub/stable/namespaces/get) | [get](/rest/api/relay/namespaces/get) |
| **MessagingSKUPlan-UpdatePlanAsync**<br/>サービス バス/イベント ハブ/リレーの名前空間を取得する<br/>```PUT https://management.core.windows.net/{subscription ID}/services/ServiceBus/Namespaces/{namespace name}/MessagingPlan``` | [createorupdate](/rest/api/servicebus/stable/namespaces/createorupdate) | [createorupdate](/rest/api/eventhub/stable/namespaces/createorupdate) | [createorupdate](/rest/api/relay/namespaces/createorupdate) |
| **NamespaceAuthorizationRules-UpdateNamespaceAuthorizationRuleAsync**<br/>サービス バス/イベント ハブ/リレーの名前空間を取得する<br/>```PUT https://management.core.windows.net/{subscription ID}/services/ServiceBus/Namespaces/{namespace name}/AuthorizationRules/{rule name}``` | [createorupdate](/rest/api/servicebus/stable/namespaces/createorupdate) | [createorupdateauthorizationrule](/rest/api/eventhub/stable/authorization-rules-event-hubs/create-or-update-authorization-rule) | [createorupdateauthorizationrule](/rest/api/relay/namespaces/createorupdateauthorizationrule) | 
| **NamespaceAuthorizationRules-CreateNamespaceAuthorizationRuleAsync**<br/> 
サービス バス/イベント ハブ/リレー<br/>```PUT https://management.core.windows.net/{subscription ID}/services/ServiceBus/Namespaces/{namespace name}/AuthorizationRules/{rule name}``` |[createorupdate](/rest/api/servicebus/stable/namespaces/createorupdate) | [createorupdateauthorizationrule](/rest/api/eventhub/stable/authorization-rules-event-hubs/create-or-update-authorization-rule) | [createorupdateauthorizationrule](/rest/api/relay/namespaces/createorupdateauthorizationrule) |
| **NamespaceProperties-GetNamespacePropertiesAsync**<br/>[サービス バスの名前空間を取得する](/rest/api/servicebus/get-namespace)<br/>[イベント ハブの名前空間を取得する](/rest/api/eventhub/get-event-hub)<br/>[リレーの名前空間を取得する](/rest/api/servicebus/get-relays)<br/>```GET https://management.core.windows.net/{subscription ID}/services/ServiceBus/Namespaces/{namespace name}``` | [get](/rest/api/servicebus/stable/namespaces/get) | [get](/rest/api/eventhub/stable/namespaces/get) | [get](/rest/api/relay/namespaces/get) |
| **RegionCodes-GetRegionCodesAsync**<br/>サービス バス/イベント ハブ/リレーの名前空間を取得する<br/>```GET https://management.core.windows.net/{subscription ID}/services/ServiceBus/Namespaces/{namespace name}``` | [listbysku](/rest/api/servicebus/stable/regions/listbysku) | [listbysku](/rest/api/eventhub/stable/regions/listbysku) | &nbsp; | 
| **NamespaceProperties-UpdateNamespacePropertyAsync**<br/>サービス バス/イベント ハブ/リレー<br/>```GET    https://management.core.windows.net/{subscription ID}/services/ServiceBus/Regions/``` | [createorupdate](/rest/api/servicebus/stable/namespaces/createorupdate) | [createorupdate](/rest/api/eventhub/stable/namespaces/createorupdate) | [createorupdate](/rest/api/relay/namespaces/createorupdate) |
| **EventHubsCrud-ListEventHubsAsync**<br/>[イベント ハブの一覧を取得する](/rest/api/eventhub/list-event-hubs)<br/>```GET https://management.core.windows.net/{subscription ID}/services/ServiceBus/Namespaces/{namespace name}/eventhubs?$skip={skip}&$top={top}``` | &nbsp; | [list](/rest/api/servicebus/stable/eventhubs/listbynamespace) | &nbsp; | 
| **EventHubsCrud-GetEventHubAsync**<br/>[イベント ハブを取得する](/rest/api/eventhub/get-event-hub)<br/>```GET https://management.core.windows.net/{subscription ID}/services/ServiceBus/Namespaces/{namespace name}/eventhubs/{eventHubPath}``` | &nbsp; | [get](/rest/api/eventhub/get-event-hub) | &nbsp; | 
| **NamespaceAuthorizationRules-DeleteNamespaceAuthorizationRuleAsync**<br/>サービス バス/イベント ハブ/リレー<br/>```DELETE https://management.core.windows.net/{subscription ID}/services/ServiceBus/Namespaces/{namespace name}/AuthorizationRules/{rule name}``` | [deleteauthorizationrule](/rest/api/servicebus/stable/namespaces-authorization-rules/delete-authorization-rule) | [deleteauthorizationrule](/rest/api/eventhub/stable/authorization-rules-namespaces/delete-authorization-rule) | [deleteauthorizationrule](/rest/api/relay/namespaces/deleteauthorizationrule) |
| **NamespaceAuthorizationRules-GetNamespaceAuthorizationRulesAsync**<br/>サービス バス/イベント ハブ/リレー<br/>```GET https://management.core.windows.net/{subscription ID}/services/ServiceBus/Namespaces/{namespace name}/AuthorizationRules``` | [listauthorizationrules](/rest/api/servicebus/stable/namespaces-authorization-rules/list-authorization-rules) | [listauthorizationrules](/rest/api/eventhub/stable/authorization-rules-namespaces/list-authorization-rules) | [listauthorizationrules](/rest/api/relay/namespaces/listauthorizationrules) |
| **NamespaceAvailability-IsNamespaceAvailable**<br/>[サービス バス名前空間の可用性](/rest/api/servicebus/check-namespace-availability)<br/>```GET https://management.core.windows.net/{subscription ID}/services/ServiceBus/CheckNamespaceAvailability/?namespace=<namespaceValue>``` | [checknameavailability](/rest/api/servicebus/stable/namespaces-check-name-availability/check-name-availability) | [checknameavailability](/rest/api/eventhub/stable/check-name-availability-namespaces/check-name-availability) | [checknameavailability](/rest/api/relay/namespaces/checknameavailability) |
| **Namespaces-CreateOrUpdateNamespaceAsync**<br/>サービス バス/イベント ハブ/リレー<br/>```PUT https://management.core.windows.net/{subscription ID}/services/ServiceBus/Namespaces/{namespace name}``` | [createorupdate](/rest/api/servicebus/stable/namespaces/createorupdate) | [createorupdate](/rest/api/eventhub/stable/namespaces/createorupdate) | [createorupdate](/rest/api/relay/namespaces/createorupdate) | 
| **Topics-GetTopicAsync**<br/>```GET https://management.core.windows.net/{subscription ID}/services/ServiceBus/Namespaces/{namespace name}/topics/{topicPath}``` | [get](/rest/api/servicebus/stable/topics/get) | &nbsp; | &nbsp; |

## <a name="service-manager-powershell---resource-manager-powershell"></a>Service Manager PowerShell と Resource Manager PowerShell
| Service Manager PowerShell コマンド (非推奨) | Resource Manager の新しいコマンド | Resource Manager のさらに新しいコマンド |
| ----- | ----- | ----- | 
| [Get-AzureSBAuthorizationRule](/powershell/module/servicemanagement/azure.service/get-azuresbauthorizationrule) | [Get-AzureRmServiceBusAuthorizationRule](/powershell/module/azurerm.servicebus/get-azurermservicebusauthorizationrule) | [Get-AzServiceBusAuthorizationRule](/powershell/module/az.servicebus/get-azservicebusauthorizationrule) |
| [Get-AzureSBLocation](/powershell/module/servicemanagement/azure.service/get-azuresblocation) | [Get-AzureRmServiceBusGeoDRConfiguration](/powershell/module/azurerm.servicebus/get-azurermservicebusgeodrconfiguration) | [Get-AzServiceBusGeoDRConfiguration](/powershell/module/az.servicebus/get-azservicebusgeodrconfiguration) |
| [Get-AzureSBNamespace](/powershell/module/servicemanagement/azure.service/get-azuresbnamespace) | [Get-AzureRmServiceBusNamespace](/powershell/module/azurerm.servicebus/get-azurermservicebusnamespace) | [Get-AzServiceBusNamespace](/powershell/module/az.servicebus/get-azservicebusnamespace) |
| [New-AzureSBAuthorizationRule](/powershell/module/servicemanagement/azure.service/new-azuresbauthorizationrule) | [New-AzureRmServiceBusAuthorizationRule](/powershell/module/azurerm.servicebus/new-azurermservicebusauthorizationrule) | [New-AzServiceBusAuthorizationRule](/powershell/module/az.servicebus/new-azservicebusauthorizationrule) |
| [New-AzureSBNamespace](/powershell/module/servicemanagement/azure.service/new-azuresbnamespace) | [New-AzureRmServiceBusNamespace](/powershell/module/azurerm.servicebus/new-azurermservicebusnamespace) | [New-AzServiceBusNamespace](/powershell/module/az.servicebus/new-azservicebusnamespace) |
| [Remove-AzureSBAuthorizationRule](/powershell/module/servicemanagement/azure.service/remove-azuresbauthorizationrule) | [Remove-AzureRmServiceBusAuthorizationRule](/powershell/module/azurerm.servicebus/remove-azurermservicebusauthorizationrule) | [Remove-AzServiceBusAuthorizationRule](/powershell/module/az.servicebus/remove-azservicebusauthorizationrule) |
| [Remove-AzureSBNamespace](/powershell/module/servicemanagement/azure.service/remove-azuresbnamespace) | [Remove-AzureRmServiceBusNamespace](/powershell/module/azurerm.servicebus/remove-azurermservicebusnamespace) | [Remove-AzServiceBusNamespace](/powershell/module/az.servicebus/remove-azservicebusnamespace) |
| [Set-AzureSBAuthorizationRule](/powershell/module/servicemanagement/azure.service/set-azuresbauthorizationrule) | [Set-AzureRmServiceBusAuthorizationRule](/powershell/module/azurerm.servicebus/set-azurermservicebusauthorizationrule) | [Set-AzServiceBusAuthorizationRule](/powershell/module/az.servicebus/set-azservicebusauthorizationrule) |

## <a name="next-steps"></a>次のステップ
次のドキュメントを参照してください。 

- REST API の最新のドキュメント
    - [Azure Service Bus](/rest/api/servicebus/)
    - [Azure Event Hubs](/rest/api/eventhub/)
    - [Azure Relay](/rest/api/relay/)
- PowerShell の最新のドキュメント
    - [Azure Service Bus](/powershell/module/azurerm.servicebus/#service_bus)
    - [Azure Event Hubs](/powershell/module/azurerm.eventhub/#event_hub)
    - [Azure Event Grid](/powershell/module/azurerm.eventgrid/#event_grid)
