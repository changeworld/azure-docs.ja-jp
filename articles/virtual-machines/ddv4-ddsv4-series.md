---
title: Ddv4 および Ddsv4 シリーズ
description: Dv4、Ddv4、Dsv4、および Ddsv4 シリーズ VM の仕様。
author: brbell
ms.author: brbell
ms.reviewer: mimckitt
ms.custom: mimckitt
ms.service: virtual-machines
ms.subservice: vm-sizes-general
ms.topic: conceptual
ms.date: 06/01/2020
ms.openlocfilehash: 0ac7719d108ab888b5573d03a8c45d545bcb135d
ms.sourcegitcommit: e1037fa0082931f3f0039b9a2761861b632e986d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/12/2021
ms.locfileid: "132398670"
---
# <a name="ddv4-and-ddsv4-series"></a>Ddv4 および Ddsv4 シリーズ

**適用対象:** :heavy_check_mark: Linux VM :heavy_check_mark: Windows VM :heavy_check_mark: フレキシブル スケール セット :heavy_check_mark: ユニフォーム スケール セット

Ddv4 および Ddsv4 シリーズは、ハイパースレッド構成の Intel&reg; Xeon&reg; Platinum 8272CL (Cascade Lake) プロセッサ上で実行され、ほとんどの汎用ワークロードに対してより優れた価値提案が提供されています。 3\.4 GHz の全コア ターボ クロック速度を特徴とし、[Intel&reg; Turbo Boost Technology 2.0](https://www.intel.com/content/www/us/en/architecture-and-technology/turbo-boost/turbo-boost-technology.html)、[Intel&reg; Hyper-Threading Technology](https://www.intel.com/content/www/us/en/architecture-and-technology/hyper-threading/hyper-threading-technology.html)、[Intel&reg; Advanced Vector Extensions 512 (Intel&reg; AVX-512)](https://www.intel.com/content/www/us/en/architecture-and-technology/avx-512-overview.html) の機能を備えています。 また、[Intel&reg; Deep Learning Boost](https://software.intel.com/content/www/us/en/develop/topics/ai/deep-learning-boost.html) がサポートされています。 これらの新しい VM サイズでは、50% 大きいローカル ストレージが使用されるほか、[Gen2 VM](./generation-2.md) の [Dv3/Dsv3](./dv3-dsv3-series.md) サイズと比べて読み取りと書き込みの両方のローカル ディスク IOPS が向上します。

D シリーズのユース ケースには、エンタープライズ級のアプリケーション、リレーショナル データベース、メモリ内キャッシュ、分析があります。

## <a name="ddv4-series"></a>Ddv4 シリーズ

Ddv4 シリーズのサイズは、Intel&reg; Xeon&reg; Platinum 8272CL (Cascade Lake) 上で実行されます。 Ddv4 シリーズは、ほとんどの運用環境のワークロードに適した vCPU、メモリ、一時ディスクの組み合わせを提供します。

新しい Ddv4 VM のサイズには、高速で大規模なローカル SSD ストレージ (最大 2400 GiB) が含まれており、低待機時間で高速のローカル ストレージを利用するアプリケーション向けに設計されています。そのようなアプリケーションとしては、一時ストレージへの高速な読み取り/書き込みを必要とするものや、キャッシュまたは一時ファイル用の一時ストレージを必要とするものなどがあります。 Ddv4 VM には Standard SSD および Standard HDD ストレージを接続できます。 リモート データ ディスク ストレージは、仮想マシンとは別に課金されます。

[ACU](acu.md): 195-210<br>
[Premium Storage](premium-storage-performance.md): サポートされていません<br>
[Premium Storage キャッシュ](premium-storage-performance.md): サポートされていません<br>
[ライブ マイグレーション](maintenance-and-updates.md): サポートされています<br>
[メモリ保持更新](maintenance-and-updates.md): サポートされています<br>
[VM 世代サポート](generation-2.md): 第 1 世代と第 2 世代<br>
[高速ネットワーク](../virtual-network/create-vm-accelerated-networking-cli.md):サポートされています<br>
[エフェメラル OS ディスク](ephemeral-os-disks.md):サポートされています <br>
[入れ子になった仮想化](/virtualization/hyper-v-on-windows/user-guide/nested-virtualization): サポートされています <br>
<br> 

| サイズ | vCPU | メモリ:GiB | 一時ストレージ (SSD) GiB | 最大データ ディスク数 | 一時ストレージの最大スループット: IOPS/MBps<sup>*</sup> | 最大 NIC 数|必要なネットワーク帯域幅 (Mbps) |
|---|---|---|---|---|---|---|---|
| Standard_D2d_v4<sup>1</sup> | 2  | 8   | 75   | 4  | 9000/125    | 2 | 1000  |
| Standard_D4d_v4             | 4  | 16  | 150  | 8  | 19000/250   | 2 | 2000  |
| Standard_D8d_v4             | 8  | 32  | 300  | 16 | 38000/500   | 4 | 4000  |
| Standard_D16d_v4            | 16 | 64  | 600  | 32 | 75000/1000   | 8 | 8000  |
| Standard_D32d_v4            | 32 | 128 | 1200 | 32 | 150000/2000 | 8 | 16000 |
| Standard_D48d_v4            | 48 | 192 | 1800 | 32 | 225000/3000 | 8 | 24000 |
| Standard_D64d_v4            | 64 | 256 | 2400 | 32 | 300000/4000 | 8 | 30000 |




<sup>*</sup> これらの IOPS 値を実現するには、[Gen2 VM](generation-2.md) を使用します<br>
<sup>1</sup> 高速ネットワークは、1 つの NIC にのみ適用できます。 

## <a name="ddsv4-series"></a>Ddsv4 シリーズ

Ddv4 シリーズは、Intel&reg; Xeon&reg; Platinum 8272CL (Cascade Lake) 上で実行されます。 Ddsv4 シリーズは、ほとんどの運用環境のワークロードに適した vCPU、メモリ、一時ディスクの組み合わせを提供します。

新しい Ddsv4 VM のサイズには、高速で大規模なローカル SSD ストレージ (最大 2400 GiB) が含まれており、低待機時間で高速のローカル ストレージを利用するアプリケーション向けに設計されています。そのようなアプリケーションとしては、一時ストレージへの高速な読み取り/書き込みを必要とするものや、キャッシュまたは一時ファイル用の一時ストレージを必要とするものなどがあります。 

 > [!NOTE]
 >Ddsv4 サイズの価格および課金の計算方法は、Ddv4 シリーズと同じです。

[ACU](acu.md): 195-210<br>
[Premium Storage](premium-storage-performance.md): サポートされています<br>
[Premium Storage キャッシュ](premium-storage-performance.md): サポートされています<br>
[ライブ マイグレーション](maintenance-and-updates.md): サポートされています<br>
[メモリ保持更新](maintenance-and-updates.md): サポートされています<br>
[VM 世代サポート](generation-2.md): 第 1 世代と第 2 世代<br>
[高速ネットワーク](../virtual-network/create-vm-accelerated-networking-cli.md):サポートされています<br>
[エフェメラル OS ディスク](ephemeral-os-disks.md):サポートされています <br>
[入れ子になった仮想化](/virtualization/hyper-v-on-windows/user-guide/nested-virtualization): サポートされています <br>
<br> 

| サイズ | vCPU | メモリ:GiB | 一時ストレージ (SSD) GiB | 最大データ ディスク数 | 一時ストレージの最大スループット: IOPS/MBps<sup>*</sup> | キャッシュが無効な場合の最大ディスク スループット: IOPS/MBps |  バースト キャッシュが無効なディスクの最大スループット: IOPS/MBps<sup>1</sup> | 最大 NIC 数|必要なネットワーク帯域幅 (Mbps) |
|---|---|---|---|---|---|---|---|---|---|
| Standard_D2ds_v4<sup>2</sup> | 2  | 8   | 75   | 4  | 9000/125    | 3200/48    | 4000/200   | 2 | 1000  |
| Standard_D4ds_v4             | 4  | 16  | 150  | 8  | 19000/250   | 6400/96    | 8000/200   | 2 | 2000  |
| Standard_D8ds_v4             | 8  | 32  | 300  | 16 | 38000/500   | 12800/192  | 16000/400  | 4 | 4000  |
| Standard_D16ds_v4            | 16 | 64  | 600  | 32 | 85000/1000   | 25600/384  | 32000/800  | 8 | 8000  |
| Standard_D32ds_v4            | 32 | 128 | 1200 | 32 | 150000/2000 | 51200/768  | 64000/1600 | 8 | 16000 |
| Standard_D48ds_v4            | 48 | 192 | 1800 | 32 | 225000/3000 | 76800/1152 | 80000/2000 | 8 | 24000 |
| Standard_D64ds_v4            | 64 | 256 | 2400 | 32 | 300000/4000 | 80000/1200 | 80000/2000 | 8 | 30000 |

<sup>*</sup> これらの IOPS 値を実現するには、[Gen2 VM](generation-2.md) を使用します<br>
<sup>1</sup>  Ddsv4 シリーズの VM では、ディスクのパフォーマンスを[バースト](./disk-bursting.md)でき、一度に最大 30 分間バーストを最大にしておくことができます。<br>
<sup>2</sup> 高速ネットワークは、1 つの NIC にのみ適用できます。 

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
