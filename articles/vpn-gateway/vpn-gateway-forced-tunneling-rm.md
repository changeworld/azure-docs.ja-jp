<properties pageTitle="リソース マネージャーを使用して VPN Gateway の強制トンネリングを構成する | Microsoft Azure" description="仮想ネットワークにクロスプレミス VPN Gateway を使用していればリダイレクトしたり、インターネット宛てのすべてのトラフィックをオンプレミスの場所に戻るように "強制" したりできます。この記事は、リソース マネージャー デプロイメント モデルを対象としています。" services="vpn-gateway" documentationCenter="na" authors="cherylmc" manager="carolz" editor="" tags="azure-resource-manager"/>
<tags  
   ms.service="vpn-gateway"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="11/17/2015"
   ms.author="cherylmc" />

# PowerShell および Azure リソース マネージャーを使用した強制トンネリングの構成

この記事は、Azure リソース マネージャー デプロイメント モデルを使用して作成された VNet および VPN ゲートウェイを対象としています。サービス管理デプロイメント モデルを使用して、強制トンネリングを構成する場合、「[強制トンネリングの構成](vpn-gateway-about-forced-tunneling.md)」を参照してください。

[AZURE.INCLUDE [vpn-gateway-sm-rm](../../includes/vpn-gateway-sm-rm-include.md)]

## 強制トンネリングについて

強制トンネリングによって、検査および監査には、サイト間の VPN トンネルを介して、インターネットへのすべてのトラフィックをオンプレミス ロケーションにリダイレクトまたは "強制" できます。これは、ほとんどの企業 IT ポリシーの重要なセキュリティ要件です。強制トンネリングを使用しない場合、Azure の VM からインターネットへのトラフィックは、トラフィックを検査または監査できるオプションを使用せずに、常に Azure ネットワーク インフラストラクチャからインターネットへ直接トラバースします。認証されていないインターネット アクセスは、情報の漏えいまたは他の種類のセキュリティ侵害を招く可能性があります。

以下の図には、強制トンネリングのしくみを示しています。

![強制トンネリング](./media/vpn-gateway-forced-tunneling-rm/forced-tunnel.png)

上記の例では、フロントエンドのサブネットは、トンネリングを強制されません。フロントエンドのサブネット内のワークロードは、直接、インターネットから顧客の要求を承認し応答し続けることができます。Mid-tier およびMid-tier のサブネットは、トンネリングを強制されます。これら 2 つのサブネットからのインターネットへのオウトバウンド接続は、S2S VPN トンネルの 1 つを介して、オンプレミス サイトに ”強制” リダイレクトされます。これにより、必要な多層サービス アーキテクチャが継続的に使用可能になっている間は、Azure Virtual Machines やクラウド サービスからのインターネット アクセスを制限および検査することができます。また、仮想ネットワーク内に、インターネットに接続されたワークロードがない場合、全体の仮想ネットワークに強制トンネリングを適用するオプションがあります。

## 要件と考慮事項

Azure では、強制トンネリングは仮想ネットワークのユーザー定義ルートを使用して構成されます。オンプレミス サイトへのトラフィックのリダイレクトは、Azure VPN Gateway への既定のルートとして表されます。ユーザー定義のルートおよび仮想ネットワークの詳細については、「[ユーザー定義のルートと IP 転送](../virtual-network/virtual-networks-udr-overview.md)」を参照してください。

- 各仮想ネットワーク サブネットには、システム ルーティング テーブルが組み込まれています。システム ルーティング テーブルには、次の 3 つのグループがあります。

	- **ローカル VNet ルーティング:** 直接、同じ仮想ネットワーク内の宛先 VM へ
	
	- **オンプレミス ルート:** Azure VPN ゲートウェイへ
	
	- **既定のルート:**直接、インターネットへ。前の 2 つのルートが網羅していないプライベート IP アドレスへ宛先送信されるパケットは削除されることに注意してください。

-  この手順では、ユーザー定義ルート (UDR) を使用することにより、既定のルートを追加するルーティング テーブルを作成し、そのルーティング テーブルを、ご使用の VNet サブネットに関連付け、それらのサブネットでの強制トンネリングを有効にします。

- 強制トンネリングは、ルートベースの VPN ゲートウェイを持つ VNet に関連付ける必要があります。仮想ネットワークに接続されたクロスプレミス ローカル サイト間で「既定のサイト」を設定する必要があります。

- ExpressRoute の強制トンネリングは、このメカニズムを介して構成されていませんが、代わりに ExpressRoute BGP ピアリング セッションを介して既定ルートを告知することで有効になっています。詳細については、[ExpressRoute Documentation](https://azure.microsoft.com/documentation/services/expressroute/) を参照してください。

## 強制トンネリングについて

### 構成の概要

次の手順は、リソース グループと VNet の作成に役立ちます。その後、VPN Gateway を作成し、強制トンネリングを構成します。

この例では、仮想ネットワークである ”MultiTier-VNet” には、*Frontend*、*Midtier*、および*Backend* の 3 つのサブネットがあり、 *DefaultSiteHQ*、および 3 つの*Branch* の計 4 つのクロス プレミス接続があります。以下の手順で *DefaultSiteHQ* を強制トンネリングの既定のサイト接続として設定し、強制トンネリングが使用されるように *Midtier* と *Backend* を構成します。

	
### 作業を開始する前に

構成を開始する前に、以下がそろっていることを確認します。

- Azure サブスクリプション。Azure サブスクリプションを持っていない場合は、[MSDN サブスクライバーの特典](http://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/)を有効にするか、[無料試用版](http://azure.microsoft.com/pricing/free-trial/)にサインアップしてください。

- Azure PowerShell コマンドレット (1.0 以上)。このバージョンは、[ダウンロード ページ](http://azure.microsoft.com/downloads/)の「Windows PowerShell」セクションからダウンロードしてインストールできます。このドキュメントは、PowerShell 1.0 以降を対象に記述しています。この構成に必要なコマンドレットは、以前のバージョンにはありません。

- Azure リソース マネージャーおよび PowerShell の使用方法に精通していない場合は、[この記事](../articles/powershell-azure-resource-manager.md)を参照してください。

### 構成の手順

1. PowerShell コンソールで、Azure アカウントにログインします。このコマンドレットは、Azure アカウントのログイン資格情報をユーザーに求めます。ログイン後にアカウント設定がダウンロードされるため、Azure PowerShell で使用できるようになります。

		Login-AzureRmAccount 

2. Azure サブスクリプションの一覧を取得します。

		Get-AzureRmSubscription

2. 使用するサブスクリプションを指定します。

		Select-AzureRmSubscription -Subscriptionid "GUID of subscription"
		
3. リソース グループを作成します。

		New-AzureRmResourceGroup -Name "ForcedTunneling" -Location "North Europe"

4. 仮想ネットワークを作成し、サブネットを指定します。

		$s1 = New-AzureRmVirtualNetworkSubnetConfig -Name "Frontend" -AddressPrefix "10.1.0.0/24"
		$s2 = New-AzureRmVirtualNetworkSubnetConfig -Name "Midtier" -AddressPrefix "10.1.1.0/24"
		$s3 = New-AzureRmVirtualNetworkSubnetConfig -Name "Backend" -AddressPrefix "10.1.2.0/24"
		$s4 = New-AzureRmVirtualNetworkSubnetConfig -Name "GatewaySubnet" -AddressPrefix "10.1.200.0/28"
		$vnet = New-AzureRmVirtualNetwork -Name "MultiTier-VNet" -Location "North Europe" -ResourceGroupName "ForcedTunneling" -AddressPrefix "10.1.0.0/16" -Subnet $s1,$s2,$s3,$s4

5. ローカル ネットワーク ゲートウェイを作成します。

		$lng1 = New-AzureRmLocalNetworkGateway -Name "DefaultSiteHQ" -ResourceGroupName "ForcedTunneling" -Location "North Europe" -GatewayIpAddress "111.111.111.111" -AddressPrefix "192.168.1.0/24"
		$lng2 = New-AzureRmLocalNetworkGateway -Name "Branch1" -ResourceGroupName "ForcedTunneling" -Location "North Europe" -GatewayIpAddress "111.111.111.112" -AddressPrefix "192.168.2.0/24"
		$lng3 = New-AzureRmLocalNetworkGateway -Name "Branch2" -ResourceGroupName "ForcedTunneling" -Location "North Europe" -GatewayIpAddress "111.111.111.113" -AddressPrefix "192.168.3.0/24"
		$lng4 = New-AzureRmLocalNetworkGateway -Name "Branch3" -ResourceGroupName "ForcedTunneling" -Location "North Europe" -GatewayIpAddress "111.111.111.114" -AddressPrefix "192.168.4.0/24"
		
6. ルート テーブルおよびルート規則を作成します。

		New-AzureRmRouteTable –Name "MyRouteTable" -ResourceGroupName "ForcedTunneling" –Location "North Europe"
		$rt = Get-AzureRmRouteTable –Name "MyRouteTable" -ResourceGroupName "ForcedTunneling" 
		Add-AzureRmRouteConfig -Name "DefaultRoute" -AddressPrefix "0.0.0.0/0" -NextHopType VirtualNetworkGateway -RouteTable $rt
		Set-AzureRmRouteTable -RouteTable $rt


6. ルート テーブルを Midtier および Backend サブネットに関連付けます。

		$vnet = Get-AzureRmVirtualNetwork -Name "MultiTier-Vnet" -ResourceGroupName "ForcedTunneling"
		Set-AzureRmVirtualNetworkSubnetConfig -Name "MidTier" -VirtualNetwork $vnet -AddressPrefix "10.1.1.0/24" -RouteTable $rt
		Set-AzureRmVirtualNetworkSubnetConfig -Name "Backend" -VirtualNetwork $vnet -AddressPrefix "10.1.2.0/24" -RouteTable $rt
		Set-AzureRmVirtualNetwork -VirtualNetwork $vnet

9. 既定のサイトでゲートウェイを作成します。この手順の完了には、ゲートウェイを作成および構成するため 20 分以上かかる場合があります。コンソールでいくつかコマンドレットを実行するだけのように見えますが、バックグラウンドでは多数の事柄が行われます。GatewayDefaultSite は強制したルーティング構成を動作させるコマンドレット パラメーターです。これは省略したくないでしょう。これは、PowerShell 1.0 以降でのみ利用できます。

		$pip = New-AzureRmPublicIpAddress -Name "GatewayIP" -ResourceGroupName "ForcedTunneling" -Location "North Europe" -AllocationMethod Dynamic
		$gwsubnet = Get-AzureRmVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet
		$ipconfig = New-AzureRmVirtualNetworkGatewayIpConfig -Name "gwIpConfig" -SubnetId $gwsubnet.Id -PublicIpAddressId $pip.Id
		New-AzureRmVirtualNetworkGateway -Name "Gateway1" -ResourceGroupName "ForcedTunneling" -Location "North Europe" -IpConfigurations $ipconfig -GatewayType Vpn -VpnType RouteBased -GatewayDefaultSite $lng1 -EnableBgp $false

10. サイト間 VPN 接続の確立

		$gateway = Get-AzureRmVirtualNetworkGateway -Name "Gateway1" -ResourceGroupName "ForcedTunneling"
		$lng1 = Get-AzureRmLocalNetworkGateway -Name "DefaultSiteHQ" -ResourceGroupName "ForcedTunneling" 
		$lng2 = Get-AzureRmLocalNetworkGateway -Name "Branch1" -ResourceGroupName "ForcedTunneling" 
		$lng3 = Get-AzureRmLocalNetworkGateway -Name "Branch2" -ResourceGroupName "ForcedTunneling" 
		$lng4 = Get-AzureRmLocalNetworkGateway -Name "Branch3" -ResourceGroupName "ForcedTunneling" 

		New-AzureRmVirtualNetworkGatewayConnection -Name "Connection1" -ResourceGroupName "ForcedTunneling" -Location "North Europe" -VirtualNetworkGateway1 $gateway -LocalNetworkGateway2 $lng1 -ConnectionType IPsec -SharedKey "preSharedKey"
		New-AzureRmVirtualNetworkGatewayConnection -Name "Connection2" -ResourceGroupName "ForcedTunneling" -Location "North Europe" -VirtualNetworkGateway1 $gateway -LocalNetworkGateway2 $lng2 -ConnectionType IPsec -SharedKey "preSharedKey"
		New-AzureRmVirtualNetworkGatewayConnection -Name "Connection3" -ResourceGroupName "ForcedTunneling" -Location "North Europe" -VirtualNetworkGateway1 $gateway -LocalNetworkGateway2 $lng3 -ConnectionType IPsec -SharedKey "preSharedKey"
		New-AzureRmVirtualNetworkGatewayConnection -Name "Connection4" -ResourceGroupName "ForcedTunneling" -Location "North Europe" -VirtualNetworkGateway1 $gateway -LocalNetworkGateway2 $lng4 -ConnectionType IPsec -SharedKey "preSharedKey"

		Get-AzureRmVirtualNetworkGatewayConnection -Name "Connection1" -ResourceGroupName "ForcedTunneling"
		

<!---HONumber=AcomDC_1203_2015-->