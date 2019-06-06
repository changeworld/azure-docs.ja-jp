---
title: ワークスペースとは
titleSuffix: Azure Machine Learning service
description: ワークスペースの概要と、それが Azure Machine Learning service に必要な理由を説明します。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: sgilley
author: sdgilley
ms.date: 05/21/2019
ms.openlocfilehash: 5785bf8f6538f1d91e7a23178e29487ebee14f29
ms.sourcegitcommit: cfbc8db6a3e3744062a533803e664ccee19f6d63
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/21/2019
ms.locfileid: "65989829"
---
# <a name="what-is-an-azure-machine-learning-workspace"></a>Azure Machine Learning ワークスペースとは

ワークスペースは、Azure Machine Learning service の最上位のリソースです。 Azure Machine Learning service を使用するときに作成する、すべての成果物を操作するための一元的な場所が提供されます。

ワークスペースには、スクリプトのログ、メトリック、出力、スナップショットなど、トレーニング実行の履歴が保持されています。 この情報を使用して、最適なモデルを生成するトレーニング実行を判断します。  

必要なモデルを作成したら、ワークスペースに登録します。 登録済みモデルとスコアリング スクリプトを使用して、Azure Container Instances、Azure Kubernetes Service、または Field-Programmable Gate Array (FPGA) に REST ベースの HTTP エンドポイントとしてデプロイします。 モジュールとして Azure IoT Edge デバイスにモデルをデプロイすることもできます。

## <a name="taxonomy"></a>分類 

ワークスペースの分類を次の図に示します。

[![ワークスペースの分類](./media/concept-azure-machine-learning-architecture/azure-machine-learning-taxonomy.png)](./media/concept-azure-machine-learning-architecture/azure-machine-learning-taxonomy.png#lightbox)

この図は、ワークスペースの次のコンポーネントを示しています。

+ ワークスペースには、Azure Machine Learning の実行に必要な Python 環境で構成されたクラウド リソース、[Notebook VM](quickstart-run-cloud-notebook.md) を含めることができます。
+ [ユーザー ロール](how-to-assign-roles.md)を使用すると、お使いのワークスペースを、他のユーザー、チーム、またはプロジェクトと共有できます。
+ [コンピューティング ターゲット](concept-azure-machine-learning-architecture.md#compute-target)は、ご自身の実験の実行に使用されます。
+ ワークスペースを作成すると、[関連するリソース](#resources)も自動的に作成されます。
+ [実験](concept-azure-machine-learning-architecture.md#experiment)は、ご自身のモデルの構築に使用するトレーニング実行です。  以下を使用して、実験を作成および実行できます
    + [Azure Machine Learning SDK for Python](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py)。
    + Azure portal の[自動化された機械学習の実験 (プレビュー)](how-to-create-portal-experiments.md) セクション。
    + [ビジュアル インターフェイス (プレビュー)](ui-concept-visual-interface.md)。
+ [パイプライン](concept-azure-machine-learning-architecture.md#pipeline)は、お使いのモデルをトレーニングおよび再トレーニングするための再利用可能なワークフローです。
+ [データセット](concept-azure-machine-learning-architecture.md#dataset)は、モデルのトレーニングとパイプラインの作成に使用するデータの管理に役立ちます。
+ デプロイするモデルを作成したら、[登録済みモデル](concept-azure-machine-learning-architecture.md#model-registry)を作成します。
+ 登録したモデルとスコアリング スクリプトを使用して、[デプロイ](concept-azure-machine-learning-architecture.md#image-registry)を作成します。

## <a name="tools-for-workspace-interaction"></a>ワークスペース操作のためのツール

ご自身のワークスペースは、次の方法で操作できます。

+ Web 上:
    + [Azure Portal](https://azure.portal.com)
    + [ビジュアル インターフェイス (プレビュー)](ui-concept-visual-interface.md)
+ Python では Azure Machine Learning [SDK](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py) を使用
+ コマンド ラインでは Azure Machine Learning [CLI 拡張機能](https://docs.microsoft.com/azure/machine-learning/service/reference-azure-machine-learning-cli)を使用

## <a name="machine-learning-with-a-workspace"></a>ワークスペースを使用した機械学習

機械学習タスクでは、お使いのワークスペースに対する成果物の読み取り/書き込みが行われます。 

+ 実験を実行してモデルをトレーニングします - 実験の実行結果がワークスペースに書き込まれます。
+ 自動 ML を使用してモデルをトレーニングします - トレーニング結果がワークスペースに書き込まれます。
+ ワークスペースでモデルを登録します。
+ モデルをデプロイします - 登録したモデルを使用してデプロイが作成されます。
+ 再利用可能なワークフローを作成して実行します。
+ 実験、パイプライン、モデル、デプロイなどの機械学習の成果物を表示します。
+ モデルを追跡および監視します。

## <a name="workspace-management"></a>ワークスペースの管理

次のワークスペース管理タスクを実行することもできます。

| ワークスペース管理タスク   | ポータル              | SDK        | CLI        |
|---------------------------|------------------|------------|------------|
| ワークスペースの作成        | **&check;**     | **&check;** | **&check;** |
| コンピューティング リソースを作成して管理する    | **&check;**   | **&check;** |  **&check;**   |
| ワークスペース アクセスの管理    | **&check;**   | |  **&check;**    |
| ノートブック VM を作成する | **&check;**   | |     |

[ワークスペースを作成](setup-create-workspace.md)してサービスの使用を開始します。

## <a name="resources"></a> 関連するリソース

新しいワークスペースを作成すると、ワークスペースによって使用される複数の Azure リソースが自動的に作成されます。

+ [Azure Container Registry](https://azure.microsoft.com/services/container-registry/):トレーニング中およびモデルのデプロイ時に使用する Docker コンテナーを登録します。 コストを最小限に抑えるために、ACR は、デプロイ イメージが作成されるまで**遅延読み込み**されます。
+ [Microsoft Azure Storage アカウント](https://azure.microsoft.com/services/storage/):ワークスペースの既定のデータストアとして使用されます。
+ [Azure Application Insights](https://azure.microsoft.com/services/application-insights/):モデルに関する監視情報を格納します。
+ [Azure Key Vault](https://azure.microsoft.com/services/key-vault/):コンピューティング先で使用されるシークレット、およびワークスペースで必要な他の機密情報を格納します。

> [!NOTE]
> 新しいバージョンを作成するだけでなく、既存の Azure サービスを使用することもできます。

## <a name="next-steps"></a>次の手順

Azure Machine Learning service の利用を開始する場合は、以下を参照してください。

+ [Azure Machine Learning service の概要](overview-what-is-azure-ml.md)
+ [ワークスペースを作成する](setup-create-workspace.md)
+ [ワークスペースを管理する](how-to-manage-workspace.md)
+ [チュートリアル:モデルをトレーニングする](tutorial-train-models-with-aml.md)
