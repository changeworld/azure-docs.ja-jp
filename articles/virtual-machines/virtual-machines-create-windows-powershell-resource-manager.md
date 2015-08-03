<properties
	pageTitle="Azure リソース マネージャーと PowerShell で Windows 仮想マシンを作成する"
	description="新しい Windows 仮想マシンを簡単に作成するには、Azure PowerShell のリソース管理モードを使用します。"
	services="virtual-machines"
	documentationCenter=""
	authors="davidmu1"
	manager="timlt"
	editor=""/>

<tags
	ms.service="virtual-machines"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="06/02/2015"
	ms.author="davidmu"/>

# Azure リソース マネージャーと PowerShell で Windows 仮想マシンを作成する

このトピックでは、Azure リソース マネージャーと PowerShell を使用して Windows ベースの Azure Virtual Machine を短時間で作成する方法について説明します。

[AZURE.INCLUDE [resource-manager-pointer-to-service-management](../../includes/resource-manager-pointer-to-service-management.md)]

- [PowerShell と Azure サービス管理で Windows 仮想マシンを作成する](virtual-machines-create-windows-powershell-service-manager.md)

## Windows Virtual Machine の作成

Azure PowerShell をインストール済みである場合、Azure PowerShell Version 0.9.0 以降であることが必要です。インストールした Azure PowerShell のバージョンは、Azure PowerShell コマンド プロンプトで次のコマンドを使用して確認できます。

	Get-Module azure | format-table version

Azure PowerShell をまだインストールしていない場合やバージョンを更新する必要がある場合は、[Azure PowerShell のインストールと構成の方法](install-configure-powershell.md)に関するページの手順に従って、ローカル コンピューターに Azure PowerShell をインストールしてください。次に、Azure PowerShell コマンド プロンプトを開きます。

まず、次のコマンドで Azure にログオンする必要があります。

	Add-AzureAccount

Microsoft Azure のサインイン ダイアログで、Azure アカウントの電子メール アドレスとそのパスワードを指定します。

次に、Azure サブスクリプションが複数ある場合は、使用する Azure サブスクリプションを設定する必要があります。現在のサブスクリプションを一覧表示するには、次のコマンドを実行します。

	Get-AzureSubscription | sort SubscriptionName | Select SubscriptionName

以下のコマンドを、引用符で囲まれた部分 (< and > を含む) を正しいサブスクリプション名に置き換えて実行します。

	$subscrName="<subscription name>"
	Select-AzureSubscription -SubscriptionName $subscrName –Current

次に、ストレージ アカウントを作成する必要があります。小文字と数字のみから成る一意の名前を選んでください。ストレージ アカウントの名前が一意であるかどうかは、次のコマンドを使用することで確認できます。

	Test-AzureName -Storage <Proposed storage account name>

このコマンドで "False" が返された場合は、提案した名前は一意です。

Azure データセンターの場所を指定する必要があります。Azure データセンターの一覧を取得するには、次のコマンドを実行します。

	Get-AzureLocation | sort Name | Select Name

次に、Azure PowerShell のモードをリソース マネージャーに切り替える必要があります。次のコマンドを実行します。

	Switch-AzureMode AzureResourceManager

以下の PowerShell コマンド ブロックをテキスト エディターにコピーします。引用符で囲まれた部分 (< and > を含む) は、実際のストレージ アカウントおよび場所に置き換えてください。

	$stName="<chosen storage account name>"
	$locName="<chosen Azure location name>"
	$rgName="TestRG"
	New-AzureResourceGroup -Name $rgName -Location $locName
	$storageAcc=New-AzureStorageAccount -ResourceGroupName $rgName -Name $stName -Type "Standard_GRS" -Location $locName
	$singleSubnet=New-AzureVirtualNetworkSubnetConfig -Name singleSubnet -AddressPrefix 10.0.0.0/24
	$vnet=New-AzurevirtualNetwork -Name TestNet -ResourceGroupName $rgName -Location $locName -AddressPrefix 10.0.0.0/16 -Subnet $singleSubnet
	$pip = New-AzurePublicIpAddress -Name TestNIC -ResourceGroupName $rgName -Location $locName -AllocationMethod Dynamic
	$nic = New-AzureNetworkInterface -Name TestNIC -ResourceGroupName $rgName -Location $locName -SubnetId $vnet.Subnets[0].Id -PublicIpAddressId $pip.Id
	$cred = Get-Credential -Message "Type the name and password of the local administrator account."
	$vm = New-AzureVMConfig -VMName WindowsVM -VMSize "Standard_A1"
	$vm = Set-AzureVMOperatingSystem -VM $vm -Windows -ComputerName MyWindowsVM -Credential $cred -ProvisionVMAgent -EnableAutoUpdate
	$vm = Set-AzureVMSourceImage -VM $vm -PublisherName MicrosoftWindowsServer -Offer WindowsServer -Skus 2012-R2-Datacenter -Version "latest"
	$vm = Add-AzureVMNetworkInterface -VM $vm -Id $nic.Id
	$osDiskUri = $storageAcc.PrimaryEndpoints.Blob.ToString() + "vhds/WindowsVMosDisk.vhd"
	$vm = Set-AzureVMOSDisk -VM $vm -Name "windowsvmosdisk" -VhdUri $osDiskUri -CreateOption fromImage
	New-AzureVM -ResourceGroupName $rgName -Location $locName -VM $vm

最後に、この一連のコマンドをクリップボードにコピーしてから、開いている Azure PowerShell コマンド プロンプトを右クリックします。すると、一連の PowerShell コマンドが実行され、ローカル管理者アカウントの名前とパスワードの入力を求めるメッセージが表示されて、Azure Virtual Machines が作成されます。

コマンドの実行例を以下に示します。

	PS C:\> $stName="contosost"
	PS C:\> $locName="West US"
	PS C:\> $rgName="TestRG"
	PS C:\> New-AzureResourceGroup -Name $rgName -Location $locName
	VERBOSE: 12:45:15 PM - Created resource group 'TestRG' in location 'westus'


	ResourceGroupName : TestRG
	Location          : westus
	ProvisioningState : Succeeded
	Tags              :
	Permissions       :
	                    Actions  NotActions
	                    =======  ==========
	                    *

	ResourceId        : /subscriptions/fd92919d-eeca-4f5b-840a-e45c6770d92e/resourceGroups/TestRG


	PS C:\> $storageAcc=New-AzureStorageAccount -ResourceGroupName $rgName -Name $stName -Type "Standard_GRS" -Location $locName
	PS C:\> $singleSubnet=New-AzureVirtualNetworkSubnetConfig -Name singleSubnet -AddressPrefix 10.0.0.0/24
	PS C:\> $vnet=New-AzurevirtualNetwork -Name TestNet3 -ResourceGroupName $rgName -Location $locName -AddressPrefix 10.0.0.0/16 -Subnet $singleSubnet
	PS C:\> $pip = New-AzurePublicIpAddress -Name TestNIC -ResourceGroupName $rgName -Location $locName -AllocationMethod Dynamic
	PS C:\> $nic = New-AzureNetworkInterface -Name TestNIC -ResourceGroupName $rgName -Location $locName -SubnetId $vnet.Subnets[0].Id -PublicIpAddressId $pip.Id
	PS C:\> $cred = Get-Credential -Message "Type the name and password of the local administrator account."
	PS C:\> $vm = New-AzureVMConfig -VMName WindowsVM -VMSize "Standard_A1"
	PS C:\> $vm = Set-AzureVMOperatingSystem -VM $vm -Windows -ComputerName MyWindowsVM -Credential $cred -ProvisionVMAgent -EnableAutoUpdate
	PS C:\> $vm = Set-AzureVMSourceImage -VM $vm -PublisherName MicrosoftWindowsServer -Offer WindowsServer -Skus 2012-R2-Datacenter -Version "latest"
	PS C:\> $vm = Add-AzureVMNetworkInterface -VM $vm -Id $nic.Id
	PS C:\> $osDiskUri = $storageAcc.PrimaryEndpoints.Blob.ToString() + "vhds/MyWindowsVMosDisk.vhd"
	PS C:\> $vm = Set-AzureVMOSDisk -VM $vm -Name "windowsvmosdisk" -VhdUri $osDiskUri -CreateOption fromImage
	PS C:\> New-AzureVM -ResourceGroupName $rgName -Location $locName -VM $vm


	EndTime             : 4/28/2015 1:00:05 PM -07:00
	Error               :
	Output              :
	StartTime           : 4/28/2015 12:52:52 PM -07:00
	Status              : Succeeded
	TrackingOperationId : 45035a90-ea12-4e1e-87e7-2a5e9ed12c93
	RequestId           : 98c7b4fb-b26e-4a58-b17a-b0983d896aae
	StatusCode          : OK

## その他のリソース

[Azure リソース マネージャーにおける Azure Compute、ネットワーク、ストレージ プロバイダー](virtual-machines-azurerm-versus-azuresm.md)

[Azure リソース マネージャーの概要](resource-group-overview.md)

[リソース マネージャー テンプレートと PowerShell で Windows 仮想マシンを作成する](virtual-machines-create-windows-powershell-resource-manager-template-simple.md)

[PowerShell と Azure サービス管理で Windows 仮想マシンを作成する](virtual-machines-create-windows-powershell-service-manager.md)

[Virtual Machines のドキュメント](http://azure.microsoft.com/documentation/services/virtual-machines/)

[Azure PowerShell のインストールおよび構成方法](install-configure-powershell.md)

<!---HONumber=July15_HO4-->