---
title: Bing News Search JavaScript クライアント ライブラリのクイックスタート
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 03/12/2020
ms.author: aahi
ms.custom: devx-track-js
ms.openlocfilehash: 3760213c7f469dfe89599c0f01afe98168efde2d
ms.sourcegitcommit: 5dbea4631b46d9dde345f14a9b601d980df84897
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/25/2020
ms.locfileid: "91376720"
---
このクイックスタートでは、Bing News Search の JavaScript 用のクライアント ライブラリを使用してニュースの検索を開始します。 Bing News Search の REST API はほとんどのプログラミング言語に対応していますが、このクライアント ライブラリを使用すると、サービスをお使いのアプリケーションに簡単に統合することができます。 このサンプルのソース コードは、[GitHub](https://github.com/Azure-Samples/cognitive-services-node-sdk-samples/blob/master/Samples/newsSearch.js) にあります。

## <a name="prerequisites"></a>前提条件

* 最新バージョンの [Node.js](https://nodejs.org/en/download/)。
* [Bing News Search SDK for JavaScript](https://www.npmjs.com/package/@azure/cognitiveservices-newssearch)
     *  インストールするには、`npm install @azure/cognitiveservices-newssearch` を実行します
* クライアントを認証するための `CognitiveServicesCredentials` クラス (`@azure/ms-rest-azure-js` パッケージに含まれています)。
     * インストールするには、`npm install @azure/ms-rest-azure-js` を実行します

[!INCLUDE [cognitive-services-bing-news-search-signup-requirements](~/includes/cognitive-services-bing-news-search-signup-requirements.md)]

## <a name="create-and-initialize-the-application"></a>アプリケーションを作成して初期化する

1. `CognitiveServicesCredentials` のインスタンスを作成します。 サブスクリプション キーと検索用語の変数を作成します。

    ```javascript
    const CognitiveServicesCredentials = require('@azure/ms-rest-azure-js').CognitiveServicesCredentials;
    let credentials = new CognitiveServicesCredentials('YOUR-ACCESS-KEY');
    let search_term = 'Winter Olympics'
    ```

2. クライアントをインスタンス化します。
    
    ```javascript
    const NewsSearchAPIClient = require('@azure/cognitiveservices-newssearch');
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
> [単一ページの Web アプリの作成](../../tutorial-bing-news-search-single-page-app.md)
