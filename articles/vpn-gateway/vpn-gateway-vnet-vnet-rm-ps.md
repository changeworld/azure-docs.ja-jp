<properties
   pageTitle="同じサブスクリプションに存在する VNet の VNet 間 VPN Gateway 接続を Azure リソース マネージャーおよび PowerShell を使用して作成する | Microsoft Azure"
   description="この記事では、Azure リソース マネージャーおよび PowerShell を使用して仮想ネットワーク同士を接続する方法を説明します。"
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
   ms.date="12/14/2015"
   ms.author="cherylmc"/>

# Azure リソース マネージャーおよび PowerShell を使用して同じサブスクリプション内の仮想ネットワークの VNet 間接続を構成する

> [AZURE.SELECTOR]
- [Azure Classic Portal](virtual-networks-configure-vnet-to-vnet-connection.md)
- [PowerShell - Azure Resource Manager](vpn-gateway-vnet-vnet-rm-ps.md)

この記事では、リソース マネージャー デプロイメント モデルを使用して、手順を説明します。この構成に対して別のデプロイ モデルを探す場合は、上のタブで必要な記事を選択してください。

この時点で、リソース マネージャーのデプロイメント方法を使用して作成した仮想ネットワークのうち、異なるサブスクリプション内に存在する仮想ネットワークを対象にした VNet 間接続についてはソリューションがありません。チームは現在、ソリューションの開発に取り組んでいます。年内または来年初頭に進展があることを見込んでいます。進展があった場合は、この記事で内容を説明します。次の手順は、同じサブスクリプション内にある VNet に関するものです。

**Azure のデプロイ モデルについて**

[AZURE.INCLUDE [vpn-gateway-clasic-rm](../../includes/vpn-gateway-classic-rm-include.md)]
	
- クラシック デプロイ モデルを使用して仮想ネットワークを作成した場合は、「[VNet 間接続の作成](virtual-networks-configure-vnet-to-vnet-connection.md)」を参照してください。クラシック デプロイメント モデルでは、異なるサブスクリプションに存在する VNet 間の接続をサポートします。
	
- クラシック デプロイ モデルで作成した仮想ネットワークを、Azure リソース マネージャー モデルで作成した仮想ネットワークに接続する場合は、「[従来の VNet を新しい VNet に接続する](../virtual-network/virtual-networks-arm-asm-s2s.md)」を参照してください。

## 接続図

仮想ネットワークどうし (VNet 間) の接続は、仮想ネットワークをオンプレミス サイトの場所に接続することとよく似ています。どちらの接続タイプでも、VPN ゲートウェイを使用して、IPsec/IKE を使った安全なトンネルが確保されます。接続する VNet は、リージョンが異なっていてもかまいません。マルチサイト構成と VNet 間通信を組み合わせることもできます。そのため、クロスプレミス接続と仮想ネットワーク間接続とを組み合わせたネットワーク トポロジを確立することができます (下図参照)。

![VNet 間接続の図](./media/virtual-networks-configure-vnet-to-vnet-connection/IC727360.png)
 
## 仮想ネットワークを接続する理由

仮想ネットワークを接続するのは次のような場合です。

- **リージョン間の geo 冗長性および geo プレゼンス**
	- インターネット接続エンドポイントを介さず、安全な接続を使って独自の geo レプリケーションや geo 同期をセットアップすることができます。
	- Azure Load Balancer と Microsoft (またはサード パーティ) のクラスタリング テクノロジを使用し、複数の Azure リージョンをまたぐ geo 冗長性を備えた、可用性に優れたワークロードをセットアップすることができます。たとえば、複数の Azure リージョンにまたがる SQL AlwaysOn 可用性グループをセットアップすることができます。

- **特定の地域内で強固な分離境界を備えた多層アプリケーション**
	- 同じ地域内の相互に接続された複数の仮想ネットワークを使って多層アプリケーションをセットアップすることができます。それぞれの層のアプリケーションが強固に分離され、安全な層間通信を実現することができます。

### 注意する点

この記事では、VNet1 と VNet2 の 2 つの仮想ネットワークを接続する手順を見ていきます。実際のネットワーク設計の要件に対応した IP アドレス範囲に置き換えるためには、ネットワークに関する知識が必要です。


![VNet 間の接続](./media/virtual-networks-configure-vnet-to-vnet-connection/IC727361.png)


- 仮想ネットワークが属している Azure リージョン (場所) は異なっていてもかまいません。

- クラウド サービスや負荷分散エンドポイントは、仮にそれらが相互に接続されていたとしても、仮想ネットワークの境界を越えることはできません。

- 複数の Azure Virtual Network を接続するときに、オンプレミスの VPN ゲートウェイは必要ありません (クロスプレミス接続が必要な場合を除く)。

- VNet 間接続によって仮想ネットワークを接続できます。仮想ネットワーク内に存在しない仮想マシンやクラウド サービスを接続することはできません。

- VNet 間接続には、RouteBased VPN タイプの Azure VPN ゲートウェイが必要です (以前は「動的ゲートウェイ」と呼ばれていました) 。

- 仮想ネットワーク接続は、マルチサイト VPN と同時に使用することができます。1 つの仮想ネットワーク VPN ゲートウェイに最大 10 本の VPN トンネルを確立し、他の仮想ネットワークまたはオンプレミス サイトに接続することが可能です。

- 仮想ネットワークのアドレス空間とオンプレミスのローカル ネットワーク サイトのアドレス空間とが重複しないようにする必要があります。アドレス空間が重複していると、仮想ネットワークの作成は失敗します。

- 一対の仮想ネットワーク間に冗長トンネルを確立することはできません。

- 仮想ネットワークのすべての VPN トンネルは、Azure VPN ゲートウェイ上の使用可能な帯域幅を共有し、Azure 内の同じ VPN ゲートウェイ アップタイム SLA を共有します。

- VNet 間のトラフィックは、Azure バックボーン経由で送信できます。

## 開始する前に

開始する前に、以下がそろっていることを確認します。

- Azure サブスクリプション。Azure サブスクリプションを持っていない場合は、[MSDN サブスクライバーの特典](http://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/)を有効にするか、[無料試用版](http://azure.microsoft.com/pricing/free-trial/)にサインアップしてください。

## PowerShell モジュールのインストール

接続を構成するには、Azure リソース マネージャー PowerShell コマンドレットの最新版が必要です。

[AZURE.INCLUDE [vpn-gateway-ps-rm-howto](../../includes/vpn-gateway-ps-rm-howto-include.md)]


## 1\.IP アドレス範囲を決める


ネットワーク構成に使用する範囲を決めることは重要です。VNet1 から見ると、VNet2 は、Azure プラットフォームに定義されている別の VPN 接続にすぎません。また、VNet2 から見れば、VNet1 も別の VPN 接続です。VNet の範囲やローカル ネットワークの範囲が重複することは、どのような形であれ許容されないので注意してください。


以下の手順に従って、2 つの仮想ネットワークを、それぞれのゲートウェイ サブネットおよび構成とともに作成します。その後に、これら 2 つの VNet 間の VPN ゲートウェイ接続を作成します。

この演習では、VNet に次の値を使用します。

VNet1 の値:

- 仮想ネットワーク名 = VNet1
- リソース グループ = testrg1
- アドレス空間: 10.1.0.0/16 
- リージョン = 米国西部
- GatewaySubnet = 10.1.0.0/28
- Subnet1 = 10.1.1.0/28

VNet2 の値:

- 仮想ネットワーク名 = VNet2
- リソース グループ = testrg2
- アドレス空間: 10.2.0.0/16
- リージョン = 東日本
- GatewaySubnet = 10.2.0.0/28
- Subnet1 = 10.2.1.0/28

## 2\.サブスクリプションへの接続 

リソース マネージャー コマンドレットを使用するように PowerShell モードを切り替えてください。詳細については、「[リソース マネージャーでの Windows PowerShell の使用](../powershell-azure-resource-manager.md)」を参照してください。

PowerShell コンソールを開き、アカウントに接続します。接続するには、次のサンプルを参照してください。

		    Login-AzureRmAccount

アカウントのサブスクリプションを確認します。

		    Get-AzureRmSubscription 

使用するサブスクリプションを指定します。

		    Select-AzureRmSubscription -Subscriptionid "GUID of subscription"


## 3\.仮想ネットワークの作成


仮想ネットワークとゲートウェイ サブネットを作成するには、次のサンプルを使用してください。サンプル値は実際の値に変更してください。この例では、VNet1 を作成します。後で同じ手順を繰り返して VNet2 を作成します。

まず、リソース グループを作成します。

			New-AzureRmResourceGroup -Name testrg1 -Location 'West US'

次に、仮想ネットワークを作成します。次のサンプルでは、*VNet1* という仮想ネットワークと、*GatewaySubnet* と *Subnet1* という 2 つのサブネットを作成します。正確に *GatewaySubnet* というサブネットを作成する必要があります。別の名前にすると、接続の構成は失敗します。次の例では、ゲートウェイ サブネットで /28 を使用しています。ゲートウェイ サブネットには最小値 /29 まで使用できます。機能によっては (ExpressRoute とサイト間 VPN が共存する接続など)、/27 より大きなゲートウェイ サブネットが必要となる場合があるため、将来追加する可能性がある機能に対応できるようにゲートウェイ サブネットを作成してください。

 		$subnet = New-AzureRmVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -AddressPrefix 10.1.0.0/28
		$subnet1 = New-AzureRmVirtualNetworkSubnetConfig -Name 'Subnet1' -AddressPrefix '10.1.1.0/28'
		New-AzureRmVirtualNetwork -Name VNet1 -ResourceGroupName testrg1 -Location 'West US' -AddressPrefix 10.1.0.0/16 -Subnet $subnet, $subnet1

## 4\.パブリック IP アドレスの要求


次に、VNet で作成するゲートウェイに割り当てるパブリック IP アドレスを要求します。使用する IP アドレスは指定できません。IP アドレスはゲートウェイに動的に割り当てられます。この IP アドレスは、次の構成セクションで使用します。

以下のサンプルを使用します。このアドレスの割り当て方法は動的にする必要があります。


		$gwpip= New-AzureRmPublicIpAddress -Name gwpip1 -ResourceGroupName testrg1 -Location 'West US' -AllocationMethod Dynamic

## 5\.ゲートウェイ構成の作成


ゲートウェイの構成で、使用するサブネットとパブリック IP アドレスを定義します。次のサンプルを使用して、ゲートウェイの構成を作成します。


		$vnet = Get-AzureRmVirtualNetwork -Name VNet1 -ResourceGroupName testrg1
		$subnet = Get-AzureRmVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -VirtualNetwork $vnet
		$gwipconfig = New-AzureRmVirtualNetworkGatewayIpConfig -Name gwipconfig1 -SubnetId $subnet.Id -PublicIpAddressId $gwpip.Id 

## 6\.ゲートウェイを作成する


この手順では、VNet の仮想ネットワーク ゲートウェイを作成します。VNet 間構成では、RouteBased VpnType が必要です。ゲートウェイの作成には時間がかかるため、気長に取り組んでください。

		New-AzureRmVirtualNetworkGateway -Name vnetgw1 -ResourceGroupName testrg1 -Location 'West US' -IpConfigurations $gwipconfig -GatewayType Vpn -VpnType RouteBased

## 7\.VNet2 の作成


VNet1 の構成後、前述の手順を繰り返して、VNet2 とそのゲートウェイ構成を構成します。両方の VNet とそれぞれのゲートウェイの構成が完了したら、**手順 8. ゲートウェイの接続**に進みます。

## 8\.ゲートウェイの接続

このステップでは、2 つの仮想ネットワーク ゲートウェイ間の VPN ゲートウェイ接続を作成します。この例では、参照される共有キーが表示されます。共有キーには独自の値を使用することができます。両方の構成の共有キーが一致することが重要です。

接続の作成には、多少時間がかかります。

**VNet1 から VNet2 の場合**
    
    $vnetgw1 = Get-AzureRmVirtualNetworkGateway -Name vnetgw1 -ResourceGroupName testrg1
    $vnetgw2 = Get-AzureRmVirtualNetworkGateway -Name vnetgw2 -ResourceGroupName testrg2
    
    New-AzureRmVirtualNetworkGatewayConnection -Name conn1 -ResourceGroupName testrg1 -VirtualNetworkGateway1 $vnetgw1 -VirtualNetworkGateway2 $vnetgw2 -Location 'West US' -ConnectionType Vnet2Vnet -SharedKey 'abc123'


**VNet2 から VNet1 の場合**
    
    $vnetgw1 = Get-AzureRmVirtualNetworkGateway -Name vnetgw2 -ResourceGroupName testrg2
    $vnetgw2 = Get-AzureRmVirtualNetworkGateway -Name vnetgw1 -ResourceGroupName testrg1
    
    New-AzureRmVirtualNetworkGatewayConnection -Name conn2 -ResourceGroupName testrg2 -VirtualNetworkGateway1 $vnetgw1 -VirtualNetworkGateway2 $vnetgw2 -Location 'Japan East' -ConnectionType Vnet2Vnet -SharedKey 'abc123'
    

数分後に接続が確立されます。この時点では、Azure リソース マネージャーで作成されたゲートウェイおよび接続は、プレビュー ポータルでは表示されません。

## 接続の検証

この時点では、リソース マネージャーを使用して作成された VPN 接続はプレビュー ポータルに表示されません。しかし、*Get-AzureRmVirtualNetworkGatewayConnection –Debug* を使用して、接続が成功したことを検証できます。将来、これを行うためのコマンドレットと、プレビュー ポータルで接続を表示する機能が提供される予定です。

次のコマンドレットの例を使用できます。検証する各接続と一致するように、値を変更してください。プロンプトが表示されたら、*A* を選択してすべてを実行します。

		Get-AzureRmVirtualNetworkGatewayConnection -Name vnet2connection -ResourceGroupName vnet2vnetrg -Debug 

 コマンドレットが完了したら、スクロールして値を表示します。以下の例では、接続状態は "*Connected*" と表示され、受信バイトと送信バイトを確認できます。

	Body:
	{
	  "name": "Vnet2Connection",
	  "id": "/subscriptions/a932c0e6-b5cb-4e68-b23d-5064372c8a3cdef/resourceGroups/Vnet2VnetRG/providers/Microsoft.Network/connections/Vnet2Connection",
	  "properties": {
	    "provisioningState": "Succeeded",
	    "resourceGuid": "3c0bc049-860d-46ea-9909-e08098680a8bdef",
	    "virtualNetworkGateway1": {
	      "id": "/subscriptions/a932c0e6-b5cb-4e68-b23d-5064372c8a3cdef/resourceGroups/Vnet2VnetRG/providers/Microsoft.Network/virtualNetworkGateways/Vnet2Gw"
	    },
	    "virtualNetworkGateway2": {
	      "id": "/subscriptions/a932c0e6-b5cb-4e68-b23d-5064372c8a3cdef/resourceGroups/Vnet2VnetRG/providers/Microsoft.Network/virtualNetworkGateways/Vnet1Gw"
	    },
	    "connectionType": "Vnet2Vnet",
	    "routingWeight": 3,
	    "sharedKey": "abc123",
	    "connectionStatus": "Connected",
	    "ingressBytesTransferred": 3359044,
	    "egressBytesTransferred": 4142451
	  }
	} 

[AZURE.INCLUDE [vpn-gateway-no-nsg-include](../../includes/vpn-gateway-no-nsg-include.md)]

## 既存 VNet の接続

Azure リソース マネージャー モードで既に作成した複数の仮想ネットワークを接続するには、次を確認します。

- 各 VNet には最低 /29 またはそれ以上の規模のゲートウェイ サブネットがある。
- 仮想ネットワークのアドレス範囲が重複していない。
- VNet は同じサブスクリプションにあります。

VNet にゲートウェイ サブネットを追加する必要がある場合は、次のサンプルを使用して、値を置き換えます。ゲートウェイ サブネットには、必ず 'GatewaySubnet' という名前を付けてください。別の名前にすると、VPN の構成は期待どおりに機能しません。

	
		$vnet = Get-AzureRmVirtualNetwork -ResourceGroupName testrg -Name testvnet
		Add-AzureRmVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -AddressPrefix 10.0.3.0/28 -VirtualNetwork $vnet
		Set-AzureRmVirtualNetwork -VirtualNetwork $vnet

ゲートウェイ サブネットが正しく構成されていることを確認したら、引き続き、**手順4. パブリック IP アドレスの要求**の手順に従います。


## 次のステップ

接続が完成したら、仮想ネットワークに仮想マシンを追加することができます。手順については、[仮想マシンの作成](../virtual-machines/virtual-machines-windows-tutorial.md)に関するページを参照してください。

<!---HONumber=AcomDC_1217_2015-->