---
title: 多クラスのブースト デシジョン ツリー:モジュール リファレンス
titleSuffix: Azure Machine Learning
description: Azure Machine Learning の多クラスのブースト デシジョン ツリー モジュールを使用して、ラベル付きデータを使用して分類器を作成する方法について説明します。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 02/19/2020
ms.openlocfilehash: 7d51e3007b7773e28d846f8d30178426f5668cfb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/28/2020
ms.locfileid: "77920061"
---
# <a name="multiclass-boosted-decision-tree"></a>多クラスのブースト デシジョン ツリー

この記事では Azure Machine Learning デザイナー (プレビュー) 内のモジュールについて説明します。

ブースト デシジョン ツリー アルゴリズムに基づく機械学習モデルを作成するには、このモジュールを使用します。

ブースト デシジョン ツリーは、第 2 のツリーで第 1 のツリーの誤差を補正し、第 3 のツリーで第 1 および第 2 のツリーの誤差を補正するといったアンサンブル学習手法です。 予測は、ツリーの集団に基づいて行われます。

## <a name="how-to-configure"></a>構成方法 

このモジュールでは、トレーニングされていない分類モデルが作成されます。 分類は教師あり学習手法であるため、ラベル列のすべての行に値を含んだ "*ラベル付けされたデータセット*" が必要です。

このタイプのモデルは、"[モデルのトレーニング](././train-model.md)" を使用してトレーニングできます。 

1.  **多クラスのブースト デシジョン ツリー** モジュールをパイプラインに追加します。

1.  **[Create trainer mode]\(トレーナー モードの作成\)** オプションを設定して、モデルのトレーニング方法を指定します。

    + **Single Parameter (単一パラメーター)** : モデルの構成方法がわかっている場合、特定の値のセットを引数として渡すことができます。
    
    + **[Parameter Range]\(パラメーター範囲\)** : 最適なパラメーターがわからず、パラメーター スイープを実行する場合は、このオプションを選択します。 反復する値の範囲を選択します。[モデルのハイパーパラメーターの調整](tune-model-hyperparameters.md)では、指定した設定の可能なすべての組み合わせに対して反復処理を行い、最適な結果を生成するハイパーパラメーターを決定します。  

1. **[Maximum number of leaves per tree]\(ツリーあたりの最大リーフ数\)** は、ツリーに作成できる終端ノード (リーフ) の最大数を制限します。
    
        By increasing this value, you potentially increase the size of the tree and achieve higher precision, at the risk of overfitting and longer training time.
  
1. **[Minimum number of samples per leaf node]\(リーフ ノードごとの最小サンプル数\)** は、ツリーの終端ノード (リーフ) を作成するうえで必要なケース数を指定します。  

         By increasing this value, you increase the threshold for creating new rules. For example, with the default value of 1, even a single case can cause a new rule to be created. If you increase the value to 5, the training data would have to contain at least five cases that meet the same conditions.

1. **[学習速度]** は、学習時のステップ サイズを定義します。 0 から 1 までの数値を入力してください。

         The learning rate determines how fast or slow the learner converges on an optimal solution. If the step size is too large, you might overshoot the optimal solution. If the step size is too small, training takes longer to converge on the best solution.

1. **[Number of trees constructed]\(構築するツリーの数\)** は、集団として作成するデシジョン ツリーの総数を指定します。 作成するデシジョン ツリーを増やすと、カバレッジが向上する可能性はありますが、トレーニング時間が長くなります。

1. **[Random number seed]\(乱数シード\)** に、ランダム シード値として使用する値 (負でない整数) を必要に応じて設定します。 シードを指定することによって、同じデータとパラメーターで繰り返し実行したときの再現性が確保されます。  

         The random seed is set by default to 42. Successive runs using different random seeds can have different results.

> [!Note]
> **[Create trainer mode]\(トレーナー モードの作成\)** を **[Single Parameter]\(単一パラメーター\)** に設定した場合は、タグ付けされたデータセットと[モデルのトレーニング](./train-model.md) モジュールを接続します。

## <a name="next-steps"></a>次のステップ

Azure Machine Learning で[使用できる一連のモジュール](module-reference.md)を参照してください。 
