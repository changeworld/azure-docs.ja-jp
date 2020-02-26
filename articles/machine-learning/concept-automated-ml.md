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
ms.date: 11/04/2019
ms.openlocfilehash: 2869384d4f4072e1e71ab0a69af81edc68e7a5b7
ms.sourcegitcommit: f97f086936f2c53f439e12ccace066fca53e8dc3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/15/2020
ms.locfileid: "77366250"
---
# <a name="what-is-automated-machine-learning"></a>自動化された機械学習とは

自動化された機械学習 (自動化された ML とも呼ばれる) は、時間のかかる反復的な機械学習モデルの開発タスクを自動化するプロセスです。 これにより、データ サイエンティスト、アナリスト、開発は、モデルの品質を維持しながら、高いスケール、効率性、生産性で ML モデルを構築することができます。 自動化された ML は、[Microsoft Research 部門](https://arxiv.org/abs/1705.05355)の最先端技術に基づいています。

機械学習モデルの従来の開発はリソース集約型であり、ドメインに関する広範な知識と多数のモデルを生成して比較するための大量の時間を必要とします。 指定したターゲット メトリックを使用して自分の代わりに Azure Machine Learning にモデルのトレーニングと調整を行わせる場合は、自動化された ML を適用します。 それにより、サービスは、機能選択と組み合わせた ML アルゴリズムを介して反復し、それぞれの反復で、トレーニング スコアを含むモデルを生成します。 このスコアが高いほど、モデルがデータに「適合している」と見なされます。

自動化された機械学習を使用することで、すぐに実稼働環境で使用できる ML モデルを取得するための時間を、容易にかつ効率的に短縮することができます。

## <a name="when-to-use-automated-ml"></a>自動化された ML をいつ使用するか

自動化された ML を使うと、誰でも機械学習モデルの開発プロセスを使用でき、ユーザーはデータ サイエンスの専門知識に関係なく、どの問題についてもエンド ツー エンドの機械学習パイプラインを識別することができます。

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

## <a name="how-automated-ml-works"></a>自動 ML の動作

**Azure Machine Learning** を利用するとき、次の手順で自動 ML トレーニング実験を設計し、実験できます。

1. 解決すべき **ML 問題を特定します**。分類、予測、または回帰になります。

1. **ラベルの付いたトレーニング データのソースとフォーマットを指定します**。Numpy 配列または Pandas データフレーム

1. [ローカル コンピューター、Azure Machine Learning コンピューティング、リモート VM、Azure Databricks](how-to-set-up-training-targets.md) など、**モデル トレーニングのためのコンピューティング先を構成します**。  リモート リソースでの自動トレーニングに関する詳細は[こちら](how-to-auto-train-remote.md)にあります。

1. さまざまなモデルでの繰り返しの回数、ハイパーパラメーター設定、前処理/特徴付けの詳細、最良のモデルを決定するときに考慮されるメトリックを決定する**自動化された機械学習のパラメーターを構成します**。  自動トレーニング実験の設定は [Azure Machine Learning Studio](https://ml.azure.com) または [SDK](how-to-configure-auto-train.md) で構成できます。 

    [!INCLUDE [aml-applies-to-enterprise-sku](../../includes/aml-applies-to-enterprise-sku-inline.md)]

1. **トレーニング実行を送信します。**

  ![自動化された機械学習](./media/concept-automated-ml/automl-concept-diagram2.png)

トレーニング中、Azure Machine Learning は、さまざまなアルゴリズムとパラメーターを試行する多数の並行パイプラインを作成します。 実験に定義されている終了基準に到達すると停止します。

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

データ ガードレール、エンコード、変換など、高度な前処理と特徴付けも追加で利用できます。 含まれる特徴付けに関する詳細は[こちら](how-to-create-portal-experiments.md#featurization)から参照してください。 この設定は次の方法で有効にできます。

+ Azure Machine Learning Studio:[これらの手順に従って](how-to-create-portal-experiments.md#create-and-run-experiment)、 **[View additional configuration]\(追加構成の表示\)** セクションで **[Automatic featurization]\(自動特性付け\)** を有効にします。

+ Python SDK:[`AutoMLConfig` クラス](/python/api/azureml-train-automl-client/azureml.train.automl.automlconfig.automlconfig) に `"feauturization": 'auto' / 'off' / 'FeaturizationConfig'` を指定します。 

## <a name="prevent-over-fitting"></a>オーバーフィットを防ぐ

機械学習のオーバーフィットは、モデルがトレーニング データに過剰にフィットする場合に発生します。結果として、未見のテスト データで正確な予測を実行できなくなります。 言い換えると、モデルは、トレーニング データ内の特定のパターンや不要な情報を単に記憶しただけで、実際のデータで予測を行うだけの十分な柔軟性がありません。 最も極端なケースでは、オーバーフィットしたモデルは、トレーニング中に検出された特徴値の組み合わせが、ターゲットのまったく同じ出力に常に生成されると見なします。 

オーバーフィットを防ぐ最も適切な方法は、次のような ML のベスト プラクティスに従うことです。

* より多くのトレーニング データを使用し、統計的偏りを排除する
* ターゲット漏えいを防ぐ
* 使用する機能の削減
* **正則化とハイパーパラメーターの最適化**
* **モデルの複雑さの制限**
* **クロス検証**

自動 ML のコンテキストでは、上記の最初の 3 つの項目は、**ユーザーが実装するベスト プラクティス**です。 最後の 3 つの太字の項目は、オーバーフィットを防ぐために既定で**自動 ML により実装されるベスト プラクティス**です。 自動 ML 以外の設定でも、モデルのオーバーフィットを回避するために、6 つのベスト プラクティスすべてに従うことをお勧めします。

### <a name="best-practices-you-implement"></a>ユーザーが実装するベスト プラクティス

**より多くのデータ**を使用することは、オーバーフィットを防ぐための最も簡単で最も効果的な方法です。また、追加の利点として、通常は精度が向上します。 より多くのデータを使用すると、モデルは正確なパターンを記憶することが困難になり、より多くの条件に対応するより柔軟な解を導き出さざるを得なくなります。 また、**統計的偏り**を認識して、実際の予測データに存在しないような分離パターンがトレーニング データに含まれないようにすることも重要です。 このシナリオは解決するのが困難な場合があります。これは、トレーニング セットとテスト セットの間でオーバーフィットが発生しない可能性がある一方で、実際のテスト データと比較した場合に、オーバーフィットが発生する可能性があるためです。

ターゲット漏えいは同様の問題であり、トレーニング セットとテスト セットの間でオーバーフィットが確認されない一方で、予測時にこれが現れる可能性があります。 ターゲット漏えいは、モデルが予測時には通常使用しないデータにトレーニング中にアクセスすることで、"ずる" を行うと発生します。 たとえば、問題が、金曜日に商品価格がどのようになるかを月曜日に予測することであるときに、いずれかの特徴に木曜日のデータが誤って含まれている場合、それは予測時にモデルが使用しないデータです。未来は調べることができないためです。 ターゲット漏えいは、見逃しやすいミスですが、問題に対して異常に精度が高いという特色が多くの場合発生します。 株価を予測し、95% の精度でモデルをトレーニングした場合、特徴のどこかにターゲット漏えいがある可能性が高くなります。

特徴を削除することが、オーバーフィットで役立つ場合もあります。これは、モデルで特定のパターンを記憶するために使用するフィールドが多くなり過ぎるのを防ぎ、柔軟性を向上させます。 定量的な測定は困難な場合がありますが、特徴を削除しても同じ精度を維持できる場合、モデルの柔軟性が向上し、オーバーフィットのリスクが軽減された可能性が高いと言えます。

### <a name="best-practices-automated-ml-implements"></a>自動 ML により実装されるベスト プラクティス

正則化は、オーバーフィットした複雑なモデルにペナルティーを科すためのコスト関数を最小化するプロセスです。 さまざまな種類の正則化関数がありますが、一般に、これらはすべて、モデルの係数サイズ、分散、および複雑さに対してペナルティーを科します。 自動 ML では、オーバーフィットを制御するさまざまなモデル ハイパーパラメーター設定と共に、L1 (Lasso)、L2 (Ridge)、および ElasticNet (L1 と L2 を同時使用) がさまざまな組み合わせで使用されます。 簡単に言うと、自動 ML は、モデルをどの程度規制するかを変えて、最適な結果を選択します。

自動 ML では、オーバーフィットを防ぐために、明示的なモデルの複雑さの制限も実装されます。 ほとんどの場合、この実装は、特にデシジョン ツリーまたはフォレスト アルゴリズム用です。ここでは、個々のツリーの最大深度が制限され、さらにフォレストまたはアンサンブル手法で使用されるツリーの合計数が制限されます。

クロス検証 (CV) は、全トレーニング データのサブセットを多く取得し、各サブセットでモデルをトレーニングするプロセスです。 これは、モデルは、"運がよい" 場合に、1 つのサブセットで高い精度を実現できる可能性があるが、多くのサブセットを使用することで、モデルはこの高い精度を毎回は達成できないという考え方です。 CV を実行する場合、検証の予約データセットを指定し、CV フォールド (サブセットの数) を指定します。自動 ML は、モデルをトレーニングし、ハイパーパラメーターを調整して検証セットのエラーを最小限に抑えます。 CV フォールドが 1 つでは、オーバーフィットとなる可能性がありますが、それらを多数使用することで、最終的なモデルがオーバーフィットする確率は低下します。 このトレードオフとして、CV ではトレーニング時間が長くなり、コストが高くなります。これは、モデルを一度にトレーニングするのではなく、*n* 個の各 CV サブセットで 1 回ずつトレーニングするためです。

> [!NOTE]
> クロス検証は既定では有効になっていません。自動 ML 設定で構成する必要があります。 ただし、CV を構成し、検証データセットを用意した後は、プロセスは自動化されます。

### <a name="identifying-over-fitting"></a>オーバーフィットの識別

次のトレーニング済みモデルと、それに対応するトレーニングとテストの精度について検討します。

| モデル | トレーニングの精度 | テストの精度 |
|-------|----------------|---------------|
| A | 99.9% | 95% |
| B | 87% | 87% |
| C | 99.9% | 45% |

モデル **A** では、未見のデータに対するテストの精度が、トレーニングの精度よりも低くなっています。この場合に、モデルがオーバーフィットしていると認識するのはよくある誤認です。 ただし、テストの精度は常にトレーニングの精度よりも低くなるべきであり、*どの程度*精度が低いかによって、オーバーフィットしているか、適切にフィットしているか見分けます。 

モデル **A** とモデル **B** を比較した場合、モデル **A** の方が適切なモデルです。これは、テストの精度がより高く、またテストの精度が 95% と少し低めですが、オーバーフィットが発生していることを示す大きな差がないためです。 トレーニングの精度とテストの精度が非常に近いという単純な理由で、モデル **B** は選択しません。

モデル **C** は、オーバーフィットが発生していることを明確に表しています。トレーニングの精度が非常に高い一方で、テストの精度はまったく高くありません。 この見分け方は主観的ですが、問題とデータの知識、および許容できる誤差の大きさから得られるものです。 

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

## <a name="ensemble"></a> アンサンブル モデル

自動化された機械学習では、既定で有効になっているアンサンブル モデルがサポートされています。 アンサンブル学習では、1 つのモデルを使用するのではなく、複数のモデルを組み合わせることによって、機械学習の結果と予測パフォーマンスが改善されます。 アンサンブル イテレーションは、実行の最終イテレーションとして表示されます。 自動化された機械学習では、モデルの結合に投票とスタッキングの両方のアンサンブル方法を使用します。

* **投票**: 予測されたクラス確率 (分類タスクの場合) または予測された回帰ターゲット (回帰タスクの場合) の加重平均に基づいて予測します。
* **スタッキング**: スタッキングは異種のモデルを結合し、個々のモデルの出力に基づいてメタモデルをトレーニングします。 現在の既定のメタモデルは、分類タスクの場合は LogisticRegression、回帰/予測タスクの場合は ElasticNet です。

初期アンサンブルが並べ替えられた [Caruana のアンサンブル選択アルゴリズム](http://www.niculescu-mizil.org/papers/shotgun.icml04.revised.rev2.pdf)を使用して、アンサンブル内で使用するモデルを決定します。 大まかに言えば、このアルゴリズムでは、最適な個別スコアを持つ最大 5 つのモデルを使用してアンサンブルを初期化し、これらのモデルが最適なスコアの 5% のしきい値内にあることを確認して、不適切な初期アンサンブルを回避します。 その後、各アンサンブルの繰り返しに対して、新しいモデルが既存のエンティティに追加され、結果のスコアが計算されます。 新しいモデルによって既存のアンサンブル スコアが向上した場合、アンサンブルはその新しいモデルを含むように変更されます。

自動化された機械学習の既定のアンサンブル設定を変更する方法については、[ハウツー](how-to-configure-auto-train.md#ensemble)を参照してください。

## <a name="imbalance"></a> 偏ったデータ

偏ったデータは、機械学習の分類シナリオのデータでよく見られるもので、各クラスに不適切な観測比率を含むデータを意味します。 この不均衡によって、入力データが 1 つのクラスに偏り、トレーニングされたモデルがその偏りを再現するため、モデルの精度に擬陽性の影響が生じる可能性があります。 

機械学習ワークフローを簡略化する目標の一環として、自動 ML には、偏ったデータを処理するための以下のような機能が組み込まれています。 

- **重み列**: 自動 ML では、重み列が入力としてサポートされているため、データ内の行が重み付けされ、クラスの "重要度" を増減できます。

- 自動 ML によって使用されるアルゴリズムでは、最大 20:1 の不均衡を適切に処理できます。つまり、最も一般的なクラスは、最も一般的でないクラスの 20 倍の行をデータ内に持つことができます。

### <a name="identify-models-with-imbalanced-data"></a>偏ったデータを含むモデルを特定する

分類アルゴリズムは通常、精度によって評価されるため、モデルの精度スコアを確認することは、偏ったデータの影響を受けたかどうかを特定するのに適した方法です。 特定のクラスはとても高精度またはとても低精度でしたか?

さらに、自動 ML を実行すると、次のグラフが自動的に生成されます。これは、モデルの分類の正確性を把握し、偏ったデータの影響を受ける可能性のあるモデルを特定するのに役立ちます。

グラフ| 説明
---|---
[混同行列](how-to-understand-automated-ml.md#confusion-matrix)| 適切に分類されたラベルをデータの実際のラベルと比較して評価します。 
[精度/再現率](how-to-understand-automated-ml.md#precision-recall-chart)| 適切なラベルの比率を、検出されたデータのラベル インスタンスの比率と比較して評価します 
[ROC 曲線](how-to-understand-automated-ml.md#roc)| 適切なラベルの比率を、擬陽性ラベルの比率と比較して評価します。

### <a name="handle-imbalanced-data"></a>偏ったデータの処理 

次の手法は、自動 ML の外部で偏ったデータを処理するための追加のオプションです。 

- より小さいクラスをアップサンプリングするか、より大きいクラスをダウンサンプリングすることで、クラスの偏りを均等にするための再サンプリング。 これらの方法では、処理および分析するための専門知識が必要です。

- 偏ったデータをより適切に処理するパフォーマンス メトリックを使用します。 たとえば、F1 スコアは、精度とリコールの加重平均です。 精度では分類子の正確性が測定され、低精度は擬陽性の数が多いことを示します。リコールでは分類子の完全性が測定されて、低いリコールは擬陰性の数が多いことを示します。 

## <a name="use-with-onnx-in-c-apps"></a>C# アプリで ONNX を使用する

Azure Machine Learning では、自動化された ML を使用して Python モデルを構築し、それを ONNX 形式に変換できます。 ONNX は C# に対応しています。そのため、コードを書き直す必要がなく、また、REST エンドポイントで発生するネットワークの遅延なく、C# アプリで自動的に構築されたモデルを使用できます。 [この Jupyter ノートブックで](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/classification-bank-marketing-all-features/auto-ml-classification-bank-marketing-all-features.ipynb)このフローのサンプルをお試しください。

## <a name="automated-ml-in-azure-machine-learning"></a>Azure Machine Learning の自動 ML

Azure Machine Learning には、自動 ML を使用するための 2 つのエクスペリエンスが用意されています。

* コードの経験がある場合は、[Azure Machine Learning Python SDK](https://docs.microsoft.com/python/api/overview/azureml-sdk/?view=azure-ml-py) に関する記事を参照してください 

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
プライマリ メトリック以外に基づいて最適なモデルを表示する|✓|ONNX モデルの互換性を有効または無効にする|✓|
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
  + Azure Machine Learning Studio で、[こちらの手順](how-to-create-portal-experiments.md)を使用します。
  + Python SDK で、[こちらの手順](how-to-configure-auto-train.md)を使用します。

+ [こちらの手順](how-to-auto-train-forecast.md)を使用し、時系列データを使用して自動トレーニングする方法について学習してください。

+ [自動化された機械学習の Jupyter Notebook のサンプル](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/)をお試しください。

* 自動 ML は、[ML.NET](https://docs.microsoft.com/dotnet/machine-learning/automl-overview)、[HDInsight](../hdinsight/spark/apache-spark-run-machine-learning-automl.md)、[Power BI](https://docs.microsoft.com/power-bi/service-machine-learning-automated)、[SQL Server](https://cloudblogs.microsoft.com/sqlserver/2019/01/09/how-to-automate-machine-learning-on-sql-server-2019-big-data-clusters/) などの他の Microsoft ソリューションでも使用できます
