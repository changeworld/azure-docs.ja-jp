---
title: InfiniBand 対応の H シリーズおよび N シリーズの Microsoft Azure Virtual Machines の構成と最適化
description: InfiniBand 対応の H シリーズおよび N シリーズの HPC 用 VM の構成と最適化について説明します。
author: vermagit
ms.service: virtual-machines
ms.subservice: hpc
ms.topic: article
ms.date: 03/18/2021
ms.author: amverma
ms.reviewer: cynthn
ms.openlocfilehash: 0c6f5dc55f7406aba7d6e3dc1a278b57fe4ec9ba
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "104721279"
---
# <a name="configure-and-optimize-vms"></a>VM の構成と最適化

この記事では、InfiniBand 対応の [H シリーズ](../../sizes-hpc.md) と [N シリーズ](../../sizes-gpu.md) VM を HPC 用に構成および最適化するためのガイダンスを紹介します。

## <a name="vm-images"></a>VM イメージ
InfiniBand 対応の VM では、RDMA を有効にするために適切なドライバーが必要です。
- Marketplace の [CentOS-HPC VM イメージ](#centos-hpc-vm-images) は、適切な IB ドライバーを使用して事前構成されており、最も簡単に始めることができます。
- [Ubuntu VM イメージ](#ubuntu-vm-images)は、適正な IB ドライバーを使用して構成できます。 適切なドライバーと構成を使用して[カスタム VM イメージ](../../linux/tutorial-custom-images.md)を作成し、それらを繰り返し再使用することをお勧めします。

GPU 対応の [N シリーズ](../../sizes-gpu.md) VM では、[VM 拡張機能](../../extensions/hpccompute-gpu-linux.md)を介して、または[手動](../../linux/n-series-driver-setup.md)で追加できる適切な GPU ドライバーがさらに必要です。 Marketplace の一部の VM イメージには、Nvidia からの VM イメージなど、Nvidia GPU ドライバーがプレインストールされています。

### <a name="centos-hpc-vm-images"></a>CentOS-HPC VM イメージ

#### <a name="sr-iov-enabled-vms"></a>SR-IOV 対応の VM
SR-IOV 対応の [RDMA 対応 VM](../../sizes-hpc.md#rdma-capable-instances) の場合、Marketplace バージョン 7.6 以降の [CentOS-HPC VM イメージ](https://azuremarketplace.microsoft.com/marketplace/apps/openlogic.centos-hpc?tab=Overview)が適しています。 これらの VM イメージは最適化され、RDMA 用の OFED ドライバー、一般的に使用されているさまざまな MPI ライブラリ、科学コンピューティング パッケージが事前に読み込まれているため、最も簡単に始められます。
- CentOS-HPC バージョン7.6 以降の VM イメージに含まれる内容の詳細については、[テクニカルコミュニティの記事](https://techcommunity.microsoft.com/t5/Azure-Compute/CentOS-HPC-VM-Image-for-SR-IOV-enabled-Azure-HPC-VMs/ba-p/665557)をご覧ください。
- CentOS-HPC バージョン 7.6 以降の VM イメージを基本の CentOS Marketplace イメージから作成するときに使用されるスクリプトについては、[azhpc-images リポジトリ](https://github.com/Azure/azhpc-images/tree/master/centos)を参照してください。
  
> [!NOTE] 
> 最新の Azure HPC マーケットプレースのイメージには、Mellanox OFED 5.1 以降が含まれています。これは ConnectX3-Pro InfiniBand カードをサポートしていません。 FDR InfiniBand (NCv3 以前など) を備えた SR-IOV 対応 N シリーズ VM サイズには、次の CentOS-HPC VM イメージ以前のバージョンを Marketplace から使用できます。
>- OpenLogic:CentOS-HPC:7.6:7.6.2020062900
>- OpenLogic:CentOS-HPC:7_6gen2:7.6.2020062901
>- OpenLogic:CentOS-HPC:7.7:7.7.2020062600
>- OpenLogic:CentOS-HPC:7_7-gen2:7.7.2020062601
>- OpenLogic:CentOS-HPC:8_1:8.1.2020062400
>- OpenLogic:CentOS-HPC:8_1-gen2:8.1.2020062401

#### <a name="non-sr-iov-enabled-vms"></a>SR-IOV 非対応の VM
SR-IOV 非対応の [RDMA 対応 VM](../../sizes-hpc.md#rdma-capable-instances)、CentOS-HPC バージョン 6.5 以降の場合、Marketplace の最大 7.4 が適しています。 例えば、[H16 シリーズ VM](../../h-series.md) の場合、バージョン 7.1 から 7.4 をお勧めします。 これらの VM イメージには、RDMA および Intel MPI バージョン 5.1 用の Network Direct ドライバーが事前に読み込まれています。

> [!NOTE]
> SR-IOV 非対応 VM 用のこれらの CentOS ベースの HPC イメージでは、カーネルの更新は **yum** 構成ファイルで無効になっています。 これは、NetworkDirect Linux RDMA ドライバーが RPM パッケージとして配布されており、カーネルが更新された場合にドライバーの更新プログラムが機能しない可能性があるためです。

### <a name="rhelcentos-vm-images"></a>RHEL または CentOS VM イメージ
Marketplace 上の RHEL または CentOS ベースの非 HPC VM イメージは、SR-IOV 対応の [RDMA 対応 VM](../../sizes-hpc.md#rdma-capable-instances) で使用するように構成できます。 VM での [InfiniBand の有効化](enable-infiniband.md)と [MPI の設定](setup-mpi.md)の詳細を参照してください。
- CentOS-HPC バージョン 7.6 以降の VM イメージを基本の CentOS Marketplace イメージから作成するスクリプトは、 [azhpc-images リポジトリ](https://github.com/Azure/azhpc-images/tree/master/centos)からも使用できます。
  
> [!NOTE]
> Mellanox OFED 5.1 以降は、FDR InfiniBand (NCv3 など) を使用する SR-IOV 対応 N シリーズ VM サイズ上の ConnectX3-Pro InfiniBand カードをサポートしていません。 ConnectX3-Pro カードが搭載された N シリーズ VM 上では、LTS Mellanox OFED バージョン 4.9-0.1.7.0 以前を使用してください。 詳細については、[こちら](https://www.mellanox.com/products/infiniband-drivers/linux/mlnx_ofed)を参照してください。

### <a name="ubuntu-vm-images"></a>Ubuntu VM イメージ
Marketplace の Ubuntu Server 16.04 LTS、18.04 LTS、および 20.04 LTS VM のイメージは、SR-IOV および非 SR-IOV の [RDMA 対応 VM](../../sizes-hpc.md#rdma-capable-instances) の両方でサポートされています。 VM での [InfiniBand の有効化](enable-infiniband.md)と [MPI の設定](setup-mpi.md)の詳細を参照してください。
- Ubuntu VM イメージで InfiniBand を有効にする手順については、 [「テクニカルコミュニティ](https://techcommunity.microsoft.com/t5/azure-compute/configuring-infiniband-for-ubuntu-hpc-and-gpu-vms/ba-p/1221351)」記事を参照してください。
- 基本の Ubuntu Marketplace イメージから Ubuntu 18.04 および 20.04 LTS ベースの HPC VM イメージを作成するために使用されるスクリプトは、 [azhpc-images リポジトリ](https://github.com/Azure/azhpc-images/tree/master/ubuntu)にあります。

### <a name="suse-linux-enterprise-server-vm-images"></a>SUSE Linux Enterprise Server VM イメージ
Marketplace の SLES 12 SP3 for HPC、SLES 12 SP3 for HPC (Premium)、SLES 12 SP1 for HPC、SLES 12 SP1 for HPC (Premium)、SLES 12 SP4、SLES 15 VM の各イメージがサポートされています。 これらの VM イメージには、RDMA および Intel MPI バージョン 5.1 用の Network Direct ドライバーが事前に読み込まれています。 詳細については、VM での [MPI の設定](setup-mpi.md)に関するページを参照してください。

## <a name="optimize-vms"></a>VM を最適化する

VM のパフォーマンスを向上させるためのオプションの最適化設定を次に示します。

### <a name="update-lis"></a>LIS を更新する

機能またはパフォーマンスに必要な場合、サポートされている OS ディストリビューションに [Linux Integration Services (LIS) ドライバー](../../linux/endorsed-distros.md)をインストールするか更新することができます。特に、カスタム イメージまたは以前の OS バージョン (CentOS、RHEL 6.x または以前のバージョンの 7.x など) を使用して展開します。

```bash
wget https://aka.ms/lis
tar xzf lis
pushd LISISO
./upgrade.sh
```

### <a name="reclaim-memory"></a>メモリを再利用する

リモート メモリ アクセスを回避するためにメモリを自動的に再利用することでパフォーマンスを向上させます。

```bash
echo 1 >/proc/sys/vm/zone_reclaim_mode
```

VM の再起動後もこれを永続化するには、次の手順を実行します。

```bash
echo "vm.zone_reclaim_mode = 1" >> /etc/sysctl.conf sysctl -p
```

### <a name="disable-firewall-and-selinux"></a>ファイアウォールと SELinux を無効にする

```bash
systemctl stop iptables.service
systemctl disable iptables.service
systemctl mask firewalld
systemctl stop firewalld.service
systemctl disable firewalld.service
iptables -nL
sed -i -e's/SELINUX=enforcing/SELINUX=disabled/g' /etc/selinux/config
```

### <a name="disable-cpupower"></a>cpupower を無効にする

```bash
service cpupower status
if enabled, disable it:
service cpupower stop
sudo systemctl disable cpupower
```

### <a name="configure-walinuxagent"></a>WALinuxAgent を構成する

```bash
sed -i -e 's/# OS.EnableRDMA=y/OS.EnableRDMA=y/g' /etc/waagent.conf
```
必要に応じて、HPC ワークロードに VM リソースを最大限に使用できるように、ジョブ前の手順として WALinuxAgent を無効にし、ジョブ後に有効に戻します。


## <a name="next-steps"></a>次の手順

- InfiniBand 対応の [H シリーズ](../../sizes-hpc.md)および [N シリーズ](../../sizes-gpu.md) VM 上で [InfiniBand を有効にする](enable-infiniband.md)方法の詳細を確認します。
- VM で[サポートされているさまざまな MPI ライブラリ](setup-mpi.md)のインストールと最適な構成の詳細について確認します。
- [HBv3 シリーズの概要](hbv3-series-overview.md)および [HC シリーズの概要](hc-series-overview.md)に関する記事を確認します。
- [Azure Compute Tech Community のブログ](https://techcommunity.microsoft.com/t5/azure-compute/bg-p/AzureCompute)で、最新の発表、HPC ワークロードの例、およびパフォーマンスの結果について参照します。
- HPC ワークロードの実行をアーキテクチャの面から見た概要については、「[Azure でのハイ パフォーマンス コンピューティング (HPC)](/azure/architecture/topics/high-performance-computing/)」をご覧ください。
