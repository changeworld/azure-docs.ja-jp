<properties 
    pageTitle="Azure Machine Learning とは|Microsoft Azure" 
    description="Azure Machine Learning サービスの概要。" 
    services="machine-learning" 
    documentationCenter="" 
    authors="tedway" 
    manager="neerajkh" 
    editor="cgronlun"/>

<tags 
    ms.service="machine-learning" 
    ms.workload="data-services" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="04/22/2015" 
    ms.author="tedway;olgali"/>


# 機械学習とは
機械学習は、あらゆる場所で活用されていますオンライン ショッピングでは、機械学習が、ユーザーが今までに購入した製品に基づいて他の商品をお勧めするのに役立ちます。クレジット カードが読み取られると、機械学習は、銀行による不正の検出を支援し、トランザクションが疑わしい場合は通知します。機械学習は、既存のデータから学習するモデルを作成するプロセスであり、将来のデータについての予測分析をします。

## Azure Machine Learning とは
Azure Machine Learning は、クラウド ベースの強力な予測分析サービスであり、分析ソリューションの迅速な作成を可能にします。Azure Machine Learning は誰もが使用できる機械学習として構築されました。機械学習テクノロジーの構築とデプロイに関わる複雑な作業をなくし、誰もがアクセスできるようになっています。これは、完全に管理されたサービスであり、ハードウェアを購入したり、仮想マシンを手動で管理したりする必要はありません。

ブラウザー ベース ツールである  [Machine Learning Studio](machine-learning-what-is-ml-studio.md) を使用して、機械学習のワークフローを迅速に作成し、自動化できます。何百もの既存の [機械学習ライブラリ](https://msdn.microsoft.com/library/azure/f5c746fd-dcea-4929-ba50-2a79c4c067d7) をドラッグ アンド ドロップすれば予測分析ソリューションをすぐに開始することができます。また、必要に応じて、独自のカスタム [R](machine-learning-r-quickstart.md) と [Python](machine-learning-execute-python-scripts.md) スクリプトを追加してこれらを拡張することができます。Studio は、どのブラウザーでも動作し、ソリューション上で迅速に開発、反復処理することができます。![Azure Machine Learning Studio を使用したクラウドでの予測分析機能実験](./media/machine-learning-what-is-machine-learning/AzureMLStudio.png)

顧客ごとに、[Web サービス](machine-learning-publish-a-machine-learning-web-service.md)、[API を使用したモデルのトレーニングと再トレーニング](machine-learning-retrain-models-programmatically.md)、[エンドポイントの管理](machine-learning-create-endpoint.md) と [Web サービスのスケール](machine-learning-scaling-endpoints.md)を簡単に発見、作成し、サービスの監視とデバッグの診断を構成します。最新の機能は、次のとおりです。

- 構成可能なカスタム R モジュールの作成、独自のトレーニングまたは予測 R-スクリプトの組み込み、numpy、scipy、pandas、scikit など の大規模なエコシステムのライブラリを使用する Python スクリプトの追加。今すぐ、[カウントで学習][learning-with-counts]を使用したテラバイト規模のデータを使用してトレーニングできます。異常検出には PCA または 1 クラス SVM を使用できます、使い慣れた SQLite を使用してデータを簡単に変更、フィルター処理、削除できます。 
- [「Machine Learning Community Gallery(機械学習コミュニティ ギャラリー)」](machine-learning-gallery-how-to-use-contribute-publish.md) では、他のユーザーが作成した興味深い実験を見つけたり、使用したりすることができます。[ギャラリー](http://gallery.azureml.net)内の実験について質問したり、コメントを投稿したりすることができます。また自分の実験を公開することもできます。LinkedIn や Twitter などのソーシャル チャネル経由で興味深い実験へのリンクを共有することができます。ギャラリーは、ユーザーが Azure Machine Learning を始めるための効果的な方法であり、またコミュニティの他のユーザーから学ぶことができる場所でもあります。![サンプルの予測実験を試すか、ご自身の実験を Azure Machine Learning ギャラリーに投稿します。](./media/machine-learning-what-is-machine-learning/AzureMLGallery.png)
- Azure のサブスクリプションで[マーケットプレース アプリケーション](https://datamarket.azure.com/browse?query=machine+learning)を購入し、Azure Marketplace から直接、Recommendations、Text Analytics、 Anomaly Detection などの完成した Web サービスを利用できます。 
- 生データから消費型 Web サービスへのデータサイエンスをステップ バイ ステップでガイドするので、クラウド ベースのデータ サイエンスが簡単に利用できるようになります。Azure Machine Learning で使用できる iPython Notebook 、Python Tools for Visual Studio などの人気のツール使用する機能が追加されています。

## 今すぐ使ってみる
[ステップ バイ ステップ チュートリアル](machine-learning-create-experiment.md)を使用し、また[サンプル上に構築](machine-learning-sample-experiments.md)することによって予測分析と機械学習の基礎について学習できます。Studio での実験する場合は、Machine Learning を始めるにあたって Azure サブスクリプションやクレジット カードは必要ありません。


<!-- Module References -->
[learning-with-counts]: https://msdn.microsoft.com/library/azure/81c457af-f5c0-4b2d-922c-fdef2274413c/

<!--HONumber=54--> 