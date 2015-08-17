<properties 
   pageTitle="NSP との Expressroute の構成"
   description="このチュートリアルでは、NSP を通じた ExpressRoute の設定について説明します。"
   documentationCenter="na"
   services="expressroute"
   authors="cherylmc"
   manager="adinah"
   editor="tysonn"/>

<tags 
   ms.service="expressroute"
   ms.devlang="na"
   ms.topic="article" 
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services" 
   ms.date="06/29/2015"
   ms.author="cherylmc"/>

#  ネットワーク サービス プロバイダーによる ExpressRoute 接続の構成

ネットワーク サービス プロバイダーを通じて ExpressRoute 接続を構成するには、複数の手順を正しい順序で完了する必要があります。これらの手順は、次の各操作を行う際に役立ちます。

- ExpressRoute 回線の作成と管理
- ExpressRoute 回線への Virtual Network のリンク

##  構成の前提条件


構成を開始する前に、次の前提条件を満たしていることを確認してください。

- Azure サブスクリプション
- Windows PowerShell の最新バージョン
- Virtual Network に関する次の要件:
	- Azure の仮想ネットワークで使用される IP アドレス プレフィックスのセット。
	- オンプレミスの IP プレフィックスのセット (パブリック IP アドレスを含むことが可能)。
	- Virtual Network ゲートウェイは、/28 サブネットで作成されている必要があります。
	- 仮想ネットワークの外部の、追加の IP プレフィックスのセット (/28)。これはルートを構成するために使用されます。これをネットワーク サービス プロバイダーに提供する必要があります。
	- ネットワークの AS 番号。これをネットワーク サービス プロバイダーに提供する必要があります。プライベート AS 番号を使用できます。これを使用する場合は、65000 を超える AS 番号が必要です。AS 番号の詳細については、自立システム (AS) 番号に関するページを参照してください。 

- ネットワーク サービス プロバイダーから、次のようにします。
	- ExpressRoute でサポートされている VPN サービスが必要です。既存の VPN サービスが適応しているか、ネットワーク サービス プロバイダーに確認します。

##  デプロイメント ワークフロー

![ネットワーク サービス プロバイダーのワークフロー](./media/expressroute-configuring-nsps/expressroute-nsp-connectivity-workflow.png)

##  PowerShell を使用して設定を構成する
Windows PowerShell は、Azure でのワークロードのデプロイメントと管理を制御し、自動化するために使用できる、強力なスクリプティング環境です。詳細については、[MSDN](https://msdn.microsoft.com/library/windowsazure/jj156055.aspx) にある PowerShell のドキュメントを参照してください。



1. **ExpressRoute 用の PowerShell モジュールをインポートします。**

		Import-Module 'C:\Program Files (x86)\Microsoft SDKs\Azure\PowerShell\ServiceManagement\Azure\Azure.psd1'
		Import-Module 'C:\Program Files (x86)\Microsoft SDKs\Azure\PowerShell\ServiceManagement\Azure\ExpressRoute\ExpressRoute.psd1' 

2. **プロバイダー、ロケーション、およびサポートされている帯域幅のリストを取得します。**

	回線を作成する前に、サービス プロバイダー、サポートされているロケーション、各ロケーションで選択できる帯域幅のリストが必要になります。次の PowerShell コマンドレットを実行すると、この情報が返されます。この情報は、後のステップで使用します。

		PS C:\> Get-AzureDedicatedCircuitServiceProvider

	返される情報は、次の例のようになります。

		PS C:\> Get-AzureDedicatedCircuitServiceProvider
	
		Name                 DedicatedCircuitLocations      DedicatedCircuitBandwidths                                                                                                                                                                                   
		----                 -------------------------      --------------------------                                                                                                                                                                                   
		AT&T                 Silicon Valley,Washington DC   10Mbps:10, 50Mbps:50, 100Mbps:100, 500Mbps:500, 1Gbps:1000                                                                                                                                                   
		British Telecom      London,Amsterdam               10Mbps:10, 50Mbps:50, 100Mbps:100, 500Mbps:500, 1Gbps:1000                                                                                                                                                   
		Equinix              Amsterdam,Atlanta,Chicago,Dall 200Mbps:200, 500Mbps:500, 1Gbps:1000, 10Gbps:10000                                                                                                                                                           
		                     as,New York,Seattle,Silicon                                                                                                                                                                                                                 
		                     Valley,Washington                                                                                                                                                                                                                           
		                     DC,London,Hong                                                                                                                                                                                                                              
		                     Kong,Singapore,Sydney,Tokyo                                                                                                                                                                                                                 
		IIJ                  Tokyo                          10Mbps:10, 50Mbps:50, 100Mbps:100, 500Mbps:500, 1Gbps:1000                                                                                                                                                   
		Level 3              London,Silicon                 200Mbps:200, 500Mbps:500, 1Gbps:1000                                                                                                                                                                         
		Communications -     Valley,Washington DC                                                                                                                                                                                                                        
		Exchange                                                                                                                                                                                                                                                         
		Level 3              London,Silicon                 10Mbps:10, 50Mbps:50, 100Mbps:100, 500Mbps:500, 1Gbps:1000                                                                                                                                                   
		Communications -     Valley,Washington DC                                                                                                                                                                                                                        
		IPVPN                                                                                                                                                                                                                                                            
		Orange               Amsterdam,London               10Mbps:10, 50Mbps:50, 100Mbps:100, 500Mbps:500, 1Gbps:1000                                                                                                                                                   
		SingTel Domestic     Singapore                      10Mbps:10, 50Mbps:50, 100Mbps:100, 500Mbps:500, 1Gbps:1000                                                                                                                                                   
		SingTel              Singapore                      10Mbps:10, 50Mbps:50, 100Mbps:100, 500Mbps:500, 1Gbps:1000                                                                                                                                                   
		International                                                                                                                                                                                                                                                    
		TeleCity Group       Amsterdam,London               200Mbps:200, 500Mbps:500, 1Gbps:1000, 10Gbps:10000                                                                                                                                                           
		Telstra Corporation  Sydney                         10Mbps:10, 50Mbps:50, 100Mbps:100, 500Mbps:500, 1Gbps:1000                                                                                                                                                   
		Verizon              Silicon Valley,Washington DC   10Mbps:10, 50Mbps:50, 100Mbps:100, 500Mbps:500, 1Gbps:1000
		

3. **サービス キーを要求し、ネットワーク サービス プロバイダーに渡します。**

	この要求を行うには、PowerShell コマンドレットを使用します。この例では、AT&T Netbond をサービス プロバイダーとして使用しており、シリコン バレーで 50 Mbps の ExpressRoute 回線を指定しています。別のプロバイダーと設定を使用する場合は、要求を実行するときにその情報に置き換えてください。

	以下に、新しいサービス キーの要求の例を示します。

		##Creating a new circuit
		$Bandwidth = 50
		$CircuitName = "NetBondSVTest"
		$ServiceProvider = "at&t"
		$Location = "Silicon Valley"
		
		New-AzureDedicatedCircuit -CircuitName $CircuitName -ServiceProviderName $ServiceProvider -Bandwidth $Bandwidth -Location $Location
		
		#Getting service key
		Get-AzureDedicatedCircuit

	応答は、以下の例のようになります。

		Bandwidth                        : 50
		CircuitName                      : NetBondSVTest
		Location                         : Silicon Valley
		ServiceKey                       : *********************************
		ServiceProviderName              : at&t
		ServiceProviderProvisioningState : NotProvisioned
		Status                           : Enabled

	この情報は、Get-AzureCircuit コマンドレットを使用していつでも取得できます。パラメーターを指定せずに呼び出しを実行すると、すべての回線が一覧表示されます。サービス キーは ServiceKey フィールドに表示されます。

		PS C:\> Get-AzureDedicatedCircuit
		
		Bandwidth                        : 500
		CircuitName                      : NetBondSVTest
		Location                         : Silicon Valley
		ServiceKey                       : 00-0000-0000-0000-0000000000
		ServiceProviderName              : at&t
		ServiceProviderProvisioningState : NotProvisioned
		Status                           : Enabled

	この手順が完了したら、Azure のストレージおよびその他のサービスに接続できます。



4. **Virtual Network とゲートウェイを構成します。**

	「[ExpressRoute 用の仮想ネットワークとゲートウェイの構成](https://msdn.microsoft.com/library/azure/dn643737.aspx)」を参照してください。ExpressRoute 接続を使用するために、ゲートウェイ サブネットは /28 にする必要があります。

5. **ネットワークを回線にリンクします。**

	次のようになっていることを確認してから、次の手順に進みます。
 
	- ServiceProviderState: Provisioned
	- Status: Enabled

	少なくとも 1 つの Azure 仮想ネットワークでゲートウェイが作成済みであることを確認します。ゲートウェイが実行されている必要があります。

		PS C:\> $Vnet = "MyTestVNet"
		New-AzureDedicatedCircuitLink -ServiceKey $ServiceKey -VNetName $Vnet
		
		Provisioned 

<!---HONumber=August15_HO6-->