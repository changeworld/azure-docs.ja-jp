---
title: HC シリーズの VM サイズのパフォーマンス
description: Azure での HC シリーズ VM サイズのパフォーマンス テスト結果について説明します。
services: virtual-machines
documentationcenter: ''
author: vermagit
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.topic: article
ms.date: 09/10/2020
ms.author: amverma
ms.openlocfilehash: 0d63d9770dacf6a200e8b81e8d47d9f807a8a448
ms.sourcegitcommit: 80b9c8ef63cc75b226db5513ad81368b8ab28a28
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/16/2020
ms.locfileid: "90603438"
---
# <a name="hc-series-virtual-machine-sizes"></a>HC シリーズの仮想マシンのサイズ

HC シリーズのサイズについては、いくつかのパフォーマンス テストが実行されています。 このパフォーマンス テストの結果の一部を次に示します。

| ワークロード                                        | HB                    |
|-------------------------------------------------|-----------------------|
| STREAM Triad                                    | 190 GB/秒 (Intel MLC AVX-512)  |
| High-Performance Linpack (HPL)                  | 3520 GigaFLOPS (Rpeak)、2970 GigaFLOPS (Rmax) |
| RDMA の待機時間と帯域幅                        | 1.05 マイクロ秒、96.8 Gb/秒   |
| ローカル NVMe SSD 上の FIO                           | 1.3 GB/秒の読み取り、900 MB/秒の書き込み |  
| 4 枚の Azure Premium SSD 上の IOR (P30 マネージド ディスク、RAID0)**  | 780 MB/秒の読み取り、780 MB/秒の書き込み |

## <a name="mpi-latency"></a>MPI 待機時間

OSU マイクロベンチマーク スイートからの MPI 待機時間テストが実行されます。 サンプル スクリプトは [GitHub](https://github.com/Azure/azhpc-images/blob/04ddb645314a6b2b02e9edb1ea52f079241f1297/tests/run-tests.sh) にあります

```bash
./bin/mpirun_rsh -np 2 -hostfile ~/hostfile MV2_CPU_MAPPING=[INSERT CORE #] ./osu_latency 
```

:::image type="content" source="./media/latency-hc.png" alt-text="Azure HC の MPI 待機時間。":::

## <a name="mpi-bandwidth"></a>MPI 帯域幅

OSU マイクロベンチマーク スイートからの MPI 帯域幅テストが実行されます。 サンプル スクリプトは [GitHub](https://github.com/Azure/azhpc-images/blob/04ddb645314a6b2b02e9edb1ea52f079241f1297/tests/run-tests.sh) にあります

```bash
./mvapich2-2.3.install/bin/mpirun_rsh -np 2 -hostfile ~/hostfile MV2_CPU_MAPPING=[INSERT CORE #] ./mvapich2-2.3/osu_benchmarks/mpi/pt2pt/osu_bw
```

:::image type="content" source="./media/bandwidth-hc.png" alt-text="Azure HC の MPI 待機時間。":::


## <a name="mellanox-perftest"></a>Mellanox Perftest

[Mellanox Perftest パッケージ](https://community.mellanox.com/s/article/perftest-package)には、待機時間 (ib_send_lat) や帯域幅 (ib_send_bw) などの多くの InfiniBand テストがあります。 コマンドの例を次に示します。

```console
numactl --physcpubind=[INSERT CORE #]  ib_send_lat -a
```

## <a name="next-steps"></a>次の手順

- [Azure Compute Tech Community のブログ](https://techcommunity.microsoft.com/t5/azure-compute/bg-p/AzureCompute)で、最新の発表およびハイ パフォーマンス コンピューティング (HPC) の例と結果について参照します。
- HPC ワークロードの実行をアーキテクチャの面から見た概要については、「[Azure でのハイ パフォーマンス コンピューティング (HPC)](/azure/architecture/topics/high-performance-computing/)」をご覧ください。
