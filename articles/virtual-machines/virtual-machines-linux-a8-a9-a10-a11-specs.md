<properties
 pageTitle="A8 ～ A11 インスタンスと Linux について | Microsoft Azure"
 description="Linux VM の Azure A8、A9、A10、A11 という多くのコンピューティング処理を要するサイズの背景情報および使用上の注意事項について説明します。"
 services="virtual-machines-linux"
 documentationCenter=""
 authors="dlepow"
 manager="timlt"
 editor=""
 tags="azure-resource-manager,azure-service-management"/>
<tags
ms.service="virtual-machines-linux"
 ms.devlang="na"
 ms.topic="article"
 ms.tgt_pltfrm="vm-linux"
 ms.workload="infrastructure-services"
 ms.date="01/26/2016"
 ms.author="danlep"/>

# Linux での A8、A9、A10、A11 コンピューティング集中型インスタンスの使用について

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]

[AZURE.INCLUDE [virtual-machines-common-a8-a9-a10-a11-specs](../../includes/virtual-machines-common-a8-a9-a10-a11-specs.md)]

## RDMA ネットワークへのアクセス

単一のクラウド サービスまたは可用性セット内では、A8 および A9 インスタンスは Azure の RDMA ネットワークにアクセスして Linux MPI アプリケーションを実行できます。現時点で、[Intel MPI Library 5](https://software.intel.com/ja-JP/intel-mpi-library/) では Azure Linux RDMA のみがサポートされています。

>[AZURE.NOTE] 現在、Azure Linux RDMA ドライバーは、ドライバー拡張機能を介したインストールでは使用できません。これを使用する唯一の方法は、Azure Marketplace の RDMA 対応 SLES 12 イメージを使用することです。

以下の表に、Linux MPI アプリケーションがコンピューティング ノード (IaaS) のクラスターの RDMA ネットワークにアクセスするための前提条件をまとめます。デプロイメントのオプションと構成手順については、「[MPI アプリケーションを実行するように Linux RDMA クラスターを設定する](virtual-machines-linux-classic-rdma-cluster.md)」を参照してください。

前提条件 | 仮想マシン (IaaS)
------------ | -------------
オペレーティング システム | Azure Marketplace から入手した SLES 12 HPC イメージ
MPI | Intel MPI Library 5

## HPC Pack および Linux を使用する場合の考慮事項

[HPC Pack](https://technet.microsoft.com/library/jj899572.aspx) は、Windows 向け Microsoft の無料の HPC クラスターおよびジョブ管理ソリューションです。最新リリースの HPC Pack 2012 R2 では、Azure VM にデプロイされ、Windows Server ヘッド ノードで管理されるコンピューティング ノードで、複数の Linux ディストリビューションを実行できるようになりました。A8 または A9 の VM でデプロイされており、サポートされている MPI 実装を実行している Linux コンピューティング ノードでは、RDMA ネットワークにアクセスする MPI アプリケーションを実行できます。作業を開始するには、「[チュートリアル: Azure の HPC Pack クラスターで Linux コンピューティング ノードの使用を開始する](virtual-machines-linux-classic-hpcpack-cluster.md)」を参照してください。

## 次のステップ

* A8、A9、A10、および A11 インスタンスの可用性と価格の詳細については、「[Virtual Machines の料金](https://azure.microsoft.com/pricing/details/virtual-machines/)」を参照してください。

* Linux 上の RDMA により A8 および A9 インスタンスのデプロイと使用を開始する方法については、「[Set up a Linux RDMA cluster to run MPI applications (Linux RDMA クラスターをセットアップして MPI アプリケーションを実行する)](virtual-machines-linux-classic-rdma-cluster.md)」を参照してください。

<!---HONumber=AcomDC_0323_2016-->