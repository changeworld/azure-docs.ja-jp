---
title: 'Select Columns Transform: コンポーネント リファレンス'
titleSuffix: Azure Machine Learning
description: Azure Machine Learning デザイナーで Select Columns Transform (列変換の選択) コンポーネントを使用し、選択変換する方法について説明します。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 10/10/2020
ms.openlocfilehash: e29f8ba9224390be51c3f29bc255a554b96591ad
ms.sourcegitcommit: e41827d894a4aa12cbff62c51393dfc236297e10
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/04/2021
ms.locfileid: "131565627"
---
# <a name="select-columns-transform"></a>Select Columns Transform (列変換の選択)

この記事では、Azure Machine Learning デザイナーで Select Columns Transform (列変換の選択) コンポーネントを使用する方法について説明します。 Select Columns Transform (列変換の選択) コンポーネントの目的は、予測可能で一貫性のある一連の列がダウンストリームの機械学習操作で使用されるようにすることです。

このコンポーネントは、特定の列を必要とするスコアリングなどのタスクに役立ちます。 使用可能な列を変更すると、パイプラインが中断されたり、結果が変わったりする可能性があります。

Select Columns Transform (列変換の選択) を使用して、一連の列を作成して保存します。 次に、[Apply Transformation](apply-transformation.md) (変換の適用) コンポーネントを使用してそれらの選択を新しいデータに適用します。

## <a name="how-to-use-select-columns-transform"></a>Select Columns Transform (列変換の選択) の使用方法

このシナリオでは、特徴選択を使用して、モデルのトレーニングに使用する列の動的セットを生成することを前提としています。 列の選択がスコアリング プロセスで同じになるように、Select Columns Transform (列変換の選択) コンポーネントを使用して列の選択をキャプチャし、パイプラインの他の場所にそれらを適用します。

1. デザイナーでパイプラインに入力データセットを追加します。

2. [Filter Based Feature Selection](filter-based-feature-selection.md) (フィルターに基づく特徴選択) のインスタンスを追加します。

3. コンポーネントを接続し、特徴選択コンポーネントを構成して、入力データセット内でいくつかの最適な特徴を自動的に検出します。

4. [Train Model](train-model.md) (モデルのトレーニング) のインスタンスを追加し、[Filter Based Feature Selection](filter-based-feature-selection.md) (フィルターに基づく特徴選択) の出力をトレーニングの入力として使用します。

    > [!IMPORTANT]
    > 特徴量の重要度は列の値に基づくため、[Train Model](train-model.md) (モデルのトレーニング) への入力に使用できる列を事前に把握することはできません。  
5. Select Columns Transform (列変換の選択) コンポーネントのインスタンスを接続します。 

    この手順により、他のデータセットに保存または適用できる変換として列の選択が生成されます。 この手順により、特徴選択で識別された列が、他のコンポーネントで再利用できるように保存されます。

6. [[Score Model]\(モデルのスコア付け\)](score-model.md) コンポーネントを追加します。 

   *入力データセットは接続しないでください。* 代わりに、[Apply Transformation](apply-transformation.md) (変換の適用) コンポーネントを追加し、特徴選択変換の出力を接続します。

   パイプライン構造は次のようになります。

   > [!div class="mx-imgBorder"]
   > ![サンプル パイプライン](media/module/filter-based-feature-selection-score.png)

   > [!IMPORTANT]
   > [Filter Based Feature Selection](filter-based-feature-selection.md) (フィルターに基づく特徴選択) をスコアリング データセットに適用しても、同じ結果が得られるわけではありません。 特徴選択は値に基づくため、異なる列セットが選択される可能性があります。その場合、スコアリング操作は失敗します。
    
7. パイプラインを送信します。

列選択を保存してから適用するこのプロセスにより、同じデータ スキーマをトレーニングとスコアリングに使用することができます。


## <a name="next-steps"></a>次のステップ

Azure Machine Learning で[使用できる一連のコンポーネント](component-reference.md)を参照してください。 
