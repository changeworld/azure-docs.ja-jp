---
title: 'デザイナー: 収入の分類と予測の例'
titleSuffix: Azure Machine Learning
description: この例では、Azure Machine Learning デザイナーを使用して、コードを書かずに分類子を構築し、収入を予測します。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
author: xiaoharper
ms.author: zhanxia
ms.reviewer: peterlu
ms.date: 11/04/2019
ms.openlocfilehash: adc7712a4f41daea9ed691e6df52290e98e8d81f
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/20/2019
ms.locfileid: "74214133"
---
# <a name="build-a-classifier--use-feature-selection-to-predict-income-with-azure-machine-learning-designer"></a>Azure Machine Learning デザイナーで、分類子を作成し、特徴選択を使用して、収入を予測する

**デザイナー (プレビュー) サンプル 3**

[!INCLUDE [applies-to-skus](../../../includes/aml-applies-to-enterprise-sku.md)]

コードを 1 行も書くことなく、デザイナー (プレビュー) を使用して、機械学習の分類器を構築する方法について説明します。 このサンプルでは、**2 クラス ブースト デシジョン ツリー**をトレーニングして、国勢調査の成人収入 (>= 50,000 または <= 50,000) を予測します。

なぜなら、質問では「どれにするか」ということに答えようとしているからです。 これは分類問題と呼ばれます。 ただし、同じ基本的なプロセスを適用して、回帰、分類、クラスタリングなど、あらゆる種類の機械学習問題に対処することができます。

このサンプルの最終的なパイプラインのグラフは次のようになります。

![パイプラインのグラフ](media/how-to-designer-sample-classification-predict-income/overall-graph.png)

## <a name="prerequisites"></a>前提条件

[!INCLUDE [aml-ui-prereq](../../../includes/aml-ui-prereq.md)]

4. サンプル 3 をクリックして開きます。



## <a name="data"></a>Data

データセットには、14 個の特徴と 1 つのラベル列が含まれています。 特徴には、数値やカテゴリなどの複数の種類があります。 次の図は、データセット ![data](media/how-to-designer-sample-classification-predict-income/data.png) からの抜粋を示しています



## <a name="pipeline-summary"></a>パイプラインの概要

次の手順に従ってパイプラインを作成します。

1. 国勢調査の成人収入に関するバイナリ データセット モジュールをパイプライン キャンバスにドラッグします。
1. トレーニング セットとテスト セットを作成するために、**データの分割**モジュールを追加します。 [最初の出力データセットにおける列の割合] を 0.7 に設定します。 この設定は、データの 70% がモジュールの左側のポートに出力され、残りが右側のポートに出力されることを指定します。 左側のデータセットをトレーニングに使用し、右側のデータセットをテストに使用します。
1. **フィルターに基づく特徴選択**モジュールを追加して、PearsonCorreclation により 5 つの特徴を選択します。 
1. ブースト デシジョン ツリー分類器を初期化するために、**2 クラス ブースト デシジョン ツリー** モジュールを追加します。
1. **モデルのトレーニング** モジュールを追加します。 前の手順の分類器を**モデルのトレーニング**の左側の入力ポートに接続します。 フィルターに基づく特徴選択モジュールからのフィルター処理されたデータセットを、トレーニング データセットとして接続します。  **モデルのトレーニング**が分類器をトレーニングします。
1. 列変換の選択と変換の適用モジュールを追加して、テスト データセットに同じ変換 (フィルターに基づく特徴選択) を適用します。
![apply-transformation](media/how-to-designer-sample-classification-predict-income/transformation.png)
1. **モデルのスコア付け**モジュールを追加し、それに**モデルのトレーニング** モジュールを接続します。 次に、テストセット (特徴選択をテストセットに適用する、変換の適用モジュールの出力) を**モデルのスコア付け**に追加します。 **モデルのスコア付け**で予測が作成されます。 その出力ポートを選択すると、予測と正のクラス確率が表示されます。


    このパイプラインには 2 つのスコア付けモジュールがあり、右側の 1 つのモジュールでは、予測を行う前にラベル列が除外されています。 これにより、Web サービスの入力ではラベル以外の特徴のみが想定されるため、リアルタイム エンドポイントをデプロイする準備ができます。 

1. **モデルの評価**モジュールを追加し、スコア付けされたデータセットをその左側の入力ポートに接続します。 評価結果を表示するには、**モデルの評価**モジュールの出力ポートを選択して、 **[視覚化]** を選択します。

## <a name="results"></a>結果

![結果を評価](media/how-to-designer-sample-classification-predict-income/evaluate-result.png)

評価結果では、ROC や精度/再現率などの曲線、および混同行列が表示されます。 

## <a name="clean-up-resources"></a>リソースのクリーンアップ

[!INCLUDE [aml-ui-cleanup](../../../includes/aml-ui-cleanup.md)]

## <a name="next-steps"></a>次の手順

デザイナーで利用できる他のサンプルを確認します。

- [サンプル 1 - 回帰: 自動車の価格を予測する](how-to-designer-sample-regression-automobile-price-basic.md)
- [サンプル 2 - 回帰: 自動車の価格予測のためのアルゴリズムを比較する](how-to-designer-sample-regression-automobile-price-compare-algorithms.md)
- [サンプル 4 - 分類:信用リスクを予測する (費用重視)](how-to-designer-sample-classification-credit-risk-cost-sensitive.md)
- [サンプル 5 - 分類:顧客離れを予測する](how-to-designer-sample-classification-churn.md)
- [サンプル 6 - 分類:フライトの遅延を予測する](how-to-designer-sample-classification-flight-delay.md)
- [サンプル 7 - テキスト分類:Wikipedia SP 500 データセット](how-to-designer-sample-text-classification.md)
