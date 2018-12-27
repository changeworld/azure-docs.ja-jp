---
title: Windows 仮想マシンを非管理対象ディスクからマネージド ディスクに変換する - Azure Managed Disks |Microsoft Docs
description: Resource Manager デプロイ モデルで PowerShell を使用して Windows VM を非管理対象ディスクからマネージド ディスクに変換する方法
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
ms.date: 07/12/2018
ms.author: cynthn
ms.openlocfilehash: 6e7d4a0ab6d79e1615f921965fb3d77998eaf90c
ms.sourcegitcommit: e0a678acb0dc928e5c5edde3ca04e6854eb05ea6
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/13/2018
ms.locfileid: "39000949"
---
# <a name="convert-a-windows-virtual-machine-from-unmanaged-disks-to-managed-disks"></a>Windows 仮想マシンを非管理対象ディスクからマネージド ディスクに変換します

非管理対象ディスクを使用する既存の Windows 仮想マシン (VM) を所有している場合、[Azure Managed Disks](managed-disks-overview.md) サービスを使用して、マネージド ディスクを使用するように VM を変換できます。 このプロセスでは、OS ディスクと接続されたすべてのデータ ディスクの両方を変換します。

この記事では、Azure PowerShell を使用して VM を変換する方法を説明します。 インストールまたはアップグレードする必要がある場合は、[Azure PowerShell モジュールのインストールと構成](/powershell/azure/install-azurerm-ps)に関するページを参照してください。

## <a name="before-you-begin"></a>開始する前に


* 
  [Managed Disks への移行の計画](on-prem-to-azure.md#plan-for-the-migration-to-managed-disks)を確認します。

* 
  [Managed Disks への移行に関する FAQ](faq-for-disks.md#migrate-to-managed-disks) を確認します。

[!INCLUDE [virtual-machines-common-convert-disks-considerations](../../../includes/virtual-machines-common-convert-disks-considerations.md)]




## <a name="convert-single-instance-vms"></a>単一インスタンスの VM を変換する
このセクションでは、単一インスタンスの Azure VM を非管理対象ディスクからマネージド ディスクに変換する方法について説明します。 (VM が可用性セットに含まれている場合は次のセクションを参照してください。) 

1. [Stop-AzureRmVM](/powershell/module/azurerm.compute/stop-azurermvm) コマンドレットを使用して VM の割り当てを解除します。 次の例では、`myResourceGroup` という名前のリソース グループに含まれる `myVM` という名前の VM の割り当てを解除します。 

  ```azurepowershell-interactive
  $rgName = "myResourceGroup"
  $vmName = "myVM"
  Stop-AzureRmVM -ResourceGroupName $rgName -Name $vmName -Force
  ```

2. 
  [ConvertTo-AzureRmVMManagedDisk](/powershell/module/azurerm.compute/convertto-azurermvmmanageddisk) コマンドレットを使用して、VM をマネージド ディスクに変換します。 次のプロセスでは、OS ディスクとすべてのデータ ディスクを含む前の VM を変換し、仮想マシンを起動します。

  ```azurepowershell-interactive
  ConvertTo-AzureRmVMManagedDisk -ResourceGroupName $rgName -VMName $vmName
  ```



## <a name="convert-vms-in-an-availability-set"></a>可用性セットの VM を変換する

マネージド ディスクに変換する VM が可用性セット内にある場合は、最初に可用性セットを管理対象の可用性セットに変換する必要があります。

1. [Update-AzureRmAvailabilitySet](/powershell/module/azurerm.compute/update-azurermavailabilityset) コマンドレットを使用して可用性セットを変換します。 次の例では、`myResourceGroup` という名前のリソース グループの `myAvailabilitySet` という名前の可用性セットを更新します。

  ```azurepowershell-interactive
  $rgName = 'myResourceGroup'
  $avSetName = 'myAvailabilitySet'

  $avSet = Get-AzureRmAvailabilitySet -ResourceGroupName $rgName -Name $avSetName
  Update-AzureRmAvailabilitySet -AvailabilitySet $avSet -Sku Aligned 
  ```

  可用性セットがあるリージョンに管理障害ドメインが 2 つだけあるが、非管理対象障害ドメインの数が 3 つの場合、このコマンドは、"指定した障害ドメインの数 3 は、1 から 2 の範囲でなければなりません。" のようなエラーを示します。 エラーを解決するには、次のように、障害ドメインを 2 に更新し、`Sku` を `Aligned` に更新します。

  ```azurepowershell-interactive
  $avSet.PlatformFaultDomainCount = 2
  Update-AzureRmAvailabilitySet -AvailabilitySet $avSet -Sku Aligned
  ```

2. 可用性セット内の VM の割り当てを解除し、変換します。 次のスクリプトは、[Stop-AzureRmVM](/powershell/module/azurerm.compute/stop-azurermvm) コマンドレットを使用して各 VM の割り当てを解除し、これを [ConvertTo-AzureRmVMManagedDisk](/powershell/module/azurerm.compute/convertto-azurermvmmanageddisk) を使用して変換し、変換プロセスの一環として自動的に再起動します。

  ```azurepowershell-interactive
  $avSet = Get-AzureRmAvailabilitySet -ResourceGroupName $rgName -Name $avSetName

  foreach($vmInfo in $avSet.VirtualMachinesReferences)
  {
     $vm = Get-AzureRmVM -ResourceGroupName $rgName | Where-Object {$_.Id -eq $vmInfo.id}
     Stop-AzureRmVM -ResourceGroupName $rgName -Name $vm.Name -Force
     ConvertTo-AzureRmVMManagedDisk -ResourceGroupName $rgName -VMName $vm.Name
  }
  ```


## <a name="troubleshooting"></a>トラブルシューティング

変換中にエラーが発生する場合、または以前の変換の問題のために VM がエラー状態になっている場合は、`ConvertTo-AzureRmVMManagedDisk` コマンドレットをもう一度実行します。 通常再試行するだけで状況が好転します。
変換前に、すべての VM の拡張機能が "プロビジョニング成功" 状態にあることを確認してください。そのようになっていないとエラー コード 409 で変換に失敗します。


## <a name="convert-using-the-azure-portal"></a>Azure Portal を使用して変換する

Azure Portal を使用して、アンマネージド ディスクをマネージド ディスクに変換することもできます。

1. [Azure Portal](https://portal.azure.com) にサインインします。
2. ポータルで VM の一覧から VM を選択します。
3. VM のブレードで、メニューから **[ディスク]** を選択します。
4. **[ディスク]** ブレードの上部で、**[編集]** を選択します。
5. VM が可用性セット内にある場合は、可用性セットを先に変換する必要があることを示す警告が **[マネージド ディスクへの移行]** ブレードに表示されます。 警告には、可用性セットを変換するためにクリックできるリンクが含まれています。 可用性セットが変換された後、または VM が可用性セットに含まれていない場合は、**[移行]** をクリックして、ディスクをマネージド ディスクに移行するプロセスを開始します。 

VM が停止し、移行の完了後に再起動します。

## <a name="next-steps"></a>次の手順


  [Standard マネージド ディスクを Premium マネージド ディスクに変換する](convert-disk-storage.md)

[スナップショット](snapshot-copy-managed-disk.md)を使用して、VM の読み取り専用コピーを取得します。

