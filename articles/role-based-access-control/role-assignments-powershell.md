---
title: Azure PowerShell を使用したロールベースのアクセス制御 (RBAC) の管理 | Microsoft Docs
description: Azure PowerShell を使用して RBAC を管理する方法 (ロールの表示、ロールの割り当て、ロール割り当ての削除など)。
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.assetid: 9e225dba-9044-4b13-b573-2f30d77925a9
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/17/2018
ms.author: rolyon
ms.reviewer: rqureshi
ms.openlocfilehash: 9fe7e6d12c3a831b006107ae53ca37cd22e34d4c
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/19/2018
---
# <a name="manage-role-based-access-control-with-azure-powershell"></a>Azure PowerShell を使用したロールベースのアクセス制御の管理
> [!div class="op_single_selector"]
> * [PowerShell](role-assignments-powershell.md)
> * [Azure CLI](role-assignments-cli.md)
> * [REST API](role-assignments-rest.md)

ロールベースのアクセス制御 (RBAC) を使用して特定の範囲にロールを割り当てることにより、ユーザー、グループ、およびサービス プリンシパルのアクセスを定義します。 この記事では、Azure PowerShell を使用してアクセスを管理する方法について説明します。

## <a name="prerequisites"></a>前提条件

PowerShell を使って RBAC を管理するには、次のいずれかが必要です。

* [Azure Cloud Shell の PowerShell](/azure/cloud-shell/overview)
* [Azure PowerShell 5.1.0 以降](/powershell/azure/install-azurerm-ps)

## <a name="list-roles"></a>ロールの一覧表示

### <a name="list-all-available-roles"></a>使用可能なすべてのロールの表示

割り当てることができる RBAC のロールを表示したり、アクセス権が付与されている操作を調べたりするには、[Get-AzureRmRoleDefinition](/powershell/module/azurerm.resources/get-azurermroledefinition) を使用します。

```azurepowershell
Get-AzureRmRoleDefinition | FT Name, Description
```

```Example
AcrImageSigner                                    acr image signer
AcrQuarantineReader                               acr quarantine data reader
AcrQuarantineWriter                               acr quarantine data writer
API Management Service Contributor                Can manage service and the APIs
API Management Service Operator Role              Can manage service but not the APIs
API Management Service Reader Role                Read-only access to service and APIs
Application Insights Component Contributor        Can manage Application Insights components
Application Insights Snapshot Debugger            Gives user permission to use Application Insights Snapshot Debugge...
Automation Job Operator                           Create and Manage Jobs using Automation Runbooks.
Automation Operator                               Automation Operators are able to start, stop, suspend, and resume ...
...
```

### <a name="list-a-specific-role"></a>特定のロールを一覧表示する

特定のロールを一覧表示するには、[Get-AzureRmRoleDefinition](/powershell/module/azurerm.resources/get-azurermroledefinition) を使用します。

```azurepowershell
Get-AzureRmRoleDefinition <role name>
```

```Example
PS C:\> Get-AzureRmRoleDefinition "Contributor"

Name             : Contributor
Id               : b24988ac-6180-42a0-ab88-20f7382dd24c
IsCustom         : False
Description      : Lets you manage everything except access to resources.
Actions          : {*}
NotActions       : {Microsoft.Authorization/*/Delete, Microsoft.Authorization/*/Write,
                   Microsoft.Authorization/elevateAccess/Action}
AssignableScopes : {/}
```

### <a name="list-a-specific-role-in-json-format"></a>特定のロールを JSON 形式で一覧表示する

特定のロールを JSON 形式で一覧表示するには、[Get-AzureRmRoleDefinition](/powershell/module/azurerm.resources/get-azurermroledefinition) を使用します。

```azurepowershell
Get-AzureRmRoleDefinition <role name> | ConvertTo-Json
```

```Example
PS C:\> Get-AzureRmRoleDefinition "Contributor" | ConvertTo-Json

{
    "Name":  "Contributor",
    "Id":  "b24988ac-6180-42a0-ab88-20f7382dd24c",
    "IsCustom":  false,
    "Description":  "Lets you manage everything except access to resources.",
    "Actions":  [
                    "*"
                ],
    "NotActions":  [
                       "Microsoft.Authorization/*/Delete",
                       "Microsoft.Authorization/*/Write",
                       "Microsoft.Authorization/elevateAccess/Action"
                   ],
    "AssignableScopes":  [
                             "/"
                         ]
}
```

### <a name="list-actions-of-a-role"></a>ロールのアクションの表示

特定のロールのアクションを一覧表示するには、[Get-AzureRmRoleDefinition](/powershell/module/azurerm.resources/get-azurermroledefinition) を使用します。

```azurepowershell
Get-AzureRmRoleDefinition <role name> | FL Actions, NotActions
```

```Example
PS C:\> Get-AzureRmRoleDefinition "Contributor" | FL Actions, NotActions

Actions    : {*}
NotActions : {Microsoft.Authorization/*/Delete, Microsoft.Authorization/*/Write,
             Microsoft.Authorization/elevateAccess/Action}
```

```azurepowershell
(Get-AzureRmRoleDefinition <role name>).Actions
```

```Example
PS C:\> (Get-AzureRmRoleDefinition "Virtual Machine Contributor").Actions

Microsoft.Authorization/*/read
Microsoft.Compute/availabilitySets/*
Microsoft.Compute/locations/*
Microsoft.Compute/virtualMachines/*
Microsoft.Compute/virtualMachineScaleSets/*
Microsoft.DevTestLab/schedules/*
Microsoft.Insights/alertRules/*
Microsoft.Network/applicationGateways/backendAddressPools/join/action
Microsoft.Network/loadBalancers/backendAddressPools/join/action
...
```

## <a name="see-who-has-access"></a>アクセスできるユーザーの確認

RBAC アクセスの割り当てを一覧表示するには、[Get-AzureRmRoleAssignment](/powershell/module/azurerm.resources/get-azurermroleassignment) を使用します。

### <a name="list-role-assignments-at-a-specific-scope"></a>特定のスコープでのロールの割り当ての表示

指定したサブスクリプション、リソース グループ、またはリソースに対するすべてのロールの割り当てを表示できます。 たとえば、リソース グループのすべてのアクティブな割り当てを表示するには、[Get-AzureRmRoleAssignment](/powershell/module/azurerm.resources/get-azurermroleassignment) を使用します。

```azurepowershell
Get-AzureRmRoleAssignment -ResourceGroupName <resource group name>
```

```Example
PS C:\> Get-AzureRmRoleAssignment -ResourceGroupName pharma-sales-projectforecast | FL DisplayName, RoleDefinitionName, Scope

DisplayName        : Alain Charon
RoleDefinitionName : Backup Operator
Scope              : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales-projectforecast

DisplayName        : Isabella Simonsen
RoleDefinitionName : BizTalk Contributor
Scope              : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales-projectforecast

DisplayName        : Alain Charon
RoleDefinitionName : Virtual Machine Contributor
Scope              : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales-projectforecast
```

### <a name="list-roles-assigned-to-a-user"></a>ユーザーに割り当てられたロールの表示

指定したユーザーに割り当てられているすべてのロールを一覧表示するには、[Get-AzureRmRoleAssignment](/powershell/module/azurerm.resources/get-azurermroleassignment) を使用します。

```azurepowershell
Get-AzureRmRoleAssignment -SignInName <user email>
```

```Example
PS C:\> Get-AzureRmRoleAssignment -SignInName isabella@example.com | FL DisplayName, RoleDefinitionName, Scope

DisplayName        : Isabella Simonsen
RoleDefinitionName : BizTalk Contributor
Scope              : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales-projectforecast
```

特定のユーザーに割り当てられたすべてのロールと、そのユーザーが所属するグループに割り当てられたロールを一覧表示するには、[Get-AzureRmRoleAssignment](/powershell/module/azurerm.resources/get-azurermroleassignment) を使用します。

```azurepowershell
Get-AzureRmRoleAssignment -SignInName <user email> -ExpandPrincipalGroups
```

```Example
Get-AzureRmRoleAssignment -SignInName isabella@example.com -ExpandPrincipalGroups | FL DisplayName, RoleDefinitionName, Scope
```

### <a name="list-classic-service-administrator-and-coadmin-role-assignments"></a>従来のサービス管理者ロールと共同管理者ロールの割り当ての表示

従来のサブスクリプション管理者と共同管理者のアクセス権の割り当てを表示するには、[Get-AzureRmRoleAssignment](/powershell/module/azurerm.resources/get-azurermroleassignment) を使用します。

```azurepowershell
Get-AzureRmRoleAssignment -IncludeClassicAdministrators
```

## <a name="grant-access"></a>アクセス権の付与

### <a name="search-for-object-ids"></a>オブジェクト ID の検索

ロールを割り当てるには、オブジェクト (ユーザー、グループ、またはアプリケーション) とスコープの両方を特定する必要があります。

サブスクリプション ID がわからない場合は、Azure Portal の **[サブスクリプション]** ブレードで、または [Get-AzureRmSubscription](/powershell/module/azurerm.profile/get-azurermsubscription) を使用して、その ID を見つけることができます。

Azure AD グループのオブジェクト ID を取得するには、[Get-AzureRmADGroup](/powershell/module/azurerm.resources/get-azurermadgroup) を使用します。

```azurepowershell
Get-AzureRmADGroup -SearchString <group name in quotes>
```

Azure AD サービス プリンシパル、つまりアプリケーションのオブジェクト ID を取得するには、[Get-AzureRmADServicePrincipal](/powershell/module/azurerm.resources/get-azurermadserviceprincipal) を使用します。

```azurepowershell
Get-AzureRmADServicePrincipal -SearchString <service name in quotes>
```

### <a name="assign-a-role-to-an-application-at-the-subscription-scope"></a>サブスクリプションのスコープでのアプリケーションへのロールの割り当て

サブスクリプションのスコープでアプリケーションにアクセス権を付与するには、[New-AzureRmRoleAssignment](/powershell/module/azurerm.resources/new-azurermroleassignment) を使用します。

```azurepowershell
New-AzureRmRoleAssignment -ObjectId <application id> -RoleDefinitionName <role name> -Scope <subscription id>
```

```Example
PS C:\> New-AzureRmRoleAssignment -ObjectId 77777777-7777-7777-7777-777777777777 -RoleDefinitionName "Reader" -Scope /subscriptions/00000000-0000-0000-0000-000000000000

RoleAssignmentId   : /subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.Authorization/roleAssignments/66666666-6666-6666-6666-666666666666
Scope              : /subscriptions/00000000-0000-0000-0000-000000000000
DisplayName        : MyApp1
SignInName         :
RoleDefinitionName : Reader
RoleDefinitionId   : acdd72a7-3385-48ef-bd42-f606fba81ae7
ObjectId           : 77777777-7777-7777-7777-777777777777
ObjectType         : ServicePrincipal
CanDelegate        : False
```

### <a name="assign-a-role-to-a-user-at-the-resource-group-scope"></a>リソース グループのスコープでのユーザーへのロールの割り当て

リソース グループのスコープでユーザーにアクセス権を付与するには、[New-AzureRmRoleAssignment](/powershell/module/azurerm.resources/new-azurermroleassignment) を使用します。

```azurepowershell
New-AzureRmRoleAssignment -SignInName <email of user> -RoleDefinitionName <role name in quotes> -ResourceGroupName <resource group name>
```

```Example
PS C:\> New-AzureRmRoleAssignment -SignInName alain@example.com -RoleDefinitionName "Virtual Machine Contributor" -ResourceGroupName pharma-sales-projectforecast


RoleAssignmentId   : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales-projectforecast/pr
                     oviders/Microsoft.Authorization/roleAssignments/55555555-5555-5555-5555-555555555555
Scope              : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales-projectforecast
DisplayName        : Alain Charon
SignInName         : alain@example.com
RoleDefinitionName : Virtual Machine Contributor
RoleDefinitionId   : 9980e02c-c2be-4d73-94e8-173b1dc7cf3c
ObjectId           : 44444444-4444-4444-4444-444444444444
ObjectType         : User
CanDelegate        : False
```

### <a name="assign-a-role-to-a-group-at-the-resource-scope"></a>リソースのスコープでのグループへのロールの割り当て

リソースのスコープでグループにアクセス権を付与するには、[New-AzureRmRoleAssignment](/powershell/module/azurerm.resources/new-azurermroleassignment) を使用します。

```azurepowershell
New-AzureRmRoleAssignment -ObjectId <object id> -RoleDefinitionName <role name in quotes> -ResourceName <resource name> -ResourceType <resource type> -ParentResource <parent resource> -ResourceGroupName <resource group name>
```

```Example
PS C:\> Get-AzureRmADGroup -SearchString "Pharma"

SecurityEnabled DisplayName         Id                                   Type
--------------- -----------         --                                   ----
           True Pharma Sales Admins aaaaaaaa-aaaa-aaaa-aaaa-aaaaaaaaaaaa Group

PS C:\> New-AzureRmRoleAssignment -ObjectId aaaaaaaa-aaaa-aaaa-aaaa-aaaaaaaaaaaa -RoleDefinitionName "Virtual Machine Contributor" -ResourceName RobertVirtualNetwork -ResourceType Microsoft.Network/virtualNetworks -ResourceGroupName RobertVirtualNetworkResourceGroup

RoleAssignmentId   : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/MyVirtualNetworkResourceGroup
                     /providers/Microsoft.Network/virtualNetworks/RobertVirtualNetwork/providers/Microsoft.Authorizat
                     ion/roleAssignments/bbbbbbbb-bbbb-bbbb-bbbb-bbbbbbbbbbbb
Scope              : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/MyVirtualNetworkResourceGroup
                     /providers/Microsoft.Network/virtualNetworks/RobertVirtualNetwork
DisplayName        : Pharma Sales Admins
SignInName         :
RoleDefinitionName : Virtual Machine Contributor
RoleDefinitionId   : 9980e02c-c2be-4d73-94e8-173b1dc7cf3c
ObjectId           : aaaaaaaa-aaaa-aaaa-aaaa-aaaaaaaaaaaa
ObjectType         : Group
CanDelegate        : False
```

## <a name="remove-access"></a>アクセス権の削除

ユーザー、グループ、アプリケーションのアクセス権を削除するには、[Remove-AzureRmRoleAssignment](/powershell/module/azurerm.resources/remove-azurermroleassignment) を使用します。

```azurepowershell
Remove-AzureRmRoleAssignment -ObjectId <object id> -RoleDefinitionName <role name> -Scope <scope such as subscription id>
```

```Example
PS C:\> Remove-AzureRmRoleAssignment -SignInName alain@example.com -RoleDefinitionName "Virtual Machine Contributor" -ResourceGroupName pharma-sales-projectforecast
```

## <a name="list-custom-roles"></a>カスタム ロールの一覧表示

特定のスコープで割り当て可能なロールを一覧表示するには、[Get-AzureRmRoleDefinition](/powershell/module/azurerm.resources/get-azurermroledefinition) コマンドを使用します。

次の例では、選択したサブスクリプションで割り当て可能なすべてのロールが一覧表示されます。

```azurepowershell
Get-AzureRmRoleDefinition | FT Name, IsCustom
```

```Example
Name                                              IsCustom
----                                              --------
Virtual Machine Operator                              True
AcrImageSigner                                       False
AcrQuarantineReader                                  False
AcrQuarantineWriter                                  False
API Management Service Contributor                   False
...
```

次の例では、選択したサブスクリプションで割り当て可能なカスタム ロールだけが一覧表示されます。

```azurepowershell
Get-AzureRmRoleDefinition | ? {$_.IsCustom -eq $true} | FT Name, IsCustom
```

```Example
Name                     IsCustom
----                     --------
Virtual Machine Operator     True
```

選択したサブスクリプションがロールの `AssignableScopes` にない場合、カスタム ロールは一覧表示されません。

## <a name="create-a-custom-role"></a>カスタム ロールの作成

カスタム ロールを作成するには、[New-AzureRmRoleDefinition](/powershell/module/azurerm.resources/new-azurermroledefinition) コマンドを使用します。 ロールを構成する方法は 2 つあります。`PSRoleDefinition` オブジェクトを使用するか JSON テンプレートを使用します。 

### <a name="get-operations-for-a-resource-provider"></a>リソース プロバイダー操作を取得する

カスタム ロールを作成するときは、リソース プロバイダーから可能なすべての操作を理解しておくことが重要です。
この情報を取得するには、[リソース プロバイダー操作](resource-provider-operations.md)のリストを表示するか、[Get-AzureRMProviderOperation](/powershell/module/azurerm.resources/get-azurermprovideroperation) コマンドを使用します。
たとえば、仮想マシンで使用可能なすべての操作を確認する場合は、次のコマンドを使います。

```azurepowershell
Get-AzureRMProviderOperation <operation> | FT OperationName, Operation, Description -AutoSize
```

```Example
PS C:\> Get-AzureRMProviderOperation "Microsoft.Compute/virtualMachines/*" | FT OperationName, Operation, Description -AutoSize

OperationName                                  Operation                                                      Description
-------------                                  ---------                                                      -----------
Get Virtual Machine                            Microsoft.Compute/virtualMachines/read                         Get the propertie...
Create or Update Virtual Machine               Microsoft.Compute/virtualMachines/write                        Creates a new vir...
Delete Virtual Machine                         Microsoft.Compute/virtualMachines/delete                       Deletes the virtu...
Start Virtual Machine                          Microsoft.Compute/virtualMachines/start/action                 Starts the virtua...
...
```

### <a name="create-a-role-with-psroledefinition-object"></a>PSRoleDefinition オブジェクトを使用したロールの作成

PowerShell を使ってカスタム ロールを作成する場合は、[組み込みのロール](built-in-roles.md)を出発点として使うことも、ゼロから始めることもできます。 このセクションの最初の例では、組み込みのロールから始めて、さらに多くのアクセス許可を持つようにカスタマイズします。 その属性を編集し、必要に応じて `Actions`、`NotActions`、または `AssignableScopes`を追加して、変更内容を新しいロールとして保存します。

以下の例では、[仮想マシンの共同作業者](built-in-roles.md#virtual-machine-contributor)組み込みロールを土台として、*仮想マシン オペレーター*というカスタム ロールを作成しています。 この新しいロールは、*Microsoft.Compute*、*Microsoft.Storage*、*Microsoft.Network* リソース プロバイダーのすべての読み取り操作を許可し、仮想マシンの起動、再起動、監視を許可します。 カスタム ロールは 2 つのサブスクリプションで使用できます。

```azurepowershell
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
$role.AssignableScopes.Add("/subscriptions/00000000-0000-0000-0000-000000000000")
$role.AssignableScopes.Add("/subscriptions/11111111-1111-1111-1111-111111111111")
New-AzureRmRoleDefinition -Role $role
```

次の例では、*仮想マシン オペレーター* カスタム ロールを作成する別の方法を示しています。 最初に新しい PSRoleDefinition オブジェクトを作成します。 このアクション操作は、`perms` 変数で指定され、`Actions` プロパティに設定されます。 `NotActions` プロパティは、[仮想マシンの共同作成者](built-in-roles.md#virtual-machine-contributor)組み込みロールから `NotActions` を読み取ることで設定されます。 [仮想マシンの共同作成者](built-in-roles.md#virtual-machine-contributor)には `NotActions` がないため、この行は必要ありませんが、別のロールから情報を取得する方法を示しています。

```azurepowershell
$role = [Microsoft.Azure.Commands.Resources.Models.Authorization.PSRoleDefinition]::new()
$role.Name = 'Virtual Machine Operator 2'
$role.Description = 'Can monitor and restart virtual machines.'
$role.IsCustom = $true
$perms = 'Microsoft.Storage/*/read','Microsoft.Network/*/read','Microsoft.Compute/*/read'
$perms += 'Microsoft.Compute/virtualMachines/start/action','Microsoft.Compute/virtualMachines/restart/action'
$perms += 'Microsoft.Authorization/*/read','Microsoft.Resources/subscriptions/resourceGroups/read'
$perms += 'Microsoft.Insights/alertRules/*','Microsoft.Support/*'
$role.Actions = $perms
$role.NotActions = (Get-AzureRmRoleDefinition -Name 'Virtual Machine Contributor').NotActions
$subs = '/subscriptions/00000000-0000-0000-0000-000000000000','/subscriptions/11111111-1111-1111-1111-111111111111'
$role.AssignableScopes = $subs
New-AzureRmRoleDefinition -Role $role
```

### <a name="create-role-with-json-template"></a>JSON テンプレートを使用したロールの作成

カスタム ロールのソース定義として JSON テンプレートを使用できます。 次の例では、ストレージと計算リソースへの読み取りアクセス、サポートへのアクセスを許可するカスタム ロールを作成し、そのロールを 2 つのサブスクリプションに追加します。 以下のコード例を含んだ新しいファイル `C:\CustomRoles\customrole1.json` を作成します。 新しい ID が自動的に生成されるため、最初のロール作成では ID を `null` に設定する必要があります。 

```json
{
  "Name": "Custom Role 1",
  "Id": null,
  "IsCustom": true,
  "Description": "Allows for read access to Azure storage and compute resources and access to support",
  "Actions": [
    "Microsoft.Compute/*/read",
    "Microsoft.Storage/*/read",
    "Microsoft.Support/*"
  ],
  "NotActions": [
  ],
  "AssignableScopes": [
    "/subscriptions/00000000-0000-0000-0000-000000000000",
    "/subscriptions/11111111-1111-1111-1111-111111111111"
  ]
}
```

ロールをサブスクリプションに追加するには、次の PowerShell コマンドを実行します。

```azurepowershell
New-AzureRmRoleDefinition -InputFile "C:\CustomRoles\customrole1.json"
```

## <a name="modify-a-custom-role"></a>カスタム ロールの修正

カスタム ロールの作成と同様に、`PSRoleDefinition` オブジェクトまたは JSON テンプレートを使用して既存のカスタム ロールを変更できます。

### <a name="modify-role-with-psroledefinition-object"></a>PSRoleDefinition オブジェクトを使用したロールの変更

カスタム ロールを修正するには、まず [Get-AzureRmRoleDefinition](/powershell/module/azurerm.resources/get-azurermroledefinition) コマンドを使用してロール定義を取得します。 次に、必要に応じてロール定義を変更します。 最後に、[Set-AzureRmRoleDefinition](/powershell/module/azurerm.resources/set-azurermroledefinition) コマンドを使用して変更したロール定義を保存します。

次の例では、 `Microsoft.Insights/diagnosticSettings/*` 操作が *仮想マシン オペレーター* カスタム ロールに追加されます。

```azurepowershell
$role = Get-AzureRmRoleDefinition "Virtual Machine Operator"
$role.Actions.Add("Microsoft.Insights/diagnosticSettings/*")
Set-AzureRmRoleDefinition -Role $role
```

```Example
PS C:\> $role = Get-AzureRmRoleDefinition "Virtual Machine Operator"
PS C:\> $role.Actions.Add("Microsoft.Insights/diagnosticSettings/*")
PS C:\> Set-AzureRmRoleDefinition -Role $role

Name             : Virtual Machine Operator
Id               : 88888888-8888-8888-8888-888888888888
IsCustom         : True
Description      : Can monitor and restart virtual machines.
Actions          : {Microsoft.Storage/*/read, Microsoft.Network/*/read, Microsoft.Compute/*/read,
                   Microsoft.Compute/virtualMachines/start/action...}
NotActions       : {}
AssignableScopes : {/subscriptions/00000000-0000-0000-0000-000000000000,
                   /subscriptions/11111111-1111-1111-1111-111111111111}
```

次の例では、Azure サブスクリプションが *仮想マシン オペレーター* カスタム ロールの割り当て可能なスコープに追加されます。

```azurepowershell
Get-AzureRmSubscription -SubscriptionName Production3

$role = Get-AzureRmRoleDefinition "Virtual Machine Operator"
$role.AssignableScopes.Add("/subscriptions/22222222-2222-2222-2222-222222222222")
Set-AzureRmRoleDefinition -Role $role
```

```Example
PS C:\> Get-AzureRmSubscription -SubscriptionName Production3

Name     : Production3
Id       : 22222222-2222-2222-2222-222222222222
TenantId : 99999999-9999-9999-9999-999999999999
State    : Enabled

PS C:\> $role = Get-AzureRmRoleDefinition "Virtual Machine Operator"
PS C:\> $role.AssignableScopes.Add("/subscriptions/22222222-2222-2222-2222-222222222222")
PS C:\> Set-AzureRmRoleDefinition -Role $role

Name             : Virtual Machine Operator
Id               : 88888888-8888-8888-8888-888888888888
IsCustom         : True
Description      : Can monitor and restart virtual machines.
Actions          : {Microsoft.Storage/*/read, Microsoft.Network/*/read, Microsoft.Compute/*/read,
                   Microsoft.Compute/virtualMachines/start/action...}
NotActions       : {}
AssignableScopes : {/subscriptions/00000000-0000-0000-0000-000000000000,
                   /subscriptions/11111111-1111-1111-1111-111111111111,
                   /subscriptions/22222222-2222-2222-2222-222222222222}
```

### <a name="modify-role-with-json-template"></a>JSON テンプレートを使用したロールの変更

前の JSON テンプレートを使用して、既存のカスタム ロールを簡単に変更して、操作を追加または削除できます。 次の例のように、JSON テンプレートを更新し、ネットワークの読み取り操作を追加します。 テンプレートに示されている定義は、既存の定義に累積的には適用されません。つまり、ロールは、テンプレートに指定したとおりに表れます。 また、Id フィールドをロールの ID で更新する必要もあります。 この値が不明な場合は、[Get-AzureRmRoleDefinition](/powershell/module/azurerm.resources/get-azurermroledefinition) コマンドレットを使用してこの情報を取得できます。

```json
{
  "Name": "Custom Role 1",
  "Id": "acce7ded-2559-449d-bcd5-e9604e50bad1",
  "IsCustom": true,
  "Description": "Allows for read access to Azure storage and compute resources and access to support",
  "Actions": [
    "Microsoft.Compute/*/read",
    "Microsoft.Storage/*/read",
    "Microsoft.Network/*/read",
    "Microsoft.Support/*"
  ],
  "NotActions": [
  ],
  "AssignableScopes": [
    "/subscriptions/00000000-0000-0000-0000-000000000000",
    "/subscriptions/11111111-1111-1111-1111-111111111111"
  ]
}
```

既存のロールを更新するには、次の PowerShell コマンドを実行します。

```azurepowershell
Set-AzureRmRoleDefinition -InputFile "C:\CustomRoles\customrole1.json"
```

## <a name="delete-a-custom-role"></a>カスタム ロールの削除

カスタム ロールを削除するには、[Remove-AzureRmRoleDefinition](/powershell/module/azurerm.resources/remove-azurermroledefinition) コマンドを使用します。

次の例では、 *仮想マシン オペレーター* カスタム ロールが削除されます。

```azurepowershell
Get-AzureRmRoleDefinition "Virtual Machine Operator"
Get-AzureRmRoleDefinition "Virtual Machine Operator" | Remove-AzureRmRoleDefinition
```

```Example
PS C:\> Get-AzureRmRoleDefinition "Virtual Machine Operator"

Name             : Virtual Machine Operator
Id               : 88888888-8888-8888-8888-888888888888
IsCustom         : True
Description      : Can monitor and restart virtual machines.
Actions          : {Microsoft.Storage/*/read, Microsoft.Network/*/read, Microsoft.Compute/*/read,
                   Microsoft.Compute/virtualMachines/start/action...}
NotActions       : {}
AssignableScopes : {/subscriptions/00000000-0000-0000-0000-000000000000,
                   /subscriptions/11111111-1111-1111-1111-111111111111}

PS C:\> Get-AzureRmRoleDefinition "Virtual Machine Operator" | Remove-AzureRmRoleDefinition

Confirm
Are you sure you want to remove role definition with name 'Virtual Machine Operator'.
[Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"): Y
```

## <a name="see-also"></a>関連項目

* [Azure リソース マネージャーでの Windows PowerShell の使用](../azure-resource-manager/powershell-azure-resource-manager.md)

[!INCLUDE [role-based-access-control-toc.md](../../includes/role-based-access-control-toc.md)]
