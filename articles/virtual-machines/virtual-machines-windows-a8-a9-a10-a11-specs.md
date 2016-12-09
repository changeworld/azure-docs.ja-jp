---
title: "Windows でのコンピューティング集中型 VM について | Microsoft Docs"
description: "Windows VM とクラウド サービスに Azure H シリーズと A8、A9、A10、A11 という多くのコンピューティング処理を要するサイズを使用する場合の背景情報と考慮事項について説明します。"
services: virtual-machines-windows, cloud-services
documentationcenter: 
author: dlepow
manager: timlt
editor: 
tags: azure-resource-manager,azure-service-management
ms.assetid: 28d8e1f2-8e61-4fbe-bfe8-80a68443baba
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 09/21/2016
ms.author: danlep
translationtype: Human Translation
ms.sourcegitcommit: ee34a7ebd48879448e126c1c9c46c751e477c406
ms.openlocfilehash: 3223de765d7f746473b48f99cd9d27fb013ec7ef


---
# <a name="about-h-series-and-compute-intensive-a-series-vms"></a>H シリーズとコンピューティング集中型 A シリーズの VM について
この記事では、*コンピューティング集中型*インスタンスとも呼ばれる新しい Azure H シリーズとそれ以前の A8、A9、A10、A11 インスタンスの背景情報と使用上の考慮事項を示します。 この記事は、Windows VM のこれらのインスタンスの使用方法について説明していますが、 [Linux VM](virtual-machines-linux-a8-a9-a10-a11-specs.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) にも適用されます。

[!INCLUDE [virtual-machines-common-a8-a9-a10-a11-specs](../../includes/virtual-machines-common-a8-a9-a10-a11-specs.md)]

## <a name="access-to-the-rdma-network"></a>RDMA ネットワークへのアクセス
RDMA 対応の Windows Server インスタンスのクラスターを作成して、サポートされている MPI 実装のいずれかをデプロイすることにより、Azure RDMA ネットワークを利用できます。 待機時間が短くスループットが高いこのネットワークは MPI トラフィック専用です。

* **オペレーティング システム**
  
  * **仮想マシン** - Windows Server 2012 R2、Windows Server 2012
  * **クラウド サービス** - Windows Server 2012 R2、Windows Server 2012、または Windows Server 2008 R2 ゲスト OS ファミリ
* **MPI** -Microsoft MPI (MS-MPI) 2012 R2 以降、Intel MPI ライブラリ 5.x

サポートされている MPI 実装では、インスタンス間の通信に Microsoft Network Direct インターフェイスが使用されます。 デプロイ オプションとサンプルの構成手順については、「[HPC Pack と A8 および A9 インスタンスを使用して Windows RDMA クラスターを設定して MPI アプリケーションを実行する](virtual-machines-windows-classic-hpcpack-rdma-cluster.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)」および「[Azure Batch でのマルチインスタンス タスクを使用した Message Passing Interface (MPI) アプリケーションの実行](../batch/batch-mpi.md)」をご覧ください。

> [!NOTE]
> RDMA 対応のコンピューティング集中型 VM では、HpcVmDrivers 拡張機能を VM に追加して、RDMA 接続に必要な Windows ネットワーク デバイス ドライバーをインストールする必要があります。 ほとんどのデプロイでは、HpcVmDrivers 拡張機能は自動的に追加されます。 拡張機能を手動で追加する必要がある場合は、「[仮想マシン拡張機能の管理](virtual-machines-windows-classic-manage-extensions.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)」をご覧ください。
> 
> 

## <a name="considerations-for-hpc-pack-and-windows"></a>HPC Pack および Windows を使用する場合の考慮事項
無料で利用できる Microsoft の HPC クラスターとジョブの管理ソリューションである [Microsoft HPC Pack](https://technet.microsoft.com/library/jj899572.aspx) は、Windows Server でコンピューティング集中型インスタンスを使用する場合は不要です。 ただし、Windows ベースの MPI アプリケーションおよびその他の HPC ワークロードを実行するコンピューティング クラスターを Azure で作成する場合は、オプションとして使用できます。 HPC Pack 2012 R2 以降のバージョンには、RDMA 対応の VM 上でデプロイした場合に Azure RDMA ネットワークを使用できる MS-MPI 用のランタイム環境が含まれています。

Windows Server で HPC Pack を使って、多くのコンピューティング処理を要するインスタンスを使用する方法およびチェックリストについては、「[HPC Pack を使用して Windows RDMA クラスターをセットアップして MPI アプリケーションを実行する](virtual-machines-windows-classic-hpcpack-rdma-cluster.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)」を参照してください。

## <a name="next-steps"></a>次のステップ
* コンピューティング集中型サイズの可用性と料金の詳細については、「[Virtual Machines の料金](https://azure.microsoft.com/pricing/details/virtual-machines/#Windows)」および「[Cloud Services の価格](https://azure.microsoft.com/pricing/details/cloud-services/)」を参照してください。
* ストレージの容量とディスクの詳細については、「[仮想マシンのサイズ](virtual-machines-linux-sizes.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)」をご覧ください。
* Windows 上の HPC Pack によりコンピューティング集中型インスタンスのデプロイと使用を開始する方法については、「[PC Pack を使用して Windows RDMA クラスターをセットアップして MPI アプリケーションを実行する](virtual-machines-windows-classic-hpcpack-rdma-cluster.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)」を参照してください。
* A8 および A9 インスタンスを使用して Azure Batch で MPI アプリケーションを実行する方法については、「 [Azure Batch でのマルチインスタンス タスクを使用した Message Passing Interface (MPI) アプリケーションの実行](../batch/batch-mpi.md)」をご覧ください。




<!--HONumber=Nov16_HO3-->


