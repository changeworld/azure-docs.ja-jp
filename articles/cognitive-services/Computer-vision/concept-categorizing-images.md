---
title: イメージへの分類 - Computer Vision
titleSuffix: Azure Cognitive Services
description: Computer Vision API を使用したイメージへの分類に関連する概念。
services: cognitive-services
author: deken
manager: cgronlun
ms.service: cognitive-services
ms.component: computer-vision
ms.topic: conceptual
ms.date: 08/29/2018
ms.author: v-deken
ms.openlocfilehash: 345432cbc8a552ba7f6a4902cec72bcc1bf9feac
ms.sourcegitcommit: 776b450b73db66469cb63130c6cf9696f9152b6a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/18/2018
ms.locfileid: "45983555"
---
# <a name="categorizing-images"></a>イメージの分類

タグ付けや説明に加え、Computer Vision では、前のバージョンで定義した分類ベースのカテゴリが返されます。 これらのカテゴリは親/子の遺伝階層を持つ分類として整理されます。 すべてのカテゴリは英語です。 単独で、または新しいタグ付けモデルと共に使用できます。

## <a name="the-86-category-concept"></a>86 のカテゴリ概念

以下の図に示されている 86 の概念のリストに基づき、イメージを細かく分けることができます。 テキスト形式の完全な分類については、[カテゴリの分類](category-taxonomy.md)に関するページを参照してください。

![カテゴリの分析](./Images/analyze_categories.png)

## <a name="image-categorization-examples"></a>イメージの分類の例

次の JSON 応答は、視覚的特徴に基づいてサンプル イメージを分類する際に、Computer Vision から返される内容を示します。

![屋上にいる女性](./Images/woman_roof.png)

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
| ![家族写真](./Images/family_photo.png) | people_group |
| ![かわいい犬](./Images/cute_dog.png) | animal_dog |
| ![屋外の山](./Images/mountain_vista.png) | outdoor_mountain |
| ![Vision Analyze パン](./Images/bread.png) | food_bread |

## <a name="next-steps"></a>次の手順

[イメージのタグ付け](concept-tagging-images.md)および[イメージの説明](concept-describing-images.md)に関する概念を確認します。