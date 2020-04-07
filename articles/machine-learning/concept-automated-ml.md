---
title: 自動 ML/AutoML とは
titleSuffix: Azure Machine Learning
description: Azure Machine Learning で、モデルにとって最適なアルゴリズムを選択するために指定するパラメーターと条件を使用することで、どのようにして自動的にアルゴリズムを選択し、それからモデルを生成して時間を節約できるかを説明します。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: jmartens
author: cartacioS
ms.author: sacartac
ms.date: 02/28/2020
ms.openlocfilehash: c8864e00be9f491d87478c253286070b9334a26e
ms.sourcegitcommit: e040ab443f10e975954d41def759b1e9d96cdade
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2020
ms.locfileid: "80383193"
---
# <a name="what-is-automated-machine-learning"></a>自動化された機械学習とは

自動化された機械学習 (自動化された ML とも呼ばれる) は、時間のかかる反復的な機械学習モデルの開発タスクを自動化するプロセスです。 これにより、データ サイエンティスト、アナリスト、開発は、モデルの品質を維持しながら、高いスケール、効率性、生産性で ML モデルを構築することができます。 自動化された ML は、[Microsoft Research 部門](https://arxiv.org/abs/1705.05355)の最先端技術に基づいています。

機械学習モデルの従来の開発はリソース集約型であり、ドメインに関する広範な知識と多数のモデルを生成して比較するための大量の時間を必要とします。 自動化された機械学習を使用することで、すぐに実稼働環境で使用できる ML モデルを取得するための時間を、容易にかつ効率的に短縮することができます。

 


## <a name="when-to-use-automated-ml"></a>自動化された ML をいつ使用するか

指定したターゲット メトリックを使用して自分の代わりに Azure Machine Learning にモデルのトレーニングと調整を行わせる場合は、自動化された ML を適用します。 自動化された ML を使うと、誰でも機械学習モデルの開発プロセスを使用でき、ユーザーはデータ サイエンスの専門知識に関係なく、どの問題についてもエンド ツー エンドの機械学習パイプラインを識別することができます。

さまざまな業界のデータ サイエンティスト、アナリスト、開発者が、自動化された ML を使用して次のことを実行できます。

+ 広範なプログラミング知識なしで機械学習ソリューションを実装する
+ 時間とリソースを節約する
+ データ サイエンスのベスト プラクティスを活用する
+ アジャイルな問題解決を提供する

次の表は、自動化された ML の一般的なユースケースを示しています。 

分類| 時系列予測 | 回帰
---|---|---
[不正行為の検出](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/classification-credit-card-fraud/auto-ml-classification-credit-card-fraud.ipynb)|[売上の予測](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/forecasting-orange-juice-sales/auto-ml-forecasting-orange-juice-sales.ipynb)|[CPU パフォーマンスの予測](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/regression-hardware-performance-explanation-and-featurization/auto-ml-regression-hardware-performance-explanation-and-featurization.ipynb)
|[マーケティングの予測](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/classification-bank-marketing-all-features/auto-ml-classification-bank-marketing-all-features.ipynb)|[需要の予測](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/forecasting-energy-demand/auto-ml-forecasting-energy-demand.ipynb)|
|[ニュースグループ データの分類](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/classification-text-dnn/auto-ml-classification-text-dnn.ipynb)|[飲料生産の予測](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/forecasting-beer-remote/auto-ml-forecasting-beer-remote.ipynb)|

## <a name="design-automated-ml-experiments"></a>自動 ML の実験を設計する

**Azure Machine Learning** を利用するとき、次の手順で自動 ML トレーニング実験を設計し、実験できます。

1. 解決すべき **ML 問題を特定します**。分類、予測、または回帰になります。

1. **ラベルの付いたトレーニング データのソースとフォーマットを指定します**。Numpy 配列または Pandas データフレーム

1. [ローカル コンピューター、Azure Machine Learning コンピューティング、リモート VM、Azure Databricks](how-to-set-up-training-targets.md) など、**モデル トレーニングのためのコンピューティング先を構成します**。  リモート リソースでの自動トレーニングに関する詳細は[こちら](how-to-auto-train-remote.md)にあります。

1. さまざまなモデルでの繰り返しの回数、ハイパーパラメーター設定、前処理/特徴付けの詳細、最良のモデルを決定するときに考慮されるメトリックを決定する**自動化された機械学習のパラメーターを構成します**。  自動トレーニング実験の設定は [Azure Machine Learning Studio](https://ml.azure.com) または [SDK](how-to-configure-auto-train.md) で構成できます。 

    [!INCLUDE [aml-applies-to-enterprise-sku](../../includes/aml-applies-to-enterprise-sku-inline.md)]

1. **トレーニング実行を送信します。**

## <a name="how-automated-ml-works"></a>自動 ML の動作

トレーニング中、Azure Machine Learning は、さまざまなアルゴリズムとパラメーターを試行する多数の並行パイプラインを作成します。 サービスは、機能選択と組み合わせた ML アルゴリズムを介して反復し、それぞれの反復で、トレーニング スコアを含むモデルを生成します。 このスコアが高いほど、モデルがデータに「適合している」と見なされます。  実験に定義されている終了基準に到達すると停止します。 このプロセスを説明する図を次に示します。 

  ![自動化された機械学習](./media/concept-automated-ml/automl-concept-diagram2.png)


ログに記録された実行情報を調べることもできます。これには、実行中に収集した[メトリックが含まれています](how-to-understand-automated-ml.md)。 トレーニングを実行すると、モデルおよびデータ前処理を含む Python シリアル化オブジェクト (`.pkl` ファイル) が生成されます。

モデルの構築は自動ですが、生成されたモデルにとって[特徴がどれだけ重要であるか、または関連性があるか](how-to-configure-auto-train.md#explain)を学ぶこともできます。

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE2Xc9t]

<a name="preprocess"></a>

## <a name="preprocessing"></a>前処理

自動化されたすべての機械学習実験において、データは既定の手法で前処理されます。任意で高度な前処理を利用されることもあります。

> [!NOTE]
> 自動化された機械学習の前処理手順 (機能の正規化、欠損データの処理、テキストから数値への変換など) は、基になるモデルの一部になります。 モデルを予測に使用する場合、トレーニング中に適用されたのと同じ前処理手順が入力データに自動的に適用されます。

### <a name="automatic-preprocessing-standard"></a>自動の前処理 (標準)

自動化されたすべての機械学習実験において、アルゴリズムが十分に実行されるよう、自動的にデータの規模が調整され、正規化されます。  モデル トレーニングの間、次のいずれかのスケーリング手法または正規化手法が各モデルに適用されます。

|スケーリング&nbsp;&&nbsp;正規化| 説明 |
| ------------- | ------------- |
| [StandardScaleWrapper](https://scikit-learn.org/stable/modules/generated/sklearn.preprocessing.StandardScaler.html)  | 中間を取り除き、単位差異に合わせてスケールを変更することで特徴を正規化します  |
| [MinMaxScalar](https://scikit-learn.org/stable/modules/generated/sklearn.preprocessing.MinMaxScaler.html)  | その列の最小と最大で各特徴のスケールを変更することで特徴を変換します  |
| [MaxAbsScaler](https://scikit-learn.org/stable/modules/generated/sklearn.preprocessing.MaxAbsScaler.html#sklearn.preprocessing.MaxAbsScaler) |その最大絶対値で各特徴のスケールを変更します |
| [RobustScalar](https://scikit-learn.org/stable/modules/generated/sklearn.preprocessing.RobustScaler.html) |分位範囲で特徴のスケールを変更します |
| [PCA](https://scikit-learn.org/stable/modules/generated/sklearn.decomposition.PCA.html) |データの特異値分解を利用した線形次元削減により、低いほうの次元空間にデータを投影させます |
| [TruncatedSVDWrapper](https://scikit-learn.org/stable/modules/generated/sklearn.decomposition.TruncatedSVD.html) |このトランスフォーマーは端数を切り捨てた特異値分解 (SVD) によって線形次元削減を実行します。 PCA とは異なり、この推定では、特異値分解を計算する前にデータを中心に置くことはありません。つまり、scipy.sparse マトリックスを効率的に使用できます。 |
| [SparseNormalizer](https://scikit-learn.org/stable/modules/generated/sklearn.preprocessing.Normalizer.html) | その標準 (l1 または l2) が 1 に等しくなるよう、ゼロ以外の要素を少なくとも 1 つ有する各サンプルのスケールが他のサンプルに依存せずに変更されます。 |

### <a name="advanced-preprocessing-optional-featurization"></a>高度な前処理: 任意の特徴付け

データ ガードレール、エンコード、変換など、高度な前処理と特徴付けも追加で利用できます。 含まれる特徴付けに関する詳細は[こちら](how-to-use-automated-ml-for-ml-models.md#featurization)から参照してください。 この設定は次の方法で有効にできます。

+ Azure Machine Learning Studio:[これらの手順に従って](how-to-use-automated-ml-for-ml-models.md#create-and-run-experiment)、 **[View additional configuration]\(追加構成の表示\)** セクションで **[Automatic featurization]\(自動特性付け\)** を有効にします。

+ Python SDK:[`AutoMLConfig` クラス](/python/api/azureml-train-automl-client/azureml.train.automl.automlconfig.automlconfig) に `"feauturization": 'auto' / 'off' / 'FeaturizationConfig'` を指定します。 

## <a name="classification--regression"></a>分類と回帰

分類と回帰は、機械学習タスクのもっとも一般的な種類のタスクです。 どちらも、モデルでトレーニング データを使用して学習を行い、得られた知識を新しいデータに適用する、教師あり学習の一種です。 Azure Machine Learning には、これらのタスクに特化した特徴付けが用意されています (分類用のディープ ニューラル ネットワーク テキスト特徴抽出器など)。 「[特徴付けオプション](how-to-use-automated-ml-for-ml-models.md#featurization)」を参照してください。 

分類モデルの主な目的は、トレーニング データから学習した知識に基づいて、新しいデータがどのカテゴリに分類されるかを予測することです。 一般的な分類の例には、不正行為の検出、手書き認識、オブジェクトの検出などがあります。  [自動機械学習を使用した分類](tutorial-train-models-with-aml.md)に関する記事で、詳細と例を確認してください。

予測される出力値がカテゴリである分類とは異なり、回帰モデルでは、独立した予測子に基づいて数値の出力値が予測されます。 回帰の目的は、1 つの変数が他の変数にどのように影響するかを推定することによって、独立した予測変数間の関係を確立することです。 たとえば、自動車の価格は、燃費効率や安全性の評価などの特徴に基づいています。 [自動機械学習を使用した回帰](tutorial-auto-train-models.md)に関する記事で、詳細と例を確認してください。

## <a name="time-series-forecasting"></a>時系列予測

予測を行うことは、収益、在庫、販売、顧客需要にかかわらず、あらゆるビジネスに不可欠です。 自動化された ML を使用してテクニックとアプローチを組み合わせて、推奨される高品質な時系列予測を得ることができます。

自動化された時系列の実験は、多変量回帰問題として扱われます。 過去の時系列値は "ピボット" されて、他の予測因子とともにリグレッサーの追加ディメンションとなります。 このアプローチには、従来の時系列手法と異なり、トレーニング中に複数のコンテキスト変数とその関係を自然に取り込めるという利点があります。 自動化された ML では、データセットと予測期間内のすべての項目について、単一ではあるがしばしば内部的に分岐するモデルが学習されます。 したがって、モデルのパラメーターを見積もるために多くのデータを使用でき、目に見えない系列の一般化が可能になります。

[時系列予測の自動化された機械学習](how-to-auto-train-forecast.md)について理解を深め、例を確認してください。 または、次などの、高度な予測の構成の詳細なコード例については、[エネルギー需要ノートブック](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/forecasting-energy-demand/auto-ml-forecasting-energy-demand.ipynb)を参照してください。

* 休日の検出と特性付け
* 時系列と DNN 学習 (自動 ARIMA、Prophet、ForecastTCN)
* グループ化による多くのモデルのサポート
* ローリング オリジン クロス検証
* 構成可能なラグ
* ローリング ウィンドウの集計機能

## <a name="ensemble-models"></a><a name="ensemble"></a> アンサンブル モデル

自動化された機械学習では、既定で有効になっているアンサンブル モデルがサポートされています。 アンサンブル学習では、1 つのモデルを使用するのではなく、複数のモデルを組み合わせることによって、機械学習の結果と予測パフォーマンスが改善されます。 アンサンブル イテレーションは、実行の最終イテレーションとして表示されます。 自動化された機械学習では、モデルの結合に投票とスタッキングの両方のアンサンブル方法を使用します。

* **投票**: 予測されたクラス確率 (分類タスクの場合) または予測された回帰ターゲット (回帰タスクの場合) の加重平均に基づいて予測します。
* **スタッキング**: スタッキングは異種のモデルを結合し、個々のモデルの出力に基づいてメタモデルをトレーニングします。 現在の既定のメタモデルは、分類タスクの場合は LogisticRegression、回帰/予測タスクの場合は ElasticNet です。

初期アンサンブルが並べ替えられた [Caruana のアンサンブル選択アルゴリズム](http://www.niculescu-mizil.org/papers/shotgun.icml04.revised.rev2.pdf)を使用して、アンサンブル内で使用するモデルを決定します。 大まかに言えば、このアルゴリズムでは、最適な個別スコアを持つ最大 5 つのモデルを使用してアンサンブルを初期化し、これらのモデルが最適なスコアの 5% のしきい値内にあることを確認して、不適切な初期アンサンブルを回避します。 その後、各アンサンブルの繰り返しに対して、新しいモデルが既存のエンティティに追加され、結果のスコアが計算されます。 新しいモデルによって既存のアンサンブル スコアが向上した場合、アンサンブルはその新しいモデルを含むように変更されます。

自動化された機械学習の既定のアンサンブル設定を変更する方法については、[ハウツー](how-to-configure-auto-train.md#ensemble)を参照してください。

## <a name="use-with-onnx-in-c-apps"></a>C# アプリで ONNX を使用する

Azure Machine Learning では、自動化された ML を使用して Python モデルを構築し、それを ONNX 形式に変換できます。 ONNX は C# に対応しています。そのため、コードを書き直す必要がなく、また、REST エンドポイントで発生するネットワークの遅延なく、C# アプリで自動的に構築されたモデルを使用できます。 [この Jupyter ノートブックで](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/classification-bank-marketing-all-features/auto-ml-classification-bank-marketing-all-features.ipynb)このフローのサンプルをお試しください。

## <a name="automated-ml-in-azure-machine-learning"></a>Azure Machine Learning の自動 ML

Azure Machine Learning には、自動 ML を使用するための 2 つのエクスペリエンスが用意されています。

* コードの経験がある場合は、[Azure Machine Learning Python SDK](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py) に関する記事を参照してください 

* コードの経験があまりない、またはない場合は、Azure Machine Learning Studio ([https://ml.azure.com](https://ml.azure.com/)) に関する記事を参照してください。  

各経験レベルでサポートされる高レベルの自動 ML 機能を以下にまとめます。

<a name="parity"></a>

### <a name="experiment-settings"></a>実験の設定 

次の設定を使用して、自動 ML の実験を構成できます。 

| | Python SDK| studio
----|:----:|:----:
データをトレーニングおよび検証セットに分割する| ✓|✓
ML タスクのサポート: 分類、回帰、予測| ✓| ✓
プライマリ メトリックに基づいて最適化する| ✓| ✓
AML コンピューティングをコンピューティング ターゲットとしてサポートする | ✓|✓
予測期間、ターゲットのラグ、ローリング期間を構成する|✓|✓
終了条件を設定する |✓|✓ 
コンカレント イテレーションを設定する| ✓|✓
列の削除| ✓|✓
ブロック アルゴリズム|✓|✓
クロス検証 |✓|✓
Azure Databricks クラスターでのトレーニングをサポートする| ✓|
エンジニアリング機能の名前を表示する|✓|
特徴付けの概要| ✓|
休日の特徴付け|✓|
ログ ファイルの詳細レベル| ✓|

### <a name="model-settings"></a>モデルの設定

これらの設定は、自動 ML 実験の結果として最適なモデルに適用できます。

||Python SDK|studio
----|:----:|:----:
最適なモデルの登録| ✓|✓
最適なモデル デプロイ| ✓| ✓
最適なモデル説明| ✓|✓
投票アンサンブルとスタック アンサンブル モデルを有効にする| ✓|✓
プライマリ メトリック以外に基づいて最適なモデルを表示する|✓|
ONNX モデルの互換性を有効または無効にする|✓|
モデルのテスト | ✓| |

### <a name="run-control-settings"></a>コントロール設定を実行する

これらの設定を使用すると、実験の実行とその子の実行を確認し、制御することができます。 

||Python SDK| studio
----|:----:|:----:
実行の概要テーブル| ✓|✓
実行の取り消し| ✓|✓
子の実行の取り消し| ✓| ✓
ガードレールを取得する| ✓|✓
実行を一時停止する| ✓| 
実行を再開する| ✓| 

## <a name="next-steps"></a>次のステップ

例を参照して、自動化された機械学習を使用してモデルを構築する方法を学習してください。

+ 次のチュートリアルを修了してください。[チュートリアル:Azure Machine Learning で回帰モデルを自動的にトレーニングする](tutorial-auto-train-models.md)

+ 自動トレーニング実験の設定を構成してください。
  + Azure Machine Learning Studio で、[こちらの手順](how-to-use-automated-ml-for-ml-models.md)を使用します。
  + Python SDK で、[こちらの手順](how-to-configure-auto-train.md)を使用します。

+ [こちらの手順](how-to-auto-train-forecast.md)を使用し、時系列データを使用して自動トレーニングする方法について学習してください。

+ [自動化された機械学習の Jupyter Notebook のサンプル](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/)をお試しください。

* 自動 ML は、[ML.NET](https://docs.microsoft.com/dotnet/machine-learning/automl-overview)、[HDInsight](../hdinsight/spark/apache-spark-run-machine-learning-automl.md)、[Power BI](https://docs.microsoft.com/power-bi/service-machine-learning-automated)、[SQL Server](https://cloudblogs.microsoft.com/sqlserver/2019/01/09/how-to-automate-machine-learning-on-sql-server-2019-big-data-clusters/) などの他の Microsoft ソリューションでも使用できます
