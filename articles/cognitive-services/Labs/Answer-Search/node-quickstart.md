---
title: Microsoft Cognitive Services、Project Answer Search の Node のクイック スタート | Microsoft Docs
description: Azure 上で Microsoft Cognitive Services の Project Answer Search の使用を開始します。
services: cognitive-services
author: mikedodaro
ms.service: cognitive-services
ms.technology: project-answer-search
ms.topic: article
ms.date: 04/13/2018
ms.author: rosh, v-gedod
ms.openlocfilehash: 36b2709d39230aae7929164ba4c9306f57043b43
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/23/2018
ms.locfileid: "35376192"
---
# <a name="project-answer-search-node-quickstart"></a>Project Answer Search の Node のクイック スタート

次の Node の例では、ヨセミテ国立公園に関する情報を問い合わせるクエリを作成します。

## <a name="prerequisites"></a>前提条件

[Cognitive Services Labs](https://aka.ms/answersearchsubscription) で無料試用版のアクセス キーを取得します

この例では、Node v8.9.4 を使用します

## <a name="code-scenario"></a>シナリオのコードを書く 

次のコードを使用して回答を取得します。
これは、次の手順で実装されます。
1. エンドポイントをホストとパスで指定する変数を宣言します。
2. プレビューするクエリ URL を指定し、クエリ パラメーターを追加します。  
3. 応答のハンドラー関数を作成します。
4. 要求を作成し、*Ocp-Apim-Subscription-Key* ヘッダーを追加する検索関数を定義します。
5. 検索機能を実行します。 

このデモの完全なコードを次に示します。

````
'use strict';

let https = require('https');

// Replace the subscriptionKey string value with your valid subscription key.
let subscriptionKey = 'YOUR-SUBSCRIPTION-KEY'; 

let host = 'api.labs.cognitive.microsoft.com';
let path = '/answerSearch/v7.0/search';

let mkt = 'en-us';
let q = 'Yosemite National Park';

let params = '?q=' + encodeURI(q) + '&mkt=en-us';

let response_handler = function (response) {
    let body = '';
    response.on('data', function (d) {
        body += d;
    });
    response.on('end', function () {
        let body_ = JSON.parse(body);
        let body__ = JSON.stringify(body_, null, '  ');
        console.log(body__);
    });
    response.on('error', function (e) {
        console.log('Error: ' + e.message);
    });
};

let Search = function () {
    let request_params = {
        method: 'GET',
        hostname: host,
        path: path + params,
        headers: {
            'Ocp-Apim-Subscription-Key': subscriptionKey,
        }
    };

    let req = https.request(request_params, response_handler);
    req.end();
}

Search();

````

## <a name="next-steps"></a>次の手順
- [C# のコード例](c-sharp-quickstart.md)
- [Java のクイック スタート](java-quickstart.md)
- [Python のクイック スタート](python-quickstart.md)