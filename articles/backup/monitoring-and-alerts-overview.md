---
title: Azure Backup の監視とレポートのソリューション
description: Azure Backup に用意されているさまざまな監視およびレポート ソリューションについて説明します。
ms.topic: conceptual
ms.date: 09/10/2021
ms.openlocfilehash: 9045290c51d012db0346b4b2b81fa8ac88f22a69
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/24/2021
ms.locfileid: "128699253"
---
# <a name="monitoring-and-reporting-solutions-for-azure-backup"></a>Azure Backup の監視とレポートのソリューション

この記事では、監視とレポートに関して Azure Backup に用意されているさまざまなソリューションについて説明します。

Azure Backup は、Azure Resource Graph、Azure Monitor アラート、Azure Monitor ログ、Azure Resource Health など各種の Azure サービスとの統合に対応しています。 また、Azure Backup には、Azure portal、Azure PowerShell、Azure CLI、Azure REST API など各種 Azure クライアントとのインターフェイスも用意されています。 監視とレポートの要件に応じて、これらの統合を組み合わせて使用することができます。

## <a name="monitoring-and-reporting-scenarios"></a>監視とレポートのシナリオ

次の表は、エンタープライズ バックアップのデプロイでよく見られるさまざまな監視とレポートのシナリオをまとめたものです。現在 Azure Backup に用意されている各種機能、そして関連するリファレンス ドキュメントがシナリオごとに記載されています。

| シナリオ | 使用可能なソリューション |
| --- | --- |
| バックアップ ジョブとバックアップ インスタンスを監視する | <ul><li>**組み込みの監視**: [[バックアップ センター]](/azure/backup/backup-center-overview) ダッシュボード経由で、バックアップ ジョブとバックアップ インスタンスをリアルタイムに監視できます。</li><li>**監視ダッシュボードのカスタマイズ**: Azure Backup では、ポータル以外のクライアント ([PowerShell](/azure/backup/backup-azure-vms-automation)、[CLI](/azure/backup/create-manage-azure-services-using-azure-command-line-interface)、[REST API](/azure/backup/backup-azure-arm-userestapi-managejobs) など) を使用して、カスタム ダッシュボードで使用するバックアップ監視データに対するクエリを実行できます。  <br><br>  また、[Azure Resource Graph (ARG)](/azure/backup/query-backups-using-azure-resource-graph) を使用すれば、(コンテナー、サブスクリプション、リージョン、Lighthouse テナントの全体で) 広範囲にバックアップのクエリを実行できます。    <br><br>    監視ブックの一例である[バックアップ エクスプローラー](/azure/backup/monitor-azure-backup-with-backup-explorer)には ARG のデータが使用されていて、それを自身のダッシュボードを作成する際のリファレンスとして使用することができます。 </li></ul> |
| 全体的なバックアップの正常性を監視する      |   **リソース正常性**: Recovery Services コンテナーの正常性を監視し、リソース正常性の問題を引き起こしているイベントのトラブルシューティングを行えます。 [詳細については、こちらを参照してください](/azure/service-health/resource-health-overview)。   <br><br>   正常性の履歴を確認することで、リソースの正常性に影響を及ぼしているイベントを突き止めることができます。 リソース正常性イベントに関連したアラートをトリガーすることもできます。  |
| クリティカルなバックアップ インシデントに関するアラートを受け取る     |  <ul><li>**Azure Monitor (プレビュー) を使用した組み込みのアラート**: Azure Backup には、バックアップ データの削除、論理的な削除の無効化、バックアップ エラー、復元エラーなどのシナリオに向けて、[Azure Monitor に基づくアラート ソリューション](/azure/backup/backup-azure-monitoring-built-in-monitor#azure-monitor-alerts-for-azure-backup-preview)が用意されています。    <br><br>  これらのアラートは、バックアップ センターで表示、管理することができます。 これらのアラートの[通知を構成](/azure/backup/backup-azure-monitoring-built-in-monitor#configuring-notifications-for-alerts) (メールなど) したい場合は、Azure Monitor の [[アクション ルール]](/azure/azure-monitor/alerts/alerts-action-rules?tabs=portal) と [[アクション グループ]](/azure/azure-monitor/alerts/action-groups) を使用することで、多様な通知チャネルにアラートをルーティングできます。  </li><li>**クラシック アラート**: [Recovery Services コンテナー] ブレードの [[バックアップ アラート] タブからアクセス](/azure/backup/backup-azure-monitoring-built-in-monitor#backup-alerts-in-recovery-services-vault)するアラート ソリューションであり、最新ではありません。 これらのアラートをバックアップ センターで表示することはできません。 クラシック アラートを使用する場合はまず、先見的なアラート ソリューションである Azure Monitor ベースのアラート ソリューション (前述) の使用をお勧めします。 </li><li>**カスタム アラート**: カスタム ロジックに基づいてアラートを生成する必要があるシナリオの場合、[Log Analytics ベースのアラート](/azure/backup/backup-azure-monitoring-use-azuremonitor#create-alerts-by-using-log-analytics)を利用することができます。このようなシナリオでは、診断データを Log Analytics (LA) ワークスペースに送信するようにコンテナーが構成されていることが条件となります。 現時点での [LA ワークスペースのデータ更新頻度](/azure/backup/backup-azure-monitoring-use-azuremonitor#diagnostic-data-update-frequency)の関係上、このソリューションは主に、実際のインシデントが発生してからアラートが生成されるまでの間に小さなタイム ラグが許容されるシナリオが使用対象となります。 </li></ul>     |
| 過去の傾向を分析する        |     <ul><li>**組み込みのレポート**: (Azure Monitor ログに基づく) [バックアップ レポート](/azure/backup/configure-reports)を使用してジョブの成功とバックアップの利用状況に関連した過去の傾向を分析することで、バックアップの最適化の機会を発見することができます。 これらのレポートの[定期的なメールを構成](/azure/backup/backup-reports-email)することもできます。 </li><li>**レポート ダッシュボードのカスタマイズ**: [システム関数](/azure/backup/backup-reports-system-functions)のドキュメントを使用して Azure Monitor ログ (LA) のデータにクエリを実行することで、バックアップに関連した履歴情報を分析する独自のダッシュボードを作成することもできます。</li></ul>    |
| ユーザーによってトリガーされたコンテナー アクションを監査する    |       **アクティビティ ログ**: バックアップ ポリシーの変更、バックアップ項目の復元など、ユーザーによってトリガーされたさまざまなアクションは、コンテナーの標準の[アクティビティ ログ](/azure/azure-monitor/essentials/activity-log)を使用して確認できます。 アクティビティ ログに関するアラートを構成したり、長期保有を目的にそれらのログを Log Analytics ワークスペースにエクスポートしたりすることもできます。

## <a name="next-steps"></a>次のステップ

- バックアップ センターの[詳細情報](/azure/backup/backup-center-overview)。
- Azure Monitor アラートの[詳細情報](/azure/backup/backup-azure-monitoring-built-in-monitor#azure-monitor-alerts-for-azure-backup-preview)。
- Azure Resource Health の[詳細情報](/azure/service-health/resource-health-overview)。
