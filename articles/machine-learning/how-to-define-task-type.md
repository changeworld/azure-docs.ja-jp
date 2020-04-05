---
title: 自動機械学習の実行のための機械学習タスクを定義する
titleSuffix: Azure Machine Learning
description: 自動機械学習の実行のための機械学習タスクを定義する方法を説明します。
services: machine-learning
author: RachelKellam
ms.author: rakellam
ms.reviewer: nibaccam
ms.service: machine-learning
ms.subservice: core
ms.topic: how-to
ms.date: 11/04/2019
ms.openlocfilehash: a58959904559cd3b2cec27762f2df93cfea72abd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/27/2020
ms.locfileid: "75535323"
---
# <a name="how-to-define-a-machine-learning-task"></a>機械学習タスクを定義する方法 
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

この記事では、サポートされている機械学習タスクと、それらを自動機械学習 (自動 ML) の実験の実行用に定義する方法について説明します。


## <a name="what-is-a-machine-learning-task"></a><a name="What is a machine learning task?"></a> 機械学習タスクとは

機械学習タスクは、予測モデルを作成することによって解決される問題の種類を表します。 自動 ML では、分類、回帰、時系列予測を含む 3 つの異なる種類のタスクがサポートされています。

タスクの種類| 説明| 例
----|----|----
分類 | データセット内の特定の行のカテゴリを予測するタスク。 | クレジットカードでの不正行為の検出。 ターゲット列は **[Fraud Detected]\(不正の検出\)** で、*True* または *False* のカテゴリが使用されます。 この例では、データの各行を true または false に分類します。
回帰 | 連続する数量の出力を予測するタスク。 | 自動車の特徴に基づく自動車のコストで、ターゲット列は **[price]\(価格\)** になります。
予測 |将来の傾向の方向性を決定する際に、情報に基づいた推定を行うタスク。| 今後 48 時間のエネルギー需要を予測します。 ターゲット列は **[demand]\(需要\)** で、予測値を使用してエネルギー需要のパターンを示します。

自動 ML では、自動化とチューニング プロセスの間に、次のアルゴリズムがサポートされています。 ユーザーは、アルゴリズムを指定する必要はありません。

分類 | 回帰 | 時系列予測
-- |-- |--
[ロジスティック回帰](https://scikit-learn.org/stable/modules/linear_model.html#logistic-regression)| [Elastic Net](https://scikit-learn.org/stable/modules/linear_model.html#elastic-net)| [Elastic Net](https://scikit-learn.org/stable/modules/linear_model.html#elastic-net)
[Light GBM](https://lightgbm.readthedocs.io/en/latest/index.html)|[Light GBM](https://lightgbm.readthedocs.io/en/latest/index.html)|[Light GBM](https://lightgbm.readthedocs.io/en/latest/index.html)
[勾配ブースティング](https://scikit-learn.org/stable/modules/ensemble.html#classification)|[勾配ブースティング](https://scikit-learn.org/stable/modules/ensemble.html#regression)|[勾配ブースティング](https://scikit-learn.org/stable/modules/ensemble.html#regression)
[デシジョン ツリー](https://scikit-learn.org/stable/modules/tree.html#decision-trees)|[デシジョン ツリー](https://scikit-learn.org/stable/modules/tree.html#regression)|[デシジョン ツリー](https://scikit-learn.org/stable/modules/tree.html#regression)
[K ニアレスト ネイバー](https://scikit-learn.org/stable/modules/neighbors.html#nearest-neighbors-regression)|[K ニアレスト ネイバー](https://scikit-learn.org/stable/modules/neighbors.html#nearest-neighbors-regression)|[K ニアレスト ネイバー](https://scikit-learn.org/stable/modules/neighbors.html#nearest-neighbors-regression)
[Linear SVC](https://scikit-learn.org/stable/modules/svm.html#classification)|[LARS Lasso](https://scikit-learn.org/stable/modules/linear_model.html#lars-lasso)|[LARS Lasso](https://scikit-learn.org/stable/modules/linear_model.html#lars-lasso)
[C のサポート ベクター分類 (SVC)](https://scikit-learn.org/stable/modules/svm.html#classification)|[確率的勾配降下法 (SGD)](https://scikit-learn.org/stable/modules/sgd.html#regression)|[確率的勾配降下法 (SGD)](https://scikit-learn.org/stable/modules/sgd.html#regression)
[ランダム フォレスト](https://scikit-learn.org/stable/modules/ensemble.html#random-forests)|[ランダム フォレスト](https://scikit-learn.org/stable/modules/ensemble.html#random-forests)|[ランダム フォレスト](https://scikit-learn.org/stable/modules/ensemble.html#random-forests)
[Extremely Randomized Trees](https://scikit-learn.org/stable/modules/ensemble.html#extremely-randomized-trees)|[Extremely Randomized Trees](https://scikit-learn.org/stable/modules/ensemble.html#extremely-randomized-trees)|[Extremely Randomized Trees](https://scikit-learn.org/stable/modules/ensemble.html#extremely-randomized-trees)
[Xgboost](https://xgboost.readthedocs.io/en/latest/parameter.html)|[Xgboost](https://xgboost.readthedocs.io/en/latest/parameter.html)| [Xgboost](https://xgboost.readthedocs.io/en/latest/parameter.html)
[DNN 分類子](https://www.tensorflow.org/api_docs/python/tf/estimator/DNNClassifier)|[DNN リグレッサー](https://www.tensorflow.org/api_docs/python/tf/estimator/DNNRegressor) | [DNN リグレッサー](https://www.tensorflow.org/api_docs/python/tf/estimator/DNNRegressor)|
[DNN 線形分類子](https://www.tensorflow.org/api_docs/python/tf/estimator/LinearClassifier)|[線形リグレッサー](https://www.tensorflow.org/api_docs/python/tf/estimator/LinearRegressor)|[線形リグレッサー](https://www.tensorflow.org/api_docs/python/tf/estimator/LinearRegressor)
[単純ベイズ](https://scikit-learn.org/stable/modules/naive_bayes.html#bernoulli-naive-bayes)||
[確率的勾配降下法 (SGD)](https://scikit-learn.org/stable/modules/sgd.html#sgd)||


### <a name="set-the-task-type"></a>タスクの種類を設定する
SDK または Azure Machine Learning Studio を使用して、自動 ML 実験のタスクの種類を設定できます。

`task` コンストラクターで `AutoMLConfig` パラメーターを使用して、実験の種類を指定します。

```python
from azureml.train.automl import AutoMLConfig

# task can be one of classification, regression, forecasting
automl_config = AutoMLConfig(task="classification")
```

このタスクは、Azure Machine Learning Studio での自動 ML 実験実行の作成の一部として設定できます。 

[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-enterprise-sku-inline.md)]

![タスクの種類の選択](./media/how-to-define-task-type/task-type.png)


## <a name="next-steps"></a>次のステップ

+ Azure Machine Learning の[自動機械学習](concept-automated-ml.md)について学習します。
+ Azure Machine Learning の[時系列予測モデルの自動トレーニング](how-to-auto-train-forecast.md)について学習します。
+ [自動機械学習分類](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/automated-machine-learning/model-explanation)のチュートリアルを試します。
+ [自動機械学習回帰](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/automated-machine-learning/model-explanation)のサンプル ノートブックを試します。

