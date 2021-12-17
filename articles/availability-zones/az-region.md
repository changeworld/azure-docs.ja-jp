---
title: 可用性ゾーンをサポートする Azure サービス
description: 可用性ゾーンでサポートされているサービスを把握し、すべての Azure サービスの回復性を理解しましょう。
author: prsandhu
ms.service: azure
ms.topic: conceptual
ms.date: 10/01/2021
ms.author: rarco
ms.reviewer: cynthn
ms.custom: references_regions
ms.openlocfilehash: 2c039bb9cabdd1d4786d4f0d76d591887781e2b5
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/11/2021
ms.locfileid: "132302123"
---
# <a name="azure-services-that-support-availability-zones"></a>可用性ゾーンをサポートする Azure サービス

Azure の[リージョンと可用性ゾーン](az-overview.md)は、各 Azure リージョン内の物理的に離れた場所であり、Azure サービスのインフラストラクチャは冗長化され、論理的に独立しているため、データセンターの障害に対して耐性があります。 

可用性ゾーンは、ネットワークの冗長性が重要です。 可用性ゾーンをサポートする Azure サービスは、適切なレベルの回復性、柔軟性と共に、非常に短い待機時間を実現するように設計されています。 独自の回復性を構築する場合でも、自動レプリケーションと配布を選択する場合でも、可用性ゾーンをサポートする Azure サービスを使用する利点は同じです。 サービスの種類に関係なく、可用性の高いサービス全体で優れた回復性が得られます。 

Azure は、すべてのサービスとオファリングで高い回復性を実現するよう努めています。 可用性ゾーンをサポートする Azure サービスを実行すると、ほぼすべてのシナリオに対して、中断することなく、完全に透過的で一貫性のある回復性が得られます。

## <a name="azure-regions-with-availability-zones"></a>可用性ゾーンを含む Azure のリージョン

Azure は、クラウド プロバイダーの中でも最も広範なグローバル フットプリントを提供し、新しいリージョンと可用性ゾーンを迅速に開設しています。 現在、次のリージョンが可用性ゾーンをサポートしています。

| アメリカ | ヨーロッパ | アフリカ | アジア太平洋 |
|--------------------|----------------------|---------------------|----------------|
| ブラジル南部 | フランス中部 | 南アフリカ北部 | オーストラリア東部 |
| カナダ中部 | ドイツ中西部 | | インド中部\* |
| 米国中部 | 北ヨーロッパ | | 東日本 |
| 米国東部 | ノルウェー東部 | | 韓国中部 |
| 米国東部 2 | 英国南部 | | 東南アジア |
| 米国中南部 | 西ヨーロッパ | | 東アジア |
| US Gov バージニア州 | スウェーデン | | |
| 米国西部 2 | | | |
| 米国西部 3 | | | |

\* このリージョンの可用性ゾーンおよび利用可能なサービス サポートの詳細については、Microsoft の営業またはカスタマー担当者にお問い合わせください。 可用性ゾーンのサポートが今後予定されているリージョンについては、「[Azure の地域](https://azure.microsoft.com/global-infrastructure/geographies/)」を参照してください。

可用性ゾーンをサポートする Azure リージョン別の Azure サービス一覧については、[可用性ゾーンのドキュメント](az-overview.md)を参照してください。

## <a name="highly-available-services"></a>高可用性サービス

*ゾーン型*、*ゾーン冗長型*、*常時利用可能型* サービスという 3 種類の Azure サービスが可用性ゾーンをサポートしています。 回復性戦略を設計するときは、これら 3 つのアーキテクチャ アプローチすべてを組み合わせることができます。

- **ゾーン型サービス**: 待機時間またはパフォーマンスのより厳しい要件を満たすために、自分で選んだ特定の可用性ゾーンにリソースをデプロイできます。 アプリケーションとデータをリージョン内の 1 つ以上のゾーンにレプリケートすることによって、回復性が自己設計されます。 リソースは特定のゾーンにピン留めできます。 たとえば、仮想マシン、マネージド ディスク、標準 IP アドレスなどを特定のゾーンに固定し、リソースの 1 つ以上のインスタンスを複数のゾーンに分散させることで回復性を高めることができます。
- **ゾーン冗長型サービス**: リソースはゾーン間で自動的にレプリケートまたは分散されます。 たとえば、ゾーン冗長型サービスの場合、1 つのゾーン内の障害がデータの高可用性に影響しないように、3 つのゾーンにデータがレプリケートされます。 
- **常時利用可能型サービス**: すべての Azure の地域で常時利用可能であり、ゾーン全体の停止やリージョン全体の停止に対して回復性があります。 Azure の常時利用可能型サービス (非リージョン型サービスとも呼ばれます) の完全な一覧については、「[リージョン別の利用可能な製品](https://azure.microsoft.com/global-infrastructure/services/)」を参照してください。

この記事で先ほど説明したように、すべての Azure サービスがすべてのリージョンで普遍的に使用できるわけではありません。 オファリングは、"_リージョン_" の可用性を反映して、"*基本*"、"*メインストリーム*"、"*戦略的*" という 3 つのカテゴリに分類されています。 特定リージョンでのサービスのデプロイに関する Azure の一般的なポリシーは、主にリージョンの種類、サービス カテゴリ、および顧客の要望によって決まります。 詳細については、「[Azure サービス](region-types-service-categories-azure.md)」を参照してください。

- **基本サービス**: リージョンが一般提供されている場合、または新しい基本サービスの一般提供から 90 日以内に、すべての推奨されるリージョンと代替リージョンで利用できます。
- **メインストリーム サービス**: リージョンの一般提供から 90 日以内にすべての推奨されるリージョンで利用可能です。 メインストリーム サービスは代替リージョンでの需要に応じて提供され、多くは既に代替リージョンの大規模なサブセットにデプロイされています。
- **戦略的サービス**: 特に業界に重点を置いているハードウェア、またはカスタマイズされたハードウェアによってサポートされる、対象指定のサービス内容。 戦略的サービスは、複数のリージョンにわたり、需要に応じて提供され、多くは、既に別のリージョンの大きなサブセットにデプロイされています。

可用性ゾーンをサポートする Azure サービスは、ゾーン型およびゾーン冗長型オファリングを含め、継続的に拡張されています。

旧世代の仮想マシンの詳細については、「[旧世代の仮想マシンのサイズ](../virtual-machines/sizes-previous-gen.md)」を参照してください。

次の表は、ゾーン型、ゾーン冗長型、および常時利用可能型 Azure サービスの現在のオファリングをまとめたものです。 Azure オファリングを、それぞれのリージョンでの可能性に応じて一覧に記載しています。

##### <a name="legend"></a>凡例
![表内の各サービスのサービスのカテゴリとリージョンの可用性に関するアイコンとその意味が記載されている凡例。](media/legend.png) 

製品カタログには、常時利用可能型サービスは "非リージョン型" サービスとして記載されています。

### <a name="an-icon-that-signifies-this-service-is-foundational-foundational-services"></a>![このサービスが基本であることを示すアイコン。](media/icon-foundational.svg) 基礎となるサービス 

| **成果物**   | **回復性**   |
| --- | --- |
| [Azure Application Gateway (V2)](../application-gateway/application-gateway-autoscaling-zone-redundant.md)  | ![このサービスがゾーン冗長型であることを示すアイコン。](media/icon-zone-redundant.svg) ![このサービスがゾーン型であることを示すアイコン。](media/icon-zonal.svg)  |
| [Azure Backup](../backup/backup-create-rs-vault.md)  | ![このサービスがゾーン型であることを示すアイコン。](media/icon-zonal.svg)  |
| [Azure Cosmos DB](../cosmos-db/high-availability.md#availability-zone-support)  | ![このサービスがゾーン冗長型であることを示すアイコン。](media/icon-zone-redundant.svg)   |
| [Azure Data Lake Storage Gen 2](../storage/blobs/data-lake-storage-introduction.md)  | ![このサービスがゾーン冗長型であることを示すアイコン。](media/icon-zone-redundant.svg)  |
| [Azure DNS: Azure DNS Private Zones](../dns/private-dns-getstarted-portal.md)  | ![このサービスがゾーン冗長型であることを示すアイコン。](media/icon-zone-redundant.svg)  |
| [Azure ExpressRoute](../expressroute/designing-for-high-availability-with-expressroute.md)  | ![このサービスがゾーン冗長型であることを示すアイコン。](media/icon-zone-redundant.svg)  |
| [Azure パブリック IP](../virtual-network/ip-services/public-ip-addresses.md)  | ![このサービスがゾーン冗長型であることを示すアイコン。](media/icon-zone-redundant.svg) ![このサービスがゾーン型であることを示すアイコン。](media/icon-zonal.svg)  |
| Azure SQL Database ([General Purpose レベル](../azure-sql/database/high-availability-sla.md))  | ![このサービスがゾーン冗長型であることを示すアイコン。](media/icon-zone-redundant.svg)  |
| Azure SQL Database ([Premium &amp; Business Critical レベル](../azure-sql/database/high-availability-sla.md))  | ![このサービスがゾーン冗長型であることを示すアイコン。](media/icon-zone-redundant.svg)  |
| [Azure Disk Storage](../storage/common/storage-redundancy.md)  | ![このサービスがゾーン冗長型であることを示すアイコン。](media/icon-zone-redundant.svg)  |
| [Azure Event Hubs](../event-hubs/event-hubs-geo-dr.md#availability-zones)  | ![このサービスがゾーン冗長型であることを示すアイコン。](media/icon-zone-redundant.svg)  |
| [Azure Key Vault](../key-vault/general/disaster-recovery-guidance.md)  | ![このサービスがゾーン冗長型であることを示すアイコン。](media/icon-zone-redundant.svg)  |
| [Azure Load Balancer](../load-balancer/load-balancer-standard-availability-zones.md)  | ![このサービスがゾーン冗長型であることを示すアイコン。](media/icon-zone-redundant.svg) ![このサービスがゾーン型であることを示すアイコン。](media/icon-zonal.svg)  |
| [Azure Service Bus](../service-bus-messaging/service-bus-geo-dr.md#availability-zones)  | ![このサービスがゾーン冗長型であることを示すアイコン。](media/icon-zone-redundant.svg)  |
| [Azure Service Fabric](../service-fabric/service-fabric-cross-availability-zones.md)  | ![このサービスがゾーン冗長型であることを示すアイコン。](media/icon-zone-redundant.svg) ![このサービスがゾーン型であることを示すアイコン。](media/icon-zonal.svg)  |
| [Azure Storage アカウント](../storage/common/storage-redundancy.md)  | ![このサービスがゾーン冗長型であることを示すアイコン。](media/icon-zone-redundant.svg)  |
| ストレージ:  [ホットまたはクール Azure Blob Storage レベル](../storage/common/storage-redundancy.md)  | ![このサービスがゾーン冗長型であることを示すアイコン。](media/icon-zone-redundant.svg)  |
| ストレージ:  [Managed Disks](../virtual-machines/managed-disks-overview.md)  | ![このサービスがゾーン冗長型であることを示すアイコン。](media/icon-zone-redundant.svg) ![このサービスがゾーン型であることを示すアイコン。](media/icon-zonal.svg)  |
| [Azure Virtual Machine Scale Sets](../virtual-machine-scale-sets/scripts/cli-sample-zone-redundant-scale-set.md)  | ![このサービスがゾーン冗長型であることを示すアイコン。](media/icon-zone-redundant.svg) ![このサービスがゾーン型であることを示すアイコン。](media/icon-zonal.svg)  |
| [Azure Virtual Machines](../virtual-machines/windows/create-powershell-availability-zone.md)  | ![このサービスがゾーン型であることを示すアイコン。](media/icon-zonal.svg)  |
| Virtual Machines:  [Av2 シリーズ](../virtual-machines/windows/create-powershell-availability-zone.md)  | ![このサービスがゾーン型であることを示すアイコン。](media/icon-zonal.svg)  |
| Virtual Machines:  [Bs シリーズ](../virtual-machines/windows/create-powershell-availability-zone.md)  | ![このサービスがゾーン型であることを示すアイコン。](media/icon-zonal.svg)  |
| Virtual Machines:  [DSv2 シリーズ](../virtual-machines/windows/create-powershell-availability-zone.md)  | ![このサービスがゾーン型であることを示すアイコン。](media/icon-zonal.svg)  |
| Virtual Machines:  [DSv3 シリーズ](../virtual-machines/windows/create-powershell-availability-zone.md)  | ![このサービスがゾーン型であることを示すアイコン。](media/icon-zonal.svg)  |
| Virtual Machines:  [Dv2 シリーズ](../virtual-machines/windows/create-powershell-availability-zone.md)  | ![このサービスがゾーン型であることを示すアイコン。](media/icon-zonal.svg)  |
| Virtual Machines:  [Dv3 シリーズ](../virtual-machines/windows/create-powershell-availability-zone.md)  | ![このサービスがゾーン型であることを示すアイコン。](media/icon-zonal.svg)  |
| Virtual Machines:  [ESv3 シリーズ](../virtual-machines/windows/create-powershell-availability-zone.md)  | ![このサービスがゾーン型であることを示すアイコン。](media/icon-zonal.svg)  |
| Virtual Machines:  [Ev3 シリーズ](../virtual-machines/windows/create-powershell-availability-zone.md)  | ![このサービスがゾーン型であることを示すアイコン。](media/icon-zonal.svg)  |
| Virtual Machines:  [F シリーズ](../virtual-machines/windows/create-powershell-availability-zone.md)  | ![このサービスがゾーン型であることを示すアイコン。](media/icon-zonal.svg)  |
| Virtual Machines:  [FS シリーズ](../virtual-machines/windows/create-powershell-availability-zone.md)  | ![このサービスがゾーン型であることを示すアイコン。](media/icon-zonal.svg)  |
| Virtual Machines:  [Shared Image Gallery](../virtual-machines/shared-image-galleries.md#make-your-images-highly-available)  | ![このサービスがゾーン冗長型であることを示すアイコン。](media/icon-zone-redundant.svg)  |
| [Azure Virtual Network](../vpn-gateway/create-zone-redundant-vnet-gateway.md)  | ![このサービスがゾーン冗長型であることを示すアイコン。](media/icon-zone-redundant.svg)  |
| [Azure VPN Gateway](../vpn-gateway/about-zone-redundant-vnet-gateways.md)  | ![このサービスがゾーン冗長型であることを示すアイコン。](media/icon-zone-redundant.svg)  |
### <a name="an-icon-that-signifies-this-service-is-mainstream-mainstream-services"></a>![このサービスがメインストリームであることを示すアイコン。](media/icon-mainstream.svg) メインストリーム サービス

| **成果物**   | **回復性**   |
| --- | --- |
| [App Service](../app-service/how-to-zone-redundancy.md)  | ![このサービスがゾーン冗長型であることを示すアイコン。](media/icon-zone-redundant.svg) ![このサービスがゾーン型であることを示すアイコン。](media/icon-zonal.svg)  |
| [Azure App Service 環境](../app-service/environment/zone-redundancy.md)  | ![このサービスがゾーン冗長型であることを示すアイコン。](media/icon-zone-redundant.svg) ![このサービスがゾーン型であることを示すアイコン。](media/icon-zonal.svg)  |
| [Azure Active Directory Domain Services](../active-directory-domain-services/overview.md)  | ![このサービスがゾーン冗長型であることを示すアイコン。](media/icon-zone-redundant.svg)  |
| [Azure API Management](../api-management/zone-redundancy.md)  | ![このサービスがゾーン型であることを示すアイコン。](media/icon-zonal.svg)  |
| [Azure App Configuration](../azure-app-configuration/faq.yml#how-does-app-configuration-ensure-high-data-availability)  | ![このサービスがゾーン冗長型であることを示すアイコン。](media/icon-zone-redundant.svg)  |
| [Azure Bastion](../bastion/bastion-overview.md)  | ![このサービスがゾーン冗長型であることを示すアイコン。](media/icon-zone-redundant.svg)  |
| [Azure Batch](../batch/create-pool-availability-zones.md)  | ![このサービスがゾーン冗長型であることを示すアイコン。](media/icon-zone-redundant.svg)  |
| [Azure Cache for Redis](../azure-cache-for-redis/cache-high-availability.md)  | ![このサービスがゾーン冗長型であることを示すアイコン。](media/icon-zone-redundant.svg) ![このサービスがゾーン型であることを示すアイコン。](media/icon-zonal.svg)  |
| [Azure Cognitive Search](../search/search-performance-optimization.md#availability-zones)  | ![このサービスがゾーン冗長型であることを示すアイコン。](media/icon-zone-redundant.svg)  |
| Azure Cognitive Services:  [Text Analytics](../cognitive-services/text-analytics/index.yml)  | ![このサービスがゾーン冗長型であることを示すアイコン。](media/icon-zone-redundant.svg)  |
| [Azure Data Explorer](/azure/data-explorer/create-cluster-database-portal)  | ![このサービスがゾーン冗長型であることを示すアイコン。](media/icon-zone-redundant.svg)  |
| [Azure Data Factory](../data-factory/index.yml)  | ![このサービスがゾーン冗長型であることを示すアイコン。](media/icon-zone-redundant.svg)  |
| Azure Database for MySQL -  [フレキシブル サーバー](../mysql/flexible-server/concepts-high-availability.md)  | ![このサービスがゾーン冗長型であることを示すアイコン。](media/icon-zone-redundant.svg)  |
| Azure Database for PostgreSQL -  [フレキシブル サーバー](../postgresql/flexible-server/overview.md)  | ![このサービスがゾーン冗長型であることを示すアイコン。](media/icon-zone-redundant.svg)  |
| [Azure DDoS Protection](../ddos-protection/ddos-faq.yml)  | ![このサービスがゾーン冗長型であることを示すアイコン。](media/icon-zone-redundant.svg) |
| [Azure Disk Encryption](../virtual-machines/disks-redundancy.md)  | ![このサービスがゾーン冗長型であることを示すアイコン。](media/icon-zone-redundant.svg) |
| [Azure Firewall](../firewall/deploy-availability-zone-powershell.md)  | ![このサービスがゾーン冗長型であることを示すアイコン。](media/icon-zone-redundant.svg)  |
| [Azure Firewall Manager](../firewall-manager/quick-firewall-policy.md)  | ![このサービスがゾーン冗長型であることを示すアイコン。](media/icon-zone-redundant.svg)  |
| [Azure Functions](https://azure.github.io/AppService/2021/08/25/App-service-support-for-availability-zones.html)  | ![このサービスがゾーン冗長型であることを示すアイコン。](media/icon-zone-redundant.svg)  |
| [Azure Kubernetes Service (AKS)](../aks/availability-zones.md)  | ![このサービスがゾーン冗長型であることを示すアイコン。](media/icon-zone-redundant.svg) ![このサービスがゾーン型であることを示すアイコン。](media/icon-zonal.svg)  |
| [Azure Monitor](../azure-monitor/logs/availability-zones.md)  | ![このサービスがゾーン冗長型であることを示すアイコン。](media/icon-zone-redundant.svg)  |
| [Azure Monitor:Application Insights](../azure-monitor/logs/availability-zones.md)  | ![このサービスがゾーン冗長型であることを示すアイコン。](media/icon-zone-redundant.svg)  |
| [Azure Monitor:Log Analytics](../azure-monitor/logs/availability-zones.md)  | ![このサービスがゾーン冗長型であることを示すアイコン。](media/icon-zone-redundant.svg)  |
| [Azure Private Link](../private-link/private-link-overview.md)  | ![このサービスがゾーン冗長型であることを示すアイコン。](media/icon-zone-redundant.svg)  |
| [Azure Site Recovery](../site-recovery/azure-to-azure-how-to-enable-zone-to-zone-disaster-recovery.md)  | ![このサービスがゾーン型であることを示すアイコン。](media/icon-zonal.svg)  |
| Azure SQL:  [Virtual Machine](../azure-sql/database/high-availability-sla.md)  | ![このサービスがゾーン冗長型であることを示すアイコン。](media/icon-zone-redundant.svg) |
| [Azure Web アプリケーション ファイアウォール](../firewall/deploy-availability-zone-powershell.md)  | ![このサービスがゾーン冗長型であることを示すアイコン。](media/icon-zone-redundant.svg)  |
| [Azure Container Registry](../container-registry/zone-redundancy.md)  | ![このサービスがゾーン冗長型であることを示すアイコン。](media/icon-zone-redundant.svg)  |
| [Azure Event Grid](../event-grid/overview.md)  | ![このサービスがゾーン冗長型であることを示すアイコン。](media/icon-zone-redundant.svg)  |
| [Azure HDInsight](../hdinsight/hdinsight-use-availability-zones.md)  | ![このサービスがゾーン型であることを示すアイコン。](media/icon-zonal.svg)  |
| [Azure Network Watcher](../network-watcher/frequently-asked-questions.yml)  | ![このサービスがゾーン冗長型であることを示すアイコン。](media/icon-zone-redundant.svg)  |
| Network Watcher:  [Traffic Analytics](../network-watcher/frequently-asked-questions.yml)  | ![このサービスがゾーン冗長型であることを示すアイコン。](media/icon-zone-redundant.svg)  |
| [Power BI Embedded](/power-bi/admin/service-admin-failover#what-does-high-availability)  | ![このサービスがゾーン冗長型であることを示すアイコン。](media/icon-zone-redundant.svg)  |
| [Premium Blob Storage](../storage/common/storage-account-overview.md)  | ![このサービスがゾーン冗長型であることを示すアイコン。](media/icon-zone-redundant.svg)  |
| ストレージ:  [Azure Premium ファイル](../storage/files/storage-files-planning.md)  | ![このサービスがゾーン冗長型であることを示すアイコン。](media/icon-zone-redundant.svg)  |
| 仮想マシン:  [Azure Dedicated Host](../virtual-machines/windows/create-powershell-availability-zone.md)  | ![このサービスがゾーン型であることを示すアイコン。](media/icon-zonal.svg)  |
| Virtual Machines:  [Ddsv4 シリーズ](../virtual-machines/windows/create-powershell-availability-zone.md)  | ![このサービスがゾーン型であることを示すアイコン。](media/icon-zonal.svg)  |
| Virtual Machines:  [Ddv4 シリーズ](../virtual-machines/windows/create-powershell-availability-zone.md)  | ![このサービスがゾーン型であることを示すアイコン。](media/icon-zonal.svg)  |
| Virtual Machines:  [Dsv4 シリーズ](../virtual-machines/windows/create-powershell-availability-zone.md)  | ![このサービスがゾーン型であることを示すアイコン。](media/icon-zonal.svg)  |
| Virtual Machines:  [Dv4 シリーズ](../virtual-machines/windows/create-powershell-availability-zone.md)  | ![このサービスがゾーン型であることを示すアイコン。](media/icon-zonal.svg)  |
| Virtual Machines:  [Edsv4 シリーズ](../virtual-machines/windows/create-powershell-availability-zone.md)  | ![このサービスがゾーン型であることを示すアイコン。](media/icon-zonal.svg)  |
| Virtual Machines:  [Edv4 シリーズ](../virtual-machines/windows/create-powershell-availability-zone.md)  | ![このサービスがゾーン型であることを示すアイコン。](media/icon-zonal.svg)  |
| Virtual Machines:  [Esv4 シリーズ](../virtual-machines/windows/create-powershell-availability-zone.md)  | ![このサービスがゾーン型であることを示すアイコン。](media/icon-zonal.svg)  |
| Virtual Machines:  [Ev4 シリーズ](../virtual-machines/windows/create-powershell-availability-zone.md)  | ![このサービスがゾーン型であることを示すアイコン。](media/icon-zonal.svg)  |
| Virtual Machines:  [Fsv2 シリーズ](../virtual-machines/windows/create-powershell-availability-zone.md)  | ![このサービスがゾーン型であることを示すアイコン。](media/icon-zonal.svg)  |
| Virtual Machines:  [M シリーズ](../virtual-machines/windows/create-powershell-availability-zone.md)  | ![このサービスがゾーン型であることを示すアイコン。](media/icon-zonal.svg)  |
| [Azure Virtual WAN](../virtual-wan/virtual-wan-faq.md#how-are-availability-zones-and-resiliency-handled-in-virtual-wan)  | ![このサービスがゾーン冗長型であることを示すアイコン。](media/icon-zone-redundant.svg)  |
| Virtual WAN:  [Azure ExpressRoute](../virtual-wan/virtual-wan-faq.md#how-are-availability-zones-and-resiliency-handled-in-virtual-wan)  | ![このサービスがゾーン冗長型であることを示すアイコン。](media/icon-zone-redundant.svg)  |
| Virtual WAN:  [ポイント対サイト VPN Gateway](../vpn-gateway/about-zone-redundant-vnet-gateways.md)  | ![このサービスがゾーン冗長型であることを示すアイコン。](media/icon-zone-redundant.svg)  |
| Virtual WAN:  [サイト間 VPN Gateway](../vpn-gateway/about-zone-redundant-vnet-gateways.md)  | ![このサービスがゾーン冗長型であることを示すアイコン。](media/icon-zone-redundant.svg)  |

### <a name="an-icon-that-signifies-this-service-is-strategic-strategic-services"></a>![このサービスが戦略的であることを示すアイコン。](media/icon-strategic.svg) 戦略的サービス

| **成果物**   | **回復性**   |
| --- | --- |
| Azure Red Hat OpenShift  | ![このサービスがゾーン冗長型であることを示すアイコン。](media/icon-zone-redundant.svg) ![このサービスがゾーン型であることを示すアイコン。](media/icon-zonal.svg)  |
| Azure Cognitive Services: Anomaly Detector  | ![このサービスがゾーン冗長型であることを示すアイコン。](media/icon-zone-redundant.svg)  |
| Azure Cognitive Services: Form Recognizer  | ![このサービスがゾーン冗長型であることを示すアイコン。](media/icon-zone-redundant.svg)  |
| Azure Storage: Ultra Disk  | ![このサービスがゾーン型であることを示すアイコン。](media/icon-zonal.svg)  |

### <a name="an-icon-that-signifies-this-service-is-non-regional-non-regional-services-always-available-services"></a>![このサービスが非リージョン型であることを示すアイコン。](media/icon-always-available.svg) 非リージョン型サービス (常時利用型サービス)

| **成果物**   | **回復性**   |
| --- | --- |
| Azure DNS  | ![このサービスが常時利用可能型であることを示すアイコン。](media/icon-always-available.svg) |
| Azure Active Directory  | ![このサービスが常時利用可能型であることを示すアイコン。](media/icon-always-available.svg) |
| Azure Advanced Threat Protection  | ![このサービスが常時利用可能型であることを示すアイコン。](media/icon-always-available.svg) |
| Azure Advisor  | ![このサービスが常時利用可能型であることを示すアイコン。](media/icon-always-available.svg) |
| Azure Blueprint  | ![このサービスが常時利用可能型であることを示すアイコン。](media/icon-always-available.svg) |
| Azure Bot Services  | ![このサービスが常時利用可能型であることを示すアイコン。](media/icon-always-available.svg) |
| Azure Front Door  | ![このサービスが常時利用可能型であることを示すアイコン。](media/icon-always-available.svg) |
| Microsoft Defender for IoT  | ![このサービスが常時利用可能型であることを示すアイコン。](media/icon-always-available.svg) |
| Azure Front Door  | ![このサービスが常時利用可能型であることを示すアイコン。](media/icon-always-available.svg) |
| Azure Information Protection  | ![このサービスが常時利用可能型であることを示すアイコン。](media/icon-always-available.svg) |
| Azure Lighthouse  | ![このサービスが常時利用可能型であることを示すアイコン。](media/icon-always-available.svg) |
| Azure Managed Applications  | ![このサービスが常時利用可能型であることを示すアイコン。](media/icon-always-available.svg) |
| Azure Maps  | ![このサービスが常時利用可能型であることを示すアイコン。](media/icon-always-available.svg) |
| Azure Performance Diagnostics  | ![このサービスが常時利用可能型であることを示すアイコン。](media/icon-always-available.svg) |
| Azure Policy  | ![このサービスが常時利用可能型であることを示すアイコン。](media/icon-always-available.svg) |
| Azure Resource Graph  | ![このサービスが常時利用可能型であることを示すアイコン。](media/icon-always-available.svg) |
| Microsoft Sentinel  | ![このサービスが常時利用可能型であることを示すアイコン。](media/icon-always-available.svg) |
| Azure Stack  | ![このサービスが常時利用可能型であることを示すアイコン。](media/icon-always-available.svg) |
| Azure Stack Edge  | ![このサービスが常時利用可能型であることを示すアイコン。](media/icon-always-available.svg) |
| Azure Cloud Shell  | ![このサービスが常時利用可能型であることを示すアイコン。](media/icon-always-available.svg) |
| Azure Content Delivery Network  | ![このサービスが常時利用可能型であることを示すアイコン。](media/icon-always-available.svg) |
| Azure Cost Management および Billing | ![このサービスが常時利用可能型であることを示すアイコン。](media/icon-always-available.svg) |
| Microsoft Azure 用カスタマー ロックボックス  | ![このサービスが常時利用可能型であることを示すアイコン。](media/icon-always-available.svg) |
| Microsoft Intune  | ![このサービスが常時利用可能型であることを示すアイコン。](media/icon-always-available.svg) |
| Azure Peering Service  | ![このサービスが常時利用可能型であることを示すアイコン。](media/icon-always-available.svg) |
| Azure portal  | ![このサービスが常時利用可能型であることを示すアイコン。](media/icon-always-available.svg) |
| Microsoft Defender for Cloud Apps  | ![このサービスが常時利用可能型であることを示すアイコン。](media/icon-always-available.svg) |
| Microsoft Graph  | ![このサービスが常時利用可能型であることを示すアイコン。](media/icon-always-available.svg) |
| Microsoft Defender for Cloud  | ![このサービスが常時利用可能型であることを示すアイコン。](media/icon-always-available.svg) |
| Azure の Traffic Manager  | ![このサービスが常時利用可能型であることを示すアイコン。](media/icon-always-available.svg) |

可用性ゾーンをサポートする Azure リージョン別の Azure サービス一覧については、[可用性ゾーンのドキュメント](az-overview.md)を参照してください。

## <a name="pricing-for-virtual-machines-in-availability-zones"></a>可用性ゾーン内の仮想マシンの価格

Azure サブスクリプションを使用して Azure 可用性ゾーンにアクセスできます。 詳細については、「[帯域幅の価格](https://azure.microsoft.com/pricing/details/bandwidth/)」を参照してください。

## <a name="next-steps"></a>次の手順

- [Availability Zones を使用した高可用性のためのソリューションの構築](/azure/architecture/high-availability/building-solutions-for-high-availability)
- [Azure サービスでの高可用性](/azure/architecture/framework/resiliency/overview)
- [可用性のための設計パターン](/azure/architecture/framework/resiliency/app-design)
