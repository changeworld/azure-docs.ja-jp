---
title: オブジェクトの検出 - Computer Vision
titleSuffix: Azure Cognitive Services
description: Computer Vision API を使用したオブジェクトの検出に関連する概念。
services: cognitive-services
author: PatrickFarley
manager: cgronlun
ms.service: cognitive-services
ms.component: computer-vision
ms.topic: conceptual
ms.date: 12/03/2018
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: 89323e584b4020613fe3ff8411df50f2ab96f156
ms.sourcegitcommit: 7cd706612a2712e4dd11e8ca8d172e81d561e1db
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/18/2018
ms.locfileid: "53582731"
---
# <a name="object-detection"></a>オブジェクトの検出

オブジェクトの検出は[タグ付け](concept-tagging-images.md)に似ていますが、API で返されるのは、検出された各オブジェクトの境界ボックスの座標 (ピクセル単位) です。 たとえば犬や猫、人物が画像に含まれている場合、検出操作によって、それらのオブジェクトが、画像における対応する座標と共に一覧表示されます。 この機能を使用すれば、画像に含まれるオブジェクト間の関係を処理することができます。 画像内に同じタグのインスタンスが複数存在するかどうかも判別できます。

Detect API は、画像内で特定されたオブジェクトや生物に応じてタグを適用します。 この時点では、タグ付けに使用される分類とオブジェクト検出に使用される分類との間に、正式な関係は存在しないことに注意してください。 概念レベルで言えば、Detect API はオブジェクトや生物を検出するだけであるのに対し、Tag API では、コンテキスト上の用語 ("indoor" など) も対象となり、それらの位置を境界ボックスで特定することはできません。

## <a name="object-detection-example"></a>オブジェクト検出の例

次の JSON 応答は、Computer Vision がサンプル画像からオブジェクトを検出するときに返す内容を示しています。

![キッチンで Microsoft Surface デバイスを使用する女性](./Images/windows-kitchen.jpg)

```json
{
   "objects":[
      {
         "rectangle":{
            "x":730,
            "y":66,
            "w":135,
            "h":85
         },
         "object":"kitchen appliance",
         "confidence":0.501
      },
      {
         "rectangle":{
            "x":523,
            "y":377,
            "w":185,
            "h":46
         },
         "object":"computer keyboard",
         "confidence":0.51
      },
      {
         "rectangle":{
            "x":471,
            "y":218,
            "w":289,
            "h":226
         },
         "object":"Laptop",
         "confidence":0.85,
         "parent":{
            "object":"computer",
            "confidence":0.851
         }
      },
      {
         "rectangle":{
            "x":654,
            "y":0,
            "w":584,
            "h":473
         },
         "object":"person",
         "confidence":0.855
      }
   ],
   "requestId":"a7fde8fd-cc18-4f5f-99d3-897dcd07b308",
   "metadata":{
      "width":1260,
      "height":473,
      "format":"Jpeg"
   }
}
```

## <a name="next-steps"></a>次の手順

[イメージの分類](concept-categorizing-images.md)および[イメージの説明](concept-describing-images.md)に関する概念を確認します。