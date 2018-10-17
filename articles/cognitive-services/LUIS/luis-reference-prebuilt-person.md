---
title: personName 作成済みエンティティ - LUIS リファレンス
titleSuffix: Azure Cognitive Services
description: この記事では、Language Understanding (LUIS) での personName 作成済みエンティティについて説明します。
services: cognitive-services
author: diberry
manager: cjgronlund
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 09/24/2018
ms.author: diberry
ms.openlocfilehash: 0bc9d59fa44125429bfcb4e6ca9e68d93b81c04d
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/24/2018
ms.locfileid: "46950575"
---
# <a name="personname-entity"></a>personName エンティティ
作成済みの personName エンティティは、人の名前を検出します。 このエンティティは既にトレーニングされているので、personName を含む発話の例をアプリケーション意図に追加する必要はありません。 personName エンティティは、英語および中国語の[カルチャ](luis-reference-prebuilt-entities.md)でサポートされます。

## <a name="resolution-for-personname-entity"></a>personName エンティティの解決
次の例では、**builtin.personName** エンティティの解決を示します。

```JSON
{
  "query": "Is Jill Jones in Cairo?",
  "topScoringIntent": {
    "intent": "WhereIsEmployee",
    "score": 0.762141049
  }
  "entities": [
    {
      "entity": "Jill Jones",
      "type": "builtin.personName",
      "startIndex": 3,
      "endIndex": 12
    }
  ]
}
```

## <a name="next-steps"></a>次の手順

[email](luis-reference-prebuilt-email.md)、[number](luis-reference-prebuilt-number.md)、[ordinal](luis-reference-prebuilt-ordinal.md) エンティティについて学習します。 