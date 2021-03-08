---
title: HBv2 シリーズ VM の概要 - Azure Virtual Machines | Microsoft Docs
description: Azure での HBv2 シリーズ VM のサイズについて説明します。
services: virtual-machines
author: vermagit
tags: azure-resource-manager
ms.service: virtual-machines
ms.subservice: hpc
ms.workload: infrastructure-services
ms.topic: article
ms.date: 09/28/2020
ms.author: amverma
ms.reviewer: cynthn
ms.openlocfilehash: 6648f77c5eacf40f848bc9b24aa6e257d8adf626
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/02/2021
ms.locfileid: "101674643"
---
# <a name="hbv2-series-virtual-machine-overview"></a>HBv2 シリーズの仮想マシンの概要 

 
AMD EPYC でハイパフォーマンス コンピューティング (HPC) アプリケーションのパフォーマンスを最大化するには、綿密なアプローチによるメモリの局所性とプロセスの配置が必要です。 以下、AMD EPYC アーキテクチャと、Azure での HPC アプリケーション向けのその実装について概説します。 物理 NUMA ドメインを指して **pNUMA** という用語を、また仮想化 NUMA ドメインを指して **vNUMA** を使用します。 

物理的には、[HBv2 シリーズ](../../hbv2-series.md) サーバーは 2 * 64 コアの EPYC 7742 CPU であり、物理コアは合計 128 個です。 これらの 128 コアは 32 の pNUMA ドメイン (ソケットあたり 16) に分割されます。それぞれは 4 コアであり、AMD によって **コア コンプレックス** (または **CCX**) という名前が付けられています。 各 CCX には独自の L 3 キャッシュがあり、OS はこれによって pNUMA/vNUMA 境界を認識します。 4 つの隣接する CCX によって、2 チャンネルの物理的な DRAM へのアクセスが共有されます。 

Azure ハイパーバイザーが VM に干渉せずに動作する余地を確保するために、物理 pNUMA ドメイン 0 および 16 (各 CPU ソケットの最初の CCX) を予約しています。 残りの 30 の pNUMA ドメインはすべて VM に割り当てられ、その時点で vNUMA になります。 そのため、VM では以下が認識されます。

VM あたり `(30 vNUMA domains) * (4 cores/vNUMA) = 120` コア 

VM 自体に、pNUMA 0 と 16 が予約されているという認識はありません。 0-29 として認識されている vNUMA が列挙され、ソケットあたり 15 の vNUMA が対称的になっています (vSocket 0 上の vNUMA 0-14、vSocket 1 上の vNUMA 15-29)。 次のセクションでは、この非対称の NUMA レイアウトで MPI アプリケーションを最適に実行する方法を説明します。 

基になるシリコンはそのままゲスト VM に公開しているため、プロセス固定は HBv2 シリーズ VM 上で機能します。 最適なパフォーマンスと一貫性のために、プロセス固定を強くお勧めします。 


## <a name="hardware-specifications"></a>ハードウェア仕様 

| ハードウェア仕様          | HBv2 シリーズ VM                   | 
|----------------------------------|----------------------------------|
| コア                            | 120 (SMT は無効)               | 
| CPU                              | AMD EPYC 7742                    | 
| CPU 周波数 (非 AVX)          | ~ 3.1 GHz (シングル + 全コア)    | 
| メモリ                           | 4 GB/コア (合計 480 GB)         | 
| ローカル ディスク                       | 960 GB NVMe (ブロック)、480 GB SSD (ページ ファイル) | 
| Infiniband                       | 200 GB/秒 EDR Mellanox ConnectX-6 | 
| ネットワーク                          | 50 GB/秒イーサネット (40 GB/秒使用可能) Azure 第 2 世代 SmartNIC | 


## <a name="software-specifications"></a>ソフトウェア仕様 

| ソフトウェア仕様     | HBv2 シリーズ VM                                            | 
|-----------------------------|-----------------------------------------------------------|
| 最大 MPI ジョブ サイズ            | 36000 コア (1 つの仮想マシン スケール セットに 300 の VM、singlePlacementGroup=true) |
| MPI のサポート                 | HPC-X、Intel MPI、OpenMPI、MVAPICH2、MPICH、Platform MPI  |
| その他のフレームワーク       | Unified Communication X、libfabric、PGAS                  |
| Azure Storage のサポート       | Standard および Premium ディスク (最大 8 ディスク)              |
| SRIOV RDMA の OS サポート   | CentOS/RHEL 7.6 以降、SLES 12 SP4 以降、WinServer 2016 以降           |
| Orchestrator のサポート        | CycleCloud、Batch                                         | 


## <a name="next-steps"></a>次のステップ

- [AMD EPYC アーキテクチャ](https://bit.ly/2Epv3kC)および[マルチチップ アーキテクチャ](https://bit.ly/2GpQIMb)の詳細を確認します。 詳細については、[AMD EPYC プロセッサ向けの HPC チューニング ガイド](https://bit.ly/2T3AWZ9)を参照してください。
- [Azure Compute Tech Community のブログ](https://techcommunity.microsoft.com/t5/azure-compute/bg-p/AzureCompute)で、最新の発表および HPC のいくつかの例について参照します。
- HPC ワークロードの実行をアーキテクチャの面から見た概要については、「[Azure でのハイ パフォーマンス コンピューティング (HPC)](/azure/architecture/topics/high-performance-computing/)」をご覧ください。