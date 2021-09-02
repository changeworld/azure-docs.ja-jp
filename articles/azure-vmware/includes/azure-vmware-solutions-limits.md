---
title: Azure VMware Solution の制限
description: Azure VMware Solution の制限。
ms.topic: include
ms.date: 03/24/2021
author: shortpatti
ms.author: v-patsho
ms.service: azure-vmware
ms.openlocfilehash: 02840f5d74a1490b0af71933f795385b70467d7b
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/13/2021
ms.locfileid: "122014615"
---
<!-- Used in /azure/azure-resource-manager/management/azure-subscription-service-limits.md and concepts-networking.md -->

Azure VMware Solution の上限を次の表に示します。

| **リソース** | **制限** |
| :-- | :-- |
| プライベート クラウドあたりのクラスター数 | 12 |
| クラスターごとのホストの最小数 | 3 |
| クラスターごとのホストの最大数 | 16 |
| プライベート クラウドあたりのホスト数 | 96 |
| プライベート クラウドあたりの vCenter 数 | 1  |
| HCX サイトのペアリング数 | Advanced エディションの場合は 3、Enterprise エディションの場合は 10 |
| AVS ExpressRoute にリンクされるプライベート クラウドの最大数 | 4<br />実際にリンクされるプライベート クラウドの最大数は、使用される仮想ネットワーク ゲートウェイによって決まります。  詳細については、「[ExpressRoute の仮想ネットワーク ゲートウェイについて](../../expressroute/expressroute-about-virtual-network-gateways.md)」を参照してください | 
| AVS ExpressRoute のポート速度 | 10 Gbps<br />実際の帯域幅は、使用される仮想ネットワーク ゲートウェイによって決まります。 詳細については、「[ExpressRoute の仮想ネットワーク ゲートウェイについて](../../expressroute/expressroute-about-virtual-network-gateways.md)」を参照してください | 
| vWAN を介して公開されるパブリック IP 数 | 100 |
| vSAN の容量制限 | 使用可能な合計の 75% (SLA のために 25% を使用可能に保つ)  |

VMware 固有のその他の制限については、[VMware Configuration Maximums ツール](https://configmax.vmware.com/)を使用してください。
