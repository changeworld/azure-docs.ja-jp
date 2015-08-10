<properties
   pageTitle="Azure リソース マネージャーと PowerShell を使用してサイト間 VPN 接続で仮想ネットワークを作成する | Microsoft Azure"
   description="Azure リソース マネージャーと PowerShell を使用して仮想ネットワークからオンプレミスの場所へのサイト間 VPN 接続を作成する"
   services="vpn-gateway"
   documentationCenter="na"
   authors="cherylmc"
   manager="carolz"
   editor=""
   tags="azure-resource-manager"/>

<tags
   ms.service="vpn-gateway"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="07/28/2015"
   ms.author="cherylmc"/>

# Azure リソース マネージャーと PowerShell を使用してサイト間 VPN 接続で仮想ネットワークを作成する

> [AZURE.SELECTOR]
- [Azure Portal](vpn-gateway-site-to-site-create.md)
- [PowerShell - Resource Manager](vpn-gateway-create-site-to-site-rm-powershell.md)


このトピックでは、Azure リソース マネージャーの仮想ネットワークと、オンプレミス ネットワークに対するサイト間 VPN 接続を作成する手順について説明します。

現在、Azure には 2 つのデプロイ モデルがあります。従来のデプロイ モデルと、Azure リソース マネージャーのデプロイ モデルです。サイト間の設定は、仮想ネットワークのデプロイに使用されたモデルによって変わります。次の手順は、リソース マネージャーに適用されます。従来のデプロイ モデルを使用してサイト間 VPN ゲートウェイ接続を作成する場合は、「[管理ポータルでサイト間 VPN 接続を作成する](vpn-gateway-site-to-site-create.md)」を参照してください。


## 開始する前に

開始する前に、以下がそろっていることを確認します。

- 互換性のある VPN デバイス (およびデバイスを構成できる人員)。「[VPN デバイスについて](vpn-gateway-vpn-devices.md)」を参照してください。
- VPN デバイスの外部接続用パブリック IP アドレス。この IP アドレスを NAT の内側に割り当てることはできません。
- 最新バージョンの Azure PowerShell コマンドレット。[ダウンロード ページ](http://azure.microsoft.com/downloads/)の Windows PowerShell セクションから最新バージョンをダウンロードしてインストールできます。 
- Azure サブスクリプション。Azure サブスクリプションを持っていない場合は、[MSDN サブスクライバーの特典](http://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/)を有効にするか、[無料試用版](http://azure.microsoft.com/pricing/free-trial/)にサインアップしてください。
	

## サブスクリプションへの接続 


PowerShell コンソールを開き、Azure リソース マネージャー モードに切り替えます。接続するには、次のサンプルを参照してください。

		Add-AzureAccount

Select-AzureSubscription を実行して、使用するサブスクリプションに接続します。

		Select-AzureSubscription "yoursubscription"

次に、ARM モードに切り替えます。ARM モードを使用できるモードに切り替わります。

		Switch-AzureMode -Name AzureResourceManager


## 仮想ネットワークとゲートウェイ サブネットを作成する

- 仮想ネットワークとゲートウェイ サブネットが既にある場合は、「[ローカル サイトを追加する](#add-your-local-site)」に進むことができます。 
- 仮想ネットワークがあり、VNet にゲートウェイ サブネットを追加する場合は、「[VNet にゲートウェイ サブネットを追加する](#gatewaysubnet)」を参照してください。

### 仮想ネットワークとゲートウェイ サブネットを作成するには

仮想ネットワークとゲートウェイ サブネットを作成するには、次のサンプルを使用してください。サンプル値は実際の値に変更してください。

まず、リソース グループを作成します。

	
		New-AzureResourceGroup -Name testrg -Location 'West US'

次に、仮想ネットワークを作成します。次のサンプルでは、*testvnet* という仮想ネットワークと、*GatewaySubnet* と *Subnet1* という 2 つのサブネットを作成します。正確に *GatewaySubnet* というサブネットを作成する必要があります。別の名前にすると、接続の構成は失敗します。

		$subnet1 = New-AzureVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -AddressPrefix 10.0.0.0/28
		$subnet2 = New-AzureVirtualNetworkSubnetConfig -Name 'Subnet1' -AddressPrefix '10.0.1.0/28'
		New-AzurevirtualNetwork -Name testvnet -ResourceGroupName testrg -Location 'West US' -AddressPrefix 10.0.0.0/16 -Subnet $subnet1, $subnet2


### <a name="gatewaysubnet"></a>VNet にゲートウェイ サブネットを追加するには

既存の仮想ネットワークがあり、仮想ネットワークにゲートウェイ サブネットを追加する場合は、次のサンプルを使用してゲートウェイ サブネットを作成できます。ゲートウェイ サブネットには、必ず 'GatewaySubnet' という名前を付けてください。別の名前にすると、VPN の構成は期待どおりに機能しません。


	
		$vnet = Get-AzureVirtualNetwork -ResourceGroupName testrg -Name testvnet
		Add-AzureVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -AddressPrefix 10.0.3.0/28 -VirtualNetwork $vnet
		Set-AzureVirtualNetwork -VirtualNetwork $vnet

## ローカル サイトを追加する

通常、仮想ネットワークでは、*ローカル サイト*とはオンプレミスの場所を指します。Azure から参照できるように、ローカル サイトに名前を付けます。

また、ローカル サイトのアドレス空間のプレフィックスも指定します。指定した IP アドレス プレフィックスは、Azure がローカル サイトに送信するトラフィックを特定するときに使用されます。つまり、ローカル サイトに関連付ける各アドレス プレフィックスを指定する必要があります。オンプレミス ネットワークが変わる場合でも、アドレス プレフィックスを簡単に更新できます。次の PowerShell サンプルを使用して、ローカル サイトを指定します。

	
- *GatewayIPAddress* は、オンプレミス VPN デバイスの IP アドレスです。VPN デバイスを NAT の内側に配置することはできません。 
- *AddressPrefix* は、オンプレミス アドレス空間です。

次の例を使用してローカル サイトを追加します。

		New-AzureLocalNetworkGateway -Name LocalSite -ResourceGroupName testrg -Location 'West US' -GatewayIpAddress '23.99.221.164' -AddressPrefix '10.5.51.0/24'

## VNet ゲートウェイのパブリック IP アドレスを要求する

次に、Azure VNet VPN ゲートウェイに割り当てるパブリック IP アドレスを要求します。これは VPN デバイスに割り当てられているものと同じ IP アドレスではなく、Azure VPN ゲートウェイに割り当てられている IP アドレスです。使用する IP アドレスは指定できません。IP アドレスはゲートウェイに動的に割り当てられます。ゲートウェイに接続するオンプレミス VPN デバイスを構成するときに、この IP アドレスを使用します。

次の PowerShell サンプルを使用してください。このアドレスの割り当て方法は動的にする必要があります。

		$gwpip= New-AzurePublicIpAddress -Name gwpip -ResourceGroupName testrg -Location 'West US' -AllocationMethod Dynamic

## ゲートウェイ IP アドレス指定の構成を作成する

ゲートウェイの構成で、使用するサブネットとパブリック IP アドレスを定義します。次のサンプルを使用して、ゲートウェイの構成を作成します。


		$vnet = Get-AzureVirtualNetwork -Name testvnet -ResourceGroupName testrg
		$subnet = Get-AzureVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -VirtualNetwork $vnet
		$gwipconfig = New-AzureVirtualNetworkGatewayIpConfig -Name gwipconfig1 -SubnetId $subnet.Id -PublicIpAddressId $gwpip.Id 


## ゲートウェイを作成する

この手順では、仮想ネットワーク ゲートウェイを作成します。次の値を使用します。

- ゲートウェイの種類は *Vpn* です。
- VpnType には、RouteBased\* (ドキュメントによっては動的ゲートウェイと呼ばれます) または *Policy Based* (ドキュメントによっては静的ゲートウェイと呼ばれます) を指定できます。VPN ゲートウェイの種類については、「[VPN ゲートウェイについて](vpn-gateway-about-vpngateways.md)」を参照してください。 	

		New-AzureVirtualNetworkGateway -Name vnetgw1 -ResourceGroupName testrg -Location 'West US' -IpConfigurations $gwipconfig -GatewayType Vpn -VpnType RouteBased


## VPN デバイスの構成

オンプレミス VPN デバイスを構成するには、この時点で仮想ネットワーク ゲートウェイのパブリック IP アドレスが必要です。具体的な構成方法については、お使いのデバイスの製造元の情報を参照してください。また、詳細については「[VPN デバイス](http://go.microsoft.com/fwlink/p/?linkid=615099)」を参照してください。

仮想ネットワーク ゲートウェイのパブリック IP アドレスを検索するには、次のサンプルを使用します。

	Get-AzurePublicIpAddress -Name gwpip -ResourceGroupName testrg

## VPN 接続を作成する

次に、仮想ネットワーク ゲートウェイと VPN デバイス間にサイト間 VPN 接続を作成します。サンプルの値は必ず実際の値に変更してください。共有キーは、VPN デバイスの構成に使用したものと同じ値にする必要があります。

		$gateway1 = Get-AzureVirtualNetworkGateway -Name vnetgw1 -ResourceGroupName testrg
		$local = Get-AzureLocalNetworkGateway -Name LocalSite -ResourceGroupName testrg

		New-AzureVirtualNetworkGatewayConnection -Name localtovon -ResourceGroupName testrg -Location 'West US' -VirtualNetworkGateway1 $gateway1 -LocalNetworkGateway2 $local -ConnectionType IPsec -RoutingWeight 10 -SharedKey 'abc123'

数分後に接続が確立されます。この時点で、リソース マネージャーを使用して作成されたサイト間 VPN 接続はポータルに表示されません。


## 次のステップ

仮想ネットワークに仮想マシンを追加します。[仮想マシンの作成](../virtual-machines/virtual-machines-windows-tutorial.md)

<!---HONumber=July15_HO5-->