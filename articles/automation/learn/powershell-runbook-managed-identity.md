---
title: Azure Automation でマネージド ID を使用して PowerShelll Runbook を作成する
description: このチュートリアルでは、Azure Automation の PowerShell Runbook でマネージド ID を使用する方法について説明します。
services: automation
ms.subservice: process-automation
ms.date: 09/28/2021
ms.topic: tutorial
ms.openlocfilehash: 31f9e30d938f70852eac5c5185626828e5305897
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/03/2021
ms.locfileid: "131427173"
---
# <a name="tutorial-create-automation-powershell-runbook-using-managed-identity"></a>チュートリアル: マネージド ID を使用して Automation PowerShell Runbook を作成する

このチュートリアルでは、Azure Automation で [PowerShell Runbook](../automation-runbook-types.md#powershell-runbooks) を作成し、リソースの操作に実行アカウントではなく、[マネージド ID](../automation-security-overview.md#managed-identities) を使用する方法を説明します。 PowerShell Runbook は、Windows PowerShell に基づきます。 Azure Active Directory (Azure AD) のマネージド ID を使用すると、Runbook が Azure AD で保護された他のリソースに簡単にアクセスできます。

このチュートリアルでは、以下の内容を学習します。

> [!div class="checklist"]
> * マネージド ID にアクセス許可を割り当てる
> * PowerShell Runbook を作成する

Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) を作成してください。

## <a name="prerequisites"></a>前提条件

* 少なくとも 1 つのユーザー割り当てマネージド ID を持つ Azure Automation アカウント。 詳細については、「[Azure Automation アカウントのユーザー割り当てマネージド ID を使用する](../add-user-assigned-identity.md)」を参照してください。
* Az モジュール: `Az.Accounts`、`Az.Automation`、`Az.ManagedServiceIdentity`、および `Az.Compute` が Automation アカウントにインポートされている。 詳細については、「[Az モジュールをインポートする](../shared-resources/modules.md#import-az-modules)」を参照してください。
* [Azure Az PowerShell モジュール](/powershell/azure/new-azureps-module-az)がマシンにインストールされている。 インストールまたはアップグレードするには、[Azure Az PowerShell モジュールをインストールする方法](/powershell/azure/install-az-ps)に関するページを参照してください。 `Az.ManagedServiceIdentity` はプレビュー モジュールであり、Az モジュールの一部としてインストールされません。 インストールするには、`Install-Module -Name Az.ManagedServiceIdentity` を実行します。
* [Azure 仮想マシン](../../virtual-machines/windows/quick-create-powershell.md)。 マシンを停止して起動するので、運用 VM は使用しないでください。
* [Automation Runbook](../manage-runbooks.md) に関する一般的な理解。

## <a name="assign-permissions-to-managed-identities"></a>マネージド ID にアクセス許可を割り当てる

マネージド ID に、仮想マシンの停止と起動を許可するアクセス許可を割り当てます。

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
    $resourceGroup = "resourceGroupName"
    
    # These values are used in this tutorial
    $automationAccount = "xAutomationAccount"
    $userAssignedManagedIdentity = "xUAMI"
    ```

1. PowerShell コマンドレット [New-AzRoleAssignment](/powershell/module/az.resources/new-azroleassignment) を使用して、システム割り当てマネージド ID にロールを割り当てます。

    ```powershell
    $role1 = "DevTest Labs User"
    
    $SAMI = (Get-AzAutomationAccount -ResourceGroupName $resourceGroup -Name $automationAccount).Identity.PrincipalId
    New-AzRoleAssignment `
        -ObjectId $SAMI `
        -ResourceGroupName $resourceGroup `
        -RoleDefinitionName $role1
    ```

1. ユーザー割り当てマネージド ID にも同じロールの割り当てが必要です

    ```powershell
    $UAMI = (Get-AzUserAssignedIdentity -ResourceGroupName $resourceGroup -Name $userAssignedManagedIdentity).PrincipalId
    New-AzRoleAssignment `
        -ObjectId $UAMI `
        -ResourceGroupName $resourceGroup `
        -RoleDefinitionName $role1
    ```

1. このチュートリアルで使用するコマンドレット `Get-AzUserAssignedIdentity` と `Get-AzAutomationAccount` を実行するには、システム割り当てマネージド ID に追加のアクセス許可が必要です。

    ```powershell
    $role2 = "Reader"
    New-AzRoleAssignment `
        -ObjectId $SAMI `
        -ResourceGroupName $resourceGroup `
        -RoleDefinitionName $role2
    ```

## <a name="create-powershell-runbook"></a>初めての PowerShell Runbook

どちらのマネージド ID でも実行可能な Runbook を作成します。 この Runbook は、停止した VM を起動したり、実行中の VM を停止したりします。

1. [Azure portal](https://portal.azure.com/) にサインインし、お使いの Automation アカウントに移動します。

1. **[プロセス オートメーション]** の **[Runbook]** を選択します。

1. **[Runbook の作成]** を選択します。
    1. Runbook に `miTesting` という名前を付けます。
    1. **[Runbook の種類]** ドロップダウン メニューから、 **[PowerShell]** を選択します。
    1. **［作成］** を選択します

1. Runbook エディターで、次のコードを貼り付けます。

    ```powershell
    Param(
     [string]$resourceGroup,
     [string]$VMName,
     [string]$method,
     [string]$UAMI 
    )
    
    $automationAccount = "xAutomationAccount"
    
    # Ensures you do not inherit an AzContext in your runbook
    Disable-AzContextAutosave -Scope Process | Out-Null
    
    # Connect using a Managed Service Identity
    try {
            $AzureContext = (Connect-AzAccount -Identity).context
        }
    catch{
            Write-Output "There is no system-assigned user identity. Aborting."; 
            exit
        }
    
    # set and store context
    $AzureContext = Set-AzContext -SubscriptionName $AzureContext.Subscription `
        -DefaultProfile $AzureContext
    
    if ($method -eq "SA")
        {
            Write-Output "Using system-assigned managed identity"
        }
    elseif ($method -eq "UA")
        {
            Write-Output "Using user-assigned managed identity"
    
            # Connects using the Managed Service Identity of the named user-assigned managed identity
            $identity = Get-AzUserAssignedIdentity -ResourceGroupName $resourceGroup `
                -Name $UAMI -DefaultProfile $AzureContext
    
            # validates assignment only, not perms
            if ((Get-AzAutomationAccount -ResourceGroupName $resourceGroup `
                    -Name $automationAccount `
                    -DefaultProfile $AzureContext).Identity.UserAssignedIdentities.Values.PrincipalId.Contains($identity.PrincipalId))
                {
                    $AzureContext = (Connect-AzAccount -Identity -AccountId $identity.ClientId).context
    
                    # set and store context
                    $AzureContext = Set-AzContext -SubscriptionName $AzureContext.Subscription -DefaultProfile $AzureContext
                }
            else {
                    Write-Output "Invalid or unassigned user-assigned managed identity"
                    exit
                }
        }
    else {
            Write-Output "Invalid method. Choose UA or SA."
            exit
         }
    
    # Get current state of VM
    $status = (Get-AzVM -ResourceGroupName $resourceGroup -Name $VMName `
        -Status -DefaultProfile $AzureContext).Statuses[1].Code
    
    Write-Output "`r`n Beginning VM status: $status `r`n"
    
    # Start or stop VM based on current state
    if($status -eq "Powerstate/deallocated")
        {
            Start-AzVM -Name $VMName -ResourceGroupName $resourceGroup -DefaultProfile $AzureContext
        }
    elseif ($status -eq "Powerstate/running")
        {
            Stop-AzVM -Name $VMName -ResourceGroupName $resourceGroup -DefaultProfile $AzureContext -Force
        }
    
    # Get new state of VM
    $status = (Get-AzVM -ResourceGroupName $resourceGroup -Name $VMName -Status `
        -DefaultProfile $AzureContext).Statuses[1].Code  
    
    Write-Output "`r`n Ending VM status: $status `r`n `r`n"
    
    Write-Output "Account ID of current context: " $AzureContext.Account.Id
    ```

1. エディターの 8 行目で、必要に応じて `$automationAccount` 変数の値を変更します。

1. **[保存]** 、 **[テスト] ペイン** の順に選択します。

1. パラメーター `RESOURCEGROUP` と `VMNAME` に適切な値を設定します。 `METHOD` パラメーターに `SA` を、`UAMI` パラメーターに `xUAMI` を入力します。 この Runbook は、システム割り当てマネージド ID を使用して VM の電源状態の変更を試行します。

1. **[スタート]** を選択します。 この Runbook が完了すると、出力は次のようになります。

    ```output
     Beginning VM status: PowerState/deallocated
    
    OperationId : 5b707401-f415-4268-9b43-be1f73ddc54b
    Status      : Succeeded
    StartTime   : 8/3/2021 10:52:09 PM
    EndTime     : 8/3/2021 10:52:50 PM
    Error       : 
    Name        : 
    
     Ending VM status: PowerState/running 
     
    Account ID of current context: 
    MSI@50342
    ```

1. `METHOD` パラメーターの値を `UA` に変更します。

1. **[スタート]** を選択します。 Runbook は、ユーザー割り当てマネージド ID を使用して VM の電源状態の変更を試行します。 この Runbook が完了すると、出力は次のようになります。

    ```output
    Using user-assigned managed identity
    
     Beginning VM status: PowerState/running 
    
    OperationId : 679fcadf-d0b9-406a-9282-66bc211a9fbf
    Status      : Succeeded
    StartTime   : 8/3/2021 11:06:03 PM
    EndTime     : 8/3/2021 11:06:49 PM
    Error       : 
    Name        : 
    
     Ending VM status: PowerState/deallocated 
     
    Account ID of current context: 
    9034f5d3-c46d-44d4-afd6-c78aeab837ea
    ```

## <a name="clean-up-resources"></a>リソースをクリーンアップする

不要になったリソースを削除するには、次の Runbook を実行します。

```powershell
#Remove runbook
Remove-AzAutomationRunbook `
    -ResourceGroupName $resourceGroup `
    -AutomationAccountName $automationAccount `
    -Name "miTesting" `
    -Force

# Remove role assignments
Remove-AzRoleAssignment `
    -ObjectId $UAMI `
    -ResourceGroupName $resourceGroup `
    -RoleDefinitionName $role1

Remove-AzRoleAssignment `
    -ObjectId $SAMI `
    -ResourceGroupName $resourceGroup `
    -RoleDefinitionName $role2

Remove-AzRoleAssignment `
    -ObjectId $SAMI `
    -ResourceGroupName $resourceGroup `
    -RoleDefinitionName $role1
```

## <a name="next-steps"></a>次の手順

このチュートリアルでは、Azure Automation で [PowerShell Runbook](../automation-runbook-types.md#powershell-runbooks) を作成し、リソースの操作に実行アカウントではなく、[マネージド ID](../automation-security-overview.md#managed-identities) を使用しました。 PowerShell ワークフロー Runbook については、以下を参照してください。

> [!div class="nextstepaction"]
> [チュートリアル:PowerShell Workflow Runbook を作成する](automation-tutorial-runbook-textual.md)