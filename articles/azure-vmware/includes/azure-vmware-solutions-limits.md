---
title: Azure VMware Solution の制限
description: Azure VMware Solution の制限。
ms.topic: include
ms.date: 09/02/2021
author: suzizuber
ms.author: v-szuber
ms.service: azure-vmware
ms.openlocfilehash: 7a8479a5262c1db61dbb03558632ebebac1a6983
ms.sourcegitcommit: 1d56a3ff255f1f72c6315a0588422842dbcbe502
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/06/2021
ms.locfileid: "129638216"
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
| HCX サイトのペアリング数 | 25 (任意のエディション) |
| Azure VMware Solution ExpressRoute のリンクされるプライベート クラウド最大数 | 4<br />実際にリンクされるプライベート クラウドの最大数は、使用される仮想ネットワーク ゲートウェイによって決まります。  詳細については、「[ExpressRoute の仮想ネットワーク ゲートウェイについて](../../expressroute/expressroute-about-virtual-network-gateways.md)」を参照してください | 
| Azure VMware Solution ExpressRoute のポート速度 | 10 Gbps<br />実際の帯域幅は、使用される仮想ネットワーク ゲートウェイによって決まります。 詳細については、「[ExpressRoute の仮想ネットワーク ゲートウェイについて](../../expressroute/expressroute-about-virtual-network-gateways.md)」を参照してください | 
| vWAN を介して公開されるパブリック IP 数 | 100 |
| vSAN の容量制限 | 使用可能な合計の 75% (SLA のために 25% を使用可能に保つ)  |

VMware 固有のその他の制限については、[VMware Configuration Maximums ツール](https://configmax.vmware.com/)を使用してください。
