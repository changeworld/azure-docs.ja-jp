---
title: 'クイック スタート: Azure PowerShell を使用してルート サーバーを作成および構成する'
description: このクイック スタートでは、Azure PowerShell を使用して、ルート サーバーを作成して構成する方法について学習します。
services: route-server
author: duongau
ms.author: duau
ms.date: 09/01/2021
ms.topic: quickstart
ms.service: route-server
ms.custom: devx-track-azurepowershell, mode-api
ms.openlocfilehash: a9467e2c876cdb324a85c9be3f42cd3867a24709
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/02/2021
ms.locfileid: "131023597"
---
# <a name="quickstart-create-and-configure-route-server-using-azure-powershell"></a>クイック スタート: Azure PowerShell を使用してルート サーバーを作成および構成する

この記事は、Azure PowerShell を使用して、仮想ネットワーク内のネットワーク仮想アプライアンス (NVA) とピアリングするように Azure Route Server を構成するのに役立ちます。 Route Server では、NVA からルートを学習し、仮想ネットワーク内の仮想マシン上にそれらをプログラムします。 また、Azure Route Server は NVA に仮想ネットワーク ルートをアドバタイズします。 詳細については、[Azure Route Server](overview.md) に関するページを参照してください。

:::image type="content" source="media/quickstart-configure-route-server-portal/environment-diagram.png" alt-text="Azure PowerShell を使用した Route Server デプロイ環境の図。" border="false":::

> [!IMPORTANT]
> 9 月 1 日より前に作成した Azure Route Server があり、パブリック IP アドレスが関連付けられていない場合は、管理目的で IP アドレスを取得できるようにするため、Route Server を再作成する必要があります。

## <a name="prerequisites"></a>前提条件

* アクティブなサブスクリプションが含まれる Azure アカウント。 [無料でアカウントを作成できます](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
* 最新の Azure PowerShell モジュールがあることを確認してください。ポータルで Azure Cloud Shell を使用することもできます。
* [Azure Route Server のサービスの制限](route-server-faq.md#limitations)を確認します。
* PowerShell をローカルで実行している場合、`Connect-AzAccount` を実行して Azure との接続を作成することも必要です。

## <a name="create-resource-group-and-a-virtual-network"></a>リソース グループと仮想ネットワークを作成する

### <a name="create-a-resource-group"></a>リソース グループを作成する 

Azure Route Server を作成する前に、Route Server をホストするリソース グループを作成する必要があります。 [New-AzResourceGroup](/powershell/module/az.Resources/New-azResourceGroup) を使用して Azure リソース グループを作成します。 次の例では、**myRouteServerRG** という名前のリソース グループを **WestUS** の場所に作成します。

```azurepowershell-interactive
$rg = @{
    Name = 'myRouteServerRG'
    Location = 'WestUS'
}
New-AzResourceGroup @rg
```

### <a name="create-a-virtual-network"></a>仮想ネットワークの作成

[New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork) を使用して仮想ネットワークを作成します。 この例では、**myVirtualNetwork** という名前の既定の仮想ネットワークを **WestUS** の場所に作成します。仮想ネットワークが既にある場合は、次のセクションに進むことができます。

```azurepowershell-interactive
$vnet = @{
    Name = 'myVirtualNetwork'
    ResourceGroupName = 'myRouteServerRG'
    Location = 'WestUS'
    AddressPrefix = '10.0.0.0/16'    
}
$virtualNetwork = New-AzVirtualNetwork @vnet
```

### <a name="add-a-dedicated-subnet"></a>専用サブネットを追加する

Azure Route Server には、*RouteServerSubnet* という名前の専用サブネットが必要です。 サブネットのサイズは、少なくとも /27 または短いプレフィックス (/26 や /25 など) である必要があります。そうでない場合は、Route Server をデプロイするときにエラー メッセージが表示されます。 [Add-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/add-azvirtualnetworksubnetconfig) を使用して、**RouteServerSubnet** という名前のサブネット構成を作成します。

```azurepowershell-interactive
$subnet = @{
    Name = 'RouteServerSubnet'
    VirtualNetwork = $virtualNetwork
    AddressPrefix = '10.0.0.0/24'
}
$subnetConfig = Add-AzVirtualNetworkSubnetConfig @subnet

$virtualnetwork | Set-AzVirtualNetwork
```

## <a name="create-the-route-server"></a>ルート サーバーを作成する

1. Route Server の構成が管理されるバックエンド サービスへの接続を確保するために、パブリック IP アドレスの割り当てが必要です。 [New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress) を使用して、**RouteServerIP** という名前の標準パブリック IP を作成します。

    ```azurepowershell-interactive
    $ip = @{
        Name = 'myRouteServerIP'
        ResourceGroupName = 'myRouteServerRG'
        Location = 'WestUS'
        AllocationMethod = 'Static'
        IpAddressVersion = 'Ipv4'
        Sku = 'Standard'
    }
    $publicIp = New-AzPublicIpAddress @ip
    ```
    
2. [New-AzRouteServer](/powershell/module/az.network/new-azrouteserver) を使用して Azure Route Server を作成します。 この例では、**myRouteServer** という名前の Azure Route Server を **WestUS** の場所に作成します。 *HostedSubnet* は、前のセクションで取得した RouteServerSubnet のリソース ID です。

    ```azurepowershell-interactive
    $rs = @{
        RouteServerName = 'myRouteServer'
        ResourceGroupName = 'myRouteServerRG'
        Location = 'WestUS'
        HostedSubnet = $subnetConfig.Id
        PublicIP = $publicIp
    }
    New-AzRouteServer @rs 
    ```

## <a name="create-bgp-peering-with-an-nva"></a>NVA との BGP ピアリングを作成する

Route Server から NVA への BGP ピアリングを確立するには、[Add-AzRouteServerPeer](/powershell/module/az.network/add-azrouteserverpeer) を使用します。

"your_nva_ip" は、NVA に割り当てられた仮想ネットワーク IP です。 "your_nva_asn" は、NVA に構成された自律システム番号 (ASN) です。 ASN は、65515 から 65520 の範囲に含まれているもの以外の 16 ビットの任意の数値にすることができます。 この ASN の範囲は Microsoft によって予約されています。

```azurepowershell-interactive
$peer = @{
    PeerName = 'myNVA"
    PeerIp = '192.168.0.1'
    PeerAsn = '65501'
    RouteServerName = 'myRouteServer'
    ResourceGroupName = myRouteServerRG'
}
Add-AzRouteServerPeer @peer
```

冗長性を確保するために、異なる NVA、または同じ NVA の別のインスタンスとのピアリングを設定するには、異なる *PeerName*、*PeerIp*、および *PeerAsn* を使って上記と同じコマンドを使用します。

## <a name="complete-the-configuration-on-the-nva"></a>NVA の構成を完了する

NVA の構成を完了して BGP セッションを有効にするには、Azure Route Server の IP と ASN が必要です。 この情報は、[Get-AzRouteServer](/powershell/module/az.network/get-azrouteserver) を使用して取得できます。

```azurepowershell-interactive
$routeserver = @{
    RouteServerName = 'myRouteServer'
    ResourceGroupName = 'myRouteServerRG'
} 
Get-AzRouteServer @routeserver
```

出力は次のようになります。

``` 
RouteServerAsn : 65515
RouteServerIps : {10.5.10.4, 10.5.10.5}
```

## <a name="configure-route-exchange"></a><a name = "route-exchange"></a>ルート交換を構成する

同じ仮想ネットワーク内に ExpressRoute と Azure VPN ゲートウェイがあり、それらでルートを交換する場合は、Azure Route Server でルート交換を有効にできます。

1. Azure Route Server とゲートウェイ間のルート交換を有効にするには、 *-AllowBranchToBranchTraffic* フラグを指定して [Update-AzRouteServer](/powershell/module/az.network/update-azrouteserver) を使用します。

```azurepowershell-interactive
$routeserver = @{
    RouteServerName = 'myRouteServer'
    ResourceGroupName = 'myRouteServerRG'
    AllowBranchToBranchTraffic
}  
Update-AzRouteServer @routeserver 
```

2. Azure Route Server とゲートウェイ間のルート交換を無効にするには、 *-AllowBranchToBranchTraffic* フラグを指定せずに [Update-AzRouteServer](/powershell/module/az.network/update-azrouteserver) を使用します。

```azurepowershell-interactive
$routeserver = @{
    RouteServerName = 'myRouteServer'
    ResourceGroupName = 'myRouteServerRG'
}  
Update-AzRouteServer @routeserver 
```

## <a name="troubleshooting"></a>トラブルシューティング

Azure Route Server によってアドバタイズされたルートを表示するには、[Get-AzRouteServerPeerAdvertisedRoute](/powershell/module/az.network/get-azrouteserverpeeradvertisedroute) を使用します。

```azurepowershell-interactive
$remotepeer = @{
    RouteServerName = 'myRouteServer'
    ResourceGroupName = 'myRouteServerRG'
    PeerName = 'myNVA'
}
Get-AzRouteServerPeerAdvertisedRoute @routeserver
```

Azure Route Server によって学習されたルートを表示するには、[Get-AzRouteServerPeerLearnedRoute](/powershell/module/az.network/get-azrouteserverpeerlearnedroute) を使用します。

```azurepowershell-interactive
$routeserver = @{
    RouteServerName = 'myRouteServer'
    ResourceGroupName = 'myRouteServerRG'
    AllowBranchToBranchTraffic
}  
Get-AzRouteServerPeerLearnedRoute @routeserver
```
## <a name="clean-up-resources"></a>リソースをクリーンアップする

Azure Route Server が不要になった場合は、最初のコマンドを使用して BGP ピアリングを削除してから、2 番目のコマンドを使用して Route Server を削除します。 

1. [Remove-AzRouteServerPeer](/powershell/module/az.network/remove-azrouteserverpeer) を使用して、Azure Route Server と NVA の間の BGP ピアリングを削除します。

```azurepowershell-interactive
$peer = @{
    PeerName = 'myNVA'
    RouteServerName = 'myRouteServer'
    ResourceGroupName = 'myRouteServerRG'
} 
Remove-AzRouteServerPeer @peer
```

2. [Remove-AzRouteServer](/powershell/module/az.network/remove-azrouteserver) を使用して Azure Route Server を削除します。

```azurepowershell-interactive 
$routeserver = @{
    RouteServerName = 'myRouteServer'
    ResourceGroupName = 'myRouteServerRG'
} 
Remove-AzRouteServer @routeserver
```

## <a name="next-steps"></a>次のステップ

Azure Route Server を作成した後、Azure Route Server と ExpressRoute および VPN Gateway との連携方法について引き続き学習するには、次を参照してください。 

> [!div class="nextstepaction"]
> [Azure ExpressRoute と Azure VPN のサポート](expressroute-vpn-support.md)
