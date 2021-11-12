---
title: Azure IaaS VM 用のディスクの種類の選択 - マネージド ディスク
description: Ultra Disks、Premium SSD、Standard SSD、Standard HDD など、仮想マシンで使用できる Azure ディスクの種類について説明します。
author: roygara
ms.author: rogarana
ms.date: 11/03/2021
ms.topic: conceptual
ms.service: storage
ms.subservice: disks
ms.custom: references_regions
ms.openlocfilehash: 5e4eb581f8cf9b95e9a8ba4dffd442efc6c055ef
ms.sourcegitcommit: 2cc9695ae394adae60161bc0e6e0e166440a0730
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/03/2021
ms.locfileid: "131505443"
---
# <a name="azure-managed-disk-types"></a>Azure マネージド ディスクの種類

**適用対象:** :heavy_check_mark: Linux VM :heavy_check_mark: Windows VM :heavy_check_mark: フレキシブル スケール セット :heavy_check_mark: ユニフォーム スケール セット

現在、Azure マネージド ディスクには 4 種類のディスクがあり、それぞれ特定の顧客シナリオに対処することを目的としています。

- Ultra ディスク
- Premium SSD (ソリッドステート ドライブ)
- Standard SSD
- Standard HDD (ハード ディスク ドライブ)

## <a name="disk-type-comparison"></a>ディスクの種類の比較

次の表では、使用するディスクの種類を決定する場合に役立つように、4 種類のディスクを比較しています。

|         | Ultra ディスク | Premium SSD | Standard SSD | <nobr>Standard HDD</nobr> |
| ------- | ---------- | ----------- | ------------ | ------------ |
| **ディスクの種類** | SSD | SSD | SSD | HDD |
| **シナリオ**  | [SAP HANA](workloads/sap/hana-vm-operations-storage.md) やトップ レベルのデータベース (たとえば SQL や Oracle) などの I/O 集約型のワークロードと、その他のトランザクションが多いワークロード。 | 運用環境のワークロードやパフォーマンスに影響されやすいワークロード | Web サーバー、あまり使用されていないエンタープライズ アプリケーション、および開発/テスト | バックアップ、重要ではない、不定期に起こるアクセス |
| **最大ディスク サイズ** | 65,536 ギビバイト (GiB) | 32,767 GiB | 32,767 GiB | 32,767 GiB |
| **最大スループット** | 4,000 MB/秒 | 900 MB/秒 | 750 MB/秒 | 500 MB/秒 |
| **最大 IOPS** | 160,000 | 20,000 | 6,000 | 2,000 |

## <a name="ultra-disks"></a>Ultra ディスク

Azure Ultra Disks は、Azure 仮想マシン (VM) 用の最高性能のストレージ オプションです。 VM を再起動しなくても、Ultra ディスクのパフォーマンス パラメーターを変更できます。 Ultra ディスクは、SAP HANA、最上位層のデータベース、トランザクションの多いワークロードなどのデータ集中型のワークロードに適しています。

Ultra ディスクはデータ ディスクとして使用する必要があり、空のディスクとしてのみ作成できます。 Microsoft では、Premium ソリッドステート ドライブ (SSD) をオペレーティング システム (OS) ディスクとして使用することを推奨しています。

### <a name="ultra-disk-size"></a>Ultra Disk サイズ

Azure Ultra ディスクからは、既定でサブスクリプション別にリージョンあたり最大 32 TiB が提供されますが、Ultra ディスクでは要求があればそれを超える容量が提供されます。 容量の増加を依頼するには、クォータの増加を要求するか、Azure サポートにお問い合わせください。

次の表では、使用するものを決定する場合に役立つように、ディスク サイズとパフォーマンス上限を比較しています。

|ディスク サイズ (GiB)  |IOPS の上限  |スループットの上限 (MBps)  |
|---------|---------|---------|
|4     |1,200         |300         |
|8     |2,400         |600         |
|16     |4,800         |1,200         |
|32     |9,600         |2,400         |
|64     |19,200         |4,000         |
|128     |38,400         |4,000         |
|256     |76,800         |4,000         |
|512     |153,600         |4,000         |
|1,024 - 65,536 (この範囲内のサイズは 1 TiB ずつ増えます)     |160,000         |4,000         |

Ultra ディスク は、ミリ秒未満の遅延と、前出の表に示した目標 IOPS とスループットを 99.99% の時間で提供するように設計されています。

### <a name="ultra-disk-performance"></a>Ultra ディスクのパフォーマンス

Ultra ディスクは、ディスクをプロビジョニングする前と後の両方で、IOPS とスループットを個別に構成できる柔軟なパフォーマンス構成モデルを特長としています。 Ultra Disk は 4 GiB から最大 64 TiB までのいくつかの固定サイズで用意されています。

### <a name="ultra-disk-iops"></a>Ultra Disk の IOPS

Ultra Disk は、300 IOPS/GiB の IOPS 制限をサポートし、ディスクあたり最大 160,000 IOPS をサポートします。 ディスクのターゲット IOPS を達成するには、選択したディスクの IOPS が VM の IOPS 制限未満であることを確認してください。

現在、一般提供されているサイズの 1 つの VM の IOPS の上限は 80,000 です。 IOPS がさらに大きい Ultra ディスクは、複数の VM をサポートする共有ディスクとして使用できます。

ディスクあたりに保証されている最小 IOPS は 1 IOPS/GiB で、全体のベースラインの最小値は 100 IOPS です。 たとえば、4 GiB の Ultra Disk をプロビジョニングした場合、そのディスクの最小 IOPS は 8 ではなく 100 です。

IOPS の詳細については、「[仮想マシンとディスクのパフォーマンス](disks-performance.md)」を参照してください。

### <a name="ultra-disk-throughput"></a>Ultra Disk のスループット

1 つの Ultra Disk のスループットは、プロビジョニングされた IOPS ごとに 256 KiB/秒に制限され、ディスクあたり最大 4000 MBps に制限されます (MBps = 秒あたり 10^6 バイト)。 各ディスクで保証されている最小スループットは、プロビジョニングされた IOPS ごとに 4 KiB/秒で、全体のベースラインの最小値は 1 MBps です。

実行時に、仮想マシンからディスクをデタッチすることなく、Ultra Disk の IOPS とスループット パフォーマンスを調整できます。 ディスクでパフォーマンス サイズ変更操作を実行した場合、変更が有効になるまでに最大で 1 時間かかることがあります。 24 時間の期間中、最大 4 回のパフォーマンス サイズ変更操作が許可されます。

パフォーマンス帯域幅の容量が不足しているために、パフォーマンス サイズ変更操作が失敗する可能性があります。

### <a name="ultra-disk-limitations"></a>Ultra Disk の制限

[!INCLUDE [managed-disks-ultra-disks-GA-scope-and-limitations](../../includes/managed-disks-ultra-disks-GA-scope-and-limitations.md)]

Ultra Disk の使用を開始したい場合は、[Ultra Disk の使用](disks-enable-ultra-ssd.md)に関する記事を参照してください。

## <a name="premium-ssds"></a>Premium SSD

Azure Premium SSD では、IO (入出力) を集中的に行うワークロードが存在する仮想マシン (VM) 向けに、高パフォーマンスで待ち時間の少ないディスク サポートが提供されます。 Premium SSD の速度とパフォーマンスを活用するために、既存の VM ディスクを Premium SSD に移行することができます。 Premium SSD はミッションクリティカルな運用アプリケーションに適していますが、互換性のある VM シリーズでのみ使用できます。

Premium Storage に互換性のあるサイズなど、Windows または Linux 用の Azure VM の個別の種類とサイズについて詳しくは、「[Azure の仮想マシンのサイズ](sizes.md)」を参照してください。 個々の VM サイズの記事を確認し、Premium Storage と互換性があるかどうかを判断する必要があります。

### <a name="premium-ssd-size"></a>Premium SSD のサイズ
[!INCLUDE [disk-storage-premium-ssd-sizes](../../includes/disk-storage-premium-ssd-sizes.md)]

容量、IOPS、スループットは、Premium Storage ディスクがプロビジョニングされるときに保証されます。 たとえば、P50 ディスクを作成した場合、対象のディスクに 4,095 GB のストレージ容量、7,500 IOPS、および 250 MB/秒のスループットがプロビジョニングされます。 アプリケーションでは、容量とパフォーマンスのすべてまたは一部を使用できます。 Premium SSD は、1 桁のミリ秒の遅延と、前出の表に示した目標 IOPS とスループットを、99.9% の確率で提供するように設計されています。

### <a name="premium-ssd-bursting"></a>Premium SSD のバースト

Premium SSD はディスク バースティングを提供し、IO パターンの予測できない変更に対して優れた耐性を示します。 ディスク バースティングは、OS ディスクのブート時や、スパイクの多いトラフィックがあるアプリケーションに特に役立ちます。 Azure ディスクのバーストのしくみの詳細については、「[ディスク レベルのバースト](disk-bursting.md#disk-level-bursting)」を参照してください。

### <a name="premium-ssd-transactions"></a>Premium SSD のトランザクション

Premium SSD では、スループットが 256 KiB 以下の I/O 操作は、それぞれ単一の I/O 操作とみなされます。 スループットが 256 KiB を超える I/O 操作は、サイズが 256 KiB の複数の I/O とみなされます。

## <a name="standard-ssds"></a>Standard SSD

Azure Standard SSD は、低い IOPS レベルで一貫したパフォーマンスを必要とするワークロード向けに最適化されています。 オンプレミスのハード ディスク ドライブ (HDD) ソリューションでサポートされているさまざまなワークロードを抱えるお客様にとって、特に適した選択肢です。 Standard HDD に比べて、Standard SSD は、可用性、一貫性、信頼性、遅延が優れています。 Standard SSD は、Web サーバー、低い IOPS のアプリケーション サーバー、使用の少ないエンタープライズ アプリケーション、非運用ワークロードに適しています。 Standard HDD のように、Standard SSD はすべての Azure VM で使用できます。

### <a name="standard-ssd-size"></a>Standard SSD のサイズ

[!INCLUDE [disk-storage-standard-ssd-sizes](../../includes/disk-storage-standard-ssd-sizes.md)]

Standard SSD は、1 桁のミリ秒の遅延と、前出の表に示されている上限までの IOPS とスループットを 99% の時間で提供するように設計されています。 実際の IOPS とスループットは、トラフィック パターンによって異なる場合があります。 Standard SSD は、HDD ディスクよりも一貫したパフォーマンスを提供し、遅延が少なくなります。

### <a name="standard-ssd-transactions"></a>Standard SSD のトランザクション

Standard SSD では、スループットが 256 KiB 以下の I/O 操作は、それぞれ単一の I/O 操作とみなされます。 スループットが 256 KiB を超える I/O 操作は、サイズが 256 KiB の複数の I/O とみなされます。 これらのトランザクションでは、課金対象のコストが発生します。

### <a name="standard-ssd-bursting"></a>Standard SSD のバースト

Standard SSD はディスク バースティングを提供し、IO パターンの予測できない変更に対して優れた耐性を示します。 OS ブート ディスクとトラフィック スパイクの影響を受けやすいアプリケーションは、どちらもディスク バースティングから利点が得られます。 Azure ディスクのバーストのしくみの詳細については、「[ディスク レベルのバースト](disk-bursting.md#disk-level-bursting)」を参照してください。

## <a name="standard-hdds"></a>Standard HDD

Azure Standard HDD によって、遅延に耐性があるワークロードを実行する VM 向けの信頼性の高い低コストのディスク サポートが提供されます。 Standard Storage では、データは HDD に保存され、パフォーマンスは SSD ベースのディスクよりも大きく変化する可能性があります。 Standard HDD は、ほとんどの IO 操作で、書き込み待機時間が 10 ミリ秒未満および読み取り待機時間が 20 ミリ秒未満になるように設計されています。 ただし、実際のパフォーマンスは IO サイズやワークロード パターンによって異なる場合があります。 VM を使用するとき、開発/テスト シナリオや重要度の低いワークロードには Standard HDD ディスクを使用できます。 Standard HDD はすべての Azure リージョンで使用可能であり、すべての Azure VM で使用できます。

### <a name="standard-hdd-size"></a>Standard HDD のサイズ
[!INCLUDE [disk-storage-standard-hdd-sizes](../../includes/disk-storage-standard-hdd-sizes.md)]

### <a name="standard-hdd-transactions"></a>Standard HDD のトランザクション

Standard HDD では、各 I/O 操作は、I/O サイズに関係なく、単一のトランザクションとみなされます。 これらのトランザクションは、課金への影響があります。

## <a name="billing"></a>課金

マネージド ディスクを使用するときには、課金に関する次の考慮事項が適用されます。

- ディスクの種類
- マネージド ディスクのサイズ
- スナップショット
- 送信データ転送
- トランザクション数

**マネージド ディスクのサイズ**: マネージド ディスクは、それらのプロビジョニング済みサイズに従って課金されます。 Azure では、プロビジョニング済みサイズ (切り上げたもの) は、提供される最も近いディスク サイズにマップされます。 提供されるディスク サイズの詳細については、前の表を参照してください。 各ディスクは、サポートされているプロビジョニング済みディスク サイズのオファリングにマップされ、それに応じて課金されます。 たとえば、200 GiB の Standard SSD をプロビジョニングした場合、E15 (256 GiB) のディスク サイズのオファーにマップされます。 プロビジョニングされたディスクには、ストレージ サービスの月額料金を使用して、時間割りで計算して課金されます。 たとえば、E10 ディスクをプロビジョニングし、20 時間の使用後に削除するとします。 この場合、ディスクに書き込まれたデータの量に関係なく、E10 オファリングの 20 時間分に対して課金されます。

**スナップショット**: スナップショットは、使用されているサイズに基づいて請求されます。 たとえば、64 GiB のプロビジョニング済み容量でマネージド ディスクのスナップショットを作成し、実際に使用されたデータ サイズが 10 GiB であるとします。 この場合、スナップショットは使用されたデータ サイズである 10 GiB 分のみ課金されます。

スナップショットの詳細については、[マネージド ディスクの概要](managed-disks-overview.md#managed-disk-snapshots)に関するページのスナップショットのセクションを参照してください。

**送信データ転送**: [送信データ転送](https://azure.microsoft.com/pricing/details/bandwidth/) (Azure データ センターから送信されるデータ) では、帯域幅の使用量に対して課金されます。

**トランザクション**: Standard マネージド ディスクで実行されるトランザクションの数に対して課金されます。 Standard SSD では、スループットが 256 KiB 以下の I/O 操作は、それぞれ単一の I/O 操作とみなされます。 スループットが 256 KiB を超える I/O 操作は、サイズが 256 KiB の複数の I/O とみなされます。 Standard HDD では、各 IO 操作は、I/O サイズに関係なく、単一のトランザクションとみなされます。

トランザクション コストを含め、Managed Disks の価格の詳細については、「[Managed Disks の価格](https://azure.microsoft.com/pricing/details/managed-disks)」をご覧ください。

### <a name="ultra-disks-vm-reservation-fee"></a>Ultra ディスクの VM 予約料金

Azure VM には、Ultra Disk と互換性があるかどうかを示す機能があります。 Ultra ディスク互換 VM では、コンピューティング VM インスタンスとブロック ストレージ スケール ユニット間に専用の帯域幅容量が割り当てられてパフォーマンスが最適化され、待機時間が減少します。 この容量を VM に追加すると、予約料金がかかります。 予約料金は、Ultra ディスクがアタッチされていない VM 上で Ultra ディスク キャパシティを有効にした場合にのみかかります。 Ultra ディスク互換 VM に Ultra ディスクがアタッチされている場合、予約料金は適用されません。 この料金は、VM 上にプロビジョニングされている vCPU ごとにかかります。

> [!Note]
> [制約付きコア VM サイズ](constrained-vcpu.md)の場合、予約料金は、制限付きコアではなく実際の vCPU 数に基づいています。 Standard_E32-8s_v3 の場合、予約料金は 32 コアに基づきます。

Ultra Disk の価格について詳しくは、[Azure ディスクの価格](https://azure.microsoft.com/pricing/details/managed-disks/)に関するページをご覧ください。

### <a name="azure-disk-reservation"></a>Azure ディスク予約

ディスク予約により、1 年分のディスク ストレージの前払い購入で割引が提供されるため、総コストが削減されます。 ディスク予約を購入する場合は、ターゲットリージョンで特定のディスク SKU を選択します。 たとえば、米国中部リージョンでは、1 年間に 5 つの P30 (1 TiB) の Premium SSD を選択できます。 ディスク予約エクスペリエンスは、Azure 予約 VM インスタンスに似ています。 VM 予約とディスク予約をバンドルすると、最大限に節約できます。 現時点で、Azure ディスク予約には、すべての運用リージョンで P30 (1 TiB) から P80 (32 TiB) までの Premium SSD SKU の 1 年間のコミットメント プランが用意されています。 予約済みディスクの価格の詳細については、[Azure ディスクの価格のページ](https://azure.microsoft.com/pricing/details/managed-disks/)を参照してください。

## <a name="next-steps"></a>次のステップ

作業を開始するには、「[Managed Disks の価格](https://azure.microsoft.com/pricing/details/managed-disks/)」を参照してください。
