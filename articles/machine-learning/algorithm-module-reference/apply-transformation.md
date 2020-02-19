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
ms.date: 02/11/2020
ms.openlocfilehash: 443b021b266a202775e94e44acac3a91a2b70617
ms.sourcegitcommit: b95983c3735233d2163ef2a81d19a67376bfaf15
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/11/2020
ms.locfileid: "77137576"
---
# <a name="apply-transformation-module"></a>変換の適用モジュール

この記事では Azure Machine Learning デザイナー (プレビュー) 内のモジュールについて説明します。

このモジュールを使用して、事前に計算された変換に基づいて入力データセットを変更します。  
  
たとえば、**データの正規化**モジュールを使用してトレーニング データを正規化するために z スコアを使用した場合は、スコアリング フェーズ中も、トレーニング用に計算された z スコア値を使用することができます。 Azure Machine Learning では、正規化方法を変換として保存し、**変換の適用**を使用して、スコアリングの前の入力データに z スコアを適用することができます。
  
Azure Machine Learning には、作成に関するサポート、およびさまざまな種類のカスタム変換の適用に関するサポートが用意されています。 たとえば、変換を保存し再使用して、次のことを実行できます。  
  
- **[見つからないデータのクリーンアップ]** を使用して、欠落している値を削除したり置換する
- **[データの正規化]** を使用してデータを正規化する
  

## <a name="how-to-use-apply-transformation"></a>変換の適用の使用方法  
  
1. **Apply Transformation (変換の適用)** モジュールをパイプラインに追加します。 このモジュールは、 **[モデルのスコアリングと評価]** カテゴリにあります。 
  
2. 入力として使用する既存の変換を見つけます。 以前に保存された変換は、左側のモジュール ツリーの **[データセット]** カテゴリの **[My Datasets]\(マイ データセット\)** グループにあります。  
  
   
  
3. 変換するデータセットを接続します。 そのデータセットには、この変換を最初に設計した対象データセットとまったく同じスキーマ (列数、列名、データ型) があるはずです。  
  
4. 変換を定義しているときにすべてのカスタマイズが行われているため、他のパラメーターを設定する必要はありません。  
  
5. 新しいデータセットに変換を適用するには、パイプラインを実行します。  

## <a name="next-steps"></a>次のステップ

Azure Machine Learning で[使用できる一連のモジュール](module-reference.md)を参照してください。 