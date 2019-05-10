---
title: 分類:信用リスクを予測する (費用重視)
titleSuffix: Azure Machine Learning service
description: このビジュアル インターフェイス サンプルの実験では、カスタマイズされた Python スクリプトを使用して、費用重視の二項分類を実行する方法を示します。 与信取引申請書で提供された情報に基づいて、信用リスクを予測します。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: article
author: xiaoharper
ms.author: zhanxia
ms.reviewer: sgilley
ms.date: 05/02/2019
ms.openlocfilehash: 03dfa250091b906ea1496fea3a4dd9b3753e0aaf
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/02/2019
ms.locfileid: "65027705"
---
# <a name="sample-4---classification-predict-credit-risk-cost-sensitive"></a>サンプル 4 - 分類:信用リスクを予測する (費用重視)

このビジュアル インターフェイス サンプルの実験では、カスタマイズされた Python スクリプトを使用して、費用重視の二項分類を実行する方法を示します。 肯定的サンプルを誤って分類した場合のコストは、否定的サンプルを誤って分類した場合のコストの 5 倍です。

このサンプルでは、与信取引申請書で提供された情報に基づき、誤分類のコストを考慮に入れて、信用リスクを予測します。

この実験では、この問題を解決するモデルを生成するため、次の 2 つの異なるアプローチを比較します。

- 元のデータセットを使ってトレーニングする。
- レプリケートされたデータセットを使ってトレーニングする。

どちらのアプローチでも、レプリケーションでテスト データセットを使用してモデルを評価し、結果がコスト関数と一致していることを確認します。 両方のアプローチで 2 つの分類子、**Two-Class Support Vector Machine (2 クラス サポート ベクター マシン)** と **Two-Class Boosted Decision Tree (2 クラス ブースト デシジョン ツリー)** をテストします。

## <a name="prerequisites"></a>前提条件

[!INCLUDE [aml-ui-prereq](../../../includes/aml-ui-prereq.md)]

4. サンプル 4 実験の **[Open]\(開く\)** ボタンを選択します。

    ![実験を開く](media/ui-sample-classification-predict-credit-risk-cost-sensitive/open-sample4.png)

## <a name="related-sample"></a>関連サンプル

誤分類のコストを調整せずに、この実験と同じ問題を解決するための基本的な実験については、「[Sample 3 - Classification:Credit Risk Prediction (Basic)](ui-sample-classification-predict-churn.md)」(サンプル 3 - 分類:信用リスクを予測する (基本)) を参照してください。

## <a name="data"></a>データ

UC Irvine リポジトリから German Credit Card のデータセットを使用します。 このデータセットには、20 個のフィーチャーと 1 個のラベルを含む 1,000 個のサンプルが含まれています。 サンプルはそれぞれ人を表します。 20 個のフィーチャーには、数値とカテゴリのフィーチャーが含まれています。 データセットの詳細については、[UCI Web サイト](https://archive.ics.uci.edu/ml/datasets/Statlog+%28German+Credit+Data%29)を参照してください。 最後の列は信用リスクを表すラベルで、使用できるのは次の 2 つの値だけです: 高信用リスク = 2、および低信用リスク = 1。

## <a name="experiment-summary"></a>実験の概要

低リスクのサンプルを高リスクとして誤って分類した場合のコストは 1 で、高リスクのサンプルを低リスクとして誤って分類した場合のコストは 5 です。 この誤分類コストを考慮するため、**Execute Python Script (Python スクリプトの実行)** モジュールを使用します。

実験のグラフを次に示します。

[ ![実験のグラフ](media/ui-sample-classification-predict-credit-risk-cost-sensitive/graph.png)](media/ui-sample-classification-predict-credit-risk-cost-sensitive/graph.png#lightbox)

## <a name="data-processing"></a>データ処理

まず、**Metadata Editor (メタデータ エディター)** モジュールを使用して列名を追加し、既定の列名を UCI サイト上のデータセットの説明で取得したものよりわかりやすい名前に置き換えます。 **Metadata Editor (メタデータ エディター)** の **[New column]\(新しい列\)** 名のフィールドに、新しい列名をコンマ区切り値として指定します。

次に、リスク予測モデルを開発するために使用するトレーニングとテストのセットを生成します。 **Split Data (データの分割)** モジュールを使用して、元のデータセットを同じサイズのトレーニングとテストのセットに分割します。 等しいサイズのセットを作成するため、**[Fraction of rows in the first output dataset]\(最初の出力データセットにおける列の割合\)** を 0.5 に設定します。

### <a name="generate-the-new-dataset"></a>新しいデータセットを生成する

リスクの過小評価によるコストは高くつくため、誤分類のコストを次のように設定します。

- 高リスクのケースを低リスクとして誤分類した場合:5
- 低リスクのケースを高リスクとして誤分類した場合:1

このコスト関数を反映するため、新しいデータセットを生成します。 新しいデータセットでは、高リスクのサンプルがそれぞれ 5 回レプリケートされますが、低リスクのサンプルの数は変わりません。 同じ行が両方のセットに存在することを防ぐため、レプリケートの前にデータをトレーニングとテストのデータセットに分割します。

高リスクのデータをレプリケートするには、この Python コードを **Execute Python Script (Python スクリプトの実行)** モジュールに挿入します。

```
import pandas as pd

def azureml_main(dataframe1 = None, dataframe2 = None):

    df_label_1 = dataframe1[dataframe1.iloc[:, 20] == 1]
    df_label_2 = dataframe1[dataframe1.iloc[:, 20] == 2]

    result = df_label_1.append([df_label_2] * 5, ignore_index=True)
    return result,
```

**Execute Python Script (Python スクリプトの実行)** モジュールでは、トレーニングとテストの両方のデータセットがレプリケートされます。

### <a name="feature-engineering"></a>特徴エンジニアリング

**Two-Class Support Vector Machine (2 クラス サポート ベクター マシン)** アルゴリズムには、正規化されたデータが必要です。 そのため、**Normalize Data (データの正規化)** モジュールを使用して、`tanh` 変換ですべての数値フィーチャーの範囲を正規化します。 `tanh` 変換は、値の全体的な分布を維持しながら、すべての数値フィーチャーを 0 と 1 の範囲内の値に変換します。

**Two-Class Support Vector Machine (2 クラス サポート ベクター マシン)** モジュールでは、文字列フィーチャーをカテゴリのフィーチャーに変換してから、0 または 1 の値を持つバイナリ フィーチャーに変換して処理します。 そのため、これらのフィーチャーを正規化する必要はありません。

## <a name="models"></a>モデル

**Two-Class Support Vector Machine (2 クラス サポート ベクター マシン)** (SVM) と **Two-Class Boosted Decision Tree (2 クラス ブースト デシジョン ツリー)** の 2 つの分類子に適用し、2 つのデータセットも使用しているため、合計で 4 つのモデルを生成します。

- 元のデータでトレーニングされた SVM。
- レプリケートしたデータでトレーニングされた SVM。
- 元のデータでトレーニングされたブースト デシジョン ツリー。
- レプリケートしたデータでトレーニングされたブースト デシジョン ツリー。

標準的な実験ワークフローを使用して、モデルを作成、トレーニング、テストします。

1. **Two-Class Support Vector Machine (2 クラス サポート ベクター マシン)** と **Two-Class Boosted Decision Tree (2 クラス ブースト デシジョン ツリー)** を使用して学習アルゴリズムを初期化します。
1. **Train Model (モデルのトレーニング)** を使用して、データにアルゴリズムを適用し、実際のモデルを作成します。
3. **Score Model (モデルのスコア付け)** を使用して、テスト サンプルを使用してスコアを生成しまうす。

次の図は、この実験の一部を示したもので、ここでは 2 つの異なる SVM モデルをトレーニングするために元のトレーニング セットとレプリケートされたトレーニング セットが使用されています。 **Train Model (モデルのトレーニング)** はトレーニング セットに接続され、**Score Model (モデルのスコア付け)** はテスト セットに接続されています。

![実験グラフ](media/ui-sample-classification-predict-credit-risk-cost-sensitive/score-part.png)


実験の評価ステージでは、これらの 4 つのモデルの精度をそれぞれ計算します。 この実験のために、**Evaluate Model (モデルの評価)** を使用して同じ誤分類のコストを持つサンプルを比較します。

**Evaluate Model (モデルの評価)** モジュールでは、2 つのスコア付けされたモデルと同じ数のパフォーマンス メトリックを計算できます。 そのため、**Evaluate Model (モデルの評価)** の 1 つのインスタンスを使って、2 つの SVM モデルと **Evaluate Model (モデルの評価)** のもう 1 つのインスタンスを評価して、2 つのブースト デシジョン ツリー モデルを評価します。

レプリケートされたテスト データセットが **Score Model (モデルのスコア付け)** の入力として使用されていることに注目してください。 つまり、最終的な精度のスコアには、間違ったラベルを取得するコストが含まれます。

## <a name="combine-multiple-results"></a>複数の結果を組み合わせる

**Evaluate Model (モデルの評価)** モジュールは、さまざまなメトリックを含む単一の行を持つテーブルを生成します。 精度の結果の 1 つのセットを作成するには、最初に **[Add Rows]\(行の追加\)** を使用して結果を 1 つのテーブルに結合します。 次に、**Execute Python Script (Python スクリプトの実行)** モジュールで次の Python スクリプトを使用して、結果のテーブル内の各行に対してモデル名とトレーニング アプローチを追加します。

```
import pandas as pd

def azureml_main(dataframe1 = None, dataframe2 = None):

    new_cols = pd.DataFrame(
            columns=["Algorithm","Training"],
            data=[
                ["SVM", "weighted"],
                ["SVM", "unweighted"],
                ["Boosted Decision Tree","weighted"],
                ["Boosted Decision Tree","unweighted"]
            ])

    result = pd.concat([new_cols, dataframe1], axis=1)
    return result,
```


## <a name="results"></a>結果

実験の結果を表示するには、最後の **Select Columns in Dataset (データセット内の列の選択)** モジュールの可視化出力を右クリックします。

![可視化出力](media/ui-sample-classification-predict-credit-risk-cost-sensitive/result.png)

最初の列には、モデルの生成に使用された機械学習アルゴリズムが一覧表示されます。
2 番目の列には、トレーニング セットの種類が示されます。
3 番目の列には、費用重視の精度の値が含まれます。

これらの結果から、最適な精度が、**Two-Class Support Vector Machine (2 クラス サポート ベクター マシン)** で作成されたモデルによって提供され、レプリケートされたトレーニング データセットでトレーニングされたことがわかります。

## <a name="clean-up-resources"></a>リソースのクリーンアップ

[!INCLUDE [aml-ui-cleanup](../../../includes/aml-ui-cleanup.md)]

## <a name="next-steps"></a>次の手順

ビジュアル インターフェイスで利用できるその他のサンプルを確認します。

- [サンプル 1 - 回帰:自動車の価格を予測する](ui-sample-regression-predict-automobile-price-basic.md)
- [サンプル 2 - 回帰:自動車の価格予測のためのアルゴリズムを比較する](ui-sample-regression-predict-automobile-price-compare-algorithms.md)
- [サンプル 3 - 分類:信用リスクを予測する](ui-sample-classification-predict-credit-risk-basic.md)
- [サンプル 5 - 分類:顧客離れを予測する](ui-sample-classification-predict-churn.md)
