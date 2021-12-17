---
title: Azure Active Directory 認証を有効にする
description: この記事では、Azure SQL Database と Azure SQL Managed Instance で Azure Active Directory (Azure AD) 専用認証機能を有効にする手順について説明します。
titleSuffix: Azure SQL Database & Azure SQL Managed Instance
ms.service: sql-db-mi
ms.subservice: security
ms.topic: tutorial
author: GithubMirek
ms.author: mireks
ms.reviewer: vanto
ms.date: 11/02/2021
ms.openlocfilehash: 09e855391a591e39d31c1eb939bce6c2dc1cc6b2
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/03/2021
ms.locfileid: "131473625"
---
# <a name="tutorial-enable-azure-active-directory-only-authentication-with-azure-sql"></a>チュートリアル: Azure SQL を使用して Azure Active Directory 専用認証を有効にする

[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

この記事では、Azure SQL Database および Azure SQL Managed Instance 内で [Azure AD 専用認証](authentication-azure-ad-only-authentication.md)機能を有効にする手順について説明します。 Azure AD 専用認証を有効にした SQL Database または SQL Managed Instance をプロビジョニングしようとしている場合は、[Azure SQL で Azure AD 専用認証を有効にしたサーバーを作成する](authentication-azure-ad-only-authentication-create-server.md)方法に関する記事をご覧ください。

このチュートリアルでは、次の作業を行う方法について説明します。

> [!div class="checklist"]
> - Azure AD 専用認証を有効にするためのロールを割り当てる
> - Azure portal、Azure CLI、または PowerShell を使用して Azure AD 専用認証を有効にする
> - Azure AD 専用認証が有効かどうかを確認する
> - Azure SQL への接続をテストする
> - Azure portal、Azure CLI、または PowerShell を使用して Azure AD 専用認証を無効にする


## <a name="prerequisites"></a>前提条件

- Azure AD インスタンス。 詳細については、「[Azure SQL での Azure AD 認証を構成して管理する](authentication-aad-configure.md)」を参照してください。
- データベースとログインまたはユーザーを含む SQL Database または SQL Managed Instance。 「[クイック スタート:Azure SQL Database の単一データベースを作成する](single-database-create-quickstart.md)」(Azure SQL Database をまだ作成していない場合) または「[クイック スタート:Azure SQL マネージド インスタンスの作成](../managed-instance/instance-create-quickstart.md)」を参照してください。

## <a name="assign-role-to-enable-azure-ad-only-authentication"></a>Azure AD 専用認証を有効にするためのロールを割り当てる

Azure AD 専用認証を有効または無効にするには、このチュートリアルでこれらの操作を実行する Azure AD ユーザーに、選択された組み込みロールが必要です。 このチュートリアルでは、ユーザーに [SQL セキュリティ管理者](../../role-based-access-control/built-in-roles.md#sql-security-manager)ロールを割り当てます。

Azure AD アカウントにロールを割り当てる方法の詳細については、「[Azure Active Directory を使ってユーザーに管理者と管理者以外のロールを割り当てる](../../active-directory/fundamentals/active-directory-users-assign-role-azure-portal.md)」を参照してください

Azure AD 専用認証を有効または無効にするために必要なアクセス許可の詳細については、[Azure AD 専用認証に関する記事の「アクセス許可」セクション](authentication-azure-ad-only-authentication.md#permissions)を参照してください。

1. この例では、ユーザー `UserSqlSecurityManager@contoso.onmicrosoft.com` に **SQL セキュリティ管理者** ロールを割り当てます。 Azure AD ロールを割り当てることができる特権ユーザーを使用して [Azure portal](https://portal.azure.com/) にサインインします。
1. SQL Server リソースにアクセスし、メニューの **[アクセス制御 (IAM)]** を選択します。 **[追加]** ボタンを選択してから、 **[ロールの割り当ての追加]** を選択します。

   :::image type="content" source="media/authentication-azure-ad-only-authentication/azure-ad-only-authentication-access-control.png" alt-text="Azure portal の [アクセス制御] ウィンドウ":::

1. **[ロールの割り当ての追加]** ウィンドウで、 **[SQL セキュリティ管理者]** ロールを選択し、Azure AD 専用認証を有効または無効にすることができるユーザーを選択します。

   :::image type="content" source="media/authentication-azure-ad-only-authentication/azure-ad-only-authentication-access-control-add-role.png" alt-text="Azure portal の [ロールの割り当ての追加] ウィンドウ":::

1. **[保存]** をクリックします。

## <a name="enable-azure-ad-only-authentication"></a>Azure AD のみの認証を有効化する

# <a name="portal"></a>[ポータル](#tab/azure-portal)

## <a name="enable-in-sql-database-using-azure-portal"></a>Azure portal を使用して SQL Database で有効にする

Azure portal で Azure AD 専用認証を有効にするには、次の手順を参照してください。

1. [SQL セキュリティ管理者](../../role-based-access-control/built-in-roles.md#sql-security-manager)ロールを持つユーザーを使用して、[Azure portal](https://portal.azure.com/) にアクセスします。
1. SQL Server リソースにアクセスし、 **[設定]** メニューの **[Azure Active Directory]** を選択します。

   :::image type="content" source="media/authentication-azure-ad-only-authentication/azure-ad-only-authentication-portal.png" alt-text="Azure AD 専用認証の有効化メニュー":::

1. **Azure Active Directory 管理者** を追加していない場合は、Azure AD 専用認証を有効にする前にこれを設定する必要があります。
1. **[このサーバーの Azure Active Directory 認証のみをサポートする]** チェックボックスをオンにします。
1. **[Azure AD 認証のみを有効にする]** ポップアップが表示されます。 **[はい]** ボタンをクリックしてこの機能を有効にし、設定を **保存** します。

## <a name="enable-in-sql-managed-instance-using-azure-portal"></a>Azure portal を使用して SQL Managed Instance で有効にする

Azure portal で Azure AD 専用認証を有効にするには、次の手順を参照してください。

1. [SQL セキュリティ管理者](../../role-based-access-control/built-in-roles.md#sql-security-manager)ロールを持つユーザーを使用して、[Azure portal](https://portal.azure.com/) にアクセスします。
1. **SQL Managed Instance** リソースにアクセスし、 **[設定]** メニューの **[Active Directory 管理者]** を選択します。

1. **Azure Active Directory 管理者** を追加していない場合は、Azure AD 専用認証を有効にする前にこれを設定する必要があります。
1. **[このマネージド インスタンスの Azure Active Directory 認証のみをサポートする]** チェックボックスをオンにします。
1. **[Azure AD 認証のみを有効にする]** ポップアップが表示されます。 **[はい]** ボタンをクリックしてこの機能を有効にし、設定を **保存** します。

# <a name="the-azure-cli"></a>[Azure CLI](#tab/azure-cli)

## <a name="enable-in-sql-database-using-azure-cli"></a>Azure CLI を使用して SQL Database で有効にする

Azure CLI を使用して Azure SQL Database で Azure AD 専用認証を有効にするには、次のコマンドを参照してください。 [Azure CLI の最新バージョンをインストールします](/cli/azure/install-azure-cli-windows)。 Azure CLI バージョン **2.14.2** 以上が必要です。 これらのコマンドの詳細については、「[az sql server ad-only-auth](/cli/azure/sql/server/ad-only-auth)」を参照してください。

API を使用した Azure AD 専用認証の管理の詳細については、「[API を使用した Azure AD 専用認証の管理](authentication-azure-ad-only-authentication.md#managing-azure-ad-only-authentication-using-apis)」を参照してください。

> [!NOTE]
> Azure AD 専用認証を有効にする前に、サーバーの Azure AD 管理者を設定する必要があります。 そうしないと、Azure CLI コマンドが失敗します。
>
> Azure AD 専用認証を有効にするためにこれらのコマンドを実行するユーザーに必要なアクセス許可とアクションについては、[Azure AD 専用認証](authentication-azure-ad-only-authentication.md#permissions)に関する記事をご覧ください。

1. [SQL セキュリティ管理者](../../role-based-access-control/built-in-roles.md#sql-security-manager)ロールを持つアカウントを使用して、[Azure にサインイン](/cli/azure/authenticate-azure-cli)します。

   ```azurecli
   az login
   ```

1. 次のコマンドの `<myserver>` を SQL Server 名に置き換え、`<myresource>` を SQL Server を保持している Azure リソースに置き換えて、実行します。

   ```azurecli
   az sql server ad-only-auth enable --resource-group <myresource> --name <myserver>
   ```

## <a name="enable-in-sql-managed-instance-using-azure-cli"></a>Azure CLI を使用して SQL Managed Instance で有効にする

Azure CLI を使用して Azure SQL Managed Instance で Azure AD 専用認証を有効にするには、次のコマンドを参照してください。 [Azure CLI の最新バージョンをインストールします](/cli/azure/install-azure-cli-windows)。 

1. [SQL セキュリティ管理者](../../role-based-access-control/built-in-roles.md#sql-security-manager)ロールを持つアカウントを使用して、[Azure にサインイン](/cli/azure/authenticate-azure-cli)します。

   ```azurecli
   az login
   ```

1. 次のコマンドの `<myserver>` を SQL Server 名に置き換え、`<myresource>` を SQL Server を保持している Azure リソースに置き換えて、実行します。

   ```azurecli
   az sql mi ad-only-auth enable --resource-group <myresource> --name <myserver>
   ```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

## <a name="enable-in-sql-database-using-powershell"></a>PowerShell を使用して SQL Database で有効にする

PowerShell を使用して Azure SQL Database で Azure AD 専用認証を有効にするには、次のコマンドを参照してください。 これらのコマンドを実行するには、[Az.Sql 2.10.0](https://www.powershellgallery.com/packages/Az.Sql/2.10.0) 以降のモジュールが必要です。 これらのコマンドの詳細については、「[Enable-AzSqlInstanceActiveDirectoryOnlyAuthentication](/powershell/module/az.sql/enable-azsqlinstanceactivedirectoryonlyauthentication)」を参照してください。

API を使用した Azure AD 専用認証の管理の詳細については、「[API を使用した Azure AD 専用認証の管理](authentication-azure-ad-only-authentication.md#managing-azure-ad-only-authentication-using-apis)」を参照してください

> [!NOTE]
> Azure AD 専用認証を有効にする前に、サーバーの Azure AD 管理者を設定する必要があります。 そうしないと、PowerShell コマンドが失敗します。
>
> Azure AD 専用認証を有効にするためにこれらのコマンドを実行するユーザーに必要なアクセス許可とアクションについては、[Azure AD 専用認証](authentication-azure-ad-only-authentication.md#permissions)に関する記事をご覧ください。 ユーザーに十分な権限がない場合、次のエラーが表示されます。
>
> ```output
> Enable-AzSqlServerActiveDirectoryOnlyAuthentication : The client
> 'UserSqlServerContributor@contoso.onmicrosoft.com' with object id
> '<guid>' does not have authorization to perform
> action 'Microsoft.Sql/servers/azureADOnlyAuthentications/write' over scope
> '/subscriptions/<guid>...'
> ```

1. [SQL セキュリティ管理者](../../role-based-access-control/built-in-roles.md#sql-security-manager)ロールを持つアカウントを使用して、[Azure にサインイン](/powershell/azure/authenticate-azureps)します。

   ```powershell
   Connect-AzAccount
   ```

1. 次のコマンドの `<myserver>` を SQL Server 名に置き換え、`<myresource>` を SQL Server を保持している Azure リソースに置き換えて、実行します。

   ```powershell
   Enable-AzSqlServerActiveDirectoryOnlyAuthentication -ServerName <myserver>  -ResourceGroupName <myresource>
   ```

## <a name="enable-in-sql-managed-instance-using-powershell"></a>PowerShell を使用して SQL Managed Instance で有効にする

PowerShell を使用して Azure SQL Managed Instance で Azure AD 専用認証を有効にするには、次のコマンドを参照してください。 これらのコマンドを実行するには、[Az.Sql 2.10.0](https://www.powershellgallery.com/packages/Az.Sql/2.10.0) 以降のモジュールが必要です。 

API を使用した Azure AD 専用認証の管理の詳細については、「[API を使用した Azure AD 専用認証の管理](authentication-azure-ad-only-authentication.md#managing-azure-ad-only-authentication-using-apis)」を参照してください。


1. [SQL セキュリティ管理者](../../role-based-access-control/built-in-roles.md#sql-security-manager)ロールを持つアカウントを使用して、[Azure にサインイン](/powershell/azure/authenticate-azureps)します。

   ```powershell
   Connect-AzAccount
   ```

1. 次のコマンドの `<myinstance>` を SQL Managed Instance 名に置き換え、`<myresource>` を **SQL Managed Instance** を保持している Azure リソースに置き換えて、実行します。

   ```powershell
   Enable-AzSqlInstanceActiveDirectoryOnlyAuthentication -InstanceName <myinstance> -ResourceGroupName <myresource>
   ```

---

## <a name="check-the-azure-ad-only-authentication-status"></a>Azure AD 専用認証の状態を確認する

サーバーまたはインスタンスに対して Azure AD 専用認証が有効になっているかどうかを確認します。

# <a name="portal"></a>[ポータル](#tab/azure-portal)

## <a name="check-status-in-sql-database"></a>SQL Database で状態を確認する

[Azure portal](https://portal.azure.com/) で **SQL Server** リソースにアクセスします。 **[設定]** メニューの **[Azure Active Directory]** を選択します。

## <a name="check-status-in-sql-managed-instance"></a>SQL Managed Instance で状態を確認する

[Azure portal](https://portal.azure.com/) で、**SQL Managed Instance** リソースにアクセスします。 **[設定]** メニューで **[Active Directory 管理者]** を選択します。

# <a name="the-azure-cli"></a>[Azure CLI](#tab/azure-cli)

これらのコマンドは、Azure SQL Database の[論理サーバー](logical-servers.md)または SQL Managed Instance に対して Azure AD 専用認証が有効になっているかどうかを確認するために使用できます。 [SQL Server 共同作成者](../../role-based-access-control/built-in-roles.md#sql-server-contributor)および [SQL Managed Instance 共同作成者](../../role-based-access-control/built-in-roles.md#sql-managed-instance-contributor)ロールのメンバーは、これらのコマンドを使用して Azure AD 専用認証の状態を確認できますが、この機能を有効または無効にすることはできません。

## <a name="check-status-in-sql-database"></a>SQL Database で状態を確認する

1. [SQL セキュリティ管理者](../../role-based-access-control/built-in-roles.md#sql-security-manager)ロールを持つアカウントを使用して、[Azure にサインイン](/cli/azure/authenticate-azure-cli)します。 API を使用した Azure AD 専用認証の管理の詳細については、「[API を使用した Azure AD 専用認証の管理](authentication-azure-ad-only-authentication.md#managing-azure-ad-only-authentication-using-apis)」を参照してください

   ```azurecli
   az login
   ```

1. 次のコマンドの `<myserver>` を SQL Server 名に置き換え、`<myresource>` を SQL Server を保持している Azure リソースに置き換えて、実行します。

   ```azurecli
   az sql server ad-only-auth get --resource-group <myresource> --name <myserver>
   ```

1. 次の出力が表示されます。

   ```json
   {
    "azureAdOnlyAuthentication": true,
    "/subscriptions/<guid>/resourceGroups/mygroup/providers/Microsoft.Sql/servers/myserver/azureADOnlyAuthentications/Default",
    "name": "Default",
    "resourceGroup": "myresource",
    "type": "Microsoft.Sql/servers"
   }
   ```

## <a name="check-status-in-sql-managed-instance"></a>SQL Managed Instance で状態を確認する

1. [SQL セキュリティ管理者](../../role-based-access-control/built-in-roles.md#sql-security-manager)ロールを持つアカウントを使用して、[Azure にサインイン](/cli/azure/authenticate-azure-cli)します。

   ```azurecli
   az login
   ```

1. 次のコマンドの `<myserver>` を SQL Server 名に置き換え、`<myresource>` を SQL Server を保持している Azure リソースに置き換えて、実行します。

   ```azurecli
   az sql mi ad-only-auth get --resource-group <myresource> --name <myserver>
   ```

1. 次の出力が表示されます。

   ```json
   {
    "azureAdOnlyAuthentication": true,
    "id": "/subscriptions/<guid>/resourceGroups/myresource/providers/Microsoft.Sql/managedInstances/myinstance/azureADOnlyAuthentications/Default",
    "name": "Default",
    "resourceGroup": "myresource",
    "type": "Microsoft.Sql/managedInstances"
   }
   ```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

これらのコマンドは、Azure SQL Database の[論理サーバー](logical-servers.md)または SQL Managed Instance に対して Azure AD 専用認証が有効になっているかどうかを確認するために使用できます。 [SQL Server 共同作成者](../../role-based-access-control/built-in-roles.md#sql-server-contributor)および [SQL Managed Instance 共同作成者](../../role-based-access-control/built-in-roles.md#sql-managed-instance-contributor)ロールのメンバーは、これらのコマンドを使用して Azure AD 専用認証の状態を確認できますが、この機能を有効または無効にすることはできません。

この機能が有効になっている場合は、状態として **True** が返され、無効になっている場合は、**False** が返されます。

## <a name="check-status-in-sql-database"></a>SQL Database で状態を確認する

1. [SQL セキュリティ管理者](../../role-based-access-control/built-in-roles.md#sql-security-manager)ロールを持つアカウントを使用して、[Azure にサインイン](/powershell/azure/authenticate-azureps)します。 API を使用した Azure AD 専用認証の管理の詳細については、「[API を使用した Azure AD 専用認証の管理](authentication-azure-ad-only-authentication.md#managing-azure-ad-only-authentication-using-apis)」を参照してください

   ```powershell
   Connect-AzAccount
   ```

1. 次のコマンドの `<myserver>` を SQL Server 名に置き換え、`<myresource>` を SQL Server を保持している Azure リソースに置き換えて、実行します。

   ```powershell
   Get-AzSqlServerActiveDirectoryOnlyAuthentication  -ServerName <myserver> -ResourceGroupName <myresource>
   ```

## <a name="check-status-in-sql-managed-instance"></a>SQL Managed Instance で状態を確認する

1. [SQL セキュリティ管理者](../../role-based-access-control/built-in-roles.md#sql-security-manager)ロールを持つアカウントを使用して、[Azure にサインイン](/powershell/azure/authenticate-azureps)します。

   ```powershell
   Connect-AzAccount
   ```

1. 次のコマンドの `<myinstance>` を SQL Managed Instance 名に置き換え、`<myresource>` を **SQL Managed Instance** を保持している Azure リソースに置き換えて、実行します。

   ```powershell
   Get-AzSqlInstanceActiveDirectoryOnlyAuthentication -InstanceName <myinstance> -ResourceGroupName <myresource>
   ```

---

## <a name="test-sql-authentication-with-connection-failure"></a>接続エラーが発生した SQL 認証をテストする

Azure AD 専用認証を有効にした後、[SQL Server Management Studio (SSMS)](/sql/ssms/download-sql-server-management-studio-ssms) を使用して、[SQL Database または SQL Managed Instance への接続](connect-query-ssms.md)をテストします。 接続には SQL 認証を使用します。

次の出力のようなログイン失敗メッセージが表示されます。

```output
Cannot connect to <myserver>.database.windows.net.
Additional information:
  Login failed for user 'username'. Reason: Azure Active Directory only authentication is enabled.
  Please contact your system administrator. (Microsoft SQL Server, Error: 18456)
```

## <a name="disable-azure-ad-only-authentication"></a>Azure AD 専用認証を無効にする

Azure AD 専用認証機能を無効にすることで、Azure SQL の SQL 認証と Azure AD 認証の両方を許可します。

# <a name="portal"></a>[ポータル](#tab/azure-portal)

## <a name="disable-in-sql-database-using-azure-portal"></a>Azure portal を使用して SQL Database で無効にする

1. [SQL セキュリティ管理者](../../role-based-access-control/built-in-roles.md#sql-security-manager)ロールを持つユーザーを使用して、[Azure portal](https://portal.azure.com/) にアクセスします。
1. SQL Server リソースにアクセスし、 **[設定]** メニューの **[Azure Active Directory]** を選択します。
1. Azure AD 専用認証機能を無効にするには、 **[このサーバーの Azure Active Directory 認証のみをサポートする]** チェックボックスをオフにして、設定を **保存** します。

## <a name="disable-in-sql-managed-instance-using-azure-portal"></a>Azure portal を使用して SQL Managed Instance で無効にする

1. [SQL セキュリティ管理者](../../role-based-access-control/built-in-roles.md#sql-security-manager)ロールを持つユーザーを使用して、[Azure portal](https://portal.azure.com/) にアクセスします。
1. **SQL Managed Instance** リソースにアクセスし、 **[設定]** メニューの **[Active Directory 管理者]** を選択します。
1. Azure AD 専用認証機能を無効にするには、 **[このマネージド インスタンスの Azure Active Directory 認証のみをサポートする]** チェックボックスをオフにして、設定を **保存** します。

# <a name="the-azure-cli"></a>[Azure CLI](#tab/azure-cli)

## <a name="disable-in-sql-database-using-azure-cli"></a>Azure CLI を使用して SQL Database で無効にする

Azure CLI を使用して Azure SQL Database で Azure AD 専用認証を無効にするには、次のコマンドを参照してください。 

1. [SQL セキュリティ管理者](../../role-based-access-control/built-in-roles.md#sql-security-manager)ロールを持つアカウントを使用して、[Azure にサインイン](/cli/azure/authenticate-azure-cli)します。

   ```azurecli
   az login
   ```

1. 次のコマンドの `<myserver>` を SQL Server 名に置き換え、`<myresource>` を SQL Server を保持している Azure リソースに置き換えて、実行します。

   ```azurecli
   az sql server ad-only-auth disable --resource-group <myresource> --name <myserver>
   ```

1. Azure AD 専用認証を無効にした後、状態を確認すると、次の出力が表示されます。

   ```json
   {
    "azureAdOnlyAuthentication": false,
    "/subscriptions/<guid>/resourceGroups/mygroup/providers/Microsoft.Sql/servers/myserver/azureADOnlyAuthentications/Default",
    "name": "Default",
    "resourceGroup": "myresource",
    "type": "Microsoft.Sql/servers"
   }
   ```

## <a name="disable-in-sql-managed-instance-using-azure-cli"></a>Azure CLI を使用して SQL Managed Instance で無効にする

Azure CLI を使用して Azure SQL Managed Instance で Azure AD 専用認証を無効にするには、次のコマンドを参照してください。 

1. [SQL セキュリティ管理者](../../role-based-access-control/built-in-roles.md#sql-security-manager)ロールを持つアカウントを使用して、[Azure にサインイン](/cli/azure/authenticate-azure-cli)します。

   ```azurecli
   az login
   ```

1. 次のコマンドの `<myserver>` を SQL Server 名に置き換え、`<myresource>` を SQL Server を保持している Azure リソースに置き換えて、実行します。

   ```azurecli
   az sql mi ad-only-auth disable --resource-group <myresource> --name <myserver>
   ```

1. Azure AD 専用認証を無効にした後、状態を確認すると、次の出力が表示されます。

   ```json
   {
    "azureAdOnlyAuthentication": false,
    "id": "/subscriptions/<guid>/resourceGroups/myresource/providers/Microsoft.Sql/managedInstances/myinstance/azureADOnlyAuthentications/Default",
    "name": "Default",
    "resourceGroup": "myresource",
    "type": "Microsoft.Sql/managedInstances"
   }
   ```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

## <a name="disable-in-sql-database-using-powershell"></a>PowerShell を使用して SQL Database で無効にする

PowerShell を使用して Azure SQL Database で Azure AD 専用認証を無効にするには、次のコマンドを参照してください。

1. [SQL セキュリティ管理者](../../role-based-access-control/built-in-roles.md#sql-security-manager)ロールを持つアカウントを使用して、[Azure にサインイン](/powershell/azure/authenticate-azureps)します。

   ```powershell
   Connect-AzAccount
   ```

1. 次のコマンドの `<myserver>` を SQL Server 名に置き換え、`<myresource>` を SQL Server を保持している Azure リソースに置き換えて、実行します。

   ```powershell
   Disable-AzSqlServerActiveDirectoryOnlyAuthentication -ServerName <myserver>  -ResourceGroupName <myresource>
   ```

## <a name="disable-in-sql-managed-instance-using-powershell"></a>PowerShell を使用して SQL Managed Instance で無効にする

PowerShell を使用して Azure SQL Managed Instance で Azure AD 専用認証を無効にするには、次のコマンドを参照してください。

1. [SQL セキュリティ管理者](../../role-based-access-control/built-in-roles.md#sql-security-manager)ロールを持つアカウントを使用して、[Azure にサインイン](/powershell/azure/authenticate-azureps)します。

   ```powershell
   Connect-AzAccount
   ```

1. 次のコマンドの `<myinstance>` を SQL Managed Instance 名に置き換え、`<myresource>` を Managed Instance を保持している Azure リソースに置き換えて、実行します。

   ```powershell
   Disable-AzSqlInstanceActiveDirectoryOnlyAuthentication -InstanceName <myinstance> -ResourceGroupName <myresource>
   ```

---

## <a name="test-connecting-to-azure-sql-again"></a>Azure SQL への接続をもう一度テストする

Azure AD 専用認証を無効にした後、SQL 認証ログインを使用して接続をテストします。 これで、サーバーまたはインスタンスに接続できるはずです。

## <a name="next-steps"></a>次のステップ

- [Azure SQL を使用した Azure AD 専用認証](authentication-azure-ad-only-authentication.md)
- [Azure SQL で Azure AD 専用認証を有効にしたサーバーを作成する](authentication-azure-ad-only-authentication-create-server.md)
- [Azure Policy を使用して Azure SQL で Azure Active Directory のみの認証を適用するには](authentication-azure-ad-only-authentication-policy-how-to.md)
