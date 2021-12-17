---
title: 仮想マシンのサイズ変更
description: Azure 仮想マシンに使用する VM のサイズを変更します。
author: cynthn
ms.service: virtual-machines
ms.workload: infrastructure
ms.topic: how-to
ms.date: 09/13/2021
ms.author: cynthn
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: a95331f1b5cd0110cc38e60870ad9bfd5e4a16bd
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/03/2021
ms.locfileid: "131466810"
---
# <a name="change-the-size-of-a-virtual-machine"></a>仮想マシンのサイズの変更 

**適用対象:** :heavy_check_mark: Linux VM :heavy_check_mark: Windows VM :heavy_check_mark: フレキシブル スケール セット 

この記事では、VM を別の [VM サイズ](sizes.md)に移行する方法について説明します。

仮想マシン (VM) を作成した後、VM のサイズを変更することで、VM をスケールアップまたはスケールダウンできます。 場合によっては、先に VM の割り当てを解除する必要があります。 これは、現在 VM をホストしているハードウェア クラスターで新しいサイズを使用できない場合に発生する可能性があります。

VM が Premium Storage を使用している場合、Premium Storage のサポートを受けるには、**s** バージョンのサイズを選択する必要があります。 たとえば、Standard_E4_v3 ではなく Standard_E4 **s** _v3 を選択します。

## <a name="change-the-vm-size"></a>VM のサイズの変更

### <a name="portal"></a>[ポータル](#tab/portal)

1. [Azure Portal](https://portal.azure.com)を開きます。
1. 仮想マシンのページを開きます。
1. 左側のメニューで **[サイズ]** を選択します。
1. 利用可能なサイズの一覧から新しいサイズを選択し、 **[サイズ変更]** をクリックします。


仮想マシンが現在実行されている場合、そのサイズを変更すると、再起動されます。 

VM がまだ実行されているときに、リストに必要なサイズが表示されていない場合、仮想マシンを停止するとより大きいサイズが表示される可能性があります。

### <a name="cli"></a>[CLI](#tab/cli)

VM のサイズを変更するには、[Azure CLI ](/cli/azure/install-az-cli2) の最新版をインストールし、[az login](/cli/azure/reference-index) を使用して Azure アカウントにログインしてください。

1. [az vm list-vm-resize-options](/cli/azure/vm) を使用して、VM がホストされているハードウェア クラスター上で使用できる、VM のサイズの一覧を確認します。 次の例では、リソース グループ `myResourceGroup` リージョンの `myVM` という名前の VM のサイズが表示されています。
   
    ```azurecli-interactive
    az vm list-vm-resize-options \
    --resource-group myResourceGroup \
    --name myVM --output table
    ```

2. 必要な VM サイズが表示されている場合は、[az vm resize](/cli/azure/vm) を使用して、VM のサイズを変更してください。 次の例では、`myVM` という名前の VM のサイズを `Standard_DS3_v2` サイズに変更します。
   
    ```azurecli-interactive
    az vm resize \
    --resource-group myResourceGroup \
    --name myVM \
    --size Standard_DS3_v2
    ```
   
    この処理中に、VM が再起動します。 再起動後に、既存の OS とデータ ディスクがリマップされます。 一時ディスク上に格納されているデータはすべて失われます。

3. 必要な VM サイズが表示されない場合は、まず [az vm deallocate](/cli/azure/vm) を使用して VM の割り当てを解除します。 これにより、リージョンがサポートする任意のサイズに VM を変更できるようになり、その後 VM を起動できます。 次の手順では、`myResourceGroup` という名前のリソース グループの `myVM` という VM の割り当てを解除し、サイズを変更し、起動します。
   
    ```azurecli-interactive
    # Variables will make this easier. Replace the values with your own.
    resourceGroup=myResourceGroup
    vm=myVM
    size=Standard_DS3_v2

    az vm deallocate \
    --resource-group $resourceGroup \
    --name myVM
    az vm resize \
    --resource-group $resourceGroup \
    --name $vm \
    --size $size
    az vm start \
    --resource-group $resourceGroup \
    --name $vm
    ```
   
   > [!WARNING]
   > VM の割り当てを解除すると、VM に割り当てられているすべての動的 IP アドレスも解放されます。 OS とデータ ディスクには影響しません。

### <a name="powershell"></a>[PowerShell](#tab/powershell)

**PowerShell を使用して、可用性セット内にない VM のサイズを変更します。**

変数をいくつか設定します。 値は実際の値に変更してください。

```azurepowershell-interactive
$resourceGroup = "myResourceGroup"
$vmName = "myVM"
```

VM がホストされているリージョンで使用できる VM のサイズを一覧表示します。 
   
```azurepowershell-interactive
Get-AzVMSize -ResourceGroupName $resourceGroup -VMName $vmName 
```

目的のサイズが表示される場合は、次のコマンドを実行して VM のサイズを変更します。 目的のサイズが表示されない場合は、手順 3. に進みます。
   
```azurepowershell-interactive
$vm = Get-AzVM -ResourceGroupName $resourceGroup -VMName $vmName
$vm.HardwareProfile.VmSize = "<newVMsize>"
Update-AzVM -VM $vm -ResourceGroupName $resourceGroup
```

目的のサイズが表示されない場合は、次のコマンドを実行して VM の割り当てを解除します。その後、VM のサイズを変更して、再起動します。 **\<newVMsize>** を目的のサイズに置き換えます。
   
```azurepowershell-interactive
Stop-AzVM -ResourceGroupName $resourceGroup -Name $vmName -Force
$vm = Get-AzVM -ResourceGroupName $resourceGroup -VMName $vmName
$vm.HardwareProfile.VmSize = "<newVMSize>"
Update-AzVM -VM $vm -ResourceGroupName $resourceGroup
Start-AzVM -ResourceGroupName $resourceGroup -Name $vmName
```

> [!WARNING]
> VM の割り当てを解除すると、VM に割り当てられているすべての動的 IP アドレスが解放されます。 OS とデータ ディスクには影響しません。 
> 
> 

**PowerShell を使用して可用性セット内の VM のサイズを変更する**

現在 VM をホストしているハードウェア クラスターで可用性セット内の VM の新しいサイズを使用できない場合、可用性セット内のすべての VM の割り当てを解除して、VM のサイズを変更する必要があります。 また、1 つの VM のサイズを変更した後、可用性セット内の他の VM のサイズを更新する必要がある場合もあります。 可用性セット内の VM のサイズを変更するには、次の手順を実行します。

```azurepowershell-interactive
$resourceGroup = "myResourceGroup"
$vmName = "myVM"
```

VM がホストされているハードウェア クラスターで使用できる VM のサイズを一覧表示します。 
   
```azurepowershell-interactive
Get-AzVMSize `
-ResourceGroupName $resourceGroup `
-VMName $vmName 
```

目的のサイズが表示される場合は、次のコマンドを実行して VM のサイズを変更します。 表示されない場合は、次のセクションに進みます。
   
```azurepowershell-interactive
$vm = Get-AzVM `
-ResourceGroupName $resourceGroup `
-VMName $vmName 
$vm.HardwareProfile.VmSize = "<newVmSize>"
Update-AzVM `
-VM $vm `
-ResourceGroupName $resourceGroup
```
    
目的のサイズが表示されない場合は、次の手順を続行して、可用性セット内のすべての VM の割り当てを解除し、VM のサイズを変更してから再起動します。

可用性セット内のすべての VM を停止します。
   
```azurepowershell-interactive
$availabilitySetName = "<availabilitySetName>"
$as = Get-AzAvailabilitySet `
-ResourceGroupName $resourceGroup `
-Name $availabilitySetName
$virtualMachines = $as.VirtualMachinesReferences |  Get-AzResource | Get-AzVM
$virtualMachines |  Stop-AzVM -Force -NoWait  
```

可用性セット内の VM のサイズを変更して、再起動します。
   
```azurepowershell-interactive
$availabilitySetName = "<availabilitySetName>"
$newSize = "<newVmSize>"
$as = Get-AzAvailabilitySet -ResourceGroupName $resourceGroup -Name $availabilitySetName
$virtualMachines = $as.VirtualMachinesReferences |  Get-AzResource | Get-AzVM
$virtualMachines | Foreach-Object { $_.HardwareProfile.VmSize = $newSize }
$virtualMachines | Update-AzVM
$virtualMachines | Start-AzVM
```

---

## <a name="next-steps"></a>次のステップ

スケーラビリティを高めるには、複数の VM インスタンスを実行してスケール アウトします。詳細については、[仮想マシン スケール セットでのマシンの自動スケール](../virtual-machine-scale-sets/tutorial-autoscale-powershell.md)に関する記事をご覧ください。
