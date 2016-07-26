<properties
   pageTitle="VNet 間の VPN ゲートウェイ接続を Azure リソース マネージャーと PowerShell for VNets を使用して作成する | Microsoft Azure"
   description="この記事では、Azure リソース マネージャーおよび PowerShell を使用して仮想ネットワーク同士を接続する方法を説明します。"
   services="vpn-gateway"
   documentationCenter="na"
   authors="cherylmc"
   manager="carmonm"
   editor=""
   tags="azure-resource-manager"/>

<tags
   ms.service="vpn-gateway"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="07/15/2016"
   ms.author="cherylmc"/>

# Azure リソース マネージャーと PowerShell を使用した VNet 間の接続の構成

> [AZURE.SELECTOR]
- [Azure クラシック ポータル](virtual-networks-configure-vnet-to-vnet-connection.md)
- [PowerShell - Azure リソース マネージャー](vpn-gateway-vnet-vnet-rm-ps.md)

この記事では、Resource Manager デプロイ モデルと PowerShell を使用して仮想ネットワーク間の接続を作成する手順について説明します。仮想ネットワークが属しているリージョンやサブスクリプションは異なっていてもかまいません。


**Azure のデプロイ モデルについて**

[AZURE.INCLUDE [vpn-gateway-clasic-rm](../../includes/vpn-gateway-classic-rm-include.md)]

**VNet 対 VNet 用のデプロイ モデルとツール**

両方のデプロイ モデルで、さまざまなツールを利用し、VNet 間の接続を構成できます。詳細については、下の表を参照してください。この表は、この構成について新しい記事、新しいデプロイメント モデル、追加のツールが利用できるようになったら更新されるものです。記事が利用できるようになったら、表から直接リンクできるようにします。

[AZURE.INCLUDE [vpn-gateway-table-vnet-vnet](../../includes/vpn-gateway-table-vnet-to-vnet-include.md)]


## VNet 間接続の概要

仮想ネットワークどうし (VNet 間) の接続は、仮想ネットワークをオンプレミス サイトの場所に接続することとよく似ています。どちらの接続タイプでも、Azure VPN ゲートウェイを使用し、IPsec/IKE を使った安全なトンネルが確保されます。接続する VNet は、リージョンが異なっていてもかまいません。あるいは、サブスクリプションが異なっていてもかまいません。マルチサイト構成と VNet 間通信を組み合わせることもできます。そのため、クロスプレミス接続と仮想ネットワーク間接続とを組み合わせたネットワーク トポロジを確立することができます (下図参照)。


![接続について](./media/vpn-gateway-vnet-vnet-rm-ps/aboutconnections.png)
 
### 仮想ネットワークを接続する理由

仮想ネットワークを接続するのは次のような場合です。

- **リージョン間の geo 冗長性および geo プレゼンス**
	- インターネット接続エンドポイントを介さず、安全な接続を使って独自の geo レプリケーションや geo 同期をセットアップすることができます。
	- Azure Traffic Manager と Azure Load Balancer を使用し、複数の Azure リージョンをまたぐ geo 冗長性を備えた、可用性に優れたワークロードをセットアップすることができます。たとえば、複数の Azure リージョンにまたがる SQL AlwaysOn 可用性グループをセットアップすることができます。

- **特定のリージョン内で分離または管理境界を備えた多層アプリケーション**
	- 同じリージョン内で、分離または管理要件に基づいて相互に接続された複数の仮想ネットワークを利用し、多層アプリケーションをセットアップすることができます。


### VNet 間接続に関してよく寄せられる質問

[AZURE.INCLUDE [vpn-gateway-vnet-vnet-faq](../../includes/vpn-gateway-vnet-vnet-faq-include.md)]


## どの手順を利用するべきでしょうか。

この記事では、リソース マネージャー デプロイ モデルを利用して作成された VNet にさまざまな手順が適用されます。VNet 間構成手順は、VNet のサブスクリプションが同じか異なるかによって変わります。

![両方の接続](./media/vpn-gateway-vnet-vnet-rm-ps/differentsubscription.png)


構成手順については、すべての仮想ネットワーク リソースとゲートウェイ リソースを同じ PowerShell セッションで作成し、構成できるかどうかがこの 2 つの大きな違いとなります。このドキュメントでは、それぞれの手順について説明します。上の図は、サブスクリプションが同じ VNet 間接続とサブスクリプションが異なる VNet 間接続を示しています。


- [サブスクリプションが同じ VNet](#samesub)
- [サブスクリプションが異なる VNet](#difsub)


## <a name="samesub"></a>同じサブスクリプション内にある VNet を接続する方法

この構成は、下の図のように同じサブスクリプションにある仮想ネットワークに適用されます。

![同じサブスクリプションの VNet2VNet](./media/vpn-gateway-vnet-vnet-rm-ps/samesubscription.png)

### 開始する前に

- Azure サブスクリプションを持っていることを確認します。Azure サブスクリプションをまだお持ちでない場合は、[MSDN サブスクライバーの特典](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/)を有効にするか、[無料アカウント](https://azure.microsoft.com/pricing/free-trial/)にサインアップしてください。
	
- Azure リソース マネージャー PowerShell コマンドレットをインストールする必要があります。PowerShell コマンドレットのインストールの詳細については、「[Azure PowerShell のインストールおよび構成方法](../powershell-install-configure.md)」を参照してください。

### <a name="Step1"></a>手順 1 - IP アドレス範囲を決める


ネットワーク構成に使用する範囲を決めることは重要です。VNet の範囲やローカル ネットワークの範囲が重複することは、どのような形であれ許容されないので注意してください。

以下の手順に従って、2 つの仮想ネットワークを、それぞれのゲートウェイ サブネットおよび構成とともに作成します。その後に、これら 2 つの VNet 間の VPN ゲートウェイ接続を作成します。

この演習では、VNet に次の値を使用します。

**TestVNet1 の値:**

- VNet の名前: TestVNet1
- リソース グループ: TestRG1
- 場所: 米国東部
- TestVNet1: 10.11.0.0/16 & 10.12.0.0/16
- FrontEnd: 10.11.0.0/24
- BackEnd: 10.12.0.0/24
- GatewaySubnet: 10.12.255.0/27
- DNS サーバー: 8.8.8.8
- ゲートウェイの名前: VNet1GW
- パブリック IP: VNet1GWIP
- VPN の種類: RouteBased
- 接続 (1 ～ 4): VNet1toVNet4
- 接続 (1 ～ 5): VNet1toVNet5
- 接続の種類: VNet2VNet


**TestVNet4 の値:**

- VNet の名前: TestVNet4
- TestVNet2: 10.41.0.0/16 & 10.42.0.0/16
- FrontEnd: 10.41.0.0/24
- BackEnd: 10.42.0.0/24
- GatewaySubnet: 10.42.255.0.0/27
- リソース グループ: TestRG4
- 場所: 米国西部
- DNS サーバー: 8.8.8.8
- ゲートウェイの名前: VNet4GW
- パブリック IP: VNet4GWIP
- VPN の種類: RouteBased
- 接続: VNet4toVNet1
- 接続の種類: VNet2VNet



### <a name="Step2"></a>手順 2 - TestVNet1 を作成し、構成する

1. 変数を宣言する

	この練習では、最初に変数を宣言します。次の例では、この演習の値を利用し、変数を宣言します。運用環境の場合、実際の値を使用します。この手順を通して実行し、この種の構成になれたら、これらの変数を利用できます。変数を変更し、コピーし、PowerShell コンソールに貼り付けます。

		$Sub1          = "Replace_With_Your_Subcription_Name"
		$RG1           = "TestRG1"
		$Location1     = "East US"
		$VNetName1     = "TestVNet1"
		$FESubName1    = "FrontEnd"
		$BESubName1    = "Backend"
		$GWSubName1    = "GatewaySubnet"
		$VNetPrefix11  = "10.11.0.0/16"
		$VNetPrefix12  = "10.12.0.0/16"
		$FESubPrefix1  = "10.11.0.0/24"
		$BESubPrefix1  = "10.12.0.0/24"
		$GWSubPrefix1  = "10.12.255.0/27"
		$DNS1          = "8.8.8.8"
		$GWName1       = "VNet1GW"
		$GWIPName1     = "VNet1GWIP"
		$GWIPconfName1 = "gwipconf1"
		$Connection14  = "VNet1toVNet4"
		$Connection15  = "VNet1toVNet5"

2. サブスクリプション 1 に接続する

	リソース マネージャー コマンドレットを使用するように PowerShell モードを切り替えてください。詳細については、「[リソース マネージャーでの Windows PowerShell の使用](../powershell-azure-resource-manager.md)」を参照してください。

	PowerShell コンソールを開き、アカウントに接続します。接続するには、次のサンプルを参照してください。

		Login-AzureRmAccount

	アカウントのサブスクリプションを確認します。

		Get-AzureRmSubscription 

	使用するサブスクリプションを指定します。

		Select-AzureRmSubscription -SubscriptionName $Sub1

3. 新しいリソース グループを作成する

		New-AzureRmResourceGroup -Name $RG1 -Location $Location1

4. TestVNet1 のサブネット構成を作成する

	下の例では、TestVNet1 という名前の仮想ネットワークと 3 つのサブネットを作成します。サブネットの名前は GatewaySubnet、FrontEnd、Backend です。値を代入するときは、ゲートウェイの名前を必ず GatewaySubnet にすることが重要です。別の名前にすると、接続を作成できません。

	次の例では、ゲートウェイ サブネットで /27 を使用しています。技術的には /29 のような小さなサブネットでゲートウェイ サブネットを作成できますが、それは推奨されません。既存または今後の構成で大きなゲートウェイ サブネットが必要になることを想定し、/27 や /26 など、可能な限り大きなサブネットが推奨されます。


		$fesub1 = New-AzureRmVirtualNetworkSubnetConfig -Name $FESubName1 -AddressPrefix $FESubPrefix1
		$besub1 = New-AzureRmVirtualNetworkSubnetConfig -Name $BESubName1 -AddressPrefix $BESubPrefix1
		$gwsub1 = New-AzureRmVirtualNetworkSubnetConfig -Name $GWSubName1 -AddressPrefix $GWSubPrefix1


5. TestVNet1 を作成する

		New-AzureRmVirtualNetwork -Name $VNetName1 -ResourceGroupName $RG1 -Location $Location1 -AddressPrefix $VNetPrefix11,$VNetPrefix12 -Subnet $fesub1,$besub1,$gwsub1

6. パブリック IP アドレスの要求

	次に、VNet で作成するゲートウェイに割り当てるパブリック IP アドレスを要求します。使用する IP アドレスは指定できません。IP アドレスはゲートウェイに動的に割り当てられます。この IP アドレスは、次の構成セクションで使用します。次の例を使用します。このアドレスの割り当て方法は動的にする必要があります。

		$gwpip1    = New-AzureRmPublicIpAddress -Name $GWIPName1 -ResourceGroupName $RG1 -Location $Location1 -AllocationMethod Dynamic

7. ゲートウェイ構成の作成

	ゲートウェイの構成で、使用するサブネットとパブリック IP アドレスを定義します。次のサンプルを使用して、ゲートウェイの構成を作成します。

		$vnet1     = Get-AzureRmVirtualNetwork -Name $VNetName1 -ResourceGroupName $RG1
		$subnet1   = Get-AzureRmVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet1
		$gwipconf1 = New-AzureRmVirtualNetworkGatewayIpConfig -Name $GWIPconfName1 -Subnet $subnet1 -PublicIpAddress $gwpip1

8. TestVNet1 のゲートウェイを作成する

	この手順では、TestVNet1 の仮想ネットワーク ゲートウェイを作成します。VNet 間構成では、RouteBased VpnType が必要です。ゲートウェイの作成には時間がかかります (完了に 30 分以上必要とします)。

		New-AzureRmVirtualNetworkGateway -Name $GWName1 -ResourceGroupName $RG1 -Location $Location1 -IpConfigurations $gwipconf1 -GatewayType Vpn -VpnType RouteBased -GatewaySku Standard

### 手順 3 - TestVNet4 を作成し、構成する

TestVNet1 を構成したら、この手順を繰り返し、TestVNet4 を作成します。下の手順を実行します。値は必要に応じて実際の値に置換します。サブスクリプションが同じため、この手順は同じ PowerShell セッション内で実行できます。

1. 変数を宣言する

	値は実際の構成で使用する値に置換します。

		$RG4           = "TestRG4"
		$Location4     = "West US"
		$VnetName4     = "TestVNet4"
		$FESubName4    = "FrontEnd"
		$BESubName4    = "Backend"
		$GWSubName4    = "GatewaySubnet"
		$VnetPrefix41  = "10.41.0.0/16"
		$VnetPrefix42  = "10.42.0.0/16"
		$FESubPrefix4  = "10.41.0.0/24"
		$BESubPrefix4  = "10.42.0.0/24"
		$GWSubPrefix4  = "10.42.255.0/27"
		$DNS4          = "8.8.8.8"
		$GWName4       = "VNet4GW"
		$GWIPName4     = "VNet4GWIP"
		$GWIPconfName4 = "gwipconf4"
		$Connection41  = "VNet4toVNet1"

	続行する前に、サブスクリプションの 1 に接続されていることを確認してください。

2. 新しいリソース グループを作成する

		New-AzureRmResourceGroup -Name $RG4 -Location $Location4

3. TestVNet4 のサブネット構成を作成する

		$fesub4 = New-AzureRmVirtualNetworkSubnetConfig -Name $FESubName4 -AddressPrefix $FESubPrefix4
		$besub4 = New-AzureRmVirtualNetworkSubnetConfig -Name $BESubName4 -AddressPrefix $BESubPrefix4
		$gwsub4 = New-AzureRmVirtualNetworkSubnetConfig -Name $GWSubName4 -AddressPrefix $GWSubPrefix4

4. TestVNet4 を作成する

		New-AzureRmVirtualNetwork -Name $VnetName4 -ResourceGroupName $RG4 -Location $Location4 -AddressPrefix $VnetPrefix41,$VnetPrefix42 -Subnet $fesub4,$besub4,$gwsub4

5. パブリック IP アドレスの要求

		$gwpip4    = New-AzureRmPublicIpAddress -Name $GWIPName4 -ResourceGroupName $RG4 -Location $Location4 -AllocationMethod Dynamic

6. ゲートウェイ構成の作成

		$vnet4     = Get-AzureRmVirtualNetwork -Name $VnetName4 -ResourceGroupName $RG4
		$subnet4   = Get-AzureRmVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet4
		$gwipconf4 = New-AzureRmVirtualNetworkGatewayIpConfig -Name $GWIPconfName4 -Subnet $subnet4 -PublicIpAddress $gwpip4

7. TestVNet4 ゲートウェイを作成する

		New-AzureRmVirtualNetworkGateway -Name $GWName4 -ResourceGroupName $RG4 -Location $Location4 -IpConfigurations $gwipconf4 -GatewayType Vpn -VpnType RouteBased -GatewaySku Standard

### 手順 4 - ゲートウェイを接続する

1. 両方の仮想ネットワーク ゲートウェイを取得する

	この例では、両方のゲートウェイのサブスクリプションが同じであるため、この手順を同じ PowerShell セッションで完了できます。

		$vnet1gw = Get-AzureRmVirtualNetworkGateway -Name $GWName1 -ResourceGroupName $RG1
		$vnet4gw = Get-AzureRmVirtualNetworkGateway -Name $GWName4 -ResourceGroupName $RG4


2. TestVNet1 から TestVNet4 への接続を作成する

	この手順では、TestVNet1 から TestVNet4 への接続を作成します。この例では、参照される共有キーが表示されます。共有キーには独自の値を使用することができます。両方の接続の共有キーが一致することが重要です。接続の作成完了までしばらくかかります。

		New-AzureRmVirtualNetworkGatewayConnection -Name $Connection14 -ResourceGroupName $RG1 -VirtualNetworkGateway1 $vnet1gw -VirtualNetworkGateway2 $vnet4gw -Location $Location1 -ConnectionType Vnet2Vnet -SharedKey 'AzureA1b2C3'

3. TestVNet4 から TestVNet1 への接続を作成する

	この手順は上記の手順と同じですが、TestVNet4 から TestVNet1 への接続になります。共有キーが一致することを確認してください。

		New-AzureRmVirtualNetworkGatewayConnection -Name $Connection41 -ResourceGroupName $RG4 -VirtualNetworkGateway1 $vnet4gw -VirtualNetworkGateway2 $vnet1gw -Location $Location4 -ConnectionType Vnet2Vnet -SharedKey 'AzureA1b2C3'

	数分後に接続が確立されます。

## <a name="Verify"></a>VNet 間の接続を検証する方法

次の例では、接続を確認する方法を説明します。環境に合わせて値を変更してください。

### Azure ポータルを使用して接続を確認するには

Azure ポータルで VPN 接続を確認するには、**[仮想ネットワーク ゲートウェイ]** に移動し、**ゲートウェイ名**、**[設定]**、**[接続]** の順にクリックします。接続の名前を選択すると、**[接続]** ブレードに詳細な情報が表示されます。


### PowerShell を使用して接続を確認するには

*Get-AzureRmVirtualNetworkGatewayConnection –Debug* を使用して、接続が成功したことを検証することもできます。次のサンプルを使用し、値を実際に使用する値に変えます。プロンプトが表示されたら、A を選択してすべてを実行します。

	Get-AzureRmVirtualNetworkGatewayConnection -Name $Connection1 -ResourceGroupName $RG1 -Debug

コマンドレットが完了したら、スクロールして値を表示します。以下の PowerShell の出力例では、接続状態は "*Connected*" と表示されており、受信バイトと送信バイトを確認できます。



	AuthorizationKey           :
	VirtualNetworkGateway1     : Microsoft.Azure.Commands.Network.Models.PSVirtualNetworkGateway
	VirtualNetworkGateway2     : Microsoft.Azure.Commands.Network.Models.PSVirtualNetworkGateway
	LocalNetworkGateway2       :
	Peer                       :
	ConnectionType             : Vnet2Vnet
	RoutingWeight              : 0
	SharedKey                  : AzureA1b2C3
	ConnectionStatus           : Connected
	EgressBytesTransferred     : 1376
	IngressBytesTransferred    : 1232
	ProvisioningState          : Succeeded
	VirtualNetworkGateway1Text : "/subscriptions/<SubscriptionID>/resourceGroups/TestRG1/providers/Microsoft.Network/virtualNetworkGateways/VNet1GW"
	VirtualNetworkGateway2Text : "/subscriptions/<SubscriptionID>/resourceGroups/TestRG4/providers/Microsoft.Network/virtualNetworkGateways/VNet4GW"
	LocalNetworkGateway2Text   :
	PeerText                   :
	ResourceGroupName          : TestRG1
	Location                   : eastus
	ResourceGuid               : 173489d1-37e2-482c-b8b8-6ca69fc3e069
	Tag                        : {}
	TagsTable                  :
	Name                       : VNet1toVNet4
	Id                         : /subscriptions/<SubscriptionID>/resourceGroups/TestRG1/providers/Micr osoft.Network/connections/VNet1toVNet4

## <a name="difsub"></a>異なるサブスクリプション内にある VNet を接続する方法

次の構成手順では、異なるサブスクリプションにある VNet 間の接続を追加し、TestVNet1 から TestVNet5 に接続します。ここでの違いは、特に 2 つのサブスクリプションが異なる組織に属するとき、2 つ目のサブスクリプションとの関連で、構成手順の一部を別個の PowerShell セッションで実行する必要があることです。次の手順を完了すると、下の図のような構成が完成します。

![異なるサブスクリプションの VNet2VNet](./media/vpn-gateway-vnet-vnet-rm-ps/differentsubscription.png)

以下の指示は、前の手順からの続きになります。TestVNet1 と TestVNet1 の VPN ゲートウェイを作成し、構成するには、[手順 1.](#Step1) と[手順 2.](#Step2) を完了する必要があります。サブスクリプションの異なる VNet のみを接続する予定の場合、前の演習の手順 3 と 4 は省略し、以下の手順 5 に進むことができます。

### 手順 5 - 追加の IP アドレス範囲を確認する

新しい仮想ネットワークである TestVNet5 の IP アドレス スペースが自分の VNet 範囲やローカルのネットワーク ゲートウェイ範囲と重ならないようにします。

この例は、仮想ネットワークが異なる組織に属することも想定されています。この演習では、TestVNet5 に次の値を使用します。

**TestVNet5 の値:**

- VNet の名前: TestVNet5
- リソース グループ: TestRG5
- 場所: 東日本
- TestVNet5: 10.51.0.0/16 & 10.52.0.0/16
- FrontEnd: 10.51.0.0/24
- BackEnd: 10.52.0.0/24
- GatewaySubnet: 10.52.255.0.0/27
- DNS サーバー: 8.8.8.8
- GatewayName: VNet5GW
- パブリック IP: VNet5GWIP
- VPN の種類: RouteBased
- 接続: VNet5toVNet1
- 接続の種類: VNet2VNet

**TestVNet1 の追加の値:**

- 接続: VNet1toVNet5


### 手順 6 - TestVNet5 を作成し、構成する

この手順は新しいサブスクリプションとの関連で実行する必要があります。この部分は、サブスクリプションを所有する別の組織の管理者が実行することがあります。

1. 変数を宣言する

	値は実際の構成で使用する値に置換します。

		$Sub5          = "Replace_With_the_New_Subcription_Name"
		$RG5           = "TestRG5"
		$Location5     = "Japan East"
		$VnetName5     = "TestVNet5"
		$FESubName5    = "FrontEnd"
		$BESubName5    = "Backend"
		$GWSubName5    = "GatewaySubnet"
		$VnetPrefix51  = "10.51.0.0/16"
		$VnetPrefix52  = "10.52.0.0/16"
		$FESubPrefix5  = "10.51.0.0/24"
		$BESubPrefix5  = "10.52.0.0/24"
		$GWSubPrefix5  = "10.52.255.0/27"
		$DNS5          = "8.8.8.8"
		$GWName5       = "VNet5GW"
		$GWIPName5     = "VNet5GWIP"
		$GWIPconfName5 = "gwipconf5"
		$Connection51  = "VNet5toVNet1"

2. サブスクリプション 5 に接続する

	PowerShell コンソールを開き、アカウントに接続します。接続するには、次のサンプルを参照してください。

		Login-AzureRmAccount

	アカウントのサブスクリプションを確認します。

		Get-AzureRmSubscription 

	使用するサブスクリプションを指定します。

		Select-AzureRmSubscription -SubscriptionName $Sub5

3. 新しいリソース グループを作成する

		New-AzureRmResourceGroup -Name $RG5 -Location $Location5

4. TestVNet4 のサブネット構成を作成する
	
		$fesub5 = New-AzureRmVirtualNetworkSubnetConfig -Name $FESubName5 -AddressPrefix $FESubPrefix5
		$besub5 = New-AzureRmVirtualNetworkSubnetConfig -Name $BESubName5 -AddressPrefix $BESubPrefix5
		$gwsub5 = New-AzureRmVirtualNetworkSubnetConfig -Name $GWSubName5 -AddressPrefix $GWSubPrefix5

5. TestVNet5 を作成する

		New-AzureRmVirtualNetwork -Name $VnetName5 -ResourceGroupName $RG5 -Location $Location5 -AddressPrefix $VnetPrefix51,$VnetPrefix52 -Subnet $fesub5,$besub5,$gwsub5

6. パブリック IP アドレスの要求

		$gwpip5    = New-AzureRmPublicIpAddress -Name $GWIPName5 -ResourceGroupName $RG5 -Location $Location5 -AllocationMethod Dynamic


7. ゲートウェイ構成の作成

		$vnet5     = Get-AzureRmVirtualNetwork -Name $VnetName5 -ResourceGroupName $RG5
		$subnet5   = Get-AzureRmVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet5
		$gwipconf5 = New-AzureRmVirtualNetworkGatewayIpConfig -Name $GWIPconfName5 -Subnet $subnet5 -PublicIpAddress $gwpip5

8. TestVNet5 ゲートウェイを作成する

		New-AzureRmVirtualNetworkGateway -Name $GWName5 -ResourceGroupName $RG5 -Location $Location5 -IpConfigurations $gwipconf5 -GatewayType Vpn -VpnType RouteBased -GatewaySku Standard

### 手順 7 - ゲートウェイを接続する

この例では、ゲートウェイが異なるサブスクリプションにあるため、[Subscription 1] ならびに [Subscription 5] というマークの付いた 2 つの PowerShell セッションにこの手順を分割しました。

1. **[Subscription 1]** サブスクリプション 1 の仮想ネットワーク ゲートウェイを取得する

	ログインし、サブスクリプション 1 に接続します。

		$vnet1gw = Get-AzureRmVirtualNetworkGateway -Name $GWName1 -ResourceGroupName $RG1

	次の要素の出力をコピーし、メールや他の方法でサブスクリプション 5 の管理者に送信します。

		$vnet1gw.Name
		$vnet1gw.Id

	これら 2 つの要素には、次のサンプル出力ような値が与えられます。

		PS D:> $vnet1gw.Name
		VNet1GW
		PS D:> $vnet1gw.Id
		/subscriptions/b636ca99-6f88-4df4-a7c3-2f8dc4545509/resourceGroupsTestRG1/providers/Microsoft.Network/virtualNetworkGateways/VNet1GW

2. **[Subscription 5]** サブスクリプション 5 の仮想ネットワーク ゲートウェイを取得する

	ログインし、サブスクリプション 5 に接続します。

		$vnet5gw = Get-AzureRmVirtualNetworkGateway -Name $GWName5 -ResourceGroupName $RG5

	次の要素の出力をコピーし、メールや他の方法でサブスクリプション 1 の管理者に送信します。

		$vnet5gw.Name
		$vnet5gw.Id

	これら 2 つの要素には、次のサンプル出力ような値が与えられます。

		PS C:\> $vnet5gw.Name
		VNet5GW
		PS C:\> $vnet5gw.Id
		/subscriptions/66c8e4f1-ecd6-47ed-9de7-7e530de23994/resourceGroups/TestRG5/providers/Microsoft.Network/virtualNetworkGateways/VNet5GW

3. **[Subscription 1]** TestVNet1 から TestVNet5 への接続を作成する

	この手順では、TestVNet1 から TestVNet5 への接続を作成します。ここでの違いは、サブスクリプションが異なるため、$vnet5gw を直接取得できないことです。上の手順でサブスクリプション 1 から伝えられた値で新しい PowerShell オブジェクトを作成する必要があります。名前、ID、共有キーを実際の値に置き換えてください。両方の接続の共有キーが一致することが重要です。接続の作成完了までしばらくかかります。

	サブスクリプション 1 に接続します。
	
		$vnet5gw = New-Object Microsoft.Azure.Commands.Network.Models.PSVirtualNetworkGateway
		$vnet5gw.Name = "VNet5GW"
		$vnet5gw.Id   = "/subscriptions/66c8e4f1-ecd6-47ed-9de7-7e530de23994/resourceGroups/TestRG5/providers/Microsoft.Network/virtualNetworkGateways/VNet5GW"
		$Connection15 = "VNet1toVNet5"
		New-AzureRmVirtualNetworkGatewayConnection -Name $Connection15 -ResourceGroupName $RG1 -VirtualNetworkGateway1 $vnet1gw -VirtualNetworkGateway2 $vnet5gw -Location $Location1 -ConnectionType Vnet2Vnet -SharedKey 'AzureA1b2C3'

4. **[Subscription 5]** TestVNet5 から TestVNet1 への接続を作成する

	この手順は上記の手順と同じですが、TestVNet5 から TestVNet1 への接続になります。サブスクリプション 1 から取得した値に基づいて PowerShell オブジェクトを作成するという同じプロセスがここでも適用されます。この手順では、共有キーが一致することを確認します。

	サブスクリプション 5 に接続します。

		$vnet1gw = New-Object Microsoft.Azure.Commands.Network.Models.PSVirtualNetworkGateway
		$vnet1gw.Name = "VNet1GW"
		$vnet1gw.Id   = "/subscriptions/b636ca99-6f88-4df4-a7c3-2f8dc4545509/resourceGroups/TestRG1/providers/Microsoft.Network/virtualNetworkGateways/VNet1GW "
		New-AzureRmVirtualNetworkGatewayConnection -Name $Connection51 -ResourceGroupName $RG5 -VirtualNetworkGateway1 $vnet5gw -VirtualNetworkGateway2 $vnet1gw -Location $Location5 -ConnectionType Vnet2Vnet -SharedKey 'AzureA1b2C3'

5. 接続を検証する

	以上の手順を完了したら、「[VNet 間の接続を検証する方法](#Verify)」で紹介されている方法で接続を確認できます。

## 次のステップ

- 接続が完成したら、仮想ネットワークに仮想マシンを追加することができます。手順については、[仮想マシンの作成](../virtual-machines/virtual-machines-windows-hero-tutorial.md)に関するページを参照してください。
- BGP の詳細については、[BGP の概要](vpn-gateway-bgp-overview.md)に関するページと [BGP の構成方法](vpn-gateway-bgp-resource-manager-ps.md)に関するページを参照してください。

<!---HONumber=AcomDC_0720_2016-->