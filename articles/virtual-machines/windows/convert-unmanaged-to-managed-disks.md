---
title: "Windows VM を非管理対象ディスクから Managed Disks に変換する - Azure | Microsoft Docs"
description: "Resource Manager デプロイメント モデルで PowerShell を使用して Windows VM を非管理対象ディスクから Azure Managed Disks に変換する方法"
services: virtual-machines-windows
documentationcenter: 
author: cynthn
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 06/23/2017
ms.author: cynthn
ms.translationtype: Human Translation
ms.sourcegitcommit: 6efa2cca46c2d8e4c00150ff964f8af02397ef99
ms.openlocfilehash: 636d4f7c5da72973a7837718cfb42dda93bba2cc
ms.contentlocale: ja-jp
ms.lasthandoff: 07/01/2017

---

# <a name="convert-a-windows-vm-from-unmanaged-disks-to-azure-managed-disks"></a>Windows VM を非管理対象ディスクから Azure Managed Disks に変換する

非管理対象ディスクを使用する既存の Windows 仮想マシン (VM) を所有している場合、[Azure Managed Disks](../../storage/storage-managed-disks-overview.md) を使用するように VM を変換することができます。 このプロセスでは、OS ディスクと接続されたすべてのデータ ディスクの両方を変換します。

この記事では、Azure PowerShell を使用して VM を変換する方法を説明します。 インストールまたはアップグレードする必要がある場合は、[Azure PowerShell モジュールのインストールと構成](/powershell/azure/install-azurerm-ps.md)に関するページを参照してください。

## <a name="before-you-begin"></a>開始する前に


* [Managed Disks への移行の計画](on-prem-to-azure.md#plan-for-the-migration-to-managed-disks)を確認します。

[!INCLUDE [virtual-machines-common-convert-disks-considerations](../../../includes/virtual-machines-common-convert-disks-considerations.md)]




## <a name="convert-single-instance-vms"></a>単一インスタンスの VM を変換する
このセクションでは、単一インスタンスの Azure VM を非管理対象ディスクから Managed Disks に変換する方法について説明します。 (VM が可用性セットに含まれている場合は次のセクションを参照してください。) 

1. [Stop-AzureRmVM](/powershell/module/azurerm.compute/stop-azurermvm) コマンドレットを使用して VM の割り当てを解除します。 次の例では、`myResourceGroup` という名前のリソース グループに含まれる `myVM` という名前の VM の割り当てを解除します。 

  ```powershell
  $rgName = "myResourceGroup"
  $vmName = "myVM"
  Stop-AzureRmVM -ResourceGroupName $rgName -Name $vmName -Force
  ```

2. [ConvertTo-AzureRmVMManagedDisk](/powershell/module/azurerm.compute/convertto-azurermvmmanageddisk) コマンドレットを使用して、VM を Managed Disks に変換します。 次のプロセスでは、OS ディスクとすべてのデータ ディスクを含む前の VM を変換します。

  ```powershell
  ConvertTo-AzureRmVMManagedDisk -ResourceGroupName $rgName -VMName $vmName
  ```

3. Managed Disks に変換した後、[Start-AzureRmVM](/powershell/module/azurerm.compute/start-azurermvm) で VM を起動します。 次の例では、前の VM を再起動します。

  ```powershell
  Start-AzureRmVM -ResourceGroupName $rgName -Name $vmName
  ```


## <a name="convert-vms-in-an-availability-set"></a>可用性セットの VM を変換する

Managed Disks に変換する VM が可用性セット内にある場合は、最初に可用性セットを管理対象の可用性セットに変換する必要があります。

1. [Update-AzureRmAvailabilitySet](/powershell/module/azurerm.compute/update-azurermavailabilityset) コマンドレットを使用して可用性セットを変換します。 次の例では、`myResourceGroup` という名前のリソース グループの `myAvailabilitySet` という名前の可用性セットを更新します。

  ```powershell
  $rgName = 'myResourceGroup'
  $avSetName = 'myAvailabilitySet'

  $avSet = Get-AzureRmAvailabilitySet -ResourceGroupName $rgName -Name $avSetName
  Update-AzureRmAvailabilitySet -AvailabilitySet $avSet -Sku Aligned 
  ```

  可用性セットがあるリージョンに管理障害ドメインが 2 つだけあるが、非管理対象障害ドメインの数が 3 つの場合、このコマンドは、"The specified fault domain count 3 must fall in the range 1 to 2"(障害ドメイン数 3 が指定されましたが、この数は 1 ~ 2 にする必要があります) のようなエラーを示します。 エラーを解決するには、次のように、障害ドメインを 2 に更新し、`Sku` を `Aligned` に更新します。

  ```powershell
  $avSet.PlatformFaultDomainCount = 2
  Update-AzureRmAvailabilitySet -AvailabilitySet $avSet -Sku Aligned
  ```

2. 可用性セット内の VM の割り当てを解除し、変換します。 次のスクリプトは、[Stop-AzureRmVM](/powershell/module/azurerm.compute/stop-azurermvm) コマンドレットを使用して各 VM の割り当てを解除し、これを [ConvertTo-AzureRmVMManagedDisk](/powershell/module/azurerm.compute/convertto-azurermvmmanageddisk) を使用して変換し、[Start-AzureRmVM](/powershell/module/azurerm.compute/start-azurermvm) を使用して再起動します。

  ```powershell
  $avSet = Get-AzureRmAvailabilitySet -ResourceGroupName $rgName -Name $avSetName

  foreach($vmInfo in $avSet.VirtualMachinesReferences)
  {
     $vm = Get-AzureRmVM -ResourceGroupName $rgName | Where-Object {$_.Id -eq $vmInfo.id}
     Stop-AzureRmVM -ResourceGroupName $rgName -Name $vm.Name -Force
     ConvertTo-AzureRmVMManagedDisk -ResourceGroupName $rgName -VMName $vm.Name
     Start-AzureRmVM -ResourceGroupName $rgName -Name $vmName
  }
  ```


## <a name="convert-standard-managed-disks-to-premium"></a>Standard Managed Disks を Premium に変換する
VM を Managed Disks に変換したら、管理されるディスクに変換した後は、ストレージ タイプを切り替えることもできます。 Standard Storage を使用するディスクと Premium Storage を使用するディスクは混在させることができます。 次の例では、標準から Premium のストレージに切り替える方法を示します。 Premium Managed Disks を使用するには、Premium Storage に対応している [VM のサイズ](sizes.md)を使用している必要があります。 この例は、Premium ストレージに対応するサイズへの切り替えも行います。

```powershell
$rgName = 'myResourceGroup'
$vmName = 'YourVM'
$size = 'Standard_DS2_v2'
$vm = Get-AzureRmVM -Name $vmName -rgName $resourceGroupName

# Stop deallocate the VM before changing the size
Stop-AzureRmVM -ResourceGroupName $rgName -Name $vmName -Force

# Change VM size to a size supporting Premium storage
$vm.HardwareProfile.VmSize = $size
Update-AzureRmVM -VM $vm -ResourceGroupName $rgName

# Get all disks in the resource group of the VM
$vmDisks = Get-AzureRmDisk -ResourceGroupName $rgName 

# For disks that belong to the VM selected, convert to Premium storage
foreach ($disk in $vmDisks)
{
    if ($disk.OwnerId -eq $vm.Id)
    {
        $diskUpdateConfig = New-AzureRmDiskUpdateConfig –AccountType PremiumLRS
        Update-AzureRmDisk -DiskUpdate $diskUpdateConfig -ResourceGroupName $rgName `
        -DiskName $disk.Name
    }
}

Start-AzureRmVM -ResourceGroupName $rgName -Name $vmName
```

## <a name="troubleshooting"></a>トラブルシューティング

変換中にエラーが発生する場合、または以前の変換の問題のために VM がエラー状態になっている場合は、`ConvertTo-AzureRmVMManagedDisk` コマンドレットをもう一度実行します。 通常再試行するだけで状況が好転します。


## <a name="managed-disks-and-azure-storage-service-encryption"></a>Managed Disks と Azure Storage Service Encryption

非管理対象ディスクが [Azure Storage Service Encryption](../../storage/storage-service-encryption.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) を使って暗号化されたことがあるストレージ アカウントに存在する場合は、前述の手順を使って非管理対象ディスクを Managed Disks に変換することはできません。 次の手順では、暗号化されたストレージ アカウントにある非管理対象ディスクをコピーして使用する方法について詳しく説明します。

1. [AzCopy](../../storage/storage-use-azcopy.md) を使用して、Azure Storage Service Encryption が有効にされたことのないストレージ アカウントに仮想ハード ディスク (VHD) をコピーします。

2. コピーした VM は、次の方法のいずれかで使用します。

  * Managed Disks を使う VM を作成し、`New-AzureRmVm` での作成時にその VHD ファイルを指定します。

  * `Add-AzureRmVmDataDisk` を使って、コピーした VHD を Managed Disks で実行中の VM に接続します。


## <a name="next-steps"></a>次のステップ

[スナップショット](snapshot-copy-managed-disk.md)を使用して、VM の読み取り専用コピーを取得します。


