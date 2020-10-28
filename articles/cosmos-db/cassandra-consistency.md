---
title: Apache Cassandra と Azure Cosmos DB の整合性レベル
description: Apache Cassandra と Azure Cosmos DB の整合性レベル。
author: TheovanKraay
ms.author: thvankra
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 10/12/2020
ms.reviewer: sngun
ms.openlocfilehash: 155e1ed5b089c16887c845c94f36c9c6d9f0af6e
ms.sourcegitcommit: b6f3ccaadf2f7eba4254a402e954adf430a90003
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/20/2020
ms.locfileid: "92283292"
---
# <a name="apache-cassandra-and-azure-cosmos-db-consistency-levels"></a>Apache Cassandra と Azure Cosmos DB の整合性レベル

Apache Cassandra は、Azure Cosmos DB とは異なり、整合性の保証がネイティブで正確に定義されていません。 代わりに Apache Cassandra には、高可用性、整合性および待機時間とのトレードオフを可能にする、書き込みの整合性レベルと読み取りの整合性レベルがあります。 Azure Cosmos DB の Cassandra API を使用する場合:

* Apache Cassandra の書き込み整合性レベルは、お使いの Azure Cosmos アカウントに構成された既定の整合性レベルにマップされます。 書き込み操作 (CL) の整合性は、要求ごとに変更することはできません。

* Azure Cosmos DB は、Cassandra クライアント ドライバーによって指定された読み取り整合性レベルを、読み取り要求に動的に構成された Azure Cosmos DB 整合性レベルの 1 つに動的にマップします。

## <a name="mapping-consistency-levels"></a>整合性レベルのマッピング

次の表に、Cassandra API を使用した場合に、ネイティブ Cassandra 整合性レベルがどのように Azure Cosmos DB の整合性レベルにマップされるかを示しています。  

:::image type="content" source="./media/consistency-levels-across-apis/consistency-model-mapping-cassandra.png" alt-text="Cassandra の整合性モデル マッピング" lightbox="./media/consistency-levels-across-apis/consistency-model-mapping-cassandra.png" :::

Azure Cosmos アカウントが、強固な整合性以外の任意の整合性レベルで構成されている場合、 *確率論的有界整合性制約* (PBS) メトリックを調べることによって、ワークロードの厳密な整合性を持つ読み取りをクライアントが取得する確率を調べることができます。 このメトリックは、Azure portal で公開されます。詳しくは、「[確率的有界整合性制約 (PBS) メトリックを監視する](how-to-manage-consistency.md#monitor-probabilistically-bounded-staleness-pbs-metric)」をご覧ください。

確率論的有界整合性制約は、最終的な整合性が、どれほど最終的であるかを示します。 このメトリックでは、Azure Cosmos アカウントで現在構成されている整合性レベルより強力な整合性をどのくらいの頻度で得られるかについての分析情報が提供されます。 つまり、書き込みと読み取りのリージョンの組み合わせに対して厳密な整合性の読み取りを取得する確率 (ミリ秒で測定) を表示できます。

## <a name="next-steps"></a>次のステップ

Azure Cosmos DB でのグローバル配信と整合性レベルについて学習します。

* [グローバル配信の概要](distribute-data-globally.md)
* [整合性レベルの概要](consistency-levels.md)
* [高可用性](high-availability.md)
