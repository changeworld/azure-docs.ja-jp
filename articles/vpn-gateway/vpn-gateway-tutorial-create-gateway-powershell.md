---
title: PowerShell を使用した Azure VPN ゲートウェイの作成と管理 | Microsoft Docs
description: チュートリアル - Azure PowerShell モジュールを使用して VPN ゲートウェイを作成および管理する
services: vpn-gateway
documentationcenter: na
author: yushwang
manager: rossort
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: vpn-gateway
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: infrastructure
ms.date: 05/14/2018
ms.author: yushwang
ms.custom: mvc
ms.openlocfilehash: 0f10384e7e21d65b3a16869a10f8294b9643c74c
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/11/2018
ms.locfileid: "38705685"
---
# <a name="create-and-manage-vpn-gateway-with-the-azure-powershell-module"></a>Azure PowerShell モジュールを使用した VPN ゲートウェイの作成と管理

Azure VPN ゲートウェイは、お客様のオンプレミスと Azure 間のクロスプレミス接続を提供します。 このチュートリアルでは、VPN ゲートウェイの作成と管理など、Azure VPN ゲートウェイのデプロイの基本事項について説明します。 学習内容は次のとおりです。

> [!div class="checklist"]
> * VPN ゲートウェイの作成
> * VPN ゲートウェイのサイズ変更
> * VPN ゲートウェイのリセット

次の図は、このチュートリアルの一環として作成する仮想ネットワークと VPN ゲートウェイを示しています。

![VNet と VPN ゲートウェイ](./media/vpn-gateway-tutorial-create-gateway-powershell/vnet1-gateway.png)

### <a name="azure-cloud-shell-and-azure-powershell"></a>Azure Cloud Shell および Azure PowerShell

[!INCLUDE [working with cloudshell](../../includes/vpn-gateway-cloud-shell-powershell.md)]

PowerShell をインストールしてローカルで使用する場合、このチュートリアルでは Azure PowerShell モジュール バージョン 5.3 以降が必要になります。 バージョンを確認するには、`Get-Module -ListAvailable AzureRM` を実行します。 アップグレードする必要がある場合は、[Azure PowerShell モジュールのインストール](/powershell/azure/install-azurerm-ps)に関するページを参照してください。 PowerShell をローカルで実行している場合、`Login-AzureRmAccount` を実行して Azure との接続を作成することも必要です。 

## <a name="common-network-parameter-values"></a>一般的なネットワーク パラメーター値

環境とネットワークのセットアップに基づいて次の値を変更します。

```azurepowershell-interactive
$RG1         = "TestRG1"
$VNet1       = "VNet1"
$Location1   = "East US"
$FESubnet1   = "FrontEnd"
$BESubnet1   = "Backend"
$GwSubnet1   = "GatewaySubnet"
$VNet1Prefix = "10.1.0.0/16"
$FEPrefix1   = "10.1.0.0/24"
$BEPrefix1   = "10.1.1.0/24"
$GwPrefix1   = "10.1.255.0/27"
$VNet1ASN    = 65010
$DNS1        = "8.8.8.8"
$Gw1         = "VNet1GW"
$GwIP1       = "VNet1GWIP"
$GwIPConf1   = "gwipconf1"
```

## <a name="create-resource-group"></a>リソース グループの作成

[New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup) コマンドでリソース グループを作成します。 Azure リソース グループとは、Azure リソースのデプロイと管理に使用する論理コンテナーです。 リソース グループを最初に作成する必要があります。 次の例では、*TestRG1* という名前のリソース グループが "*米国東部*" リージョンに作成されます。

```azurepowershell-interactive
New-AzureRmResourceGroup -ResourceGroupName $RG1 -Location $Location1
```

## <a name="create-a-virtual-network"></a>仮想ネットワークの作成

Azure VPN ゲートウェイは、仮想ネットワークのクロスプレミス接続と P2S VPN サーバー機能を提供します。 VPN ゲートウェイを既存の仮想ネットワークに追加するか、新しい仮想ネットワークとゲートウェイを作成します。 次の例では、[New-AzureRmVirtualNetworkSubnetConfig](/powershell/module/azurerm.network/new-azurermvirtualnetworksubnetconfig) と [New-AzureRmVirtualNetwork](/powershell/module/azurerm.network/new-azurermvirtualnetwork) を使用して、Frontend、Backend、GatewaySubnetの 3 つのサブネットを持つ新しい仮想ネットワークを作成します。

```azurepowershell-interactive
$fesub1 = New-AzureRmVirtualNetworkSubnetConfig -Name $FESubnet1 -AddressPrefix $FEPrefix1
$besub1 = New-AzureRmVirtualNetworkSubnetConfig -Name $BESubnet1 -AddressPrefix $BEPrefix1
$gwsub1 = New-AzureRmVirtualNetworkSubnetConfig -Name $GWSubnet1 -AddressPrefix $GwPrefix1
$vnet   = New-AzureRmVirtualNetwork `
            -Name $VNet1 `
            -ResourceGroupName $RG1 `
            -Location $Location1 `
            -AddressPrefix $VNet1Prefix `
            -Subnet $fesub1,$besub1,$gwsub1
```

## <a name="request-a-public-ip-address-for-the-vpn-gateway"></a>VPN ゲートウェイのパブリック IP アドレスを要求する

Azure VPN ゲートウェイは、オンプレミスの VPN デバイスとインターネット経由で通信して IKE (インターネット キー交換) ネゴシエーションを実行し、IPsec トンネルを確立します。 次の例に示すように、[New-AzureRmPublicIpAddress](/powershell/module/azurerm.network/new-azurermpublicipaddress) と [New-AzureRmVirtualNetworkGatewayIpConfig](/powershell/module/azurerm.network/new-azurermvirtualnetworkgatewayipconfig) を使用して、パブリック IP アドレスを作成し、VPN ゲートウェイに割り当てます。

> [!IMPORTANT]
> 現時点では、ゲートウェイに使用できるのは、動的パブリック IP アドレスだけです。 静的 IP アドレスは、Azure VPN ゲートウェイではサポートされていません。

```azurepowershell-interactive
$gwpip    = New-AzureRmPublicIpAddress -Name $GwIP1 -ResourceGroupName $RG1 `
              -Location $Location1 -AllocationMethod Dynamic
$subnet   = Get-AzureRmVirtualNetworkSubnetConfig -Name 'GatewaySubnet' `
              -VirtualNetwork $vnet
$gwipconf = New-AzureRmVirtualNetworkGatewayIpConfig -Name $GwIPConf1 `
              -Subnet $subnet -PublicIpAddress $gwpip
```

## <a name="create-vpn-gateway"></a>VPN ゲートウェイを作成する

VPN ゲートウェイの作成には 45 分以上かかる場合があります。 ゲートウェイの作成が完了したら、仮想ネットワークと別の VNet 間の接続を作成できます。 または、仮想ネットワークとオンプレミスの場所の間の接続を作成します。 [New-AzureRmVirtualNetworkGateway](/powershell/module/azurerm.network/New-AzureRmVirtualNetworkGateway) コマンドレットを使用して VPN ゲートウェイを作成します。

```azurepowershell-interactive
New-AzureRmVirtualNetworkGateway -Name $Gw1 -ResourceGroupName $RG1 `
  -Location $Location1 -IpConfigurations $gwipconf -GatewayType Vpn `
  -VpnType RouteBased -GatewaySku VpnGw1
```

主なパラメーター値は次のとおりです。
* GatewayType: サイト間接続と VNet 間接続には、**Vpn** を使用します。
* VpnType: より広範な VPN デバイスやより多くのルーティング機能と対話するには、**RouteBased** を使用します。
* GatewaySku: 既定値は **VpnGw1** です。より高いスループットやより多くの接続が必要な場合は、VpnGw2 または VpnGw3 に変更してください。 詳細については、「[ゲートウェイの SKU](vpn-gateway-about-vpn-gateway-settings.md#gwsku)」を参照してください。

ゲートウェイの作成が完了したら、仮想ネットワークと別の VNet 間の接続を作成するか、仮想ネットワークとオンプレミスの場所の間の接続を作成できます。 また、クライアント コンピューターから VNet への P2S 接続を構成することもできます。

## <a name="resize-vpn-gateway"></a>VPN ゲートウェイのサイズを変更する

ゲートウェイの作成後、VPN ゲートウェイ SKU を変更できます。 ゲートウェイ SKU ごとに、サポートされる仕様 (スループット、接続数など) が異なります。次の例では、[Resize-AzureRmVirtualNetworkGateway](/powershell/module/azurerm.network/Resize-AzureRmVirtualNetworkGateway) を使用して、ゲートウェイのサイズを VpnGw1 から VpnGw2 に変更します。 詳細については、「[ゲートウェイの SKU](vpn-gateway-about-vpn-gateway-settings.md#gwsku)」を参照してください。

```azurepowershell-interactive
$gw = Get-AzureRmVirtualNetworkGateway -Name $Gw1 -ResourceGroup $RG1
Resize-AzureRmVirtualNetworkGateway -GatewaySku VpnGw2 -VirtualNetworkGateway $gateway
```

VPN ゲートウェイのサイズ変更も、約 30 から 45 分かかりますが、この操作によって、既存の接続と構成が中断されたり削除されたりすることは**ありません**。

## <a name="reset-vpn-gateway"></a>VPN ゲートウェイをリセットする

トラブルシューティングの手順の一環として、Azure VPN ゲートウェイをリセットして、VPN ゲートウェイに IPsec/IKE トンネル構成を強制的に再開させることができます。 [Reset-AzureRmVirtualNetworkGateway](/powershell/module/azurerm.network/Reset-AzureRmVirtualNetworkGateway) を使用してゲートウェイをリセットします。

```azurepowershell-interactive
$gw = Get-AzureRmVirtualNetworkGateway -Name $Gw1 -ResourceGroup $RG1
Reset-AzureRmVirtualNetworkGateway -VirtualNetworkGateway $gateway
```

詳細については、「[Reset a VPN gateway (VPN ゲートウェイのリセット)](vpn-gateway-resetgw-classic.md)」をご覧ください。

## <a name="get-the-gateway-public-ip-address"></a>ゲートウェイのパブリック IP アドレスを取得する

パブリック IP アドレスの名前がわかっている場合は、[Get-AzureRmPublicIpAddress](/powershell/module/azurerm.network/Reset-AzureRmPublicIpAddress) を使用して、ゲートウェイに割り当てられたパブリック IP アドレスを表示します。

```azurepowershell-interactive
$myGwIp = Get-AzureRmPublicIpAddress -Name $GwIP1 -ResourceGroup $RG1
$myGwIp.IpAddress
```

## <a name="delete-vpn-gateway"></a>VPN ゲートウェイを削除する

クロスプレミスおよび VNet 間の接続の完全な構成には、VPN ゲートウェイだけでなく、複数のリソースの種類が必要です。 ゲートウェイ自体を削除する前に、VPN ゲートウェイに関連付けられている接続を削除します。 ゲートウェイが削除されたら、ゲートウェイのパブリック IP アドレスを削除できます。 詳細な手順については、[VPN ゲートウェイの削除](vpn-gateway-delete-vnet-gateway-powershell.md)に関する記事をご覧ください。

ゲートウェイが protype または概念実証のデプロイの一部である場合は、[Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup) コマンドを使用して、リソース グループ、VPN ゲートウェイ、およびすべての関連リソースを削除できます。

```azurepowershell-interactive
Remove-AzureRmResourceGroup -Name $RG1
```

## <a name="next-steps"></a>次の手順

このチュートリアルでは、次の方法など、VPN ゲートウェイの基本的な作成と管理について説明しました。

> [!div class="checklist"]
> * VPN ゲートウェイの作成
> * VPN ゲートウェイのサイズ変更
> * VPN ゲートウェイのリセット

S2S 接続、VNet 間接続、P2S 接続については、次のチュートリアルに進んでください。

> [!div class="nextstepaction"]
> * [S2S 接続の作成](vpn-gateway-tutorial-vpnconnection-powershell.md)
> * [VNet 間接続の作成](vpn-gateway-howto-vnet-vnet-resource-manager-portal.md)
> * [P2S 接続の作成](vpn-gateway-howto-point-to-site-resource-manager-portal.md)
