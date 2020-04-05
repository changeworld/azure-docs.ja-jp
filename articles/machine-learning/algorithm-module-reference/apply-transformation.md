---
title: 変換の適用:モジュール リファレンス
titleSuffix: Azure Machine Learning
description: Azure Machine Learning で変換の適用モジュールを使用して、事前に計算された変換に基づいて入力データセットを変更する方法について説明します。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 03/05/2020
ms.openlocfilehash: ccf9d0c3eef50c7dfd838f1929e52506e8984879
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/28/2020
ms.locfileid: "78395267"
---
# <a name="apply-transformation-module"></a>変換の適用モジュール

この記事では Azure Machine Learning デザイナー (プレビュー) 内のモジュールについて説明します。

このモジュールを使用して、事前に計算された変換に基づいて入力データセットを変更します。

たとえば、**データの正規化**モジュールを使用してトレーニング データを正規化するために z スコアを使用した場合は、スコアリング フェーズ中も、トレーニング用に計算された z スコア値を使用することができます。 Azure Machine Learning では、正規化方法を変換として保存し、**変換の適用**を使用して、スコアリングの前の入力データに z スコアを適用することができます。

## <a name="how-to-save-transformations"></a>変換を保存する方法

デザイナーを使用すると、データ変換を **データセット** として保存し、他のパイプラインで使用できるようになります。

1. 正常に実行されたデータ変換モジュールを選択します。

1. **[出力 + ログ]** タブを選択します。

1. **[保存アイコン]** を選択して、 **[結果変換]** を保存します。

## <a name="how-to-use-apply-transformation"></a>変換の適用の使用方法  
  
1. **Apply Transformation (変換の適用)** モジュールをパイプラインに追加します。 このモジュールは、モジュール パレットの **[モデルのスコアリングと評価]** セクションにあります。 
  
1. モジュール パレットで **Datasets** > **My Datasets**で使用する保存済みの変換を検出します。

1. 保存した変換の出力を **Apply Transformation (変換の適用)** の左側の入力ポートに接続します。

    そのデータセットには、この変換を最初に設計した対象データセットとまったく同じスキーマ (列数、列名、データ型) があるはずです。  
  
1. 目的のモジュールのデータセット出力を **Apply Transformation (変換の適用)** モジュールの右側の入力ポートに接続します。
  
1. 新しいデータセットに変換を適用するには、パイプラインを実行します。  

## <a name="next-steps"></a>次のステップ

Azure Machine Learning で[使用できる一連のモジュール](module-reference.md)を参照してください。 