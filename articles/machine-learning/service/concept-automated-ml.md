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
ms.date: 05/02/2019
ms.custom: seodec18
ms.openlocfilehash: 136141f5b598fd080edf3254fd01200f2742c763
ms.sourcegitcommit: 2ce4f275bc45ef1fb061932634ac0cf04183f181
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/07/2019
ms.locfileid: "65235835"
---
# <a name="what-is-automated-machine-learning"></a>自動化された機械学習とは

自動化された機械学習は AutoML とも呼ばれていますが、これをデータ サイエンティスト、アナリスト、開発者が使用すると、モデルの品質を維持しながら、高いスケール、効率性、生産性で ML モデルを構築できます。 

自動 ML では、トレーニングのためのモデルがインテリジェントに選択された上で一連の ML モデルが自動的に構築され、最良のモデルが利用者に推奨されます。 機械学習モデルの従来の開発では、リソースを大量に使用し、ドメインに関する広範な知識と大量のモデルを生成して比較するための時間を必要とします。 自動 ML は効率性に優れ、本稼働に対応できる ML モデルを短期間で構築できます。

自動 ML の裏側では、定義済みの対象の特徴がトレーニング データとともに取得され、ML アルゴリズムと特徴選択の組み合わせを利用してインテリジェントに反復処理されます。 次に、トレーニング スコアに基づき、最適なモデルが特定され、利用者に推奨されます。 

利用者は実験を制御することができ、実験の様子を把握することもできます。 時間、精度、反復の数などに基づいて制約と実験の目標を決定できます。 実験で生成された各モデル、反復ごとのトレーニング フロー、特定のモデルで最も影響力のある特徴を確認できます。

## <a name="how-automated-ml-works"></a>自動 ML の動作

**Azure Machine Learning service** を利用するとき、次の手順で自動 ML トレーニング実験を設計し、実験できます。

1. 解決すべき **ML 問題を特定します**。分類、予測、または回帰になります。
   
1. **ラベルの付いたトレーニング データのソースとフォーマットを指定します**。Numpy 配列または Pandas データフレーム

1. [ローカル コンピューター、Azure Machine Learning コンピューティング、リモート VM、Azure Databricks](how-to-set-up-training-targets.md) など、**モデル トレーニングのためのコンピューティング先を構成します**。  リモート リソースでの自動トレーニングに関する詳細は[こちら](how-to-auto-train-remote.md)にあります。

1. さまざまなモデルでの繰り返しの回数、ハイパーパラメーター設定、前処理/特徴付けの詳細、最良のモデルを決定するときに考慮されるメトリックを決定する**自動化された機械学習のパラメーターを構成します**。  自動トレーニング実験の設定は [Azure portal](how-to-create-portal-experiments.md) または [SDK](how-to-configure-auto-train.md) で構成できます。

1. **トレーニング実行を送信します。** 


[![自動化された機械学習](./media/how-to-automated-ml/automated-machine-learning.png)](./media/how-to-automated-ml/automated-machine-learning.png#lightbox)

トレーニング中、Azure Machine Learning サービスは、さまざまなアルゴリズムとパラメーターを試行する多数のパイプラインを作成します。 実験に定義されている終了基準に到達すると停止します。 

ログに記録された実行情報を調べることもできます。これには、実行中に収集したメトリックが含まれています。 トレーニングを実行すると、モデルおよびデータ前処理を含む Python シリアル化オブジェクト (`.pkl` ファイル) が生成されます。


モデルの構築は自動ですが、生成されたモデルにとって[特徴がどれだけ重要であるか、または関連性があるか](how-to-configure-auto-train.md#explain)を学ぶこともできます。 

> [!VIDEO https://www.youtube.com/embed/l8c-4iDPE0M]

<a name="preprocess"></a>

## <a name="preprocessing"></a>前処理

自動化されたすべての機械学習実験において、データは既定の手法で前処理されます。任意で高度な前処理を利用されることもあります。

### <a name="automatic-preprocessing-standard"></a>自動の前処理 (標準)
自動化されたすべての機械学習実験において、アルゴリズムが十分に実行されるよう、自動的にデータの規模が調整され、正規化されます。  モデル トレーニングの間、次のいずれかのスケーリング手法または正規化手法が各モデルに適用されます。

|スケーリング&nbsp;&&nbsp;正規化| 説明 |
| ------------- | ------------- |
| [StandardScaleWrapper](https://scikit-learn.org/stable/modules/generated/sklearn.preprocessing.StandardScaler.html)  | 中間を取り除き、単位差異に合わせてスケールを変更することで特徴を正規化します  |
| [MinMaxScalar](https://scikit-learn.org/stable/modules/generated/sklearn.preprocessing.MinMaxScaler.html)  | その列の最小と最大で各特徴のスケールを変更することで特徴を変換します  |
| [MaxAbsScaler](https://scikit-learn.org/stable/modules/generated/sklearn.preprocessing.MaxAbsScaler.html#sklearn.preprocessing.MaxAbsScaler) |    その最大絶対値で各特徴のスケールを変更します |  
| [RobustScalar](https://scikit-learn.org/stable/modules/generated/sklearn.preprocessing.RobustScaler.html) |   分位範囲で特徴のスケールを変更します |
| [PCA](https://scikit-learn.org/stable/modules/generated/sklearn.decomposition.PCA.html) | データの特異値分解を利用した線形次元削減により、低いほうの次元空間にデータを投影させます | 
| [TruncatedSVDWrapper](https://scikit-learn.org/stable/modules/generated/sklearn.decomposition.TruncatedSVD.html) |    このトランスフォーマーは端数を切り捨てた特異値分解 (SVD) によって線形次元削減を実行します。 PCA とは異なり、この推定では、特異値分解を計算する前にデータを中心に置くことはありません。 つまり、scipy.sparse マトリックスを効率的に使用できます。 | 
| [SparseNormalizer](https://scikit-learn.org/stable/modules/generated/sklearn.preprocessing.Normalizer.html) | その標準 (l1 または l2) が 1 に等しくなるよう、ゼロ以外の要素を少なくとも 1 つ有する各サンプルのスケールが他のサンプルに依存せずに変更されます。 | 

### <a name="advanced-preprocessing-optional-featurization"></a>高度な前処理: 任意の特徴付け

不足値の代入、エンコード、変換など、高度な前処理と特徴付けも追加で利用できます。 含まれる特徴付けに関する詳細は[こちら](how-to-create-portal-experiments.md#preprocess)から参照してください。 この設定は次の方法で有効にできます。
+ Azure portal:[こちらの手順で](how-to-create-portal-experiments.md) **[詳細設定]** の **[前処理]** チェックボックスを選択します。 
+ Python SDK:[`AutoMLConfig` クラス](https://docs.microsoft.com/python/api/azureml-train-automl/azureml.train.automl.automlconfig?view=azure-ml-py) に `"preprocess": True` を指定します。

## <a name="ensemble-models"></a>アンサンブル モデル

自動化された機械学習を使用するとき、[並び変えられたアンサンブルの初期化を含む Caruana アンサンブル選択アルゴリズム](http://www.niculescu-mizil.org/papers/shotgun.icml04.revised.rev2.pdf)を使用してアンサンブル モデルをトレーニングできます。 アンサンブル学習では、1 つのモデルを使用するのではなく、多くのモデルを組み合わせることによって、機械学習の結果と予測パフォーマンスが改善されます。 アンサンブル イテレーションは、実行の最後のイテレーションとして表示されます。

## <a name="use-with-onnx-in-c-apps"></a>C# アプリで ONNX を使用する

Azure Machine Learning では、自動化された ML を使用して Python モデルを構築し、それを ONNX 形式に変換できます。 ONNX は C# に対応しています。そのため、コードを書き直す必要がなく、また、REST エンドポイントで発生するネットワークの遅延なく、C# アプリで自動的に構築されたモデルを使用できます。 [この Jupyter ノートブックで](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/classification-with-onnx/auto-ml-classification-with-onnx.ipynb)このフローのサンプルをお試しください。

## <a name="automated-ml-across-microsoft"></a>Microsoft ソリューション全体で自動化された ML

自動化された ML は次のような他の Microsoft ソリューションでも利用できます。
+ Visual Studio、Visual Studio Code、[ML.NET](https://docs.microsoft.com/dotnet/machine-learning/automl-overview) を使用した .NET アプリで
+ 並列にしてある HDInsight クラスターの Spark で自動化された ML トレーニング ジョブをスケールアウトする [HDInsight](../../hdinsight/spark/apache-spark-run-machine-learning-automl.md) で 
+ [Power BI で](https://docs.microsoft.com/power-bi/service-machine-learning-automated)

## <a name="next-steps"></a>次の手順

例を参照して、Automated Machine Learning を使用してモデルを構築する方法を学習してください。

+ 次のチュートリアルを修了してください。[チュートリアル:Azure Automated Machine Learning で分類モデルを自動的にトレーニングする](tutorial-auto-train-models.md)

+ 自動トレーニング実験の設定を構成してください。 
   + Azure portal インターフェイスで、[こちらの手順](how-to-create-portal-experiments.md)を利用します。
   + Python SDK で、[こちらの手順](how-to-configure-auto-train.md)を使用します。
  
 + [こちらの手順](how-to-auto-train-forecast.md)を使用し、時系列データを使用して自動トレーニングする方法について学習してください。

+ [Jupyter Notebook のサンプル](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/)をお試しください。
