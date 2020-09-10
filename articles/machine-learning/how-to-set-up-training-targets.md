---
title: コンピューティング先にトレーニングの実行を送信する
titleSuffix: Azure Machine Learning
description: さまざまなトレーニング環境 (コンピューティング先) で機械学習モデルをトレーニングします。 トレーニング環境を簡単に切り替えることができます。 ローカルでトレーニングを開始します。 スケール アウトする必要がある場合は、クラウド ベースのコンピューティング先に切り替えます。
services: machine-learning
author: sdgilley
ms.author: sgilley
ms.reviewer: sgilley
ms.service: machine-learning
ms.subservice: core
ms.date: 08/28/2020
ms.topic: conceptual
ms.custom: how-to, devx-track-python, contperfq1
ms.openlocfilehash: ca76ec5bef1d908ca3cea6ce0f58b1205c1676ca
ms.sourcegitcommit: d7352c07708180a9293e8a0e7020b9dd3dd153ce
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/30/2020
ms.locfileid: "89144092"
---
# <a name="submit-a-training-run-to-a-compute-target"></a>コンピューティング先にトレーニングの実行を送信する

[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

この記事では、さまざまなトレーニング環境 ([コンピューティング先](concept-compute-target.md)) を使用して機械学習モデルをトレーニングする方法について説明します。

トレーニングのときは、ローカル コンピューターで開始し、後で別のコンピューティング先でそのトレーニング スクリプトを実行するのが一般的です。 Azure Machine Learning では、トレーニング スクリプトを変更しなくても、さまざまなコンピューティング先でスクリプトを実行できます。

必要なのは、**スクリプト実行構成**内で各コンピューティング先の環境を定義することだけです。  その後、異なるコンピューティング先でトレーニング実験を実行するときは、そのコンピューティングの実行構成を指定します。

## <a name="prerequisites"></a>前提条件

* Azure サブスクリプションをお持ちでない場合は、開始する前に無料アカウントを作成してください。 [無料版または有料版の Azure Machine Learning](https://aka.ms/AMLFree) を試してください
* [Azure Machine Learning SDK for Python](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py)
* [Azure Machine Learning ワークスペース](how-to-manage-workspace.md)、`ws`
* コンピューティング先、`my_compute_target`。  コンピューティング先は次のものを使用して作成します。
  * [Python SDK](how-to-create-attach-compute-sdk.md) 
  * [Azure Machine Learning Studio](how-to-create-attach-compute-studio.md)

## <a name="whats-a-script-run-configuration"></a><a name="whats-a-run-configuration"></a>スクリプト実行構成とは

[ScriptRunConfig](https://docs.microsoft.com/python/api/azureml-core/azureml.core.scriptrunconfig?view=azure-ml-py) オブジェクトを使用して、トレーニング実験を送信します。  このオブジェクトには以下のものが含まれます。

* **source_directory**:トレーニング スクリプトが格納されているソース ディレクトリ
* **script**:トレーニング スクリプトを示します
* **run_config**:トレーニングが行われる場所が順に定義される[実行構成](https://docs.microsoft.com/python/api/azureml-core/azureml.core.runconfiguration?view=azure-ml-py)。 `run_config` では、トレーニング スクリプトの実行時に使用するコンピューティング先と環境を指定します。  

## <a name="whats-an-environment"></a>環境とは

Azure Machine Learning [環境](concept-environments.md)は、機械学習トレーニングが行われる環境をカプセル化したものです。 そこでは、トレーニングとスコアリングのスクリプトに関連する、Python パッケージ、環境変数、およびソフトウェア設定を指定します。 また、実行時間 (Python、Spark、または Docker) も指定します。  

環境は、`ScriptRunConfig` 内の `run_config` オブジェクトで指定されます。

## <a name="train-your-model"></a><a id="submit"></a>モデルをトレーニングする

トレーニングの実行を送信するためのコード パターンは、すべての種類のコンピューティング先について同じです。

1. 実行する実験を作成する
1. スクリプトが実行される環境を作成する
1. コンピューティング先と環境を参照するスクリプト実行構成を作成する
1. 実行の送信
1. 実行が完了するまで待機する

または、次のことができます。

* [Estimator での ML モデルのトレーニング](how-to-train-ml-models.md)に関する記事で示されているように、`Estimator` オブジェクトを使用して実験を送信します。
* [ハイパーパラメーターのチューニング](how-to-tune-hyperparameters.md)用の HyperDrive 実行を送信します。
* [VS Code 拡張機能](tutorial-train-deploy-image-classification-model-vscode.md#train-the-model)を介して実験を送信します。

## <a name="create-an-experiment"></a>実験の作成

ご自分のワークスペース内に実験を作成します。

```python
from azureml.core import Experiment

experiment_name = 'my_experiment'

experiment = Experiment(workspace=ws, name=experiment_name)
```

## <a name="create-an-environment"></a>環境の作成

キュレーションされた環境には、既定でお使いのワークスペースで使用できる Python パッケージのコレクションが含まれています。 これらの環境は、キャッシュされた Docker イメージでバックアップされ、実行の準備コストを下げます。 リモート コンピューティング先の場合は、一般的なキュレーション環境のいずれかを使用して開始できます。

```python
from azureml.core import Workspace, Environment

ws = Workspace.from_config()
my_environment = Environment.get(workspace=ws, name="AzureML-Minimal")
```

環境の詳細については、「[Azure Machine Learning でソフトウェア環境を作成して使用する](how-to-use-environments.md)」をご覧ください。
  
### <a name="local-compute-target"></a>ローカル コンピューティング先

コンピューティング先が**ローカル コンピューター**である場合は、スクリプトが実行される Python 環境で、必要なすべてのパッケージが使用できることをユーザー自身で確認する必要があります。  `python.user_managed_dependencies` を使用すると、現在の Python 環境 (または指定したパス上の Python) を使用できます。

```python
from azureml.core import Environment

# Editing a run configuration property on-fly.
my_environment = Environment("user-managed-env")

my_environment.python.user_managed_dependencies = True

# You can choose a specific Python environment by pointing to a Python path 
#my_environment.python.interpreter_path = '/home/johndoe/miniconda3/envs/myenv/bin/python'
```

## <a name="create-script-run-configuration"></a>スクリプト実行構成を作成する

コンピューティング先 (`compute_target`) と環境 (`my_environment`) が用意できたので、`project_folder` ディレクトリにあるトレーニング スクリプト (`train.py`) を実行するスクリプト実行構成を作成します。

```python
from azureml.core import ScriptRunConfig

script_run_config = ScriptRunConfig(source_directory=project_folder, script='train.py')

# Set compute target
script_run_config.run_config.target = my_compute_target

# Set environment.   If you don't do this, a default environment will be created.
script_run_config.run_config.environment = my_environment
```

また、実行用のフレームワークを設定することもできます。

* HDI クラスターの場合:
    ```python
    src.run_config.framework = "pyspark"
    ```

* リモート仮想マシンの場合:
    ```python
    src.run_config.framework = "python"
    ```

## <a name="submit-the-experiment"></a>実験を送信する

```python
run = experiment.submit(config=script_run_config)
```

> [!IMPORTANT]
> トレーニングの実行を送信すると、トレーニング スクリプトが含まれるディレクトリのスナップショットが作成されて、コンピューティング先に送信されます。 スナップショットは、ご利用のワークスペースにも実験の一部として保存されます。 ファイルを変更して実行を再度送信すると、変更したファイルのみがアップロードされます。
>
> [!INCLUDE [amlinclude-info](../../includes/machine-learning-amlignore-gitignore.md)]
> 
> スナップショットの詳細については、「[スナップショット](concept-azure-machine-learning-architecture.md#snapshots)」をご覧ください。


<a id="gitintegration"></a>

## <a name="git-tracking-and-integration"></a>Git の追跡と統合

ソース ディレクトリがローカル Git リポジトリであるトレーニング実行を開始すると、リポジトリに関する情報が実行履歴に格納されます。 詳細については、「[Azure Machine Learning との Git 統合](concept-train-model-git-integration.md)」を参照してください。

## <a name="notebook-examples"></a>ノートブックの例

さまざまなコンピューティング先を使用したトレーニングの例については、以下のノートブックをご覧ください。
* [how-to-use-azureml/training](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training)
* [tutorials/img-classification-part1-training.ipynb](https://github.com/Azure/MachineLearningNotebooks/blob/master/tutorials/image-classification-mnist-data/img-classification-part1-training.ipynb)

[!INCLUDE [aml-clone-in-azure-notebook](../../includes/aml-clone-for-examples.md)]

## <a name="next-steps"></a>次のステップ

* [チュートリアル:モデルのトレーニング](tutorial-train-models-with-aml.md)に関する記事では、マネージド コンピューティング先を使用してモデルをトレーニングします。
* より優れたモデルを構築するために、[ハイパーパラメーター](how-to-tune-hyperparameters.md)を効率的に調整する方法を学習します。
* モデルのトレーニングが済んだら、[モデルをデプロイする方法と場所](how-to-deploy-and-where.md)を確認します。
* [RunConfiguration クラス](https://docs.microsoft.com/python/api/azureml-core/azureml.core.runconfig.runconfiguration?view=azure-ml-py)の SDK リファレンスを確認します。
* [Azure Machine Learning と Azure Virtual Network を使用する](how-to-enable-virtual-network.md)
