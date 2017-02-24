---
title: "Linux でのコンピューティング集中型 VM について | Microsoft Docs"
description: "Linux VM の H シリーズと A8、A9、A10、A11 というコンピューティング集中型サイズの背景情報と使用上の注意事項について説明します。"
services: virtual-machines-linux
documentationcenter: 
author: dlepow
manager: timlt
editor: 
tags: azure-resource-manager,azure-service-management
ms.assetid: 16cf6e2d-f401-4b22-af8c-9a337179f5f6
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 11/18/2016
ms.author: danlep
translationtype: Human Translation
ms.sourcegitcommit: f73cbfec2ce2e41589b84997891ff0b60266c9b2
ms.openlocfilehash: 10a10e138c9c8ed8c15136bf1d6565edc57758b5


---
# <a name="about-h-series-and-compute-intensive-a-series-vms"></a>H シリーズとコンピューティング集中型 A シリーズの VM について
この記事では、新しい Azure H シリーズとそれ以前の A8、A9、A10、A11 サイズ (*コンピューティング集中型*インスタンスとも呼ばれます) の背景情報と使用上の考慮事項を示します。 この記事は、Linux VM のこれらのサイズの使用方法について説明していますが、 [Windows VM](virtual-machines-windows-a8-a9-a10-a11-specs.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) にも適用されます。 

基本的な仕様、ストレージの容量、ディスクの詳細については、[仮想マシンのサイズ](virtual-machines-linux-sizes.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)に関する記事をご覧ください。

[!INCLUDE [virtual-machines-common-a8-a9-a10-a11-specs](../../includes/virtual-machines-common-a8-a9-a10-a11-specs.md)]

## <a name="access-to-the-rdma-network"></a>RDMA ネットワークへのアクセス
Azure RDMA ネットワークを活用するために、次のサポートされる Linux HPC ディストリビューションのいずれかとサポートされる MPI 実装を実行する、RDMA 対応 Linux VM のクラスターを作成することができます。 デプロイのオプションとサンプルの構成手順については、「[MPI アプリケーションを実行するように Linux RDMA クラスターを設定する](virtual-machines-linux-classic-rdma-cluster.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json)」をご覧ください。

* **ディストリビューション** - Azure Marketplace にある RDMA 対応 SUSE Linux Enterprise Server (SLES) イメージまたは OpenLogic CentOS ベースの HPC イメージから VM をデプロイする必要があります。 次の Marketplace イメージでのみ、必要な Linux RDMA ドライバーがサポートされます。
  
  * SLES 12 SP1 for HPC、SLES 12 SP1 for HPC (Premium)
  * SLES 12 for HPC、SLES 12 for HPC (Premium)
  * CentOS ベースの 7.1 HPC
  * CentOS ベースの 6.5 HPC
    
    > [!NOTE]
    > H シリーズ VM の場合、SLES 12 SP1 for HPC イメージまたは CentOS ベースの 7.1 HPC イメージのいずれかをお勧めします。
    > 
    > CentOS ベースの HPC イメージでは、 **yum** 構成ファイルでのカーネルの更新は無効にされています。 これは、Linux RDMA ドライバーが RPM パッケージとして配布されており、カーネルが更新された場合にドライバーの更新プログラムが機能しない可能性があるためです。
    > 
    > 
* **MPI** - Intel MPI Library 5.x
  
    選択した Marketplace イメージに応じて、次に示すような Intel MPI の個別のライセンス、インストール、または構成が必要になることがあります。 
  
  * **SLES 12 SP1 for HPC イメージ** - 次のコマンドを実行して、VM に配布された Intel MPI パッケージをインストールします。
    
          sudo rpm -v -i --nodeps /opt/intelMPI/intel_mpi_packages/*.rpm
  * **SLES 12 for HPC イメージ** - Intel MPI のダウンロードおよびインストールを行うには、個別に登録する必要があります。 手順については、[Intel MPI Library のドキュメント](https://software.intel.com/en-us/intel-mpi-library/documentation)を参照してください。
  * **CentOS ベースの HPC イメージ** - Intel MPI 5.1 は既にインストール済みです。  
    
    クラスター化された VM で MPI ジョブを実行するには、追加のシステム構成が必要です。 たとえば、VM のクラスターでは、コンピューティング ノード間で信頼関係を確立する必要があります。 標準的な設定については、「[MPI アプリケーションを実行するように Linux RDMA クラスターを設定する](virtual-machines-linux-classic-rdma-cluster.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json)」をご覧ください。

## <a name="considerations-for-hpc-pack-and-linux"></a>HPC Pack および Linux を使用する場合の考慮事項
[HPC Pack](https://technet.microsoft.com/library/jj899572.aspx)(Microsoft が無償で提供している HPC クラスターとジョブの管理ソリューション) は、Linux でコンピューティング集中型インスタンスを使用するための選択肢の 1 つです。 最新リリースの HPC Pack 2012 R2 では、Azure VM にデプロイされ、Windows Server ヘッド ノードで管理されるコンピューティング ノードで、複数の Linux ディストリビューションを実行できるようになりました。 HPC Pack を使用すると、Intel MPI を実行する RDMA 対応 Linux コンピューティング ノードで、RDMA ネットワークにアクセスする MPI アプリケーションのスケジュール設定と実行が可能です。 作業を開始するには、「[Azure の HPC Pack クラスターで Linux コンピューティング ノードの使用を開始する](virtual-machines-linux-classic-hpcpack-cluster.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json)」をご覧ください。

## <a name="network-topology-considerations"></a>ネットワーク トポロジに関する考慮事項
* Azure の RDMA 対応 Linux VM の場合、Eth1 は RDMA のネットワーク トラフィック用に予約されています。 Eth1 設定や、このネットワークを参照する構成ファイル内の情報を変更しないでください。 Eth0 は Azure の通常のネットワーク トラフィック用に予約されています。
* Azure では、IP over InfiniBand (IB) はサポートされません。 RDMA over IB のみがサポートされます。

## <a name="rdma-driver-updates-for-sles-12"></a>SLES 12 の RDMA ドライバーの更新プログラム
SLES 12 HPC イメージに基づいて VM を作成したら、RDMA ネットワーク接続のために VM の RDMA ドライバーを更新することが必要になる場合があります。 

> [!IMPORTANT]
> この手順は、すべての Azure リージョンの SLES 12 for HPC VM デプロイに **必要です** 。 SLES 12 SP1 for HPC、CentOS ベースの 7.1 HPC、または CentOS ベースの 6.5 HPC VM をデプロイする場合には、この手順は不要です。 
> 
> 

ドライバーを更新する前に、すべての **zypper** プロセス、または VM の SUSE リポジトリ データベースをロックするプロセスを停止します。 そうしないと、ドライバーが正しく更新されない場合があります。  

クライアント コンピューターで次の Azure CLI コマンド セットのいずれかを実行して、各 VM の Linux RDMA ドライバーを更新します。

**クラシック デプロイメント モデルでプロビジョニングされた SLES 12 for HPC VM**

```
azure config mode asm

azure vm extension set <vm-name> RDMAUpdateForLinux Microsoft.OSTCExtensions 0.1
```

**Resource Manager デプロイメント モデルでプロビジョニングされた SLES 12 for HPC VM**

```
azure config mode arm

azure vm extension set <resource-group> <vm-name> RDMAUpdateForLinux Microsoft.OSTCExtensions 0.1
```

> [!NOTE]
> ドライバーのインストールには時間がかかる場合があります。また、コマンドは出力なしで終了します。 更新後、VM は再起動され、数分で使用できるようになります。
> 
> 

### <a name="sample-script-for-driver-updates"></a>ドライバー更新プログラムのサンプル スクリプト
SLES 12 for HPC VM のクラスターがある場合、クラスターのすべてのノードのドライバー更新をスクリプトにすることができます。 たとえば、次のスクリプトは、8 ノード クラスター内のドライバーを更新します。

```

#!/bin/bash -x

# Define a prefix naming scheme for compute nodes, e.g., cluster11, cluster12, etc.

vmname=cluster

# Plug in appropriate numbers in the for loop below.

for (( i=11; i<19; i++ )); do

# For VMs created in the classic deployment model, use the following command in your script.

azure vm extension set $vmname$i RDMAUpdateForLinux Microsoft.OSTCExtensions 0.1

# For VMs created in the Resource Manager deployment model, use the following command in your script.

# azure vm extension set <resource-group> $vmname$i RDMAUpdateForLinux Microsoft.OSTCExtensions 0.1

done

```


## <a name="next-steps"></a>次のステップ
* コンピューティング集中型サイズの可用性と価格の詳細については、 [Virtual Machines の価格](https://azure.microsoft.com/pricing/details/virtual-machines/#Linux)に関するページを参照してください。
* ストレージの容量とディスクの詳細については、「[仮想マシンのサイズ](virtual-machines-linux-sizes.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)」をご覧ください。
* Linux 上の RDMA によりコンピューティング集中型サイズのデプロイと使用を開始する方法については、「[MPI アプリケーションを実行するように Linux RDMA クラスターを設定する](virtual-machines-linux-classic-rdma-cluster.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json)」をご覧ください。




<!--HONumber=Nov16_HO4-->


