---
title: age 作成済みエンティティ
titleSuffix: Azure
description: この記事には、Language Understanding (LUIS) での age 作成済みエンティティについての情報が含まれます。
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: article
ms.date: 05/07/2019
ms.author: diberry
ms.openlocfilehash: b8239688000f0ce32ca2c2be054b1443bbb698b5
ms.sourcegitcommit: 24fd3f9de6c73b01b0cee3bcd587c267898cbbee
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/20/2019
ms.locfileid: "65069127"
---
# <a name="age-prebuilt-entity-for-a-luis-app"></a>LUIS アプリの age 作成済みエンティティ
作成済みの age エンティティは、数値と、年数、月数、週数、日数の両方で、年齢の値をキャプチャします。 このエンティティは既にトレーニングされているので、年齢を含む発話の例をアプリケーション意図に追加する必要はありません。 age エンティティは、[多くのカルチャ](luis-reference-prebuilt-entities.md)でサポートされます。 

## <a name="types-of-age"></a>age のタイプ
age は [Recognizers-text](https://github.com/Microsoft/Recognizers-Text/blob/master/Patterns/English/English-NumbersWithUnit.yaml#L3) GitHub リポジトリから管理されます。

## <a name="resolution-for-prebuilt-age-entity"></a>作成済み age エンティティの解決

### <a name="api-version-2x"></a>API バージョン 2.x

次の例では、**builtin.age** エンティティの解決を示します。

```json
{
  "query": "A 90 day old utilities bill is quite late.",
  "topScoringIntent": {
    "intent": "None",
    "score": 0.8236133
  },
  "entities": [
    {
      "entity": "90 day old",
      "type": "builtin.age",
      "startIndex": 2,
      "endIndex": 11,
      "resolution": {
        "unit": "Day",
        "value": "90"
      }
    }
  ]
}
```

### <a name="preview-api-version-3x"></a>プレビュー API バージョン 3.x

次の JSON は、`verbose` パラメーターが `false` に設定されている場合です。

```json
{
    "query": "A 90 day old utilities bill is quite late.",
    "prediction": {
        "normalizedQuery": "a 90 day old utilities bill is quite late.",
        "topIntent": "None",
        "intents": {
            "None": {
                "score": 0.558252
            }
        },
        "entities": {
            "age": [
                {
                    "number": 90,
                    "unit": "Day"
                }
            ]
        }
    }
}
```

次の JSON は、`verbose` パラメーターが `true` に設定されている場合です。

```json
{
    "query": "A 90 day old utilities bill is quite late.",
    "prediction": {
        "normalizedQuery": "a 90 day old utilities bill is quite late.",
        "topIntent": "None",
        "intents": {
            "None": {
                "score": 0.558252
            }
        },
        "entities": {
            "age": [
                {
                    "number": 90,
                    "unit": "Day"
                }
            ],
            "$instance": {
                "age": [
                    {
                        "type": "builtin.age",
                        "text": "90 day old",
                        "startIndex": 2,
                        "length": 10,
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

[currency](luis-reference-prebuilt-currency.md)、[datetimeV2](luis-reference-prebuilt-datetimev2.md)、[dimension](luis-reference-prebuilt-dimension.md) エンティティについて学習します。 
