さまざまな組織が、大規模なコンピューティングに対するニーズを抱えています。 大規模なコンピューティングを必要とするワークロードとしては、エンジニアリング設計と分析、金融分野のリスク計算、画像レンダリング、複雑なモデリング、モンテカルロ シミュレーションなどが挙げられます。 

Azure を使えば、Azure のインフラストラクチャ サービスや管理されたサービスのなかから好きなものを選んで、Azure 仮想マシンのクラスター上でハイ パフォーマンス コンピューティング (HPC) ワークロードやバッチ ワークロードを実行できます。 Azure ではコンピューティング リソースを何千もの VM やコアにまでスケールしたり、必要なリソースが減った時点でスケールダウンしたりできるため、さまざまな状況に柔軟に対応できます。 

Azure の HPC ソリューションでは、並列バッチ ジョブから従来の HPC シミュレーションに至るまで、コンピューティングが大量に発生する Linux ワークロードおよび Windows ワークロードを効率的に実行できます。 Azure VM では主要な HPC アプリケーションの実行をサポートしています。また、コンピューティングが大量に発生するジョブ専用に設計された VM サイズとイメージを利用できます。 


## <a name="solution-architectures"></a>ソリューションのアーキテクチャ

Azure のコンピューティング集中型ソリューションのアーキテクチャのサンプルは以下のとおりです。

* Linux または Windows 向けの任意のクラスター/グリッド マネージャーを使用し、仮想マシンまたは[仮想マシン スケール セット](../articles/virtual-machine-scale-sets/virtual-machine-scale-sets-overview.md)で HPC アプリケーションを実行する ([詳細](https://azure.microsoft.com/en-us/solutions/architecture/hpc-cluster/))
* オンプレミスの HPC クラスターを拡張してピーク時のワークロードを Azure VM にオフロード ("バースト") するハイブリッド ソリューションを作成する ([詳細](https://azure.microsoft.com/en-us/solutions/architecture/hpc-on-prem-burst/))
* 管理されたスケーラブルな Azure [Batch](https://azure.microsoft.com/services/batch/) サービスを使用して、基礎となるインフラストラクチャを管理することなく、コンピューティングが大量に発生するワークロードを実行する ([詳細](https://azure.microsoft.com/en-us/solutions/architecture/hpc-big-compute-saas/))

## <a name="hpc-cluster-and-grid-solutions"></a>HPC クラスター/グリッドのソリューション
使い慣れた HPC ワークロード管理ツールを Azure 仮想マシンにデプロイまたは拡張して、コンピューティングが大量に発生するワークロードを実行できます。 ツールの選択肢としては、[Slurm Workload Manager](https://slurm.schedmd.com/) などのオープンソース ツールのほか、Microsoft などの公開元が提供するクラスター/グリッド マネージャーがあります。

### <a name="linux-and-oss-cluster-solutions"></a>Linux と OSS クラスターのソリューション
仮想マシンまたは仮想マシン スケール セットに以下の Linux HPC クラスター ソリューションをデプロイするには、Azure Resource Manager テンプレートを使用します。

* [Slurm](https://azure.microsoft.com/documentation/templates/slurm/)
* [Torque](https://azure.microsoft.com/documentation/templates/torque-cluster/)
* [PBS Professional](https://github.com/xpillons/azure-hpc/tree/master/Compute-Grid-Infra)

こちらで[5 クリック テンプレート](https://github.com/tanewill/5clickTemplates)のコレクションもご覧ください。

### <a name="grid-managers"></a>グリッド マネージャー
Microsoft はさまざまな商用グリッド マネージャーとパートナーシップを締結し、Azure VM で利用できるソリューションの拡充に努めています。 たとえば、次のようになります。

* [IBM Spectrum Symphony および Symphony LSF](https://azure.microsoft.com/blog/ibm-and-microsoft-azure-support-spectrum-symphony-and-spectrum-lsf/)
* [TIBCO DataSynapse GridServer](https://azure.microsoft.com/blog/tibco-datasynapse-comes-to-the-azure-marketplace/) 
* [Bright Cluster Manager](http://www.brightcomputing.com/technology-partners/microsoft)
* [Univa Grid Engine](http://www.univa.com/products/grid-engine)

### <a name="microsoft-hpc-pack"></a>Microsoft HPC Pack
[HPC Pack](https://technet.microsoft.com/library/cc514029(v=ws.11).aspx) は、Microsoft Azure と Windows Server テクノロジに基づいて構築された Microsoft の無料 HPC ソリューションです。 HPC Pack では、[Windows](../articles/virtual-machines/windows/hpcpack-cluster-options.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) VM と [Linux](../articles/virtual-machines/linux/hpcpack-cluster-options.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) VM で実行できるオプションが幅広く用意されています。 

以下の項目について説明します。

* [Azure に HPC Pack 2016 クラスターを作成する](../articles/virtual-machines/windows/hpcpack-2016-cluster.md)
* [HPC Pack を使用して Azure にバーストする](https://technet.microsoft.com/library/gg481749(v=ws.11).aspx)




## <a name="azure-batch"></a>Azure Batch
[Batch](../articles/batch/batch-technical-overview.md) は、大規模な並列コンピューティングやハイ パフォーマンス コンピューティング (HPC) のアプリケーションをクラウドで効率的に実行するためのプラットフォーム サービスです。 大量のコンピューティングを要する作業を仮想マシンの管理されたプールで実行するようにスケジュールを設定し、ジョブのニーズに合わせてコンピューティング リソースを自動的にスケールできます。 

Batch の SDK とツールを使えば、HPC アプリケーションやコンテナー ワークロードを Azure に統合し、Azure にデータをステージングして、ジョブ実行パイプラインを作成できます。 さらに、余った (優先度の低い) Azure VM の容量を[割引価格](https://azure.microsoft.com/pricing/details/batch/)で利用できるため、一部のワークロードを Batch で実行するコストが大幅に下がります。

以下の項目について説明します。

* [Batch を使った初めての開発](../articles/batch/batch-dotnet-get-started.md)
* [Azure Batch のコード サンプルを使用する](https://github.com/Azure/azure-batch-samples)
* [Batch で優先順位の低い VM を使用する (プレビュー)](../articles/batch/batch-low-pri-vms.md)
* [Batch Shipyard を使ってコンテナー化した HPC ワークロードを実行する](https://github.com/Azure/batch-shipyard)
* [Batch で R 言語を使用する](https://github.com/Azure/doAzureParallel)

## <a name="hpc-and-gpu-vm-sizes"></a>HPC と GPU の VM サイズ
Azure ではコンピューティングが大量に発生するワークロードに適したものも含め、[Linux](../articles/virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) VM と [Windows](../articles/virtual-machines/windows/sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) VM のために幅広い VM サイズを用意しています。 たとえば、H16r VM と H16mr VM であれば、高スループットのバックエンド RDMA ネットワークに接続できます。 このクラウド ネットワークでは、[Microsoft MPI](https://msdn.microsoft.com/library/bb524831.aspx) または Intel MPI の下で実行される緊密に結合した並列アプリケーションのパフォーマンスを高めることができます。 N シリーズ VM は、人工知能 (AI) の学習や視覚化などによりコンピューティングやグラフィック使用量が多いアプリケーションのために設計された NVIDIA GPU を採用しています。 

詳細情報:

* [Linux](../articles/virtual-machines/linux/sizes-hpc.md) VM と [Windows](../articles/virtual-machines/windows/sizes-hpc.md) VM のハイ パフォーマンス コンピューティング サイズ 
* [Linux](../articles/virtual-machines/linux/sizes-gpu.md) VM と [Windows](../articles/virtual-machines/windows/sizes-gpu.md) VM の GPU 対応サイズ 

以下の項目について説明します。

* [MPI アプリケーションを実行するように Linux RDMA クラスターを設定する](../articles/virtual-machines/linux/classic/rdma-cluster.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json)
* [Set up a Windows RDMA cluster with HPC Pack to run MPI applications (HPC Pack を使用して Windows RDMA クラスターをセットアップして MPI アプリケーションを実行する)](../articles/virtual-machines/windows/classic/hpcpack-rdma-cluster.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)
* [Batch プールでコンピューティング集中型 VM を使用する](../articles/batch/batch-pool-compute-intensive-sizes.md)

## <a name="hpc-images"></a>HPC イメージ

Linux と Windows の HPC 用 VM イメージについては、[Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/) を参照してください。 たとえば、次のようになります。

* [RogueWave CentOS-based HPC](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/RogueWave.CentOSbased73HPC?tab=Overview)
* [SUSE Linux Enterprise Server for HPC](https://azure.microsoft.com/marketplace/partners/suse/suselinuxenterpriseserver12optimizedforhighperformancecompute/)
* [TIBCO Grid Server 6.2.0 Engine](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/tibco-software.gridserverlinuxengine?tab=Overview)
* [Windows および Linux 用 Azure データ サイエンス VM ](../articles/machine-learning/machine-learning-data-science-virtual-machine-overview.md)


 
## <a name="hpc-applications"></a>HPC アプリケーション

Azure ではカスタム HPC アプリケーションや商用 HPC アプリケーションを実行できます。 さまざまなアプリケーションで、VM やコンピューティング コアを追加して効率的にスケールできることが確認されています。 以下のセクションにその例を示します。

> [!IMPORTANT]
> クラウドで実行するためのライセンスまたはその他の制限事項については、商用アプリケーションのベンダーに確認してください。 すべてのベンダーが従量課金制ライセンスを提供しているとは限りません。 ソリューション用にクラウド内にライセンス サーバーを用意したり、オンプレミスのライセンス サーバーに接続することが必要になる場合があります。

### <a name="engineering-applications"></a>エンジニアリング アプリケーション


* [Altair RADIOSS](https://azure.microsoft.com/blog/availability-of-altair-radioss-rdma-on-microsoft-azure/)
* [ANSYS CFD](https://azure.microsoft.com/blog/ansys-cfd-and-microsoft-azure-perform-the-best-hpc-scalability-in-the-cloud/)
* [MATLAB Distributed Computing Server](../articles/virtual-machines/windows/matlab-mdcs-cluster.md)
* [StarCCM+](https://blogs.msdn.microsoft.com/azurecat/2017/07/07/run-star-ccm-in-an-azure-hpc-cluster/)
* [OpenFOAM](https://simulation.azure.com/casestudies/Team-182-ABB-UC-Final.pdf)



### <a name="graphics-and-rendering"></a>グラフィックとレンダリング

* Azure Batch での [Autodesk Maya、3ds Max、Arnold](../articles/batch/batch-rendering-service.md) (プレビュー)

### <a name="ai-and-deep-learning"></a>AI とディープ ラーニング

* [Microsoft Cognitive Toolkit](https://docs.microsoft.com/cognitive-toolkit/cntk-on-azure)
* [Deep learning toolkit for Data Science VM](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-ads.dsvm-deep-learning)
* [ディープ ラーニング用の Batch Shipyard レシピ](https://github.com/Azure/batch-shipyard/tree/master/recipes#deeplearning)

## <a name="solution-partners"></a>ソリューション パートナー

Azure で利用できるバッチ アプリケーションと HPC アプリケーションを開発または提供しているパートナーの例を以下に示します。

* [Cycle Computing](https://cyclecomputing.com/) (現在は [Microsoft が買収](https://blogs.microsoft.com/blog/2017/08/15/microsoft-acquires-cycle-computing-accelerate-big-computing-cloud/))
* [Rescale](https://www.rescale.com/azure/)
* [UberCloud](https://www.theubercloud.com/)

## <a name="hpc-storage"></a>HPC ストレージ

バッチ ワークロードや HPC ワークロードが大規模の場合には、従来のクラウド ファイル システムの容量を上回るデータ ストレージが必要になったり、データ アクセスが発生したりします。 Azure には、[Lustre](http://lustre.org/)、[BeeGFS](http://www.beegfs.com/content/) などの並列ファイル システム ソリューションを実装できます。

詳細情報:

* [Azure での HPC ストレージの並列ファイル システム](https://blogs.msdn.microsoft.com/azurecat/2017/03/17/parallel-file-systems-for-hpc-storage-on-azure/)


## <a name="related-azure-services"></a>関連する Azure サービス

Azure の仮想マシン、仮想マシン スケール セット、Batch、およびこれらに関連するコンピューティング サービスは、Azure のほとんどの HPC ソリューションの基盤となっています。 もっとも、お使いのソリューションでも Azure の関連サービスの多くを利用できます。 その一部を以下に紹介します。

### <a name="storage"></a>Storage

* [Blob、Table、Queue Storage](../articles/storage/storage-introduction.md)
* [File Storage](../articles/storage/storage-files-introduction.md)

### <a name="data-and-analytics"></a>データと分析
* [HDInsight](../articles/hdinsight/hdinsight-hadoop-introduction.md) (Azure 上の Hadoop クラスター用)
* [Data Factory](../articles/data-factory/data-factory-introduction.md)
* [Data Lake Store](../articles/data-lake-store/data-lake-store-overview.md)
* [Machine Learning](../articles/machine-learning/machine-learning-what-is-machine-learning.md)
* [SQL Database](../articles/sql-database/sql-database-technical-overview.md)

### <a name="networking"></a>ネットワーク
* [Virtual Network](../articles/virtual-network/virtual-networks-overview.md)
* [ExpressRoute](../articles/expressroute/expressroute-introduction.md)

### <a name="containers"></a>コンテナー
* [Container Service](../articles/container-service/dcos-swarm/container-service-intro.md)
* [コンテナー レジストリ](../articles/container-registry/container-registry-intro.md)



## <a name="customer-stories"></a>顧客事例

Azure HPC ソリューションを使ってビジネスの問題を解決したお客様の例を以下に紹介します。

* [ANEO](https://customers.microsoft.com/story/it-provider-finds-highly-scalable-cloud-based-hpc-redu) 
* [AXA Global P&C](https://customers.microsoft.com/story/axa-global-p-and-c)
* [Axioma](https://customers.microsoft.com/story/axioma-delivers-fintechs-first-born-in-the-cloud-multi-asset-class-enterprise-risk-solution)
* [d3View](https://customers.microsoft.com/story/big-data-solution-provider-adopts-new-cloud-gains-thou)
* [Hymans Robertson](https://customers.microsoft.com/story/hymans-robertson)
* [MetLife](https://enterprise.microsoft.com/en-us/customer-story/industries/insurance/metlife/)
* [Microsoft Research](https://customers.microsoft.com/doclink/fast-lmm-and-windows-azure-put-genetics-research-on-fa)
* [Milliman](https://customers.microsoft.com/story/actuarial-firm-works-to-transform-insurance-industry-w)
* [Mitsubishi UFJ Securities International](https://customers.microsoft.com/story/powering-risk-compute-grids-in-the-cloud)
* [Schlumberger](http://azure.microsoft.com/blog/big-compute-for-large-engineering-simulations)
* [Towers Watson](https://customers.microsoft.com/story/insurance-tech-provider-delivers-disruptive-solutions)


## <a name="next-steps"></a>次のステップ
* [エンジニアリング シミュレーション](https://simulation.azure.com/)、[レンダリング](https://simulation.azure.com/)、[銀行業および資本市場](https://finance.azure.com/)向けの大規模コンピューティング ソリューションの詳細については、それぞれのトピックを参照してください。
* 最新情報については、[Microsoft HPC と Batch のチーム ブログ](http://blogs.technet.com/b/windowshpc/)と[Azure ブログ](https://azure.microsoft.com/blog/tag/hpc/)をご覧ください。


