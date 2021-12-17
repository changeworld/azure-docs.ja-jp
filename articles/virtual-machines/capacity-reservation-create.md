---
title: Azure で容量予約を作成する (プレビュー)
description: 容量予約を作成して、Azure リージョンまたは可用性ゾーンにコンピューティング容量を予約する方法について説明します。
author: vargupt
ms.author: vargupt
ms.service: virtual-machines
ms.topic: how-to
ms.date: 08/09/2021
ms.reviewer: cynthn, jushiman
ms.custom: template-how-to
ms.openlocfilehash: 51139277b0c8da4fb3dfdd61bf826305657280b5
ms.sourcegitcommit: 4abfec23f50a164ab4dd9db446eb778b61e22578
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/15/2021
ms.locfileid: "130065016"
---
# <a name="create-a-capacity-reservation-preview"></a>容量予約を作成する (プレビュー)

容量予約は、常に容量予約グループの一部として作成されます。 最初の手順は、グループを作成してから (適切なグループがまだない場合) 予約を作成することです。 正常に作成されると、仮想マシンで予約をすぐに使用できるようになります。 容量は、予約が削除されていない限り、使用するために予約されています。     

適切な形式の容量予約グループの要求は、容量が予約されないため、常に成功します。 予約のコンテナーとして機能するだけです。 ただし、VM シリーズに必要なクォータがない場合、または Azure に要求を満たすための十分な容量がない場合、容量予約の要求は失敗することがあります。 さらにクォータを要求するか、別の VM サイズ、場所、またはゾーンの組み合わせを試してください。 

容量予約の作成は、完全に成功するか、失敗するかです。 10 個のインスタンスを予約する要求の場合は、10 個すべてが割り当てられた場合にのみ成功が返されます。 それ以外の場合、容量予約の作成は失敗します。 

> [!IMPORTANT]
> 容量予約は現在パブリック プレビュー段階にあります。
> このプレビュー バージョンはサービス レベル アグリーメントなしで提供されており、運用環境のワークロードに使用することは推奨されません。 特定の機能はサポート対象ではなく、機能が制限されることがあります。 詳しくは、[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)に関するページをご覧ください。


## <a name="considerations"></a>考慮事項

容量予約は、次の規則を満たしている必要があります。 
- location パラメーターは、親の容量予約グループの location プロパティと一致している必要があります。 一致していない場合は、エラーが発生します。 
- ターゲット リージョンで VM サイズが使用可能である必要があります。 それ以外の場合、予約の作成は失敗します。 
- サブスクリプションには、VM シリーズとそのリージョン全体に予約されている VM の数量以上の十分な承認済みクォータがある必要があります。 必要な場合は、[さらにクォータを要求](../azure-portal/supportability/per-vm-quota-requests.md)します。
- 各容量予約グループでは、特定の VM サイズに対して 1 つの予約のみを持つことができます。 たとえば、VM サイズ `Standard_D2s_v3` に対して作成できる容量予約は 1 つだけです。 同じ容量予約グループの `Standard_D2s_v3` に 2 つ目の予約を作成しようとすると、エラーが発生します。 ただし、`Standard_D4s_v3`、`Standard_D8s_v3` などの他の VM サイズの場合は、同じグループに別の予約を作成できます。  
- ゾーンをサポートする容量予約グループの場合、各予約の種類は、**VM サイズ** と **ゾーン** の組み合わせによって定義されます。 たとえば、`Zone 1` に `Standard_D2s_v3` の 1 つの容量予約、`Zone 2` に `Standard_D2s_v3` の別の容量予約、および `Zone 3` に `Standard_D2s_v3` の 3 番目の容量予約がサポートされます。


## <a name="create-a-capacity-reservation"></a>容量予約を作成する 

### <a name="api"></a>[API](#tab/api1)

1. 容量予約グループを作成する 

    容量予約グループを作成するには、*Microsoft.Compute* プロバイダーに対する次の PUT 要求を作成します。 
    
    ```rest
    PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/CapacityReservationGroups/{CapacityReservationGroupName}&api-version=2021-04-01
    ``` 
    
    要求本文に、次の内容を含めます。 
    
    ```json
    { 
      "location":"eastus"
    } 
    ```
    
    このグループは、米国東部の場所の予約を含めるために作成されます。 
    
    この例では、作成時にゾーンが指定されなかったため、グループではリージョンの予約のみがサポートされます。 ゾーンのグループを作成するには、次に示すように、要求本文に追加のパラメーター *zones* を渡します。 
    
    ```json
    { 
      "location":"eastus",
      "zones": ["1", "2", "3"] 
    } 
    ```
 
1. 容量予約を作成する 

    予約を作成するには、*Microsoft.Compute* プロバイダーに対する次の PUT 要求を作成します。 
    
    ```rest
    PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/CapacityReservationGroups/{CapacityReservationGroupName}/capacityReservations/{capacityReservationName}?api-version=2021-04-01 
    ```
    
    要求本文に、次の内容を含めます。 
    
    ```json
    { 
      "location": "eastus", 
      "sku": { 
        "name": "Standard_D2s_v3", 
        "capacity": 5 
      }, 
     "tags": { 
            "environment": "testing" 
    } 
    ```
    
    上記の要求では、米国東部の場所に 5 つの D2s_v3 VM サイズの予約が作成されます。 


### <a name="portal"></a>[ポータル](#tab/portal1)

<!-- no images necessary if steps are straightforward --> 

1. [Azure portal](https://portal.azure.com) を開きます。
1. 検索バーで、「**容量予約グループ**」と入力します
1. オプションから **[容量予約グループ]** を選択します
1. **[作成]**
1. *[基本]* タブで、容量予約グループを作成します。
    1. **[サブスクリプション]** を選択します
    1. **[リソース グループ]** を選択または作成します
    1. グループに **[名前]** を付けます 
    1. **[リージョン]** を選択します 
    1. 必要に応じて、 **[可用性ゾーン]** を選択するか、ゾーンを指定せずに Azure による選択を許可します
1. **[次へ]** を選択します
1. *[予約]* タブで、少なくとも 1 つの容量予約を作成します。
    1. 各予約に **[予約名]** と VM の **[インスタンス]** の数量を指定し、一意の **[VM サイズ]** を選択します
    1. *[コスト/月]* 列には、選択内容に基づいて課金情報が表示されます
1. **[次へ]** を選択します
1. *[タグ]* タブで、必要に応じてタグを作成します
1. **[次へ]** を選択します 
1. *[レビューと作成]* タブで、容量予約グループの情報を確認します
1. **[作成]**


### <a name="cli"></a>[CLI](#tab/cli1)

1. 容量予約を作成する前に、`az group create` を使用してリソース グループを作成します。 次の例では、リソース グループ *myResourceGroup* を米国東部の場所に作成しています。

    ```azurecli-interactive
    az group create 
    -l eastus 
    -g myResourceGroup
    ```

1. `az capacity reservation group create` を使用して容量予約グループを作成します。 次の例では、米国東部の場所の 3 つの可用性ゾーンすべてにグループ *myCapacityReservationGroup* を作成しています。

    ```azurecli-interactive
    az capacity reservation group create 
    -n myCapacityReservationGroup 
    -l eastus 
    -g myResourceGroup 
    --zones 1 2 3 
    ```

1. 容量予約グループを作成したら、`az capacity reservation create` を使用して新しい容量予約を作成します。 次の例では、米国東部のゾーン 1 に Standard_D2s_v3 VM サイズの 5 つの *myCapacityReservation* を作成します。

    ```azurecli-interactive
    az capacity reservation create 
    -c myCapacityReservationGroup 
    -n myCapacityReservation 
    -l eastus 
    -g myResourceGroup 
    --sku Standard_D2s_v3 
    --capacity 5 
    --zone 1
    ```

### <a name="powershell"></a>[PowerShell](#tab/powershell1)

1. 容量予約を作成する前に、`New-AzResourceGroup` を使用してリソース グループを作成します。 次の例では、リソース グループ *myResourceGroup* を米国東部の場所に作成しています。

    ```powershell-interactive
    New-AzResourceGroup
    -ResourceGroupName "myResourceGroup"
    -Location "eastus"
    ```

1. `New-AzCapacityReservationGroup` を使用して容量予約グループを作成します。 次の例では、米国東部の場所の 3 つの可用性ゾーンすべてにグループ *myCapacityReservationGroup* を作成しています。

    ```powershell-interactive
    New-AzCapacityReservationGroup
    -ResourceGroupName "myResourceGroup"
    -Location "eastus"
    -Zone "1","2","3"
    -Name "myCapacityReservationGroup"
    ```

1. 容量予約グループを作成したら、`New-AzCapacityReservation` を使用して新しい容量予約を作成します。 次の例では、米国東部のゾーン 1 に Standard_D2s_v3 VM サイズの 5 つの *myCapacityReservation* を作成します。

    ```powershell-interactive
    New-AzCapacityReservation
    -ResourceGroupName "myResourceGroup"
    -Location "eastus"
    -Zone "1"
    -ReservationGroupName "myCapacityReservationGroup"
    -Name "myCapacityReservation"
    -Sku "Standard_D2s_v3"
    -CapacityToReserve 5
    ```

詳細については、Azure PowerShell コマンド「[New-AzResourceGroup](/powershell/module/az.Resources/new-azresourcegroup)」、「[New-AzCapacityReservationGroup](/powershell/module/az.compute/new-azcapacityreservationgroup)」、「[New-AzCapacityReservation](/powershell/module/az.compute/new-azcapacityreservation)」を参照してください。


### <a name="arm-template"></a>[ARM テンプレート](#tab/arm1)

 [ARM テンプレート](../azure-resource-manager/templates/overview.md) は JavaScript Object Notation (JSON) ファイルであり、プロジェクトのインフラストラクチャと構成が定義されています。 このテンプレートでは、宣言型の構文が使用されています。 宣言型の構文では、デプロイしようとしているものを、デプロイを作成する一連のプログラミング コマンドを記述しなくても記述できます。 

ARM テンプレートを使用して、関連するリソースのグループをデプロイできます。 1 つのテンプレートで、容量予約グループと容量予約を作成できます。 テンプレートは、Azure portal、Azure CLI、Azure PowerShell からデプロイすることも、継続的インテグレーション/継続的デリバリー (CI/CD) パイプラインから呼び出すこともできます。 

環境で前提条件が満たされていて、ARM テンプレートの使用に慣れている場合は、次のいずれかのテンプレートを使用します。 

- [ゾーンの容量予約の作成](https://github.com/Azure/on-demand-capacity-reservation/blob/main/ZonalCapacityReservation.json)
- [容量予約を持つ VM の作成](https://github.com/Azure/on-demand-capacity-reservation/blob/main/VirtualMachineWithReservation.json)
- [容量予約を使用して Virtual Machine Scale Sets を作成する](https://github.com/Azure/on-demand-capacity-reservation/blob/main/VirtualMachineScaleSetWithReservation.json)


--- 
<!-- The three dashes above show that your section of tabbed content is complete. Don't remove them :) -->

## <a name="check-on-your-capacity-reservation"></a>容量予約を確認する 

正常に作成されると、VM で容量予約をすぐに使用できるようになります。 

### <a name="api"></a>[API](#tab/api2)

```rest
GET  
https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/CapacityReservationGroups/{CapacityReservationGroupName}/capacityReservations/{capacityReservationName}?api-version=2021-04-01
```
 
```json
{ 
    "name": "<CapacityReservationName>", 
    "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/capacityReservationGroups/{CapacityReservationGroupName}/capacityReservations/{CapacityReservationName}", 
    "type": "Microsoft.Compute/capacityReservationGroups/capacityReservations", 
    "location": "eastus", 
    "tags": { 
        "environment": "testing" 
    }, 
    "sku": { 
        "name": "Standard_D2s_v3", 
        "capacity": 5 
    }, 
    "properties": { 
        "reservationId": "<reservationId>", 
         "provisioningTime": "<provisioningTime>", 
         "provisioningState": "Updating" 
    } 
} 
```

### <a name="cli"></a>[CLI](#tab/cli2)

 ```azurecli-interactive
 az capacity reservation show 
 -c myCapacityReservationGroup 
 -n myCapacityReservation 
 -g myResourceGroup
 ```

### <a name="powershell"></a>[PowerShell](#tab/powershell2)

容量予約を確認します。

```powershell-interactive
Get-AzCapacityReservation
-ResourceGroupName <"ResourceGroupName">
-ReservationGroupName <"CapacityReservationGroupName">
-Name <"CapacityReservationName">
```

予約されている VM サイズと数量を確認するには、次のコマンドを使用します。 

```powershell-interactive
$CapRes =
Get-AzCapacityReservation
-ResourceGroupName <"ResourceGroupName">
-ReservationGroupName <"CapacityReservationGroupName">
-Name <"CapacityReservationName">

$CapRes.sku
```

詳細については、Azure PowerShell コマンド「[Get-AzCapacityReservation](/powershell/module/az.compute/get-azcapacityreservation)」を参照してください。

### <a name="portal"></a>[ポータル](#tab/portal3)

1. [Azure portal](https://portal.azure.com) を開きます。
1. 検索バーで、「**容量予約グループ**」と入力します
1. オプションから **[容量予約グループ]** を選択します
1. 一覧から、先ほど作成した容量予約グループ名を選択します
1. 左側の **[概要]** を選択します
1. **[予約]** を選択します
1. このビューでは、グループ内のすべての予約と、予約された VM サイズおよび数量を確認できます
--- 
<!-- The three dashes above show that your section of tabbed content is complete. Don't remove them :) -->

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [容量予約を変更する方法を確認する](capacity-reservation-modify.md)
