---
title: Azure Resource Manager テンプレートでワークスペースを作成する
titleSuffix: Azure Machine Learning
description: Azure Resource Manager テンプレートを使用して新しい Azure Machine Learning ワークスペースを作成する方法について説明します。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: how-to
ms.author: larryfr
author: Blackmist
ms.date: 05/19/2020
ms.custom: seoapril2019
ms.openlocfilehash: 39c694f4e2afbf5d781a8fde43a7db9c4a255466
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/02/2020
ms.locfileid: "85392670"
---
# <a name="use-an-azure-resource-manager-template-to-create-a-workspace-for-azure-machine-learning"></a>Azure Resource Manager テンプレートを使用して Azure Machine Learning のワークスペースを作成します。

[!INCLUDE [aml-applies-to-basic-enterprise-sku](../../includes/aml-applies-to-basic-enterprise-sku.md)]
<br>

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
> 新しいコンテナー レジストリまたはストレージ アカウントを作成するのではなく、Azure Resource Manager テンプレートで既存のものを参照することもできます。 ただし、使用するコンテナー レジストリでは、__管理者アカウント__を有効にする必要があります。 管理者アカウントを有効にする方法の詳細については、「[管理者アカウント](/azure/container-registry/container-registry-authentication#admin-account)」を参照してください。

[!INCLUDE [machine-learning-delete-acr](../../includes/machine-learning-delete-acr.md)]

テンプレートの詳細については、次の記事を参照してください。

* [Azure リソース マネージャーのテンプレートの作成](../azure-resource-manager/templates/template-syntax.md)
* [Azure Resource Manager テンプレートを使用したアプリケーションのデプロイ](../azure-resource-manager/templates/deploy-powershell.md)
* [Microsoft.MachineLearningServices resource types (Microsoft.MachineLearningServices リソースの種類)](https://docs.microsoft.com/azure/templates/microsoft.machinelearningservices/allversions)

### <a name="advanced-template"></a>高度なテンプレート

次のテンプレート例では、3 つの設定を使用してワークスペースを作成する方法を示します。

* ワークスペースの高機密性の設定を有効にする
* ワークスペースの暗号化を有効にする
* 既存の Azure Key Vault を使用して、カスタマー マネージド キーを取得する

詳細については、「[保存時の暗号化](concept-enterprise-security.md#encryption-at-rest)」を参照してください。

> [!IMPORTANT]
> このテンプレートを使用する前に、サブスクリプションで満たしている必要がある特定の要件がいくつかあります。
> * __Azure Machine Learning__ アプリケーションは、Azure サブスクリプションの__共同作成者__である必要があります。
> * 暗号化キーを含む既存の Azure Key Vault が必要です。
> * __取得__、__ラップ__、__ラップ解除__のアクセス権を __Azure Cosmos DB__ アプリケーションに付与するためのアクセス ポリシーを Azure Key Vault に設定する必要があります。
> * Azure Key Vault は、Azure Machine Learning ワークスペースの作成を計画しているリージョンと同じリージョンにある必要があります。

__共同作成者として Azure Machine Learning アプリを追加するには__、次のコマンドを使用します。

1. CLI から Azure に対して認証するには、次のコマンドを使用します。

    ```azurecli-interactive
    az login
    ```
    
    [!INCLUDE [subscription-login](../../includes/machine-learning-cli-subscription.md)]

1. Azure Machine Learning アプリのオブジェクト ID を取得するには、次のコマンドを使用します。 この値は、Azure サブスクリプションごとに異なる場合があります。

    ```azurecli-interactive
    az ad sp list --display-name "Azure Machine Learning" --query '[].[appDisplayName,objectId]' --output tsv
    ```

    このコマンドでは、GUID であるオブジェクト ID が返されます。

1. オブジェクト ID を共同作成者としてサブスクリプションに追加するには、次のコマンドを使用します。 `<object-ID>` を、前の手順の GUID に置き換えます。 `<subscription-ID>` を Azure サブスクリプションの名前または ID に置き換えます。

    ```azurecli-interactive
    az role assignment create --role 'Contributor' --assignee-object-id <object-ID> --subscription <subscription-ID>
    ```

__Azure Key Vault にキーを追加するには__、「__Azure CLI を使用した Key Vault の管理__」という記事の「[キー コンテナーにキー、シークレット、または証明書を追加する](../key-vault/general/manage-with-cli2.md#adding-a-key-secret-or-certificate-to-the-key-vault)」セクションにある情報を使用してください。

__アクセス ポリシーをキー コンテナーに追加するには、次のコマンドを使用します__。

1. Azure Cosmos DB アプリのオブジェクト ID を取得するには、次のコマンドを使用します。 この値は、Azure サブスクリプションごとに異なる場合があります。

    ```azurecli-interactive
    az ad sp list --display-name "Azure Cosmos DB" --query '[].[appDisplayName,objectId]' --output tsv
    ```
    
    このコマンドでは、GUID であるオブジェクト ID が返されます。

1. ポリシーを設定するには、次のコマンドを使用します。 `<keyvault-name>` を、既存の Azure Key Vault の名前に置き換えます。 `<object-ID>` を、前の手順の GUID に置き換えます。

    ```azurecli-interactive
    az keyvault set-policy --name <keyvault-name> --object-id <object-ID> --key-permissions get unwrapKey wrapKey
    ```

`cmk_keyvault` (Key Vault の ID) およびこのテンプレートで必要な `resource_cmk_uri` (キー URI) パラメーターの__値を取得するには__、次の手順を使用します。

1. Key Vault ID を取得するには、次のコマンドを使用します。

    ```azurecli-interactive
    az keyvault show --name mykeyvault --resource-group myresourcegroup --query "id"
    ```

    このコマンドでは `/subscriptions/{subscription-guid}/resourceGroups/myresourcegroup/providers/Microsoft.KeyVault/vaults/mykeyvault` のような値が返されます。

1. カスタマー マネージド キーの URI の値を取得するには、次のコマンドを使用します。

    ```azurecli-interactive
    az keyvault key show --vault-name mykeyvault --name mykey --query "key.kid"
    ```

    このコマンドでは `https://mykeyvault.vault.azure.net/keys/mykey/{guid}` のような値が返されます。

__テンプレートの例__

:::code language="json" source="~/quickstart-templates/201-machine-learning-encrypted-workspace/azuredeploy.json":::

> [!IMPORTANT]
> ワークスペースが作成されたら、機密データ、暗号化、Key Vault ID、またはキー識別子の設定を変更することはできません。 これらの値を変更するには、新しい値を使用して新しいワークスペースを作成する必要があります。

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
