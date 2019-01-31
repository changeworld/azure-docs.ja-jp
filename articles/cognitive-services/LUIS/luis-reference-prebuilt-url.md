---
title: url 作成済みエンティティ
titleSuffix: Azure
description: この記事には、Language Understanding (LUIS) での url 作成済みエンティティについての情報が含まれます。
services: cognitive-services
author: diberry
manager: cgronlun
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: article
ms.date: 11/27/2018
ms.author: diberry
ms.openlocfilehash: f9e331abd31ef37e9214214118748ebda3eb9470
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/29/2019
ms.locfileid: "55225799"
---
# <a name="url-prebuilt-entity-for-a-luis-app"></a>LUIS アプリの url 作成済みエンティティ
url エンティティは、ドメイン名または IP アドレスを含む URL を抽出します。 このエンティティは既にトレーニングされているので、URL を含む発話の例をアプリケーションに追加する必要はありません。 url エンティティは、`en-us` カルチャでのみサポートされます。 

## <a name="types-of-urls"></a>url のタイプ
url は [Recognizers-text](https://github.com/Microsoft/Recognizers-Text/blob/master/Patterns/Base-URL.yaml) GitHub リポジトリから管理されます。

## <a name="resolution-for-prebuilt-url-entity"></a>作成済み url エンティティの解決
次の例では、**builtin.url** エンティティの解決を示します。

```json
{
  "query": "http://www.luis.ai is a great cognitive services example of artificial intelligence",
  "topScoringIntent": {
    "intent": "None",
    "score": 0.781975448
  },
  "intents": [
    {
      "intent": "None",
      "score": 0.781975448
    }
  ],
  "entities": [
    {
      "entity": "http://www.luis.ai",
      "type": "builtin.url",
      "startIndex": 0,
      "endIndex": 17
    }
  ]
}
```

## <a name="next-steps"></a>次の手順

[ordinal](luis-reference-prebuilt-ordinal.md)、[number](luis-reference-prebuilt-number.md)、[temperature](luis-reference-prebuilt-temperature.md) エンティティについて学習します。
