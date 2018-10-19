---
title: 'クイック スタート: Bing News Search API (Python)'
titlesuffix: Azure Cognitive Services
description: Bing News Search API をすぐに使い始めるのに役立つ情報とコード サンプルを提供します。
services: cognitive-services
author: v-jerkin
manager: cgronlun
ms.service: cognitive-services
ms.component: bing-news-search
ms.topic: quickstart
ms.date: 9/21/2017
ms.author: v-jerkin
ms.openlocfilehash: 583b304a742d9abfd799442c9aa2999ad6783a34
ms.sourcegitcommit: 9eaf634d59f7369bec5a2e311806d4a149e9f425
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/05/2018
ms.locfileid: "48803547"
---
# <a name="quickstart-for-bing-news-search-api-with-python"></a>Python での Bing News Search API のクイック スタート
このチュートリアルでは、Bing News Search API の呼び出しと、結果として得られる JSON オブジェクトの事後処理を行う簡単な例を示します。 詳細については、[Bing News Search のドキュメント](https://docs.microsoft.com/rest/api/cognitiveservices/bing-web-api-v7-reference)を参照してください。  

この例は、起動 Binder バッジ上でクリックすることで、[MyBinder](https://mybinder.org) の Jupyter Notebook として実行できます。 

[![Binder](https://mybinder.org/badge.svg)](https://mybinder.org/v2/gh/Microsoft/cognitive-services-notebooks/master?filepath=BingNewsSearchAPI.ipynb)

## <a name="prerequisites"></a>前提条件

[Cognitive Services API アカウント](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account)と **Bing Search APIs** を取得している必要があります。 このクイック スタートには[無料試用版](https://azure.microsoft.com/try/cognitive-services/?api=bing-web-search-api)で十分です。 無料試用版を起動するとき、アクセス キーを入力する必要があります。または、Azure ダッシュボードの有料サブスクリプション キーを使用できます。

## <a name="running-the-walkthrough"></a>チュートリアルの実行
まず、`subscription_key` に Bing API サービス用の API キーを設定します。


```python
subscription_key = None
assert subscription_key
```

次に、`search_url` エンドポイントが正しいことを確認します。 このドキュメントの作成時点では、Bing Search APIs にはエンドポイントは 1 つだけ使用されます。 承認エラーが発生した場合は、Azure ダッシュボードの Bing Search エンドポイントに対するこの値を再確認してください。


```python
search_url = "https://api.cognitive.microsoft.com/bing/v7.0/news/search"
```

`search_term` に、Microsoft に関するニュース記事を検索するための値 (Microsoft) を設定します。


```python
search_term = "Microsoft"
```

次のブロックでは、Python 内の `requests` ライブラリを使用して、Bing Search API を呼び出し、JSON オブジェクトとして結果を返します。 `headers` を介して API キーを渡し、`params` ディクショナリを介して検索用語を渡していることに注意してください。 検索結果をフィルター処理するために使用できる全オプションの一覧を確認するには、「[REST API](https://docs.microsoft.com/rest/api/cognitiveservices/bing-news-api-v7-reference)」ドキュメントをご覧ください。


```python
import requests

headers = {"Ocp-Apim-Subscription-Key" : subscription_key}
params  = {"q": search_term, "textDecorations": True, "textFormat": "HTML"}
response = requests.get(search_url, headers=headers, params=params)
response.raise_for_status()
search_results = response.json()
```

`search_results` オブジェクトには、関連するニュース記事と豊富なメタデータが含まれています。 たとえば、次のコード行は、記事の説明を抽出します。


```python
descriptions = [article["description"] for article in search_results["value"]]
```

これらの説明は、**太字**で強調表示される検索キーワードと一緒に表としてテーブルとして出力できます。


```python
from IPython.display import HTML
rows = "\n".join(["<tr><td>{0}</td></tr>".format(desc) for desc in descriptions])
HTML("<table>"+rows+"</table>")
```

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [ニュースのページング](paging-news.md)
> [装飾マーカーを使用したテキストの強調表示](hit-highlighting.md)

## <a name="see-also"></a>関連項目 

 [Web でニュースを検索する](search-the-web.md)  
 [試してみる](https://azure.microsoft.com/services/cognitive-services/bing-news-search-api/)
