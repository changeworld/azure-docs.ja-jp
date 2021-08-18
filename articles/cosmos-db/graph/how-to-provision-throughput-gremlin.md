---
title: Azure Cosmos DB Gremlin API リソース スループットのプロビジョニング
description: Azure Cosmos DB Gremlin API リソースでコンテナー、データベース、自動スケールのスループットをプロビジョニングする方法について説明します。 Azure portal、CLI、PowerShell、その他のさまざまな SDK を使用します。
ms.service: cosmos-db
ms.subservice: cosmosdb-graph
ms.topic: how-to
ms.date: 10/15/2020
author: manishmsfte
ms.author: mansha
ms.custom: devx-track-js, devx-track-azurecli, devx-track-csharp
ms.openlocfilehash: 4804bce1fc2b88dac3ee989aee54b12283b28520
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/13/2021
ms.locfileid: "121779295"
---
# <a name="provision-database-container-or-autoscale-throughput-on-azure-cosmos-db-gremlin-api-resources"></a>Azure Cosmos DB Gremlin API リソースのデータベース、コンテナー、または自動スケール スループットのプロビジョニング
[!INCLUDE[appliesto-gremlin-api](../includes/appliesto-gremlin-api.md)]

この記事では、Azure Cosmos DB Gremlin API のスループットをプロビジョニングする方法について説明します。 標準 (手動) または自動スケーリングのスループットは、コンテナーを対象にプロビジョニングできるほか、データベースを対象にプロビジョニングして、それをデータベース内の複数のコンテナーで共有することもできます。 スループットは、Azure portal、Azure CLI、Azure Cosmos DB SDK のいずれかを使用してプロビジョニングできます。

別の API を使用している場合は、[SQL API](../how-to-provision-container-throughput.md)、[Cassandra API](../cassandra/how-to-provision-throughput-cassandra.md)、[API for MongoDB](../mongodb/how-to-provision-throughput-mongodb.md) のスループット プロビジョニングに関する記事を参照してください。

## <a name="azure-portal"></a><a id="portal-gremlin"></a> Azure portal

1. [Azure portal](https://portal.azure.com/) にサインインします。

1. [新しい Azure Cosmos アカウントを作成する](../mongodb/create-mongodb-dotnet.md#create-a-database-account)か、既存の Azure Cosmos アカウントを選択します。

1. **[データ エクスプローラー]** ウィンドウを開いて **[新しいグラフ]** を選択します。 次に、以下の詳細を指定します。

   * 新しいデータベースを作成するか、既存のデータベースを使用するかを指定します。 データベース レベルでスループットをプロビジョニングする場合は、 **[データベースのスループットのプロビジョニング]** オプションを選択します。
   * グラフ ID を入力します。
   * パーティション キーの値 (`/ItemID` など) を入力します。
   * プロビジョニングするスループットを入力します (例: 1,000 RU)。
   * **[OK]** を選択します。

    :::image type="content" source="./media/how-to-provision-throughput-gremlin/provision-database-throughput-portal-gremlin-api.png" alt-text="データベース レベルのスループットで新しいグラフを作成しているときの、データ エクスプローラーのスクリーンショット":::

## <a name="net-sdk"></a>.NET SDK

> [!Note]
> Cassandra および MongoDB API を除くすべての Azure Cosmos DB API に対するスループットをプロビジョニングするには、SQL API 用の Cosmos SDK を使用します。

### <a name="provision-container-level-throughput"></a>コンテナー レベルのスループットのプロビジョニング

# <a name="net-sdk-v2"></a>[.NET SDK V2](#tab/dotnetv2)

```csharp
// Create a container with a partition key and provision throughput of 400 RU/s
DocumentCollection myCollection = new DocumentCollection();
myCollection.Id = "myContainerName";
myCollection.PartitionKey.Paths.Add("/myPartitionKey");

await client.CreateDocumentCollectionAsync(
    UriFactory.CreateDatabaseUri("myDatabaseName"),
    myCollection,
    new RequestOptions { OfferThroughput = 400 });
```

# <a name="net-sdk-v3"></a>[.NET SDK V3](#tab/dotnetv3)

[!code-csharp[](~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos/tests/Microsoft.Azure.Cosmos.Tests/SampleCodeForDocs/ContainerDocsSampleCode.cs?name=ContainerCreateWithThroughput)]

---

### <a name="provision-database-level-throughput"></a>データベース レベルのスループットのプロビジョニング

# <a name="net-sdk-v2"></a>[.NET SDK V2](#tab/dotnetv2)

```csharp
//set the throughput for the database
RequestOptions options = new RequestOptions
{
    OfferThroughput = 500
};

//create the database
await client.CreateDatabaseIfNotExistsAsync(
    new Database {Id = databaseName},  
    options);
```

# <a name="net-sdk-v3"></a>[.NET SDK V3](#tab/dotnetv3)

[!code-csharp[](~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos/tests/Microsoft.Azure.Cosmos.Tests/SampleCodeForDocs/DatabaseDocsSampleCode.cs?name=DatabaseCreateWithThroughput)]

---

## <a name="azure-resource-manager"></a>Azure Resource Manager

Azure Resource Manager テンプレートを使用すると、すべての Azure Cosmos DB API に対して、データベースまたはコンテナー レベルのリソースへの自動スケーリングのスループットをプロビジョニングできます。 サンプルについては、「[Azure Cosmos DB の Azure Resource Manager テンプレート](resource-manager-template-samples.md)」を参照してください。

## <a name="azure-cli"></a>Azure CLI

Azure CLI を使用すると、すべての Azure Cosmos DB API に対して、データベースまたはコンテナー レベルのリソースへの自動スケーリングのスループットをプロビジョニングできます。 サンプルについては、「[Azure Cosmos DB の Azure CLI サンプル](cli-samples.md)」を参照してください。

## <a name="azure-powershell"></a>Azure PowerShell

Azure PowerShell を使用すると、すべての Azure Cosmos DB API に対して、データベースまたはコンテナー レベルのリソースへの自動スケーリングのスループットをプロビジョニングできます。 サンプルについては、「[Azure Cosmos DB 用 Azure PowerShell サンプル](powershell-samples.md)」を参照してください。

## <a name="next-steps"></a>次のステップ

Azure Cosmos DB でのスループットのプロビジョニングについては、次の記事を参照してください。

* [Azure Cosmos DB における要求ユニットとスループット](../request-units.md)