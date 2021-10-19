---
title: メトリックの表示方法 – Hyperscale (Citus) - Azure Database for PostgreSQL
description: Azure Database for PostgreSQL - Hyperscale (Citus) のデータベース メトリックにアクセスする方法
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: how-to
ms.date: 10/05/2021
ms.openlocfilehash: b698a69631ba2ad8aba58f6de17d8cbee1cbafaf
ms.sourcegitcommit: 1d56a3ff255f1f72c6315a0588422842dbcbe502
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/06/2021
ms.locfileid: "129620842"
---
# <a name="how-to-view-metrics-in-azure-database-for-postgresql---hyperscale-citus"></a>Azure Database for PostgreSQL - Hyperscale (Citus) のメトリックを表示する方法

リソース メトリックは、Hyperscale (Citus) サーバー グループのあらゆるノードで利用できます。また、リソース メトリックはノード全体で集計されます。

## <a name="view-metrics"></a>メトリックを表示する

Hyperscale (Citus) サーバーのメトリックにアクセスするには、Azure portal で **[監視]** の下にある **[メトリック]** を開きます。

:::image type="content" source="media/howto-hyperscale-monitoring/metrics.png" alt-text="メトリック画面":::

ディメンションと集計、たとえば、 **[CPU 割合]** と **[最大]** を選択すると、すべてのノードを対象に集計されたメトリックが表示されます。 各メトリックの説明については、[こちら](concepts-hyperscale-monitoring.md#list-of-metrics)を参照してください。

:::image type="content" source="media/howto-hyperscale-monitoring/dimensions.png" alt-text="[ディメンションの選択]":::

### <a name="view-metrics-per-node"></a>ノードごとにメトリックを表示する

各ノードのメトリックを同じグラフで個別に表示することを "分割" と呼びます。
これを有効にするには、 **[Apply splitting]\(分割の適用\)** を選択します。

:::image type="content" source="media/howto-hyperscale-monitoring/splitting.png" alt-text="[Apply splitting]\(分割の適用\) ボタン":::

分割する値を選択します。 Hyperscale (Citus) ノードの場合、 **[サーバー名]** を選択します。

:::image type="content" source="media/howto-hyperscale-monitoring/split-value.png" alt-text="分割する値を選択する":::

メトリックはこれで、ノードごとに 1 つの色分けされた線でプロットされます。

:::image type="content" source="media/howto-hyperscale-monitoring/split-chart.png" alt-text="複数ノードのグラフ線":::

## <a name="next-steps"></a>次の手順

* Hyperscale (Citus) の[監視の概念](concepts-hyperscale-monitoring.md)を確認する
