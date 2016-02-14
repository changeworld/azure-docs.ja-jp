<properties
	pageTitle="Azure PowerShell を使用したロールベースのアクセス制御 (RBAC) の管理 |Microsoft Azure"
	description="Azure PowerShell を使用して、ロールの表示、ロールの割り当て、ロールの割り当ての削除などを行って RBAC を管理する方法。"
	services="active-directory"
	documentationCenter=""
	authors="kgremban"
	manager="stevenpo"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.workload="identity"
	ms.date="01/22/2016"
	ms.author="kgremban"/>

# Azure PowerShell を使用したロールベースのアクセス制御の管理
> [AZURE.SELECTOR]
- [PowerShell](role-based-access-control-manage-access-powershell.md)
- [Azure CLI](role-based-access-control-manage-access-azure-cli.md)
- [REST API](role-based-access-control-manage-access-rest.md)

## ロールベースのアクセス制御 (RBAC) ロールの一覧
### 使用可能なすべてのロールの表示
割り当てることができる RBAC のロールを表示したり、アクセス権が付与されている操作を調べたりするには、次のコマンドを使用します。

		Get-AzureRmRoleDefinition

![RBAC PowerShell - Get-AzureRmRoleDefinition - スクリーンショット](./media/role-based-access-control-manage-access-powershell/1-get-azure-rm-role-definition1.png)

### ロールのアクションの表示
特定のロールのアクションを表示するには、次のコマンドを使用します。

    Get-AzureRmRoleDefinition <role name>

![RBAC PowerShell - 特定のロールの Get-AzureRmRoleDefinition - スクリーンショット](./media/role-based-access-control-manage-access-powershell/1-get-azure-rm-role-definition2.png)

## アクセス権の表示
### 選択したサブスクリプションのすべてのロールの割り当ての表示
指定したサブスクリプション、リソース、またはリソース グループで有効な RBAC のアクセス権の割り当てを表示するには、次のコマンドを使用します。

    Get-AzureRmRoleAssignment

###	リソース グループに対して有効なロールの割り当ての表示
リソース グループのアクセス権の割り当てを表示するには、次のコマンドを使用します。

    Get-AzureRmRoleAssignment -ResourceGroupName <resource group name>

![RBAC PowerShell - リソース グループの Get-AzureRmRoleAssignment - スクリーンショット](./media/role-based-access-control-manage-access-powershell/4-get-azure-rm-role-assignment1.png)

### ユーザーへのロールの割り当て (ユーザー グループに割り当てられているロールを含む) の表示
指定したユーザーと、そのユーザーが属するグループへのアクセス権の割り当てを表示するには、次のコマンドを使用します。

    Get-AzureRmRoleAssignment -ExpandPrincipalGroups

![RBAC PowerShell - ユーザーの Get-AzureRmRoleAssignment - スクリーンショット](./media/role-based-access-control-manage-access-powershell/4-get-azure-rm-role-assignment2.png)

### 従来のサービス管理者ロールと共同管理者ロールの割り当ての表示
従来のサブスクリプション管理者と共同管理者のアクセス権の割り当てを表示するには、次のコマンドを使用します。

    Get-AzureRmRoleAssignment -IncludeClassicAdministrators

## アクセス権の付与
### オブジェクト ID の検索
以下のコマンド シーケンスを使用するには、オブジェクト ID を検索しておく必要があります。使用するサブスクリプション ID が既にわかっていることを前提としています。それ以外の場合は、MSDN の 「[Get-AzureSubscription](https://msdn.microsoft.com/library/dn495302.aspx)」をご覧ください。

#### Azure AD グループのオブジェクト ID の検索
Azure AD グループのオブジェクト ID を取得するには、次のコマンドを使用します。

    Get-AzureRmADGroup -SearchString <group name in quotes>

#### Azure AD サービス プリンシパルのオブジェクト ID の検索
Azure AD サービス プリンシパル使用のオブジェクト ID の検索

    Get-AzureRmADServicePrincipal -SearchString <service name in quotes>

### サブスクリプションのスコープでのグループへのロールの割り当て
サブスクリプションのスコープでグループにアクセス権を付与するには、次のコマンドを使用します。

    New-AzureRmRoleAssignment -ObjectId <object id> -RoleDefinitionName <role name in quotes> -Scope <scope such as subscription/subscription id>

![RBAC PowerShell - New-AzureRmRoleAssignment - スクリーンショット](./media/role-based-access-control-manage-access-powershell/2-new-azure-rm-role-assignment1.png)

### サブスクリプションのスコープでのアプリケーションへのロールの割り当て
サブスクリプションのスコープでアプリケーションにアクセス権を付与するには、次のコマンドを使用します。

    New-AzureRmRoleAssignment -ObjectId <object id> -RoleDefinitionName <role name in quotes> -Scope <scope such as subscription/subscription id>

![RBAC PowerShell - New-AzureRmRoleAssignment - スクリーンショット](./media/role-based-access-control-manage-access-powershell/2-new-azure-rm-role-assignment2.png)

### リソース グループのスコープでのユーザーへのロールの割り当て
リソース グループのスコープでユーザーにアクセス権を付与するには、次のコマンドを使用します。

    New-AzureRmRoleAssignment -SignInName <email of user> -RoleDefinitionName <role name in quotes> -ResourceGroupName <resource group name>

![RBAC PowerShell - New-AzureRmRoleAssignment - スクリーンショット](./media/role-based-access-control-manage-access-powershell/2-new-azure-rm-role-assignment3.png)

### リソースのスコープでのグループへのロールの割り当て
リソースのスコープでグループにアクセス権を付与するには、次のコマンドを使用します。

    New-AzureRmRoleAssignment -ObjectId <object id> -RoleDefinitionName <role name in quotes> -ResourceName <resource name> -ResourceType <resource type> -ParentResource <parent resource> -ResourceGroupName <resource group name>

![RBAC PowerShell - New-AzureRmRoleAssignment - スクリーンショット](./media/role-based-access-control-manage-access-powershell/2-new-azure-rm-role-assignment4.png)

## アクセス権の削除
ユーザー、グループ、アプリケーションのアクセス権を削除するには、次のコマンドを使用します。

    Remove-AzureRmRoleAssignment -ObjectId <object id> -RoleDefinitionName <role name> -Scope <scope such as subscription/subscription id>

![RBAC PowerShell - Remove-AzureRmRoleAssignment - スクリーンショット](./media/role-based-access-control-manage-access-powershell/3-remove-azure-rm-role-assignment.png)

## カスタム ロールの作成
カスタム ロールを作成するには、`New-AzureRmRoleDefinition` コマンドを使用します。

次の例では、「*Virtual Machine Operator*」という名前のカスタム ロールが作成されます。このロールは、*Microsoft.Compute*、*Microsoft.Storage*、*Microsoft.Network* リソース プロバイダーのすべての読み取り操作を許可し、仮想マシンの起動、再起動、監視を許可します。カスタム ロールは 2 つのサブスクリプションで使用できます。

![RBAC PowerShell - Get-AzureRmRoleDefinition - スクリーンショット](./media/role-based-access-control-manage-access-powershell/2-new-azurermroledefinition.png)

## カスタム ロールの修正
カスタム ロールを修正するには、最初に `Get-AzureRmRoleDefinition` コマンドを使用してロール定義を取得します。必要に応じてロール定義を変更します。最後に、`Set-AzureRmRoleDefinition` コマンドを使用して変更したロール定義を保存します。

次の例では、`Microsoft.Insights/diagnosticSettings/*` 操作が *Virtual Machine Operator* カスタム ロールに追加されます。

![RBAC PowerShell - Set-AzureRmRoleDefinition - スクリーンショット](./media/role-based-access-control-manage-access-powershell/3-set-azurermroledefinition-1.png)

次の例では、Azure サブスクリプションが Virtual Machine Operator カスタム ロールの割り当て可能なスコープに追加されます。

![RBAC PowerShell - Set-AzureRmRoleDefinition - スクリーンショット](./media/role-based-access-control-manage-access-powershell/3-set-azurermroledefinition-2.png)

## カスタム ロールの削除

カスタム ロールを削除するには、`Remove-AzureRmRoleDefinition` コマンドを使用します。

次の例では、*Virtual Machine Operator* カスタム ロールが削除されます。

![RBAC PowerShell - Remove-AzureRmRoleDefinition - スクリーンショット](./media/role-based-access-control-manage-access-powershell/4-remove-azurermroledefinition.png)

## カスタム ロールの一覧表示
特定のスコープで割り当て可能なロールを一覧表示するには、`Get-AzureRmRoleDefinition` コマンドを使用します。

次の例では、選択したサブスクリプションで割り当て可能なすべてのロールが一覧表示されます。

![RBAC PowerShell - Get-AzureRmRoleDefinition - スクリーンショット](./media/role-based-access-control-manage-access-powershell/5-get-azurermroledefinition-1.png)

次の例では、*Virtual Machine Operator* カスタム ロールは *Production4* サブスクリプションでは利用できません。そのサブスクリプションはロールの **AssignableScopes** にないためです。

![RBAC PowerShell - Get-AzureRmRoleDefinition - スクリーンショット](./media/role-based-access-control-manage-access-powershell/5-get-azurermroledefinition2.png)

## RBAC のトピック
[AZURE.INCLUDE [role-based-access-control-toc.md](../../includes/role-based-access-control-toc.md)]

<!---HONumber=AcomDC_0204_2016-->