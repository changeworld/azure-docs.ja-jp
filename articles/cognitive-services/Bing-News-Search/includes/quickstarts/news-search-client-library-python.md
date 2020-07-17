---
title: Bing News Search Python クライアント ライブラリのクイックスタート
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 03/12/2020
ms.author: aahi
ms.openlocfilehash: c1bd0d86a3fd9d19d67d84b9b05955421373e01e
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/27/2020
ms.locfileid: "79503511"
---
このクイックスタートでは、Bing News Search の Python 用のクライアント ライブラリを使用してニュースの検索を開始します。 Bing News Search の REST API はほとんどのプログラミング言語に対応していますが、このクライアント ライブラリを使用すると、サービスをお使いのアプリケーションに簡単に統合することができます。 このサンプルのソース コードは、[GitHub](https://github.com/Azure-Samples/cognitive-services-python-sdk-samples/blob/master/samples/search/news_search_samples.py) にあります。

## <a name="prerequisites"></a>前提条件

* [Python](https://www.python.org/) 2.x または 3.x

Python 開発用の[仮想環境](https://docs.python.org/3/tutorial/venv.html)を使用することをお勧めします。 仮想環境のインストールと初期化は、[venv モジュール](https://pypi.python.org/pypi/virtualenv)を使用して行えます。 Python 2.7 向けの virtualenv をインストールする必要があります。 次のコマンドで仮想環境を作成できます。

```console
python -m venv mytestenv
```

Bing News Search クライアント ライブラリの依存関係は、次のコマンドでインストールできます。
    
```console
python -m pip install azure-cognitiveservices-search-newssearch
```

[!INCLUDE [cognitive-services-bing-news-search-signup-requirements](~/includes/cognitive-services-bing-news-search-signup-requirements.md)]

## <a name="create-and-initialize-the-application"></a>アプリケーションを作成して初期化する

1. 任意の IDE またはエディターで新しい Python ファイルを作成し、次のライブラリをインポートします。 サブスクリプション キーと検索語句の変数を作成します。

    ```python
    from azure.cognitiveservices.search.newssearch import NewsSearchClient
    from msrest.authentication import CognitiveServicesCredentials
    subscription_key = "YOUR-SUBSCRIPTION-KEY"
    endpoint = "YOUR-ENDPOINT"
    search_term = "Quantum Computing"
    ```

## <a name="initialize-the-client-and-send-a-request"></a>クライアントを初期化して要求を送信する

1. `CognitiveServicesCredentials` のインスタンスを作成します。
    
    ```python
    client = NewsSearchClient(endpoint=endpoint, credentials=CognitiveServicesCredentials(subscription_key))
    ```

2. News Search API に検索クエリを送信して、応答を保存します。

    ```python
    news_result = client.news.search(query=search_term, market="en-us", count=10)
    ```

## <a name="parse-the-response"></a>応答を解析する

検索結果が見つかったら、最初の Web ページの結果を出力します。

```python
if news_result.value:
    first_news_result = news_result.value[0]
    print("Total estimated matches value: {}".format(
        news_result.total_estimated_matches))
    print("News result count: {}".format(len(news_result.value)))
    print("First news name: {}".format(first_news_result.name))
    print("First news url: {}".format(first_news_result.url))
    print("First news description: {}".format(first_news_result.description))
    print("First published time: {}".format(first_news_result.date_published))
    print("First news provider: {}".format(first_news_result.provider[0].name))
else:
    print("Didn't see any news result data..")
```

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [単一ページの Web アプリの作成](../../tutorial-bing-news-search-single-page-app.md)
