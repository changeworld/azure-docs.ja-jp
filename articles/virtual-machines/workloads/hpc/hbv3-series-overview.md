---
title: HBv3 シリーズ VM の概要 - Azure Virtual Machines | Microsoft Docs
description: Azure での HBv3 シリーズ VM のサイズについて説明します。
services: virtual-machines
author: vermagit
tags: azure-resource-manager
ms.service: virtual-machines
ms.subservice: workloads
ms.workload: infrastructure-services
ms.topic: article
ms.date: 03/12/2021
ms.author: amverma
ms.reviewer: cynthn
ms.openlocfilehash: d1abd03f517f9e0b13a2994418cbae5cfbe22454
ms.sourcegitcommit: ba3a4d58a17021a922f763095ddc3cf768b11336
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/23/2021
ms.locfileid: "104801870"
---
# <a name="hbv3-series-virtual-machine-overview"></a>HBv3 シリーズの仮想マシンの概要 

[HBv3 シリーズ](../../hbv3-series.md)のサーバーは 64 コア EPYC 7V13 CPU を 2 個搭載しており、合わせて 128 個の物理 "Zen3" コアとなります。 同時マルチスレッド (SMT) は HBv3 では無効になっています。 これらの 128 個のコアは 16 個のセクション (ソケットあたり 8 個) に分けられ、各セクションに含まれる 8 個のプロセッサ コアが 32 MB の L3 キャッシュに一様にアクセスできます。 Azure HBv3 サーバーでは、次の AMD BIOS 設定も実行されます。

```bash
Nodes per Socket =2
L3 as NUMA = Disabled
```

その結果、サーバーを起動する 4 つの NUMA ドメイン (ソケットあたり 2 つ) は、それぞれのサイズが 32 コアになります。 各 NUMA は、3200 MT/秒で動作する 4 つのチャネルの物理 DRAM に直接アクセスできます。

Azure ハイパーバイザーが VM に干渉することなく動作する余地を与えるため、予備としてサーバーあたり 8 個の物理コアを確保しています。 

制約付きコア VM のサイズで減少するのは VM に公開される物理コアの数だけであることに注意してください。 すべてのグローバル共有アセット (RAM、メモリ帯域幅、L3 キャッシュ、GMI および xGMI 接続、InfiniBand、Azure イーサネット ネットワーク、ローカル SSD) は一定のままです。 これにより、お客様は特定のワークロードまたはソフトウェア ライセンスのニーズに合わせて最適な VM サイズを選択できます。

次の図は、Azure Hypervisor (黄色) と HBv3 シリーズ VM (緑) 用に予約されているコアの分離を示しています。
![Azure Hypervisor と HBv3 シリーズ VM 用に予約されているコアの分離](./media/architecture/hbv3-segregation-cores.png)

## <a name="infiniband-networking"></a>InfiniBand ネットワーク
HBv3 VM では Nvidia Mellanox HDR InfiniBand ネットワーク アダプター (ConnectX-6) も最大 200 Gb/秒で動作しています。NIC は SRIOV 経由で VM に渡されるため、ネットワーク トラフィックがハイパーバイザーをバイパスできます。 その結果、標準の Mellanox OFED ドライバーが、ベアメタル環境と同様に HBv3 VM にロードされます。

HBv3 VM では、アダプティブ ルーティング、標準の RC および UD トランスポートに加えて Dynamic Connected Transport (DCT)、ConnectX-6 アダプターのオンボード プロセッサへのハードウェアベースの MPI コレクティブのオフロードもサポートされています。 これらの機能により、アプリケーションのパフォーマンス、スケーラビリティ、および整合性が向上するため、それらを使用することを強くお勧めします。

## <a name="temporary-storage"></a>一時ストレージ
HBv3 VM の特徴は、物理的にローカルの 3 つの SSD デバイスです。 1 つのデバイスがページ ファイルとして機能するよう事前フォーマットされており、VM 内に汎用 "SSD" デバイスとして表示されます。

他の 2 つのより大きな SSD は、NVMeDirect を介した未フォーマットのブロック NVMe デバイスとして提供されています。 ブロック NVMe デバイスはハイパーバイザーをバイパスするので、帯域幅が大きく、IOPS が向上し、IOP あたりの待機時間が短くなります。

ストライピングされた配列でペアになっている場合、NVMe SSD では最大 7 GB/秒の読み取りと 3 GB/秒の書き込みが可能で、最大 186,000 の IOPS (読み取り) と 201,000 の IOPS (書き込み) というディープ キューの深さが提供されます。

## <a name="hardware-specifications"></a>ハードウェア仕様 

| ハードウェア仕様          | HBv3 シリーズ VM              |
|----------------------------------|----------------------------------|
| コア                            | 120、96、64、32、または 16 (SMT 無効)               | 
| CPU                              | AMD EPYC 7V13                   | 
| CPU 周波数 (非 AVX)          | 3.1 GHz (すべてのコア)、3.675 GHz (最大 10 コア)    | 
| メモリ                           | 448 GB (コアあたりの RAM は VM のサイズによって異なる)         | 
| ローカル ディスク                       | 960 GB NVMe × 2 (ブロック)、480 GB SSD (ページ ファイル) | 
| Infiniband                       | 200 Gb/s Mellanox ConnectX-6 HDR InfiniBand | 
| ネットワーク                          | 50 GB/秒イーサネット (40 GB/秒使用可能) Azure 第 2 世代 SmartNIC | 

## <a name="software-specifications"></a>ソフトウェア仕様 

| ソフトウェア仕様        | HBv3 シリーズ VM                                            | 
|--------------------------------|-----------------------------------------------------------|
| 最大 MPI ジョブ サイズ               | 36,000 コア (1 つの仮想マシン スケール セットに 300 の VM、singlePlacementGroup=true) |
| MPI のサポート                    | HPC-X、Intel MPI、OpenMPI、MVAPICH2、MPICH  |
| その他のフレームワーク          | UCX、libfabric、PGAS                  |
| Azure Storage のサポート          | Standard および Premium のディスク (最大 32 ディスク)              |
| SRIOV RDMA の OS サポート      | CentOS または RHEL 7.6 以降、Ubuntu 18.04 以降、SLES 12 SP4 以降、WinServer 2016 以降           |
| パフォーマンス向上のために推奨される OS | CentOS 8.1、Windows Server 2019 以降
| Orchestrator のサポート           | Azure CycleCloud、Azure Batch、AKS。[クラスター構成オプション](../../sizes-hpc.md#cluster-configuration-options)                      | 

> [!NOTE] 
> Windows Server 2012 R2 は、HBv3、および 64 個を超える (仮想または物理) コアを備えたその他の VM ではサポートされていません。 詳細については、[こちら](https://docs.microsoft.com/windows-server/virtualization/hyper-v/supported-windows-guest-operating-systems-for-hyper-v-on-windows)を参照してください。

## <a name="next-steps"></a>次のステップ

- [Azure Compute Tech Community のブログ](https://techcommunity.microsoft.com/t5/azure-compute/bg-p/AzureCompute)で、最新の発表、HPC ワークロードの例、およびパフォーマンスの結果について参照します。
- HPC ワークロードの実行をアーキテクチャの面から見た概要については、「[Azure でのハイ パフォーマンス コンピューティング (HPC)](/azure/architecture/topics/high-performance-computing/)」をご覧ください。