---
title: ワイドかつディープなレコメンダーのトレーニング コンポーネントを使用する
titleSuffix: Azure Machine Learning
description: Azure Machine Learning デザイナーでワイドかつディープなレコメンダーのトレーニング コンポーネントを使用して、推奨モデルをトレーニングする方法について説明します。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 03/19/2021
ms.openlocfilehash: 36d83468faaf5b3df5d8516fd788773a9f8187d4
ms.sourcegitcommit: e41827d894a4aa12cbff62c51393dfc236297e10
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/04/2021
ms.locfileid: "131565727"
---
# <a name="train-wide--deep-recommender"></a>ワイドかつディープなレコメンダーのトレーニング
この記事では、Azure Machine Learning デザイナーで **ワイドかつディープなレコメンダーのトレーニング** コンポーネントを使用して、推奨モデルをトレーニングする方法について説明します。 このコンポーネントは、Google によって提案されているワイド & ディープ ラーニング (Wide & Deep Learning) に基づいています。

**ワイドかつディープなレコメンダーのトレーニング** コンポーネントは、ユーザー項目の評価 3 要素のデータセットと、オプションで一部のユーザーと項目の特徴を読み取ります。 これにより、トレーニング済みのワイドかつディープなレコメンダーが返されます。  その後、トレーニング済みのモデルを使用して、[ワイドかつディープなレコメンダーのスコア付け](score-wide-and-deep-recommender.md)コンポーネントを使用して評価予測や提案を生成することができます。  

<!-- Currently, **Train Wide & Deep Recommender** component supports both single node and distributed training. -->

## <a name="more-about-recommendation-models-and-the-wide--deep-recommender"></a>推奨モデルとワイドかつディープなレコメンダーの詳細  

レコメンデーション システムの主な目的は、システムの *ユーザー* に対して 1 つ以上の *項目* を推奨することです。 項目の例として、映画、レストラン、本、曲などがあります。 ユーザーは、個人、個人のグループ、または項目の好みがあるその他のエンティティにすることができます。  

レコメンダー システムには、主に 2 つのアプローチがあります。 

+ 1 つ目は、**コンテンツベースの** アプローチです。これは、ユーザーと項目の両方の特徴を利用します。 ユーザーは、年齢や性別などのプロパティによって記述できます。また、項目は作成者や製造元などのプロパティによって記述される場合があります。 コンテンツベースのレコメンデーション システムの一般的な例は、出会い系のサイトです。 
+ 2 番目の方法は **協調フィルタリング** です。このフィルターでは、ユーザーと項目の識別子のみを使用し、ユーザーが項目に対して指定した (疎な) 評価マトリックスから、これらのエンティティについての暗黙的な情報を取得します。 ユーザーについての情報は、そのユーザーが評価した項目や、同じ項目を評価した他のユーザーから学ぶことができます。  

ワイドかつディープなレコメンダーは、協調フィルタリングとコンテンツベースのアプローチを使用して、これらのアプローチを組み合わせています。 したがって、**ハイブリッド レコメンダー** と考えることができます。 

このしくみは次のとおりです。ユーザーがシステムの比較的初心者である場合は、ユーザーに関する特徴の情報を使用することによって予測が向上します。このようにしてよく知られた "コールド スタート" の問題に対処します。 ただし、特定のユーザーから十分な数の評価を収集した後は、これらのユーザーに対して、その特徴だけではなく特定の評価に基づいて完全に個人的な予測を行うことができます。 そのため、コンテンツベースの推奨事項から協調フィルタリングに基づく推奨事項へのスムーズな移行があります。 ユーザーまたは項目の特徴を使用できない場合でも、ワイドかつディープなレコメンダーは協調フィルタリング モードで動作します。  

ワイド & ディープ レコメンダーと、それに使用している確率的アルゴリズムの詳細は『[Wide & Deep Learning for Recommender Systems](https://arxiv.org/pdf/1606.07792.pdf)』(レコメンダー システムのためのワイド & ディープ ラーニング) という論文で説明されています。  

## <a name="how-to-configure-train-wide--deep-recommender"></a>ワイドかつディープなレコメンダーのトレーニングを構成する方法  

+ [トレーニング データの準備](#prepare-data)
+ [モデルのトレーニング](#train-the-model)

### <a name="prepare-data"></a>データを準備する

コンポーネントを使用する前に、データがレコメンデーション モデルにふさわしい形式になっていることを確認してください。 **ユーザー、項目、評価を表す 3 つの要素** のトレーニング データセットが必要ですが、ユーザーの特徴と項目の特徴 (入手可能な場合) を別々のデータセットに含めることもできます。

#### <a name="required-dataset-of-user-item-ratings"></a>ユーザー、項目、評価の必須データセット

トレーニングに使用する入力データに、適切な形式のデータが含まれている必要があります。 

+ 最初の列には、ユーザーの識別子が含まれている必要があります。
+ 2 番目の列には、項目の識別子が含まれている必要があります。
+ 3 番目の列には、ユーザーと項目のペアの評価が含まれています。 評価の値は数値型にする必要があります。 

たとえば、典型的なユーザー、項目、評価のセットは次のようになります。

|UserId|MovieId|Rating|
|------------|-------------|------------|
|1|68646|10|
|223|31381|10|

#### <a name="user-features-dataset-optional"></a>ユーザーの特徴のデータセット (オプション)

**ユーザーの特徴** のデータセットには、ユーザーの識別子を含める必要があります。また、ユーザー、項目、評価のデータセットの最初の列に指定されていたものと同じ識別子を使用します。 その他の列には、ユーザーを表す特徴がいくつ含まれていてもかまいません。  

たとえば、典型的なユーザーの特徴のセットは次のようになります。 

|UserId|Age|性別|興味|場所|
|------------|--------------|-----------------------|---------------|------------|
|1|25|male| ドラマ    |ヨーロッパ|
|223|40|female|ロマンス|アジア|

#### <a name="item-features-dataset-optional"></a>項目の特徴のデータセット (オプション)

項目の特徴のデータセットでは、最初の列に項目の識別子が含まれている必要があります。 その他の列には、項目を説明する特徴がいくつ含まれていてもかまいません。  

たとえば、典型的な項目の特徴のセットは次のようになります。  

|MovieId|タイトル|原語|ジャンル|年|
|-------------|-------------|-------------------|-----------|---------------|
|68646|ゴッドファーザー|英語|ドラマ|1972|
|31381|風と共に去りぬ|英語|履歴|1939|

### <a name="train-the-model"></a>モデルをトレーニングする

1.  **ワイドかつディープなレコメンダーのトレーニング** コンポーネントをデザイナー内の実験に追加し、これをトレーニング データセットに接続します。  
  
2. ユーザーの特徴または項目の特徴のいずれかの個別のデータセットがある場合は、これらを **ワイドかつディープなレコメンダーのトレーニング** コンポーネントに接続します。  
  
    - **ユーザーの特徴のデータセット**:ユーザーを記述するデータセットを 2 番目の入力に接続します。
    - **項目の特徴のデータセット**:項目を記述するデータセットを 3 番目の入力に接続します。  
    
3.  **[Epochs]\(エポック\)** : アルゴリズムがトレーニング データ全体を処理する回数を示します。 

    この数が多いほど、トレーニングが十分になります。ただし、トレーニングにかかる時間が増え、オーバーフィットの原因になる可能性があります。

4. **[バッチ サイズ]** : 1 回のトレーニング ステップで使用されるトレーニング例の数を入力します。 

     このハイパーパラメーターはトレーニング速度に影響を与える場合があります。 バッチ サイズを大きくすると時間が短縮されますが、収束時間が長くなる可能性があります。 バッチが大きすぎて GPU または CPU で処理できない場合、メモリ エラーが発生することがあります。

5.  **[Wide part optimizer]\(ワイド パート オプティマイザー\)** : モデルのワイド部分にグラデーションを適用するための 1 つのオプティマイザーを選択します。

6.  **[Wide optimizer learning rate]\(ワイド オプティマイザー学習速度\)** : ワイド パート オプティマイザーの学習速度を定義する 0.0 から 2.0 までの数値を入力します。

    このハイパーパラメーターは、各トレーニング ステップでのステップ サイズを決定しながら、損失関数の最適解に近づきます。 学習率が大きいと学習時に最小値を通り越す場合がありますが、学習率が小さすぎると収束に関する問題が起こる場合があります。

7.  **[Crossed feature dimension]\(クロス積変換された特徴の次元\)** : 目的のユーザー ID とアイテム ID の特徴を入力することで次元を指定します。 

    ワイド & ディープ レコメンダーでは、ユーザー ID およびアイテム ID の特徴に対し、既定でクロス積変換を行います。 クロス積変換された結果は、ディメンションを確認するために、この数に従ってハッシュされます。

8.  **[Deep part optimizer]\(ディープ パート オプティマイザー\)** : モデルのディープ部分にグラデーションを適用するための 1 つのオプティマイザーを選択します。

9.  **[Deep optimizer learning rate]\(ディープ オプティマイザー学習速度\)** : ディープ パート オプティマイザーの学習速度を定義する 0.0 から 2.0 までの数値を入力します。

10.  **[User embedding dimension]\(ユーザーの埋め込みの次元\)** : ユーザー ID の埋め込みの次元を整数で指定します。

     ワイド & ディープ レコメンダーでは、作成したユーザー ID およびアイテム ID の埋め込みを、ワイド部分とディープ部分で共有します。

11.  **[Item embedding dimension]\(項目の埋め込みの次元\)** : アイテム ID 埋め込みの次元を整数で指定します。

12.  **[Categorical features embedding dimension]\(カテゴリ別の特徴の埋め込みディメンション\)** : カテゴリ別の特徴の埋め込みのディメンションを指定する整数を入力します。

     ワイド & ディープ レコメンダーのディープ部分では、各カテゴリ特徴量について埋め込みベクトルを学習します。 また、これらの埋め込みベクトルは同じディメンションを共有します。

13.  **[Hidden units]\(非表示単位\)** : ディープ コンポーネントの非表示ノードの数を入力します。 各レイヤーのノード数は、コンマで区切られます。 たとえば、「1000,500,100」と入力すると、それぞれ 1,000 個、500 個、100 個のノードを含む 3 つの層をディープ部分に設定できます。

14.  **[Activation function]\(アクティブ化関数\)** : 各レイヤーに適用されているアクティブ化関数を 1 つ選択します。既定値は ReLU です。

15.  **ドロップアウト**: トレーニング中に各レイヤーで出力が削除される確率を決定するために、0.0 から 1.0 の範囲の数値を入力します。

     ドロップアウトはニューラル ネットワークのオーバーフィットを防ぐための正則化メソッドです。 この値に対する一般的な決定の 1 つは、さまざまなネットワークやタスクにとってほぼ最適と思われる 0.5 で開始することです。

16.  **[Batch Normalization]\(バッチ正規化\)** : ディープ コンポーネントの各非表示レイヤーの後にバッチ正規化を使用するには、このオプションを選択します。

     バッチ正規化は、ネットワーク トレーニング中の内部的な共変量シフトの問題を軽減するための手法です。 これは一般に、ネットワークの速度、パフォーマンス、および安定性を向上させるのに役立ちます。 

17.  パイプラインを実行します。


<!-- ## Distributed training

In distributed training the workload to train a model is split up and shared among multiple mini processors, called worker nodes. These worker nodes work in parallel to speed up model training. Currently the designer support distributed training for **Train Wide & Deep Recommender** component.

### How to enable distributed training

To enable distributed training for **Train Wide & Deep Recommender** component, you can set in **Run settings** in the right pane of the component. Only **[AML Compute cluster](../how-to-create-attach-compute-cluster.md?tabs=python)** is supported for distributed training.

1. Select the component and open the right panel. Expand the **Run settings** section.

    [![Screenshot showing how to set distributed training in run setting](./media/module/distributed-training-run-setting.png)](./media/module/distributed-training-run-setting.png#lightbox)

1. Make sure you have select AML compute for the compute target.

1. In **Resource layout** section, you need to set the following values:

    - **Node count**: Number of nodes in the compute target used for training. It should be **less than or equal to** the **Maximum number of nodes** your compute cluster. By default it is 1, which means single node job.

    - **Process count per node**: Number of processes triggered per node. It should be **less than or equal to** the **Processing Unit** of your compute. By default it is 1, which means single node job.

    You can check the **Maximum number of nodes** and **Processing Unit** of your compute by clicking the compute name into the compute detail page.

    [![Screenshot showing how to check compute cluster](./media/module/compute-cluster-node.png)](./media/module/compute-cluster-node.png#lightbox)

You can learn more about distributed training in Azure Machine Learning [here](../concept-distributed-training.md).


### Troubleshooting for distributed training

If you enable distributed training for this component, there will be driver logs for each process. `70_driver_log_0` is for master process. You can check driver logs for error details of each process under **Outputs+logs** tab in the right pane.

[![Screenshot showing driver log](./media/module/distributed-training-error-driver-log.png)](./media/module/distributed-training-error-driver-log.png#lightbox) 

If the component enabled distributed training fails without any `70_driver` logs, you can check `70_mpi_log` for error details.

The following example shows a common error that is **Process count per node** is larger than **Processing Unit** of the compute.

[![Screenshot showing mpi log](./media/module/distributed-training-error-mpi-log.png)](./media/module/distributed-training-error-mpi-log.png#lightbox)

## Results

After pipeline run is completed, to use the model for scoring, connect the [Train Wide and Deep Recommender](train-wide-and-deep-recommender.md) to [Score Wide and Deep Recommender](score-wide-and-deep-recommender.md), to predict values for new input examples.
 -->

##  <a name="technical-notes"></a>テクニカル ノート

ワイド & ディープでは、ワイド線形モデルとディープ ニューラル ネットワークを結合してトレーニングすることで、記憶と汎化の長所を組み合わせることができます。 ワイド コンポーネントは、特徴の相互作用を記憶するために、一連の未加工の特徴と特徴の変換を受け入れます。 また、ディープ部分では、多くの特徴量エンジニアリングを行なうことなく、特徴の低次元な高密度埋め込みを使用して、未来の未知の組み合わせを一般化します。 

ワイドかつディープなレコメンダーの実装では、コンポーネントは既定のモデル構造を使用します。 ワイド部分では、ユーザーの埋め込み、アイテムの埋め込み、ユーザー ID とアイテム ID のクロス積変換を入力として使用します。 モデルのディープ部分では、各カテゴリ特徴量について埋め込みベクトルを学習します。 これらのベクトルは、その他の数値特徴ベクトルと共に、詳細なフィードフォワード ニューラル ネットワークに取り込まれます。 ワイド パートとディープ パートは、最終的な出力対数オッズを予測として合計することによって組み合わされ、最終的には結合トレーニングのための 1 つの共通損失関数になります。


## <a name="next-steps"></a>次のステップ

Azure Machine Learning で[使用できる一連のコンポーネント](component-reference.md)を参照してください。