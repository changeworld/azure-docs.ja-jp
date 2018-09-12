---
title: 'Computer Vision API PHP クイック スタート: OCR | Microsoft Docs'
titleSuffix: Microsoft Cognitive Services
description: このクイック スタートでは、Cognitive Services の Computer Vision と PHP を使って、画像内の印刷されたテキストを抽出します。
services: cognitive-services
author: noellelacharite
manager: nolachar
ms.service: cognitive-services
ms.component: computer-vision
ms.topic: quickstart
ms.date: 08/28/2018
ms.author: v-deken
ms.openlocfilehash: 85df021357c76330be21ceff77d79491edcc23b0
ms.sourcegitcommit: 0c64460a345c89a6b579b1d7e273435a5ab4157a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/31/2018
ms.locfileid: "43771981"
---
# <a name="quickstart-extract-printed-text-ocr---rest-php"></a>クイック スタート: 印刷されたテキストの抽出 (OCR) - REST、PHP

このクイック スタートでは、Computer Vision を使って、画像内の印刷されたテキストを抽出します。これを光学式文字認識 (OCR) と呼ぶこともあります。

## <a name="prerequisites"></a>前提条件

Computer Vision を使用するにはサブスクリプション キーが必要です。「[サブスクリプション キーを取得する](../Vision-API-How-to-Topics/HowToSubscribe.md)」をご覧ください。

## <a name="ocr-request"></a>OCR 要求

[OCR メソッド](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fc)を使って画像内の印刷されたテキストを検出し、認識した文字をマシンで扱うことができる文字ストリームに抽出します。

このサンプルを実行するには、次の手順を実行します。

1. エディターに次のコードをコピーします。
1. `<Subscription Key>` を、有効なサブスクリプション キーに置き換えます。
1. 必要に応じて、サブスクリプション キーの取得場所を使用するように `uriBase` を変更します。
1. 必要に応じて `imageUrl` を、分析する画像に設定します。
1. `.php` という拡張子でファイルを保存します。
1. PHP をサポートするブラウザー ウィンドウでファイルを開きます。

このサンプルでは、PHP5 [HTTP_Request2](http://pear.php.net/package/HTTP_Request2) パッケージを使用します。

```php
<?php
<html>
<head>
    <title>OCR Sample</title>
</head>
<body>
<?php
// Replace <Subscription Key> with a valid subscription key.
$ocpApimSubscriptionKey = '<Subscription Key>';

// You must use the same location in your REST call as you used to obtain
// your subscription keys. For example, if you obtained your subscription keys
// from westus, replace "westcentralus" in the URL below with "westus".
$uriBase = 'https://westcentralus.api.cognitive.microsoft.com/vision/v2.0/';

$imageUrl = 'https://upload.wikimedia.org/wikipedia/commons/thumb/a/af/' .
    'Atomist_quote_from_Democritus.png/338px-Atomist_quote_from_Democritus.png';

require_once 'HTTP/Request2.php';

$request = new Http_Request2($uriBase . 'ocr');
$url = $request->getUrl();

$headers = array(
    // Request headers
    'Content-Type' => 'application/json',
    'Ocp-Apim-Subscription-Key' => $ocpApimSubscriptionKey
);
$request->setHeader($headers);

$parameters = array(
    // Request parameters
    'language' => 'unk',
    'detectOrientation' => 'true'
);
$url->setQueryVariables($parameters);

$request->setMethod(HTTP_Request2::METHOD_POST);

// Request body parameters
$body = json_encode(array('url' => $imageUrl));

// Request body
$request->setBody($body);

try
{
    $response = $request->send();
    echo "<pre>" .
        json_encode(json_decode($response->getBody()), JSON_PRETTY_PRINT) . "</pre>";
}
catch (HttpException $ex)
{
    echo "<pre>" . $ex . "</pre>";
}
?>
</body>
</html>
```

## <a name="ocr-response"></a>OCR 応答

成功時に返される OCR の結果には、テキストのほか、領域、線、単語を囲む境界ボックスが含まれます。その例を次に示します。

```json
{
    "language": "en",
    "orientation": "Up",
    "textAngle": 0,
    "regions": [
        {
            "boundingBox": "21,16,304,451",
            "lines": [
                {
                    "boundingBox": "28,16,288,41",
                    "words": [
                        {
                            "boundingBox": "28,16,288,41",
                            "text": "NOTHING"
                        }
                    ]
                },
                {
                    "boundingBox": "27,66,283,52",
                    "words": [
                        {
                            "boundingBox": "27,66,283,52",
                            "text": "EXISTS"
                        }
                    ]
                },
                {
                    "boundingBox": "27,128,292,49",
                    "words": [
                        {
                            "boundingBox": "27,128,292,49",
                            "text": "EXCEPT"
                        }
                    ]
                },
                {
                    "boundingBox": "24,188,292,54",
                    "words": [
                        {
                            "boundingBox": "24,188,292,54",
                            "text": "ATOMS"
                        }
                    ]
                },
                {
                    "boundingBox": "22,253,297,32",
                    "words": [
                        {
                            "boundingBox": "22,253,105,32",
                            "text": "AND"
                        },
                        {
                            "boundingBox": "144,253,175,32",
                            "text": "EMPTY"
                        }
                    ]
                },
                {
                    "boundingBox": "21,298,304,60",
                    "words": [
                        {
                            "boundingBox": "21,298,304,60",
                            "text": "SPACE."
                        }
                    ]
                },
                {
                    "boundingBox": "26,387,294,37",
                    "words": [
                        {
                            "boundingBox": "26,387,210,37",
                            "text": "Everything"
                        },
                        {
                            "boundingBox": "249,389,71,27",
                            "text": "else"
                        }
                    ]
                },
                {
                    "boundingBox": "127,431,198,36",
                    "words": [
                        {
                            "boundingBox": "127,431,31,29",
                            "text": "is"
                        },
                        {
                            "boundingBox": "172,431,153,36",
                            "text": "opinion."
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
