<properties
	pageTitle="SharePoint Server 2013 ファームのフェーズ 4 | Microsoft Azure"
	description="SharePoint サーバーの仮想マシンおよび新しい SharePoint ファームを作成します。"
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
	ms.date="04/01/2016"
	ms.author="josephd"/>

# SharePoint イントラネット ファーム ワークロードのフェーズ 4: SharePoint サーバーの構成

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)]クラシック デプロイ モデル。

イントラネット専用 SharePoint 2013 ファームと SQL Server AlwaysOn 可用性グループを Azure インフラストラクチャ サービスにデプロイする作業のこのフェーズでは、SharePoint ファームのアプリケーション層と Web 層を構築し、SharePoint 構成ウィザードを使用してファームを作成します。

[フェーズ 5](virtual-machines-windows-ps-sp-intranet-ph5.md) に進むには、このフェーズを完了する必要があります。全フェーズについては、「[Azure での SharePoint と SQL Server AlwaysOn 可用性グループのデプロイ](virtual-machines-windows-sp-intranet-overview.md)」をご覧ください。

## Azure での SharePoint サーバー仮想マシンの作成

SharePoint サーバー仮想マシンは 4 つあります。2 つはフロントエンド Web サーバー用であり、残りの 2 つは SharePoint アプリケーションの管理およびホスト用です。各層の 2 つの SharePoint サーバーによって高可用性が実現されます。

最初に、クライアント トラフィックが、Azure によって 2 つのフロントエンド Web サーバーに均等に配分されるように、内部ロード バランサーを構成します。この場合、名前とサブネットのアドレス空間 (Azure 仮想ネットワークに割り当てたアドレス空間) から取得した独自の IP アドレスで構成される内部負荷分散インスタンスを指定する必要があります。

> [AZURE.NOTE] 次のコマンド セットは、Azure PowerShell 1.0 以降を使用します。詳細については、「[Azure PowerShell 1.0](https://azure.microsoft.com/blog/azps-1-0/)」を参照してください。

< and > の文字を削除して、各変数の値を指定します。次の Azure PowerShell コマンド セットでは、以下の表の値を使用します。

- 表 M (仮想マシン)
- 表 V (仮想ネットワーク設定)
- 表 S (サブネット)
- 表 ST (ストレージ アカウント)
- 表 A (可用性セット)

表 M は「[フェーズ 2: ドメイン コントローラーの構成](virtual-machines-windows-ps-sp-intranet-ph2.md)」で、表 V、S、ST、A は「[フェーズ 1: Azure の構成](virtual-machines-windows-ps-sp-intranet-ph1.md)」でそれぞれ定義したものです。

適切な値をすべて指定したら、Azure PowerShell コマンド プロンプトでそのブロックを実行します。

	# Set up key variables
	$rgName="<resource group name>"
	$locName="<Azure location of your resource group>"
	$vnetName="<Table V – Item 1 – Value column>"
	$privIP="<available IP address on the subnet>"
	$vnet=Get-AzureRMVirtualNetwork -Name $vnetName -ResourceGroupName $rgName

	$frontendIP=New-AzureRMLoadBalancerFrontendIpConfig -Name SharePointWebServers-LBFE -PrivateIPAddress $privIP -SubnetId $vnet.Subnets[1].Id
	$beAddressPool=New-AzureRMLoadBalancerBackendAddressPoolConfig -Name SharePointWebServers-LBBE

	# This example assumes unsecured (HTTP-based) web traffic to the web servers.
	$healthProbe=New-AzureRMLoadBalancerProbeConfig -Name WebServersProbe -Protocol "TCP" -Port 80 -IntervalInSeconds 15 -ProbeCount 2
	$lbrule=New-AzureRMLoadBalancerRuleConfig -Name "WebTraffic" -FrontendIpConfiguration $frontendIP -BackendAddressPool $beAddressPool -Probe $healthProbe -Protocol "TCP" -FrontendPort 80 -BackendPort 80
	New-AzureRMLoadBalancer -ResourceGroupName $rgName -Name "SharePointWebServersInAzure" -Location $locName -LoadBalancingRule $lbrule -BackendAddressPool $beAddressPool -Probe $healthProbe -FrontendIpConfiguration $frontendIP

次に、SharePoint ファームの完全修飾ドメイン名 (spfarm.corp.contoso.com など) を、内部ロード バランサーに割り当てられている IP アドレス (前の Azure PowerShell コマンド ブロックの $privIP の値) に解決する DNS アドレス レコードを、組織の内部 DNS インフラストラクチャに追加します。

Azure PowerShell コマンドの次のブロックを使用して、4 つの SharePoint サーバーの仮想マシンを作成します。適切な値をすべて指定したら、Azure PowerShell コマンド プロンプトでそのブロックを実行します。

	# Set up key variables
	$rgName="<resource group name>"
	$locName="<Azure location of your resource group>"
	$saName="<Table ST – Item 2 – Storage account name column>"
	$vnetName="<Table V – Item 1 – Value column>"
	$avName="<Table A – Item 3 – Availability set name column>"
	
	# Create the first application server
	$vmName="<Table M – Item 6 - Virtual machine name column>"
	$vmSize="<Table M – Item 6 - Minimum size column>"
	$vnet=Get-AzureRMVirtualNetwork -Name $vnetName -ResourceGroupName $rgName
	$nic=New-AzureRMNetworkInterface -Name ($vmName +"-NIC") -ResourceGroupName $rgName -Location $locName -SubnetId $vnet.Subnets[1].Id
	$avSet=Get-AzureRMAvailabilitySet –Name $avName –ResourceGroupName $rgName 
	$vm=New-AzureRMVMConfig -VMName $vmName -VMSize $vmSize -AvailabilitySetId $avset.Id
	$cred=Get-Credential -Message "Type the name and password of the local administrator account for the first application server." 
	$vm=Set-AzureRMVMOperatingSystem -VM $vm -Windows -ComputerName $vmName -Credential $cred -ProvisionVMAgent -EnableAutoUpdate
	$vm=Set-AzureRMVMSourceImage -VM $vm -PublisherName MicrosoftSharePoint -Offer MicrosoftSharePointServer -Skus 2013 -Version "latest"
	$vm=Add-AzureRMVMNetworkInterface -VM $vm -Id $nic.Id
	$storageAcc=Get-AzureRMStorageAccount -ResourceGroupName $rgName -Name $saName
	$osDiskUri=$storageAcc.PrimaryEndpoints.Blob.ToString() + "vhds/" + $vmName + "-OSDisk.vhd"
	$vm=Set-AzureRMVMOSDisk -VM $vm -Name "OSDisk" -VhdUri $osDiskUri -CreateOption fromImage
	New-AzureRMVM -ResourceGroupName $rgName -Location $locName -VM $vm

	# Create the second application server
	$vmName="<Table M – Item 7 - Virtual machine name column>"
	$vmSize="<Table M – Item 7 - Minimum size column>"
	$vnet=Get-AzureRMVirtualNetwork -Name $vnetName -ResourceGroupName $rgName
	$nic=New-AzureRMNetworkInterface -Name ($vmName +"-NIC") -ResourceGroupName $rgName -Location $locName -SubnetId $vnet.Subnets[1].Id
	$avSet=Get-AzureRMAvailabilitySet –Name $avName –ResourceGroupName $rgName 
	$vm=New-AzureRMVMConfig -VMName $vmName -VMSize $vmSize -AvailabilitySetId $avset.Id
	$cred=Get-Credential -Message "Type the name and password of the local administrator account for the second application server." 
	$vm=Set-AzureRMVMOperatingSystem -VM $vm -Windows -ComputerName $vmName -Credential $cred -ProvisionVMAgent -EnableAutoUpdate
	$vm=Set-AzureRMVMSourceImage -VM $vm -PublisherName MicrosoftSharePoint -Offer MicrosoftSharePointServer -Skus 2013 -Version "latest"
	$vm=Add-AzureRMVMNetworkInterface -VM $vm -Id $nic.Id
	$storageAcc=Get-AzureRMStorageAccount -ResourceGroupName $rgName -Name $saName
	$osDiskUri=$storageAcc.PrimaryEndpoints.Blob.ToString() + "vhds/" + $vmName + "-OSDisk.vhd"
	$vm=Set-AzureRMVMOSDisk -VM $vm -Name "OSDisk" -VhdUri $osDiskUri -CreateOption fromImage
	New-AzureRMVM -ResourceGroupName $rgName -Location $locName -VM $vm

	# Change the availability set
	$avName="<Table A – Item 4 – Availability set name column>"

	# Set up key variables
	$beSubnetName="<Table S - Item 2 - Name column>"
	$webLB=Get-AzureRMLoadBalancer -ResourceGroupName $rgName -Name "SharePointWebServersInAzure"	
	$vnet=Get-AzureRMVirtualNetwork -Name $vnetName -ResourceGroupName $rgName
	$backendSubnet=Get-AzureRMVirtualNetworkSubnetConfig -Name $beSubnetName -VirtualNetwork $vnet
	
	# Create the first front end web server virtual machine
	$vmName="<Table M – Item 8 - Virtual machine name column>"
	$vmSize="<Table M – Item 8 - Minimum size column>"
	$nic=New-AzureRMNetworkInterface -Name ($vmName + "-NIC") -ResourceGroupName $rgName -Location $locName -Subnet $backendSubnet -LoadBalancerBackendAddressPool $webLB.BackendAddressPools[0]
	$avSet=Get-AzureRMAvailabilitySet -Name $avName –ResourceGroupName $rgName 
	$vm=New-AzureRMVMConfig -VMName $vmName -VMSize $vmSize -AvailabilitySetId $avset.Id
	$cred=Get-Credential -Message "Type the name and password of the local administrator account for the first front end web server." 
	$vm=Set-AzureRMVMOperatingSystem -VM $vm -Windows -ComputerName $vmName -Credential $cred -ProvisionVMAgent -EnableAutoUpdate
	$vm=Set-AzureRMVMSourceImage -VM $vm -PublisherName MicrosoftSharePoint -Offer MicrosoftSharePointServer -Skus 2013 -Version "latest"
	$vm=Add-AzureRMVMNetworkInterface -VM $vm -Id $nic.Id
	$storageAcc=Get-AzureRMStorageAccount -ResourceGroupName $rgName -Name $saName
	$osDiskUri=$storageAcc.PrimaryEndpoints.Blob.ToString() + "vhds/" + $vmName + "-OSDisk.vhd"
	$vm=Set-AzureRMVMOSDisk -VM $vm -Name "OSDisk" -VhdUri $osDiskUri -CreateOption fromImage
	New-AzureRMVM -ResourceGroupName $rgName -Location $locName -VM $vm
	
	# Create the second front end web server virtual machine
	$vmName="<Table M – Item 9 - Virtual machine name column>"
	$vmSize="<Table M – Item 9 - Minimum size column>"
	$nic=New-AzureRMNetworkInterface -Name ($vmName + "-NIC") -ResourceGroupName $rgName -Location $locName -Subnet $backendSubnet -LoadBalancerBackendAddressPool $webLB.BackendAddressPools[0]
	$vm=New-AzureRMVMConfig -VMName $vmName -VMSize $vmSize -AvailabilitySetId $avset.Id
	$cred=Get-Credential -Message "Type the name and password of the local administrator account for the second front end web server." 
	$vm=Set-AzureRMVMOperatingSystem -VM $vm -Windows -ComputerName $vmName -Credential $cred -ProvisionVMAgent -EnableAutoUpdate
	$vm=Set-AzureRMVMSourceImage -VM $vm -PublisherName MicrosoftSharePoint -Offer MicrosoftSharePointServer -Skus 2013 -Version "latest"
	$vm=Add-AzureRMVMNetworkInterface -VM $vm -Id $nic.Id
	$storageAcc=Get-AzureRMStorageAccount -ResourceGroupName $rgName -Name $saName
	$osDiskUri=$storageAcc.PrimaryEndpoints.Blob.ToString() + "vhds/" + $vmName + "-OSDisk.vhd"
	$vm=Set-AzureRMVMOSDisk -VM $vm -Name "OSDisk" -VhdUri $osDiskUri -CreateOption fromImage
	New-AzureRMVM -ResourceGroupName $rgName -Location $locName -VM $vm

> [AZURE.NOTE] これらの仮想マシンはイントラネット アプリケーション用であるため、パブリック IP アドレスや DNS ドメイン名ラベルは割り当てられず、インターネットに公開されません。ただし、これは Azure ポータルから接続できないことを意味します。仮想マシンのプロパティを表示する際に、**[接続]** ボタンは使用できません。

好みのリモート デスクトップ クライアントを使用し、仮想マシンのそれぞれへのリモート デスクトップ接続を作成します。仮想マシンのイントラネット DNS 名またはコンピューター名と、ローカル管理者アカウントの資格情報を使用します。

次に、Windows PowerShell プロンプトで次のコマンドを実行し、各仮想マシンを適切な Active Directory ドメインに参加させます。

	$domName="<Active Directory domain name to join, such as corp.contoso.com>"
	Add-Computer -DomainName $domName
	Restart-Computer

**Add-Computer** コマンドを入力した後、ドメイン アカウントの資格情報を指定する必要があることに注意してください。

再起動したら、「[リモート デスクトップ接続を使用した仮想マシンへのログオン](virtual-machines-windows-ps-sp-intranet-ph2.md#logon)」の手順を 4 回 (SharePoint サーバーごとに 1 回) 実行し、[Domain]\\sp\_farm\_db アカウントの資格情報を使用してログオンします。これらの資格情報は、「[フェーズ 2: ドメイン コント ローラーを構成する](virtual-machines-windows-ps-sp-intranet-ph2.md)」で作成しました。

「[接続をテストするには](virtual-machines-windows-ps-sp-intranet-ph2.md#testconn)」の手順を 4 回 (SharePoint サーバーごとに 1 回) 実行して、組織のネットワーク上の場所への接続をテストします。

> [AZURE.NOTE] SharePoint サーバーは、SharePoint Server 2013 試用版のイメージから作成されます。インストールを変換して SharePoint Server 2013 の Standard エディションまたは Enterprise エディションの製品版またはボリューム ライセンス キーを使用する必要があります。

## SharePoint ファームの構成

ファームの最初の SharePoint サーバーを構成するには、次の手順に従います。

1.	最初の SharePoint アプリケーション サーバーのデスクトップで、**[SharePoint 2013 製品構成ウィザード]** をダブルクリックします。プログラムがコンピューターに変更を加えることを許可するよう求められたら、**[はい]** をクリックします。
2.	**[SharePoint 製品へようこそ]** ページで **[次へ]** をクリックします。
3.	サービス (IIS など) が再起動またはリセットされることを警告する **[SharePoint 製品構成ウィザード]** ダイアログが表示されます。**[はい]** をクリックします。
4.	**[サーバー ファームへの接続]** ページで、**[新しいサーバー ファームの作成]** をクリックし、**[次へ]** をクリックします。
5.	**[構成データベースの設定]** ページで、次の操作を行います。
 - **[データベース サーバー]** に、プライマリ データベース サーバーの名前を入力します。
 - **[ユーザー名]** に、「[Domain]**\\sp\_farm\_db** (「[フェーズ 2: ドメイン コントローラーを構成する](virtual-machines-windows-ps-sp-intranet-ph2.md)」で作成)」と入力します。sp\_farm\_db アカウントには、データベース サーバーに対する sysadmin 権限があります。
 - **[パスワード]** に、sp\_farm\_db アカウントのパスワードを入力します。
6.	**[次へ]** をクリックします。
7.	**[ファームのセキュリティ設定の指定]** ページで、パスフレーズを 2 回入力します。後で参照できるように、パスフレーズを記録して、安全な場所に保管しておきます。**[次へ]** をクリックします。
8.	**[SharePoint サーバーの全体管理 Web アプリケーションの構成]** ページで、**[次へ]** をクリックします。
9.	**[SharePoint 製品構成ウィザードの終了]** ページが表示されます。**[次へ]** をクリックします。
10.	**[SharePoint 製品の構成中]** ページが表示されます。構成プロセスが完了するまで約 8 分待ちます。
11.	ファームが正常に構成されたら、**[完了]** をクリックします。新しい管理 Web サイトが開始されます。
12.	SharePoint ファームの構成を開始するには、**[ウィザードの開始]** をクリックします。

2 番目の SharePoint アプリケーション サーバーと、2 つのフロントエンド Web サーバーで、次の手順を実行します。

1.	デスクトップで **[SharePoint 2013 製品構成ウィザード]** をダブルクリックします。プログラムがコンピューターに変更を加えることを許可するよう求められたら、**[はい]** をクリックします。
2.	**[SharePoint 製品へようこそ]** ページで **[次へ]** をクリックします。
3.	サービス (IIS など) が再起動またはリセットされることを警告する **[SharePoint 製品構成ウィザード]** ダイアログが表示されます。**[はい]** をクリックします。
4.	**[サーバー ファームへの接続]** ページで、**[既存のサーバー ファームへの接続]** をクリックし、**[次へ]** をクリックします。
5.	**[構成データベースの設定]** ページで、**[データベース サーバー]** にプライマリ データベース サーバーの名前を入力し、**[データベース名の取得]** をクリックします。
6.	データベース名の一覧の **[SharePoint\_Config]** をクリックし、**[次へ]** をクリックします。
7.	**[ファームのセキュリティ設定の指定]** ページで、前の手順のパスフレーズを入力します。**[次へ]** をクリックします。
8.	**[SharePoint 製品構成ウィザードの終了]** ページが表示されます。**[次へ]** をクリックします。
9.	**[構成成功]** ページで **[完了]** をクリックします。

ファームの作成時に、プライマリ SQL Server 仮想マシンで一連のサーバー ログインが構成されます。SQL Server 2012 では、包含データベースのパスワードを持つユーザーの概念が導入されています。データベース自体にすべてのデータベース メタデータとユーザー情報が格納されており、このデータベースで定義されたユーザーは、対応するログインを持つ必要はありません。このデータベース内の情報は、可用性グループによってレプリケートされ、フェールオーバー後に使用できます。詳細については、「[包含データベース](http://go.microsoft.com/fwlink/p/?LinkId=262794)」をご覧ください。

ただし、既定では SharePoint データベースは包含データベースではありません。そのため、SharePoint ファーム アカウントの一連のログインがプライマリ データベース サーバーと同じものになるように、セカンダリ データベース サーバーを手動で構成する必要があります。この同期は、両方のサーバーに同時に接続することで、SQL Server Management Studio から実行できます。

この初期セットアップが完了すると、SharePoint ファームの機能について、さらに多くの構成オプションを使用できます。詳細については、「[Azure インフラストラクチャ サービスでの SharePoint 2013 の計画](http://msdn.microsoft.com/library/dn275958.aspx)」をご覧ください。

次の図は、このフェーズが問題なく完了したときの構成を示しています。

![](./media/virtual-machines-windows-ps-sp-intranet-ph4/workload-spsqlao_04.png)

## 次のステップ

- [フェーズ 5](virtual-machines-windows-ps-sp-intranet-ph5.md) を使用して、このワークロードを引き続き構成します。

<!---HONumber=AcomDC_0413_2016-->