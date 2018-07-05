---
title: Azure Availability Zones にゾーン冗長仮想ネットワーク ゲートウェイを作成する - プレビュー | Microsoft Docs
description: Availability Zones に VPN ゲートウェイと ExpressRoute ゲートウェイをデプロイする - プレビュー
services: vpn-gateway
documentationcenter: na
author: cherylmc
Customer intent: As someone with a basic network background, I want to understand how to create zone-redundant gateways.
ms.service: vpn-gateway
ms.topic: article
ms.date: 06/28/2018
ms.author: cherylmc
ms.openlocfilehash: c484358bf98f0121cfc3ce270b162b01c75b5b09
ms.sourcegitcommit: d7725f1f20c534c102021aa4feaea7fc0d257609
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/29/2018
ms.locfileid: "37096235"
---
# <a name="create-a-zone-redundant-virtual-network-gateway-in-azure-availability-zones---preview"></a>Azure Availability Zones にゾーン冗長仮想ネットワーク ゲートウェイを作成する - プレビュー

[Azure Availability Zones](../availability-zones/az-overview.md) に、VPN ゲートウェイと ExpressRoute ゲートウェイをデプロイできます。 これにより、仮想ネットワーク ゲートウェイに回復性、スケーラビリティ、高可用性が提供されます。 Azure Availability Zones にゲートウェイをデプロイすると、オンプレミス ネットワークの Azure への接続をゾーン レベルの障害から保護しながら、ゲートウェイを 1 つのリージョン内に物理的かつ論理的に分離できます。

ゾーン ゲートウェイとゾーン冗長ゲートウェイでは、基本的なパフォーマンスが通常の仮想ネットワーク ゲートウェイよりも向上しています。 さらに、ゾーン仮想ネットワーク ゲートウェイとゾーン冗長仮想ネットワーク ゲートウェイは他のゲートウェイよりも短時間で作成できます。 所要時間は、通常の 45 分に対して ExpressRoute ゲートウェイは 15 分、VPN ゲートウェイは 19 分です。

### <a name="zrgw"></a>ゾーン冗長ゲートウェイ

複数の可用性ゾーンにわたって仮想ネットワーク ゲートウェイを自動的にデプロイするには、ゾーン冗長仮想ネットワーク ゲートウェイを使用します。 ゾーン冗長ゲートウェイでは、Azure 上のミッション クリティカルでスケーラブルなサービスへのアクセスにおいて、99.99% のアップタイム SLA (一般提供時) が提供されます。

<br>
<br>

![ゾーン冗長ゲートウェイの図](./media/create-zone-redundant-vnet-gateway/zonered.png)

### <a name="zgw"></a>ゾーン ゲートウェイ

特定の 1 つのゾーンにゲートウェイをデプロイするには、ゾーン ゲートウェイを使用します。 ゾーン ゲートウェイをデプロイすると、両方のゲートウェイ インスタンスが同じ可用性ゾーンにデプロイされます。

<br>
<br>

![ゾーン ゲートウェイの図](./media/create-zone-redundant-vnet-gateway/zonal.png)

## <a name="gwskus"></a>ゲートウェイの SKU

ゾーン冗長ゲートウェイとゾーン ゲートウェイは新しいゲートウェイ SKU を使用する必要があります。 [プレビューに自己登録](#enroll)すると、すべての Azure AZ リージョン内の新しい仮想ネットワーク ゲートウェイ SKU が表示されます。 これらの SKU は ExpressRoute および VPN Gateway の対応する SKU と似ていますが、ゾーン冗長ゲートウェイとゾーン ゲートウェイに固有であるという点が異なります。

新しいゲートウェイ SKU は次のとおりです。

### <a name="vpn-gateway"></a>VPN Gateway

* VpnGw1AZ
* VpnGw2AZ
* VpnGw3AZ

### <a name="expressroute"></a>ExpressRoute

* ErGw1AZ
* ErGw2AZ
* ErGw3AZ

## <a name="pipskus"></a>パブリック IP の SKU

ゾーン冗長ゲートウェイとゾーン ゲートウェイはいずれも、Azure パブリック IP リソースの *Standard* SKU に依存します。 Azure パブリック IP リソースの構成によって、デプロイするゲートウェイがゾーン冗長ゲートウェイになるかゾーン ゲートウェイになるかが決まります。 *Basic* SKU を使用してパブリック IP リソースを作成した場合、ゲートウェイはゾーン冗長性を持たず、ゲートウェイ リソースはリージョン固有となります。

### <a name="pipzrg"></a>ゾーン冗長ゲートウェイ

ゾーンを指定せずに **Standard** パブリック IP SKU を使用してパブリック IP アドレスを作成した場合、動作はゲートウェイが VPN ゲートウェイであるか ExpressRoute ゲートウェイであるかによって異なります。 

* VPN ゲートウェイの場合は、2 つのゲートウェイ インスタンスがこれらの 3 つのゾーンの 2 つにデプロイされ、ゾーン冗長性が提供されます。 
* ExpressRoute ゲートウェイの場合は、3 つ以上のインスタンスを作成できるため、ゲートウェイは 3 つのゾーンすべてにわたることができます。

### <a name="pipzg"></a>ゾーン ゲートウェイ

**Standard** パブリック IP SKU を使用してパブリック IP アドレスを作成し、ゾーン (1、2、または 3) を指定した場合、すべてのゲートウェイ インスタンスが同じゾーンにデプロイされます。

### <a name="piprg"></a>リージョン ゲートウェイ

**Basic** パブリック IP SKU を使用してパブリック IP アドレスを作成した場合、ゲートウェイはリージョン ゲートウェイとしてデプロイされ、ゲートウェイにはゾーン冗長性が組み込まれません。

## <a name="before-you-begin"></a>開始する前に

お使いのコンピューターにローカルにインストールされている PowerShell か、Azure Cloud Shell のいずれかを使用できます。 PowerShell をローカルにインストールして使用する場合、この機能には最新バージョンの PowerShell モジュールが必要です。

[!INCLUDE [Cloud shell](../../includes/vpn-gateway-cloud-shell-powershell.md)]

### <a name="to-use-powershell-locally"></a>PowerShell をローカルで使用するには

Cloud Shell を使用するのではなくコンピューターで PowerShell をローカルに使用する場合は、PowerShell モジュール 6.1.1 以降をインストールする必要があります。 インストールされている PowerShell のバージョンを確認するには、次のコマンドを使用します。

```azurepowershell
Get-Module AzureRM -ListAvailable | Select-Object -Property Name,Version,Path
```

アップグレードする必要がある場合は、[Azure PowerShell モジュールのインストール](/powershell/azure/install-azurerm-ps)に関するページを参照してください。

[!INCLUDE [PowerShell login](../../includes/vpn-gateway-ps-login-include.md)]

## <a name="enroll"></a>1.プレビューに登録する

ゾーン冗長ゲートウェイまたはゾーン ゲートウェイを構成する前に、まずプレビューでサブスクリプションを自己登録する必要があります。 サブスクリプションがプロビジョニングされると、すべての Azure AZ リージョン内の新しいゲートウェイ SKU が表示されます。 

Azure アカウントにサインインしていて、このプレビューのホワイトリストに登録するサブスクリプションを使用していることを確認します。 次の例を使用して登録します。

```azurepowershell-interactive
Register-AzureRmProviderFeature -FeatureName AllowVMSSVirtualNetworkGateway -ProviderNamespace Microsoft.Network
Register-AzureRmResourceProvider -ProviderNamespace Microsoft.Network
```

次のコマンドを使用して、そのサブスクリプションで 'AllowVMSSVirtualNetworkGateway' 機能が登録されていることを確認します。

```azurepowershell-interactive
Get-AzureRmProviderFeature -ProviderNamespace Microsoft.Network
```

結果は次の例のようになります。

![プロビジョニングの完了](./media/create-zone-redundant-vnet-gateway/verifypreview.png)

## <a name="variables"></a>2.変数を宣言する

この例の手順で使用する値は以下のとおりです。 さらに、一部の例では、手順で宣言された変数を使用します。 実際の環境でこれらの手順を使用するときは、必ずこれらの値を実際の値で置き換えてください。 場所を指定するときは、指定するリージョンがサポートされていることを確認してください。 詳細については、[FAQ](#faq) をご覧ください。

```azurepowershell-interactive
$RG1         = "TestRG1"
$VNet1       = "VNet1"
$Location1   = "CentralUS"
$FESubnet1   = "FrontEnd"
$BESubnet1   = "Backend"
$GwSubnet1   = "GatewaySubnet"
$VNet1Prefix = "10.1.0.0/16"
$FEPrefix1   = "10.1.0.0/24"
$BEPrefix1   = "10.1.1.0/24"
$GwPrefix1   = "10.1.255.0/27"
$Gw1         = "VNet1GW"
$GwIP1       = "VNet1GWIP"
$GwIPConf1   = "gwipconf1"
```

## <a name="configure"></a>3.仮想ネットワークの作成

リソース グループを作成します。

```azurepowershell-interactive
New-AzureRmResourceGroup -ResourceGroupName $RG1 -Location $Location1
```

仮想ネットワークを作成します。

```azurepowershell-interactive
$fesub1 = New-AzureRmVirtualNetworkSubnetConfig -Name $FESubnet1 -AddressPrefix $FEPrefix1
$besub1 = New-AzureRmVirtualNetworkSubnetConfig -Name $BESubnet1 -AddressPrefix $BEPrefix1
$vnet = New-AzureRmVirtualNetwork -Name $VNet1 -ResourceGroupName $RG1 -Location $Location1 -AddressPrefix $VNet1Prefix -Subnet $fesub1,$besub1
```

## <a name="gwsub"></a>4.Add the gateway subnet

ゲートウェイ サブネットには、仮想ネットワーク ゲートウェイ サービスが使用する予約済み IP アドレスが含まれます。 次の例を使用して、ゲートウェイ サブネットを追加し設定します。

ゲートウェイ サブネットを追加します。

```azurepowershell-interactive
$getvnet = Get-AzureRmVirtualNetwork -ResourceGroupName $RG1 -Name VNet1
Add-AzureRmVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -AddressPrefix 10.1.255.0/27 -VirtualNetwork $getvnet
```

仮想ネットワークのゲートウェイ サブネット構成を設定します。

```azurepowershell-interactive
$getvnet | Set-AzureRmVirtualNetwork
```
## <a name="publicip"></a>5.パブリック IP アドレスの要求
 
この手順では、作成するゲートウェイに当てはまる説明を選択してください。 ゲートウェイをデプロイするゾーンの選択は、パブリック IP アドレスに対して指定されたゾーンに依存します。

### <a name="ipzoneredundant"></a>ゾーン冗長ゲートウェイの場合

**Standard** PublicIpaddress SKU を使用してパブリック IP アドレスを要求し、ゾーンは指定しません。 この場合、作成される Standard パブリック IP アドレスはゾーン冗長パブリック IP となります。   

```azurepowershell-interactive
$pip1 = New-AzureRmPublicIpAddress -ResourceGroup $RG1 -Location $Location1 -Name $GwIP1 -AllocationMethod Static -Sku Standard
```

### <a name="ipzonalgw"></a>ゾーン ゲートウェイの場合

**Standard** PublicIpaddress SKU を使用してパブリック IP アドレスを要求し、 ゾーン (1、2、または 3) を指定します。 すべてのゲートウェイ インスタンスがこのゾーンにデプロイされます。

```azurepowershell-interactive
$pip1 = New-AzureRmPublicIpAddress -ResourceGroup $RG1 -Location $Location1 -Name $GwIP1 -AllocationMethod Static -Sku Standard -Zone 1
```

### <a name="ipregionalgw"></a>リージョン ゲートウェイの場合

**Basic** PublicIpaddress SKU を使用してパブリック IP アドレスを要求します。 この場合、ゲートウェイはリージョン ゲートウェイとしてデプロイされ、ゲートウェイにはゾーン冗長性が組み込まれません。 ゲートウェイ インスタンスはすべてのゾーンにそれぞれ作成されます。

```azurepowershell-interactive
$pip1 = New-AzureRmPublicIpAddress -ResourceGroup $RG1 -Location $Location1 -Name $GwIP1 -AllocationMethod Dynamic -Sku Basic
```
## <a name="gwipconfig"></a>6.IP 構成を作成する

```azurepowershell-interactive
$getvnet = Get-AzureRmVirtualNetwork -ResourceGroupName $RG1 -Name $VNet1
$subnet = Get-AzureRmVirtualNetworkSubnetConfig -Name $GwSubnet1 -VirtualNetwork $getvnet
$gwipconf1 = New-AzureRmVirtualNetworkGatewayIpConfig -Name $GwIPConf1 -Subnet $subnet -PublicIpAddress $pip1
```

## <a name="gwconfig"></a>7.ゲートウェイを作成する

仮想ネットワーク ゲートウェイを作成します。

>[!NOTE]
>現時点では、ゲートウェイの SKU を指定することはできません。 SKU は自動的に既定値に設定され、ExpressRoute の場合は ErGw1AZ、VPN Gateway の場合は VpnGw1AZ となります。
>

### <a name="for-expressroute"></a>ExpressRoute の場合

```azurepowershell-interactive
New-AzureRmVirtualNetworkGateway -ResourceGroup $RG1 -Location $Location1 -Name $Gw1 -IpConfigurations $GwIPConf1 -GatewayType ExpressRoute
```

### <a name="for-vpn-gateway"></a>VPN Gateway の場合

```azurepowershell-interactive
New-AzureRmVirtualNetworkGateway -ResourceGroup $RG1 -Location $Location1 -Name $Gw1 -IpConfigurations $GwIPConf1 -GatewayType Vpn -VpnType RouteBased
```

## <a name="feedback"></a>フィードバックの提供方法

お客様からのフィードバックをお待ちしています。 ゾーン冗長およびゾーン VPN ゲートウェイと Express Route ゲートウェイに関する問題を報告したり (肯定的、否定的を問わず) フィードバック を送信するには、aznetworkgateways@microsoft.com まで電子メールをお送りください。 件名の “[ ]” には、お客様の会社名を入力してください。 また、問題を報告する場合は、サブスクリプション ID も入力してください。

## <a name="faq"></a>FAQ

### <a name="how-do-i-sign-up-for-the-preview"></a>プレビューにサインアップするにはどうすればよいですか。

この記事の PowerShell コマンドを使用して[自己登録](#enroll)していただくことができます。

### <a name="what-will-change-when-i-enroll"></a>登録すると何が変わりますか。

お客様の観点からは、プレビュー中に、ゾーン冗長性を備えたゲートウェイをデプロイできるようになります。 つまり、すべてのゲートウェイ インスタンスが複数の Azure Availability Zones にわたってデプロイされ、それぞれの Availability Zone が別々の障害ドメインおよび更新ドメインとなります。 これにより、ゲートウェイの信頼性、可用性、およびゾーン障害に対する回復性が向上します。

### <a name="what-regions-are-available-for-the-preview"></a>このプレビューではどのようなリージョンを利用できますか。

ゾーン冗長ゲートウェイとゾーン ゲートウェイは、運用環境/Azure パブリック リージョンで利用できます。

### <a name="will-i-be-billed-for-participating-in-this-preview"></a>このプレビューへの参加に対しては課金されますか。

プレビュー中は、ゲートウェイに対して課金されることはありません。 ただし、デプロイには SLA が関連付けられていません。 ぜひ、フィードバックをお寄せください。

> [!NOTE]
> ExpressRoute ゲートウェイでは、ゲートウェイに対する課金または料金請求はありません。 しかし、(ゲートウェイではなく) 回路自体に対しては課金されます。

### <a name="what-regions-are-available-for-me-to-try-this-in"></a>これを試すのに、どのようなリージョンを利用できますか。

パブリック プレビューは、米国中部およびフランス中部リージョン (Availability Zones が一般提供されている Azure リージョン) で利用できます。 将来的には、他の Azure パブリック リージョンでもゾーン冗長ゲートウェイを利用できるようになる予定です。

### <a name="can-i-change-my-existing-virtual-network-gateways-to-zone-redundant-or-zonal-gateways"></a>既存の仮想ネットワーク ゲートウェイをゾーン冗長ゲートウェイまたはゾーン ゲートウェイに変更することはできますか。

既存の仮想ネットワーク ゲートウェイからゾーン冗長ゲートウェイまたはゾーン ゲートウェイへの移行は、現時点ではサポートされていません。 しかし、既存のゲートウェイを削除して、ゾーン冗長ゲートウェイまたはゾーン ゲートウェイを再作成することは可能です。

### <a name="can-i-deploy-both-vpn-and-express-route-gateways-in-same-virtual-network"></a>VPN ゲートウェイと Express Route ゲートウェイの両方を同じ仮想ネットワークにデプロイできますか。

パブリック プレビュー中は、同じ仮想ネットワーク内での VPN ゲートウェイと Express Route ゲートウェイの共存がサポートされています。 ただし、次の要件および制限事項に注意してください。

* ゲートウェイ サブネット用に、/27 IP アドレス範囲を予約する。
* ゾーン冗長/ゾーン Express Route ゲートウェイは、ゾーン冗長/ゾーン VPN ゲートウェイとしか共存できない。
* ゾーン冗長/ゾーン VPN ゲートウェイをデプロイする前に、ゾーン冗長/ゾーン Express Route ゲートウェイをデプロイする。
* ゾーン冗長/ゾーン Express Route ゲートウェイは、最大 4 つの回路にしか接続できない。

## <a name="next-steps"></a>次の手順

お客様からのフィードバックをお待ちしています。 ゾーン冗長およびゾーン VPN ゲートウェイと Express Route ゲートウェイに関する問題を報告したり (肯定的、否定的を問わず) フィードバック を送信するには、aznetworkgateways@microsoft.com まで電子メールをお送りください。 件名の “[ ]” には、お客様の会社名を入力してください。 また、問題を報告する場合は、サブスクリプション ID も入力してください。
