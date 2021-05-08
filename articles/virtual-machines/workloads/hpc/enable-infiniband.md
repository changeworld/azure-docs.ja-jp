---
title: HPC VM で InifinBand を有効にする - Azure Virtual Machines | Microsoft Docs
description: Azure HPC VM で InfiniBand を有効にする方法について説明します。
author: vermagit
ms.service: virtual-machines
ms.subservice: hpc
ms.topic: article
ms.date: 03/18/2021
ms.author: amverma
ms.reviewer: cynthn
ms.openlocfilehash: 089976f2009e006f53dd2a77f09f57d5090429b7
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "104721237"
---
# <a name="enable-infiniband"></a>InfiniBand の有効化

[RDMA 対応](../../sizes-hpc.md#rdma-capable-instances)の [H シリーズ](../../sizes-hpc.md)および [N シリーズ](../../sizes-gpu.md) VM の通信は、低待機時間で高帯域幅の InfiniBand ネットワークを介して行われます。 そのような相互接続を介した RDMA 機能は、分散ノードの HPC および AI ワークロードのスケーラビリティとパフォーマンスを向上させるために重要です。 InfiniBand 対応の H シリーズおよび N シリーズの VM は、最適で一貫した RDMA のパフォーマンスのため、小直径設計の非ブロッキング ファット ツリーで接続されます。

対応する VM サイズで InfiniBand を有効にするには、さまざまな方法があります。

## <a name="vm-images-with-infiniband-drivers"></a>InfiniBand ドライバーが含まれる VM イメージ
Marketplace でサポートされている VM イメージの一覧については、「[VM イメージ](configure.md#vm-images)」を参照してください。これらは、InfiniBand ドライバーとともにあらかじめ読み込まれています (SR-IOV または非 SR-IOV の VM の場合)。または、[RDMA 対応 VM](../../sizes-hpc.md#rdma-capable-instances) に適切なドライバーを使用して構成できます。
- Marketplace の [CentOS-HPC](configure.md#centos-hpc-vm-images) VM イメージが、最も簡単な作業の開始方法です。
- [Ubuntu](configure.md#ubuntu-vm-images) VM イメージは、適正な IB ドライバーを使用して構成できます。

## <a name="infiniband-driver-vm-extensions"></a>InfiniBand ドライバー VM 拡張機能
Linux の場合、SR-IOV が有効になっている H シリーズと N シリーズの VM で、[InfiniBandDriverLinux VM 拡張機能](../../extensions/hpc-compute-infiniband-linux.md) を使用して、Mellanox OFED ドライバーをインストールし、InfiniBand を有効にすることができます。

Windows では、[InfiniBandDriverWindows VM 拡張機能](../../extensions/hpc-compute-infiniband-windows.md)は、RDMA 接続用に Windows Network Direct ドライバーを (SR-IOV に非対応の VM 上に) インストールするか、または Mellanox OFED ドライバーを (SR-IOV VM 上に) インストールします。 A8 インスタンスと A9 インスタンスの特定のデプロイでは、HpcVmDrivers 拡張機能は自動的に追加されます。 HpcVmDrivers VM 拡張機能は廃止される予定であり、更新されないことに注意してください。

VM に VM 拡張機能を追加するには、[Azure PowerShell](/powershell/azure/) コマンドレットを使用できます。 詳しくは、[仮想マシン拡張機能とその機能](../../extensions/overview.md)に関する記事をご覧ください。 [クラシック デプロイ モデル](/previous-versions/azure/virtual-machines/windows/classic/agents-and-extensions-classic)にデプロイされている VM にも拡張機能を使用できます。

## <a name="manual-installation"></a>手動のインストール
[Mellanox OpenFabrics ドライバー (OFED)](https://www.mellanox.com/products/InfiniBand-VPI-Software) は、[SR-IOV が有効になっている](../../sizes-hpc.md#rdma-capable-instances) [H シリーズ](../../sizes-hpc.md)と [N シリーズ](../../sizes-gpu.md)の VM に手動でインストールできます。

### <a name="linux"></a>Linux
[Linux 用 OFED ドライバー](https://www.mellanox.com/products/infiniband-drivers/linux/mlnx_ofed)は、次の例を使用してインストールできます。 これらの例は RHEL/CentOS に対するものですが、手順は一般的で、Ubuntu (16.04、18.04、19.04、20.04)、SLES (12 SP4、15) などの互換性のある Linux オペレーティング システムで使用できます。 他のディストリビューションのその他の例については、[azhpc-images リポジトリ](https://github.com/Azure/azhpc-images/blob/master/ubuntu/ubuntu-18.x/ubuntu-18.04-hpc/install_mellanoxofed.sh)を参照してください。 受信トレイドライバーも同様に機能しますが、OFED ドライバーではさらに多くの機能が提供されます。

```bash
MLNX_OFED_DOWNLOAD_URL=http://content.mellanox.com/ofed/MLNX_OFED-5.0-2.1.8.0/MLNX_OFED_LINUX-5.0-2.1.8.0-rhel7.7-x86_64.tgz
# Optionally verify checksum
wget --retry-connrefused --tries=3 --waitretry=5 $MLNX_OFED_DOWNLOAD_URL
tar zxvf MLNX_OFED_LINUX-5.0-2.1.8.0-rhel7.7-x86_64.tgz

KERNEL=( $(rpm -q kernel | sed 's/kernel\-//g') )
KERNEL=${KERNEL[-1]}
# Uncomment the lines below if you are running this on a VM
#RELEASE=( $(cat /etc/centos-release | awk '{print $4}') )
#yum -y install http://olcentgbl.trafficmanager.net/centos/${RELEASE}/updates/x86_64/kernel-devel-${KERNEL}.rpm
yum install -y kernel-devel-${KERNEL}
./MLNX_OFED_LINUX-5.0-2.1.8.0-rhel7.7-x86_64/mlnxofedinstall --kernel $KERNEL --kernel-sources /usr/src/kernels/${KERNEL} --add-kernel-support --skip-repo
```

### <a name="windows"></a>Windows
Windows の場合は、[Windows 用の Mellanox OFED ドライバー](https://www.mellanox.com/products/adapter-software/ethernet/windows/winof-2)をダウンロードしてインストールします。

## <a name="enable-ip-over-infiniband-ib"></a>IP over InfiniBand (IB) を有効にする
MPI ジョブを実行する予定がある場合は、通常、IPoIB は必要ありません。 MPI ライブラリでは、IB 通信に動詞インターフェイスが使用されます (MPI ライブラリの TCP/IP チャネルを明示的に使用しない場合)。 ただし、通信に TCP/IP を使用するアプリがあり、IB 経由で実行したい場合は、IB インターフェイスで IPoIB を使用できます。 次のコマンド (RHEL/CentOS 用) を実行して、IP over InfiniBand を有効にします。

```bash
sudo sed -i -e 's/# OS.EnableRDMA=n/OS.EnableRDMA=y/g' /etc/waagent.conf
sudo systemctl restart waagent
```

## <a name="next-steps"></a>次のステップ

- VM で[サポートされているさまざまな MPI ライブラリ](setup-mpi.md)のインストールと最適な構成の詳細について確認します。
- [HBv3 シリーズの概要](hbv3-series-overview.md)および [HC シリーズの概要](hc-series-overview.md)に関する記事を確認します。
- [Azure Compute Tech Community のブログ](https://techcommunity.microsoft.com/t5/azure-compute/bg-p/AzureCompute)で、最新の発表、HPC ワークロードの例、およびパフォーマンスの結果について参照します。
- HPC ワークロードの実行をアーキテクチャの面から見た概要については、「[Azure でのハイ パフォーマンス コンピューティング (HPC)](/azure/architecture/topics/high-performance-computing/)」をご覧ください。
