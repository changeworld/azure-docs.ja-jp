---
title: Azure Automation Runbook からメールを送信する
description: この記事では、Runbook 内からメールを送信する方法について説明します。
services: automation
ms.subservice: process-automation
ms.date: 09/21/2021
ms.topic: how-to
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: c5f89a755cecd2dcf36d3a8a41711fe941108ffa
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/03/2021
ms.locfileid: "131427268"
---
# <a name="send-an-email-from-am-automation-runbook"></a>Automation Runbook からメールを送信する

PowerShell を使用して、[SendGrid](https://sendgrid.com/solutions) によって Runbook からメールを送信できます。 

Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) を作成してください。

## <a name="prerequisites"></a>前提条件

* [SendGrid アカウント](https://docs.sendgrid.com/for-developers/partners/microsoft-azure-2021#create-a-sendgrid-account)。
* SendGrid 送信者の確認。 [ドメインまたは単一の送信者](https://sendgrid.com/docs/for-developers/sending-email/sender-identity/)のいずれか 
* [SendGrid API キー](https://docs.sendgrid.com/for-developers/partners/microsoft-azure-2021#to-find-your-sendgrid-api-key)。

* 少なくとも 1 つのユーザー割り当てマネージド ID を持つ Azure Automation アカウント。 詳細については、[マネージド ID の有効化](./quickstarts/enable-managed-identity.md)に関するページを参照してください。
* Az モジュール: `Az.Accounts` および `Az.KeyVault` が Automation アカウントにインポートされている。 詳細については、「[Az モジュールをインポートする](./shared-resources/modules.md#import-az-modules)」を参照してください。
* [Azure Az PowerShell モジュール](/powershell/azure/new-azureps-module-az)がマシンにインストールされている。 インストールまたはアップグレードするには、[Azure Az PowerShell モジュールをインストールする方法](/powershell/azure/install-az-ps)に関するページを参照してください。

## <a name="create-an-azure-key-vault"></a>Azure Key Vault を作成する

Azure Key Vault と、指定されたキー コンテナー内のキー コンテナー シークレットを資格情報で取得および設定できるようにする [Key Vault アクセス ポリシー](../key-vault/general/assign-access-policy-portal.md)を作成します。

1. [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) コマンドレットを使用して、Azure に対話的にサインインし、指示に従います。

    ```powershell
    # Sign in to your Azure subscription
    $sub = Get-AzSubscription -ErrorAction SilentlyContinue
    if(-not($sub))
    {
        Connect-AzAccount
    }
    
    # If you have multiple subscriptions, set the one to use
    # Select-AzSubscription -SubscriptionId <SUBSCRIPTIONID>
    ```

1. 下の変数に適切な値を指定し、スクリプトを実行します。

    ```powershell
    $resourceGroup = "<Resource group>"
    $automationAccount = "<Automation account>"
    $region = "<Region>"
    $SendGridAPIKey = "<SendGrid API key>"
    $VaultName = "<A universally unique vault name>"

    $userAssignedManagedIdentity = "<User-assigned managed identity>"
    ```

1. Key Vault を作成し、アクセス許可を割り当てます。

    ```powershell
    # Create the new key vault
    $newKeyVault = New-AzKeyVault `
        -VaultName $VaultName `
        -ResourceGroupName $resourceGroup `
        -Location $region

    $resourceId = $newKeyVault.ResourceId
    
    # Convert the SendGrid API key into a SecureString
    $Secret = ConvertTo-SecureString -String $SendGridAPIKey `
        -AsPlainText -Force

    Set-AzKeyVaultSecret -VaultName $VaultName `
        -Name 'SendGridAPIKey' `
        -SecretValue $Secret
    
    # Grant Key Vault access to the Automation account's system-assigned managed identity.
    $SA_PrincipalId = (Get-AzAutomationAccount `
        -ResourceGroupName $resourceGroup `
        -Name $automationAccount).Identity.PrincipalId

    Set-AzKeyVaultAccessPolicy `
        -VaultName $vaultName `
        -ObjectId $SA_PrincipalId `
        -PermissionsToSecrets Set, Get

    # Grant Key Vault access to the user-assigned managed identity.
    $UAMI = Get-AzUserAssignedIdentity `
        -ResourceGroupName $resourceGroup `
        -Name $userAssignedManagedIdentity

    Set-AzKeyVaultAccessPolicy `
        -VaultName $vaultName `
        -ObjectId $UAMI.PrincipalId `
        -PermissionsToSecrets Set, Get
    ```

   Azure Key Vault を作成してシークレットを格納するその他の方法については、[Key Vault のクイックスタート](../key-vault/index.yml)を参照してください。

## <a name="assign-permissions-to-managed-identities"></a>マネージド ID にアクセス許可を割り当てる

適切な[マネージド ID](./automation-security-overview.md#managed-identities) にアクセス許可を割り当てます。 Runbook では、Automation アカウントのシステム割り当てマネージド ID またはユーザー割り当てマネージド ID のいずれかを使用できます。 以下に、各 ID にアクセス許可を割り当てる手順を示します。 この手順では PowerShell を使用します。 ポータルを使用する場合は、「[Azure portal を使用して Azure ロールを割り当てる](./../role-based-access-control/role-assignments-portal.md)」を参照してください。

1. PowerShell コマンドレット [New-AzRoleAssignment](/powershell/module/az.resources/new-azroleassignment) を使用して、システム割り当てマネージド ID にロールを割り当てます。

    ```powershell
    New-AzRoleAssignment `
        -ObjectId $SA_PrincipalId `
        -ResourceGroupName $resourceGroup `
        -RoleDefinitionName "Reader"
    ```

1. ユーザー割り当てマネージド ID にロールを割り当てます。

    ```powershell
    New-AzRoleAssignment `
        -ObjectId $UAMI.PrincipalId`
        -ResourceGroupName $resourceGroup `
        -RoleDefinitionName "Reader"
    ```

1. システム割り当てマネージド ID の場合は、`ClientId` を表示し、後で使用するためにその値を記録します。

   ```powershell
   $UAMI.ClientId
   ```

## <a name="create-the-runbook-to-send-an-email"></a>Runbook を作成してメールを送信する

Key Vault を作成し、`SendGrid` API キーを格納したら、次に、API キーを取得してメールを送信する Runbook を作成します。 Azure Key Vault からシークレットを取得するために、[システム割り当てマネージド ID](./automation-security-overview.md#managed-identities) を使用して Azure に対して認証する Runbook を使用しましょう。 **Send-GridMailMessage** Runbook を呼び出します。 さまざまなシナリオに使用される PowerShell スクリプトを変更できます。

1. [Azure portal](https://portal.azure.com) にサインインし、お使いの Automation アカウントに移動します。

1. 開いている [Automation アカウント] ページの **[プロセス オートメーション]** で、 **[Runbook]** を選択します。

1. **[+ Runbook の作成]** を選択します。
    1. Runbook に `Send-GridMailMessage` という名前を付けます。
    1. **[Runbook の種類]** ドロップダウン リストから、 **[PowerShell]** を選択します。
    1. **［作成］** を選択します

   ![Runbook を作成する](./media/automation-send-email/automation-send-email-runbook.png)

1. Runbook が作成され、[PowerShell Runbook の編集] ページが開きます。
   ![Runbook を編集する](./media/automation-send-email/automation-send-email-edit.png)

1. [編集] ページに、次の PowerShell の例をコピーします。 `VaultName` には、Key Vault に対して選択した名前が指定されていることを確認します。

    ```powershell
    Param(
      [Parameter(Mandatory=$True)]
      [String] $destEmailAddress,
      [Parameter(Mandatory=$True)]
      [String] $fromEmailAddress,
      [Parameter(Mandatory=$True)]
      [String] $subject,
      [Parameter(Mandatory=$True)]
      [String] $content,
      [Parameter(Mandatory=$True)]
      [String] $ResourceGroupName
    )

    # Ensures you do not inherit an AzContext in your runbook
    Disable-AzContextAutosave -Scope Process
    
    # Connect to Azure with system-assigned managed identity
    $AzureContext = (Connect-AzAccount -Identity).context

    # set and store context
    $AzureContext = Set-AzContext -SubscriptionName $AzureContext.Subscription -DefaultProfile $AzureContext 

    $VaultName = "<Enter your vault name>"

    $SENDGRID_API_KEY = Get-AzKeyVaultSecret `
        -VaultName $VaultName `
        -Name "SendGridAPIKey" `
        -AsPlainText -DefaultProfile $AzureContext

    $headers = New-Object "System.Collections.Generic.Dictionary[[String],[String]]"
    $headers.Add("Authorization", "Bearer " + $SENDGRID_API_KEY)
    $headers.Add("Content-Type", "application/json")

    $body = @{
    personalizations = @(
        @{
            to = @(
                    @{
                        email = $destEmailAddress
                    }
            )
        }
    )
    from = @{
        email = $fromEmailAddress
    }
    subject = $subject
    content = @(
        @{
            type = "text/plain"
            value = $content
        }
    )
    }

    $bodyJson = $body | ConvertTo-Json -Depth 4

    $response = Invoke-RestMethod -Uri https://api.sendgrid.com/v3/mail/send -Method Post -Headers $headers -Body $bodyJson
    ```

1. Runbook をシステム割り当てマネージド ID で実行する場合は、コードをそのままにしておきます。 ユーザー割り当てマネージド ID を使用する場合は、次のようにします。
    1. 18 行目から、`$AzureContext = (Connect-AzAccount -Identity).context` を削除し、
    1. それを `$AzureContext = (Connect-AzAccount -Identity -AccountId <ClientId>).context` に置き換えた後、
    1. 前に取得したクライアント ID を入力します。

1. **[保存]** 、 **[発行]** の順に選択し、確認を求められたら **[はい]** を選択します。

Runbook が正常に実行されることを確認するには、「[Runbook をテストする](manage-runbooks.md#test-a-runbook)」または [Runbook の開始](start-runbooks.md)に関するページの手順に従います。

最初にテスト用のメールが表示されない場合は、**迷惑メール** および **スパム** のフォルダーを確認してください。

## <a name="clean-up-resources"></a>リソースをクリーンアップする

1. Runbook が必要なくなったら、それを Runbook の一覧で選択し、 **[削除]** を選択します。

1. キー コンテナーを削除するには、[Remove-AzKeyVault](/powershell/module/az.keyvault/remove-azkeyvault) コマンドレットを使用します。

    ```powershell
    $VaultName = "<your KeyVault name>"
    $resourceGroup = "<your ResourceGroup name>"
    Remove-AzKeyVault -VaultName $VaultName -ResourceGroupName $resourceGroup
    ```

## <a name="next-steps"></a>次のステップ

* Runbook ジョブのデータを Log Analytics ワークスペースに送信するには、「[Azure Monitor ログに Azure Automation のジョブ データを転送する](automation-manage-send-joblogs-log-analytics.md)」を参照してください。
* 基本レベルのメトリックとログを監視するには、「[Azure Automation Runbook をトリガーするアラートを使用する](automation-create-alert-triggered-runbook.md)」を参照してください。
