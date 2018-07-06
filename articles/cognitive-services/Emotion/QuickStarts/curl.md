---
title: Emotion API cURL クイック スタート | Microsoft Docs
description: Cognitive Services の Emotion API と cURL の使用をすぐに開始するために役立つ情報とコード サンプルを提供します。
services: cognitive-services
author: anrothMSFT
manager: corncar
ms.service: cognitive-services
ms.component: emotion-api
ms.topic: article
ms.date: 05/23/2017
ms.author: anroth
ms.openlocfilehash: a7ca2cac718797462bb4dc889b3f1361b252435e
ms.sourcegitcommit: 0fa8b4622322b3d3003e760f364992f7f7e5d6a9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/27/2018
ms.locfileid: "37021100"
---
# <a name="emotion-api-curl-quick-start"></a>Emotion API cURL クイック スタート

> [!IMPORTANT]
> Video API のプレビューは 2017 年 10 月 30 日をもって終了します。 新しい [Video Indexer API のプレビュー](https://azure.microsoft.com/services/cognitive-services/video-indexer/)をお試しください。ビデオから分析情報を手軽に抽出でき、ビデオ内で話される言葉や表情、性格、感情を検知することで、検索結果などのコンテンツの検索エクスペリエンスを強化できます。 [詳細情報](https://docs.microsoft.com/azure/cognitive-services/video-indexer/video-indexer-overview)。

この記事では、[Emotion API Recognize メソッド](https://westus.dev.cognitive.microsoft.com/docs/services/5639d931ca73072154c1ce89/operations/563b31ea778daf121cc3a5fa) と cURL を使って、画像内の 1 人以上の個人が表す感情を認識するための、役立つ情報とコード サンプルを提供します。 

## <a name="prerequisite"></a>前提条件
* 無料のサブスクリプション キーを[ここから](https://azure.microsoft.com/try/cognitive-services/)入手します

## <a name="recognize-emotions-curl-example-request"></a>感情認識 cURL の要求の例

> [!NOTE]
> REST 呼び出しには、サブスクリプション キーの取得に使用したのと同じ場所を使用する必要があります。 たとえば、サブスクリプション キーを westcentralus から取得した場合、下記の URL の "westus" を "westcentralus" に置き換えます。

```json
@ECHO OFF

curl -v -X POST "https://westus.api.cognitive.microsoft.com/emotion/v1.0/recognize"
-H "Content-Type: application/json"
-H "Ocp-Apim-Subscription-Key: {subscription key}"

--data-ascii "{body}" 
```

## <a name="recognize-emotions-sample-response"></a>感情認識の応答例
呼び出しが成功すると、顔エントリとそれらに関連付けられた感情スコアの配列が、顔の四角形サイズの降順で返されます。 空の応答は、顔が検出されなかったことを示します。 感情エントリには次のフィールドが含まれます。
* faceRectangle - 画像内の顔の四角形の位置。
* scores - 画像内のそれぞれの顔の Emotion スコア。 

```json
application/json 
[
  {
    "faceRectangle": {
      "left": 68,
      "top": 97,
      "width": 64,
      "height": 97
    },
    "scores": {
      "anger": 0.00300731952,
      "contempt": 5.14648448E-08,
      "disgust": 9.180124E-06,
      "fear": 0.0001912825,
      "happiness": 0.9875571,
      "neutral": 0.0009861537,
      "sadness": 1.889955E-05,
      "surprise": 0.008229999
    }
  }
]

