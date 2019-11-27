---
title: 機械学習エンティティ型 - LUIS
titleSuffix: Azure Cognitive Services
description: ''
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
ms.date: 11/11/2019
ms.author: diberry
ms.openlocfilehash: 2289e8ac7744a7b4cbee300e77efda89d29ee2f5
ms.sourcegitcommit: ae8b23ab3488a2bbbf4c7ad49e285352f2d67a68
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/13/2019
ms.locfileid: "74017208"
---
# <a name="machine-learned-entity"></a>機械学習エンティティ 



**エンティティは、次のようなテキスト データに最適です。**


![リスト エンティティ](./media/luis-concept-entities/list-entity.png)

## <a name="example-json"></a>JSON の例

アプリに `Cities` いう名前のリストがあり、空港がある都市 (Sea-tac)、空港コード (SEA)、郵便番号 (98101)、および電話の市外局番 (206) など、都市名のバリエーションに対応しています。

|リスト項目|項目のシノニム|
|---|---|
|`Seattle`|`sea-tac`、`sea`、`98101`、`206`、`+1` |
|`Paris`|`cdg`、`roissy`、`ory`、`75001`、`1`、`+33`|

`book 2 tickets to paris`

前の発話では、単語 `paris` は、`Cities` リスト エンティティの一部として、パリ項目にマップされます。 このリスト エンティティは、項目のシノニムだけでなく、項目の正規化された名前と一致します。

#### <a name="v2-prediction-endpoint-responsetabv2"></a>[V2 予測エンドポイントの応答](#tab/V2)

```JSON
  "entities": [
    {
      "entity": "paris",
      "type": "Cities",
      "startIndex": 18,
      "endIndex": 22,
      "resolution": {
        "values": [
          "Paris"
        ]
      }
    }
  ]
```

#### <a name="v3-prediction-endpoint-responsetabv3"></a>[V3 予測エンドポイントの応答](#tab/V3)


これは、クエリ文字列で `verbose=false` が設定されている場合の JSON です。

```json
"entities": {
    "Cities": [
        [
            "Paris"
        ]
    ]
}
```

これは、クエリ文字列で `verbose=true` が設定されている場合の JSON です。

```json
"entities": {
    "Cities": [
        [
            "Paris"
        ]
    ],
    "$instance": {
        "Cities": [
            {
                "type": "Cities",
                "text": "paris",
                "startIndex": 18,
                "length": 5,
                "modelTypeId": 5,
                "modelType": "List Entity Extractor",
                "recognitionSources": [
                    "model"
                ]
            }
        ]
    }
}
```

* * * 

|データ オブジェクト|エンティティ名|値|
|--|--|--|
|リスト エンティティ|`Cities`|`paris`|


## <a name="next-steps"></a>次の手順

[リスト](reference-entity-list.md) エンティティと[正規表現](reference-entity-regular-expression.md)エンティティについて説明します。