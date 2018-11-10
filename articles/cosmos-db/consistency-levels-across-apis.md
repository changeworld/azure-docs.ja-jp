---
title: 整合性レベルおよび Azure Cosmos DB の API | Microsoft Docs
description: Azure Cosmos DB の API 間での整合性レベルについて理解します。
keywords: 整合性, azure cosmos db, azure, モデル, mongodb, cassandra, グラフ, テーブル, Microsoft azure
services: cosmos-db
author: markjbrown
ms.service: cosmos-db
ms.devlang: na
ms.topic: conceptual
ms.date: 10/23/2018
ms.author: mjbrown
ms.openlocfilehash: ed08b90b9e216ee8713bfe445e98144bf2ba02d4
ms.sourcegitcommit: dbfd977100b22699823ad8bf03e0b75e9796615f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/30/2018
ms.locfileid: "50243997"
---
# <a name="consistency-levels-and-cosmos-db-apis"></a>整合性レベルと Cosmos DB API

5 つの整合性モデルは、Cosmos DB を使用する場合の既定の API である SQL API でネイティブにサポートされています。 Cosmos DB は、SQL API だけでなく、MongoDB、Apache Cassandra、Gremlin、Azure Tables などの一般的なデータベースのワイヤ プロトコル対応 API に対するネイティブ サポートを提供します。 これらのデータベースには、正確に定義された整合性モデルも SLA でサポートされた整合性レベルの保証も提供されず、通常は Cosmos DB が提供する 5 つの整合性モデルのサブセットのみが提供されます。 SQL API、Gremlin API、および Table API の場合、Cosmos アカウントで構成された既定の整合性レベルが使用されます。

次の表は、Cassandra API および MongoDB API を使用する場合の Apache Cassandra 4.x および MongoDB 3.4 の OSS クライアント ドライバーによって要求されるそれぞれのデータ整合性と、対応する Cosmos DB の整合性レベルのマッピングを示しています。

## <a id="cassandra-mapping"></a>Apache Cassandra と Cosmos DB の整合性レベルのマッピング

次の表は、マルチリー ジョンおよび単一リージョンの両方のデプロイにおいて、Apache Cassandra 4.x クライアントと Cosmos DB の「既定の」整合性レベルの間の読み取り整合性のマッピングを示します。

| **Apache Cassandra 4.x** | **Cosmos DB (マルチリージョン)** | **Cosmos DB (単一リージョン)** |
| - | - | - |
| ONE、TWO、THREE | 一貫性のあるプレフィックス | 一貫性のあるプレフィックス |
| LOCAL_ONE | 一貫性のあるプレフィックス | 一貫性のあるプレフィックス |
| QUORUM、ALL、SERIAL | Bounded Staleness (既定) または Strong (プライベート プレビュー) | Strong |
| LOCAL_QUORUM | Bounded Staleness | Strong |
| LOCAL_SERIAL | Bounded Staleness | Strong |

## <a id="mongo-mapping"></a>MongoDB 3.4 と Cosmos DB の整合性レベルのマッピング

次の表は、マルチリー ジョンおよび単一リージョンの両方のデプロイにおいて、MongoDB 3.4 と Cosmos DB の「既定の」整合性レベルの「読み取りの問題」についてのマッピングを示します。

| **MongoDB 3.4** | **Cosmos DB (マルチリージョン)** | **Cosmos DB (単一リージョン)** |
| - | - | - |
| 線形化可能 | Strong | Strong |
| マジョリティ | Bounded Staleness | Strong |
| ローカル | 一貫性のあるプレフィックス | 一貫性のあるプレフィックス |

## <a name="next-steps"></a>次の手順

次の記事で、Cosmos DB API とオープン ソースの API の間の整合性レベルおよび互換性についての詳細を参照してください。

* [さまざまな整合性レベルでの可用性およびパフォーマンスのトレードオフ](consistency-levels-tradeoffs.md)
* [Cosmos DB MongoDB API でサポートされている MongoDB の機能](mongodb-feature-support.md)
* [Cosmos DB の Cassandra API でサポートされる Apache Cassandra の機能](cassandra-support.md)