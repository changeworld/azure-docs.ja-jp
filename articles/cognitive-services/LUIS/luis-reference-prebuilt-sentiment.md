---
title: 感情分析 - LUIS
titleSuffix: Azure Cognitive Services
description: センチメント分析が構成されている場合、LUIS の json 応答には、センチメント分析が含まれます。
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 10/22/2019
ms.author: diberry
ms.openlocfilehash: a91246e4a4b3dc98bf11d2b014478fd40252f14b
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/04/2019
ms.locfileid: "73506874"
---
# <a name="sentiment-analysis"></a>センチメント分析
センチメント分析が構成されている場合、LUIS の json 応答には、センチメント分析が含まれます。 センチメント分析の詳細については、[Text Analytics](https://docs.microsoft.com/azure/cognitive-services/text-analytics/) のドキュメントを参照してください。


## <a name="resolution-for-sentiment"></a>センチメントの解決

センチメント データは 1 と 0 の間のスコアで、1 に近いほどポジティブなセンチメントを示し、0 に近いほどネガティブな感情を示します。

#### <a name="english-languagetabenglish"></a>[英語](#tab/english)

カルチャが `en-us` の場合、応答は次のとおりです。

```JSON
"sentimentAnalysis": {
  "label": "positive",
  "score": 0.9163064
}
```

#### <a name="other-languagestabother-languages"></a>[その他の言語](#tab/other-languages)

その他のすべてのカルチャについては、応答は次のとおりです。

```JSON
"sentimentAnalysis": {
  "score": 0.9163064
}
```
* * * 

## <a name="next-steps"></a>次の手順

[V3 予測エンドポイント](luis-migration-api-v3.md)の詳細について学習します。

