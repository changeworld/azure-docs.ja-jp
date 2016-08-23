<properties
 pageTitle="A8、A9、A10、A11 VM サイズと Windows について | Microsoft Azure"
 description="Windows VM とクラウド サービスに Azure A8、A9、A10、A11 という多くのコンピューティング処理を要するサイズを使用する場合の背景情報と考慮事項について説明します。"
 services="virtual-machines-windows, cloud-services"
 documentationCenter=""
 authors="dlepow"
 manager="timlt"
 editor=""
 tags="azure-resource-manager,azure-service-management"/>
<tags
ms.service="virtual-machines-windows"
 ms.devlang="na"
 ms.topic="article"
 ms.tgt_pltfrm="vm-windows"
 ms.workload="infrastructure-services"
 ms.date="08/04/2016"
 ms.author="danlep"/>

# A8、A9、A10、A11 コンピューティング集中型インスタンスについて

この記事では、*コンピューティング集中型*インスタンスとも呼ばれる Azure A8、A9、A10、A11 インスタンスの背景情報と使用上の考慮事項を示します。この記事は、Windows VM のこれらのインスタンスの使用方法について説明していますが、[Linux VM](virtual-machines-linux-a8-a9-a10-a11-specs.md) にも適用されます。

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]

[AZURE.INCLUDE [virtual-machines-common-a8-a9-a10-a11-specs](../../includes/virtual-machines-common-a8-a9-a10-a11-specs.md)]

## RDMA ネットワークへのアクセス

単一のクラウド サービス、可用性セット、または Azure Batch プール内では、A8 および A9 インスタンスは、Azure の RDMA ネットワークにアクセスし、Microsoft Network Direct インターフェイスを使用してインスタンス間の通信を行う Windows MPI アプリケーションを実行できます。

MPI アプリケーションが、Windows 仮想マシン、クラウド サービス、A8 または A9 インスタンスの Azure Batch プールで RDMA ネットワークにアクセスするための前提条件を次に示します。一般的なデプロイ シナリオについては、「[HPC Pack と A8 および A9 インスタンスを使用して Windows RDMA クラスターを設定して MPI アプリケーションを実行する](virtual-machines-windows-classic-hpcpack-rdma-cluster.md)」および「[Azure Batch でのマルチインスタンス タスクを使用した Message Passing Interface (MPI) アプリケーションの実行](../batch/batch-mpi.md)」をご覧ください。


前提条件 | 仮想マシン | クラウド サービスまたは Batch プール 
---------- | ------------ | ------------- 
オペレーティング システム | Windows Server 2012 R2 または Windows Server 2012 | Windows Server 2012 R2、Windows Server 2012、または Windows Server 2008 R2 ゲスト OS ファミリ 
MPI | MS-MPI 2012 R2 以降、または Intel MPI Library 5 | MS-MPI 2012 R2 以降、または Intel MPI Library 5 


>[AZURE.NOTE]A8 および A9 サイズの仮想マシンでは、HpcVmDrivers 拡張機能を VM に追加して、RDMA 接続に必要な Windows ネットワーク デバイス ドライバーをインストールする必要があります。デプロイ方法に応じて、A8 または A9 サイズの VM への HpcVmDrivers 拡張機能の追加は、自動的に実行される場合もあれば、手動で行うことが必要な場合もあります。拡張機能を手動で追加する方法については、「[仮想マシン拡張機能の管理](virtual-machines-windows-classic-manage-extensions.md)」をご覧ください。

## HPC Pack および Windows を使用する場合の考慮事項

Windows Server で A8、A9、A10、A11 インスタンスを使用するときに HPC Pack は不要ですが、このツールは Azure で RDMA ネットワークにアクセスする Windows ベースの MPI アプリケーションを実行する際に役立ちます。HPC Pack 2012 R2 以降のバージョンには、A8 および A9 インスタンスへのデプロイ時に Azure RDMA ネットワークを使用できる、Windows 用 の Message Passing Interface の Microsoft 実装 (MS-MPI) のためのランタイム環境が含まれています。

Windows Server で HPC Pack を使って、多くのコンピューティング処理を要するインスタンスを使用する方法およびチェックリストについては、「[Set up a Windows RDMA cluster with HPC Pack to run MPI applications (HPC Pack を使用して Windows RDMA クラスターをセットアップして MPI アプリケーションを実行する)](virtual-machines-windows-classic-hpcpack-rdma-cluster.md)」を参照してください。




## 次のステップ

* A8、A9、A10、および A11 インスタンスの可用性と料金の詳細については、「[Virtual Machines の料金](https://azure.microsoft.com/pricing/details/virtual-machines/#Windows)」および「[Cloud Services の料金](https://azure.microsoft.com/pricing/details/cloud-services/)」を参照してください。

* ストレージの容量とディスクの詳細については、「[仮想マシンのサイズ](virtual-machines-linux-sizes.md)」をご覧ください。

* Windows 上の HPC Pack により A8 および A9 インスタンスのデプロイと使用を開始する方法については、「[Set up a Windows RDMA cluster with HPC Pack to run MPI applications (HPC Pack を使用して Windows RDMA クラスターをセットアップして MPI アプリケーションを実行する)](virtual-machines-windows-classic-hpcpack-rdma-cluster.md)」を参照してください。

* A8 および A9 インスタンスを使用して Azure Batch で MPI アプリケーションを実行する方法については、「[Azure Batch でのマルチインスタンス タスクを使用した Message Passing Interface (MPI) アプリケーションの実行](../batch/batch-mpi.md)」をご覧ください。

<!---HONumber=AcomDC_0810_2016-->