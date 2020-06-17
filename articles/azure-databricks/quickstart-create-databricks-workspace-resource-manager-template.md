---
title: クイックスタート - Azure Resource Manager テンプレートで Azure Databricks ワークスペースを作成する
description: このクイックスタートでは、Azure Resource Manager テンプレートを使用して Azure Databricks ワークスペースを作成する方法について説明します。
services: azure-databricks
ms.service: azure-databricks
author: mamccrea
ms.author: mamccrea
ms.reviewer: jasonh
ms.workload: big-data
ms.topic: quickstart
ms.custom: mvc, subject-armqs
ms.date: 05/27/2020
ms.openlocfilehash: c8503aee5cefbe197fd63cd6406006a1cdf8afa9
ms.sourcegitcommit: 813f7126ed140a0dff7658553a80b266249d302f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/06/2020
ms.locfileid: "84463152"
---
# <a name="quickstart-create-an-azure-databricks-workspace-by-using-the-azure-resource-manager-template"></a>クイック スタート:Azure Resource Manager テンプレートを使用して Azure Databricks ワークスペースを作成する

このクイックスタートでは、Azure Resource Manager テンプレートを使用して、Azure Databricks ワークスペースを作成します。 ワークスペースの作成後、デプロイを検証します。

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

## <a name="prerequisites"></a>前提条件

この記事を完了するには、以下が必要です。

* Azure サブスクリプションがあること - [無料アカウントを作成します](https://azure.microsoft.com/free/)

## <a name="create-an-azure-databricks-workspace"></a>Azure Databricks ワークスペースを作成する

### <a name="review-the-template"></a>テンプレートを確認する

このクイックスタートで使用されるテンプレートは [Azure クイックスタート テンプレート](https://azure.microsoft.com/resources/templates/101-databricks-workspace/)からのものです。

:::code language="json" source="~/quickstart-templates/101-databricks-workspace/azuredeploy.json" range="1-53" highlight="33-46":::

テンプレートで定義されている Azure リソースは [Microsoft.Databricks/workspaces](/azure/templates/microsoft.databricks/workspaces) です。つまり Azure Databricks ワークスペースを作成します。

### <a name="deploy-the-template"></a>テンプレートのデプロイ

このセクションでは、Azure Resource Manager テンプレートを使って Azure Databricks ワークスペースを作成します。

1. Azure にサインインし、テンプレートを開くには次のイメージを選択します。 このテンプレートでは、Azure Databricks ワークスペースを作成します。

   [![Azure へのデプロイ](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-databricks-workspace%2Fazuredeploy.json)

2. Azure Databricks ワークスペースを作成するために必要な値を入力します。

   ![Azure Resource Manager テンプレートを使用して Azure Databricks ワークスペースを作成する](./media/quickstart-create-databricks-workspace-resource-manager-template/create-databricks-workspace-using-resource-manager-template.png "Azure Resource Manager テンプレートを使用して Azure Databricks ワークスペースを作成する")

   次の値を指定します。

   |プロパティ  |説明  |
   |---------|---------|
   |**サブスクリプション**     | ドロップダウンから Azure サブスクリプションを選択します。        |
   |**リソース グループ**     | 新しいリソース グループを作成するか、既存のリソース グループを使用するかを指定します。 リソース グループは、Azure ソリューションの関連するリソースを保持するコンテナーです。 詳しくは、[Azure リソース グループの概要](../azure-resource-manager/management/overview.md)に関するページをご覧ください。 |
   |**場所**     | **[米国東部 2]** を選択します。 使用可能な他のリージョンについては、「[リージョン別の利用可能な製品](https://azure.microsoft.com/regions/services/)」をご覧ください。        |
   |**ワークスペース名**     | Databricks ワークスペースの名前を指定します        |
   |**価格レベル**     |  **Standard** と **Premium** のいずれかを選択します。 これらのレベルの詳細については、[Databricks の価格に関するページ](https://azure.microsoft.com/pricing/details/databricks/)を参照してください。       |

3. **[確認および作成]** 、 **[作成]** の順に選択します。

4. ワークスペースの作成には数分かかります。 ワークスペースのデプロイが失敗した場合でも、ワークスペースはエラー状態で作成されます。 失敗したワークスペースを削除し、デプロイ エラーのない新しいワークスペースを作成します。 失敗したワークスペースを削除すると、管理対象リソース グループと、正常にデプロイされたリソースもすべて削除されます。

## <a name="review-deployed-resources"></a>デプロイされているリソースを確認する

Azure portal を使用して Azure Databricks ワークスペースを確認するか、次の Azure CLI または Azure PowerShell スクリプトを使用してリソースを一覧表示できます。

### <a name="azure-cli"></a>Azure CLI

```azurecli-interactive
echo "Enter your Azure Databricks workspace name:" &&
read databricksWorkspaceName &&
echo "Enter the resource group where the Azure Databricks workspace exists:" &&
read resourcegroupName &&
az databricks workspace show -g $resourcegroupName -n $databricksWorkspaceName
```

### <a name="azure-powershell"></a>Azure PowerShell

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the resource group name where your Azure Databricks workspace exists"
(Get-AzResource -ResourceType "Microsoft.Databricks/workspaces" -ResourceGroupName $resourceGroupName).Name
 Write-Host "Press [ENTER] to continue..."
```

## <a name="clean-up-resources"></a>リソースをクリーンアップする

引き続き後続のチュートリアルに進む場合は、これらのリソースをそのまま残しておくことをお勧めします。 不要になったら、リソース グループを削除してください。リソース グループを削除すれば、Azure Databricks ワークスペースおよび関連する管理対象リソースが削除されます。 Azure CLI または Azure PowerShell を使用してリソース グループを削除するには、次を実行します。

### <a name="azure-cli"></a>Azure CLI

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
az group delete --name $resourceGroupName &&
echo "Press [ENTER] to continue ..."
```

### <a name="azure-powershell"></a>Azure PowerShell

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
Remove-AzResourceGroup -Name $resourceGroupName
Write-Host "Press [ENTER] to continue..."
```

## <a name="next-steps"></a>次のステップ

このクイックスタートでは、Azure Resource Manager テンプレートを使用して Azure Databricks ワークスペースを作成し、デプロイを検証しました。 次の記事に進んで、Azure Databricks を使った ETL (データの抽出、変換、読み込み) 操作の実行方法について学びましょう。

> [!div class="nextstepaction"]
> [Azure Databricks を使ったデータの抽出、変換、読み込み](databricks-extract-load-sql-data-warehouse.md)
