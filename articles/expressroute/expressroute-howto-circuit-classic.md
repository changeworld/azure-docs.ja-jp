<properties
   pageTitle="PowerShell を使用した ExpressRoute 回線の構成手順 | Microsoft Azure"
   description="この記事では、ExpressRoute 回線の作成およびプロビジョニング手順について説明します。この記事では回線の状態確認、更新、または削除およびプロビジョニング解除の方法も示します。"
   documentationCenter="na"
   services="expressroute"
   authors="cherylmc"
   manager="carolz"
   editor=""
   tags="azure-service-management"/>
<tags
   ms.service="expressroute"
   ms.devlang="na"
   ms.topic="article" 
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="01/16/2016"
   ms.author="cherylmc"/>

# PowerShell を使用した ExpressRoute 回線の作成と変更

> [AZURE.SELECTOR]
[PowerShell - Classic](expressroute-howto-circuit-classic.md)
[PowerShell - Resource Manager](expressroute-howto-circuit-arm.md)

この記事では、PowerShell コマンドレットとクラシック デプロイメント モデルを使用して、ExpressRoute 回線を作成する手順について説明します。以下の手順では、ExpressRoute 回線の状態確認、更新、または削除およびプロビジョニング解除の方法も示します。

[AZURE.INCLUDE [vpn-gateway-sm-rm](../../includes/vpn-gateway-sm-rm-include.md)]


## 構成の前提条件

- Azure PowerShell モジュールの最新バージョンが必要になります。[Azure ダウンロード ページ](https://azure.microsoft.com/downloads/)の PowerShell セクションから、最新の PowerShell モジュールをダウンロードすることができます。Azure PowerShell モジュールを使用するようにコンピューターを構成する方法の手順を示す、[Azure PowerShell をインストールして構成する方法](../powershell-install-configure.md)の手順に従ってください。 
- 構成を開始する前に、必ず、[前提条件](expressroute-prerequisites.md)ページと[ワークフロー](expressroute-workflows.md)ページを確認してください。

## ExpressRoute 回線を作成してプロビジョニングするには

1. **ExpressRoute 用の PowerShell モジュールをインポートします。**

 	ExpressRoute コマンドレットの使用を開始するには、PowerShell セッションに Azure および ExpressRoute モジュールをインポートする必要があります。PowerShell セッションに Azure および ExpressRoute モジュールをインポートするには、次のコマンドを実行します。

	    Import-Module 'C:\Program Files (x86)\Microsoft SDKs\Azure\PowerShell\ServiceManagement\Azure\Azure.psd1'
	    Import-Module 'C:\Program Files (x86)\Microsoft SDKs\Azure\PowerShell\ServiceManagement\Azure\ExpressRoute\ExpressRoute.psd1'

2. **プロバイダー、ロケーション、およびサポートされている帯域幅のリストを取得します。**

	ExpressRoute 回線を作成する前に、接続プロバイダー、サポートされている場所、帯域幅オプションのリストが必要になります。PowerShell コマンドレット *Get-AzureDedicatedCircuitServiceProvider* を実行すると、この情報が返されます。この情報は後の手順で使用します。コマンドレットを実行すると、結果は次の例のようになります。

		PS C:\> Get-AzureDedicatedCircuitServiceProvider

		Name                 DedicatedCircuitLocations      DedicatedCircuitBandwidths                                                                                                                                                                                   
		----                 -------------------------      --------------------------                                                                                                                                                                                   
		Aryaka Networks      Silicon Valley,Washington      200Mbps:200, 500Mbps:500, 1Gbps:1000                                                                                                                                                                         
		                     DC,Singapore                                                                                                                                                                                                                                
		AT&T                 Silicon Valley,Washington DC   10Mbps:10, 50Mbps:50, 100Mbps:100, 500Mbps:500, 1Gbps:1000                                                                                                                                                   
		AT&T Netbond         Washington DC,Silicon          10Mbps:10, 50Mbps:50, 100Mbps:100, 500Mbps:500, 1Gbps:1000                                                                                                                                                   
		                     Valley,Dallas                                                                                                                                                                                                                               
		British Telecom      London,Amsterdam,Washington    10Mbps:10, 50Mbps:50, 100Mbps:100, 500Mbps:500, 1Gbps:1000                                                                                                                                                   
		                     DC,Tokyo,Silicon Valley                                                                                                                                                                                                                     
		Colt Ethernet        Amsterdam,London               200Mbps:200, 500Mbps:500, 1Gbps:1000, 10Gbps:10000                                                                                                                                                           
		Colt IPVPN           Amsterdam,London               10Mbps:10, 50Mbps:50, 100Mbps:100, 500Mbps:500, 1Gbps:1000                                                                                                                                                   
		Comcast              Washington DC,Silicon Valley   200Mbps:200, 500Mbps:500, 1Gbps:1000, 10Gbps:10000                                                                                                                                                           
		Equinix              Amsterdam,Atlanta,Chicago,Dall 200Mbps:200, 500Mbps:500, 1Gbps:1000, 10Gbps:10000                                                                                                                                                           
		                     as,New York,Seattle,Silicon                                                                                                                                                                                                                 
		                     Valley,Washington                                                                                                                                                                                                                           
		                     DC,London,Hong Kong,Singapore,                                                                                                                                                                                                              
		                     Sydney,Tokyo,Sao Paulo,Los                                                                                                                                                                                                                  
		                     Angeles,Melbourne                                                                                                                                                                                                                           
		IIJ                  Tokyo                          10Mbps:10, 50Mbps:50, 100Mbps:100, 500Mbps:500, 1Gbps:1000                                                                                                                                                   
		InterCloud           Washington                     200Mbps:200, 500Mbps:500, 1Gbps:1000, 10Gbps:10000                                                                                                                                                           
		                     DC,London,Singapore,Amsterdam                                                                                                                                                                                                               
		Internet Solutions   London,Amsterdam               10Mbps:10, 50Mbps:50, 100Mbps:100, 500Mbps:500, 1Gbps:1000                                                                                                                                                   
		– Cloud Connect                                                                                                                                                                                                                                                  
		Interxion            Amsterdam                      200Mbps:200, 500Mbps:500, 1Gbps:1000, 10Gbps:10000                                                                                                                                                           
		Level 3              London,Chicago,Dallas,Seattle, 200Mbps:200, 500Mbps:500, 1Gbps:1000, 10Gbps:10000                                                                                                                                                           
		Communications -     Silicon Valley,Washington DC                                                                                                                                                                                                                
		Exchange                                                                                                                                                                                                                                                         
		Level 3              London,Chicago,Dallas,Seattle, 10Mbps:10, 50Mbps:50, 100Mbps:100, 500Mbps:500, 1Gbps:1000                                                                                                                                                   
		Communications -     Silicon Valley,Washington DC                                                                                                                                                                                                                
		IPVPN                                                                                                                                                                                                                                                            
		Megaport             Melbourne,Sydney               200Mbps:200, 500Mbps:500, 1Gbps:1000, 10Gbps:10000                                                                                                                                                           
		NEXTDC               Melbourne                      200Mbps:200, 500Mbps:500, 1Gbps:1000, 10Gbps:10000                                                                                                                                                           
		NTT Communications   Tokyo                          10Mbps:10, 50Mbps:50, 100Mbps:100, 500Mbps:500, 1Gbps:1000                                                                                                                                                   
		Orange               Amsterdam,London,Silicon       10Mbps:10, 50Mbps:50, 100Mbps:100, 500Mbps:500, 1Gbps:1000                                                                                                                                                   
		                     Valley,Washington DC,Hong Kong                                                                                                                                                                                                              
		PCCW Global Limited  Hong Kong                      10Mbps:10, 50Mbps:50, 100Mbps:100, 500Mbps:500, 1Gbps:1000                                                                                                                                                   
		SingTel Domestic     Singapore                      10Mbps:10, 50Mbps:50, 100Mbps:100, 500Mbps:500, 1Gbps:1000                                                                                                                                                   
		SingTel              Singapore                      10Mbps:10, 50Mbps:50, 100Mbps:100, 500Mbps:500, 1Gbps:1000                                                                                                                                                   
		International                                                                                                                                                                                                                                                    
		Tata Communications  Hong Kong,Singapore,London,Ams 10Mbps:10, 50Mbps:50, 100Mbps:100, 500Mbps:500, 1Gbps:1000                                                                                                                                                   
		                     terdam,Chennai,Mumbai                                                                                                                                                                                                                       
		TeleCity Group       Amsterdam,London               200Mbps:200, 500Mbps:500, 1Gbps:1000, 10Gbps:10000                                                                                                                                                           
		Telstra Corporation  Sydney,Melbourne               10Mbps:10, 50Mbps:50, 100Mbps:100, 500Mbps:500, 1Gbps:1000                                                                                                                                                   
		Verizon              London,Hong Kong,Silicon       10Mbps:10, 50Mbps:50, 100Mbps:100, 500Mbps:500, 1Gbps:1000                                                                                                                                                   
		                     Valley,Washington DC                                                                                                                                                                                                                        
		Vodafone             London                         10Mbps:10, 50Mbps:50, 100Mbps:100, 500Mbps:500, 1Gbps:1000                                                                                                                                                   
		Zayo Group           Washington DC                  200Mbps:200, 500Mbps:500, 1Gbps:1000, 10Gbps:10000                                                                                                                                                           
    	

3. **ExpressRoute 回線を作成します。**

	以下の例では、Silicon Valley の Equinix を通じて 200 Mbps の ExpressRoute 回線を作成する方法を示します。別のプロバイダーと設定を使用する場合は、要求を実行するときにその情報に置き換えてください。

	以下に、新しいサービス キーの要求の例を示します。

		#Creating a new circuit
		$Bandwidth = 200
		$CircuitName = "MyTestCircuit"
		$ServiceProvider = "Equinix"
		$Location = "Silicon Valley"

		New-AzureDedicatedCircuit -CircuitName $CircuitName -ServiceProviderName $ServiceProvider -Bandwidth $Bandwidth -Location $Location -sku Standard -BillingType MeteredData 

	または、Premium アドオンを使用して ExpressRoute 回線を作成する場合は、次の例を使用します。Premium アドオンの詳細については、「[ExpressRoute の FAQ](expressroute-faqs.md)」ページを参照してください。

		New-AzureDedicatedCircuit -CircuitName $CircuitName -ServiceProviderName $ServiceProvider -Bandwidth $Bandwidth -Location $Location -sku Premium - BillingType MeteredData
	
	
	応答にはサービス キーが含まれます。以下を実行することで、すべてのパラメーターの詳細な説明を取得できます。

		get-help new-azurededicatedcircuit -detailed 

4. **すべての ExpressRoute 回線の一覧を表示します。**

	*Get-AzureDedicatedCircuit* を実行することで、作成したすべての ExpressRoute 回線の一覧を取得できます。

		#Getting service key
		Get-AzureDedicatedCircuit

	応答は、以下の例のようになります。

		Bandwidth                        : 200
		CircuitName                      : MyTestCircuit
		Location                         : Silicon Valley
		ServiceKey                       : *********************************
		ServiceProviderName              : equinix
		ServiceProviderProvisioningState : NotProvisioned
		Sku                              : Standard
		Status                           : Enabled

	この情報は、*Get-AzureDedicatedCircuit* コマンドレットを使用していつでも取得できます。パラメーターを指定せずに呼び出しを実行すると、すべての回線が一覧表示されます。サービス キーは *ServiceKey* フィールドに一覧表示されます。

		PS C:\> Get-AzureDedicatedCircuit

		Bandwidth                        : 200
		CircuitName                      : MyTestCircuit
		Location                         : Silicon Valley
		ServiceKey                       : *********************************
		ServiceProviderName              : equinix
		ServiceProviderProvisioningState : NotProvisioned
		Sku                              : Standard
		Status                           : Enabled

	以下を実行することで、すべてのパラメーターの詳細な説明を取得できます。

		get-help get-azurededicatedcircuit -detailed 

5. **プロビジョニングのためにサービス キーを接続プロバイダーに送信します。**

	新しい ExpressRoute 回線を作成する場合、この回線は次の状態になります。
	
		ServiceProviderProvisioningState : NotProvisioned
		
		Status                           : Enabled

	*ServiceProviderProvisioningState* はサービス プロバイダー側でのプロビジョニングの現在の状態に関する情報を提供し、Statusは Microsoft 側での状態を示します。ExpressRoute 回線をユーザーが使用できるように、次の状態にする必要があります。

		ServiceProviderProvisioningState : Provisioned
		
		Status                           : Enabled

	回線は、接続プロバイダーが有効にしている間、次の状態になります。

		ServiceProviderProvisioningState : Provisioned
		
		Status                           : Enabled



5. **回線キーのステータスと状態を定期的に確認します。**

	これにより、プロバイダーがいつ回線を有効にしたのかが分かります。回線が構成されると、以下の例に示すように、*ServiceProviderProvisioningState* が *Provisioned* と表示されます。

		PS C:\> Get-AzureDedicatedCircuit

		Bandwidth                        : 200
		CircuitName                      : MyTestCircuit
		Location                         : Silicon Valley
		ServiceKey                       : *********************************
		ServiceProviderName              : equinix
		ServiceProviderProvisioningState : Provisioned
		Sku                              : Standard
		Status                           : Enabled

6. **ルーティング構成を作成します。**
	
	詳しい手順については、[ExpressRoute 回線のルーティング構成 (回線ピアリングの作成と変更)](expressroute-howto-routing-classic.md) に関するページを参照してください。

7. **ExpressRoute 回線への VNet のリンク**

	次に、ExpressRoute 回線に VNet をリンクします。詳しい手順については、[VNet への ExpressRoute 回線のリンク](expressroute-howto-linkvnet-classic.md)に関するページを参照してください。ExpressRoute のクラシック デプロイメント モデルを使用して仮想ネットワークを作成する必要がある場合は、「[ExpressRoute 用 VNet の作成](expressroute-howto-vnet-portal-classic.md)」を参照してください。

##  ExpressRoute 回線の状態を取得するには

この情報は、*Get-AzureCircuit* コマンドレットを使用していつでも取得できます。パラメーターを指定せずに呼び出しを実行すると、すべての回線が一覧表示されます。

		PS C:\> Get-AzureDedicatedCircuit

		Bandwidth                        : 200
		CircuitName                      : MyTestCircuit
		Location                         : Silicon Valley
		ServiceKey                       : *********************************
		ServiceProviderName              : equinix
		ServiceProviderProvisioningState : Provisioned
		Sku                              : Standard
		Status                           : Enabled

		Bandwidth                        : 1000
		CircuitName                      : MyAsiaCircuit
		Location                         : Singapore
		ServiceKey                       : #################################
		ServiceProviderName              : equinix
		ServiceProviderProvisioningState : Provisioned
		Sku                              : Standard
		Status                           : Enabled

呼び出しに対するパラメーターとしてサービス キーを渡すことで、特定の ExpressRoute 回線に関する情報を取得できます。

		PS C:\> Get-AzureDedicatedCircuit -ServiceKey "*********************************"

		Bandwidth                        : 200
		CircuitName                      : MyTestCircuit
		Location                         : Silicon Valley
		ServiceKey                       : *********************************
		ServiceProviderName              : equinix
		ServiceProviderProvisioningState : Provisioned
		Sku                              : Standard
		Status                           : Enabled


以下を実行することで、すべてのパラメーターの詳細な説明を取得できます。

		get-help get-azurededicatedcircuit -detailed 

##  ExpressRoute 回線を変更するには

ExpressRoute 回線の特定のプロパティは、接続に影響を与えることなく変更できます。

以下の操作を行うことができます。

- ダウンタイムを発生させることなく、ExpressRoute 回線の ExpressRoute Premium アドオンを有効または無効にする。
- ダウンタイムを発生させることなく、ExpressRoute 回線の帯域幅を増やす。

制限および制約事項の詳細は、「[ExpressRoute の FAQ](expressroute-faqs.md)」ページを参照してください。

### ExpressRoute Premium アドオンを有効にする方法

次の PowerShell コマンドレットを使用して、既存の回線の ExpressRoute Premium アドオンを有効にできます。
	
		PS C:\> Set-AzureDedicatedCircuitProperties -ServiceKey "*********************************" -Sku Premium
		
		Bandwidth                        : 1000
		CircuitName                      : TestCircuit
		Location                         : Silicon Valley
		ServiceKey                       : *********************************
		ServiceProviderName              : equinix
		ServiceProviderProvisioningState : Provisioned
		Sku                              : Premium
		Status                           : Enabled

これで、回線の ExpressRoute Premium アドオン機能が有効になります。このコマンドが正常に実行された時点で、Premium アドオン機能の課金が始まることに注意してください。

### ExpressRoute Premium アドオンを無効にする方法

次の PowerShell コマンドレットを使用して、既存の回線の ExpressRoute Premium アドオンを無効にできます。
	
		PS C:\> Set-AzureDedicatedCircuitProperties -ServiceKey "*********************************" -Sku Standard
		
		Bandwidth                        : 1000
		CircuitName                      : TestCircuit
		Location                         : Silicon Valley
		ServiceKey                       : *********************************
		ServiceProviderName              : equinix
		ServiceProviderProvisioningState : Provisioned
		Sku                              : Standard
		Status                           : Enabled

これで回線の Premium アドオンが無効になります。

>[AZURE.IMPORTANT] 標準回線で許可されるリソースより多くのリソースを使用する場合、この操作は失敗することがあります。
>
>- Premium から標準にダウングレードする前に、回線にリンクされている仮想ネットワークの数が 10 未満であることを確認する必要があります。そうしないと、更新要求は失敗し、Premium 料金が課金されます。
- 他の地理的リージョンではすべての仮想ネットワークのリンクを解除する必要があります。そうしないと、更新要求は失敗し、Premium 料金が課金されます。
- プライベート ピアリングの場合、ルート テーブルのサイズを 4000 ルート未満にする必要があります。ルート テーブルのサイズが 4000 ルートを超える場合、BGP セッションがドロップし、アドバタイズされたプレフィックスの数が 4000 未満になるまで再度有効になりません。


### ExpressRoute 回線の帯域幅を更新する方法

プロバイダーでサポートされている帯域幅のオプションについては、「[ExpressRoute の FAQ](expressroute-faqs.md)」ページを確認してください。既存の回線のサイズを超えるサイズを選択することができます。必要なサイズを決定した後、次のコマンドを使用して、回線のサイズを変更することができます。

		PS C:\> Set-AzureDedicatedCircuitProperties -ServiceKey ********************************* -Bandwidth 1000
		
		Bandwidth                        : 1000
		CircuitName                      : TestCircuit
		Location                         : Silicon Valley
		ServiceKey                       : *********************************
		ServiceProviderName              : equinix
		ServiceProviderProvisioningState : Provisioned
		Sku                              : Standard
		Status                           : Enabled

回線のサイズは、Microsoft 側で増やされます。接続プロバイダーに連絡し、この変更に合わせて接続プロバイダー側の構成を更新するよう求める必要があります。更新された帯域幅のオプションの課金は、この時点から開始されます。

>[AZURE.IMPORTANT] 中断せずに ExpressRoute 回線の帯域幅を減らすことはできません。帯域幅をダウングレードするには、ExpressRoute 回線のプロビジョニングを解除してから、新しい ExpressRoute 回線を再度プロビジョニングする必要があります。

##  ExpressRoute 回線を削除してプロビジョニング解除するには

ExpressRoute 回線は、次のコマンドを実行して削除できます。

		PS C:\> Remove-AzureDedicatedCircuit -ServiceKey "*********************************"	

この操作を正常に行うには、ExpressRoute からすべての仮想ネットワークのリンクを解除する必要があることに注意してください。この操作が失敗した場合は、回線にリンクされている仮想ネットワークがないか確認してください。

ExpressRoute 回線サービス プロバイダーのプロビジョニング状態が有効の場合、状態は有効状態から*無効化中*に移ります。サービス プロバイダー側の回線のプロビジョニングを解除するには、サービス プロバイダーに連絡する必要があります。サービス プロバイダーが回線のプロビジョニング解除を完了し、通知するまで、リソースの予約と課金は続行されます。

ユーザーが上記のコマンドレットを実行する前にサービス プロバイダーが回路のプロビジョニングを解除済み (サービス プロバイダーのプロビジョニング状態が*未プロビジョニング*に設定されている) の場合、回線のプロビジョニングが解除され、課金が停止します。

## 次のステップ

- [ルーティングの構成](expressroute-howto-routing-classic.md)

<!---HONumber=AcomDC_0204_2016-->