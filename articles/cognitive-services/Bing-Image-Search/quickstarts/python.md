---
title: 呼び出しと応答 - Azure Cognitive Services、Bing Image Search API のための Python のクイック スタート | Microsoft Docs
description: Azure 上の Microsoft Cognitive Services の Bing Image Search API の使用をすぐに開始するために役立つ情報とコード サンプルを提供します。
services: cognitive-services
author: v-jerkin
ms.service: cognitive-services
ms.component: bing-image-search
ms.topic: article
ms.date: 9/21/2017
ms.author: v-jerkin
ms.openlocfilehash: 3b5d6a961ce4bcde8aaf73f1fbd30689a6c2c2d1
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/23/2018
ms.locfileid: "35374024"
---
# <a name="call-and-response-your-first-bing-image-search-query-in-python"></a>呼び出しと応答: Python での初めての Bing Image Search クエリ

Bing Image Search API は、Bing.com/Images と同様、ユーザーの検索クエリを Bing に送信して関連性の高い一連の画像を取得する機能を備えています。

このチュートリアルでは、Bing Image Search API の呼び出しと、結果として得られる JSON オブジェクトの事後処理を行う簡単な例を示します。 詳細については、[Bing Image Search のドキュメント](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference)を参照してください。

この例は、起動 Binder バッジ上でクリックすることで、[MyBinder](https://mybinder.org) の Jupyter Notebook として実行できます。 

[![Binder](https://mybinder.org/badge.svg)](https://mybinder.org/v2/gh/Microsoft/cognitive-services-notebooks/master?filepath=BingImageSearchAPI.ipynb)

## <a name="prerequisites"></a>前提条件

[Cognitive Services APIs アカウント](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account)と **Bing Search APIs** を取得している必要があります。 このクイック スタートには[無料試用版](https://azure.microsoft.com/try/cognitive-services/?api=bing-web-search-api)で十分です。 無料試用版を起動するとき、アクセス キーを入力する必要があります。または、Azure ダッシュボードの有料サブスクリプション キーを使用できます。

## <a name="running-the-walkthrough"></a>チュートリアルの実行
このチュートリアルを続行するには、`subscription_key` に Bing API サービス用 API キーを設定します。


```python
subscription_key = None
assert subscription_key
```

次に、`search_url` エンドポイントが正しいことを確認します。 このドキュメントの作成時点では、Bing Search API にはエンドポイントは 1 つだけ使用されます。 承認エラーが発生した場合は、Azure ダッシュボードの Bing Search エンドポイントに対するこの値を再確認してください。


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
