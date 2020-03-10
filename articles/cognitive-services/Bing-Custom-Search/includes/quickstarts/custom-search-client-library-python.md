---
title: Bing Custom Search Python クライアント ライブラリのクイックスタート
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 02/27/2020
ms.author: aahi
ms.openlocfilehash: ec0ffdcf86e67a7126a3100c1e20b6e5c3474e35
ms.sourcegitcommit: e4c33439642cf05682af7f28db1dbdb5cf273cc6
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/03/2020
ms.locfileid: "78253051"
---
Python 用 Bing Custom Search クライアント ライブラリの使用を開始します。 以下の手順に従って、パッケージをインストールし、基本タスクのコード例を試してみましょう。 Bing Custom Search API を使用すると、関心のあるトピックに合わせてカスタマイズされた、広告なしの検索エクスペリエンスを作成できます。このサンプルのソース コードは、[GitHub](https://github.com/Azure-Samples/cognitive-services-python-sdk-samples/blob/master/samples/search/custom_search_samples.py) にあります。

Python 用 Bing Custom Search クライアント ライブラリを使用して、次のことを行います。
* Web で Bing Custom Search インスタンスの検索結果を探します。

[リファレンス ドキュメント](https://docs.microsoft.com/python/api/overview/azure/cognitiveservices/customsearch?view=azure-python) | [ライブラリのソース コード](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/cognitiveservices/azure-cognitiveservices-search-customsearch) | [パッケージ (PyPi)](https://pypi.org/project/azure-cognitiveservices-search-customsearch/) | [サンプル](https://github.com/Azure-Samples/cognitive-services-python-sdk-samples/)


## <a name="prerequisites"></a>前提条件

- Bing Custom Search インスタンス。 「[クイック スタート:初めての Bing Custom Search インスタンスを作成する](../../quick-start.md)」で詳細を確認する。
- Python [2.x または 3.x](https://www.python.org/) 
- [Bing Custom Search SDK for Python](https://pypi.org/project/azure-cognitiveservices-search-customsearch/) 

[!INCLUDE [cognitive-services-bing-custom-search-prerequisites](~/includes/cognitive-services-bing-custom-search-signup-requirements.md)]

## <a name="install-the-python-client-library"></a>Python クライアント ライブラリをインストールする

次のコマンドを使用して、Bing Custom Search クライアント ライブラリをインストールします。

```Console
python -m pip install azure-cognitiveservices-search-customsearch
```


## <a name="create-a-new-application"></a>新しいアプリケーションを作成する

好みのエディターまたは IDE で新しい Python ファイルを作成し、次の import を追加します。

```python
from azure.cognitiveservices.search.customsearch import CustomSearchClient
from msrest.authentication import CognitiveServicesCredentials
```

## <a name="create-a-search-client-and-send-a-request"></a>検索クライアントを作成して要求を送信する

1. 対象のサブスクリプション キーとエンドポイントの変数を作成します。

    ```python
    subscription_key = 'your-subscription-key'
    endpoint = 'your-endpoint'
    ```

2. サブスクリプション キーを使用して、`CognitiveServicesCredentials` オブジェクトを使って、`CustomSearchClient` のインスタンスを作成します。 

    ```python
    client = CustomSearchClient(endpoint=endpoint, credentials=CognitiveServicesCredentials(subscription_key))
    ```

3. `client.custom_instance.search()` を含む検索要求を送信します。 検索語句を `query` パラメーターに追加し、検索インスタンスを使用するためにカスタム構成 ID に `custom_config` を設定します。 ID を [Bing Custom Search ポータル](https://www.customsearch.ai/)から取得するには、 **[運用]** タブをクリックします。

    ```python
    web_data = client.custom_instance.search(query="xbox", custom_config="your-configuration-id")
    ```

## <a name="view-the-search-results"></a>検索結果を表示する

任意の Web ページの検索結果が見つかった場合は、1 つ目を取得し、その名前、URL、および見つかった Web ページの合計を印刷します。

```python
if web_data.web_pages.value:
    first_web_result = web_data.web_pages.value[0]
    print("Web Pages result count: {}".format(len(web_data.web_pages.value)))
    print("First Web Page name: {}".format(first_web_result.name))
    print("First Web Page url: {}".format(first_web_result.url))
else:
    print("Didn't see any web data..")
```

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [Custom Search Web アプリの作成](../../tutorials/custom-search-web-page.md)
