<properties
	pageTitle="VM 用の一般的なネットワークの Azure PowerShell コマンド | Microsoft Azure"
	description="仮想ネットワークと VM に関連するリソースの作成を開始する際に使用される一般的な PowerShell コマンド"
	services="virtual-machines-windows"
	documentationCenter=""
	authors="davidmu1"
	manager="timlt"
	editor=""
	tags="azure-resource-manager"/>

<tags
	ms.service="virtual-machines-windows"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="06/07/2016"
	ms.author="davidmu"/>

# VM 用の一般的なネットワークの Azure PowerShell コマンド

仮想マシンを作成するには、[仮想ネットワーク](../virtual-network/virtual-networks-overview.md)を作成するか、VM を追加できる既存の仮想ネットワークについて知る必要があります。通常、VM を作成するときに、この記事で説明されているリソースの作成を検討する必要があります。

## Azure PowerShell を使用してリソースを作成する

最新バージョンの Azure PowerShell をインストールし、使用するサブスクリプションを選択して、Azure アカウントにサインインする方法については、「[Azure PowerShell のインストールと構成の方法](../powershell-install-configure.md)」を参照してください。

リソース | コマンド 
-------------- | -------------------------
サブネット | $internetSubnet = [New-AzureRmVirtualNetworkSubnetConfig](https://msdn.microsoft.com/library/mt619412.aspx) -Name internetSubnet -AddressPrefix 10.0.1.0/24<BR>$internalSubnet = New-AzureRmVirtualNetworkSubnetConfig -Name internalSubnet -AddressPrefix 10.0.2.0/24<BR><BR>標準的なネットワークには、[インターネットに接続するロード バランサー](../load-balancer/load-balancer-internet-overview.md)のサブネットと[内部ロード バランサー](../load-balancer/load-balancer-internal-overview.md)の別のサブネットがある場合があります。 |
Virtual Network | 仮想ネットワークを作成する:<BR><BR>$rgName = "[リソース グループ名](../powershell-azure-resource-manager.md)"<BR>$locName = "[場所名](https://msdn.microsoft.com/library/azure/dn495177.aspx)"<BR>$vnetName = "仮想ネットワーク名"<BR>$vnet = [New-AzureRmVirtualNetwork](https://msdn.microsoft.com/library/mt603657.aspx) -Name $vnetName -ResourceGroupName $rgName -Location $locName -AddressPrefix 10.0.0.0/16 -Subnet $internetSubnet,$internalSubnet<BR><BR>仮想ネットワークの一覧を取得する:<BR><BR>[Get-AzureRmVirtualNetwork](https://msdn.microsoft.com/library/mt603515.aspx) -ResourceGroupName $rgName &#124; Sort Name &#124; Select Name<BR><BR>仮想ネットワーク内のサブネットの一覧を表示する:<BR><BR>Get-AzureRmVirtualNetwork -Name $vnetName -ResourceGroupName $rgName &#124; Select Subnets<BR><BR>サブネットの一覧を示す次のような内容が表示されます。<BR><BR>Subnets<BR>-------<BR>{internetSubnet, internalSubnet}<BR><BR>internetSubnet のサブネット インデックスは 0 です。internalSubnet のサブネット インデックスは 1 です。
ドメイン名のラベル | $domName = "ドメイン名"<BR>[Test-AzureRmDnsAvailability](https://msdn.microsoft.com/library/mt619419.aspx) -DomainQualifiedName $domName -Location $locName<BR><BR>[パブリック IP リソース](../virtual-network/virtual-network-ip-addresses-overview-arm.md)に DNS ドメイン名ラベルを指定して、Azure で管理される DNS サーバーのパブリック IP アドレスに対する domainnamelabel.location.cloudapp.azure.com のマッピングを作成できます。このラベルには、文字、数字、ハイフンのみを含めることができます。最初と最後の文字が英字または数字であり、ドメイン名のラベルが Azure の場所内で一意である必要があります。ドメイン名のラベルがグローバルに一意であるかどうかを必ずテストする必要があります。**True** が返された場合、提案した名前はグローバルに一意です。
パブリック IP アドレス | $ipName = "パブリック IP アドレス名"<BR>$pip = [New-AzureRmPublicIpAddress](https://msdn.microsoft.com/library/mt603620.aspx) -Name $ipName -ResourceGroupName $rgName -DomainNameLabel $domName -Location $locName -AllocationMethod Dynamic<BR><BR>パブリック IP アドレスは、事前に作成したドメイン名のラベルを使用し、ロード バランサーのフロントエンド構成で使用されます。
フロントエンド IP の構成 | $feConfigName = "フロントエンド IP 構成名"<BR>$frontendIP = [New-AzureRmLoadBalancerFrontendIpConfig](https://msdn.microsoft.com/library/mt603510.aspx) -Name $feConfigName -PublicIpAddress $pip<BR><BR>フロントエンド構成には、受信ネットワーク トラフィック用にあらかじめ作成したパブリック IP アドレスが含まれます。
バックエンド アドレス プール | $bePoolName = "バックエンド プール名"<BR>$beAddressPool = [New-AzureRmLoadBalancerBackendAddressPoolConfig](https://msdn.microsoft.com/library/mt603791.aspx) -Name $bePoolName<BR><BR>ネットワーク インターフェイス経由でアクセスされるロード バランサーのバックエンドの内部アドレスを指定します。
プローブ | $probeName = "正常性プローブ名"<BR>$healthProbe = [New-AzureRmLoadBalancerProbeConfig](https://msdn.microsoft.com/library/mt603847.aspx) -Name $probeName -RequestPath 'HealthProbe.aspx' -Protocol http -Port 80 -IntervalInSeconds 15 -ProbeCount 2<BR><BR>バックエンド アドレス プール内の仮想マシン インスタンスの可用性の確認に使用される正常性プローブが含まれます。
負荷分散規則 | $lbRule = [New-AzureRmLoadBalancerRuleConfig](https://msdn.microsoft.com/library/mt619391.aspx) -Name HTTP -FrontendIpConfiguration $frontendIP -BackendAddressPool $beAddressPool -Probe $healthProbe -Protocol Tcp -FrontendPort 80 -BackendPort 80<BR><BR>ロード バランサーのパブリック ポートをバックエンド アドレス プール内のポートにマッピングする規則が含まれます。
受信 NAT 規則 | $ruleName = "NAT ルール名"<BR>$inboundNATRule = [New-AzureRmLoadBalancerInboundNatRuleConfig](https://msdn.microsoft.com/library/mt603606.aspx) -Name $ruleName -FrontendIpConfiguration $frontendIP -Protocol TCP -FrontendPort 3441 -BackendPort 3389<BR><BR>ロード バランサーのパブリック ポートをバックエンド アドレス プール内の特定の仮想マシンのポートにマッピングする規則が含まれます。
Load Balancer | $lbName = "ロード バランサー名"<BR>$loadBalancer = [New-AzureRmLoadBalancer](https://msdn.microsoft.com/library/mt619450.aspx) -ResourceGroupName $rgName -Name $lbName -Location $locName -FrontendIpConfiguration $frontendIP -InboundNatRule $inboundNATRule -LoadBalancingRule $lbRule -BackendAddressPool $beAddressPool -Probe $healthProbe
ネットワーク インターフェイス | $vnet = [Get-AzureRmVirtualNetwork](https://msdn.microsoft.com/library/mt603515.aspx) -Name $vnetName -ResourceGroupName $rgName<BR>$internalSubnet = [Get-AzureRmVirtualNetworkSubnetConfig](https://msdn.microsoft.com/library/mt603817.aspx) -Name "internalSubnet" -VirtualNetwork $vnet<BR>$nicName = "ネットワーク インターフェイス名"<BR>$nic1= [New-AzureRmNetworkInterface](https://msdn.microsoft.com/library/mt619370.aspx) -ResourceGroupName $rgName -Name $nicName -Location $locName -PrivateIpAddress 10.0.2.6 -Subnet $internalSubnet -LoadBalancerBackendAddressPool $loadBalancer.BackendAddressPools[0] -LoadBalancerInboundNatRule $loadBalancer.InboundNatRules[0]<BR><BR>パブリック IP アドレスと事前に作成した仮想ネットワークのサブネットを使用して、ネットワーク インターフェイスを作成します。
	
## 次のステップ

- [VM を作成する](virtual-machines-windows-ps-create.md)ときに、作成したネットワーク インターフェイスを使用します。
- [複数のネットワーク インターフェイスを持つ VM を作成する](../virtual-network/virtual-networks-multiple-nics.md)方法について学習します。

<!---HONumber=AcomDC_0629_2016-->