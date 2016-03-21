<properties
	pageTitle="PowerShell での SQL Server 仮想マシンの作成 | Microsoft Azure"
	description="SQL Server 仮想マシン ギャラリー のイメージを使用して Azure VM を作成するための手順と PowerShell スクリプトを提供します。"
	services="virtual-machines"
	documentationCenter="na"
	authors="rothja"
	manager="jeffreyg"
	editor="monicar"
	tags="azure-service-management" />
<tags
	ms.service="virtual-machines"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="vm-windows-sql-server"
	ms.workload="infrastructure-services"
	ms.date="01/22/2016"
	ms.author="jroth" />

# Azure での SQL Server 仮想マシンの作成 (PowerShell)

> [AZURE.SELECTOR]
- [Classic portal](virtual-machines-provision-sql-server.md)
- [PowerShell](virtual-machines-sql-server-create-vm-with-powershell.md)
- [Azure Resource Manager portal](virtual-machines-sql-server-provision-resource-manager.md)


## 概要

この記事では、PowerShell コマンドレットを使用して、Azure で SQL Server 仮想マシンを作成する手順を説明します。

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]リソース マネージャー モデル。


## PowerShell のインストールと構成

1. Azure アカウントを持っていない場合は、[Azure の無料試用版サイト](https://azure.microsoft.com/pricing/free-trial/)にアクセスしてください。

2. [最新の Azure PowerShell コマンドレットをインストールします。](../powershell-install-configure.md/#how-to-install-azure-powershell)

3. [PowerShell と Azure サブスクリプションを接続します](../powershell-install-configure.md/#how-to-connect-to-your-subscription)。

## ターゲット Azure リージョンの決定

SQL Server の仮想マシンは、特定の Azure リージョンに存在するクラウド サービスでホストされます。次の手順は、このチュートリアルの残りの部分に使用されるリージョン、ストレージ アカウント、およびクラウド サービスの決定に役立ちます。

1. SQL Server VM のホストに使用するデータ センターを決定します。次の PowerShell コマンドによって、使用可能なリージョンの詳細が文末の要約リストとともに表示されます。

		Get-AzureLocation
		(Get-AzureLocation).Name

2.  希望する場所を特定したら、**$dcLocation** という名前の変数をそのリージョンに設定します。

		$dcLocation = "<region name>"

## サブスクリプションとストレージ アカウントの設定

1. 新しい仮想マシンに使用する Azure サブスクリプションを決定します。

		(Get-AzureSubscription).SubscriptionName

1. ターゲットの Azure サブスクリプションを **$subscr** 変数に割り当てます。次に、これを現在の Azure のサブスクリプションとして設定します。

		$subscr="<subscription name>"
		Select-AzureSubscription -SubscriptionName $subscr –Current

1. 既存のストレージ アカウントを確認します。次のスクリプトでは、選択したリージョン内に存在するすべてのストレージ アカウントが表示されます。

		(Get-AzureStorageAccount | where { $_.GeoPrimaryLocation -eq $dcLocation }).StorageAccountName

	>[AZURE.NOTE] 新しいストレージ アカウントが必要な場合は、次の例のように、New-AzureStorageAccount コマンドを使用してすべてが小文字のストレージ アカウント名を最初に作成します: **New-AzureStorageAccount -StorageAccountName "<storage account name>" -Location $dcLocation**

1. 対象のストレージ アカウント名を **$staccount** に割り当てます。次に、**Set-AzureSubscription** を使用してサブスクリプションと現在のストレージ アカウントを設定します。

		$staccount="<storage account name>"
		Set-AzureSubscription -SubscriptionName $subscr -CurrentStorageAccountName $staccount

## SQL Server 仮想マシン イメージの選択

1. ギャラリーから使用可能な SQL Server 仮想マシン イメージのリストを見つけます。これらのイメージにはすべて、"SQL" で始まる **ImageFamily** プロパティが含まれています。次のクエリでは、SQL Server が事前にインストールされている、使用可能なイメージ ファミリが表示されます。

		Get-AzureVMImage | where { $_.ImageFamily -like "SQL*" } | select ImageFamily -Unique | Sort-Object -Property ImageFamily

1. 仮想マシン イメージのファミリが見つかったら、そのファミリに発行されたイメージが複数ある可能性があります。次のスクリプトを使用して、選択したイメージ ファミリについて発行された最新の仮想マシン イメージ名を見つけます (**SQL Server 2014 SP1 Enterprise on Windows Server 2012 R2** など)。

		$family="<ImageFamily value>"
		$image=Get-AzureVMImage | where { $_.ImageFamily -eq $family } | sort PublishedDate -Descending | select -ExpandProperty ImageName -First 1

		echo "Selected SQL Server image name:"
		echo "   $image"

## 仮想マシンの作成

最後に、PowerShell を使用して仮想マシンを作成します。

1. 新しい VM をホストするクラウド サービスを作成します。既存のクラウド サービスを代わりに使用することもできます。クラウド サービスの短縮名を使用して、新しい **$svcname** 変数を作成します。

		$svcname = "<cloud service name>"
		New-AzureService -ServiceName $svcname -Label $svcname -Location $dcLocation

2. 仮想マシンの名前とサイズを指定します。仮想マシンのサイズの詳細については、「[Azure の Virtual Machines のサイズ](virtual-machines-size-specs.md)」を参照してください。

		$vmname="<machine name>"
		$vmsize="<Specify a valid machine size>" # see the link to virtual machine sizes
		$vm1=New-AzureVMConfig -Name $vmname -InstanceSize $vmsize -ImageName $image

3. ローカル管理者アカウントとパスワードを指定します。

		$cred=Get-Credential -Message "Type the name and password of the local administrator account."
		$vm1 | Add-AzureProvisioningConfig -Windows -AdminUsername $cred.GetNetworkCredential().Username -Password $cred.GetNetworkCredential().Password

4. 次のスクリプトを実行して、仮想マシンを作成します。

		New-AzureVM –ServiceName $svcname -VMs $vm1

>[AZURE.NOTE] 詳細な説明と構成オプションについては、「[Azure PowerShell を使用して Windows ベースの仮想マシンを作成および事前構成する](virtual-machines-ps-create-preconfigure-windows-vms.md)」の「**コマンド セットの構築**」セクションを参照してください。

## PowerShell サンプル スクリプト

次のスクリプトは、**SQL Server 2014 SP1 Enterprise on Windows Server 2012 R2** 仮想マシンを作成する完全なスクリプトの例です。このスクリプトを使用する場合、このトピックの前のステップに基づいて、初期変数をカスタマイズする必要があります。

	# Customize these variables based on your settings and requirements:
	$dcLocation = "East US"
	$subscr="mysubscription"
	$staccount="mystorageaccount"
	$family="SQL Server 2014 SP1 Enterprise on Windows Server 2012 R2"
	$svcname = "mycloudservice"
	$vmname="myvirtualmachine"
	$vmsize="A5"

	# Set the current subscription and storage account
	# Comment out the New-AzureStorageAccount line if the account already exists
	Select-AzureSubscription -SubscriptionName $subscr –Current
	New-AzureStorageAccount -StorageAccountName $staccount -Location $dcLocation
	Set-AzureSubscription -SubscriptionName $subscr -CurrentStorageAccountName $staccount

	# Select the most recent VM image in this image family:
	$image=Get-AzureVMImage | where { $_.ImageFamily -eq $family } | sort PublishedDate -Descending | select -ExpandProperty ImageName -First 1

	# Create the new cloud service; comment out this line if cloud service exists already:
	New-AzureService -ServiceName $svcname -Label $svcname -Location $dcLocation

	# Create the VM config:
	$vm1=New-AzureVMConfig -Name $vmname -InstanceSize $vmsize -ImageName $image

	# Set administrator credentials:
	$cred=Get-Credential -Message "Type the name and password of the local administrator account."
	$vm1 | Add-AzureProvisioningConfig -Windows -AdminUsername $cred.GetNetworkCredential().Username -Password $cred.GetNetworkCredential().Password

	# Create the SQL Server VM:
	New-AzureVM –ServiceName $svcname -VMs $vm1


## リモート デスクトップへの接続

1. 現在のユーザーのドキュメント フォルダに .RDP ファイルを作成し、これらの仮想マシンを起動してセットアップを完了します。

		$documentspath = [environment]::getfolderpath("mydocuments")
		Get-AzureRemoteDesktopFile -ServiceName $svcname -Name $vmname -LocalPath "$documentspath\vm1.rdp"

1. ドキュメント ディレクトリで、RDP ファイルを起動します。以前に提供された管理ユーザー名とパスワードを使用して接続します (たとえば、ユーザー名が VMAdmin であった場合、ユーザーとして「\\VMAdmin」を指定し、パスワードを入力します)。

		.\vm1.rdp

## リモート アクセス用の SQL Server コンピューターの構成を完了する

リモート デスクトップを使用してコンピューターにログオンした後、「[Azure VM で SQL Server への接続を構成する手順](virtual-machines-sql-server-connectivity.md#steps-for-configuring-sql-server-connectivity-in-an-azure-vm)」の説明に基づいて SQL Server を構成します。

## 次のステップ

PowerShell を使用した仮想マシンのプロビジョニングの詳細については、[仮想マシンのドキュメント](virtual-machines-ps-create-preconfigure-windows-vms.md)を参照してください。SQL Server および Premium Storage に関連するその他のスクリプトについては、「[仮想マシン上での Azure Premium Storage と SQL Server の使用](virtual-machines-sql-server-use-premium-storage.md)」を参照してください。

多くの場合、次の手順はこの新しい SQL Server VM にデータベースを移行することです。データベース移行方法については、「[Azure VM の SQL Server へのデータベースの移行](virtual-machines-migrate-onpremises-database.md)」を参照してください。

これらの手順をAzure クラシック ポータルから実行する方法についても興味がある場合は、「[Azure での SQL Server 仮想マシンのプロビジョニング](virtual-machines-provision-sql-server.md)」を参照してください。

これらのリソースのほかにも、[Azure Virtual Machines における SQL Server の実行に関連するその他のトピック](virtual-machines-sql-server-infrastructure-services.md)もご覧になることをお勧めします。

<!---HONumber=AcomDC_0128_2016-->