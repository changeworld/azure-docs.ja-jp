---
title: Bing Image Search Python クライアント ライブラリのクイックスタート
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 03/04/2020
ms.author: aahi
ms.openlocfilehash: c9dd8bdfb9af1d20433a083b48ee1b7e14658438
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/02/2021
ms.locfileid: "131253600"
---
このクイックスタートでは、Bing Image Search クライアント ライブラリを使用して最初の画像検索を行います。このクライアント ライブラリは、API のラッパーであり、同じ機能を含んでいます。 このシンプルな Python アプリケーションは、画像検索クエリを送信し、JSON 応答を解析して、返された最初の画像の URL を表示します。

このサンプルのソース コードは、追加のエラー処理と注釈を含め、[GitHub](https://github.com/Azure-Samples/cognitive-services-python-sdk-samples/blob/master/samples/search/image-search-quickstart.py) で入手できます。

## <a name="prerequisites"></a>前提条件

* [Python 2.7 または 3.6 以降](https://www.python.org/)。

* Python 用 [Azure Image Search クライアント ライブラリ](https://pypi.org/project/azure-cognitiveservices-search-imagesearch/)
    * `pip install azure-cognitiveservices-search-imagesearch` を使用してインストールする

[!INCLUDE [cognitive-services-bing-image-search-signup-requirements](~/includes/cognitive-services-bing-image-search-signup-requirements.md)]

## <a name="create-and-initialize-the-application"></a>アプリケーションを作成して初期化する

1. お気に入りの IDE またはエディターで新しい Python スクリプトを作成し、次のインポートを行います。

    ```python
    from azure.cognitiveservices.search.imagesearch import ImageSearchClient
    from msrest.authentication import CognitiveServicesCredentials
    ```

2. サブスクリプション キーと検索用語の変数を作成します。

    ```python
    subscription_key = "Enter your key here"
    subscription_endpoint = "Enter your endpoint here"
    search_term = "canadian rockies"
    ```

## <a name="create-the-image-search-client"></a>画像検索クライアントを作成する

1. `CognitiveServicesCredentials` のインスタンスを作成し、それを使用してクライアントをインスタンス化します。

    ```python
    client = ImageSearchClient(endpoint=subscription_endpoint, credentials=CognitiveServicesCredentials(subscription_key))
    ```

1. Bing Image Search API に検索クエリを送信します。

    ```python
    image_results = client.images.search(query=search_term)
    ```

## <a name="process-and-view-the-results"></a>結果の処理と表示

応答で返された画像の結果を解析します。

応答に検索結果が含まれている場合は、最初の結果を格納して、返された URL の合計数と共にサムネイルの URL などの詳細を出力します。  

```python
if image_results.value:
    first_image_result = image_results.value[0]
    print("Total number of images returned: {}".format(len(image_results.value)))
    print("First image thumbnail url: {}".format(
        first_image_result.thumbnail_url))
    print("First image content url: {}".format(first_image_result.content_url))
else:
    print("No image results returned!")
```

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [Bing Image Search の単一ページ アプリのチュートリアル](../../tutorial-bing-image-search-single-page-app.md)

## <a name="see-also"></a>関連項目

* [Bing Image Search とは](../../overview.md)  
* [オンラインの対話型デモを試す](https://azure.microsoft.com/services/cognitive-services/bing-image-search-api/)  
* [Azure Cognitive Services SDK の Python サンプル](https://github.com/Azure-Samples/cognitive-services-python-sdk-samples)  
* [Azure Cognitive Services のドキュメント](../../../index.yml)
* [Bing Image Search API リファレンス](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference)