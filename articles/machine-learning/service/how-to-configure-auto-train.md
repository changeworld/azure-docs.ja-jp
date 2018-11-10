---
title: 自動機械学習の実験を構成する - Azure Machine Learning
description: 自動機械学習は、アルゴリズムを自動的に選択して、デプロイできる状態のモデルを生成します。 自動機械学習の実験の構成に使用できるオプションについて説明します。
author: nacharya1
ms.author: nilesha
ms.reviewer: sgilley
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: conceptual
ms.date: 09/24/2018
ms.openlocfilehash: 1aeb1315cfafbcdf3507a6e49d71e1f1e69b537c
ms.sourcegitcommit: 707bb4016e365723bc4ce59f32f3713edd387b39
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/19/2018
ms.locfileid: "49430189"
---
# <a name="configure-your-automated-machine-learning-experiment"></a>自動機械学習の実験を構成する

自動機械学習 (自動 ML) は、アルゴリズムとハイパーパラメーターを自動的に選択して、デプロイできる状態のモデルを生成します。 モデルをダウンロードしてさらにカスタマイズすることもできます。 自動 ML の実験の構成に使用できるオプションがいくつかあります。 このガイドでは、さまざまな構成設定を定義する方法について説明します。

自動 ML のサンプルを見るには、「[チュートリアル: 自動機械学習で分類モデルをトレーニングする](tutorial-auto-train-models.md)」または「[クラウドで自動機械学習を使用してモデルをトレーニングする](how-to-auto-train-remote.md)」をご覧ください。

自動機械学習で使用できる構成オプション:

* 実験の種類を選択する (分類や回帰など) 
* データ ソース、形式、およびデータのフェッチ
* コンピューティング ターゲットを選択する (ローカルまたはリモート)
* 自動 ML の実験の設定
* 自動 ML の実験を実行する
* モデル メトリックを探索する
* モデルを登録して展開する

## <a name="select-your-experiment-type"></a>実験の種類を選択する
実験を始める前に、解決する機械学習の問題の種類を決める必要があります。 自動 ML では、教師あり学習のカテゴリとして分類と回帰の 2 つがサポートされています。 自動 ML では、自動化とチューニング プロセスの間に、次のアルゴリズムがサポートされています。 ユーザーは、アルゴリズムを指定する必要はありません。
分類 | 回帰
--|--
sklearn.linear_model.LogisticRegression | sklearn.linear_model.ElasticNet
sklearn.linear_model.SGDClassifier  | sklearn.ensemble.GradientBoostingRegressor
sklearn.naive_bayes.BernoulliNB | sklearn.tree.DecisionTreeRegressor
sklearn.naive_bayes.MultinomialNB | sklearn.neighbors.KNeighborsRegressor
sklearn.svm.SVC | sklearn.linear_model.LassoLars
sklearn.svm.LinearSVC | sklearn.linear_model.SGDRegressor
sklearn.calibration.CalibratedClassifierCV |    sklearn.ensemble.RandomForestRegressor
sklearn.neighbors.KNeighborsClassifier |    sklearn.ensemble.ExtraTreesRegressor
sklearn.tree.DecisionTreeClassifier |   lightgbm.LGBMRegressor
sklearn.ensemble.RandomForestClassifier |
sklearn.ensemble.ExtraTreesClassifier   |
sklearn.ensemble.GradientBoostingClassifier |
lightgbm.LGBMClassifier |


## <a name="data-source-and-format"></a>データ ソースと形式
自動 ML は、ローカル デスクトップ上またはクラウドの Azure Blob Storage 内に存在するデータをサポートします。 データは、scikit-learn でサポートされているデータ形式に読み込むことができます。 1) Numpy 配列 X (機能) および y (ターゲット変数またはラベルとも呼ばれます) または 2) Pandas データフレームにデータを読み取ることができます。 

次に例を示します。

1.  Numpy 配列

    ```python
    digits = datasets.load_digits()
    X_digits = digits.data 
    y_digits = digits.target
    ```

2.  Pandas データフレーム

    ```python
    Import pandas as pd
    df = pd.read_csv("https://automldemods.blob.core.windows.net/datasets/PlayaEvents2016,_1.6MB,_3.4k-rows.cleaned.2.tsv", delimiter="\t", quotechar='"') 
    # get integer labels 
    le = LabelEncoder() 
    le.fit(df["Label"].values) 
    y = le.transform(df["Label"].values) 
    df = df.drop(["Label"], axis=1) 
    df_train, _, y_train, _ = train_test_split(df, y, test_size=0.1, random_state=42)
    ```

## <a name="fetch-data-for-running-experiment-on-remote-compute"></a>リモート コンピューティング上で実行している実験にデータをフェッチする

リモート コンピューティングを使用して実験を実行している場合、データのフェッチを別の Python スクリプト `get_data()` でラップする必要があります。 このスクリプトは、自動 ML の実験が実行されているリモート コンピューティングで実行されます。 `get_data` を使用すると、各イテレーションに対してネットワーク経由でデータをフェッチする必要がなくなります。 `get_data` を使用しないと、リモート コンピューティングで実行している場合は実験が失敗します。

`get_data` の例を以下に示します。

```python
%%writefile $project_folder/get_data.py 
import pandas as pd 
from sklearn.model_selection 
import train_test_split 
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

## <a name="train-and-validation-data"></a>データをトレーニングして検証する

get_data() を使用して、または `AutoMLConfig` メソッドで直接、個別のトレーニングと検証セットを指定できます。

## <a name="cross-validation-split-options"></a>クロス検証の分割のオプション

### <a name="k-folds-cross-validation"></a>K フォールド クロス検証

`n_cross_validations` の設定を使用して、クロス検証の数を指定します。 トレーニング データ セットは、等しいサイズの `n_cross_validations` 個のフォールドにランダムに分割されます。 各クロス検証ラウンドの間に、フォールドの 1 つが、残りのフォールドでトレーニングされたモデルの検証に使用されます。 各フォールドが検証セットとして 1 回使用されるまで、このプロセスが `n_cross_validations` ラウンドに対して繰り返されます。 最後に、すべての `n_cross_validations` ラウンドの平均スコアが報告されて、対応するモデルがトレーニング データ セットが全体で再トレーニングされます。

### <a name="monte-carlo-cross-validation-aka-repeated-random-sub-sampling"></a>モンテカルロ クロス検証 (別名: 反復ランダム サブサンプリング)

`validation_size` を使用して検証に使用する必要があるトレーニング データセットの割合を指定し、`n_cross_validations` を使用してクロス検証の数を指定します。 各クロス検証ラウンドの間に、サイズ `validation_size` のサブセットが、残りのデータでトレーニングされたモデルの検証にランダムに選択されます。 最後に、すべての `n_cross_validations` ラウンドの平均スコアが報告されて、対応するモデルがトレーニング データ セットが全体で再トレーニングされます。

### <a name="custom-validation-dataset"></a>カスタム検証データセット

ランダムな分割が受け入れられない場合は (通常は、時系列データまたは不均衡なデータ)、カスタム検証データセットを使用します。 これでは、独自の検証データセットを指定できます。 モデルは、ランダムなデータセットではなく、指定された検証データセットに対して評価されます。

## <a name="compute-to-run-experiment"></a>実験を実行するために計算する

次に、モデルをトレーニングする場所を決定します。 自動 ML トレーニング実験は、ユーザーが所有および管理しているコンピューティング ターゲットで実行されます。 

サポートされているコンピューティング オプションは次のとおりです。
1.  ローカル デスクトップやラップトップなどのローカル コンピューター – 一般に、データセットが小さく、まだ探索ステージにいる場合。
2.  クラウド内のリモート マシン – Linux を実行している [Azure Data Science Virtual Machine](https://azure.microsoft.com/services/virtual-machines/data-science-virtual-machines/) – データセットが大きく、Azure クラウドで使用可能な大きいマシンにスケールアップする場合。 
3.  Azure Batch AI クラスター – スケールアウトし、自動 ML イテレーションを並列で実行するように設定できるマネージド クラスター。 

<a name='configure-experiment'/>
## <a name="configure-your-experiment-settings"></a>実験の設定を構成する

自動 ML の実験の構成に使用できるノブがいくつかあります。 これらのパラメーターは、`AutoMLConfig` オブジェクトをインスタンス化することによって設定します。

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
`task`  |機械学習の問題の種類を指定します。 次の値を使用できます <li>分類</li><li>回帰</li>    | なし |
`primary_metric` |モデルの構築で最適化したいメトリック。 たとえば、primary_metric として精度を指定すると、自動 ML は最大精度のモデルを探します。 実験ごとに指定できる primary_metric は 1 つのみです。 次の値を使用できます <br/>**分類**:<br/><li> accuracy  </li><li> AUC_weighted</li><li> precision_score_weighted </li><li> balanced_accuracy </li><li> average_precision_score_weighted </li><br/>**回帰**: <br/><li> normalized_mean_absolute_error </li><li> spearman_correlation </li><li> normalized_root_mean_squared_error </li><li> normalized_root_mean_squared_log_error</li><li> R2_score    </li> | 分類の場合: accuracy <br/>回帰の場合: spearman_correlation <br/> |
`exit_score` |  primary_metric に対するターゲット値を設定できます。 primary_metric ターゲットを満たすモデルが見つかった後、自動 ML は繰り返しを停止し、実験は終了します。 この値が設定されていない場合 (既定)、自動 ML の実験はイテレーションで指定されている回数だけ実行を続けます。 double 値を受け取ります。 ターゲットに達しない場合は、自動 ML はイテレーションで指定されている回数になるまで続行されます。|   なし
`iterations` |イテレーションの最大数。 各イテレーションは、パイプラインになるトレーニング ジョブと同じです。 パイプラインは、データの前処理とモデルです。 高品質のモデルを得るには、250 以上を使用します | 100
`Concurrent_iterations`|    並列で実行するイテレーションの最大数。 この設定は、リモート コンピューティングに対してのみ機能します。|    1
`max_cores_per_iteration`   | 1 つのパイプラインのトレーニングに使用されるコンピューティング ターゲット上のコアの数を示します。 アルゴリズムが複数のコアを使用できる場合は、これによりマルチコア マシンでのパフォーマンスが向上します。 -1 に設定すると、マシンで利用可能なすべてのコアを使用できます。|  1
`max_time_sec` |    特定のイテレーションにかかる時間 (秒数) を制限します。 イテレーションが指定した時間を超えると、そのイテレーションはキャンセルされます。 設定しないと、イテレーションは完了するまで実行を続けます。 |   なし
`n_cross_validations`   |クロス検証の分割の数| なし
`validation_size`   |すべてのトレーニング サンプルの割合として設定する検証のサイズ。|  なし
`preprocess` | True または False <br/>True を指定すると、実験は入力に対して前処理を実行できます。 前処理のサブセットを次に示します<li>不足データ: 不足データの入力 - 平均での番号、ほとんどの発生でのテキスト </li><li>カテゴリ値: データ型が数値で一意数の数が 5% 未満の場合、ワンホット エンコードに変換します </li><li>その他。完全なリストについては [GitHub リポジトリ](https://aka.ms/aml-notebooks)を確認</li><br/>注: データが疎の場合は、preprocess = true を使用できません |  False | 
`blacklist_algos`   | 自動 ML の実験には、試すさまざまなアルゴリズムがあります。 特定のアルゴリズムを実験から除外するように自動 ML を構成します。 アルゴリズムがデータセットに対してうまく動作しないことがわかっている場合に役立ちます。 アルゴリズムを除外すると、コンピューティング リソースとトレーニング時間を節約できます。<br/>分類に使用できる値<br/><li>ロジスティック回帰</li><li>SGD 分類子</li><li>MultinomialNB</li><li>BernoulliNB</li><li>SVM</li><li>LinearSVM</li><li>kNN</li><li>DT</li><li>RF</li><li>エクストラ ツリー</li><li>勾配ブースティング</li><li>lgbm_classifier</li><br/>回帰に使用できる値<br/><li>エラスティック ネット</li><li>勾配ブースティング リグレッサー</li><li>DT リグレッサー</li><li>kNN リグレッサー</li><li>Lasso lars</li><li>SGD リグレッサー</li><li>RF リグレッサー</li><li>エクストラ ツリー リグレッサー</li>|   なし
`verbosity` |ログのレベルを制御します。INFO は最も詳細で、CRITICAL は最小限です。<br/>使用できる値は、以下のとおりです。<br/><li>logging.INFO</li><li>logging.WARNING</li><li>logging.ERROR</li><li>logging.CRITICAL</li>  | logging.INFO</li> 
`X` | トレーニングするすべての機能 |  なし
`y` |   トレーニングするラベル データ。 分類の場合、整数の配列にする必要があります。|  なし
`X_valid`|"_省略可能_" 検証するすべての機能。 指定しない場合、X はトレーニング間で分割されて検証されます |   なし
`y_valid`   |"_省略可能_" 検証するラベル データ。 指定しない場合、y はトレーニング間で分割されて検証されます    | なし
`sample_weight` |   "_省略可能_" 各サンプルの重み値。 データ ポイントに異なる重みを割り当てる場合に使用します |   なし
`sample_weight_valid`   |   "_省略可能_" 各検証サンプルの重み値。 指定しない場合、sample_weight はトレーニング間で分割されて検証されます   | なし
`run_configuration` |   RunConfiguration オブジェクト。  リモート実行に使用されます。 |なし
`data_script`  |    get_data メソッドを含むファイルへのパス。  リモート実行に必要です。   |なし


## <a name="run-experiment"></a>実験を実行する

次に、実験の実行を開始して、モデルを生成できます。 モデルを生成するには、`AutoMLConfig` を `submit` メソッドに渡します。

```python
run = experiment.submit(automl_config, show_output=True)
```

>[!NOTE]
>最初に依存関係が新しい DSVM にインストールされます。  出力が表示されるまで、最大で 10 分間かかる場合があります。
>`show_output` を True に設定すると、コンソールに出力が表示されます。


## <a name="explore-model-metrics"></a>モデル メトリックを探索する
結果をウィジェットで、またはノートブックの場合はインラインで、表示できます。 詳細を確認して "モデルを追跡および評価" します。 (AML のコンテンツに自動 ML の関連情報が含まれていることを確認します)

各イテレーションで、次のメトリックが保存されます。
* AUC_macro
* AUC_micro
* AUC_weighted
* accuracy
* average_precision_score_macro
* average_precision_score_micro
* average_precision_score_weighted
* balanced_accuracy
* f1_score_macro
* f1_score_micro
* f1_score_weighted
* log_loss
* norm_macro_recall
* precision_score_macro
* precision_score_micro
* precision_score_weighted
* recall_score_macro
* recall_score_micro
* recall_score_weighted
* weighted_accuracy

## <a name="next-steps"></a>次の手順

[モデルをデプロイする方法と場所](how-to-deploy-and-where.md)についてさらに詳しく学習する。

[自動 ML で分類モデルをトレーニングする方法](tutorial-auto-train-models.md)または[リモート リソースで自動 ML を使用してトレーニングする方法](how-to-auto-train-remote.md)についてさらに詳しく学習する。 