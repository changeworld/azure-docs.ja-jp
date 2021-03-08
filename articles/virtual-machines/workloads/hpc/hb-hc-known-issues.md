---
title: HPC および GPU VM の既知の問題のトラブルシューティング - Azure Virtual Machines |Microsoft Docs
description: Azure の HPC VM と GPU VM のサイズに関する既知の問題のトラブルシューティングについて説明します。
author: vermagit
ms.service: virtual-machines
ms.subservice: hpc
ms.topic: article
ms.date: 1/19/2021
ms.author: amverma
ms.reviewer: cynthn
ms.openlocfilehash: 83f9778da91cebb651d98e2e85748cda7435230a
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/02/2021
ms.locfileid: "101674675"
---
# <a name="known-issues-with-h-series-and-n-series-vms"></a>H シリーズおよび N シリーズ VM に関する既知の問題

この記事では、[H シリーズ](../../sizes-hpc.md)と [N シリーズ](../../sizes-gpu.md)の HPC および GPU VM を使用する場合の最も一般的な問題とその解決方法について説明します。

## <a name="accelerated-networking-on-hb-hc-hbv2-and-ndv2"></a>HB、HC、HBv2、NDv2 の高速ネットワーク

[Azure 高速ネットワーク](https://azure.microsoft.com/blog/maximize-your-vm-s-performance-with-accelerated-networking-now-generally-available-for-both-windows-and-linux/)が、RDMA および InfiniBand 対応および SR-IOV 対応の VM サイズの [HB](../../hb-series.md)、[HC](../../hc-series.md)、[HBv2](../../hbv2-series.md) および [NDv2](../../ndv2-series.md) で利用できるようになりました。 この機能により、Azure イーサネット ネットワーク上でのスループット (最大 30 Gbps) と待機時間が改善されます。 これは InfiniBand ネットワーク上の RDMA 機能とは別のものですが、この機能の一部のプラットフォーム変更は、InfiniBand を介してジョブを実行するときに特定の MPI 実装の動作に影響を与える可能性があります。 具体的には、一部の VM の InfiniBand インターフェイスの名前がわずかに異なる場合があります (以前の mlx5_0 とは異なり mlx5_1)。これは、特に UCX インターフェイスを (通常は OpenMPI と HPC-X と一緒に) 使用する場合には、MPI コマンド ラインを微調整する必要があります。
これに関する詳細については、こちらの[ブログ記事](https://techcommunity.microsoft.com/t5/azure-compute/accelerated-networking-on-hb-hc-and-hbv2/ba-p/2067965)を参照してください。発生した問題に対処する方法についても記載されています。

## <a name="infiniband-driver-installation-on-n-series-vms"></a>N シリーズ VM への InfiniBand ドライバーのインストール

NC24r_v3 と ND40r_v2 では SR-IOV が有効になります。一方、NC24r と NC24r_v2 では SR-IOV は有効になりません。 2 つに分かれることの詳細は[こちら](../../sizes-hpc.md#rdma-capable-instances)にあります。
InfiniBand (IB) は、OFED ドライバーがある SR-IOV 対応 VM サイズ上で構成できます。一方、SR-IOV ではない VM サイズには ND ドライバーが必要です。 この IB サポートは [CentOS-HPC VMI](configure.md) で適切に利用できます。 Ubuntu の場合、[こちらのドキュメント](enable-infiniband.md#vm-images-with-infiniband-drivers)で説明されているように OFED ドライバーと ND ドライバーの両方をインストールする方法については[こちらの指示](https://techcommunity.microsoft.com/t5/azure-compute/configuring-infiniband-for-ubuntu-hpc-and-gpu-vms/ba-p/1221351)をご覧ください。

## <a name="duplicate-mac-with-cloud-init-with-ubuntu-on-h-series-and-n-series-vms"></a>H シリーズおよび N シリーズ VM 上の Ubuntu で cloud-init により発生する重複した MAC

Ubuntu VM イメージ上の cloud-init には、IB インターフェイスを有効にしようとするときに発生する既知の問題があります。 これは、VM の再起動時に、または一般化した後で VM イメージを作成しようとするときに発生する可能性があります。 VM ブート ログには、"ネットワーク サービスを開始しています...RuntimeError: 重複した mac が見つかりました。 'eth1' と 'ib0' の両方に mac があります" というエラーが表示されます。

この "Ubuntu で cloud-init により発生する重複した MAC" は既知の問題です。 回避策は次のとおりです。
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

## <a name="dram-on-hb-series"></a>HB シリーズの DRAM

現時点では、HB シリーズの VM はゲスト VM に 228 GB の RAM しか公開できません。 これは、ゲスト VM 用に予約されている AMD CCX (NUMA ドメイン) のローカル DRAM にページが割り当てられないようにするための Azure ハイパーバイザーの既知の制限によるものです。

## <a name="accelerated-networking"></a>高速ネットワーク

現時点では、IB が有効な HPC および GPU VM の Azure 高速ネットワークは有効にされていません。 この機能がサポートされたら、お客様にお知らせします。

## <a name="qp0-access-restriction"></a>qp0 アクセス制限

セキュリティの脆弱性の原因となる可能性がある低レベルのハードウェア アクセスを防ぐために、Queue Pair 0 はゲスト VM からアクセスできません。 これは、通常、ConnectX-5 NIC の管理や、ibdiagnet などの InfiniBand 診断の実行に関連するアクションのみに影響します。エンド ユーザー アプリケーション自体には影響しません。

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
- [Azure Compute Tech Community のブログ](https://techcommunity.microsoft.com/t5/azure-compute/bg-p/AzureCompute)で、最新の発表および HPC の例と結果について参照します。
- アーキテクチャの面から見た HPC ワークロードの実行の概要については、「[Azure でのハイ パフォーマンス コンピューティング (HPC)](/azure/architecture/topics/high-performance-computing/)」を参照してください。
