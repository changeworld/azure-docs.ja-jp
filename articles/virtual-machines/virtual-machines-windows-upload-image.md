<properties
	pageTitle="リソース マネージャー向けに Windows VM をアップロードする | Microsoft Azure"
	description="Resource Manager デプロイメント モデルで使用する Windows の仮想マシン イメージをアップロードする方法を説明します。"
	services="virtual-machines-windows"
	documentationCenter=""
	authors="cynthn"
	manager="timlt"
	editor="tysonn"
	tags="azure-resource-manager"/>

<tags
	ms.service="virtual-machines-windows"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-windows"
	ms.devlang="na"
	ms.topic="article"
	ms.date="08/02/2016"
	ms.author="cynthn"/>

# Resource Manager デプロイメント向けに Windows VM イメージを Azure にアップロードする


この記事では、VM をすばやく作成できるように、Windows の仮想ハード ディスク (VHD) イメージを作成してアップロードする方法を示します。Azure でのディスクと VHD の詳細については、「[仮想マシン用のディスクと VHD について](virtual-machines-linux-about-disks-vhds.md)」を参照してください。


## 前提条件

この記事では、以下のことを前提としています。

- **Azure サブスクリプション** - まだお持ちでない場合は、[無料で Azure アカウントを開く](/pricing/free-trial/?WT.mc_id=A261C142F)か、[MSDN サブスクライバ―の特典を有効にします](/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F)。

- **Azure PowerShell バージョン 1.4 以降** - インストールされていない場合は、「[Azure PowerShell のインストールおよび構成方法](../powershell-install-configure.md)」を参照してください。

- **Windows を実行する仮想マシン** - オンプレミスの仮想マシンを作成するための多くのツールがあります。たとえば、「[Hyper-V の役割のインストールと仮想マシンの構成](http://technet.microsoft.com/library/hh846766.aspx)」を参照してください。Azure でサポートされている Windows オペレーティング システムの詳細については、「[Microsoft Azure 仮想マシンのマイクロソフト サーバー ソフトウェアのサポート](https://support.microsoft.com/kb/2721672)」を参照してください。


## VM が適切なファイル形式になっていることを確認する

Azure では、VHD ファイル形式の[第 1 世代の仮想マシン](http://blogs.technet.com/b/ausoemteam/archive/2015/04/21/deciding-when-to-use-generation-1-or-generation-2-virtual-machines-with-hyper-v.aspx)のみを使用できます。VHD は、固定サイズで、メガバイトの整数 (つまり、8 で割り切ることのできる数) にする必要があります。VHD のサイズの上限は、1,023 GB です。

- VHDX 形式の Windows VM イメージがある場合は、次のいずれかを使用して VHD に変換します。

	- Hyper-V: Hyper-V を開いて、左側でローカル コンピューターを選択します。次に、その上にあるメニューで、**[アクション]**、**[ディスクの編集...]** の順にクリックします。**[次へ]** をクリックし、次のオプションを順番に入力または選択していきます (*VHDX ファイルのパス* > **[変換]** > **[VHD]** > **[固定サイズ]** > *新しい VHD ファイルのパス*)。**[完了]** をクリックして閉じます。

	- [Convert-VHD PowerShell コマンドレット](http://technet.microsoft.com/library/hh848454.aspx): 詳細については、ブログの投稿「[Hyper-V .vhdx を .vhd ファイル形式に変換する](https://blogs.technet.microsoft.com/cbernier/2013/08/29/converting-hyper-v-vhdx-to-vhd-file-formats-for-use-in-windows-azure/)」を参照してください。

- [VMDK ファイル形式](https://en.wikipedia.org/wiki/VMDK)の Windows VM イメージがある場合は、[Microsoft Virtual Machine Converter](https://www.microsoft.com/download/details.aspx?id=42497) を使用して VHD に変換できます。詳細については、ブログ記事「[VMware VMDK から Hyper-V VHD への変換方法](http://blogs.msdn.com/b/timomta/archive/2015/06/11/how-to-convert-a-vmware-vmdk-to-hyper-v-vhd.aspx)」を参照してください。


## VHD のアップロードを準備する

このセクションでは、Windows 仮想マシンを一般化する方法を説明します。特に重要なこととして、Sysprep は、すべての個人アカウント情報を削除します。Sysprep の詳細については、「[Sysprep の使用方法: 紹介](http://technet.microsoft.com/library/bb457073.aspx)」を参照してください。

1. Windows 仮想マシンへのサインイン

2. 管理者としてコマンド プロンプト ウィンドウを開きます。ディレクトリを **%windir%\\system32\\sysprep** に変更し、`sysprep.exe` を実行します。

3. **[システム準備ツール]** ダイアログ ボックスで、次の操作を行います。

	1. **[システム クリーンアップ アクション]** で **[システムの OOBE (Out-of-Box Experience) に入る]** を選択し、**[一般化する]** チェック ボックスがオンになっていることを確認します。

	2. **[シャットダウン オプション]** の **[シャットダウン]** を選択します。

	3. **[OK]** をクリックします。

	![Sysprep の開始](./media/virtual-machines-windows-upload-image/sysprepgeneral.png)

</br>


## Azure へのログイン

1. Azure PowerShell を開き、Azure アカウントにサインインします。

		Login-AzureRmAccount

	Azure アカウント資格情報を入力するためのポップアップ ウィンドウが開きます。

2. 使用可能なサブスクリプションのサブスクリプション ID を取得します。

		Get-AzureRmSubscription

3. このサブスクリプション ID を使用して、適切なサブスクリプションを設定します。

		Select-AzureRmSubscription -SubscriptionId "<subscriptionID>"

	
## ストレージ アカウントを取得する

アップロードした VM イメージを格納するには、Azure にストレージ アカウントが必要です。既存のストレージ アカウントを選択することも、新しいストレージ アカウントを作成することもできます。

使用できるストレージ アカウントを表示します。

		Get-AzureRmStorageAccount

既存のストレージ アカウントを使用する場合は、「[VM イメージのアップロード](#upload-the-vm-image-to-your-storage-account)」セクションに進みます。

ストレージ アカウントを作成する場合は、次の手順に従います。

1. このストレージ アカウントのリソース グループがあることを確認します。サブスクリプションのすべてのリソース グループを検索するには、次のコマンドを使用します。

		Get-AzureRmResourceGroup

2. リソース グループを作成するには、次のコマンドを使用します。

		New-AzureRmResourceGroup -Name <resourceGroupName> -Location "West US"

3. [New-AzureStorageAccount](https://msdn.microsoft.com/library/mt607148.aspx) コマンドレットを使用して、このリソース グループのストレージ アカウントを作成します。

		New-AzureRmStorageAccount -ResourceGroupName <resourceGroupName> -Name <storageAccountName> -Location "<location>" -SkuName "<skuName>" -Kind "Storage"
			
-SkuName の値が次のとおりであることを確認します。

- **Standard\_LRS** - ローカル冗長ストレージ。
- **Standard\_ZRS** - ゾーン冗長ストレージ。
- **Standard\_GRS** - geo 冗長ストレージ。
- **Standard\_RAGRS** - 読み取りアクセス geo 冗長ストレージ。
- **Premium\_LRS** - Premium ローカル冗長ストレージ。



## ストレージ アカウントに VM イメージをアップロードする

[Add-AzureRmVhd](https://msdn.microsoft.com/library/mt603554.aspx) コマンドレットを使用して、ストレージ アカウント内のコンテナーにイメージをアップロードします。

		$rgName = "<resourceGroupName>"
		$urlOfUploadedImageVhd = "<storageAccount>/<blobContainer>/<targetVHDName>.vhd"
		Add-AzureRmVhd -ResourceGroupName $rgName -Destination $urlOfUploadedImageVhd -LocalFilePath <localPathOfVHDFile>

各値の説明:

- **storageAccount** は、イメージのストレージ アカウントの名前です。

- **blobContainer** は、イメージを格納する BLOB コンテナーです。この名前の既存の BLOB コンテナーが見つからない場合は、作成されます。

- **targetVHDName** は、アップロードした VHD ファイルに使用する名前です。

- **localPathOfVHDFile** は、ローカル コンピューター上の .vhd ファイルの完全なパスと名前です。


成功した場合、次のような応答を取得します。

		C:\> Add-AzureRmVhd -ResourceGroupName testUpldRG -Destination https://testupldstore2.blob.core.windows.net/testblobs/WinServer12.vhd -LocalFilePath "C:\temp\WinServer12.vhd"
		MD5 hash is being calculated for the file C:\temp\WinServer12.vhd.
		MD5 hash calculation is completed.
		Elapsed time for the operation: 00:03:35
		Creating new page blob of size 53687091712...
		Elapsed time for upload: 01:12:49

		LocalFilePath           DestinationUri
		-------------           --------------
		C:\temp\WinServer12.vhd https://testupldstore2.blob.core.windows.net/testblobs/WinServer12.vhd

このコマンドは、ネットワーク接続や VHD ファイルのサイズによっては、完了に時間がかかることがあります。




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

>[AZURE.NOTE] VM は、アップロードした VHD ファイルと同じストレージ アカウント内にある必要があります。

</br>

	
	
	#Create variables
	# Enter a new user name and password to use as the local administrator account for the remotely accessing the VM
	$cred = Get-Credential
	
	# Name of the storage account where the VHD file is and where the OS disk will be created
	$storageAccName = "<storageAccountName>"
	
	# Name of the virtual machine
	$vmName = "<vmName>"
	
	# Size of the virtual machine. See the VM sizes documentation for more information: https://azure.microsoft.com/documentation/articles/virtual-machines-windows-sizes/
	$vmSize = "<vmSize>"
	
	# Computer name for the VM
	$computerName = "<computerName>"
	
	# Name of the disk that holds the OS
	$osDiskName = "<osDiskName>"

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
	$vm = Set-AzureRmVMOSDisk -VM $vm -Name $osDiskName -VhdUri $osDiskUri -CreateOption fromImage -SourceImageUri $urlOfUploadedImageVhd -Windows

	#Create the new VM
	New-AzureRmVM -ResourceGroupName $rgName -Location $location -VM $vm



完了したときに、新しく作成された VM を、[Azure ポータル](https://portal.azure.com)の **[参照]** の **[仮想マシン]**、または次の PowerShell コマンドを使用して確認します。

	$vmList = Get-AzureRmVM -ResourceGroupName $rgName
	$vmList.Name


## 次のステップ

Azure PowerShell を使用して新しい仮想マシンを管理する方法については、「[Azure Resource Manager と PowerShell を使用した仮想マシンの管理](virtual-machines-windows-ps-manage.md)」を参照してください。

<!---HONumber=AcomDC_0803_2016-->