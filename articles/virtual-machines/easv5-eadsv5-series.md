---
title: Easv5 および Eadsv5 シリーズ - Azure Virtual Machines
description: Easv5 および Eadsv5 シリーズ VM の仕様。
author: brbell
ms.author: brbell
ms.reviewer: mimckitt
ms.service: virtual-machines
ms.subservice: vm-sizes-memory
ms.topic: conceptual
ms.date: 10/8/2021
ms.openlocfilehash: 3642323efddfab0c791273169e2c0fb0ec76f0b9
ms.sourcegitcommit: 5af89a2a7b38b266cc3adc389d3a9606420215a9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/08/2021
ms.locfileid: "131989909"
---
# <a name="easv5-and-eadsv5-series"></a>Easv5 および Eadsv5 シリーズ

**適用対象:** :heavy_check_mark: Linux VM :heavy_check_mark: Windows VM :heavy_check_mark: フレキシブルなスケール セット :heavy_check_mark: 均一スケール セット

Easv5 シリーズおよび Eadsv5 シリーズは、AMD の第 3 世代 EPYC<sup>TM</sup> 7763v プロセッサを、最大 256 MB の L3 キャッシュを備えたマルチスレッド構成で利用し、ほとんどのメモリ最適化されたワークロードを実行するための顧客オプションを増やします。 これらの仮想マシンでは、リレーショナル データベース サーバーやインメモリ分析ワークロードなど、ほとんどのメモリ集中型のエンタープライズ アプリケーションに関連する要件を満たす vCPU とメモリの組み合わせが提供されます。

## <a name="easv5-series"></a>Easv5 シリーズ

Easv5 シリーズは、3.5 GHz のブーストされた最大周波数を達成できる、AMD の第 3 世代 EPYC<sup>TM</sup> 7763v プロセッサを利用しています。 Easv5 シリーズのサイズでは、メモリ集中型のエンタープライズ アプリケーションに最適な vCPU とメモリの組み合わせが提供されます。 ローカル ディスクのない新しい VM は、ローカル一時ディスクを必要としないワークロードに対して、より優れた価値提案を提供します。

> [!NOTE]
> よく寄せられる質問については、[ローカル一時ディスクを持たない Azure VM のサイズ](azure-vms-no-temp-disk.yml)に関する記事をご覧ください。

Easv5 シリーズの仮想マシンでは、Standard SSD、Standard HDD、および Premium SSD のディスクの種類がサポートされています。 また、リージョンでの可用性に基づいて、Ultra Disk ストレージを接続することもできます。 ディスク ストレージは、仮想マシンとは別に課金されます。 [ディスクの価格に関するページを参照してください](https://azure.microsoft.com/pricing/details/managed-disks/)。

[Premium Storage](premium-storage-performance.md): サポートされています <br>
[Premium Storage キャッシュ](premium-storage-performance.md): サポートされています <br>
[ライブ マイグレーション](maintenance-and-updates.md): サポートされています <br>
[メモリ保持更新](maintenance-and-updates.md): サポートされています <br>
[VM 世代サポート](generation-2.md): 第 1 世代と第 2 世代 <br>
[高速ネットワーク](../virtual-network/create-vm-accelerated-networking-cli.md):サポートされています <br>
[エフェメラル OS ディスク](ephemeral-os-disks.md):サポートされていません <br><br>

| サイズ | vCPU | メモリ:GiB | 一時ストレージ (SSD) GiB | 最大データ ディスク数 | キャッシュが無効な場合の最大ディスク スループット: IOPS/MBps | バースト キャッシュが無効なディスクの最大スループット: IOPS/MBps<sup>1</sup> | 最大 NIC 数 | 最大ネットワーク帯域幅 (Mbps) |
|---|---|---|---|---|---|---|---|---|
| Standard_E2as_v5              | 2  | 16  | リモート ストレージのみ | 4  | 3750/82    | 10000/600  | 2 | 12500  |
| Standard_E4as_v5<sup>2</sup>  | 4  | 32  | リモート ストレージのみ | 8  | 6400/144   | 20000/600  | 2 | 12500  |
| Standard_E8as_v5<sup>2</sup>  | 8  | 64  | リモート ストレージのみ | 16 | 12800/200  | 20000/600  | 4 | 12500  |
| Standard_E16as_v5<sup>2</sup> | 16 | 128 | リモート ストレージのみ | 32 | 25600/384  | 40000/800  | 8 | 12500 |
| Standard_E20as_v5             | 20 | 160 | リモート ストレージのみ | 32 | 32000/480  | 64000/1000 | 8 | 12500 |
| Standard_E32as_v5<sup>2</sup> | 32 | 256 | リモート ストレージのみ | 32 | 51200/768  | 80000/1600 | 8 | 16000 |
| Standard_E48as_v5             | 48 | 384 | リモート ストレージのみ | 32 | 76800/1152 | 80000/2000 | 8 | 24000 |
| Standard_E64as_v5<sup>2</sup> | 64 | 512 | リモート ストレージのみ | 32 | 80000/1200 | 80000/2000 | 8 | 32000 |
| Standard_E96as_v5<sup>2</sup> | 96 | 672 | リモート ストレージのみ | 32 | 80000/1600 | 80000/2000 | 8 | 40000 |

<sup>1</sup> Easv5 シリーズの VM では、ディスクのパフォーマンスを[バースト](disk-bursting.md)でき、一度に最大 30 分間バーストを最大にしておくことができます。<br>
<sup>2</sup> [コア数を制限したサイズも提供されています](constrained-vcpu.md)。



## <a name="eadsv5-series"></a>Eadsv5 シリーズ

Eadsv5 シリーズは、3.5 GHz のブーストされた最大周波数を達成できる、AMD の第 3 世代 EPYC<sup>TM</sup> 7763v プロセッサを利用しています。 Eadsv5 シリーズのサイズでは、メモリ集中型のエンタープライズ アプリケーションに最適な vCPU、メモリ、一時ストレージの組み合わせが提供されます。 これらの新しい VM では、50% 大きいローカル ストレージが使用されるほか、[Gen2](generation-2.md) VM の [Eav4 または Easv4](eav4-easv4-series.md) サイズと比べて読み取りと書き込みの両方のローカル ディスク IOPS が向上します。

Eadsv5 シリーズの仮想マシンでは、Standard SSD、Standard HDD、および Premium SSD のディスクの種類がサポートされています。 また、リージョンでの可用性に基づいて、Ultra Disk ストレージを接続することもできます。 ディスク ストレージは、仮想マシンとは別に課金されます。 [ディスクの価格に関するページを参照してください](https://azure.microsoft.com/pricing/details/managed-disks/)。

[Premium Storage](premium-storage-performance.md): サポートされています <br>
[Premium Storage キャッシュ](premium-storage-performance.md): サポートされています <br>
[ライブ マイグレーション](maintenance-and-updates.md): サポートされています <br>
[メモリ保持更新](maintenance-and-updates.md): サポートされています <br>
[VM 世代サポート](generation-2.md): 第 1 世代と第 2 世代 <br>
[高速ネットワーク](../virtual-network/create-vm-accelerated-networking-cli.md):サポートされています <br>
[エフェメラル OS ディスク](ephemeral-os-disks.md):サポートされています <br><br>

| サイズ | vCPU | メモリ:GiB | 一時ストレージ (SSD) GiB | 最大データ ディスク数 | 一時ストレージの最大スループット: IOPS/MBps | キャッシュが無効な場合の最大ディスク スループット: IOPS/MBps | バースト キャッシュが無効なディスクの最大スループット: IOPS/MBps<sup>1</sup> | 最大 NIC 数 | 最大ネットワーク帯域幅 (Mbps) |
|---|---|---|---|---|---|---|---|---|---|
| Standard_E2ads_v5              | 2  | 16  | 75   | 4  | 9000/125    | 3750/82      | 10000/600  | 2 | 12500  |
| Standard_E4ads_v5<sup>2</sup>  | 4  | 32  | 150  | 8  | 19000/250   | 6400/144     | 20000/600  | 2 | 12500  |
| Standard_E8ads_v5<sup>2</sup>  | 8  | 64  | 300  | 16 | 38000/500   | 12800/200    | 20000/600  | 4 | 12500  |
| Standard_E16ads_v5<sup>2</sup> | 16 | 128 | 600  | 32 | 75000/1000  | 25600/384    | 40000/800  | 8 | 12500 |
| Standard_E20ads_v5             | 20 | 160 | 750  | 32 | 94000/1250  | 32000/480    | 64000/1000 | 8 | 12500 |
| Standard_E32ads_v5<sup>2</sup> | 32 | 256 | 1200 | 32 | 150000/2000 | 51200/768    | 80000/1600 | 8 | 16000 |
| Standard_E48ads_v5             | 48 | 384 | 1800 | 32 | 225000/3000 | 76800/1152   | 80000/2000 | 8 | 24000 |
| Standard_E64ads_v5<sup>2</sup> | 64 | 512 | 2400 | 32 | 300000/4000 | 80000/1200   | 80000/2000 | 8 | 32000 |
| Standard_E96ads_v5<sup>2</sup> | 96 | 672 | 3600 | 32 | 450000/4000 | 80000/1600   | 80000/2000 | 8 | 40000 |

* これらの IOPS 値を実現するには、Gen2 VM を使用します。<br>
<sup>1</sup> Eadsv5 シリーズの VM では、ディスクのパフォーマンスを[バースト](disk-bursting.md)でき、一度に最大 30 分間バーストを最大にしておくことができます。<br>
<sup>2</sup> [コア数を制限したサイズも提供しています](constrained-vcpu.md)。


[!INCLUDE [virtual-machines-common-sizes-table-defs](../../includes/virtual-machines-common-sizes-table-defs.md)]

## <a name="other-sizes-and-information"></a>その他のサイズと情報

- [汎用](sizes-general.md)
- [メモリの最適化](sizes-memory.md)
- [ストレージの最適化](sizes-storage.md)
- [GPU の最適化](sizes-gpu.md)
- [ハイ パフォーマンス コンピューティング](sizes-hpc.md)
- [旧世代](sizes-previous-gen.md)

料金計算ツール:[料金計算ツール](https://azure.microsoft.com/pricing/calculator/)

ディスクの種類の詳細については、「[Azure で利用できるディスクの種類](disks-types.md)」を参照してください

## <a name="next-steps"></a>次のステップ

[Azure コンピューティング ユニット (ACU)](acu.md) を確認することで、Azure SKU 全体の処理性能を比較できます。
