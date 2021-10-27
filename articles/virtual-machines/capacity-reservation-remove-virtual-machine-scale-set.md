---
title: 容量予約グループから仮想マシン スケール セットの関連付けを削除する (プレビュー)
description: 容量予約グループから仮想マシン スケール セットを削除する方法について説明します。
author: vargupt
ms.author: vargupt
ms.service: virtual-machines
ms.topic: how-to
ms.date: 08/09/2021
ms.reviewer: cynthn, jushiman
ms.custom: template-how-to
ms.openlocfilehash: cc3b433b0ae36076a0442c8dc91e502020bdfd04
ms.sourcegitcommit: 4abfec23f50a164ab4dd9db446eb778b61e22578
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/15/2021
ms.locfileid: "130063642"
---
# <a name="remove-a-virtual-machine-scale-set-association-from-a-capacity-reservation-group"></a>容量予約グループから仮想マシン スケール セットの関連付けを削除する 

この記事では、容量予約グループから仮想マシン スケール セットの関連付けを削除する手順について説明します。 容量予約の詳細については、[概要について説明した記事](capacity-reservation-overview.md)を参照してください。 

VM と基になる容量予約の両方で容量が論理的に消費されるため、Azure では、あいまいな割り当て状態や予期しないエラーを回避するために、このプロセスにいくつかの制約を課しています。  

割り当てを変更する方法は 2 つあります。 
- オプション 1: 仮想マシン スケール セットの割り当てを解除して、スケール セット レベルで容量予約グループのプロパティを変更してから、基になる VM を更新する
- オプション 2: 予約数量をゼロに更新した後、容量予約グループのプロパティを変更する

> [!IMPORTANT]
> 容量予約は現在パブリック プレビュー段階にあります。
> このプレビュー バージョンはサービス レベル アグリーメントなしで提供されており、運用環境のワークロードに使用することは推奨されません。 特定の機能はサポート対象ではなく、機能が制限されることがあります。 詳しくは、[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)に関するページをご覧ください。

## <a name="deallocate-the-virtual-machine-scale-set"></a>仮想マシン スケール セットの割り当て解除

1 つ目のオプションは、仮想マシン スケール セットの割り当てを解除して、スケール セット レベルで容量予約グループのプロパティを変更してから、基になる VM を更新する方法です。 

自動、ローリング、手動によるアップグレードの詳細については、[アップグレード ポリシー](#upgrade-policies)に関する説明を参照してください。 

### <a name="api"></a>[API](#tab/api1)

1. 仮想マシン スケール セットの割り当て解除

    ```rest
    POST  https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachineScaleSets/{VMScaleSetName}/deallocate?api-version=2021-04-01
    ```

1. 仮想マシン スケール セットを更新して、容量予約グループとの関連付けを削除する
    
    ```rest
    PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachineScaleSets/{VMScaleSetName}/update?api-version=2021-04-01
    ```
    要求本文で、`capacityReservationGroup` プロパティを null に設定して、グループへの仮想マシン スケール セットの関連付けを削除します。

    ```json
    {
    "location": "eastus",
    "properties": {
        "virtualMachineProfile": {
            "capacityReservation": {
                "capacityReservationGroup":{
                    "id":null    
                }
            }
        }
    }
    }
    ```

### <a name="cli"></a>[CLI](#tab/cli1)

1. 仮想マシン スケール セットの割り当てを解除します。 次のように設定すると、スケール セット内のすべての仮想マシンの割り当てが解除されます。 

    ```azurecli-interactive
    az vmss deallocate
    --location eastus
    --resource-group myResourceGroup 
    --name myVMSS 
    ```

1. スケール セットを更新して、容量予約グループとの関連付けを削除します。 `capacity-reservation-group` プロパティを None に設定すると、スケール セットと容量予約グループの関連付けは削除されます。 

    ```azurecli-interactive
    az vmss update 
    --resource-group myresourcegroup 
    --name myVMSS 
    --capacity-reservation-group None
    ```


### <a name="powershell"></a>[PowerShell](#tab/powershell1)

1. 仮想マシン スケール セットの割り当てを解除します。 次のように設定すると、スケール セット内のすべての仮想マシンの割り当てが解除されます。 

    ```powershell-interactive
    Stop-AzVmss
    -ResourceGroupName "myResourceGroup"
    -VMScaleSetName "myVmss"
    ```

1. スケール セットを更新して、容量予約グループとの関連付けを削除します。 `CapacityReservationGroupId` プロパティを null に設定すると、スケール セットと容量予約グループの関連付けは削除されます。 

    ```powershell-interactive
    $vmss =
    Get-AzVmss
    -ResourceGroupName "myResourceGroup"
    -VMScaleSetName "myVmss"
    
    Update-AzVmss
    -ResourceGroupName "myResourceGroup"
    -VMScaleSetName "myvmss"
    -VirtualMachineScaleSet $vmss
    -CapacityReservationGroupId $null
    ```

詳細については、Azure PowerShell コマンド「[Stop-AzVmss](/powershell/module/az.compute/stop-azvmss)」、「[Get-AzVmss](/powershell/module/az.compute/get-azvmss)」、「[Update-AzVmss](/powershell/module/az.compute/update-azvmss)」を参照してください。

--- 
<!-- The three dashes above show that your section of tabbed content is complete. Don't remove them :) -->


## <a name="update-the-reserved-quantity-to-zero"></a>予約数量をゼロに更新する 

2 つ目のオプションでは、予約数量をゼロに更新した後、容量予約グループのプロパティを変更することが伴います。

このオプションは、仮想マシン スケール セットの割り当てを解除できず、かつ予約が不要になった場合に適しています。 たとえば、容量予約を作成して、大規模なデプロイ中に容量を一時的に確保することができます。 完了すると、予約は不要になります。 

自動、ローリング、手動によるアップグレードの詳細については、[アップグレード ポリシー](#upgrade-policies)に関する説明を参照してください。 

### <a name="api"></a>[API](#tab/api2)

1. 予約数量をゼロに更新する 

    ```rest
    PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/CapacityReservationGroups/{CapacityReservationGroupName}/CapacityReservations/{CapacityReservationName}?api-version=2021-04-01
    ```

    要求本文に、次の内容を含めます。
    
    ```json
    {
    "sku": 
        {
        "capacity": 0
        }
    } 
    ```
    
    上記では、`capacity` プロパティが 0 に設定されていることに注目してください。

1. 仮想マシン スケール セットを更新して、容量予約グループとの関連付けを削除する

    ```rest
    PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachineScaleSets/{VMScaleSetName}/update?api-version=2021-04-01
    ```

    要求本文で、`capacityReservationGroup` プロパティを null に設定して、関連付けを削除します。
    
    ```json
    {
    "location": "eastus",
    "properties": {
        "virtualMachineProfile": {
            "capacityReservation": {
                "capacityReservationGroup":{
                    "id":null
                }
            }
        }
    }
    }
    ```

### <a name="cli"></a>[CLI](#tab/cli2)

1. 予約数量をゼロに更新します。

    ```azurecli-interactive
    az capacity reservation update 
    -g myResourceGroup 
    -c myCapacityReservationGroup 
    -n myCapacityReservation 
    --capacity 0
    ```

2. `capacity-reservation-group` プロパティを None に設定することで、スケール セットを更新して容量予約グループとの関連付けを削除します。 

    ```azurecli-interactive
    az vmss update 
    --resource-group myResourceGroup 
    --name myVMSS 
    --capacity-reservation-group None
    ```

### <a name="powershell"></a>[PowerShell](#tab/powershell2)

1. 予約数量をゼロに更新します。

    ```powershell-interactive
    Update-AzCapacityReservation
    -ResourceGroupName "myResourceGroup"
    -ReservationGroupName "myCapacityReservationGroup"
    -Name "myCapacityReservation"
    -CapacityToReserve 0
    ```

2. `CapacityReservationGroupId` プロパティを null に設定することで、スケール セットを更新して容量予約グループとの関連付けを削除します。 

    ```powershell-interactive
    $vmss =
    Get-AzVmss
    -ResourceGroupName "myResourceGroup"
    -VMScaleSetName "myVmss"
    
    Update-AzVmss
    -ResourceGroupName "myResourceGroup"
    -VMScaleSetName "myvmss"
    -VirtualMachineScaleSet $vmss
    -CapacityReservationGroupId $null
    ```

詳細については、Azure PowerShell コマンド「[New-AzCapacityReservation](/powershell/module/az.compute/new-azcapacityreservation)」、「[Get-AzVmss](/powershell/module/az.compute/get-azvmss)」、「[Update-AzVmss](/powershell/module/az.compute/update-azvmss)」を参照してください。

--- 
<!-- The three dashes above show that your section of tabbed content is complete. Don't remove them :) -->


## <a name="upgrade-policies"></a>アップグレード ポリシー

- **自動アップグレード** – このモードでは、他の操作なしで自動的に、スケール セット VM インスタンスと容量予約グループの関連付けが解除されます。
- **ローリング アップグレード** – このモードでは、他の操作なしで、スケール セット VM インスタンスと容量予約グループの関連付けが解除されます。 ただし、バッチ間に任意の一時停止時間が設けられたバッチで更新されます。
- **手動アップグレード** – このモードでは、仮想マシン スケール セットが更新された場合、スケール セット VM インスタンスには何も起こりません。 [最新のスケール セット モデル でアップグレード](../virtual-machine-scale-sets/virtual-machine-scale-sets-upgrade-scale-set.md#how-to-bring-vms-up-to-date-with-the-latest-scale-set-model)して、各スケール セット VM を個別に削除する必要があります。

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [容量予約の過剰割り当てについて学習する](capacity-reservation-overallocate.md)
