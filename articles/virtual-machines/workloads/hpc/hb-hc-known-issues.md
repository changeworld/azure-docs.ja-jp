---
title: HPC および GPU VM の既知の問題のトラブルシューティング - Azure Virtual Machines |Microsoft Docs
description: Azure の HPC VM と GPU VM のサイズに関する既知の問題のトラブルシューティングについて説明します。
author: vermagit
ms.service: virtual-machines
ms.subservice: hpc
ms.topic: article
ms.date: 03/25/2021
ms.author: amverma
ms.reviewer: cynthn
ms.openlocfilehash: d8c3a2d961cc5b6fd719b77dae07b6e46c3d8b65
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "105604840"
---
# <a name="known-issues-with-h-series-and-n-series-vms"></a>H シリーズおよび N シリーズ VM に関する既知の問題

この記事は、[H シリーズ](../../sizes-hpc.md)と [N シリーズ](../../sizes-gpu.md)の HPC および GPU VM の使用時に最近よく発生している問題とその解決方法を一覧で示すことを意図したものです。

## <a name="mofed-installation-on-ubuntu"></a>Ubuntu での MOFED のインストール
Ubuntu-18.04 では、Mellanox OFED で、カーネル バージョン `5.4.0-1039-azure #42` 以降との非互換性が示されています。これにより、VM の起動時間が約30 分に増加します。 これは、Mellanox OFED のバージョン 5.2-1.0.4.0 と 5.2-2.2.0.0 の両方について報告されています。
一時的な解決策は、**Canonical:UbuntuServer:18_04-lts-gen2:18.04.202101290** 以降の Marketplace イメージを使用することと、カーネルを更新しないことです。
この問題は、新しい MOFED で解決されることが予想されています (未定)。

## <a name="mpi-qp-creation-errors"></a>MPI QP 作成エラー
MPI ワークロードの実行中に、下に示すような InfiniBand QP 作成エラーがスローされた場合、VM を再起動し、ワークロードを再試行することをお勧めします。 この問題は、今後修正される予定です。

```bash
ib_mlx5_dv.c:150  UCX  ERROR mlx5dv_devx_obj_create(QP) failed, syndrome 0: Invalid argument
```

問題が発生した場合、次のようにキュー ペアの最大数の値を確認できます。
```bash
[user@azurehpc-vm ~]$ ibv_devinfo -vv | grep qp
max_qp: 4096
```

## <a name="accelerated-networking-on-hb-hc-hbv2-and-ndv2"></a>HB、HC、HBv2、NDv2 の高速ネットワーク

[Azure 高速ネットワーク](https://azure.microsoft.com/blog/maximize-your-vm-s-performance-with-accelerated-networking-now-generally-available-for-both-windows-and-linux/)が、RDMA と InfiniBand 対応で、SR-IOV 対応の VM サイズ、[HB](../../hb-series.md)、[HC](../../hc-series.md)、[HBv2](../../hbv2-series.md)、[NDv2](../../ndv2-series.md) で利用できるようになりました。 この機能により、Azure イーサネット ネットワーク上でのスループット (最大 30 Gbps) と待機時間が改善されます。 これは InfiniBand ネットワーク上の RDMA 機能とは別のものですが、この機能の一部のプラットフォーム変更は、InfiniBand を介してジョブを実行するときに特定の MPI 実装の動作に影響を与える可能性があります。 具体的には、一部の VM の InfiniBand インターフェイスの名前がわずかに異なる場合があります (以前の mlx5_0 とは異なり mlx5_1)。これは、特に UCX インターフェイスを (通常は OpenMPI と HPC-X と一緒に) 使用する場合には、MPI コマンド ラインを微調整する必要があります。 現時点での最も簡単な解決策は、CentOS-HPC VM イメージで最新の HPC-X を使用するか、必要でない場合は高速ネットワークを無効にすることです。
これに関する詳細については、こちらの [TechCommunity の記事](https://techcommunity.microsoft.com/t5/azure-compute/accelerated-networking-on-hb-hc-and-hbv2/ba-p/2067965)を参照してください。発生した問題に対処する方法についても記載されています。

## <a name="infiniband-driver-installation-on-non-sr-iov-vms"></a>SR-IOV ではない VM への InfiniBand ドライバーのインストール

現在、H16r、H16mr、NC24r は SR-IOV に対応していません。 InfiniBand スタックの分岐の詳細については、[こちら](../../sizes-hpc.md#rdma-capable-instances)を参照してください。
InfiniBand は、OFED ドライバーがある SR-IOV 対応 VM サイズ上で構成できます。一方、SR-IOV ではない VM サイズには ND ドライバーが必要です。 この IB サポートは [CentOS、RHEL、Ubuntu](configure.md) で適宜利用できます。

## <a name="duplicate-mac-with-cloud-init-with-ubuntu-on-h-series-and-n-series-vms"></a>H シリーズおよび N シリーズ VM 上の Ubuntu で cloud-init により発生する重複した MAC

Ubuntu VM イメージ上の cloud-init には、IB インターフェイスを有効にしようとするときに発生する既知の問題があります。 これは、VM の再起動時に、または一般化した後で VM イメージを作成しようとするときに発生する可能性があります。 VM ブート ログには、
```console
“Starting Network Service...RuntimeError: duplicate mac found! both 'eth1' and 'ib0' have mac”.
```

この "Ubuntu で cloud-init により発生する重複した MAC" は既知の問題です。 これは新しいカーネルで解決されます。 問題が発生した場合、回避策は次のとおりです。
1) (Ubuntu 18.04) マーケットプレース VM イメージをデプロイします
2) 必要なソフトウェア パッケージをインストールして IB を有効にします ([手順はこちら](https://techcommunity.microsoft.com/t5/azure-compute/configuring-infiniband-for-ubuntu-hpc-and-gpu-vms/ba-p/1221351))
3) waagent.conf を編集して EnableRDMA=y を変更します
4) cloud-init のネットワークを無効にします
    ```console
    echo network: {config: disabled} | sudo tee /etc/cloud/cloud.cfg.d/99-disable-network-config.cfg
    ```
5) cloud-init によって生成された netplan のネットワーク構成ファイルを編集して、MAC を削除します
    ```console
    sudo bash -c "cat > /etc/netplan/50-cloud-init.yaml" <<'EOF'
    network:
      ethernets:
        eth0:
          dhcp4: true
      version: 2
    EOF
    ```

## <a name="qp0-access-restriction"></a>qp0 アクセス制限

セキュリティの脆弱性の原因となる可能性がある低レベルのハードウェア アクセスを防ぐために、Queue Pair 0 はゲスト VM からアクセスできません。 これは、通常、ConnectX-5 NIC の管理や、ibdiagnet などの InfiniBand 診断の実行に関連するアクションのみに影響します。エンド ユーザー アプリケーション自体には影響しません。

## <a name="dram-on-hb-series-vms"></a>HB シリーズ VM の DRAM

現時点では、HB シリーズの VM はゲスト VM に 228 GB の RAM しか公開できません。 同様に、HBv2 で 458 GB、HBv3 VM で 448 GB です。 これは、ゲスト VM 用に予約されている AMD CCX (NUMA ドメイン) のローカル DRAM にページが割り当てられないようにするための Azure ハイパーバイザーの既知の制限によるものです。

## <a name="gss-proxy"></a>GSS プロキシ

GSS プロキシには CentOS/RHEL 7.5 の既知のバグがあり、NFS と共に使用すると、パフォーマンスと応答性が大幅に低下することがあります。 これは、次の方法で軽減できます。

```console
sed -i 's/GSS_USE_PROXY="yes"/GSS_USE_PROXY="no"/g' /etc/sysconfig/nfs
```

## <a name="cache-cleaning"></a>キャッシュのクリーニング

HPC システムでは、ジョブの完了後、次のユーザーに同じノードが割り当てられる前にメモリをクリーンアップすると便利なことがよくあります。 Linux でアプリケーションを実行した後、アプリケーションを実行していないにもかかわらず、バッファーのメモリが増加する一方で使用可能なメモリが減少することがあります。

![クリーニング前のコマンド プロンプトのスクリーンショット](./media/known-issues/cache-cleaning-1.png)

`numactl -H` を使用すると、メモリをバッファー処理している 1 つまたは複数 (場合によってはすべて) の NUMAnode がわかります。 Linux では、ユーザーは 3 つの方法でキャッシュをクリーンアップし、バッファー処理またはキャッシュされたメモリを "空き" に戻すことができます。 ルートになるか、sudo アクセス許可を持つ必要があります。

```console
echo 1 > /proc/sys/vm/drop_caches [frees page-cache]
echo 2 > /proc/sys/vm/drop_caches [frees slab objects e.g. dentries, inodes]
echo 3 > /proc/sys/vm/drop_caches [cleans page-cache and slab objects]
```

![クリーニング後のコマンド プロンプトのスクリーンショット](./media/known-issues/cache-cleaning-2.png)

## <a name="kernel-warnings"></a>カーネルの警告

Linux で HB シリーズ VM を起動するときに、次のカーネルの警告メッセージを無視することができます。 これは、Azure ハイパーバイザーの既知の制限事項であり、今後対処する予定です。

```console
[  0.004000] WARNING: CPU: 4 PID: 0 at arch/x86/kernel/smpboot.c:376 topology_sane.isra.3+0x80/0x90
[  0.004000] sched: CPU #4's llc-sibling CPU #0 is not on the same node! [node: 1 != 0]. Ignoring dependency.
[  0.004000] Modules linked in:
[  0.004000] CPU: 4 PID: 0 Comm: swapper/4 Not tainted 3.10.0-957.el7.x86_64 #1
[  0.004000] Hardware name: Microsoft Corporation Virtual Machine/Virtual Machine, BIOS 090007 05/18/2018
[  0.004000] Call Trace:
[  0.004000] [<ffffffffb8361dc1>] dump_stack+0x19/0x1b
[  0.004000] [<ffffffffb7c97648>] __warn+0xd8/0x100
[  0.004000] [<ffffffffb7c976cf>] warn_slowpath_fmt+0x5f/0x80
[  0.004000] [<ffffffffb7c02b34>] ? calibrate_delay+0x3e4/0x8b0
[  0.004000] [<ffffffffb7c574c0>] topology_sane.isra.3+0x80/0x90
[  0.004000] [<ffffffffb7c57782>] set_cpu_sibling_map+0x172/0x5b0
[  0.004000] [<ffffffffb7c57ce1>] start_secondary+0x121/0x270
[  0.004000] [<ffffffffb7c000d5>] start_cpu+0x5/0x14
[  0.004000] ---[ end trace 73fc0e0825d4ca1f ]---
```


## <a name="next-steps"></a>次のステップ

- [HB シリーズの概要](hb-series-overview.md)と [HC シリーズの概要](hc-series-overview.md)に関するページを参照して、パフォーマンスとスケーラビリティのためにワークロードを最適に構成する方法を学習します。
- [Azure Compute Tech Community のブログ](https://techcommunity.microsoft.com/t5/azure-compute/bg-p/AzureCompute)で、最新の発表、HPC ワークロードの例、およびパフォーマンスの結果について参照します。
- アーキテクチャの面から見た HPC ワークロードの実行の概要については、「[Azure でのハイ パフォーマンス コンピューティング (HPC)](/azure/architecture/topics/high-performance-computing/)」を参照してください。
