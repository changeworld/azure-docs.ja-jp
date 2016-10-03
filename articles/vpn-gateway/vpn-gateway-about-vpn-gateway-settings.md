<properties 
   pageTitle="仮想ネットワーク ゲートウェイの VPN Gateway 設定について | Microsoft Azure"
   description="Azure Virtual Network の VPN Gateway 設定について説明します。"
   services="vpn-gateway"
   documentationCenter="na"
   authors="cherylmc"
   manager="carmonm"
   editor=""
   tags="azure-resource-manager,azure-service-management"/>
<tags 
   ms.service="vpn-gateway"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="09/21/2016"
   ms.author="cherylmc" />

# VPN Gateway の設定について

VPN Gateway 接続ソリューションは、複数のリソースの構成を使用して、仮想ネットワークとオンプレミスの間でネットワーク トラフィックを送信します。各リソースには、構成可能な設定が含まれています。どのように接続されるかは、リソースと設定の組み合わせによって決まります。

この記事の各セクションでは、**Resource Manager** デプロイメント モデルの VPN Gateway に関連するリソースと設定について説明します。接続トポロジ ダイアグラムを使用すると、利用可能な構成の表示に役立つ場合があります。各接続ソリューションの説明とトポロジ ダイアグラムについては、「[VPN Gateway について](vpn-gateway-about-vpngateways.md)」を参照してください。

## <a name="gwtype"></a>ゲートウェイの種類

各仮想ネットワークに配置できる各種類の仮想ネットワーク ゲートウェイは 1 つに限られています。仮想ネットワーク ゲートウェイを作成するときは、ゲートウェイの種類が構成に対して適切であることを確認する必要があります。

-GatewayType に使用できる値は次のとおりです。

- Vpn
- ExpressRoute

VPN Gateway では、`-GatewayType` を *Vpn* にする必要があります。

例:

	New-AzureRmVirtualNetworkGateway -Name vnetgw1 -ResourceGroupName testrg `
	-Location 'West US' -IpConfigurations $gwipconfig -GatewayType Vpn `
	-VpnType RouteBased
 

## <a name="gwsku"></a>ゲートウェイの SKU


[AZURE.INCLUDE [vpn-gateway-gwsku-include](../../includes/vpn-gateway-gwsku-include.md)]

**Azure Portal でゲートウェイの SKU を指定する**

Azure Portal を使用して Resource Manager の仮想ネットワーク ゲートウェイを作成する場合、既定では、仮想ネットワーク ゲートウェイは Standard SKU を使用して構成されます。現時点では、Azure Portal で Resource Manager デプロイメント モデルにその他の SKU を指定することはできません。ただし、ゲートウェイの作成後に、`Resize-AzureRmVirtualNetworkGateway` という PowerShell コマンドレットを使用してより強力な SKU (Basic/Standard から HighPerformance) にアップグレードすることができます。また、PowerShell を使用してゲートウェイの SKU サイズをダウングレードすることもできます。

**PowerShell を使用してゲートウェイの SKU を指定する**


次の PowerShell の例では、`-GatewaySku` が *Standard* として指定されています。

	New-AzureRmVirtualNetworkGateway -Name vnetgw1 -ResourceGroupName testrg `
	-Location 'West US' -IpConfigurations $gwipconfig -GatewaySku Standard `
	-GatewayType Vpn -VpnType RouteBased

**ゲートウェイの SKU を変更する**

ゲートウェイの SKU サイズを変更することができます。次の PowerShell サンプルでは、ゲートウェイ SKU のサイズを HighPerformance に変更しています。

	$gw = Get-AzureRmVirtualNetworkGateway -Name vnetgw1 -ResourceGroupName testrg
	Resize-AzureRmVirtualNetworkGateway -VirtualNetworkGateway $gw -GatewaySku HighPerformance

<br>


###  <a name="aggthroughput"></a>SKU とゲートウェイの種類別の予測される合計スループット


次の表は、ゲートウェイの種類、および予測される合計スループットを示したものです。この表は、リソース マネージャーとクラシック デプロイ モデルの両方に適用されます。

[AZURE.INCLUDE [vpn-gateway-table-gwtype-aggthroughput](../../includes/vpn-gateway-table-gwtype-aggtput-include.md)]


## <a name="connectiontype"></a>接続の種類

Resource Manager デプロイメント モデルの各構成では、仮想ネットワーク ゲートウェイの接続の種類を指定する必要があります。`-ConnectionType` に使用できる Resource Manager PowerShell 値は次のとおりです。

- IPsec
- Vnet2Vnet
- ExpressRoute
- VPNClient

次の PowerShell の例では、接続の種類 *IPsec* を必要とする S2S 接続を作成しています。

	New-AzureRmVirtualNetworkGatewayConnection -Name localtovon -ResourceGroupName testrg `
	-Location 'West US' -VirtualNetworkGateway1 $gateway1 -LocalNetworkGateway2 $local `
	-ConnectionType IPsec -RoutingWeight 10 -SharedKey 'abc123'


## <a name="vpntype"></a>VPN の種類

VPN Gateway 構成に対して仮想ネットワーク ゲートウェイを作成する場合は、VPN の種類を指定する必要があります。選択する VPN の種類は、作成する接続トポロジによって異なります。たとえば、P2S 接続の場合、VPN の種類は RouteBased である必要があります。VPN の種類は、使用しているハードウェアにも左右されます。S2S 構成では、VPN デバイスが必要です。一部の VPN デバイスでは、特定の VPN の種類のみがサポートされます。

選択した VPN の種類は、作成するソリューションのすべての接続要件を満たす必要があります。たとえば、同じ仮想ネットワークに対して、S2S VPN ゲートウェイ接続と P2S VPN ゲートウェイ接続を作成する必要がある場合は、VPN の種類として *RouteBased* を使用します。P2S では、VPN の種類が RouteBased である必要があるためです。VPN デバイスで RouteBased VPN 接続がサポートされていることも、確認する必要があります。

一度作成された仮想ネットワーク ゲートウェイの VPN の種類を変更することはできません。仮想ネットワーク ゲートウェイを削除し、新しいものを作成する必要があります。次の 2 つの種類の VPN があります。

[AZURE.INCLUDE [vpn-gateway-vpntype](../../includes/vpn-gateway-vpntype-include.md)]


次の PowerShell の例では、`-VpnType` を *RouteBased* に指定しています。ゲートウェイを作成するときは、-VpnType が構成に対して適切であることを確認する必要があります。

	New-AzureRmVirtualNetworkGateway -Name vnetgw1 -ResourceGroupName testrg `
	-Location 'West US' -IpConfigurations $gwipconfig `
	-GatewayType Vpn -VpnType RouteBased

##  <a name="requirements"></a>ゲートウェイの要件

[AZURE.INCLUDE [vpn-gateway-table-requirements](../../includes/vpn-gateway-table-requirements-include.md)]


## <a name="gwsub"></a>ゲートウェイ サブネット

仮想ネットワーク ゲートウェイを構成するには、まず VNet のゲートウェイ サブネットを作成する必要があります。ゲートウェイ サブネットを正常に動作させるには、*GatewaySubnet* という名前を付ける必要があります。この名前により、Azure はこのサブネットをゲートウェイに使用することを認識できます。

ゲートウェイ サブネットの最小サイズは、作成する構成に完全に依存します。/29 のような小さいゲートウェイ サブネットを作成できますが、/28 以上 (/28、/27、/26 など) のゲートウェイ サブネットを作成することをお勧めします。

ゲートウェイのサイズを大きめに作成しておくと、ゲートウェイ サイズの制限に達するのを防ぐことができます。たとえば、S2S 接続用の、サイズが /29 のゲートウェイ サブネットの仮想ネットワーク ゲートウェイを作成済みだとします。S2S と ExpressRoute が共存する構成を作成するには、最小サイズが /28 のゲートウェイ サブネットが必要になります。その構成を作成するには、ゲートウェイ サブネットを接続の最小要件となる /28 に変更しなければなりません。

次の Resource Manager PowerShell の例では、GatewaySubnet という名前のゲートウェイ サブネットを示しています。CIDR 表記で /27 を指定しています。これで既存のほとんどの構成で IP アドレスに十分対応できます。

	Add-AzureRmVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -AddressPrefix 10.0.3.0/27

>[AZURE.IMPORTANT] ゲートウェイ サブネットにネットワーク セキュリティ グループ (NSG) が適用されていないことを確認してください。接続に失敗する原因となります。


## <a name="lng"></a>ローカル ネットワーク ゲートウェイ

VPN Gateway 構成を作成する場合、ほとんどのローカル ネットワーク ゲートウェイはオンプレミスの場所になります。クラシック デプロイ モデルでは、ローカル ネットワーク ゲートウェイはローカル サイトとして参照されます。

ローカル ネットワーク ゲートウェイに名前とオンプレミス VPN デバイスのパブリック IP アドレスを指定し、オンプレミスの場所に存在するアドレスのプレフィックスを指定します。Azure は、ネットワーク トラフィックの宛先アドレスのプレフィックスを参照して、ローカル ネットワーク ゲートウェイに指定された構成を確認し、それに応じてパケットをルーティングします。また、VPN ゲートウェイ接続を使用している VNet 間の構成に対して、ローカル ネットワーク ゲートウェイを指定します。

次の PowerShell の例では、新しいローカル ネットワーク ゲートウェイを作成します。

	New-AzureRmLocalNetworkGateway -Name LocalSite -ResourceGroupName testrg `
	-Location 'West US' -GatewayIpAddress '23.99.221.164' -AddressPrefix '10.5.51.0/24'

場合によっては、ローカル ネットワーク ゲートウェイ設定を変更する必要があります。たとえば、アドレス範囲を追加または変更する場合や、VPN デバイスの IP アドレスが変更された場合などです。クラシック VNet の場合、[ローカル ネットワーク] ページのクラシック ポータルでこれらの設定を変更できます。Resource Manager の場合、「[PowerShell を使用したローカル ネットワーク ゲートウェイの設定の変更](vpn-gateway-modify-local-network-gateway.md)」を参照してください。

## <a name="resources"></a>REST API および PowerShell コマンドレット

VPN Gateway 構成に対して REST API および PowerShell コマンドレットを使用する場合のテクニカル リソースおよび特定構文の要件については、次のページを参照してください。

|**クラシック** | **Resource Manager**|
|-----|----|
|[PowerShell](https://msdn.microsoft.com/library/mt270335.aspx)|[PowerShell](https://msdn.microsoft.com/library/mt163510.aspx)|
|[REST API](https://msdn.microsoft.com/library/jj154113.aspx)|[REST API](https://msdn.microsoft.com/library/mt163859.aspx)|


## 次のステップ

使用可能な接続構成の詳細については、「[VPN Gateway について](vpn-gateway-about-vpngateways.md)」を参照してください。







 

<!---HONumber=AcomDC_0921_2016-->