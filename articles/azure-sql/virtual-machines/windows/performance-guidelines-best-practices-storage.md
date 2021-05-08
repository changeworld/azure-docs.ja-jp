---
title: 'ストレージ: パフォーマンスのベスト プラクティスおよびガイドライン'
description: Azure 仮想マシン (VM) 上の SQL Server のパフォーマンスを最適化するための、ストレージのベスト プラクティスおよびガイドラインについて説明します。
services: virtual-machines-windows
documentationcenter: na
author: dplessMSFT
editor: ''
tags: azure-service-management
ms.assetid: a0c85092-2113-4982-b73a-4e80160bac36
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 03/25/2021
ms.author: dpless
ms.reviewer: jroth
ms.openlocfilehash: 001a9a15c259d0b0d73eec9c9a39ad7c27f26721
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "105572289"
---
# <a name="storage-performance-best-practices-for-sql-server-on-azure-vms"></a>ストレージ: Azure VM 上の SQL Server のパフォーマンスに関するベスト プラクティス
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

この記事では、Azure 仮想マシン (VM) 上の SQL Server のパフォーマンスを最適化するための、ストレージのベスト プラクティスおよびガイドラインについて説明します。

通常、コストの最適化とパフォーマンスの最適化はトレードオフの関係になっています。 このパフォーマンスに関するベスト プラクティス シリーズでは、Azure Virtual Machines 上の SQL Server の "*最善の*" パフォーマンスを得ることに重点を置いています。 ワークロードの要求が厳しくない場合は、推奨される最適化がすべて必要になるわけではありません。 各推奨事項を評価するときに、パフォーマンスのニーズ、コスト、およびワークロードのパターンを考慮してください。

詳細については、このシリーズの他の記事、「[パフォーマンス チェックリスト](performance-guidelines-best-practices-checklist.md)」、「[VM サイズ](performance-guidelines-best-practices-vm-size.md)」、および「[ベースラインの収集](performance-guidelines-best-practices-collect-baseline.md)」を参照してください。 

## <a name="checklist"></a>チェック リスト

次のチェックリストを参照して、この記事の残りの部分で詳しく説明されているストレージのベスト プラクティスの概要を確認してください。 

- ディスクの種類を選択する前に、アプリケーションを監視し、SQL Server のデータ、ログ、および tempdb の各ファイルの[ストレージ帯域幅と待機時間の要件を判断します](../../../virtual-machines/premium-storage-performance.md#counters-to-measure-application-performance-requirements)。 
- ストレージのパフォーマンスを最適化するには、キャッシュ不使用時の使用可能な最大の IOPS を計画し、データ読み取りのパフォーマンス機能としてデータ キャッシュを使用する一方で、[仮想マシンとディスクの上限](../../../virtual-machines/premium-storage-performance.md#throttling)を回避します。
- データ、ログ、tempdb の各ファイルを別々のドライブに配置します。
    - データ ドライブには、[Premium P30 と P40 のディスク](../../../virtual-machines/disks-types.md#premium-ssd)のみを使用して、キャッシュ サポートの可用性を確保します
    - ログ ドライブについては、[Premium P30 から P80 のディスク](../../../virtual-machines/disks-types.md#premium-ssd)を評価しながら、容量を計画し、コスト パフォーマンスをテストします
      - ミリ秒未満のストレージ待機時間が求められる場合は、トランザクション ログに [Azure Ultra ディスク](../../../virtual-machines/disks-types.md#ultra-disk)を使用します。 
      - M シリーズの仮想マシン デプロイについて、Azure Ultra ディスクの使用よりも[書き込みアクセラレータ](../../../virtual-machines/how-to-enable-write-accelerator.md)を検討します。
    - 最適な VM サイズを選択した後、大半の SQL Server ワークロード用のローカル エフェメラル SSD `D:\` ドライブに [tempdb](/sql/relational-databases/databases/tempdb-database) を配置します。 
      - ローカル ドライブの容量が tempdb に対して十分でない場合は、VM のサイズを増やすことを検討します。 詳細については、「[データ ファイルのキャッシュ ポリシー](#data-file-caching-policies)」を参照してください。
- [記憶域スペース](/windows-server/storage/storage-spaces/overview)を使用して複数の Azure データ ディスクをストライプ化し、ターゲット仮想マシンの IOPS およびスループットの上限まで I/O 帯域幅を増やします。
- データ ファイル ディスクの場合は、[[ホスト キャッシュ]](../../../virtual-machines/disks-performance.md#virtual-machine-uncached-vs-cached-limits) を [読み取り専用] に設定します。
- ログ ファイル ディスクの場合は、[[ホスト キャッシュ]](../../../virtual-machines/disks-performance.md#virtual-machine-uncached-vs-cached-limits) を [なし] に設定します。
    - SQL Server ファイルが含まれているディスクでは、読み取り/書き込みキャッシュを有効にしないでください。 
    - ディスクのキャッシュ設定を変更する前に、必ず SQL Server サービスを停止してください。
- 開発とテストのワークロード、および長期的なバックアップのアーカイブでは、Standard Storageの使用を検討してください。 運用環境のワークロードに Standard HDD/SDD を使用することはお勧めしません。
- [クレジットベースのディスク バースト](../../../virtual-machines/disk-bursting.md#credit-based-bursting) (P1 から P20) は、小規模な開発またはテストのワークロードおよび部門別システムでのみ検討してください。
- ストレージ アカウントは、SQL Server VM と同じリージョンにプロビジョニングします。 
- ストレージ アカウントで Azure geo 冗長ストレージ (geo レプリケーション) を無効にし、 LRS (ローカル冗長ストレージ) を使用します。
- ドライブに配置されるすべてのデータ ファイルに 64 KB アロケーション ユニット サイズを使用するように、データ ディスクをフォーマットします。ただし、一時 `D:\` ドライブ (既定値は 4 KB) 以外が対象です。 Azure Marketplace を通じてデプロイされた SQL Server VM には、アロケーション ユニット サイズでフォーマットされたデータ ディスクが付属しており、64 KB に設定された記憶域プールに対してインターリーブします。 

このストレージのチェックリストを他と比較するには、総合的な[パフォーマンスのベスト プラクティスのチェックリスト](performance-guidelines-best-practices-checklist.md)をご覧ください。 

## <a name="overview"></a>概要

Azure VM での SQL Server ワークロードの最も効果的な構成を見つけるには、まず[ビジネス アプリケーションのストレージ パフォーマンスを測定](performance-guidelines-best-practices-collect-baseline.md#storage)します。 ストレージの要件がわかったら、必要な IOPS とスループットを適切なメモリ対仮想コアの比率でサポートする仮想マシンを選択します。 

ワークロードに必要なストレージのスケーラビリティを備えた VM サイズと、実際のビジネスの容量とパフォーマンスの要件を満たすディスクの組み合わせ (通常は 1 つのストレージ プールに属する) を選択します。 

ディスクの種類は、ディスクでホストされているファイルの種類と、ピーク時のパフォーマンス要件によって異なります。

> [!TIP]
> Azure portal を通じて SQL Server VM をプロビジョニングすると、ストレージ構成プロセスに従って、データとログ ファイル用に個別の記憶域プールを作成したり、tempdb を `D:\` ドライブに配置したり、最適なキャッシュ ポリシーを有効にしたりするなど、ストレージのほとんどのベスト プラクティスを実装できます。 ストレージのプロビジョニングと構成の詳細については、[SQL VM ストレージの構成](storage-configuration.md)に関する記事を参照してください。 

## <a name="vm-disk-types"></a>VM ディスクの種類

ディスクのパフォーマンス レベルを選択できます。 基になるストレージとして使用できるマネージド ディスクの種類は (パフォーマンス向上能力が低いものから高いものの順に) 標準のハード ディスク ドライブ (HDD)、標準 SSD、Premium ソリッドステート ドライブ (SSD)、および Ultra ディスクです。 

ディスクのパフォーマンスは、容量に従って増加します。これは、4 GiB の容量と 120 の IOPS を持つ P1 から、32 TiB のストレージと 20,000 の IOSP を持つ P80 まで、[Premium ディスク ラベル](../../../virtual-machines/disks-types.md#premium-ssd)でグループ分けされます。 Premium Storage では、一部のワークロードの読み取りと書き込みのパフォーマンスを向上させるストレージ キャッシュがサポートされています。 詳細については、[マネージド ディスクの概要](../../../virtual-machines/managed-disks-overview.md)に関する記事を参照してください。 

Azure VM 上の SQL Server として考慮すべき主な[ディスクの種類](../../../virtual-machines/managed-disks-overview.md#disk-roles)として、OS ディスク、一時ディスク、データ ディスクの 3 種類があります。 オペレーティング システム ドライブと`(C:\)`エフェメラル一時ドライブに格納される内容を慎重に選択してください`(D:\)`。 

### <a name="operating-system-disk"></a>オペレーティング システム ディスク

オペレーティング システム ディスクは、実行中のバージョンのオペレーティング システムとして起動およびマウントできる VHD であり、`C:\` ドライブとしてラベル付けされます。 Azure 仮想マシンを作成すると、プラットフォームによって、少なくとも 1 つのディスクがオペレーティング システム ディスク用に VM に接続されます。 `C:\` ドライブは、アプリケーションのインストールとファイルの構成の既定の場所です。 

運用環境の SQL Server 環境では、データ ファイル、ログ ファイル、エラー ログにオペレーティング システム ディスクを使用しないでください。 

### <a name="temporary-disk"></a>一時ディスク

Azure 仮想マシンには、一時ディスクと呼ばれる別の種類のディスク (`D:\`ドライブとしてラベル付けされる) が含まれています。 仮想マシンのシリーズとサイズにより、このディスクの容量は異なります。 一時ディスクは一過性のものです。つまり、仮想マシンが再起動されたり、([サービス復旧](/troubleshoot/azure/virtual-machines/understand-vm-reboot)などの目的で) 別のホストに移動されたりすると、ディスク ストレージは再作成されます (割り当てが解除され、再度割り当てられる)。 

一時ストレージ ドライブはリモート ストレージに保存されないため、ユーザー データベース ファイル、トランザクション ログ ファイル、および保存する必要があるものはここに保存しないでください。 

ローカル キャッシュの使用に懸念がある場合を除き、tempdb は、SQL Server ワークロード用のローカル一時 SSD の `D:\` ドライブに配置してください。 [一時ディスクがない](../../../virtual-machines/azure-vms-no-temp-disk.md)仮想マシンを使用している場合は、キャッシュを読み取り専用に設定して、独自の分離ディスクまたは記憶域プールに tempdb を配置することをお勧めします。 詳細については、「[tempdb データ キャッシュ ポリシー](performance-guidelines-best-practices-storage.md#data-file-caching-policies)」を参照してください。

### <a name="data-disks"></a>データ ディスク

データ ディスクはリモート ストレージ ディスクであり、多くの場合、1 つのディスクが仮想マシンに提供できる容量とパフォーマンスを拡大するために、[記憶域プール](/windows-server/storage/storage-spaces/overview)に作成されるます。

ワークロードの IOPS、スループット、および容量の要件を満たす最小ディスク数を接続します。 サイズ変更する予定の最小の仮想マシンのデータ ディスクの最大数を超えないようにしてください。

データ ファイルとログ ファイルは、パフォーマンス要件に最も適した形でプロビジョニングされたデータ ディスクに配置してください。 

ドライブに配置されるすべてのデータ ファイルに 64 KB アロケーション ユニット サイズを使用するように、データ ディスクをフォーマットします。ただし、一時 `D:\` ドライブ (既定値は 4 KB) 以外が対象です。 Azure Marketplace を通じてデプロイされた SQL Server VM には、アロケーション ユニット サイズでフォーマットされたデータ ディスクが付属しており、64 KB に設定された記憶域プールに対してインターリーブします。 

## <a name="premium-disks"></a>Premium Disk

運用 SQL Server ワークロードのデータ ファイルとログ ファイルには、Premium SSD ディスクを使用します。 Premium SSD の IOPS と帯域幅は、[ディスクのサイズと種類](../../../virtual-machines/disks-types.md)によって異なります。 

運用環境のワークロードの場合は、SQL Server のデータ ファイルに P30 ディスクまたは P40 ディスクを使用してキャッシュのサポートを確保し、SQL Server トランザクション ログ ファイルには P30 から P80 を使用します。  総所有コストを最大限に高めるには、データ ファイルとログ ファイルには P30 (5000 IOPS/200 MBPS) から開始し、仮想マシンのディスク数を制御する必要がある場合にのみ、より高い容量を選択します。

OLTP ワークロードの場合は、ピーク時のワークロードと `Disk Reads/sec` + `Disk Writes/sec` パフォーマンス カウンターを使用して、ディスクあたりのターゲット IOPS (または記憶域プール) とパフォーマンス要件を一致させます。 データ ウェアハウスとレポートのワークロードでは、ピーク時と `Disk Read Bytes/sec` + `Disk Write Bytes/sec` のワークロードを使用してターゲットのスループットに適合させます。 

記憶域スペースを使用して最適なパフォーマンスを達成し、2 つのプール (ログ ファイル用とデータ ファイル用にそれぞれ 1 つずつ) を構成します。 ディスク ストライピングを使用していない場合は、1 つのディスクにログ ファイルが含まれ、もう 1 つにデータが含まれた 2 つの Premium SSD ディスクを使用します。

記憶域プールの一部として使用される、ディスクあたりの[プロビジョニングされた IOPS とスループット](../../../virtual-machines/disks-types.md#premium-ssd)。 ディスクの総合的な IOPS とスループットの能力は、仮想マシンのスループット上限までの最大能力です。

ベスト プラクティスは、IOPS (およびスループット) と容量の最小要件を満たす一方で、できるだけ少ない数のディスクを使用することです。 ただし、価格とパフォーマンスのバランスは、小容量のディスクを多数使用した方が、大容量ディスクを少数使用した場合よりも、良くなる傾向があります。

### <a name="scaling-premium-disks"></a>Premium ディスクのスケーリング

Azure マネージド ディスクを最初にデプロイするとき、そのディスクのパフォーマンス レベルは、プロビジョニングされたディスク サイズに基づきます。 パフォーマンス レベルは、デプロイ時に指定するか、またはその後にディスクのサイズを変えずに変更できます。 需要が増えた場合、ビジネス ニーズに合わせてパフォーマンス レベルを上げることができます。 

パフォーマンス レベルを変更すると、管理者は、[ディスクのバースト機能](../../../virtual-machines/disk-bursting.md#credit-based-bursting)を使用せずに、より高い需要に備え、対応することができます。 

ストレージのパフォーマンス レベルを満たすように課金が設計されている場合には、必要な期間だけ、より高いパフォーマンス レベルを使用します。 容量を増やすことなく、パフォーマンス要件に合わせてレベルをアップグレードします。 パフォーマンス増加が不要になった場合は、元のレベルに戻します。

パフォーマンスに関するこのコスト効率の高い、一時的な拡張は、高いパフォーマンスが短期間だけ必要となる、ショッピング、パフォーマンス テスト、トレーニング イベント、その他の短期の対象イベントにとって強力なユースケースです。 

詳細については、「[マネージド ディスクのパフォーマンス レベル](../../../virtual-machines/disks-change-performance.md)」を参照してください。 

## <a name="azure-ultra-disk"></a>Azure Ultra ディスク

待機時間を短縮し、ミリ秒未満の応答時間が必要な場合は、SQL Server ログ ドライブに [Azure Ultra ディスク](../../../virtual-machines/disks-types.md#ultra-disk)を使用するか、I/O 待機時間が非常に重要なアプリケーション用のデータ ドライブを使用することを検討してください。 

容量と IOPS を個別にスケーリングできるように、Ultra Disk を構成できます。 Ultra Disk を使用すると、管理者は、アプリケーションのニーズに基づいて、容量、IOPS、スループットの要件を備えたディスクをプロビジョニングできます。 

Ultra ディスクはすべての VM シリーズでサポートされているわけではなく、リージョンの可用性、冗長性、Azure Backup のサポートなど、その他の制限があります。 制限の一覧については、「[Azure Ultra ディスクの使用](../../../virtual-machines/disks-enable-ultra-ssd.md)」を参照してください。 

## <a name="standard-hdds-and-ssds"></a>Standard の HDD と SSD

[Standard の HDD](../../../virtual-machines/disks-types.md#standard-hdd) と SSD には、さまざまな待機時間や帯域幅があり、開発/テストのワークロードにのみ推奨されます。 運用環境のワークロードでは、Premium SSD を使用する必要があります。 Standard SSD (開発/テスト シナリオ) を使用している場合は、最良のパフォーマンスを得るために、ご使用の [VM サイズ](../../../virtual-machines/sizes.md?toc=/azure/virtual-machines/windows/toc.json)でサポートされる最大数のデータ ディスクを追加し、記憶域スペースでディスク ストライピングを使用することをお勧めします。

## <a name="caching"></a>キャッシュ

Premium Storage キャッシュをサポートする仮想マシンでは、Azure BlobCache またはホストキャッシュと呼ばれる追加機能を利用して、仮想マシンの IOPS とスループットの機能を拡張することができます。 Premium Storage と Premium Storage キャッシュの両方に対応した仮想マシンでは、これら 2 つの異なるストレージ帯域幅の制限があり、これらを組み合わせて使用することで記憶域のパフォーマンスを向上できます。

キャッシュを使用しない IOPS と MBps のスループットは、仮想マシンのキャッシュ不使用時のディスク スループット上限に対して不利に作用します。 最大キャッシュ制限によって、読み取り用の追加のバッファーが提供され、増加や予想外のピークに対処できます。

Premium キャッシュは追加コストなしでデータ ドライブに対する読み取りのパフォーマンスを大幅に向上させるため、このオプションがサポートされている場合には常に有効にしてください。 

Azure BlobCache (キャッシュ使用時の IOPS とスループット) の読み取りと書き込みは、仮想マシンのキャッシュ不使用時の IOPS とスループットの上限に対して不利に作用しません。

> [!NOTE]
> 4 TiB 以上のディスク (P50 以上) では、ディスク キャッシュはサポートされていません。 複数のディスクが VM に接続されている場合、4 TiB 未満の各ディスクでは、キャッシュがサポートされます。 詳細については、「[ディスク キャッシュ](../../../virtual-machines/premium-storage-performance.md#disk-caching)」を参照してください。 

### <a name="uncached-throughput"></a>キャッシュ不使用時のスループット

キャッシュ不使用時のディスクの最大 IOPS および最大スループットは、仮想マシンが処理できるリモート ストレージの上限になります。 この上限は仮想マシンで定義されており、基になるディスク ストレージの上限ではありません。 この上限は、VM にリモート接続されたデータ ドライブに対する I/O のみに適用され、一時ドライブ (`D:\` ドライブ) や OS ドライブに対するローカル I/O には適用されません。

VM で使用できるキャッシュ不使用時の IOPS とスループットの量は、お使いの仮想マシンのドキュメントで確認できます。

たとえば、[M シリーズ](../../../virtual-machines/m-series.md)のドキュメントでは、Standard_M8ms VM のキャッシュ不使用時の最大スループットについては IOPS が 5,000、キャッシュ不使用時のディスク スループットが 125 MBps であることが示されています。 

![M シリーズのキャッシュ不使用時のディスク スループットのドキュメントを示すスクリーンショット。](./media/performance-guidelines-best-practices/m-series-table.png)

同様に、Standard_M32ts は、キャッシュ不使用時のディスク IOPS として 20,000、キャッシュ不使用時のディスク スループットとして 500 MBps をサポートしていることがわかります。 この制限は、基になる Premium ディスク ストレージに関係なく、仮想マシン レベルで管理されます。

詳細については、[キャッシュ不使用時とキャッシュ使用時の上限](../../../virtual-machines/linux/disk-performance-linux.md#virtual-machine-uncached-vs-cached-limits)に関する記事を参照してください。


### <a name="cached-and-temp-storage-throughput"></a>キャッシュが有効な場合の一時ストレージのスループット

キャッシュが有効な場合の一時ストレージの最大スループットの上限は、仮想マシンのキャッシュ不使用時のスループットの上限とは別です。 Azure BlobCache は、仮想マシン ホストのランダム アクセス メモリとローカルに接続された SSD の組み合わせで構成されます。 仮想マシン内の一時ドライブ (`D:\` ドライブ) も、このローカル SSD でホストされています。

キャッシュが有効な場合の一時ストレージの最大スループット上限は、ホストキャッシュが有効になっている **場合にのみ**、ローカル一時ドライブ (`D:\` ドライブ) と Azure BlobCache e に対する I/O を制御します。 

Premium Storage でキャッシュを有効にすると、仮想マシンは、リモート ストレージのキャッシュ不使用時の VM の IOPS とスループットの上限を超えて拡張できます。  

特定の仮想マシンのみが、Premium Storage と Premium Storage キャッシュの両方をサポートしています (仮想マシンのドキュメントで確認する必要があります)。 たとえば、[M シリーズ](../../../virtual-machines/m-series.md)のドキュメントには、Premium Storage と Premium Storage キャッシュの両方がサポートされていることが示されています。 

![M シリーズの Premium Storage サポートを示すスクリーンショット。](./media/performance-guidelines-best-practices/m-series-table-premium-support.png)

キャッシュの上限は、仮想マシンのサイズによって異なります。 たとえば、Standard_M8ms VM では、キャッシュが有効な場合のディスク IOPS として 10000、キャッシュが有効な場合のディスク スループットとして 1000 MBps がサポートされ、合計キャッシュサイズは 793 GiB です。 同様に、Standard_M32ts VM では、キャッシュが有効な場合のディスク IOPS として 40000、キャッシュが有効な場合のディスク スループットとして 400 MBps がサポートされ、合計キャッシュサイズは 3174 GiB です。 

![M シリーズのキャッシュ ディスク スループットのドキュメントを示すスクリーンショット。](./media/performance-guidelines-best-practices/m-series-table-cached-temp.png)

既存の VM でホスト キャッシュを手動で有効にすることができます。 仮想マシンのキャッシュ ポリシーに変更を加える前に、すべてのアプリケーション ワークロードと SQL Server サービスを停止してください。 仮想マシンのキャッシュ設定を変更すると、ターゲット ディスクがデタッチされ、設定が適用された後に再アタッチされます。

### <a name="data-file-caching-policies"></a>データ ファイルのキャッシュ ポリシー

ストレージ キャッシュ ポリシーは、ドライブでホストされている SQL Server データ ファイルの種類によって異なります。 

次の表は、SQL Server データの種類に基づいて推奨されるキャッシュ ポリシーの概要を示しています。 

|SQL Server ディスク |推奨 |
|---------|---------|
| **データ ディスク** | SQL Server データ ファイルをホストするディスクの `Read-only` キャッシュを有効にします。 <br/> キャッシュからの読み取りは、データ ディスクからのキャッシュ不使用時の読み取りよりも高速です。 <br/> キャッシュ不使用時の IOPS とスループットに加え、キャッシュが有効な場合の IOPS とスループットによって、VM の制限内で、仮想マシンから利用可能なパフォーマンスの合計が得られますが、実際のパフォーマンスは、ワークロードのキャッシュ使用能力 (キャッシュ ヒット率) によって異なります。 <br/>|
|**トランザクション ログ ディスク**|トランザクション ログをホストするディスクのキャッシュ ポリシーを `None` に設定します。  トランザクション ログ ディスクのキャッシュを有効にしてもパフォーマンス上の利点はありません。それだけではなく、ログ ドライブで `Read-only` または `Read/Write` のキャッシュを有効にすると、ドライブに対する書き込みのパフォーマンスが低下し、データ ドライブの読み取りに使用できるキャッシュの量が減少することがあります。  |
|**OS ディスクの運用** | OS ドライブの既定のキャッシュ ポリシーは、`Read-only` または `Read/write` にできます。 <br/> OS ドライブのキャッシュ レベルは変更しないことをお勧めします。  |
| **tempdb**| 容量の理由により、tempdb を一時ドライブ は `D:\` に配置できない場合、仮想マシンのサイズを変更して、より大きな一時ドライブを取得するか、`Read-only` キャッシュが構成された別のデータドライブに tempdb を配置します。 <br/> 仮想マシンのキャッシュと一時ドライブはどちらもローカル SSD を使用します。そのため、サイズ変更する際には、エフェメラル ドライブでホストされると、tempdb の I/O が、キャッシュが有効な IOPS とスループットの仮想マシン上限に対して不利に作用することに注意してください。| 
| | | 


詳細については、「[ディスク キャッシュ](../../../virtual-machines/premium-storage-performance.md#disk-caching)」を参照してください。 


## <a name="disk-striping"></a>ディスク ストライピング

ログと tempdb を含む、SQL Server データ ファイルに必要なスループットと帯域幅を分析し、データ ディスクの数を判断します。 スループットと帯域幅の制限は、VM のサイズによって異なります。 詳細については、[VM のサイズ](../../../virtual-machines/sizes.md)に関する記事を参照してください。

データ ディスクをさらに追加し、ディスク ストライピングを使用してスループットを向上させます。 たとえば、12,000 IOPS と 180 MB/秒のスループットを必要とするアプリケーションでは、ストライピングされた 3 つの P30 ディスクを使用して 15000 IOPS と 600 MB/秒のスループットを提供できます。 

ディスク ストライピングを構成するには、「[ディスク ストライピング](storage-configuration.md#disk-striping)」を参照してください。 

## <a name="disk-capping"></a>ディスクの上限 

ディスクと仮想マシンの両方のレベルでスループットの上限があります。 IOPS の上限は、VM あたりとディスクあたりで異なり、互いに独立しています。

これらの上限を超えるリソースを消費するアプリケーションは、調整されます (制限とも呼ばれます)。 アプリケーション要件を満たし、上限に達しない、ディスク ストライプの仮想マシンとディスク サイズを選択します。 上限に対処するには、キャッシュを使用するか、アプリケーションを調整して必要なスループットを低下させます。

たとえば、12000 IOPS と 180 MB/秒を必要とするアプリケーションでは、次のことが可能です。 
- キャッシュ不使用時の最大ディスク スループットが 20,000 IOPS と 500 MBps の [Standard_M32ms](../../../virtual-machines/m-series.md) を使用します。
- 15000 IOPS と 600 MB/秒のスループットを実現するために、3 つの P30 ディスクをストライピングします。
- [Standard_M16ms](../../../virtual-machines/m-series.md) の仮想マシンを使用し、ホスト キャッシュを使用して、スループットの消費にローカル キャッシュを利用します。 

高使用率の時間帯にスケール アップするように構成された仮想マシンでは、最大 VM サイズをサポートするために必要な IOPS とスループットを備えたストレージをプロビジョニングすると同時に、全体的なディスク数を、使用対象の最小の VM SKU でサポートされている最大ディスク数以下に維持する必要があります。

ディスクの上限とキャッシュを使用して上限の回避する方法の詳細については、[ディスク IO の上限](../../../virtual-machines/disks-performance.md)に関する記事を参照してください。

> [!NOTE] 
> ディスクの上限によっては、ユーザーにとって満足できるパフォーマンスが得られる場合があります。より大きな VM にサイズ変更するのではなく、ワークロードを調整して維持し、ビジネスのコストとパフォーマンスの管理を調整してください。 


## <a name="write-acceleration"></a>書き込みアクセラレータ

書き込みアクセラレータは、[M シリーズ](https://docs.microsoft.com/azure/virtual-machines/m-series)の仮想マシン (VM) でのみ使用できるディスク機能です。 書き込みアクセラレータの目的は、大量のミッション クリティカルな OLTP ワークロードまたはデータウェアハウス環境によって、一桁の I/O 待機時間が必要な場合に、Azure Premium Storage に対する書き込みの I/O 待機時間を向上させることです。 

ログ ファイルをホストしているドライブへの書き込みの待機時間を短縮するには、書き込みアクセラレータを使用します。 SQL Server データ ファイルには書き込みアクセラレータを使用しないでください。 

書き込みアクセラレータ ディスクは、仮想マシンと同じ IOPS 上限を共有します。 アタッチされているディスクは、VM の書き込みアクセラレータの IOPS 上限を超えることはできません。  

次の表に、仮想マシンごとにサポートされるデータ ディスクの数と IOPS を示します。 

| VM の SKU  | 書き込みアクセラレータ ディスクの数  | VM あたりの書き込みアクセラレータ ディスクの IOPS  |
|---|---|---|
| M416ms_v2、M416s_v2  | 16  | 20000  |
| M128ms、M128s  | 16  | 20000  |
| M208ms_v2、M208s_v2  | 8  | 10000  |
| M64ms、M64ls、M64s  |  8 | 10000 |
| M32ms、M32ls、M32ts、M32s  | 4  | 5000  |
| M16ms、M16s  | 2 | 2500 |
| M8ms、M8s  | 1 | 1250 |

書き込みアクセラレータの使用には、いくつかの制限があります。 詳細については、「[書き込みアクセラレータを使用するときの制限事項](../../../virtual-machines/how-to-enable-write-accelerator.md#restrictions-when-using-write-accelerator)」を参照してください。


### <a name="comparing-to-azure-ultra-disk"></a>Azure Ultra Disk との比較

書き込みアクセラレータと Azure Ultra Disk の最大の違いは、書き込みアクセラレータは M シリーズのみで使用できる仮想マシン機能であり、Azure Ultra Disk はストレージのオプションであるということです。 書き込みアクセラレータは、書き込み用に最適化されたキャッシュであり、仮想マシンのサイズに基づいて独自の制限があります。 Azure Ultra Disks は、Azure Virtual Machines 用の、待機時間が低いディスク ストレージのオプションです。 

可能であれば、トランザクション ログ ディスクには、Ultra Disks ではなく、書き込みアクセラレータを使用してください。 書き込みアクセラレータをサポートしておらず、トランザクション ログの待機時間を短くする必要がある仮想マシンの場合は、Azure Ultra Disks を使用します。 

## <a name="monitor-storage-performance"></a>ストレージのパフォーマンスの監視

ストレージのニーズを評価し、ストレージのパフォーマンスを判断するには、測定対象とその指標の意味を理解する必要があります。 

[IOPS (1 秒あたりの入力/出力)](../../../virtual-machines/premium-storage-performance.md#iops) は、アプリケーションがストレージに対して 1 秒あたりに行う要求の数です。 パフォーマンス モニター カウンター `Disk Reads/sec` と `Disk Writes/sec` を使用して IOPS を測定します。 [OLTP (オンライン トランザクション処理)](/azure/architecture/data-guide/relational-data/online-transaction-processing) アプリケーションでは、最適なパフォーマンスを実現するために、IOPS を上げる必要があります。 支払い処理システム、オンライン ショッピング、販売時点管理システムなどのアプリケーションは、すべて OLTP アプリケーションの例です。

[スループット](../../../virtual-machines/premium-storage-performance.md#throughput)とは、基になるストレージに送信されるデータの量です。多くの場合、1 秒あたりのメガバイト数で測定されます。 パフォーマンス モニター カウンター`Disk Read Bytes/sec` および `Disk Write Bytes/sec` を使用して、スループットを測定します。 [データ ウェアハウス](/azure/architecture/data-guide/relational-data/data-warehousing)は、IOPS よりもスループットを最大化することによって最適化されています。 分析、レポート、ETL ワークストリーム、およびその他のビジネス インテリジェンス ターゲットのデータ ストアなどのアプリケーションは、すべてデータ ウェアハウス アプリケーションの例です。

I/O ユニットサイズは IOPS とスループットの能力に影響を及ぼします。これは、I/O サイズが小さいほど IOPS が高くなり、I/O サイズが大きいほどスループットが高くなるためです。 SQL Server では、最適な I/O サイズが自動的に選択されます。 詳細については、[アプリケーションの IOPS、スループット、待機時間の最適化](../../../virtual-machines/premium-storage-performance.md#optimize-iops-throughput-and-latency-at-a-glance)に関する記事を参照してください。 

仮想マシンとディスク レベルでの上限の検出、および AzureBlob キャッシュの使用量と正常性の検出に非常に重要な特定の Azure Monitor メトリックがあります。 監視ソリューションと Azure portal ダッシュボードに追加する主要なカウンターを判断するには、[ストレージ使用率のメトリック](../../../virtual-machines/disks-metrics.md#storage-io-utilization-metrics)に関する記事を参照してください。 

> [!NOTE]
> Azure Monitor では現在、一時ドライブのディスク レベル メトリックが提供されていません`(D:\)`。 VM のキャッシュが有効な場合の IOPS 使用率と VM のキャッシュが有効な場合の帯域幅使用率は、エフェメラル一時ドライブ `(D:\)` とホスト キャッシュの両方からの IOPS とスループットを反映します。


## <a name="next-steps"></a>次のステップ

パフォーマンスのベスト プラクティスの詳細については、このシリーズの他の記事を参照してください。
- [クイック チェックリスト](performance-guidelines-best-practices-checklist.md)
- [VM サイズ](performance-guidelines-best-practices-vm-size.md)
- [ベースラインの収集](performance-guidelines-best-practices-collect-baseline.md)

セキュリティのベスト プラクティスについては、「[Azure Virtual Machines 上の SQL Server のセキュリティに関する考慮事項](security-considerations-best-practices.md)」をご覧ください。

TPC-E および TPC_C ベンチマークによる Azure VM 上の SQL Server のパフォーマンスの詳細なテストについては、[OLTP のパフォーマンスの最適化](https://techcommunity.microsoft.com/t5/sql-server/optimize-oltp-performance-with-sql-server-on-azure-vm/ba-p/916794)に関するブログを参照してください。

SQL Server Virtual Machines に関する他の記事については、[Azure Virtual Machines 上の SQL Server の概要](sql-server-on-azure-vm-iaas-what-is-overview.md)に関するページをご覧ください。 SQL Server の仮想マシンに関するご質問については、[よくあるご質問](frequently-asked-questions-faq.md)に関するページをご覧ください。
