---
title: Azure Cosmos DB の一貫性レベル | Microsoft Docs
description: Azure Cosmos DB には、結果的な一貫性、可用性、待機時間のトレードオフを調整できる 5 つの一貫性レベルがあります。
keywords: eventual 一貫性, azure cosmos db, azure, Microsoft azure
services: cosmos-db
author: aliuy
manager: kfile
ms.service: cosmos-db
ms.devlang: na
ms.topic: conceptual
ms.date: 03/27/2018
ms.author: andrl
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 2611c25764503551c4da918d06bcaabe315cbf7c
ms.sourcegitcommit: ada7419db9d03de550fbadf2f2bb2670c95cdb21
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/02/2018
ms.locfileid: "50963083"
---
# <a name="consistency-levels-in-azure-cosmos-db"></a>Azure Cosmos DB の整合性レベル

分散型データベースでは高可用性もしくは低遅延、またはその両方がレプリケーションに依存するため、読み取りの整合性と、可用性、待機時間、およびスループットとの間に基本的なトレードオフが生じます。 市販のほとんどの分散型データベースでは、開発者は 2 つの極端な整合性モデル (厳密な整合性と最終的な整合性) のどちらかを選ぶことを求められます。  [直線化可能性](http://cs.brown.edu/~mph/HerlihyW90/p463-herlihy.pdf)すなわち厳密な整合性モデルは、データ プログラミングの標準基準ですが、(安定状態での) 待機時間が長く、(障害発生時の) 可用性が低いという代償が発生します。 その一方で、最終的な整合性は、可用性が高くパフォーマンスに優れていますが、アプリケーションのプログラミングが難しくなります。

Cosmos DB では、2 つの両極端ではなく、幅広い選択肢を利用できるデータ整合性に対応しています。 選択肢の両端にあるのが厳密な整合性と最終的な整合性ですが、その間には多くの整合性の選択肢があります。 これらの整合性オプションを利用すると、開発者が最適な選択を行い、高可用性とパフォーマンスに関して詳細なトレードオフを決定できます。 Cosmos DB により開発者は、整合性の程度 (強い順) に応じて明確に定義された 5 種類の整合性モデルの中から選択することができるようになりました。具体的には、**厳密**、**有界整合性制約**、**セッション**、**一貫性のあるプレフィックス**、**最終的**の 5 つが用意されています。 これらの整合性モデルはそれぞれ、適切に定義されており、直感的に理解することができます。また、実際の具体的なシナリオで利用できます。 この 5 つの整合性モデルはそれぞれ、[可用性およびパフォーマンスのトレードオフ](consistency-levels-tradeoffs.md)を提供し、包括的な SLA によってサポートされます。

![範囲としての整合性](./media/consistency-levels/five-consistency-levels.png)

整合性レベルはリージョンに依存しません。 Cosmos DB アカウントの整合性レベルは、以下のプロパティに関係なくすべての読み取り操作について保証されます。

- 読み取りと書き込みが行われるリージョン
- Cosmos アカウントに関連付けられているリージョンの数
- アカウントに構成されている書き込みリージョンが 1 つか複数か

## <a name="scope-of-the-read-consistency"></a>読み取り整合性のスコープ

読み取り整合性は、パーティションキー範囲 (論理パーティション) 内をスコープとする 1 つの読み取り操作に適用されます。 読み取り操作はリモート クライアントまたはストアド プロシージャが発行できます。

## <a name="configuring-the-default-consistency-level"></a>既定の一貫性レベルの構成

Cosmos DB アカウントの**既定の整合性レベル**はいつでも構成できます。 自分のアカウントに構成されている既定の整合性レベルは、そのアカウントのすべての Cosmos データベース (およびコンテナー) に適用されます。 コンテナーまたはデータベースに対して発行されるすべての読み取りとクエリは、指定された整合性レベルを既定で使用します。 詳細については、[既定の整合性レベルを構成する](how-to-manage-consistency.md#configure-the-default-consistency-level)方法に関する記事をご覧ください。

## <a name="guarantees-associated-with-consistency-levels"></a>整合性レベルに関連付けられた保証

Azure Cosmos DB によって提供される包括的 SLA では、読み取り要求の 100% が、選択した任意の整合性レベルの整合性の保証を満たすことが保証されます。 整合性レベルに関連付けられている整合性の保証がすべて満たされていれば、読み取り要求は整合性の SLA を満たしていると見なされます。 [TLA+ 仕様言語](http://lamport.azurewebsites.net/tla/tla.html)を使用した Cosmos DB での 5 つの整合性レベルの正確な定義は、[azure-cosmos-tla](https://github.com/Azure/azure-cosmos-tla) GitHub リポジトリに提供されています。 5 つの整合性レベルのセマンティクスを以下で説明します。

- **整合性レベル = "厳密"**: 厳密な整合性では、[線形化可能性](https://aphyr.com/posts/313-strong-consistency-models)が保証されます。ここでは読み取りに対して最新のコミット済みバージョンのアイテムを返すことが保証されています。 クライアントが、コミットされていない書き込みや部分的な書き込みを見ることは決してありません。 ユーザーが最新のコミット済みの書き込みを読み取ることが常に保証されます。

- **整合性レベル = "有界整合性制約"**: 読み取りでは、一貫性のあるプレフィックスの優先が保証されます。 読み取りが書き込みよりも遅れますが、最大でアイテムの K 個のバージョン (更新) の分、あるいは期間 "t" の分だけです。 Bounded Staleness を選択する場合、"staleness" は 2 つの方法で構成できます。 

  * アイテムのバージョンの数 (K) または
  * 書き込みに対して読み取りが遅れる期間 (t)。 

  Bounded Staleness では、"staleness 期間" 内を除いてトータルなグローバル順序が実現されます。 リージョン内では、"整合性制約期間" 内外の両方でモノトニックな読み取り保証が実現されます。 厳密な整合性にも、有界整合性制約で提供されるのと同じセマンティクスがあり、"整合性制約期間" は 0 です。 有界整合性制約は、**遅延線形化可能性**とも呼ばれます。 書き込みを受け入れるリージョン内でクライアントが読み取り操作を実行するとき、有界整合性制約整合性で提供される保証は、厳密な整合性の場合と同じです。

- **整合性レベル = "セッション"**: 読み取りでは、一貫性のあるプレフィックス、単調読み取り、単調書き込み、自己書き込みの読み取り、読み取り後の書き込みの優先が保証されます。 セッション整合性のスコープはクライアント セッションです。

- **整合性レベル = "一貫性のあるプレフィックス"**: 返される更新には、それ以外の全更新の一部のプレフィックスが含まれます (ギャップなし)。 また、読み取りでは決して順不同の書き込みが確認されないことが保証されます。

- **整合性レベル = "最終的"**: 読み取りの順序の保証がありません。 さらに書き込みがない場合、レプリカが最終的に収束します。

## <a name="consistency-levels-explained-through-baseball"></a>野球で説明する整合性レベル

例として野球の試合のシナリオを見てみましょう。「[Replicated Data Consistency Through Baseball](https://www.microsoft.com/en-us/research/wp-content/uploads/2011/10/ConsistencyAndBaseballReport.pdf)」(レプリケート データの整合性を野球にたとえると) という論文で説明しているように、一連の書き込みが、野球の試合の回ごとの点数に相当すると想像してみてください。 この仮説の野球の試合は、現在 7 回の表と裏の間 (有名なセブンイニング ストレッチ) で、ホーム チームが 2 対 5 でリードしています。

| | **1** | **2** | **3** | **4** | **5** | **6** | **7** | **8** | **9** | **計** |
| - | - | - | - | - | - | - | - | - | - | - |
| **ビジター** | 0 | 0 | 1 | 0 | 1 | 0 | 0 |  |  | 2 |
| **ホーム** | 1 | 0 | 1 | 1 | 0 | 2 |  |  |  | 5 |

Cosmos DB コンテナーに、ビジター チームとホーム チームの合計得点が格納されます。 試合の進行中に、さまざまな読み取り保証を使用すると、クライアントは異なる点数を読み取ることになります。 次の表に、5 つの整合性保証それぞれを使用してビジターとホームの点数を読み取ったときに、返される可能性がある点数の一覧を示します。 最初に表示されているのはビジターの点数で、さまざまな戻り値がコンマで区切って示されています。

| **整合性レベル** | **点数** |
| - | - |
| **厳密** | 2-5 |
| **有界整合性制約** | 最大で 1 イニング遅れの点数" 2-3、2-4、2-5 |
| **セッション** | <ul><li>書き込み側" 2-5</li><li> 書き込み側以外: 0-0、0-1、0-2、0-3、0-4、0-5、1-0、1-1、1-2、1-3、1-4、1-5、2-0、2-1、2-2、2-3、2-4、2-5</li><li>1-3 を読み取った後: 1-3、1-4、1-5、2-3、2-4、2-5</li> |
| **一貫性のあるプレフィックス** | 0-0、0-1、1-1、1-2、1-3、2-3、2-4、2-5 |
| **最終的** | 0-0、0-1、0-2、0-3、0-4、0-5、1-0、1-1、1-2、1-3、1-4、1-5、2-0、2-1、2-2、2-3、2-4、2-5 |

## <a name="additional-reading"></a>その他の情報

整合性の概念について詳しくは、以下の記事をご覧ください。

- 「[High-level TLA+ specifications for the five consistency levels offered by Azure Cosmos DB (Azure Cosmos DB によって提供される 5 つの整合性レベルのための高レベル TLA+ 仕様)](https://github.com/Azure/azure-cosmos-tla)」
- 「[Replicated Data Consistency Explained Through Baseball (レプリケート データの整合性を野球にたとえると) (Doug Terry によるビデオ)](https://www.youtube.com/watch?v=gluIh8zd26I)」
- 「[Replicated Data Consistency Explained Through Baseball (レプリケート データの整合性を野球にたとえると) (Doug Terry によるホワイトペーパー)](https://www.microsoft.com/en-us/research/publication/replicated-data-consistency-explained-through-baseball/?from=http%3A%2F%2Fresearch.microsoft.com%2Fpubs%2F157411%2Fconsistencyandbaseballreport.pdf)」
- 「[Session Guarantees for Weakly Consistent Replicated Data (弱一貫性レプリケート データのためのセッション保証)](https://dl.acm.org/citation.cfm?id=383631)」
- 「[Consistency Tradeoffs in Modern Distributed Database Systems Design: CAP is only part of the story (先進的な分散データベース システム設計における整合性のトレードオフ: CAP 以外の考慮事項について)](https://www.computer.org/web/csdl/index/-/csdl/mags/co/2012/02/mco2012020037-abs.html)」
- 「[Probabilistic Bounded Staleness (PBS) for Practical Partial Quorums (現実的なパーシャル クォーラムのための Probabilistic Bounded Staleness (PBS))](http://vldb.org/pvldb/vol5/p776_peterbailis_vldb2012.pdf)」
- 「[Eventually Consistent - Revisited (最終的な整合性 - 改訂版)](https://www.allthingsdistributed.com/2008/12/eventually_consistent.html)」

## <a name="next-steps"></a>次の手順

Cosmos DB の整合性レベルの概念について詳しくは、以下の記事をご覧ください。

* [アプリケーションのための適切な整合性レベルの選択](consistency-levels-choosing.md)
* [Cosmos DB API における整合性レベル](consistency-levels-across-apis.md)
* [さまざまな整合性レベルでの可用性およびパフォーマンスのトレードオフ](consistency-levels-tradeoffs.md)
* [既定の整合性レベルを構成する方法](how-to-manage-consistency.md#configure-the-default-consistency-level)
* [既定の整合性レベルをオーバーライドする方法](how-to-manage-consistency.md#override-the-default-consistency-level)

