<properties
	pageTitle="Azure PowerShell を使用した Linux VM の作成 | Microsoft Azure"
	description="Azure PowerShell を使用して Linux VM を作成および事前構成する方法を説明します。"
	services="virtual-machines"
	documentationCenter=""
	authors="cynthn"
	manager="timlt"
	editor=""
	tags="azure-service-management"/>

<tags
	ms.service="virtual-machines"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-linux"
	ms.devlang="na"
	ms.topic="article"
	ms.date="11/11/2015"
	ms.author="cynthn"/>

# Azure PowerShell を使用して Linux 仮想マシンを作成および事前構成する

> [AZURE.SELECTOR]
- [Azure CLI](virtual-machines-linux-tutorial.md)
- [PowerShell](virtual-machines-ps-create-preconfigure-linux-vms.md)

<br>

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]リソース マネージャー モデル。
 
Azure PowerShell コマンド セットを作成するために、空欄を埋める方式を使用して Linux 仮想マシンを作成する方法を説明します。この方法は、Azure PowerShell を初めて使う場合や、構成を正しく行うためにどの値を指定するとよいかを知りたい場合に役立ちます。

コマンド ブロック セットをテキスト ファイルか PowerShell ISE にコピーし、変数の値を入力した後、文字 < and > を削除して、コマンド セットを構築します。この記事の末尾にある 2 つの[例](#examples)を、最終結果のアイデアとしてご覧ください。

このトピックと対になっている、Windows ベースの仮想マシンについては、「[Use Azure PowerShell to create Windows-based virtual machines (Azure PowerShell を使用して Windows ベースの仮想マシンを作成する)](virtual-machines-ps-create-preconfigure-windows-vms.md)」を参照してください。

## Azure PowerShell をインストールするには

まだインストールしていない場合は、[Azure PowerShell をインストールして構成します](../install-configure-powershell.md)。次に、Azure PowerShell コマンド プロンプトを開きます。

## サブスクリプションとストレージ アカウントの設定

Azure PowerShell コマンド プロンプトで次のコマンドを実行して Azure サブスクリプションとストレージ アカウントを設定します。

**Get-AzureSubscription** コマンドで出力される **SubscriptionName** プロパティで正しいサブスクリプション名を取得できます。

Select-AzureSubscription コマンドの実行後、**Get-AzureStorageAccount** コマンドを実行すると、出力される **Label** プロパティで正しいストレージ アカウント名を取得できます。

引用符内のすべての文字 (< and > を含む) を、正しい名前に置き換えます。

	$subscr="<subscription name>"
	$staccount="<storage account name>"
	Select-AzureSubscription -SubscriptionName $subscr –Current
	Set-AzureSubscription -SubscriptionName $subscr -CurrentStorageAccountName $staccount


## 使用するイメージを検索する

次に、使用するイメージの ImageFamily 値を特定する必要があります。次のコマンドで、利用可能な ImageFamily 値の一覧を取得できます。

	Get-AzureVMImage | select ImageFamily -Unique

Linux ベースのコンピューターで使用する ImageFamily 値の例は次のとおりです。

- Ubuntu Server 12.10
- CoreOS Alpha
- SUSE Linux Enterprise Server 12

任意のテキスト エディターの最新インスタンスまたは PowerShell Integrated Scripting Environment (ISE) のインスタンスを開きます。新しいテキスト ファイルまたは PowerShell ISE に次のコードをコピーし、ImageFamily 値を置き換えます。

	$family="<ImageFamily value>"
	$image=Get-AzureVMImage | where { $_.ImageFamily -eq $family } | sort PublishedDate -Descending | select -ExpandProperty ImageName -First 1

## 名前、サイズ、および必要に応じて可用性セットを指定します。

この 2 つのコマンド ブロックのいずれかを選択することからコマンド セットを開始します (必須)。

**オプション 1**: 仮想マシンの名前とサイズを指定します。

	$vmname="<machine name>"
	$vmsize="<Specify one: Small, Medium, Large, ExtraLarge, A5, A6, A7, A8, A9>"
	$vm1=New-AzureVMConfig -Name $vmname -InstanceSize $vmsize -ImageName $image

**オプション 2**: 名前、サイズ、可用性セット名を指定します。

	$vmname="<machine name>"
	$vmsize="<Specify one: Small, Medium, Large, ExtraLarge, A5, A6, A7, A8, A9>"
	$availset="<set name>"
	$vm1=New-AzureVMConfig -Name $vmname -InstanceSize $vmsize -ImageName $image -AvailabilitySetName $availset

D、DS、または G シリーズの各仮想マシンの InstanceSize 値の詳細については、「[Azure の仮想マシンおよびクラウド サービスのサイズ](https://msdn.microsoft.com/library/azure/dn197896.aspx)」を参照してください。


## ユーザー アクセス セキュリティ オプションの設定

**オプション 1**: 最初の Linux ユーザー名とパスワードを指定します (必須)。強力なパスワードを選択してください。強度を確認するには、[パスワード チェッカーの強力なパスワードの使用](https://www.microsoft.com/security/pc-security/password-checker.aspx)に関するページを参照してください。

	$cred=Get-Credential -Message "Type the name and password of the initial Linux account."
	$vm1 | Add-AzureProvisioningConfig -Linux -LinuxUser $cred.GetNetworkCredential().Username -Password $cred.GetNetworkCredential().Password

**オプション 2**: サブスクリプションにデプロイ済みの SSH キー ペアのセットを指定します。

	$vm1 | Add-AzureProvisioningConfig -Linux -SSHKeyPairs "<SSH key pairs>"

詳細については、「[Azure 上の Linux における SSH の使用方法](virtual-machines-linux-use-ssh-key.md)」をご覧ください。

**オプション 3**: サブスクリプションにデプロイ済みの SSH 公開キーの一覧を指定します。

	$vm1 | Add-AzureProvisioningConfig -Linux - SSHPublicKeys "<SSH public keys>"

これ以外の Linux ベースの仮想マシンの事前構成のオプションについては、「[Add-AzureProvisioningConfig](https://msdn.microsoft.com/library/azure/dn495299.aspx)」で **Linux** パラメーター セットの構文をご覧ください。


## 省略可能: 静的 DIP を割り当てる

必要に応じて、次のように、"静的 DIP" と呼ばれる特定の IP アドレスを仮想マシンに割り当てます。

	$vm1 | Set-AzureStaticVNetIP -IPAddress <IP address>

次のコマンドを使用して、特定の IP アドレスが利用可能かどうかを調べることができます。

	Test-AzureStaticVNetIP –VNetName <VNet name> –IPAddress <IP address>

## 省略可能: 特定のサブネットに仮想マシンを割り当てる 

次のように、Azure 仮想ネットワークの特定のサブネットに仮想マシンを割り当てます。

	$vm1 | Set-AzureSubnet -SubnetNames "<name of the subnet>"

	
## 省略可能: データ ディスクを追加する
	
以下をコマンド セットに追加し、データ ディスクを仮想マシンに追加します。

	$disksize=<size of the disk in GB>
	$disklabel="<the label on the disk>"
	$lun=<Logical Unit Number (LUN) of the disk>
	$hcaching="<Specify one: ReadOnly, ReadWrite, None>"
	$vm1 | Add-AzureDataDisk -CreateNew -DiskSizeInGB $disksize -DiskLabel $disklabel -LUN $lun -HostCaching $hcaching

## 省略可能: 既存の負荷分散セットに仮想マシンを追加する 

以下をコマンド セットに追加して、外部トラフィックを処理するために、既存の負荷分散セットに仮想マシンを次のように追加します。

	$prot="<Specify one: tcp, udp>"
	$localport=<port number of the internal port>
	$pubport=<port number of the external port>
	$endpointname="<name of the endpoint>"
	$lbsetname="<name of the existing load-balanced set>"
	$probeprotocol="<Specify one: tcp, http>"
	$probeport=<TCP or HTTP port number of probe traffic>
	$probepath="<URL path for probe traffic>"
	$vm1 | Add-AzureEndpoint -Name $endpointname -Protocol $prot -LocalPort $localport -PublicPort $pubport -LBSetName $lbsetname -ProbeProtocol $probeprotocol -ProbePort $probeport -ProbePath $probepath

## 仮想マシンの作成プロセスを開始する方法を決める 

ブロックをコマンド セットに追加して、次のコマンド ブロックのいずれかを選択して、仮想マシン作成プロセスを開始します。

**オプション 1**: 既存のクラウド サービスに仮想マシンを作成します。

	New-AzureVM –ServiceName "<short name of the cloud service>" -VMs $vm1

クラウド サービスの短い名前が、Azure クラシック ポータルの [クラウド サービス] ボックスの一覧か、Azure ポータルの [リソース グループ] ボックスの一覧に表示されます。

**オプション 2**: 仮想マシンを既存のクラウド サービスと仮想ネットワークに作成します。

	$svcname="<short name of the cloud service>"
	$vnetname="<name of the virtual network>"
	New-AzureVM –ServiceName $svcname -VMs $vm1 -VNetName $vnetname

## コマンド セットの実行

テキスト エディターに構築した Azure PowerShell コマンド セットまたは PowerShell ISE をレビューして、すべての変数を指定していることと、それらの値が正しいことを確認します。さらに、文字 < and > がすべて削除されていることも確認します。

クリップボードにコマンド セットをコピーしてから、開いている Azure PowerShell コマンド プロンプトを右クリックします。この操作により、コマンド セットが一連の PowerShell コマンドとして実行され、Azure 仮想マシンが作成されます。

仮想マシンを作成したら、「[Linux を実行する仮想マシンにログオンする方法](virtual-machines-linux-how-to-log-on.md)」をご覧ください。

コマンド セットを再利用する場合は、以下の操作を実行できます。

- このコマンド セットを PowerShell スクリプト ファイル (*.ps1) として保存する。
- Azure クラシック ポータルの **[オートメーション]** セクションで、このコマンド セットを Azure Automation Runbook として保存する

## <a id="examples"></a>例

次に、Linux ベースの Azure 仮想マシンを作成するために、前の手順を使用して Azure PowerShell コマンド セットを構築する例を 2 つ示します。

### 例 1

次の条件で MySQL サーバー用の最初の Linux 仮想マシンを作成する PowerShell コマンド セットが必要な場合。

- Ubuntu Server 12.10 イメージを使用する。
- 名前は AZMYSQL1 。
- 500 GB の追加データ ディスク容量。
- 静的 IP アドレスが 192.168.244.4 。
- AZDatacenter 仮想ネットワークの BackEnd サブネットに属している。
- Azure-TailspinToys クラウド サービスに属している。

この仮想マシンを作成するための対応する Azure PowerShell コマンド セットは次のとおりです。読みやすくするために各ブロックの間に空白行を入れてあります。

	$family="Ubuntu Server 12.10"
	$image=Get-AzureVMImage | where { $_.ImageFamily -eq $family } | sort PublishedDate -Descending | select -ExpandProperty ImageName -First 1

	$vmname="AZMYSQL1"
	$vmsize="Large"
	$vm1=New-AzureVMConfig -Name $vmname -InstanceSize $vmsize -ImageName $image

	$cred=Get-Credential -Message "Type the name and password of the initial Linux account."
	$vm1 | Add-AzureProvisioningConfig -Linux -LinuxUser $cred.GetNetworkCredential().Username -Password $cred.GetNetworkCredential().Password

	$vm1 | Set-AzureSubnet -SubnetNames "BackEnd"

	$vm1 | Set-AzureStaticVNetIP -IPAddress 192.168.244.4

	$disksize=500
	$disklabel="MySQLData"
	$lun=0
	$hcaching="None"
	$vm1 | Add-AzureDataDisk -CreateNew -DiskSizeInGB $disksize -DiskLabel $disklabel -LUN $lun -HostCaching $hcaching

	$svcname="Azure-TailspinToys"
	$vnetname="AZDatacenter"
	New-AzureVM –ServiceName $svcname -VMs $vm1 -VNetName $vnetname

### 例 2

次の条件で Apache サーバー用に Linux 仮想マシンを作成する PowerShell コマンド セットが必要な場合。

- SUSE Linux Enterprise Server 12 イメージを使用する。
- 名前は LOB1。
- 50 GB の追加データ ディスク容量。
- 標準 Web トラフィックの LOBServers ロード バランサー セットのメンバーである。
- AZDatacenter 仮想ネットワークの FrontEnd サブネットに属している。
- Azure-TailspinToys クラウド サービスに属している。

この仮想マシンを作成するための対応する Azure PowerShell コマンド セットは次のとおりです。

	$family="SUSE Linux Enterprise Server 12"
	$image=Get-AzureVMImage | where { $_.ImageFamily -eq $family } | sort PublishedDate -Descending | select -ExpandProperty ImageName -First 1

	$vmname="LOB1"
	$vmsize="Medium"
	$vm1=New-AzureVMConfig -Name $vmname -InstanceSize $vmsize -ImageName $image

	$cred=Get-Credential -Message "Type the name and password of the initial Linux account."
	$vm1 | Add-AzureProvisioningConfig -Linux -LinuxUser $cred.GetNetworkCredential().Username -Password $cred.GetNetworkCredential().Password

	$vm1 | Set-AzureSubnet -SubnetNames "FrontEnd"

	$disksize=50
	$disklabel="LOBApp"
	$lun=0
	$hcaching="ReadWrite"
	$vm1 | Add-AzureDataDisk -CreateNew -DiskSizeInGB $disksize -DiskLabel $disklabel -LUN $lun -HostCaching $hcaching

	$prot="tcp"
	$localport=80
	$pubport=80
	$endpointname="LOB1"
	$lbsetname="LOBServers"
	$probeprotocol="tcp"
	$probeport=80
	$probepath="/"
	$vm1 | Add-AzureEndpoint -Name $endpointname -Protocol $prot -LocalPort $localport -PublicPort $pubport -LBSetName $lbsetname -ProbeProtocol $probeprotocol -ProbePort $probeport -ProbePath $probepath

	$svcname="Azure-TailspinToys"
	$vnetname="AZDatacenter"
	New-AzureVM –ServiceName $svcname -VMs $vm1 -VNetName $vnetname

## その他のリソース

[Virtual Machines のドキュメント](http://azure.microsoft.com/documentation/services/virtual-machines/)

[Azure 仮想マシンに関する FAQ](http://msdn.microsoft.com/library/azure/dn683781.aspx)

[Azure Virtual Machines の概要](http://msdn.microsoft.com/library/azure/jj156143.aspx)

[Azure PowerShell のインストールおよび構成方法](../install-configure-powershell.md)

[Linux を実行する仮想マシンにログオンする方法](virtual-machines-linux-how-to-log-on.md)

[Azure PowerShell を使用して Windows ベースの仮想マシンを作成および事前構成する](virtual-machines-ps-create-preconfigure-windows-vms.md)

<!---HONumber=AcomDC_1203_2015-->