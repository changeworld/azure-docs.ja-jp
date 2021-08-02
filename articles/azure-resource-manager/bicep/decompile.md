---
title: ARM テンプレート JSON を Bicep に逆コンパイルする
description: Azure Resource Manager テンプレートを Bicep ファイルに逆コンパイルするためのコマンドについて説明します。
ms.topic: conceptual
ms.date: 06/01/2021
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: b9244eb0a076785c424603dc79ba13af6c29fb03
ms.sourcegitcommit: bd65925eb409d0c516c48494c5b97960949aee05
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/06/2021
ms.locfileid: "111537021"
---
# <a name="decompiling-arm-template-json-to-bicep"></a>Bicep への ARM テンプレート JSON の逆コンパイル

この記事では、Azure Resource Manager テンプレート (ARM テンプレート) を Bicep ファイルに逆コンパイルする方法について説明します。 変換コマンドを実行するには、[Bicep CLI がインストール](./install.md)されている必要があります。

ARM テンプレートの逆コンパイルは、Bicep の開発を開始するうえで役立ちます。 ARM テンプレートのライブラリがあり、今後の開発に Bicep を使用する場合は、Bicep に逆コンパイルすることができます。 しかし、Bicep ファイルには、Bicep のベスト プラクティスを実装するためのリビジョンが必要になる場合があります。

この記事では、Azure CLI で `decompile` コマンドを実行する方法を示します。 Azure CLI を使用しない場合は、コマンドの先頭に `az` を指定せずにコマンドを実行します。 たとえば、`az bicep decompile` を ``bicep decompile`` にします。

## <a name="decompile-from-json-to-bicep"></a>JSON から Bicep に逆コンパイルする

ARM テンプレート JSON を Bicep に逆コンパイルするには、以下を使用します。

```azurecli
az bicep decompile --file main.json
```

このコマンドでは、ARM テンプレートと同じディレクトリに _main.bicep_ という名前のファイルを作成します。

> [!CAUTION]
> 逆コンパイルではファイルの変換が試行されますが、ARM テンプレート JSON から Bicep へのマッピングは保証されていません。 生成された Bicep ファイルの警告とエラーの修正が必要になる場合があります。 または、正確な変換ができない場合、逆コンパイルに失敗するおそれがあります。 問題または不正確な変換を報告するには、[イシューを作成します](https://github.com/Azure/bicep/issues)。

decompile と [build](bicep-cli.md#build) コマンドにより、機能的に同等のテンプレートが生成されます。 ただし、実装がまったく同じであるとは限りません。 テンプレートを JSON から Bicep に変換した後、JSON に再変換すると、元のテンプレートとは構文が異なったテンプレートになる可能性があります。 デプロイすると、変換されたテンプレートは同じ結果を生成します。

## <a name="fix-conversion-issues"></a>変換に関する問題を修正する

次の ARM テンプレートがあるとします。

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
      "apiVersion": "2019-06-01",
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

逆コンパイルすると、次のようになります。

```bicep
@allowed([
  'Standard_LRS'
  'Standard_GRS'
  'Standard_ZRS'
  'Premium_LRS'
])
@description('Storage Account type')
param storageAccountType string = 'Standard_LRS'

@description('Location for all resources.')
param location string = resourceGroup().location

var storageAccountName_var = 'store${uniqueString(resourceGroup().id)}'

resource storageAccountName 'Microsoft.Storage/storageAccounts@2019-06-01' = {
  name: storageAccountName_var
  location: location
  sku: {
    name: storageAccountType
  }
  kind: 'StorageV2'
  properties: {}
}

output storageAccountName string = storageAccountName_var
```

逆コンパイルされたファイルは動作しますが、変更が必要になる場合がある名前がいくつかあります。 変数 `var storageAccountName_var` には、通常とは異なる名前付け規則があります。 これを次のように変更してみましょう。

```bicep
var uniqueStorageName = 'store${uniqueString(resourceGroup().id)}'
```

リソースには、変更が必要になる場合があるシンボル名があります。 シンボル名の `storageAccountName` の代わりに、`exampleStorage` を使用します。

```bicep
resource exampleStorage 'Microsoft.Storage/storageAccounts@2019-06-01' = {
```

ストレージ アカウント名の変数の名前を変更したため、その使用場所を変更する必要があります。

```bicep
resource exampleStorage 'Microsoft.Storage/storageAccounts@2019-06-01' = {
  name: uniqueStorageName
```

また、出力では、以下を使用します。

```bicep
output storageAccountName string = uniqueStorageName
```

完全なファイルは次のとおりです。

```bicep
@allowed([
  'Standard_LRS'
  'Standard_GRS'
  'Standard_ZRS'
  'Premium_LRS'
])
@description('Storage Account type')
param storageAccountType string = 'Standard_LRS'

@description('Location for all resources.')
param location string = resourceGroup().location

var uniqueStorageName = 'store${uniqueString(resourceGroup().id)}'

resource exampleStorage 'Microsoft.Storage/storageAccounts@2019-06-01' = {
  name: uniqueStorageName
  location: location
  sku: {
    name: storageAccountType
  }
  kind: 'StorageV2'
  properties: {}
}

output storageAccountName string = uniqueStorageName
```

## <a name="export-template-and-convert"></a>テンプレートをエクスポートして変換する

リソース グループのテンプレートをエクスポートしてから、それを `decompile` コマンドに直接渡すことができます。 次の例は、エクスポートされたテンプレートを逆コンパイルする方法を示しています。

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli
az group export --name "your_resource_group_name" > main.json
az bicep decompile --file main.json
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
Export-AzResourceGroup -ResourceGroupName "your_resource_group_name" -Path ./main.json
bicep decompile main.json
```

# <a name="portal"></a>[ポータル](#tab/azure-portal)

ポータルを使用して[テンプレートをエクスポート](../templates/export-template-portal.md)します。

ダウンロードしたファイルに対して `bicep decompile <filename>` を使用します。

---

## <a name="side-by-side-view"></a>横に並べて表示

[Bicep Playground](https://aka.ms/bicepdemo) を使用すると、同等の ARM テンプレートと Bicep ファイルを横に並べて表示できます。 サンプル テンプレートを選択すると、両方のバージョンを表示できます。 または、`Decompile` を選択して、独自の ARM テンプレートをアップロードし、同等の Bicep ファイルを表示します。

## <a name="next-steps"></a>次のステップ

すべての Bicep CLI コマンドの詳細については、「[BICEP CLI コマンド](bicep-cli.md)」を参照してください。
