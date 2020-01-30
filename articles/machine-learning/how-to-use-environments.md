---
title: 再利用可能な ML 環境を作成する
titleSuffix: Azure Machine Learning
description: モデルのトレーニングとデプロイのための環境を作成し、管理します。 環境の Python パッケージとその他の設定を管理します。
services: machine-learning
author: rastala
ms.author: roastala
ms.reviewer: nibaccam
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.date: 01/06/2020
ms.openlocfilehash: de1e01e56079753a1a9ee8debc2f2172e1dc8375
ms.sourcegitcommit: 87781a4207c25c4831421c7309c03fce5fb5793f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/23/2020
ms.locfileid: "76548307"
---
# <a name="reuse-environments-for-training--deployment-with-azure-machine-learning"></a>Azure Machine Learning を使用してトレーニングとデプロイのための環境を再利用します。
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

この記事では、Azure Machine Learning の[環境](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py)を作成および管理し、プロジェクトのソフトウェア依存関係の変化を追跡して再現できるようにする方法について説明します。

ソフトウェアの依存関係の管理は、開発者にとって一般的なタスクです。 手動で多くのソフトウェアを構成しなくても、ビルドの再現可能性を保証できることが理想的です。 Azure Machine Learning の Environments クラスは、ローカル開発のための pip や Conda などのソリューションを念頭に、ローカルと分散型両方のクラウド開発のためのソリューションを提供します。

この記事の例は、以下の方法を示します。

* 環境を作成してパッケージの依存関係を指定する
* 環境を取得および更新する
* トレーニングに環境を使用する
* Web サービスのデプロイに環境を使用する

Azure Machine Learning の環境のしくみの概要については、[概念に関する記事](concept-environments.md)を参照してください。

## <a name="prerequisites"></a>前提条件

* Azure Machine Learning SDK for Python が[インストール](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py)されていること。
* [Azure Machine Learning ワークスペース](how-to-manage-workspace.md)。

## <a name="create-an-environment"></a>環境の作成

実験用の環境を作成するには、複数の方法があります。

### <a name="use-curated-environment"></a>キュレートされた環境を使用する

キュレートされた環境の 1 つを選択し、開始できます。 

* __AzureML-Minimal__ 環境には、実行追跡とアセット アップロードを可能にする最小限のパッケージ セットが含まれています。 それを独自の環境の開始点として利用できます。

* __AzureML-Tutorial__ 環境には、Scikit-Learn、Pandas、Matplotlib など、一般的なデータ サイエンス パッケージと大きな azureml-sdk パッケージ セットが含まれています。

キュレートされた環境は Docker イメージのキャッシュでバックアップされ、実行準備コストを下げます。

__Environment.get__ メソッドを使用し、次のいずれかのキュレートされた環境を選択します。

```python
from azureml.core import Workspace, Environment

ws = Workspace.from_config()
env = Environment.get(workspace=ws, name="AzureML-Minimal")
```

次のコードを利用し、キュレートされた環境とそのパッケージを一覧表示できます。
```python
envs = Environment.list(workspace=ws)

for env in envs:
    if env.startswith("AzureML"):
        print("Name",env)
        print("packages", envs[env].python.conda_dependencies.serialize_to_string())
```

> [!WARNING]
>  独自の環境の名前にはプレフィックスとして _AzureML_ を使用しないでください。 これはキュレートされた環境用に予約されています。

### <a name="instantiate-an-environment-object"></a>環境オブジェクトのインスタンス化

環境を手動で作成するには、SDK から Environment クラスをインポートし、次のコードを使用して環境オブジェクトをインスタンス化します。

```python
from azureml.core.environment import Environment
Environment(name="myenv")
```

### <a name="conda-and-pip-specification-files"></a>Conda と pip の仕様ファイル

Conda 仕様または pip 要件ファイルから環境を作成することもできます。
[from_conda_specification()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py#from-conda-specification-name--file-path-) または [from_pip_requirements()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py#from-pip-requirements-name--file-path-) メソッドを使用し、環境名と目的のファイルのパスをメソッドの引数に含めます。

```python
# From a Conda specification file
myenv = Environment.from_conda_specification(name = "myenv",
                                             file_path = "path-to-conda-specification-file")

#From a pip requirements file
myenv = Environment.from_pip_requirements(name = "myenv"
                                          file_path = "path-to-pip-requirements-file")
```

### <a name="existing-conda-environment"></a>既存の Conda 環境

ローカル コンピューター上に既存の Conda 環境がある場合、それから環境オブジェクトを作成するためのソリューションがサービスによって提供されます。 これにより、ローカルの対話型環境をリモート実行で再利用できます。

次のコードでは、既存の Conda 環境 `mycondaenv` から、[from_existing_conda_environment()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py#from-existing-conda-environment-name--conda-environment-name-) メソッドを使用して環境オブジェクトを作成します。

``` python
myenv = Environment.from_existing_conda_environment(name = "myenv",
                                                    conda_environment_name = "mycondaenv")
```

### <a name="automatically-create-environments"></a>環境を自動的に作成する

submit() メソッドを使用してトレーニング実行を送信することによって、環境を自動的に作成します。 トレーニング実行を送信すると、必要な依存関係のサイズによっては、新しい環境の構築に数分かかる場合があります。 

実行を送信する前に実行構成で環境を指定しなかった場合は、既定の環境が作成されます。

```python
from azureml.core import ScriptRunConfig, Experiment, Environment
# Create experiment 
myexp = Experiment(workspace=ws, name = "environment-example")

# Attaches training script and compute target to run config
runconfig = ScriptRunConfig(source_directory=".", script="example.py")
runconfig.run_config.target = "local"

# Submit the run
run = myexp.submit(config=runconfig)

# Shows each step of run 
run.wait_for_completion(show_output=True)
```

同様に、[`Estimator`](https://docs.microsoft.com//python/api/azureml-train-core/azureml.train.estimator.estimator?view=azure-ml-py) オブジェクトをトレーニングに使用する場合、環境を指定しなくても推定インスタンスを実行として直接送信できます。 `Estimator` オブジェクトが既に環境とコンピューティング ターゲットをカプセル化しています。


## <a name="add-packages-to-an-environment"></a>環境にパッケージを追加する

Conda、pip、またはプライベート wheel ファイルを使用してパッケージを環境に追加します。 [CondaDependency クラス](https://docs.microsoft.com/python/api/azureml-core/azureml.core.conda_dependencies.condadependencies?view=azure-ml-py)を使用して各パッケージの依存関係を指定し、それを環境の PythonSection に追加します。

### <a name="conda-and-pip-packages"></a>Conda パッケージと pip パッケージ

Conda パッケージ リポジトリでパッケージが利用可能な場合、Conda over pip インストールを使用することをお勧めします。 理由は、Conda パッケージには通常、インストールの信頼性を高めるビルド済みのバイナリが付属しているからです。

次の例では、環境 `myenv` に、[`add_conda_package()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.conda_dependencies.condadependencies?view=azure-ml-py#add-conda-package-conda-package-) メソッドを使用して `scikit-learn` (具体的にはバージョン 0.21.3) を、また [`add_pip_package()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.conda_dependencies.condadependencies?view=azure-ml-py#add-pip-package-pip-package-) メソッドを使用して `pillow` パッケージを追加します。

```python
from azureml.core.environment import Environment
from azureml.core.conda_dependencies import CondaDependencies

myenv = Environment(name="myenv")
conda_dep = CondaDependencies()

# Installs scikit-learn version 0.21.3 conda package
conda_dep.add_conda_package("scikit-learn==0.21.3")

# Adds dependencies to PythonSection of myenv
myenv.python.conda_dependencies=conda_dep
```

### <a name="private-wheel-files"></a>プライベート wheel ファイル

プライベート pip wheel ファイルを使用できます。それには、まず静的な [`add_private_pip_wheel()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py#add-private-pip-wheel-workspace--file-path--exist-ok-false-) メソッドを使用してファイルをワークスペース ストレージにアップロードし、次にストレージ URL をキャプチャして、URL を `add_pip_package()` メソッドに渡します。

```python
# During environment creation the service replaces the URL by secure SAS URL, so your wheel file is kept private and secure
whl_url = Environment.add_private_pip_wheel(workspace=ws,file_path = "my-custom.whl")
myenv = Environment(name="myenv")
conda_dep = CondaDependencies()
conda_dep.add_pip_package(whl_url)
myenv.python.conda_dependencies=conda_dep
```

## <a name="manage-environments"></a>環境を管理する

コンピューティング ターゲット全体で、またワークスペースの他のユーザーと一緒に環境を更新、追跡、再利用できるよう、環境を管理します。

### <a name="register-environments"></a>環境を登録する

実行を送信するか、Web サービスをデプロイすると、環境が自動的にワークスペースに登録されます。 [register()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment(class)?view=azure-ml-py#register-workspace-) メソッドを使用して、環境を手動で登録することもできます。 これは環境をエンティティに変える操作であり、このエンティティはクラウド内で追跡およびバージョン管理され、ワークスペースのユーザー間で共有することができます。

次のコードでは、環境 `myenv` をワークスペース `ws` に登録します。

```python
myenv.register(workspace=ws)
```

トレーニングまたはデプロイで初めて使用するときに、コンピューティング ターゲット上で環境がワークスペースに登録、ビルド、およびデプロイされます。 環境はサービスによってキャッシュされます。 キャッシュされた環境を再利用すると、新しいサービスや更新されたサービスを使用するよりもずっと短時間で済みます。

### <a name="get-existing-environments"></a>既存の環境を取得する

Environment クラスには、ご利用のワークスペース内の既存の環境を、名前によって、リストとして、またはトラブルシューティングまたは監査目的での特定のトレーニング実行および再現性によって取得できるメソッドが用意されています。

#### <a name="view-list-of-environments"></a>環境の一覧を表示する

[`Environment.list(workspace="workspace_name")`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment(class)?view=azure-ml-py#list-workspace-) を使用してワークスペース内の環境を表示し、再利用する環境を選択します。

#### <a name="get-environment-by-name"></a>名前を指定して環境を取得する

名前とバージョンを指定して特定の環境を取得することもできます。
次のコードでは、[get()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment(class)?view=azure-ml-py#get-workspace--name--version-none-) メソッドを使用して、`ws` ワークスペース上の環境 `myenv` のバージョン `1` を取得します。

```python
restored_environment = Environment.get(workspace=ws,name="myenv",version="1")
```

#### <a name="training-run-specific-environment"></a>トレーニング実行固有の環境

トレーニングが完了した後に、特定の実行に使用される環境を取得するには、Run クラスで [get_environment()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run.run?view=azure-ml-py#get-environment--) メソッドを使用します。

```python
from azureml.core import Run
Run.get_environment()
```

### <a name="update-an-existing-environment"></a>既存の環境を更新する

Python パッケージの追加など、既存の環境に変更を加えた場合、実行を送信、モデルをデプロイ、または環境を手動で登録した時点で環境の新しいバージョンが作成されます。 バージョン管理を使用すると、時間の経過に伴う環境の変更を表示できます。

既存の環境の Python パッケージ バージョンを更新するには、そのパッケージの正確なバージョン番号を指定します。 それ以外の場合、Azure Machine Learning は、環境が作成された時点のパッケージ バージョンを含む既存の環境を再利用します。

### <a name="debug-the-image-build"></a>イメージ ビルドをデバッグする

この例では、[build()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment(class)?view=azure-ml-py#build-workspace-) メソッドを使用して手動で Docker イメージとして環境を作成し、[wait_for_completion()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.image(class)?view=azure-ml-py#wait-for-creation-show-output-false-) を使用してイメージ ビルドからの出力ログを監視します。 ビルドされたイメージは、デバッグに役立つワークスペース Azure Container Registry の下に表示されます。

```python
from azureml.core import Image
build = env.build(workspace=ws)
build.wait_for_completion(show_output=True)
```

## <a name="docker-and-environments"></a>Docker と環境

 Azure Machine Learning `Environments` クラスの [DockerSection](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.dockersection?view=azure-ml-py) では、トレーニングの実行の場所であるゲスト オペレーティング システムを詳細にカスタマイズおよび制御できます。

Docker を `enable` にすると、サービスは Docker イメージを構築し、指定された設定でその Docker コンテナー内に Python 環境を作成します。 これにより、トレーニングの実行に関する分離性と再現性が向上します。

```python
# Creates the environment inside a Docker container.
myenv.docker.enabled = True
```

ビルドされた Docker イメージは既定で、ワークスペースに関連付けられている Azure Container Registry に表示されます。  リポジトリ名の形式は *azureml/azureml_\<uuid\>* です。 一意識別子 (*uuid*) の部分は、環境構成から計算されたハッシュに対応します。 これにより、サービスは、特定の環境に対応するイメージが既に存在していて再利用できるかどうかを判断できます。

さらに、サービスは、Ubuntu Linux ベースの[基本イメージ](https://github.com/Azure/AzureML-Containers)の 1 つを自動的に使用し、指定された Python パッケージをインストールします。 基本イメージには、CPU と GPU のバージョンが含まれます。 Azure Machine Learning では、使用するバージョンが自動的に検出されます。

```python
# Specify custom Docker base image and registry, if you don't want to use the defaults
myenv.docker.base_image="your_base-image"
myenv.docker.base_image_registry="your_registry_location"
# Alternatively, you can specify the contents of dockerfile of your base image
with open("docker_file_of_your_base_image", "r") as f:
    dockerfile_contents_of_your_base_image=f.read()
myenv.docker.base_dockerfile=dockerfile_contents_of_your_base_image 
```

> [!NOTE]
> カスタム Docker イメージの使用中に `environment.python.user_managed_dependencies=False` を指定した場合、サービスはイメージ内に Conda 環境を構築して、その環境で実行します。基本イメージにインストールされている可能性がある Python ライブラリは使用しません。 独自にインストールしたパッケージを使用するには、パラメーターを `True` に設定します。

## <a name="using-environments-for-training"></a>トレーニングに環境を使用する

トレーニング実行を送信するには、環境、[コンピューティング ターゲット](concept-compute-target.md)、トレーニング Python スクリプトを結合して、実行の送信に使用されるラッパー オブジェクトである実行構成を作成する必要があります。

トレーニング実行を送信すると、必要な依存関係のサイズによっては、新しい環境の構築に数分かかる場合があります。 環境はサービスによってキャッシュされるため、環境定義が変更されない限り、完全セットアップにかかる時間は 1 回だけです。

次のローカル スクリプト実行の例では、[ScriptRunConfig](https://docs.microsoft.com/python/api/azureml-core/azureml.core.script_run_config.scriptrunconfig?view=azure-ml-py) をラッパー オブジェクトとして使用します。

```python
from azureml.core import ScriptRunConfig, Experiment
from azureml.core.environment import Environment

exp = Experiment(name="myexp", workspace = ws)
# Instantiate environment
myenv = Environment(name="myenv")

# Add training script to run config
runconfig = ScriptRunConfig(source_directory=".", script="train.py")

# Attach compute target to run config
runconfig.run_config.target = "local"

# Attach environment to run config
runconfig.run_config.environment = myenv

# Submit run 
run = exp.submit(runconfig)
```

> [!NOTE]
> 実行履歴または実行スナップショットを無効にするには、`ScriptRunConfig.run_config.history` の下の設定を使用します。

実行構成で環境を指定しない場合、実行を送信するときにサービスによって既定の環境が作成されます。

### <a name="train-with-an-estimator"></a>Estimator でトレーニングする

トレーニングのために [Estimator](how-to-train-ml-models.md) を使用している場合、Estimator インスタンスには環境とコンピューティング ターゲットが既にカプセル化されているため、そのインスタンスを直接送信するだけで済みます。

次のコードでは、scikit-learn モデルに対してリモート コンピューティングで実行される単一ノード トレーニングに Estimator を使用し、以前に作成されたコンピューティング ターゲット オブジェクト `compute_target` とデータストア オブジェクト `ds` を想定します。

```python
from azureml.train.estimator import Estimator

script_params = {
    '--data-folder': ds.as_mount(),
    '--regularization': 0.8
}

sk_est = Estimator(source_directory='./my-sklearn-proj',
                   script_params=script_params,
                   compute_target=compute_target,
                   entry_script='train.py',
                   conda_packages=['scikit-learn'])

# Submit the run 
run = experiment.submit(sk_est)
```

## <a name="using-environments-for-web-service-deployment"></a>Web サービスのデプロイに環境を使用する

モデルを Web サービスとしてデプロイするときに環境を使用できます。 これにより、トレーニング計算と推論計算の両方でまったく同じライブラリを使用してモデルをトレーニング、テスト、デプロイできる、再現可能で連続的なワークフローが実現します。

Web サービスをデプロイするには、環境、推論計算、スコアリング スクリプト、および登録済みモデルをデプロイ オブジェクトに結合して、[deploy()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py#deploy-workspace--name--models--inference-config-none--deployment-config-none--deployment-target-none--overwrite-false-) を実行します。 [Web サービスのデプロイ](how-to-deploy-and-where.md)の詳細を確認してください。

この例では、トレーニング実行を完了して、そのモデルを Azure コンテナー インスタンス (ACI) にデプロイすると仮定します。 Web サービスをビルドすると、モデルとスコアリング ファイルがイメージにマウントされ、Azure Machine Learning 推論スタックがイメージに追加されます。

```python
from azureml.core.model import InferenceConfig, Model
from azureml.core.webservice import AciWebservice, Webservice

# Register the model to deploy
model = run.register_model(model_name = "mymodel", model_path = "outputs/model.pkl")

# Combine scoring script & environment in Inference configuration
inference_config = InferenceConfig(entry_script="score.py", environment=myenv)

# Set deployment configuration
deployment_config = AciWebservice.deploy_configuration(cpu_cores = 1, memory_gb = 1)

# Define the model, inference & deployment configuration and web service name and location to deploy
service = Model.deploy(
    workspace = ws,
    name = "my_web_service",
    models = [model],
    inference_config = inference_config,
    deployment_config = deployment_config)
```

## <a name="example-notebooks"></a>サンプルの Notebook

この[サンプル Notebook](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/training/using-environments) は、この記事で説明している概念と方法論をベースに拡張したものです。

[カスタム Docker イメージを使用するモデルのデプロイ方法](how-to-deploy-custom-docker-image.md)に関するページでは、カスタム Docker ベース イメージを使用してモデルをデプロイする方法を示します。

この[サンプル Notebook](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/deployment/spark) は、Spark モデルを Web サービスとしてデプロイする方法を示します。

## <a name="create-and-manage-environments-with-the-cli"></a>CLI を使用して環境を作成および管理する

[Azure Machine Learning CLI](reference-azure-machine-learning-cli.md) は、Python SDK のほとんどの機能を反映し、環境の作成と管理に使用できます。 次のコマンドは、基本的な機能を示しています。

以下のコマンドでは、指定されたディレクトリ内の既定の環境定義のファイルをスキャフォールディングします。 これらのファイルは、SDK の対応するクラスと同様の機能を持つ JSON ファイルであり、カスタム設定で新しい環境を作成するために使用できます。 

```azurecli-interactive
az ml environment scaffold -n myenv -d myenvdir
```

次のコマンドを実行して、指定されたディレクトリから環境を登録します。

```azurecli-interactive
az ml environment register -d myenvdir
```

次のコマンドを実行すると、登録されたすべての環境が一覧表示されます。

```azurecli-interactive
az ml environment list
```

次のコマンドを使用して、登録された環境をダウンロードします。

```azurecli-interactive
az ml environment download -n myenv -d downloaddir
```

## <a name="next-steps"></a>次のステップ

* [チュートリアル:モデルのトレーニング](tutorial-train-models-with-aml.md)に関する記事では、マネージド コンピューティング先を使用してモデルをトレーニングします。
* モデルのトレーニングが済んだら、[モデルをデプロイする方法と場所](how-to-deploy-and-where.md)を確認します。
* [Environment クラス](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment(class)?view=azure-ml-py)の SDK リファレンスを確認します。
