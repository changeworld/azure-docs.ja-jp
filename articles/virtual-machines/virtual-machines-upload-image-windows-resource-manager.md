<properties
	pageTitle="リソース マネージャー向けに Windows VM をアップロードする | Microsoft Azure"
	description="リソース マネージャー デプロイ モデルで使用する Windows ベースの仮想マシン イメージをアップロードする方法を説明します。"
	services="virtual-machines"
	documentationCenter=""
	authors="dsk-2015"
	manager="timlt"
	editor="tysonn"
	tags="azure-resource-manager"/>

<tags
	ms.service="virtual-machines"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-windows"
	ms.devlang="na"
	ms.topic="article"
	ms.date="02/05/2016"
	ms.author="dkshir"/>

# リソース マネージャー デプロイ向けに Windows VM イメージを Microsoft Azure にアップロードする

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)] [classic deployment model](virtual-machines-create-upload-vhd-windows-server.md).


この記事では、Windows ベース オペレーティング システムの仮想ハード ディスク (VHD) をアップロードし、それを基にリソース マネージャー デプロイ モデルを使用して Windows Virtual Machines を作成する方法について説明します。Microsoft Azure でのディスクと VHD の詳細については、「[Virtual Machines 用のディスクと VHD について](virtual-machines-disks-vhds.md)」を参照してください。



## 前提条件

この記事では、以下のことを前提としています。

1. **Azure サブスクリプション** - サブスクリプションがない場合は、[Azure アカウントを無料で作成](/pricing/free-trial/?WT.mc_id=A261C142F)できます。Azure の有料サービスを試用できるクレジットが提供されます。このクレジットを使い切ってもアカウントは維持されるため、Websites など無料の Azure サービスをご利用になれます。明示的に設定を変更しない限り、クレジット カードに課金されることはありません。[MSDN サブスクライバーの特典を有効にする](/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F)こともできます。MSDN サブスクリプションにより、有料の Azure サービスを利用できるクレジットが毎月付与されます。

2. **Microsoft Azure PowerShell 1.0.x** - Microsoft Azure PowerShell Version 1.0.x をインストールしておきます。インストールされていない場合は、「[Azure PowerShell のインストールおよび構成方法](../powershell-install-configure.md)」を読んでインストールしてください。古いバージョンの PowerShell にはリソース マネージャーの新しい機能が追加されていないので、バージョン 1.0 以降を使用することをお勧めします。バージョンの違いについて詳しくは、「[Azure PowerShell 1.0](https://azure.microsoft.com/blog/azps-1-0/)」を参照してください。

3. **Windows オペレーティング システムを実行する仮想マシン** - オンプレミスの仮想マシンを作成するための多くのツールがあります。たとえば、Hyper-V マネージャーを使用して仮想マシンを作成し、オペレーティング システムをインストールすることができます。詳細については、「[Hyper-V の役割のインストールと仮想マシンの構成](http://technet.microsoft.com/library/hh846766.aspx)」を参照してください。サポートされているオペレーティング システムの詳細については、「[Microsoft Azure 仮想マシンのマイクロソフト サーバー ソフトウェアのサポート](https://support.microsoft.com/kb/2721672)」を参照してください。


## VM が適切なファイル形式になっていることを確認します。

Microsoft Azure では、VHD ファイル形式で保存された[第 1 世代の仮想マシン](http://blogs.technet.com/b/ausoemteam/archive/2015/04/21/deciding-when-to-use-generation-1-or-generation-2-virtual-machines-with-hyper-v.aspx)のイメージのみが受け入れられます。VHD のサイズは固定サイズで、MB 単位の整数である必要があります。VHD のサイズの上限は、1,023 GB です。

- HYPER-V マネージャーは通常、VM イメージを VHDX 形式で保存しますが、この形式は Microsoft Azure ではサポートされていません。Hyper-V または [Convert-VHD PowerShell コマンドレット](http://technet.microsoft.com/library/hh848454.aspx)を使用して、VHDX 形式を VHD 形式に変換できます。PowerShell を使用する手順については、「[Converting Hyper-V .vhdx to .vhd file formats (Hyper-V の .vhdx ファイル形式から.vhdx ファイル形式に変換する)](https://blogs.technet.microsoft.com/cbernier/2013/08/29/converting-hyper-v-vhdx-to-vhd-file-formats-for-use-in-windows-azure/)」をご覧ください。または、Hyper-V で、左側にあるローカル コンピューターを選択し、その上のメニューで **[操作]**、**[ディスクの編集]** の順にクリックします。**[次へ]** をクリックし、次のオプションを順番に入力または選択していきます (VHDX ファイルのパス > **[変換]** > **[VHD]** > **[固定サイズ]** > 新しい VHD ファイルのパス)。**[完了]** をクリックして閉じます。

- [VMDK ファイル形式](https://en.wikipedia.org/wiki/VMDK)の Windows VM イメージがある場合は、[Microsoft Virtual Machine Converter](https://www.microsoft.com/download/details.aspx?id=42497) を使用して VHD ファイル形式に変換できます。詳細については、ブログ記事「[How to Convert a VMWare VMDK to Hyper-V VHD (VMWare VMDK から Hyper-V VHD への変換方法)](http://blogs.msdn.com/b/timomta/archive/2015/06/11/how-to-convert-a-vmware-vmdk-to-hyper-v-vhd.aspx)」をご覧ください。


## VHD のアップロードを準備する

このセクションでは、Windows 仮想マシンを一般化する方法を説明します。特に重要なのは、すべての個人アカウント情報を削除することです。通常、この VM イメージを使用して似た仮想マシンを迅速にデプロイする場合は、これを行う必要があります。Sysprep の詳細については、「[Sysprep の使用方法: 紹介](http://technet.microsoft.com/library/bb457073.aspx)」を参照してください。

1. Windows ベースの仮想マシンにログインします。

2. 管理者としてコマンド プロンプト ウィンドウを開きます。ディレクトリを **%windir%\\system32\\sysprep** に変更し、`sysprep.exe` を実行します。

3. **[システム準備ツール]** ダイアログ ボックスで以下のようにします。

	1. **[システム クリーンアップ アクション]** で **[システムの OOBE (Out-of-Box Experience) に入る]** を選択し、**[一般化する]** チェック ボックスがオンになっていることを確認します。

	2. **[シャットダウン オプション]** の **[シャットダウン]** を選択します。

	3. **[OK]** をクリックします。

	![Sysprep の開始](./media/virtual-machines-upload-image-windows-resource-manager/sysprepgeneral.png)

</br>
## Azure Storage アカウントの作成または検索

VM イメージをアップロードするには、Azure にストレージ アカウントが必要です。既存のストレージ アカウントを選択することも、新しいストレージ アカウントを作成することもできます。これには、ポータルまたは PowerShell を使用します。

### ポータルの使用

1. [Azure ポータル](https://portal.azure.com)にサインインします。

2. **[参照]**、**[ストレージ アカウント]** の順にクリックします。

3. このイメージのアップロードに使用するストレージ アカウントがあるかどうかを確認します。ストレージ アカウントの名前をメモしておきます。既存のストレージ アカウントを使用する場合は、「[VM イメージのアップロード](#uploadvm)」セクションに進みます。

4. 新しいストレージ アカウントを作成する場合は、**[追加]** をクリックして次の情報を入力します。

	1. ストレージ アカウントの**名前**を入力します。3 ～ 24 文字で小文字と数字のみを含めることができます。この名前は、ストレージ アカウントの BLOB、ファイル、その他のリソースにアクセスするときに使用する URL の一部になります。

	2. 作成するストレージ アカウントの**種類**を選択します。詳細については、「[Azure ストレージ アカウントについて](../storage/storage-create-storage-account.md)」をご覧ください。

	3. **リソース グループ**の名前を入力します。指定した名前のリソース グループが見つからなかった場合は、ポータルによって新しいリソース グループが作成されます。

	4. ストレージ アカウントの**場所**を選択します。

	5. **[作成]** をクリックします。作成したアカウントが **[ストレージ アカウント]** パネルに表示されます。

		![ストレージ アカウントの詳細の入力](./media/virtual-machines-upload-image-windows-resource-manager/portal_create_storage_account.png)

	6. このステップと次のステップでは、このストレージ アカウントに BLOB コンテナーを作成する方法を説明します。このステップは省略可能です。なぜなら、PowerShell コマンドでイメージをアップロードするときに、イメージ用に新しい BLOB コンテナーを作成することもできるからです。BLOB コンテナーを自分で作成しない場合は、「[VM イメージのアップロード](#uploadvm)」セクションに進んでください。作成する場合は、**[サービス]** タイルの **[BLOB]** をクリックします。

		![BLOB サービス](./media/virtual-machines-upload-image-windows-resource-manager/portal_create_blob.png)

	7. BLOB のパネルが表示されたら、**[+ コンテナー]** をクリックして、新しい BLOB ストレージ コンテナーを作成します。コンテナーの名前と、アクセスの種類を入力します。

		![新しい BLOB の作成](./media/virtual-machines-upload-image-windows-resource-manager/portal_create_container.png)

  		> [AZURE.NOTE] 既定では、コンテナーはプライベートであり、アカウント所有者のみがアクセスできます。コンテナー内の BLOB にはパブリック読み取りアクセスを許可し、コンテナーのプロパティやメタデータにはアクセスを許可しない場合は、**[BLOB]** オプションを使用します。コンテナーと BLOB に完全パブリック読み取りアクセスを許可するには、**[コンテナー]** オプションを使用します。

	8. **[BLOB サービス]** パネルに、新しい BLOB コンテナーが表示されます。このコンテナーの URL をメモしておいてください。PowerShell コマンドでイメージをアップロードする際に必要になります。URL の長さと画面の解像度によっては、URL の一部が隠れて読めない場合があります。そのような場合は、右上隅の *[最大化]* アイコンをクリックします。


### PowerShell の使用

1. Azure PowerShell 1.0.x を開き、Azure アカウントにログインします。

		Login-AzureRmAccount

	ポップアップ ウィンドウが開くので、Azure の資格情報を入力します。

2. 既定で選択されているサブスクリプション ID が作業するものと異なる場合は、次のいずれかを使用して適切なサブスクリプションを設定します。

		Set-AzureRmContext -SubscriptionId "xxxx-xxxx-xxxx-xxxx"

	または

		Select-AzureRmSubscription -SubscriptionId "xxxx-xxxx-xxxx-xxxx"

	Azure アカウントのサブスクリプションを検索するには、`Get-AzureRmSubscription` コマンドを使用します。

3. このサブスクリプションで使用可能なストレージ アカウントを検索します。

		Get-AzureRmStorageAccount

	既存のストレージ アカウントを使用する場合は、「[VM イメージのアップロード](#uploadvm)」セクションに進みます。

4. このイメージを保持する新しいストレージ アカウントを作成する場合は、次の手順に従います。

	1. このストレージ アカウントのリソース グループがあることを確認します。サブスクリプションのすべてのリソース グループを検索するには、次のコマンドを使用します。

			Get-AzureRmResourceGroup

	2. 新しいリソース グループを作成する場合は、次のコマンドを使用します。

			New-AzureRmResourceGroup -Name YourResourceGroup -Location "West US"

	3. このリソース グループに新しいストレージ アカウントを作成するには、次のコマンドを使用します。

			New-AzureRmStorageAccount -ResourceGroupName YourResourceGroup -Name YourStorageAccountName -Location "West US" -Type "Standard_GRS"


</br> <a id="uploadvm"></a>
## ストレージ アカウントに VM イメージをアップロードする

Azure PowerShell で次の手順を行って、ストレージ アカウントに VM イメージをアップロードします。イメージはこのアカウントの Blob Storage コンテナーにアップロードされます。既存のコンテナーを使用するか、新しいコンテナーを作成することができます。

1. 前のセクションで説明したように、`Login-AzureRmAccount` を使用して Azure PowerShell 1.0.x にログインし、`Set-AzureRmContext -SubscriptionId "xxxx-xxxx-xxxx-xxxx"` を使用して正しいサブスクリプションを使用していることを確認します。

2. 次のように [Add-AzureRmVhd](https://msdn.microsoft.com/library/mt603554.aspx) を使用して、ストレージ アカウントに汎用 Azure VHD を追加します。

		Add-AzureRmVhd -ResourceGroupName YourResourceGroup -Destination "<StorageAccountURL>/<BlobContainer>/<TargetVHDName>.vhd" -LocalFilePath <LocalPathOfVHDFile>

	ここで、- **StorageAccountURL** はストレージ アカウントの URL です。通常、`https://YourStorageAccountName.blob.core.windows.net` のような形式になります。**YourStorageAccountName** は、使用するストレージ アカウントの名前に置き換えてください。- **BlobContainer** は、イメージを格納する BLOB コンテナーです。指定された名前の BLOB コンテナーが見つからなかった場合、コマンドレットによって新しいコンテナーが作成されます。- **TargetVHDName** は、保存するイメージの名前です。- **LocalPathOfVHDFile** は、ローカル コンピューター上の .vhd ファイルの完全なパスと名前です。

	`Add-AzureRmVhd` が正常に実行された場合、次のようになります。

		C:\> Add-AzureRmVhd -ResourceGroupName testUpldRG -Destination https://testupldstore2.blob.core.windows.net/testblobs/WinServer12.vhd -LocalFilePath "C:\temp\WinServer12.vhd"
		MD5 hash is being calculated for the file  C:\temp\WinServer12.vhd.
		MD5 hash calculation is completed.
		Elapsed time for the operation: 00:03:35
		Creating new page blob of size 53687091712...
		Elapsed time for upload: 01:12:49

		LocalFilePath           DestinationUri
		-------------           --------------
		C:\temp\WinServer12.vhd https://testupldstore2.blob.core.windows.net/testblobs/WinServer12.vhd

	このコマンドは、ネットワーク接続や VHD ファイルのサイズによっては、完了に時間がかかります。

</br>
## アップロードしたイメージから新しい VM をデプロイする

アップロードしたイメージを使用して、新しい Windows VM を作成できます。次の手順では、Azure PowerShell と上の手順でアップロードした VM イメージを使用して、新しい Virtual Network に VM を作成する方法を示します。

>[AZURE.NOTE] VM イメージは、作成される実際の仮想マシンと同じストレージ アカウント内に存在する必要があります。

### ネットワーク リソースを作成する

次のサンプル PowerShell スクリプトを使用して、新しい VM 用に仮想ネットワークと NIC を設定します。**$** 記号によって表される変数には、実際のアプリケーションに適した値を使用します。

	$pip = New-AzureRmPublicIpAddress -Name $pipName -ResourceGroupName $rgName -Location $location -AllocationMethod Dynamic

	$subnetconfig = New-AzureRmVirtualNetworkSubnetConfig -Name $subnet1Name -AddressPrefix $vnetSubnetAddressPrefix

	$vnet = New-AzureRmVirtualNetwork -Name $vnetName -ResourceGroupName $rgName -Location $location -AddressPrefix $vnetAddressPrefix -Subnet $subnetconfig

	$nic = New-AzureRmNetworkInterface -Name $nicname -ResourceGroupName $rgName -Location $location -SubnetId $vnet.Subnets[0].Id -PublicIpAddressId $pip.Id

### 新しい仮想マシンを作成する

次の PowerShell スクリプトでは、仮想マシンの構成を設定し、アップロードした VM イメージを新しいインストールのソースとして使用する方法を示します。 </br>

	#Enter a new username and password in the pop-up for the following
	$cred = Get-Credential

	#Get the storage account where the uploaded image is stored
	$storageAcc = Get-AzureRmStorageAccount -ResourceGroupName $rgName -AccountName $storageAccName

	#Set the VM name and size
	#Use "Get-Help New-AzureRmVMConfig" to know the available options for -VMsize
	$vmConfig = New-AzureRmVMConfig -VMName $vmName -VMSize "Standard_A4"

	#Set the Windows operating system configuration and add the NIC
	$vm = Set-AzureRmVMOperatingSystem -VM $vmConfig -Windows -ComputerName $computerName -Credential $cred -ProvisionVMAgent -EnableAutoUpdate

	$vm = Add-AzureRmVMNetworkInterface -VM $vm -Id $nic.Id

	#Create the OS disk URI
	$osDiskUri = '{0}vhds/{1}{2}.vhd' -f $storageAcc.PrimaryEndpoints.Blob.ToString(), $vmName.ToLower(), $osDiskName

	#Configure the OS disk to be created from image (-CreateOption fromImage) and give the URL of the uploaded image VHD for the -SourceImageUri parameter
	#You can find this URL in the result of the Add-AzureRmVhd cmdlet above
	$vm = Set-AzureRmVMOSDisk -VM $vm -Name $osDiskName -VhdUri $osDiskUri -CreateOption fromImage -SourceImageUri $urlOfUploadedImageVhd -Windows

	#Create the new VM
	New-AzureRmVM -ResourceGroupName $rgName -Location $location -VM $vm

たとえば、ワークフローは次のようになります。

		C:\> $pipName = "testpip6"
		C:\> $pip = New-AzureRmPublicIpAddress -Name $pipName -ResourceGroupName $rgName -Location $location -AllocationMethod Dynamic
		C:\> $subnet1Name = "testsub6"
		C:\> $nicname = "testnic6"
		C:\> $vnetName = "testvnet6"
		C:\> $subnetconfig = New-AzureRmVirtualNetworkSubnetConfig -Name $subnet1Name -AddressPrefix $vnetSubnetAddressPrefix
		C:\> $vnet = New-AzureRmVirtualNetwork -Name $vnetName -ResourceGroupName $rgName -Location $location -AddressPrefix $vnetAddressPrefix -Subnet $subnetconfig
		C:\> $nic = New-AzureRmNetworkInterface -Name $nicname -ResourceGroupName $rgName -Location $location -SubnetId $vnet.Subnets[0].Id -PublicIpAddressId $pip.Id
		C:\> $vmName = "testupldvm6"
		C:\> $vmConfig = New-AzureRmVMConfig -VMName $vmName -VMSize "Standard_A4"
		C:\> $computerName = "testupldcomp6"
		C:\> $vm = Set-AzureRmVMOperatingSystem -VM $vmConfig -Windows -ComputerName $computerName -Credential $cred -ProvisionVMAgent -EnableAutoUpdate
		C:\> $vm = Add-AzureRmVMNetworkInterface -VM $vm -Id $nic.Id
		C:\> $osDiskName = "testupos6"
		C:\> $osDiskUri = '{0}vhds/{1}{2}.vhd' -f $storageAcc.PrimaryEndpoints.Blob.ToString(), $vmName.ToLower(), $osDiskName
		C:\> $urlOfUploadedImageVhd = "https://testupldstore2.blob.core.windows.net/testblobs/WinServer12.vhd"
		C:\> $vm = Set-AzureRmVMOSDisk -VM $vm -Name $osDiskName -VhdUri $osDiskUri -CreateOption fromImage -SourceImageUri $urlOfUploadedImageVhd -Windows
		C:\> $result = New-AzureRmVM -ResourceGroupName $rgName -Location $location -VM $vm
		C:\> $result
		RequestId IsSuccessStatusCode StatusCode ReasonPhrase
		--------- ------------------- ---------- ------------
		                         True         OK OK

新しく作成された VM は、[Azure ポータル](https://portal.azure.com)の **[参照]** の **[仮想マシン]**、または次の PowerShell コマンドで確認できます。

	$vmList = Get-AzureRmVM -ResourceGroupName $rgName
	$vmList.Name


## 次のステップ

Azure PowerShell で新しい仮想マシンを管理する方法については、「[Azure リソース マネージャーと PowerShell を使用した仮想マシンの管理](virtual-machines-deploy-rmtemplates-powershell.md)」を参照してください。

<!---HONumber=AcomDC_0211_2016-->