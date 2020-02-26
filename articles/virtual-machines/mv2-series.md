---
title: Mv2 シリーズ - Azure Virtual Machines
description: Mv2 シリーズ VM の仕様。
services: virtual-machines
author: jonbeck7
ms.service: virtual-machines
ms.topic: article
ms.date: 02/03/2020
ms.author: lahugh
ms.openlocfilehash: caa80443a189874bfd699dba412fb749c8711556
ms.sourcegitcommit: 98a5a6765da081e7f294d3cb19c1357d10ca333f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/20/2020
ms.locfileid: "77492504"
---
# <a name="mv2-series"></a>Mv2 シリーズ

Mv2 シリーズは、高スループットで低待機時間のプラットフォームを特長としています。このプラットフォームは、全コア ベース周波数 2.5 GHz、最大ターボ周波数 3.8 GHz を備えたハイパースレッド Intel® Xeon® Platinum 8180 M 2.5 GHz (Skylake) プロセッサで実行されています。 すべての Mv2 シリーズ仮想マシンで、標準またはプレミアムの永続ディスクを使用できます。 Mv2 シリーズ インスタンスはメモリ最適化済み VM サイズで、非常に優れたコンピューティング性能によって大規模なメモリ内データベースとワークロードをサポートし、リレーショナル データベース サーバー、大規模キャッシュ、インメモリ分析に適した高いメモリ対 CPU 比率を提供します。

Mv2 シリーズの VM は Intel® ハイパー スレッディング テクノロジを利用しています

Premium Storage: サポートされています

Premium Storage キャッシュ:サポートされています

書き込みアクセラレータ:[サポートされています](https://docs.microsoft.com/azure/virtual-machines/windows/how-to-enable-write-accelerator)

|Size | vCPU | メモリ:GiB | 一時ストレージ (SSD) GiB | 最大データ ディスク数 | キャッシュが有効な場合の一時ストレージの最大スループット: IOPS/MBps (キャッシュ サイズは GiB 単位) | キャッシュが無効な場合の最大ディスク スループット: IOPS/MBps | 最大 NIC 数/想定ネットワーク帯域幅 (Mbps) |
|---|---|---|---|---|---|---|---|
| Standard_M208ms_v2<sup>1</sup> | 208 | 5700 | 4096 | 64 | 80000 / 800 (7040) | 40000 / 1000 | 8/1,6000 |
| Standard_M208s_v2<sup>1</sup> | 208 | 2850 | 4096 | 64 | 80000 / 800 (7040) | 40000 / 1000 | 8/1,6000 |
| Standard_M416ms_v2<sup>1、2</sup> | 416 | 11400 | 8192 | 64 | 250000 / 1600 (14080) | 80000 / 2000 | 8 / 32000 |
| Standard_M416s_v2<sup>1、2</sup> | 416 | 5700 | 8192 | 64 | 250000 / 1600 (14080) | 80000 / 2000 | 8 / 32000 |

<sup>1</sup> Mv2 シリーズの VM は第 2 世代のみです。 Linux を使用している場合、イメージを検索して選択する方法については、[Azure での第 2 世代 VM のサポート](/linux/generation-2.md)に関するページを参照してください。

<sup>2</sup> M416ms_v2 および M416s_v2 のサイズについては、次のイメージの初期サポートしかないことに注意してください:"GEN2:SUSE Linux Enterprise Server (SLES) 12 SP4 for SAP Applications"

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