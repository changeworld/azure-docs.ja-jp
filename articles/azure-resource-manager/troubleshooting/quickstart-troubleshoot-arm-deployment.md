---
title: ARM テンプレート デプロイのトラブルシューティング
description: Azure Resource Manager テンプレート (ARM テンプレート) のデプロイを監視およびトラブルシューティングする方法について学習します。 アクティビティ ログとデプロイ履歴を紹介します。
ms.date: 11/01/2021
ms.topic: quickstart
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 2b2dae6d4f90410b8ce1f8fc455aa95ede0ab0d3
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/02/2021
ms.locfileid: "131091410"
---
# <a name="quickstart-troubleshoot-arm-template-deployments"></a>クイックスタート: ARM テンプレート デプロイのトラブルシューティング

このクイックスタートでは、Azure Resource Manager テンプレート (ARM テンプレート) デプロイ エラーのトラブルシューティング方法について説明します。 2 つのエラーがあるテンプレートを設定し、アクティビティ ログとデプロイ履歴を使用してエラーを修正する方法について説明します。

テンプレート デプロイに関連するエラーには、次の 2 種類があります。

- **検証エラー** デプロイ開始前に、ファイル中の構文エラーによって発生します。
- **デプロイ エラー** デプロイ プロセス中に発生し、API バージョンなどの正しくない値が原因で起きる場合があります。

この 2 種類のエラーでは、デプロイのトラブルシューティングに使用するエラー コードが返されます。 どちらの種類のエラーもアクティビティ ログに表示されます。 検証エラーは、デプロイが開始されていないため、デプロイ履歴には表示されません。

## <a name="prerequisites"></a>前提条件

このクイックスタートを実行するには、次のものが必要です。

- Azure サブスクリプションをお持ちでない場合は、開始する前に[無料アカウントを作成](https://azure.microsoft.com/free/)してください。
- [Visual Studio Code](https://code.visualstudio.com/) と最新の [Azure Resource Manager ツール拡張機能](https://marketplace.visualstudio.com/items?itemName=msazurermtools.azurerm-vscode-tools)。
- [Azure Cloud Shell](../templates/deploy-cloud-shell.md#deploy-local-template) を使用してローカル テンプレートをデプロイする方法を確認してください。

## <a name="create-a-template-with-errors"></a>エラーがあるテンプレートの作成

次のテンプレートには、検証エラーとデプロイ エラーが含まれています。 ストレージ アカウントをデプロイできるように、テンプレートのエラーをトラブルシューティングし、修正します。

サンプル テンプレートの両方のエラーが、次の行にあります。

```json
"apiVersion1": "2018-07-02",
```

1. Visual Studio Code を開き、 **[ファイル]**  >  **[新しいファイル]** の順に選択します。
1. テンプレートをコピーするには、 **[コピー]** を選択し、新しいファイルに貼り付けます。
1. **[ファイル]**  >  **[名前を付けて保存]** の順に選択し、ファイルに _azuredeploy.json_ という名前を付けて、自分のローカル コンピューターに保存します。

```json
{
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
      "defaultValue": "[resourceGroup().location]",
      "metadata": {
        "description": "Location for all resources."
      }
    }
  },
  "variables": {
    "storageAccountName": "[concat('store', uniquestring(resourceGroup().id))]"
  },
  "resources": [
    {
      "type": "Microsoft.Storage/storageAccounts",
      "apiVersion1": "2018-07-02",
      "name": "[variables('storageAccountName')]",
      "location": "[parameters('location')]",
      "sku": {
        "name": "[parameters('storageAccountType')]"
      },
      "kind": "StorageV2",
      "properties": {}
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

## <a name="deploy-the-template"></a>テンプレートのデプロイ

テンプレートを Cloud Shell にアップロードし、Azure CLI または Azure PowerShell を使用してデプロイします。

1. [Cloud Shell](https://shell.azure.com) にサインインします。
1. 左上隅から **[Bash]** または **[PowerShell]** を選択します。

    :::image type="content" source="media/quickstart-troubleshoot-arm-deployment/cloud-shell-upload.png" alt-text="シェルを選択し、ファイルをアップロードするための Azure Cloud Shell のスクリーンショット。":::

1. **[ファイルのアップロード/ダウンロード]** を選択し、_azuredeploy. json_ ファイルをアップロードします。
1. テンプレートをデプロイするには、次のコマンドをコピーし、シェル ウィンドウに貼り付けます。

    # <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

    ```azurepowershell
    $resourceGroupName = Read-Host -Prompt "Enter a resource group name"
    $location = Read-Host -Prompt "Enter the location (i.e. centralus)"
    New-AzResourceGroup -Name "$resourceGroupName" -Location "$location"
    New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateFile "$HOME/azuredeploy.json"
    Write-Host "Press [ENTER] to continue ..."
    ```

    # <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

    ```azurecli
    echo "Enter a resource group name:" &&
    read resourceGroupName &&
    echo "Enter the location (i.e. centralus):" &&
    read location &&
    az group create --name $resourceGroupName --location $location &&
    az deployment group create --resource-group $resourceGroupName --template-file $HOME/azuredeploy.json &&
    echo "Press [ENTER] to continue ..."
    ```

    ---

    > [!NOTE]
    > デプロイを実行するのと同じリソース グループ名と場所を使用します。 リソース グループの更新を求めるメッセージが表示されたら、 **[はい]** を選択します。

## <a name="troubleshoot-the-validation-error"></a>検証エラーのトラブルシューティング

検証エラーでは、エラー コードが `InvalidRequestContent` であるエラー メッセージがシェルに表示されます。

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```Output
New-AzResourceGroupDeployment: 6:49:10 PM - Error: Code=InvalidRequestContent; Message=The request content
was invalid and could not be deserialized: 'Could not find member 'apiVersion1' on object of type
'TemplateResource'. Path 'properties.template.resources[0].apiVersion1', line 34, position 24.'.

New-AzResourceGroupDeployment: The deployment validation failed
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```Output
{"error":{"code":"InvalidRequestContent","message":"The request content was invalid and could not be
deserialized: 'Could not find member 'apiVersion1' on object of type 'TemplateResource'.
Path 'properties.template.resources[0].apiVersion1', line 32, position 20.'."}}
```

---

### <a name="fix-the-validation-error"></a>検証エラーを修正する

エラーの原因は、`apiVersion1` が正しくないことです。 Visual Studio Code を使用して、`apiVersion1` を `apiVersion` に変更し、テンプレートを保存します。 [テンプレートをデプロイする](#deploy-the-template)手順に従ってテンプレートをアップロードし、デプロイを再実行します。

## <a name="troubleshoot-the-deployment-error"></a>デプロイ エラーのトラブルシューティング

デプロイ エラーでは、エラー コードが `NoRegisteredProviderFound` であるエラー メッセージがシェルに表示されます。 リソース グループの **[デプロイ]** と **[アクティビティ ログ]** でもエラーを表示できます。

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```Output
New-AzResourceGroupDeployment: 6:51:48 PM - The deployment 'azuredeploy' failed with error(s).
Showing 1 out of 1 error(s).
Status Message: No registered resource provider found for location 'centralus' and API version '2018-07-02'
for type 'storageAccounts'. The supported api-versions are '2021-06-01, 2021-05-01, 2021-04-01, 2021-02-01,
2021-01-01, 2020-08-01-preview, 2019-06-01, 2019-04-01, 2018-11-01, 2018-07-01, 2018-03-01-preview,
2018-02-01, 2017-10-01, 2017-06-01, 2016-12-01, 2016-05-01, 2016-01-01, 2015-06-15, 2015-05-01-preview'.
The supported locations are 'eastus, eastus2, westus, westeurope, eastasia, southeastasia, japaneast,
japanwest, northcentralus, southcentralus, centralus, northeurope, brazilsouth, australiaeast,
australiasoutheast, southindia, centralindia, westindia, canadaeast, canadacentral, westus2, westcentralus,
uksouth, ukwest, koreacentral, koreasouth, francecentral, australiacentral, southafricanorth, uaenorth,
switzerlandnorth, germanywestcentral, norwayeast, westus3, jioindiawest'.
(Code:NoRegisteredProviderFound)
CorrelationId: 11111111-1111-1111-1111-111111111111
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```Output
{"status":"Failed","error":{"code":"DeploymentFailed","message":"At least one resource deployment operation
failed. Please list deployment operations for details. Please see https://aka.ms/DeployOperations for usage
details.", "details":[{"code":"BadRequest","message":"{\r\n  \"error\": {\r\n
\"code\": \"NoRegisteredProviderFound\", \r\n    \"message\": \"No registered resource provider found for
location 'centralus' and API version '2018-07-02' for type 'storageAccounts'. The supported api-versions
are '2021-06-01, 2021-05-01, 2021-04-01, 2021-02-01, 2021-01-01, 2020-08-01-preview, 2019-06-01,
2019-04-01, 2018-11-01, 2018-07-01, 2018-03-01-preview, 2018-02-01, 2017-10-01, 2017-06-01, 2016-12-01,
2016-05-01, 2016-01-01, 2015-06-15, 2015-05-01-preview'.
The supported locations are 'eastus, eastus2, westus, westeurope, eastasia, southeastasia, japaneast,
japanwest, northcentralus, southcentralus, centralus, northeurope, brazilsouth, australiaeast,
australiasoutheast, southindia, centralindia, westindia, canadaeast, canadacentral, westus2, westcentralus,
uksouth, ukwest, koreacentral, koreasouth, francecentral, australiacentral, southafricanorth, uaenorth,
switzerlandnorth, germanywestcentral, norwayeast, westus3, jioindiawest'.\"\r\n  }\r\n}"}]}}
```

---

### <a name="deployments"></a>デプロイメント

デプロイ エラーは、次の手順を使用して、Azure portal で表示できます。

1. [Azure Portal](https://portal.azure.com) にサインインします。
1. 検索ボックスに「_リソース グループ_」と入力し、 **[リソース グループ]** を選択します。

    :::image type="content" source="./media/quickstart-troubleshoot-arm-deployment/search-box.png" alt-text="Azure portal の検索ボックスのスクリーンショット。":::

1. デプロイのリソース グループ名を選択します。
1. **[概要]** に移動し、リンク **[1 失敗]** を選択します。

    :::image type="content" source="./media/quickstart-troubleshoot-arm-deployment/arm-template-deployment-error.png" alt-text="失敗したデプロイへのリンクが強調表示されているスクリーンショット。":::

1. **[デプロイ]** で **[エラーの詳細]** を選択します。

    :::image type="content" source="./media/quickstart-troubleshoot-arm-deployment/arm-template-deployment-error-details.png" alt-text="エラーの詳細への、失敗したデプロイのリンクのスクリーンショット。":::

    エラー メッセージは、デプロイ コマンドの出力と同じです。

    :::image type="content" source="./media/quickstart-troubleshoot-arm-deployment/arm-template-deployment-error-summary.png" alt-text="デプロイ エラーの詳細のスクリーンショット。":::

### <a name="activity-log"></a>アクティビティ ログ

リソース グループのアクティビティ ログでエラーを見つけることもできます。 アクティビティ ログに最新のデプロイ情報が表示されるまでに、数分かかります。

1. リソース グループで、 **[アクティビティ ログ]** を選択します。
1. ログを見つけ、表示するログを選択するには、フィルターを使用します。

    :::image type="content" source="./media/quickstart-troubleshoot-arm-deployment/arm-template-deployment-activity-log.png" alt-text="失敗したデプロイが強調表示されている、リソース グループのアクティビティ ログのスクリーンショット。":::

1. ログを選択すると、詳細が表示されます。

    :::image type="content" source="./media/quickstart-troubleshoot-arm-deployment/arm-template-deployment-activity-log-details.png" alt-text="失敗したデプロイのエラー メッセージを示す、アクティビティ ログの詳細のスクリーンショット。":::

### <a name="fix-the-deployment-error"></a>デプロイ エラーの修正

**"登録済みのリソース プロバイダーが見つかりませんでした"** というデプロイ エラーは、正しくない API バージョンによって発生します。 Visual Studio Code を使用して API バージョンを `2021-04-01` などの有効な値に変更し、テンプレートを保存します。 [テンプレートをデプロイする](#deploy-the-template)手順に従ってテンプレートをアップロードし、デプロイを再実行します。

検証エラーとデプロイ エラーを修正すると、ストレージ アカウントが作成されます。 リソース グループの **[概要]** に移動し、リソースを表示します。 **[デプロイ]** と **[アクティビティ ログ]** に、成功したデプロイが表示されます。

## <a name="clean-up-resources"></a>リソースをクリーンアップする

Azure リソースが必要なくなったときは、リソース グループを削除します。 リソース グループは Cloud Shell かポータルで削除できます。

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

山かっこを含めて、`<rgname>` をリソース グループ名に置き換えてください。

```azurepowershell
Remove-AzResourceGroup -Name <rgname>
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

山かっこを含めて、`<rgname>` をリソース グループ名に置き換えてください。

```azurecli
az group delete --name <rgname>
```

---

ポータルでリソース グループを削除するには、このステップに従います。

1. Azure portal で、検索ボックスに「**リソース グループ**」と入力します。
1. **[名前でフィルター]** フィールドに、リソース グループ名を入力します。
1. リソース グループ名を選択します。
1. **[リソース グループの削除]** を選択します。
1. 削除を確認するには、リソース グループ名を入力して **[削除]** を選択します。

## <a name="next-steps"></a>次のステップ

このクイックスタートでは、ARM テンプレート デプロイ エラーのトラブルシューティング方法について説明しました。

> [!div class="nextstepaction"]
> [よくある Azure デプロイ エラーのトラブルシューティング](common-deployment-errors.md)に関する記事
