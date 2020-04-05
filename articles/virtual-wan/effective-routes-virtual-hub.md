---
title: 仮想ハブの有効なルートを表示する:Azure Virtual WAN | Microsoft Docs
description: Azure Virtual WAN で仮想ハブの有効なルートを表示する
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 10/18/2019
ms.author: cherylmc
ms.openlocfilehash: 1173da81736661048d1e4e12d9919bc2aadf73ee
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/27/2020
ms.locfileid: "73511212"
---
# <a name="view-effective-routes-of-a-virtual-hub"></a>仮想ハブの有効なルートを表示する

Azure portal の Virtual WAN ハブのすべてのルートを表示できます。 ルートを表示するには、仮想ハブに移動し、 **[ルーティング] -> [有効なルートの表示]** を選択します。

## <a name="understanding-routes"></a><a name="understand"></a>ルートについて

次の例は、Virtual WAN のルーティングがどのように表示されるかを理解するのに役立ちます。

この例では、3 つのハブを持つ Virtual WAN を使用します。 最初のハブは米国東部リージョン、2 番目のハブは西ヨーロッパ リージョン、3 番目のハブは米国西部リージョンにあります。 Virtual WAN では、すべてのハブが相互接続されます。 この例では、米国東部と西ヨーロッパのハブが、オンプレミスのブランチ (スポーク) と Azure 仮想ネットワーク (スポーク) に接続されていることを前提としています。

ネットワーク仮想アプライアンス (10.4.0.6) を備えた Azure VNet スポーク (10.4.0.0/16) は、さらに VNet (10.5.0.0/16) とピアリングされます。 ハブ ルート テーブルの詳細については、この記事の後半の[追加情報](#abouthubroute)を参照してください。

この例では、西ヨーロッパのブランチ 1 が米国東部ハブと西ヨーロッパ ハブに接続されていることも想定しています。 米国東部のブランチ 2 は、ExpressRoute 回線によって米国東部ハブと接続されています。

![ダイアグラム](./media/effective-routes-virtual-hub/diagram.png)

## <a name="view-effective-routes"></a><a name="view"></a>有効なルートの表示

ポータルで [有効なルートの表示] を選択すると、米国東部ハブの[ハブ ルート テーブル](#routetable)に表示される出力が生成されます。

これを整理すると、最初の行は VPN の*次ホップの種類*接続 ("次ホップ" VPN Gateway Instance0 IP 10.1.0.6、Instance1 IP 10.1.0.7) により、米国東部ハブが 10.20.1.0/24 (ブランチ 1) のルートを学習したことを意味します。 *ルートの発信元*はリソース ID を指します。 *AS パス*はブランチ 1 の AS パスを示します。

### <a name="hub-route-table"></a><a name="routetable"></a>ハブ ルート テーブル

AS パスを表示するには、テーブルの下部にあるスクロールバーを使用します。

| **プレフィックス** |  **次ホップの種類** | **次ホップ** |  **ルートの発信元** |**AS パス** |
| ---        | ---                | ---          | ---               | ---         |
| 10.20.1.0/24|VPN |10.1.0.6, 10.1.0.7| /subscriptions/`<sub>`/resourceGroups/`<rg>`/providers/Microsoft.Network/vpnGateways/343a19aa6ac74e4d81f05ccccf1536cf-eastus-gw| 20000|
|10.21.1.0/24 |ExpressRoute|10.1.0.10, 10.1.0.11|/subscriptions/`<sub>`/resourceGroups/`<rg>`/providers/Microsoft.Network/expressRouteGateways/4444a6ac74e4d85555-eastus-gw|21000|
|10.23.1.0/24| VPN |10.1.0.6, 10.1.0.7|/subscriptions/`<sub>`/resourceGroups/`<rg>`/providers/Microsoft.Network/vpnGateways/343a19aa6ac74e4d81f05ccccf1536cf-eastus-gw|23000|
|10.4.0.0/16|仮想ネットワーク接続| On-link |  |  |
|10.5.0.0/16| IP アドレス| 10.4.0.6|/subscriptions/`<sub>`/resourceGroups/`<rg>`/providers/Microsoft.Network/virtualHubs/easthub_1/routeTables/table_1| |
|0.0.0.0/0| IP アドレス| `<Azure Firewall IP>` |/subscriptions/`<sub>`/resourceGroups/`<rg>`/providers/Microsoft.Network/virtualHubs/easthub_1/routeTables/table_1| |
|10.22.1.0/16| リモート ハブ|10.8.0.6, 10.8.0.7|/subscriptions/`<sub>`/resourceGroups/`<rg>`/providers/Microsoft.Network/virtualHubs/westhub_| 4848-22000 |
|10.9.0.0/16| リモート ハブ|  On-link |/subscriptions/`<sub>`/resourceGroups/`<rg>`/providers/Microsoft.Network/virtualHubs/westhub_1| |

>[!NOTE]
> この例のトポロジで、米国東部と西ヨーロッパのハブが相互に通信していない場合、学習したルート (10.9.0.0/16) は存在しません。 ハブは、直接接続されているネットワークのみをアドバタイズします。
>

## <a name="additional-information"></a><a name="additional"></a>追加情報

### <a name="about-the-hub-route-table"></a><a name="abouthubroute"></a>ハブ ルート テーブルについて

仮想ハブのルートを作成して、そのルートを仮想ハブのルート テーブルに適用することができます。 仮想ハブのルート テーブルには、複数のルートを適用できます。 これにより、IP アドレス (通常はスポーク VNet のネットワーク仮想アプライアンス (NVA)) を使用して、送信先 VNet のルートを設定できます。 NVA の詳細については、「[仮想ハブから NVA にトラフィックをルーティングする](virtual-wan-route-table-portal.md)」を参照してください。

### <a name="about-default-route-00000"></a><a name="aboutdefaultroute"></a>既定のルート (0.0.0.0/0) について

仮想ハブは、仮想ネットワーク、サイト間 VPN、または ExpressRoute 接続に対し、学習した既定のルートを伝達することができます (対応するフラグが、その接続で "有効" になっている場合)。 このフラグは、仮想ネットワーク接続、VPN 接続、または ExpressRoute 接続を編集するときに表示されます。 ハブ VNet、ExpressRoute、および VPN 接続では、"EnableInternetSecurity" は既定で常に false です。

既定のルートの起点は Virtual WAN ハブ内にありません。 Virtual WAN ハブにファイアウォールをデプロイした結果としてハブが既定のルートを既に学習している場合、または接続されている別のサイトで強制トンネリングが有効な場合に、既定のルートは伝達されます。

## <a name="next-steps"></a>次のステップ

Virtual WAN の詳細については、[Virtual WAN の概要](virtual-wan-about.md)に関するページを参照してください。