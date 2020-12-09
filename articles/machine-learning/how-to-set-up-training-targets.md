---
title: トレーニングの実行を構成する
titleSuffix: Azure Machine Learning
description: さまざまなトレーニング環境 (コンピューティング先) で機械学習モデルをトレーニングします。 トレーニング環境を簡単に切り替えることができます。
services: machine-learning
author: sdgilley
ms.author: sgilley
ms.reviewer: sgilley
ms.service: machine-learning
ms.subservice: core
ms.date: 09/28/2020
ms.topic: conceptual
ms.custom: how-to, devx-track-python, contperfq1
ms.openlocfilehash: 492d1370a228fc4fc80880102899c9207a514f57
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/01/2020
ms.locfileid: "96447198"
---
# <a name="configure-and-submit-training-runs"></a>トレーニングの実行を構成して送信する

この記事では、モデルをトレーニングするために Azure Machine Learning の実行を構成して送信する方法について説明します。 コード スニペットでは、トレーニング スクリプトの構成と送信の主要な部分について説明します。  その後、[ノートブックの例](#notebooks)のいずれかを使用して、完全なエンドツーエンドの作業例を見つけます。

トレーニングの場合は、ローカル コンピューター上で開始し、後でクラウドベースのクラスターにスケール アウトするのが一般的です。 Azure Machine Learning では、トレーニング スクリプトを変更しなくても、さまざまなコンピューティング先でスクリプトを実行できます。

必要なのは、**スクリプト実行構成** 内で各コンピューティング先の環境を定義することだけです。  その後、異なるコンピューティング先でトレーニング実験を実行するときは、そのコンピューティングの実行構成を指定します。

## <a name="prerequisites"></a>前提条件

* Azure サブスクリプションをお持ちでない場合は、開始する前に無料アカウントを作成してください。 [無料版または有料版の Azure Machine Learning](https://aka.ms/AMLFree) を試してください
* [Azure Machine Learning SDK for Python](/python/api/overview/azure/ml/install?preserve-view=true&view=azure-ml-py) (1.13.0 以降)
* [Azure Machine Learning ワークスペース](how-to-manage-workspace.md)、`ws`
* コンピューティング先、`my_compute_target`。  [コンピューティング ターゲットを作成する](how-to-create-attach-compute-studio.md) 

## <a name="whats-a-script-run-configuration"></a><a name="whats-a-run-configuration"></a>スクリプト実行構成とは
[ScriptRunConfig](/python/api/azureml-core/azureml.core.scriptrunconfig?preserve-view=true&view=azure-ml-py) は、トレーニングの実行を実験の一部として送信するのに必要な情報を構成するために使用されます。

ScriptRunConfig オブジェクトを使用して、トレーニング実験を送信します。  このオブジェクトには以下のものが含まれます。

* **source_directory**:トレーニング スクリプトが格納されているソース ディレクトリ
* **script**:実行するトレーニング スクリプト
* **compute_target**: 実行が行われるコンピューティング ターゲット
* **環境**: スクリプトを実行する場合に使用する環境
* および、いくつかの構成可能な追加のオプション (詳細については、該当する[リファレンス ドキュメント](/python/api/azureml-core/azureml.core.scriptrunconfig?preserve-view=true&view=azure-ml-py)を参照してください)

## <a name="train-your-model"></a><a id="submit"></a>モデルをトレーニングする

トレーニングの実行を送信するためのコード パターンは、すべての種類のコンピューティング先について同じです。

1. 実行する実験を作成する
1. スクリプトが実行される環境を作成する
1. コンピューティング ターゲットと環境を指定する ScriptRunConfig を作成する
1. 実行の送信
1. 実行が完了するまで待機する

または、次のことができます。

* [ハイパーパラメーターのチューニング](how-to-tune-hyperparameters.md)用の HyperDrive 実行を送信します。
* [VS Code 拡張機能](tutorial-train-deploy-image-classification-model-vscode.md#train-the-model)を介して実験を送信します。

## <a name="create-an-experiment"></a>実験の作成

ご自分のワークスペース内に実験を作成します。

```python
from azureml.core import Experiment

experiment_name = 'my_experiment'
experiment = Experiment(workspace=ws, name=experiment_name)
```

## <a name="select-a-compute-target"></a>コンピューティング ターゲットを選択する

トレーニング スクリプトを実行するコンピューティング ターゲットを選択します。 ScriptRunConfig でコンピューティング ターゲットが指定されていない場合、または `compute_target='local'` である場合は、Azure ML によってスクリプトがローカルで実行されます。 

この記事のコード例では、「前提条件」セクションのコンピューティング ターゲット `my_compute_target` が既に作成されていることを前提としています。

## <a name="create-an-environment"></a>環境の作成
Azure Machine Learning [環境](concept-environments.md)は、機械学習トレーニングが行われる環境をカプセル化したものです。 そこでは、トレーニングとスコアリングのスクリプトに関連する、Python パッケージ、Docker イメージ、環境変数、およびソフトウェア設定が指定されます。 また、実行時間 (Python、Spark、または Docker) も指定されます。

独自の環境を定義することも、Azure ML のキュレーションされた環境を使用することもできます。 [キュレーションされた環境](./how-to-use-environments.md#use-a-curated-environment)とは、ワークスペース内で既定で使用できる定義済みの環境です。 これらの環境は、キャッシュされた Docker イメージでバックアップされ、実行の準備コストを下げます。 利用可能なキュレーション環境の完全な一覧については、「[Azure Machine Learning のキュレーションされた環境](./resource-curated-environments.md)」を参照してください。

リモート コンピューティング先の場合は、一般的なキュレーション環境のいずれかを使用して開始できます。

```python
from azureml.core import Workspace, Environment

ws = Workspace.from_config()
myenv = Environment.get(workspace=ws, name="AzureML-Minimal")
```

環境の詳細については、「[Azure Machine Learning でソフトウェア環境を作成して使用する](how-to-use-environments.md)」をご覧ください。
  
### <a name="local-compute-target"></a><a name="local"></a>ローカル コンピューティング先

コンピューティング先が **ローカル コンピューター** である場合は、スクリプトが実行される Python 環境で、必要なすべてのパッケージが使用できることをユーザー自身で確認する必要があります。  `python.user_managed_dependencies` を使用すると、現在の Python 環境 (または指定したパス上の Python) を使用できます。

```python
from azureml.core import Environment

myenv = Environment("user-managed-env")
myenv.python.user_managed_dependencies = True

# You can choose a specific Python environment by pointing to a Python path 
# myenv.python.interpreter_path = '/home/johndoe/miniconda3/envs/myenv/bin/python'
```

## <a name="create-the-script-run-configuration"></a>スクリプトの実行構成を作成する

コンピューティング先 (`my_compute_target`) と環境 (`myenv`) が用意できたので、`project_folder` ディレクトリにあるトレーニング スクリプト (`train.py`) を実行するスクリプト実行構成を作成します。

```python
from azureml.core import ScriptRunConfig

src = ScriptRunConfig(source_directory=project_folder,
                      script='train.py',
                      compute_target=my_compute_target,
                      environment=myenv)

# Set compute target
# Skip this if you are running on your local computer
script_run_config.run_config.target = my_compute_target
```

環境を指定しないと、既定の環境が自動的に作成されます。

トレーニング スクリプトに渡したいコマンドライン引数を用意している場合は、ScriptRunConfig コンストラクターの **`arguments`** パラメーター (`arguments=['--arg1', arg1_val, '--arg2', arg2_val]`など) を使用してそれらを指定することができます。

実行に対する既定の最大許容時間をオーバーライドしたい場合は、 **`max_run_duration_seconds`** パラメーターを使用してそれを行うことができます。 この値よりも時間がかかる場合は、システムによって自動的に実行のキャンセルが試みられます。

### <a name="specify-a-distributed-job-configuration"></a>分散ジョブの構成を指定する
分散トレーニング ジョブを実行する場合は、分散ジョブ固有の構成を **`distributed_job_config`** パラメーターに指定します。 サポートされている構成の種類には、[MpiConfiguration](/python/api/azureml-core/azureml.core.runconfig.mpiconfiguration?preserve-view=true&view=azure-ml-py)、[TensorflowConfiguration](/python/api/azureml-core/azureml.core.runconfig.tensorflowconfiguration?preserve-view=true&view=azure-ml-py)、および [PyTorchConfiguration](/python/api/azureml-core/azureml.core.runconfig.pytorchconfiguration?preserve-view=true&view=azure-ml-py) があります。 

Horovod、TensorFlow、PyTorch の各分散ジョブの実行の詳細と例については、以下を参照してください。

* [TensorFlow モデルをトレーニングする](./how-to-train-tensorflow.md#distributed-training)
* [PyTorch モデルをトレーニングする](./how-to-train-pytorch.md#distributed-training)

## <a name="submit-the-experiment"></a>実験を送信する

```python
run = experiment.submit(config=src)
run.wait_for_completion(show_output=True)
```

> [!IMPORTANT]
> トレーニングの実行を送信すると、トレーニング スクリプトが含まれるディレクトリのスナップショットが作成されて、コンピューティング先に送信されます。 スナップショットは、ご利用のワークスペースにも実験の一部として保存されます。 ファイルを変更して実行を再度送信すると、変更したファイルのみがアップロードされます。
>
> [!INCLUDE [amlinclude-info](../../includes/machine-learning-amlignore-gitignore.md)]
> 
> スナップショットの詳細については、「[スナップショット](concept-azure-machine-learning-architecture.md#snapshots)」をご覧ください。

> [!IMPORTANT]
> **特殊フォルダー** *outputs* および *logs* の 2 つのフォルダーは、Azure Machine Learning によって特別に扱われます｡ トレーニング中に、ルート ディレクトリを基準にした *outputs* と *logs* というフォルダー (それぞれ `./outputs` と `./logs`) にファイルを書き込んだ場合、それらのファイルは自動的に実行履歴にアップロードされて、実行が完了すると、それらのファイルにアクセスできるようになります。
>
> トレーニング中に成果物 (モデル ファイル、チェックポイント、データ ファイル、プロット画像など) を作成するには、`./outputs` フォルダーにそれら成果物を書き込みます。
>
> 同様に、トレーニング実行のログを `./logs` フォルダーに書き込むこともできます。 Azure Machine Learning の [ TensorBoard 統合](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/track-and-monitor-experiments/tensorboard/export-run-history-to-tensorboard/export-run-history-to-tensorboard.ipynb)を利用するには､このフォルダーに TensorBoard のログを書き込むようにします｡ 実行中に、TensorBoard を起動して、それらログをストリーミングできます｡  過去のどの実行についても､後でログを復元することができます。
>
> たとえば､リモート トレーニングの実行後に *outputs* フォルダーに書き込まれたファイルをローカル コンピューターには､次のコードを使用します。 `run.download_file(name='outputs/my_output_file', output_file_path='my_destination_path')`

## <a name="git-tracking-and-integration"></a><a id="gitintegration"></a>Git の追跡と統合

ソース ディレクトリがローカル Git リポジトリであるトレーニング実行を開始すると、リポジトリに関する情報が実行履歴に格納されます。 詳細については、「[Azure Machine Learning との Git 統合](concept-train-model-git-integration.md)」を参照してください。

## <a name="notebook-examples"></a><a name="notebooks"></a>ノートブックの例

さまざまなトレーニング シナリオでの実行の構成例については、次のノートブックを参照してください。
* [さまざまなコンピューティング ターゲット上でのトレーニング](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training)
* [ML フレームワークを使用したトレーニング](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/ml-frameworks)
* [tutorials/img-classification-part1-training.ipynb](https://github.com/Azure/MachineLearningNotebooks/blob/master/tutorials/image-classification-mnist-data/img-classification-part1-training.ipynb)

[!INCLUDE [aml-clone-in-azure-notebook](../../includes/aml-clone-for-examples.md)]

## <a name="next-steps"></a>次のステップ

* [チュートリアル:モデルのトレーニング](tutorial-train-models-with-aml.md)に関する記事では、マネージド コンピューティング先を使用してモデルをトレーニングします。
* [Scikit-learn](how-to-train-scikit-learn.md)、[TensorFlow](how-to-train-tensorflow.md)、[PyTorch](how-to-train-pytorch.md) など、特定の ML フレームワークを使用してモデルをトレーニングする方法を参照してください。
* より優れたモデルを構築するために、[ハイパーパラメーター](how-to-tune-hyperparameters.md)を効率的に調整する方法を学習します。
* モデルのトレーニングが済んだら、[モデルをデプロイする方法と場所](how-to-deploy-and-where.md)を確認します。
* [ScriptRunConfig クラス](/python/api/azureml-core/azureml.core.scriptrunconfig?preserve-view=true&view=azure-ml-py) SDK リファレンスを表示します。
* [Azure Machine Learning と Azure Virtual Network を使用する](./how-to-network-security-overview.md)
