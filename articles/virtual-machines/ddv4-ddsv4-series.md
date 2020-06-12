---
title: Ddv4 および Ddsv4 シリーズ - Azure Virtual Machines
description: Dv4、Ddv4、Dsv4、および Ddsv4 シリーズ VM の仕様。
author: brbell
ms.author: brbell
ms.reviewer: cynthn
ms.custom: mimckitt
ms.service: virtual-machines
ms.topic: conceptual
ms.date: 06/01/2020
ms.openlocfilehash: 42b42a7477326196546ad445367691192f00569a
ms.sourcegitcommit: 309cf6876d906425a0d6f72deceb9ecd231d387c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/01/2020
ms.locfileid: "84263247"
---
# <a name="ddv4-and-ddsv4-series"></a>Ddv4 および Ddsv4 シリーズ

Ddv4 および Ddsv4 シリーズは、ハイパースレッド構成の Intel&reg; Xeon&reg; Platinum 8272CL (Cascade Lake) プロセッサ上で実行され、ほとんどの汎用ワークロードに対してより優れた価値提案が提供されています。 持続する 3.4 GHz の全コア ターボ クロック速度を特徴とし、[Intel&reg; Turbo Boost Technology 2.0](https://www.intel.com/content/www/us/en/architecture-and-technology/turbo-boost/turbo-boost-technology.html)、[Intel&reg; Hyper-Threading Technology](https://www.intel.com/content/www/us/en/architecture-and-technology/hyper-threading/hyper-threading-technology.html)、および [Intel&reg; Advanced Vector Extensions 512 (Intel&reg; AVX-512)](https://www.intel.com/content/www/us/en/architecture-and-technology/avx-512-overview.html) の機能を備えています。 これらの新しい VM サイズでは、50% 大きいローカル ストレージが使用されるほか、[Gen2 VM](https://docs.microsoft.com/azure/virtual-machines/linux/generation-2) の [Dv3/Dsv3](https://docs.microsoft.com/azure/virtual-machines/dv3-dsv3-series) サイズと比べて読み取りと書き込みの両方のローカル ディスク IOPS が向上します。

D シリーズのユース ケースには、エンタープライズ級のアプリケーション、リレーショナル データベース、メモリ内キャッシュ、分析があります。

> [!IMPORTANT]
> Ddv4 または Ddsv4 シリーズを使用して新しい仮想マシンをデプロイし、Linux イメージを使用する場合は、RHEL 8.x、CentOS 8.x、または Oracle 7.x 以降を使用する必要があります。 RHEL 7.x、CentOS 7.x、または Orcale 6.x を選択すると、カーネル パニック エラーが発生します。 Microsoft は積極的に修正プログラムをデプロイしています。 RHEL、CentOS、および Oracle のみが影響を受けます。 

## <a name="ddv4-series"></a>Ddv4 シリーズ

Ddv4 シリーズのサイズは、Intel&reg; Xeon&reg; Platinum 8272CL (Cascade Lake) 上で実行されます。 Ddv4 シリーズは、ほとんどの運用環境のワークロードに適した vCPU、メモリ、一時ディスクの組み合わせを提供します。

新しい Ddv4 VM のサイズには、高速で大規模なローカル SSD ストレージ (最大 2400 GiB) が含まれており、低待機時間で高速のローカル ストレージを利用するアプリケーション向けに設計されています。そのようなアプリケーションとしては、一時ストレージへの高速な読み取り/書き込みを必要とするものや、キャッシュまたは一時ファイル用の一時ストレージを必要とするものなどがあります。 Ddv4 VM には Standard SSD および Standard HDD ストレージを接続できます。 リモート データ ディスク ストレージは、仮想マシンとは別に課金されます。

ACU: 195-210

Premium Storage: サポートされていません

Premium Storage キャッシュ:サポートされていません

ライブ マイグレーション: サポートされています

メモリ保持更新: サポートされています

| サイズ | vCPU | メモリ:GiB | 一時ストレージ (SSD) GiB | 最大データ ディスク数 | キャッシュが有効な場合および一時ストレージの最大スループットIOPS/MBps | 最大 NIC 数/想定ネットワーク帯域幅 (Mbps) |
|---|---|---|---|---|---|---|
| Standard_D2d_v4 | 2 | 8 | 75 | 4 | 19000/120 | 2/1000 |
| Standard_D4d_v4 | 4 | 16 | 150 | 8 | 38500/242 | 2/2000 |
| Standard_D8d_v4 | 8 | 32 | 該当なし | 16 | 77000/485 | 4/4000 |
| Standard_D16d_v4 | 16 | 64 | 600 | 32 | 154000/968 | 8/8000 |
| Standard_D32d_v4 | 32 | 128 | 1200 | 32 | 308000/1936 | 8/16000 |
| Standard_D48d_v4 | 48 | 192 | 1800 | 32 | 462000/2904 | 8/24000 |
| Standard_D64d_v4 | 64 | 256 | 2400 | 32 | 615000/3872 | 8/30000 |

## <a name="ddsv4-series"></a>Ddsv4 シリーズ

Ddv4 シリーズは、Intel&reg; Xeon&reg; Platinum 8272CL (Cascade Lake) 上で実行されます。 Ddsv4 シリーズは、ほとんどの運用環境のワークロードに適した vCPU、メモリ、一時ディスクの組み合わせを提供します。

新しい Ddsv4 VM のサイズには、高速で大規模なローカル SSD ストレージ (最大 2400 GiB) が含まれており、低待機時間で高速のローカル ストレージを利用するアプリケーション向けに設計されています。そのようなアプリケーションとしては、一時ストレージへの高速な読み取り/書き込みを必要とするものや、キャッシュまたは一時ファイル用の一時ストレージを必要とするものなどがあります。 

 > [!NOTE]
 >Ddsv4 サイズの価格および課金の計算方法は、Ddv4 シリーズと同じです。

ACU: 195-210

Premium Storage: サポートされています

Premium Storage キャッシュ:サポートされています

ライブ マイグレーション:サポートされています

メモリ保持更新: サポートされています

| サイズ | vCPU | メモリ:GiB | 一時ストレージ (SSD) GiB | 最大データ ディスク数 | キャッシュが有効な場合および一時ストレージの最大スループットIOPS/MBps | キャッシュが無効な場合の最大ディスク スループット: IOPS/MBps | 最大 NIC 数/想定ネットワーク帯域幅 (Mbps) |
|---|---|---|---|---|---|---|---|
| Standard_D2ds_v4 | 2 | 8 | 75 | 4 | 19000/120(50) | 3000/48 | 2/1000 |
| Standard_D4ds_v4 | 4 | 16 | 150 | 8 | 38500/242(100) | 6400/96 | 2/2000 |
| Standard_D8ds_v4 | 8 | 32 | 該当なし | 16 | 77000/485(200) | 12800/192 | 4/4000 |
| Standard_D16ds_v4 | 16 | 64 | 600 | 32 | 154000/968(400) | 25600/384 | 8/8000 |
| Standard_D32ds_v4 | 32 | 128 | 1200 | 32 | 308000/1936(800) | 51200/768 | 8/16000 |
| Standard_D48ds_v4 | 48 | 192 | 1800 | 32 | 462000/2904(1200) | 76800/1152 | 8/24000 |
| Standard_D64ds_v4 | 64 | 256 | 2400 | 32 | 615000/3872(1600) | 80000/1200 | 8/30000 |

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
