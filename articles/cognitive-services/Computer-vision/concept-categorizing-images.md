---
title: イメージへの分類 - Computer Vision
titleSuffix: Azure Cognitive Services
description: Computer Vision API の画像分類機能に関連する概念について説明します。
services: cognitive-services
author: PatrickFarley
manager: cgronlun
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 08/29/2018
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: f182110d150583ee1c241c23a2e1924d9f3e3bd4
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/29/2019
ms.locfileid: "55158045"
---
# <a name="image-categorization-with-computer-vision"></a>Computer Vision での画像の分類

タグ付けや説明に加え、Computer Vision では、前のバージョンで定義した分類ベースのカテゴリが返されます。 これらのカテゴリは親/子の遺伝階層を持つ分類として整理されます。 すべてのカテゴリは英語です。 単独で、または新しいタグ付けモデルと共に使用できます。

## <a name="the-86-category-concept"></a>86 のカテゴリ概念

以下の図に示されている 86 の概念のリストに基づき、イメージを細かく分けることができます。 テキスト形式の完全な分類については、[カテゴリの分類](category-taxonomy.md)に関するページを参照してください。

![カテゴリの分類に含まれるすべてのカテゴリのグループ化されたリスト](./Images/analyze_categories-v2.png)

## <a name="image-categorization-examples"></a>イメージの分類の例

次の JSON 応答は、視覚的特徴に基づいてサンプル イメージを分類する際に、Computer Vision から返される内容を示します。

![共同住宅の屋上にいる女性](./Images/woman_roof.png)

```json
{
    "categories": [
        {
            "name": "people_",
            "score": 0.81640625
        }
    ],
    "requestId": "bae7f76a-1cc7-4479-8d29-48a694974705",
    "metadata": {
        "height": 200,
        "width": 300,
        "format": "Jpeg"
    }
}
```

次の表は、各イメージについて Computer Vision から返される一般的なイメージ セットとカテゴリを示しています。

| イメージ | Category |
|-------|----------|
| ![家族としてポーズをとっている 4 人の人物](./Images/family_photo.png) | people_group |
| ![草原の座っている子犬](./Images/cute_dog.png) | animal_dog |
| ![日没時に山頂の岩の上に立っている人物](./Images/mountain_vista.png) | outdoor_mountain |
| ![テーブル上の丸パンの山](./Images/bread.png) | food_bread |

## <a name="next-steps"></a>次の手順

[イメージのタグ付け](concept-tagging-images.md)および[イメージの説明](concept-describing-images.md)に関する概念を確認します。
