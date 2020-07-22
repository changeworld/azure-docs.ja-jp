---
title: シナリオ:カスタム設定を使用して NVA 経由でトラフィックをルーティングする
titleSuffix: Azure Virtual WAN
description: このシナリオでは、NVA 経由でトラフィックをルーティングし、インターネットへのトラフィックには別の NVA を使用することができます。
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 06/29/2020
ms.author: cherylmc
ms.openlocfilehash: 3fcad59bcfb75b8de6af14eae7e0bcb4962b17eb
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/02/2020
ms.locfileid: "85567908"
---
# <a name="scenario-route-traffic-through-nvas---custom-preview"></a>シナリオ:NVA 経由でトラフィックをルーティングする - カスタム (プレビュー)

Virtual WAN の仮想ハブ ルーティングを使用する場合、多くのシナリオを利用できます。 この NVA (ネットワーク仮想アプライアンス) シナリオでは、VNet <-> ブランチに対して NVA 経由でトラフィックをルーティングし、インターネットへのトラフィックに対して別の NVA を使用することが目標です。 仮想ハブ ルーティングの詳細については、「[仮想ハブのルーティングについて](about-virtual-hub-routing.md)」を参照してください。

## <a name="scenario-architecture"></a><a name="architecture"></a>シナリオのアーキテクチャ

**図 1** には、1 つのハブ (**Hub 1**) があります。

* **Hub 1** は、NVA VNet の **VNET 4** および **VNET 5** に直接接続されています。

* VNET 1、2、3、およびブランチ (VPN/ER/P2S) 間のトラフィックは、**VNET 4 NVA** 10.4.0.5 経由で送信されることが想定されています。

* VNET 1、2、3 からインターネットへのトラフィックは、すべて **VNET 5 NVA** 10.5.0.5 経由で送信されることが想定されています。

**図 1**

:::image type="content" source="./media/routing-scenarios/nva-custom/figure-1.png" alt-text="図 1":::

## <a name="scenario-workflow"></a><a name="workflow"></a>シナリオのワークフロー

NVA 経由のルーティングを設定するには、次のような手順を検討します。

1. インターネットへのトラフィックが VNET5 経由で送信されるようにするために、VNET 1、2、3 を VNet ピアリング経由で VNET 5 に直接接続する必要があります。 また、0.0.0.0/0 と次ホップ 10.5.0.5 に対して VNet の UDR を設定する必要もあります。 現在、Virtual WAN では、0.0.0.0/0 に対して仮想ハブで次ホップの NVA を設定することは許可されていません。

1. Azure portal で仮想ハブに移動し、すべての VNet およびブランチ接続からの伝達を通じてルートを学習するカスタム ルート テーブル **RT_Shared** を作成します。 **図 2** では、空のカスタム ルート テーブル **RT_Shared** として表されています。

   * **[ルート]:** 静的ルートを追加する必要はありません。

   * **[関連付け]:** VNET 4 と 5 を選択します。VNET 4 と 5 の接続がルート テーブル **RT_Shared** に関連付けられるという意味です。

   * **[伝達]:** すべてのブランチと VNet の接続からこのルート テーブルに対して動的にルートが伝達されるようにする必要があるため、ブランチとすべての VNet を選択します。

1. VNET 1、2、3 からブランチにトラフィックを転送するためのカスタム ルート テーブル **RT_V2B** を作成します。

   * **[ルート]:** ブランチ (VPN/ER/P2S) に対する集約静的ルート エントリ (**図 2** の 10.2.0.0/16) を追加し、次ホップを VNET 4 接続として指定します。 また、ブランチのプレフィックスに対して VNET 4 の接続で静的ルートを構成し、次ホップが VNET 4 の NVA の特定の IP になるように指定する必要もあります。

   * **[関連付け]:** VNET 1、2、3 をすべて選択します。 これは、VNet 接続 1、2、および 3 がこのルート テーブルに関連付けられ、このルート テーブルで (静的および伝達によって動的) ルートを学習できるようになるという意味です。

   * **[伝達]:** 接続からルート テーブルにルートが伝達されます。 VNET 1、2、3 を選択すると、VNET 1、2、3 からこのルート テーブルにルートを伝達できるようになります。 ブランチ接続から RT_V2B にルートを伝達する必要はありません。ブランチ VNet のトラフィックは VNET4 の NVA 経由で送信されるためです。
  
1. 既定のルート テーブル **DefaultRouteTable** を編集します。

   すべての VPN、ExpressRoute、およびユーザー VPN 接続は、既定のルート テーブルに関連付けられます。 すべての VPN、ExpressRoute、およびユーザー VPN 接続からは、同じルート テーブルのセットにルートが伝達されます。

   * **[ルート]:** VNET 1、2、3 に対する集約静的ルート エントリ (**図 2** の 10.1.0.0/16) を追加し、次ホップを VNET 4 接続として指定します。 また、VNET 1、2、3 の集約プレフィックスに対して VNET 4 の接続で静的ルートを構成し、次ホップが VNET 4 の NVA の特定の IP になるように指定する必要もあります。

   * **[関連付け]:** ブランチ (VPN/ER/P2S) のオプションが選択されていることを確認し、オンプレミス ブランチ接続が *defaultroutetable* に関連付けられるようにします。

   * **[Propagation from]\(伝達元\):** ブランチ (VPN/ER/P2S) のオプションが選択されていることを確認し、オンプレミス接続から *defaultroutetable* にルートが伝達されるようにします。

**図 2**

:::image type="content" source="./media/routing-scenarios/nva-custom/figure-2.png" alt-text="図 2":::

## <a name="next-steps"></a>次のステップ

* Virtual WAN の詳細については、[FAQ](virtual-wan-faq.md) を参照してください。
* 仮想ハブ ルーティングの詳細については、「[仮想ハブのルーティングについて](about-virtual-hub-routing.md)」をご覧ください。
