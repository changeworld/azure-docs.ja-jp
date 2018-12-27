---
title: クイック スタート:イメージを検索する - Bing Image Search REST API と Python
titleSuffix: Azure Cognitive Services
description: このクイック スタートを使用して、Python を使って Bing Image Search REST API にイメージ検索要求を送信し、JSON 応答を受信します。
services: cognitive-services
author: aahill
manager: cgronlun
ms.service: cognitive-services
ms.component: bing-image-search
ms.topic: quickstart
ms.date: 8/20/2018
ms.author: aahi
ms.custom: seodec2018
ms.openlocfilehash: a93a044279cccd883de5f946bb236cad4b088ae2
ms.sourcegitcommit: 1c1f258c6f32d6280677f899c4bb90b73eac3f2e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/11/2018
ms.locfileid: "53261979"
---
# <a name="quickstart-search-for-images-using-the-bing-image-search-rest-api-and-python"></a>クイック スタート:Bing Image Search REST API と Python を使用してイメージを検索する

このクイック スタートを使用すると、Bing Image Search API への最初の呼び出しを行い、JSON 応答を受け取ることができます。 このシンプルな Python アプリケーションは、検索クエリを API に送信し、生の結果を表示します。

このアプリケーションは Python で記述されていますが、API はほとんどのプログラミング言語と互換性のある RESTful Web サービスです。

この例は、起動 Binder バッジ上でクリックすることで、[MyBinder](https://mybinder.org) の Jupyter Notebook として実行できます。

[![Binder](https://mybinder.org/badge.svg)](https://mybinder.org/v2/gh/Microsoft/cognitive-services-notebooks/master?filepath=BingImageSearchAPI.ipynb)


また、このサンプルのソース コードは、[GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/python/Search/BingImageSearchv7.py) で入手できます。

## <a name="prerequisites"></a>前提条件

[!INCLUDE [cognitive-services-bing-image-search-signup-requirements](../../../../includes/cognitive-services-bing-image-search-signup-requirements.md)]

「[Cognitive Services の価格 - Bing Search API](https://azure.microsoft.com/pricing/details/cognitive-services/search-api/)」もご覧ください。

## <a name="running-the-quickstart"></a>クイックスタートの実行

まず、`subscription_key` を Bing API サービスの有効なサブスクリプション キーに設定します。

```python
subscription_key = None
assert subscription_key
```

次に、`search_url` エンドポイントが正しいことを確認します。 このドキュメントの作成時点では、Bing Search APIs にはエンドポイントは 1 つだけ使用されます。 承認エラーが発生した場合は、Azure ダッシュボードの Bing Search エンドポイントに対するこの値を再確認してください。


```python
search_url = "https://api.cognitive.microsoft.com/bing/v7.0/images/search"
```

`search_term` に、子犬の画像を検索するための値 (puppies) を設定します。


```python
search_term = "puppies"
```

次のブロックでは、Python 内の `requests` ライブラリを使用して、Bing Search API を呼び出し、JSON オブジェクトとして結果を返します。 `headers` を介して API キーを渡し、`params` ディクショナリを介して検索用語を渡していることに注意してください。 検索結果をフィルター処理するために使用できる全オプションの一覧を確認するには、「[REST API](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference)」ドキュメントをご覧ください。


```python
import requests

headers = {"Ocp-Apim-Subscription-Key" : subscription_key}
params  = {"q": search_term, "license": "public", "imageType": "photo"}
response = requests.get(search_url, headers=headers, params=params)
response.raise_for_status()
search_results = response.json()
```

`search_results` オブジェクトには、実際の画像が、関連項目などの豊富なメタデータと共に含まれています。 たとえば、次のコード行では、最初の 16 の結果のサムネイルの URL を抽出できます。


```python
thumbnail_urls = [img["thumbnailUrl"] for img in search_results["value"][:16]]
```

次に、`PIL` ライブラリを使用してサムネイル画像をダウンロードし、`matplotlib` ライブラリを使用してそれらの画像を 4 列 x 4 行のグリッドに出力します。


```python
%matplotlib inline
import matplotlib.pyplot as plt
from PIL import Image
from io import BytesIO

f, axes = plt.subplots(4, 4)
for i in range(4):
    for j in range(4):
        image_data = requests.get(thumbnail_urls[i+4*j])
        image_data.raise_for_status()
        image = Image.open(BytesIO(image_data.content))        
        axes[i][j].imshow(image)
        axes[i][j].axis("off")
plt.show()
```

## <a name="sample-json-response"></a>サンプルの JSON 応答

Bing Image Search API からの応答は、JSON として返されます。 このサンプル応答は、1 つの結果だけを表示するように切り詰められています。

```json
{
"_type":"Images",
"instrumentation":{
    "_type":"ResponseInstrumentation"
},
"readLink":"images\/search?q=tropical ocean",
"webSearchUrl":"https:\/\/www.bing.com\/images\/search?q=tropical ocean&FORM=OIIARP",
"totalEstimatedMatches":842,
"nextOffset":47,
"value":[
    {
        "webSearchUrl":"https:\/\/www.bing.com\/images\/search?view=detailv2&FORM=OIIRPO&q=tropical+ocean&id=8607ACDACB243BDEA7E1EF78127DA931E680E3A5&simid=608027248313960152",
        "name":"My Life in the Ocean | The greatest WordPress.com site in ...",
        "thumbnailUrl":"https:\/\/tse3.mm.bing.net\/th?id=OIP.fmwSKKmKpmZtJiBDps1kLAHaEo&pid=Api",
        "datePublished":"2017-11-03T08:51:00.0000000Z",
        "contentUrl":"https:\/\/mylifeintheocean.files.wordpress.com\/2012\/11\/tropical-ocean-wallpaper-1920x12003.jpg",
        "hostPageUrl":"https:\/\/mylifeintheocean.wordpress.com\/",
        "contentSize":"897388 B",
        "encodingFormat":"jpeg",
        "hostPageDisplayUrl":"https:\/\/mylifeintheocean.wordpress.com",
        "width":1920,
        "height":1200,
        "thumbnail":{
        "width":474,
        "height":296
        },
        "imageInsightsToken":"ccid_fmwSKKmK*mid_8607ACDACB243BDEA7E1EF78127DA931E680E3A5*simid_608027248313960152*thid_OIP.fmwSKKmKpmZtJiBDps1kLAHaEo",
        "insightsMetadata":{
        "recipeSourcesCount":0,
        "bestRepresentativeQuery":{
            "text":"Tropical Beaches Desktop Wallpaper",
            "displayText":"Tropical Beaches Desktop Wallpaper",
            "webSearchUrl":"https:\/\/www.bing.com\/images\/search?q=Tropical+Beaches+Desktop+Wallpaper&id=8607ACDACB243BDEA7E1EF78127DA931E680E3A5&FORM=IDBQDM"
        },
        "pagesIncludingCount":115,
        "availableSizesCount":44
        },
        "imageId":"8607ACDACB243BDEA7E1EF78127DA931E680E3A5",
        "accentColor":"0050B2"
    }
}
```

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [Bing Image Search の単一ページ アプリのチュートリアル](../tutorial-bing-image-search-single-page-app.md)

## <a name="see-also"></a>関連項目

* [Bing Image Search とは](https://docs.microsoft.com/azure/cognitive-services/bing-image-search/overview)  
* [オンラインの対話型デモを試す](https://azure.microsoft.com/services/cognitive-services/bing-image-search-api/)  
* [無料の Cognitive Services アクセス キーを取得する](https://azure.microsoft.com/try/cognitive-services/?api=bing-image-search-api)  
* [Azure Cognitive Services のドキュメント](https://docs.microsoft.com/azure/cognitive-services)
* [Bing Image Search API リファレンス](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference)
