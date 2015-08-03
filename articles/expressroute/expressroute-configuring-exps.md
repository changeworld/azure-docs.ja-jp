<properties
   pageTitle="Exchange プロバイダーを通じた ExpressRoute の構成"
   description="このチュートリアルでは、Exchange プロバイダー (EXP) を通じた ExpressRoute の設定について説明します。"
   documentationCenter="na"
   services="expressroute"
   authors="cherylmc"
   manager="adinah"
   editor="tysonn" />
<tags
   ms.service="expressroute"
   ms.devlang="na"
   ms.topic="hero-article" 
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="04/29/2015"
   ms.author="cherylmc"/>

#  Exchange プロバイダーを通じて ExpressRoute 接続を構成する

Exchange プロバイダーを通じて ExpressRoute 接続を構成するには、複数の手順を正しい順序で完了する必要があります。これらの手順は、次の各操作を行う際に役立ちます。

- ExpressRoute 回線の作成と管理
- ExpressRoute 回線用の BGP ピアリングの構成
- ExpressRoute 回線への Virtual Network のリンク

##  構成の前提条件


構成を開始する前に、次の前提条件を満たしていることを確認してください。

- Azure サブスクリプション
- Azure PowerShell の最新バージョン
- Virtual Network に関する次の要件:
	- Azure の仮想ネットワークで使用される IP アドレス プレフィックスのセット
	- オンプレミスの IP プレフィックスのセット (パブリック IP アドレスを含むことが可能)
	- Virtual Network ゲートウェイは、/28 サブネットで作成されている必要があります。
	- 仮想ネットワークの外部の、追加の IP プレフィックスのセット (/28)。これは、BGP ピアリングの構成に使用されます。
	- ネットワークの AS 番号。AS 番号の詳細については、[自立システム (AS) 番号](http://www.iana.org/assignments/as-numbers/as-numbers.xhtml)に関するページを参照してください。
	- MD5 ハッシュ (認証済み BGP セッションが必要な場合)
	- トラフィックの送信に使用される VLAN ID。回線ごとに 2 つの VLAN ID が必要になります。1 つは仮想ネットワーク用で、もう 1 つはパブリック IP アドレスでホストされるサービス用です。
	- ネットワークの[自立システム (AS) 番号](http://www.iana.org/assignments/as-numbers/as-numbers.xhtml)。
	- Exchange プロバイダーのイーサネット交換への、2 つの 1 Gbps/10 Gbps クロス接続。
	- ルーティングのために BGP をサポートできるルーターのペア

##  デプロイメント ワークフロー


![](./media/expressroute-configuring-exps/expressroute-exp-connectivity-workflow.png)

##  PowerShell を使用して設定を構成する

Windows PowerShell は、Azure でのワークロードのデプロイメントと管理を制御し、自動化するために使用できる、強力なスクリプティング環境です。詳細については、[MSDN](https://msdn.microsoft.com/library/windowsazure/jj156055.aspx) にある PowerShell のドキュメントを参照してください。

1. **ExpressRoute 用の PowerShell モジュールをインポートします。**

	    Import-Module 'C:\Program Files (x86)\Microsoft SDKs\Azure\PowerShell\ServiceManagement\Azure\Azure.psd1'
	    Import-Module 'C:\Program Files (x86)\Microsoft SDKs\Azure\PowerShell\ServiceManagement\Azure\ExpressRoute\ExpressRoute.psd1'

2. **プロバイダー、ロケーション、およびサポートされている帯域幅のリストを取得します。**

	回線を作成する前に、サービス プロバイダー、サポートされているロケーション、各ロケーションで選択できる帯域幅のリストが必要になります。次の PowerShell コマンドレットを実行すると、この情報が返されます。この情報は、後のステップで使用します。

    	PS C:\> Get-AzureDedicatedCircuitServiceProvider
		**The information returned will look similar to the example below:**


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

3. **専用回線を作成します。**

	以下の例では、Equinix Silicon Valley を通じて 200 Mbps の ExpressRoute 回線を作成する方法を示します。別のプロバイダーと設定を使用する場合は、要求を実行するときにその情報に置き換えてください。

	以下に、新しいサービス キーの要求の例を示します。

		#Creating a new circuit
		$Bandwidth = 200
		$CircuitName = "EquinixSVTest"
		$ServiceProvider = "Equinix"
		$Location = "Silicon Valley"

		New-AzureDedicatedCircuit -CircuitName $CircuitName -ServiceProviderName $ServiceProvider -Bandwidth $Bandwidth -Location $Location

		#Getting service key
		Get-AzureDedicatedCircuit

	応答は、以下の例のようになります。

		Bandwidth                        : 200
		CircuitName                      : EquinixSVTest
		Location                         : Silicon Valley
		ServiceKey                       : *********************************
		ServiceProviderName              : equinix
		ServiceProviderProvisioningState : NotProvisioned
		Status                           : Enabled

	この情報は、Get-AzureCircuit コマンドレットを使用していつでも取得できます。パラメーターを指定せずに呼び出しを実行すると、すべての回線が一覧表示されます。サービス キーは ServiceKey フィールドに表示されます。

		PS C:\> Get-AzureDedicatedCircuit

		Bandwidth                        : 200
		CircuitName                      : EquinixSVTest
		Location                         : Silicon Valley
		ServiceKey                       : *********************************
		ServiceProviderName              : equinix
		ServiceProviderProvisioningState : NotProvisioned
		Status                           : Enabled


4. **サービス キーを Exchange プロバイダーに送信します。**

	Exchange プロバイダーは、サービス キーを使用して Exchange プロバイダー側の接続を有効にします。接続を有効にするには、接続プロバイダーに追加の情報を提供する必要があります。

5. **回線キーのステータスと状態を定期的に確認します。**

	これにより、プロバイダーがいつ回線を有効にしたのかを知ることができます。回線が有効になると、以下の例に示すように、*ServiceProviderProvisioningState* が *Provisioned* と表示されます。

		PS C:\> Get-AzureDedicatedCircuit

		Bandwidth                        : 200
		CircuitName                      : EquinixSVTest
		Location                         : Silicon Valley
		ServiceKey                       : *********************************
		ServiceProviderName              : equinix
		ServiceProviderProvisioningState : Provisioned
		Status                           : Enabled

6. **仮想ネットワークのルーティングを構成します。**

	BGP セッションを使用するのはルートを交換するためであり、また高可用性を保証するためでもあります。以下の例を使用して、回線の BGP セッションを作成します。セッションを作成する際は、実際の値に置き換えてください。

		#Setting up a bgp session
		$ServiceKey = "<your key>"
		$PriSN = "<subnet/30 you use IP #1 and Azure uses IP #2>"
		$SecSN = "<subnet/30 use IP #1 and Azure uses IP #2>"
		$ASN = <your ASN>
		$VLAN = <your vlan ID>

		#Create a new bgp peering session
		New-AzureBGPPeering -ServiceKey $ServiceKey -PrimaryPeerSubnet $PriSN -SecondaryPeerSubnet $SecSN -PeerAsn $ASN VlanId $VLAN –AccessType Private
		#Get BGP parameters and Azure ASN
		Get-AzureBGPPeering -ServiceKey $ServiceKey –AccessType Private
		#Update BGP peering config
		Set-AzureBGPPeering  -ServiceKey $ServiceKey -PrimaryPeerSubnet $PriSN -SecondaryPeerSubnet $SecSN -PeerAsn $ASN -VlanId $VLAN –AccessType Private
		#Removing BGP peering config
		Remove-AzureBGPPeering -ServiceKey $ServiceKey –AccessType Private

	回線のルーティング情報を取得するには、Get-AzureBGPPeering を使用してサービス キーを指定します。Set-AzureBGPPeering を使用して BGP 設定を更新することもできます。このコマンドを実行しても、BGP セッションは確立されません。BGP セッションを確立するには、回線が 1 つ以上の VNet にリンクしている必要があります。

	以下に示す応答で返された情報が、次のステップで必要になります。ピア ASN は、ルーターの VRF に対する BGP の構成に使用します。

		PS C:\> New-AzureBGPPeering -ServiceKey $ServiceKey -PrimaryPeerSubnet $PriSN -SecondaryPeerSubnet $SecSN -PeerAsn $ASN -VlanId $VLAN –AccessType Private

		AzureAsn            : 12076
		PeerAsn             : 65001
		PrimaryAzurePort    : EQIX-SJC-06GMR-CIS-1-PRI-A
		PrimaryPeerSubnet   : 10.0.1.0/30
		SecondaryAzurePort  : EQIX-SJC-06GMR-CIS-2-SEC-A
		SecondaryPeerSubnet : 10.0.2.0/30
		State               : Enabled
		VlanId              : 100

7. **パブリック IP アドレスでホストされるサービスのルーティングを構成します。**

	BGP セッションを使用するのはルートを交換するためであり、また高可用性を保証するためでもあります。以下の例を使用して、回線の BGP セッションを作成します。セッションを作成する際は、実際の値に置き換えてください。

		#Setting up a bgp session
		$ServiceKey = "<your key>"
		$PriSN = "<subnet/30 subnet you use IP #1 and Azure uses IP #2>"
		$SecSN = "< subnet/30 subnet you use IP #1 and Azure uses IP #2>"
		$ASN = <your ASN>
		$VLAN = <your vlan ID>

		#Create a new bgp peering session
		New-AzureBGPPeering -ServiceKey $ServiceKey -PrimaryPeerSubnet $PriSN -SecondaryPeerSubnet $SecSN -PeerAsn $ASN -VlanId $VLAN –AccessType Public
		#Get BGP parameters and Azure ASN
		Get-AzureBGPPeering -ServiceKey $ServiceKey –AccessType Public
		#Update BGP peering config
		Set-AzureBGPPeering  -ServiceKey $ServiceKey -PrimaryPeerSubnet $PriSN -SecondaryPeerSubnet $SecSN -PeerAsn $ASN -VlanId $VLAN –AccessType Public
		#Removing BGP peering config
		Remove-AzureBGPPeering -ServiceKey $ServiceKey –AccessType Public

	回線のルーティング情報を取得するには、Get-AzureBGPPeering を使用してサービス キーを指定します。Set-AzureBGPPeering を使用して BGP 設定を更新することもできます。このコマンドを実行しても、BGP セッションは確立されません。BGP セッションを確立するには、回線が 1 つ以上の VNet にリンクしている必要があります。

	以下に示す応答で返された情報が、次のステップで必要になります。ピア ASN は、ルーターの VRF に対する BGP の構成に使用します。

		PS C:\> New-AzureBGPPeering -ServiceKey $ServiceKey -PrimaryPeerSubnet $PriSN -SecondaryPeerSubnet $SecSN -PeerAsn $ASN -VlanId $VLAN –AccessType Private

		AzureAsn            : 12076
		PeerAsn             : 65001
		PrimaryAzurePort    : EQIX-SJC-06GMR-CIS-1-PRI-A
		PrimaryPeerSubnet   : 10.0.1.8/30
		SecondaryAzurePort  : EQIX-SJC-06GMR-CIS-2-SEC-A
		SecondaryPeerSubnet : 10.0.2.8/30
		State               : Enabled
		VlanId              : 101

8. **Virtual Network とゲートウェイを構成します。**

	「[ExpressRoute 用の仮想ネットワークとゲートウェイの構成](https://msdn.microsoft.com/library/azure/dn643737.aspx)」を参照してください。ExpressRoute 接続を使用するために、ゲートウェイ サブネットは /28 にする必要があります。

9. **ネットワークを回線にリンクします。** 次に示す手順に進む前に必ず、回線が以下の状態とステータスに移行したことを確認してください。
	- ServiceProviderProvisioningState: Provisioned
	- Status: Enabled

			PS C:\> $Vnet = "MyTestVNet"
			New-AzureDedicatedCircuitLink -ServiceKey $ServiceKey -VNetName $Vnet
 

<!---HONumber=July15_HO4-->