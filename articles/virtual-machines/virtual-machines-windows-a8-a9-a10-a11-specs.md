<properties
 pageTitle="A8 ～ A11 インスタンスと Windows について | Microsoft Azure"
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
 ms.date="01/26/2016"
 ms.author="danlep"/>

# Windows での A8、A9、A10、A11 コンピューティング集中型インスタンスの使用について

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]

[AZURE.INCLUDE [virtual-machines-common-a8-a9-a10-a11-specs](../../includes/virtual-machines-common-a8-a9-a10-a11-specs.md)]

## RDMA ネットワークへのアクセス

単一のクラウド サービスまたは可用性セット内では、A8 および A9 インスタンスは、Azure の RDMA ネットワークにアクセスして、Microsoft Network Direct インターフェイスを使用してインスタンス間の通信を行う Windows MPI アプリケーションを実行できます。

A8 または A9 インスタンスの仮想マシン (IaaS) およびクラウド サービス (PaaS) デプロイ内の RDMA ネットワークにアクセスするための MPI アプリケーションの要件を、次の表に示します。標準的なデプロイのシナリオについては、「[Set up a Windows RDMA cluster with HPC Pack to run MPI applications (HPC Pack を使用して Windows RDMA クラスターをセットアップして MPI アプリケーションを実行する)](virtual-machines-windows-classic-hpcpack-rdma-cluster.md)」を参照してください。


前提条件 | 仮想マシン (IaaS) | クラウド サービス (PaaS)
---------- | ------------ | -------------
オペレーティング システム | Windows Server 2012 R2 または Windows Server 2012 | Windows Server 2012 R2、Windows Server 2012、または Windows Server 2008 R2 ゲスト OS ファミリ
MPI | MS-MPI 2012 R2 以降 (スタンドアロンまたは HPC Pack 2012 R2 以降によってインストール済み)<br/><br/>Intel MPI Library 5 | MS-MPI 2012 R2 以降 (HPC Pack 2012 R2 以降によってインストール済み)<br/><br/>Intel MPI Library 5


>[AZURE.NOTE]IaaS シナリオの場合、HpcVmDrivers 拡張機能を VM に追加して RDMA 接続に必要な Windows ネットワーク デバイス ドライバーをインストールする必要があります。デプロイの方法によって、サイズ A8 または A9 の VM への HpcVmDrivers 拡張機能の追加は、自動的に行われることも、手動で行う必要があることもあります。拡張機能の追加については、「[仮想マシン拡張機能の管理](virtual-machines-windows-classic-manage-extensions.md)」を参照してください。

## HPC Pack および Windows を使用する場合の考慮事項

HPC Pack は、Windows Server で A8、A9、A10、および A11 インスタンスを使用するためには必要ありませんが、Azure で RDMA ネットワークにアクセスする Windows ベースの MPI アプリケーションを実行する場合は推奨されるツールです。HPC Pack には、マイクロソフトによる Windows 用の Message Passing Interface (MS-MPI) の実装のためのランタイム環境が含まれます。

Windows Server で HPC Pack を使って、多くのコンピューティング処理を要するインスタンスを使用する方法およびチェックリストについては、「[Set up a Windows RDMA cluster with HPC Pack to run MPI applications (HPC Pack を使用して Windows RDMA クラスターをセットアップして MPI アプリケーションを実行する)](virtual-machines-windows-classic-hpcpack-rdma-cluster.md)」を参照してください。

## 次のステップ

* A8、A9、A10、および A11 インスタンスの可用性と料金の詳細については、「[Virtual Machines の料金](https://azure.microsoft.com/pricing/details/virtual-machines/)」および「[Cloud Services の料金](https://azure.microsoft.com/pricing/details/cloud-services/)」を参照してください。

* Windows 上の HPC Pack により A8 および A9 インスタンスのデプロイと使用を開始する方法については、「[Set up a Windows RDMA cluster with HPC Pack to run MPI applications (HPC Pack を使用して Windows RDMA クラスターをセットアップして MPI アプリケーションを実行する)](virtual-machines-windows-classic-hpcpack-rdma-cluster.md)」を参照してください。

* A8 および A9 インスタンスを使用して Azure Batch で MPI アプリケーションを実行する方法については、「[Azure Batch でのマルチインスタンス タスクを使用した Message Passing Interface (MPI) アプリケーションの実行](../batch/batch-mpi.md)」を参照してください。

<!---HONumber=AcomDC_0323_2016-->