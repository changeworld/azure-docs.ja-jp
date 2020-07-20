---
title: シナリオ:VNet の分離
titleSuffix: Azure Virtual WAN
description: ルーティングのシナリオ - VNet の分離
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 06/29/2020
ms.author: cherylmc
ms.openlocfilehash: f43f17a0f3742831920836e448de3ef757f2dfa6
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/02/2020
ms.locfileid: "85567655"
---
# <a name="scenario-isolating-vnets"></a>シナリオ:VNet の分離

Virtual WAN の仮想ハブ ルーティングを使用する場合、多くのシナリオを利用できます。 このシナリオでは、VNet が他方に到達できないようにすることを目標としています。 これは、VNet の分離と呼ばれます。 VNet 内のワークロードは分離されたままであり、Any-to-Any シナリオと同様に、他の VNet と通信することはできません。 ただし、すべてのブランチ (VPN、ER、User VPN) に接続するには、VNet が必要です。 このシナリオでは、すべての VPN 接続、ExpressRoute 接続、ユーザー VPN 接続は、同じ 1 つのルート テーブルに関連付けられます。 すべての VPN 接続、ExpressRoute 接続、ユーザー VPN 接続は、同じルート テーブルのセットにルートを伝達します。 仮想ハブ ルーティングの詳細については、「[仮想ハブ ルーティングについて](about-virtual-hub-routing.md)」を参照してください。

## <a name="scenario-workflow"></a><a name="workflow"></a>シナリオのワークフロー

このシナリオを構成するには、次の手順を考慮してください。

1. カスタム ルート テーブルを作成します。 この例では、ルート テーブルは **RT_VNet** です。 ルート テーブルを作成するには、「[仮想ハブ ルーティングを構成する方法](how-to-virtual-hub-routing.md)」を参照してください。 ルート テーブルの詳細については、「[仮想ハブ ルーティングについて](about-virtual-hub-routing.md)」を参照してください。
2. **RT_VNet** ルート テーブルを作成する場合は、次の設定を構成します。

   * **Association**: 分離する VNet を選択します。
   * **伝達**: ブランチのオプションを選択します。ブランチ (VPN/ER/P2S) 接続を暗黙的に指定することで、ルートはこのルート テーブルに伝達されます。

:::image type="content" source="./media/routing-scenarios/isolated/isolated-vnets.png" alt-text="分離された VNet":::

## <a name="next-steps"></a>次のステップ

* Virtual WAN の詳細については、[FAQ](virtual-wan-faq.md) を参照してください。
* 仮想ハブ ルーティングの詳細については、「[仮想ハブ ルーティングについて](about-virtual-hub-routing.md)」を参照してください。
