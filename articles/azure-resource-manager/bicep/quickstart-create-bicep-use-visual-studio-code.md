---
title: Bicep ファイルを作成する - Visual Studio Code
description: Visual Studio Code と Bicep 拡張機能を使用して、Azure リソースをデプロイするための Bicepファイルを作成します
ms.date: 11/09/2021
ms.topic: quickstart
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: f5950a143510b85ea6de8f7af4e08c7d4b5ed293
ms.sourcegitcommit: 838413a8fc8cd53581973472b7832d87c58e3d5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/10/2021
ms.locfileid: "132136231"
---
# <a name="quickstart-create-bicep-files-with-visual-studio-code"></a>クイックスタート: Visual Studio Code を使用して Bicep ファイルを作成する

このクイックスタートでは、Visual Studio Code で [Bicep ファイル](overview.md)を作成する手順について説明します。 ストレージ アカウントと仮想ネットワークを作成します。 また、Bicep 拡張機能に備わっているタイプ セーフ、構文の検証、オートコンプリートによって、開発がどのように簡略化されるかについても説明します。

## <a name="prerequisites"></a>前提条件

Azure サブスクリプションをお持ちでない場合は、開始する前に[無料アカウントを作成](https://azure.microsoft.com/free/)してください。

Bicep の開発環境を設定するには、「[Bicep ツールをインストールする](install.md)」を参照してください。 これらの手順を完了すると、[Visual Studio Code](https://code.visualstudio.com/) と [Bicep 拡張機能](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-bicep)を利用できるようになります。 また、最新の [Azure CLI](/cli/azure/) または最新の [Azure PowerShell モジュール](/powershell/azure/new-azureps-module-az)を用意します。

## <a name="add-resource-snippet"></a>リソース スニペットを追加する

Visual Studio Code を起動し、*main.bicep* という名前の新しいファイルを作成します。

VS Code と Bicep 拡張機能を使用すると、事前に定義されたスニペットが用意されているので、開発が簡単になります。 このクイックスタートでは、仮想ネットワークを作成するスニペットを追加します。

*main.bicep* に「**vnet**」と入力します。 リストから **[res-vnet]** を選択し、Tab キーまたは Enter キーを押します。

:::image type="content" source="./media/quickstart-create-bicep-use-visual-studio-code/add-snippet.png" alt-text="仮想ネットワークのスニペットを追加する":::

> [!TIP]
> これらの IntelliSense オプションが VS Code に表示されない場合は、「[前提条件](#prerequisites)」で指定されている Bicep 拡張機能をインストールしていることを確認してください。 この拡張機能をインストールしている場合は、Bicep ファイルを開いた後、Bicep 言語サービスが起動されるまで少し時間を置いてください。 通常はすぐに起動されますが、起動されるまでは IntelliSense オプションが表示されません。 右下隅の通知は、このサービスが起動中であることを示します。 その通知が消えたとき、このサービスが実行されています。

これで、Bicep ファイルには次のコードが追加されました。

```bicep
resource virtualNetwork 'Microsoft.Network/virtualNetworks@2019-11-01' = {
  name: 'name'
  location: resourceGroup().location
  properties: {
    addressSpace: {
      addressPrefixes: [
        '10.0.0.0/16'
      ]
    }
    subnets: [
      {
        name: 'Subnet-1'
        properties: {
          addressPrefix: '10.0.0.0/24'
        }
      }
      {
        name: 'Subnet-2'
        properties: {
          addressPrefix: '10.0.1.0/24'
        }
      }
    ]
  }
}
```

このスニペットには、仮想ネットワークの定義に必要な値がすべて含まれています。 ただし、必要に応じてこのコードを変更することができます。 たとえば、`name` は仮想ネットワークに適した名前ではありません。 `name` プロパティを `examplevnet` に変更します。

```bicep
name: 'examplevnet'
```

この Bicep ファイルをデプロイすることもできますが、ここではデプロイする前にパラメーターとストレージ アカウントを追加します。

## <a name="add-parameter"></a>パラメーターを追加する

次に、ストレージ アカウント名に使用するパラメーターを追加します。 ファイルの先頭に、次のように追加します。

```bicep
param storageName
```

**storageName** の後にスペースを追加すると、そのパラメーターに使用できるデータ型が IntelliSense によって提案されることがわかります。 **[string]** を選択します。

:::image type="content" source="./media/quickstart-create-bicep-use-visual-studio-code/add-param.png" alt-text="パラメーターに文字列型を追加する":::

次のようなパラメーターになります。

```bicep
param storageName string
```

このパラメーターは問題なく機能しますが、ストレージ アカウントの名前には長さの制限があります。 名前は 3 文字以上、24 文字以下にする必要があります。 これらの要件を指定するには、パラメーターにデコレーターを追加します。

パラメーターの上に行を追加して、「 **@** 」と入力します。 使用できるデコレータが表示されます。 **minLength** と **maxLength** の両方のデコレータがあることに注目してください。

:::image type="content" source="./media/quickstart-create-bicep-use-visual-studio-code/add-decorators.png" alt-text="パラメーターにデコレータを追加する":::

両方のデコレーターを追加して、次のように文字数制限を指定します。

```bicep
@minLength(3)
@maxLength(24)
param storageName string
```

また、パラメーターの説明を追加することもできます。 Bicep ファイルをデプロイする担当者が、指定する値を理解するために役立つ情報を含めてください。

```bicep
@minLength(3)
@maxLength(24)
@description('Provide a name for the storage account. Use only lower case letters and numbers. The name must be unique across Azure.')
param storageName string
```

これで、パラメーターを使用する準備は完了です。

## <a name="add-resource"></a>リソースの追加

ここでは、スニペットを使用してストレージ アカウントを定義するのではなく、IntelliSense を使用して値を設定します。 IntelliSense を使用すると、手動で値を入力するよりもはるかに簡単にこの手順を行うことができます。

リソースを定義するには、`resource` キーワードを使用します。  仮想ネットワークの下に「**resource exampleStorage**」と入力します。

```bicep
resource exampleStorage
```

**exampleStorage** は、デプロイするリソースのシンボリック名です。 この名前は、Bicep ファイルの他の部分にあるリソースを参照するために使用できます。

シンボリック名の後にスペースを追加すると、リソースの種類の一覧が表示されます。 続けて「**storage**」と入力すると、使用できるオプションから選択できるようになります。

:::image type="content" source="./media/quickstart-create-bicep-use-visual-studio-code/select-resource-type.png" alt-text="リソースの種類に対応するストレージ アカウントを選択する":::

**[Microsoft.Storage/storageAccounts]** を選択すると、使用できる API バージョンが表示されます。 **[2021-02-01]** を選択します。

:::image type="content" source="./media/quickstart-create-bicep-use-visual-studio-code/select-api-version.png" alt-text="リソースの種類に対応する API バージョンを選択する":::

リソースの種類の一重引用符の後に `=` とスペースを追加します。 リソースにプロパティを追加するためのオプションが表示されます。 **[required-properties]** を選択します。

:::image type="content" source="./media/quickstart-create-bicep-use-visual-studio-code/select-required-properties.png" alt-text="必要なプロパティを追加する":::

このオプションを選択すると、デプロイに必要なリソースの種類のすべてのプロパティが追加されます。 このオプションを選択すると、ストレージ アカウントに次のプロパティが追加されます。

```bicep
resource exampleStorage 'Microsoft.Storage/storageAccounts@2021-02-01' = {
  name:
  location:
  sku: {
    name:
  }
  kind:

}
```

完了までもう少しです。 これらのプロパティに値を指定するだけです。

ここでも IntelliSense が役立ちます。 `name` を `storageName` に設定します。これは、ストレージ アカウントの名前を含むパラメーターです。 `location` の場合は、`'eastus'` に設定します。 SKU の名前と種類を追加すると、IntelliSense によって有効なオプションが表示されます。

完了すると、次のようになります。

```bicep
@minLength(3)
@maxLength(24)
param storageName string

resource virtualNetwork 'Microsoft.Network/virtualNetworks@2019-11-01' = {
  name: 'examplevnet'
  location: resourceGroup().location
  properties: {
    addressSpace: {
      addressPrefixes: [
        '10.0.0.0/16'
      ]
    }
    subnets: [
      {
        name: 'Subnet-1'
        properties: {
          addressPrefix: '10.0.0.0/24'
        }
      }
      {
        name: 'Subnet-2'
        properties: {
          addressPrefix: '10.0.1.0/24'
        }
      }
    ]
  }
}

resource exampleStorage 'Microsoft.Storage/storageAccounts@2021-02-01' = {
  name: storageName
  location: 'eastus'
  sku: {
    name: 'Standard_LRS'
  }
  kind: 'StorageV2'
}
```

Bicep の構文の詳細については、[Bicep の構造](./file.md)に関する記事を参照してください。

## <a name="visualize-resources"></a>リソースを視覚化する

ファイル内のリソースの表現を確認することができます。

左上にあるビジュアライザー ボタンを選択すると、Bicep Visualizer が開きます。

:::image type="content" source="./media/quickstart-create-bicep-use-visual-studio-code/bicep-visualizer.png" alt-text="Bicep Visualizer":::

このビジュアライザーには、Bicep ファイルに定義されているリソースと、リソースの依存関係情報が表示されます。 このクイックスタートで定義した 2 つのリソースには依存関係がないので、2 つのリソース間にコネクタは表示されません。

## <a name="deploy-the-bicep-file"></a>Bicep ファイルをデプロイする

作成したファイルをデプロイするには、PowerShell または Azure CLI を開きます。 統合 Visual Studio Code ターミナルを使用する場合は、`ctrl` + ```` ` ```` キーの組み合わせを選択します。 現在のディレクトリを Bicep ファイルがある場所に変更します。

# <a name="cli"></a>[CLI](#tab/CLI)

```azurecli
az group create --name exampleRG --location eastus

az deployment group create --resource-group exampleRG --template-file main.bicep --parameters storageName=uniquename
```

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

```azurepowershell
New-AzResourceGroup -Name exampleRG -Location eastus

New-AzResourceGroupDeployment -ResourceGroupName exampleRG -TemplateFile ./main.bicep -storageName "uniquename"
```

---

> [!NOTE]
> **uniquename** は、一意のストレージ アカウント名に置き換えてください。 ストレージ アカウントが既に取得されていることを示すエラー メッセージが表示された場合は、指定したストレージ名が使用されています。 一意である可能性が高い名前を指定してください。

デプロイが完了すると、デプロイが成功したことを示すメッセージが表示されます。

## <a name="clean-up-resources"></a>リソースをクリーンアップする

Azure リソースが不要になったら、Azure CLI か Azure PowerShell のどちらかのモジュールを使用してクイックスタート リソース グループを削除します。

# <a name="cli"></a>[CLI](#tab/CLI)

```azurecli
az group delete --name exampleRG
```

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

```azurepowershell
Remove-AzResourceGroup -Name exampleRG
```

---

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [Microsoft Learn の Bicep](learn-bicep.md)
