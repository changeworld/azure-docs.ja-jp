---
title: "Azure Linux VM のサイズ - HPC | Microsoft Docs"
description: "Azure の Linux ハイ パフォーマンス コンピューティング仮想マシンで使用できるさまざまなサイズを一覧表示します。"
services: virtual-machines-linux
documentationcenter: 
author: jonbeck7
manager: timlt
editor: 
tags: azure-resource-manager,azure-service-management
ms.assetid: 
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 11/08/2017
ms.author: jonbeck
ms.openlocfilehash: 31a1ac1f58f52abd2d62e95a2de1a42ce678c43c
ms.sourcegitcommit: 93902ffcb7c8550dcb65a2a5e711919bd1d09df9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/09/2017
---
# <a name="high-performance-compute-virtual-machine-sizes"></a>ハイ パフォーマンス コンピューティング仮想マシンのサイズ

[!INCLUDE [virtual-machines-common-sizes-hpc](../../../includes/virtual-machines-common-sizes-hpc.md)]

[!INCLUDE [virtual-machines-common-sizes-table-defs](../../../includes/virtual-machines-common-sizes-table-defs.md)]

[!INCLUDE [virtual-machines-common-a8-a9-a10-a11-specs](../../../includes/virtual-machines-common-a8-a9-a10-a11-specs.md)]

## <a name="rdma-capable-instances"></a>RDMA 対応のインスタンス
コンピューティング集中型インスタンス (H16r、H16mr、A8、A9) のサブセットには、リモート ダイレクト メモリ アクセス (RDMA) 接続のためのネットワーク インターフェイスが備わっています。 このインターフェイスは、標準の Azure ネットワーク インターフェイスと同様に、他の VM サイズでも利用可能です。 
  
このインターフェイスにより、RDMA 対応インスタンスは InfiniBand ネットワークを介して通信することができ、H16r と H16mr の仮想マシンでは FDR のレートで、A8 と A9 の仮想マシンでは QDR のレートで動作します。 これらの RDMA 機能により、Intel MPI 5.x 以降のバージョンで実行している Message Passing Interface (MPI) アプリケーションのスケーラビリティとパフォーマンスが向上します。

RDMA 対応の VM を、同じ可用性セット (Azure Resource Manager デプロイメント モデルを使用している場合) または同じクラウド サービス (クラシック デプロイメント モデルを使用している場合) 内にデプロイします。 RDMA 対応の Linux VM が Azure RDMA ネットワークにアクセスするための追加の要件が発生します。

### <a name="distributions"></a>ディストリビューション
 
RDMA 接続をサポートする Azure Marketplace で、イメージの 1 つからコンピューティング集約型の VM をデプロイします。
  
* **Ubuntu** - Ubuntu Server 16.04 LTS。 VM で RDMA ドライバーを構成し、Intel に登録して Intel MPI をダウンロードします。

  [!INCLUDE [virtual-machines-common-ubuntu-rdma](../../../includes/virtual-machines-common-ubuntu-rdma.md)]

* **SUSE Linux Enterprise Server** - SLES 12 SP3 for HPC、SLES 12 SP3 for HPC (Premium)、SLES 12 SP1 for HPC、SLES 12 SP1 for HPC (Premium)。 RDMA ドライバーがインストールされ、Intel MPI パッケージが VM に配布されます。 次のコマンドを実行して MPI をインストールします。

  ```bash
  sudo rpm -v -i --nodeps /opt/intelMPI/intel_mpi_packages/*.rpm
  ```
    
* **CentOS ベース HPC** - CentOS ベース 7.3 HPC, CentOS ベース 7.1 HPC、CentOS ベース 6.8 HPC、または CentOS ベース 6.5 HPC (H シリーズの場合、バージョン 7.1 以降が推奨されます)。 RDMA ドライバーおよび Intel MPI 5.1 は、VM にインストールされます。  
 
  > [!NOTE]
  > CentOS ベースの HPC イメージでは、 **yum** 構成ファイルでのカーネルの更新は無効にされています。 これは、Linux RDMA ドライバーが RPM パッケージとして配布されており、カーネルが更新された場合にドライバーの更新プログラムが機能しない可能性があるためです。
  > 
 
### <a name="cluster-configuration"></a>クラスター構成 
    
クラスター化された VM で MPI ジョブを実行するには、追加のシステム構成が必要です。 たとえば、VM のクラスターでは、コンピューティング ノード間で信頼関係を確立する必要があります。 標準的な設定については、「[MPI アプリケーションを実行するように Linux RDMA クラスターを設定する](classic/rdma-cluster.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json)」をご覧ください。

### <a name="network-topology-considerations"></a>ネットワーク トポロジに関する考慮事項
* Azure の RDMA 対応 Linux VM の場合、Eth1 は RDMA のネットワーク トラフィック用に予約されています。 Eth1 設定や、このネットワークを参照する構成ファイル内の情報を変更しないでください。 Eth0 は Azure の通常のネットワーク トラフィック用に予約されています。

* Azure では、IP over InfiniBand (IB) はサポートされません。 RDMA over IB のみがサポートされます。

## <a name="using-hpc-pack"></a>HPC Pack の使用
[HPC Pack](https://technet.microsoft.com/library/jj899572.aspx) (Microsoft が無償で提供している HPC クラスターとジョブの管理ソリューション) は、Linux でコンピューティング集中型インスタンスを使用するための選択肢の 1 つです。 最新リリースの HPC Pack では、Azure VM にデプロイされ、Windows Server ヘッド ノードで管理されるコンピューティング ノードで、複数の Linux ディストリビューションを実行できるようになりました。 HPC Pack を使用すると、Intel MPI を実行する RDMA 対応 Linux コンピューティング ノードで、RDMA ネットワークにアクセスする MPI アプリケーションのスケジュール設定と実行が可能です。 「[Azure の HPC Pack クラスターで Linux コンピューティング ノードの使用を開始する](classic/hpcpack-cluster.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json)」を参照してください。

## <a name="other-sizes"></a>その他のサイズ
- [汎用](sizes-general.md)
- [コンピューティングの最適化](sizes-compute.md)
- [メモリの最適化](sizes-memory.md)
- [ストレージの最適化](sizes-storage.md)
- [GPU](../windows/sizes-gpu.md)


## <a name="next-steps"></a>次のステップ

- Linux 上の RDMA によりコンピューティング集中型サイズのデプロイと使用を開始する方法については、「[MPI アプリケーションを実行するように Linux RDMA クラスターを設定する](classic/rdma-cluster.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json)」をご覧ください。

- [Azure コンピューティング ユニット (ACU)](acu.md) を確認することで、Azure SKU 全体の処理性能を比較できます。




