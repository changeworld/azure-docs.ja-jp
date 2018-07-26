---
title: LUIS 作成済みエンティティ currency リファレンス - Azure | Microsoft Docs
titleSuffix: Azure
description: この記事には、Language Understanding (LUIS) での currency 作成済みエンティティについての情報が含まれます。
services: cognitive-services
author: diberry
manager: cjgronlund
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 06/20/2018
ms.author: diberry
ms.openlocfilehash: b383e21f870d15818c540b79a9a56c1dd65fa342
ms.sourcegitcommit: 194789f8a678be2ddca5397137005c53b666e51e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/25/2018
ms.locfileid: "39236824"
---
# <a name="currency-entity"></a>currency エンティティ
作成済みの currency エンティティは、LUIS アプリのカルチャに関係なく、多くの貨幣単位や国の通貨を検出します。 このエンティティは既にトレーニングされているので、通貨を含む発話の例をアプリケーション意図に追加する必要はありません。 currency エンティティは、[多くのカルチャ](luis-reference-prebuilt-entities.md)でサポートされています。 

## <a name="types-of-currency"></a>currency の種類
currency は [Recognizers-text](https://github.com/Microsoft/Recognizers-Text/blob/master/Patterns/English/English-NumbersWithUnit.yaml#L26) Github リポジトリから管理されます。

## <a name="resolution-for-currency-entity"></a>currency エンティティの解決
次の例では、**builtin.currency** エンティティの解決を示します。

```JSON
{
  "query": "search for items under $10.99",
  "topScoringIntent": {
    "intent": "SearchForItems",
    "score": 0.926173568
  },
  "intents": [
    {
      "intent": "SearchForItems",
      "score": 0.926173568
    },
    {
      "intent": "None",
      "score": 0.07376878
    }
  ],
  "entities": [
    {
      "entity": "$10.99",
      "type": "builtin.currency",
      "startIndex": 23,
      "endIndex": 28,
      "resolution": {
        "unit": "Dollar",
        "value": "10.99"
      }
    }
  ]
}
```

## <a name="next-steps"></a>次の手順

[datetimeV2](luis-reference-prebuilt-datetimev2.md)、[dimension](luis-reference-prebuilt-dimension.md)、[email](luis-reference-prebuilt-email.md) エンティティについて学習します。 