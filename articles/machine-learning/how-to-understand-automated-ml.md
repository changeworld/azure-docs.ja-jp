---
title: AutoML 実験結果の評価
titleSuffix: Azure Machine Learning
description: 自動機械学習の実験を実行するごとに、グラフとメトリックを確認し、評価する方法について説明します。
services: machine-learning
ms.reviewer: nibaccam
ms.service: machine-learning
ms.subservice: automl
ms.date: 10/21/2021
ms.topic: how-to
ms.custom: contperf-fy21q2, automl
ms.openlocfilehash: 3b638535784e35089050f3f7e55d85232800ef06
ms.sourcegitcommit: e41827d894a4aa12cbff62c51393dfc236297e10
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/04/2021
ms.locfileid: "131561558"
---
# <a name="evaluate-automated-machine-learning-experiment-results"></a>自動機械学習実験の結果を評価

この記事では、自動機械学習 (自動 ML) の実験でトレーニングされたモデルを評価し、比較する方法について説明します。 自動 ML 実験の過程で、多くの実行が作成され、各実行でモデルが作成されます。 自動 ML では、モデルごとに、モデルのパフォーマンスを測定するのに役立つ評価メトリックとグラフが生成されます。 

たとえば、自動 ML では、実験の種類に基づいて次のグラフが生成されます。

| 分類| 回帰/予測 |
| ----------------------------------------------------------- | ---------------------------------------- |
| [混同行列](#confusion-matrix)                       | [Residuals ヒストグラム](#residuals)        |
| [受信者操作特性 (ROC) 曲線](#roc-curve) | [予測と True](#predicted-vs-true) |
| [適合率 - 再現率 (PR) 曲線](#precision-recall-curve)      |                                          |
| [リフト曲線](#lift-curve)                                   |                                          |
| [累積ゲイン曲線](#cumulative-gains-curve)           |                                          |
| [較正曲線](#calibration-curve)                     |                     


## <a name="prerequisites"></a>前提条件

- Azure サブスクリプション。 Azure サブスクリプションをお持ちでない場合は、開始する前に[無料アカウントを作成](https://azure.microsoft.com/free/)してください。
- Azure Machine Learning 実験は、次のいずれかを使用して作成します。
  - [Azure Machine Learning スタジオ](how-to-use-automated-ml-for-ml-models.md) (コード不要)
  - [Azure Machine Learning Python SDK](how-to-configure-auto-train.md)

## <a name="view-run-results"></a>実行結果の表示

自動 ML 実験の完了後、実行履歴を次の方法で見つけることができます。
  - ブラウザーと [Azure Machine Learning スタジオ](overview-what-is-machine-learning-studio.md)
  - Jupyter Notebook で [RunDetails Jupyter ウィジェット](/python/api/azureml-widgets/azureml.widgets.rundetails)を使用

次の手順とビデオでは、スタジオで実行履歴とモデル評価のメトリックとグラフを表示する方法について説明します。

1. [スタジオにサインイン](https://ml.azure.com/)し、ワークスペースに移動します。
1. 左側のメニューで **[実験]** を選択します。
1. 使用する実験を実験の一覧から選択します。
1. ページの下部にあるテーブルで、自動 ML 実行を選択します。
1. **[モデル]** タブで、評価するモデルの **[アルゴリズム名]** を選択します。
1. **[メトリック]** タブで、左側のチェックボックスを使用してメトリックとグラフを表示します。

![スタジオでメトリックを表示する手順](./media/how-to-understand-automated-ml/how-to-studio-metrics.gif)

## <a name="classification-metrics"></a>分類メトリック

自動 ML では、実験用に生成された分類モデルごとにパフォーマンス メトリックが計算されます。 これらのメトリックは、scikit-learn 実装に基づいています。 

2 つのクラスの二項分類には多くの分類メトリックが定義されており、複数クラス分類用に 1 つのスコアを生成するには、クラスの平均値を求める必要があります。 Scikit-learn ではいくつかの平均化方法が提供され、そのうちの 3 つ、**マクロ**、**マイクロ**、および **加重** が、自動 ML で公開されます。

- **マクロ** -各クラスのメトリックを計算し、非加重平均を取得します。
- **マイクロ** - 真陽性、偽陰性、偽陽性の合計をカウントすることによって、メトリックをグローバルに計算します (クラスとは無関係)。
- **加重** - 各クラスのメトリックを計算し、クラスあたりのサンプル数に基づいて加重平均を取得します。

各平均化方法にはそれぞれのメリットがありますが、適切な方法を選択する際に共通する考慮事項の 1 つは、クラスの不均衡です。 クラスのサンプル数が異なる場合は、マイノリティ クラスがマジョリティ クラスと同等に重み付けされる、マクロ平均を使用する方が有益である可能性があります。 [自動 ML でのバイナリ メトリックと多クラス メトリック](#binary-vs-multiclass-classification-metrics)の詳細について確認してください。 

次の表は、実験用に生成された各分類モデルに対して自動 ML によって計算されるモデル パフォーマンス メトリックをまとめたものです。 詳細については、各メトリックの **"計算"** フィールドにリンクされている scikit-learn のドキュメントを参照してください。 

> [!NOTE]
> 画像分類モデルのメトリックの詳細については、[画像メトリック](#metrics-for-image-models-preview)に関するセクションを参照してください。

|メトリック|説明|計算|
|--|--|---|
|AUC | AUC は[受信者操作特性曲線](#roc-curve)の下の領域です。<br><br> **目的:** 1 に近いほど良い <br> **範囲:** [0, 1]<br> <br>サポートされているメトリック名の例 <li>`AUC_macro`: クラスごとの AUC の算術平均です。<li> `AUC_micro`、真陽性、偽陰性、偽陽性の合計をカウントすることによって計算されます。 <li> `AUC_weighted`: 各クラスのスコアの算術平均で、各クラス内の true インスタンスの数によって重み付けされます。 <li> `AUC_binary`: 1 つの特定のクラスを `true` クラスとして扱い、他のすべてのクラスを `false` クラスとして結合することによる、AUC の値。<br><br>|[計算](https://scikit-learn.org/0.22/modules/generated/sklearn.metrics.roc_auc_score.html) | 
|accuracy| 精度は、true クラス ラベルと正確に一致する予測の割合です。 <br> <br>**目的:** 1 に近いほど良い <br> **範囲:** [0, 1]|[計算](https://scikit-learn.org/0.22/modules/generated/sklearn.metrics.accuracy_score.html)|
|average_precision|平均適合率は、各しきい値で達成した適合率の加重平均として適合率-再現率曲線をまとめたもので、前のしきい値より増加した再現率を重みとして使用します。 <br><br> **目的:** 1 に近いほど良い <br> **範囲:** [0, 1]<br> <br>サポートされているメトリック名の例<li>`average_precision_score_macro`: 各クラスの平均適合率スコアの算術平均です。<li> `average_precision_score_micro`、真陽性、偽陰性、偽陽性の合計をカウントすることによって計算されます。<li>`average_precision_score_weighted`: 各クラスの平均適合率スコアの算術平均で、各クラス内の true インスタンスの数によって重み付けされます。 <li> `average_precision_score_binary`: 1 つの特定のクラスを `true` クラスとして扱い、他のすべてのクラスを `false` クラスとして結合することによる、平均適合率の値。|[計算](https://scikit-learn.org/0.22/modules/generated/sklearn.metrics.average_precision_score.html)|
balanced_accuracy|バランスの取れた精度は、各クラスの再現率の算術平均です。<br> <br>**目的:** 1 に近いほど良い <br> **範囲:** [0, 1]|[計算](https://scikit-learn.org/0.22/modules/generated/sklearn.metrics.recall_score.html)|
f1_score|F1 スコアは、適合率と再現率の調和平均です。 偽陽性と偽陰性の両方を適切に調整して測定します。 ただし、真陰性は考慮されません。 <br> <br>**目的:** 1 に近いほど良い <br> **範囲:** [0, 1]<br> <br>サポートされているメトリック名の例<li>  `f1_score_macro`: 各クラスの F1 スコアの算術平均です。 <li> `f1_score_micro`: 真陽性、偽陰性、偽陽性の合計をカウントすることによって計算されます。 <li> `f1_score_weighted`: 各クラスの F1 スコアのクラスごとの頻度の加重平均です。 <li> `f1_score_binary`: 1 つの特定のクラスを `true` クラスとして扱い、他のすべてのクラスを `false` クラスとして結合することによる、f1 の値。|[計算](https://scikit-learn.org/0.22/modules/generated/sklearn.metrics.f1_score.html)|
log_loss|これは、(多項) ロジスティック回帰とその拡張機能 (ニューラル ネットワークなど) で使用される損失関数で、確率的分類法の予測を前提として、true ラベルの負の対数尤度として定義されます。 <br><br> **目的:** 0 に近いほど良い <br> **範囲:** [0, inf)|[計算](https://scikit-learn.org/0.22/modules/generated/sklearn.metrics.log_loss.html)|
norm_macro_recall| 正規化されたマクロ再現率は、ランダムなパフォーマンスでのスコアが 0、最適なパフォーマンスでのスコアが 1 になるように、マクロ平均化および正規化された再現率です。 <br> <br>**目的:** 1 に近いほど良い <br> **範囲:** [0, 1] |`(recall_score_macro - R)`&nbsp;/&nbsp;`(1 - R)` <br><br>ここで、`R` はランダム予測の `recall_score_macro` の予想される値です。<br><br>`R = 0.5`&nbsp;: &nbsp;バイナリ&nbsp;分類の場合。 <br>`R = (1 / C)`: C クラス分類の問題の場合。|
matthews_correlation | Matthews 相関係数は、精度のバランスを取ります。これは、1 つのクラスに他より多くのサンプルが含まれている場合でも使用できます。 係数 1 は完全な予測、0 はランダムな予測、-1 は逆予測を示します。<br><br> **目的:** 1 に近いほど良い <br> **範囲:** [-1, 1]|[計算](https://scikit-learn.org/0.22/modules/generated/sklearn.metrics.matthews_corrcoef.html)|
精度|適合率は、負のサンプルが正としてラベル付けされないようにするモデルの機能です。 <br><br> **目的:** 1 に近いほど良い <br> **範囲:** [0, 1]<br> <br>サポートされているメトリック名の例 <li> `precision_score_macro`: 各クラスの適合率の算術平均です。 <li> `precision_score_micro`: 真陽性と偽陽性の合計をカウントすることによって、グローバルに計算されます。 <li> `precision_score_weighted`: 各クラスの適合率の算術平均で、各クラス内の true インスタンスの数によって重み付けされます。 <li> `precision_score_binary`: 1 つの特定のクラスを `true` クラスとして扱い、他のすべてのクラスを `false` クラスとして結合することによる、適合率の値。|[計算](https://scikit-learn.org/0.22/modules/generated/sklearn.metrics.precision_score.html)|
リコール| 再現率は、すべての正のサンプルを検出するモデルの機能です。 <br><br> **目的:** 1 に近いほど良い <br> **範囲:** [0, 1]<br> <br>サポートされているメトリック名の例 <li>`recall_score_macro`: 各クラスの再現率の算術平均です。 <li> `recall_score_micro`: 真陽性、偽陰性、偽陽性の合計をカウントすることによって、グローバルに計算されます。<li> `recall_score_weighted`: 各クラスの再現率の算術平均で、各クラス内の true インスタンスの数によって重み付けされます。 <li> `recall_score_binary`: 1 つの特定のクラスを `true` クラスとして扱い、他のすべてのクラスを `false` クラスとして結合することによる、再現率の値。|[計算](https://scikit-learn.org/0.22/modules/generated/sklearn.metrics.recall_score.html)|
weighted_accuracy|加重精度は、各サンプルが同じクラスに属するサンプルの合計数によって重み付けされる精度です。 <br><br>**目的:** 1 に近いほど良い <br>**範囲:** [0, 1]|[計算](https://scikit-learn.org/0.22/modules/generated/sklearn.metrics.accuracy_score.html)|

### <a name="binary-vs-multiclass-classification-metrics"></a>バイナリと多クラスの分類メトリック

自動 ML では、データがバイナリかどうかが自動的に検出され、`true` クラスを指定することによってデータが多クラスの場合でも、ユーザーは二項分類メトリックをアクティブにすることができます。 多クラス分類メトリックは、データセットのクラスが 2 つか 3 つ以上かに関係なく報告されます。 二項分類メトリックは、データがバイナリの場合、またはユーザーがオプションをアクティブにした場合にのみ報告されます。 

> [!Note]
> 二項分類タスクが検出されると、`numpy.unique` を使用してラベルのセットが検索され、それ以降のラベルが `true` クラスとして使用されます。 `numpy.unique` には並べ替えのプロシージャが含まれるので、`true` クラスの選択は安定します。

多クラス分類メトリックは多クラス分類を目的としていることに注意してください。 バイナリ データセットに適用した場合、これらのメトリックによってどのクラスも `true` クラスとして扱われません。 明らかに多クラス向けのメトリックには、`micro`、`macro`、または `weighted` がサフィックスとして付けられます。 例として、`average_precision_score`、`f1_score`、`precision_score`、`recall_score`、`AUC` などがあります。 たとえば、リコールを `tp / (tp + fn)` として計算する代わりに、多クラスの平均リコール (`micro`、`macro`、または `weighted`) は、二項分類データセットの両方のクラスの平均をとります。 これは、`true` クラスと `false` クラスのリコールを個別に計算してから、その 2 つの平均を取得することと同じです。

また、二項分類の自動検出がサポートされてはいますが、それでも、`true` クラスを常に手動で指定して、二項分類メトリックが正しいクラスに対して計算されるようにすることをお勧めします。

データセット自体が多クラスのときに、二項分類データセットのメトリックをアクティブにするために、ユーザーが行う必要があるのは、`true` クラスとして扱われるクラスの指定だけであり、これらのメトリックが計算されます。

## <a name="confusion-matrix"></a>混同行列

混同行列は、機械学習モデルが分類モデルの予測でどのように体系的なエラーをするかを視覚的に示します。 "混同" という言葉は、モデルの "混同" やサンプルの誤ったラベル付けに由来しています。 混同行列内の行 `i` と列 `j` にあるセルには、クラス `C_i` に属し、モデルによってクラス `C_j`に分類された、評価データセット内のサンプルの数が含まれています。

スタジオでは、セルが濃い方がサンプルが多いことを示しています。 ドロップダウンで **正規化** ビューを選択すると、各マトリックス行が正規化され、`C_j` クラスと予測される `C_i` クラスの割合が示されます。 既定の **未処理** ビューを使用するメリットは、実際のクラスの分布の不均衡によって、モデルがマイノリティ クラスのサンプルを誤って分類しているかどうかを確認できることです。これは、不均衡なデータセットでよくある問題です。

適切なモデルの混同行列では、ほとんどのサンプルが対角線に沿っています。

### <a name="confusion-matrix-for-a-good-model"></a>適切なモデルの混同行列 
![適切なモデルの混同行列 ](./media/how-to-understand-automated-ml/chart-confusion-matrix-good.png)

### <a name="confusion-matrix-for-a-bad-model"></a>適切でないモデルの混同行列
![適切でないモデルの混同行列](./media/how-to-understand-automated-ml/chart-confusion-matrix-bad.png)

## <a name="roc-curve"></a>ROC 曲線

受信者操作特性 (ROC) 曲線は、決定しきい値の変化に応じて、真陽性率 (TPR) と偽陽性率 (FPR) との関係をプロットします。 マジョリティ クラスによってマイノリティ クラスからのコントリビューションが打ち消される可能性があるため、クラスの不均衡が大きいデータセットでモデルをトレーニングする場合、ROC 曲線はあまり有益ではありません。

曲線 (AUC) の下の領域は、適切に分類されたサンプルの割合と解釈できます。 より正確に言うと、AUC は、分類子がランダムに選択された正のサンプルをランダムに選択された負のサンプルよりも高くランク付けする確率です。 曲線の形状は、分類のしきい値または決定の境界に応じて、TPR と FPR の関係を直感できるようにします。

グラフの左上隅に近づく曲線は、最適なモデルである 100% の TPR と 0% の FPR に近づいています。 ランダム モデルでは、左下隅から右上に向かう `y = x` 線に沿って ROC 曲線が生成されます。 ランダム モデルより良くないのは、ROC 曲線が `y = x` 線より下がるものです。
> [!TIP]
> 分類の実験では、自動 ML モデル用に生成された各折れ線グラフを使用して、クラスごとにモデルを評価したり、すべてのクラスに対して平均化したりできます。 グラフの右側にある凡例のクラス ラベルをクリックすると、これらのビューを切り替えることができます。

### <a name="roc-curve-for-a-good-model"></a>適切なモデルの ROC 曲線
![適切なモデルの ROC 曲線](./media/how-to-understand-automated-ml/chart-roc-curve-good.png)

### <a name="roc-curve-for-a-bad-model"></a>適切でないモデルの ROC 曲線
![適切でないモデルの ROC 曲線](./media/how-to-understand-automated-ml/chart-roc-curve-bad.png)

## <a name="precision-recall-curve"></a>適合率 - 再現率曲線

適合率 - 再現率曲線は、決定しきい値の変化に応じて、適合率と再現率の関係をプロットします。 再現率はすべての正のサンプルを検出するモデルの機能であり、適合率は負のサンプルを正としてラベル付けすることを回避するモデルの機能です。 いくつかのビジネス上の問題により、偽陰性の回避と偽陽性の回避のどちらが相対的に重要かに応じて、より高い再現性と適合率が求められる場合があります。
> [!TIP]
> 分類の実験では、自動 ML モデル用に生成された各折れ線グラフを使用して、クラスごとにモデルを評価したり、すべてのクラスに対して平均化したりできます。 グラフの右側にある凡例のクラス ラベルをクリックすると、これらのビューを切り替えることができます。
### <a name="precision-recall-curve-for-a-good-model"></a>適切なモデルの適合率 - 再現率曲線
![適切なモデルの適合率 - 再現率曲線](./media/how-to-understand-automated-ml/chart-precision-recall-curve-good.png)

### <a name="precision-recall-curve-for-a-bad-model"></a>適切でないモデルの適合率 - 再現率曲線
![適切でないモデルの適合率 - 再現率曲線](./media/how-to-understand-automated-ml/chart-precision-recall-curve-bad.png)

## <a name="cumulative-gains-curve"></a>累積ゲイン曲線

累積ゲイン曲線は、検討しているサンプルの割合に応じて、適切に分類された正のサンプルの割合をプロットします。サンプルは、予測される確率の順序で検討します。

ゲインを計算するには、まず、モデルで予測される確率が最高のものから最小のものまで、すべてのサンプルを並べ替えます。 次に、最も信頼度の高い予測の `x%` を使用します。 この `x%` で検出された正のサンプルの数を正のサンプルの合計数で割ることで、ゲインを得ることができます。 累積ゲインは、正のクラスに属する可能性が最も高いデータの割合を考慮した場合に検出される、正のサンプルの割合です。

完全なモデルでは、すべての正のサンプルをすべての負のサンプルの上に優先度付けして、2 つの直線セグメントで構成される累積ゲイン曲線を示します。 1 つ目は `(0, 0)` から `(x, 1)` への傾き `1 / x` の線で、`x` は正のクラスに属するサンプルの割合です (クラスの均衡が取れている場合は `1 / num_classes`)。 2 つ目は `(x, 1)` から `(1, 1)` までの水平線です。 最初のセグメントでは、すべての正のサンプルが正しく分類され、累積ゲインは、考慮されたサンプルの最初の `x%` 内で `100%` になります。

ベースライン ランダム モデルでは、`y = x` の後に累積ゲイン曲線があります。ここでは、検討されたサンプルの `x%` について、正のサンプルの合計の約 `x%` のみが検出されました。 バランスの取れたデータセットに完璧なモデルでは、マイクロ平均曲線と、累積ゲインが 100% になるまで傾きが `num_classes` のマクロ平均線があり、その後はデータの割合が 100 になるまで水平になります。
> [!TIP]
> 分類の実験では、自動 ML モデル用に生成された各折れ線グラフを使用して、クラスごとにモデルを評価したり、すべてのクラスに対して平均化したりできます。 グラフの右側にある凡例のクラス ラベルをクリックすると、これらのビューを切り替えることができます。
### <a name="cumulative-gains-curve-for-a-good-model"></a>適切なモデルの累積ゲイン曲線
![適切なモデルの累積ゲイン曲線](./media/how-to-understand-automated-ml/chart-cumulative-gains-curve-good.png)

### <a name="cumulative-gains-curve-for-a-bad-model"></a>適切でないモデルの累積ゲイン曲線
![適切でないモデルの累積ゲイン曲線](./media/how-to-understand-automated-ml/chart-cumulative-gains-curve-bad.png)

## <a name="lift-curve"></a>リフト曲線

リフト曲線には、ランダム モデルと比較して、モデルのパフォーマンスが何倍優れているかが示されます。 リフトは、ランダム モデルの累積ゲインに対する累積ゲインの比率として定義されます (常に `1` になる必要があります)。

この相対的なパフォーマンスでは、クラスの数を増やすと分類が困難になるという事実が考慮されます。 ランダム モデルでは、2 つのクラスを持つデータセットと比較して、10 個のクラスを持つデータセットからのサンプルの割合が、誤って予測されます。

ベースライン リフト曲線は、モデルのパフォーマンスとランダム モデルのパフォーマンスが一致する `y = 1` の線です。 一般的に、適切なモデルのリフト曲線は、グラフ上でより高くなり、x 軸からより離れています。つまり、モデルの予測の信頼度が最も高い場合は、ランダムな推測よりもパフォーマンスが何倍も良くなります。

> [!TIP]
> 分類の実験では、自動 ML モデル用に生成された各折れ線グラフを使用して、クラスごとにモデルを評価したり、すべてのクラスに対して平均化したりできます。 グラフの右側にある凡例のクラス ラベルをクリックすると、これらのビューを切り替えることができます。
### <a name="lift-curve-for-a-good-model"></a>適切なモデルのリフト曲線
![適切なモデルのリフト曲線](./media/how-to-understand-automated-ml/chart-lift-curve-good.png)
 
### <a name="lift-curve-for-a-bad-model"></a>適切でないモデルのリフト曲線
![適切でないモデルのリフト曲線](./media/how-to-understand-automated-ml/chart-lift-curve-bad.png)

## <a name="calibration-curve"></a>較正曲線

較正曲線は、各信頼レベルでの、正のサンプルの比率に対するモデルの予測の信頼度をプロットします。 適切に較正されたモデルでは、予測の 100% が正確に分類され、100% の信頼度が割り当てられます。また、予測の 50% に 50% の信頼度が、予測の 20% に 20% の信頼度が割り当てられます。 完全に較正されたモデルでは、`y = x` の線の後に較正曲線があり、サンプルが各クラスに属している確率が完全に予測されます。

過剰信頼モデルでは、0 と 1 に近い確率が過剰予測され、各サンプルのクラスが不確実であることはほぼありません。また、較正曲線は逆さの "S" のようになります。 信頼されないモデルでは、予測したクラスに概して低い確率が割り当てられ、関連付けられている較正曲線は "S" のようになります。 較正曲線は、適切に分類するモデルの能力を表すのではなく、予測に信頼を適切に割り当てる能力を表します。 適切でないモデルでも、そのモデルが低い信頼度と高い不確実性を適切に割り当てる場合は、適切な較正曲線が得られます。

> [!NOTE]
> 較正曲線はサンプル数に依存しているため、小さな検証セットによって、解釈が難しいノイズのある結果が生成される可能性があります。 これは、必ずしも、モデルが適切に較正されていないことを意味するわけではありません。

### <a name="calibration-curve-for-a-good-model"></a>適切なモデルの較正曲線
![適切なモデルの較正曲線](./media/how-to-understand-automated-ml/chart-calibration-curve-good.png)

### <a name="calibration-curve-for-a-bad-model"></a>適切でないモデルの較正曲線
![適切でないモデルの較正曲線](./media/how-to-understand-automated-ml/chart-calibration-curve-bad.png)

## <a name="regressionforecasting-metrics"></a>回帰/予測メトリック

自動 ML は、回帰実験であるか予測実験であるかに関係なく、生成される各モデルに対して同じパフォーマンス メトリックを計算します。 また、これらのメトリックは、異なる範囲のデータでトレーニングされたモデルを比較できるように正規化されます。 詳細については、「[メトリックの正規化](#metric-normalization)」を参照してください。  

次の表は、回帰および予測実験用に生成される、モデル パフォーマンス メトリックをまとめたものです。 分類メトリックと同様に、これらのメトリックも scikit-learn 実装に基づいています。 適切な scikit-learn ドキュメントが、 **"計算"** フィールドに適宜リンクされています。

|メトリック|説明|計算|
--|--|--|
explained_variance|説明分散では、モデルでターゲット変数のバリエーションを指定する範囲を測定します。 エラーの分散に対する元データの分散の減少の割合です。 誤差の平均が 0 の場合は、決定係数と等しくなります (以下の r2_score を参照)。 <br> <br> **目的:** 1 に近いほど良い <br> **範囲:** (-inf, 1]|[計算](https://scikit-learn.org/0.22/modules/generated/sklearn.metrics.explained_variance_score.html)|
mean_absolute_error|平均絶対誤差は、ターゲットと予測の間における差異の絶対値について予期される値です。<br><br> **目的:** 0 に近いほど良い <br> **範囲:** [0, inf) <br><br> タイプ: <br>`mean_absolute_error` <br>  `normalized_mean_absolute_error`: データの範囲で除算した mean_absolute_error です。 | [計算](https://scikit-learn.org/0.22/modules/generated/sklearn.metrics.mean_absolute_error.html)|
mean_absolute_percentage_error|平均絶対パーセント誤差 (MAPE) は、予測された値と実際の値との平均差を測定したものです。<br><br> **目的:** 0 に近いほど良い <br> **範囲:** [0, inf) ||
median_absolute_error|中央絶対誤差は、ターゲットと予測の間におけるすべての絶対差の中央値です。 この損失は外れ値に対してロバストです。<br><br> **目的:** 0 に近いほど良い <br> **範囲:** [0, inf)<br><br>タイプ: <br> `median_absolute_error`<br> `normalized_median_absolute_error`: データの範囲で除算した median_absolute_error です。 |[計算](https://scikit-learn.org/0.22/modules/generated/sklearn.metrics.median_absolute_error.html)|
r2_score|R<sup>2</sup> (決定係数) では、観測されたデータの全分散と比較して平均二乗誤差 (MSE) の比例減少が測定されます。 <br> <br> **目的:** 1 に近いほど良い <br> **範囲:** [-1, 1]<br><br>注: R<sup>2</sup> は、(-inf, 1] の範囲を持つことがよくあります。 MSE は観測された分散よりも大きい場合があるため、データとモデル予測によっては、R<sup>2</sup> は任意の大きな負の値を持つ場合があります。 自動 ML クリップによって -1 の R<sup>2</sup> スコアが報告された場合、R<sup>2</sup> の値 -1 は、実際の R<sup>2</sup> スコアが -1 未満であることを意味する可能性があります。 負の R<sup>2</sup> スコアを解釈する場合は、他のメトリック値とデータのプロパティを考慮してください。|[計算](https://scikit-learn.org/0.22/modules/generated/sklearn.metrics.r2_score.html)|
root_mean_squared_error |平均平方二乗誤差 (RMSE) は、ターゲットと予測の間における予期される二乗誤差の平方根です。 不偏推定の場合、RMSE は標準偏差と等しくなります。<br> <br> **目的:** 0 に近いほど良い <br> **範囲:** [0, inf)<br><br>タイプ:<br> `root_mean_squared_error` <br> `normalized_root_mean_squared_error`: データの範囲で除算した root_mean_squared_error です。 |[計算](https://scikit-learn.org/0.22/modules/generated/sklearn.metrics.mean_squared_error.html)|
root_mean_squared_log_error|対数平均平方二乗誤差は、予期される対数二乗誤差の平方根です。<br><br>**目的:** 0 に近いほど良い <br> **範囲:** [0, inf) <br> <br>タイプ: <br>`root_mean_squared_log_error` <br> `normalized_root_mean_squared_log_error`: データの範囲で除算した root_mean_squared_log_error です。  |[計算](https://scikit-learn.org/0.22/modules/generated/sklearn.metrics.mean_squared_log_error.html)|
spearman_correlation| スピアマンの相関は、2 つのデータセット間の関係の単調性に対するノンパラメトリック測定です。 ピアソンの相関とは異なり、スピアマンの相関は両方のデータセットが正規分布していることを想定しません。 他の相関係数と同様に、スピアマンは -1 と 1 の間で変化し、0 は相関関係がないことを示します。 相関係数が -1 または 1 の場合は、完全に単調な関係であることを示します。 <br><br> スピアマンはランク順序の相関関係メトリックです。これは、予測値または実際の値を変更しても、予測値または実際の値のランク順序を変更しないと、スピアマンの結果が変更されないことを意味します。<br> <br> **目的:** 1 に近いほど良い <br> **範囲:** [-1, 1]|[計算](https://docs.scipy.org/doc/scipy-1.5.2/reference/generated/scipy.stats.spearmanr.html)|

### <a name="metric-normalization"></a>メトリックの正規化

自動 ML は回帰および予測メトリックを正規化します。これにより、異なる範囲のデータでトレーニングされたモデルを比較できます。 より大きな範囲のデータでトレーニングされたモデルは、そのエラーが正規化されていない限り、より小さな範囲のデータでトレーニングされた同じモデルよりもエラーが高くなります。

エラー メトリックを標準化する標準的な方法はありませんが、自動 ML では、データの範囲によってエラーを除算する一般的な方法を採用しています: `normalized_error = error / (y_max - y_min)`

>[!Note]
>データの範囲はモデルと一緒に保存されません。 予約テスト セットに対して同じモデルを使用して推論を行う場合、`y_min` と `y_max` はテスト データに応じて変化する可能性があります。また、トレーニング セットとテスト セットに対するモデルのパフォーマンスを比較するために正規化されたメトリックを直接使用できないことがあります。 トレーニング セットから `y_min` と `y_max` の値を渡すことで、比較を公平に行うことができます。

時系列データの予測モデルを評価する場合、自動 ML では、時系列 ID (粒度) ごとに正規化が行われるように追加の手順が必要になります。これは、各時系列でターゲット値の分布が異なる可能性があるためです。
## <a name="residuals"></a>残差

残差グラフは、回帰実験および予測実験のために生成される予測エラー (残差) のヒストグラムです。 残差はすべてのサンプルの `y_predicted - y_true` として計算され、モデルの偏りを示すヒストグラムとして表示されます。

この例では、両方のモデルで、実際の値よりも低く予測するように若干偏っています。 実際のターゲットが傾斜分布しているデータセットの場合、これは珍しくありませんが、モデルのパフォーマンスが悪いことを示します。 適切なモデルでは、極端な場合はごくわずかな残差の、最高でゼロの残差分布になります。 適切でないモデルでは、ほぼゼロの少数のサンプルの、拡散した残差分布になります。

### <a name="residuals-chart-for-a-good-model"></a>適切なモデルの残差グラフ
![適切なモデルの残差グラフ](./media/how-to-understand-automated-ml/chart-residuals-good.png)

### <a name="residuals-chart-for-a-bad-model"></a>適切でないモデルの残差グラフ
![適切でないモデルの残差グラフ](./media/how-to-understand-automated-ml/chart-residuals-bad.png)

## <a name="predicted-vs-true"></a>予測と True

回帰および予測の実験の場合、予測と True のグラフは、ターゲットの特徴 (True の値/実際の値) とモデルの予測との関係をプロットします。 True の値は x 軸に沿ってビン分割され、各ビンの平均予測値はエラー バーでプロットされます。 これにより、モデルで特定の値を予測するように偏りがあるかどうかを確認できます。 線は平均の予測を表示し、網掛けされた領域はその平均に対する予測の分散を示します。

多くの場合、最も一般的な True 値では、分散が最も低い、最も正確な予測になります。 True の値が少ない理想的な `y = x` 線からの傾向線の距離は、外れ値に対するモデルのパフォーマンスを測る正しい尺度です。 グラフの下部にあるヒストグラムを使用して、実際のデータ分布を判断できます。 分布がまばらなデータ サンプルを追加すると、未認識のデータに対するモデルのパフォーマンスが向上することがあります。

この例では、より優れたモデルで、予測と True の線が理想的な `y = x` の線に近くなっています。

### <a name="predicted-vs-true-chart-for-a-good-model"></a>適切なモデルの予測と True のグラフ
![適切なモデルの予測と True のグラフ](./media/how-to-understand-automated-ml/chart-predicted-true-good.png)

### <a name="predicted-vs-true-chart-for-a-bad-model"></a>適切でないモデルの予測と True のグラフ
![適切でないモデルの予測と True のグラフ](./media/how-to-understand-automated-ml/chart-predicted-true-bad.png)

## <a name="metrics-for-image-models-preview"></a>イメージ モデルのメトリック (プレビュー)

モデルのパフォーマンスを評価するために、自動 ML には検証データセットの画像が使用されます。 トレーニングの進行状況を把握するために、モデルのパフォーマンスは **エポックレベル** で測定されます。 ニューラル ネットワーク内でデータセット全体が 1 回だけ送信と返信で渡されると、1 エポックが経過します。 

### <a name="image-classification-metrics"></a>画像分類メトリック

評価の主なメトリックは、バイナリおよびマルチクラス分類モデルの場合は **正確性** であり、マルチラベル分類モデルの場合は **IoU** ([Intersection over Union](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.jaccard_score.html#sklearn.metrics.jaccard_score)) です。
画像分類モデルの分類メトリックは、「[分類メトリック](#classification-metrics)」セクションに定義されているものと同じです。 また、あるエポックに関連付けられた損失値はログに記録もされます。これは、トレーニングの進行状況を監視し、モデルが過剰適合か過少適合かを判断するのに役立ちます。

分類モデルのすべての予測は、予測が行われたときの信頼度のレベルを示す信頼度スコアと関連付けられています。 マルチラベル画像分類モデルは、既定では、0.5 のスコアしきい値を使用して評価されます。つまり、少なくともこのレベルの信頼性を持つ予測のみが、関連するクラスの陽性の予測と見なされます。 マルチクラス分類にはスコアしきい値が使用されませんが、代わりに、最大の信頼度スコアを持つクラスが予測と見なされます。 

#### <a name="epoch-level-metrics-for-image-classification"></a>画像分類のためのエポックレベルのメトリック
表形式データセットの分類メトリックとは異なり、画像分類モデルの場合、以下に示すように、すべての分類メトリックはエポックレベルでログに記録されます。

![画像分類のエポックレベルのグラフ](./media/how-to-understand-automated-ml/image-classification-accuracy.png)

#### <a name="summary-metrics-for-image-classification"></a>画像分類の概要メトリック

エポック レベルでログに記録されるスカラー メトリックとは別に、画像分類モデルの場合、[混同行列](#confusion-matrix)、ROC 曲線、適合率 - 再現率曲線を含む[分類グラフ](#roc-curve)、最高レベルの主要メトリック (正確性) スコアを取得した最善のエポックのモデルの分類レポートなどの概要メトリックもログに記録されます。

分類レポートには、適合率、リコール、f1 スコア、サポート、auc、平均適合率などのメトリックのクラスレベルの値を、以下に示すように、ミクロ、マクロ、加重といったさまざまなレベルの平均化と共に利用できます。
「[分類メトリック](#classification-metrics)」セクションのメトリック定義 を参照してください。

![画像分類の分類レポート](./media/how-to-understand-automated-ml/image-classification-report.png)

### <a name="object-detection-and-instance-segmentation-metrics"></a>オブジェクト検出とインスタンスのセグメント化メトリック

画像オブジェクト検出またはインスタンス セグメント化のモデルからのすべての予測は、信頼度スコアと関連付けられます。
信頼度スコアがスコアのしきい値より大きい予測は予測として出力され、メトリックの計算に使用されます。その既定値はモデルによって異なり、[ハイパーパラメーターの調整](how-to-auto-train-image-models.md#model-specific-hyperparameters)に関するページ (`box_score_threshold` ハイパーパラメーター) から参照できます。

画像オブジェクト検出とインスタンス セグメント化モデルのメトリック コンピューティングは、**IoU** ([Intersection over Union](https://en.wikipedia.org/wiki/Jaccard_index)) というメトリックで定義される重複測定に基づいて行われます。これを計算するには、グランドトゥルースと予測の間の積集合を、グランドトゥルースと予測の和集合で除算します。 すべての予測から計算された IoU は、IoU しきい値と呼ばれる **重複しきい値** と比較されます。これにより、予測がユーザーが注釈をつけたグランドトゥルースとどれだけ重なると、予測が陽性の予測と見なされるかが決まります。 予測から計算された IoU が重複しきい値よりも小さい場合、その予測は関連するクラスの陽性の予測とは見なされません。

画像オブジェクト検出モデルとインスタンス セグメント化モデルを評価するための主要なメトリックは、**平均適合率 (mAP)** です。 mAP は、すべてのクラスの平均適合率 (AP) の平均値です。 自動化 ML オブジェクト検出モデルは、次の 2 つの一般的な方法を使用した mAP の計算をサポートしています。

**パスカル VOC メトリック**: 

[パスカル VOC](http://host.robots.ox.ac.uk/pascal/VOC/voc2012/htmldoc/devkit_doc.html#SECTION00044000000000000000) mAP は、オブジェクト検出またはインスタンス セグメント化モデルで既定の mAP 計算方法です。 パスカル VOC スタイルの mAP 法により、適合率 - 再現率曲線のバージョンの下の面積が計算されます。 最初の p(rᵢ) は、再現率 i における適合率であり、すべての一意の再現率値に対して計算されます。 次に、p(rᵢ) は任意の再現率 r' >= rᵢ で取得された最大適合率に置き換えられます。 このバージョンの曲線の適合率値は、単調に減少しています。 パスカル VOC mAP メトリックは、既定では IoU しきい値 0.5 で評価されます。 この概念の詳細な説明については、この[ブログ](https://jonathan-hui.medium.com/map-mean-average-precision-for-object-detection-45c121a31173)を参照してください。


**COCO メトリック**: 

[COCO 評価法](https://cocodataset.org/#detection-eval)の場合、10 個の IoU しきい値の平均値と共に、101 点補間法が AP の計算に使用されます。 AP@[.5:.95] は 0.5 から 0.95 までの IoU の平均 AP に対応し、ステップ サイズは 0.05 です。 自動 ML を使用すると、AP や AR (平均再現率) などの COCO 法で定義された 12 個のメトリックがさまざまなスケールでアプリケーション ログに記録されますが、メトリックのユーザー インターフェイスには、IoU しきい値が 0.5 の mAP のみが表示されます。 

> [!TIP]
> [ハイパーパラメーターの調整](how-to-auto-train-image-models.md#task-specific-hyperparameters)に関するセクションで説明されているように、`validation_metric_type`ハイパーパラメーターを 'coco' に設定すると、画像オブジェクト検出モデルの評価に coco メトリックを使用できます。

#### <a name="epoch-level-metrics-for-object-detection-and-instance-segmentation"></a>オブジェクト検出とインスタンス セグメント化のエポックレベル メトリック
mAP、適合率、再現率の値は、画像オブジェクト検出およびインスタンス セグメント化モデルのエポックレベルでログに記録されます。 また、mAP、適合率、再現率のメトリックは、'per_label_metrics' という名前でクラスレベルでもログに記録されます。 この 'per_label_metrics' は表形式で表示することをお勧めします。 

> [!NOTE]
> 'coco' 法を使用している場合、適合率、再現率、ラベル単位のメトリックのエポックレベル メトリックは使用できません。

![オブジェクト検出のエポックレベルのグラフ](./media/how-to-understand-automated-ml/image-object-detection-map.png)

## <a name="model-explanations-and-feature-importances"></a>モデルの説明と特徴の重要度

モデル評価メトリックおよびグラフは、モデルの一般的な質を測るのに適していますが、信頼できる AI を実現するには、モデルがその予測にデータセットのどの特徴を使用したかを調査することが重要です。 データセットの特徴の相対的なコントリビューションを測定してレポートする、モデルの説明ダッシュボードが自動 ML に備わっているのは、このためです。 [Azure Machine Learning スタジオで説明ダッシュボードを表示](how-to-use-automated-ml-for-ml-models.md#model-explanations-preview)する方法をご覧ください。

コード ファースト エクスペリエンスについては、[Azure Machine Learning Python SDK を使用した自動 ML 実験のモデルの説明](how-to-machine-learning-interpretability-automl.md)を設定する方法をご覧ください。

> [!NOTE]
> 解釈可能性、最適なモデルの説明は、次のアルゴリズムを最適なモデルまたはアンサンブルとして推奨する自動 ML の予測実験では使用できません。 
> * TCNForecaster
> * AutoArima
> * ExponentialSmoothing
> * Prophet
> * 平均 
> * Naive
> * Seasonal Average 
> * Seasonal Naive

## <a name="next-steps"></a>次のステップ
* [自動機械学習モデルの説明のサンプル ノートブック](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/explain-model)を試してください。
* 自動 ML 固有の質問については、askautomatedml@microsoft.com にお問い合わせください。
