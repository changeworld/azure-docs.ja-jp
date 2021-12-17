---
title: ResNet
titleSuffix: Azure Machine Learning
description: Azure Machine Learning デザイナーで ResNet アルゴリズムを使用してイメージ分類モデルを作成する方法について説明します。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 09/26/2020
ms.openlocfilehash: c6be11cc4dafb5423d38bc869fc8b915b48814ce
ms.sourcegitcommit: e41827d894a4aa12cbff62c51393dfc236297e10
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/04/2021
ms.locfileid: "131565576"
---
# <a name="resnet"></a>ResNet

この記事では、Azure Machine Learning デザイナーの **ResNet** コンポーネントを使用して、ResNet アルゴリズムを用いてイメージ分類モデルを作成する方法について説明します。  

この分類アルゴリズムは、教師あり学習手法であり、ラベル付きデータセットが必要です。 
> [!NOTE]
> このコンポーネントは、スタジオの *[データのラベル付け]* から生成されたラベル付きデータセットはサポートしておらず、[[Convert to Image Directory]\(イメージ ディレクトリへの変換\)](convert-to-image-directory.md) コンポーネントから生成されたラベル付きイメージ ディレクトリのみサポートしています。 

モデルとラベル付きイメージ ディレクトリを [Train Pytorch Model](train-pytorch-model.md) への入力として提供することにより、モデルをトレーニングすることができます。 その後、トレーニングされたモデルを使用して、[Score Image Model](score-image-model.md) を使用した新しい入力例の値を予測することができます。

### <a name="more-about-resnet"></a>ResNet の詳細

ResNet の詳細については、[こちらの資料](https://pytorch.org/vision/stable/models.html#torchvision.models.resnext101_32x8d)を参照してください。

## <a name="how-to-configure-resnet"></a>ResNet を構成する方法

1.  デザイナーで、**ResNet** コンポーネントをパイプラインに追加します。  

2.  **Model name** には、特定の ResNet 構造体の名前を指定します。サポートされている次の resnet から選択できます: "resnet18"、"resnet34"、"resnet50"、"resnet101"、"resnet152"、"resnet152"、"resnext50\_32x4d"、"resnext101\_32x8d"、"wide_resnet50\_2"、"wide_resnet101\_2"。

3.  **Pretrained** には、ImageNet で事前トレーニングされているモデルを使用するかどうかを指定します。 選択した場合は、選択した事前トレーニング済みモデルに基づいてモデルを微調整できます。選択を解除した場合は、ゼロからトレーニングできます。

4.  **[Zero init residual]\(残余のゼロ初期化\)** には、残余ブランチごとに、最後のバッチ正規化レイヤーをゼロ初期化するかどうかを指定します。 選択した場合、残余ブランチはゼロで始まり、それぞれの残余ブロックは識別子のように動作します。 このことは、 https://arxiv.org/abs/1706.02677 によると、大きなバッチ サイズでの収束に役立ちます。

5.  トレーニングと検証のイメージ データセット コンポーネントである **ResNet** コンポーネントの出力を [Pytorch モデルのトレーニング](train-pytorch-model.md)に接続します。 

6.  パイプラインを送信します。

## <a name="results"></a>結果

パイプラインの実行が完了した後、モデルをスコア付けに使用するには、[PyTorch モデルのトレーニング](train-pytorch-model.md)を[画像モデルのスコア付け](score-image-model.md)に接続し、新しい入力例の値を予測します。

## <a name="technical-notes"></a>テクニカル ノート  

###  <a name="component-parameters"></a>コンポーネントのパラメーター  

| 名前       | Range | Type    | Default           | 説明                              |
| ---------- | ----- | ------- | ----------------- | ---------------------------------------- |
| モデル名 | Any   | モード    | resnext101\_32x8d | 特定の ResNet 構造体の名前       |
| Pretrained | Any   | Boolean | True              | ImageNet で事前トレーニング済みモデルを使用するかどうか |
| Zero init residual (残余のゼロ初期化) | Any | Boolean | False | 残余ブランチごとに、最後のバッチ正規化レイヤーをゼロ初期化するかどうか |
|            |       |         |                   |                                          |

###  <a name="output"></a>出力  

| 名前            | 種類                    | 説明                              |
| --------------- | ----------------------- | ---------------------------------------- |
| 未トレーニング モデル | UntrainedModelDirectory | Train Pytorch Model に接続できる未トレーニングの ResNet モデル。 |

## <a name="next-steps"></a>次のステップ

Azure Machine Learning で[使用できる一連のコンポーネント](component-reference.md)を参照してください。 