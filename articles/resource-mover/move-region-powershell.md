---
title: Azure Resource Mover で PowerShell を使用してリソースをリージョン間で移動する
description: Azure Resource Mover で PowerShell を使用してリソースをリージョン間で移動する方法について説明します。
manager: evansma
author: rayne-wiselman
ms.service: resource-move
ms.topic: how-to
ms.date: 09/10/2020
ms.author: raynew
ms.openlocfilehash: 176f12a0a06a5bcae601463e30189bc139d3531f
ms.sourcegitcommit: c95e2d89a5a3cf5e2983ffcc206f056a7992df7d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/24/2020
ms.locfileid: "95543852"
---
# <a name="move-resources-across-regions-in-powershell"></a>PowerShell でリソースをリージョン間で移動する

Azure Resource Mover で PowerShell を使用して Azure リソースを別のリージョンに移動する方法について説明します。 

> [!NOTE]
> Azure Resource Mover は、現在プレビュー段階にあります。



## <a name="before-you-start"></a>開始する前に

- お使いの Azure サブスクリプションが Resource Mover にアクセスできる必要があります。また、そのサブスクリプションに対する[所有者](../role-based-access-control/built-in-roles.md#owner)または[ユーザー アクセス管理者](../role-based-access-control/built-in-roles.md#user-access-administrator)のアクセス許可を持っている必要があります。
- 変更やアップグレードは Resource Mover で追跡されないため、リソースの移動を始める前に、リソースに対して必要な変更を行ってください。
- 現在、PowerShell を使用してリソースを移動するときにターゲット リージョンの設定を編集することはできません。 これらの設定は、ポータルで直接変更してください。
- 別のリージョンに移動するための準備として、リソースを移動コレクションに追加すると、移動に関するメタデータが、その目的のために作成されたリソース グループ内に格納されます。 現在、このリソース グループが存在できるのは、米国東部 2 または北ヨーロッパ リージョンです。 Azure リソースは、これらのリージョンのいずれかに存在するメタデータを使用して、あらゆるパブリック リージョン間で移動できます。

## <a name="sample-values"></a>サンプルの値

スクリプトの例では、以下の値を使用します。

**設定** | **Value** 
--- | ---
サブスクリプション ID | subscription-id
Resource Mover のサービスの場所 | 米国東部 2
メタデータ リソース グループ | RegionMoveRG-centralus-westcentralus
メタデータ リソース グループの場所 | 米国東部 2
移動コレクション名 | MoveCollection-centralus-westcentralus
ソース リージョン |  centralus
ソース リソース グループ | PSDemoRM
ターゲット リージョン | westcentralus
ターゲット リソース グループ | PSDemoRMtgt
移動する VM | PSDemoVM

## <a name="sign-in-to-azure"></a>Azure へのサインイン

Azure サブスクリプションにサインインします。

```azurepowershell-interactive
# Sign in to an Azure subscription
Connect-AzAccount – Subscription "<subscription-id>"
```

## <a name="create-the-resource-group-for-metadata"></a>メタデータのリソース グループを作成する

移動コレクションのメタデータと構成情報を保持するリソース グループを作成します。

```azurepowershell-interactive
# Sign in to an Azure subscription
New-AzResourceGroup -Name MoveCollection-centralus-westcentralus -Location "East US 2"
```

**想定される出力**:

![リソース グループの作成後の出力テキスト](./media/move-region-powershell/output-create-resource-group.png)

## <a name="register-the-resource-provider"></a>リソース プロバイダーの登録

MoveCollection リソースを作成できるよう、リソース プロバイダーの Microsoft.Migrate を登録します。

```azurepowershell-interactive
# Register the provider
Register-AzResourceProvider -ProviderNamespace Microsoft.Migrate 

# Wait for registration
While(((Get-AzResourceProvider -ProviderNamespace Microsoft.Migrate)| where {$_.RegistrationState -eq "Registered" -and $_.ResourceTypes.ResourceTypeName -eq "moveCollections"}|measure).Count -eq 0)
  {
      Start-Sleep -Seconds 5
      Write-Output "Waiting for registration to complete."
  }
```

## <a name="create-the-move-collection"></a>移動コレクションを作成する

MoveCollection オブジェクトには、移動するリソースに関するメタデータと構成情報が格納されます。

- Azure Resource Mover サービスが配置されているサブスクリプションに MoveCollection オブジェクトがアクセスするには、サブスクリプションによって信頼されている[システム割り当てマネージド ID](../active-directory/managed-identities-azure-resources/overview.md#managed-identity-types) (旧称: マネージド サービス ID (MSI)) が必要です。
- この ID には、ソース サブスクリプションの共同作成者またはユーザー アクセス管理者ロールが割り当てられます。
- ID にロールを割り当てるには、次のアクセス許可を持つサブスクリプションのロール (所有者やユーザー アクセス管理者など) が必要です。
    -  Microsoft.Authorization/roleAssignments/write
    - Microsoft.Authorization/roleAssignments/delete


```azurepowershell-interactive
# Create a MoveCollection object
New-AzResourceMoverMoveCollection -Name "MoveCollection-centralus-westcentralus " -ResourceGroupName "RegionMoveRG-centralus-westcentralus " -SubscriptionId "<subscription-id>" -SourceRegion "centralus" -TargetRegion "westcentralus" -Location  "East US 2" -IdentityType SystemAssigned
```
**想定される出力**:

![移動コレクションの作成後の出力テキスト](./media/move-region-powershell/output-move-collection.png)


## <a name="assign-a-managed-identity"></a>マネージド ID を割り当てる 

サブスクリプション ID を設定し、MoveCollection オブジェクトの ID プリンシパルを取得して、Azure ロールをそれに割り当てます。


```azurepowershell-interactive
# Set the subscriptionId
$subscriptionId = "<subscription-id>"

# Retrieve the principal managed identity of the MoveCollection
$moveCollection = Get-AzResourceMoverMoveCollection -SubscriptionId $subscriptionId -ResourceGroupName "RegionMoveRG-centralus-westcentralus " -Name "MoveCollection-centralus-westcentralus "

# Set the IdentityPrincipalID
$identityPrincipalId = $moveCollection.IdentityPrincipalId

# Assign the role to the IdentityPrincipalID
New-AzRoleAssignment -ObjectId $identityPrincipalId -RoleDefinitionName Contributor -Scope "/subscriptions/$subscriptionId"

New-AzRoleAssignment -ObjectId $identityPrincipalId -RoleDefinitionName "User Access Administrator" -Scope "/subscriptions/$subscriptionId"

```

## <a name="add-resources-to-the-move-collection"></a>移動コレクションにリソースを追加する

移動するソース リソースの ID を取得します。 次に、これを移動コレクションに追加します。

```azurepowershell-interactive
# Retrieve the resource ID
Get-AzResource -Name PSDemoVM -ResourceGroupName PSDemoRM
```

**想定される出力**

![リソース ID を取得した後の出力テキスト](./media/move-region-powershell/output-retrieve-resource.png)


```azurepowershell-interactive
# Add the resource to the move collection
New-AzResourceMoverMoveResource -SubscriptionId “<subscription-id>” -ResourceGroupName “RegionMoveRG-centralus-westcentralus ” -MoveCollectionName “MoveCollection-centralus-westcentralus ” -SourceId “/subscriptions/e80eb9fa-c996-4435-aa32-5af6f3d3077c/resourceGroups/PSDemoRM/providers/Microsoft.Compute/virtualMachines/PSDemoVM” -Name “PSDemoVM” -ResourceSettingResourceType “ Microsoft.Compute/virtualMachines” -ResourceSettingTargetResourceName “PSDemoVM”
```

**想定される出力**
![リソースを追加した後の出力テキスト](./media/move-region-powershell/output-add-resource.png)

## <a name="resolve-dependencies"></a>依存関係を解決する

追加したリソースを検証し、他のリソースの依存関係を解決します。

```azurepowershell-interactive
# Resolve dependencies
Resolve-AzResourceMoverMoveCollectionDependency -SubscriptionId “<subscription-id>” -ResourceGroupName “RegionMoveRG-centralus-westcentralus” -MoveCollectionName “MoveCollection-centralus-westcentralus”
```
**依存関係が存在する場合の出力**

移動コレクション内のリソースが他のリソースに依存している場合は、失敗メッセージが出されます。

![依存関係を確認した後の出力テキスト](./media/move-region-powershell/dependency-error.png)

### <a name="retrieve-dependencies"></a>依存関係を取得する

移動するリソースが他のリソースに依存している場合は、不足している依存関係に関する情報を取得できます。

```azurepowershell-interactive
# Identify dependencies
Get-AzResourceMoverUnresolvedDependency -MoveCollectionName “MoveCollection-centralus-westcentralus ” -ResourceGroupName “RegionMoveRG-centralus-westcentralus ” -SubscriptionId  “<subscription-id>”
```

**想定される出力**

![依存関係を取得した後の出力テキスト](./media/move-region-powershell/missing-dependencies.png)

この例では、次の 2 つの依存関係が不足していることが特定されます。

- ソース リソース グループ:PSDemoRM
- VM 上の NIC:PSDemoVM62

## <a name="add-dependencies-to-collection"></a>コレクションに依存関係を追加する


取得したリソース ID を使用して、不足しているリソースの名前を特定し、それらを移動コレクションに追加します。

### <a name="add-the-nic"></a>NIC の追加

NIC の名前と種類を取得し、コレクションに追加します。

```azurepowershell-interactive
# Get the NIC name and resource type
Get-AzResource -ResourceId “/subscriptions/ <subscription-id> /resourcegroups/psdemorm/providers/microsoft.network/networkinterfaces/psdemovm62”

```

**想定される出力**

![NIC を取得した後の出力テキスト](./media/move-region-powershell/output-retrieve-nic.png)

ここで、移動コレクションに NIC を追加します。 この例では、ターゲット NIC に同じ名前を付けます。 設定と大文字と小文字の区別を維持してください。

```azurepowershell-interactive
# Add the NIC to the collection. 
New-AzResourceMoverMoveResource -SubscriptionId “<subscription-id>” -ResourceGroupName “RegionMoveRG-centralus-westcentralus” -MoveCollectionName “MoveCollection-centralus-westcentralus ” -SourceId “/subscriptions/<subscription-id>/resourceGroups/PSDemoRM/providers/Microsoft.Network/networkInterfaces/psdemovm62” -Name “psdemovm62” -ResourceSettingResourceType “Microsoft.Network/networkInterfaces” -ResourceSettingTargetResourceName “psdemovm62”
```

**想定される出力**

![NIC をコレクションに追加した後の出力テキスト](./media/move-region-powershell/add-nic.png)

## <a name="add-the-source-resource-group"></a>ソース リソース グループを追加する

```azurepowershell-interactive
# Get the resource group name and resource type
Get-AzResource -ResourceId “/subscriptions/<subscription-id>/resourcegroups/psdemorm”

# Add the resource group to the collection. 
New-AzResourceMoverMoveResource -SubscriptionId “<subscription-id>” -ResourceGroupName “RegionMoveRG-centralus-westcentralus” -MoveCollectionName “MoveCollection-centralus-westcentralus” -SourceId “/subscriptions/<subscription-id>/resourcegroups/psdemorm” -Name “psdemorm” -ResourceSettingResourceType “resourcegroups” -ResourceSettingTargetResourceName “psdemorm”
```

**想定される出力**

![リソース グループをコレクションに追加した後の出力テキスト](./media/move-region-powershell/add-source-resource-group.png)

## <a name="recheck-dependencies"></a>依存関係を再確認する

不足している依存関係がないか、もう一度確認します。

```azurepowershell-interactive
Get-AzResourceMoverUnresolvedDependency -MoveCollectionName “MoveCollection-centralus-westcentralus ” -ResourceGroupName “RegionMoveRG-centralus-westcentralus ” -SubscriptionId  “<subscription-id>”
```

**出力**

不足している依存関係がまだあります。

![依存関係を再確認した後の出力テキスト](./media/move-region-powershell/recheck-dependencies.png)

## <a name="retrieve-additional-dependencies"></a>依存関係をさらに取得する

```azurepowershell-interactive
# Identify dependencies
Get-AzResourceMoverUnresolvedDependency -MoveCollectionName “MoveCollection-centralus-westcentralus ” -ResourceGroupName “RegionMoveRG-centralus-westcentralus” -SubscriptionId  “<subscription-id>”
```

**想定される出力**


![依存関係をさらに取得した後の出力テキスト](./media/move-region-powershell/additional-missing-dependencies.png)

この例では、さらに次の 3 つの依存関係が不足していることが特定されます。

- パブリック IP アドレス: psdemovm-ip
- Azure の仮想ネットワーク: psdemorm-vnet
- ネットワーク セキュリティ グループ (NSG): psdemovm-nsg

## <a name="add-additional-dependencies"></a>依存関係を追加する

1. [手順に従って](#add-dependencies-to-collection)、これらのリソースを移動コレクションに追加します。
2. リソースを追加した後、すべての依存関係が追加されるまで、再び検証します。


## <a name="prepare-and-move-the-source-resource-group"></a>ソース リソース グループを準備して移動する

リソースを移動する前に、ソース リージョンでリソースを準備します。 準備プロセスは、移動するリソースによって異なります。 たとえば、ステートレス リソースの場合、準備では Azure Resource Manager (ARM) テンプレートを準備してエクスポートすることがあります。 また、ステートフル リソースの場合はレプリケーションを開始する可能性があります。 

ソース リソースを準備する前に、ソース リソース グループを準備して移動する必要があります。

### <a name="prepare"></a>準備

```azurepowershell-interactive
# Prepare the source resource group
Invoke-AzResourceMoverPrepare -SubscriptionId “<subscription-id>” -ResourceGroupName “RegionMoveRG-centralus-westcentralus ” -MoveCollectionName “MoveCollection-centralus-westcentralus ” -MoveResource “PSDemoRM”
```

**想定される出力**

![ソース リソース グループを準備した後の出力テキスト](./media/move-region-powershell/prepare-source-resource-group.png)

### <a name="initiate-move"></a>移動を開始する

```azurepowershell-interactive
# Initiate move
Invoke-AzResourceMoverInitiateMove -SubscriptionId “<subscription-id>” -ResourceGroupName “RegionMoveRG-centralus-westcentralus ” -MoveCollectionName “MoveCollection-centralus-westcentralus ” -MoveResource “PSDemoRM”

**Expected output**

![Output text after initiating move of the source resource group](./media/move-region-powershell/initiate-move-resource-group.png)


```azurepowershell-interactive
# Commit move
Invoke-AzResourceMoverCommit -SubscriptionId “<subscription-id” -ResourceGroupName “RegionMoveRG-centralus-westcentralus ” -MoveCollectionName “PS-centralus-
westcentralus-demoRM” -MoveResource “PSDemoRM”
```

**想定される出力**

![ソース リソース グループをコミットした後の出力テキスト](./media/move-region-powershell/commit-move-resource-group.png)


## <a name="prepare-resources"></a>リソースを準備する

ソース リソース グループをターゲット リージョンに移動した後、移動コレクション内のリソースの一覧を取得し、移動する準備をします。

```azurepowershell-interactive
# Retrieve resources in the collection
Get-AzResourceMoverMoveResource  -SubscriptionId “ <subscription-id> “ -ResourceGroupName “RegionMoveRG-centralus-westcentralus ” -MoveCollectionName “MoveCollection-centralus-westcentralus ”   | Where-Object {  $_.MoveStatusMoveState -eq “PreparePending” } | Select Name
```
**想定される出力**

![コレクション内のリソースを取得した後の出力テキスト](./media/move-region-powershell/collection-resources.png)

次に、リソースを準備します。

```azurepowershell-interactive
# Prepare the resources, using the resource name
Invoke-AzResourceMoverPrepare -SubscriptionId  <subscription-id> -ResourceGroupName RegionMoveRG-centralus-westcentralus  -MoveCollectionName MoveCollection-centralus-westcentralus   -MoveResource $('psdemovm62', 'PSDemoVM-ip', 'PSDemoRM-vnet','PSDemoVM-nsg', ‘PSDemoVM’)
```
また、名前の代わりにリソース ID を使用して、リソースを準備して移動することもできます。

```azurepowershell-interactive
# Prepare the resources using the resource ID
Invoke-AzResourceMoverPrepare -SubscriptionId  <subscription-id> -ResourceGroupName RegionMoveRG-centralus-westcentralus  -MoveCollectionName MoveCollection-centralus-westcentralus  -MoveResourceInputType MoveResourceSourceId  -MoveResource $('/subscriptions/<subscription-id>/resourceGroups/PSDemoRM/providers/Microsoft.Network/networkSecurityGroups/PSDemoVM-nsg')
```

**想定される出力**

![コレクション内のリソースを準備した後の出力テキスト](./media/move-region-powershell/collection-prepare.png)

## <a name="initiate-resource-move"></a>リソースの移動を開始する

リソースの準備が完了したら、移動を開始します。

```azurepowershell-interactive
# Initiate the move 
Invoke-AzResourceMoverInitiateMove -SubscriptionId <subscription-id> -ResourceGroupName RegionMoveRG-centralus-westcentralus  -MoveCollectionName MoveCollection-centralus-westcentralus   -MoveResource $('psdemovm62', 'PSDemoVM-ip', 'PSDemoRM-vnet','PSDemoVM-nsg', ‘PSDemoVM’)
```
**想定される出力**
![リソース移動を開始した後の出力テキスト](./media/move-region-powershell/initiate-resource-move.png)

## <a name="discard-or-commit-the-move"></a>移動を破棄またはコミットする

最初の移動後に、移動をコミットするか、破棄するかを決定できます。 

- **破棄**: テストする場合は移動を破棄できますが、実際にはソース リソースを移動したくありません。 移動を破棄すると、リソースが "*移動の開始が保留中*" の状態に戻ります。 このとき必要に応じて、移動をもう一度開始できます。
- **コミット**:コミットすると、ターゲット リージョンへの移動が完了します。 コミット後、ソース リソースは "*ソースの削除が保留中*" の状態になり、削除するかどうかを決定できます。

### <a name="discard"></a>破棄

移動を破棄するには、以下を実行します。

```azurepowershell-interactive
# Discard the move 
Invoke-AzResourceMoverDiscard -SubscriptionId  <subscription-id> `-ResourceGroupName RegionMoveRG-centralus-westcentralus  -MoveCollectionName MoveCollection-centralus-westcentralus   -MoveResource $('psdemovm62', 'PSDemoVM-ip', 'PSDemoRM-vnet','PSDemoVM-nsg', ‘PSDemoVM’)
```
**想定される出力**
![移動を破棄した後の出力テキスト](./media/move-region-powershell/discard-move.png)


### <a name="commit"></a>Commit

移動をコミットするには、以下を実行します。

```azurepowershell-interactive
# Commit the move 
Invoke-AzResourceMoverCommit -SubscriptionId  <subscription-id> -ResourceGroupName RegionMoveRG-centralus-westcentralus  -MoveCollectionName MoveCollection-centralus-westcentralus   -MoveResource $('psdemovm62', 'PSDemoVM-ip', 'PSDemoRM-vnet','PSDemoVM-nsg', ‘PSDemoVM’)
```
**想定される出力**

![移動をコミットした後の出力テキスト](./media/move-region-powershell/commit-move.png)

## <a name="delete-source-resources"></a>ソース リソースを削除する

移動をコミットし、ターゲット リージョンでリソースが想定どおりに動作することを確認した後、[Azure portal](../azure-resource-manager/management/manage-resources-portal.md#delete-resources)、[PowerShell](../azure-resource-manager/management/manage-resources-powershell.md#delete-resources) または [Azure CLI](../azure-resource-manager/management/manage-resources-cli.md#delete-resources) を使用してソース リソースを削除できます。

## <a name="next-steps"></a>次の手順

移動コレクションからリソースを削除する[方法を学習](remove-move-resources.md)します。