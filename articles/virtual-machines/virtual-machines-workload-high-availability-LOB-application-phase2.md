<properties 
	pageTitle="基幹業務アプリケーションのワークロード フェーズ 2: ドメイン コントローラーを構成する" 
	description="この 2 番目のフェーズでは、2 つの Active Directory ドメイン コントローラーを作成して構成します。" 
	documentationCenter=""
	services="virtual-machines" 
	authors="JoeDavies-MSFT" 
	manager="timlt" 
	editor=""
	tags="azure-resource-manager"/>

<tags 
	ms.service="virtual-machines" 
	ms.workload="infrastructure-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="08/11/2015" 
	ms.author="josephd"/>

# 基幹業務アプリケーションのワークロード フェーズ 2: ドメイン コントローラーを構成する

高可用な基幹業務アプリケーションを Azure インフラストラクチャ サービスにデプロイする作業のこのフェーズでは、オンプレミス ネットワークへの接続で認証トラフィックを送信するのではなく、Web リソースに対するクライアントの Web 要求を Azure 仮想ネットワーク内でローカルに認証できるように、Azure 仮想ネットワークで 2 つのレプリカ ドメイン コントローラーを構成します。

[フェーズ 3](virtual-machines-workload-high-availability-LOB-application-phase3.md) に進むには、このフェーズを完了する必要があります。全フェーズについては、「[Azure での高可用な基幹業務アプリケーションのデプロイ](virtual-machines-workload-high-availability-LOB-application-overview.md)」をご覧ください。

## Azure でのドメイン コントローラー仮想マシンの作成

まず、表 M の **[仮想マシン名]** 列に記入する必要があります。また、必要に応じて、**[最小サイズ]** 列で仮想マシンのサイズを変更します。

項目 | 仮想マシン名 | ギャラリー イメージ | 最小サイズ 
--- | --- | --- | --- 
1\. | \_\_\_\_\_\_\_\_\_\_\_\_\_\_ (最初のドメイン コントローラー (例: DC1)) | Windows Server 2012 R2 Datacenter | Standard\_D1
2\. | \_\_\_\_\_\_\_\_\_\_\_\_\_\_ (2 番目のドメイン コントローラー (例: DC2)) | Windows Server 2012 R2 Datacenter | Standard\_D1
3\. | \_\_\_\_\_\_\_\_\_\_\_\_\_\_ (プライマリ データベース サーバー (例: SQL1)) | Microsoft SQL Server 2014 Enterprise - Windows Server 2012 R2 | 	Standard\_DS4
4\. | \_\_\_\_\_\_\_\_\_\_\_\_\_\_ (セカンダリ データベース サーバー (例: SQL2)) | Microsoft SQL Server 2014 Enterprise - Windows Server 2012 R2 | 	Standard\_DS4
5\. | \_\_\_\_\_\_\_\_\_\_\_\_\_\_ (クラスターのマジョリティ ノード監視 (例: MN1)) | Windows Server 2012 R2 Datacenter | Standard\_D1
6\. | \_\_\_\_\_\_\_\_\_\_\_\_\_\_ (最初の Web サーバー (例: WEB1)) | Windows Server 2012 R2 Datacenter | Standard\_D3
7\. | \_\_\_\_\_\_\_\_\_\_\_\_\_\_ (2 番目の Web サーバー (例: WEB2)) | Windows Server 2012 R2 Datacenter | Standard\_D3

**表 M - Azure での高可用な基幹業務アプリケーションのデプロイ用の仮想マシン**

仮想マシンのサイズの一覧については、「[Azure の仮想マシンおよびクラウド サービスのサイズ](https://msdn.microsoft.com/library/azure/dn197896.aspx)」をご覧ください。

Azure PowerShell コマンドの次のブロックを使用して、2 つのドメイン コントローラーの仮想マシンを作成します。< and > の文字を削除して、各変数の値を指定します。この PowerShell コマンド セットでは、次の値を使用します。

- 表 M (仮想マシン)
- 表 V (仮想ネットワーク設定)
- 表 S (サブネット)
- 表 ST (ストレージ アカウント)
- 表 A (可用性セット)

V、S、ST、Aの各表は、「[フェーズ 1: Azure を構成する](virtual-machines-workload-high-availability-LOB-application-phase1.md)」で定義したものです。

適切な値をすべて指定したら、Azure PowerShell プロンプトでそのブロックを実行します。

	# Set up subscription and key variables
	$subscr="<name of the Azure subscription>"
	Set-AzureSubscription -SubscriptionName $subscr
	Switch-AzureMode AzureResourceManager
	$rgName="<resource group name>"
	$locName="<Azure location of your resource group>"
	$saName="<Table ST – Item 2 – Storage account name column>"
	$vnetName="<Table V – Item 1 – Value column>"
	$avName="<Table A – Item 1 – Availability set name column>"
	
	# Create the first domain controller
	$vmName="<Table M – Item 1 - Virtual machine name column>"
	$vmSize="<Table M – Item 1 - Minimum size column>"
	$staticIP="<Table V – Item 6 - Value column>"
	$vnet=Get-AzurevirtualNetwork -Name $vnetName -ResourceGroupName $rgName
	$nic=New-AzureNetworkInterface -Name ($vmName +"-NIC") -ResourceGroupName $rgName -Location $locName -SubnetId $vnet.Subnets[1].Id -PrivateIpAddress $staticIP
	$avSet=Get-AzureAvailabilitySet –Name $avName –ResourceGroupName $rgName 
	$vm=New-AzureVMConfig -VMName $vmName -VMSize $vmSize -AvailabilitySetId $avset.Id
	
	$diskSize=<size of the extra disk for AD DS data in GB>
	$storageAcc=Get-AzureStorageAccount -ResourceGroupName $rgName -Name $saName
	$vhdURI=$storageAcc.PrimaryEndpoints.Blob.ToString() + "vhds/" + $vmName + "-ADDSDisk.vhd"
	Add-AzureVMDataDisk -VM $vm -Name "ADDSData" -DiskSizeInGB $diskSize -VhdUri $vhdURI  -CreateOption empty
	
	$cred=Get-Credential -Message "Type the name and password of the local administrator account for the first domain controller." 
	$vm=Set-AzureVMOperatingSystem -VM $vm -Windows -ComputerName $vmName -Credential $cred -ProvisionVMAgent -EnableAutoUpdate
	$vm=Set-AzureVMSourceImage -VM $vm -PublisherName MicrosoftWindowsServer -Offer WindowsServer -Skus 2012-R2-Datacenter -Version "latest"
	$vm=Add-AzureVMNetworkInterface -VM $vm -Id $nic.Id
	$storageAcc=Get-AzureStorageAccount -ResourceGroupName $rgName -Name $saName
	$osDiskUri=$storageAcc.PrimaryEndpoints.Blob.ToString() + "vhds/" + $vmName + "-OSDisk.vhd"
	$vm=Set-AzureVMOSDisk -VM $vm -Name "OSDisk" -VhdUri $osDiskUri -CreateOption fromImage
	New-AzureVM -ResourceGroupName $rgName -Location $locName -VM $vm
	
	# Create the second domain controller
	$vmName="<Table M – Item 2 - Virtual machine name column>"
	$vmSize="<Table M – Item 2 - Minimum size column>"
	$staticIP="<Table V – Item 7 - Value column>"
	$vnet=Get-AzurevirtualNetwork -Name $vnetName -ResourceGroupName $rgName
	$nic=New-AzureNetworkInterface -Name ($vmName +"-NIC") -ResourceGroupName $rgName -Location $locName -SubnetId $vnet.Subnets[1].Id -PrivateIpAddress $staticIP
	$avSet=Get-AzureAvailabilitySet –Name $avName –ResourceGroupName $rgName 
	$vm=New-AzureVMConfig -VMName $vmName -VMSize $vmSize -AvailabilitySetId $avset.Id
	
	$diskSize=<size of the extra disk for AD DS data in GB>
	$storageAcc=Get-AzureStorageAccount -ResourceGroupName $rgName -Name $saName
	$vhdURI=$storageAcc.PrimaryEndpoints.Blob.ToString() + "vhds/" + $vmName + "-ADDSDisk.vhd"
	Add-AzureVMDataDisk -VM $vm -Name "ADDSData" -DiskSizeInGB $diskSize -VhdUri $vhdURI  -CreateOption empty
	
	$cred=Get-Credential -Message "Type the name and password of the local administrator account for the second domain controller." 
	$vm=Set-AzureVMOperatingSystem -VM $vm -Windows -ComputerName $vmName -Credential $cred -ProvisionVMAgent -EnableAutoUpdate
	$vm=Set-AzureVMSourceImage -VM $vm -PublisherName MicrosoftWindowsServer -Offer WindowsServer -Skus 2012-R2-Datacenter -Version "latest"
	$vm=Add-AzureVMNetworkInterface -VM $vm -Id $nic.Id
	$storageAcc=Get-AzureStorageAccount -ResourceGroupName $rgName -Name $saName
	$osDiskUri=$storageAcc.PrimaryEndpoints.Blob.ToString() + "vhds/" + $vmName + "-OSDisk.vhd"
	$vm=Set-AzureVMOSDisk -VM $vm -Name "OSDisk" -VhdUri $osDiskUri -CreateOption fromImage
	New-AzureVM -ResourceGroupName $rgName -Location $locName -VM $vm

## 最初のドメイン コントローラーの構成

好みのリモート デスクトップ クライアントを使用し、最初のドメイン コントローラー仮想マシンへのリモート デスクトップ接続を作成します。仮想マシンのイントラネット DNS 名またはコンピューター名と、ローカル管理者アカウントの資格情報を使用します。

次に、最初のドメイン コントローラーにデータ ディスクを追加する必要があります。

### <a id="datadisk"></a>空のディスクを初期化するには

1.	サーバー マネージャーの左側のウィンドウで、**[ファイル サービスと記憶域サービス]** をクリックし、**[ディスク]** をクリックします。
2.	コンテンツ ウィンドウで、**[ディスク]** グループの (**[パーティション]** が **[不明]** に設定されている) **[ディスク 2]** をクリックします。
3.	**[タスク]** をクリックし、**[ボリューム]** をクリックします。
4.	新しいボリューム ウィザードの [開始する前に] ページで **[次へ]** をクリックします。
5.	[サーバーとディスクの選択] ページで、**[ディスク 2]** をクリックし、**[次へ]** をクリックします。メッセージが表示されたら、[OK] をクリックします。
6.	[ボリュームのサイズの指定] ページで、**[次へ]** をクリックします。
7.	[ドライブ文字またはフォルダーへの割り当て] ページで、**[次へ]** をクリックします。
8.	[ファイル システム形式の選択] ページで、**[次へ]** をクリックします。
9.	[選択内容の確認] ページで、**[作成]** をクリックします。
10.	完了したら、**[閉じる]** をクリックします。

次に、最初のドメイン コントローラーから組織のネットワーク上の場所への接続をテストします。

### <a id="testconn"></a>接続をテストするには

1.	デスクトップで Windows PowerShell プロンプトを開きます。
2.	組織のネットワーク上のリソースの名前と IP アドレスに対して **ping** コマンドを実行します。

この手順により、DNS 名前解決が正しく機能していること (オンプレミス DNS サーバーで仮想マシンが正しく構成されていること) と、クロスプレミス仮想ネットワーク間でパケットを送受信できることが確認されます。この基本テストに失敗した場合は、IT 部門に連絡し、DNS 名前解決とパケット配信の問題のトラブルシューティングを行ってください。

次に、最初のドメイン コントローラーの Windows PowerShell コマンド プロンプトで、次のコマンドを実行します。

	$domname="<DNS domain name of the domain for which this computer will be a domain controller>"
	Install-WindowsFeature AD-Domain-Services -IncludeManagementTools
	Install-ADDSDomainController -InstallDns –DomainName $domname  -DatabasePath "F:\NTDS" -SysvolPath "F:\SYSVOL" -LogPath "F:\Logs"

ドメイン管理者アカウントの資格情報の入力が求められます。コンピューターが再起動します。

## 2 番目のドメイン コントローラーの構成

好みのリモート デスクトップ クライアントを使用し、2 番目のドメイン コントローラー仮想マシンへのリモート デスクトップ接続を作成します。仮想マシンのイントラネット DNS 名またはコンピューター名と、ローカル管理者アカウントの資格情報を使用します。

次に、2 番目のドメイン コントローラーにデータ ディスクを追加する必要があります。「[空のディスクを初期化するには](#datadisk)」の手順をご覧ください。

次に、2 番目のドメイン コントローラーの Windows PowerShell プロンプトで、以下のコマンドを実行します。

	$domname="<DNS domain name of the domain for which this computer will be a domain controller>"
	Install-WindowsFeature AD-Domain-Services -IncludeManagementTools
	Install-ADDSDomainController -InstallDns –DomainName $domname  -DatabasePath "F:\NTDS" -SysvolPath "F:\SYSVOL" -LogPath "F:\Logs"

ドメイン管理者アカウントの資格情報の入力が求められます。コンピューターが再起動します。

次に、DNS サーバーとして使用する 2 つの新しいドメイン コントローラーの IP アドレスが仮想マシンに割り当てられるように、仮想ネットワークの DNS サーバーを更新する必要があります。この手順では、表 V (仮想ネットワーク設定) および表 M (仮想マシン) の値を使用します。

1.	[Azure プレビュー ポータル](https://portal.azure.com/)の左ウィンドウで、**[すべて参照] > [仮想ネットワーク]** をクリックし、仮想ネットワークの名前 (表 V - 項目 1 - "値" 列) をクリックします。
2.	仮想ネットワークのウィンドウで、**[すべての設定]** をクリックします。
3.	**[設定]** ウィンドウで、**[DNS サーバー]** をクリックします。
4.	**[DNS サーバー]** ウィンドウで、次を入力します。
	- **プライマリ DNS サーバー**: 表 V - 項目 6 - "値" 列
	- **セカンダリ DNS サーバー**: 表 V - 項目 7 - "値" 列
5.	Azure プレビュー ポータルの左ウィンドウで、**[すべて参照] > [仮想マシン]** をクリックします。
6.	**[仮想マシン]** ウィンドウで、最初のドメイン コントローラーの名前 (表 M – 項目 1 - "仮想マシン名" 列) をクリックします。
7.	仮想マシンのウィンドウで、**[再起動]** をクリックします。
8.	最初のドメイン コントローラーが起動したら、**[仮想マシン]** ウィンドウで、2 番目のドメイン コントローラーの名前 (表 M – 項目 2 - "仮想マシン名" 列) をクリックします。
9.	仮想マシンのウィンドウで、**[再起動]** をクリックします。2 番目のドメイン コントローラーが起動するまで待ちます。

2 つのドメイン コントローラーを再起動するのは、オンプレミス DNS サーバーで DNS サーバーとして構成されないようにするためです。これらのドメイン コントローラーは、それ自体が DNS サーバーであるため、ドメイン コントローラーに昇格されたときに、オンプレミス DNS サーバーで DNS フォワーダーとして自動的に構成されています。

次に、Azure 仮想ネットワークのサーバーでローカル ドメイン コントローラーが使用されるように、Active Directory レプリケーション サイトを作成する必要があります。ドメイン管理者アカウントを使用してプライマリ ドメイン コントローラーにログオンし、管理者レベルの Windows PowerShell プロンプトで次のコマンドを実行します。

	$vnet="<Table V – Item 1 – Value column>"
	$vnetSpace="<Table V – Item 5 – Value column>"
	New-ADReplicationSite -Name $vnet 
	New-ADReplicationSubnet –Name $vnetSpace –Site $vnet

次に、SQL Server 仮想マシンを管理するユーザー アカウントを追加します。

	New-ADUser -SamAccountName sqlservice -AccountPassword (read-host "Set user password" -assecurestring) -name "sqlservice" -enabled $true -PasswordNeverExpires $true -ChangePasswordAtLogon $false

次の図は、このフェーズが問題なく完了したときの構成を示しています。プレースホルダーにコンピューター名が示されています。

![](./media/virtual-machines-workload-high-availability-LOB-application-phase2/workload-lobapp-phase2.png)

## 次のステップ

このワークロードを引き続き構成するには、「[フェーズ 3: SQL Server インフラストラクチャを構成する](virtual-machines-workload-high-availability-LOB-application-phase3.md)」に進んでください。

## その他のリソース

[Azure での高可用な基幹業務アプリケーションのデプロイ](virtual-machines-workload-high-availability-LOB-application-overview.md)

[基幹業務アプリケーションのアーキテクチャ ブループリント](http://msdn.microsoft.com/dn630664)

[テスト用のハイブリッド クラウドでの Web ベース LOB アプリケーションの設定](../virtual-network/virtual-networks-setup-lobapp-hybrid-cloud-testing.md)

[Azure インフラストラクチャ サービス実装ガイドライン](virtual-machines-infrastructure-services-implementation-guidelines.md)

[Azure インフラストラクチャ サービスのワークロード: SharePoint Server 2013 ファーム](virtual-machines-workload-intranet-sharepoint-farm.md)

<!---HONumber=August15_HO8-->