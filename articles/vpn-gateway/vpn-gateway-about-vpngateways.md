<properties 
   pageTitle="VPN Gateway について| Microsoft Azure"
   description="Azure Virtual Network の VPN Gateway について説明します。"
   services="vpn-gateway"
   documentationCenter="na"
   authors="cherylmc"
   manager="carmonm"
   editor=""
   tags="azure-resource-manager,azure-service-management"/>
<tags 
   ms.service="vpn-gateway"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="07/20/2016"
   ms.author="cherylmc" />

# VPN Gateway について

VPN Gateway は、仮想ネットワークとオンプレミスとの間でネットワーク トラフィックの送信に使用される一連の設定です。この記事の各セクションでは、VPN Gateway に関連した設定について説明します。VPN Gateway は、サイト対サイト、ポイント対サイト、ExpressRoute の各接続で使用されます。また、Azure 内で複数の仮想ネットワーク間のトラフィック送信にも使用されます (VNet 間)。

VPN Gateway を仮想ネットワークに追加することで接続を作成することができます。それぞれの仮想ネットワークに追加できる VPN Gateway は 1 つだけであり、接続ごとの決まった構成手順が存在します。接続ダイアグラムについては、[Azure VPN Gateway 接続トポロジ](vpn-gateway-topology.md)に関するページを参照してください。

## <a name="gwsku"></a>ゲートウェイの SKU

VPN ゲートウェイを作成するときは、使用するゲートウェイの SKU を指定する必要があります。ゲートウェイの SKU は、ExpressRoute と VPN Gateway の両方のタイプに適用されます。料金はゲートウェイの SKU によって異なります。料金については、[VPN Gateway の料金](https://azure.microsoft.com/pricing/details/vpn-gateway/)のページをご確認ください。ExpressRoute の詳細については、「[ExpressRoute の技術概要](../expressroute/expressroute-introduction.md)」を参照してください。

次の 3 つの VPN Gateway の SKU があります。

- Basic
- Standard
- HighPerformance

次の例では、`-GatewaySku` を *Standard* に指定しています。

	New-AzureRmVirtualNetworkGateway -Name vnetgw1 -ResourceGroupName testrg -Location 'West US' -IpConfigurations $gwipconfig -GatewaySku Standard -GatewayType Vpn -VpnType RouteBased

###  <a name="aggthroughput"></a>SKU とゲートウェイの種類別の予測される合計スループット


次の表は、ゲートウェイの種類、および予測される合計スループットを示したものです。この表は、リソース マネージャーとクラシック デプロイ モデルの両方に適用されます。

[AZURE.INCLUDE [vpn-gateway-table-gwtype-aggthroughput](../../includes/vpn-gateway-table-gwtype-aggtput-include.md)]

## <a name="gwtype"></a>ゲートウェイの種類

ゲートウェイの種類は、ゲートウェイ自体の接続方法を指定するものであり、Resource Manager のデプロイ モデルに必要な構成設定です。VPN 用のルーティングの種類を指定する VPN の種類とゲートウェイの種類を混同しないでください。`-GatewayType` に使用できる値は次のとおりです。

- Vpn
- ExpressRoute


この Resource Manager デプロイメント モデルの例では、-GatewayType に *Vpn* を指定しています。ゲートウェイを作成するときは、ゲートウェイの種類が構成に対して適切であることを確認する必要があります。

	New-AzureRmVirtualNetworkGateway -Name vnetgw1 -ResourceGroupName testrg -Location 'West US' -IpConfigurations $gwipconfig -GatewayType Vpn -VpnType RouteBased

## <a name="connectiontype"></a>接続の種類

各構成には、特定の接続の種類が必要です。`-ConnectionType` に使用できる Resource Manager PowerShell 値は次のとおりです。

- IPsec
- Vnet2Vnet
- ExpressRoute
- VPNClient

次の例では、サイト間接続を作成しています。必要な接続の種類は "IPsec" です。

	New-AzureRmVirtualNetworkGatewayConnection -Name localtovon -ResourceGroupName testrg -Location 'West US' -VirtualNetworkGateway1 $gateway1 -LocalNetworkGateway2 $local -ConnectionType IPsec -RoutingWeight 10 -SharedKey 'abc123'

## <a name="vpntype"></a>VPN の種類

各構成を適切に機能させるためには、特定の VPN の種類が必要です。同じ VNet に対してサイト間接続とポイント対サイト接続を作成するなど、2 つの構成を組み合わせる場合は、両方の接続の要件を満たす VPN の種類を使用する必要があります。

ポイント対サイト接続とサイト間接続を共存させるときは、Azure Resource Manager のデプロイ モデルの場合はルート ベースの VPN の種類、クラシック デプロイ モデルの場合は動的ゲートウェイを使用する必要があります。

構成を作成するときに、接続に必要な VPN の種類を選択します。

次の 2 つの種類の VPN があります。

[AZURE.INCLUDE [vpn-gateway-vpntype](../../includes/vpn-gateway-vpntype-include.md)]

この Resource Manager デプロイメント モデルの例では、`-VpnType` に *RouteBased* を指定しています。ゲートウェイを作成するときは、-VpnType が構成に対して適切であることを確認する必要があります。

	New-AzureRmVirtualNetworkGateway -Name vnetgw1 -ResourceGroupName testrg -Location 'West US' -IpConfigurations $gwipconfig -GatewayType Vpn -VpnType RouteBased

##  <a name="requirements"></a>ゲートウェイの要件

[AZURE.INCLUDE [vpn-gateway-table-requirements](../../includes/vpn-gateway-table-requirements-include.md)]


## <a name="gwsub"></a>ゲートウェイ サブネット

VPN ゲートウェイを構成するには、まず VNet のゲートウェイ サブネットを作成する必要があります。ゲートウェイ サブネットを正常に動作させるには、*GatewaySubnet* という名前を付ける必要があります。この名前により、Azure はこのサブネットをゲートウェイに使用することを認識できます。<BR>クラシック ポータルを使用している場合、ゲートウェイ サブネットにはポータル インターフェイスで自動的に *Gateway* という名前が付けられます。これは、クラシック ポータルでゲートウェイ サブネットを表示している場合に限られます。この場合、サブネットが *GatewaySubnet* として Azure に実際に作成され、Azure ポータルと PowerShell ではこのように表示されます。

ゲートウェイ サブネットの最小サイズは、作成する構成に完全に依存します。構成によっては /29 のような小さいゲートウェイ サブネットを作成できますが、/28 以上 (/28、/27、/26 など) のゲートウェイ サブネットを作成することをお勧めします。

ゲートウェイのサイズを大きめに作成しておくと、ゲートウェイ サイズの制限に達するのを防ぐことができます。たとえば、ゲートウェイ サブネットのサイズが /29 のゲートウェイを作成していた場合に、サイト間と ExpressRoute の共存を構成するには、ゲートウェイとゲートウェイ サブネットを削除し、/28 以上のゲートウェイ サブネットを作成してから、ゲートウェイを再作成する必要があります。

最初から大きめのサイズでゲートウェイ サブネットを作成しておくと、後でネットワーク環境に新しい構成機能を追加する際に時間を節約できます。

次の例は、GatewaySubnet という名前のゲートウェイ サブネットを示しています。CIDR 表記で /27 を指定していますが、これは現時点で存在するほとんどの構成の IP アドレスに十分対応できます。

	Add-AzureRmVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -AddressPrefix 10.0.3.0/27

>[AZURE.IMPORTANT] GatewaySubnet にネットワーク セキュリティ グループ (NSG) が適用されていないことを確認してください。接続に失敗する原因となります。



## <a name="lng"></a>ローカル ネットワーク ゲートウェイ

ローカル ネットワーク ゲートウェイは通常、オンプレミスの場所を指します。クラシック デプロイ モデルでは、ローカル ネットワーク ゲートウェイはローカル サイトとして参照されます。ローカル ネットワーク ゲートウェイに名前とオンプレミス VPN デバイスのパブリック IP アドレスを指定し、オンプレミスの場所に存在するアドレスのプレフィックスを指定します。Azure は、ネットワーク トラフィックの宛先アドレスのプレフィックスを参照して、ローカル ネットワーク ゲートウェイに指定された構成を確認し、それに応じてパケットをルーティングします。これらのアドレス プレフィックスは、必要に応じて変更できます。


### アドレス プレフィックスの変更 - Resource Manager

アドレス プレフィックスを変更する際の手順は、VPN ゲートウェイを既に作成しているかどうかによって異なります。[ローカル ネットワーク ゲートウェイのアドレスのプレフィックスの変更](vpn-gateway-create-site-to-site-rm-powershell.md#modify)に関するセクションを参照してください。

次の例では、MyOnPremiseWest という名前のローカル ネットワーク ゲートウェイが指定されており、2 つの IP アドレスのプレフィックスが含まれることがわかります。

	New-AzureRmLocalNetworkGateway -Name MyOnPremisesWest -ResourceGroupName testrg -Location 'West US' -GatewayIpAddress '23.99.221.164' -AddressPrefix @('10.0.0.0/24','20.0.0.0/24')	

### アドレス プレフィックスの変更 - クラシック デプロイ

クラシック デプロイ モデルを使用していてローカル サイトを変更する必要がある場合、クラシック ポータルの [ローカル ネットワークの構成] ページを使用するか、ネットワーク構成ファイル NETCFG.XML を直接変更できます。



## 次のステップ

構成の計画と設計に進む前に、詳細について「[VPN Gateway に関する FAQ](vpn-gateway-vpn-faq.md)」を参照してください。





 

<!---HONumber=AcomDC_0720_2016-->