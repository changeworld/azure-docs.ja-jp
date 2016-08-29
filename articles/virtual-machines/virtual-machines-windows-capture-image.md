<properties
	pageTitle="Azure VM からの VM イメージの作成 |Microsoft Azure"
	description="Resource Manager デプロイメント モデルで作成された既存の Azure VM から、一般化された VM イメージを作成する方法について説明します"
	services="virtual-machines-windows"
	documentationCenter=""
	authors="cynthn"
	manager="timlt"
	editor=""
	tags="azure-resource-manager"/>

<tags
	ms.service="virtual-machines-windows"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-windows"
	ms.devlang="na"
	ms.topic="article"
	ms.date="08/04/2016"
	ms.author="cynthn"/>

# 既存の Azure VM から VM イメージを作成する方法


この記事では、Azure PowerShell を使用して、既存の Azure VM の一般化されたイメージを作成する方法について説明します。その後、そのイメージを使用して、別の VM を作成できます。このイメージには、仮想マシンに接続された OS ディスクやデータ ディスクが含まれます。イメージには仮想ネットワーク リソースは含まれないため、イメージを使用して VM を作成するときは、こうしたリソースを設定する必要があります。このプロセスにより、[一般化された Windows イメージ](https://technet.microsoft.com/library/hh824938.aspx)が作成されます。


## 前提条件

- これらの手順では、イメージの作成に使用する Azure 仮想マシンが Resource Manager デプロイメント モデルで作成されているものとします。VM 名とリソース グループの名前が必要です。サブスクリプションのリソース グループの一覧を取得するには、PowerShell コマンドレット `Get-AzureRmResourceGroup` を入力する必要があります。また、サブスクリプションの VM の一覧を取得するには、`Get-AzureRMVM` を入力します。

- Azure PowerShell Version 1.0.x 以降がインストールされている必要があります。PowerShell がインストールされていない場合、インストール手順については、「[Azure PowerShell のインストールおよび構成方法](../powershell-install-configure.md)」を参照してください。

- コンピューター上で実行されるサーバー ロールが Sysprep でサポートされていることを確認します。詳しくは、「[Sysprep Support for Server Roles](https://msdn.microsoft.com/windows/hardware/commercialize/manufacture/desktop/sysprep-support-for-server-roles)」 (サーバー ロールに対する Sysprep サポート) をご覧ください。

## ソース VM の準備 

このセクションでは、Windows 仮想マシンをイメージとして使用できるように一般化する方法について説明します。

> [AZURE.WARNING] VM を一般化すると、すべてのユーザー アカウントが削除されます。したがって、一般化した VM には RDP 経由ではログインできなくなります。この変更を元に戻すことはできません。

1. Windows 仮想マシンにサインインします。[Azure ポータル](https://portal.azure.com)で、**[参照]**、**[仮想マシン]**、対象の Windows 仮想マシン、**[接続]** の順に選択します。

2. 管理者としてコマンド プロンプト ウィンドウを開きます。

3. ディレクトリを `%windir%\system32\sysprep` に変更し、sysprep.exe を実行します。

4. **[システム準備ツール]** ダイアログ ボックスで以下のようにします。

	- **[システム クリーンアップ アクション]** で **[システムの OOBE (Out-of-Box Experience) に入る]** を選択し、**[一般化する]** チェック ボックスがオンになっていることを確認します。Sysprep の使い方の詳細については、「[Sysprep の使用方法: 紹介](http://technet.microsoft.com/library/bb457073.aspx)」を参照してください。

	- **[シャットダウン オプション]** の **[シャットダウン]** を選択します。

	- **[OK]** をクリックします。

	![Sysprep を実行する](./media/virtual-machines-windows-capture-image/SysprepGeneral.png)

   仮想マシンがシャットダウンされます。Azure ポータルで状態が **[停止済み]** に変わります。


## Azure PowerShell へのログイン

1. Azure PowerShell を開き、Azure アカウントにサインインします。

		Login-AzureRmAccount

	Azure アカウント資格情報を入力するためのポップアップ ウィンドウが開きます。

2. 使用可能なサブスクリプションのサブスクリプション ID を取得します。

		Get-AzureRmSubscription

3. このサブスクリプション ID を使用して、適切なサブスクリプションを設定します。

		Select-AzureRmSubscription -SubscriptionId "<subscriptionID>"


## VM の割り当てを解除して、一般化状態に設定		

1. VM リソースの割り当てを解除します。

		Stop-AzureRmVM -ResourceGroupName <resourceGroup> -Name <vmName>

	Azure ポータルで VM の [状態] が **[停止済み]** から **[停止済み (割り当て解除)]** に変わります。

2. 仮想マシンの状態を **[一般化]** に設定します。

		Set-AzureRmVm -ResourceGroupName <resourceGroup> -Name <vmName> -Generalized

3. VM の状態を確認します。VM の **[OSState/generalized (OS の状態/一般化)]** セクションの **[DisplayStatus (ステータス表示)]** が **[VM が汎用化されました]** に設定されています。
		
		$vm = Get-AzureRmVM -ResourceGroupName <resourceGroup> -Name <vmName> -status
		$vm.Statuses

		
## イメージの作成 

1. 次のコマンドを使用して、仮想マシンのイメージをコピー先ストレージ コンテナーにコピーします。イメージは、元の仮想マシンと同じストレージ アカウントに作成されます。`-Path` 変数により、JSON テンプレートのコピーがローカルに保存されます。`-DestinationContainerName` 変数は、イメージを格納するコンテナーの名前です。コンテナーが存在しない場合、コンテナーは自動的に作成されます。

		Save-AzureRmVMImage -ResourceGroupName YourResourceGroup -Name YourWindowsVM -DestinationContainerName YourImagesContainer -VHDNamePrefix YourTemplatePrefix -Path Yourlocalfilepath\Filename.json

	イメージの URL は、JSON ファイル テンプレートから取得できます。イメージの完全なパスは、**resources** > **storageProfile** > **osDisk** > **image** > **uri** セクションにあります。イメージの URL は `https://<storageAccountName>.blob.core.windows.net/system/Microsoft.Compute/Images/<imagesContainer>/<templatePrefix-osDisk>.xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx.vhd` のようになります。
	
	ポータルで URI を確認することもできます。イメージは、ストレージ アカウントの **system** という名前の BLOB にコピーされます。

2. イメージへのパスの変数を作成します。

		$imageURI = "<https://<storageAccountName>.blob.core.windows.net/system/Microsoft.Compute/Images/<imagesContainer>/<templatePrefix-osDisk>.xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx.vhd>"


## 仮想ネットワークの作成

[仮想ネットワーク](../virtual-network/virtual-networks-overview.md)の vNet とサブネットを作成します。
		

1. 変数の値を実際の情報に置き換えます。CIDR 形式でサブネットのアドレス プレフィックスを指定します。変数とサブネットを作成します。

    	$rgName = "<resourceGroup>"
		$location = "<location>"
        $subnetName = "<subNetName>"
        $singleSubnet = New-AzureRmVirtualNetworkSubnetConfig -Name $subnetName -AddressPrefix <0.0.0.0/0>
        
2. **$vnetName** の値を、仮想ネットワークの名前に置き換えます。CIDR 形式で仮想ネットワークのアドレス プレフィックスを指定します。サブネットで変数と仮想ネットワークを作成します。

        $vnetName = "<vnetName>"
        $vnet = New-AzureRmVirtualNetwork -Name $vnetName -ResourceGroupName $rgName -Location $locName -AddressPrefix <0.0.0.0/0> -Subnet $singleSubnet
        
            
## パブリック IP アドレスとネットワーク インターフェイスの作成

仮想ネットワークでの仮想マシンとの通信を有効にするには、[パブリック IP アドレス](../virtual-network/virtual-network-ip-addresses-overview-arm.md)とネットワーク インターフェイスが必要です。

1. **$ipName** の値を、パブリック IP アドレスの名前に置き換えます。変数とパブリック IP アドレスを作成します。

        $ipName = "<ipName>"
        $pip = New-AzureRmPublicIpAddress -Name $ipName -ResourceGroupName $rgName -Location $locName -AllocationMethod Dynamic
        
2. **$nicName** の値を、ネットワーク インターフェイスの名前に置き換えます。変数とネットワーク インターフェイスを作成します。

        $nicName = "<nicName>"
        $nic = New-AzureRmNetworkInterface -Name $nicName -ResourceGroupName $rgName -Location $locName -SubnetId $vnet.Subnets[0].Id -PublicIpAddressId $pip.Id


## VM の作成

次の PowerShell スクリプトでは、仮想マシンの構成を設定し、アップロードした VM イメージを新しいインストールのソースとして使用する方法を示します。

>[AZURE.NOTE] VM は、元の VHD ファイルと同じストレージ アカウントにある必要があります。

</br>

	
	
	
	#Create variables
	# Enter a new user name and password to use as the local administrator account for the remotely accessing the VM
	$cred = Get-Credential
	
	# Name of the storage account 
	$storageAccName = "<storageAccountName>"
	
	# Name of the virtual machine
	$vmName = "<vmName>"
	
	# Size of the virtual machine. See the VM sizes documentation for more information: https://azure.microsoft.com/documentation/articles/virtual-machines-windows-sizes/
	$vmSize = "<vmSize>"
	
	# Computer name for the VM
	$computerName = "<computerName>"
	
	# Name of the disk that holds the OS
	$osDiskName = "<osDiskName>"
	
	# Assign a SKU name
	# Valid values for -SkuName are: **Standard_LRS** - locally redundant storage, **Standard_ZRS** - zone redundant storage, **Standard_GRS** - geo redundant storage, **Standard_RAGRS** - read access geo redundant storage, **Premium_LRS** - premium locally redundant storage. 
	$skuName = "<skuName>"
	
	# Create a new storage account for the VM
	New-AzureRmStorageAccount -ResourceGroupName $rgName -Name $storageAccName -Location $location -SkuName $skuName -Kind "Storage"

	#Get the storage account where the uploaded image is stored
	$storageAcc = Get-AzureRmStorageAccount -ResourceGroupName $rgName -AccountName $storageAccName

	#Set the VM name and size
	#Use "Get-Help New-AzureRmVMConfig" to know the available options for -VMsize
	$vmConfig = New-AzureRmVMConfig -VMName $vmName -VMSize $vmSize

	#Set the Windows operating system configuration and add the NIC
	$vm = Set-AzureRmVMOperatingSystem -VM $vmConfig -Windows -ComputerName $computerName -Credential $cred -ProvisionVMAgent -EnableAutoUpdate

	$vm = Add-AzureRmVMNetworkInterface -VM $vm -Id $nic.Id

	#Create the OS disk URI
	$osDiskUri = '{0}vhds/{1}-{2}.vhd' -f $storageAcc.PrimaryEndpoints.Blob.ToString(), $vmName.ToLower(), $osDiskName

	#Configure the OS disk to be created from the image (-CreateOption fromImage), and give the URL of the uploaded image VHD for the -SourceImageUri parameter
	#You set this variable when you uploaded the VHD
	$vm = Set-AzureRmVMOSDisk -VM $vm -Name $osDiskName -VhdUri $osDiskUri -CreateOption fromImage -SourceImageUri $imageURI -Windows

	#Create the new VM
	New-AzureRmVM -ResourceGroupName $rgName -Location $location -VM $vm



完了したときに、新しく作成された VM を、[Azure ポータル](https://portal.azure.com)の **[参照]** の **[仮想マシン]**、または次の PowerShell コマンドを使用して確認します。

	$vmList = Get-AzureRmVM -ResourceGroupName $rgName
	$vmList.Name


## 次のステップ

Azure PowerShell で新しい仮想マシンを管理する方法については、[Azure Resource Manager と PowerShell を使用した仮想マシンの管理](virtual-machines-windows-ps-manage.md)に関するページをご覧ください。

<!---HONumber=AcomDC_0817_2016-->