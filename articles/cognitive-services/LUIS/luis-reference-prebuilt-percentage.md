---
title: LUIS 作成済みエンティティ percentage リファレンス - Azure | Microsoft Docs
titleSuffix: Azure
description: この記事には、Language Understanding (LUIS) での percentage 作成済みエンティティについての情報が含まれます。
services: cognitive-services
author: diberry
manager: cgronlun
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 06/20/2018
ms.author: diberry
ms.openlocfilehash: 6929fc3cc41db4e4bd4a1f8bda62c953a3722eff
ms.sourcegitcommit: 4ecc62198f299fc215c49e38bca81f7eb62cdef3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/24/2018
ms.locfileid: "47041170"
---
# <a name="percentage-entity"></a>percentage エンティティ
パーセンテージの値は、分数 `3 1/2` またはパーセンテージ `2%` として出現する可能性があります。 このエンティティは既にトレーニングされているので、パーセンテージを含む発話の例をアプリケーション意図に追加する必要はありません。 percentage エンティティは、[多くのカルチャ](luis-reference-prebuilt-entities.md)でサポートされています。 

## <a name="types-of-percentage"></a>percentage のタイプ
percentage は [Recognizers-text](https://github.com/Microsoft/Recognizers-Text/blob/master/Patterns/English/English-Numbers.yaml#L114) GitHub リポジトリから管理されます。

## <a name="resolution-for-prebuilt-percentage-entity"></a>作成済み percentage エンティティの解決
次の例では、**builtin.percentage** エンティティの解決を示します。

```JSON
{
  "query": "set a trigger when my stock goes up 2%",
  "topScoringIntent": {
    "intent": "SetTrigger",
    "score": 0.971157849
  },
  "intents": [
    {
      "intent": "SetTrigger",
      "score": 0.971157849
    }
  ],
  "entities": [
    {
      "entity": "2%",
      "type": "builtin.percentage",
      "startIndex": 36,
      "endIndex": 37,
      "resolution": {
        "value": "2%"
      }
    }
  ]
}
```

## <a name="next-steps"></a>次の手順

[ordinal](luis-reference-prebuilt-ordinal.md)、[number](luis-reference-prebuilt-number.md)、[temperature](luis-reference-prebuilt-temperature.md) エンティティについて学習します。 
