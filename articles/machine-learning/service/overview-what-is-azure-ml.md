---
title: 概要
titleSuffix: Azure Machine Learning service
description: Azure Machine Learning サービスの概要。これは、高度な分析アプリケーションの開発、実験、デプロイをクラウド規模で行うプロフェッショナルなデータ サイエンティスト向けの統合されたエンドツーエンドのデータ サイエンス ソリューションです。
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: overview
ms.reviewer: jmartens
author: garyericson
ms.author: garye
ms.date: 12/04/2018
ms.custom: seodec18
ms.openlocfilehash: a9e91a2c11dbef3b3d9b887bfd6bb4b3372f3523
ms.sourcegitcommit: c37122644eab1cc739d735077cf971edb6d428fe
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/14/2018
ms.locfileid: "53408614"
---
# <a name="what-is-azure-machine-learning-service"></a>Azure Machine Learning サービスの概要

Azure Machine Learning サービスとは、機械学習モデルのトレーニング、デプロイ、自動化、および管理を行うために使用できるクラウド サービスであり、これらの操作のすべてをクラウドによって提供される幅広い規模で行うことができます。

## <a name="what-is-machine-learning"></a>機械学習とは

機械学習は、コンピューターで既存のデータを使って、将来の動き、結果、傾向を予測できるデータ サイエンスの手法の 1 つです。 機械学習を使用することで、明示的にプログラムすることなく、コンピューターが学習します。

機械学習からの予想や予測によってアプリやデバイスの機能性を高めることができます。 たとえばオンライン ショッピングでは、ユーザーが今までに購入した製品に基づいて他の商品をお勧めするのに機械学習が役立っています。 また、クレジット カードの読み取り時に、機械学習では、トランザクションをトランザクションのデータベースと比較することで不正の検出を支援します。 また、ロボット掃除機が部屋を掃除するとき、機械学習は、作業が行われているかどうかを判断するのを支援します。

## <a name="what-is-azure-machine-learning-service"></a>Azure Machine Learning サービスの概要

Azure Machine Learning サービスには、機械学習モデルの開発、トレーニング、テスト、デプロイ、管理、および追跡に使用できるクラウドベースの環境が用意されています。

[ ![Azure Machine Learning サービスのワークフロー](./media/overview-what-is-azure-ml/aml.png) ] (./media/overview-what-is-azure-ml/aml.png#lightbox)

Azure Machine Learning サービスはオープンソース テクノロジを完全にサポートしているため、TensorFlow や scikit-learn などの機械学習コンポーネントで数万ものオープンソース Python パッケージを使用できます。
[Jupyter ノートブック](http://jupyter.org)や [Azure Machine Learning for Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=ms-toolsai.vscode-ai#overview) などの豊富なツールのサポートによって、インタラクティブなデータの探索と変換、モデルの開発とテストを簡単に行うことができます。
Azure Machine Learning サービスには、[モデルの生成とチューニングを自動化する](tutorial-auto-train-models.md)機能があるため、モデルを簡単に、効率的に、また正確に作成することができます。

Azure Machine Learning サービスを使用すると、ローカル マシンでトレーニングを開始してから、クラウドにスケール アウトすることができます。 クラウドのパワーと、Azure Machine Learning コンピューティングや [Azure Databricks](/azure/azure-databricks/what-is-azure-databricks) などの利用可能な多数の[コンピューティング先](how-to-set-up-training-targets.md)と[高度なハイパーパラメーター調整サービス](how-to-tune-hyperparameters.md)を活用することで、高品質のモデルを手早く構築できます。

適切なモデルがあれば、Docker などのコンテナーへのデプロイも簡単です。 つまり、Azure Container Instances または Azure Kubernetes Service へのデプロイは簡単であり、オンプレミスまたはクラウド上の自社のデプロイでコンテナーを使用できます。 詳細については、[デプロイする方法と場所](how-to-deploy-and-where.md)に関するドキュメントを参照してください。
デプロイされたモデルを管理し、実験しながら複数の実行を追跡することで最適なソリューションを見つけることができます。
デプロイされたモデルは、[リアルタイム](how-to-consume-web-service.md)で予測を返したり、データが大量の場合は[非同期で](how-to-run-batch-predictions.md)予測を返したりすることができます。

さらに、高度な[機械学習パイプライン](concept-ml-pipelines.md)を使用して、データの準備、モデルのトレーニングと評価、およびデプロイのすべての手順で共同で作業することができます。

## <a name="what-can-i-do-with-azure-machine-learning-service"></a>Azure Machine Learning サービスでできること

Azure Machine Learning サービスでは、モデルの自動トレーニングと自動調整が可能です。
例については、「[チュートリアル:Azure Automated Machine Learning で分類モデルを自動的にトレーニングする](tutorial-auto-train-models.md)」を参照してください。

Azure Machine Learning <a href="https://aka.ms/aml-sdk" target="_blank">SDK</a> for Python とオープンソースの Python パッケージを使用して、Azure Machine Learning サービス ワークスペースで精度の高い機械学習モデルとディープ ラーニング モデルをご自身で構築してトレーニングできます。
以下のようなオープンソースの Python パッケージで使用できる多数の機械学習コンポーネントから選択することができます。

- <a href="https://scikit-learn.org/stable/" target="_blank">Scikit-learn</a>
- <a href="https://www.tensorflow.org" target="_blank">Tensorflow</a>
- <a href="https://pytorch.org" target="_blank">PyTorch</a>
- <a href="https://www.microsoft.com/en-us/cognitive-toolkit/" target="_blank">CNTK</a>
- <a href="http://mxnet.io" target="_blank">MXNet</a>

モデルを作成したら、それを使用して、テスト用にローカルにデプロイできるコンテナー (Docker など) を作成します。 モデルのテストが終わったら、Azure Container Instances または Azure Kubernetes Service を使用して、運用 Web サービスとしてモデルをデプロイできます。 詳細については、[デプロイする方法と場所](how-to-deploy-and-where.md)に関するドキュメントを参照してください。

デプロイされたモデルは、[Azure Machine Learning SDK for Python](https://aka.ms/aml-sdk) または [Azure portal](https://portal.azure.com/)を使用して管理できます。
モデルの実験を追跡しながら、モデルのメトリックの評価、再トレーニング、モデルの新しいバージョンのデプロイのすべてを実行できます。

Azure Machine Learning サービスの基本的な使い方については、後述する「[次の手順](#next-steps)」を参照してください。

## <a name="how-is-azure-machine-learning-service-different-from-studio"></a>Azure Machine Learning サービスと Studio の違い

Azure Machine Learning Studio は、共同作業に対応するドラッグアンドドロップ式の視覚的なワークスペースです。コードを記述することなく機械学習ソリューションを構築、テスト、およびデプロイすることができます。 事前に構築および構成された機械学習アルゴリズムとデータ処理モジュールが使用されています。

Machine Learning Studio を使用するのは、機械学習モデルをすばやく簡単に試したいときで、組み込みの機械学習アルゴリズムで十分な場合です。

Machine Learning サービスを使用するのは、Python 環境で作業していて機械学習アルゴリズムを詳細に制御したい場合、またはオープンソースの機械学習ライブラリを使用したい場合です。

> [!NOTE]
> Azure Machine Learning Studio で作成されたモデルは、Azure Machine Learning サービスでデプロイまたは管理することはできません。

## <a name="free-trial"></a>無料試用版

Azure サブスクリプションをお持ちでない場合は、開始する前に無料アカウントを作成してください。 [無料版または有料版の Azure Machine Learning service](http://aka.ms/AMLFree) を今日からお試しいただけます。

Azure サービスを使用するクレジットを取得できます。 このクレジットを使い切った後に、アカウントを保持して、[無料の Azure サービス](https://azure.microsoft.com/free/)を使用できます。 明示的に設定を変更して課金を了承しない限り、クレジット カードに課金されることはありません。 または、[MSDN サブスクライバーの特典を有効にする](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F) こともできます。これにより、有料の Azure のサービスを使用できるクレジットが毎月与えられます。

## <a name="next-steps"></a>次の手順

- [Azure portal](quickstart-get-started.md) または [Python](quickstart-create-workspace-with-python.md) を使用して、Machine Learning Service ワークスペースの作成を開始します。

- 詳細なチュートリアル「[Azure Machine Learning サービスで画像分類モデルをトレーニングする](tutorial-train-models-with-aml.md)」に従います。

- [Azure Machine Learning を使用して、モデルの自動生成と自動調整を実行します](tutorial-auto-train-models.md)。

- [Azure Machine Learning Data Prep SDK](https://aka.ms/data-prep-sdk) を使用してデータを準備します。

- 機械学習シナリオを構築、最適化、および管理する[機械学習パイプライン](/azure/machine-learning/service/concept-ml-pipelines)について学習します。

- [Azure Machine Learning サービスのアーキテクチャと概念](concept-azure-machine-learning-architecture.md)に関する詳細な記事を読みます。

- Microsoft のその他の機械学習製品については、[Microsoft のその他の機械学習製品](./overview-more-machine-learning.md)に関する記事を参照してください。


<!-- 

An intro to AML or an end-to-end quickstart video could go here.

In this 9-minute video, learn how you can benefit your app. You'll learn about key features and what a typical workflow looks like. 

>[!VIDEO https://channel9.msdn.com/Events/Connect/2016/138/player]
 
+ 0-3 minutes covers key features and use-cases.
+ 3-4 minutes covers service provisioning. 
+ 4-6 minutes covers Import Data wizard used to create an index using the built-in real estate dataset.

-->
