---
title: 自動 ML の実験を作成する
titleSuffix: Azure Machine Learning service
description: 自動機械学習は、アルゴリズムを自動的に選択して、デプロイできる状態のモデルを生成します。 自動機械学習の実験の構成に使用できるオプションについて説明します。
author: nacharya1
ms.author: nilesha
ms.reviewer: sgilley
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: conceptual
ms.date: 12/04/2018
ms.custom: seodec18
ms.openlocfilehash: 3dedf5de1ac2c88a9a00fd5f62e0663b840c0fd9
ms.sourcegitcommit: c2e61b62f218830dd9076d9abc1bbcb42180b3a8
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/15/2018
ms.locfileid: "53438525"
---
# <a name="configure-automated-machine-learning-experiments"></a>自動機械学習の実験を構成する

自動機械学習は、アルゴリズムとハイパーパラメーターを自動的に選択して、デプロイできる状態のモデルを生成します。 自動機械学習の実験の構成に使用できるオプションはいくつかあります。 このガイドでは、さまざまな構成設定を定義する方法について説明します。

自動機械学習の実験の例を確認するには、[自動機械学習を使用した分類モデルのトレーニングのチュートリアル](tutorial-auto-train-models.md)に関するページか、「[クラウドで自動機械学習を使用してモデルをトレーニングする](how-to-auto-train-remote.md)」をご覧ください。

自動機械学習で使用できる構成オプション:

* 実験の種類を選択する:分類、回帰、または予測
* データ ソース、形式、およびデータのフェッチ
* コンピューティング先を選択する: ローカルまたはリモート
* 自動機械学習の実験の設定
* 自動機械学習の実験を実行する
* モデル メトリックを探索する
* モデルを登録して展開する

## <a name="select-your-experiment-type"></a>実験の種類を選択する
実験を始める前に、解決する機械学習の問題の種類を決める必要があります。 自動機械学習でサポートされるタスクの種類は、分類、回帰、予測です。 

自動機械学習の機能は一般提供されていますが、**予測機能はまだパブリック プレビュー段階にあります**。

自動機械学習では、自動化とチューニングのプロセス中に次のアルゴリズムがサポートされています。 ユーザーは、アルゴリズムを指定する必要はありません。

分類 | 回帰 | 予測
|-- |-- |--
[ロジスティック回帰](https://scikit-learn.org/stable/modules/linear_model.html#logistic-regression)| [Elastic Net](https://scikit-learn.org/stable/modules/linear_model.html#elastic-net)| [Elastic Net](https://scikit-learn.org/stable/modules/linear_model.html#elastic-net)
[確率的勾配降下法 (SGD)](https://scikit-learn.org/stable/modules/sgd.html#sgd)|[Light GBM](https://lightgbm.readthedocs.io/en/latest/index.html)|[Light GBM](https://lightgbm.readthedocs.io/en/latest/index.html)
[単純ベイズ](https://scikit-learn.org/stable/modules/naive_bayes.html#bernoulli-naive-bayes)|[勾配ブースティング](https://scikit-learn.org/stable/modules/ensemble.html#regression)|[勾配ブースティング](https://scikit-learn.org/stable/modules/ensemble.html#regression)
[C のサポート ベクター分類 (SVC)](https://scikit-learn.org/stable/modules/svm.html#classification)|[デシジョン ツリー](https://scikit-learn.org/stable/modules/tree.html#regression)|[デシジョン ツリー](https://scikit-learn.org/stable/modules/tree.html#regression)
[Linear SVC](https://scikit-learn.org/stable/modules/svm.html#classification)|[K ニアレスト ネイバー](https://scikit-learn.org/stable/modules/neighbors.html#nearest-neighbors-regression)|[K ニアレスト ネイバー](https://scikit-learn.org/stable/modules/neighbors.html#nearest-neighbors-regression)
[K ニアレスト ネイバー](https://scikit-learn.org/stable/modules/neighbors.html#nearest-neighbors)|[LARS Lasso](https://scikit-learn.org/stable/modules/linear_model.html#lars-lasso)|[LARS Lasso](https://scikit-learn.org/stable/modules/linear_model.html#lars-lasso)
[デシジョン ツリー](https://scikit-learn.org/stable/modules/tree.html#decision-trees)|[確率的勾配降下法 (SGD)](https://scikit-learn.org/stable/modules/sgd.html#regression)|[確率的勾配降下法 (SGD)](https://scikit-learn.org/stable/modules/sgd.html#regression)
[ランダム フォレスト](https://scikit-learn.org/stable/modules/ensemble.html#random-forests)|[ランダム フォレスト](https://scikit-learn.org/stable/modules/ensemble.html#random-forests)|[ランダム フォレスト](https://scikit-learn.org/stable/modules/ensemble.html#random-forests)
[Extremely Randomized Trees](https://scikit-learn.org/stable/modules/ensemble.html#extremely-randomized-trees)|[Extremely Randomized Trees](https://scikit-learn.org/stable/modules/ensemble.html#extremely-randomized-trees)|[Extremely Randomized Trees](https://scikit-learn.org/stable/modules/ensemble.html#extremely-randomized-trees)
[勾配ブースティング](https://scikit-learn.org/stable/modules/ensemble.html#classification)|
[Light GBM](https://lightgbm.readthedocs.io/en/latest/index.html)|


## <a name="data-source-and-format"></a>データ ソースと形式
自動機械学習では、ローカル デスクトップ上またはクラウド (Azure Blob Storage など) に存在するデータがサポートされます。 データは、scikit-learn でサポートされているデータ形式に読み込むことができます。 次のものにデータを読み込めます。
* Numpy 配列 X (特徴) と y (ターゲット変数、ラベルとも呼ばれます)
* Pandas データフレーム 

次に例を示します。

*   Numpy 配列

    ```python
    digits = datasets.load_digits()
    X_digits = digits.data 
    y_digits = digits.target
    ```

*   Pandas データフレーム

    ```python
    import pandas as pd
    df = pd.read_csv("https://automldemods.blob.core.windows.net/datasets/PlayaEvents2016,_1.6MB,_3.4k-rows.cleaned.2.tsv", delimiter="\t", quotechar='"') 
    # get integer labels 
    df = df.drop(["Label"], axis=1) 
    df_train, _, y_train, _ = train_test_split(df, y, test_size=0.1, random_state=42)
    ```

## <a name="fetch-data-for-running-experiment-on-remote-compute"></a>リモート コンピューティング上で実行している実験にデータをフェッチする

リモート コンピューティングを使用して実験を実行している場合、データのフェッチを別の Python スクリプト `get_data()` でラップする必要があります。 このスクリプトは、自動機械学習の実験が実行されているリモート コンピューティングで実行されます。 `get_data` を使用すると、各イテレーションに対してネットワーク経由でデータをフェッチする必要がなくなります。 `get_data` を使用しないと、リモート コンピューティングで実行している場合は実験が失敗します。

`get_data` の例を以下に示します。

```python
%%writefile $project_folder/get_data.py 
import pandas as pd 
from sklearn.model_selection import train_test_split 
from sklearn.preprocessing import LabelEncoder 
def get_data(): # Burning man 2016 data 
    df = pd.read_csv("https://automldemods.blob.core.windows.net/datasets/PlayaEvents2016,_1.6MB,_3.4k-rows.cleaned.2.tsv", delimiter="\t", quotechar='"') 
    # get integer labels 
    le = LabelEncoder() 
    le.fit(df["Label"].values) 
    y = le.transform(df["Label"].values) 
    df = df.drop(["Label"], axis=1) 
    df_train, _, y_train, _ = train_test_split(df, y, test_size=0.1, random_state=42) 
    return { "X" : df, "y" : y }
```

`AutoMLConfig` オブジェクト内では、`data_script` パラメーターを指定し、次のように `get_data` スクリプト ファイルへのパスを指定します。

```python
automl_config = AutoMLConfig(****, data_script=project_folder + "/get_data.py", **** )
```

`get_data` スクリプトは次のものを返すことができます。

キー | type |    相互に排他的 | 説明
---|---|---|---
X | Pandas データフレームまたは Numpy 配列 | data_train、label、columns |  トレーニングするすべての機能
y | Pandas データフレームまたは Numpy 配列 |   label   | トレーニングするラベル データ。 分類の場合、整数の配列にする必要があります。
X_valid | Pandas データフレームまたは Numpy 配列   | data_train、label | "_省略可能_" 検証するすべての機能。 指定しない場合、X はトレーニング間で分割されて検証されます
y_valid |   Pandas データフレームまたは Numpy 配列 | data_train、label | "_省略可能_" 検証するラベル データ。 指定しない場合、y はトレーニング間で分割されて検証されます
sample_weight | Pandas データフレームまたは Numpy 配列 |   data_train、label、columns| "_省略可能_" 各サンプルの重み値。 データ ポイントに異なる重みを割り当てる場合に使用します 
sample_weight_valid | Pandas データフレームまたは Numpy 配列 | data_train、label、columns |    "_省略可能_" 各検証サンプルの重み値。 指定しない場合、sample_weight はトレーニング間で分割されて検証されます
data_train |    Pandas データフレーム |  X、y、X_valid、y_valid |    トレーニングするすべてのデータ (機能 + ラベル)
label | string  | X、y、X_valid、y_valid |  data_train 内のどの列がラベルを表すか
columns | 文字列の配列  ||  "_省略可能_" 機能に使用する列のホワイトリスト
cv_splits_indices   | 整数の配列 ||  "_省略可能_" クロス検証用にデータを分割するためのインデックスのリスト

### <a name="load-and-prepare-data-using-dataprep-sdk"></a>データ準備 SDK を使用してデータを読み込み、準備する
自動機械学習の実験では、データ準備 SDK を使用したデータの読み込みと変換がサポートされます。 SDK を使用すると次の機能を実現できます。

>* パラメーターの推論 (エンコード、区切り、ヘッダー) を解析してさまざまな種類のファイルから読み込む
>* ファイルの読み込み中に推論を使用した型変換
>* MS SQL Server と Azure Data Lake Storage の接続のサポート
>* 式による列の追加
>* 欠損値の補完
>* 列の派生の例
>* フィルター処理
>* カスタム Python 変換

データ準備 SDK の詳細については、[モデル化のためのデータの準備方法に関する記事](how-to-load-data.md)をご覧ください。 データ準備 SDK を使用したデータの読み込みの例を次に示します。 
```python
# The data referenced here was pulled from `sklearn.datasets.load_digits()`.
simple_example_data_root = 'https://dprepdata.blob.core.windows.net/automl-notebook-data/'
X = dprep.auto_read_file(simple_example_data_root + 'X.csv').skip(1)  # Remove the header row.
# You can use `auto_read_file` which intelligently figures out delimiters and datatypes of a file.

# Here we read a comma delimited file and convert all columns to integers.
y = dprep.read_csv(simple_example_data_root + 'y.csv').to_long(dprep.ColumnSelector(term='.*', use_regex = True))
```

## <a name="train-and-validation-data"></a>データをトレーニングして検証する

get_data() を使用して、または `AutoMLConfig` メソッドで直接、個別のトレーニングと検証セットを指定できます。

## <a name="cross-validation-split-options"></a>クロス検証の分割のオプション

### <a name="k-folds-cross-validation"></a>K フォールド クロス検証

`n_cross_validations` の設定を使用して、クロス検証の数を指定します。 トレーニング データ セットは、等しいサイズの `n_cross_validations` 個のフォールドにランダムに分割されます。 各クロス検証ラウンドの間に、フォールドの 1 つが、残りのフォールドでトレーニングされたモデルの検証に使用されます。 各フォールドが検証セットとして 1 回使用されるまで、このプロセスが `n_cross_validations` ラウンドに対して繰り返されます。 すべての `n_cross_validations` ラウンドの平均スコアがレポートされ、対応するモデルがトレーニング データ セット全体で再トレーニングされます。

### <a name="monte-carlo-cross-validation-aka-repeated-random-sub-sampling"></a>モンテカルロ クロス検証 (別名: 反復ランダム サブサンプリング)

`validation_size` を使用して検証に使用する必要があるトレーニング データセットの割合を指定し、`n_cross_validations` を使用してクロス検証の数を指定します。 各クロス検証ラウンドの間に、サイズ `validation_size` のサブセットが、残りのデータでトレーニングされたモデルの検証にランダムに選択されます。 最後に、すべての `n_cross_validations` ラウンドの平均スコアが報告されて、対応するモデルがトレーニング データ セットが全体で再トレーニングされます。

### <a name="custom-validation-dataset"></a>カスタム検証データセット

ランダムな分割が受け入れられない場合は (通常は、時系列データまたは不均衡なデータ)、カスタム検証データセットを使用します。 独自の検証データセットを指定できます。 モデルは、ランダムなデータセットではなく、指定された検証データセットに対して評価されます。

## <a name="compute-to-run-experiment"></a>実験を実行するために計算する

次に、モデルをトレーニングする場所を決定します。 自動機械学習のトレーニング実験は、次のコンピューティング オプションで実行できます。
*   ローカル デスクトップやラップトップなどのローカル コンピューター – 一般に、データセットが小さく、まだ探索ステージにいる場合。
*   クラウド上のリモート マシン – [Azure Machine Learning Managed Compute](concept-azure-machine-learning-architecture.md#managed-and-unmanaged-compute-targets) は、Azure 仮想マシンのクラスター上で機械学習モデルをトレーニングできるようにするマネージド サービスです。

ローカルとリモートのコンピューティング先を使用したノートブックの例については、[GitHub サイト](https://github.com/Azure/MachineLearningNotebooks/tree/master/automl)をご覧ください。

<a name='configure-experiment'/>

## <a name="configure-your-experiment-settings"></a>実験の設定を構成する

自動機械学習の実験を構成するために使用できるオプションがいくつかあります。 これらのパラメーターは、`AutoMLConfig` オブジェクトをインスタンス化することによって設定します。

次に例をいくつか示します。

1.  イテレーションごとの最大時間が 12,000 秒で、プライマリ メトリックとして重み付けされた AUC を使用する分類の実験。実験は、50 回のイテレーションと 2 つのクロス検証フォールドの後で終了します。

    ```python
    automl_classifier = AutoMLConfig(
        task='classification',
        primary_metric='AUC_weighted',
        max_time_sec=12000,
        iterations=50,
        X=X, 
        y=y,
        n_cross_validations=2)
    ```
2.  次の例は、100 回のイテレーションの後で終了するように設定された回帰実験です。各イテレーションは、5 個の検証クロス フォールドで 600 秒続きます。

    ````python
    automl_regressor = AutoMLConfig(
        task='regression',
        max_time_sec=600,
        iterations=100,
        primary_metric='r2_score',
        X=X, 
        y=y,
        n_cross_validations=5)
    ````

次の表では、実験に使用できるパラメーター設定とその既定値の一覧を示します。

プロパティ |  説明 | 既定値
--|--|--
`task`  |機械学習の問題の種類を指定します。 次の値を使用できます <li>分類</li><li>回帰</li><li>予測</li>    | なし |
`primary_metric` |モデルの構築で最適化したいメトリック。 たとえば、primary_metric として精度を指定すると、自動機械学習は最大精度のモデルを探します。 実験ごとに指定できる primary_metric は 1 つのみです。 次の値を使用できます <br/>**分類**:<br/><li> accuracy  </li><li> AUC_weighted</li><li> precision_score_weighted </li><li> balanced_accuracy </li><li> average_precision_score_weighted </li><br/>**回帰**: <br/><li> normalized_mean_absolute_error </li><li> spearman_correlation </li><li> normalized_root_mean_squared_error </li><li> normalized_root_mean_squared_log_error</li><li> R2_score  </li> | 分類の場合: accuracy <br/>回帰の場合: spearman_correlation <br/> |
`experiment_exit_score` |   primary_metric に対するターゲット値を設定できます。 primary_metric ターゲットを満たすモデルが見つかると、自動機械学習はイテレーションを停止し、実験は終了します。 この値が設定されていない場合 (既定)、自動機械学習の実験はイテレーションで指定されている回数だけ実行を続けます。 double 値を受け取ります。 ターゲットに達しない場合は、自動機械学習はイテレーションで指定されている回数になるまで続行されます。| なし
`iterations` |イテレーションの最大数。 各イテレーションは、パイプラインになるトレーニング ジョブと同じです。 パイプラインは、データの前処理とモデルです。 高品質のモデルを得るには、250 以上を使用します    | 100
`max_concurrent_iterations`|    並列で実行するイテレーションの最大回数。 この設定は、リモート コンピューティングに対してのみ機能します。|   1
`max_cores_per_iteration`   | 1 つのパイプラインのトレーニングに使用されるコンピューティング ターゲット上のコアの数を示します。 アルゴリズムが複数のコアを使用できる場合は、これによりマルチコア マシンでのパフォーマンスが向上します。 -1 に設定すると、マシンで利用可能なすべてのコアを使用できます。|  1
`iteration_timeout_minutes` |   特定のイテレーションにかかる時間 (分) を制限します。 イテレーションが指定した時間を超えると、そのイテレーションはキャンセルされます。 設定しないと、イテレーションは完了するまで実行を続けます。 |   なし
`n_cross_validations`   |クロス検証の分割の数| なし
`validation_size`   |すべてのトレーニング サンプルの割合として設定する検証のサイズ。|  なし
`preprocess` | True または False <br/>True を指定すると、実験は入力に対して前処理を実行できます。 前処理のサブセットを次に示します<li>不足データ:不足データの補完 - 平均での数値、ほとんどの発生でのテキスト </li><li>カテゴリ値:データ型が数値で、一意の値の数が 5% 未満の場合、ワンホット エンコードに変換します </li><li>その他。完全なリストについては [GitHub リポジトリ](https://aka.ms/aml-notebooks)を確認</li><br/>注: データが疎の場合は、preprocess = true を使用できません |  False | 
`blacklist_models`  | 自動機械学習の実験で試すアルゴリズムには、さまざまなものがあります。 特定のアルゴリズムを実験から除外するように構成します。 アルゴリズムがデータセットに対してうまく動作しないことがわかっている場合に役立ちます。 アルゴリズムを除外すると、コンピューティング リソースとトレーニング時間を節約できます。<br/>分類に使用できる値<br/><li>LogisticRegression</li><li>SGD</li><li>MultinomialNaiveBayes</li><li>BernoulliNaiveBayes</li><li>SVM</li><li>LinearSVM</li><li>KNN</li><li>DecisionTree</li><li>RandomForest</li><li>ExtremeRandomTrees</li><li>LightGBM</li><li>GradientBoosting</li><li>TensorFlowDNN</li><li>TensorFlowLinearClassifier</li><br/>回帰に使用できる値<br/><li>ElasticNet</li><li>GradientBoosting</li><li>DecisionTree</li><li>KNN</li><li>LassoLars</li><li>SGD </li><li>RandomForest</li><li>ExtremeRandomTree</li><li>LightGBM</li><li>TensorFlowLinearRegressor</li><li>TensorFlowDNN</li></li><br/>予測に使用できる値<br/><li>ElasticNet</li><li>GradientBoosting</li><li>DecisionTree</li><li>KNN</li><li>LassoLars</li><li>SGD </li><li>RandomForest</li><li>ExtremeRandomTree</li><li>LightGBM</li><li>TensorFlowLinearRegressor</li><li>TensorFlowDNN</li></li>|   なし
`whitelist_models`  | 自動機械学習の実験で試すアルゴリズムには、さまざまなものがあります。 特定のアルゴリズムを実験に含めるように構成します。 アルゴリズムが自分のデータセットに対してうまく動作することがわかっている場合に役立ちます。 <br/>分類に使用できる値<br/><li>LogisticRegression</li><li>SGD</li><li>MultinomialNaiveBayes</li><li>BernoulliNaiveBayes</li><li>SVM</li><li>LinearSVM</li><li>KNN</li><li>DecisionTree</li><li>RandomForest</li><li>ExtremeRandomTrees</li><li>LightGBM</li><li>GradientBoosting</li><li>TensorFlowDNN</li><li>TensorFlowLinearClassifier</li><br/>回帰に使用できる値<br/><li>ElasticNet</li><li>GradientBoosting</li><li>DecisionTree</li><li>KNN</li><li>LassoLars</li><li>SGD </li><li>RandomForest</li><li>ExtremeRandomTree</li><li>LightGBM</li><li>TensorFlowLinearRegressor</li><li>TensorFlowDNN</li></li><br/>予測に使用できる値<br/><li>ElasticNet</li><li>GradientBoosting</li><li>DecisionTree</li><li>KNN</li><li>LassoLars</li><li>SGD </li><li>RandomForest</li><li>ExtremeRandomTree</li><li>LightGBM</li><li>TensorFlowLinearRegressor</li><li>TensorFlowDNN</li></li>|  なし
`verbosity` |ログのレベルを制御します。INFO は最も詳細で、CRITICAL は最小限です。 詳細レベルは、Python ロギング パッケージで定義されているものと同じ値を取ります。 使用できる値は、以下のとおりです。<br/><li>logging.INFO</li><li>logging.WARNING</li><li>logging.ERROR</li><li>logging.CRITICAL</li>  | logging.INFO</li> 
`X` | トレーニングするすべての機能 |  なし
`y` |   トレーニングするラベル データ。 分類の場合、整数の配列にする必要があります。|  なし
`X_valid`|"_省略可能_" 検証するすべての機能。 指定しない場合、X はトレーニング間で分割されて検証されます |   なし
`y_valid`   |"_省略可能_" 検証するラベル データ。 指定しない場合、y はトレーニング間で分割されて検証されます    | なし
`sample_weight` |   "_省略可能_" 各サンプルの重み値。 データ ポイントに異なる重みを割り当てる場合に使用します |   なし
`sample_weight_valid`   |   "_省略可能_" 各検証サンプルの重み値。 指定しない場合、sample_weight はトレーニング間で分割されて検証されます   | なし
`run_configuration` |   RunConfiguration オブジェクト。  リモート実行に使用されます。 |なし
`data_script`  |    get_data メソッドを含むファイルへのパス。  リモート実行に必要です。   |なし
`model_explainability` | "_省略可能_" True または False <br/>  True を指定すると、実験でイテレーションごとに特徴の重要度が計算されます。 特定のイテレーションで explain_model() メソッドを使用して、実験の完了後にそのイテレーションの特徴の重要度を必要に応じて計算することもできます。 | False
`enable_ensembling`|他のすべてのイテレーションの完了後に、アンサンブル イテレーションを有効にするフラグを設定します。| True 
`ensemble_iterations`|最後のアンサンブルに含める適合したパイプラインを選択するイテレーションの数。| 15
`experiment_timeout_minutes`| 実験全体の実行にかけられる時間 (分) を制限します | なし

## <a name="data-pre-processing-and-featurization"></a>データの前処理と特徴付け

`preprocess=True` を使用すると、次のデータ前処理手順が自動的に実行されます。
1.  高カーディナリティまたは差異なしの特徴の削除
    * トレーニングおよび検証セットから、有用な情報のない特徴を削除します。 これには、まったく値が存在しない特徴、すべての行の値が同じである特徴、非常に高いカーディナリティ (ハッシュ、ID、GUID など) の特徴が含まれます。
1.  欠落値の補完
    *   数値特徴の場合、その列の平均値で欠落値を補完します。
    *   カテゴリ特徴の場合、出現回数が最も多い値で欠落値を補完します。
1.  その他の特徴の生成
    * DateTime の特徴:年、月、日、曜日、年の通算日、四半期、年の通算週、時間、分、秒。
    * テキストの特徴:単語のユニグラム、バイグラム、トリグラムに基づく用語の出現頻度、回数のベクター化。
1.  変換とエンコード
    * 一意の値がほとんどない数値特徴は、カテゴリ特徴に変換されます。
    * カテゴリ特徴のカーディナリティに応じて、ラベル エンコードまたは (ハッシュ) ワンホット エンコードを実行します。

## <a name="run-experiment"></a>実験を実行する

実験を送信して実行し、モデルを生成します。 モデルを生成するには、`AutoMLConfig` を `submit` メソッドに渡します。

```python
run = experiment.submit(automl_config, show_output=True)
```

>[!NOTE]
>最初に依存関係が新しいマシンにインストールされます。  出力が表示されるまで、最大で 10 分間かかる場合があります。
>`show_output` を `True` に設定すると、コンソールに出力が表示されます。


## <a name="explore-model-metrics"></a>モデル メトリックを探索する
結果をウィジェットで、またはノートブックの場合はインラインで、表示できます。 詳細については、[モデルの追跡と評価](how-to-track-experiments.md#view-run-details)に関するセクションをご覧ください。


### <a name="classification-metrics"></a>分類メトリック
分類タスクの各イテレーションで、次のメトリックが保存されます。

|主要メトリック|説明|計算|追加のパラメーター
--|--|--|--|--|
AUC_Macro| AUC は、受信者操作特徴曲線の下の領域です。 Macro は、クラスごとの AUC の算術平均です。  | [計算](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.roc_auc_score.html) | average="macro"|
AUC_Micro| AUC は、受信者操作特徴曲線の下の領域です。 Micro は、各クラスの真陽性と偽陽性を組み合わせることでグローバルに計算されます| [計算](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.roc_auc_score.html) | average="micro"|
AUC_Weighted  | AUC は、受信者操作特徴曲線の下の領域です。 重み付けは各クラスのスコアの算術平均で、各クラス内の true インスタンスの数によって重み付けされます| [計算](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.roc_auc_score.html)|average="weighted"
accuracy|精度は、true ラベルと正確に一致する予測ラベルの割合です。 |[計算](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.accuracy_score.html) |なし|
average_precision_score_macro|平均適合率は、各しきい値で達成した適合率の加重平均として適合率-再現率曲線をまとめたもので、前のしきい値より増加した再現率を重みとして使用します。 Macro は、各クラスの平均適合率スコアの算術平均です|[計算](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.average_precision_score.html)|average="macro"|
average_precision_score_micro|平均適合率は、各しきい値で達成した適合率の加重平均として適合率-再現率曲線をまとめたもので、前のしきい値より増加した再現率を重みとして使用します。 Micro は、各カットオフの真陽性と偽陽性を組み合わせることでグローバルに計算されます|[計算](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.average_precision_score.html)|average="micro"|
average_precision_score_weighted|平均適合率は、各しきい値で達成した適合率の加重平均として適合率-再現率曲線をまとめたもので、前のしきい値より増加した再現率を重みとして使用します。 重み付けは各クラスの平均適合率スコアの算術平均で、各クラス内の true インスタンスの数によって重み付けされます|[計算](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.average_precision_score.html)|average="weighted"|
balanced_accuracy|バランスの取れた精度は、各クラスの再現率の算術平均です。|[計算](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.recall_score.html)|average="macro"|
f1_score_macro|F1 スコアは、適合率と再現率の調和平均です。 Macro は、各クラスの F1 スコアの算術平均です|[計算](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.f1_score.html)|average="macro"|
f1_score_micro|F1 スコアは、適合率と再現率の調和平均です。 Micro は、真陽性、偽陰性、偽陽性の合計をカウントすることによって、グローバルに計算されます|[計算](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.f1_score.html)|average="micro"|
f1_score_weighted|F1 スコアは、適合率と再現率の調和平均です。 各クラスの F1 スコアのクラスごとの頻度の加重平均|[計算](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.f1_score.html)|average="weighted"|
log_loss|これは、(多項) ロジスティック回帰とその拡張機能 (ニューラル ネットワークなど) で使用される損失関数で、確率的分類法の予測を前提として、true ラベルの負の対数尤度として定義されます。 {0,1} の true ラベル yt と yt = 1 の推定確率 yp の 1 つの例として、log loss は -log P(yt&#124;yp) = -(yt log(yp) + (1 - yt) log(1 - yp))|[計算](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.log_loss.html)|なし|
norm_macro_recall|正規化されたマクロ再現率は、ランダムなパフォーマンスでのスコアが 0、最適なパフォーマンスでのスコアが 1 になるように正規化されたマクロ再現率です。 これは、norm_macro_recall := (recall_score_macro - R)/(1 - R) で実現されます。R はランダム予測の recall_score_macro で期待される値 (つまり、二項分類では R=0.5、C クラス分類問題では R=(1/C))|[計算](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.precision_score.html)|average = "macro"。次に (recall_score_macro - R)/(1 - R)。R はランダム予測の recall_score_macro で期待される値 (つまり、二項分類では R=0.5、C クラス分類問題では R=(1/C))|
precision_score_macro|適合率は、あるクラスとしてラベル付けされ、実際にそのクラスに存在する要素の割合です。 Macro は、各クラスの適合率の算術平均です|[計算](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.precision_score.html)|average="macro"|
precision_score_micro|適合率は、あるクラスとしてラベル付けされ、実際にそのクラスに存在する要素の割合です。 Micro は、真陽性と偽陽性の合計をカウントすることによって、グローバルに計算されます|[計算](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.precision_score.html)|average="micro"|
precision_score_weighted|適合率は、あるクラスとしてラベル付けされ、実際にそのクラスに存在する要素の割合です。 重み付けは各クラスの適合率の算術平均で、各クラス内の true インスタンスの数によって重み付けされます|[計算](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.precision_score.html)|average="weighted"|
recall_score_macro|再現率は、あるクラスに実際に存在し、正しくラベル付けされている要素の割合です。 Macro は、各クラスの再現率の算術平均です|[計算](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.recall_score.html)|average="macro"|
recall_score_micro|再現率は、あるクラスに実際に存在し、正しくラベル付けされている要素の割合です。 Micro は、真陽性と偽陰性の合計をカウントすることによって、グローバルに計算されます|[計算](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.recall_score.html)|average="micro"|
recall_score_weighted|再現率は、あるクラスに実際に存在し、正しくラベル付けされている要素の割合です。 重み付けは各クラスの再現率の算術平均で、各クラス内の true インスタンスの数によって重み付けされます|[計算](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.recall_score.html)|average="weighted"|
weighted_accuracy|重み付けされた精度は、それぞれの例に対して指定された重みが、その例の true クラス内の true インスタンスの割合に一致する精度です|[計算](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.accuracy_score.html)|sample_weight は、ターゲット内の各要素に対して、そのクラスの割合が等しいベクターです|

### <a name="regression-and-forecasting-metrics"></a>回帰メトリックと予測メトリック
回帰タスクまたは予測タスクの各イテレーションで、次のメトリックが保存されます。

|主要メトリック|説明|計算|追加のパラメーター
--|--|--|--|--|
explained_variance|説明分散は、ある数学的モデルが、提供されたデータ セットのバリエーションに占める割合です。 エラーの分散に対する元データの分散の減少の割合です。 エラーの平均が 0 の場合は、説明分散と同じです。|[計算](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.explained_variance_score.html)|なし|
r2_score|R2 は、平均を出力するベースライン モデルと比較した、決定係数または二乗誤差の減少の割合です。 エラーの平均が 0 の場合は、説明分散と同じです。|[計算](https://scikit-learn.org/0.16/modules/generated/sklearn.metrics.r2_score.html)|なし|
spearman_correlation|スピアマンの相関は、2 つのデータセット間の関係の単調性に対するノンパラメトリック測定です。 ピアソンの相関とは異なり、スピアマンの相関は両方のデータセットが正規分布していることを想定しません。 他の相関係数と同様に、-1 と +1 の間で変化し、0 は相関関係がないことを示します。 相関係数が -1 または +1 の場合は、完全に単調な関係であることを示します。 正の相関関係は、x、y ともに増加することを示します。 負の相関関係は、x が増加すると y は減少することを示します。|[計算](https://docs.scipy.org/doc/scipy-0.16.1/reference/generated/scipy.stats.spearmanr.html)|なし|
mean_absolute_error|平均絶対誤差は、ターゲットと予測の間における差異の絶対値について予期される値です|[計算](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.mean_absolute_error.html)|なし|
normalized_mean_absolute_error|正規化された平均絶対誤差は、データの範囲で除算した平均絶対誤差です|[計算](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.mean_absolute_error.html)|データの範囲で除算します|
median_absolute_error|中央絶対誤差は、ターゲットと予測の間におけるすべての絶対差の中央値です。 この損失は外れ値に対してロバストです。|[計算](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.median_absolute_error.html)|なし|
normalized_median_absolute_error|正規化された中央絶対誤差は、データの範囲で除算した中央絶対誤差です|[計算](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.median_absolute_error.html)|データの範囲で除算します|
root_mean_squared_error|平均平方二乗誤差は、ターゲットと予測の間における予期される二乗誤差の平方根です|[計算](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.mean_squared_error.html)|なし|
normalized_root_mean_squared_error|正規化された平均平方二乗誤差は、データの範囲で除算した平均平方二乗誤差です|[計算](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.mean_squared_error.html)|データの範囲で除算します|
root_mean_squared_log_error|対数平均平方二乗誤差は、予期される対数二乗誤差の平方根です|[計算](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.mean_squared_log_error.html)|なし|
normalized_root_mean_squared_log_error|正規化された対数平均平方二乗誤差は、データの範囲で除算した対数平均平方二乗誤差です|[計算](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.mean_squared_log_error.html)|データの範囲で除算します|

## <a name="explain-the-model"></a>モデルを説明する

自動機械学習の機能は一般提供されていますが、**モデルの説明機能はまだパブリック プレビュー段階にあります**。

自動機械学習を使用すると、特徴の重要度を把握できます。  トレーニング プロセス中に、モデルのグローバルな特徴の重要度を取得できます。  分類のシナリオでは、クラスレベルの特徴の重要度も取得できます。  特徴の重要度を取得するには、検証データセット (X_valid) を提供する必要があります。

特徴の重要度を生成する方法は 2 つあります。

*   実験の完了後に、任意のイテレーションで `explain_model` メソッドを使用します。

    ```
    from azureml.train.automl.automlexplainer import explain_model
    
    shap_values, expected_values, overall_summary, overall_imp, per_class_summary, per_class_imp = \
        explain_model(fitted_model, X_train, X_test)
    
    #Overall feature importance
    print(overall_imp)
    print(overall_summary) 
    
    #Class-level feature importance
    print(per_class_imp)
    print(per_class_summary) 
    ```

*   すべてのイテレーションに対する特徴の重要度を確認するには、AutoMLConfig で `model_explainability` フラグを `True` に設定します。  

    ```
    automl_config = AutoMLConfig(task = 'classification',
                                 debug_log = 'automl_errors.log',
                                 primary_metric = 'AUC_weighted',
                                 max_time_sec = 12000,
                                 iterations = 10,
                                 verbosity = logging.INFO,
                                 X = X_train, 
                                 y = y_train,
                                 X_valid = X_test,
                                 y_valid = y_test,
                                 model_explainability=True,
                                 path=project_folder)
    ```

    これを実行すると、retrieve_model_explanation メソッドを使用して特定のイテレーションの特徴の重要度を取得できます。

    ```
    from azureml.train.automl.automlexplainer import retrieve_model_explanation
    
    shap_values, expected_values, overall_summary, overall_imp, per_class_summary, per_class_imp = \
        retrieve_model_explanation(best_run)
    
    #Overall feature importance
    print(overall_imp)
    print(overall_summary) 
    
    #Class-level feature importance
    print(per_class_imp)
    print(per_class_summary) 
    ```

Azure portal のワークスペースで、特徴の重要度のグラフを視覚化できます。 このグラフは、ノートブックの Jupyter ウィジェットを使用して表示することもできます。 グラフの詳細については、[Azure ML ノートブックのサンプルに関する記事](samples-notebooks.md)ご覧ください。

```python
from azureml.widgets import RunDetails
RunDetails(local_run).show()
```
![特徴の重要度のグラフ](./media/how-to-configure-auto-train/feature-importance.png)

## <a name="next-steps"></a>次の手順

[モデルをデプロイする方法と場所](how-to-deploy-and-where.md)についてさらに詳しく学習する。

[自動機械学習を利用して分類モデルをトレーニングする方法](tutorial-auto-train-models.md)または[リモート リソースに対して自動機械学習を使用してトレーニングする方法](how-to-auto-train-remote.md)についてさらに詳しく学習する。 
