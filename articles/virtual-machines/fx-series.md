---
title: FX シリーズ
description: FX シリーズ VM の仕様。
author: brbell
ms.service: virtual-machines
ms.subservice: vm-sizes-compute
ms.topic: conceptual
ms.date: 06/10/2021
ms.author: jushiman
ms.openlocfilehash: f287fddc2f7639f2ff61588c9d0fe8068cb675dc
ms.sourcegitcommit: e1037fa0082931f3f0039b9a2761861b632e986d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/12/2021
ms.locfileid: "132402164"
---
# <a name="fx-series"></a>FX シリーズ

FX シリーズは、Intel® Xeon® Gold 6246R (Cascade Lake) プロセッサで実行されます。 4\.0 GHz の全コア ターボ周波数、仮想 CPU あたり 21 GB の RAM、最大 1 TB の合計 RAM、ローカル一時ストレージを備えています。 FX シリーズは、高い CPU クロック速度、高いメモリ対 CPU 比を必要とするワークロード、コア ベースのライセンス コストが高いワークロード、優れたシングル コア パフォーマンスを必要とするアプリケーションに役立ちます。 FX シリーズの一般的なユース ケースは、Electronic Design Automation (EDA) ワークロードです。

FX シリーズ VM には、[Intel® Turbo Boost Technology 2.0](https://www.intel.com/content/www/us/en/architecture-and-technology/turbo-boost/turbo-boost-technology.html)、[Intel® Hyper-Threading Technology](https://www.intel.com/content/www/us/en/architecture-and-technology/hyper-threading/hyper-threading-technology.html)、[Intel® Advanced Vector Extensions 512 (Intel® AVX-512)](https://www.intel.com/content/www/us/en/architecture-and-technology/avx-512-overview.html) が搭載されています。

[ACU](acu.md): 310 - 340<br>
[Premium Storage](premium-storage-performance.md): サポートされています<br>
[Premium Storage キャッシュ](premium-storage-performance.md): サポートされています<br>
[ライブ マイグレーション](maintenance-and-updates.md): サポートされています<br>
[メモリ保持更新](maintenance-and-updates.md): サポートされています<br>
[VM 世代サポート](generation-2.md): 第 2 世代<br>
[高速ネットワーク](../virtual-network/create-vm-accelerated-networking-cli.md):サポートされています<br>
[エフェメラル OS ディスク](ephemeral-os-disks.md):サポートされています <br>
[入れ子になった仮想化](/virtualization/hyper-v-on-windows/user-guide/nested-virtualization): サポートされています <br>
<br>

| サイズ | vCPU の数 | メモリ:GiB | 一時ストレージ (SSD) GiB | 最大データ ディスク数 | キャッシュが有効な場合および一時ストレージの最大スループットIOPS/MBps | キャッシュが無効な場合の最大ディスク スループット: IOPS/MBps | 最大 NIC 数|必要なネットワーク帯域幅 (Mbps) |
|---|---|---|---|---|---|---|---|---|
| Standard_FX4mds  | 4   | 84   | 168  | 8   | 40000/343     | 6700/104   | 2 | 4000  |
| Standard_FX12mds | 12  | 252  | 504  | 24  | 100000/1029   | 20000/314  | 4 | 8000  |
| Standard_FX24mds | 24  | 504  | 1008 | 32  | 200000/2057   | 40000/629  | 4 | 16000 |
| Standard_FX36mds | 36  | 756  | 1512 | 32  | 300000/3086   | 60000/944  | 8 | 24000 |
| Standard_FX48mds | 48  | 1008 | 2016 | 32  | 400000/3871   | 80000/1258 | 8 | 32000 |


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


## <a name="next-steps"></a>次のステップ

[Azure コンピューティング ユニット (ACU)](acu.md) を確認することで、Azure SKU 全体の処理性能を比較できます。
