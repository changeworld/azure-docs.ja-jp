---
title: Azure で容量予約を変更する (プレビュー)
description: 容量予約を変更する方法について説明します。
author: vargupt
ms.author: vargupt
ms.service: virtual-machines
ms.topic: how-to
ms.date: 08/09/2021
ms.reviewer: cynthn, jushiman
ms.custom: template-how-to
ms.openlocfilehash: 25b6eaea3c639d39721bd80aaad08cf2c6f80380
ms.sourcegitcommit: 4abfec23f50a164ab4dd9db446eb778b61e22578
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/15/2021
ms.locfileid: "130066473"
---
# <a name="modify-a-capacity-reservation-preview"></a>容量予約を変更する (プレビュー)

容量予約グループと容量予約を作成した後に、予約を変更することができます。 この記事では、API、Azure portal、PowerShell を使用して次の操作を行う方法について説明します。 

> [!div class="checklist"]
> * 容量予約で予約されているインスタンスの数を更新する 
> * 容量予約グループに関連付けられている VM サイズを変更する
> * 容量予約グループと容量予約を削除する

> [!IMPORTANT]
> 容量予約は現在パブリック プレビュー段階にあります。
> このプレビュー バージョンはサービス レベル アグリーメントなしで提供されており、運用環境のワークロードに使用することは推奨されません。 特定の機能はサポート対象ではなく、機能が制限されることがあります。 詳しくは、[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)に関するページをご覧ください。


## <a name="update-the-number-of-instances-reserved"></a>予約されているインスタンスの数を更新する 

容量予約で予約されている仮想マシン インスタンスの数を更新します。  

> [!IMPORTANT]
> まれに、既存の容量予約用に予約された数量を増やす要求を Azure で満たすことができない場合、予約が "*失敗*" 状態になり、[数量が元の量に復元される](#restore-instance-quantity)まで使用できなくなる可能性があります。

### <a name="api"></a>[API](#tab/api1)

```rest
    PATCH https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/CapacityReservationGroups/{CapacityReservationGroupName}/capacityReservations/{capacityReservationName}?api-version=2021-04-01
``` 
    
要求本文で、予約する新しい数に `capacity` プロパティを更新します。 
    
```json
{
    "sku":
    {
        "capacity": 5
    }
} 
```

この例で `capacity` プロパティが 5 に設定されたことに注目してください。


### <a name="portal"></a>[ポータル](#tab/portal1) 

1. [Azure portal](https://portal.azure.com) を開きます。
1. 容量予約グループに移動します
1. **[概要]** を選択します 
1. **[予約]** を選択します 
1. 上部にある **[予約の管理]** を選択します 
1. *[予約管理]* ページで、 **[インスタンス]** フィールドに予約する新しい数量を入力します 
1. **[保存]** を選びます。 

### <a name="cli"></a>[CLI](#tab/cli1)
予約された数量を更新するには、更新された `capacity ` プロパティを指定して `az capacity reservation update` を使用します。

 ```azurecli-interactive
 az capacity reservation update 
 -c myCapacityReservationGroup 
 -n myCapacityReservation 
 -g myResourceGroup2 
 --capacity 5
 ```

### <a name="powershell"></a>[PowerShell](#tab/powershell1)

予約された数量を更新するには、更新された `capacityToReserve` プロパティを指定して `New-AzCapacityReservation` を使用します。

```powershell-interactive
Update-AzCapacityReservation
-ResourceGroupName "myResourceGroup"
-ReservationGroupName "myCapacityReservationGroup"
-Name "myCapacityReservation"
-CapacityToReserve 5
```

詳細については、Azure PowerShell コマンド「[Update-AzCapacityReservation](/powershell/module/az.compute/update-azcapacityreservation)」を参照してください。

--- 
<!-- The three dashes above show that your section of tabbed content is complete. Don't remove them :) -->


## <a name="resize-vms-associated-with-a-capacity-reservation-group"></a>容量予約グループに関連付けられている VM サイズを変更する 

現在、サイズ変更中の仮想マシンが容量予約グループにアタッチされていて、そのグループにターゲット サイズの予約が存在しない場合は、そのサイズの新しい予約を作成するか、サイズを変更する前に予約グループからその仮想マシンを削除します。 

ターゲット サイズが予約グループの一部であるかどうかを確認します。 

### <a name="api"></a>[API](#tab/api2)

1. グループ内のすべての容量予約の名前を取得します。
 
    ```rest
        GET https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/CapacityReservationGroups/{CapacityReservationGroupName}?api-version=2021-04-01
    ``` 
    
    ```json
    { 
        "name": "<CapacityReservationGroupName>", 
        "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/capacityReservationGroups/{CapacityReservationGroupName}", 
        "type": "Microsoft.Compute/capacityReservationGroups", 
        "location": "eastUS", 
        "zones": [ 
            "1" 
        ], 
        "properties": { 
            "capacityReservations": [ 
                { 
                    "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/capacityReservationGroups/{CapacityReservationGroupName}/capacityReservations/{capacityReservationName1}" 
                }, 
    { 
                    "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/capacityReservationGroups/{CapacityReservationGroupName}/capacityReservations/{capacityReservationName2}" 
                } 
            ] 
        } 
    } 
    ```

1. 予約ごとに予約されている VM サイズを確認します。 `capacityReservationName1` の例を次に示しますが、他の予約に対してこの手順を繰り返すことができます。

    ```rest
        GET https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/CapacityReservationGroups/{CapacityReservationGroupName}/capacityReservations/{capacityReservationName1}?api-version=2021-04-01
    ``` 
    
    ```json
    { 
        "name": "capacityReservationName1", 
        "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/capacityReservationGroups/{CapacityReservationGroupName}/capacityReservations/{capacityReservationName1}", 
        "type": "Microsoft.Compute/capacityReservationGroups/capacityReservations", 
        "location": "eastUS", 
        "sku": { 
            "name": "Standard_D2s_v3", 
            "capacity": 3 
        }, 
        "zones": [ 
            "1" 
        ], 
        "properties": { 
            "reservationId": "<reservationId>", 
            "provisioningTime": "<provisioningTime>", 
            "provisioningState": "Succeeded" 
        } 
    }  
    ```

1. 以下、具体例に沿って説明します。 
    1. ターゲット VM サイズがグループの一部ではない場合は、ターゲット VM の[新しい容量予約を作成](capacity-reservation-create.md)します 
    1. ターゲット VM サイズがグループに既に存在する場合は、[仮想マシンのサイズを変更します](resize-vm.md) 

### <a name="portal"></a>[ポータル](#tab/portal2)

1. [Azure portal](https://portal.azure.com) を開きます。
1. 容量予約グループに移動します
1. **[概要]** を選択します 
1. **[予約]** を選択します 
1. 各予約に予約されている "*VM サイズ*" を確認します 
    1. ターゲット VM サイズがグループの一部ではない場合は、ターゲット VM の[新しい容量予約を作成](capacity-reservation-create.md)します 
    1. ターゲット VM サイズがグループに既に存在する場合は、[仮想マシンのサイズを変更します](resize-vm.md) 

### <a name="cli"></a>[CLI](#tab/cli2)

1. `az capacity reservation group show` を使用して、容量予約グループ内のすべての容量予約の名前を取得します

    ```azurecli-interactive
    az capacity reservation group show 
    -g myResourceGroup
    -n myCapacityReservationGroup 
    ```

1. その応答から、すべての容量予約の名前を確認します

1. 次のコマンドを実行して、各予約で予約されている VM サイズを確認します

    ```azurecli-interactive
    az capacity reservation show
    -g myResourceGroup
    -c myCapacityReservationGroup 
    -n myCapacityReservation 
    ```

1. 以下、具体例に沿って説明します。 
    1. ターゲット VM サイズがグループの一部ではない場合は、ターゲット VM の[新しい容量予約を作成](capacity-reservation-create.md)します 
    1. ターゲット VM サイズがグループに既に存在する場合は、[仮想マシンのサイズを変更します](resize-vm.md) 


### <a name="powershell"></a>[PowerShell](#tab/powershell2)

1. `Get-AzCapacityReservationGroup` を使用して、グループ内のすべての容量予約の名前を取得します

    ```powershell-interactive
    Get-AzCapacityReservationGroup
    -ResourceGroupName "myResourceGroup"
    -Name "myCapacityReservationGroup"
    ```

1. その応答から、すべての容量予約の名前を確認します

1. 次のコマンドを実行して、各予約で予約されている VM サイズを確認します

    ```powershell-interactive
    $CapRes =
    Get-AzCapacityReservation
    -ResourceGroupName "myResourceGroup"
    -ReservationGroupName "myCapacityReservationGroup"
    -Name "mycapacityReservation"
    
    $CapRes.Sku
    ```

1. 以下、具体例に沿って説明します。 
    1. ターゲット VM サイズがグループの一部ではない場合は、ターゲット VM の[新しい容量予約を作成](capacity-reservation-create.md)します 
    1. ターゲット VM サイズがグループに既に存在する場合は、[仮想マシンのサイズを変更します](resize-vm.md) 


詳細については、Azure PowerShell コマンド「[Get-AzCapacityReservationGroup](/powershell/module/az.compute/get-azcapacityreservationgroup)」、「[Get-AzCapacityReservation](/powershell/module/az.compute/get-azcapacityreservation)」を参照してください。

--- 
<!-- The three dashes above show that your section of tabbed content is complete. Don't remove them :) -->


## <a name="delete-a-capacity-reservation-group-and-capacity-reservation"></a>容量予約グループと容量予約を削除する 

Azure では、メンバーの容量予約がすべて削除され、グループに仮想マシンが関連付けられていない場合に、容量予約グループを削除できます。  

容量予約を削除するには、まず、それに関連付けられているすべての仮想マシンを確認します。 仮想マシンの一覧は `virtualMachinesAssociated` プロパティで確認できます。 

### <a name="api"></a>[API](#tab/api3)

まず、容量予約グループに関連付けられているすべての仮想マシンを見つけて、関連付けを解除します。
 
```rest
    GET https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/CapacityReservationGroups/{CapacityReservationGroupName}?$expand=instanceView&api-version=2021-04-01
``` 

```json
{ 
    "name": "<capacityReservationGroupName>", 
    "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/capacityReservationGroups/{capacityReservationGroupName}", 
    "type": "Microsoft.Compute/capacityReservationGroups", 
    "location": "eastus", 
    "properties": { 
        "capacityReservations": [ 
            { 
                "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/capacityReservationGroups/{capacityReservationGroupName}/capacityReservations/{capacityReservationName}" 
            } 
        ], 
        "virtualMachinesAssociated": [ 
            { 
                "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachines/{VirtualMachineName1}" 
            }, 
            { 
                "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachines/{VirtualMachineName2}" 
            } 
        ], 
        "instanceView": { 
            "capacityReservations": [ 
                { 
                    "name": "{capacityReservationName}", 
                    "utilizationInfo": { 
                        "virtualMachinesAllocated": [ 
                            { 
                                "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachines/{VirtualMachineName1}" 
                            } 
                        ] 
                    }, 
                    "statuses": [ 
                        { 
                            "code": "ProvisioningState/succeeded", 
                            "level": "Info", 
                            "displayStatus": "Provisioning succeeded", 
                            "time": "<time>" 
                        } 
                    ] 
                } 
            ] 
        } 
    } 
}  
```
上記の応答から、`virtualMachinesAssociated` プロパティですべての仮想マシンの名前を見つけて、「[容量予約への VM の関連付けを削除する](capacity-reservation-remove-vm.md)」の手順を使用して、それらを容量予約グループから削除します。 

すべての仮想マシンが容量予約グループから削除されたら、メンバーの容量予約を削除します。

```rest
DELETE https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/CapacityReservationGroups/{CapacityReservationGroupName}/capacityReservations/{capacityReservationName}?api-version=2021-04-01
```

最後に、親の容量予約グループを削除します。

```rest
DELETE https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/CapacityReservationGroups/{CapacityReservationGroupName}?api-version=2021-04-01
```


### <a name="portal"></a>[ポータル](#tab/portal3)

1. [Azure portal](https://portal.azure.com) を開きます。
1. 容量予約グループに移動します
1. **[リソース]** を選択します 
1. グループに関連付けられているすべての仮想マシンを見つけます
1. [すべての仮想マシンの関連付けを解除します](capacity-reservation-remove-vm.md)
1. グループ内のすべての容量予約を削除します
    1. **[予約]** に移動します
    1. 各予約を選択します 
    1. **[削除]** を選択します
1. 容量予約グループを削除します
    1. 容量予約グループに移動します
    1. ページの上部にある **[削除]** を選択します

### <a name="cli"></a>[CLI](#tab/cli3)

容量予約グループに関連付けられているすべての仮想マシンを見つけて、関連付けを解除します。

1. 次のコマンドレットを実行します。 
    
    ```azurecli-interactive
    az capacity reservation group show
    -g myResourceGroup
    -n myCapacityReservationGroup
    ```

1. 上記の応答から、`VirtualMachinesAssociated` プロパティですべての仮想マシンの名前を見つけて、「[容量予約グループから仮想マシンの関連付けを削除する](capacity-reservation-remove-vm.md)」で説明されている手順を使用して、それらを容量予約グループから削除します。

1. すべての仮想マシンがグループから削除されたら、次の手順に進みます。 

1. 容量予約を削除します。

    ```azurecli-interactive
    az capacity reservation delete 
    -g myResourceGroup 
    -c myCapacityReservationGroup 
    -n myCapacityReservation 
    ```

1. 容量予約グループを削除します。

    ```azurecli-interactive
    az capacity reservation group delete 
    -g myResourceGroup 
    -n myCapacityReservationGroup
    ```

### <a name="powershell"></a>[PowerShell](#tab/powershell3)

容量予約グループに関連付けられているすべての仮想マシンを見つけて、関連付けを解除します。

1. 次のコマンドレットを実行します。 
    
    ```powershell-interactive
    Get-AzCapacityReservationGroup
    -ResourceGroupName "myResourceGroup"
    -Name "myCapacityReservationGroup"
    ```

1. 上記の応答から、`VirtualMachinesAssociated` プロパティですべての仮想マシンの名前を見つけて、「[容量予約グループから仮想マシンの関連付けを削除する](capacity-reservation-remove-vm.md)」で説明されている手順を使用して、それらを容量予約グループから削除します。

1. すべての仮想マシンがグループから削除されたら、次の手順に進みます。 

1. 容量予約を削除します。

    ```powershell-interactive
    Remove-AzCapacityReservation
    -ResourceGroupName "myResourceGroup"
    -ReservationGroupName "myCapacityReservationGroup"
    -Name "myCapacityReservation"
    ```

1. 容量予約グループを削除します。

    ```powershell-interactive
    Remove-AzCapacityReservationGroup
    -ResourceGroupName "myResourceGroup"
    -Name "myCapacityReservationGroup"
    ```

詳細については、Azure PowerShell コマンド「[Get-AzCapacityReservationGroup](/powershell/module/az.compute/get-azcapacityreservationgroup)」、「[Remove-AzCapacityReservation](/powershell/module/az.compute/remove-azcapacityreservation)」、「[Remove-AzCapacityReservationGroup](/powershell/module/az.compute/remove-azcapacityreservationgroup)」を参照してください。

--- 
<!-- The three dashes above show that your section of tabbed content is complete. Don't remove them :) -->


## <a name="restore-instance-quantity"></a>インスタンスの数量を復元する 

予約された数量を減らすための適切な形式の要求は、予約に関連付けられている仮想マシンの数に関係なく、常に成功します。 ただし、予約された数量を増やすと、より多くのクォータが必要になり、Azure で追加の容量要求を満たす必要がある場合があります。 まれに、既存の予約用に予約された数量を増やす要求を Azure で満たすことができない場合、予約が "*失敗*" 状態になり、予約された数量が元の量に復元されるまで使用できなくなる可能性があります。  

> [!NOTE]
> 予約が "*失敗*" 状態の場合、予約に関連付けられているすべての VM は通常通りに引き続き動作します。  

たとえば、`myCapacityReservation` の予約済みの数量が 5 だとします。 5 つの追加インスタンスを要求して、予約された数量の合計を 10 にします。 ただし、リージョンの容量の状況が制約されているため、要求された追加の 5 つの数量を Azure で満たすことができません。 この場合、`myCapacityReservation` は、10 個の数量が予約されている意図された状態を満たすことができず、"*失敗*" 状態になります。 

この失敗を解決するには、次の手順を実行して、予約された数量の古い値を見つけます。  

1. Azure portal で、[[アプリケーション変更分析]](https://ms.portal.azure.com/#blade/Microsoft_Azure_ChangeAnalysis/ChangeAnalysisBaseBlade) に移動します 
1. フィルターで該当する **[サブスクリプション]** 、 **[リソース グループ]** 、 **[時間の範囲]** を選択します
    - **[時間の範囲]** フィルターでは、過去 14 日間までしか戻れません 
1. 容量予約の名前を検索します
1. その予約の `sku.capacity` プロパティの変更を探します 
    - 古い予約されている数量は、 **[古い値]** 列の値です 

`myCapacityReservation` を古い予約済みの数量で更新します。 更新されると、仮想マシンでその予約をすぐに使用できるようになります。 


## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [容量予約から VM を削除する方法を確認する](capacity-reservation-remove-vm.md)
