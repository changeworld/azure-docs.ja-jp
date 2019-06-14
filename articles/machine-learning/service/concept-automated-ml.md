---
title: 自動化された ML アルゴリズムの選択とチューニング
titleSuffix: Azure Machine Learning service
description: Azure Machine Learning service で、モデルにとって最適なアルゴリズムを選択するために指定するパラメーターと条件を使用することで、どのようにして自動的にアルゴリズムを選択し、それからモデルを生成して時間を節約できるかを説明します。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: jmartens
author: nacharya1
ms.author: nilesha
ms.date: 05/21/2019
ms.custom: seodec18
ms.openlocfilehash: 93eb0fba91ce5064d04a340e8b3e5b984ee73081
ms.sourcegitcommit: adb6c981eba06f3b258b697251d7f87489a5da33
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/04/2019
ms.locfileid: "66515566"
---
# <a name="what-is-automated-machine-learning"></a>自動化された機械学習とは

自動化された機械学習 (自動化された ML とも呼ばれる) は、時間のかかる反復的な機械学習モデルの開発タスクを自動化するプロセスです。 これにより、データ サイエンティスト、アナリスト、開発は、モデルの品質を維持しながら、高いスケール、効率性、生産性で ML モデルを構築することができます。

機械学習モデルの従来の開発はリソース集約型であり、ドメインに関する広範な知識と多数のモデルを生成して比較するための大量の時間を必要とします。 指定したターゲット メトリックを使用して自分の代わりに Azure Machine Learning にモデルのトレーニングと調整を行わせる場合は、自動化された ML を適用します。 それにより、サービスは、機能選択と組み合わせた ML アルゴリズムを介して反復し、それぞれの反復で、トレーニング スコアを含むモデルを生成します。 このスコアが高いほど、モデルがデータに「適合している」と見なされます。

自動化された機械学習を使用することで、すぐに実稼働環境で使用できる ML モデルを取得するための時間を、容易にかつ効率的に短縮することができます。

## <a name="when-to-use-automated-ml"></a>自動化された ML をいつ使用するか

自動化された ML を使うと、誰でも機械学習モデルの開発プロセスを使用でき、ユーザーはデータ サイエンスの専門知識に関係なく、どの問題についてもエンド ツー エンドの機械学習パイプラインを識別することができます。

さまざまな業界にわたるデータ サイエンティスト、アナリスト、および開発者は、自動化された ML を使用して次のことを実行できます。

+ 広範なプログラミング知識なしで機械学習ソリューションを実装する
+ 時間とリソースを節約する
+ データ サイエンスのベスト プラクティスを活用する
+ アジャイルな問題解決を提供する

## <a name="how-automated-ml-works"></a>自動 ML の動作

**Azure Machine Learning service** を利用するとき、次の手順で自動 ML トレーニング実験を設計し、実験できます。

1. 解決すべき **ML 問題を特定します**。分類、予測、または回帰になります。

1. **ラベルの付いたトレーニング データのソースとフォーマットを指定します**。Numpy 配列または Pandas データフレーム

1. [ローカル コンピューター、Azure Machine Learning コンピューティング、リモート VM、Azure Databricks](how-to-set-up-training-targets.md) など、**モデル トレーニングのためのコンピューティング先を構成します**。  リモート リソースでの自動トレーニングに関する詳細は[こちら](how-to-auto-train-remote.md)にあります。

1. さまざまなモデルでの繰り返しの回数、ハイパーパラメーター設定、前処理/特徴付けの詳細、最良のモデルを決定するときに考慮されるメトリックを決定する**自動化された機械学習のパラメーターを構成します**。  自動トレーニング実験の設定は [Azure portal](how-to-create-portal-experiments.md) または [SDK](how-to-configure-auto-train.md) で構成できます。

1. **トレーニング実行を送信します。**

  ![自動化された機械学習](./media/how-to-automated-ml/automl-concept-diagram2.png)

トレーニング中、Azure Machine Learning サービスは、さまざまなアルゴリズムとパラメーターを試行する多数の並行パイプラインを作成します。 実験に定義されている終了基準に到達すると停止します。

ログに記録された実行情報を調べることもできます。これには、実行中に収集したメトリックが含まれています。 トレーニングを実行すると、モデルおよびデータ前処理を含む Python シリアル化オブジェクト (`.pkl` ファイル) が生成されます。

モデルの構築は自動ですが、生成されたモデルにとって[特徴がどれだけ重要であるか、または関連性があるか](how-to-configure-auto-train.md#explain)を学ぶこともできます。

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE2Xc9t]

<a name="preprocess"></a>

## <a name="preprocessing"></a>前処理

自動化されたすべての機械学習実験において、データは既定の手法で前処理されます。任意で高度な前処理を利用されることもあります。

### <a name="automatic-preprocessing-standard"></a>自動の前処理 (標準)

自動化されたすべての機械学習実験において、アルゴリズムが十分に実行されるよう、自動的にデータの規模が調整され、正規化されます。  モデル トレーニングの間、次のいずれかのスケーリング手法または正規化手法が各モデルに適用されます。

|スケーリング&nbsp;&&nbsp;正規化| 説明 |
| ------------- | ------------- |
| [StandardScaleWrapper](https://scikit-learn.org/stable/modules/generated/sklearn.preprocessing.StandardScaler.html)  | 中間を取り除き、単位差異に合わせてスケールを変更することで特徴を正規化します  |
| [MinMaxScalar](https://scikit-learn.org/stable/modules/generated/sklearn.preprocessing.MinMaxScaler.html)  | その列の最小と最大で各特徴のスケールを変更することで特徴を変換します  |
| [MaxAbsScaler](https://scikit-learn.org/stable/modules/generated/sklearn.preprocessing.MaxAbsScaler.html#sklearn.preprocessing.MaxAbsScaler) |その最大絶対値で各特徴のスケールを変更します |
| [RobustScalar](https://scikit-learn.org/stable/modules/generated/sklearn.preprocessing.RobustScaler.html) |分位範囲で特徴のスケールを変更します |
| [PCA](https://scikit-learn.org/stable/modules/generated/sklearn.decomposition.PCA.html) |データの特異値分解を利用した線形次元削減により、低いほうの次元空間にデータを投影させます |
| [TruncatedSVDWrapper](https://scikit-learn.org/stable/modules/generated/sklearn.decomposition.TruncatedSVD.html) |このトランスフォーマーは端数を切り捨てた特異値分解 (SVD) によって線形次元削減を実行します。 PCA とは異なり、この推定では、特異値分解を計算する前にデータを中心に置くことはありません。 つまり、scipy.sparse マトリックスを効率的に使用できます。 |
| [SparseNormalizer](https://scikit-learn.org/stable/modules/generated/sklearn.preprocessing.Normalizer.html) | 少なくとも 1 つのゼロ以外のコンポーネントを持つ各サンプル (つまり、データ マトリックスの各行) は、その標準 (l1 または l2) が 1 に等しくなるように、他のサンプルとは関係なく再スケーリングされます。 |

### <a name="advanced-preprocessing-optional-featurization"></a>高度な前処理: 任意の特徴付け

不足値の代入、エンコード、変換など、高度な前処理と特徴付けも追加で利用できます。 含まれる特徴付けに関する詳細は[こちら](how-to-create-portal-experiments.md#preprocess)から参照してください。 この設定は次の方法で有効にできます。

+ Azure portal:[こちらの手順で](how-to-create-portal-experiments.md) **[詳細設定]** の **[前処理]** チェックボックスを選択します。

+ Python SDK:[`AutoMLConfig` クラス](https://docs.microsoft.com/python/api/azureml-train-automl/azureml.train.automl.automlconfig?view=azure-ml-py) に `"preprocess": True` を指定します。

## <a name="ensemble-models"></a>アンサンブル モデル

自動化された機械学習を使用するとき、[並び変えられたアンサンブルの初期化を含む Caruana アンサンブル選択アルゴリズム](http://www.niculescu-mizil.org/papers/shotgun.icml04.revised.rev2.pdf)を使用してアンサンブル モデルをトレーニングできます。 アンサンブル学習では、1 つのモデルを使用するのではなく、多くのモデルを組み合わせることによって、機械学習の結果と予測パフォーマンスが改善されます。 アンサンブル イテレーションは、実行の最後のイテレーションとして表示されます。

## <a name="training-metric-output"></a>メトリック出力のトレーニング

実行イテレーションごとのトレーニング精度メトリックを表示する方法は複数あります。

* Jupyter ウィジェットを使用する。
* 任意の `Run` オブジェクト上で `get_metrics()` 関数を使用する。
* 実験で、Azure portal にメトリックを表示する。

### <a name="classification-metrics"></a>分類メトリック

分類タスクの実行イテレーションごとに、次のメトリックが保存されます。

|メトリック|説明|計算|追加のパラメーター
--|--|--|--|
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
norm_macro_recall|正規化されたマクロ再現率は、ランダムなパフォーマンスでのスコアが 0、最適なパフォーマンスでのスコアが 1 になるように正規化されたマクロ再現率です。 これは、norm_macro_recall := (recall_score_macro - R)/(1 - R) で実現されます。R はランダム予測の recall_score_macro で期待される値 (つまり、二項分類では R=0.5、C クラス分類問題では R=(1/C))|[計算](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.recall_score.html)|average = "macro"。次に (recall_score_macro - R)/(1 - R)。R はランダム予測の recall_score_macro で期待される値 (つまり、二項分類では R=0.5、C クラス分類問題では R=(1/C))|
precision_score_macro|適合率は、あるクラスとしてラベル付けされ、実際にそのクラスに存在する要素の割合です。 Macro は、各クラスの適合率の算術平均です|[計算](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.precision_score.html)|average="macro"|
precision_score_micro|適合率は、あるクラスとしてラベル付けされ、実際にそのクラスに存在する要素の割合です。 Micro は、真陽性と偽陽性の合計をカウントすることによって、グローバルに計算されます|[計算](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.precision_score.html)|average="micro"|
precision_score_weighted|適合率は、あるクラスとしてラベル付けされ、実際にそのクラスに存在する要素の割合です。 重み付けは各クラスの適合率の算術平均で、各クラス内の true インスタンスの数によって重み付けされます|[計算](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.precision_score.html)|average="weighted"|
recall_score_macro|再現率は、あるクラスに実際に存在し、正しくラベル付けされている要素の割合です。 Macro は、各クラスの再現率の算術平均です|[計算](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.recall_score.html)|average="macro"|
recall_score_micro|再現率は、あるクラスに実際に存在し、正しくラベル付けされている要素の割合です。 Micro は、真陽性と偽陰性の合計をカウントすることによって、グローバルに計算されます|[計算](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.recall_score.html)|average="micro"|
recall_score_weighted|再現率は、あるクラスに実際に存在し、正しくラベル付けされている要素の割合です。 重み付けは各クラスの再現率の算術平均で、各クラス内の true インスタンスの数によって重み付けされます|[計算](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.recall_score.html)|average="weighted"|
weighted_accuracy|重み付けされた精度は、それぞれの例に対して指定された重みが、その例の true クラス内の true インスタンスの割合に一致する精度です|[計算](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.accuracy_score.html)|sample_weight は、ターゲット内の各要素に対して、そのクラスの割合が等しいベクターです|

### <a name="regression-and-forecasting-metrics"></a>回帰メトリックと予測メトリック

回帰タスクまたは予測タスクの実行イテレーションごとに、次のメトリックが保存されます。

|メトリック|説明|計算|追加のパラメーター
--|--|--|--|
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


## <a name="use-with-onnx-in-c-apps"></a>C# アプリで ONNX を使用する

Azure Machine Learning では、自動化された ML を使用して Python モデルを構築し、それを ONNX 形式に変換できます。 ONNX は C# に対応しています。そのため、コードを書き直す必要がなく、また、REST エンドポイントで発生するネットワークの遅延なく、C# アプリで自動的に構築されたモデルを使用できます。 [この Jupyter ノートブックで](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/classification-with-onnx/auto-ml-classification-with-onnx.ipynb)このフローのサンプルをお試しください。

## <a name="automated-ml-across-microsoft"></a>Microsoft ソリューション全体で自動化された ML

自動化された ML は次のような他の Microsoft ソリューションでも利用できます。

|統合|説明|
|------------|-----------|
|[ML.NET](https://docs.microsoft.com/dotnet/machine-learning/automl-overview)|ML.NET 自動 ML とともに Visual Studio および Visual Studio Code を使用した .NET アプリでの自動モデル選択およびトレーニング (プレビュー)。|
|[HDIsnight](../../hdinsight/spark/apache-spark-run-machine-learning-automl.md)|並列にしてある HDInsight クラスターの Spark で自動化された ML トレーニング ジョブをスケールアウトします。|
|[PowerBI](https://docs.microsoft.com/power-bi/service-machine-learning-automated)|直接 Power BI で機械学習モデルを呼び出します (プレビュー)。|

## <a name="next-steps"></a>次の手順

例を参照して、Automated Machine Learning を使用してモデルを構築する方法を学習してください。

+ 次のチュートリアルを修了してください。[チュートリアル:Azure Automated Machine Learning で分類モデルを自動的にトレーニングする](tutorial-auto-train-models.md)

+ 自動トレーニング実験の設定を構成してください。
  + Azure portal インターフェイスで、[こちらの手順](how-to-create-portal-experiments.md)を利用します。
  + Python SDK で、[こちらの手順](how-to-configure-auto-train.md)を使用します。

+ [こちらの手順](how-to-auto-train-forecast.md)を使用し、時系列データを使用して自動トレーニングする方法について学習してください。

+ [Jupyter Notebook のサンプル](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/)をお試しください。
