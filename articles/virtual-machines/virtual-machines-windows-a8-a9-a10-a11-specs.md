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
ms.date: 11/21/2016
ms.author: danlep
translationtype: Human Translation
ms.sourcegitcommit: 5a46b53f42fce7577485517e9d345cf2ad3f0926
ms.openlocfilehash: d4d0a221d531c4f6eda7bb6410fbc4dcebf5074b


---
# <a name="about-h-series-and-compute-intensive-a-series-vms"></a>H シリーズとコンピューティング集中型 A シリーズの VM について
この記事では、*コンピューティング集中型*インスタンスとも呼ばれる新しい Azure H シリーズとそれ以前の A8、A9、A10、A11 インスタンスの背景情報と使用上の考慮事項を示します。 この記事は、Windows VM のこれらのインスタンスの使用方法について説明していますが、 [Linux VM](virtual-machines-linux-a8-a9-a10-a11-specs.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) にも適用されます。

基本的な仕様、ストレージの容量、ディスクの詳細については、[仮想マシンのサイズ](virtual-machines-windows-sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)に関する記事をご覧ください。

[!INCLUDE [virtual-machines-common-a8-a9-a10-a11-specs](../../includes/virtual-machines-common-a8-a9-a10-a11-specs.md)]

## <a name="access-to-the-rdma-network"></a>RDMA ネットワークへのアクセス
Windows MPI トラフィック用に Azure RDMA ネットワークにアクセスするには、RDMA 対応のインスタンスが次の要件を満たす必要があります。 

* **オペレーティング システム**
  
  * **仮想マシン** - Windows Server 2012 R2、Windows Server 2012
  * **クラウド サービス** - Windows Server 2012 R2、Windows Server 2012、または Windows Server 2008 R2 ゲスト OS ファミリ
* **MPI** -Microsoft MPI (MS-MPI) 2012 R2 以降、Intel MPI ライブラリ 5.x

  サポートされている MPI 実装では、インスタンス間の通信に Microsoft Network Direct インターフェイスが使用されます。 
* **HpcVmDrivers 拡張機能** - RDMA 対応の VM で、HpcVmDrivers 拡張機能を追加して、RDMA 接続を有効にする Windows ネットワーク デバイス ドライバーをインストールする必要があります  (A8 インスタンスと A9 インスタンスの一部のデプロイでは、HpcVmDrivers 拡張機能は自動的に追加されます)。VM に VM 拡張機能を追加する必要がある場合は、Azure Resource Manager の [Azure PowerShell](/powershell/azureps-cmdlets-docs) コマンドレットを使用します。

  最新の HpcVmDrivers 拡張機能に関する情報を取得するには: 

  ```PowerShell
  Get-AzureVMAvailableExtension -ExtensionName  "HpcVmDrivers"
  ```

  既存の RDMA 対応 VM (myVM) に HpcVMDrivers 拡張機能の最新バージョン 1.1 をインストールするには: 
  ```PowerShell
  Set-AzureRmVMExtension -ResourceGroupName "myResourceGroup" -Location "westus" -VMName "myVM" -ExtensionName "HpcVmDrivers" -Publisher "Microsoft.HpcCompute" -Type "HpcVmDrivers" -TypeHandlerVersion "1.1"
  ```
  詳細については、[VM 拡張機能の管理](virtual-machines-windows-classic-manage-extensions.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)に関するページをご覧ください。 [クラシック デプロイ モデル](virtual-machines-windows-classic-manage-extensions.md)でも、VM の拡張機能を使用できます。


## <a name="considerations-for-hpc-pack-and-windows"></a>HPC Pack および Windows を使用する場合の考慮事項
無料で利用できる Microsoft の HPC クラスターとジョブの管理ソリューションである [Microsoft HPC Pack](https://technet.microsoft.com/library/jj899572.aspx) は、Windows Server でコンピューティング集中型インスタンスを使用する場合は不要です。 ただし、Windows ベースの MPI アプリケーションおよびその他の HPC ワークロードを実行するコンピューティング クラスターを Azure で作成する場合は、オプションとして使用できます。 HPC Pack 2012 R2 以降のバージョンには、RDMA 対応の VM 上でデプロイした場合に Azure RDMA ネットワークを使用できる MS-MPI 用のランタイム環境が含まれています。

Windows Server で HPC Pack を使って、多くのコンピューティング処理を要するインスタンスを使用する方法およびチェックリストについては、「[HPC Pack を使用して Windows RDMA クラスターをセットアップして MPI アプリケーションを実行する](virtual-machines-windows-classic-hpcpack-rdma-cluster.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)」を参照してください。

## <a name="next-steps"></a>次のステップ
* コンピューティング集中型サイズの可用性と料金の詳細については、「[Virtual Machines の料金](https://azure.microsoft.com/pricing/details/virtual-machines/#Windows)」および「[Cloud Services の価格](https://azure.microsoft.com/pricing/details/cloud-services/)」を参照してください。
* ストレージの容量とディスクの詳細については、「[仮想マシンのサイズ](virtual-machines-linux-sizes.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)」をご覧ください。
* Windows 上の HPC Pack によりコンピューティング集中型インスタンスのデプロイと使用を開始する方法については、「[PC Pack を使用して Windows RDMA クラスターをセットアップして MPI アプリケーションを実行する](virtual-machines-windows-classic-hpcpack-rdma-cluster.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)」を参照してください。
* コンピューティング集中型インスタンスを使用して Azure Batch で MPI アプリケーションを実行する方法については、「[Azure Batch でのマルチインスタンス タスクを使用した Message Passing Interface (MPI) アプリケーションの実行](../batch/batch-mpi.md)」をご覧ください。




<!--HONumber=Jan17_HO1-->


