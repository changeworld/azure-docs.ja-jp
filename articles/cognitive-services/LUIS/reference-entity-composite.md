---
title: 複合エンティティ型 - LUIS
titleSuffix: Azure Cognitive Services
description: 複合エンティティは、事前構築済みエンティティ、シンプル、正規表現、リスト エンティティなどの他のエンティティで構成されます。 個別のエンティティが、エンティティ全体を形成します。
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
ms.date: 07/24/2019
ms.author: diberry
ms.openlocfilehash: ea258275cf954bc6e06da03324c2ae93de0e7fde
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/26/2019
ms.locfileid: "68563247"
---
# <a name="composite-entity"></a>複合エンティティ 

複合エンティティは、事前構築済みエンティティ、シンプル、正規表現、リスト エンティティなどの他のエンティティで構成されます。 個別のエンティティが、エンティティ全体を形成します。 

**このエンティティは、次のようなデータに最適です。**

* 相互に関連している。 
* 発話のコンテキストで相互に関連する。
* さまざまなエンティティ型を使用する。
* クライアント アプリケーションによって情報の単位としてグループ化され、処理される必要がある。
* 機械学習を必要とするさまざまなユーザー発話がある。

![複合エンティティ](./media/luis-concept-entities/composite-entity.png)

## <a name="example-json"></a>JSON の例

次の発話で事前構築済みの `number` と `Location::ToLocation` の複合エンティティを考えてみましょう。

`book 2 tickets to paris`

number の `2` と ToLocation `paris` の間には、どのエンティティにも属さない単語があります。 [LUIS](luis-reference-regions.md) Web サイトの、ラベルの付いた発話で使用される緑色の下線が複合エンティティを示します。

![複合エンティティ](./media/luis-concept-data-extraction/composite-entity.png)

複合エンティティは、`compositeEntities` 配列で返され、その複合内のすべてのエンティティも、`entities` 配列で返されます。

```JSON

"entities": [
    {
    "entity": "2 tickets to cairo",
    "type": "ticketInfo",
    "startIndex": 0,
    "endIndex": 17,
    "score": 0.67200166
    },
    {
    "entity": "2",
    "type": "builtin.number",
    "startIndex": 0,
    "endIndex": 0,
    "resolution": {
        "subtype": "integer",
        "value": "2"
    }
    },
    {
    "entity": "cairo",
    "type": "builtin.geographyV2",
    "startIndex": 13,
    "endIndex": 17
    }
],
"compositeEntities": [
    {
    "parentType": "ticketInfo",
    "value": "2 tickets to cairo",
    "children": [
        {
        "type": "builtin.geographyV2",
        "value": "cairo"
        },
        {
        "type": "builtin.number",
        "value": "2"
        }
    ]
    }
]
```    

|データ オブジェクト|エンティティ名|値|
|--|--|--|
|事前構築済みのエンティティ - number|"builtin.number"|"2"|
|事前構築済みエンティティ - GeographyV2|"Location::ToLocation"|"paris"|

## <a name="next-steps"></a>次の手順

この[チュートリアル](luis-tutorial-composite-entity.md)では、さまざまな種類の抽出されたデータを、1 つの包含するエンティティにバンドルするための**複合エンティティ**を追加します。 データをバンドルすることにより、クライアント アプリケーションはさまざまなデータ型で関連データを簡単に抽出できます。
