---
title: Mv2 シリーズ - Azure Virtual Machines
description: Mv2 シリーズ VM の仕様。
author: ayshakeen
ms.service: virtual-machines
ms.subservice: vm-sizes-memory
ms.topic: conceptual
ms.date: 04/07/2020
ms.author: jushiman
ms.openlocfilehash: 0f311645f29f5daaaf65474a60031ee8cceff688
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/02/2021
ms.locfileid: "131063672"
---
# <a name="mv2-series"></a>Mv2 シリーズ

**適用対象:** :heavy_check_mark: Linux VM :heavy_check_mark: Windows VM :heavy_check_mark: フレキシブル スケール セット :heavy_check_mark: ユニフォーム スケール セット

Mv2 シリーズは、高スループットで低待機時間のプラットフォームを特長としています。このプラットフォームは、全コア ベース周波数 2.5 GHz、最大ターボ周波数 3.8 GHz を備えたハイパースレッド Intel® Xeon® Platinum 8180 M 2.5 GHz (Skylake) プロセッサで実行されています。 すべての Mv2 シリーズ仮想マシンで、標準またはプレミアムの永続ディスクを使用できます。 Mv2 シリーズ インスタンスはメモリ最適化済み VM サイズで、非常に優れたコンピューティング性能によって大規模なメモリ内データベースとワークロードをサポートし、リレーショナル データベース サーバー、大規模キャッシュ、インメモリ分析に適した高いメモリ対 CPU 比率を提供します。

Mv2 シリーズの VM は Intel® ハイパー スレッディング テクノロジを利用しています

[Premium Storage](premium-storage-performance.md): サポートされています<br>
[Premium Storage キャッシュ](premium-storage-performance.md): サポートされています<br>
[ライブ マイグレーション](maintenance-and-updates.md): サポートされていません<br>
[メモリ保持更新](maintenance-and-updates.md): サポートされていません<br>
[VM 世代サポート](generation-2.md): 第 2 世代<br>
[書き込みアクセラレータ](./how-to-enable-write-accelerator.md): サポートされています<br>
[高速ネットワーク](../virtual-network/create-vm-accelerated-networking-cli.md):サポートされています<br>
[エフェメラル OS ディスク](ephemeral-os-disks.md):サポートされています <br>
<br>

|サイズ | vCPU | メモリ:GiB | 一時ストレージ (SSD) GiB | 最大データ ディスク数 | キャッシュが有効な場合および一時ストレージの最大スループットIOPS/MBps (キャッシュ サイズは GiB 単位) | キャッシュが無効な場合の最大ディスク スループット: IOPS/MBps | 最大 NIC 数 | 必要なネットワーク帯域幅 (Mbps) |
|---|---|---|---|---|---|---|---|---|
| Standard_M208ms_v2<sup>1</sup> | 208 | 5700 | 4096 | 64 | 80000 / 800 (7040) | 40000 / 1000 | 8 | 16000 |
| Standard_M208s_v2<sup>1</sup> | 208 | 2850 | 4096 | 64 | 80000 / 800 (7040) | 40000 / 1000 | 8 | 16000 |
| Standard_M416ms_v2<sup>1,2</sup> | 416 | 11400 | 8192 | 64 | 250000 / 1600 (14080) | 80000 / 2000 | 8 | 32000 |
| Standard_M416s_v2<sup>1,2</sup> | 416 | 5700 | 8192 | 64 | 250000 / 1600 (14080) | 80000 / 2000 | 8 | 32000 |

<sup>1</sup> Mv2 シリーズの VM は第 2 世代のみであり、第 2 世代でサポートされているイメージのサブセットがサポートされます。 Mv2 シリーズでサポートされているイメージの完全な一覧については、以下を参照してください。 Linux を使用している場合、イメージを検索して選択する方法については、[Azure での第 2 世代 VM のサポート](./generation-2.md)に関するページを参照してください。 Windows を使用している場合、イメージを検索して選択する方法については、[Azure での第 2 世代 VM のサポート](./generation-2.md)に関するページを参照してください。 

- Windows Server 2019 以降
- SUSE Linux Enterprise Server 12 SP4 以降または SUSE Linux Enterprise Server 15 SP1 以降
- Red Hat Enterprise Linux 7.6 以降、および 8.1 以降
- Oracle Enterprise Linux 7.7 以降、および 8.1 以降
- カーネル 5.4.0-azure 以降が搭載された Ubuntu 18.04

<sup>2</sup> [コア数を制限したサイズも提供しています](./constrained-vcpu.md)。


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
