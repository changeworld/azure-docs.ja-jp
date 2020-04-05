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
ms.topic: reference
ms.date: 09/27/2019
ms.author: diberry
ms.openlocfilehash: f7aa2a341a82d30f7022af49e626a3cd358854b7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/28/2020
ms.locfileid: "78273477"
---
# <a name="email-prebuilt-entity-for-a-luis-app"></a>LUIS アプリの email 作成済みエンティティ
email の抽出には、発話からのメール アドレス全体が含まれます。 このエンティティは既にトレーニングされているので、メールを含む発話の例をアプリケーション意図に追加する必要はありません。 email エンティティは、`en-us` カルチャでのみサポートされます。

## <a name="resolution-for-prebuilt-email"></a>作成済みの email の解決

次のエンティティ オブジェクトがクエリに対して返されます。

`please send the information to patti@contoso.com`

#### <a name="v3-response"></a>[V3 の応答](#tab/V3)

次の JSON は、`verbose` パラメーターが `false` に設定されている場合です。

```json
"entities": {
    "email": [
        "patti@contoso.com"
    ]
}
```
#### <a name="v3-verbose-response"></a>[V3 の詳細な応答](#tab/V3-verbose)

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
#### <a name="v2-response"></a>[V2 の応答](#tab/V2)

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

## <a name="next-steps"></a>次のステップ

[V3 予測エンドポイント](luis-migration-api-v3.md)の詳細について学習します。

[number](luis-reference-prebuilt-number.md)、[ordinal](luis-reference-prebuilt-ordinal.md)、[percentage](luis-reference-prebuilt-percentage.md) について学習します。
