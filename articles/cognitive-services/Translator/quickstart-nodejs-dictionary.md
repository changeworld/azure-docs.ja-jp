---
title: Translator Text と Node.js で翻訳の代替候補を探す | Microsoft Docs
titleSuffix: Microsoft Cognitive Services
description: このクイック スタートでは、Cognitive Services の Translator Text API と Node.js を使って、文脈中における用語の例と翻訳の代替候補を探します。
services: cognitive-services
author: noellelacharite
manager: nolachar
ms.service: cognitive-services
ms.component: translator-text
ms.topic: quickstart
ms.date: 06/21/2018
ms.author: nolachar
ms.openlocfilehash: aeb97e1c4e7c4e8df852bea55383e857f7d5a690
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/23/2018
ms.locfileid: "43771087"
---
# <a name="quickstart-find-alternate-translations-and-usage-with-nodejs"></a>クイック スタート: Node.js を使って翻訳の代替候補と用法を探す

このクイック スタートでは、Translator Text API を使って、特定の用語の翻訳について考えられる代替候補の情報とそれらの代替候補の用例を探します。

## <a name="prerequisites"></a>前提条件

このコードを実行するには [Node.js 6](https://nodejs.org/en/download/) が必要です。

Translator Text API を使用するには、サブスクリプション キーも必要となります。「[Translator Text API にサインアップする方法](translator-text-how-to-signup.md)」を参照してください。

## <a name="dictionary-lookup-request"></a>辞書検索要求

以下のコードは、[Dictionary Lookup](./reference/v3-0-dictionary-lookup.md) メソッドを使って、単語の翻訳の代替候補を取得します。

1. 任意のコード エディターで新しい Node.js プロジェクトを作成します。
2. 次に示すコードを追加します。
3. `subscriptionKey` の値を、お使いのサブスクリプションで有効なアクセス キーに置き換えます。
4. プログラムを実行します。

```javascript
'use strict';

let fs = require ('fs');
let https = require ('https');

// **********************************************
// *** Update or verify the following values. ***
// **********************************************

// Replace the subscriptionKey string value with your valid subscription key.
let subscriptionKey = 'ENTER KEY HERE';

let host = 'api.cognitive.microsofttranslator.com';
let path = '/dictionary/lookup?api-version=3.0';

let params = '&from=en&to=fr';

let text = 'great';

let response_handler = function (response) {
    let body = '';
    response.on ('data', function (d) {
        body += d;
    });
    response.on ('end', function () {
        let json = JSON.stringify(JSON.parse(body), null, 4);
        console.log(json);
    });
    response.on ('error', function (e) {
        console.log ('Error: ' + e.message);
    });
};

let get_guid = function () {
  return 'xxxxxxxx-xxxx-4xxx-yxxx-xxxxxxxxxxxx'.replace(/[xy]/g, function(c) {
    var r = Math.random() * 16 | 0, v = c == 'x' ? r : (r & 0x3 | 0x8);
    return v.toString(16);
  });
}

let DictionaryLookup = function (content) {
    let request_params = {
        method : 'POST',
        hostname : host,
        path : path + params,
        headers : {
            'Content-Type' : 'application/json',
            'Ocp-Apim-Subscription-Key' : subscriptionKey,
            'X-ClientTraceId' : get_guid (),
        }
    };

    let req = https.request (request_params, response_handler);
    req.write (content);
    req.end ();
}

let content = JSON.stringify ([{'Text' : text}]);

DictionaryLookup (content);
```

## <a name="dictionary-lookup-response"></a>辞書検索応答

成功した応答は、次の例に示すように JSON で返されます。

```json
[
  {
    "normalizedSource": "great",
    "displaySource": "great",
    "translations": [
      {
        "normalizedTarget": "grand",
        "displayTarget": "grand",
        "posTag": "ADJ",
        "confidence": 0.2783,
        "prefixWord": "",
        "backTranslations": [
          {
            "normalizedText": "great",
            "displayText": "great",
            "numExamples": 15,
            "frequencyCount": 34358
          },
          {
            "normalizedText": "big",
            "displayText": "big",
            "numExamples": 15,
            "frequencyCount": 21770
          },
...
        ]
      },
      {
        "normalizedTarget": "super",
        "displayTarget": "super",
        "posTag": "ADJ",
        "confidence": 0.1514,
        "prefixWord": "",
        "backTranslations": [
          {
            "normalizedText": "super",
            "displayText": "super",
            "numExamples": 15,
            "frequencyCount": 12023
          },
          {
            "normalizedText": "great",
            "displayText": "great",
            "numExamples": 15,
            "frequencyCount": 10931
          },
...
        ]
      },
...
    ]
  }
]
```

## <a name="dictionary-examples-request"></a>辞書の例の要求

以下のコードは、辞書に登録されている用語について、文脈中におけるその使い方の例を [Dictionary Examples](./reference/v3-0-dictionary-examples.md) メソッドを使って取得します。

1. 任意のコード エディターで新しい Node.js プロジェクトを作成します。
2. 次に示すコードを追加します。
3. `subscriptionKey` の値を、お使いのサブスクリプションで有効なアクセス キーに置き換えます。
4. プログラムを実行します。

```javascript
'use strict';

let fs = require ('fs');
let https = require ('https');

// **********************************************
// *** Update or verify the following values. ***
// **********************************************

// Replace the subscriptionKey string value with your valid subscription key.
let subscriptionKey = 'ENTER KEY HERE';

let host = 'api.cognitive.microsofttranslator.com';
let path = '/dictionary/examples?api-version=3.0';

let params = '&from=en&to=fr';

let text = 'great';
let translation = 'formidable';

let response_handler = function (response) {
    let body = '';
    response.on ('data', function (d) {
        body += d;
    });
    response.on ('end', function () {
        let json = JSON.stringify(JSON.parse(body), null, 4);
        console.log(json);
    });
    response.on ('error', function (e) {
        console.log ('Error: ' + e.message);
    });
};

let get_guid = function () {
  return 'xxxxxxxx-xxxx-4xxx-yxxx-xxxxxxxxxxxx'.replace(/[xy]/g, function(c) {
    var r = Math.random() * 16 | 0, v = c == 'x' ? r : (r & 0x3 | 0x8);
    return v.toString(16);
  });
}

let DictionaryExamples = function (content) {
    let request_params = {
        method : 'POST',
        hostname : host,
        path : path + params,
        headers : {
            'Content-Type' : 'application/json',
            'Ocp-Apim-Subscription-Key' : subscriptionKey,
            'X-ClientTraceId' : get_guid (),
        }
    };

    let req = https.request (request_params, response_handler);
    req.write (content);
    req.end ();
}

let content = JSON.stringify ([{'Text' : text, 'Translation' : translation}]);

DictionaryExamples (content);
```

## <a name="dictionary-examples-response"></a>辞書の例の応答

成功した応答は、次の例に示すように JSON で返されます。

```json
[
  {
    "normalizedSource": "great",
    "normalizedTarget": "formidable",
    "examples": [
      {
        "sourcePrefix": "You have a ",
        "sourceTerm": "great",
        "sourceSuffix": " expression there.",
        "targetPrefix": "Vous avez une expression ",
        "targetTerm": "formidable",
        "targetSuffix": "."
      },
      {
        "sourcePrefix": "You played a ",
        "sourceTerm": "great",
        "sourceSuffix": " game today.",
        "targetPrefix": "Vous avez été ",
        "targetTerm": "formidable",
        "targetSuffix": "."
      },
...
    ]
  }
]
```

## <a name="next-steps"></a>次の手順

このクイック スタートをはじめとする各種ドキュメントで翻訳と表記変換を含んだサンプル コードを詳しく見てみましょう。GitHub にある Translator Text の各種サンプル プロジェクトもご覧ください。

> [!div class="nextstepaction"]
> [GitHub で Node.js のコード例を詳しく見てみる](https://aka.ms/TranslatorGitHub?type=&language=javascript)
