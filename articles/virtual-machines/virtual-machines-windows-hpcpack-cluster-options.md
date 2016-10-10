<properties
 pageTitle="クラウドでの Windows HPC Pack クラスター オプション | Microsoft Azure"
 description="Microsoft HPC Pack を使用して Azure クラウドで Windows ハイ パフォーマンス コンピューティング (HPC) クラスターを作成および管理するためのオプションについて学習します。"
 services="virtual-machines-windows,cloud-services,batch"
 documentationCenter=""
 authors="dlepow"
 manager="timlt"
 editor=""
 tags="azure-resource-manager,azure-service-management,hpc-pack"/>
<tags
ms.service="virtual-machines-windows"
 ms.devlang="na"
 ms.topic="article"
 ms.tgt_pltfrm="vm-windows"
 ms.workload="big-compute"
 ms.date="09/26/2016"
 ms.author="danlep"/>

# Azure で Windows HPC クラスターを作成して管理するために使用する HPC Pack のオプション

[AZURE.INCLUDE [virtual-machines-common-hpcpack-cluster-options](../../includes/virtual-machines-common-hpcpack-cluster-options.md)]

この記事では、Windows ワークロードの実行に使用する HPC Pack クラスターを作成するためのオプションについて説明します。[HPC Pack によって Linux HPC ワークロード](virtual-machines-linux-hpcpack-cluster-options.md)を実行するのに使用するクラスターを作成するためのオプションもあります。


## Azure VM での HPC Pack クラスターの実行

### Azure テンプレート

* (Marketplace) [HPC Pack cluster for Windows workloads](https://azure.microsoft.com/marketplace/partners/microsofthpc/newclusterwindowscn/)

* (Marketplace) [HPC Pack cluster for Excel workloads](https://azure.microsoft.com/marketplace/partners/microsofthpc/newclusterexcelcn/)

* (クイックスタート) [HPC クラスターを作成する](https://github.com/Azure/azure-quickstart-templates/tree/master/create-hpc-cluster)

* (クイックスタート) [Create an HPC cluster with custom compute node image (カスタム コンピューティング ノード イメージを使用した HPC クラスターの作成)](https://github.com/Azure/azure-quickstart-templates/tree/master/create-hpc-cluster-custom-image)

### Azure VM イメージ

* [Windows Server 2012 R2 上の HPC Pack](https://azure.microsoft.com/marketplace/partners/microsoft/hpcpack2012r2onwindowsserver2012r2/)

* [Windows Server 2012 R2 上の HPC Pack コンピューティング ノード](https://azure.microsoft.com/marketplace/partners/microsoft/hpcpack2012r2computenodeonwindowsserver2012r2/)

* [Windows Server 2012 R2 上の HPC Pack コンピューティング ノードと Excel](https://azure.microsoft.com/marketplace/partners/microsoft/hpcpack2012r2computenodewithexcelonwindowsserver2012r2/)



### PowerShell デプロイメント スクリプト

* [Create an HPC cluster with the HPC Pack IaaS deployment script (HPC Pack IaaS デプロイメント スクリプトを使用した HPC クラスターの作成)](virtual-machines-windows-classic-hpcpack-cluster-powershell-script.md)

### Tutorials (チュートリアル)

* [チュートリアル: Azure で HPC Pack クラスターを開始して Excel と SOA ワークロードを実行する](virtual-machines-windows-excel-cluster-hpcpack.md)



### Azure ポータルを使用した手動デプロイメント

* [Set up the head node of an HPC Pack cluster in an Azure VM (Azure VM での HPC Pack クラスターのヘッド ノードのセットアップ)](virtual-machines-windows-hpcpack-cluster-headnode.md)

### クラスターの管理

* [Manage compute nodes in an HPC Pack cluster in Azure (Azure の HPC Pack クラスターでコンピューティング ノードを管理する)](virtual-machines-windows-classic-hpcpack-cluster-node-manage.md)

* [Grow and shrink Azure compute resources in an HPC Pack cluster (HPC Pack クラスターでの Azure コンピューティング リソースの増減)](virtual-machines-windows-classic-hpcpack-cluster-node-autogrowshrink.md)

* [Submit jobs to an HPC Pack cluster in Azure (Azure の HPC Pack クラスターにジョブを送信する)](virtual-machines-windows-hpcpack-cluster-submit-jobs.md)

* [HPC Pack でのジョブ管理](https://technet.microsoft.com/library/jj899585.aspx)


## HPC Pack クラスターへの worker ロールの追加


* [HPC Pack を使用した Azure ワーカー インスタンスへのバースト](https://technet.microsoft.com/library/gg481749.aspx)

* [Azure で HPC Pack を使用してハイブリッド クラスターをセットアップする](../cloud-services/cloud-services-setup-hybrid-hpcpack-cluster.md)

* [Add Azure "burst" nodes to an HPC Pack head node in Azure (Azure の HPC Pack ヘッド ノードに Azure "バースト" ノードを追加する)](virtual-machines-windows-classic-hpcpack-cluster-node-burst.md)


## Azure Batch との統合 

* [HPC Pack を使用した Azure Batch へのバースト](https://technet.microsoft.com/library/mt612877.aspx)

## MPI ワークロードのための RDMA のクラスターの作成

* [Set up a Windows RDMA cluster with HPC Pack to run MPI applications (HPC Pack を使用して Windows RDMA クラスターをセットアップして MPI アプリケーションを実行する)](virtual-machines-windows-classic-hpcpack-rdma-cluster.md)

<!---HONumber=AcomDC_0928_2016-->