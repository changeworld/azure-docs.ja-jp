---
title: HPC のメッセージ パッシング インターフェイス (MPI) の設定 - Azure Virtual Machines | Microsoft Docs
description: Azure で HPC の MPI を設定する方法について説明します。
author: vermagit
ms.service: virtual-machines
ms.subservice: hpc
ms.topic: article
ms.date: 03/18/2021
ms.author: amverma
ms.reviewer: cynthn
ms.openlocfilehash: 66de34c43ab1b3a6b4245f77196793bf9ad8530c
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "105606642"
---
# <a name="set-up-message-passing-interface-for-hpc"></a>HPC の Message Passing Interface を設定する

[メッセージ パッシング インターフェイス (MPI)](https://en.wikipedia.org/wiki/Message_Passing_Interface) は、分散型メモリ並列化についてのオープン ライブラリであり、事実上の標準です。 多くの HPC ワークロードにわたってよく使用されています。 [RDMA 対応](../../sizes-hpc.md#rdma-capable-instances)の [H シリーズ](../../sizes-hpc.md)および [N シリーズ](../../sizes-gpu.md) VM 上の HPC ワークロードは、MPI を使用し、低待機時間で高帯域幅の InfiniBand ネットワークを介して通信することができます。
- Azure の SR-IOV 対応 VM サイズでは、ほぼすべてのフレーバーの MPI を Mellanox OFED と一緒に使用できます。
- SR-IOV に対応していない VM の場合、サポートされている MPI 実装では、VM 間の通信に Microsoft Network Direct (ND) インターフェイスが使用されます。 そのため、Microsoft MPI (MS MPI) 2012 R2 以降と Intel MPI 5.x バージョンのみがサポートされています。 Intel MPI ランタイム ライブラリの以降のバージョン (2017、2018) は、Azure RDMA ドライバーと互換性がある場合とない場合があります。

SR-IOV 対応の [RDMA 対応 VM](../../sizes-hpc.md#rdma-capable-instances) の場合、バージョン 7.6 以降の [CentOS-HPC VM イメージ](configure.md#centos-hpc-vm-images)が適しています。 これらの VM イメージは最適化され、RDMA 用の OFED ドライバー、一般的に使用されているさまざまな MPI ライブラリ、科学コンピューティング パッケージが事前に読み込まれているため、最も簡単に始められます。

これらの例は RHEL/CentOS に向けたものですが、手順は一般的であり、Ubuntu (16.04、18.04、19.04、20.04) や SLES (12 SP4、15) などの互換性のあるすべての Linux オペレーティング システムで使用できます。 その他の MPI 実装を別のディストリビューションで設定するための例については、[azhpc-images リポジトリ](https://github.com/Azure/azhpc-images/blob/master/ubuntu/ubuntu-18.x/ubuntu-18.04-hpc/install_mpis.sh)により多く用意されています。

> [!NOTE]
> 特定の MPI ライブラリ (プラットフォーム MPI など) を使用して SR-IOV が有効になっている VM で MPI ジョブを実行するには、分離とセキュリティを実現するために、テナント全体にパーティションキー (p キー) を設定する必要がある場合があります。 p キーの値を決定し、MPI ライブラリでその値を MPI ジョブのために正しく設定することの詳細については、「[パーティション キーを検出する](#discover-partition-keys)」セクションの手順に従ってください。

> [!NOTE]
> 次はコード スニペットの例です。 パッケージの最新の安定バージョンを使用するか、 [azhpc イメージ リポジトリ](https://github.com/Azure/azhpc-images/blob/master/ubuntu/ubuntu-18.x/ubuntu-18.04-hpc/install_mpis.sh)を参照することをお勧めします。

## <a name="ucx"></a>UCX

[Unified Communication X (UCX)](https://github.com/openucx/ucx) は、HPC 用の通信 API フレームワークです。 InfiniBand 経由の MPI 通信のために最適化されており、OpenMPI や MPICH などの多くの MPI 実装で動作します。

```bash
wget https://github.com/openucx/ucx/releases/download/v1.4.0/ucx-1.4.0.tar.gz
tar -xvf ucx-1.4.0.tar.gz
cd ucx-1.4.0
./configure --prefix=<ucx-install-path>
make -j 8 && make install
```

> [!NOTE]
> UCX の最近のビルドでは、複数の NIC インターフェイスが存在する場合に適切な InfiniBand インターフェイスを選択するという [問題](https://github.com/openucx/ucx/pull/5965) が修正されています。 VM で高速ネットワークが有効になっている場合に InfiniBand で MPI を実行する方法の詳細については、[こちら](hb-hc-known-issues.md#accelerated-networking-on-hb-hc-hbv2-and-ndv2) を参照してください。

## <a name="hpc-x"></a>HPC-X

[HPC-x ソフトウェア ツールキット](https://www.mellanox.com/products/hpc-x-toolkit)には UCX および HCOLL が含まれており、UCX に対して構築できます。

```bash
HPCX_VERSION="v2.6.0"
HPCX_DOWNLOAD_URL=https://azhpcstor.blob.core.windows.net/azhpc-images-store/hpcx-v2.6.0-gcc-MLNX_OFED_LINUX-5.0-1.0.0.0-redhat7.7-x86_64.tbz
get --retry-connrefused --tries=3 --waitretry=5 $HPCX_DOWNLOAD_URL
tar -xvf hpcx-${HPCX_VERSION}-gcc-MLNX_OFED_LINUX-5.0-1.0.0.0-redhat7.7-x86_64.tbz
mv hpcx-${HPCX_VERSION}-gcc-MLNX_OFED_LINUX-5.0-1.0.0.0-redhat7.7-x86_64 ${INSTALL_PREFIX}
HPCX_PATH=${INSTALL_PREFIX}/hpcx-${HPCX_VERSION}-gcc-MLNX_OFED_LINUX-5.0-1.0.0.0-redhat7.7-x86_64
```

HPC-X を実行します

```bash
${HPCX_PATH}mpirun -np 2 --map-by ppr:2:node -x UCX_TLS=rc ${HPCX_PATH}/ompi/tests/osu-micro-benchmarks-5.3.2/osu_latency
```

### <a name="optimizing-mpi-collectives"></a>MPI コレクティブの最適化

MPI コレクティブ通信プリミティブでは、グループ通信操作を実装するための柔軟で移植可能な方法が提供されます。 これらは、さまざまな科学的並列アプリケーションで広く使用されており、アプリケーションのパフォーマンス全体に大きな影響を与えます。 コレクティブ通信用の HPC-X および HCOLL ライブラリを使用したコレクティブ通信のパフォーマンスを最適化する構成パラメーターについて詳しくは、[TechCommunity の記事](https://techcommunity.microsoft.com/t5/azure-compute/optimizing-mpi-collective-communication-using-hpc-x-on-azurehpc/ba-p/1356740)を参照してください。

> [!NOTE] 
> HPC-X 2.7.4 + では、MOFED の UCX バージョン と HPC-X のものが異なる場合は、明示的に LD_LIBRARY_PATH を渡す必要がある場合があります。

## <a name="openmpi"></a>OpenMPI

前述したように UCX をインストールします。 HCOLL は [HPC-X ソフトウェア ツールキット](https://www.mellanox.com/products/hpc-x-toolkit)の一部であり、特別なインストールは不要です。

リポジトリで入手可能なパッケージから OpenMPI をインストールできます。

```bash
sudo yum install –y openmpi
```

UCX で OpenMPI の最新の安定したリリースをビルドすることをお勧めします。

```bash
OMPI_VERSION="4.0.3"
OMPI_DOWNLOAD_URL=https://download.open-mpi.org/release/open-mpi/v4.0/openmpi-${OMPI_VERSION}.tar.gz
wget --retry-connrefused --tries=3 --waitretry=5 $OMPI_DOWNLOAD_URL
tar -xvf openmpi-${OMPI_VERSION}.tar.gz
cd openmpi-${OMPI_VERSION}
./configure --prefix=${INSTALL_PREFIX}/openmpi-${OMPI_VERSION} --with-ucx=${UCX_PATH} --with-hcoll=${HCOLL_PATH} --enable-mpirun-prefix-by-default --with-platform=contrib/platform/mellanox/optimized && make -j$(nproc) && make install
```

最適なパフォーマンスを得るには、`ucx`と `hcoll` を使用して OpenMPI を実行し ます。

```bash
${INSTALL_PREFIX}/bin/mpirun -np 2 --map-by node --hostfile ~/hostfile -mca pml ucx --mca btl ^vader,tcp,openib -x UCX_NET_DEVICES=mlx5_0:1  -x UCX_IB_PKEY=0x0003  ./osu_latency
```

前述のように、パーティション キーを確認します。

## <a name="intel-mpi"></a>Intel MPI

選択したバージョンの [Intel MPI](https://software.intel.com/mpi-library/choose-download) をダウンロードします。 バージョンに応じて I_MPI_FABRICS 環境変数を変更します。
- Intel MPI 2019 および 2021: `I_MPI_FABRICS=shm:ofi`、`I_MPI_OFI_PROVIDER=mlx` を使用してください。 `mlx`プロバイダーは UCX を使用します。 Verb の使用が不安定で、パフォーマンスが低下しています。 詳しくは、[TechCommunity 記事](https://techcommunity.microsoft.com/t5/azure-compute/intelmpi-2019-on-azure-hpc-clusters/ba-p/1403149)をご覧ください。
- Intel MPI 2018: `I_MPI_FABRICS=shm:ofa` を使用
- Intel MPI 2016: `I_MPI_DAPL_PROVIDER=ofa-v2-ib0` を使用

### <a name="non-sr-iov-vms"></a>SR-IOV 非対応 VM
SR-IOV 非対応 VM の場合、5.x ランタイムの[無料評価版](https://registrationcenter.intel.com/en/forms/?productid=1740)をダウンロードする例は次のとおりです。
```bash
wget http://registrationcenter-download.intel.com/akdlm/irc_nas/tec/9278/l_mpi_p_5.1.3.223.tgz
```
インストール手順については、[Intel MPI ライブラリのインストール ガイド](https://registrationcenter-download.intel.com/akdlm/irc_nas/1718/INSTALL.html?lang=en&fileExt=.html)をご覧ください。
必要に応じて (最新バージョンの Intel MPI に必要な) non-root non-debugger プロセスに対して ptrace を有効にすることをお勧めします。
```bash
echo 0 | sudo tee /proc/sys/kernel/yama/ptrace_scope
```

### <a name="suse-linux"></a>SUSE Linux
SUSE Linux Enterprise Server の VM イメージ バージョンが SLES 12 SP3 for HPC、SLES 12 SP3 for HPC (Premium)、SLES 12 SP1 for HPC、SLES 12 SP1 for HPC (Premium)、SLES 12 SP4、SLES 15 の場合、RDMA ドライバーがインストールされ、 VM には Intel MPI パッケージが配布されます。 次のコマンドを実行して Intel MPI をインストールします。
```bash
sudo rpm -v -i --nodeps /opt/intelMPI/intel_mpi_packages/*.rpm
```

## <a name="mvapich2"></a>MVAPICH2

MVAPICH2 をビルドします。

```bash
wget http://mvapich.cse.ohio-state.edu/download/mvapich/mv2/mvapich2-2.3.tar.gz
tar -xv mvapich2-2.3.tar.gz
cd mvapich2-2.3
./configure --prefix=${INSTALL_PREFIX}
make -j 8 && make install
```

MVAPICH2 を実行します。

```bash
${INSTALL_PREFIX}/bin/mpirun_rsh -np 2 -hostfile ~/hostfile MV2_CPU_MAPPING=48 ./osu_latency
```

## <a name="platform-mpi"></a>プラットフォーム MPI

プラットフォーム MPI のコミュニティ エディションに必要なパッケージをインストールします。

```bash
sudo yum install libstdc++.i686
sudo yum install glibc.i686
Download platform MPI at https://www.ibm.com/developerworks/downloads/im/mpi/index.html 
sudo ./platform_mpi-09.01.04.03r-ce.bin
```

インストール プロセスに従います。

次のコマンドは、CentOS-HPC 7.6、7.8、および 8.1 の VM イメージを使用して、HBv3 VM 上でプラットフォーム MPI を使用して MPI pingpong と allreduce を実行する例です。

```bash
/opt/ibm/platform_mpi/bin/mpirun -hostlist 10.0.0.8:1,10.0.0.9:1 -np 2 -e MPI_IB_PKEY=0x800a  -ibv  /home/jijos/mpi-benchmarks/IMB-MPI1 pingpong
/opt/ibm/platform_mpi/bin/mpirun -hostlist 10.0.0.8:120,10.0.0.9:120 -np 240 -e MPI_IB_PKEY=0x800a  -ibv  /home/jijos/mpi-benchmarks/IMB-MPI1 allreduce -npmin 240
```


## <a name="mpich"></a>MPICH

前述したように UCX をインストールします。 MPICH をビルドします。

```bash
wget https://www.mpich.org/static/downloads/3.3/mpich-3.3.tar.gz
tar -xvf mpich-3.3.tar.gz
cd mpich-3.3
./configure --with-ucx=${UCX_PATH} --prefix=${INSTALL_PREFIX} --with-device=ch4:ucx
make -j 8 && make install
```

MPICH を実行します。

```bash
${INSTALL_PREFIX}/bin/mpiexec -n 2 -hostfile ~/hostfile -env UCX_IB_PKEY=0x0003 -bind-to hwthread ./osu_latency
```

前述のように、パーティション キーを確認します。

## <a name="osu-mpi-benchmarks"></a>OSU MPI ベンチマーク

[OSU MPI ベンチマーク](http://mvapich.cse.ohio-state.edu/benchmarks/)をダウンロードし、解凍します。

```bash
wget http://mvapich.cse.ohio-state.edu/download/mvapich/osu-micro-benchmarks-5.5.tar.gz
tar –xvf osu-micro-benchmarks-5.5.tar.gz
cd osu-micro-benchmarks-5.5
```

特定の MPI ライブラリを使用してベンチマークをビルドします:

```bash
CC=<mpi-install-path/bin/mpicc>CXX=<mpi-install-path/bin/mpicxx> ./configure 
make
```

MPI ベンチマークは `mpi/` フォルダー下にあります。


## <a name="discover-partition-keys"></a>パーティション キーを検出する

同じテナント (可用性セットまたは仮想マシン スケール セット) 内の他の VM と通信するために、パーティション キー (p キー) を検出します。

```bash
/sys/class/infiniband/mlx5_0/ports/1/pkeys/0
/sys/class/infiniband/mlx5_0/ports/1/pkeys/1
```

2 つのうち大きいほうは、MPI で使用する必要があるテナント キーです。 例:p キーが次の場合、MPI では 0x800b を使用する必要があります。

```bash
cat /sys/class/infiniband/mlx5_0/ports/1/pkeys/0
0x800b
cat /sys/class/infiniband/mlx5_0/ports/1/pkeys/1
0x7fff
```

既定 (0x7fff) のパーティション キー以外のパーティションを使用します。 UCX では、p キーの MSB をクリアする必要があります。 たとえば、0x800b の場合、UCX_IB_PKEY を 0x000b と設定します。

テナント (可用性セットまたは仮想マシン スケール セット) が存在する限り、p キーは同じままであることにも注意してください。 これは、ノードが追加または削除された場合でも当てはまります。 新しいテナントには、別の p キーが割り当てられます。


## <a name="set-up-user-limits-for-mpi"></a>MPI のユーザー制限を設定する

MPI のユーザー制限を設定します。

```bash
cat << EOF | sudo tee -a /etc/security/limits.conf
*               hard    memlock         unlimited
*               soft    memlock         unlimited
*               hard    nofile          65535
*               soft    nofile          65535
EOF
```

## <a name="set-up-ssh-keys-for-mpi"></a>MPI の SSH キーを設定する

SSH キーが必要な MPI タイプの場合、設定します。

```bash
ssh-keygen -f /home/$USER/.ssh/id_rsa -t rsa -N ''
cat << EOF > /home/$USER/.ssh/config
Host *
    StrictHostKeyChecking no
EOF
cat /home/$USER/.ssh/id_rsa.pub >> /home/$USER/.ssh/authorized_keys
chmod 600 /home/$USER/.ssh/authorized_keys
chmod 644 /home/$USER/.ssh/config
```

上記の構文は共有ホーム ディレクトリを想定しており、それ以外の場合は .ssh ディレクトリを各ノードにコピーする必要があります。

## <a name="next-steps"></a>次の手順

- [InfiniBand 対応](../../sizes-hpc.md#rdma-capable-instances)の [H シリーズ](../../sizes-hpc.md)および [N シリーズ](../../sizes-gpu.md)の VM について学習します
- [HBv3 シリーズの概要](hbv3-series-overview.md)および [HC シリーズの概要](hc-series-overview.md)に関する記事を確認します。
- [Azure Compute Tech Community のブログ](https://techcommunity.microsoft.com/t5/azure-compute/bg-p/AzureCompute)で、最新の発表、HPC ワークロードの例、およびパフォーマンスの結果について参照します。
- HPC ワークロードの実行をアーキテクチャの面から見た概要については、「[Azure でのハイ パフォーマンス コンピューティング (HPC)](/azure/architecture/topics/high-performance-computing/)」をご覧ください。
