---
title: チュートリアル:ノートブックを使用して予測モデルを作成する (パート 1/2)
titleSuffix: Azure Machine Learning
description: Microsoft Power BI で機械学習モデルを使用して結果を予測できるように、Jupyter Notebook でコードを使用して機械学習モデルを作成、デプロイする方法について説明します。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
ms.author: samkemp
author: samuel100
ms.reviewer: sdgilley
ms.date: 12/11/2020
ms.openlocfilehash: f8209c0d26cf8c572d10666696231b0468cfcbc6
ms.sourcegitcommit: 1bdcaca5978c3a4929cccbc8dc42fc0c93ca7b30
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/13/2020
ms.locfileid: "97370840"
---
# <a name="tutorial-power-bi-integration---create-the-predictive-model-with-a-notebook-part-1-of-2"></a>チュートリアル:Power BI 統合 - ノートブックを使用して予測モデルを作成する (パート 1/2)

このチュートリアルのパート 1 では、Jupyter Notebook でコードを使用して予測機械学習モデルをトレーニング、デプロイします。 パート 2 では、Microsoft Power BI でそのモデルを使用して結果を予測します。

このチュートリアルでは、次の作業を行いました。

> [!div class="checklist"]
> * Jupyter Notebook の作成
> * Azure Machine Learning コンピューティング インスタンスを作成する
> * scikit-learn を使用して回帰モデルをトレーニングする
> * リアルタイム スコアリング エンドポイントにモデルをデプロイする

Power BI で使用するモデルは、3 とおりの方法で作成、デプロイすることができます。  この記事では、オプション A (Notebook を使用してモデルをトレーニング、デプロイする) を取り上げます。  このオプションで紹介するのは、Azure Machine Learning スタジオにホストされた Jupyter ノートブックを使用した、コード優先の作成エクスペリエンスです。 

その他、次の方法を使用できます。

* [オプション B: デザイナーを使用してモデルをトレーニング、デプロイする](tutorial-power-bi-designer-model.md) - デザイナーを使用してわずかなコードを記述するだけの作成エクスペリエンス (ドラッグ アンド ドロップ ユーザー インターフェイス)。
* [オプション C: 自動 ML を使用してモデルをトレーニング、デプロイする](tutorial-power-bi-automated-model.md) - データ準備とモデルのトレーニングを完全に自動化する、コーディング不要の作成エクスペリエンス。


## <a name="prerequisites"></a>[前提条件]

- Azure サブスクリプション ([無料試用版を利用できます](https://aka.ms/AMLFree))。 
- Azure Machine Learning ワークスペース。 まだワークスペースをお持ちでない場合は、[Azure Machine Learning ワークスペースの作成方法](./how-to-manage-workspace.md#create-a-workspace)に関する手順に従ってください。
- Python 言語と機械学習ワークフローの基礎知識。

## <a name="create-a-notebook-and-compute"></a>ノートブックを作成してコンピューティングを実行する

[Azure Machine Learning スタジオ](https://ml.azure.com)のホームページで **[新規作成]** を選択し、 **[ノートブック]** を選択します。

:::image type="content" source="media/tutorial-power-bi/create-new-notebook.png" alt-text="ノートブックの作成方法を示すスクリーンショット":::
 
**新しいファイルを作成する** ためのダイアログ ボックスが表示されたら、次の情報を入力します。

1. ノートブックのファイル名 (例: `my_model_notebook`)
1. **[ファイルの種類]** を **[ノートブック]** に変更します

**［作成］** を選択します 次に、コード セルを実行するために、コンピューティングを作成してノートブックにアタッチする必要があります。 そのために、ノートブックの上部にあるプラス アイコンを選択してください。

:::image type="content" source="media/tutorial-power-bi/create-compute.png" alt-text="コンピューティング インスタンスの作成方法を示すスクリーンショット":::

次に、 **[Create compute instance]\(コンピューティング インスタンスの作成\)** ページで次の作業を行います。

1. 仮想マシンの CPU サイズを選択します。このチュートリアルの用途であれば、**Standard_D11_v2** (2 コア、14 GB RAM) をお勧めします。
1. **[次へ]** を選択します。 
1. **[Configure Settings]\(構成の設定\)** ページで、有効な **コンピューティング名** を入力します (有効な文字は大文字、小文字、数字、ハイフンです)。
1. **［作成］** を選択します

ノートブックを見ると、 **[コンピューティング]** の横にある円がシアンに変化し、コンピューティング インスタンスが作成中であることがわかります。

:::image type="content" source="media/tutorial-power-bi/creating.png" alt-text="コンピューティングが作成中であることを示すスクリーンショット":::

> [!NOTE]
> コンピューティングがプロビジョニングされるまでに 2 分から 4 分ほどかかる場合があります。

コンピューティングのプロビジョニングが完了したら、ノートブックを使用してコード セルを実行できます。 たとえば、セルに次のように入力します。

```python
import numpy as np

np.sin(3)
```

続けて **Shift + Enter** キーを押します (または **Control + Enter** キーを押すか、セルの横にある再生ボタンを選択します)。 次の出力が表示されます。

:::image type="content" source="media/tutorial-power-bi/simple-sin.png" alt-text="セルの実行を示すスクリーンショット":::

以上で、機械学習モデルを作成する準備は完了です。

## <a name="build-a-model-using-scikit-learn"></a>scikit-learn を使用してモデルを作成する

このチュートリアルでは、[Diabetes (糖尿病) データセット](https://www4.stat.ncsu.edu/~boos/var.select/diabetes.html)を使用します。これは [Azure Open Datasets](https://azure.microsoft.com/services/open-datasets/) から入手できます。 


### <a name="import-data"></a>データのインポート

データをインポートするために、次のコードをコピーしてノートブックの新しい **コード セル** に貼り付けます。

```python
from azureml.opendatasets import Diabetes

diabetes = Diabetes.get_tabular_dataset()
X = diabetes.drop_columns("Y")
y = diabetes.keep_columns("Y")
X_df = X.to_pandas_dataframe()
y_df = y.to_pandas_dataframe()
X_df.info()
```

Pandas データ フレーム `X_df` には、10 個のベースライン入力変数が含まれています (年齢、性、肥満度指数、平均血圧、および血清に関する 6 つの測定値)。 Pandas データ フレーム `y_df` はターゲット変数で、調査開始から 1 年経過時の疾患の進行を表す定量的測定値を保持します。 レコード数は合計 442 件です。

### <a name="train-model"></a>モデルのトレーニング

ノートブックに新しい **コード セル** を作成し、次のコード スニペットをコピーして貼り付けます。リッジ回帰モデルを構築し、Python の Pickle 形式を使用してモデルをシリアル化するものです。

```python
import joblib
from sklearn.linear_model import Ridge

model = Ridge().fit(X,y)
joblib.dump(model, 'sklearn_regression_model.pkl')
```

### <a name="register-the-model"></a>モデルを登録する

登録したモデルには、モデル ファイルそのものの内容に加えて、モデルのメタデータ (モデルの説明、タグ、フレームワーク情報) も格納されます。ワークスペースのモデルを管理したりデプロイしたりする際に、これらのメタデータが役立ちます。 たとえばワークスペース内のモデルを一覧表示するときであれば、タグを使用してモデルを分類し、フィルターを適用することができます。 また、このモデルを scikit-learn フレームワークでマークすれば、後で説明するように、Web サービスとして容易にデプロイすることができます。

次のコードをコピーして、ノートブックの新しい **コード セル** に貼り付けてください。

```python
import sklearn

from azureml.core import Workspace
from azureml.core import Model
from azureml.core.resource_configuration import ResourceConfiguration

ws = Workspace.from_config()

model = Model.register(workspace=ws,
                       model_name='my-sklearn-model',                # Name of the registered model in your workspace.
                       model_path='./sklearn_regression_model.pkl',  # Local file to upload and register as a model.
                       model_framework=Model.Framework.SCIKITLEARN,  # Framework used to create the model.
                       model_framework_version=sklearn.__version__,  # Version of scikit-learn used to create the model.
                       sample_input_dataset=X,
                       sample_output_dataset=y,
                       resource_configuration=ResourceConfiguration(cpu=2, memory_in_gb=4),
                       description='Ridge regression model to predict diabetes progression.',
                       tags={'area': 'diabetes', 'type': 'regression'})

print('Name:', model.name)
print('Version:', model.version)
```

Azure Machine Learning スタジオで、左側のメニューにある **[エンドポイント]** に移動してモデルを表示することもできます。

:::image type="content" source="media/tutorial-power-bi/model.png" alt-text="モデルを示すスクリーンショット":::

### <a name="define-the-scoring-script"></a>スコアリング スクリプトを定義する

モデルをデプロイして Microsoft Power BI に統合する際は、Python の "*スコアリング スクリプト*" とカスタム環境を定義する必要があります。 スコアリング スクリプトには、次の 2 つの関数が含まれます。

- `init()` - サービスが開始されると、この関数が実行されます。 モデルは、この関数によって読み込まれ (モデルは自動的にモデル レジストリからダウンロードされることに注意してください)、逆シリアル化されます。
- `run(data)` - スコアリングを必要とする入力データを使用してサービスを呼び出すと、この関数が実行されます。 

>[!NOTE]
> ここでは、Python デコレーターを使用して入力データと出力データのスキーマを定義します。Microsoft Power BI 統合が正しく機能するためには、これが重要となります。

次のコードをコピーして、ノートブックの新しい **コード セル** に貼り付けてください。 次のコード スニペットには、score.py という名前のファイルにコードを書き込むセル マジックが記述されています。

```python
%%writefile score.py

import json
import pickle
import numpy as np
import pandas as pd
import os
import joblib
from azureml.core.model import Model

from inference_schema.schema_decorators import input_schema, output_schema
from inference_schema.parameter_types.numpy_parameter_type import NumpyParameterType
from inference_schema.parameter_types.pandas_parameter_type import PandasParameterType


def init():
    global model
    # Replace filename if needed.
    path = os.getenv('AZUREML_MODEL_DIR') 
    model_path = os.path.join(path, 'sklearn_regression_model.pkl')
    # Deserialize the model file back into a sklearn model.
    model = joblib.load(model_path)


input_sample = pd.DataFrame(data=[{
    "AGE": 5,
    "SEX": 2,
    "BMI": 3.1,
    "BP": 3.1,
    "S1": 3.1,
    "S2": 3.1,
    "S3": 3.1,
    "S4": 3.1,
    "S5": 3.1,
    "S6": 3.1
}])

# This is an integer type sample. Use the data type that reflects the expected result.
output_sample = np.array([0])

# To indicate that we support a variable length of data input,
# set enforce_shape=False
@input_schema('data', PandasParameterType(input_sample))
@output_schema(NumpyParameterType(output_sample))
def run(data):
    try:
        print("input_data....")
        print(data.columns)
        print(type(data))
        result = model.predict(data)
        print("result.....")
        print(result)
    # You can return any data type, as long as it is JSON serializable.
        return result.tolist()
    except Exception as e:
        error = str(e)
        return error
```

### <a name="define-the-custom-environment"></a>カスタム環境を定義する

次に、モデルをスコアリングするための環境を定義する必要があります。先ほど定義したスコアリング スクリプト (score.py) に必要な Python パッケージ (pandas、scikit-learn など) をこの環境に定義する必要があります。

環境を定義するために、次のコードをコピーしてノートブックの新しい **コード セル** に貼り付けます。

```python
from azureml.core.model import InferenceConfig
from azureml.core import Environment
from azureml.core.conda_dependencies import CondaDependencies

environment = Environment('my-sklearn-environment')
environment.python.conda_dependencies = CondaDependencies.create(pip_packages=[
    'azureml-defaults',
    'inference-schema[numpy-support]',
    'joblib',
    'numpy',
    'pandas',
    'scikit-learn=={}'.format(sklearn.__version__)
])

inference_config = InferenceConfig(entry_script='./score.py',environment=environment)
```

### <a name="deploy-the-model"></a>モデルをデプロイする

モデルをデプロイするために、次のコードをコピーしてノートブックの新しい **コード セル** に貼り付けます。

```python
service_name = 'my-diabetes-model'

service = Model.deploy(ws, service_name, [model], inference_config, overwrite=True)
service.wait_for_deployment(show_output=True)
```

>[!NOTE]
> サービスのデプロイには 2 分から 4 分かかることがあります。

正常にデプロイされたサービスの次の出力が表示されます。

```txt
Tips: You can try get_logs(): https://aka.ms/debugimage#dockerlog or local deployment: https://aka.ms/debugimage#debug-locally to debug if deployment takes longer than 10 minutes.
Running......................................................................................
Succeeded
ACI service creation operation finished, operation "Succeeded"
```

Azure Machine Learning スタジオで、左側のメニューにある **[エンドポイント]** に移動してサービスを表示することもできます。

:::image type="content" source="media/tutorial-power-bi/endpoint.png" alt-text="エンドポイントを示すスクリーンショット":::

Web サービスが想定どおりに機能することを確認するために、テストを行うようお勧めします。 Azure Machine Learning スタジオの左側のメニューにある **[Notebooks]** を選択してノートブックに戻りましょう。 サービスをテストするために、次のコードをコピーしてノートブックの新しい **コード セル** に貼り付けます。

```python
import json


input_payload = json.dumps({
    'data': X_df[0:2].values.tolist(),
    'method': 'predict'  # If you have a classification model, you can get probabilities by changing this to 'predict_proba'.
})

output = service.run(input_payload)

print(output)
```

出力は、`{'predict': [[205.59], [68.84]]}` のような JSON 構造になっています。

## <a name="next-steps"></a>次のステップ

このチュートリアルでは、Microsoft Power BI で利用できるような形でモデルを作成、デプロイする方法を見てきました。 次のパートでは、このモデルを Power BI レポートから利用する方法について説明します。

> [!div class="nextstepaction"]
> [チュートリアル: Power BI でモデルを利用する](/power-bi/connect-data/service-aml-integrate?context=azure/machine-learning/context/ml-context)
