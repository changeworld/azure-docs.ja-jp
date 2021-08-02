---
title: Cloud Shell を使用した Bicep ファイルのデプロイ
description: Azure Resource Manager と Azure Cloud Shell を使用してリソースを Azure にデプロイします。 リソースは Bicep ファイルに定義されています。
author: mumian
ms.author: jgao
ms.topic: conceptual
ms.date: 06/01/2021
ms.openlocfilehash: fa74250a290cc3e254e5c9f3757bb3e712c020da
ms.sourcegitcommit: 7f59e3b79a12395d37d569c250285a15df7a1077
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/02/2021
ms.locfileid: "111026848"
---
# <a name="deploy-bicep-files-from-azure-cloud-shell"></a>Azure Cloud Shell から Bicep ファイルをデプロイする

[Azure Cloud Shell](../../cloud-shell/overview.md) を使用して、Bicep ファイルをデプロイできます。 現時点では、Cloud Shell からのみローカル Bicep ファイルをデプロイできます。

任意のスコープにデプロイできます。 この記事では、リソース グループへのデプロイについて説明します。

## <a name="deploy-local-bicep-file"></a>ローカルの Bicep ファイルをデプロイする

ローカルの Bicep ファイルをデプロイするには、最初に Bicep ファイルを Cloud Shell セッションにアップロードする必要があります。

1. [Cloud Shell](https://shell.azure.com) にサインインします。
1. **[PowerShell]** または **[Bash]** を選択します。

    :::image type="content" source="./media/deploy-cloud-shell/bicep-cloud-shell-bash-powershell.png" alt-text="[Bash] または [PowerShell]"::: を選択します。

1. **[ファイルのアップロード/ダウンロード]** を選択し、 **[アップロード]** を選択します。

    :::image type="content" source="./media/deploy-cloud-shell/bicep-cloud-shell-upload.png" alt-text="ファイルをアップロードする":::

1. アップロードする Bicep ファイルを選択し、 **[開く]** を選択します。
1. Bicep ファイルをデプロイするには、次のコマンドを使用します。

   # <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

   ```azurecli-interactive
   az group create --name ExampleGroup --location "South Central US"
   az deployment group create \
     --resource-group ExampleGroup \
     --template-file azuredeploy.bicep \
     --parameters storageAccountType=Standard_GRS
   ```

   # <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

   ```azurepowershell-interactive
   New-AzResourceGroup -Name ExampleGroup -Location "Central US"
   New-AzResourceGroupDeployment `
     -DeploymentName ExampleDeployment `
     -ResourceGroupName ExampleGroup `
     -TemplateFile azuredeploy.bicep `
     -storageAccountType Standard_GRS
   ```

   ---

## <a name="next-steps"></a>次のステップ

- デプロイ コマンドの詳細については、「[Bicep と Azure CLI でリソースをデプロイする](deploy-cli.md)」および「[Bicep と Azure PowerShell を使用したリソースのデプロイ](deploy-powershell.md)」を参照してください。
- Bicep ファイルをデプロイする前に変更をプレビューするには、[Bicep デプロイの what-if 操作](./deploy-what-if.md)に関する説明を参照してください。
