---
title: チュートリアル:ノートブックを使用して予測モデルを作成する (パート 1/2)
titleSuffix: Azure Machine Learning
description: Jupyter Notebook でコードを使用して、機械学習モデルを構築およびデプロイする方法を学習します。 また、Microsoft Power BI に統合しやすいよう、入力と出力を定義するスコアリング スクリプトを作成します。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
ms.author: samkemp
author: samuel100
ms.reviewer: sdgilley
ms.date: 12/11/2020
ms.openlocfilehash: 6e71116d79496340c306628a29504aa140e9d5f5
ms.sourcegitcommit: f29615c9b16e46f5c7fdcd498c7f1b22f626c985
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/04/2021
ms.locfileid: "129429910"
---
# <a name="tutorial-power-bi-integration---create-the-predictive-model-with-a-jupyter-notebook-part-1-of-2"></a>チュートリアル:Power BI 統合 - Jupyter Notebook を使用して予測モデルを作成する (パート 1/2)

このチュートリアルのパート 1 では、Jupyter Notebook でコードを使用して予測機械学習モデルをトレーニングおよびデプロイします。 また、Power BI への統合を視野に、モデルの入力スキーマと出力スキーマを定義するスコアリング スクリプトを作成します。  パート 2 では、Microsoft Power BI でそのモデルを使用して結果を予測します。

このチュートリアルでは、次の作業を行いました。

> [!div class="checklist"]
> * Jupyter Notebook を作成する
> * Azure Machine Learning コンピューティング インスタンスを作成する。
> * Scikit-learn を使用して回帰モデルをトレーニングする。
> * Microsoft Power BI に統合しやすいよう、入力と出力を定義するスコアリング スクリプトを作成する。
> * リアルタイム スコアリング エンドポイントにモデルをデプロイする。

Power BI で使用するモデルは、3 とおりの方法で作成してデプロイすることができます。  この記事では、"オプション A: ノートブックを使用してモデルをトレーニングおよびデプロイする" について説明します。  このオプションは、コード優先の作成エクスペリエンスです。 Azure Machine Learning スタジオでホストされている Jupyter Notebook を使用します。 

ただし、代わりに、その他のオプションのいずれかを使用することもできます。

* [オプション B: Azure Machine Learning デザイナーを使用してモデルをトレーニングおよびデプロイする](tutorial-power-bi-designer-model.md)。 わずかなコードを記述するだけのこの作成エクスペリエンスでは、ドラッグ アンド ドロップ ユーザー インターフェイスを使用します。
* [オプション C: 自動機械学習を使用してモデルをトレーニングおよびデプロイする](tutorial-power-bi-automated-model.md)。 コーディング不要のこの作成エクスペリエンスでは、データ準備とモデルのトレーニングが完全に自動化されます。


## <a name="prerequisites"></a>前提条件

- Azure サブスクリプション。 まだサブスクリプションをお持ちでない場合は、[無料試用版](https://azure.microsoft.com/free/)を使用できます。 
- Azure Machine Learning ワークスペース。 ワークスペースがまだない場合は、「[Azure Machine Learning ワークスペースを作成して管理する](./how-to-manage-workspace.md#create-a-workspace)」を参照してください。
- Python 言語と機械学習ワークフローの基礎知識。

## <a name="create-a-notebook-and-compute"></a>ノートブックを作成してコンピューティングを実行する

[**Azure Machine Learning スタジオ**](https://ml.azure.com)のホーム ページで **[新規作成]**  >  **[ノートブック]** の順に選択します。

:::image type="content" source="media/tutorial-power-bi/create-new-notebook.png" alt-text="ノートブックの作成方法を示すスクリーンショット。":::
 
**[新しいファイルの作成]** ページで次の作業を行います。

1. ノートブックに名前を付けます (例: *my_model_notebook*)。
1. **[ファイルの種類]** を **[ノートブック]** に変更します。
1. **［作成］** を選択します 
 
次に、コード セルを実行するために、コンピューティング インスタンスを作成してご自分のノートブックにアタッチします。 まず、ノートブックの上部にあるプラス アイコンを選択します。

:::image type="content" source="media/tutorial-power-bi/create-compute.png" alt-text="コンピューティング インスタンスの作成方法を示すスクリーンショット。":::

**[Create compute instance]\(コンピューティング インスタンスの作成\)** ページで次の作業を行います。

1. CPU 仮想マシンのサイズを選択します。 このチュートリアルでは、2 コアと 14 GB の RAM を備えた **Standard_D11_v2** を選択できます。
1. **[次へ]** を選択します。 
1. **[設定の構成]** ページで、有効な **コンピューティング名** を指定します。 有効な文字は、大文字、小文字、数字、ハイフン (-) です。
1. **［作成］** を選択します

ノートブックで、 **[コンピューティング]** の横にある円がシアンに変化していることに気付くかもしれません。 この色の変化は、コンピューティング インスタンスが作成中であることを示します。

:::image type="content" source="media/tutorial-power-bi/creating.png" alt-text="コンピューティングが作成中であることを示すスクリーンショット。":::

> [!NOTE]
> コンピューティング インスタンスがプロビジョニングされるまでに 2 分から 4 分かかる場合があります。

コンピューティングのプロビジョニングが完了したら、ノートブックを使用してコード セルを実行できます。 たとえば、セルに以下のコードを入力できます。

```python
import numpy as np

np.sin(3)
```

続けて Shift + Enter キーを押します (または Control + Enter キーを押すか、セルの横にある **[再生]** ボタンを選択します)。 次の出力が表示されます。

:::image type="content" source="media/tutorial-power-bi/simple-sin.png" alt-text="セルの出力を示すスクリーンショット。":::

以上で、機械学習モデルを構築する準備は完了です。

## <a name="build-a-model-by-using-scikit-learn"></a>Scikit-learn を使用してモデルを構築する

このチュートリアルでは、[Diabetes データセット](https://www4.stat.ncsu.edu/~boos/var.select/diabetes.html)を使用します。 このデータセットは [Azure Open Datasets](https://azure.microsoft.com/services/open-datasets/) から入手できます。


### <a name="import-data"></a>データのインポート

データをインポートするために、次のコードをコピーしてご自分のノートブックの新しい "*コード セル*" に貼り付けます。

```python
from azureml.opendatasets import Diabetes

diabetes = Diabetes.get_tabular_dataset()
X = diabetes.drop_columns("Y")
y = diabetes.keep_columns("Y")
X_df = X.to_pandas_dataframe()
y_df = y.to_pandas_dataframe()
X_df.info()
```

Pandas データ フレーム `X_df` には、10 個のベースライン入力変数が含まれています。 これらの変数には、年齢、性、肥満度指数、平均血圧、血清に関する 6 つの測定値などが含まれます。 Pandas データ フレーム `y_df` はターゲット変数です。 調査開始から 1 年経過時の疾患の進行を表す定量的測定値を保持します。 このデータ フレームには、442 件のレコードが含まれています。

### <a name="train-the-model"></a>モデルをトレーニングする

ご自分のノートブックに新しい "*コード セル*" を作成します。 次に、以下のコードをコピーし、そのセルに貼り付けます。 このコード スニペットは、ねじ山回帰モデルを構築し、Python の Pickle 形式を使用してモデルをシリアル化します。

```python
import joblib
from sklearn.linear_model import Ridge

model = Ridge().fit(X_df,y_df)
joblib.dump(model, 'sklearn_regression_model.pkl')
```

### <a name="register-the-model"></a>モデルを登録する

登録済みのモデルには、モデル ファイル自体のコンテンツに加えて、メタデータが格納されます。 メタデータには、モデルの説明、タグ、およびフレームワーク情報が含まれます。 

メタデータは、ご自分のワークスペースでモデルを管理およびデプロイする際に便利です。 たとえばご自分のワークスペース内のモデルを一覧表示するときであれば、タグを使用してモデルを分類し、フィルターを適用することができます。 また、このモデルを Scikit-learn フレームワークでマークすれば、Web サービスとして容易にデプロイすることができます。

次のコードをコピーしてご自分のノートブックの新しい "*コード セル*" に貼り付けます。

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

Azure Machine Learning スタジオでモデルを表示することもできます。 左側のメニューで **[モデル]** を選択します。

:::image type="content" source="media/tutorial-power-bi/model.png" alt-text="モデルの表示方法を示すスクリーンショット。":::

## <a name="define-the-scoring-script"></a>スコアリング スクリプトを定義する

Power BI に統合するモデルをデプロイする際は、Python の "*スコアリング スクリプト*" とカスタム環境を定義する必要があります。 スコアリング スクリプトには、次の 2 つの関数が含まれます。

- `init()` 関数は、サービスの開始時に実行されます。 これによって、モデルが (自動的にモデル レジストリからダウンロードされて) 読み込まれ、逆シリアル化されます。
- `run(data)` 関数は、スコア付けが必要な入力データがサービスの呼び出しに含まれている場合に実行されます。 

>[!NOTE]
> 以下のコードにある Python デコレーターは、入力データと出力データのスキーマを定義するものです。Power BI に統合するためには、これが重要となります。

次のコードをコピーしてご自分のノートブックの新しい "*コード セル*" に貼り付けます。 次のコード スニペットには、*score.py* という名前のファイルにコードを書き込むセル マジックが記述されています。

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
    # You can return any data type, as long as it can be serialized by JSON.
        return result.tolist()
    except Exception as e:
        error = str(e)
        return error
```

### <a name="define-the-custom-environment"></a>カスタム環境を定義する

次に、モデルにスコアを付けるための環境を定義します。 この環境で、スコアリング スクリプト (*score.py*) に必要な Python パッケージ (Pandas、Scikit-learn など) を定義します。

環境を定義するために、次のコードをコピーしてご自分のノートブックの新しい "*コード セル*" に貼り付けます。

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

モデルをデプロイするために、次のコードをコピーしてご自分のノートブックの新しい "*コード セル*" に貼り付けます。

```python
service_name = 'my-diabetes-model'

service = Model.deploy(ws, service_name, [model], inference_config, overwrite=True)
service.wait_for_deployment(show_output=True)
```

>[!NOTE]
> サービスのデプロイには、2 分から 4 分かかることがあります。

サービスが正常にデプロイされると、次の出力が表示されます。

```txt
Tips: You can try get_logs(): https://aka.ms/debugimage#dockerlog or local deployment: https://aka.ms/debugimage#debug-locally to debug if deployment takes longer than 10 minutes.
Running......................................................................................
Succeeded
ACI service creation operation finished, operation "Succeeded"
```

Azure Machine Learning スタジオでサービスを表示することもできます。 左側のメニューで **[エンドポイント]** を選択します。

:::image type="content" source="media/tutorial-power-bi/endpoint.png" alt-text="サービスの表示方法を示すスクリーンショット。":::

Web サービスが想定どおりに機能することを確認するために、テストを行うようお勧めします。 ご自分のノートブックに戻るために、Azure Machine Learning スタジオの左側のメニューにある **[Notebooks]** を選択します。 その後、次のコードをコピーしてご自分のノートブックの新しい "*コード セル*" に貼り付けて、サービスをテストします。

```python
import json

input_payload = json.dumps({
    'data': X_df[0:2].values.tolist()
})

output = service.run(input_payload)

print(output)
```

出力は、`{'predict': [[205.59], [68.84]]}` のような JSON 構造になっています。

## <a name="next-steps"></a>次のステップ

このチュートリアルでは、Power BI で利用できるようにモデルを構築およびデプロイする方法を見てきました。 次のパートでは、このモデルを Power BI レポートで利用する方法について説明します。

> [!div class="nextstepaction"]
> [チュートリアル: Power BI でモデルを利用する](/power-bi/connect-data/service-aml-integrate?context=azure/machine-learning/context/ml-context)
