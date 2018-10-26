---
title: Azure Resource Manager テンプレートでの Key Vault シークレット | Microsoft Docs
description: デプロイメント時にパラメーターとして Key Vault からシークレットを渡す方法について説明します。
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
editor: tysonn
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/10/2018
ms.author: tomfitz
ms.openlocfilehash: 06719f3a92dae805081ea85c346df97ebed0e0dc
ms.sourcegitcommit: 4b1083fa9c78cd03633f11abb7a69fdbc740afd1
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/10/2018
ms.locfileid: "49078072"
---
# <a name="use-azure-key-vault-to-pass-secure-parameter-value-during-deployment"></a>デプロイ時に Azure Key Vault を使用して、セキュリティで保護されたパラメーター値を渡す

デプロイ時に、セキュリティで保護された値 (パスワードなど) をパラメーターとして渡す必要がある場合は、[Azure Key Vault](../key-vault/key-vault-whatis.md) からその値を取得できます。 値を取得するには、キー コンテナーとパラメーター ファイル内のシークレットを参照します。 参照するのは Key Vault ID だけであるため、値が公開されることはありません。 キー コンテナーは、デプロイ先のリソース グループとは異なるサブスクリプションに存在していてもかまいません。

## <a name="deploy-a-key-vault-and-secret"></a>Key Vault とシークレットのデプロイ

キー コンテナーとシークレットを作成するには、Azure CLI または PowerShell のいずれかを使用します。 `enabledForTemplateDeployment` はキー コンテナーのプロパティです。 Resource Manager のデプロイからこのキー コンテナー内のシークレットにアクセスするには、`enabledForTemplateDeployment` を `true` にする必要があります。 

次のサンプルの Azure PowerShell と Azure CLI スクリプトは、キー コンテナーとシークレットを作成する方法を示しています。

Azure CLI では、次を使用します。

```azurecli-interactive
keyVaultName='{your-unique-vault-name}'
resourceGroupName='{your-resource-group-name}'
location='centralus'
userPrincipalName='{your-email-address-associated-with-your-subscription}'

# Create a resource group
az group create --name $resourceGroupName --location $location

# Create a Key Vault
az keyvault create \
  --name $keyVaultName \
  --resource-group $resourceGroupName \
  --location $location \
  --enabled-for-template-deployment true
az keyvault set-policy --upn $userPrincipalName --name $keyVaultName --secret-permissions set delete get list

# Create a secret with the name, vmAdminPassword
password=$(openssl rand -base64 32)
echo $password
az keyvault secret set --vault-name $keyVaultName --name 'vmAdminPassword' --value $password
```

PowerShell では、次を使用します。

```azurepowershell-interactive
$keyVaultName = "{your-unique-vault-name}"
$resourceGroupName="{your-resource-group-name}"
$location='Central US'
$userPrincipalName='{your-email-address-associated-with-your-subscription}'

New-AzureRmResourceGroup -Name $resourceGroupName -Location $location

New-AzureRmKeyVault `
  -VaultName $keyVaultName `
  -resourceGroupName $resourceGroupName `
  -Location $location `
  -EnabledForTemplateDeployment
Set-AzureRmKeyVaultAccessPolicy -VaultName $keyVaultName -UserPrincipalName $userPrincipalName -PermissionsToSecrets set,delete,get,list

$password = openssl rand -base64 32
echo $password
$secretvalue = ConvertTo-SecureString $password -AsPlainText -Force
Set-AzureKeyVaultSecret -VaultName $keyVaultName -Name "vmAdminPassword" -SecretValue $secretvalue
```

PowerShell スクリプトを Cloud Shell 以外で実行する場合は、代わりに次のコマンドを使用してパスワードを生成します。

```powershell
Add-Type -AssemblyName System.Web
[System.Web.Security.Membership]::GeneratePassword(16,3)
```

Resource Manager テンプレートを使用する場合は、「[チュートリアル: Resource Manager テンプレートのデプロイで Azure Key Vault を統合する](./resource-manager-tutorial-use-key-vault.md#prepare-the-key-vault)」を参照してください。

> [!NOTE]
> 各Azure サービスには、特定のパスワード要件があります。 たとえば、Azure 仮想マシンの要件については、「[VM を作成する際のパスワードの要件は何ですか](../virtual-machines/windows/faq.md#what-are-the-password-requirements-when-creating-a-vm)」で確認できます。

## <a name="enable-access-to-the-secret"></a>シークレットへのアクセスの有効化

`enabledForTemplateDeployment` を `true` に設定する以外に、テンプレートをデプロイするユーザーは、リソース グループとキー コンテナーなど、キー コンテナーを含むスコープに対して `Microsoft.KeyVault/vaults/deploy/action` アクセス許可を持っている必要があります。 このアクセスは、[所有者](../role-based-access-control/built-in-roles.md#owner)ロールと[共同作成者](../role-based-access-control/built-in-roles.md#contributor)ロールが許可します。 キー コンテナーを作成すると所有者になるので、そのアクセス許可持つことになります。 キー コンテナーが別のサブスクリプション以下にある場合、キー コンテナーの所有者がアクセス権を付与する必要があります。

次の手順は、最小限のアクセス許可を持つロールを作成する方法と、ユーザーを割り当てる方法を示します
1. カスタム ロール定義の JSON ファイルを作成します。

    ```json
    {
      "Name": "Key Vault resource manager template deployment operator",
      "IsCustom": true,
      "Description": "Lets you deploy a resource manager template with the access to the secrets in the Key Vault.",
      "Actions": [
        "Microsoft.KeyVault/vaults/deploy/action"
      ],
      "NotActions": [],
      "DataActions": [],
      "NotDataActions": [],
      "AssignableScopes": [
        "/subscriptions/00000000-0000-0000-0000-000000000000"
      ]
    }
    ```
    "00000000-0000-0000-0000-000000000000" は、テンプレートをデプロイする必要があるユーザーのサブスクリプション ID に置き換えます。

2. JSON ファイルを使用して新しいロールを作成します。

    ```azurepowershell
    $resourceGroupName= "<Resource Group Name>" # the resource group which contains the Key Vault
    $userPrincipalName = "<Email Address of the deployment operator>"
    New-AzureRmRoleDefinition -InputFile "<PathToTheJSONFile>" 
    New-AzureRmRoleAssignment -ResourceGroupName $resourceGroupName -RoleDefinitionName "Key Vault resource manager template deployment operator" -SignInName $userPrincipalName
    ```

    `New-AzureRmRoleAssignment` サンプルでは、カスタム ロールをリソース グループ レベルのユーザーに割り当てます。  

[Managed Applications](../managed-applications/overview.md) のテンプレートで Key Vault を使用する場合は、**アプライアンス リソース プロバイダー** サービス プリンシパルにアクセス許可を付与する必要があります。 詳細については、「[Access Key Vault secret when deploying Azure Managed Applications](../managed-applications/key-vault-access.md)」(Azure Managed Applications のデプロイ時に Key Vault シークレットにアクセスする) を参照してください。

## <a name="reference-a-secret-with-static-id"></a>固定 ID でのシークレットの参照

キー コンテナーのシークレットを受け取るテンプレートは、その他のテンプレートに似ています。 これは、**テンプレートではなく、パラメーター ファイルでキー コンテナーを参照する**ためです。 次の図は、パラメーター ファイルがシークレットを参照し、その値をテンプレートに渡すしくみを示しています。

![静的 ID](./media/resource-manager-keyvault-parameter/statickeyvault.png)

たとえば、[次のテンプレート](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/keyvaultparameter/sqlserver.json)では、管理者のパスワードを含む SQL データベースがデプロイされます。 パスワード パラメーターは、セキュリティで保護された文字列に設定されます。 しかしこのテンプレートでは、その値がどこから来るかは指定されません。

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "adminLogin": {
      "type": "string"
    },
    "adminPassword": {
      "type": "securestring"
    },
    "sqlServerName": {
      "type": "string"
    }
  },
  "resources": [
    {
      "name": "[parameters('sqlServerName')]",
      "type": "Microsoft.Sql/servers",
      "apiVersion": "2015-05-01-preview",
      "location": "[resourceGroup().location]",
      "tags": {},
      "properties": {
        "administratorLogin": "[parameters('adminLogin')]",
        "administratorLoginPassword": "[parameters('adminPassword')]",
        "version": "12.0"
      }
    }
  ],
  "outputs": {
  }
}
```

ここで、前のテンプレートのためにパラメーター ファイルを作成します。 このパラメーター ファイルで、テンプレート内のパラメーターの名前に一致するパラメーターを指定します。 パラメーター値のために、キー コンテナーのシークレットを参照します。 シークレットを参照するには、Key Vault のリソース識別子とシークレットの名前を渡します。 [次のパラメーター ファイル](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/keyvaultparameter/sqlserver.parameters.json)では、Key Vault シークレットが既に存在しているという前提で、リソース ID に静的な値を指定します。 このファイルをローカルにコピーし、サブスクリプション ID、コンテナー名、および SQL サーバー名を設定します。

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "adminLogin": {
            "value": "exampleadmin"
        },
        "adminPassword": {
            "reference": {
              "keyVault": {
                "id": "/subscriptions/<subscription-id>/resourceGroups/examplegroup/providers/Microsoft.KeyVault/vaults/<vault-name>"
              },
              "secretName": "examplesecret"
            }
        },
        "sqlServerName": {
            "value": "<your-server-name>"
        }
    }
}
```

現在のバージョン以外のバージョンのシークレットを使用する必要がある場合は、`secretVersion` プロパティを使用します。

```json
"secretName": "examplesecret",
"secretVersion": "cd91b2b7e10e492ebb870a6ee0591b68"
```

ここで、テンプレートをデプロイし、パラメーター ファイルを渡します。 GitHub のサンプル テンプレートを使用できますが、環境に合わせて値が設定されているローカル パラメーター ファイルを使用する必要があります。

Azure CLI では、次を使用します。

```azurecli-interactive
az group create --name datagroup --location $location
az group deployment create \
    --name exampledeployment \
    --resource-group datagroup \
    --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/keyvaultparameter/sqlserver.json \
    --parameters @sqlserver.parameters.json
```

PowerShell では、次を使用します。

```powershell-interactive
New-AzureRmResourceGroup -Name datagroup -Location $location
New-AzureRmResourceGroupDeployment `
  -Name exampledeployment `
  -ResourceGroupName datagroup `
  -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/keyvaultparameter/sqlserver.json `
  -TemplateParameterFile sqlserver.parameters.json
```

## <a name="reference-a-secret-with-dynamic-id"></a>動的 ID でのシークレットの参照

前のセクションでは、Key Vault シークレットの静的リソース ID をパラメーターから渡す方法について説明しました。 しかし参照すべき Key Vault シークレットがデプロイごとに変わる状況も考えられます。 また、パラメーター ファイルに参照パラメーターを作成するのではなく、単にテンプレートでパラメーター値を渡すこともできます。 いずれの場合でも、リンクされたテンプレートを使用して、キー コンテナー シークレットのリソース ID を動的に生成することができます。

パラメーター ファイルではテンプレート式が使用できないので、パラメーター ファイルでリソース ID を動的に生成することはできません。 

親テンプレートに、そのリンクされたテンプレートを追加し、動的に生成されたリソース ID をパラメーターに格納して渡します。 次の図は、リンクされたテンプレート内のパラメーターがシークレットを参照するしくみを示しています。

![動的 ID](./media/resource-manager-keyvault-parameter/dynamickeyvault.png)

[次のテンプレート](https://github.com/Azure/azure-quickstart-templates/tree/master/201-key-vault-use-dynamic-id)は、動的に key vault ID を作成し、パラメーターとして渡します。

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "location": {
            "type": "string",
            "defaultValue": "[resourceGroup().location]",
            "metadata": {
                "description": "The location where the resources will be deployed."
            }
        },
        "vaultName": {
            "type": "string",
            "metadata": {
                "description": "The name of the keyvault that contains the secret."
            }
        },
        "secretName": {
            "type": "string",
            "metadata": {
                "description": "The name of the secret."
            }
        },
        "vaultResourceGroupName": {
            "type": "string",
            "metadata": {
                "description": "The name of the resource group that contains the keyvault."
            }
        },
        "vaultSubscription": {
            "type": "string",
            "defaultValue": "[subscription().subscriptionId]",
            "metadata": {
                "description": "The name of the subscription that contains the keyvault."
            }
        },
        "_artifactsLocation": {
            "type": "string",
            "metadata": {
                "description": "The base URI where artifacts required by this template are located. When the template is deployed using the accompanying scripts, a private location in the subscription will be used and this value will be automatically generated."
            },
            "defaultValue": "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-key-vault-use-dynamic-id/"
        },
        "_artifactsLocationSasToken": {
            "type": "securestring",
            "metadata": {
                "description": "The sasToken required to access _artifactsLocation.  When the template is deployed using the accompanying scripts, a sasToken will be automatically generated."
            },
            "defaultValue": ""
        }
    },
    "resources": [
        {
            "apiVersion": "2018-05-01",
            "name": "dynamicSecret",
            "type": "Microsoft.Resources/deployments",
            "properties": {
                "mode": "Incremental",
                "templateLink": {
                    "contentVersion": "1.0.0.0",
                    "uri": "[uri(parameters('_artifactsLocation'), concat('./nested/sqlserver.json', parameters('_artifactsLocationSasToken')))]"
                },
                "parameters": {
                    "location": {
                        "value": "[parameters('location')]"
                    },
                    "adminLogin": {
                        "value": "ghuser"
                    },
                    "adminPassword": {
                        "reference": {
                            "keyVault": {
                                "id": "[resourceId(parameters('vaultSubscription'), parameters('vaultResourceGroupName'), 'Microsoft.KeyVault/vaults', parameters('vaultName'))]"
                            },
                            "secretName": "[parameters('secretName')]"
                        }
                    }
                }
            }
        }
    ],
    "outputs": {
        "sqlFQDN": {
            "type": "string",
            "value": "[reference('dynamicSecret').outputs.sqlFQDN.value]"
        }
    }
}
```

上記のテンプレートをデプロイし、パラメーターの値を指定します。 GitHub のサンプル テンプレートを使用できますが、環境に合わせたパラメーター値を指定する必要があります。

Azure CLI では、次を使用します。

```azurecli-interactive
az group create --name datagroup --location $location
az group deployment create \
    --name exampledeployment \
    --resource-group datagroup \
    --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-key-vault-use-dynamic-id/azuredeploy.json \
    --parameters vaultName=<your-vault> vaultResourceGroupName=examplegroup secretName=examplesecret
```

PowerShell では、次を使用します。

```powershell
New-AzureRmResourceGroup -Name datagroup -Location $location
New-AzureRmResourceGroupDeployment `
  -Name exampledeployment `
  -ResourceGroupName datagroup `
  -TemplateUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-key-vault-use-dynamic-id/azuredeploy.json `
  -vaultName <your-vault> -vaultResourceGroupName examplegroup -secretName examplesecret
```

## <a name="next-steps"></a>次の手順
* Key Vault の全般的な情報については、「[Azure Key Vault の概要](../key-vault/key-vault-get-started.md)」をご覧ください。
* キー シークレットの詳細な参照例については、 [Key Vault の例](https://github.com/rjmax/ArmExamples/tree/master/keyvaultexamples)を参照してください。
