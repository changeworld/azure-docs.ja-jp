<properties
	pageTitle="Azure CLI を使用したロールベースのアクセス制御 (RBAC) の管理 |Microsoft Azure"
	description="Azure コマンド ライン インターフェイスを使用して、ロールとロールのアクションの表示、サブスクリプションとアプリケーションのスコープへのロールの割り当てなどを行って、ロールベースのアクセス制御 (RBAC) を管理する方法について説明します。"
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

# Azure コマンド ライン インターフェイス (CLI) を使用したロールベースのアクセス制御 (RBAC) の管理
<!-- Azure Selector -->
> [AZURE.SELECTOR]
- [PowerShell](role-based-access-control-manage-access-powershell.md)
- [Azure CLI](role-based-access-control-manage-access-azure-cli.md)

## RBAC のロールの表示
###	使用可能なすべてのロールの表示
使用可能なすべてのロールを表示するには、次のコマンドを使用します。

		azure role list

次の例では、*使用可能なすべてのロール*のリストを表示しています。

![](./media/role-based-access-control-manage-access-azure-cli/1-azure-role-list.png)

###	ロールのアクションの表示
ロールのアクションを表示するには、次のコマンドを使用します。

    azure role show <role in quotes>

次の例では、*共同作業者*ロールと*仮想マシンの共同作業者*ロールのアクションを表示しています。

![](./media/role-based-access-control-manage-access-azure-cli/1-azure-role-show.png)

##	アクセス権の表示
###	リソース グループに対して有効なロールの割り当ての表示
リソース グループに対して有効なロールの割り当てを表示するには、次のコマンドを使用します。

    azure role assignment list --resource-group <resource group name>

次の例では、*pharma-sales-projecforcast* グループに対して有効なロールの割り当てを表示しています。

![](./media/role-based-access-control-manage-access-azure-cli/4-azure-role-assignment-list-1.png)

###	ユーザーへのロールの割り当て (ユーザーのグループに割り当てられているロールを含む) の表示

次の例では、*pharma-sales-projecforcast* グループに対して有効なロールの割り当てを表示しています。

![](./media/role-based-access-control-manage-access-azure-cli/4-azure-role-assignment-list-2.png)

##	アクセス権の付与
割り当てるロールを特定した後、アクセス権を付与するには、次のコマンドを使用します。

    azure role assignment create

###	サブスクリプションのスコープでのグループへのロールの割り当て
サブスクリプションのスコープでグループにロールを割り当てるには、次のコマンドを使用します。

   azure role assignment create --objId <group's object id> --role <name of role> --scope <subscription/subscription id>

次の例では、*サブスクリプション*のスコープで *Christine Koch のチーム*に*閲覧者*ロールを割り当てています。

![](./media/role-based-access-control-manage-access-azure-cli/2-azure-role-assignment-create-1.png)

###	サブスクリプションのスコープでのアプリケーションへのロールの割り当て
サブスクリプションのスコープでアプリケーションにロールを割り当てるには、次のコマンドを使用します。

    azure role assignment create --objId  <applications's object id> --role <name of role> --scope <subscription/subscription id>

次の例では、選択したサブスクリプションの *Azure AD* アプリケーションに*共同作業者*ロールを付与しています。

 ![](./media/role-based-access-control-manage-access-azure-cli/2-azure-role-assignment-create-2.png)

###	リソース グループのスコープでのユーザーへのロールの割り当て
リソース グループのスコープでユーザーにロールを割り当てるには、次のコマンドを使用します。

    azure role assignment create --signInName  <user's email address> --roleName <name of role in quotes> --resourceGroup <resource group name>

次の例では、*Pharma-Sales-ProjectForcast* リソース グループのスコープで、ユーザー **samert@aaddemo.com* に*仮想マシンの共同作業者*ロールを付与しています。

![](./media/role-based-access-control-manage-access-azure-cli/2-azure-role-assignment-create-3.png)

###	リソースのスコープでのグループへのロールの割り当て
リソースのスコープでグループにロールを割り当てるには、次のコマンドを使用します。

    azure role assignment create --objId  <group id> --roleName <name of role in quotes> --resource-name <resource group name> --resource-type <resource group type> --parent <resource group parent> --resource-group <resource group>

次の例では、*サブネット*の *Azure AD* グループに*仮想マシンの共同作業者*ロールを付与しています。

![](./media/role-based-access-control-manage-access-azure-cli/2-azure-role-assignment-create-4.png)

##	アクセス権の削除
ロールの割り当てを削除するには、次のコマンドを使用します。

    azure role assignment delete --objId <object id to from which to remove role> --roleName <role name>

次の例では、*Pharma-Sales-ProjectForcast* リソース グループの **sammert@aaddemo.com* から、*仮想マシンの共同作業者*ロールの割り当てを削除しています。次に、このサブスクリプションのグループからロールの割り当てを削除しています。

![](./media/role-based-access-control-manage-access-azure-cli/3-azure-role-assignment-delete.png)

## RBAC のトピック
[AZURE.INCLUDE [role-based-access-control-toc.md](../../includes/role-based-access-control-toc.md)]

<!---HONumber=Oct15_HO3-->