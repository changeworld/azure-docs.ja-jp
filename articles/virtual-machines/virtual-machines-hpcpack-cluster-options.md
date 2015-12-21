<properties
 pageTitle="クラウドでの HPC Pack クラスター オプション | Microsoft Azure"
 description="Microsoft HPC Pack を使用して Azure クラウドでハイ パフォーマンス コンピューティング (HPC) クラスターを作成および管理するためのオプションについて学習します。"
 services="virtual-machines,cloud-services"
 documentationCenter=""
 authors="dlepow"
 manager="timlt"
 editor=""
 tags="azure-resource-manager,azure-service-management,hpc-pack"/>
<tags
ms.service="virtual-machines"
 ms.devlang="na"
 ms.topic="article"
 ms.tgt_pltfrm="vm-multiple"
 ms.workload="big-compute"
 ms.date="10/08/2015"
 ms.author="danlep"/>

# Microsoft HPC Pack を使用して Azure でハイパフォーマンス コンピューティング (HPC) クラスターを作成および管理するためのオプション

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]


Microsoft HPC Pack と、Azure のコンピューティングおよびインフラストラクチャ サービスを使用して、クラウド ベースのハイ パフォーマンス コンピューティング (HPC) クラスターを作成および管理します。[HPC Pack](https://technet.microsoft.com/library/jj899572.aspx) は、Microsoft Azure と Windows Server テクノロジに基づいて構築された、Microsoft の無料 HPC ソリューションであり、Windows と Linux HPC の両方のワークロードをサポートしています。クラウド ベースの HPC Pack クラスターには、クラスター管理者や独立系ソフトウェア ベンダー (ISV) がオンプレミスのコンピューティング クラスター インフラストラクチャへの投資を減らしながら、コンピューティング集約型アプリケーションを実行できる、柔軟でスケーラブルなプラットフォームが用意されています。


## Azure VM での HPC Pack クラスターの実行


### Azure VM イメージ

* [Windows Server 2012 R2 上の HPC Pack](http://azure.microsoft.com/marketplace/partners/microsoft/hpcpack2012r2onwindowsserver2012r2/)

* [Windows Server 2012 R2 上の HPC Pack コンピューティング ノード](http://azure.microsoft.com/marketplace/partners/microsoft/hpcpack2012r2computenodeonwindowsserver2012r2/)

* [Windows Server 2012 R2 上の HPC Pack コンピューティング ノードと Excel](http://azure.microsoft.com/marketplace/partners/microsoft/hpcpack2012r2computenodewithexcelonwindowsserver2012r2/)

### Azure クイック スタート テンプレート

* [HPC クラスターを作成する](https://azure.microsoft.com/documentation/templates/create-hpc-cluster/)

* [Linux コンピューティング ノードがある HPC クラスターを作成する](https://azure.microsoft.com/documentation/templates/create-hpc-cluster-linux-cn/)

* [Create an HPC cluster with custom compute node image (カスタム コンピューティング ノード イメージを使用した HPC クラスターの作成)](https://azure.microsoft.com/documentation/templates/create-hpc-cluster-custom-image/)

### PowerShell デプロイメント スクリプト

* [Create an HPC cluster with the HPC Pack IaaS deployment script (HPC Pack IaaS デプロイメント スクリプトを使用した HPC クラスターの作成)](virtual-machines-hpcpack-cluster-powershell-script.md)

### Tutorials (チュートリアル)

* [チュートリアル: Azure の HPC Pack クラスターで Linux コンピューティング ノードの使用を開始する](virtual-machines-linux-cluster-hpcpack.md)

* [チュートリアル: Azure の Linux コンピューティング ノード上で Microsoft HPC Pack を使用して NAMD を実行する](virtual-machines-linux-cluster-hpcpack-namd.md)

* [チュートリアル: Azure の Linux RDMA クラスター上で Microsoft HPC Pack を使用して OpenFOAM を実行する](virtual-machines-linux-cluster-hpcpack-openfoam.md)

* [チュートリアル: Azure で HPC Pack クラスターを開始して Excel と SOA ワークロードを実行する](virtual-machines-excel-cluster-hpcpack.md)



### Azure クラシック ポータルを使用した手動デプロイメント



* [Set up the head node of an HPC Pack cluster in an Azure VM (Azure VM での HPC Pack クラスターのヘッド ノードのセットアップ)](virtual-machines-hpcpack-cluster-headnode.md)

### クラスターの管理

* [Manage compute nodes in an HPC Pack cluster in Azure (Azure の HPC Pack クラスターでコンピューティング ノードを管理する)](virtual-machines-hpcpack-cluster-node-manage.md)

* [Add Azure "burst" nodes to an HPC Pack head node in Azure (Azure の HPC Pack ヘッド ノードに Azure "バースト" ノードを追加する)](virtual-machines-hpcpack-cluster-node-burst.md)

* [Grow and shrink Azure compute resources in an HPC Pack cluster (HPC Pack クラスターでの Azure コンピューティング リソースの増減)](virtual-machines-hpcpack-cluster-node-autogrowshrink.md)

* [Submit jobs to an HPC Pack cluster in Azure (Azure の HPC Pack クラスターにジョブを送信する)](virtual-machines-hpcpack-cluster-submit-jobs.md)


## HPC Pack クラスターへの worker ロールの追加


* [HPC Pack を使用した Azure ワーカー インスタンスへのバースト](https://technet.microsoft.com/library/gg481749.aspx)

* [Azure で HPC Pack を使用してハイブリッド クラスターをセットアップする](../cloud-services/cloud-services-setup-hybrid-hpcpack-cluster.md)

* [Add Azure "burst" nodes to an HPC Pack head node in Azure (Azure の HPC Pack ヘッド ノードに Azure "バースト" ノードを追加する)](virtual-machines-hpcpack-cluster-node-burst.md)

* [Grow and shrink Azure compute resources in an HPC Pack cluster (HPC Pack クラスターでの Azure コンピューティング リソースの増減)](virtual-machines-hpcpack-cluster-node-autogrowshrink.md)

## Azure Batch との統合 

* [HPC Pack を使用した Azure Batch へのバースト](https://technet.microsoft.com/library/mt612877.aspx)

## MPI ワークロードのための RDMA のクラスターの作成

* [Set up a Windows RDMA cluster with HPC Pack to run MPI applications (HPC Pack を使用して Windows RDMA クラスターをセットアップして MPI アプリケーションを実行する)](virtual-machines-windows-hpcpack-cluster-rdma.md)

* [チュートリアル: Azure の Linux RDMA クラスター上で Microsoft HPC Pack を使用して OpenFOAM を実行する](virtual-machines-linux-cluster-hpcpack-openfoam.md)

<!-- * [Set up a Linux RDMA cluster to run MPI applications](virtual-machines-linux-hpcpack-cluster-rdma.md) -->

<!---HONumber=AcomDC_1210_2015-->