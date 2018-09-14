---
title: インクルード ファイル
description: インクルード ファイル
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: include
ms.date: 06/12/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: c681e5f68299c03685dd7722f8dc671e49026f78
ms.sourcegitcommit: 1fb353cfca800e741678b200f23af6f31bd03e87
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/30/2018
ms.locfileid: "43303847"
---
#### <a name="expressroute-limits"></a>ExpressRoute の制限
各サブスクリプションの ExpressRoute リソースには、次の制限が適用されます。

| リソース | 既定の制限 |
| --- | --- |
| サブスクリプションあたりの ExpressRoute 回線数 |10 |
| サブスクリプションあたりのリージョンごとの ExpressRoute 回線数 (Azure Resource Manager) |10 |
| ExpressRoute Standard の Azure プライベート ピアリング用の最大ルート数 |4,000 |
| ExpressRoute Premium アドオンの Azure プライベート ピアリング用の最大ルート数 |10,000 |
| ExpressRoute Standard の Azure Microsoft ピアリング用の最大ルート数 |200 |
| ExpressRoute Premium アドオンの Azure Microsoft ピアリング用の最大ルート数 |200 |
| さまざまなピアリング場所で同じ仮想ネットワークにリンクされる ExpressRoute の最大数 |4 |
| ExpressRoute 回線あたりで許可される仮想ネットワークのリンク数 |次の表を参照 |

#### <a name="number-of-virtual-networks-per-expressroute-circuit"></a>ExpressRoute 回線あたりの Virtual Network 数
| **回線のサイズ** | **Standard での VNET リンク数** | **Premium アドオンでの VNET リンク数** |
| --- | --- | --- |
| 50 Mbps |10 |20 |
| 100 Mbps |10 |25 |
| 200 Mbps |10 |25 |
| 500 Mbps |10 |40 |
| 1 Gbps |10 |50 |
| 2 Gbps |10 |60 |
| 5 Gbps |10 |75 |
| 10 Gbps |10 |100 |

