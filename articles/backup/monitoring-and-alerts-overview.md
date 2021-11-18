---
title: Azure Backup の監視とレポートのソリューション
description: Azure Backup に用意されているさまざまな監視およびレポート ソリューションについて説明します。
ms.topic: conceptual
ms.date: 10/23/2021
ms.openlocfilehash: 3e289e4b657f5bb3a1e84d70b9af14f06b8f12f0
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/17/2021
ms.locfileid: "132714183"
---
# <a name="monitoring-and-reporting-solutions-for-azure-backup"></a>Azure Backup の監視とレポートのソリューション

この記事では、監視とレポートに関して Azure Backup に用意されているさまざまなソリューションについて説明します。

Azure Backup は、Azure Resource Graph、Azure Monitor アラート、Azure Monitor ログ、Azure Resource Health など各種の Azure サービスとの統合に対応しています。 また、Azure Backup には、Azure portal、Azure PowerShell、Azure CLI、Azure REST API など各種 Azure クライアントとのインターフェイスも用意されています。 監視とレポートの要件に応じて、これらの統合を組み合わせて使用することができます。

## <a name="monitoring-and-reporting-scenarios"></a>監視とレポートのシナリオ

次の表は、エンタープライズ バックアップのデプロイでよく見られるさまざまな監視とレポートのシナリオをまとめたものです。現在 Azure Backup に用意されている各種機能、そして関連するリファレンス ドキュメントがシナリオごとに記載されています。

| シナリオ | 使用可能なソリューション |
| --- | --- |
| バックアップ ジョブとバックアップ インスタンスを監視する | <ul><li>**組み込みの監視**: [[バックアップ センター]](./backup-center-overview.md) ダッシュボード経由で、バックアップ ジョブとバックアップ インスタンスをリアルタイムに監視できます。</li><li>**監視ダッシュボードのカスタマイズ**: Azure Backup では、ポータル以外のクライアント ([PowerShell](./backup-azure-vms-automation.md)、[CLI](./create-manage-azure-services-using-azure-command-line-interface.md)、[REST API](./backup-azure-arm-userestapi-managejobs.md) など) を使用して、カスタム ダッシュボードで使用するバックアップ監視データに対するクエリを実行できます。  <br><br>  また、[Azure Resource Graph (ARG)](./query-backups-using-azure-resource-graph.md) を使用すれば、(コンテナー、サブスクリプション、リージョン、Lighthouse テナントの全体で) 広範囲にバックアップのクエリを実行できます。    <br><br>    監視ブックの一例である[バックアップ エクスプローラー](./monitor-azure-backup-with-backup-explorer.md)には ARG のデータが使用されていて、それを自身のダッシュボードを作成する際のリファレンスとして使用することができます。 </li></ul> |
| 全体的なバックアップの正常性を監視する      |   <ul><li>**リソース正常性**: Recovery Services コンテナーの正常性を監視し、リソース正常性の問題を引き起こしているイベントのトラブルシューティングを行えます。 [詳細については、こちらを参照してください](../service-health/resource-health-overview.md)。   <br><br>   正常性の履歴を確認することで、リソースの正常性に影響を及ぼしているイベントを突き止めることができます。 リソース正常性イベントに関連したアラートをトリガーすることもできます。  </li><li>**Azure Monitor メトリック**: Azure Backup はまた、Azure Monitor を介して上記の正常性メトリックを提供します。これにより、バックアップの正常性に関する詳細情報が提供されます。 これにより、これらのメトリックに対してアラートと通知を構成することもできます。 [詳細情報](./metrics-overview.md)</li></ul>  |
| クリティカルなバックアップ インシデントに関するアラートを受け取る     |  <ul><li>**Azure Monitor (プレビュー) を使用した組み込みのアラート**: Azure Backup には、バックアップ データの削除、論理的な削除の無効化、バックアップ エラー、復元エラーなどのシナリオに向けて、[Azure Monitor に基づくアラート ソリューション](./backup-azure-monitoring-built-in-monitor.md#azure-monitor-alerts-for-azure-backup-preview)が用意されています。    <br><br>  これらのアラートは、バックアップ センターで表示、管理することができます。 これらのアラートの[通知を構成](./backup-azure-monitoring-built-in-monitor.md#configuring-notifications-for-alerts) (メールなど) したい場合は、Azure Monitor の [[アクション ルール]](../azure-monitor/alerts/alerts-action-rules.md?tabs=portal) と [[アクション グループ]](../azure-monitor/alerts/action-groups.md) を使用することで、多様な通知チャネルにアラートをルーティングできます。  </li> <li> **Azure Monitor を使用した Azure Backup メトリック アラート (プレビュー)** : Azure Monitor メトリックを使用して、さまざまな KPI にわたってバックアップ項目の正常性を監視するカスタム アラート ルールを作成できます。 [詳細情報](./metrics-overview.md) </li> <li>**クラシック アラート**: [Recovery Services コンテナー] ブレードの [[バックアップ アラート] タブからアクセス](./backup-azure-monitoring-built-in-monitor.md#backup-alerts-in-recovery-services-vault)するアラート ソリューションであり、最新ではありません。 これらのアラートをバックアップ センターで表示することはできません。 クラシック アラートを使用する場合はまず、先見的なアラート ソリューションである Azure Monitor ベースのアラート ソリューション (前述) を 1 つ以上使用することをお勧めします。 </li><li>**カスタム ログ アラート**: カスタム ロジックに基づいてアラートを生成する必要があるシナリオの場合、[Log Analytics ベースのアラート](./backup-azure-monitoring-use-azuremonitor.md#create-alerts-by-using-log-analytics)を利用することができます。このようなシナリオでは、診断データを Log Analytics (LA) ワークスペースに送信するようにコンテナーが構成されていることが条件となります。 現時点での [LA ワークスペースのデータ更新頻度](./backup-azure-monitoring-use-azuremonitor.md#diagnostic-data-update-frequency)の関係上、このソリューションは主に、実際のインシデントが発生してからアラートが生成されるまでの間に小さなタイム ラグが許容されるシナリオが使用対象となります。 </li></ul>     |
| 過去の傾向を分析する        |     <ul><li>**組み込みのレポート**: (Azure Monitor ログに基づく) [バックアップ レポート](./configure-reports.md)を使用してジョブの成功とバックアップの利用状況に関連した過去の傾向を分析することで、バックアップの最適化の機会を発見することができます。 これらのレポートの[定期的なメールを構成](./backup-reports-email.md)することもできます。 </li><li>**レポート ダッシュボードのカスタマイズ**: [システム関数](./backup-reports-system-functions.md)のドキュメントを使用して Azure Monitor ログ (LA) のデータにクエリを実行することで、バックアップに関連した履歴情報を分析する独自のダッシュボードを作成することもできます。</li></ul>    |
| ユーザーによってトリガーされたコンテナー アクションを監査する    |       **アクティビティ ログ**: バックアップ ポリシーの変更、バックアップ項目の復元など、ユーザーによってトリガーされたさまざまなアクションは、コンテナーの標準の [アクティビティ ログ](../azure-monitor/essentials/activity-log.md)を使用して確認できます。 アクティビティ ログに関するアラートを構成したり、長期保有を目的にそれらのログを Log Analytics ワークスペースにエクスポートしたりすることもできます。 |

## <a name="next-steps"></a>次のステップ

- バックアップ センターの[詳細情報](./backup-center-overview.md)。
- Azure Monitor アラートの[詳細情報](./backup-azure-monitoring-built-in-monitor.md#azure-monitor-alerts-for-azure-backup-preview)。
- Azure Resource Health の[詳細情報](../service-health/resource-health-overview.md)。