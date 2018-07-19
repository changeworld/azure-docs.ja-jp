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
ms.date: 07/09/2018
ms.author: tomfitz
ms.openlocfilehash: 3a29319a0d478537dfc4905ee77865b8fea64587
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/11/2018
ms.locfileid: "38598409"
---
# <a name="use-azure-key-vault-to-pass-secure-parameter-value-during-deployment"></a>デプロイ時に Azure Key Vault を使用して、セキュリティで保護されたパラメーター値を渡す

デプロイ時に、セキュリティで保護された値 (パスワードなど) をパラメーターとして渡す必要がある場合は、[Azure Key Vault](../key-vault/key-vault-whatis.md) からその値を取得できます。 値を取得するには、キー コンテナーとパラメーター ファイル内のシークレットを参照します。 参照するのは Key Vault ID だけであるため、値が公開されることはありません。 キー コンテナーは、デプロイ先のリソース グループとは異なるサブスクリプションに存在していてもかまいません。

## <a name="enable-access-to-the-secret"></a>シークレットへのアクセスの有効化

テンプレートのデプロイ時にキー コンテナーにアクセスするために存在する必要がある、次の 2 つの重要な条件があります。

1. キー コンテナー プロパティ `enabledForTemplateDeployment` は `true` である必要があります。
2. テンプレートをデプロイするユーザーは、シークレットにアクセスできる必要があります。 ユーザーには、キー コンテナーに対する `Microsoft.KeyVault/vaults/deploy/action` アクセス許可が必要です。 このアクセスは、[所有者](../role-based-access-control/built-in-roles.md#owner)ロールと[共同作成者](../role-based-access-control/built-in-roles.md#contributor)ロールが許可します。

[Managed Applications](../managed-applications/overview.md) のテンプレートで Key Vault を使用する場合は、**アプライアンス リソース プロバイダー** サービス プリンシパルにアクセス許可を付与する必要があります。 詳細については、「[Access Key Vault secret when deploying Azure Managed Applications](../managed-applications/key-vault-access.md)」(Azure Managed Applications のデプロイ時に Key Vault シークレットにアクセスする) を参照してください。


## <a name="deploy-a-key-vault-and-secret"></a>Key Vault とシークレットのデプロイ

キー コンテナーとシークレットを作成するには、Azure CLI または PowerShell のいずれかを使用します。 テンプレートのデプロイのために、キー コンテナーが有効にされることに注意してください。 

Azure CLI では、次を使用します。

```azurecli-interactive
vaultname={your-unique-vault-name}
password={password-value}

az group create --name examplegroup --location 'South Central US'
az keyvault create \
  --name $vaultname \
  --resource-group examplegroup \
  --location 'South Central US' \
  --enabled-for-template-deployment true
az keyvault secret set --vault-name $vaultname --name examplesecret --value $password
```

PowerShell では、次を使用します。

```powershell
$vaultname = "{your-unique-vault-name}"
$password = "{password-value}"

New-AzureRmResourceGroup -Name examplegroup -Location "South Central US"
New-AzureRmKeyVault `
  -VaultName $vaultname `
  -ResourceGroupName examplegroup `
  -Location "South Central US" `
  -EnabledForTemplateDeployment
$secretvalue = ConvertTo-SecureString $password -AsPlainText -Force
Set-AzureKeyVaultSecret -VaultName $vaultname -Name "examplesecret" -SecretValue $secretvalue
```

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
az group create --name datagroup --location "South Central US"
az group deployment create \
    --name exampledeployment \
    --resource-group datagroup \
    --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/keyvaultparameter/sqlserver.json \
    --parameters @sqlserver.parameters.json
```

PowerShell では、次を使用します。

```powershell
New-AzureRmResourceGroup -Name datagroup -Location "South Central US"
New-AzureRmResourceGroupDeployment `
  -Name exampledeployment `
  -ResourceGroupName datagroup `
  -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/keyvaultparameter/sqlserver.json `
  -TemplateParameterFile sqlserver.parameters.json
```

## <a name="reference-a-secret-with-dynamic-id"></a>動的 ID でのシークレットの参照

前のセクションでは、Key Vault シークレットの静的リソース ID を渡す方法を紹介しました。 しかし参照すべき Key Vault シークレットがデプロイごとに変わる状況も考えられます。 その場合、パラメーター ファイルにリソース ID をハードコードすることはできません。 パラメーター ファイルではテンプレート式が使用できないので、パラメーター ファイルでリソース ID を動的に生成することはできません。

Key Vault シークレットのリソース ID を動的に生成するには、そのシークレットを必要とするリソースを、リンクされたテンプレートに移す必要があります。 親テンプレートに、そのリンクされたテンプレートを追加し、動的に生成されたリソース ID をパラメーターに格納して渡します。 次の図は、リンクされたテンプレート内のパラメーターがシークレットを参照するしくみを示しています。

![動的 ID](./media/resource-manager-keyvault-parameter/dynamickeyvault.png)

リンクされたテンプレートは、外部 URI を通じて使用可能である必要があります。 通常は、テンプレートをストレージ アカウントに追加し、`https://<storage-name>.blob.core.windows.net/templatecontainer/sqlserver.json` のような URI を通じてアクセスします。

[次のテンプレート](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/keyvaultparameter/sqlserver-dynamic-id.json)は、動的に key vault ID を作成し、パラメーターとして渡します。 GitHub の[サンプル テンプレート](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/keyvaultparameter/sqlserver.json)にリンクしています。

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
      "vaultName": {
        "type": "string"
      },
      "vaultResourceGroup": {
        "type": "string"
      },
      "secretName": {
        "type": "string"
      },
      "adminLogin": {
        "type": "string"
      },
      "sqlServerName": {
        "type": "string"
      }
    },
    "resources": [
    {
      "apiVersion": "2015-01-01",
      "name": "nestedTemplate",
      "type": "Microsoft.Resources/deployments",
      "properties": {
        "mode": "incremental",
        "templateLink": {
          "uri": "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/keyvaultparameter/sqlserver.json",
          "contentVersion": "1.0.0.0"
        },
        "parameters": {
          "adminPassword": {
            "reference": {
              "keyVault": {
                "id": "[resourceId(subscription().subscriptionId,  parameters('vaultResourceGroup'), 'Microsoft.KeyVault/vaults', parameters('vaultName'))]"
              },
              "secretName": "[parameters('secretName')]"
            }
          },
          "adminLogin": { "value": "[parameters('adminLogin')]" },
          "sqlServerName": {"value": "[parameters('sqlServerName')]"}
        }
      }
    }],
    "outputs": {}
}
```

上記のテンプレートをデプロイし、パラメーターの値を指定します。 GitHub のサンプル テンプレートを使用できますが、環境に合わせたパラメーター値を指定する必要があります。

Azure CLI では、次を使用します。

```azurecli-interactive
az group create --name datagroup --location "South Central US"
az group deployment create \
    --name exampledeployment \
    --resource-group datagroup \
    --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/keyvaultparameter/sqlserver-dynamic-id.json \
    --parameters vaultName=<your-vault> vaultResourceGroup=examplegroup secretName=examplesecret adminLogin=exampleadmin sqlServerName=<server-name>
```

PowerShell では、次を使用します。

```powershell
New-AzureRmResourceGroup -Name datagroup -Location "South Central US"
New-AzureRmResourceGroupDeployment `
  -Name exampledeployment `
  -ResourceGroupName datagroup `
  -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/keyvaultparameter/sqlserver-dynamic-id.json `
  -vaultName <your-vault> -vaultResourceGroup examplegroup -secretName examplesecret -adminLogin exampleadmin -sqlServerName <server-name>
```

## <a name="next-steps"></a>次の手順
* Key Vault の全般的な情報については、「[Azure Key Vault の概要](../key-vault/key-vault-get-started.md)」をご覧ください。
* キー シークレットの詳細な参照例については、 [Key Vault の例](https://github.com/rjmax/ArmExamples/tree/master/keyvaultexamples)を参照してください。
