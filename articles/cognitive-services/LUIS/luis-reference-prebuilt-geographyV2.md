---
title: Geography V2 作成済みエンティティ
titleSuffix: Azure Cognitive Services
description: この記事には、Language Understanding (LUIS) での geographyV2 作成済みエンティティについての情報が含まれます。
services: cognitive-services
author: diberry
manager: cjgronlund
ms.custom: seodec18
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 09/24/2018
ms.author: diberry
ms.openlocfilehash: a81c97cf8883d2a3629d1eb1b9f6137609c6edda
ms.sourcegitcommit: 78ec955e8cdbfa01b0fa9bdd99659b3f64932bba
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/10/2018
ms.locfileid: "53135102"
---
# <a name="geographyv2-prebuilt-entity-for-a-luis-app"></a>LUIS アプリの geographyV2 作成済みエンティティ
作成済みの geographyV2 エンティティは、場所を検出します。 このエンティティは既にトレーニングされているので、GeographyV2 を含む発話の例をアプリケーション意図に追加する必要はありません。 GeographyV2 エンティティは、英語[カルチャ](luis-reference-prebuilt-entities.md)でサポートされています。

## <a name="subtypes"></a>サブタイプ
地理的な場所にはサブタイプがあります。

|Subtype|目的|
|--|--|
|`poi`|関心のある場所|
|`city`|市の名前|
|`countryRegion`|国または地域の名前|
|`continent`|大陸の名前|
|`state`|州や都道府県の名前|


## <a name="resolution-for-geographyv2-entity"></a>GeographyV2 エンティティの解決
次の例では、**builtin.geographyV2** エンティティの解決を示します。

```json
{
    "query": "Carol is visiting the sphinx in gizah egypt in africa before heading to texas",
    "topScoringIntent": {
        "intent": "None",
        "score": 0.8008023
    },
    "intents": [
        {
            "intent": "None",
            "score": 0.8008023
        }
    ],
    "entities": [
        {
            "entity": "the sphinx",
            "type": "builtin.geographyV2.poi",
            "startIndex": 18,
            "endIndex": 27
        },
        {
            "entity": "gizah",
            "type": "builtin.geographyV2.city",
            "startIndex": 32,
            "endIndex": 36
        },
        {
            "entity": "egypt",
            "type": "builtin.geographyV2.countryRegion",
            "startIndex": 38,
            "endIndex": 42
        },
        {
            "entity": "africa",
            "type": "builtin.geographyV2.continent",
            "startIndex": 47,
            "endIndex": 52
        },
        {
            "entity": "texas",
            "type": "builtin.geographyV2.state",
            "startIndex": 72,
            "endIndex": 76
        },
        {
            "entity": "carol",
            "type": "builtin.personName",
            "startIndex": 0,
            "endIndex": 4
        }
    ]
} 
```

## <a name="next-steps"></a>次の手順

[email](luis-reference-prebuilt-email.md)、[number](luis-reference-prebuilt-number.md)、[ordinal](luis-reference-prebuilt-ordinal.md) エンティティについて学習します。 