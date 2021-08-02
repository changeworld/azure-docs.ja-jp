---
title: 読み取り専用アクセスのトラブルシューティング - Hyperscale (Citus) - Azure Database for PostgreSQL
description: Hyperscale (Citus) サーバー グループが読み取り専用になる理由と対処法について説明する
keywords: postgresql 接続、読み取り専用
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: how-to
ms.date: 6/4/2021
ms.openlocfilehash: 13d0c630c38e71b243a32404b26e3a141a20a6b2
ms.sourcegitcommit: 832e92d3b81435c0aeb3d4edbe8f2c1f0aa8a46d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/07/2021
ms.locfileid: "111561765"
---
# <a name="troubleshoot-read-only-access-to-azure-database-for-postgresql---hyperscale-citus"></a>Azure Database for PostgreSQL - Hyperscale (Citus) への読み取り専用アクセスのトラブルシューティング

PostgreSQL は、空きディスク領域がないマシンでは実行できません。 PostgreSQL サーバーへのアクセスを維持するには、ディスク領域が不足しないようにする必要があります。

Hyperscale (Citus) では、ディスクがほぼいっぱいになった時点で、ノードが読み取り専用 (RO) 状態に設定されます。 書き込みを禁止することで、ディスクがこれ以上いっぱいになるのを防ぎ、ノードを読み取り可能な状態に保ちます。 読み取り専用状態の間に、より多くのディスク領域を解放するための対策を講じることができます。

具体的には、Hyperscale (Citus) ノードは、空きストレージが 5 GiB 未満になると、読み取り専用になります。 サーバーが読み取り専用になると、既存のすべてのセッションが切断され、コミットされていないトランザクションがロールバックされます。 書き込み操作とトランザクション コミットはすべて失敗しますが、読み取りクエリは引き続き動作します。

## <a name="ways-to-recover-write-access"></a>書き込みアクセスを回復する方法

### <a name="on-the-coordinator-node"></a>コーディネーター ノードの場合

* コーディネーター ノードの[ストレージ サイズを増やす](howto-hyperscale-scale-grow.md#increase-storage-on-nodes)、および/または
* ローカル テーブルをワーカー ノードに分散するか、データを削除します。 どちらのオプションでも、データベースに接続した後、他のコマンドを実行する前に `SET SESSION CHARACTERISTICS AS TRANSACTION READ WRITE` を実行する必要があります。

### <a name="on-a-worker-node"></a>ワーカー ノードの場合

* ワーカー ノードの[ストレージ サイズを増やす](howto-hyperscale-scale-grow.md#increase-storage-on-nodes)、および/または
* 他のノードに[データを再調整する](howto-hyperscale-scale-rebalance.md)か、一部のデータを削除します。
    * どちらのオプションでも、ワーカー ノードを一時的に読み取り/書き込みとして設定する必要があります。 これを行うには、サポート リクエストを送信します。 または、プレビューの Hyperscale (Citus) サーバー グループを実行している場合は、ワーカー ノードには直接接続でき、コーディネーター ノードには前述のように `SET SESSION CHARACTERISTICS` を使用できます。

## <a name="prevention"></a>防止

サーバーのストレージがしきい値に近づいたときに通知するアラートを設定することをお勧めします。 そうすることで、読み取り専用状態にならないように早めに行動できます。 詳細については、[推奨されるアラート](howto-hyperscale-alert-on-metric.md#suggested-alerts)に関するドキュメントを参照してください。

## <a name="next-steps"></a>次のステップ

* 事前通知用に [Azure アラートを設定](howto-hyperscale-alert-on-metric.md#suggested-alerts)して、読み取り専用状態に達する前にアクションを実行できるようにする。
* PostgreSQL のドキュメントで[ディスク使用量](https://www.postgresql.org/docs/current/diskusage.html)について学ぶ。
* PostgreSQL のドキュメントで[セッションの特性](https://www.postgresql.org/docs/13/sql-set-transaction.html)について学ぶ。
