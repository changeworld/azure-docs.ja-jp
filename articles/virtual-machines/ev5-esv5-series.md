---
title: Ev5 および Esv5 シリーズ - Azure Virtual Machines
description: Ev5 および Esv5 シリーズ VM の仕様。
author: styli365
ms.author: joelpell
ms.reviewer: joelpell
ms.custom: mimckitt
ms.service: virtual-machines
ms.subservice: vm-sizes-memory
ms.topic: conceptual
ms.date: 10/20/2021
ms.openlocfilehash: 897ae578ca1ff088e2f3f937bb9f00977ee233ad
ms.sourcegitcommit: e1037fa0082931f3f0039b9a2761861b632e986d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/12/2021
ms.locfileid: "132399354"
---
# <a name="ev5-and-esv5-series"></a>Ev5 および Esv5 シリーズ

**適用対象:** :heavy_check_mark: Linux VM :heavy_check_mark: Windows VM :heavy_check_mark: フレキシブルなスケール セット :heavy_check_mark: 均一スケール セット

Ev5 および Esv5 シリーズの仮想マシンは、[ハイパースレッド](https://www.intel.com/content/www/us/en/architecture-and-technology/hyper-threading/hyper-threading-technology.html)構成の第 3 世代 Intel&reg; Xeon&reg; Platinum 8370C (Ice Lake) プロセッサ上で実行され、ほとんどの汎用ワークロードに対してより優れた価値提案が提供されています。 この新しいプロセッサは、3.5 GHz の全コア ターボ クロック速度を特徴とし、[Intel&reg; Turbo Boost Technology](https://www.intel.com/content/www/us/en/architecture-and-technology/turbo-boost/turbo-boost-technology.html)、[Intel&reg; Advanced-Vector Extensions 512 (Intel&reg; AVX-512)](https://www.intel.com/content/www/us/en/architecture-and-technology/avx-512-overview.html)、[Intel&reg; Deep Learning Boost](https://software.intel.com/content/www/us/en/develop/topics/ai/deep-learning-boost.html) の機能を備えています。 これらの仮想マシンは、最大 672 GiB の RAM を搭載しており、メモリ集中型のエンタープライズ アプリケーション、リレーショナル データベース サーバー、メモリ内分析ワークロードに最適です。 Ev5 および Esv5 シリーズは、ローカル一時ディスクを必要としないワークロードに対して、より優れた価値提案を提供します。 ローカル ディスクを使用する同様の仮想マシンの詳細については、[Edv5 および Edsv5 シリーズの VM](edv5-edsv5-series.md) に関する記事をご覧ください。

> [!NOTE]
> よく寄せられる質問については、[ローカル一時ディスクを持たない Azure VM のサイズ](azure-vms-no-temp-disk.yml)に関する記事をご覧ください。

## <a name="ev5-series"></a>Ev5 シリーズ

Ev5 シリーズの仮想マシンは、第 3世代 Intel® Xeon® Platinum 8370C (Ice Lake) プロセッサ上で実行され、最大 3.5 GHz の全コア ターボ クロック速度に達します。  これらの仮想マシンでは、最大 104 個の vCPU と 672 GiB の RAM が提供されます。 Ev5 シリーズの仮想マシンには一時ストレージがないため、エントリ価格が低くなります。

Ev5 シリーズでサポートされているディスクの種類は、Standard SSD と Standard HDD です。 Premium SSD または Ultra Disk のストレージを使用するには、Esv5 シリーズの仮想マシンを選択します。 ディスク ストレージは、仮想マシンとは別に課金されます。 [ディスクの価格に関するページを参照してください](https://azure.microsoft.com/pricing/details/managed-disks/)。

[Premium Storage](premium-storage-performance.md): サポートされていません<br>
[Premium Storage キャッシュ](premium-storage-performance.md): サポートされていません<br>
[ライブ マイグレーション](maintenance-and-updates.md): サポートされています<br>
[メモリ保持更新](maintenance-and-updates.md): サポートされています<br>
[VM 世代サポート](generation-2.md): 第 1 世代と第 2 世代<br>
[高速ネットワーク](../virtual-network/create-vm-accelerated-networking-cli.md): 必須 <br>
[エフェメラル OS ディスク](ephemeral-os-disks.md):サポートされていません <br>
[入れ子になった仮想化](/virtualization/hyper-v-on-windows/user-guide/nested-virtualization): サポートされています <br>
<br>

| サイズ | vCPU | メモリ:GiB | 一時ストレージ (SSD) GiB | 最大データ ディスク数 | 最大 NIC 数|最大ネットワーク帯域幅 (Mbps) |
|---|---|---|---|---|---|---|
| Standard_E2_v5<sup>1、2</sup>  | 2   | 16  | リモート ストレージのみ | 4  | 2 | 12500 |
| Standard_E4_v5                | 4   | 32  | リモート ストレージのみ | 8  | 2 | 12500 |
| Standard_E8_v5                | 8   | 64  | リモート ストレージのみ | 16 | 4 | 12500 |
| Standard_E16_v5               | 16  | 128 | リモート ストレージのみ | 32 | 8 | 12500 |
| Standard_E20_v5               | 20  | 160 | リモート ストレージのみ | 32 | 8 | 12500  |
| Standard_E32_v5               | 32  | 256 | リモート ストレージのみ | 32 | 8 | 16000  |
| Standard_E48_v5               | 48  | 384 | リモート ストレージのみ | 32 | 8 | 24000  |
| Standard_E64_v5               | 64  | 512 | リモート ストレージのみ | 32 | 8 | 30000  |
| Standard_E96_v5               | 96  | 672 | リモート ストレージのみ | 32 | 8 | 30000  |
| Standard_E104i_v5<sup>3</sup> | 104 | 672 | リモート ストレージのみ | 64 | 8 | 100000 |

<sup>1</sup> 高速ネットワークは必須であり、すべての Ev5 仮想マシンで既定でオンになっています。<br>
<sup>2</sup> 高速ネットワークは 2 つの NIC に適用できます。<br>
<sup>3</sup> インスタンスは、単一の顧客専用のハードウェアに[分離](../security/fundamentals/isolation-choices.md#compute-isolation)されます。<br>

## <a name="esv5-series"></a>Esv5 シリーズ

Esv5 シリーズの仮想マシンは、第 3世代 Intel® Xeon® Platinum 8370C (Ice Lake) プロセッサ上で実行され、最大 3.5 GHz の全コア ターボ クロック速度に達します。  これらの仮想マシンでは、最大 104 個の vCPU と 672 GiB の RAM が提供されます。 Esv5 シリーズの仮想マシンには一時ストレージがないため、エントリ価格が低くなります。

Esv5 シリーズでサポートされているディスクの種類は、Standard SSD、Standard HDD、Premium SSD です。 また、リージョンで Ultra Disk ストレージを使用できる場合は、このストレージも接続できます。 ディスク ストレージは、仮想マシンとは別に課金されます。 [ディスクの価格に関するページを参照してください](https://azure.microsoft.com/pricing/details/managed-disks/)。

[Premium Storage](premium-storage-performance.md): サポートされています<br>
[Premium Storage キャッシュ](premium-storage-performance.md): サポートされています<br>
[ライブ マイグレーション](maintenance-and-updates.md): サポートされています<br>
[メモリ保持更新](maintenance-and-updates.md): サポートされています<br>
[VM 世代サポート](generation-2.md): 第 1 世代と第 2 世代<br>
[高速ネットワーク](../virtual-network/create-vm-accelerated-networking-cli.md): 必須 <br>
[エフェメラル OS ディスク](ephemeral-os-disks.md):サポートされていません <br>
[入れ子になった仮想化](/virtualization/hyper-v-on-windows/user-guide/nested-virtualization): サポートされています <br>
<br>

| サイズ | vCPU | メモリ:GiB | 一時ストレージ (SSD) GiB | 最大データ ディスク数 | キャッシュが無効な場合の最大ディスク スループット: IOPS/MBps | キャッシュ不使用時の最大バースト ディスク スループット (IOPS/MBps)<sup>5</sup> | 最大 NIC 数 | 最大ネットワーク帯域幅 (Mbps) |
|---|---|---|---|---|---|---|---|---|
| Standard_E2s_v5<sup>1、2</sup>  | 2   | 16  | リモート ストレージのみ | 4  | 3750/85     | 10000/1200 | 2 | 12500 |
| Standard_E4s_v5                | 4   | 32  | リモート ストレージのみ | 8  | 6400/145    | 20000/1200 | 2 | 12500 |
| Standard_E8s_v5                | 8   | 64  | リモート ストレージのみ | 16 | 12800/290   | 20000/1200 | 4 | 12500 |
| Standard_E16s_v5               | 16  | 128 | リモート ストレージのみ | 32 | 25600/600   | 40000/1200 | 8 | 12500 |
| Standard_E20s_v5               | 20  | 160 | リモート ストレージのみ | 32 | 32000/750   | 64000/1600 | 8 | 12500  |
| Standard_E32s_v5               | 32  | 256 | リモート ストレージのみ | 32 | 51200/865   | 80000/2000 | 8 | 16000  |
| Standard_E48s_v5               | 48  | 384 | リモート ストレージのみ | 32 | 76800/1315  | 80000/3000 | 8 | 24000  |
| Standard_E64s_v5               | 64  | 512 | リモート ストレージのみ | 32 | 80000/1735  | 80000/3000 | 8 | 30000  |
| Standard_E96s_v5<sup>3</sup>   | 96  | 672 | リモート ストレージのみ | 32 | 80000/2600  | 80000/4000 | 8 | 35000  |
| Standard_E104is_v5<sup>4</sup> | 104 | 672 | リモート ストレージのみ | 64 | 120000/4000 | 120000/4000 | 8 | 100000 |

<sup>1</sup> 高速ネットワークは必須であり、すべての Esv5 仮想マシンで既定でオンになっています。<br>
<sup>2</sup> 高速ネットワークは 2 つの NIC に適用できます。<br>
<sup>3</sup> [コア数を制限した](constrained-vcpu.md)サイズも提供しています。<br>
<sup>4</sup> インスタンスは、単一の顧客専用のハードウェアに[分離](../security/fundamentals/isolation-choices.md#compute-isolation)されます。<br>
<sup>5</sup> Esv5 シリーズの VM では、ディスクのパフォーマンスを[バースト](disk-bursting.md)でき、一度に最大 30 分間バーストを最大にしておくことができます。

[!INCLUDE [virtual-machines-common-sizes-table-defs](../../includes/virtual-machines-common-sizes-table-defs.md)]

## <a name="other-sizes-and-information"></a>その他のサイズと情報

- [汎用](sizes-general.md)
- [メモリの最適化](sizes-memory.md)
- [ストレージの最適化](sizes-storage.md)
- [GPU の最適化](sizes-gpu.md)
- [ハイ パフォーマンス コンピューティング](sizes-hpc.md)
- [旧世代](sizes-previous-gen.md)

料金計算ツール: [料金計算ツール](https://azure.microsoft.com/pricing/calculator/)

ディスクの種類の詳細情報: [ディスクの種類](./disks-types.md#ultra-disks)
