---
title: Azure Cosmos DB で一貫性を管理する
description: Azure portal、.NET SDK、Java SDK など各種の SDK を使用して、Azure Cosmos DB に整合性レベルを構成、管理する方法について説明します
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 04/24/2020
ms.author: mjbrown
ms.openlocfilehash: 28266471fb1e440a45e412ee889e0706cfc2ce49
ms.sourcegitcommit: f57297af0ea729ab76081c98da2243d6b1f6fa63
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/06/2020
ms.locfileid: "82870095"
---
# <a name="manage-consistency-levels-in-azure-cosmos-db"></a>Azure Cosmos DB で一貫性レベルを管理する

この記事では、Azure Cosmos DB で一貫性レベルを管理する方法について説明します。 読者は、既定の一貫性レベルを構成する方法、既定の一貫性をオーバーライドする方法、セッション トークンを手動を管理する方法、および確率論的有界整合性制約 (PBS) メトリックを確認する方法について学習します。

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="configure-the-default-consistency-level"></a>既定の整合性レベルを構成する

[既定の整合性レベル](consistency-levels.md)は、クライアントによって既定で使用される整合性レベルです。

# <a name="azure-portal"></a>[Azure Portal](#tab/portal)

既定の一貫性レベルを表示または変更するには、Azure portal にサインインします。 Azure Cosmos アカウントを見つけて、 **[既定の整合性]** ウィンドウを開きます。 目的の一貫性レベルを新しい既定として選択し、 **[保存]** を選択します。 Azure portal では、音符によるさまざまな一貫性レベルの視覚化も提供しています。 

![Azure portal の一貫性メニュー](./media/how-to-manage-consistency/consistency-settings.png)

# <a name="cli"></a>[CLI](#tab/cli)

セッションの整合性を持つ Cosmos アカウントを作成し、既定の整合性を更新します。

```azurecli
# Create a new account with Session consistency
az cosmosdb create --name $accountName --resource-group $resourceGroupName --default-consistency-level Session

# update an existing account's default consistency
az cosmosdb update --name $accountName --resource-group $resourceGroupName --default-consistency-level Strong
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

セッションの整合性を持つ Cosmos アカウントを作成し、既定の整合性を更新します。

```azurepowershell-interactive
# Create a new account with Session consistency
New-AzCosmosDBAccount -ResourceGroupName $resourceGroupName `
  -Location $locations -Name $accountName -DefaultConsistencyLevel "Session"

# Update an existing account's default consistency
Update-AzCosmosDBAccount -ResourceGroupName $resourceGroupName `
  -Name $accountName -DefaultConsistencyLevel "Strong"
```

---

## <a name="override-the-default-consistency-level"></a>既定の一貫性レベルを上書きする

クライアントは、サービスによって設定された既定の一貫性レベルを上書きできます。 整合性レベルは要求ごとに設定できます。この設定によって、アカウント レベルで設定される既定の整合性レベルがオーバーライドされます。

> [!TIP]
> 整合性は、要求レベルでのみ **緩和**されます。 弱い整合性からより強い整合性に移行するには、Cosmos アカウントの既定の整合性を更新します。

### <a name="net-sdk"></a><a id="override-default-consistency-dotnet"></a>.NET SDK

# <a name="net-sdk-v2"></a>[.NET SDK V2](#tab/dotnetv2)

```csharp
// Override consistency at the client level
documentClient = new DocumentClient(new Uri(endpoint), authKey, connectionPolicy, ConsistencyLevel.Eventual);

// Override consistency at the request level via request options
RequestOptions requestOptions = new RequestOptions { ConsistencyLevel = ConsistencyLevel.Eventual };

var response = await client.CreateDocumentAsync(collectionUri, document, requestOptions);
```

# <a name="net-sdk-v3"></a>[.NET SDK V3](#tab/dotnetv3)

```csharp
// Override consistency at the request level via request options
ItemRequestOptions requestOptions = new ItemRequestOptions { ConsistencyLevel = ConsistencyLevel.Strong };

var response = await client.GetContainer(databaseName, containerName)
    .CreateItemAsync(
        item,
        new PartitionKey(itemPartitionKey),
        requestOptions);
```
---

### <a name="java-sdk"></a><a id="override-default-consistency-java"></a>Java SDK

# <a name="java-async-sdk"></a>[Java Async SDK](#tab/javaasync)

```java
// Override consistency at the client level
ConnectionPolicy policy = new ConnectionPolicy();

AsyncDocumentClient client =
        new AsyncDocumentClient.Builder()
                .withMasterKey(this.accountKey)
                .withServiceEndpoint(this.accountEndpoint)
                .withConsistencyLevel(ConsistencyLevel.Eventual)
                .withConnectionPolicy(policy).build();
```

# <a name="java-sync-sdk"></a>[Java Sync SDK](#tab/javasync)

```java
// Override consistency at the client level
ConnectionPolicy connectionPolicy = new ConnectionPolicy();
DocumentClient client = new DocumentClient(accountEndpoint, accountKey, connectionPolicy, ConsistencyLevel.Eventual);
```
---

### <a name="nodejsjavascripttypescript-sdk"></a><a id="override-default-consistency-javascript"></a>Node.js/JavaScript/TypeScript SDK

```javascript
// Override consistency at the client level
const client = new CosmosClient({
  /* other config... */
  consistencyLevel: ConsistencyLevel.Eventual
});

// Override consistency at the request level via request options
const { body } = await item.read({ consistencyLevel: ConsistencyLevel.Eventual });
```

### <a name="python-sdk"></a><a id="override-default-consistency-python"></a>Python SDK

```python
# Override consistency at the client level
connection_policy = documents.ConnectionPolicy()
client = cosmos_client.CosmosClient(self.account_endpoint, {
                                    'masterKey': self.account_key}, connection_policy, documents.ConsistencyLevel.Eventual)
```

## <a name="utilize-session-tokens"></a>セッション トークンを利用する

Azure Cosmos DB の整合性レベルの 1 つとして、"*セッション*" の整合性があります。 これは、Cosmos アカウントに既定で適用されるレベルです。 "*セッション*" の整合性で作業している場合、クライアントでは設定した整合性レベルが維持されるように、各読み取り/クエリ要求で内部的にセッション トークンが使用されます。

セッション トークンを手動で管理するには、応答からセッション トークンを取得し、それらを要求ごとに設定します。 セッション トークンを手動で管理する必要がない場合は、これらのサンプルを使用する必要はありません。 SDK では、セッション トークンが自動的に追跡されます。 セッション トークンを手動で設定しなかった場合、既定では、SDK によって直近のセッション トークンが使用されます。

### <a name="net-sdk"></a><a id="utilize-session-tokens-dotnet"></a>.NET SDK

# <a name="net-sdk-v2"></a>[.NET SDK V2](#tab/dotnetv2)

```csharp
var response = await client.ReadDocumentAsync(
                UriFactory.CreateDocumentUri(databaseName, collectionName, "SalesOrder1"));
string sessionToken = response.SessionToken;

RequestOptions options = new RequestOptions();
options.SessionToken = sessionToken;
var response = await client.ReadDocumentAsync(
                UriFactory.CreateDocumentUri(databaseName, collectionName, "SalesOrder1"), options);
```

# <a name="net-sdk-v3"></a>[.NET SDK V3](#tab/dotnetv3)

```csharp
Container container = client.GetContainer(databaseName, collectionName);
ItemResponse<SalesOrder> response = await container.CreateItemAsync<SalesOrder>(salesOrder);
string sessionToken = response.Headers.Session;

ItemRequestOptions options = new ItemRequestOptions();
options.SessionToken = sessionToken;
ItemResponse<SalesOrder> response = await container.ReadItemAsync<SalesOrder>(salesOrder.Id, new PartitionKey(salesOrder.PartitionKey), options);
```
---

### <a name="java-sdk"></a><a id="utilize-session-tokens-java"></a>Java SDK

# <a name="java-async-sdk"></a>[Java Async SDK](#tab/javaasync)

```java
// Get session token from response
RequestOptions options = new RequestOptions();
options.setPartitionKey(new PartitionKey(document.get("mypk")));
Observable<ResourceResponse<Document>> readObservable = client.readDocument(document.getSelfLink(), options);
readObservable.single()           // we know there will be one response
  .subscribe(
      documentResourceResponse -> {
          System.out.println(documentResourceResponse.getSessionToken());
      },
      error -> {
          System.err.println("an error happened: " + error.getMessage());
      });

// Resume the session by setting the session token on RequestOptions
RequestOptions options = new RequestOptions();
requestOptions.setSessionToken(sessionToken);
Observable<ResourceResponse<Document>> readObservable = client.readDocument(document.getSelfLink(), options);
```

# <a name="java-sync-sdk"></a>[Java Sync SDK](#tab/javasync)

```java
// Get session token from response
ResourceResponse<Document> response = client.readDocument(documentLink, null);
String sessionToken = response.getSessionToken();

// Resume the session by setting the session token on the RequestOptions
RequestOptions options = new RequestOptions();
options.setSessionToken(sessionToken);
ResourceResponse<Document> response = client.readDocument(documentLink, options);
```
---

### <a name="nodejsjavascripttypescript-sdk"></a><a id="utilize-session-tokens-javascript"></a>Node.js/JavaScript/TypeScript SDK

```javascript
// Get session token from response
const { headers, item } = await container.items.create({ id: "meaningful-id" });
const sessionToken = headers["x-ms-session-token"];

// Immediately or later, you can use that sessionToken from the header to resume that session.
const { body } = await item.read({ sessionToken });
```

### <a name="python-sdk"></a><a id="utilize-session-tokens-python"></a>Python SDK

```python
// Get the session token from the last response headers
item = client.ReadItem(item_link)
session_token = client.last_response_headers["x-ms-session-token"]

// Resume the session by setting the session token on the options for the request
options = {
    "sessionToken": session_token
}
item = client.ReadItem(doc_link, options)
```

## <a name="monitor-probabilistically-bounded-staleness-pbs-metric"></a>確率的有界整合性制約 (PBS) メトリックを監視する

最終的な整合性は、どのくらい最終的でしょうか。 平均的なケースでは、バージョン履歴と時刻に関して、古さの限度を提示できます。 [**確率的有界整合性制約 (PBS)** ](https://pbs.cs.berkeley.edu/) メトリックは、古さの可能性を定量化し、それをメトリックとして表示します。 PBS メトリックを表示するには、Azure portal で Azure Cosmos アカウントに移動します。 **[メトリック]** ウィンドウを開き、 **[整合性]** タブを選択します。**Probability of strongly consistent reads based on your workload (see PBS) (ワークロードに基づいた、強固な一貫性がある読み取りの確率 (PBS を参照))** という名前のグラフを確認します。

![Azure portal の PBS グラフ](./media/how-to-manage-consistency/pbs-metric.png)

## <a name="next-steps"></a>次のステップ

データの競合を管理する方法について学習するか、Azure Cosmos DB に関する次の主要概念に進んでください。 次の記事をご覧ください。

* [Azure Cosmos DB の整合性レベル](consistency-levels.md)
* [リージョン間の競合の管理](how-to-manage-conflicts.md)
* [パーティション分割とデータ分散](partition-data.md)
* [最新の分散データベース システム設計における整合性のトレードオフ](https://www.computer.org/csdl/magazine/co/2012/02/mco2012020037/13rRUxjyX7k)
* [高可用性](high-availability.md)
* [Azure Cosmos DB SLA](https://azure.microsoft.com/support/legal/sla/cosmos-db/v1_2/)
