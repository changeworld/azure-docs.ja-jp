---
title: 'クイック スタート: Python で Bing Image Search API 用の REST API を使用して検索クエリを送信する'
description: このクイック スタートでは、検索クエリを Bing Search API に送信し、Python を使用して関連するイメージの一覧を取得します。
services: cognitive-services
author: v-jerkin
ms.service: cognitive-services
ms.component: bing-image-search
ms.topic: article
ms.date: 9/21/2017
ms.author: v-jerkin
ms.openlocfilehash: bc527ba39b580935f113f56aa63f7bdd283ba304
ms.sourcegitcommit: a2ae233e20e670e2f9e6b75e83253bd301f5067c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/13/2018
ms.locfileid: "41929872"
---
# <a name="quickstart-send-search-queries-using-the-rest-api-and-python"></a>クイック スタート: REST API および Python を使用した検索クエリの送信

Bing Image Search API は、Bing.com/Images と同様、ユーザーの検索クエリを Bing に送信して関連性の高い一連の画像を取得する機能を備えています。

このチュートリアルでは、Bing Image Search API の呼び出しと、結果として得られる JSON オブジェクトの事後処理を行う簡単な例を示します。 詳細については、[Bing Image Search のドキュメント](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference)を参照してください。

この例は、起動 Binder バッジ上でクリックすることで、[MyBinder](https://mybinder.org) の Jupyter Notebook として実行できます。 

[![Binder](https://mybinder.org/badge.svg)](https://mybinder.org/v2/gh/Microsoft/cognitive-services-notebooks/master?filepath=BingImageSearchAPI.ipynb)

## <a name="prerequisites"></a>前提条件

[!INCLUDE [cognitive-services-bing-image-search-signup-requirements](../../../../includes/cognitive-services-bing-image-search-signup-requirements.md)]

## <a name="running-the-walkthrough"></a>チュートリアルの実行
このチュートリアルを続行するには、`subscription_key` に Bing API サービス用 API キーを設定します。


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

次に、`PIL` ライブラリを使用してサムネイル画像をダウンロードし、`matplotlib` ライブラリを使用してそれらの画像を 4 列 x 4 行のグリッドに出力できます。


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
```

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [Bing Image Search の単一ページ アプリのチュートリアル](../tutorial-bing-image-search-single-page-app.md)

## <a name="see-also"></a>関連項目 

[Bing Image Search の概要](../overview.md)  
[試してみる](https://azure.microsoft.com/services/cognitive-services/bing-image-search-api/)  
[無料試用版のアクセス キーを入手する](https://azure.microsoft.com/try/cognitive-services/?api=bing-image-search-api)  
[Bing Image Search API リファレンス](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference)
