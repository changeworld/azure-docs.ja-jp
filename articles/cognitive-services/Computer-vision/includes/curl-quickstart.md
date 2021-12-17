---
title: 'クイックスタート: 光学式文字認識 REST API'
titleSuffix: Azure Cognitive Services
description: このクイックスタートでは、光学式文字認識 REST API の基本的な使い方について説明します。
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: include
ms.date: 04/19/2021
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: ceeb1804c9332d9e0d3e11336ff92e8aacc8516c
ms.sourcegitcommit: c27f71f890ecba96b42d58604c556505897a34f3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/05/2021
ms.locfileid: "129585269"
---
光学式文字認識 REST API を使用して、印刷されたテキストや手書きのテキストを読み取ります。

> [!NOTE]
> このクイックスタートでは、cURL コマンドを使用して REST API を呼び出します。 また、プログラミング言語を使用して REST API を呼び出すこともできます。 GitHub のサンプルを参照して、<bpt id="p1">[</bpt>C#<ept id="p1">](https://github.com/Azure-Samples/cognitive-services-quickstart-code/tree/master/dotnet/ComputerVision/REST)</ept>、<bpt id="p2">[</bpt>Python<ept id="p2">](https://github.com/Azure-Samples/cognitive-services-quickstart-code/tree/master/python/ComputerVision/REST)</ept>、<bpt id="p3">[</bpt>Java<ept id="p3">](https://github.com/Azure-Samples/cognitive-services-quickstart-code/tree/master/java/ComputerVision/REST)</ept>、<bpt id="p4">[</bpt>JavaScript<ept id="p4">](https://github.com/Azure-Samples/cognitive-services-quickstart-code/tree/master/javascript/ComputerVision/REST)</ept>、<bpt id="p5">[</bpt>Go<ept id="p5">](https://github.com/Azure-Samples/cognitive-services-quickstart-code/tree/master/go/ComputerVision/REST)</ept> の例をご確認ください。

## <a name="prerequisites"></a>前提条件

* Azure サブスクリプション - <bpt id="p1">[</bpt>無料アカウントを作成します<ept id="p1">](https://azure.microsoft.com/free/cognitive-services/)</ept> 
* Azure サブスクリプションを入手したら、Azure portal で <ph id="ph1"><a href="https://portal.azure.com/#create/Microsoft.CognitiveServicesComputerVision"  title="</ph>Computer Vision リソースを作成<ph id="ph2">"  target="_blank"></ph>Computer Vision リソースを作成<ph id="ph3"></a></ph>し、キーとエンドポイントを取得します。 デプロイされたら、 <bpt id="p1">**</bpt>[リソースに移動]<ept id="p1">**</ept> をクリックします。
  * 対象のアプリケーションを Computer Vision サービスに接続するには、作成したリソースのキーとエンドポイントが必要です。 このクイックスタートで後に示すコードに、自分のキーとエンドポイントを貼り付けます。
  * Free 価格レベル (<ph id="ph1">`F0`</ph>) を使用してサービスを試用し、後から運用環境用の有料レベルにアップグレードすることができます。
* インストールされた <bpt id="p1">[</bpt>cURL<ept id="p1">](https://curl.haxx.se/)</ept>


## <a name="extract-printed-and-handwritten-text"></a>印刷されたテキストと手書きテキストの抽出

OCR サービスを使用すると、画像またはドキュメント内の可視テキストを抽出し、文字ストリームに変換することができます。 テキスト抽出の詳細については、[光学式文字認識 (OCR)](../overview-ocr.md) の概要を参照してください。


### <a name="call-the-read-api"></a>Read API を呼び出す

このサンプルを作成して実行するには、次の手順を実行します。

1. テキスト エディターに次のコマンドをコピーします。
1. 必要に応じて、コマンドに次の変更を加えます。
    1. <ph id="ph1">`<subscriptionKey>`</ph> 値を、サブスクリプション キーに置き換えます。
    1. 要求 URL (<ph id="ph1">`westcentralus`</ph>) の最初の部分を独自のエンドポイント URL 内のテキストに置き換えます。
        [!INCLUDE [Custom subdomains notice](../../../../includes/cognitive-services-custom-subdomains-note.md)]
    1. 必要に応じて、要求本文の画像 URL (<ph id="ph1">`https://upload.wikimedia.org/wikipedia/commons/thumb/a/af/Atomist_quote_from_Democritus.png/338px-Atomist_quote_from_Democritus.png\`</ph>) を、分析する別の画像の URL に変更します。
1. コマンド プロンプト ウィンドウを開きます。
1. テキスト エディターからコマンド プロンプト ウィンドウにコマンドを貼り付けて、コマンドを実行します。

```bash
curl -v -X POST "https://westcentralus.api.cognitive.microsoft.com/vision/v3.2/read/analyze" -H "Content-Type: application/json" -H "Ocp-Apim-Subscription-Key: <subscription key>" --data-ascii "{\"url\":\"https://upload.wikimedia.org/wikipedia/commons/thumb/a/af/Atomist_quote_from_Democritus.png/338px-Atomist_quote_from_Democritus.png\"}"
```

応答には <ph id="ph1">`Operation-Location`</ph> ヘッダーが含まれ、その値は一意の URL になります。 この URL を使用して、読み取り操作の結果に対してクエリを実行します。 URL の有効期間は 48 時間です。

### <a name="how-to-use-preview-features"></a>プレビュー機能の使用方法
プレビューの言語と機能については、最新のプレビューを使用するための[モデル バージョンの指定方法](../Vision-API-How-to-Topics/call-read-api.md#determine-how-to-process-the-data-optional)に関するページを参照してください。 プレビュー モデルには、現在の GA の言語と機能に対する拡張機能が含まれています。

### <a name="get-read-results"></a>読み取りの結果を取得する

1. テキスト エディターに次のコマンドをコピーします。
1. URL を前の手順でコピーした <ph id="ph1">`Operation-Location`</ph> 値に置き換えます。
1. 必要に応じて、コマンドに次の変更を加えます。
    1. <ph id="ph1">`<subscriptionKey>`</ph> 値を、サブスクリプション キーに置き換えます。
1. コマンド プロンプト ウィンドウを開きます。
1. テキスト エディターからコマンド プロンプト ウィンドウにコマンドを貼り付けて、コマンドを実行します。

```bash
curl -v -X GET "https://westcentralus.api.cognitive.microsoft.com/vision/v3.2/read/analyzeResults/{operationId}" -H "Ocp-Apim-Subscription-Key: {subscription key}" --data-ascii "{body}" 
```

### <a name="examine-the-response"></a>結果の確認

成功応答が JSON で返されます。 サンプル アプリケーションによって成功応答が解析され、次の例のようにコマンド プロンプト ウィンドウに表示されます。

```json
{
  "status": "succeeded",
  "createdDateTime": "2021-04-08T21:56:17.6819115+00:00",
  "lastUpdatedDateTime": "2021-04-08T21:56:18.4161316+00:00",
  "analyzeResult": {
    "version": "3.2",
    "readResults": [
      {
        "page": 1,
        "angle": 0,
        "width": 338,
        "height": 479,
        "unit": "pixel",
        "lines": [
          {
            "boundingBox": [
              25,
              14,
              318,
              14,
              318,
              59,
              25,
              59
            ],
            "text": "NOTHING",
            "appearance": {
              "style": {
                "name": "other",
                "confidence": 0.971
              }
            },
            "words": [
              {
                "boundingBox": [
                  27,
                  15,
                  294,
                  15,
                  294,
                  60,
                  27,
                  60
                ],
                "text": "NOTHING",
                "confidence": 0.994
              }
            ]
          }
        ]
      }
    ]
  }
}

```



## <a name="next-steps"></a>次のステップ

このクイックスタートでは、Read REST API を呼び出す方法について説明しました。 次は、Read API の機能の詳細について学習しましょう。

> [!div class="nextstepaction"]
><bpt id="p1">[</bpt>Read API を呼び出す<ept id="p1">](../Vision-API-How-to-Topics/call-read-api.md)</ept>

* <bpt id="p1">[</bpt>OCR の概要<ept id="p1">](../overview-ocr.md)</ept>
