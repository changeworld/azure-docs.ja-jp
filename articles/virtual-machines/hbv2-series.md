---
title: HBv2 シリーズ - Azure Virtual Machines
description: HBv2 シリーズ VM の仕様。
services: virtual-machines
author: vermagit
ms.service: virtual-machines
ms.topic: article
ms.date: 02/03/2020
ms.author: amverma
ms.openlocfilehash: ad5fb1098f88a253252dad43c9da51b5d9f11237
ms.sourcegitcommit: 98a5a6765da081e7f294d3cb19c1357d10ca333f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/20/2020
ms.locfileid: "77492328"
---
# <a name="hbv2-series"></a>HBv2 シリーズ

HBv2 シリーズ VM は、流体力学、有限要素解析、貯留層シミュレーションなどの、メモリ帯域幅に基づいたアプリケーション向けに最適化されています。 HBv2 VM は 120 個の AMD EPYC 7742 プロセッサ コア、CPU コアあたり 4 GB の RAM を備え、同時マルチスレッドはありません。 各 HBv2 VM では、最大 340 GB/秒のメモリ帯域幅が提供され、最大 4 テラフロップの FP64 コンピューティングが提供されます。

Premium Storage: サポートされています

| Size | vCPU | プロセッサ | メモリ (GB) | メモリ帯域幅 GB/秒 | ベース CPU 周波数 (GHz) | 全コア周波数 (GHz、ピーク) | シングルコア周波数 (GHz、ピーク) | RDMA パフォーマンス (GB/秒) | MPI のサポート | 一時ストレージ (GB) | 最大データ ディスク数 | 最大イーサネット NIC |
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_HB120rs_v2 | 120 | AMD EPYC 7V12 | 480 | 350 | 2.45 | 3.1 | 3.3 | 200 | All | 480 + 960 | 8 | 1 |


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