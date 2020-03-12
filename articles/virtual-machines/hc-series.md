---
title: HC シリーズ - Azure Virtual Machines
description: HC シリーズ VM の仕様。
services: virtual-machines
author: jonbeck7
ms.service: virtual-machines
ms.topic: article
ms.date: 02/03/2020
ms.author: lahugh
ms.openlocfilehash: cc25fb9b21d535ef07bcfae673be48216427b370
ms.sourcegitcommit: 1f738a94b16f61e5dad0b29c98a6d355f724a2c7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/28/2020
ms.locfileid: "78164782"
---
# <a name="hc-series"></a>HC シリーズ

HC シリーズ VM は、陰解法有限要素解析、分子力学、計算化学などの、高密度計算に基づいたアプリケーション向けに最適化されています。 HC VM は 44 個の Intel Xeon Platinum 8168 プロセッサ コア、CPU コアあたり 8 GB の RAM を搭載し、ハイパースレッディングはありません。 Intel Xeon Platinum プラットフォームは、Intel Math Kernel Library などの Intel の豊富なソフトウェア ツールのエコシステムをサポートしています。

ACU: 297-315

Premium Storage: サポートされています

Premium Storage Caching: サポートされています

ライブ移行:サポートされていません

メモリ保持更新:サポートされていません

| サイズ | vCPU | プロセッサ | メモリ (GB) | メモリ帯域幅 GB/秒 | ベース CPU 周波数 (GHz) | 全コア周波数 (GHz、ピーク) | シングルコア周波数 (GHz、ピーク) | RDMA パフォーマンス (GB/秒) | MPI のサポート | 一時ストレージ (GB) | 最大データ ディスク数 | 最大イーサネット NIC |
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_HC44rs | 44 | Intel Xeon Platinum 8168 | 352 | 191 | 2.7 | 3.4 | 3.7 | 100 | All | 700 | 4 | 1 |

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