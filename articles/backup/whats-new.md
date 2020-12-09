---
title: Azure Backup の新着情報
description: Azure Backup の新機能について説明しています。
ms.topic: conceptual
ms.date: 11/11/2020
ms.openlocfilehash: ba29ddea5d5f096640f2bfc012c44ab06bb3e131
ms.sourcegitcommit: ac7029597b54419ca13238f36f48c053a4492cb6
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/29/2020
ms.locfileid: "96309666"
---
# <a name="whats-new-in-azure-backup"></a>Azure Backup の新着情報

Azure Backup は常に改善が行われ、Azure でのデータの保護を強化する新機能がリリースされています。 これらの新機能により、データ保護が新しいワークロードの種類に拡張され、セキュリティが強化されて、バックアップ データの可用性が向上します。 また、管理、監視、自動化に関する新しい機能も追加されます。

このページをブックマークするか、[こちらで更新情報をサブスクライブする](https://azure.microsoft.com/updates/?query=backup)ことにより、新しいリリースについての詳細を入手できます。

## <a name="updates-summary"></a>更新の概要

- 2020 年 11 月
  - [Azure ファイル共有 (AFS) バックアップ用の Azure Resource Manager テンプレート](#azure-resource-manager-template-for-afs-backup)
  - [Azure VM 上の SAP HANA データベースの増分バックアップ](#incremental-backups-for-sap-hana-databases)
- 2020 年 9 月
  - [バックアップ センター](#backup-center)
  - [Azure Database for PostgreSQL のバックアップ](#backup-azure-database-for-postgresql)
  - [ディスクの選択的なバックアップと復元](#selective-disk-backup-and-restore)
  - [Azure VM 上の SQL Server データベースと SAP HANA データベースのリージョン間の復元](#cross-region-restore-for-sql-server-and-sap-hana)
  - [最大 32 個のディスクを使用する VM のバックアップのサポート](#support-for-backup-of-vms-with-up-to-32-disks)
  - [Azure VM での SQL のバックアップ構成エクスペリエンスの簡素化](#simpler-backup-configuration-for-sql-in-azure-vms)
  - [RHEL の Azure 仮想マシンでの SAP HANA のバックアップ](#backup-sap-hana-in-rhel-azure-virtual-machines)
  - [バックアップ データ用のゾーン冗長ストレージ (ZRS)](#zone-redundant-storage-zrs-for-backup-data)
  - [Azure VM での SQL Server および SAP HANA ワークロードの論理的な削除](#soft-delete-for-sql-server-and-sap-hana-workloads)

## <a name="azure-resource-manager-template-for-afs-backup"></a>AFS バックアップ用の Azure Resource Manager テンプレート

Azure Backup では、Azure Resource Manager (ARM) テンプレートを使用した既存の Azure ファイル共有のバックアップの構成をサポートするようになりました。 このテンプレートでは、Recovery Services コンテナーとバックアップ ポリシーの適切な詳細を指定することによって、既存の Azure ファイル共有の保護を構成できます。 必要に応じて、新しい Recovery Services コンテナーとバックアップ ポリシーが作成され、ファイル共有を含むストレージ アカウントが Recovery Services コンテナーに登録されます。

詳細については、「[Azure Backup 用 Azure Resource Manager テンプレート](backup-rm-template-samples.md)」を参照してください。

## <a name="incremental-backups-for-sap-hana-databases"></a>SAP HANA データベースの増分バックアップ

Azure VM でホストされている SAP HANA データベースの増分バックアップが、Azure Backup でサポートされるようになりました。 これにより、SAP HANA データのバックアップを、いっそう高速に、コスト効率よく行うことができます。

詳細については、[バックアップポリシーの作成時に使用できるさまざまなオプション](sap-hana-faq-backup-azure-vm.md#policy)および [SAP HANA データベース用のバックアップ ポリシーを作成する方法](tutorial-backup-sap-hana-db.md#creating-a-backup-policy)に関するページを参照してください。

## <a name="backup-center"></a>バックアップ センター

Azure Backup で、中央コンソールからバックアップ資産全体を管理するための新しいネイティブ管理機能を使用できるようになりました。 バックアップ センターを使用すると、Azure のネイティブ管理エクスペリエンスと一貫性のある統合された方法で、大規模なデータ保護を監視、操作、制御、最適化できます。

バックアップ センターからは、Azure Lighthouse を使用して、サブスクリプション、場所、リソース グループ、コンテナー、さらにはテナントまで横断的に、インベントリの集約されたビューを取得できます。 バックアップ センターはアクション センターでもあり、バックアップの構成、復元、ポリシーやコンテナーの作成など、バックアップに関連するすべてのアクティビティを、1 つの場所からトリガーできます。 さらに、Azure Policy とのシームレスな統合により、環境の管理やコンプライアンスの追跡を、バックアップの観点から行うことができるようになりました。 また、Azure Backup に固有の組み込みの Azure ポリシーを使用して、大規模なバックアップを構成することもできます。

詳細については、「[バックアップ センターの概要](backup-center-overview.md)」を参照してください。

## <a name="backup-azure-database-for-postgresql"></a>Azure Database for PostgreSQL のバックアップ

Azure Backup と Azure Database Services の連携により、Azure PostgreSQL 向けのエンタープライズ クラスのバックアップ ソリューションが構築されます (現在はプレビュー)。 お客様が管理するバックアップ ポリシーを使用して、データの保護とコンプライアンスのニーズを満たすことができるようになり、バックアップを最大 10 年間保持できます。 これにより、個々のデータベース レベルでバックアップと復元の操作の管理をきめ細かく制御できます。 同様に、PostgreSQL の異なるバージョン間で、または Blob Storage にも、簡単に復元できます。

詳細については、[Azure Database for PostgreSQL のバックアップ](backup-azure-database-postgresql.md)に関するページを参照してください。

## <a name="selective-disk-backup-and-restore"></a>ディスクの選択的なバックアップと復元

Azure Backup では、仮想マシン バックアップ ソリューションを使用して、VM 内のすべてのディスク (オペレーティング システムとデータ) をまとめてバックアップすることがサポートされています。 現在、選択的なディスク バックアップと復元の機能を使用して、VM 内のデータ ディスクのサブセットをバックアップできるようになっています。 これにより、バックアップと復元のニーズに応じた効率的で費用対効果の高いソリューションが提供されます。 各復旧ポイントには、バックアップ操作に含まれるディスクだけが含まれます。

詳細については、「[Azure 仮想マシンの選択的なディスク バックアップと復元](selective-disk-backup-restore.md)」を参照してください。

## <a name="cross-region-restore-for-sql-server-and-sap-hana"></a>SQL Server と SAP HANA のリージョン間の復元

リージョン間の復元の導入により、セカンダリ リージョンで復元を自由に開始できるようになり、環境のプライマリ リージョンでの実際のダウンタイムの問題が軽減されます。 これにより、お客様はセカンダリ リージョンでの復元を完全に制御できます。 そのような復元の場合、セカンダリ リージョンにレプリケートされたバックアップ データが Azure Backup によって使用されます。

Azure 仮想マシンのリージョン間の復元に加えて、この機能は、Azure 仮想マシン内の SQL データベースと SAP HANA データベースの復元にも拡張されています。

詳細については、[SQL データベースのリージョン間の復元](restore-sql-database-azure-vm.md#cross-region-restore)および [SAP HANA データベースのリージョン間の復元](sap-hana-db-restore.md#cross-region-restore)に関するページを参照してください。

## <a name="support-for-backup-of-vms-with-up-to-32-disks"></a>最大 32 個のディスクを使用する VM のバックアップのサポート

これまでは、Azure Backup でサポートされるマネージド ディスクは VM ごとに 16 個でした。 現在は、VM あたり最大 32 個のマネージド ディスクのバックアップが、Azure Backup でサポートされるようになっています。

詳細については、[VM のストレージのサポート マトリックス](backup-support-matrix-iaas.md#vm-storage-support)に関するページを参照してください。

## <a name="simpler-backup-configuration-for-sql-in-azure-vms"></a>Azure VM 内の SQL のバックアップ構成の簡素化

Azure VM 内の SQL Server のバックアップの構成が、Azure portal の VM ペインに統合されたインライン バックアップ構成により、いっそう簡単になりました。 わずか数ステップで、SQL Server のバックアップを有効にして、既存のすべてのデータベースと、今後追加されるデータベースを保護することができます。

詳細については、「[VM ウィンドウから SQL Server をバックアップする](backup-sql-server-vm-from-vm-pane.md)」を参照してください。

## <a name="backup-sap-hana-in-rhel-azure-virtual-machines"></a>RHEL の Azure 仮想マシンでの SAP HANA のバックアップ

Azure Backup は、Azure 用のネイティブ バックアップ ソリューションであり、SAP によって認定された BackInt です。 SAP HANA の実行に最も広く使用されている Linux オペレーティング システムの 1 つである Red Hat Enterprise Linux (RHEL) のサポートが、Azure Backup に追加されました。

詳細については、[SAP HANA データベースのバックアップ シナリオのサポート マトリックス](sap-hana-backup-support-matrix.md#scenario-support)に関するページを参照してください。

## <a name="zone-redundant-storage-zrs-for-backup-data"></a>バックアップ データ用のゾーン冗長ストレージ (ZRS)

Azure Storage を使用すると、さまざまな冗長性オプションにより、ハイ パフォーマンス、高可用性、高データ回復性の優れたバランスが提供されます。 Azure Backup を使用すると、バックアップをローカル冗長ストレージ (LRS) と geo 冗長ストレージ (GRS) に格納するオプションにより、これらの利点をデータのバックアップまで拡張できます。 現在は、ゾーン冗長ストレージ (ZRS) のサポートの追加により、持続性オプションが追加されています。

詳細については、[Recovery Services コンテナー用のストレージ冗長性の設定](backup-create-rs-vault.md#set-storage-redundancy)に関するページを参照してください。

## <a name="soft-delete-for-sql-server-and-sap-hana-workloads"></a>SQL Server および SAP HANA ワークロードの論理的な削除

マルウェア、ランサムウェア、侵入などのセキュリティ問題への懸念が高まっています。 これらのセキュリティ問題は、金銭とデータの両方の観点からコストがかかる可能性があります。 このような攻撃から保護するため、Azure Backup には、削除後もバックアップ データを保護するためのセキュリティ機能が用意されています。

このような機能の 1 つに、論理的な削除があります。 論理的な削除を使用すると、悪意のあるアクターによってバックアップが削除 (またはバックアップ データが誤って削除) された場合でも、バックアップ データは追加で 14 日間保持されるので、データを失うことなくバックアップ項目を回復できます。 バックアップ データが "論理的な削除" 状態にあるこの追加の 14 日間のリテンション期間中は、お客様にコストは発生しません。

現在は、Azure VM の論理的な削除のサポートに加えて、Azure VM の SQL Server と SAP HANA ワークロードも、論理的な削除によって保護されています。

詳細については、「[Azure VM での SQL Server の論理的な削除、および Azure VM ワークロードの SAP HANA の論理的な削除](soft-delete-sql-saphana-in-azure-vm.md)」を参照してください。

## <a name="next-steps"></a>次の手順

- [Azure Backup のガイダンスとベスト プラクティス](guidance-best-practices.md)
