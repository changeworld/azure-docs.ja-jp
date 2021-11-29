---
title: MLflow と Azure Machine Learning
titleSuffix: Azure Machine Learning
description: MLflow と Azure Machine Learning を使用して ML モデルのメトリックと成果物をログに記録し、ML モデルを Web サービスとしてデプロイすることについて説明します。
services: machine-learning
author: cjgronlund
ms.author: cgronlun
ms.service: machine-learning
ms.subservice: mlops
ms.reviewer: nibaccam
ms.date: 10/21/2021
ms.topic: conceptual
ms.custom: how-to, devx-track-python
ms.openlocfilehash: 7860384cdf905f481b9f003affa3eec4c3e38243
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/17/2021
ms.locfileid: "132706632"
---
# <a name="mlflow-and-azure-machine-learning"></a>MLflow と Azure Machine Learning

[MLflow](https://www.mlflow.org) は、機械学習の実験のライフ サイクルを管理するためのオープンソース ライブラリです。  MLflow の追跡 URI とログ API (まとめて [MLflow Tracking](https://mlflow.org/docs/latest/quickstart.html#using-the-tracking-api) と呼ばれます) は MLflow のコンポーネントであり、実験の環境がローカル コンピューター、リモート コンピューティング ターゲット、仮想マシン、Azure Databricks クラスターのいずれであるかにかかわらず、トレーニング実行のメトリックとモデル成果物をログに記録し、追跡します。 

MLflow Tracking と Azure Machine Learning をまとめて使用すると、実験の実行メトリックを追跡し、Azure Machine Learning ワークスペース内にモデル成果物を保存できます。 その実験は、お使いのコンピューター、リモート コンピューティング ターゲット、仮想マシン、または [Azure Databricks クラスター](how-to-use-mlflow-azure-databricks.md)上でローカルに実行されています。 

## <a name="compare-mlflow-and-azure-machine-learning-clients"></a>MLflow と Azure Machine Learning のクライアントの比較

 次の表に、Azure Machine Learning を使用できるさまざまなクライアントとそれぞれの機能を示します。

 MLflow Tracking は、メトリックのログ機能と成果物の保存機能を提供します。他の方法では、[Azure Machine Learning Python SDK](/python/api/overview/azure/ml/intro) を使用している場合にのみこれらの機能を利用できます。

| 機能 | MLflow Tracking & Deployment | Azure Machine Learning Python SDK |  Azure Machine Learning CLI | Azure Machine Learning Studio|
|---|---|---|---|---|
| ワークスペースの管理 |   | ✓ | ✓ | ✓ |
| データ ストアの使用  |   | ✓ | ✓ | |
| メトリックのログ記録      | ✓ | ✓ |   | |
| 成果物のアップロード | ✓ | ✓ |   | |
| メトリックを表示する     | ✓ | ✓ | ✓ | ✓ |
| コンピューティングの管理   |   | ✓ | ✓ | ✓ |
| モデルをデプロイする    | ✓ | ✓ | ✓ | ✓ |
|モデル パフォーマンスを監視する||✓|  |   |
| データの誤差を検出する |   | ✓ |   | ✓ |


## <a name="track-experiments"></a>実験を追跡する

MLflow Tracking では、MLflow 実験のバックエンドとして Azure Machine Learning を接続できます。 これを行うことで、以下のタスクを実行できます。

+ [Azure Machine Learning ワークスペース](./concept-azure-machine-learning-architecture.md#workspace)で、実験のメトリックと成果物を追跡してログに記録します。 実験に MLflow Tracking を既に使用している場合、トレーニングのメトリックとモデルを保存するための一元化された安全でスケーラブルな場所がワークスペースに用意されています。 

+ MLflow および Azure Machine Learning モデル レジストリでモデルを追跡および管理します。

+ [Azure Databricks のトレーニングの実行を追跡します](how-to-use-mlflow-azure-databricks.md)。

詳細については、「[MLflow と Azure Machine Learning を使用して ML モデルを追跡する](how-to-use-mlflow.md)」を参照してください。 

## <a name="train-mlflow-projects-preview"></a>MLflow プロジェクトをトレーニングする (プレビュー)

[!INCLUDE [preview disclaimer](../../includes/machine-learning-preview-generic-disclaimer.md)]

MLflow の追跡 URI とログ API (まとめて MLflow Tracking と呼ばれます) を使用して、トレーニング ジョブを [MLflow プロジェクト](https://www.mlflow.org/docs/latest/projects.html)と Azure Machine Learning バックエンド サポート (プレビュー) に送信できます。 Azure Machine Learning 追跡を使用してローカルにジョブを送信することも、[Azure Machine Learning コンピューティング](./how-to-create-attach-compute-cluster.md)を介するなどして実行をクラウドに移行することもできます。

詳細については、「[MLflow プロジェクトと Azure Machine Learning を使用して ML モデルをトレーニングする (プレビュー)](how-to-train-mlflow-projects.md)」を参照してください。

## <a name="deploy-mlflow-experiments"></a>MLflow 実験をデプロイする

[MLflow モデルを Azure Web サービスとしてデプロイ](how-to-deploy-mlflow-models.md)できます。これにより、Azure Machine Learning のモデル管理とデータ ドリフト検出の機能を活用し、実稼働モデルに適用できるようになります。

## <a name="next-steps"></a>次のステップ
* [MLflow と Azure Machine Learning を使用して ML モデルを追跡する](how-to-use-mlflow.md)。 
* [MLflow プロジェクトと Azure Machine Learning を使用して ML モデルをトレーニングする (プレビュー)](how-to-train-mlflow-projects.md)。
* [MLflow を使用して Azure Databricks 実行を追跡する](how-to-use-mlflow-azure-databricks.md)。
* [MLflow を使用してモデルをデプロイします](how-to-deploy-mlflow-models.md)。


