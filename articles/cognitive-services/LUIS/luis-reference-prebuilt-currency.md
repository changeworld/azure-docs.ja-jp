---
title: currency 作成済みエンティティ
titleSuffix: Azure
description: この記事には、Language Understanding (LUIS) での currency 作成済みエンティティについての情報が含まれます。
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: article
ms.date: 05/07/2019
ms.author: diberry
ms.openlocfilehash: 7e882a66ae5a090e1fd3a0850ff35281dc4e692d
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/27/2019
ms.locfileid: "65072019"
---
# <a name="currency-prebuilt-entity-for-a-luis-app"></a>LUIS アプリの currency 作成済みエンティティ
作成済みの currency エンティティは、LUIS アプリのカルチャに関係なく、多くの貨幣単位や国/地域の通貨を検出します。 このエンティティは既にトレーニングされているので、通貨を含む発話の例をアプリケーション意図に追加する必要はありません。 currency エンティティは、[多くのカルチャ](luis-reference-prebuilt-entities.md)でサポートされています。 

## <a name="types-of-currency"></a>currency の種類
currency は [Recognizers-text](https://github.com/Microsoft/Recognizers-Text/blob/master/Patterns/English/English-NumbersWithUnit.yaml#L26) GitHub リポジトリから管理されます

## <a name="resolution-for-currency-entity"></a>currency エンティティの解決

### <a name="api-version-2x"></a>API バージョン 2.x

次の例では、**builtin.currency** エンティティの解決を示します。

```json
{
  "query": "search for items under $10.99",
  "topScoringIntent": {
    "intent": "SearchForItems",
    "score": 0.926173568
  },
  "intents": [
    {
      "intent": "SearchForItems",
      "score": 0.926173568
    },
    {
      "intent": "None",
      "score": 0.07376878
    }
  ],
  "entities": [
    {
      "entity": "$10.99",
      "type": "builtin.currency",
      "startIndex": 23,
      "endIndex": 28,
      "resolution": {
        "unit": "Dollar",
        "value": "10.99"
      }
    }
  ]
}
```



### <a name="preview-api-version-3x"></a>プレビュー API バージョン 3.x

次の JSON は、`verbose` パラメーターが `false` に設定されている場合です。

```json
{
    "query": "search for items under $10.99",
    "prediction": {
        "normalizedQuery": "search for items under $10.99",
        "topIntent": "None",
        "intents": {
            "None": {
                "score": 0.605889857
            }
        },
        "entities": {
            "money": [
                {
                    "number": 10.99,
                    "unit": "Dollar"
                }
            ]
        }
    }
}
```

次の JSON は、`verbose` パラメーターが `true` に設定されている場合です。

```json
{
    "query": "search for items under $10.99",
    "prediction": {
        "normalizedQuery": "search for items under $10.99",
        "topIntent": "None",
        "intents": {
            "None": {
                "score": 0.605889857
            }
        },
        "entities": {
            "money": [
                {
                    "number": 10.99,
                    "unit": "Dollar"
                }
            ],
            "$instance": {
                "money": [
                    {
                        "type": "builtin.currency",
                        "text": "$10.99",
                        "startIndex": 23,
                        "length": 6,
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

[datetimeV2](luis-reference-prebuilt-datetimev2.md)、[dimension](luis-reference-prebuilt-dimension.md)、[email](luis-reference-prebuilt-email.md) エンティティについて学習します。 
