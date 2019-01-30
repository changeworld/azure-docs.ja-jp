---
title: 整合性レベルと Azure Cosmos DB API
description: Azure Cosmos DB の API 間での整合性レベルについて理解します。
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 10/23/2018
ms.reviewer: sngun
ms.openlocfilehash: 4d2994ea6ab6d6472ec56f0f2e378062590c8920
ms.sourcegitcommit: 98645e63f657ffa2cc42f52fea911b1cdcd56453
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/23/2019
ms.locfileid: "54806999"
---
# <a name="consistency-levels-and-azure-cosmos-db-apis"></a>整合性レベルと Azure Cosmos DB API

Azure Cosmos DB によって提供される 5 つの整合性モデルは、Azure Cosmos DB SQL API でネイティブにサポートされています。 Azure Cosmos DB を使用する場合、既定は SQL API です。 

また、Azure Cosmos DB では、一般的なデータベースのワイヤ プロトコル対応 API に対するネイティブ サポートも提供しています。 データベースには MongoDB、Apache Cassandra、Gremlin、および Azure テーブル ストレージが含まれます。 これらのデータベースでは、厳密に定義された整合性モデルや SLA で裏付けられた整合性レベルの保証は提供されていません。 通常、これらのデータベースでは、Azure Cosmos DB によって提供される 5 つの整合性モデルのサブセットのみが提供されます。 SQL API、Gremlin API、および Table API の場合、Azure Cosmos DB アカウントで構成された既定の整合性レベルが使用されます。 

以下のセクションでは、Apache Cassandra 4.x および MongoDB 3.4 の OSS クライアント ドライバーによって要求されるデータ整合性間のマッピングを示します。 このドキュメントでは、Apache Cassandra と MongoDB の対応する Azure Cosmos DB の整合性レベルも示されます。

## <a id="cassandra-mapping"></a>Apache Cassandra と Azure Cosmos DB の間の整合性レベルのマッピング

次の表では、Apache Cassandra と、Azure Cosmos DB の整合性レベルの間での、"読み取り整合性" のマッピングを示します。 Cassandra の読み取りと書き込みの整合性レベルのそれぞれに対し、対応する Cosmos DB の整合性レベルはより強力な、つまりより厳密な保証を提供します。

次の表に、Azure Cosmos DB と Cassandra の**書き込み整合性のマッピング**を示します。

| Cassandra | Azure Cosmos DB | 保証 |
| - | - | - |
|ALL|Strong  | 線形化可能性 |
| EACH_QUORUM   | Strong    | 線形化可能性 | 
| QUORUM、SERIAL |  Strong |    線形化可能性 |
| LOCAL_QUORUM、THREE、TWO、ONE、LOCAL_ONE、ANY | 一貫性のあるプレフィックス |グローバルな一貫性のあるプレフィックス |
| EACH_QUORUM   | Strong    | 線形化可能性 |
| QUORUM、SERIAL |  Strong |    線形化可能性 |
| LOCAL_QUORUM、THREE、TWO、ONE、LOCAL_ONE、ANY | 一貫性のあるプレフィックス | グローバルな一貫性のあるプレフィックス |
| QUORUM、SERIAL | Strong   | 線形化可能性 |
| LOCAL_QUORUM、THREE、TWO、ONE、LOCAL_ONE、ANY | 一貫性のあるプレフィックス | グローバルな一貫性のあるプレフィックス |
| LOCAL_QUORUM、LOCAL_SERIAL、TWO、THREE    | Bounded Staleness | <ul><li>有界整合性制約。</li><li>最大 K バージョンまたは t 時間の遅れ。</li><li>リージョンでコミットされた最新の値を読み取ります。</li></ul> |
| ONE、LOCAL_ONE、ANY   | 一貫性のあるプレフィックス | リージョンごとに一貫性のあるプレフィックス |

次の表に、Azure Cosmos DB と Cassandra の**読み取り整合性のマッピング**を示します。

| Cassandra | Azure Cosmos DB | 保証 |
| - | - | - |
| ALL、QUORUM、SERIAL、LOCAL_QUORUM、LOCAL_SERIAL、THREE、TWO、ONE、LOCAL_ONE | Strong  | 線形化可能性|
| ALL、QUORUM、SERIAL、LOCAL_QUORUM、LOCAL_SERIAL、THREE、TWO   |Strong |   線形化可能性 |
|LOCAL_ONE、ONE | 一貫性のあるプレフィックス | グローバルな一貫性のあるプレフィックス |
| ALL、QUORUM、SERIAL   | Strong    | 線形化可能性 |
| LOCAL_ONE、ONE、LOCAL_QUORUM、LOCAL_SERIAL、TWO、THREE |  一貫性のあるプレフィックス   | グローバルな一貫性のあるプレフィックス |
| LOCAL_ONE、ONE、TWO、THREE、LOCAL_QUORUM、QUORUM |    一貫性のあるプレフィックス   | グローバルな一貫性のあるプレフィックス |
| ALL、QUORUM、SERIAL、LOCAL_QUORUM、LOCAL_SERIAL、THREE、TWO   |Strong |   線形化可能性 |
| LOCAL_ONE、ONE    | 一貫性のあるプレフィックス | グローバルな一貫性のあるプレフィックス|
| ALL、QUORUM、SERIAL   Strong  線形化可能性
LOCAL_ONE、ONE、LOCAL_QUORUM、LOCAL_SERIAL、TWO、THREE  |一貫性のあるプレフィックス  | グローバルな一貫性のあるプレフィックス |
|ALL    |Strong |線形化可能性 |
| LOCAL_ONE、ONE、TWO、THREE、LOCAL_QUORUM、QUORUM  |一貫性のあるプレフィックス  |グローバルな一貫性のあるプレフィックス|
|ALL、QUORUM、SERIAL    Strong  線形化可能性
LOCAL_ONE、ONE、LOCAL_QUORUM、LOCAL_SERIAL、TWO、THREE  |一貫性のあるプレフィックス  |グローバルな一貫性のあるプレフィックス |
|ALL    |Strong | 線形化可能性 |
| LOCAL_ONE、ONE、TWO、THREE、LOCAL_QUORUM、QUORUM  | 一貫性のあるプレフィックス | グローバルな一貫性のあるプレフィックス |
| QUORUM、LOCAL_QUORUM、LOCAL_SERIAL、TWO、THREE |  Bounded Staleness   | <ul><li>有界整合性制約。</li><li>最大 K バージョンまたは t 時間の遅れ。 </li><li>リージョンでコミットされた最新の値を読み取ります。</li></ul>
| LOCAL_ONE、ONE |一貫性のあるプレフィックス | リージョンごとに一貫性のあるプレフィックス |
| LOCAL_ONE、ONE、TWO、THREE、LOCAL_QUORUM、QUORUM  | 一貫性のあるプレフィックス | リージョンごとに一貫性のあるプレフィックス |


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
* [Azure Cosmos DB の MongoDB 用 API でサポートされている MongoDB の機能](mongodb-feature-support.md)
* [Azure Cosmos DB Cassandra API でサポートされる Apache Cassandra の機能](cassandra-support.md)