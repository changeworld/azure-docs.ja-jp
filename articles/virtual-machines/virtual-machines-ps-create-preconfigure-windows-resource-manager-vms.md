<properties
	pageTitle="VM の作成と事前構成 | Microsoft Azure"
	description="リソース マネージャー デプロイ モデルと PowerShell で Azure Virtual Machine を作成して事前構成します。"
	services="virtual-machines"
	documentationCenter=""
	authors="cynthn"
	manager="timlt"
	editor=""
	tags="azure-resource-manager"/>

<tags
	ms.service="virtual-machines"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="07/22/2015"
	ms.author="cynthn"/>

# リソース マネージャーと Azure PowerShell を使用して、Windows 仮想マシンを作成し、事前構成する

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-include.md)]この記事では、リソース マネージャーのデプロイメント モデルを使用したリソースの作成について説明します。また、[クラシック デプロイ モデル](virtual-machines-ps-create-preconfigure-windows-vms.md)を使用してリソースを作成することもできます。

ここでは、リソース マネージャー デプロイ モデルで Azure Virtual Machine を作成および事前構成する一連の Azure PowerShell コマンドを構築する手順について説明します。この基本的なプロセスを利用すると、簡単な新しい Windows ベースの仮想マシン向けにコマンド セットを作成し、既存のデプロイを拡張できるようになります。また、カスタムの開発環境、テスト環境、IT プロ環境を簡単に構築する複数のコマンド セットを作成することもできます。

これらの手順では、空白に記入する方式に従って Azure PowerShell コマンド セットを作成します。この方法は、PowerShell を初めて使う場合や、構成を正しく行うためにどの値を指定するとよいかを知りたい場合に役立ちます。PowerShell に慣れているユーザーは、コマンドの変数を独自の値で置き換えることができます ("$" で始まる行)。

## 手順 1. Azure PowerShell をインストールする

また、Azure PowerShell Version 0.9.0 以降も必要です。Azure PowerShell をまだインストールおよび構成していない場合は、[こちら](../powershell-install-configure.md)で手順を参照してください。

インストールした Azure PowerShell のバージョンは、Azure PowerShell プロンプトで次のコマンドを実行して確認できます。

	Get-Module azure | format-table version

たとえば次のようになります。

	Version
	-------
	0.9.0

バージョン 0.9.0 以降でない場合は、コントロール パネルの [プログラムと機能] を使用して Azure PowerShell を削除してから、最新バージョンをインストールする必要があります。詳細については、[Azure PowerShell のインストールと構成の方法](../powershell-install-configure.md)に関するページを参照してください。

## 手順2. サブスクリプションを設定する

まず Azure PowerShell プロンプトを実行します。

次に、Azure PowerShell プロンプトでこれらのコマンドを実行して、Azure サブスクリプションを設定します。引用符内のすべての文字 (< and > を含む) を、正しい名前に置き換えます。

	$subscr="<subscription name>"
	Select-AzureSubscription -SubscriptionName $subscr –Current

このコマンドの表示から、正しいサブスクリプション名を取得できます。

	Get-AzureSubscription | Sort SubscriptionName | Select SubscriptionName

次に、Azure PowerShell をリソース マネージャー モードに切り替えます。

	Switch-AzureMode AzureResourceManager

## 手順 3. 必要なリソースを作成する

リソース マネージャー ベースの仮想マシンには、リソース グループが必要です。必要に応じて、次のコマンドを実行して新しい仮想マシンの新しいリソース グループを作成します。引用符内のすべての文字 (< and > を含む) を、正しい名前に置き換えます。

	$rgName="<resource group name>"
	$locName="<location name, such as West US>"
	New-AzureResourceGroup -Name $rgName -Location $locName

一意のリソース グループ名を確認するには、次のコマンドを実行して、既存のリソース グループの一覧を取得します。

	Get-AzureResourceGroup | Sort ResourceGroupName | Select ResourceGroupName

リソース マネージャー ベースの仮想マシンを作成できる Azure の場所一覧を取得するには、次のコマンドを実行します。

	$loc=Get-AzureLocation | where { $_.Name –eq "Microsoft.Compute/virtualMachines" }
	$loc.Locations

リソース マネージャー ベースの仮想マシンには、リソース マネージャー ベースのストレージ アカウントが必要です。必要に応じて、次のコマンドを実行して新しい仮想マシンの新しいストレージ アカウントを作成します。

	$rgName="<resource group name>"
	$locName="<location name, such as West US>"
	$saName="<storage account name>"
	$saType="<storage account type, specify one: Standard_LRS, Standard_GRS, Standard_RAGRS, or Premium_LRS>"
	New-AzureStorageAccount -Name $saName -ResourceGroupName $rgName –Type $saType -Location $locName

小文字のアルファベットと数字のみが含まれているストレージ アカウントのグローバルに一意の名前を選択する必要があります。既存のストレージ アカウントの一覧を取得するには、次のコマンドを実行します。

	Get-AzureStorageAccount | Sort Name | Select Name

選択したストレージ アカウント名がグローバルに一意かどうかをテストするには、PowerShell の Azure サービス管理モードで **Test-AzureName** コマンドを実行する必要があります。次のコマンドを実行します。

	Switch-AzureMode AzureServiceManagement
	Test-AzureName -Storage <Proposed storage account name>

Test-AzureName コマンドで "False" と表示される場合、指定した名前は一意です。一意の名前だと判断できたら、次のコマンドで Azure PowerShell をリソース マネージャー モードに戻ります。

	Switch-AzureMode AzureResourceManager

リソース マネージャー ベースの仮想マシンには、パブリック ドメイン名ラベルを使用できます。このラベルには、アルファベット、数字、およびハイフンのみ使用できます。フィールドの先頭と末尾の文字は、文字または数字としてください。

選択したドメイン名のラベルがグローバルに一意かどうかをテストするには、次のコマンドを実行します。

	$domName="<domain name label to test>"
	$loc="<short name of an Azure location, for example, for West US, the short name is westus>"
	Test-AzureDnsAvailability -DomainQualifiedName $domName -Location $loc

DNSNameAvailability が"True"の場合、指定の名前はグローバルに一意です。

>[AZURE.NOTE]Test-AzureDnsAvailability コマンドレットは、バージョン 0.9.5 より前のバージョンの Azure PowerShell では Get-AzureCheckDnsAvailability という名前でした。バージョン 0.9.4 以前を使用している場合は、上記のコマンドの Test-AzureDnsAvailability を Get-AzureCheckDnsAvailability に置き換えてください。

リソース マネージャー ベースの仮想マシンは、リソース マネージャー ベースの可用性セットに配置できます。必要に応じて、次のコマンドを実行して新しい仮想マシンの新しい可用性セットを作成します。

	$avName="<availability set name>"
	$rgName="<resource group name>"
	$locName="<location name, such as West US>"
	New-AzureAvailabilitySet –Name $avName –ResourceGroupName $rgName -Location $locName

既存の可用性セットの一覧を取得するには、次のコマンドを実行します。

	Get-AzureAvailabilitySet –ResourceGroupName $rgName | Sort Name | Select Name

リソース マネージャーを基盤とする仮想マシンは、インターネットから入ってくるトラフィックを許可し、負荷を分散して配置するように受信 NAT ルールで構成できます。いずれの場合でも、ロード バランサーのインスタンスとその他の設定を指定する必要があります。詳細については、「[Azure リソース マネージャーを使用したロード バランサーの作成](../load-balancer/load-balancer-arm-powershell.md)」を参照してください。

リソース マネージャー ベースの仮想マシンには、リソース マネージャー ベースの仮想ネットワークが必要です。必要に応じて、新しい仮想マシンの少なくとも 1 つのサブネットを含む、新しいリソース マネージャー ベースの仮想ネットワークを作成します。次に、frontendSubnet と backendSubnet という 2 つのサブネットを含む新しい仮想ネットワークの例を示します。

	$rgName="LOBServers"
	$locName="West US"
	$frontendSubnet=New-AzureVirtualNetworkSubnetConfig -Name frontendSubnet -AddressPrefix 10.0.1.0/24
	$backendSubnet=New-AzureVirtualNetworkSubnetConfig -Name backendSubnet -AddressPrefix 10.0.2.0/24
	New-AzurevirtualNetwork -Name TestNet -ResourceGroupName $rgName -Location $locName -AddressPrefix 10.0.0.0/16 -Subnet $frontendSubnet,$backendSubnet

既存の仮想ネットワークの一覧を取得するには、次のコマンドを実行します。

	$rgName="<resource group name>"
	Get-AzureVirtualNetwork -ResourceGroupName $rgName | Sort Name | Select Name

## 手順 4. コマンド セットを構築する

好みのテキスト エディターまたは PowerShell Integrated Scripting Environment (ISE) の新規インスタンスを開き、コマンド セットの先頭に次の行をコピーします。この新しい仮想マシンのリソース グループ名、Azure の場所、ストレージ アカウントを指定します。引用符内のすべての文字 (< and > を含む) を、正しい名前に置き換えます。

	Switch-AzureMode AzureResourceManager
	$rgName="<resource group name>"
	$locName="<Azure location, such as West US>"
	$saName="<storage account name>"

リソース マネージャー ベースの仮想ネットワーク名と、仮想ネットワーク内のサブネットのインデックス番号を指定する必要があります。仮想ネットワークのサブネットの一覧を取得するには、次のコマンドを実行します。

	$rgName="<resource group name>"
	$vnetName="<virtual network name>"
	Get-AzureVirtualNetwork -Name $vnetName -ResourceGroupName $rgName | Select Subnets

サブネット インデックスは、このコマンドの出力に表示されるサブネットの番号です。左から右の順に、0から連続して番号が付けられます。

例:

	PS C:\> Get-AzureVirtualNetwork -Name TestNet -ResourceGroupName LOBServers | Select Subnets

	Subnets
	-------
	{frontendSubnet, backendSubnet}

frontendSubnet のサブネット インデックスは 0 です。backendSubnet のサブネット インデックスは 1 です。

コマンド セットに次の行をコピーし、既存の仮想ネットワーク名と、仮想マシンのサブネットのインデックスを指定します。

	$vnetName="<name of an existing virtual network>"
	$subnetIndex=<index of the subnet on which to create the NIC for the virtual machine>
	$vnet=Get-AzurevirtualNetwork -Name $vnetName -ResourceGroupName $rgName

次に、ネットワーク インターフェイス カード (NIC) を作成します。次のオプションのいずれかをコマンド セットにコピーし、必要な情報を入力します。

### オプション 1: NIC 名を指定し、パブリック IP アドレスを割り当てます。

コマンド セットに次の行をコピーし、NIC の名前を指定します。

	$nicName="<name of the NIC of the VM>"
	$pip = New-AzurePublicIpAddress -Name $nicName -ResourceGroupName $rgName -Location $locName -AllocationMethod Dynamic
	$nic = New-AzureNetworkInterface -Name $nicName -ResourceGroupName $rgName -Location $locName -SubnetId $vnet.Subnets[$subnetIndex].Id -PublicIpAddressId $pip.Id

### オプション 2: NIC 名と DNS ドメイン名ラベルを指定します。

コマンド セットに次の行をコピーし、NIC の名前とグローバルに一意なドメイン名ラベルを指定します。Azure PowerShell のサービス管理モードで仮想マシンを作成すると、次の手順が自動的に実行されます。

	$nicName="<name of the NIC of the VM>"
	$domName="<domain name label>"
	$pip = New-AzurePublicIpAddress -Name $nicName -ResourceGroupName $rgName -DomainNameLabel $domName -Location $locName -AllocationMethod Dynamic
	$nic = New-AzureNetworkInterface -Name $nicName -ResourceGroupName $rgName -Location $locName -SubnetId $vnet.Subnets[$subnetIndex].Id -PublicIpAddressId $pip.Id

### オプション 3: NIC 名を指定し、静的なプライベート IP アドレスを割り当てます。

コマンド セットに次の行をコピーし、NIC の名前を指定します。

	$nicName="<name of the NIC of the VM>"
	$staticIP="<available static IP address on the subnet>"
	$pip = New-AzurePublicIpAddress -Name $nicName -ResourceGroupName $rgName -Location $locName -AllocationMethod Dynamic
	$nic = New-AzureNetworkInterface -Name $nicName -ResourceGroupName $rgName -Location $locName -SubnetId $vnet.Subnets[$subnetIndex].Id -PublicIpAddressId $pip.Id -PrivateIpAddress $staticIP

### オプション 4: NIC 名と受信 NAT ルールのロード バランサーのインスタンスを指定します。

NIC を作成し、受信 NAT ルールのロード バランサーのインスタンスに追加するには、次が必要になります。

- 仮想マシンに転送するトラフィックに対して受信 NAT ルールを持つ、以前に作成されたロード バランサーの名前。
- NIC に割り当てるロード バランサーのインスタンスのバック エンド アドレス プールのインデックス数。
- NIC に割り当てる受信 NAT ルールのインデックス番号。

受信 NAT ルールでロード バランサーのインスタンスを作成する方法については、「[Azure リソース マネージャーを使用したロード バランサーの作成](../load-balancer/load-balancer-arm-powershell.md)」を参照してください。

コマンド セットに次の行をコピーし、必要な名前とインデックス番号を指定します。

	$nicName="<name of the NIC of the VM>"
	$lbName="<name of the load balancer instance>"
	$bePoolIndex=<index of the back end pool, starting at 0>
	$natRuleIndex=<index of the inbound NAT rule, starting at 0>
	$lb=Get-AzureLoadBalancer -Name $lbName -ResourceGroupName $rgName
	$nic=New-AzureNetworkInterface -Name $nicName -ResourceGroupName $rgName -Location $locName -Subnet $vnet.Subnets[$subnetIndex].Id -LoadBalancerBackendAddressPool $lb.BackendAddressPools[$bePoolIndex] -LoadBalancerInboundNatRule $lb.InboundNatRules[$natRuleIndex]

$NicName 文字列はリソース グループで一意にする必要があります。最良事例は、「LOB07-NIC」のように文字列に仮想マシン名を組み込むことです。

### オプション 5: NIC 名と負荷分散セットのロード バランサーのインスタンスを指定します。

NIC を作成し、負荷分散セットのロード バランサーのインスタンスに追加するには、次が必要になります。

- 負荷分散トラフィックのルールを持つ、以前に作成されたロード バランサーの名前。
- NIC に割り当てるロード バランサーのインスタンスのバック エンド アドレス プールのインデックス数。

負荷分散トラフィックのルールを持つロード バランサーのインスタンスを作成する方法については、「[Azure リソース マネージャーを使用したロード バランサーの作成](../load-balancer/load-balancer-arm-powershell.md)」を参照してください。

コマンド セットに次の行をコピーし、必要な名前とインデックス番号を指定します。

	$nicName="<name of the NIC of the VM>"
	$lbName="<name of the load balancer instance>"
	$bePoolIndex=<index of the back end pool, starting at 0>
	$lb=Get-AzureLoadBalancer -Name $lbName -ResourceGroupName $rgName
	$nic=New-AzureNetworkInterface -Name $nicName -ResourceGroupName $rgName -Location $locName -Subnet $vnet.Subnets[$subnetIndex].Id -LoadBalancerBackendAddressPool $lb.BackendAddressPools[$bePoolIndex]

次に、ローカル VM オブジェクトを作成し、必要に応じて可用性セットに追加します。次の 2 つのオプションのいずれかをコマンド セットをコピーし、名前、サイズ、および可用性セット名を入力します。

オプション 1. 仮想マシンの名前とサイズを指定します。

	$vmName="<VM name>"
	$vmSize="<VM size string>"
	$vm=New-AzureVMConfig -VMName $vmName -VMSize $vmSize

オプション 1 の VM サイズ文字列の有効な値を確認するには、次のコマンドを実行します。

	$locName="<Azure location of your resource group>"
	Get-AzureVMSize -Location $locName | Select Name

オプション 2. 仮想マシンの名前とサイズを指定し、可用性セットに追加します。

	$vmName="<VM name>"
	$vmSize="<VM size string>"
	$avName="<availability set name>"
	$avSet=Get-AzureAvailabilitySet –Name $avName –ResourceGroupName $rgName
	$vm=New-AzureVMConfig -VMName $vmName -VMSize $vmSize -AvailabilitySetId $avset.Id

オプション 2 の VM サイズ文字列の有効な値を確認するには、次のコマンドを実行します。

	$rgName="<resource group name>"
	$avName="<availability set name>"
	Get-AzureVMSize -ResourceGroupName $rgName -AvailabilitySetName $avName | Select Name

> [AZURE.NOTE]現在、リソース マネージャーでは、作成時にのみ仮想マシンを可用性セットに追加できます。

VM にデータ ディスクを追加するには、こませに次の行をコピーし、ディスク設定を指定します。

	$diskSize=<size of the disk in GB>
	$diskLabel="<the label on the disk>"
	$diskName="<name identifier for the disk in Azure storage, such as 21050529-DISK02>"
	$storageAcc=Get-AzureStorageAccount -ResourceGroupName $rgName -Name $saName
	$vhdURI=$storageAcc.PrimaryEndpoints.Blob.ToString() + "vhds/" + $vmName + $diskName  + ".vhd"
	Add-AzureVMDataDisk -VM $vm -Name $diskLabel -DiskSizeInGB $diskSize -VhdUri $vhdURI  -CreateOption empty

次に、仮想マシンのイメージの発行元、プラン、提供、および SKU を判断する必要があります。次の表は、一般的に使用される Windows ベースのイメージの一覧です。

|発行元の名前 | プラン名 | SKU 名
|:---------------------------------|:-------------------------------------------|:---------------------------------|
|MicrosoftWindowsServer | WindowsServer | 2008-R2-SP1 |
|MicrosoftWindowsServer | WindowsServer | 2012-Datacenter |
|MicrosoftWindowsServer | WindowsServer | 2012-R2-Datacenter |
|MicrosoftDynamicsNAV | DynamicsNAV | 2015 |
|MicrosoftSharePoint | MicrosoftSharePointServer | 2013 |
|MicrosoftSQLServer | SQL2014-WS2012R2 | Enterprise-Optimized-for-DW |
|MicrosoftSQLServer | SQL2014-WS2012R2 | Enterprise-Optimized-for-OLTP |
|MicrosoftWindowsServerEssentials | WindowsServerEssentials | WindowsServerEssentials |
|MicrosoftWindowsServerHPCPack | WindowsServerHPCPack | 2012R2 |

必要な仮想マシン イメージが掲載されていない場合は、[こちら](resource-groups-vm-searching.md#powershell)の手順に従って発行元、プラン、および SKU 名を判断します。

コマンド セットに次のコマンドをコピーし、発行元、プラン、および SKU 名を入力します。

	$pubName="<Image publisher name>"
	$offerName="<Image offer name>"
	$skuName="<Image SKU name>"
	$cred=Get-Credential -Message "Type the name and password of the local administrator account."
	$vm=Set-AzureVMOperatingSystem -VM $vm -Windows -ComputerName $vmName -Credential $cred -ProvisionVMAgent -EnableAutoUpdate
	$vm=Set-AzureVMSourceImage -VM $vm -PublisherName $pubName -Offer $offerName -Skus $skuName -Version "latest"
	$vm=Add-AzureVMNetworkInterface -VM $vm -Id $nic.Id

最後に、コマンド セットに次のコマンドをコピーし、VM のオペレーティング システム ディスクの名前の識別子を入力します。

	$diskName="<name identifier for the disk in Azure storage, such as OSDisk>"
	$storageAcc=Get-AzureStorageAccount -ResourceGroupName $rgName -Name $saName
	$osDiskUri=$storageAcc.PrimaryEndpoints.Blob.ToString() + "vhds/" + $diskName  + ".vhd"
	$vm=Set-AzureVMOSDisk -VM $vm -Name $diskName -VhdUri $osDiskUri -CreateOption fromImage
	New-AzureVM -ResourceGroupName $rgName -Location $locName -VM $vm

## 手順 5. コマンド セットを実行する

手順 4. で、テキスト エディターまたは PowerShell ISE を使用して構築した、複数のコマンド ブロックで構築されている Azure PowerShell コマンド セットを確認します。必要なすべての変数が指定され、それらの値がすべて正しいことを確認します。さらに、文字 < and > がすべて削除されていることも確認します。

テキスト エディターでコマンドを構築した場合は、コマンド セットをクリップボードにコピーしてから、開いている Azure PowerShell プロンプトを右クリックします。この操作により、コマンド セットが一連の PowerShell コマンドとして実行され、Azure 仮想マシンが作成されます。または、Azure PowerShell ISE のコマンド セットを実行します。

この仮想マシンを作成する場合、またはこれに似た仮想マシンをもう一度作成する場合は、PowerShell スクリプト ファイル (*.ps1) としてこのコマンド セットを保存することができます。

## 例

次のような、Web ベースの基幹業務ワークロード用の仮想マシンを追加する PowerShell コマンド セットが必要です。

- 既存の LOBServers リソース グループ内にある
- Windows Server 2012 R2 Datacenter イメージを使用する
- 名前は LOB07 で、既存の WEB\_AS 可用性セット内にある
- 既存の AZDatacenter 仮想ネットワークの FrontEnd サブネット (サブネット インデックス 0) 内のパブリック IP アドレスを持つ NIC がある
- 200 GB の追加データ ディスク容量

手順 4. で説明したプロセスに基づいて、対応する Azure PowerShell コマンド セットを実行して、この仮想マシンを作成します。

	# Switch to the Resource Manager mode
	Switch-AzureMode AzureResourceManager

	# Set values for existing resource group and storage account names
	$rgName="LOBServers"
	$locName="West US"
	$saName="contosolobserverssa"

	# Set the existing virtual network and subnet index
	$vnetName="AZDatacenter"
	$subnetIndex=0
	$vnet=Get-AzurevirtualNetwork -Name $vnetName -ResourceGroupName $rgName

	# Create the NIC
	$nicName="LOB07-NIC"
	$domName="contoso-vm-lob07"
	$pip=New-AzurePublicIpAddress -Name $nicName -ResourceGroupName $rgName -DomainNameLabel $domName -Location $locName -AllocationMethod Dynamic
	$nic=New-AzureNetworkInterface -Name $nicName -ResourceGroupName $rgName -Location $locName -SubnetId $vnet.Subnets[$subnetIndex].Id -PublicIpAddressId $pip.Id

	# Specify the name, size, and existing availability set
	$vmName="LOB07"
	$vmSize="Standard_A3"
	$avName="WEB_AS"
	$avSet=Get-AzureAvailabilitySet –Name $avName –ResourceGroupName $rgName
	$vm=New-AzureVMConfig -VMName $vmName -VMSize $vmSize -AvailabilitySetId $avset.Id

	# Add a 200 GB additional data disk
	$diskSize=200
	$diskLabel="APPStorage"
	$diskName="21050529-DISK02"
	$storageAcc=Get-AzureStorageAccount -ResourceGroupName $rgName -Name $saName
	$vhdURI=$storageAcc.PrimaryEndpoints.Blob.ToString() + "vhds/" + $vmName + $diskName  + ".vhd"
	Add-AzureVMDataDisk -VM $vm -Name $diskLabel -DiskSizeInGB $diskSize -VhdUri $vhdURI -CreateOption empty

	# Specify the image and local administrator account, and then add the NIC
	$pubName="MicrosoftWindowsServer"
	$offerName="WindowsServer"
	$skuName="2012-R2-Datacenter"
	$cred=Get-Credential -Message "Type the name and password of the local administrator account."
	$vm=Set-AzureVMOperatingSystem -VM $vm -Windows -ComputerName $vmName -Credential $cred -ProvisionVMAgent -EnableAutoUpdate
	$vm=Set-AzureVMSourceImage -VM $vm -PublisherName $pubName -Offer $offerName -Skus $skuName -Version "latest"
	$vm=Add-AzureVMNetworkInterface -VM $vm -Id $nic.Id

	# Specify the OS disk name and create the VM
	$diskName="OSDisk"
	$storageAcc=Get-AzureStorageAccount -ResourceGroupName $rgName -Name $saName
	$osDiskUri=$storageAcc.PrimaryEndpoints.Blob.ToString() + "vhds/" + $vmName + $diskName  + ".vhd"
	$vm=Set-AzureVMOSDisk -VM $vm -Name $diskName -VhdUri $osDiskUri -CreateOption fromImage
	New-AzureVM -ResourceGroupName $rgName -Location $locName -VM $vm

## その他のリソース

[Azure リソース マネージャーにおける Azure Compute、ネットワーク、ストレージ プロバイダー](virtual-machines-azurerm-versus-azuresm.md)

[Azure リソース マネージャーの概要](../resource-group-overview.md)

[リソース マネージャー テンプレートと PowerShell を使用した Azure Virtual Machines のデプロイと管理](virtual-machines-deploy-rmtemplates-powershell.md)

[リソース マネージャー テンプレートと PowerShell で Windows 仮想マシンを作成する](virtual-machines-create-windows-powershell-resource-manager-template-simple)

[Azure PowerShell のインストールおよび構成方法](../install-configure-powershell.md)

<!---HONumber=Sept15_HO4-->