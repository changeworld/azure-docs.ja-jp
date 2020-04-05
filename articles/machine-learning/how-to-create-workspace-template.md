---
title: Azure Resource Manager テンプレートでワークスペースを作成する
titleSuffix: Azure Machine Learning
description: Azure Resource Manager テンプレートを使用して新しい Azure Machine Learning ワークスペースを作成する方法について説明します。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: larryfr
author: Blackmist
ms.date: 03/05/2020
ms.custom: seoapril2019
ms.openlocfilehash: 9403cc05ed5b31f3b76c16c4232506e2ddc5da2d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/28/2020
ms.locfileid: "78402905"
---
[!INCLUDE [aml-applies-to-basic-enterprise-sku](../../includes/aml-applies-to-basic-enterprise-sku.md)]
<br>

# <a name="use-an-azure-resource-manager-template-to-create-a-workspace-for-azure-machine-learning"></a>Azure Resource Manager テンプレートを使用して Azure Machine Learning のワークスペースを作成します。

この記事では、Azure Resource Manager テンプレートを使用して Azure Machine Learning ワークスペースを作成するさまざまな方法について説明します。 Resource Manager テンプレートを使用すると、1 つの調整された操作でリソースを簡単に作成できます。 テンプレートは、デプロイに必要なリソースを定義する JSON ドキュメントです。 デプロイ パラメーターを指定することもできます。 パラメーターは、テンプレートの使用時に入力値を指定するために使用します。

詳細については、「 [Azure リソース マネージャーのテンプレートを使用したアプリケーションのデプロイ](../azure-resource-manager/templates/deploy-powershell.md)」を参照してください。

## <a name="prerequisites"></a>前提条件

* **Azure サブスクリプション**。 お持ちでない場合は、[無料版または有料版の Azure Machine Learning](https://aka.ms/AMLFree) をお試しください。

* CLI からテンプレートを使用するには、[Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview?view=azps-1.2.0) または [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) が必要です。

## <a name="resource-manager-template"></a>Resource Manager テンプレート

次の Resource Manager テンプレートを使用すると、Azure Machine Learning ワークスペースと関連する Azure リソースを作成できます。

[!code-json[create-azure-machine-learning-service-workspace](~/quickstart-templates/101-machine-learning-create/azuredeploy.json)]

このテンプレートでは、次の Azure サービスが作成されます。

* Azure リソース グループ
* Azure Storage アカウント
* Azure Key Vault
* Azure Application Insights
* Azure Container Registry
* Azure Machine Learning ワークスペース

リソース グループは、サービスを保持するコンテナーです。 Azure Machine Learning ワークスペースにはさまざまなサービスが必要です。

サンプルのテンプレートには次に示す 2 つのパラメーターがあります。

* リソース グループとサービスを作成する**場所**。

    このテンプレートでは、大部分のリソース用に選択する場所が使用されます。 例外は Application Insights サービスです。このサービスは、他のサービスが存在するすべての場所で使用できません。 使用できない場所を選択すると、米国中南部の場所にサービスが作成されます。

* **ワークスペース名**。これは Azure Machine Learning ワークスペースのフレンドリ名です。

    > [!NOTE]
    > ワークスペース名では、大文字と小文字は区別されません。

    その他のサービスの名前はランダムに生成されます。

> [!TIP]
> このドキュメントに関連付けられているテンプレートでは新しい Azure コンテナー レジストリが作成されますが、コンテナー レジストリを作成せずに新しいワークスペースを作成することもできます。 コンテナー レジストリが必要な操作を実行すると、それが 1 つ作成されます。 たとえば、モデルのトレーニングやデプロイなどです。
>
> 新しいコンテナー レジストリまたはストレージ アカウントを作成するのではなく、Azure Resource Manager テンプレートで既存のものを参照することもできます。

[!INCLUDE [machine-learning-delete-acr](../../includes/machine-learning-delete-acr.md)]

テンプレートの詳細については、次の記事を参照してください。

* [Azure リソース マネージャーのテンプレートの作成](../azure-resource-manager/templates/template-syntax.md)
* [Azure Resource Manager テンプレートを使用したアプリケーションのデプロイ](../azure-resource-manager/templates/deploy-powershell.md)
* [Microsoft.MachineLearningServices resource types (Microsoft.MachineLearningServices リソースの種類)](https://docs.microsoft.com/azure/templates/microsoft.machinelearningservices/allversions)

### <a name="advanced-template"></a>高度なテンプレート

次のテンプレート例では、3 つの設定を使用してワークスペースを作成する方法を示します。

* ワークスペースの高機密性の設定を有効にする
* ワークスペースの暗号化を有効にする
* 既存の Azure Key Vault を使用する

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "workspaceName": {
      "type": "string",
      "metadata": {
        "description": "Specifies the name of the Azure Machine Learning workspace."
      }
    },
    "location": {
      "type": "string",
      "defaultValue": "southcentralus",
      "allowedValues": [
        "eastus",
        "eastus2",
        "southcentralus",
        "southeastasia",
        "westcentralus",
        "westeurope",
        "westus2"
      ],
      "metadata": {
        "description": "Specifies the location for all resources."
      }
    },
    "sku":{
      "type": "string",
      "defaultValue": "basic",
      "allowedValues": [
        "basic",
        "enterprise"
      ],
      "metadata": {
        "description": "Specifies the sku, also referred to as 'edition' of the Azure Machine Learning workspace."
      }
    },
    "hbi_workspace":{
      "type": "string",
      "defaultValue": "false",
      "allowedValues": [
        "false",
        "true"
      ],
      "metadata": {
        "description": "Specifies that the Azure Machine Learning workspace holds highly confidential data."
      }
    },
    "encryption_status":{
      "type": "string",
      "defaultValue": "Disabled",
      "allowedValues": [
        "Enabled",
        "Disabled"
      ],
      "metadata": {
        "description": "Specifies if the Azure Machine Learning workspace should be encrypted with the customer managed key."
      }
    },
    "cmk_keyvault":{
      "type": "string",
      "metadata": {
        "description": "Specifies the customer managed keyvault Resource Manager ID."
      }
    },
    "resource_cmk_uri":{
      "type": "string",
      "metadata": {
        "description": "Specifies the customer managed keyvault key uri."
      }
    }
  },
  "variables": {
    "storageAccountName": "[concat('sa',uniqueString(resourceGroup().id))]",
    "storageAccountType": "Standard_LRS",
    "keyVaultName": "[concat('kv',uniqueString(resourceGroup().id))]",
    "tenantId": "[subscription().tenantId]",
    "applicationInsightsName": "[concat('ai',uniqueString(resourceGroup().id))]",
    "containerRegistryName": "[concat('cr',uniqueString(resourceGroup().id))]"
  },
  "resources": [
    {
      "type": "Microsoft.Storage/storageAccounts",
      "apiVersion": "2018-07-01",
      "name": "[variables('storageAccountName')]",
      "location": "[parameters('location')]",
      "sku": {
        "name": "[variables('storageAccountType')]"
      },
      "kind": "StorageV2",
      "properties": {
        "encryption": {
          "services": {
            "blob": {
              "enabled": true
            },
            "file": {
              "enabled": true
            }
          },
          "keySource": "Microsoft.Storage"
        },
        "supportsHttpsTrafficOnly": true
      }
    },
    {
      "type": "Microsoft.KeyVault/vaults",
      "apiVersion": "2018-02-14",
      "name": "[variables('keyVaultName')]",
      "location": "[parameters('location')]",
      "properties": {
        "tenantId": "[variables('tenantId')]",
        "sku": {
          "name": "standard",
          "family": "A"
        },
        "accessPolicies": []
      }
    },
    {
      "type": "Microsoft.Insights/components",
      "apiVersion": "2015-05-01",
      "name": "[variables('applicationInsightsName')]",
      "location": "[if(or(equals(parameters('location'),'eastus2'),equals(parameters('location'),'westcentralus')),'southcentralus',parameters('location'))]",
      "kind": "web",
      "properties": {
        "Application_Type": "web"
      }
    },
    {
      "type": "Microsoft.ContainerRegistry/registries",
      "apiVersion": "2017-10-01",
      "name": "[variables('containerRegistryName')]",
      "location": "[parameters('location')]",
      "sku": {
        "name": "Standard"
      },
      "properties": {
        "adminUserEnabled": true
      }
    },
    {
      "type": "Microsoft.MachineLearningServices/workspaces",
      "apiVersion": "2020-01-01",
      "name": "[parameters('workspaceName')]",
      "location": "[parameters('location')]",
      "dependsOn": [
        "[resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName'))]",
        "[resourceId('Microsoft.KeyVault/vaults', variables('keyVaultName'))]",
        "[resourceId('Microsoft.Insights/components', variables('applicationInsightsName'))]",
        "[resourceId('Microsoft.ContainerRegistry/registries', variables('containerRegistryName'))]"
      ],
      "identity": {
        "type": "systemAssigned"
      },
      "sku": {
            "tier": "[parameters('sku')]",
            "name": "[parameters('sku')]"
      },
      "properties": {
        "friendlyName": "[parameters('workspaceName')]",
        "keyVault": "[resourceId('Microsoft.KeyVault/vaults',variables('keyVaultName'))]",
        "applicationInsights": "[resourceId('Microsoft.Insights/components',variables('applicationInsightsName'))]",
        "containerRegistry": "[resourceId('Microsoft.ContainerRegistry/registries',variables('containerRegistryName'))]",
        "storageAccount": "[resourceId('Microsoft.Storage/storageAccounts/',variables('storageAccountName'))]",
         "encryption": {
                "status": "[parameters('encryption_status')]",
                "keyVaultProperties": {
                    "keyVaultArmId": "[parameters('cmk_keyvault')]",
                    "keyIdentifier": "[parameters('resource_cmk_uri')]"
                  }
            },
        "hbi_workspace": "[parameters('hbi_workspace')]"
      }
    }
  ]
}
```

Key Vault の ID と、このテンプレートに必要なキー URI を取得するために、Azure CLI を使用できます。 次のコマンドは、Azure CLI を使用して Key Vault リソースの ID および URI を取得する例です。

```azurecli-interactive
az keyvault show --name mykeyvault --resource-group myresourcegroup --query "[id, properties.vaultUri]"
```

このコマンドにより、次のテキストのような値が返されます。 最初の値は ID で、2 番目は URI です。

```text
[
  "/subscriptions/{subscription-guid}/resourceGroups/myresourcegroup/providers/Microsoft.KeyVault/vaults/mykeyvault",
  "https://mykeyvault.vault.azure.net/"
]
```

## <a name="use-the-azure-portal"></a>Azure ポータルの使用

1. 「[カスタム テンプレートからリソースをデプロイする](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-template-deploy-portal#deploy-resources-from-custom-template)」の手順に従います。 __[テンプレートの編集]__ 画面に到達したら、このドキュメントからテンプレートを貼り付けます。
1. __[保存]__ を選択してテンプレートを使用します。 次の情報を指定して、表示される使用条件に同意します。

   * サブスクリプション:これらのリソースに使用する Azure サブスクリプションを選択します。
   * リソース グループ: サービスが含まれるリソース グループを選択または作成します。
   * ワークスペース名: 作成される Azure Machine Learning ワークスペースに使用する名前。 ワークスペース名は 3 から 33 文字で指定する必要があります。 使用できるのは英数字と "-" のみです。
   * 場所:リソースを作成する場所を選択します。

詳細については、「[カスタム テンプレートからリソースをデプロイする](../azure-resource-manager/templates/deploy-portal.md#deploy-resources-from-custom-template)」を参照してください。

## <a name="use-azure-powershell"></a>Azure PowerShell の使用

この例では、現在のディレクトリ内の `azuredeploy.json` という名前のファイルにテンプレートを保存したと仮定します。

```powershell
New-AzResourceGroup -Name examplegroup -Location "East US"
new-azresourcegroupdeployment -name exampledeployment `
  -resourcegroupname examplegroup -location "East US" `
  -templatefile .\azuredeploy.json -workspaceName "exampleworkspace" -sku "basic"
```

詳細については、「[Resource Manager テンプレートと Azure PowerShell を使用したリソースのデプロイ](../azure-resource-manager/templates/deploy-powershell.md)」と「[SAS トークンと Azure PowerShell を使用してプライベートの Resource Manager テンプレートをデプロイする](../azure-resource-manager/templates/secure-template-with-sas-token.md)」を参照してください。

## <a name="use-the-azure-cli"></a>Azure CLI の使用

この例では、現在のディレクトリ内の `azuredeploy.json` という名前のファイルにテンプレートを保存したと仮定します。

```azurecli-interactive
az group create --name examplegroup --location "East US"
az group deployment create \
  --name exampledeployment \
  --resource-group examplegroup \
  --template-file azuredeploy.json \
  --parameters workspaceName=exampleworkspace location=eastus sku=basic
```

詳細については、「[Resource Manager テンプレートと Azure CLI を使用したリソースのデプロイ](../azure-resource-manager/templates/deploy-cli.md)」と「[SAS トークンと Azure CLI を使用してプライベートの Resource Manager テンプレートをデプロイする](../azure-resource-manager/templates/secure-template-with-sas-token.md)」を参照してください。

## <a name="troubleshooting"></a>トラブルシューティング

### <a name="resource-provider-errors"></a>リソース プロバイダーのエラー

[!INCLUDE [machine-learning-resource-provider](../../includes/machine-learning-resource-provider.md)]

### <a name="azure-key-vault-access-policy-and-azure-resource-manager-templates"></a>Azure Key Vault アクセス ポリシーと Azure Resource Manager テンプレート

これは、Azure Resource Manager テンプレートを使用してワークスペースおよび関連付けられたリソース (Azure Key Vault など) を複数回作成する場合があります。 たとえば、継続的インテグレーションおよびデプロイ パイプラインの一部として同じパラメーターを指定して、テンプレートを複数回使用する場合が挙げられます。

テンプレートによるリソース作成操作のほとんどはべき等操作ですが、テンプレートを使用するたびに Key Vault によってアクセス ポリシーはクリアされます。 アクセス ポリシーをクリアすると、それを使用している既存のワークスペース用の Key Vault へのアクセスは中断されます。 たとえば、Azure Notebooks VM の停止/作成機能が失敗することがあります。  

この問題を回避するには、次のいずれかのアプローチをお勧めします。

* パラメーターが同じである場合は、テンプレートを複数回デプロイしないでください。 または、テンプレートを使用してリソースを作成する前に、既存の同じものを削除してください。

* Key Vault のアクセス ポリシーを調べ、これらのポリシーを使用してテンプレートの `accessPolicies` プロパティを設定します。 アクセス ポリシーを表示するには、次の Azure CLI コマンドを使用します。

    ```azurecli-interactive
    az keyvault show --name mykeyvault --resource-group myresourcegroup --query properties.accessPolicies
    ```

    テンプレートの `accessPolicies` セクションの使用方法の詳細については、[AccessPolicyEntry オブジェクトのリファレンス](https://docs.microsoft.com/azure/templates/Microsoft.KeyVault/2018-02-14/vaults#AccessPolicyEntry)を参照してください。

* Key Vault リソースが既に存在しているかどうかを確認します。 存在している場合は、テンプレートを使ってそれを再作成しないでください。 たとえば、新規作成するのではなく、既存の Key Vault を使用するには、テンプレートに次の変更を加えます。

    * 既存の Key Vault リソースの ID を受け入れるパラメーターを**追加**します。

        ```json
        "keyVaultId":{
          "type": "string",
          "metadata": {
            "description": "Specify the existing Key Vault ID."
          }
        }
      ```

    * Key Vault リソースを作成するセクションを**削除**します。

        ```json
        {
          "type": "Microsoft.KeyVault/vaults",
          "apiVersion": "2018-02-14",
          "name": "[variables('keyVaultName')]",
          "location": "[parameters('location')]",
          "properties": {
            "tenantId": "[variables('tenantId')]",
            "sku": {
              "name": "standard",
              "family": "A"
            },
            "accessPolicies": [
            ]
          }
        },
        ```

    * ワークスペースの `dependsOn` セクションから `"[resourceId('Microsoft.KeyVault/vaults', variables('keyVaultName'))]",` の行を**削除**します。 また、`keyVaultId` パラメーターを参照するために、ワークスペースの `properties` セクションの `keyVault` エントリを**変更**します。

        ```json
        {
          "type": "Microsoft.MachineLearningServices/workspaces",
          "apiVersion": "2019-11-01",
          "name": "[parameters('workspaceName')]",
          "location": "[parameters('location')]",
          "dependsOn": [
            "[resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName'))]",
            "[resourceId('Microsoft.Insights/components', variables('applicationInsightsName'))]"
          ],
          "identity": {
            "type": "systemAssigned"
          },
          "sku": {
            "tier": "[parameters('sku')]",
            "name": "[parameters('sku')]"
          },
          "properties": {
            "friendlyName": "[parameters('workspaceName')]",
            "keyVault": "[parameters('keyVaultId')]",
            "applicationInsights": "[resourceId('Microsoft.Insights/components',variables('applicationInsightsName'))]",
            "storageAccount": "[resourceId('Microsoft.Storage/storageAccounts/',variables('storageAccountName'))]"
          }
        }
        ```

    これらの変更が完了したら、テンプレートを実行するときに既存の Key Vault リソースの ID を指定できます。 その後、テンプレートは、ワークスペースの `keyVault` プロパティをその ID に設定することによって、Key Vault を再利用します。

    Key Vault の ID を取得するには、元のテンプレート実行の出力を参照するか、Azure CLI を使用します。 次のコマンドは、Azure CLI を使用して Key Vault リソースの ID を取得する例です。

    ```azurecli-interactive
    az keyvault show --name mykeyvault --resource-group myresourcegroup --query id
    ```

    このコマンドにより、次のテキストのような値が返されます。

    ```text
    /subscriptions/{subscription-guid}/resourceGroups/myresourcegroup/providers/Microsoft.KeyVault/vaults/mykeyvault
    ```

## <a name="next-steps"></a>次のステップ

* [Resource Manager テンプレートと Resource Manager REST API を使用したリソースのデプロイ](../azure-resource-manager/templates/deploy-rest.md)。
* [Visual Studio での Azure リソース グループの作成とデプロイ](../azure-resource-manager/templates/create-visual-studio-deployment-project.md)。
