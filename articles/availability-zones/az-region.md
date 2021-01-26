---
title: Availability Zones をサポートする Azure サービス
description: Azure に高可用性で回復力のあるアプリケーションを作成できるように、Availability Zones ではリソースの実行に使用できる物理的に独立した場所が提供されています。
author: cynthn
ms.service: azure
ms.topic: article
ms.date: 12/17/2020
ms.author: cynthn
ms.custom: fasttrack-edit, mvc, references_regions
ms.openlocfilehash: 0365a60317538ba31f39928cd30a57e2c969c832
ms.sourcegitcommit: a4533b9d3d4cd6bb6faf92dd91c2c3e1f98ab86a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/22/2020
ms.locfileid: "97723042"
---
# <a name="azure-services-that-support-availability-zones"></a>Availability Zones をサポートする Azure サービス

Availability Zones とは高可用性を提供するサービスで、アプリケーションとデータをデータセンターの障害から保護します。 Availability Zones をサポートしている既存のリージョンと今後予定されているリージョンの一覧については、「[Azure のリージョンと Availability Zones](az-overview.md)」を参照してください。  

このセクションでは、Availability Zones がサポートされている Azure のサービスの一覧を表示します。 

各リージョンで利用可能なサービスと、今後の可用性のためのロードマップについては、[リージョン別の利用可能な製品](https://azure.microsoft.com/global-infrastructure/services/)に関するページを参照してください。

すべての Azure 管理サービスは、リージョン レベルの障害から回復できるように設計されています。 障害にはさまざまなものがありますが、可用性ゾーンの障害は、リージョン内で 1 つ以上発生しても、リージョン全体の障害と比べて、障害の範囲は狭くなります。 Azure では、リージョン内で発生した管理サービスのゾーン レベルの障害から復旧できます。 Azure では、リージョン内で 1 度に 1 つのゾーンで重要なメンテナンスが実行されます。これは、何らかの障害が発生した場合でも、リージョン内の可用性ゾーン全体にデプロイされたお客様のリソースに影響が及ばないようにするためです。


![3 つのゾーンがある Azure リージョンの概念図](./media/az-region/azure-region-availability-zones.png)


Availability Zones をサポートする Azure サービスは、**ゾーン**、**ゾーン冗長**、**非リージョン** サービスの 3 つのカテゴリに分類されます。 お客様のワークロードは、アプリケーションのパフォーマンスと持続性に合わせて、これらのいずれかのアーキテクチャ シナリオを利用するように分類できます。

- **ゾーン サービス** – 待機時間またはパフォーマンスのより厳しい要件を満たすために、自分で選んだ特定の Availability Zones にリソースをデプロイできます。  アプリケーションとデータをリージョン内の 1 つ以上のゾーンにレプリケートすることによって、回復性が自己設計されます。  リソースは特定のゾーンにピン留めできます。 たとえば、仮想マシン、マネージド ディスク、標準 IP アドレスなどを特定のゾーンに固定し、リソースの 1 つ以上のインスタンスを複数のゾーンに分散させることで回復性を高めることができます。

- **ゾーン冗長サービス** – Azure プラットフォームにより、リソースとデータがゾーン間でレプリケートされます。  Azure によってリージョン内のインスタンスが自動的にレプリケートされ、分散されるため、Microsoft が高可用性の提供を管理します。  たとえば、ZRS ではデータが 3 つのゾーンにレプリケートされるので、ゾーンの障害が発生しても、データの HA に影響を及ぼすことはありません。 

- **非リージョン サービス** – 特定の Azure リージョンに依存しないサービスです。これにより、ゾーン全体の障害やリージョン全体の障害に対する回復性が確保されます。


Azure での包括的なビジネス継続性を実現するには、Availability Zones と Azure リージョンの組み合わせを使用してアプリケーション アーキテクチャを構築します。 1 つの Azure リージョン内で Availability Zones を使用してアプリケーションとデータを同期的にレプリケートして高可用性を実現し、複数の Azure リージョン全体で非同期的にレプリケートしてディザスター リカバリー保護を実現できます。 詳細については、「[Availability Zones を使用した高可用性のためのソリューションの構築](https://docs.microsoft.com/azure/architecture/high-availability/building-solutions-for-high-availability)」をご覧ください。 


### <a name="azure-services-supporting-availability-zones"></a>Availability Zones をサポートしている Azure サービス

- 旧世代の仮想マシンは以下に表示されていません。 詳しくは、「[旧世代の仮想マシンのサイズ](../virtual-machines/sizes-previous-gen.md)」をご覧ください。

- 一部のサービスは非リージョンです。詳細については、「[Azure のリージョンと Availability Zones](az-overview.md)」をご覧ください。 これらのサービスは、特定の Azure リージョンに依存しません。そのため、ゾーン全体の障害やリージョン全体の障害に対する回復性が確保されます。  非リージョン サービスの一覧は、[リージョン別の利用可能な製品](https://azure.microsoft.com/global-infrastructure/services/)に関するページで参照できます。



## <a name="americas"></a>アメリカ

| **成果物** | **米国中部** | **米国東部** | **米国東部 2** | **米国西部 2** | **カナダ中部** |
|--|--|--|--|--|--|
| **Compute** |  |  |  |  |  |
| [App Service Environment (ILB)](../app-service/environment/zone-redundancy.md#how-to-deploy-an-app-service-environment-in-an-availability-zone) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Service Fabric](../service-fabric/service-fabric-cross-availability-zones.md) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Virtual Machines スケール セット](../virtual-machine-scale-sets/virtual-machine-scale-sets-use-availability-zones.md) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Virtual Machines](https://azure.microsoft.com/services/virtual-machines/) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | 
| **Containers** |  |  |  |
| [Azure Kubernetes Service (AKS)](../aks/availability-zones.md) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [コンテナー レジストリ](../container-registry/zone-redundancy.md) |  | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |  | 
| **Storage** |  |  |  |  |  |
| [Azure Data Lake Storage Gen2](../storage/common/storage-account-create.md?tabs=azure-portal)  | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Premium File Storage](../storage/files/storage-files-planning.md) |  | :heavy_check_mark: |  | :heavy_check_mark: | :heavy_check_mark: |
| [Blob Storage](../storage/blobs/storage-blobs-introduction.md) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Managed Disks](https://azure.microsoft.com/en-gb/updates/azure-managed-snapshots-images-ga/) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| **ネットワーク** |  |  |  |  |  |
| [Application Gateway V2](../application-gateway/application-gateway-autoscaling-zone-redundant.md)  | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Azure ExpressRoute](../expressroute/designing-for-high-availability-with-expressroute.md) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Azure Firewall](../firewall/deploy-availability-zone-powershell.md) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [標準 IP アドレス](../virtual-network/public-ip-addresses.md) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Load Balancer](../load-balancer/load-balancer-standard-availability-zones.md#concepts) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Virtual Network](../vpn-gateway/create-zone-redundant-vnet-gateway.md) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Virtual Network NAT](../virtual-network/nat-gateway-resource.md#availability-zones) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Virtual WAN](../virtual-wan/virtual-wan-faq.md) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [VPN Gateway](../vpn-gateway/about-zone-redundant-vnet-gateways.md) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| **データベース** |  |  |  |  |  |
| [Azure Cache for Redis](../azure-cache-for-redis/cache-overview.md) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Azure Cosmos DB](../cosmos-db/high-availability.md#availability-zone-support) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Azure Data Explorer](/azure/data-explorer/create-cluster-database-portal) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Azure Database for MySQL - フレキシブル サーバー](../mysql/flexible-server/concepts-high-availability.md) | :x: | :x: | :heavy_check_mark: | :heavy_check_mark: | :x: |
| [Azure Database for PostgreSQL - フレキシブル サーバー](../postgresql/flexible-server/overview.md) | :x: | :x: | :heavy_check_mark: | :heavy_check_mark: | :x: |
| [Azure SQL Database (General Purpose レベル)](../azure-sql/database/high-availability-sla.md#general-purpose-service-tier-zone-redundant-availability-preview) | :x: | :heavy_check_mark:(プレビュー) | :heavy_check_mark:(プレビュー) | :heavy_check_mark:(プレビュー) | :x: |
| [Azure SQL Database (Premium & Business Critical レベル)](../azure-sql/database/high-availability-sla.md#premium-and-business-critical-service-tier-zone-redundant-availability) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| **Analytics** |  |  |  |  |  |
| [Event Hubs](../event-hubs/index.yml) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| **統合** |  |  |  |  |  |
| [Event Grid](../event-grid/index.yml) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Service Bus](../service-fabric/service-fabric-cross-availability-zones.md) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| **管理とガバナンス** |  |  |  |  |  |
| [Network Watcher](../network-watcher/frequently-asked-questions.md) | :x: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :x: |
| **Security** |  |  |  |  |  |
| [Azure Active Directory Domain Services](../active-directory-domain-services/overview.md) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |  |

## <a name="europe"></a>ヨーロッパ

| **成果物** | **フランス中部** | **北ヨーロッパ** | **英国南部** | "**西ヨーロッパ**" |
|--|--|--|--|--|
| **Compute** |  |  |  |  |
| [App Service Environment (ILB)](../app-service/environment/zone-redundancy.md#how-to-deploy-an-app-service-environment-in-an-availability-zone) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Azure Kubernetes Service (AKS)](../aks/availability-zones.md) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Service Fabric](../service-fabric/service-fabric-cross-availability-zones.md) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Virtual Machines スケール セット](../virtual-machine-scale-sets/virtual-machine-scale-sets-use-availability-zones.md) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Virtual Machines](https://azure.microsoft.com/services/virtual-machines/) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| **Storage** |  |  |  |  |
| [Azure Data Lake Storage Gen2](../storage/common/storage-account-create.md?tabs=azure-portal)  | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Premium File Storage](../storage/files/storage-files-planning.md) |  | :heavy_check_mark: | :heavy_check_mark: |  |
| [Blob Storage](../storage/blobs/storage-blobs-introduction.md) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Managed Disks](https://azure.microsoft.com/en-gb/updates/azure-managed-snapshots-images-ga/) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| **ネットワーク** |  |  |  |  |
| [Application Gateway V2](../application-gateway/application-gateway-autoscaling-zone-redundant.md)  | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Azure ExpressRoute](../expressroute/designing-for-high-availability-with-expressroute.md) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Azure Firewall](../firewall/deploy-availability-zone-powershell.md) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [標準 IP アドレス](../virtual-network/public-ip-addresses.md) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Load Balancer](../load-balancer/load-balancer-standard-availability-zones.md#concepts) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Virtual Network](../vpn-gateway/create-zone-redundant-vnet-gateway.md) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Virtual Network NAT](../virtual-network/nat-gateway-resource.md#availability-zones) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Virtual WAN](../virtual-wan/virtual-wan-faq.md) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [VPN Gateway](../vpn-gateway/about-zone-redundant-vnet-gateways.md) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| **データベース** |  |  |  |  |
| [Azure Cache for Redis](../azure-cache-for-redis/cache-overview.md) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Azure Cosmos DB](../cosmos-db/high-availability.md#availability-zone-support) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Azure Data Explorer](/azure/data-explorer/create-cluster-database-portal) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Azure Database for MySQL - フレキシブル サーバー](../mysql/flexible-server/concepts-high-availability.md) | :x: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Azure Database for PostgreSQL - フレキシブル サーバー](../postgresql/flexible-server/overview.md) | :x: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Azure SQL Database (General Purpose レベル)](../azure-sql/database/high-availability-sla.md#general-purpose-service-tier-zone-redundant-availability-preview) | :x: | :heavy_check_mark:(プレビュー) | :x: | :heavy_check_mark:(プレビュー) |
| [Azure SQL Database (Premium & Business Critical レベル)](../azure-sql/database/high-availability-sla.md#premium-and-business-critical-service-tier-zone-redundant-availability) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| **Analytics** |  |  |  |  |
| [Event Hubs](../event-hubs/index.yml) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| **統合** |  |  |  |  |
| [Event Grid](../event-grid/index.yml) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Service Bus](../service-fabric/service-fabric-cross-availability-zones.md) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| **管理とガバナンス** |  |  |  |  |
| [Network Watcher](../network-watcher/frequently-asked-questions.md) | :heavy_check_mark: | :heavy_check_mark: | :x: | :heavy_check_mark: |
| **Security** |  |  |  |  |
| [Azure Active Directory Domain Services](../active-directory-domain-services/overview.md) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |  |

## <a name="asia-pacific"></a>アジア太平洋



| **成果物** | **Japan East** | **東南アジア** | **オーストラリア東部** |
|--|--|--|--|
| **Compute** |  |  |  |
| [App Service Environment (ILB)](../app-service/environment/zone-redundancy.md#how-to-deploy-an-app-service-environment-in-an-availability-zone) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Azure Kubernetes Service (AKS)](../aks/availability-zones.md) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Service Fabric](../service-fabric/service-fabric-cross-availability-zones.md) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Virtual Machines スケール セット](../virtual-machine-scale-sets/virtual-machine-scale-sets-use-availability-zones.md) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Virtual Machines](https://azure.microsoft.com/services/virtual-machines/) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| **Storage** |  |  |  |
| [Azure Data Lake Storage Gen2](../storage/common/storage-account-create.md?tabs=azure-portal)  | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Premium File Storage](../storage/files/storage-files-planning.md) |  | :heavy_check_mark: | :heavy_check_mark: |
| [Blob Storage](../storage/blobs/storage-blobs-introduction.md) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Managed Disks](https://azure.microsoft.com/en-gb/updates/azure-managed-snapshots-images-ga/) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| **ネットワーク** |  |  |  |
| [Application Gateway V2](../application-gateway/application-gateway-autoscaling-zone-redundant.md)  | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Azure ExpressRoute](../expressroute/designing-for-high-availability-with-expressroute.md) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Azure Firewall](../firewall/deploy-availability-zone-powershell.md) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [標準 IP アドレス](../virtual-network/public-ip-addresses.md) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Load Balancer](../load-balancer/load-balancer-standard-availability-zones.md#concepts) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Virtual Network](../vpn-gateway/create-zone-redundant-vnet-gateway.md) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Virtual Network NAT](../virtual-network/nat-gateway-resource.md#availability-zones) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Virtual WAN](../virtual-wan/virtual-wan-faq.md) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [VPN Gateway](../vpn-gateway/about-zone-redundant-vnet-gateways.md) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| **データベース** |  |  |  |
| [Azure Cache for Redis](../azure-cache-for-redis/cache-overview.md) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Azure Cosmos DB](../cosmos-db/high-availability.md#availability-zone-support) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Azure Data Explorer](/azure/data-explorer/create-cluster-database-portal) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Azure Database for MySQL - フレキシブル サーバー](../mysql/flexible-server/concepts-high-availability.md) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Azure Database for PostgreSQL - フレキシブル サーバー](../postgresql/flexible-server/overview.md) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Azure SQL Database (General Purpose レベル)](../azure-sql/database/high-availability-sla.md#general-purpose-service-tier-zone-redundant-availability-preview) | :x: | :heavy_check_mark:(プレビュー) | :heavy_check_mark:(プレビュー) |
| [Azure SQL Database (Premium & Business Critical レベル)](../azure-sql/database/high-availability-sla.md#premium-and-business-critical-service-tier-zone-redundant-availability) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| **Analytics** |  |  |  |
| [Event Hubs](../event-hubs/index.yml) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| **統合** |  |  |  |
| [Event Grid](../event-grid/index.yml) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Service Bus](../service-fabric/service-fabric-cross-availability-zones.md) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| **管理とガバナンス** |  |  |  |
| [Network Watcher](../network-watcher/frequently-asked-questions.md) | :heavy_check_mark: | :x: | :x: |
| **Security** |  |  |  |
| [Azure Active Directory Domain Services](../active-directory-domain-services/overview.md) | :heavy_check_mark: | :heavy_check_mark: |  |


## <a name="upcoming-availability-zones"></a>今後提供予定の Availability Zones 

Azure では、次のリージョンで Availability Zones サポートが提供されます。
- US Gov バージニア州
- 南アフリカ北部
- 米国中南部
- ドイツ中西部

Availability Zones をサポートしている既存のリージョンと、今後提供が予定されているリージョンの一覧については、[こちら](https://azure.microsoft.com/global-infrastructure/geographies/)を参照してください。    

これらのリージョンでの Availability Zones サポートの詳細については、Microsoft の営業担当者またはカスタマー担当者にお問い合わせください。


## <a name="pricing-for-vms-in-availability-zones"></a>Availability Zones の VM の料金

Availability Zones にデプロイされる仮想マシンに追加のコストは発生しません。 詳しくは、「[帯域幅の料金詳細](https://azure.microsoft.com/pricing/details/bandwidth/)」をご覧ください。


## <a name="get-started-with-availability-zones"></a>Availability Zones の使用を開始する

- [仮想マシンの作成](../virtual-machines/windows/create-portal-availability-zone.md)
- [PowerShell を使った管理ディスクの追加](../virtual-machines/windows/attach-disk-ps.md#add-an-empty-data-disk-to-a-virtual-machine)
- [ゾーン冗長仮想マシン スケール セットの作成](../virtual-machine-scale-sets/virtual-machine-scale-sets-use-availability-zones.md)
- [ゾーン冗長フロントエンドによる Standard Load Balancer を使用したゾーン間での VM の負荷分散](https://docs.microsoft.com/azure/load-balancer/quickstart-load-balancer-standard-public-cli?tabs=option-1-create-load-balancer-standard)
- [ゾーン フロントエンドによる Standard Load Balancer を使用した単一のゾーン内での VM の負荷分散](https://docs.microsoft.com/azure/load-balancer/quickstart-load-balancer-standard-public-cli?tabs=option-1-create-load-balancer-standard)
- [ゾーン冗長ストレージ](../storage/common/storage-redundancy.md)
- [SQL Database General Purpose レベル](../azure-sql/database/high-availability-sla.md#general-purpose-service-tier-zone-redundant-availability-preview)
- [Event Hubs の geo ディザスター リカバリー](../event-hubs/event-hubs-geo-dr.md#availability-zones)
- [Service Bus の geo ディザスター リカバリー](../service-bus-messaging/service-bus-geo-dr.md#availability-zones)
- [ゾーン冗長仮想ネットワーク ゲートウェイの作成](../vpn-gateway/create-zone-redundant-vnet-gateway.md)
- [Azure Cosmos DB のゾーン冗長リージョンの追加](../cosmos-db/high-availability.md#availability-zone-support)
- [Azure Cache for Redis Availability Zones の概要](https://gist.github.com/JonCole/92c669ea482bbb7996f6428fb6c3eb97#file-redisazgettingstarted-md)
- [Azure Active Directory Domain Services インスタンスの作成](https://docs.microsoft.com/azure/active-directory-domain-services/tutorial-create-instance)
- [可用性ゾーンを使用する Azure Kubernetes Service (AKS) クラスターを作成する](../aks/availability-zones.md)


## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [Azure のリージョンと Availability Zones](az-overview.md)
