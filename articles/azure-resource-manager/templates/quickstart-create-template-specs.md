---
title: テンプレート スペックの作成とデプロイ
description: ARM テンプレートからテンプレート スペックを作成する方法について説明します。 次に、サブスクリプションのリソース グループにテンプレート スペックをデプロイします。
author: tfitzmac
ms.date: 12/01/2020
ms.topic: quickstart
ms.author: tomfitz
ms.openlocfilehash: 03cf2013f1cec9722af5d7e72285d9f11d8a6bc1
ms.sourcegitcommit: 84e3db454ad2bccf529dabba518558bd28e2a4e6
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/02/2020
ms.locfileid: "96518959"
---
# <a name="quickstart-create-and-deploy-template-spec-preview"></a>クイック スタート:テンプレート スペックの作成とデプロイ (プレビュー)

このクイックスタートでは、Azure Resource Manager テンプレート (ARM テンプレート) を[テンプレート スペック](template-specs.md)にパッケージ化してから、そのテンプレート スペックをデプロイする方法について説明します。テンプレート スペックには、ストレージ アカウントをデプロイする ARM テンプレートが含まれています。

## <a name="prerequisites"></a>前提条件

アクティブなサブスクリプションが含まれる Azure アカウント。 [無料でアカウントを作成できます](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

> [!NOTE]
> Template Specs は現在プレビューの段階です。 Azure PowerShell でこれを使用するには、[バージョン 5.0.0 以降](/powershell/azure/install-az-ps)をインストールする必要があります。 Azure CLI でこれを使用するには、[バージョン 2.14.2 以降](/cli/azure/install-azure-cli)を使用します。

## <a name="create-template-spec"></a>テンプレート スペックの作成

テンプレート スペックは、**Microsoft.Resources/templateSpecs** という名前のリソースの種類です。 テンプレート スペックを作成するには、Azure portal、Azure PowerShell、Azure CLI、または ARM テンプレートを使用できます。 すべてのオプションで、テンプレート スペック内にパッケージ化された ARM テンプレートが必要です。

PowerShell と CLI では、ARM テンプレートはパラメーターとしてコマンドに渡されます。 ARM テンプレートを使用すると、テンプレート スペック内にパッケージ化する ARM テンプレートがテンプレート スペック定義内に埋め込まれます。

これらのオプションを次に示します。

# <a name="portal"></a>[ポータル](#tab/azure-portal)

1. [Azure portal](https://portal.azure.com) にサインインします。
1. 画面上部の **[Search resources, services and docs]\(リソース、サービス、ドキュメントを検索します\)** に、「**テンプレート スペック**」と入力し、 **[Template specs]\(テンプレートの仕様\)** を選択します。
1. **[Create template spec]\(テンプレート スペックの作成\)** を選択します。
1. 次の値を選択または入力します。

    - **[名前]** : テンプレート スペックの名前を入力します。たとえば、**storageSpec**
    - **[サブスクリプション]** : テンプレート スペックの作成に使用する Azure サブスクリプションを選択します。
    - **[リソース グループ]** : **[新規作成]** を選択し、新しいリソース グループの名前を入力します。  たとえば、**templateSpecRG**。
    - **場所**: リソース グループの場所を選択します。 たとえば、 **[米国西部 2]** 。
    - **[バージョン]** : テンプレート スペックのバージョンを入力します。たとえば、**1.0** または **v1.0**。

1. **Next:テンプレートの編集** を選択します。
1. テンプレートの内容を次の JSON に置き換えます。

    :::code language="json" source="~/quickstart-templates/101-storage-account-create/azuredeploy.json":::

    これは、テンプレート スペック内にパッケージ化されるテンプレートです。
1. **[確認および作成]** を選択します。
1. **［作成］** を選択します

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

1. PowerShell でテンプレート スペックを作成する場合は、ローカル テンプレートを渡すことができます。 次のテンプレートをコピーし、**azuredeploy.json** というファイル名でローカルに保存します。 このクイックスタートでは、パス **c:\Templates\azuredeploy.json** に保存したことを想定していますが、任意のパスを使用できます。

    :::code language="json" source="~/quickstart-templates/101-storage-account-create/azuredeploy.json":::

1. テンプレート スペックを含む新しいリソース グループを作成します。

    ```azurepowershell
    New-AzResourceGroup `
      -Name templateSpecRG `
      -Location westus2
    ```

1. 次に、そのリソース グループ内にテンプレート スペックを作成します。 新しいテンプレート スペックに **storageSpec** という名前を付けます。

    ```azurepowershell
    New-AzTemplateSpec `
      -Name storageSpec `
      -Version "1.0" `
      -ResourceGroupName templateSpecRG `
      -Location westus2 `
      -TemplateFile "c:\Templates\azuredeploy.json"
    ```

# <a name="cli"></a>[CLI](#tab/azure-cli)

1. CLI でテンプレート スペックを作成する場合は、ローカル テンプレートを渡すことができます。 次のテンプレートをコピーし、**azuredeploy.json** というファイル名でローカルに保存します。 このクイックスタートでは、パス **c:\Templates\azuredeploy.json** に保存したことを想定していますが、任意のパスを使用できます。

    :::code language="json" source="~/quickstart-templates/101-storage-account-create/azuredeploy.json":::

1. テンプレート スペックを含む新しいリソース グループを作成します。

    ```azurecli
    az group create \
      --name templateSpecRG \
      --location westus2
    ```

1. 次に、そのリソース グループ内にテンプレート スペックを作成します。 新しいテンプレート スペックに **storageSpec** という名前を付けます。

    ```azurecli
    az ts create \
      --name storageSpec \
      --version "1.0" \
      --resource-group templateSpecRG \
      --location "westus2" \
      --template-file "c:\Templates\azuredeploy.json"
    ```

# <a name="arm-template"></a>[ARM テンプレート](#tab/azure-resource-manager)

1. ARM テンプレートを使用してテンプレート スペックを作成する場合は、テンプレートはリソース定義に埋め込まれます。 次のテンプレートをコピーし、**azuredeploy.json** としてローカルに保存します。 このクイックスタートでは、パス **c:\Templates\azuredeploy.json** に保存したことを想定していますが、任意のパスを使用できます。

    > [!NOTE]
    > 埋め込みテンプレートの場合、すべての[テンプレート式](template-expressions.md)を 2 番目の左角かっこでエスケープする必要があります。 `"[` ではなく `"[[` を使用します。 JSON 配列の場合は、やはり 1 つの左かっこを使用します。

    ```json
    {
      "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {},
      "functions": [],
      "variables": {},
      "resources": [
        {
          "type": "Microsoft.Resources/templateSpecs",
          "apiVersion": "2019-06-01-preview",
          "name": "storageSpec",
          "location": "westus2",
          "properties": {
            "displayName": "Storage template spec"
          },
          "tags": {},
          "resources": [
            {
              "type": "versions",
              "apiVersion": "2019-06-01-preview",
              "name": "1.0",
              "location": "westus2",
              "dependsOn": [ "storageSpec" ],
              "properties": {
                "template": {
                  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
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
                      "defaultValue": "[[resourceGroup().location]",
                      "metadata": {
                        "description": "Location for all resources."
                      }
                    }
                  },
                  "variables": {
                    "storageAccountName": "[[concat('store', uniquestring(resourceGroup().id))]"
                  },
                  "resources": [
                    {
                      "type": "Microsoft.Storage/storageAccounts",
                      "apiVersion": "2019-04-01",
                      "name": "[[variables('storageAccountName')]",
                      "location": "[[parameters('location')]",
                      "sku": {
                        "name": "[[parameters('storageAccountType')]"
                      },
                      "kind": "StorageV2",
                      "properties": {}
                    }
                  ],
                  "outputs": {
                    "storageAccountName": {
                      "type": "string",
                      "value": "[[variables('storageAccountName')]"
                    }
                  }
                }
              },
              "tags": {}
            }
          ]
        }
      ],
      "outputs": {}
    }
    ```

1. Azure CLI または PowerShell を使用して、新しいリソース グループを作成します。

    ```azurepowershell
    New-AzResourceGroup `
      -Name templateSpecRG `
      -Location westus2
    ```

    ```azurecli
    az group create \
      --name templateSpecRG \
      --location westus2
    ```

1. Azure CLI または PowerShell を使用してテンプレートをデプロイします。

    ```azurepowershell
    New-AzResourceGroupDeployment `
      -ResourceGroupName templateSpecRG `
      -TemplateFile "c:\Templates\azuredeploy.json"
    ```

    ```azurecli
    az deployment group create \
      --name templateSpecRG \
      --template-file "c:\Templates\azuredeploy.json"
    ```

---

## <a name="deploy-template-spec"></a>テンプレート スペックのデプロイ

テンプレート スペックをデプロイする準備ができました。テンプレート スペックのデプロイは、Azure PowerShell または Azure CLI でテンプレート スペックのリソース ID を渡す点を除けば、含まれているテンプレートをデプロイするのと同じです。 同じデプロイ コマンドを使用し、必要に応じて、テンプレート スペックのパラメーター値を渡します。

# <a name="portal"></a>[ポータル](#tab/azure-portal)

1. Azure portal で、最後の手順で作成したリソース グループを開きます。  たとえば、**templateSpecRG**。
1. 作成したテンプレート スペックを選択します。 たとえば、**storageSpec**。
1. **[デプロイ]** を選択します。
1. 次の値を選択または入力します。

    - **[サブスクリプション]** : リソースを作成するための Azure サブスクリプションを選択します。
    - **[リソース グループ]** : **[新規作成]** を選択し、「**storageRG**」と入力します。
    - **[Storage Account Type]\(ストレージ アカウントの種類\)** : **[Standard_GRS]** を選択します。

    新しいリソース グループを作成し、テンプレート スペック内のテンプレートを新しいリソース グループにデプロイします。

1. **[Review + create]\(レビュー + 作成\)** を選択します。
1. **［作成］** を選択します

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

1. 新しいストレージ アカウントを格納するリソース グループを作成します。

    ```azurepowershell
    New-AzResourceGroup `
      -Name storageRG `
      -Location westus2
    ```

1. テンプレート スペックのリソース ID を取得します。

    ```azurepowershell
    $id = (Get-AzTemplateSpec -ResourceGroupName templateSpecRG -Name storageSpec -Version "1.0").Versions.Id
    ```

1. テンプレート スペックをデプロイします。

    ```azurepowershell
    New-AzResourceGroupDeployment `
      -TemplateSpecId $id `
      -ResourceGroupName storageRG
    ```

1. ARM テンプレートの場合とまったく同じようにパラメーターを指定します。 ストレージ アカウントの種類のパラメーターを指定して、テンプレート スペックを再デプロイします。

    ```azurepowershell
    New-AzResourceGroupDeployment `
      -TemplateSpecId $id `
      -ResourceGroupName storageRG `
      -storageAccountType Standard_GRS
    ```

# <a name="cli"></a>[CLI](#tab/azure-cli)

1. 新しいストレージ アカウントを格納するリソース グループを作成します。

    ```azurecli
    az group create \
      --name storageRG \
      --location westus2
    ```

1. テンプレート スペックのリソース ID を取得します。

    ```azurecli
    id = $(az ts show --name storageSpec --resource-group templateSpecRG --version "1.0" --query "id")
    ```

    > [!NOTE]
    > Windows PowerShell におけるテンプレート スペック ID の取得と変数への代入には既知の問題があります。

1. テンプレート スペックをデプロイします。

    ```azurecli
    az deployment group create \
      --resource-group storageRG \
      --template-spec $id
    ```

1. ARM テンプレートの場合とまったく同じようにパラメーターを指定します。 ストレージ アカウントの種類のパラメーターを指定して、テンプレート スペックを再デプロイします。

    ```azurecli
    az deployment group create \
      --resource-group storageRG \
      --template-spec $id \
      --parameters storageAccountType='Standard_GRS'
    ```

# <a name="arm-template"></a>[ARM テンプレート](#tab/azure-resource-manager)

1. 次のテンプレートをコピーし、**storage.json** というファイル名でローカルに保存します。

    ```json
       {
      "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {},
      "functions": [],
      "variables": {},
      "resources": [
        {
          "type": "Microsoft.Resources/deployments",
          "apiVersion": "2020-06-01",
          "name": "demo",
          "properties": {
            "templateLink": {
              "id": "[resourceId('templateSpecRG', 'Microsoft.Resources/templateSpecs/versions', 'storageSpec', '1.0')]"
            },
            "parameters": {
            },
            "mode": "Incremental"
          }
        }
      ],
      "outputs": {}
    }
    ```

1. Azure CLI または PowerShell を使用して、ストレージ アカウント用の新しいリソース グループを作成します。

    ```azurepowershell
    New-AzResourceGroup `
      -Name storageRG `
      -Location westus2
    ```

    ```azurecli
    az group create \
      --name storageRG \
      --location westus2
    ```

1. Azure CLI または PowerShell を使用してテンプレートをデプロイします。

    ```azurepowershell
    New-AzResourceGroupDeployment `
      -ResourceGroupName storageRG `
      -TemplateFile "c:\Templates\storage.json"
    ```

    ```azurecli
    az deployment group create \
      --name storageRG \
      --template-file "c:\Templates\storage.json"
    ```

---

## <a name="grant-access"></a>アクセス権の付与

組織内の他のユーザーがテンプレート スペックをデプロイできるようにするには、そのユーザーに読み取りアクセス権を付与する必要があります。 共有するテンプレート スペックを含むリソース グループの Azure AD グループに閲覧者ロールを割り当てることができます。 詳細については、[チュートリアル: Azure PowerShell を使用して Azure リソースへのアクセス権をグループに付与する](../../role-based-access-control/tutorial-role-assignments-group-powershell.md)」を参照してください。

## <a name="clean-up-resources"></a>リソースをクリーンアップする

このクイックスタートでデプロイしたリソースをクリーンアップするには、作成したリソース グループを両方とも削除します。

1. Azure portal で、左側のメニューから [リソース グループ] を選択します。

1. [名前でフィルター] フィールドに、リソース グループ名 (templateSpecRG and storageRG) を入力します。

1. リソース グループ名を選択します。

1. トップ メニューから [リソース グループの削除] を選択します。

## <a name="next-steps"></a>次のステップ

リンクされたテンプレートを含むテンプレート スペックを作成する方法については、「[リンクされたテンプレートのテンプレート スペックを作成する](template-specs-create-linked.md)」を参照してください。
