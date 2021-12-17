---
title: Azure Germany で利用可能なサービス | Microsoft Docs
description: この記事では、Azure Germany で利用可能なサービスの概要を紹介します。
ms.topic: article
ms.date: 10/16/2020
author: gitralf
ms.author: ralfwi
ms.service: germany
ms.custom: bfdocs
ms.openlocfilehash: 523ef05023f669c5ff37f2d5f06ecc0d410faff0
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/21/2020
ms.locfileid: "117028982"
---
# <a name="available-services-in-azure-germany"></a>Azure Germany で利用可能なサービス

[!INCLUDE [closureinfo](../../includes/germany-closure-info.md)]

Azure Germany はサービスを拡張していません。 この記事では、Azure Germany で現在利用できるサービスについて説明します。 

>[!NOTE]
> サービスの最新の一覧については、「[リージョン別の利用可能な製品](https://azure.microsoft.com/regions/services/)」を参照してください。 
>
>

以下の表で Azure Resource Manager が有効になっているサービスは、リソース プロバイダーがあり、PowerShell を使って管理できます。 Resource Manager のプロバイダー、API のバージョン、およびスキーマの詳細については、[Resource Manager でサポートされるサービス](../azure-resource-manager/management/resource-providers-and-types.md)に関するページをご覧ください。 ポータルで利用可能として指定されているサービスは、[Azure Germany ポータル](https://portal.microsoftazure.de/)で管理できます。 

## <a name="compute"></a>[Compute](./germany-services-compute.md)

| サービス | リソース マネージャー | ポータル |
| --- | --- | --- |
| [Virtual Machines](./germany-services-compute.md#virtual-machines)  | はい | はい |
| Virtual Machine Scale Sets | はい | はい |
| Service Fabric | はい | はい |


## <a name="networking"></a>[ネットワーク](./germany-services-networking.md)

| サービス | リソース マネージャー | ポータル |
| --- | --- | --- |
| [ExpressRoute](./germany-services-networking.md#expressroute-private-connectivity) | はい | はい |
| Virtual Network | はい | はい |
| [Load Balancer](./germany-services-networking.md#support-for-load-balancer) | はい | はい |
| [Traffic Manager](./germany-services-networking.md#support-for-traffic-manager)  | はい | はい |
|  [VPN Gateway](./germany-services-networking.md#support-for-vpn-gateway) | はい | はい |
| Application Gateway | はい | はい |



## <a name="storage"></a>[Storage](./germany-services-storage.md)

| サービス | リソース マネージャー | ポータル |
| --- | --- | --- |
| [Storage](./germany-services-storage.md#storage) | はい | はい |
| StorSimple | いいえ | いいえ |
| バックアップ | はい | はい |
| Site Recovery | はい | はい |



## <a name="web-and-mobile"></a>[Web とモバイル](./germany-services-webandmobile.md)

| サービス | リソース マネージャー | ポータル |
| --- | --- | --- |
| [App Service: Web Apps](./germany-services-webandmobile.md#app-service) | はい | はい |
| [App Service: API Apps](./germany-services-webandmobile.md#app-service) | はい | はい |
| [App Service: Mobile Apps](./germany-services-webandmobile.md#app-service) | はい | はい |
| Media Services | はい | はい |


## <a name="databases"></a>[データベース](./germany-services-database.md)

| サービス | リソース マネージャー | ポータル |
| --- | --- | --- |
| [SQL Database](./germany-services-database.md#sql-database) | はい | はい |
| Azure Synapse Analytics | はい | はい |
| SQL Server Stretch Database | はい | はい |
| [Azure Cache for Redis](./germany-services-database.md#azure-cache-for-redis) | はい | はい |
| Azure Cosmos DB | はい | はい |


## <a name="intelligence-and-analytics"></a>インテリジェンスと分析

| サービス | リソース マネージャー | ポータル |
| --- | --- | --- |
| HDInsight | はい | はい |
| Machine Learning | はい | いいえ |


## <a name="internet-of-things-iot"></a>[モノのインターネット (IoT)](./germany-services-iot.md)

| サービス | リソース マネージャー | ポータル |
| --- | --- | --- |
| Event Hubs | はい | はい |
| IoT Hub | はい | はい |
| Notification Hubs | はい | いいえ |
| Stream Analytics | はい | はい |


## <a name="enterprise-integration"></a>エンタープライズ統合

| サービス | リソース マネージャー | ポータル |
| --- | --- | --- |
| Service Bus | はい | はい |
| StorSimple | いいえ | いいえ |
| SQL Server Stretch Database | はい | はい |



## <a name="security-and-identity"></a>[セキュリティと ID](./germany-services-securityandidentity.md)

| サービス | リソース マネージャー | ポータル |
| --- | --- | --- |
| Active Directory Free | はい | はい |
| Active Directory Premium | いいえ | いいえ |
|  [Key Vault](./germany-services-securityandidentity.md#key-vault)  | はい | いいえ |



## <a name="monitoring-and-management"></a>監視と管理

| サービス | リソース マネージャー | ポータル |
| --- | --- | --- |
| Automation | いいえ | いいえ |
| バックアップ | はい | はい |
| Azure Monitor ログ | いいえ | いいえ |
| Site Recovery | はい | はい |



## <a name="next-steps"></a>次のステップ
補足情報と最新情報については、[Microsoft Azure Germany ブログ](/archive/blogs/azuregermany/)をご覧ください。