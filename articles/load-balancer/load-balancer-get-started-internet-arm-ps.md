<properties 
   pageTitle="リソース マネージャーで PowerShell を使用してインターネットに接続するロード バランサーを作成する | Microsoft Azure"
   description="リソース マネージャーで PowerShell を使用してインターネットに接続するロード バランサーを作成する方法について説明します"
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
   ms.date="04/05/2016"
   ms.author="joaoma" />

# リソース マネージャーで PowerShell を使用して、インターネットに接続するロード バランサーを作成を開始する

[AZURE.INCLUDE [load-balancer-get-started-internet-arm-selectors-include.md](../../includes/load-balancer-get-started-internet-arm-selectors-include.md)]

[AZURE.INCLUDE [load-balancer-get-started-internet-intro-include.md](../../includes/load-balancer-get-started-internet-intro-include.md)]

[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/azure-arm-classic-important-include.md)] この記事では、リソース マネージャーのデプロイ モデルについて説明します。[従来のデプロイ モデルを使用してインターネットに接続するロード バランサーを作成する方法](load-balancer-get-started-internet-classic-cli.md)についても説明します。

[AZURE.INCLUDE [load-balancer-get-started-internet-scenario-include.md](../../includes/load-balancer-get-started-internet-scenario-include.md)]

以下の手順では、PowerShell で Azure リソース マネージャーを使用して、インターネットに接続するロード バランサーを作成する方法を示します。Azure リソース マネージャーでは、インターネットに接続するロード バランサーを作成するための項目は、個別に構成されてから、リソースを作成するためにまとめられます。

ここでは、ロード バランサーを作成するために実行する必要のある一連の作業を個別に取り上げ、目的を達成するために実行する事柄を詳しく説明します。

## インターネットに接続するロード バランサーを作成するために必要な項目

ロード バランサーをデプロイするには、次のオブジェクトを作成して構成する必要があります。

- フロント エンド IP 構成 - 受信ネットワーク トラフィックのパブリック IP アドレスが含まれます。

- バック エンド アドレス プール - ロード バランサーからネットワーク トラフィックを受信する、仮想マシンのネットワーク インターフェイス (NIC) が含まれます。

- 負荷分散規則 - ロード バランサーのパブリック ポートをバック エンド アドレス プール内のポートにマッピングする規則が含まれます。

- 受信 NAT 規則 - ロード バランサーのパブリック ポートをバック エンド アドレス プール内の特定の仮想マシンのポートにマッピングする規則が含まれます。

- プローブ - バック エンド アドレス プール内の仮想マシン インスタンスの可用性を確認するために使用する正常性プローブが含まれます。

Azure リソース マネージャーでのロード バランサー コンポーネントの詳細については、「[Azure リソース マネージャーによる Load Balancer のサポート](load-balancer-arm.md)」をご覧ください。


## リソース マネージャーを使用するように PowerShell をセットアップする

PowerShell 用 Azure リソース マネージャー (ARM) モジュールが最新の製品版であることを確認します。

### 手順 1.

		Login-AzureRmAccount

資格情報を使用して認証を行うように求めるメッセージが表示されます。<BR>

### 手順 2.

アカウントのサブスクリプションを確認する

		Get-AzureRmSubscription 

### 手順 3. 

使用する Azure サブスクリプションを選択します。<BR>

		Select-AzureRmSubscription -SubscriptionId 'GUID of subscription'

### 手順 4.

新しいリソース グループを作成します (既存のリソース グループを使用する場合は、この手順をスキップしてください)。


    	New-AzureRmResourceGroup -Name NRP-RG -location "West US"


## Virtual Network と、フロント エンド IP プールのパブリック IP アドレスの作成

### 手順 1.

サブネットと仮想ネットワークを作成します。

    $backendSubnet = New-AzureRmVirtualNetworkSubnetConfig -Name LB-Subnet-BE -AddressPrefix 10.0.2.0/24
    New-AzureRmvirtualNetwork -Name NRPVNet -ResourceGroupName NRP-RG -Location 'West US' -AddressPrefix 10.0.0.0/16 -Subnet $backendSubnet

### 手順 2.

フロントエンド IP プールで使用される *PublicIP* という名前の Azure パブリック IP アドレス (PIP) リソースを作成します。DNS 名は *loadbalancernrp.westus.cloudapp.azure.com* です。次のコマンドでは、静的な割り当てタイプが使用されます。

	$publicIP = New-AzureRmPublicIpAddress -Name PublicIp -ResourceGroupName NRP-RG -Location 'West US' –AllocationMethod Static -DomainNameLabel loadbalancernrp 

>[AZURE.IMPORTANT] ロード バランサーはその FQDN のプレフィックスとしてパブリック IP のドメイン ラベルを使用します。これは、ロード バランサー FQDN としてクラウド サービスを使用するクラシック デプロイ モデルからの変更点です。この例では、FQDN は *loadbalancernrp.westus.cloudapp.azure.com* になります。

## フロントエンド IP プールとバックエンド アドレス プールの作成

### 手順 1. 

 *PublicIp* PIP を使用する *LB-Frontend* という名前のフロントエンド IP プールを作成します。

	$frontendIP = New-AzureRmLoadBalancerFrontendIpConfig -Name LB-Frontend -PublicIpAddress $publicIP 

### 手順 2. 

*LB-backend* という名前のバックエンド アドレス プールを作成します。

	$beaddresspool = New-AzureRmLoadBalancerBackendAddressPoolConfig -Name LB-backend

## LB ルール、NAT 規則、プローブ、およびロード バランサーの作成

次の例では、以下の項目が作成されます。

- ポート 3441 のすべての受信トラフィックをポート 3389 に転送する NAT 規則。
- ポート 3442 のすべての受信トラフィックをポート 3389 に転送する NAT 規則。
- バックエンド プールのアドレスでポート 80 ～ 80 に入ってくるすべてのトラフィックを分散するロード バランサー規則。
- *HealthProbe.aspx* という名前のページで正常性状態を確認するプローブ規則。
- 上記のオブジェクトをすべて使用するロード バランサー。


### 手順 1

NAT 規則を作成します。

	$inboundNATRule1= New-AzureRmLoadBalancerInboundNatRuleConfig -Name RDP1 -FrontendIpConfiguration $frontendIP -Protocol TCP -FrontendPort 3441 -BackendPort 3389

	$inboundNATRule2= New-AzureRmLoadBalancerInboundNatRuleConfig -Name RDP2 -FrontendIpConfiguration $frontendIP -Protocol TCP -FrontendPort 3442 -BackendPort 3389

### 手順 2

ロード バランサー規則を作成します。

	$lbrule = New-AzureRmLoadBalancerRuleConfig -Name HTTP -FrontendIpConfiguration $frontendIP -BackendAddressPool  $beAddressPool -Probe $healthProbe -Protocol Tcp -FrontendPort 80 -BackendPort 80

### 手順 3.

正常性プローブを作成します。プローブは次の 2 とおりの方法で構成できます。
 
HTTP プローブ
	
	$healthProbe = New-AzureRmLoadBalancerProbeConfig -Name HealthProbe -RequestPath 'HealthProbe.aspx' -Protocol http -Port 80 -IntervalInSeconds 15 -ProbeCount 2
または

TCP プローブ
	
	$healthProbe = New-AzureRmLoadBalancerProbeConfig -Name HealthProbe -Protocol Tcp -Port 80 -IntervalInSeconds 15 -ProbeCount 2

### 手順 4.

上記で作成したオブジェクトを使用し、ロード バランサーを作成します。

	$NRPLB = New-AzureRmLoadBalancer -ResourceGroupName NRP-RG -Name NRP-LB -Location 'West US' -FrontendIpConfiguration $frontendIP -InboundNatRule $inboundNATRule1,$inboundNatRule2 -LoadBalancingRule $lbrule -BackendAddressPool $beAddressPool -Probe $healthProbe

## NIC の作成

ネットワーク インターフェイスを作成し (または既存のネットワーク インターフェイスを変更し)、それを NAT 規則、ロード バランサー規則、プローブに関連付ける必要があります。

### 手順 1. 

NIC の作成先となる Virtual Network と Virtual Network のサブネットを取得します。

	$vnet = Get-AzureRmVirtualNetwork -Name NRPVNet -ResourceGroupName NRP-RG
	$backendSubnet = Get-AzureRmVirtualNetworkSubnetConfig -Name LB-Subnet-BE -VirtualNetwork $vnet 

### 手順 2.

 *lb-nic1-be* という名前の NIC を作成し、それを最初の NAT 規則に関連付け、それから最初 (で唯一) のバックエンド アドレス プールに関連付けます。
	
	$backendnic1= New-AzureRmNetworkInterface -ResourceGroupName NRP-RG -Name lb-nic1-be -Location 'West US' -PrivateIpAddress 10.0.2.6 -Subnet $backendSubnet -LoadBalancerBackendAddressPool $nrplb.BackendAddressPools[0] -LoadBalancerInboundNatRule $nrplb.InboundNatRules[0]

### 手順 3.

 *lb-nic2-be* という名前の NIC を作成し、それを 2 番目の NAT 規則に関連付け、それから最初 (で唯一) のバックエンド アドレス プールに関連付けます。

	$backendnic2= New-AzureRmNetworkInterface -ResourceGroupName NRP-RG -Name lb-nic2-be -Location 'West US' -PrivateIpAddress 10.0.2.7 -Subnet $backendSubnet -LoadBalancerBackendAddressPool $nrplb.BackendAddressPools[0] -LoadBalancerInboundNatRule $nrplb.InboundNatRules[1]

### 手順 4.

NIC を確認します。


	$backendnic1

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

`Add-AzureRmVMNetworkInterface` コマンドレットを使用し、NIC をさまざまな VM に割り当てます。

仮想マシンを作成し、NIC を割り当てる方法については、「[リソース マネージャーと Azure PowerShell を使用して、Windows 仮想マシンを作成し、事前構成する](../virtual-machines/virtual-machines-windows-create-powershell.md#Example)」を参照してください。例のオプション 5 を使用します。


既に仮想マシンを作成済みの場合は、次の手順でネットワーク インターフェイスを追加することができます。

#### 手順 1. 

変数にロード バランサーのリソースを読み込みます (まだ実行していない場合)。変数名は $lb で、上記で作成したロード バランサーのリソースと同じ名前を使用します。

	$lb= get-azurermloadbalancer –name NRP-LB -resourcegroupname NRP-RG

#### 手順 2. 

変数にバックエンド構成を読み込みます。

	$backend=Get-AzureRmLoadBalancerBackendAddressPoolConfig -name backendpool1 -LoadBalancer $lb

#### 手順 3. 

変数に作成済みのネットワーク インターフェイスを読み込みます。変数名は $nic で、使用するネットワーク インターフェイス名は、上記の例と同じです。

	$nic =get-azurermnetworkinterface –name lb-nic1-be -resourcegroupname NRP-RG

#### 手順 4.

ネットワーク インターフェイス上のバックエンド構成を変更します。

	$nic.IpConfigurations[0].LoadBalancerBackendAddressPools=$backend

#### 手順 5. 

ネットワーク インターフェイス オブジェクトを保存します。

	Set-AzureRmNetworkInterface -NetworkInterface $nic

ネットワーク インターフェイスがロード バランサーのバックエンド プールに追加されると、そのロード バランサー リソースの負荷分散規則に基づいてネットワーク トラフィックの受信を開始します。

## 既存のロード バランサーの更新


### 手順 1

上の例のロード バランサーを使用し、ロード バランサーのオブジェクトを変数 $slb using Get-AzureLoadBalancer に割り当てます。

	$slb = get-AzureRmLoadBalancer -Name NRPLB -ResourceGroupName NRP-RG

### 手順 2

次の例では、フロント エンドでポート 81、バック エンド プールでポート 8181 を使用し、既存のロード バランサーに新しい受信 NAT 規則を追加します。

	$slb | Add-AzureRmLoadBalancerInboundNatRuleConfig -Name NewRule -FrontendIpConfiguration $slb.FrontendIpConfigurations[0] -FrontendPort 81  -BackendPort 8181 -Protocol TCP


### 手順 3.

Set-AzureLoadBalancer を使用して、新しい構成を保存します。

	$slb | Set-AzureRmLoadBalancer

## Load Balancer の削除

`Remove-AzureLoadBalancer` コマンドを使用して、"NRP-RG" というリソース グループから、以前に作成された "NRP-LB" という名前のロード バランサーを削除します。

	Remove-AzureRmLoadBalancer -Name NRPLB -ResourceGroupName NRP-RG

>[AZURE.NOTE] オプションのスイッチ -Force を使用することで、削除のためのプロンプトを回避できます。

## 次のステップ

[内部ロード バランサーの構成の開始](load-balancer-get-started-ilb-arm-ps.md)

[ロード バランサー分散モードの構成](load-balancer-distribution-mode.md)

[ロード バランサーのアイドル TCP タイムアウト設定の構成](load-balancer-tcp-idle-timeout.md)

<!---HONumber=AcomDC_0629_2016-->