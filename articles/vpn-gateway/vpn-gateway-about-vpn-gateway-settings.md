<properties 
   pageTitle="VPN Gateway の設定について | Microsoft Azure"
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
   ms.date="08/17/2016"
   ms.author="cherylmc" />

# VPN Gateway の設定について

VPN Gateway は、仮想ネットワークとオンプレミスとの間でネットワーク トラフィックの送信に使用される一連の設定です。Azure 内の VNET 間でトラフィックを送信するのに VPN Gateway を使用することもできます。この記事の各セクションでは、VPN Gateway に関連した設定について説明します。

接続図を使用すると、利用可能な構成を表示できます。「[Azure VPN Gateway 接続](vpn-gateway-topology.md)」セクションに記載される各構成のデプロイ方法の図を参照してください。


## <a name="gwsku"></a>ゲートウェイの SKU

VPN ゲートウェイを作成するときは、使用するゲートウェイの SKU を指定する必要があります。ゲートウェイの SKU は、ExpressRoute と VPN Gateway の両方のタイプに適用されます。料金はゲートウェイの SKU によって異なります。料金については、[VPN Gateway の料金](https://azure.microsoft.com/pricing/details/vpn-gateway/)のページをご確認ください。ExpressRoute の詳細については、「[ExpressRoute の技術概要](../expressroute/expressroute-introduction.md)」を参照してください。

次の 3 つの VPN Gateway の SKU があります。

- 基本
- Standard
- HighPerformance

次の PowerShell の例では、`-GatewaySku` が *Standard* として指定されています。

	New-AzureRmVirtualNetworkGateway -Name vnetgw1 -ResourceGroupName testrg `
	-Location 'West US' -IpConfigurations $gwipconfig -GatewaySku Standard `
	-GatewayType Vpn -VpnType RouteBased


###  <a name="aggthroughput"></a>SKU とゲートウェイの種類別の予測される合計スループット


次の表は、ゲートウェイの種類、および予測される合計スループットを示したものです。この表は、リソース マネージャーとクラシック デプロイ モデルの両方に適用されます。

[AZURE.INCLUDE [vpn-gateway-table-gwtype-aggthroughput](../../includes/vpn-gateway-table-gwtype-aggtput-include.md)]

## <a name="gwtype"></a>ゲートウェイの種類

ゲートウェイの種類は、ゲートウェイ自体の接続方法を指定するものであり、Resource Manager デプロイメント モデルに必要な構成設定です。VPN 用のルーティングの種類を指定する VPN の種類とゲートウェイの種類を混同しないでください。`-GatewayType` に使用できる値は次のとおりです。

- Vpn
- ExpressRoute


この Resource Manager デプロイメント モデルの PowerShell の例では、-GatewayType に *Vpn* を指定しています。ゲートウェイを作成するときは、ゲートウェイの種類が構成に対して適切であることを確認する必要があります。

	New-AzureRmVirtualNetworkGateway -Name vnetgw1 -ResourceGroupName testrg `
	-Location 'West US' -IpConfigurations $gwipconfig -GatewayType Vpn `
	-VpnType RouteBased

## <a name="connectiontype"></a>接続の種類

各構成には、特定の接続の種類が必要です。`-ConnectionType` に使用できる Resource Manager PowerShell 値は次のとおりです。

- IPsec
- Vnet2Vnet
- ExpressRoute
- VPNClient

次の PowerShell の例では、接続の種類 "IPsec" を必要とする S2S 接続を作成しています。

	New-AzureRmVirtualNetworkGatewayConnection -Name localtovon -ResourceGroupName testrg `
	-Location 'West US' -VirtualNetworkGateway1 $gateway1 -LocalNetworkGateway2 $local `
	-ConnectionType IPsec -RoutingWeight 10 -SharedKey 'abc123'


## <a name="vpntype"></a>VPN の種類

各構成を適切に機能させるためには、特定の VPN の種類が必要です。構成を作成するときに、接続に必要な VPN の種類を選択します。

たとえば、サイト間 (S2S) とポイント対サイト (P2S) の両方を使用して VNet に接続する場合は、両方の構成の接続要件を満たす VPN の種類を使用する必要があります。P2S 接続ではルート ベースの VPN の種類 (動的ルーティング ゲートウェイ) が必要である一方、S2S 接続ではポリシー ベースの VPN の種類がサポートされる場合があります。これはつまり、ポリシー ベースの VPN の種類 (静的ルーティング) を使用する S2S 接続を既に利用している場合、P2S をサポートするにはルート ベースの VPN の種類 (動的ルーティング) を使用するゲートウェイを再作成する必要があるということです。

次の 2 つの種類の VPN があります。

[AZURE.INCLUDE [vpn-gateway-vpntype](../../includes/vpn-gateway-vpntype-include.md)]



この Resource Manager デプロイメント モデルの PowerShell の例では、`-VpnType` に *RouteBased* を指定しています。ゲートウェイを作成するときは、-VpnType が構成に対して適切であることを確認する必要があります。

	New-AzureRmVirtualNetworkGateway -Name vnetgw1 -ResourceGroupName testrg `
	-Location 'West US' -IpConfigurations $gwipconfig `
	-GatewayType Vpn -VpnType RouteBased

##  <a name="requirements"></a>ゲートウェイの要件

[AZURE.INCLUDE [vpn-gateway-table-requirements](../../includes/vpn-gateway-table-requirements-include.md)]


## <a name="gwsub"></a>ゲートウェイ サブネット

VPN ゲートウェイを構成するには、まず VNet のゲートウェイ サブネットを作成する必要があります。ゲートウェイ サブネットを正常に動作させるには、*GatewaySubnet* という名前を付ける必要があります。この名前により、Azure はこのサブネットをゲートウェイに使用することを認識できます。<BR>クラシック ポータルを使用している場合、ゲートウェイ サブネットにはポータル インターフェイスで自動的に *Gateway* という名前が付けられます。これは、クラシック ポータルでゲートウェイ サブネットを表示している場合に限られます。この場合、サブネットが値 *GatewaySubnet* を使用して実際に作成され、Azure ポータルと PowerShell ではこのように表示されます。

ゲートウェイ サブネットの最小サイズは、作成する構成に完全に依存します。/29 のような小さいゲートウェイ サブネットを作成できますが、/28 以上 (/28、/27、/26 など) のゲートウェイ サブネットを作成することをお勧めします。

ゲートウェイのサイズを大きめに作成しておくと、ゲートウェイ サイズの制限に達するのを防ぐことができます。たとえば、S2S 接続用の、サイズが /29 のゲートウェイ サブネットの仮想ネットワーク ゲートウェイを作成済みだとします。S2S と ExpressRoute が共存する構成を作成するには、最小サイズが /28 のゲートウェイ サブネットが必要になります。その構成を作成するには、ゲートウェイ サブネットを接続の最小要件となる /28 に変更しなければなりません。

次の PowerShell の例では、GatewaySubnet という名前のゲートウェイ サブネットを示しています。CIDR 表記で /27 を指定しています。これで既存のほとんどの構成で IP アドレスに十分対応できます。

	Add-AzureRmVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -AddressPrefix 10.0.3.0/27

>[AZURE.IMPORTANT] ゲートウェイ サブネットにネットワーク セキュリティ グループ (NSG) が適用されていないことを確認してください。接続に失敗する原因となります。


## <a name="lng"></a>ローカル ネットワーク ゲートウェイ

ローカル ネットワーク ゲートウェイは通常、オンプレミスの場所を指します。クラシック デプロイ モデルでは、ローカル ネットワーク ゲートウェイはローカル サイトとして参照されます。ローカル ネットワーク ゲートウェイに名前とオンプレミス VPN デバイスのパブリック IP アドレスを指定し、オンプレミスの場所に存在するアドレスのプレフィックスを指定します。Azure は、ネットワーク トラフィックの宛先アドレスのプレフィックスを参照して、ローカル ネットワーク ゲートウェイに指定された構成を確認し、それに応じてパケットをルーティングします。

場合によっては、ローカル ネットワーク ゲートウェイ設定を変更する必要があります。たとえば、アドレス範囲を追加または変更する場合や、VPN デバイスの IP アドレスが変更された場合などです。クラシック VNet の場合、[ローカル ネットワーク] ページのクラシック ポータルでこれらの設定を変更できます。Resource Manager の場合、「[PowerShell を使用したローカル ネットワーク ゲートウェイの設定の変更](vpn-gateway-modify-local-network-gateway.md)」を参照してください。

## <a name="resources"></a>REST API と PowerShell

REST API と PowerShell を使用する場合のテクニカル リソースおよび特定構文の要件については、次のページを参照してください。

|**クラシック** | **Resource Manager**|
|-----|----|
|[PowerShell](https://msdn.microsoft.com/library/mt270335.aspx)|[PowerShell](https://msdn.microsoft.com/library/mt163510.aspx)|
|[REST API](https://msdn.microsoft.com/library/jj154113.aspx)|[REST API](https://msdn.microsoft.com/library/mt163859.aspx)|


## 次のステップ

使用可能な接続構成の詳細については、「[VPN Gateway について](vpn-gateway-topology.md)」を参照してください。







 

<!---HONumber=AcomDC_0824_2016-->