---
title: 'Computer Vision API cURL クイック スタート: リモート画像の分析 | Microsoft Docs'
titleSuffix: Microsoft Cognitive Services
description: このクイック スタートでは、Cognitive Services の Computer Vision と cURL を使ってリモート画像を分析します。
services: cognitive-services
author: noellelacharite
manager: nolachar
ms.service: cognitive-services
ms.component: computer-vision
ms.topic: quickstart
ms.date: 08/28/2018
ms.author: v-deken
ms.openlocfilehash: c092432adbcba9cf7a131584131fd05991d1edef
ms.sourcegitcommit: 0c64460a345c89a6b579b1d7e273435a5ab4157a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/31/2018
ms.locfileid: "43772275"
---
# <a name="quickstart-analyze-a-remote-image---rest-curl"></a>クイック スタート: リモート画像の分析 - REST、cURL

このクイック スタートでは、Computer Vision を使ってリモート画像を分析し、視覚的特徴を抽出します。 ローカル画像の分析については、[cURL を使ったローカル画像の分析](curl-disk.md)に関するページをご覧ください。

## <a name="prerequisites"></a>前提条件

Computer Vision を使用するにはサブスクリプション キーが必要です。「[サブスクリプション キーを取得する](../Vision-API-How-to-Topics/HowToSubscribe.md)」をご覧ください。

## <a name="analyze-a-remote-image"></a>リモート画像の分析

[画像分析メソッド](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa)を使うと、画像の内容に基づいて視覚的特徴を抽出できます。 画像をアップロードするか、画像の URL を指定して、取得する特徴を選択することができます。その例を次に示します。

* 画像の内容に関連するタグの詳細なリスト。
* 画像の内容を完全な文章で説明したもの。
* 画像に含まれている顔の座標、性別、年齢。
* ImageType (クリップ アートまたは線画)。
* ドミナント カラー、アクセント カラー、白黒画像かどうか。
* こちらの[分類](../Category-Taxonomy.md)で定義されているカテゴリ。
* 成人向けのコンテンツや性的な含みのあるコンテンツが画像に含まれているか。

このサンプルを実行するには、次の手順を実行します。

1. エディターに次のコードをコピーします。
1. `<Subscription Key>` を、有効なサブスクリプション キーに置き換えます。
1. 必要に応じて、サブスクリプション キーの取得場所を使用するように要求 URL (`https://westcentralus.api.cognitive.microsoft.com/vision/v2.0`) を変更します。
1. 必要に応じて、分析対象の画像 (`{\"url\":\"...`) を変更します。
1. 必要に応じて、応答の言語を変更します (`language=en`)。
1. cURL がインストールされているコンピューターのコマンド ウィンドウを開きます。
1. ウィンドウにコードを貼り付けて、コマンドを実行します。

>[!NOTE]
>REST 呼び出しには、サブスクリプション キーの取得に使用したのと同じ場所を使用する必要があります。 たとえば、サブスクリプション キーを westus から取得した場合、下記の URL の "westcentralus" を "westus" に置き換えます。

## <a name="analyze-image-request"></a>画像分析要求

```json
curl -H "Ocp-Apim-Subscription-Key: <Subscription Key>" -H "Content-Type: application/json" "https://westcentralus.api.cognitive.microsoft.com/vision/v2.0/analyze?visualFeatures=Categories,Description&details=Landmarks&language=en" -d "{\"url\":\"http://upload.wikimedia.org/wikipedia/commons/3/3c/Shaki_waterfall.jpg\"}"
```

## <a name="analyze-image-response"></a>画像分析応答

成功応答が JSON で返されます。その例を次に示します。

```json
{
  "categories": [
    {
      "name": "outdoor_water",
      "score": 0.9921875,
      "detail": {
        "landmarks": []
      }
    }
  ],
  "description": {
    "tags": [
      "nature",
      "water",
      "waterfall",
      "outdoor",
      "rock",
      "mountain",
      "rocky",
      "grass",
      "hill",
      "covered",
      "hillside",
      "standing",
      "side",
      "group",
      "walking",
      "white",
      "man",
      "large",
      "snow",
      "grazing",
      "forest",
      "slope",
      "herd",
      "river",
      "giraffe",
      "field"
    ],
    "captions": [
      {
        "text": "a large waterfall over a rocky cliff",
        "confidence": 0.916458423253597
      }
    ]
  },
  "requestId": "b6e33879-abb2-43a0-a96e-02cb5ae0b795",
  "metadata": {
    "height": 959,
    "width": 1280,
    "format": "Jpeg"
  }
}
```

## <a name="next-steps"></a>次の手順

画像の分析、著名人やランドマークの検出、サムネイルの作成、印刷されたテキストや手書きテキストの抽出に使用される Computer Vision API の詳細を確認します。 Computer Vision API を簡単に試す場合は、[Open API テスト コンソール](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa/console)をお試しください。

> [!div class="nextstepaction"]
> [Computer Vision API の詳細](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44)
