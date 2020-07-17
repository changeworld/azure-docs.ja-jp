---
title: SR-IOV を使用して InifinBand を有効にする - Azure Virtual Machines | Microsoft Docs
description: SR-IOV を使用して InfiniBand を有効にする方法について説明します。
services: virtual-machines
documentationcenter: ''
author: vermagit
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.topic: article
ms.date: 10/17/2019
ms.author: amverma
ms.openlocfilehash: 7f7907482da886d9da17ef1e7844b205f3e4b906
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/27/2020
ms.locfileid: "74196764"
---
# <a name="enable-infiniband-with-sr-iov"></a>SR-IOV を使用して InfiniBand を有効にする

Azure NC、ND、および H シリーズの VM はすべて、専用の InfiniBand ネットワークを利用しています。 RDMA 対応のすべてのサイズは、Intel MPI を使用してそのネットワークを活用できます。 一部の VM シリーズでは、SR-IOV によるすべての MPI 実装と RDMA 動詞のサポートが拡張されています。 RDMA 対応の VM には、[GPU 最適化済み](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-gpu)の[ハイパフォーマンス コンピューティング (HPC)](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-hpc) VM が含まれます。

## <a name="choose-your-installation-path"></a>インストール パスの選択

操作を開始するための簡単なオプションは、InfiniBand 用に事前に構成されたプラットフォーム イメージを使用する方法です。

- **HPC IaaS VM** – HPC 向け IaaS VM の使用を開始するための最も簡単なソリューションは、InfiniBand で既に構成されている [CentOS-HPC 7.6 VM OS イメージ](https://techcommunity.microsoft.com/t5/Azure-Compute/CentOS-HPC-VM-Image-for-SR-IOV-enabled-Azure-HPC-VMs/ba-p/665557)を使用することです。 このイメージは既に InfiniBand で構成されているため、手動で構成する必要はありません。 互換性のある Windows バージョンについては、「[Windows RDMA 対応のインスタンス](https://docs.microsoft.com/azure/virtual-machines/windows/sizes-hpc#rdma-capable-instances)」を参照してください。

- **GPU IaaS VM** – [CentOS-HPC 7.6 VM OS イメージ](https://techcommunity.microsoft.com/t5/Azure-Compute/CentOS-HPC-VM-Image-for-SR-IOV-enabled-Azure-HPC-VMs/ba-p/665557)を除き、現在 GPU 最適化されている VM 用に事前に構成されたプラットフォーム イメージはありません。 InfiniBand を使用してカスタム イメージを構成する方法については、「[Mellanox OFEDを手動でインストール](#manually-install-mellanox-ofed)」を参照してください。

カスタム VM イメージまたは [GPU 最適化されている](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-gpu) VM を使用している場合、InfiniBandDriverLinux または InfiniBandDriverWindows VM 拡張機能をデプロイして、InfiniBand (IB) でそれを構成する必要があります。 [Linux](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-hpc#rdma-capable-instances) および [Windows](https://docs.microsoft.com/azure/virtual-machines/windows/sizes-hpc#rdma-capable-instances) でこれらの VM 拡張機能を使用する方法について説明します。

## <a name="manually-install-mellanox-ofed"></a>Mellanox OFED の手動インストール

SR-IOV で InfiniBand を手動で構成するには、次の手順に従います。 これらの手順の例では、RHEL/CentOS の構文を示していますが、手順は一般的で、Ubuntu (16.04、18.04、19.04)、SLES (12 SP4、15) などの互換性のあるオペレーティング システムで使用できます。 受信トレイドライバーも同様に機能しますが、Mellanox OpenFabrics ドライバーはより多くの機能を提供します。

Mellanox ドライバーでサポートされているディストリビューションの詳細については、最新の [Mellanox OpenFabrics ドライバー](https://www.mellanox.com/page/products_dyn?product_family=26)に関するページを参照してください。 Mellanox OpenFabrics ドライバーの詳細については、[Mellanox ユーザー ガイド](https://docs.mellanox.com/category/mlnxofedib)のページを参照してください。

Linux で InfiniBand を構成する方法については、次の例を参照してください。

```bash
# Modify the variable to desired Mellanox OFED version
MOFED_VERSION=#4.7-1.0.0.1
# Modify the variable to desired OS
MOFED_OS=#rhel7.6
pushd /tmp
curl -fSsL https://www.mellanox.com/downloads/ofed/MLNX_OFED-${MOFED_VERSION}/MLNX_OFED_LINUX-${MOFED_VERSION}-${MOFED_OS}-x86_64.tgz | tar -zxpf -
cd MLNX_OFED_LINUX-*
sudo ./mlnxofedinstall
popd
```

Windows の場合は、[Windows 用の Mellanox OFED ドライバー](https://www.mellanox.com/page/products_dyn?product_family=32&menu_section=34)をダウンロードしてインストールします。

## <a name="enable-ip-over-infiniband"></a>IP over InfiniBand を有効にする

次のコマンドを実行して、IP over InfiniBand を有効にします。

```bash
sudo sed -i -e 's/# OS.EnableRDMA=y/OS.EnableRDMA=y/g' /etc/waagent.conf
sudo systemctl restart waagent
```

## <a name="next-steps"></a>次のステップ

Azure での [HPC](https://docs.microsoft.com/azure/architecture/topics/high-performance-computing/) の詳細を確認する。
