<properties
   pageTitle="Azure における大規模なコンピューティング: Batch とハイ パフォーマンス コンピューティング (HPC) に関するテクニカル リソース"
   description="この記事では、Azure で大規模並列、Batch、HPC ワークロードを実行するのに役立つテクニカル リソースを一覧します。"
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
   ms.date="05/29/2015"
   ms.author="danlep"/>

# Azure における大規模なコンピューティング: Batch とハイ パフォーマンス コンピューティング (HPC) に関するテクニカル リソース
これは、Azure で大規模並列、Batch、HPC ワークロードを実行するのに役立つテクニカル リソース ガイドです。既存の Batch または HPC ワークロードを Azure クラウドに拡張するか、新しい大規模なコンピューティング ソリューションを Azure サービスを使用して Azure に構築します。

## ソリューションのオプション


Azure の大規模なコンピューティング オプションについて説明し、ワークロードやビジネス ニーズに適切なアプローチを選択します。

* [概要](https://msdn.microsoft.com/library/azure/dn482130.aspx)

* [ビデオ: Azure と HPC を使用したクラウドでの大規模なコンピューティング](http://azure.microsoft.com/documentation/videos/teched-europe-2014-big-compute-in-the-cloud-with-high-performance-computing-on-azure/)


## Azure Batch

[Batch](http://azure.microsoft.com/services/batch/) (プレビュー) は、アプリケーションを簡単にクラウド対応にすることができ、クラスターやジョブ スケジューラをセットアップして管理しなくてもジョブを実行できるプラットフォーム サービスです。SDK を使用して、さまざまな言語を介してクライアント アプリケーションを Azure Batch と統合し、Azure にデータをステージングし、ジョブの実行パイプラインをビルドします。

* [プレビュー](http://azure.microsoft.com/services/preview/)のサインアップ

* [ドキュメント](http://azure.microsoft.com/documentation/services/batch/)

* [API リファレンス](https://msdn.microsoft.com/library/azure/dn820177.aspx)

* [Batch フォーラム](https://social.msdn.microsoft.com/Forums/home?forum=azurebatch)

* [チュートリアル - .NET 向け Azure Batch ライブラリの概要](batch-dotnet-get-started.md)

* [Batch のビデオ](https://azure.microsoft.com/documentation/videos/index/?services=batch)

## HPC クラスターのソリューション

既存の Windows HPC クラスターを拡張または移行して、Azure でコンピューティング集中型のワークロードを実行します。

### Microsoft HPC Pack

[HPC Pack](https://technet.microsoft.com/library/cc514029) は Microsoft の無料のクラスター マネージャーであり、オンプレミス、ハイブリッド、クラウド ベース HPC のためのジョブ スケジューリング ソリューションです。

* [HPC Pack を使用した Azure へのバースト](https://technet.microsoft.com/library/gg481749.aspx)

* [Azure VM の HPC Pack](https://msdn.microsoft.com/library/azure/dn518135.aspx)

* [チュートリアル: Azure での HPC Pack を使用したハイブリッド クラスターのセットアップ](../cloud-services-setup-hybrid-hpcpack-cluster.md)

* [Windows HPC フォーラム](https://social.microsoft.com/Forums/home?category=windowshpc)

## Microsoft MPI

[Microsoft MPI](https://msdn.microsoft.com/library/bb524831.aspx) (MS-MPI) は、Windows プラットフォームで並列アプリケーションを開発して実行するための、Microsoft が実装する標準 Message Passing Interface です。


* [MS-MPI のダウンロード](http://go.microsoft.com/FWLink/p/?LinkID=389556)

* [MS-MPI リファレンス](https://msdn.microsoft.com/library/dn473458.aspx)

* [MPI フォーラム](https://social.microsoft.com/Forums/home?forum=windowshpcmpi)


## コンピューティング集中型インスタンス

Azure では、Batch や HPC ワークロードを実行するために、[A8、A9、A10、A11 インスタンス](https://msdn.microsoft.com/library/azure/dn689095.aspx)などの[さまざまなサイズ](https://msdn.microsoft.com/library/azure/dn197896.aspx)が用意されています。


* [A8および A9 インスタンス: HPC Pack 使用クイック スタート](https://msdn.microsoft.com/library/azure/dn594431.aspx)

* [A8 および A9 インスタンス上での MPI アプリケーションの実行](https://msdn.microsoft.com/library/azure/dn592104.aspx)

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

<!---HONumber=58_postMigration-->