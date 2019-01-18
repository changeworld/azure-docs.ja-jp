---
title: 概要
titleSuffix: Azure Machine Learning service
description: Azure Machine Learning service の概要。これは、高度な分析アプリケーションの開発、実験、デプロイをクラウド規模で行うプロフェッショナルなデータ サイエンティスト向けの統合されたエンドツーエンドのデータ サイエンス ソリューションです。
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: overview
ms.reviewer: jmartens
author: garyericson
ms.author: garye
ms.date: 12/04/2018
ms.custom: seodec18
ms.openlocfilehash: 3d9517bfc03a4d5696a2a97bd41f391e904e4dc8
ms.sourcegitcommit: 7cd706612a2712e4dd11e8ca8d172e81d561e1db
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/18/2018
ms.locfileid: "53580589"
---
# <a name="what-is-azure-machine-learning-service"></a>Azure Machine Learning サービスの概要

Azure Machine Learning service とは、機械学習モデルのトレーニング、デプロイ、自動化、管理を行うためのクラウド サービスであり、これらの操作のすべてをクラウドによって提供される幅広い規模で行うことができます。

## <a name="what-is-machine-learning"></a>機械学習とは

機械学習は、コンピューターで既存のデータを使って、将来の動き、結果、傾向を予測できるデータ サイエンスの手法の 1 つです。 機械学習を使用することで、明示的にプログラムすることなく、コンピューターが学習します。

機械学習からの予想や予測によってアプリやデバイスの機能性を高めることができます。 たとえばオンライン ショッピングでは、利用者が欲しいと考えそうな他の商品を、過去の購入履歴に基づいてお勧めするのに機械学習が役立っています。 また、クレジット カードの読み取り時に、機械学習では、トランザクションをトランザクションのデータベースと比較することで不正の検出を支援します。 また、ロボット掃除機が部屋を掃除するとき、機械学習は、作業が行われているかどうかを判断するのを支援します。

## <a name="what-is-azure-machine-learning-service"></a>Azure Machine Learning サービスの概要

Azure Machine Learning サービスには、機械学習モデルの開発、トレーニング、テスト、デプロイ、管理、および追跡に使用できるクラウドベースの環境が用意されています。

[ ![Azure Machine Learning service のワークフロー](./media/overview-what-is-azure-ml/aml.png)] (./media/overview-what-is-azure-ml/aml.png#lightbox)

Azure Machine Learning service では、オープン ソース テクノロジが完全にサポートされます。 そのため、数万のオープン ソース Python パッケージを機械学習コンポーネントと組み合わせて使用することができます。 その例として、TensorFlow と scikit-learn があります。
豊富なツールのサポートにより、インタラクティブにデータを探索し、変換し、モデルを開発してテストすることができます。 その例としては、[Jupyter Notebook](http://jupyter.org) や [Azure Machine Learning for Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=ms-toolsai.vscode-ai#overview) 拡張機能があります。
Azure Machine Learning サービスには、[モデルの生成とチューニングを自動化する](tutorial-auto-train-models.md)機能があるため、モデルを簡単に、効率的に、また正確に作成することができます。

Azure Machine Learning service を使用すると、ローカル マシンでトレーニングを開始してから、クラウドにスケールアウトすることができます。 クラウドのパワーと、Azure Machine Learning コンピューティングや [Azure Databricks](/azure/azure-databricks/what-is-azure-databricks) などの利用可能な多数の[コンピューティング先](how-to-set-up-training-targets.md)と[高度なハイパーパラメーター調整サービス](how-to-tune-hyperparameters.md)を活用することで、高品質のモデルを手早く構築できます。

適切なモデルがあれば、Docker などのコンテナーへのデプロイも簡単です。 つまり、Azure Container Instances または Azure Kubernetes Service に対して簡単にデプロイすることができます。 または、オンプレミスまたはクラウド上の自社のデプロイでコンテナーを使用することもできます。 詳細については、[デプロイする方法と場所](how-to-deploy-and-where.md)に関する記事を参照してください。

デプロイされたモデルを管理し、実験しながら複数の実行を追跡することで最適なソリューションを見つけることができます。
デプロイされたモデルは、[リアルタイム](how-to-consume-web-service.md)で予測を返したり、データが大量の場合は[非同期で](how-to-run-batch-predictions.md)予測を返したりすることができます。

さらに、高度な[機械学習パイプライン](concept-ml-pipelines.md)を使用して、データの準備、モデルのトレーニングと評価、およびデプロイのすべての手順で共同で作業することができます。

## <a name="what-can-i-do-with-azure-machine-learning-service"></a>Azure Machine Learning サービスでできること

Azure Machine Learning service では、モデルの自動トレーニングと自動調整が可能です。
その例については、[自動化された機械学習を使用して回帰モデルをトレーニングする方法](tutorial-auto-train-models.md)に関するページを参照してください。

Azure Machine Learning <a href="https://aka.ms/aml-sdk" target="_blank">SDK</a> for Python とオープンソースの Python パッケージを使用すると、Azure Machine Learning service ワークスペースできわめて正確な機械学習およびディープ ラーニング モデルを構築し、トレーニングすることができます。
以下の例のようなオープンソースの Python パッケージで使用できる多数の機械学習コンポーネントから選択することができます。

- <a href="https://scikit-learn.org/stable/" target="_blank">Scikit-learn</a>
- <a href="https://www.tensorflow.org" target="_blank">Tensorflow</a>
- <a href="https://pytorch.org" target="_blank">PyTorch</a>
- <a href="https://www.microsoft.com/en-us/cognitive-toolkit/" target="_blank">CNTK</a>
- <a href="http://mxnet.io" target="_blank">MXNet</a>

モデルを作成したら、それを使用して、テスト用にローカルにデプロイできるコンテナー (Docker など) を作成します。 テストが終わったら、Azure Container Instances または Azure Kubernetes Service を使用して、運用 Web サービスとしてモデルをデプロイできます。 詳細については、[デプロイする方法と場所](how-to-deploy-and-where.md)に関する記事を参照してください。

デプロイされたモデルは、[Azure Machine Learning SDK for Python](https://aka.ms/aml-sdk) または [Azure portal](https://portal.azure.com/) を使用して管理できます。
モデルの実験を追跡しながら、モデルのメトリックの評価、再トレーニング、モデルの新しいバージョンのデプロイのすべてを実行できます。

Azure Machine Learning service の基本的な使い方については、「[次の手順](#next-steps)」を参照してください。

## <a name="how-is-azure-machine-learning-service-different-from-machine-learning-studio"></a>Azure Machine Learning service と Machine Learning Studio の違い

Azure Machine Learning Studio は、共同作業に対応するドラッグアンドドロップ式の視覚的なワークスペースです。コードを記述することなく機械学習ソリューションを構築、テスト、およびデプロイすることができます。 事前に構築および構成された機械学習アルゴリズムとデータ処理モジュールが使用されています。

Machine Learning Studio を使用するのは、機械学習モデルをすばやく簡単に試したいときで、組み込みの機械学習アルゴリズムで十分な場合です。

Machine Learning サービスを使用するのは、Python 環境で作業していて機械学習アルゴリズムを詳細に制御したい場合、またはオープンソースの機械学習ライブラリを使用したい場合です。

> [!NOTE]
> Azure Machine Learning Studio で作成されたモデルは、Azure Machine Learning service でデプロイまたは管理することはできません。

## <a name="free-trial"></a>無料試用版

Azure サブスクリプションをお持ちでない場合は、開始する前に無料アカウントを作成してください。 [無料版または有料版の Azure Machine Learning service](http://aka.ms/AMLFree) を今日からお試しいただけます。

Azure サービスを使用するクレジットを取得できます。 このクレジットを使い切った後に、アカウントを保持して、[無料の Azure サービス](https://azure.microsoft.com/free/)を使用できます。 明示的に設定を変更して課金を了承しない限り、クレジット カードに課金されることはありません。 または、[MSDN サブスクライバーの特典を有効にする](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F)こともできます。これにより、有料の Azure のサービスを使用できるクレジットが毎月与えられます。

## <a name="next-steps"></a>次の手順

- [Azure portal](quickstart-get-started.md) または [Python](quickstart-create-workspace-with-python.md) を使用して、Machine Learning service ワークスペースの作成を開始します。

- 詳細なチュートリアル「[Azure Machine Learning service でイメージ分類モデルをトレーニングする](tutorial-train-models-with-aml.md)」に従います。

- [Azure Machine Learning を使用して、モデルの自動生成と自動調整を実行します](tutorial-auto-train-models.md)。

- [Azure Machine Learning Data Prep SDK](https://aka.ms/data-prep-sdk) を使用してデータを準備します。

- 機械学習シナリオを構築、最適化、および管理する[機械学習パイプライン](/azure/machine-learning/service/concept-ml-pipelines)について学習します。

- [Azure Machine Learning サービスのアーキテクチャと概念](concept-azure-machine-learning-architecture.md)に関する詳細な記事を読みます。

- [Microsoft が提供する他の機械学習製品](./overview-more-machine-learning.md)を参照して、さらに情報を入手します。


<!-- 

An intro to AML or an end-to-end quickstart video could go here.

In this 9-minute video, learn how you can benefit your app. You'll learn about key features and what a typical workflow looks like. 

>[!VIDEO https://channel9.msdn.com/Events/Connect/2016/138/player]
 
+ 0-3 minutes covers key features and use-cases.
+ 3-4 minutes covers service provisioning. 
+ 4-6 minutes covers Import Data wizard used to create an index using the built-in real estate dataset.

-->
