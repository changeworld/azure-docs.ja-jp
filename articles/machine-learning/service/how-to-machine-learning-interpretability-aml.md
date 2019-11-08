---
title: ローカルおよびリモート実行のモデルの解釈可能性
titleSuffix: Azure Machine Learning
description: Azure Machine Learning SDK を使用して、モデルから得られる予測の理由を説明する方法について説明します。 これは、モデルで特徴量の重要度が決定され、予測が行われる方法を理解するために、トレーニング中と推論中に使用できます。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: mesameki
author: mesameki
ms.reviewer: trbye
ms.date: 10/25/2019
ms.openlocfilehash: a2b71a10606b7cd20f06b2497515b758426833a9
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/04/2019
ms.locfileid: "73511044"
---
# <a name="model-interpretability-for-local-and-remote-runs"></a>ローカルおよびリモート実行のモデルの解釈可能性

[!INCLUDE [applies-to-skus](../../../includes/aml-applies-to-basic-enterprise-sku.md)]

この記事では、Azure Machine Learning Python SDK の解釈可能性パッケージを使用してモデルで行われた予測の理由を説明する方法について説明します。 次のタスクについて説明します。

* ローカルとリモート コンピューティング リソースの両方でトレーニングされた機械学習モデルを解釈する
* ローカルおよびグローバルの説明を Azure の履歴に格納する
* [Azure Machine Learning Studio](https://ml.azure.com) で解釈可能性の視覚化を確認する
* 自分のモデルを使用してスコアリング Explainer をデプロイする

モデルの解釈可能性の詳細については、[概念に関する記事](how-to-machine-learning-interpretability.md)を参照してください。

## <a name="local-interpretability"></a>ローカルの解釈可能性

次の例では、Azure サービスに接続することなくローカルで解釈可能性パッケージを使用する方法について説明します。 `pip install azureml-interpret` を実行して解釈可能性パッケージを取得します。

1. ローカルの Jupyter ノートブックでサンプル モデルをトレーニングします。

    ```python
    # load breast cancer dataset, a well-known small dataset that comes with scikit-learn
    from sklearn.datasets import load_breast_cancer
    from sklearn import svm
    from sklearn.model_selection import train_test_split
    breast_cancer_data = load_breast_cancer()
    classes = breast_cancer_data.target_names.tolist()
    
    # split data into train and test
    from sklearn.model_selection import train_test_split
    x_train, x_test, y_train, y_test = train_test_split(breast_cancer_data.data,            
                                                        breast_cancer_data.target,  
                                                        test_size=0.2,
                                                        random_state=0)
    clf = svm.SVC(gamma=0.001, C=100., probability=True)
    model = clf.fit(x_train, y_train)
    ```

2. ローカルで Explainer を呼び出します。Explainer オブジェクトを初期化するには、モデルとトレーニング データの一部を Explainer のコンストラクターに渡す必要があります。 また、説明や視覚化をよりわかりやすくするために使用される機能名と出力クラス名 (分類を行う場合) をオプションで渡すこともできます。 ローカルで `TabularExplainer`、`MimicExplainer`、`PFIExplainer` を使用して Explainer オブジェクトをインスタンス化する方法を次に示します。 `TabularExplainer` は下にある 3 つの SHAP Explainer (`TreeExplainer`、`DeepExplainer`、`KernelExplainer`) のいずれかを呼び出し、ユース ケース用に最適な 1 つを自動的に選択します。 ただし、基になっている 3 つの Explainer をそれぞれ直接呼び出すことができます。

    ```python
    from interpret.ext.blackbox import TabularExplainer

    # "features" and "classes" fields are optional
    explainer = TabularExplainer(model, 
                                 x_train, 
                                 features=breast_cancer_data.feature_names, 
                                 classes=classes)
    ```

    or

    ```python

    from interpret.ext.blackbox import MimicExplainer
    
    # you can use one of the following four interpretable models as a global surrogate to the black box model
    
    from interpret.ext.glassbox import LGBMExplainableModel
    from interpret.ext.glassbox import LinearExplainableModel
    from interpret.ext.glassbox import SGDExplainableModel
    from interpret.ext.glassbox import DecisionTreeExplainableModel

    # "features" and "classes" fields are optional
    # augment_data is optional and if true, oversamples the initialization examples to improve surrogate model accuracy to fit original model.  Useful for high-dimensional data where the number of rows is less than the number of columns. 
    # max_num_of_augmentations is optional and defines max number of times we can increase the input data size.
    # LGBMExplainableModel can be replaced with LinearExplainableModel, SGDExplainableModel, or DecisionTreeExplainableModel
    explainer = MimicExplainer(model, 
                               x_train, 
                               LGBMExplainableModel, 
                               augment_data=True, 
                               max_num_of_augmentations=10, 
                               features=breast_cancer_data.feature_names, 
                               classes=classes)
    ```
   or

    ```python
    from interpret.ext.blackbox import PFIExplainer 
    
    # "features" and "classes" fields are optional
    explainer = PFIExplainer(model, 
                             features=breast_cancer_data.feature_names, 
                             classes=classes)
    ```

### <a name="overall-global-feature-importance-values"></a>全体的 (グローバル) な特徴量の重要度の値

グローバルな特徴の重要度の値を取得します。
    
```python

# you can use the training data or the test data here
global_explanation = explainer.explain_global(x_train)

# if you used the PFIExplainer in the previous step, use the next line of code instead
# global_explanation = explainer.explain_global(x_train, true_labels=y_test)

# sorted feature importance values and feature names
sorted_global_importance_values = global_explanation.get_ranked_global_values()
sorted_global_importance_names = global_explanation.get_ranked_global_names()
dict(zip(sorted_global_importance_names, sorted_global_importance_values))

# alternatively, you can print out a dictionary that holds the top K feature names and values
global_explanation.get_feature_importance_dict()
```

### <a name="instance-level-local-feature-importance-values"></a>インスタンスレベルにおける (ローカルな) 特徴量の重要度の値

ローカルな特徴の重要度の値を取得する: 次の関数呼び出しを使用して、個々のインスタンスまたはインスタンスのグループを説明します。 PFIExplainer はローカルな説明をサポートしていないことに注意してください。

```python
# explain the first data point in the test set
local_explanation = explainer.explain_local(x_test[0:5])

# sorted feature importance values and feature names
sorted_local_importance_names = local_explanation.get_ranked_local_names()
sorted_local_importance_values = local_explanation.get_ranked_local_values()
```

## <a name="interpretability-for-remote-runs"></a>リモート実行の解釈可能性

この例では、`ExplanationClient` クラスを使用してリモート実行のモデルの解釈可能性を有効にする方法について説明します。 コンセプトは前のセクションと似ていますが、リモート実行では `ExplanationClient` を使用して解釈可能性のコンテキストをアップロードできます。このコンテキストは後からローカル環境でダウンロードできます。 `pip install azureml-contrib-interpret` を使用して必要なパッケージを取得します。

1. ローカルの Jupyter ノートブックでトレーニング スクリプト (train_explain.py など) を作成します。

    ```python
    from azureml.contrib.interpret.explanation.explanation_client import ExplanationClient
    from azureml.core.run import Run
    from interpret.ext.blackbox import TabularExplainer

    run = Run.get_context()
    client = ExplanationClient.from_run(run)

    # write code to get and split your data into train and test sets here
    # write code to train your model here 

    # explain predictions on your local machine
    # "features" and "classes" fields are optional
    explainer = TabularExplainer(model, 
                                 x_train, 
                                 features=feature_names, 
                                 classes=classes)

    # explain overall model predictions (global explanation)
    global_explanation = explainer.explain_global(x_test)
    
    # uploading global model explanation data for storage or visualization in webUX
    # the explanation can then be downloaded on any compute
    # multiple explanations can be uploaded
    client.upload_model_explanation(global_explanation, comment='global explanation: all features')
    # or you can only upload the explanation object with the top k feature info
    #client.upload_model_explanation(global_explanation, top_k=2, comment='global explanation: Only top 2 features')
    ```

1. Azure Machine Learning コンピューティングをコンピューティング先として設定し、トレーニングの実行を送信する方法について学習するには、[モデル トレーニング用のコンピューティング ターゲットの設定](how-to-set-up-training-targets.md#amlcompute)に関する手順に従ってください。 [サンプル ノートブック](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/explain-model/azure-integration/remote-explanation)も参照できます。

1. ローカル環境の Jupyter Notebook で説明をダウンロードします。

    ```python
    from azureml.contrib.interpret.explanation.explanation_client import ExplanationClient
    
    client = ExplanationClient.from_run(run)
    
    # get model explanation data
    explanation = client.download_model_explanation()
    # or only get the top k (e.g., 4) most important features with their importance values
    explanation = client.download_model_explanation(top_k=4)
    
    global_importance_values = explanation.get_ranked_global_values()
    global_importance_names = explanation.get_ranked_global_names()
    print('global importance values: {}'.format(global_importance_values))
    print('global importance names: {}'.format(global_importance_names))
    ```

## <a name="raw-feature-transformations"></a>未加工の特徴の変換

必要に応じて、(train_explain.py で) 特徴変換パイプラインを Explainer に渡して、(エンジニアリングされた特徴ではなく) 変換前の未加工の特徴における説明を受け取ることができます。 これを省略した場合、Explainer はエンジニアリングされた特徴における説明を提供します。

サポートされている変換の形式は、[sklearn-pandas](https://github.com/scikit-learn-contrib/sklearn-pandas) に記載されているものと同じです。 一般に、どのような変換も、単一の列で動作し、したがって明らかに 1 対多である限りサポートされます。 

生の特徴は、`sklearn.compose.ColumnTransformer` または適合するトランスフォーマー タプルの一覧を使って説明します。 下のコードでは、`sklearn.compose.ColumnTransformer` が使用されています。 


```python
from sklearn.compose import ColumnTransformer

numeric_transformer = Pipeline(steps=[
    ('imputer', SimpleImputer(strategy='median')),
    ('scaler', StandardScaler())])

categorical_transformer = Pipeline(steps=[
    ('imputer', SimpleImputer(strategy='constant', fill_value='missing')),
    ('onehot', OneHotEncoder(handle_unknown='ignore'))])

preprocessor = ColumnTransformer(
    transformers=[
        ('num', numeric_transformer, numeric_features),
        ('cat', categorical_transformer, categorical_features)])

# append classifier to preprocessing pipeline.
# now we have a full prediction pipeline.
clf = Pipeline(steps=[('preprocessor', preprocessor),
                      ('classifier', LogisticRegression(solver='lbfgs'))])


# clf.steps[-1][1] returns the trained classification model
# pass transformation as an input to create the explanation object
# "features" and "classes" fields are optional
tabular_explainer = TabularExplainer(clf.steps[-1][1],
                                     initialization_examples=x_train,
                                     features=dataset_feature_names,
                                     classes=dataset_classes,
                                     transformations=preprocessor)
```

適合したトランスフォーマー タプルの一覧を使用して例を実行したい場合は、次のコードを使用します。

```python
from sklearn.pipeline import Pipeline
from sklearn.impute import SimpleImputer
from sklearn.preprocessing import StandardScaler, OneHotEncoder
from sklearn.linear_model import LogisticRegression
from sklearn_pandas import DataFrameMapper

# assume that we have created two arrays, numerical and categorical, which holds the numerical and categorical feature names

numeric_transformations = [([f], Pipeline(steps=[('imputer', SimpleImputer(
    strategy='median')), ('scaler', StandardScaler())])) for f in numerical]

categorical_transformations = [([f], OneHotEncoder(
    handle_unknown='ignore', sparse=False)) for f in categorical]

transformations = numeric_transformations + categorical_transformations

# append model to preprocessing pipeline.
# now we have a full prediction pipeline.
clf = Pipeline(steps=[('preprocessor', DataFrameMapper(transformations)),
                      ('classifier', LogisticRegression(solver='lbfgs'))])

# clf.steps[-1][1] returns the trained classification model
# pass transformation as an input to create the explanation object
# "features" and "classes" fields are optional
tabular_explainer = TabularExplainer(clf.steps[-1][1],
                                     initialization_examples=x_train,
                                     features=dataset_feature_names,
                                     classes=dataset_classes,
                                     transformations=transformations)
```

## <a name="visualizations"></a>視覚化

ローカルの Jupyter ノートブックに説明をダウンロードしたら、自分のモデルを理解して解釈するために視覚化ダッシュボードを使用できます。

### <a name="global-visualizations"></a>グローバルな視覚化

次のプロットは、トレーニング済みモデルの全体像を、その予測および説明とともに示しています。

|プロット|説明|
|----|-----------|
|データ探索| データセットの概要と予測値。|
|グローバルな重要度|グローバルに上位 K 個 (構成可能な K) の重要な特徴を示します。 このグラフは、基になるモデルのグローバルな動作を理解するのに役立ちます。|
|説明の探索|ある特徴が、モデルの予測値 (または予測値の確率) の変更にいかに関与しているかを示します。 また、2 つの特徴がどのように相互作用して予測に影響しているかも示します。|
|重要度の概要| すべてのデータ ポイントにわたって、符号付きローカル特徴の重要度の値を使用して、各特徴が予測値に与える影響の分布を表示します。|

[![視覚化ダッシュ ボード グローバル](./media/machine-learning-interpretability-explainability/global-charts.png)](./media/machine-learning-interpretability-explainability/global-charts.png#lightbox)

### <a name="local-visualizations"></a>ローカルな視覚化

任意の時点で前のプロットの個々のデータ ポイントをクリックして、特定のデータ ポイントに対するローカル特徴量の重要度プロットを読み込みます。

|プロット|説明|
|----|-----------|
|ローカルな重要度|グローバルに上位 K 個 (構成可能な K) の重要な特徴を示します。 このグラフは、特定のデータ ポイントについての基になるモデルのローカルな動作を理解するのに役立ちます。|
|補正の探索|選択したデータ ポイントの特徴値を変更し、それらの変更が予測値にどのように影響するかを観察できます。|
|個別条件付き期待値 (ICE)| 特徴値を最小値から最大値に変更して、特徴が変化したときにデータ ポイントの予測がどのように変化するかを確認できます。|

[![視覚化ダッシュボードのローカルな特徴の重要度](./media/machine-learning-interpretability-explainability/local-charts.png)](./media/machine-learning-interpretability-explainability/local-charts.png#lightbox)


[![視覚化ダッシュボードの特徴の補正](./media/machine-learning-interpretability-explainability/perturbation.gif)](./media/machine-learning-interpretability-explainability/perturbation.gif#lightbox)


[![視覚化ダッシュボードの ICE プロット](./media/machine-learning-interpretability-explainability/ice-plot.png)](./media/machine-learning-interpretability-explainability/ice-plot.png#lightbox)

Jupyter カーネルを起動する前に、視覚化ダッシュボードのウィジェット拡張機能を有効にする必要があります。

* Jupyter Notebook

    ```shell
    jupyter nbextension install --py --sys-prefix azureml.contrib.interpret.visualize
    jupyter nbextension enable --py --sys-prefix azureml.contrib.interpret.visualize
    ```



* Jupyter のラボ

    ```shell
    jupyter labextension install @jupyter-widgets/jupyterlab-manager
    jupyter labextension install microsoft-mli-widget
    ```

視覚化ダッシュボードを読み込むには、次のコードを使用します。

```python
from azureml.contrib.interpret.visualize import ExplanationDashboard

ExplanationDashboard(global_explanation, model, x_test)
```

### <a name="visualization-in-azure-machine-learning-studio"></a>Azure Machine Learning Studio での視覚化

[リモートの解釈可能性](how-to-machine-learning-interpretability-aml.md#interpretability-for-remote-runs)に関するセクションの手順を完了すると、[Azure Machine Learning Studio](https://ml.azure.com) で視覚化ダッシュボードを確認できます。 Azure Machine Learning Studio に表示されるダッシュボードは、上述の視覚化ダッシュボードの簡易バージョンであり、サポートされるのは次の 2 つのタブのみです。

|プロット|説明|
|----|-----------|
|グローバルな重要度|グローバルに上位 K 個 (構成可能な K) の重要な特徴を示します。 このグラフは、基になるモデルのグローバルな動作を理解するのに役立ちます。|
|重要度の概要| すべてのデータ ポイントにわたって、符号付きローカル特徴の重要度の値を使用して、各特徴が予測値に与える影響の分布を表示します。|

グローバルとローカルの両方の説明が使用できる場合は、両方のタブでデータが設定されます。 使用できるのがグローバルの説明のみの場合、2 つ目のタブは無効になります。

Azure Machine Learning Studio で視覚化ダッシュボードにアクセスするには、次のいずれかの方法を使用できます。

1. [実験] タブ (プレビュー): [実験] タブをクリックすると、Azure Machine Learning service で実行した実験の一覧が表示されます。 そのリストから特定の実験を選択すると、選択した実験の名前の下にすべての実行が表示されたページにリダイレクトされます。 各実行とその [説明] タブをクリックすると、説明の視覚化ダッシュボードが表示されます。


[![視覚化ダッシュボードのローカルな特徴の重要度](./media/machine-learning-interpretability-explainability/amlstudio-experiments.png)](./media/machine-learning-interpretability-explainability/amlstudio-experiments.png#lightbox)


2. [モデル] タブ: 「[Azure Machine Learning を使用してモデルをデプロイする](https://docs.microsoft.com/azure/machine-learning/service/how-to-deploy-and-where)」の手順を使用して独自のモデルを登録した場合は、ご自分のモデルが [モデル] タブの一覧に表示されます。各モデルとその [説明] タブをクリックすると、説明の視覚化ダッシュボードが表示されます。

## <a name="interpretability-at-inference-time"></a>推論時の解釈可能性

Explainer は独自のモデルと共にデプロイでき、ローカルの説明情報を提供するために推論時に使用できます。 推論時の解釈可能性のパフォーマンスを高めるために、より軽量のスコアリング Explainer も用意されています。 軽量のスコアリング Explainer をデプロイするプロセスはモデルのデプロイと同様で、次の手順が含まれます。


1. 説明オブジェクトを作成します (たとえば TabularExplainer を使用します)。

   ```python
    from interpret.ext.blackbox import TabularExplainer


   explainer = TabularExplainer(model, 
                                initialization_examples=x_train, 
                                features=dataset_feature_names, 
                                classes=dataset_classes, 
                                transformations=transformations)
   ```

1. 説明オブジェクトを使用してスコアリング Explainer を作成します。

   ```python
   from azureml.contrib.interpret.scoring.scoring_explainer import KernelScoringExplainer, save

   # create a lightweight explainer at scoring time
   scoring_explainer = KernelScoringExplainer(explainer)

   # pickle scoring explainer
   # pickle scoring explainer locally
   OUTPUT_DIR = 'my_directory'
   save(scoring_explainer, directory=OUTPUT_DIR, exist_ok=True)
   ```

1. スコアリング Explainer モデルを使用するイメージを構成して登録します。

   ```python
   # register explainer model using the path from ScoringExplainer.save - could be done on remote compute
   # scoring_explainer.pkl is the filename on disk, while my_scoring_explainer.pkl will be the filename in cloud storage
   run.upload_file('my_scoring_explainer.pkl', os.path.join(OUTPUT_DIR, 'scoring_explainer.pkl'))
   
   scoring_explainer_model = run.register_model(model_name='my_scoring_explainer', 
                                                model_path='my_scoring_explainer.pkl')
   print(scoring_explainer_model.name, scoring_explainer_model.id, scoring_explainer_model.version, sep = '\t')
   ```

1. [省略可能] スコアリング Explainer をクラウドから取得し、説明をテストします

   ```python
   from azureml.contrib.interpret.scoring.scoring_explainer import load

   # retrieve the scoring explainer model from cloud"
   scoring_explainer_model = Model(ws, 'my_scoring_explainer')
   scoring_explainer_model_path = scoring_explainer_model.download(target_dir=os.getcwd(), exist_ok=True)

   # load scoring explainer from disk
   scoring_explainer = load(scoring_explainer_model_path)

   # test scoring explainer locally
   preds = scoring_explainer.explain(x_test)
   print(preds)
   ```

1. イメージをコンピューティング先にデプロイします。

   1. スコアリング ファイルを作成します (この手順の前に、「[Azure Machine Learning を使用してモデルをデプロイする](https://docs.microsoft.com/azure/machine-learning/service/how-to-deploy-and-where)」の手順を実行して元の予測モデルを登録します)。

        ```python
        %%writefile score.py
        import json
        import numpy as np
        import pandas as pd
        import os
        import pickle
        from sklearn.externals import joblib
        from sklearn.linear_model import LogisticRegression
        from azureml.core.model import Model

        def init():

            global original_model
            global scoring_model

            # retrieve the path to the model file using the model name
            # assume original model is named original_prediction_model
            original_model_path = Model.get_model_path('original_prediction_model')
            scoring_explainer_path = Model.get_model_path('my_scoring_explainer')

            original_model = joblib.load(original_model_path)
            scoring_explainer = joblib.load(scoring_explainer_path)

        def run(raw_data):
            # get predictions and explanations for each data point
            data = pd.read_json(raw_data)
            # make prediction
            predictions = original_model.predict(data)
            # retrieve model explanations
            local_importance_values = scoring_explainer.explain(data)
            # you can return any data type as long as it is JSON-serializable
            return {'predictions': predictions.tolist(), 'local_importance_values': local_importance_values}
        ```

   1. デプロイ構成を定義します (この構成は、モデルの要件によって異なります。 次の例では、1 つの CPU コアと 1 GB のメモリが使用される構成を定義しています)

        ```python
        from azureml.core.webservice import AciWebservice

        aciconfig = AciWebservice.deploy_configuration(cpu_cores=1,
                                                       memory_gb=1,
                                                       tags={"data": "NAME_OF_THE_DATASET",
                                                             "method" : "local_explanation"},
                                                       description='Get local explanations for NAME_OF_THE_PROBLEM')
        ```

   1. 環境の依存関係を持つファイルを作成します。

        ```python
        from azureml.core.conda_dependencies import CondaDependencies

        # WARNING: to install this, g++ needs to be available on the Docker image and is not by default (look at the next cell)

        azureml_pip_packages = ['azureml-defaults', 'azureml-contrib-interpret', 'azureml-core', 'azureml-telemetry', 'azureml-interpret']
 

        # specify CondaDependencies obj
        myenv = CondaDependencies.create(conda_packages=['scikit-learn', 'pandas'],
                                         pip_packages=['sklearn-pandas'] + azureml_pip_packages,
                                         pin_sdk_version=False)


        with open("myenv.yml","w") as f:
            f.write(myenv.serialize_to_string())

        with open("myenv.yml","r") as f:
            print(f.read())
        ```

   1. g++ がインストールされているカスタム Dockerfile を作成します

        ```python
        %%writefile dockerfile
        RUN apt-get update && apt-get install -y g++
        ```

   1. 作成済みのイメージをデプロイします (推定所要時間:5 分)

        ```python
        from azureml.core.webservice import Webservice
        from azureml.core.image import ContainerImage

        # use the custom scoring, docker, and conda files we created above
        image_config = ContainerImage.image_configuration(execution_script="score.py",
                                                        docker_file="dockerfile",
                                                        runtime="python",
                                                        conda_file="myenv.yml")

        # use configs and models generated above
        service = Webservice.deploy_from_model(workspace=ws,
                                            name='model-scoring-service',
                                            deployment_config=aciconfig,
                                            models=[scoring_explainer_model, original_model],
                                            image_config=image_config)

        service.wait_for_deployment(show_output=True)
        ```

1. 展開をテスト

    ```python
    import requests

    # create data to test service with
    examples = x_list[:4]
    input_data = examples.to_json()

    headers = {'Content-Type':'application/json'}

    # send request to service
    resp = requests.post(service.scoring_uri, input_data, headers=headers)

    print("POST to url", service.scoring_uri)
    # can covert back to Python objects from json string if desired
    print("prediction:", resp.text)
    ```

1. クリーンアップ:デプロイされた Web サービスを削除するには、`service.delete()` を使用します。

## <a name="next-steps"></a>次の手順

モデルの解釈可能性の詳細については、[概念に関する記事](how-to-machine-learning-interpretability.md)を参照してください。
