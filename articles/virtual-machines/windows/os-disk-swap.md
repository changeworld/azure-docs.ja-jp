---
title: PowerShell を使用して Azure VM の OS ディスクを交換する | Microsoft Docs
description: PowerShell を使用して Azure 仮想マシンで使用されるオペレーティング システムのディスクを変更します。
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
ms.date: 04/24/2018
ms.author: cynthn
ms.openlocfilehash: caa8fe2088995e3d30c9b808f639b9280e3a74be
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/28/2018
ms.locfileid: "32195955"
---
# <a name="change-the-os-disk-used-by-an-azure-vm-using-powershell"></a>PowerShell を使用して Azure VM で使用される OS ディスクを変更する

既存の VM があり、そのディスクをバックアップ ディスクまたは別の OS ディスクに交換する場合、Azure PowerShell を使用して OS ディスクを交換できます。 VM を削除して再作成する必要はありません。 別のリソース グループ内のマネージド ディスクでも、まだ使用されていない場合に限り使用することができます。

この場合は、VM を停止するか、割り当てを解除する必要があります。その後は、マネージド ディスクのリソース ID を別のマネージド ディスクのリソース ID に置き換えることができます。

VM のサイズとストレージの種類が、接続するディスクと互換性があることを確認します。 たとえば、使用するディスクが Premium Storage 内にある場合、VM は Premium Storage (DS シリーズのサイズなど) に対応している必要があります。 

[Get-AzureRmDisk](/powershell/module/azurerm.compute/get-azurermdisk) を使用してリソース グループ内のディスクの一覧を取得する

```azurepowershell-interactive
Get-AzureRmDisk -ResourceGroupName myResourceGroup | Format-Table -Property Name
```
 
使用するディスクの名前がわかっている場合は、VM の OS ディスクとしてその名前を設定します。 この例では、*myVM* という名前の VM を停止するか割り当てを解除し、*newDisk* という名前のディスクを新しい OS ディスクとして割り当てます。 
 
```azurepowershell-interactive 
# Get the VM 
$vm = Get-AzureRmVM -ResourceGroupName myResourceGroup -Name myVM 

# Make sure the VM is stopped\deallocated
Stop-AzureRmVM -ResourceGroupName myResourceGroup -Name $vm.Name -Force

# Get the new disk that you want to swap in
$disk = Get-AzureRmDisk -ResourceGroupName myResourceGroup -Name newDisk

# Set the VM configuration to point to the new disk  
Set-AzureRmVMOSDisk -VM $vm -ManagedDiskId $disk.Id -Name $disk.Name 

# Update the VM with the new OS disk
Update-AzureRmVM -ResourceGroupName myResourceGroup -VM $vm 

# Start the VM
Start-AzureRmVM -Name $vm.Name -ResourceGroupName myResourceGroup

```

**次のステップ**

ディスクのコピーを作成する方法については、「[スナップショットの作成](snapshot-copy-managed-disk.md)」を参照してください。