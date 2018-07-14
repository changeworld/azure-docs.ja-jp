---
title: 呼び出しと応答 - Azure Cognitive Services、Bing Web Search API のための Python クイック スタート | Microsoft Docs
description: Azure 上の Microsoft Cognitive Services で Bing Web Search API の使用をすぐに開始するために役立つ情報とコード サンプルを提供します。
services: cognitive-services
author: v-jerkin
ms.service: cognitive-services
ms.component: bing-web-search
ms.topic: article
ms.date: 9/18/2017
ms.author: v-jerkin
ms.openlocfilehash: 8d4df9db60c7a74a5b9e53d4622528c0054b4f19
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/23/2018
ms.locfileid: "35374144"
---
# <a name="call-and-response-your-first-bing-web-search-query-in-python"></a>呼び出しと応答: Python ではじめての Bing Web Search クエリ

Bing Web Search API は、Bing によって決定される、ユーザーのクエリに関連する検索結果を返すことによって、Bing.com/Search と同様のエクスペリエンスを提供します。 結果には、Web ページ、画像、動画、ニュース、およびエンティティと共に、関連する検索クエリ、誤字の修正、タイム ゾーン、単位変換、翻訳、計算が含まれます。 取得する結果の種類は、それらの関連性と、ユーザーがサブスクライブしている Bing Search APIs のレベルに基づいています。

API の技術的な詳細については、[API リファレンス](https://docs.microsoft.com/rest/api/cognitiveservices/bing-web-api-v7-reference)をご覧ください。

バインダーの起動バッジをクリックして、次の例を [MyBinder](https://mybinder.org) 上で Jupyter Notebook として実行できます。 

[![バインダー](https://mybinder.org/badge.svg)](https://mybinder.org/v2/gh/Microsoft/cognitive-services-notebooks/master?filepath=BingWebSearchAPI.ipynb)


## <a name="prerequisites"></a>前提条件
[Cognitive Services API アカウント](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account)と **Bing Search APIs** を取得している必要があります。 このクイック スタートには[無料試用版](https://azure.microsoft.com/try/cognitive-services/?api=bing-web-search-api)で十分です。 無料試用版を起動するとき、アクセス キーを入力する必要があります。あるいは、Azure ダッシュボードの有料サブスクリプション キーを使用できます。

## <a name="running-the-walkthrough"></a>チュートリアルの実行

`subscription_key` を Bing API サービス用のお使いの API キーに設定します。


```python
subscription_key = None
assert subscription_key
```

次に、`search_url` エンドポイントが正しいことを確認します。 このドキュメントの作成時点では、Bing Search APIs にはエンドポイントは 1 つだけ使用されます。 承認エラーが発生した場合は、Azure ダッシュボードで Bing Search エンドポイントに対してこの値をダブルチェックしてください。


```python
search_url = "https://api.cognitive.microsoft.com/bing/v7.0/search"
```

Bing for Microsoft Cognitive Services のクエリに `search_term` を設定します。


```python
search_term = "Microsoft Cognitive Services"
```

次のブロックでは、Python 内の `requests` ライブラリを使用して、Bing Search APIs を呼び出し、JSON オブジェクトとして結果を返します。 `headers` を介して API キーを渡し、`params` ディクショナリを介して検索用語を渡していることに注意してください。 検索結果をフィルター処理するために使用できる全オプションの一覧を確認するには、「[REST API](https://docs.microsoft.com/rest/api/cognitiveservices/bing-web-api-v7-reference)」ドキュメントをご覧ください。


```python
import requests

headers = {"Ocp-Apim-Subscription-Key" : subscription_key}
params  = {"q": search_term, "textDecorations":True, "textFormat":"HTML"}
response = requests.get(search_url, headers=headers, params=params)
response.raise_for_status()
search_results = response.json()
```

`search_results` オブジェクトには、関連するクエリやページなどのさまざまなメタデータと共に、検索結果が含まれています。 次のコード行は、クエリによって返される上位ページの書式を設定します。


```python
from IPython.display import HTML

rows = "\n".join(["""<tr>
                       <td><a href=\"{0}\">{1}</a></td>
                       <td>{2}</td>
                     </tr>""".format(v["url"],v["name"],v["snippet"]) \
                  for v in search_results["webPages"]["value"]])
HTML("<table>{0}</table>".format(rows))
```

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [Bing Web 検索単一ページ アプリのチュートリアル](../tutorial-bing-web-search-single-page-app.md)

## <a name="see-also"></a>関連項目 

[Bing Web Search の概要](../overview.md)  
[試してみる](https://azure.microsoft.com/services/cognitive-services/bing-web-search-api/)  
[無料試用版のアクセス キーを取得する](https://azure.microsoft.com/try/cognitive-services/?api=bing-web-search-api)
[Bing Web Search API リファレンス](https://docs.microsoft.com/rest/api/cognitiveservices/bing-web-api-v7-reference)
