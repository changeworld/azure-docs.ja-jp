---
title: ローカル環境で実行および展開する方法
titleSuffix: Azure Machine Learning
description: ローカル コンピューターでトレーニング済みのモデルを実行する方法について説明します。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: laobri
author: lobrien
ms.date: 11/20/2020
ms.topic: conceptual
ms.custom: how-to, deploy
ms.openlocfilehash: fcea7c162e5c978a7c8ff1b42e09ffe0afb98f3c
ms.sourcegitcommit: c95e2d89a5a3cf5e2983ffcc206f056a7992df7d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/24/2020
ms.locfileid: "95547968"
---
# <a name="deploy-on-your-local-machines-models-trained-with-azure-machine-learning"></a>Azure Machine Learning でトレーニングされたモデルをローカル コンピューターに展開する

この記事では、Azure Machine Learning で作成されたモデルのトレーニングまたは展開のターゲットとしてローカル コンピューターを使用する方法について説明します。 Azure Machine Learning の柔軟性により、ほとんどの Python 機械学習フレームワークでそれを動作させることができます。 機械学習ソリューションには、通常、複製が困難な場合がある複雑な依存関係があります。 この記事では、全体的な制御と使いやすさのバランスを取る方法について説明します。

ローカル展開には次のようなシナリオがあります。

* プロジェクトの早い段階での、データ、スクリプト、モデルのすばやいイテレーション
* 後のステージでのデバッグとトラブルシューティング
* ユーザーが管理するハードウェアへの最終的な展開

## <a name="prerequisites"></a>前提条件

- Azure Machine Learning ワークスペース。 詳細については、[Azure Machine Learning ワークスペースの作成](how-to-manage-workspace.md)に関するページを参照してください
- モデルと環境。 トレーニング済みのモデルがない場合、[こちらのチュートリアル](tutorial-train-models-with-aml.md)で提供されているモデルと依存関係のファイルを使用できます
- [Python 用 Azure Machine Learning ソフトウェア開発キット (SDK)](/python/api/overview/azure/ml/intro?preserve-view=true&view=azure-ml-py)
- Azure Machine Learning のパッケージの依存関係をミラー化する場合は、Anaconda や miniconda などの Conda マネージャー
- Docker。Azure Machine Learning 環境のコンテナー化されたバージョンを使用する場合

## <a name="prepare-your-local-machine"></a>ローカル コンピューターを準備する

Azure Machine Learning モデルをローカル環境で実行する最も信頼性の高い方法は、Docker イメージを使用することです。 Docker イメージを使用すると、ハードウェアの問題を除いた Azure 実行環境が複製される、分離されてコンテナー化されたエクスペリエンスが提供されます。 開発シナリオ用に Docker をインストールして構成する方法の詳細については、「[Windows での Docker リモート開発の概要](/windows/dev-environment/docker/overview)」を参照してください。

Docker で実行されているプロセスにデバッガーをアタッチすることはできますが (「[実行中のコンテナーにアタッチする](https://code.visualstudio.com/docs/remote/attach-container)」を参照)、Docker を使用せずに Python コードのデバッグとイテレーションを行う方が好まれる場合があります。 このシナリオでは、Azure Machine Learning で実験を実行するときに使用するのと同じライブラリをローカル コンピューターでも使用することが重要です。 Python の依存関係の管理に、Azure では [Conda](https://docs.conda.io/) を使用します。 他のパッケージ マネージャーを使用して環境を作り直すこともできますが、ローカル コンピューターに Conda をインストールして構成するのが最も簡単な同期方法です。 

## <a name="prepare-your-entry-script"></a>エントリ スクリプトを準備する

Docker を使用してモデルと依存関係を管理する場合でも、Python のスコア付けスクリプトはローカル環境に用意する必要があります。 このスクリプトには 2 つのメソッドが必要です。

- 引数を受け取らず何も返さない `init()` メソッド 
- JSON 形式の文字列を受け取り、JSON でシリアル化できるオブジェクトを返す `run()` メソッド。

`run()` メソッドへの引数は次のような形式です。 

```json
{
    "data": <model-specific-data-structure>
}
```

`run()` メソッドから返すオブジェクトで、`toJSON() -> string` を実装する必要があります。

次の例では、登録された Scikit-learn モデルを読み込み、それを numpy データでスコア付けする方法を示します。 この例は、[このチュートリアル](tutorial-train-models-with-aml.md)のモデルと依存関係に基づいています。

```python
import json
import numpy as np
import os
import pickle
import joblib

def init():
    global model
    # AZUREML_MODEL_DIR is an environment variable created during deployment.
    # It is the path to the model folder (./azureml-models/$MODEL_NAME/$VERSION)
    # For multiple models, it points to the folder containing all deployed models (./azureml-models)
    model_path = os.path.join(os.getenv('AZUREML_MODEL_DIR'), 'sklearn_mnist_model.pkl')
    model = joblib.load(model_path)

def run(raw_data):
    data = np.array(json.loads(raw_data)['data'])
    # make prediction
    y_hat = model.predict(data)
    # you can return any data type as long as it is JSON-serializable
    return y_hat.tolist()
```

Swagger スキーマの自動生成とバイナリ データ (画像など) のスコア付け方法を含む、より高度な例については、「[高度なエントリ スクリプトの作成](how-to-deploy-advanced-entry-script.md)」を参照してください。 

## <a name="deploy-as-a-local-web-service-using-docker"></a>Docker を使用してローカル Web サービスとして展開する

Azure Machine Learning によって使用される環境を複製する最も簡単な方法は、Docker を使用して Web サービスを展開することです。 ローカル コンピューターで Docker を実行し、次のようにします。

1. モデルが登録されている Azure Machine Learning ワークスペースに接続します
1. モデルを表す `Model` オブジェクトを作成します
1. 依存関係が含まれ、コードを実行するソフトウェア環境が定義されている、`Environment` オブジェクトを作成します
1. エントリ スクリプトと `Environment` を関連付ける `InferenceConfig` オブジェクトを作成します
1. サブクラス `LocalWebserviceDeploymentConfiguration` の `DeploymentConfiguration` オブジェクトを作成します
1. `Model.deploy()` を使用して、`Webservice` オブジェクトを作成します。 このメソッドにより、Docker イメージがダウンロードされて、`Model`、`InferenceConfig`、`DeploymentConfiguration` と関連付けられます
1. `Webservice.wait_for_deployment()` を使用して `Webservice` をアクティブ化します

これらの手順を示すコードは次のようになります。

```python
from azureml.core.webservice import Webservice
from azure.core.model import InferenceConfig
from azureml.core.environment import Environment
from azureml.core import Workspace
from azureml.core.model import Model

ws = Workspace.from_config()
model = Model(ws, 'sklearn_mnist')


myenv = Environment.get(workspace=ws, name="tutorial-env", version="1")
inference_config = InferenceConfig(entry_script="score.py", environment=myenv)

deployment_config = LocalWebservice.deploy_configuration(port=6789)

local_service = Model.deploy(workspace=ws, 
                       name='sklearn-mnist-local', 
                       models=[model], 
                       inference_config=inference_config, 
                       deployment_config = deployment_config)

local_service.wait_for_deployment(show_output=True)
print(f"Scoring URI is : {local_service.scoring_uri}")
```

`Model.deploy()` の呼び出しには数分かかることがあります。 最初の展開が済むと、その後の `update()` メソッドは、ゼロから始めるより効率的に使用できるようになります。 「[デプロイされた Web サービスを更新する](how-to-deploy-update-web-service.md)」を参照してください。

### <a name="test-your-local-deployment"></a>ローカルな展開をテストする

前の展開スクリプトを実行すると、スコア付けのためにデータを POST できる URI が出力されます (例: `http://localhost:6789/score`)。 次のサンプルでは、ローカルに展開されたモデル `"sklearn-mnist-local"` を使用して、サンプル データのスコア付けを行うスクリプトを示します。 モデルが適切にトレーニングされていれば、`normalized_pixel_values` は "2" と解釈される必要があることが推測されます。 

```python
import requests

normalized_pixel_values = "[\
0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, \
0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, \
0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, \
0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.5, 0.5, 0.7, 1.0, 1.0, 0.6, 0.4, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, \
0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.7, 1.0, 1.0, 1.0, 1.0, 1.0, 1.0, 0.9, 0.1, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, \
0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.7, 1.0, 1.0, 1.0, 0.8, 0.6, 0.7, 1.0, 1.0, 0.5, 0.0, 0.0, 0.0, 0.0, 0.0, \
0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.2, 1.0, 1.0, 0.8, 0.1, 0.0, 0.0, 0.0, 0.8, 1.0, 0.5, 0.0, 0.0, 0.0, 0.0, \
0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.3, 1.0, 0.8, 0.1, 0.0, 0.0, 0.0, 0.5, 1.0, 1.0, 0.3, 0.0, 0.0, 0.0, \
0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.1, 0.1, 0.0, 0.0, 0.0, 0.0, 0.8, 1.0, 1.0, 0.3, 0.0, 0.0, \
0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.5, 1.0, 1.0, 0.8, 0.0, 0.0, \
0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.3, 1.0, 1.0, 0.9, 0.2, 0.0, \
0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.5, 1.0, 1.0, 0.6, 0.0, \
0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.7, 1.0, 1.0, 0.6, 0.0, \
0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.1, 0.9, 1.0, 0.9, 0.1, \
0.1, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.8, 1.0, 1.0, 0.6, \
0.6, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.3, 1.0, 1.0, 0.7, \
0.7, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.1, 0.8, 1.0, 1.0, \
1.0, 0.6, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.5, 1.0, 1.0, \
1.0, 0.7, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 1.0, 1.0, \
1.0, 1.0, 0.1, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 1.0, \
1.0, 1.0, 1.0, 0.2, 0.1, 0.1, 0.1, 0.1, 0.0, 0.0, 0.0, 0.1, 0.1, 0.1, 0.6, 0.6, 0.6, 0.6, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, \
0.0, 1.0, 1.0, 1.0, 1.0, 1.0, 1.0, 1.0, 1.0, 0.7, 0.6, 0.7, 1.0, 1.0, 1.0, 1.0, 1.0, 1.0, 1.0, 0.5, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, \
0.0, 0.0, 0.7, 1.0, 1.0, 1.0, 1.0, 1.0, 1.0, 1.0, 1.0, 1.0, 1.0, 1.0, 1.0, 1.0, 1.0, 0.7, 0.5, 0.5, 0.2, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, \
0.0, 0.0, 0.0, 0.0, 0.5, 0.5, 0.5, 0.5, 0.7, 1.0, 1.0, 1.0, 0.6, 0.5, 0.5, 0.2, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, \
0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, \
0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, \
0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, \
0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0]"

input_data = "{\"data\": [" + normalized_pixel_values + "]}"

headers = {'Content-Type': 'application/json'}

scoring_uri = "http://localhost:6789/score"
resp = requests.post(scoring_uri, input_data, headers=headers)

print("Should be predicted as '2'")
print("prediction:", resp.text)
```

## <a name="download-and-run-your-model-directly"></a>モデルを直接ダウンロードして実行する

Docker を使用して Web サービスとしてモデルをデプロイするのが最も一般的なオプションですが、ローカル Python スクリプトを使用して直接コードを実行することもできます。 2 つの重要な要素が必要になります。 

- モデル自体
- モデルが依存している依存関係 

モデルのダウンロードは、次のようにして行うことができます。  

- ポータルで **[モデル]** タブを選択し、目的のモデルを選択して、 **[詳細]** ページで **[ダウンロード]** を選択します
- コマンドラインから `az ml model download` を使用します ([model download リファレンス](/cli/azure/ext/azure-cli-ml/ml/model?view=azure-cli-latest#ext_azure_cli_ml_az_ml_model_download&preserve-view=false)に関するページを参照してください)
- Python SDK で、`Model.download()` メソッドを使用します ([Model API リファレンス](/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py#download-target-dir------exist-ok-false--exists-ok-none-&preserve-view=false)に関するページを参照してください)

Azure モデルは、1 つまたは複数のシリアル化された Python オブジェクトであり、Python pickle ファイル ( **.pkl** 拡張子) としてパッケージ化されています。 pickle ファイルの内容は、モデルのトレーニングに使用された ML ライブラリまたは技法によって異なります。 たとえば、チュートリアルのモデルでは、次のようにしてモデルを読み込むことができます。

```python
import pickle

with open('sklearn_mnist_model.pkl', 'rb') as f : 
    logistic_model = pickle.load(f, encoding='latin1')
```

依存関係を正しく把握することは常に難しく、特に機械学習では、特定のバージョンを必要とするわかりにくい複雑な関係になることがよくあります。 ローカル コンピューター上の Azure Machine Learning 環境は、完全な Conda 環境として、または `Environment` クラスの `build_local()` メソッドを使用することにより Docker イメージとして、作り直すことができます。 

```python
ws = Workspace.from_config()
myenv = Environment.get(workspace=ws, name="tutorial-env", version="1")
myenv.build_local(workspace=ws, useDocker=False) #Creates conda env
```

`build_local()` の引数 `useDocker` を `True` に設定すると、Conda 環境ではなく Docker イメージが作成されます。 より詳細な制御が必要な場合は、`Environment` の `save_to_directory()` メソッドを使用できます。これにより、定義ファイル **conda_dependencies.yml** と **azureml_environment.json** が作成されるので、それを微調整し、拡張機能のベースとして使用でます。 

`Environment` クラスには、コンピューティング ハードウェア、Azure ワークスペース、Docker イメージの間で環境を同期するためのメソッドが他にもいくつかあります。 詳細については、[`Environment` API リファレンス](/python/api/azureml-core/azureml.core.environment(class))に関するページを参照してください。

モデルをダウンロードし、その依存関係を解決した後は、スコア付けの実行、モデルの微調整、転移学習の使用などの方法に関して、Azure で定義されている制限はありません。 

## <a name="upload-a-retrained-model-to-azure-machine-learning"></a>再トレーニングされたモデルを Azure Machine Learning にアップロードする

ローカル環境でトレーニングまたは再トレーニングしたモデルがある場合は、Azure に登録できます。 登録が完了したら、Azure Compute を使用してそれを引き続きチューニングするか、[Azure Kubernetes Service](how-to-deploy-azure-kubernetes-service.md) や [Triton Inference Server (プレビュー)](how-to-deploy-with-triton.md) などの Azure の機能を使用して展開することができます。

Azure Machine Learning の Python SDK で使用するには、モデルをシリアル化された Python オブジェクトとして pickle 形式 (**pkl** ファイル) で格納し、JSON でシリアル化可能なオブジェクトを返す `predict(data)` メソッドを実装する必要があります。 たとえば、ローカル環境でトレーニングした Scikit-learn 糖尿病モデルを次の方法で保存できます。 

```python
import joblib

from sklearn.datasets import load_diabetes
from sklearn.linear_model import Ridge

dataset_x, dataset_y = load_diabetes(return_X_y=True)

sk_model = Ridge().fit(dataset_x, dataset_y)

joblib.dump(sk_model, "sklearn_regression_model.pkl")
```

モデルを Azure で使用できるようにするには、`Model` クラスの `register()` メソッドを使用します。

```python
from azureml.core.model import Model

model = Model.register(model_path="sklearn_regression_model.pkl",
                       model_name="sklearn_regression_model",
                       tags={'area': "diabetes", 'type': "regression"},
                       description="Ridge regression model to predict diabetes",
                       workspace=ws)
```

新しく登録したモデルは、Azure Machine Learning の **[モデル]** タブで検索できます。

:::image type="content" source="media/how-to-deploy-local/registered-model.png" alt-text="アップロードしたモデルが示されている Azure Machine Learning の [モデル] タブのスクリーンショット。":::

モデルと環境のアップロードと更新の詳細については、「[高度な使用方法でモデルを登録してローカル環境に展開する](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/deployment/deploy-to-local/register-model-deploy-local-advanced.ipynb)」を参照してください。

## <a name="next-steps"></a>次のステップ

- 環境の管理の詳細については、「[Azure Machine Learning でソフトウェア環境を作成して使用する](how-to-use-environments.md)」を参照してください
- データストアからデータへのアクセスの詳細については、「[Azure のストレージ サービスに接続する](how-to-access-data.md)」を参照してください