---
title: Azure マネージド ディスクのストレージを Standard から Premium に (または Premium から Standard に) 変換する | Microsoft Docs
description: Azure マネージド ディスクを Azure PowerShell を使用して Standard から Premium に (または Premium から Standard に) 変換する方法。
services: virtual-machines-windows
documentationcenter: ''
author: ramankum
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
ms.openlocfilehash: 958f661585b38b156cf523fe00986e7594474917
ms.sourcegitcommit: 4047b262cf2a1441a7ae82f8ac7a80ec148c40c4
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2018
ms.locfileid: "49093818"
---
# <a name="update-the-storage-type-of-a-managed-disk"></a>マネージド ディスクのストレージの種類を更新する

Azure Managed Disks には、[Premium SSD](../windows/premium-storage.md)、[Standard SSD](../windows/disks-standard-ssd.md)、[Standard HDD](../windows/standard-storage.md) という 3 種類のストレージ オプションがあります。 パフォーマンスのニーズに応じて、ダウンタイムを最小限に抑えてストレージの種類間でマネージド ディスクを切り替えることができます。 アンマネージド ディスクでは、ストレージの種類の切り替えはサポートされていません。ただし、[アンマネージド ディスクをマネージド ディスクに簡単に変換する](convert-unmanaged-to-managed-disks.md)ことができます。

この記事では、マネージド ディスクを Azure PowerShell を使用して Standard から Premium に (または Premium から Standard に) 変換する方法を示します。 PowerShell をインストールまたはアップグレードする必要がある場合は、[Azure PowerShell モジュールのインストールと構成](https://docs.microsoft.com/powershell/azure/install-azurerm-ps?view=azurermps-6.8.1)に関するページを参照してください。

## <a name="prerequisites"></a>前提条件

* 変換作業には仮想マシン (VM) の再起動が必要なので、既に設定されているメンテナンス期間中にディスク ストレージの移行をスケジュールするようにします。 
* アンマネージド ディスクを使用している場合は、まず[マネージド ディスクに変換して](convert-unmanaged-to-managed-disks.md)、ストレージの種類間で切り替えることができます。 
* この記事の例では、バージョン 6.0.0 以降の Azure PowerShell が必要です。 バージョンを確認するには、`Get-Module -ListAvailable AzureRM` を実行します。 アップグレードする必要がある場合は、[Azure PowerShell モジュールのインストール](/powershell/azure/install-azurerm-ps)に関するページを参照してください。 Azure との接続を作成するには、[Connect-AzureRmAccount](https://docs.microsoft.com/powershell/module/azurerm.profile/connect-azurermaccount) を実行します。


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
Stop-AzureRmVM -ResourceGroupName $rgName -Name $vmName -Force

$vm = Get-AzureRmVM -Name $vmName -resourceGroupName $rgName

# Change the VM size to a size that supports premium storage
# Skip this step if converting storage from premium to standard
$vm.HardwareProfile.VmSize = $size
Update-AzureRmVM -VM $vm -ResourceGroupName $rgName

# Get all disks in the resource group of the VM
$vmDisks = Get-AzureRmDisk -ResourceGroupName $rgName 

# For disks that belong to the selected VM, convert to premium storage
foreach ($disk in $vmDisks)
{
    if ($disk.ManagedBy -eq $vm.Id)
    {
        $diskUpdateConfig = New-AzureRmDiskUpdateConfig –AccountType $storageType
        Update-AzureRmDisk -DiskUpdate $diskUpdateConfig -ResourceGroupName $rgName `
        -DiskName $disk.Name
    }
}

Start-AzureRmVM -ResourceGroupName $rgName -Name $vmName
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

$disk = Get-AzureRmDisk -DiskName $diskName -ResourceGroupName $rgName

# Get parent VM resource
$vmResource = Get-AzureRmResource -ResourceId $disk.ManagedBy

# Stop and deallocate the VM before changing the storage type
Stop-AzureRmVM -ResourceGroupName $vmResource.ResourceGroupName -Name $vmResource.Name -Force

$vm = Get-AzureRmVM -ResourceGroupName $vmResource.ResourceGroupName -Name $vmResource.Name 

# Change the VM size to a size that supports premium storage
# Skip this step if converting storage from premium to standard
$vm.HardwareProfile.VmSize = $size
Update-AzureRmVM -VM $vm -ResourceGroupName $rgName

# Update the storage type
$diskUpdateConfig = New-AzureRmDiskUpdateConfig -AccountType $storageType -DiskSizeGB $disk.DiskSizeGB
Update-AzureRmDisk -DiskUpdate $diskUpdateConfig -ResourceGroupName $rgName `
-DiskName $disk.Name

Start-AzureRmVM -ResourceGroupName $vm.ResourceGroupName -Name $vm.Name
```

## <a name="convert-a-managed-disk-from-standard-hdd-to-standard-ssd"></a>マネージド ディスクを Standard HDD から Standard SSD に変換する

次の例は、VM の 1 つのディスクを Standard HDD から Standard SSD に (または Standard SSD から Standard HDD に) 切り替える方法を示しています。

```azurepowershell-interactive

$diskName = 'yourDiskName'
# resource group that contains the managed disk
$rgName = 'yourResourceGroupName'
# Choose between Standard_LRS and StandardSSD_LRS based on your scenario
$storageType = 'StandardSSD_LRS'

$disk = Get-AzureRmDisk -DiskName $diskName -ResourceGroupName $rgName

# Get parent VM resource
$vmResource = Get-AzureRmResource -ResourceId $disk.ManagedBy

# Stop and deallocate the VM before changing the storage type
Stop-AzureRmVM -ResourceGroupName $vmResource.ResourceGroupName -Name $vmResource.Name -Force

$vm = Get-AzureRmVM -ResourceGroupName $vmResource.ResourceGroupName -Name $vmResource.Name 

# Update the storage type
$diskUpdateConfig = New-AzureRmDiskUpdateConfig -AccountType $storageType -DiskSizeGB $disk.DiskSizeGB
Update-AzureRmDisk -DiskUpdate $diskUpdateConfig -ResourceGroupName $rgName `
-DiskName $disk.Name

Start-AzureRmVM -ResourceGroupName $vm.ResourceGroupName -Name $vm.Name
```

## <a name="next-steps"></a>次の手順

[スナップショット](snapshot-copy-managed-disk.md)を使用して、VM の読み取り専用コピーを取得します。

