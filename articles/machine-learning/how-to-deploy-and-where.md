---
title: 機械学習モデルをデプロイする方法
titleSuffix: Azure Machine Learning
description: 機械学習モデルをデプロイする方法と場所について説明します。 Azure Container Instances、Azure Kubernetes Service、Azure IoT Edge、および FPGA にデプロイします。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: gopalv
author: gvashishtha
ms.reviewer: larryfr
ms.date: 04/21/2021
ms.topic: conceptual
ms.custom: devx-track-python, deploy, devx-track-azurecli, contperf-fy21q2, contperf-fy21q4
adobe-target: true
ms.openlocfilehash: dd5f4b610ea179b83d1deafd4b79ee011f32415b
ms.sourcegitcommit: 80d311abffb2d9a457333bcca898dfae830ea1b4
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/26/2021
ms.locfileid: "110462726"
---
# <a name="deploy-machine-learning-models-to-azure"></a>機械学習モデルを Azure にデプロイする 

機械学習またはディープ ラーニング モデルを Web サービスとして Azure クラウドにデプロイする方法について説明します。

> [!TIP]
> マネージド オンライン エンドポイント (プレビュー) を使用すると、基盤となるインフラストラクチャを作成および管理することなく、トレーニング済みモデルをデプロイすることができます。 詳細については、「[マネージド オンライン エンドポイントを使用して機械学習モデルをデプロイおよびスコアリングする (プレビュー)](how-to-deploy-managed-online-endpoints.md)」を参照してください。

モデルをどこにデプロイするかに関係なく、ワークフローは同様です。

1. モデルを登録する
1. エントリ スクリプトを用意する
1. 推論構成ファイルを準備します
1. モデルをローカルに展開して、すべてが正しく動作することを確認する
1. コンピューティング ターゲットを選択する
1. モデルをクラウドに再展開する
1. 結果の Web サービスをテストする

機械学習デプロイ ワークフローに関連する概念の詳細については、[Azure Machine Learning でのモデルの管理、デプロイ、監視](concept-model-management-and-deployment.md)に関する記事を参照してください。

## <a name="prerequisites"></a>前提条件

# <a name="azure-cli"></a>[Azure CLI](#tab/azcli)

- Azure Machine Learning ワークスペース。 詳細については、[Azure Machine Learning ワークスペースの作成](how-to-manage-workspace.md)に関するページをご覧ください。
- モデルです。 トレーニング済みのモデルがない場合、[こちらのチュートリアル](https://aka.ms/azml-deploy-cloud)で提供されているモデルと依存関係のファイルを使用できます。
- [Machine Learning service 用 Azure コマンド ライン インターフェイス (CLI) の拡張機能](reference-azure-machine-learning-cli.md)。
- [コンピューティング インスタンス](how-to-create-manage-compute-instance.md)などの、Docker を実行できるコンピューター。

# <a name="python"></a>[Python](#tab/python)

- Azure Machine Learning ワークスペース。 詳細については、[Azure Machine Learning ワークスペースの作成](how-to-manage-workspace.md)に関するページをご覧ください。
- モデルです。 トレーニング済みのモデルがない場合、[こちらのチュートリアル](https://aka.ms/azml-deploy-cloud)で提供されているモデルと依存関係のファイルを使用できます。
- [Python 用 Azure Machine Learning ソフトウェア開発キット (SDK)](/python/api/overview/azure/ml/intro)。
- [コンピューティング インスタンス](how-to-create-manage-compute-instance.md)などの、Docker を実行できるコンピューター。
---

## <a name="connect-to-your-workspace"></a>ワークスペースに接続する

# <a name="azure-cli"></a>[Azure CLI](#tab/azcli)

推奨

```azurecli-interactive
az login
az account set -s <my subscription>
az ml workspace list --resource-group=<my resource group>
```

アクセス権のあるワークスペースを表示します。

# <a name="python"></a>[Python](#tab/python)

```python
from azureml.core import Workspace
ws = Workspace(subscription_id="<subscription_id>",
               resource_group="<resource_group>",
               workspace_name="<workspace_name>")
```

SDK を使用してワークスペースに接続する方法の詳細については、[Azure Machine Learning SDK for Python](/python/api/overview/azure/ml/intro#workspace) のドキュメントをご覧ください。


---

## <a name="register-your-model"></a><a id="registermodel"></a> モデルを登録する

展開する機械学習サービスには通常、次のものが必要です。
    
 + 展開する特定のモデルを表すリソース (例: Pytorch のモデルのファイル)
 + 特定の入力に応じてモデルを実行するために、そのサービス上で実行するコード

Azure Machine Learnings では、サービスを 2 つの部分に分けて展開することにより、それまでと同じコードを維持しつつモデルを更新できます。 コードとは "_別に_" モデルをアップロードする仕組みを "モデルの登録" と定義しています。

モデルを登録すると、そのモデルは (ワークスペースの既定のストレージ アカウントの) クラウドにアップロードされ、Web サービスを実行しているコンピューターにマウントされます。

次の例では、モデルを登録する方法を示します。

[!INCLUDE [trusted models](../../includes/machine-learning-service-trusted-model.md)]

# <a name="azure-cli"></a>[Azure CLI](#tab/azcli)

### <a name="register-a-model-from-a-local-file"></a>ローカル ファイルからモデルを登録する

[!notebook-python[] (~/azureml-examples-main/python-sdk/tutorials/deploy-local/2.deploy-local-cli.ipynb?name=register-model-from-local-file-code)]

`-p` を、登録したいフォルダまたはファイルのパスに設定してください。

`az ml model register` に関する詳細については、[リファレンス ドキュメント](/cli/azure/ext/azure-cli-ml/ml/model)を参照してください。

### <a name="register-a-model-from-an-azure-ml-training-run"></a>Azure ML トレーニングの実行からモデルを登録する

```azurecli-interactive
az ml model register -n bidaf_onnx --asset-path outputs/model.onnx --experiment-name myexperiment --run-id myrunid --tag area=qna
```

[!INCLUDE [install extension](../../includes/machine-learning-service-install-extension.md)]

`--asset-path` パラメーターは、クラウドでのモデルの場所を示します。 この例では、1 つのファイルへのパスが使用されます。 モデルの登録に複数のファイルを含めるには、`--asset-path` を、それらのファイルが含まれているフォルダーのパスに設定します。

`az ml model register` に関する詳細については、[リファレンス ドキュメント](/cli/azure/ml/model)を参照してください。

# <a name="python"></a>[Python](#tab/python)

### <a name="register-a-model-from-a-local-file"></a>ローカル ファイルからモデルを登録する

モデルのローカル パスを指定することで、モデルを登録できます。 ローカル コンピューター上のフォルダーまたは単一のファイルのパスを指定できます。
<!-- pyhton nb call -->
[!notebook-python[] (~/azureml-examples-main/python-sdk/tutorials/deploy-local/1.deploy-local.ipynb?name=register-model-from-local-file-code)]


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

[!INCLUDE [write entry script](../../includes/machine-learning-dummy-entry-script.md)]


## <a name="define-an-inference-configuration"></a>推論構成を定義する

推論構成には、Web サービスの初期化時に使用する Docker コンテナーとファイルを記述します。 Web サービスを展開するときは、ソース ディレクトリにあるすべてのファイル (サブディレクトリを含む) を圧縮してクラウドにアップロードします。

下の推論構成では、展開する機械学習サービスで、`./source_dir` ディレクトリの `echo_score.py` ファイルによって受信要求を処理することと、`project_environment` 環境で指定した Python パッケージを Docker イメージで使用することを指定しています。

プロジェクトの環境を作成するときは、任意の [Azure Machine Learning でキュレーションされた環境](./resource-curated-environments.md)を、ベースの Docker イメージとして使用できます。 必要な依存関係がそこにインストールされ、作成された Docker イメージが、皆さまのワークスペースに紐付いたレポジトリに保存されます。

# <a name="azure-cli"></a>[Azure CLI](#tab/azcli)

最小限の推論構成は、次のように記述することができます。

:::code language="json" source="~/azureml-examples-main/python-sdk/tutorials/deploy-local/dummyinferenceconfig.json":::

`dummyinferenceconfig.json` という名前でこのファイルを保存します。


推論構成に関する詳細な説明については、[こちらの記事を参照](./reference-azure-machine-learning-cli.md#inference-configuration-schema)してください。 

# <a name="python"></a>[Python](#tab/python)

次の例で、上で用意したダミーのスコアリング スクリプトを使用して、pip 関連の依存関係がない最小の環境を作成する方法を示します。

[!notebook-python[] (~/azureml-examples-main/python-sdk/tutorials/deploy-local/1.deploy-local.ipynb?name=inference-configuration-code)]

環境の詳細については、[トレーニングとデプロイのための環境の作成と管理](how-to-use-environments.md)に関する記事を参照してください。

推論構成の詳細については、[InferenceConfig](/python/api/azureml-core/azureml.core.model.inferenceconfig) クラスのドキュメントを参照してください。

---


## <a name="define-a-deployment-configuration"></a>デプロイ構成を定義する

展開構成では、Web サービスを動作させるために予約が必要なメモリの量およびコアの数と、必要な処理を実際に実行する Web サービスの構成の詳細を指定します。 展開構成ではたとえば、メモリ 2 GB、CPU コア 2 個、GPU コア 1 個をサービスで使用し、自動スケーリングを有効にすることを指定できます。

デプロイ構成で使用できるオプションは、選択するコンピューティング ターゲットによって異なります。 ローカルに展開する場合は、Web サービスで使用するポートだけを指定できます。

# <a name="azure-cli"></a>[Azure CLI](#tab/azcli)

[!INCLUDE [aml-local-deploy-config](../../includes/machine-learning-service-local-deploy-config.md)]

詳細については、[こちらのリファレンス](./reference-azure-machine-learning-cli.md#deployment-configuration-schema)を参照してください。

# <a name="python"></a>[Python](#tab/python)

ローカル用の展開構成は次の手順で作成します。

[!notebook-python[] (~/azureml-examples-main/python-sdk/tutorials/deploy-local/1.deploy-local.ipynb?name=deployment-configuration-code)]

---

## <a name="deploy-your-machine-learning-model"></a>機械学習モデルをデプロイする

これでモデルをデプロイする準備が整いました。 

[!INCLUDE [aml-deploy-service](../../includes/machine-learning-deploy-service.md)]


## <a name="call-into-your-model"></a>モデルに対する呼び出しを行う

エコー モデルを正しく展開できたことを確認しましょう。 単純な liveness の要求やスコアリング要求を実行できるはずです。

# <a name="azure-cli"></a>[Azure CLI](#tab/azcli)
<!-- cli nb call -->

[!notebook-python[] (~/azureml-examples-main/python-sdk/tutorials/deploy-local/2.deploy-local-cli.ipynb?name=call-into-model-code)]

# <a name="python"></a>[Python](#tab/python)
<!-- python nb call -->
[!notebook-python[] (~/azureml-examples-main/python-sdk/tutorials/deploy-local/1.deploy-local.ipynb?name=call-into-model-code)]

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

inf_config = InferenceConfig(environment=env, source_directory='./source_dir', entry_script='./score.py')
```

詳細については、[LocalWebservice](/python/api/azureml-core/azureml.core.webservice.local.localwebservice)、[Model.deploy()](/python/api/azureml-core/azureml.core.model.model#deploy-workspace--name--models--inference-config-none--deployment-config-none--deployment-target-none--overwrite-false-)、および [Webservice](/python/api/azureml-core/azureml.core.webservice.webservice) のドキュメントを参照してください。

---
Deploy your service again: null
---

# <a name="azure-cli"></a>[Azure CLI](#tab/azcli)

`bidaf_onnx:1` を、モデルの名前とそのバージョン番号に置き換えます。

[!notebook-python[] (~/azureml-examples-main/python-sdk/tutorials/deploy-local/2.deploy-local-cli.ipynb?name=re-deploy-model-code)]

# <a name="python"></a>[Python](#tab/python)

[!notebook-python[] (~/azureml-examples-main/python-sdk/tutorials/deploy-local/1.deploy-local.ipynb?name=re-deploy-model-code)]

[!notebook-python[] (~/azureml-examples-main/python-sdk/tutorials/deploy-local/1.deploy-local.ipynb?name=re-deploy-model-print-logs)]

詳細についてはね[Model.deploy()](/python/api/azureml-core/azureml.core.model.model#deploy-workspace--name--models--inference-config-none--deployment-config-none--deployment-target-none--overwrite-false-) および [Webservice](/python/api/azureml-core/azureml.core.webservice.webservice) のドキュメントを参照してください。

---
次に、POST 要求をサービスに送信できることを確認します。

# <a name="azure-cli"></a>[Azure CLI](#tab/azcli)

[!notebook-python[] (~/azureml-examples-main/python-sdk/tutorials/deploy-local/2.deploy-local-cli.ipynb?name=send-post-request-code)]

# <a name="python"></a>[Python](#tab/python)

[!notebook-python[] (~/azureml-examples-main/python-sdk/tutorials/deploy-local/1.deploy-local.ipynb?name=send-post-request-code)]

---

## <a name="choose-a-compute-target"></a>コンピューティング ターゲットを選択する

コンピューティング先を選ぶときは下の図を参考にしてください。

[![コンピューティング先を選ぶ方法](./media/how-to-deploy-and-where/how-to-choose-target.png)](././media/how-to-deploy-and-where/how-to-choose-target.png#lightbox)

[!INCLUDE [aml-deploy-target](../../includes/aml-compute-target-deploy.md)]

## <a name="re-deploy-to-cloud"></a>クラウドに再展開する

サービスがローカルで動作することを確認してリモートのコンピューティング先を選んだら、クラウドに展開する準備は完了です。 

選択したコンピューティング先 (この場合は Azure Container Instances) に合わせて展開構成を変更します。

# <a name="azure-cli"></a>[Azure CLI](#tab/azcli)

デプロイ構成で使用できるオプションは、選択するコンピューティング ターゲットによって異なります。

:::code language="json" source="~/azureml-examples-main/python-sdk/tutorials/deploy-local/re-deploymentconfig.json":::

このファイルを `re-deploymentconfig.json` として保存します。

詳細については、[こちらのリファレンス](./reference-azure-machine-learning-cli.md#deployment-configuration-schema)を参照してください。

# <a name="python"></a>[Python](#tab/python)

[!notebook-python[] (~/azureml-examples-main/python-sdk/tutorials/deploy-local/1.deploy-local.ipynb?name=deploy-model-on-cloud-code)]

---

もう一度サービスを展開します。


# <a name="azure-cli"></a>[Azure CLI](#tab/azcli)

`bidaf_onnx:1` を、モデルの名前とそのバージョン番号に置き換えます。



[!notebook-python[] (~/azureml-examples-main/python-sdk/tutorials/deploy-local/2.deploy-local-cli.ipynb?name=deploy-model-on-cloud-code)]

# <a name="python"></a>[Python](#tab/python)


[!notebook-python[] (~/azureml-examples-main/python-sdk/tutorials/deploy-local/1.deploy-local.ipynb?name=re-deploy-service-code)]

[!notebook-python[] (~/azureml-examples-main/python-sdk/tutorials/deploy-local/1.deploy-local.ipynb?name=re-deploy-service-print-logs)]

詳細についてはね[Model.deploy()](/python/api/azureml-core/azureml.core.model.model#deploy-workspace--name--models--inference-config-none--deployment-config-none--deployment-target-none--overwrite-false-) および [Webservice](/python/api/azureml-core/azureml.core.webservice.webservice) のドキュメントを参照してください。

---


## <a name="call-your-remote-webservice"></a>リモートの Web サービスを呼び出す

リモートに展開する場合、キー認証が有効になっている場合があります。 下の例では、推論要求を行うために Python でサービス キーを取得する方法を示します。

[!notebook-python[] (~/azureml-examples-main/python-sdk/tutorials/deploy-local/1.deploy-local.ipynb?name=call-remote-web-service-code)]

[!notebook-python[] (~/azureml-examples-main/python-sdk/tutorials/deploy-local/1.deploy-local.ipynb?name=call-remote-webservice-print-logs)]



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


[!notebook-python[] (~/azureml-examples-main/python-sdk/tutorials/deploy-local/2.deploy-local-cli.ipynb?name=delete-resource-code)]

[!notebook-python[] (~/azureml-examples-main/python-sdk/tutorials/deploy-local/2.deploy-local-cli.ipynb?name=delete-your-resource-code)]

デプロイされた Web サービスを削除するには、`az ml service delete <name of webservice>` を使用します。

ワークスペースから登録済みのモデルを削除するには、`az ml model delete <model id>` を使用します。

[Web サービスの削除](/cli/azure/ml/service#az_ml_service_delete)と[モデルの削除](/cli/azure/ml/model#az_ml_model_delete)に関する詳細を確認してください。

# <a name="python"></a>[Python](#tab/python)

[!notebook-python[] (~/azureml-examples-main/python-sdk/tutorials/deploy-local/1.deploy-local.ipynb?name=delete-resource-code)]

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
