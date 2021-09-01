---
title: サーバー グループをスケーリングする - Hyperscale (Citus) - Azure Database for PostgreSQL
description: 負荷の増加に対処するためにサーバー グループのメモリ、ディスク、CPU リソースを調整する
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: how-to
ms.date: 08/03/2021
ms.openlocfilehash: 12c4cd7848b0d58fd6b91e27e254ccc613ff5676
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/13/2021
ms.locfileid: "121751911"
---
# <a name="scale-a-hyperscale-citus-server-group"></a>Hyperscale (Citus) サーバー グループをスケーリングする

Azure Database for PostgreSQL - Hyperscale (Citus) では、増加した負荷に対処するためにセルフサービス スケーリングを行うことができます。 Azure portal を使用すると、簡単に新しいワーカー ノードを追加したり既存ノードの仮想コアを増やしたりできます。 ノードを追加するとダウンタイムが発生しなくなります。また、クエリを中断させることなく、新しいノードにシャードを移動 ([シャードの再調整](howto-hyperscale-scale-rebalance.md)と呼ばれます) することも可能です。

## <a name="add-worker-nodes"></a>ワーカー ノードの追加

ノードを追加するには、ハイパースケール (Citus) サーバー グループの **[コンピューティングとストレージ]** タブに移動します。  **[ワーカー ノードの数]** のスライダーをドラッグすると、値が変化します。

> [!NOTE]
>
> [Basic レベル](concepts-hyperscale-tiers.md)で作成された Hyperscale (Citus) サーバー グループにはワーカーが備わっていません。 ワーカーの数を増やすと、サーバー グループは自動的に Standard レベルに移行されます。  サーバー グループを Standard レベルに移行した後、Basic レベルにダウングレードすることはできません。

:::image type="content" source="./media/howto-hyperscale-scaling/01-sliders-workers.png" alt-text="リソースのスライダー":::

**[保存]** ボタンをクリックして、変更した値を有効にします。

> [!NOTE]
> ワーカー ノードの数は、いったん増やして保存すると、スライダーを使用して減らすことはできません。

> [!NOTE]
> 新しく追加したノードを利用するには、[分散テーブルのシャードを再調整する](howto-hyperscale-scale-rebalance.md)必要があります。つまり、既存のノードから新しいノードにいくつかの[シャード](concepts-hyperscale-distributed-data.md#shards)を移動します。 再調整はバックグラウンドで実行可能で、ダウンタイムは必要ありません。

## <a name="increase-or-decrease-vcores-on-nodes"></a>ノードの仮想コアを増減させる

新しいノードを追加するだけでなく、既存のノードの能力を増強することもできます。 計算処理能力の調整 (増減) は、パフォーマンス実験のほか、トラフィック需要の短期的変更または長期的変更に役立つ場合があります。

すべてのワーカー ノードの仮想コアを変更するには、 **[構成 (ワーカー ノードあたり)]** の下の **[仮想コア]** スライダーを調整します。 コーディネーター ノードの仮想コアは、個別に調整することができます。 **[構成 (コーディネーター ノード)]** の **[仮想コア]** スライダーを調整します。

## <a name="increase-storage-on-nodes"></a>ノードの記憶域を増やす

新しいノードを追加するだけでなく、既存のノードのディスク領域を増強することもできます。 ディスク領域を増やすと、ワーカー ノードを追加する前に、既存のワーカー ノードでさらに多くのことができるようになります。

すべてのワーカー ノードのストレージを変更するには、 **[構成 (ワーカー ノードあたり)]** の下の **[ストレージ]** スライダーを調整します。 コーディネーター ノードのストレージは、個別に調整することができます。 **[構成 (コーディネーター ノード)]** の **[ストレージ]** スライダーを調整します。

> [!NOTE]
> ノードあたりのストレージの数は、いったん増やして保存すると、スライダーを使用して減らすことはできません。

## <a name="next-steps"></a>次の手順

- サーバー グループの[パフォーマンス オプション](concepts-hyperscale-configuration-options.md)の詳細を確認します。
- すべてのワーカー ノードが並列クエリに参加できるように[分散テーブル シャードを再調整する](howto-hyperscale-scale-rebalance.md)
