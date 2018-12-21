---
title: Azure Cosmos DB のコンテナーのスループットをプロビジョニングする
description: Azure Cosmos DB のコンテナー レベルでスループットをプロビジョニングする方法について説明します。
services: cosmos-db
author: markjbrown
ms.service: cosmos-db
ms.topic: sample
ms.date: 11/06/2018
ms.author: mjbrown
ms.openlocfilehash: dd47976bca75569142f1912eee06c66061e92fa6
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/08/2018
ms.locfileid: "53097667"
---
# <a name="provision-throughput-for-an-azure-cosmos-db-container"></a>Azure Cosmos DB コンテナーのスループットをプロビジョニングする

この記事では、Azure Cosmos DB のコンテナー (コレクション、グラフ、テーブル) のスループットをプロビジョニングする方法について説明します。 スループットは、単一のコンテナーを対象にプロビジョニングできるほか、[データベースを対象にプロビジョニング](how-to-provision-database-throughput.md)して、それをデータベース内の複数のコンテナーで共有することもできます。 コンテナーのスループットは、Azure portal、Azure CLI、CosmosDB SDK のいずれかを使用してプロビジョニングできます。

## <a name="provision-throughput-using-azure-portal"></a>Azure portal を使用してスループットをプロビジョニングする

1. [Azure ポータル](https://portal.azure.com/)にサインインします。

1. [新しい Cosmos DB アカウントを作成](create-sql-api-dotnet.md#create-a-database-account)するか、または既存のアカウントを選択します。

1. **[データ エクスプローラー]** ウィンドウを開いて **[新しいコレクション]** を選択します。 続けてフォームに次の詳細を入力します。

   * 新しいデータベースを作成するか、既存のデータベースを使用します。
   * コレクション ID (またはテーブル、グラフ) を入力します。
   * パーティション キーの値 (`/userid` など) を入力します。
   * スループットを入力します (例: 1000 RU)。
   * **[OK]** を選択します。

![SQL API でコンテナーのスループットをプロビジョニング](./media/how-to-provision-container-throughput/provision-container-throughput-portal-all-api.png)

## <a name="provision-throughput-using-azure-cli"></a>Azure CLI を使用してスループットをプロビジョニングする

```azurecli-interactive
# Create a container with a partition key and provision throughput of 1000 RU/s
az cosmosdb collection create \
    --resource-group $resourceGroupName \
    --collection-name $containerName \
    --name $accountName \
    --db-name $databaseName \
    --partition-key-path /myPartitionKey \
    --throughput 1000
```

MongoDB API アカウントのスループットをプロビジョニングする場合は、パーティション キーのパスに "/myShardKey" を、Cassandra API アカウントのスループットをプロビジョニングするときは、パーティション キーのパスに "/myPrimaryKey" を使用します。

## <a name="provision-throughput-using-net-sdk"></a>.NET SDK を使用してスループットをプロビジョニング

> [!Note]
> Cassandra API を除くすべての API は、SQL API を使用してスループットをプロビジョニングします。

### <a id="dotnet-most"></a>SQL API、MongoDB API、Gremlin API、Table API

```csharp
// Create a container with a partition key and provision throughput of 1000 RU/s
DocumentCollection myCollection = new DocumentCollection();
myCollection.Id = "myContainerName";
myCollection.PartitionKey.Paths.Add("/myPartitionKey");

await client.CreateDocumentCollectionAsync(
    UriFactory.CreateDatabaseUri("myDatabaseName"),
    myCollection,
    new RequestOptions { OfferThroughput = 1000 });
```

### <a id="dotnet-cassandra"></a>Cassandra API

```csharp
// Create a Cassandra table with a partition (primary) key and provision throughput of 1000 RU/s
session.Execute(CREATE TABLE myKeySpace.myTable(
    user_id int PRIMARY KEY,
    firstName text,
    lastName text) WITH cosmosdb_provisioned_throughput=1000);
```

## <a name="next-steps"></a>次の手順

Cosmos DB におけるスループットのプロビジョニングについては、次の記事を参照してください。

* [データベースのスループットをプロビジョニングする方法](how-to-provision-database-throughput.md)
* [Azure Cosmos DB における要求ユニットとスループット](request-units.md)
