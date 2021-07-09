---
title: スマート トリミングされたサムネイル - Computer Vision
titleSuffix: Azure Cognitive Services
description: Computer Vision API を使用するイメージ サムネイルの生成に関する概念。
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 03/11/2018
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: bcfaf250aa5ebf29f51fae22f87c14c29416107e
ms.sourcegitcommit: 80d311abffb2d9a457333bcca898dfae830ea1b4
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/26/2021
ms.locfileid: "110465333"
---
# <a name="generating-smart-cropped-thumbnails-with-computer-vision"></a>Computer Vision でのスマート トリミングされたサムネイルの生成

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

![山の画像とさまざまなトリミングの構成](./Images/thumbnail-demo.png)

次の表は、Computer Vision で生成される、イメージ例に対する一般的なサムネイルを示しています。 スマート トリミングが有効になっており、ターゲットの高さと幅が 50 ピクセルに指定されたサムネイルが生成されています。

| Image | サムネイル |
|-------|-----------|
|![山の夕日と人のシルエット](./Images/mountain_vista.png) | ![山の夕日と人のシルエットのサムネイル](./Images/mountain_vista_thumbnail.png) |
|![白い花と緑の背景](./Images/flower.png) | ![Vision Analyze 花のサムネイル](./Images/flower_thumbnail.png) |
|![共同住宅の屋上にいる女性](./Images/woman_roof.png) | ![共同住宅の屋上にいる女性のサムネイル](./Images/woman_roof_thumbnail.png) |

## <a name="use-the-api"></a>API の使用

サムネイルの生成機能は、[Get Thumbnail](https://westus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-2/operations/56f91f2e778daf14a499f20c) および [Get Area of Interest](https://westus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-2/operations/b156d0f5e11e492d9f64418d)で使用できます。 ネイティブ SDK または REST を呼び出すことでこの API を呼び出すことができます。 

* [クイック スタート: Computer Vision REST API またはクライアント ライブラリ](./quickstarts-sdk/image-analysis-client-library.md?pivots=programming-language-csharp)
