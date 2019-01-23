---
title: ARM Managed Disk VM にクラシック VM を移行する | Microsoft Docs
description: Resource Manager デプロイ モデルで、1 つの Azure VM をクラシック デプロイ モデルから Managed Disks に移行します。
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 06/15/2017
ms.author: cynthn
ms.openlocfilehash: a662a61d737dbb620d07fa6d114649e70c082796
ms.sourcegitcommit: dede0c5cbb2bd975349b6286c48456cfd270d6e9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/16/2019
ms.locfileid: "54329771"
---
# <a name="migrate-a-classic-vm-to-use-a-managed-disk"></a>クラシック VM を移行して Managed Disks を使用する 


このセクションでは、Resource Manager デプロイ モデルで、既存の Azure VM をクラシック デプロイ モデルから [Managed Disks](managed-disks-overview.md) に移行する方法を説明します。


## <a name="plan-for-the-migration-to-managed-disks"></a>Managed Disks への移行の計画

このセクションでは、VM とディスクの種類に関する最適な決定を行います。


### <a name="location"></a>場所

Managed Disks を使用できる場所を選びます。 Premium Storage でバックアップしている Managed Disks に移行する場合は、そのリージョンで Premium Storage が使用可能であることも確認します。 使用できる場所に関する最新情報については、[リージョン別の Azure サービス](https://azure.microsoft.com/regions/#services)に関するページをご覧ください。

### <a name="vm-sizes"></a>VM サイズ

Premium Storage を使用している Managed Disks に移行する場合は、VM が配置されているリージョンで利用できる Premium Storage 対応サイズに合うように VM のサイズを更新する必要があります。 Premium Storage で対応できる VM サイズをご確認ください。 Azure VM のサイズの仕様は、「 [仮想マシンのサイズ](sizes.md)」に記載されています。
Premium Storage で動作する仮想マシンのパフォーマンス特性を確認し、ワークロードに最適な VM を選択してください。 ディスク トラフィックが流れるのに十分な帯域幅が VM で利用できることを確認します。

### <a name="disk-sizes"></a>ディスク サイズ

**Premium**

VM で使える Premium Storage マネージド ディスクには 7 種類あり、それぞれに特定の IOPS とスループットの制限があります。 VM の Premium ディスクの種類を選ぶ場合は、容量、パフォーマンス、スケーラビリティ、最大負荷に関するアプリケーションのニーズに基づいて、これらの制限を考慮してください。

| Premium ディスクの種類  | P4    | P6    | P10   | P20   | P30   | P40   | P50   | 
|---------------------|-------|-------|-------|-------|-------|-------|-------|
| ディスク サイズ           | 128 GB| 512 GB| 128 GB| 512 GB            | 1024 GB (1 TB)    | 2048 GB (2 TB)    | 4095 GB (4 TB)    | 
| ディスクあたりの IOPS       | 120   | 240   | 500   | 2300              | 5000              | 7500              | 7500              | 
| ディスクあたりのスループット | 25 MB/秒  | 50 MB/秒  | 100 MB/秒 | 150 MB/秒 | 200 MB/秒 | 250 MB/秒 | 250 MB/秒 | 

**Standard**

VM で使用できる Standard ディスクは 7 種類あります。 それぞれ容量は異なりますが、IOPS とスループットの制限は同じです。 アプリケーションの容量のニーズに基づいて Standard マネージド ディスクの種類を選択してください。

| Standard ディスクの種類  | S4               | S6               | S10              | S20              | S30              | S40              | S50              | 
|---------------------|---------------------|---------------------|------------------|------------------|------------------|------------------|------------------| 
| ディスク サイズ           | 30 GB            | 64 GB            | 128 GB           | 512 GB           | 1024 GB (1 TB)   | 2048 GB (2 TB)    | 4095 GB (4 TB)   | 
| ディスクあたりの IOPS       | 500              | 500              | 500              | 500              | 500              | 500             | 500              | 
| ディスクあたりのスループット | 60 MB/秒 | 60 MB/秒 | 60 MB/秒 | 60 MB/秒 | 60 MB/秒 | 60 MB/秒 | 60 MB/秒 | 


### <a name="disk-caching-policy"></a>ディスク キャッシュ ポリシー 

**Premium Managed Disks**

既定では、ディスクのキャッシュ ポリシーは、すべてのPremium データ ディスクに対して「*読み取り専用*」、VM にアタッチされた Premium オペレーティング システム ディスクに対して「*読み取り/書き込み*」です。 アプリケーションの IO パフォーマンスを最適化するには、この構成をお勧めします。 書き込み量の多いディスクや書き込み専用のディスク (SQL Server ログ ファイルなど) の場合は、ディスク キャッシュを無効にすることで、アプリケーションのパフォーマンスを向上できる場合があります。

### <a name="pricing"></a>価格

[Managed Disks の価格](https://azure.microsoft.com/pricing/details/managed-disks/)をご確認ください。 管理対象の Premium ディスクの価格は、非管理対象の Premium ディスクと同じです。 一方、管理対象の Standard ディスクの価格は、非管理対象の Standard ディスクと異なります。


## <a name="checklist"></a>チェック リスト

1.  Premium 管理ディスクに移行する場合は、移行先のリージョンでそのディスクが使用できることを確認してください。

2.  使用する新しい VM シリーズを決定します。 Premium 管理ディスクに移行する場合は、Premium Storage に対応したものにする必要があります。

3.  使用する正確な VM のサイズを、移行先のリージョンで使用可能なものから選択します。 VM サイズは、所有するデータ ディスクの数がサポートされるように十分な大きさにする必要があります。 たとえば、データ ディスクが 4 つある場合、VM には 2 つ以上のコアが必要です。 さらに、処理能力、メモリ、ネットワーク帯域幅のニーズについても検討します。

4.  現在の VM の詳細 (ディスクの一覧や対応する VHD BLOB など) を手元に用意します。

ダウンタイムに備えてアプリケーションを準備します。 移行を問題なく実行するには、現在のシステムですべての処理を停止する必要があります。 そうすることで初めて、新しいプラットフォームに移行できる安定した状態になります。 ダウンタイム時間は、移行するディスクのデータ量によって異なります。


## <a name="migrate-the-vm"></a>VM の移行

ダウンタイムに備えてアプリケーションを準備します。 移行を問題なく実行するには、現在のシステムですべての処理を停止する必要があります。 そうすることで初めて、新しいプラットフォームに移行できる安定した状態になります。 ダウンタイム時間は、移行するディスクのデータ量によって異なります。

ここでは、Azure PowerShell モジュール バージョン 6.0.0 以降が必要です。 バージョンを確認するには、` Get-Module -ListAvailable AzureRM` を実行します。 アップグレードする必要がある場合は、[Azure PowerShell モジュールのインストール](/powershell/azure/install-azurerm-ps)に関するページを参照してください。 `Connect-AzureRmAccount` を実行して、Azure との接続を作成する必要もあります。


一般的なパラメーターのための変数を作成します。

```powershell
$resourceGroupName = 'yourResourceGroupName'

$location = 'your location' 

$virtualNetworkName = 'yourExistingVirtualNetworkName'

$virtualMachineName = 'yourVMName'

$virtualMachineSize = 'Standard_DS3'

$adminUserName = "youradminusername"

$adminPassword = "yourpassword" | ConvertTo-SecureString -AsPlainText -Force

$imageName = 'yourImageName'

$osVhdUri = 'https://storageaccount.blob.core.windows.net/vhdcontainer/osdisk.vhd'

$dataVhdUri = 'https://storageaccount.blob.core.windows.net/vhdcontainer/datadisk1.vhd'

$dataDiskName = 'dataDisk1'
```

VHD を使用してクラシック VM から管理 OS ディスクを作成します。 $osVhdUri パラメーターには、OS VHD の完全な URI を指定してください。 また、移行先のディスクの種類 (Premium または Standard) に合わせて、**-AccountType** で「**Premium_LRS**」または「**Standard_LRS**」と入力します。

```powershell
$osDisk = New-AzureRmDisk -DiskName $osDiskName '
   -Disk (New-AzureRmDiskConfig '
   -AccountType Premium_LRS '
   -Location $location '
   -CreateOption Import '
   -SourceUri $osVhdUri) '
   -ResourceGroupName $resourceGroupName
```

新しい VM に OS ディスクを接続します。

```powershell
$VirtualMachine = New-AzureRmVMConfig -VMName $virtualMachineName -VMSize $virtualMachineSize
$VirtualMachine = Set-AzureRmVMOSDisk '
   -VM $VirtualMachine '
   -ManagedDiskId $osDisk.Id '
   -StorageAccountType Premium_LRS '
   -DiskSizeInGB 128 '
   -CreateOption Attach -Windows
```

データ VHD ファイルからマネージド データ ディスクを作成し、新しい VM に追加します。

```powershell
$dataDisk1 = New-AzureRmDisk '
   -DiskName $dataDiskName '
   -Disk (New-AzureRmDiskConfig '
   -AccountType Premium_LRS '
   -Location $location '
   -CreationOption Import '
   -SourceUri $dataVhdUri ) '
   -ResourceGroupName $resourceGroupName
    
$VirtualMachine = Add-AzureRmVMDataDisk '
   -VM $VirtualMachine '
   -Name $dataDiskName '
   -CreateOption Attach '
   -ManagedDiskId $dataDisk1.Id '
   -Lun 1
```

パブリック IP、仮想ネットワーク、NIC を設定して新しい VM を作成します。

```powershell
$publicIp = New-AzureRmPublicIpAddress '
   -Name ($VirtualMachineName.ToLower()+'_ip') '
   -ResourceGroupName $resourceGroupName '
   -Location $location '
   -AllocationMethod Dynamic
    
$vnet = Get-AzureRmVirtualNetwork '
   -Name $virtualNetworkName '
   -ResourceGroupName $resourceGroupName
    
$nic = New-AzureRmNetworkInterface '
   -Name ($VirtualMachineName.ToLower()+'_nic') '
   -ResourceGroupName $resourceGroupName '
   -Location $location '
   -SubnetId $vnet.Subnets[0].Id '
   -PublicIpAddressId $publicIp.Id
    
$VirtualMachine = Add-AzureRmVMNetworkInterface '
   -VM $VirtualMachine '
   -Id $nic.Id
    
New-AzureRmVM -VM $VirtualMachine '
   -ResourceGroupName $resourceGroupName '
   -Location $location
```

> [!NOTE]
>このガイドで説明されていないアプリケーションをサポートするには、追加の手順が必要になる場合があります。
>
>

## <a name="next-steps"></a>次の手順

- 仮想マシンへの接続 手順については、「 [Windows が実行されている Azure 仮想マシンに接続してログオンする方法](connect-logon.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)」をご覧ください。

