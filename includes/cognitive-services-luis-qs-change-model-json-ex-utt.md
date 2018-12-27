---
title: インクルード ファイル
description: インクルード ファイル
services: cognitive-services
author: diberry
manager: cgronlun
ms.service: cognitive-services
ms.component: luis
ms.topic: include
ms.custom: include file
ms.date: 08/16/2018
ms.author: diberry
ms.openlocfilehash: a1b0afce31d7202c38b049addf546350ff347719
ms.sourcegitcommit: 4ecc62198f299fc215c49e38bca81f7eb62cdef3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/24/2018
ms.locfileid: "47044170"
---
発話ファイルの例 **utterances.json** は、特定の形式に従っています。 

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