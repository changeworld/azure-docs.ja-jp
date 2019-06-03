---
title: keyPhrase 作成済みエンティティ
titleSuffix: Azure
description: この記事には、Language Understanding (LUIS) での keyPhrase 作成済みエンティティについての情報が含まれます。
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: article
ms.date: 05/07/2019
ms.author: diberry
ms.openlocfilehash: 988609f411ad405b0f1dc244b23fb6db446136a2
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/27/2019
ms.locfileid: "65072000"
---
# <a name="keyphrase-prebuilt-entity-for-a-luis-app"></a>LUIS アプリの keyPhrase 作成済みエンティティ
keyPhrase は、発話からさまざまなキー フレーズを抽出します。 keyPhrase が含まれている発話例をアプリケーションに追加する必要はありません。 keyPhrase エンティティは、[テキスト分析](../text-analytics/overview.md)フィーチャーの一環として[多くのカルチャ](luis-language-support.md#languages-supported)でサポートされています。 

## <a name="resolution-for-prebuilt-keyphrase-entity"></a>作成済み keyPhrase エンティティの解決

### <a name="api-version-2x"></a>API バージョン 2.x

次の例では、**builtin.keyPhrase** エンティティの解決を示します。

```json
{
  "query": "where is the educational requirements form for the development and engineering group",
  "topScoringIntent": {
    "intent": "GetJobInformation",
    "score": 0.182757929
  },
  "entities": [
    {
      "entity": "development",
      "type": "builtin.keyPhrase",
      "startIndex": 51,
      "endIndex": 61
    },
    {
      "entity": "educational requirements",
      "type": "builtin.keyPhrase",
      "startIndex": 13,
      "endIndex": 36
    }
  ]
}
```

## <a name="next-steps"></a>次の手順

[percentage](luis-reference-prebuilt-percentage.md)、[number](luis-reference-prebuilt-number.md)、[age](luis-reference-prebuilt-age.md) エンティティについて学習します。
