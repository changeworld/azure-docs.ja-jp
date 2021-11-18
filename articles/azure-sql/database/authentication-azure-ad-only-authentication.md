---
title: Azure Active Directory 専用認証
description: この記事では、Azure SQL Database と Azure SQL Managed Instance を使用した Azure Active Directory (Azure AD) 専用認証機能に関する情報を提供します
titleSuffix: Azure SQL Database & Azure SQL Managed Instance
ms.service: sql-db-mi
ms.subservice: security
ms.topic: conceptual
author: GithubMirek
ms.author: mireks
ms.reviewer: vanto
ms.date: 11/03/2021
ms.custom: ignite-fall-2021
ms.openlocfilehash: bb35d3bd246a4915d30c00bb6afd87e43d661c5a
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/17/2021
ms.locfileid: "132719658"
---
# <a name="azure-ad-only-authentication-with-azure-sql"></a>Azure SQL を使用した Azure AD 専用認証

[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

Azure AD 専用認証は、サービスで Azure AD 専用認証をサポートできるようにする [Azure SQL](../azure-sql-iaas-vs-paas-what-is-overview.md) 内の機能であり、[Azure SQL Database](sql-database-paas-overview.md) および [Azure SQL Managed Instance](../managed-instance/sql-managed-instance-paas-overview.md) でサポートされています。 Azure SQL 環境で Azure AD 専用認証を有効にすると、SQL Server 管理者、ログイン、ユーザーからの接続を含め、SQL 認証は無効になります。 [Azure AD 認証](authentication-aad-overview.md)を使用しているユーザーのみが、サーバーまたはデータベースへの接続を許可されます。

Azure AD 専用認証は、Azure portal、Azure CLI、PowerShell、または REST API を使用して有効または無効にすることができます。 また、Azure AD 専用認証は、サーバーの作成時に ARM テンプレートを使用して構成することもできます。

Azure SQL 認証の詳細については、「[認証と承認](logins-create-manage.md#authentication-and-authorization)」を参照してください。

## <a name="feature-description"></a>機能の説明

Azure AD 専用認証を有効にすると、[SQL 認証](logins-create-manage.md#authentication-and-authorization)はサーバーまたはマネージド インスタンス レベルで無効になり、SQL 認証資格情報に基づく認証はすべて禁止されます。 SQL 認証ユーザーは、Azure SQL Database またはマネージド インスタンスの[論理サーバー](logical-servers.md) (すべてのデータベースを含む) に接続できなくなります。 SQL 認証は無効になっていますが、適切なアクセス許可を持つ Azure AD アカウントを使用して、新しい SQL 認証ログインおよびユーザーを作成できます。 新しく作成された SQL 認証アカウントは、サーバーへの接続を許可されません。 Azure AD 専用認証を有効にしても、既存の SQL 認証ログインおよびユーザー アカウントは削除されません。 この機能により、これらのアカウントはサーバーと、このサーバー用に作成されたデータベースに単に接続できなくなります。

Azure Policy を使用して Azure AD 専用認証が有効になっているサーバーを強制的に作成することもできます。 詳細については、[Azure AD 専用認証のための Azure Policy](authentication-azure-ad-only-authentication-policy.md) に関するページを参照してください。

## <a name="permissions"></a>アクセス許可

Azure AD 専用認証を有効または無効にできるのは、Azure サブスクリプションの[所有者](../../role-based-access-control/built-in-roles.md#owner)、[共同作成者](../../role-based-access-control/built-in-roles.md#contributor)、[全体管理者](../../active-directory/roles/permissions-reference.md#global-administrator)など、高い特権を持つ [Azure AD 組み込みのロール](../../role-based-access-control/built-in-roles.md)のメンバーである Azure AD ユーザーです。 また、[SQL セキュリティ管理者](../../role-based-access-control/built-in-roles.md#sql-security-manager)ロールも、Azure AD 専用認証機能を有効または無効にできます。

[SQL Server 共同作成者](../../role-based-access-control/built-in-roles.md#sql-server-contributor)と [SQL Managed Instance 共同作成者](../../role-based-access-control/built-in-roles.md#sql-managed-instance-contributor)ロールには、Azure AD 専用認証機能を有効または無効にするためのアクセス許可がありません。 これは、[職務の分離](security-best-practice.md#implement-separation-of-duties)アプローチ (Azure SQL Server を作成したり Azure AD 管理者を作成したりできるユーザーは、セキュリティ機能を有効または無効にすることができない) と一致します。

### <a name="actions-required"></a>必要な操作

[SQL セキュリティ管理者](../../role-based-access-control/built-in-roles.md#sql-security-manager)ロールには、Azure AD 専用認証機能を管理できるように、次のアクションが追加されています。

- Microsoft.Sql/servers/azureADOnlyAuthentications/*
- Microsoft.Sql/servers/administrators/read - Azure portal の **[Azure Active Directory]** メニューにアクセスするユーザーにのみ必要
- Microsoft.Sql/managedInstances/azureADOnlyAuthentications/*
- Microsoft.Sql/managedInstances/read

上記のアクションは、Azure AD 専用認証を管理するためにカスタム ロールに追加することもできます。 詳細については、「[Azure Active Directory でカスタム ロールを作成して割り当てる](../../active-directory/roles/custom-create.md)」を参照してください。

## <a name="managing-azure-ad-only-authentication-using-apis"></a>API を使用した Azure AD 専用認証の管理

> [!IMPORTANT]
> Azure AD 専用認証を有効にする前に、Azure AD 管理者を設定する必要があります。

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Azure CLI バージョン **2.14.2** 以上が必要です。

`name` は、サーバー名またはインスタンス名のプレフィックス (`myserver` など) に対応し、`resource-group` は、サーバーが属するリソース (`myresource` など) に対応します。

## <a name="azure-sql-database"></a>Azure SQL データベース

詳細については、「[az sql server ad-only-auth](/cli/azure/sql/server/ad-only-auth)」を参照してください。

### <a name="enable-or-disable-in-sql-database"></a>SQL Database で有効または無効にする

**[有効化]**

```azurecli
az sql server ad-only-auth enable --resource-group myresource --name myserver
```

**Disable**

```azurecli
az sql server ad-only-auth disable --resource-group myresource --name myserver
```

### <a name="check-the-status-in-sql-database"></a>SQL Database で状態を確認する

```azurecli
az sql server ad-only-auth get --resource-group myresource --name myserver
```

## <a name="azure-sql-managed-instance"></a>Azure SQL Managed Instance

詳細については、「[az sql mi ad-only-auth](/cli/azure/sql/mi/ad-only-auth)」を参照してください。

**[有効化]**

```azurecli
az sql mi ad-only-auth enable --resource-group myresource --name myserver
```

**Disable**

```azurecli
az sql mi ad-only-auth disable --resource-group myresource --name myserver
```

### <a name="check-the-status-in-sql-managed-instance"></a>SQL Managed Instance で状態を確認する

```azurecli
az sql mi ad-only-auth get --resource-group myresource --name myserver
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

[Az.Sql 2.10.0](https://www.powershellgallery.com/packages/Az.Sql/2.10.0) モジュール以上が必要です。

`ServerName` または `InstanceName` は、サーバー名のプレフィックス (`myserver` や `myinstance` など) に対応し、`ResourceGroupName` は、サーバーが属するリソース (`myresource` など) に対応します。

## <a name="azure-sql-database"></a>Azure SQL データベース

### <a name="enable-or-disable-in-sql-database"></a>SQL Database で有効または無効にする

**有効化**

詳細については、「[Enable-AzSqlServerActiveDirectoryOnlyAuthentication](/powershell/module/az.sql/enable-azsqlserveractivedirectoryonlyauthentication)」を参照してください。 `get-help Enable-AzSqlServerActiveDirectoryOnlyAuthentication -full` を実行することもできます。

```powershell
Enable-AzSqlServerActiveDirectoryOnlyAuthentication -ServerName myserver -ResourceGroupName myresource
```

次のコマンドを使用することもできます。

```powershell
Get-AzSqlServer -ServerName myserver | Enable-AzSqlServerActiveDirectoryOnlyAuthentication
```

**無効化**

詳細については、「[Disable-AzSqlServerActiveDirectoryOnlyAuthentication](/powershell/module/az.sql/disable-azsqlserveractivedirectoryonlyauthentication)」を参照してください。

```powershell
Disable-AzSqlServerActiveDirectoryOnlyAuthentication -ServerName myserver -ResourceGroupName myresource
```

### <a name="check-the-status-in-sql-database"></a>SQL Database で状態を確認する

```powershell
Get-AzSqlServerActiveDirectoryOnlyAuthentication  -ServerName myserver -ResourceGroupName myresource
```

次のコマンドを使用することもできます。

```powershell
Get-AzSqlServer -ServerName myserver | Get-AzSqlServerActiveDirectoryOnlyAuthentication
```

## <a name="azure-sql-managed-instance"></a>Azure SQL Managed Instance

### <a name="enable-or-disable-in-sql-managed-instance"></a>SQL Managed Instance で有効または無効にする

**有効化**

詳細については、「[Enable-AzSqlInstanceActiveDirectoryOnlyAuthentication](/powershell/module/az.sql/enable-azsqlinstanceactivedirectoryonlyauthentication)」を参照してください。

```powershell
Enable-AzSqlInstanceActiveDirectoryOnlyAuthentication -InstanceName myinstance -ResourceGroupName myresource
```

次のコマンドを使用することもできます。

```powershell
Get-AzSqlInstance -InstanceName myinstance | Enable-AzSqlInstanceActiveDirectoryOnlyAuthentication
```

これらの PowerShell コマンドの詳細については、`get-help  Enable-AzSqlInstanceActiveDirectoryOnlyAuthentication -full` を実行してください。

**無効化**

詳細については、「[Disable-AzSqlInstanceActiveDirectoryOnlyAuthentication](/powershell/module/az.sql/disable-azsqlinstanceactivedirectoryonlyauthentication)」を参照してください。

```powershell
Disable-AzSqlInstanceActiveDirectoryOnlyAuthentication -InstanceName myinstance -ResourceGroupName myresource
```

### <a name="check-the-status-in-sql-managed-instance"></a>SQL Managed Instance で状態を確認する

```powershell
Get-AzSqlInstanceActiveDirectoryOnlyAuthentication -InstanceName myinstance -ResourceGroupName myresource
```

次のコマンドを使用することもできます。

```powershell
Get-AzSqlInstance -InstanceName myinstance | Get-AzSqlInstanceActiveDirectoryOnlyAuthentication
```

# <a name="rest-api"></a>[REST API](#tab/rest-api)

次のパラメーターを定義する必要があります。

- `<subscriptionId>` は、Azure portal の **[サブスクリプション]** に移動すると確認できます。
- `<myserver>` は、サーバー名またはインスタンス名のプレフィックス (`myserver` など) に対応します。
- `<myresource>` は、サーバーが属するリソース (`myresource` など) に対応します。

最新の MSAL を使用するには、 https://www.powershellgallery.com/packages/MSAL.PS からダウンロードします。

```rest
$subscriptionId = '<subscriptionId>'
$serverName = "<myserver>"
$resourceGroupName = "<myresource>"
```

## <a name="azure-sql-database"></a>Azure SQL データベース

詳細については、REST API ドキュメント「[Server の Azure AD 専用認証](/rest/api/sql/2021-02-01-preview/serverazureadonlyauthentications)」を参照してください。

### <a name="enable-or-disable-in-sql-database"></a>SQL Database で有効または無効にする

**[有効化]**

```rest
$body = @{ properties = @{ azureADOnlyAuthentication = 1 } } | ConvertTo-Json
Invoke-RestMethod -Uri "https://management.azure.com/subscriptions/$subscriptionId/resourceGroups/$resourceGroupName/providers/Microsoft.Sql/servers/$serverName/azureADOnlyAuthentications/default?api-version=2020-02-02-preview" -Method PUT -Headers $authHeader -Body $body -ContentType "application/json"
```

**Disable**

```rest
$body = @{ properties = @{ azureADOnlyAuthentication = 0 } } | ConvertTo-Json
Invoke-RestMethod -Uri "https://management.azure.com/subscriptions/$subscriptionId/resourceGroups/$resourceGroupName/providers/Microsoft.Sql/servers/$serverName/azureADOnlyAuthentications/default?api-version=2020-02-02-preview" -Method PUT -Headers $authHeader -Body $body -ContentType "application/json"
```

### <a name="check-the-status-in-sql-database"></a>SQL Database で状態を確認する

```rest
Invoke-RestMethod -Uri "https://management.azure.com/subscriptions/$subscriptionId/resourceGroups/$resourceGroupName/providers/Microsoft.Sql/servers/$serverName/azureADOnlyAuthentications/default?api-version=2020-02-02-preview" -Method GET -Headers $authHeader  | Format-List
```

## <a name="azure-sql-managed-instance"></a>Azure SQL Managed Instance

詳細については、REST API のドキュメント「[Managed Instance の Azure AD 専用認証](/rest/api/sql/2021-02-01-preview/managedinstanceazureadonlyauthentications)」を参照してください。

### <a name="enable-or-disable-in-sql-managed-instance"></a>SQL Managed Instance で有効または無効にする

**[有効化]**

```rest
$body = @{   properties = @{  azureADOnlyAuthentication = 1 }  } | ConvertTo-Json 
Invoke-RestMethod -Uri "https://management.azure.com/subscriptions/$subscriptionId/resourceGroups/$resourceGroupName/providers/Microsoft.Sql/managedInstances/$serverName/azureADOnlyAuthentications/default?api-version=2020-02-02-preview" -Method PUT -Headers $authHeader -Body $body -ContentType "application/json"
```

**Disable**

```rest
$body = @{   properties = @{  azureADOnlyAuthentication = 0 }  } | ConvertTo-Json 
Invoke-RestMethod -Uri "https://management.azure.com/subscriptions/$subscriptionId/resourceGroups/$resourceGroupName/providers/Microsoft.Sql/managedInstances/$serverName/azureADOnlyAuthentications/default?api-version=2020-02-02-preview" -Method PUT -Headers $authHeader -Body $body -ContentType "application/json"
```

### <a name="check-the-status-in-sql-managed-instance"></a>SQL Managed Instance で状態を確認する

```rest
Invoke-RestMethod -Uri "https://management.azure.com/subscriptions/$subscriptionId/resourceGroups/$resourceGroupName/providers/Microsoft.Sql/managedInstances/$serverName/azureADOnlyAuthentications/default?api-version=2020-02-02-preview" -Method GET -Headers $authHeader  | Format-List
```

# <a name="arm-template"></a>[ARM テンプレート](#tab/arm)

- デプロイの Azure AD 管理者を入力します。 ユーザー オブジェクト ID は、[Azure portal](https://portal.azure.com) に移動し、 **[Azure Active Directory]** リソースに移動すると確認できます。 **[管理]** にある **[ユーザー]** を選択します。 Azure SQL Server の Azure AD 管理者として設定するユーザーを検索します。 ユーザーを選択すると、その **[プロファイル]** ページに **オブジェクト ID** が表示されます。
- テナント ID は、 **[Azure Active Directory]** リソースの **[概要]** ページで確認できます。

## <a name="azure-sql-database"></a>Azure SQL データベース

### <a name="enable"></a>有効化

次の ARM テンプレートでは、Azure SQL Database で Azure AD 専用認証を有効にします。 Azure AD 専用認証を無効にするには、`azureADOnlyAuthentication` プロパティを `false` に設定します。

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.1",
    "parameters": {
        "sqlServer_name": {
            "type": "String"
        },
        "aad_admin_name": {
            "type": "String"
        },
        "aad_admin_objectid": {
            "type": "String"
        },
        "aad_admin_tenantid": {
            "type": "String"
        }
    },
    "resources": [
        {
            "type": "Microsoft.Sql/servers/administrators",
            "apiVersion": "2020-02-02-preview",
            "name": "[concat(parameters('sqlServer_name'), '/ActiveDirectory')]",
            "properties": {
                "administratorType": "ActiveDirectory",
                "login": "[parameters('aad_admin_name')]",
                "sid": "[parameters('aad_admin_objectid')]",
                "tenantId": "[parameters('aad_admin_tenantId')]"
            }
        },        
        {
            "type": "Microsoft.Sql/servers/azureADOnlyAuthentications",
            "apiVersion": "2020-02-02-preview",
            "name": "[concat(parameters('sqlServer_name'), '/Default')]",
            "dependsOn": [
                "[resourceId('Microsoft.Sql/servers/administrators', parameters('sqlServer_name'), 'ActiveDirectory')]"
            ],
            "properties": {
                "azureADOnlyAuthentication": true
            }
        }
    ]
}
```

詳細については、「[Microsoft.Sql servers/azureADOnlyAuthentications](/azure/templates/microsoft.sql/servers/azureadonlyauthentications)」を参照してください。

## <a name="azure-sql-managed-instance"></a>Azure SQL Managed Instance

### <a name="enable"></a>有効化

次の ARM テンプレートでは、Azure SQL Managed Instance で Azure AD 専用認証を有効にします。 Azure AD 専用認証を無効にするには、`azureADOnlyAuthentication` プロパティを `false` に設定します。

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.1",
    "parameters": {
        "instance": {
            "type": "String"
        },
        "aad_admin_name": {
            "type": "String"
        },
        "aad_admin_objectid": {
            "type": "String"
        },
        "aad_admin_tenantid": {
            "type": "String"
        }
    },
    "resources": [
        {
            "type": "Microsoft.Sql/managedInstances/administrators",
            "apiVersion": "2020-02-02-preview",
            "name": "[concat(parameters('instance'), '/ActiveDirectory')]",
            "properties": {
                "administratorType": "ActiveDirectory",
                "login": "[parameters('aad_admin_name')]",
                "sid": "[parameters('aad_admin_objectid')]",
                "tenantId": "[parameters('aad_admin_tenantId')]"
            }
        },
        {
            "type": "Microsoft.Sql/managedInstances/azureADOnlyAuthentications",
            "apiVersion": "2020-02-02-preview",
            "name": "[concat(parameters('instance'), '/Default')]",
            "dependsOn": [
                "[resourceId('Microsoft.Sql/managedInstances/administrators', parameters('instance'), 'ActiveDirectory')]"
            ],
            "properties": {
                "azureADOnlyAuthentication": true
            }
        }
    ]
}

```

詳細については、「[Microsoft.Sql managedInstances/azureADOnlyAuthentications](/azure/templates/microsoft.sql/managedinstances/azureadonlyauthentications)」を参照してください。

---

### <a name="checking-azure-ad-only-authentication-using-t-sql"></a>T-SQL を使用した Azure AD 専用認証の確認

サーバーまたはマネージド インスタンスで Azure AD 専用認証が有効になっているかどうかを確認するために、[SEVERPROPERTY](/sql/t-sql/functions/serverproperty-transact-sql) `IsExternalAuthenticationOnly` が追加されています。 `1` は、この機能が有効になっていることを示し、`0` は、この機能が無効になっていることを示します。

```sql
SELECT SERVERPROPERTY('IsExternalAuthenticationOnly') 
```

## <a name="remarks"></a>解説

- [SQL Server 共同作成者](../../role-based-access-control/built-in-roles.md#sql-server-contributor)は、Azure AD 管理者を設定または削除できますが、 **[Azure Active Directory authentication only]\(Azure Active Directory 専用認証\)** を設定することはできません。 [SQL セキュリティ管理者](../../role-based-access-control/built-in-roles.md#sql-security-manager)は、Azure AD 管理者を設定または削除することはできませんが、 **[Azure Active Directory authentication only]\(Azure Active Directory 専用認証\)** を設定することはできます。 両方のアクセス許可を含む上位の Azure RBAC ロールまたはカスタム ロールを持つアカウントだけが、Azure AD 管理者を設定または削除でき、かつ **[Azure Active Directory authentication only]\(Azure Active Directory 専用認証\)** を設定できます。 そのようなロールの 1 つは[共同作成者](../../role-based-access-control/built-in-roles.md#contributor)ロールです。
- Azure portal で **[Azure Active Directory authentication only]\(Azure Active Directory 専用認証\)** を有効または無効にすると、 **[SQL Server]** メニューに **[アクティビティ ログ]** エントリが表示されます。
    :::image type="content" source="media/authentication-azure-ad-only-authentication/azure-ad-only-authentication-portal-sql-server-activity-log.png" alt-text="Azure portal の [アクティビティ ログ] エントリ":::
- **[Azure Active Directory authentication only]\(Azure Active Directory 専用認証\)** 設定は、**Azure Active Directory 管理者** が指定されている場合に、適切なアクセス許可を持つユーザーだけが有効または無効にできます。 Azure AD 管理者が設定されていない場合、 **[Azure Active Directory authentication only]\(Azure Active Directory 専用認証\)** 設定は非アクティブのままになり、有効または無効にすることはできません。 Azure AD 管理者が設定されていない場合、API を使用して Azure AD 専用認証を有効にする操作も失敗します。
- Azure AD 専用認証が有効になっている場合に Azure AD 管理者を変更する操作は、適切なアクセス許可を持つユーザーに対してサポートされています。
- Azure AD 管理者を変更し、Azure AD 専用認証を有効または無効にする操作は、適切なアクセス許可を持つユーザーに対して Azure portal で許可されます。 どちらの操作も、Azure portal で **[保存]** を 1 回使用して実行できます。 Azure AD 専用認証を有効にするには、Azure AD 管理者を設定する必要があります。
- Azure AD 専用認証機能が有効になっている場合に Azure AD 管理者を削除する操作は、サポートされていません。 Azure AD 専用認証機能が有効になっている場合、API を使用して Azure AD 管理者を削除する操作は失敗します。
    - **[Azure Active Directory authentication only]\(Azure Active Directory 専用認証\)** 設定が有効になっている場合、Azure portal の **[管理者の削除]** ボタンは非アクティブになっています。
- Azure AD 管理者を削除し、 **[Azure Active Directory authentication only]\(Azure Active Directory 専用認証)\** 設定を無効にする操作は許可されますが、操作を完了するには適切なユーザー アクセス許可が必要です。 どちらの操作も、Azure portal で **[保存]** を 1 回使用して実行できます。
- 適切なアクセス許可を持つ Azure AD ユーザーは、既存の SQL ユーザーの権限を借用できます。
    - Azure AD 専用認証機能が有効になっている場合でも、権限借用は SQL 認証ユーザー間で引き続き機能します。

### <a name="limitations-for-azure-ad-only-authentication-in-sql-database"></a>SQL Database での Azure AD 専用認証に関する制限事項

SQL Database に対して Azure AD 専用認証が有効になっている場合、次の機能はサポートされていません。

- [Azure SQL Database サーバーのロール](security-server-roles.md)
- [エラスティック ジョブ](job-automation-overview.md)
- [SQL データ同期](sql-data-sync-data-sql-server-sql-database.md)
- [変更データ キャプチャ (CDC)](/sql/relational-databases/track-changes/about-change-data-capture-sql-server) - Azure AD ユーザーとして Azure SQL Database でデータベースを作成し、その上で変更データ キャプチャを有効にした場合、SQL ユーザーは CDC 成果物を無効にしたり、変更したりすることはできません。 ただし、別の Azure AD ユーザーは、同じデータベースで CDC を有効または無効にできます。 同様に、SQL ユーザーとして Azure SQL Database を作成する場合、CDC を Azure AD ユーザーとして有効にしたり、無効にしたりできません。
- [トランザクション レプリケーション](../managed-instance/replication-transactional-overview.md) - レプリケーションの参加者間の接続には SQL 認証が必要なため、Azure AD 専用認証を有効にすると、以下のシナリオの SQL Database では、トランザクション レプリケーションはサポートされません。Azure SQL Managed Instance、オンプレミスの SQL Server、または Azure VM SQL Server インスタンスに加えられた変更を Azure SQL Database 内のデータベースにプッシュするためにトランザクションレプリケーションが使用される場合。
- [SQL Insights](../../azure-monitor/insights/sql-insights-overview.md)
- Azure AD グループ メンバー アカウントの EXEC AS ステートメント

### <a name="limitations-for-azure-ad-only-authentication-in-managed-instance"></a>Managed Instance での Azure AD 専用認証に関する制限事項

Managed Instance に対して Azure AD 専用認証が有効になっている場合、次の機能はサポートされていません。

- [トランザクション レプリケーション](../managed-instance/replication-transactional-overview.md) 
- [Managed Instance の SQL Agent ジョブ](../managed-instance/job-automation-managed-instance.md)は Azure AD 専用認証をサポートしています。 ただし、マネージド インスタンスにアクセスできる Azure AD グループのメンバーである Azure AD ユーザーは、SQL Agent ジョブを所有できません
- [SQL Insights](../../azure-monitor/insights/sql-insights-overview.md)
- Azure AD グループ メンバー アカウントの EXEC AS ステートメント

詳しくは、「[SQL Server と Azure SQL Managed Instance での T-SQL の相違点](../managed-instance/transact-sql-tsql-differences-sql-server.md#logins-and-users)」を参照してください。

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [チュートリアル: Azure SQL を使用して Azure Active Directory 専用認証を有効にする](authentication-azure-ad-only-authentication-tutorial.md)

> [!div class="nextstepaction"]
> [Azure SQL で Azure AD 専用認証を有効にしたサーバーを作成する](authentication-azure-ad-only-authentication-create-server.md)