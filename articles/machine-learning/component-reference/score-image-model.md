---
title: 画像モデルのスコア付けコンポーネントを使用する
titleSuffix: Azure Machine Learning
description: Azure Machine Learning で画像モデルのスコア付けコンポーネントを使用して、トレーニングされた画像モデルにより予測を生成する方法について説明します。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 05/26/2020
ms.openlocfilehash: 57cd0fb2b4c64010955f189b9cf28ced54883637
ms.sourcegitcommit: e41827d894a4aa12cbff62c51393dfc236297e10
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/04/2021
ms.locfileid: "131565683"
---
# <a name="score-image-model"></a>画像モデルのスコア付け

この記事では Azure Machine Learning デザイナーのコンポーネントについて説明します。

入力画像データに対してトレーニングされた画像モデルを使用して予測を生成するには、このコンポーネントを使用します。

## <a name="how-to-configure-score-image-model"></a>画像モデルのスコア付けを構成する方法

1. **Score Image Model (画像モデルのスコア付け)** コンポーネントをパイプラインに追加します。

2. トレーニングされた画像モデルと入力画像データを含むデータセットをアタッチします。 

    データは ImageDirectory 型である必要があります。 イメージ ディレクトリを取得する方法の詳細については、「[イメージ ディレクトリへの変換](convert-to-image-directory.md)」を参照してください。 また、一般に、入力データセットのスキーマはモデルのトレーニングに使用されたデータのスキーマと一致している必要があります。

3. パイプラインを送信します。

## <a name="results"></a>結果

[画像モデルのスコア付け](score-image-model.md)を使用して一連のスコアを生成したら、モデルの精度 (パフォーマンス) を評価するために使用される一連のメトリックを生成するために、このコンポーネントとスコア付けされたデータセットを接続して[モデルを評価](evaluate-model.md)することができます。 

### <a name="publish-scores-as-a-web-service"></a>Web サービスとしてスコアを公開する

スコア付けの一般的な用途は、予測 Web サービスの一部として出力を返すことです。 詳細については、Azure Machine Learning デザイナーでのパイプラインに基づいたリアルタイム エンドポイントのデプロイ方法に関する[このチュートリアル](../tutorial-designer-automobile-price-deploy.md)を参照してください。

## <a name="next-steps"></a>次のステップ

Azure Machine Learning で[使える一連のコンポーネント](component-reference.md)を参照してください。