---
title: 'デザイナー: 顧客離れの予測の例'
titleSuffix: Azure Machine Learning
description: Azure Machine Learning デザイナーとブースト デシジョン ツリーを使用して顧客離れを予測するには、次の分類の例に従います。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
author: xiaoharper
ms.author: zhanxia
ms.reviewer: sgilley
ms.date: 11/04/2019
ms.openlocfilehash: 1fe3598fd15424ab2593e3d236146c7566493743
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/20/2019
ms.locfileid: "74225100"
---
# <a name="use-boosted-decision-tree-to-predict-churn-with-azure-machine-learning-designer"></a>Azure Machine Learning デザイナーでブースト デシジョン ツリーを使用して顧客離れを予測する

**デザイナー (プレビュー) のサンプル 5**

[!INCLUDE [applies-to-skus](../../../includes/aml-applies-to-enterprise-sku.md)]

コードを 1 行も書くことなく、デザイナー (プレビュー) を使用して、複雑な機械学習のパイプラインを構築する方法について説明します。

このパイプラインでは、顧客関係管理 (CRM) システムの一般的なタスクである顧客離れを予測するために、2 つの **2 クラス ブースト デシジョン ツリー**分類子をトレーニングします。 データ値とラベルは複数のデータ ソースに分割され、顧客情報を匿名化するためにスクランブルがかけられますが、それでもデザイナーを使用してデータ セットを組み合わせ、隠された値を使用してモデルをトレーニングすることができます。

なぜなら、「どれにするか」という質問に答えようとしているからです。 これは分類問題と呼ばれていますが、このサンプルに示した同じ理論を適用して、回帰、分類、クラスタリングなど、あらゆる種類の機械学習問題に対処することができます。

このパイプラインの完成したグラフを次に示します。

![パイプラインのグラフ](./media/how-to-designer-sample-classification-predict-churn/pipeline-graph.png)

## <a name="prerequisites"></a>前提条件

[!INCLUDE [aml-ui-prereq](../../../includes/aml-ui-prereq.md)]

4. サンプル 5 をクリックして開きます。 

## <a name="data"></a>Data

このパイプライン用のデータは、KDD Cup 2009 のものです。 データには、50,000 行と 230 の特徴列が含まれます。 タスクは、これらの特徴を使用する顧客の顧客離れ、強い欲求、アップセルを予測することです。 データとタスクについて詳細については、[KDD の Web サイト](https://www.kdd.org/kdd-cup/view/kdd-cup-2009)を参照してください。

## <a name="pipeline-summary"></a>パイプラインの概要

デザイナーのこのサンプル パイプラインは、顧客関係管理 (CRM) の一般的なタスクである顧客離れ、強い欲求、アップセルのバイナリ分類器予測を示します。

最初に、簡単なデータ処理がいくつかあります。

- 生のデータセットには、多くの欠損値が含まれています。 **見つからないデータのクリーンアップ** モジュールを使用して、欠損値を 0 に置換します。

    ![データセットをクリーンアップする](./media/how-to-designer-sample-classification-predict-churn/cleaned-dataset.png)

- 特徴と、それに対応する顧客離れは、異なるデータセットに含まれます。 **列の追加**モジュールを使用して、ラベル列を特徴列に追加します。 最初の列 **Col1** はラベル列です。 視覚化の結果から、データセットが不均衡であることがわかります。 正 (+ 1) の例よりも、負 (-1) の例の方がずっと多くあります。 後で **SMOTE** モジュールを使用して、少ししか存在しないケースを増やします。

    ![列のデータセットを追加する](./media/how-to-designer-sample-classification-predict-churn/added-column1.png)



- **データの分割**モジュールを使用して、データセットをトレーニング セットとテスト セットに分割します。

- その後、ブースト デシジョン ツリー バイナリ分類器と既定のパラメーターを使用して、予測モデルを構築します。 タスクごとに 1 つのモデル、つまりアップセル、強い欲求、顧客離れの予測ごとに 1 つのモデルを構築します。

- パイプラインの右側の部分で **SMOTE** モジュールを使用して、正の例の割合を増やします。 SMOTE 率を 100 に設定して、正の例を 2 倍にします。 SMOTE モジュールのしくみについて詳しくは、[SMOTE モジュールのリファレンス 0](../././algorithm-module-reference/SMOTE.md) を参照してください。

## <a name="results"></a>結果

**モデルの評価**モジュールの出力を視覚化し、テスト セットでのモデルのパフォーマンスを確認します。 

![結果を評価](./media/how-to-designer-sample-classification-predict-churn/evaluate-result.png)

 **[しきい値]** スライダーを動かして、バイナリ分類器タスクのメトリックの変化を確認できます。 

## <a name="clean-up-resources"></a>リソースのクリーンアップ

[!INCLUDE [aml-ui-cleanup](../../../includes/aml-ui-cleanup.md)]

## <a name="next-steps"></a>次の手順

デザイナーで利用できる他のサンプルを確認します。

- [サンプル 1 - 回帰: 自動車の価格を予測する](how-to-designer-sample-regression-automobile-price-basic.md)
- [サンプル 2 - 回帰: 自動車の価格予測のためのアルゴリズムを比較する](how-to-designer-sample-regression-automobile-price-compare-algorithms.md)
- [サンプル 3 -特徴選択による分類: 収入予測](how-to-designer-sample-classification-predict-income.md)
- [サンプル 4 - 分類: 信用リスクを予測する (費用重視)](how-to-designer-sample-classification-credit-risk-cost-sensitive.md)
- [サンプル 6 - 分類:フライトの遅延を予測する](how-to-designer-sample-classification-flight-delay.md)
- [サンプル 7 - テキスト分類:Wikipedia SP 500 データセット](how-to-designer-sample-text-classification.md)
