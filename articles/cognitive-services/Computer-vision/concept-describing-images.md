---
title: 画像の説明 - Computer Vision
titleSuffix: Azure Cognitive Services
description: Computer Vision API を使用した画像の記述に関連する概念。
services: cognitive-services
author: PatrickFarley
manager: cgronlun
ms.service: cognitive-services
ms.component: computer-vision
ms.topic: conceptual
ms.date: 08/29/2018
ms.author: pafarley
ms.openlocfilehash: 423d1be57bc800108a08a81b72587ca2711bbc3d
ms.sourcegitcommit: 1aacea6bf8e31128c6d489fa6e614856cf89af19
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/16/2018
ms.locfileid: "49342417"
---
# <a name="describing-images"></a>イメージの説明

Computer Vision のアルゴリズムでは、画像の内容が分析されます。 この分析では、完全な文章で人間が判読できる言語として表示される "説明" の基礎が形成されます。 説明には、イメージで検出された内容の概要が示されます。 Computer Vision のアルゴリズムでは、画像で識別された視覚的特徴に基づいて、さまざまな説明が生成されます。 各説明は評価されて、信頼度スコアが生成されます。 その後、信頼度スコアが最も高いものから最も低いものの順に並べられたリストが返されます。

## <a name="image-description-example"></a>画像の説明の例

次の JSON 応答は、視覚的特徴に基づいてサンプル画像を説明するときに、Computer Vision から返される内容を示します。

![白黒のビル](./Images/bw_buildings.png)

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