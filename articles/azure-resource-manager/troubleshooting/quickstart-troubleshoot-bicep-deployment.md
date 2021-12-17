---
title: Bicep ファイルのデプロイのトラブルシューティング
description: Bicep ファイルのデプロイのモニターとトラブルシューティングを行う方法を説明します。 アクティビティ ログとデプロイ履歴を紹介します。
ms.date: 11/04/2021
ms.topic: quickstart
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 31d212ef608b5fbabb4430b5320ae033ae2be325
ms.sourcegitcommit: 8946cfadd89ce8830ebfe358145fd37c0dc4d10e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/05/2021
ms.locfileid: "131849446"
---
# <a name="quickstart-troubleshoot-bicep-file-deployments"></a>クイックスタート: Bicep ファイルのデプロイのトラブルシューティング

このクイックスタートでは、Bicep ファイルのデプロイ エラーのトラブルシューティングを行う方法を説明します。 エラーのあるファイルを作成して、それらのエラーを修正する方法を学びます。

デプロイに関するエラーには 3 種類あります。

- **検証エラー** デプロイ開始前に、ファイル中の構文エラーによって発生します。 これらのエラーはエディターで特定できます。
- **プレフライト検証エラー** は、デプロイ コマンドが実行されてもリソースがデプロイされない場合に発生します。 これらのエラーは、デプロイを開始しなくても検出されます。 たとえば、パラメーターの値が正しくない場合は、プレフライト検証でエラーが検出されます。
- **デプロイ エラー** デプロイのプロセスで発生し、デプロイの進行状況を確認することでのみ検出できます。

どの種類のエラーでも、デプロイのトラブルシューティングに使用できるエラー コードが出力されます。 検証とプレフライトのエラーはアクティビティ ログに表示されますが、デプロイ履歴には表示されません。 構文エラーがある Bicep ファイルは JSON にコンパイルされず、アクティビティ ログに表示されません。

## <a name="prerequisites"></a>前提条件

このクイックスタートを実行するには、次のものが必要です。

- Azure サブスクリプションをお持ちでない場合は、開始する前に[無料アカウントを作成](https://azure.microsoft.com/free/)してください。
- 最新の [Bicep 拡張機能](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-bicep)をインストールした [Visual Studio Code](https://code.visualstudio.com)。
- 最新バージョンの [Azure PowerShell](/powershell/azure/install-az-ps) または [Azure CLI](/cli/azure/install-azure-cli)。

## <a name="create-a-bicep-file-with-errors"></a>エラーのある Bicep ファイルを作成する

次の Bicep ファイルをコピーしてローカルに保存します。 このファイルを使用して、検証エラー、プレフライト エラー、デプロイ エラーのトラブルシューティングを行います。 このクイックスタートでは、ファイルに **troubleshoot.bicep** という名前を付けていますが、実際には好きな名前を付けることができます。

```bicep
@description('SKU for the storage account')
@allowed([
  'Standard_LRS'
  'Standard_GRS'
  'Standard_ZRS'
  'Premium_LRS'
])
parameter storageAccountType string = 'Standard_LRS'

@description('Prefix for storage name.')
param prefixName string

var storageAccountName = '${prefixName}${uniqueString(resourceGroup().id)}'

resource storageAccount 'Microsoft.Storage/storageAccounts@2021-06-01' = {
  name: storageAccountName
  location: resourceGroup().location
  sku: {
    name: storageAccountType
  }
  kind: 'StorageV2'
  properties: {}
}

resource existingVNet 'Microsoft.Network/virtualNetworks@2021-03-01' existing = {
  name: 'doesnotexist'
}

output storageAccountName string = storageAccountName
output vnetResult object = existingVNet
```

## <a name="fix-validation-error"></a>検証エラーを修正する

Visual Studio Code でファイルを開きます。 Visual Studio Code により構文エラーが検出されます。 最初のパラメーター宣言が赤の波線でマークされ、エラーがあることを示しています。

:::image type="content" source="media/quickstart-troubleshoot-bicep-deployment/show-visual-studio-code-error.png" alt-text="構文のエラーを示す Visual Studio Code のスクリーンショット。":::

エラーがあるためにマークされているのは次の行です。

```bicep
@allowed([
  'Standard_LRS'
  'Standard_GRS'
  'Standard_ZRS'
  'Premium_LRS'
])
parameter storageAccountType string = 'Standard_LRS'
```

`parameter` にマウス ポインターを重ねると、エラー メッセージが表示されます。

:::image type="content" source="media/quickstart-troubleshoot-bicep-deployment/declaration-not-recognized.png" alt-text="Visual Studio Code のエラー メッセージのスクリーンショット。":::

メッセージには「この宣言の型を認識できません。 パラメーター、変数、リソースまたは出力の宣言を指定してください。」と表示されています。 このファイルをデプロイしようとすると、デプロイするためのコマンドを実行したときに、同じエラー メッセージが表示されます。

[パラメーター宣言](../bicep/parameters.md)のドキュメントを読むと、実際のキーワードは `param` であることが分かります。 その構文を修正すると、検証エラーはなくなります。 `@allowed` デコレーターもエラーによりマークされますが、このエラーもパラメーター宣言を修正すれば解決できます。 このデコレーターがマークされたのは、デコレーターの後にパラメーター宣言がないことにより、エラーが発生しているからです。 宣言が正しくないので、この条件を満たせないのです。

修正後の行は次のようになります。

```bicep
param storageAccountType string = 'Standard_LRS'
```

## <a name="fix-preflight-error"></a>プレフライト エラーを修正する

検証エラーを修正したので、次はファイルをデプロイします。 ところが、入力するパラメーターが正しくないので、プレフライト エラーが発生します。

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli
az group create --name troubleshootRG --location westus
az deployment group create \
  --resource-group troubleshootRG \
  --template-file troubleshoot.bicep \
  --parameters prefixName=longNamewith!!Charactersthatarenotallowed
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
New-AzResourceGroup -Name troubleshootRG -Location westus
New-AzResourceGroupDeployment `
  -ResourceGroupName troubleshootRG `
  -TemplateFile troubleshoot.bicep `
  -prefixName longNamewith!!Charactersthatarenotallowed
```

---

Azure Resource Manager により、ストレージ アカウントの名前に許可されていない文字が含まれていると判定されます。 デプロイは実行されません。

プレフライト検証に通らなかったことを示すエラー メッセージが表示されます。 ストレージ アカウント名は長さが 3 - 24 文字で、数字と小文字だけを使用する必要がある、というメッセージも表示されます。 入力したプレフィックスが、この要件を満たしていませんでした。 このエラー コードの詳しい情報は、[ストレージ アカウント名のエラーの解決](error-storage-account-name.md)に関する記事をご覧ください。

エラーを検出したのがプレフライトだったため、履歴にデプロイの記録がありません。

:::image type="content" source="media/quickstart-troubleshoot-bicep-deployment/no-deployment.png" alt-text="ポータルのスクリーンショット。履歴にデプロイの記録がない。":::

ですが、Activity Log には失敗したデプロイが記録されます。

:::image type="content" source="media/quickstart-troubleshoot-bicep-deployment/preflight-activity-log.png" alt-text="エラーを示すアクティビティ ログのスクリーンショット。":::

ログのエントリの詳細を開けばエラー メッセージを表示できます。

## <a name="fix-deployment-error"></a>デプロイ エラーを修正する

名前のプレフィックス パラメーターに許可された値を入力して、もう一度ファイルをデプロイします。

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli
az group create --name troubleshootRG --location westus
az deployment group create \
  --resource-group troubleshootRG \
  --template-file troubleshoot.bicep \
  --parameters prefixName=stg
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
New-AzResourceGroup -Name troubleshootRG -Location westus
New-AzResourceGroupDeployment `
  -ResourceGroupName troubleshootRG `
  -TemplateFile troubleshoot.bicep `
  -prefixName stg
```

---

デプロイが始まりますが、仮想ネットワークが見つからなかったことを示すメッセージが表示されて失敗します。 通常、リソースへの参照を修正すれば、このエラーを修正できます。 このクイックスタートでは、参照を削除します。 このエラー コードの詳しい情報は、[リソースが見つからないエラーの解決](error-not-found.md)に関する記事をご覧ください。

ポータルでは、履歴にデプロイが表示されます。

:::image type="content" source="media/quickstart-troubleshoot-bicep-deployment/view-deployment-history.png" alt-text="ポータルに表示されたデプロイの履歴のスクリーンショット｡":::

デプロイの履歴のエントリを開けば、エラーの詳細を取得できます。 このエラーは、アクティビティ ログにも記録されます。

この Bicep ファイルでは、リソース グループに存在しない仮想ネットワークを参照しようとします。 既存の仮想ネットワークへの参照を削除してエラーを解決します。

```bicep
@description('SKU for the storage account')
@allowed([
  'Standard_LRS'
  'Standard_GRS'
  'Standard_ZRS'
  'Premium_LRS'
])
parameter storageAccountType string = 'Standard_LRS'

@description('Prefix for storage name.')
param prefixName string

var storageAccountName = '${prefixName}${uniqueString(resourceGroup().id)}'

resource storageAccount 'Microsoft.Storage/storageAccounts@2021-06-01' = {
  name: storageAccountName
  location: resourceGroup().location
  sku: {
    name: storageAccountType
  }
  kind: 'StorageV2'
  properties: {}
}

output storageAccountName string = storageAccountName
```

エラーを出さずに Bicep ファイルをデプロイできるようになります。

## <a name="clean-up-resources"></a>リソースをクリーンアップする

Azure リソースが必要なくなったときは、リソース グループを削除します。 リソース グループは Cloud Shell かポータルで削除できます。

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli
az group delete --name troubleshootRG
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

山かっこを含めて、`<rgname>` をリソース グループ名に置き換えてください。

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

このクイックスタートでは、Bicep ファイルのデプロイ エラーのトラブルシューティングを行う方法を説明します。

> [!div class="nextstepaction"]
> [よくある Azure デプロイ エラーのトラブルシューティング](common-deployment-errors.md)に関する記事
