---
title: Azure Cosmos DB Graph を使用したシステム ドキュメントのプロパティへのアクセス
description: Gremlin API を使用して Cosmos DB システム ドキュメントのプロパティの読み取りと書き込みを行う方法について学習します
ms.service: cosmos-db
ms.subservice: cosmosdb-graph
ms.topic: how-to
ms.date: 09/16/2021
author: manishmsfte
ms.author: mansha
ms.openlocfilehash: 8a0574d5622ae0ceceb52be72ccd8c5d99a18529
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/24/2021
ms.locfileid: "128614809"
---
# <a name="system-document-properties"></a>システム ドキュメントのプロパティ
[!INCLUDE[appliesto-gremlin-api](../includes/appliesto-gremlin-api.md)]

Azure Cosmos DB では、すべてのドキュメントに[システム プロパティ](/rest/api/cosmos-db/databases) (```_ts```、```_self```、```_attachments```、```_rid```、```_etag``` など) があります。 さらに、Gremlin エンジンによって、辺の ```inVPartition``` および ```outVPartition``` プロパティが追加されます。 既定では、これらのプロパティはトラバーサルに使用できます。 ただし、特定のプロパティ、またはすべてのプロパティを Gremlin トラバーサルに含めることは可能です。

```console
g.withStrategies(ProjectionStrategy.build().IncludeSystemProperties('_ts').create())
```

## <a name="e-tag"></a>E-Tag

このプロパティは、オプティミスティック コンカレンシー制御に使用されます。 アプリケーションで操作をいくつかの別個のトラバーサルに分割する必要がある場合、eTag プロパティを使用して、同時書き込み時のデータ損失を回避できます。

```console
g.withStrategies(ProjectionStrategy.build().IncludeSystemProperties('_etag').create()).V('1').has('_etag', '"00000100-0000-0800-0000-5d03edac0000"').property('test', '1')
```

## <a name="time-to-live-ttl"></a>Time-to-live (TTL)

コレクションでドキュメントの有効期限が有効になっており、ドキュメントに `ttl` プロパティが設定されている場合、このプロパティは通常の頂点または辺プロパティとして Gremlin トラバーサルで使用できるようになります。 Time-to-live プロパティの公開を有効にするために `ProjectionStrategy` は必要ありません。

* 新しい頂点に有効期限を設定するには、次のコマンドを使用します。

  ```console
  g.addV(<ID>).property('ttl', <expirationTime>)
  ```

  たとえば、次のトラバーサルで作成された頂点は、*123 秒* 後に自動的に削除されます。

  ```console
  g.addV('vertex-one').property('ttl', 123)
  ```

* 既存の頂点に有効期限を設定するには、次のコマンドを使用します。

  ```console
  g.V().hasId(<ID>).has('pk', <pk>).property('ttl', <expirationTime>)
  ```

* 頂点に Time-to-live プロパティを適用しても、自動的にエッジに適用されるわけではありません。 エッジはデータベース ストア内の独立したレコードであるためです。 次のコマンドを使用して、頂点の有効期限と、頂点のすべての内向きと外向きのエッジを設定します。

  ```console
  g.V().hasId(<ID>).has('pk', <pk>).as('v').bothE().hasNot('ttl').property('ttl', <expirationTime>)
  ```

コンテナーの TTL を-1 に設定することも、Azure portal から **[オン (既定値なし)]** に設定することもできます。ただし、項目に TTL 値が明示的に設定されていない限り、TTL はどの項目でも無期限になります。

## <a name="next-steps"></a>次のステップ
* [Cosmos DB のオプティミスティック コンカレンシー制御](../faq.yml#how-does-the-sql-api-provide-concurrency-)
* Azure Cosmos DB の [Time to Live (TTL)](../time-to-live.md)
