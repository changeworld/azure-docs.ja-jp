---
title: MongoDB 用 Azure Cosmos DB API の Azure Resource Manager テンプレート
description: Azure Resource Manager テンプレートを使用して、MongoDB 用 Azure Cosmos DB API を作成および構成します。
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 08/05/2019
ms.author: mjbrown
ms.openlocfilehash: 92aaa42aeca294db62445a9434d0b5838a1f3855
ms.sourcegitcommit: c62a68ed80289d0daada860b837c31625b0fa0f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/05/2019
ms.locfileid: "73606533"
---
# <a name="manage-azure-cosmos-db-mongodb-api-resources-using-azure-resource-manager-templates"></a>Azure Resource Manager テンプレートを使用して Azure Cosmos DB MongoDB API リソースを管理する

この記事では、Azure Resource Manager テンプレートを使用して Azure Cosmos DB のアカウント、データベース、コンテナーの管理を自動化するさまざまな操作を実行する方法について説明します。 この記事に含まれているのは、MongoDB 用の Azure Cosmos DB の API の例のみです。他の種類の API アカウントでの例については、[Cassandra](manage-cassandra-with-resource-manager.md)、[Gremlin](manage-gremlin-with-resource-manager.md)、[SQL](manage-sql-with-resource-manager.md)、[Table](manage-table-with-resource-manager.md) 用の Azure Cosmos DB の API での Resource Manager テンプレートの使用に関する記事を参照してください。

## MongoDB アカウント、データベース、コレクション用の Azure Cosmos DB API を作成する <a id="create-resource"></a>

Azure Resource Manager テンプレートを使用して Azure Cosmos DB リソースを作成します。 このテンプレートは、データベース レベルで 400 RU/秒のスループットを共有する 2 つのコレクションを含む MongoDB API の Azure Cosmos アカウントを作成します。 テンプレートをコピーして次に示すようにデプロイするか、[Azure クイック スタート ギャラリー](https://azure.microsoft.com/resources/templates/101-cosmosdb-mongodb/)にアクセスして Azure portal からデプロイします。 テンプレートをローカル コンピューターにダウンロードするか、新しいテンプレートを作成して、`--template-file` パラメーターでローカル パスを指定することもできます。

> [!NOTE]
> アカウント名は小文字とし、31 文字未満にする必要があります。

[!code-json[create-cosmos-mongo](~/quickstart-templates/101-cosmosdb-mongodb/azuredeploy.json)]

### <a name="deploy-via-azure-cli"></a>Azure CLI によるデプロイ

Azure CLI を使用して Resource Manager テンプレートをデプロイするには、スクリプトの **[コピー]** を実行し、 **[試してみる]** を選択して、Azure Cloud Shell を開きます。 スクリプトを貼り付けるには、シェルを右クリックし、 **[貼り付け]** を選択します。

```azurecli-interactive

read -p 'Enter the Resource Group name: ' resourceGroupName
read -p 'Enter the location (i.e. westus2): ' location
read -p 'Enter the account name: ' accountName
read -p 'Enter the primary region (i.e. westus2): ' primaryRegion
read -p 'Enter the secondary region (i.e. eastus2): ' secondaryRegion
read -p 'Enter the database name: ' databaseName
read -p 'Enter the first collection name: ' collection1Name
read -p 'Enter the second collection name: ' collection2Name

az group create --name $resourceGroupName --location $location
az group deployment create --resource-group $resourceGroupName \
  --template-uri https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/101-cosmosdb-mongodb/azuredeploy.json \
  --parameters accountName=$accountName primaryRegion=$primaryRegion secondaryRegion=$secondaryRegion \
  databaseName=$databaseName collection1Name=$collection1Name collection2Name=$collection2Name

az cosmosdb show --resource-group $resourceGroupName --name accountName --output tsv
```

`az cosmosdb show` コマンドは、新しく作成された Azure Cosmos アカウントをそのプロビジョニング後に表示します。 Cloud Shell を使用せずに、Azure CLI のローカルでインストールされたバージョンを使用する場合、「[Azure コマンド ライン インターフェイス (CLI)](/cli/azure/)」の記事を参照してください。

## データベースのスループット (RU/秒) を更新する <a id="database-ru-update"></a>

次のテンプレートでは、データベースのスループットが更新されます。 テンプレートをコピーして次に示すようにデプロイするか、[Azure クイック スタート ギャラリー](https://azure.microsoft.com/resources/templates/101-cosmosdb-mongodb-database-ru-update/)にアクセスして Azure portal からデプロイします。 テンプレートをローカル コンピューターにダウンロードするか、新しいテンプレートを作成して、`--template-file` パラメーターでローカル パスを指定することもできます。

[!code-json[cosmosdb-mongodb-database-ru-update](~/quickstart-templates/101-cosmosdb-mongodb-database-ru-update/azuredeploy.json)]

### <a name="deploy-database-template-via-azure-cli"></a>Azure CLI を使用してデータベース テンプレートをデプロイする

Azure CLI を使用して Resource Manager テンプレートをデプロイするには、 **[試してみる]** を選択して、Azure Cloud Shell を開きます。 スクリプトを貼り付けるには、シェルを右クリックし、 **[貼り付け]** を選択します。

```azurecli-interactive
read -p 'Enter the Resource Group name: ' resourceGroupName
read -p 'Enter the account name: ' accountName
read -p 'Enter the database name: ' databaseName
read -p 'Enter the new throughput: ' throughput

az group deployment create --resource-group $resourceGroupName \
   --template-uri https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/101-cosmosdb-mongodb-database-ru-update/azuredeploy.json \
   --parameters accountName=$accountName databaseName=$databaseName throughput=$throughput
```

## コレクションのスループット (RU/秒) を更新する <a id="collection-ru-update"></a>

次のテンプレートでは、コレクションのスループットが更新されます。 テンプレートをコピーして次に示すようにデプロイするか、[Azure クイック スタート ギャラリー](https://azure.microsoft.com/resources/templates/101-cosmosdb-mongodb-collection-ru-update/)にアクセスして Azure portal からデプロイします。 テンプレートをローカル コンピューターにダウンロードするか、新しいテンプレートを作成して、`--template-file` パラメーターでローカル パスを指定することもできます。

[!code-json[cosmosdb-mongodb-collection-ru-update](~/quickstart-templates/101-cosmosdb-mongodb-collection-ru-update/azuredeploy.json)]

### <a name="deploy-collection-template-via-azure-cli"></a>Azure CLI を使用してコレクション テンプレートをデプロイする

Azure CLI を使用して Resource Manager テンプレートをデプロイするには、 **[試してみる]** を選択して、Azure Cloud Shell を開きます。 スクリプトを貼り付けるには、シェルを右クリックし、 **[貼り付け]** を選択します。

```azurecli-interactive
read -p 'Enter the Resource Group name: ' resourceGroupName
read -p 'Enter the account name: ' accountName
read -p 'Enter the database name: ' databaseName
read -p 'Enter the collection name: ' collectionName
read -p 'Enter the new throughput: ' throughput

az group deployment create --resource-group $resourceGroupName \
   --template-uri https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/101-cosmosdb-mongodb-collection-ru-update/azuredeploy.json \
   --parameters accountName=$accountName databaseName=$databaseName collectionName=$collectionName throughput=$throughput
```

## <a name="next-steps"></a>次の手順

次にその他のリソースを示します。

- [Azure Resource Manager のドキュメント](/azure/azure-resource-manager/)
- [Azure Cosmos DB リソース プロバイダー スキーマ](/azure/templates/microsoft.documentdb/allversions)
- [Azure Cosmos DB クイックスタート テンプレート](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.DocumentDB&pageNumber=1&sort=Popular)
- [Azure Resource Manager デプロイの一般的なエラーのトラブルシューティング](../azure-resource-manager/resource-manager-common-deployment-errors.md)