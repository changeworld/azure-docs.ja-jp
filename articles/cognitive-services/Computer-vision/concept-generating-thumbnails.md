---
title: サムネイルの生成 - Computer Vision
titleSuffix: Azure Cognitive Services
description: Computer Vision API を使用するイメージ サムネイルの生成に関する概念。
services: cognitive-services
author: PatrickFarley
manager: cgronlun
ms.service: cognitive-services
ms.component: computer-vision
ms.topic: conceptual
ms.date: 11/30/2018
ms.author: pafarley
ms.openlocfilehash: 7d914f394ecfcf02ed26f41cd8fe2ef799cf6103
ms.sourcegitcommit: 5d837a7557363424e0183d5f04dcb23a8ff966bb
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/06/2018
ms.locfileid: "52966740"
---
# <a name="generating-thumbnails"></a>サムネイルの生成

サムネイルは、画像のサイズを縮小したものです。 無駄のないレイアウトしやすい方法で画像やその他のデータを表現するためにサムネイルは使用されます。 Computer Vision API は、画像のサイズ変更にスマート トリミングを併用することで、特定の画像の直感的なサムネイルを作成します。

Computer Vision のサムネイル生成アルゴリズムは次のように動作します。
1. 画像から邪魔な要素を取り除き、"_関心領域_" (画像内の主な被写体が写っている領域) を識別します。
1. 識別された "_関心領域_" に基づいて、画像をトリミングします。
1. ターゲットのサムネイルの寸法に合わせて縦横比を変更します。

## <a name="area-of-interest"></a>関心領域

画像をアップロードすると、Computer Vision API によって画像が分析されて "*関心領域*" が特定されます。 その領域を使用して画像のトリミング方法を決めることができます。 ただし縦横比が指定されている場合、トリミング操作は常に所定の縦横比と一致します。

**areaOfInterest** API を呼び出すと、その元の "*関心領域*" について未加工の境界ボックスの座標を取得することもできます。 その情報を使用して、元の画像に自由に変更を加えることができます。

## <a name="examples"></a>例

生成されるサムネイルは、以下の図に示すように、高さ、幅、スマート トリミングについて指定する内容によって大きく異なる場合があります。

![サムネイル](./Images/thumbnail-demo.png)

次の表は、Computer Vision で生成される、イメージ例に対する一般的なサムネイルを示しています。 スマート トリミングが有効になっており、ターゲットの高さと幅が 50 ピクセルに指定されたサムネイルが生成されています。

| イメージ | サムネイル |
|-------|-----------|
|![屋外の山](./Images/mountain_vista.png) | ![屋外の山のサムネイル](./Images/mountain_vista_thumbnail.png) |
|![Vision Analyze 花](./Images/flower.png) | ![Vision Analyze 花のサムネイル](./Images/flower_thumbnail.png) |
|![屋上にいる女性](./Images/woman_roof.png) | ![屋上にいる女性のサムネイル](./Images/woman_roof_thumbnail.png) |

## <a name="next-steps"></a>次の手順

[イメージへのタグ付け](concept-tagging-images.md)および[イメージの分類](concept-categorizing-images.md)について確認します。