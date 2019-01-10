---
title: Azure Cosmos DB にコンテナーを作成する
description: Azure Cosmos DB にコンテナーを作成する方法について説明します
author: markjbrown
ms.service: cosmos-db
ms.topic: sample
ms.date: 11/06/2018
ms.author: mjbrown
ms.openlocfilehash: 7434407c7d1afe197a560d9ed9c4f1e7e94b6c2b
ms.sourcegitcommit: 8330a262abaddaafd4acb04016b68486fba5835b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/04/2019
ms.locfileid: "54033538"
---
# <a name="create-a-container-in-azure-cosmos-db"></a>Azure Cosmos DB にコンテナーを作成する

この記事では、コンテナー (コレクション、テーブル、グラフ) を作成するいくつかの方法について説明します。 コンテナーは、Azure portal、Azure CLI、またはサポートされている SDK を使用して作成することができます。 この記事では、コンテナーを作成してパーティション キーを指定し、スループットをプロビジョニングする方法を紹介します。

## <a name="create-a-container-using-azure-portal"></a>Azure portal を使用してコンテナーを作成する

### <a id="portal-sql"></a>SQL API

1. [Azure ポータル](https://portal.azure.com/)にサインインします。

1. [新しい Cosmos DB アカウントを作成](create-sql-api-dotnet.md#create-a-database-account)するか、または既存のアカウントを選択します。

1. **[データ エクスプローラー]** ウィンドウを開いて **[新しいコレクション]** を選択します。 続けてフォームに次の詳細を入力します。

   * 新しいデータベースを作成するか、既存のデータベースを使用します。
   * コレクション ID を入力します。
   * パーティション キーを入力します。
   * スループットを入力します (例: 1000 RU)。
   * **[OK]** を選択します。

![SQL API でコレクションを作成](./media/how-to-create-container/partitioned-collection-create-sql.png)

### <a id="portal-mongodb"></a>Azure Cosmos DB の MongoDB 用 API

1. [Azure ポータル](https://portal.azure.com/)にサインインします。

1. [新しい Cosmos DB アカウントを作成](create-mongodb-dotnet.md#create-a-database-account)するか、または既存のアカウントを選択します。

1. **[データ エクスプローラー]** ウィンドウを開いて **[新しいコレクション]** を選択します。 続けてフォームに次の詳細を入力します。

   * 新しいデータベースを作成するか、既存のデータベースを使用します。
   * コレクション ID を入力します。
   * **[無制限]** のストレージ容量を選択します。
   * シャード キーを入力します。
   * スループットを入力します (例: 1000 RU)。
   * **[OK]** を選択します。

![Azure Cosmos DB の MongoDB 用 API でコレクションが作成される](./media/how-to-create-container/partitioned-collection-create-mongodb.png)

### <a id="portal-cassandra"></a>Cassandra API

1. [Azure ポータル](https://portal.azure.com/)にサインインします。

1. [新しい Cosmos DB アカウントを作成](create-cassandra-dotnet.md#create-a-database-account)するか、または既存のアカウントを選択します。

1. **[データ エクスプローラー]** ウィンドウを開いて **[新しいテーブル]** を選択します。 続けてフォームに次の詳細を入力します。

   * 新しいキースペースを作成するか、既存のキースペースを使用します。
   * テーブルの名前を入力します。
   * プロパティを入力し、PRIMARY KEY を指定します。
   * スループットを入力します (例: 1000 RU)。
   * **[OK]** を選択します。

![Cassandra API でコレクションを作成](./media/how-to-create-container/partitioned-collection-create-cassandra.png)

> [!NOTE]
> Cassandra API では、主キーがパーティション キーとして使用されます。

### <a id="portal-gremlin"></a>Gremlin API

1. [Azure ポータル](https://portal.azure.com/)にサインインします。

1. [新しい Cosmos DB アカウントを作成](create-graph-dotnet.md#create-a-database-account)するか、または既存のアカウントを選択します。

1. **[データ エクスプローラー]** ウィンドウを開いて **[新しいグラフ]** を選択します。 続けてフォームに次の詳細を入力します。

   * 新しいデータベースを作成するか、既存のデータベースを使用します。
   * グラフ ID を入力します。
   * **[無制限]** のストレージ容量を選択します。
   * 頂点のパーティション キーを入力します。
   * スループットを入力します (例: 1000 RU)。
   * **[OK]** を選択します。

![Gremlin API でコレクションを作成](./media/how-to-create-container/partitioned-collection-create-gremlin.png)

### <a id="portal-table"></a>Table API

1. [Azure ポータル](https://portal.azure.com/)にサインインします。

1. [新しい Cosmos DB アカウントを作成](create-table-dotnet.md#create-a-database-account)するか、または既存のアカウントを選択します。

1. **[データ エクスプローラー]** ウィンドウを開いて **[新しいテーブル]** を選択します。 続けてフォームに次の詳細を入力します。

   * テーブル ID を入力します。
   * **[無制限]** のストレージ容量を選択します。
   * スループットを入力します (例: 1000 RU)。
   * **[OK]** を選択します。

![Table API でコレクションを作成](./media/how-to-create-container/partitioned-collection-create-table.png)

> [!Note]
> Table API では、新しい行を追加するたびにパーティション キーが指定されます。

## <a name="create-a-container-using-azure-cli"></a>Azure CLI を使用してコンテナーを作成する

### <a id="cli-sql"></a>SQL API

```azurecli-interactive
# Create a container with a partition key and provision 1000 RU/s throughput.

az cosmosdb collection create \
    --resource-group $resourceGroupName \
    --collection-name $containerName \
    --name $accountName \
    --db-name $databaseName \
    --partition-key-path /myPartitionKey \
    --throughput 1000
```

### <a id="cli-mongodb"></a>Azure Cosmos DB の MongoDB 用 API

```azurecli-interactive
# Create a collection with a shard key and provision 1000 RU/s throughput.
az cosmosdb collection create \
    --resource-group $resourceGroupName \
    --collection-name $collectionName \
    --name $accountName \
    --db-name $databaseName \
    --partition-key-path /myShardKey \
    --throughput 1000
```

### <a id="cli-cassandra"></a>Cassandra API

```azurecli-interactive
# Create a table with a partition/primary key and provision 1000 RU/s throughput.
az cosmosdb collection create \
    --resource-group $resourceGroupName \
    --collection-name $tableName \
    --name $accountName \
    --db-name $keyspaceName \
    --partition-key-path /myPrimaryKey \
    --throughput 1000
```

### <a id="cli-gremlin"></a>Gremlin API

```azurecli-interactive
# Create a graph with a partition key and provision 1000 RU/s throughput.
az cosmosdb collection create \
    --resource-group $resourceGroupName \
    --collection-name $graphName \
    --name $accountName \
    --db-name $databaseName \
    --partition-key-path /myPartitionKey \
    --throughput 1000
```

### <a id="cli-table"></a>Table API

```azurecli-interactive
# Create a table with 1000 RU/s
# Note: you don't need to specify partition key in the following command because the partition key is set on each row.
az cosmosdb collection create \
    --resource-group $resourceGroupName \
    --collection-name $tableName \
    --name $accountName \
    --db-name $databaseName \
    --throughput 1000
```

## <a name="create-a-container-using-net-sdk"></a>.NET SDK を使用してコンテナーを作成する

### <a id="dotnet-sql-graph"></a>SQL API と Gremlin API

```csharp
// Create a container with a partition key and provision 1000 RU/s throughput.
DocumentCollection myCollection = new DocumentCollection();
myCollection.Id = "myContainerName";
myCollection.PartitionKey.Paths.Add("/myPartitionKey");

await client.CreateDocumentCollectionAsync(
    UriFactory.CreateDatabaseUri("myDatabaseName"),
    myCollection,
    new RequestOptions { OfferThroughput = 1000 });
```

### <a id="dotnet-mongodb"></a>Azure Cosmos DB の MongoDB 用 API

```csharp
// Create a collection with a partition key by using Mongo Shell:
db.runCommand( { shardCollection: "myDatabase.myCollection", key: { myShardKey: "hashed" } } )
```

> [!Note]
MongoDB ワイヤ プロトコルには、要求ユニットという概念はありません。 スループットを指定して新しいコレクションを作成する場合は、前の例で紹介した Azure portal または SQL API を使用してください。

### <a id="dotnet-cassandra"></a>Cassandra API

```csharp
// Create a Cassandra table with a partition/primary key and provision 1000 RU/s throughput.
session.Execute(CREATE TABLE myKeySpace.myTable(
    user_id int PRIMARY KEY,
    firstName text,
    lastName text) WITH cosmosdb_provisioned_throughput=1000);
```

## <a name="next-steps"></a>次の手順

Cosmos DB でのパーティション分割については、次の記事を参照してください。

- [Azure Cosmos DB でのパーティション分割](partitioning-overview.md)
