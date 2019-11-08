---
title: インクルード ファイル
description: インクルード ファイル
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: luis
ms.topic: include
ms.custom: include file
ms.date: 10/19/2019
ms.author: diberry
ms.openlocfilehash: ba400beb13d0bd4b29eee0c60617ec7ad128dd77
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/04/2019
ms.locfileid: "73505829"
---
発話の例は、特定の形式に従っています。 

`text` フィールドには、サンプルの発話テキストが含まれています。 `intentName` フィールドは、LUIS アプリの既存の意図の名前と対応している必要があります。 `entityLabels` フィールドは必須です。 どのエンティティもラベル付けしない場合は、空の配列を指定します。

entityLabels 配列が空でない場合、`entityName` フィールドで示されるエンティティを `startCharIndex` および `endCharIndex` で指定する必要があります。 インデックスは 0 から始まります。つまり上の例の 6 は、大文字の S の前の空白ではなく、Seattle の「S」を指します。テキスト内のスペースで始まる、または終わるラベルを指定すると、発話に追加する API 呼び出しは失敗します。

```JSON
[
  {
    "text": "go to Seattle today",
    "intentName": "BookFlight",
    "entityLabels": [
      {
        "entityName": "Location::LocationTo",
        "startCharIndex": 6,
        "endCharIndex": 12
      }
    ]
  },
  {
    "text": "purple dogs are difficult to work with",
    "intentName": "BookFlight",
    "entityLabels": []
  }
]
```
