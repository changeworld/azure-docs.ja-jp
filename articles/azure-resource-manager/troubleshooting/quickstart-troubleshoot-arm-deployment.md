---
title: ARM テンプレート デプロイのトラブルシューティング
description: Azure Resource Manager テンプレート (ARM テンプレート) デプロイのトラブルシューティング方法について説明します。
ms.date: 11/08/2021
ms.topic: quickstart
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: ce9bf9ee097a41b3e3ecbc0cd515dd58cf30ea82
ms.sourcegitcommit: 61f87d27e05547f3c22044c6aa42be8f23673256
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/09/2021
ms.locfileid: "132062060"
---
# <a name="quickstart-troubleshoot-arm-template-deployments"></a>クイックスタート: ARM テンプレート デプロイのトラブルシューティング

このクイックスタートでは、Azure Resource Manager テンプレート (ARM テンプレート) デプロイ エラーのトラブルシューティング方法について説明します。 エラーを含むテンプレートを設定し、エラーを修正する方法を学びます。

デプロイに関するエラーには 3 種類あります。

- **検証エラー** デプロイ開始前に、ファイル中の構文エラーによって発生します。 これらのエラーはエディターで特定できます。
- **プレフライト検証エラー** は、デプロイ コマンドが実行されてもリソースがデプロイされない場合に発生します。 これらのエラーは、デプロイを開始しなくても検出されます。 たとえば、パラメーターの値が正しくない場合は、プレフライト検証でエラーが検出されます。
- **デプロイ エラー** デプロイのプロセスで発生し、デプロイの進行状況を確認することでのみ検出できます。

どの種類のエラーでも、デプロイのトラブルシューティングに使用できるエラー コードが出力されます。 検証とプレフライトのエラーはアクティビティ ログに表示されますが、デプロイ履歴には表示されません。

## <a name="prerequisites"></a>前提条件

このクイックスタートを実行するには、次のものが必要です。

- Azure サブスクリプションをお持ちでない場合は、開始する前に[無料アカウントを作成](https://azure.microsoft.com/free/)してください。
- [Visual Studio Code](https://code.visualstudio.com/) と最新の [Azure Resource Manager ツール拡張機能](https://marketplace.visualstudio.com/items?itemName=msazurermtools.azurerm-vscode-tools)。
- 最新バージョンの [Azure PowerShell](/powershell/azure/install-az-ps) または [Azure CLI](/cli/azure/install-azure-cli) をインストールします。

## <a name="create-a-template-with-errors"></a>エラーがあるテンプレートの作成

次のテンプレートをコピーしてローカルに保存します。 このファイルを使用して、検証エラー、プレフライト エラー、デプロイ エラーのトラブルシューティングを行います。 このクイックスタートでは、ファイルに _troubleshoot.json_ という名前を付けていますが、任意の名前を使用できます。

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameterss": {
    "storageAccountType": {
      "type": "string",
      "defaultValue": "Standard_LRS",
      "allowedValues": [
        "Standard_LRS",
        "Standard_GRS",
        "Standard_ZRS",
        "Premium_LRS"
      ]
    },
    "location": {
      "type": "string",
      "defaultValue": "[resourceGroup().location]"
    },
    "prefixName": {
      "type": "string"
    }
  },
  "variables": {
    "storageAccountName": "[concat(parameters('prefixName'), uniquestring(resourceGroup().id))]"
  },
  "resources": [
    {
      "type": "Microsoft.Storage/storageAccounts",
      "apiVersion": "2021-04-01",
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
    },
    "vnetResult": {
      "type": "object",
      "value": "[reference(resourceId('Microsoft.Network/virtualNetworks', 'doesnotexist'), '2021-03-01', 'Full')]"
    }
  }
}
```

## <a name="fix-validation-error"></a>検証エラーを修正する

Visual Studio Code でファイルを開きます。 `parameterss:` の下の波線はエラーを示しています。 検証エラーを表示するために、エラーにカーソルを合わせます。

:::image type="content" source="media/quickstart-troubleshoot-arm-deployment/validation-error.png" alt-text="Visual Studio Code でのテンプレートの検証エラーのスクリーンショット。":::

`variables` と `resources` に _undefined parameter reference_ のエラーがあるのがわかります。 テンプレートの検証エラーを表示するには、 **[表示]**  >  **[Problems]\(問題\)** を選択します。

:::image type="content" source="media/quickstart-troubleshoot-arm-deployment/validation-undefined-parameter.png" alt-text="undefined parameter reference エラーを示す Visual Studio Code のスクリーンショット。":::

このすべてのエラーは、要素名のスペルが正しくないために発生します。

```json
"parameterss": {
```

エラー メッセージは、_Template validation failed: Could not find member 'parameterss' on object of type 'Template'. Path 'parameterss', line 4, position 16_ となっています。

[parameters](../templates/syntax.md#parameters) の ARM テンプレート構文は、`parameters` が正しい要素名であることを示しています。

検証エラーと _undefined parameter reference_ エラーを修正するにはスペルを修正し、ファイルを保存します。

```json
"parameters": {
```

## <a name="fix-preflight-error"></a>プレフライト エラーを修正する

プレフライト検証エラーを作成するには、`prefixName` パラメーターに正しくない値を使用します。

このクイックスタートではリソース グループ名に _troubleshootRG_ を使用していますが、任意の名前を使用できます。

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli
az group create --name troubleshootRG --location westus
az deployment group create \
  --resource-group troubleshootRG \
  --template-file troubleshoot.json \
  --parameters prefixName=long!!StoragePrefix
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
New-AzResourceGroup -Name troubleshootRG -Location westus
New-AzResourceGroupDeployment `
  -ResourceGroupName troubleshootRG `
  -TemplateFile troubleshoot.json `
  -prefixName long!!StoragePrefix
```

---

テンプレートはプレフライト検証に失敗し、デプロイが実行されません。 `prefixName` が 11 文字を超え、特殊文字と大文字を含んでいます。

ストレージ名は 3 から 24 文字までとし、小文字と数字のみを使用する必要があります。 プレフィックス値によって無効なストレージ名が作成されました。 詳細については、「[ストレージ アカウント名のエラーの解決](error-storage-account-name.md)」を参照してください。 プレフライト エラーを修正するには、使用するプレフィックスを 11 文字以下にし、小文字または数字のみを含めます。

デプロイが実行されなかったため、デプロイ履歴はありません。

:::image type="content" source="media/quickstart-troubleshoot-arm-deployment/preflight-no-deploy.png" alt-text="プレフライト エラーのためにデプロイが実行されなかったことを示すリソース グループの概要のスクリーンショット。":::

アクティビティ ログにプレフライト エラーが表示されます。 ログを選択するとエラーの詳細が表示されます。

:::image type="content" source="media/quickstart-troubleshoot-arm-deployment/preflight-activity-log.png" alt-text="プレフライト エラーが発生しているリソース グループのアクティビティ ログのスクリーンショット。":::

## <a name="fix-deployment-error"></a>デプロイ エラーを修正する

`storage` のような有効なプレフィックス値を使用してデプロイを実行します。

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli
az group create --name troubleshootRG --location westus
az deployment group create \
  --resource-group troubleshootRG \
  --template-file troubleshoot.json \
  --parameters prefixName=storage
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
New-AzResourceGroup -Name troubleshootRG -Location westus
New-AzResourceGroupDeployment `
  -ResourceGroupName troubleshootRG `
  -TemplateFile troubleshoot.json `
  -prefixName storage
```

---

デプロイが始まり、デプロイ履歴に表示されます。 `outputs` がリソース グループに存在しない仮想ネットワークを参照しているため、デプロイは失敗します。 ただし、ストレージ アカウントにはエラーがなかったため、リソースはデプロイされました。 デプロイ履歴には、デプロイが失敗したことが表示されます。

:::image type="content" source="media/quickstart-troubleshoot-arm-deployment/deployment-failed.png" alt-text="デプロイの失敗を示すリソース グループの概要のスクリーンショット。":::

デプロイ エラーを修正するには、有効なリソースを使用するように reference 関数を変更します。 詳細については、「[リソースが見つからないエラーを解決する](error-not-found.md)」を参照してください。 このクイック スタートでは、`vnetResult` の前のコンマとすべての `vnetResult` を削除します。 ファイルを保存し、デプロイを再実行します。

```json
"vnetResult": {
  "type": "object",
  "value": "[reference(resourceId('Microsoft.Network/virtualNetworks', 'doesnotexist'), '2021-03-01', 'Full')]"
}
```

検証、プレフライト、デプロイのエラーが修正された後、次のテンプレートによってストレージ アカウントがデプロイされます。 デプロイ履歴とアクティビティ ログに、デプロイが成功したことが表示されます。

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
      ]
    },
    "location": {
      "type": "string",
      "defaultValue": "[resourceGroup().location]"
    },
    "prefixName": {
      "type": "string"
    }
  },
  "variables": {
    "storageAccountName": "[concat(parameters('prefixName'), uniquestring(resourceGroup().id))]"
  },
  "resources": [
    {
      "type": "Microsoft.Storage/storageAccounts",
      "apiVersion": "2021-04-01",
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

## <a name="clean-up-resources"></a>リソースをクリーンアップする

Azure リソースが必要なくなったときは、リソース グループを削除します。

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli
az group delete --name troubleshootRG
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
Remove-AzResourceGroup -Name troubleshootRG
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
