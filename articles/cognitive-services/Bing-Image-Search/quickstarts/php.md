---
title: 'クイック スタート: PHP で Bing Image Search API 用の REST API を使用して検索クエリを送信する'
description: このクイック スタートでは、PHP を使用して検索クエリを Bing Search API に送信し、関連するイメージの一覧を取得します。
services: cognitive-services
documentationcenter: ''
author: v-jerkin
ms.service: cognitive-services
ms.component: bing-image-search
ms.topic: article
ms.date: 9/21/2017
ms.author: v-jerkin
ms.openlocfilehash: d91021c4bd5e0f78e518811f3794055b397c1a39
ms.sourcegitcommit: a2ae233e20e670e2f9e6b75e83253bd301f5067c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/13/2018
ms.locfileid: "41929753"
---
# <a name="quickstart-send-search-queries-using-the-rest-api-and-php"></a>クイック スタート: REST API および PHP を使用した検索クエリの送信

Bing Image Search API は、Bing.com/Images と同様、ユーザーの検索クエリを Bing に送信して関連性の高い一連の画像を取得する機能を備えています。

この記事には、Bing Image Search API クエリを実行し、返された生の検索結果を表示する簡単なコンソール アプリケーションが含まれています。検索結果は、JSON 形式です。 このアプリケーションは PHP で記述されていますが、この API は HTTP 要求の発行と JSON の解析が可能な任意のプログラミング言語と互換性がある RESTful Web サービスです。 

## <a name="prerequisites"></a>前提条件

このコードを実行するには、[PHP 5.6.x](http://php.net/downloads.php) が必要です。

[!INCLUDE [cognitive-services-bing-image-search-signup-requirements](../../../../includes/cognitive-services-bing-image-search-signup-requirements.md)]

## <a name="running-the-application"></a>アプリケーションの実行

このアプリケーションを実行するには、次の手順に従います。

1. コードのコメントで説明されているように、`php.ini` でセキュリティ保護された HTTP のサポートが有効になっていることを確認します。 Windows では、このファイルは `C:\windows` にあります。
2. 適当な IDE またはエディターで新しい PHP プロジェクトを作成します。
3. 示されているコードを追加します。
4. `accessKey` の値を、お使いのサブスクリプションで有効なアクセス キーに置き換えます。
5. プログラムを実行します。

```php
<?php

// NOTE: Be sure to uncomment the following line in your php.ini file.
// ;extension=php_openssl.dll

// **********************************************
// *** Update or verify the following values. ***
// **********************************************

// Replace the accessKey string value with your valid access key.
$accessKey = 'enter key here';

// Verify the endpoint URI.  At this writing, only one endpoint is used for Bing
// search APIs.  In the future, regional endpoints may be available.  If you
// encounter unexpected authorization errors, double-check this value against
// the endpoint for your Bing Search instance in your Azure dashboard.
$endpoint = 'https://api.cognitive.microsoft.com/bing/v7.0/images/search';

$term = 'puppies';

function BingImageSearch ($url, $key, $query) {
    // Prepare HTTP request
    // NOTE: Use the key 'http' even if you are making an HTTPS request. See:
    // http://php.net/manual/en/function.stream-context-create.php
    $headers = "Ocp-Apim-Subscription-Key: $key\r\n";
    $options = array ( 'http' => array (
                           'header' => $headers,
                           'method' => 'GET' ));

    // Perform the Web request and get the JSON response
    $context = stream_context_create($options);
    $result = file_get_contents($url . "?q=" . urlencode($query), false, $context);

    // Extract Bing HTTP headers
    $headers = array();
    foreach ($http_response_header as $k => $v) {
        $h = explode(":", $v, 2);
        if (isset($h[1]))
            if (preg_match("/^BingAPIs-/", $h[0]) || preg_match("/^X-MSEdge-/", $h[0]))
                $headers[trim($h[0])] = trim($h[1]);
    }

    return array($headers, $result);
}

if (strlen($accessKey) == 32) {

    print "Searching images for: " . $term . "\n";
    
    list($headers, $json) = BingImageSearch($endpoint, $accessKey, $term);
    
    print "\nRelevant Headers:\n\n";
    foreach ($headers as $k => $v) {
        print $k . ": " . $v . "\n";
    }
    
    print "\nJSON Response:\n\n";
    echo json_encode(json_decode($json), JSON_PRETTY_PRINT);

} else {

    print("Invalid Bing Search API subscription key!\n");
    print("Please paste yours into the source code.\n");

}
?>
```

## <a name="json-response"></a>JSON 応答

応答のサンプルは次のとおりです。 JSON の長さを制限するため、1 つの結果のみが表示されており、応答の他の部分は切り捨てられています。 

```json
{
  "_type": "Images",
  "instrumentation": {},
  "readLink": "https://api.cognitive.microsoft.com/api/v7/images/search?q=puppies",
  "webSearchUrl": "https://www.bing.com/images/search?q=puppies&FORM=OIIARP",
  "totalEstimatedMatches": 955,
  "nextOffset": 1,
  "value": [
    {
      "webSearchUrl": "https://www.bing.com/images/search?view=detailv...",
      "name": "So cute - Puppies Wallpaper",
      "thumbnailUrl": "https://tse3.mm.bing.net/th?id=OIP.jHrihoDNkXGS1t...",
      "datePublished": "2014-02-01T21:55:00.0000000Z",
      "contentUrl": "http://images4.contoso.com/image/photos/14700000/So-cute-puppies...",
      "hostPageUrl": "http://www.contoso.com/clubs/puppies/images/14749028/...",
      "contentSize": "394455 B",
      "encodingFormat": "jpeg",
      "hostPageDisplayUrl": "www.contoso.com/clubs/puppies/images/14749...",
      "width": 1600,
      "height": 1200,
      "thumbnail": {
        "width": 300,
        "height": 225
      },
      "imageInsightsToken": "ccid_jHrihoDN*mid_F68CC526226E163FD1EA659747AD...",
      "insightsMetadata": {
        "recipeSourcesCount": 0
      },
      "imageId": "F68CC526226E163FD1EA659747ADCB8F9FA36",
      "accentColor": "8D613E"
    }
  ],
  "queryExpansions": [
    {
      "text": "Shih Tzu Puppies",
      "displayText": "Shih Tzu",
      "webSearchUrl": "https://www.bing.com/images/search?q=Shih+Tzu+Puppies...",
      "searchLink": "https://api.cognitive.microsoft.com/api/v7/images/search?q=Shih...",
      "thumbnail": {
        "thumbnailUrl": "https://tse2.mm.bing.net/th?q=Shih+Tzu+Puppies&pid=Api..."
      }
    }
  ],
  "pivotSuggestions": [
    {
      "pivot": "puppies",
      "suggestions": [
        {
          "text": "Dog",
          "displayText": "Dog",
          "webSearchUrl": "https://www.bing.com/images/search?q=Dog&tq=%7b%22pq%...",
          "searchLink": "https://api.cognitive.microsoft.com/api/v7/images/search?q=Dog...",
          "thumbnail": {
            "thumbnailUrl": "https://tse1.mm.bing.net/th?q=Dog&pid=Api&mkt=en-US..."
          }
        }
      ]
    }
  ],
  "similarTerms": [
    {
      "text": "cute",
      "displayText": "cute",
      "webSearchUrl": "https://www.bing.com/images/search?q=cute&FORM=...",
      "thumbnail": {
        "url": "https://tse2.mm.bing.net/th?q=cute&pid=Api&mkt=en-US..."
      }
    }
  ],
  "relatedSearches": [
    {
      "text": "Cute Puppies",
      "displayText": "Cute Puppies",
      "webSearchUrl": "https://www.bing.com/images/search?q=Cute+Puppies",
      "searchLink": "https://api.cognitive.microsoft.com/api/v7/images/sear...",
      "thumbnail": {
        "thumbnailUrl": "https://tse4.mm.bing.net/th?q=Cute+Puppies&pid=..."
      }
    }
  ]
}
```

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [Bing Image Search の単一ページ アプリのチュートリアル](../tutorial-bing-image-search-single-page-app.md)

## <a name="see-also"></a>関連項目 

[Bing Image Search の概要](../overview.md)  
[試してみる](https://azure.microsoft.com/services/cognitive-services/bing-image-search-api/)  
[無料試用版のアクセス キーを入手する](https://azure.microsoft.com/try/cognitive-services/?api=bing-image-search-api)  
[Bing Image Search API リファレンス](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference)
