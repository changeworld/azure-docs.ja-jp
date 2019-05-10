---
title: アルゴリズムとモジュールのリファレンス
titleSuffix: Azure Machine Learning service
description: Azure Machine Learning のビジュアル インターフェイスで使用可能なモジュールについて説明します
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/02/2019
ms.openlocfilehash: 6602eb4bacdc3b6382c1b6873a465cdfc0632693
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/02/2019
ms.locfileid: "65027785"
---
# <a name="algorithm--module-reference-overview"></a>アルゴリズムとモジュールのリファレンスの概要

このリファレンス コンテンツでは、Azure Machine Learning service のビジュアル インターフェイス (プレビュー) で使用可能な各機械学習アルゴリズムとモジュールの技術的な背景について説明します。 

各モジュールは、個別に実行可能なコードのセットを表し、必要な入力を取得して機械学習タスクを実行します。 モジュールには、特定のアルゴリズムが含まれているものや、機械学習において重要なタスク (欠損値の置換、統計分析など) を実行するものがあります。 

> [!TIP]
> ビジュアル インターフェイス内の実験では、特定のモジュールに関する情報を取得できます。 モジュールを選択し、**[Quick Help]\(クイック ヘルプ\)** ウィンドウの **[more help]\(さらにヘルプを見る\)** リンクを選択します。

モジュールは、機能別に整理されています。

**データ形式の変換**

  + [CSV への変換](convert-to-csv.md)

**データの入力と出力のモジュール**は、クラウド ソースから実験へデータを移行する処理を行います。 実験の実行中に、結果や中間データを Azure Storage、SQL データベース、または Hive に書き込むことができます。また、クラウド ストレージを使用して、複数の実験間でデータを交換できます。  

  + [データのインポート](import-data.md)

  + [データのエクスポート](export-data.md)

  + [データの手動入力](enter-data-manually.md)


**データ変換モジュール**は、機械学習に固有のデータに対する操作 (データの正規化やビン分割、特徴選択、次元削減など) をサポートします。

  + [データセット内の列の選択](select-columns-in-dataset.md)

  + [メタデータの編集](edit-metadata.md)

  + [見つからないデータのクリーンアップ](clean-missing-data.md)

  + [列の追加](add-columns.md)

  + [行の追加](add-rows.md)

  + [重複する行の削除](remove-duplicate-rows.md)

  + [データの分割](split-data.md)

  + [データの正規化](normalize-data.md)

  + [パーティションとサンプル](partition-and-sample.md)


**機械学習アルゴリズム** (クラスタリング、サポート ベクター マシン、ニューラル ネットワークなど) は、適切なパラメーターを使用して機械学習タスクをカスタマイズ可能な個別のモジュール内で使用できます。  
  + [モデルのスコア付け](score-model.md)

  + [クラスターへのデータの割り当て](assign-data-to-clusters.md)

  + [モデルのトレーニング](train-model.md)

  + [クラスタリング モデルのトレーニング](train-clustering-model.md)

  + [モデルの評価](evaluate-model.md)

  + [変換の適用](apply-transformation.md)

  + [線形回帰](linear-regression.md)

  + [ニューラル ネットワーク回帰](neural-network-regression.md)

  + [デシジョン フォレスト回帰](decision-forest-regression.md)

  + [ブースト デシジョン ツリー回帰](boosted-decision-tree-regression.md)

  + [2 クラス ブースト デシジョン ツリー](two-class-boosted-decision-tree.md)

  + [2 クラス ロジスティック回帰](two-class-logistic-regression.md)

  + [多クラス ロジスティック回帰](multiclass-logistic-regression.md)

  + [多クラス ニューラル ネットワーク](multiclass-neural-network.md)

  + [多クラス デシジョン フォレスト](multiclass-decision-forest.md)

  + [2 クラス平均化パーセプトロン](two-class-averaged-perceptron.md)

  + [2 クラス デシジョン フォレスト](two-class-decision-forest.md)

  + [2 クラス ニューラル ネットワーク](two-class-neural-network.md)

  + [2 クラス サポート ベクター マシン](two-class-support-vector-machine.md)
  
  + [K-Means クラスタリング](k-means-clustering.md)


**Python モジュール**により、カスタム関数を簡単に実行できます。 コードを記述してモジュール内に埋め込み、Python と実験サービスを統合します。
  + [Python スクリプトの実行](execute-python-script.md)

  + [Python モデルの作成](create-python-model.md)


