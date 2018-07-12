---
title: LUIS 作成済みエンティティ temperature リファレンス - Azure | Microsoft Docs
titleSuffix: Azure
description: この記事には、Language Understanding (LUIS) での temperature 作成済みエンティティについての情報が含まれます。
services: cognitive-services
author: v-geberr
manager: kaiqb
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 06/20/2018
ms.author: v-geberr
ms.openlocfilehash: 3cc16e7ec87775407c4261655d8f680cc0903e81
ms.sourcegitcommit: 65b399eb756acde21e4da85862d92d98bf9eba86
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/22/2018
ms.locfileid: "36321504"
---
# <a name="temperature-entity"></a>temperature エンティティ
temperature は、さまざまな温度の種類を抽出します。 このエンティティは既にトレーニングされているので、温度を含む発話の例をアプリケーションに追加する必要はありません。 temperature エンティティは、[多くのカルチャ](luis-reference-prebuilt-entities.md)でサポートされています。 

## <a name="types-of-temperature"></a>temperature の種類
temperature は [Recognizers-text](https://github.com/Microsoft/Recognizers-Text/blob/master/Patterns/English/English-NumbersWithUnit.yaml#L819) Github リポジトリから管理されます。

## <a name="resolution-for-prebuilt-temperature-entity"></a>作成済み temperature エンティティの解決
次の例では、**builtin.temperature** エンティティの解決を示します。

```JSON
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