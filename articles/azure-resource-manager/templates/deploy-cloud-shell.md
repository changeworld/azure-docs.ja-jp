---
title: Cloud Shell を使用したテンプレートのデプロイ
description: Azure Resource Manager と Azure Cloud Shell を使用してリソースを Azure にデプロイします。 リソースは Azure Resource Manager テンプレート (ARM テンプレート) で定義されます。
ms.topic: conceptual
ms.date: 09/03/2021
ms.openlocfilehash: 0228f4862cbdcda98bc577844bca9dad34feb96f
ms.sourcegitcommit: f2d0e1e91a6c345858d3c21b387b15e3b1fa8b4c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/07/2021
ms.locfileid: "123535113"
---
# <a name="deploy-arm-templates-from-azure-cloud-shell"></a>Azure Cloud Shell から ARM テンプレートをデプロイする

[Azure Cloud Shell](../../cloud-shell/overview.md) を使用して、Azure Resource Manager テンプレート (ARM テンプレート) をデプロイできます。 リモートに格納されている ARM テンプレートをデプロイするか、Cloud Shell のローカル ストレージ アカウントに格納されている ARM テンプレートをデプロイできます。

任意のスコープにデプロイできます。 この記事では、リソース グループへのデプロイについて説明します。

## <a name="deploy-remote-template"></a>リモート テンプレートのデプロイ

外部テンプレートをデプロイするには、外部デプロイの場合とまったく同じように、テンプレートの URI を指定します。 外部テンプレートは、GitHub リポジトリまたは外部ストレージ アカウントに存在する可能性があります。

1. Cloud Shell のプロンプトを開きます。

   :::image type="content" source="./media/deploy-cloud-shell/open-cloud-shell.png" alt-text="Cloud Shell を開く":::

1. テンプレートをデプロイするには、次のコマンドを使用します。

   # <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

   ```azurecli-interactive
   az group create --name ExampleGroup --location "Central US"
   az deployment group create \
     --name ExampleDeployment \
     --resource-group ExampleGroup \
     --template-uri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/quickstarts/microsoft.storage/storage-account-create/azuredeploy.json" \
     --parameters storageAccountType=Standard_GRS
   ```

   # <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

   ```azurepowershell-interactive
   New-AzResourceGroup -Name ExampleGroup -Location "Central US"
   New-AzResourceGroupDeployment `
     -DeploymentName ExampleDeployment `
     -ResourceGroupName ExampleGroup `
     -TemplateUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/quickstarts/microsoft.storage/storage-account-create/azuredeploy.json `
     -storageAccountType Standard_GRS
   ```

   ---

## <a name="deploy-local-template"></a>ローカル テンプレートのデプロイ

ローカル テンプレートをデプロイするには、最初に Cloud Shell セッションに接続されているストレージ アカウントにテンプレートをアップロードする必要があります。

1. [Cloud Shell](https://shell.azure.com) にサインインします。

1. **[PowerShell]** または **[Bash]** を選択します。

   :::image type="content" source="./media/deploy-cloud-shell/cloud-shell-bash-powershell.png" alt-text="[Bash] または [PowerShell]"::: を選択します。

1. **[ファイルのアップロード/ダウンロード]** を選択し、 **[アップロード]** を選択します。

   :::image type="content" source="./media/deploy-cloud-shell/cloud-shell-upload.png" alt-text="ファイルをアップロードする":::

1. アップロードする ARM テンプレートを選択し、 **[開く]** を選択します。

1. テンプレートをデプロイするには、次のコマンドを使用します。

   # <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

   ```azurecli-interactive
   az group create --name ExampleGroup --location "South Central US"
   az deployment group create \
     --resource-group ExampleGroup \
     --template-file azuredeploy.json \
     --parameters storageAccountType=Standard_GRS
   ```

   # <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

   ```azurepowershell-interactive
   New-AzResourceGroup -Name ExampleGroup -Location "Central US"
   New-AzResourceGroupDeployment `
     -DeploymentName ExampleDeployment `
     -ResourceGroupName ExampleGroup `
     -TemplateFile azuredeploy.json `
     -storageAccountType Standard_GRS
   ```

   ---

## <a name="next-steps"></a>次のステップ

- デプロイ コマンドの詳細については、「[ARM テンプレートと Azure CLI でリソースをデプロイする](deploy-cli.md)」および「[ARM テンプレートと Azure PowerShell を使用したリソースのデプロイ](deploy-powershell.md)」を参照してください。
- テンプレートをデプロイする前に変更をプレビューするには、「[ARM テンプレートのデプロイの what-if 操作](./deploy-what-if.md)」を参照してください。