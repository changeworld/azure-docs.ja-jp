---
title: LUIS 作成済みエンティティ phonenumber リファレンス - Azure | Microsoft Docs
titleSuffix: Azure
description: この記事には、Language Understanding (LUIS) での phonenumber 作成済みエンティティについての情報が含まれます。
services: cognitive-services
author: v-geberr
manager: kaiqb
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 06/20/2018
ms.author: v-geberr
ms.openlocfilehash: 0f72b807b9b0ec110a80d67babb1c45902b8c810
ms.sourcegitcommit: 65b399eb756acde21e4da85862d92d98bf9eba86
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/22/2018
ms.locfileid: "36321564"
---
# <a name="phonenumber-entity"></a>phonenumber エンティティ
`phonenumber` エンティティは、国番号を含むさまざまな電話番号を抽出します。 このエンティティは既にトレーニングされているので、発話の例をアプリケーションに追加する必要はありません。 `phonenumber` エンティティは、`en-us` カルチャでのみサポートされます。 

## <a name="types-of-phonenumber"></a>phonenumber のタイプ
phonenumber は [Recognizers-text](https://github.com/Microsoft/Recognizers-Text/blob/master/Patterns/Base-PhoneNumbers.yaml) Github リポジトリから管理されます。

## <a name="resolution-for-prebuilt-phonenumber-entity"></a>作成済み phonenumber エンティティの解決
次の例では、**builtin.phonenumber** エンティティの解決を示します。

```JSON
{
  "query": "my mobile is 00 44 161 1234567",
  "topScoringIntent": {
    "intent": "None",
    "score": 0.8448457
  },
  "intents": [
    {
      "intent": "None",
      "score": 0.8448457
    }
  ],
  "entities": [
    {
      "entity": "00 44 161 1234567",
      "type": "builtin.phonenumber",
      "startIndex": 13,
      "endIndex": 29,
      "resolution": {
        "value": "00 44 161 1234567"
      }
    }
  ]
}
```


## <a name="next-steps"></a>次の手順

[percentage](luis-reference-prebuilt-percentage.md)、[number](luis-reference-prebuilt-number.md)、[temperature](luis-reference-prebuilt-temperature.md) エンティティについて学習します。 