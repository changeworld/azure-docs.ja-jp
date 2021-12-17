---
title: 'Azure ExpressRoute: Azure portal を使用して IPv6 サポートを追加する'
description: Azure portal を使用して Azure デプロイに接続するための IPv6 サポートを追加する方法について説明します。
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: how-to
ms.date: 03/09/2021
ms.author: duau
ms.openlocfilehash: fb7f606b0427a5b3f5293fd82bfd9a0544bc945f
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/22/2021
ms.locfileid: "130226340"
---
# <a name="add-ipv6-support-for-private-peering-using-the-azure-portal"></a>Azure portal を使用してプライベート ピアリングに対する IPv6 サポートを追加する

この記事では、Azure portal を使用して Azure 内のリソースに ExpressRoute 経由で接続するための IPv6 サポートを追加する方法について説明します。

>[!NOTE]
> ポータル エクスペリエンスのいくつかの側面は引き続き実装されています。 そのため、このドキュメントで提供されている手順の正確な順序に従って、ポータル経由で IPv6 サポートを正常に追加してください。 具体的には、ポータルで新しい仮想ネットワーク ゲートウェイを作成する "*前に*"、必ず仮想ネットワークとサブネットを作成するか、または既存の仮想ネットワークと GatewaySubnet に IPv6 アドレス空間を追加するようにしてください。

## <a name="sign-in-to-the-azure-portal"></a>Azure portal にサインインする

ブラウザーから [Azure portal](https://portal.azure.com) に移動し、Azure アカウントでサインインします。

## <a name="add-ipv6-private-peering-to-your-expressroute-circuit"></a>ExpressRoute 回線に IPv6 プライベート ピアリングを追加する

1. [ExpressRoute 回線を作成する](expressroute-howto-circuit-portal-resource-manager.md)か、変更する既存の回線に移動します。

    :::image type="content" source="./media/expressroute-howto-add-ipv6-portal/navigate-to-circuit.png" alt-text="ExpressRoute 回線の一覧のスクリーンショット。":::

1. **[Azure プライベート]** のピアリング構成を選択します。

    :::image type="content" source="./media/expressroute-howto-add-ipv6-portal/navigate-to-peering.png" alt-text="Express Route の概要ページのスクリーンショット。":::

1. **[サブネット]** に対して [両方] を選択して、既存の IPv4 プライベート ピアリング構成に IPv6 プライベート ピアリングを追加するか、[IPv6] を選択して、新しい回線で IPv6 プライベート ピアリングのみを有効にします。 プライマリ リンクとセカンダリ リンク用に所有している /126 IPv6 サブネットのペアを指定します。 これらの各サブネットから、ユーザーは 1 番目に使用可能な IP アドレスを自分のルーターに割り当て、Microsoft は 2 番目に使用可能な IP アドレスをそのルーターに割り当てます。 すべてのパラメーターを指定したら、ピアリング構成を **保存** します。

    :::image type="content" source="./media/expressroute-howto-add-ipv6-portal/add-ipv6-peering.png" alt-text="プライベート ピアリング ページでの Ipv6 の追加のスクリーンショット。":::

1. 構成が正常に受け入れられると、次の例のようなものが表示されます。

    :::image type="content" source="./media/expressroute-howto-add-ipv6-portal/view-ipv6-peering.png" alt-text="プライベート ピアリングに構成された Ipv6 のスクリーンショット。":::

## <a name="update-your-connection-to-an-existing-virtual-network"></a>既存の仮想ネットワークへの接続を更新する

IPv6 プライベート ピアリングを使用する Azure リソースの既存環境がある場合は、下の手順に従います。

1. ExpressRoute 回線が接続されている仮想ネットワークに移動します。

    :::image type="content" source="./media/expressroute-howto-add-ipv6-portal/navigate-to-vnet.png" alt-text="仮想ネットワークの一覧のスクリーンショット。":::

1. **[アドレス空間]** タブに移動し、仮想ネットワークに IPv6 アドレス空間を追加します。 アドレス空間を **保存** します。

    :::image type="content" source="./media/expressroute-howto-add-ipv6-portal/add-ipv6-space.png" alt-text="仮想ネットワークへの Ipv6 アドレス空間の追加のスクリーンショット。":::

1. **[サブネット]** タブに移動し、 **[GatewaySubnet]** を選択します。 **[Add IPv6 address space]\(IPv6 アドレス空間を追加する\)** チェックボックスをオンにし、サブネットの IPv6 アドレス空間を指定します。 ゲートウェイ IPv6 サブネットは /64 以上である必要があります。 すべてのパラメーターを指定したら、構成を **保存** します。

    :::image type="content" source="./media/expressroute-howto-add-ipv6-portal/add-ipv6-gateway-space.png" alt-text="サブネットへの Ipv6 アドレス空間の追加のスクリーンショット。":::
    
1. 既存のゾーン冗長ゲートウェイがある場合は、PowerShell で次を実行して IPv6 接続を有効にします (変更が反映されるまでに最大 1 時間かかることがあります)。 それ以外の場合は、任意の SKU と標準の静的パブリック IP アドレスを使用して、[仮想ネットワーク　ゲートウェイを作成](./expressroute-howto-add-gateway-portal-resource-manager.md)します。 FastPath を使用する場合は、UltraPerformance または ErGw3AZ を使用します (このオプションは ExpressRoute Direct を使用する回線でのみ利用できます)。

    ```azurepowershell-interactive
    $gw = Get-AzVirtualNetworkGateway -Name "GatewayName" -ResourceGroupName "ExpressRouteResourceGroup"
    Set-AzVirtualNetworkGateway -VirtualNetworkGateway $gw
    
>[!NOTE]
> If you have an existing gateway that is not zone-redundant (meaning it is Standard, High Performance, or Ultra Performance SKU), you will need to delete and [recreate the gateway](expressroute-howto-add-gateway-portal-resource-manager.md#create-the-virtual-network-gateway) using any SKU and a Standard, Static public IP address.

## Create a connection to a new virtual network

Follow the steps below if you plan to connect to a new set of Azure resources using your IPv6 Private Peering.

1. Create a dual-stack virtual network with both IPv4 and IPv6 address space. For more information, see [Create a virtual network](../virtual-network/quick-create-portal.md#create-a-virtual-network).

1. [Create the dual-stack gateway subnet](expressroute-howto-add-gateway-portal-resource-manager.md#create-the-gateway-subnet).

1. [Create the virtual network gateway](expressroute-howto-add-gateway-resource-manager.md) using any SKU and a Standard, Static public IP address. If you plan to use FastPath, use UltraPerformance or ErGw3AZ (note that this option is only available for circuits using ExpressRoute Direct). **NOTE:** Please use the PowerShell instructions for this step as the Azure portal experience is still under development.

1. [Link your virtual network to your ExpressRoute circuit](expressroute-howto-linkvnet-portal-resource-manager.md).

## Limitations
While IPv6 support is available for connections to deployments in Public Azure regions, it doesn't support the following use cases:

* Connections to *existing* ExpressRoute gateways that are not zone-redundant. Note that *newly* created ExpressRoute gateways of any SKU (both zone-redundant and not) using  a Standard, Static IP address can be used for dual-stack ExpressRoute connections
* Global Reach connections between ExpressRoute circuits
* Use of ExpressRoute with virtual WAN
* FastPath with non-ExpressRoute Direct circuits
* FastPath with circuits in the following peering locations: Dubai
* Coexistence with VPN Gateway

## Next steps

To troubleshoot ExpressRoute problems, see the following articles:

* [Verifying ExpressRoute connectivity](expressroute-troubleshooting-expressroute-overview.md)
* [Troubleshooting network performance](expressroute-troubleshooting-network-performance.md)
