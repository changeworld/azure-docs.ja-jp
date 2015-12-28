<properties 
   pageTitle="ExpressRoute 回線への仮想ネットワークのリンク |Microsoft Azure"
   description="このドキュメントでは、ExpressRoute 回線に仮想ネットワーク (VNet) をリンクする方法の概要を示します。"
   services="expressroute"
   documentationCenter="na"
   authors="ganesr"
   manager="carolz"
   editor=""
   tags="azure-service-management"/>
<tags 
   ms.service="expressroute"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="12/09/2015"
   ms.author="ganesr" />

# ExpressRoute 回線への仮想ネットワークのリンク

> [AZURE.SELECTOR]
- [PowerShell - Classic](expressroute-howto-linkvnet-classic.md)
- [PowerShell - Resource Manager] (expressroute-howto-linkvnet-arm.md)
- [Template - Resource Manager](https://github.com/Azure/azure-quickstart-templates/tree/ecad62c231848ace2fbdc36cbe3dc04a96edd58c/301-expressroute-circuit-vnet-connection)

この記事では、ExpressRoute 回線に仮想ネットワーク (VNet) をリンクする方法の概要を示します。仮想ネットワークは、同じサブスクリプションにあっても、別のサブスクリプションの一部であってもかいまいません。この記事は、クラシック デプロイメント モデルを使用してデプロイされた VNet に適用されます。Azure リソース マネージャーのデプロイメント方法を使用してデプロイされた仮想ネットワークをリンクする場合は、テンプレートを使用して行うことができます。テンプレートに移動する場合は、上のタブを参照してください。

[AZURE.INCLUDE [vpn-gateway-sm-rm](../../includes/vpn-gateway-sm-rm-include.md)]

## 構成の前提条件

- Azure PowerShell モジュールの最新バージョン (バージョン 1.0 以降) が必要です。 
- 構成を開始する前に、必ず、[前提条件](expressroute-prerequisites.md)ページ、[ルーティングの要件](expressroute-routing.md)ページおよび[ワークフロー](expressroute-workflows.md) ページを確認してください。
- アクティブな ExpressRoute 回線が必要です。手順に従って、[ExpressRoute 回線を作成](expressroute-howto-circuit-classic.md)し、接続プロバイダー経由で回線を有効にしてから続行してください。ExpressRoute 回線をプロビジョニングされ、有効になっている状態にする必要があります。そうすれば、以下で説明されているコマンドレットを実行できます。
- アクティブな ExpressRoute 回線が必要です。 
	- 手順に従って、[ExpressRoute 回線を作成](expressroute-howto-circuit-arm.md)し、接続プロバイダー経由で回線を有効にしてください。 
	- 回線用に Azure プライベート ピアリングが構成されていることを確認してください。ルーティング手順については、[ルーティングの構成](expressroute-howto-routing-arm.md)に関する記事を参照してください。 
	- Azure プライベート ピアリングを構成する必要があります。また、ネットワークと Microsoft 間の BGP ピアリングを起動して、エンド ツー エンド接続を有効にする必要があります。
	- 仮想ネットワークと仮想ネットワーク ゲートウェイを作成して完全にプロビジョニングする必要があります。手順に従って [VPN Gateway](../articles/vpn-gateway-create-site-to-site-rm-powershell.md) を作成してください。

最大 10 個の仮想ネットワークを ExpressRoute 回線に接続できます。ExpressRoute 回線はすべて同じ地理的リージョンにある必要があります。ExpressRoute Premium アドオンを有効にした場合は、ExpressRoute 回線に多数の仮想ネットワークをリンクできます。Premium アドオンの詳細については、[FAQ](expressroute-faqs.md) を確認してください。

## 同じ Azure サブスクリプションの VNet を ExpressRoute 回線に接続する

次のコマンドレットを使用して、ExpressRoute 回線に仮想ネットワーク ゲートウェイを接続できます。コマンドレットを実行する前に、仮想ネットワーク ゲートウェイが作成されており、リンクの準備ができていることを確認してください。

	$circuit = Get-AzureRmExpressRouteCircuit -Name "MyCircuit" -ResourceGroupName "MyRG"
	$gw = Get-AzureRmVirtualNetworkGateway -Name "ExpressRouteGw" -ResourceGroupName "MyRG"
	$connection = New-AzureRmVirtualNetworkGatewayConnection -Name "ERConnection" -ResourceGroupName "MyRG" -Location "East US" -VirtualNetworkGateway1 $gw -PeerId $circuit.Id -ConnectionType ExpressRoute

## 異なる Azure サブスクリプションの仮想ネットワークを ExpressRoute 回線に接続する

複数のサブスクリプションで ExpressRoute 回線を共有することができます。下図に、複数のサブスクリプションで ExpressRoute 回線を共有するしくみについて概略を示します。大規模クラウド内のそれぞれの小規模クラウドは、組織内のさまざまな部門に属するサブスクリプションを表すために使用されています。組織内の各部門はサービスのデプロイ用に固有のサブスクリプションを使用できますが、1 つの ExpressRoute 回線を共有することで、オンプレミス ネットワークに接続し直すことができます。1 つの部門 (この例では IT) で ExpressRoute 回線を所有できます。組織内の他のサブスクリプションも ExpressRoute 回線を使用できます。

>[AZURE.NOTE]専用回線の接続と帯域幅の料金は、ExpressRoute 回線の所有者が負担することになります。すべての仮想ネットワークが同じ帯域幅を共有します。

![サブスクリプション間接続](./media/expressroute-howto-linkvnet-classic/cross-subscription.png)

### 管理

*回線所有者*は、ExpressRoute 回線リソースの権限のあるパワー ユーザーです。回線所有者は、*回線ユーザー*が利用できる承認を作成できます。*回線ユーザー*は、(ExpressRoute 回線とは別のサブスクリプション内にある) 仮想ネットワーク ゲートウェイの所有者です。*回線ユーザー*は、承認を利用できます (仮想ネットワークごとに 1 つの承認)。

*回線所有者*は、承認をいつでも変更し、取り消す権限を持っています。承認を取り消すと、アクセスが取り消されたサブスクリプションからすべての接続が削除されます。

### 回線所有者の操作 

#### 承認の作成
	
回線所有者は、承認を作成します。その結果、回線ユーザーが各自の仮想ネットワーク ゲートウェイを ExpressRoute 回線に接続するために使用できる承認キーが作成されます。承認は、1 つの接続に対してのみ有効です。

次のコマンドレット スニペットは、承認を作成する方法を示しています。

		Add-AzureRmExpressRouteCircuitAuthorization -Circuit $circuit -Name "MyAuthorization1"
		Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $circuit
		$circuit = Get-AzureRmExpressRouteCircuit -Name "MyCircuit" -ResourceGroupName "MyRG"

		$auth1 = Get-AzureRmExpressRouteCircuitAuthorization -Circuit $circuit -Name "MyAuthorization1"
		

このコマンドレットの応答に、承認キーと状態が含まれます。

		Name                   : MyAuthorization1
		Id                     : /subscriptions/&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&/resourceGroups/ERCrossSubTestRG/providers/Microsoft.Network/expressRouteCircuits/CrossSubTest/authorizations/MyAuthorization1
		Etag                   : &&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&& 
		AuthorizationKey       : ####################################
		AuthorizationUseStatus : Available
		ProvisioningState      : Succeeded

		

#### 承認の確認

回線所有者は、次のコマンドレットを実行して、特定の回線で発行されるすべての承認を確認できます。

	$circuit = Get-AzureRmExpressRouteCircuit -Name "MyCircuit" -ResourceGroupName "MyRG"
	$authorizations = Get-AzureRmExpressRouteCircuitAuthorization -Circuit $circuit
	

#### 承認の追加

回線所有者は、次のコマンドレットを使用して承認を追加できます。

	$circuit = Get-AzureRmExpressRouteCircuit -Name "MyCircuit" -ResourceGroupName "MyRG"
	Add-AzureRmExpressRouteCircuitAuthorization -Circuit $circuit -Name "MyAuthorization2"
	Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $circuit
	
	$circuit = Get-AzureRmExpressRouteCircuit -Name "MyCircuit" -ResourceGroupName "MyRG"
	$authorizations = Get-AzureRmExpressRouteCircuitAuthorization -Circuit $circuit

	
#### 承認の削除

回線所有者は、次のコマンドレットを実行して、ユーザーに対する承認を取り消したり削除したりすることができます。

	Remove-AzureRmExpressRouteCircuitAuthorization -Name "MyAuthorization2" -Circuit $circuit
	Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $circuit	

### 回線ユーザーの操作

回線ユーザーは、ピア ID と回線所有者が作成した承認キーを必要とします。回線キーは次に示すようになります。


承認キーは GUID です。

#### 接続承認の利用

回線ユーザーは以下のコマンドレットを実行して、リンク承認を適用することができます。

	$id = "/subscriptions/********************************/resourceGroups/ERCrossSubTestRG/providers/Microsoft.Network/expressRouteCircuits/MyCircuit"	
	$connection = New-AzureRmVirtualNetworkGatewayConnection -Name "ERConnection" -ResourceGroupName "RemoteResourceGroup -Location "East US" -VirtualNetworkGateway1 $gw -PeerId $id -ConnectionType ExpressRoute -AuthorizationKey "^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^"

#### 接続承認の解除

ExpressRoute 回線を仮想ネットワークにリンクしている接続を削除することで、承認を解除できます。

## 次のステップ

ExpressRoute の詳細については、「[ExpressRoute のFAQ](expressroute-faqs.md)」をご覧ください。

<!---HONumber=AcomDC_1217_2015-->