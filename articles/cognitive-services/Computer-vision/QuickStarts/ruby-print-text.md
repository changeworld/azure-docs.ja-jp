---
title: 'Computer Vision API Ruby クイック スタート: OCR | Microsoft Docs'
titleSuffix: Microsoft Cognitive Services
description: このクイック スタートでは、Cognitive Services の Computer Vision と Ruby を使って、画像内の印刷されたテキストを抽出します。
services: cognitive-services
author: noellelacharite
manager: nolachar
ms.service: cognitive-services
ms.component: computer-vision
ms.topic: quickstart
ms.date: 08/28/2018
ms.author: v-deken
ms.openlocfilehash: 4f381444401718906bb352860aec525d73da1eb2
ms.sourcegitcommit: 0c64460a345c89a6b579b1d7e273435a5ab4157a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/31/2018
ms.locfileid: "43772401"
---
# <a name="quickstart-extract-printed-text-ocr---rest-ruby"></a>クイック スタート: 印刷されたテキストの抽出 (OCR) - REST、Ruby

このクイック スタートでは、Computer Vision を使って、画像内の印刷されたテキストを抽出します。これを光学式文字認識 (OCR) と呼ぶこともあります。

## <a name="prerequisites"></a>前提条件

Computer Vision を使用するにはサブスクリプション キーが必要です。「[サブスクリプション キーを取得する](../Vision-API-How-to-Topics/HowToSubscribe.md)」をご覧ください。

## <a name="ocr-request"></a>OCR 要求

[OCR メソッド](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fc)を使って画像内の印刷されたテキストを検出し、認識した文字をマシンで扱うことができる文字ストリームに抽出します。

このサンプルを実行するには、次の手順を実行します。

1. エディターに次のコードをコピーします。
1. `<Subscription Key>` を、有効なサブスクリプション キーに置き換えます。
1. 必要に応じて `uri` の値を、サブスクリプション キーを取得した場所に変更します。
1. 必要に応じて、分析対象の画像 (`{\"url\":\"...`) を変更します。
1. `.rb` という拡張子でファイルを保存します。
1. Ruby コマンド プロンプトを開いてファイルを実行します (例: `ruby myfile.rb`)。

```ruby
require 'net/http'

# You must use the same location in your REST call as you used to get your
# subscription keys. For example, if you got your subscription keys from westus,
# replace "westcentralus" in the URL below with "westus".
uri = URI('https://westcentralus.api.cognitive.microsoft.com/vision/v2.0/ocr')
uri.query = URI.encode_www_form({
    # Request parameters
    'language' => 'unk',
    'detectOrientation' => 'true'
})

request = Net::HTTP::Post.new(uri.request_uri)

# Request headers
# Replace <Subscription Key> with your valid subscription key.
request['Ocp-Apim-Subscription-Key'] = '<Subscription Key>'
request['Content-Type'] = 'application/json'

request.body =
    "{\"url\": \"https://upload.wikimedia.org/wikipedia/commons/thumb/a/af/" +
    "Atomist_quote_from_Democritus.png/338px-Atomist_quote_from_Democritus.png\"}"

response = Net::HTTP.start(uri.host, uri.port, :use_ssl => uri.scheme == 'https') do |http|
    http.request(request)
end

puts response.body
```

## <a name="ocr-response"></a>OCR 応答

成功時に返される OCR の結果には、テキストのほか、領域、線、単語を囲む境界ボックスが含まれます。その例を次に示します。

```json
{
  "language": "en",
  "textAngle": -2.0000000000000338,
  "orientation": "Up",
  "regions": [
    {
      "boundingBox": "462,379,497,258",
      "lines": [
        {
          "boundingBox": "462,379,497,74",
          "words": [
            {
              "boundingBox": "462,379,41,73",
              "text": "A"
            },
            {
              "boundingBox": "523,379,153,73",
              "text": "GOAL"
            },
            {
              "boundingBox": "694,379,265,74",
              "text": "WITHOUT"
            }
          ]
        },
        {
          "boundingBox": "565,471,289,74",
          "words": [
            {
              "boundingBox": "565,471,41,73",
              "text": "A"
            },
            {
              "boundingBox": "626,471,150,73",
              "text": "PLAN"
            },
            {
              "boundingBox": "801,472,53,73",
              "text": "IS"
            }
          ]
        },
        {
          "boundingBox": "519,563,375,74",
          "words": [
            {
              "boundingBox": "519,563,149,74",
              "text": "JUST"
            },
            {
              "boundingBox": "683,564,41,72",
              "text": "A"
            },
            {
              "boundingBox": "741,564,153,73",
              "text": "WISH"
            }
          ]
        }
      ]
    }
  ]
}
```

## <a name="next-steps"></a>次の手順

画像の分析、著名人やランドマークの検出、サムネイルの作成、印刷されたテキストや手書きテキストの抽出に使用される Computer Vision API の詳細を確認します。 Computer Vision API を簡単に試す場合は、[Open API テスト コンソール](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa/console)をお試しください。

> [!div class="nextstepaction"]
> [Computer Vision API の詳細](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44)
