---
title: LUIS 作成済みエンティティ email リファレンス - Azure | Microsoft Docs
titleSuffix: Azure
description: この記事には、Language Understanding (LUIS) での email 作成済みエンティティについての情報が含まれます。
services: cognitive-services
author: diberry
manager: cgronlun
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 06/20/2018
ms.author: diberry
ms.openlocfilehash: c69fc9cb19871611b383ebf6603197fdcd781a03
ms.sourcegitcommit: 4ecc62198f299fc215c49e38bca81f7eb62cdef3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/24/2018
ms.locfileid: "47030599"
---
# <a name="email-entity"></a>email エンティティ
email の抽出には、発話からのメール アドレス全体が含まれます。 このエンティティは既にトレーニングされているので、メールを含む発話の例をアプリケーション意図に追加する必要はありません。 email エンティティは、`en-us` カルチャでのみサポートされます。 

## <a name="resolution-for-prebuilt-email"></a>作成済みの email の解決
次の例では、**builtin.email** エンティティの解決を示します。

```JSON
{
  "query": "please send the information to patti.owens@microsoft.com",
  "topScoringIntent": {
    "intent": "None",
    "score": 0.811592042
  },
  "intents": [
    {
      "intent": "None",
      "score": 0.811592042
    }
  ],
  "entities": [
    {
      "entity": "patti.owens@microsoft.com",
      "type": "builtin.email",
      "startIndex": 31,
      "endIndex": 55
    }
  ]
}
```

## <a name="next-steps"></a>次の手順

[number](luis-reference-prebuilt-number.md)、[ordinal](luis-reference-prebuilt-ordinal.md)、[percentage](luis-reference-prebuilt-percentage.md) について学習します。 