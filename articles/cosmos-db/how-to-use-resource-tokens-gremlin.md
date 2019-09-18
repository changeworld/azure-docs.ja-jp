---
title: Gremlin での Azure Cosmos DB リソース トークン
description: リソース トークンを作成し、それらを使用してグラフ データベースにアクセスする方法について説明します
author: olignat
ms.service: cosmos-db
ms.subservice: cosmosdb-graph
ms.topic: overview
ms.date: 09/06/2019
ms.author: olignat
ms.openlocfilehash: fcb18fb14cf787713735da07ca2048d0853fa46c
ms.sourcegitcommit: b8578b14c8629c4e4dea4c2e90164e42393e8064
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/09/2019
ms.locfileid: "70807013"
---
# <a name="azure-cosmos-db-resource-tokens-with-gremlin"></a>Gremlin での Azure Cosmos DB リソース トークン
この記事では、[Cosmos DB リソース トークン](secure-access-to-data.md)を使用して、Gremlin SDK 経由で Graph データベースにアクセスする方法について説明します。

## <a name="create-a-resource-token"></a>リソース グループを作成する

TinkerPop Gremlin SDK には、リソース トークンを作成するための API がありません。 リソース トークンは Cosmos DB の概念です。 リソース トークンを作成するには、[Azure Cosmos DB SDK](sql-api-sdk-dotnet.md) をダウンロードします。 ご利用のアプリケーションでリソース トークンを作成し、それらを使用して Graph データベースにアクセスする必要がある場合は、個別の SDK が 2 つ必要です。

リソース トークンの上にあるオブジェクト モデル階層:
- **Cosmos DB アカウント** - DNS が関連付けられている最上位のエンティティ (たとえば、`contoso.gremlin.cosmos.azure.com`)
  - **Cosmos DB データベース**
    - **User**
      - **アクセス許可**
        - *トークン* - 許可または拒否するアクションを示す**アクセス許可**オブジェクトのプロパティです。

リソース トークンの形式は `"type=resource&ver=1&sig=<base64 string>;<base64 string>;"` となります。 この文字列はクライアントに対して非透過的であり、変更または解釈を行わずにそのまま使用する必要があります。

```csharp
// Notice that document client is created against .NET SDK end-point rather than Gremlin.
DocumentClient client = new DocumentClient(
  new Uri("https://contoso.documents.azure.com:443/"), 
  "<master key>", 
  new ConnectionPolicy 
  {
    EnableEndpointDiscovery = false, 
    ConnectionMode = ConnectionMode.Direct 
  });

  // Read specific permission to obtain a token.
  // Token will not be returned during ReadPermissionReedAsync() call.
  // This call will succeed only if database id, user id and permission id already exist. 
  // Note that <database id> is not a database name, it is a base64 string that represents database identifier, for example "KalVAA==".
  // Similar comment applies to <user id> and <permission id>
  Permission permission = await client.ReadPermissionAsync(UriFactory.CreatePermissionUri("<database id>", "<user id>", "<permission id>"));

  Console.WriteLine("Obtained token {0}", permission.Token);
}
```

## <a name="use-a-resource-token"></a>リソース トークンを使用する
リソース トークンは、`GremlinServer` クラスを構築するときに、"password" プロパティとして直接使用できます。

```csharp
// Gremlin application needs to be given a resource token. It can't discover the token on its own.
// Token can be obtained for a given permission using Cosmos DB SDK or passed into the application as command line argument or configuration value.
string resourceToken = GetResourceToken();

// Configure gremlin servier to use resource token rather than master key
GremlinServer server = new GremlinServer(
  "contoso.gremlin.cosmosdb.azure.com",
  port: 443,
  enableSsl: true,
  username: "/dbs/<database name>/colls/<collection name>",

  // Format of the token is "type=resource&ver=1&sig=<base64 string>;<base64 string>;"
  password: resourceToken);

  using (GremlinClient gremlinClient = new GremlinClient(server, new GraphSON2Reader(), new GraphSON2Writer(), GremlinClient.GraphSON2MimeType))
  {
      await gremlinClient.SubmitAsync("g.V().limit(1)");
  }
```

同じアプローチを、すべての TinkerPop Gremlin SDK で使用できます。

```java
Cluster.Builder builder = Cluster.build();

AuthProperties authenticationProperties = new AuthProperties();
authenticationProperties.with(AuthProperties.Property.USERNAME,
    String.format("/dbs/%s/colls/%s", "<database name>", "<collection name>"));

// Format of the token is "type=resource&ver=1&sig=<base64 string>;<base64 string>;"
authenticationProperties.with(AuthProperties.Property.PASSWORD, resourceToken);

builder.authProperties(authenticationProperties);
```

## <a name="limit"></a>制限

単一の Gremlin アカウントで発行できるトークンの数に制限はありませんが、**1 時間**以内に同時に使用できるトークンは **100** 個までです。 アプリケーションが 1 時間あたりのトークン制限を超えた場合、認証要求は拒否され、エラー メッセージ `"Exceeded allowed resource token limit of 100 that can be used concurrently"` が返されます。 新しいトークン用にスロットを解放するために、特定のトークンとのアクティブな接続を閉じることは効果的ではありません。 Cosmos DB Gremlin エンジンでは、認証要求の前に、過去 1 時間の個別のトークンが追跡されます。

## <a name="permission"></a>アクセス許可

リソース トークンの使用中に発生する一般的なエラー アプリケーションは `"Insufficient permissions provided in the authorization header for the corresponding request. Please retry with another authorization header."` です。 Gremlin トラバーサルでエッジまたは頂点の書き込みが試みられたが、リソース トークンからは `Read` アクセス許可しか付与されていない場合、このエラーが返されます。 次のステップのいずれかが含まれているかどうか、ご利用のトラバーサルを調べてください: `.addV()`、`.addE()`、`.drop()`、または `.property()`。

## <a name="next-steps"></a>次の手順
* Azure Cosmos DB の[ロールベースのアクセス制御](role-based-access-control.md)
* Azure Cosmos DB の[データへのアクセスをセキュリティで保護する方法](secure-access-to-data.md)
