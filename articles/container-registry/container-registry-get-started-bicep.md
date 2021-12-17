---
title: クイック スタート - レジストリの作成 - Bicep
description: Bicep ファイルを使用して Azure コンテナー レジストリを作成する方法について説明します。
services: azure-resource-manager
author: mumian
ms.author: jgao
ms.date: 09/27/2021
ms.topic: quickstart
ms.service: azure-resource-manager
ms.openlocfilehash: a945540dc44b0ed5fd19b721424cfacee4fb924d
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/03/2021
ms.locfileid: "131430602"
---
# <a name="quickstart-create-a-container-registry-by-using-a-bicep-file"></a>クイック スタート: Bicep ファイルを使用したコンテナー レジストリの作成

このクイックスタートでは、Bicep ファイルを使用して Azure Container Registry インスタンスを作成する方法について説明します。

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-bicep-introduction.md)]

## <a name="prerequisites"></a>前提条件

Azure サブスクリプションをお持ちでない場合は、開始する前に[無料](https://azure.microsoft.com/free/)アカウントを作成してください。

## <a name="review-the-bicep-file"></a>Bicep ファイルを確認する

Visual Studio Code または使い慣れたエディターを使用して次の内容のファイルを作成し、「**main.bicep**」という名前を付けます。

```bicep
@minLength(5)
@maxLength(50)
@description('Provide a globally unique name of your Azure Container Registry')
param acrName string = 'acr${uniqueString(resourceGroup().id)}'

@description('Provide a location for the registry.')
param location string = resourceGroup().location

@description('Provide a tier of your Azure Container Registry.')
param acrSku string = 'Basic'

resource acrResource 'Microsoft.ContainerRegistry/registries@2021-06-01-preview' = {
  name: acrName
  location: location
  sku: {
    name: acrSku
  }
  properties: {
    adminUserEnabled: false
  }
}

@description('Output the login server property for later use')
output loginServer string = acrResource.properties.loginServer

```

Bicep ファイルには、次のリソースが定義されています。

* **[Microsoft.ContainerRegistry/registries](/azure/templates/microsoft.containerregistry/registries)** : Azure コンテナー レジストリを作成します

その他の Azure Container Registry テンプレートのサンプルについては、[クイックスタート テンプレート ギャラリー](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Containerregistry&pageNumber=1&sort=Popular)を参照してください。

## <a name="deploy-the-bicep-file"></a>Bicep ファイルをデプロイする

作成したファイルをデプロイするには、PowerShell または Azure CLI を開きます。 統合 Visual Studio Code ターミナルを使用する場合は、`ctrl` + ```` ` ```` キーの組み合わせを選択します。 現在のディレクトリを Bicep ファイルがある場所に変更します。

# <a name="cli"></a>[CLI](#tab/CLI)

```azurecli
az group create --name myContainerRegRG --location centralus

az deployment group create --resource-group myContainerRegRG --template-file main.bicep --parameters acrName={your-unique-name}
```

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

```azurepowershell
New-AzResourceGroup -Name myContainerRegRG -Location centralus

New-AzResourceGroupDeployment -ResourceGroupName myContainerRegRG -TemplateFile ./main.bicep -acrName "{your-unique-name}"
```

---

> [!NOTE]
> **{your-unique-name}** (中かっこを含む) は、一意のコンテナー レジストリ名に置き換えてください。

デプロイが完了すると、デプロイが成功したことを示すメッセージが表示されます。

## <a name="review-deployed-resources"></a>デプロイされているリソースを確認する

Azure portal またはツール (Azure CLI など) を使用して、コンテナー レジストリのプロパティを確認します。

1. ポータルで「**Container Registries**」を検索し、作成したコンテナー レジストリを選択します。

1. **[概要]** ページで、レジストリの **ログイン サーバー** を書き留めます。 Docker を使用してイメージにタグを付けてレジストリにプッシュするときに、この URI を使用します。 情報については、[Docker CLI を使用した最初のイメージのプッシュ](container-registry-get-started-docker-cli.md)に関するページを参照してください。

    :::image type="content" source="media/container-registry-get-started-bicep/registry-overview.png" alt-text="レジストリの概要":::

## <a name="clean-up-resources"></a>リソースをクリーンアップする

リソースが不要になったら、リソース グループとレジストリを削除してください。 これを行うには、Azure portal に移動し、レジストリを含むリソース グループを選択して、 **[リソース グループの削除]** を選択します。

リソース グループの削除

## <a name="next-steps"></a>次のステップ

このクイック スタートでは、Bicep ファイルを使用して Azure Container Registry を作成しました。 Azure Container Registry のチュートリアルに進んで、ACR についての理解を深めましょう。

> [!div class="nextstepaction"]
> [Azure Container Registry のチュートリアル](container-registry-tutorial-prepare-registry.md)

Bicep ファイルの作成手順について説明したチュートリアルについては、以下を参照してください。

> [!div class="nextstepaction"]
> [クイックスタート: Visual Studio Code を使用して Bicep ファイルを作成する](../azure-resource-manager/bicep/quickstart-create-bicep-use-visual-studio-code.md)
