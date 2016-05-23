<properties
	pageTitle="Windows VM のコピーの作成 | Microsoft Azure"
	description="*特殊化イメージ* を作成して、Resource Manager デプロイ モデルで、Windows を実行する Azure 仮想マシンのコピーを作成する方法について説明します。"
	services="virtual-machines-windows"
	documentationCenter=""
	authors="dsk-2015"
	manager="timlt"
	editor=""
	tags="azure-resource-manager"/>

<tags
	ms.service="virtual-machines-windows"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-windows"
	ms.devlang="na"
	ms.topic="article"
	ms.date="04/26/2016"
	ms.author="dkshir"/>

# Resource Manager デプロイ モデルで Windows 仮想マシンのコピーを作成する方法


この記事では、Azure PowerShell および Azure ポータルを使用して、Windows を実行する Azure 仮想マシン (VM) のコピーを Resource Manager デプロイ モデルで作成する方法について説明します。元の VM のユーザー アカウントやその他の状態データが保持された Azure VM の**_特殊化_**イメージを作成する方法を示します。Windows VM を従来のデプロイ モデルから Resource Manager デプロイ モデルに移植したり、あるいは Resource Manager デプロイ モデルで作成された Windows VM のバックアップ コピーを作成したりする場合に、特殊化イメージが役立ちます。この方法で OS やデータ ディスクをコピーし、ネットワーク リソースをセットアップして、新しい仮想マシンを作成することができます。

同様の Windows VM の一括デプロイを行うには*一般化された*イメージが必要です。これについては [Windows 仮想マシンのキャプチャ方法に関する記事](virtual-machines-windows-capture-image.md)を参照してください。



## 始める前のチェック事項

この記事では、以下のことを前提としています。

1. **Windows を実行する Azure の仮想マシン**が、クラシック デプロイメント モデルまたは Resource Manager デプロイメント モデルで、オペレーティング システムが構成され、データ ディスクが接続され、必要なアプリケーションのインストールされている。この VM の作成でヘルプが必要な場合は、「[Resource Manager で Windows VM を作成する](virtual-machines-windows-ps-create.md)」を参照してください。 

1. **Azure PowerShell 1.0 以降**がコンピューターにインストールされ、Azure サブスクリプションにログインしている。詳細については、「[PowerShell のインストールおよび構成方法](../powershell-install-configure.md)」を参照してください。

1. **AzCopy ツール**がコンピューターにインストールされている。詳細については、「[AzCopy コマンドライン ツールを使用してデータを転送する](../storage/storage-use-azcopy.md)」を参照してください。

1. **リソース グループ**が**ストレージ アカウント**と **BLOB コンテナー**を使用して、VHD のコピー先で作成されている。既存のストレージ アカウントを使用する手順または新しいアカウントを作成する手順については、「[Azure Storage アカウントの作成または検索](virtual-machines-windows-upload-image.md#createstorage)」を参照してください。



> [AZURE.NOTE] 2 つのデプロイ モデルのいずれかをソース イメージとして使用して作成された VM には、同様の手順が適用されます。手順が若干異なる部分については、その都度説明します。


## Resource Manager ストレージ アカウントに VHD ファイルをコピーする


1. 最初に、次のいずれかのオプションにより、ソース VM が使用する VHD を解放します。

	- ソース仮想マシンを**_コピー_**する場合は、**停止**して**割り当てを解除**します。
	
		- クラシック デプロイメント モデルを使用して作成された VM の場合、[ポータル](https://portal.azure.com)を使用して **[参照]**、**[仮想マシン (クラシック)]**、*目的の VM*、**[停止]** の順にクリックするか、PowerShell コマンド `Stop-AzureVM -ServiceName <yourServiceName> -Name <yourVmName>` を使用できます。 
		
		- Resource Manager デプロイメント モデルの VM の場合、ポータルにログインして **[参照]**、**[仮想マシン]**、*目的の VM*、**[停止]** の順にクリックするか、PowerShell コマンド `Stop-AzureRmVM -ResourceGroupName <yourResourceGroup> -Name <yourVmName>` を使用できます。ポータルの VM の*状態*が **[実行中]** から **[停止済み (割り当て解除)]** に変わります。

	
	- ソース仮想マシンを**_移行_**する場合は、該当する VM を**削除**して後に残った VHD ファイルを使用します。[ポータル](https://portal.azure.com)の仮想マシンを**参照**して、**[削除]** をクリックします。
	
1. ソース VHD を含むストレージ アカウントと、VHD をコピーして新しい VM を作成するストレージ アカウントのアクセス キーを見つけます。VHD のコピー元のアカウントのキーを*ソース キー*と呼び、コピー先のアカウントのキーを*コピー先キー*と呼びます。アクセス キーの詳細については、「[Azure ストレージ アカウントについて](../storage/storage-create-storage-account.md)」を参照してください。

	- ソース VM がクラシック デプロイメント モデルを使用して作成された場合は、**[参照]**、**[ストレージ アカウント (クラシック)]**、 *使用しているストレージ アカウント*、**[すべての設定]**、**[キー]** の順にクリックし、**プライマリ アクセス キー**としてラベル付けされているキーをコピーします。 
	
	- Resource Manager デプロイメント モデルを使用して作成した VM の場合、または新しい VM に使用するストレージ アカウントの場合は、**[参照]** 、**[ストレージ アカウント]**、*使用しているストレージ アカウント*、**[すべての設定]**、**[アクセス キー]** の順にクリックして、**key1** としてラベル付けされているキーをコピーします。

1. コピー元およびコピー先のストレージ アカウントにアクセスする URL を取得します。ポータルで、ストレージ アカウントを**参照**して、**[BLOB]** をクリックします。ソース VHD (クラシック デプロイメント モデルの *vhds* など) をホストするコンテナーか、VHD のコピー先のコンテナーをクリックします。コンテナーの **[プロパティ]** をクリックし、**URL** というラベルのテキストをコピーします。コピー元およびコピー先の両方のコンテナーの URL が必要です。URL は `https://myaccount.blob.core.windows.net/mycontainer` のようになります。

1. ローカル コンピューターでコマンド ウィンドウを開き、AzCopy がインストールされているフォルダーに移動します (*C:\\Program Files (x86)\\Microsoft SDKs\\Azure\\AzCopy* など)。そこから次のコマンドを実行します。</br>

		AzCopy /Source:<URL_of_the_source_blob_container> /Dest:<URL_of_the_destination_blob_container> /SourceKey:<Access_key_for_the_source_storage> /DestKey:<Access_key_for_the_destination_storage> /Pattern:<File_name_of_the_VHD_you_are_copying>
</br>

>[AZURE.NOTE] 上記にあるように、OS とデータ ディスクは AzCopy を使用して個別にコピーする必要があります。


## コピーした VHD を使用して VM を作成する

次の手順は、Azure PowerShell を使用して Resource Manager ベースの Windows VM を新しい仮想ネットワークに作成する方法を示しています。その場合は先程の手順でコピーした VHD ファイルを使用します。VHD は、作成される新しい仮想マシンと同じストレージ アカウント内に存在する必要があります。


はじめに、次のスクリプトを参考にして新しい VM 用に仮想ネットワークと NIC を設定します。**$** 記号によって表される変数には、実際のアプリケーションに適した値を使用します。

	$pip = New-AzureRmPublicIpAddress -Name $pipName -ResourceGroupName $rgName -Location $location -AllocationMethod Dynamic

	$subnetconfig = New-AzureRmVirtualNetworkSubnetConfig -Name $subnet1Name -AddressPrefix $vnetSubnetAddressPrefix

	$vnet = New-AzureRmVirtualNetwork -Name $vnetName -ResourceGroupName $rgName -Location $location -AddressPrefix $vnetAddressPrefix -Subnet $subnetconfig

	$nic = New-AzureRmNetworkInterface -Name $nicname -ResourceGroupName $rgName -Location $location -SubnetId $vnet.Subnets[0].Id -PublicIpAddressId $pip.Id


次に、VM 構成を設定し、コピーした VHD を使用して、以下で説明しているように新しい仮想マシンを作成します。</br>

	#Set the VM name and size
	$vmConfig = New-AzureRmVMConfig -VMName $vmName -VMSize "Standard_A2"

	#Add the NIC
	$vm = Add-AzureRmVMNetworkInterface -VM $vmConfig -Id $nic.Id

	#Add the OS disk using the URL of the copied OS VHD
	$osDiskName = $vmName + "osDisk"
	$vm = Set-AzureRmVMOSDisk -VM $vm -Name $osDiskName -VhdUri $osDiskUri -CreateOption attach -Windows
	
	#Add data disks using the URLs of the copied data VHDs at the appropriate Logical Unit Number (Lun)
	$dataDiskName = $vmName + "dataDisk"
	$vm = Add-AzureRmVMDataDisk -VM $vm -Name $dataDiskName -VhdUri $dataDiskUri -Lun 0 -CreateOption attach
	
データおよび OS ディスクの URL は次のようになります: `https://StorageAccountName.blob.core.windows.net/BlobContainerName/DiskName.vhd`。ポータルでこれを見つけるには、対象のストレージ コンテナーを参照し、コピーした OS またはデータ VHD をクリックして、**URL** の内容をコピーします。
	
	#Create the new VM
	New-AzureRmVM -ResourceGroupName $rgName -Location $location -VM $vm
	
このコマンドが成功した場合は、次のような出力が表示されます。

	RequestId IsSuccessStatusCode StatusCode ReasonPhrase
	--------- ------------------- ---------- ------------
	                         True         OK OK


新しく作成された VM は、[Azure ポータル](https://portal.azure.com)の **[参照]** の **[仮想マシン]**、または次の PowerShell コマンドで確認できます。

	$vmList = Get-AzureRmVM -ResourceGroupName $rgName
	$vmList.Name

新しい仮想マシンにサインインするには、[ポータル](https://portal.azure.com)で VM を**参照**し、**[接続]** をクリックして、*リモート デスクトップ* RDP ファイルを開きます。元の仮想マシンのアカウント資格情報を使用して、新しい仮想マシンにログインします。


## 次のステップ

Azure PowerShell で新しい仮想マシンを管理する方法については、「[Azure Resource Manager と PowerShell を使用した仮想マシンの管理](virtual-machines-windows-ps-manage.md)」を参照してください。

<!---HONumber=AcomDC_0511_2016-->