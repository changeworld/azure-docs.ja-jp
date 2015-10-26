<properties
	pageTitle="Azure PowerShell を使用した Windows VM の作成と管理 | Microsoft Azure"
	description="従来のデプロイ モデルを使用して Azure PowerShell で Windows 仮想マシンを作成および管理する"
	services="virtual-machines"
	documentationCenter=""
	authors="cynthn"
	manager="timlt"
	editor=""
	tags="azure-service-management"/>

<tags
	ms.service="virtual-machines"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-windows"
	ms.devlang="na"
	ms.topic="article"
	ms.date="07/09/2015"
	ms.author="cynthn"/>

# Azure PowerShell を使用した Windows 仮想マシンの作成と管理

この記事では、Azure PowerShell を使用して、Windows ベースの仮想マシンを作成および管理する方法について説明します。

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)] [Resource Manager model](virtual-machines-deploy-rmtemplates-powershell.md)。



## Azure PowerShell の設定

Azure PowerShell をインストール済みである場合、Azure PowerShell Version 0.8.0 以降であることが必要です。インストールした Azure PowerShell のバージョンは、Azure PowerShell コマンド プロンプトで次のコマンドを使用して確認できます。

	Get-Module azure | format-table version

まだ完了していない場合は、[Azure PowerShell のインストールと構成の方法](../install-configure-powershell.md)に関するページの手順に従って、Azure PowerShell をご使用のローカル コンピューターにインストールします。次に、Azure PowerShell コマンド プロンプトを開きます。

まず、次のコマンドを使用して Azure にサインインする必要があります。

	Add-AzureAccount

Microsoft Azure のサインイン ダイアログで、Azure アカウントの電子メール アドレスとそのパスワードを指定します。

次に、Azure サブスクリプションが複数ある場合は、使用する Azure サブスクリプションを設定する必要があります。現在のサブスクリプションを一覧表示するには、次のコマンドを実行します。

	Get-AzureSubscription | sort SubscriptionName | Select SubscriptionName

以下のコマンドを、引用符で囲まれた部分 (< and > を含む) を正しいサブスクリプション名に置き換えて実行します。

	$subscrName="<subscription name>"
	Select-AzureSubscription -SubscriptionName $subscrName –Current

## 仮想マシンの作成

最初に、ストレージ アカウントが必要になります。現在使用しているストレージ アカウントは、次のコマンドを使用すると一覧表示できます。

	Get-AzureStorageAccount | sort Label | Select Label

まだストレージ アカウントを持っていない場合は、新しいストレージ アカウントを作成します。小文字と数字のみから成る一意の名前を選んでください。ストレージ アカウントの名前が一意であるかどうかは、次のコマンドを使用することで確認できます。

	Test-AzureName -Storage <Proposed Storage account name>

このコマンドで "False" が返された場合は、提案した名前は一意です。

ストレージ アカウントを作成する際は、Azure データセンターの場所を指定する必要があります。Azure データセンターの一覧を取得するには、次のコマンドを実行します。

	Get-AzureLocation | sort Name | Select Name

以下のコマンドを使用して、ストレージ アカウントの作成と設定を行います。引用符で囲まれた部分 (< and > を含む) は、実際のストレージ アカウントの名前に置き換えてください。

	$stAccount="<chosen Storage account name>"
	$locName="<Azure location>"
	New-AzureStorageAccount -StorageAccountName $stAccount -Location $locName
	Set-AzureStorageAccount -StorageAccountName $stAccount
	Set-AzureSubscription -SubscriptionName $subscrName -CurrentStorageAccountName $stAccount

次に、クラウド サービスが必要です。クラウド サービスがまだない場合は、新たに作成する必要があります。小文字、数字、ハイフンのみから成る一意の名前を選んでください。フィールドの先頭と末尾の文字は、文字または数字としてください。

たとえば、TestCS-*UniqueSequence* という名前を付けることができます。*UniqueSequence* は組織の略称です。たとえば、組織の名前が Tailspin Toys であれば、クラウド サービスに TestCS-Tailspin という名前を付けることができます。

次の Azure PowerShell コマンドを使用することで、名前の一意性を確認することができます。

	Test-AzureName -Service <Proposed cloud service name>

このコマンドで "False" が返された場合は、提案した名前は一意です。以下のコマンドを使用して、クラウド サービスを作成します。

	$csName="<cloud service name>"
	$locName="<Azure location>"
	New-AzureService -Service $csName -Location $locName

次に、以下の一連の Azure PowerShell コマンドを、メモ帳などのテキスト エディターにコピーします。

	$vmName="<machine name>"
	$csName="<cloud service name>"
	$locName="<Azure location>"
	$image=Get-AzureVMImage | where { $_.ImageFamily -eq "Windows Server 2012 R2 Datacenter" } | sort PublishedDate -Descending | select -ExpandProperty ImageName -First 1
	$vm=New-AzureVMConfig -Name $vmName -InstanceSize Medium -ImageName $image
	$cred=Get-Credential -Message "Type the name and password of the local administrator account."
	$vm | Add-AzureProvisioningConfig -Windows -AdminUsername $cred.GetNetworkCredential().Username -Password $cred.GetNetworkCredential().Password
	New-AzureVM –ServiceName $csName –Location $locName -VMs $vm

ご使用のテキスト エディターで、仮想マシンの名前、クラウド サービスの名前、場所を入力します。

最後に、一連のコマンドをクリップボードにコピーしてから、開いている Azure PowerShell コマンド プロンプトを右クリックします。これにより一連の Azure PowerShell コマンドが実行され、ローカル管理者アカウントの名前とパスワードの入力を求めるメッセージが表示されてから、Azure 仮想マシンが作成されます。以下の例に、一連のコマンドが実行される様子を示します。

	PS C:\> $vmName="PSTest"
	PS C:\> $csName=" TestCS-Tailspin"
	PS C:\> $locName="West US"
	PS C:\> $image=Get-AzureVMImage | where { $_.ImageFamily -eq "Windows Server 2012 R2 Datacenter" } | sort PublishedDate -Descending | select -ExpandProperty ImageName -First 1
	VERBOSE: 3:01:17 PM - Begin Operation: Get-AzureVMImage
	VERBOSE: 3:01:22 PM - Completed Operation: Get-AzureVMImage
	VERBOSE: 3:01:22 PM - Begin Operation: Get-AzureVMImage
	VERBOSE: 3:01:23 PM - Completed Operation: Get-AzureVMImage
	PS C:\> $vm=New-AzureVMConfig -Name $vmName -InstanceSize Medium -ImageName $image
	PS C:\> $cred=Get-Credential -Message "Type the name and password of the local administrator account."
	PS C:\> $vm | Add-AzureProvisioningConfig -Windows -AdminUsername $cred.GetNetworkCredential().Username -Password $cred.
	GetNetworkCredential().Password


	AvailabilitySetName               :
	ConfigurationSets                 : PSTest,Microsoft.WindowsAzure.Commands.ServiceManagement.Model.NetworkConfigurationSet}
	DataVirtualHardDisks              : {}
	Label                             : PSTest
	OSVirtualHardDisk                 : Microsoft.WindowsAzure.Commands.ServiceManagement.Model.OSVirtualHardDisk
	RoleName                          : PSTest
	RoleSize                          : Medium
	RoleType                          : PersistentVMRole
	WinRMCertificate                  :
	X509Certificates                  : {}
	NoExportPrivateKey                : False
	NoRDPEndpoint                     : False
	NoSSHEndpoint                     : False
	DefaultWinRmCertificateThumbprint :
	ProvisionGuestAgent               : True
	ResourceExtensionReferences       : {BGInfo}
	DataVirtualHardDisksToBeDeleted   :
	VMImageInput                      :

	PS C:\> New-AzureVM -ServiceName $csName -Location $locName -VMs $vm
	VERBOSE: 3:01:46 PM - Begin Operation: New-AzureVM - Create Deployment with VM PSTest
	VERBOSE: 3:02:49 PM - Completed Operation: New-AzureVM - Create Deployment with VM PSTest

	OperationDescription                    OperationId                            OperationStatus
	--------------------                    -----------                            --------------
	New-AzureVM                             8072cbd1-4abe-9278-9de2-8826b56e9221   Succeeded

## 仮想マシンに関する情報の表示
これは頻繁に使用することになる基本的なタスクです。VM に関する情報の取得、VM でのタスクの実行、変数に格納する出力の取得に使用します。

VM に関する情報を取得するには、このコマンドを実行し、引用符内のすべての文字 (< and > を含む) を置き換えます。

     Get-AzureVM -ServiceName "<cloud service name>" -Name "<virtual machine name>"

出力を $vm 変数に格納するには、次のように実行します。

    $vm = Get-AzureVM -ServiceName "<cloud service name>" -Name "<virtual machine name>"

## Windows ベースの仮想マシンへのログオン

次の各コマンドを実行します。

	$svcName="<cloud service name>"
	$vmName="<virtual machine name>"
	$localPath="<drive and folder location to store the downloaded RDP file, example: c:\temp >"
	$localFile=$localPath + "" + $vmname + ".rdp"
	Get-AzureRemoteDesktopFile -ServiceName $svcName -Name $vmName -LocalPath $localFile -Launch

>[AZURE.NOTE]仮想マシンとクラウド サービス名は、**Get-azurevm** コマンドの表示から取得できます。

## VM の停止

次のコマンドを実行します。

    Stop-AzureVM -ServiceName "<cloud service name>" -Name "<virtual machine name>"

>[AZURE.IMPORTANT]**StayProvisioned** パラメーターは、VM がクラウド サービス内の最後の VM である場合に、そのクラウド サービスの仮想 IP (VIP) を保持するために使用します。このパラメーターを使用する場合、その VM に対して引き続き課金されます。

## VM の起動

次のコマンドを実行します。

    Start-AzureVM -ServiceName "<cloud service name>" -Name "<virtual machine name>"

## データ ディスクの接続
このタスクには、いくつかの手順が必要です。まず、**Add-AzureDataDisk** コマンドレットを使用して、$vm オブジェクトにディスクを追加します。次に、Update-AzureVM コマンドレットを使用して、VM の構成を更新します。

新しいディスクとデータを含むディスクのどちらを接続するかについても決める必要があります。新しいディスクの場合、コマンドによって .vhd ファイルが作成され、それが同じコマンドで接続されます。

新しいディスクを接続するには、次のコマンドを実行します。

    Add-AzureDataDisk -CreateNew -DiskSizeInGB <disk size> -DiskLabel "<label name>" -LUN <LUN number> -VM $vm | Update-AzureVM

既存のデータ ディスクを接続するには、次のコマンドを実行します。

    Add-AzureDataDisk -Import -DiskName "<existing disk name>" -LUN <LUN number> | Update-AzureVM

BLOB ストレージにある既存の .vhd ファイルからデータ ディスクを接続するには、次のコマンドを実行します。

    $diskLoc="https://mystorage.blob.core.windows.net/mycontainer/" + "<existing disk name>" + ".vhd"
	Add-AzureDataDisk -ImportFrom -MediaLocation  $diskLoc -DiskLabel "<label name>" -LUN <LUN number> | Update-AzureVM

## その他のリソース

[リソース マネージャーと Azure PowerShell を使用して Windows 仮想マシンを作成する](virtual-machines-create-windows-powershell-resource-manager.md)

[リソース マネージャー テンプレートと Azure PowerShell を使用して Windows 仮想マシンを作成する](virtual-machines-create-windows-powershell-resource-manager-template-simple.md)

[Virtual Machines のドキュメント](http://azure.microsoft.com/documentation/services/virtual-machines/)

[Azure PowerShell のインストールおよび構成方法](../install-configure-powershell.md)

[Azure PowerShell を使用して Windows ベースの仮想マシンを作成および事前構成する](virtual-machines-ps-create-preconfigure-windows-vms.md)

<!---HONumber=Oct15_HO3-->