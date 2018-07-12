---
title: News Search SDK の Python 向けクイック スタート | Microsoft Docs
description: News Search SDK コンソール アプリケーションの設定。
titleSuffix: Azure News Search SDK Python quickstart
services: cognitive-services
author: mikedodaro
manager: rosh
ms.service: cognitive-services
ms.component: bing-news-search
ms.topic: article
ms.date: 02/14/2018
ms.author: v-gedod
ms.openlocfilehash: 6d212d1477ecf583a038e33e72aab3d60f6aa050
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/23/2018
ms.locfileid: "35377925"
---
# <a name="news-search-sdk-python-quickstart"></a>News Search SDK Python のクイック スタート

News Search SDK には、Web クエリと結果解析のための REST API 機能が含まれています。 

[Python Bing News Search SDK サンプルのソース コード](https://github.com/Azure-Samples/cognitive-services-python-sdk-samples/blob/master/samples/search/news_search_samples.py)は、Git Hub で入手できます。

## <a name="application-dependencies"></a>アプリケーションの依存関係
Python をまだインストールしていない場合は、インストールします。 この SDK は、Python 2.7、3.3、3.4、3.5、3.6 と互換性があります。

Python 開発では、一般的に、[仮想環境](https://docs.python.org/3/tutorial/venv.html)を使用することをお勧めします。 [venv モジュール](https://pypi.python.org/pypi/virtualenv)を使用して仮想環境をインストールして初期化します。 Python 2.7 向けの virtualenv をインストールする必要があります。
```
python -m venv mytestenv
```
Bing News Search SDK 依存関係をインストールします。
```
cd mytestenv
python -m pip install azure-cognitiveservices-search-newssearch
```
## <a name="news-search-client"></a>News Search クライアント
*[検索]* で [Cognitive Services のアクセス キー](https://azure.microsoft.com/try/cognitive-services/)を取得します。 インポートを追加します。
```
from azure.cognitiveservices.search.newssearch import NewsSearchAPI
from msrest.authentication import CognitiveServicesCredentials

subscription_key = "YOUR-SUBSCRIPTION-KEY"
```
`CognitiveServicesCredentials` のインスタンスを作成します。 クライアントをインスタンス化します。
```
client = NewsSearchAPI(CognitiveServicesCredentials(subscription_key))
```
結果を検索し、最初の Web ページの結果を出力します。
```
news_result = client.news.search(query="Quantum Computing", market="en-us", count=10)
print("Search news for query \"Quantum Computing\" with market and count")

if news_result.value:
    first_news_result = news_result.value[0]
    print("Total estimated matches value: {}".format(news_result.total_estimated_matches))
    print("News result count: {}".format(len(news_result.value)))
    print("First news name: {}".format(first_news_result.name))
    print("First news url: {}".format(first_news_result.url))
    print("First news description: {}".format(first_news_result.description))
    print("First published time: {}".format(first_news_result.date_published))
    print("First news provider: {}".format(first_news_result.provider[0].name))
else:
    print("Didn't see any news result data..")

```
`freshness` および `sortBy` パラメーターを使用して、最近の "Artificial Intelligence" (人工知能) に関するニュースをフィルターを使用して検索します。 結果の数を確認し、最初のニュース項目結果の `totalEstimatedMatches`、`name`、`url`、`description`、`published time`、および `name of provider` を出力します。
```
def news_search_with_filtering(subscription_key):

    client = NewsSearchAPI(CognitiveServicesCredentials(subscription_key))

    try:
        news_result = client.news.search(
            query="Artificial Intelligence",
            market="en-us",
            freshness="Week",
            sort_by="Date"
        )
        print("\r\nSearch most recent news for query \"Artificial Intelligence\" with freshness and sortBy")

        if news_result.value:
            first_news_result = news_result.value[0]
            print("News result count: {}".format(len(news_result.value)))
            print("First news name: {}".format(first_news_result.name))
            print("First news url: {}".format(first_news_result.url))
            print("First news description: {}".format(first_news_result.description))
            print("First published time: {}".format(first_news_result.date_published))
            print("First news provider: {}".format(first_news_result.provider[0].name))
        else:
            print("Didn't see any news result data..")

    except Exception as err:
        print("Encountered exception. {}".format(err))

```
セーフ サーチを使用して、映画とテレビのエンターテインメントに関するカテゴリのニュースを検索します。 結果の数を確認し、最初のニュース項目結果の `category`、`name`、`url`、`description`、`published time`、および `name of provider` を出力します。
```
def news_category(subscription_key):

    client = NewsSearchAPI(CognitiveServicesCredentials(subscription_key))

    try:
        news_result = client.news.category(
            category="Entertainment_MovieAndTV",
            market="en-us",
            safe_search="strict"
        )
        print("\r\nSearch category news for movie and TV entertainment with safe search")

        if news_result.value:
            first_news_result = news_result.value[0]
            print("News result count: {}".format(len(news_result.value)))
            print("First news category: {}".format(first_news_result.category))
            print("First news name: {}".format(first_news_result.name))
            print("First news url: {}".format(first_news_result.url))
            print("First news description: {}".format(first_news_result.description))
            print("First published time: {}".format(first_news_result.date_published))
            print("First news provider: {}".format(first_news_result.provider[0].name))
        else:
            print("Didn't see any news result data..")

    except Exception as err:
        print("Encountered exception. {}".format(err))


```
Bing でトレンドのニュース トピックを検索します。  結果の数を確認し、最初のニュース結果の `name`、`text of query`、`webSearchUrl`、`newsSearchUrl`、および `image Url` を出力します。
```
def news_trending(subscription_key):

    client = NewsSearchAPI(CognitiveServicesCredentials(subscription_key))

    try:
        trending_topics = client.news.trending(market="en-us")
        print("\r\nSearch news trending topics in Bing")

        if trending_topics.value:
            first_topic = trending_topics.value[0]
            print("News result count: {}".format(len(trending_topics.value)))
            print("First topic name: {}".format(first_topic.name))
            print("First topic query: {}".format(first_topic.query.text))
            print("First topic image url: {}".format(first_topic.image.url))
            print("First topic webSearchUrl: {}".format(first_topic.web_search_url))
            print("First topic newsSearchUrl: {}".format(first_topic.news_search_url))
        else:
            print("Didn't see any topics result data..")

    except Exception as err:
        print("Encountered exception. {}".format(err))

```

## <a name="next-steps"></a>次の手順

[Cognitive Services の Python 向け SDK のサンプル](https://github.com/Azure-Samples/cognitive-services-python-sdk-samples)


