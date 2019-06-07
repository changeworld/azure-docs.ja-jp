---
title: Azure Resource Manager テンプレートを使用して Azure Cosmos DB を作成および管理する
description: Azure Resource Manager テンプレートを使用して SQL (コア) 用 Azure Cosmos DB API を作成および構成する
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/20/2019
ms.author: mjbrown
ms.openlocfilehash: a3798ac0c73c7bc6c4012dbb089275254f4c3504
ms.sourcegitcommit: e9a46b4d22113655181a3e219d16397367e8492d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/21/2019
ms.locfileid: "65968842"
---
# <a name="manage-azure-cosmos-db-sql-core-api-resources-using-azure-resource-manager-templates"></a>Azure Resource Manager テンプレートを使用して Azure Cosmos DB SQL (コア) API リソースを管理する

## Azure Cosmos アカウント、データベース、コンテナーを作成する <a id="create-resource"></a>

Azure Resource Manager テンプレートを使用して Azure Cosmos DB リソースを作成します。 このテンプレートは、データベース レベルで 400 RU/秒のスループットを共有する 2 つのコンテナーを含む Azure Cosmos アカウントを作成します。 テンプレートをコピーして次に示すようにデプロイするか、[Azure クイック スタート ギャラリー](https://azure.microsoft.com/resources/templates/101-cosmosdb-sql/)にアクセスして Azure portal からデプロイします。 テンプレートをローカル コンピューターにダウンロードするか、新しいテンプレートを作成して、`--template-file` パラメーターでローカル パスを指定することもできます。

[!code-json[create-cosmosdb-sql](~/quickstart-templates/101-cosmosdb-sql/azuredeploy.json)]

### <a name="deploy-via-powershell"></a>PowerShell 経由でのデプロイ

PowerShell を使用して Resource Manager テンプレートをデプロイするには、スクリプトの **[コピー]** を実行し、 **[試してみる]** を選択して、Azure Cloud Shell を開きます。 スクリプトを貼り付けるには、シェルを右クリックし、 **[貼り付け]** を選択します。

```azurepowershell-interactive

$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
$accountName = Read-Host -Prompt "Enter the account name"
$location = Read-Host -Prompt "Enter the location (i.e. westus2)"
$primaryRegion = Read-Host -Prompt "Enter the primary region (i.e. westus2)"
$secondaryRegion = Read-Host -Prompt "Enter the secondary region (i.e. eastus2)"
$databaseName = Read-Host -Prompt "Enter the database name"
$container1Name = Read-Host -Prompt "Enter the first container name"
$container2Name = Read-Host -Prompt "Enter the second container name"

New-AzResourceGroup -Name $resourceGroupName -Location $location
New-AzResourceGroupDeployment `
    -ResourceGroupName $resourceGroupName `
    -TemplateUri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-cosmosdb-sql/azuredeploy.json" `
    -accountName $accountName `
    -location $location `
    -primaryRegion $primaryRegion `
    -secondaryRegion $secondaryRegion `
    -databaseName $databaseName `
    -container1Name $container1Name `
    -container2Name $container2Name

 (Get-AzResource --ResourceType "Microsoft.DocumentDb/databaseAccounts" --ApiVersion "2015-04-08" --ResourceGroupName $resourceGroupName).name
```

Azure Cloud Shell からではなく、PowerShell のローカルでインストールされたバージョンを使用する場合、Azure PowerShell モジュールを[インストール](/powershell/azure/install-az-ps)する必要があります。 バージョンを確認するには、`Get-Module -ListAvailable Az` を実行します。

### <a name="deploy-via-azure-cli"></a>Azure CLI によるデプロイ

Azure CLI を使用して Resource Manager テンプレートをデプロイするには、 **[試してみる]** を選択して、Azure Cloud Shell を開きます。 スクリプトを貼り付けるには、シェルを右クリックし、 **[貼り付け]** を選択します。

```azurecli-interactive
read -p 'Enter the Resource Group name: ' resourceGroupName
read -p 'Enter the location (i.e. westus2): ' location
read -p 'Enter the account name: ' accountName
read -p 'Enter the primary region (i.e. westus2): ' primaryRegion
read -p 'Enter the secondary region (i.e. eastus2): ' secondaryRegion
read -p 'Enter the database name: ' databaseName
read -p 'Enter the first container name: ' container1Name
read -p 'Enter the second container name: ' container2Name

az group create --name $resourceGroupName --location $location
az group deployment create --resource-group $resourceGroupName \
   --template-uri https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/101-cosmosdb-sql/azuredeploy.json \
   --parameters accountName=$accountName primaryRegion=$primaryRegion secondaryRegion=$secondaryRegion databaseName=$databaseName \
   container1Name=$container1Name container2Name=$container2Name

az cosmosdb show --resource-group $resourceGroupName --name accountName --output tsv
```

`az cosmosdb show` コマンドは、新しく作成された Azure Cosmos アカウントをそのプロビジョニング後に表示します。 Cloud Shell を使用せずに、Azure CLI のローカルでインストールされたバージョンを使用する場合、「[Azure コマンド ライン インターフェイス (CLI)](/cli/azure/)」の記事を参照してください。

## データベースのスループット (RU/秒) を更新する <a id="database-ru-update"></a>

次のテンプレートでは、データベースのスループットが更新されます。 テンプレートをコピーして次に示すようにデプロイするか、[Azure クイック スタート ギャラリー](https://azure.microsoft.com/resources/templates/101-cosmosdb-sql-database-ru-update/)にアクセスして Azure portal からデプロイします。 テンプレートをローカル コンピューターにダウンロードするか、新しいテンプレートを作成して、`--template-file` パラメーターでローカル パスを指定することもできます。

[!code-json[cosmosdb-sql-database-ru-update](~/quickstart-templates/101-cosmosdb-sql-database-ru-update/azuredeploy.json)]

### <a name="deploy-database-template-via-powershell"></a>PowerShell を使用してデータベース テンプレートをデプロイする

PowerShell を使用して Resource Manager テンプレートをデプロイするには、スクリプトの **[コピー]** を実行し、 **[試してみる]** を選択して、Azure Cloud Shell を開きます。 スクリプトを貼り付けるには、シェルを右クリックし、 **[貼り付け]** を選択します。

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
$accountName = Read-Host -Prompt "Enter the account name"
$databaseName = Read-Host -Prompt "Enter the database name"
$throughput = Read-Host -Prompt "Enter new throughput for database"

New-AzResourceGroupDeployment `
    -ResourceGroupName $resourceGroupName `
    -TemplateUri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-cosmosdb-sql-database-ru-update/azuredeploy.json" `
    -accountName $accountName `
    -databaseName $databaseName `
    -throughput $throughput
```

### <a name="deploy-database-template-via-azure-cli"></a>Azure CLI を使用してデータベース テンプレートをデプロイする

Azure CLI を使用して Resource Manager テンプレートをデプロイするには、 **[試してみる]** を選択して、Azure Cloud Shell を開きます。 スクリプトを貼り付けるには、シェルを右クリックし、 **[貼り付け]** を選択します。

```azurecli-interactive
read -p 'Enter the Resource Group name: ' resourceGroupName
read -p 'Enter the account name: ' accountName
read -p 'Enter the database name: ' databaseName
read -p 'Enter the new throughput: ' throughput

az group deployment create --resource-group $resourceGroupName \
   --template-uri https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/101-cosmosdb-sql-database-ru-update/azuredeploy.json \
   --parameters accountName=$accountName databaseName=$databaseName throughput=$throughput
```

## コンテナーのスループット (RU/秒) を更新する <a id="container-ru-update"></a>

次のテンプレートでは、コンテナーのスループットが更新されます。 テンプレートをコピーして次に示すようにデプロイするか、[Azure クイック スタート ギャラリー](https://azure.microsoft.com/resources/templates/101-cosmosdb-sql-container-ru-update/)にアクセスして Azure portal からデプロイします。 テンプレートをローカル コンピューターにダウンロードするか、新しいテンプレートを作成して、`--template-file` パラメーターでローカル パスを指定することもできます。

[!code-json[cosmosdb-sql-container-ru-update](~/quickstart-templates/101-cosmosdb-sql-container-ru-update/azuredeploy.json)]

### <a name="deploy-container-template-via-powershell"></a>PowerShell を使用してコンテナー テンプレートをデプロイする

PowerShell を使用して Resource Manager テンプレートをデプロイするには、スクリプトの **[コピー]** を実行し、 **[試してみる]** を選択して、Azure Cloud Shell を開きます。 スクリプトを貼り付けるには、シェルを右クリックし、 **[貼り付け]** を選択します。

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
$accountName = Read-Host -Prompt "Enter the account name"
$databaseName = Read-Host -Prompt "Enter the database name"
$containerName = Read-Host -Prompt "Enter the container name"
$throughput = Read-Host -Prompt "Enter new throughput for container"

New-AzResourceGroupDeployment `
    -ResourceGroupName $resourceGroupName `
    -TemplateUri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-cosmosdb-sql-container-ru-update/azuredeploy.json" `
    -accountName $accountName `
    -databaseName $databaseName `
    -containerName $containerName `
    -throughput $throughput
```

### <a name="deploy-container-template-via-azure-cli"></a>Azure CLI を使用してコンテナー テンプレートをデプロイする

Azure CLI を使用して Resource Manager テンプレートをデプロイするには、 **[試してみる]** を選択して、Azure Cloud Shell を開きます。 スクリプトを貼り付けるには、シェルを右クリックし、 **[貼り付け]** を選択します。

```azurecli-interactive
read -p 'Enter the Resource Group name: ' resourceGroupName
read -p 'Enter the account name: ' accountName
read -p 'Enter the database name: ' databaseName
read -p 'Enter the container name: ' containerName
read -p 'Enter the new throughput: ' throughput

az group deployment create --resource-group $resourceGroupName \
   --template-uri https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/101-cosmosdb-sql-container-ru-update/azuredeploy.json \
   --parameters accountName=$accountName databaseName=$databaseName containerName=$containerName throughput=$throughput
```

## <a name="next-steps"></a>次の手順

次にその他のリソースを示します。

- [Azure Resource Manager のドキュメント](/azure/azure-resource-manager/)
- [Azure Cosmos DB リソース プロバイダー スキーマ](/azure/templates/microsoft.documentdb/allversions)
- [Azure Cosmos DB クイックスタート テンプレート](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.DocumentDB&pageNumber=1&sort=Popular)
- [Azure Resource Manager デプロイの一般的なエラーのトラブルシューティング](../azure-resource-manager/resource-manager-common-deployment-errors.md)