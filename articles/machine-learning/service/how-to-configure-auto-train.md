---
title: 自動 ML の実験を作成する
titleSuffix: Azure Machine Learning
description: 自動機械学習は、アルゴリズムを自動的に選択して、デプロイできる状態のモデルを生成します。 自動機械学習の実験の構成に使用できるオプションについて説明します。
author: cartacioS
ms.author: sacartac
ms.reviewer: sgilley
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.date: 11/04/2019
ms.custom: seodec18
ms.openlocfilehash: ddcf97a8c7ae6000c14638a8292bc8b4f39ed87d
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/10/2019
ms.locfileid: "74978341"
---
# <a name="configure-automated-ml-experiments-in-python"></a>Python で自動 ML の実験を構成する
[!INCLUDE [applies-to-skus](../../../includes/aml-applies-to-basic-enterprise-sku.md)]

このガイドでは、[Azure Machine Learning SDK](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py) を使用して、自動機械学習の実験のさまざまな構成設定を定義する方法について説明します。 自動機械学習は、アルゴリズムとハイパーパラメーターを自動的に選択して、デプロイできる状態のモデルを生成します。 自動機械学習の実験の構成に使用できるオプションはいくつかあります。

自動機械学習の実験の例を確認するには、[自動機械学習を使用した分類モデルのトレーニングのチュートリアル](tutorial-auto-train-models.md)に関するページか、「[クラウドで自動機械学習を使用してモデルをトレーニングする](how-to-auto-train-remote.md)」をご覧ください。

自動機械学習で使用できる構成オプション:

* 実験の種類を選択する:分類、回帰、または時系列予測
* データ ソース、形式、およびデータのフェッチ
* コンピューティング先を選択する: ローカルまたはリモート
* 自動機械学習の実験の設定
* 自動機械学習の実験を実行する
* モデル メトリックを探索する
* モデルを登録して展開する

コードを使用しないエクスペリエンスを希望する場合は、[Azure Machine Learning Studio で自動化された機械学習の実験を作成する](how-to-create-portal-experiments.md)こともできます。

## <a name="select-your-experiment-type"></a>実験の種類を選択する

実験を始める前に、解決する機械学習の問題の種類を決める必要があります。 自動機械学習でサポートされるタスクの種類は、分類、回帰、予測です。 [タスクの種類](how-to-define-task-type.md)についての詳細情報を参照してください。

自動機械学習では、自動化とチューニングのプロセス中に次のアルゴリズムがサポートされています。 ユーザーは、アルゴリズムを指定する必要はありません。

分類 | 回帰 | 時系列予測
|-- |-- |--
[ロジスティック回帰](https://scikit-learn.org/stable/modules/linear_model.html#logistic-regression)| [Elastic Net](https://scikit-learn.org/stable/modules/linear_model.html#elastic-net)| [Elastic Net](https://scikit-learn.org/stable/modules/linear_model.html#elastic-net)
[Light GBM](https://lightgbm.readthedocs.io/en/latest/index.html)|[Light GBM](https://lightgbm.readthedocs.io/en/latest/index.html)|[Light GBM](https://lightgbm.readthedocs.io/en/latest/index.html)
[勾配ブースティング](https://scikit-learn.org/stable/modules/ensemble.html#classification)|[勾配ブースティング](https://scikit-learn.org/stable/modules/ensemble.html#regression)|[勾配ブースティング](https://scikit-learn.org/stable/modules/ensemble.html#regression)
[デシジョン ツリー](https://scikit-learn.org/stable/modules/tree.html#decision-trees)|[デシジョン ツリー](https://scikit-learn.org/stable/modules/tree.html#regression)|[デシジョン ツリー](https://scikit-learn.org/stable/modules/tree.html#regression)
[K ニアレスト ネイバー](https://scikit-learn.org/stable/modules/neighbors.html#nearest-neighbors-regression)|[K ニアレスト ネイバー](https://scikit-learn.org/stable/modules/neighbors.html#nearest-neighbors-regression)|[K ニアレスト ネイバー](https://scikit-learn.org/stable/modules/neighbors.html#nearest-neighbors-regression)
[Linear SVC](https://scikit-learn.org/stable/modules/svm.html#classification)|[LARS Lasso](https://scikit-learn.org/stable/modules/linear_model.html#lars-lasso)|[LARS Lasso](https://scikit-learn.org/stable/modules/linear_model.html#lars-lasso)
[サポート ベクター分類 (SVC)](https://scikit-learn.org/stable/modules/svm.html#classification)|[確率的勾配降下法 (SGD)](https://scikit-learn.org/stable/modules/sgd.html#regression)|[確率的勾配降下法 (SGD)](https://scikit-learn.org/stable/modules/sgd.html#regression)
[ランダム フォレスト](https://scikit-learn.org/stable/modules/ensemble.html#random-forests)|[ランダム フォレスト](https://scikit-learn.org/stable/modules/ensemble.html#random-forests)|[ランダム フォレスト](https://scikit-learn.org/stable/modules/ensemble.html#random-forests)
[Extremely Randomized Trees](https://scikit-learn.org/stable/modules/ensemble.html#extremely-randomized-trees)|[Extremely Randomized Trees](https://scikit-learn.org/stable/modules/ensemble.html#extremely-randomized-trees)|[Extremely Randomized Trees](https://scikit-learn.org/stable/modules/ensemble.html#extremely-randomized-trees)
[Xgboost](https://xgboost.readthedocs.io/en/latest/parameter.html)|[Xgboost](https://xgboost.readthedocs.io/en/latest/parameter.html)| [Xgboost](https://xgboost.readthedocs.io/en/latest/parameter.html)
[DNN 分類子](https://www.tensorflow.org/api_docs/python/tf/estimator/DNNClassifier)|[DNN リグレッサー](https://www.tensorflow.org/api_docs/python/tf/estimator/DNNRegressor) | [DNN リグレッサー](https://www.tensorflow.org/api_docs/python/tf/estimator/DNNRegressor)|
[DNN 線形分類子](https://www.tensorflow.org/api_docs/python/tf/estimator/LinearClassifier)|[線形リグレッサー](https://www.tensorflow.org/api_docs/python/tf/estimator/LinearRegressor)|[線形リグレッサー](https://www.tensorflow.org/api_docs/python/tf/estimator/LinearRegressor)
[単純ベイズ](https://scikit-learn.org/stable/modules/naive_bayes.html#bernoulli-naive-bayes)|[高速線形リグレッサー](https://docs.microsoft.com/python/api/nimbusml/nimbusml.linear_model.fastlinearregressor?view=nimbusml-py-latest)|[自動 ARIMA](https://www.alkaline-ml.com/pmdarima/modules/generated/pmdarima.arima.auto_arima.html#pmdarima.arima.auto_arima)
[確率的勾配降下法 (SGD)](https://scikit-learn.org/stable/modules/sgd.html#sgd)|[オンライン勾配降下リグレッサー](https://docs.microsoft.com/python/api/nimbusml/nimbusml.linear_model.onlinegradientdescentregressor?view=nimbusml-py-latest)|[Prophet](https://facebook.github.io/prophet/docs/quick_start.html)
|[平均化パーセプトロン分類子](https://docs.microsoft.com/python/api/nimbusml/nimbusml.linear_model.averagedperceptronbinaryclassifier?view=nimbusml-py-latest)||ForecastTCN
|[線形 SVM 分類子](https://docs.microsoft.com/python/api/nimbusml/nimbusml.linear_model.linearsvmbinaryclassifier?view=nimbusml-py-latest)||

`AutoMLConfig` コンストラクターで `task` パラメーターを使用して、実験の種類を指定します。

```python
from azureml.train.automl import AutoMLConfig

# task can be one of classification, regression, forecasting
automl_config = AutoMLConfig(task = "classification")
```

## <a name="data-source-and-format"></a>データ ソースと形式

自動機械学習では、ローカル デスクトップ上またはクラウド (Azure Blob Storage など) に存在するデータがサポートされます。 データは、**Pandas DataFrame** または **Azure Machine Learning TabularDataset** に読み込むことができます。  [データセットの詳細情報](https://github.com/MicrosoftDocs/azure-docs-pr/pull/how-to-create-register-datasets.md).

トレーニング データの要件:
- データは表形式である必要があります。
- 予測する値、ターゲット列は、データ内にある必要があります。

次のコード例は、これらの形式でデータを格納する方法を示しています。

* TabularDataset
  ```python
  from azureml.core.dataset import Dataset
  
  tabular_dataset = Dataset.Tabular.from_delimited_files("https://automldemods.blob.core.windows.net/datasets/PlayaEvents2016,_1.6MB,_3.4k-rows.cleaned.2.tsv")
  train_dataset, test_dataset = tabular_dataset.random_split(percentage = 0.1, seed = 42)
  label = "Label"
  ```

* Pandas データフレーム

    ```python
    import pandas as pd
    from sklearn.model_selection import train_test_split

    df = pd.read_csv("https://automldemods.blob.core.windows.net/datasets/PlayaEvents2016,_1.6MB,_3.4k-rows.cleaned.2.tsv", delimiter="\t", quotechar='"')
    train_data, test_data = train_test_split(df, test_size = 0.1, random_state = 42)
    label = "Label"
    ```

## <a name="fetch-data-for-running-experiment-on-remote-compute"></a>リモート コンピューティング上で実行している実験にデータをフェッチする

リモート実行の場合、トレーニング データにリモート コンピューティングからアクセスできる必要があります。 SDK のクラス [`Datasets`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset.dataset?view=azure-ml-py) では、次の機能を公開しています。

* 静的ファイルまたは URL ソースからワークスペースにデータを簡単に転送する
* クラウド コンピューティング リソースでの実行時にデータをトレーニング スクリプトで使用できるようにする

`Dataset` クラスを使用してコンピューティング ターゲットにデータをマウントする例については、[方法](how-to-train-with-datasets.md#option-2--mount-files-to-a-remote-compute-target)の記事を参照してください。

## <a name="train-and-validation-data"></a>データをトレーニングして検証する

`AutoMLConfig` コンストラクターでは、個別のトレーニングおよび検証セットを直接指定できます。

### <a name="k-folds-cross-validation"></a>K フォールド クロス検証

`n_cross_validations` の設定を使用して、クロス検証の数を指定します。 トレーニング データ セットは、等しいサイズの `n_cross_validations` 個のフォールドにランダムに分割されます。 各クロス検証ラウンドの間に、フォールドの 1 つが、残りのフォールドでトレーニングされたモデルの検証に使用されます。 各フォールドが検証セットとして 1 回使用されるまで、このプロセスが `n_cross_validations` ラウンドに対して繰り返されます。 すべての `n_cross_validations` ラウンドの平均スコアがレポートされ、対応するモデルがトレーニング データ セット全体で再トレーニングされます。

### <a name="monte-carlo-cross-validation-repeated-random-sub-sampling"></a>モンテカルロ クロス検証 (反復ランダム サブサンプリング)

`validation_size` を使用して検証に使用する必要があるトレーニング データセットの割合を指定し、`n_cross_validations` を使用してクロス検証の数を指定します。 各クロス検証ラウンドの間に、サイズ `validation_size` のサブセットが、残りのデータでトレーニングされたモデルの検証にランダムに選択されます。 最後に、すべての `n_cross_validations` ラウンドの平均スコアが報告されて、対応するモデルがトレーニング データ セットが全体で再トレーニングされます。 モンテカルロは時系列予測ではサポートされていません。

### <a name="custom-validation-dataset"></a>カスタム検証データセット

ランダムな分割が受け入れられない場合は (通常は、時系列データまたは不均衡なデータ)、カスタム検証データセットを使用します。 独自の検証データセットを指定できます。 モデルは、ランダムなデータセットではなく、指定された検証データセットに対して評価されます。

## <a name="compute-to-run-experiment"></a>実験を実行するために計算する

次に、モデルをトレーニングする場所を決定します。 自動機械学習のトレーニング実験は、次のコンピューティング オプションで実行できます。
*   ローカル デスクトップやラップトップなどのローカル コンピューター – 一般に、データセットが小さく、まだ探索ステージにいる場合。
*   クラウド上のリモート マシン – [Azure Machine Learning Managed Compute](concept-compute-target.md#amlcompute) は、Azure 仮想マシンのクラスター上で機械学習モデルをトレーニングできるようにするマネージド サービスです。

ローカルとリモートのコンピューティング先を使用したノートブックの例については、[GitHub サイト](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/automated-machine-learning)をご覧ください。

*   Azure サブスクリプション内の Azure Databricks クラスター。 詳細については、[自動 ML のための Azure Databricks クラスターの構成](how-to-configure-environment.md#azure-databricks)に関する記事を参照してください

Azure Databricks でのノートブックの例については、[GitHub サイト](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/azure-databricks/automl) を参照してください。

<a name='configure-experiment'></a>

## <a name="configure-your-experiment-settings"></a>実験の設定を構成する

自動機械学習の実験を構成するために使用できるオプションがいくつかあります。 これらのパラメーターは、`AutoMLConfig` オブジェクトをインスタンス化することによって設定します。 パラメーターの完全な一覧については、「[AutoMLConfig クラス](https://docs.microsoft.com/python/api/azureml-train-automl/azureml.train.automl.automlconfig?view=azure-ml-py)」をご覧ください。

次に例をいくつか示します。

1.  プライマリ メトリックとして重み付けされた AUC を使用する分類の実験。実験のタイムアウトの分数は 30 分間、クロス検証フォールドは 2 つに設定されます。

    ```python
    automl_classifier=AutoMLConfig(
        task='classification',
        primary_metric='AUC_weighted',
        experiment_timeout_minutes=30,
        blacklist_models='XGBoostClassifier',
        training_data=train_data,
        label_column_name=label,
        n_cross_validations=2)
    ```
2.  次の例は、60 分後、クロス検証フォールドが 5 つで終了するように設定された回帰実験です。

    ```python
    automl_regressor = AutoMLConfig(
        task='regression',
        experiment_timeout_minutes=60,
        whitelist_models='kNN regressor'
        primary_metric='r2_score',
        training_data=train_data,
        label_column_name=label,
        n_cross_validations=5)
    ```

3 つの異なる `task` パラメーター値 (3 つ目の task-type は `forecasting` であり、`regression` タスクと類似のアルゴリズム プールを使用します) によって、適用するモデルの一覧が決まります。 使用可能なモデルを包含または除外してさらにイテレーションを変更するには、`whitelist` または `blacklist` パラメーターを使用します。 サポートされているモデルの一覧については、「[SupportedModels クラス](https://docs.microsoft.com/python/api/azureml-train-automl/azureml.train.automl.constants.supportedmodels?view=azure-ml-py)」を参照してください。

### <a name="primary-metric"></a>主要メトリック
主要メトリックによって、モデルのトレーニング中に最適化のために使用されるメトリックが決まります。 選択できるメトリックは、選択したタスクの種類によって決まります。次の表に、各タスクの種類に有効な主要メトリックを示します。

|分類 | 回帰 | 時系列予測
|-- |-- |--
|accuracy| spearman_correlation | spearman_correlation
|AUC_weighted | normalized_root_mean_squared_error | normalized_root_mean_squared_error
|average_precision_score_weighted | r2_score | r2_score
|norm_macro_recall | normalized_mean_absolute_error | normalized_mean_absolute_error
|precision_score_weighted |

それらの具体的な定義については、「[自動化機械学習の結果の概要](how-to-understand-automated-ml.md)」を参照してください。

### <a name="data-preprocessing--featurization"></a>データの前処理と特徴付け

すべての自動機械学習実験では、さまざまなスケールの特徴を検知できる*特定の*アルゴリズムをサポートできるように、データが[自動的にスケーリングおよび正規化](concept-automated-ml.md#preprocess)されます。  ただし、不足値の代入、エンコード、および変換などの前処理と特徴付けも追加で有効にできます。 含まれる特徴付けに関する詳細は[こちら](how-to-create-portal-experiments.md#preprocess)にあります。

この特徴付けを有効にするには、[`AutoMLConfig` クラス](https://docs.microsoft.com/python/api/azureml-train-automl/azureml.train.automl.automlconfig?view=azure-ml-py)に `"preprocess": True` を指定します。

> [!NOTE]
> 自動化された機械学習の前処理手順 (機能の正規化、欠損データの処理、テキストから数値への変換など) は、基になるモデルの一部になります。 モデルを予測に使用する場合、トレーニング中に適用されたのと同じ前処理手順が入力データに自動的に適用されます。

### <a name="time-series-forecasting"></a>時系列予測
時系列 `forecasting` タスクでは、構成オブジェクトに追加のパラメーターが必要です。

1. `time_column_name`:有効な時系列を含むトレーニング データ内の列の名前を定義する必須のパラメーターです。
1. `max_horizon`:トレーニング データの周期性に基づいて予測する時間の長さを定義します。 たとえば、1 日の時間グレインのトレーニング データがある場合は、モデルをトレーニングする日数を定義します。
1. `grain_column_names`:トレーニング データ内の個々の時系列データを含む列の名前を定義します。 たとえば、店舗ごとに特定のブランドの売上を予測している場合は、店舗とブランドの列をグレインの列として定義します。 グレインやグループごとに個別の時系列と予測が作成されます。 

以下で使用されている設定の例については、[サンプル ノートブック](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/forecasting-orange-juice-sales/auto-ml-forecasting-orange-juice-sales.ipynb)のページを参照してください。

```python
# Setting Store and Brand as grains for training.
grain_column_names = ['Store', 'Brand']
nseries = data.groupby(grain_column_names).ngroups

# View the number of time series data with defined grains
print('Data contains {0} individual time-series.'.format(nseries))
```

```python
time_series_settings = {
    'time_column_name': time_column_name,
    'grain_column_names': grain_column_names,
    'drop_column_names': ['logQuantity'],
    'max_horizon': n_test_periods
}

automl_config = AutoMLConfig(task = 'forecasting',
                             debug_log='automl_oj_sales_errors.log',
                             primary_metric='normalized_root_mean_squared_error',
                             experiment_timeout_minutes=20,
                             training_data=train_data,
                             label_column_name=label,
                             n_cross_validations=5,
                             path=project_folder,
                             verbosity=logging.INFO,
                             **time_series_settings)
```

### <a name="ensemble"></a> アンサンブル構成

アンサンブル モデルは既定で有効になっており、自動化された機械学習の実行での最終実行イテレーションとして表示されます。 現在サポートされているアンサンブル法は、投票とスタッキングです。 投票は加重平均を使用したソフト投票として実装され、スタッキング実装は 2 層の実装を使用します。ここでは、第 1 層は投票アンサンブルと同じモデルを持ち、第 2 層のモデルは、第 1 層からのモデルの最適な組み合わせを見つけるために使用されます。 ONNX モデルを使用している場合、**または**モデルの説明を有効にしている場合、スタッキングは無効になり、投票だけが使用されます。

既定のスタッキング アンサンブル動作を変更するために、`AutoMLConfig` オブジェクトに `kwargs` として提供できる既定の引数が複数あります。

* `stack_meta_learner_type`: メタ学習器は、個々の異種モデルの出力でトレーニングされたモデルです。 既定のメタ学習器は、分類タスクには `LogisticRegression` (またはクロス検証が有効になっている場合は `LogisticRegressionCV`) で、回帰/予測タスクには `ElasticNet` (またはクロス検証が有効になっている場合は `ElasticNetCV`) です。 このパラメーターは、`LogisticRegression`、`LogisticRegressionCV`、`LightGBMClassifier`、`ElasticNet`、`ElasticNetCV`、`LightGBMRegressor`、または `LinearRegression` のいずれかの文字列になります。
* `stack_meta_learner_train_percentage`: メタ学習器のトレーニング用に予約されるトレーニング セットの割合を指定します。(トレーニングと、トレーニングの検証の種類を選択する場合)。 既定値は `0.2` です。
* `stack_meta_learner_kwargs`: メタ学習器の初期化子に渡す省略可能なパラメーター。 これらのパラメーターとパラメーターの型は、対応するモデル コンストラクターからのものを反映しており、モデル コンストラクターに転送されます。

次のコードは、`AutoMLConfig` オブジェクトでカスタムのアンサンブル動作を指定する例を示します。

```python
ensemble_settings = {
    "stack_meta_learner_type": "LogisticRegressionCV",
    "stack_meta_learner_train_percentage": 0.3,
    "stack_meta_learner_kwargs": {
        "refit": True,
        "fit_intercept": False,
        "class_weight": "balanced",
        "multi_class": "auto",
        "n_jobs": -1
    }
}

automl_classifier = AutoMLConfig(
        task='classification',
        primary_metric='AUC_weighted',
        experiment_timeout_minutes=30,
        training_data=train_data,
        label_column_name=label,
        n_cross_validations=5,
        **ensemble_settings
        )
```

既定でアンサンブル トレーニングが有効になっていますが、`enable_voting_ensemble` および `enable_stack_ensemble` のブール型パラメーターを使用して無効にすることができます。

```python
automl_classifier = AutoMLConfig(
        task='classification',
        primary_metric='AUC_weighted',
        experiment_timeout_minutes=30,
        training_data=data_train,
        label_column_name=label,
        n_cross_validations=5,
        enable_voting_ensemble=False,
        enable_stack_ensemble=False
        )
```

## <a name="run-experiment"></a>実験を実行する

自動化された ML の場合は `Experiment` オブジェクトを作成します。これは、実験を実行するために使用される `Workspace` 内の名前付きオブジェクトです。

```python
from azureml.core.experiment import Experiment

ws = Workspace.from_config()

# Choose a name for the experiment and specify the project folder.
experiment_name = 'automl-classification'
project_folder = './sample_projects/automl-classification'

experiment = Experiment(ws, experiment_name)
```

実験を送信して実行し、モデルを生成します。 モデルを生成するには、`AutoMLConfig` を `submit` メソッドに渡します。

```python
run = experiment.submit(automl_config, show_output=True)
```

>[!NOTE]
>最初に依存関係が新しいマシンにインストールされます。  出力が表示されるまで、最大で 10 分間かかる場合があります。
>`show_output` を `True` に設定すると、コンソールに出力が表示されます。

### <a name="exit-criteria"></a>終了基準
実験を終了するために定義できるオプションがいくつかあります。
1. 基準なし:終了パラメーターを定義しない場合、実験は、主なメトリックでそれ以上の進歩が見られなくなるまで続けられます。
1. 一定の時間が経過したら終了する:設定で `experiment_timeout_minutes` を使用すると、実験の実行を継続する時間 (分) を定義できます。
1. スコアに達した後に終了する:`experiment_exit_score` を使用すると、主要メトリックのスコアに達した後に実験が完了します。

### <a name="explore-model-metrics"></a>モデル メトリックを探索する

トレーニング結果をウィジェットで、またはノートブックの場合はインラインで、表示できます。 詳細については、[モデルの追跡と評価](how-to-track-experiments.md#view-run-details)に関するセクションをご覧ください。

## <a name="understand-automated-ml-models"></a>自動化された ML モデルを理解する

自動化された ML を使用して生成されたモデルには、次の手順が含まれます。
+ 自動化された特徴エンジニアリング: (preprocess=True の場合)
+ ハイパーパラメーター値を使用したスケーリング/正規化とアルゴリズム

透過的に処理して、自動化された ML の fitted_model 出力からこの情報を取得します。

```python
automl_config = AutoMLConfig(…)
automl_run = experiment.submit(automl_config …)
best_run, fitted_model = automl_run.get_output()
```

### <a name="automated-feature-engineering"></a>自動化された特徴エンジニア リング

feauturization =auto の場合に実行される前処理および[自動化された特徴エンジニアリング](concept-automated-ml.md#preprocess)の一覧を参照してください。

次の例を考えてみましょう。
+ 次の 4 つの入力特徴があります:A (数値)、B (数値)、C (数値)、D (日時)
+ 数値の特徴 C は、すべて一意の値を持つ ID 列であるためドロップされます
+ 数値の特徴 A と B には欠損値があるため、平均値で補完されます
+ 日時の特徴 D は、11 の異なるエンジニアリングされた特徴に特徴付けされます

適合モデルの最初のステップで次の 2 つの API を使用して、詳細を確認します。  [こちらのサンプル ノートブック](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/automated-machine-learning/forecasting-energy-demand)を参照してください。

+ API 1: `get_engineered_feature_names()` はエンジニアリングされた特徴の名前の一覧を返します。

  用途:
  ```python
  fitted_model.named_steps['timeseriestransformer']. get_engineered_feature_names ()
  ```

  ```
  Output: ['A', 'B', 'A_WASNULL', 'B_WASNULL', 'year', 'half', 'quarter', 'month', 'day', 'hour', 'am_pm', 'hour12', 'wday', 'qday', 'week']
  ```

  このリストには、すべてのエンジニアリングされた特徴の名前が含まれます。

  >[!Note]
  >task='forecasting' に 'timeseriestransformer' を使用するか、'regression' または 'classification' タスクに 'datatransformer' を使用します。

+ API 2: `get_featurization_summary()` は、すべての入力特徴について、特徴付けの概要を返します。

  用途:
  ```python
  fitted_model.named_steps['timeseriestransformer'].get_featurization_summary()
  ```

  >[!Note]
  >task='forecasting' に 'timeseriestransformer' を使用するか、'regression' または 'classification' タスクに 'datatransformer' を使用します。

  出力:
  ```
  [{'RawFeatureName': 'A',
    'TypeDetected': 'Numeric',
    'Dropped': 'No',
    'EngineeredFeatureCount': 2,
    'Tranformations': ['MeanImputer', 'ImputationMarker']},
   {'RawFeatureName': 'B',
    'TypeDetected': 'Numeric',
    'Dropped': 'No',
    'EngineeredFeatureCount': 2,
    'Tranformations': ['MeanImputer', 'ImputationMarker']},
   {'RawFeatureName': 'C',
    'TypeDetected': 'Numeric',
    'Dropped': 'Yes',
    'EngineeredFeatureCount': 0,
    'Tranformations': []},
   {'RawFeatureName': 'D',
    'TypeDetected': 'DateTime',
    'Dropped': 'No',
    'EngineeredFeatureCount': 11,
    'Tranformations': ['DateTime','DateTime','DateTime','DateTime','DateTime','DateTime','DateTime','DateTime','DateTime','DateTime','DateTime']}]
  ```

   各値の説明:

   |Output|定義|
   |----|--------|
   |RawFeatureName|指定されたデータセットの入力特徴/列の名前。|
   |TypeDetected|検出された入力特徴のデータ型。|
   |Dropped|入力特徴がドロップされたか、または使用されたかを示す。|
   |EngineeringFeatureCount|自動化された特徴エンジニアリングの変換によって生成された特徴の数。|
   |変換|エンジニアリングされた特徴を生成するために入力特徴に適用される変換の一覧。|
   
### <a name="customize-feature-engineering"></a>特徴エンジニアリングをカスタマイズする
特徴エンジニアリングをカスタマイズするには、 `"feauturization":FeaturizationConfig` を指定します。

サポートされているカスタマイズは次のとおりです。

|カスタマイズ|定義|
|--|--|
|列の目的の更新|指定した列の特徴の種類をオーバーライドします。|
|トランスフォーマー パラメーターの更新 |指定したトランスフォーマーのパラメーターを更新します。 現在、Imputer および HashOneHotEncoder がサポートされています。|
|列の削除 |特徴付けされない列です。|
|ブロック トランスフォーマー| 特徴付けプロセスで使用されるトランスフォーマーをブロックします。|

API 呼び出しを使用して、FeaturizationConfig オブジェクトを作成します。
```python
featurization_config = FeaturizationConfig()
featurization_config.blocked_transformers = ['LabelEncoder']
featurization_config.drop_columns = ['aspiration', 'stroke']
featurization_config.add_column_purpose('engine-size', 'Numeric')
featurization_config.add_column_purpose('body-style', 'CategoricalHash')
#default strategy mean, add transformer param for for 3 columns
featurization_config.add_transformer_params('Imputer', ['engine-size'], {"strategy": "median"})
featurization_config.add_transformer_params('Imputer', ['city-mpg'], {"strategy": "median"})
featurization_config.add_transformer_params('Imputer', ['bore'], {"strategy": "most_frequent"})
featurization_config.add_transformer_params('HashOneHotEncoder', [], {"number_of_bits": 3})
```

### <a name="scalingnormalization-and-algorithm-with-hyperparameter-values"></a>ハイパーパラメーター値を使用したスケーリング/正規化とアルゴリズム:

パイプラインのスケーリング/正規化およびアルゴリズム/ハイパーパラメーター値を理解するには、fitted_model.steps を使用します。 [スケーリング/正規化の詳細については、こちらを参照してください](concept-automated-ml.md#preprocess)。 出力例を次に示します。

```
[('RobustScaler', RobustScaler(copy=True, quantile_range=[10, 90], with_centering=True, with_scaling=True)), ('LogisticRegression', LogisticRegression(C=0.18420699693267145, class_weight='balanced', dual=False, fit_intercept=True, intercept_scaling=1, max_iter=100, multi_class='multinomial', n_jobs=1, penalty='l2', random_state=None, solver='newton-cg', tol=0.0001, verbose=0, warm_start=False))
```

詳細については、[こちらのサンプル ノートブック](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/classification/auto-ml-classification.ipynb)に示されたヘルパー関数を使用してください。

```python
from pprint import pprint


def print_model(model, prefix=""):
    for step in model.steps:
        print(prefix + step[0])
        if hasattr(step[1], 'estimators') and hasattr(step[1], 'weights'):
            pprint({'estimators': list(
                e[0] for e in step[1].estimators), 'weights': step[1].weights})
            print()
            for estimator in step[1].estimators:
                print_model(estimator[1], estimator[0] + ' - ')
        else:
            pprint(step[1].get_params())
            print()


print_model(fitted_model)
```

特定のアルゴリズム (この例では、LogisticRegression with RobustScalar) を使用したパイプラインのサンプル出力を次に示します。

```
RobustScaler
{'copy': True,
'quantile_range': [10, 90],
'with_centering': True,
'with_scaling': True}

LogisticRegression
{'C': 0.18420699693267145,
'class_weight': 'balanced',
'dual': False,
'fit_intercept': True,
'intercept_scaling': 1,
'max_iter': 100,
'multi_class': 'multinomial',
'n_jobs': 1,
'penalty': 'l2',
'random_state': None,
'solver': 'newton-cg',
'tol': 0.0001,
'verbose': 0,
'warm_start': False}
```

<a name="explain"></a>

## <a name="model-interpretability"></a>モデルの解釈可能性

モデルの解釈機能を使用すると、モデルで予測が行われた理由や、基になる特徴の重要度の値を把握できます。 SDK には、トレーニングと推論時間の両方で、ローカル モデルとデプロイされたモデルについて、モデルの解釈可能性の特徴を有効にするためのさまざまなパッケージが含まれています。

特に自動化された機械学習の実験で解釈可能性の特徴を有効にする方法のコード サンプルについては、その[方法](how-to-machine-learning-interpretability-automl.md)を参照してください。

自動機械学習ではない SDK の他の領域で、モデルの説明と特徴の重要度を有効にする方法の一般情報については、解釈可能性の[概念](how-to-machine-learning-interpretability.md)に関する記事を参照してください。

## <a name="next-steps"></a>次の手順

[モデルをデプロイする方法と場所](how-to-deploy-and-where.md)についてさらに詳しく学習する。

[自動機械学習を使用して回帰モデルをトレーニングする方法](tutorial-auto-train-models.md)または[リモート リソースに対して自動機械学習を使用してトレーニングする方法](how-to-auto-train-remote.md)についてさらに詳しく学習する。
