---
title: インクルード ファイル
description: インクルード ファイル
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.date: 05/18/2020
ms.subservice: language-understanding
ms.topic: include
ms.custom: include file
ms.author: diberry
ms.openlocfilehash: acf3968510bc45838f26c4b3cf366abdee06f298
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/19/2020
ms.locfileid: "83655513"
---
発話の例は、特定の形式に従っています。

`text` フィールドには、サンプルの発話テキストが含まれています。 `intentName` フィールドは、LUIS アプリの既存の意図の名前と対応している必要があります。 `entityLabels` フィールドは必須です。 どのエンティティもラベル付けしない場合は、空の配列を指定します。

entityLabels 配列が空でない場合、`entityName` フィールドで示されるエンティティを `startCharIndex` および `endCharIndex` で指定する必要があります。 インデックスは 0 から始まります。 テキスト内のスペースでラベルを開始または終了すると、発話を追加する API 呼び出しは失敗します。

```JSON
[
    {
        "text": "order a cheese pizza",
        "intentName": "ModifyOrder",
        "entityLabels":[]
    },
    {
        "text": "order a large pepperoni pizza",
        "intentName": "ModifyOrder",
        "entityLabels": [
            {
                "entityName": "Order",
                "startCharIndex": 8,
                "endCharIndex": 28
            }
        ]
    },
    {
        "text": "order 2 large pepperoni pizzas on thin crust",
        "intentName": "ModifyOrder",
        "entityLabels": [
            {
                "entityName": "Order",
                "startCharIndex": 6,
                "endCharIndex": 61
            },
            {
                "entityName": "FullPizzaWithModifiers",
                "startCharIndex": 6,
                "endCharIndex": 61
            },
            {
                "entity": "Quantity",
                "startPos": 6,
                "endPos": 7
            },
            {
                "entity": "PizzaType",
                "startPos": 14,
                "endPos": 22
            },
            {
                "entity": "Size",
                "startPos": 8,
                "endPos": 12
            },
            {
                "entity": "Crust",
                "startPos": 34,
                "endPos": 37
            }
        ]
    }
]
```
