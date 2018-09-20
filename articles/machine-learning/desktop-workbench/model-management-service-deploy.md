---
title: Azure Machine Learning モデル管理 Web サービスのデプロイ | Microsoft Docs
description: このドキュメントでは、Azure Machine Learning モデル管理を使って機械学習モデルをデプロイする手順について説明します。
services: machine-learning
author: aashishb
ms.author: aashishb
manager: hjerez
ms.reviewer: jasonwhowell, mldocs
ms.service: machine-learning
ms.component: core
ms.workload: data-services
ms.topic: article
ms.date: 01/03/2018
ms.openlocfilehash: f6bb6f3fbafe9b529d483af8edd55e16a35e703a
ms.sourcegitcommit: e8f443ac09eaa6ef1d56a60cd6ac7d351d9271b9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/12/2018
ms.locfileid: "35635117"
---
# <a name="deploying-a-machine-learning-model-as-a-web-service"></a>Machine Learning モデルを Web サービスとしてデプロイする

Azure Machine Learning モデル管理は、コンテナー化された Docker ベースの Web サービスとしてモデルをデプロイするインターフェイスを提供します。 Spark、Microsoft Cognitive Toolkit (CNTK)、Keras、Tensorflow、Python などのフレームワークを使って作成したモデルをデプロイできます。 

このドキュメントでは、Azure Machine Learning モデル管理のコマンド ライン インターフェイス (CLI) を使って Web サービスとしてモデルをデプロイする手順について説明します。

## <a name="what-you-need-to-get-started"></a>はじめにやるべきこと

このガイドを最大限に活用するには、モデルをデプロイできる Azure サブスクリプションまたはリソース グループに対する共同作成者アクセス権が必要です。
Azure Machine Learning Workbench および [Azure DSVM](https://docs.microsoft.com/azure/machine-learning/machine-learning-data-science-virtual-machine-overview) には CLI がプレインストールされています。  これは、スタンドアロン パッケージとしてもインストールできます。

さらに、モデル管理アカウントとデプロイメント環境が既に設定されている必要があります。  モデル管理アカウントとローカルおよびクラスター デプロイ用の環境の設定の詳細については、[モデル管理の構成](deployment-setup-configuration.md)に関するページを参照してください。

## <a name="deploying-web-services"></a>Web サービスのデプロイ
CLI を使うと、ローカル コンピューターまたはクラスター上で実行する Web サービスをデプロイできます。

ローカル デプロイで始めることをお勧めします。 モデルとコードが動作することを検証してから、運用スケールで使うためにクラスターに Web サービスをデプロイします。

デプロイの手順は次のとおりです。
1. 保存したトレーニング済みの Machine Learning モデルを使う
2. Web サービスの入力データと出力データのスキーマを作成する
3. Docker ベースのコンテナー イメージを作成する
4. Web サービスを作成してデプロイする

### <a name="1-save-your-model"></a>1.モデルを保存する
保存したトレーニング済みのモデル ファイル (mymodel.pkl など) を使って始めます。 ファイルの拡張子は、モデルのトレーニングと保存に使ったプラットフォームによって異なります。 

たとえば、Python の Pickle ライブラリを使って、トレーニング済みのモデルをファイルに保存できます。 Iris データセットを使う[例についてはこちら](http://scikit-learn.org/stable/modules/model_persistence.html)をご覧ください。

```python
import pickle
from sklearn import datasets
iris = datasets.load_iris()
X, y = iris.data, iris.target
clf = linear_model.LogisticRegression()
clf.fit(X, y)  
saved_model = pickle.dumps(clf)
```

### <a name="2-create-a-schemajson-file"></a>2.schema.json ファイルを作成する

スキーマの生成は省略可能ですが、より適切な処理のために、要求と入力変数の形式を定義することを強くお勧めします。

Web サービスの入力と出力を自動的に検証するには、スキーマを作成します。 また、CLI は、スキーマを使って Web サービスの Swagger ドキュメントを生成します。

スキーマを作成するには、次のライブラリをインポートします。

```python
from azureml.api.schema.dataTypes import DataTypes
from azureml.api.schema.sampleDefinition import SampleDefinition
from azureml.api.realtime.services import generate_schema
```
次に、Spark データフレーム、Pandas データ フレーム、NumPy 配列などの入力変数を定義します。 次の例では、Numpy 配列を使っています。

```python
inputs = {"input_array": SampleDefinition(DataTypes.NUMPY, yourinputarray)}
generate_schema(run_func=run, inputs=inputs, filepath='./outputs/service_schema.json')
```
次の例では、Spark データフレームを使っています。

```python
inputs = {"input_df": SampleDefinition(DataTypes.SPARK, yourinputdataframe)}
generate_schema(run_func=run, inputs=inputs, filepath='./outputs/service_schema.json')
```

次の例では、PANDAS データフレームを使っています。

```python
inputs = {"input_df": SampleDefinition(DataTypes.PANDAS, yourinputdataframe)}
generate_schema(run_func=run, inputs=inputs, filepath='./outputs/service_schema.json')
```

次の例では、汎用の JSON 形式を使用します。

```python
inputs = {"input_json": SampleDefinition(DataTypes.STANDARD, yourinputjson)}
generate_schema(run_func=run, inputs=inputs, filepath='./outputs/service_schema.json')
```

### <a name="3-create-a-scorepy-file"></a>手順 3.score.py ファイルを作成する
score.py ファイルを提供します。このファイルは、モデルを読み込み、モデルを使った予測の結果を返します。

このファイルには、2 つの関数 init と run が含まれる必要があります。

モデル入力と予測データの収集に役立つデータ収集機能を有効にするには、score.py ファイルの先頭に次のコードを追加します。

```python
from azureml.datacollector import ModelDataCollector
```

この機能を使う方法について詳しくは、[モデル データの収集](how-to-use-model-data-collection.md)に関するセクションをご覧ください。

#### <a name="init-function"></a>init 関数
保存されているモデルを読み込むには、init 関数を使います。

init 関数を使ってモデルを読み込む簡単な例を次に示します。

```python
def init():  
    from sklearn.externals import joblib
    global model, inputs_dc, prediction_dc
    model = joblib.load('model.pkl')

    inputs_dc = ModelDataCollector('model.pkl',identifier="inputs")
    prediction_dc = ModelDataCollector('model.pkl', identifier="prediction")
```

#### <a name="run-function"></a>run 関数
run 関数は、モデルと入力データを使って、予測を返します。

入力を処理して予測結果を返す run 関数の簡単な例を次に示します。

```python
def run(input_df):
    global clf2, inputs_dc, prediction_dc
    try:
        prediction = model.predict(input_df)
        inputs_dc.collect(input_df)
        prediction_dc.collect(prediction)
        return prediction
    except Exception as e:
        return (str(e))
```

### <a name="4-register-a-model"></a>4.モデルを登録する
上記の手順 1 で作成したモデルを登録するには、次のコマンドを使います。

```
az ml model register --model [path to model file] --name [model name]
```

### <a name="5-create-manifest"></a>5.マニフェストを作成する
次のコマンドは、モデルのマニフェストの作成に役立ちます。

```
az ml manifest create --manifest-name [your new manifest name] -f [path to score file] -r [runtime for the image, e.g. spark-py]
```
上のコマンドの `--model-id` または `-i` 引数を使って、前に登録したモデルをマニフェストに追加できます。 -i 引数を追加して、複数のモデルを指定できます。

### <a name="6-create-an-image"></a>6.イメージを作成する 
イメージを作成する前に、イメージのマニフェストを作成することができます。 

```
az ml image create -n [image name] --manifest-id [the manifest ID]
```

>[!NOTE] 
>また、1 つのコマンドを使用して、モデルの登録、マニフェストとモデルの作成を実行することもできます。 service create コマンドの詳細については、-h を使ってください。

あるいは、次のように、モデルの登録、マニフェストの作成、およびイメージの作成を 1 つの手順として実行する (ただし、まだ Web サービスの作成やデプロイは行いません) 1 つのコマンドが存在します。

```
az ml image create -n [image name] --model-file [model file or folder path] -f [code file, e.g. the score.py file] -r [the runtime e.g. spark-py which is the Docker container image base]
```

>[!NOTE]
>コマンドのパラメーターについての詳しい説明を見るには、コマンドの最後に -h を付けて入力します (例: az ml image create -h)。


### <a name="7-create-and-deploy-the-web-service"></a>7.Web サービスを作成してデプロイする
次のコマンドを使ってサービスをデプロイします。

```
az ml service create realtime --image-id <image id> -n <service name>
```

>[!NOTE] 
>また、1 つのコマンドを使用して、前の 4 つの手順をすべて実行することもできます。 service create コマンドの詳細については、-h を使ってください。

あるいは、次のように、モデルの登録、マニフェストの作成、イメージの作成や、Web サービスの作成およびデプロイを 1 つの手順として実行する 1 つのコマンドが存在します。

```azurecli
az ml service create realtime --model-file [model file/folder path] -f [scoring file e.g. score.py] -n [your service name] -s [schema file e.g. service_schema.json] -r [runtime for the Docker container e.g. spark-py or python] -c [conda dependencies file for additional python packages]
```


### <a name="8-test-the-service"></a>8.サービスをテストする
サービスを呼び出す方法についての情報を取得するには、次のコマンドを使います。

```
az ml service usage realtime -i <service id>
```

コマンド プロンプトから run 関数を使って、サービスを呼び出します。

```
az ml service run realtime -i <service id> -d "{\"input_df\": [INPUT DATA]}"
```

次の例では、サンプルの Iris Web サービスを呼び出しています。

```
az ml service run realtime -i <service id> -d "{\"input_df\": [{\"sepal length\": 3.0, \"sepal width\": 3.6, \"petal width\": 1.3, \"petal length\":0.25}]}"
```

## <a name="next-steps"></a>次の手順
Web サービスをローカルに実行してテストしたので、クラスターにデプロイして大規模に使うことができます。 Web サービスのデプロイ用にクラスターをセットアップする方法について詳しくは、「[モデル管理のセットアップ](deployment-setup-configuration.md)」をご覧ください。 
