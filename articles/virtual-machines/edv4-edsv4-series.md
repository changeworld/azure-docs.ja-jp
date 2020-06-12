---
title: Edv4 および Edsv4 シリーズ - Azure Virtual Machines
description: Ev4、Edv4、Esv4 および Edsv4 シリーズ VM の仕様。
author: brbell
ms.author: brbell
ms.reviewer: cynthn
ms.custom: mimckitt
ms.service: virtual-machines
ms.topic: conceptual
ms.date: 02/04/2020
ms.openlocfilehash: 795891f56985504a3584089d7377f753605ba4dd
ms.sourcegitcommit: 309cf6876d906425a0d6f72deceb9ecd231d387c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/01/2020
ms.locfileid: "84263042"
---
# <a name="edv4-and-edsv4-series"></a>Edv4 および Edsv4 シリーズ

Edv4 と Edsv4 シリーズは、ハイパースレッド構成の Intel&reg; Xeon&reg; Platinum 8272CL (Cascade Lake) プロセッサ上で実行されます。メモリを集中的に使用するさまざまなエンタープライズ アプリケーションに最適で、最大 504 GiB の RAM、[Intel&reg; Turbo Boost Technology 2.0](https://www.intel.com/content/www/us/en/architecture-and-technology/turbo-boost/turbo-boost-technology.html)、[Intel&reg; Hyper-Threading Technology](https://www.intel.com/content/www/us/en/architecture-and-technology/hyper-threading/hyper-threading-technology.html) および [Intel&reg; Advanced Vector Extensions 512 (Intel&reg; AVX-512)](https://www.intel.com/content/www/us/en/architecture-and-technology/avx-512-overview.html) の機能を備えています。 これらの新しい VM サイズでは、50% 大きいローカル ストレージが使用されるほか、[Gen2 VM](https://docs.microsoft.com/azure/virtual-machines/linux/generation-2) の [Ev3/Esv3](https://docs.microsoft.com/azure/virtual-machines/ev3-esv3-series) サイズと比べて読み取りと書き込みの両方のローカル ディスク IOPS が向上します


> [!IMPORTANT]
> Edv4 または Edsv4 シリーズを使用して新しい仮想マシンをデプロイし、Linux イメージを使用する場合は、RHEL 8.x、CentOS 8.x、または Oracle 7.x 以降を使用する必要があります。 RHEL 7.x、CentOS 7.x、または Orcale 6.x を選択すると、カーネル パニック エラーが発生します。 Microsoft は積極的に修正プログラムをデプロイしています。 RHEL、CentOS、および Oracle のみが影響を受けます。

## <a name="edv4-series"></a>Edv4 シリーズ

Edv4 シリーズのサイズは、Intel&reg; Xeon&reg; Platinum 8272CL (Cascade Lake) プロセッサ上で実行されます。 Edv4 仮想マシンのサイズは、高速かつ大規模なローカル SSD ストレージ (最大 2400 GiB) に加えて、最大 504 GiB の RAM を搭載しています。 これらの仮想マシンは、メモリを集中的に使用するエンタープライズ アプリケーションや、低待機時間で高速なローカル ストレージを利用するアプリケーションに最適です。 Edv4 VM には Standard SSD および Standard HDD ディスク ストレージを接続できます。 

ACU: 195 - 210

Premium Storage: サポートされていません

Premium Storage キャッシュ:サポートされていません

ライブ マイグレーション: サポートされています

メモリ保持更新: サポートされています

| サイズ | vCPU | メモリ:GiB | 一時ストレージ (SSD) GiB | 最大データ ディスク数 | キャッシュが有効な場合および一時ストレージの最大スループットIOPS/MBps | 最大 NIC 数/想定ネットワーク帯域幅 (Mbps) |
|---|---|---|---|---|---|---|
| Standard_E2d_v4  | 2 | 16 | 75 | 4 | 19000/120 | 2/1000 |
| Standard_E4d_v4  | 4 | 32 | 150 | 8 | 38500/242 | 2/2000 |
| Standard_E8d_v4 | 8 | 64 | 該当なし | 16 | 77000/485 | 4/4000 |
| Standard_E16d_v4 | 16 | 128 | 600 | 32 | 154000/968 | 8/8000 |
| Standard_E20d_v4 | 20 | 160 | 750 | 32 | 193000/1211  | 8/10000 |
| Standard_E32d_v4 | 32 | 256 | 1200 | 32 | 308000/1936 | 8/16000 |
| Standard_E48d_v4 | 48 | 384 | 1800 | 32 | 462000/2904 | 8/24000 |
| Standard_E64d_v4 | 64 | 504 | 2400 | 32 | 615000/3872 | 8/30000 |


## <a name="edsv4-series"></a>Edsv4 シリーズ

Edsv4 シリーズのサイズは、Intel&reg; Xeon&reg; Platinum 8272CL (Cascade Lake) プロセッサ上で実行されます。 Edsv4 仮想マシンのサイズは、高速かつ大規模なローカル SSD ストレージ (最大 2400 GiB) に加えて、最大 504 GiB の RAM を搭載しています。 これらの仮想マシンは、メモリを集中的に使用するエンタープライズ アプリケーションや、低待機時間で高速なローカル ストレージを利用するアプリケーションに最適です。

ACU: 195-210

Premium Storage: サポートされています

Premium Storage キャッシュ:サポートされています

ライブ マイグレーション:サポートされています

メモリ保持更新: サポートされています

| サイズ | vCPU | メモリ:GiB | 一時ストレージ (SSD) GiB | 最大データ ディスク数 | キャッシュが有効な場合および一時ストレージの最大スループットIOPS/MBps | キャッシュが無効な場合の最大ディスク スループット: IOPS/MBps | 最大 NIC 数/想定ネットワーク帯域幅 (Mbps) |
|---|---|---|---|---|---|---|---|
| Standard_E2ds_v4  | 2 | 16 | 75 | 4 | 19000/120(50) | 3200/48 | 2/1000 |
| Standard_E4ds_v4  | 4 | 32 | 150 | 8 | 38500/242(100) | 6400/96 | 2/2000 |
| Standard_E8ds_v4 | 8 | 64 | 該当なし | 16 | 77000/485(200) | 12800/192 | 4/4000 |
| Standard_E16ds_v4 | 16 | 128 | 600 | 32 | 154000/968(400) | 25600/384 | 8/8000 |
| Standard_E20ds_v4 | 20 | 160 | 750 | 32 | 193000/1211(500)  | 32000/480  | 8/10000 |
| Standard_E32ds_v4 | 32 | 256 | 1200 | 32 | 308000/1936(800) | 51200/768  | 8/16000 |
| Standard_E48ds_v4 | 48 | 384 | 1800 | 32 | 462000/2904(1200) | 76800/1152 | 8/24000 |
| Standard_E64ds_v4 <sup>1</sup> | 64 | 504 | 2400 | 32 | 615000/3872(1600) | 80000/1200 | 8/30000 |

<sup>1</sup> [コア数を制限したサイズも提供しています](https://docs.microsoft.com/azure/virtual-machines/windows/constrained-vcpu)。


[!INCLUDE [virtual-machines-common-sizes-table-defs](../../includes/virtual-machines-common-sizes-table-defs.md)]

## <a name="other-sizes"></a>その他のサイズ

- [汎用](sizes-general.md)
- [メモリの最適化](sizes-memory.md)
- [ストレージの最適化](sizes-storage.md)
- [GPU の最適化](sizes-gpu.md)
- [ハイ パフォーマンス コンピューティング](sizes-hpc.md)
- [旧世代](sizes-previous-gen.md)

## <a name="next-steps"></a>次のステップ

[Azure コンピューティング ユニット (ACU)](acu.md) を確認することで、Azure SKU 全体の処理性能を比較できます。
