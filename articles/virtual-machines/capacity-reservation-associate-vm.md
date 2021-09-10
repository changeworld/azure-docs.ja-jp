---
title: 仮想マシンを容量予約グループに関連付ける (プレビュー)
description: 新規または既存の仮想マシンを容量予約グループに関連付ける方法について説明します。
author: vargupt
ms.author: vargupt
ms.service: virtual-machines
ms.topic: how-to
ms.date: 08/09/2021
ms.reviewer: cynthn, jushiman
ms.custom: template-how-to
ms.openlocfilehash: 28c1d138cc4a2993caf2b162b9363bc33b6666b7
ms.sourcegitcommit: 7b6ceae1f3eab4cf5429e5d32df597640c55ba13
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/31/2021
ms.locfileid: "123273293"
---
# <a name="associate-a-vm-to-a-capacity-reservation-group-preview"></a>VM を容量予約グループに関連付ける (プレビュー) 

この記事では、新規または既存の仮想マシンを容量予約グループに関連付ける手順について説明します。 容量予約の詳細については、[概要について説明した記事](capacity-reservation-overview.md)を参照してください。 

> [!IMPORTANT]
> 容量予約は現在パブリック プレビュー段階にあります。
> このプレビュー バージョンはサービス レベル アグリーメントなしで提供されており、運用環境のワークロードに使用することは推奨されません。 特定の機能はサポート対象ではなく、機能が制限されることがあります。 詳しくは、[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)に関するページをご覧ください。


## <a name="register-for-capacity-reservation"></a>容量予約を登録する 

容量予約の機能を使う前に、[プレビュー版を利用するためにサブスクリプションを登録](capacity-reservation-overview.md#register-for-capacity-reservation)しなければなりません。 登録が完了するまでに数分かかる場合があります。 Azure CLI または PowerShell のいずれかを使用して、機能の登録を完了することができます。

## <a name="associate-a-new-vm"></a>新しい VM を関連付ける

新しい VM を容量予約グループに関連付けるには、仮想マシンのプロパティとしてグループを明示的に参照する必要があります。 このリファレンスでは、グループ内の照合予約を、重要度の低いアプリケーションや、それを使用することを意図していないワークロードによって偶発的に消費されるのを防ぎます。  

### <a name="api"></a>[API](#tab/api1)

VM に `capacityReservationGroup` プロパティを追加するには、*Microsoft.Compute* プロバイダーへの次の PUT 要求を作成します。

```rest
PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachines/{VirtualMachineName}?api-version=2021-04-01
```

要求本文に、次のように `capacityReservationGroup` プロパティを含めます。

```json
{ 
  "location": "eastus", 
  "properties": { 
    "hardwareProfile": { 
      "vmSize": "Standard_D2s_v3" 
    }, 
    … 
   "CapacityReservation":{ 
    "capacityReservationGroup":{ 
        "id":"subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/CapacityReservationGroups/{CapacityReservationGroupName}" 
    } 
    "storageProfile": { 
    … 
    }, 
    "osProfile": { 
    … 
    }, 
    "networkProfile": { 
     …     
    } 
  } 
} 
```

### <a name="portal"></a>[ポータル](#tab/portal1)

<!-- no images necessary if steps are straightforward --> 

1. [Azure portal](https://portal.azure.com) を開きます。
1. 検索バーに「**仮想マシン**」と入力します。
1. *[サービス]* で、 **[仮想マシン]** を選択します。
1. *[仮想マシン]* ページで、 **[作成]** を選択してから、 **[仮想マシン]** を選択します。
1. *[基本]* タブの *[プロジェクトの詳細]* で、適切な **サブスクリプション** を選択してから、新しい **リソース グループ** を作成するか、既存のものを使用するかを選択します。
1. *[インスタンスの詳細]* で、仮想マシンの **名前** を入力し、**リージョン** を選択します。
1. **イメージ** と **VM のサイズ** を選択します。
1. *[管理者アカウント]* で、**ユーザー名** と **パスワード** を指定します。
    1. パスワードは 12 文字以上で、定義された複雑さの要件を満たす必要があります。
1. *[受信ポートの規則]* で **[選択したポートを許可する]** を選択し、ドロップダウンから **[RDP]** (3389) と **[HTTP]** (80) を選択します。
1. *[詳細設定]* セクションに移動します。
1. **[容量予約]** ドロップダウンで、VM を関連付ける容量予約グループを選択します。
1. **[確認および作成]** ボタンを選択します。 
1. 検証の実行後、 **[作成]** ボタンを選択します。 
1. デプロイが完了したら、 **[リソースに移動]** を選択します。


### <a name="powershell"></a>[PowerShell](#tab/powershell1)

`New-AzVM` を使用して新しい VM を作成し、`CapacityReservationGroupId` プロパティを追加して既存の容量予約グループに関連付けます。 次の例では、米国東部の場所に Standard_D2s_v3 VM を作成し、その VM を容量予約グループに関連付けます。

```powershell-interactive
New-AzVm
-ResourceGroupName "myResourceGroup"
-Name "myVM"
-Location "eastus"
-VirtualNetworkName "myVnet"
-SubnetName "mySubnet"
-SecurityGroupName "myNetworkSecurityGroup"
-PublicIpAddressName "myPublicIpAddress"
-OpenPorts 80,3389
-Size "Standard_D2s_v3"
-CapacityReservationGroupId "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/capacityReservationGroups/{capacityReservationGroupName}"
```

詳細については、Azure PowerShell コマンド「[New-AzVM](/powershell/module/az.compute/new-azvm)」を参照してください。

### <a name="arm-template"></a>[ARM テンプレート](#tab/arm1)

 [ARM テンプレート](/azure/azure-resource-manager/templates/overview) は JavaScript Object Notation (JSON) ファイルであり、プロジェクトのインフラストラクチャと構成が定義されています。 このテンプレートでは、宣言型の構文が使用されています。 宣言型の構文では、デプロイしようとしているものを、デプロイを作成する一連のプログラミング コマンドを記述しなくても記述できます。 

ARM テンプレートを使用して、関連するリソースのグループをデプロイできます。 1 つのテンプレートで、容量予約グループと容量予約を作成できます。 テンプレートは、Azure portal、Azure CLI、Azure PowerShell からデプロイすることも、継続的インテグレーション/継続的デリバリー (CI/CD) パイプラインから呼び出すこともできます。 

お使いの環境が前提条件を満たし、ARM テンプレートを使い慣れている場合は、この[容量予約を使用して VM を作成する](https://github.com/Azure/on-demand-capacity-reservation/blob/main/VirtualMachineWithReservation.json)テンプレートを使用してください。 


--- 
<!-- The three dashes above show that your section of tabbed content is complete. Don't remove them :) -->


## <a name="associate-an-existing-vm"></a>既存の VM を関連付ける 

容量予約がプレビュー段階の場合、既存の VM を容量予約グループに関連付けるには、最初に VM の割り当てを解除してから、再割り当て時に関連付けを行う必要があります。 このプロセスにより、VM は予約内のいずれかの空のスポットを使用します。 

### <a name="api"></a>[API](#tab/api2)

1. VM の割り当てを解除します。 

    ```rest
    PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourcegroupname}/providers/Microsoft.Compute/virtualMachines/{VirtualMachineName}/deallocate?api-version=2021-04-01
    ```

1. `capacityReservationGroup` プロパティを VM に追加します。 *Microsoft.Compute* プロバイダーへの次の PUT 要求を作成します。

    ```rest
    PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachines/{VirtualMachineName}?api-version=2021-04-01
    ```

    要求本文に、`capacityReservationGroup` プロパティを含めます。 
    
    ```json
    {
    "location": "eastus",
    "properties": {
        "capacityReservation": {
            "capacityReservationGroup": {
                "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/capacityReservationGroups/{capacityReservationGroupName}"
            }
        }
    }
    }
    ```


### <a name="portal"></a>[ポータル](#tab/portal2)

1. [Azure portal](https://portal.azure.com) を開きます。
1. 仮想マシンに移動します。
1. 左側の **[概要]** を選択します。
1. ページ上部にある **[停止]** を選択して VM の割り当てを解除します。 
1. 左側の **[構成]** に移動します。
1. **[容量予約グループ]** ドロップダウンで、VM を関連付けるグループを選択します。 


### <a name="powershell"></a>[PowerShell](#tab/powershell2)

1. VM の割り当てを解除する

    ```powershell-interactive
    Stop-AzVM
    -ResourceGroupName "myResourceGroup"
    -Name "myVM"
    ```

1. VM を容量予約グループに関連付ける

    ```powershell-interactive
    $VirtualMachine =
    Get-AzVM
    -ResourceGroupName "myResourceGroup"
    -Name "myVM"
    
    Update-AzVM
    -ResourceGroupName "myResourceGroup"
    -VM $VirtualMachine
    -CapacityReservationGroupId "subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/capacityReservationGroups/{CapacityReservationGroupName}"
    ```

詳細については、Azure PowerShell コマンド「[Stop-AzVM](/powershell/module/az.compute/stop-azvm)」、「[Get-AzVM](/powershell/module/az.compute/get-azvm)」、「[Update-AzVM](/powershell/module/az.compute/update-azvm)」を参照してください。

--- 
<!-- The three dashes above show that your section of tabbed content is complete. Don't remove them :) -->


## <a name="view-vm-association-with-instance-view"></a>インスタンス ビューで VM の関連付けを表示する 

`capacityReservationGroup` プロパティが設定されると、VM とグループの間に関連付けが存在するようになります。 Azure により、グループ内で一致する容量予約が自動的に検出され、予約済みスロットが消費されます。 次に示すように、容量予約の *インスタンス ビュー* では、`virtualMachinesAllocated` プロパティの下に新しい VM が反映されます。 

### <a name="api"></a>[API](#tab/api3)

```rest
GET https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/CapacityReservationGroups/{capacityReservationGroupName}?$expand=instanceView&api-version=2021-04-01 
```

```json
{
   "name":"{CapacityReservationGroupName}",
   "id":"/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/capacityReservationGroups/{CapacityReservationGroupName}",
   "type":"Microsoft.Compute/capacityReservationGroups",
   "location":"eastus",
   "properties":{
      "capacityReservations":[
         {
            "id":"/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/capacityReservationGroups/ {CapacityReservationGroupName}/capacityReservations/{CapacityReservationName}"
         }
      ],
      "virtualMachinesAssociated":[
         {
            "id":"/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachines/{myVM}"
         }
      ],
      "instanceView":{
         "capacityReservations":[
            {
               "name":"{CapacityReservationName}",
               "utilizationInfo":{
                  "virtualMachinesAllocated":[
                     {
                        "id":"/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachines/{myVM}"
                     }
                  ]
               },
               "statuses":[
                  {
                     "code":"ProvisioningState/succeeded",
                     "level":"Info",
                     "displayStatus":"Provisioning succeeded",
                     "time":"2021-05-25T15:12:10.4165243+00:00"
                  }
               ]
            }
         ]
      }
   }
} 
``` 
### <a name="powershell"></a>[PowerShell](#tab/powershell3)

```powershell-interactive
$CapRes=
Get-AzCapacityReservation
-ResourceGroupName <"ResourceGroupName">
-ReservationGroupName] <"CapacityReservationGroupName">
-Name <"CapacityReservationName">
-InstanceView

$CapRes.InstanceView.Utilizationinfo.VirtualMachinesAllocated
```

詳細については、Azure PowerShell コマンド「[Get-AzCapacityReservation](/powershell/module/az.compute/get-azcapacityreservation)」を参照してください。


### <a name="portal"></a>[ポータル](#tab/portal3)

1. [Azure portal](https://portal.azure.com) を開きます。
1. 容量予約グループに移動します
1. 左側の **[設定]** で **[リソース]** を選択します。
1. テーブルを参照して、容量予約グループに関連付けられているすべての VM を確認します。  

--- 
<!-- The three dashes above show that your section of tabbed content is complete. Don't remove them :) -->


## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [容量予約グループに対する VM の関連付けを削除する](capacity-reservation-remove-vm.md)