<properties
	pageTitle="Azure PowerShell を使用したロールベースのアクセス制御 (RBAC) の管理 |Microsoft Azure"
	description="Azure PowerShell を使用して、ロールの表示、ロールの割り当て、ロールの割り当ての削除などを行って RBAC を管理する方法。"
	services="active-directory"
	documentationCenter=""
	authors="IHenkel"
	manager="stevenpo"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.workload="identity"
	ms.date="10/12/2015"
	ms.author="inhenk"/>

# Azure PowerShell を使用したロールベースのアクセス制御 (RBAC) の管理
<!-- Azure Selector -->
> [AZURE.SELECTOR]
- [PowerShell](role-based-access-control-manage-access-powershell.md)
- [Azure CLI](role-based-access-control-manage-access-azure-cli.md)

## RBAC のロールの表示
### 使用可能なすべてのロールの表示
割り当てることができる RBAC のロールを表示したり、アクセス権が付与されている操作を調べたりするには、次のコマンドを使用します。

		Get-AzureRoleDefinition

![](./media/role-based-access-control-manage-access-powershell/1-get-azure-rm-role-definition1.png)

### ロールのアクションの表示
特定のロールのアクションを表示するには、次のコマンドを使用します。

    Get-AzureRoleDefinition <role name>

![](./media/role-based-access-control-manage-access-powershell/1-get-azure-rm-role-definition2.png)

## アクセス権の表示
### 選択したサブスクリプションのすべてのロールの割り当ての表示
指定したサブスクリプション、リソース、またはリソース グループで有効な RBAC のアクセス権の割り当てを表示するには、次のコマンドを使用します。

    Get-AzureRoleAssignment

###	リソース グループに対して有効なロールの割り当ての表示
リソース グループのアクセス権の割り当てを表示するには、次のコマンドを使用します。

    Get-AzureRoleAssignment -ResourceGroupName <resource group name>

![](./media/role-based-access-control-manage-access-powershell/4-get-azure-rm-role-assignment1.png)

### ユーザーへのロールの割り当て (ユーザー グループに割り当てられているロールを含む) の表示
指定したユーザーと、そのユーザーが属するグループへのアクセス権の割り当てを表示するには、次のコマンドを使用します。

    Get-AzureRoleAssignment -ExpandPrincipalGroups

![](./media/role-based-access-control-manage-access-powershell/4-get-azure-rm-role-assignment2.png)

### 従来のサービス管理者ロールと共同管理者ロールの割り当ての表示
従来のサブスクリプション管理者と共同管理者のアクセス権の割り当てを表示するには、次のコマンドを使用します。

    Get-AzureRoleAssignment -IncludeClassicAdministrators

## アクセスの許可
### オブジェクト ID の検索
以下のコマンド シーケンスを使用するには、オブジェクト ID を検索しておく必要があります。使用するサブスクリプション ID が既にわかっていることを前提としています。それ以外の場合は、MSDN の 「[Get-AzureSubscription](https://msdn.microsoft.com/library/dn495302.aspx)」をご覧ください。

#### Azure AD グループのオブジェクト ID の検索
Azure AD グループのオブジェクト ID を取得するには、次のコマンドを使用します。

    Get-AzureADGroup -SearchString <group name in quotes>

#### Azure AD サービス プリンシパルのオブジェクト ID の検索
Azure AD サービス プリンシパルのオブジェクト ID を取得するには、Get-AzureADServicePrincipal -SearchString<service name in quotes> を使用します。

### サブスクリプションのスコープでのグループへのロールの割り当て
サブスクリプションのスコープでグループにアクセス権を付与するには、次のコマンドを使用します。

    New-AzureRoleAssignment -ObjId <object id> -RoleDefinitionName <role name in quotes> -Scope <scope such as subscription/subscription id>

![](./media/role-based-access-control-manage-access-powershell/2-new-azure-rm-role-assignment1.png)

### サブスクリプションのスコープでのアプリケーションへのロールの割り当て
サブスクリプションのスコープでアプリケーションにアクセス権を付与するには、次のコマンドを使用します。

    New-AzureRoleAssignment -ObjId <object id> -RoleDefinitionName <role name in quotes> -Scope <scope such as subscription/subscription id>

![](./media/role-based-access-control-manage-access-powershell/2-new-azure-rm-role-assignment2.png)

### リソース グループのスコープでのユーザーへのロールの割り当て
リソース グループのスコープでユーザーにアクセス権を付与するには、次のコマンドを使用します。

    New-AzureRoleAssignment -SignInName <email of user> -RoleDefinitionName <role name in quotes> -ResourceGroupName <resource group name>

![](./media/role-based-access-control-manage-access-powershell/2-new-azure-rm-role-assignment3.png)

### リソースのスコープでのグループへのロールの割り当て
リソースのスコープでグループにアクセス権を付与するには、次のコマンドを使用します。

    New-AzureRoleAssignment -ObjId <object id> -RoleDefinitionName <role name in quotes> -ResourceName <resource name> -ResourceType <resource type> -ParentResource <parent resource> -ResourceGroupName <resource group name>

![](./media/role-based-access-control-manage-access-powershell/2-new-azure-rm-role-assignment4.png)

## アクセス権の削除
ユーザー、グループ、アプリケーションのアクセス権を削除するには、次のコマンドを使用します。

    Remove-AzureRoleAssignment -ObjId <object id> -RoleDefinitionName <role name> -Scope <scope such as subscription/subscription id>

![](./media/role-based-access-control-manage-access-powershell/3-remove-azure-rm-role-assignment.png)

## RBAC のトピック
[AZURE.INCLUDE [role-based-access-control-toc.md](../../includes/role-based-access-control-toc.md)]

<!---HONumber=Oct15_HO3-->