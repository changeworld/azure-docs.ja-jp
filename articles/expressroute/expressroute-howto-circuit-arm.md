<properties
   pageTitle="Azure リソース マネージャーと PowerShell を使用して ExpressRoute 回線を構成する | Microsoft Azure"
   description="この記事では、ExpressRoute 回線の作成およびプロビジョニング手順について説明します。この記事では回線の状態確認、更新、または削除およびプロビジョニング解除の方法も示します。"
   documentationCenter="na"
   services="expressroute"
   authors="cherylmc"
   manager="carolz"
   editor=""
   tags="azure-resource-manager"/>
<tags
   ms.service="expressroute"
   ms.devlang="na"
   ms.topic="article" 
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="01/26/2016"
   ms.author="cherylmc"/>

# Azure リソース マネージャーと PowerShell を使用して ExpressRoute 回線を作成および変更する

> [AZURE.SELECTOR]
[PowerShell - Classic](expressroute-howto-circuit-classic.md)
[PowerShell - Resource Manager](expressroute-howto-circuit-arm.md)

この記事では、PowerShell コマンドレットと Azure リソース マネージャー デプロイメント モデルを使用して、ExpressRoute 回線を作成する手順について説明します。以下の手順では、ExpressRoute 回線の状態確認、更新、または削除およびプロビジョニング解除の方法も示します。

[AZURE.INCLUDE [vpn-gateway-sm-rm](../../includes/vpn-gateway-sm-rm-include.md)]

## 構成の前提条件

- Azure PowerShell モジュールの最新バージョン (バージョン 1.0 以降) が必要です。Azure PowerShell モジュールを使用するようにコンピューターを構成する方法の手順を示す、[Azure PowerShell をインストールして構成する方法](../powershell-install-configure.md)の手順に従ってください。 
- 構成を開始する前に、必ず、[前提条件](expressroute-prerequisites.md)ページと[ワークフロー](expressroute-workflows.md)ページを確認してください。

## ExpressRoute 回線を作成してプロビジョニングするには

1. **ExpressRoute 用の PowerShell モジュールをインポートします。**

 	ExpressRoute コマンドレットを使用するには、[PowerShell ギャラリー](http://www.powershellgallery.com/)から最新の PowerShell インストーラーをインストールし、Azure リソース マネージャー モジュールを PowerShell セッションにインポートする必要があります。管理者として PowerShell を実行する必要があります。

	    Install-Module AzureRM

		Install-AzureRM

	既知のセマンティック バージョン範囲内の AzureRM.* モジュールをすべてインポートします。

		Import-AzureRM

	既知のセマンティック バージョン範囲内の選択したモジュールのみをインポートすることもできます。
		
		Import-Module AzureRM.Network 

	ご使用のアカウントにログオンします。

		Login-AzureRmAccount

	ExpressRoute 回線を作成するサブスクリプションを選択します。
		
		Select-AzureRmSubscription -SubscriptionId "<subscription ID>"
			


2. **プロバイダー、ロケーション、およびサポートされている帯域幅のリストを取得します。**

	ExpressRoute 回線を作成する前に、接続プロバイダー、サポートされている場所、帯域幅オプションのリストが必要になります。PowerShell コマンドレット *Get-AzureRmExpressRouteServiceProvider* を実行すると、この情報が返されます。この情報は後の手順で使用します。

		Get-AzureRmExpressRouteServiceProvider

	接続プロバイダーがそこにリストされているかどうかを確認します。回線を作成する際に必要になるため、以下の項目を書き留めておいてください。
	
	- 名前
	- PeeringLocations
	- BandwidthsOffered

	これで、ExpressRoute 回線を作成する準備が整いました。

		
3. **ExpressRoute 回線を作成します。**

	ExpressRoute 回線を作成する前に、リソース グループがまだない場合は作成しておく必要があります。リソース グループを作成するには、次のコマンドを実行します。

		New-AzureRmResourceGroup -Name “ExpressRouteResourceGroup” -Location "West US"

	以下の例では、Silicon Valley の Equinix を通じて 200 Mbps の ExpressRoute 回線を作成する方法を示します。別のプロバイダーと設定を使用する場合は、要求を実行するときにその情報に置き換えてください。

	以下に、新しいサービス キーの要求の例を示します。

		New-AzureRmExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup" -Location "West US" -SkuTier Standard -SkuFamily MeteredData -ServiceProviderName "Equinix" -PeeringLocation "Silicon Valley" -BandwidthInMbps 200

	必ず、適切な SKU レベルと SKU ファミリを指定してください。
 
	 - SKU レベルによって、ExpressRoute の Standard と Premium のどちらのアドオンが有効になるかが決まります。Standard SKU を取得する場合は *Standard*、Premium アドオンの場合は *Premium* を指定できます。
	 - SKU ファミリによって、課金の種類が決まります。従量制課金データ プランの場合は *metereddata*、無制限データ プランの場合は *unlimiteddata" を選択できます。**注:** 回線が作成された後で、課金の種類を変更することはできません。

	応答にはサービス キーが含まれます。以下を実行することで、すべてのパラメーターの詳細な説明を取得できます。

		Get-Help New-AzureRmExpressRouteCircuit -detailed 

4. **すべての ExpressRoute 回線の一覧を表示します。**

	*Get-AzureRmExpressRouteCircuit* コマンドを実行することで、作成したすべての ExpressRoute 回線の一覧を取得できます。

		
		Get-AzureRmExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"

	応答は、以下の例のようになります。

		Name                             : ExpressRouteARMCircuit
		ResourceGroupName                : ExpressRouteResourceGroup
		Location                         : westus
		Id                               : /subscriptions/***************************/resourceGroups/ExpressRouteResourceGroup/providers/Microsoft.Network/expressRouteCircuits/ExpressRouteARMCircuit
		Etag                             : W/"################################"
		ProvisioningState                : Succeeded
		Sku                              : {
		                                     "Name": "Standard_MeteredData",
		                                     "Tier": "Standard",
		                                     "Family": "MeteredData"
		                                   }
		CircuitProvisioningState         : Enabled
		ServiceProviderProvisioningState : NotProvisioned
		ServiceProviderNotes             : 
		ServiceProviderProperties        : {
		                                     "ServiceProviderName": "Equinix",
		                                     "PeeringLocation": "Silicon Valley",
		                                     "BandwidthInMbps": 200
		                                   }
		ServiceKey                       : **************************************
		Peerings                         : []


	この情報は、*Get-AzureRmExpressRouteCircuit* コマンドレットを使用していつでも取得できます。パラメーターを指定せずに呼び出しを実行すると、すべての回線が一覧表示されます。サービス キーは *ServiceKey* フィールドに一覧表示されます。

		Get-AzureRmExpressRouteCircuit

	応答は、以下の例のようになります。

		Name                             : ExpressRouteARMCircuit
		ResourceGroupName                : ExpressRouteResourceGroup
		Location                         : westus
		Id                               : /subscriptions/***************************/resourceGroups/ExpressRouteResourceGroup/providers/Microsoft.Network/expressRouteCircuits/ExpressRouteARMCircuit
		Etag                             : W/"################################"
		ProvisioningState                : Succeeded
		Sku                              : {
		                                     "Name": "Standard_MeteredData",
		                                     "Tier": "Standard",
		                                     "Family": "MeteredData"
		                                   }
		CircuitProvisioningState         : Enabled
		ServiceProviderProvisioningState : NotProvisioned
		ServiceProviderNotes             : 
		ServiceProviderProperties        : {
		                                     "ServiceProviderName": "Equinix",
		                                     "PeeringLocation": "Silicon Valley",
		                                     "BandwidthInMbps": 200
		                                   }
		ServiceKey                       : **************************************
		Peerings                         : []



	以下を実行することで、すべてのパラメーターの詳細な説明を取得できます。

		Get-Help Get-AzureRmExpressRouteCircuit -detailed 

5. **プロビジョニングのためにサービス キーを接続プロバイダーに送信します。**

	新しい ExpressRoute 回線を作成する場合、この回線は次の状態になります。
	
		ServiceProviderProvisioningState : NotProvisioned
		
		CircuitProvisioningState         : Enabled

	*ServiceProviderProvisioningState* はサービス プロバイダー側でのプロビジョニングの現在の状態に関する情報を提供し、Statusは Microsoft 側での状態を示します。ExpressRoute 回線をユーザーが使用できるように、次の状態にする必要があります。

		ServiceProviderProvisioningState : Provisioned
		
		CircuitProvisioningState         : Enabled

	回線は、接続プロバイダーが有効にしている間、次の状態になります。

		ServiceProviderProvisioningState : Provisioned
		
		Status                           : Enabled



6. **回線キーのステータスと状態を定期的に確認します。**

	これにより、プロバイダーがいつ回線を有効にしたのかが分かります。回線が構成されると、以下の例に示すように、*ServiceProviderProvisioningState* が *Provisioned* と表示されます。

		Get-AzureRmExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"

	応答は、以下の例のようになります。

		Name                             : ExpressRouteARMCircuit
		ResourceGroupName                : ExpressRouteResourceGroup
		Location                         : westus
		Id                               : /subscriptions/***************************/resourceGroups/ExpressRouteResourceGroup/providers/Microsoft.Network/expressRouteCircuits/ExpressRouteARMCircuit
		Etag                             : W/"################################"
		ProvisioningState                : Succeeded
		Sku                              : {
		                                     "Name": "Standard_MeteredData",
		                                     "Tier": "Standard",
		                                     "Family": "MeteredData"
		                                   }
		CircuitProvisioningState         : Enabled
		ServiceProviderProvisioningState : Provisioned
		ServiceProviderNotes             : 
		ServiceProviderProperties        : {
		                                     "ServiceProviderName": "Equinix",
		                                     "PeeringLocation": "Silicon Valley",
		                                     "BandwidthInMbps": 200
		                                   }
		ServiceKey                       : **************************************
		Peerings                         : []

7. **ルーティングを構成して VNet にリンクする**

	a.**ルーティング構成を作成します。** 詳細な手順については、「[PowerShell を使用した ExpressRoute 回線のルーティングの作成と変更](expressroute-howto-routing-arm.md)」をご覧ください。ルーティングの手順は、サービス プロバイダーが提供するレイヤー 2 接続サービスで作成された回線にのみ適用されることにご注意ください。サービス プロバイダーが提供する管理対象レイヤー 3 サービス (MPLS など、通常は IPVPN) を使用する場合、接続プロバイダーがユーザーに代わってルーティングを構成および管理します。そのような場合は、ユーザーがピアリングを作成したり、管理したりすることはできません。
	
	b.**VNet を ExpressRoute 回線にリンクします。** ルーティングが構成されていることを確認した後、VNet を ExpressRoute 回線にリンクする必要があります。詳細な手順については、「[ExpressRoute 回線への仮想ネットワークのリンク](expressroute-howto-linkvnet-arm.md)」をご覧ください。

##  ExpressRoute 回線の状態を取得するには

この情報は、*Get-AzureRmExpressRouteCircuit* コマンドレットを使用していつでも取得できます。パラメーターを指定せずに呼び出しを実行すると、すべての回線が一覧表示されます。

		Get-AzureRmExpressRouteCircuit

応答は次の例のようになります。

		Name                             : ExpressRouteARMCircuit
		ResourceGroupName                : ExpressRouteResourceGroup
		Location                         : westus
		Id                               : /subscriptions/***************************/resourceGroups/ExpressRouteResourceGroup/providers/Microsoft.Network/expressRouteCircuits/ExpressRouteARMCircuit
		Etag                             : W/"################################"
		ProvisioningState                : Succeeded
		Sku                              : {
		                                     "Name": "Standard_MeteredData",
		                                     "Tier": "Standard",
		                                     "Family": "MeteredData"
		                                   }
		CircuitProvisioningState         : Enabled
		ServiceProviderProvisioningState : Provisioned
		ServiceProviderNotes             : 
		ServiceProviderProperties        : {
		                                     "ServiceProviderName": "Equinix",
		                                     "PeeringLocation": "Silicon Valley",
		                                     "BandwidthInMbps": 200
		                                   }
		ServiceKey                       : **************************************
		Peerings                         : []

呼び出しに対するパラメーターとしてリソース グループ名と回線名を渡すことで、特定の ExpressRoute 回線に関する情報を取得できます。

		Get-AzureRmExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"


応答は、以下の例のようになります。

		Name                             : ExpressRouteARMCircuit
		ResourceGroupName                : ExpressRouteResourceGroup
		Location                         : westus
		Id                               : /subscriptions/***************************/resourceGroups/ExpressRouteResourceGroup/providers/Microsoft.Network/expressRouteCircuits/ExpressRouteARMCircuit
		Etag                             : W/"################################"
		ProvisioningState                : Succeeded
		Sku                              : {
		                                     "Name": "Standard_MeteredData",
		                                     "Tier": "Standard",
		                                     "Family": "MeteredData"
		                                   }
		CircuitProvisioningState         : Enabled
		ServiceProviderProvisioningState : Provisioned
		ServiceProviderNotes             : 
		ServiceProviderProperties        : {
		                                     "ServiceProviderName": "Equinix",
		                                     "PeeringLocation": "Silicon Valley",
		                                     "BandwidthInMbps": 200
		                                   }
		ServiceKey                       : **************************************
		Peerings                         : []

以下を実行することで、すべてのパラメーターの詳細な説明を取得できます。

		Get-Help Get-azurededicatedcircuit -detailed 

## ExpressRoute 回線を変更するには

ExpressRoute 回線の特定のプロパティは、接続に影響を与えることなく変更できます。制限および制約事項の詳細は、「[ExpressRoute の FAQ](expressroute-faqs.md)」ページを参照してください。

次の設定は、ダウンタイムを発生させずに変更できます。

- ダウンタイムを発生させることなく、ExpressRoute 回線の ExpressRoute Premium アドオンを有効または無効にする。
- ダウンタイムを発生させることなく、ExpressRoute 回線の帯域幅を増やす。



### ExpressRoute Premium アドオンを有効にする方法

次の PowerShell スニペットを使用して、既存の回線の ExpressRoute Premium アドオンを有効にすることができます。

		$ckt = Get-AzureRmExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"

		$ckt.Sku.Tier = "Premium"
		$ckt.sku.Name = "Premium_MeteredData"

		Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt
	
		
これで、回線の ExpressRoute Premium アドオン機能が有効になります。このコマンドが正常に実行された時点で、Premium アドオン機能の課金が始まることに注意してください。

### ExpressRoute Premium アドオンを無効にする方法

既存の回線の ExpressRoute Premium アドオンを無効にできます。ExpressRoute Premium アドオンを無効にする場合は、次の考慮事項に注意してください。

- Premium から標準にダウングレードする前に、回線にリンクされている仮想ネットワークの数が 10 未満であることを確認する必要があります。これに該当しない場合、更新要求は失敗し、Premium 料金が課金されます。
- 他の地理的リージョンではすべての仮想ネットワークのリンクを解除する必要があります。そうしないと、更新要求は失敗し、Premium 料金が課金されます。
- プライベート ピアリングの場合、ルート テーブルのサイズを 4000 ルート未満にする必要があります。ルート テーブルのサイズが 4000 ルートを超える場合、BGP セッションがドロップし、アドバタイズされたプレフィックスの数が 4000 未満になるまで再度有効になりません。

Premium アドオンを無効にするには、次の PowerShell コマンドレットの例を使用します。標準回線で許可されるリソースより多くのリソースを使用する場合、この操作は失敗することがあります。
	
		$ckt = Get-AzureRmExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"
		
		$ckt.Sku.Tier = "Standard"
		$ckt.sku.Name = "Standard_MeteredData"
		
		Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt

### ExpressRoute 回線の帯域幅を更新する方法

プロバイダーでサポートされている帯域幅のオプションについては、「[ExpressRoute の FAQ](expressroute-faqs.md)」ページを確認してください。既存の回線のサイズを**超える**サイズを選択できます。これによりダウンタイムが発生することはありません。

>[AZURE.IMPORTANT] 中断せずに ExpressRoute 回線の帯域幅を減らすことはできません。帯域幅をダウングレードするには、ExpressRoute 回線のプロビジョニングを解除してから、新しい ExpressRoute 回線を再度プロビジョニングする必要があります。

必要なサイズを決定した後、次のサンプルを使用して、回線のサイズを変更することができます。このコマンドレットを実行すると、Microsoft 側で回線のサイズが増やされます。接続プロバイダーに連絡し、この変更に合わせて接続プロバイダー側の構成を更新するよう求める必要があります。更新された帯域幅のオプションの課金は、この時点から開始されます。

		$ckt = Get-AzureRmExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"

		$ckt.ServiceProviderProperties.BandwidthInMbps = 1000

		Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt

## ExpressRoute 回線を削除してプロビジョニング解除するには

ExpressRoute 回線を削除できます。ExpressRoute 回線を削除するときは、次の考慮事項に注意してください。

- この操作を正常に行うには、ExpressRoute からすべての仮想ネットワークのリンクを解除する必要があります。この操作が失敗した場合は、回線にリンクされている仮想ネットワークがないか確認してください。

- ExpressRoute 回線サービス プロバイダーのプロビジョニング状態が有効の場合、状態は有効状態から*無効化中*に移ります。サービス プロバイダー側の回線のプロビジョニングを解除するには、サービス プロバイダーに連絡する必要があります。サービス プロバイダーが回線のプロビジョニング解除を完了し、通知するまで、リソースの予約と課金は続行されます。

- コマンドレットを実行する前にサービス プロバイダーが回路のプロビジョニングを解除済み (サービス プロバイダーのプロビジョニング状態が*未プロビジョニング*に設定されている) の場合、回線のプロビジョニングが解除され、課金が停止します。

ExpressRoute 回線を削除するには、次の PowerShell コマンドレットの例を使用します。

		Remove-AzureRmExpressRouteCircuit -ResourceGroupName "ExpressRouteResourceGroup" -Name "ExpressRouteARMCircuit"

## 次のステップ

回線を作成したら、次の操作を必ず実行します。

1.  [ExpressRoute 回線のルーティングの作成と変更を行う](expressroute-howto-routing-arm.md)
2.  [仮想ネットワークを ExpressRoute 回線にリンクする](expressroute-howto-linkvnet-arm.md)

<!---HONumber=AcomDC_0204_2016-->