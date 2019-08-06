---
title: Pattern.any エンティティ型
titleSuffix: Language Understanding - Azure Cognitive Services
description: Pattern.any は、エンティティの開始位置と終了位置を示すためにパターンのテンプレート発話でのみ使用される、可変長プレースホルダーです。
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
ms.date: 07/24/2019
ms.author: diberry
ms.openlocfilehash: f3e5bd068b5df99bdfabff0bfe18ccebff980fc6
ms.sourcegitcommit: 75a56915dce1c538dc7a921beb4a5305e79d3c7a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/24/2019
ms.locfileid: "68480115"
---
# <a name="patternany-entity"></a>Pattern.any エンティティ 

Pattern.any は、エンティティの開始位置と終了位置を示すためにパターンのテンプレート発話でのみ使用される、可変長プレースホルダーです。  

Pattern.any エンティティは、意図のユーザー例内ではなく、[パターン](luis-how-to-model-intent-pattern.md) テンプレート例内でマークする必要があります。

**エンティティは、次のような場合に最適です。**

* エンティティの末尾を発話の残りのテキストと混同する可能性がある。 

## <a name="usage"></a>使用法

タイトルに基づいて書籍を検索するクライアント アプリケーションでは、pattern.any によって完全なタイトルが抽出されます。 この書籍検索に対して pattern.any を使用するテンプレート発話は `Was {BookTitle} written by an American this year[?]` です。 

次の表では、各行に 2 つのバージョンの発話があります。 一番上の発話は、LUIS が最初に発話を認識する方法です。 書籍タイトルの開始位置と終了位置が明確ではありません。 一番下の発話は、Pattern.any エンティティを使用してエンティティの開始と終了をマークします。 

|太字はエンティティを含む発話|
|--|
|`Was The Man Who Mistook His Wife for a Hat and Other Clinical Tales written by an American this year?`<br><br>「**The Man Who Mistook His Wife for a Hat and Other Clinical Tales (自分の妻を帽子と間違える男やその他の臨床例)** 」は、アメリカ人によって今年執筆されましたか?|
|`Was Half Asleep in Frog Pajamas written by an American this year?`<br><br>「**Half Asleep in Frog Pajamas (カエルのパジャマで夢うつつ)** 」は、アメリカ人によって今年執筆されましたか?|
|`Was The Particular Sadness of Lemon Cake: A Novel written by an American this year?`<br><br>「**The Particular Sadness of Lemon Cake:A Novel (悲しきレモン ケーキ: 小説)** 」は、アメリカ人によって今年執筆されましたか?|
|`Was There's A Wocket In My Pocket! written by an American this year?`<br><br>「**There's A Wocket In My Pocket! (ポケットにウォケットが!)** 」 は、アメリカ人によって今年執筆されましたか?|
||

## <a name="example-json"></a>JSON の例

```JSON
{
  "query": "where is the form Understand your responsibilities as a member of the community and who needs to sign it after I read it?",
  "topScoringIntent": {
    "intent": "FindForm",
    "score": 0.999999464
  },
  "intents": [
    {
      "intent": "FindForm",
      "score": 0.999999464
    },
    {
      "intent": "GetEmployeeBenefits",
      "score": 4.883697E-06
    },
    {
      "intent": "None",
      "score": 1.02040713E-06
    },
    {
      "intent": "GetEmployeeOrgChart",
      "score": 9.278342E-07
    },
    {
      "intent": "MoveAssetsOrPeople",
      "score": 9.278342E-07
    }
  ],
  "entities": [
    {
      "entity": "understand your responsibilities as a member of the community",
      "type": "FormName",
      "startIndex": 18,
      "endIndex": 78,
      "role": ""
    }
  ]
}
```

## <a name="next-steps"></a>次の手順

この[チュートリアル](luis-tutorial-pattern-any.md)では、発話が正しい形式であって、データの末尾が発話の残りの単語と混同しやすい可能性がある場合に、**Pattern.any** エンティティを使用して発話からデータを抽出します。