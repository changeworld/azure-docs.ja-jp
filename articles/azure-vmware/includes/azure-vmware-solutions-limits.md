---
title: Azure VMware Solution の制限
description: Azure VMware Solution の制限。
ms.topic: include
ms.date: 03/04/2021
ms.openlocfilehash: 99c27694a300284cfd99b8411306c90ad4d8a12e
ms.sourcegitcommit: 24a12d4692c4a4c97f6e31a5fbda971695c4cd68
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/05/2021
ms.locfileid: "102193603"
---
<!-- Used in /azure/azure-resource-manager/management/azure-subscription-service-limits.md -->

Azure VMware Solution の上限を次の表に示します。

| **リソース** | **制限** |
| :-- | :-- |
| プライベート クラウドあたりのクラスター数 | 4 |
| クラスターごとのノードの最小数 | 3 |
| クラスターごとのノードの最大数 | 16 |
| プライベート クラウドあたりのノード数 | 64 |
| プライベート クラウドあたりの vCenter 数 | 1  |
| HCX サイトのペアリング数 | Advanced エディションの場合は 3、Enterprise エディションの場合は 10 |
| AVS ExpressRoute にリンクされる SDDC の最大数 | 4 |
| AVS ExpressRoute のポート速度 | 10 Gbps | 
| vWAN を介して公開されるパブリック IP 数 | 100 |
| vSAN の容量制限 | 使用可能な合計の 75% (SLA のために 25% を使用可能に保つ)  |

VMware 固有のその他の制限については、[VMware Configuration Maximums ツール](https://configmax.vmware.com/)を使用してください。
