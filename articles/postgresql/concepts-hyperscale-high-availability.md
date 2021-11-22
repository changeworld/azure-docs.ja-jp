---
title: 高可用性 - Hyperscale (Citus) - Azure Database for PostgreSQL
description: 高可用性とディザスター リカバリーの概念
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: conceptual
ms.date: 11/15/2021
ms.openlocfilehash: d708bb832673d424b1737ad0bea00716f1f3f278
ms.sourcegitcommit: 2ed2d9d6227cf5e7ba9ecf52bf518dff63457a59
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/16/2021
ms.locfileid: "132524856"
---
# <a name="high-availability-in-azure-database-for-postgresql--hyperscale-citus"></a>Azure Database for PostgreSQL の高可用性 - Hyperscale (Citus)

高可用性 (HA) は、サーバー グループ内の各ノードのスタンバイ レプリカを維持することでデータベースのダウンタイムを回避する機能です。 ノードがダウンした場合、Hyperscale (Citus) は、障害が発生したノードからそのスタンバイに受信接続を切り替えます。 フェールオーバーは数分で完了し、昇格されたノードには、PostgreSQL の同期ストリーミング レプリケーションを通じて常に最新のデータが格納されます。

HA が有効になっていない場合でも、各ハイパースケール (Citus) ノードには、独自のローカル冗長ストレージ (LRS) が与えられ、3 つの同期レプリカが Azure Storage サービスによって管理されます。  1 つのレプリカが故障した場合、故障が Azure Storage サービスによって検出され、ユーザーを介さずに再作成されます。 LRS ストレージの持続性については、[こちらのページ](../storage/common/storage-redundancy.md#summary-of-redundancy-options)のメトリックを参照してください。

HA が有効に "*なっている*" とき、ハイパースケール (Citus) によってサーバー グループのプライマリ ノードごとにスタンバイ ノードが 1 つ実行されます。 プライマリとそのスタンバイでは、同期 PostgreSQL レプリケーションが使用されます。 このレプリケーションにより、プライマリ ノードが故障した場合、ダウンタウンが予測できます。 簡単に言えば、Microsoft サービスによってプライマリ ノードの故障が検出され、スタンバイ ノードにフェールオーバーされ、データ損失がゼロに抑えられます。

コーディネーター ノードで HA を利用するためには、接続の切断とトランザクションの失敗をデータベース アプリケーションが検出して再試行する必要があります。 新たに昇格されたコーディネーターには、同じ接続文字列でアクセスできます。

復旧は、検出、フェールオーバー、完全復旧の 3 つのステージに分けることができます。  すべてのノードは、Hyperscale (Citus) によって定期的に正常性チェックが実行されます。チェックで 4 回不合格になると、そのノードはダウンしていると判断されます。 その後 Hyperscale (Citus) は、スタンバイ ノードの状態をプライマリに昇格させ (フェールオーバー)、新しいスタンバイ候補をプロビジョニングします。
ストリーミング レプリケーションが開始されて、新しいノードが最新の状態になります。  すべてのデータのレプリケートが完了したとき、ノードは完全復旧の状態に達します。

### <a name="next-steps"></a>次のステップ

- Hyperscale (Citus) サーバー グループで[高可用性を有効にする](howto-hyperscale-high-availability.md)方法について学習します。
