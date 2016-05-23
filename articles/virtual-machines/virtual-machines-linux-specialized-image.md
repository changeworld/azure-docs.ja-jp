<properties
	pageTitle="Linux VM のコピーの作成 | Microsoft Azure"
	description="*特殊化イメージ* を作成して、Resource Manager デプロイ モデルで、Linux を実行する Azure 仮想マシン のコピーを作成する方法について説明します。"
	services="virtual-machines-linux"
	documentationCenter=""
	authors="dsk-2015"
	manager="timlt"
	editor=""
	tags="azure-resource-manager"/>

<tags
	ms.service="virtual-machines-linux"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-linux"
	ms.devlang="na"
	ms.topic="article"
	ms.date="04/26/2016"
	ms.author="dkshir"/>

# Resource Manager デプロイ モデルで Linux 仮想マシンのコピーを作成する方法



この記事では、Azure CLI および Azure ポータルを使用して、Linux を実行する Azure 仮想マシン (VM) のコピーを Resource Manager デプロイ モデルで作成する方法について説明します。元の仮想マシン (VM) のユーザー アカウントやその他の状態データが保持された Azure 仮想マシン (VM) の**_特殊化_**イメージを作成する方法を示します。 Linux 仮想マシンを従来のデプロイ モデルから Resource Manager デプロイ モデルに移植したり、あるいは Resource Manager デプロイ モデルで作成されたLinux 仮想マシンのバックアップ コピーを作成する場合に、特殊化イメージが役立ちます。この方法で OS やデータ ディスクをコピーし、ネットワーク リソースをセットアップして、新しい仮想マシンを作成することができます。

同様の Linux 仮想マシンの一括デプロイを行うには*一般化された* イメージが必要です。これについては[Linux 仮想マシンのキャプチャ方法](virtual-machines-linux-capture-image.md)に関する記事をご覧ください。



## 始める前のチェック事項

この記事では、以下のことを前提としています。

1. **Linux を実行する Azure 仮想マシン**が、クラシック デプロイメント モデルまたは Resource Manager デプロイメント モデルであり、オペレーティング システムが構成され、データ ディスクが接続され、必要なアプリケーションがインストールされている。この VM の作成方法に関するヘルプが必要な場合は、「[CLI を使用した Azure での Linux VM の作成](virtual-machines-linux-quick-create-cli.md)」をご覧ください。 

1. コンピューターに **Azure CLI** がインストールされており、Azure サブスクリプションにログインしている。詳細については、「[Azure CLI のインストール](../xplat-cli-install.md)」をご覧ください。

1. **リソース グループ**が**ストレージ アカウント**と **BLOB コンテナー**を使用して VHD のコピー先で作成されている。詳細については、「[Azure Storage での Azure CLI の使用](../storage/storage-azure-cli.md)」をご覧ください。



> [AZURE.NOTE] 2 つのデプロイ モデルのいずれかをソース イメージとして使用して作成された VM には、同様の手順が適用されます。手順が若干異なる部分については、その都度説明します。


## Resource Manager ストレージ アカウントに VHD ファイルをコピーする


1. 最初に、次のいずれかのオプションにより、ソース VM の使用した VHD ファイルを 解放します：

	- ソース仮想マシンを**_コピー_**する場合は、仮想マシンを**停止**し、**割り当てを解除**します。ポータルで、**[参照]**、**[仮想マシン]** または **[仮想マシン (クラシック)]**、*目的の仮想マシン*、**[停止]** の順にクリックします。Resource Manager デプロイメント モデルで作成された VM の場合、Azure CLI コマンドを使用することもできます。その場合、`azure vm stop <yourResourceGroup> <yourVmName>` コマンドの後に `azure vm deallocate <yourResourceGroup> <yourVmName>` コマンドを使用します。ポータルの VM の *[状態]* が **[実行中]** から **[停止済み (割り当て解除)]** に変わります。
	
	- ソース仮想マシンを**_移行_**する場合は、その VM を**削除**し、後に残された VHD ファイルを使用します。[ポータル](https://portal.azure.com)で仮想マシンを**参照**し、**[削除]** をクリックします。
	
1. ソース VHD ファイルを含むストレージ アカウントのアクセス キーを検索します。 アクセス キーの詳細については、「[Azure ストレージ アカウントについて](../storage/storage-create-storage-account.md)」をご覧ください。

	- クラシック デプロイメント モデルを使用してソース VM が作成されている場合は、**[参照]**、**[ストレージ アカウント (クラシック)]**、*目的のストレージ アカウント*、**[すべての設定]**、**[キー]** の順にクリックし、**プライマリ アクセス キー**としてラベル付けされているキーをコピーします。または、Azure CLI で、`azure config mode asm` を使用してクラシック モードに変更してから `azure storage account keys list <yourSourceStorageAccountName>` を使用します。

	- Resource Manager デプロイメント モデルを使用して作成された VM の場合、**[参照]**、**[ストレージ アカウント]**、*目的のストレージ アカウント*、**[すべての設定]**、**[アクセス キー]** の順にクリックし、**キー 1** としてラベル付けされているテキストをコピーします。または、Azure CLI で、`azure config mode arm` を使用して Resource Manager モードであることを確認してから `azure storage account keys list -g <yourDestinationResourceGroup> <yourDestinationStorageAccount>` を使用します。

1. 次の手順で説明するように、[Azure Storage で Azure CLI コマンド](../storage/storage-azure-cli.md)を使用して VHD ファイルをコピーします。同じ結果を得るために UI アプローチを選択した場合は、代わりに [Microsoft Azure Storage エクスプローラー](http://storageexplorer.com/)を使用できます。</br>
	1. コピー先のストレージ アカウントの接続文字列を設定します。この接続文字列は、ストレージ アカウントのアクセス キーを含みます。
	
			$azure storage account connectionstring show -g <yourDestinationResourceGroup> <yourDestinationStorageAccount>
			$export AZURE_STORAGE_CONNECTION_STRING=<the_connectionstring_output_from_above_command>
	
	2. ソース ストレージ アカウントで VHD ファイルの [Shared Access Signature](../storage/storage-dotnet-shared-access-signature-part-1.md) を作成します。次のコマンドの **Shared Access URL** 出力をメモします。
	
			$azure storage blob sas create  --account-name <yourSourceStorageAccountName> --account-key <SourceStorageAccessKey> --container <SourceStorageContainerName> --blob <FileNameOfTheVHDtoCopy> --permissions "r" --expiry <mm/dd/yyyy_when_you_want_theSAS_to_expire>
	
	3. 次のコマンドを使用して、ソース ストレージからコピー先に VHD ファイルをコピーします。
	
			$azure storage blob copy start <SharedAccessURL_ofTheSourceVHD> <DestinationContainerName>
	
	4. VHD ファイルが非同期的にコピーされます。次のコマンドを使用して進行状況を確認することができます。
	
			$azure storage blob copy show <DestinationContainerName> <FileNameOfTheVHDtoCopy>
		
</br>

>[AZURE.NOTE] 上記にあるように、OS とデータ ディスクは個別にコピーする必要があります。


## コピーした VHD ファイルを使用して VM を作成

次に、Azure CLI を使用して、Resource Manager ベースの Linux VM を新しい仮想ネットワークに作成する方法について説明します。その際は先程の手順でコピーした VHD ファイルを使います。VHD ファイルは、作成される仮想マシンと同じストレージ アカウント内に存在する必要があります。


はじめに、次のスクリプトを参考にして新しい VM 用に仮想ネットワークと NIC を設定します。変数には実際のアプリケーションに適した値を使用します。

	$azure network vnet create <yourResourceGroup> <yourVnetName> -l <yourLocation>

	$azure network vnet subnet create <yourResourceGroup> <yourVnetName> <yourSubnetName>

	$azure network public-ip create <yourResourceGroup> <yourIpName> -l <yourLocation>

	$azure network nic create <yourResourceGroup> <yourNicName> -k <yourSubnetName> -m <yourVnetName> -p <yourIpName> -l <yourLocation>


次のコマンドを使用して、コピーした VHD ファイルを使って新しい仮想マシンを作成します。</br>

	$azure vm create -g <yourResourceGroup> -n <yourVmName> -f <yourNicName> -d <UriOfYourOsDisk> -x <UriOfYourDataDisk> -e <DataDiskSizeGB> -Y -l <yourLocation> -y Linux -z "Standard_A1" -o <DestinationStorageAccountName> -R <DestinationStorageAccountBlobContainer>

	
データと OS ディスクの URL は、`https://StorageAccountName.blob.core.windows.net/BlobContainerName/DiskName.vhd` のようになります。ポータルでこれを見つけるには、ストレージ コンテナーを参照し、コピーした OS またはデータ の VHD ファイルをクリックして、**URL** の内容をコピーします。
	
	
コマンドが成功した場合は、次のような出力が表示されます：

	$azure vm create -g "testcopyRG" -n "redhatcopy" -f "LinCopyNic" -d https://testcopystore.blob.core.windows.net/testcopyblob/RedHat201631816334.vhd -x https://testcopystore.blob.core.windows.net/testcopyblob/RedHat-data.vhd -e 10 -Y -l "West US" -y Linux -z "Standard_A1" -o "testcopystore" -R "testcopyblob"
	info:    Executing command vm create
	+ Looking up the VM "redhatcopy"
	info:    Using the VM Size "Standard_A1"
	+ Looking up the NIC "LinCopyNic"
	info:    Found an existing NIC "LinCopyNic"
	info:    Found an IP configuration with virtual network subnet id "/subscriptions/b8e6a92b-d6b7-4dbb-87a8-3c8dfe248156/resourceGroups/testcopyRG/providers/Microsoft.Network/virtualNetworks/LinCopyVnet/subnets/LinCopySub" in the NIC "LinCopyNic"
	info:    This NIC IP configuration has a public ip already configured "/subscriptions/b8e6a92b-d6b7-4dbb-87a8-3c8dfe248156/resourcegroups/testcopyrg/providers/microsoft.network/publicipaddresses/lincopyip", any public ip parameters if provided, will be ignored.
	+ Looking up the storage account testcopystore
	info:    The storage URI 'https://testcopystore.blob.core.windows.net/' will be used for boot diagnostics settings, and it can be overwritten by the parameter input of '--boot-diagnostics-storage-uri'.
	+ Creating VM "redhatcopy"
	info:    vm create command OK

**[参照]**、**[仮想マシン]** の順にクリックして、[Azure ポータル](https://portal.azure.com)で新しく作成した VM を確認します。

任意の SSH クライアントを使用して新しい仮想マシンに接続します。その際、元の仮想マシンのアカウントの資格情報を使用します (例：`ssh OldAdminUser@<IPaddressOfYourNewVM>`)。Linux VM における SSH の使用方法の詳細については、[Azure 上の Linux での SSH の使用方法](virtual-machines-linux-ssh-from-linux.md)に関する記事をご覧ください。


## 次のステップ

Azure CLI を使用して新しい仮想マシンを管理する方法については、[Azure Resource Manager の Azure CLI コマンド](azure-cli-arm-commands.md)に関する記事をご覧ください。

<!---HONumber=AcomDC_0511_2016-->