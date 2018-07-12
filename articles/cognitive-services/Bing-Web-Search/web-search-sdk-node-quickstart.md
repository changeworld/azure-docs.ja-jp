---
title: Web Search SDK Node のクイック スタート | Microsoft Docs
description: Web Search SDK コンソール アプリケーションの設定。
titleSuffix: Azure cognitive services
services: cognitive-services
author: mikedodaro
manager: rosh
ms.service: cognitive-services
ms.component: bing-web-search
ms.topic: article
ms.date: 02/12/2018
ms.author: v-gedod
ms.openlocfilehash: 44f7f97f6c442df3fbb1e5e08189b8db7d4b9db0
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/23/2018
ms.locfileid: "35377960"
---
# <a name="web-search-sdk-node-quickstart"></a>Web Search SDK Node のクイック スタート

Bing Web Search SDK には、Web クエリと結果解析のための REST API 機能が含まれています。

Git Hub に [Node Bing Web Search SDK のサンプル ソース コード](https://github.com/Azure-Samples/cognitive-services-node-sdk-samples/blob/master/Samples/webSearch.js)があります。

## <a name="application-dependencies"></a>アプリケーションの依存関係

Bing Web Search SDK を利用してコンソール アプリケーションを設定するには、ご利用の開発環境で `npm install azure-cognitiveservices-websearch` を実行します。

## <a name="web-search-client"></a>Web Search クライアント
*[検索]* で [Cognitive Services のアクセス キー](https://azure.microsoft.com/try/cognitive-services/)を取得します。 `CognitiveServicesCredentials` のインスタンスを作成します。
```
const CognitiveServicesCredentials = require('ms-rest-azure').CognitiveServicesCredentials;
let credentials = new CognitiveServicesCredentials('YOUR-ACCESS-KEY');
```
次に、クライアントをインスタンス化します。
```
const WebSearchAPIClient = require('azure-cognitiveservices-websearch');
let webSearchApiClient = new WebSearchAPIClient(credentials);
```
結果を探します。
```
webSearchApiClient.web.search('seahawks').then((result) => {
    let properties = ["images", "webPages", "news", "videos"];
    for (let i = 0; i < properties.length; i++) {
        if (result[properties[i]]) {
            console.log(result[properties[i]].value);
        } else {
            console.log(`No ${properties[i]} data`);
        }
    }
}).catch((err) => {
    throw err;
})

```
このコードでは、テキストを解析せず、コンソールに `result.value` アイテムを出力します。  カテゴリ別の結果がある場合、その結果には次が含まれます。
- _type: 'ImageObject'
- _type: 'NewsArticle'
- _type: 'WebPage'
- _type: 'VideoObjectElementType'

<!-- Remove until this can be replaced with a sanitized version.
![Video results](media/web-search-sdk-node-results.png)
-->

## <a name="next-steps"></a>次の手順

[Cognitive Services Node.js SDK サンプル](https://github.com/Azure-Samples/cognitive-services-node-sdk-samples)
