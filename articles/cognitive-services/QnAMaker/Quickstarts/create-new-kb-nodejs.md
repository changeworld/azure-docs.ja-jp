---
title: 'クイック スタート: API Node.js - ナレッジ ベースの作成 - QnA Maker'
description: このクイック スタートでは、Cognitive Services API アカウントの Azure ダッシュボードに表示される QnA Maker ナレッジ ベースのサンプルをプログラムから作成する手順を紹介しています。
services: cognitive-services
author: diberry
manager: cgronlun
ms.service: cognitive-services
ms.technology: qna-maker
ms.topic: quickstart
ms.date: 09/12/2018
ms.author: diberry
ms.openlocfilehash: 435c937e64d0befc5a3bf30f9d58ccd303247b9e
ms.sourcegitcommit: 4ecc62198f299fc215c49e38bca81f7eb62cdef3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/24/2018
ms.locfileid: "47037305"
---
# <a name="create-a-new-knowledge-base-in-nodejs"></a>新しいナレッジ ベースを Node.js で作成する

このクイック スタートでは、Cognitive Services API アカウントの Azure ダッシュボードに表示される QnA Maker ナレッジ ベースのサンプルをプログラムから作成する手順を紹介しています。

[!INCLUDE [Code is available in Azure-Samples Github repo](../../../../includes/cognitive-services-qnamaker-nodejs-repo-note.md)]

以下 (**req={}** の "urls") に、2 つのサンプル FAQ の URL が記載されています。コンテンツは、これらの URL から取得されます。 QnA Maker は、この半構造化コンテンツから質問とその回答を自動的に抽出します。詳しくは、こちらの[データ ソース](../Concepts/data-sources-supported.md)に関するドキュメントをご覧ください。 このクイック スタートの FAQ URL は、独自のものを使用してもかまいません。

## <a name="prerequisites"></a>前提条件

このコードを実行するには [Node.js 6 以上](https://nodejs.org/en/download/) が必要です。

[Cognitive Services API アカウント](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account)と **Microsoft QnA Maker API** を用意している必要があります。 [Azure ダッシュボード](https://portal.azure.com/#create/Microsoft.CognitiveServices)の有料サブスクリプション キーが必要です。 このクイック スタートでは、どちらのキーでも問題ありません。

![Azure ダッシュボードのサービス キー](../media/sub-key.png)

Visual Studio と Node.js について詳しくは、「[クイック スタート: Visual Studio を使用して初めての Node.js アプリを作成する](https://docs.microsoft.com/en-us/visualstudio/ide/quickstart-nodejs)」をご覧ください。

## <a name="create-knowledge-base"></a>ナレッジ ベースを作成する

次のコードでは、[Create](https://westus.dev.cognitive.microsoft.com/docs/services/5a93fcf85b4ccd136866eb37/operations/5ac266295b4ccd1554da75ff) メソッドを利用し、新しいサポート技術情報が作成されます。

1. 好みの IDE で新しい Node.js プロジェクトを作成します。
2. 次に示すコードを追加します。
3. `subscriptionKey` 値を、有効なサブスクリプション キーに置き換えます。
4. プログラムを実行します。

```nodejs
'use strict';

let fs = require('fs');
let https = require('https');

// **********************************************
// *** Update or verify the following values. ***
// **********************************************

// Replace this with a valid subscription key.
let subscriptionKey = 'YOUR SUBSCRIPTION KEY HERE';

// Components used to create HTTP request URIs for QnA Maker operations.
let host = 'westus.api.cognitive.microsoft.com';
let service = '/qnamaker/v4.0';
let method = '/knowledgebases/create';

// Formats and indents JSON for display.
let pretty_print = function (s) {
    return JSON.stringify(JSON.parse(s), null, 4);
};

// The 'callback' is called from the entire response.
let response_handler = function (callback, response) {
    let body = '';
    response.on('data', function (d) {
        body += d;
    });
    response.on('end', function () {
        // Call the 'callback' with the status code, headers, and body of the response.
        callback({ status: response.statusCode, headers: response.headers, body: body });
    });
    response.on('error', function (e) {
        console.log('Error: ' + e.message);
    });
};

// Get an HTTP response handler that calls 'callback' from the entire response.
let get_response_handler = function (callback) {
    // Return a function that takes an HTTP response and is closed over the specified callback.
    // This function signature is required by https.request, hence the need for the closure.
    return function (response) {
        response_handler(callback, response);
    };
};

// Call 'callback' when we have the entire response from the POST request.
let post = function (path, content, callback) {
    let request_params = {
        method: 'POST',
        hostname: host,
        path: path,
        headers: {
            'Content-Type': 'application/json',
            'Content-Length': content.length,
            'Ocp-Apim-Subscription-Key': subscriptionKey
        }
    };

    // Pass the 'callback' function to the response handler.
    let req = https.request(request_params, get_response_handler(callback));
    req.write(content);
    req.end();
};

// Call 'callback' when we have the entire response from the GET request.
let get = function (path, callback) {
    let request_params = {
        method: 'GET',
        hostname: host,
        path: path,
        headers: {
            'Ocp-Apim-Subscription-Key': subscriptionKey
        }
    };

    // Pass the callback function to the response handler.
    let req = https.request(request_params, get_response_handler(callback));
    req.end();
};

// Call 'callback' when we have the response from the /knowledgebases/create POST method.
let create_kb = function (path, req, callback) {
    console.log('Calling ' + host + path + '.');
    // Send the POST request.
    post(path, req, function (response) {
        // Extract the data we want from the POST response and pass it to the callback function.
        callback({ operation: response.headers.location, response: response.body });
    });
};

// Call 'callback' when we have the response from the GET request to check the status.
let check_status = function (path, callback) {
    console.log('Calling ' + host + path + '.');
    // Send the GET request.
    get(path, function (response) {
        // Extract the data we want from the GET response and pass it to the callback function.
        callback({ wait: response.headers['retry-after'], response: response.body });
    });
};

// Dictionary that holds the knowledge base.
// The data source includes a QnA pair with metadata, the URL for the
// QnA Maker FAQ article, and the URL for the Azure Bot Service FAQ article.
let req = {
    "name": "QnA Maker FAQ",
    "qnaList": [
        {
            "id": 0,
            "answer": "You can use our REST APIs to manage your Knowledge Base. See here for details: https://westus.dev.cognitive.microsoft.com/docs/services/58994a073d9e04097c7ba6fe/operations/58994a073d9e041ad42d9baa",
            "source": "Custom Editorial",
            "questions": [
                "How do I programmatically update my Knowledge Base?"
            ],
            "metadata": [
                {
                    "name": "category",
                    "value": "api"
                }
            ]
        }
    ],
    "urls": [
        "https://docs.microsoft.com/en-in/azure/cognitive-services/qnamaker/faqs",
        "https://docs.microsoft.com/en-us/bot-framework/resources-bot-framework-faq"
    ],
    "files": []
};

// Build your path URL.
var path = service + method;
// Convert the request to a string.
let content = JSON.stringify(req);
create_kb(path, content, function (result) {
    // Formats and indents the JSON response from the /knowledgebases/create method for display.
    console.log(pretty_print(result.response));
    // Loop until the operation is complete.
    let loop = function () {
        path = service + result.operation;
        // Check the status of the operation.
        check_status(path, function (status) {
            // Formats and indents the JSON for display.
            console.log(pretty_print(status.response));
            // Convert the status into an object and get the value of the 'operationState'.
            var state = (JSON.parse(status.response)).operationState;
            // If the operation isn't complete, wait and query again.
            if (state === 'Running' || state === 'NotStarted') {
                console.log('Waiting ' + status.wait + ' seconds...');
                setTimeout(loop, status.wait * 1000);
            }
        });
    };
    // Begin the loop.
    loop();
});
```

## <a name="understand-what-qna-maker-returns"></a>QnA Maker から返される応答

成功した応答は、次の例に示すように JSON で返されます。 実際に返される結果は若干異なる場合があります。 最後の呼び出しで "Succeeded" 状態が返された場合、ナレッジ ベースは正常に作成されています。 トラブルシューティングについては、QnA Maker API の「[Get Operation Details (操作の詳細の取得)](https://westus.dev.cognitive.microsoft.com/docs/services/5a93fcf85b4ccd136866eb37/operations/operations_getoperationdetails)」をご覧ください。

```json
Calling https://westus.api.cognitive.microsoft.com/qnamaker/v4.0/knowledgebases/create.
{
  "operationState": "NotStarted",
  "createdTimestamp": "2018-04-13T01:52:30Z",
  "lastActionTimestamp": "2018-04-13T01:52:30Z",
  "userId": "2280ef5917tt4ebfa1aae41fb1cebb4a",
  "operationId": "e88b5b23-e9ab-47fe-87dd-3affc2fb10f3"
}
Calling https://westus.api.cognitive.microsoft.com/qnamaker/v4.0/operations/d9d40918-01bd-49f4-88b4-129fbc434c94.
{
  "operationState": "Running",
  "createdTimestamp": "2018-04-13T01:52:30Z",
  "lastActionTimestamp": "2018-04-13T01:52:30Z",
  "userId": "2280ef5917tt4ebfa1aae41fb1cebb4a",
  "operationId": "e88b5b23-e9ab-47fe-87dd-3affc2fb10f3"
}
Waiting 30 seconds...
Calling https://westus.api.cognitive.microsoft.com/qnamaker/v4.0/operations/d9d40918-01bd-49f4-88b4-129fbc434c94.
{
  "operationState": "Succeeded",
  "createdTimestamp": "2018-04-13T01:52:30Z",
  "lastActionTimestamp": "2018-04-13T01:52:46Z",
  "resourceLocation": "/knowledgebases/b0288f33-27b9-4258-a304-8b9f63427dad",
  "userId": "2280ef5917tt4ebfa1aae41fb1cebb4a",
  "operationId": "e88b5b23-e9ab-47fe-87dd-3affc2fb10f3"
}
Press any key to continue.
```

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [QnA Maker (V4) REST API リファレンス](https://westus.dev.cognitive.microsoft.com/docs/services/5a93fcf85b4ccd136866eb37/operations/5ac266295b4ccd1554da75ff)