<properties
   pageTitle="Azure リソース マネージャーと PowerShell を使用してサイト間 VPN 接続で仮想ネットワークを作成する | Microsoft Azure"
   description="この記事では、リソース マネージャ モデルを使用して VNet を作成し、サイト間 VPN Gateway 接続を使用してローカルのオンプレミス ネットワークにこれを接続する方法を説明します。既存のローカル サイトの IP アドレスのプレフィックスを変更する追加の手順が含まれています。"
   services="vpn-gateway"
   documentationCenter="na"
   authors="cherylmc"
   manager="carolz"
   editor=""
   tags="azure-resource-manager"/>

<tags
   ms.service="vpn-gateway"
   ms.devlang="na"
   ms.topic="hero-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="10/13/2015"
   ms.author="cherylmc"/>

# PowerShell を使用してサイト間 VPN 接続で仮想ネットワークを作成する

> [AZURE.SELECTOR]
- [Azure Portal](vpn-gateway-site-to-site-create.md)
- [PowerShell - Resource Manager](vpn-gateway-create-site-to-site-rm-powershell.md)

この記事では、Azure リソース マネージャーのデプロイ モデルを使用して、仮想ネットワークと、オンプレミス ネットワークに対するサイト間 VPN 接続を作成する手順について説明します。上にあるタブを使用して、デプロイ モデルとデプロイ ツールに関する記事を選択できます。

>[AZURE.NOTE]Azure は現在、2 つのデプロイ モデル (リソース マネージャーおよび従来のモデル) で使用できることを理解しておくことが重要です。構成を開始する前に、デプロイ モデルとツールについて理解しておくようにしてください。デプロイ モデルについては、「[Azure デプロイ モデル](../azure-classic-rm.md)」をご覧ください。

## 作業を開始する前に

構成を開始する前に、以下がそろっていることを確認します。

- 互換性のある VPN デバイスおよびデバイスを構成できる人員。「[VPN デバイスについて](vpn-gateway-about-vpn-devices.md)」を参照してください。

- VPN デバイスの外部接続用パブリック IP アドレス。この IP アドレスを NAT の内側に割り当てることはできません。

>[AZURE.IMPORTANT]VPN デバイスの構成に習熟していないか、またはオンプレミス ネットワーク構成の IP アドレス範囲を把握していない場合は、詳細な情報を把握している担当者と協力して作業を行ってください。
	
- Azure サブスクリプション。Azure サブスクリプションを持っていない場合は、[MSDN サブスクライバーの特典](http://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/)を有効にするか、[無料試用版](http://azure.microsoft.com/pricing/free-trial/)にサインアップしてください。

- 最新バージョンの Azure PowerShell コマンドレット。最新バージョンは、[ダウンロード ページ](http://azure.microsoft.com/downloads/)の Windows PowerShell のセクションからダウンロードしてインストールできます。この記事は、Azure PowerShell *0.9.8* 向けに書かれています。

>[AZURE.NOTE]ミッション クリティカルなアプリを実行している場合は、Azure PowerShell 0.9.8 を使用し続けてください。ほとんどの場合、2 つのバージョンの唯一の違いは、1.0 Preview のコマンドレット名は {verb}-AzureRm{noun} というパターンになっているものの、0.9.8 の名前には Rm が含まれないという点です。たとえば、New-AzureResourceGroup は New-AzureRmResourceGroup になっています。Azure PowerShell 1.0 Preview については、この[ブログ記事](https://azure.microsoft.com/blog/azps-1-0-pre/)を参照してください。Azure PowerShell 1.0 Preview のコマンドレットの詳細については、[Azure リソース マネージャーのコマンドレット](https://msdn.microsoft.com/library/mt125356.aspx)に関するページを参照してください。


## 1\.サブスクリプションへの接続 


PowerShell コンソールを開き、アカウントに接続します。

**注:** 以下の手順は Azure PowerShell コマンドレットのバージョン 0.9.8 に基づいています。

接続するには、次のサンプルを参照してください。

		Add-AzureAccount

複数のサブスクリプションがある場合、*Select-AzureSubscription* を実行して、使用するサブスクリプションに接続します。

		Select-AzureSubscription "yoursubscription"

次に、Azure リソース マネージャー モードに切り替えます。
		
		Switch-AzureMode -Name AzureResourceManager

## 2\.仮想ネットワークとゲートウェイ サブネットを作成する

- 仮想ネットワークとゲートウェイ サブネットが既にある場合は、「**手順 3. ローカル サイトを追加する**」に進むことができます。 
- 仮想ネットワークが既にあり、VNet にゲートウェイ サブネットを追加する場合は、「[VNet にゲートウェイ サブネットを追加する](#gatewaysubnet)」を参照してください。

### 仮想ネットワークとゲートウェイ サブネットを作成するには

仮想ネットワークとゲートウェイ サブネットを作成するには、次のサンプルを使用してください。サンプル値は実際の値に変更してください。

まず、リソース グループを作成します。

	
		New-AzureResourceGroup -Name testrg -Location 'West US'

次に、仮想ネットワークを作成します。指定したアドレス空間がオンプレミス ネットワーク内に存在するあらゆるアドレス空間と重複していないことを確認します。

次のサンプルでは、*testvnet* という仮想ネットワークに加えて、*GatewaySubnet* と *Subnet1* という 2 つのサブネットを作成します。正確に *GatewaySubnet* というサブネットを作成する必要があります。別の名前にすると、接続の構成は失敗します。

		$subnet1 = New-AzureVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -AddressPrefix 10.0.0.0/28
		$subnet2 = New-AzureVirtualNetworkSubnetConfig -Name 'Subnet1' -AddressPrefix '10.0.1.0/28'
		New-AzureVirtualNetwork -Name testvnet -ResourceGroupName testrg -Location 'West US' -AddressPrefix 10.0.0.0/16 -Subnet $subnet1, $subnet2

### <a name="gatewaysubnet"></a>VNet にゲートウェイ サブネットを追加するには (省略可能)

この手順は、VNet にゲートウェイ サブネットを追加する必要がある場合にのみ必要です。

既存の仮想ネットワークがあり、仮想ネットワークにゲートウェイ サブネットを追加する場合は、次のサンプルを使用してゲートウェイ サブネットを作成できます。ゲートウェイ サブネットには、必ず 'GatewaySubnet' という名前を付けてください。別の名前を付けてサブネットを作成しても、Azure はそれをゲートウェイ サブネットとして認識しません。

		$vnet = Get-AzureVirtualNetwork -ResourceGroupName testrg -Name testvnet
		Add-AzureVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -AddressPrefix 10.0.3.0/28 -VirtualNetwork $vnet

次に、構成を設定します。

		Set-AzureVirtualNetwork -VirtualNetwork $vnet

## 3\.ローカル サイトを追加する

通常、仮想ネットワークでは、*ローカル サイト*とはオンプレミスの場所を指します。Azure から参照できるように、ローカル サイトに名前を付けます。

また、ローカル サイトのアドレス空間のプレフィックスも指定します。指定した IP アドレス プレフィックスは、Azure がローカル サイトに送信するトラフィックを特定するときに使用されます。つまり、ローカル サイトに関連付ける各アドレス プレフィックスを指定する必要があります。オンプレミス ネットワークが変わる場合でも、アドレス プレフィックスを簡単に更新できます。

PowerShell の例を使用する場合は、以下の点に注意してください。
	
- *GatewayIPAddress* は、オンプレミス VPN デバイスの IP アドレスです。VPN デバイスを NAT の内側に配置することはできません。 
- *AddressPrefix* は、オンプレミス アドレス空間です。

1 つのアドレス プレフィックスを含むローカル サイトを追加するには

		New-AzureLocalNetworkGateway -Name LocalSite -ResourceGroupName testrg -Location 'West US' -GatewayIpAddress '23.99.221.164' -AddressPrefix '10.5.51.0/24'

複数のアドレス プレフィックスを含むローカル サイトを追加するには

		New-AzureLocalNetworkGateway -Name LocalSite -ResourceGroupName testrg -Location 'West US' -GatewayIpAddress '23.99.221.164' -AddressPrefix @('10.0.0.0/24','20.0.0.0/24')

### ローカル サイト用の IP アドレスのプレフィックスを変更するには

ローカル サイト プレフィックスは変わる場合があります。IP アドレス プレフィックスを変更する手順は、VPN ゲートウェイの接続を作成したかどうかによって異なります。[ローカル サイト用の IP アドレスのプレフィックスの変更](#to-modify-ip-address-prefixes-for-a-local-site)に関するセクションをご覧ください。


## 4\.ゲートウェイのパブリック IP アドレスを要求する

次に、Azure VNet VPN ゲートウェイに割り当てるパブリック IP アドレスを要求します。これは VPN デバイスに割り当てられているものと同じ IP アドレスではなく、Azure VPN ゲートウェイに割り当てられている IP アドレスです。使用する IP アドレスは指定できません。IP アドレスはゲートウェイに動的に割り当てられます。ゲートウェイに接続するオンプレミス VPN デバイスを構成するときに、この IP アドレスを使用します。

次の PowerShell サンプルを使用してください。このアドレスの割り当て方法は動的にする必要があります。

		$gwpip= New-AzurePublicIpAddress -Name gwpip -ResourceGroupName testrg -Location 'West US' -AllocationMethod Dynamic

## 5\.ゲートウェイ IP アドレス指定の構成を作成する

ゲートウェイの構成で、使用するサブネットとパブリック IP アドレスを定義します。次のサンプルを使用して、ゲートウェイの構成を作成します。


		$vnet = Get-AzureVirtualNetwork -Name testvnet -ResourceGroupName testrg
		$subnet = Get-AzureVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -VirtualNetwork $vnet
		$gwipconfig = New-AzureVirtualNetworkGatewayIpConfig -Name gwipconfig1 -SubnetId $subnet.Id -PublicIpAddressId $gwpip.Id 

## 6\.ゲートウェイを作成する

この手順では、仮想ネットワーク ゲートウェイを作成します。ゲートウェイの作成が完了するまで、少し時間がかかります。

次の値を使用します。

- ゲートウェイの種類は *Vpn* です。
- VpnType には、RouteBased* (ドキュメントによっては動的ゲートウェイと呼ばれます) または *Policy Based* (ドキュメントによっては静的ゲートウェイと呼ばれます) を指定できます。VPN ゲートウェイの種類については、「[VPN ゲートウェイについて](vpn-gateway-about-vpngateways.md)」をご覧ください。 	

		New-AzureVirtualNetworkGateway -Name vnetgw1 -ResourceGroupName testrg -Location 'West US' -IpConfigurations $gwipconfig -GatewayType Vpn -VpnType RouteBased

## 7\.VPN デバイスの構成

オンプレミス VPN デバイスを構成するには、この時点で仮想ネットワーク ゲートウェイのパブリック IP アドレスが必要です。具体的な構成方法については、お使いのデバイスの製造元の情報を参照してください。また、詳細については、[VPN デバイス](http://go.microsoft.com/fwlink/p/?linkid=615099)に関するページをご覧ください。

仮想ネットワーク ゲートウェイのパブリック IP アドレスを検索するには、次のサンプルを使用します。

	Get-AzurePublicIpAddress -Name gwpip -ResourceGroupName testrg

## 8\.VPN 接続を作成する

次に、仮想ネットワーク ゲートウェイと VPN デバイス間にサイト間 VPN 接続を作成します。サンプルの値は必ず実際の値に変更してください。共有キーは、VPN デバイスの構成に使用したものと同じ値にする必要があります。

		$gateway1 = Get-AzureVirtualNetworkGateway -Name vnetgw1 -ResourceGroupName testrg
		$local = Get-AzureLocalNetworkGateway -Name LocalSite -ResourceGroupName testrg

		New-AzureVirtualNetworkGatewayConnection -Name localtovon -ResourceGroupName testrg -Location 'West US' -VirtualNetworkGateway1 $gateway1 -LocalNetworkGateway2 $local -ConnectionType IPsec -RoutingWeight 10 -SharedKey 'abc123'

しばらくすると、接続が確立されます。

## 9\.VPN 接続の検証

この時点では、リソース マネージャーを使用して作成されたサイト間 VPN 接続はプレビュー ポータルに表示されません。しかし、*Get-AzureVirtualNetworkGatewayConnection –Debug* を使用して、接続が成功したことを検証できます。将来、これを行うためのコマンドレットと、プレビュー ポータルで接続を表示する機能が提供される予定です。

次のコマンドレット サンプルを使用して、実際に使用する値と同じ値を構成できます。プロンプトが表示されたら、*A* を選択してすべてを実行します。

		Get-AzureVirtualNetworkGatewayConnection -Name localtovon -ResourceGroupName testrg -Debug

 コマンドレットが完了したら、スクロールして値を表示します。以下の例では、接続状態は *[接続中]* と表示され、受信バイトと送信バイトを確認できます。

	Body:
	{
	  "name": "localtovon",
	  "id":
	"/subscriptions/086cfaa0-0d1d-4b1c-9455-f8e3da2a0c7789/resourceGroups/testrg/providers/Microsoft.Network/connections/loca
	ltovon",
	  "properties": {
	    "provisioningState": "Succeeded",
	    "resourceGuid": "1c484f82-23ec-47e2-8cd8-231107450446b",
	    "virtualNetworkGateway1": {
	      "id":
	"/subscriptions/086cfaa0-0d1d-4b1c-9455-f8e3da2a0c7789/resourceGroups/testrg/providers/Microsoft.Network/virtualNetworkGa
	teways/vnetgw1"
	    },
	    "localNetworkGateway2": {
	      "id":
	"/subscriptions/086cfaa0-0d1d-4b1c-9455-f8e3da2a0c7789/resourceGroups/testrg/providers/Microsoft.Network/localNetworkGate
	ways/LocalSite"
	    },
	    "connectionType": "IPsec",
	    "routingWeight": 10,
	    "sharedKey": "abc123",
	    "connectionStatus": "Connected",
	    "ingressBytesTransferred": 33509044,
	    "egressBytesTransferred": 4142431
	  }


## ローカル サイト用の IP アドレスのプレフィックスを変更するには

ローカル サイト用のプレフィックスを変更する必要がある場合は、以下の手順を使用します。VPN Gateway 接続を作成したかどうかに応じて、2 種類の手順が示されます。

### VPN Gateway 接続を使用しないプレフィックスの追加または削除


- まだ VPN Gateway 接続がない状態で、作成したローカル サイトにさらにアドレスのプレフィックスを**追加するには**、以下の例を使用してください。

		$local = Get-AzureLocalNetworkGateway -Name LocalSite -ResourceGroupName testrg
		Set-AzureLocalNetworkGateway -LocalNetworkGateway $local -AddressPrefix @('10.0.0.0/24','20.0.0.0/24','30.0.0.0/24')


- VPN 接続がないローカル サイトからアドレスのプレフィックスを**削除するには**、以下の例を使用してください。不要になったプレフィックスは削除します。この例では、プレフィックス 20.0.0.0/24 (前の例に含まれる) が不要になったため、ローカル サイトを更新してそのプレフィックスを削除します。

		local = Get-AzureLocalNetworkGateway -Name LocalSite -ResourceGroupName testrg
		Set-AzureLocalNetworkGateway -LocalNetworkGateway $local -AddressPrefix @('10.0.0.0/24','30.0.0.0/24')

### VPN Gateway 接続を使用したプレフィックスの追加または削除

VPN 接続を作成していた場合に、ローカル サイトに含まれている IP アドレスのプレフィックスを追加または削除するには、次の手順を順番に実行する必要があります。これにより、ゲートウェイを削除して再作成する必要があるため、VPN 接続のためにある程度のダウンタイムが発生します。ただし、その接続用の IP アドレスは要求済みであるため、以前に使用した値を変更する場合を除き、オンプレミスの VPN ルーターを再構成する必要はありません。

 
1. ゲートウェイの接続を削除します。 
2. ローカル サイトのプレフィックスを変更します。 
3. 新しいゲートウェイ接続を作成します。 

次のサンプルをガイドラインとして使用できます。


		$gateway1 = Get-AzureVirtualNetworkGateway -Name vnetgw1 -ResourceGroupName testrg
		$local = Get-AzureLocalNetworkGateway -Name LocalSite -ResourceGroupName testrg

		remove-AzureVirtualNetworkGatewayConnection -Name vnetgw1 -ResourceGroupName testrg

		$local = Get-AzureLocalNetworkGateway -Name LocalSite -ResourceGroupName testrg
		Set-AzureLocalNetworkGateway -LocalNetworkGateway $local -AddressPrefix @('10.0.0.0/24','20.0.0.0/24','30.0.0.0/24')
	

		New-AzureVirtualNetworkGatewayConnection -Name localtovon -ResourceGroupName testrg -Location 'West US' -VirtualNetworkGateway1 $gateway1 -LocalNetworkGateway2 $local -ConnectionType IPsec -RoutingWeight 10 -SharedKey 'abc123'


## 次のステップ

仮想ネットワークに仮想マシンを追加します。[仮想マシンを作成します。](../virtual-machines/virtual-machines-windows-tutorial.md)

<!---HONumber=Oct15_HO3-->