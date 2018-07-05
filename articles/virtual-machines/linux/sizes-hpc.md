---
title: Azure Linux VM のサイズ - HPC | Microsoft Docs
description: Azure の Linux ハイ パフォーマンス コンピューティング仮想マシンで使用できるさまざまなサイズを一覧表示します。 このシリーズのストレージのスループットとネットワーク帯域幅に加え、vCPU、データ ディスク、NIC の数に関する情報を一覧表示します。
services: virtual-machines-linux
documentationcenter: ''
author: jonbeck7
manager: jeconnoc
editor: ''
tags: azure-resource-manager,azure-service-management
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 03/15/2018
ms.author: jonbeck
ms.openlocfilehash: 441e99b86e9560d47af8ea18a2633e3f37a05e94
ms.sourcegitcommit: 86cb3855e1368e5a74f21fdd71684c78a1f907ac
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/04/2018
ms.locfileid: "34653843"
---
# <a name="high-performance-compute-virtual-machine-sizes"></a>ハイ パフォーマンス コンピューティング仮想マシンのサイズ

[!INCLUDE [virtual-machines-common-sizes-hpc](../../../includes/virtual-machines-common-sizes-hpc.md)]

[!INCLUDE [virtual-machines-common-sizes-table-defs](../../../includes/virtual-machines-common-sizes-table-defs.md)]

[!INCLUDE [virtual-machines-common-a8-a9-a10-a11-specs](../../../includes/virtual-machines-common-a8-a9-a10-a11-specs.md)]


### <a name="mpi"></a>MPI 

Intel MPI 5.x バージョンのみがサポートされています。 Intel MPI ランタイム ライブラリの以降のバージョン (2017、2018) は、Azure Linux RDMA ドライバーと互換性がありません。


### <a name="distributions"></a>ディストリビューション
 
RDMA 接続をサポートする Azure Marketplace で、イメージの 1 つからコンピューティング集約型の VM をデプロイします。
  
* **Ubuntu** - Ubuntu Server 16.04 LTS。 VM で RDMA ドライバーを構成し、Intel に登録して Intel MPI をダウンロードします。

  [!INCLUDE [virtual-machines-common-ubuntu-rdma](../../../includes/virtual-machines-common-ubuntu-rdma.md)]

* **SUSE Linux Enterprise Server** - SLES 12 SP3 for HPC、SLES 12 SP3 for HPC (Premium)、SLES 12 SP1 for HPC、SLES 12 SP1 for HPC (Premium)。 RDMA ドライバーがインストールされ、Intel MPI パッケージが VM に配布されます。 次のコマンドを実行して MPI をインストールします。

  ```bash
  sudo rpm -v -i --nodeps /opt/intelMPI/intel_mpi_packages/*.rpm
  ```
    
* **CentOS ベース HPC** - CentOS ベース 6.5 HPC 以降のバージョン (H シリーズの場合、バージョン 7.1 以降が推奨されます)。 RDMA ドライバーおよび Intel MPI 5.1 は、VM にインストールされます。  
 
  > [!NOTE]
  > CentOS ベースの HPC イメージでは、 **yum** 構成ファイルでのカーネルの更新は無効にされています。 これは、Linux RDMA ドライバーが RPM パッケージとして配布されており、カーネルが更新された場合にドライバーの更新プログラムが機能しない可能性があるためです。
  > 
 
### <a name="cluster-configuration"></a>クラスター構成 
    
クラスター化された VM で MPI ジョブを実行するには、追加のシステム構成が必要です。 たとえば、VM のクラスターでは、コンピューティング ノード間で信頼関係を確立する必要があります。 標準的な設定については、「[MPI アプリケーションを実行するように Linux RDMA クラスターを設定する](classic/rdma-cluster.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json)」をご覧ください。

### <a name="network-topology-considerations"></a>ネットワーク トポロジに関する考慮事項
* Azure の RDMA 対応 Linux VM の場合、Eth1 は RDMA のネットワーク トラフィック用に予約されています。 Eth1 設定や、このネットワークを参照する構成ファイル内の情報を変更しないでください。 Eth0 は Azure の通常のネットワーク トラフィック用に予約されています。

* Azure の RDMA ネットワークでは、アドレス空間 172.16.0.0/16 は予約済みです。 


## <a name="using-hpc-pack"></a>HPC Pack の使用
[HPC Pack](https://technet.microsoft.com/library/jj899572.aspx) (Microsoft が無償で提供している HPC クラスターとジョブの管理ソリューション) は、Linux でコンピューティング集中型インスタンスを使用するための選択肢の 1 つです。 最新リリースの HPC Pack では、Azure VM にデプロイされ、Windows Server ヘッド ノードで管理されるコンピューティング ノードで、複数の Linux ディストリビューションを実行できるようになりました。 HPC Pack を使用すると、Intel MPI を実行する RDMA 対応 Linux コンピューティング ノードで、RDMA ネットワークにアクセスする MPI アプリケーションのスケジュール設定と実行が可能です。 「[Azure の HPC Pack クラスターで Linux コンピューティング ノードの使用を開始する](classic/hpcpack-cluster.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json)」を参照してください。

## <a name="other-sizes"></a>その他のサイズ
- [汎用](sizes-general.md)
- [コンピューティングの最適化](sizes-compute.md)
- [メモリの最適化](sizes-memory.md)
- [ストレージの最適化](sizes-storage.md)
- [GPU](../windows/sizes-gpu.md)
- [旧世代](sizes-previous-gen.md)

## <a name="next-steps"></a>次の手順

- Linux 上の RDMA によりコンピューティング集中型サイズのデプロイと使用を開始する方法については、「[MPI アプリケーションを実行するように Linux RDMA クラスターを設定する](classic/rdma-cluster.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json)」をご覧ください。

- [Azure コンピューティング ユニット (ACU)](acu.md) を確認することで、Azure SKU 全体の処理性能を比較できます。




