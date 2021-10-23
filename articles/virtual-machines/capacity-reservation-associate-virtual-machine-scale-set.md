---
title: 仮想マシン スケール セットを容量予約グループに関連付ける (プレビュー)
description: 新しいまたは既存の仮想マシン スケール セットを容量予約グループに関連付ける方法について説明します。
author: vargupt
ms.author: vargupt
ms.service: virtual-machines
ms.topic: how-to
ms.date: 08/09/2021
ms.reviewer: cynthn, jushiman
ms.custom: template-how-to
ms.openlocfilehash: 9f8f9f14099c20259d26e9cf031695a1e72171a9
ms.sourcegitcommit: 4abfec23f50a164ab4dd9db446eb778b61e22578
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/15/2021
ms.locfileid: "130066283"
---
# <a name="associate-a-virtual-machine-scale-set-to-a-capacity-reservation-group-preview"></a>仮想マシン スケール セットを容量予約グループに関連付ける (プレビュー)

Virtual Machine Scale Sets には次の 2 つのモードがあります。 

- **均一オーケストレーション モード:** このモードの仮想マシン スケール セットでは、VM プロファイルまたはテンプレートを使用して目的の容量にスケールアップします。 個々の VM インスタンスを管理またはカスタマイズする機能はいくつかありますが、均一では同一の VM インスタンスを使用します。 これらのインスタンスは、仮想マシン スケール セット VM API を介して公開され、標準の Azure IaaS VM API コマンドとの互換性はありません。 スケール セットは実際の VM 操作をすべて実行するため、予約は仮想マシン スケール セットに直接関連付けられます。 スケール セットが予約に関連付けられると、それ以降の VM 割り当てはすべて、予約に対して行われます。 
- **フレキシブル オーケストレーション モード:** このモードでは、仮想マシン スケール セットの VM インスタンスで、スケール セット インターフェイスを使用する代わりに標準の Azure IaaS VM API を使用できるため、個々のインスタンスをより柔軟に管理できます。 パブリック プレビュー期間中、このモードは容量予約で機能しません。

これらのモードの詳細については、[仮想マシン スケール セットのオーケストレーション モード](../virtual-machine-scale-sets/virtual-machine-scale-sets-orchestration-modes.md)に関するページを参照してください。 この記事の残りの部分では、均一仮想マシン スケール セットを容量予約グループに関連付ける方法について説明します。 

> [!IMPORTANT]
> 容量予約は現在パブリック プレビュー段階にあります。
> このプレビュー バージョンはサービス レベル アグリーメントなしで提供されており、運用環境のワークロードに使用することは推奨されません。 特定の機能はサポート対象ではなく、機能が制限されることがあります。 詳しくは、[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)に関するページをご覧ください。


## <a name="limitations-of-scale-sets-in-uniform-orchestration"></a>均一オーケストレーションでのスケール セットの制限 

- 均一オーケストレーションの仮想マシン スケール セットと容量予約との互換性を確保するには、`singlePlacementGroup` プロパティを *False* に設定する必要があります。 
- 複数ゾーンの均一スケール セットの可用性オプションである **静的固定拡散** は、容量予約ではサポートされていません。 このオプションでは 5 つの障害ドメインを使用する必要がありますが、予約では、汎用サイズに対して最大 3 つの障害ドメインしかサポートされていません。 推奨されるアプローチとして、**最大拡散** オプションを使用する方法があります。このオプションを使用すると、各ゾーン内の可能な限り多くの FD に VM が拡散されます。 必要に応じて、3 つ以下のカスタム障害ドメイン構成を構成します。 

容量予約を使用する場合、他にもいくつかの制限があります。 完全な一覧については、[容量予約の概要](capacity-reservation-overview.md)に関するページを参照してください。  

## <a name="associate-a-new-virtual-machine-scale-set-to-a-capacity-reservation-group"></a>新しい仮想マシン スケール セットを容量予約グループに関連付ける


### <a name="api"></a>[API](#tab/api1)  

新しい均一仮想マシン スケール セットを容量予約グループに関連付けるには、*Microsoft.Compute* プロバイダーへの次の PUT 要求を作成します。

```rest
PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachineScaleSets/{VMScaleSetName}?api-version=2021-04-01
```

次に示すように、`virtualMachineProfile` に `capacityReservationGroup` プロパティを追加します。 

```json
{ 
    "name": "<VMScaleSetName>", 
    "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachineScaleSets/{VMScaleSetName}", 
    "type": "Microsoft.Compute/virtualMachineScaleSets", 
    "location": "eastus", 
    "sku": { 
        "name": "Standard_D2s_v3", 
        "tier": "Standard", 
        "capacity": 3 
}, 
"properties": { 
    "virtualMachineProfile": { 
        "capacityReservation": { 
            "capacityReservationGroup":{ 
                "id":"subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/CapacityReservationGroup/{CapacityReservationGroupName}" 
            } 
         }, 
        "osProfile": { 
            … 
        }, 
        "storageProfile": { 
            … 
        }, 
        "networkProfile": { 
            …,
            "extensionProfile": { 
                … 
            } 
        } 
    } 
```

### <a name="cli"></a>[CLI](#tab/cli1)

`az vmss create` を使用して新しい仮想マシン スケール セットを作成し、`capacity-reservation-group` プロパティを追加して、スケール セットを既存の容量予約グループに関連付けます。 次の例では、米国東部の場所に Standard_Ds1_v2 VM の均一スケール セットを作成し、そのスケール セットを容量予約グループに関連付けます。

```azurecli-interactive
az vmss create 
--resource-group myResourceGroup 
--name myVMSS 
--location eastus 
--vm-sku Standard_Ds1_v2 
--image UbuntuLTS 
--capacity-reservation-group /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/capacityReservationGroups/{capacityReservationGroupName} 
```


### <a name="powershell"></a>[PowerShell](#tab/powershell1) 

`New-AzVmss` を使用して新しい仮想マシン スケール セットを作成し、`CapacityReservationGroupId` プロパティを追加して、スケール セットを既存の容量予約グループに関連付けます。 次の例では、米国東部の場所に Standard_Ds1_v2 VM の均一スケール セットを作成し、そのスケール セットを容量予約グループに関連付けます。

```powershell-interactive
$vmssName = <"VMSSNAME">
$vmPassword = ConvertTo-SecureString <"PASSWORD"> -AsPlainText -Force
$vmCred = New-Object System.Management.Automation.PSCredential(<"USERNAME">, $vmPassword)
New-AzVmss
–Credential $vmCred
-VMScaleSetName $vmssName
-ResourceGroupName "myResourceGroup"
-CapacityReservationGroupId "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/capacityReservationGroups/{capacityReservationGroupName}"
-PlatformFaultDomainCount 2
```

詳細については、Azure PowerShell コマンド「[New-AzVmss](/powershell/module/az.compute/new-azvmss)」を参照してください。

### <a name="arm-template"></a>[ARM テンプレート](#tab/arm1)

 [ARM テンプレート](../azure-resource-manager/templates/overview.md) は JavaScript Object Notation (JSON) ファイルであり、プロジェクトのインフラストラクチャと構成が定義されています。 このテンプレートでは、宣言型の構文が使用されています。 宣言型の構文では、デプロイしようとしているものを、デプロイを作成する一連のプログラミング コマンドを記述しなくても記述できます。 

ARM テンプレートを使用して、関連するリソースのグループをデプロイできます。 1 つのテンプレートで、容量予約グループと容量予約を作成できます。 テンプレートは、Azure portal、Azure CLI、Azure PowerShell からデプロイすることも、継続的インテグレーション/継続的デリバリー (CI/CD) パイプラインから呼び出すこともできます。 

お使いの環境が前提条件を満たし、ARM テンプレートを使い慣れている場合は、この[容量予約を使用して仮想マシン スケール セットを作成する](https://github.com/Azure/on-demand-capacity-reservation/blob/main/VirtualMachineScaleSetWithReservation.json)テンプレートを使用してください。 

--- 
<!-- The three dashes above show that your section of tabbed content is complete. Don't remove them :) -->


## <a name="associate-an-existing-virtual-machine-scale-set-to-capacity-reservation-group"></a>既存の仮想マシン スケール セットを容量予約グループに関連付ける 

パブリック プレビューの期間中、既存の均一仮想マシン スケール セットを容量予約グループに関連付けるには、最初にスケール セットの割り当てを解除してから、再割り当て時に関連付けを行う必要があります。 これにより、すべてのスケール セット VM で、再割り当て時に容量予約が消費されるようになります。

### <a name="important-notes-on-upgrade-policies"></a>アップグレード ポリシーに関する重要な注意事項 

- **自動アップグレード** - このモードでは、スケール セット VM インスタンスが容量予約グループに自動的に関連付けられ、ユーザーが他の操作を行う必要はありません。 スケール セット VM が再割り当てされると、予約容量の消費が開始されます。
- **ローリング アップグレード** - このモードでは、スケール セット VM インスタンスが容量予約グループに関連付けられ、ユーザーが他の操作を行う必要はありません。 ただし、バッチ間に任意の一時停止時間が設けられたバッチで更新されます。 スケール セット VM が再割り当てされると、予約容量の消費が開始されます。
- **手動アップグレード** - このモードでは、仮想マシン スケール セットが容量予約グループに関連付けられている場合、スケール セット VM インスタンスに対しては何も発生しません。 [最新のスケール セット モデルでアップグレード](../virtual-machine-scale-sets/virtual-machine-scale-sets-upgrade-scale-set.md#how-to-bring-vms-up-to-date-with-the-latest-scale-set-model)して、各スケール セット VM を個別に更新する必要があります。

### <a name="api"></a>[API](#tab/api2)

1. 仮想マシン スケール セットの割り当てを解除します。 

    ```rest
    POST https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourcegroupname}/providers/Microsoft.Compute/virtualMachineScaleSets/{VMScaleSetName}/deallocate?api-version=2021-04-01
    ```

1. `capacityReservationGroup` プロパティをスケール セット モデルに追加します。 *Microsoft.Compute* プロバイダーへの次の PUT 要求を作成します。

    ```rest
    PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourcegroupname}/providers/Microsoft.Compute/virtualMachineScaleSets/{VMScaleSetName}?api-version=2021-04-01
    ```

    要求本文に、`capacityReservationGroup` プロパティを含めます。

    ```json
    "location": "eastus",
    "properties": {
        "virtualMachineProfile": {
             "capacityReservation": {
                      "capacityReservationGroup": {
                            "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/capacityReservationGroups/{capacityReservationGroupName}"
                      }
                }
        }
    }
    ```

### <a name="cli"></a>[CLI](#tab/cli2)

1. 仮想マシン スケール セットの割り当てを解除します。 

    ```azurecli-interactive
    az vmss deallocate 
    --location eastus
    --resource-group myResourceGroup 
    --name myVMSS 
    ```

1. スケール セットを容量予約グループに関連付けます。 

    ```azurecli-interactive
    az vmss update 
    --resource-group myResourceGroup 
    --name myVMSS 
    --capacity-reservation-group /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/capacityReservationGroups/{capacityReservationGroupName}
    ```

### <a name="powershell"></a>[PowerShell](#tab/powershell2) 

1. 仮想マシン スケール セットの割り当てを解除します。 

    ```powershell-interactive
    Stop-AzVmss
    -ResourceGroupName "myResourceGroup”
    -VMScaleSetName "myVmss”
    ```

1. スケール セットを容量予約グループに関連付けます。 

    ```powershell-interactive
    $vmss =
    Get-AzVmss
    -ResourceGroupName "myResourceGroup"
    -VMScaleSetName "myVmss"
    
    Update-AzVmss
    -ResourceGroupName "myResourceGroup"
    -VMScaleSetName "myVmss"
    -VirtualMachineScaleSet $vmss
    -CapacityReservationGroupId "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/capacityReservationGroups/{capacityReservationGroupName}"
    ```

詳細については、Azure PowerShell コマンド「[Stop-AzVM](/powershell/module/az.compute/stop-azvmss)」、「[Get-AzVM](/powershell/module/az.compute/get-azvmss)」、「[Update-AzVM](/powershell/module/az.compute/update-azvmss)」を参照してください。

--- 
<!-- The three dashes above show that your section of tabbed content is complete. Don't remove them :) -->

## <a name="view-virtual-machine-scale-set-association-with-instance-view"></a>インスタンス ビューで仮想マシン スケール セットの関連付けを表示する 

均一仮想マシン スケール セットが容量予約グループに関連付けられると、それ以降のすべての VM 割り当ては、容量予約に対して行われます。 Azure により、グループ内で一致する容量予約が自動的に検出され、予約済みスロットが消費されます。 

### <a name="api"></a>[API](#tab/api3) 

次に示すように、容量予約グループの "*インスタンス ビュー*" では、`virtualMachinesAssociated` および `virtualMachinesAllocated` プロパティの下に新しいスケール セット VM が反映されます。 

```rest
GET https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/CapacityReservationGroups/{CapacityReservationGroupName}?$expand=instanceview&api-version=2021-04-01 
```

```json
{ 
    "name": "<CapacityReservationGroupName>", 
    "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/capacityReservationGroups/{CapacityReservationGroupName}", 
    "type": "Microsoft.Compute/capacityReservationGroups", 
    "location": "eastus" 
}, 
    "properties": { 
        "capacityReservations": [ 
            { 
                "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/capacityReservationGroups/{CapacityReservationGroupName}/capacityReservations/{CapacityReservationName}" 
            } 
        ], 
        "virtualMachinesAssociated": [ 
            { 
                "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachineScaleSets/{VMScaleSetName}/virtualMachines/{VirtualMachineId}" 
            } 
        ], 
        "instanceView": { 
            "capacityReservations": [ 
                { 
                    "name": "<CapacityReservationName>", 
                    "utilizationInfo": { 
                        "virtualMachinesAllocated": [ 
                            { 
                                "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachineScaleSets/{VMScaleSetName}/virtualMachines/{VirtualMachineId}" 
                            } 
                        ] 
                    },
                    "statuses": [ 
                        { 
                            "code": "ProvisioningState/succeeded", 
                            "level": "Info", 
                            "displayStatus": "Provisioning succeeded", 
                            "time": "2021-05-25T15:12:10.4165243+00:00" 
                        } 
                    ] 
                } 
            ] 
        } 
    } 
} 
```  

### <a name="cli"></a>[CLI](#tab/cli3)

```azurecli-interactive
az capacity reservation group show 
-g myResourceGroup
-n myCapacityReservationGroup 
``` 

### <a name="powershell"></a>[PowerShell](#tab/powershell3) 

PowerShell を使用して、インスタンス ビューで仮想マシン スケール セットと容量予約グループの関連付けを表示します。 

```powershell-interactive
$CapRes=
Get-AzCapacityReservationGroup
-ResourceGroupName <"ResourceGroupName">
-Name <"CapacityReservationGroupName">
-InstanceView

$CapRes.InstanceView.Utilizationinfo.VirtualMachinesAllocated
``` 

詳細については、Azure PowerShell コマンド「[Get-AzCapacityReservationGroup](/powershell/module/az.compute/get-azcapacityreservationGroup)」を参照してください。

### <a name="portal"></a>[ポータル](#tab/portal3)

1. [Azure portal](https://portal.azure.com) を開きます。
1. 容量予約グループに移動します
1. 左側の **[設定]** で **[リソース]** を選択します
1. テーブルで、容量予約グループに関連付けられているすべてのスケール セット VM を確認できます
--- 
<!-- The three dashes above show that your section of tabbed content is complete. Don't remove them :) -->

## <a name="region-and-availability-zones-considerations"></a>リージョンと Availability Zones に関する考慮事項 

仮想マシン スケール セットをリージョンまたは 1 つ以上の Availability Zones で作成して、データ センター レベルの障害から保護することができます。 複数ゾーンの仮想マシン スケール セットの詳細については、[Availability Zones を使用する仮想マシン スケール セット](../virtual-machine-scale-sets/virtual-machine-scale-sets-use-availability-zones.md)に関するページを参照してください。  

 
>[!IMPORTANT]
> 関連付けを成功させるには、仮想マシン スケール セットと容量予約グループの場所 (リージョンまたは Availability Zones) を一致させる必要があります。 リージョン スケール セットの場合、スケール セットと容量予約グループの間でリージョンを一致させる必要があります。 ゾーン スケール セットの場合、スケール セットと容量予約グループの間でリージョンとゾーンの両方を一致させる必要があります。 


スケール セットが複数のゾーンに分散されている場合は常に、含まれている Availability Zones 全体への均等なデプロイが試みられます。 この均等なデプロイのために、容量予約グループの予約済み VM の数量を各ゾーンで常に同じにする必要があります。 これが重要である理由の説明として、次の例について考えてみましょう。   

この例では、各ゾーンの予約済み数量が異なります。 仮想マシン スケール セットは、75 のインスタンスにスケールアウトされます。 スケール セットはゾーン間で常に均等なデプロイが試みられるため、VM の分布は次のようになります。 

| ゾーン  | 予約済み数量  | いいえ。 各ゾーンのスケール セット VM の  | 未使用の予約済み数量  | 過剰割り当て   |
|---|---|---|---|---|
| 1  | 40  | 25  | 15  | 0  |
| 2  | 20  | 25  | 0  | 5  |
| 3  | 15  | 25  | 0  | 10  |

この場合、スケール セットでは、ゾーン 1 の 15 個の未使用インスタンスに対して追加コストが発生します。 さらに、このスケールアウトは、容量予約によって保護されていない、ゾーン 2 の 5 個の VM とゾーン 3 の 10 個の VM に依存しています。 各ゾーンで容量インスタンスが 25 個ずつ予約されていれば、75 個のすべての VM が容量予約によって保護され、デプロイによって、未使用インスタンスに対する追加コストが発生することはありません。  

予約を過剰割り当てすることができるため、スケール セットは、予約の制限を超えて通常どおりスケーリングを続けることができます。 唯一の違いは、予約された数量を超えて割り当てられた VM は、容量予約 SLA の対象にならないことです。 詳細については、「[容量予約の過剰割り当て](capacity-reservation-overallocate.md)」を参照してください。


## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [スケール セットの関連付けを容量予約から削除する方法の詳細](capacity-reservation-remove-virtual-machine-scale-set.md)
