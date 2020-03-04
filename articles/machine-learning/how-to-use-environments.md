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
ms.openlocfilehash: cb76c7d7804a7d39e8a18c7a4cf41e9b4e0a7593
ms.sourcegitcommit: 5a71ec1a28da2d6ede03b3128126e0531ce4387d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/26/2020
ms.locfileid: "77623651"
---
# <a name="reuse-environments-for-training-and-deployment-by-using-azure-machine-learning"></a>Azure Machine Learning を使用してトレーニングとデプロイのための環境を再利用する
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

この記事では、Azure Machine Learning の[環境](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py)を作成および管理する方法を学習します。 環境を使用して、プロジェクトのソフトウェアの発展する依存関係を追跡および再現します。

ソフトウェアの依存関係の管理は、開発者にとって一般的なタスクです。 広範な手動によるソフトウェア構成を行わずに、ビルドを再現できることを保証する必要があります。 Azure Machine Learning の `Environment` クラスでは、pip や Conda などのローカル開発ソリューションが考慮され、ローカルと分散型の両方のクラウド開発のためのソリューションが提供されます。

この記事の例は、以下の方法を示します。

* 環境を作成して、パッケージの依存関係を指定する。
* 環境を取得および更新する。
* トレーニングに環境を使用する。
* Web サービスのデプロイに環境を使用する。

Azure Machine Learning の環境のしくみの概要については、[ML 環境の概要](concept-environments.md)に関するページを参照してください。

## <a name="prerequisites"></a>前提条件

* [Azure Machine Learning SDK for Python](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py)
* [Azure Machine Learning ワークスペース](how-to-manage-workspace.md)

## <a name="create-an-environment"></a>環境の作成

以下のセクションでは、実験用の環境を作成できる複数の方法について説明します。

### <a name="use-a-curated-environment"></a>選別された環境を使用する

選別された環境のいずれかを選択して開始できます。 

* _AzureML-Minimal_ 環境には、実行追跡とアセット アップロードを可能にする最小限のパッケージ セットが含まれています。 それを独自の環境の開始点として利用できます。

* _AzureML-Tutorial_ 環境には、一般的なデータ サイエンス パッケージが含まれています。 これらのパッケージには、Scikit-Learn、Pandas、Matplotlib、およびより大きな azureml-sdk パッケージのセットが含まれます。

選別された環境は、キャッシュされた Docker イメージによってバックアップされます。 このバックアップにより、実行準備コストが削減されます。

`Environment.get` メソッドを使用して、選別された環境のいずれかを選択します。

```python
from azureml.core import Workspace, Environment

ws = Workspace.from_config()
env = Environment.get(workspace=ws, name="AzureML-Minimal")
```

次のコードを使用して、選別された環境とそのパッケージを一覧表示できます。

```python
envs = Environment.list(workspace=ws)

for env in envs:
    if env.startswith("AzureML"):
        print("Name",env)
        print("packages", envs[env].python.conda_dependencies.serialize_to_string())
```

> [!WARNING]
>  独自の環境名の先頭には、_AzureML_ プレフィックスを付加しないでください。 このプレフィックスは、選別された環境用に予約されています。

### <a name="instantiate-an-environment-object"></a>環境オブジェクトのインスタンス化

環境を手動で作成するには、SDK から `Environment` クラスをインポートします。 次に、以下のコードを使用して、環境オブジェクトをインスタンス化します。

```python
from azureml.core.environment import Environment
Environment(name="myenv")
```

### <a name="use-conda-and-pip-specification-files"></a>Conda と pip の仕様ファイルを使用する

Conda 仕様または pip 要件ファイルから環境を作成することもできます。 [`from_conda_specification()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py#from-conda-specification-name--file-path-) メソッド、または [`from_pip_requirements()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py#from-pip-requirements-name--file-path-) メソッドを使用します。 メソッドの引数に、ご利用の環境の名前と、必要なファイルのファイル パスを含めます。

```python
# From a Conda specification file
myenv = Environment.from_conda_specification(name = "myenv",
                                             file_path = "path-to-conda-specification-file")

# From a pip requirements file
myenv = Environment.from_pip_requirements(name = "myenv"
                                          file_path = "path-to-pip-requirements-file")
```

### <a name="use-existing-conda-environments"></a>既存の Conda 環境を使用する

ローカル コンピューター上に既存の Conda 環境がある場合は、環境オブジェクトを作成するためのサービスを使用できます。 この戦略を使用することで、ローカルの対話型環境をリモート実行で再利用できます。

次のコードでは、既存の Conda 環境 `mycondaenv` から環境オブジェクトを作成します。 [`from_existing_conda_environment()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py#from-existing-conda-environment-name--conda-environment-name-) メソッドが使用されます。

``` python
myenv = Environment.from_existing_conda_environment(name = "myenv",
                                                    conda_environment_name = "mycondaenv")
```

### <a name="create-environments-automatically"></a>環境を自動的に作成する

トレーニング実行を送信することで、環境を自動的に作成します。 `submit()` メソッドを使用して、実行を送信します。 トレーニング実行を送信するときに、新しい環境のビルドに数分かかる場合があります。 ビルド期間は、必要な依存関係のサイズによって異なります。 

実行を送信する前に実行構成で環境を指定しなかった場合は、自動的に既定の環境が作成されます。

```python
from azureml.core import ScriptRunConfig, Experiment, Environment
# Create experiment 
myexp = Experiment(workspace=ws, name = "environment-example")

# Attach training script and compute target to run config
runconfig = ScriptRunConfig(source_directory=".", script="example.py")
runconfig.run_config.target = "local"

# Submit the run
run = myexp.submit(config=runconfig)

# Show each step of run 
run.wait_for_completion(show_output=True)
```

同様に、[`Estimator`](https://docs.microsoft.com//python/api/azureml-train-core/azureml.train.estimator.estimator?view=azure-ml-py) オブジェクトをトレーニングに使用する場合は、環境を指定せずに、推定インスタンスを実行として直接送信できます。 `Estimator` オブジェクトでは既に、環境とコンピューティング ターゲットがカプセル化されています。

## <a name="add-packages-to-an-environment"></a>環境にパッケージを追加する

Conda、pip、またはプライベート wheel ファイルを使用して、パッケージを環境に追加します。 [`CondaDependency`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.conda_dependencies.condadependencies?view=azure-ml-py) クラスを使用して、各パッケージの依存関係を指定します。 それを環境の `PythonSection` に追加します。

### <a name="conda-and-pip-packages"></a>Conda パッケージと pip パッケージ

パッケージが Conda パッケージ リポジトリで使用可能な場合は、pip インストールではなく、Conda インストールを使用することをお勧めします。 Conda パッケージには通常、インストールの信頼性を高めるビルド済みのバイナリが付属しています。

次の例では環境に追加します。 バージョン 0.21.3 の `scikit-learn` が追加されます。 また、`pillow` パッケージ `myenv` も追加されます。 この例では、[`add_conda_package()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.conda_dependencies.condadependencies?view=azure-ml-py#add-conda-package-conda-package-) メソッドと [`add_pip_package()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.conda_dependencies.condadependencies?view=azure-ml-py#add-pip-package-pip-package-) メソッドがそれぞれ使用されています。

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

プライベート pip wheel ファイルは、最初にワークスペース ストレージにそれらをアップロードすることで使用できます。 静的 [`add_private_pip_wheel()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py#add-private-pip-wheel-workspace--file-path--exist-ok-false-) メソッドを使用して、それらをアップロードします。 次に、ストレージ URL をキャプチャし、その URL を `add_pip_package()` メソッドに渡します。

```python
# During environment creation the service replaces the URL by secure SAS URL, so your wheel file is kept private and secure
whl_url = Environment.add_private_pip_wheel(workspace=ws,file_path = "my-custom.whl")
myenv = Environment(name="myenv")
conda_dep = CondaDependencies()
conda_dep.add_pip_package(whl_url)
myenv.python.conda_dependencies=conda_dep
```

## <a name="manage-environments"></a>環境を管理する

コンピューティング ターゲット全体で、またワークスペースの他のユーザーと共に環境を更新、追跡、再利用できるように環境を管理します。

### <a name="register-environments"></a>環境を登録する

実行を送信するか、Web サービスをデプロイすると、環境が自動的にワークスペースに登録されます。 [`register()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment(class)?view=azure-ml-py#register-workspace-) メソッドを使用して、環境を手動で登録することもできます。 この操作により、環境は、クラウドで追跡およびバージョン管理されるエンティティに変わります。 エンティティは、ワークスペース ユーザー間で共有できます。

次のコードでは、`myenv` 環境を `ws` ワークスペースに登録します。

```python
myenv.register(workspace=ws)
```

トレーニングまたはデプロイで初めて環境を使用するときに、ワークスペースに登録されます。 その後、コンピューティング ターゲットにビルドおよびデプロイされます。 環境はサービスによってキャッシュされます。 キャッシュされた環境を再利用すると、新しいサービスや更新されたサービスを使用するよりもずっと短時間で済みます。

### <a name="get-existing-environments"></a>既存の環境を取得する

`Environment` クラスでは、ワークスペース内の既存の環境を取得できるメソッドが提供されます。 環境は、名前、一覧、または特定のトレーニング実行によって取得できます。 この情報は、トラブルシューティングや監査に、また再現性を確保するのに役立ちます。

#### <a name="view-a-list-of-environments"></a>環境の一覧を表示する

[`Environment.list(workspace="workspace_name")`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment(class)?view=azure-ml-py#list-workspace-) クラスを使用して、ワークスペース内の環境を表示します。 次に、再利用する環境を選択します。

#### <a name="get-an-environment-by-name"></a>名前を指定して環境を取得する

名前とバージョンを指定して特定の環境を取得することもできます。 次のコードでは、[`get()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment(class)?view=azure-ml-py#get-workspace--name--version-none-) メソッドを使用して、`ws` ワークスペース上のバージョン `1` の `myenv` 環境を取得します。

```python
restored_environment = Environment.get(workspace=ws,name="myenv",version="1")
```

#### <a name="train-a-run-specific-environment"></a>実行固有の環境をトレーニングする

トレーニングが完了した後に特定の実行に使用された環境を取得するには、`Run` クラスの [`get_environment()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run.run?view=azure-ml-py#get-environment--) メソッドを使用します。

```python
from azureml.core import Run
Run.get_environment()
```

### <a name="update-an-existing-environment"></a>既存の環境を更新する

たとえば、Python パッケージを追加するなどして、既存の環境を変更したとします。 その後、実行の送信、モデルのデプロイ、または環境の手動登録を行うときに、新しいバージョンの環境が作成されます。 バージョン管理を使用すると、時間の経過に伴う環境の変化を確認できます。

既存の環境の Python パッケージ バージョンを更新するには、そのパッケージのバージョン番号を指定します。 正確なバージョン番号を使用しない場合、Azure Machine Learning では、元のパッケージ バージョンで既存の環境が再利用されます。

### <a name="debug-the-image-build"></a>イメージ ビルドをデバッグする

次の例では、[`build()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment(class)?view=azure-ml-py#build-workspace-) メソッドを使用して、環境を Docker イメージとして手動で作成します。 [`wait_for_completion()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.image(class)?view=azure-ml-py#wait-for-creation-show-output-false-) を使用して、イメージ ビルドからの出力ログを監視します。 その後、ビルドされたイメージは、ワークスペースの Azure Container Registry インスタンスに表示されます。 この情報はデバッグに役立ちます。

```python
from azureml.core import Image
build = env.build(workspace=ws)
build.wait_for_completion(show_output=True)
```

## <a name="enable-docker"></a>Docker を有効にする

 Azure Machine Learning `Environment` クラスの [`DockerSection`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.dockersection?view=azure-ml-py) では、トレーニングを実行するゲスト オペレーティング システムを細かくカスタマイズおよび制御できます。

Docker を有効にすると、サービスによって Docker イメージがビルドされます。 また、その Docker コンテナー内で指定された設定を使用する Python 環境も作成されます。 この機能により、トレーニングの実行に関する分離性と再現性が向上します。

```python
# Creates the environment inside a Docker container.
myenv.docker.enabled = True
```

既定では、新しくビルドされた Docker イメージは、ワークスペースに関連付けられているコンテナー レジストリに表示されます。  リポジトリ名の形式は *azureml/azureml_\<uuid\>* です。 名前の一意識別子 (*uuid*) の部分は、環境構成から計算されたハッシュに対応します。 この対応により、サービスでは、特定の環境のイメージが既に存在していて再利用できるかどうかを判断できます。

また、サービスでは、Ubuntu Linux ベースの[基本イメージ](https://github.com/Azure/AzureML-Containers)のいずれかが自動的に使用されます。 指定された Python パッケージがインストールされます。 基本イメージには、CPU バージョンと GPU バージョンがあります。 Azure Machine Learning では、使用するバージョンが自動的に検出されます。

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
> カスタム Docker イメージの使用中に `environment.python.user_managed_dependencies=False` を指定すると、サービスによって、イメージ内に Conda 環境がビルドされます。 基本イメージにインストールした Python ライブラリを使用するのではなく、その環境で実行されます。 独自にインストールしたパッケージを使用するには、パラメーターを `True` に設定します。

## <a name="use-environments-for-training"></a>トレーニングに環境を使用する

トレーニング実行を送信するには、環境、[コンピューティング ターゲット](concept-compute-target.md)、トレーニング Python スクリプトを実行構成に結合する必要があります。 この構成は、実行の送信に使用されるラッパー オブジェクトです。

トレーニング実行を送信するときに、新しい環境のビルドに数分かかる場合があります。 その期間は、必要な依存関係のサイズによって異なります。 環境はサービスによってキャッシュされます。 そのため、環境定義が変更されていない限り、完全なセットアップの時間が発生するのは一度だけです。

次のローカル スクリプトの実行例では、[`ScriptRunConfig`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.script_run_config.scriptrunconfig?view=azure-ml-py) をラッパー オブジェクトとして使用する場所を示しています。

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

### <a name="use-an-estimator-for-training"></a>トレーニングに見積もりツールを使用する

トレーニングに[見積りツール](how-to-train-ml-models.md)を使用する場合は、推定インスタンスを直接送信できます。 環境とコンピューティング ターゲットは既にカプセル化されています。

次のコードでは、単一ノードのトレーニング実行で見積もりツールを使用します。 これは、`scikit-learn` モデルのリモート コンピューティングで実行されます。 以前にコンピューティング ターゲット オブジェクト `compute_target`、およびデータストア オブジェクト `ds` を作成したことを前提とします。

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

## <a name="use-environments-for-web-service-deployment"></a>Web サービスのデプロイに環境を使用する

モデルを Web サービスとしてデプロイするときに、環境を使用できます。 この機能により、再現可能な、接続されたワークフローが有効になります。 このワークフローでは、トレーニング コンピューティングと推論計算の両方で同じライブラリを使用して、モデルのトレーニング、テスト、およびデプロイを行うことができます。

Web サービスをデプロイするには、環境、推論計算、スコアリング スクリプト、および登録済みモデルをデプロイ オブジェクト [`deploy()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py#deploy-workspace--name--models--inference-config-none--deployment-config-none--deployment-target-none--overwrite-false-) に結合します。 詳細については、「[モデルをデプロイする方法と場所](how-to-deploy-and-where.md)」を参照してください。

この例では、トレーニングの実行が完了していることを前提とします。 次に、そのモデルを Azure Container Instances にデプロイします。 Web サービスをビルドすると、モデルとスコアリング ファイルがイメージにマウントされ、Azure Machine Learning 推論スタックがイメージに追加されます。

```python
from azureml.core.model import InferenceConfig, Model
from azureml.core.webservice import AciWebservice, Webservice

# Register the model to deploy
model = run.register_model(model_name = "mymodel", model_path = "outputs/model.pkl")

# Combine scoring script & environment in Inference configuration
inference_config = InferenceConfig(entry_script="score.py", environment=myenv)

# Set deployment configuration
deployment_config = AciWebservice.deploy_configuration(cpu_cores = 1, memory_gb = 1)

# Define the model, inference, & deployment configuration and web service name and location to deploy
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

この[サンプルの Notebook](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/deployment/spark) では、Spark モデルを Web サービスとしてデプロイする方法が示されます。

## <a name="create-and-manage-environments-with-the-cli"></a>CLI を使用して環境を作成および管理する

[Azure Machine Learning CLI](reference-azure-machine-learning-cli.md) では、Python SDK のほとんどの機能をミラーリングします。 それを使用して、環境を作成および管理することができます。 このセクションで説明するコマンドでは、基本的な機能が示されています。

以下のコマンドでは、指定されたディレクトリ内の既定の環境定義のファイルをスキャフォールディングします。 これらのファイルは JSON ファイルです。 これらは、SDK の対応するクラスと同じように機能します。 これらのファイルを使用すると、カスタム設定を含む新しい環境を作成できます。 

```azurecli-interactive
az ml environment scaffold -n myenv -d myenvdir
```

次のコマンドを実行して、指定されたディレクトリから環境を登録します。

```azurecli-interactive
az ml environment register -d myenvdir
```

次のコマンドを実行して、登録されたすべての環境を一覧表示します。

```azurecli-interactive
az ml environment list
```

次のコマンドを使用して、登録された環境をダウンロードします。

```azurecli-interactive
az ml environment download -n myenv -d downloaddir
```

## <a name="next-steps"></a>次のステップ

* マネージド コンピューティング ターゲットを使用してモデルをトレーニングする場合は、[チュートリアル: モデルのトレーニング](tutorial-train-models-with-aml.md)に関するページを参照してください。
* モデルのトレーニングが完了した後、[モデルをデプロイする方法と場所](how-to-deploy-and-where.md)について学習します。
* [`Environment` クラスの SDK リファレンス](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment(class)?view=azure-ml-py)を参照します。
* この記事で説明されている概念とメソッドの詳細については、[サンプルの Notebook](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/training/using-environments) を参照してください。
