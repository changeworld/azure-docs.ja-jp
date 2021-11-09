---
title: Azure サービス
description: Azure のリージョンの種類とサービス カテゴリについて説明します。
author: mamccrea
ms.service: azure
ms.topic: conceptual
ms.date: 10/01/2021
ms.author: mamccrea
ms.reviewer: cynthn
ms.custom: references_regions
ms.openlocfilehash: 7a8159a10749e877c6502ef775071af6cc420f46
ms.sourcegitcommit: 61f87d27e05547f3c22044c6aa42be8f23673256
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/09/2021
ms.locfileid: "132059516"
---
# <a name="azure-services"></a>Azure サービス

Azure リージョンの枠を超えたサービスの可用性は、リージョンの種類によって異なります。 Azure には、"*推奨*" と "*代替*" という 2 種類のリージョンがあります。

- **推奨**: 最も広範なサービス機能が提供されるリージョンです。現在は可用性ゾーンがサポートされています。 Azure portal では **[推奨]** に指定されています。
- **代替**: 推奨されるリージョンが現在存在するデータ所在地の境界内で Azure のフットプリントを拡張するリージョンです。 代替リージョンは、待機時間を最適化し、ディザスター リカバリーのニーズに対応する 2 つ目のリージョンを提供します。しかし、可用性ゾーンはサポートされていません。 Azure 上で代替リージョンの定期的な評価が行われ、推奨されるリージョンにすべきかどうかが判断されます。 Azure portal では **[その他]** に指定されています。

## <a name="service-categories-across-region-types"></a>リージョンの種類を超えたサービス カテゴリ

Azure サービスは、"*基本*"、"*メインストリーム*"、"*戦略的*" という 3 つのカテゴリに分類されています。 特定のリージョンへのサービスのデプロイに関する Azure の一般的なポリシーは、主にリージョンの種類、サービス カテゴリ、および顧客の需要によって決まります。

- **基本**: リージョンが一般提供されている場合、または新しい基本サービスの一般提供から 90 日以内に、すべての推奨されるリージョンと代替リージョンで利用できます。
- **メインストリーム**: リージョンの一般提供から 90 日以内にすべての推奨されるリージョンで利用可能です。 代替リージョンでの需要に応じて提供され、多くは既に代替リージョンの大規模なサブセットにデプロイされています。
- **戦略的** (以前の専用): 業界に重点を置いているか、カスタマイズされたハードウェアによってサポートされることが多い、対象指定のサービス内容。 複数のリージョンにわたり、需要に応じて提供され、多くは、既に別のリージョンの大きなサブセットにデプロイされています。

リージョンにデプロイされているサービスと、リージョン内のサービスのプレビューまたは一般提供のロードマップを確認するには、「[リージョン別の利用可能な製品](https://azure.microsoft.com/global-infrastructure/services/)」を参照してください。

リージョンでサービス内容を利用できない場合は、Microsoft の営業担当者に詳細を問い合わせ、オプションをご確認ください。

| リージョンの種類 | リージョン依存なし | 基本 | メインストリーム | 戦略的 | 可用性ゾーン | データの保存場所 |
| --- | --- | --- | --- | --- | --- | --- |
| 推奨 | **Y** | **Y** | **Y** | 需要主導型 | **Y** | **Y** |
| 代替 | **Y** | **Y** | 需要主導型 | 需要主導型 | 該当なし | **Y** |

## <a name="available-services-by-category"></a>カテゴリ別の利用可能なサービス

サービス カテゴリは、一般提供時に Azure 上で基本、メインストリーム、戦略的として割り当てられます。 通常、サービスは戦略的なサービスとして開始され、需要と使用率の増加に応じてメインストリームおよび基本にアップグレードされます。

次の表に、Azure サービスをカテゴリ別に示します。 一部のサービスはリージョンに依存しないことにご注意ください。 つまり、リージョンに関係なく、グローバルに使用できます。 リージョンに依存しないサービスの詳細と完全な一覧については、「[リージョン別の利用可能な製品](https://azure.microsoft.com/global-infrastructure/services/)」を参照してください。

> [!div class="mx-tableFixed"]
> | ![このサービスが基本であることを示すアイコン。](media/icon-foundational.svg) 基本                           | ![このサービスがメインストリームであることを示すアイコン。](media/icon-mainstream.svg) メインストリーム                                        | 
> |----------------------------------------|---------------------------------------------------|
> | Azure ストレージ アカウント                 | Azure API Management                              | 
> | Azure Application Gateway              | Azure App Configuration                           | 
> | Azure Backup                           | Azure App Service                                 | 
> | Azure Cosmos DB                        | Azure Automation                                  | 
> | Azure Data Lake Storage Gen2           | Azure Active Directory Domain Services            | 
> | Azure ExpressRoute                     | Azure Bastion                                     | 
> | Azure パブリック IP                        | Azure Cache for Redis                             | 
> | Azure SQL データベース                     | Azure Cognitive Services                          | 
> | Azure SQL Managed Instance             | Azure Cognitive Services: Computer Vision         | 
> | Disk Storage                           | Azure Cognitive Services: Content Moderator       | 
> | Azure Event Hubs                       | Azure Cognitive Services: Face                    | 
> | Azure Key Vault                        | Azure Cognitive Services: Text Analytics          | 
> | Azure Load Balancer                    | Azure Data Explorer                               | 
> | Azure Service Bus                      | Azure Database for MySQL                          | 
> | Azure Service Fabric                   | Azure Database for PostgreSQL                     | 
> | Azure Storage: ホットまたはクール BLOB ストレージ レベル   | Azure DDoS Protection                       | 
> | ストレージ:Managed Disks                 | Azure Firewall                                    | 
> | Azure 仮想マシン スケール セット       | Azure Firewall Manager                            | 
> | Azure Virtual Machines                 | Azure Functions                                   | 
> | 仮想マシン: Azure Dedicated Host | Azure IoT Hub                                     | 
> | Virtual Machines: Av2 シリーズ           | Azure Kubernetes Service (AKS)                    | 
> | Virtual Machines: Bs シリーズ            | Azure Monitor:Application Insights               | 
> | Virtual Machines: DSv2 シリーズ          | Azure Monitor:Log Analytics                      | 
> | Virtual Machines: DSv3 シリーズ          | Azure Private Link                                | 
> | Virtual Machines: Dv2 シリーズ           | Azure Site Recovery                               | 
> | Virtual Machines: Dv3 シリーズ           | Azure Synapse Analytics                           |     
> | Virtual Machines: ESv3 シリーズ          | Azure Batch                                       | 
> | Virtual Machines: Ev3 シリーズ           | Azure クラウド サービス: M シリーズ                     | 
> | Azure Virtual Network                  | Azure Container Instances                         | 
> | Azure VPN Gateway                      | Azure Container Registry                          | 
> |                                        | Azure Data Factory                                | 
> |                                        | Azure Event Grid                                  | 
> |                                        | Azure HDInsight                                   |  
> |                                        | Azure Logic Apps                                  | 
> |                                        | Azure Media Services                              | 
> |                                        | Azure Network Watcher                             | 
> |                                        | Premium Blob Storage                              | 
> |                                        | Premium File Storage                             | 
> |                                        | Virtual Machines: Ddsv4 シリーズ                    | 
> |                                        | Virtual Machines: Ddv4 シリーズ                     | 
> |                                        | Virtual Machines: Dsv4 シリーズ                     | 
> |                                        | Virtual Machines: Dv4 シリーズ                      | 
> |                                        | Virtual Machines: Edsv4 シリーズ                    | 
> |                                        | Virtual Machines: Edv4 シリーズ                     | 
> |                                        | Virtual Machines: Esv4 シリーズ                     | 
> |                                        | Virtual Machines: Ev4 シリーズ                      | 
> |                                        | Virtual Machines: Fsv2 シリーズ                     | 
> |                                        | Virtual Machines: M シリーズ                        | 
> |                                        | Azure Virtual WAN                                 | 

### <a name="strategic-services"></a>戦略的サービス
前述のように、Azure のサービスは、基本、メインストリーム、戦略的という 3 つのカテゴリに分類されています。 サービス カテゴリは、一般提供の際に割り当てられます。 多くの場合、サービスのライフサイクルは戦略的サービスから始まり、その需要や使用率が増加すると、メインストリーム サービスまたは基本サービスに昇格される可能性があります。 次の表は、戦略的サービスの一覧です。 

> [!div class="mx-tableFixed"]
> | ![このサービスが戦略的であることを示すアイコン。](media/icon-strategic.svg) 戦略的                                          |
> |------------------------------------------------------|
> | FHIR 用の Azure API                                   |
> | Azure Analysis Services                              |
> | Azure Blockchain Service                             |
> | Azure Cognitive Services: Anomaly Detector           |
> | Azure Cognitive Services: Custom Vision              |
> | Azure Cognitive Services: Form Recognizer            |
> | Azure Cognitive Services: Immersive Reader           |
> | Azure Cognitive Services: Language Understanding     |
> | Azure Cognitive Services: Personalizer               |
> | Azure Cognitive Services: QnA Maker                  |
> | Azure Cognitive Services: Speech Services            |
> | Azure Data Share                                     |
> | Azure Databricks                                     |
> | Azure Database for MariaDB                           |
> | Azure Database Migration Service                     |
> | Azure の専用 HSM                                  |
> | Azure Digital Twins                                  |
> | Azure Health Bot                                     |
> | Azure HPC Cache                                      |
> | Azure Lab Services                                   |
> | Azure NetApp Files                                   |
> | Azure Red Hat OpenShift                              |
> | Azure SignalR Service                                |
> | Azure Spring Cloud                                   |
> | Azure Stream Analytics                               |
> | Azure Time Series Insights                           |
> | Azure VMware Solution                                |
> | Azure VMware Solution by CloudSimple                 |
> | Azure Spatial Anchors                                |
> | ストレージ:Archive Storage                             |
> | Azure Ultra Disk Storage                             |
> | Video Indexer                                        |
> | Virtual Machines: DASv4 シリーズ                       |
> | Virtual Machines: DAv4 シリーズ                        |
> | Virtual Machines: DCsv2 シリーズ                       |
> | Virtual Machines: EASv4 シリーズ                       |
> | Virtual Machines: EAv4 シリーズ                        |
> | Virtual Machines: HBv1 シリーズ                        |
> | Virtual Machines: HBv2 シリーズ                        |
> | Virtual Machines: HCv1 シリーズ                        |
> | Virtual Machines: H シリーズ                           |
> | Virtual Machines: LSv2 シリーズ                        |
> | Virtual Machines: Mv2 シリーズ                         |
> | Virtual Machines: NCv3 シリーズ                        |
> | Virtual Machines: NDv2 シリーズ                        |
> | Virtual Machines: NVv3 シリーズ                        |
> | Virtual Machines: NVv4 シリーズ                        | 
> | Virtual Machines: SAP HANA on Azure Large Instances  |

\*可用性ゾーンをサポートする VM: AV2 シリーズ、B シリーズ、DSv2 シリーズ、DSv3 シリーズ、Dv2 シリーズ、Dv3 シリーズ、ESv3 シリーズ、Ev3 シリーズ、F シリーズ、FS シリーズ、FSv2 シリーズ、M シリーズ。\*

旧世代のサービスや仮想マシンは一覧表示されていません。 詳しくは、「[旧世代の仮想マシンのサイズ](../virtual-machines/sizes-previous-gen.md)」をご覧ください。

まだ一般提供されていないプレビュー サービスの詳細と、これらのサービスの一覧については、「[リージョン別の利用可能な製品](https://azure.microsoft.com/global-infrastructure/services/)」を参照してください。 可用性ゾーンをサポートするサービスの完全な一覧については、「[Availability Zones をサポートする Azure サービス](az-region.md)」を参照してください。

## <a name="next-steps"></a>次の手順

- [Availability Zones をサポートする Azure サービス](az-region.md)
- [Azure のリージョンと可用性ゾーン](az-overview.md)
