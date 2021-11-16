---
title: Edv5 および Edsv5 シリーズ - Azure Virtual Machines
description: Edv5 および Edsv5 シリーズ VM の仕様。
author: styli365
ms.author: joelpell
ms.reviewer: joelpell
ms.custom: mimckitt
ms.service: virtual-machines
ms.subservice: vm-sizes-memory
ms.topic: conceptual
ms.date: 10/20/2021
ms.openlocfilehash: 496dc7271b3468a106bd6c1dbe25e00bc147c26a
ms.sourcegitcommit: e1037fa0082931f3f0039b9a2761861b632e986d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/12/2021
ms.locfileid: "132401973"
---
# <a name="edv5-and-edsv5-series"></a>Edv5 および Edsv5 シリーズ

**適用対象:** :heavy_check_mark: Linux VM :heavy_check_mark: Windows VM :heavy_check_mark: フレキシブル スケール セット :heavy_check_mark: ユニフォーム スケール セット

Edv5 および Edsv5 シリーズの仮想マシンは、[ハイパースレッド](https://www.intel.com/content/www/us/en/architecture-and-technology/hyper-threading/hyper-threading-technology.html)構成の第 3 世代 Intel&reg; Xeon&reg; Platinum 8370C (Ice Lake) プロセッサ上で実行されます。このシリーズでは、ほとんどの汎用ワークロードに対してより優れた価値提案が提供されます。 この新しいプロセッサは、3.5 GHz の全コア ターボ クロック速度を特徴とし、[Intel&reg; Turbo Boost Technology](https://www.intel.com/content/www/us/en/architecture-and-technology/turbo-boost/turbo-boost-technology.html)、[Intel&reg; Advanced-Vector Extensions 512 (Intel&reg; AVX-512)](https://www.intel.com/content/www/us/en/architecture-and-technology/avx-512-overview.html)、[Intel&reg; Deep Learning Boost](https://software.intel.com/content/www/us/en/develop/topics/ai/deep-learning-boost.html) の機能を備えています。 これらの仮想マシンは、最大 672 GiB の RAM を搭載しており、メモリ集中型のエンタープライズ アプリケーション、リレーショナル データベース サーバー、メモリ内分析ワークロードに最適です。 また、これらの VM は、高速で大規模なローカル SSD ストレージ (最大 3,900 GiB) も特徴としています。

## <a name="edv5-series"></a>Edv5 シリーズ

Edv5 シリーズの仮想マシンは、最大 3.5 GHz の全コア ターボ クロック速度に達する、第 3世代 Intel® Xeon® Platinum 8370C (Ice Lake) プロセッサ上で実行されます。  これらの仮想マシンは、最大 104 個の vCPU と最大 672 GiB の RAM を提供し、さらに最大 3800 GiB の高速なローカル SSD ストレージを提供します。 Edv5 シリーズの仮想マシンは、メモリを集中的に使用するエンタープライズ アプリケーションや、待機時間が短く高速なローカル ストレージを利用するアプリケーションに最適です。

Edv5 シリーズの仮想マシンでは、ディスクの種類として Standard SSD と Standard HDD がサポートされています。 Premium SSD または Ultra Disk のストレージを使用するには、Edsv5 シリーズの仮想マシンを選んでください。 ディスク ストレージは、仮想マシンとは別に課金されます。 [ディスクの価格に関するページを参照してください](https://azure.microsoft.com/pricing/details/managed-disks/)。

[Premium Storage](premium-storage-performance.md): サポートされていません<br>
[Premium Storage キャッシュ](premium-storage-performance.md): サポートされていません<br>
[ライブ マイグレーション](maintenance-and-updates.md): サポートされています<br>
[メモリ保持更新](maintenance-and-updates.md): サポートされています<br>
[VM 世代サポート](generation-2.md): 第 1 世代と第 2 世代<br>
[高速ネットワーク](../virtual-network/create-vm-accelerated-networking-cli.md): 必須 <br>
[エフェメラル OS ディスク](ephemeral-os-disks.md):サポートされていません <br>
[入れ子になった仮想化](/virtualization/hyper-v-on-windows/user-guide/nested-virtualization): サポートされています <br>
<br>

| サイズ | vCPU | メモリ:GiB | 一時ストレージ (SSD) GiB | 最大データ ディスク数 | キャッシュが有効な場合および一時ストレージの最大スループット: IOPS/MBps<sup>*</sup>  | 最大 NIC 数|最大ネットワーク帯域幅 (Mbps) |
|---|---|---|---|---|---|---|---|
| Standard_E2d_v5<sup>1,2</sup>  | 2   | 16  | 75   | 4  | 9000/125    | 2 | 12500 |
| Standard_E4d_v5                | 4   | 32  | 150  | 8  | 19000/250   | 2 | 12500 |
| Standard_E8d_v5                | 8   | 64  | 300  | 16 | 38000/500   | 4 | 12500 |
| Standard_E16d_v5               | 16  | 128 | 600  | 32 | 75000/1000  | 8 | 12500 |
| Standard_E20d_v5               | 20  | 160 | 750  | 32 | 94000/1250  | 8 | 12500  |
| Standard_E32d_v5               | 32  | 256 | 1200 | 32 | 150000/2000 | 8 | 16000  |
| Standard_E48d_v5               | 48  | 384 | 1800 | 32 | 225000/3000 | 8 | 24000  |
| Standard_E64d_v5               | 64  | 512 | 2400 | 32 | 300000/4000 | 8 | 30000  |
| Standard_E96d_v5               | 96  | 672 | 3600 | 32 | 450000/4000 | 8 | 35000  |
| Standard_E104id_v5<sup>3</sup> | 104 | 672 | 3800 | 64 | 450000/4000 | 8 | 100000 |

<sup>*</sup> これらの IOPS 値を保証するには、[Gen2 VM](generation-2.md) を使用します。<br>
<sup>1</sup> 高速ネットワークは必須であり、すべての Edv5 仮想マシンで既定でオンになっています。<br>
<sup>2</sup> 高速ネットワークは 2 つの NIC に適用できます。<br>
<sup>3</sup> インスタンスは、単一の顧客専用のハードウェアに[分離](../security/fundamentals/isolation-choices.md#compute-isolation)されます。


## <a name="edsv5-series"></a>Edsv5 シリーズ

Edsv5 シリーズの仮想マシンは、最大 3.5 GHz の全コア ターボ クロック速度に達する、第 3世代 Intel® Xeon® Platinum 8370C (Ice Lake) プロセッサ上で実行されます。  これらの仮想マシンは、最大 104 個の vCPU と最大 672 GiB の RAM を提供し、さらに最大 3800 GiB の高速なローカル SSD ストレージを提供します。 Edsv5 シリーズの仮想マシンは、メモリを集中的に使用するエンタープライズ アプリケーションや、待機時間が短く高速なローカル ストレージを利用するアプリケーションに最適です。

Edsv5 シリーズの仮想マシンでは、ディスクの種類として Standard SSD と Standard HDD がサポートされています。 これらの VM には、Standard SSD、Standard HDD、Premium SSD のディスク ストレージを接続できます。 また、リージョンで Ultra Disk ストレージを使用できる場合は、このストレージも接続できます。 ディスク ストレージは、仮想マシンとは別に課金されます。 [ディスクの価格に関するページを参照してください](https://azure.microsoft.com/pricing/details/managed-disks/)。

[Premium Storage](premium-storage-performance.md): サポートされています<br>
[Premium Storage キャッシュ](premium-storage-performance.md): サポートされています<br>
[ライブ マイグレーション](maintenance-and-updates.md): サポートされています<br>
[メモリ保持更新](maintenance-and-updates.md): サポートされています<br>
[VM 世代サポート](generation-2.md): 第 1 世代と第 2 世代<br>
[高速ネットワーク](../virtual-network/create-vm-accelerated-networking-cli.md): 必須 <br>
[エフェメラル OS ディスク](ephemeral-os-disks.md):サポートされています <br>
[入れ子になった仮想化](/virtualization/hyper-v-on-windows/user-guide/nested-virtualization): サポートされています <br>
<br>

| サイズ | vCPU | メモリ:GiB | 一時ストレージ (SSD) GiB | 最大データ ディスク数 | キャッシュが有効な場合および一時ストレージの最大スループット: IOPS/MBps<sup>*</sup> | キャッシュが無効な場合の最大ディスク スループット: IOPS/MBps | キャッシュ不使用時の最大バースト ディスク スループット (IOPS/MBps)<sup>5</sup> | 最大 NIC 数 | 最大ネットワーク帯域幅 (Mbps) |
|---|---|---|---|---|---|---|---|---|---|
| Standard_E2ds_v5<sup>1,2</sup>  | 2   | 16  | 75   | 4  | 9000/125    | 3750/85      | 10000/1200 | 2 | 12500 |
| Standard_E4ds_v5                | 4   | 32  | 150  | 8  | 19000/250   | 6400/145     | 20000/1200 | 2 | 12500 |
| Standard_E8ds_v5                | 8   | 64  | 300  | 16 | 38000/500   | 12800/290    | 20000/1200 | 4 | 12500 |
| Standard_E16ds_v5               | 16  | 128 | 600  | 32 | 75000/1000  | 25600/600    | 40000/1200 | 8 | 12500 |
| Standard_E20ds_v5               | 20  | 160 | 750  | 32 | 94000/1250  | 32000/750    | 64000/1600 | 8 | 12500  |
| Standard_E32ds_v5               | 32  | 256 | 1200 | 32 | 150000/2000 | 51200/865    | 80000/2000 | 8 | 16000  |
| Standard_E48ds_v5               | 48  | 384 | 1800 | 32 | 225000/3000 | 76800/1315   | 80000/3000 | 8 | 24000  |
| Standard_E64ds_v5               | 64  | 512 | 2400 | 32 | 375000/4000 | 80000/1735   | 80000/3000 | 8 | 30000  |
| Standard_E96ds_v5<sup>3</sup>   | 96  | 672 | 3600 | 32 | 450000/4000 | 80000/2600   | 80000/4000 | 8 | 35000  |
| Standard_E104ids_v5<sup>4</sup> | 104 | 672 | 3800 | 64 | 450000/4000 | 120000/4000  | 120000/4000 | 8 | 100000 |

<sup>*</sup> これらの IOPS 値を保証するには、[Gen2 VM](generation-2.md) を使用します。<br>
<sup>1</sup> 高速ネットワークは必須であり、すべての Edsv5 仮想マシンで既定でオンになっています。<br>
<sup>2</sup> 高速ネットワークは 2 つの NIC に適用できます。<br>
<sup>3</sup> [コア数を制限](constrained-vcpu.md)したサイズも提供されています。<br>
<sup>4</sup> インスタンスは、単一の顧客専用のハードウェアに[分離](../security/fundamentals/isolation-choices.md#compute-isolation)されます。<br>
<sup>5</sup> Edsv5 シリーズの仮想マシンでは、ディスクのパフォーマンスを[バースト](disk-bursting.md)でき、一度に最大 30 分間バーストを最大にしておくことができます。

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
