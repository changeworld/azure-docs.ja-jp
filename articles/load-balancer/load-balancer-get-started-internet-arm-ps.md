<properties 
   pageTitle="PowerShell を使用し、リソース マネージャーでインターネットに接続するロード バランサーを作成する | Microsoft Azure"
   description="PowerShell を使用し、リソース マネージャーでインターネットに接続するロード バランサーを作成する方法について説明します"
   services="load-balancer"
   documentationCenter="na"
   authors="joaoma"
   manager="carmonm"
   editor=""
   tags="azure-resource-manager"
/>
<tags  
   ms.service="load-balancer"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="10/21/2015"
   ms.author="joaoma" />

# PowerShell を使用し、リソース マネージャーでインターネットに接続するロード バランサーを作成する

[AZURE.INCLUDE [load-balancer-get-started-internet-arm-selectors-include.md](../../includes/load-balancer-get-started-internet-arm-selectors-include.md)]

[AZURE.INCLUDE [load-balancer-get-started-internet-intro-include.md](../../includes/load-balancer-get-started-internet-intro-include.md)]

[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/azure-arm-classic-important-include.md)]この記事では、リソース マネージャーのデプロイ モデルについて説明します。Azure クラシック デプロイ モデルについて確認する場合は、「[クラシック デプロイを使用したインターネットに接続するロード バランサーを作成する](load-balancer-get-started-internet-classic-portal.md)」を参照してください。

[AZURE.INCLUDE [load-balancer-get-started-internet-scenario-include.md](../../includes/load-balancer-get-started-internet-scenario-include.md)]

以下の手順では、PowerShell で Azure リソース マネージャーを使用して、インターネットに接続するロード バランサーを作成する方法を示します。Azure リソース マネージャーでは、インターネットに接続するロード バランサーを作成するための項目は、個別に構成されてから、リソースを作成するためにまとめられます。

このページでは、ロード バランサーを作成するために実行する必要のある一連の作業を個別に取り上げ、ロード バランサーを作成するという目的を達成するために実行する事柄を詳細に説明します。

## インターネットに接続するロード バランサーを作成するために必要な項目

ロード バランサーをデプロイするには、次のオブジェクトを作成して構成する必要があります。

- フロント エンド IP 構成 - 受信ネットワーク トラフィックのパブリック IP アドレスが含まれます。 

- バック エンド アドレス プール - ロード バランサーからトラフィックを受信するためのネットワーク インターフェイス (NIC) が含まれます。

- 負荷分散の規則 - ロード バランサーのパブリック ポートをバック エンド アドレス プール内の NIC のポートにマッピングする規則が含まれます。

- 受信 NAT 規則 - ロード バランサーのパブリック ポートをバック エンド アドレス プール内の個々の NIC のポートにマッピングする規則が含まれます。

- プローブ - バック エンド アドレス プール内の NIC にリンクされている VM の可用性を確認するための正常性プローブが含まれます。

Azure リソース マネージャーでのロード バランサー コンポーネントの詳細については、「[Azure リソース マネージャーによるロード バランサーのサポート](load-balancer-arm.md)」をご覧ください。


## リソース マネージャーを使用するように PowerShell をセットアップする
PowerShell 用 Azure モジュールが最新の製品版であり、Azure サブスクリプションにアクセスできるように PowerShell が正しく設定されていることを確認します。

### 手順 1

1. Azure PowerShell を初めて使用する場合は、[Azure PowerShell のインストールおよび構成方法](powershell-install-configure.md)を参照し、このページにある手順をすべて最後まで実行し、Azure にサインインしてサブスクリプションを選択します。
2. 次に示すように、Azure PowerShell プロンプトで **Switch-AzureMode** コマンドレットを実行して、リソース マネージャー モードに切り替えます。

		Switch-AzureMode AzureResourceManager
	
	予想される出力:

		WARNING: The Switch-AzureMode cmdlet is deprecated and will be removed in a future release.


>[AZURE.WARNING]Switch-AzureMode コマンドレットは間もなく廃止予定です。これらが廃止された場合は、すべてのリソース マネージャー コマンドレットの名前が変更されます。



### 手順 2.

Azure アカウントにログインします。


    PS C:\> Add-AzureAccount

資格情報を使用して認証を行うよう求められます。


### 手順 3.

使用する Azure サブスクリプションを選択します。

    PS C:\> Select-AzureSubscription -SubscriptionName "MySubscription"

使用できるサブスクリプションのリストを表示するには、Get-AzureSubscription コマンドレットを使用します。

## リソース グループの作成

Azure 地域の*米国西部*に「*NRP-RG*」という名前の新しいリソース グループを作成します。

    PS C:\> New-AzureResourceGroup -Name NRP-RG -location "West US"

## Virtual Network と、フロント エンド IP プールのパブリック IP アドレスの作成

### 手順 1

サブネットと仮想ネットワークを作成します。

	$backendSubnet = New-AzureVirtualNetworkSubnetConfig -Name LB-Subnet-BE -AddressPrefix 10.0.2.0/24
	New-AzurevirtualNetwork -Name NRPVNet -ResourceGroupName NRP-RG -Location "West US" -AddressPrefix 10.0.0.0/16 -Subnet $backendSubnet

### 手順 2

フロントエンド IP プールで使用される「*PublicIP*」という名前のパブリック IP アドレス (PIP) を作成します。DNS 名は「*loadbalancernrp.westus.cloudapp.azure.com*」です。次のコマンドでは、静的な割り当てタイプが使用されます。

	$publicIP = New-AzurePublicIpAddress -Name PublicIp -ResourceGroupName NRP-RG -Location "West US" –AllocationMethod Static -DomainNameLabel loadbalancernrp 

>[AZURE.IMPORTANT]ロード バランサーはその FQDN としてパブリック IP のドメイン ラベルを使用します。これは、ロード バランサー FQDN としてクラウド サービスを使用するクラシック デプロイ モデルからの変更点です。この例では、FQDN は *loadbalancernrp.westus.cloudapp.azure.com* になります。

## フロント エンド IP プールとバックエンド アドレス プールの作成

### 手順 1 

*PublicIp* PIP を使用する「*LB-Frontend*」という名前のフロント エンド IP プールを作成します。

	$frontendIP = New-AzureLoadBalancerFrontendIpConfig -Name LB-Frontend -PublicIpAddress $publicIP 

### 手順 2. 

「*LB-backend*」という名前のバックエンド アドレス プールを作成します。

	$beaddresspool= New-AzureLoadBalancerBackendAddressPoolConfig -Name "LB-backend"

## LB ルール、NAT 規則、プローブ、ロード バランサーの作成

次の例では、以下の項目が作成されます。

- ポート 3441 のすべての受信トラフィックをポート 3389 に転送する NAT 規則<sup>1</sup>。
- ポート 3442 のすべての受信トラフィックをポート 3389 に転送する NAT 規則。
- ポート 80 のすべての受信トラフィックをバック エンド プールのアドレスのポート 80 に負荷分散するロード バランサー規則。
- *HealthProbe.aspx* という名前のページで正常性状態を確認するプローブ規則。
- 上記のオブジェクトをすべて使用するロード バランサー。


<sup>1</sup> NAT 規則は、ロード バランサーの背後にある特定の仮想マシン インスタンスに関連付られています。ポート 3341 への受信ネットワーク トラフィックは、以下の例の NAT 規則に関連付けられている特定の仮想マシンのポート 3389 に送信されます。NAT 規則では、UDP または TCP のプロトコルを選択する必要があります。両方のプロトコルを同じポートに割り当てることはできません。

### 手順 1.

NAT 規則を作成します。

	$inboundNATRule1= New-AzureLoadBalancerInboundNatRuleConfig -Name "RDP1" -FrontendIpConfiguration $frontendIP -Protocol TCP -FrontendPort 3441 -BackendPort 3389

	$inboundNATRule2= New-AzureLoadBalancerInboundNatRuleConfig -Name "RDP2" -FrontendIpConfiguration $frontendIP -Protocol TCP -FrontendPort 3442 -BackendPort 3389

### 手順 2

ロード バランサー規則を作成します。

	$lbrule = New-AzureLoadBalancerRuleConfig -Name "HTTP" -FrontendIpConfiguration $frontendIP -BackendAddressPool  $beAddressPool -Probe $healthProbe -Protocol Tcp -FrontendPort 80 -BackendPort 80

### 手順 3.

正常性プローブを作成します。

	$healthProbe = New-AzureLoadBalancerProbeConfig -Name "HealthProbe" -RequestPath "HealthProbe.aspx" -Protocol http -Port 80 -IntervalInSeconds 15 -ProbeCount 2

### 手順 4.

上記で作成したオブジェクトを使用し、ロード バランサーを作成します。

	$NRPLB = New-AzureLoadBalancer -ResourceGroupName "NRP-RG" -Name "NRP-LB" -Location "West US" -FrontendIpConfiguration $frontendIP -InboundNatRule $inboundNATRule1,$inboundNatRule2 -LoadBalancingRule $lbrule -BackendAddressPool $beAddressPool -Probe $healthProbe 

## NIC の作成

NIC を作成し (または既存の NIC を変更し)、それを NAT 規則、ロード バランサー規則、プローブに関連付ける必要があります。

### 手順 1 

NIC を作成する必要がある VNet とサブネットを取得します。

	$vnet = Get-AzureVirtualNetwork -Name NRPVNet -ResourceGroupName NRP-RG
	$backendSubnet = Get-AzureVirtualNetworkSubnetConfig -Name LB-Subnet-BE -VirtualNetwork $vnet 

### 手順 2

「*lb-nic1-be*」という名前の NIC を作成し、それを最初の NAT 規則に関連付け、それから最初 (で唯一) のバックエンド アドレス プールに関連付けます。
	
	$backendnic1= New-AzureNetworkInterface -ResourceGroupName "NRP-RG" -Name lb-nic1-be -Location "West US" -PrivateIpAddress 10.0.2.6 -Subnet $backendSubnet -LoadBalancerBackendAddressPool $nrplb.BackendAddressPools[0] -LoadBalancerInboundNatRule $nrplb.InboundNatRules[0]

### 手順 3.

「*lb-nic2-be*」という名前の NIC を作成し、それを 2 番目の NAT 規則に関連付け、それから最初 (で唯一) のバックエンド アドレス プールに関連付けます。

	$backendnic2= New-AzureNetworkInterface -ResourceGroupName "NRP-RG" -Name lb-nic2-be -Location "West US" -PrivateIpAddress 10.0.2.7 -Subnet $backendSubnet -LoadBalancerBackendAddressPool $nrplb.BackendAddressPools[0] -LoadBalancerInboundNatRule $nrplb.InboundNatRules[1]

### 手順 4.

NIC を確認します。


	PS C:\> $backendnic1

予想される出力:

	Name                 : lb-nic1-be
	ResourceGroupName    : NRP-RG
	Location             : westus
	Id                   : /subscriptions/f50504a2-1865-4541-823a-b32842e3e0ee/resourceGroups/NRP-RG/providers/Microsoft.Network/networkInterfaces/lb-nic1-be
	Etag                 : W/"d448256a-e1df-413a-9103-a137e07276d1"
	ProvisioningState    : Succeeded
	Tags                 :
	VirtualMachine       : null
	IpConfigurations     : [
                         {
                           "PrivateIpAddress": "10.0.2.6",
                           "PrivateIpAllocationMethod": "Static",
                           "Subnet": {
                             "Id": "/subscriptions/f50504a2-1865-4541-823a-b32842e3e0ee/resourceGroups/NRP-RG/providers/Microsoft.Network/virtualNetworks/NRPVNet/subnets/LB-Subnet-BE"
                           },
                           "PublicIpAddress": {
                             "Id": null
                           },
                           "LoadBalancerBackendAddressPools": [
                             {
                               "Id": "/subscriptions/f50504a2-1865-4541-823a-b32842e3e0ee/resourceGroups/NRP-RG/providers/Microsoft.Network/loadBalancers/NRPlb/backendAddressPools/LB-backend"
                             }
                           ],
                           "LoadBalancerInboundNatRules": [
                             {
                               "Id": "/subscriptions/f50504a2-1865-4541-823a-b32842e3e0ee/resourceGroups/NRP-RG/providers/Microsoft.Network/loadBalancers/NRPlb/inboundNatRules/RDP1"
                             }
                           ],
                           "ProvisioningState": "Succeeded",
                           "Name": "ipconfig1",
                           "Etag": "W/"d448256a-e1df-413a-9103-a137e07276d1"",
                           "Id": "/subscriptions/f50504a2-1865-4541-823a-b32842e3e0ee/resourceGroups/NRP-RG/providers/Microsoft.Network/networkInterfaces/lb-nic1-be/ipConfigurations/ipconfig1"
                         }
                       ]
	DnsSettings          : {
                         "DnsServers": [],
                         "AppliedDnsServers": []
                       }
	AppliedDnsSettings   :
	NetworkSecurityGroup : null
	Primary              : False



### 手順 5.

`Add-AzureVMNetworkInterface` コマンドレットを使用し、NIC をさまざまな VM に割り当てます。

仮想マシンを作成し、NIC を割り当てる方法については、「[リソース マネージャーと Azure PowerShell を使用して、Windows 仮想マシンを作成し、事前構成する](virtual-machines-ps-create-preconfigure-windows-resource-manager-vms.md#Example)」を参照してください。例のオプション 5 を使用します。

## 既存のロード バランサーの更新


### 手順 1

上の例のロード バランサーを使用し、ロード バランサー オブジェクトを変数 $slb using Get-AzureLoadBalancer に割り当てます。

	$slb=get-azureLoadBalancer -Name NRPLB -ResourceGroupName NRP-RG

### 手順 2

次の例では、フロント エンドでポート 81、バック エンド プールでポート 8181 を使用し、既存のロード バランサーに新しい受信 NAT 規則を追加します。

	$slb | Add-AzureLoadBalancerInboundNatRuleConfig -Name NewRule -FrontendIpConfiguration $slb.FrontendIpConfigurations[0] -FrontendPort 81  -BackendPort 8181 -Protocol Tcp


### 手順 3.

Set-AzureLoadBalancer を使用して、新しい構成を保存します。

	$slb | Set-AzureLoadBalancer

## ロード バランサーの削除

コマンド Remove-AzureLoadBalancer を使用して、リソース グループ "NRP-RG" から、前に作成された "NRP-LB" という名前のロード バランサーを削除します。

	Remove-AzureLoadBalancer -Name NRPLB -ResourceGroupName NRP-RG

>[AZURE.NOTE]オプションのスイッチ -Force を使用することで、削除のためのプロンプトを回避できます。

## 次のステップ

[内部ロード バランサーの構成の開始](load-balancer-internal-getstarted.md)

[ロード バランサー分散モードの構成](load-balancer-distribution-mode.md)

[ロード バランサーのアイドル TCP タイムアウト設定の構成](load-balancer-tcp-idle-timeout.md)

<!---HONumber=Nov15_HO3-->