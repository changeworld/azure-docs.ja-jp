---
title: ordinal 作成済みエンティティ - LUIS
titleSuffix: Azure Cognitive Services
description: この記事には、Language Understanding (LUIS) での ordinal 作成済みエンティティについての情報が含まれます。
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 06/25/2019
ms.author: diberry
ms.openlocfilehash: 51f1082034a710ed9d0f78b79bc6ae469771459e
ms.sourcegitcommit: 13a289ba57cfae728831e6d38b7f82dae165e59d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/09/2019
ms.locfileid: "68933466"
---
# <a name="ordinal-prebuilt-entity-for-a-luis-app"></a>LUIS アプリの ordinal 作成済みエンティティ
序数は、セット内のオブジェクトの数値表現 `first`、`second`、`third` です。 このエンティティは既にトレーニングされているので、序数メールを含む発話の例をアプリケーション意図に追加する必要はありません。 ordinal エンティティは、[多くのカルチャ](luis-reference-prebuilt-entities.md)でサポートされます。 

## <a name="types-of-ordinal"></a>ordinal の種類
ordinal は [Recognizers-text](https://github.com/Microsoft/Recognizers-Text/blob/master/Patterns/English/English-Numbers.yaml#L45) GitHub リポジトリから管理されます。

## <a name="resolution-for-prebuilt-ordinal-entity"></a>作成済み ordinal エンティティの解決

### <a name="api-version-2x"></a>API バージョン 2.x

次の例では、**builtin.ordinal** エンティティの解決を示します。

```json
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

### <a name="preview-api-version-3x"></a>プレビュー API バージョン 3.x

次の JSON は、`verbose` パラメーターが `false` に設定されている場合です。

```json
{
    "query": "Order the second option",
    "prediction": {
        "normalizedQuery": "order the second option",
        "topIntent": "None",
        "intents": {
            "None": {
                "score": 0.7124502
            }
        },
        "entities": {
            "ordinal": [
                {
                    "offset": 2,
                    "relativeTo": "start"
                }
            ]
        }
    }
}
```

次の JSON は、`verbose` パラメーターが `true` に設定されている場合です。

```json
{
    "query": "Order the second option",
    "prediction": {
        "normalizedQuery": "order the second option",
        "topIntent": "None",
        "intents": {
            "None": {
                "score": 0.7124502
            }
        },
        "entities": {
            "ordinal": [
                {
                    "offset": 2,
                    "relativeTo": "start"
                }
            ],
            "$instance": {
                "ordinal": [
                  {
                    "type": "builtin.ordinal",
                    "text": "second",
                    "startIndex": 10,
                    "length": 6,
                    "modelTypeId": 2,
                    "modelType": "Prebuilt Entity Extractor",
                    "recognitionSources": [
                        "model"
                    ]
                  }
                ]
            }
        }
    }
}
```

## <a name="next-steps"></a>次の手順

[OrdinalV2](luis-reference-prebuilt-ordinal-v2.md)、[phone number](luis-reference-prebuilt-phonenumber.md)、[temperature](luis-reference-prebuilt-temperature.md) エンティティについて学習します。 
