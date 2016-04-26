<properties
	pageTitle="Azure PowerShell を使用したロールベースのアクセス制御 (RBAC) の管理 |Microsoft Azure"
	description="Azure PowerShell を使用して RBAC を管理する方法 (ロールの表示、ロールの割り当て、ロール割り当ての削除など)。"
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
	ms.date="04/12/2016"
	ms.author="kgremban"/>

# Azure PowerShell を使用したロールベースのアクセス制御の管理

> [AZURE.SELECTOR]
- [PowerShell](role-based-access-control-manage-access-powershell.md)
- [Azure CLI](role-based-access-control-manage-access-azure-cli.md)
- [REST API](role-based-access-control-manage-access-rest.md)


Azure ポータルと Azure リソース管理 API のロールベースのアクセス制御 (RBAC) を使用すると、サブスクリプションへのアクセスを詳細に管理できます。この機能を使用すると、Active Directory ユーザー、グループ、サービス プリンシパルに特定のスコープで役割を割り当てて、アクセス権を付与できます。

PowerShell を使って RBAC を管理するには、以下を用意しておく必要があります。

- Azure PowerShell バージョン 0.8.8 以降。最新バージョンをインストールして、Azure サブスクリプションに関連付けるには、「[How to install and configure Azure PowerShell (Azure PowerShell のインストールと構成の方法)](../powershell-install-configure.md)」をご覧ください。

- Azure Resource Manager コマンドレット。PowerShell で [Azure Resource Manager コマンドレット](https://msdn.microsoft.com/library/mt125356.aspx)をインストールします。

## ロールの一覧表示

### 使用可能なすべてのロールの表示
割り当てることができる RBAC のロールを表示したり、アクセス権が付与されている操作を調べたりするには、次のコマンドを使用します。

		Get-AzureRmRoleDefinition

![RBAC PowerShell - Get-AzureRmRoleDefinition - スクリーンショット](./media/role-based-access-control-manage-access-powershell/1-get-azure-rm-role-definition1.png)

### ロールのアクションの表示
特定のロールのアクションを表示するには、次のコマンドを使用します。

    Get-AzureRmRoleDefinition <role name>

![RBAC PowerShell - 特定のロールの Get-AzureRmRoleDefinition - スクリーンショット](./media/role-based-access-control-manage-access-powershell/1-get-azure-rm-role-definition2.png)

## アクセスできるユーザーの確認
RBAC のアクセス権の割り当てを表示するには、次のコマンドを使用します。

    Get-AzureRmRoleAssignment

###	特定のスコープでのロールの割り当ての表示
指定したサブスクリプション、リソース グループ、またはリソースに対するすべてのアクセス権の割り当てを表示できます。たとえば、リソース グループのすべてのアクティブな割り当てを表示するには、次のコマンドを使用します。

    Get-AzureRmRoleAssignment -ResourceGroupName <resource group name>

![RBAC PowerShell - リソース グループの Get-AzureRmRoleAssignment - スクリーンショット](./media/role-based-access-control-manage-access-powershell/4-get-azure-rm-role-assignment1.png)

### ユーザーに割り当てられたロールの表示
指定したユーザーに割り当てられたすべてのロール (そのユーザーのグループに割り当てられたロールを含む) を表示するには、次のコマンドを使用します。

    Get-AzureRmRoleAssignment -SignInName <User email> -ExpandPrincipalGroups

![RBAC PowerShell - ユーザーの Get-AzureRmRoleAssignment - スクリーンショット](./media/role-based-access-control-manage-access-powershell/4-get-azure-rm-role-assignment2.png)

### 従来のサービス管理者ロールと共同管理者ロールの割り当ての表示
従来のサブスクリプション管理者と共同管理者のアクセス権の割り当てを表示するには、次のコマンドを使用します。

    Get-AzureRmRoleAssignment -IncludeClassicAdministrators

## アクセス権の付与
### オブジェクト ID の検索
ロールを割り当てるには、オブジェクト (ユーザー、グループ、またはアプリケーション) とスコープの両方を特定する必要があります。

サブスクリプション ID がわからない場合は、Azure ポータルの **[サブスクリプション]** ブレードで、その ID を見つけることができます。または、MSDN で [Get-AzureSubscription](https://msdn.microsoft.com/library/dn495302.aspx) を使って ID のクエリを実行する方法を確認してください。

Azure AD グループのオブジェクト ID を取得するには、次のコマンドを使用します。

    Get-AzureRmADGroup -SearchString <group name in quotes>

Azure AD サービス プリンシパル、つまりアプリケーションのオブジェクト ID を取得するには、次のコマンドを使用します。

    Get-AzureRmADServicePrincipal -SearchString <service name in quotes>

### サブスクリプションのスコープでのアプリケーションへのロールの割り当て
サブスクリプションのスコープでアプリケーションにアクセス権を付与するには、次のコマンドを使用します。

    New-AzureRmRoleAssignment -ObjectId <application id> -RoleDefinitionName <role name in quotes> -Scope <subscription id>

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

    Remove-AzureRmRoleAssignment -ObjectId <object id> -RoleDefinitionName <role name> -Scope <scope such as subscription id>

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

## 関連項目
- [Azure リソース マネージャーでの Windows PowerShell の使用](../powershell-azure-resource-manager.md)[AZURE.INCLUDE [role-based-access-control-toc.md](../../includes/role-based-access-control-toc.md)]

<!---HONumber=AcomDC_0413_2016-->