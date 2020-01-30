---
title: 'デザイナー: 自動車価格の予測 (詳細) の例'
titleSuffix: Azure Machine Learning
description: Azure Machine Learning デザイナーを使用して技術的な機能に基づいて自動車の価格を予測するために、複数の ML 回帰モデルを構築および比較します。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
author: likebupt
ms.author: keli19
ms.reviewer: peterlu
ms.date: 12/25/2019
ms.openlocfilehash: b2511feefbe419223f355509ecb435518ce8382e
ms.sourcegitcommit: a9b1f7d5111cb07e3462973eb607ff1e512bc407
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/22/2020
ms.locfileid: "76311049"
---
# <a name="train--compare-multiple-regression-models-to-predict-car-prices-with-azure-machine-learning-designer"></a>Azure Machine Learning デザイナーを使用して自動車価格を予測するために、複数の回帰モデルをトレーニングおよび比較する

**デザイナー サンプル 2**

[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-enterprise-sku.md)]

コードを 1 行も書くことなく、デザイナーを使用して、機械学習のパイプラインを構築する方法について説明します。 このサンプルでは、技術的な特徴に基づいて自動車の価格を予測するために、複数の回帰モデルをトレーニングし、比較します。 ご自身で機械学習の問題に取り組むことができるように、ここではこのパイプラインで行われた選択の理論的な根拠を提示します。

機械学習を始めたばかりの場合は、このパイプラインの[基本的なバージョン](how-to-designer-sample-regression-automobile-price-basic.md)を参照してください。

このパイプラインの完成したグラフを次に示します。

[![パイプラインのグラフ](./media/how-to-designer-sample-regression-automobile-price-compare-algorithms/graph.png)](./media/how-to-designer-sample-regression-automobile-price-compare-algorithms/graph.png#lightbox)

## <a name="prerequisites"></a>前提条件

[!INCLUDE [aml-ui-prereq](../../includes/aml-ui-prereq.md)]

4. サンプル 2 をクリックして開きます。 

## <a name="pipeline-summary"></a>パイプラインの概要

機械学習のパイプラインを構築するには、次のステップを使用します。

1. データを取得する。
1. データを前処理する。
1. モデルをトレーニングする。
1. モデルをテスト、評価、比較する。

## <a name="get-the-data"></a>データを取得する

このサンプルでは、UCI Machine Learning Repository から取得された **Automobile price data (Raw)** データセットを使用します。 このデータセットには、メーカー、モデル、価格、車両特徴 (シリンダー数など)、MPG、保険リスク スコアなど、自動車に関する情報を含む 26 列が含まれています。

## <a name="pre-process-the-data"></a>データを前処理する

主なデータ準備タスクには、データのクリーニング、統合、変換、削減、および離散化または量子化が含まれます。 デザイナーには、左側のパネルの **[Data Transformation]\(データ変換\)** グループに、これらの操作およびその他のデータ前処理タスクを実行するためのモジュールがあります。

**データセット内の列の選択**モジュールを使用して、欠損値が多い正規化された損失を除外します。 次に、 **[Clean Missing Data]\(欠損データのクリーニング\)** を使用して、欠損値がある行を削除します。 これにより、クリーンなトレーニング データセットを作成できます。

![データの前処理](./media/how-to-designer-sample-regression-automobile-price-compare-algorithms/data-processing.png)

## <a name="train-the-model"></a>モデルをトレーニングする

機械学習の問題はさまざまです。 一般的な機械学習タスクには、分類、クラスタリング、回帰、推奨システムなどがあり、それぞれに異なるアルゴリズムが必要になる場合があります。 アルゴリズムの選択は、多くの場合、ユース ケースの要件によって異なります。 アルゴリズムを選択したら、より正確なモデルをトレーニングするためにそのパラメーターを調整する必要があります。 次に、精度、明瞭度、効率性などのメトリックに基づいて、すべてのモデルを評価する必要があります。

このパイプラインの目的は自動車の価格を予測することであり、またラベル列 (価格) には実数が含まれるため、回帰モデルを選択することをお勧めします。 特徴の数が比較的少なく (100 未満)、これらの特徴が疎でないことを考慮すると、決定境界は非線形である可能性があります。

異なるアルゴリズムのパフォーマンスを比較するために、**Boosted Decision Tree Regression** と **Decision Forest Regression** の 2 つの非線形アルゴリズムを使用してモデルを構築します。 どちらのアルゴリズムにも変更可能なパラメーターがありますが、このサンプルではこのパイプラインの既定値を使用します。

**データの分割**モジュールを使用して、トレーニング データセットが元のデータの 70% を含み、テスト データセットが元のデータの 30% を含むように、入力データをランダムに分割します。

## <a name="test-evaluate-and-compare-the-models"></a>モデルをテスト、評価、比較する

前のセクションで説明したように、ランダムに選択した 2 つの異なるデータセットを使用してモデルをトレーニングし、テストします。 モデルの評価をより客観的に行うために、データセットを分割し、異なるデータセットを使用してモデルをトレーニングおよびテストします。

モデルのトレーニングが終わったら、**モデルのスコア付け**モジュールと**モデルの評価**モジュールを使用して予測結果を生成し、モデルを評価します。 **モデルのスコア付け**は、トレーニングされたモデルを使用してテスト データセットの予測を生成します。 次に、そのスコアを **Evaluate Model (モデルの評価)** に渡して評価メトリックを生成します。



結果は次のようになります。

![結果を比較する](./media/how-to-designer-sample-regression-automobile-price-compare-algorithms/result.png)

これらの結果は、**Boosted Decision Tree Regression** で構築したモデルが、**Decision Forest Regression** で構築したモデルよりも二乗平均平方根誤差が小さいことを示しています。



## <a name="clean-up-resources"></a>リソースをクリーンアップする

[!INCLUDE [aml-ui-cleanup](../../includes/aml-ui-cleanup.md)]

## <a name="next-steps"></a>次のステップ

デザイナーで利用できる他のサンプルを確認します。

- [サンプル 1 - 回帰: 自動車の価格を予測する](how-to-designer-sample-regression-automobile-price-basic.md)
- [サンプル 3 -特徴選択による分類:収入予測](how-to-designer-sample-classification-predict-income.md)
- [サンプル 4 - 分類: 信用リスクを予測する (費用重視)](how-to-designer-sample-classification-credit-risk-cost-sensitive.md)
- [サンプル 5 - 分類:顧客離れを予測する](how-to-designer-sample-classification-churn.md)
- [サンプル 6 - 分類:フライトの遅延を予測する](how-to-designer-sample-classification-flight-delay.md)
- [サンプル 7 - テキスト分類:Wikipedia SP 500 データセット](how-to-designer-sample-text-classification.md)
