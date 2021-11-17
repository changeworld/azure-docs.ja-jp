---
title: Dasv5 および Dadsv5 シリーズ - Azure Virtual Machines
description: Dasv5 および Dadsv5 シリーズ VM の仕様。
author: brbell
ms.author: brbell
ms.reviewer: mimckitt
ms.service: virtual-machines
ms.subservice: vm-sizes-general
ms.topic: conceptual
ms.date: 10/8/2021
ms.openlocfilehash: c9f2c270f8794effccd8439edd57422db15b71b6
ms.sourcegitcommit: 5af89a2a7b38b266cc3adc389d3a9606420215a9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/08/2021
ms.locfileid: "131989928"
---
# <a name="dasv5-and-dadsv5-series"></a>Dasv5 および Dadsv5 シリーズ

**適用対象:** :heavy_check_mark: Linux VM :heavy_check_mark: Windows VM :heavy_check_mark: フレキシブルなスケール セット :heavy_check_mark: 均一スケール セット

Dasv5 シリーズおよび Dadsv5 シリーズは、AMD の第 3 世代 EPYC<sup>TM</sup> 7763v プロセッサを、最大 256 MB の L3 キャッシュを備えたマルチスレッド構成で利用し、汎用ワークロードを実行するための顧客オプションを増やします。 これらの仮想マシンは、小規模から中規模のデータベース、低から中程度のトラフィックの Web サーバー、アプリケーション サーバーなど、ほとんどのエンタープライズ ワークロードに関連する要件を満たすために、vCPU とメモリの組み合わせを提供します。

## <a name="dasv5-series"></a>Dasv5 シリーズ

Dasv5 シリーズ VM は、3.5 GHz のブーストされた最大周波数を達成できる、AMD の第 3 世代 EPYC<sup>TM</sup> 7763v プロセッサを利用しています。 Dasv5 シリーズのサイズでは、ほとんどの運用環境のワークロードに適した vCPU とメモリの組み合わせが提供されます。 ローカル ディスクのない新しい VM は、ローカル一時ディスクを必要としないワークロードに対して、より優れた価値提案を提供します。

> [!NOTE]
> よく寄せられる質問については、[ローカル一時ディスクを持たない Azure VM のサイズ](azure-vms-no-temp-disk.yml)に関する記事をご覧ください。

Dasv5 シリーズの仮想マシンでは、Standard SSD、Standard HDD、および Premium SSD のディスクの種類がサポートされています。 また、リージョンでの可用性に基づいて、Ultra Disk ストレージをアタッチすることもできます。 ディスク ストレージは、仮想マシンとは別に課金されます。 [ディスクの価格に関するページを参照してください](https://azure.microsoft.com/pricing/details/managed-disks/)。

[Premium Storage](premium-storage-performance.md): サポートされています <br>
[Premium Storage キャッシュ](premium-storage-performance.md): サポートされています <br>
[ライブ マイグレーション](maintenance-and-updates.md): サポートされています <br>
[メモリ保持更新](maintenance-and-updates.md): サポートされています <br>
[VM 世代サポート](generation-2.md): 第 1 世代と第 2 世代 <br>
[高速ネットワーク](../virtual-network/create-vm-accelerated-networking-cli.md):サポートされています <br>
[エフェメラル OS ディスク](ephemeral-os-disks.md):サポートされていません <br><br>

| サイズ | vCPU | メモリ:GiB | 一時ストレージ (SSD) GiB | 最大データ ディスク数 | キャッシュが無効な場合の最大ディスク スループット: IOPS/MBps | バースト キャッシュが無効なディスクの最大スループット: IOPS/MBps<sup>1</sup> | 最大 NIC 数 | 最大ネットワーク帯域幅 (Mbps) |
|---|---|---|---|---|---|---|---|---|
| Standard_D2as_v5  | 2  | 8   | リモート ストレージのみ | 4  | 3750/82    | 10000/600   | 2 | 12500  |
| Standard_D4as_v5  | 4  | 16  | リモート ストレージのみ | 8  | 6400/144   | 20000/600   | 2 | 12500  |
| Standard_D8as_v5  | 8  | 32  | リモート ストレージのみ | 16 | 12800/200  | 20000/600   | 4 | 12500  |
| Standard_D16as_v5 | 16 | 64  | リモート ストレージのみ | 32 | 25600/384  | 40000/800   | 8 | 12500 |
| Standard_D32as_v5 | 32 | 128 | リモート ストレージのみ | 32 | 51200/768  | 80000/1600  | 8 | 16000 |
| Standard_D48as_v5 | 48 | 192 | リモート ストレージのみ | 32 | 76800/1152 | 80000/2000  | 8 | 24000 |
| Standard_D64as_v5 | 64 | 256 | リモート ストレージのみ | 32 | 80000/1200 | 80000/2000  | 8 | 32000 |
| Standard_D96as_v5 | 96 | 384 | リモート ストレージのみ | 32 | 80000/1600 | 80000/2000  | 8 | 40000 |


<sup>1</sup> Dasv5 シリーズの VM では、ディスクのパフォーマンスを[バースト](disk-bursting.md)でき、一度に最大 30 分間バーストを最大にしておくことができます。


## <a name="dadsv5-series"></a>Dadsv5 シリーズ

Dadsv5 シリーズは、3.5 GHz のブーストされた最大周波数を達成できる、AMD の第 3 世代 EPYC<sup>TM</sup> 7763v プロセッサを利用しています。 Dadsv5 シリーズのサイズでは、ほとんどの運用環境のワークロードに適した vCPU、メモリ、および一時ストレージの組み合わせが提供されます。 これらの新しい VM では、50% 大きいローカル ストレージが使用されるほか、[Gen2](generation-2.md) VM の [Dav4 または Dasv4](dav4-dasv4-series.md) サイズと比べて読み取りと書き込みの両方のローカル ディスク IOPS が向上します。

Dadsv5 シリーズの仮想マシンでは、Standard SSD、Standard HDD、および Premium SSD のディスクの種類がサポートされています。 また、リージョンでの可用性に基づいて、Ultra Disk ストレージをアタッチすることもできます。 ディスク ストレージは、仮想マシンとは別に課金されます。 [ディスクの価格に関するページを参照してください](https://azure.microsoft.com/pricing/details/managed-disks/)。


[Premium Storage](premium-storage-performance.md): サポートされています <br>
[Premium Storage キャッシュ](premium-storage-performance.md): サポートされています <br>
[ライブ マイグレーション](maintenance-and-updates.md): サポートされています <br>
[メモリ保持更新](maintenance-and-updates.md): サポートされています <br>
[VM 世代サポート](generation-2.md): 第 1 世代と第 2 世代 <br>
[高速ネットワーク](../virtual-network/create-vm-accelerated-networking-cli.md):サポートされています <br>
[エフェメラル OS ディスク](ephemeral-os-disks.md):サポートされています <br><br>

| サイズ | vCPU | メモリ:GiB | 一時ストレージ (SSD) GiB | 最大データ ディスク数 | 一時ストレージの最大スループット: IOPS/MBps | キャッシュが無効な場合の最大ディスク スループット: IOPS/MBps | バースト キャッシュが無効なディスクの最大スループット: IOPS/MBps<sup>1</sup> | 最大 NIC 数 | 最大ネットワーク帯域幅 (Mbps) |
|---|---|---|---|---|---|---|---|---|---|
| Standard_D2ads_v5  | 2  | 8   | 75   | 4  | 9000/125    | 3750/82    | 10000/600  | 2 | 12500  |
| Standard_D4ads_v5  | 4  | 16  | 150  | 8  | 19000/250   | 6400/144   | 20000/600  | 2 | 12500  |
| Standard_D8ads_v5  | 8  | 32  | 300  | 16 | 38000/500   | 12800/200  | 20000/600  | 4 | 12500  |
| Standard_D16ads_v5 | 16 | 64  | 600  | 32 | 75000/1000  | 25600/384  | 40000/800  | 8 | 12500 |
| Standard_D32ads_v5 | 32 | 128 | 1200 | 32 | 150000/2000 | 51200/768  | 80000/1000 | 8 | 16000 |
| Standard_D48ads_v5 | 48 | 192 | 1800 | 32 | 225000/3000 | 76800/1152 | 80000/200 | 8 | 24000 |
| Standard_D64ads_v5 | 64 | 256 | 2400 | 32 | 300000/4000 | 80000/1200 | 80000/2000 | 8 | 32000 |
| Standard_D96ads_v5 | 96 | 384 | 3600 | 32 | 450000/4000 | 80000/1600 | 80000/2000 | 8 | 40000 |

* これらの IOPS 値は、Gen2 VM を使用して実現できます。<br>
<sup>1</sup> Dadsv5 シリーズの VM では、ディスクのパフォーマンスを[バースト](disk-bursting.md)でき、一度に最大 30 分間バーストを最大にしておくことができます。


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
