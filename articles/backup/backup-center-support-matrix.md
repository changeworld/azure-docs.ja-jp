---
title: バックアップ センターのサポート マトリックス
description: この記事では、バックアップ センターがワークロードの種類ごとにサポートするシナリオを要約しています
ms.topic: conceptual
ms.date: 10/20/2021
ms.openlocfilehash: 602884f3cf077e53061bd02dc5235ddbe3073075
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/02/2021
ms.locfileid: "131033553"
---
# <a name="support-matrix-for-backup-center"></a>バックアップ センターのサポート マトリックス

バックアップ センターは、企業が[バックアップを大規模に管理、監視、操作、分析する](backup-center-overview.md)ための 1 つのウィンドウを提供します。 この記事では、バックアップ センターがワークロードの種類ごとにサポートするシナリオを要約しています。

## <a name="supported-scenarios"></a>サポートされるシナリオ

| **カテゴリ** | **シナリオ**  | **サポートされるワークロード**  | **制限** |
| -------------| ------------- | ----------------------- |------------|
| 監視   | すべてのジョブの表示 | <li> Azure Virtual Machine <br><br> <li> Azure Database for PostgreSQL サーバー <br><br> <li> Azure VM 内の SQL <br><br> <li> Azure VM 内の SAP HANA <br><br> <li> Azure Files<br/><br/> <li>Azure BLOB<br/><br/> <li>Azure Managed Disks | <li> すぐに使用できるのは 7 日分のジョブ。 <br> <li> 各フィルター/ドロップダウンでは最大 1000 項目がサポートされます。 そのため、バックアップ センターを使用して、テナントにまたがる最大 1000 のサブスクリプションと 1000 のコンテナーを監視できます。 |
| 監視 | すべてのバックアップ インスタンスの表示 | <li> Azure Virtual Machine <br><br> <li> Azure Database for PostgreSQL サーバー <br><br> <li> Azure VM 内の SQL <br><br> <li> Azure VM 内の SAP HANA <br><br> <li> Azure Files<br/><br/> <li>Azure BLOB<br/><br/> <li>Azure Managed Disks | 同上 |
| 監視 | すべてのバックアップ ポリシーの表示 | <li> Azure Virtual Machine <br><br> <li> Azure Database for PostgreSQL サーバー <br><br> <li> Azure VM 内の SQL <br><br> <li> Azure VM 内の SAP HANA <br><br> <li> Azure Files<br/><br/> <li>Azure BLOB<br/><br/> <li>Azure Managed Disks | 同上 |
| 監視 | すべてのコンテナーの表示 | <li> Azure Virtual Machine <br><br> <li> Azure Database for PostgreSQL サーバー <br><br> <li> Azure VM 内の SQL <br><br> <li> Azure VM 内の SAP HANA <br><br> <li> Azure Files<br/><br/> <li>Azure BLOB<br/><br/> <li>Azure Managed Disks | 同上 |
| 監視 | 大規模な Azure Monitor アラートの表示 | <li> Azure Virtual Machine <br><br> <li> Azure Database for PostgreSQL サーバー <br><br> <li> Azure VM 内の SQL <br><br> <li> Azure VM 内の SAP HANA <br><br> <li> Azure Files<br/><br/> <li>Azure BLOB<br/><br/> <li>Azure Managed Disks | [アラート](./backup-azure-monitoring-built-in-monitor.md#azure-monitor-alerts-for-azure-backup-preview)に関するドキュメントを参照 |
| 監視     |   Azure Backup メトリックの表示とメトリック アラート ルールの書き込み | <li>Azure VM   </li><li>Azure VM 内の SQL </li><li> Azure VM 内の SAP HANA</li><li>Azure Files </li> |   リージョンとサブスクリプションのすべての Recovery Services コンテナーのメトリックを同時に表示できます。 現在、さらに大きなスコープのメトリックを Azure portal で表示することは、サポートされていません。 メトリック アラート ルールの構成にも、同じ制限が適用されます。 詳細については、「[Azure portal でメトリックを表示する](metrics-overview.md#view-metrics-in-the-azure-portal)」を参照してください。|
| アクション | バックアップの構成 | <li> Azure Virtual Machine <br><br> <li> Azure Database for PostgreSQL サーバー <br><br> <li> Azure VM 内の SQL <br><br> <li> Azure VM 内の SAP HANA <br><br> <li> Azure Files<br/><br/> <li>Azure BLOB<br/><br/> <li>Azure Managed Disks | [Azure VM バックアップ](./backup-support-matrix-iaas.md)および[Azure Database for PostgreSQL サーバーのバックアップ](backup-azure-database-postgresql-overview.md#support-matrix)のサポート マトリックスを参照 |
| アクション | バックアップ インスタンスの復元 | <li> Azure Virtual Machine <br><br> <li> Azure Database for PostgreSQL サーバー <br><br> <li> Azure VM 内の SQL <br><br> <li> Azure VM 内の SAP HANA <br><br> <li> Azure Files<br/><br/> <li>Azure BLOB<br/><br/> <li>Azure Managed Disks | [Azure VM バックアップ](./backup-support-matrix-iaas.md)および[Azure Database for PostgreSQL サーバーのバックアップ](backup-azure-database-postgresql-overview.md#support-matrix)のサポート マトリックスを参照 |
| アクション | [コンテナーの作成] | <li> Azure Virtual Machine <br><br> <li> Azure Database for PostgreSQL サーバー <br><br> <li> Azure VM 内の SQL <br><br> <li> Azure VM 内の SAP HANA <br><br> <li> Azure Files<br/><br/> <li>Azure BLOB<br/><br/> <li>Azure Managed Disks | [Recovery Services コンテナー](./backup-support-matrix.md#vault-support)のサポート マトリックスを参照 |
| アクション | バックアップ ポリシーを作成する | <li> Azure Virtual Machine <br><br> <li> Azure Database for PostgreSQL サーバー <br><br> <li> Azure VM 内の SQL <br><br> <li> Azure VM 内の SAP HANA <br><br> <li> Azure Files<br/><br/> <li>Azure BLOB<br/><br/> <li>Azure Managed Disks | [Recovery Services コンテナー](./backup-support-matrix.md#vault-support)のサポート マトリックスを参照 |
| アクション | バックアップ インスタンスのオンデマンド バックアップの実行 | <li> Azure Virtual Machine <br><br> <li> Azure Database for PostgreSQL サーバー <br><br> <li> Azure VM 内の SQL <br><br> <li> Azure VM 内の SAP HANA <br><br> <li> Azure Files<br/><br/> <li>Azure BLOB<br/><br/> <li>Azure Managed Disks | [Azure VM バックアップ](./backup-support-matrix-iaas.md)および[Azure Database for PostgreSQL サーバーのバックアップ](backup-azure-database-postgresql-overview.md#support-matrix)のサポート マトリックスを参照 |
| アクション | バックアップ インスタンスのバックアップを停止する | <li> Azure Virtual Machine <br><br> <li> Azure Database for PostgreSQL サーバー <br><br> <li> Azure VM 内の SQL <br><br> <li> Azure VM 内の SAP HANA <br><br> <li> Azure Files<br/><br/> <li>Azure BLOB<br/><br/> <li>Azure Managed Disks | [Azure VM バックアップ](./backup-support-matrix-iaas.md)および[Azure Database for PostgreSQL サーバーのバックアップ](backup-azure-database-postgresql-overview.md#support-matrix)のサポート マトリックスを参照 |
| アクション | バックアップ センターからのリージョン間復元ジョブの実行 | <li> Azure Virtual Machine <br><br> <li> Azure VM 内の SQL <br><br> <li> Azure VM 内の SAP HANA | [リージョンをまたがる復元](./backup-create-rs-vault.md#set-cross-region-restore)に関するドキュメントを参照 |
| 洞察 | View Backup Reports | <li> Azure Virtual Machine <br><br> <li> Azure 仮想マシンでの SQL <br><br> <li> Azure 仮想マシンでの SAP HANA <br><br> <li> Azure Files <br><br> <li> System Center Data Protection Manager <br><br> <li> Azure Backup エージェント (MARS) <br><br> <li> Azure Backup Server (MABS) | [バックアップ レポートのサポートされるシナリオ](./configure-reports.md#supported-scenarios)を参照 |
| ガバナンス | カテゴリ [バックアップ] での組み込みとカスタムの Azure ポリシーの表示と割り当て | N/A | N/A |
| ガバナンス | バックアップ用に構成されていないデータソースの表示 | <li> Azure Virtual Machine <br><br> <li> Azure Database for PostgreSQL サーバー | N/A |

## <a name="unsupported-scenarios"></a>サポートされていないシナリオ

| **カテゴリ** | **シナリオ**  |
|--------------|---------------|
| アクション | コンテナー設定の大規模な構成は、現在、バックアップ センターからサポートされていません |

## <a name="next-steps"></a>次のステップ

* [Azure Backup のサポート マトリックスを確認する](./backup-support-matrix.md)
* [Azure VM バックアップのサポート マトリックスを確認する](./backup-support-matrix-iaas.md)
* [Azure Database for PostgreSQL サーバーのバックアップのサポート マトリックスを確認する](backup-azure-database-postgresql-overview.md#support-matrix)