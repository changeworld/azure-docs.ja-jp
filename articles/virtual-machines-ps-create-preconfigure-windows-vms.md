<properties 
	pageTitle="Azure PowerShell を使用して Windows ベースの仮想マシンを作成と事前構成する" 
	description="Azure PowerShell を使用して、Windows ベースの Azure 仮想マシンを作成と事前構成する方法について説明します。" 
	services="virtual-machines" 
	documentationCenter="" 
	authors="JoeDavies-MSFT" 
	manager="timlt" 
	editor=""/>

<tags 
	ms.service="virtual-machines" 
	ms.workload="infrastructure-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="03/05/2015" 
	ms.author="josephd"/>

# Azure PowerShell を使用して Windows ベースの仮想マシンを作成と事前構成する

以下の手順では、構成ブロック手法を使用して、Azure PowerShell コマンド セットをカスタマイズする方法を示します。このコマンド セットでは、Windows ベースの Azure 仮想マシンを作成と事前構成します。このプロセスを使用すると、新しい Windows ベースの仮想マシンのコマンド セットを迅速に作成して既存のデプロイメントを拡張することや、複数のコマンド セットを作成してカスタムの開発とテスト環境または IT プロの環境をすばやく構築することもできます。

これらの手順では、空白に記入する方式に従って Azure PowerShell コマンド セットを作成します。この方法は、PowerShell を初めて使う場合や、構成を正しく行うためにどの値を指定するとよいかを知りたい場合に役立ちます。PowerShell に慣れているユーザーは、コマンドの変数を独自の値で置き換えることができます ("$" で始まる行)。

このトピックと対になっている、Linux ベースの仮想マシンの構成については、「[Azure PowerShell を使用して Linux ベースの仮想マシンを作成と事前構成する](http://azure.microsoft.com/documentation/articles/virtual-machines-ps-create-preconfigure-linux-vms/)」をご覧ください。

## 手順 1:Azure PowerShell をインストールするには

まだ完了していない場合は、「[Azure PowerShell のインストールと構成方法](install-configure-powershell.md)」の手順に従って、Azure PowerShell をご使用のローカル コンピューターにインストールします。次に、Azure PowerShell コマンド プロンプトを開きます。

## 手順 2:サブスクリプションとストレージ アカウントの設定

Azure PowerShell コマンド プロンプトでこれらのコマンドを実行して Azure サブスクリプションとストレージ アカウントを設定します。引用符内のすべての文字 (< および > を含む) を、正しい名前に置き換えます。

	$subscr="<subscription name>"
	$staccount="<storage account name>"
	Select-AzureSubscription -SubscriptionName $subscr -Current
	Set-AzureSubscription -SubscriptionName $subscr -CurrentStorageAccountName $staccount

**Get-AzureSubscription** コマンドで出力される SubscriptionName プロパティで正しいサブスクリプション名を取得できます。**Select-AzureSubscription** コマンドの実行後、**Get-AzureStorageAccount** コマンドを実行すると、出力される Label プロパティで正しいストレージ アカウント名を取得できます。これらのコマンドをテキスト ファイルに保存して、後で使用することもできます。

## 手順 3:ImageFamily の特定

次に、作成する Azure 仮想マシンに対応する特定のイメージで使用するために、ImageFamily または Label の値を特定する必要があります。Azure 管理ポータルにあるギャラリーに例がいくつかあります。

![](./media/virtual-machines-use-PS-create-preconfigure-windows-vms/PSPreconfigWindowsVMs_1.png)
 
このコマンドで、利用可能な ImageFamily 値の一覧を取得できます。

	Get-AzureVMImage | select ImageFamily -Unique

Windows ベースのコンピューターで使用する ImageFamily 値の例は次のとおりです。

- Windows Server 2012 R2 Datacenter 
- [Windows Server 2008 R2 SP1] 
- Windows Server Technical Preview 
- SQL Server 2012 SP1 Enterprise on Windows Server 2012 

目的のイメージが見つかったら、選択したテキスト エディターの最新インスタンスを開き、ImageFamily 値に置き換える以下のコマンドを新しいテキスト ファイルにコピーします。 

	$family="<ImageFamily value>"
	$image=Get-AzureVMImage | where { $_.ImageFamily -eq $family } | sort PublishedDate -Descending | select -ExpandProperty ImageName -First 1

イメージの名前は、ImageFamily 値ではなく Label プロパティに存在する場合があります。ImageFamily プロパティを使用しても目的のイメージが見つからなかった場合は、次のコマンドを実行して Label プロパティのイメージを一覧表示します。

	Get-AzureVMImage | select Label -Unique

このコマンドで正しいイメージが見つかったら、選択したテキスト エディターの最新のインスタンスを開き、Label 値を置き換える以下のコマンドを新しいテキスト ファイルにコピーします。 

	$label="<Label value>"
	$image = Get-AzureVMImage | where { $_.Label -eq $label } | sort PublishedDate -Descending | select -ExpandProperty ImageName -First 1

## 手順 4:コマンド セットの構築

下の該当するブロック セットを新しいテキスト ファイルにコピーしてから変数の値を入力し、文字 < と > を削除する手順によって残りのコマンド セットを構築します。この記事の最後の 2 つの[例](#examples) を、最終結果のアイデアとしてご覧ください。

この 2 つのコマンド ブロックのいずれかを選択することからコマンド セットを開始します (必須)。


方法 1:仮想マシンの名前とサイズを指定します。

	$vmname="<machine name>"
	$vmsize="<Specify one: Small, Medium, Large, ExtraLarge, A5, A6, A7, A8, A9>"
	$vm1=New-AzureVMConfig -Name $vmname -InstanceSize $vmsize -ImageName $image

方法 2:名前、サイズ、可用性セット名を指定します。

	$vmname="<machine name>"
	$vmsize="<Specify one: Small, Medium, Large, ExtraLarge, A5, A6, A7, A8, A9>"
	$availset="<set name>"
	$vm1=New-AzureVMConfig -Name $vmname -InstanceSize $vmsize -ImageName $image -AvailabilitySetName $availset

D、DS、または G シリーズの各仮想マシンの InstanceSize 値の詳細については、「[Azure の仮想マシンとクラウド サービスのサイズ](https://msdn.microsoft.com/library/azure/dn197896.aspx)」をご覧ください。

スタンドアロンの Windows コンピューターの場合は、必要に応じて、ローカル管理者のアカウントとパスワードを指定します。

	$localadminusername="<local administrator account name>"
	$localadminpassword="<local administrator account password>"
	$vm1 | Add-AzureProvisioningConfig -Windows -AdminUsername $localadminusername -Password $localadminpassword

また、必要に応じて Windows コンピューターを既存の Active Directory ドメインに追加するには、ローカル管理者のアカウントとパスワード、ドメイン名、ドメイン アカウントの名前とパスワードを指定します。

	$localadminusername="<local administrator account name>"
	$localadminpassword="<local administrator account password>"
	$domaindns="<FQDN of the domain that the machine is joining>"
	$domacctdomain="<domain of the account that has permission to add the machine to the domain>"
	$domacctname="<domain account name that has permission to add the machine to the domain>"
	$domacctpassword="<password of the domain account that has permission to add the machine to the domain>"
	$vm1 | Add-AzureProvisioningConfig -AdminUserName $localadminusername -Password $localadminpassword -WindowsDomain -Domain $domacctdomain -DomainUserName $domacctname -DomainPassword $domacctpassword -JoinDomain $domaindns

アカウント名とパスワードの指定は両方の方法で必要になります。最終的なコマンド セットをファイルに保存する場合は、ファイルを安全な場所に保管して保護されるようにします。

これ以外の Windows ベースの事前構成の追加方法については、「[Add-AzureProvisioningConfig](https://msdn.microsoft.com/library/azure/dn495299.aspx)」で **Windows** パラメーター セットと **WindowsDomain** パラメーター セットの構文をご覧ください。

必要に応じて、次のように、"静的 DIP" と呼ばれる特定の IP アドレスを仮想マシンに割り当てます。

	$vm1 | Set-AzureStaticVNetIP -IPAddress <IP address>

次のように実行すると、特定の IP アドレスが利用可能かどうかを調べることができます。

	Test-AzureStaticVNetIP -VNetName <VNet name> -IPAddress <IP address>

必要に応じて、次のように、Azure 仮想ネットワークの特定のサブネットに仮想マシンを割り当てます。

	$vm1 | Set-AzureSubnet -SubnetNames "<name of the subnet>"

必要に応じて、次のように、単一データ ディスクを仮想マシンに追加します。 

	$disksize=<size of the disk in GB>
	$disklabel="<the label on the disk>"
	$lun=<Logical Unit Number (LUN) of the disk>
	$hcaching="<Specify one: ReadOnly, ReadWrite, None>"
	$vm1 | Add-AzureDataDisk -CreateNew -DiskSizeInGB $disksize -DiskLabel $disklabel -LUN $lun -HostCaching $hcaching

Active Directory ドメイン コントローラーでは、$hcaching を "None" に設定します。

必要に応じて、外部トラフィックを処理するために、既存の負荷分散セットに仮想マシンを次のように追加します。

	$prot="<Specify one: tcp, udp>"
	$localport=<port number of the internal port>
	$pubport=<port number of the external port>
	$endpointname="<name of the endpoint>"
	$lbsetname="<name of the existing load-balanced set>"
	$probeprotocol="<Specify one: tcp, udp>"
	$probeport=<TCP or UDP port number of probe traffic>
	$probepath="<URL path for probe traffic>"
	$vm1 | Add-AzureEndpoint -Name $endpointname -Protocol $prot -LocalPort $localport -PublicPort $pubport -LBSetName $lbsetname -ProbeProtocol $probeprotocol -ProbePort $probeport -ProbePath $probepath

最後に、これらのコマンド ブロックのいずれかを選択して、仮想マシン作成プロセスを開始します (必須)。

方法 1:新しいクラウド サービスに仮想マシンを作成する。 

	New-AzureVM -Location "<An Azure location, such as US West>" -VMs $vm1

方法 2:既存のクラウド サービスに仮想マシンを作成する。 

	New-AzureVM -ServiceName "<short name of the cloud service>" -VMs $vm1

クラウド サービスの短い名前が、Azure 管理ポータルの [クラウド サービス] ボックスの一覧または、Azure プレビュー ポータルの [リソース グループ] ボックスの一覧に表示されます。 

方法 3:仮想マシンを既存のクラウド サービスと仮想ネットワークに作成する。

	$svcname="<short name of the cloud service>"
	$vnetname="<name of the virtual network>"
	New-AzureVM -ServiceName $svcname -VMs $vm1 -VNetName $vnetname

## 手順 5:コマンド セットの実行

手順 4. でテキスト エディターを使用して作成した、複数のコマンド ブロックで構成される Azure PowerShell コマンド セットを確認します。必要なすべての変数が指定され、それらの値がすべて正しいことを確認します。さらに、文字 < と > がすべて削除されていることも確認します。

クリップボードにコマンド セットをコピーしてから、開いている Azure PowerShell コマンド プロンプトを右クリックします。この操作により、コマンド セットが一連の PowerShell コマンドとして実行され、Azure 仮想マシンが作成されます。

この仮想マシンまたは同様のマシンを再び作成する場合は、次のことができます。 

- このコマンド セットをテキスト ファイルまたは PowerShell スクリプト ファイル (*.ps1) として保存する。
- Azure 管理ポータルの **[自動化]** セクションに、このコマンド セットを Azure の Runbook Automation として保存する。 

## <a id="examples"></a>例

次に、Windows ベースの Azure 仮想マシンを作成するために、前の手順を使用して Azure PowerShell コマンド セットを構築する例を 2 つ示します。

### 例 1

次の条件で Active Directory ドメイン コントローラーに最初の仮想マシンを作成する PowerShell コマンド セットが必要な場合。

- Windows Server 2012 R2 Datacenter イメージを使用する
- 名前は AZDC1 
- スタンドアロン コンピューターである
- 20 GB の追加データ ディスク容量
- 静的 IP アドレスは 192.168.244.4
- AZDatacenter 仮想ネットワークの BackEnd サブネットに属している
- Azure-TailspinToys クラウド サービスに属している

この仮想マシンを作成するための対応する Azure PowerShell コマンド セットは次のとおりです。読みやすくするために各ブロックの間に空白行を入れてあります。

	$family="Windows Server 2012 R2 Datacenter"
	$image=Get-AzureVMImage | where { $_.ImageFamily -eq $family } | sort PublishedDate -Descending | select -ExpandProperty ImageName -First 1
	$vmname="AZDC1"
	$vmsize="Medium"
	$vm1=New-AzureVMConfig -Name $vmname -InstanceSize $vmsize -ImageName $image

	$localadminusername="DCLocalAdmin"
	$localadminpassword="DCeq7294*"
	$vm1 | Add-AzureProvisioningConfig -Windows -AdminUsername $localadminusername -Password $localadminpassword

	$vm1 | Set-AzureSubnet -SubnetNames "BackEnd"

	$vm1 | Set-AzureStaticVNetIP -IPAddress 192.168.244.4

	$disksize=20
	$disklabel="DCData"
	$lun=0
	$hcaching="None"
	$vm1 | Add-AzureDataDisk -CreateNew -DiskSizeInGB $disksize -DiskLabel $disklabel -LUN $lun -HostCaching $hcaching

	$svcname="Azure-TailspinToys"
	$vnetname="AZDatacenter"
	New-AzureVM -ServiceName $svcname -VMs $vm1 -VNetName $vnetname

### 例 2

次の条件で基幹業務サーバー用に仮想マシンを作成する PowerShell コマンド セットが必要な場合。

- Windows Server 2012 R2 Datacenter イメージを使用する
- 名前は LOB1
- corp.contoso.com ドメインのメンバーである
- 200 GB の追加データ ディスク容量 
- AZDatacenter 仮想ネットワークの FrontEnd サブネットに属している
- Azure-TailspinToys クラウド サービスに属している

この仮想マシンを作成するための対応する Azure PowerShell コマンド セットは次のとおりです。

	$family="Windows Server 2012 R2 Datacenter"
	$image=Get-AzureVMImage | where { $_.ImageFamily -eq $family } | sort PublishedDate -Descending | select -ExpandProperty ImageName -First 1
	$vmname="LOB1"
	$vmsize="Large"
	$vm1=New-AzureVMConfig -Name $vmname -InstanceSize $vmsize -ImageName $image

	$localadminusername="LOBLocalAdmin"
	$localadminpassword="LOBmx7137*"
	$domacctdomain="CORP"
	$domacctname="admin7"
	$domacctpassword="frank0987&"
	$domaindns="corp.contoso.com"
	$vm1 | Add-AzureProvisioningConfig -AdminUserName $localadminusername -Password $ localadminpassword -WindowsDomain -Domain $domacctdomain -DomainUserName $domacctname -DomainPassword $domacctpassword -JoinDomain $domaindns

	$vm1 | Set-AzureSubnet -SubnetNames "FrontEnd"

	$disksize=200
	$disklabel="LOBData"
	$lun=0
	$hcaching="ReadWrite"
	$vm1 | Add-AzureDataDisk -CreateNew -DiskSizeInGB $disksize -DiskLabel $disklabel -LUN $lun -HostCaching $hcaching

	$svcname="Azure-TailspinToys"
	$vnetname="AZDatacenter"
	New-AzureVM -ServiceName $svcname -VMs $vm1 -VNetName $vnetname


## その他のリソース

[仮想マシンに関するドキュメント](http://azure.microsoft.com/documentation/services/virtual-machines/)

[Azure の仮想マシンに関する FAQ](http://msdn.microsoft.com/library/azure/dn683781.aspx)

[Azure の仮想マシンの概要](http://msdn.microsoft.com/library/azure/jj156143.aspx)

[Azure PowerShell のインストールと構成方法](install-configure-powershell.md)

[Azure PowerShell を使用して Linux ベースの仮想マシンを作成と事前構成する](virtual-machines-ps-create-preconfigure-linux-vms.md)

<!--HONumber=47-->
