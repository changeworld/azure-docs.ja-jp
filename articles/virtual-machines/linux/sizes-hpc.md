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
ms.date: 10/12/2018
ms.author: jonbeck
ms.openlocfilehash: 70dca655d5300fcd34b4198093e136f6a971963b
ms.sourcegitcommit: 1aacea6bf8e31128c6d489fa6e614856cf89af19
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/16/2018
ms.locfileid: "49344491"
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
 
### <a name="cluster-configuration-options"></a>クラスター構成オプション

Azure には、次に示すような、RDMA ネットワークを使用して通信できる Linux HPC VM のクラスターを作成するためのオプションがいくつか用意されています。 

* **仮想マシン** - 同じ可用性セット内に RDMA 対応の HPC VM をデプロイします (Azure Resource Manager デプロイ モデルを使用する場合)。 クラシック デプロイ モデルを使用する場合は、同じクラウド サービス内に VM をデプロイします。 

* **仮想マシン スケール セット** - VM スケール セットでは、デプロイを 1 つの配置グループに制限するようにしてください。 たとえば、Resource Manager テンプレートでは、`singlePlacementGroup` プロパティを `true` に設定します。 

* **Azure CycleCloud** - [Azure CycleCloud](/azure/cyclecloud/) 内に HPC クラスターを作成して、Linux ノード上で MPI ジョブを実行します。

* **Azure Batch** - [Azure Batch](/azure/batch/) プールを作成して、Linux コンピューティング ノード上で MPI ワークロードを実行します。 詳細については、「[Batch プールでの RDMA 対応または GPU 対応インスタンスの使用](../../batch/batch-pool-compute-intensive-sizes.md)」を参照してください。 また、Batch でのコンテナー ベースのワークロードの実行について [Batch Shipyard](https://github.com/Azure/batch-shipyard) プロジェクトも参照してください。

* **Microsoft HPC Pack** - [HPC Pack](https://docs.microsoft.com/powershell/high-performance-computing/overview) は、Windows Server ヘッド ノードによって管理される、RDMA 対応の Azure VM にデプロイされたコンピューティング ノード上での複数の Linux ディストリビューションの実行をサポートします。 デプロイの例については、[Azure での HPC Pack Linux RDMA クラスターの作成](https://docs.microsoft.com/powershell/high-performance-computing/hpcpack-linux-openfoam)に関するページを参照してください。

クラスター管理ツールの選択によっては、MPI ジョブを実行するために追加のシステム構成が必要になることがあります。 たとえば、VM のクラスター上では、SSH キーを生成するか、またはパスワードなしの SSH 信頼関係を確立することによって、クラスター ノード間の信頼を確立することが必要になる場合があります。

### <a name="network-topology-considerations"></a>ネットワーク トポロジに関する考慮事項
* Azure の RDMA 対応 Linux VM の場合、Eth1 は RDMA のネットワーク トラフィック用に予約されています。 Eth1 設定や、このネットワークを参照する構成ファイル内の情報を変更しないでください。 Eth0 は Azure の通常のネットワーク トラフィック用に予約されています。

* Azure の RDMA ネットワークでは、アドレス空間 172.16.0.0/16 は予約済みです。 




## <a name="other-sizes"></a>その他のサイズ
- [汎用](sizes-general.md)
- [コンピューティングの最適化](sizes-compute.md)
- [メモリの最適化](sizes-memory.md)
- [ストレージの最適化](sizes-storage.md)
- [GPU](../windows/sizes-gpu.md)
- [旧世代](sizes-previous-gen.md)

## <a name="next-steps"></a>次の手順

- [Azure コンピューティング ユニット (ACU)](acu.md) を確認することで、Azure SKU 全体の処理性能を比較できます。




