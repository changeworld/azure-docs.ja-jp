<properties
   pageTitle="クラウドのバッチと HPC のワークロードのリソース | Microsoft Azure"
   description="Azure で大規模並列、Batch、高性能コンピューティング (HPC) ワークロードを実行するのに役立つテクニカル リソースの一覧を示します。"
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
   ms.date="06/08/2016"
   ms.author="danlep"/>

# Azure における大規模なコンピューティング: バッチとハイ パフォーマンス コンピューティング (HPC) に関するテクニカル リソース
これは、Azure で大規模並列、Batch、HPC ワークロードを実行するのに役立つテクニカル リソース ガイドです。既存の Batch または HPC ワークロードを Azure クラウドに拡張するか、新しい大規模なコンピューティング ソリューションを Azure サービスを使用して Azure に構築します。

## ソリューションのオプション

Azure の大規模なコンピューティング オプションについて説明し、ワークロードやビジネス ニーズに適切なアプローチを選択します。

* [Batch と HPC ソリューション](batch-hpc-solutions.md)

* [ビデオ: Azure と HPC を使用したクラウドでの大規模なコンピューティング](https://azure.microsoft.com/documentation/videos/teched-europe-2014-big-compute-in-the-cloud-with-high-performance-computing-on-azure/)


## Azure Batch

[Batch](https://azure.microsoft.com/services/batch/) は、Linux および Windows アプリケーションを簡単にクラウド対応にすることができ、クラスターやジョブ スケジューラーをセットアップして管理しなくてもジョブを実行できるプラットフォーム サービスです。SDK を使用して、さまざまな言語を介してクライアント アプリケーションを Azure Batch と統合し、Azure にデータをステージングし、ジョブの実行パイプラインをビルドします。

* [ドキュメント](https://azure.microsoft.com/documentation/services/batch/)

* [.NET](https://msdn.microsoft.com/library/azure/mt348682.aspx)、[Python](http://azure-sdk-for-python.readthedocs.io/latest/)、[Node.js](http://azure.github.io/azure-sdk-for-node/azure-batch/latest/)、[Java](http://azure.github.io/azure-sdk-for-java/)、および [REST](https://msdn.microsoft.com/library/azure/dn820158.aspx) API リファレンス

* [Batch Management .NET ライブラリ](https://msdn.microsoft.com/library/mt463120.aspx) リファレンス

* チュートリアル: [.NET 向け Azure Batch ライブラリ](batch-dotnet-get-started.md)および [Batch Python クライアント](batch-python-tutorial.md)の概要

* [Batch フォーラム](https://social.msdn.microsoft.com/Forums/ja-JP/home?forum=azurebatch)

* [Batch のビデオ](https://azure.microsoft.com/documentation/videos/index/?services=batch)

## HPC クラスターのソリューション

既存の Windows または Linux HPC クラスターを Azure にデプロイまたは拡張して、コンピューティング集中型のワークロードを実行します。

### Microsoft HPC Pack

HPC Pack は、Microsoft Azure と Windows Server テクノロジに基づいて構築された、Microsoft の無料 HPC ソリューションであり、Windows と Linux HPC の両方のワークロードを実行できます。

* [HPC Pack 2012 R2 Update 3 をダウンロードする](https://www.microsoft.com/download/details.aspx?id=49922)

* [ドキュメント](https://technet.microsoft.com/library/jj899572.aspx)


* Azure における [Linux](../virtual-machines/virtual-machines-linux-hpcpack-cluster-options.md) および [Windows](../virtual-machines/virtual-machines-windows-hpcpack-cluster-options.md) の HPC Pack クラスター オプション

* [HPC Pack を使用した Azure ワーカー インスタンスへのバースト](https://technet.microsoft.com/library/gg481749.aspx)

* [HPC Pack を使用した Azure Batch へのバースト](https://technet.microsoft.com/library/mt612877.aspx)


* [Windows HPC フォーラム](https://social.microsoft.com/Forums/home?category=windowshpc)

### Linux と OSS クラスターのソリューション

次の Azure テンプレートを使用して、Linux HPC クラスターをデプロイします。

* [SLURM クラスターの起動](https://azure.microsoft.com/documentation/templates/slurm/)と[ブログの投稿](http://blogs.technet.com/b/windowshpc/archive/2015/06/06/deploy-a-slurm-cluster-on-azure.aspx)

* [トルク クラスターの起動](https://azure.microsoft.com/documentation/templates/torque-cluster/)

* [Intel Cloud Edition for Lustre Software - Eval](https://azure.microsoft.com/marketplace/partners/intel/lustre-cloud-edition-evaleval-lustre-2-7/)

## Microsoft MPI

[Microsoft MPI](https://msdn.microsoft.com/library/bb524831.aspx) (MS-MPI) は、Windows プラットフォームで並列アプリケーションを開発して実行するための、Microsoft が実装する標準 Message Passing Interface です。最新バージョンは MS-MPI v7 です。


* [MS-MPI のダウンロード](http://go.microsoft.com/FWLink/p/?LinkID=389556)

* [MS-MPI リファレンス](https://msdn.microsoft.com/library/dn473458.aspx)

* [MPI フォーラム](https://social.microsoft.com/Forums/ja-JP/home?forum=windowshpcmpi)

## コンピューティング集中型インスタンス

Azure では、Linux と Windows の HPC ワークロードを実行するために[さまざまなサイズ](../virtual-machines/virtual-machines-windows-sizes.md)が用意されています。たとえば、コンピューティング集中型の [A8 や A9](../virtual-machines/virtual-machines-windows-a8-a9-a10-a11-specs.md) インスタンスはバックエンド RDMA ネットワークに接続できます。


* [MPI アプリケーションを実行するように Linux RDMA クラスターを設定する](../virtual-machines/virtual-machines-linux-classic-rdma-cluster.md)

* [Set up a Windows RDMA cluster with HPC Pack to run MPI applications (HPC Pack を使用して Windows RDMA クラスターをセットアップして MPI アプリケーションを実行する)](../virtual-machines/virtual-machines-windows-classic-hpcpack-rdma-cluster.md)



## サンプルとデモ

* [Azure Batch C# および Python のコード サンプル](https://github.com/Azure/azure-batch-samples)

* [体験版 SUSE Linux Enterprise Server for HPC](https://azure.microsoft.com/marketplace/partners/suse/suselinuxenterpriseserver12optimizedforhighperformancecompute/)

## 関連する Azure サービス

* [Data Factory](https://azure.microsoft.com/documentation/services/data-factory/)

* [Machine Learning](https://azure.microsoft.com/documentation/services/machine-learning/)

* [HDInsight](https://azure.microsoft.com/documentation/services/hdinsight/)

* [Virtual Machines](https://azure.microsoft.com/documentation/services/virtual-machines/)

* [仮想マシン スケール セット](https://azure.microsoft.com/documentation/services/virtual-machine-scale-sets/)

* [Cloud Services](https://azure.microsoft.com/documentation/services/cloud-services/)

* [Media Services](https://azure.microsoft.com/documentation/services/media-services/)

## アーキテクチャの設計図

* [HPC and data orchestration using Azure Batch and Azure Data Factory (Azure Batch と Azure Data Factory を使用した HPC とデータのオーケストレーション)](http://go.microsoft.com/fwlink/?linkid=717686) (PDF) と[記事](../data-factory/data-factory-data-processing-using-batch.md)

## 業界のソリューション

* [銀行と証券](https://finance.azure.com/)

* [エンジニアリング シミュレーション](https://simulation.azure.com/)

## 顧客事例

* [ANEO](https://customers.microsoft.com/Pages/CustomerStory.aspx?recid=4168) 

* [d3View](https://customers.microsoft.com/Pages/CustomerStory.aspx?recid=22088)

* [Ludwig Institute of Cancer Research](https://customers.microsoft.com/Pages/CustomerStory.aspx?recid=5830)

* [Microsoft Research](https://customers.microsoft.com/Pages/CustomerStory.aspx?recid=15634)

* [Milliman](https://customers.microsoft.com/Pages/CustomerStory.aspx?recid=14967)

* [Mitsubishi UFJ Securities International](https://customers.microsoft.com/Pages/CustomerStory.aspx?recid=26266)

* [Schlumberger](http://azure.microsoft.com/blog/big-compute-for-large-engineering-simulations)

* [Towers Watson](https://customers.microsoft.com/Pages/CustomerStory.aspx?recid=18222)

* [UberCloud](https://simulation.azure.com/casestudies/Team-182-ABB-UC-Final.pdf)



## 次のステップ

* 最新情報については、「[Microsoft HPC と Batch のチーム ブログ](http://blogs.technet.com/b/windowshpc/)」と「[Azure ブログ](https://azure.microsoft.com/blog/tag/hpc/)」をご覧ください。
* また、「[Batch の新機能](https://azure.microsoft.com/updates/?service=batch)」を確認するか、[RSS フィード](https://azure.microsoft.com/updates/feed/?service=batch)にご登録ください。

<!---HONumber=AcomDC_0615_2016-->