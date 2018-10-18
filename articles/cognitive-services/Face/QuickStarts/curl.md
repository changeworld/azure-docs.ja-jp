---
title: 'クイック スタート: 画像の中にある顔を検出する - Face API、cURL'
titleSuffix: Azure Cognitive Services
description: このクイック スタートでは、cURL で Face API を使って画像から顔を検出します。
services: cognitive-services
author: PatrickFarley
manager: cgronlun
ms.service: cognitive-services
ms.component: face-api
ms.topic: quickstart
ms.date: 05/10/2018
ms.author: pafarley
ms.openlocfilehash: da18b7b3666863742f987b2ee0da297c0838d266
ms.sourcegitcommit: 1aacea6bf8e31128c6d489fa6e614856cf89af19
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/16/2018
ms.locfileid: "49343063"
---
# <a name="quickstart-detect-faces-in-an-image-using-curl"></a>クイック スタート: cURL を使って画像の中にある顔を検出する

このクイック スタートでは、Face API を使って画像から顔を検出します。

## <a name="prerequisites"></a>前提条件

サンプルを実行するにはサブスクリプション キーが必要です。 無料試用版のサブスクリプション キーは「[Cognitive Services を試す](https://azure.microsoft.com/try/cognitive-services/?api=face-api)」から取得できます。

## <a name="detect-faces-in-an-image"></a>画像内の顔を検出する

"[顔 - 検出](https://westcentralus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236)" メソッドを使用すると、画像の中にある顔を検出して、次のような属性を取得することができます。

* Face ID: Face API の各種シナリオで使用される一意の ID。
* 顔四角形: 画像内での顔の位置を示す値 (左、上、幅、高さ)。
* ランドマーク: 顔の構成要素の重要な位置を示す 27 地点のランドマークの配列。
* 顔の属性 (年齢、性別、笑顔の強さ、頭部姿勢、顔ひげなど)。

このサンプルを実行するには、次の手順を実行します。

1. コマンド プロンプトを開きます。
2. `<Subscription Key>` を、有効なサブスクリプション キーに置き換えます。
3. 必要に応じて、サブスクリプション キーの取得場所を使用するように URL (`https://westcentralus.api.cognitive.microsoft.com/face/v1.0/detect`) を変更します。
4. 必要に応じて、分析対象の画像 (`"{\"url\":...`) を変更します。
5. ウィンドウにコードを貼り付けます。
6. コマンドを実行します。

### <a name="face---detect-request"></a>顔検出要求

> [!NOTE]
> REST 呼び出しには、サブスクリプション キーの取得に使用したのと同じ場所を使用する必要があります。 たとえば、サブスクリプション キーを westus から取得した場合、次の URL の "westcentralus" を "westus" に置き換えます。

```shell
curl -H "Ocp-Apim-Subscription-Key: <Subscription Key>" "https://westcentralus.api.cognitive.microsoft.com/face/v1.0/detect?returnFaceId=true&returnFaceLandmarks=false&returnFaceAttributes=age,gender,headPose,smile,facialHair,glasses,emotion,hair,makeup,occlusion,accessories,blur,exposure,noise" -H "Content-Type: application/json" --data-ascii "{\"url\":\"https://upload.wikimedia.org/wikipedia/commons/c/c3/RH_Louise_Lillian_Gish.jpg\"}"
```

### <a name="face---detect-response"></a>顔検出応答

成功応答が JSON で返されます。

```json
[
  {
    "faceId": "49d55c17-e018-4a42-ba7b-8cbbdfae7c6f",
    "faceRectangle": {
      "top": 131,
      "left": 177,
      "width": 162,
      "height": 162
    },
    "faceAttributes": {
      "smile": 0,
      "headPose": {
        "pitch": 0,
        "roll": 0.1,
        "yaw": -32.9
      },
      "gender": "female",
      "age": 22.9,
      "facialHair": {
        "moustache": 0,
        "beard": 0,
        "sideburns": 0
      },
      "glasses": "NoGlasses",
      "emotion": {
        "anger": 0,
        "contempt": 0,
        "disgust": 0,
        "fear": 0,
        "happiness": 0,
        "neutral": 0.986,
        "sadness": 0.009,
        "surprise": 0.005
      },
      "blur": {
        "blurLevel": "low",
        "value": 0.06
      },
      "exposure": {
        "exposureLevel": "goodExposure",
        "value": 0.67
      },
      "noise": {
        "noiseLevel": "low",
        "value": 0
      },
      "makeup": {
        "eyeMakeup": true,
        "lipMakeup": true
      },
      "accessories": [],
      "occlusion": {
        "foreheadOccluded": false,
        "eyeOccluded": false,
        "mouthOccluded": false
      },
      "hair": {
        "bald": 0,
        "invisible": false,
        "hairColor": [
          {
            "color": "brown",
            "confidence": 1
          },
          {
            "color": "black",
            "confidence": 0.87
          },
          {
            "color": "other",
            "confidence": 0.51
          },
          {
            "color": "blond",
            "confidence": 0.08
          },
          {
            "color": "red",
            "confidence": 0.08
          },
          {
            "color": "gray",
            "confidence": 0.02
          }
        ]
      }
    }
  }
]
```

## <a name="next-steps"></a>次の手順

画像の中から人の顔を検出し、顔の境界を四角形で囲んで、属性 (年齢、性別など) を返す Face API について考察します。

> [!div class="nextstepaction"]
> [Face API](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236)
