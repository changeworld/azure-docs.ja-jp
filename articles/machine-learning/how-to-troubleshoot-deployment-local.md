---
title: ローカルでのモデル デプロイのトラブルシューティング
titleSuffix: Azure Machine Learning
description: モデル デプロイ エラーのトラブルシューティングの最初の手順として、ローカルでのモデル デプロイを試してください。
services: machine-learning
ms.service: machine-learning
ms.subservice: mlops
ms.reviewer: luquinta
ms.date: 10/21/2021
ms.topic: troubleshooting
ms.custom: devx-track-python, deploy, contperf-fy21q2
ms.openlocfilehash: 6db00dfa109bad8acf0f9a56b9dd2559e5999d3e
ms.sourcegitcommit: e41827d894a4aa12cbff62c51393dfc236297e10
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/04/2021
ms.locfileid: "131560722"
---
# <a name="troubleshooting-with-a-local-model-deployment"></a>ローカルでのモデル デプロイを使用したトラブルシューティング

Azure Container Instances (ACI) または Azure Kubernetes Service (AKS) へのデプロイのトラブルシューティングの最初の手順として、ローカルでのモデル デプロイを試してください。  ローカルの Web サービスを使用すると、Azure Machine Learning の一般的な Docker Web サービスのデプロイ エラーを見つけて修正することがより簡単にできます。

## <a name="prerequisites"></a>前提条件

* **Azure サブスクリプション**。 [無料版または有料版の Azure Machine Learning](https://azure.microsoft.com/free/) をお試しください。
* オプション A (**推奨**) - Azure Machine Learning コンピューティング インスタンスでローカルにデバッグする
   * [コンピューティング インスタンス](how-to-deploy-local-container-notebook-vm.md)が実行されている Azure Machine Learning ワークスペース
* オプション B - お使いのコンピューティングでローカルにデバッグする
   * [Azure Machine Learning SDK](/python/api/overview/azure/ml/install)。
   * [Azure CLI](/cli/azure/install-azure-cli)。
   * [Azure Machine Learning 用 CLI 拡張機能](reference-azure-machine-learning-cli.md)。
   * ローカル システムで動作する Docker インストールを使用します。 
   * Docker のインストールを確認するには、ターミナルまたはコマンド プロンプトからコマンド `docker run hello-world` を使用します。 Docker のインストール、または Docker のエラーのトラブルシューティングについては、[Docker のドキュメント](https://docs.docker.com/)を参照してください。
* オプション C - Azure Machine Learning 推論 HTTP サーバーを使用したローカル デバッグを有効にします。
    * Azure Machine Learning 推論 HTTP サーバー [(プレビュー)](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) は、ローカルの開発環境で入力スクリプト (`score.py`) を簡単に検証できる Python のパッケージです。 スコアリング スクリプトに問題がある場合、サーバーからエラーが返されます。 また、エラーが発生した場所も返されます。
    * サーバーは、継続的インテグレーションとデプロイ パイプラインで検証ゲートを作成するときにも使用できます。 たとえば、候補のスクリプトでサーバーを起動し、ローカル エンドポイントに対してテスト スイートを実行します。

## <a name="azure-machine-learning-inference-http-server"></a>Azure Machine Learning 推論 HTTP サーバー

ローカル推論サーバーを使用すると、エントリ スクリプト (`score.py`) をすばやくデバッグできます。 基になるスコア スクリプトにバグがある場合、サーバーはモデルの初期化やサービスの提供に失敗します。 代わりに、例外と、問題が発生した場所がスローされます。 [Azure Machine Learning 推論 HTTP サーバーの詳細](how-to-inference-server-http.md)

1. [Pypi](https://pypi.org/) フィードから `azureml-inference-server-http` パッケージをインストールします。

    ```bash
    python -m pip install azureml-inference-server-http
    ```

2. サーバーを起動し、エントリ スクリプトとして `score.py` を設定します。

    ```bash
    azmlinfsrv --entry_script score.py
    ```

3. `curl` を使用して、スコアリング要求をサーバーに送信します。

    ```bash
    curl -p 127.0.0.1:5001/score
    ```

## <a name="debug-locally"></a>ローカル デバッグ

[MachineLearningNotebooks](https://github.com/Azure/MachineLearningNotebooks) リポジトリにあるサンプル[ローカル展開ノートブック](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/deployment/deploy-to-local/register-model-deploy-local.ipynb)から、実行可能な例を探索します。

> [!WARNING]
> ローカル Web サービスのデプロイは、運用シナリオではサポートされていません。

ローカルにデプロイするには、`LocalWebservice.deploy_configuration()` を使用してデプロイ構成を作成するようにコードを変更します。 次に、`Model.deploy()` を使用して、サービスをデプロイします。 次の例では、モデル (モデル変数に含まれる) をローカル Web サービスとしてデプロイします。

```python
from azureml.core.environment import Environment
from azureml.core.model import InferenceConfig, Model
from azureml.core.webservice import LocalWebservice


# Create inference configuration based on the environment definition and the entry script
myenv = Environment.from_conda_specification(name="env", file_path="myenv.yml")
inference_config = InferenceConfig(entry_script="score.py", environment=myenv)
# Create a local deployment, using port 8890 for the web service endpoint
deployment_config = LocalWebservice.deploy_configuration(port=8890)
# Deploy the service
service = Model.deploy(
    ws, "mymodel", [model], inference_config, deployment_config)
# Wait for the deployment to complete
service.wait_for_deployment(True)
# Display the port that the web service is available on
print(service.port)
```

独自の conda 仕様 YAML を定義する場合、pip の依存関係として 1.0.45 以降のバージョンの azureml-defaults を列挙します。 このパッケージは、Web サービスとしてモデルをホストするために必要です。

この時点で、通常どおりにサービスを操作できます。 次のコードは、サービスにデータを送信する方法を示しています。

```python
import json

test_sample = json.dumps({'data': [
    [1, 2, 3, 4, 5, 6, 7, 8, 9, 10],
    [10, 9, 8, 7, 6, 5, 4, 3, 2, 1]
]})

test_sample = bytes(test_sample, encoding='utf8')

prediction = service.run(input_data=test_sample)
print(prediction)
```

Python 環境のカスタマイズの詳細については、[トレーニングとデプロイのための環境の作成と管理](how-to-use-environments.md)に関するページを参照してください。 

### <a name="update-the-service"></a>サービスの更新

ローカル テスト中に、ログ記録を追加したり、発見した問題の解決を試みるために、`score.py` ファイルを更新する必要がある場合があります。 変更を `score.py` ファイルに再度読み込むには、`reload()` を使用します。 たとえば、次のコードは、サービスのスクリプトを再度読み込み、サービスにデータを送信します。 データは、更新された `score.py` ファイルを使用してスコア付けされます。

> [!IMPORTANT]
> `reload` メソッドは、ローカル デプロイでのみ使用できます。 デプロイを別のコンピューティング先に更新する方法については、[Web サービスを更新する方法](how-to-deploy-update-web-service.md)に関する記事を参照してください。

```python
service.reload()
print(service.run(input_data=test_sample))
```

> [!NOTE]
> スクリプトは、サービスによって使用される `InferenceConfig` オブジェクトによって指定された場所から再度読み込まれます。

モデル、Conda の依存関係、またはデプロイ構成を変更するには、[update()](/python/api/azureml-core/azureml.core.webservice%28class%29#update--args-) を使用します。 次の例では、サービスで使用されるモデルを更新します。

```python
service.update([different_model], inference_config, deployment_config)
```

### <a name="delete-the-service"></a>サービスの削除

サービスを削除するには、[delete()](/python/api/azureml-core/azureml.core.webservice%28class%29#delete--) を使用します。

### <a name="inspect-the-docker-log"></a><a id="dockerlog"></a> Docker ログの確認

サービス オブジェクトから詳細な Docker エンジン ログ メッセージを出力できます。 ACI、AKS、およびローカル デプロイのログを表示できます。 次の例は、ログを出力する方法を示しています。

```python
# if you already have the service object handy
print(service.get_logs())

# if you only know the name of the service (note there might be multiple services with the same name but different version number)
print(ws.webservices['mysvc'].get_logs())
```

ログに `Booting worker with pid: <pid>` の行が何度も表示されている場合、これはワーカーを起動するのに十分なメモリがないことを意味します。
このエラーを解決するには、`deployment_config` にある `memory_gb` の値を増やします

## <a name="next-steps"></a>次のステップ

デプロイの詳細については、以下を参照してください。

* [リモート デプロイのトラブルシューティング方法](how-to-troubleshoot-deployment.md)
* [Azure Machine Learning 推論 HTTP サーバー](how-to-inference-server-http.md)
* [デプロイする方法とその場所](how-to-deploy-and-where.md)
* [チュートリアル:モデルのトレーニングとデプロイ](tutorial-train-models-with-aml.md)
* [ローカルで実験を実行およびデバッグする方法](./how-to-debug-visual-studio-code.md)