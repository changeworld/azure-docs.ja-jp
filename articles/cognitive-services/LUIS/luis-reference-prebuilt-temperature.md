---
title: temperature 作成済みエンティティ - LUIS
titleSuffix: Azure Cognitive Services
description: この記事には、Language Understanding (LUIS) での temperature 作成済みエンティティについての情報が含まれます。
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 05/07/2019
ms.author: diberry
ms.openlocfilehash: ecdaec6dcade033bf99842dd384be095dd363a05
ms.sourcegitcommit: 13a289ba57cfae728831e6d38b7f82dae165e59d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/09/2019
ms.locfileid: "68933401"
---
# <a name="temperature-prebuilt-entity-for-a-luis-app"></a>LUIS アプリの temperature 作成済みエンティティ
temperature は、さまざまな温度の種類を抽出します。 このエンティティは既にトレーニングされているので、温度を含む発話の例をアプリケーションに追加する必要はありません。 temperature エンティティは、[多くのカルチャ](luis-reference-prebuilt-entities.md)でサポートされています。 

## <a name="types-of-temperature"></a>temperature の種類
temperature は [Recognizers-text](https://github.com/Microsoft/Recognizers-Text/blob/master/Patterns/English/English-NumbersWithUnit.yaml#L819) GitHub リポジトリから管理されます。

## <a name="resolution-for-prebuilt-temperature-entity"></a>作成済み temperature エンティティの解決

### <a name="api-version-2x"></a>API バージョン 2.x

次の例では、**builtin.temperature** エンティティの解決を示します。

```json
{
  "query": "set the temperature to 30 degrees",
  "topScoringIntent": {
    "intent": "None",
    "score": 0.85310787
  },
  "intents": [
    {
      "intent": "None",
      "score": 0.85310787
    }
  ],
  "entities": [
    {
      "entity": "30 degrees",
      "type": "builtin.temperature",
      "startIndex": 23,
      "endIndex": 32,
      "resolution": {
        "unit": "Degree",
        "value": "30"
      }
    }
  ]
}
```

### <a name="preview-api-version-3x"></a>プレビュー API バージョン 3.x

次の JSON は、`verbose` パラメーターが `false` に設定されている場合です。

```json
{
    "query": "set the temperature to 30 degrees",
    "prediction": {
        "normalizedQuery": "set the temperature to 30 degrees",
        "topIntent": "None",
        "intents": {
            "None": {
                "score": 0.656305432
            }
        },
        "entities": {
            "temperature": [
                {
                    "number": 30,
                    "unit": "Degree"
                }
            ]
        }
    }
}
```

次の JSON は、`verbose` パラメーターが `true` に設定されている場合です。

```json
{
    "query": "set the temperature to 30 degrees",
    "prediction": {
        "normalizedQuery": "set the temperature to 30 degrees",
        "topIntent": "None",
        "intents": {
            "None": {
                "score": 0.656305432
            }
        },
        "entities": {
            "temperature": [
                {
                    "number": 30,
                    "unit": "Degree"
                }
            ],
            "$instance": {
                "temperature": [
                    {
                        "type": "builtin.temperature",
                        "text": "30 degrees",
                        "startIndex": 23,
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

[percentage](luis-reference-prebuilt-percentage.md)、[number](luis-reference-prebuilt-number.md)、[age](luis-reference-prebuilt-age.md) エンティティについて学習します。 
