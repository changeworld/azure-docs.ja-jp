---
title: 'クイック スタート: 画像から顔の表情を認識する - Emotion API (cURL)'
titlesuffix: Azure Cognitive Services
description: Emotion API と cURL の使用をすぐに開始するために役立つ情報とコード サンプルを提供します。
services: cognitive-services
author: anrothMSFT
manager: cgronlun
ms.service: cognitive-services
ms.component: emotion-api
ms.topic: quickstart
ms.date: 05/23/2017
ms.author: anroth
ROBOTS: NOINDEX
ms.openlocfilehash: dfdaa89c9d29e419539f385f601dc7f264bf838e
ms.sourcegitcommit: 1981c65544e642958917a5ffa2b09d6b7345475d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/03/2018
ms.locfileid: "48237077"
---
# <a name="quickstart-build-an-app-to-recognize-emotions-on-faces-in-an-image"></a>クイック スタート: 画像から顔の表情を認識するアプリの作成

> [!IMPORTANT]
> Emotion API は、2019 年 2 月 15 日に非推奨となる予定です。 現在は、[Face API](https://docs.microsoft.com/azure/cognitive-services/face/) の一部として感情認識機能が一般提供されています。

この記事では、[Emotion API Recognize メソッド](https://westus.dev.cognitive.microsoft.com/docs/services/5639d931ca73072154c1ce89/operations/563b31ea778daf121cc3a5fa) と cURL を使って、画像内の 1 人以上の個人が表す感情を認識するための、役立つ情報とコード サンプルを提供します。

## <a name="prerequisite"></a>前提条件
* 無料のサブスクリプション キーを[ここから](https://azure.microsoft.com/try/cognitive-services/)入手します。

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
