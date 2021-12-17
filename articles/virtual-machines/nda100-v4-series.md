---
title: ND A100 v4-シリーズ
description: ND A100 v4 シリーズ VM の仕様。
ms.service: virtual-machines
ms.subservice: vm-sizes-gpu
ms.topic: conceptual
ms.date: 05/26/2021
ms.openlocfilehash: d18172751bb58d6a21115dd482024bdfcc47bea1
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/02/2021
ms.locfileid: "131060442"
---
# <a name="nd-a100-v4-series"></a>ND A100 v4-シリーズ

ND A100 v4 シリーズの仮想マシンは、ハイエンドのディープ ラーニング トレーニングと密結合のスケールアップおよびスケールアウトの HPC ワークロード向けに設計された、Azure GPU ファミリに新たに追加された主力製品です。 

ND A100 v4 シリーズは、単一の仮想マシン (VM) と 8 つの NVIDIA アンペア A100 テンソル コア GPU で開始されます。 ND A100 v4 ベースのデプロイでは、VM あたり 1.6 Tb/秒の相互接続帯域幅を備えた数千の GPU までスケールアップできます。 VM 内の各 GPU には、トポロジに依存しない専用の 200 Gb/秒 NVIDIA Mellanox HDR InfiniBand 接続が用意されています。 これらの接続は、同じ仮想マシンスケール セットを占有する VM 間で自動的に構成され、GPUDirect RDMA をサポートします。

各 GPU では、VM 内の通信に対して NVLINK 3.0 接続が使用されます。また、インスタンスは 96 個の物理第2世代 AMD Epyc™ CPU コアにも支えられています。

これらのインスタンスは、GPU アクセラレーションを "標準" でサポートするさまざまな AI、ML、分析ツール (TensorFlow、Pytorch、Caffe、RAPIDS、そのほかのフレームワークなど) を活用して、優れたパフォーマンスを発揮します。 さらに、GMU のシームレスなクラスタリング用に NVIDIA の NCCL2 通信ライブラリに基づいて構築された、多数の既存の AI および HPC ツールによって、スケールアウト InfiniBand 相互接続がサポートされています。

> [!IMPORTANT]
> ND A100 v4 VM の使用を開始するには、「[HPC ワークロードの構成と最適化](./workloads/hpc/configure.md)」を参照してドライバーとネットワークの構成などの手順を確認してください。
> GPU メモリ I/O 占有領域の増加により、ND A100 v4 では、[第 2 世代の VM](./generation-2.md) とマーケットプレース イメージを使用する必要があります。 [AZURE HPC イメージ](./workloads/hpc/configure.md)を強くお勧めします。 Azure HPC Ubuntu 18.04、20.04、および Azure HPC CentOS 7.9 のイメージがサポートされています。
> 

<br>

[Premium Storage](premium-storage-performance.md): サポートされています<br>
[Premium Storage キャッシュ](premium-storage-performance.md): サポートされています<br>
[Ultra Disk](disks-types.md#ultra-disks): サポートされています (可用性、使用状況、およびパフォーマンスについては、[詳細をご覧ください](https://techcommunity.microsoft.com/t5/azure-compute/ultra-disk-storage-for-hpc-and-gpu-vms/ba-p/2189312)) <br>
[ライブ マイグレーション](maintenance-and-updates.md): サポートされていません<br>
[メモリ保持更新](maintenance-and-updates.md): サポートされていません<br>
[VM 世代サポート](generation-2.md): 第 2 世代<br>
[高速ネットワーク](../virtual-network/create-vm-accelerated-networking-cli.md):サポートされていません<br>
[エフェメラル OS ディスク](ephemeral-os-disks.md):サポートされています<br>
InfiniBand: サポートされています, GPUDirect RDMA, 8 x 200 Gigabit HDR<br>
Nvidia NVLink Interconnect:サポートされています<br>
<br>
ND A100 v4 シリーズは次のカーネル バージョンをサポートしています。 <br>
CentOS 7.9 HPC: 3.10.0-1160.24.1.el7.x86_64 <br>
Ubuntu 18.04: 5.4.0-1043-azure <br>
Ubuntu 20.04: 5.4.0-1046-azure <br>
<br>

| サイズ | vCPU | メモリ:GiB | 一時ストレージ (SSD): GiB | GPU | GPU メモリ: GiB | 最大データ ディスク数 | キャッシュが無効な場合の最大ディスク スループット: IOPS/MBps | 最大ネットワーク帯域幅 | 最大 NIC 数 |
|---|---|---|---|---|---|---|---|---|---|
| Standard_ND96asr_v4 | 96 | 900 | 6000 | 8 A100 40 GB GPUs (NVLink 3.0) | 40 | 32 | 80,000 / 800 | 24,000 Mbps | 8 |

[!INCLUDE [virtual-machines-common-sizes-table-defs](../../includes/virtual-machines-common-sizes-table-defs.md)]

## <a name="other-sizes-and-information"></a>その他のサイズと情報

- [汎用](sizes-general.md)
- [メモリの最適化](sizes-memory.md)
- [ストレージの最適化](sizes-storage.md)
- [GPU の最適化](sizes-gpu.md)
- [ハイ パフォーマンス コンピューティング](sizes-hpc.md)
- [旧世代](sizes-previous-gen.md)

料金計算ツール:[料金計算ツール](https://azure.microsoft.com/pricing/calculator/)

ディスクの種類の詳細については、「[Azure で利用できるディスクの種類](disks-types.md)」を参照してください

## <a name="next-steps"></a>次のステップ

[Azure コンピューティング ユニット (ACU)](acu.md) を確認することで、Azure SKU 全体の処理性能を比較できます。
