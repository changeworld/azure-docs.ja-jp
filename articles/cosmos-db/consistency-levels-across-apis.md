---
title: 整合性レベルと Azure Cosmos DB API
description: Azure Cosmos DB の API 間での整合性レベルについて理解します。
keywords: 整合性, azure cosmos db, azure, モデル, mongodb, cassandra, グラフ, テーブル, Microsoft azure
services: cosmos-db
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 10/23/2018
ms.openlocfilehash: 277a064d93e2ebcea82f3909b3fd16328a775105
ms.sourcegitcommit: 11d8ce8cd720a1ec6ca130e118489c6459e04114
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/04/2018
ms.locfileid: "52832498"
---
# <a name="consistency-levels-and-azure-cosmos-db-apis"></a>整合性レベルと Azure Cosmos DB API

Azure Cosmos DB によって提供される 5 つの整合性モデルは、Azure Cosmos DB SQL API でネイティブにサポートされています。 Azure Cosmos DB を使用する場合、既定は SQL API です。 

また、Azure Cosmos DB では、一般的なデータベースのワイヤ プロトコル対応 API に対するネイティブ サポートも提供しています。 データベースには MongoDB、Apache Cassandra、Gremlin、および Azure テーブル ストレージが含まれます。 これらのデータベースでは、厳密に定義された整合性モデルや SLA で裏付けられた整合性レベルの保証は提供されていません。 通常、これらのデータベースでは、Azure Cosmos DB によって提供される 5 つの整合性モデルのサブセットのみが提供されます。 SQL API、Gremlin API、および Table API の場合、Azure Cosmos DB アカウントで構成された既定の整合性レベルが使用されます。 

以下のセクションでは、Apache Cassandra 4.x および MongoDB 3.4 の OSS クライアント ドライバーによって要求されるデータ整合性間のマッピングを示します。 このドキュメントでは、Apache Cassandra と MongoDB の対応する Azure Cosmos DB の整合性レベルも示されます。

## <a id="cassandra-mapping"></a>Apache Cassandra と Azure Cosmos DB の間の整合性レベルのマッピング

次の表では、Apache Cassandra 4.x クライアントと、Azure Cosmos DB の既定の整合性レベルの間での、"読み取り整合性" のマッピングを示します。 この表は、マルチリージョンのデプロイと単一リージョンのデプロイを示しています。

| **Apache Cassandra 4.x** | **Azure Cosmos DB (マルチリージョン)** | **Azure Cosmos DB (単一リージョン)** |
| - | - | - |
| ONE、TWO、THREE | 一貫性のあるプレフィックス | 一貫性のあるプレフィックス |
| LOCAL_ONE | 一貫性のあるプレフィックス | 一貫性のあるプレフィックス |
| QUORUM、ALL、SERIAL | 既定値は有界整合性制約です。 Strong はプライベート プレビュー段階にあります。 | Strong |
| LOCAL_QUORUM | Bounded staleness | Strong |
| LOCAL_SERIAL | Bounded staleness | Strong |

## <a id="mongo-mapping"></a>MongoDB 3.4 と Azure Cosmos DB の間の整合性レベルのマッピング

次の表では、MongoDB 3.4 と、Azure Cosmos DB の既定の整合性レベルの間での、"読み取りの問題" のマッピングを示します。 この表は、マルチリージョンのデプロイと単一リージョンのデプロイを示しています。

| **MongoDB 3.4** | **Azure Cosmos DB (マルチリージョン)** | **Azure Cosmos DB (単一リージョン)** |
| - | - | - |
| 線形化可能 | Strong | Strong |
| マジョリティ | Bounded staleness | Strong |
| ローカル | 一貫性のあるプレフィックス | 一貫性のあるプレフィックス |

## <a name="next-steps"></a>次の手順

Azure Cosmos DB API とオープン ソースの API の間の整合性レベルおよび互換性についての詳細を参照してください。 次の記事を参照してください。

* [さまざまな整合性レベルでの可用性およびパフォーマンスのトレードオフ](consistency-levels-tradeoffs.md)
* [Azure Cosmos DB MongoDB API でサポートされている MongoDB の機能](mongodb-feature-support.md)
* [Azure Cosmos DB Cassandra API でサポートされる Apache Cassandra の機能](cassandra-support.md)