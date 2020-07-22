---
title: シナリオ:VNet のカスタム分離
titleSuffix: Azure Virtual WAN
description: ルーティングのシナリオ - 選択された VNet が相互に通信できないようにする
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 06/29/2020
ms.author: cherylmc
ms.openlocfilehash: 3719956df0dce62ee69d8e306ff2cad27197616d
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/02/2020
ms.locfileid: "85567661"
---
# <a name="scenario-custom-isolation-for-vnets"></a>シナリオ:VNet のカスタム分離

Virtual WAN の仮想ハブ ルーティングを使用する場合、多くのシナリオを利用できます。 VNet のカスタム分離シナリオでは、特定の VNet のセットが他の特定の VNet のセットに到達できないようにすることが目標です。 ただし、すべてのブランチ (VPN/ER/User VPN) に接続するには、VNet が必要です。

このシナリオでは、VPN、ExpressRoute、およびユーザー VPN 接続 (総称してブランチと呼ばれます) が同じルート テーブル (既定のルート テーブル) に関連付けられています。 すべての VPN 接続、ExpressRoute 接続、ユーザー VPN 接続は、同じルート テーブルのセットにルートを伝達します。 仮想ハブ ルーティングの詳細については、「[仮想ハブのルーティングについて](about-virtual-hub-routing.md)」を参照してください。

## <a name="scenario-workflow"></a><a name="architecture"></a>シナリオのワークフロー

**図 1** には、青と赤の VNet 接続があります。

* 青の接続された Vnet は互いに通信できるだけでなく、すべてのブランチ (VPN/ER/P2S) 接続にも対応できます。
* 赤の Vnet は互いに通信できるだけでなく、すべてのブランチ (VPN/ER/P2S) 接続にも対応できます。

ルーティングを設定するときは、次の手順を考慮してください。

1. Azure portal に 2 つのカスタム ルート テーブルを作成し、**RT_BLUE** および **RT_RED** を作成します。
2. ルート テーブル **RT_BLUE** で次のようにします。
   * **Association**: すべての青の Vnet を選択
   * **Propagation**:ブランチについては、ブランチのオプションを選択します。ブランチ (VPN/ER/P2S) 接続を暗黙的に指定することで、ルートはこのルート テーブルに伝達されます。
3. 赤の Vnet とブランチ (VPN/ER/P2S) の **RT_RED** ルート テーブルについても同じ手順を繰り返します。

これで、ルーティング構成が次の図に示すように変更されます

**図 1**

:::image type="content" source="./media/routing-scenarios/custom-isolation/custom.png" alt-text="図 1":::

## <a name="next-steps"></a>次のステップ

* Virtual WAN の詳細については、[FAQ](virtual-wan-faq.md) を参照してください。
* 仮想ハブ ルーティングの詳細については、「[仮想ハブのルーティングについて](about-virtual-hub-routing.md)」を参照してください。
