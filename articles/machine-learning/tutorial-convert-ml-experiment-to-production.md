---
title: 機械学習の実験コードを運用コードに変換する
titleSuffix: Azure Machine Learning
description: MLOpsPython コード テンプレートを使用して、機械学習の実験コードを運用コードに変換する方法について説明します。
author: bjcmit
ms.author: brysmith
ms.service: machine-learning
ms.topic: tutorial
ms.date: 02/10/2020
ms.openlocfilehash: b929dd8979ecf587872092af2fa44e5ae03626d1
ms.sourcegitcommit: 64def2a06d4004343ec3396e7c600af6af5b12bb
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/19/2020
ms.locfileid: "77472487"
---
# <a name="tutorial-convert-ml-experimental-code-to-production-code"></a>チュートリアル:ML の実験コードを運用コードに変換する

機械学習プロジェクトでは、実際のデータセットを使用して Jupyter Notebook などのアジャイル ツールで仮説をテストする実験が必要です。 運用のためのモデルの準備ができたら、モデル コードを運用コード リポジトリに配置する必要があります。 場合によっては、モデル コードを Python スクリプトに変換して、運用コード リポジトリに配置する必要があります。 このチュートリアルでは、実験コードを Python スクリプトにエクスポートする方法について、推奨される方法を説明します。  

このチュートリアルでは、以下の内容を学習します。

> [!div class="checklist"]
> * 不要なコードを消去する
> * Jupyter Notebook コードを関数にリファクタリングする
> * 関連タスク用の Python スクリプトを作成する
> * 単体テストを作成する

## <a name="prerequisites"></a>前提条件

- [MLOpsPython テンプレート](https://github.com/microsoft/MLOpsPython/generate)を生成し、`experimentation/Diabetes Ridge Regression Training.ipynb` と `experimentation/Diabetes Ridge Regression Scoring.ipynb` ノートブックを使用します。 これらのノートブックは、実験環境から運用に変換する例として使用されます。
- nbconvert をインストールします。 [インストール](https://nbconvert.readthedocs.io/en/latest/install.html) ページの「__nbconvert のインストール__」セクションにあるインストール手順のみに従ってください。

## <a name="remove-all-nonessential-code"></a>不要なコードをすべて削除する

実験中に作成されるコードの中には、探索的な目的でのみ使用されるものもあります。 そのため、実験コードを運用コードに変換するための最初のステップは、この不要なコードを削除することです。 不要なコードを削除すると、コードの保守性も向上します。 このセクションでは、`experimentation/Diabetes Ridge Regression Training.ipynb` ノートブックからコードを削除します。 `X` と `y` の形状を出力するステートメントおよび `features.describe` を呼び出すセルはデータ探索のためのものであり、削除できます。 不要なコードを削除すると、`experimentation/Diabetes Ridge Regression Training.ipynb` はマークダウンのない次のコードのようになります。

```python
from sklearn.datasets import load_diabetes
from sklearn.linear_model import Ridge
from sklearn.metrics import mean_squared_error
from sklearn.model_selection import train_test_split
import joblib
 
X, y = load_diabetes(return_X_y=True)

X_train, X_test, y_train, y_test = train_test_split(
        X, y, test_size=0.2, random_state=0)
data = {"train": {"X": X_train, "y": y_train},
        "test": {"X": X_test, "y": y_test}}

alpha = 0.5

reg = Ridge(alpha=alpha)
reg.fit(data["train"]["X"], data["train"]["y"])

preds = reg.predict(data["test"]["X"])
print("mse", mean_squared_error(preds, data["test"]["y"]))

model_name = "sklearn_regression_model.pkl"
joblib.dump(value=reg, filename=model_name)
```

## <a name="refactor-code-into-functions"></a>コードを関数にリファクタリングする

次に、Jupyter コードを関数にリファクタリングする必要があります。 コードを関数にリファクタリングすると、単体テストが容易になり、コードの保守性が向上します。 このセクションでは、次のようにリファクタリングします。
- Diabetes Ridge 回帰トレーニング ノートブック (`experimentation/Diabetes Ridge Regression Training.ipynb`)
- Diabetes Ridge 回帰スコアリング ノートブック (`experimentation/Diabetes Ridge Regression Scoring.ipynb`)

### <a name="refactor-diabetes-ridge-regression-training-notebook-into-functions"></a>Diabetes Ridge 回帰トレーニング ノートブックを関数にリファクタリングする
`experimentation/Diabetes Ridge Regression Training.ipynb` で、次の手順を実行します。

1. パラメーター `data` および `alpha` を取って、モデルを返す、`train_model` という関数を作成します。 
1. “Train Model on Training Set” および “Validate Model on Validation Set” の見出しの下にあるコードを `train_model` 関数にコピーします。

`train_model` 関数は次のコードのようになります。

```python
def train_model(data, alpha):
    reg = Ridge(alpha=alpha)
    reg.fit(data["train"]["X"], data["train"]["y"])
    preds = reg.predict(data["test"]["X"])
    print("mse", mean_squared_error(
        preds, data["test"]["y"]))
    return reg
```

`train_model` 関数が作成されたら、“Train Model on Training Set” および “Validate Model on Validation Set” の見出しの下にあるコードを以下のステートメントに置き換えます。

```python
reg = train_model(data, alpha)
```

前のステートメントは、`train_model` 関数を呼び出して `data` と `alpha` パラメーターを渡し、モデルを返します。

`experimentation/Diabetes Ridge Regression Training.ipynb` で、次の手順を実行します。

1. パラメーターを取らず、何も返さない、`main` という新しい関数を作成します。
1. “Load Data”、“Split Data into Training and Validation Sets”、“Save Model” の見出しの下のコードを `main` 関数にコピーします。
1. 新しく作成された `train_model` の呼び出しを `main` 関数にコピーします。

`main` 関数は次のコードのようになります。

```python
def main():

    model_name = "sklearn_regression_model.pkl"
    alpha = 0.5
    
    X, y = load_diabetes(return_X_y=True)

    X_train, X_test, y_train, y_test = train_test_split(
        X, y, test_size=0.2, random_state=0)
    data = {"train": {"X": X_train, "y": y_train},
            "test": {"X": X_test, "y": y_test}}

    reg = train_model(data, alpha)

    joblib.dump(value=reg, filename=model_name)
```

`main` 関数が作成されたら、“Load Data”、“Split Data into Training and Validation Sets”、“Save Model” の見出しの下のすべてのコードを、新しく作成された `train_model` の呼び出しとともに、次のステートメントに置き換えます。

```python
main()
```

リファクタリング後、`experimentation/Diabetes Ridge Regression Training.ipynb` はマークダウンのない次のコードのようになります。

```python
from sklearn.datasets import load_diabetes
from sklearn.linear_model import Ridge
from sklearn.metrics import mean_squared_error
from sklearn.model_selection import train_test_split
import joblib


def train_model(data, alpha):
    reg = Ridge(alpha=alpha)
    reg.fit(data["train"]["X"], data["train"]["y"])
    preds = reg.predict(data["test"]["X"])
    print("mse", mean_squared_error(
        preds, data["test"]["y"]))
    return reg

def main():

    model_name = "sklearn_regression_model.pkl"
    alpha = 0.5
    
    X, y = load_diabetes(return_X_y=True)

    X_train, X_test, y_train, y_test = train_test_split(
        X, y, test_size=0.2, random_state=0)
    data = {"train": {"X": X_train, "y": y_train},
            "test": {"X": X_test, "y": y_test}}

    reg = train_model(data, alpha)

    joblib.dump(value=reg, filename=model_name)

main()
```

### <a name="refactor-diabetes-ridge-regression-scoring-notebook-into-functions"></a>Diabetes Ridge 回帰スコアリング ノートブックを関数にリファクタリングする
`experimentation/Diabetes Ridge Regression Scoring.ipynb` で、次の手順を実行します。

1. パラメーターを取らず、何も返さない、`init` という新しい関数を作成します。
1. “Load Model”の見出しの下にあるコードを `init` 関数にコピーします。

`init` 関数は次のコードのようになります。

```python
def init():
    model_path = Model.get_model_path(
        model_name="sklearn_regression_model.pkl")
    model = joblib.load(model_path)
```

`init` 関数が作成されたら、"Load Model" という見出しの下にあるすべてのコードを、次のように `init` の単一の呼び出しに置き換えます。

```python
init()
```

`experimentation/Diabetes Ridge Regression Scoring.ipynb` で、次の手順を実行します。

1. `run` という新しい関数を作成します。これは、パラメーターとして `raw_data` と `request_headers` を取り、次のように結果のディクショナリを返します。

    ```python
    {"result": result.tolist()}
    ```

1. “Prepare Data” および “Score Data” という見出しの下にあるコードを `run` 関数にコピーします。

    `run` 関数は次のコードのようになります (変数 `raw_data` と `request_headers` を設定するステートメントを忘れずに削除してください。これらは、後で `run` 関数が呼び出されたときに使用されます)。

    ```python
    def run(raw_data, request_headers):
        data = json.loads(raw_data)["data"]
        data = numpy.array(data)
        result = model.predict(data)

        return {"result": result.tolist()}
    ```

`run` 関数が作成されたら、“Prepare Data” および “Score Data” という見出しの下にあるすべてのコードを次のコードに置き換えます。

```python
raw_data = '{"data":[[1,2,3,4,5,6,7,8,9,10],[10,9,8,7,6,5,4,3,2,1]]}'
request_header = {}
prediction = run(raw_data, request_header)
print("Test result: ", prediction)
```
前のコードでは、変数 `raw_data` と `request_header` を設定し、`raw_data` と `request_header` で関数 `run` を呼び出し、予測を出力します。

リファクタリング後、`experimentation/Diabetes Ridge Regression Scoring.ipynb` はマークダウンのない次のコードのようになります。

```python
import json
import numpy
from azureml.core.model import Model
import joblib

def init():
    model_path = Model.get_model_path(
        model_name="sklearn_regression_model.pkl")
    model = joblib.load(model_path)

def run(raw_data, request_headers):
    data = json.loads(raw_data)["data"]
    data = numpy.array(data)
    result = model.predict(data)

    return {"result": result.tolist()}

init()
test_row = '{"data":[[1,2,3,4,5,6,7,8,9,10],[10,9,8,7,6,5,4,3,2,1]]}'
request_header = {}
prediction = run(test_row, {})
print("Test result: ", prediction)
```

## <a name="combine-related-functions-in-python-files"></a>Python ファイルで関連する関数を結合する
3 番目に、コードの再利用を容易にするために、関連する関数を Python ファイルにマージする必要があります。 このセクションでは、次のノートブック用の Python ファイルを作成します。
- Diabetes Ridge 回帰トレーニング ノートブック (`experimentation/Diabetes Ridge Regression Training.ipynb`)
- Diabetes Ridge 回帰スコアリング ノートブック (`experimentation/Diabetes Ridge Regression Scoring.ipynb`)

### <a name="create-python-file-for-the-diabetes-ridge-regression-training-notebook"></a>Diabetes Ridge 回帰トレーニング ノートブック用の Python ファイルを作成する
コマンド プロンプトで、nbconvert パッケージと `experimentation/Diabetes Ridge Regression Training.ipynb` のパスを使用する次のステートメントを実行して、ノートブックを実行可能スクリプトに変換します。

```
jupyter nbconvert -- to script "Diabetes Ridge Regression Training.ipynb" –output train
```

ノートブックが `train.py` に変換されたら、すべてのコメントを削除します。 `train.py` ファイルは次のコードのようになります。

```python
from sklearn.datasets import load_diabetes
from sklearn.linear_model import Ridge
from sklearn.metrics import mean_squared_error
from sklearn.model_selection import train_test_split
import joblib


def train_model(data, alpha):
    reg = Ridge(alpha=alpha)
    reg.fit(data["train"]["X"], data["train"]["y"])
    preds = reg.predict(data["test"]["X"])
    print("mse", mean_squared_error(
        preds, data["test"]["y"]))
    return reg

def main():
    model_name = "sklearn_regression_model.pkl"
    alpha = 0.5
    
    X, y = load_diabetes(return_X_y=True)

    X_train, X_test, y_train, y_test = train_test_split(
        X, y, test_size=0.2, random_state=0)
    data = {"train": {"X": X_train, "y": y_train},
            "test": {"X": X_test, "y": y_test}}

    reg = train_model(data, alpha)

    joblib.dump(value=reg, filename=model_name)

main()
```

MLOpsPython リポジトリの `diabetes_regression/training` ディレクトリにある `train.py` ファイルは、コマンドライン引数 (つまり、`build_id`、`model_name`、および `alpha`) をサポートしています。 動的なモデル名と `alpha` 値をサポートするために、コマンドライン引数のサポートを `train.py` ファイルに追加することはできますが、コードを正常に実行するための必須ではありません。

### <a name="create-python-file-for-the-diabetes-ridge-regression-scoring-notebook"></a>Diabetes Ridge 回帰スコアリング ノートブック用の Python ファイルを作成する
コマンド プロンプトで、nbconvert パッケージと `experimentation/Diabetes Ridge Regression Scoring.ipynb` のパスを使用する次のステートメントを実行して、ノートブックを実行可能スクリプトに変換します。

```
jupyter nbconvert -- to script "Diabetes Ridge Regression Scoring.ipynb" –output score
```

ノートブックが `score.py` に変換されたら、すべてのコメントを削除します。 `score.py` ファイルは次のコードのようになります。

```python
import json
import numpy
from azureml.core.model import Model
import joblib

def init():
    model_path = Model.get_model_path(
        model_name="sklearn_regression_model.pkl")
    model = joblib.load(model_path)

def run(raw_data, request_headers):
    data = json.loads(raw_data)["data"]
    data = numpy.array(data)
    result = model.predict(data)

    return {"result": result.tolist()}

init()
test_row = '{"data":[[1,2,3,4,5,6,7,8,9,10],[10,9,8,7,6,5,4,3,2,1]]}'
request_header = {}
prediction = run(test_row, request_header)
print("Test result: ", prediction)
```

`train_model` 関数は、スクリプト全体で参照できるようにグローバル変数モデルをインスタンス化するための変更が必要です。 `init` 関数の先頭に次のステートメントを追加します。

```python
global model
```

前のステートメントを追加すると、`init` 関数は次のコードのようになります。

```python
def init():
    global model

    # load the model from file into a global object
    model_path = Model.get_model_path(
        model_name="sklearn_regression_model.pkl")
    model = joblib.load(model_path)
```

## <a name="create-unit-tests-for-each-python-file"></a>各 Python ファイルの単体テストを作成する
4 番目に、各 Python ファイルに対して単体テストを作成する必要があります。これにより、コードがより堅牢になり、保守しやすくなります。 このセクションでは、`train.py` の関数の 1 つに対して単体テストを作成します。

`train.py` には 2 つの関数 `train_model` と `main` が含まれています。 各関数には単体テストが必要ですが、このチュートリアルでは Pytest フレームワークを使用して `train_model` 関数の単体テストを 1 つだけ作成します。  Pytest は、唯一の Python 単体テスト フレームワークではありませんが、とても一般的に使用されているものの 1 つです。 詳細については、[Pytest](https://pytest.org) をご覧ください。

単体テストには通常、次の 3 つの主要なアクションが含まれています。
- オブジェクトを配置する (必要なオブジェクトの作成と設定)
- オブジェクトを操作する
- 予期されるものに対してアサートを行う

`train_model` の一般的な条件は、`data` と `alpha` 値が渡されるタイミングです。 予期される結果は、`Ridge.train` と `Ridge.predict` 関数が呼び出されることです。 機械学習のトレーニング方法は高速に実行されないことが多いため、`Ridge.train` の呼び出しをモックします。 `Ridge.train` の戻り値はモック オブジェクトであるため、`Ridge.predict` もモックします。 モックと Pytest フレームワークを使用して `Ridge.train` と `Ridge.predict` 関数が呼び出されることが予期される結果である、`data` と `alpha` 値の受け渡しをテストする `train_model` の単体テストは、次のコードのようになります。

```python
import pytest
from code.training.train import train_model

class TestTrain:

    @staticmethod
    def test_train_model(mocker):
        # Arrange
        test_data = {"train": {"X": [[1, 2, 3]], "y": [0]},
                     "test": {"X": [[4, 5, 6]], "y": [0]}}
        test_alpha = 0.5
        mock_ridge_fit = mocker.patch('Ridge.fit')
        mock_ridge_predict = mocker.patch(Ridge.predict')

        # Act
        train_model(test_data, test_alpha)

        # Assert
        mock_ridge_fit.assert_called()
        mock_ridge_predict.assert_called()
```

## <a name="use-your-own-model-with-mlopspython-code-template"></a>MLOpsPython コード テンプレートで独自のモデルを使用する
このガイドの手順に従っている場合は、MLOpsPython リポジトリで使用可能なトレーニング/スコア/テスト スクリプトに関連する一連のスクリプトを使用できます。  上記の構造に従って、次の手順では、独自の機械学習プロジェクトでこれらのファイルを使用するために必要なことについて順を追って説明します。  

1.  ファースト ステップ ガイドに従う
2.  トレーニング コードを置き換える
3.  スコア コードを置き換える
4.  評価コードを更新する

### <a name="follow-the-getting-started-guide"></a>ファースト ステップ ガイドに従う
サポート インフラストラクチャとパイプラインで MLOpsPython を実行するには、ファースト ステップ ガイドに従う必要があります。  構造とパイプラインが適切に機能することを確認するために、独自のコードを組み込む前に MLOpsPython コードをそのままデプロイすることをお勧めします。  また、リポジトリのコード構造を理解するのにも役立ちます。

### <a name="replace-training-code"></a>トレーニング コードを置き換える
ソリューションを独自のコードで機能させるには、モデルのトレーニングに使用するコードを置き換え、対応する単体テストを削除または置換することが必要です。  具体的には、次の手順に従います。

1. `diabetes_regression\training\train.py` を置き換えます。 このスクリプトはローカルまたは Azure ML コンピューティングでモデルをトレーニングします。
1. `tests/unit/code_test.py` にあるトレーニング単体テストを削除または置換します

### <a name="replace-score-code"></a>スコア コードを置き換える
モデルでリアルタイム推論機能を提供するには、スコア コードを置き換える必要があります。 MLOpsPython テンプレートは、スコア コードを使用してモデルをデプロイし、ACI、AKS、または Web アプリでリアルタイム スコアリングを行います。  スコアリングを継続する場合は、`diabetes_regression/scoring/score.py` を置き換えます。

### <a name="update-evaluation-code"></a>評価コードを更新する
MLOpsPython テンプレートは、evaluate_model スクリプトを使用して、新しくトレーニングされたモデルと現在の運用モデルのパフォーマンスを平均二乗誤差に基づいて比較します。 新しくトレーニングされたモデルのパフォーマンスが現在の運用モデルよりも優れている場合、パイプラインは継続されます。 そうでない場合、パイプラインは停止されます。 評価を継続するには、`diabetes_regression/evaluate/evaluate_model.py` 内の `mse` のすべてのインスタンスを必要なメトリックに置き換えます。 

評価を削除するには、`.pipelines\diabetes_regression-variables` の DevOps パイプライン変数 `RUN_EVALUATION` を `false` に設定します。

## <a name="next-steps"></a>次のステップ

実験から運用コードに変換する方法を確認しました。次に、以下のリンクを使用して、Web サービスとしてデプロイされた実験の実行とモデルを監視する方法を確認してください。

> [!div class="nextstepaction"]
> [Azure ML の実験の実行とメトリックを監視する](https://docs.microsoft.com/azure/machine-learning/how-to-track-experiments)
> [ML Web サービス エンドポイントからのデータを監視および収集する](https://docs.microsoft.com/azure/machine-learning/how-to-enable-app-insights)
