---
title: Azure Automation アカウントのシステム割り当てマネージド ID を無効にする
description: この記事では、Azure Automation アカウントのシステム割り当てマネージド ID を無効にする方法について説明します。
services: automation
ms.subservice: process-automation
ms.date: 10/26/2021
ms.topic: conceptual
ms.openlocfilehash: b3bf648914635e6ea9345a234c49bfb64c757bc1
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/03/2021
ms.locfileid: "131435417"
---
# <a name="disable-system-assigned-managed-identity-for-azure-automation-account"></a>Azure Automation アカウントのシステム割り当てマネージド ID を無効にする

Azure portal または REST API を使用して、Azure Automation のシステム割り当てマネージド ID を無効にできます。

## <a name="disable-using-the-azure-portal"></a>Azure portal を使用して無効にする

システム割り当てマネージド ID が最初に設定された方法に関係なく、Azure portal からシステム割り当てマネージド ID を無効にすることができます。

1. [Azure portal](https://portal.azure.com) にサインインします。

1. 対象の Automation アカウントに移動し、 **[アカウント設定]** で **[ID]** を選択します。

1. **[システムが割り当て済み]** タブの **[状態]** ボタンで **[オフ]** を選択し、 **[保存]** を選択します。 確認を求めるメッセージが表示されたら、 **[はい]** を選択します。

システム割り当てマネージド ID が無効になり、ターゲット リソースにアクセスできなくなります。

## <a name="disable-using-rest-api"></a>REST API を使用して無効にする

構文と手順の例を次に示します。

### <a name="request-body"></a>要求本文

次の要求本文では、システム割り当てマネージド ID を無効にし、HTTP **PATCH** メソッドを使用してすべてのユーザー割り当てマネージド ID を削除します。

```json
{ 
 "identity": { 
   "type": "None" 
  } 
}

```

複数のユーザー割り当て ID が定義されている場合にそれらを保持し、システム割り当て ID のみを削除するには、コンマ区切りリストを使用してそれぞれのユーザー割り当て ID を指定する必要があります。 以下の例では、HTTP **PATCH** メソッドを使用しています。

```json
{ 
"identity" : {
    "type": "UserAssigned",
    "userAssignedIdentities": {
        "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/resourceGroupName/providers/Microsoft.ManagedIdentity/userAssignedIdentities/firstIdentity": {},
        "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/resourceGroupName/providers/Microsoft.ManagedIdentity/userAssignedIdentities/secondIdentity": {}
        }
    }
}
```

以下は、PATCH 要求を送信するためのサービスの REST API 要求 URI です。

```http
PATCH https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/resource-group-name/providers/Microsoft.Automation/automationAccounts/automation-account-name?api-version=2020-01-13-preview
```

### <a name="example"></a>例

次の手順に従います。

1. 実行する操作に応じて要求本文をコピーし、`body_remove_sa.json` という名前のファイルに貼り付けます。 ローカル コンピューターまたは Azure ストレージ アカウントにファイルを保存します。

1. [Connect-AzAccount](/powershell/module/Az.Accounts/Connect-AzAccount) コマンドレットを使用して、Azure に対話的にサインインし、指示に従います。

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

1. 変数に適切な値を指定し、スクリプトを実行します。

    ```powershell
    $subscriptionID = "subscriptionID"
    $resourceGroup = "resourceGroupName"
    $automationAccount = "automationAccountName"
    $file = "path\body_remove_sa.json"
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
    Invoke-RestMethod -Uri $URI -Method PATCH -Headers $authHeader -Body $body
    
    # Confirm removal
    (Get-AzAutomationAccount `
        -ResourceGroupName $resourceGroup `
        -Name $automationAccount).Identity.Type
    ```

    使用した構文に応じて、出力は `UserAssigned` または空白になります。

## <a name="next-steps"></a>次のステップ

- Azure Automation でのマネージド ID の有効化の詳細については、[Automation でのマネージド ID の有効化と使用](enable-managed-identity-for-automation.md)に関する記事を参照してください。

- Automation アカウントのセキュリティの概要については、[Automation アカウントの認証の概要](automation-security-overview.md)に関する記事を参照してください。