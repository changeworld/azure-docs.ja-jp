---
title: MLflow Projects を使用してトレーニングする
titleSuffix: Azure Machine Learning
description: ML モデルのメトリックと成果物をログに記録するために、Azure Machine Learning で MLflow を設定します
services: machine-learning
author: cjgronlund
ms.author: cgronlun
ms.service: machine-learning
ms.subservice: core
ms.reviewer: nibaccam
ms.date: 06/16/2021
ms.topic: conceptual
ms.custom: how-to, devx-track-python
ms.openlocfilehash: 7148abe48a3d1c7d79e989f7af825e8eeaab86ed
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/17/2021
ms.locfileid: "132714772"
---
# <a name="train-ml-models-with-mlflow-projects-and-azure-machine-learning-preview"></a>MLflow プロジェクトと Azure Machine Learning を使用して ML モデルをトレーニングする (プレビュー)

[!INCLUDE [preview disclaimer](../../includes/machine-learning-preview-generic-disclaimer.md)]

この記事では、MLflow の追跡 URI とログ API (まとめて [MLflow Tracking](https://mlflow.org/docs/latest/quickstart.html#using-the-tracking-api) と呼ばれる) を有効にし、[MLflow プロジェクト](https://www.mlflow.org/docs/latest/projects.html)と Azure Machine Learning バックエンド サポートを使用してトレーニング ジョブを送信する方法について説明します。 Azure Machine Learning 追跡を使用してローカルにジョブを送信することも、[Azure Machine Learning コンピューティング](./how-to-create-attach-compute-cluster.md)を介するなどして実行をクラウドに移行することもできます。

[MLflow Projects](https://mlflow.org/docs/latest/projects.html) を使用すると、他のデータ サイエンティスト (または自動化ツール) が実行できるようにするために、自分のコードの整理および記述を行うことができます。 Azure Machine Learning で MLflow プロジェクトを使用すると、ワークスペースでのトレーニングの実行を追跡および管理することができます。

[MLflow](https://www.mlflow.org) は、機械学習の実験のライフ サイクルを管理するためのオープンソース ライブラリです。 MLFlow Tracking は MLflow のコンポーネントです。これは、実験の環境がローカル コンピューター、リモートのコンピューティング先、仮想マシン、[Azure Databricks クラスター](how-to-use-mlflow-azure-databricks.md)のいずれであるかにかかわらず、トレーニング実行のメトリックとモデル成果物をログに記録し、追跡します。

[MLflow と Azure Machine Learning の統合の詳細について、ご確認ください](how-to-use-mlflow.md)。

> [!TIP]
> このドキュメントの情報は主に、モデルのトレーニング プロセスを監視したいデータ サイエンティストや開発者を対象としています。 Azure Machine Learning からリソース使用状況やイベント (クォータ、トレーニング実行の完了、モデル デプロイの完了など) を監視することに関心がある管理者の方は、「[Azure Machine Learning の監視](monitor-azure-machine-learning.md)」を参照してください。

## <a name="prerequisites"></a>前提条件

* `azureml-mlflow` パッケージをインストールします。
    * このパッケージからは自動的に、MLflow がワークスペースにアクセスするための接続を提供する、[Azure Machine Learning Python SDK](/python/api/overview/azure/ml/install) の `azureml-core` が持ち込まれます。
* [Azure Machine Learning ワークスペースを作成](how-to-manage-workspace.md)します。
    * [ワークスペースで MLflow 操作を実行するために必要なアクセス許可](how-to-assign-roles.md#mlflow-operations)を確認します。

## <a name="train-mlflow-projects-on-local-compute"></a>ローカル コンピューティングで MLflow プロジェクトをトレーニングする

この例では、Azure Machine Learning 追跡を使用して MLflow プロジェクトをローカルに送信する方法を示します。

実験において Azure Machine Learning で MLflow Tracking をローカルに使用するには、`azureml-mlflow` パッケージをインストールします。 実験は、Jupyter Notebook またはコード エディターを介して実行できます。

```shell
pip install azureml-mlflow
```

MLflow の追跡 URI にアクセスし、ワークスペースを構成するには、`mlflow` および [`Workspace`](/python/api/azureml-core/azureml.core.workspace%28class%29) クラスをインポートします。

```Python
import mlflow
from azureml.core import Workspace

ws = Workspace.from_config()

mlflow.set_tracking_uri(ws.get_mlflow_tracking_uri())
```

`set_experiment()` で MLflow の実験名を設定し、`start_run()` でトレーニング実行を開始します。 次に、`log_metric()` を使用して MLflow ログ API をアクティブにし、トレーニング実行のメトリックのログ記録を開始します。

```Python
experiment_name = 'experiment-with-mlflow-projects'
mlflow.set_experiment(experiment_name)
```

バックエンド構成オブジェクトを作成して、コンピューティング先や使用するマネージド環境の種類など、統合に必要な情報を格納します。

```python
backend_config = {"USE_CONDA": False}
```

ワークスペースのメトリックと重要な成果物を追跡するために、`azureml-mlflow` パッケージを pip 依存関係として環境構成ファイルに追加します。 

``` shell
name: mlflow-example
channels:
  - defaults
  - anaconda
  - conda-forge
dependencies:
  - python=3.6
  - scikit-learn=0.19.1
  - pip
  - pip:
    - mlflow
    - azureml-mlflow
```

ローカル実行を送信し、パラメーター `backend = "azureml" ` を確実に設定します。 この設定を使用すると、実行をローカルに送信して、自動出力追跡、ログ ファイル、スナップショット、およびエラーの出力といったサポートを自分のワークスペースに追加できます。

[Azure Machine Learning Studio](overview-what-is-machine-learning-studio.md) で実行とメトリックを表示します。

```python
local_env_run = mlflow.projects.run(uri=".", 
                                    parameters={"alpha":0.3},
                                    backend = "azureml",
                                    use_conda=False,
                                    backend_config = backend_config, 
                                    )

```

## <a name="train-mlflow-projects-with-remote-compute"></a>リモート コンピューティングで MLflow プロジェクトをトレーニングする

この例では、Azure Machine Learning 追跡を使用して MLflow プロジェクトをリモート コンピューティングに送信する方法を示しています。

実験において Azure Machine Learning で MLflow Tracking をローカルに使用するには、`azureml-mlflow` パッケージをインストールします。 実験は、Jupyter Notebook またはコード エディターを介して実行できます。

```shell
pip install azureml-mlflow
```

MLflow の追跡 URI にアクセスし、ワークスペースを構成するには、`mlflow` および [`Workspace`](/python/api/azureml-core/azureml.core.workspace%28class%29) クラスをインポートします。

```Python
import mlflow
from azureml.core import Workspace

ws = Workspace.from_config()

mlflow.set_tracking_uri(ws.get_mlflow_tracking_uri())
```

`set_experiment()` で MLflow の実験名を設定し、`start_run()` でトレーニング実行を開始します。 次に、`log_metric()` を使用して MLflow ログ API をアクティブにし、トレーニング実行のメトリックのログ記録を開始します。

```Python
experiment_name = 'train-mlflow-project-amlcompute'
mlflow.set_experiment(experiment_name)
```

バックエンド構成オブジェクトを作成して、コンピューティング先や使用するマネージド環境の種類など、統合に必要な情報を格納します。

統合では、"COMPUTE" と "USE_CONDA" をパラメーターとして受け取ります。ここで、"COMPUTE" はリモート コンピューティング クラスターの名前に設定され、"USE_CONDA" は環境構成ファイルからプロジェクト用の新しい環境を作成することを指定します。 オブジェクトに "COMPUTE" が存在する場合、プロジェクトは自動的にリモート コンピューティングに送信され、"USE_CONDA" は無視されます。 MLflow は、ディクショナリ オブジェクトまたは JSON ファイルを受け入れます。

```python
# dictionary
backend_config = {"COMPUTE": "cpu-cluster", "USE_CONDA": False}
```

ワークスペースのメトリックと重要な成果物を追跡するために、`azureml-mlflow` パッケージを pip 依存関係として環境構成ファイルに追加します。 

``` shell
name: mlflow-example
channels:
  - defaults
  - anaconda
  - conda-forge
dependencies:
  - python=3.6
  - scikit-learn=0.19.1
  - pip
  - pip:
    - mlflow
    - azureml-mlflow
```

MLflow プロジェクトの実行を送信し、パラメーター `backend = "azureml" ` を確実に設定します。 この設定を使用すると、実行をリモート コンピューティングに送信して、自動出力追跡、ログ ファイル、スナップショット、エラーの出力といった追加サポートを自分のワークスペースで取得できます。

[Azure Machine Learning Studio](overview-what-is-machine-learning-studio.md) で実行とメトリックを表示します。

```python
remote_mlflow_run = mlflow.projects.run(uri=".", 
                                    parameters={"alpha":0.3},
                                    backend = "azureml",
                                    backend_config = backend_config, 
                                    )

```


## <a name="clean-up-resources"></a>リソースをクリーンアップする

ログに記録されたメトリックと成果物をワークスペースで使用する予定がない場合、現時点では、それらを個別に削除する機能は提供されていません。 代わりに、ストレージ アカウントとワークスペースを含むリソース グループを削除すれば、課金は発生しません。

1. Azure Portal で、左端にある **[リソース グループ]** を選択します。

   ![Azure portal で削除する](./media/how-to-use-mlflow/delete-resources.png)

1. 作成したリソース グループを一覧から選択します。

1. **[リソース グループの削除]** を選択します。

1. リソース グループ名を入力します。 次に、 **[削除]** を選択します。

## <a name="example-notebooks"></a>サンプルの Notebook

[Azure ML ノートブックでの MLflow](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/track-and-monitor-experiments/using-mlflow) は、この記事で提示した概念を示し、さらに詳しく説明します。

  * [ローカル コンピューティングで MLflow プロジェクトをトレーニングする](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/track-and-monitor-experiments/using-mlflow/train-projects-local/train-projects-local.ipynb)
  * [リモート コンピューティングで MLflow プロジェクトをトレーニングする](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/track-and-monitor-experiments/using-mlflow/train-projects-remote/train-projects-remote.ipynb)。

> [!NOTE]
> mlflow を使用したコミュニティ主導の例のリポジトリについては、 https://github.com/Azure/azureml-examples を参照してください。

## <a name="next-steps"></a>次のステップ

* [MLflow を使用してモデルをデプロイします](how-to-deploy-mlflow-models.md)。
* [データの誤差](./how-to-enable-data-collection.md)について実稼働モデルを監視します。
* [MLflow を使用して Azure Databricks 実行を追跡する](how-to-use-mlflow-azure-databricks.md)。
* [モデルを管理します](concept-model-management-and-deployment.md)。
