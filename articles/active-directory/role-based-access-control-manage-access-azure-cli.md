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
	ms.date="01/04/2016"
	ms.author="inhenk"/>

# Azure コマンド ライン インターフェイス (CLI) を使用したロールベースのアクセス制御 (RBAC) の管理

> [AZURE.SELECTOR]
- [PowerShell](role-based-access-control-manage-access-powershell.md)
- [Azure CLI](role-based-access-control-manage-access-azure-cli.md)
- [REST API](role-based-access-control-manage-access-rest.md)

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

## カスタム ロールの作成
カスタム ロールを作成するには、`azure role create` コマンドを使用します。

次の例では、「*Virtual Machine Operator*」という名前のカスタム ロールが作成されます。このロールは、*Microsoft.Compute*、*Microsoft.Storage*、*Microsoft.Network* リソース プロバイダーのすべての読み取り操作を許可し、仮想マシンの起動、再起動、監視を許可します。カスタム ロールは 2 つのサブスクリプションで使用できます。この例では、入力として json ファイルが採用されます。

![](./media/role-based-access-control-manage-access-azure-cli/2-azure-role create-1.png)

![](./media/role-based-access-control-manage-access-azure-cli/2-azure-role-create-2.png)

## カスタム ロールの修正

カスタム ロールを修正するには、最初に azure role show コマンドを使用してロール定義を取得します。必要に応じてロール定義を変更します。最後に、azure role set コマンドを使用して変更したロール定義を保存します。

次の例では、Microsoft.Insights/diagnosticSettings/* 操作が Actions に追加され、Azure サブスクリプションが Virtual Machine Operator カスタム ロールの AssignableScopes に追加されます。

![](./media/role-based-access-control-manage-access-azure-cli/3-azure-role-set-1.png)

![](./media/role-based-access-control-manage-access-azure-cli/3-azure-role-set2.png)

## カスタム ロールの削除

カスタム ロールを削除するには、最初に `azure role show` コマンドを使用し、ロールの **Id** を決定します。次に、`azure role delete` コマンドを使用し、**Id** を指定してロールを削除します。

次の例では、*Virtual Machine Operator* カスタム ロールが削除されます。

![](./media/role-based-access-control-manage-access-azure-cli/4-azure-role-delete.png)

## カスタム ロールの一覧表示

特定のスコープで割り当て可能なロールを一覧表示するには、`azure role list` コマンドを使用します。

次の例では、選択したサブスクリプションで割り当て可能なすべてのロールが一覧表示されます。

![](./media/role-based-access-control-manage-access-azure-cli/5-azure-role-list1.png)

次の例では、*Virtual Machine Operator* カスタム ロールは *Production4* サブスクリプションでは利用できません。そのサブスクリプションはロールの **AssignableScopes** にないためです。

![](./media/role-based-access-control-manage-access-azure-cli/5-azure-role-list2.png)





## RBAC のトピック
[AZURE.INCLUDE [role-based-access-control-toc.md](../../includes/role-based-access-control-toc.md)]

<!---HONumber=AcomDC_0107_2016-->