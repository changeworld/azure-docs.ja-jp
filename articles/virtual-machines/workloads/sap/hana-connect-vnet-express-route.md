---
title: 仮想ネットワークから SAP HANA on Azure (L インスタンス) への接続のセットアップ | Microsoft Docs
description: SAP HANA on Azure (L インスタンス) を使用した仮想ネットワークからの接続のセットアップ。
services: virtual-machines-linux
documentationcenter: ''
author: RicksterCDN
manager: jeconnoc
editor: ''
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 09/10/2018
ms.author: rclaus
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 5efdda485e4e1f5013948c6636b267f0d388f4d5
ms.sourcegitcommit: 2d961702f23e63ee63eddf52086e0c8573aec8dd
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/07/2018
ms.locfileid: "44167637"
---
# <a name="connecting-a-vnet-to-hana-large-instance-expressroute"></a>HANA L インスタンスの ExpressRoute への VNet の接続

IP アドレス範囲をすべて定義し、必要なデータを Microsoft から入手したら、いよいよ先ほど作成した VNet を HANA L インスタンスに接続することができます。 Azure VNet を作成した後、VNet 上に ExpressRoute ゲートウェイを作成する必要があります。これを利用して、L インスタンス スタンプのカスタマー テナントに接続する ExpressRoute 回線に VNet をリンクします。

> [!NOTE] 
> この手順では、新しいゲートウェイは指定された Azure サブスクリプション内で作成されてから、指定された Azure VNet に接続されるので、最大 30 分かかる場合があります。

ゲートウェイが既に存在する場合、それが ExpressRoute ゲートウェイかどうかを確認します。 そうでなかった場合、ゲートウェイを削除し、ExpressRoute ゲートウェイとして再作成する必要があります。 ExpressRoute ゲートウェイが既に確立されている場合、Azure VNet は既にオンプレミス接続を行うために ExpressRoute 回線に接続されているので、次の「VNet のリンク」セクションに進んでください。

- (新しい) [Azure Portal](https://portal.azure.com/) または PowerShell を使用して、お使いの VNet に接続された ExpressRoute VPN ゲートウェイを作成します。
  - Azure Portal を使用する場合、新しい **Virtual Network ゲートウェイ**を追加し、ゲートウェイの種類として **ExpressRoute** を選択します。
  - 代わりに PowerShell を使用する場合、まずは最新の [Azure PowerShell SDK](https://azure.microsoft.com/downloads/) をダウンロードして使用し、最適なエクスペリエンスを確保します。 次のコマンドは ExpressRoute ゲートウェイを作成します。 _$_ の後に続くテキストはユーザー定義変数で、ユーザー固有の情報に更新する必要があります。

```PowerShell
# These Values should already exist, update to match your environment
$myAzureRegion = "eastus"
$myGroupName = "SAP-East-Coast"
$myVNetName = "VNet01"

# These values are used to create the gateway, update for how you wish the GW components to be named
$myGWName = "VNet01GW"
$myGWConfig = "VNet01GWConfig"
$myGWPIPName = "VNet01GWPIP"
$myGWSku = "HighPerformance" # Supported values for HANA Large Instances are: HighPerformance or UltraPerformance

# These Commands create the Public IP and ExpressRoute Gateway
$vnet = Get-AzureRmVirtualNetwork -Name $myVNetName -ResourceGroupName $myGroupName
$subnet = Get-AzureRmVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -VirtualNetwork $vnet
New-AzureRmPublicIpAddress -Name $myGWPIPName -ResourceGroupName $myGroupName `
-Location $myAzureRegion -AllocationMethod Dynamic
$gwpip = Get-AzureRmPublicIpAddress -Name $myGWPIPName -ResourceGroupName $myGroupName
$gwipconfig = New-AzureRmVirtualNetworkGatewayIpConfig -Name $myGWConfig -SubnetId $subnet.Id `
-PublicIpAddressId $gwpip.Id

New-AzureRmVirtualNetworkGateway -Name $myGWName -ResourceGroupName $myGroupName -Location $myAzureRegion `
-IpConfigurations $gwipconfig -GatewayType ExpressRoute `
-GatewaySku $myGWSku -VpnType PolicyBased -EnableBgp $true
```

この例では、HighPerformance ゲートウェイ SKU が使用されました。 使用できるのは、HighPerformance または UltraPerformance です。SAP HANA on Azure (L インスタンス) でサポートされているゲートウェイ SKU はこれらのみとなります。

> [!IMPORTANT]
> Type II クラス SKU の HANA Large Instances の場合、UltraPerformance ゲートウェイ SKU を使用することが必須です。

**VNet のリンク**

Azure VNet 用に ExpressRoute ゲートウェイを作成した後は、Microsoft から提供された承認情報を使用して、ExpressRoute ゲートウェイを SAP HANA on Azure (L インスタンス) の ExpressRoute 回線 (この接続を行うために作成したもの) に接続します。 この手順は、Azure Portal または PowerShell で実行できます。 ポータルの使用を推奨しますが、PowerShell での手順は次のとおりです。 

- 接続ごとに異なる AuthGUID を使用して、各 VNet ゲートウェイで次のコマンドを実行します。 次のスクリプトに示す最初の 2 つのエントリは、Microsoft から提供される情報です。 また、AuthGUID はすべての VNet とそのゲートウェイに固有です。 つまり、別の Azure VNet を追加する場合は、HANA L インスタンスを Azure に接続する ExpressRoute 回線の AuthID がもう 1 つ必要になります。 

```PowerShell
# Populate with information provided by Microsoft Onboarding team
$PeerID = "/subscriptions/9cb43037-9195-4420-a798-f87681a0e380/resourceGroups/Customer-USE-Circuits/providers/Microsoft.Network/expressRouteCircuits/Customer-USE01"
$AuthGUID = "76d40466-c458-4d14-adcf-3d1b56d1cd61"

# Your ExpressRoute Gateway Information
$myGroupName = "SAP-East-Coast"
$myGWName = "VNet01GW"
$myGWLocation = "East US"

# Define the name for your connection
$myConnectionName = "VNet01GWConnection"

# Create a new connection between the ER Circuit and your Gateway using the Authorization
$gw = Get-AzureRmVirtualNetworkGateway -Name $myGWName -ResourceGroupName $myGroupName
    
New-AzureRmVirtualNetworkGatewayConnection -Name $myConnectionName `
-ResourceGroupName $myGroupName -Location $myGWLocation -VirtualNetworkGateway1 $gw `
-PeerId $PeerID -ConnectionType ExpressRoute -AuthorizationKey $AuthGUID
```

サブスクリプションに関連付けられた複数の ExpressRoute 回線にゲートウェイを接続する場合は、この手順を複数回実行しなければならないことがあります。 たとえば、通常は同じ VNet ゲートウェイを、VNet とオンプレミス ネットワークとを接続する ExpressRoute 回線に接続します。

**次のステップ**

- 「[HLI 向け追加のネットワーク要件](hana-additional-network-requirements.md)」を参照してください。