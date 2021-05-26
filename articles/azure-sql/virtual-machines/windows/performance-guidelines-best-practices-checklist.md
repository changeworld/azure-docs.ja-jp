---
title: 'チェックリスト: パフォーマンスのベスト プラクティスおよびガイドライン'
description: Azure 仮想マシン (VM) 上の SQL Server のパフォーマンスを最適化するためのベスト プラクティスおよびガイドラインを確認するためのクイック チェックリストを提供します。
services: virtual-machines-windows
documentationcenter: na
author: dplessMSFT
editor: ''
tags: azure-service-management
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 05/06/2021
ms.author: dpless
ms.custom: contperf-fy21q3
ms.reviewer: jroth
ms.openlocfilehash: 3649c593fcc7a0d27ed7326efa03b1325763649d
ms.sourcegitcommit: a9f131fb59ac8dc2f7b5774de7aae9279d960d74
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/19/2021
ms.locfileid: "110190901"
---
# <a name="checklist-performance-best-practices-for-sql-server-on-azure-vms"></a>チェックリスト: Azure VM 上の SQL Server のパフォーマンスに関するベスト プラクティス
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

この記事では、Azure 仮想マシン (VM) 上の SQL Server のパフォーマンスを最適化するための一連のベスト プラクティスおよびガイドラインとして、クイック チェックリストを提供します。 

包括的な詳細については、このシリーズの他の記事 ([VM サイズ](performance-guidelines-best-practices-vm-size.md)、[ストレージ](performance-guidelines-best-practices-storage.md)、[ベースラインの収集](performance-guidelines-best-practices-collect-baseline.md)) を参照してください。 


## <a name="overview"></a>概要

Azure Virtual Machines で SQL Server を実行するときは、オンプレミスのサーバー環境内の SQL Server に適用できるデータベース パフォーマンス チューニング オプションと同じものを引き続き使用します。 ただし、パブリック クラウド内のリレーショナル データベースのパフォーマンスは、仮想マシンのサイズやデータ ディスクの構成などのさまざまな要素に左右されます。

通常、コストの最適化とパフォーマンスの最適化はトレードオフの関係になっています。 このパフォーマンスに関するベスト プラクティス シリーズでは、Azure Virtual Machines の SQL Server の "*最善の*" パフォーマンスを得ることに重点を置いています。 ワークロードの要求が厳しくない場合は、推奨される最適化がすべて必要になるわけではありません。 各推奨事項を評価するときに、パフォーマンスのニーズ、コスト、およびワークロードのパターンを考慮してください。

## <a name="vm-size"></a>VM サイズ

Azure VM で SQL Server を実行する場合の VM サイズに関するベスト プラクティスのクイック チェックリストは次のとおりです。 

- [Standard_M8-4ms](../../../virtual-machines/m-series.md)、[E4ds_v4](../../../virtual-machines/edv4-edsv4-series.md#edv4-series)、または [DS12_v2](../../../virtual-machines/dv2-dsv2-series-memory.md#dsv2-series-11-15) 以上などの vCPU が 4 個以上の VM サイズを使用します。 
- SQL Server ワークロードの最適なパフォーマンスを得るために、[メモリ最適化済み](../../../virtual-machines/sizes-memory.md)仮想マシン サイズを使用します。 
- [DSv2 11-15](../../../virtual-machines/dv2-dsv2-series-memory.md)、[Edsv4](../../../virtual-machines/edv4-edsv4-series.md) シリーズ、[M-](../../../virtual-machines/m-series.md)、[Mv2-](../../../virtual-machines/mv2-series.md) シリーズでは、OLTP ワークロードに必要な、最適なメモリと仮想コアの比率が提供されます。 M シリーズの VM はどちらも、ミッション クリティカルなワークロードに必要な最も高いメモリと仮想コアの比率を提供するほか、データ ウェアハウスのワークロードにも最適です。 
- ミッション クリティカルおよびデータ ウェアハウスのワークロードには、より高いメモリと仮想コアの比率を検討します。 
- SQL Server のパフォーマンスが最適になるように SQL Server の設定とストレージ オプションが構成されているため、Azure 仮想マシンのマーケットプレース イメージを使用します。 
- ターゲット ワークロードのパフォーマンス特性を収集し、それらを使用してお客様のビジネスに適した VM サイズを決定します。

詳細については、包括的な [VM サイズのベスト プラクティス](performance-guidelines-best-practices-vm-size.md)に関するページを参照してください。 

## <a name="storage"></a>ストレージ

Azure VM で SQL Server を実行する場合のストレージ構成に関するベスト プラクティスのクイック チェックリストは次のとおりです。 

- ディスクの種類を選択する前に、アプリケーションを監視し、SQL Server のデータ、ログ、および tempdb の各ファイルの[ストレージ帯域幅と待機時間の要件を明確にします](../../../virtual-machines/premium-storage-performance.md#counters-to-measure-application-performance-requirements)。 
- ストレージのパフォーマンスを最適化するには、使用可能な最大の未キャッシュ IOPS を計画し、データの読み取りのパフォーマンス機能としてデータ キャッシュを使用しながら、[仮想マシンとディスクの上限や調整](../../../virtual-machines/premium-storage-performance.md#throttling)を回避します。
- データ、ログ、tempdb の各ファイルを別々のドライブに配置します。
    - データ ドライブには、[Premium P30 と P40 のディスク](../../../virtual-machines/disks-types.md#premium-ssd)のみを使用して、キャッシュ サポートの可用性を確保します
    - ログ ドライブについては、[Premium P30 から P80 のディスク](../../../virtual-machines/disks-types.md#premium-ssd)を評価しながら、コストと対比して容量の計画とパフォーマンスのテストを行います。
      - ミリ秒未満のストレージの待機時間が求められる場合は、トランザクション ログに [Azure Ultra ディスク](../../../virtual-machines/disks-types.md#ultra-disk)を使用します。 
      - M シリーズの仮想マシン デプロイに対しては、Azure Ultra ディスクの使用よりも[書き込みアクセラレータ](../../../virtual-machines/how-to-enable-write-accelerator.md)を検討します。
    - 最適な VM サイズを選択した後、ほとんどの SQL Server ワークロード用に、ローカル エフェメラル SSD `D:\` ドライブに [tempdb](/sql/relational-databases/databases/tempdb-database) を配置します。 
      - ローカル ドライブの容量が tempdb に対して十分でない場合は、VM のサイズを増やすことを検討します。 詳細については、「[データ ファイルのキャッシュ ポリシー](performance-guidelines-best-practices-storage.md#data-file-caching-policies)」を参照してください。
- [記憶域スペース](/windows-server/storage/storage-spaces/overview)を使用して複数の Azure データ ディスクをストライピングし、ターゲット仮想マシンの IOPS およびスループットの上限まで I/O 帯域幅を増やします。
- データ ファイル ディスクの場合は、[[ホスト キャッシュ]](../../../virtual-machines/disks-performance.md#virtual-machine-uncached-vs-cached-limits) を [読み取り専用] に設定します。
- ログ ファイル ディスクの場合は、[[ホスト キャッシュ]](../../../virtual-machines/disks-performance.md#virtual-machine-uncached-vs-cached-limits) を [なし] に設定します。
    - SQL Server ファイルが含まれているディスクでは、読み取り/書き込みキャッシュを有効にしないでください。 
    - ディスクのキャッシュ設定を変更する前に、必ず SQL Server サービスを停止してください。
- 開発とテストのワークロードには、Standard ストレージの使用を検討します。 運用環境のワークロードに Standard HDD/SDD を使用することはお勧めしません。
- [クレジットベースのディスク バースト](../../../virtual-machines/disk-bursting.md#credit-based-bursting) (P1 から P20) は、小規模な開発またはテストのワークロードおよび部門別システムでのみ検討してください。
- ドライブに配置されるすべてのデータ ファイルに 64 KB アロケーション ユニット サイズを使用するように、データ ディスクをフォーマットします。ただし、一時 `D:\` ドライブ (既定値は 4 KB) 以外が対象です。 Azure Marketplace を通じてデプロイされた SQL Server VM には、アロケーション ユニット サイズでフォーマットされたデータ ディスクが付属しており、64 KB に設定された記憶域プールに対してインターリーブします。 

詳細については、包括的な[ストレージのベスト プラクティス](performance-guidelines-best-practices-storage.md)に関するページを参照してください。 

## <a name="sql-server-features"></a>SQL Server 機能

次に示すのは、実稼働環境の Azure 仮想マシンで SQL Server インスタンスを実行する場合の、SQL Server 構成設定のベスト プラクティスの簡単なチェックリストです。 

- 必要に応じて、[データベース ページの圧縮](/sql/relational-databases/data-compression/data-compression)を有効にします。
- [バックアップ圧縮](/sql/relational-databases/backup-restore/backup-compression-sql-server)を有効にします。
- データ ファイルの[瞬時初期化](/sql/relational-databases/databases/database-instant-file-initialization)を有効にします。
- データベースの[自動拡張](/troubleshoot/sql/admin/considerations-autogrow-autoshrink#considerations-for-autogrow)を制限します。
- データベースの[自動圧縮](/troubleshoot/sql/admin/considerations-autogrow-autoshrink#considerations-for-auto_shrink)を無効にします。
- データベースの自動終了を無効にします。
- [システム データベース](/sql/relational-databases/databases/move-system-databases)も含め、すべてのデータベースをデータ ディスクに移動します。
- SQL Server エラー ログとトレース ファイルのディレクトリをデータ ディスクに移動します。
- 既定のバックアップとデータベース ファイルの場所を構成します。
- オペレーティング システムに 十分なメモリを 残す [SQL Server の最大メモリ制限](/sql/database-engine/configure-windows/server-memory-server-configuration-options#use-)を設定します。 ([[メモリ\使用可能なバイト数]](/sql/relational-databases/performance-monitor/monitor-memory-usage) を利用して、オペレーティング システムのメモリの正常性を監視します)。
- [Lock Pages in Memory](/sql/database-engine/configure-windows/enable-the-lock-pages-in-memory-option-windows) を有効にします。
- OLTP の負荷の高い環境で[アドホック ワークロードの最適化](/sql/database-engine/configure-windows/optimize-for-ad-hoc-workloads-server-configuration-option)を有効にします。
- インストールされている SQL Server のバージョンの[最新の累積的な更新プログラム](/sql/database-engine/install-windows/latest-updates-for-microsoft-sql-server)を評価して適用します。
- [ベスト プラクティスに従って](/sql/relational-databases/performance/best-practice-with-the-query-store)、実稼働のすべての SQL Server データベースで[クエリ ストア](/sql/relational-databases/performance/monitoring-performance-by-using-the-query-store)を有効にします。
- ミッション クリティカルなアプリケーション データベースの[自動チューニング](/sql/relational-databases/automatic-tuning/automatic-tuning)を有効にします。
- すべての [tempdb のベスト プラクティス](/sql/relational-databases/databases/tempdb-database#optimizing-tempdb-performance-in-sql-server)に従います。
- tempdb をエフェメラル D:/ にドライブに配置します。
- 1 ファイルから始まって最大 8 ファイルまで複数の tempdb データ ファイルを使用して、[推奨される数のファイルを使用します](/troubleshoot/sql/performance/recommendations-reduce-allocation-contention#resolution)。
- [DBCC CHECKDB](/sql/t-sql/database-console-commands/dbcc-checkdb-transact-sql#a-checking-both-the-current-and-another-database)、[インデックスの再構成](/sql/relational-databases/indexes/reorganize-and-rebuild-indexes#reorganize-an-index)、[インデックス再構築](/sql/relational-databases/indexes/reorganize-and-rebuild-indexes#rebuild-an-index)、[統計更新](/sql/t-sql/statements/update-statistics-transact-sql#examples)の各ジョブを実行する SQL Server エージェント ジョブのスケジュールを設定します。
- SQL Server [トランザクション ログ ファイル](/sql/relational-databases/logs/manage-the-size-of-the-transaction-log-file#Recommendations)の正常性とサイズを監視および管理します。
- 使用されているバージョンで使用できる新しい [SQL Server 機能](/sql/sql-server/what-s-new-in-sql-server-ver15)を利用します。
- デプロイを検討しているエディション間で[サポートされる機能](/sql/sql-server/editions-and-components-of-sql-server-version-15)の違いに注意してください。

## <a name="azure-features"></a>機能とのマップ

以下は、Azure VM で SQL Server を実行する場合の Azure 固有のガイダンスに関するベスト プラクティスのクイック チェックリストです。

- [SQL IaaS Agent 拡張機能](sql-agent-extension-manually-register-single-vm.md)に登録して、多くの[機能面の利点](sql-server-iaas-agent-extension-automate-management.md#feature-benefits)を活用できるようにします。
- SQL Server ワークロードに最適な[バックアップと復元](backup-restore.md#decision-matrix)の戦略を活用します。
- 仮想マシンで[高速ネットワークが有効](../../../virtual-network/create-vm-accelerated-networking-cli.md#portal-creation)になっていることを確認します。
- [Azure Security Center](../../../security-center/index.yml) を活用して、仮想マシンのデプロイの全体的なセキュリティ体制を改善します。
- [Azure Security Center](https://azure.microsoft.com/services/security-center/) と統合された [Azure Defender](../../../security-center/azure-defender.md) を利用して、脆弱性評価や Just-In-Time アクセスなど、特定の [SQL Server VM の対象範囲](../../../security-center/defender-for-sql-introduction.md)に対応します。攻撃サービスが減る一方、正当なユーザーは必要に応じて仮想マシンにアクセスできます。 詳細については、[脆弱性評価](../../../security-center/defender-for-sql-on-machines-vulnerability-assessment.md)、[SQL Server VM の脆弱性評価の有効化](sql-vulnerability-assessment-enable.md)、[Just-In-Time アクセス](../../../security-center/just-in-time-explained.md)を参照してください。 
- [Azure Advisor](../../../advisor/advisor-overview.md) を活用して、[パフォーマンス](../../../advisor/advisor-performance-recommendations.md)、[コスト](../../../advisor/advisor-cost-recommendations.md)、[信頼性](../../../advisor/advisor-high-availability-recommendations.md)、[オペレーショナル エクセレンス](../../../advisor/advisor-operational-excellence-recommendations.md)、および[セキュリティに関する推奨事項](../../../advisor/advisor-security-recommendations.md)に対処します。
- [Azure Monitor](../../../azure-monitor/vm/quick-monitor-azure-vm.md)を活用して、SQL Server 環境からテレメトリ データを収集、分析、操作します。 これには、より詳細なシンダンのために [VM insights](../../../azure-monitor/vm/vminsights-overview.md) を使用してインフラストラクチャの問題を特定することと、[Log Analytics](../../../azure-monitor/logs/log-query-overview.md) を使用してデータを監視することが含まれます。
- 開発環境とテスト環境で[自動シャットダウン](../../../automation/automation-solution-vm-management.md)を有効にします。 
- ビジネス継続性の SLA を満たす高可用性とディザスターリカバリー (HADR) ソリューションを実装します。Azure VM 上の SQL Server に使用できる [HADR オプション](business-continuity-high-availability-disaster-recovery-hadr-overview.md#deployment-architectures)オプションを参照してください。 
- Azure portal (サポート + トラブルシューティング) を使用して[リソースの正常性](../../../service-health/resource-health-overview.md)と履歴を評価します。必要に応じて、新しいサポート要求を送信します。

## <a name="next-steps"></a>次のステップ

詳細については、このシリーズの他の記事を参照してください。
- [VM サイズ](performance-guidelines-best-practices-vm-size.md)
- [Storage](performance-guidelines-best-practices-storage.md)
- [ベースラインの収集](performance-guidelines-best-practices-collect-baseline.md)

セキュリティのベスト プラクティスについては、「[Azure Virtual Machines 上の SQL Server のセキュリティに関する考慮事項](security-considerations-best-practices.md)」をご覧ください。

SQL Server Virtual Machines に関する他の記事については、[Azure Virtual Machines 上の SQL Server の概要](sql-server-on-azure-vm-iaas-what-is-overview.md)に関するページをご覧ください。 SQL Server の仮想マシンに関するご質問については、[よくあるご質問](frequently-asked-questions-faq.md)に関するページをご覧ください。
