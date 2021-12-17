---
title: クイック スタート:REST API と Python を使用して画像に関する分析情報を取得する - Bing Visual Search
titleSuffix: Azure Cognitive Services
description: Bing Visual Search API および Python を使用して画像をアップロードし、その画像に関する分析情報を取得する方法について説明します。
services: cognitive-services
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-visual-search
ms.topic: quickstart
ms.date: 05/22/2020
ms.custom: devx-track-python
ms.openlocfilehash: 1470bfe66ee79b993e624f1bf6f9d5ca31c84b60
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/24/2021
ms.locfileid: "128658699"
---
# <a name="quickstart-get-image-insights-using-the-bing-visual-search-rest-api-and-python"></a>クイック スタート:Bing Visual Search REST API と Python を使用して画像に関する分析情報を取得する

> [!WARNING]
> Bing Search API は、Cognitive Services から Bing Search Services に移行されます。 **2020 年 10 月 30 日** 以降、Bing Search の新しいインスタンスは、[こちら](/bing/search-apis/bing-web-search/create-bing-search-service-resource)に記載されているプロセスに従ってプロビジョニングする必要があります。
> Cognitive Services を使用してプロビジョニングされた Bing Search API は、次の 3 年間、または Enterprise Agreement の終わり (どちらか先に発生した方) までサポートされます。
> 移行手順については、[Bing Search Services](/bing/search-apis/bing-web-search/create-bing-search-service-resource) に関するページを参照してください。

このクイックスタートを使用して、Bing Visual Search API を呼び出してみましょう。 この Python アプリケーションは、API に画像をアップロードし、返された情報を表示するものです。 このアプリケーションは Python で記述されていますが、API はほとんどのプログラミング言語と互換性のある RESTful Web サービスです。

## <a name="prerequisites"></a>前提条件

* [Python 3.x](https://www.python.org/)

[!INCLUDE [cognitive-services-bing-visual-search-signup-requirements](../../../../includes/cognitive-services-bing-visual-search-signup-requirements.md)]

## <a name="initialize-the-application"></a>アプリケーションを初期化する

1. 好みの IDE またはエディターで新しい Python ファイルを作成し、次の `import` ステートメントを追加します。

    ```python
    import requests, json
    ```

2. サブスクリプション キー、エンドポイント、およびアップロードしている画像へのパスのための変数を作成します。 `BASE_URI` 値には、次のコードのグローバル エンドポイントを使用するか、Azure portal に表示される、お使いのリソースの[カスタム サブドメイン](../../../cognitive-services/cognitive-services-custom-subdomains.md) エンドポイントを使用することができます。

    ```python

    BASE_URI = 'https://api.cognitive.microsoft.com/bing/v7.0/images/visualsearch'
    SUBSCRIPTION_KEY = 'your-subscription-key'
    imagePath = 'your-image-path'
    ```
    
3. ローカルの画像をアップロードする際には、フォーム データに `Content-Disposition` ヘッダーが含まれている必要があります。 その `name` パラメーターを "image" に設定して、`filename` パラメーターをお使いの画像のファイル名に設定します。 フォームの内容には、画像のバイナリ データが含まれます。 アップロードできる画像の最大サイズは、1 MB です。
    
    ```
    --boundary_1234-abcd
    Content-Disposition: form-data; name="image"; filename="myimagefile.jpg"
    
    ÿØÿà JFIF ÖÆ68g-¤CWŸþ29ÌÄøÖ‘º«™æ±èuZiÀ)"óÓß°Î= ØJ9á+*G¦...
    
    --boundary_1234-abcd--
    ```

4. 要求のヘッダー情報を保持するディクショナリ オブジェクトを作成します。 お使いのサブスクリプション キーを `Ocp-Apim-Subscription-Key` 文字列とバインドします。

    ```python
    HEADERS = {'Ocp-Apim-Subscription-Key': SUBSCRIPTION_KEY}
    ```

5. お使いの画像を格納する別のディクショナリを作成します。この画像は、要求を送信するときに開かれてアップロードされます。

    ```python
    file = {'image' : ('myfile', open(imagePath, 'rb'))}
    ```

## <a name="parse-the-json-response"></a>JSON 応答を解析します

`print_json()` という名前のメソッドを作成します。このメソッドは、API 応答を受け取り、JSON を出力します。

```python
def print_json(obj):
    """Print the object as json"""
    print(json.dumps(obj, sort_keys=True, indent=2, separators=(',', ': ')))
```

## <a name="send-the-request"></a>要求を送信する

`requests.post()` を使用して、要求を Bing Visual Search API に送信します。 エンドポイント、ヘッダー、およびファイル情報の文字列を含めます。 `print_json()` を使用して `response.json()` を出力します。

```python
try:
    response = requests.post(BASE_URI, headers=HEADERS, files=file)
    response.raise_for_status()
    print_json(response.json())
    
except Exception as ex:
    raise ex
```

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [Visual Search のシングルページ Web アプリを作成する](../tutorial-bing-visual-search-single-page-app.md)