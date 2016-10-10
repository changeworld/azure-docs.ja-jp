<properties
	pageTitle="Windows VM のコピーの作成 | Microsoft Azure"
	description="*特殊化イメージ* を作成して、Resource Manager デプロイ モデルで、Windows を実行する Azure 仮想マシンのコピーを作成する方法について説明します。"
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
	ms.date="09/27/2016"
	ms.author="cynthn"/>

# Azure Resource Manager デプロイメント モデルでの Windows 仮想マシンのコピーの作成


この記事では、Windows を実行している Azure 仮想マシン (VM) のコピーを作成する方法について説明します。具体的には、Azure Resource Manager デプロイメント モデルで、Azure PowerShell と Azure ポータルを使用してこの操作を行う方法を説明します。また、元の VM のユーザー アカウントやその他の状態データが保持された Azure VM の*特殊化*イメージを作成する方法を示します。特殊化イメージが役立つのは、Windows 仮想マシンを従来のデプロイメント モデルから Resource Manager デプロイメント モデルに移植したり、Resource Manager デプロイメント モデルで作成された Windows VM のバックアップ コピーを作成したりする場合です。この方法でオペレーティング システムやデータ ディスクをコピーし、ネットワーク リソースをセットアップして、新しい仮想マシンを作成することができます。

同様の Windows VM の一括デプロイを作成する必要がある場合は、*一般化された*イメージを使用します。これについては、[Windows 仮想マシンのキャプチャ方法](virtual-machines-windows-capture-image.md)に関するページをご覧ください。



## 開始する前に

手順を開始する前に、次の前提条件が満たされていることを確認します。

- **Windows を実行している Azure 仮想マシンがある**。この仮想マシンは、クラシック デプロイメント モデルまたは Resource Manager デプロイメント モデルのいずれかを使用して作成されている必要があります。また、オペレーティング システムの構成、データ ディスクの接続、その他のカスタマイズ (必要なアプリケーションのインストールなど) が完了している必要があります。この VM を使用して、コピーを作成します。ソース VM の作成については、[Resource Manager を使用した Windows VM の作成方法](virtual-machines-windows-ps-create.md)に関するページをご覧ください。

- 使用しているコンピューターに **Azure PowerShell 1.0 以降**がインストールされていること。また、Azure サブスクリプションにサインインしている必要があります。詳細については、「[Azure PowerShell のインストールおよび構成方法](../powershell-install-configure.md)」を参照してください。

- **AzCopy ツール**がダウンロードおよびインストールされていること。このツールの詳細については、「[AzCopy コマンド ライン ユーティリティを使用してデータを転送する](../storage/storage-use-azcopy.md)」を参照してください。

- **リソース グループ**、**ストレージ アカウント**、および **BLOB コンテナー**が、VHD のコピー先となるリソース グループで作成されていること。既存のストレージ アカウントを使用する手順または新しいアカウントを作成する手順については、[Azure Storage アカウントの作成または検索](virtual-machines-windows-upload-image.md#createstorage)に関するページをご覧ください。

> [AZURE.NOTE] 2 つのデプロイメント モデルのいずれかをソース イメージとして使用して作成された VM には、同様の手順が適用されます。この記事では、必要に応じてそのわずかな違いについて説明します。


## Resource Manager ストレージ アカウントに VHD ファイルをコピーする


1. 次のいずれかの操作を行って、ソース VM が使用する VHD を解放します。

	- ソース仮想マシンをコピーする場合は、仮想マシンを停止し、割り当てを解除します。

		- クラシック デプロイメント モデルを使用して作成された VM の場合、[ポータル](https://portal.azure.com)を使用して **[参照]**、**[仮想マシン (クラシック)]**、*目的の VM*、**[停止]** の順にクリックするか、PowerShell コマンド `Stop-AzureVM -ServiceName <yourServiceName> -Name <yourVmName>` を使用できます。

		- Resource Manager デプロイメント モデルを使用して作成された VM の場合、ポータルにサインインして **[参照]**、**[仮想マシン]**、*目的の VM*、**[停止]** の順にクリックするか、PowerShell コマンド `Stop-AzureRmVM -ResourceGroupName <yourResourceGroup> -Name <yourVmName>` を使用できます。ポータルの VM の状態が **[実行中]** から **[停止済み (割り当て解除)]** に変わります。

	- ソース仮想マシンを移行する場合は、その VM を削除し、後に残った VHD を使用します。[ポータル](https://portal.azure.com)で仮想マシンを参照し、**[削除]** をクリックします。

1. ソース VHD を含むストレージ アカウントのアクセス キーと、VHD をコピーして新しい VM を作成するストレージ アカウントのアクセス キーを見つけます。VHD のコピー元アカウントのキーを*ソース キー*と呼び、コピー先アカウントのキーを*宛先キー*と呼びます。アクセス キーの詳細については、「[Azure ストレージ アカウントについて](../storage/storage-create-storage-account.md)」を参照してください。

	- ソース VM がクラシック デプロイメント モデルを使用して作成された場合は、**[参照]**、**[ストレージ アカウント (クラシック)]**、*使用しているストレージ アカウント*、**[すべての設定]**、**[キー]** の順にクリックします。**プライマリ アクセス キー**としてラベル付けされているキーをコピーします。

	- ソース VM が Resource Manager デプロイメント モデルを使用して作成された場合、または新しい VM に使用するストレージ アカウントの場合は、**[参照]**、**[ストレージ アカウント]**、*使用しているストレージ アカウント*、**[すべての設定]**、**[アクセス キー]** の順にクリックします。**key1** としてラベル付されているキーをコピーします。

1. コピー元およびコピー先のストレージ アカウントにアクセスする URL を取得します。ポータルで、ストレージ アカウントを参照して、**[BLOB]** をクリックします。ソース VHD (クラシック デプロイメント モデルの *vhds* など) をホストするコンテナーか、VHD のコピー先のコンテナーをクリックします。コンテナーの **[プロパティ]** をクリックし、**URL** というラベルのテキストをコピーします。コピー元およびコピー先の両方のコンテナーの URL が必要です。URL は `https://myaccount.blob.core.windows.net/mycontainer` のようになります。

1. ローカル コンピューターでコマンド ウィンドウを開き、AzCopy がインストールされているフォルダーに移動します (*C:\\Program Files (x86)\\Microsoft SDKs\\Azure\\AzCopy* など)。 そこから次のコマンドを実行します。</br>

		AzCopy /Source:<URL_of_the_source_blob_container> /Dest:<URL_of_the_destination_blob_container> /SourceKey:<Access_key_for_the_source_storage> /DestKey:<Access_key_for_the_destination_storage> /Pattern:<File_name_of_the_VHD_you_are_copying>
</br>

>[AZURE.NOTE] 前の手順で説明したように、オペレーティング システムとデータ ディスクは AzCopy を使用して別個にコピーする必要があります。


## コピーした VHD を使用して VM を作成する

前の手順でコピーした VHD を使用して、Azure PowerShell を使って、Resource Manager ベースの Windows VM を新しい仮想ネットワークに作成できます。VHD は、作成される新しい仮想マシンと同じストレージ アカウント内に存在する必要があります。


次のスクリプトを参考にして、新しい VM 用に仮想ネットワークと NIC を設定します。**$** 記号によって表される変数には、実際のアプリケーションに適した値を使用します。

	$pip = New-AzureRmPublicIpAddress -Name $pipName -ResourceGroupName $rgName -Location $location -AllocationMethod Dynamic

	$subnetconfig = New-AzureRmVirtualNetworkSubnetConfig -Name $subnet1Name -AddressPrefix $vnetSubnetAddressPrefix

	$vnet = New-AzureRmVirtualNetwork -Name $vnetName -ResourceGroupName $rgName -Location $location -AddressPrefix $vnetAddressPrefix -Subnet $subnetconfig

	$nic = New-AzureRmNetworkInterface -Name $nicname -ResourceGroupName $rgName -Location $location -SubnetId $vnet.Subnets[0].Id -PublicIpAddressId $pip.Id


次に、VM 構成を設定し、コピーした VHD を使用して、新しい仮想マシンを作成します。</br>

	#Set the VM name and size
	$vmConfig = New-AzureRmVMConfig -VMName $vmName -VMSize "Standard_A2"

	#Add the NIC
	$vm = Add-AzureRmVMNetworkInterface -VM $vmConfig -Id $nic.Id

	#Add the OS disk by using the URL of the copied OS VHD
	$osDiskName = $vmName + "osDisk"
	$vm = Set-AzureRmVMOSDisk -VM $vm -Name $osDiskName -VhdUri $osDiskUri -CreateOption attach -Windows

	#Add data disks by using the URLs of the copied data VHDs at the appropriate Logical Unit Number (Lun)
	$dataDiskName = $vmName + "dataDisk"
	$vm = Add-AzureRmVMDataDisk -VM $vm -Name $dataDiskName -VhdUri $dataDiskUri -Lun 0 -CreateOption attach

データとオペレーティング システム ディスクの URL は、`https://StorageAccountName.blob.core.windows.net/BlobContainerName/DiskName.vhd` のようになります。ポータルでこれを見つけるには、ターゲット ストレージ コンテナーを参照し、コピーしたオペレーティング システムまたはデータ VHD をクリックして、URL の内容をコピーします。

	#Create the new VM
	New-AzureRmVM -ResourceGroupName $rgName -Location $location -VM $vm

このコマンドが成功した場合は、次のような出力が表示されます。

	RequestId IsSuccessStatusCode StatusCode ReasonPhrase
	--------- ------------------- ---------- ------------
	                         True         OK OK


新しく作成された VM は、[Azure ポータル](https://portal.azure.com)の **[参照]** の **[仮想マシン]**、または次の PowerShell コマンドで確認できます。

	$vmList = Get-AzureRmVM -ResourceGroupName $rgName
	$vmList.Name

新しい仮想マシンにサインインするには、[ポータル](https://portal.azure.com)で VM を参照し、**[接続]** をクリックして、リモート デスクトップ RDP ファイルを開きます。元の仮想マシンのアカウント資格情報を使用して、新しい仮想マシンにサインインします。


## 次のステップ

Azure PowerShell で新しい仮想マシンを管理する方法については、[Azure Resource Manager と PowerShell を使用した仮想マシンの管理](virtual-machines-windows-ps-manage.md)に関するページをご覧ください。

<!---HONumber=AcomDC_0928_2016-->