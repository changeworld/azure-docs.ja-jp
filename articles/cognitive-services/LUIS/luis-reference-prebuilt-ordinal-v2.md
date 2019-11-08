---
title: 事前作成済み ordinal V2 エンティティ - LUIS
titleSuffix: Azure Cognitive Services
description: この記事には、Language Understanding (LUIS) の事前作成済み ordinal V2 エンティティの情報が含まれています。
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 09/27/2019
ms.author: diberry
ms.openlocfilehash: 02bc6657126cb1cf241c2ca4668e62bd49608d4b
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/04/2019
ms.locfileid: "73491256"
---
# <a name="ordinal-v2-prebuilt-entity-for-a-luis-app"></a>LUIS アプリの事前作成済み ordinal V2 エンティティ
ordinal V2 (序数) では、[ordinal](luis-reference-prebuilt-ordinal.md) を拡張して、`next`、`last`、`previous` などの相対参照を提供します。 事前作成済み ordinal エンティティを使用しても、これらは抽出されません。

## <a name="resolution-for-prebuilt-ordinal-v2-entity"></a>事前作成済み ordinal V2 エンティティの解決

次のエンティティ オブジェクトがクエリに対して返されます。

`what is the second to last choice in the list`

#### <a name="v3-responsetabv3"></a>[V3 の応答](#tab/V3)

次の JSON は、`verbose` パラメーターが `false` に設定されている場合です。

```json
"entities": {
    "ordinalV2": [
        {
            "offset": -1,
            "relativeTo": "end"
        }
    ]
}
```

#### <a name="v3-verbose-responsetabv3-verbose"></a>[V3 の詳細な応答](#tab/V3-verbose)

次の JSON は、`verbose` パラメーターが `true` に設定されている場合です。

```json
"entities": {
    "ordinalV2": [
        {
            "offset": -1,
            "relativeTo": "end"
        }
    ],
    "$instance": {
        "ordinalV2": [
            {
                "type": "builtin.ordinalV2.relative",
                "text": "the second to last",
                "startIndex": 8,
                "length": 18,
                "modelTypeId": 2,
                "modelType": "Prebuilt Entity Extractor",
                "recognitionSources": [
                    "model"
                ]
            }
        ]
    }
}
```
#### <a name="v2-responsetabv2"></a>[V2 の応答](#tab/V2)

次の例は、**builtin.ordinalV2** エンティティの解決を示しています。

```json
"entities": [
    {
        "entity": "the second to last",
        "type": "builtin.ordinalV2.relative",
        "startIndex": 8,
        "endIndex": 25,
        "resolution": {
            "offset": "-1",
            "relativeTo": "end"
        }
    }
]
```
* * * 

## <a name="next-steps"></a>次の手順

[V3 予測エンドポイント](luis-migration-api-v3.md)の詳細について学習します。

[percentage](luis-reference-prebuilt-percentage.md)、[phone number](luis-reference-prebuilt-phonenumber.md)、および [temperature](luis-reference-prebuilt-temperature.md) エンティティについて学習します。 
