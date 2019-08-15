---
title: 電話番号の作成済みエンティティ - LUIS
titleSuffix: Azure Cognitive Services
description: この記事には、Language Understanding (LUIS) での phonenumber 作成済みエンティティについての情報が含まれます。
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 05/07/2019
ms.author: diberry
ms.openlocfilehash: bf2fd053cabeaf85b177e284f86ba378e0e4389e
ms.sourcegitcommit: 13a289ba57cfae728831e6d38b7f82dae165e59d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/09/2019
ms.locfileid: "68933420"
---
# <a name="phone-number-prebuilt-entity-for-a-luis-app"></a>LUIS アプリの電話番号の事前構築済みエンティティ
`phonenumber` エンティティは、国番号を含むさまざまな電話番号を抽出します。 このエンティティは既にトレーニングされているので、発話の例をアプリケーションに追加する必要はありません。 `phonenumber` エンティティは、`en-us` カルチャでのみサポートされます。 

## <a name="types-of-a-phone-number"></a>電話番号のタイプ
`Phonenumber` は、[Recognizers-text](https://github.com/Microsoft/Recognizers-Text/blob/master/Patterns/Base-PhoneNumbers.yaml) GitHub リポジトリから管理されます

## <a name="resolution-for-this-prebuilt-entity"></a>この事前構築済みエンティティのための解決

### <a name="api-version-2x"></a>API バージョン 2.x

次の例では、**builtin.phonenumber** エンティティの解決を示します。

```json
{
  "query": "my mobile is 1 (800) 642-7676",
  "topScoringIntent": {
    "intent": "None",
    "score": 0.8448457
  },
  "intents": [
    {
      "intent": "None",
      "score": 0.8448457
    }
  ],
  "entities": [
    {
        "entity": "1 (800) 642-7676",
        "type": "builtin.phonenumber",
        "startIndex": 13,
        "endIndex": 28,
        "resolution": {
            "score": "1",
            "value": "1 (800) 642-7676"
        }
    }
  ]
}
```

### <a name="preview-api-version-3x"></a>プレビュー API バージョン 3.x

次の JSON は、`verbose` パラメーターが `false` に設定されている場合です。

```json
{
    "query": "my mobile is 1 (800) 642-7676",
    "prediction": {
        "normalizedQuery": "my mobile is 1 (800) 642-7676",
        "topIntent": "None",
        "intents": {
            "None": {
                "score": 0.592748761
            }
        },
        "entities": {
            "phonenumber": [
                "1 (800) 642-7676"
            ]
        }
    }
}
```

次の JSON は、`verbose` パラメーターが `true` に設定されている場合です。

```json
{
    "query": "my mobile is 1 (800) 642-7676",
    "prediction": {
        "normalizedQuery": "my mobile is 1 (800) 642-7676",
        "topIntent": "None",
        "intents": {
            "None": {
                "score": 0.592748761
            }
        },
        "entities": {
            "phonenumber": [
                "1 (800) 642-7676"
            ],
            "$instance": {
                "phonenumber": [
                    {
                        "type": "builtin.phonenumber",
                        "text": "1 (800) 642-7676",
                        "startIndex": 13,
                        "length": 16,
                        "score": 1,
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

[percentage](luis-reference-prebuilt-percentage.md)、[number](luis-reference-prebuilt-number.md)、[temperature](luis-reference-prebuilt-temperature.md) エンティティについて学習します。 
