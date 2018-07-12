---
title: Image Search SDK Python クイック スタート | Microsoft Docs
description: Image Search SDK コンソール アプリケーションの設定。
titleSuffix: Azure Image Search SDK Python quickstart
services: cognitive-services
author: mikedodaro
manager: rosh
ms.service: cognitive-services
ms.component: bing-image-search
ms.topic: article
ms.date: 02/14/2018
ms.author: v-gedod
ms.openlocfilehash: e30852439ad8ec2d5ddc667b75167e8b5d35be33
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/23/2018
ms.locfileid: "35377893"
---
# <a name="image-search-sdk-python-quickstart"></a>Image Search SDK Python のクイック スタート

Bing Image Search SDK には、Web クエリと結果解析のための REST API 機能が含まれています。 

[Python 向けの Image Search SDK のサンプル ソース コード](https://github.com/Azure-Samples/cognitive-services-python-sdk-samples/blob/master/samples/search/image_search_samples.py)は、GitHub で公開しています。

## <a name="application-dependencies"></a>アプリケーションの依存関係
Python をまだインストールしていない場合は、インストールします。 この SDK は、Python 2.7、3.3、3.4、3.5、3.6 と互換性があります。

Python 開発では、一般的に、[仮想環境](https://docs.python.org/3/tutorial/venv.html)を使用することをお勧めします。 [venv モジュール](https://pypi.python.org/pypi/virtualenv)を使用して仮想環境をインストールして初期化します。 Python 2.7 向けの virtualenv をインストールする必要があります。
```
python -m venv mytestenv
```
Bing Image Search SDK 依存関係をインストールします。
```
cd mytestenv
python -m pip install azure-cognitiveservices-search-imagesearch
```
## <a name="image-search-client"></a>Image Search クライアント
*[検索]* で [Cognitive Services のアクセス キー](https://azure.microsoft.com/try/cognitive-services/)を取得します。 インポートを追加します。
```
from azure.cognitiveservices.search.imagesearch import ImageSearchAPI
from azure.cognitiveservices.search.imagesearch.models import ImageType, ImageAspect, ImageInsightModule
from msrest.authentication import CognitiveServicesCredentials

subscription_key = "YOUR-SUBSCRIPTION-KEY"
```
`CognitiveServicesCredentials` のインスタンスを作成し、クライアントをインスタンス化します。
```
client = ImageSearchAPI(CognitiveServicesCredentials(subscription_key))
```
クエリ (Yosemite) で画像を検索します。アニメーション Gif と広アスペクトで絞り込みます。 結果数を確認し、最初の結果の分析情報トークン、サムネイル URL、Web URL を出力します。
```
image_results = client.images.search(
        query="Yosemite",
        image_type=ImageType.animated_gif, # Could be the str "AnimatedGif"
        aspect=ImageAspect.wide # Could be the str "Wide"
    )
    print("\r\nSearch images for \"Yosemite\" results that are animated gifs and wide aspect")

    if image_results.value:
        first_image_result = image_results.value[0]
        print("Image result count: {}".format(len(image_results.value)))
        print("First image insights token: {}".format(first_image_result.image_insights_token))
        print("First image thumbnail url: {}".format(first_image_result.thumbnail_url))
        print("First image web search url: {}".format(first_image_result.web_search_url))
    else:
        print("Couldn't find image results!")

```
クエリ (Yosemite) で画像を検索し、アニメーション Gif と広アスペクトで絞り込みます。  結果数を確認します。  最初の結果の `insightsToken`、`thumbnail url`、`web url` を出力します。
```
image_results = client.images.search(
    query="Yosemite",
    image_type=ImageType.animated_gif, # Could be the str "AnimatedGif"
    aspect=ImageAspect.wide # Could be the str "Wide"
)
print("\r\nSearch images for \"Yosemite\" results that are animated gifs and wide aspect")

if image_results.value:
    first_image_result = image_results.value[0]
    print("Image result count: {}".format(len(image_results.value)))
    print("First image insights token: {}".format(first_image_result.image_insights_token))
    print("First image thumbnail url: {}".format(first_image_result.thumbnail_url))
    print("First image web search url: {}".format(first_image_result.web_search_url))
else:
    print("Couldn't find image results!")

```

トレンドの結果を取得します。
```
trending_result = client.images.trending()
print("\r\nSearch trending images")

# Categorires
if trending_result.categories:
    first_category = trending_result.categories[0]
    print("Category count: {}".format(len(trending_result.categories)))
    print("First category title: {}".format(first_category.title))
    if first_category.tiles:
        first_tile = first_category.tiles[0]
        print("Subcategory tile count: {}".format(len(first_category.tiles)))
        print("First tile text: {}".format(first_tile.query.text))
        print("First tile url: {}".format(first_tile.query.web_search_url))
    else:
        print("Couldn't find subcategory tiles!")
    else:
        print("Couldn't find categories!")

```

## <a name="next-steps"></a>次の手順

[Cognitive Services の Python 向け SDK のサンプル](https://github.com/Azure-Samples/cognitive-services-python-sdk-samples)


