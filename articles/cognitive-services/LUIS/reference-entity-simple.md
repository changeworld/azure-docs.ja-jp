---
title: シンプル エンティティ型
titleSuffix: Language Understanding - Azure Cognitive Services
description: シンプル エンティティとは、1 つの概念について説明し、機械学習コンテキストから学習した汎用的エンティティです。 シンプル エンティティは、一般に会社名、製品名、その他のカテゴリの名前などの名前なので、使用する名前のシグナルの強化にシンプル エンティティを使用する場合は、フレーズ リストを追加します。
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
ms.date: 07/24/2019
ms.author: diberry
ms.openlocfilehash: 3f03b33f685bb5c7c9ba8f2267b8556c5dadade4
ms.sourcegitcommit: 75a56915dce1c538dc7a921beb4a5305e79d3c7a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/24/2019
ms.locfileid: "68480135"
---
# <a name="simple-entity"></a>シンプル エンティティ 

シンプル エンティティとは、1 つの概念について説明し、機械学習コンテキストから学習した汎用的エンティティです。 シンプル エンティティは、一般に会社名、製品名、その他のカテゴリの名前などの名前なので、使用する名前のシグナルの強化にシンプル エンティティを使用する場合は、[フレーズ リスト](luis-concept-feature.md)を追加します。 

**エンティティは、次のような場合に最適です。**

* データが一貫して書式設定されていないが、同じことを示している。 

![シンプル エンティティ](./media/luis-concept-entities/simple-entity.png)

## <a name="example-json"></a>JSON の例

`Bob Jones wants 3 meatball pho`

前の発話では、`Bob Jones` は、シンプルな `Customer` エンティティとしてラベルが付けられています。

エンドポイントから返されるデータには、エンティティ名、発話から検出されたテキスト、検出されたテキストの場所、およびスコアが含まれます。

```JSON
"entities": [
  {
  "entity": "bob jones",
  "type": "Customer",
  "startIndex": 0,
  "endIndex": 8,
  "score": 0.473899543
  }
]
```

|データ オブジェクト|エンティティ名|値|
|--|--|--|
|シンプル エンティティ|`Customer`|`bob jones`|

## <a name="next-steps"></a>次の手順

この[チュートリアル](luis-quickstart-primary-and-secondary-data.md)では、**Simple エンティティ**を使用して発話から雇用ジョブ名の機械学習データを抽出します。 抽出精度を向上させるために、Simple エンティティに固有の用語の[フレーズ リスト](luis-concept-feature.md)を追加します。