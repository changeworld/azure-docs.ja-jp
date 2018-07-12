---
title: PowerShell を使用して Azure S2S VPN 接続を作成および管理する | Microsoft Docs
description: チュートリアル - Azure PowerShell モジュールを使用して S2S VPN 接続を作成および管理する
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
ms.date: 05/08/2018
ms.author: yushwang
ms.custom: mvc
ms.openlocfilehash: da077f013c558448be63dce9b215ded99362d22e
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/11/2018
ms.locfileid: "38452466"
---
# <a name="create-and-manage-s2s-vpn-connections-with-the-azure-powershell-module"></a>Azure PowerShell モジュールを使用して S2S VPN 接続を作成および管理する

Azure S2S VPN 接続は、顧客構内と Azure との間の安全なクロスプレミス接続を提供します。 このチュートリアルでは、S2S VPN 接続の作成や管理などの IPsec S2S VPN 接続のライフ サイクルについて説明します。 学習内容は次のとおりです。

> [!div class="checklist"]
> * S2S VPN 接続を作成する
> * 接続プロパティを更新する: 事前共有キー、BGP、IPsec/IKE ポリシー
> * VPN 接続をさらに追加する
> * VPN 接続を削除する

次の図に、このチュートリアルのトポロジを示します。

![サイト間 VPN 接続の図](./media/vpn-gateway-tutorial-vpnconnection-powershell/site-to-site-diagram.png)

[!INCLUDE [cloud-shell-powershell.md](../../includes/cloud-shell-powershell.md)]

PowerShell をインストールしてローカルで使用する場合、このチュートリアルでは Azure PowerShell モジュール バージョン 5.3 以降が必要になります。 バージョンを確認するには、`Get-Module -ListAvailable AzureRM` を実行します。 アップグレードする必要がある場合は、[Azure PowerShell モジュールのインストール](/powershell/azure/install-azurerm-ps)に関するページを参照してください。 PowerShell をローカルで実行している場合、`Login-AzureRmAccount` を実行して Azure との接続を作成することも必要です。

## <a name="requirements"></a>必要条件

[Azure PowerShell を使用した VPN ゲートウェイの作成](vpn-gateway-tutorial-create-gateway-powershell.md)に関する最初のチュートリアルを実行して、次のリソースを作成します。

1. リソース グループ (TestRG1)、仮想ネットワーク (VNet1)、および GatewaySubnet
2. VPN ゲートウェイ (VNet1GW)

仮想ネットワークのパラメーター値は次のとおりです。 使用しているオンプレミス ネットワークを表すローカル ネットワーク ゲートウェイの追加の値に注意してください。 環境とネットワークの設定に基づいて値を変更してください。

```azurepowershell-interactive
# Virtual network
$RG1         = "TestRG1"
$VNet1       = "VNet1"
$Location1   = "East US"
$VNet1Prefix = "10.1.0.0/16"
$VNet1ASN    = 65010
$Gw1         = "VNet1GW"

# On-premises network
$LNG1        = "VPNsite1"
$LNGprefix1  = "10.101.0.0/24"
$LNGprefix2  = "10.101.1.0/24"
$LNGIP1      = "YourDevicePublicIP"

# Optional - on-premises BGP properties
$LNGASN1     = 65011
$BGPPeerIP1  = "10.101.1.254"

# Connection
$Connection1 = "VNet1ToSite1"
```

S2S VPN 接続を作成するためのワークフローは単純です。

1. オンプレミス ネットワークを表すローカル ネットワーク ゲートウェイを作成する
2. Azure VPN ゲートウェイとローカル ネットワーク ゲートウェイの間に接続を作成する

## <a name="create-a-local-network-gateway"></a>ローカル ネットワーク ゲートウェイの作成

ローカル ネットワーク ゲートウェイは、オンプレミスのネットワークを表します。 オンプレミス ネットワークのプロパティはローカル ネットワーク ゲートウェイに指定できます。これには次の項目が含まれます。

* VPN デバイスのパブリック IP アドレス
* オンプレミスのアドレス空間
* (オプション) BGP 属性 (BGP ピア IP アドレスおよび AS 番号)

[New-AzureRmLocalNetworkGateway](/powershell/module/azurerm.resources/new-azurermlocalnetworkgateway) コマンドを使用して、ローカル ネットワーク ゲートウェイを作成します。

```azurepowershell-interactive
New-AzureRmLocalNetworkGateway -Name $LNG1 -ResourceGroupName $RG1 `
  -Location 'East US' -GatewayIpAddress $LNGIP1 -AddressPrefix $LNGprefix1,$LNGprefix2
```

## <a name="create-a-s2s-vpn-connection"></a>S2S VPN 接続を作成する

次に、[New-AzureRmVirtualNetworkGatewayConnection](/powershell/module/azurerm.resources/new-azurermvirtualnetworkgatewayconnection) を使用して、仮想ネットワーク ゲートウェイと VPN デバイスの間にサイト間 VPN 接続を作成します。 サイト間 VPN の "-ConnectionType" が *IPsec* であることに注意してください。

```azurepowershell-interactive
$vng1 = Get-AzureRmVirtualNetworkGateway -Name $GW1  -ResourceGroupName $RG1
$lng1 = Get-AzureRmLocalNetworkGateway   -Name $LNG1 -ResourceGroupName $RG1

New-AzureRmVirtualNetworkGatewayConnection -Name $Connection1 -ResourceGroupName $RG1 `
  -Location $Location1 -VirtualNetworkGateway1 $vng1 -LocalNetworkGateway2 $lng1 `
  -ConnectionType IPsec -SharedKey "Azure@!b2C3"
```

BGP を使用している場合は、オプションの "**-EnableBGP $True**" プロパティを追加して、接続の BGP を有効にします。 この機能は既定で無効になっています。

## <a name="update-the-vpn-connection-pre-shared-key-bgp-and-ipsecike-policy"></a>VPN 接続の事前共有キー、BGP、および IPsec/IKE ポリシーを更新する

### <a name="view-and-update-your-pre-shared-key"></a>事前共有キーを表示および更新する

Azure S2S VPN 接続では、事前共有キー (シークレット) を使用して、オンプレミス VPN デバイスと Azure VPN ゲートウェイとの間の認証を行います。 [Get-AzureRmVirtualNetworkGatewayConnectionSharedKey](/powershell/module/azurerm.resources/get-azurermvirtualnetworkgatewayconnectionsharedkey) および [Set-AzureRmVirtualNetworkGatewayConnectionSharedKey](/powershell/module/azurerm.resources/set-azurermvirtualnetworkgatewayconnectionsharedkey) を使用して、接続の事前共有キーを表示および更新できます。

> [!IMPORTANT]
> 事前共有キーは、長さが 128 文字以下の**印刷可能 ASCII 文字**から構成される文字列です。

このコマンドを実行すると、接続の事前共有キーが表示されます。

```azurepowershell-interactive
Get-AzureRmVirtualNetworkGatewayConnectionSharedKey `
  -Name $Connection1 -ResourceGroupName $RG1
```

上記の例の出力は、"**Azure@!b2C3**" になります。 事前共有キーの値を "**Azure@!_b2=C3**" に変更するには、次のコマンドを使用します。

```azurepowershell-interactive
Set-AzureRmVirtualNetworkGatewayConnectionSharedKey `
  -Name $Connection1 -ResourceGroupName $RG1 `
  -Value "Azure@!_b2=C3"
```

### <a name="enable-bgp-on-vpn-connection"></a>VPN 接続で BGP を有効にする

Azure VPN ゲートウェイは、BGP 動的ルーティング プロトコルをサポートします。 オンプレミス ネットワークおよびデバイスで BGP を使用しているかどうかに応じて、個々の接続で BGP を有効にすることができます。 接続で BGP を有効にする前に、次の BGP プロパティを指定します。

* Azure VPN ASN (自律システム番号)
* オンプレミス ローカル ネットワーク ゲートウェイ ASN
* オンプレミス ローカル ネットワーク ゲートウェイ BGP ピア IP アドレス

BGP のプロパティを構成していない場合は、[Set-AzureRmVirtualNetworkGateway](/powershell/module/azurerm.resources/set-azurermvirtualnetworkgateway) コマンドと [Set-AzureRmLocalNetworkGateway](/powershell/module/azurerm.resources/set-azurermlocalnetworkgateway) コマンドを使用して、VPN ゲートウェイとローカル ネットワーク ゲートウェイにこれらのプロパティを追加します。

```azurepowershell-interactive
$vng1 = Get-AzureRmVirtualNetworkGateway -Name $GW1  -ResourceGroupName $RG1
Set-AzureRmVirtualNetworkGateway -VirtualNetworkGateway $vng1 -Asn $VNet1ASN

$lng1 = Get-AzureRmLocalNetworkGateway   -Name $LNG1 -ResourceGroupName $RG1
Set-AzureRmLocalNetworkGateway -LocalNetworkGateway $lng1 `
  -Asn $LNGASN1 -BgpPeeringAddress $BGPPeerIP1
```

[Set-AzureRmVirtualNetworkGatewayConnection](/powershell/module/azurerm.resources/set-azurermvirtualnetworkgatewayconnection) を使用して BGP を有効にします。

```azurepowershell-interactive
$connection = Get-AzureRmVirtualNetworkGatewayConnection `
  -Name $Connection1 -ResourceGroupName $RG1

Set-AzureRmVirtualNetworkGatewayConnection -VirtualNetworkGatewayConnection $connection `
  -EnableBGP $True
```

BGP を無効にするには、"-EnableBGP" プロパティの値を **$False** に変更します。 Azure VPN ゲートウェイの BGP の詳細については、[Azure VPN ゲートウェイの BGP](vpn-gateway-bgp-resource-manager-ps.md)に関するページを参照してください。

### <a name="apply-a-custom-ipsecike-policy-on-the-connection"></a>接続にカスタム IPsec/IKE ポリシーを適用する

オプションの IPsec/IKE ポリシーを適用して、[既定のプロポーザル](vpn-gateway-about-vpn-devices.md#ipsec)を使用する代わりに、接続における IPsec/IKE 暗号アルゴリズムとキー強度の正確な組み合わせを指定できます。 次のサンプル スクリプトは、次のアルゴリズムとパラメーターを使用して別の IPsec/IKE ポリシーを作成します。

* IKEv2: AES256、SHA256、DHGroup14
* IPsec: AES128、SHA1、PFS14、SA の有効期間 14,400 秒、および 102,400,000 KB

```azurepowershell-interactive
$connection = Get-AzureRmVirtualNetworkGatewayConnection -Name $Connection1 `
                -ResourceGroupName $RG1
$newpolicy  = New-AzureRmIpsecPolicy `
                -IkeEncryption AES256 -IkeIntegrity SHA256 -DhGroup DHGroup14 `
                -IpsecEncryption AES128 -IpsecIntegrity SHA1 -PfsGroup PFS2048 `
                -SALifeTimeSeconds 14400 -SADataSizeKilobytes 102400000

Set-AzureRmVirtualNetworkGatewayConnection -VirtualNetworkGatewayConnection $connection `
  -IpsecPolicies $newpolicy
```

アルゴリズムと手順の完全な一覧については、[S2S または VNet 間接続の IPsec/IKE ポリシー](vpn-gateway-ipsecikepolicy-rm-powershell.md)に関するページを参照してください。

## <a name="add-another-s2s-vpn-connection"></a>別の S2S VPN 接続を追加する

追加の S2S VPN 接続を同じ VPN ゲートウェイに追加するには、別のローカル ネットワーク ゲートウェイを作成し、新しいローカル ネットワーク ゲートウェイと VPN ゲートウェイとの間に新しい接続を作成します。 この記事の例を示します。

```azurepowershell-interactive
# On-premises network
$LNG2        = "VPNsite2"
$Location2   = "West US"
$LNGprefix21 = "10.102.0.0/24"
$LNGprefix22 = "10.102.1.0/24"
$LNGIP2      = "YourDevicePublicIP"
$Connection2 = "VNet1ToSite2"

New-AzureRmLocalNetworkGateway -Name $LNG2 -ResourceGroupName $RG1 `
  -Location $Location2 -GatewayIpAddress $LNGIP2 -AddressPrefix $LNGprefix21,$LNGprefix22

$vng1 = Get-AzureRmVirtualNetworkGateway -Name $GW1  -ResourceGroupName $RG1
$lng2 = Get-AzureRmLocalNetworkGateway   -Name $LNG2 -ResourceGroupName $RG1

New-AzureRmVirtualNetworkGatewayConnection -Name $Connection2 -ResourceGroupName $RG1 `
  -Location $Location1 -VirtualNetworkGateway1 $vng1 -LocalNetworkGateway2 $lng2 `
  -ConnectionType IPsec -SharedKey "AzureA1%b2_C3+"
```

これで、Azure VPN ゲートウェイへの 2 つの S2S VPN 接続が作成されました。

![マルチサイトの VPN 接続](./media/vpn-gateway-tutorial-vpnconnection-powershell/multisite-connections.png)

## <a name="delete-a-s2s-vpn-connection"></a>S2S VPN 接続を削除する

[Remove-AzureRmVirtualNetworkGatewayConnection](/powershell/module/azurerm.resources/remove-azurermvirtualnetworkgatewayconnection) を使用して S2S VPN 接続を削除します。

```azurepowershell-interactive
Remove-AzureRmVirtualNetworkGatewayConnection -Name $Connection2 -ResourceGroupName $RG1
```

ローカル ネットワーク ゲートウェイが不要になった場合はこれを削除します。 ローカル ネットワーク ゲートウェイに他の接続が関連付けられている場合、そのローカル ネットワーク ゲートウェイを削除することはできません。

```azurepowershell-interactive
Remove-AzureRmVirtualNetworkGatewayConnection -Name $LNG2 -ResourceGroupName $RG1
```

## <a name="next-steps"></a>次の手順

このチュートリアルでは、次のような S2S VPN 接続の作成と管理について説明しました。

> [!div class="checklist"]
> * S2S VPN 接続を作成する
> * 接続プロパティを更新する: 事前共有キー、BGP、IPsec/IKE ポリシー
> * VPN 接続をさらに追加する
> * VPN 接続を削除する

S2S 接続、VNet 間接続、および P2S 接続については、次のチュートリアルに進んでください。

> [!div class="nextstepaction"]
> * [VNet 間接続の作成](vpn-gateway-howto-vnet-vnet-resource-manager-portal.md)
> * [P2S 接続の作成](vpn-gateway-howto-point-to-site-resource-manager-portal.md)
