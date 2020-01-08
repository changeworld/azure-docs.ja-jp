---
title: scikit-learn 機械学習モデルをトレーニングする
titleSuffix: Azure Machine Learning
description: Azure Machine Learning の SKlearn 推定クラスを使用して、scikit-learn トレーニング スクリプトをエンタープライズ規模で実行する方法について説明します。 サンプル スクリプトは、アイリスの花の画像を分類して、scikit-learn のアイリス データセットに基づく機械学習モデルを構築します。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: maxluk
author: maxluk
ms.date: 08/02/2019
ms.custom: seodec18
ms.openlocfilehash: 60b5b47b305ae809304f86b8f6e12d06b2acbf21
ms.sourcegitcommit: ce4a99b493f8cf2d2fd4e29d9ba92f5f942a754c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/28/2019
ms.locfileid: "75534111"
---
# <a name="build-scikit-learn-models-at-scale-with-azure-machine-learning"></a>Azure Machine Learning を使用して Scikit-learn モデルを大規模に構築する
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

この記事では、Azure Machine Learning の [SKlearn のエスティメーター](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.sklearn.sklearn?view=azure-ml-py) クラスを使用して、scikit-learn トレーニング スクリプトをエンタープライズ規模で実行する方法について説明します。 

この記事のサンプル スクリプトを使用し、アイリスの花の画像を分類して、scikit-learn の[アイリス データセット](https://archive.ics.uci.edu/ml/datasets/iris)に基づく機械学習モデルを構築します。

scikit-learn の機械学習モデルを一からトレーニングする場合でも、既存のモデルをクラウドに持ち込む場合でも、Azure Machine Learning のエラスティック クラウド コンピューティング リソースを使用して、オープンソースのトレーニング ジョブをスケールアウトできます。 Azure Machine Learning を使用して、運用レベルのモデルを構築、配置、バージョン管理、および監視することができます。

## <a name="prerequisites"></a>前提条件

このコードは、次の環境のいずれかで実行してください。
 - Azure Machine Learning コンピューティング インスタンス - ダウンロードやインストールは必要なし

    - 「[チュートリアル: 環境とワークスペースを設定する](tutorial-1st-experiment-sdk-setup.md)」を完了して、SDK とサンプル リポジトリが事前に読み込まれた専用のノートブック サーバーを作成します。
    - Notebook サーバー上の samples トレーニング用フォルダーで、**how-to-use-azureml > ml-frameworks > scikit-learn > training > train-hyperparameter-tune-deploy-with-sklearn** の順に選択してこのディレクトリに移動し、完了済みで展開済みのノートブックを見つけます。

 - 独自の Jupyter Notebook サーバー

    - [Azure Machine Learning SDK をインストールします](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py)。
    - [ワークスペース構成ファイルを作成します](how-to-configure-environment.md#workspace)。
    - データセットとサンプル スクリプト ファイルをダウンロードする 
        - [アイリス データセット](https://archive.ics.uci.edu/ml/datasets/iris)
        - [`train_iris.py`](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/ml-frameworks/scikit-learn/training/train-hyperparameter-tune-deploy-with-sklearn)
    - このガイドの完成した [Jupyter Notebook バージョン](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/ml-frameworks/scikit-learn/training/train-hyperparameter-tune-deploy-with-sklearn/train-hyperparameter-tune-deploy-with-sklearn.ipynb)は、GitHub サンプル ページにもあります。 ノートブックには、インテリジェントなハイパーパラメーターのチューニングをカバーし、プライマリ メトリックによる最適なモデルを取得する、拡張セクションが含まれています。

## <a name="set-up-the-experiment"></a>実験を設定する

このセクションでは、必要な Python パッケージを読み込み、ワークスペースを初期化し、実験を作成し、トレーニング データとトレーニング スクリプトをアップロードすることで、トレーニング実験を設定します。

### <a name="import-packages"></a>パッケージをインポートする

まず、必要な Python ライブラリをインポートします。

```Python
import os
import urllib
import shutil
import azureml

from azureml.core import Experiment
from azureml.core import Workspace, Run

from azureml.core.compute import ComputeTarget, AmlCompute
from azureml.core.compute_target import ComputeTargetException
```

### <a name="initialize-a-workspace"></a>ワークスペースを初期化する

[Azure Machine Learning ワークスペース](concept-workspace.md)は、サービス用の最上位のリソースです。 作成されるすべての成果物を操作できる一元的な場所が用意されています。 Python SDK では、[`workspace`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace.workspace?view=azure-ml-py) オブジェクトを作成することでワークスペースの成果物にアクセスできます。

[前提条件のセクション](#prerequisites)で作成した `config.json` ファイルからワークスペース オブジェクトを作成します。

```Python
ws = Workspace.from_config()
```

### <a name="create-a-machine-learning-experiment"></a>機械学習実験を作成する

トレーニング スクリプトを保存するための実験とフォルダーを作成します。 この例では、"sklearn-iris" という実験を作成します。

```Python
project_folder = './sklearn-iris'
os.makedirs(project_folder, exist_ok=True)

exp = Experiment(workspace=ws, name='sklearn-iris')
```

### <a name="prepare-training-script"></a>トレーニング スクリプトを準備する

このチュートリアルでは、トレーニング スクリプトの **train_iris.py** は既に用意されています。 実際には、コードを変更しなくても、あらゆるカスタム トレーニング スクリプトをそのまま Azure ML で実行できるはずです。

Azure ML の追跡およびメトリック機能を利用するには、トレーニング スクリプト内に少量の Azure ML コードを追加します。  トレーニング スクリプト **train_iris.py** には、スクリプト内の `Run` オブジェクトを使用して Azure ML 実行に一部のメトリックをログ記録する方法があります。

指定されたトレーニング スクリプトでは、`iris = datasets.load_iris()` 関数のサンプル データを使用します。  独自のデータについては、[データセットやスクリプトのアップロード](how-to-train-keras.md#data-upload)などの手順を使用して、トレーニング中にデータを使用できるようにすることが必要になる場合があります。

トレーニング スクリプト **train_iris.py** をプロジェクト ディレクトリにコピーします。

```
import shutil
shutil.copy('./train_iris.py', project_folder)
```

## <a name="create-or-get-a-compute-target"></a>コンピューティング先を作成または取得する

scikit-learn ジョブを実行するためのコンピューティング先を作成します。 scikit-learn では、単一ノードの CPU コンピューティングのみがサポートされてます。

次のコードでは、リモート トレーニング コンピューティング リソース用に Azure Machine Learning のマネージド コンピューティング (AmlCompute) が作成されます。 AmlCompute の作成には約 5 分かかります。 その名前の AmlCompute がワークスペースに既にある場合、このコードの作成プロセスはスキップされます。

```Python
cluster_name = "cpu-cluster"

try:
    compute_target = ComputeTarget(workspace=ws, name=cluster_name)
    print('Found existing compute target')
except ComputeTargetException:
    print('Creating a new compute target...')
    compute_config = AmlCompute.provisioning_configuration(vm_size='STANDARD_D2_V2', 
                                                           max_nodes=4)

    compute_target = ComputeTarget.create(ws, cluster_name, compute_config)

    compute_target.wait_for_completion(show_output=True, min_node_count=None, timeout_in_minutes=20)
```

コンピューティング先の詳細については、[コンピューティング先の概要](concept-compute-target.md)に関する記事を参照してください。

## <a name="create-a-scikit-learn-estimator"></a>scikit-learn のエスティメーターを作成する

[scikit-learn のエスティメーター](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.sklearn?view=azure-ml-py)では、コンピューティング先で scikit-learn トレーニング ジョブを開始する簡単な方法が提供されます。 それは [`SKLearn`](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.sklearn.sklearn?view=azure-ml-py) クラスによって実装されます。このクラスを使って、単一ノードの CPU トレーニングをサポートできます。

トレーニング スクリプトを実行するために追加の pip パッケージまたは conda パッケージが必要な場合は、`pip_packages` および `conda_packages` 引数に名前を渡すことで、パッケージを結果の docker イメージにインストールできます。

```Python
from azureml.train.sklearn import SKLearn

script_params = {
    '--kernel': 'linear',
    '--penalty': 1.0,
}

estimator = SKLearn(source_directory=project_folder, 
                    script_params=script_params,
                    compute_target=compute_target,
                    entry_script='train_iris.py'
                    pip_packages=['joblib']
                   )
```

## <a name="submit-a-run"></a>実行を送信する

[実行オブジェクト](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run%28class%29?view=azure-ml-py)には、ジョブの実行中および完了後の実行履歴へのインターフェイスが用意されています。

```Python
run = experiment.submit(estimator)
run.wait_for_completion(show_output=True)
```

実行は、以下の段階を経て実施されます。

- **準備**:docker イメージは TensorFlow エスティメーターに従って作成されます。 イメージはワークスペースのコンテナー レジストリにアップロードされ、後で実行するためにキャッシュされます。 ログは実行履歴にもストリーミングされ、進行状況を監視するために表示することができます。

- **拡大縮小**:Batch AI クラスターでの実行に現在使用可能な数より多くのノードが必要な場合、クラスターはスケールアップを試みます。

- **[実行中]** : script フォルダー内のすべてのスクリプトがコンピューティング先にアップロードされ、データ ストアがマウントまたはコピーされ、entry_script が実行されます。 stdout および ./logs フォルダーの出力は実行履歴にストリーミングされ、実行を監視するために使用できます。

- **後処理**:実行の ./outputs フォルダーが実行履歴にコピーされます。

## <a name="save-and-register-the-model"></a>モデルを保存して登録する

モデルのトレーニングが終わったら、それをワークスペースに保存して登録できます。 モデルの登録を使用すると、モデルをワークスペースに格納し、バージョン管理して、[モデルの管理とデプロイ](concept-model-management-and-deployment.md)を簡単にすることができます。

モデルを保存するには、トレーニング スクリプト train_iris.py に次のコードを追加します。 

``` Python
import joblib

joblib.dump(svm_model_linear, 'model.joblib')
```

次のコードでワークスペースにモデルを登録します。 パラメーター `model_framework`、`model_framework_version`、および `resource_configuration` を指定することによって、コードなしのモデル デプロイが使用可能になります。 これにより、登録済みのモデルからモデルを Web サービスとして直接デプロイできるようになり、`ResourceConfiguration` オブジェクトによって Web サービスのコンピューティング リソースが定義されます。

```Python
from azureml.core import Model
from azureml.core.resource_configuration import ResourceConfiguration

model = run.register_model(model_name='sklearn-iris', 
                           model_path='model.joblib',
                           model_framework=Model.Framework.SCIKITLEARN,
                           model_framework_version='0.19.1',
                           resource_configuration=ResourceConfiguration(cpu=1, memory_in_gb=0.5))
```

## <a name="deployment"></a>デプロイ

今登録したモデルは、どのエスティメーターをトレーニングに使用したかには関係なく、Azure Machine Learning の他のすべての登録済みのモデルとまったく同じ方法でデプロイできます。 デプロイ方法にはモデルの登録に関するセクションが含まれていますが、登録済みのモデルが既にあるため、デプロイのために[コンピューティング先の作成](how-to-deploy-and-where.md#choose-a-compute-target)に直接スキップできます。

### <a name="preview-no-code-model-deployment"></a>(プレビュー) コードなしのモデル デプロイ

従来のデプロイ ルートの代わりに、scikit-learn にコードなしのデプロイ機能 (プレビュー) を使用することもできます。 コードなしのモデル デプロイは、すべての組み込みの scikit-learn モデルの種類に対してサポートされています。 `model_framework`、`model_framework_version`、および `resource_configuration` パラメーターを使用して前に示したようにモデルを登録することにより、単純に `deploy()` 静的関数を使用してモデルをデプロイできます。

```python
web_service = Model.deploy(ws, "scikit-learn-service", [model])
```

注:事前に構築された scikit-learn 推論コンテナーには、次の依存関係が含まれています。

```yaml
    - azureml-defaults
    - inference-schema[numpy-support]
    - scikit-learn
    - numpy
```

完全な[方法](how-to-deploy-and-where.md)では、Azure Machine Learning を使用したデプロイがさらに詳細に説明されています。


## <a name="next-steps"></a>次のステップ

この記事では、scikit-learn モデルをトレーニングして登録した後、デプロイ オプションについて学習しました。 Azure Machine Learning の詳細については、以下の他の記事をご覧ください。

* [トレーニング中に実行メトリクスを追跡する](how-to-track-experiments.md)
* [ハイパーパラメーターを調整する](how-to-tune-hyperparameters.md)
* [Azure での分散型ディープ ラーニング トレーニングの参照アーキテクチャ](/azure/architecture/reference-architectures/ai/training-deep-learning)
