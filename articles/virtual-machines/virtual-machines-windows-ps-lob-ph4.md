<properties 
	pageTitle="基幹業務アプリケーションのフェーズ 4 | Microsoft Azure" 
	description="Web サーバーを作成し、基幹業務アプリケーションを Web サーバーにロードします。" 
	documentationCenter=""
	services="virtual-machines-windows" 
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
	ms.date="01/21/2016" 
	ms.author="josephd"/>

# 基幹業務アプリケーションのワークロード フェーズ 4: Web サーバーを構成する

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)]クラシック デプロイ モデル。

高可用な基幹業務アプリケーションを Azure インフラストラクチャ サービスにデプロイする作業のこのフェーズでは、Web サーバーを構築して基幹業務アプリケーションをロードします。

[フェーズ 5](virtual-machines-windows-ps-lob-ph5.md) に進むには、このフェーズを完了する必要があります。全フェーズについては、「[Azure での高可用な基幹業務アプリケーションのデプロイ](virtual-machines-windows-lob-overview.md)」をご覧ください。

## Azure での Web サーバー仮想マシンの作成

ASP.NET アプリケーション、または Windows Server 2012 R2 のインターネット インフォメーション サービス (IIS) 8 でホストできる古いアプリケーションをデプロイできる、2 つの Web サーバー仮想マシンがあります。

最初に、基幹業務アプリケーションへのクライアント トラフィックが、Azure によって 2 つの Web サーバーに均等に配分されるように、内部ロード バランサーを構成します。この場合、名前とサブネットのアドレス空間 (Azure 仮想ネットワークに割り当てたアドレス空間) から割り当てられた独自の IP アドレスで構成される内部負荷分散インスタンスを指定する必要があります。

> [AZURE.NOTE] 次のコマンド セットは、Azure PowerShell 1.0 以降を使用します。詳細については、「[Azure PowerShell 1.0](https://azure.microsoft.com/blog/azps-1-0/)」を参照してください。

< and > の文字を削除して、各変数の値を指定します。次の Azure PowerShell コマンド セットでは、以下の表の値を使用します。

- 表 M (仮想マシン)
- 表 V (仮想ネットワーク設定)
- 表 S (サブネット)
- 表 ST (ストレージ アカウント)
- 表 A (可用性セット)

表 M は[フェーズ 2](virtual-machines-windows-ps-lob-ph2.md) で、V、S、ST、A の各表は[フェーズ 1](virtual-machines-windows-ps-lob-ph1.md) で定義したものです。

適切な値をすべて指定したら、Azure PowerShell コマンド プロンプトでそのブロックを実行します。

	# Set up key variables
	$rgName="<resource group name>"
	$locName="<Azure location of your resource group>"
	$vnetName="<Table V – Item 1 – Value column>"
	$privIP="<available IP address on the subnet>"
	$vnet=Get-AzureRMVirtualNetwork -Name $vnetName -ResourceGroupName $rgName

	$frontendIP=New-AzureRMLoadBalancerFrontendIpConfig -Name WebServers-LBFE -PrivateIPAddress $privIP -SubnetId $vnet.Subnets[1].Id
	$beAddressPool=New-AzureRMLoadBalancerBackendAddressPoolConfig -Name WebServers-LBBE

	# This example assumes unsecured (HTTP-based) web traffic to the web servers.
	$healthProbe=New-AzureRMLoadBalancerProbeConfig -Name WebServersProbe -Protocol "TCP" -Port 80 -IntervalInSeconds 15 -ProbeCount 2
	$lbrule=New-AzureRMLoadBalancerRuleConfig -Name "WebTraffic" -FrontendIpConfiguration $frontendIP -BackendAddressPool $beAddressPool -Probe $healthProbe -Protocol "TCP" -FrontendPort 80 -BackendPort 80
	New-AzureRMLoadBalancer -ResourceGroupName $rgName -Name "WebServersInAzure" -Location $locName -LoadBalancingRule $lbrule -BackendAddressPool $beAddressPool -Probe $healthProbe -FrontendIpConfiguration $frontendIP

次に、基幹業務アプリケーションの完全修飾ドメイン名 (lobapp.corp.contoso.com など) を、内部ロード バランサーに割り当てられている IP アドレス (前の Azure PowerShell コマンド ブロックの $privIP の値) に解決する DNS アドレス レコードを、組織の内部 DNS インフラストラクチャに追加します。

次に、PowerShell コマンドの次のブロックを使用して、2 つの Web サーバーの仮想マシンを作成します。

適切な値をすべて指定したら、Azure PowerShell プロンプトでそのブロックを実行します。

	# Set up key variables
	$rgName="<resource group name>"
	$locName="<Azure location of your resource group>"
	$webLB=Get-AzureRMLoadBalancer -ResourceGroupName $rgName -Name "WebServersInAzure"	
	
	# Use the standard storage account
	$saName="<Table ST – Item 2 – Storage account name column>"

	$vnetName="<Table V – Item 1 – Value column>"
	$beSubnetName="<Table S - Item 2 - Name column>"
	$avName="<Table A – Item 3 – Availability set name column>"
	$vnet=Get-AzureRMVirtualNetwork -Name $vnetName -ResourceGroupName $rgName
	$backendSubnet=Get-AzureRMVirtualNetworkSubnetConfig -Name $beSubnetName -VirtualNetwork $vnet
	
	# Create the first web server virtual machine
	$vmName="<Table M – Item 6 - Virtual machine name column>"
	$vmSize="<Table M – Item 6 - Minimum size column>"
	$nic=New-AzureRMNetworkInterface -Name ($vmName + "-NIC") -ResourceGroupName $rgName -Location $locName -Subnet $backendSubnet -LoadBalancerBackendAddressPool $webLB.BackendAddressPools[0]
	$avSet=Get-AzureRMAvailabilitySet -Name $avName –ResourceGroupName $rgName 
	$vm=New-AzureRMVMConfig -VMName $vmName -VMSize $vmSize -AvailabilitySetId $avset.Id
	$cred=Get-Credential -Message "Type the name and password of the local administrator account for the first web server." 
	$vm=Set-AzureRMVMOperatingSystem -VM $vm -Windows -ComputerName $vmName -Credential $cred -ProvisionVMAgent -EnableAutoUpdate
	$vm=Set-AzureRMVMSourceImage -VM $vm -PublisherName MicrosoftWindowsServer -Offer WindowsServer -Skus 2012-R2-Datacenter -Version "latest"
	$vm=Add-AzureRMVMNetworkInterface -VM $vm -Id $nic.Id
	$storageAcc=Get-AzureRMStorageAccount -ResourceGroupName $rgName -Name $saName
	$osDiskUri=$storageAcc.PrimaryEndpoints.Blob.ToString() + "vhds/" + $vmName + "-OSDisk.vhd"
	$vm=Set-AzureRMVMOSDisk -VM $vm -Name "OSDisk" -VhdUri $osDiskUri -CreateOption fromImage
	New-AzureRMVM -ResourceGroupName $rgName -Location $locName -VM $vm
	
	# Create the second web server virtual machine
	$vmName="<Table M – Item 7 - Virtual machine name column>"
	$vmSize="<Table M – Item 7 - Minimum size column>"
	$nic=New-AzureRMNetworkInterface -Name ($vmName + "-NIC") -ResourceGroupName $rgName -Location $locName -Subnet $backendSubnet -LoadBalancerBackendAddressPool $webLB.BackendAddressPools[0]
	$vm=New-AzureRMVMConfig -VMName $vmName -VMSize $vmSize -AvailabilitySetId $avset.Id
	$cred=Get-Credential -Message "Type the name and password of the local administrator account for the second SQL Server computer." 
	$vm=Set-AzureRMVMOperatingSystem -VM $vm -Windows -ComputerName $vmName -Credential $cred -ProvisionVMAgent -EnableAutoUpdate
	$vm=Set-AzureRMVMSourceImage -VM $vm -PublisherName MicrosoftWindowsServer -Offer WindowsServer -Skus 2012-R2-Datacenter -Version "latest"
	$vm=Add-AzureRMVMNetworkInterface -VM $vm -Id $nic.Id
	$storageAcc=Get-AzureRMStorageAccount -ResourceGroupName $rgName -Name $saName
	$osDiskUri=$storageAcc.PrimaryEndpoints.Blob.ToString() + "vhds/" + $vmName + "-OSDisk.vhd"
	$vm=Set-AzureRMVMOSDisk -VM $vm -Name "OSDisk" -VhdUri $osDiskUri -CreateOption fromImage
	New-AzureRMVM -ResourceGroupName $rgName -Location $locName -VM $vm

> [AZURE.NOTE] これらの仮想マシンはイントラネット アプリケーション用であるため、パブリック IP アドレスや DNS ドメイン名ラベルは割り当てられず、インターネットに公開されません。ただし、これは Azure ポータルから接続できないことを意味します。仮想マシンのプロパティを表示する際に、**[接続]** ボタンは使用できません。

好みのリモート デスクトップ クライアントを使用し、Web サーバー仮想マシンのそれぞれへのリモート デスクトップ接続を作成します。仮想マシンのイントラネット DNS 名またはコンピューター名と、ローカル管理者アカウントの資格情報を使用します。

次に、Windows PowerShell プロンプトで次のコマンドを実行し、各 Web サーバー仮想マシンを適切な Active Directory ドメインに参加させます。

	$domName="<Active Directory domain name to join, such as corp.contoso.com>"
	Add-Computer -DomainName $domName
	Restart-Computer

**Add-Computer** コマンドを入力した後、ドメイン アカウントの資格情報を指定する必要があることに注意してください。

仮想マシンの再起動後、ローカル管理者特権を持つアカウントを使用して再接続します。

次に、各 Web サーバーに IIS をインストールして構成します。

1. サーバー マネージャーを実行し、**[役割と機能の追加]** をクリックします。
2. [開始する前に] ページで **[次へ]** をクリックします。
3. [インストールの種類の選択] ページで **[次へ]** をクリックします。
4. [対象サーバーの選択] ページで **[次へ]** をクリックします。
5. [サーバーの役割] ページの **[役割]** の一覧で、**[Web サーバー (IIS)]** をクリックします。
6. メッセージが表示されたら、**[機能の追加]** をクリックし、**[次へ]** をクリックします。
7. [機能の選択] ページで **[次へ]** をクリックします。
8. [Web サーバー (IIS)] ページで **[次へ]** をクリックします。
9. [役割サービスの選択] ページで、LOB アプリケーションでの必要性に応じて、サービスのチェック ボックスをオンまたはオフにし、**[次へ]** をクリックします。10. [インストール オプションの確認] ページで、**[インストール]** をクリックします。

## Web サーバー仮想マシンへの基幹業務アプリケーションのデプロイ

オンプレミス ネットワーク上のコンピューターで、次を実行します。

1.	基幹業務アプリケーション用のファイルを 2 つの Web サーバーに追加します。
2.	SQL Server クラスターに基幹業務アプリケーション用のデータベースを作成します。
3.	基幹業務アプリケーションへのアクセスとその機能をテストします。

次の図は、このフェーズが問題なく完了したときの構成を示しています。

![](./media/virtual-machines-windows-ps-lob-ph4/workload-lobapp-phase4.png)

## 次のステップ

- [フェーズ 5](virtual-machines-windows-ps-lob-ph5.md) を使用して、このワークロードの構成を完了します。

<!---HONumber=AcomDC_0323_2016-->