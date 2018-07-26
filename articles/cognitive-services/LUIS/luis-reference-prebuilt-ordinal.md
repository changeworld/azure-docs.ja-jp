---
title: LUIS 作成済みエンティティ ordinal リファレンス - Azure | Microsoft Docs
titleSuffix: Azure
description: この記事には、Language Understanding (LUIS) での ordinal 作成済みエンティティについての情報が含まれます。
services: cognitive-services
author: diberry
manager: cjgronlund
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 06/20/2018
ms.author: diberry
ms.openlocfilehash: 216114ec521e2065cb13cd39b4086f50ec81ba56
ms.sourcegitcommit: 194789f8a678be2ddca5397137005c53b666e51e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/25/2018
ms.locfileid: "39236756"
---
# <a name="ordinal-entity"></a>ordinal エンティティ
序数は、セット内のオブジェクトの数値表現 `first`、`second`、`third` です。 このエンティティは既にトレーニングされているので、序数メールを含む発話の例をアプリケーション意図に追加する必要はありません。 ordinal エンティティは、[多くのカルチャ](luis-reference-prebuilt-entities.md)でサポートされます。 

## <a name="types-of-ordinal"></a>ordinal の種類
ordinal は [Recognizers-text](https://github.com/Microsoft/Recognizers-Text/blob/master/Patterns/English/English-Numbers.yaml#L45) Github リポジトリから管理されます。

## <a name="resolution-for-prebuilt-ordinal-entity"></a>作成済み ordinal エンティティの解決
次の例では、**builtin.ordinal** エンティティの解決を示します。

```JSON
{
  "query": "Order the second option",
  "topScoringIntent": {
    "intent": "OrderFood",
    "score": 0.9993253
  },
  "intents": [
    {
      "intent": "OrderFood",
      "score": 0.9993253
    },
    {
      "intent": "None",
      "score": 0.05046708
    }
  ],
  "entities": [
    {
      "entity": "second",
      "type": "builtin.ordinal",
      "startIndex": 10,
      "endIndex": 15,
      "resolution": {
        "value": "2"
      }
    }
  ]
}
```

## <a name="next-steps"></a>次の手順

[percentage](luis-reference-prebuilt-percentage.md)、[phonenumber](luis-reference-prebuilt-phonenumber.md)、[temperature](luis-reference-prebuilt-temperature.md) エンティティについて学習します。 