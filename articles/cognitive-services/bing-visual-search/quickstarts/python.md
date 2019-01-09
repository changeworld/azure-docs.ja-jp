---
title: クイック スタート:Bing Visual Search REST API と Python を使用して画像に関する分析情報を取得する
titleSuffix: Azure Cognitive Services
description: Bing Visual Search API に画像をアップロードし、画像に関する分析情報を取得する方法について説明します。
services: cognitive-services
author: swhite-msft
manager: cgronlun
ms.service: cognitive-services
ms.component: bing-visual-search
ms.topic: quickstart
ms.date: 5/16/2018
ms.author: scottwhi
ms.openlocfilehash: 3930de4d8d1f50c0ba6908ea642fc152c29b7371
ms.sourcegitcommit: 21466e845ceab74aff3ebfd541e020e0313e43d9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/21/2018
ms.locfileid: "53744804"
---
# <a name="quickstart-your-first-bing-visual-search-query-in-python"></a>クイック スタート:Python での最初の Bing Visual Search クエリ

このクイック スタートを使用すると、Bing Visual Search API への最初の呼び出しを行い、検索結果を表示することができます。 このシンプルな JavaScript アプリケーションは、API に画像をアップロードし、それについて返された情報を表示するというものです。 このアプリケーションは JavaScript で記述されていますが、API はほとんどのプログラミング言語と互換性のある RESTful Web サービスです。

ローカルの画像をアップロードする際には、POST フォーム データに Content-Disposition ヘッダーが含まれている必要があります。 その `name` パラメーターには "image" を設定する必要があり、`filename` パラメーターには任意の文字列を設定できます。 フォームの内容は、イメージのバイナリです。 アップロードできるイメージの最大サイズは、1 MB です。

```
--boundary_1234-abcd
Content-Disposition: form-data; name="image"; filename="myimagefile.jpg"

ÿØÿà JFIF ÖÆ68g-¤CWŸþ29ÌÄøÖ‘º«™æ±èuZiÀ)"óÓß°Î= ØJ9á+*G¦...

--boundary_1234-abcd--
```

## <a name="prerequisites"></a>前提条件

* [Python 3.x](https://www.python.org/)


[!INCLUDE [cognitive-services-bing-visual-search-signup-requirements](../../../../includes/cognitive-services-bing-image-search-signup-requirements.md)]

## <a name="initialize-the-application"></a>アプリケーションを初期化する

1. お気に入りの IDE またはエディターで新しい Python ファイルを作成し、次の import ステートメントを追加します。

    ```python
    import requests, json
    ```

2. サブスクリプション キー、エンドポイント、およびアップロードしている画像へのパスのための変数を作成します。

    ```python

    BASE_URI = 'https://api.cognitive.microsoft.com/bing/v7.0/images/visualsearch'
    SUBSCRIPTION_KEY = 'your-subscription-key'
    imagePath = 'your-image-path'
    ```

3. 要求のヘッダー情報を保持するディクショナリ オブジェクトを作成します。 次に示すように、文字列 `Ocp-Apim-Subscription-Key` にサブスクリプション キーをバインドします。

    ```python
    HEADERS = {'Ocp-Apim-Subscription-Key': SUBSCRIPTION_KEY}
    ```

4. 画像を格納するための別のディクショナリを作成します。この画像は、要求を送信するときに開かれてアップロードされます。 

    ```python
    file = {'image' : ('myfile', open(imagePath, 'rb'))}
    ```

## <a name="parse-the-json-response"></a>JSON 応答を解析します

1. `print_json()` という名前のメソッドを作成します。このメソッドは、API 応答を取り込み、JSON を出力します。

    ```python
    def print_json(obj):
        """Print the object as json"""
        print(json.dumps(obj, sort_keys=True, indent=2, separators=(',', ': ')))
    ```

## <a name="send-the-request"></a>要求を送信する

1. `requests.post()` を使用して、要求を Bing Visual Search API に送信します。 エンドポイント、ヘッダー、およびファイル情報の文字列を含めます。 `print_json()` を使用して `response.json()` を出力します

    ```python
    try:
        response = requests.post(BASE_URI, headers=HEADERS, files=file)
        response.raise_for_status()
        print_json(response.json())
    
    except Exception as ex:
        raise ex
    ```

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [Custom Search Web アプリの作成](../tutorial-bing-visual-search-single-page-app.md)