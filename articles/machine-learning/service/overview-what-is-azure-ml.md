---
title: Azure Machine Learning サービスの概要
description: クラウドにおける機械学習の基本的な概念とその用途について説明し、機械学習の用語を定義します。 Azure Machine Learning の概要。これは、高度な分析アプリケーションの開発、実験、デプロイをクラウド規模で行うプロフェッショナルなデータ サイエンティスト向けの統合されたエンドツーエンドのデータ サイエンス ソリューションです。
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: overview
ms.reviewer: jmartens
author: garyericson
ms.author: garye
ms.date: 09/24/2018
ms.openlocfilehash: 6e0294f99d1c2291e84bf2ac5d5b1f771222b551
ms.sourcegitcommit: 7c4fd6fe267f79e760dc9aa8b432caa03d34615d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/28/2018
ms.locfileid: "47433414"
---
# <a name="what-is-azure-machine-learning-service-preview"></a>Azure Machine Learning サービス (プレビュー) の概要

Azure Machine Learning サービス (プレビュー) は、機械学習モデルの開発とデプロイに使用できるクラウド サービスです。 Azure Machine Learning サービスを使用すると、クラウドで提供されている大規模な環境で、モデルの構築、トレーニング、デプロイ、および管理というすべての処理時にモデルを追跡できます。

## <a name="what-is-machine-learning"></a>機械学習とは

機械学習は、コンピューターで既存のデータを使って、将来の動き、結果、傾向を予測できるデータ サイエンスの手法の 1 つです。 機械学習を使用することで、明示的にプログラムすることなく、コンピューターが学習します。

機械学習からの予想や予測によってアプリやデバイスの機能性を高めることができます。 たとえばオンライン ショッピングでは、ユーザーが今までに購入した製品に基づいて他の商品をお勧めするのに機械学習が役立っています。 また、クレジット カードの読み取り時に、機械学習では、トランザクションをトランザクションのデータベースと比較することで不正の検出を支援します。 また、ロボット掃除機が部屋を掃除するとき、機械学習は、作業が行われているかどうかを判断するのを支援します。

## <a name="what-is-azure-machine-learning-service"></a>Azure Machine Learning サービスの概要

Azure Machine Learning サービスには、機械学習モデルの開発、トレーニング、テスト、デプロイ、管理、および追跡に使用できるクラウドベースの環境が用意されています。

[ ![Azure Machine Learning サービスのワークフロー](./media/overview-what-is-azure-ml/aml.png) ] (./media/overview-what-is-azure-ml/aml.png#lightbox)

Azure Machine Learning サービスはオープンソース テクノロジを完全にサポートしているため、TensorFlow や scikit-learn などの機械学習コンポーネントで数万ものオープンソース Python パッケージを使用できます。
[Jupyter ノートブック](http://jupyter.org)や [Visual Studio Code Tools for AI](https://visualstudio.microsoft.com/downloads/ai-tools-vscode/) などの豊富なツールを使用できるので、インタラクティブにデータを探索し、変換し、開発して、モデルをテストすることができます。
Azure Machine Learning サービスには、[モデルの生成とチューニングを自動化する](tutorial-auto-train-models.md)機能があるため、モデルを簡単に、効率的に、また正確に作成することができます。

Azure Machine Learning サービスを使用すると、ローカル マシンでトレーニングを開始してから、クラウドにスケール アウトすることができます。 [Azure Batch AI](https://azure.microsoft.com/services/batch-ai/) がネイティブでサポートされ、[高度なハイパーパラメーター チューニング サービス](how-to-tune-hyperparameters.md)が提供されているため、クラウドの機能を利用して優れたモデルをより迅速に構築できます。 

適切なモデルがあれば、Docker などのコンテナーへのデプロイも簡単です。 つまり、[Azure Container Instances](how-to-deploy-to-aci.md) または [Azure Kubernetes Service](how-to-deploy-to-aks.md) へのデプロイが簡単であり、オンプレミスまたはクラウド上の自社のデプロイでコンテナーを使用できます。
デプロイされたモデルを管理し、実験しながら複数の実行を追跡することで最適なソリューションを見つけることができます。

[!INCLUDE [aml-preview-note](../../../includes/aml-preview-note.md)]

## <a name="what-can-i-do-with-azure-machine-learning-service"></a>Azure Machine Learning サービスでできること

Azure Machine Learning サービスを使用すると、モデルを自動生成し、自動調整することができます。
例については、[自動化された Azure Machine Learning で分類モデルを自動的にトレーニングするチュートリアル](tutorial-auto-train-models.md)に関する記事を参照してください。

また、Azure Machine Learning <a href="http://aka.ms/aml-sdk" target="_blank">SDK</a> for Python とオープンソースの Python パッケージを使用すると、Azure Machine Learning ワークスペースで極めて正確な機械学習およびディープ ラーニング モデルを構築し、トレーニングすることができます。
以下のようなオープンソースの Python パッケージで使用できる多数の機械学習コンポーネントから選択することができます。

- <a href="http://scikit-learn.org/stable/" target="_blank">Scikit-learn</a>
- <a href="https://www.tensorflow.org" target="_blank">Tensorflow</a>
- <a href="https://pytorch.org" target="_blank">PyTorch</a>
- <a href="https://www.microsoft.com/cognitive-toolkit/" target="_blank">CNTK</a>
- <a href="http://mxnet.io" target="_blank">MXNet</a>

モデルを作成したら、それを使用してローカルで展開できるコンテナー (Docker など) を作成してテストしてから、[Azure Container Instances](how-to-deploy-to-aci.md) または [Azure Kubernetes Service](how-to-deploy-to-aks.md) で実稼働 Web サービスとして使用します。

デプロイされたモデルは、[Azure portal](https://portal.azure.com/) または [Azure Machine Learning CLI 拡張機能](https://review.docs.microsoft.com/azure/machine-learning/service/reference-azure-machine-learning-cli)を使用して管理できます。
モデルの実験を追跡しながら、モデルのメトリックを評価し、モデルの新しいバージョンを再トレーニングして再デプロイすることができます。

Azure Machine Learning サービスの基本的な使い方については、後述する「[次の手順](#next-steps)」を参照してください。

## <a name="how-is-azure-machine-learning-service-different-from-studio"></a>Azure Machine Learning サービスと Studio の違い

Azure Machine Learning Studio は、共同作業に対応するドラッグアンドドロップ式の視覚的なワークスペースです。コードを記述することなく機械学習ソリューションを構築、テスト、およびデプロイすることができます。 事前に構築および構成された機械学習アルゴリズムとデータ処理モジュールが使用されています。

Machine Learning Studio を使用するのは、機械学習モデルをすばやく簡単に試したいときで、組み込みの機械学習アルゴリズムで十分な場合です。

Machine Learning サービスを使用するのは、Python 環境で作業していて機械学習アルゴリズムを詳細に制御したい場合、またはオープンソースの機械学習ライブラリを使用したい場合です。

> [!NOTE]
> Azure Machine Learning Studio で作成されたモデルは、Azure Machine Learning サービスでデプロイまたは管理することはできません。

## <a name="free-trial"></a>無料試用版
サブスクライバーでない場合は、[Azure アカウントを無料で開設できます](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A261C142F)。 Azure サービスを使用するクレジットを取得できます。 このクレジットを使い切った後に、アカウントを保持して、[無料の Azure サービス](https://azure.microsoft.com/free/)を使用できます。 明示的に設定を変更して課金を了承しない限り、クレジット カードに課金されることはありません。 または、[MSDN サブスクライバーの特典を有効にする](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F) こともできます - MSDN サブスクリプションにより、有料の Azure のサービスを使用できるクレジットが毎月与えられます。

## <a name="next-steps"></a>次の手順

- [まず Azure portal の使用](quickstart-get-started.md)に関する記事を参照して機械学習ワークスペースを作成します
 
- 詳細なチュートリアルの「[Train an image classification model with Azure Machine Learning](tutorial-train-models-with-aml.md)」(Azure Machine Learning を使用して画像の分類モデルをトレーニングする) に従って、Azure Machine Learning サービスでモデルをトレーニングおよびデプロイする方法を学びます。

- Azure Machine Learning でモデルの自動生成および自動調整する方法については、[自動化された Azure Machine Learning で分類モデルを自動的にトレーニングする方法に関するチュートリアル](tutorial-auto-train-models.md)を参照してください。

- サービスの技術的な詳細情報については、[Azure Machine Learning サービスのアーキテクチャと概念](concept-azure-machine-learning-architecture.md)に関する記事を参照してください。

- Microsoft のその他の機械学習製品については、[Microsoft のその他の機械学習製品](./overview-more-machine-learning.md)に関する記事を参照してください。


<!-- 

An intro to AML or an end-to-end quickstart video could go here.

In this 9-minute video, learn how you can benefit your app. You'll learn about key features and what a typical workflow looks like. 

>[!VIDEO https://channel9.msdn.com/Events/Connect/2016/138/player]
 
+ 0-3 minutes covers key features and use-cases.
+ 3-4 minutes covers service provisioning. 
+ 4-6 minutes covers Import Data wizard used to create an index using the built-in real estate dataset.

-->
