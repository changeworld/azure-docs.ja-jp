---
title: Azure Automation アカウントのシステム割り当てマネージド ID を使用する
description: この記事では、Azure Automation アカウントのマネージド ID を設定する方法について説明します。
services: automation
ms.subservice: process-automation
ms.date: 10/26/2021
ms.topic: conceptual
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 685126603c302a02d56aff51873cd34340947494
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/03/2021
ms.locfileid: "131470758"
---
# <a name="using-a-system-assigned-managed-identity-for-an-azure-automation-account"></a>Azure Automation アカウントのシステム割り当てマネージド ID を使用する

この記事では、Azure Automation アカウントに対してシステム割り当てマネージド ID を有効にし、それを使用して他のリソースにアクセスする方法を説明します。 マネージド ID と Azure Automation の連携方法の詳細については、[マネージド ID](automation-security-overview.md#managed-identities) に関する記事を参照してください。

Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) を作成してください。

## <a name="prerequisites"></a>前提条件

- Azure Automation アカウント。 手順については、「[Azure Automation アカウントを作成する](./quickstarts/create-account-portal.md)」をご覧ください。

- 最新バージョンの Az PowerShell モジュール Az.Accounts、Az.Resources、Az.Automation、Az.KeyVault。

- Automation Runbook からアクセスする必要がある Azure リソース。 このリソースには、マネージド ID に対して定義されたロールが必要です。これにより、Automation Runbook はリソースへのアクセスを認証できます。 ロールを追加するには、対応する Azure AD テナントのリソースの所有者である必要があります。

- マネージド ID を使用してハイブリッド ジョブを実行する場合は、Hybrid Runbook Worker を最新バージョンに更新します。 最低限必要なバージョンは次のとおりです。

  - Windows Hybrid Runbook Worker: バージョン 7.3.1125.0
  - Linux Hybrid Runbook Worker: バージョン 1.7.4.0

## <a name="enable-a-system-assigned-managed-identity-for-an-azure-automation-account"></a>Azure Automation アカウントのシステム割り当てマネージド ID を有効にする

有効にすると、次のプロパティがシステム割り当てマネージド ID に割り当てられます。

|プロパティ (JSON) | 値 | 説明|
|----------|-----------|------------|
| principalid | \<principal-ID\> | Azure AD テナント内の Automation アカウントを表すシステム割り当てマネージド ID に対するサービス プリンシパル オブジェクトのグローバル一意識別子 (GUID)。 この GUID は、"オブジェクト ID" (objectID) として表されることがあります。 |
| tenantid | \<Azure-AD-tenant-ID\> | Automation アカウントが現在メンバーである Azure AD テナントを表すグローバル一意識別子 (GUID)。 Azure AD テナント内では、サービス プリンシパルは Automation アカウントと同じ名前です。 |

Azure portal、PowerShell、Azure REST API、または ARM テンプレートを使用して、Azure Automation アカウントのシステム割り当てマネージド ID を有効にできます。 関連する PowerShell の例として、最初に [Connect-AzAccount](/powershell/module/Az.Accounts/Connect-AzAccount) コマンドレットを使用して、Azure に対話的にサインインし、指示に従います。

```powershell
# Sign in to your Azure subscription
$sub = Get-AzSubscription -ErrorAction SilentlyContinue
if(-not($sub))
{
    Connect-AzAccount
}

# If you have multiple subscriptions, set the one to use
# Select-AzSubscription -SubscriptionId "<SUBSCRIPTIONID>"
```

次に、例全体で使用される一連の変数を初期化します。 以下の値を修正し、実行します。

```powershell
$subscriptionID = "subscriptionID"
$resourceGroup = "resourceGroupName"
$automationAccount = "automationAccountName"
```

> [!IMPORTANT]
> 新しい Automation アカウントレベルの ID によって、以前の VM レベルのシステム割り当て ID (「[マネージド ID で Runbook 認証を使用する](./automation-hrw-run-runbooks.md#runbook-auth-managed-identities)」で説明されている) がオーバーライドされます。 VM のシステム割り当て ID を使用して Runbook リソースにアクセスする Azure VM でハイブリッド ジョブを実行している場合は、そのハイブリッド ジョブに対して Automation アカウント ID が使用されます。 これは、Automation アカウントのカスタマー マネージド キー (CMK) 機能を使用している場合に、既存のジョブの実行が影響を受ける可能性があることを意味します。<br/><br/>VM のマネージド ID を引き続き使用する場合は、Automation アカウント レベルの ID を有効にしないでください。 それを既に有効にしている場合は、Automation アカウントのシステム割り当てマネージド ID を無効にできます。 [Azure Automation アカウントのマネージド ID の無効化](./disable-managed-identity-for-automation.md)に関する記事を参照してください。

### <a name="enable-using-the-azure-portal"></a>Azure portal を使用した有効化

次の手順に従います。

1. [Azure portal](https://portal.azure.com) にサインインします。

1. Azure Portal で、Automation アカウントに移動します。

1.  **[アカウント設定]** で、 **[ID]** を選択します。

1. **[システム割り当て済み]** オプションを **[オン]** に設定し、 **[保存]** を押します。 確認を求めるメッセージが表示されたら、 **[はい]** を選択します。

   :::image type="content" source="media/managed-identity/managed-identity-on.png" alt-text="Azure portal でのシステム割り当て ID の有効化。":::

   これで、Automation アカウントはシステム割り当て ID を使用できるようになりました。これは Azure Active Directory (Azure AD) に登録され、オブジェクト ID で表されます。

   :::image type="content" source="media/managed-identity/managed-identity-object-id.png" alt-text="マネージド ID であるオブジェクト ID。":::

### <a name="enable-using-powershell"></a>PowerShell を使用した有効化

システム割り当てマネージド ID を有効にするには、PowerShell コマンドレット [Set-AzAutomationAccount](/powershell/module/az.automation/set-azautomationaccount) を使用します。

```powershell
$output = Set-AzAutomationAccount `
    -ResourceGroupName $resourceGroup `
    -Name $automationAccount `
    -AssignSystemIdentity

$output
```

出力は次のようになります。

:::image type="content" source="media/enable-managed-identity-for-automation/set-azautomationaccount-output.png" alt-text="set-azautomationaccount コマンドからの出力。":::

追加の出力については、`$output.identity | ConvertTo-Json` を指定するように例を変更します。

### <a name="enable-using-a-rest-api"></a>REST API を使用した有効化

構文と手順の例を次に示します。

#### <a name="syntax"></a>構文

次の本文の構文では、HTTP **PATCH** メソッドを使用して、既存の Automation アカウントに対してシステム割り当てマネージド ID を有効にできます。 ただし、この構文では、Automation アカウントに関連付けられている既存のユーザー割り当てマネージド ID が削除されます。

```json
{ 
 "identity": { 
   "type": "SystemAssigned" 
  } 
}
```

複数のユーザー割り当て ID が定義されている場合にそれらを保持し、システム割り当て ID のみを削除するには、コンマ区切りリストを使用してそれぞれのユーザー割り当て ID を指定する必要があります。 以下の例では、HTTP **PATCH** メソッドを使用しています。

```json
{ 
  "identity" : {
    "type": "SystemAssigned, UserAssigned",
    "userAssignedIdentities": {
        "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/resourceGroupName/providers/Microsoft.ManagedIdentity/userAssignedIdentities/cmkID": {},
        "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/resourceGroupName/providers/Microsoft.ManagedIdentity/userAssignedIdentities/cmkID2": {}
    }
  }
}

```

API の構文は次のとおりです。

```http
PATCH https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/resource-group-name/providers/Microsoft.Automation/automationAccounts/automation-account-name?api-version=2020-01-13-preview
```

#### <a name="example"></a>例

次の手順に従います。

1. 本文の構文をコピーして、`body_sa.json` という名前のファイルに貼り付けます。 ローカル コンピューターまたは Azure ストレージ アカウントにファイルを保存します。

1. 以下の変数値を更新し、実行します。

    ```powershell
    $file = "path\body_sa.json"
    ```

1. この例では、PowerShell コマンドレット [Invoke-RestMethod](/powershell/module/microsoft.powershell.utility/invoke-restmethod) を使用して、PATCH 要求を対象の Automation アカウントに送信します。

    ```powershell
    # build URI
    $URI = "https://management.azure.com/subscriptions/$subscriptionID/resourceGroups/$resourceGroup/providers/Microsoft.Automation/automationAccounts/$automationAccount`?api-version=2020-01-13-preview"
    
    # build body
    $body = Get-Content $file
    
    # obtain access token
    $azContext = Get-AzContext
    $azProfile = [Microsoft.Azure.Commands.Common.Authentication.Abstractions.AzureRmProfileProvider]::Instance.Profile
    $profileClient = New-Object -TypeName Microsoft.Azure.Commands.ResourceManager.Common.RMProfileClient -ArgumentList ($azProfile)
    $token = $profileClient.AcquireAccessToken($azContext.Subscription.TenantId)
    $authHeader = @{
        'Content-Type'='application/json'
        'Authorization'='Bearer ' + $token.AccessToken
    }
    
    # Invoke the REST API
    $response = Invoke-RestMethod -Uri $URI -Method PATCH -Headers $authHeader -Body $body
    
    # Review output
    $response.identity | ConvertTo-Json
    ```

    出力は次のようになります。

    ```json
    {
        "PrincipalId":  "aaaaaaaa-aaaa-aaaa-aaaa-aaaaaaaaaaaa",
        "TenantId":  "bbbbbbbb-bbbb-bbbb-bbbb-bbbbbbbbbbbb",
        "Type":  0,
        "UserAssignedIdentities":  null
    }
    ```

### <a name="enable-using-an-arm-template"></a>ARM テンプレートを使用した有効化

構文と手順の例を次に示します。

#### <a name="template-syntax"></a>テンプレート構文

次のサンプル テンプレート構文では、既存の Automation アカウントに対してシステム割り当てマネージド ID が有効になります。 ただし、この構文では、Automation アカウントに関連付けられている既存のユーザー割り当てマネージド ID が削除されます。

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "resources": [
    {
      "type": "Microsoft.Automation/automationAccounts",
      "apiVersion": "2020-01-13-preview",
      "name": "yourAutomationAccount",
      "location": "[resourceGroup().location]",
      "identity": {
        "type": "SystemAssigned"
        },
      "properties": {
        "sku": {
          "name": "Basic"
        }
      }
    }
  ]
}
```

#### <a name="example"></a>例

次の手順に従います。

1. Automation アカウントを使用して `template_sa.json` という名前のファイルに保存するために、上記のテンプレートの構文を変更します。

1. 以下の変数値を更新し、実行します。

    ```powershell
    $templateFile = "path\template_sa.json"
    ```

1. PowerShell コマンドレット [New-AzResourceGroupDeployment](/powershell/module/az.resources/new-azresourcegroupdeployment) を使用してテンプレートをデプロイします。

    ```powershell
    New-AzResourceGroupDeployment `
        -Name "SystemAssignedDeployment" `
        -ResourceGroupName $resourceGroup `
        -TemplateFile $templateFile
    ```

   コマンドは出力を生成しません。ただし、次のコードを使用して確認できます。

    ```powershell
    (Get-AzAutomationAccount `
    -ResourceGroupName $resourceGroup `
    -Name $automationAccount).Identity | ConvertTo-Json
    ```

   出力は、上記の REST API の例に示されている出力のようになります。

## <a name="give-access-to-azure-resources-by-obtaining-a-token"></a>トークンを取得して Azure リソースにアクセスできるようにする

Azure Key Vault など、Azure AD によって保護されているその他のリソースにアクセスするために、Automation アカウントはそのシステム割り当てマネージド ID を使用してトークンを取得できます。 これらのトークンは、アプリケーションの特定のユーザーを表すものではありません。 代わりに、それらは、リソースにアクセスしているアプリケーションを表します。 たとえば、今回の場合、トークンは Automation アカウントを表します。

システム割り当てマネージド ID を認証に使用するには、その ID を使用する予定の Azure リソースに対する ID のアクセスを先に設定します。 このタスクを完了するには、ターゲットの Azure リソース上でその ID に適切な役割を割り当てます。

最小特権の原則に従って、Runbook を実行するのに必要なアクセス許可のみを慎重に割り当てます。 たとえば、Automation アカウントが Azure VM の開始または停止を行うためにのみ必要な場合は、その実行アカウントまたはマネージド ID に割り当てられるアクセス許可は、VM の開始または停止のためのみのものである必要があります。 同様に、Runbook が BLOB ストレージから読み取りを行う場合は、読み取り専用アクセス許可を割り当てます。

次の例では、Azure PowerShell を使用して、サブスクリプションの共同作成者ロールをターゲットの Azure リソースに割り当てる方法を示します。 共同作成者ロールは例として使用されます。ご自身のケースに必要な場合と不要な場合があります。

```powershell
New-AzRoleAssignment `
    -ObjectId <automation-Identity-object-id> `
    -Scope "/subscriptions/<subscription-id>" `
    -RoleDefinitionName "Contributor"
```

## <a name="authenticate-access-with-system-assigned-managed-identity"></a>システム割り当てマネージド ID を使用してアクセスを認証する

Automation アカウントのマネージド ID を有効にし、ID がターゲット リソースにアクセスできるようにした後、マネージド ID をサポートするリソースに対して Runbook 内でその ID を指定できます。 ID のサポートのために、Az cmdlet コマンドレットの `Connect-AzAccount` コマンドレットを使用します。 PowerShell リファレンスの「[Connect-AzAccount](/powershell/module/az.accounts/Connect-AzAccount)」を参照してください。

```powershell
# Ensures you do not inherit an AzContext in your runbook
Disable-AzContextAutosave -Scope Process

# Connect to Azure with system-assigned managed identity
$AzureContext = (Connect-AzAccount -Identity).context

# set and store context
$AzureContext = Set-AzContext -SubscriptionName $AzureContext.Subscription -DefaultProfile $AzureContext
```

> [!NOTE]
> 非推奨の AzureRM コマンドレットを組織でまだ使用している場合は、`Connect-AzureRMAccount -Identity` を使用できます。

## <a name="generate-an-access-token-without-using-azure-cmdlets"></a>Azure コマンドレットを使用せずにアクセス トークンを生成する

HTTP エンドポイントに対して、次のことを確認してください。

- メタデータ ヘッダーが存在し、"true" に設定されている必要があります。
- リソースを GET 要求の場合はクエリ パラメーターとして、POST 要求の場合はフォーム データとして、要求と共に渡す必要があります。
- X-IDENTITY-HEADER は、Hybrid Runbook Worker の環境変数 IDENTITY_HEADER の値に設定する必要があります。
- Post 要求のコンテンツの種類は 'application/x-www-form-urlencoded' である必要があります。

### <a name="get-access-token-for-system-assigned-managed-identity-using-http-get"></a>HTTP Get を使用してシステム割り当てマネージド ID のアクセス トークンを取得する

```powershell
$resource= "?resource=https://management.azure.com/" 
$url = $env:IDENTITY_ENDPOINT + $resource 
$Headers = New-Object "System.Collections.Generic.Dictionary[[String],[String]]" 
$Headers.Add("X-IDENTITY-HEADER", $env:IDENTITY_HEADER) 
$Headers.Add("Metadata", "True") 
$accessToken = Invoke-RestMethod -Uri $url -Method 'GET' -Headers $Headers
Write-Output $accessToken.access_token
```

### <a name="get-access-token-for-system-assigned-identity-using-http-post"></a>HTTP Post を使用してシステム割り当て ID のアクセス トークンを取得する

```powershell
$url = $env:IDENTITY_ENDPOINT  
$headers = New-Object "System.Collections.Generic.Dictionary[[String],[String]]" 
$headers.Add("X-IDENTITY-HEADER", $env:IDENTITY_HEADER) 
$headers.Add("Metadata", "True") 
$body = @{resource='https://management.azure.com/' } 
$accessToken = Invoke-RestMethod $url -Method 'POST' -Headers $headers -ContentType 'application/x-www-form-urlencoded' -Body $body 
Write-Output $accessToken.access_token
```

### <a name="using-system-assigned-managed-identity-in-azure-powershell"></a>Azure PowerShell でシステム割り当てマネージド ID を使用する

詳しくは「[Get-AzKeyVaultSecret](/powershell/module/az.keyvault/get-azkeyvaultsecret)」をご覧ください。

```powershell
Write-Output "Connecting to azure via  Connect-AzAccount -Identity" 
Connect-AzAccount -Identity 
Write-Output "Successfully connected with Automation account's Managed Identity" 
Write-Output "Trying to fetch value from key vault using MI. Make sure you have given correct access to Managed Identity" 
$secret = Get-AzKeyVaultSecret -VaultName '<KVname>' -Name '<KeyName>' 

$ssPtr = [System.Runtime.InteropServices.Marshal]::SecureStringToBSTR($secret.SecretValue) 
try { 
  $secretValueText = [System.Runtime.InteropServices.Marshal]::PtrToStringBSTR($ssPtr) 
    Write-Output $secretValueText 
} finally { 
    [System.Runtime.InteropServices.Marshal]::ZeroFreeBSTR($ssPtr) 
}
```

### <a name="using-system-assigned-managed-identity-in-python-runbook"></a>Python Runbook でシステム割り当てマネージド ID を使用する

```python
#!/usr/bin/env python3 
import os 
import requests  
# printing environment variables 
endPoint = os.getenv('IDENTITY_ENDPOINT')+"?resource=https://management.azure.com/" 
identityHeader = os.getenv('IDENTITY_HEADER') 
payload={} 
headers = { 
  'X-IDENTITY-HEADER': identityHeader,
  'Metadata': 'True' 
} 
response = requests.request("GET", endPoint, headers=headers, data=payload) 
print(response.text) 
```

### <a name="using-system-assigned-managed-identity-to-access-sql-database"></a>システム割り当てマネージド ID を使用した SQL Database へのアクセス

Azure SQL データベースへのアクセス権のプロビジョニングの詳細は、[Azure AD 管理者のプロビジョニング (SQL Database)](../azure-sql/database/authentication-aad-configure.md#provision-azure-ad-admin-sql-database) に関するセクションを参照してください。

```powershell
$queryParameter = "?resource=https://database.windows.net/" 
$url = $env:IDENTITY_ENDPOINT + $queryParameter
$Headers = New-Object "System.Collections.Generic.Dictionary[[String],[String]]" 
$Headers.Add("X-IDENTITY-HEADER", $env:IDENTITY_HEADER) 
$Headers.Add("Metadata", "True") 
$content =[System.Text.Encoding]::Default.GetString((Invoke-WebRequest -UseBasicParsing -Uri $url -Method 'GET' -Headers $Headers).RawContentStream.ToArray()) | ConvertFrom-Json 
$Token = $content.access_token 
echo "The managed identities for Azure resources access token is $Token" 
$SQLServerName = "<ServerName>"    # Azure SQL logical server name  
$DatabaseName = "<DBname>"     # Azure SQL database name 
Write-Host "Create SQL connection string" 
$conn = New-Object System.Data.SqlClient.SQLConnection  
$conn.ConnectionString = "Data Source=$SQLServerName.database.windows.net;Initial Catalog=$DatabaseName;Connect Timeout=30" 
$conn.AccessToken = $Token 
Write-host "Connect to database and execute SQL script" 
$conn.Open()  
$ddlstmt = "CREATE TABLE Person( PersonId INT IDENTITY PRIMARY KEY, FirstName NVARCHAR(128) NOT NULL)" 
Write-host " " 
Write-host "SQL DDL command" 
$ddlstmt 
$command = New-Object -TypeName System.Data.SqlClient.SqlCommand($ddlstmt, $conn) 
Write-host "results" 
$command.ExecuteNonQuery() 
$conn.Close()
```

## <a name="migrate-from-existing-run-as-accounts-to-managed-identity"></a>既存の実行アカウントからマネージド ID に移行する

Azure Automation を使用すると、実行アカウントを使用して、Azure Resource Manager リソースまたはクラシック デプロイ モデルにデプロイされたリソースを管理するための認証を行うことができるようになります。 Runbook 認証のマネージド ID に実行アカウントを切り替えるには、次の手順に従います。

1. [システム割り当て](enable-managed-identity-for-automation.md)と[ユーザー割り当て](add-user-assigned-identity.md)、または両方の種類のマネージド ID を有効にします。
1. 実行アカウントが割り当てられたものに一致する同じ特権をマネージド ID に Azure リソースに付与します。
1. マネージド ID を使用して認証を行う Runbook を更新します。
1. マネージド ID を使用するために Runbook を変更します。 ID のサポートのために、Az cmdlet コマンドレットの `Connect-AzAccount` コマンドレットを使用します。 PowerShell リファレンスの「[Connect-AzAccount](/powershell/module/az.accounts/Connect-AzAccount)」を参照してください。

   - AzureRM モジュールを使用している場合は、`AzureRM.Profile` を最新バージョンに更新し、`Add-AzureRMAccount` コマンドレットを使用して `Connect-AzureRMAccount –Identity` に置き換える必要があります。
   - Az モジュールを使用している場合は、[Azure PowerShell モジュールの更新](automation-update-azure-modules.md#update-az-modules)に関する記事の手順に従って、最新バージョンに更新します。

## <a name="next-steps"></a>次の手順

- Runbook が正常に完了しない場合は、「[Azure Automation マネージド ID に関する問題のトラブルシューティング](troubleshoot/managed-identity.md)」を参照してください。

- マネージド ID を無効にする必要がある場合は、「[Azure Automation アカウントのマネージド ID を無効にする](disable-managed-identity-for-automation.md)」を参照してください。

- Azure Automation アカウントのセキュリティの概要については、[Automation アカウントの認証の概要](automation-security-overview.md)に関する記事を参照してください。
