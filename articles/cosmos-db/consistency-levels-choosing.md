---
title: Azure Cosmos DB を使用するアプリケーションのための適切な整合性レベルを選択する
description: Azure Cosmos DB でアプリケーションのための適切な整合性レベルを選択します。
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 10/24/2018
ms.reviewer: sngun
ms.openlocfilehash: c4523b5028aae5de565e517fea61c62e29a4e7a3
ms.sourcegitcommit: 8330a262abaddaafd4acb04016b68486fba5835b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/04/2019
ms.locfileid: "54044078"
---
# <a name="choose-the-right-consistency-level-for-your-application"></a>アプリケーションのための適切な整合性レベルを選択する

分散型データベースでは高可用性もしくは低遅延、またはその両方がレプリケーションに依存するため、読み取りの整合性と、可用性、待機時間、およびスループットとの間に基本的なトレードオフが生じます。 市販のほとんどの分散型データベースでは、開発者は 2 つの極端な整合性モデル (厳密な整合性と最終的な整合性) のどちらかを選ぶことを求められます。 Azure Cosmos DB により開発者は、明確に定義された 5 種類の整合性モデルの中から選択することができます。具体的には、"厳密"、"有界整合性制約"、"セッション"、"一貫性のあるプレフィックス"、"結果的" の 5 つが用意されています。 これらの整合性モデルはそれぞれ、適切に定義されており、直感的に理解することができます。また、実際の具体的なシナリオで利用できます。 この 5 つの整合性モデルはそれぞれ、[可用性およびパフォーマンスのトレードオフ](consistency-levels-tradeoffs.md)を提供し、包括的な SLA によってサポートされます。 次の単純な考慮事項は、多くの一般的なシナリオで最適な選択を行う際に役立ちます。

## <a name="sql-api-and-table-api"></a>SQL API と Table API

アプリケーションが Cosmos DB SQL API または Table API を使用して構築されている場合は、次の点を検討します

- 多くの現実のシナリオでは、セッションの整合性は最適であり、これが推奨されるオプションです。 詳細については、[アプリケーションのセッション トークンを管理する方法](how-to-manage-consistency.md#utilize-session-tokens)に関する記事を参照してください。

- アプリケーションで強力な整合性を必要とする場合は、有界整合性制約の整合性レベルを使用することをお勧めします。

- セッションの整合性で提供されるものよりも厳密で、1 桁ミリ秒の書き込み待機時間の整合性の保証を必要とする場合は、有界整合性制約の整合性レベルを使用することをお勧めします。  

- アプリケーションで最終的な整合性を必要とする場合は、一貫性のあるプレフィックスの整合性レベルを使用することをお勧めします。

- セッションの整合性で提供されるよりも厳密でない整合性の保証を必要とする場合は、一貫性のあるプレフィックスの整合性レベルを使用することをお勧めします。

- 最高レベルの可用性と最も短い待機時間が必要な場合は、最終的な整合性レベルを使用します。

## <a name="cassandra-mongodb-and-gremlin-api"></a>Cassandra、MongoDB、Gremlin API

- Apache Cassandra と Cosmos DB の整合性レベルで提供される "読み取り整合性レベル" の間のマッピングについて詳しくは、「[整合性レベルと Cosmos DB API](consistency-levels-across-apis.md#cassandra-mapping)」をご覧ください。

- MongoDB と Azure Cosmos DB の整合性レベルの「読み取りの問題」の間のマッピングの詳細については、[整合性レベルと Cosmos DB API](consistency-levels-across-apis.md#mongo-mapping)を参照してください。

## <a name="consistency-guarantees-in-practice"></a>整合性の実際の保証

実際には、より強力な整合性の保証を取得することができます。 読み取り操作の整合性の保証は、要求するデータベースの状態の鮮度と順序に対応します。 読み取り一貫性は書き込み/更新操作の順序と伝達に関連付けられています。  

* 整合性レベルを**有界整合性制約**に設定すると、Cosmos DB では、クライアントは遅延が陳腐化期間で限定されている、以前の書き込みの値を常に読み取ることを保証します。

* 整合性レベルを**強力**に設定すると、陳腐化期間は 0 に相当し、クライアントは書き込み操作の最新のコミット値を読み取ることが保証されます。

* 残りの 3 つの整合性レベルについては、陳腐化期間はワークロードに大きく依存します。 たとえば、データベースへの書き込み操作がない場合、**最終的**、**セッション**、または**一貫性のあるプレフィックス**の整合性レベルでの読み取り操作は、強力な整合性レベルの読み取り操作と同じ結果になることがあります。

Cosmos DB アカウントが、強力な整合性以外の任意の整合性レベルで構成されている場合、確率論的有界整合性制約 (PBS) メトリックを調べることによって、ワークロードの厳密な整合性を持つ読み取りをクライアントが取得する確率を調べることができます。 このメトリックは、Azure portal で公開されます。詳しくは、「[確率的有界整合性制約 (PBS) メトリックを監視する](how-to-manage-consistency.md#monitor-probabilistically-bounded-staleness-pbs-metric)」をご覧ください。

確率論的有界整合性制約は、最終的な整合性が、どれほど最終的であるかを示します。 このメトリックでは、Cosmos DB アカウントで現在構成されている整合性レベルより強力な整合性をどのくらいの頻度で得られるかについての分析情報が提供されます。 つまり、書き込みと読み取りのリージョンの組み合わせに対して厳密な整合性の読み取りを取得する確率 (ミリ秒で測定) を表示できます。

## <a name="next-steps"></a>次の手順

次の記事で、整合性レベルについての詳細を参照してください。

* [Cosmos DB API 間での整合性レベルのマッピング](consistency-levels-across-apis.md)
* [さまざまな整合性レベルでの可用性およびパフォーマンスのトレードオフ](consistency-levels-tradeoffs.md)
* [アプリケーションのセッション トークンを管理する方法](how-to-manage-consistency.md#utilize-session-tokens)
* [確率的有界整合性制約 (PBS) メトリックを監視する](how-to-manage-consistency.md#monitor-probabilistically-bounded-staleness-pbs-metric)
