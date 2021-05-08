---
title: 感情分析 - LUIS
titleSuffix: Azure Cognitive Services
description: センチメント分析が構成されている場合、LUIS の json 応答には、センチメント分析が含まれます。
services: cognitive-services
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
ms.date: 04/06/2021
ms.openlocfilehash: 7524644b34a6fd479c08b9ce6418c547c836add5
ms.sourcegitcommit: b0557848d0ad9b74bf293217862525d08fe0fc1d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/07/2021
ms.locfileid: "106554027"
---
# <a name="sentiment-analysis"></a>センチメント分析
センチメント分析が構成されている場合、LUIS の json 応答には、センチメント分析が含まれます。 センチメント分析の詳細については、[Text Analytics](../text-analytics/index.yml) のドキュメントを参照してください。

LUIS では Text Analytics V2 を使用します。 

感情分析は、アプリケーションを公開するときに構成されます。 詳細については、[アプリを発行する方法](./luis-how-to-publish-app.md)に関する記事を参照してください。

## <a name="resolution-for-sentiment"></a>センチメントの解決

センチメント データは 1 と 0 の間のスコアで、1 に近いほどポジティブなセンチメントを示し、0 に近いほどネガティブな感情を示します。

#### <a name="english-language"></a>[英語](#tab/english)

カルチャが `en-us` の場合、応答は次のとおりです。

```JSON
"sentimentAnalysis": {
  "label": "positive",
  "score": 0.9163064
}
```

#### <a name="other-languages"></a>[その他の言語](#tab/other-languages)

その他のすべてのカルチャについては、応答は次のとおりです。

```JSON
"sentimentAnalysis": {
  "score": 0.9163064
}
```
* * *

## <a name="next-steps"></a>次のステップ

[V3 予測エンドポイント](luis-migration-api-v3.md)の詳細について学習します。
