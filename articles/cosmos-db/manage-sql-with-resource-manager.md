---
title: Azure Resource Manager テンプレートを使用して Azure Cosmos DB を作成および管理する
description: Azure Resource Manager テンプレートを使用して SQL (コア) 用 Azure Cosmos DB API を作成および構成する
author: TheovanKraay
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 11/12/2019
ms.author: thvankra
ms.openlocfilehash: 62c04fed03ad2346d0f548a4a8028f2d7d6b3486
ms.sourcegitcommit: c38a1f55bed721aea4355a6d9289897a4ac769d2
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/05/2019
ms.locfileid: "74850467"
---
# <a name="manage-azure-cosmos-db-sql-core-api-resources-with-azure-resource-manager-templates"></a>Azure Resource Manager テンプレートを使用して Azure Cosmos DB SQL (コア) API リソースを管理する

この記事では、ご利用の Azure Cosmos DB アカウント、データベース、およびコンテナーの管理を自動化するのに役立つ Azure Resource Manager テンプレートの使用方法について説明します。

この記事では、SQL API アカウント用の Azure Resource Manager テンプレートの例のみを示します。 [Cassandra](manage-cassandra-with-resource-manager.md)、[Gremlin](manage-gremlin-with-resource-manager.md)、[MongoDB](manage-mongodb-with-resource-manager.md)、[Table](manage-table-with-resource-manager.md) の API の例もあります。

<a id="create-resource"></a>

## <a name="create-an-azure-cosmos-account-database-and-container"></a>Azure Cosmos アカウント、データベース、コンテナーを作成する

以下の Azure Resource Manager テンプレートでは、次のものを使用して Azure Cosmos アカウントを作成します。

* 1 秒あたりの要求ユニット (RU/秒) が 400 のスループットをデータベース レベルで共有する 2 つのコンテナー。
* 400 RU/秒の専用スループットを持つ 1 つのコンテナー。

Azure Cosmos DB リソースを作成するには、次のテンプレート例をコピーし、それを説明に従って [PowerShell](#deploy-via-powershell) または [Azure CLI](#deploy-via-azure-cli) を介してデプロイします。

* 必要に応じて、[Azure クイック スタート ギャラリー](https://azure.microsoft.com/resources/templates/101-cosmosdb-sql/)にアクセスし、Azure portal からテンプレートをデプロイできます。
* テンプレートをローカル コンピューターにダウンロードするか、新しいテンプレートを作成して、`--template-file` パラメーターでローカル パスを指定することもできます。

> [!IMPORTANT]
>
> * Azure Cosmos アカウントに対して場所の追加または削除を行う場合、他のプロパティを同時に変更することはできません。 これらの操作は個別に行う必要があります。
> * アカウント名は、44 文字 (すべて小文字) に制限されています。
> * スループットの値を変更するには、RU/秒を更新したテンプレートを再送信します。

[!code-json[create-cosmosdb-sql](~/quickstart-templates/101-cosmosdb-sql/azuredeploy.json)]

> [!NOTE]
> 大きいパーティション キーを持つコンテナーを作成するには、前のテンプレートを変更して、`partitionKey` オブジェクト内に `"version":2` プロパティを含めます。

### <a name="deploy-via-powershell"></a>PowerShell 経由でのデプロイ

PowerShell を使用して Azure Resource Manager テンプレートをデプロイするには、次のようにします。

1. スクリプトを**コピー**します。
2. **[試してみる]** を選択して、Azure Cloud Shell を開きます。
3. Azure Cloud Shell ウィンドウ内を右クリックしてから、 **[貼り付け]** を選択します。

```azurepowershell-interactive

$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
$accountName = Read-Host -Prompt "Enter the account name"
$location = Read-Host -Prompt "Enter the location (i.e. westus2)"
$primaryRegion = Read-Host -Prompt "Enter the primary region (i.e. westus2)"
$secondaryRegion = Read-Host -Prompt "Enter the secondary region (i.e. eastus2)"
$databaseName = Read-Host -Prompt "Enter the database name"
$sharedThroughput = Read-Host -Prompt "Enter the shared database throughput (i.e. 400)"
$sharedContainer1Name = Read-Host -Prompt "Enter the first shared container name"
$sharedContainer2Name = Read-Host -Prompt "Enter the second shared container name"
$dedicatedContainer1Name = Read-Host -Prompt "Enter the dedicated container name"
$dedicatedThroughput = Read-Host -Prompt "Enter the dedicated container throughput (i.e. 400)"

New-AzResourceGroup -Name $resourceGroupName -Location $location
New-AzResourceGroupDeployment `
    -ResourceGroupName $resourceGroupName `
    -TemplateUri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-cosmosdb-sql/azuredeploy.json" `
    -accountName $accountName `
    -location $location `
    -primaryRegion $primaryRegion `
    -secondaryRegion $secondaryRegion `
    -databaseName $databaseName `
    -sharedThroughput $ $sharedThroughput `
    -sharedContainer1Name $sharedContainer1Name `
    -sharedContainer2Name $sharedContainer2Name `
    -dedicatedContainer1Name $dedicatedContainer1Name `
    -dedicatedThroughput $dedicatedThroughput

 (Get-AzResource --ResourceType "Microsoft.DocumentDb/databaseAccounts" --ApiVersion "2019-08-01" --ResourceGroupName $resourceGroupName).name
```

Azure Cloud Shell ではなく、ローカルにインストールされたバージョンの PowerShell を使用してテンプレートをデプロイすることも選択できます。 [Azure PowerShell モジュールをインストール](/powershell/azure/install-az-ps)する必要があります。 `Get-Module -ListAvailable Az` を実行して、必要なバージョンを見つけます。

### <a name="deploy-via-azure-cli"></a>Azure CLI によるデプロイ

Azure CLI を使用して Azure Resource Manager テンプレートをデプロイするには、次のようにします。

1. スクリプトを**コピー**します。
2. **[試してみる]** を選択して、Azure Cloud Shell を開きます。
3. Azure Cloud Shell ウィンドウ内を右クリックしてから、 **[貼り付け]** を選択します。

```azurecli-interactive
read -p 'Enter the Resource Group name: ' resourceGroupName
read -p 'Enter the location (i.e. westus2): ' location
read -p 'Enter the account name: ' accountName
read -p 'Enter the primary region (i.e. westus2): ' primaryRegion
read -p 'Enter the secondary region (i.e. eastus2): ' secondaryRegion
read -p 'Enter the database name: ' databaseName
read -p 'Enter the shared database throughput: sharedThroughput
read -p 'Enter the first shared container name: ' sharedContainer1Name
read -p 'Enter the second shared container name: ' sharedContainer2Name
read -p 'Enter the dedicated container name: ' dedicatedContainer1Name
read -p 'Enter the dedicated container throughput: dedicatedThroughput

az group create --name $resourceGroupName --location $location
az group deployment create --resource-group $resourceGroupName \
   --template-uri https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/101-cosmosdb-sql/azuredeploy.json \
   --parameters accountName=$accountName \
   primaryRegion=$primaryRegion \
   secondaryRegion=$secondaryRegion \
   databaseName=$databaseName \
   sharedThroughput=$sharedThroughput \
   sharedContainer1Name=$sharedContainer1Name \
   sharedContainer2Name=$sharedContainer2Name \
   dedicatedContainer1Name=$dedicatedContainer1Name \
   dedicatedThroughput=$dedicatedThroughput

az cosmosdb show --resource-group $resourceGroupName --name accountName --output tsv
```

`az cosmosdb show` コマンドを使用すると、新しく作成した Azure Cosmos アカウントを、それがプロビジョニングされた後に表示できます。 Azure Cloud Shell ではなく、ローカルにインストールされたバージョンの Azure CLI を使用してテンプレートをデプロイすることも選択できます。 詳細については、記事「[Azure コマンド ライン インターフェイス (CLI)](/cli/azure/)」を参照してください。

<a id="create-sproc"></a>

## <a name="create-an-azure-cosmos-db-container-with-server-side-functionality"></a>サーバー側機能を使用して Azure Cosmos DB コンテナーを作成する

Azure Resource Manager テンプレートを使用することで、ストアド プロシージャ、トリガー、ユーザー定義関数を含む Azure Cosmos DB コンテナーを作成することができます。

次のテンプレート例をコピーし、それを説明に従って [PowerShell](#deploy-with-powershell) または [Azure CLI](#deploy-with-azure-cli) を使用してデプロイします。

* 必要に応じて、[Azure クイック スタート ギャラリー](https://azure.microsoft.com/resources/templates/101-cosmosdb-sql-container-sprocs/)にアクセスし、Azure portal からテンプレートをデプロイできます。
* テンプレートをローカル コンピューターにダウンロードするか、新しいテンプレートを作成して、`--template-file` パラメーターでローカル パスを指定することもできます。

[!code-json[create-cosmosdb-sql-sprocs](~/quickstart-templates/101-cosmosdb-sql-container-sprocs/azuredeploy.json)]

### <a name="deploy-with-powershell"></a>PowerShell でデプロイする

PowerShell を使用して Azure Resource Manager テンプレートをデプロイするには、次のようにします。

1. スクリプトを**コピー**します。
1. **[試してみる]** を選択して、Azure Cloud Shell を開きます。
1. [Azure Cloud Shell] ウィンドウを右クリックして、 **[貼り付け]** を選択します。

```azurepowershell-interactive

$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
$accountName = Read-Host -Prompt "Enter the account name"
$location = Read-Host -Prompt "Enter the location (i.e. westus2)"
$primaryRegion = Read-Host -Prompt "Enter the primary region (i.e. westus2)"
$secondaryRegion = Read-Host -Prompt "Enter the secondary region (i.e. eastus2)"
$databaseName = Read-Host -Prompt "Enter the database name"
$containerName = Read-Host -Prompt "Enter the container name"

New-AzResourceGroup -Name $resourceGroupName -Location $location
New-AzResourceGroupDeployment `
    -ResourceGroupName $resourceGroupName `
    -TemplateUri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-cosmosdb-sql-container-sprocs/azuredeploy.json" `
    -accountName $accountName `
    -location $location `
    -primaryRegion $primaryRegion `
    -secondaryRegion $secondaryRegion `
    -databaseName $databaseName `
    -containerName $containerName

 (Get-AzResource --ResourceType "Microsoft.DocumentDb/databaseAccounts" --ApiVersion "2019-08-01" --ResourceGroupName $resourceGroupName).name
```

Azure Cloud Shell ではなく、ローカルにインストールされたバージョンの PowerShell を使用してテンプレートをデプロイすることも選択できます。 [Azure PowerShell モジュールをインストール](/powershell/azure/install-az-ps)する必要があります。 `Get-Module -ListAvailable Az` を実行して、必要なバージョンを見つけます。

### <a name="deploy-with-azure-cli"></a>Azure CLI でのデプロイ

Azure CLI を使用して Azure Resource Manager テンプレートをデプロイするには、次のようにします。

1. スクリプトを**コピー**します。
2. **[試してみる]** を選択して、Azure Cloud Shell を開きます。
3. Azure Cloud Shell ウィンドウ内を右クリックしてから、 **[貼り付け]** を選択します。

```azurecli-interactive
read -p 'Enter the Resource Group name: ' resourceGroupName
read -p 'Enter the location (i.e. westus2): ' location
read -p 'Enter the account name: ' accountName
read -p 'Enter the primary region (i.e. westus2): ' primaryRegion
read -p 'Enter the secondary region (i.e. eastus2): ' secondaryRegion
read -p 'Enter the database name: ' databaseName
read -p 'Enter the container name: ' containerName

az group create --name $resourceGroupName --location $location
az group deployment create --resource-group $resourceGroupName \
   --template-uri https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/101-cosmosdb-sql-container-sprocs/azuredeploy.json \
   --parameters accountName=$accountName primaryRegion=$primaryRegion secondaryRegion=$secondaryRegion databaseName=$databaseName \
   containerName=$containerName

az cosmosdb show --resource-group $resourceGroupName --name accountName --output tsv
```

## <a name="next-steps"></a>次の手順

次にその他のリソースを示します。

* [Azure Resource Manager のドキュメント](/azure/azure-resource-manager/)
* [Azure Cosmos DB リソース プロバイダー スキーマ](/azure/templates/microsoft.documentdb/allversions)
* [Azure Cosmos DB クイックスタート テンプレート](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.DocumentDB&pageNumber=1&sort=Popular)
* [Azure Resource Manager デプロイの一般的なエラーのトラブルシューティング](../azure-resource-manager/resource-manager-common-deployment-errors.md)
