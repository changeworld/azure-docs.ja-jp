<properties
	pageTitle="Linux VM のコピーの作成 | Microsoft Azure"
	description="*特殊化イメージ* を作成して、Resource Manager デプロイ モデルで、Linux を実行する Azure 仮想マシン のコピーを作成する方法について説明します。"
	services="virtual-machines-linux"
	documentationCenter=""
	authors="cynthn"
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
	ms.author="cynthn"/>

# Azure Resource Manager デプロイメント モデルでの Linux 仮想マシンのコピーの作成


この記事では、Linux を実行している Azure 仮想マシン (VM) のコピーを作成する方法について説明します。具体的には、Resource Manager デプロイメント モデルで、Azure CLI と Azure ポータルを使用してこの操作を行う方法を説明します。また、元の VM のユーザー アカウントやその他の状態データが保持された Azure VM の*特殊化*イメージを作成する方法を示します。特殊化イメージが役立つのは、Linux 仮想マシンを従来のデプロイメント モデルから Resource Manager デプロイメント モデルに移植したり、Resource Manager デプロイメント モデルで作成された Linux 仮想マシンのバックアップ コピーを作成したりする場合です。この方法でオペレーティング システムやデータ ディスクをコピーし、ネットワーク リソースをセットアップして、新しい仮想マシンを作成することができます。

同様の Linux VM の一括デプロイを作成する必要がある場合は、*一般化された*イメージを使用します。これについては、[Linux 仮想マシンのキャプチャ方法](virtual-machines-linux-capture-image.md)に関するページをご覧ください。



## 開始する前に

手順を開始する前に、次の前提条件が満たされていることを確認します。

- Linux を実行している Azure 仮想マシンがある。この仮想マシンは、クラシック デプロイメント モデルまたは Resource Manager デプロイメント モデルのいずれかを使用して作成されている必要があります。また、オペレーティング システムの構成、データ ディスクの接続、その他のカスタマイズ (必要なアプリケーションのインストールなど) が完了している必要があります。この VM を使用して、コピーを作成します。ソース VM の作成については、[Azure での Linux VM 作成](virtual-machines-linux-quick-create-cli.md)に関するページをご覧ください。

- 使用しているコンピューターに Azure CLI がダウンロードおよびインストールされていること。また、Azure サブスクリプションにサインインしていること。詳細については、[Azure CLI のインストール方法](../xplat-cli-install.md)に関するページをご覧ください。

- リソース グループ、ストレージ アカウント、および BLOB コンテナーが、VHD のコピー先となるリソース グループで作成されていること。ストレージ アカウントおよびBLOB コンテナーの作成の詳細については、「[Azure Storage での Azure CLI の使用 ](../storage/storage-azure-cli.md)」をご覧ください。

> [AZURE.NOTE] 2 つのデプロイメント モデルのいずれかをソース イメージとして使用して作成された VM には、同様の手順が適用されます。この記事では、必要に応じてそのわずかな違いについて説明します。


## Resource Manager ストレージ アカウントに VHD ファイルをコピーする


1. 次のいずれかの操作を行って、ソース VM が使用する VHD を解放します。

	- ソース仮想マシンをコピーする場合は、仮想マシンを停止し、割り当てを解除します。ポータルで、**[参照]**、**[仮想マシン]** または **[仮想マシン (クラシック)]**、*目的の仮想マシン*、**[停止]** の順にクリックします。Resource Manager デプロイメント モデルで作成された VM の場合、Azure CLI コマンドを使用することもできます。その場合は、`azure vm stop <yourResourceGroup> <yourVmName>` コマンドの後に `azure vm deallocate <yourResourceGroup> <yourVmName>` コマンドを使用します。ポータルの VM の状態が **[実行中]** から **[停止済み (割り当て解除)]** に変わります。

	- ソース仮想マシンを移行する場合は、その VM を削除し、後に残った VHD を使用します。[ポータル](https://portal.azure.com)で仮想マシンを参照し、**[削除]** をクリックします。

1. ソース VHD を含むストレージ アカウントのアクセス キーを検索します。アクセス キーの詳細については、「[Azure ストレージ アカウントについて](../storage/storage-create-storage-account.md)」を参照してください。

	- ソース VM がクラシック デプロイメント モデルを使用して作成された場合は、**[参照]**、**[ストレージ アカウント (クラシック)]**、*使用しているストレージ アカウント*、**[すべての設定]**、**[キー]** の順にクリックします。**プライマリ アクセス キー**としてラベル付けされているキーをコピーします。または、Azure CLI で、`azure config mode asm` を使用してクラシック モードに変更してから `azure storage account keys list <yourSourceStorageAccountName>` を使用します。

	- ソース VM が Resource Manager デプロイメント モデルを使用して作成された場合は、**[参照]**、**[ストレージ アカウント]**、*使用しているストレージ アカウント*、**[すべての設定]**、**[アクセス キー]** の順にクリックします。**key1** としてラベル付されているテキストをコピーします。または、Azure CLI で、`azure config mode arm` を使用して Resource Manager モードであることを確認してから `azure storage account keys list -g <yourDestinationResourceGroup> <yourDestinationStorageAccount>` を使用します。

1. 次の手順で説明するように、[Storage で Azure CLI コマンド](../storage/storage-azure-cli.md)を使用して VHD ファイルをコピーします。また、ユーザー インターフェイスを使いたい場合は、代わりに [Microsoft Azure Storage エクスプローラー](http://storageexplorer.com/)を使用できます。</br>
	1. コピー先のストレージ アカウントの接続文字列を設定します。この接続文字列は、ストレージ アカウントのアクセス キーを含みます。

			$azure storage account connectionstring show -g <yourDestinationResourceGroup> <yourDestinationStorageAccount>
			$export AZURE_STORAGE_CONNECTION_STRING=<the_connectionstring_output_from_above_command>

	2. ソース ストレージ アカウントで VHD ファイルの [Shared Access Signature](../storage/storage-dotnet-shared-access-signature-part-1.md) を作成します。次のコマンドの **Shared Access URL** 出力をメモします。

			$azure storage blob sas create  --account-name <yourSourceStorageAccountName> --account-key <SourceStorageAccessKey> --container <SourceStorageContainerName> --blob <FileNameOfTheVHDtoCopy> --permissions "r" --expiry <mm/dd/yyyy_when_you_want_theSAS_to_expire>

	3. 次のコマンドを使用して、ソース ストレージからターゲットに VHD をコピーします。

			$azure storage blob copy start <SharedAccessURL_ofTheSourceVHD> <DestinationContainerName>

	4. VHD ファイルは非同期的にコピーされます。次のコマンドを使用して、進行状況を確認します。

			$azure storage blob copy show <DestinationContainerName> <FileNameOfTheVHDtoCopy>

</br>

>[AZURE.NOTE] 既に説明したように、オペレーティング システムとデータ ディスクは個別にコピーする必要があります。


## コピーした VHD を使用して VM を作成する

前の手順でコピーした VHD を使用して、Azure CLI を使って、Resource Manager ベースの Linux VM を新しい仮想ネットワークに作成できます。VHD は、作成される新しい仮想マシンと同じストレージ アカウント内に存在する必要があります。


次のスクリプトを参考にして、新しい VM 用に仮想ネットワークと NIC を設定します。変数には実際のアプリケーションに適した値を使用します。

	$azure network vnet create <yourResourceGroup> <yourVnetName> -l <yourLocation>

	$azure network vnet subnet create <yourResourceGroup> <yourVnetName> <yourSubnetName>

	$azure network public-ip create <yourResourceGroup> <yourIpName> -l <yourLocation>

	$azure network nic create <yourResourceGroup> <yourNicName> -k <yourSubnetName> -m <yourVnetName> -p <yourIpName> -l <yourLocation>


次のコマンドを使用して、コピーした VHD を使って、新しい仮想マシンを作成します。</br>

	$azure vm create -g <yourResourceGroup> -n <yourVmName> -f <yourNicName> -d <UriOfYourOsDisk> -x <UriOfYourDataDisk> -e <DataDiskSizeGB> -Y -l <yourLocation> -y Linux -z "Standard_A1" -o <DestinationStorageAccountName> -R <DestinationStorageAccountBlobContainer>


データとオペレーティング システム ディスクの URL は、`https://StorageAccountName.blob.core.windows.net/BlobContainerName/DiskName.vhd` のようになります。ポータルでこれを見つけるには、ストレージ コンテナーを参照し、コピーしたオペレーティング システムまたはデータ VHD をクリックして、URL の内容をコピーします。


このコマンドが成功した場合は、次のような出力が表示されます。

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

任意の SSH クライアントを使用して新しい仮想マシンに接続します。その際、元の仮想マシンのアカウントの資格情報を使用します (例：`ssh OldAdminUser@<IPaddressOfYourNewVM>`)。詳細については、「[Azure 上の Linux における SSH の使用方法](virtual-machines-linux-ssh-from-linux.md)」をご覧ください。


## 次のステップ

Azure CLI を使用して新しい仮想マシンを管理する方法については、[Azure Resource Manager の Azure CLI コマンド](azure-cli-arm-commands.md)に関する記事をご覧ください。

<!---HONumber=AcomDC_0525_2016-->