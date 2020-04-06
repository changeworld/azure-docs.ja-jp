---
title: 顔検出 - Computer Vision
titleSuffix: Azure Cognitive Services
description: Computer Vision API の顔検出機能に関連する概念について説明します。
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 04/17/2019
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: d10e4c98d3e2f00e7139063c8263e65ffd015d7c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/28/2020
ms.locfileid: "80244703"
---
# <a name="face-detection-with-computer-vision"></a>Computer Vision での顔の検出

Computer Vision は、画像内の人間の顔を検出し、検出された各顔の年齢、性別、四角形を生成できます。 

> [!NOTE]
> この機能は、Azure [Face](/azure/cognitive-services/face/) サービスでも利用できます。 ここでは、顔識別、姿勢検出など、より詳細な顔分析について説明しています。 

## <a name="face-detection-examples"></a>顔検出の例

次の例では、人の顔を 1 つ含む画像に対して Computer Vision によって返される JSON 応答を示します。

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

## <a name="use-the-api"></a>API の使用

顔検出機能は [Analyze Image](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa) API に含まれています。 ネイティブ SDK または REST を呼び出すことでこの API を呼び出すことができます。 `Faces` を **visualFeatures** クエリ パラメーターに追加します。 その後、完全な JSON 応答が得られたら、`"faces"` セクションのコンテンツを対象に文字列を解析します。

* [クイック スタート: Computer Vision .NET SDK](./quickstarts-sdk/client-library.md?pivots=programming-language-csharp)
* [クイック スタート: 画像の分析 (REST API)](./quickstarts/csharp-analyze.md)
