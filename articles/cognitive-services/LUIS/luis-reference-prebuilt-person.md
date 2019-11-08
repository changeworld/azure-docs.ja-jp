---
title: PersonName 作成済みエンティティ - LUIS
titleSuffix: Azure Cognitive Services
description: この記事では、Language Understanding (LUIS) での personName 作成済みエンティティについて説明します。
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 05/07/2019
ms.author: diberry
ms.openlocfilehash: 9777c62d97c70d4f6a0d0a4d912dea3fa8decd23
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/04/2019
ms.locfileid: "73499555"
---
# <a name="personname-prebuilt-entity-for-a-luis-app"></a>LUIS アプリの PersonName 作成済みエンティティ
作成済みの personName エンティティは、人の名前を検出します。 このエンティティは既にトレーニングされているので、personName を含む発話の例をアプリケーション意図に追加する必要はありません。 personName エンティティは、英語および中国語の[カルチャ](luis-reference-prebuilt-entities.md)でサポートされます。

## <a name="resolution-for-personname-entity"></a>personName エンティティの解決

クエリに対して次のエンティティ オブジェクトが返されます。

`Is Jill Jones in Cairo?`


#### <a name="v3-responsetabv3"></a>[V3 の応答](#tab/V3)


次の JSON は、`verbose` パラメーターが `false` に設定されている場合です。

```json
"entities": {
    "personName": [
        "Jill Jones"
    ]
}
```
#### <a name="v3-verbose-responsetabv3-verbose"></a>[V3 の詳細な応答](#tab/V3-verbose)
次の JSON は、`verbose` パラメーターが `true` に設定されている場合です。

```json
"entities": {
    "personName": [
        "Jill Jones"
    ],
    "$instance": {
        "personName": [
            {
                "type": "builtin.personName",
                "text": "Jill Jones",
                "startIndex": 3,
                "length": 10,
                "modelTypeId": 2,
                "modelType": "Prebuilt Entity Extractor",
                "recognitionSources": [
                    "model"
                ]
            }
        ],
    }
}
```
#### <a name="v2-responsetabv2"></a>[V2 の応答](#tab/V2)

次の例では、**builtin.personName** エンティティの解決を示します。

```json
"entities": [
{
    "entity": "Jill Jones",
    "type": "builtin.personName",
    "startIndex": 3,
    "endIndex": 12
}
]
```
* * * 

## <a name="next-steps"></a>次の手順

[V3 予測エンドポイント](luis-migration-api-v3.md)の詳細について学習します。

[email](luis-reference-prebuilt-email.md)、[number](luis-reference-prebuilt-number.md)、[ordinal](luis-reference-prebuilt-ordinal.md) エンティティについて学習します。 
