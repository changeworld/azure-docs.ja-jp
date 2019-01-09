---
title: dimension 作成済みエンティティ
titleSuffix: Azure
description: この記事には、Language Understanding (LUIS) での dimension 作成済みエンティティについての情報が含まれます。
services: cognitive-services
ms.custom: seodec18
author: diberry
manager: cgronlun
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 11/26/2018
ms.author: diberry
ms.openlocfilehash: 0ed5d68905353cc0b99a8ce5e1d1b6bfde9d1b87
ms.sourcegitcommit: efcd039e5e3de3149c9de7296c57566e0f88b106
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/10/2018
ms.locfileid: "53166049"
---
# <a name="dimension-prebuilt-entity-for-a-luis-app"></a>LUIS アプリの dimension 作成済みエンティティ
作成済みの dimension エンティティは、LUIS アプリのカルチャに関係なく、さまざまな種類の寸法を検出します。 このエンティティは既にトレーニングされているので、寸法を含む発話の例をアプリケーション意図に追加する必要はありません。 dimension エンティティは、[多くのカルチャ](luis-reference-prebuilt-entities.md)でサポートされています。 

## <a name="types-of-dimension"></a>dimension のタイプ

dimension は [Recognizers-text](https://github.com/Microsoft/Recognizers-Text/blob/master/Patterns/English/English-NumbersWithUnit.yaml) GitHub リポジトリから管理されます


## <a name="resolution-for-dimension-entity"></a>dimension エンティティの解決
次の例では、**builtin.dimension** エンティティの解決を示します。

```json
{
  "query": "it takes more than 10 1/2 miles of cable and wire to hook it all up , and 23 computers.",
  "topScoringIntent": {
    "intent": "None",
    "score": 0.762141049
  },
  "intents": [
    {
      "intent": "None",
      "score": 0.762141049
    }
  ],
  "entities": [
    {
      "entity": "10 1/2 miles",
      "type": "builtin.dimension",
      "startIndex": 19,
      "endIndex": 30,
      "resolution": {
        "unit": "Mile",
        "value": "10.5"
      }
    }
  ]
}
```

## <a name="next-steps"></a>次の手順

[email](luis-reference-prebuilt-email.md)、[number](luis-reference-prebuilt-number.md)、[ordinal](luis-reference-prebuilt-ordinal.md) エンティティについて学習します。 