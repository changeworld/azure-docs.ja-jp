---
title: Azure Cosmos DB でのスループットのスケーリング
description: この記事では、Azure Cosmos DB がスループットを弾力的にスケーリングする方法について説明します
services: cosmos-db
author: dharmas
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 10/15/2018
ms.author: dharmas
ms.reviewer: sngun
ms.openlocfilehash: d29f01c7f953ed211b429e41b844a01c67e41054
ms.sourcegitcommit: ba4570d778187a975645a45920d1d631139ac36e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/08/2018
ms.locfileid: "51282378"
---
# <a name="scaling-throughput-in-azure-cosmos-db"></a>Azure Cosmos DB でのスループットのスケーリング

Azure Cosmos DB では、プロビジョニングされたスループットは、要求ユニット/秒 (RU/秒、複数形: RUs) として表現されます。 次の図に示すように、RU は Cosmos コンテナーに対する読み書き両方の操作のコストを測定します。

![要求ユニット](./media/scale-throughput/figure1.png)

RU は Cosmos コンテナーまたは Cosmos データベースにプロビジョニングできます。 コンテナーにプロビジョニングされた RU は、そのコンテナーに対して実行される操作についてのみ使用できます。 データベースにプロビジョニングされた RU は、そのデータベース内のすべてのコンテナー間で共有されます (排他的に割り当てられた RU を持つコンテナーを除く)。

スループットを弾力的にスケーリングするために、プロビジョニングされた RU/秒をいつでも増減できます。 詳細については、[スループットのプロビジョニングに関する方法](set-throughput.md)、および Cosmos コンテナーとデータベースを弾力的にスケーリングする方法に関するページを参照してください。 スループットをグローバルにスケーリングするために、Cosmos アカウントにリージョンをいつでも追加または削除できます。 詳細については、[Cosmos アカウントにリージョンを追加または削除する方法](how-to-manage-database-account.md#addremove-regions-from-your-database-account)に関するページを参照してください。 Cosmos アカウントに複数のリージョンを関連付けることは、短い待機時間と[高い可用性](high-availability.md)を世界中で実現するために多くのシナリオで重要です。

## <a name="how-provisioned-throughput-is-distributed-across-regions"></a>プロビジョニングされたスループットのリージョン間での分散方法

Cosmos コンテナー (またはデータベース) に 'R' 個の RU をプロビジョニングする場合、Cosmos DB は、Cosmos アカウントに関連付けられている*それぞれの*リージョンで 'R' 個の RU が利用可能であることを保証します。 アカウントに新しいリージョンを追加するたびに、Cosmos DB では、新しく追加されたリージョンに 'R' 個の RU を自動的にプロビジョニングします。 Cosmos コンテナーに対して実行される操作は、各リージョンで 'R' 個の RU を取得することが保証されます。 特定のリージョンに RU を選択的に割り当てることはできません。 Cosmos コンテナー (またはデータベース) 用にプロビジョニングされた RU は、Cosmos アカウントに関連付けられているすべてのリージョンに対してプロビジョニングされます。

Cosmos コンテナーに 'R' 個の RU が構成され、 Cosmos アカウントに 'N' 個のリージョンが関連付けられていると仮定すると、次のようになります。

- Cosmos アカウントに単一の書き込みリージョンが構成されている場合、コンテナーでグローバルに利用可能な RU の合計は R x N 個です。

- Cosmos アカウントに複数の書き込みリージョンが構成されている場合、コンテナーでグローバルに利用可能な RU の合計は R x (N+1) 個です。 追加の R 個の RU は、更新の競合およびリージョン間でのアンチエントロピ トラフィックを処理するために自動的にプロビジョニングされます。

選択した[整合性モデル](consistency-levels.md)もスループットに影響します。 "セッション"、"一貫性のあるプレフィックス"、"最終的" の整合性では、"有界整合性制約" または "厳密" の場合と比べてほぼ 2 倍の読み取りスループットを得ることができます。

## <a name="next-steps"></a>次の手順

次の記事を使用して、スループットを構成する方法を学習できます。

* [コンテナーとデータベースのスループットの取得と設定](set-throughput.md) 

