---
title: Azure ガイド - Azure Resource Manager テンプレートを使用して Azure キー コンテナーとコンテナーのアクセス ポリシーを作成する | Microsoft Docs
description: Azure Resource Manager テンプレートを使用して、Azure キー コンテナーとコンテナーのアクセス ポリシーを作成する方法について説明します。
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: how-to
ms.date: 10/5/2020
ms.author: mbaldwin
ms.openlocfilehash: cf19561005fe2e98b7b5cf6812ff9224fd9474dc
ms.sourcegitcommit: 23aa0cf152b8f04a294c3fca56f7ae3ba562d272
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/07/2020
ms.locfileid: "91804267"
---
# <a name="how-to-create-azure-key-vault-and-vault-access-policy-using-a-resource-manager-template"></a>Resource Manager テンプレートを使用して Azure キー コンテナーとコンテナーのアクセス ポリシーを作成する方法

[Azure Key Vault](../general/overview.md) は、キー、パスワード、証明書、その他のシークレットなど、シークレットのための安全な保管場所を提供するクラウド サービスです。 このガイドでは、Azure Resource Manager テンプレート (ARM テンプレート) をデプロイしてキー コンテナーを作成するプロセスを中心に取り上げます。

[!INCLUDE [About Azure Resource Manager](../../../includes/resource-manager-quickstart-introduction.md)]

## <a name="prerequisites"></a>前提条件

この記事を完了するには:

* Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) を作成してください。


## <a name="create-key-vault-resource-manager-template"></a>Key Vault Resource Manager テンプレートを作成する

次のテンプレートは、キー コンテナーを作成する基本的な方法を示したものです。 一部の値は、テンプレート内で指定されます。

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "keyVaultName": {
      "type": "string",
      "metadata": {
        "description": "Specifies the name of the key vault."
      }
    },
    "skuName": {
      "type": "string",
      "defaultValue": "Standard",
      "allowedValues": [
        "Standard",
        "Premium"
      ],
      "metadata": {
        "description": "Specifies whether the key vault is a standard vault or a premium vault."
      }
    }
   },
  "resources": [
    {
      "type": "Microsoft.KeyVault/vaults",
      "apiVersion": "2019-09-01",
      "name": "[parameters('keyVaultName')]",
      "location": "[resourceGroup().location]",
      "properties": {
        "enabledForDeployment": "false",
        "enabledForDiskEncryption": "false",
        "enabledForTemplateDeployment": "false",
        "tenantId": "[subscription().tenantId]",
        "accessPolicies": [],
        "sku": {
          "name": "[parameters('skuName')]",
          "family": "A"
        },
        "networkAcls": {
          "defaultAction": "Allow",
          "bypass": "AzureServices"
        }
      }
    }
  ]
}

```

Key Vault テンプレート設定の詳細については、[Key Vault ARM テンプレート リファレンス](https://docs.microsoft.com/azure/templates/microsoft.keyvault/vaults)に関するページを参照してください。

> [!IMPORTANT]
> テンプレートが再デプロイされると、それによってキー コンテナー内の既存のアクセス ポリシーがオーバーライドされます。 キー コンテナーへのアクセスが失われないように、`accessPolicies` プロパティに既存のアクセス ポリシーを設定することをお勧めします。 

## <a name="add-access-policy-to-key-vault-resource-manager-template"></a>Key Vault Resource Manager テンプレートにアクセス ポリシーを追加する

キー コンテナー テンプレート全体を再デプロイすることなく、既存のキー コンテナーにアクセス ポリシーをデプロイできます。 次のテンプレートは、アクセス ポリシーを作成する基本的な方法を示したものです。

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "keyVaultName": {
      "type": "string",
      "metadata": {
        "description": "Specifies the name of the key vault."
      }
    },
    "objectId": {
      "type": "string",
      "metadata": {
        "description": "Specifies the object ID of a user, service principal or security group in the Azure Active Directory tenant for the vault. The object ID must be unique for the list of access policies. Get it by using Get-AzADUser or Get-AzADServicePrincipal cmdlets."
      }
    },
    "keysPermissions": {
      "type": "array",
      "defaultValue": [
        "list"
      ],
      "metadata": {
        "description": "Specifies the permissions to keys in the vault. Valid values are: all, encrypt, decrypt, wrapKey, unwrapKey, sign, verify, get, list, create, update, import, delete, backup, restore, recover, and purge."
      }
    },
    "secretsPermissions": {
      "type": "array",
      "defaultValue": [
        "list"
      ],
      "metadata": {
        "description": "Specifies the permissions to secrets in the vault. Valid values are: all, get, list, set, delete, backup, restore, recover, and purge."
      }
    },
    "certificatePermissions": {
      "type": "array",
      "defaultValue": [
        "list"
      ],
      "metadata": {
        "description": "Specifies the permissions to certificates in the vault. Valid values are: all,  create, delete, update, deleteissuers, get, getissuers, import, list, listissuers, managecontacts, manageissuers,  recover, backup, restore, setissuers, and purge."
      }
    },
  "resources": [
     {
      "type": "Microsoft.KeyVault/vaults/accessPolicies",
      "name": "[concat(parameters('keyVaultName'), '/add')]",
      "apiVersion": "2019-09-01",
      "properties": {
        "accessPolicies": [
          {
            "tenantId": "[subscription().tenantId]",
            "objectId": "[parameters('objectId')]",
            "permissions": {
              "keys": "[parameters('keysPermissions')]",
              "secrets": "[parameters('secretsPermissions')]",
              "certificates": [parameters('certificatesPermissions')]
            }
          }
        ]
      }
    }
  ]
}

```
Key Vault テンプレート設定の詳細については、[Key Vault ARM テンプレート リファレンス](https://docs.microsoft.com/azure/templates/microsoft.keyvault/vaults/accesspolicies)に関するページを参照してください。

## <a name="other-available-key-vault-resource-manager-templates"></a>その他に使用可能な Key Vault Resource Manager テンプレート

Key Vault オブジェクトで使用できる Resource Manager テンプレートが他にもあります。

| シークレット | [キー] | 証明書 |
|--|--|--|
|[クイックスタート](https://docs.microsoft.com/azure/key-vault/secrets/quick-create-template)<br>[参照](https://docs.microsoft.com/azure/templates/microsoft.keyvault/vaults/secrets)|該当なし|該当なし|

その他の Key Vault テンプレートについては、こちらを参照してください: [Key Vault Resource Manager リファレンス](https://docs.microsoft.com/azure/templates/microsoft.keyvault/allversions)

## <a name="deploy-the-templates"></a>テンプレートの配備

Azure portal を使用すれば、次のガイドに記載された "エディターで独自のテンプレートを作成する" オプションを使用して上記のテンプレートをデプロイすることができます。[カスタム テンプレートからリソースをデプロイする](https://docs.microsoft.com/azure/azure-resource-manager/templates/deploy-portal#deploy-resources-from-custom-template)

また、上記のテンプレートをファイルに保存し、次のコマンドを使用することもできます。[New-AzResourceGroupDeployment](/powershell/module/az.resources/new-azresourcegroupdeployment) および [az group deployment create](/cli/azure/group/deployment#az-group-deployment-create):

```azurepowershell
New-AzResourceGroupDeployment -ResourceGroupName ExampleGroup -TemplateFile key-vault-template.json
```

```azurecli
az group deployment create --resource-group ExampleGroup --template-file key-vault-template.json
```

## <a name="clean-up-resources"></a>リソースをクリーンアップする

後続のクイック スタートおよびチュートリアルで引き続き作業する予定がある場合は、これらのリソースをそのまま残しておくことができます。 リソースが不要になったら、リソース グループを削除します。これにより、キー コンテナーおよび関連リソースが削除されます。 Azure CLI または Azure PowerShell を使用してリソース グループを削除するには、次の手順を使用します。

# <a name="cli"></a>[CLI](#tab/CLI)

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
az group delete --name $resourceGroupName &&
echo "Press [ENTER] to continue ..."
```

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
Remove-AzResourceGroup -Name $resourceGroupName
Write-Host "Press [ENTER] to continue..."
```

---

## <a name="resources"></a>リソース

- [Azure Key Vault の概要](../general/overview.md)を確認する
- [Azure Resource Manager](../../azure-resource-manager/management/overview.md) の詳細を確認する
- [Azure Key Vault のベスト プラクティス](../general/best-practices.md)を確認する

## <a name="next-steps"></a>次の手順

- [キー コンテナーへのアクセスをセキュリティで保護する](secure-your-key-vault.md)
- [キー コンテナーに対する認証](authentication.md)
- [Azure Key Vault 開発者ガイド](developers-guide.md)
