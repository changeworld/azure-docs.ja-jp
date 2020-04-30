---
title: サーバー グループをスケーリングする - Hyperscale (Citus) - Azure Database for PostgreSQL
description: 負荷の増加に対処するためにサーバー グループのメモリ、ディスク、CPU リソースを調整する
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: conceptual
ms.date: 3/16/2020
ms.openlocfilehash: f8e8e1672f754e843a3bd1c75d496599d31e5f11
ms.sourcegitcommit: b9d4b8ace55818fcb8e3aa58d193c03c7f6aa4f1
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/29/2020
ms.locfileid: "82584008"
---
# <a name="scale-a-hyperscale-citus-server-group"></a>Hyperscale (Citus) サーバー グループをスケーリングする

Azure Database for PostgreSQL - Hyperscale (Citus) では、増加した負荷に対処するためにセルフサービス スケーリングを行うことができます。 Azure portal を使用すると、簡単に新しいワーカー ノードを追加したり既存ノードの仮想コアを増やしたりできます。

## <a name="add-worker-nodes"></a>ワーカー ノードの追加

ノードを追加するには、Hyperscale (Citus) サーバー グループの **[構成]** タブに移動します。  **[ワーカー ノードの数]** のスライダーをドラッグすると、値が変化します。

![リソースのスライダー](./media/howto-hyperscale-scaling/01-sliders-workers.png)

**[保存]** ボタンをクリックして、変更した値を有効にします。

> [!NOTE]
> ワーカー ノードの数は、いったん増やして保存すると、スライダーを使用して減らすことはできません。

### <a name="rebalance-shards"></a>シャードの再調整

新しく追加したノードを利用するには、分散テーブルの[シャード](concepts-hyperscale-distributed-data.md#shards)を再調整する必要があります。これは、既存のノードから新しいノードにいくつかのシャードを移動することを意味します。 まず、新しいワーカーがプロビジョニングを正常に完了したことを確認します。 次に、シャードを再調整するバランサーを開始します。そのためには、psql を使用してクラスター コーディネーター ノードに接続し、以下を実行します。

```sql
SELECT rebalance_table_shards('distributed_table_name');
```

`rebalance_table_shards` 関数により、引数に指定したテーブルの[コロケーション](concepts-hyperscale-colocation.md) グループ内にあるすべてのテーブルが再調整されます。 したがって、すべての分散テーブルに対してその関数を呼び出す必要はありません。各コロケーション グループの代表的なテーブルに対して呼び出すだけです。

## <a name="increase-or-decrease-vcores-on-nodes"></a>ノードの仮想コアを増減させる

> [!NOTE]
> 現在、この機能はプレビュー段階にあります。 サーバー グループのノードを対象に仮想コアの変更を要求するには、[Azure サポートにお問い合わせください](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)。

新しいノードを追加するだけでなく、既存のノードの能力を増強することもできます。 計算処理能力の調整 (増減) は、パフォーマンス実験のほか、トラフィック需要の短期的変更または長期的変更に役立つ場合があります。

すべてのワーカー ノードの仮想コアを変更するには、 **[構成 (ワーカー ノードあたり)]** の下の **[仮想コア]** スライダーを調整します。 コーディネーター ノードの仮想コアは、個別に調整することができます。 **[コーディネーター ノード]** の **[構成の変更]** リンクをクリックします。 コーディネーターの仮想コアとストレージ容量に対するスライダーを備えたダイアログが表示されます。 必要に応じてスライダーを調整し、 **[OK]** を選択してください。

## <a name="next-steps"></a>次のステップ

サーバー グループの[パフォーマンス オプション](concepts-hyperscale-configuration-options.md)の詳細を確認します。
