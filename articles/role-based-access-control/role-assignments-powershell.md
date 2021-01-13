---
title: Azure PowerShell を使用して Azure ロールの割り当てを追加または削除する - Azure RBAC
description: Azure PowerShell と Azure のロールベースのアクセス制御 (Azure RBAC) を使用して、ユーザー、グループ、サービス プリンシパル、またはマネージド ID に対して Azure リソースへのアクセス権を付与する方法について説明します。
services: active-directory
author: rolyon
manager: mtillman
ms.service: role-based-access-control
ms.topic: how-to
ms.workload: identity
ms.date: 11/25/2020
ms.author: rolyon
ms.openlocfilehash: 3bb09133ba6991554072b4bf68b5306c78f868a7
ms.sourcegitcommit: f6f928180504444470af713c32e7df667c17ac20
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/07/2021
ms.locfileid: "97964288"
---
# <a name="add-or-remove-azure-role-assignments-using-azure-powershell"></a>Azure PowerShell を使用して Azure ロールの割り当てを追加または削除する

[!INCLUDE [Azure RBAC definition grant access](../../includes/role-based-access-control/definition-grant.md)] この記事では、Azure PowerShell を使用してロールを割り当てる方法について説明します。

[!INCLUDE [az-powershell-update](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>前提条件

ロールの割り当てを追加または削除するには、以下が必要です。

- `Microsoft.Authorization/roleAssignments/write` および `Microsoft.Authorization/roleAssignments/delete` のアクセス許可 ([ユーザー アクセス管理者](built-in-roles.md#user-access-administrator)や[所有者](built-in-roles.md#owner)など)
- [Azure Cloud Shell の PowerShell](../cloud-shell/overview.md) または [Azure PowerShell](/powershell/azure/install-az-ps)
- PowerShell コマンドの実行に使用するアカウントには、Microsoft Graph の `Directory.Read.All` のアクセス許可が必要です。

## <a name="steps-to-add-a-role-assignment"></a>ロールの割り当てを追加する手順

Azure RBAC でアクセス権を付与するには、ロールの割り当てを追加します。 ロールの割り当ては、セキュリティ プリンシパル、ロールの定義、スコープの 3 つの要素で構成されています。 ロールの割り当てを追加するには、次の手順のようにします。

### <a name="step-1-determine-who-needs-access"></a>手順 1: アクセスが必要なユーザーを決定する

ロールを、ユーザー、グループ、サービス プリンシパル、またはマネージド ID に割り当てることができます。 ロールの割り当てを追加するには、オブジェクトの一意の ID の指定が必要になることがあります。 ID の形式は `11111111-1111-1111-1111-111111111111` です。 この ID は、Azure portal または Azure PowerShell を使用して取得できます。

**User**

Azure AD ユーザーの場合は、ユーザー プリンシパル名 (*patlong\@contoso.com* など) またはユーザー オブジェクト ID を取得します。 オブジェクト ID を取得するには、[Get-AzADUser](/powershell/module/az.resources/get-azaduser) を使用できます。

```azurepowershell
Get-AzADUser -StartsWith <userName>
(Get-AzADUser -DisplayName <userName>).id
```

**グループ**

Azure AD グループの場合は、グループ オブジェクト ID が必要です。 オブジェクト ID を取得するには、[Get-AzADGroup](/powershell/module/az.resources/get-azadgroup) を使用できます。

```azurepowershell
Get-AzADGroup -SearchString <groupName>
(Get-AzADGroup -DisplayName <groupName>).id
```

**サービス プリンシパル**

Azure AD サービス プリンシパル (アプリケーションによって使用される ID) の場合は、サービス プリンシパル オブジェクト ID が必要です。 オブジェクト ID を取得するには、[Get-AzADServicePrincipal](/powershell/module/az.resources/get-azadserviceprincipal) を使用できます。 サービス プリンシパルの場合は、アプリケーション ID **ではなく**、オブジェクト ID を使用します。

```azurepowershell
Get-AzADServicePrincipal -SearchString <principalName>
(Get-AzADServicePrincipal -DisplayName <principalName>).id
```

**管理対象 ID**

システム割り当てまたはユーザー割り当てのマネージド ID の場合は、オブジェクト ID が必要です。 オブジェクト ID を取得するには、[Get-AzADServicePrincipal](/powershell/module/az.resources/get-azadserviceprincipal) を使用できます。

```azurepowershell
Get-AzADServicePrincipal -SearchString <principalName>
(Get-AzADServicePrincipal -DisplayName <principalName>).id
```
    
### <a name="step-2-find-the-appropriate-role"></a>手順 2:適切なロールを見つける

アクセス許可はロールでまとめてグループ化されます。 複数の [Azure 組み込みロール](built-in-roles.md)の一覧から選択することも、独自のカスタム ロールを使用することもできます。 必要最小限の特権を持つアクセス権を付与するのがベスト プラクティスなので、より広範なロールを割り当てることは避けてください。

ロールの一覧を表示し、一意のロール ID を取得するには、[Get-AzRoleDefinition](/powershell/module/az.resources/get-azroledefinition) を使用できます。

```azurepowershell
Get-AzRoleDefinition | FT Name, IsCustom, Id
```

特定のロールの詳細を一覧表示する方法は次のとおりです。

```azurepowershell
Get-AzRoleDefinition <roleName>
```

詳細については、「[Azure ロールの定義を一覧表示する](role-definitions-list.md#azure-powershell)」を参照してください。
 
### <a name="step-3-identify-the-needed-scope"></a>手順 3:必要なスコープを特定する

Azure には、リソース、[リソース グループ](../azure-resource-manager/management/overview.md#resource-groups)、サブスクリプション、[管理グループ](../governance/management-groups/overview.md)という 4 つのレベルのスコープが用意されています。 必要最小限の特権を持つアクセス権を付与するのがベスト プラクティスなので、より広範なスコープのロールを割り当てることは避けてください。 スコープの詳細については、[スコープについての理解](scope-overview.md)に関する記事を参照してください。

**リソースのスコープ**

リソース スコープの場合は、リソースのリソース ID が必要です。 Azure portal でリソースのプロパティを見ることで、リソース ID を検索できます。 リソース ID の形式は次のとおりです。

```
/subscriptions/<subscriptionId>/resourcegroups/<resourceGroupName>/providers/<providerName>/<resourceType>/<resourceSubType>/<resourceName>
```

**リソース グループ スコープ**

リソース グループ スコープの場合は、リソース グループの名前が必要です。 Azure portal の **[リソース グループ]** ページで、または [Get-AzResourceGroup](/powershell/module/az.resources/get-azresourcegroup) を使用して、名前を検索できます。

```azurepowershell
Get-AzResourceGroup
```

**サブスクリプション スコープ** 

サブスクリプション スコープの場合は、サブスクリプション ID が必要です。 Azure portal の **[サブスクリプション]** ページで、または [Get-AzSubscription](/powershell/module/az.accounts/get-azsubscription) を使用して、ID を検索できます。

```azurepowershell
Get-AzSubscription
```

**管理グループ スコープ** 

管理グループ スコープの場合は、管理グループ名が必要です。 Azure portal の **[管理グループ]** ページで、または [Get-AzManagementGroup](/powershell/module/az.resources/get-azmanagementgroup) を使用して、名前を検索できます。

```azurepowershell
Get-AzManagementGroup
```
    
### <a name="step-4-add-role-assignment"></a>手順 4:ロールの割り当ての追加

ロールの割り当てを追加するには、[New-AzRoleAssignment](/powershell/module/az.resources/new-azroleassignment) コマンドを使用します。 コマンドは、スコープに応じて、通常、次のいずれかの形式になります。

**リソースのスコープ**

```azurepowershell
New-AzRoleAssignment -ObjectId <objectId> `
-RoleDefinitionName <roleName> `
-Scope /subscriptions/<subscriptionId>/resourcegroups/<resourceGroupName>/providers/<providerName>/<resourceType>/<resourceSubType>/<resourceName>
```

```azurepowershell
New-AzRoleAssignment -ObjectId <objectId> `
-RoleDefinitionId <roleId> `
-ResourceName <resourceName> `
-ResourceType <resourceType> `
-ResourceGroupName <resourceGroupName>
```

**リソース グループ スコープ**

```azurepowershell
New-AzRoleAssignment -SignInName <emailOrUserprincipalname> `
-RoleDefinitionName <roleName> `
-ResourceGroupName <resourceGroupName>
```

```azurepowershell
New-AzRoleAssignment -ObjectId <objectId> `
-RoleDefinitionName <roleName> `
-ResourceGroupName <resourceGroupName>
```

**サブスクリプション スコープ** 

```azurepowershell
New-AzRoleAssignment -SignInName <emailOrUserprincipalname> `
-RoleDefinitionName <roleName> `
-Scope /subscriptions/<subscriptionId>
```

```azurepowershell
New-AzRoleAssignment -ObjectId <objectId> `
-RoleDefinitionName <roleName> `
-Scope /subscriptions/<subscriptionId>
```

**管理グループ スコープ** 

```azurepowershell
New-AzRoleAssignment -SignInName <emailOrUserprincipalname> `
-RoleDefinitionName <roleName> `
-Scope /providers/Microsoft.Management/managementGroups/<groupName>
``` 

```azurepowershell
New-AzRoleAssignment -ObjectId <objectId> `
-RoleDefinitionName <roleName> `
-Scope /providers/Microsoft.Management/managementGroups/<groupName>
``` 
    
## <a name="add-role-assignment-examples"></a>ロールの割り当ての追加の例

#### <a name="add-role-assignment-for-all-blob-containers-in-a-storage-account-resource-scope"></a>ストレージ アカウント リソース スコープ内のすべての BLOB コンテナーにロールの割り当てを追加する

[ストレージ BLOB データ共同作成者](built-in-roles.md#storage-blob-data-contributor)ロールを、*storage12345* という名前のストレージ アカウントに対するリソース スコープで、オブジェクト ID が *55555555-5555-5555-5555-555555555555* のサービス プリンシパルに割り当てます。

```azurepowershell
PS C:\> New-AzRoleAssignment -ObjectId 55555555-5555-5555-5555-555555555555 `
-RoleDefinitionName "Storage Blob Data Contributor" `
-Scope "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/Example-Storage-rg/providers/Microsoft.Storage/storageAccounts/storage12345"

RoleAssignmentId   : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/Example-Storage-rg/providers/Microsoft.Storage/storageAccounts/storage12345/providers/Microsoft.Authorization/roleAssignments/cccccccc-cccc-cccc-cccc-cccccccccccc
Scope              : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/Example-Storage-rg/providers/Microsoft.Storage/storageAccounts/storage12345
DisplayName        : example-identity
SignInName         :
RoleDefinitionName : Storage Blob Data Contributor
RoleDefinitionId   : ba92f5b4-2d11-453d-a403-e96b0029c9fe
ObjectId           : 55555555-5555-5555-5555-555555555555
ObjectType         : ServicePrincipal
CanDelegate        : False
```

#### <a name="add-role-assignment-for-a-specific-blob-container-resource-scope"></a>特定の BLOB コンテナー リソース スコープにロールの割り当てを追加する

[ストレージ BLOB データ共同作成者](built-in-roles.md#storage-blob-data-contributor)ロールを、*blob-container-01* という名前の BLOB コンテナーに対するリソース スコープで、オブジェクト ID が *55555555-5555-5555-5555-555555555555* のサービス プリンシパルに割り当てます。

```azurepowershell
PS C:\> New-AzRoleAssignment -ObjectId 55555555-5555-5555-5555-555555555555 `
-RoleDefinitionName "Storage Blob Data Contributor" `
-Scope "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/Example-Storage-rg/providers/Microsoft.Storage/storageAccounts/storage12345/blobServices/default/containers/blob-container-01"

RoleAssignmentId   : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/Example-Storage-rg/providers/Microsoft.Storage/storageAccounts/storage12345/blobServices/default/containers/blob-container-01/providers/Microsoft.Authorization/roleAssignm
                     ents/dddddddd-dddd-dddd-dddd-dddddddddddd
Scope              : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/Example-Storage-rg/providers/Microsoft.Storage/storageAccounts/storage12345/blobServices/default/containers/blob-container-01
DisplayName        : example-identity
SignInName         :
RoleDefinitionName : Storage Blob Data Contributor
RoleDefinitionId   : ba92f5b4-2d11-453d-a403-e96b0029c9fe
ObjectId           : 55555555-5555-5555-5555-555555555555
ObjectType         : ServicePrincipal
CanDelegate        : False
```

#### <a name="add-role-assignment-for-a-group-in-a-specific-virtual-network-resource-scope"></a>特定の仮想ネットワーク リソース スコープ内のグループにロールの割り当てを追加する

[仮想マシン共同作成者](built-in-roles.md#virtual-machine-contributor)ロールを、*pharma-sales-project-network* という名前の仮想ネットワークに対するリソース スコープで、ID が aaaaaaaa-aaaa-aaaa-aaaa-aaaaaaaaaaaa の *Pharma Sales Admins* グループに割り当てます。

```azurepowershell
PS C:\> New-AzRoleAssignment -ObjectId aaaaaaaa-aaaa-aaaa-aaaa-aaaaaaaaaaaa `
-RoleDefinitionName "Virtual Machine Contributor" `
-ResourceName pharma-sales-project-network `
-ResourceType Microsoft.Network/virtualNetworks `
-ResourceGroupName MyVirtualNetworkResourceGroup

RoleAssignmentId   : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/MyVirtualNetworkResourceGroup
                     /providers/Microsoft.Network/virtualNetworks/pharma-sales-project-network/providers/Microsoft.Authorizat
                     ion/roleAssignments/bbbbbbbb-bbbb-bbbb-bbbb-bbbbbbbbbbbb
Scope              : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/MyVirtualNetworkResourceGroup
                     /providers/Microsoft.Network/virtualNetworks/pharma-sales-project-network
DisplayName        : Pharma Sales Admins
SignInName         :
RoleDefinitionName : Virtual Machine Contributor
RoleDefinitionId   : 9980e02c-c2be-4d73-94e8-173b1dc7cf3c
ObjectId           : aaaaaaaa-aaaa-aaaa-aaaa-aaaaaaaaaaaa
ObjectType         : Group
CanDelegate        : False
```

#### <a name="add-a-role-assignment-for-a-user-at-a-resource-group-scope"></a>リソース グループ スコープでユーザーにロールの割り当てを追加する

[仮想マシン共同作成者](built-in-roles.md#virtual-machine-contributor)ロールを、*pharma-sales* リソース グループ スコープで、*patlong\@contoso.com* ユーザーに割り当てます。

```azurepowershell
PS C:\> New-AzRoleAssignment -SignInName patlong@contoso.com `
-RoleDefinitionName "Virtual Machine Contributor" `
-ResourceGroupName pharma-sales

RoleAssignmentId   : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales/pr
                     oviders/Microsoft.Authorization/roleAssignments/55555555-5555-5555-5555-555555555555
Scope              : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales
DisplayName        : Pat Long
SignInName         : patlong@contoso.com
RoleDefinitionName : Virtual Machine Contributor
RoleDefinitionId   : 9980e02c-c2be-4d73-94e8-173b1dc7cf3c
ObjectId           : 44444444-4444-4444-4444-444444444444
ObjectType         : User
CanDelegate        : False
```

または、`-Scope` パラメーターを使用して、完全修飾リソース グループを指定することもできます。

```azurepowershell
PS C:\> New-AzRoleAssignment -SignInName patlong@contoso.com `
-RoleDefinitionName "Virtual Machine Contributor" `
-Scope "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales"

RoleAssignmentId   : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales/providers/Microsoft.Authorization/roleAssignments/55555555-5555-5555-5555-555555555555
Scope              : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales
DisplayName        : Pat Long
SignInName         : patlong@contoso.com
RoleDefinitionName : Virtual Machine Contributor
RoleDefinitionId   : 9980e02c-c2be-4d73-94e8-173b1dc7cf3c
ObjectId           : 44444444-4444-4444-4444-444444444444
ObjectType         : User
CanDelegate        : False
```

#### <a name="add-role-assignment-for-a-user-using-the-unique-role-id-at-a-resource-group-scope"></a>リソース グループ スコープで一意のロール ID を使用してユーザーにロールの割り当てを追加する

ロール名が変更されるときがあります。たとえば次のような場合です。

- 独自のカスタム ロールを使用していて、名前を変更することにした場合。
- 名前に **(プレビュー)** が含まれるプレビュー ロールを使用している場合。 ロールがリリースされると、ロールの名前が変更されます。

ロールの名前が変更される場合でも、ロールの ID は変わりません。 スクリプトまたはオートメーションを使用してロールの割り当てを作成する場合は、ロール名ではなく一意のロール ID を使用するのがベスト プラクティスです。 そうすれば、ロールの名前が変更されても、スクリプトが動作する可能性が高くなります。

次の例では、*pharma-sales* リソース グループ スコープで、*patlong\@contoso.com* ユーザーに [仮想マシン共同作成者](built-in-roles.md#virtual-machine-contributor)ロールが付与されます。

```azurepowershell
PS C:\> New-AzRoleAssignment -ObjectId 44444444-4444-4444-4444-444444444444 `
-RoleDefinitionId 9980e02c-c2be-4d73-94e8-173b1dc7cf3c `
-Scope "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales"

RoleAssignmentId   : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales/providers/Microsoft.Authorization/roleAssignments/55555555-5555-5555-5555-555555555555
Scope              : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales
DisplayName        : Pat Long
SignInName         : patlong@contoso.com
RoleDefinitionName : Virtual Machine Contributor
RoleDefinitionId   : 9980e02c-c2be-4d73-94e8-173b1dc7cf3c
ObjectId           : 44444444-4444-4444-4444-444444444444
ObjectType         : User
CanDelegate        : False
```

#### <a name="add-role-assignment-for-an-application-at-a-resource-group-scope"></a>リソース グループをスコープとするアプリケーションのロールの割り当てを追加する

*pharma-sales* リソース グループ スコープで、サービス プリンシパル オブジェクト ID が 77777777-7777-7777-7777-777777777777 のアプリケーションに [仮想マシン共同作成者](built-in-roles.md#virtual-machine-contributor)ロールを割り当てます。

```azurepowershell
PS C:\> New-AzRoleAssignment -ObjectId 77777777-7777-7777-7777-777777777777 `
-RoleDefinitionName "Virtual Machine Contributor" `
-ResourceGroupName pharma-sales

RoleAssignmentId   : /subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.Authorization/roleAssignments/66666666-6666-6666-6666-666666666666
Scope              : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales
DisplayName        : MyApp1
SignInName         :
RoleDefinitionName : Virtual Machine Contributor
RoleDefinitionId   : 9980e02c-c2be-4d73-94e8-173b1dc7cf3c
ObjectId           : 77777777-7777-7777-7777-777777777777
ObjectType         : ServicePrincipal
CanDelegate        : False
```

#### <a name="add-role-assignment-for-a-user-at-a-subscription-scope"></a>サブスクリプション スコープでユーザーにロールの割り当てを追加する

[閲覧者](built-in-roles.md#reader)ロールを、サブスクリプション スコープで、*annm\@example.com* ユーザーに割り当てます。

```azurepowershell
PS C:\> New-AzRoleAssignment -SignInName annm@example.com `
-RoleDefinitionName "Reader" `
-Scope "/subscriptions/00000000-0000-0000-0000-000000000000"

RoleAssignmentId   : /subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.Authorization/roleAssignments/66666666-6666-6666-6666-666666666666
Scope              : /subscriptions/00000000-0000-0000-0000-000000000000
DisplayName        : Ann M
SignInName         : annm@example.com
RoleDefinitionName : Reader
RoleDefinitionId   : acdd72a7-3385-48ef-bd42-f606fba81ae7
ObjectId           : 77777777-7777-7777-7777-777777777777
ObjectType         : ServicePrincipal
CanDelegate        : False
```

#### <a name="add-role-assignment-for-a-user-at-a-management-group-scope"></a>管理グループ スコープでユーザーにロールの割り当てを追加する

[課金データ閲覧者](built-in-roles.md#billing-reader)ロールを、管理グループ スコープで、*alain\@example.com* ユーザーに割り当てます。

```azurepowershell
PS C:\> New-AzRoleAssignment -SignInName alain@example.com `
-RoleDefinitionName "Billing Reader" `
-Scope "/providers/Microsoft.Management/managementGroups/marketing-group"

RoleAssignmentId   : /providers/Microsoft.Management/managementGroups/marketing-group/providers/Microsoft.Authorization/roleAssignments/22222222-2222-2222-2222-222222222222
Scope              : /providers/Microsoft.Management/managementGroups/marketing-group
DisplayName        : Alain Charon
SignInName         : alain@example.com
RoleDefinitionName : Billing Reader
RoleDefinitionId   : fa23ad8b-c56e-40d8-ac0c-ce449e1d2c64
ObjectId           : 44444444-4444-4444-4444-444444444444
ObjectType         : User
CanDelegate        : False
```

## <a name="remove-a-role-assignment"></a>ロールの割り当てを削除する

Azure RBAC でアクセス権を削除するには、[Remove-AzRoleAssignment](/powershell/module/az.resources/remove-azroleassignment) を使用してロールの割り当てを削除します。

次の例では、*pharma-sales* リソース グループの *patlong\@contoso.com* ユーザーから、"[仮想マシンの共同作成者](built-in-roles.md#virtual-machine-contributor)" ロールの割り当てを削除します。

```azurepowershell
PS C:\> Remove-AzRoleAssignment -SignInName patlong@contoso.com `
-RoleDefinitionName "Virtual Machine Contributor" `
-ResourceGroupName pharma-sales
```

[閲覧者](built-in-roles.md#reader)ロールを、サブスクリプション スコープで、ID が 22222222-2222-2222-2222-222222222222 の *Ann Mack Team* グループから削除します。

```azurepowershell
PS C:\> Remove-AzRoleAssignment -ObjectId 22222222-2222-2222-2222-222222222222 `
-RoleDefinitionName "Reader" `
-Scope "/subscriptions/00000000-0000-0000-0000-000000000000"
```

[課金データ閲覧者](built-in-roles.md#billing-reader)ロールを、管理グループ スコープで、*alain\@example.com* ユーザーから削除します。

```azurepowershell
PS C:\> Remove-AzRoleAssignment -SignInName alain@example.com `
-RoleDefinitionName "Billing Reader" `
-Scope "/providers/Microsoft.Management/managementGroups/marketing-group"
```

"The provided information does not map to a role assignment" (指定された情報は、ロールの割り当てにマップされていません) というエラー メッセージが表示された場合、`-Scope` または `-ResourceGroupName` パラメーターも指定してください。 詳細については、「[Azure RBAC のトラブルシューティング](troubleshooting.md#role-assignments-with-identity-not-found)」を参照してください。

## <a name="next-steps"></a>次のステップ

- [Azure PowerShell を使用して Azure でのロールの割り当てを一覧表示する](role-assignments-list-powershell.md)
- [チュートリアル:Azure PowerShell を使用して Azure リソースへのアクセス権をグループに付与する](tutorial-role-assignments-group-powershell.md)
- [Azure PowerShell でリソースを管理する](../azure-resource-manager/management/manage-resources-powershell.md)
