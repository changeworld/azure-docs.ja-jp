---
title: Azure Cosmos DB で一貫性を管理する方法について
description: Azure Cosmos DB で一貫性を管理する方法について
services: cosmos-db
author: christopheranderson
ms.service: cosmos-db
ms.topic: sample
ms.date: 10/17/2018
ms.author: chrande
ms.openlocfilehash: be2c68922221af848c9e484d03527d02808c071a
ms.sourcegitcommit: ba4570d778187a975645a45920d1d631139ac36e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/08/2018
ms.locfileid: "51283820"
---
# <a name="manage-consistency-levels-in-azure-cosmos-db"></a>Azure Cosmos DB で一貫性レベルを管理する

この記事では、既定の一貫性の設定、クライアントでの一貫性の上書き、セッション トークンの手動管理、確率論的有界整合性制約 (PBS) メトリックの確認を行う、さまざまな方法について説明します。

## <a name="configure-the-default-consistency-level"></a>既定の一貫性レベルを構成する

既定の一貫性レベルは、クライアントによって既定で使用される一貫性レベルです。 これは、クライアントによる上書きが可能です。

### <a name="cli"></a>CLI

```bash
# create with a default consistency
az cosmosdb create --name <name of Cosmos DB Account> --resource-group <resource group name> --default-consistency-level Strong

# update an existing account's default consistency
az cosmosdb update --name <name of Cosmos DB Account> --resource-group <resource group name> --default-consistency-level BoundedStaleness
```

### <a name="powershell"></a>PowerShell

下の例では、米国東部リージョンと米国西部リージョンでマルチマスターが有効な新しい Cosmos DB アカウントを作成します。既定の一貫性ポリシーは、最大整合性制約間隔が 10 秒、許容される古い要求の最大数が 200 で、有界整合性制約に設定されています。

```azurepowershell-interactive
$locations = @(@{"locationName"="East US"; "failoverPriority"=0},
             @{"locationName"="West US"; "failoverPriority"=1})

$iprangefilter = ""

$consistencyPolicy = @{"defaultConsistencyLevel"="BoundedStaleness";
                       "maxIntervalInSeconds"= "10";
                       "maxStalenessPrefix"="200"}

$CosmosDBProperties = @{"databaseAccountOfferType"="Standard";
                        "locations"=$locations;
                        "consistencyPolicy"=$consistencyPolicy;
                        "ipRangeFilter"=$iprangefilter;
                        "enableMultipleWriteLocations"="true"}

New-AzureRmResource -ResourceType "Microsoft.DocumentDb/databaseAccounts" `
  -ApiVersion "2015-04-08" `
  -ResourceGroupName "myResourceGroup" `
  -Location "East US" `
  -Name "myCosmosDbAccount" `
  -Properties $CosmosDBProperties
```

### <a name="portal"></a>ポータル

既定の一貫性レベルを表示または変更するには、Azure portal にサインインします。 Cosmos DB アカウントを見つけて、**[既定の整合性]** ウィンドウを開きます。 そこで、新しい既定値にする一貫性のレベルを選択して、[保存] をクリックします。

![Azure portal の一貫性メニューの画像](./media/how-to-manage-consistency/consistency-settings.png)

## <a name="override-the-default-consistency-level"></a>既定の一貫性レベルを上書きする

クライアントは、サービスによって設定された既定の一貫性レベルを上書きできます。 これはクライアント全体で実行できるほか、要求ごとに実行できます。

### <a id="override-default-consistency-dotnet"></a>.NET SDK

```csharp
// Override consistency at the client level
ConsistencyPolicy consistencyPolicy = new ConsistencyPolicy
    {
        DefaultConsistencyLevel = ConsistencyLevel.BoundedStaleness,
        MaxStalenessIntervalInSeconds = 5,
        MaxStalenessPrefix = 100
    };
documentClient = new DocumentClient(new Uri(endpoint), authKey, connectionPolicy, consistencyPolicy);

// Override consistency at the request level via request options
RequestOptions requestOptions = new RequestOptions { ConsistencyLevel = ConsistencyLevel.Strong };

var response = await client.CreateDocumentAsync(collectionUri, document, requestOptions);
```

### <a id="override-default-consistency-java-async"></a>Java Async SDK

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

### <a id="override-default-consistency-java-sync"></a>Java Sync SDK

```java
// Override consistency at the client level
ConnectionPolicy connectionPolicy = new ConnectionPolicy();
DocumentClient client = new DocumentClient(accountEndpoint, accountKey, connectionPolicy, ConsistencyLevel.Strong);
```

### <a id="override-default-consistency-javascript"></a>Node.js SDK、JavaScript SDK、TypeScript SDK

```javascript
// Override consistency at the client level
const client = new CosmosClient({
  /* other config... */
  consistencyLevel: ConsistencyLevel.Strong
});

// Override consistency at the request level via request options
const { body } = await item.read({ consistencyLevel: ConsistencyLevel.Eventual });
```

### <a id="override-default-consistency-python"></a>Python SDK

```python
# Override consistency at the client level
connection_policy = documents.ConnectionPolicy()
client = cosmos_client.CosmosClient(self.account_endpoint, {'masterKey': self.account_key}, connection_policy, documents.ConsistencyLevel.Strong)
```

## <a name="utilize-session-tokens"></a>セッション トークンを利用する

セッション トークンを手動で管理したい場合は、応答からそれらを取得して要求ごとに設定できます。 セッション トークンを手動で管理する必要がない場合、下のサンプルを使用する必要はありません。 SDK によってセッション トークンが自動的に追跡され、お客様が自分でセッション トークンを設定していない場合に最も最近のセッション トークンが使用されます。

### <a id="utilize-session-tokens-dotnet"></a>.NET SDK

```csharp
var response = await client.ReadDocumentAsync(
                UriFactory.CreateDocumentUri(databaseName, collectionName, "SalesOrder1"));
string sessionToken = response.SessionToken;

RequestOptions options = new RequestOptions();
options.SessionToken = sessionToken;
var response = await client.ReadDocumentAsync(
                UriFactory.CreateDocumentUri(databaseName, collectionName, "SalesOrder1"), options);
```

### <a id="utilize-session-tokens-java-async"></a>Java Async SDK

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

### <a id="utilize-session-tokens-java-sync"></a>Java Sync SDK

```java
// Get session token from response
ResourceResponse<Document> response = client.readDocument(documentLink, null);
String sessionToken = response.getSessionToken();

// Resume the session by setting the session token on the RequestOptions
RequestOptions options = new RequestOptions();
options.setSessionToken(sessionToken);
ResourceResponse<Document> response = client.readDocument(documentLink, options);
```

### <a id="utilize-session-tokens-javascript"></a>Node.js SDK、JavaScript SDK、TypeScript SDK

```javascript
// Get session token from response
const { headers, item } = await container.items.create({ id: "meaningful-id" });
const sessionToken = headers["x-ms-session-token"];

// Immediately or later, you can use that sessionToken from the header to resume that session.
const { body } = await item.read({ sessionToken });
```

### <a id="utilize-session-tokens-python"></a>Python SDK

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

PBS メトリックを表示するには、Azure portal で Cosmos DB アカウントに移動して、**[メトリック]** ウィンドウを開きます。 そこで、**[整合性]** タブをクリックして、"**Probability of strongly consistent reads based on your workload (see PBS) (ワークロードに基づいた、強固な一貫性がある読み取りの確率 (PBS を参照))**" という名前のグラフを確認します。

![Azure portal の PBS グラフの画像](./media/how-to-manage-consistency/pbs-metric.png)

このメトリックを確認するには、Cosmos DB メトリックのメニューを使用します。 これは Azure 監視メトリック エクスペリエンスに表示されません。

## <a name="next-steps"></a>次の手順

次のドキュメントを使用して、データ競合の管理について詳しく学習するか、Cosmos DB の次の重要な概念に進むことができます。

* [リージョン間での競合を管理する方法](how-to-manage-conflicts.md)
* [パーティション分割とデータ分散](partition-data.md)
