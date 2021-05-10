---
title: HBv3 シリーズ VM の概要、アーキテクチャ、トポロジ - Azure Virtual Machines | Microsoft Docs
description: Azure での HBv3 シリーズ VM のサイズについて説明します。
services: virtual-machines
author: vermagit
tags: azure-resource-manager
ms.service: virtual-machines
ms.subservice: workloads
ms.workload: infrastructure-services
ms.topic: article
ms.date: 03/25/2021
ms.author: amverma
ms.reviewer: cynthn
ms.openlocfilehash: f78420a65cd9c2402266eb9ba973eabe758d7ee5
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "105608286"
---
# <a name="hbv3-series-virtual-machine-overview"></a>HBv3 シリーズの仮想マシンの概要 

[HBv3 シリーズ](../../hbv3-series.md)のサーバーは 64 コア EPYC 7V13 CPU を 2 個搭載しており、合わせて 128 個の物理 "Zen3" コアとなります。 同時マルチスレッド (SMT) は HBv3 では無効になっています。 これらの 128 個のコアは 16 個のセクション (ソケットあたり 8 個) に分けられ、各セクションに含まれる 8 個のプロセッサ コアが 32 MB の L3 キャッシュに一様にアクセスできます。 Azure HBv3 サーバーでは、次の AMD BIOS 設定も実行されます。

```bash
Nodes per Socket (NPS) = 2
L3 as NUMA = Disabled
NUMA domains within VM OS = 4
C-states = Enabled
```

その結果、サーバーを起動する 4 つの NUMA ドメイン (ソケットあたり 2 つ) は、それぞれのサイズが 32 コアになります。 各 NUMA は、3200 MT/秒で動作する 4 つのチャネルの物理 DRAM に直接アクセスできます。

Azure ハイパーバイザーが VM に干渉することなく動作する余地を与えるため、予備としてサーバーあたり 8 個の物理コアを確保しています。

## <a name="vm-topology"></a>VM のトポロジ

次の図は、サーバーのトポロジを示しています。 これらの 8 つのハイパーバイザー ホスト コア (黄色) が両方の CPU ソケットに対して対称的に予約されており、各 NUMA ドメインにある特定の Core Complex Die (CCD) の最初の 2 つのコアが使用されています。残りのコアは、HBv3 シリーズ VM 用になります (緑色)。

![HBv3 シリーズ サーバーのトポロジ](./media/architecture/hbv3/hbv3-topology-server.png)

CCD 境界は NUMA 境界と同等ではないことに注意してください。 HBv3 では、ホスト サーバー レベルとゲスト VM 内の両方で、4 つの連続する (4) CCD のグループが NUMA ドメインとして構成されます。 したがって、すべての HBv3 VM サイズで、下に示されていように OS とアプリケーションに対して提示される 4 つの NUMA ドメインが公開されます。これらは、特定の [HBv3 VM サイズ](../../hbv3-series.md)に基づいて、それぞれコアの数が異なります。

![HBv3 シリーズ VM のトポロジ](./media/architecture/hbv3/hbv3-topology-vm.png)

各 HBv3 VM のサイズは、次のように、AMD EPYC 7003 シリーズの各種 CPU の物理レイアウト、機能、パフォーマンスに類似しています。

| HBv3 シリーズ VM のサイズ             | NUMA ドメイン | NUMA ドメインごとのコア数  | AMD EPYC との類似性         |
|---------------------------------|--------------|------------------------|----------------------------------|
Standard_HB120rs_v3               | 4            | 30                     | デュアル ソケット EPYC 7713            |
Standard_HB120r-96s_v3            | 4            | 24                     | デュアル ソケット EPYC 7643            |
Standard_HB120r-64s_v3            | 4            | 16                     | デュアル ソケット EPYC 7543            |
Standard_HB120r-32s_v3            | 4            | 8                      | デュアル ソケット EPYC 7313            |
Standard_HB120r-16s_v3            | 4            | 4                      | デュアル ソケット EPYC 72F3            |

> [!NOTE]
> 制約付きコア VM のサイズでは、VM に公開される物理コアの数のみ減少します。 すべてのグローバル共有アセット (RAM、メモリ帯域幅、L3 キャッシュ、GMI および xGMI 接続、InfiniBand、Azure イーサネット ネットワーク、ローカル SSD) は一定のままです。 これにより、お客様は特定のワークロードまたはソフトウェア ライセンスのニーズに合わせて最適な VM サイズを選択できます。

各 HBv3 VM サイズの仮想 NUMA マッピングは、基になる物理 NUMA トポロジにマップされます。 ハードウェア トポロジが誤って抽象化される可能性はありません。 

各種の [HBV3 VM サイズ](../../hbv3-series.md)の正確なトポロジは、[lstopo](https://linux.die.net/man/1/lstopo) の出力を使用して次のように表示されます。
```bash
lstopo-no-graphics --no-io --no-legend --of txt
```
<br>
<details>
<summary>クリックして Standard_HB120rs_v3 の lstopo 出力を表示</summary>

![HBv3-120 VM の lstopo 出力](./media/architecture/hbv3/hbv3-120-lstopo.png)
</details>

<details>
<summary>クリックして Standard_HB120rs-96_v3 の lstopo 出力を表示</summary>

![HBv3-96 VM の lstopo 出力](./media/architecture/hbv3/hbv3-96-lstopo.png)
</details>

<details>
<summary>クリックして Standard_HB120rs-64_v3 の lstopo 出力を表示</summary>

![HBv3-64 VM の lstopo 出力](./media/architecture/hbv3/hbv3-64-lstopo.png)
</details>

<details>
<summary>クリックして Standard_HB120rs-32_v3 の lstopo 出力を表示</summary>

![HBv3-32 VM の lstopo 出力](./media/architecture/hbv3/hbv3-32-lstopo.png)
</details>

<details>
<summary>クリックして Standard_HB120rs-16_v3 の lstopo 出力を表示</summary>

![HBv3-16 VM の lstopo 出力](./media/architecture/hbv3/hbv3-16-lstopo.png)
</details>

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
