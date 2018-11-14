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
ms.openlocfilehash: 974531cd5907e4f69e7d064125d3e51fa4974949
ms.sourcegitcommit: ada7419db9d03de550fbadf2f2bb2670c95cdb21
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/02/2018
ms.locfileid: "50956385"
---
# <a name="consistency-levels-and-azure-cosmos-db-apis"></a>整合性レベルと Azure Cosmos DB API

Azure Cosmos DB によって提供される 5 つの整合性モデルは、Cosmos DB を使用する場合の既定の API である Cosmos DB SQL API でネイティブにサポートされています。 Cosmos DB は、SQL API だけでなく、MongoDB、Apache Cassandra、Gremlin、Azure Tables などの一般的なデータベースのワイヤ プロトコル対応 API に対するネイティブ サポートを提供します。 これらのデータベースでは、厳密に定義された整合性モデルも、SLA で裏付けられた整合性レベルの保証も、提供されていません。 通常、これらのデータベースでは、Cosmos DB によって提供される 5 つの整合性モデルのサブセットのみが提供されます。 SQL API、Gremlin API、および Table API の場合、ユーザーが Cosmos アカウントで構成する既定の整合性レベルが使用されます。

以下のセクションでは、Cassandra API および MongoDB API を使用する場合の Apache Cassandra 4.x および MongoDB 3.4 の OSS クライアント ドライバーによって要求されるそれぞれのデータ整合性と、対応する Cosmos DB の整合性レベルのマッピングを示します。

## <a id="cassandra-mapping"></a>Apache Cassandra と Cosmos DB の間の整合性レベルのマッピング

次の表では、マルチリージョンのデプロイと単一リージョンのデプロイの両方について、Apache Cassandra 4.x クライアントと、Cosmos DB の既定の整合性レベルの間での、"読み取り整合性" のマッピングを示します。

| **Apache Cassandra 4.x** | **Cosmos DB (マルチリージョン)** | **Cosmos DB (単一リージョン)** |
| - | - | - |
| ONE、TWO、THREE | 一貫性のあるプレフィックス | 一貫性のあるプレフィックス |
| LOCAL_ONE | 一貫性のあるプレフィックス | 一貫性のあるプレフィックス |
| QUORUM、ALL、SERIAL | Bounded Staleness (既定) または Strong (プライベート プレビュー) | Strong |
| LOCAL_QUORUM | Bounded Staleness | Strong |
| LOCAL_SERIAL | Bounded Staleness | Strong |

## <a id="mongo-mapping"></a>MongoDB 3.4 と Cosmos DB の整合性レベルのマッピング

次の表では、マルチリージョンのデプロイと単一リージョンのデプロイの両方について、MongoDB 3.4 と、Cosmos DB の既定の整合性レベルの間での、"読み取りの問題" のマッピングを示します。

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