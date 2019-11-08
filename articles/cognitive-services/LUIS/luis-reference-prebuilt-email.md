---
title: LUIS 作成済みエンティティ email リファレンス
titleSuffix: Azure Cognitive Services
description: この記事には、Language Understanding (LUIS) での email 作成済みエンティティについての情報が含まれます。
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 09/27/2019
ms.author: diberry
ms.openlocfilehash: 6f262752a50b58eae8ffbea81b8e7fc4d8c65b98
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/04/2019
ms.locfileid: "73464980"
---
# <a name="email-prebuilt-entity-for-a-luis-app"></a>LUIS アプリの email 作成済みエンティティ
email の抽出には、発話からのメール アドレス全体が含まれます。 このエンティティは既にトレーニングされているので、メールを含む発話の例をアプリケーション意図に追加する必要はありません。 email エンティティは、`en-us` カルチャでのみサポートされます。 

## <a name="resolution-for-prebuilt-email"></a>作成済みの email の解決

クエリに対して次のエンティティ オブジェクトが返されます。

`please send the information to patti@contoso.com`

#### <a name="v3-responsetabv3"></a>[V3 の応答](#tab/V3)

次の JSON は、`verbose` パラメーターが `false` に設定されている場合です。

```json
"entities": {
    "email": [
        "patti@contoso.com"
    ]
}
```
#### <a name="v3-verbose-responsetabv3-verbose"></a>[V3 の詳細な応答](#tab/V3-verbose)

次の JSON は、`verbose` パラメーターが `true` に設定されている場合です。

```json
"entities": {
    "email": [
        "patti@contoso.com"
    ],
    "$instance": {
        "email": [
            {
                "type": "builtin.email",
                "text": "patti@contoso.com",
                "startIndex": 31,
                "length": 17,
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

次の例では、**builtin.email** エンティティの解決を示します。

```json
"entities": [
    {
        "entity": "patti@contoso.com",
        "type": "builtin.email",
        "startIndex": 31,
        "endIndex": 55,
        "resolution": {
        "value": "patti@contoso.com"
        }
    }
]
```
* * * 

## <a name="next-steps"></a>次の手順

[V3 予測エンドポイント](luis-migration-api-v3.md)の詳細について学習します。

[number](luis-reference-prebuilt-number.md)、[ordinal](luis-reference-prebuilt-ordinal.md)、[percentage](luis-reference-prebuilt-percentage.md) について学習します。 
