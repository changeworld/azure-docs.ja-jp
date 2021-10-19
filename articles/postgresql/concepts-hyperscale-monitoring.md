---
title: 監視とチューニング - Hyperscale (Citus) - Azure Database for PostgreSQL
description: この記事では、Azure Database for PostgreSQL (Hyperscale (Citus)) の監視およびチューニング機能について説明します。
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: conceptual
ms.date: 10/05/2021
ms.openlocfilehash: 89fcd5ce46ea9093779ad24dcca5a80a3a4f6ada
ms.sourcegitcommit: 1d56a3ff255f1f72c6315a0588422842dbcbe502
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/06/2021
ms.locfileid: "129618330"
---
# <a name="monitor-and-tune-azure-database-for-postgresql---hyperscale-citus"></a>Azure Database for PostgreSQL の監視とチューニング - Hyperscale (Citus)

サーバーに関する監視データは、ワークロードをトラブルシューティングしたり最適化したりするのに役立ちます。 Hyperscale (Citus) には、サーバー グループ内のノードの動作を分析するためのさまざまな監視オプションが用意されています。

## <a name="metrics"></a>メトリック

Hyperscale (Citus) からは、サーバー グループに含まれるノードのメトリックと、全体としてのグループの集計メトリックが提供されます。 メトリックを使うと、サポート リソースの動作を分析できます。 各メトリックは 1 分間隔で出力されます。履歴は最大 30 日分です。

メトリックのグラフを表示するだけでなく、アラートを構成することもできます。 詳細な手順については、[アラートの設定方法](howto-hyperscale-alert-on-metric.md)に関する記事をご覧ください。  その他のタスクとして、自動化されたアクションの設定、高度な分析の実行、履歴のアーカイブなどがあります。 詳細については、[Azure のメトリックの概要](../azure-monitor/data-platform.md)に関する記事をご覧ください。

### <a name="per-node-vs-aggregate"></a>ノードあたりと集計

既定では、Azure portal では、サーバー グループ内のノード全体で Hyperscale (Citus) メトリックが収集されます。 ただし、ディスク使用量 (%) など、一部のメトリックはノードあたりを基準にした場合に情報の有益性が上がります。 ノードのメトリックを個別に表示するには、サーバー名を基準に Azure Monitor [メトリック分割](howto-hyperscale-monitoring.md#view-metrics-per-node)を使用します。

> [!NOTE]
>
> 一部の Hyperscale (Citus) サーバー グループでは、メトリック分割をサポートしていません。 そのようなサーバー グループでは、サーバー グループの **[概要]** ページでノード名をクリックすることで個々のノードのメトリックを表示できます。 次に、ノードの **[メトリック]** ページを開きます。

### <a name="list-of-metrics"></a>メトリックの一覧

これらのメトリックは、Hyperscale (Citus) ノードで使用できます。

|メトリック|メトリックの表示名|ユニット|説明|
|---|---|---|---|
|active_connections|アクティブな接続|Count|サーバーへのアクティブな接続の数|
|cpu_percent|CPU 使用率|Percent|使用されている CPU の割合|
|iops|IOPS|Count|[IOPS 定義](../virtual-machines/premium-storage-performance.md#iops)と [Hyperscale (Citus) スループット](concepts-hyperscale-configuration-options.md)に関する記事を参照してください|
|memory_percent|メモリの割合|Percent|使用されているメモリの割合|
|network_bytes_ingress|Network In|バイト|アクティブな接続全体のネットワーク受信。|
|network_bytes_egress|Network Out|バイト|アクティブな接続全体のネットワーク送信。|
|storage_percent|ストレージの割合|Percent|サーバーの最大数のうち使用されているストレージの割合|
|storage_used|使用済みストレージ|バイト|使用されているストレージの量。 サービスで使用されるストレージには、データベース ファイル、トランザクション ログ、サーバー ログが含まれることがあります。|

Azure にはクラスター全体の集計メトリックは用意されていませんが、複数のノードのメトリックを同じグラフに配置することができます。

## <a name="next-steps"></a>次のステップ

- Hyperscale (Citus) サーバー グループの[メトリックを表示する](howto-hyperscale-monitoring.md)方法について確認します。
- メトリックに対するアラートの作成のガイダンスについては、[アラートを設定する方法](howto-hyperscale-alert-on-metric.md)に関するページをご覧ください。
- サーバー グループ内でノードあたりのメトリックを調べる目的で[メトリック分割](../azure-monitor/essentials/metrics-charts.md#metric-splitting)する方法について学習します。
