---
title: Azure マネージド ディスクのストレージを Standard から Premium に (または Premium から Standard に) 変換する | Microsoft Docs
description: Azure マネージド ディスクを Azure PowerShell を使用して Standard から Premium に (または Premium から Standard に) 変換する方法。
services: virtual-machines-windows
documentationcenter: ''
author: ramankumarlive
manager: kavithag
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 10/04/2018
ms.author: ramankum
ms.subservice: disks
ms.openlocfilehash: 94482666d0db3157b0c18c0b47f9937457172521
ms.sourcegitcommit: fec0e51a3af74b428d5cc23b6d0835ed0ac1e4d8
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/12/2019
ms.locfileid: "56115999"
---
# <a name="update-the-storage-type-of-a-managed-disk"></a>マネージド ディスクのストレージの種類を更新する

マネージド ディスクには、[Premium SSD](../windows/premium-storage.md)、[Standard SSD](../windows/disks-standard-ssd.md)、および [Standard HDD](../windows/standard-storage.md) の 3 つのストレージ オプションが用意されています。 パフォーマンスのニーズに応じて、ダウンタイムを最小限に抑えてストレージの種類間でマネージド ディスクを切り替えることができます。 アンマネージド ディスクでは、ストレージの種類の切り替えはサポートされていません。ただし、[アンマネージド ディスクをマネージド ディスクに簡単に変換する](convert-unmanaged-to-managed-disks.md)ことができます。

[!INCLUDE [updated-for-az-vm.md](../../../includes/updated-for-az-vm.md)]

## <a name="prerequisites"></a>前提条件

* 変換作業には仮想マシン (VM) の再起動が必要なので、既に設定されているメンテナンス期間中にディスク ストレージの移行をスケジュールするようにします。 
* アンマネージド ディスクを使用している場合は、まず[マネージド ディスクに変換して](convert-unmanaged-to-managed-disks.md)、ストレージの種類間で切り替えることができます。 
* この記事の例では、バージョン 6.0.0 以降の Azure PowerShell が必要です。 バージョンを確認するには、`Get-Module -ListAvailable AzureRM` を実行します。 アップグレードする必要がある場合は、[Azure PowerShell モジュールのインストール](/powershell/azure/azurerm/install-azurerm-ps)に関するページを参照してください。 Azure との接続を作成するには、[Connect-AzAccount](https://docs.microsoft.com/powershell/module/az.accounts/connect-azaccount) を実行します。


## <a name="convert-all-the-managed-disks-of-a-vm-from-standard-to-premium"></a>VM のすべてのマネージド ディスクを Standard から Premium に変換する

次の例は、VM のすべてのディスクを Standard ストレージから Premium ストレージに切り替える方法を示しています。 Premium マネージド ディスクを使用するには、Premium Storage に対応している [VM のサイズ](sizes.md)を使用している必要があります。 この例は、Premium ストレージに対応するサイズへの切り替えも行います。

```azurepowershell-interactive
# Name of the resource group that contains the VM
$rgName = 'yourResourceGroup'

# Name of the your virtual machine
$vmName = 'yourVM'

# Choose between StandardLRS and PremiumLRS based on your scenario
$storageType = 'Premium_LRS'

# Premium capable size
# Required only if converting storage from standard to premium
$size = 'Standard_DS2_v2'

# Stop and deallocate the VM before changing the size
Stop-AzVM -ResourceGroupName $rgName -Name $vmName -Force

$vm = Get-AzVM -Name $vmName -resourceGroupName $rgName

# Change the VM size to a size that supports premium storage
# Skip this step if converting storage from premium to standard
$vm.HardwareProfile.VmSize = $size
Update-AzVM -VM $vm -ResourceGroupName $rgName

# Get all disks in the resource group of the VM
$vmDisks = Get-AzDisk -ResourceGroupName $rgName 

# For disks that belong to the selected VM, convert to premium storage
foreach ($disk in $vmDisks)
{
    if ($disk.ManagedBy -eq $vm.Id)
    {
        $diskUpdateConfig = New-AzDiskUpdateConfig –AccountType $storageType
        Update-AzDisk -DiskUpdate $diskUpdateConfig -ResourceGroupName $rgName `
        -DiskName $disk.Name
    }
}

Start-AzVM -ResourceGroupName $rgName -Name $vmName
```

## <a name="convert-a-managed-disk-from-standard-to-premium"></a>マネージド ディスクを Standard から Premium に変換する

開発/テスト ワークロードでは、コストを削減するために Standard ディスクと Premium ディスクを混在させたい場合があります。 そのためには、パフォーマンスの向上が必要なディスクのみを Premium ストレージにアップグレードします。 次の例は、VM の 1 つのディスクを Standard から Premium に (または Premium から Standard に) 切り替える方法を示しています。 Premium マネージド ディスクを使用するには、Premium Storage に対応している [VM のサイズ](sizes.md)を使用している必要があります。 この例では、Premium ストレージに対応するサイズに切り替える方法も示しています。

```azurepowershell-interactive

$diskName = 'yourDiskName'
# resource group that contains the managed disk
$rgName = 'yourResourceGroupName'
# Choose between StandardLRS and PremiumLRS based on your scenario
$storageType = 'Premium_LRS'
# Premium capable size 
$size = 'Standard_DS2_v2'

$disk = Get-AzDisk -DiskName $diskName -ResourceGroupName $rgName

# Get parent VM resource
$vmResource = Get-AzResource -ResourceId $disk.ManagedBy

# Stop and deallocate the VM before changing the storage type
Stop-AzVM -ResourceGroupName $vmResource.ResourceGroupName -Name $vmResource.Name -Force

$vm = Get-AzVM -ResourceGroupName $vmResource.ResourceGroupName -Name $vmResource.Name 

# Change the VM size to a size that supports premium storage
# Skip this step if converting storage from premium to standard
$vm.HardwareProfile.VmSize = $size
Update-AzVM -VM $vm -ResourceGroupName $rgName

# Update the storage type
$diskUpdateConfig = New-AzDiskUpdateConfig -AccountType $storageType -DiskSizeGB $disk.DiskSizeGB
Update-AzDisk -DiskUpdate $diskUpdateConfig -ResourceGroupName $rgName `
-DiskName $disk.Name

Start-AzVM -ResourceGroupName $vm.ResourceGroupName -Name $vm.Name
```

## <a name="convert-a-managed-disk-from-standard-hdd-to-standard-ssd"></a>マネージド ディスクを Standard HDD から Standard SSD に変換する

次の例は、VM の 1 つのディスクを Standard HDD から Standard SSD に (または Standard SSD から Standard HDD に) 切り替える方法を示しています。

```azurepowershell-interactive

$diskName = 'yourDiskName'
# resource group that contains the managed disk
$rgName = 'yourResourceGroupName'
# Choose between Standard_LRS and StandardSSD_LRS based on your scenario
$storageType = 'StandardSSD_LRS'

$disk = Get-AzDisk -DiskName $diskName -ResourceGroupName $rgName

# Get parent VM resource
$vmResource = Get-AzResource -ResourceId $disk.ManagedBy

# Stop and deallocate the VM before changing the storage type
Stop-AzVM -ResourceGroupName $vmResource.ResourceGroupName -Name $vmResource.Name -Force

$vm = Get-AzVM -ResourceGroupName $vmResource.ResourceGroupName -Name $vmResource.Name 

# Update the storage type
$diskUpdateConfig = New-AzDiskUpdateConfig -AccountType $storageType -DiskSizeGB $disk.DiskSizeGB
Update-AzDisk -DiskUpdate $diskUpdateConfig -ResourceGroupName $rgName `
-DiskName $disk.Name

Start-AzVM -ResourceGroupName $vm.ResourceGroupName -Name $vm.Name
```

## <a name="next-steps"></a>次の手順

[スナップショット](snapshot-copy-managed-disk.md)を使用して、VM の読み取り専用コピーを取得します。

