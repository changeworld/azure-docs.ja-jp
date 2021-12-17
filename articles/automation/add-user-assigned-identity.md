---
title: Azure Automation アカウントのユーザー割り当てマネージド ID を使用する
description: この記事では、Azure Automation アカウントのユーザー割り当てマネージド ID を設定する方法について説明します。
services: automation
ms.subservice: process-automation
ms.date: 10/26/2021
ms.topic: conceptual
ms.openlocfilehash: 033f25f5d5902b339a2777cffc8c526a459ca587
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/03/2021
ms.locfileid: "131435531"
---
# <a name="using-a-user-assigned-managed-identity-for-an-azure-automation-account"></a>Azure Automation アカウントのユーザー割り当てマネージド ID を使用する

この記事では、Azure Automation アカウントに対してユーザー割り当てマネージド ID を追加し、それを使用して他のリソースにアクセスする方法を説明します。 マネージド ID と Azure Automation の連携方法の詳細については、[マネージド ID](automation-security-overview.md#managed-identities) に関する記事を参照してください。

> [!NOTE]
> ユーザー割り当てマネージド ID がサポートされているのはクラウド ジョブの場合だけです。  

Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) を作成してください。

## <a name="prerequisites"></a>前提条件

- Azure Automation アカウント。 手順については、「[Azure Automation アカウントを作成する](./quickstarts/create-account-portal.md)」をご覧ください。

- システム割り当てマネージド ID。 手順については、「[Azure Automation アカウントのシステム割り当てマネージド ID を使用する](enable-managed-identity-for-automation.md)」を参照してください。

- ユーザー割り当てマネージド ID。 手順については、「[ユーザー割り当てマネージド ID を作成する](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md#create-a-user-assigned-managed-identity)」を参照してください。

- ユーザー割り当てマネージド ID と、その ID を使用して Runbook によって管理されるターゲット Azure リソースが、同じ Azure サブスクリプションに存在する必要があります。

- Azure アカウント モジュールの最新バージョン。 現在 2.2.8 です。 (このバージョンの詳細は [Az.Accounts](https://www.powershellgallery.com/packages/Az.Accounts/) をご覧ください。)

- Automation Runbook からアクセスする必要がある Azure リソース。 このリソースには、ユーザー割り当てマネージド ID に対して定義されたロールが必要です。これにより、Automation Runbook はリソースへのアクセスを認証できます。 ロールを追加するには、対応する Azure AD テナントのリソースの所有者である必要があります。

- ユーザー割り当てマネージド ID を使用してハイブリッド ジョブを実行する場合は、Hybrid Runbook Worker を最新バージョンに更新します。 最低限必要なバージョンは次のとおりです。

  - Windows Hybrid Runbook Worker: バージョン 7.3.1125.0
  - Linux Hybrid Runbook Worker: バージョン 1.7.4.0

## <a name="add-user-assigned-managed-identity-for-azure-automation-account"></a>Azure Automation アカウントのユーザー割り当てマネージド ID を追加する

Azure portal、PowerShell、Azure REST API、または ARM テンプレートを使用して、Azure Automation アカウントのユーザー割り当てマネージド ID を追加できます。 関連する PowerShell の例として、最初に [Connect-AzAccount](/powershell/module/Az.Accounts/Connect-AzAccount) コマンドレットを使用して、Azure に対話的にサインインし、指示に従います。

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
$userAssignedOne = "userAssignedIdentityOne"
$userAssignedTwo = "userAssignedIdentityTwo"
```

### <a name="add-using-the-azure-portal"></a>Azure portal を使用して追加する

次の手順に従います。

1. [Azure portal](https://portal.azure.com) にサインインします。

1. Azure Portal で、Automation アカウントに移動します。

1.  **[アカウント設定]** で、 **[ID]** を選択します。

1. **[ユーザー割り当て]** タブを選択し、 **[追加]** を選択します。

1. 既存のユーザー割り当てマネージド ID を選択し、 **[追加]** を選択します。 **[ユーザー割り当て]** タブに自動的に戻ります。

   :::image type="content" source="media/add-user-assigned-identity/user-assigned-managed-identity.png" alt-text="ポータルからの出力。":::

### <a name="add-using-powershell"></a>PowerShell を使用して追加する

ユーザー割り当てマネージド ID を追加するには、PowerShell コマンドレット [Set-AzAutomationAccount](/powershell/module/az.automation/set-azautomationaccount) を使用します。 まず、既存のシステム割り当てマネージド ID があるかどうかを検討する必要があります。 次の例では、既存の Automation アカウントにユーザー割り当てマネージド ID を 2 つ追加します。システム割り当てマネージド ID が存在する場合は無効になります。

```powershell
$output = Set-AzAutomationAccount `
    -ResourceGroupName $resourceGroup `
    -Name $automationAccount `
    -AssignUserIdentity "/subscriptions/$subscriptionID/resourcegroups/$resourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/$userAssignedOne", `
        "/subscriptions/$subscriptionID/resourcegroups/$resourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/$userAssignedTwo"

$output
```

システム割り当てマネージド ID を保持するには、以下を使用します。

```powershell
$output = Set-AzAutomationAccount `
    -ResourceGroupName $resourceGroup `
    -Name $automationAccount `
    -AssignUserIdentity "/subscriptions/$subscriptionID/resourcegroups/$resourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/$userAssignedOne", `
        "/subscriptions/$subscriptionID/resourcegroups/$resourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/$userAssignedTwo" `
    -AssignSystemIdentity

$output
```

出力は次のようになります。

:::image type="content" source="media/add-user-assigned-identity/set-azautomationaccount-output.png" alt-text="Set-AzAutomationAccount コマンドからの出力。":::

その他の出力については、`$output.identity | ConvertTo-Json` を実行します。

### <a name="add-using-a-rest-api"></a>REST API を使用して追加する

構文と手順の例を次に示します。

#### <a name="syntax"></a>構文

次に示す本文の構文のサンプルを使用すると、システム割り当てマネージド ID がまだ有効になっていない場合は有効になり、既存の Automation アカウントに既存の 2 つのユーザー割り当てマネージド ID が割り当てられます。

PATCH

```json
{
  "identity": {
    "type": "SystemAssigned, UserAssigned",
    "userAssignedIdentities": {
      "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/resource-group-name/providers/Microsoft.ManagedIdentity/userAssignedIdentities/firstIdentity": {},
      "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/resource-group-name/providers/Microsoft.ManagedIdentity/userAssignedIdentities/secondIdentity": {}
    }
  }
}
```

API の構文は次のとおりです。

```http
https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/resource-group-name/providers/Microsoft.Automation/automationAccounts/automation-account-name?api-version=2020-01-13-preview 
```

#### <a name="example"></a>例

次の手順に従います。

1. 上記の本文の構文を、`body_ua.json` という名前のファイルに変更します。 ローカル コンピューターまたは Azure ストレージ アカウントにファイルを保存します。

1. 以下の変数値を変更してから実行します。

    ```powershell
    $file = "path\body_ua.json"
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
    "type": "SystemAssigned, UserAssigned",
    "principalId": "00000000-0000-0000-0000-000000000000",
    "tenantId": "00000000-0000-0000-0000-000000000000",
    "userAssignedIdentities":  {
        "/subscriptions/ContosoID/resourcegroups/ContosoLab/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ContosoUAMI1":  {
                "PrincipalId":  "00000000-0000-0000-0000-000000000000",
                "ClientId":  "00000000-0000-0000-0000-000000000000"
                    },
        "/subscriptions/ContosoID/resourcegroups/ContosoLab/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ContosoUAMI2":  {
                "PrincipalId":  "00000000-0000-0000-0000-000000000000",
                "ClientId":  "00000000-0000-0000-0000-000000000000"
                    }
        }
    }
    ```

### <a name="add-using-an-arm-template"></a>ARM テンプレートを使用して追加する

構文と手順の例を次に示します。

#### <a name="template-syntax"></a>テンプレート構文

次に示すテンプレートの構文のサンプルを使用すると、システム割り当てマネージド ID がまだ有効になっていない場合は有効になり、既存の Automation アカウントに既存の 2 つのユーザー割り当てマネージド ID が割り当てられます。

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "automationAccountName": {
     "defaultValue": "YourAutomationAccount",
      "type": "String",
      "metadata": {
        "description": "Automation account name"
      }
    },
    "userAssignedOne": {
     "defaultValue": "userAssignedOne",
      "type": "String",
      "metadata": {
        "description": "User-assigned managed identity"
      }
      },
    "userAssignedTwo": {
     "defaultValue": "userAssignedTwo",
      "type": "String",
      "metadata": {
        "description": "User-assigned managed identity"
      }
      }
   },
  "resources": [
    {
      "type": "Microsoft.Automation/automationAccounts",
      "apiVersion": "2020-01-13-preview",
      "name": "[parameters('automationAccountName')]",
      "location": "[resourceGroup().location]",
      "identity": {
        "type": "SystemAssigned, UserAssigned",
        "userAssignedIdentities": {
          "[resourceID('Microsoft.ManagedIdentity/userAssignedIdentities/',parameters('userAssignedOne'))]": {},
          "[resourceID('Microsoft.ManagedIdentity/userAssignedIdentities/',parameters('userAssignedTwo'))]": {}
        }
      },
      "properties": {
        "sku": {
          "name": "Basic"
        },
        "encryption": {
          "keySource": "Microsoft.Automation",
          "identity": {}
        }
      }
    }
  ]
}
```

#### <a name="example"></a>例

次の手順に従います。

1. テンプレートをコピーし、`template_ua.json` という名前のファイルに貼り付けます。 ローカル コンピューターまたは Azure ストレージ アカウントにファイルを保存します。

1. 以下の変数値を変更してから実行します。

    ```powershell
    $templateFile = "path\template_ua.json"
    ```

1. PowerShell コマンドレット [New-AzResourceGroupDeployment](/powershell/module/az.resources/new-azresourcegroupdeployment) を使用してテンプレートをデプロイします。

    ```powershell
    New-AzResourceGroupDeployment `
        -Name "UserAssignedDeployment" `
        -ResourceGroupName $resourceGroup `
        -TemplateFile $templateFile `
        -automationAccountName $automationAccount `
        -userAssignedOne $userAssignedOne `
        -userAssignedTwo $userAssignedTwo
    ```

   コマンドは出力を生成しません。ただし、次のコードを使用して確認できます。

    ```powershell
    (Get-AzAutomationAccount `
    -ResourceGroupName $resourceGroup `
    -Name $automationAccount).Identity | ConvertTo-Json
    ```

    出力は、上記の REST API の例に示されている出力のようになります。

## <a name="give-identity-access-to-azure-resources-by-obtaining-a-token"></a>トークンの取得によって ID が Azure リソースにアクセスできるようにする

Azure AD によって保護されている Azure Key Vault など他のリソースに Automation アカウントからアクセスするには、そのユーザー割り当てマネージド ID を使用してトークンを取得できます。 これらのトークンは、アプリケーションの特定のユーザーを表すものではありません。 代わりに、それらは、リソースにアクセスしているアプリケーションを表します。 たとえば、今回の場合、トークンは Automation アカウントを表します。

ユーザー割り当てマネージド ID を認証に使用するには、その ID を使用する予定の Azure リソースに対する ID のアクセスを先に設定します。 このタスクを完了するには、ターゲットの Azure リソース上でその ID に適切な役割を割り当てます。

最小特権の原則に従って、Runbook を実行するのに必要なアクセス許可のみを慎重に割り当てます。 たとえば、Automation アカウントが Azure VM の開始または停止を行うためにのみ必要な場合は、その実行アカウントまたはマネージド ID に割り当てられるアクセス許可は、VM の開始または停止のためのみのものである必要があります。 同様に、Runbook が BLOB ストレージから読み取りを行う場合は、読み取り専用アクセス許可を割り当てます。

この例では、Azure PowerShell を使用して、サブスクリプションの共同作成者ロールをターゲットの Azure リソースに割り当てる方法を示します。 共同作成者ロールは例として使用されており、実際には必要な場合も必要ない場合もあります。 代わりに、[Azure portal](../role-based-access-control/role-assignments-portal.md) で、対象の Azure リソースにロールを割り当てることもできます。

```powershell
New-AzRoleAssignment `
    -ObjectId <automation-Identity-object-id> `
    -Scope "/subscriptions/<subscription-id>" `
    -RoleDefinitionName "Contributor"
```

## <a name="authenticate-access-with-user-assigned-managed-identity"></a>ユーザー割り当てマネージド ID を使用してアクセスを認証する

Automation アカウントのユーザー割り当てマネージド ID を有効にし、ID がターゲット リソースにアクセスできるようにした後、マネージド ID をサポートするリソースに対して Runbook 内でその ID を指定できます。 ID をサポートするには、Az コマンドレットの [Connect-AzAccount](/powershell/module/az.accounts/Connect-AzAccount) を使用します。

```powershell
# Ensures you do not inherit an AzContext in your runbook
Disable-AzContextAutosave -Scope Process

# Connect to Azure with user-assigned managed identity
$AzureContext = (Connect-AzAccount -Identity -AccountId <user-assigned-identity-ClientId>).context

# set and store context
$AzureContext = Set-AzContext -SubscriptionName $AzureContext.Subscription -DefaultProfile $AzureContext
```

## <a name="generate-an-access-token-without-using-azure-cmdlets"></a>Azure コマンドレットを使用せずにアクセス トークンを生成する

HTTP エンドポイントに対して、次のことを確認してください。
- メタデータ ヘッダーが存在し、"true" に設定されている必要があります。
- リソースを GET 要求の場合はクエリ パラメーターとして、POST 要求の場合はフォーム データとして、要求と共に渡す必要があります。
- Post 要求のコンテンツ タイプは `application/x-www-form-urlencoded` である必要があります。

### <a name="get-access-token-for-user-assigned-managed-identity-using-http-get"></a>HTTP Get を使ってユーザー割り当てマネージド ID のアクセス トークンを取得する  

```powershell
$resource= "?resource=https://management.azure.com/"
$client_id="&client_id=<ClientId of USI>"
$url = $env:IDENTITY_ENDPOINT + $resource + $client_id 
$Headers = New-Object "System.Collections.Generic.Dictionary[[String],[String]]"  
$Headers.Add("Metadata", "True")
$accessToken = Invoke-RestMethod -Uri $url -Method 'GET' -Headers $Headers
Write-Output $accessToken.access_token 
```

### <a name="get-access-token-for-user-assigned-managed-identity-using-http-post"></a>HTTP Post を使ってユーザー割り当てマネージド ID のアクセス トークンを取得する

```powershell
$url = $env:IDENTITY_ENDPOINT
$headers = New-Object "System.Collections.Generic.Dictionary[[String],[String]]"
$headers.Add("Metadata", "True")
$body = @{'resource'='https://management.azure.com/' 
'client_id'='<ClientId of USI>'}
$accessToken = Invoke-RestMethod $url -Method 'POST' -Headers $headers -ContentType 'application/x-www-form-urlencoded' -Body $body
Write-Output $accessToken.access_token 
```

### <a name="using-user-assigned-managed-identity-in-azure-powershell"></a>Azure PowerShell でユーザー割り当てマネージド ID を使用する

```powershell
Write-Output "Connecting to azure via  Connect-AzAccount -Identity -AccountId <ClientId of USI>"  
Connect-AzAccount -Identity -AccountId <ClientId of USI> 
Write-Output "Successfully connected with Automation account's Managed Identity"  
Write-Output "Trying to fetch value from key vault using User Assigned Managed identity. Make sure you have given correct access to Managed Identity"  
$secret = Get-AzKeyVaultSecret -VaultName '<KVname>' -Name '<KeyName>'  
$ssPtr = [System.Runtime.InteropServices.Marshal]::SecureStringToBSTR($secret.SecretValue)  
try {  
  $secretValueText = [System.Runtime.InteropServices.Marshal]::PtrToStringBSTR($ssPtr)  
    Write-Output $secretValueText  
} finally {  
    [System.Runtime.InteropServices.Marshal]::ZeroFreeBSTR($ssPtr)  
} 
```

### <a name="using-user-assigned-managed-identity-in-python-runbook"></a>Python Runbook でユーザー割り当てマネージド ID を使用する

```python
#!/usr/bin/env python3  
import os  
import requests   

resource = "?resource=https://management.azure.com/" 
client_id = "&client_id=<ClientId of USI>" 
endPoint = os.getenv('IDENTITY_ENDPOINT')+ resource +client_id 
payload={}  
headers = {  
  'Metadata': 'True'  
}  
response = requests.request("GET", endPoint, headers=headers, data=payload)  
print(response.text) 
```

## <a name="next-steps"></a>次の手順

- Runbook が正常に完了しない場合は、「[Azure Automation マネージド ID に関する問題のトラブルシューティング](troubleshoot/managed-identity.md)」を参照してください。

- マネージド ID を無効にする必要がある場合は、「[Azure Automation アカウントのマネージド ID を無効にする](disable-managed-identity-for-automation.md)」を参照してください。

- Azure Automation アカウントのセキュリティの概要については、[Automation アカウントの認証の概要](automation-security-overview.md)に関する記事を参照してください。