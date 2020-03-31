---
title: HB シリーズ - Azure Virtual Machines
description: HB シリーズ VM の仕様。
services: virtual-machines
author: jonbeck7
ms.service: virtual-machines
ms.topic: article
ms.date: 02/03/2020
ms.author: lahugh
ms.openlocfilehash: 600f10e81742e9bb66c800b747fd7b2dc062754d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/28/2020
ms.locfileid: "78164833"
---
# <a name="hb-series"></a>HB シリーズ

HB シリーズ VM は、流体力学、陽解法有限要素解析、気象モデリングなどの、メモリ帯域幅に基づいたアプリケーション向けに最適化されています。 HBv2 VM は 60 個の AMD EPYC 7551 プロセッサ コア、CPU コアあたり 4 GB の RAM を備え、同時マルチスレッドはありません。 HB VM では、最大 260 GB/秒のメモリ帯域幅が提供されます。

ACU: 199-216

Premium Storage: サポートされています

Premium Storage Caching: サポートされています

ライブ マイグレーション: サポートされていません

メモリ保持更新: サポートされていません

| サイズ | vCPU | プロセッサ | メモリ (GB) | メモリ帯域幅 GB/秒 | ベース CPU 周波数 (GHz) | 全コア周波数 (GHz、ピーク) | シングルコア周波数 (GHz、ピーク) | RDMA パフォーマンス (GB/秒) | MPI のサポート | 一時ストレージ (GB) | 最大データ ディスク数 | 最大イーサネット NIC |
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_HB60rs | 60 | AMD EPYC 7551 | 240 | 263 | 2.0 | 2.55 | 2.55 | 100 | All | 700 | 4 | 1 |

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