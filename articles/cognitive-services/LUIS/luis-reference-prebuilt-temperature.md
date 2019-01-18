---
title: temperature 作成済みエンティティ
titleSuffix: Azure
description: この記事には、Language Understanding (LUIS) での temperature 作成済みエンティティについての情報が含まれます。
services: cognitive-services
author: diberry
manager: cgronlun
ms.custom: seodec18
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 11/27/2018
ms.author: diberry
ms.openlocfilehash: 348030d888383c497d80259b279056d8ff892bfe
ms.sourcegitcommit: efcd039e5e3de3149c9de7296c57566e0f88b106
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/10/2018
ms.locfileid: "53165524"
---
# <a name="temperature-prebuilt-entity-for-a-luis-app"></a>LUIS アプリの temperature 作成済みエンティティ
temperature は、さまざまな温度の種類を抽出します。 このエンティティは既にトレーニングされているので、温度を含む発話の例をアプリケーションに追加する必要はありません。 temperature エンティティは、[多くのカルチャ](luis-reference-prebuilt-entities.md)でサポートされています。 

## <a name="types-of-temperature"></a>temperature の種類
temperature は [Recognizers-text](https://github.com/Microsoft/Recognizers-Text/blob/master/Patterns/English/English-NumbersWithUnit.yaml#L819) GitHub リポジトリから管理されます。

## <a name="resolution-for-prebuilt-temperature-entity"></a>作成済み temperature エンティティの解決
次の例では、**builtin.temperature** エンティティの解決を示します。

```json
{
  "query": "set the temperature to 30 degrees",
  "topScoringIntent": {
    "intent": "None",
    "score": 0.85310787
  },
  "intents": [
    {
      "intent": "None",
      "score": 0.85310787
    }
  ],
  "entities": [
    {
      "entity": "30 degrees",
      "type": "builtin.temperature",
      "startIndex": 23,
      "endIndex": 32,
      "resolution": {
        "unit": "Degree",
        "value": "30"
      }
    }
  ]
}
```

## <a name="next-steps"></a>次の手順

[percentage](luis-reference-prebuilt-percentage.md)、[number](luis-reference-prebuilt-number.md)、[age](luis-reference-prebuilt-age.md) エンティティについて学習します。 