---
title: Bing Video Search JavaScript クライアント ライブラリのクイックスタート
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 03/19/2020
ms.author: aahi
ms.custom: devx-track-js
ms.openlocfilehash: fa1ecb24649d9355e6104bff8add5430fdd64c3c
ms.sourcegitcommit: 5dbea4631b46d9dde345f14a9b601d980df84897
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/25/2020
ms.locfileid: "91376621"
---
このクイックスタートでは、Bing Video Search の JavaScript 用のクライアント ライブラリを使用してニュースを検索する方法を説明します。 Bing Video Search の REST API はほとんどのプログラミング言語に対応していますが、このクライアント ライブラリを使用すると、サービスをお使いのアプリケーションに簡単に統合することができます。 このサンプルのソース コードは、[GitHub](https://github.com/Azure-Samples/cognitive-services-node-sdk-samples/blob/master/Samples/videoSearch.js) にあります。 そちらの方が注釈や機能が詳しく記載されています。

## <a name="prerequisites"></a>前提条件

* 最新バージョンの [Node.js](https://nodejs.org/en/download/)。
* [Bing Video Search SDK for JavaScript](https://www.npmjs.com/package/@azure/cognitiveservices-videosearch)
     *  インストールするには、`npm install @azure/cognitiveservices-videosearch` を実行します
* クライアントを認証するための `CognitiveServicesCredentials` クラス (`@azure/ms-rest-azure-js` パッケージに含まれています)。
     * インストールするには、`npm install @azure/ms-rest-azure-js` を実行します

[!INCLUDE [cognitive-services-bing-video-search-signup-requirements](~/includes/cognitive-services-bing-video-search-signup-requirements.md)]

## <a name="create-and-initialize-the-application"></a>アプリケーションを作成して初期化する

1. ご自分のお好きな IDE またはエディターを使用して新しい JavaScript ファイルを作成し、`CognitiveServicesCredentials` モジュールと Bing Video Search クライアント ライブラリ用の `require()` ステートメントを追加します。 サブスクリプション キーの変数を作成します。 
    
    ```javascript
    const CognitiveServicesCredentials = require('@azure/ms-rest-azure-js').CognitiveServicesCredentials;
    const VideoSearchAPIClient = require('@azure/cognitiveservices-videosearch');
    ```

2. 実際のキーを指定して `CognitiveServicesCredentials` のインスタンスを作成します。 さらに、それを使用して動画検索クライアントのインスタンスを作成します。

    ```javascript
    let credentials = new CognitiveServicesCredentials('YOUR-ACCESS-KEY');
    let client = new VideoSearchAPIClient(credentials);
    ```

## <a name="send-the-search-request"></a>検索要求を送信する

1. `client.videosOperations.search()` を使用して、Bing Video Search API に検索要求を送信します。 検索結果が返されたら、`.then()` を使用して結果をログします。
    
    ```javascript
    client.videosOperations.search('Interstellar Trailer').then((result) => {
        console.log(result.value);
    }).catch((err) => {
        throw err;
    });
    ```

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [シングル ページ Web アプリを作成する](../../tutorial-bing-video-search-single-page-app.md)

## <a name="see-also"></a>関連項目 

* [Bing Video Search API とは](../../overview.md)
* [Cognitive Services の .NET 向け SDK のサンプル](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/BingSearchv7)