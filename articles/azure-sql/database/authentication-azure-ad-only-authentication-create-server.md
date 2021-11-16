---
title: Azure Active Directory 専用認証を有効にしたサーバーを作成する
description: この記事では、Azure Active Directory (Azure AD) 専用認証を有効にした Azure SQL 論理サーバーまたはマネージド インスタンスを作成する手順について説明します。これにより、SQL 認証を使用した接続が無効になります。
titleSuffix: Azure SQL Database & Azure SQL Managed Instance
ms.service: sql-db-mi
ms.subservice: security
ms.topic: how-to
author: GithubMirek
ms.author: mireks
ms.reviewer: vanto
ms.date: 11/02/2021
ms.openlocfilehash: 845ecacf97887ef3488d1fd80b40f9424234e257
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/11/2021
ms.locfileid: "132290495"
---
# <a name="create-server-with-azure-ad-only-authentication-enabled-in-azure-sql"></a>Azure SQL で Azure AD 専用認証を有効にしたサーバーを作成する

[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]


この攻略ガイドでは、プロビジョニング中に [Azure AD 専用認証](authentication-azure-ad-only-authentication.md)を有効にした Azure SQL Database 用[論理サーバー](logical-servers.md)または [Azure SQL Managed Instance](../managed-instance/sql-managed-instance-paas-overview.md) を作成する手順の概要を示します。 Azure AD 専用認証機能を使用すると、ユーザーは SQL 認証を使用してサーバーまたはマネージド インスタンスに接続できなくなり、Azure AD 認証を使用した接続のみが許可されます。

## <a name="prerequisites"></a>前提条件

- Azure CLI を使用する場合は、バージョン 2.26.1 以降が必要です。 インストールと最新バージョンの詳細については、「[Azure CLI のインストール](/cli/azure/install-azure-cli)」を参照してください。
- PowerShell を使用する場合は、[Az 6.1.0](https://www.powershellgallery.com/packages/Az/6.1.0) モジュール以降が必要です。
- Azure CLI、PowerShell、または Rest API を使用してマネージド インスタンスをプロビジョニングしている場合は、開始する前に仮想ネットワークとサブネットを作成する必要があります。 詳細については、「[Azure SQL Managed Instance 用の仮想ネットワークを作成する](../managed-instance/virtual-network-subnet-create-arm-template.md)」を参照してください。

## <a name="permissions"></a>アクセス許可

論理サーバーまたはマネージド インスタンスをプロビジョニングするには、これらのリソースを作成するための適切なアクセス許可が必要です。 サブスクリプションの[所有者](../../role-based-access-control/built-in-roles.md#owner)、[共同作成者](../../role-based-access-control/built-in-roles.md#contributor)、[サービス管理者](../../role-based-access-control/rbac-and-directory-admin-roles.md#classic-subscription-administrator-roles)、[共同管理者](../../role-based-access-control/rbac-and-directory-admin-roles.md#classic-subscription-administrator-roles)といった、より高いアクセス許可を持つ Azure ユーザーは、SQL サーバーまたはマネージド インスタンスを作成する特権を持ちます。 最小特権の Azure RBAC ロールでこれらのリソースを作成するには、SQL Database の場合は [SQL Server 共同作成者](../../role-based-access-control/built-in-roles.md#sql-server-contributor)ロールを使用し、Managed Instance の場合は [SQL Managed Instance 共同作成者](../../role-based-access-control/built-in-roles.md#sql-managed-instance-contributor)ロールを使用します。

[SQL セキュリティ管理者](../../role-based-access-control/built-in-roles.md#sql-security-manager) Azure RBAC ロールには、Azure AD 専用認証を有効にしたサーバーまたはインスタンスを作成するための十分なアクセス許可がありません。 サーバーまたはインスタンスの作成後に Azure AD 専用認証機能を管理するには、[SQL セキュリティ管理者](../../role-based-access-control/built-in-roles.md#sql-security-manager)ロールが必要です。

## <a name="provision-with-azure-ad-only-authentication-enabled"></a>Azure AD 専用認証を有効にしたプロビジョニング

次のセクションでは、サーバーまたはインスタンスの Azure AD 管理者セットを使用して論理サーバーまたはマネージド インスタンスを作成する方法およびサーバー作成時に Azure AD 専用認証を有効にする方法に関する例とスクリプトを紹介します。 機能の詳細については、「[Azure AD 専用認証](authentication-azure-ad-only-authentication.md)」を参照してください。

この例では、システムに割り当てられたサーバー管理者とパスワードを使用して、サーバーまたはマネージド インスタンスの作成時に Azure AD 専用認証を有効にします。 これにより Azure AD 専用認証が有効になっている場合にサーバー管理者がアクセスできなくなり、Azure AD 管理者のみがリソースにアクセスできるようになります。 サーバー作成時に独自のサーバー管理者とパスワードを含めるためにパラメーターを API に追加することは省略可能です。 ただし、Azure AD 専用認証を無効にするまで、パスワードをリセットすることはできません。

サーバーまたはマネージド インスタンスの作成後に既存のプロパティを変更するには、他の既存の API を使用する必要があります。 詳細については、「[API を使用した Azure AD 専用認証の管理](authentication-azure-ad-only-authentication.md#managing-azure-ad-only-authentication-using-apis)」および「[Azure SQL での Azure AD 認証を構成して管理する](authentication-aad-configure.md)」を参照してください。

> [!NOTE]
> Azure AD 専用認証が false に設定されている場合 (既定)、サーバーまたはマネージド インスタンスの作成時にすべての API にサーバー管理者とパスワードを含める必要があります。

## <a name="azure-sql-database"></a>Azure SQL Database

# <a name="portal"></a>[ポータル](#tab/azure-portal)

1. Azure portal の [[SQL デプロイ オプションの選択]](https://portal.azure.com/#create/Microsoft.AzureSQL) ページを参照します。

1. まだ Azure portal にサインインしていない場合は、求められたらサインインします。

1. **[SQL データベース]** で、 **[リソースの種類]** を **[単一データベース]** に設定し、 **[作成]** を選択します。

1. **[SQL データベースの作成]** フォームの **[基本]** タブにある **[プロジェクトの詳細]** で、目的の Azure **[サブスクリプション]** を選択します。

1. **[リソース グループ]** の **[新規作成]** を選択し、リソース グループの名前を入力し、 **[OK]** を選択します。

1. **[データベース名]** に、データベースの名前を入力します。

1. **[サーバー]** で、 **[新規作成]** を選択し、新しいサーバー フォームに次の値を入力します。

   - **[サーバー名]** : 一意のサーバー名を入力します。 サーバー名は、サブスクリプション内で一意ではなく、Azure のすべてのサーバーに対してグローバルに一意である必要があります。 値を入力すると Azure portal で使用可能かどうかが示されます。
   - **[場所]** : ドロップダウン リストから場所を選択します
   - **[認証方法]** : **[Azure Active Directory (Azure AD) 認証のみを使用]** を選択します。
   - **[管理者の設定]** を選択します。論理サーバー Azure AD 管理者として Azure AD プリンシパルを選択するメニューが表示されます。 完了したら、 **[選択]** ボタンを使用して管理者を設定します。

   :::image type="content" source="media/authentication-azure-ad-only-authentication/azure-ad-portal-create-server.png" alt-text="Azure AD 専用認証が有効なサーバーを作成するスクリーンショット":::
    
1. **ページの下部にある [Next: Networking]\(次へ: ネットワーク\)** を選択します。

1. **[ネットワーク]** タブの **[接続方法]** で、 **[パブリック エンドポイント]** を選択します。

1. **[ファイアウォール規則]** で、 **[現在のクライアント IP アドレスを追加する]** を **[はい]** に設定します。 **[Azure サービスおよびリソースにこのサーバー グループへのアクセスを許可する]** を **[いいえ]** に設定したままにします。 

1. **[接続ポリシー]** と **[最小 TLS バージョン]** は規定値のままにします。

1. ページの下部で **[次へ: セキュリティ]** を選択します。 ご自分の環境に、 **[Microsoft Defender for SQL]** 、 **[Ledger]** 、 **[ID]** 、 **[Transparent Data Encryption]** の設定を構成します。 これらの設定は省略することもできます。

   > [!NOTE]
   > ユーザー割り当てマネージド ID (UMI) の使用は、Azure AD 専用認証ではサポートされていません。 **[ID]** セクションでサーバー ID を UMI として設定しないでください。

1. ページ下部にある **[確認と作成]** を選択します。

1. **[確認と作成]** ページで、確認後、 **[作成]** を選択します。

# <a name="the-azure-cli"></a>[Azure CLI](#tab/azure-cli)

Azure CLI コマンド `az sql server create` は、新しい論理サーバーをプロビジョニングするために使用されます。 次のコマンドは、Azure AD 専用認証が有効になっている新しいサーバーをプロビジョニングします。

サーバー SQL 管理者ログインが自動的に作成され、パスワードはランダムなパスワードに設定されます。 このサーバーを作成すると SQL 認証接続が無効になるため、SQL 管理者ログインは使用されません。

サーバー Azure AD 管理者は、`<AzureADAccount>` に設定したアカウントになり、サーバーの管理に使用できます。

この例では次の値を置き換えます。

- `<AzureADAccount>`: Azure AD のユーザーまたはグループを指定できます。 たとえば、`DummyLogin` のように指定します。
- `<AzureADAccountSID>`: ユーザーの Azure AD オブジェクト ID
- `<ResourceGroupName>`: 論理サーバーのリソース グループの名前
- `<ServerName>`: 一意の論理サーバー名を使用します

```azurecli
az sql server create --enable-ad-only-auth --external-admin-principal-type User --external-admin-name <AzureADAccount> --external-admin-sid <AzureADAccountSID> -g <ResourceGroupName> -n <ServerName>
```

詳細については、「[az sql server create](/cli/azure/sql/server#az_sql_server_create)」を参照してください。

作成後にサーバーの状態を確認するには、次のコマンドを参照してください。

```azurecli
az sql server show --name <ServerName> --resource-group <ResourceGroupName> --expand-ad-admin
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

PowerShell コマンド `New-AzSqlServer` は、新しい Azure SQL 論理サーバーをプロビジョニングするために使用されます。 次のコマンドは、Azure AD 専用認証が有効になっている新しいサーバーをプロビジョニングします。 

サーバー SQL 管理者ログインが自動的に作成され、パスワードはランダムなパスワードに設定されます。 このサーバーを作成すると SQL 認証接続が無効になるため、SQL 管理者ログインは使用されません。

サーバー Azure AD 管理者は、`<AzureADAccount>` に設定したアカウントになり、サーバーの管理に使用できます。

この例では次の値を置き換えます。

- `<ResourceGroupName>`: 論理サーバーのリソース グループの名前
- `<Location>`: サーバーの場所 (`West US`、`Central US` など)
- `<ServerName>`: 一意の論理サーバー名を使用します
- `<AzureADAccount>`: Azure AD のユーザーまたはグループを指定できます。 たとえば、`DummyLogin` のように指定します。

```powershell
New-AzSqlServer -ResourceGroupName "<ResourceGroupName>" -Location "<Location>" -ServerName "<ServerName>" -ServerVersion "12.0" -ExternalAdminName "<AzureADAccount>" -EnableActiveDirectoryOnlyAuthentication
```

詳細については、「[New-AzSqlServer](/powershell/module/az.sql/new-azsqlserver)」を参照してください。

# <a name="rest-api"></a>[Rest API](#tab/rest-api)

[Servers - Create Or Update](/rest/api/sql/2020-11-01-preview/servers/create-or-update) Rest API を使用して、プロビジョニング中に Azure AD 専用認証が有効になっている論理サーバーを作成できます。 

次のスクリプトでは、論理サーバーをプロビジョニングし、Azure AD 管理者を `<AzureADAccount>` として設定して、Azure AD 専用認証を有効にします。 サーバー SQL 管理者ログインも自動的に作成され、パスワードはランダムなパスワードに設定されます。 このプロビジョニングでは SQL 認証接続が無効になるため、SQL 管理者ログインは使用されません。

Azure AD 管理者 `<AzureADAccount>` は、プロビジョニングが完了したときにサーバーを管理するために使用できます。

この例では次の値を置き換えます。

- `<tenantId>`: [Azure portal](https://portal.azure.com) から **Azure Active Directory** リソースにアクセスして確認できます。 **[概要]** ペインに **[テナント ID]** が表示されます
- `<subscriptionId>`: サブスクリプション ID は Azure portal で確認できます
- `<ServerName>`: 一意の論理サーバー名を使用します
- `<ResourceGroupName>`: 論理サーバーのリソース グループの名前
- `<AzureADAccount>`: Azure AD のユーザーまたはグループを指定できます。 たとえば、`DummyLogin` のように指定します。
- `<Location>`: サーバーの場所 (`westus2`、`centralus` など)
- `<objectId>`: [Azure portal](https://portal.azure.com) から **Azure Active Directory** リソースにアクセスして確認できます。 **[ユーザー]** ペインで、Azure AD ユーザーを検索し、**オブジェクト ID** を特定します

```rest
Import-Module Azure
Import-Module MSAL.PS

$tenantId = '<tenantId>'
$clientId = '1950a258-227b-4e31-a9cf-717495945fc2' # Static Microsoft client ID used for getting a token
$subscriptionId = '<subscriptionId>'
$uri = "urn:ietf:wg:oauth:2.0:oob" 
$authUrl = "https://login.windows.net/$tenantId"
$serverName = "<ServerName>"
$resourceGroupName = "<ResourceGroupName>"

Login-AzAccount -tenantId $tenantId

# login as a user with SQL Server Contributor role or higher 

# Get a token

$result = Get-MsalToken -RedirectUri $uri -ClientId $clientId -TenantId $tenantId -Scopes "https://management.core.windows.net/.default"

#Authetication header
$authHeader = @{
'Content-Type'='application\json; '
'Authorization'=$result.CreateAuthorizationHeader()
}

# Enable Azure AD-only auth 
# No server admin is specified, and only Azure AD admin and Azure AD-only authentication is set to true
# Server admin (login and password) is generated by the system

# Authentication body
# The sid is the Azure AD Object ID for the user 

$body = '{ 
"location": "<Location>",
"properties": { "administrators":{ "login":"<AzureADAccount>", "sid":"<objectId>", "tenantId":"<tenantId>", "principalType":"User", "azureADOnlyAuthentication":true }
  }
}'

# Provision the server

Invoke-RestMethod -Uri https://management.azure.com/subscriptions/$subscriptionId/resourceGroups/$resourceGroupName/providers/Microsoft.Sql/servers/$serverName/?api-version=2020-11-01-preview -Method PUT -Headers $authHeader -Body $body -ContentType "application/json"
```

サーバーの状態を確認するには、次のスクリプトを使用します。

```rest
$uri = 'https://management.azure.com/subscriptions/'+$subscriptionId+'/resourceGroups/'+$resourceGroupName+'/providers/Microsoft.Sql/servers/'+$serverName+'?api-version=2020-11-01-preview&$expand=administrators/activedirectory'

$responce=Invoke-WebRequest -Uri $uri -Method PUT -Headers $authHeader -Body $body -ContentType "application/json"

$responce.statuscode

$responce.content
```

# <a name="arm-template"></a>[ARM テンプレート](#tab/arm-template)

ARM テンプレートの詳細については、「[Azure SQL Database および SQL Managed Instance 用 Azure Resource Manager テンプレート](arm-templates-content-guide.md)」を参照してください。

ARM テンプレートを使用して、サーバーの Azure AD 管理者が設定され、Azure AD 専用認証が有効になった論理サーバーをプロビジョニングするには、「[Azure AD 専用認証を使用した Azure SQL 論理サーバー](https://github.com/Azure/azure-quickstart-templates/tree/master/quickstarts/microsoft.sql/sql-logical-server-aad-only-auth)」クイックスタート テンプレートを参照してください。

次のテンプレートを使用することもできます。 [Azure portal のカスタム デプロイ](https://portal.azure.com/#create/Microsoft.Template)を使用し、**エディターで独自のテンプレートを作成** します。 次に、例に貼り付けたら、構成を **保存** します。

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.1",
    "parameters": {
        "server": {
            "type": "string",
            "defaultValue": "[uniqueString('sql', resourceGroup().id)]",
            "metadata": {
                "description": "The name of the logical server."
            }
        },
        "location": {
            "type": "string",
            "defaultValue": "[resourceGroup().location]",
            "metadata": {
                "description": "Location for all resources."
            }
        },
        "aad_admin_name": {
            "type": "String",
            "metadata": {
                "description": "The name of the Azure AD admin for the SQL server."
            }
        },
        "aad_admin_objectid": {
            "type": "String",
            "metadata": {
                "description": "The Object ID of the Azure AD admin."
            }
        },
        "aad_admin_tenantid": {
            "type": "String",
            "defaultValue": "[subscription().tenantId]",
            "metadata": {
                "description": "The Tenant ID of the Azure Active Directory"
            }
        },
        "aad_admin_type": {
            "defaultValue": "User",
            "allowedValues": [
                "User",
                "Group",
                "Application"
            ],
            "type": "String"
        },
        "aad_only_auth": {
            "defaultValue": true,
            "type": "Bool"
        }
    },
    "resources": [
        {
            "type": "Microsoft.Sql/servers",
            "apiVersion": "2020-11-01-preview",
            "name": "[parameters('server')]",
            "location": "[parameters('location')]",
            "properties": {
                "administrators": {
                    "login": "[parameters('aad_admin_name')]",
                    "sid": "[parameters('aad_admin_objectid')]",
                    "tenantId": "[parameters('aad_admin_tenantid')]",
                    "principalType": "[parameters('aad_admin_type')]",
                    "azureADOnlyAuthentication": "[parameters('aad_only_auth')]"
                }
            }
        }
    ]
}
```

---

## <a name="azure-sql-managed-instance"></a>Azure SQL Managed Instance

# <a name="portal"></a>[ポータル](#tab/azure-portal)

1. Azure portal の [[SQL デプロイ オプションの選択]](https://portal.azure.com/#create/Microsoft.AzureSQL) ページを参照します。

1. まだ Azure portal にサインインしていない場合は、求められたらサインインします。

1. **[SQL マネージド インスタンス]** で、 **[リソースの種類]** を **[単一インスタンス]** に設定し、 **[作成]** を選択します。

1. **[基本]** タブの **[プロジェクトの詳細]** と **[マネージド ドインスタンスの詳細]** に必須情報を記入します。 これは、SQL Managed Instance をプロビジョニングするために必要な最小限の情報セットです。

   :::image type="content" source="media/authentication-azure-ad-only-authentication/azure-ad-only-managed-instance-create-basic.png" alt-text="Managed Instance の作成の基本タブが表示されている Azure portal のスクリーンショット":::

   構成オプションの詳細については、[クイック スタート: Azure SQL Managed Instance の作成](../managed-instance/instance-create-quickstart.md)に関するページを参照してください。

1. **[認証]** の下の **[認証方法]** にある **[Azure Active Directory (Azure AD) 認証のみを使用]** を選択します。

1. **[管理者の設定]** を選択します。マネージド インスタンス Azure AD 管理者として Azure AD プリンシパルを選択するメニューが表示されます。 完了したら、 **[選択]** ボタンを使用して管理者を設定します。

   :::image type="content" source="media/authentication-azure-ad-only-authentication/azure-ad-only-managed-instance-create-basic-choose-authentication.png" alt-text="Managed Instance の作成の基本タブが表示され、Azure AD 認証のみが選択されている Azure portal のスクリーンショット":::

1. 残りの設定は既定値のまま使用できます。 **[ネットワーク]** や **[セキュリティ]** などのタブや設定の詳細については、[クイック スタート: Azure SQL Managed Instance の作成](../managed-instance/instance-create-quickstart.md)に関する記事を参照してください。

1. 設定の構成が完了したら、 **[確認と作成]** を選択して続行します。 **[作成]** を選択して、マネージド インスタンスのプロビジョニングを開始します。

# <a name="the-azure-cli"></a>[Azure CLI](#tab/azure-cli)

Azure CLI コマンド `az sql mi create` は、新しい Azure SQL Managed Instance をプロビジョニングするために使用されます。 次のコマンドは、Azure AD 専用認証が有効になっている新しいマネージド インスタンスをプロビジョニングします。

> [!NOTE]
> このスクリプトでは、前提条件として仮想ネットワークとサブネットを作成する必要があります。

マネージド インスタンス SQL 管理者ログインが自動的に作成され、パスワードはランダムなパスワードに設定されます。 このプロビジョニングでは SQL 認証接続が無効になるため、SQL 管理者ログインは使用されません。

Azure AD 管理者は、`<AzureADAccount>` に設定したアカウントになり、プロビジョニングの完了時にインスタンスを管理するために使用できます。

この例では次の値を置き換えます。

- `<AzureADAccount>`: Azure AD のユーザーまたはグループを指定できます。 たとえば、`DummyLogin` のように指定します。
- `<AzureADAccountSID>`: ユーザーの Azure AD オブジェクト ID
- `<managedinstancename>`: 作成するマネージド インスタンスの名前を指定します
- `<ResourceGroupName>`: マネージド インスタンスのリソース グループの名前。 リソース グループには、作成された仮想ネットワークとサブネットも含める必要があります
- `subnet` パラメーターは、`<Subscription ID>`、`<ResourceGroupName>`、`<VNetName>`、および `<SubnetName>` を使用して更新する必要があります。 サブスクリプション ID は Azure portal で確認できます

```azurecli
az sql mi create --enable-ad-only-auth --external-admin-principal-type User --external-admin-name <AzureADAccount> --external-admin-sid <AzureADAccountSID> -g <ResourceGroupName> -n <managedinstancename> --subnet /subscriptions/<Subscription ID>/resourceGroups/<ResourceGroupName>/providers/Microsoft.Network/virtualNetworks/<VNetName>/subnets/<SubnetName>
```

詳細については、「[az sql mi create](/cli/azure/sql/mi#az_sql_mi_create)」を参照してください。

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

PowerShell コマンド `New-AzSqlInstance` は、新しい Azure SQL Managed Instance をプロビジョニングするために使用されます。 次のコマンドは、Azure AD 専用認証が有効になっている新しいマネージド インスタンスをプロビジョニングします。

> [!NOTE]
> このスクリプトでは、前提条件として仮想ネットワークとサブネットを作成する必要があります。

マネージド インスタンス SQL 管理者ログインが自動的に作成され、パスワードはランダムなパスワードに設定されます。 このプロビジョニングでは SQL 認証接続が無効になるため、SQL 管理者ログインは使用されません。

Azure AD 管理者は、`<AzureADAccount>` に設定したアカウントになり、プロビジョニングの完了時にインスタンスを管理するために使用できます。

この例では次の値を置き換えます。

- `<managedinstancename>`: 作成するマネージド インスタンスの名前を指定します
- `<ResourceGroupName>`: マネージド インスタンスのリソース グループの名前。 リソース グループには、作成された仮想ネットワークとサブネットも含める必要があります
- `<Location>`: サーバーの場所 (`West US`、`Central US` など)
- `<AzureADAccount>`: Azure AD のユーザーまたはグループを指定できます。 たとえば、`DummyLogin` のように指定します。
- `SubnetId` パラメーターは、`<Subscription ID>`、`<ResourceGroupName>`、`<VNetName>`、および `<SubnetName>` を使用して更新する必要があります。 サブスクリプション ID は Azure portal で確認できます


```powershell
New-AzSqlInstance -Name "<managedinstancename>" -ResourceGroupName "<ResourceGroupName>" -ExternalAdminName "<AzureADAccount>" -EnableActiveDirectoryOnlyAuthentication -Location "<Location>" -SubnetId "/subscriptions/<SubscriptionID>/resourceGroups/<ResourceGroupName>/providers/Microsoft.Network/virtualNetworks/<VNetName>/subnets/<SubnetName>" -LicenseType LicenseIncluded -StorageSizeInGB 1024 -VCore 16 -Edition "GeneralPurpose" -ComputeGeneration Gen4
```

詳細については、「[New-AzSqlInstance](/powershell/module/az.sql/new-azsqlinstance)」をご覧ください。

# <a name="rest-api"></a>[Rest API](#tab/rest-api)

[Managed Instances - Create Or Update](/rest/api/sql/2020-11-01-preview/managed-instances/create-or-update) Rest API を使用して、プロビジョニング中に Azure AD 専用認証が有効になっているマネージド インスタンスを作成できます。

> [!NOTE]
> このスクリプトでは、前提条件として仮想ネットワークとサブネットを作成する必要があります。

次のスクリプトでは、Azure SQL マネージド インスタンスをプロビジョニングし、Azure AD 管理者を `<AzureADAccount>` として設定して、Azure AD 専用認証を有効にします。 インスタンス SQL 管理者ログインも自動的に作成され、パスワードはランダムなパスワードに設定されます。 このプロビジョニングでは SQL 認証接続が無効になるため、SQL 管理者ログインは使用されません。

Azure AD 管理者 `<AzureADAccount>` は、プロビジョニングが完了したときにインスタンスを管理するために使用できます。

この例では次の値を置き換えます。

- `<tenantId>`: [Azure portal](https://portal.azure.com) から **Azure Active Directory** リソースにアクセスして確認できます。 **[概要]** ペインに **[テナント ID]** が表示されます
- `<subscriptionId>`: サブスクリプション ID は Azure portal で確認できます
- `<instanceName>`: 一意のマネージド インスタンス名を使用します
- `<ResourceGroupName>`: 論理サーバーのリソース グループの名前
- `<AzureADAccount>`: Azure AD のユーザーまたはグループを指定できます。 たとえば、`DummyLogin` のように指定します。
- `<Location>`: サーバーの場所 (`westus2`、`centralus` など)
- `<objectId>`: [Azure portal](https://portal.azure.com) から **Azure Active Directory** リソースにアクセスして確認できます。 **[ユーザー]** ペインで、Azure AD ユーザーを検索し、**オブジェクト ID** を特定します
- `subnetId` パラメーターは、`<ResourceGroupName>`、`Subscription ID`、`<VNetName>`、および `<SubnetName>` を使用して更新する必要があります。


```rest
Import-Module Azure
Import-Module MSAL.PS

$tenantId = '<tenantId>'
$clientId = '1950a258-227b-4e31-a9cf-717495945fc2' # Static Microsoft client ID used for getting a token
$subscriptionId = '<subscriptionId>'
$uri = "urn:ietf:wg:oauth:2.0:oob" 
$instanceName = "<instanceName>"
$resourceGroupName = "<ResourceGroupName>"
$scopes ="https://management.core.windows.net/.default"

Login-AzAccount -tenantId $tenantId

# Login as an Azure AD user with permission to provision a managed instance

$result = Get-MsalToken -RedirectUri $uri -ClientId $clientId -TenantId $tenantId -Scopes $scopes

$authHeader = @{
'Content-Type'='application\json; '
'Authorization'=$result.CreateAuthorizationHeader()
}

$body = '{
"name": "<instanceName>", "type": "Microsoft.Sql/managedInstances", "identity": { "type": "SystemAssigned"},"location": "<Location>", "sku": {"name": "GP_Gen5", "tier": "GeneralPurpose", "family":"Gen5","capacity": 8},
"properties": {"administrators":{ "login":"<AzureADAccount>", "sid":"<objectId>", "tenantId":"<tenantId>", "principalType":"User", "azureADOnlyAuthentication":true },
"subnetId": "/subscriptions/<subscriptionId>/resourceGroups/<ResourceGroupName>/providers/Microsoft.Network/virtualNetworks/<VNetName>/subnets/<SubnetName>",
"licenseType": "LicenseIncluded", "vCores": 8, "storageSizeInGB": 2048, "collation": "SQL_Latin1_General_CP1_CI_AS", "proxyOverride": "Proxy", "timezoneId": "UTC", "privateEndpointConnections": [], "storageAccountType": "GRS", "zoneRedundant": false 
  }
}'

# To provision the instance, execute the `PUT` command

Invoke-RestMethod -Uri https://management.azure.com/subscriptions/$subscriptionId/resourceGroups/$resourceGroupName/providers/Microsoft.Sql/managedInstances/$instanceName/?api-version=2020-11-01-preview -Method PUT -Headers $authHeader -Body $body -ContentType "application/json"

```

結果を確認するには、次のように `GET` コマンドを実行します。

```rest
Invoke-RestMethod -Uri https://management.azure.com/subscriptions/$subscriptionId/resourceGroups/$resourceGroupName/providers/Microsoft.Sql/managedInstances/$instanceName/?api-version=2020-11-01-preview -Method GET -Headers $authHeader  | Format-List
```

# <a name="arm-template"></a>[ARM テンプレート](#tab/arm-template)

インスタンスに対して Azure AD 管理者セットを設定し、Azure AD 専用認証を有効にした新しいマネージド インスタンス、仮想ネットワーク、およびサブネットをプロビジョニングするには、次のテンプレートを使用します。

[Azure portal のカスタム デプロイ](https://portal.azure.com/#create/Microsoft.Template)を使用し、**エディターで独自のテンプレートを作成** します。 次に、例に貼り付けたら、構成を **保存** します。

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.1",
    "parameters": {
        "managedInstanceName": {
            "type": "String",
            "metadata": {
                "description": "Enter managed instance name."
            }
        },
        "aad_admin_name": {
            "type": "String",
            "metadata": {
                "description": "The name of the Azure AD admin for the SQL managed instance."
            }
        },
        "aad_admin_objectid": {
            "type": "String",
            "metadata": {
                "description": "The Object ID of the Azure AD admin."
            }
        },
        "aad_admin_tenantid": {
            "type": "String",
            "defaultValue": "[subscription().tenantId]",
            "metadata": {
                "description": "The Tenant ID of the Azure Active Directory"
            }
        },
        "aad_admin_type": {
            "defaultValue": "User",
            "allowedValues": [
                "User",
                "Group",
                "Application"
            ],
            "type": "String"
        },
        "aad_only_auth": {
            "defaultValue": true,
            "type": "Bool"
        },
        "location": {
            "defaultValue": "[resourceGroup().location]",
            "type": "String",
            "metadata": {
                "description": "Enter location. If you leave this field blank resource group location would be used."
            }
        },
        "virtualNetworkName": {
            "type": "String",
            "defaultValue": "SQLMI-VNET",
            "metadata": {
                "description": "Enter virtual network name. If you leave this field blank name will be created by the template."
            }
        },
        "addressPrefix": {
            "defaultValue": "10.0.0.0/16",
            "type": "String",
            "metadata": {
                "description": "Enter virtual network address prefix."
            }
        },
        "subnetName": {
            "type": "String",
            "defaultValue": "ManagedInstances",
            "metadata": {
                "description": "Enter subnet name. If you leave this field blank name will be created by the template."
            }
        },
        "subnetPrefix": {
            "defaultValue": "10.0.0.0/24",
            "type": "String",
            "metadata": {
                "description": "Enter subnet address prefix."
            }
        },
        "skuName": {
            "defaultValue": "GP_Gen5",
            "allowedValues": [
                "GP_Gen5",
                "BC_Gen5"
            ],
            "type": "String",
            "metadata": {
                "description": "Enter sku name."
            }
        },
        "vCores": {
            "defaultValue": 16,
            "allowedValues": [
                8,
                16,
                24,
                32,
                40,
                64,
                80
            ],
            "type": "Int",
            "metadata": {
                "description": "Enter number of vCores."
            }
        },
        "storageSizeInGB": {
            "defaultValue": 256,
            "minValue": 32,
            "maxValue": 8192,
            "type": "Int",
            "metadata": {
                "description": "Enter storage size."
            }
        },
        "licenseType": {
            "defaultValue": "LicenseIncluded",
            "allowedValues": [
                "BasePrice",
                "LicenseIncluded"
            ],
            "type": "String",
            "metadata": {
                "description": "Enter license type."
            }
        }
    },
    "variables": {
        "networkSecurityGroupName": "[concat('SQLMI-', parameters('managedInstanceName'), '-NSG')]",
        "routeTableName": "[concat('SQLMI-', parameters('managedInstanceName'), '-Route-Table')]"
    },
    "resources": [
        {
            "type": "Microsoft.Network/networkSecurityGroups",
            "apiVersion": "2020-06-01",
            "name": "[variables('networkSecurityGroupName')]",
            "location": "[parameters('location')]",
            "properties": {
                "securityRules": [
                    {
                        "name": "allow_tds_inbound",
                        "properties": {
                            "description": "Allow access to data",
                            "protocol": "Tcp",
                            "sourcePortRange": "*",
                            "destinationPortRange": "1433",
                            "sourceAddressPrefix": "VirtualNetwork",
                            "destinationAddressPrefix": "*",
                            "access": "Allow",
                            "priority": 1000,
                            "direction": "Inbound"
                        }
                    },
                    {
                        "name": "allow_redirect_inbound",
                        "properties": {
                            "description": "Allow inbound redirect traffic to Managed Instance inside the virtual network",
                            "protocol": "Tcp",
                            "sourcePortRange": "*",
                            "destinationPortRange": "11000-11999",
                            "sourceAddressPrefix": "VirtualNetwork",
                            "destinationAddressPrefix": "*",
                            "access": "Allow",
                            "priority": 1100,
                            "direction": "Inbound"
                        }
                    },
                    {
                        "name": "deny_all_inbound",
                        "properties": {
                            "description": "Deny all other inbound traffic",
                            "protocol": "*",
                            "sourcePortRange": "*",
                            "destinationPortRange": "*",
                            "sourceAddressPrefix": "*",
                            "destinationAddressPrefix": "*",
                            "access": "Deny",
                            "priority": 4096,
                            "direction": "Inbound"
                        }
                    },
                    {
                        "name": "deny_all_outbound",
                        "properties": {
                            "description": "Deny all other outbound traffic",
                            "protocol": "*",
                            "sourcePortRange": "*",
                            "destinationPortRange": "*",
                            "sourceAddressPrefix": "*",
                            "destinationAddressPrefix": "*",
                            "access": "Deny",
                            "priority": 4096,
                            "direction": "Outbound"
                        }
                    }
                ]
            }
        },
        {
            "type": "Microsoft.Network/routeTables",
            "apiVersion": "2020-06-01",
            "name": "[variables('routeTableName')]",
            "location": "[parameters('location')]",
            "properties": {
                "disableBgpRoutePropagation": false
            }
        },
        {
            "type": "Microsoft.Network/virtualNetworks",
            "apiVersion": "2020-06-01",
            "name": "[parameters('virtualNetworkName')]",
            "location": "[parameters('location')]",
            "dependsOn": [
                "[variables('routeTableName')]",
                "[variables('networkSecurityGroupName')]"
            ],
            "properties": {
                "addressSpace": {
                    "addressPrefixes": [
                        "[parameters('addressPrefix')]"
                    ]
                },
                "subnets": [
                    {
                        "name": "[parameters('subnetName')]",
                        "properties": {
                            "addressPrefix": "[parameters('subnetPrefix')]",
                            "routeTable": {
                                "id": "[resourceId('Microsoft.Network/routeTables', variables('routeTableName'))]"
                            },
                            "networkSecurityGroup": {
                                "id": "[resourceId('Microsoft.Network/networkSecurityGroups', variables('networkSecurityGroupName'))]"
                            },
                            "delegations": [
                                {
                                    "name": "miDelegation",
                                    "properties": {
                                        "serviceName": "Microsoft.Sql/managedInstances"
                                    }
                                }
                            ]
                        }
                    }
                ]
            }
        },
        {
            "type": "Microsoft.Sql/managedInstances",
            "apiVersion": "2020-11-01-preview",
            "name": "[parameters('managedInstanceName')]",
            "location": "[parameters('location')]",
            "dependsOn": [
                "[parameters('virtualNetworkName')]"
            ],
            "sku": {
                "name": "[parameters('skuName')]"
            },
            "identity": {
                "type": "SystemAssigned"
            },
            "properties": {
                "subnetId": "[resourceId('Microsoft.Network/virtualNetworks/subnets', parameters('virtualNetworkName'), parameters('subnetName'))]",
                "storageSizeInGB": "[parameters('storageSizeInGB')]",
                "vCores": "[parameters('vCores')]",
                "licenseType": "[parameters('licenseType')]",
                "administrators": {
                    "login": "[parameters('aad_admin_name')]",
                    "sid": "[parameters('aad_admin_objectid')]",
                    "tenantId": "[parameters('aad_admin_tenantid')]",
                    "principalType": "[parameters('aad_admin_type')]",
                    "azureADOnlyAuthentication": "[parameters('aad_only_auth')]"
                }
            }
        }
    ]
}
```

---

### <a name="grant-directory-readers-permissions"></a>ディレクトリ閲覧者のアクセス許可を付与する

マネージド インスタンスのデプロイが完了すると、Azure Active Directory にアクセスするために、Managed Instance に **読み取り** アクセス許可が必要な場合があります。 読み取りアクセス許可を付与するには、十分な特権を持つユーザーが Azure portal に表示されているメッセージをクリックします。 詳細については、「[Azure SQL の Azure Active Directory のディレクトリ閲覧者ロール](authentication-aad-directory-readers-role.md)」を参照してください。

:::image type="content" source="media/authentication-azure-ad-only-authentication/azure-ad-portal-read-permissions.png" alt-text="Azure portal で読み取りアクセス許可が必要であることを示す Active Directory 管理メニューのスクリーンショット":::

## <a name="limitations"></a>制限事項

- サーバー管理者のパスワードをリセットするには、Azure AD 専用認証を無効にする必要があります。
- Azure AD 専用認証が無効になっている場合は、すべての API を使用するときに、サーバー管理者とパスワードを持つサーバーを作成する必要があります。

## <a name="next-steps"></a>次のステップ

- SQL サーバーまたはマネージド インスタンスが既に存在し、Azure AD 専用認証を有効にするだけの場合は、「[チュートリアル: Azure SQL を使用して Azure Active Directory 専用認証を有効にする](authentication-azure-ad-only-authentication-tutorial.md)」を参照してください。
- Azure AD 専用認証機能の詳細については、「[Azure SQL を使用した Azure AD 専用認証](authentication-azure-ad-only-authentication.md)」を参照してください。
- Azure AD 認証が有効になっているサーバーの作成を強制する場合は、[Azure SQL を使用して Azure Active Directory のみの認証を行うための Azure Policy](authentication-azure-ad-only-authentication-policy.md) に関する記事を参照してください
