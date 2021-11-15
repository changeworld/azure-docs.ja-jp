---
title: Azure Automation アカウントのユーザー割り当てマネージド ID を削除する
description: この記事では、Azure Automation アカウントのユーザー割り当てマネージド ID を削除する方法について説明します。
services: automation
ms.subservice: process-automation
ms.date: 10/26/2021
ms.topic: conceptual
ms.openlocfilehash: 11db735a21e4d4007c35f1a82f1e5dfadb377fd1
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/03/2021
ms.locfileid: "131432455"
---
# <a name="remove-user-assigned-managed-identity-for-azure-automation-account"></a>Azure Automation アカウントのユーザー割り当てマネージド ID を削除する

Azure portal、PowerShell、Azure REST API、または Azure Resource Manager (ARM) テンプレートを使用して、Azure Automation のユーザー割り当てマネージド ID を削除できます。

## <a name="remove-using-the-azure-portal"></a>Azure portal を使用して削除する

ユーザー割り当てマネージド ID が最初に追加された方法に関係なく、Azure portal からユーザー割り当てマネージド ID を削除できます。

1. [Azure portal](https://portal.azure.com) にサインインします。

1. 対象の Automation アカウントに移動し、 **[アカウント設定]** で **[ID]** を選択します。

1. **[ユーザー割り当て済み]** タブを選択します。

1. 削除するユーザー割り当てマネージド ID を一覧から選択します。

1. **[削除]** を選択します。 確認を求めるメッセージが表示されたら、 **[はい]** を選択します。

ユーザー割り当てマネージド ID が削除され、ターゲット リソースにアクセスできなくなります。

## <a name="remove-using-powershell"></a>PowerShell を使用して削除する

PowerShell コマンドレット [Set-AzAutomationAccount](/powershell/module/az.automation/set-azautomationaccount) を使用して、すべてのユーザー割り当てマネージド ID を削除する一方で、既存のシステム割り当てマネージド ID を保持します。

1. [Connect-AzAccount](/powershell/module/Az.Accounts/Connect-AzAccount) コマンドレットを使用して、Azure に対話的にサインインし、指示に従います。

    ```powershell
    # Sign in to your Azure subscription
    $sub = Get-AzSubscription -ErrorAction SilentlyContinue
    if(-not($sub))
    {
        Connect-AzAccount
    }
    ```

1. 変数に適切な値を指定し、スクリプトを実行します。

    ```powershell
    $resourceGroup = "resourceGroupName"
    $automationAccount = "automationAccountName"
    ```

1. [Set-AzAutomationAccount](/powershell/module/az.automation/set-azautomationaccount) を実行します。

    ```powershell
    # Removes all UAs, keeps SA
    $output = Set-AzAutomationAccount `
        -ResourceGroupName $resourceGroup `
        -Name $automationAccount `
        -AssignSystemIdentity 
    
    $output.identity.Type
    ```

    出力は `SystemAssigned` になります。

## <a name="remove-using-rest-api"></a>REST API を使用して削除する

次の REST API 呼び出しと例を使用して、Automation アカウントからユーザー割り当てマネージド ID を削除できます。

### <a name="request-body"></a>要求本文

シナリオ: システム割り当てマネージド ID が有効になっているか、有効に設定されます。 多数のユーザー割り当てマネージド ID の 1 つが削除されます。 この例では、HTTP **PATCH** メソッドを使用して、`firstIdentity` という名前のユーザー割り当てマネージド ID を削除します。

```json
{
  "identity": {
    "type": "SystemAssigned, UserAssigned",
    "userAssignedIdentities": {
      "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/resource-group-name/providers/Microsoft.ManagedIdentity/userAssignedIdentities/firstIdentity": null
    }
  }
}
```

シナリオ: システム割り当てマネージド ID が有効になっているか、有効に設定されます。 すべてのユーザー割り当てマネージド ID が HTTP **PUT** メソッドを使用して削除されます。

```json
{
  "identity": {
    "type": "SystemAssigned"
  }
}
```

シナリオ: システム割り当てマネージド ID が無効になっているか、無効に設定されます。 多数のユーザー割り当てマネージド ID の 1 つが削除されます。 この例では、HTTP **PATCH** メソッドを使用して、`firstIdentity` という名前のユーザー割り当てマネージド ID を削除します。

```json
{
  "identity": {
    "type": "UserAssigned",
    "userAssignedIdentities": {
      "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/resource-group-name/providers/Microsoft.ManagedIdentity/userAssignedIdentities/firstIdentity": null
    }
  }
}

```

シナリオ: システム割り当てマネージド ID が無効になっているか、無効に設定されます。 すべてのユーザー割り当てマネージド ID が HTTP **PUT** メソッドを使用して削除されます。

```json
{
  "identity": {
    "type": "None"
  }
}
```

以下は、PATCH 要求を送信するためのサービスの REST API 要求 URI です。

```http
https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/resource-group-name/providers/Microsoft.Automation/automationAccounts/automation-account-name?api-version=2020-01-13-preview
```

### <a name="example"></a>例

次の手順に従います。

1. 実行する操作に応じて要求本文をコピーし、`body_remove_ua.json` という名前のファイルに貼り付けます。 必要な変更を加えて、ローカル コンピューターまたは Azure ストレージ アカウントにファイルを保存します。

1. [Connect-AzAccount](/powershell/module/Az.Accounts/Connect-AzAccount) コマンドレットを使用して、Azure に対話的にサインインし、指示に従います。

    ```powershell
    # Sign in to your Azure subscription
    $sub = Get-AzSubscription -ErrorAction SilentlyContinue
    if(-not($sub))
    {
        Connect-AzAccount -Subscription
    }
    ```

1. 変数に適切な値を指定し、スクリプトを実行します。

    ```powershell
    $subscriptionID = "subscriptionID"
    $resourceGroup = "resourceGroupName"
    $automationAccount = "automationAccountName"
    $file = "path\body_remove_ua.json"
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

   使用した構文に応じて、出力は `SystemAssignedUserAssigned`、`SystemAssigned`、`UserAssigned`、または空白になります。

## <a name="remove-using-azure-resource-manager-template"></a>Azure Resource Manager テンプレートを使用して削除する

Azure Resource Manager テンプレートを使用して Automation アカウントにユーザー割り当てマネージド ID を追加した場合は、テンプレートを変更して再実行することによって、そのユーザー割り当てマネージド ID を削除できます。

シナリオ: システム割り当てマネージド ID が有効になっているか、有効に設定されます。 2 つのユーザー割り当てマネージド ID の 1 つが削除されます。 この構文スニペットでは、テンプレートにパラメーターとして渡されたものを **除いて**、**すべての** ユーザー割り当てマネージド ID を削除します。

```json
...
"identity": {
    "type": "SystemAssigned, UserAssigned",
    "userAssignedIdentities": {
        "[resourceID('Microsoft.ManagedIdentity/userAssignedIdentities/',parameters('userAssignedOne'))]": {}
    }
},
...
```

シナリオ: システム割り当てマネージド ID が有効になっているか、有効に設定されます。 すべてのユーザー割り当てマネージド ID が削除されます。

```json
...
"identity": {
    "type": "SystemAssigned"
},
...
```

シナリオ: システム割り当てマネージド ID が無効になっているか、無効に設定されます。 2 つのユーザー割り当てマネージド ID の 1 つが削除されます。 この構文スニペットでは、テンプレートにパラメーターとして渡されたものを **除いて**、**すべての** ユーザー割り当てマネージド ID を削除します。

```json
...
"identity": {
    "type": "UserAssigned",
    "userAssignedIdentities": {
        "[resourceID('Microsoft.ManagedIdentity/userAssignedIdentities/',parameters('userAssignedOne'))]": {}
    }
},
...
```

[Get-AzAutomationAccount](/powershell/module/az.automation/get-azautomationaccount) コマンドレットを使用して確認します。 使用した構文に応じて、出力は `SystemAssignedUserAssigned`、`SystemAssigned`、または `UserAssigned` になります。

```powershell
(Get-AzAutomationAccount `
    -ResourceGroupName $resourceGroup `
    -Name $automationAccount).Identity.Type
```

## <a name="next-steps"></a>次のステップ

- Azure Automation でのマネージド ID の有効化の詳細については、[Automation でのマネージド ID の有効化と使用](enable-managed-identity-for-automation.md)に関する記事を参照してください。

- Automation アカウントのセキュリティの概要については、[Automation アカウントの認証の概要](automation-security-overview.md)に関する記事を参照してください。
