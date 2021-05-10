---
title: 'VM サイズ: パフォーマンスのベスト プラクティスおよびガイドライン'
description: Azure 仮想マシン (VM) 上の SQL Server のパフォーマンスを最適化するために、VM サイズのガイドラインとベスト プラクティスについて説明します。
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
ms.date: 03/25/2021
ms.author: dpless
ms.reviewer: jroth
ms.openlocfilehash: 88adef7ea50744f913780d99594ce3baadade84b
ms.sourcegitcommit: 950e98d5b3e9984b884673e59e0d2c9aaeabb5bb
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/18/2021
ms.locfileid: "107600898"
---
# <a name="vm-size-performance-best-practices-for-sql-server-on-azure-vms"></a>VM サイズ: Azure VM 上の SQL Server のパフォーマンスに関するベスト プラクティス
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

この記事では、Azure Virtual Machines (VM) 上の SQL Server のパフォーマンスを最適化するための一連のベスト プラクティスおよびガイドラインとして、VM サイズのガイダンスを提供します。

通常、コストの最適化とパフォーマンスの最適化はトレードオフの関係になっています。 このパフォーマンスに関するベスト プラクティス シリーズでは、Azure Virtual Machines の SQL Server の "*最善の*" パフォーマンスを得ることに重点を置いています。 ワークロードの要求が厳しくない場合は、推奨される最適化がすべて必要になるわけではありません。 各推奨事項を評価するときに、パフォーマンスのニーズ、コスト、およびワークロードのパターンを考慮してください。


## <a name="checklist"></a>チェック リスト

次のチェックリストを参照して、この記事の残りの部分で詳しく説明されている VM サイズのベスト プラクティスの概要を確認してください。 

- [Standard_M8-4ms](/azure/virtual-machines/m-series)、[E4ds_v4](../../../virtual-machines/edv4-edsv4-series.md#edv4-series)、または [DS12_v2](../../../virtual-machines/dv2-dsv2-series-memory.md#dsv2-series-11-15) 以上などの vCPU が 4 個以上の VM サイズを使用します。 
- SQL Server ワークロードの最適なパフォーマンスを得るために、[メモリ最適化済み](../../../virtual-machines/sizes-memory.md)仮想マシン サイズを使用します。 
- [DSv2 11-15](../../../virtual-machines/dv2-dsv2-series-memory.md)、[Edsv4](../../../virtual-machines/edv4-edsv4-series.md) シリーズ、[M-](/azure/virtual-machines/m-series)、[Mv2-](../../../virtual-machines/mv2-series.md) シリーズでは、OLTP ワークロードに必要な、最適なメモリと仮想コアの比率が提供されます。 M シリーズの VM はどちらも、ミッション クリティカルなワークロードに必要な最も高いメモリと仮想コアの比率を提供するほか、データ ウェアハウスのワークロードにも最適です。 
- ミッション クリティカルな、およびデータ ウェアハウスのワークロードには、より高いメモリと仮想コアの比率を検討します。 
- SQL Server のパフォーマンスが最適になるように SQL Server の設定とストレージ オプションが構成されているため、Azure 仮想マシンのマーケットプレース イメージを活用します。 
- ターゲット ワークロードのパフォーマンス特性を収集し、それらを使用してお客様のビジネスに適した VM サイズを決定します。

この VM サイズのチェックリストを他と比較するには、総合的な[パフォーマンスに関するベスト プラクティスのチェックリスト](performance-guidelines-best-practices-checklist.md)をご覧ください。 

## <a name="overview"></a>概要

Azure VM で SQL Server を作成する場合、必要なワークロードの種類を慎重に検討してください。 既存の環境を移行する場合、[パフォーマンス ベースラインを収集](performance-guidelines-best-practices-collect-baseline.md)して、Azure VM 上の SQL Server の要件を特定します。 新しい VM の場合は、ベンダーの要件に基づいて新しい SQL Server VM を作成します。 

クラウド用に構築された新しいアプリケーションを使用して新しい SQL Server VM を作成する場合は、データや使用量の要件が変化したときに、それに合わせて SQL Server VM のサイズを簡単に変更できます。
下位レベルの D シリーズ、B シリーズ、または Av2 シリーズで開発環境を開始し、時間の経過とともに環境を拡張します。 

ポータルでのストレージ構成で SQL Server VM のマーケットプレース イメージを使用します。 これにより、お客様のワークロードに必要なサイズ、IOPS、スループットを確保するために必要な記憶域プールの適切な作成が容易になります。 Premium Storage と Premium Storage キャッシュをサポートしている SQL Server VM を選択することが重要です。 詳細については、[ストレージ](performance-guidelines-best-practices-storage.md)に関する記事をご覧ください。 

OLTP 運用環境で推奨される最小構成は、4 仮想コア、32 GB のメモリ、メモリと仮想コアの比率 8 です。 新しい環境では、4 個の仮想コアを搭載したマシンから始め、データとコンピューティングの要件が変化したときに、8、16、32 個以上の仮想コアに拡張します。 OLTP のスループットについては、仮想コアあたり 5000 IOPS の SQL Server VM をターゲットとします。 

SQL Server のデータ ウェアハウスおよびミッション クリティカルな環境では多くの場合、メモリと仮想コアの比率を 8 よりも大きくする必要があります。 中規模の環境では、メモリと仮想コアの比率 16 を選択し、より大規模なデータ ウェアハウス環境では、メモリと仮想コアの比率 32 を選択することをお勧めします。 

SQL Server のデータ ウェアハウス環境では多くの場合、サイズの大きなマシンの並列処理が有効です。 このため、M シリーズと Mv2 シリーズは、大規模なデータ ウェアハウス環境に最適なオプションです。

現在のオンプレミス SQL Server データベースを Azure VM 上の SQL Server に移行するためのベースラインとして、ソース マシンの vCPU とメモリ構成を使用します。 お客様のコア ライセンスを Azure に導入すると、[Azure ハイブリッド特典](https://azure.microsoft.com/pricing/hybrid-benefit/)を利用して、SQL Server のライセンス コストを節約できます。

## <a name="memory-optimized"></a>メモリ最適化

[メモリ最適化済み仮想マシン サイズ](../../../virtual-machines/sizes-memory.md)は、SQL Server VM のプライマリ ターゲットであり、Microsoft が推奨する選択肢です。 メモリ最適化済み仮想マシンでは、メモリと CPU のより高い比率および中規模から大規模のキャッシュ オプションを提供します。 

### <a name="m-mv2-and-mdsv2-series"></a>M、Mv2、Mdsv2 シリーズ

[M シリーズ](/azure/virtual-machines/m-series)は、一部の最大の SQL Server ワークロードに適した仮想コア数とメモリを提供します。  

[Mv2 シリーズ](../../../virtual-machines/mv2-series.md)は、最大の仮想コア数とメモリを備えており、ミッション クリティカルおよびデータ ウェアハウス ワークロードに推奨されています。 Mv2 シリーズ インスタンスはメモリ最適化済み VM サイズで、非常に優れたコンピューティング性能によって大規模なメモリ内データベースとワークロードをサポートし、リレーショナル データベース サーバー、大規模キャッシュ、インメモリ分析に最適な、メモリと CPU の高い比率を提供します。

たとえば、[Standard_M64ms](/azure/virtual-machines/m-series) のメモリと仮想コアの比率は 28 です。

[Mdsv2 ミディアム メモリ シリーズ](../../..//virtual-machines/msv2-mdsv2-series.md)は、現在[プレビュー](https://aka.ms/Mv2MedMemoryPreview)段階の新しい M シリーズで、Midtier メモリ オファリングを備えたさまざまな M シリーズ レベルの Azure 仮想マシンを提供します。 これらのマシンは、サポートされるメモリと仮想コアの比率が最小で 10、最大で 30 の SQL Server ワークロードに適しています。

SQL Server のパフォーマンスにとって魅力的な M および Mv2 シリーズの機能の一部には、[Premium Storage](../../../virtual-machines/premium-storage-performance.md) と [Premium Storage キャッシュ](../../../virtual-machines/premium-storage-performance.md#disk-caching)のサポート、[Ultra ディスク](../../../virtual-machines/disks-enable-ultra-ssd.md)のサポート、[書き込みアクセラレータ](../../../virtual-machines/how-to-enable-write-accelerator.md)などがあります。

### <a name="edsv4-series"></a>Edsv4 シリーズ

[Edsv4 シリーズ](../../../virtual-machines/edv4-edsv4-series.md)は、メモリを集中的に使用するアプリケーション向けに設計されています。 これらの VM には、大容量のローカル SSD ストレージ、強力なローカル ディスク IOPS、最大 504 GiB の RAM が搭載されています。 これらのほとんどの仮想マシンでは、仮想コアあたりのメモリが一貫して約 8 GiB であり、標準の SQL Server ワークロードに最適です。 

このグループには、仮想コア数が 80、メモリが 504 GB、メモリと仮想コアの比率が 6 の [Standard_E80ids_v4](../../../virtual-machines/edv4-edsv4-series.md) を使用した新しい仮想マシンがあります。 この仮想マシンは、[分離](../../../virtual-machines/isolation.md)されるため注目されています。つまり、ホスト上で実行される唯一の仮想マシンであることが保証され、他のお客様のワークロードから分離されます。 これのメモリと仮想コアの比率は、SQL Server に推奨される比率よりも低いため、分離が必要な場合にのみ使用してください。

Edsv4 シリーズの仮想マシンでは、[Premium Storage](../../../virtual-machines/premium-storage-performance.md) と [Premium Storage キャッシュ](../../../virtual-machines/premium-storage-performance.md#disk-caching)がサポートされています。

### <a name="dsv2-series-11-15"></a>DSv2 シリーズ 11 - 15

[DSv2 シリーズ 11 - 15](../../../virtual-machines/dv2-dsv2-series-memory.md#dsv2-series-11-15) のメモリおよびディスク構成は以前の D シリーズと同じです。 このシリーズでは、すべての仮想マシンでメモリと CPU の比率が一貫して 7 になっています。 これは、最小のメモリ最適化シリーズであり、エントリレベルの SQL Server ワークロードに適した低コストのオプションです。

[DSv2 シリーズ 11 - 15](../../../virtual-machines/dv2-dsv2-series-memory.md#dsv2-series-11-15) では [Premium Storage](../../../virtual-machines/premium-storage-performance.md) と [Premium Storage キャッシュ](../../../virtual-machines/premium-storage-performance.md#disk-caching)がサポートされており、最適なパフォーマンスを得るためには、これを強くお勧めします。

## <a name="general-purpose"></a>General Purpose

[汎用仮想マシン サイズ](../../../virtual-machines/sizes-general.md)は、開発とテスト、Web サーバー、小規模なデータベース サーバーのようなエントリ レベルの小さなワークロードに対して、メモリと仮想コアのバランスの取れた比率を提供するように設計されています。 

汎用仮想マシンでのメモリと仮想コアの比率が小さいため、メモリベースのパフォーマンス カウンターを注意深く監視して、SQL Server が必要なバッファー キャッシュ メモリを確実に取得できるようにすることが重要です。 詳細については、[メモリのパフォーマンス ベースライン](performance-guidelines-best-practices-collect-baseline.md#memory)をご覧ください。 

運用環境のワークロードの開始時に推奨されるメモリと仮想コアの比率は 8 であるため、SQL Server が動作している汎用 VM の推奨される最小構成は、4 vCPU および 32 GB のメモリです。 

### <a name="ddsv4-series"></a>Ddsv4 シリーズ

[Ddsv4 シリーズ](../../../virtual-machines/ddv4-ddsv4-series.md)では、vCPU、メモリ、一時ディスクの適正な組み合わせを提供しますが、サポートされている、メモリと仮想コアの比率は小さくなっています。 

Ddsv4 VM には、低待機時間で高速のローカル ストレージが含まれています。

これらのマシンは、一時ストレージと部門別リレーショナル データベースへの高速アクセスを必要とする SQL やアプリの並列デプロイに最適です。 このシリーズのすべての仮想マシンでは、メモリと仮想コアの標準比率が 4 になっています。 

このため、このシリーズのスターター仮想マシンとして D8ds_v4 を利用することをお勧めします。これには、8 個の仮想コアと 32 GB のメモリが搭載されています。 最大のマシンは、64 個の仮想コアと 256 GB のメモリが搭載された D64ds_v4 です。

[Ddsv4 シリーズ](../../../virtual-machines/ddv4-ddsv4-series.md)の仮想マシンでは、[Premium Storage](../../../virtual-machines/premium-storage-performance.md) と [Premium Storage キャッシュ](../../../virtual-machines/premium-storage-performance.md#disk-caching)がサポートされています。

> [!NOTE]
> [Ddsv4 シリーズ](../../../virtual-machines/ddv4-ddsv4-series.md)では、メモリと仮想コアの比率が、SQL Server のワークロードに推奨される 8 ではありません。 そのため、小規模なアプリケーションおよび開発ワークロードにのみ、これらの仮想マシンを使用することを検討してください。

### <a name="b-series"></a>B シリーズ

[負荷の急増に対応できる B シリーズ](../../../virtual-machines/sizes-b-series-burstable.md)の仮想マシン サイズは、概念実証や、非常に小さなアプリケーションおよび開発サーバーなど、一貫したパフォーマンスを必要としないワークロードに最適です。 

[負荷の急増に対応できる B シリーズ](../../../virtual-machines/sizes-b-series-burstable.md)の仮想マシン サイズのメモリと仮想コアの比率は 4 です。 これらのマシンの最大構成は、20 個の仮想コアと 80 GB のメモリを搭載した [Standard_B20ms](../../../virtual-machines/sizes-b-series-burstable.md) です。

このシリーズの特徴は、マシン サイズに応じて変化するバースト可能なクレジットを使用して業務時間中にアプリで **バースト** できることです。 

クレジットを使い切ると、VM がマシンのベースライン パフォーマンスに戻ります。

B シリーズの利点は、特に 1 日を通して処理能力をそれほど必要としない場合、他のシリーズの他の VM サイズに比べて、コンピューティングの節約を実現しやすいことです。

このシリーズでは、[Premium Storage](../../../virtual-machines/premium-storage-performance.md) がサポートされていますが、[Premium Storage キャッシュ](../../../virtual-machines/premium-storage-performance.md#disk-caching)は **サポートされていません**。

> [!NOTE] 
> [負荷の急増に対応できる B シリーズ](../../../virtual-machines/sizes-b-series-burstable.md)では、メモリと仮想コアの比率が、SQL Server のワークロードに推奨される 8 ではありません。 そのため、小規模なアプリケーション、Web サーバー、開発ワークロードにのみ、これらの仮想マシンを使用することを検討してください。

### <a name="av2-series"></a>Av2 シリーズ

[Av2 シリーズ](../../../virtual-machines/av2-series.md)の VM は、開発とテスト、低トラフィックの Web サーバー、小規模から中規模アプリのデータベース、概念実証のようなエントリ レベルのワークロードに最適です。

[Standard_A2m_v2](../../../virtual-machines/av2-series.md) (2 個の仮想コアと 16 GB のメモリ)、[Standard_A4m_v2](../../../virtual-machines/av2-series.md) (4 個の仮想コアと 32 GB のメモリ)、[Standard_A8m_v2](../../../virtual-machines/av2-series.md) (8 個の仮想コアと 64 GB のメモリ) のみ、メモリと仮想コアの比率がこれらの上位 3 つの仮想マシンに最適な 8 になっています。 

これらの仮想マシンはどれも、小規模な開発およびテスト用の SQL Server マシンに適したオプションです。 

また、8 仮想コアの [Standard_A8m_v2](../../../virtual-machines/av2-series.md) は、小規模なアプリケーションおよび Web サーバーにも適したオプションです。

> [!NOTE] 
> Av2 シリーズでは Premium Storage がサポートされていないため、メモリと仮想コアの比率が 8 の仮想マシンであっても、運用環境の SQL Server ワークロードにはお勧めしません。

## <a name="storage-optimized"></a>ストレージ最適化

[ストレージ最適化済み VM サイズ](../../../virtual-machines/sizes-storage.md)は、特定のユース ケースに適しています。 これらの仮想マシンは、最適化されたディスク スループットおよび IO を考慮して特別に設計されたものです。 

### <a name="lsv2-series"></a>Lsv2 シリーズ

[Lsv2 シリーズ](../../../virtual-machines/lsv2-series.md)は、高スループットで低遅延のローカル NVMe ストレージを特長としています。 Lsv2 シリーズの VM は、持続性のあるデータ ディスクを使用する代わりに、VM に直接接続されているノード上のローカル ディスクを使用するように最適化されています。 

これらの仮想マシンは、ビッグ データ、データ ウェアハウス、レポート、ETL などのワークロードに最適なオプションです。 ローカル NVMe ストレージの高いスループットと IOPS は、データベースに読み込まれるファイルを処理する場合や、ソース システムまたはその他のリポジトリ (Azure BLOB ストレージや Azure Data Lake など) からデータを再作成できるその他のシナリオに対して使用するのに適しています。 また、[Lsv2 シリーズ](../../../virtual-machines/lsv2-series.md)の VM では、一度に最大 30 分間、ディスク パフォーマンスをバーストすることもできます。

これらの仮想マシンのサイズは 8 - 80 vCPU で、vCPU あたり 8 GiB のメモリを搭載し、8 vCPU ごとに 1.92 TB の NVMe SSD があります。 つまり、このシリーズの最大の VM である [L80s_v2](../../../virtual-machines/lsv2-series.md) では、80 vCPU、640 BiB のメモリ、10 x 1.92 TB の NVMe ストレージが搭載されています。  これらのすべての仮想マシンでは、メモリと仮想コアの比率が一貫して 8 になっています。

NVMe ストレージはエフェメラルです。つまり、仮想マシンの割り当てを解除した場合、またはサービス復旧のために別のホストにデータを移動した場合、これらのディスクのデータは失われます。

Lsv2 および Ls シリーズでは、[Premium Storage](../../../virtual-machines/premium-storage-performance.md) がサポートされていますが、Premium Storage キャッシュはサポートされていません。 IOPS を増やすためのローカル キャッシュの作成はサポートされていません。 

> [!WARNING]
> エフェメラルな NVMe ストレージにデータ ファイルを格納すると、VM の割り当てが解除されたときにデータが失われる可能性があります。 

## <a name="constrained-vcores"></a>制約付き仮想コア

SQL Server の高パフォーマンス ワークロードでは、仮想コアの数が多くなくても、大量のメモリ、I/O、スループットが必要になる場合が頻繁にあります。 

ほとんどの OLTP ワークロードは、多数の小さなトランザクションによって駆動されるアプリケーション データベースです。 OLTP ワークロードでは、少量のデータが読み取られるか変更されるだけですが、ユーザーの数に基づいて駆動されるトランザクションの量は非常に大きくなります。 SQL Server のメモリを使用して、プランをキャッシュし、パフォーマンスのために最近アクセスしたデータを格納し、物理読み取りをメモリにすばやく読み込むことができるようにすることが重要です。 

これらの OLTP 環境では、より多くのメモリ、高速ストレージ、そして最適な実行に必要な I/O 帯域幅が必要です。 

SQL Server のライセンス コストを上げずにこのレベルのパフォーマンスを維持するため、Azure では[vCPU の数が制限された](../../../virtual-machines/constrained-vcpu.md) VM サイズを提供しています。 

これにより、親仮想マシンのメモリ、ストレージ、I/O 帯域幅を維持しながら、使用可能な仮想コア数を減らすことで、ライセンス コストを抑えることができます。

vCPU の数を、元の VM サイズの半分または 4 分の 1 に制限することができます。 仮想マシンで使用できる仮想コア数を削減すると、メモリと仮想コアの比率が高くなりますが、コンピューティング コストは変わりません。

これらの新しい VM サイズでは、識別しやすいように、アクティブな vCPU の数を指定するサフィックスが付加されています。 

たとえば、[M64-32ms](../../../virtual-machines/constrained-vcpu.md) には 32 個の SQL Server 仮想コアと、[M64ms](/azure/virtual-machines/m-series) のメモリ、I/O、スループットのみのライセンスが必要であり、[M64-16ms](../../../virtual-machines/constrained-vcpu.md) には 16 個の仮想コアのみのライセンスが必要です。  しかし、[M64-16ms](../../../virtual-machines/constrained-vcpu.md) では、SQL Server のライセンス コストは M64ms の 4 分の 1 になりますが、仮想マシンのコンピューティング コストは同じです。

> [!NOTE] 
> - それでも中規模から大規模のデータ ウェアハウスのワークロードでは[制約付き仮想コア VM](../../../virtual-machines/constrained-vcpu.md) が有効である可能性がありますが、データ ウェアハウスのワークロードは一般に、並列で実行されるクエリ プランによって少ない数のユーザーとプロセスで大量のデータを処理するという特徴があります。 
> - コンピューティング コスト (オペレーティング システムのライセンスを含む) は、親仮想マシンと同じままです。 



## <a name="next-steps"></a>次のステップ

詳細については、このシリーズの他の記事を参照してください。
- [クイック チェックリスト](performance-guidelines-best-practices-checklist.md)
- [Storage](performance-guidelines-best-practices-storage.md)
- [ベースラインの収集](performance-guidelines-best-practices-collect-baseline.md)

セキュリティのベスト プラクティスについては、「[Azure Virtual Machines 上の SQL Server のセキュリティに関する考慮事項](security-considerations-best-practices.md)」をご覧ください。

SQL Server Virtual Machines に関する他の記事については、[Azure Virtual Machines 上の SQL Server の概要](sql-server-on-azure-vm-iaas-what-is-overview.md)に関するページをご覧ください。 SQL Server の仮想マシンに関するご質問については、[よくあるご質問](frequently-asked-questions-faq.md)に関するページをご覧ください。 
