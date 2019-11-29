---
title: Azure Cosmos DB Table API の Azure Resource Manager テンプレート
description: Azure Resource Manager テンプレートを使用して、Azure Cosmos DB Table API を作成および構成します。
author: TheovanKraay
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 11/12/2019
ms.author: thvankra
ms.openlocfilehash: bd30f240700ea4dcf9c9ec20b8007a4445ae35e5
ms.sourcegitcommit: 39da2d9675c3a2ac54ddc164da4568cf341ddecf
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/12/2019
ms.locfileid: "73960554"
---
# <a name="manage-azure-cosmos-db-table-api-resources-using-azure-resource-manager-templates"></a>Azure Resource Manager テンプレートを使用して Azure Cosmos DB Table API リソースを管理する

この記事では、Azure Resource Manager テンプレートを使用して Azure Cosmos DB のアカウント、データベース、コンテナーの管理を自動化するさまざまな操作の実行方法について説明します。 この記事に含まれているのは、Table API アカウントの例のみです。他の種類の API アカウントでの例については、[Cassandra](manage-cassandra-with-resource-manager.md)、[Gremlin](manage-gremlin-with-resource-manager.md)、[MongoDB](manage-mongodb-with-resource-manager.md)、[SQL](manage-sql-with-resource-manager.md) 用の Azure Cosmos DB の API での Azure Resource Manager テンプレートの使用に関する記事を参照してください。

## Azure Cosmos アカウントとテーブルを作成する <a id="create-resource"></a>

Azure Resource Manager テンプレートを使用して Azure Cosmos DB リソースを作成します。 このテンプレートは、400 RU/秒のスループットで 1 つのテーブルを含む Table API の Azure Cosmos アカウントを作成します。 テンプレートをコピーして次に示すようにデプロイするか、[Azure クイック スタート ギャラリー](https://azure.microsoft.com/resources/templates/101-cosmosdb-table/)にアクセスして Azure portal からデプロイします。 テンプレートをローカル コンピューターにダウンロードするか、新しいテンプレートを作成して、`--template-file` パラメーターでローカル パスを指定することもできます。

> [!NOTE]
> アカウント名は小文字で、かつ 44 文字以下にする必要があります。
> RU/秒を更新するには、スループットのプロパティ値が更新されたテンプレートを再送信します。

[!code-json[create-cosmos-table](~/quickstart-templates/101-cosmosdb-table/azuredeploy.json)]

### <a name="deploy-via-powershell"></a>PowerShell 経由でのデプロイ

PowerShell を使用して Resource Manager テンプレートをデプロイするには、スクリプトの **[コピー]** を実行し、 **[試してみる]** を選択して、Azure Cloud Shell を開きます。 スクリプトを貼り付けるには、シェルを右クリックし、 **[貼り付け]** を選択します。

```azurepowershell-interactive

$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
$accountName = Read-Host -Prompt "Enter the account name"
$location = Read-Host -Prompt "Enter the location (i.e. westus2)"
$primaryRegion = Read-Host -Prompt "Enter the primary region (i.e. westus2)"
$secondaryRegion = Read-Host -Prompt "Enter the secondary region (i.e. eastus2)"
$tableName = Read-Host -Prompt "Enter the table name"

New-AzResourceGroup -Name $resourceGroupName -Location $location
New-AzResourceGroupDeployment `
    -ResourceGroupName $resourceGroupName `
    -TemplateUri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-cosmosdb-table/azuredeploy.json" `
    -primaryRegion $primaryRegion `
    -secondaryRegion $secondaryRegion `
    -tableName $tableName

 (Get-AzResource --ResourceType "Microsoft.DocumentDb/databaseAccounts" --ApiVersion "2015-04-08" --ResourceGroupName $resourceGroupName).name
```

Azure Cloud Shell からではなく、PowerShell のローカルでインストールされたバージョンを使用する場合、Azure PowerShell モジュールを[インストール](/powershell/azure/install-az-ps)する必要があります。 バージョンを確認するには、`Get-Module -ListAvailable Az` を実行します。

### <a name="deploy-via-the-azure-cli"></a>Azure CLI によるデプロイ

Azure CLI を使用して Azure Resource Manager テンプレートをデプロイするには、スクリプトの **[コピー]** を実行し、 **[試してみる]** を選択して、Azure Cloud Shell を開きます。 スクリプトを貼り付けるには、シェルを右クリックし、 **[貼り付け]** を選択します。

```azurecli-interactive
read -p 'Enter the Resource Group name: ' resourceGroupName
read -p 'Enter the location (i.e. westus2): ' location
read -p 'Enter the account name: ' accountName
read -p 'Enter the primary region (i.e. westus2): ' primaryRegion
read -p 'Enter the secondary region (i.e. eastus2): ' secondaryRegion
read -p 'Enter the table name: ' tableName

az group create --name $resourceGroupName --location $location
az group deployment create --resource-group $resourceGroupName \
   --template-uri https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/101-cosmosdb-table/azuredeploy.json \
   --parameters accountName=$accountName primaryRegion=$primaryRegion secondaryRegion=$secondaryRegion tableName=$tableName

az cosmosdb show --resource-group $resourceGroupName --name accountName --output tsv
```

`az cosmosdb show` コマンドは、新しく作成された Azure Cosmos アカウントをそのプロビジョニング後に表示します。 Cloud Shell を使用する代わりに、ローカルでインストールされたバージョンの Azure CLI を使用することを選択する場合は、[Azure CLI](/cli/azure/) に関する記事を参照してください。

## <a name="next-steps"></a>次の手順

次にその他のリソースを示します。

- [Azure Resource Manager のドキュメント](/azure/azure-resource-manager/)
- [Azure Cosmos DB リソース プロバイダー スキーマ](/azure/templates/microsoft.documentdb/allversions)
- [Azure Cosmos DB クイックスタート テンプレート](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.DocumentDB&pageNumber=1&sort=Popular)
- [Azure Resource Manager デプロイの一般的なエラーのトラブルシューティング](../azure-resource-manager/resource-manager-common-deployment-errors.md)