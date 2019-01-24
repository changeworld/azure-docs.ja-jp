---
title: Azure PowerShell を使用して SQL Server VM をプロビジョニングするためのガイド | Microsoft Docs
description: SQL Server 仮想マシン ギャラリー のイメージを使用して Azure VM を作成するための手順と PowerShell コマンドを提供します。
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
manager: craigg
editor: ''
tags: azure-resource-manager
ms.assetid: 98d50dd8-48ad-444f-9031-5378d8270d7b
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 12/21/2018
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: 60f04ac857079a1019ca744f3b26b0d05ae6ca6c
ms.sourcegitcommit: 9999fe6e2400cf734f79e2edd6f96a8adf118d92
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/22/2019
ms.locfileid: "54426932"
---
# <a name="how-to-provision-sql-server-virtual-machines-with-azure-powershell"></a>Azure PowerShell を使用して SQL Server 仮想マシンをプロビジョニングする方法

このガイドでは、Azure PowerShell を使用して Windows SQL Server VM を作成するオプションについて説明します。 より多くの規定値を使用し、効率化された Azure PowerShell の例については、[SQL VM の Azure PowerShell クイック スタート](quickstart-sql-vm-create-powershell.md)を参照してください。

Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) を作成してください。

この記事では、Azure PowerShell モジュール バージョン 3.6 以降が必要です。 バージョンを確認するには、`Get-Module -ListAvailable AzureRM` を実行します。 インストールまたはアップグレードする必要がある場合は、[Azure PowerShell モジュールのインストール](/powershell/azure/azurerm/install-azurerm-ps)に関するページを参照してください。

## <a name="configure-your-subscription"></a>サブスクリプションの構成

1. PowerShell を開いて **Connect-AzureRmAccount** コマンドを実行し、Azure アカウントへのアクセスを確立します。

   ```PowerShell
   Connect-AzureRmAccount
   ```

1. 資格情報を入力するための画面が表示されます。 Azure ポータルへのサインインに使用しているものと同じ電子メールとパスワードを使用します。

## <a name="define-image-variables"></a>イメージの変数の定義
値を再利用してスクリプトの作成を簡略化するには、まず、変数の数を定義します。 パラメーターの値は適宜変更してください。ただし、指定した値に変更を加える際は、名前の長さや特殊文字に関連した制限に注意してください。

### <a name="location-and-resource-group"></a>場所とリソース グループ
データのリージョンと、他の VM リソースの作成先となるリソース グループを定義します。

必要に応じて変更してから、以下のコマンドレットを実行し、これらの変数を初期化します。

```PowerShell
$Location = "SouthCentralUS"
$ResourceGroupName = "sqlvm2"
```

### <a name="storage-properties"></a>ストレージのプロパティ
ストレージ アカウントと、仮想マシンで使用するストレージの種類を定義します。

必要に応じて変更してから、以下のコマンドレットを実行し、これらの変数を初期化します。 運用環境のワークロードには [Premium Storage](../premium-storage.md) を使用することをお勧めします。

```PowerShell
$StorageName = $ResourceGroupName + "storage"
$StorageSku = "Premium_LRS"
```

### <a name="network-properties"></a>Network properties
仮想マシンのネットワークで使用するプロパティを定義します。 

- Linux
- TCP/IP の割り当て方法
- 仮想ネットワーク名
- 仮想サブネット名
- 仮想ネットワークの IP アドレスの範囲
- サブネットの IP アドレスの範囲
- パブリック ドメイン名のラベル

必要に応じて変更してから、このコマンドレットを実行し、これらの変数を初期化します。

```PowerShell
$InterfaceName = $ResourceGroupName + "ServerInterface"
$NsgName = $ResourceGroupName + "nsg"
$TCPIPAllocationMethod = "Dynamic"
$VNetName = $ResourceGroupName + "VNet"
$SubnetName = "Default"
$VNetAddressPrefix = "10.0.0.0/16"
$VNetSubnetAddressPrefix = "10.0.0.0/24"
$DomainName = $ResourceGroupName
```

### <a name="virtual-machine-properties"></a>仮想マシン プロパティ
仮想マシン名、コンピューター名、仮想マシン サイズ、仮想マシンのオペレーティング システム ディスク名を定義します。

必要に応じて変更してから、このコマンドレットを実行し、これらの変数を初期化します。

```PowerShell
$VMName = $ResourceGroupName + "VM"
$ComputerName = $ResourceGroupName + "Server"
$VMSize = "Standard_DS13"
$OSDiskName = $VMName + "OSDisk"
```

### <a name="choose-a-sql-server-image"></a>SQL Server イメージを選択します。

仮想マシンに使用するイメージは、以下の変数を使用して定義します。 

1. まず、`Get-AzureRmVMImageOffer` コマンドを使用して、提供されているすべての SQL Server イメージを一覧表示します。 このコマンドにより、Azure Portal で提供されている現在のイメージが一覧表示されます。また、PowerShell でのみインストールできる古いイメージも一覧表示されます。

   ```PowerShell
   Get-AzureRmVMImageOffer -Location $Location -Publisher 'MicrosoftSQLServer'
   ```

1. このチュートリアルでは、次の変数を使用して、Windows Server 2016 上の SQL Server 2017 を指定します。

   ```PowerShell
   $OfferName = "SQL2017-WS2016"
   $PublisherName = "MicrosoftSQLServer"
   $Version = "latest"
   ```

1. 次に、お使いのサービスで使用可能なエディションを一覧表示します。

   ```PowerShell
   Get-AzureRmVMImageSku -Location $Location -Publisher 'MicrosoftSQLServer' -Offer $OfferName | Select Skus
   ```

1. このチュートリアルでは、SQL Server 2017 Developer エディション (**SQLDEV**) を使用します。 Developer エディションはテストと開発のために無料でライセンスされます。VM を実行するコストのみを支払います。

   ```PowerShell
   $Sku = "SQLDEV"
   ```

## <a name="create-a-resource-group"></a>リソース グループの作成
Resource Manager デプロイ モデルで最初に作成するオブジェクトはリソース グループです。 [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup) コマンドレットを使用して、Azure リソース グループとそのリソースを作成します。 前に初期化した、リソース グループの名前と場所の変数を指定します。

このコマンドレットを実行し、新しいリソース グループを作成します。

```PowerShell
New-AzureRmResourceGroup -Name $ResourceGroupName -Location $Location
```

## <a name="create-a-storage-account"></a>ストレージ アカウントの作成
仮想マシンには、オペレーティング システム ディスク用と SQL Server (データおよびログ ファイル) 用のストレージ リソースが必要となります。 単純化するために、両方の用途を兼ねた単一のディスクを作成します。 後から [Add-Azure Disk](/powershell/module/servicemanagement/azure/add-azuredisk) コマンドレットを使用して追加のディスクを接続すると、SQL Server のデータ ファイルとログ ファイルを専用のディスクに格納できます。 [New-AzureRmStorageAccount](/powershell/module/azurerm.storage/new-azurermstorageaccount) コマンドレットを使用して、新しいリソース グループ内に標準ストレージ アカウントを作成します。 前に初期化した、ストレージ アカウント名、ストレージ SKU 名、および場所の変数を指定します。

このコマンドレットを実行し、新しいストレージ アカウントを作成します。

```PowerShell
$StorageAccount = New-AzureRmStorageAccount -ResourceGroupName $ResourceGroupName `
   -Name $StorageName -SkuName $StorageSku `
   -Kind "Storage" -Location $Location
```

> [!TIP]
> ストレージ アカウントを作成するには数分かかることがあります。

## <a name="create-network-resources"></a>ネットワーク リソースを作成する
ネットワーク接続を確保するためには、仮想マシンにさまざまなネットワーク リソースが必要となります。

* 個々の仮想マシンに仮想ネットワークが必要となります。
* 仮想ネットワークには少なくとも 1 つのサブネットが定義されている必要があります。
* パブリックまたはプライベートの IP アドレスを持つネットワーク インターフェイスが定義されている必要があります。

### <a name="create-a-virtual-network-subnet-configuration"></a>仮想ネットワークのサブネット構成の作成
まず、仮想ネットワークのサブネット構成を作成します。 このチュートリアルでは、[New-AzureRmVirtualNetworkSubnetConfig](/powershell/module/azurerm.network/new-azurermvirtualnetworksubnetconfig) コマンドレットを使用して既定のサブネットを作成します。 前に初期化した、サブネット名とアドレス プレフィックスの変数を指定します。

> [!NOTE]
> サブネット名とアドレス プレフィックス以外のプロパティもこのコマンドレットで定義できますが、このチュートリアルの範囲を超えるため、ここでは取り上げません。

このコマンドレットを実行して、仮想サブネットの構成を作成します。

```PowerShell
$SubnetConfig = New-AzureRmVirtualNetworkSubnetConfig -Name $SubnetName -AddressPrefix $VNetSubnetAddressPrefix
```

### <a name="create-a-virtual-network"></a>仮想ネットワークの作成
次に、[New-AzureRmVirtualNetwork](/powershell/module/azurerm.network/new-azurermvirtualnetwork) コマンドレットを使用して、新しいリソース グループ内に仮想ネットワークを作成します。 前に初期化した、名前、場所、およびアドレス プレフィックスの変数を指定します。 前の手順で定義したサブネット構成を使用します。

このコマンドレットを実行して、仮想ネットワークを作成します。

```PowerShell
$VNet = New-AzureRmVirtualNetwork -Name $VNetName `
   -ResourceGroupName $ResourceGroupName -Location $Location `
   -AddressPrefix $VNetAddressPrefix -Subnet $SubnetConfig
```

### <a name="create-the-public-ip-address"></a>パブリック IP アドレスの作成
これで仮想ネットワークが定義されたので、仮想マシンへの接続用の IP アドレスを構成する必要があります。 このチュートリアルでは、インターネット接続をサポートするために、動的 IP アドレス指定を使用してパブリック IP アドレスを作成します。 [New-AzureRmPublicIpAddress](/powershell/module/azurerm.network/new-azurermpublicipaddress) コマンドレットを使用して、新しいリソース グループ内にパブリック IP アドレスを作成します。 前に初期化した、名前、場所、割り当て方法、および DNS ドメイン名ラベルの変数を指定します。

> [!NOTE]
> パブリック IP アドレスのプロパティもこのコマンドレットで定義できますが、このチュートリアルの範囲を超えるため、ここでは取り上げません。 またプライベート アドレスや静的アドレスを作成することもできますが、このチュートリアルの範囲を超えるため、同様に説明を省略します。

このコマンドレットを実行して、パブリック IP アドレスを作成します。

```PowerShell
$PublicIp = New-AzureRmPublicIpAddress -Name $InterfaceName `
   -ResourceGroupName $ResourceGroupName -Location $Location `
   -AllocationMethod $TCPIPAllocationMethod -DomainNameLabel $DomainName
```

### <a name="create-the-network-security-group"></a>ネットワーク セキュリティ グループの作成
VM と SQL Server トラフィックを保護するには、ネットワーク セキュリティ グループを作成します。

1. まず、リモート デスクトップ接続のための RDP 用ネットワーク セキュリティ グループ ルールを作成します。

   ```PowerShell
   $NsgRuleRDP = New-AzureRmNetworkSecurityRuleConfig -Name "RDPRule" -Protocol Tcp `
      -Direction Inbound -Priority 1000 -SourceAddressPrefix * -SourcePortRange * `
      -DestinationAddressPrefix * -DestinationPortRange 3389 -Access Allow
   ```
1. TCP ポート 1433 でトラフィックを許可するネットワーク セキュリティ グループ ルールを構成します。 そうすることで、インターネット経由での SQL Server への接続が可能になります。

   ```PowerShell
   $NsgRuleSQL = New-AzureRmNetworkSecurityRuleConfig -Name "MSSQLRule"  -Protocol Tcp `
      -Direction Inbound -Priority 1001 -SourceAddressPrefix * -SourcePortRange * `
      -DestinationAddressPrefix * -DestinationPortRange 1433 -Access Allow
   ```

1. ネットワーク セキュリティ グループを作成します。

   ```PowerShell
   $Nsg = New-AzureRmNetworkSecurityGroup -ResourceGroupName $ResourceGroupName `
      -Location $Location -Name $NsgName `
      -SecurityRules $NsgRuleRDP,$NsgRuleSQL
   ```

### <a name="create-the-network-interface"></a>ネットワーク インターフェイスの作成
これで、仮想マシンのネットワーク インターフェイスを作成する準備が整いました。 [New-AzureRmNetworkInterface](/powershell/module/azurerm.network/new-azurermnetworkinterface) コマンドレットを使用して、新しいリソース グループ内にネットワーク インターフェイスを作成します。 前に定義した名前、場所、サブネット、およびパブリック IP アドレスを指定します。

このコマンドレットを実行して、ネットワーク インターフェイスを作成します。

```PowerShell
$Interface = New-AzureRmNetworkInterface -Name $InterfaceName `
   -ResourceGroupName $ResourceGroupName -Location $Location `
   -SubnetId $VNet.Subnets[0].Id -PublicIpAddressId $PublicIp.Id `
   -NetworkSecurityGroupId $Nsg.Id
```

## <a name="configure-a-vm-object"></a>VM オブジェクトの構成
これでストレージとネットワーク リソースが定義されたので、仮想マシンのコンピューティング リソースを定義する準備が整いました。

- 仮想マシンのサイズと、さまざまなオペレーティング システムのプロパティを指定します。
- 前に作成したネットワーク インターフェイスを指定します。
- BLOB ストレージを定義します。
- オペレーティング システム ディスクを指定します。

### <a name="create-the-vm-object"></a>VM オブジェクトの作成
まず、仮想マシンのサイズを指定します。 このチュートリアルでは DS13 を指定します。 [New-AzureRmVMConfig](/powershell/module/azurerm.compute/new-azurermvmconfig) コマンドレットを使用して、構成可能な仮想マシン オブジェクトを作成します。 前に初期化した、名前とサイズの変数を指定します。

このコマンドレットを実行して、仮想マシン オブジェクトを作成します。

```PowerShell
$VirtualMachine = New-AzureRmVMConfig -VMName $VMName -VMSize $VMSize
```

### <a name="create-a-credential-object-to-hold-the-name-and-password-for-the-local-administrator-credentials"></a>ローカル管理者の資格情報の名前とパスワードを保持する資格情報オブジェクトを作成する
仮想マシンのオペレーティング システムのプロパティを設定する前に、ローカル管理者アカウントの資格情報を、セキュリティで保護された文字列で指定する必要があります。 ここでは、[Get-Credential](https://technet.microsoft.com/library/hh849815.aspx) コマンドレットを使用します。

次のコマンドレットを実行し、PowerShell の資格情報要求ウィンドウで、仮想マシンのローカル管理者アカウントに使用する名前とパスワードを入力します。

```PowerShell
$Credential = Get-Credential -Message "Type the name and password of the local administrator account."
```

### <a name="set-the-operating-system-properties-for-the-virtual-machine"></a>仮想マシンに使用するオペレーティング システムのプロパティ設定
[Set-AzureRmVMOperatingSystem](/powershell/module/azurerm.compute/set-azurermvmoperatingsystem) コマンドレットを使用して仮想マシンのオペレーティング システムのプロパティを設定する準備ができました。

- オペレーティング システムの種類は Windows として設定します。
- [仮想マシン エージェント](../../extensions/agent-windows.md)がインストールされる必要があります。
- このコマンドレットで自動更新を有効にすることを指定します。
- 前に初期化した、仮想マシン名、コンピューター名、および資格情報の変数を指定します。

このコマンドレットを実行し、仮想マシンのオペレーティング システムのプロパティを設定します。

```PowerShell
$VirtualMachine = Set-AzureRmVMOperatingSystem -VM $VirtualMachine `
   -Windows -ComputerName $ComputerName -Credential $Credential `
   -ProvisionVMAgent -EnableAutoUpdate
```

### <a name="add-the-network-interface-to-the-virtual-machine"></a>仮想マシンにネットワーク インターフェイスを追加する
次に、[Add-AzureRmVMNetworkInterface](/powershell/module/azurerm.compute/add-azurermvmnetworkinterface) コマンドレットを使用し、前に定義した変数を使用してネットワーク インターフェイスを追加します。

このコマンドレットを実行し、仮想マシンのネットワーク インターフェイスを設定します。

```PowerShell
$VirtualMachine = Add-AzureRmVMNetworkInterface -VM $VirtualMachine -Id $Interface.Id
```

### <a name="set-the-blob-storage-location-for-the-disk-to-be-used-by-the-virtual-machine"></a>仮想マシンで使用するディスクの Blob Storage の場所を設定する
次に、前に定義した変数を使用して、VM のディスク用の BLOB ストレージの場所を設定します。

このコマンドレットを実行し、BLOB ストレージの場所を設定します。

```PowerShell
$OSDiskUri = $StorageAccount.PrimaryEndpoints.Blob.ToString() + "vhds/" + $OSDiskName + ".vhd"
```

### <a name="set-the-operating-system-disk-properties-for-the-virtual-machine"></a>仮想マシンに使用するオペレーティング システムのディスクのプロパティ設定
次に、[Set-AzureRmVMOSDisk](/powershell/module/azurerm.compute/set-azurermvmosdisk) コマンドレットを使用して、仮想マシンのオペレーティング システム ディスクのプロパティを設定します。 

- 仮想マシンのオペレーティング システムはイメージから取得することを指定します。
- キャッシュは読み取り専用に設定します (同じディスクに SQL Server がインストールされるため)。
- 前に初期化した、VM の名前とオペレーティング システム ディスクの変数を指定します。

このコマンドレットを実行し、仮想マシンのオペレーティング システム ディスクのプロパティを設定します。

```PowerShell
$VirtualMachine = Set-AzureRmVMOSDisk -VM $VirtualMachine -Name `
   $OSDiskName -VhdUri $OSDiskUri -Caching ReadOnly -CreateOption FromImage
```

### <a name="specify-the-platform-image-for-the-virtual-machine"></a>仮想マシンのプラットフォーム イメージを指定する
最後の構成手順は、仮想マシン用のプラットフォーム イメージを指定することです。 このチュートリアルでは、最新の SQL Server 2016 CTP イメージを使用します。 前に定義した変数を指定して [Set-AzureRmVMSourceImage](/powershell/module/azurerm.compute/set-azurermvmsourceimage) コマンドレットを使用し、このイメージを使用します。

このコマンドレットを実行し、仮想マシンのプラットフォーム イメージを指定します。

```PowerShell
$VirtualMachine = Set-AzureRmVMSourceImage -VM $VirtualMachine `
   -PublisherName $PublisherName -Offer $OfferName `
   -Skus $Sku -Version $Version
```

## <a name="create-the-sql-vm"></a>SQL VM の作成
これで構成手順を終えたので、仮想マシンを作成する準備が整いました。 定義済みの変数を使用して [New-AzureRmVM](/powershell/module/azurerm.compute/new-azurermvm) コマンドレットを実行し、仮想マシンを作成します。

> [!TIP]
> VM の作成には数分かかることがあります。

このコマンドレットを実行して仮想マシンを作成します。

```PowerShell
New-AzureRmVM -ResourceGroupName $ResourceGroupName -Location $Location -VM $VirtualMachine
```

これで仮想マシンが作成されました。

> [!NOTE]
> ブート診断に関するエラーが表示される場合、そのエラーは無視できます。 仮想マシンのディスク用に指定したストレージ アカウントは Premium Storage アカウントであるため、ブート診断用に Standard Storage アカウントが作成されることに注意してください。

## <a name="install-the-sql-iaas-agent"></a>SQL Iaas Agent のインストール
SQL Server 仮想マシンでは、[SQL Server IaaS エージェントの拡張機能](virtual-machines-windows-sql-server-agent-extension.md)を使用して自動管理機能をサポートします。 新しい VM にエージェントをインストールするには、VM の作成後に次のコマンドを実行します。


   ```PowerShell
   Set-AzureRmVMSqlServerExtension -ResourceGroupName $ResourceGroupName -VMName $VMName -name "SQLIaasExtension" -version "1.2" -Location $Location
   ```

## <a name="stop-or-remove-a-vm"></a>VM の停止または削除

VM を継続的に実行する必要がない場合は、使用中でないときに停止することで、不要な料金の発生を回避できます。 次のコマンドでは、VM を停止しますが、後から使用できるように残しておきます。

```PowerShell
Stop-AzureRmVM -Name $VMName -ResourceGroupName $ResourceGroupName
```

仮想マシンに関連付けらているすべてのリソースは、**Remove-AzureRmResourceGroup** コマンドを使用して完全に削除することもできます。 そのようにすると仮想マシンも完全に削除されるため、このコマンドは注意して使用してください。

## <a name="example-script"></a>サンプル スクリプト
このチュートリアルで使用した PowerShell スクリプト全体は、次のようになっています。 使用する Azure サブスクリプションは、**Connect-AzureRmAccount** コマンドと **Select-AzureRmSubscription** コマンドで、既に設定してあると想定しています。

```PowerShell
# Variables

## Global
$Location = "SouthCentralUS"
$ResourceGroupName = "sqlvm2"

## Storage
$StorageName = $ResourceGroupName + "storage"
$StorageSku = "Premium_LRS"

## Network
$InterfaceName = $ResourceGroupName + "ServerInterface"
$NsgName = $ResourceGroupName + "nsg"
$VNetName = $ResourceGroupName + "VNet"
$SubnetName = "Default"
$VNetAddressPrefix = "10.0.0.0/16"
$VNetSubnetAddressPrefix = "10.0.0.0/24"
$TCPIPAllocationMethod = "Dynamic"
$DomainName = $ResourceGroupName

##Compute
$VMName = $ResourceGroupName + "VM"
$ComputerName = $ResourceGroupName + "Server"
$VMSize = "Standard_DS13"
$OSDiskName = $VMName + "OSDisk"

##Image
$PublisherName = "MicrosoftSQLServer"
$OfferName = "SQL2017-WS2016"
$Sku = "SQLDEV"
$Version = "latest"

# Resource Group
New-AzureRmResourceGroup -Name $ResourceGroupName -Location $Location

# Storage
$StorageAccount = New-AzureRmStorageAccount -ResourceGroupName $ResourceGroupName -Name $StorageName -SkuName $StorageSku -Kind "Storage" -Location $Location

# Network
$SubnetConfig = New-AzureRmVirtualNetworkSubnetConfig -Name $SubnetName -AddressPrefix $VNetSubnetAddressPrefix
$VNet = New-AzureRmVirtualNetwork -Name $VNetName -ResourceGroupName $ResourceGroupName -Location $Location -AddressPrefix $VNetAddressPrefix -Subnet $SubnetConfig
$PublicIp = New-AzureRmPublicIpAddress -Name $InterfaceName -ResourceGroupName $ResourceGroupName -Location $Location -AllocationMethod $TCPIPAllocationMethod -DomainNameLabel $DomainName
$NsgRuleRDP = New-AzureRmNetworkSecurityRuleConfig -Name "RDPRule" -Protocol Tcp -Direction Inbound -Priority 1000 -SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix * -DestinationPortRange 3389 -Access Allow
$NsgRuleSQL = New-AzureRmNetworkSecurityRuleConfig -Name "MSSQLRule"  -Protocol Tcp -Direction Inbound -Priority 1001 -SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix * -DestinationPortRange 1433 -Access Allow
$Nsg = New-AzureRmNetworkSecurityGroup -ResourceGroupName $ResourceGroupName -Location $Location -Name $NsgName -SecurityRules $NsgRuleRDP,$NsgRuleSQL
$Interface = New-AzureRmNetworkInterface -Name $InterfaceName -ResourceGroupName $ResourceGroupName -Location $Location -SubnetId $VNet.Subnets[0].Id -PublicIpAddressId $PublicIp.Id -NetworkSecurityGroupId $Nsg.Id

# Compute
$VirtualMachine = New-AzureRmVMConfig -VMName $VMName -VMSize $VMSize
$Credential = Get-Credential -Message "Type the name and password of the local administrator account."
$VirtualMachine = Set-AzureRmVMOperatingSystem -VM $VirtualMachine -Windows -ComputerName $ComputerName -Credential $Credential -ProvisionVMAgent -EnableAutoUpdate #-TimeZone = $TimeZone
$VirtualMachine = Add-AzureRmVMNetworkInterface -VM $VirtualMachine -Id $Interface.Id
$OSDiskUri = $StorageAccount.PrimaryEndpoints.Blob.ToString() + "vhds/" + $OSDiskName + ".vhd"
$VirtualMachine = Set-AzureRmVMOSDisk -VM $VirtualMachine -Name $OSDiskName -VhdUri $OSDiskUri -Caching ReadOnly -CreateOption FromImage

# Image
$VirtualMachine = Set-AzureRmVMSourceImage -VM $VirtualMachine -PublisherName $PublisherName -Offer $OfferName -Skus $Sku -Version $Version

# Create the VM in Azure
New-AzureRmVM -ResourceGroupName $ResourceGroupName -Location $Location -VM $VirtualMachine

# Add the SQL IaaS Extension
Set-AzureRmVMSqlServerExtension -ResourceGroupName $ResourceGroupName -VMName $VMName -name "SQLIaasExtension" -version "1.2" -Location $Location
```

## <a name="next-steps"></a>次の手順
これで仮想マシンが作成され、以下のことを実行できます。

- RDP を使用して仮想マシンに接続します
- ポータルで、以下を含む VM の SQL Server 設定を構成します。
   - [ストレージの設定](virtual-machines-windows-sql-server-storage-configuration.md) 
   - [管理タスクの自動化](virtual-machines-windows-sql-server-agent-extension.md)
- [接続を構成します](virtual-machines-windows-sql-connect.md)
- クライアントとアプリケーションを新しい SQL Server インスタンスに接続します

