---
title: 'デザイナー: 書籍レビュー分類の例'
titleSuffix: Azure Machine Learning
description: Azure Machine Learning デザイナーを使用して、マルチクラス ロジスティック回帰分類器を構築し、Wikipedia SP 500 データセットで会社のカテゴリを予測します。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
author: xiaoharper
ms.author: zhanxia
ms.reviewer: peterlu
ms.date: 11/04/2019
ms.openlocfilehash: 16253abce2940690a80f84aa5b68521c09212bb9
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/20/2019
ms.locfileid: "74213760"
---
# <a name="build-a-classifier-to-predict-company-category-using-azure-machine-learning-designer"></a>Azure Machine Learning デザイナーを使用して、会社のカテゴリを予測する分類器を作成する

**デザイナー (プレビュー) サンプル 7**

[!INCLUDE [applies-to-skus](../../../includes/aml-applies-to-enterprise-sku.md)]

このサンプルでは、Azure Machine Learning デザイナー (プレビュー) でテキスト分析モジュールを使用してテキスト分類パイプラインを構築する方法を示します。

テキスト分類の目的は、いくつかのテキストを 1 つまたは複数のクラスまたはカテゴリに割り当てることです。 テキストには、ドキュメント、ニュース記事、検索クエリ、電子メール、ツイート、サポート チケット、顧客フィードバック、ユーザーの製品レビューなどがあります。テキスト分類の用途には、新聞記事やニュース ワイヤーのコンテンツをトピックに分類する、Web ページを階層的なカテゴリに整理する、スパム メールのフィルター処理、センチメント分析、検索クエリからユーザーの意図を予測する、サポート チケットのルーティング、顧客フィードバックの分析などがあります。 

このパイプラインを使って、**マルチクラス ロジスティック回帰分類器**をトレーニングし、**Wikipedia から取得した Wikipedia SP 500 データセット**で会社のカテゴリを予測します。  

テキスト データを使用したトレーニング機械学習モデルの基本的な手順は次のとおりです。

1. データを取得する

1. テキスト データを前処理する

1. 特徴エンジニアリング

   特徴ハッシュなどの特徴抽出モジュールを使用してテキスト特徴を数値特徴に変換し、テキスト データから N-gram 特徴を抽出します。

1. モデルをトレーニングする

1. データセットをスコア付けする

1. モデルを評価する

これから取り組むパイプラインの最終的な完成したグラフを次に示します。 ご自身で同様の決定を下すことができるように、ここではすべてのモジュールの理論的な根拠を提示します。

[![パイプラインのグラフ](./media/how-to-designer-sample-text-classification/nlp-modules-overall.png)](./media/how-to-designer-sample-text-classification/nlp-modules-overall.png#lightbox)

## <a name="data"></a>Data

このパイプラインでは、**Wikipedia SP 500** データセットを使用します。 データセットは、S&P 500 企業それぞれの記事に基づいて Wikipedia (https://www.wikipedia.org/) ) から取得したものです。 Azure Machine Learning デザイナーにアップロードする前に、データセットは次のように処理されています。

- 特定の企業のテキスト コンテンツを抽出します。
- Wiki の書式設定を削除します。
- 英数字以外の文字を削除します。
- すべてのテキストを小文字に変換します。
- 既知の会社のカテゴリを追加します。

いくつかの企業については記事が見つからないため、レコード数は 500 未満です。

## <a name="pre-process-the-text-data"></a>テキスト データを前処理する

**[Preprocess Text]\(テキストの前処理\)** モジュールを使用してテキスト データを前処理します。これには、文の検出、文のトークン化などが含まれます。 サポートされているすべてのオプションについては、「[**Preprocess Text (テキストの前処理)** ](../algorithm-module-reference/preprocess-text.md)」の記事を参照してください。 テキスト データを前処理した後、 **[Split Data]\(データの分割\)** モジュールを使用して、トレーニング データセットが元のデータの 50% を含み、テスト データセットが元のデータの 50% を含むように、入力データをランダムに分割します。

## <a name="feature-engineering"></a>特徴エンジニアリング
このサンプルでは、2 つの手法を用いて特徴エンジニアリングを実行します。

### <a name="feature-hashing"></a>特徴ハッシュ
[ **[Feature Hashing]\(特徴ハッシュ\)** ](../algorithm-module-reference/feature-hashing.md) モジュールを使用して記事のプレーン テキストを整数に変換し、整数値をモデルへの入力特徴として使用しました。 

**[Feature Hashing]\(特徴ハッシュ\)** モジュールは、Vowpal Wabbit ライブラリによって提供される 32 ビット murmurhash v3 ハッシュ法を使用して、可変長のテキスト ドキュメントを等しい長さの数値特徴ベクトルに変換するために使用できます。 特徴ハッシュを使用する目的は、ディメンションの削減です。また特徴ハッシュは、文字列比較の代わりにハッシュ値比較を使用するため、分類時に特徴の重みのルックアップを高速化します。

サンプルのパイプラインでは、ハッシュ化ビットの数を 14 に設定し、N-gram の数を 2 に設定します。 これらの設定により、ハッシュ テーブルは 2^14 エントリを保持できます。それぞれのハッシュ化特徴は 1 つ以上の N-gram 特徴を表し、その値はテキスト インスタンスにおけるその N-gram の出現頻度を表します。 多くの問題にとって、このサイズのハッシュ テーブルは十分に適正ですが、競合を避けるためにより多くの領域が必要になる場合もあります。 さまざまなビット数を使用して、機械学習ソリューションのパフォーマンスを評価します。 

### <a name="extract-n-gram-feature-from-text"></a>テキストからの N-gram 特徴抽出

N-gram は、テキストの特定のシーケンスからの n 個の用語の隣接したシーケンスです。 サイズ 1 の N-gram はユニグラム、サイズ 2 の N-gram はバイグラム、サイズ 3 の N-gram はトライグラムとそれぞれ呼ばれます。 より大きなサイズの N-gram を、"4 グラム"、"5 グラム" のように n の値によって呼ぶ場合があります。

特徴エンジニアリングのための別のソリューションとして、[ **[Extract N-Gram Feature from Text]\(テキストからの N-gram 特徴抽出\)** ](../algorithm-module-reference/extract-n-gram-features-from-text.md) モジュールを使用しました。 このモジュールは、まず N-gram のセットを抽出し、N-gram に加えて、それぞれの N-gram がテキストに出現するドキュメントの数がカウントされます (DF)。 このサンプルでは、特徴値を計算するために TF-IDF メトリックが使用されます。 次に、非構造化テキスト データを等しい長さの数値特徴ベクトルに変換します。それぞれの特徴は、テキスト インスタンス内の N-gram の TF-IDF を表します。

テキスト データを数値特徴ベクトルに変換した後、テキスト データをデータセットから削除するために **[Select Column]\(列の選択\)** モジュールが使用されます。 

## <a name="train-the-model"></a>モデルをトレーニングする

アルゴリズムの選択は、多くの場合、ユース ケースの要件によって異なります。 このパイプラインの目標は会社のカテゴリを予測することであるため、マルチクラス分類器モデルが良い選択肢です。 特徴の数が多く、これらの特徴が疎であることを考慮して、このパイプラインには**マルチクラス ロジスティック回帰**モデルを使用します。

## <a name="test-evaluate-and-compare"></a>テスト、評価、比較を行う

 モデルの評価をより客観的に行うために、データセットを分割し、異なるデータセットを使用してモデルをトレーニングおよびテストします。

モデルのトレーニングが終わったら、 **[Score Model]\(モデルのスコア付け\)** モジュールと **[Evaluate Model]\(モデルの評価\)** モジュールを使用して予測結果を生成し、モデルを評価します。 ただし、 **[Score Model]\(モデルのスコア付け\)** モジュールを使用する前に、トレーニング中に行ったような特徴エンジニアリングを実行することを要求されます。 

**[Feature Hashing]\(特徴ハッシュ\)** モジュールの場合、スコア付けフローでトレーニング フローのように特徴エンジニアリングを実行するのは簡単です。 **[Feature Hashing]\(特徴ハッシュ\)** モジュールを直接使用して、入力テキスト データを処理します。

**[Extract N-Gram Feature from Text]\(テキストからの N-gram 特徴抽出\)** モジュールの場合、トレーニング データフローからの **[Result Vocabulary output]\(結果ボキャブラリ出力\)** をスコア付けデータフローの **[Input Vocabulary]\(入力ボキャブラリ\)** に接続し、 **[Vocabulary mode]\(ボキャブラリ モード\)** パラメーターを **[ReadOnly]\(読み取り専用\)** に設定します。
[![N-gram スコアのグラフ](./media/how-to-designer-sample-text-classification/n-gram.png)](./media/how-to-designer-sample-text-classification/n-gram.png)

エンジニアリング手順を完了した後、 **[Score Model]\(モデルのスコア付け\)** を使用すると、トレーニングされたモデルを用いることによってテスト データセットに対して予測を生成できます。 結果を確認するには、 **[Score Model]\(モデルのスコア付け\)** の出力ポートを選択し、 **[Visualize]\(可視化\)** を選択します。

次に、そのスコアを**モデルの評価**モジュールに渡して評価メトリックを生成します。 **[Evaluate Model]\(モデルの評価\)** には 2 つの入力ポートがあるため、異なる手法を用いて生成されたスコア付きデータセットを評価および比較できます。 このサンプルでは、特徴ハッシュ法と N-gram 法を用いて生成された結果のパフォーマンスを比較します。
結果を確認するには、 **[Evaluate Model]\(モデルの評価\)** の出力ポートを選択し、 **[Visualize]\(可視化\)** を選択します。

## <a name="clean-up-resources"></a>リソースのクリーンアップ

[!INCLUDE [aml-ui-cleanup](../../../includes/aml-ui-cleanup.md)]

## <a name="next-steps"></a>次の手順

デザイナーで利用できる他のサンプルを確認します。
- [サンプル 1 - 回帰: 自動車の価格を予測する](how-to-designer-sample-regression-automobile-price-basic.md)
- [サンプル 2 - 回帰: 自動車の価格予測のためのアルゴリズムを比較する](how-to-designer-sample-regression-automobile-price-compare-algorithms.md)
- [サンプル 3 -特徴選択による分類: 収入予測](how-to-designer-sample-classification-predict-income.md)
- [サンプル 4 - 分類: 信用リスクを予測する (費用重視)](how-to-designer-sample-classification-credit-risk-cost-sensitive.md)
- [サンプル 5 - 分類:顧客離れを予測する](how-to-designer-sample-classification-churn.md)
- [サンプル 6 - 分類:フライトの遅延を予測する](how-to-designer-sample-classification-flight-delay.md)
