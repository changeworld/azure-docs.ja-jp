---
title: Edv4 および Edsv4 シリーズ
description: Ev4、Edv4、Esv4 および Edsv4 シリーズ VM の仕様。
author: brbell
ms.author: brbell
ms.reviewer: jushiman
ms.custom: mimckitt
ms.service: virtual-machines
ms.subservice: vm-sizes-memory
ms.topic: conceptual
ms.date: 10/20/2021
ms.openlocfilehash: eabaae542003c0c8c664cb820831bec40e47db91
ms.sourcegitcommit: 901ea2c2e12c5ed009f642ae8021e27d64d6741e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/12/2021
ms.locfileid: "132372096"
---
# <a name="edv4-and-edsv4-series"></a>Edv4 および Edsv4 シリーズ

**適用対象:** :heavy_check_mark: Linux VM :heavy_check_mark: Windows VM :heavy_check_mark: フレキシブル スケール セット :heavy_check_mark: 均一スケール セット

Edv4 と Edsv4 シリーズは、ハイパースレッド構成の Intel&reg; Xeon&reg; Platinum 8272CL (Cascade Lake) プロセッサ上で実行されます。メモリを集中的に使用するさまざまなエンタープライズ アプリケーションに最適で、最大 504 GiB の RAM、[Intel&reg; Turbo Boost Technology 2.0](https://www.intel.com/content/www/us/en/architecture-and-technology/turbo-boost/turbo-boost-technology.html)、[Intel&reg; Hyper-Threading Technology](https://www.intel.com/content/www/us/en/architecture-and-technology/hyper-threading/hyper-threading-technology.html) および [Intel&reg; Advanced Vector Extensions 512 (Intel&reg; AVX-512)](https://www.intel.com/content/www/us/en/architecture-and-technology/avx-512-overview.html) の機能を備えています。 また、[Intel&reg; ディープ ラーニング ブースト](https://software.intel.com/content/www/us/en/develop/topics/ai/deep-learning-boost.html)をサポートしています。 これらの新しい VM サイズには 50% 大きいローカル ストレージがある他、[Gen2 VM](./generation-2.md) の [Ev3/Esv3](./ev3-esv3-series.md) サイズと比べて読み取りと書き込みの両方のローカル ディスク IOPS が向上しています。 これは、3.4 GHz の全コア ターボ クロック速度を特徴としています。 

## <a name="edv4-series"></a>Edv4 シリーズ

Edv4 シリーズのサイズは、Intel&reg; Xeon&reg; Platinum 8272CL (Cascade Lake) プロセッサ上で実行されます。 Edv4 仮想マシンのサイズは、高速かつ大規模なローカル SSD ストレージ (最大 2400 GiB) に加えて、最大 504 GiB の RAM を搭載しています。 これらの仮想マシンは、メモリを集中的に使用するエンタープライズ アプリケーションや、低待機時間で高速なローカル ストレージを利用するアプリケーションに最適です。 Edv4 VM には Standard SSD および Standard HDD ディスク ストレージを接続できます。 

[ACU](acu.md): 195 - 210<br>
[Premium Storage](premium-storage-performance.md): サポートされていません<br>
[Premium Storage キャッシュ](premium-storage-performance.md): サポートされていません<br>
[ライブ マイグレーション](maintenance-and-updates.md): サポートされています<br>
[メモリ保持更新](maintenance-and-updates.md): サポートされています<br>
[VM 世代サポート](generation-2.md): 第 1 世代と第 2 世代<br>
[高速ネットワーク](../virtual-network/create-vm-accelerated-networking-cli.md): サポートされています<sup>1</sup> <br>
[エフェメラル OS ディスク](ephemeral-os-disks.md):サポートされていません <br>
[入れ子になった仮想化](/virtualization/hyper-v-on-windows/user-guide/nested-virtualization.md): サポートされています <br>
<br>

| サイズ | vCPU | メモリ:GiB | 一時ストレージ (SSD) GiB | 最大データ ディスク数 | 一時ストレージの最大スループット: IOPS/MBps<sup>*</sup>  | 最大 NIC 数|最大ネットワーク帯域幅 (Mbps) |
|---|---|---|---|---|---|---|---|
| Standard_E2d_v4<sup>1</sup>  | 2  | 16  | 75   | 4  | 9000/125    | 2 | 1000  |
| Standard_E4d_v4              | 4  | 32  | 150  | 8  | 19000/250   | 2 | 2000  |
| Standard_E8d_v4              | 8  | 64  | 300  | 16 | 38000/500   | 4 | 4000  |
| Standard_E16d_v4             | 16 | 128 | 600  | 32 | 75000/1000   | 8 | 8000  |
| Standard_E20d_v4             | 20 | 160 | 750  | 32 | 94000/1250  | 8 | 10000 |
| Standard_E32d_v4             | 32 | 256 | 1200 | 32 | 150000/2000 | 8 | 16000 |
| Standard_E48d_v4             | 48 | 384 | 1800 | 32 | 225000/3000 | 8 | 24000 |
| Standard_E64d_v4             | 64 | 504 | 2400 | 32 | 300000/4000 | 8 | 30000 |

<sup>*</sup>これらの IOP 値は、[Gen2 VM](generation-2.md)
<sup>1</sup> を使って実現されます。高速ネットワークは 1 つの NIC にのみ適用できます。 <br>

## <a name="edsv4-series"></a>Edsv4 シリーズ

Edsv4 シリーズのサイズは、Intel&reg; Xeon&reg; Platinum 8272CL (Cascade Lake) プロセッサ上で実行されます。 Edsv4 仮想マシンのサイズは、高速かつ大規模なローカル SSD ストレージ (最大 2400 GiB) に加えて、最大 504 GiB の RAM を搭載しています。 これらの仮想マシンは、メモリを集中的に使用するエンタープライズ アプリケーションや、低待機時間で高速なローカル ストレージを利用するアプリケーションに最適です。

[ACU](acu.md): 195-210<br>
[Premium Storage](premium-storage-performance.md): サポートされています<br>
[Premium Storage キャッシュ](premium-storage-performance.md): サポートされています<br>
[ライブ マイグレーション](maintenance-and-updates.md): サポートされています<br>
[メモリ保持更新](maintenance-and-updates.md): サポートされています<br>
[VM 世代サポート](generation-2.md): 第 1 世代と第 2 世代<br>
[高速ネットワーク](../virtual-network/create-vm-accelerated-networking-cli.md):サポートされています <br>
[エフェメラル OS ディスク](ephemeral-os-disks.md):サポートされています <br>
[入れ子になった仮想化](/virtualization/hyper-v-on-windows/user-guide/nested-virtualization.md): サポートされています <br>
<br>

| サイズ | vCPU | メモリ:GiB | 一時ストレージ (SSD) GiB | 最大データ ディスク数 | 一時ストレージの最大スループット: IOPS/MBps<sup>*</sup> | キャッシュが無効な場合の最大ディスク スループット: IOPS/MBps | バースト キャッシュが無効なディスクの最大スループット: IOPS/MBps<sup>1</sup> | 最大 NIC 数|最大ネットワーク帯域幅 (Mbps) |
|---|---|---|---|---|---|---|---|---|---|
| Standard_E2ds_v4<sup>4</sup>    | 2  | 16  | 75   | 4  | 9000/125    | 3200/48    | 4000/200   | 2 | 1000  |
| Standard_E4ds_v4                | 4  | 32  | 150  | 8  | 19000/250   | 6400/96    | 8000/200   | 2 | 2000  |
| Standard_E8ds_v4                | 8  | 64  | 300  | 16 | 38000/500   | 12800/192  | 16000/400  | 4 | 4000  |
| Standard_E16ds_v4               | 16 | 128 | 600  | 32 | 75000/1000   | 25600/384  | 32000/800  | 8 | 8000  |
| Standard_E20ds_v4               | 20 | 160 | 750  | 32 | 94000/1250  | 32000/480  | 40000/1000 | 8 | 10000 |
| Standard_E32ds_v4               | 32 | 256 | 1200 | 32 | 150000/2000 | 51200/768  | 64000/1600 | 8 | 16000 |
| Standard_E48ds_v4               | 48 | 384 | 1800 | 32 | 225000/3000 | 76800/1152 | 80000/2000 | 8 | 24000 |
| Standard_E64ds_v4 <sup>2</sup>  | 64 | 504 | 2400 | 32 | 300000/4000 | 80000/1200 | 80000/2000 | 8 | 30000 |
| Standard_E80ids_v4 <sup>3</sup> | 80 | 504 | 2400 | 64 | 375000/4000 | 80000/1200 | 80000/2000 | 8 | 30000 |

<sup>*</sup> これらの IOPS 値を保証するには、[Gen2 VM](generation-2.md) を使用します。<br>
<sup>1</sup> Edsv4 シリーズの VM では、ディスクのパフォーマンスを[バースト](./disk-bursting.md)でき、一度に最大 30 分間バーストを最大にしておくことができます。<br>
<sup>2</sup> [コア数を制限したサイズも提供されています](./constrained-vcpu.md)。<br>
<sup>3</sup> インスタンスは、単一の顧客専用のハードウェアに分離されます。<br>
<sup>4</sup> 高速ネットワークは、1 つの NIC にのみ適用できます。 



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


## <a name="next-steps"></a>次のステップ

[Azure コンピューティング ユニット (ACU)](acu.md) を確認することで、Azure SKU 全体の処理性能を比較できます。
