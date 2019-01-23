---
title: インクルード ファイル
description: インクルード ファイル
services: virtual-machines-linux, virtual-machines-windows
author: dlepow
ms.service: multiple
ms.topic: include
ms.date: 01/15/2019
ms.author: danlep
ms.custom: include file
ms.openlocfilehash: 2a2d5f9c85a881dad8f53f81a39c84d4a34b632e
ms.sourcegitcommit: a408b0e5551893e485fa78cd7aa91956197b5018
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/17/2019
ms.locfileid: "54360192"
---
さまざまな組織が、大規模なコンピューティングに対するニーズを抱えています。 大規模なコンピューティングを必要とするワークロードとしては、エンジニアリング設計と分析、金融分野のリスク計算、画像レンダリング、複雑なモデリング、モンテカルロ シミュレーションなどが挙げられます。 

Azure クラウドを使用して、並列バッチ ジョブから従来の HPC シミュレーションに至るまで、コンピューティングが大量に発生する Linux ワークロードおよび Windows ワークロードを効率的に実行できます。 任意のコンピューティング サービス、グリッド マネージャー、Marketplace のソリューション、またはベンダーがホストする (SaaS) アプリケーションを選んで、Azure のインフラストラクチャ上で HPC ワークロードやバッチ ワークロードを実行します。 Azure の柔軟なソリューションにより、作業を分散して何千もの VM やコアにスケールし、必要なリソースが減ったときにスケール ダウンするといったことが可能です。 

## <a name="solution-options"></a>ソリューションのオプション

* **お客様ご自身によるソリューション**
    * Azure 仮想マシンまたは[仮想マシンのスケール セット](../articles/virtual-machine-scale-sets/virtual-machine-scale-sets-overview.md)に独自のクラスター環境を設定します。 
    * オンプレミスのクラスターをリフト アンドシフトするか、容量を増やしたい場合は Azure に新しいクラスターをデプロイします。 
    * Azure Resource Manager テンプレートを使って、業界をリードする[ワークロード マネージャー](#workload-managers)、インフラストラクチャ、および[アプリケーション](#hpc-applications)をデプロイします。 
    * [HPC および GPU の VM サイズ](#hpc-and-gpu-sizes)を選択します。これには、MPI ワークロードまたは GPU ワークロードのための特別なハードウェアとネットワーク接続が含まれます。 
    * I/O が集中するワークロードのための[高性能ストレージ](#hpc-storage)を追加します。
* **ハイブリッド ソリューション**
    * オンプレミスのソリューションを拡張し、Azure インフラストラクチャに対するピーク時のワークロードを軽減 ("バースト") します。
    * 既存の[ワークロード マネージャー](#workload-manager)とともにクラウド コンピューティングをオンデマンドで使用します。
    * MPI ワークロードまたは GPU ワークロードのために [HPC および GPU の VM サイズ](#hpc-and-gpu-sizes)を利用します。
* **サービスとしての大規模なコンピューティング ソリューション**
    * [Azure CycleCloud](#azure-cyclecloud)、[Azure Batch](#azure-batch)、および関連する [Azure サービス](#related-azure-services)を使用して、カスタムの大規模コンピューティング ソリューションとワークフローを開発します。
    * [Altair](http://www.altair.com/)、[Rescale](https://www.rescale.com/azure/)、[Cycle Computing](https://cyclecomputing.com/) (現在は [Microsoft と統合](https://blogs.microsoft.com/blog/2017/08/15/microsoft-acquires-cycle-computing-accelerate-big-computing-cloud/)) などのベンダーが提供する、Azure 対応のエンジニアリングおよびシミュレーション ソリューションを実行します。
    * Azure でホストされるサービスとして [Cray スーパー コンピューター](https://www.cray.com/solutions/supercomputing-as-a-service/cray-in-azure)を使用します。
* **Marketplace のソリューション**
    * [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/) で提供される大規模な [HPC アプリケーション](#hpc-applications)や[ソリューション](#marketplace-solutions)を使用します。 
    
次のセクションでは、サポートされる技術の詳細とガイドへのリンクを提供します。

## <a name="marketplace-solutions"></a>Marketplace のソリューション

Linux と Windows の HPC 用 VM イメージとソリューションについては、[Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/) を参照してください。 たとえば、次のようになります。

* [RogueWave CentOS-based HPC](https://azuremarketplace.microsoft.com/marketplace/apps/RogueWave.CentOSbased74HPC?tab=Overview)
* [SUSE Linux Enterprise Server for HPC](https://azure.microsoft.com/marketplace/partners/suse/suselinuxenterpriseserver12optimizedforhighperformancecompute/)
*  [TIBCO Grid Server Engine](https://azuremarketplace.microsoft.com/marketplace/apps/tibco-software.gridserverlinuxengine?tab=Overview)
* [Windows および Linux 用 Azure データ サイエンス VM ](../articles/machine-learning/machine-learning-data-science-virtual-machine-overview.md)
* [D3View](https://azuremarketplace.microsoft.com/marketplace/apps/xfinityinc.d3view-v5?tab=Overview)
* [UberCloud](https://azure.microsoft.com/search/marketplace/?q=ubercloud)

## <a name="hpc-applications"></a>HPC アプリケーション

Azure ではカスタム HPC アプリケーションや商用 HPC アプリケーションを実行できます。 このセクションのさまざまな例で、VM やコンピューティング コアを追加して効率的にスケールできることが確認されています。 すぐにデプロイ可能なソリューションについては、[Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace) を参照してください。

> [!NOTE]
> クラウドで実行するためのライセンスまたはその他の制限事項については、商用アプリケーションのベンダーに確認してください。 すべてのベンダーが従量課金制ライセンスを提供しているとは限りません。 ソリューション用にクラウド内にライセンス サーバーを用意したり、オンプレミスのライセンス サーバーに接続することが必要になる場合があります。

### <a name="engineering-applications"></a>エンジニアリング アプリケーション

* [Altair RADIOSS](https://azure.microsoft.com/blog/availability-of-altair-radioss-rdma-on-microsoft-azure/)
* [ANSYS CFD](https://azure.microsoft.com/blog/ansys-cfd-and-microsoft-azure-perform-the-best-hpc-scalability-in-the-cloud/)
* [MATLAB Distributed Computing Server](../articles/virtual-machines/windows/matlab-mdcs-cluster.md)
* [StarCCM+](https://blogs.msdn.microsoft.com/azurecat/2017/07/07/run-star-ccm-in-an-azure-hpc-cluster/)
* [OpenFOAM](https://simulation.azure.com/casestudies/Team-182-ABB-UC-Final.pdf)

### <a name="graphics-and-rendering"></a>グラフィックとレンダリング

* Autodesk Maya、3ds Max、Arnold、Chaos Group V-Ray、Blender を含む Azure Batch での[アプリケーションのレンダリング](../articles/batch/batch-rendering-service.md)

### <a name="ai-and-deep-learning"></a>AI とディープ ラーニング

* [Microsoft Cognitive Toolkit](https://docs.microsoft.com/cognitive-toolkit/cntk-on-azure)
* [ディープ ラーニング VM](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-ads.dsvm-deep-learning)
* [ディープ ラーニング用の Batch Shipyard レシピ](https://github.com/Azure/batch-shipyard/tree/master/recipes#deeplearning)

## <a name="hpc-and-gpu-vm-sizes"></a>HPC と GPU の VM サイズ

Azure ではコンピューティングが大量に発生するワークロードに適したものも含め、[Linux](../articles/virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) VM と [Windows](../articles/virtual-machines/windows/sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) VM のために幅広い VM サイズを用意しています。 たとえば、H16r VM と H16mr VM であれば、高スループットのバックエンド RDMA ネットワークに接続できます。 このクラウド ネットワークでは、[Microsoft MPI](https://msdn.microsoft.com/library/bb524831.aspx) または Intel MPI の下で実行される緊密に結合した並列アプリケーションのパフォーマンスを高めることができます。 

N シリーズ VM は、人工知能 (AI) の学習や視覚化などによりコンピューティングやグラフィック使用量が多いアプリケーションのために設計された NVIDIA GPU を採用しています。 

詳細情報:

* [Linux](../articles/virtual-machines/linux/sizes-hpc.md) VM と [Windows](../articles/virtual-machines/windows/sizes-hpc.md) VM のハイ パフォーマンス コンピューティング サイズ 
* [Linux](../articles/virtual-machines/linux/sizes-gpu.md) VM と [Windows](../articles/virtual-machines/windows/sizes-gpu.md) VM の GPU 対応サイズ 

## <a name="azure-cyclecloud"></a>Azure CycleCloud

[Azure CycleCloud](https://docs.microsoft.com/azure/cyclecloud/overview) で Azure VM 上の HPC クラスターを作成および最適化すると同時に、一般的なワークロードを簡単かつ効率的に管理できます。

以下の項目について説明します。

* [Resource Manager テンプレートで CycleCloud をインストールおよびセットアップする](https://docs.microsoft.com/azure/cyclecloud/quickstart-install-cyclecloud)

* [CycleCloud を手動でセットアップする](https://docs.microsoft.com/azure/cyclecloud/quickstart-installation)

## <a name="azure-batch"></a>Azure Batch

[Batch](../articles/batch/batch-technical-overview.md) は、大規模な並列コンピューティングやハイ パフォーマンス コンピューティング (HPC) のアプリケーションをクラウドで効率的に実行するためのプラットフォーム サービスです。 大量のコンピューティングを要する作業を仮想マシンの管理されたプールで実行するようにスケジュールを設定し、ジョブのニーズに合わせてコンピューティング リソースを自動的にスケールできます。 

SaaS のプロバイダーやデベロッパーは、Batch の SDK とツールを使って、HPC アプリケーションやコンテナー ワークロードを Azure に統合し、Azure にデータをステージングして、ジョブ実行パイプラインを作成できます。 

以下の項目について説明します。

* [Batch を使った初めての開発](../articles/batch/quick-run-dotnet.md)
* [Azure Batch のコード サンプルを使用する](https://github.com/Azure/azure-batch-samples)
* [Batch で優先順位の低い VM を使用する](../articles/batch/batch-low-pri-vms.md)
* [Batch Shipyard を使ってコンテナー化した HPC ワークロードを実行する](https://github.com/Azure/batch-shipyard)
* [Batch で並列 R ワークロードを実行する](https://github.com/Azure/doAzureParallel)
* [Batch でオンデマンド Spark ジョブを実行する](https://github.com/Azure/aztk)

## <a name="workload-managers"></a>ワークロード マネージャー

Azure のインフラストラクチャで実行できるクラスターおよびワークロード マネージャーの例を次に示します。 Azure VM にスタンドアロンのクラスターを作成するか、オンプレミス クラスターから Azure VM にバーストします。 
* [Alces フライト コンピューティング](https://azuremarketplace.microsoft.com/marketplace/apps/alces-flight-limited.alces-flight-compute-solo?tab=Overview)
* [TIBCO DataSynapse GridServer](https://azure.microsoft.com/blog/tibco-datasynapse-comes-to-the-azure-marketplace/) 
* [Bright Cluster Manager](http://www.brightcomputing.com/technology-partners/microsoft)
* [IBM Spectrum Symphony および Symphony LSF](https://azure.microsoft.com/blog/ibm-and-microsoft-azure-support-spectrum-symphony-and-spectrum-lsf/)
* [PBS Pro](http://pbspro.org)
* [Microsoft HPC Pack](https://docs.microsoft.com/powershell/high-performance-computing/overview) 

## <a name="hpc-storage"></a>HPC ストレージ

バッチ ワークロードや HPC ワークロードが大規模の場合には、従来のクラウド ファイル システムの容量を上回るデータ ストレージが必要になったり、データ アクセスが発生したりします。 

詳細情報:

* [Avere vFXT for Azure](../articles/avere-vfxt/avere-vfxt-overview.md) 
* [Lustre](http://lustre.org/) と [BeeGFS](http://www.beegfs.com/content/) を含む [Azure 上の並列仮想ファイル システム](https://azure.microsoft.com/resources/parallel-virtual-file-systems-on-microsoft-azure/)。

## <a name="related-azure-services"></a>関連する Azure サービス

Azure の仮想マシン、仮想マシン スケール セット、Batch、およびこれらに関連するコンピューティング サービスは、Azure のほとんどの HPC ソリューションの基盤となっています。 もっとも、お使いのソリューションでも Azure の関連サービスの多くを利用できます。 その一部を以下に紹介します。

### <a name="storage"></a>Storage

* [Blob、Table、Queue Storage](../articles/storage/storage-introduction.md)
* [File Storage](../articles/storage/storage-files-introduction.md)

### <a name="data-and-analytics"></a>データと分析

* [HDInsight](../articles/hdinsight/hadoop/apache-hadoop-introduction.md)
* [Data Factory](../articles/data-factory/introduction.md)
* [Data Lake Storage Gen1](../articles/data-lake-store/data-lake-store-overview.md)
* [Databricks](../articles/azure-databricks/what-is-azure-databricks.md)
* [SQL Database](../articles/sql-database/sql-database-technical-overview.md)

### <a name="ai-and-machine-learning"></a>AI と機械学習

* [Machine Learning service](../articles/machine-learning/service/overview-what-is-azure-ml.md)
* [Genomics](../articles/genomics/overview-what-is-genomics.md)

### <a name="networking"></a>ネットワーク

* [Virtual Network](../articles/virtual-network/virtual-networks-overview.md)
* [ExpressRoute](../articles/expressroute/expressroute-introduction.md)

### <a name="containers"></a>Containers

* [Azure Kubernetes Service (AKS)](../articles/aks/intro-kubernetes.md)
* [コンテナー レジストリ](../articles/container-registry/container-registry-intro.md)
* [Container Instances](../articles/container-instances/container-instances-overview.md)

## <a name="customer-stories"></a>顧客事例

Azure HPC ソリューションを使ってビジネスの問題を解決したお客様の例:

* [ANEO](https://customers.microsoft.com/story/it-provider-finds-highly-scalable-cloud-based-hpc-redu) 
* [AXA Global P&C](https://customers.microsoft.com/story/axa-global-p-and-c)
* [Axioma](https://customers.microsoft.com/story/axioma-delivers-fintechs-first-born-in-the-cloud-multi-asset-class-enterprise-risk-solution)
* [d3View](https://customers.microsoft.com/story/big-data-solution-provider-adopts-new-cloud-gains-thou)
* [EFS](https://customers.microsoft.com/story/efs-professionalservices-azure)
* [Hymans Robertson](https://customers.microsoft.com/story/hymans-robertson)
* [MetLife](https://customers.microsoft.com/story/metlife-insurance-azure-cloud-services-windows-server-analytics-platform-server-en)
* [Microsoft Research](https://customers.microsoft.com/doclink/fast-lmm-and-windows-azure-put-genetics-research-on-fa)
* [Milliman](https://customers.microsoft.com/story/actuarial-firm-works-to-transform-insurance-industry-w)
* [Mitsubishi UFJ Securities International](https://customers.microsoft.com/story/powering-risk-compute-grids-in-the-cloud)
* [NeuroInitiative](https://customers.microsoft.com/story/neuroinitiative-health-provider-azure)
* [Schlumberger](https://azure.microsoft.com/blog/big-compute-for-large-engineering-simulations)
* [Towers Watson](https://customers.microsoft.com/story/insurance-tech-provider-delivers-disruptive-solutions)

## <a name="next-steps"></a>次の手順

* [高パフォーマンス コンピューティング](https://azure.microsoft.com/solutions/high-performance-computing/)、[レンダリング](https://azure.microsoft.com/solutions/big-compute/rendering/)、[銀行業および資本市場](https://finance.azure.com/)、および[ゲノミクス](https://enterprise.microsoft.com/industries/health/genomics/)向けの Azure ソリューションについて詳しく学習します。

* [ビッグ コンピューティング ソリューションのアーキテクチャ](https://azure.microsoft.com/solutions/architecture/?solution=high-performance-computing)について詳しく学習します。
