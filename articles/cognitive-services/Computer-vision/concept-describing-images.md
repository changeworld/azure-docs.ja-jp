---
title: 画像の説明 - Computer Vision
titleSuffix: Azure Cognitive Services
description: Computer Vision API の画像説明機能に関連する概念。
services: cognitive-services
author: PatrickFarley
manager: cgronlun
ms.service: cognitive-services
ms.component: computer-vision
ms.topic: conceptual
ms.date: 08/29/2018
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: 5b920e4ce8df131b81a9ef6ce2d66c7082d8f5e4
ms.sourcegitcommit: 7cd706612a2712e4dd11e8ca8d172e81d561e1db
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/18/2018
ms.locfileid: "53583410"
---
# <a name="describe-images-with-human-readable-language"></a>人間が判読できる言語で画像を説明する

Computer Vision のアルゴリズムでは、画像の内容が分析されます。 この分析では、完全な文章で人間が判読できる言語として表示される "説明" の基礎が形成されます。 説明には、イメージで検出された内容の概要が示されます。 Computer Vision のアルゴリズムでは、画像で識別された視覚的特徴に基づいて、さまざまな説明が生成されます。 各説明は評価されて、信頼度スコアが生成されます。 その後、信頼度スコアが最も高いものから最も低いものの順に並べられたリストが返されます。

## <a name="image-description-example"></a>画像の説明の例

次の JSON 応答は、視覚的特徴に基づいてサンプル画像を説明するときに、Computer Vision から返される内容を示します。

![マンハッタンのビル群の白黒写真](./Images/bw_buildings.png)

```json
{
    "description": {
        "tags": ["outdoor", "building", "photo", "city", "white", "black", "large", "sitting", "old", "water", "skyscraper", "many", "boat", "river", "group", "street", "people", "field", "tall", "bird", "standing"],
        "captions": [
            {
                "text": "a black and white photo of a city",
                "confidence": 0.95301952483304808
            },
            {
                "text": "a black and white photo of a large city",
                "confidence": 0.94085190563213816
            },
            {
                "text": "a large white building in a city",
                "confidence": 0.93108362931954824
            }
        ]
    },
    "requestId": "b20bfc83-fb25-4b8d-a3f8-b2a1f084b159",
    "metadata": {
        "height": 300,
        "width": 239,
        "format": "Jpeg"
    }
}
```

## <a name="next-steps"></a>次の手順

[イメージへのタグ付け](concept-tagging-images.md)および[イメージの分類](concept-categorizing-images.md)に関する概念を確認します。