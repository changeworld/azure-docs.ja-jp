<properties 
	pageTitle="基幹業務アプリケーションのワークロード フェーズ 1: Azure を構成する" 
	description="高可用な基幹業務アプリケーションを Azure インフラストラクチャ サービスにデプロイする作業のこの第 1 フェーズでは、Azure Virtual Network およびその他の Azure インフラストラクチャ要素を作成します。" 
	documentationCenter=""
	services="virtual-machines" 
	authors="JoeDavies-MSFT" 
	manager="timlt" 
	editor=""/>

<tags 
	ms.service="virtual-machines" 
	ms.workload="infrastructure-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="08/11/2015" 
	ms.author="josephd"/>

# 基幹業務アプリケーションのワークロード フェーズ 1: Azure を構成する

イントラネット専用の高可用な基幹業務アプリケーションを Azure インフラストラクチャ サービスにデプロイする作業のこのフェーズでは、Azure のネットワークおよびストレージ インフラストラクチャを構築します。[フェーズ 2](virtual-machines-workload-high-availability-LOB-application-phase2.md) に進むには、このフェーズを完了する必要があります。全フェーズについては、「[Azure での高可用な基幹業務アプリケーションのデプロイ](virtual-machines-workload-high-availability-LOB-application-overview.md)」をご覧ください。

Azure を次の基本的なネットワーク コンポーネントでプロビジョニングする必要があります。

- サブネットを 1 つ含むクロスプレミス仮想ネットワーク (Azure 仮想マシンのホスト用)
- 2 つの Azure ストレージ アカウント (VHD ディスク イメージと追加のデータ ディスクの格納用)
- 3 つの可用性セット

## 開始する前に

Azure コンポーネントの構成を開始する前に、次の表を作成します。Azure を構成するときに役立つよう、このセクションを印刷して必要な情報を書き込むか、またはこのセクションをドキュメントにコピーして情報を入力してください。

仮想ネットワーク (VNet) の設定を表 V に記入します。

項目 | 構成要素 | 説明 | 値 
--- | --- | --- | --- 
1\. | VNet の名前 | Azure Virtual Network に割り当てる名前 (例: SPFarmNet) 。 | \_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_
2\. | VNet の場所 | 仮想ネットワークを含む Azure データセンター。 | \_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_
3\. | ローカル ネットワークの名前 | 組織のネットワークに割り当てる名前。 | \_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_
4\. | VPN デバイスの IP アドレス | インターネット上の VPN デバイスのインターフェイスのパブリック IPv4 アドレス。IT 部門と相談してこのアドレスを決定します。 | \_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_
5\. | VNet のアドレス空間 | 単一のプライベート アドレス プレフィックスで定義されている、仮想ネットワークのアドレス空間。IT 部門と相談してこのアドレス空間を決定します。 | \_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_
6\. | 仮想ネットワークの最初の DNS サーバー | 仮想ネットワークの、2 番目のサブネットのアドレス空間に対して可能な 4 番目の IP アドレス (表 S を参照)。IT 部門と相談してこのアドレスを決定します。 | \_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_
7\. | 仮想ネットワークの 2 番目の DNS サーバー | 仮想ネットワークの、2 番目のサブネットのアドレス空間に対して可能な 5 番目の IP アドレス (表 S を参照)。IT 部門と相談してこのアドレスを決定します。 | \_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_
8\. | IPsec 共有キー | サイト間 VPN 接続の両側で認証に使用される、128 文字のランダムな英数文字列。IT 部門またはセキュリティ部門と相談して、このキーの値を決定します。 | \_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_


**表 V: クロスプレミス仮想ネットワークの構成**

このソリューションのサブネットに関する表 S に記入します。

- 最初のサブネットでは、Azure ゲートウェイ サブネットに対して 29 ビットのアドレス空間 (プレフィックスの長さが /29) を指定します。
- 2 番目のサブネットでは、表示名、仮想ネットワーク アドレス空間に基づく 1 つの IP アドレス空間、および用途の説明を指定します。 

IT 部門と相談して、仮想ネットワーク アドレス空間からこれらのアドレス空間を決定します。両方のアドレス空間は、ネットワーク プレフィックス形式とも呼ばれるクラスレス ドメイン間ルーティング (CIDR) 形式である必要があります。たとえば、10.24.64.0/20 のような形式です。

項目 | サブネット名 | サブネットのアドレス空間 | 目的 
--- | --- | --- | --- 
1\. | ゲートウェイ サブネット | \_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_ | Azure ゲートウェイの仮想マシンで使用されるサブネットです。
2\. | \_\_\_\_\_\_\_\_\_\_\_\_\_\_\_ | \_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_ | \_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_

**表 S: 仮想ネットワークのサブネット**

> [AZURE.NOTE]この定義済みアーキテクチャでは、わかりやすくするため単一のサブネットを使用します。一連のトラフィック フィルターをオーバーレイしてサブネットの分離をエミュレートする場合は、Azure の[ネットワーク セキュリティ グループ](https://msdn.microsoft.com/library/azure/dn848316.aspx)を使用できます。

仮想ネットワーク内にドメイン コント ローラーを最初にセットアップするときに使用する 2 つのオンプレミス DNS サーバーについて、表 D に記入します。各 DNS サーバーの表示名および単一の IP アドレスを指定します。この表示名は、DNS サーバーのホスト名またはコンピューター名と一致している必要はありません。記入欄は 2 つですが、さらに追加してもかまいません。IT 部門と相談してこのリストを決定します。

項目 | DNS サーバーの表示名 | DNS サーバーの IP アドレス 
--- | --- | ---
1\. | \_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_ | \_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_
2\. | \_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_ | \_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_ 

**表 D: オンプレミス DNS サーバー**

サイト間 VPN 接続を使用して Azure 仮想ネットワークから組織のネットワークにパケットをルーティングするには、組織のオンプレミス ネットワーク上にあるすべての到達可能な場所のアドレス空間 (CIDR 表記) のリストを含むローカル ネットワークで、仮想ネットワークを構成する必要があります。ローカル ネットワークを定義するアドレス空間のリストは、一意である必要があり、他の仮想ネットワークまたは他のローカル ネットワークに使用されるアドレス空間と重複して設定することはできません。

一連のローカル ネットワーク アドレス空間について、表 L に記入します。記入欄は 3 つですが、通常はさらに必要です。IT 部門と相談してこのアドレス空間のリストを決定します。

項目 | ローカル ネットワークのアドレス空間 
--- | ---
1\. | \_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_
2\. | \_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_
3\. | \_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_

**表 L: ローカル ネットワークのアドレス プレフィックス**

次に、Azure PowerShell Version 0.9.5 以降をインストールする必要があります。Azure PowerShell のバージョンを確認するには、次のコマンドを実行します。

	Get-Module azure | format-table version

最新バージョンの Azure PowerShell をインストールする必要がある場合は、**[コントロール パネル] の [プログラムと機能]** を使用して、現在のバージョンを削除します。次に、「[Azure PowerShell のインストールおよび構成方法](../install-configure-powershell.md)」の手順に従って、ローカル コンピューターに Azure PowerShell をインストールします。Azure PowerShell プロンプトを開きます。

まず以下のコマンドで、適切な Azure サブスクリプションを選択します。引用符内のすべての文字 (< and > を含む) を、正しい名前に置き換えます。

	$subscr="<Subscription name>"
	Select-AzureSubscription -SubscriptionName $subscr –Current

**Get-AzureSubscription** コマンドの出力の **SubscriptionName** プロパティで、サブスクリプション名を取得できます。

次に、以下のコマンドを実行して、Azure PowerShell をリソース マネージャー モードに切り替えます。

	Switch-AzureMode AzureResourceManager 

次に、基幹業務アプリケーション用の新しいリソース グループを作成します。

一意のリソース グループ名を確認するには、次のコマンドを実行して、既存のリソース グループの一覧を取得します。

	Get-AzureResourceGroup | Sort ResourceGroupName | Select ResourceGroupName

リソース マネージャー ベースの仮想マシンを作成できる Azure の場所一覧を取得するには、次のコマンドを実行します。

	$loc=Get-AzureLocation | where { $_.Name –eq "Microsoft.Compute/virtualMachines" }
	$loc.Locations

これらのコマンドを使用して、新しいリソース グループを作成します。

	$rgName="<resource group name>"
	$locName="<an Azure location, such as West US>"
	New-AzureResourceGroup -Name $rgName -Location $locName

リソース マネージャー ベースの仮想マシンには、リソース マネージャー ベースのストレージ アカウントが必要です。

項目 | ストレージ アカウント名 | 目的 
--- | --- | ---
1\. | \_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_ | SQL Server 仮想マシンで使用する Premium Storage アカウント。
2\. | \_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_ | ワークロード内のその他すべての仮想マシンで使用する Standard Storage アカウント。 

**表 ST: ストレージ アカウント**

これらの名前は、フェーズ 2、3、4 で仮想マシンを作成するときに必要になります。

それぞれのストレージ アカウントには、小文字のアルファベットと数字のみが含まれる、グローバルに一意の名前を選択する必要があります。既存のストレージ アカウントの一覧を取得するには、次のコマンドを実行します。

	Get-AzureStorageAccount | Sort Name | Select Name

選択したストレージ アカウント名がグローバルに一意かどうかをテストするには、PowerShell の Azure サービス管理モードで **Test-AzureName** コマンドを実行する必要があります。次のコマンドを実行します。

	Switch-AzureMode AzureServiceManagement
	Test-AzureName -Storage <Proposed storage account name>

Test-AzureName コマンドで **False** と表示される場合、指定した名前は一意です。両方のストレージ アカウントに一意の名前を指定したら、表 ST を更新し、次のコマンドを実行して Azure PowerShell をリソース マネージャー モードに戻します。

	Switch-AzureMode AzureResourceManager 

最初のストレージ アカウントを作成するには、次のコマンドを実行します。

	$rgName="<your new resource group name>"
	$locName="<the location of your new resource group>"
	$saName="<Table ST – Item 1 - Storage account name column>"
	New-AzureStorageAccount -Name $saName -ResourceGroupName $rgName –Type Premium_LRS -Location $locName

2 番目のストレージ アカウントを作成するには、次のコマンドを実行します。

	$rgName="<your new resource group name>"
	$locName="<the location of your new resource group>"
	$saName="<Table ST – Item 2 - Storage account name column>"
	New-AzureStorageAccount -Name $saName -ResourceGroupName $rgName –Type Standard_LRS -Location $locName

次に、基幹業務アプリケーションをホストする Azure 仮想ネットワークを作成します。

	$rgName="<name of your new resource group>"
	$locName="<Azure location of the new resource group>"
	$vnetName="<Table V – Item 1 – Value column>"
	$vnetAddrPrefix="<Table V – Item 5 – Value column>"
	$lobSubnetName="<Table S – Item 2 – Subnet name column>"
	$lobSubnetPrefix="<Table S – Item 2 – Subnet address space column>"
	$gwSubnetPrefix="<Table S – Item 1 – Subnet address space column>"
	$dnsServers=@( "<Table D – Item 1 – DNS server IP address column>", "<Table D – Item 2 – DNS server IP address column>" )
	$gwSubnet=New-AzureVirtualNetworkSubnetConfig -Name "GatewaySubnet" -AddressPrefix $gwSubnetPrefix
	$lobSubnet=New-AzureVirtualNetworkSubnetConfig -Name $lobSubnetName -AddressPrefix $lobSubnetPrefix
	New-AzurevirtualNetwork -Name $vnetName -ResourceGroupName $rgName -Location $locName -AddressPrefix $vnetAddrPrefix -Subnet $gwSubnet,$lobSubnet -DNSServer $dnsServers

次に、以下のコマンドを使用して、サイト間 VPN 接続用のゲートウェイを作成します。

	$vnetName="<Table V – Item 1 – Value column>"
	$vnet=Get-AzureVirtualNetwork -Name $vnetName -ResourceGroupName $rgName
	
	# Attach a virtual network gateway to a public IP address and the gateway subnet
	$publicGatewayVipName="LOBAppPublicIPAddress"
	$vnetGatewayIpConfigName="LOBAppPublicIPConfig"
	New-AzurePublicIpAddress -Name $vnetGatewayIpConfigName -ResourceGroupName $rgName -Location $locName -AllocationMethod Dynamic
	$publicGatewayVip=Get-AzurePublicIpAddress -Name $vnetGatewayIpConfigName -ResourceGroupName $rgName
	$vnetGatewayIpConfig=New-AzureVirtualNetworkGatewayIpConfig -Name $vnetGatewayIpConfigName -PublicIpAddressId $publicGatewayVip.Id -SubnetId $vnet.Subnets[0].Id

	# Create the Azure gateway
	$vnetGatewayName="LOBAppAzureGateway"
	$vnetGateway=New-AzureVirtualNetworkGateway -Name $vnetGatewayName -ResourceGroupName $rgName -Location $locName -GatewayType Vpn -VpnType RouteBased -IpConfigurations $vnetGatewayIpConfig
	
	# Create the gateway for the local network
	$localGatewayName="LOBAppLocalNetGateway"
	$localGatewayIP="<Table V – Item 4 – Value column>"
	$localNetworkPrefix=@( <comma-separated, double-quote enclosed list of the local network address prefixes from Table L, example: "10.1.0.0/24", "10.2.0.0/24"> )
	$localGateway=New-AzureLocalNetworkGateway -Name $localGatewayName -ResourceGroupName $rgName -Location $locName -GatewayIpAddress $localGatewayIP -AddressPrefix $localNetworkPrefix
	
	# Define the Azure virtual network VPN connection
	$vnetConnectionName="LOBAppS2SConnection"
	$vnetConnectionKey="<Table V – Item 8 – Value column>"
	$vnetConnection=New-AzureVirtualNetworkGatewayConnection -Name $vnetConnectionName -ResourceGroupName $rgName -Location $locName -ConnectionType IPsec -SharedKey $vnetConnectionKey -VirtualNetworkGateway1 $vnetGateway -LocalNetworkGateway2 $localGateway

次に、Azure VPN Gateway に接続するオンプレミスの VPN デバイスを構成します。詳細については、「[VPN デバイスの構成](../virtual-networks/vpn-gateway-configure-vpn-gateway-mp.md#configure-your-vpn-device)」を参照してください。

オンプレミスの VPN デバイスを構成するには、次のものが必要です。

- 仮想ネットワーク用の Azure VPN Gateway のパブリック IPv4 アドレス (**Get-AzurePublicIpAddress -Name $publicGatewayVipName -ResourceGroupName $rgName** コマンドで表示)
- サイト間 VPN 接続用の IPsec 事前共有キー (表 V - 項目 8 - "値" 列)

次に、仮想ネットワークのアドレス空間がオンプレミス ネットワークから到達できることを確認します。これは、通常、仮想ネットワークのアドレス空間に対応するルートを VPN デバイスに追加した後、組織ネットワークのルーティング インフラストラクチャの他の部分にそのルートをアドバタイズすることによって行います。IT 部門と相談してこの方法を決定します。

次に、3 つの可用性セットの名前を定義します。表 A に記入します。

項目 | 目的 | 可用性セットの名前 
--- | --- | --- 
1\. | ドメイン コントローラー | \_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_
2\. | SQL Server | \_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_
3\. | Web サーバー | \_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_

**表 A: 可用性セットの名前**

これらの名前は、フェーズ 2、3、4 で仮想マシンを作成するときに必要になります。

次の Azure PowerShell コマンドを実行して、これらの新しい可用性セットを作成します。

	$rgName="<your new resource group name>"
	$locName="<the Azure location for your new resource group>"
	$avName="<Table A – Item 1 – Availability set name column>"
	New-AzureAvailabilitySet –Name $avName –ResourceGroupName $rgName -Location $locName
	$avName="<Table A – Item 2 – Availability set name column>"
	New-AzureAvailabilitySet –Name $avName –ResourceGroupName $rgName -Location $locName
	$avName="<Table A – Item 3 – Availability set name column>"
	New-AzureAvailabilitySet –Name $avName –ResourceGroupName $rgName -Location $locName

次の図は、このフェーズが問題なく完了したときの構成を示しています。

![](./media/virtual-machines-workload-high-availability-LOB-application-phase1/workload-lobapp-phase1.png)

## 次のステップ

このワークロードを引き続き構成するには、「[フェーズ 2: ドメイン コントローラーの構成](virtual-machines-workload-high-availability-LOB-application-phase2.md)」に進んでください。

## その他のリソース

[Azure での高可用な基幹業務アプリケーションのデプロイ](virtual-machines-workload-high-availability-LOB-application-overview.md)

[基幹業務アプリケーションのアーキテクチャ ブループリント](http://msdn.microsoft.com/dn630664)

[テスト用のハイブリッド クラウドでの Web ベース LOB アプリケーションの設定](../virtual-network/virtual-networks-setup-lobapp-hybrid-cloud-testing.md)

[Azure インフラストラクチャ サービス実装ガイドライン](virtual-machines-infrastructure-services-implementation-guidelines.md)

[Azure インフラストラクチャ サービスのワークロード: SharePoint Server 2013 ファーム](virtual-machines-workload-intranet-sharepoint-farm.md)

<!---HONumber=August15_HO8-->