<properties 
	pageTitle="ハイブリッド クラウドのテスト環境 | Microsoft Azure" 
	description="シンプル化されたオンプレミス ネットワークを含む、IT プロまたは開発のテスト用のハイブリッド クラウド環境を作成する方法について説明します。" 
	services="virtual-machines-windows" 
	documentationCenter="" 
	authors="JoeDavies-MSFT" 
	manager="timlt" 
	editor=""
	tags="azure-resource-manager"/>

<tags 
	ms.service="virtual-machines-windows" 
	ms.workload="infrastructure-services" 
	ms.tgt_pltfrm="Windows" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="04/01/2016" 
	ms.author="josephd"/>

# テスト用のハイブリッド クラウド環境の設定

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)]クラシック デプロイ モデル。
 
このトピックでは、Microsoft Azure でテスト用のハイブリッド クラウド環境を作成する手順について説明します。完成すると次のような構成になります。

![](./media/virtual-machines-windows-ps-hybrid-cloud-test-env-base/virtual-machines-windows-ps-hybrid-cloud-test-env-base-ph5.png)

この構成では、インターネット上の現在の場所から実際のハイブリッド運用環境をシミュレートします。構成は次のとおりです。

-  簡略化されたオンプレミス ネットワーク (Corpnet サブネット)。
-  Azure でホストされているクロスプレミス仮想ネットワーク (TestVNET)。
-  サイト間 VPN 接続
-  TestVNET 仮想ネットワークのセカンダリ ドメイン コントローラー。

この構成を基盤や共通の出発点にして以下を実行できます。

-  ハイブリッド クラウド環境でアプリケーションを開発とテストする。
-  コンピューターのテスト構成を作成する。この構成には、ハイブリッド クラウド ベースの IT ワークロードに対応するために、Corpnet サブネット上のコンピューターと TestVNET 仮想ネットワーク内のコンピューターが含まれます。

このハイブリッド クラウド テスト環境を設定する手順は、大きく次の 5 つのフェーズに分かれています。

1.	Corpnet サブネット上のコンピューターを構成する。
2.	RRAS1 を構成する。
3.	クロスプレミスの Azure 仮想ネットワークを作成する。
4.	サイト間 VPN 接続を作成する。
5.	DC2 を構成する。 

Azure サブスクリプションを持っていない場合は、[Azure の無料試用版のページ](https://azure.microsoft.com/pricing/free-trial/)で無料アカウントにサインアップすることもできます。MSDN または Visual Studio サブスクリプションをお持ちの場合は、「[Visual Studio サブスクライバー向けの月単位の Azure クレジット](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/)」をご覧ください。

>[AZURE.NOTE] Azure の仮想マシンと仮想ネットワーク ゲートウェイは、稼働していると継続的に費用が発生します。Azure VPN ゲートウェイは、2 台 1 組みの Azure の仮想マシンとして実装されます。詳細については、[Virtual Network 料金](https://azure.microsoft.com/pricing/details/virtual-network/)に関するページを参照してください。VPN Gateway の稼働コストを最小限に抑えるためには、テスト環境を作成し、できる限り迅速に必要なテストとデモンストレーションを行います。

この構成では、パブリック IP アドレスを使用してインターネットに直接接続された最大 4 台のコンピューターのテスト サブネットが必要です。これらのリソースがない場合は、[テスト用のシミュレートされたハイブリッド クラウド環境](virtual-machines-windows-ps-hybrid-cloud-test-env-sim.md)を設定することもできます。シミュレートされたハイブリッド クラウド テスト環境に必要なのは Azure サブスクリプションのみです。

## フェーズ 1: Corpnet サブネット上のコンピューターを構成する

[Windows Server 2012 R2 基本構成 のテスト ラボ ガイド](http://www.microsoft.com/download/details.aspx?id=39638)の Corpnet サブネットの構成手順に関するセクションの指示に従って、Corpnet という名前のサブネット上で DC1、APP1、CLIENT1 というコンピューターを構成します。**このサブネットは、RRAS1 コンピューターを通じてインターネットに直接接続されるため、社内ネットワークから分離する必要があります。**

次に、CORP\\User1 の資格情報を使用して、DC1 にログオンします。コンピューターとユーザーが認証にローカル ドメイン コントローラーを使用するよう CORP ドメインを構成するために、管理者レベルの Windows PowerShell コマンド プロンプトから次のコマンドを実行します。

	New-ADReplicationSite -Name "TestLab" 
	New-ADReplicationSite -Name "TestVNET"
	New-ADReplicationSubnet “Name "10.0.0.0/8" “Site "TestLab"
	New-ADReplicationSubnet “Name "192.168.0.0/16" “Site "TestVNET

現在の構成は次のようになります。

![](./media/virtual-machines-windows-ps-hybrid-cloud-test-env-base/virtual-machines-windows-ps-hybrid-cloud-test-env-base-ph1.png)
 
## フェーズ 2: RRAS1 を構成する

RRAS1 は、Corpnet サブネットと TestVNET 仮想ネットワークの間にトラフィック ルーティングと VPN デバイスのサービスを提供します。RRAS1 には、2 つのネットワーク アダプターが取り付けられている必要があります。

まず、RRAS1 にオペレーティング システムをインストールします。

1.	Windows Server 2012 R2 のインストールを開始します。
2.	指示に従ってインストールを完了し、ローカル管理者アカウントの強力なパスワードを設定します。ローカル管理者アカウントを使用してログオンします。
3.	インターネットにアクセスできるネットワークに RRAS1 を接続し、Windows Update を実行して Windows Server 2012 R2 の最新の更新プログラムをインストールします。
4.	ネットワーク アダプターの 1 つを Corpnet サブネットに接続し、もう 1 つをインターネットに直接接続します。RRAS1 は、インターネット ファイアウォールの内側には配置できますが、ネットワーク アドレス変換器 (NAT) の内側には配置しないでください。

次に、RRAS1 の TCP/IP プロパティを構成します。インターネット サービス プロバイダー (ISP) のアドレス、サブネット マスク (またはプレフィックス長)、既定のゲートウェイと DNS サーバーを含むパブリック IP アドレス構成が必要です。フェーズ 3 ではパブリック IP アドレスが必要になります。

RRAS1 で管理者レベルの Windows PowerShell コマンド プロンプトから次のコマンドを実行します。これらのコマンドを実行する前に、変数の値を入力し、< and > の文字を削除します。**Get-NetAdapter** コマンドの表示からネットワーク アダプターの現在の名前を確認できます。

	$corpnetAdapterName="<Name of the adapter attached to the Corpnet subnet>"
	$internetAdapterName="<Name of the adapter attached to the Internet>"
	[Ipaddress]$publicIP="<Your public IP address>"
	$publicIPpreflength=<Prefix length of your public IP address>
	[IPAddress]$publicDG="<Your ISP default gateway>"
	[IPAddress]$publicDNS="<Your ISP DNS server(s)>"
	Rename-NetAdapter -Name $corpnetAdapterName -NewName Corpnet
	Rename-NetAdapter -Name $internetAdapterName -NewName Internet
	New-NetIPAddress -InterfaceAlias "Internet" -IPAddress $publicIP -PrefixLength $publicIPpreflength “DefaultGateway $publicDG
	Set-DnsClientServerAddress -InterfaceAlias Internet -ServerAddresses $publicDNS
	New-NetIPAddress -InterfaceAlias "Corpnet" -IPAddress 10.0.0.2 -AddressFamily IPv4 -PrefixLength 24
	Set-DnsClientServerAddress -InterfaceAlias "Corpnet" -ServerAddresses 10.0.0.1
	Set-DnsClient -InterfaceAlias "Corpnet" -ConnectionSpecificSuffix corp.contoso.com
	New-NetFirewallRule -DisplayName "Allow ICMPv4-Input" -Protocol ICMPv4
	New-NetFirewallRule -DisplayName "Allow ICMPv4-Output" -Protocol ICMPv4 -Direction Outbound
	Disable-NetAdapterBinding -Name "Internet" -ComponentID ms_msclient
	Disable-NetAdapterBinding -Name "Internet" -ComponentID ms_server
	ping dc1.corp.contoso.com

最後のコマンドで、IP アドレス 10.0.0.1 から 4 つの応答が返ってきていることを確認します。

現在の構成は次のようになります。

![](./media/virtual-machines-windows-ps-hybrid-cloud-test-env-base/virtual-machines-windows-ps-hybrid-cloud-test-env-base-ph2.png)

## フェーズ 3: クロスプレミスの Azure Virtual Network を作成する

Azure PowerShell プロンプトを開始します。

> [AZURE.NOTE] 次のコマンド セットは、Azure PowerShell 1.0 以降を使用します。詳細については、「[Azure PowerShell 1.0](https://azure.microsoft.com/blog/azps-1-0/)」を参照してください。

アカウントにログインします。

	Login-AzureRMAccount

次のコマンドを使用して、サブスクリプション名を取得します。

	Get-AzureRMSubscription | Sort SubscriptionName | Select SubscriptionName

Azure サブスクリプションを設定します。基本構成をビルドするために使用したものと同じサブスクリプションを使用します。引用符内のすべての文字 (< and > を含む) を、正しい名前に置き換えます。

	$subscr="<subscription name>"
	Get-AzureRmSubscription –SubscriptionName $subscr | Select-AzureRmSubscription

次に、ハイブリッド テスト環境用の新しいリソース グループを作成します。

一意のリソース グループ名を確認するには、次のコマンドを実行して、既存のリソース グループの一覧を取得します。

	Get-AzureRMResourceGroup | Sort ResourceGroupName | Select ResourceGroupName

これらのコマンドを使用して、新しいリソース グループを作成します。

	$rgName="<resource group name>"
	$locName="<an Azure location, such as West US>"
	New-AzureRMResourceGroup -Name $rgName -Location $locName

リソース マネージャー ベースの仮想マシンには、リソース マネージャー ベースのストレージ アカウントが必要です。それぞれのストレージ アカウントには、小文字のアルファベットと数字のみが含まれる、グローバルに一意の名前を選択する必要があります。既存のストレージ アカウントの一覧を取得するには、次のコマンドを実行します。

	Get-AzureRMStorageAccount | Sort Name | Select Name

これらのコマンドを使用して、新しいストレージ アカウントを作成します。

	$rgName="<your new resource group name>"
	$locName="<the location of your new resource group>"
	$saName="<unique storage account name >"
	New-AzureRMStorageAccount -Name $saName -ResourceGroupName $rgName –Type Standard_LRS -Location $locName

次に、ハイブリッド クラウド環境をホストし、ネットワーク セキュリティ グループで保護する Azure Virtual Network を作成します。

	$locShortName="<the location of your new resource group in lowercase with spaces removed, example: westus>"
	$gwSubnet=New-AzureRMVirtualNetworkSubnetConfig -Name "GatewaySubnet" -AddressPrefix "192.168.255.248/29"
	$vmSubnet=New-AzureRMVirtualNetworkSubnetConfig -Name "TestSubnet" -AddressPrefix "192.168.0.0/24"
	New-AzureRMVirtualNetwork -Name "TestVNET" -ResourceGroupName $rgName -Location $locName -AddressPrefix "192.168.0.0/16" -Subnet $gwSubnet,$vmSubnet -DNSServer "10.0.0.1"
	$rule1=New-AzureRMNetworkSecurityRuleConfig -Name "RDPTraffic" -Description "Allow RDP to all VMs on the subnet" -Access Allow -Protocol Tcp -Direction Inbound -Priority 100 -SourceAddressPrefix Internet -SourcePortRange * -DestinationAddressPrefix * -DestinationPortRange 3389
	New-AzureRMNetworkSecurityGroup -Name "TestSubnet" -ResourceGroupName $rgName -Location $locShortName -SecurityRules $rule1
	$vnet=Get-AzureRMVirtualNetwork -ResourceGroupName $rgName -Name "TestVNET"
	$nsg=Get-AzureRMNetworkSecurityGroup -Name "TestSubnet" -ResourceGroupName $rgName
	Set-AzureRMVirtualNetworkSubnetConfig -VirtualNetwork $vnet -Name TestSubnet -AddressPrefix "192.168.0.0/24" -NetworkSecurityGroup $nsg

次に、以下のコマンドを使用して、サイト間 VPN 接続用のゲートウェイを作成します。フェーズ 2 で取得した、RRAS1 のインターネット インターフェイスのパブリック IP アドレスが必要になります。

	$localGatewayIP="<the public IP address assigned to the Internet interface of RRAS1>"
	$vnet=Get-AzureRMVirtualNetwork -Name TestVNET -ResourceGroupName $rgName
	
	# Attach a virtual network gateway to a public IP address and the gateway subnet
	$publicGatewayVipName="HybCloudPublicIPAddress"
	$vnetGatewayIpConfigName="HybCloudPublicIPConfig"
	New-AzureRMPublicIpAddress -Name $vnetGatewayIpConfigName -ResourceGroupName $rgName -Location $locName -AllocationMethod Dynamic
	$publicGatewayVip=Get-AzureRMPublicIpAddress -Name $vnetGatewayIpConfigName -ResourceGroupName $rgName
	$vnetGatewayIpConfig=New-AzureRMVirtualNetworkGatewayIpConfig -Name $vnetGatewayIpConfigName -PublicIpAddressId $publicGatewayVip.Id -SubnetId $vnet.Subnets[0].Id
	
	# Create the Azure gateway
	$vnetGatewayName="HybCloudAzureGateway"
	$vnetGateway=New-AzureRMVirtualNetworkGateway -Name $vnetGatewayName -ResourceGroupName $rgName -Location $locName -GatewayType Vpn -VpnType RouteBased -IpConfigurations $vnetGatewayIpConfig
	
	# Create the gateway for the local network
	$localGatewayName="HybCloudLocalNetGateway"
	$localNetworkPrefix="10.0.0.0/8"
	$localGateway=New-AzureRMLocalNetworkGateway -Name $localGatewayName -ResourceGroupName $rgName -Location $locName -GatewayIpAddress $localGatewayIP -AddressPrefix $localNetworkPrefix

新しいゲートウェイは、完成するまで 20 分程度かかる場合があることに注意してください。

次に、次のコマンドを使用して、仮想ネットワーク用の Azure VPN Gateway のパブリック IP アドレスを決定します。

	Get-AzureRMPublicIpAddress -Name $vnetGatewayIpConfigName -ResourceGroupName $rgName

表示されている **IPAddress** フィールドの IP アドレスを書き留めておきます。この情報はフェーズ 4 で必要になります。

次に、ランダムな、暗号強度の高い 32 文字の事前共有キーをネットワーク管理者またはセキュリティ管理者から取得します。または、[「Create a random string for an IPsec preshared key」](http://social.technet.microsoft.com/wiki/contents/articles/32330.create-a-random-string-for-an-ipsec-preshared-key.aspx) (IPsec 事前共有キー用にランダムな文字列を作成する) に記載された情報を使用して事前共有キーを取得します。

以下のコマンドを使用して、Azure でサイト間 VPN 接続を作成します。

	# Define the Azure virtual network VPN connection
	$vnetConnectionKey="<32-character pre-shared key>"
	$vnetConnectionName="HybCloudS2SConnection"
	$vnetConnection=New-AzureRMVirtualNetworkGatewayConnection -Name $vnetConnectionName -ResourceGroupName $rgName -Location $locName -ConnectionType IPsec -SharedKey $vnetConnectionKey -VirtualNetworkGateway1 $vnetGateway -LocalNetworkGateway2 $localGateway

現在の構成は次のようになります。

![](./media/virtual-machines-windows-ps-hybrid-cloud-test-env-base/virtual-machines-windows-ps-hybrid-cloud-test-env-base-ph3.png)
 
## フェーズ 4: サイト間 VPN 接続を作成する

まず、ルーティングとリモート アクセス サービスを使用して、Corpnet サブネットの VPN デバイスとして動作するように RRAS1 を構成します。ローカル管理者として RRAS1 にログオンし、Windows PowerShell コマンド プロンプトで次のコマンドを実行します。

	Import-Module ServerManager
	Install-WindowsFeature RemoteAccess -IncludeManagementTools
	Add-WindowsFeature -name Routing -IncludeManagementTools

次に、Azure VPN Gateway からのサイト間 VPN 接続を受信するように RRAS1 を構成します。RRAS1 を再起動し、ローカル管理者としてログオンして、Windows PowerShell コマンド プロンプトで次のコマンドを実行します。フェーズ 3 で取得した Azure VPN Gateway のパブリック IP アドレスと事前共有キー値を入力する必要があります。

	$PresharedKey="<32-character pre-shared key>"
	Import-Module RemoteAccess
	Install-RemoteAccess -VpnType VpnS2S
	Add-VpnS2SInterface -Protocol IKEv2 -AuthenticationMethod PSKOnly -Persistent -NumberOfTries 3 -ResponderAuthenticationMethod PSKOnly -Name S2StoTestVNET -Destination "<public IP address of the Azure VPN gateway>" -IPv4Subnet @("192.168.0.0/24:100") -SharedSecret $PresharedKey
	Set-VpnServerIPsecConfiguration -EncryptionType MaximumEncryption
	Set-VpnServerIPsecConfiguration -SADataSizeForRenegotiationKilobytes 33553408
	New-ItemProperty -Path HKLM:\System\CurrentControlSet\Services\RemoteAccess\Parameters\IKEV2 -Name SkipConfigPayload -PropertyType DWord -Value 1
	Restart-Service RemoteAccess

RRAS1 と Azure VPN Gateway 間の接続が確立されるまで数分かかります。

続いて、インターネット上の場所への変換されたトラフィックをサポートするように RRAS1 を構成します。RRAS1 で:

1.	スタート画面で「**rras**」と入力し、**[ルーティングとリモート アクセス]** をクリックします。 
2.	コンソール ツリーで、サーバー名を開き、**[IPv4]** をクリックします。
3.	**[全般]** を右クリックして、**[新しいルーティング プロトコル]** をクリックします。
4.	**[NAT]** をクリックし、**[OK]** をクリックします。
5.	コンソール ツリーで **[NAT]** を右クリックし、**[新しいインターフェイス]**、**[Corpnet]** の順にクリックして、**[OK]** を 2 回クリックします。
6.	**[NAT]** を右クリックし、**[新しいインターフェイス]**、**[インターネット]** の順にクリックして、**[OK]** をクリックします。
7.	**[NAT]** タブで、**[インターネットに接続されたパブリック インターフェイス]** をクリックし、**[このインターフェイスで NAT を有効にする]** を選択して、**[OK]** をクリックします。

次に、既定のゲートウェイとして RRAS1 を使用するように DC1、APP1、CLIENT1 を構成します。
 
DC1 で管理者レベルの Windows PowerShell コマンド プロンプトから次のコマンドを実行します。

	New-NetRoute -DestinationPrefix "0.0.0.0/0" -InterfaceAlias "Ethernet" -NextHop 10.0.0.2
	Set-DhcpServerv4OptionValue -Router 10.0.0.2

インターフェイスの名前が "イーサネット" でない場合は、**Get-NetAdapter** コマンドを使用して、インターフェイスの名前を特定してください。

APP1 で管理者レベルの Windows PowerShell コマンド プロンプトから次のコマンドを実行します。

	New-NetRoute -DestinationPrefix "0.0.0.0/0" -InterfaceAlias "Ethernet" -NextHop 10.0.0.2

CLIENT1 で管理者レベルの Windows PowerShell コマンド プロンプトから次のコマンドを実行します。

	ipconfig /renew

現在の構成は次のようになります。
 

![](./media/virtual-machines-windows-ps-hybrid-cloud-test-env-base/virtual-machines-windows-ps-hybrid-cloud-test-env-base-ph4.png)


## フェーズ 5: DC2 を構成する

まず、ローカル コンピューターで Azure PowerShell コマンド プロンプトから次のコマンドを実行して、DC2 用に Azure 仮想マシンを作成します。

	$rgName="<your resource group name>"
	$locName="<your Azure location, such as West US>"
	$saName="<your storage account name>"
	
	$vnet=Get-AzureRMVirtualNetwork -Name "TestVNET" -ResourceGroupName $rgName
	$subnet=Get-AzureRmVirtualNetworkSubnetConfig -VirtualNetwork $vnet -Name "TestSubnet"
	$pip=New-AzureRMPublicIpAddress -Name DC2-NIC -ResourceGroupName $rgName -Location $locName -AllocationMethod Dynamic
	$nic=New-AzureRMNetworkInterface -Name DC2-NIC -ResourceGroupName $rgName -Location $locName -Subnet $subnet -PublicIpAddress $pip -PrivateIpAddress 192.168.0.4
	$vm=New-AzureRMVMConfig -VMName DC2 -VMSize Standard_A1
	$storageAcc=Get-AzureRMStorageAccount -ResourceGroupName $rgName -Name $saName
	$vhdURI=$storageAcc.PrimaryEndpoints.Blob.ToString() + "vhds/DC2-TestVNET-ADDSDisk.vhd"
	Add-AzureRMVMDataDisk -VM $vm -Name ADDS-Data -DiskSizeInGB 20 -VhdUri $vhdURI  -CreateOption empty
	$cred=Get-Credential -Message "Type the name and password of the local administrator account for DC2."
	$vm=Set-AzureRMVMOperatingSystem -VM $vm -Windows -ComputerName DC2 -Credential $cred -ProvisionVMAgent -EnableAutoUpdate
	$vm=Set-AzureRMVMSourceImage -VM $vm -PublisherName MicrosoftWindowsServer -Offer WindowsServer -Skus 2012-R2-Datacenter -Version "latest"
	$vm=Add-AzureRMVMNetworkInterface -VM $vm -Id $nic.Id
	$osDiskUri=$storageAcc.PrimaryEndpoints.Blob.ToString() + "vhds/DC2-TestLab-OSDisk.vhd"
	$vm=Set-AzureRMVMOSDisk -VM $vm -Name DC2-TestVNET-OSDisk -VhdUri $osDiskUri -CreateOption fromImage
	New-AzureRMVM -ResourceGroupName $rgName -Location $locName -VM $vm

次に、Azure ポータルでローカル管理者アカウントの資格情報を使用して、新しい DC2 仮想マシンに接続します。

続いて、基本的な接続テストを行うためのトラフィックを許可するよう Windows ファイアウォール規則を構成します。DC2 で管理者レベルの Windows PowerShell コマンド プロンプトから次のコマンドを実行します。

	Set-NetFirewallRule -DisplayName "File and Printer Sharing (Echo Request - ICMPv4-In)" -enabled True
	ping dc1.corp.contoso.com

ping コマンドで IP アドレス 10.0.0.1 からの応答が 4 回成功する必要があります。*シミュレートされたハイブリッド クラウド構成*を使用している場合、IP アドレス 10.0.0.4 からの応答が 4 回成功する必要があります。これはサイト間 VPN 接続または VNet 間接続を介したトラフィックのテストです。

次に、余っているデータ ディスクを新しいボリュームとして追加し、ドライブ文字 F: を割り当てます。

1.	サーバー マネージャーの左側のウィンドウで、**[ファイル サービスと記憶域サービス]** をクリックし、**[ディスク]** をクリックします。
2.	コンテンツ ウィンドウの **[ディスク]** グループで、(**[パーティション]** が **[不明]** に設定されている) **[ディスク 2]** をクリックします。
3.	**[タスク]** をクリックし、**[ボリューム]** をクリックします。
4.	新しいボリューム ウィザードの [開始する前に] ページで **[次へ]** をクリックします。
5.	[サーバーとディスクの選択] ページで、**[ディスク 2]** をクリックし、**[次へ]** をクリックします。メッセージが表示されたら、**[OK]** をクリックします。
6.	[ボリュームのサイズの指定] ページで、**[次へ]** をクリックします。
7.	[ドライブ文字またはフォルダーへの割り当て] ページで、**[次へ]** をクリックします。
8.	[ファイル システム形式の選択] ページで、**[次へ]** をクリックします。
9.	[選択内容の確認] ページで、**[作成]** をクリックします。
10.	完了したら、**[閉じる]** をクリックします。

次に、DC2 を corp.contoso.com ドメインのレプリカ ドメイン コントローラーとして構成します。DC2 で Windows PowerShell コマンド プロンプトから次のコマンドを実行します。

	Install-WindowsFeature AD-Domain-Services -IncludeManagementTools
	Install-ADDSDomainController -Credential (Get-Credential CORP\User1) -DomainName "corp.contoso.com" -InstallDns:$true -DatabasePath "F:\NTDS" -LogPath "F:\Logs" -SysvolPath "F:\SYSVOL"

CORP\\User1 のパスワードとディレクトリ サービス復元モード (DSRM) のパスワードの両方を入力し、DC2 を再起動するよう求められます。

これで TestVNET 仮想ネットワーク独自の DNS サーバー (DC2) が設定されたので、この DNS サーバーを使用するように TestVNET 仮想ネットワークを構成する必要があります。

1.	Azure ポータルの左側のウィンドウで、仮想ネットワーク アイコンをクリックし、**[TestVNET]** をクリックします。
2.	**[設定]** タブで、**[DNS サーバー]** をクリックします。
3.	**[プライマリ DNS サーバー]** で、「**192.168.0.4**」と入力して 10.0.0.4 を置き換えます。
4.	[保存] をクリックします。

次に、Azure PowerShell コマンド プロンプトで以下のコマンドを使用して、新しい DNS サーバーの構成を使用するように DC2 を再起動します。

	Stop-AzureRmVM -ResourceGroupName $rgName -Name DC2
	Start-AzureRmVM -ResourceGroupName $rgName -Name DC2

現在の構成は次のようになります。

![](./media/virtual-machines-windows-ps-hybrid-cloud-test-env-base/virtual-machines-windows-ps-hybrid-cloud-test-env-base-ph5.png)

これで、ハイブリッド クラウド環境をテストする準備が整いました。
 
## 次のステップ

- この環境に [SharePoint イントラネット ファーム](virtual-machines-windows-ps-hybrid-cloud-test-env-sp.md)、[Web ベース LOB アプリケーション](virtual-machines-windows-ps-hybrid-cloud-test-env-lob.md)、または [Office 365 ディレクトリ同期 (DirSync) サーバー](virtual-machines-windows-ps-hybrid-cloud-test-env-dirsync.md)をセットアップします。

<!---HONumber=AcomDC_0413_2016-->