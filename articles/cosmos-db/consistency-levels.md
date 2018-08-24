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
ms.openlocfilehash: 8d95790dc09f6d26c6ae749ed0cd386053c5cb35
ms.sourcegitcommit: 7b845d3b9a5a4487d5df89906cc5d5bbdb0507c8
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/14/2018
ms.locfileid: "42140949"
---
# <a name="tunable-data-consistency-levels-in-azure-cosmos-db"></a>Azure Cosmos DB の調整可能なデータの一貫性レベル
Azure Cosmos DB は、すべてのデータ モデルについて、最初からグローバル分散を念頭に置いて設計されています。 その設計により、予測可能な待機時間の短縮の保証と、明確に定義された複数の緩やかな一貫性モデルが提供されます。 現在、Azure Cosmos DB では、厳密、有界整合性制約、セッション、最終的の 5 つの整合性レベルが用意されています。 有界整合性制約、セッション、一貫性のあるプレフィックス、および最終的は、厳密より一貫性が低いので "緩やかな一貫性モデル" と呼ばれます。厳密は、使用できる最も一貫性の高いモデルです。 

分散データベースが一般的に提供している**厳密**と**最終的の一貫性**モデルに加え、Azure Cosmos DB では、注意深く体系化、運用化された一貫性モデルとして、**有界整合性制約**、**セッション**、**一貫性のあるプレフィックス**の 3 つがさらに提供されています。 これらの各一貫性レベルの有用性は、現実のユース ケースに対して検証されています。 これら 5 つの一貫性レベルが揃っているため、一貫性、可用性、待機時間の最適なトレードオフを根拠を持って検討することができます。 

次の動画では、Azure Cosmos DB プログラム マネージャーの Andrew Liu がターン キー グローバル分散機能を紹介します。

>[!VIDEO https://www.youtube.com/embed/-4FsGysVD14]

## <a name="distributed-databases-and-consistency"></a>分散データベースと一貫性
分散型の商用データベースは、2 つのカテゴリに分けることができます。1 つは整合性に関して明確に定義された証明可能な選択肢が用意されていないデータベース、もう 1 つはプログラム面で大きく異なる 2 つの選択肢 (厳密な整合性と結果整合性) が存在するデータベースです。 

前者はレプリケーションのプロトコルが細かく、アプリケーション開発者にとって負担となります。また、整合性、可用性、待ち時間、スループットのトレードオフを検討するという難しい作業が発生します。 後者は、まったく異なる 2 つの選択肢から 1 つを選ばなければならないという点が大きな重圧となります。 整合性モデルに関する調査や提案はこれまでに幾度となく実施されており、対象となったモデルの種類も 50 以上にのぼりますが、分散データベースのコミュニティでは依然として、厳密な整合性と結果整合性を超える整合性レベルの商品化には至っていません。 Cosmos DB により開発者は、一貫性の程度に応じて明確に定義された 5 種類の一貫性モデルの中から選択することができます。具体的には、"厳密"、"有界整合性制約"、"[セッション](http://dl.acm.org/citation.cfm?id=383631)"、"一貫性のあるプレフィックス"、"結果的" の 5 つが用意されています。 

![Azure Cosmos DB では、明確に定義された (緩やかな) 整合性モデルがいくつも用意されており、そこから 1 つを選択できます](./media/consistency-levels/five-consistency-levels.png)

以下の表は、それぞれの整合性レベルで提供される具体的な保証の内容を示したものです。
 
**整合性レベルと保証内容**

| 整合性レベル | 保証内容 |
| --- | --- |
| Strong | 線形化可能性。 読み取りでは、項目の最新バージョンが返ることが保証されています。|
| Bounded Staleness | 一貫性のあるプレフィックス。 書き込みに対して読み取りが最大でプレフィックス k または間隔 t の後れを取る |
| Session   | 一貫性のあるプレフィックス。 単調読み取り、単調書き込み、書き込み後の読み取り、読み取り後の書き込み |
| 一貫性のあるプレフィックス | 返される更新が、それ以外の全更新の一部のプレフィックスとなる (ギャップなし) |
| Eventual  | 読み取りは順不同 |

Cosmos DB アカウントには、既定の整合性レベルを構成できます (さらに、後から特定の読み取り要求について既定の整合性をオーバーライドすることもできます)。 内部的には、複数のリージョンにまたがる可能性があるパーティション セット内のデータに対して、既定の整合性レベルが適用されます。 Azure Cosmos DB テナントの 約 73% がセッション一貫性を使い、20% が有界整合性制約を使っています。 Azure Cosmos DB の顧客の約 3% が、アプリケーション用に特定の一貫性の設定を選ぶ前に、さまざまな一貫性レベルを試しています。 要求ベースで一貫性レベルをオーバーライドしているのは、Azure Cosmos DB テナントのわずか 2% です。 

Cosmos DB では、Session、Consistent Prefix、Eventual Consistency での読み取りは、Strong または Bounded Staleness 一貫性での読み取りより 2 倍節約できます。 Cosmos DB は、一貫性の保証に加え、可用性、スループット、待機時間を含む、業界最高レベルの包括的な SLA を誇ります。 Azure Cosmos DB では、サービス テレメトリで継続的に動作し、一貫性の違反を公表する[線形化可能性チェッカー](http://dl.acm.org/citation.cfm?id=1806634)が採用されています。 有界整合性制約では、違反がないか監視して k および t 制約に報告します。 また、5 つの緩やかな一貫性レベルすべてについて、[確率論的有界整合性制約メトリック](http://dl.acm.org/citation.cfm?id=2212359)をユーザーに直接報告します。  

## <a name="service-level-agreements"></a>サービス レベル アグリーメント

Azure Cosmos DB は、包括的な 99.99% の [SLA](https://azure.microsoft.com/support/legal/sla/cosmos-db/) を提供します。これは、5 つの一貫性レベルのいずれかで構成された単一の Azure リージョンを対象とする Azure Cosmos DB データベース アカウント、または 4 つの緩やかな一貫性レベルのいずれかで構成された複数の Azure リージョンにまたがるデータベース アカウントに対し、スループット、一貫性、可用性、待機時間を保証します。 さらに、一貫性レベルの選択とは関係なく、Azure Cosmos DB は複数の Azure リージョンにまたがるデータベース アカウントの読み取り可用性に対して 99.999% の SLA を提供します。

## <a name="scope-of-consistency"></a>一貫性のスコープ
一貫性の粒度の対象は、単一のユーザー要求です。 書き込み要求は、挿入、置換、アップサート、または削除のトランザクションに対応付けられます。 書き込みと同様に、読み取り/クエリ トランザクションの対象も単一のユーザー要求です。 ユーザーは複数のパーティションにまたがって大規模な結果セットを読み込む必要がありますが、各読み取りトランザクションは単一のページを対象とし、単一のパーティション内から提供されます。

## <a name="consistency-levels"></a>一貫性レベル
データベース アカウントには既定の一貫性レベルを構成できます。既定の一貫性レベルは、対象となる Cosmos DB アカウント下のすべてのコンテナー (およびデータベース) に適用されます。 既定では、ユーザー定義リソースに対して発行されたすべての読み取りとクエリに、データベース アカウントに指定された既定の一貫性レベルが使用されます。 サポートされている各 API の特定の読み取り/クエリ要求の一貫性レベルを緩和することもできます。 このセクションに示されているように、Azure Cosmos DB のレプリケーション プロトコルによって 5 種類の一貫性レベルがサポートされており、特定の一貫性の保証とパフォーマンスの間の明確なトレードオフを実現しています。

<a id="strong"></a>
**Strong**: 

* Strong 一貫性では、[線形化可能性](https://aphyr.com/posts/313-strong-consistency-models)が保証されます。ここでは読み取りに対して最新バージョンのアイテムを返すことが保証されています。 
* レプリカのマジョリティ クォーラムによってコミットされた後でのみ、書き込み結果が見える状態になることが保証されます。 書き込みは、プライマリとセカンダリのクォーラムとの両方によって同期的かつ永続的にコミットされるか、または中止されるか、のどちらかとなります。 読み取りは常に、マジョリティ リード クォーラムによって確認応答されます。未コミットの書き込みや部分的な書き込みがクライアントから見えることはありません。読み取りの内容は常に、確認応答が返された最新の書き込み結果であることが保証されます。 
* Strong 一貫性を使用するように構成された Azure Cosmos DB アカウントでは、複数の Azure リージョンをその Azure Cosmos DB アカウントに関連付けることができません。  
* "厳密な" 整合性を使用した場合の (消費される [要求ユニット](request-units.md) に関する) 読み取り操作のコストは "セッション" 整合性や "最終的" 整合性よりも高いものの、有界整合性制約と同じです。

<a id="bounded-staleness"></a>
**Bounded Staleness**: 

* Bounded Staleness 一貫性では、最大でアイテムの *K* 個のバージョンまたはプレフィックスあるいは期間 *t* の分だけ、読み取りが書き込みに対し遅れることが保証されます。 
* このため、Bounded Staleness を選択する場合、"staleness" は、アイテムのバージョンの数 *K* (書き込みに対する読み取りの遅れ) と、期間 *t* の 2 つの方法で構成できます。 
* Bounded Staleness では、"staleness 期間" 内を除いてトータルなグローバル順序が実現されます。 リージョン内では、"整合性制約期間" 内外の両方でモノトニックな読み取り保証が実現されます。 
* 有界整合性制約では、セッションまたは最終的の一貫性より強力な一貫性が保証されます。 グローバルに分散されるアプリケーションについては、強力な一貫性だけでなく、99.99% の可用性と短い待機時間も求められるシナリオで Bounded Staleness を使用することをお勧めします。   
* Bounded Staleness 一貫性が構成された Azure Cosmos DB アカウントでは、任意の数の Azure リージョンをその Azure Cosmos DB アカウントと関連付けることができます。 
* "有界整合性制約" を使用した場合の (消費される RU に関する) 読み取り操作のコストは "セッション" 整合性や "最終的" 整合性よりも高いものの、"厳密な" 整合性と同じです。

<a id="session"></a>
**Session**: 

* Strong と Bounded Staleness の一貫性レベルで得られるグローバルな一貫性モデルとは異なり、Session 一貫性はクライアント セッションを対象とします。 
* Session 一貫性は、デバイスまたはユーザーのセッションが関係するすべてのシナリオに最適です。これは、モノトニックな読み取り、モノトニックな書き込み、自己の書き込みの読み取り (RYW) が保証されるためです。 
* Session 一貫性では、セッションの予測可能な一貫性が確保されます。また、書き込みと読み取りの待機時間が最も短い一方で、読み取りスループットは最大です。 
* Session 一貫性が構成された Azure Cosmos DB アカウントでは、任意の数の Azure リージョンをその Azure Cosmos DB アカウントと関連付けることができます。 
* セッション一貫性レベルを使った場合の (消費される RU に関する) 読み取り操作のコストは、厳密および有界整合性制約より低く、最終的一貫性より高くなります。

<a id="consistent-prefix"></a>
**一貫性のあるプレフィックス**: 

* 一貫性のあるプレフィックスでは、長期間書き込みがない場合にグループ内のレプリカがやがて収束することが保証されます。 
* また、読み取りでは決して順不同の書き込みが確認されないことが保証されます。 書き込みが `A, B, C` の順で実行された場合、クライアントでは `A`、`A,B`、または `A,B,C` が確認されますが、`A,C` または `B,A,C` などの順不同が確認されることはありません。
* 一貫性のあるプレフィックスが構成された Azure Cosmos DB アカウントでは、任意の数の Azure リージョンをその Azure Cosmos DB アカウントと関連付けることができます。 

<a id="eventual"></a>
**Eventual**: 

* Eventual 一貫性では、長期間書き込みがない場合にグループ内のレプリカがやがて収束することが保証されます。 
* Eventual 一貫性は最も弱い形態の一貫性であり、クライアントは、過去に認識した値よりも古い値を取得する可能性があります。
* Eventual の一貫性は、読み取りの一貫性という点は最も弱いですが、読み取りと書き込みの待機時間は最短となります。
* Eventual 一貫性が構成された Azure Cosmos DB アカウントでは、任意の数の Azure リージョンをその Azure Cosmos DB アカウントと関連付けることができます。 
* Eventual 一貫性レベルを使用した場合の (消費される RU に関する) 読み取り操作のコストは、すべての Azure Cosmos DB 一貫性レベルの中で最も低くなります。

## <a name="configuring-the-default-consistency-level"></a>既定の一貫性レベルの構成
1. [Azure Portal](https://portal.azure.com/) で、ジャンプバーの **[Azure Cosmos DB]** をクリックします。
2. **[Azure Cosmos DB]** ページで、変更するデータベース アカウントを選びます。
3. アカウント ページで、**[既定の整合性]** をクリックします。
4. **[既定の整合性]** ページで、新しい一貫性レベルを選び、**[保存]** をクリックします。
   
    ![[設定] アイコンと [既定の整合性] エントリが強調表示されたスクリーン ショット](./media/consistency-levels/database-consistency-level-1.png)

## <a name="consistency-levels-for-queries"></a>クエリの一貫性レベル
既定では、ユーザー定義リソースに関するクエリの一貫性レベルは、読み取りの一貫性レベルと同じです。 既定では、Cosmos DB コンテナーに対するアイテムの挿入、置換、削除のたびに、インデックスが同期的に更新されます。 これにより、クエリには、アイテムの読み取りと同じ一貫性レベルが保証されます。 Azure Cosmos DB では書き込みが最適化されており、持続的書き込み、同期インデックス メンテナンス、クエリの一貫性の確保がサポートされていますが、特定のコンテナーのインデックスが遅れて更新されるように構成することもできます。 インデックスの遅延作成は、書き込みのパフォーマンスを飛躍的に高める効果があり、ワークロードで主に読み取りの負荷が激しい状況での一括インジェストに最適です。  

| インデックス作成モード | 読み取り | クエリ |
| --- | --- | --- |
| 一貫性 (既定) |Strong、Bounded Staleness、Session、Consistent Prefix、Eventual の中から選択 |Strong、Bounded Staleness、Session、Eventual の中から選択 |
| 遅延 |Strong、Bounded Staleness、Session、Consistent Prefix、Eventual の中から選択 |Eventual |
| なし |Strong、Bounded Staleness、Session、Consistent Prefix、Eventual の中から選択 |適用不可 |

読み取り要求と同様に、各 API の特定のクエリ要求の一貫性レベルを引き下げることができます。

## <a name="consistency-levels-for-the-mongodb-api"></a>MongoDB API の一貫性レベル

Azure Cosmos DB は現在 MongoDB バージョン 3.4 を実装しています。このバージョンには Strong と Eventual という 2 つの一貫性設定があります。 Azure Cosmos DB はマルチ API であるため、一貫性設定をアカウント レベルに適用でき、一貫性の強制は API ごとに制御されます。  MongoDB 3.6 まではセッションの一貫性の概念がなかったため、セッションの一貫性を使用するために MongoDB API アカウントを設定すると、MongoDB API を使用したときに一貫性が Eventual にダウングレードされます。 MongoDB API アカウントに read-your-own-write (RYOW) の保証が必要な場合、アカウントの既定の一貫性レベルは Strong または Bounded Stateless に設定する必要があります。

## <a name="next-steps"></a>次の手順
一貫性レベルとトレードオフの詳細については、以下の資料を参照してください。

* 「[Replicated Data Consistency Explained Through Baseball (レプリケート データの一貫性を野球にたとえると) (Doug Terry によるビデオ)](https://www.youtube.com/watch?v=gluIh8zd26I)」
* 「[Replicated Data Consistency Explained Through Baseball (レプリケート データの一貫性を野球にたとえると) (Doug Terry によるホワイトペーパー)](http://research.microsoft.com/pubs/157411/ConsistencyAndBaseballReport.pdf)」
* 「[Session Guarantees for Weakly Consistent Replicated Data (弱一貫性レプリケート データのためのセッション保証)](http://dl.acm.org/citation.cfm?id=383631)」
* 「[Consistency Tradeoffs in Modern Distributed Database Systems Design: CAP is only part of the story (先進的な分散データベース システム設計における一貫性のトレードオフ: CAP 以外の考慮事項について)](https://www.computer.org/web/csdl/index/-/csdl/mags/co/2012/02/mco2012020037-abs.html)」
* 「[Probabilistic Bounded Staleness (PBS) for Practical Partial Quorums (現実的なパーシャル クォーラムのための Probabilistic Bounded Staleness (PBS))](http://vldb.org/pvldb/vol5/p776_peterbailis_vldb2012.pdf)」
* 「[Eventual Consistent - Revisited (結果的な一貫性 - 改訂版)](http://allthingsdistributed.com/2008/12/eventually_consistent.html)」
* 「[The Load, Capacity, and Availability of Quorum Systems (クォーラム システムの負荷、容量、および可用性), SIAM Journal on Computing](http://epubs.siam.org/doi/abs/10.1137/S0097539795281232)」
* 「[Line-up: a complete and automatic linearizability checker, Proceedings of the 2010 ACM SIGPLAN conference on Programming language design and implementation (ラインナップ: 完全かつ自動化された不可分操作チェッカー、プログラミング言語の設計と実装に関する 2010 ACM SIGPLAN カンファレンスの議事録)](http://dl.acm.org/citation.cfm?id=1806634)」
* 「[Probabilistically bounded staleness for practical partial quorums (現実的なパーシャル クォーラムのための確率的有界整合性制約)](http://dl.acm.org/citation.cfm?id=2212359)」
