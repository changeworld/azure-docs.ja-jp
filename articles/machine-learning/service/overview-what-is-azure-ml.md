---
title: Azure Machine Learning とは | Microsoft Docs
description: クラウドにおける機械学習の基本的な概念とその用途について説明し、機械学習の用語を定義します。 Azure Machine Learning の概要。これは、高度な分析アプリケーションの開発、実験、デプロイをクラウド規模で行うプロフェッショナルなデータ サイエンティスト向けの統合されたエンドツーエンドのデータ サイエンス ソリューションです。
services: machine-learning
author: mwinkle
ms.author: mwinkle
manager: cgronlun
ms.service: machine-learning
ms.component: core
ms.workload: data-services
ms.topic: overview
ms.date: 09/21/2017
ms.openlocfilehash: 3e744b0e4a7ccebcdedac5a822ff717bed6b1f72
ms.sourcegitcommit: 301855e018cfa1984198e045872539f04ce0e707
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/19/2018
ms.locfileid: "36268418"
---
# <a name="what-is-machine-learning"></a>機械学習とは

機械学習は、コンピューターで既存のデータを使って、将来の動き、結果、傾向を予測できるデータ サイエンスの手法の 1 つです。 機械学習を使用することで、明示的にプログラムすることなく、コンピューターが学習します。

機械学習からの予想や予測によってアプリやデバイスの機能性を高めることができます。 オンライン ショッピングでは、ユーザーが今までに購入した製品に基づいて他の商品をお勧めするのに機械学習が役立っています。 クレジット カードが読み取られると、機械学習は、トランザクションをトランザクションのデータベースと比較し、不正の検出を支援します。 ロボット掃除機が部屋を掃除するとき、機械学習は、作業が行われているかどうかを判断するのを支援します。

## <a name="what-is-azure-machine-learning"></a>Azure Machine Learning とは
Azure Machine Learning は、統合されたエンドツーエンドのデータ サイエンスおよび高度な分析ソリューションです。 データ サイエンティストは、このソリューションを使用してデータの準備、実験の開発、モデルのデプロイをクラウド規模で行うことができます。

Azure Machine Learning の主要なコンポーネントは次のとおりです。
- Azure Machine Learning Workbench
- Azure Machine Learning 実験サービス
- Azure Machine Learning モデル管理サービス
- Microsoft Machine Learning Library for Apache Spark (MMLSpark ライブラリ)
- Visual Studio Code Tools for AI

これらのアプリケーションとサービスが一体となって、データ サイエンス プロジェクトの開発とデプロイを大幅に加速する助けとなります。 

![Azure Machine Learning の概念](./media/overview-what-is-azure-ml/aml-concepts.png)


## <a name="open-source-compatible"></a>オープン ソースとの互換性

Azure Machine Learning では、オープン ソース テクノロジが完全にサポートされます。 以下の機械学習フレームワークなど、数万のオープン ソース Python パッケージを使用できます。

- [scikit-learn](http://scikit-learn.org/)
- [TensorFlow](https://www.tensorflow.org/)
- [Microsoft Cognitive Toolkit](https://www.microsoft.com/en-us/cognitive-toolkit/)
- [Spark ML](https://spark.apache.org/docs/2.1.1/ml-pipeline.html)

Docker コンテナーや Spark クラスターなどの管理された環境で実験を実行することができます。 また、[Azure の GPU に対応する仮想マシン](https://docs.microsoft.com/azure/virtual-machines/windows/sizes-gpu)などの高度なハードウェアを使用して、実行を高速化することもできます。

Azure Machine Learning は、次のオープン ソース テクノロジの上に構築されています。

- [Jupyter Notebook](http://jupyter.org/)
- [Apache Spark](https://spark.apache.org/)
- [Docker](https://www.docker.com/)
- [Kubernetes](https://kubernetes.io/)
- [Python](https://www.python.org/)
- [Conda](https://conda.io/docs/)

これには、[Microsoft Machine Learning Library for Apache Spark](https://github.com/Azure/mmlspark) や Cognitive Toolkit など、Microsoft のオープン ソース テクノロジも含まれています。

さらに、大規模な問題を解決するために Microsoft が設計および開発した、最新の実証済みの機械学習テクノロジの恩恵を受けることもできます。 これらは、次のような多くの Microsoft 製品で実際に使用されています。

- Windows
- Bing
- Xbox
- Office
- SQL Server

Azure Machine Learning に含まれている Microsoft の機械学習テクノロジの一部を次に示します。

- [PROSE](https://microsoft.github.io/prose/) (PROgram Synthesis using Examples)
- [microsoftml](https://docs.microsoft.com/r-server/r/concept-what-is-the-microsoftml-package)
- [revoscalepy](https://docs.microsoft.com/r-server/python-reference/revoscalepy/revoscalepy-package)

## <a name="azure-machine-learning-workbench"></a>Azure Machine Learning Workbench
Azure Machine Learning Workbench は、Windows と macOS の両方でサポートされるデスクトップ アプリケーションとコマンドライン ツールです。 これを使用して、データ サイエンスのライフ サイクル全体で機械学習ソリューションを管理することができます。

- データの取り込みと準備
- モデルの開発と実験の管理
- さまざまなターゲット環境でのモデルのデプロイ

Azure Machine Learning Workbench によって提供される主な機能を次に示します。

- データ変換ロジックを例で学習できるデータ準備ツール。
- UX および Python コードを通じてアクセス可能なデータ ソース抽象化。
- 視覚的に構築されたデータ準備パッケージを呼び出すための Python SDK。
- 組み込みの Jupyter Notebook サービスとクライアント UX。
- 実行履歴ビューによる実験の監視と管理。
- Azure Active Directory を介して共有とコラボレーションを可能にするロールベースのアクセス制御。
- 実行ごとの自動プロジェクト スナップショット作成、およびネイティブ Git 統合による明示的なバージョン管理。 
- 一般的な Python IDE との統合。

詳細については、次の記事を参照してください。
- [データ準備ユーザー ガイド](../desktop-workbench/data-prep-user-guide.md)
- [Azure Machine Learning で Git を使用する](../desktop-workbench/using-git-ml-project.md)
- [Azure Machine Learning で Jupyter Notebook を使用する](../desktop-workbench/how-to-use-jupyter-notebooks.md)
- [ローミングと共有](../desktop-workbench/roaming-and-collaboration.md)
- [実行履歴ガイド](../desktop-workbench/how-to-use-run-history-model-metrics.md)
- [IDE 統合](../desktop-workbench/how-to-configure-your-ide.md)

## <a name="azure-machine-learning-experimentation-service"></a>Azure Machine Learning 実験サービス
実験サービスでは、機械学習実験の実行が処理されます。 また、プロジェクト管理、Git 統合、アクセス制御、ローミング、共有により、Workbench もサポートされます。 

ユーザーは、簡単な構成を通じて、さまざまなコンピューティング環境オプションで実験を実行できます。

- ローカル ネイティブ
- ローカル Docker コンテナー
- リモート VM 上の Docker コンテナー
- Azure のスケールアウト Spark クラスター

実験サービスでは、スクリプトを分離して実行して再現性のある結果を得られるようにするために仮想環境が構築されます。 実行履歴情報が記録され、その履歴が視覚的に表示されます。 ユーザーは、実行した実験の中から最良のモデルを簡単に選択することができます。 

詳細については、[実験サービスの構成](../desktop-workbench/experimentation-service-configuration.md)に関するページを参照してください。

## <a name="azure-machine-learning-model-management-service"></a>Azure Machine Learning モデル管理サービス

モデル管理サービスは、データ サイエンティストと DevOps チームがさまざまな環境に予測モデルをデプロイすることを可能にします。 モデルのバージョンと系列は、トレーニングの実行からデプロイまで追跡されます。 モデルは、クラウドに保存、登録、管理されます。 

単純な CLI コマンドを使用して、モデル、スコア付けスクリプト、依存関係を Docker イメージにコンテナー化できます。 これらのイメージは、Azure (Azure Container Registry) でホストされる独自の Docker レジストリに登録されます。 これらは、次のターゲットを確実にデプロイすることができます。

- ローカル マシン
- オンプレミスのサーバー
- クラウド
- IoT エッジ デバイス

Azure Container Service (ACS) で動作する Kubernetes は、クラウドのスケールアウト デプロイに使用されます。 視覚的な分析のために、AppInsights でモデル実行テレメトリがキャプチャされます。 

モデル管理サービスの詳細については、[モデル管理の概要](../desktop-workbench/model-management-overview.md)に関するページを参照してください。


## <a name="microsoft-machine-learning-library-for-apache-spark"></a>Microsoft Machine Learning Library for Apache Spark

[MMLSpark](https://github.com/Azure/mmlspark) (Microsoft Machine Learning Library for Apache Spark) は、Apache Spark 向けのディープ ラーニングおよびデータ サイエンス ツールを提供するオープン ソースの Spark パッケージです。 このパッケージによって、[Spark Machine Learning Pipelines](https://spark.apache.org/docs/2.1.1/ml-pipeline.html) が [Microsoft Cognitive Toolkit](https://www.microsoft.com/en-us/cognitive-toolkit/) および [OpenCV](http://opencv.org/) ライブラリと統合されます。 これを使用することで、大きな画像およびテキスト データセットに対する強力かつスケーラブルな予測モデルと分析モデルをすぐに作成できます。 いくつかの特徴を次に示します。

- HDFS から Spark DataFrame に画像を簡単に取り込む
- OpenCV からの変換を使用して画像データを前処理する
- Microsoft Cognitive Toolkit を使用して、事前にトレーニングされたディープ ニューラル ネットを使用して画像の特徴を生成する 
- Keras の事前にトレーニングされた双方向 LSTM を使用して、医療エンティティを抽出する
- Azure 上の N シリーズ GPU VM で DNN ベースの画像分類モデルをトレーニングする
- 単一のトランスフォーマーを介して SparkML のプリミティブ上で使いやすい API を使用して、自由形式のテキスト データの特徴を生成する
- 暗黙的なデータの特徴の生成を介して分類モデルと回帰モデルをトレーニングする
- インスタンスごとのメトリックなどの豊富な評価メトリックを計算する

詳細については、[Azure Machine Learning での MMLSpark の使用](../desktop-workbench/how-to-use-mmlspark.md)に関するページを参照してください。

## <a name="visual-studio-code-tools-for-ai"></a>Visual Studio Code Tools for AI
Visual Studio Code Tools for AI は、ディープ ラーニングおよび AI ソリューションを構築、テスト、デプロイするための Visual Studio Code の拡張機能です。 このツールには、以下を含む Azure Machine Learning との多くの統合ポイントが用意されています。
- 実行履歴ビューには、トレーニングの実行のパフォーマンスと記録されたメトリックが表示されます。
- ギャラリー ビューでは、ユーザーは、Microsoft Cognitive Toolkit、TensorFlow、その他の多数のディープ ラーニング フレームワークを使用して、新しいプロジェクトを参照およびブートストラップできます。 
- エクスプローラー ビューでは、実行するスクリプトのコンピューティング ターゲットを選択できます。
 

## <a name="what-are-the-machine-learning-options-from-microsoft"></a>Microsoft の機械学習オプション
Azure には、Azure Machine Learning に加えて、機械学習モデルを構築、デプロイ、管理するためのさまざまなオプションが用意されています。 [詳しくは、こちらをご覧ください。](../desktop-workbench/overview-more-machine-learning.md)

[!INCLUDE [machine-learning-free-trial](../../../includes/machine-learning-free-trial.md)]

## <a name="next-steps"></a>次の手順
* [Azure Machine Learning をインストールおよび作成する](quickstart-installation.md)
