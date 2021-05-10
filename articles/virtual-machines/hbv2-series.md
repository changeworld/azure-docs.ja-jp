---
title: HBv2 シリーズ - Azure Virtual Machines
description: HBv2 シリーズ VM の仕様。
author: vermagit
ms.service: virtual-machines
ms.subservice: vm-sizes-hpc
ms.topic: conceptual
ms.date: 03/08/2021
ms.author: amverma
ms.reviewer: jushiman
ms.openlocfilehash: 25f0933c2d0b8b3c8ec227ce52c974a50a671043
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/13/2021
ms.locfileid: "107309732"
---
# <a name="hbv2-series"></a>HBv2 シリーズ

HBv2 シリーズ VM は、流体力学、有限要素解析、貯留層シミュレーションなどの、メモリ帯域幅に基づいたアプリケーション向けに最適化されています。 HBv2 VM は 120 個の AMD EPYC 7742 プロセッサ コア、CPU コアあたり 4 GB の RAM を備え、同時マルチスレッドはありません。 各 HBv2 VM では、最大 340 GB/秒のメモリ帯域幅が提供され、最大 4 テラフロップの FP64 コンピューティングが提供されます。

HBv2 シリーズ VM は、200 Gb/秒の Mellanox HDR InfiniBand を特徴としています。 これらの VM は、最適化された一貫性のある RDMA パフォーマンスを確保するために、ノンブロッキング ファット ツリー構造で接続されています。 これらの VM は、アダプティブ ルーティング、および標準 RC トランスポートと UD トランスポートに加え、動的接続トランスポート (DCT) をサポートしています。 これらの機能により、アプリケーションのパフォーマンス、スケーラビリティ、および整合性が向上するため、これらを使用することをお勧めします。

[Premium Storage](premium-storage-performance.md): サポートされています<br>
[Premium Storage キャッシュ](premium-storage-performance.md): サポートされています<br>
[Ultra Disks](disks-types.md#ultra-disk): サポートされています (可用性、使用状況、およびパフォーマンスの[詳細](https://techcommunity.microsoft.com/t5/azure-compute/ultra-disk-storage-for-hpc-and-gpu-vms/ba-p/2189312)を参照) <br>
[ライブ マイグレーション](maintenance-and-updates.md): サポートされていません<br>
[メモリ保持更新](maintenance-and-updates.md): サポートされていません<br>
[VM 世代サポート](generation-2.md): 第 1 世代と第 2 世代<br>
[高速ネットワーク](../virtual-network/create-vm-accelerated-networking-cli.md): サポートされています (パフォーマンスと潜在的な問題の[詳細](https://techcommunity.microsoft.com/t5/azure-compute/accelerated-networking-on-hb-hc-hbv2-and-ndv2/ba-p/2067965)) <br>
[エフェメラル OS ディスク](ephemeral-os-disks.md):サポートされていません <br>
<br>

| サイズ | vCPU | プロセッサ | メモリ (GiB) | メモリ帯域幅 GB/秒 | ベース CPU 周波数 (GHz) | 全コア周波数 (GHz、ピーク) | シングルコア周波数 (GHz、ピーク) | RDMA パフォーマンス (GB/秒) | MPI のサポート | 一時ストレージ (GiB) | 最大データ ディスク数 | 最大イーサネット vNIC 数 |
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_HB120rs_v2 | 120 | AMD EPYC 7V12 | 456 | 350 | 2.45 | 3.1 | 3.3 | 200 | All | 480 + 960 | 8 | 8 |

詳細については、下記を参照してください。
- [アーキテクチャと VM トポロジ](./workloads/hpc/hbv2-series-overview.md)
- サポート対象 OS など、サポート対象の[ソフトウェア スタック](./workloads/hpc/hbv2-series-overview.md#software-specifications)
- HBv2 シリーズ VM に見込まれる[パフォーマンス](./workloads/hpc/hbv2-performance.md)

[!INCLUDE [hpc-include](./workloads/hpc/includes/hpc-include.md)]

[!INCLUDE [virtual-machines-common-sizes-table-defs](../../includes/virtual-machines-common-sizes-table-defs.md)]

## <a name="other-sizes"></a>その他のサイズ

- [汎用](sizes-general.md)
- [メモリの最適化](sizes-memory.md)
- [ストレージの最適化](sizes-storage.md)
- [GPU の最適化](sizes-gpu.md)
- [ハイ パフォーマンス コンピューティング](sizes-hpc.md)
- [旧世代](sizes-previous-gen.md)

## <a name="next-steps"></a>次のステップ

- [Azure Compute Tech Community のブログ](https://techcommunity.microsoft.com/t5/azure-compute/bg-p/AzureCompute)で、最新の発表、HPC ワークロードの例、およびパフォーマンスの結果について参照します。
- HPC ワークロードの実行をアーキテクチャの面から見た概要については、「[Azure でのハイ パフォーマンス コンピューティング (HPC)](/azure/architecture/topics/high-performance-computing/)」をご覧ください。
- [Azure コンピューティング ユニット (ACU)](acu.md) を確認することで、Azure SKU 全体の処理性能を比較できます。
