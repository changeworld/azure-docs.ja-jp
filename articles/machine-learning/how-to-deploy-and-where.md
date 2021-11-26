---
title: 機械学習モデルのデプロイ
titleSuffix: Azure Machine Learning
description: 機械学習モデルをデプロイする方法と場所について説明します。 Azure Container Instances、Azure Kubernetes Service、および FPGA にデプロイします。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.reviewer: larryfr
ms.author: ssambare
author: shivanissambare
ms.date: 11/12/2021
ms.topic: how-to
ms.custom: devx-track-python, deploy, devx-track-azurecli, contperf-fy21q2, contperf-fy21q4, mktng-kw-nov2021
adobe-target: true
ms.openlocfilehash: c24aa6fd09539c1bd9536ba0cecb3815edb79e2f
ms.sourcegitcommit: 362359c2a00a6827353395416aae9db492005613
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/15/2021
ms.locfileid: "132488185"
---
# <a name="deploy-machine-learning-models-to-azure"></a>機械学習モデルを Azure にデプロイする 

機械学習またはディープ ラーニング モデルを Web サービスとして Azure クラウドにデプロイする方法について説明します。

[!INCLUDE [endpoints-option](../../includes/machine-learning-endpoints-preview-note.md)]

## <a name="workflow-for-deploying-a-model"></a>モデルをデプロイするためのワークフロー

モデルをどこにデプロイするかに関係なく、ワークフローは同様です。

1. モデルを登録します。
1. エントリ スクリプトを用意します。
1. 推論構成を準備します。
1. モデルをローカルにデプロイして、すべてが正しく動作することを確認します。
1. コンピューティング ターゲットを選択します。
1. モデルをクラウドにデプロイします。
1. 結果の Web サービスをテストします。

機械学習デプロイ ワークフローに関連する概念の詳細については、[Azure Machine Learning でのモデルの管理、デプロイ、監視](concept-model-management-and-deployment.md)に関する記事を参照してください。

## <a name="prerequisites"></a>前提条件

# <a name="azure-cli"></a>[Azure CLI](#tab/azcli)

[!INCLUDE [cli10-only](../../includes/machine-learning-cli-version-1-only.md)]

- Azure Machine Learning ワークスペース。 詳細については、[Azure Machine Learning ワークスペースの作成](how-to-manage-workspace.md)に関するページをご覧ください。
- モデルです。 この記事の例では、事前トレーニング済みのモデルを使用します。
- [コンピューティング インスタンス](how-to-create-manage-compute-instance.md)などの、Docker を実行できるコンピューター。

# <a name="python"></a>[Python](#tab/python)

- Azure Machine Learning ワークスペース。 詳細については、[Azure Machine Learning ワークスペースの作成](how-to-manage-workspace.md)に関するページをご覧ください。
- モデルです。 この記事の例では、事前トレーニング済みのモデルを使用します。
- [Python 用 Azure Machine Learning ソフトウェア開発キット (SDK)](/python/api/overview/azure/ml/intro)。
- [コンピューティング インスタンス](how-to-create-manage-compute-instance.md)などの、Docker を実行できるコンピューター。
---

## <a name="connect-to-your-workspace"></a>ワークスペースに接続する

# <a name="azure-cli"></a>[Azure CLI](#tab/azcli)

アクセスできるワークスペースを表示するには、次のコマンドを使用します。

```azurecli-interactive
az login
az account set -s <subscription>
az ml workspace list --resource-group=<resource-group>
```

# <a name="python"></a>[Python](#tab/python)

```python
from azureml.core import Workspace
ws = Workspace(subscription_id="<subscription_id>",
               resource_group="<resource_group>",
               workspace_name="<workspace_name>")
```

SDK を使用してワークスペースに接続する方法の詳細については、[Azure Machine Learning SDK for Python](/python/api/overview/azure/ml/intro#workspace) のドキュメントをご覧ください。


---

## <a name="register-the-model"></a><a id="registermodel"></a> モデルを登録する

展開する機械学習サービスには通常、次のものが必要です。
    
+ デプロイする特定のモデルを表すリソース (例: Pytorch モデルのファイル)
+ 特定の入力に対してモデルを実行する、サービスで実行されるコード

Azure Machine Learnings では、サービスを 2 つの部分に分けて展開することにより、それまでと同じコードを維持しつつモデルを更新できます。 コードとは "_別に_" モデルをアップロードする仕組みを "モデルの登録" と定義しています。

モデルを登録すると、そのモデルは (ワークスペースの既定のストレージ アカウントの) クラウドにアップロードされ、Web サービスを実行しているコンピューターにマウントされます。

次の例では、モデルを登録する方法を示します。

[!INCLUDE [trusted models](../../includes/machine-learning-service-trusted-model.md)]

# <a name="azure-cli"></a>[Azure CLI](#tab/azcli)

次のコマンドでは、モデルがダウンロードされ、Azure Machine Learning ワークスペースに登録されます。

```azurecli-interactive
wget https://aka.ms/bidaf-9-model -O model.onnx --show-progress
az ml model register -n bidaf_onnx \
    -p ./model.onnx \
    -g <resource-group> \
    -w <workspace-name>
```

`-p` を、登録したいフォルダまたはファイルのパスに設定してください。

`az ml model register` の詳細については、[リファレンス ドキュメント](/cli/azure/ml(v1)/model)を参照してください。

### <a name="register-a-model-from-an-azure-ml-training-run"></a>Azure ML トレーニングの実行からモデルを登録する

Azure Machine Learning トレーニング ジョブによって以前に作成したモデルを登録する必要がある場合は、実験、実行、およびモデルへのパスを指定できます。

```azurecli-interactive
az ml model register -n bidaf_onnx --asset-path outputs/model.onnx --experiment-name myexperiment --run-id myrunid --tag area=qna
```

`--asset-path` パラメーターは、クラウドでのモデルの場所を示します。 この例では、1 つのファイルへのパスが使用されます。 モデルの登録に複数のファイルを含めるには、`--asset-path` を、それらのファイルが含まれているフォルダーのパスに設定します。

`az ml model register` の詳細については、[リファレンス ドキュメント](/cli/azure/ml(v1)/model)を参照してください。

# <a name="python"></a>[Python](#tab/python)

### <a name="register-a-model-from-a-local-file"></a>ローカル ファイルからモデルを登録する

モデルのローカル パスを指定することで、モデルを登録できます。 ローカル コンピューター上のフォルダーまたは単一のファイルのパスを指定できます。
<!-- pyhton nb call -->
[!Notebook-python[] (~/azureml-examples-main/python-sdk/tutorials/deploy-local/1.deploy-local.ipynb?name=register-model-from-local-file-code)]


モデルの登録に複数のファイルを含めるには、`model_path` を、それらのファイルが含まれているフォルダーのパスに設定します。

詳細については、[Model クラス](/python/api/azureml-core/azureml.core.model.model)のドキュメントを参照してください。


### <a name="register-a-model-from-an-azure-ml-training-run"></a>Azure ML トレーニングの実行からモデルを登録する

  SDK を使用してモデルをトレーニングする場合、モデルのトレーニング方法に応じて、[Run](/python/api/azureml-core/azureml.core.run.run) オブジェクトまたは [AutoMLRun](/python/api/azureml-train-automl-client/azureml.train.automl.run.automlrun) オブジェクトのいずれかを受け取ることができます。 各オブジェクトは、実験の実行によって作成されたモデルを登録するために使用できます。

  + `azureml.core.Run` オブジェクトからモデルを登録する
 
    ```python
    model = run.register_model(model_name='bidaf_onnx',
                               tags={'area': 'qna'},
                               model_path='outputs/model.onnx')
    print(model.name, model.id, model.version, sep='\t')
    ```

    `model_path` パラメーターは、クラウドでのモデルの場所を示します。 この例では、1 つのファイルへのパスが使用されます。 モデルの登録に複数のファイルを含めるには、`model_path` を、それらのファイルが含まれているフォルダーのパスに設定します。 詳細については、[Run.register_model](/python/api/azureml-core/azureml.core.run.run#register-model-model-name--model-path-none--tags-none--properties-none--model-framework-none--model-framework-version-none--description-none--datasets-none--sample-input-dataset-none--sample-output-dataset-none--resource-configuration-none----kwargs-) のドキュメントを参照してください。

  + `azureml.train.automl.run.AutoMLRun` オブジェクトからモデルを登録する

    ```python
    description = 'My AutoML Model'
    model = run.register_model(description = description,
                                tags={'area': 'qna'})

    print(run.model_id)
    ```

    この例では、`metric` パラメーターと `iteration` パラメーターが指定されていません。これにより、最適なプライマリ メトリックを持つイテレーションが登録されます。 実行から返された `model_id` 値がモデル名の代わりに使用されます。

    詳細については、[AutoMLRun.register_model](/python/api/azureml-train-automl-client/azureml.train.automl.run.automlrun#register-model-model-name-none--description-none--tags-none--iteration-none--metric-none-) のドキュメントを参照してください。

    登録済みモデルを `AutoMLRun` からデプロイするには、[Azure Machine Learning スタジオのワンクリック デプロイ ボタン](how-to-use-automated-ml-for-ml-models.md#deploy-your-model)でそれを行うことをお勧めします。 

---

## <a name="define-a-dummy-entry-script"></a>ダミー エントリ スクリプトを用意する

エントリ スクリプトは、デプロイされた Web サービスに送信されたデータを受け取り、それをモデルに渡します。 次に、クライアントに対するモデルの応答が返されます。 *このスクリプトはこのモデルに固有のものです*。 エントリ スクリプトでは、モデルが受け入れ、返すデータを認識する必要があります。

エントリ スクリプトでは、次の 2 つの処理を実行する必要があります。

1. モデルの読み込み (`init()` という関数を使用)
1. 入力データでのモデルの実行 (`run()` という関数を使用)

最初のデプロイでは、受け取ったデータを出力するダミーのエントリ スクリプトを使用します。

:::code language="python" source="~/azureml-examples-main/python-sdk/tutorials/deploy-local/source_dir/echo_score.py":::

このファイルを `source_dir` と いう名前のディレクトリ内に`echo_score.py` として保存します。 このダミー スクリプトによって、送信したデータが返されるため、モデルは使用されません。 ただし、スコアリング スクリプトが実行されていることをテストするのに役立ちます。

## <a name="define-an-inference-configuration"></a>推論構成を定義する

推論構成には、Web サービスの初期化時に使用する Docker コンテナーとファイルを記述します。 Web サービスを展開するときは、ソース ディレクトリにあるすべてのファイル (サブディレクトリを含む) を圧縮してクラウドにアップロードします。

下の推論構成では、展開する機械学習サービスで、`./source_dir` ディレクトリの `echo_score.py` ファイルによって受信要求を処理することと、`project_environment` 環境で指定した Python パッケージを Docker イメージで使用することを指定しています。

プロジェクトの環境を作成するときは、任意の [Azure Machine Learning 推論でキュレーションされた環境](concept-prebuilt-docker-images-inference.md#list-of-prebuilt-docker-images-for-inference)を、ベースの Docker イメージとして使用できます。 必要な依存関係がそこにインストールされ、作成された Docker イメージが、皆さまのワークスペースに紐付いたレポジトリに保存されます。

> [!NOTE]
> Azure Machine Learning の [推論ソース ディレクトリ](/python/api/azureml-core/azureml.core.model.inferenceconfig?view=azure-ml-py#constructor&preserve-view=true)のアップロードでは、 **.gitignore** または **.amlignore** が考慮されません

# <a name="azure-cli"></a>[Azure CLI](#tab/azcli)

最小限の推論構成は、次のように記述することができます。

:::code language="json" source="~/azureml-examples-main/python-sdk/tutorials/deploy-local/dummyinferenceconfig.json":::

`dummyinferenceconfig.json` という名前でこのファイルを保存します。


推論構成に関する詳細な説明については、[こちらの記事を参照](./reference-azure-machine-learning-cli.md#inference-configuration-schema)してください。 

# <a name="python"></a>[Python](#tab/python)

次の例で、上で用意したダミーのスコアリング スクリプトを使用して、pip 関連の依存関係がない最小の環境を作成する方法を示します。

[!Notebook-python[] (~/azureml-examples-main/python-sdk/tutorials/deploy-local/1.deploy-local.ipynb?name=inference-configuration-code)]

環境の詳細については、[トレーニングとデプロイのための環境の作成と管理](how-to-use-environments.md)に関する記事を参照してください。

推論構成の詳細については、[InferenceConfig](/python/api/azureml-core/azureml.core.model.inferenceconfig) クラスのドキュメントを参照してください。

---


## <a name="define-a-deployment-configuration"></a>デプロイ構成を定義する

デプロイ構成では、Web サービスが実行するために必要とするメモリとコアの量を指定します。 また、基になる Web サービスの構成の詳細も提供します。 展開構成ではたとえば、メモリ 2 GB、CPU コア 2 個、GPU コア 1 個をサービスで使用し、自動スケーリングを有効にすることを指定できます。

デプロイ構成で使用できるオプションは、選択するコンピューティング ターゲットによって異なります。 ローカルに展開する場合は、Web サービスで使用するポートだけを指定できます。

# <a name="azure-cli"></a>[Azure CLI](#tab/azcli)

[!INCLUDE [aml-local-deploy-config](../../includes/machine-learning-service-local-deploy-config.md)]

詳しくは、[デプロイ スキーマ](./reference-azure-machine-learning-cli.md#deployment-configuration-schema)に関するページを参照してください。

# <a name="python"></a>[Python](#tab/python)

次の Python は、ローカル デプロイ構成を作成する方法を示しています。 

[!Notebook-python[] (~/azureml-examples-main/python-sdk/tutorials/deploy-local/1.deploy-local.ipynb?name=deployment-configuration-code)]

---

## <a name="deploy-your-machine-learning-model"></a>機械学習モデルをデプロイする

これでモデルをデプロイする準備が整いました。 

# <a name="azure-cli"></a>[Azure CLI](#tab/azcli)

`bidaf_onnx:1` を、モデルの名前とそのバージョン番号に置き換えます。

```azurecli-interactive
az ml model deploy -n myservice \
    -m bidaf_onnx:1 \
    --overwrite \
    --ic dummyinferenceconfig.json \
    --dc deploymentconfig.json \
    -g <resource-group> \
    -w <workspace-name>
```

# <a name="python"></a>[Python](#tab/python)


[!Notebook-python[] (~/azureml-examples-main/python-sdk/tutorials/deploy-local/1.deploy-local.ipynb?name=deploy-model-code)]

[!Notebook-python[] (~/azureml-examples-main/python-sdk/tutorials/deploy-local/1.deploy-local.ipynb?name=deploy-model-print-logs)]

詳細についてはね[Model.deploy()](/python/api/azureml-core/azureml.core.model.model#deploy-workspace--name--models--inference-config-none--deployment-config-none--deployment-target-none--overwrite-false-) および [Webservice](/python/api/azureml-core/azureml.core.webservice.webservice) のドキュメントを参照してください。

---

## <a name="call-into-your-model"></a>モデルに対する呼び出しを行う

エコー モデルを正しく展開できたことを確認しましょう。 単純な liveness の要求やスコアリング要求を実行できるはずです。

# <a name="azure-cli"></a>[Azure CLI](#tab/azcli)

```azurecli-interactive
curl -v http://localhost:32267
curl -v -X POST -H "content-type:application/json" \
    -d '{"query": "What color is the fox", "context": "The quick brown fox jumped over the lazy dog."}' \
    http://localhost:32267/score
```

# <a name="python"></a>[Python](#tab/python)
<!-- python nb call -->
[!Notebook-python[] (~/azureml-examples-main/python-sdk/tutorials/deploy-local/1.deploy-local.ipynb?name=call-into-model-code)]

---

## <a name="define-an-entry-script"></a>エントリ スクリプトを定義する

ここでは、モデルをロードします。 最初にエントリ スクリプトを修正します。


:::code language="python" source="~/azureml-examples-main/python-sdk/tutorials/deploy-local/source_dir/score.py":::

このファイルを `score.py` という名前で `source_dir` の中に保存します。

`AZUREML_MODEL_DIR` 環境変数で、登録したモデルの場所を確認できます。 これで、いくつかの pip パッケージを追加できました。

# <a name="azure-cli"></a>[Azure CLI](#tab/azcli)

:::code language="json" source="~/azureml-examples-main/python-sdk/tutorials/deploy-local/inferenceconfig.json":::

このファイルを `inferenceconfig.json` として保存します 

# <a name="python"></a>[Python](#tab/python)

```python
env = Environment(name='myenv')
python_packages = ['nltk', 'numpy', 'onnxruntime']
for package in python_packages:
    env.python.conda_dependencies.add_pip_package(package)

inference_config = InferenceConfig(environment=env, source_directory='./source_dir', entry_script='./score.py')
```

詳細については、[LocalWebservice](/python/api/azureml-core/azureml.core.webservice.local.localwebservice)、[Model.deploy()](/python/api/azureml-core/azureml.core.model.model#deploy-workspace--name--models--inference-config-none--deployment-config-none--deployment-target-none--overwrite-false-)、および [Webservice](/python/api/azureml-core/azureml.core.webservice.webservice) のドキュメントを参照してください。

---

## <a name="deploy-again-and-call-your-service"></a>もう一度サービスを展開して呼び出す

もう一度サービスを展開します。

# <a name="azure-cli"></a>[Azure CLI](#tab/azcli)

`bidaf_onnx:1` を、モデルの名前とそのバージョン番号に置き換えます。

```azurecli-interactive
az ml model deploy -n myservice \
    -m bidaf_onnx:1 \
    --overwrite \
    --ic inferenceconfig.json \
    --dc deploymentconfig.json \
    -g <resource-group> \
    -w <workspace-name>
```

# <a name="python"></a>[Python](#tab/python)

[!Notebook-python[] (~/azureml-examples-main/python-sdk/tutorials/deploy-local/1.deploy-local.ipynb?name=re-deploy-model-code)]

[!Notebook-python[] (~/azureml-examples-main/python-sdk/tutorials/deploy-local/1.deploy-local.ipynb?name=re-deploy-model-print-logs)]

詳細についてはね[Model.deploy()](/python/api/azureml-core/azureml.core.model.model#deploy-workspace--name--models--inference-config-none--deployment-config-none--deployment-target-none--overwrite-false-) および [Webservice](/python/api/azureml-core/azureml.core.webservice.webservice) のドキュメントを参照してください。

---
次に、POST 要求をサービスに送信できることを確認します。

# <a name="azure-cli"></a>[Azure CLI](#tab/azcli)

```azurecli-interactive
curl -v -X POST -H "content-type:application/json" \
    -d '{"query": "What color is the fox", "context": "The quick brown fox jumped over the lazy dog."}' \
    http://localhost:32267/score
```

# <a name="python"></a>[Python](#tab/python)

[!Notebook-python[] (~/azureml-examples-main/python-sdk/tutorials/deploy-local/1.deploy-local.ipynb?name=send-post-request-code)]

---

## <a name="choose-a-compute-target"></a>コンピューティング ターゲットを選択する

[!INCLUDE [aml-deploy-target](../../includes/aml-compute-target-deploy.md)]

## <a name="deploy-to-cloud"></a>クラウドにデプロイする

サービスがローカルで動作することを確認してリモートのコンピューティング先を選んだら、クラウドに展開する準備は完了です。 

選択したコンピューティング先 (この場合は Azure Container Instances) に合わせて展開構成を変更します。

# <a name="azure-cli"></a>[Azure CLI](#tab/azcli)

デプロイ構成で使用できるオプションは、選択するコンピューティング ターゲットによって異なります。

:::code language="json" source="~/azureml-examples-main/python-sdk/tutorials/deploy-local/re-deploymentconfig.json":::

このファイルを `re-deploymentconfig.json` として保存します。

詳細については、[こちらのリファレンス](./reference-azure-machine-learning-cli.md#deployment-configuration-schema)を参照してください。

# <a name="python"></a>[Python](#tab/python)

[!Notebook-python[] (~/azureml-examples-main/python-sdk/tutorials/deploy-local/1.deploy-local.ipynb?name=deploy-model-on-cloud-code)]

---

もう一度サービスを展開します。


# <a name="azure-cli"></a>[Azure CLI](#tab/azcli)

`bidaf_onnx:1` を、モデルの名前とそのバージョン番号に置き換えます。

```azurecli-interactive
az ml model deploy -n myservice \
    -m bidaf_onnx:1 \
    --overwrite \
    --ic inferenceconfig.json \
    --dc re-deploymentconfig.json \
    -g <resource-group> \
    -w <workspace-name>
```

サービス ログを表示するには、次のコマンドを使用します。

```azurecli-interactive
az ml service get-logs -n myservice \
    -g <resource-group> \
    -w <workspace-name>
```

# <a name="python"></a>[Python](#tab/python)


[!Notebook-python[] (~/azureml-examples-main/python-sdk/tutorials/deploy-local/1.deploy-local.ipynb?name=re-deploy-service-code)]

[!Notebook-python[] (~/azureml-examples-main/python-sdk/tutorials/deploy-local/1.deploy-local.ipynb?name=re-deploy-service-print-logs)]

詳細についてはね[Model.deploy()](/python/api/azureml-core/azureml.core.model.model#deploy-workspace--name--models--inference-config-none--deployment-config-none--deployment-target-none--overwrite-false-) および [Webservice](/python/api/azureml-core/azureml.core.webservice.webservice) のドキュメントを参照してください。

---


## <a name="call-your-remote-webservice"></a>リモートの Web サービスを呼び出す

リモートに展開する場合、キー認証が有効になっている場合があります。 下の例では、推論要求を行うために Python でサービス キーを取得する方法を示します。

[!Notebook-python[] (~/azureml-examples-main/python-sdk/tutorials/deploy-local/1.deploy-local.ipynb?name=call-remote-web-service-code)]

[!Notebook-python[] (~/azureml-examples-main/python-sdk/tutorials/deploy-local/1.deploy-local.ipynb?name=call-remote-webservice-print-logs)]



他の言語のクライアントの例は、[Web サービスのクライアント アプリケーション](how-to-consume-web-service.md)に関する記事をご覧ください。

### <a name="understanding-service-state"></a>サービスの状態について

モデルのデプロイ中に、完全にデプロイされるまでの間に、サービスの状態が変化することがあります。

次の表で、サービスの各状態について説明します。

| Web サービスの状態 | 説明 | 最終的な状態
| ----- | ----- | ----- |
| 移行中 | サービスはデプロイ処理中です。 | いいえ |
| 異常 | サービスはデプロイされましたが、現在アクセスできません。  | いいえ |
| スケジュール不可 | リソースが不足しているため、現時点ではサービスをデプロイできません。 | いいえ |
| 失敗 | エラーまたはクラッシュが発生したため、サービスをデプロイできませんでした。 | はい |
| Healthy | サービスは正常であり、エンドポイントを使用できます。 | はい |

> [!TIP]
> デプロイ時に、コンピューティング ターゲットの Docker イメージが構築され、Azure Container Registry (ACR) から読み込まれます。 既定では、Azure Machine Learning により、*basic* サービス レベルを使用する ACR が作成されます。 ワークスペースの ACR を standard レベルまたは premium レベルに変更すると、イメージを構築してコンピューティング ターゲットにデプロイするための時間を短縮することができます。 詳細については、「[Azure Container Registry サービス階層](../container-registry/container-registry-skus.md)」を参照してください。

> [!NOTE]
> Azure Kubernetes Service (AKS) にモデルをデプロイする場合は、そのクラスターで [Azure Monitor](../azure-monitor/containers/container-insights-enable-existing-clusters.md) を有効にすることをお勧めします。 これは、全体的なクラスターの正常性とリソースの使用状況を把握するのに役立ちます。 また、次のリソースも役立つ場合があります。
>
> * [AKS クラスターに影響する Resource Health イベントを確認する](../aks/aks-resource-health.md)
> * [Azure Kubernetes Service 診断](../aks/concepts-diagnostics.md)
>
> 異常または過負荷のクラスターにモデルをデプロイしようとすると、問題が発生することが予想されます。 AKS クラスターでの問題のトラブルシューティングに関するヘルプが必要な場合は、AKS サポートにお問い合わせください。

## <a name="delete-resources"></a>リソースを削除する

# <a name="azure-cli"></a>[Azure CLI](#tab/azcli)


[!Notebook-python[] (~/azureml-examples-main/python-sdk/tutorials/deploy-local/2.deploy-local-cli.ipynb?name=delete-resource-code)]

```azurecli-interactive
az ml service delete -n myservice
az ml service delete -n myaciservice
az ml model delete --model-id=<MODEL_ID>
```

デプロイされた Web サービスを削除するには、`az ml service delete <name of webservice>` を使用します。

ワークスペースから登録済みのモデルを削除するには、`az ml model delete <model id>` を使用します。

[Web サービスの削除](/cli/azure/ml(v1)/computetarget/create#az_ml_service_delete)と[モデルの削除](/cli/azure/ml/model#az_ml_model_delete)に関する詳細を確認してください。

# <a name="python"></a>[Python](#tab/python)

[!Notebook-python[] (~/azureml-examples-main/python-sdk/tutorials/deploy-local/1.deploy-local.ipynb?name=delete-resource-code)]

デプロイされた Web サービスを削除するには、`service.delete()` を使用します。
登録済みのモデルを削除するには、`model.delete()` を使用します。

詳細については、[WebService.delete()](/python/api/azureml-core/azureml.core.webservice%28class%29#delete--) と [Model.delete()](/python/api/azureml-core/azureml.core.model.model#delete--) のドキュメントを参照してください。

---

## <a name="next-steps"></a>次のステップ

* [失敗したデプロイのトラブルシューティング](how-to-troubleshoot-deployment.md)
* [Web サービスを更新する](how-to-deploy-update-web-service.md)
* [Azure Machine Learning スタジオにおける自動化された ML 実行のためのワンクリック デプロイ](how-to-use-automated-ml-for-ml-models.md#deploy-your-model)
* [TLS を使用して Azure Machine Learning による Web サービスをセキュリティで保護する](how-to-secure-web-service.md)
* [Application Insights を使用して Azure Machine Learning のモデルを監視する](how-to-enable-app-insights.md)
* [モデル デプロイのイベント アラートおよびトリガーを作成する](how-to-use-event-grid.md)
