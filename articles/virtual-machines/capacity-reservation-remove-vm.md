---
title: 容量予約グループから仮想マシンの関連付けを削除する (プレビュー)
description: 容量予約グループから仮想マシンを削除する方法について説明します。
author: vargupt
ms.author: vargupt
ms.service: virtual-machines
ms.topic: how-to
ms.date: 08/09/2021
ms.reviewer: cynthn, jushiman
ms.custom: template-how-to
ms.openlocfilehash: f0c84e4b44218aa4f7659376251d1931ffc9516b
ms.sourcegitcommit: 4abfec23f50a164ab4dd9db446eb778b61e22578
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/15/2021
ms.locfileid: "130063623"
---
# <a name="remove-a-vm-association-from-a-capacity-reservation-group-preview"></a>容量予約グループから VM の関連付けを削除する (プレビュー)

この記事では、容量予約グループへの VM の関連付けを削除する手順について説明します。 容量予約の詳細については、[概要について説明した記事](capacity-reservation-overview.md)を参照してください。 

VM と基になる容量予約の両方で容量が論理的に消費されるため、Azure では、あいまいな割り当て状態や予期しないエラーを回避するために、このプロセスにいくつかの制約を課しています。  

割り当てを変更する方法は 2 つあります。 
- オプション 1: 仮想マシンの割り当てを解除し、容量予約グループのプロパティを変更して、必要に応じて仮想マシンを再起動する
- オプション 2: 予約数量をゼロに更新した後、容量予約グループのプロパティを変更する

> [!IMPORTANT]
> 容量予約は現在パブリック プレビュー段階にあります。
> このプレビュー バージョンはサービス レベル アグリーメントなしで提供されており、運用環境のワークロードに使用することは推奨されません。 特定の機能はサポート対象ではなく、機能が制限されることがあります。 詳しくは、[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)に関するページをご覧ください。

## <a name="deallocate-the-vm"></a>VM の割り当てを解除する

1 つ目のオプションは、仮想マシンの割り当てを解除し、容量予約グループのプロパティを変更して、必要に応じて VM を再起動することです。 

### <a name="api"></a>[API](#tab/api1)

1. VM の割り当てを解除する

    ```rest
    PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachines/{virtualMachineName}/deallocate?api-version=2021-04-01
    ```

1. VM を更新して容量予約グループとの関連付けを削除する
    
    ```rest
    PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachines/{virtualMachineName}/update?api-version=2021-04-01
    ```
    要求本文で、`capacityReservationGroup` プロパティを null に設定して、グループへの VM の関連付けを削除します。

    ```json
     {
    "location": "eastus",
    "properties": {
        "capacityReservation": {
            "capacityReservationGroup": {
                "id":null
            }
        }
    }
    }
    ```

### <a name="portal"></a>[ポータル](#tab/portal1)

<!-- no images necessary if steps are straightforward --> 

1. [Azure portal](https://portal.azure.com) を開きます。
1. 対象の仮想マシンに移動して、 **[概要]** を選択します。
1. **[停止]** を選択します。 
    1. 状態が *[停止済み (割り当て解除)]* に変わると、VM の割り当てが解除されていることがわかります。
    1. プロセスのこの時点では、VM は容量予約グループに関連付けられたままになります。これは容量予約の `virtualMachinesAssociated` プロパティに反映されます。 
1. **[構成]** を選択します。
1. **[Capacity Reservation Group]\(容量予約グループ\)** の値を *[なし]* に設定します。
    - これで VM と容量予約グループの関連付けがなくなります。 

### <a name="cli"></a>[CLI](#tab/cli1)

1. 仮想マシンの割り当てを解除する

    ```azurecli-interactive
    az vm deallocate 
    -g myResourceGroup 
    -n myVM
    ```

    状態が **[停止済み (割り当て解除)]** に変わると、仮想マシンの割り当てが解除されます。

1. `capacity-reservation-group` プロパティを None に設定することで、VM を更新して容量予約グループとの関連付けを削除します。

    ```azurecli-interactive
    az vm update 
    -g myresourcegroup 
    -n myVM 
    --capacity-reservation-group None
    ```

### <a name="powershell"></a>[PowerShell](#tab/powershell1)

1. 仮想マシンの割り当てを解除する

    ```powershell-interactive
    Stop-AzVM
    -ResourceGroupName "myResourceGroup"
    -Name "myVM"
    ```

    状態が **[停止済み (割り当て解除)]** に変わると、仮想マシンの割り当てが解除されます。

1. `CapacityReservationGroupId` プロパティを null に設定することで、VM を更新して容量予約グループとの関連付けを削除します。

    ```powershell-interactive
    $VirtualMachine =
    Get-AzVM
    -ResourceGroupName "myResourceGroup"
    -Name "myVM"
    
    Update-AzVM
    -ResourceGroupName "myResourceGroup"
    -VM $VirtualMachine
    -CapacityReservationGroupId $null
    ```

詳細については、Azure PowerShell のコマンド [Stop-AzVM](/powershell/module/az.compute/stop-azvm)、[Get-AzVM](/powershell/module/az.compute/get-azvm)、[Update-AzVM](/powershell/module/az.compute/update-azvm) に移動します。

--- 
<!-- The three dashes above show that your section of tabbed content is complete. Don't remove them :) -->


## <a name="update-the-reserved-quantity-to-zero"></a>予約数量をゼロに更新する 

2 つ目のオプションでは、予約数量をゼロに更新した後、容量予約グループのプロパティを変更することが伴います。

このオプションは、仮想マシンの割り当てを解除できず、かつ予約が不要になった場合に適しています。 たとえば、容量予約を作成して、大規模なデプロイ中に容量を一時的に確保することができます。 完了すると、予約は不要になります。 

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
    
    上記は `capacity` プロパティが 0 に設定されていることに注目してください。

1. VM を更新して容量予約グループとの関連付けを削除する

    ```rest
    PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachines/{VirtualMachineName}/update?api-version=2021-04-01
    ```

    要求本文で、`capacityReservationGroup` プロパティを null に設定して、関連付けを削除します。
    
    ```json
    {
    "location": "eastus",
    "properties": {
        "capacityReservation": {
            "capacityReservationGroup": {
                "id":null
            }
        }
    }
    } 
    ```

### <a name="portal"></a>[ポータル](#tab/portal2)

<!-- no images necessary if steps are straightforward --> 

1. [Azure portal](https://portal.azure.com) を開きます。
1. 容量予約グループに移動して、 **[概要]** を選択します。
1. **[予約]** を選択します。 
1. ページ上部の **[Manage Reservation]\(予約の管理\)** を選択します。 
1. *[予約の管理]* ブレードで、次のようにします。
    1. **[インスタンス]** フィールドに「`0`」と入力します。
    1. **[保存]** を選びます。 
1. 対象の仮想マシンに移動して、 **[構成]** を選択します。
1. **[Capacity Reservation Group]\(容量予約グループ\)** の値を *[なし]* に設定します。
    - これで VM と容量予約グループの関連付けがなくなることに注目してください。

### <a name="cli"></a>[CLI](#tab/cli2)

1. 予約数量をゼロに更新する

   ```azurecli-interactive
   az capacity reservation update 
   -g myResourceGroup
   -c myCapacityReservationGroup 
   -n myCapacityReservation 
   --capacity 0
   ```

1. `capacity-reservation-group` プロパティを None に設定することで、VM を更新して容量予約グループとの関連付けを削除します。

    ```azurecli-interactive
    az vm update 
    -g myresourcegroup 
    -n myVM 
    --capacity-reservation-group None
    ```


### <a name="powershell"></a>[PowerShell](#tab/powershell2)

1. 予約数量をゼロに更新する

    ```powershell-interactive
    Update-AzCapacityReservation
    -ResourceGroupName "myResourceGroup"
    -ReservationGroupName "myCapacityReservationGroup"
    -Name "myCapacityReservation"
    -CapacityToReserve 0
    ```

1. `CapacityReservationGroupId` プロパティを null に設定することで、VM を更新して容量予約グループとの関連付けを削除します。

    ```powershell-interactive
    $VirtualMachine =
    Get-AzVM
    -ResourceGroupName "myResourceGroup"
    -Name "myVM"
    
    Update-AzVM
    -ResourceGroupName "myResourceGroup"
    -VM $VirtualMachine
    -CapacityReservationGroupId $null
    ```

詳細については、Azure PowerShell のコマンド [New-AzCapacityReservation](/powershell/module/az.compute/new-azcapacityreservation)、[Get-AzVM](/powershell/module/az.compute/get-azvm)、[Update-AzVM](/powershell/module/az.compute/update-azvm) に移動します。

--- 
<!-- The three dashes above show that your section of tabbed content is complete. Don't remove them :) -->


## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [スケール セットを容量予約グループに関連付ける方法について確認する](capacity-reservation-associate-virtual-machine-scale-set.md)
