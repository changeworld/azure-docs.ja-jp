---
title: "Azure PowerShell を使用したロールベースの Access Control (RBAC) の管理 | Microsoft Docs"
description: "Azure PowerShell を使用して RBAC を管理する方法 (ロールの表示、ロールの割り当て、ロール割り当ての削除など)。"
services: active-directory
documentationcenter: 
author: kgremban
manager: femila
editor: 
ms.assetid: 9e225dba-9044-4b13-b573-2f30d77925a9
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/22/2016
ms.author: kgremban
translationtype: Human Translation
ms.sourcegitcommit: dcda8b30adde930ab373a087d6955b900365c4cc
ms.openlocfilehash: 9129eda8e4b3c3865878b8ceafb95a155ba02885


---
# <a name="manage-role-based-access-control-with-azure-powershell"></a>Azure PowerShell を使用したロールベースのアクセス制御の管理
> [!div class="op_single_selector"]
> * [PowerShell](role-based-access-control-manage-access-powershell.md)
> * [Azure CLI](role-based-access-control-manage-access-azure-cli.md)
> * [REST API](role-based-access-control-manage-access-rest.md)
> 
> 

Azure ポータルと Azure リソース管理 API のロールベースの Access Control (RBAC) を使用すると、サブスクリプションへのアクセスを詳細に管理できます。 この機能を使用すると、Active Directory ユーザー、グループ、サービス プリンシパルに特定のスコープで役割を割り当てて、アクセス権を付与できます。

PowerShell を使って RBAC を管理するには、以下を用意しておく必要があります。

* Azure PowerShell バージョン 0.8.8 以降。 最新バージョンをインストールして、Azure サブスクリプションに関連付けるには、「 [How to install and configure Azure PowerShell (Azure PowerShell のインストールと構成の方法)](/powershell/azureps-cmdlets-docs)」をご覧ください。
* Azure Resource Manager コマンドレット。 PowerShell で [Azure Resource Manager コマンドレット](https://msdn.microsoft.com/library/mt125356.aspx) をインストールします。

## <a name="list-roles"></a>ロールの一覧表示
### <a name="list-all-available-roles"></a>使用可能なすべてのロールの表示
割り当てることができる RBAC のロールを表示したり、アクセス権が付与されている操作を調べたりするには、 `Get-AzureRmRoleDefinition`を使用します。

```
Get-AzureRmRoleDefinition | FT Name, Description
```

![RBAC PowerShell - Get-AzureRmRoleDefinition - スクリーンショット](./media/role-based-access-control-manage-access-powershell/1-get-azure-rm-role-definition1.png)

### <a name="list-actions-of-a-role"></a>ロールのアクションの表示
特定のロールのアクションを表示するには、 `Get-AzureRmRoleDefinition <role name>`を使用します。

```
Get-AzureRmRoleDefinition Contributor | FL Actions, NotActions

(Get-AzureRmRoleDefinition "Virtual Machine Contributor").Actions
```

![RBAC PowerShell - 特定のロールの Get-AzureRmRoleDefinition - スクリーンショット](./media/role-based-access-control-manage-access-powershell/1-get-azure-rm-role-definition2.png)

## <a name="see-who-has-access"></a>アクセスできるユーザーの確認
RBAC のアクセス権の割り当てを表示するには、 `Get-AzureRmRoleAssignment`を使用します。

### <a name="list-role-assignments-at-a-specific-scope"></a>特定のスコープでのロールの割り当ての表示
指定したサブスクリプション、リソース グループ、またはリソースに対するすべてのアクセス権の割り当てを表示できます。 たとえば、リソース グループのすべてのアクティブな割り当てを表示するには、 `Get-AzureRmRoleAssignment -ResourceGroupName <resource group name>`を使用します。

```
Get-AzureRmRoleAssignment -ResourceGroupName Pharma-Sales-ProjectForcast | FL DisplayName, RoleDefinitionName, Scope
```

![RBAC PowerShell - リソース グループの Get-AzureRmRoleAssignment - スクリーンショット](./media/role-based-access-control-manage-access-powershell/4-get-azure-rm-role-assignment1.png)

### <a name="list-roles-assigned-to-a-user"></a>ユーザーに割り当てられたロールの表示
特定のユーザーに割り当てられたすべてのロールと、そのユーザーが所属するグループに割り当てられたロールを表示するには、 `Get-AzureRmRoleAssignment -SignInName <User email> -ExpandPrincipalGroups`を使用します。

```
Get-AzureRmRoleAssignment -SignInName sameert@aaddemo.com | FL DisplayName, RoleDefinitionName, Scope

Get-AzureRmRoleAssignment -SignInName sameert@aaddemo.com -ExpandPrincipalGroups | FL DisplayName, RoleDefinitionName, Scope
```

![RBAC PowerShell - ユーザーの Get-AzureRmRoleAssignment - スクリーンショット](./media/role-based-access-control-manage-access-powershell/4-get-azure-rm-role-assignment2.png)

### <a name="list-classic-service-administrator-and-coadmin-role-assignments"></a>従来のサービス管理者ロールと共同管理者ロールの割り当ての表示
従来のサブスクリプション管理者と共同管理者のアクセス権の割り当てを表示するには、次のコマンドを使用します。

    Get-AzureRmRoleAssignment -IncludeClassicAdministrators

## <a name="grant-access"></a>アクセス権の付与
### <a name="search-for-object-ids"></a>オブジェクト ID の検索
ロールを割り当てるには、オブジェクト (ユーザー、グループ、またはアプリケーション) とスコープの両方を特定する必要があります。

サブスクリプション ID がわからない場合は、Azure ポータルの **[サブスクリプション]** ブレードで、その ID を見つけることができます。 サブスクリプション ID のクエリを実行する方法については、MSDN で [Get-AzureSubscription](https://msdn.microsoft.com/library/dn495302.aspx) を参照してください。

Azure AD グループのオブジェクト ID を取得するには、次のコマンドを使用します。

    Get-AzureRmADGroup -SearchString <group name in quotes>

Azure AD サービス プリンシパル、つまりアプリケーションのオブジェクト ID を取得するには、次のコマンドを使用します。

    Get-AzureRmADServicePrincipal -SearchString <service name in quotes>

### <a name="assign-a-role-to-an-application-at-the-subscription-scope"></a>サブスクリプションのスコープでのアプリケーションへのロールの割り当て
サブスクリプションのスコープでアプリケーションにアクセス権を付与するには、次のコマンドを使用します。

    New-AzureRmRoleAssignment -ObjectId <application id> -RoleDefinitionName <role name> -Scope <subscription id>

![RBAC PowerShell - New-AzureRmRoleAssignment - スクリーンショット](./media/role-based-access-control-manage-access-powershell/2-new-azure-rm-role-assignment2.png)

### <a name="assign-a-role-to-a-user-at-the-resource-group-scope"></a>リソース グループのスコープでのユーザーへのロールの割り当て
リソース グループのスコープでユーザーにアクセス権を付与するには、次のコマンドを使用します。

    New-AzureRmRoleAssignment -SignInName <email of user> -RoleDefinitionName <role name in quotes> -ResourceGroupName <resource group name>

![RBAC PowerShell - New-AzureRmRoleAssignment - スクリーンショット](./media/role-based-access-control-manage-access-powershell/2-new-azure-rm-role-assignment3.png)

### <a name="assign-a-role-to-a-group-at-the-resource-scope"></a>リソースのスコープでのグループへのロールの割り当て
リソースのスコープでグループにアクセス権を付与するには、次のコマンドを使用します。

    New-AzureRmRoleAssignment -ObjectId <object id> -RoleDefinitionName <role name in quotes> -ResourceName <resource name> -ResourceType <resource type> -ParentResource <parent resource> -ResourceGroupName <resource group name>

![RBAC PowerShell - New-AzureRmRoleAssignment - スクリーンショット](./media/role-based-access-control-manage-access-powershell/2-new-azure-rm-role-assignment4.png)

## <a name="remove-access"></a>アクセス権の削除
ユーザー、グループ、アプリケーションのアクセス権を削除するには、次のコマンドを使用します。

    Remove-AzureRmRoleAssignment -ObjectId <object id> -RoleDefinitionName <role name> -Scope <scope such as subscription id>

![RBAC PowerShell - Remove-AzureRmRoleAssignment - スクリーンショット](./media/role-based-access-control-manage-access-powershell/3-remove-azure-rm-role-assignment.png)

## <a name="create-a-custom-role"></a>カスタム ロールの作成
カスタム ロールを作成するには、 `New-AzureRmRoleDefinition` コマンドを使用します。

PowerShell を使用してカスタム ロールを作成するときは、いずれかの [組み込みロール](role-based-access-built-in-roles.md)を土台とする必要があります。 その属性を編集し、必要に応じて *Actions*、*notActions*、*スコープ*を追加して、変更内容を新しいロールとして保存します。

以下の例では、"*仮想マシンの共同作業者*" ロールを土台として、"*仮想マシン オペレーター*" というカスタム ロールを作成しています。 この新しいロールは、*Microsoft.Compute*、*Microsoft.Storage*、*Microsoft.Network* リソース プロバイダーのすべての読み取り操作を許可し、仮想マシンの起動、再起動、監視を許可します。 カスタム ロールは 2 つのサブスクリプションで使用できます。

```
$role = Get-AzureRmRoleDefinition "Virtual Machine Contributor"
$role.Id = $null
$role.Name = "Virtual Machine Operator"
$role.Description = "Can monitor and restart virtual machines."
$role.Actions.Clear()
$role.Actions.Add("Microsoft.Storage/*/read")
$role.Actions.Add("Microsoft.Network/*/read")
$role.Actions.Add("Microsoft.Compute/*/read")
$role.Actions.Add("Microsoft.Compute/virtualMachines/start/action")
$role.Actions.Add("Microsoft.Compute/virtualMachines/restart/action")
$role.Actions.Add("Microsoft.Authorization/*/read")
$role.Actions.Add("Microsoft.Resources/subscriptions/resourceGroups/read")
$role.Actions.Add("Microsoft.Insights/alertRules/*")
$role.Actions.Add("Microsoft.Support/*")
$role.AssignableScopes.Clear()
$role.AssignableScopes.Add("/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e")
$role.AssignableScopes.Add("/subscriptions/e91d47c4-76f3-4271-a796-21b4ecfe3624")
New-AzureRmRoleDefinition -Role $role
```

![RBAC PowerShell - Get-AzureRmRoleDefinition - スクリーンショット](./media/role-based-access-control-manage-access-powershell/2-new-azurermroledefinition.png)

## <a name="modify-a-custom-role"></a>カスタム ロールの修正
カスタム ロールを修正するには、まず `Get-AzureRmRoleDefinition` コマンドを使用してロール定義を取得します。 次に、必要に応じてロール定義を変更します。 最後に、 `Set-AzureRmRoleDefinition` コマンドを使用して変更したロール定義を保存します。

次の例では、 `Microsoft.Insights/diagnosticSettings/*` 操作が *仮想マシン オペレーター* カスタム ロールに追加されます。

```
$role = Get-AzureRmRoleDefinition "Virtual Machine Operator"
$role.Actions.Add("Microsoft.Insights/diagnosticSettings/*")
Set-AzureRmRoleDefinition -Role $role
```

![RBAC PowerShell - Set-AzureRmRoleDefinition - スクリーンショット](./media/role-based-access-control-manage-access-powershell/3-set-azurermroledefinition-1.png)

次の例では、Azure サブスクリプションが *仮想マシン オペレーター* カスタム ロールの割り当て可能なスコープに追加されます。

```
Get-AzureRmSubscription - SubscriptionName Production3

$role = Get-AzureRmRoleDefinition "Virtual Machine Operator"
$role.AssignableScopes.Add("/subscriptions/34370e90-ac4a-4bf9-821f-85eeedead1a2"
Set-AzureRmRoleDefinition -Role $role)
```

![RBAC PowerShell - Set-AzureRmRoleDefinition - スクリーンショット](./media/role-based-access-control-manage-access-powershell/3-set-azurermroledefinition-2.png)

## <a name="delete-a-custom-role"></a>カスタム ロールの削除
カスタム ロールを削除するには、 `Remove-AzureRmRoleDefinition` コマンドを使用します。

次の例では、 *仮想マシン オペレーター* カスタム ロールが削除されます。

```
Get-AzureRmRoleDefinition "Virtual Machine Operator"

Get-AzureRmRoleDefinition "Virtual Machine Operator" | Remove-AzureRmRoleDefinition
```

![RBAC PowerShell - Remove-AzureRmRoleDefinition - スクリーンショット](./media/role-based-access-control-manage-access-powershell/4-remove-azurermroledefinition.png)

## <a name="list-custom-roles"></a>カスタム ロールの一覧表示
特定のスコープで割り当て可能なロールを一覧表示するには、 `Get-AzureRmRoleDefinition` コマンドを使用します。

次の例では、選択したサブスクリプションで割り当て可能なすべてのロールが一覧表示されます。

```
Get-AzureRmRoleDefinition | FT Name, IsCustom
```

![RBAC PowerShell - Get-AzureRmRoleDefinition - スクリーンショット](./media/role-based-access-control-manage-access-powershell/5-get-azurermroledefinition-1.png)

次の例では、"*仮想マシン オペレーター*" カスタム ロールは *Production4* サブスクリプションでは利用できません。そのサブスクリプションはロールの **AssignableScopes** にないためです。

![RBAC PowerShell - Get-AzureRmRoleDefinition - スクリーンショット](./media/role-based-access-control-manage-access-powershell/5-get-azurermroledefinition2.png)

## <a name="see-also"></a>関連項目
* [Azure リソース マネージャーでの Azure PowerShell の使用](../powershell-azure-resource-manager.md)
  [!INCLUDE [role-based-access-control-toc.md](../../includes/role-based-access-control-toc.md)]




<!--HONumber=Dec16_HO2-->


