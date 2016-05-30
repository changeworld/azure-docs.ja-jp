<properties
   pageTitle="Azure リソース マネージャーと PowerShell を使用して ExpressRoute 回線を作成および変更する | Microsoft Azure"
   description="この記事では、ExpressRoute 回線の作成、プロビジョニング、確認、更新、削除、プロビジョニング解除の方法について説明します。"
   documentationCenter="na"
   services="expressroute"
   authors="ganesr"
   manager="carmonm"
   editor=""
   tags="azure-resource-manager"/>
<tags
   ms.service="expressroute"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="04/15/2016"
   ms.author="ganesr"/>


# ExpressRoute 回線の作成と変更

> [AZURE.SELECTOR]
[Azure Portal - Resource Manager](expressroute-howto-circuit-portal-resource-manager.md)
[PowerShell - Resource Manager](expressroute-howto-circuit-arm.md)
[PowerShell - Classic](expressroute-howto-circuit-classic.md)


この記事では、Windows PowerShell コマンドレットと Azure リソース マネージャー デプロイ モデルを使用して、Azure ExpressRoute 回線を作成する方法について説明します。この記事では、回線の状態確認、更新、または削除およびプロビジョニング解除の方法も示します。

**Azure のデプロイ モデルについて**

[AZURE.INCLUDE [vpn-gateway-clasic-rm](../../includes/vpn-gateway-classic-rm-include.md)]

## 開始する前に


- Azure PowerShell モジュールの最新バージョン (バージョン 1.0 以降) を入手します。PowerShell モジュールを使用するようにコンピューターを構成する方法の手順については、「[Azure PowerShell のインストールと構成の方法](../powershell-install-configure.md)」の手順に従ってください。

- 構成を開始する前に、[前提条件](expressroute-prerequisites.md)と[ワークフロー](expressroute-workflows.md)を確認してください。

## ExpressRoute 回線の作成とプロビジョニング

### 1\.Azure アカウントにサインインしてサブスクリプションを選択する

構成を始めるには、Azure アカウントにサインインします。PowerShell の詳細については、「[リソース マネージャーでの Windows PowerShell の使用](../powershell-azure-resource-manager.md)」を参照してください。接続については、次の例を参照してください。

	Login-AzureRmAccount

アカウントのサブスクリプションを確認します。

	Get-AzureRmSubscription

ExpressRoute 回線を作成するサブスクリプションを選択します。

	Select-AzureRmSubscription -SubscriptionId "<subscription ID>"

### 2\.サポートされるプロバイダー、場所、帯域幅のリストを取得する

ExpressRoute 回線を作成する前に、サポートされている接続プロバイダー、場所、帯域幅オプションのリストが必要になります。

PowerShell コマンドレット `Get-AzureRmExpressRouteServiceProvider` を実行すると、この情報が返されます。この情報は、後のステップで使用します。

	Get-AzureRmExpressRouteServiceProvider

接続プロバイダーがそこにリストされているかどうかを確認します。以下の項目は、後で回線を作成する際に必要になるため、書き留めておいてください。

- 名前

- PeeringLocations

- BandwidthsOffered

これで、ExpressRoute 回線を作成する準備が整いました。

### 3\.ExpressRoute 回線の作成

リソース グループがまだない場合は、ExpressRoute 回線を作成する前に、作成しておく必要があります。リソース グループを作成するには、次のコマンドを実行します。


	New-AzureRmResourceGroup -Name "ExpressRouteResourceGroup" -Location "West US"


以下の例では、Silicon Valley の Equinix を通じて 200 Mbps の ExpressRoute 回線を作成する方法を示します。別のプロバイダーと設定を使用する場合は、要求を実行するときにその情報に置き換えてください。以下に、新しいサービス キーの要求の例を示します。

	New-AzureRmExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup" -Location "West US" -SkuTier Standard -SkuFamily MeteredData -ServiceProviderName "Equinix" -PeeringLocation "Silicon Valley" -BandwidthInMbps 200

必ず、適切な SKU レベルと SKU ファミリを指定してください。

- SKU レベルによって、ExpressRoute の Standard と Premium のどちらのアドオンが有効になるかが決まります。標準 SKU を取得する場合は *[Standard]* を、プレミアム アドオンの場合は *[Premium]* を指定できます。

- SKU ファミリによって、課金の種類が決まります。従量制課金データ プランの場合は *Metereddata*、無制限データ プランの場合は *Unlimiteddata* を指定できます。課金の種類を *Metereddata* から *Unlimiteddata* に変更することはできますが、*Unlimiteddata* から *Metereddata* に変更することはできません。


>[AZURE.IMPORTANT] ExpressRoute 回線の課金は、サービス キーが発行されたときから始まります。接続プロバイダーが回線をプロビジョニングする準備ができたら、この操作を実行します。

応答にはサービス キーが含まれます。以下を実行することで、すべてのパラメーターの詳細な説明を取得できます。


	get-help New-AzureRmExpressRouteCircuit -detailed


### 4\.すべての ExpressRoute 回線を一覧表示する

作成したすべての ExpressRoute 回線の一覧を取得するには、`Get-AzureRmExpressRouteCircuit` コマンドを実行します。


	Get-AzureRmExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"

応答は、次の例のようになります。


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
	CircuitProvisioningState          : Enabled
	ServiceProviderProvisioningState  : NotProvisioned
	ServiceProviderNotes              :
	ServiceProviderProperties         : {
	                                      "ServiceProviderName": "Equinix",
	                                      "PeeringLocation": "Silicon Valley",
	                                      "BandwidthInMbps": 200
	                                    }
	ServiceKey                        : **************************************
	Peerings                          : []

この情報は、`Get-AzureRmExpressRouteCircuit` コマンドレットを使用していつでも取得できます。パラメーターを指定せずに呼び出しを実行すると、すべての回線が一覧表示されます。サービス キーは *ServiceKey* フィールドに一覧表示されます。


	Get-AzureRmExpressRouteCircuit


応答は、次の例のようになります。


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


	get-help Get-AzureRmExpressRouteCircuit -detailed

### 5\.プロビジョニングのためにサービス キーを接続プロバイダーに送信する

*ServiceProviderProvisioningState* は、サービス プロバイダー側でのプロビジョニングの現在の状態に関する情報を提供します。Status は、Microsoft 側での状態を提供します。回線のプロビジョニング状態に関する詳細については、[ワークフロー](expressroute-workflows.md#expressroute-circuit-provisioning-states)に関する記事を参照してください。

新しい ExpressRoute 回線を作成する場合、この回線は次の状態になります。


	ServiceProviderProvisioningState : NotProvisioned
	CircuitProvisioningState         : Enabled



回線は、接続プロバイダーが有効にしている間、次の状態に変化します。

	ServiceProviderProvisioningState : Provisioning
	Status                           : Enabled

ExpressRoute 回線をユーザーが使用できるように、次の状態にする必要があります。

	ServiceProviderProvisioningState : Provisioned
	CircuitProvisioningState         : Enabled

### 6\.回線キーのステータスと状態を定期的に確認する

回線キーのステータスと状態をチェックすると、いつプロバイダーによって回線が有効にされたかがわかります。回線が構成されると、以下の例に示すように、*ServiceProviderProvisioningState* が *Provisioned* と表示されます。


	Get-AzureRmExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"


応答は、次の例のようになります。


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

### 7\.ルーティング構成を作成する

回線ピアリングの作成と変更の詳しい手順については、「[PowerShell を使用した ExpressRoute 回線のルーティングの作成と変更](expressroute-howto-routing-arm.md)」を参照してください。


>[AZURE.IMPORTANT] 次の手順は、サービス プロバイダーが提供するレイヤー 2 接続サービスで作成された回線にのみ適用されます。サービス プロバイダーが提供する管理対象レイヤー 3 サービス (MPLS など、通常は IP VPN) を使用する場合、接続プロバイダーがユーザーに代わってルーティングを構成および管理します。

### 8\.ExpressRoute 回線への仮想ネットワークのリンク

次に、ExpressRoute 回線に仮想ネットワークをリンクします。Resource Manager デプロイメント モデルを使用するときは、「[ExpressRoute 回線への仮想ネットワークのリンク](expressroute-howto-linkvnet-arm.md)」を参照してください。

## ExpressRoute 回線の状態の取得

この情報は、`Get-AzureRmExpressRouteCircuit` コマンドレットを使用していつでも取得できます。パラメーターを指定せずに呼び出しを実行すると、すべての回線が一覧表示されます。

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


応答は、次の例のようになります。


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

	get-help get-azurededicatedcircuit -detailed


## <a name="modify"></a>ExpressRoute 回線の変更

ExpressRoute 回線の特定のプロパティは、接続に影響を与えることなく変更できます。

ダウンタイムなく、次を実行できます。

- ExpressRoute 回線の ExpressRoute Premium アドオンを有効または無効にします。
- ExpressRoute 回線の帯域幅を増やす。回線の帯域幅のダウングレードはサポートされていないことに注意してください。
- 課金プランを従量制課金データから無制限データに変更します。無制限データから従量制課金データへの課金プランの変更はサポートされていないことに注意してください。
-  *従来の操作の許可*を有効または無効にできます。

制限と制約事項の詳細は、「[ExpressRoute の FAQ](expressroute-faqs.md)」を参照してください。

### ExpressRoute Premium アドオンを有効にするには

次の PowerShell スニペットを使用し、既存の回線の ExpressRoute Premium アドオンを有効にすることができます。

	$ckt = Get-AzureRmExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"

	$ckt.Sku.Tier = "Premium"
	$ckt.sku.Name = "Premium_MeteredData"

	Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt


これで、回線の ExpressRoute Premium アドオン機能が有効になります。このコマンドが正常に実行された時点で、Premium アドオン機能の課金が始まることに注意してください。

### ExpressRoute Premium アドオンを無効にするには

>[AZURE.IMPORTANT] 標準回線で許可されるリソースより多くのリソースを使用する場合、この操作は失敗することがあります。

以下の点に注意してください。

- Premium から Standard にダウングレードする前に、回線にリンクされている仮想ネットワークの数が 10 未満であることを確認する必要があります。10 以上になっていると、更新要求が失敗し、Premium 料金で課金されます。

- 他の地理的リージョンではすべての仮想ネットワークのリンクを解除する必要があります。解除しないと、更新要求が失敗し、Premium 料金で課金されます。

- プライベート ピアリングの場合、ルート テーブルのサイズを 4,000 ルート未満にする必要があります。ルート テーブルのサイズが 4,000 ルートを超える場合、BGP セッションがドロップし、アドバタイズされたプレフィックスの数が 4,000 未満になるまで再度有効になりません。

次の PowerShell コマンドレットを使用し、既存の回線の ExpressRoute Premium アドオンを無効にできます。


	$ckt = Get-AzureRmExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"

	$ckt.Sku.Tier = "Standard"
	$ckt.sku.Name = "Standard_MeteredData"

	Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt


### ExpressRoute 回線の帯域幅を更新するには

プロバイダーでサポートされている帯域幅のオプションについては、「[ExpressRoute の FAQ](expressroute-faqs.md)」を確認してください。既存の回線のサイズを超えるサイズを選択することができます。

>[AZURE.IMPORTANT] 中断せずに ExpressRoute 回線の帯域幅を減らすことはできません。帯域幅をダウングレードするには、ExpressRoute 回線のプロビジョニングを解除してから、新しい ExpressRoute 回線を再度プロビジョニングする必要があります。

必要なサイズを決定した後、次のコマンドを使用して、回線のサイズを変更できます。


	$ckt = Get-AzureRmExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"

	$ckt.ServiceProviderProperties.BandwidthInMbps = 1000

	Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt


回線のサイズは Microsoft 側で増やされます。次に、接続プロバイダーに連絡し、この変更に合わせて接続プロバイダー側の構成を更新するよう依頼する必要があります。その通知が完了すると、更新された帯域幅オプションでの課金が開始されます。


### SKU を従量制から無制限に変更するには

ExpressRoute 回線の SKU を変更するには、次の PowerShell スニペットを使用します。

	$ckt = Get-AzureRmExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"

	$ckt.Sku.Family = "UnlimitedData"
	$ckt.sku.Name = "Premium_UnlimitedData"

	Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt

### クラシック環境および Resource Manager 環境へのアクセスを制御するには  

「[クラシック デプロイメント モデルから Resource Manager デプロイメント モデルへの ExpressRoute 回線の移行](expressroute-howto-move-arm.md)」の説明を参照してください。


## ExpressRoute 回線の削除とプロビジョニング解除

以下の点に注意してください。

- ExpressRoute 回線からすべての仮想ネットワークのリンクを解除する必要があります。この操作が失敗した場合は、回線にリンクされている仮想ネットワークがないか確認してください。

- ExpressRoute 回線サービス プロバイダーのプロビジョニング状態が有効の場合、状態は有効状態から*無効化中*に移ります。サービス プロバイダー側の回線のプロビジョニングを解除するには、サービス プロバイダーに連絡する必要があります。Microsoft は、サービス プロバイダーが回線のプロビジョニング解除を完了し、通知するまで、リソースの予約と課金を続行します。

- ユーザーが上記のコマンドレットを実行する前にサービス プロバイダーが回路のプロビジョニングを解除済み (サービス プロバイダーのプロビジョニング状態が*未プロビジョニング*に設定されている) の場合、回線のプロビジョニングが解除され、課金が停止します。

ExpressRoute 回線は、次のコマンドを実行して削除できます。

	Remove-AzureRmExpressRouteCircuit -ResourceGroupName "ExpressRouteResourceGroup" -Name "ExpressRouteARMCircuit"



## 次のステップ

回線を作成したら、次の操作を必ず実行します。

- [ExpressRoute 回線のルーティングの作成と変更を行う](expressroute-howto-routing-arm.md)
- [仮想ネットワークを ExpressRoute 回線にリンクする](expressroute-howto-linkvnet-arm.md)

<!---HONumber=AcomDC_0518_2016-->