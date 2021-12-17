---
title: Azure Resource Mover で移動コレクションからリソースを削除する
description: Azure Resource Mover で移動コレクションからリソースを削除する方法について説明します。
manager: evansma
author: rayne-wiselman
ms.service: resource-move
ms.topic: how-to
ms.date: 02/22/2020
ms.author: raynew
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: a655e8f0a4e4a6d44ce8960b45991cf6e394e2db
ms.sourcegitcommit: 30e3eaaa8852a2fe9c454c0dd1967d824e5d6f81
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/22/2021
ms.locfileid: "112454322"
---
# <a name="manage-move-collections-and-resource-groups"></a>移動コレクションとリソース グループを管理する

この記事では、[Azure Resource Mover](overview.md) で移動コレクションからリソースを削除したり、移動コレクションやリソース グループを削除したりする方法について説明します。 移動コレクションは、Azure リージョン間で Azure リソースを移動するときに使用されます。

## <a name="remove-a-resource-portal"></a>リソースを削除する (ポータル)

Resource Mover ポータルで移動コレクション内のリソースを削除するには、次の手順を実行します。

1. **[リージョン間]** で、コレクションから削除するすべてのリソースを選択し、 **[削除]** を選択します。 

    ![削除する場合に選択するボタン](./media/remove-move-resources/portal-select-resources.png)

2. **[リソースの削除]** で **[削除]** をクリックします。

    ![移動コレクションからリソースを削除する場合に選択するボタン](./media/remove-move-resources/remove-portal.png)

## <a name="remove-a-move-collectionresource-group-portal"></a>移動コレクションまたはリソース グループの削除 (ポータル)

ポータルでは、移動コレクションまたはリソース グループを削除することができます。

1. 上記の手順に従って、コレクションからリソースを削除します。 リソース グループを削除する場合は、それにリソースが含まれていないことを確認してください。
2. 移動コレクションまたはリソース グループを削除します。  

## <a name="remove-a-resource-powershell"></a>リソースを削除する (PowerShell)

PowerShell コマンドレットを使用すると、MoveCollection から 1 つのリソースを削除することも、複数のリソースを削除することもできます。

### <a name="remove-a-single-resource"></a>1 つのリソースの削除

リソース (この例では、仮想ネットワーク *psdemorm-vnet*) を次のように削除します。

```azurepowershell-interactive
# Remove a resource using the resource ID
Remove-AzResourceMoverMoveResource -ResourceGroupName "RG-MoveCollection-demoRMS" -MoveCollectionName "PS-centralus-westcentralus-demoRMS" -Name "psdemorm-vnet"
```
**コマンドレットを実行した後の出力**

![移動コレクションからリソースを削除した後の出力テキスト](./media/remove-move-resources/powershell-remove-single-resource.png)

### <a name="remove-multiple-resources"></a>複数のリソースの削除

次のように、複数のリソースを削除します。

1. 依存関係の検証:

    ```azurepowershell-interactive
    $resp = Invoke-AzResourceMoverBulkRemove -ResourceGroupName "RG-MoveCollection-demoRMS" -MoveCollectionName "PS-centralus-westcentralus-demoRMS"  -MoveResource $('psdemorm-vnet') -ValidateOnly
    ```

    **コマンドレットを実行した後の出力**

    ![移動コレクションから複数のリソースを削除した後にテキストを出力する](./media/remove-move-resources/remove-multiple-validate-dependencies.png)

2. (今回の例の仮想ネットワーク psdemorm-vnet と共に) 削除する必要がある依存リソースを取得します。

    ```azurepowershell-interactive
    $resp.AdditionalInfo[0].InfoMoveResource
    ```

    **コマンドレットを実行した後の出力**

    ![削除する必要がある依存リソースを取得した後にテキストを出力する](./media/remove-move-resources/remove-multiple-get-dependencies.png)


3. 仮想ネットワークと共にあらゆるリソースを削除します。

    
    ```azurepowershell-interactive
    Invoke-AzResourceMoverBulkRemove -ResourceGroupName "RG-MoveCollection-demoRMS" -MoveCollectionName "PS-centralus-westcentralus-demoRMS"  -MoveResource $('PSDemoVM','psdemovm111', 'PSDemoRM-vnet','PSDemoVM-nsg')
    ```

    **コマンドレットを実行した後の出力**

    ![移動コレクションからすべてのリソースを削除した後にテキストを出力する](./media/remove-move-resources/remove-multiple-all.png)


## <a name="remove-a-collection-powershell"></a>コレクションを削除する (PowerShell)

次のように、サブスクリプションから移動コレクション全体を削除します。

1. 上記の手順に従い、PowerShell を使用してコレクションからリソースを削除します。
2. 次のようにコレクションを削除します。

    ```azurepowershell-interactive
    Remove-AzResourceMoverMoveCollection -ResourceGroupName "RG-MoveCollection-demoRMS" -MoveCollectionName "PS-centralus-westcentralus-demoRMS"
    ```

    **コマンドレットを実行した後の出力**
    
    ![移動コレクションを削除した後の出力テキスト](./media/remove-move-resources/remove-collection.png)

## <a name="vm-resource-state-after-removing"></a>削除後の VM リソースの状態

表にまとめられているように、移動コレクションから VM リソースを削除するとどうなるかは、リソースの状態によって異なります。

###  <a name="remove-vm-state"></a>削除後の VM 状態
**リソースの状態** | **VM** | **ネットワーク**
--- | --- | --- 
**移動コレクションに追加済み** | 移動コレクションから削除されます。 | 移動コレクションから削除されます。 
**依存関係が解決済み/準備が保留中** | 移動コレクションから削除されます。  | 移動コレクションから削除されます。 
**準備が進行中**<br/> (またはその他の進行中の状態) | エラーにより削除操作が失敗します。  | エラーにより削除操作が失敗します。
**準備が失敗** | 移動コレクションから削除されます。<br/>レプリカ ディスクを含む、ターゲット リージョンで作成されたすべてのものが削除されます。 <br/><br/> 移動中に作成されたインフラストラクチャ リソースは、手動で削除する必要があります。 | 移動コレクションから削除されます。  
**移動の開始が保留中** | 移動コレクションから削除されます。<br/><br/> VM やレプリカ ディスクなどを含む、ターゲット リージョンで作成されたすべてのものが削除されます。  <br/><br/> 移動中に作成されたインフラストラクチャ リソースは、手動で削除する必要があります。 | 移動コレクションから削除されます。
**移動の開始が失敗** | 移動コレクションから削除されます。<br/><br/> VM やレプリカ ディスクなどを含む、ターゲット リージョンで作成されたすべてのものが削除されます。  <br/><br/> 移動中に作成されたインフラストラクチャ リソースは、手動で削除する必要があります。 | 移動コレクションから削除されます。
**コミットを保留中** | ターゲット リソースが最初に削除されるようにするために、移動を破棄することをお勧めします。<br/><br/> リソースが **移動の開始が保留中** 状態に戻り、そこから続行できます。 | ターゲット リソースが最初に削除されるようにするために、移動を破棄することをお勧めします。<br/><br/> リソースが **移動の開始が保留中** 状態に戻り、そこから続行できます。 
**コミットが失敗** | ターゲット リソースが最初に削除されるようにするために、破棄することをお勧めします。<br/><br/> リソースが **移動の開始が保留中** 状態に戻り、そこから続行できます。 | ターゲット リソースが最初に削除されるようにするために、移動を破棄することをお勧めします。<br/><br/> リソースが **移動の開始が保留中** 状態に戻り、そこから続行できます。
**破棄が完了** | リソースが **移動の開始が保留中** 状態に戻ります。<br/><br/> VM、レプリカ ディスク、コンテナーなど、ターゲットで作成されたすべてのものとともに、移動コレクションから削除されます。  <br/><br/> 移動中に作成されたインフラストラクチャ リソースは、手動で削除する必要があります。 <br/><br/> 移動中に作成されたインフラストラクチャ リソースは、手動で削除する必要があります。 |  リソースが **移動の開始が保留中** 状態に戻ります。<br/><br/> 移動コレクションから削除されます。
**破棄が失敗** | ターゲット リソースが最初に削除されるようにするために、移動を破棄することをお勧めします。<br/><br/> その後、リソースが **移動の開始が保留中** 状態に戻り、そこから続行できます。 | ターゲット リソースが最初に削除されるようにするために、移動を破棄することをお勧めします。<br/><br/> その後、リソースが **移動の開始が保留中** 状態に戻り、そこから続行できます。
**ソースの削除を保留中** | 移動コレクションから削除されます。<br/><br/> ターゲット リージョンで作成されたものはどれも削除されません。  | 移動コレクションから削除されます。<br/><br/> ターゲット リージョンで作成されたものはどれも削除されません。
**ソースの削除が失敗** | 移動コレクションから削除されます。<br/><br/> ターゲット リージョンで作成されたものはどれも削除されません。 | 移動コレクションから削除されます。<br/><br/> ターゲット リージョンで作成されたものはどれも削除されません。
**移動完了** | 移動コレクションから削除されます。<br/><br/> ターゲットまたはソース リージョンで作成されたものはどれも削除されません。 |  移動コレクションから削除されます。<br/><br/> ターゲットまたはソース リージョンで作成されたものはどれも削除されません。

## <a name="sql-resource-state-after-removing"></a>削除後の SQL リソースの状態

表にまとめられているように、移動コレクションから Azure SQL リソースを削除するとどうなるかは、リソースの状態によって異なります。

**リソースの状態** | **SQL** 
--- | --- 
**移動コレクションに追加済み** | 移動コレクションから削除されます。 
**依存関係が解決済み/準備が保留中** | 移動コレクションから削除されます。 
**準備が進行中**<br/> (またはその他の進行中の状態)  | エラーにより削除操作が失敗します。 
**準備が失敗** | 移動コレクションから削除されます。<br/><br/>ターゲット リージョンで作成されたすべてのものが削除されます。 
**移動の開始が保留中** |  移動コレクションから削除されます。<br/><br/>ターゲット リージョンで作成されたすべてのものが削除されます。 SQL データベースはこの時点では存在し、削除されます。 
**移動の開始が失敗** | 移動コレクションから削除されます。<br/><br/>ターゲット リージョンで作成されたすべてのものが削除されます。 SQL データベースはこの時点では存在し、削除する必要があります。 
**コミットを保留中** | ターゲット リソースが最初に削除されるようにするために、移動を破棄することをお勧めします。<br/><br/> リソースが **移動の開始が保留中** 状態に戻り、そこから続行できます。
**コミットが失敗** | ターゲット リソースが最初に削除されるようにするために、移動を破棄することをお勧めします。<br/><br/> リソースが **移動の開始が保留中** 状態に戻り、そこから続行できます。 
**破棄が完了** |  リソースが **移動の開始が保留中** 状態に戻ります。<br/><br/> SQL データベースなど、ターゲットで作成されたすべてのものとともに、移動コレクションから削除されます。 
**破棄が失敗** | ターゲット リソースが最初に削除されるようにするために、移動を破棄することをお勧めします。<br/><br/> その後、リソースが **移動の開始が保留中** 状態に戻り、そこから続行できます。 
**ソースの削除を保留中** | 移動コレクションから削除されます。<br/><br/> ターゲット リージョンで作成されたものはどれも削除されません。 
**ソースの削除が失敗** | 移動コレクションから削除されます。<br/><br/> ターゲット リージョンで作成されたものはどれも削除されません。 
**移動完了** | 移動コレクションから削除されます。<br/><br/> ターゲットまたはソース リージョンで作成されたものはどれも削除されません。

## <a name="next-steps"></a>次のステップ

Resource Mover を使用して、別のリージョンへの [VM の移動](tutorial-move-region-virtual-machines.md)を試行する。
