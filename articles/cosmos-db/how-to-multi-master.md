---
title: Azure Cosmos DB でマルチマスターを構成する方法
description: Azure Cosmos DB 内のアプリケーションでマルチマスターを構成する方法について説明します。
author: rimman
ms.service: cosmos-db
ms.topic: sample
ms.date: 05/23/2019
ms.author: rimman
ms.openlocfilehash: 1d9fa7380f62165d360888fd8cb03919f1736297
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/27/2019
ms.locfileid: "66244758"
---
# <a name="configure-multi-master-in-your-applications-that-use-azure-cosmos-db"></a>Azure Cosmos DB を使用するアプリケーションでマルチマスターを構成する

アプリケーションでマルチマスター機能を使用するには、Azure Cosmos DB でマルチリージョンの書き込みを有効にし、マルチホーム機能を構成する必要があります。 マルチホーム機能を構成するには、アプリケーションがデプロイされているリージョンを設定します。

## <a id="netv2"></a>.NET SDK v2

アプリケーションでマルチマスターを有効にするには、`UseMultipleWriteLocations` を `true` に設定します。 また、`SetCurrentLocation` は、アプリケーションがデプロイされ、Azure Cosmos DB がレプリケートされているリージョンに設定します。

```csharp
ConnectionPolicy policy = new ConnectionPolicy
    {
        ConnectionMode = ConnectionMode.Direct,
        ConnectionProtocol = Protocol.Tcp,
        UseMultipleWriteLocations = true
    };
policy.SetCurrentLocation("West US 2");
```

## <a id="netv3"></a>.NET SDK v3 (プレビュー)

アプリケーションでマルチマスターを有効にするには、`UseCurrentRegion` を、アプリケーションがデプロイされ、Cosmos DB がレプリケートされているリージョンに設定します。

```csharp
CosmosConfiguration config = new CosmosConfiguration("endpoint", "key");
config.UseCurrentRegion("West US");
CosmosClient client = new CosmosClient(config);
```

## <a id="java"></a>Java Async SDK

アプリケーションでマルチマスターを有効にするには、`policy.setUsingMultipleWriteLocations(true)` を設定し、`policy.setPreferredLocations` は、アプリケーションがデプロイされ、Cosmos DB がレプリケートされているリージョンに設定します。

```java
ConnectionPolicy policy = new ConnectionPolicy();
policy.setUsingMultipleWriteLocations(true);
policy.setPreferredLocations(Collections.singletonList(region));

AsyncDocumentClient client =
    new AsyncDocumentClient.Builder()
        .withMasterKeyOrResourceToken(this.accountKey)
        .withServiceEndpoint(this.accountEndpoint)
        .withConsistencyLevel(ConsistencyLevel.Eventual)
        .withConnectionPolicy(policy).build();
```

## <a id="javascript"></a>Node.js、JavaScript、および TypeScript SDK

アプリケーションでマルチマスターを有効にするには、`connectionPolicy.UseMultipleWriteLocations` を `true` に設定します。 また、`connectionPolicy.PreferredLocations` は、アプリケーションがデプロイされ、Cosmos DB がレプリケートされているリージョンに設定します。

```javascript
const connectionPolicy: ConnectionPolicy = new ConnectionPolicy();
connectionPolicy.UseMultipleWriteLocations = true;
connectionPolicy.PreferredLocations = [region];

const client = new CosmosClient({
  endpoint: config.endpoint,
  auth: { masterKey: config.key },
  connectionPolicy,
  consistencyLevel: ConsistencyLevel.Eventual
});
```

## <a id="python"></a>Python SDK

アプリケーションでマルチマスターを有効にするには、`connection_policy.UseMultipleWriteLocations` を `true` に設定します。 また、`connection_policy.PreferredLocations` は、アプリケーションがデプロイされ、Cosmos DB がレプリケートされているリージョンに設定します。

```python
connection_policy = documents.ConnectionPolicy()
connection_policy.UseMultipleWriteLocations = True
connection_policy.PreferredLocations = [region]

client = cosmos_client.CosmosClient(self.account_endpoint, {'masterKey': self.account_key}, connection_policy, documents.ConsistencyLevel.Session)
```

## <a name="next-steps"></a>次の手順

次の記事を参照してください。

* [セッション トークンを使用して Azure Cosmos DB で一貫性を管理する](how-to-manage-consistency.md#utilize-session-tokens)
* [Azure Cosmos DB の競合の種類と解決ポリシー](conflict-resolution-policies.md)
* [Azure Cosmos DB での高可用性](high-availability.md)
* [Azure Cosmos DB の整合性レベル](consistency-levels.md)
* [Azure Cosmos DB で適切な一貫性レベルを選択する](consistency-levels-choosing.md)
* [Azure Cosmos DB の一貫性、可用性、パフォーマンスのトレードオフ](consistency-levels-tradeoffs.md)
* [さまざまな整合性レベルでの可用性およびパフォーマンスのトレードオフ](consistency-levels-tradeoffs.md)
* [プロビジョニング スループットのグローバルなスケーリング](scaling-throughput.md)
* [グローバル配信: しくみ](global-dist-under-the-hood.md)
