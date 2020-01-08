---
title: クイック スタート:Node.js 用の SDK を使用してニュース検索を実行する - Bing News Search
titleSuffix: Azure Cognitive Services
description: このクイック スタートを使用して、Bing News Search SDK for Node.js を使ってニュースを検索し、応答を処理します。
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-news-search
ms.topic: quickstart
ms.date: 12/12/2019
ms.author: aahi
ms.custom: seodec2018
ms.openlocfilehash: 832ce90db1d4eac0ef8db87c10a5bc2a1658216d
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/25/2019
ms.locfileid: "75383194"
---
# <a name="quickstart-perform-a-news-search-with-the-bing-news-search-sdk-for-nodejs"></a>クイック スタート:Bing News Search SDK for Node.js を使ってニュース検索を実行する

このクイック スタートを使用して、Bing News Search SDK for Node.js を使ったニュース検索を始めましょう。 Bing News Search には、ほとんどのプログラミング言語に対応した REST API がありますが、SDK を使用すれば、アプリケーションに対して簡単にサービスを統合することができます。 このサンプルのソース コードは、[GitHub](https://github.com/Azure-Samples/cognitive-services-node-sdk-samples/blob/master/Samples/newsSearch.js) にあります。

## <a name="prerequisites"></a>前提条件

* [Node.js](https://nodejs.org/en/)

Bing News Search SDK を使用してコンソール アプリケーションを設定するには:
1. 開発環境で `npm install ms-rest-azure` を実行します。
2. 開発環境で `npm install azure-cognitiveservices-newssearch` を実行します。


[!INCLUDE [cognitive-services-bing-news-search-signup-requirements](../../../includes/cognitive-services-bing-news-search-signup-requirements.md)]

## <a name="create-and-initialize-the-application"></a>アプリケーションを作成して初期化する

1. `CognitiveServicesCredentials` のインスタンスを作成します。 サブスクリプション キーと検索用語の変数を作成します。

    ```javascript
    const CognitiveServicesCredentials = require('ms-rest-azure').CognitiveServicesCredentials;
    let credentials = new CognitiveServicesCredentials('YOUR-ACCESS-KEY');
    let search_term = 'Winter Olympics'
    ```

2. クライアントをインスタンス化します。
    
    ```javascript
    const NewsSearchAPIClient = require('azure-cognitiveservices-newssearch');
    let client = new NewsSearchAPIClient(credentials);
    ```

## <a name="send-a-search-query"></a>検索クエリを送信する

1. クライアントを使用して検索語 (この場合は "Winter Olympics") を検索します。
    
    ```javascript
    client.newsOperations.search(search_term).then((result) => {
        console.log(result.value);
    }).catch((err) => {
        throw err;
    });
    ```

このコードでは、テキストを解析せず、コンソールに `result.value` アイテムを出力します。 カテゴリ別の結果がある場合、その結果には次が含まれます。

- `_type: 'NewsArticle'`
- `_type: 'WebPage'`
- `_type: 'VideoObject'`
- `_type: 'ImageObject'`

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [単一ページの Web アプリの作成](tutorial-bing-news-search-single-page-app.md)
