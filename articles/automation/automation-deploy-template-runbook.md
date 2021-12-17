---
title: Azure Automation PowerShell Runbook に Azure Resource Manager テンプレートをデプロイする
description: この記事では、Azure Storage に格納されている Azure Resource Manager テンプレートを PowerShell Runbook からデプロイする方法について説明します。
services: automation
ms.subservice: process-automation
ms.date: 09/23/2021
ms.topic: how-to
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 8dd1c0269b9211900a5532e7a0d9e7d119830f68
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/22/2021
ms.locfileid: "130265633"
---
# <a name="deploy-an-azure-resource-manager-template-in-an-automation-powershell-runbook"></a>Automation PowerShell Runbook に Azure Resource Manager テンプレートをデプロイする

[Azure Resource Manager テンプレート](../azure-resource-manager/templates/quickstart-create-templates-use-the-portal.md)を使用して、Azure リソースをデプロイする [Automation PowerShell Runbook](./learn/powershell-runbook-managed-identity.md) を記述できます。 テンプレートを使用すると、Azure Automation を使用して Azure リソースのデプロイを自動化できます。 Azure Storage など、セキュリティで保護された一元的な場所に Resource Manager テンプレートを維持することができます。

この記事では、[Azure Storage](../storage/common/storage-introduction.md) に格納されている Resource Manager テンプレートを使用して新しい Azure ストレージ アカウントをデプロイする PowerShell Runbook を作成します。

Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) を作成してください。

## <a name="prerequisites"></a>前提条件

* 少なくとも 1 つのユーザー割り当てマネージド ID を持つ Azure Automation アカウント。 詳細については、「[Azure Automation アカウントのユーザー割り当てマネージド ID を使用する](./add-user-assigned-identity.md)」を参照してください。

* Az モジュール: `Az.Accounts`、`Az.ManagedServiceIdentity`、`Az.Resources`、`Az.Storage` (Automation アカウントにインポート済み)。 詳細については、「[Az モジュールをインポートする](./shared-resources/modules.md#import-az-modules)」を参照してください。

* Resource Manager テンプレートを格納する [Azure Storage アカウント](../storage/common/storage-account-create.md)。

* ローカル コンピューターにインストールされている Azure PowerShell。 Azure PowerShell の取得方法の詳細については、[Azure PowerShell モジュールのインストール](/powershell/azure/install-az-ps)に関するページを参照してください。 モジュール [Az.ManagedServiceIdentity](/powershell/module/az.managedserviceidentity) も必要になります。 `Az.ManagedServiceIdentity` はプレビュー モジュールであり、Az モジュールの一部としてインストールされません。 インストールするには、`Install-Module -Name Az.ManagedServiceIdentity` を実行します

## <a name="assign-permissions-to-managed-identities"></a>マネージド ID にアクセス許可を割り当てる

マネージド ID にアクセス許可を割り当て、Runbook でストレージ関連のタスクを実行します。

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
    $resourceGroup = "resourceGroup"
    $automationAccount = "automationAccount"
    $storageAccount = "storageAccount"
    $userAssignedManagedIdentity = "userAssignedManagedIdentity"
    $storageTemplate = "path\storageTemplate.json"
    $runbookScript = "path\runbookScript.ps1"
    ```

1. システム割り当てのマネージド ID にロール `reader` を割り当て、コマンドレット `Get-AzUserAssignedIdentity` を実行します。

    ```powershell
    $SAMI = (Get-AzAutomationAccount -ResourceGroupName $resourceGroup -Name $automationAccount).Identity.PrincipalId
    New-AzRoleAssignment `
        -ObjectId $SAMI `
        -ResourceGroupName $resourceGroup `
        -RoleDefinitionName "Reader"
    ```

1. ストレージ アカウントに対するアクションのために、ユーザー割り当てマネージド ID にロール `Storage Account Contributor` を割り当てます。

    ```powershell
    $UAMI_ID = (Get-AzUserAssignedIdentity -ResourceGroupName $resourceGroup -Name $userAssignedManagedIdentity).PrincipalId
    New-AzRoleAssignment `
        -ObjectId $UAMI_ID `
        -ResourceGroupName $resourceGroup `
        -RoleDefinitionName "Storage Account Contributor"
    ```


## <a name="create-the-resource-manager-template"></a>Resource Manager テンプレートの作成

この例では、新しい Azure ストレージ アカウントをデプロイする Resource Manager テンプレートを使用します。 `storageTemplate.json` という名前のローカル ファイルを作成し、次のコードを貼り付けます。

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "storageAccountType": {
      "type": "string",
      "defaultValue": "Standard_LRS",
      "allowedValues": [
        "Standard_LRS",
        "Standard_GRS",
        "Standard_ZRS",
        "Premium_LRS"
      ],
      "metadata": {
        "description": "Storage Account type"
      }
    },
    "location": {
      "type": "string",
      "defaultValue": "[resourceGroup().location]",
      "metadata": {
        "description": "Location for all resources."
      }
    }
  },
  "variables": {
    "storageAccountName": "[concat(uniquestring(resourceGroup().id), 'standardsa')]"
  },
  "resources": [
    {
      "type": "Microsoft.Storage/storageAccounts",
      "name": "[variables('storageAccountName')]",
      "apiVersion": "2018-02-01",
      "location": "[parameters('location')]",
      "sku": {
          "name": "[parameters('storageAccountType')]"
      },
      "kind": "Storage", 
      "properties": {
      }
    }
  ],
  "outputs": {
      "storageAccountName": {
          "type": "string",
          "value": "[variables('storageAccountName')]"
      }
  }
}
```

## <a name="save-the-resource-manager-template-in-azure-files"></a>Resource Manager テンプレートを Azure Files に保存する

PowerShell を使用して Azure ファイル共有を作成し、`storageTemplate.json` をアップロードします。 Azure portal でファイル共有を作成し、ファイルをアップロードする方法については、[Windows での Azure Files の概要](../storage/files/storage-files-quick-create-use-windows.md)に関する記事をご覧ください。

次のコマンドを実行して、ファイル共有を作成し、そのファイル共有に Resource Manager テンプレートをアップロードします。

```powershell
# Get the access key for your storage account
$key = Get-AzStorageAccountKey -ResourceGroupName $resourceGroup -Name $storageAccount

# Create an Azure Storage context using the first access key
$context = New-AzStorageContext -StorageAccountName $storageAccount -StorageAccountKey $key[0].value

# Create a file share named 'resource-templates' in your Azure Storage account
$fileShare = New-AzStorageShare -Name 'resource-templates' -Context $context

# Add the storageTemplate.json file to the new file share
Set-AzStorageFileContent -ShareName $fileShare.Name -Context $context -Source $storageTemplate
```

## <a name="create-the-powershell-runbook-script"></a>PowerShell Runbook スクリプトの作成

`storageTemplate.json` ファイルを Azure Storage から取得し、新しい Azure Storage アカウントを作成するテンプレートをデプロイする PowerShell スクリプトを作成します。  `runbookScript.ps1` という名前のローカル ファイルを作成し、次のコードを貼り付けます。


```powershell
param (
    [Parameter(Mandatory=$true)]
    [string]
    $resourceGroup,

    [Parameter(Mandatory=$true)]
    [string]
    $storageAccount,

    [Parameter(Mandatory=$true)]
    [string]
    $storageAccountKey,

    [Parameter(Mandatory=$true)]
    [string]
    $storageFileName,

    [Parameter(Mandatory=$true)]
    [string]
    $userAssignedManagedIdentity
)

# Ensures you do not inherit an AzContext in your runbook
Disable-AzContextAutosave -Scope Process

# Connect to Azure with user-assigned managed identity
$AzureContext = (Connect-AzAccount -Identity).context
$identity = Get-AzUserAssignedIdentity -ResourceGroupName $resourceGroup `
    -Name $userAssignedManagedIdentity `
    -DefaultProfile $AzureContext
$AzureContext = (Connect-AzAccount -Identity -AccountId $identity.ClientId).context

# set and store context
$AzureContext = Set-AzContext -SubscriptionName $AzureContext.Subscription `
    -DefaultProfile $AzureContext

#Set the parameter values for the Resource Manager template
$Parameters = @{
    "storageAccountType"="Standard_LRS"
    }

# Create a new context
$Context = New-AzStorageContext -StorageAccountName $storageAccount -StorageAccountKey $storageAccountKey

Get-AzStorageFileContent `
    -ShareName 'resource-templates' `
    -Context $Context `
    -path 'storageTemplate.json' `
    -Destination 'C:\Temp' -Force

$TemplateFile = Join-Path -Path 'C:\Temp' -ChildPath $storageFileName

# Deploy the storage account
New-AzResourceGroupDeployment `
    -ResourceGroupName $resourceGroup `
    -TemplateFile $TemplateFile `
    -TemplateParameterObject $Parameters 
```

## <a name="import-and-publish-the-runbook-into-your-azure-automation-account"></a>Runbook を Azure Automation アカウントにインポートし、公開する

PowerShell を使用して Runbook を Automation アカウントにインポートし、その Runbook を公開します。 Azure portal で Runbook をインポートし、公開する方法については、「[Azure Automation で Runbook を管理する](manage-runbooks.md)」をご覧ください。

`runbookScript.ps1` を Automation アカウントに PowerShell Runbook としてインポートするには、次の PowerShell コマンドを実行します。

```powershell
$importParams = @{
    Path = $runbookScript
    ResourceGroupName = $resourceGroup
    AutomationAccountName = $automationAccount
    Type = "PowerShell"
}
Import-AzAutomationRunbook @importParams

# Publish the runbook
$publishParams = @{
    ResourceGroupName = $resourceGroup
    AutomationAccountName = $automationAccount
    Name = "runbookScript"
}
Publish-AzAutomationRunbook @publishParams
```

## <a name="start-the-runbook"></a>Runbook の起動

[Start-AzAutomationRunbook](/powershell/module/Az.Automation/Start-AzAutomationRunbook) コマンドレットを呼び出して Runbook を起動します。 Azure Portal で Runbook を起動する方法については、「[Azure Automation での Runbook の開始](./start-runbooks.md)」をご覧ください。

PowerShell コンソールで次のコマンドを実行します。

```powershell
# Set up the parameters for the runbook
$runbookParams = @{
    resourceGroup = $resourceGroup
    storageAccount = $storageAccount
    storageAccountKey = $key[0].Value # We got this key earlier
    storageFileName = "storageTemplate.json"
    userAssignedManagedIdentity = $userAssignedManagedIdentity
}

# Set up parameters for the Start-AzAutomationRunbook cmdlet
$startParams = @{
    resourceGroup = $resourceGroup
    AutomationAccountName = $automationAccount
    Name = "runbookScript"
    Parameters = $runbookParams
}

# Start the runbook
$job = Start-AzAutomationRunbook @startParams
```

Runbook の実行後、ジョブ オブジェクト `$job.Status` のプロパティ値を取得することでその状態を確認できます。

Runbook は、Resource Manager テンプレートを取得し、それを使用して新しい Azure Storage アカウントをデプロイします。 次のコマンドを実行して、新しいストレージ アカウントが作成されたことを確認できます。

```powershell
Get-AzStorageAccount
```

## <a name="next-steps"></a>次のステップ

* Resource Manager テンプレートについて詳しくは、「[Azure Resource Manager の概要](../azure-resource-manager/management/overview.md)」をご覧ください。
* Azure Storage の概要については、「[Microsoft Azure Storage の概要](../storage/common/storage-introduction.md)」をご覧ください。
* 役に立つその他の Azure Automation Runbook を探すには、[Azure Automation 用の Runbook とモジュールの使用](automation-runbook-gallery.md)に関するページをご覧ください。