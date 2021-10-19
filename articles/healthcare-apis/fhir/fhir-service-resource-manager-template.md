---
title: ARM テンプレートを使用して Azure の医療 Api FHIR サービスをデプロイする
description: Azure Resource Manager テンプレート (ARM テンプレート) を使用して FHIR サービスをデプロイする方法について説明します。
author: ginalee-dotcom
ms.service: healthcare-apis
ms.topic: tutorial
ms.author: zxue
ms.date: 08/06/2021
ms.openlocfilehash: 97322c917e12e451affc2fe8a67bb8c8f6200a74
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/13/2021
ms.locfileid: "124782424"
---
# <a name="deploy-a-fhir-service-within-azure-healthcare-apis---using-arm-template"></a>Azure の医療 Api 内での FHIR サービスのデプロイ-ARM テンプレートの使用

> [!IMPORTANT]
> Azure Healthcare APIs は現在プレビュー段階です。 ベータ版、プレビュー版、または一般提供としてまだリリースされていない Azure の機能に適用されるその他の法律条項については、「[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)」に記載されています。

この記事では、Azure Resource Manager テンプレート (ARM テンプレート) を使用して、Azure の医療 Api (FHIR サービスと呼ばれる) 内に FHIR サービスをデプロイする方法について説明します。 PowerShell または CLI を使用して、2つのオプションを提供します。

[ARM テンプレート](../../azure-resource-manager/templates/overview.md)は、プロジェクトのインフラストラクチャと構成を定義する JSON ファイルです。 このテンプレートでは、宣言型の構文が使用されています。 宣言型の構文では、デプロイしようとしているものを、デプロイを作成する一連のプログラミング コマンドを記述しなくても記述できます。

## <a name="prerequisites"></a>前提条件

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

* アクティブなサブスクリプションが含まれる Azure アカウント。 [無料で作成できます](https://azure.microsoft.com/free/)。
* コードをローカルで実行したい場合:
    * [Azure PowerShell](/powershell/azure/install-az-ps)。

# <a name="cli"></a>[CLI](#tab/CLI)

* アクティブなサブスクリプションが含まれる Azure アカウント。 [無料で作成できます](https://azure.microsoft.com/free/)。
* コードをローカルで実行したい場合:
    * Bash シェル ([Git for Windows](https://gitforwindows.org) に付属する Git Bash など)。
    * [Azure CLI](/cli/azure/install-azure-cli)。

---

## <a name="review-the-arm-template"></a>ARM テンプレートを確認する

この記事で使用されているテンプレートは [Azure クイックスタート テンプレート](https://azure.microsoft.com/resources/templates/azure-api-for-fhir/)からのものです。

このテンプレートでは、次の3つの Azure リソースを定義します。
- HealthcareApis/ワークスペース
- Microsoft.HealthcareApis/workspaces/fhirservices      
- Microsoft.Storage/storageAccounts

FHIR サービスリソースをデプロイするには 、ワークスペースリソース、ストレージリソース、および `dependsOn` "HealthcareApis/workspaces/fhirservices" リソースのプロパティを削除します。


```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "authorityurl": {
            "type": "string",
            "defaultValue": "https://login.microsoftonline.com"
        },
        "tagName": {
            "type": "string",
            "defaultValue": "My Deployment"
        },
        "region": {
            "type": "string",
                  "allowedValues": [
                "australiaeast",
                "canadacentral",
                "eastus",
                "eastus2",
                "germanywestcentral",
                "japaneast",
                "northcentralus",
                "northeurope",
                "southafricanorth",
                "southcentralus",
                "southeastasia",
                "switzerlandnorth",
                "uksouth",
                "ukwest",
                "westcentralus",
                "westeurope",
                "westus2"
            ]
        },
        "workspaceName": {
            "type": "string"
        },
        "fhirServiceName": {
            "type": "string"
        },
        "tenantid": {
            "type": "string"
        },
        "storageAccountName": {
            "type": "string"
        },
        "storageAccountConfirm": {
            "type": "bool",
            "defaultValue": true
        },
        "AccessPolicies": {
            "type": "array",
            "defaultValue": []
        },
        "smartProxyEnabled": {
            "type": "bool",
            "defaultValue": false
        }
    },
    "variables": { 
        "authority": "[Concat(parameters('authorityurl'), '/', parameters('tenantid'))]",
        "createManagedIdentity": true,
        "managedIdentityType": {
            "type": "SystemAssigned"
        },
        "storageBlobDataContributerRoleId": "[concat('/subscriptions/', subscription().subscriptionId, '/providers/Microsoft.Authorization/roleDefinitions/', 'ba92f5b4-2d11-453d-a403-e96b0029c9fe')]"
    },
    "resources": [
        {
            "type": "Microsoft.HealthcareApis/workspaces",
            "name": "[parameters('workspaceName')]",
            "apiVersion": "2020-11-01-preview",
            "location": "[parameters('region')]",
            "properties": {}
        },
       {          
            "type": "Microsoft.HealthcareApis/workspaces/fhirservices",
            "kind": "fhir-R4",
            "name": "[concat(parameters('workspaceName'), '/', parameters('fhirServiceName'))]",
            "apiVersion": "2020-11-01-preview",
            "location": "[parameters('region')]",
            "dependsOn": [
                "[resourceId('Microsoft.HealthcareApis/workspaces', parameters('workspaceName'))]"
            ],
            "tags": {
                "environmentName": "[parameters('tagName')]"
            },
            "properties": {
                "accessPolicies": "[parameters('AccessPolicies')]",
                "authenticationConfiguration": {
                    "authority": "[variables('Authority')]",
                    "audience": "[concat('https//', parameters('workspaceName'), '-', parameters('fhirServiceName'), '.fhir.azurehealthcareapis.com')]",
                    "smartProxyEnabled": "[parameters('smartProxyEnabled')]"
                },
                "corsConfiguration": {
                    "allowCredentials": false,
                    "headers": ["*"],
                    "maxAge": 1440,
                    "methods": ["DELETE", "GET", "OPTIONS", "PATCH", "POST", "PUT"],
                    "origins": ["https://localhost:6001"]
                },
                "exportConfiguration": {
                    "storageAccountName": "[parameters('storageAccountName')]"
                }
            },
            "identity": "[if(variables('createManagedIdentity'), variables('managedIdentityType'), json('null'))]"
        },
        {
            "name": "[parameters('storageAccountName')]",
            "type": "Microsoft.Storage/storageAccounts",
            "apiVersion": "2019-06-01",
            "location": "[resourceGroup().location]",
            "properties": {
                "supportsHttpsTrafficOnly": "true"
            },
            "condition": "[parameters('storageAccountConfirm')]",
            "dependsOn": [
                "[parameters('fhirServiceName')]"
            ],
            "sku": {
                "name": "Standard_LRS"
            },
            "kind": "Storage",
            "tags": {
                "environmentName": "[parameters('tagName')]",
                "test-account-rg": "true"
            }
        }
    ],
    "outputs": {
    }
}
```

## <a name="deploy-arm-template"></a>ARM テンプレートのデプロイ

PowerShell または CLI の2つのオプションを使用して、ARM テンプレートをデプロイできます。

次に示すサンプルコードでは、サブフォルダー "src" の "templates" サブフォルダーにあるテンプレートを使用しています。 テンプレートファイルを適切に参照するようにロケーションパスを変更することもできます。

デプロイプロセスが完了するまで数分かかります。 FHIR サービスとリソースグループの名前をメモしておきます。これは後で使用します。

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

### <a name="deploy-the-template-using-powershell"></a>テンプレートのデプロイ: PowerShell の使用

fhir サービスをデプロイするには、PowerShell でローカル、Visual Studio Code、または Azure Cloud Shell でコードを実行します。 

Azure にログインしていない場合は、"Connect-azaccount" を使用してログインします。 ログインしたら、"Get AzContext" を使用して、使用するサブスクリプションとテナントを確認します。 必要に応じて、サブスクリプションとテナントを変更できます。

新しいリソースグループを作成するか、既存のリソースグループを使用することができます。そのためには、手順を省略するか、"New-AzResourceGroup" で始まる行をコメントアウトします。

```powershell-interactive
### variables
$resourcegroupname="your resource group"
$location="South Central US"
$workspacename="your workspace name"
$servicename="your fhir service name"
$tenantid="xxx"
$subscriptionid="xxx"
$storageaccountname="storage account name"
$storageaccountconfirm=1

### login to azure
Connect-AzAccount 
#Connect-AzAccount SubscriptionId $subscriptionid
Set-AzContext -Subscription $subscriptionid
Connect-AzAccount -Tenant $tenantid -SubscriptionId $subscriptionid
#Get-AzContext 

### create resource group
New-AzResourceGroup -Name $resourcegroupname -Location $location

### deploy the resource
New-AzResourceGroupDeployment -ResourceGroupName $resourcegroupname -TemplateFile "src/templates/fhirtemplate.json" -region $location -workspaceName $workspacename -fhirServiceName $fhirservicename -tenantid $tenantid -storageAccountName $storageaccountname -storageAccountConfirm $storageaccountconfirm
```
# <a name="cli"></a>[CLI](#tab/CLI)

### <a name="deploy-the-template-using-cli"></a>テンプレートをデプロイする: CLI を使用する

fhir サービスをデプロイするには、Visual Studio Code または Azure Cloud Shell でローカルにコードを実行します。 

Azure にログインしていない場合は、"az login" を使用してログインします。 ログインしたら、"az account show--output table" を使用して、使用するサブスクリプションとテナントを確認します。 必要に応じて、サブスクリプションとテナントを変更できます。

新しいリソースグループを作成するか、既存のリソースグループを使用することができます。そのためには、手順をスキップするか、"az group create" で始まる行をコメントアウトします。

```azurecli-interactive
### variables
resourcegroupname=your resource group name
location=southcentralus
workspacename=your workspace name
fhirservicename=your fhir service name
tenantid=xxx
subscriptionid=xxx
storageaccountname=your storage account name
storageaccountconfirm=true

### login to azure
az login
az account show --output table
az account set --subscription $subscriptionid

### create resource group
az group create --name $resourcegroupname --location $location

### deploy the resource
az deployment group create --resource-group $resourcegroupname --template-file 'src\\templates\\fhirtemplate.json' --parameters region=$location workspaceName=$workspacename fhirServiceName=$fhirservicename tenantid=$tenantid storageAccountName=$storageaccountname storageAccountConfirm=$storageaccountconfirm
```

---

## <a name="review-the-deployed-resources"></a>デプロイされたリソースを確認する

ブラウザーを開き、に移動して、FHIR サービスが稼働していることを確認でき `https://<yourfhir servic>.azurehealthcareapis.com/metadata` ます。 機能ステートメントが自動的に表示またはダウンロードされた場合、デプロイは正常に実行されます。 

## <a name="clean-up-the-resources"></a>リソースのクリーンアップ

リソースが不要になった場合は、次のコードを実行してリソースグループを削除します。

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)
```powershell-interactive
$resourceGroupName = “your resource group name”
Remove-AzResourceGroup -Name $resourceGroupName
```
# <a name="cli"></a>[CLI](#tab/CLI)
```azurecli-interactive
resourceGroupName = “your resource group name”
az group delete --name $resourceGroupName
```
---

## <a name="next-steps"></a>次のステップ

このクイックスタートガイドでは、ARM テンプレートを使用して、Azure の医療 Api 内に FHIR サービスをデプロイしました。 FHIR サービスでサポートされている機能の詳細については、「」を参照してください。

>[!div class="nextstepaction"]
>[サポートされる FHIR 機能](fhir-features-supported.md)