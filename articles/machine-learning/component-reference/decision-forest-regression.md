---
title: 'Decision Forest Regression (デシジョン フォレスト回帰): コンポーネント レファレンス'
titleSuffix: Azure Machine Learning
description: Azure Machine Learning で Decision Forest Regression (デシジョン フォレスト回帰) コンポーネントを使用し、デシジョン ツリーの集団に基づく回帰モデルを作成する方法について説明します。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 04/22/2020
ms.openlocfilehash: cc3d1752dbf28d5564aefb309a0cf5ae4a48cd5a
ms.sourcegitcommit: e41827d894a4aa12cbff62c51393dfc236297e10
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/04/2021
ms.locfileid: "131565583"
---
# <a name="decision-forest-regression-component"></a>Decision Forest Regression (デシジョン フォレスト回帰) コンポーネント

この記事では Azure Machine Learning デザイナーのコンポーネントについて説明します。

デシジョン ツリーの集団に基づく回帰モデルを作成するには、このコンポーネントを使用します。

モデルの構成後、ラベル付けされたデータセットと [[Train Model]\(モデルのトレーニング\)](./train-model.md) コンポーネントを使用してそのモデルをトレーニングする必要があります。 その後、トレーニング済みのモデルは、予測に使用できます。 

## <a name="how-it-works"></a>しくみ

デシジョン ツリーは、リーフ ノード (決定) に到達するまでバイナリ ツリーのデータ構造を走査しながら、個々の事例について一連の単純なテストを行うノンパラメトリックなモデルです。

デシジョン ツリーには、次の利点があります。

- トレーニング時と予測時における計算とメモリ使用量の両方の点で効率に優れている。

- 非線形の決定境界を表すことができる。

- 統合された特徴選択と分類が行われ、ノイズの多い特徴が存在する状況での回復性がある。

この回帰モデルは、複数のデシジョン ツリーの集団から成ります。 回帰デシジョン フォレストに含まれる各ツリーからは、予測としてガウス分布が出力されます。 ツリーの集団に対して集計 (アグリゲーション) を行うことによって、モデルに含まれる全ツリーの分布の組み合わせに最も近いガウス分布を見つけ出します。

このアルゴリズムの理論に基づくフレームワークとその実装の詳細については、次の記事を参照してください: [Decision Forests: A Unified Framework for Classification, Regression, Density Estimation, Manifold Learning and Semi-Supervised Learning (デシジョン フォレスト: 分類、回帰、密度推定、多様体学習、半教師あり学習のための統一フレームワーク)](https://www.microsoft.com/en-us/research/publication/decision-forests-a-unified-framework-for-classification-regression-density-estimation-manifold-learning-and-semi-supervised-learning/?from=http%3A%2F%2Fresearch.microsoft.com%2Fapps%2Fpubs%2Fdefault.aspx%3Fid%3D158806#)

## <a name="how-to-configure-decision-forest-regression-model"></a>デシジョン フォレスト回帰モデルを構成する方法

1. **[Decision Forest Regression]\(デシジョン フォレスト回帰\)** コンポーネントをパイプラインに追加します。 このコンポーネントは、デザイナーの **[Machine Learning]\(機械学習\)** 、 **[Initialize Model]\(モデルの初期化\)** 、の順に選択した先の **[Regression]\(回帰\)** にあります。

2. コンポーネントのプロパティを開き、 **[Resampling method]\(再サンプリング方法\)** で、個々のツリーの作成に使用する方法を選択します。  **[Bagging]\(バギング\)** または **[レプリケート]** から選択できます。

    - **[Bagging]\(バギング\)** : バギングは、"*ブートストラップ アグリゲーティング*" とも呼ばれます。 回帰デシジョン フォレストに含まれる各ツリーからは、予測の手段としてガウス分布が出力されます。 アグリゲーションとは、個々のツリーから返された全分布の組み合わせによって得られるガウス分布の融合体のモーメントと最初の 2 つのモーメントが一致するガウス分布を見つけ出すことです。

         詳細については、Wikipedia で[ブートストラップ アグリゲーティング](https://wikipedia.org/wiki/Bootstrap_aggregating)に関する項目を参照してください。

    - **[レプリケート]** : レプリケーションでは、各ツリーがまったく同じ入力データでトレーニングされます。 それぞれのツリー ノードでどの分割述語が使用されるかの決定はランダムなままであり、ツリーは多様になります。

         **[レプリケート]** オプションを使用したトレーニング プロセスの詳細については、次の書籍を参照してください: [『Decision Forests for Computer Vision and Medical Image Analysis』(Criminisi および J. Shotton 共著、Springer 2013)](https://research.microsoft.com/projects/decisionforests/)。

3. **[Create trainer mode]\(トレーナー モードの作成\)** オプションを設定して、モデルのトレーニング方法を指定します。

    - **[Single Parameter]\(単一パラメーター\)**

      モデルの構成方法がわかっている場合、特定の値のセットを引数として渡すことができます。 これらの値は、実験によって得られるか、またはガイダンスとして得られます。

    - **[Parameter Range]\(パラメーター範囲\)** : 最適なパラメーターがわからず、パラメーター スイープを実行する場合は、このオプションを選択します。 反復する値の範囲を選択します。[モデルのハイパーパラメーターの調整](tune-model-hyperparameters.md)では、指定した設定の可能なすべての組み合わせに対して反復処理を行い、最適な結果を生成するハイパーパラメーターを決定します。 



4. 集団として作成するデシジョン ツリーの総数を **[Number of decision trees]\(デシジョン ツリーの数\)** に指定します。 作成するデシジョン ツリーを増やすと、カバレッジが向上する可能性はありますが、トレーニング時間が長くなります。

    > [!TIP]
    > 値に 1 を設定した場合であっても、これが意味するのは、生成されるツリーは 1 つ (最初の一連のパラメーターを持ったツリー) だけであり、それ以上のイテレーションは実行されないということです。

5. **[Maximum depth of the decision trees]\(デシジョン ツリーの最大深度\)** には、デシジョン ツリーの最大深度を制限する数値を入力します。 ツリーの深度を増やすと、精度が向上する可能性があるものの、オーバーフィットが発生しやすくなり、トレーニング時間が長くなるおそれがあります。

6. **[Number of random splits per node]\(ノードごとのランダム分割数\)** には、ツリーの各ノードを構築するときに使用する分割数を入力します。 "*分割*" とは、ツリー (ノード) の各レベルの特徴がランダムに分割されることを意味します。

7. **[Minimum number of samples per leaf node]\(リーフ ノードごとの最小サンプル数\)** には、ツリーの終端ノード (リーフ) を作成するうえで必要な最小ケース数を指定します。

     この値を増やすと、新しいルールを作成するためのしきい値が大きくなります。 たとえば、既定値の 1 では、ケースが 1 つであっても新しいルールを作成できます。 この値を 5 に増やした場合、同じ条件を満たすケースがトレーニング データに少なくとも 5 つ含まれている必要があります。


9. モデルをトレーニングします。

    + **[Create trainer mode]\(トレーナー モードの作成\)** を **[Single Parameter]\(単一パラメーター\)** に設定した場合は、タグ付けされたデータセットと [[Train Model]\(モデルのトレーニング\)](train-model.md) コンポーネントを接続します。  
  
    + **[Create trainer mode]\(トレーナー モードの作成\)** を **[Parameter Range]\(パラメーター範囲\)** に設定した場合は、[[Tune Model Hyperparameters]\(モデルのハイパーパラメーターの調整\)](tune-model-hyperparameters.md) を使用して、タグ付けしたデータセットを接続してモデルをトレーニングします。  
  
    > [!NOTE]
    > 
    > パラメーター範囲を [[モデルのトレーニング]](train-model.md) に渡すと、単一のパラメーター リストの既定値のみが使用されます。  
    > 
    > [[Tune Model Hyperparameters]\(モデルのハイパーパラメーターの調整\)](tune-model-hyperparameters.md) コンポーネントで各パラメーターの設定範囲が想定されているときに、単一のパラメーター値のセットを渡した場合、その値は無視され、学習器の既定値が使用されます。  
    > 
    > **[Parameter Range]\(パラメーター範囲\)** オプションを選択し、任意のパラメーターに単一の値を入力した場合、指定した単一の値はスイープ全体で使用されます。これは、他のパラメーターが値の範囲の中で変化する場合でも同様です。

   

10. パイプラインを送信します。

### <a name="results"></a>結果

トレーニングの完了後:

+ トレーニング済みモデルのスナップショットを保存するには、トレーニング コンポーネントを選択し、右側のパネルの **[出力]** タブに切り替えます。 **[モデルの登録]** アイコンをクリックします。  保存されたモデルは、コンポーネント ツリーにコンポーネントとして示されます。 

## <a name="next-steps"></a>次の手順

Azure Machine Learning で[使用できる一連のコンポーネント](component-reference.md)を参照してください。 