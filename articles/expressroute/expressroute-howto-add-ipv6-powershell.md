---
title: 'Azure ExpressRoute: Azure PowerShell を使用して IPv6 サポートを追加する'
description: Azure PowerShell を使用して Azure デプロイに接続するための IPv6 サポートを追加する方法について説明します。
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: how-to
ms.date: 03/02/2021
ms.author: duau
ms.openlocfilehash: 04248f1cf0e0d9ecb659ca3271000a0a28a6a97b
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/22/2021
ms.locfileid: "130242314"
---
# <a name="add-ipv6-support-for-private-peering-using-azure-powershell"></a>Azure PowerShell で、プライベート ピアリングの IPv6 サポートを追加する

この記事では、Azure PowerShell を使用して Azure 内のリソースに ExpressRoute 経由で接続するための IPv6 サポートを追加する方法について説明します。

## <a name="working-with-azure-powershell"></a>Azure PowerShell を使用する

[!INCLUDE [updated-for-az](../../includes/hybrid-az-ps.md)]

[!INCLUDE [expressroute-cloudshell](../../includes/expressroute-cloudshell-powershell-about.md)]

## <a name="add-ipv6-private-peering-to-your-expressroute-circuit"></a>ExpressRoute 回線に IPv6 プライベート ピアリングを追加する

1. [ExpressRoute 回線を作成](./expressroute-howto-circuit-arm.md)するか、既存の回線を使用します。 **Get-AzExpressRouteCircuit** コマンドを実行して、回線を取得します。

    ```azurepowershell-interactive
    $ckt = Get-AzExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"
    ```

2. **AzExpressRouteCircuitPeeringConfig** を実行して、回線のプライベート ピアリング構成を取得します。

    ```azurepowershell-interactive
    Get-AzExpressRouteCircuitPeeringConfig -Name "AzurePrivatePeering" -ExpressRouteCircuit $ckt
    ```

3. 既存の IPv4 プライベート ピアリング構成に IPv6 プライベート ピアリングを追加します。 プライマリ リンクとセカンダリ リンク用に所有している /126 IPv6 サブネットのペアを指定します。 これらの各サブネットから、ユーザーは 1 番目に使用可能な IP アドレスを自分のルーターに割り当て、Microsoft は 2 番目に使用可能な IP アドレスをそのルーターに割り当てます。

    > [!Note]
    > ピアの ASN と VlanId は、IPv4 プライベート ピアリング構成内のものと一致している必要があります。

    ```azurepowershell-interactive
    Set-AzExpressRouteCircuitPeeringConfig -Name "AzurePrivatePeering" -ExpressRouteCircuit $ckt -PeeringType AzurePrivatePeering -PeerASN 100 -PrimaryPeerAddressPrefix "3FFE:FFFF:0:CD30::/126" -SecondaryPeerAddressPrefix "3FFE:FFFF:0:CD30::4/126" -VlanId 200 -PeerAddressType IPv6

    Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt
    ```

4. 構成が正常に保存されたら、**AzExpressRouteCircuit** コマンドを実行して回線を再度取得します。 応答は、次の例のようになります。

    ```azurepowershell
    Name                             : ExpressRouteARMCircuit
    ResourceGroupName                : ExpressRouteResourceGroup
    Location                         : eastus
    Id                               : /subscriptions/***************************/resourceGroups/ExpressRouteResourceGroup/providers/Microsoft.Network/expressRouteCircuits/ExpressRouteARMCircuit
    Etag                             : W/"################################"
    ProvisioningState                : Succeeded
    Sku                              : {
                                         "Name": "Standard_MeteredData",
                                         "Tier": "Standard",
                                         "Family": "MeteredData"
                                       }
    CircuitProvisioningState         : Enabled
    ServiceProviderProvisioningState : Provisioned
    ServiceProviderNotes             :
    ServiceProviderProperties        : {
                                         "ServiceProviderName": "Equinix",
                                         "PeeringLocation": "Washington DC",
                                         "BandwidthInMbps": 50
                                       }
    ExpressRoutePort                 : null
    BandwidthInGbps                  :
    Stag                             : 29
    ServiceKey                       : **************************************
    Peerings                         : [
                                         {
                                           "Name": "AzurePrivatePeering",
                                           "Etag": "W/\"facc8972-995c-4861-a18d-9a82aaa7167e\"",
                                           "Id": "/subscriptions/***************************/resourceGroups/ExpressRouteResourceGroup/providers/Microsoft.Network/expressRouteCircuits/ExpressRouteARMCircuit/peerings/AzurePrivatePeering",
                                           "PeeringType": "AzurePrivatePeering",
                                           "State": "Enabled",
                                           "AzureASN": 12076,
                                           "PeerASN": 100,
                                           "PrimaryPeerAddressPrefix": "192.168.15.16/30",
                                           "SecondaryPeerAddressPrefix": "192.168.15.20/30",
                                           "PrimaryAzurePort": "",
                                           "SecondaryAzurePort": "",
                                           "VlanId": 200,
                                           "ProvisioningState": "Succeeded",
                                           "GatewayManagerEtag": "",
                                           "LastModifiedBy": "Customer",
                                           "Ipv6PeeringConfig": {
                                             "State": "Enabled",
                                             "PrimaryPeerAddressPrefix": "3FFE:FFFF:0:CD30::/126",
                                             "SecondaryPeerAddressPrefix": "3FFE:FFFF:0:CD30::4/126"
                                           },
                                           "Connections": [],
                                           "PeeredConnections": []
                                         },
                                       ]
    Authorizations                   : []
    AllowClassicOperations           : False
    GatewayManagerEtag               :
    ```

## <a name="update-your-connection-to-an-existing-virtual-network"></a>既存の仮想ネットワークへの接続を更新する

IPv6 プライベート ピアリングを使用する Azure リソースの既存環境がある場合は、下の手順に従います。

1. ExpressRoute 回線の接続先の仮想ネットワークを取得します。

    ```azurepowershell-interactive
    $vnet = Get-AzVirtualNetwork -Name "VirtualNetwork" -ResourceGroupName "ExpressRouteResourceGroup"
    ```

2. 仮想ネットワークに IPv6 アドレス空間を追加します。

    ```azurepowershell-interactive
    $vnet.AddressSpace.AddressPrefixes.add("ace:daa:daaa:deaa::/64")
    Set-AzVirtualNetwork -VirtualNetwork $vnet
    ```

3. ゲートウェイ サブネットに IPv6 アドレス空間を追加します。 ゲートウェイ IPv6 サブネットは /64 以上である必要があります。

    ```azurepowershell-interactive
    Set-AzVirtualNetworkSubnetConfig -Name GatewaySubnet -VirtualNetwork $vnet -AddressPrefix "10.0.0.0/26", "ace:daa:daaa:deaa::/64"
    Set-AzVirtualNetwork -VirtualNetwork $vnet
    ```

4. 既存のゾーン冗長ゲートウェイがある場合は、次を実行して IPv6 接続を有効にします (変更が反映されるまでに最大 1 時間かかることがあります)。 それ以外の場合は、SKU を使用して[仮想ネットワーク ゲートウェイを作成します](./expressroute-howto-add-gateway-resource-manager.md)。 FastPath を使用する場合は、UltraPerformance または ErGw3AZ を使用します (これは、ExpressRoute Direct を使用する回線でのみ利用できます)。

    ```azurepowershell-interactive
    $gw = Get-AzVirtualNetworkGateway -Name "GatewayName" -ResourceGroupName "ExpressRouteResourceGroup"
    Set-AzVirtualNetworkGateway -VirtualNetworkGateway $gw
    ```
>[!NOTE]
> 既存のゲートウェイがゾーン冗長ではない場合 (つまり、Standard、High Performance、Ultra Performance SKU である)、任意の SKU と、Standard、Static パブリック IP アドレスを使用してゲートウェイを削除して[再作成](./expressroute-howto-add-gateway-resource-manager.md#add-a-gateway)する必要があります。

## <a name="create-a-connection-to-a-new-virtual-network"></a>新しい仮想ネットワークへの接続を作成する

IPv6 プライベート ピアリングを使用して新しい Azure リソース セットに接続する場合は、下の手順に従います。

1. IPv4 と IPv6 の両方のアドレス空間を使用して、デュアルスタック仮想ネットワークを作成します。 詳細については、「[仮想ネットワークの作成](../virtual-network/quick-create-portal.md#create-a-virtual-network)」をご覧ください。

2. [デュアルスタック ゲートウェイ サブネットを作成します](./expressroute-howto-add-gateway-resource-manager.md#add-a-gateway)。

3. SKU を使用して[仮想ネットワーク ゲートウェイを作成します](./expressroute-howto-add-gateway-resource-manager.md#add-a-gateway)。 FastPath を使用する場合は、UltraPerformance または ErGw3AZ を使用します (これは、ExpressRoute Direct を使用する回線でのみ利用できます)。

4. [仮想ネットワークを ExpressRoute 回線にリンクします](./expressroute-howto-linkvnet-arm.md)。

## <a name="limitations"></a>制限事項
IPv6 サポートは、パブリック Azure リージョン内のデプロイへの接続に使用できますが、次のユース ケースはサポートされていません。

* ゾーン冗長ではない "*既存の*" ExpressRoute ゲートウェイへの接続。 標準の静的 IP アドレスを使用して "*新しく*" 作成された任意の SKU の ExpressRoute ゲートウェイは (ゾーン冗長かどうかを問わず)、デュアルスタック ExpressRoute 接続に使用できることに注意してください
* ExpressRoute 回線間の Global Reach 接続
* ExpressRoute と仮想 WAN の使用
* ExpressRoute Direct 以外の回線の FastPath
* ピアリングの場所 (ドバイ) に回線がある FastPath
* VPN Gateway との共存

## <a name="next-steps"></a>次のステップ

ExpressRoute に関する問題のトラブルシューティングを行うには、次の記事を参照してください。

* [ExpressRoute 接続の確認](expressroute-troubleshooting-expressroute-overview.md)
* [ネットワーク パフォーマンスのトラブルシューティング](expressroute-troubleshooting-network-performance.md)
