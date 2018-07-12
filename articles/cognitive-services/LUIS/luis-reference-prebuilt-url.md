---
title: LUIS 作成済みエンティティ url リファレンス - Azure | Microsoft Docs
titleSuffix: Azure
description: この記事には、Language Understanding (LUIS) での url 作成済みエンティティについての情報が含まれます。
services: cognitive-services
author: v-geberr
manager: kaiqb
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 06/20/2018
ms.author: v-geberr
ms.openlocfilehash: 4eacf564a295a568a3e2c8d2f44ad0af3fbbe258
ms.sourcegitcommit: 65b399eb756acde21e4da85862d92d98bf9eba86
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/22/2018
ms.locfileid: "36321674"
---
# <a name="url-entity"></a>url エンティティ
url エンティティは、ドメイン名または IP アドレスを含む URL を抽出します。 このエンティティは既にトレーニングされているので、URL を含む発話の例をアプリケーションに追加する必要はありません。 url エンティティは、`en-us` カルチャでのみサポートされます。 

## <a name="types-of-urls"></a>url のタイプ
url は [Recognizers-text](https://github.com/Microsoft/Recognizers-Text/blob/master/Patterns/Base-URL.yaml) Github リポジトリから管理されます。

## <a name="resolution-for-prebuilt-url-entity"></a>作成済み url エンティティの解決
次の例では、**builtin.url** エンティティの解決を示します。

```JSON
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