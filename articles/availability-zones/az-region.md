---
title: Availability Zones をサポートする Azure サービス
description: Azure に高可用性で回復力のあるアプリケーションを作成できるように、Availability Zones ではリソースの実行に使用できる物理的に独立した場所が提供されています。
author: prsandhu
ms.service: azure
ms.topic: conceptual
ms.date: 04/13/2021
ms.author: prsandhu
ms.reviewer: cynthn
ms.custom: fasttrack-edit, mvc, references_regions
ms.openlocfilehash: fd4443e4ef6bcecacc03ce190bbf438288c9ec60
ms.sourcegitcommit: dddd1596fa368f68861856849fbbbb9ea55cb4c7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/13/2021
ms.locfileid: "107364058"
---
# <a name="azure-services-that-support-availability-zones"></a>Availability Zones をサポートする Azure サービス

Microsoft Azure グローバル インフラストラクチャは、最高レベルの冗長性と回復性をお客様に提供するために、すべてのレイヤーで設計および構築されています。 Azure インフラストラクチャは、地域、リージョン、Availability Zones で構成されており、障害の影響範囲を抑えることで、お客様のアプリケーションとデータへの潜在的な影響を抑えます。 Azure Availability Zones コンストラクトは、データセンターの障害から保護し、高可用性 (HA) の向上を実現するソフトウェアおよびネットワーク ソリューションをお客様に提供するために開発されました。

Availability Zones は、Azure リージョン内の一意の物理的な場所です。 各ゾーンは、独立した電源、冷却手段、ネットワークを備えた 1 つ以上のデータセンターで構成されています。 Availability Zones はリージョン内で物理的に分離されているため、ゾーンの障害 (サイトへのアクセス、安全な移動、ユーティリティの稼働時間の延長、リソースの可用性を妨げる可能性のある、大規模な洪水、暴風雨や巨大暴風雨、その他のイベントなど) によるアプリケーションとデータへの影響を抑えることができます。 Availability Zones とその関連するデータセンターは、1 つのゾーンが侵害された場合に、リージョン内の他の Availability Zones によって、サービス、容量、可用性がサポートされるように設計されています。

すべての Azure 管理サービスは、リージョン レベルの障害から回復できるように設計されています。 障害にはさまざまなものがありますが、可用性ゾーンの障害は、リージョン内で 1 つ以上発生しても、リージョン全体の障害と比べて、障害の範囲は狭くなります。 Azure では、リージョン内で発生した管理サービスのゾーン レベルの障害から復旧できます。 Azure では、リージョン内で 1 度に 1 つのゾーンで重要なメンテナンスが実行されます。これは、何らかの障害が発生した場合でも、リージョン内の可用性ゾーン全体にデプロイされたお客様のリソースに影響が及ばないようにするためです。


![3 つのゾーンがある Azure リージョンの概念図](./media/az-region/azure-region-availability-zones.png)


Availability Zones をサポートする Azure サービスは、**ゾーン**、**ゾーン冗長**、**非リージョン** サービスの 3 つのカテゴリに分類されます。 お客様のワークロードは、アプリケーションのパフォーマンスと持続性に合わせて、これらのいずれかのアーキテクチャ シナリオを利用するように分類できます。

- **ゾーン サービス** – 待機時間またはパフォーマンスのより厳しい要件を満たすために、自分で選んだ特定の Availability Zones にリソースをデプロイできます。  アプリケーションとデータをリージョン内の 1 つ以上のゾーンにレプリケートすることによって、回復性が自己設計されます。  リソースは特定のゾーンにピン留めできます。 たとえば、仮想マシン、マネージド ディスク、標準 IP アドレスなどを特定のゾーンに固定し、リソースの 1 つ以上のインスタンスを複数のゾーンに分散させることで回復性を高めることができます。

- **ゾーン冗長サービス** – Azure プラットフォームにより、リソースとデータがゾーン間でレプリケートされます。  Azure によってリージョン内のインスタンスが自動的にレプリケートされ、分散されるため、Microsoft が高可用性の提供を管理します。  たとえば、ZRS ではデータが 3 つのゾーンにレプリケートされるので、ゾーンの障害が発生しても、データの HA に影響を及ぼすことはありません。 

- **非リージョン サービス** – サービスは常に Azure 地域から利用でき、リージョン全体の停止だけでなく、ゾーン全体の停止に対する回復力があります。 


Azure での包括的なビジネス継続性を実現するには、Availability Zones と Azure リージョンの組み合わせを使用してアプリケーション アーキテクチャを構築します。 1 つの Azure リージョン内で Availability Zones を使用してアプリケーションとデータを同期的にレプリケートして高可用性を実現し、複数の Azure リージョン全体で非同期的にレプリケートしてディザスター リカバリー保護を実現できます。 詳細については、「[Availability Zones を使用した高可用性のためのソリューションの構築](/azure/architecture/high-availability/building-solutions-for-high-availability)」をご覧ください。 

## <a name="azure-services-supporting-availability-zones"></a>Availability Zones をサポートしている Azure サービス

 - 旧世代の仮想マシンは一覧表示されていません。 詳しくは、「[旧世代の仮想マシンのサイズ](../virtual-machines/sizes-previous-gen.md)」をご覧ください。
 - 「[Azure のリージョンと Availability Zones](az-overview.md)」に記載されているとおり、一部のサービスは非リージョンです。 これらのサービスは特定の Azure リージョンに依存しません。そのため、リージョン全体の停止だけでなく、ゾーン全体の停止に対する回復力があります。  非リージョン サービスの一覧は、[リージョン別の利用可能な製品](https://azure.microsoft.com/global-infrastructure/services/)に関するページで参照できます。


## <a name="azure-regions-with-availability-zones"></a>Azure のリージョンと Availability Zones
 

| アメリカ           | ヨーロッパ               | アフリカ              | アジア太平洋   |
|--------------------|----------------------|---------------------|----------------|
|                    |                      |                     |                |
| ブラジル南部       | フランス中部       | 南アフリカ北部* | オーストラリア東部 |
| カナダ中部     | ドイツ中西部 |                     | インド中部* |
| 米国中部         | 北ヨーロッパ         |                     | 東日本     |
| 米国東部            | 英国南部             |                     | 韓国中部* |
| 米国東部 2          | 西ヨーロッパ          |                     | 東南アジア |
| 米国中南部 |                      |                     |                |
| US Gov バージニア州    |                      |                     |                |
| 米国西部 2        |                      |                     |                |
| 米国西部 3*       |                      |                     |                |

\* これらのリージョンの Availability Zones および利用可能なサービス サポートの詳細については、Microsoft の営業またはカスタマー担当者にお問い合わせください。 Availability Zones のサポートが今後予定されているリージョンについては、「[Azure の地域](https://azure.microsoft.com/en-us/global-infrastructure/geographies/)」を参照してください。


## <a name="azure-services-supporting-availability-zones"></a>Availability Zones をサポートしている Azure サービス

- 旧世代の仮想マシンは以下に表示されていません。 詳しくは、「[旧世代の仮想マシンのサイズ](../virtual-machines/sizes-previous-gen.md)」をご覧ください。

- 一部のサービスは非リージョンです。詳細については、「[Azure のリージョンと Availability Zones](az-overview.md)」をご覧ください。 これらのサービスは、特定の Azure リージョンに依存しません。そのため、ゾーン全体の障害やリージョン全体の障害に対する回復性が確保されます。  非リージョン サービスの一覧は、[リージョン別の利用可能な製品](https://azure.microsoft.com/global-infrastructure/services/)に関するページで参照できます。


### <a name="zone-resilient-services"></a>ゾーンの回復性があるサービス 

:globe_with_meridians:非リージョン サービス – サービスは常に Azure 地域から利用でき、リージョン全体の停止だけでなく、ゾーン全体の停止に対して回復性があります。

:large_blue_diamond: ゾーン全体の停止に対して回復性がある 

**基礎となるサービス**

|     製品                                                    | 回復性             |
|-----------------------------------------------------------------|:----------------------------:|
|     [Application Gateway (V2)](https://docs.microsoft.com/azure/application-gateway/application-gateway-autoscaling-zone-redundant)                                  | :large_blue_diamond:  |
|     [Azure Backup](https://docs.microsoft.com/azure/backup/backup-create-rs-vault#set-storage-redundancy)                                                | :large_blue_diamond:  |
|     [Azure Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/high-availability#availability-zone-support)                                           | :large_blue_diamond:  |
|     [Azure Data Lake Storage Gen 2](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-introduction)                             | :large_blue_diamond:  |
|     [Azure Express Route](https://docs.microsoft.com/azure/expressroute/designing-for-high-availability-with-expressroute)                                       | :large_blue_diamond:  |
|     [Azure パブリック IP](https://docs.microsoft.com/azure/virtual-network/public-ip-addresses)                                           | :large_blue_diamond:  |
|     Azure SQL Database ([General Purpose レベル](https://docs.microsoft.com/azure/azure-sql/database/high-availability-sla))                 | :large_blue_diamond:  |
|     Azure SQL Database ([Premium & Business Critical レベル](https://docs.microsoft.com/azure/azure-sql/database/high-availability-sla))     | :large_blue_diamond:  |
|     [Disk Storage](https://docs.microsoft.com/azure/storage/common/storage-redundancy)                                                | :large_blue_diamond:  |
|     [Event Hubs](https://docs.microsoft.com/azure/event-hubs/event-hubs-geo-dr#availability-zones)                                                  | :large_blue_diamond:  |
|     [Key Vault](https://docs.microsoft.com/azure/key-vault/general/disaster-recovery-guidance)                                                   | :large_blue_diamond:  |
|     [Load Balancer](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-availability-zones)                                               | :large_blue_diamond:  |
|     [Service Bus](https://docs.microsoft.com/azure/service-bus-messaging/service-bus-geo-dr#availability-zones)                                                 | :large_blue_diamond:  |
|     [Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-cross-availability-zones#:~:text=An%20Availability%20Zone%20is%20a%20unique%20physical%20location,zones.%20This%20will%20ensure%20high-availability%20of%20your%20applications)                                            | :large_blue_diamond:  |
|     [ストレージ アカウント](https://docs.microsoft.com/azure/storage/common/storage-redundancy)                                           | :large_blue_diamond:  |
|     ストレージ: [ホット/クール BLOB ストレージ レベル](https://docs.microsoft.com/azure/storage/common/storage-redundancy)                      | :large_blue_diamond:  |
|     ストレージ: [Managed Disks](https://docs.microsoft.com/azure/virtual-machines/managed-disks-overview)                                    | :large_blue_diamond:  |
|     [Virtual Machines Scale Sets](https://docs.microsoft.com/azure/virtual-machine-scale-sets/scripts/cli-sample-zone-redundant-scale-set)                               | :large_blue_diamond:  |
|     [Virtual Machines](https://docs.microsoft.com/azure/virtual-machines/windows/create-powershell-availability-zone)                                          | :large_blue_diamond:  |
|     Virtual Machines: [Av2 シリーズ](https://docs.microsoft.com/azure/virtual-machines/windows/create-powershell-availability-zone)                              | :large_blue_diamond:  |
|     Virtual Machines: [Bs シリーズ](https://docs.microsoft.com/azure/virtual-machines/windows/create-powershell-availability-zone)                               | :large_blue_diamond:  |
|     Virtual Machines: [DSv2 シリーズ](https://docs.microsoft.com/azure/virtual-machines/windows/create-powershell-availability-zone)                             | :large_blue_diamond:  |
|     Virtual Machines: [DSv3 シリーズ](https://docs.microsoft.com/azure/virtual-machines/windows/create-powershell-availability-zone)                            | :large_blue_diamond:  |
|     Virtual Machines: [Dv2 シリーズ](https://docs.microsoft.com/azure/virtual-machines/windows/create-powershell-availability-zone)                             | :large_blue_diamond:  |
|     Virtual Machines: [Dv3 シリーズ](https://docs.microsoft.com/azure/virtual-machines/windows/create-powershell-availability-zone)                              | :large_blue_diamond:  |
|     Virtual Machines: [ESv3 シリーズ](https://docs.microsoft.com/azure/virtual-machines/windows/create-powershell-availability-zone)                             | :large_blue_diamond:  |
|     Virtual Machines: [Ev3 シリーズ](https://docs.microsoft.com/azure/virtual-machines/windows/create-powershell-availability-zone)                              | :large_blue_diamond:  |
|     Virtual Machines: [F シリーズ](https://docs.microsoft.com/azure/virtual-machines/windows/create-powershell-availability-zone)                                | :large_blue_diamond:  |
|     Virtual Machines: [FS シリーズ](https://docs.microsoft.com/azure/virtual-machines/windows/create-powershell-availability-zone)                               | :large_blue_diamond:  |
|     Virtual Machines: [Shared Image Gallery](https://docs.microsoft.com/azure/virtual-machines/shared-image-galleries#make-your-images-highly-available) | :large_blue_diamond:  |
|     [仮想ネットワーク](https://docs.microsoft.com/azure/vpn-gateway/create-zone-redundant-vnet-gateway)                                         | :large_blue_diamond:  |
|     [VPN Gateway](https://docs.microsoft.com/azure/vpn-gateway/about-zone-redundant-vnet-gateways)                                             | :large_blue_diamond:  |


**メインストリーム サービス**


|     製品                                                    | 回復性             |
|-----------------------------------------------------------------|:----------------------------:|
|     [App Service Environment](https://docs.microsoft.com/azure/app-service/environment/zone-redundancy)                                    | :large_blue_diamond:  |
|     [Azure Active Directory Domain Services](https://docs.microsoft.com/azure/active-directory-domain-services/overview)                      | :large_blue_diamond:  |
|     [Azure Bastion](https://docs.microsoft.com/azure/bastion/bastion-overview)                                               | :large_blue_diamond:  |
|     [Azure Cache for Redis](https://docs.microsoft.com/azure/azure-cache-for-redis/cache-high-availability)                              | :large_blue_diamond:  |
|     [Azure Cognitive Search](https://docs.microsoft.com/azure/search/search-performance-optimization#availability-zones)               | :large_blue_diamond:  |
|     Azure Cognitive Services: [Text Analytics](https://docs.microsoft.com/azure/cognitive-services/text-analytics/)                    | :large_blue_diamond:  |
|     [Azure Data Explorer](https://docs.microsoft.com/azure/data-explorer/create-cluster-database-portal)                               | :large_blue_diamond:  |
|     Azure Database for MySQL – [フレキシブル サーバー](https://docs.microsoft.com/azure/mysql/flexible-server/concepts-high-availability)                  | :large_blue_diamond:  |
|     Azure Database for PostgreSQL – [フレキシブル サーバー](https://docs.microsoft.com/azure/postgresql/flexible-server/overview)             | :large_blue_diamond:  |
|     [Azure DDoS Protection](https://docs.microsoft.com/azure/ddos-protection/ddos-faq)                                       | :large_blue_diamond:  |
|     [Azure Disk Encryption](https://docs.microsoft.com/azure/virtual-machines/disks-redundancy)                                       | :large_blue_diamond:  |
|     [Azure Firewall](https://docs.microsoft.com/azure/firewall/deploy-availability-zone-powershell#:~:text=For%20more%20information%20about%20Azure%20Firewall%20Availability%20Zones%2C,This%20creates%20a%20zone-redundant%20IP%20address%20by%20default)                                              | :large_blue_diamond:  |
|     [Azure Firewall Manager](https://docs.microsoft.com/azure/firewall-manager/quick-firewall-policy)                                      | :large_blue_diamond:  |
|     [Azure Kubernetes Service (AKS)](https://docs.microsoft.com/azure/aks/availability-zones)                              | :large_blue_diamond:  |
|     [Azure Private Link](https://docs.microsoft.com/azure/private-link/private-link-overview)                                          | :large_blue_diamond:  |
|     [Azure Site Recovery](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-how-to-enable-zone-to-zone-disaster-recovery)                                         | :large_blue_diamond:  |
|     Azure SQL: [仮想マシン](https://docs.microsoft.com/azure/azure-sql/database/high-availability-sla)                                  | :large_blue_diamond:  |
|     [Azure Web アプリケーション ファイアウォール](https://docs.microsoft.com/azure/firewall/deploy-availability-zone-powershell#:~:text=For%20more%20information%20about%20Azure%20Firewall%20Availability%20Zones%2C,This%20creates%20a%20zone-redundant%20IP%20address%20by%20default)                              | :large_blue_diamond:  |
|     [コンテナー レジストリ](https://docs.microsoft.com/azure/container-registry/zone-redundancy)                                          | :large_blue_diamond:  |
|     [Event Grid](https://docs.microsoft.com/azure/event-grid/overview)                                                  | :large_blue_diamond:  |
|     [Network Watcher](https://docs.microsoft.com/azure/network-watcher/frequently-asked-questions#service-availability-and-redundancy)                                             | :large_blue_diamond:  |
|     Network Watcher: [Traffic Analytics](https://docs.microsoft.com/azure/network-watcher/frequently-asked-questions#service-availability-and-redundancy)                          | :large_blue_diamond:  |
|     [Power BI Embedded](https://docs.microsoft.com/power-bi/admin/service-admin-failover#what-does-high-availability)                                           | :large_blue_diamond:  |
|     [Premium Blob Storage](https://docs.microsoft.com/azure/storage/blobs/storage-blob-performance-tiers#:~:text=Table%201%20%20%20%20Area%20%20,%20%20Currently%20supports%20only%20locally-redundan%20...%20)                                        | :large_blue_diamond:  |
|     ストレージ: [Azure Premium Files](https://docs.microsoft.com/azure/storage/files/storage-files-planning)                                | :large_blue_diamond:  |
|     仮想マシン: [Azure Dedicated Host](https://docs.microsoft.com/azure/virtual-machines/windows/create-powershell-availability-zone)                     | :large_blue_diamond:  |
|     Virtual Machines: [Ddsv4 シリーズ](https://docs.microsoft.com/azure/virtual-machines/windows/create-powershell-availability-zone)                              | :large_blue_diamond:  |
|     Virtual Machines: [Ddv4 シリーズ](https://docs.microsoft.com/azure/virtual-machines/windows/create-powershell-availability-zone)                               | :large_blue_diamond:  |
|     Virtual Machines: [Dsv4 シリーズ](https://docs.microsoft.com/azure/virtual-machines/windows/create-powershell-availability-zone)                               | :large_blue_diamond:  |
|     Virtual Machines: [Dv4 シリーズ](https://docs.microsoft.com/azure/virtual-machines/windows/create-powershell-availability-zone)                                | :large_blue_diamond:  |
|     Virtual Machines: [Edsv4 シリーズ](https://docs.microsoft.com/azure/virtual-machines/windows/create-powershell-availability-zone)                              | :large_blue_diamond:  |
|     Virtual Machines: [Edv4 シリーズ](https://docs.microsoft.com/azure/virtual-machines/windows/create-powershell-availability-zone)                               | :large_blue_diamond:  |
|     Virtual Machines: [Esv4 シリーズ](https://docs.microsoft.com/azure/virtual-machines/windows/create-powershell-availability-zone)                               | :large_blue_diamond:  |
|     Virtual Machines: [Ev4 シリーズ](https://docs.microsoft.com/azure/virtual-machines/windows/create-powershell-availability-zone)                                | :large_blue_diamond:  |
|     Virtual Machines: [Fsv2 シリーズ](https://docs.microsoft.com/azure/virtual-machines/windows/create-powershell-availability-zone)                               | :large_blue_diamond:  |
|     Virtual Machines: [M シリーズ](https://docs.microsoft.com/azure/virtual-machines/windows/create-powershell-availability-zone)                                  | :large_blue_diamond:  |
|     [Virtual WAN](https://docs.microsoft.com/azure/virtual-wan/virtual-wan-about#how-are-availability-zones-and-resiliency-handled-in-virtual-wan)                                                 | :large_blue_diamond:  |
|     Virtual WAN: [ExpressRoute](https://docs.microsoft.com/azure/virtual-wan/virtual-wan-about#how-are-availability-zones-and-resiliency-handled-in-virtual-wan)                                   | :large_blue_diamond:  |
|     Virtual WAN: [ポイント対サイト VPN Gateway](https://docs.microsoft.com/azure/vpn-gateway/about-zone-redundant-vnet-gateways)                      | :large_blue_diamond:  |
|     Virtual WAN: [サイト間 VPN Gateway](https://docs.microsoft.com/azure/vpn-gateway/about-zone-redundant-vnet-gateways)                       | :large_blue_diamond:  |


**専用サービス**

|     製品                                                    | 回復性             |
|-----------------------------------------------------------------|:----------------------------:|
|     Azure Red Hat OpenShift                                     | :large_blue_diamond:  |
|     Cognitive Services:Anomaly Detector                        | :large_blue_diamond:  |
|     Cognitive Services:Form Recognizer                         | :large_blue_diamond:  |
|     ストレージ: Ultra Disk                                         | :large_blue_diamond:  |


**リージョン依存なし**

|     製品                                                    | 回復性             |
|-----------------------------------------------------------------|:----------------------------:|
|     Azure DNS                                                   | :globe_with_meridians: |
|     Azure Active Directory                                    | :globe_with_meridians: |
|     Azure Advanced Threat Protection                            | :globe_with_meridians: |
|     Azure Advisor                                               | :globe_with_meridians: |
|     Azure Blueprint                                            | :globe_with_meridians: |
|     Azure Bot Services                                          | :globe_with_meridians: |
|     Azure Front Door                                            | :globe_with_meridians: |
|     Azure Defender for IoT                                    | :globe_with_meridians: |
|     Azure Front Door                                            | :globe_with_meridians: |
|     Azure Information Protection                              | :globe_with_meridians: |
|     Azure Lighthouse                                          | :globe_with_meridians: |
|     Azure Managed Applications                                | :globe_with_meridians: |
|     Azure Maps                                                  | :globe_with_meridians: |
|     Azure Performance Diagnostics                               | :globe_with_meridians: |
|     Azure Policy                                                | :globe_with_meridians: |
|     Azure Resource Graph                                      | :globe_with_meridians: |
|     Azure Sentinel                                              | :globe_with_meridians: |
|     Azure Stack                                                 | :globe_with_meridians: |
|     Azure Stack Edge                                          | :globe_with_meridians: |
|     Cloud Shell                                                 | :globe_with_meridians: |
|     Content Delivery Network                                    | :globe_with_meridians: |
|     Cost Management                                             | :globe_with_meridians: |
|     Microsoft Azure 用カスタマー ロックボックス                      | :globe_with_meridians: |
|     Intune                                                      | :globe_with_meridians: |
|     Microsoft Azure Peering Service                           | :globe_with_meridians: |
|     Microsoft Azure portal                                    | :globe_with_meridians: |
|     Microsoft Cloud App Security                                | :globe_with_meridians: |
|     Microsoft Graph                                             | :globe_with_meridians: |
|     Security Center                                           | :globe_with_meridians: |
|     Traffic Manager                                           | :globe_with_meridians: |


## <a name="pricing-for-vms-in-availability-zones"></a>Availability Zones の VM の料金

Azure Availability Zones は、Azure サブスクリプションで使用できます。 詳細については、「[帯域幅の料金に関するページ](https://azure.microsoft.com/pricing/details/bandwidth/)」をご覧ください。


## <a name="get-started-with-availability-zones"></a>Availability Zones の使用を開始する

- [仮想マシンの作成](../virtual-machines/windows/create-portal-availability-zone.md)
- [PowerShell を使った管理ディスクの追加](../virtual-machines/windows/attach-disk-ps.md#add-an-empty-data-disk-to-a-virtual-machine)
- [ゾーン冗長仮想マシン スケール セットの作成](../virtual-machine-scale-sets/virtual-machine-scale-sets-use-availability-zones.md)
- [ゾーン冗長フロントエンドによる Standard Load Balancer を使用したゾーン間での VM の負荷分散](../load-balancer/quickstart-load-balancer-standard-public-cli.md?tabs=option-1-create-load-balancer-standard)
- [ゾーン フロントエンドによる Standard Load Balancer を使用した単一のゾーン内での VM の負荷分散](../load-balancer/quickstart-load-balancer-standard-public-cli.md?tabs=option-1-create-load-balancer-standard)
- [ゾーン冗長ストレージ](../storage/common/storage-redundancy.md)
- [SQL Database General Purpose レベル](../azure-sql/database/high-availability-sla.md#general-purpose-service-tier-zone-redundant-availability-preview)
- [Event Hubs の geo ディザスター リカバリー](../event-hubs/event-hubs-geo-dr.md#availability-zones)
- [Service Bus の geo ディザスター リカバリー](../service-bus-messaging/service-bus-geo-dr.md#availability-zones)
- [ゾーン冗長仮想ネットワーク ゲートウェイの作成](../vpn-gateway/create-zone-redundant-vnet-gateway.md)
- [Azure Cosmos DB のゾーン冗長リージョンの追加](../cosmos-db/high-availability.md#availability-zone-support)
- [Azure Cache for Redis Availability Zones の概要](https://gist.github.com/JonCole/92c669ea482bbb7996f6428fb6c3eb97#file-redisazgettingstarted-md)
- [Azure Active Directory Domain Services インスタンスの作成](../active-directory-domain-services/tutorial-create-instance.md)
- [可用性ゾーンを使用する Azure Kubernetes Service (AKS) クラスターを作成する](../aks/availability-zones.md)


## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [Azure のリージョンと Availability Zones](az-overview.md)
