---
title: Azure VMware Solution の制限
description: Azure VMware Solution の制限。
ms.topic: include
ms.date: 03/24/2021
ms.openlocfilehash: 856aca934b7a80b8f5dc7e336b91821ab299b34c
ms.sourcegitcommit: ad921e1cde8fb973f39c31d0b3f7f3c77495600f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/25/2021
ms.locfileid: "107951174"
---
<!-- Used in /azure/azure-resource-manager/management/azure-subscription-service-limits.md and concepts-networking.md -->

Azure VMware Solution の上限を次の表に示します。

| **リソース** | **制限** |
| :-- | :-- |
| プライベート クラウドあたりのクラスター数 | 12 |
| クラスターごとのノードの最小数 | 3 |
| クラスターごとのノードの最大数 | 16 |
| プライベート クラウドあたりのノード数 | 96 |
| プライベート クラウドあたりの vCenter 数 | 1  |
| HCX サイトのペアリング数 | Advanced エディションの場合は 3、Enterprise エディションの場合は 10 |
| AVS ExpressRoute にリンクされる SDDC の最大数 | 4 |
| AVS ExpressRoute のポート速度 | 10 Gbps<br />実際の帯域幅は、使用される仮想ネットワーク ゲートウェイによって決まります。 詳細については、「[ExpressRoute の仮想ネットワーク ゲートウェイについて](../../expressroute/expressroute-about-virtual-network-gateways.md)」を参照してください | 
| vWAN を介して公開されるパブリック IP 数 | 100 |
| vSAN の容量制限 | 使用可能な合計の 75% (SLA のために 25% を使用可能に保つ)  |

VMware 固有のその他の制限については、[VMware Configuration Maximums ツール](https://configmax.vmware.com/)を使用してください。
