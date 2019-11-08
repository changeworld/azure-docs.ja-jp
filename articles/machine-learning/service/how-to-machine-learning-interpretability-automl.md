---
title: 自動 ML におけるモデルの解釈可能性
titleSuffix: Azure Machine Learning
description: Azure Machine Learning SDK を使用して、自動 ML モデルによる予測の理由を説明する方法について説明します。 これは、モデルで特徴量の重要度が決定され、予測が行われる方法を理解するために、トレーニング中と推論中に使用できます。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: mesameki
author: mesameki
ms.reviewer: trbye
ms.date: 10/25/2019
ms.openlocfilehash: 2c9df55eb319dd45281eca4684c79d83dc6ef933
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/04/2019
ms.locfileid: "73511052"
---
# <a name="model-interpretability-for-automated-ml-models"></a>自動 ML モデルの解釈可能性

[!INCLUDE [applies-to-skus](../../../includes/aml-applies-to-basic-enterprise-sku.md)]

このハウツー記事では、Azure Machine Learning service を使用して、自動機械学習で解釈可能性機能を有効にする方法について説明します。 自動 ML を使用すると、生の特徴量の重要度とエンジニアリングされた特徴量の重要度の両方を理解できます。 モデルの解釈可能性を使用するには、`AutoMLConfig` オブジェクトで `model_explainability=True` を設定します。  

この記事では、次のタスクについて説明します。

* 最良のモデルまたは任意のモデルのトレーニング中の解釈可能性
* 視覚化によるデータと説明のパターンの検出の支援
* 推論中またはスコアリング中の解釈可能性

## <a name="prerequisites"></a>前提条件

* `pip install azureml-interpret azureml-contrib-interpret` を実行して、解釈可能性機能に必要なパッケージを取得します。
* この記事は、自動 ML の実験の作成に関する知識があることを前提としています。 SDK で自動 ML を使用する方法については、[チュートリアル](tutorial-auto-train-models.md)または[ハウツー記事](how-to-configure-auto-train.md)をご覧ください。
 
## <a name="interpretability-during-training-for-the-best-model"></a>最良のモデルのトレーニング中の解釈可能性 

`best_run` から説明を取得します。これには、エンジニアリングされた特徴と生の特徴の説明が含まれます。 

### <a name="download-engineered-feature-importance-from-artifact-store"></a>成果物ストアからエンジニアリングされた特徴量の重要度をダウンロードする

`ExplanationClient` を使用して、best_run の成果物ストアからエンジニアリングされた特徴の説明をダウンロードできます。 生の特徴セットの説明を取得するには、`raw=True` を設定します。 

```python
from azureml.contrib.interpret.explanation.explanation_client import ExplanationClient

client = ExplanationClient.from_run(best_run)
engineered_explanations = client.download_model_explanation(raw=False)
print(engineered_explanations.get_feature_importance_dict())
```

## <a name="interpretability-during-training-for-any-model"></a>任意のモデルのトレーニング中の解釈可能性 

このセクションでは、モデル説明を計算し、説明を視覚化する方法について説明します。 自動 ML モデルの既存のモデル説明を取得する以外に、さまざまなテスト データを使用してモデルを説明することもできます。 以下の手順では、テスト データに基づいて、エンジニアリングされた特徴量の重要度と生の特徴量の重要度を計算し、視覚化できます。

### <a name="retrieve-any-other-automl-model-from-training"></a>トレーニングから他の AutoML モデルを取得する

```python
automl_run, fitted_model = local_run.get_output(metric='r2_score')
```

### <a name="setup-the-model-explanations"></a>モデル説明を設定する

fitted_model では次の項目を生成できます。これらは、automl_setup_model_explanations を使用して、エンジニアリングされた特徴と生の特徴の説明を取得する際に使用されます。

* トレーニング サンプル/テスト サンプルの特徴付けされたデータ
* エンジニアリングされた特徴と生の特徴の名前のリストを収集する
* 分類のシナリオにおいてラベル付きの列でクラスを見つける

automl_explainer_setup_obj には、上記のすべての構造が含まれます。

```python
from azureml.train.automl.automl_explain_utilities import AutoMLExplainerSetupClass, automl_setup_model_explanations

automl_explainer_setup_obj = automl_setup_model_explanations(fitted_model, X=X_train, 
                                                             X_test=X_test, y=y_train, 
                                                             task='classification')
```
### <a name="initialize-the-mimic-explainer-for-feature-importance"></a>特徴量の重要度のための Mimic Explainer を初期化する

AutoML モデルを説明するには、`MimicWrapper` クラスを使用します。 MimicWrapper は、explainer セットアップ オブジェクト、ワークスペース、自動 ML モデル (ここでは fitted_model) を説明するためのサロゲート モデルとして機能する LightGBM モデルのパラメーターで初期化できます。 また、MimicWrapper は、生の説明とエンジニアリングされた説明のアップロード先となる automl_run オブジェクトを受け取ります。

```python
from azureml.interpret.mimic.models.lightgbm_model import LGBMExplainableModel
from azureml.interpret.mimic_wrapper import MimicWrapper

explainer = MimicWrapper(ws, automl_explainer_setup_obj.automl_estimator, LGBMExplainableModel, 
                         init_dataset=automl_explainer_setup_obj.X_transform, run=automl_run,
                         features=automl_explainer_setup_obj.engineered_feature_names, 
                         feature_maps=[automl_explainer_setup_obj.feature_map],
                         classes=automl_explainer_setup_obj.classes)
```

### <a name="use-mimicexplainer-for-computing-and-visualizing-engineered-feature-importance"></a>MimicExplainer を使用してエンジニアリングされた特徴量の重要度を計算および視覚化する

MimicWrapper の explain() メソッドを変換されたテスト サンプルで呼び出して、生成済みのエンジニアリングされた特徴の特徴量の重要度を取得できます。 また、ExplanationDashboard を使用して、自動 ML フィーチャライザーによって生成された、エンジニアリングされた特徴の特徴量の重要度の値をダッシュボードに視覚化して表示することもできます。

```python
from azureml.contrib.interpret.visualize import ExplanationDashboard
engineered_explanations = explainer.explain(['local', 'global'],              
                                            eval_dataset=automl_explainer_setup_obj.X_test_transform)

print(engineered_explanations.get_feature_importance_dict())
ExplanationDashboard(engineered_explanations, automl_explainer_setup_obj.automl_estimator, automl_explainer_setup_obj.X_test_transform)
```
### <a name="use-mimic-explainer-for-computing-and-visualizing-raw-feature-importance"></a>Mimic Explainer を使用して生の特徴量の重要度を計算および視覚化する

MimicWrapper の explain() メソッドを変換されたテスト サンプルでもう一度呼び出し、`get_raw` を True に設定すると、生の特徴の特徴量の重要度を取得できます。 また、ExplanationDashboard を使用して、生の特徴の特徴量の重要度の値をダッシュボードに視覚化して表示することもできます。

```python
from azureml.contrib.interpret.visualize import ExplanationDashboard

raw_explanations = explainer.explain(['local', 'global'], get_raw=True, 
                                     raw_feature_names=automl_explainer_setup_obj.raw_feature_names,
                                     eval_dataset=automl_explainer_setup_obj.X_test_transform)

print(raw_explanations.get_feature_importance_dict())
ExplanationDashboard(raw_explanations, automl_explainer_setup_obj.automl_pipeline, automl_explainer_setup_obj.X_test_raw)
```

### <a name="interpretability-during-inference"></a>推論中の解釈可能性

このセクションでは、前のセクションで説明の計算に使用された Explainer を使用して、自動 ML モデルを運用化する方法について説明します。

### <a name="register-the-model-and-the-scoring-explainer"></a>モデルとスコアリング Explainer を登録する

`TreeScoringExplainer` を使用して、スコアリング Explainer を作成します。これは、推論時に生およびエンジニアリングされた特徴量の重要度の値の計算に使用されます。 以前に計算された feature_map でスコアリング Explainer を初期化します。 feature_map は、スコアリング Explainer が生の特徴量の重要度を返すために使用されます。

次のコードでは、スコアリング Explainer を保存し、モデルとスコアリング Explainer をモデル管理サービスに登録します。

```python
from azureml.interpret.scoring.scoring_explainer import TreeScoringExplainer, save

# Initialize the ScoringExplainer
scoring_explainer = TreeScoringExplainer(explainer.explainer, feature_maps=[automl_explainer_setup_obj.feature_map])

# Pickle scoring explainer locally
save(scoring_explainer, exist_ok=True)

# Register trained automl model present in the 'outputs' folder in the artifacts
original_model = automl_run.register_model(model_name='automl_model', 
                                           model_path='outputs/model.pkl')

# Register scoring explainer
automl_run.upload_file('scoring_explainer.pkl', 'scoring_explainer.pkl')
scoring_explainer_model = automl_run.register_model(model_name='scoring_explainer', model_path='scoring_explainer.pkl')
```

### <a name="create-the-conda-dependencies-for-setting-up-the-service"></a>サービスを設定するための conda の依存関係を作成する

次に、デプロイされたモデルのコンテナーに必要な環境の依存関係を作成します。

```python
from azureml.core.conda_dependencies import CondaDependencies 

azureml_pip_packages = [
    'azureml-interpret', 'azureml-train-automl', 'azureml-defaults'
]

myenv = CondaDependencies.create(conda_packages=['scikit-learn', 'pandas', 'numpy', 'py-xgboost<=0.80'],
                                 pip_packages=azureml_pip_packages,
                                 pin_sdk_version=True)

with open("myenv.yml","w") as f:
    f.write(myenv.serialize_to_string())

with open("myenv.yml","r") as f:
    print(f.read())

```

### <a name="deploy-the-service"></a>サービスをデプロイする

前の手順の conda ファイルとスコアリング ファイルを使用してサービスをデプロイします。

```python
from azureml.core.webservice import Webservice
from azureml.core.model import InferenceConfig
from azureml.core.webservice import AciWebservice
from azureml.core.model import Model

aciconfig = AciWebservice.deploy_configuration(cpu_cores=1, 
                                               memory_gb=1, 
                                               tags={"data": "Bank Marketing",  
                                                     "method" : "local_explanation"}, 
                                               description='Get local explanations for Bank marketing test data')

inference_config = InferenceConfig(runtime= "python", 
                                   entry_script="score_local_explain.py",
                                   conda_file="myenv.yml")

# Use configs and models generated above
service = Model.deploy(ws, 'model-scoring', [scoring_explainer_model, original_model], inference_config, aciconfig)
service.wait_for_deployment(show_output=True)
```

### <a name="inference-using-test-data"></a>テスト データを使用して推論を行う

テスト データを使用して推論を行い、自動 ML モデルからの予測値を確認し、予測値のエンジニアリングされた特徴量の重要度と生の特徴量の重要度を表示します。

```python
if service.state == 'Healthy':
    # Serialize the first row of the test data into json
    X_test_json = X_test[:1].to_json(orient='records')
    print(X_test_json)
    # Call the service to get the predictions and the engineered and raw explanations
    output = service.run(X_test_json)
    # Print the predicted value
    print(output['predictions'])
    # Print the engineered feature importances for the predicted value
    print(output['engineered_local_importance_values'])
    # Print the raw feature importances for the predicted value
    print(output['raw_local_importance_values'])
```

### <a name="visualizations-to-aid-you-in-the-discovery-of-patterns-in-data-and-explanations-at-training-time"></a>視覚化によってトレーニング時にデータと説明のパターンの検出を支援する

[Azure Machine Learning Studio](https://ml.azure.com) のワークスペースで、特徴量の重要度のグラフを表示することもできます。 自動 ML の実行が完了したら、[View model details]\(モデルの詳細の表示\) をクリックする必要があります。これにより、特定の実行に移動できます。 ここから、[説明] タブをクリックして、説明の視覚化ダッシュボードを表示します。 

[![機械学習解釈可能性のアーキテクチャ](./media/machine-learning-interpretability-explainability/automl-explainability.png)](./media/machine-learning-interpretability-explainability/automl-explainability.png#lightbox)

## <a name="next-steps"></a>次の手順

自動機械学習外の SDK の他の領域で、モデルの説明と特徴の重要度を有効にする方法については、解釈可能性の[概念](how-to-machine-learning-interpretability.md)に関する記事を参照してください。