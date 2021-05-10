---
title: 'チェックリスト: パフォーマンスのベスト プラクティスおよびガイドライン'
description: Azure 仮想マシン (VM) 上の SQL Server のパフォーマンスを最適化するためのベスト プラクティスおよびガイドラインを確認するためのクイック チェックリストを提供します。
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
editor: ''
tags: azure-service-management
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 03/25/2021
ms.author: mathoma
ms.custom: contperf-fy21q3
ms.reviewer: jroth
ms.openlocfilehash: 84f2f4f679de80cd9b5fc986d40e084bae8a4cad
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/13/2021
ms.locfileid: "107313761"
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
- [記憶域スペース](/windows-server/storage/storage-spaces/overview)を使用して複数の Azure データ ディスクをストライプ化し、ターゲット仮想マシンの IOPS およびスループットの上限まで I/O 帯域幅を増やします。
- データ ファイル ディスクの場合は、[[ホスト キャッシュ]](../../../virtual-machines/disks-performance.md#virtual-machine-uncached-vs-cached-limits) を [読み取り専用] に設定します。
- ログ ファイル ディスクの場合は、[[ホスト キャッシュ]](../../../virtual-machines/disks-performance.md#virtual-machine-uncached-vs-cached-limits) を [なし] に設定します。
    - SQL Server ファイルが含まれているディスクでは、読み取り/書き込みキャッシュを有効にしないでください。 
    - ディスクのキャッシュ設定を変更する前に、必ず SQL Server サービスを停止してください。
- 開発とテストのワークロードには、Standard ストレージの使用を検討します。 運用環境のワークロードに Standard HDD/SDD を使用することはお勧めしません。
- [クレジットベースのディスク バースト](../../../virtual-machines/disk-bursting.md#credit-based-bursting) (P1 から P20) は、小規模な開発またはテストのワークロードおよび部門別システムでのみ検討してください。
- ストレージ アカウントは、SQL Server VM と同じリージョンにプロビジョニングします。 
- ストレージ アカウントで Azure geo 冗長ストレージ (geo レプリケーション) を無効にし、LRS (ローカル冗長ストレージ) を使用します。
- ドライブに配置されるすべてのデータ ファイルに 64 KB アロケーション ユニット サイズを使用するように、データ ディスクをフォーマットします。ただし、一時 `D:\` ドライブ (既定値は 4 KB) 以外が対象です。 Azure Marketplace を通じてデプロイされた SQL Server VM には、アロケーション ユニット サイズでフォーマットされたデータ ディスクが付属しており、64 KB に設定された記憶域プールに対してインターリーブします。 

詳細については、包括的な[ストレージのベスト プラクティス](performance-guidelines-best-practices-storage.md)に関するページを参照してください。 


## <a name="azure--sql-feature-specific"></a>Azure および SQL の機能固有

以下は、Azure VM で SQL Server を実行する場合の SQL Server および Azure 固有の構成に関するベスト プラクティスのクイック チェックリストです。 

- [SQL IaaS Agent 拡張機能](sql-agent-extension-manually-register-single-vm.md)に登録して、多くの[機能面の利点](sql-server-iaas-agent-extension-automate-management.md#feature-benefits)を活用できるようにします。 
- データベース ページの圧縮を有効にします。
- データ ファイルの瞬時初期化を有効にします。
- データベースの自動拡張を制限します。
- データベースの自動圧縮を無効にします。
- システム データベースも含め、すべてのデータベースをデータ ディスクに移動します。
- SQL Server エラー ログとトレース ファイルのディレクトリをデータ ディスクに移動します。
- 既定のバックアップとデータベース ファイルの場所を構成します。
- [メモリ内のロックされたページを有効にします](/sql/database-engine/configure-windows/enable-the-lock-pages-in-memory-option-windows)。
- インストールされている SQL Server バージョンの[最新の累積的な更新プログラム](/sql/database-engine/install-windows/latest-updates-for-microsoft-sql-server)を評価して適用します。
- Azure BLOB ストレージに直接バックアップします。
- 複数の [tempdb](/sql/relational-databases/databases/tempdb-database#optimizing-tempdb-performance-in-sql-server) ファイル (コアあたり 1 ファイル、最大 8 ファイル) を使用します。



## <a name="next-steps"></a>次のステップ

詳細については、このシリーズの他の記事を参照してください。
- [VM サイズ](performance-guidelines-best-practices-vm-size.md)
- [Storage](performance-guidelines-best-practices-storage.md)
- [ベースラインの収集](performance-guidelines-best-practices-collect-baseline.md)

セキュリティのベスト プラクティスについては、「[Azure Virtual Machines 上の SQL Server のセキュリティに関する考慮事項](security-considerations-best-practices.md)」をご覧ください。

SQL Server Virtual Machines に関する他の記事については、[Azure Virtual Machines 上の SQL Server の概要](sql-server-on-azure-vm-iaas-what-is-overview.md)に関するページをご覧ください。 SQL Server の仮想マシンに関するご質問については、[よくあるご質問](frequently-asked-questions-faq.md)に関するページをご覧ください。
