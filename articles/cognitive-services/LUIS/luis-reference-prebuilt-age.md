---
title: LUIS 作成済みエンティティ age リファレンス - Azure | Microsoft Docs
titleSuffix: Azure
description: この記事には、Language Understanding (LUIS) での age 作成済みエンティティについての情報が含まれます。
services: cognitive-services
author: v-geberr
manager: kaiqb
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 06/20/2018
ms.author: v-geberr
ms.openlocfilehash: 59732469cf0d1e55643f3977958ec34a887130d3
ms.sourcegitcommit: 65b399eb756acde21e4da85862d92d98bf9eba86
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/22/2018
ms.locfileid: "36321714"
---
# <a name="age-entity"></a>age エンティティ
作成済みの age エンティティは、数値と、年数、月数、週数、日数の両方で、年齢の値をキャプチャします。 このエンティティは既にトレーニングされているので、年齢を含む発話の例をアプリケーション意図に追加する必要はありません。 age エンティティは、[多くのカルチャ](luis-reference-prebuilt-entities.md)でサポートされます。 

## <a name="types-of-age"></a>age のタイプ
age は [Recognizers-text](https://github.com/Microsoft/Recognizers-Text/blob/master/Patterns/English/English-NumbersWithUnit.yaml#L3) Github リポジトリから管理されます。

## <a name="resolution-for-prebuilt-age-entity"></a>作成済み age エンティティの解決
次の例では、**builtin.age** エンティティの解決を示します。

```JSON
{
  "query": "A 90 day old utilities bill is quite late.",
  "topScoringIntent": {
    "intent": "None",
    "score": 0.8236133
  },
  "intents": [
    {
      "intent": "None",
      "score": 0.8236133
    }
  ],
  "entities": [
    {
      "entity": "90 day old",
      "type": "builtin.age",
      "startIndex": 2,
      "endIndex": 11,
      "resolution": {
        "unit": "Day",
        "value": "90"
      }
    }
  ]
}
```

## <a name="next-steps"></a>次の手順

[currency](luis-reference-prebuilt-currency.md)、[datetimeV2](luis-reference-prebuilt-datetimev2.md)、[dimension](luis-reference-prebuilt-dimension.md) エンティティについて学習します。 