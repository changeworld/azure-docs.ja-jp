---
title: Ddv5 および Ddsv5 シリーズ - Azure Virtual Machines
description: Ddv5 および Ddsv5 シリーズ VM の仕様。
author: styli365
ms.author: joelpell
ms.reviewer: joelpell
ms.custom: mimckitt
ms.service: virtual-machines
ms.subservice: vm-sizes-general
ms.topic: conceptual
ms.date: 10/20/2021
ms.openlocfilehash: 003b53a733e3b64c5ff5c822743f48df361a214b
ms.sourcegitcommit: 901ea2c2e12c5ed009f642ae8021e27d64d6741e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/12/2021
ms.locfileid: "132370027"
---
# <a name="ddv5-and-ddsv5-series"></a>Ddv5 および Ddsv5 シリーズ

**適用対象:** :heavy_check_mark: Linux VM :heavy_check_mark: Windows VM :heavy_check_mark: フレキシブル スケール セット :heavy_check_mark: ユニフォーム スケール セット

Ddv5 および Ddsv5 シリーズの仮想マシンは、[ハイパースレッド](https://www.intel.com/content/www/us/en/architecture-and-technology/hyper-threading/hyper-threading-technology.html)構成の第 3 世代 Intel&reg; Xeon&reg; Platinum 8370C (Ice Lake) プロセッサ上で実行されます。このシリーズでは、ほとんどの汎用ワークロードに対してより優れた価値提案が提供されます。 この新しいプロセッサは、3.5 GHz の全コア ターボ クロック速度を特徴とし、[Intel&reg; Turbo Boost Technology](https://www.intel.com/content/www/us/en/architecture-and-technology/turbo-boost/turbo-boost-technology.html)、[Intel&reg; Advanced-Vector Extensions 512 (Intel&reg; AVX-512)](https://www.intel.com/content/www/us/en/architecture-and-technology/avx-512-overview.html)、[Intel&reg; Deep Learning Boost](https://software.intel.com/content/www/us/en/develop/topics/ai/deep-learning-boost.html) の機能を備えています。 これらの仮想マシンは、小規模から中規模のデータベース、低から中程度のトラフィックの Web サーバー、アプリケーション サーバーなど、ほとんどのエンタープライズ ワークロードに関連する要件を満たすことができるように、vCPU、メモリ、一時ストレージの組み合わせを提供します。


## <a name="ddv5-series"></a>Ddv5 シリーズ
Ddv5 シリーズの仮想マシンは、最大 3.5 GHz の全コア ターボ クロック速度に達する、第 3世代 Intel® Xeon® Platinum 8370C (Ice Lake) プロセッサ上で実行されます。 これらの仮想マシンは、最大 96 個の vCPU と最大 384 GiB の RAM を提供し、さらに最大 3,600 GiB の高速なローカル SSD ストレージを提供します。 Ddv5 シリーズの仮想マシンは、以前の世代と比較して、ほとんどの汎用ワークロードに対してより優れた価値提案を提供します (たとえば、スケーラビリティの向上やアップグレードされた CPU クラスなど)。 また、これらの仮想マシンは、高速で大規模なローカル SSD ストレージ (最大 3,600 GiB) も特徴としています。

Ddv5 シリーズの仮想マシンでは、ディスクの種類として Standard SSD と Standard HDD がサポートされています。 Premium SSD または Ultra Disk のストレージを使用するには、Ddsv5 シリーズの仮想マシンを選んでください。 ディスク ストレージは、仮想マシンとは別に課金されます。 [ディスクの価格に関するページを参照してください](https://azure.microsoft.com/pricing/details/managed-disks/)。


[Premium Storage](premium-storage-performance.md): サポートされていません<br>
[Premium Storage キャッシュ](premium-storage-performance.md): サポートされていません<br>
[ライブ マイグレーション](maintenance-and-updates.md): サポートされています<br>
[メモリ保持更新](maintenance-and-updates.md): サポートされています<br>
[VM 世代サポート](generation-2.md): 第 1 世代と第 2 世代<br>
[高速ネットワーク](../virtual-network/create-vm-accelerated-networking-cli.md): 必須 <br>
[エフェメラル OS ディスク](ephemeral-os-disks.md):サポートされています <br>
[仮想化を入れ子にする](/virtualization/hyper-v-on-windows/user-guide/nested-virtualization.md): サポートされています <br>
<br> 

| サイズ | vCPU | メモリ:GiB | 一時ストレージ (SSD) GiB | 最大データ ディスク数 | キャッシュが有効な場合および一時ストレージの最大スループット: IOPS/MBps<sup>*</sup> | 最大 NIC 数|最大ネットワーク帯域幅 (Mbps) |
|---|---|---|---|---|---|---|---|
| Standard_D2d_v5<sup>1,2</sup> | 2  | 8   | 75   | 4  | 9000/125    | 2 | 12500 |
| Standard_D4d_v5               | 4  | 16  | 150  | 8  | 19000/250   | 2 | 12500 |
| Standard_D8d_v5               | 8  | 32  | 300  | 16 | 38000/500   | 4 | 12500 |
| Standard_D16d_v5              | 16 | 64  | 600  | 32 | 75000/1000  | 8 | 12500 |
| Standard_D32d_v5              | 32 | 128 | 1200 | 32 | 150000/2000 | 8 | 16000 |
| Standard_D48d_v5              | 48 | 192 | 1800 | 32 | 225000/3000 | 8 | 24000 |
| Standard_D64d_v5              | 64 | 256 | 2400 | 32 | 300000/4000 | 8 | 30000 |
| Standard_D96d_v5              | 96 | 384 | 3600 | 32 | 450000/4000 | 8 | 35000 |

<sup>*</sup> これらの IOPS 値を保証するには、[Gen2 VM](generation-2.md) を使用します。<br>
<sup>1</sup> 高速ネットワークは必須であり、すべての Ddv5 仮想マシンで既定でオンになっています。<br>
<sup>2</sup> 高速ネットワークは 2 つの NIC に適用できます。

## <a name="ddsv5-series"></a>Ddsv5 シリーズ

Ddsv5 シリーズの仮想マシンは、最大 3.5 GHz の全コア ターボ クロック速度に達する、第 3世代 Intel® Xeon® Platinum 8370C (Ice Lake) プロセッサ上で実行されます。  これらの仮想マシンは、最大 96 個の vCPU と最大 384 GiB の RAM を提供し、さらに最大 3,600 GiB の高速なローカル SSD ストレージを提供します。 Ddsv5 シリーズの仮想マシンは、以前の世代と比較して、ほとんどの汎用ワークロードに対してより優れた価値提案を提供します (たとえば、スケーラビリティの向上やアップグレードされた CPU クラスなど)。

Ddsv5 シリーズの仮想マシンでは、ディスクの種類として Standard SSD、Standard HDD、Premium SSD がサポートされています。 また、リージョンで Ultra Disk ストレージを使用できる場合は、このストレージも接続できます。 ディスク ストレージは、仮想マシンとは別に課金されます。 [ディスクの価格に関するページを参照してください](https://azure.microsoft.com/pricing/details/managed-disks/)。

[Premium Storage](premium-storage-performance.md): サポートされています<br>
[Premium Storage キャッシュ](premium-storage-performance.md): サポートされています<br>
[ライブ マイグレーション](maintenance-and-updates.md): サポートされています<br>
[メモリ保持更新](maintenance-and-updates.md): サポートされています<br>
[VM 世代サポート](generation-2.md): 第 1 世代と第 2 世代<br>
[高速ネットワーク](../virtual-network/create-vm-accelerated-networking-cli.md): 必須 <br>
[エフェメラル OS ディスク](ephemeral-os-disks.md):サポートされています <br>
[仮想化を入れ子にする](/virtualization/hyper-v-on-windows/user-guide/nested-virtualization.md): サポートされています <br>
<br> 


| サイズ | vCPU | メモリ:GiB | 一時ストレージ (SSD) GiB | 最大データ ディスク数 | キャッシュが有効な場合および一時ストレージの最大スループット: IOPS/MBps<sup>*</sup> | キャッシュが無効な場合の最大ディスク スループット: IOPS/MBps | キャッシュ不使用時の最大バースト ディスク スループット (IOPS/MBps)<sup>3</sup> | 最大 NIC 数 | 最大ネットワーク帯域幅 (Mbps) |
|---|---|---|---|---|---|---|---|---|---|
| Standard_D2ds_v5<sup>1,2</sup> | 2  | 8   | 75   | 4  | 9000/125    | 3750/85     | 10000/1200 | 2 | 12500 |
| Standard_D4ds_v5               | 4  | 16  | 150  | 8  | 19000/250   | 6400/145    | 20000/1200 | 2 | 12500 |
| Standard_D8ds_v5               | 8  | 32  | 300  | 16 | 38000/500   | 12800/290   | 20000/1200 | 4 | 12500 |
| Standard_D16ds_v5              | 16 | 64  | 600  | 32 | 75000/1000  | 25600/600   | 40000/1200 | 8 | 12500 |
| Standard_D32ds_v5              | 32 | 128 | 1200 | 32 | 150000/2000 | 51200/865   | 80000/2000 | 8 | 16000 |
| Standard_D48ds_v5              | 48 | 192 | 1800 | 32 | 225000/3000 | 76800/1315  | 80000/3000 | 8 | 24000 |
| Standard_D64ds_v5              | 64 | 256 | 2400 | 32 | 375000/4000 | 80000/1735  | 80000/3000 | 8 | 30000 |
| Standard_D96ds_v5              | 96 | 256 | 3600 | 32 | 450000/4000 | 80000/2600  | 80000/4000 | 8 | 35000 |

<sup>*</sup> これらの IOPS 値を保証するには、[Gen2 VM](generation-2.md) を使用します。<br>
<sup>1</sup> 高速ネットワークは必須であり、すべての Ddsv5 仮想マシンで既定でオンになっています。<br>
<sup>2</sup> 高速ネットワークは 2 つの NIC に適用できます。<br>
<sup>3</sup> Ddsv5 シリーズの仮想マシンでは、ディスクのパフォーマンスを[バースト](disk-bursting.md)でき、一度に最大 30 分間バーストを最大にしておくことができます。

[!INCLUDE [virtual-machines-common-sizes-table-defs](../../includes/virtual-machines-common-sizes-table-defs.md)]

## <a name="other-sizes-and-information"></a>その他のサイズと情報

- [汎用](sizes-general.md)
- [メモリの最適化](sizes-memory.md)
- [ストレージの最適化](sizes-storage.md)
- [GPU の最適化](sizes-gpu.md)
- [ハイ パフォーマンス コンピューティング](sizes-hpc.md)
- [旧世代](sizes-previous-gen.md)

料金計算ツール: [料金計算ツール](https://azure.microsoft.com/pricing/calculator/)

ディスクの種類の詳細情報:[ディスクの種類](./disks-types.md#ultra-disks)
