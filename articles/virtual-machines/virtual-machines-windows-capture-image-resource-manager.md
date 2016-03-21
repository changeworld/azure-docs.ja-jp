<properties
	pageTitle="リソース マネージャーでの Windows VM のキャプチャ | Microsoft Azure"
	description="Azure リソース マネージャー デプロイメント モデルで作成された Windows ベースの Azure 仮想マシン (VM) のイメージをキャプチャする方法について説明します。"
	services="virtual-machines"
	documentationCenter=""
	authors="dsk-2015"
	manager="timlt"
	editor=""
	tags="azure-resource-manager"/>

<tags
	ms.service="virtual-machines"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-windows"
	ms.devlang="na"
	ms.topic="article"
	ms.date="01/29/2016"
	ms.author="dkshir"/>


# リソース マネージャー デプロイメント モデルで Windows 仮想マシンをキャプチャする方法

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)] [classic deployment model](virtual-machines-capture-image-windows-server.md)。


ここでは、Azure PowerShell を使用して Windows を実行する Azure 仮想マシンをキャプチャし、それを使用して他の仮想マシンを作成する方法を説明します。このイメージには、仮想マシンに接続された OS ディスクやデータ ディスクが含まれます。Windows VM の作成に必要な仮想ネットワーク リソースは含まれないので、イメージを使用して別の仮想マシンを作成する前に、これらをセットアップする必要があります。このイメージは、[一般化された Windows イメージ](https://technet.microsoft.com/library/hh824938.aspx)としても準備されます。


## 前提条件

これらの手順では、既にリソース マネージャー デプロイメント モデルによって Azure 仮想マシンが作成され、データ ディスクの接続やその他のカスタマイズ (たとえば、アプリケーションのインストールなど) を含むオペレーティング システムの構成が完了しているものと仮定します。これらをまだ行っていない場合は、「[リソース マネージャーと PowerShell で Windows VM を作成する](virtual-machines-create-windows-powershell-resource-manager.md)」を参照してください。[Azure ポータル](https://portal.azure.com)を使用すると Windows 仮想マシンを簡単に作成できます。「[Azure ポータルで Windows 仮想マシンを作成する](virtual-machines-windows-tutorial.md)」を参照してください。


## イメージ キャプチャのために VM を準備する

このセクションでは、Windows 仮想マシンを一般化する方法を説明します。特に重要なのは、すべての個人アカウント情報を削除することです。通常、この VM イメージを使用して似た仮想マシンを迅速にデプロイする場合は、これを行う必要があります。

1. Windows 仮想マシンにログインします。[Azure ポータル](https://portal.azure.com)で、**[参照]**、**[仮想マシン]**、対象の Windows 仮想マシン、**[接続]** の順に選択します。

2. 管理者としてコマンド プロンプト ウィンドウを開きます。

3. ディレクトリを `%windir%\system32\sysprep` に変更し、sysprep.exe を実行します。

4. **[システム準備ツール]** ダイアログ ボックスで以下のようにします。

	- **[システム クリーンアップ アクション]** で **[システムの OOBE (Out-of-Box Experience) に入る]** を選択し、**[一般化する]** チェック ボックスがオンになっていることを確認します。Sysprep の使い方の詳細については、「[Sysprep の使用方法: 紹介](http://technet.microsoft.com/library/bb457073.aspx)」を参照してください。

	- **[シャットダウン オプション]** の **[シャットダウン]** を選択します。

	- **[OK]** をクリックします。

	![Sysprep を実行する](./media/virtual-machines-windows-capture-image-resource-manager/SysprepGeneral.png)

5.	仮想マシンがシャットダウンされます。Azure ポータルで状態が **[停止済み]** に変わります。


</br>
## VM をキャプチャする

Azure PowerShell または新しい Azure リソース マネージャー (ARM) エクスプローラー ツールを使用して、一般化された Windows VM をキャプチャできます。ここでは両方の手順を説明します。

### PowerShell の使用

この記事では、Azure PowerShell バージョン 1.0.x をインストールしてあるものとします。古いバージョンの PowerShell にはリソース マネージャーの新しい機能が追加されていないので、このバージョンを使用することをお勧めします。バージョンの違いについて詳しくは、「[Azure PowerShell 1.0](https://azure.microsoft.com/blog/azps-1-0/)」を参照してください。

1. Azure PowerShell 1.0.x を開き、Azure アカウントにログインします。

		Login-AzureRmAccount

	ポップアップ ウィンドウが開くので、Azure の資格情報を入力します。

2. 既定で選択されているサブスクリプション ID が作業するものと異なる場合は、次のいずれかを使用して適切なサブスクリプションを設定します。

		Set-AzureRmContext -SubscriptionId "xxxx-xxxx-xxxx-xxxx"

	または

		Select-AzureRmSubscription -SubscriptionId "xxxx-xxxx-xxxx-xxxx"

	Azure アカウントのサブスクリプションを検索するには、`Get-AzureRmSubscription` コマンドを使用します。

3. 次に、この仮想マシンで使用されているリソースの割り当てを解除する必要があります。

		Stop-AzureRmVM -ResourceGroupName YourResourceGroup -Name YourWindowsVM

	Azure ポータルで仮想マシンの状態が **[停止済み]** から **[停止済み (割り当て解除)]** に変わります。

	>[AZURE.TIP] PowerShell の次のコマンドを使用して、仮想マシンの状態を調べることもできます。</br> `$vm = Get-AzureRmVM -ResourceGroupName YourResourceGroup -Name YourWindowsVM -status`</br> `$vm.Statuses`</br>**DisplayStatus** フィールドが、Azure ポータルに表示される **[状態]** に対応します。

4. 次に、仮想マシンの状態を _[一般化]_ に設定する必要があります。この手順が必要になるのは、上記の一般化手順 (`sysprep`) は Azure が認識可能な方法では行われないためです。

		Set-AzureRmVm -ResourceGroupName YourResourceGroup -Name YourWindowsVM -Generalized

	>[AZURE.NOTE] 上で設定された一般化状態は、ポータルには表示されません。ただし、上のヒントで示したように Get-AzureRmVM コマンドを使用すれば確認できます。

5. 次のコマンドを使用して、仮想マシンのイメージをキャプチャ先ストレージ コンテナーにキャプチャします。

		Save-AzureRmVMImage -ResourceGroupName YourResourceGroup -VMName YourWindowsVM -DestinationContainerName YourImagesContainer -VHDNamePrefix YourTemplatePrefix -Path Yourlocalfilepath\Filename.json

	省略可能な `-Path` 変数を使用すると、JSON テンプレートをローカルに保存できます。`-DestinationContainerName` 変数は、イメージを格納するコンテナーの名前です。格納されたイメージの URL は、`https://YourStorageAccountName.blob.core.windows.net/system/Microsoft.Compute/Images/YourImagesContainer/YourTemplatePrefix-osDisk.xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx.vhd` のようになります。元の仮想マシンと同じストレージ アカウントに作成されます。

	>[AZURE.NOTE] イメージの場所を見つけるには、ローカルの JSON ファイル テンプレートを開きます。イメージの完全なパスは、**resources** > **storageProfile** > **osDisk** > **image** > **uri** セクションにあります。現時点では、ストレージ アカウントの _system_ コンテナーが非表示であるため、ポータルでこれらのイメージを確認する簡単な方法はありません。このため、`-Path` 変数は省略可能ですが、テンプレートをローカルに保存してイメージの URL を簡単に確認するには必ず必要になります。または、次のセクションで説明する **Azure Storage エクスプローラー** ツールを使用して確認することもできます。


### Azure リソース エクスプローラー (プレビュー) の使用

[Azure リソース エクスプローラー (プレビュー)](https://azure.microsoft.com/blog/azure-resource-explorer-a-new-tool-to-discover-the-azure-api/) は、リソース マネージャー デプロイメント モデルで作成された Azure リソースを管理するために開発された新しいツールです。このツールを使用すると次の作業を簡単にできます。

- Azure リソース管理 API を簡単に調べる
- API のドキュメントを見る
- Azure サブスクリプションで API を直接呼び出す

この強力なツールの機能の詳細については、ビデオ「[Azure Resource Manager Explorer with David Ebbo (David Ebbo が紹介する Azure リソース マネージャー エクスプローラー)](https://channel9.msdn.com/Shows/Azure-Friday/Azure-Resource-Manager-Explorer-with-David-Ebbo)」をご覧ください。

PowerShell の方法の代わりにリソース エクスプローラーを使用して、仮想マシンをキャプチャできます。

1. [リソース エクスプローラーの Web サイト](https://resources.azure.com/)を開き、Azure アカウントにログインします。

2. ツールの右上にある [**読み取り/書き込み**] を選択し、_[PUT]_ 操作と _[POST]_ 操作を許可します。既定では **[読み取り専用]** に設定されているので、_[GET]_ 操作しか許可されません。

	![リソース エクスプローラー: 読み取り/書き込み](./media/virtual-machines-windows-capture-image-resource-manager/ArmExplorerReadWrite.png)

3. 目的の Windows 仮想マシンを探します。ツールの上部にある_検索ボックス_に名前を入力して検索するか、または左側のメニューで **[サブスクリプション]**、対象の Azure サブスクリプション、**resourceGroups**、対象のリソース グループ、**[プロバイダー]**、**[Microsoft.Compute]**、**[仮想マシン]**、対象の Windows 仮想マシンの順に移動します。左側のナビゲーションで仮想マシンをクリックすると、ツールの右側に対応するテンプレートが表示されます。

4. テンプレート ページの右上には、その仮想マシンで使用できるさまざまな操作のタブが表示されます。**[アクション (POST/DELETE)]** タブをクリックします。

	![リソース エクスプローラー: アクション メニュー](./media/virtual-machines-windows-capture-image-resource-manager/ArmExplorerActionMenu.png)

5. その仮想マシンで実行できるすべてのアクションの一覧が表示されます。

	![リソース エクスプローラー: アクション項目](./media/virtual-machines-windows-capture-image-resource-manager/ArmExplorerActionItems.png)

6. **[割り当て解除]** のアクション ボタンをクリックして、仮想マシンの割り当てを解除します。VM の状態が、**[停止済み]** から **[停止済み (割り当て解除)]** に変わります。

7. **[一般化]** のアクション ボタンをクリックし、仮想マシンを一般化済みとしてマークします。左側の仮想マシン名の下にある **[インスタンス ビュー]** メニューをクリックし、右側で **[状態]** セクションに移動して、状態を確認することもできます。

8. **[キャプチャ]** アクション ボタンの下では、イメージをキャプチャするための値を設定できます。入力した値は次のようになります。

	![リソース エクスプローラー: キャプチャ](./media/virtual-machines-windows-capture-image-resource-manager/ArmExplorerCaptureAction.png)

	**[キャプチャ]** アクション ボタンをクリックして、仮想マシンのイメージをキャプチャします。イメージの新しい VHD と共に、JSON テンプレート ファイルが作成されます。現時点では、JSON テンプレート ファイルには、リソース エクスプローラーまたは [Azure ポータル](https://portal.azure.com)からはアクセスできません。

9. 新しいイメージ VHD およびテンプレートにアクセスするには、ストレージ リソース管理用の Azure ツールである [Azure Storage エクスプローラー](http://storageexplorer.com/)をダウンロードしてインストールします。コンピューターに Azure Storage エクスプローラーがローカルにインストールされます。

	- Storage エクスプローラーを開き、Azure サブスクリプションにログインします。サブスクリプションで使用可能なすべてのストレージ アカウントが表示されます。

	- 左側に、前の手順でキャプチャした仮想マシンのストレージ アカウントが表示されます。その下の **[システム]** メニューをダブルクリックします。右側に **[システム]** フォルダーの内容が表示されます。

		![ストレージ エクスプローラー: システム](./media/virtual-machines-windows-capture-image-resource-manager/StorageExplorer1.png)

	- **[Microsoft.Compute]**、**[イメージ]** の順にダブルクリックすると、すべてのイメージ フォルダーが表示されます。リソース エクスプローラーからイメージをキャプチャするときに **destinationContainerName** 変数に入力したフォルダー名をダブルクリックします。VHD と JSON テンプレート ファイルの両方が表示されます。

	- ここからは、URL を確認したり、右クリックして VHD やテンプレートをダウンロードしたりできます。

		![ストレージ エクスプローラー: テンプレート](./media/virtual-machines-windows-capture-image-resource-manager/StorageExplorer2.png)


## キャプチャしたイメージから新しい VM をデプロイする

キャプチャしたイメージを使用して、新しい Windows 仮想マシンを作成できます。次の手順では、Azure PowerShell と上の手順でキャプチャした VM イメージを使用して、新しい仮想ネットワークに VM を作成する方法を示します。

>[AZURE.NOTE] VM イメージは、作成される実際の仮想マシンと同じストレージ アカウント内に存在する必要があります。

### ネットワーク リソースを作成する

次のサンプル PowerShell スクリプトを使用して、新しい VM 用に仮想ネットワークと NIC を設定します。**$** 記号によって表される変数には、実際のアプリケーションに適した値を使用します。

	$pip = New-AzureRmPublicIpAddress -Name $pipName -ResourceGroupName $rgName -Location $location -AllocationMethod Dynamic

	$subnetconfig = New-AzureRmVirtualNetworkSubnetConfig -Name $subnet1Name -AddressPrefix $vnetSubnetAddressPrefix

	$vnet = New-AzureRmVirtualNetwork -Name $vnetName -ResourceGroupName $rgName -Location $location -AddressPrefix $vnetAddressPrefix -Subnet $subnetconfig

	$nic = New-AzureRmNetworkInterface -Name $nicname -ResourceGroupName $rgName -Location $location -SubnetId $vnet.Subnets[0].Id -PublicIpAddressId $pip.Id

### 新しい仮想マシンを作成する

次の PowerShell スクリプトでは、仮想マシンの構成を設定し、キャプチャした VM イメージを新しいインストールのソースとして使用する方法を示します。 </br>

	#Enter a new username and password in the pop-up for the following
	$cred = Get-Credential

	#Get the storage account where the captured image is stored
	$storageAcc = Get-AzureRmStorageAccount -ResourceGroupName $rgName -AccountName $storageAccName

	#Set the VM name and size
	$vmConfig = New-AzureRmVMConfig -VMName $vmName -VMSize "Standard_A2"

	#Set the Windows operating system configuration and add the NIC
	$vm = Set-AzureRmVMOperatingSystem -VM $vmConfig -Windows -ComputerName $computerName -Credential $cred -ProvisionVMAgent -EnableAutoUpdate

	$vm = Add-AzureRmVMNetworkInterface -VM $vm -Id $nic.Id

	#Create the OS disk URI
	$osDiskUri = '{0}vhds/{1}{2}.vhd' -f $storageAcc.PrimaryEndpoints.Blob.ToString(), $vmName.ToLower(), $osDiskName

	#Configure the OS disk to be created from image (-CreateOption fromImage) and give the URL of the captured image VHD for the -SourceImageUri parameter.
	#We found this URL in the local JSON template in the previous sections.
	$vm = Set-AzureRmVMOSDisk -VM $vm -Name $osDiskName -VhdUri $osDiskUri -CreateOption fromImage -SourceImageUri $urlOfCapturedImageVhd -Windows

	#Create the new VM
	New-AzureRmVM -ResourceGroupName $rgName -Location $location -VM $vm

新しく作成された VM は、[Azure ポータル](https://portal.azure.com)の **[参照]** の **[仮想マシン]**、または次の PowerShell コマンドで確認できます。

	$vmList = Get-AzureRmVM -ResourceGroupName $rgName
	$vmList.Name


## 次のステップ

Azure PowerShell で新しい仮想マシンを管理する方法については、「[Azure リソース マネージャーと PowerShell を使用した仮想マシンの管理](virtual-machines-deploy-rmtemplates-powershell.md)」を参照してください。

<!---HONumber=AcomDC_0204_2016-->