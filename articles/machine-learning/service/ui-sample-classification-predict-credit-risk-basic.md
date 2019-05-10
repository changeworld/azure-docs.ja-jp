---
title: 分類:信用リスクの予測
titleSuffix: Azure Machine Learning service
description: このビジュアル インターフェイスのサンプル実験では、与信取引申請書で提供された情報に基づいて信用リスクを予測する二項分類の実行方法を示します。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: article
author: xiaoharper
ms.author: zhanxia
ms.reviewer: sgilley
ms.date: 05/02/2019
ms.openlocfilehash: 3d4ec3c71aaed6bddb012fb17ee5bb96da00cd76
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/02/2019
ms.locfileid: "65027697"
---
# <a name="sample-3---classification-predict-credit-risk"></a>サンプル 3 - 分類:信用リスクの予測

このビジュアル インターフェイスのサンプル実験では、与信取引申請書で提供された情報に基づいて信用リスクを予測する二項分類の実行方法を示します。 データ処理操作などの基本的分類の実行、トレーニング セットとテスト セットへのデータセットの分割、モデルのトレーニング、テスト データセットのスコア付け、および予測の評価を行う方法を示します。

## <a name="prerequisites"></a>前提条件

[!INCLUDE [aml-ui-prereq](../../../includes/aml-ui-prereq.md)]

4. サンプル 3 実験用の **[開く]** ボタンを選択します。

    ![実験を開く](media/ui-sample-classification-predict-credit-risk-basic/open-sample3.png)

## <a name="related-sample"></a>関連サンプル

[サンプル 4 - 分類:信用リスクの予測 (コスト重視)](ui-sample-classification-predict-credit-risk-cost-sensitive.md) は、この実験と同じ問題を解決する高度な実験を提供します。 **Python スクリプトの実行**モジュールを使用して_コスト重視_の分類を実行し、二項分類アルゴリズムのパフォーマンスを比較する方法を示します。 分類実験の構築方法について詳しく知りたい場合に参照してください。

## <a name="data"></a>データ

UC Irvine リポジトリの German Credit Card のデータセットを使用します。
このデータセットには、20 個のフィーチャーと 1 個のラベルを含む 1,000 個のサンプルが含まれています。 各サンプルは個人を表します。 フィーチャーには、数値とカテゴリのフィーチャーが含まれています。 カテゴリー フィーチャーの意味については、[UCI Web サイト](https://archive.ics.uci.edu/ml/datasets/Statlog+%28German+Credit+Data%29)を参照してください。 最後の列は信用リスクを表すラベルで、使用できるのは次の 2 つの値だけです: 高信用リスク = 2、および低信用リスク = 1。

## <a name="experiment-summary"></a>実験の概要


次の手順に従って実験を作成します。

1. German Credit Card UCI Data データセット モジュールを実験のキャンバスにドラッグします。
1. 列ごとに意味のある名前を追加できるように、**メタデータの編集**モジュールを追加します。
1. トレーニング セットとテスト セットを作成するために、**データの分割**モジュールを追加します。 [最初の出力データセットにおける列の割合] を 0.7 に設定します。 この設定は、データの 70% がモジュールの左側のポートに出力され、残りが右側のポートに出力されることを指定します。 左側のデータセットをトレーニングに使用し、右側のデータセットをテストに使用します。
1. ブースト デシジョン ツリー分類器を初期化するために、**2 クラス ブースト デシジョン ツリー** モジュールを追加します。
1. **モデルのトレーニング** モジュールを追加します。 前の手順の分類器を**モデルのトレーニング**の左側の入力ポートに接続します。. トレーニング セット (**データの分割**の左側の出力ポート) を**モデルのトレーニング**の右側の入力ポートに追加します。 **モデルのトレーニング**が分類器をトレーニングします。
1. **モデルのスコア付け**モジュールを追加し、それに**モデルのトレーニング** モジュールを接続します。 次に、テスト セット (**データの分割**の右側のポート) を**モデルのスコア付け**に追加します。 **モデルのスコア付け**で予測が作成されます。 その出力ポートを選択すると、予測と正のクラス確率が表示されます。
1. **モデルの評価**モジュールを追加し、スコア付けされたデータセットをその左側の入力ポートに接続します。 評価結果を表示するには、**モデルの評価**モジュールの出力ポートを選択して、**[視覚化]** を選択します。
    
完全な実験グラフを次に示します。

![実験のグラフ](media/ui-sample-classification-predict-credit-risk-basic/overall-graph.png)


## <a name="results"></a>結果

![結果を評価](media/ui-sample-classification-predict-credit-risk-basic/evaluate-result.png)

評価結果で、モデルの AUC が 0.776 であることがわかります。 0.5 のしきい値では、精度は 0.621、再現率は 0.456、F1 スコアは 0.526 です。

## <a name="clean-up-resources"></a>リソースのクリーンアップ

[!INCLUDE [aml-ui-cleanup](../../../includes/aml-ui-cleanup.md)]

## <a name="next-steps"></a>次の手順

ビジュアル インターフェイスで利用できるその他のサンプルを確認します。

- [サンプル 1 - 回帰:自動車の価格を予測する](ui-sample-regression-predict-automobile-price-basic.md)
- [サンプル 2 - 回帰:自動車の価格予測のためのアルゴリズムを比較する](ui-sample-regression-predict-automobile-price-compare-algorithms.md)
- [サンプル 4 - 分類:信用リスクを予測する (費用重視)](ui-sample-classification-predict-credit-risk-cost-sensitive.md)
- [サンプル 5 - 分類:顧客離れを予測する](ui-sample-classification-predict-churn.md)