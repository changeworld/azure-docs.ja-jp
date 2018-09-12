---
title: Translator Text と Python で文の長さを取得する | Microsoft Docs
titleSuffix: Microsoft Cognitive Services
description: このクイック スタートでは、Cognitive Services の Translator Text API と Python を使って、テキストに含まれる文の長さを調べます。
services: cognitive-services
author: noellelacharite
manager: nolachar
ms.service: cognitive-services
ms.component: translator-text
ms.topic: quickstart
ms.date: 06/21/2018
ms.author: nolachar
ms.openlocfilehash: 7b822dfa2db1b9d106d6d569fb26d9f6e8109c83
ms.sourcegitcommit: 65b399eb756acde21e4da85862d92d98bf9eba86
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/22/2018
ms.locfileid: "43770986"
---
# <a name="quickstart-get-sentence-lengths-with-python"></a>クイック スタート: 文の長さを Python で取得する

このクイック スタートでは、Translator Text API を使って、テキストに含まれる文の長さを調べます。

## <a name="prerequisites"></a>前提条件

このコードを実行するには、[Python 3.x](https://www.python.org/downloads/) が必要です。

Translator Text API を使用するには、サブスクリプション キーも必要となります。「[Translator Text API にサインアップする方法](translator-text-how-to-signup.md)」を参照してください。

## <a name="breaksentence-request"></a>BreakSentence 要求

以下のコードは、[BreakSentence](./reference/v3-0-break-sentence.md) メソッドを使ってソース テキストを文に分割します。

1. 任意のコード エディターで新しい Python プロジェクトを作成します。
2. 次に示すコードを追加します。
3. `subscriptionKey` の値を、お使いのサブスクリプションで有効なアクセス キーに置き換えます。
4. プログラムを実行します。

```python
# -*- coding: utf-8 -*-

import http.client, urllib.parse, uuid, json

# **********************************************
# *** Update or verify the following values. ***
# **********************************************

# Replace the subscriptionKey string value with your valid subscription key.
subscriptionKey = 'ENTER KEY HERE'

host = 'api.cognitive.microsofttranslator.com'
path = '/breaksentence?api-version=3.0'

params = ''

text = 'How are you? I am fine. What did you do today?'

def breakSentences (content):

    headers = {
        'Ocp-Apim-Subscription-Key': subscriptionKey,
        'Content-type': 'application/json',
        'X-ClientTraceId': str(uuid.uuid4())
    }

    conn = http.client.HTTPSConnection(host)
    conn.request ("POST", path + params, content, headers)
    response = conn.getresponse ()
    return response.read ()

requestBody = [{
    'Text' : text,
}]
content = json.dumps(requestBody, ensure_ascii=False).encode('utf-8')
result = breakSentences (content)

# Note: We convert result, which is JSON, to and from an object so we can pretty-print it.
# We want to avoid escaping any Unicode characters that result contains. See:
# https://stackoverflow.com/questions/18337407/saving-utf-8-texts-in-json-dumps-as-utf8-not-as-u-escape-sequence
output = json.dumps(json.loads(result), indent=4, ensure_ascii=False)

print (output)
```

## <a name="breaksentence-response"></a>BreakSentence 応答

成功した応答は、次の例に示すように JSON で返されます。

```json
[
  {
    "detectedLanguage": {
      "language": "en",
      "score": 1.0
    },
    "sentLen": [
      13,
      11,
      22
    ]
  }
]
```

## <a name="next-steps"></a>次の手順

このクイック スタートをはじめとする各種ドキュメントで翻訳と表記変換を含んだサンプル コードを詳しく見てみましょう。GitHub にある Translator Text の各種サンプル プロジェクトもご覧ください。

> [!div class="nextstepaction"]
> [GitHub で Python のコード例を詳しく見てみる](https://aka.ms/TranslatorGitHub?type=&language=python)
