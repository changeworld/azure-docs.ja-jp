---
title: 'ベースラインの収集: パフォーマンスに関するベスト プラクティスとガイドライン'
description: Azure 仮想マシン (VM) 上の SQL Server のパフォーマンスを最適化するためのガイドラインとして、パフォーマンス ベースラインを収集する手順について説明します。
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
editor: ''
tags: azure-service-management
ms.assetid: a0c85092-2113-4982-b73a-4e80160bac36
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 03/25/2021
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: b1909d5fd5e3c02f104c73acb515740cb6ff65f6
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "105572262"
---
# <a name="collect-baseline-performance-best-practices-for-sql-server-on-azure-vm"></a>ベースラインの収集: Azure VM 上の SQL Server のパフォーマンスに関するベスト プラクティス
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

この記事では、Azure Virtual Machines (VM) 上の SQL Server のパフォーマンスを最適化するための一連のベスト プラクティスとガイドラインとして、パフォーマンス ベースラインを収集するための情報を提供します。

通常、コストの最適化とパフォーマンスの最適化はトレードオフの関係になっています。 このパフォーマンスに関するベスト プラクティス シリーズでは、Azure Virtual Machines の SQL Server の "*最善の*" パフォーマンスを得ることに重点を置いています。 ワークロードの要求が厳しくない場合は、推奨される最適化がすべて必要になるわけではありません。 各推奨事項を評価するときに、パフォーマンスのニーズ、コスト、およびワークロードのパターンを考慮してください。

## <a name="overview"></a>概要

規範的なアプローチとして、PerfMon や LogMan を使用してパフォーマンス カウンターを収集し、SQL Server の待機統計を取り込むと、ソース環境の一般的な負荷と潜在的なボトルネックについて理解を深めることができます。 

まず、[アプリケーションのパフォーマンス チェックリスト](../../../virtual-machines/premium-storage-performance.md#application-performance-requirements-checklist)に従って、ピーク時のソース ワークロードの CPU、メモリ、[IOPS](../../../virtual-machines/premium-storage-performance.md#iops)、[スループット](../../../virtual-machines/premium-storage-performance.md#throughput)、[待機時間](../../../virtual-machines/premium-storage-performance.md#latency)などを収集します。 

ピーク時間中のデータ (通常の営業日のワークロードなど) だけでなく、その他の負荷の高いプロセス (一日の終了時処理、週末の ETL ワークロードなど) も収集します。 非常に負荷の高いワークロード (四半期の最終日処理など) に合わせてリソースをスケールアップすることを検討し、その後、ワークロードが完了したらスケールダウンします。 

パフォーマンス分析を使用して、ワークロードのパフォーマンス要件に合わせてスケール調整できる [VM サイズ](../../../virtual-machines/sizes-memory.md)を選択します。


## <a name="storage"></a>ストレージ

SQL Server のパフォーマンスは I/O サブシステムに大きく依存し、ストレージのパフォーマンスは IOPS とスループットによって測定されます。 データベースが物理メモリに収まらない場合は、SQL Server によってデータベース ページが絶えずバッファー プールに取り込まれたり、バッファー プールから取り出されたりします。 SQL Server のデータ ファイルは、異なる方法で処理する必要があります。 ログ ファイルへのアクセスは順次処理されますが、トランザクションをロールバックする必要がある場合は例外で、その場合はデータ ファイル (tempdb を含む) がランダムにアクセスされます。 低速の I/O サブシステムを使用している場合、応答時間が遅い、タイムアウトが原因でタスクが完了しないなど、パフォーマンスの問題が発生する可能性があります。 

Azure Marketplace の仮想マシンでは既定で、データ ファイルとは別の物理ディスク上にログ ファイルが置かれています。 tempdb データ ファイルの数とサイズはベスト プラクティスに従います。これらのファイルは、エフェメラルな `D:\` ドライブに配置されます。 

次の PerfMon カウンターは、SQL Server に必要な IO スループットを検証するのに役立ちます。 
* **\LogicalDisk\Disk Reads/Sec** (読み取り IOPS)
* **\LogicalDisk\Disk Writes/Sec** (書き込み IOPS) 
* **\LogicalDisk\Disk Read Bytes/Sec** (データ、ログ、tempdb ファイルの読み取りスループット要件)
* **\LogicalDisk\Disk Write Bytes/Sec** (データ、ログ、tempdb ファイルの書き込みスループット要件)

ピーク時の IOPS とスループットの要件を使用して、測定値から得られた容量に対応する VM サイズを評価します。 

ワークロードが 20 K の読み取り IOPS と 10 K の書き込み IOPS を必要としている場合は、E16s_v3 (最大 32 K のキャッシュありと 25600 のキャッシュなし IOPS)、または記憶域スペースを使用してストライピングされた 2 つの P30 ディスクを備えた M16_s (最大 20 K のキャッシュありと 10 K のキャッシュなし IOPS) のどちらかを選択できます。 

VM には IOPS とスループットに対して異なるスケールの制限があるため、ワークロードのスループットと IOPS の両方の要件を把握するようにしてください。

## <a name="memory"></a>メモリ

OS によって使用される外部メモリと、SQL Server によって内部的に使用されるメモリの両方を追跡します。 どちらかのコンポーネントの負荷を特定すると、仮想マシンのサイズを調整し、チューニングの機会を見極めることができます。 

次の PerfMon カウンターは、SQL Server 仮想マシンのメモリの正常性を検証するのに役立ちます。 
* [\Memory\Available MBytes](/azure/monitoring/infrastructure-health/vmhealth-windows/winserver-memory-availmbytes)
* [\SQLServer:Memory Manager\Target Server Memory (KB)](/sql/relational-databases/performance-monitor/sql-server-buffer-manager-object)
* [\SQLServer:Memory Manager\Total Server Memory (KB)](/sql/relational-databases/performance-monitor/sql-server-buffer-manager-object)
* [\SQLServer:Buffer Manager\Lazy writes/sec](/sql/relational-databases/performance-monitor/sql-server-buffer-manager-object)
* [\SQLServer:Buffer Manager\Page life expectancy](/sql/relational-databases/performance-monitor/sql-server-buffer-manager-object)

## <a name="compute"></a>Compute

Azure におけるコンピューティングは、オンプレミスとは異なる方法で管理されます。 オンプレミスのサーバーは、管理のオーバーヘッドや新しいハードウェアを取得するためのコストが原因で、アップグレードしなくても数年間は持ちこたえるように作られています。 仮想化によってこうした問題の一部は緩和されますが、アプリケーションは基になるハードウェアを最大限に生かせるように最適化されます。つまり、リソース消費の大幅な変更には、物理環境全体の再調整が必要になります。 

さまざまなハードウェア シリーズ (異なるリージョンであっても) 上に新しい仮想マシンが配置されている Azure において、これは簡単に達成できる課題ではありません。 

Azure では、お客様は仮想マシンのリソースをできるだけ多く利用したいと考えているため、ワークロードに影響を与えることなく、平均 CPU を可能な限り高く保つように Azure 仮想マシンを構成する必要があります。 

次の PerfMon カウンターは、SQL Server 仮想マシンのコンピューティングの正常性を検証するのに役立ちます。
* **\Processor Information(_Total)\% Processor Time**
* **\Process(sqlservr)\% Processor Time**

> [!NOTE] 
> コンピューティングの 80% の使用を目指し、ピーク時は一定期間にわたって 90% を超えても 100% には達しないようにするのが理想です。 基本的にお客様は、アプリケーションに必要なコンピューティングだけをプロビジョニングし、その後、ビジネスの要件に応じてスケールアップまたはスケールダウンしたいと考えています。 


## <a name="next-steps"></a>次のステップ

詳細については、このシリーズの他の記事を参照してください。
- [クイック チェックリスト](performance-guidelines-best-practices-checklist.md)
- [VM サイズ](performance-guidelines-best-practices-vm-size.md)
- [Storage](performance-guidelines-best-practices-storage.md)


セキュリティのベスト プラクティスについては、「[Azure Virtual Machines 上の SQL Server のセキュリティに関する考慮事項](security-considerations-best-practices.md)」をご覧ください。

SQL Server Virtual Machines に関する他の記事については、[Azure Virtual Machines 上の SQL Server の概要](sql-server-on-azure-vm-iaas-what-is-overview.md)に関するページをご覧ください。 SQL Server の仮想マシンに関するご質問については、[よくあるご質問](frequently-asked-questions-faq.md)に関するページをご覧ください。
