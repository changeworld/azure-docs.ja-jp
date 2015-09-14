<properties
   pageTitle="ビッグ コンピューティング: バッチとハイ パフォーマンス コンピューティング (HPC) に関するテクニカル リソース | Microsoft Azure"
	description="Azure で大規模並列、Batch、HPC ワークロードを実行するのに役立つテクニカル リソースの一覧を示します。"
	services="batch, cloud-services, virtual-machines"
	documentationCenter=""
	authors="dlepow"
	manager="timlt"
	editor=""/>

<tags
   ms.service="multiple"
	ms.devlang="multiple"
	ms.topic="article"
	ms.tgt_pltfrm="NA"
	ms.workload="big-compute"
	ms.date="09/02/2015"
	ms.author="danlep"/>

# Azure における大規模なコンピューティング: バッチとハイ パフォーマンス コンピューティング (HPC) に関するテクニカル リソース
これは、Azure で大規模並列、Batch、HPC ワークロードを実行するのに役立つテクニカル リソース ガイドです。既存の Batch または HPC ワークロードを Azure クラウドに拡張するか、新しい大規模なコンピューティング ソリューションを Azure サービスを使用して Azure に構築します。

## ソリューションのオプション

Azure の大規模なコンピューティング オプションについて説明し、ワークロードやビジネス ニーズに適切なアプローチを選択します。

* [Batch と HPC ソリューション](batch-hpc-solutions.md)

* [ビデオ: Azure と HPC を使用したクラウドでの大規模なコンピューティング](http://azure.microsoft.com/documentation/videos/teched-europe-2014-big-compute-in-the-cloud-with-high-performance-computing-on-azure/)


## Azure Batch

[Batch](http://azure.microsoft.com/services/batch/) は、アプリケーションを簡単にクラウド対応にすることができ、クラスターやジョブ スケジューラーをセットアップして管理しなくてもジョブを実行できるプラットフォーム サービスです。SDK を使用して、さまざまな言語を介してクライアント アプリケーションを Azure Batch と統合し、Azure にデータをステージングし、ジョブの実行パイプラインをビルドします。

* [ドキュメント](http://azure.microsoft.com/documentation/services/batch/)

* [API リファレンス](https://msdn.microsoft.com/library/azure/dn820177.aspx)

* [チュートリアル: .NET 向け Azure Batch ライブラリの概要](batch-dotnet-get-started.md)

* [Batch フォーラム](https://social.msdn.microsoft.com/Forums/home?forum=azurebatch)

* [Batch のビデオ](https://azure.microsoft.com/documentation/videos/index/?services=batch)

## HPC クラスターのソリューション

既存の Windows または Linux HPC クラスターを Azure にデプロイまたは拡張して、コンピューティング集中型のワークロードを実行します。

### Microsoft HPC Pack

HPC Pack は、Microsoft Azure と Windows Server テクノロジに基づいて構築された、Microsoft の無料の HPC ソリューションです。

* [HPC Pack 2012 R2 Update 2 をダウンロードする](https://www.microsoft.com/ja-JP/download/details.aspx?id=47755)

* [ドキュメント](https://technet.microsoft.com/library/jj899572.aspx)

* **Azure VM イメージ**

    * [Windows Server 2012 R2 上の HPC Pack](http://azure.microsoft.com/marketplace/partners/microsoft/hpcpack2012r2onwindowsserver2012r2/)

    * [Windows Server 2012 R2 上の HPC Pack コンピューティング ノード](http://azure.microsoft.com/marketplace/partners/microsoft/hpcpack2012r2computenodeonwindowsserver2012r2/)

    * [Windows Server 2012 R2 上の HPC Pack コンピューティング ノードと Excel](http://azure.microsoft.com/marketplace/partners/microsoft/hpcpack2012r2computenodewithexcelonwindowsserver2012r2/)

* **Azure のクイック スタート テンプレートとスクリプト**

    * [HPC クラスターを作成する](https://azure.microsoft.com/documentation/templates/create-hpc-cluster/)

    * [カスタム コンピューティング ノード イメージを使用して HPC クラスターを作成する](https://azure.microsoft.com/documentation/templates/create-hpc-cluster-custom-image/)

    * [Linux コンピューティング ノードがある HPC クラスターを作成する](https://azure.microsoft.com/documentation/templates/create-hpc-cluster-linux-cn/)

    * [HPC Pack IaaS デプロイメント PowerShell スクリプト](https://www.microsoft.com/download/details.aspx?id=44949)

* **Azure チュートリアル**

    * [チュートリアル: Azure の HPC Pack クラスターで Linux コンピューティング ノードの使用を開始する](../virtual-machines/virtual-machines-linux-cluster-hpcpack.md)

    * [チュートリアル: Azure の Linux コンピューティング ノード上で Microsoft HPC Pack を使用して NAMD を実行する](../virtual-machines/virtual-machines-linux-cluster-hpcpack-namd.md)

    * [チュートリアル: Azure で HPC Pack クラスターを開始して Excel と SOA ワークロードを実行する](../virtual-machines/virtual-machines-excel-cluster-hpcpack.md)

    * [Azure で HPC Pack を使用してハイブリッド クラスターをセットアップする](../cloud-services/cloud-services-setup-hybrid-hpcpack-cluster.md)

    * [Azure VM の HPC Pack](https://msdn.microsoft.com/library/azure/dn518135.aspx)

    * [HPC Pack を使用した Azure へのバースト](https://technet.microsoft.com/library/gg481749.aspx)


* [Windows HPC フォーラム](https://social.microsoft.com/Forums/home?category=windowshpc)

### Linux と OSS クラスターのソリューション

これらの Azure クイック スタート テンプレートを使用して、オープン ソース ツールを備えた Linux HPC クラスターをデプロイします。

* [SLURM クラスターの起動](http://azure.microsoft.com/documentation/templates/slurm/)と[ブログの投稿](http://blogs.technet.com/b/windowshpc/archive/2015/06/06/deploy-a-slurm-cluster-on-azure.aspx)

* [トルク クラスターの起動](http://azure.microsoft.com/documentation/templates/torque-cluster/)

## Microsoft MPI

[Microsoft MPI](https://msdn.microsoft.com/library/bb524831.aspx) (MS-MPI) は、Windows プラットフォームで並列アプリケーションを開発して実行するための、Microsoft が実装する標準 Message Passing Interface です。


* [MS-MPI のダウンロード](http://go.microsoft.com/FWLink/p/?LinkID=389556)

* [MS-MPI リファレンス](https://msdn.microsoft.com/library/dn473458.aspx)

* [MPI フォーラム](https://social.microsoft.com/Forums/home?forum=windowshpcmpi)


## コンピューティング集中型インスタンス

Azure では、Linux および Windows の HPC ワークロードを実行するために、コンピューティング集中型の [A8、A9、A10、A11 インスタンス](../virtual-machines/virtual-machines-a8-a9-a10-a11-specs.md)などの[さまざまなサイズ](../virtual-machines/virtual-machines-size-specs.md)が用意されています。

* [MPI アプリケーションを実行するように Linux RDMA クラスターを設定する](../virtual-machines/virtual-machines-linux-cluster-rdma.md)

* [A8 および A9 インスタンス: HPC Pack 使用クイック スタート](https://msdn.microsoft.com/library/azure/dn594431.aspx)

* [A8 および A9 インスタンス上で MPI アプリケーションを実行する](https://msdn.microsoft.com/library/azure/dn592104.aspx)

## アーキテクチャの設計図

* [大規模なコンピューティング - 金融サービス](http://go.microsoft.com/fwlink/?LinkId=536378) (PDF) では、リスク管理、レポート、シミュレーション用のクラウドで大規模なコンピューター処理とデータ分析を運用し、調整する方法を示しています。

## サンプルとデモ

* [Azure Batch のコード サンプル](https://github.com/Azure/azure-batch-samples)

* [Batch Apps Blender のサンプル](https://github.com/Azure/azure-batch-apps-blender)と[ブログの投稿](http://azure.microsoft.com/blog/2015/01/26/blender-on-azure-batch/)

## 関連する Azure サービス

* [Data Factory](http://azure.microsoft.com/documentation/services/data-factory/)

* [Machine Learning](http://azure.microsoft.com/documentation/services/machine-learning/)

* [HDInsight](http://azure.microsoft.com/documentation/services/hdinsight/)

* [仮想マシン](http://azure.microsoft.com/documentation/services/virtual-machines/)

* [Cloud Services](http://azure.microsoft.com/documentation/services/cloud-services/)

* [Media Services](http://azure.microsoft.com/documentation/services/media-services/)



## 次のステップ

* 最新情報については、「[Microsoft HPC と Batch のチーム ブログ](http://blogs.technet.com/b/windowshpc/)」と「[Azure ブログ](http://azure.microsoft.com/blog/tag/hpc/)」をご覧ください。
* また、「[Batch の新機能](http://azure.microsoft.com/updates/?service=batch)」を確認するか、[RSS フィード](http://azure.microsoft.com/updates/feed/?service=batch)にご登録ください。

<!---HONumber=September15_HO1-->