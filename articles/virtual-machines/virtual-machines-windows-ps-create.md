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
	ms.date="04/12/2016"
	ms.author="davidmu"/>

# Resource Manager と PowerShell を使用して Windows VM を作成する

この記事では、Resource Manager と PowerShell を使用して、Windows Server を実行する Azure Virtual Machine およびそれに関連するリソースをすばやく作成する方法を示します。

この記事の手順を実行するには約 30 分かかります。

## 手順 1: Azure PowerShell をインストールする

最新バージョンの Azure PowerShell をインストールし、使用するサブスクリプションを選択して、Azure アカウントにサインインする方法については、「[Azure PowerShell のインストールと構成の方法](../powershell-install-configure.md)」を参照してください。
        
## 手順 2: リソース グループを作成する

すべてのリソースをリソース グループにデプロイする必要があります。詳細については、「[Azure リソース マネージャーの概要](../resource-group-overview.md)」を参照してください。

1. リソースの作成に使用できる場所の一覧を取得します。

	    Get-AzureLocation | sort Name | Select Name

2. **$locName** の値を一覧の場所に置き換えます (例: **Central US**)。変数を作成します。

        $locName = "location name"
        
3. **$rgName** の値を、新しいリソース グループの名前に置き換えます。変数とリソース グループを作成します。

        $rgName = "resource group name"
        New-AzureRmResourceGroup -Name $rgName -Location $locName
    
## ステップ 3: ストレージ アカウントを作成する

ストレージ アカウントは、作成する仮想マシンに関連付けられた仮想ハード ディスクに格納する必要があります。

1. **$stName** の値 (小文字と数字のみ) を、ストレージ アカウントの名前に置き換えます。名前の一意性をテストします。

        $stName = "storage account name"
        Test-AzureName -Storage $stName

    このコマンドで **False** が返された場合は、提案した名前は一意です。
    
2. 次のコマンドを実行して、ストレージ アカウントを作成します。
    
        $storageAcc = New-AzureRmStorageAccount -ResourceGroupName $rgName -Name $stName -Type "Standard_LRS" -Location $locName
        
## 手順 4: 仮想ネットワークを作成する

すべての仮想マシンは、仮想ネットワークと関連付けられている必要があります。

1. **$subnetName** の値を、サブネットの名前に置き換えます。変数とサブネットを作成します。
    	
        $subnetName = "subnet name"
        $singleSubnet = New-AzureRmVirtualNetworkSubnetConfig -Name $subnetName -AddressPrefix 10.0.0.0/24
        
2. **$vnetName** の値を、仮想ネットワークの名前に置き換えます。サブネットで変数と仮想ネットワークを作成します。

        $vnetName = "virtual network name"
        $vnet = New-AzureRmVirtualNetwork -Name $vnetName -ResourceGroupName $rgName -Location $locName -AddressPrefix 10.0.0.0/16 -Subnet $singleSubnet
        
## 手順 5: パブリック IP アドレスとネットワーク インターフェイスを作成する

仮想ネットワークでの仮想マシンとの通信を有効にするには、パブリック IP アドレスとネットワーク インターフェイスが必要です。

1. **$ipName** の値を、パブリック IP アドレスの名前に置き換えます。変数とパブリック IP アドレスを作成します。

        $ipName = "public IP address name"
        $pip = New-AzureRmPublicIpAddress -Name $ipName -ResourceGroupName $rgName -Location $locName -AllocationMethod Dynamic
        
2. **$nicName** の値を、ネットワーク インターフェイスの名前に置き換えます。変数とネットワーク インターフェイスを作成します。

        $nicName = "network interface name"
        $nic = New-AzureRmNetworkInterface -Name $nicName -ResourceGroupName $rgName -Location $locName -SubnetId $vnet.Subnets[0].Id -PublicIpAddressId $pip.Id
        
## 手順 6: 仮想マシンを作成する

すべての準備ができたので、仮想マシンを作成します。

1. 次のコマンドを実行し、仮想マシンの管理者アカウントの名前とパスワードを設定します。

        $cred = Get-Credential -Message "Type the name and password of the local administrator account."
        
2. **$vmName** の値を、仮想マシンの名前に置き換えます。変数と仮想マシンの構成を作成します。

        $vmName = "virtual machine name"
        $vm = New-AzureRmVMConfig -VMName $vmName -VMSize "Standard_A1"
        
    仮想マシンに使用できるサイズの一覧は、「[Azure の仮想マシンのサイズ](virtual-machines-windows-sizes.md)」を参照してください。
    
3. **$compName** の値を、仮想マシンのコンピューター名に置き換えます。変数を作成し、オペレーティング システムの情報を構成に追加します。

        $compName = "computer name"
        $vm = Set-AzureRmVMOperatingSystem -VM $vm -Windows -ComputerName $compName -Credential $cred -ProvisionVMAgent -EnableAutoUpdate
        
4. 仮想マシンのプロビジョニングに使用するイメージを定義します。

        $vm = Set-AzureRmVMSourceImage -VM $vm -PublisherName MicrosoftWindowsServer -Offer WindowsServer -Skus 2012-R2-Datacenter -Version "latest"
        
    使用するイメージの選択の詳細については、「[Powershell または CLI を使用した Azure での Windows 仮想マシン イメージへの移動と選択](virtual-machines-windows-cli-ps-findimage.md)」を参照してください。
        
5. 作成したネットワーク インターフェイスを構成に追加します。

        $vm = Add-AzureRmVMNetworkInterface -VM $vm -Id $nic.Id
        
6. **$blobPath** の値を、仮想ハード ディスクのストレージ内のパスとファイル名に置き換えます。通常、vhd ファイルはコンテナーに格納されます (例: "vhds/WindowsVMosDisk.vhd")。変数を作成します。

        $blobPath = "vhd path and file name"
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

- デプロイに問題がある場合は、「[Azure ポータルでのリソース グループのデプロイのトラブルシューティング](../resource-manager-troubleshoot-deployments-portal.md)」を参照してください。
- 作成した仮想マシンの管理方法については、「[Azure リソース マネージャーと PowerShell を使用した仮想マシンの管理](virtual-machines-windows-ps-manage.md)」を参照してください。
- テンプレートを使用して仮想マシンを作成する方法については、「[リソース マネージャー テンプレートで Windows 仮想マシンを作成する](virtual-machines-windows-ps-template.md)」を参照してください。

<!---HONumber=AcomDC_0420_2016-->