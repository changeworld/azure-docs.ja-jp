---
title: Triton を使用した高パフォーマンス モデルのサービス (プレビュー)
titleSuffix: Azure Machine Learning
description: Azure Machine Learning で Triton 推論サーバーを使用してモデルを展開する方法について説明します。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: gopalv
author: gvashishtha
ms.date: 09/23/2020
ms.topic: conceptual
ms.reviewer: larryfr
ms.custom: deploy
ms.openlocfilehash: 9a6e2de07921d05e123154f604c3d1b369b3b89d
ms.sourcegitcommit: 090ea6e8811663941827d1104b4593e29774fa19
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/13/2020
ms.locfileid: "91998748"
---
# <a name="high-performance-serving-with-triton-inference-server-preview"></a>Triton 推論サーバーを使用した高パフォーマンスのサービス (プレビュー) 

[NVIDIA Triton 推論サーバー](https://developer.nvidia.com/nvidia-triton-inference-server)を使用して、モデルの推論に使用する Web サービスのパフォーマンスを向上させる方法について説明します。

推論用のモデルを展開する方法の 1 つは、Web サービスとして展開することです。 たとえば、Azure Kubernetes Service または Azure Container Instances への展開です。 既定では、Azure Machine Learning はシングルスレッドの "*汎用*" Web フレームワークを使用して Web サービスを展開します。

Triton は、"*推論用に最適化された*" フレームワークです。 GPU の使用率が高く、コスト効率に優れた推論を実現します。 サーバー側では、受信した要求をバッチ処理し、推論のためにこれらのバッチを送信します。 バッチ処理は GPU リソースのより適切な利用を可能にする、Triton のパフォーマンスにおける重要な部分です。

> [!IMPORTANT]
> Azure Machine Learning からの展開に対する Triton の使用は、現在__プレビュー__段階です。 プレビューの機能は、カスタマー サポートの対象になっていない場合があります。 詳細については、「[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)」を参照してください。

> [!TIP]
> このドキュメントに記載されているコード スニペットは説明を目的としたものであり、完全なソリューションとなっていない場合があります。 動作するコード例については、[Azure Machine Learning での Triton のエンドツーエンドのサンプル](https://aka.ms/aml-triton-sample)を参照してください。

## <a name="prerequisites"></a>前提条件

* **Azure サブスクリプション**。 お持ちでない場合は、[無料版または有料版の Azure Machine Learning](https://aka.ms/AMLFree) をお試しください。
* Azure Machine Learning で[モデルを展開する方法と場所](how-to-deploy-and-where.md)について理解していること。
* [Azure Machine Learning SDK for Python](https://docs.microsoft.com/python/api/overview/azure/ml/?view=azure-ml-py) **または** [Azure CLI](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest) と [機械学習拡張機能](reference-azure-machine-learning-cli.md)。
* ローカル テスト用の Docker の動作するインストール。 Docker のインストールと検証の詳細については、[オリエンテーションとセットアップ](https://docs.docker.com/get-started/)に関する Docker ドキュメントを参照してください。

## <a name="architectural-overview"></a>アーキテクチャの概要

独自のモデルに Triton を使用する前に、Triton がどのように Azure Machine Learning と連携して動作し、既定の展開と比較してどう違うかを理解することが重要です。

**Triton を使用しない既定の展開**

* 受信した要求を同時に処理するために、複数の [Gunicorn](https://gunicorn.org/) ワーカーが開始されています。
* これらのワーカーでは、前処理、モデルの呼び出し、および後処理が行われます。 
* 推論要求には、__スコアリング URI__ が使用されます。 たとえば、「 `https://myserevice.azureml.net/score` 」のように入力します。

:::image type="content" source="./media/how-to-deploy-with-triton/normal-deploy.png" alt-text="標準の、Triton を使用しない展開アーキテクチャの図":::

**Triton を使用した推論構成の展開**

* 受信した要求を同時に処理するために、複数の [Gunicorn](https://gunicorn.org/) ワーカーが開始されています。
* 要求は **Triton サーバー**に転送されます。 
* Triton によって、GPU 使用率を最大化するために要求がバッチ処理されます。
* クライアントは、__スコアリング URI__ を使用して要求を行います。 たとえば、「 `https://myserevice.azureml.net/score` 」のように入力します。

:::image type="content" source="./media/how-to-deploy-with-triton/inferenceconfig-deploy.png" alt-text="標準の、Triton を使用しない展開アーキテクチャの図":::

モデルの展開に Triton を使用するワークフローは次のとおりです。

1. Triton がモデルに使用可能であることを確認します。
1. Triton によって展開されたモデルに、要求を送信できることを確認します。
1. Triton 固有のコードを AML の展開に組み込みます。

## <a name="optional-define-a-model-config-file"></a>(省略可能) モデル構成ファイルの定義

モデル構成ファイルでは、想定される入力の数と、それらの入力の次元を Triton に対して指定します。 構成ファイルの作成の詳細については、[モデルの構成](https://docs.nvidia.com/deeplearning/triton-inference-server/user-guide/docs/model_configuration.html)に関する NVIDIA ドキュメントを参照してください。

> [!TIP]
> Triton 推論サーバーの起動時に `--strict-model-config=false` オプションを使用します。これは、ONNX または TensorFlow モデルに `config.pbtxt` ファイルを指定する必要がないことを意味します。
> 
> このオプションの詳細については、[生成されたモデルの構成](https://docs.nvidia.com/deeplearning/triton-inference-server/user-guide/docs/model_configuration.html#generated-model-configuration)に関する NVIDIA ドキュメントを参照してください。

## <a name="directory-structure"></a>ディレクトリの構造

Azure Machine Learning にモデルを登録する場合、個々のファイルまたはディレクトリ構造を登録できます。 Triton を使用するには、`triton` という名前のディレクトリを含むディレクトリ構造に対してモデルの登録を行う必要があります。 このディレクトリの一般的な構造は次のとおりです。

```bash
models
    - triton
        - model_1
            - model_version
                - model_file
                - config_file
        - model_2
            ...
```

> [!IMPORTANT]
> このディレクトリ構造は Triton モデル リポジトリであり、モデルを Triton で動作させるために必要です。 詳細については、[Triton モデル リポジトリ](https://docs.nvidia.com/deeplearning/triton-inference-server/user-guide/docs/model_repository.html)に関する NVIDIA ドキュメントを参照してください。

## <a name="test-with-triton-and-docker"></a>Triton と Docker を使用したテスト

Triton で実行できることを確認するためにモデルをテストするには、Docker を使用できます。 次のコマンドは、Triton コンテナーをローカル コンピューターにプルし、Triton サーバーを起動します。

1. Triton サーバーのイメージをローカル コンピューターにプルするには、次のコマンドを使用します。

    ```bash
    docker pull nvcr.io/nvidia/tritonserver:20.09-py3
    ```

1. Triton サーバーを開始するには、次のコマンドを使用します。 `<path-to-models/triton>` を、対象のモデルを含む Triton モデル リポジトリへのパスに置き換えます。

    ```bash
    docker run --rm -ti -v<path-to-models/triton>:/models nvcr.io/nvidia/tritonserver:20.09-py3 tritonserver --model-repository=/models --strict-model-config=false
    ```

    > [!IMPORTANT]
    > Windows を使用している場合は、コマンドを初めて実行した際に、このプロセスのネットワーク接続を許可するように求められる場合があります。 その場合は、アクセスを有効にするよう選択します。

    開始すると、次のテキストのような情報がコマンド ラインに記録されます。

    ```bash
    I0923 19:21:30.582866 1 http_server.cc:2705] Started HTTPService at 0.0.0.0:8000
    I0923 19:21:30.626081 1 http_server.cc:2724] Started Metrics Service at 0.0.0.0:8002
    ```

    最初の行は、Web サービスのアドレスを示します。 この場合は `0.0.0.0:8000` です。これは `localhost:8000` と同じです。

1. curl などのユーティリティを使用して、正常性エンドポイントにアクセスします。

    ```bash
    curl -L -v -i localhost:8000/v2/health/ready
    ```

    このコマンドでは、次のような情報が返されます。 `200 OK` に注目してください。この状態は、Web サーバーが実行中であることを示します。

    ```bash
    *   Trying 127.0.0.1:8000...
    * Connected to localhost (127.0.0.1) port 8000 (#0)
    > GET /v2/health/ready HTTP/1.1
    > Host: localhost:8000
    > User-Agent: curl/7.71.1
    > Accept: */*
    >
    * Mark bundle as not supporting multiuse
    < HTTP/1.1 200 OK
    HTTP/1.1 200 OK
    ```

基本的な正常性チェック以外にも、推論のために Triton にデータを送信するクライアントを作成できます。 クライアントの作成の詳細については、[クライアントの例](https://docs.nvidia.com/deeplearning/triton-inference-server/user-guide/docs/client_example.html)に関する NVIDIA ドキュメントを参照してください。 [Triton GitHub に Python のサンプル](https://github.com/triton-inference-server/server/tree/master/src/clients/python/examples)もあります。

Docker を使用した Triton の実行の詳細については、[GPU を使用したシステムでの Triton の実行](https://docs.nvidia.com/deeplearning/triton-inference-server/user-guide/docs/run.html#running-triton-on-a-system-with-a-gpu)および [GPU のないシステムでの Triton の実行](https://docs.nvidia.com/deeplearning/triton-inference-server/user-guide/docs/run.html#running-triton-on-a-system-without-a-gpu)に関するページを参照してください。

## <a name="register-your-model"></a>モデルを登録する

モデルが Triton で動作することを確認したので、Azure Machine Learning に登録します。 モデルの登録により、モデル ファイルが Azure Machine Learning ワークスペースに格納され、Python SDK および Azure CLI での展開時に使用されます。

次の例では、1 つまたは複数のモデルを登録する方法を示します。

# <a name="python"></a>[Python](#tab/python)

```python
from azureml.core.model import Model

model = Model.register(
    model_path=os.path.join("..", "triton"),
    model_name="bidaf_onnx",
    tags={'area': "Natural language processing", 'type': "Question answering"},
    description="Question answering model from ONNX model zoo",
    workspace=ws
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli
az ml model register --model-path='triton' \
--name='bidaf_onnx' \
--workspace-name='<my_workspace>'
```
---

<a id="processing"></a>

## <a name="add-pre-and-post-processing"></a>前処理と後処理を追加する

Web サービスが動作していることを確認した後、"_エントリ スクリプト_" を定義することで、前処理と後処理のコードを追加できます。 このファイルの名前は `score.py` です。 エントリ スクリプトの詳細については、「[エントリ スクリプトを定義する](how-to-deploy-and-where.md#define-an-entry-script)」を参照してください。

主なステップの 2 つは、`init()` メソッドで Triton HTTP クライアントを初期化し、`run()` 関数でそのクライアントを呼び出すことです。

### <a name="initialize-the-triton-client"></a>Triton クライアントを初期化する

次の例のようなコードを `score.py` ファイルに含めます。 Azure Machine Learning の Triton は、localhost のポート 8000 でアドレス指定されることを想定しています。 この場合の localhost は、この展開の Docker イメージ内にあり、ローカル マシン上のポートではありません。

> [!TIP]
> `tritonhttpclient` pip パッケージは、キュレーションされた `AzureML-Triton` 環境に含まれているので、pip 依存関係として指定する必要はありません。

```python
import tritonhttpclient

def init():
    global triton_client
    triton_client = tritonhttpclient.InferenceServerClient(url="localhost:8000")
```

### <a name="modify-your-scoring-script-to-call-into-triton"></a>Triton を呼び出すようにスコアリング スクリプトを変更する

次の例は、モデルのメタデータを動的に要求する方法を示しています。

> [!TIP]
> Triton クライアントの `.get_model_metadata` メソッドを使用して、Triton で読み込まれたモデルのメタデータを動的に要求できます。 使用例については、[サンプル ノートブック](https://aka.ms/triton-aml-sample)を参照してください。

```python
input = tritonhttpclient.InferInput(input_name, data.shape, datatype)
input.set_data_from_numpy(data, binary_data=binary_data)

output = tritonhttpclient.InferRequestedOutput(
         output_name, binary_data=binary_data, class_count=class_count)

# Run inference
res = triton_client.infer(model_name,
                          [input]
                          request_id='0',
                          outputs=[output])

```

<a id="redeploy"></a>

## <a name="redeploy-with-an-inference-configuration"></a>推論構成を使用した再展開

推論の構成では、エントリ スクリプトと、Python SDK または Azure CLI を使用した Azure Machine Learning 展開プロセスを使用できます。

> [!IMPORTANT]
> `AzureML-Triton` [がキュレーションされた環境](./resource-curated-environments.md)を指定する必要があります。
>
> Python コード例では、`AzureML-Triton` を `My-Triton` という名前の別の環境に複製します。 Azure CLI コードでもこの環境が使用されます。 環境の複製の詳細については、[Environment.Clone()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py&preserve-view=true#clone-new-name-) のリファレンスを参照してください。

# <a name="python"></a>[Python](#tab/python)

```python
from azureml.core.webservice import LocalWebservice
from azureml.core import Environment
from azureml.core.model import InferenceConfig


local_service_name = "triton-bidaf-onnx"
env = Environment.get(ws, "AzureML-Triton").clone("My-Triton")

for pip_package in ["nltk"]:
    env.python.conda_dependencies.add_pip_package(pip_package)

inference_config = InferenceConfig(
    entry_script="score_bidaf.py",  # This entry script is where we dispatch a call to the Triton server
    source_directory=os.path.join("..", "scripts"),
    environment=env
)

local_config = LocalWebservice.deploy_configuration(
    port=6789
)

local_service = Model.deploy(
    workspace=ws,
    name=local_service_name,
    models=[model],
    inference_config=inference_config,
    deployment_config=local_config,
    overwrite=True)

local_service.wait_for_deployment(show_output = True)
print(local_service.state)
# Print the URI you can use to call the local deployment
print(local_service.scoring_uri)
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

> [!TIP]
> 推論構成の作成の詳細については、「[推論構成スキーマ](./reference-azure-machine-learning-cli.md#inference-configuration-schema)」を参照してください。

```azurecli
az ml model deploy -n triton-densenet-onnx \
-m densenet_onnx:1 \
--ic inference-config.json \
-e My-Triton --dc deploymentconfig.json \
--overwrite --compute-target=aks-gpu
```

---

展開が完了すると、スコアリング URI が表示されます。 今回のローカル展開では、`http://localhost:6789/score` です。 クラウドにデプロイする場合は、[az ml service show](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/service?view=azure-cli-latest#ext_azure_cli_ml_az_ml_service_show) CLI コマンドを使用してスコアリング URI を取得できます。

スコアリング URI に推論要求を送信するクライアントを作成する方法については、[Web サービスとして展開されたモデルの使用](how-to-consume-web-service.md)に関するページを参照してください。

## <a name="clean-up-resources"></a>リソースをクリーンアップする

Azure Machine Learning ワークスペースの使用を継続する予定で、デプロイされたサービスを削除する場合は、次のオプションのいずれかを使用します。

# <a name="python"></a>[Python](#tab/python)

```python
local_service.delete()
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli
az ml service delete -n triton-densenet-onnx
```

---

## <a name="next-steps"></a>次のステップ

* [Azure Machine Learning での Triton のエンドツーエンドのサンプルを参照する](https://aka.ms/aml-triton-sample)
* [Triton クライアントの例](https://github.com/triton-inference-server/server/tree/master/src/clients/python/examples)を確認する
* [Triton 推論サーバーのドキュメント](https://docs.nvidia.com/deeplearning/triton-inference-server/user-guide/docs/index.html)を読む
* [失敗したデプロイのトラブルシューティング](how-to-troubleshoot-deployment.md)
* [Azure Kubernetes Service にデプロイする](how-to-deploy-azure-kubernetes-service.md)
* [Web サービスを更新する](how-to-deploy-update-web-service.md)
* [実稼働環境でモデルのデータを収集する](how-to-enable-data-collection.md)
