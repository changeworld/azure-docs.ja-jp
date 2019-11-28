---
title: アルゴリズムとモジュールのリファレンス
description: Azure Machine Learning デザイナー (プレビュー) で使用可能なモジュールについて説明します
titleSuffix: Azure Machine Learning
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: peterclu
ms.author: peterlu
ms.date: 11/11/2019
ms.openlocfilehash: 21577936f2487313683ac763b0c6c661c6c1f958
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/13/2019
ms.locfileid: "74039336"
---
# <a name="algorithm--module-reference-for-azure-machine-learning-designer"></a>Azure Machine Learning デザイナーのアルゴリズムとモジュールのリファレンス

このリファレンス コンテンツでは、Azure Machine Learning デザイナー (プレビュー) で使用可能な各機械学習アルゴリズムとモジュールの技術的な背景について説明します。

各モジュールは、個別に実行可能なコードのセットを表し、必要な入力を取得して機械学習タスクを実行します。 モジュールには、特定のアルゴリズムが含まれているものや、機械学習において重要なタスク (欠損値の置換、統計分析など) を実行するものがあります。

> [!TIP]
> デザイナー内のパイプラインでは、特定のモジュールに関する情報を取得できます。 モジュールを選択し、 **[Quick Help]\(クイック ヘルプ\)** ウィンドウの **[more help]\(さらにヘルプを見る\)** リンクを選択します。

## <a name="modules"></a>モジュール

モジュールは、機能別に整理されています。

| 機能 | 説明 | モジュール |
| --- |--- | ---- |
| データの入力と出力 | クラウド ソースのデータをパイプラインに移動します。 パイプラインの実行中に、結果や中間データを Azure Storage、SQL データベース、または Hive に書き込みます。また、クラウド ストレージを使用して、パイプライン間でデータを交換します。  | [データのインポート](import-data.md) <br/> [データの手動入力](enter-data-manually.md) <br/>[データのエクスポート](export-data.md) |
| データの変換 | 機械学習に固有のデータに対する操作 (データの正規化やビン分割、次元削減、さまざまなファイル形式の間でのデータの変換など)。| [列の追加](add-columns.md) <br/> [行の追加](add-rows.md) <br/> [見つからないデータのクリーンアップ](clean-missing-data.md) <br/> [CSV への変換](convert-to-csv.md) <br/> [データセットへの変換](convert-to-dataset.md) <br/> [メタデータの編集](edit-metadata.md) <br/> [データの結合](join-data.md) <br/> [データの正規化](normalize-data.md) <br/> [重複する行の削除](remove-duplicate-rows.md) <br/> [列変換の選択](select-columns-transform.md) <br/> [データセット内の列の選択](select-columns-in-dataset.md) |
| サンプリング | 機械学習モデルをトレーニングおよびテストするため、データを 1 つ以上のサブセットに分割します。  | [モデルのクロス検証](cross-validate-model.md) <br/> [パーティションとサンプル](partition-and-sample.md) <br/> [SMOTE](smote.md) <br/> [データの分割](split-data.md) |
| 特徴選択 | 分析モデルの構築で使用する、関連する有用な特徴のサブセットを選択します。 | [フィルターに基づく特徴選択](filter-based-feature-selection.md) <br/> [順列の特徴量の重要度](permutation-feature-importance.md) |
| Python と R | コードを記述し、それをモジュール内に埋め込んで、Python と R をパイプラインと統合します。 | [Python モデルの作成](create-python-model.md) <br/> [Python スクリプトの実行](execute-python-script.md)   <br/>  [R スクリプトの実行](execute-r-script.md)
| Text Analytics | 構造化テキストと非構造化テキストの両方を操作するための特別な計算ツールを提供します。 | [テキストからの N Gram 特徴抽出](extract-n-gram-features-from-text.md) <br/> [特徴ハッシュ](feature-hashing.md) <br/> [テキストの前処理](preprocess-text.md) |
|  | **機械学習のアルゴリズム**: | |
| 分類 | クラスを予測します。  バイナリ (2 クラス) または多クラスのアルゴリズムを選択します。| [多クラス デシジョン フォレスト](multiclass-decision-forest.md) <br/> [多クラスの増幅デシジョン ツリー](multiclass-boosted-decision-tree.md) <br/> [多クラス ロジスティック回帰](multiclass-logistic-regression.md)  <br/> [多クラス ニューラル ネットワーク](multiclass-neural-network.md) <br/> [1 対全多クラス](one-vs-all-multiclass.md) <br/>  [2 クラス ロジスティック回帰](two-class-logistic-regression.md)  <br/>[2 クラス平均化パーセプトロン](two-class-averaged-perceptron.md) <br/> [2 クラス ブースト デシジョン ツリー](two-class-boosted-decision-tree.md)  <br/> [2 クラス デシジョン フォレスト](two-class-decision-forest.md)  <br/> [2 クラス ニューラル ネットワーク](two-class-neural-network.md) <br/> [2 クラス サポート ベクター マシン](two-class-support-vector-machine.md) | 
| クラスタリング | データをグループ化します。| [K-Means クラスタリング](k-means-clustering.md)
| 回帰 | 値を予測します。 | [ブースト デシジョン ツリー回帰](boosted-decision-tree-regression.md) <br/> [デシジョン フォレスト回帰](decision-forest-regression.md) <br/> [線形回帰](linear-regression.md)  <br/> [ニューラル ネットワーク回帰](neural-network-regression.md)  <br/> |
| レコメンダー | 推奨モデルを構築します。 | [レコメンダーの評価](evaluate-recommender.md) <br/> [SVD レコメンダーのスコア付け](score-svd-recommender.md) <br/> [SVD レコメンダーのトレーニング](train-SVD-recommender.md) |
|  | **モデルのビルドおよび評価**: | |
| トレーニング   | アルゴリズムを介してデータを実行します。 | [モデルのトレーニング](train-model.md)  <br/> [クラスタリング モデルのトレーニング](train-clustering-model.md) <br/>  [モデルのハイパーパラメーターの調整](tune-model-hyperparameters.md) |
| モデルの評価 | トレーニング済みモデルの正確性を測定します。 |  [モデルの評価](evaluate-model.md) |
| Score | トレーニングしたモデルから予測を取得します。 | [変換の適用](apply-transformation.md)<br/>[クラスターへのデータの割り当て](assign-data-to-clusters.md) <br/>[モデルのスコア付け](score-model.md) |
| 統計関数 | データ サイエンスに関連するさまざまな統計的方法を提供します。 | [算術演算の適用](apply-math-operation.md) <br/> [データの集計](summarize-data.md)|

## <a name="error-messages"></a>エラー メッセージ

Azure Machine Learning デザイナーでモジュールを使用しているときに発生する可能性のある[エラー メッセージと例外コード](machine-learning-module-error-codes.md)について説明します。
