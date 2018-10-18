---
title: 顔の検出 - Computer Vision
titleSuffix: Azure Cognitive Services
description: Computer Vision API を使用した顔の検出に関連する概念。
services: cognitive-services
author: PatrickFarley
manager: cgronlun
ms.service: cognitive-services
ms.component: computer-vision
ms.topic: conceptual
ms.date: 08/29/2018
ms.author: pafarley
ms.openlocfilehash: bf358d1e8f60f989ced8db966bbf0a5179fab25b
ms.sourcegitcommit: 1aacea6bf8e31128c6d489fa6e614856cf89af19
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/16/2018
ms.locfileid: "49342315"
---
# <a name="detecting-faces"></a>顔の検出

Computer Vision は、写真の中から人間の顔を検出し、検出された各顔の年齢、性別、四角形を生成します。 Computer Vision は、[[Face]](/azure/cognitive-services/face/) で見つけられる機能のサブセットを提供しており、顔の識別やポーズの検出など、より詳細な分析に Face サービスを使用できます。  

## <a name="face-detection-examples"></a>顔検出の例

最初の例では、人の顔を 1 つ含む画像に対して Computer Vision によって返される JSON 応答を示します。

![Vision Analyze 屋上にいる女性の顔](./Images/woman_roof_face.png)

```json
{
    "faces": [
        {
            "age": 23,
            "gender": "Female",
            "faceRectangle": {
                "top": 45,
                "left": 194,
                "width": 44,
                "height": 44
            }
        }
    ],
    "requestId": "8439ba87-de65-441b-a0f1-c85913157ecd",
    "metadata": {
        "height": 200,
        "width": 300,
        "format": "Png"
    }
}
```

2 番目の例では、人の顔複数含む画像に対して返される JSON 応答を示します。

![Vision Analyze 家族写真の顔](./Images/family_photo_face.png)

```json
{
    "faces": [
        {
            "age": 11,
            "gender": "Male",
            "faceRectangle": {
                "top": 62,
                "left": 22,
                "width": 45,
                "height": 45
            }
        },
        {
            "age": 11,
            "gender": "Female",
            "faceRectangle": {
                "top": 127,
                "left": 240,
                "width": 42,
                "height": 42
            }
        },
        {
            "age": 37,
            "gender": "Female",
            "faceRectangle": {
                "top": 55,
                "left": 200,
                "width": 41,
                "height": 41
            }
        },
        {
            "age": 41,
            "gender": "Male",
            "faceRectangle": {
                "top": 45,
                "left": 103,
                "width": 39,
                "height": 39
            }
        }
    ],
    "requestId": "3a383cbe-1a05-4104-9ce7-1b5cf352b239",
    "metadata": {
        "height": 230,
        "width": 300,
        "format": "Png"
    }
}
```

## <a name="next-steps"></a>次の手順

[ドメイン固有のコンテンツの検出](concept-detecting-domain-content.md)についての概念を学習する。