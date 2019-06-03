---
title: url 作成済みエンティティ
titleSuffix: Azure
description: この記事には、Language Understanding (LUIS) での url 作成済みエンティティについての情報が含まれます。
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: article
ms.date: 05/07/2019
ms.author: diberry
ms.openlocfilehash: d59020eb45f7dcced5ea8da04b3b908def34fb70
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/27/2019
ms.locfileid: "65072237"
---
# <a name="url-prebuilt-entity-for-a-luis-app"></a>LUIS アプリの url 作成済みエンティティ
url エンティティは、ドメイン名または IP アドレスを含む URL を抽出します。 このエンティティは既にトレーニングされているので、URL を含む発話の例をアプリケーションに追加する必要はありません。 url エンティティは、`en-us` カルチャでのみサポートされます。 

## <a name="types-of-urls"></a>url のタイプ
url は [Recognizers-text](https://github.com/Microsoft/Recognizers-Text/blob/master/Patterns/Base-URL.yaml) GitHub リポジトリから管理されます。

## <a name="resolution-for-prebuilt-url-entity"></a>作成済み url エンティティの解決

### <a name="api-version-2x"></a>API バージョン 2.x

次の例では、**builtin.url** エンティティの解決を示します。

```json
{
  "query": "https://www.luis.ai is a great cognitive services example of artificial intelligence",
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
      "entity": "https://www.luis.ai",
      "type": "builtin.url",
      "startIndex": 0,
      "endIndex": 17
    }
  ]
}
```

### <a name="preview-api-version-3x"></a>プレビュー API バージョン 3.x

次の JSON は、`verbose` パラメーターが `false` に設定されている場合です。

```json
{
    "query": "https://www.luis.ai is a great cognitive services example of artificial intelligence",
    "prediction": {
        "normalizedQuery": "https://www.luis.ai is a great cognitive services example of artificial intelligence",
        "topIntent": "None",
        "intents": {
            "None": {
                "score": 0.421936184
            }
        },
        "entities": {
            "url": [
                "https://www.luis.ai"
            ]
        }
    }
}
```

次の JSON は、`verbose` パラメーターが `true` に設定されている場合です。

```json
{
    "query": "https://www.luis.ai is a great cognitive services example of artificial intelligence",
    "prediction": {
        "normalizedQuery": "https://www.luis.ai is a great cognitive services example of artificial intelligence",
        "topIntent": "None",
        "intents": {
            "None": {
                "score": 0.421936184
            }
        },
        "entities": {
            "url": [
                "https://www.luis.ai"
            ],
            "$instance": {
                "url": [
                    {
                        "type": "builtin.url",
                        "text": "https://www.luis.ai",
                        "startIndex": 0,
                        "length": 19,
                        "modelTypeId": 2,
                        "modelType": "Prebuilt Entity Extractor"
                    }
                ]
            }
        }
    }
}
```

## <a name="next-steps"></a>次の手順

[ordinal](luis-reference-prebuilt-ordinal.md)、[number](luis-reference-prebuilt-number.md)、[temperature](luis-reference-prebuilt-temperature.md) エンティティについて学習します。
