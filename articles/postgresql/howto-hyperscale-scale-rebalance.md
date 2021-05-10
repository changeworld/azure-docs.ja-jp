---
title: シャードの再調整 - Hyperscale (Citus) - Azure Database for PostgreSQL
description: サーバー間でシャードを均等に分散してパフォーマンスを向上させる
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: how-to
ms.date: 04/09/2021
ms.openlocfilehash: 63322fac4c6ad5b705deedcd8a80466ddd803814
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/13/2021
ms.locfileid: "107305703"
---
# <a name="rebalance-shards-in-hyperscale-citus-server-group"></a>Hyperscale (Citus) サーバー グループでシャードを再調整する

新しく追加したノードを利用するには、分散テーブルの[シャード](concepts-hyperscale-distributed-data.md#shards)を再調整する必要があります。これは、既存のノードから新しいノードにいくつかのシャードを移動することを意味します。

## <a name="determine-if-the-server-group-needs-a-rebalance"></a>サーバー グループの再調整が必要かどうかを判断する

Azure portal では、サーバー グループ内のワーカー ノード間でデータが均等に分散されているかどうかがわかります。 これを確認するには、 **[Server group management]\(サーバー グループ管理\)** メニューの **[シャード リバランサー]** ページに移動します。 ワーカー間でデータがずれている場合は、各ノードのサイズの一覧と共に、**再調整を推奨** するメッセージが表示されます。

データの調整が取れている場合、**この時点では再調整は推奨されない** ことを示すメッセージが表示されます。

## <a name="run-the-shard-rebalancer"></a>シャード リバランサーを実行する

シャード リバランサーを起動するには、サーバー グループのコーディネーター ノードに接続し、分散テーブルで [rebalance_table_shards](reference-hyperscale-functions.md#rebalance_table_shards) SQL 関数を実行する必要があります。 関数により、引数に指定したテーブルの[コロケーション](concepts-hyperscale-colocation.md) グループ内にあるすべてのテーブルが再調整されます。 したがって、すべての分散テーブルに対してその関数を呼び出す必要はありません。各コロケーション グループの代表的なテーブルに対して呼び出すだけです。

```sql
SELECT rebalance_table_shards('distributed_table_name');
```

## <a name="monitor-rebalance-progress"></a>再調整の進行状況を監視する

リバランサーを再起動後に監視するには、Azure portal に戻ります。 **[Server group management]\(サーバー グループ管理\)** の **[シャード リバランサー]** ページを開きます。 2 つのテーブルと共に、**再調整が進行中** であることが示すメッセージが表示されます。

最初のテーブルは、ノードとの間を移動するシャードの数を示します (24 個中 6 個が移動した、など)。 2 番目のテーブルは、データベース テーブルごとの進行状況を、名前、影響を受けるシャード数、影響を受けるデータのサイズ、および再調整の状態と共に示します。

**[最新の情報に更新]** ボタンを選択してページを更新します。 再調整が完了すると、**この時点では再調整は推奨されない** ことを示すメッセージが再び表示されます。

## <a name="next-steps"></a>次の手順

- サーバー グループの[パフォーマンス オプション](concepts-hyperscale-configuration-options.md)の詳細を確認します。
- [サーバー グループをスケールアップまたはスケールアウト](howto-hyperscale-scale-grow.md)する
- [rebalance_table_shards](reference-hyperscale-functions.md#rebalance_table_shards) リファレンス資料を参照する
