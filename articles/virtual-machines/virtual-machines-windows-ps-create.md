<properties
	pageTitle="PowerShell を使用して Azure VM を作成する | Microsoft Azure"
	description="Azure PowerShell と Azure Resource Manager を使用すると、Windows Server を実行する新しい VM を簡単に作成できます。"
	services="virtual-machines-windows"
	documentationCenter=""
	authors="davidmu1"
	manager="timlt"
	editor=""
	tags="azure-resource-manager"/>

<tags
	ms.service="virtual-machines-windows"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="get-started-article"
	ms.date="06/07/2016"
	ms.author="davidmu"/>

# Resource Manager と PowerShell を使用して Windows VM を作成する

この記事では、Windows Server を実行する Azure 仮想マシンとそれに必要なリソースを [Resource Manager](../resource-group-overview.md) と PowerShell を使用してすばやく作成する方法を紹介します。

仮想マシンを作成するには、この記事のすべての手順を実施する必要があります。所要時間は約 30 分です。

## 手順 1: Azure PowerShell をインストールする

最新バージョンの Azure PowerShell をインストールし、使用するサブスクリプションを選択し、Azure アカウントにサインインする方法については、「[Azure PowerShell のインストールと構成の方法](../powershell-install-configure.md)」を参照してください。
        
## 手順 2: リソース グループを作成する

まず、リソース グループを作成します。

1. リソースを作成できる場所の一覧を取得します。

	    Get-AzureRmLocation | sort Location | Select Location
        
    次のような結果が表示されます。
    
        Location
        --------
        australiaeast
        australiasoutheast
        brazilsouth
        canadacentral
        canadaeast
        centralindia
        centralus
        eastasia
        eastus
        eastus2
        japaneast
        japanwest
        northcentralus
        northeurope
        southcentralus
        southeastasia
        southindia
        westeurope
        westindia
        westus

2. **$locName** の値を一覧の場所に置き換えます。変数を作成します。

        $locName = "centralus"
        
3. **$rgName** の値を、新しいリソース グループの名前に置き換えます。変数とリソース グループを作成します。

        $rgName = "mygroup1"
        New-AzureRmResourceGroup -Name $rgName -Location $locName
    
## ステップ 3: ストレージ アカウントを作成する

作成する仮想マシン用の仮想ハード ディスクを格納するためには[ストレージ アカウント](../storage/storage-introduction.md)が必要です。

1. **$stName** の値は、ストレージ アカウントの名前に置き換えます。名前の一意性をテストします。

        $stName = "mystorage1"
        Get-AzureRmStorageAccountNameAvailability $stName

    このコマンドで **True** が返された場合は、提案した名前は Azure 内で一意です。ストレージ アカウント名の長さは 3 ～ 24 文字で、数字と小文字のみを使用できます。
    
2. 次のコマンドを実行して、ストレージ アカウントを作成します。
    
        $storageAcc = New-AzureRmStorageAccount -ResourceGroupName $rgName -Name $stName -SkuName "Standard_LRS" -Kind "Storage" -Location $locName
        
## 手順 4: 仮想ネットワークを作成する

すべての仮想マシンは[仮想ネットワーク](../virtual-network/virtual-networks-overview.md)に属します。

1. **$subnetName** の値を、サブネットの名前に置き換えます。変数とサブネットを作成します。
    	
        $subnetName = "mysubnet1"
        $singleSubnet = New-AzureRmVirtualNetworkSubnetConfig -Name $subnetName -AddressPrefix 10.0.0.0/24
        
2. **$vnetName** の値を、仮想ネットワークの名前に置き換えます。サブネットで変数と仮想ネットワークを作成します。

        $vnetName = "myvnet1"
        $vnet = New-AzureRmVirtualNetwork -Name $vnetName -ResourceGroupName $rgName -Location $locName -AddressPrefix 10.0.0.0/16 -Subnet $singleSubnet
        
    実際のアプリケーションと環境に合った値を使用してください。
        
## 手順 5: パブリック IP アドレスとネットワーク インターフェイスを作成する

仮想ネットワークでの仮想マシンとの通信を有効にするには、[パブリック IP アドレス](../virtual-network/virtual-network-ip-addresses-overview-arm.md)とネットワーク インターフェイスが必要です。

1. **$ipName** の値を、パブリック IP アドレスの名前に置き換えます。変数とパブリック IP アドレスを作成します。

        $ipName = "myIPaddress1"
        $pip = New-AzureRmPublicIpAddress -Name $ipName -ResourceGroupName $rgName -Location $locName -AllocationMethod Dynamic
        
2. **$nicName** の値を、ネットワーク インターフェイスの名前に置き換えます。変数とネットワーク インターフェイスを作成します。

        $nicName = "mynic1"
        $nic = New-AzureRmNetworkInterface -Name $nicName -ResourceGroupName $rgName -Location $locName -SubnetId $vnet.Subnets[0].Id -PublicIpAddressId $pip.Id
        
## 手順 6: 仮想マシンを作成する

すべての準備ができたので、仮想マシンを作成します。

1. 次のコマンドを実行し、仮想マシンの管理者アカウントの名前とパスワードを設定します。

        $cred = Get-Credential -Message "Type the name and password of the local administrator account."
        
    パスワードは、8 ～ 123 文字で指定する必要があります。また、1 つの小文字、1 つの大文字、1 つの数字、1 つの特殊文字という複雑さの 4 要件のうち、3 つを満たしている必要があります。詳しくは、[ユーザー名とパスワードの要件](virtual-machines-windows-faq.md#what-are-the-username-requirements-when-creating-a-vm)をご確認ください。
        
2. **$vmName** の値を、仮想マシンの名前に置き換えます。変数と仮想マシンの構成を作成します。

        $vmName = "myvm1"
        $vm = New-AzureRmVMConfig -VMName $vmName -VMSize "Standard_A1"
        
    仮想マシンに使用できるサイズの一覧は、「[Azure の仮想マシンのサイズ](virtual-machines-windows-sizes.md)」を参照してください。
    
3. **$compName** の値を、仮想マシンのコンピューター名に置き換えます。変数を作成し、オペレーティング システムの情報を構成に追加します。

        $compName = "myvm1"
        $vm = Set-AzureRmVMOperatingSystem -VM $vm -Windows -ComputerName $compName -Credential $cred -ProvisionVMAgent -EnableAutoUpdate
        
4. 仮想マシンのプロビジョニングに使用するイメージを定義します。

        $vm = Set-AzureRmVMSourceImage -VM $vm -PublisherName MicrosoftWindowsServer -Offer WindowsServer -Skus 2012-R2-Datacenter -Version "latest"
        
    使用するイメージの選択の詳細については、「[Powershell または CLI を使用した Azure での Windows 仮想マシン イメージへの移動と選択](virtual-machines-windows-cli-ps-findimage.md)」を参照してください。
        
5. 作成したネットワーク インターフェイスを構成に追加します。

        $vm = Add-AzureRmVMNetworkInterface -VM $vm -Id $nic.Id
        
6. **$blobPath** の値を、仮想ハード ディスクで使用するストレージ内のパスとファイル名に置き換えます。通常、仮想ハード ディスク ファイルはコンテナーに格納されます (例: **vhds/WindowsVMosDisk.vhd**)。変数を作成します。

        $blobPath = "vhds/WindowsVMosDisk.vhd"
        $osDiskUri = $storageAcc.PrimaryEndpoints.Blob.ToString() + $blobPath
        
7. **$diskName** の値を、オペレーティング システム ディスクの名前に置き換えます。変数を作成し、ディスクの情報を構成に追加します。

        $diskName = "windowsvmosdisk"
        $vm = Set-AzureRmVMOSDisk -VM $vm -Name $diskName -VhdUri $osDiskUri -CreateOption fromImage
        
8. 最後に、仮想マシンを作成します。

        New-AzureRmVM -ResourceGroupName $rgName -Location $locName -VM $vm

    Azure ポータルにリソース グループとそのすべてのリソースが表示され、PowerShell ウィンドウに成功ステータスが表示されます。

        RequestId  IsSuccessStatusCode  StatusCode  ReasonPhrase
        ---------  -------------------  ----------  ------------
                                  True          OK  OK
                                  
## 次のステップ

- デプロイに問題がある場合は、[Azure Portal でのリソース グループのデプロイのトラブルシューティング](../resource-manager-troubleshoot-deployments-portal.md)に関する記事をご覧ください。
- [Azure Resource Manager と PowerShell を使用した仮想マシンの管理](virtual-machines-windows-ps-manage.md)に関する記事で、作成した仮想マシンを管理する方法を確認します。
- テンプレートを使用して仮想マシンを作成する方法については、「[Resource Manager テンプレートで Windows 仮想マシンを作成する](virtual-machines-windows-ps-template.md)」を参照してください。

<!---HONumber=AcomDC_0831_2016-->